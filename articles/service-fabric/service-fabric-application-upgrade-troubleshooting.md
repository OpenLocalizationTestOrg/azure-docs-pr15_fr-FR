<properties
   pageTitle="Résolution des problèmes de mises à niveau de l’application | Microsoft Azure"
   description="Cet article traite certains problèmes courants autour de la mise à niveau une application de Service tissu et comment les résoudre."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>

# <a name="troubleshoot-application-upgrades"></a>Résoudre les problèmes de mise à niveau de l’application

Cet article décrit certains des problèmes courants autour de la mise à niveau une application Azure Service tissu et comment les résoudre.

## <a name="troubleshoot-a-failed-application-upgrade"></a>Résoudre les problèmes d’une mise à niveau de l’application qui a échoué

Lorsqu’une mise à niveau échoue, le résultat de la commande **Get-ServiceFabricApplicationUpgrade** contient des informations supplémentaires pour le débogage l’échec.  La liste suivante indique comment les informations supplémentaires peuvent être utilisées :

1. Identifier le type d’échec.
2. Identifiez la raison de l’échec.
3. Isoler un ou plusieurs composants défectueux des recherches supplémentaires.

Ces informations sont disponibles quand TISSU Service détecte l’échec qu’il s’agisse du **FailureAction** restaurer ou suspendre la mise à niveau.

### <a name="identify-the-failure-type"></a>Identifier le type d’échec

Dans le résultat de **Get-ServiceFabricApplicationUpgrade**, **FailureTimestampUtc** identifie l’horodatage (au format UTC) à laquelle un échec de mise à niveau a été détecté par tissu de Service et **FailureAction** a été déclenché. **FailureReason** identifie une des trois raisons de haut niveau de l’échec :

1. UpgradeDomainTimeout - indique qu’un domaine de mise à niveau particulier est trop long pour terminer et **UpgradeDomainTimeout** expiré.
2. OverallUpgradeTimeout - indique que la mise à niveau global opération était trop longue pour terminer et **UpgradeTimeout** expiré.
3. Bilan - indique qu’après la mise à niveau un domaine de mise à jour, l’application est restée incorrecte selon les stratégies d’intégrité spécifiée et **HealthCheckRetryTimeout** expiré.

Ces entrées uniquement apparaissent dans la sortie lors de la mise à niveau échoue et une annulation. Informations complémentaires sont affiche en fonction du type de l’échec.

### <a name="investigate-upgrade-timeouts"></a>Examinez les délais de mise à niveau

Mise à niveau de délai d’expiration des échecs sont plus couramment causés par des problèmes de disponibilité de service. Le résultat suivant ce paragraphe est typique des mises à niveau où réplicas de service ou d’instances de ne pas démarrer dans la nouvelle version de code. Le champ **UpgradeDomainProgressAtFailure** capture un instantané de votre travail en attente de mise à niveau au moment de l’échec.

~~~
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                : fabric:/DemoApp
ApplicationTypeName            : DemoAppType
TargetApplicationTypeVersion   : v2
ApplicationParameters          : {}
StartTimestampUtc              : 4/14/2015 9:26:38 PM
FailureTimestampUtc            : 4/14/2015 9:27:05 PM
FailureReason                  : UpgradeDomainTimeout
UpgradeDomainProgressAtFailure : MYUD1

                                 NodeName            : Node4
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 744c8d9f-1d26-417e-a60e-cd48f5c098f0

                                 NodeName            : Node1
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 4b43f4d8-b26b-424e-9307-7a7a62e79750
UpgradeState                   : RollingBackCompleted
UpgradeDuration                : 00:00:46
CurrentUpgradeDomainDuration   : 00:00:00
NextUpgradeDomain              :
UpgradeDomainsStatus           : { "MYUD1" = "Completed";
                                 "MYUD2" = "Completed";
                                 "MYUD3" = "Completed" }
UpgradeKind                    : Rolling
RollingUpgradeMode             : UnmonitoredAuto
ForceRestart                   : False
UpgradeReplicaSetCheckTimeout  : 00:00:00
~~~

