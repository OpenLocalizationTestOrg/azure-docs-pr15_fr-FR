
<properties
   pageTitle="Mise à niveau de l’application : les paramètres de mise à niveau | Microsoft Azure"
   description="Décrit les paramètres relatifs à la mise à niveau une application Service tissu, y compris les vérifications au niveau à effectuer et les stratégies pour automatiquement annuler la mise à niveau."
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



# <a name="application-upgrade-parameters"></a>Paramètres de mise à niveau d’application

Cet article décrit les différents paramètres qui s’appliquent au cours de la mise à niveau d’une application Azure Service tissu. Les paramètres comprennent le nom et la version de l’application. Il s’agit des boutons qui contrôlent les délais d’expiration et les contrôles qui sont appliqués pendant la mise à niveau, et ils spécifient les stratégies qui doivent être appliquées en cas d’échec d’une mise à niveau.


<br>

| Paramètre | Description |
| --- | --- |
| ApplicationName | Nom de l’application est mis à niveau. Exemples : tissu : / VisualObjects, TISSU : / ClusterMonitor  |
| TargetApplicationTypeVersion | Type de la version de l’application des mise à niveau cibles. |
| FailureAction | L’action effectuée par tissu de Service en cas d’échec de la mise à niveau. L’application peut être restaurée vers la version mise à jour partielle (restaurer) ou la mise à niveau peut être arrêtée au niveau du domaine de mise à niveau en cours. Dans ce cas, le mode de mise à niveau est également modifié sur manuel. Les valeurs autorisées sont manuel et restauration. |
| HealthCheckWaitDurationSec | Le délai d’attente (en secondes) une fois la mise à niveau terminée sur le domaine de mise à niveau avant TISSU Service évalue l’état de l’application. Cette durée peut également être considérée comme la fois qu'une application doit s’exécuter avant qu’il peut être considéré comme correct. Si la vérification d’intégrité réussit, le processus de mise à niveau continue pour le domaine de mise à niveau suivant.  Si la vérification d’intégrité échoue, TISSU Service attend pour un intervalle (la UpgradeHealthCheckInterval) avant d’essayer de la vérification d’intégrité à nouveau jusqu'à ce que le HealthCheckRetryTimeout est atteint. La valeur recommandée et par défaut est 0 seconde. |
| HealthCheckRetryTimeoutSec | La durée (en secondes) ce Service TISSU continue de fonctionner d’évaluation d’intégrité avant de déclaration d’un échec de la mise à niveau. La valeur par défaut est 600 secondes. Cette durée démarre après que HealthCheckWaitDuration est atteint. Dans cette HealthCheckRetryTimeout, tissu de Service peut effectuer plusieurs vérifications au niveau de l’état de l’application. La valeur par défaut est 10 minutes et doit être personnalisée de manière appropriée pour votre application. |
| HealthCheckStableDurationSec | La durée (en secondes) pour vérifier que l’application est stable avant de déplacer vers le domaine de mise à niveau suivant ou de fin de la mise à niveau. Cette durée d’attente est utilisée pour empêcher les modifications non détectées de santé droite après l’exécution de la vérification d’intégrité. La valeur par défaut est 120 secondes et doit être personnalisée de manière appropriée pour votre application. |
| UpgradeDomainTimeoutSec | Durée maximale (en secondes) pour la mise à niveau un seul domaine mise à niveau. Si ce délai est atteint, la mise à niveau s’arrête et s’exécute en fonction du paramètre pour UpgradeFailureAction. La valeur par défaut n’est jamais (infini) et doit être personnalisé de manière appropriée pour votre application. |
| UpgradeTimeout | Un délai d’attente (en secondes) qui s’applique à la mise à niveau entière. Si ce délai est atteint, la mise à niveau s’arrête et UpgradeFailureAction se déclenche. La valeur par défaut n’est jamais (infini) et doit être personnalisé de manière appropriée pour votre application. |
| UpgradeHealthCheckInterval | La fréquence que l’état est activée. Ce paramètre est spécifié dans la section ClusterManager du _cluster_ _manifeste_et n’est pas spécifié dans le cadre de l’applet de commande de mise à niveau. La valeur par défaut est 60 secondes.  |






<br>
## <a name="service-health-evaluation-during-application-upgrade"></a>Évaluation d’intégrité du service pendant la mise à niveau de l’application

<br>
Les critères d’évaluation d’intégrité sont facultatives. Si les critères d’évaluation d’intégrité ne sont pas indiqués au démarrage d’une mise à niveau, Service TISSU utilise les stratégies d’intégrité d’application spécifiés dans le ApplicationManifest.xml de l’instance d’application.


<br>

