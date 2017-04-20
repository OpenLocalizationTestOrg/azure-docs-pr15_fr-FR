<properties
   pageTitle="Contrôle d’état de Service TISSU | Microsoft Azure"
   description="Présentation du modèle, qui offre un contrôle de cluster et ses services et applications de contrôle d’état Azure Service tissu."
   services="service-fabric"
   documentationCenter=".net"
   authors="oanapl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/28/2016"
   ms.author="oanapl"/>

# <a name="introduction-to-service-fabric-health-monitoring"></a>Introduction à l’analyse d’intégrité du Service TISSU
TISSU Service Azure présente un modèle de fonctionnement qui offre d’évaluation d’intégrité riche, flexible et extensible et création de rapports. Le modèle permet près en temps réel surveillance de l’état du cluster et les services en cours d’exécution qu’il contient. Vous pouvez facilement obtenir des informations sur l’état et corriger les problèmes potentiels avant qu’ils en cascade et provoquer des pannes massives. Dans le modèle par défaut, les services envoyer des rapports en fonction de leur point de vue local, et que les informations sont regroupées pour offrir globale cluster au niveau du mode.

Composants de service TISSU utilisent ce modèle santé riche pour signaler leur état actuel. Vous pouvez utiliser le même mécanisme à état des rapports à partir de vos applications. Si vous investissez dans le rapport d’intégrité de haute qualité qui capture vos conditions personnalisées, vous pouvez détecter et corriger les problèmes de votre application en cours d’exécution beaucoup plus facilement.

> [AZURE.NOTE] Nous avons commencé le sous-système santé pour répondre à un besoin contrôlée mises à jour. Service TISSU fournit contrôlée application cluster mises à niveau et qu’assurer la disponibilité complète, sans interruption de service et un minimum à aucune intervention de l’utilisateur. Pour atteindre ces objectifs, la mise à niveau vérifie santé basée sur configuré des stratégies de mise à niveau et permet une mise à niveau fonctionne uniquement lorsque la santé respecte les seuils souhaités. Dans le cas contraire, la mise à niveau est automatiquement annulée ou en pause pour permettre aux administrateurs de résoudre les problèmes. Pour en savoir plus sur les mises à niveau de l’application, voir [cet article](service-fabric-application-upgrade.md).

## <a name="health-store"></a>Banque d’intégrité
Le magasin d’intégrité reste relatifs à l’état des informations sur les entités dans le cluster pour y accéder facilement et d’évaluation. Il est implémenté comme un tissu Service persistante avec état service pour vous assurer qu’élevées. Le magasin d’intégrité fait partie de la **TISSU : / système** application et il est disponible lorsque le cluster est disponible et en cours d’exécution.

## <a name="health-entities-and-hierarchy"></a>Hiérarchie et entités santé
Les entités santé sont organisées dans une hiérarchie logique qui capture les interactions et dépendances entre différentes entités. La hiérarchie et entités sont créées automatiquement par le magasin d’intégrité basé sur les rapports reçus à partir de composants de tissu de Service.

Les entités santé reflètent les entités tissu de Service. (Par exemple, **entité d’application santé** correspond à une instance de l’application déployée dans le cluster, tandis que **entité de nœud santé** correspond à un nœud de cluster Service tissu.) La hiérarchie d’intégrité capture les interactions entre les entités système, et il est la base d’évaluation d’intégrité avancées. Vous pouvez en savoir plus sur les principaux concepts tissu de Service de [présentation technique tissu de Service](service-fabric-technical-overview.md). Pour plus d’informations sur l’application, voir [modèle d’application tissu de Service](service-fabric-application-model.md).

Entités santé et hiérarchie autoriser le cluster et les applications pour être signalé, débogage et surveiller. Le modèle de fonctionnement fournit une représentation précise, *granulaires* de l’état des courbe nombreux éléments dans le cluster.

![Entités santé.][1]
Les entités santé, organisés dans une hiérarchie basée sur les relations parent-enfant.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

Les entités santé sont :

- **Cluster**. Représente l’état d’un cluster de tissu de Service. Rapports d’intégrité cluster décrivent les conditions qui affectent la totalité du cluster et ne peut pas être réduites pour un ou plusieurs enfants mauvais état. L’avis de brain du cluster fractionnement en raison de problèmes de communication ou partition réseau sont des exemples.