Dans cet exemple, la mise à niveau a échoué au domaine mise à niveau *MYUD1* et deux partitions (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* et *4b43f4d8-b26b-424e-9307-7a7a62e79750*) ont été bloquées. Les partitions ont été bloquées, car le runtime n’a pas pu placer réplicas principales (*WaitForPrimaryPlacement*) sur les nœuds cible *nœud 1* et *Node4*.

La commande **Get-ServiceFabricNode** peut être utilisée pour vérifier que ces deux nœuds sont dans le domaine de mise à niveau *MYUD1*. *UpgradePhase* indique *PostUpgradeSafetyCheck*, ce qui signifie que ces contrôles de sécurité sont en cours une fois que tous les nœuds dans le domaine de mise à niveau ont terminé la mise à niveau. Toutes ces informations pointant vers un problème potentiel avec la nouvelle version du code d’application. Les problèmes courants des erreurs de service dans la commande Ouvrir ou promotion chemins d’accès de code principal.

Un *UpgradePhase* de *PreUpgradeSafetyCheck* indique qu’il existe des problèmes de préparation du domaine mise à niveau avant qu’il a été effectuée. Dans ce cas, les problèmes courants sont fautes de service dans la fermer ou régression à partir de chemins de code principal.

L' actuel **UpgradeState** étant *RollingBackCompleted*, la mise à niveau d’origine doit avoir été effectuée avec une annulation **FailureAction**qui annulée automatiquement la mise à niveau en cas de panne. Si la mise à niveau d’origine a été effectuée avec une **FailureAction**manuel, puis la mise à niveau serait à la place dans un état suspendu à direct déboguer de l’application.

### <a name="investigate-health-check-failures"></a>Vérifier les erreurs de vérification d’intégrité

Échecs de vérification d’intégrité peuvent être déclenchés par différents problèmes qui peuvent se produire lorsque tous les nœuds dans un domaine de mise à niveau terminé la mise à niveau et en passant tous les tests de sécurité. Le résultat suivant ce paragraphe est caractéristique d’un échec de mise à niveau en raison des vérifications au niveau a échoué. Le champ **UnhealthyEvaluations** capture un instantané de vérifications au niveau de qui a échoué au moment de la mise à niveau en fonction de la [stratégie d’intégrité](service-fabric-health-introduction.md)spécifiée.

~~~
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                         : fabric:/DemoApp
ApplicationTypeName                     : DemoAppType
TargetApplicationTypeVersion            : v4
ApplicationParameters                   : {}
StartTimestampUtc                       : 4/24/2015 2:42:31 AM
UpgradeState                            : RollingForwardPending
UpgradeDuration                         : 00:00:27
CurrentUpgradeDomainDuration            : 00:00:27
NextUpgradeDomain                       : MYUD2
UpgradeDomainsStatus                    : { "MYUD1" = "Completed";
                                          "MYUD2" = "Pending";
                                          "MYUD3" = "Pending" }
UnhealthyEvaluations                    :
                                          Unhealthy services: 50% (2/4), ServiceType='PersistedServiceType', MaxPercentUnhealthyServices=0%.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc3', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='3a9911f6-a2e5-452d-89a8-09271e7e49a8', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc2', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='744c8d9f-1d26-417e-a60e-cd48f5c098f0', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

UpgradeKind                             : Rolling
RollingUpgradeMode                      : Monitored
FailureAction                           : Manual
ForceRestart                            : False
UpgradeReplicaSetCheckTimeout           : 49710.06:28:15
HealthCheckWaitDuration                 : 00:00:00
HealthCheckStableDuration               : 00:00:10
HealthCheckRetryTimeout                 : 00:00:10
UpgradeDomainTimeout                    : 10675199.02:48:05.4775807
UpgradeTimeout                          : 10675199.02:48:05.4775807
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
~~~

Une solution échecs de vérification d’intégrité nécessite tout d’abord la compréhension du modèle d’intégrité du Service tissu. Mais même sans ces approfondies, nous voyons que deux services sont incorrects : *TISSU : / DemoApp/Svc3* et *TISSU : / DemoApp/Svc2*, ainsi que les rapports d’intégrité d’erreur (« InjectedFault » dans le cas présent). Dans cet exemple, deux quatre services sont incorrects, qui est en dessous de la cible par défaut de 0 % incorrecte (*MaxPercentUnhealthyServices*).