| Paramètre | Description |
| --- | --- |
| ConsiderWarningAsError | Valeur par défaut est False. Traiter les événements d’avertissement d’intégrité de l’application comme des erreurs lors de l’évaluation de l’état de l’application pendant la mise à niveau. Par défaut, tissu de Service n’évalue pas les événements d’état avertissement pour être échecs (erreurs), afin de procéder à la mise à niveau même s’il existe des événements d’avertissement.   |
| MaxPercentUnhealthyDeployedApplications | Valeur recommandée et par défaut est égal à 0. Spécifier le nombre maximal d’applications déployées (voir la [section État](service-fabric-health-introduction.md)) qui peuvent être incorrects avant de l’application est considéré comme incorrecte et d’échec de la mise à niveau. Ce paramètre définit l’état de l’application sur le nœud et permettant de détecter les problèmes de mise à jour. En règle générale, les réplicas de l’application obtenir équilibrage de charge vers l’autre nœud, ce qui permet à l’application doit apparaître correctement, ce qui permet la mise à niveau continuer. En spécifiant un contrôle d’intégrité MaxPercentUnhealthyDeployedApplications stricte, tissu de Service peut détecter rapidement un problème avec le package d’application et faciliter la fail rapidement la mise à niveau. |
| MaxPercentUnhealthyServices | Valeur recommandée et par défaut est égal à 0. Spécifier le nombre maximal de services dans l’instance d’application qui peut être incorrect avant de l’application est considéré comme incorrecte et d’échec de la mise à niveau. |
| MaxPercentUnhealthyPartitionsPerService | Valeur recommandée et par défaut est égal à 0. Spécifier le nombre maximal de partitions dans un service peut être mauvais état avant que le service est considéré comme incorrect. |
| MaxPercentUnhealthyReplicasPerPartition | Valeur recommandée et par défaut est égal à 0. Spécifier le nombre maximal de réplicas dans partition qui peut être incorrecte avant que la partition est considéré comme incorrecte. |
| UpgradeReplicaSetCheckTimeout | **Service sans état**--au sein d’un domaine de mise à niveau unique, TISSU Service tente de vous assurer que des instances supplémentaires du service sont disponibles. Si le nombre d’instances cible est supérieur à un, Service TISSU attend de plusieurs instances soit disponible, jusqu'à une valeur du délai maximal. Ce délai est spécifié à l’aide de la propriété UpgradeReplicaSetCheckTimeout. Si le délai d’expiration, Service TISSU procède à la mise à niveau, quel que soit le nombre d’instances de service. Si le nombre d’instances cible, tissu de Service n’attend pas et commence immédiatement la mise à niveau. **Service dynamique**--au sein d’un domaine de mise à niveau unique, tissu de Service essaie de vous assurer que le jeu de réplica a un quorum. Service TISSU attend quorum soit disponible, jusqu'à une valeur du délai maximal (spécifiée par la propriété UpgradeReplicaSetCheckTimeout). Si le délai d’expiration, Service TISSU procède à la mise à niveau, quel que soit quorum. Ce paramètre est définie comme jamais (infini) lors de la restauration et 900 secondes lors de l’annulation. |
| ForceRestart | Si vous mettez à jour une configuration ou un package de données sans mettre à jour le code de service, le service est redémarré uniquement si la propriété ForceRestart est définie sur true. Lorsque la mise à jour est terminée, TISSU Service vous avertit le service qu’il existe un nouveau package de configuration ou données. Le service est responsable de l’application des modifications. Si nécessaire, le service peut redémarrer lui-même. |



<br>
<br>
Les critères MaxPercentUnhealthyServices, MaxPercentUnhealthyPartitionsPerService et MaxPercentUnhealthyReplicasPerPartition peuvent être spécifiées par type de service pour une instance d’application. Définition de ces paramètres par service permet d’une application contenir des types différents services avec les stratégies d’évaluation différentes. Par exemple, un type de service passerelle sans état peut avoir une MaxPercentUnhealthyPartitionsPerService est la différence entre un type de service moteur dynamique pour une instance d’application particulière.

## <a name="next-steps"></a>Étapes suivantes

[La mise à niveau votre Application à l’aide de Visual Studio](service-fabric-application-upgrade-tutorial.md) vous guide dans une mise à niveau de l’application à l’aide de Visual Studio.

[La mise à niveau votre Application utilisation de Powershell](service-fabric-application-upgrade-tutorial-powershell.md) vous guide dans une mise à niveau de l’application à l’aide de PowerShell.

Vérifiez les mises à niveau votre application compatibles en apprendre à utiliser la [Sérialisation de données](service-fabric-application-upgrade-data-serialization.md).

Découvrez comment utiliser les fonctionnalités avancées nécessitent pendant la mise à niveau votre application en faisant référence à [Des rubriques avancées](service-fabric-application-upgrade-advanced.md).

Résoudre les problèmes courants dans les mises à niveau de l’application en faisant référence aux étapes de [Dépannage les mises à niveau de l’Application](service-fabric-application-upgrade-troubleshooting.md).
 