- **Nœud**. Représente l’état du nœud tissu de Service. Rapports d’intégrité nœud décrivent conditions qui affectent la fonctionnalité de nœud. Ils affectent généralement toutes les entités déployées en cours d’exécution dessus. Lorsqu’un nœud n’appartient pas d’espace disque (ou une autre propriété échelle de l’ordinateur, telles que la mémoire, connexions) et exemples lorsqu’un nœud est vers le bas. Le nœud est identifiée par le nom du nœud (chaîne).

- **Application**. Représente l’état d’une instance de l’application en cours d’exécution dans le cluster. Rapports d’intégrité application décrivent les conditions qui affectent la santé générale de l’application. Ils ne peuvent pas être réduits vers le bas de chaque enfant (services ou applications déployées). Exemples : l’interaction de bout en bout entre les différents services dans l’application. L’application est identifiée par le nom de l’application (URI).

- **Service**. Représente l’état d’un service en cours d’exécution dans le cluster. Rapports d’intégrité du service décrivent les conditions qui affectent la santé générale du service, et qu’ils ne peuvent pas être réduits à une partition ou un réplica. Une configuration du service (par exemple, le partage de fichiers externes ou port) à l’origine de problèmes de toutes les partitions des exemples. Le service est identifiée par le nom du service (URI).

- **Partition**. Représente l’état d’une partition de service. Rapports d’intégrité partition décrivent les conditions qui affectent le jeu de réplica entier. Lorsque le nombre de réplicas est inférieur à compter cible et exemples lorsqu’une partition est perte quorum. La partition est identifiée par la partition ID (GUID).

- **Réplica**. Représente l’état d’un service dynamique réplica ou une instance de service sans état. La plus petite unité agents de surveillance et les composants du système peuvent créer des rapports sur pour une application. Pour les services avec état, les exemples sont un réplica principal reporting lorsqu’il ne peut pas répliquer opérations secondaires et lorsque la réplication n’est pas continuer à attendus s’exécute. En outre, une instance sans état peut signaler lorsqu’il est insuffisant ressources ou présente des problèmes de connectivité. L’entité réplica est identifiée par la partition ID (GUID) et l’ID réplica ou instance (long).

- **DeployedApplication**. Représente l’état d’une *application en cours d’exécution sur un nœud*. Rapports d’intégrité application déployée décrivent des conditions spécifiques à l’application sur le nœud qui ne peut pas être réduit aux packages service déployés sur le même nœud. Lorsque le package d’application ne peuvent pas être téléchargé sur ce nœud et lorsqu’il y a un paramètre de problème des principaux de sécurité d’application sur le nœud sont des exemples. L’application déployée est identifiée par nom de l’application (URI) et le nom du nœud (chaîne).

- **DeployedServicePackage**. Représente l’état d’un package de service en cours d’exécution sur un nœud dans le cluster. Il traite des conditions spécifiques à un package de service qui ne concernent pas les autres packages de service sur le même nœud pour la même application. Exemples : un package de code dans le package de service qui ne peut pas être démarré et un package de configuration ne peuvent pas être lus. Le package de service déployé est identifié par le nom de l’application (URI), le nom de nœud (chaîne) et nom de manifeste de service (chaîne).

La précision du modèle d’intégrité facilite la détecter et corriger les problèmes. Par exemple, si un service ne répond pas, il est possible d’indiquer que l’instance d’application ne fonctionne pas correctement. Création de rapports en que niveau n’est pas idéal, toutefois, étant donné que le problème ne peut-être pas affecter tous les services de cette application. Le rapport doit être appliqué au service défectueux ou à une partition enfant spécifique, si plus d’informations pointe vers cette partition. Les données automatiquement surfaces via la hiérarchie et une partition mauvais état est rendu visible à des niveaux de service et de l’applications. Cette agrégation vous aide à identifier et résoudre plus rapidement la cause du problème.