La mise à niveau a été suspendu lors de l’échec en spécifiant une **FailureAction** du manuel lors du démarrage de la mise à niveau. Ce mode permet de déterminer l’origine du système live dans l’état Échec avant d’entreprendre aucune autre action.

### <a name="recover-from-a-suspended-upgrade"></a>Récupérer à partir d’une mise à niveau suspendu

Avec une annulation **FailureAction**, il n’est pas nécessaire étant donné que la mise à niveau annule automatiquement après une défaillance de récupération. Avec un manuel **FailureAction**, il existe plusieurs options de récupération :

1. Déclencher manuellement une annulation
2. Passez en revue le reste de la mise à niveau manuellement
3. Reprendre la mise à niveau contrôlée

La commande **Démarrer ServiceFabricApplicationRollback** peut être utilisée à tout moment pour démarrer l’annulation de l’application. Une fois que la commande retourne avec succès, la demande d’annulation n’a été enregistrée dans le système et démarre peu de temps après.

La commande **Resume ServiceFabricApplicationUpgrade** peut servir à manuellement, suivez les instructions de la mise à niveau un domaine mise à niveau à la fois. Dans ce mode, les contrôles de sécurité uniquement sont effectuées par le système. Vérifications au niveau de ne plus sont effectuées. Cette commande peut uniquement être utilisée lorsque *UpgradeState* affiche *RollingForwardPending*, ce qui signifie que le domaine de mise à niveau en cours a terminé la mise à niveau mais celle qui suit n’a pas commencé (en attente).

La commande de **Mise à jour ServiceFabricApplicationUpgrade** peut servir à reprendre la mise à niveau analysé avec les deux sécurité et vérifications en cours d’exécution.

~~~
PS D:\temp> Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored

UpgradeMode                             : Monitored
ForceRestart                            :
UpgradeReplicaSetCheckTimeout           :
FailureAction                           :
HealthCheckWaitDuration                 :
HealthCheckStableDuration               :
HealthCheckRetryTimeout                 :
UpgradeTimeout                          :
UpgradeDomainTimeout                    :
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :

PS D:\temp>
~~~

La mise à niveau continue à partir de la mise à niveau de domaine dans lequel il a été suspendue dernière et l’utilisation de la même mise à niveau les paramètres et les stratégies d’intégrité comme avant. Si nécessaire, les paramètres de mise à niveau et les stratégies d’intégrité figurant dans le résultat précédent peuvent être changés dans la même commande lors de la mise à niveau reprend. Dans cet exemple, la mise à niveau a été repris dans le mode contrôlés, avec les paramètres et les stratégies d’intégrité inchangés.

## <a name="further-troubleshooting"></a>Dépannage supplémentaires

### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>Service TISSU suit pas les stratégies d’état spécifié

1 des causes possibles :

Service TISSU tous les pourcentages se traduit par des nombres réels d’entités (par exemple, réplicas, partitions et services) pour l’évaluation d’intégrité et arrondit toujours à entités entières. Par exemple, si le nombre maximal _MaxPercentUnhealthyReplicasPerPartition_ est 21 % et il n’y cinq réplica, Service TISSU permet jusqu'à deux réplicas défectueux (ce is,'Math.Ceiling (5\*0,21)). Par conséquent, les stratégies d’intégrité doivent être définies en conséquence.

2 des causes possibles :

Stratégies d’intégrité sont spécifiées en pourcentage de services total et instances de service non spécifiques. Par exemple, avant une mise à niveau, si une application comporte quatre instances A, B, C et D, du service où service D ne fonctionne pas correctement, mais avec peu d’impact sur l’application. Nous voulons ignorer le service défectueux connu D lors de la mise à niveau et définir le paramètre *MaxPercentUnhealthyServices* à 25 %, en supposant que seulement A, B et C doivent être correct.

Toutefois, pendant la mise à niveau, D peut devenir exact tandis que C devient incorrecte. La mise à niveau pourront toujours aboutir car uniquement 25 % des services sont incorrects. Toutefois, il peut entraîner des erreurs inattendues en raison de C en cours de façon inattendue défectueux au lieu de D. Dans ce cas, D doit être modélisation comme type de services différente de A, B et C. Dans la mesure où les stratégies d’intégrité sont spécifiées par type de service, pourcentage mauvais état différents seuils peuvent être appliqués à différents services. 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>Je n’avez pas spécifié une stratégie d’intégrité de mise à niveau de l’application, mais la mise à niveau ne fonctionne toujours pas pour certains délais que j’ai jamais été spécifié