La hiérarchie d’intégrité se compose de relations parent-enfant. Un cluster est composé des nœuds et des applications. Les applications ont des services et applications déploiement. Applications déployées ont déployé packages de service. Les services ont partitions et chaque partition comporte un ou plusieurs réplicas. Il existe une relation entre les nœuds et entités déployées spéciale. Si un nœud ne fonctionne pas correctement comme indiqué par son composant de système autorité (Gestionnaire de basculement service), il affecte les applications déployées, packages de service et réplicas déployés sur celui-ci.

La hiérarchie d’intégrité représente le dernier état du système basé sur les derniers rapports d’intégrité, qui est presque en temps réel.
Agents de surveillance internes et externes peuvent créer des rapports sur les mêmes entités basées sur une logique spécifique à l’application ou conditions analysées personnalisées. Rapports sur les utilisateurs coexistent avec les rapports du système.

Pensez à investir sur la façon de créer des rapports et de répondre aux santé pendant la conception d’un service cloud volumineux, pour faciliter la déboguer, surveiller et le fonctionnement du service.

## <a name="health-states"></a>États de fonctionnement
Service TISSU utilise trois états d’intégrité pour décrire si une entité est correcte ou non : OK, avertissement et erreur. N’importe quel rapport envoyé au magasin santé devez spécifier un des états suivants. Résultat de l’évaluation d’intégrité est un des états suivants.