Lorsque les stratégies d’intégrité ne sont pas fournies à la demande de mise à niveau, ils sont extraits de *ApplicationManifest.xml* de la version actuelle de l’application. Par exemple, si vous mettez à niveau Application X de la version 1.0 vers la version 2.0, stratégies d’intégrité application spécifiée pour la version 1.0 sont utilisés. Si une stratégie d’intégrité différent doit être utilisée pour la mise à niveau, la stratégie doit être indiquée dans le cadre de l’appel de l’API application mise à niveau. Les stratégies spécifiés dans le cadre de l’appel API s’appliquent uniquement pendant la mise à niveau. Une fois que la mise à niveau est terminée, les stratégies spécifiés dans *ApplicationManifest.xml* sont utilisées.

### <a name="incorrect-time-outs-are-specified"></a>Délais d’expiration incorrectes sont spécifiés

Vous pouvez ont demandé sur que se passe-t-il lorsque les délais sont définies de façon incohérente. Par exemple, vous devrez un *UpgradeTimeout* qui est inférieure à *UpgradeDomainTimeout*. La réponse est qu’une erreur est renvoyée. Erreurs sont retournés si *UpgradeDomainTimeout* est inférieur à la somme de *HealthCheckWaitDuration* et *HealthCheckRetryTimeout*, ou si *UpgradeDomainTimeout* est inférieur à la somme de *HealthCheckWaitDuration* et *HealthCheckStableDuration*.

### <a name="my-upgrades-are-taking-too-long"></a>Mes mises à jour sont prend trop de temps

Le temps effectuer une mise à niveau dépend les vérifications au niveau de délais d’expiration spécifiés. Vérifications au niveau et les délais dépendent de la durée à copier, le déploiement et placez son l’application. En cours trop agressifs avec les délais peuvent signifie plus échecs mises à niveau, nous vous recommandons de commençant manière conventionnelle par les délais de plus de temps.

Voici un rappel rapide sur la façon dont les délais d’expiration interagissent avec les heures de mise à niveau :

Mises à niveau un domaine mise à niveau ne pourrez pas terminer plus rapidement que *HealthCheckWaitDuration* + *HealthCheckStableDuration*.

Échec de la mise à niveau ne peut pas se produire plus rapidement que *HealthCheckWaitDuration* + *HealthCheckRetryTimeout*.

L’heure de mise à niveau d’un domaine de mise à niveau est limitée par *UpgradeDomainTimeout*.  Si *HealthCheckRetryTimeout* et *HealthCheckStableDuration* sont tous deux différente de zéro et l’état de l’application conserve des changements, puis la mise à niveau finalement arrive à expiration sur *UpgradeDomainTimeout*. *UpgradeDomainTimeout* commence à compter vers le bas une fois que la mise à niveau pour le domaine de mise à niveau actuel commence.

## <a name="next-steps"></a>Étapes suivantes

[La mise à niveau votre Application à l’aide de Visual Studio](service-fabric-application-upgrade-tutorial.md) vous guide dans une mise à niveau de l’application à l’aide de Visual Studio.

[La mise à niveau votre Application utilisation de Powershell](service-fabric-application-upgrade-tutorial-powershell.md) vous guide dans une mise à niveau de l’application à l’aide de PowerShell.

Contrôler la façon dont votre application met à niveau en utilisant des [Paramètres de mise à niveau](service-fabric-application-upgrade-parameters.md).

Vérifiez les mises à niveau votre application compatibles en apprendre à utiliser la [Sérialisation de données](service-fabric-application-upgrade-data-serialization.md).

Découvrez comment utiliser les fonctionnalités avancées nécessitent pendant la mise à niveau votre application en faisant référence à [Des rubriques avancées](service-fabric-application-upgrade-advanced.md).

Résoudre les problèmes courants dans les mises à niveau de l’application en faisant référence aux étapes de [Dépannage les mises à niveau de l’Application](service-fabric-application-upgrade-troubleshooting.md).
 