Les [États de fonctionnement](https://msdn.microsoft.com/library/azure/system.fabric.health.healthstate) possibles sont :

- **OK**. L’entité est correcte. Il n’existe aucun problème connu signalés ou ses enfants (le cas échéant).

- **Avertissement**. L’entité rencontre des problèmes, mais il n’est pas encore incorrecte (par exemple, il existe des retards, mais ils n’entraînent pas encore des problèmes fonctionnelles). Dans certains cas, la condition d’avertissement peut résoudre lui-même sans intervention spéciale, et il est utile de fournir visibilité sur ce qui se passe. Dans les autres cas, la condition d’avertissement peut-être se dégrader dans un problème grave sans intervention de l’utilisateur.

- **Erreur**. L’entité ne fonctionne pas correctement. Il convient pour la résolution de l’état de l’entité, car il ne peut pas fonctionner correctement.

- **Inconnu**. L’entité n’existe pas dans le magasin d’intégrité. Ce résultat peut être obtenu à partir des requêtes distribuées fusionner les résultats à partir de plusieurs composants. Par exemple, obtenez requête de liste nœud accède à **basculement** et **HealthManager**, get application liste requête accède à **ClusterManager** et **HealthManager**. Ces requêtes fusionner les résultats à partir de plusieurs composants système. Si un autre composant système retourne une entité qui n’a pas atteint ou a été nettoyée à partir du magasin d’intégrité, le résultat fusionné a inconnu état d’intégrité.

## <a name="health-policies"></a>Stratégies d’intégrité
Le magasin d’intégrité applique les stratégies d’intégrité pour déterminer si une entité est correcte en fonction de ses rapports et ses enfants.

> [AZURE.NOTE] Stratégies d’intégrité peuvent être spécifiés dans le manifeste cluster (pour l’évaluation d’intégrité cluster et nœud) ou dans le manifeste d’application (pour l’évaluation de l’application et tous ses enfants). Les demandes d’évaluation d’intégrité peuvent également passer dans les stratégies d’évaluation d’état personnalisé, qui est utilisé uniquement pour cette évaluation.

Par défaut, tissu de Service applique les règles strictes (tout le contenu doit être exact) pour la relation hiérarchique parent-enfant. Si des enfants même s’il dispose d’un événement mauvais état, le parent est considéré comme incorrect.

### <a name="cluster-health-policy"></a>Stratégie d’intégrité cluster
La [stratégie d’intégrité cluster](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.aspx) est utilisée pour évaluer l’état d’intégrité cluster et états d’intégrité des nœuds. La stratégie peut être définie dans le manifeste cluster. Si elle n’est pas présent, la stratégie par défaut (zéro échecs autorisés) est utilisée.
La stratégie d’intégrité cluster contient :

- [ConsiderWarningAsError](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.considerwarningaserror.aspx). Spécifie si à considérer santé avertissement signale comme des erreurs lors de l’évaluation d’intégrité. Par défaut : false.

- [MaxPercentUnhealthyApplications](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.maxpercentunhealthyapplications.aspx). Spécifie le pourcentage maximal autorisé d’applications qui peuvent être incorrects avant le cluster est considéré comme erreur.

- [MaxPercentUnhealthyNodes](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.maxpercentunhealthynodes.aspx). Spécifie le pourcentage maximal autorisé des nœuds qui peuvent être incorrects avant le cluster est considéré comme erreur. Dans les grandes clusters, certains nœuds sont toujours vers le bas ou arrière pour corriger, afin que ce pourcentage doit être configuré pour faire face qui.

- [ApplicationTypeHealthPolicyMap](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.applicationtypehealthpolicymap.aspx). La carte de stratégie application type d’intégrité peut servir lors de l’évaluation d’intégrité de cluster pour décrire les types d’application spécial. Par défaut, toutes les applications sont placées dans un pool et évaluées avec MaxPercentUnhealthyApplications. Si certains types d’applications doivent être traités différemment, ils peuvent provenir déconnecter le pool global. En revanche, elles sont évaluées contre les pourcentages associées à leur nom de type d’application dans l’Explorateur. Par exemple, dans un cluster Voici des milliers d’applications de différents types et plusieurs instances d’application contrôle d’un type d’application spécial. Les applications de contrôle ne doivent jamais être erreur. Vous pouvez spécifier MaxPercentUnhealthyApplications globales à 20 % afin de faire face certaines défaillances, mais pour le type d’application « ControlApplicationType » définie le MaxPercentUnhealthyApplications à 0. De cette façon, si certains de nombreuses applications sont incorrects, mais sous le pourcentage global mauvais état, le cluster serait évalué à avertissement. Un état d’intégrité avertissement n’influe pas sur la mise à niveau cluster ou autres surveillance déclenchés par état d’intégrité d’erreur. Mais un seul contrôle application erreur serait cluster défectueux, ce qui déclenche annuler ou interrompt la mise à niveau cluster, selon la configuration de la mise à niveau.
Pour les types d’application définies dans le plan, toutes les instances d’application sont prises en dehors du pool global des applications. Ils sont évaluées en fonction du nombre total d’applications du type d’application, à l’aide des MaxPercentUnhealthyApplications spécifiques à partir de la carte. Toutes les autres applications restent dans le pool global et sont évaluées avec MaxPercentUnhealthyApplications.

L’exemple suivant est un extrait d’un manifeste cluster. Pour définir des entrées dans la table de types d’application, le préfixe le nom du paramètre avec « ApplicationTypeMaxPercentUnhealthyApplications- », suivi du nom de type d’application.

```xml
<FabricSettings>
  <Section Name="HealthManager/ClusterHealthPolicy">
    <Parameter Name="ConsiderWarningAsError" Value="False" />
    <Parameter Name="MaxPercentUnhealthyApplications" Value="20" />
    <Parameter Name="MaxPercentUnhealthyNodes" Value="20" />
    <Parameter Name="ApplicationTypeMaxPercentUnhealthyApplications-ControlApplicationType" Value="0" />
  </Section>
</FabricSettings>
```

### <a name="application-health-policy"></a>Stratégie d’intégrité d’application
La [stratégie d’intégrité d’application](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.aspx) décrit comment l’évaluation d’événements et d’états enfants agrégation est terminée pour les applications et leurs enfants. Il peut être défini dans le manifeste d’application, **ApplicationManifest.xml**, dans le package d’application. Si aucune stratégie n’est spécifiés, TISSU Service part du principe que l’entité ne fonctionne pas correctement s’il comporte un rapport d’intégrité ou enfant à l’état d’intégrité avertissement ou une erreur.
Les stratégies configurables sont :

- [ConsiderWarningAsError](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.considerwarningaserror.aspx). Spécifie si à considérer santé avertissement signale comme des erreurs lors de l’évaluation d’intégrité. Par défaut : false.

- [MaxPercentUnhealthyDeployedApplications](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.maxpercentunhealthydeployedapplications.aspx). Spécifie le pourcentage maximal autorisé des applications déployées qui peuvent être incorrects avant que l’application est considérée comme erreur. Ce pourcentage est calculé en divisant le nombre d’applications déployées mauvais état sur le nombre de nœuds les applications sont actuellement déployées sur dans le cluster. Le calcul arrondit tolérer une défaillance sur un petit nombre de nœuds. Par défaut pourcentage : zéro.

- [DefaultServiceTypeHealthPolicy](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.defaultservicetypehealthpolicy.aspx). Spécifie la stratégie par défaut service type santé qui remplace la stratégie d’intégrité par défaut pour tous les types de service dans l’application.

- [ServiceTypeHealthPolicyMap](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.servicetypehealthpolicymap.aspx). Fournit une carte de stratégies d’intégrité du service par type de service. Ces politiques remplacent les stratégies de santé de type de service par défaut pour chaque type de service spécifié. Par exemple, si une application a un type de service passerelle sans état et un type de service moteur dynamique, vous pouvez configurer les stratégies d’intégrité d’appréciation différemment. Lorsque vous spécifiez stratégie par type de service, vous pouvez obtenir un contrôle plus précis de l’état du service.

### <a name="service-type-health-policy"></a>Stratégie d’intégrité de type de service
La [stratégie d’intégrité du service type](https://msdn.microsoft.com/library/azure/system.fabric.health.servicetypehealthpolicy.aspx) spécifie comment évaluer et agréger les services et les enfants de services. La stratégie contient :

- [MaxPercentUnhealthyPartitionsPerService](https://msdn.microsoft.com/library/azure/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthypartitionsperservice.aspx). Spécifie le pourcentage maximal autorisé des partitions mauvais état avant d’un service est considéré comme incorrect. Par défaut pourcentage : zéro.

- [MaxPercentUnhealthyReplicasPerPartition](https://msdn.microsoft.com/library/azure/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyreplicasperpartition.aspx). Spécifie le pourcentage maximal autorisé de réplicas mauvais état avant une partition est considéré comme incorrecte. Par défaut pourcentage : zéro.

- [MaxPercentUnhealthyServices](https://msdn.microsoft.com/library/azure/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyservices.aspx). Spécifie le pourcentage maximal autorisé de services défectueux avant que l’application est considéré comme incorrecte. Par défaut pourcentage : zéro.

L’exemple suivant est un extrait d’un manifeste d’application :

```xml
    <Policies>
        <HealthPolicy ConsiderWarningAsError="true" MaxPercentUnhealthyDeployedApplications="20">
            <DefaultServiceTypeHealthPolicy
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="10"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="FrontEndServiceType"
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="20"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="BackEndServiceType"
                   MaxPercentUnhealthyServices="20"
                   MaxPercentUnhealthyPartitionsPerService="0"
                   MaxPercentUnhealthyReplicasPerPartition="0">
            </ServiceTypeHealthPolicy>
        </HealthPolicy>
    </Policies>
```

## <a name="health-evaluation"></a>Évaluation d’intégrité
Les utilisateurs et services automatisés pouvant évaluer l’intégrité d’une entité à tout moment. Pour évaluer l’intégrité d’une entité, les agrégats store santé santé tous les rapports sur l’entité et évalue tous ses enfants (le cas échéant). L’algorithme de l’agrégation d’intégrité utilise les stratégies d’intégrité qui spécifient l’évaluation des rapports d’intégrité et comment agréger les États de fonctionnement enfant (le cas échéant).

### <a name="health-report-aggregation"></a>Agrégation de rapport d’intégrité
Une entité peut avoir plusieurs rapports d’intégrité envoyés par différents journalistes (composants du système ou agents de surveillance) sur différentes propriétés. L’agrégation utilise les stratégies d’intégrité associées, notamment le membre ConsiderWarningAsError de stratégie d’application ou un cluster d’intégrité. ConsiderWarningAsError Spécifie l’évaluation des avertissements.

L’état d’intégrité agrégé est déclenchée par les rapports d’intégrité *pire* sur l’entité. S’il existe rapport d’intégrité au moins une erreur, l’état d’intégrité agrégé est une erreur.

![Agrégation de rapport d’intégrité avec le rapport d’erreurs.][2]

Une entité santé qui comporte une ou plusieurs rapports d’intégrité d’erreur est évaluée comme erreur. Il en est de même pour un rapport d’intégrité qui a expiré, quelle que soit son état d’intégrité.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

S’il existe aucun rapport d’erreurs et un ou plusieurs avertissements, l’état d’intégrité agrégé est Avertissement ou erreur, en fonction de l’indicateur de stratégie ConsiderWarningAsError.

![Agrégation de rapport d’intégrité avec rapport sur les avertissements et ConsiderWarningAsError false.][3]

Agrégation de rapport d’intégrité avec avertissement rapport et ConsiderWarningAsError définie sur false (par défaut).

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### <a name="child-health-aggregation"></a>Agrégation au niveau enfant
L’état d’intégrité agrégé d’une entité reflète les États de santé enfant (le cas échéant). L’algorithme d’agrégation des États d’intégrité enfant utilise les stratégies d’intégrité appliquées en fonction du type d’entité.

![Agrégation de santé entités enfant.][4]

Agrégation enfant en fonction des règles d’intégrité.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

Une fois que le magasin d’intégrité a évalué tous les enfants, il regroupe leurs États de fonctionnement basées sur le pourcentage maximal configuré des enfants mauvais état. Ce pourcentage provient de la stratégie en fonction du type entité et enfants.

- Si tous les enfants ont États OK, l’état d’intégrité enfant agrégé est OK.

- Si les enfants ont OK et états d’avertissement, l’état d’intégrité enfant agrégé est avertissement.

- S’il existe des enfants avec les États d’erreur qui ne respectent pas la valeur maximale autorisée pourcentage d’enfants mauvais état, l’état d’intégrité agrégé est une erreur.

- Si les enfants avec les États d’erreur respectent le pourcentage maximal autorisé d’enfants mauvais état, l’état d’intégrité agrégé est avertissement.

## <a name="health-reporting"></a>Création de rapports d’intégrité
Composants du système, des applications de tissu système et agents de surveillance interne/externe peuvent signaler entités tissu de Service. Les journalistes rendre *local* analyses de l’intégrité des entités contrôlées, basée sur les conditions analysés. Ils n’avez pas besoin de consulter les état global ou agréger les données. Le comportement souhaité est journalistes simples, ainsi que les organismes pas complexes nécessitant examiner plusieurs choses déduire les informations à envoyer.

Pour envoyer des données d’intégrité dans le magasin d’intégrité, un reporter doit identifier l’entité concernée et créer un rapport d’intégrité. Le rapport puis peut être envoyé via l’API à l’aide de [FabricClient.HealthClient.ReportHealth](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient_members.aspx), PowerShell ou autres.

### <a name="health-reports"></a>Rapports d’intégrité
Les [rapports d’intégrité](https://msdn.microsoft.com/library/azure/system.fabric.health.healthreport.aspx) pour chacune des entités du cluster contiennent les informations suivantes :

- **ID source**. Une chaîne qui identifie de façon unique le reporter de l’événement d’état.

- **Identificateur entité**. Identifie l’entité dans laquelle le rapport est appliqué. Il varie selon l' [entité tapez](service-fabric-health-introduction.md#health-entities-and-hierarchy):

  - Cluster. Aucun.

  - Nœud. Nom du nœud (chaîne).

  - Application. Nom de l’application (URI). Représente le nom de l’instance de l’application déployée dans le cluster.

  - Service. Nom de service (URI). Représente le nom de l’instance du service déployée dans le cluster.

  - Partition. ID de partition (GUID). Représente l’identificateur unique partition.

  - Réplica. L’ID de réplica avec état de service ou l’ID d’instance de service sans état (INT64).

  - DeployedApplication. Nom de l’application (URI) et le nom du nœud (chaîne).

  - DeployedServicePackage. Nom de l’application (URI), le nom de nœud (chaîne) et service manifeste name (chaîne).

- **Propriété**. *Chaîne* (pas une énumération fixe) qui permet du reporter à classer l’événement d’état pour une propriété spécifique de l’entité. Par exemple, reporter A peut signaler que l’état de la propriété Node01 « stockage » et reporter B peut signaler l’état de la propriété Node01 « connectivité ». Dans le magasin d’intégrité, ces rapports sont considérées comme des événements d’état distincts pour l’entité Node01.

- **Description**. Une chaîne qui permet un reporter fournir des informations détaillées sur l’événement d’état. **ID source**, **propriété**et **HealthState** doivent décrire entièrement le rapport. La description ajoute des informations lisible sur le rapport. Le texte simplifie pour les administrateurs et les utilisateurs à comprendre l’état d’intégrité.

- **HealthState**. [Énumération](service-fabric-health-introduction.md#health-states) qui décrit l’état d’intégrité du rapport. Les valeurs acceptées sont OK, avertissement et erreur.

- **TimeToLive**. Timespan qui indique combien de temps l’état d’intégrité est valide. Associé à **RemoveWhenExpired**, il vous permet de la banque d’intégrité cherchez à évaluer les événements qui a expiré. Par défaut, la valeur est infinie et l’état est toujours valide.

- **RemoveWhenExpired**. Valeur booléenne. Si la valeur true, l’état d’intégrité qui a expiré est automatiquement supprimée de la banque santé et le rapport n’affecte pas d’évaluation d’intégrité entité. Utilisé lorsque l’état est valide pour une période donnée uniquement, et la reporter ne doit pas explicitement effacer son. Il est également utilisé pour supprimer des rapports à partir du magasin d’intégrité (par exemple, de surveillance est modifié et arrête d’envoyer des rapports avec source précédente et propriété). Il peut envoyer un rapport avec une brève TimeToLive ainsi que RemoveWhenExpired pour libérer tout état précédent dans le magasin d’intégrité. Si la valeur est définie sur false, l’état expiré est considéré comme un message d’erreur relatif à l’évaluation d’intégrité. La valeur false signale dans le magasin d’intégrité que la source doit rapport régulièrement sur cette propriété. Si elle n’est pas, puis doit être un problème avec le minuteur. État de l’agent de surveillance est capturé en tenant compte de l’événement en tant qu’erreur.

- **Numéro de séquence**. Un nombre entier positif doit être croissante, il représente l’ordre des rapports. Il est utilisé par le magasin d’intégrité pour détecter les rapports obsolètes qui sont reçus au plus tard, en raison de retards de réseau ou d’autres problèmes. Un rapport est refusé si le numéro de séquence est qu'inférieure ou égale à la plus récemment appliquée nombre pour la même entité, la source et la propriété. S’il n’est pas spécifié, le numéro de séquence est généré automatiquement. Il est nécessaire placer le numéro de séquence uniquement lors du rapport sur les transitions d’état. Dans ce cas, la source doit mémoriser les rapports il envoyé et conserver les informations de récupération de basculement.

Ces quatre éléments d’informations, ID source, un identificateur entité, propriété et HealthState--sont requises pour tous les rapports d’intégrité. L’ID source chaîne n’est pas autorisée pour commencer avec le préfixe «**système.**», qui est réservé à un système de rapports. Pour la même entité, c’est le rapport uniquement pour la même source et la propriété. Plusieurs rapports pour la même source et propriété remplacent les uns des autres, sur le côté client d’intégrité (si elles sont regroupés) ou sur l’état stocker côté. Le remplacement est basé sur les numéros de séquence ; rapports plus récents (avec des numéros de séquence plus élevés) remplacent rapports plus anciens.

### <a name="health-events"></a>Événements d’état
En interne, le magasin d’intégrité conserve [les événements d’état](https://msdn.microsoft.com/library/azure/system.fabric.health.healthevent.aspx), qui contiennent toutes les informations dans les rapports et les métadonnées supplémentaires. Les métadonnées incluent l’heure de que l’état a été fournie au client santé et l’heure de que modification sur le côté serveur. Les événements d’état sont retournés par les [requêtes d’intégrité](service-fabric-view-entities-aggregated-health.md#health-queries).

Les métadonnées ajoutée contient :

- **SourceUtcTimestamp**. Le temps le rapport a été donné au client santé (temps universel).

- **LastModifiedUtcTimestamp**. L’heure de que dernière modification le rapport sur le côté serveur (temps universel).

- **IsExpired**. Un indicateur pour indiquer si le rapport a expiré lorsque la requête a été exécutée par le magasin d’intégrité. Un événement peut être expiré uniquement si RemoveWhenExpired a la valeur false. Dans le cas contraire, l’événement n’est pas retourné par la requête et est supprimé à partir du magasin.

- **LastOkTransitionAt**, **LastWarningTransitionAt**, **LastErrorTransitionAt**. La dernière fois pour les transitions OK/avertissement / d’erreur. Ces champs envoyer l’historique de l’état des transitions d’état de l’événement.

Les champs de transition état peuvent être utilisés pour les alertes plus intelligemment ou des informations sur les événements d’intégrité « historiques ». Ils activent scénarios tels que :

- Alerte lorsqu’une propriété a été en avertissement/erreur pour plus de X minutes. Vérification de la condition pour une période donnée permet d’éviter les alertes sur conditions temporaires. Par exemple, une alerte si l’état d’intégrité a été avertissement pendant plus de cinq minutes peut être traduite en (HealthState == avertissement et maintenant - LastWarningTransitionTime > 5 minutes).

- Alerte que dans des conditions qui ont été modifiées dans le dernier X minutes. Si un rapport était déjà en erreur avant l’heure spécifiée, il peut être ignoré, car il a été déjà signalé précédemment.

- Si une propriété est basculer entre l’avertissement et d’erreur, déterminez la durée pendant laquelle il a été défectueux (autrement dit, pas OK). Par exemple, une alerte si la propriété n’a pas été exact pendant plus de cinq minutes peut être traduite en (HealthState ! = Ok et maintenant - LastOkTransitionTime > 5 minutes).

## <a name="example-report-and-evaluate-application-health"></a>Exemple : Créer des rapports et évaluer l’intégrité de l’application
L’exemple suivant envoie un rapport d’intégrité via PowerShell sur l’application **TISSU : / WordCount** provenant de la source **MyWatchdog**. L’état d’intégrité contient des informations sur la propriété santé « disponibilité » dans un état d’intégrité d’erreur, avec TimeToLive infini. Il demande ensuite l’état d’application, qui retourne agrégé erreurs d’état d’intégrité et les événements d’état signalés dans la liste des événements d’état.

```powershell
PS C:\> Send-ServiceFabricApplicationHealthReport –ApplicationName fabric:/WordCount –SourceId "MyWatchdog" –HealthProperty "Availability" –HealthState Error

PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Error event: SourceId='MyWatchdog', Property='Availability'.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 360
                                  SentAt                : 3/22/2016 7:56:53 PM
                                  ReceivedAt            : 3/22/2016 7:56:53 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 7:56:53 PM, LastWarning = 1/1/0001 12:00:00 AM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Error
                                  SequenceNumber        : 131032204762818013
                                  SentAt                : 3/23/2016 3:27:56 PM
                                  ReceivedAt            : 3/23/2016 3:27:56 PM
                                  TTL                   : Infinite
                                  Description           :
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Ok->Error = 3/23/2016 3:27:56 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="health-model-usage"></a>Utilisation du modèle d’intégrité
Le modèle de fonctionnement permet aux services cloud et la plateforme sous-jacente tissu de Service à l’échelle, car les analyses de surveillance et la santé sont réparties sur les différents écrans au sein du cluster.
Autres systèmes propose un service unique et centralisé au niveau du cluster qui analyse toutes les *potentiellement* utiles informations émises par les services. Cette approche empêche leur extensibilité élevées. Également ne leur permettre de recueillir les informations très spécifiques pour aider à identifier les problèmes et les éventuels problèmes en tant que près de la cause possible.

Le modèle de fonctionnement sert intensément contrôlée mises à jour de surveillance et de diagnostic et pour l’évaluation d’intégrité cluster et d’application. D’autres services utilisent d’intégrité des données à effectuer réparations automatiques, générer historique des résultats cluster et émettre des alertes dans certaines conditions.

## <a name="next-steps"></a>Étapes suivantes
[Afficher les rapports d’intégrité tissu de Service](service-fabric-view-entities-aggregated-health.md)

[Utiliser les rapports d’intégrité du système de résoudre les problèmes](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Comment créer des rapports et vérifiez l’état du service](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Ajouter des rapports d’intégrité TISSU Service personnalisés](service-fabric-report-health.md)

[Surveiller et diagnostiquer les services localement](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Mise à niveau de service TISSU application](service-fabric-application-upgrade.md)
