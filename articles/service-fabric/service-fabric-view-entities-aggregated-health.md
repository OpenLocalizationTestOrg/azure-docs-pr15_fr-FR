<properties
   pageTitle="Comment afficher les entités Azure Service TISSU agrégé santé | Microsoft Azure"
   description="Décrit comment interroger, afficher et évaluer l’intégrité agrégé entités Azure Service tissu, par le biais d’intégrité des requêtes et requêtes générales."
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

# <a name="view-service-fabric-health-reports"></a>Afficher les rapports d’intégrité tissu de Service
TISSU Service Azure présente un [modèle de fonctionnement](service-fabric-health-introduction.md) qui comprend des entités intégrité du système composants et agents de surveillance peuvent signaler conditions locales analysés. La [santé stocker](service-fabric-health-introduction.md#health-store) regroupe toutes les données d’intégrité pour déterminer si entités fonctionnent correctement.

Prêts à l’emploi, le cluster est rempli avec des rapports d’intégrité envoyés par les composants du système. En savoir plus à [utiliser des rapports d’intégrité système pour résoudre les problèmes](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Service TISSU fournit plusieurs méthodes pour obtenir l’état agrégé des entités :

- [Explorer tissu de service](service-fabric-visualizing-your-cluster.md) ou d’autres outils de visualisation

- Requêtes santé (via PowerShell, API ou reste)

- Général des requêtes qui retournent une liste des personnes qui ont des santé dans l’un des propriétés (via PowerShell, API ou reste)

Pour illustrer ces options, nous allons utiliser un cluster local avec cinq nœuds. À côté du **TISSU : / système** application (qui se trouve en dehors de la zone), d’autres applications sont déployées. Un de ces applications est **TISSU : / WordCount**. Cette application contient un service dynamique configuré avec sept réplicas. Étant donné que cinq nœuds, les composants du système affiche un avertissement qui la partition se trouve sous le nombre de cibles.

```xml
<Service Name="WordCountService">
    <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
      <UniformInt64Partition PartitionCount="1" LowKey="1" HighKey="26" />
    </StatefulService>
</Service>
```

## <a name="health-in-service-fabric-explorer"></a>Santé dans l’Explorateur de tissu de Service
Service TISSU Explorer fournit un affichage visuel du cluster. Dans l’image ci-dessous, vous pouvez constater que :

- L’application **TISSU : / WordCount** est rouge (par erreur), car il comporte un événement d’erreur signalé par **MyWatchdog** pour la propriété **disponibilité**.

- Une de ses services, **TISSU : / WordCount/WordCountService** est jaune (dans le message d’avertissement). Étant donné que le service est configuré avec sept réplicas, ils ne peuvent pas toutes être placés, étant donné que cinq nœuds. Bien qu’il ne figure pas ici, la partition de service est jaune en raison de l’état du système. La partition jaune déclenche le service jaune.

- Le cluster est rouge en raison de l’application rouge.

L’évaluation utilise des stratégies par défaut à partir du manifeste cluster et manifeste d’application. Ils sont des stratégies strictes et ne tolèrent pas une défaillance.

Affichage du cluster avec l’Explorateur de tissu de Service :

![Affichage du cluster avec l’Explorateur de tissu de Service.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [AZURE.NOTE] En savoir plus sur [Service TISSU Explorer](service-fabric-visualizing-your-cluster.md).

## <a name="health-queries"></a>Requêtes d’intégrité
Service TISSU expose requêtes santé pour chacun des [types d’entités](service-fabric-health-introduction.md#health-entities-and-hierarchy)pris en charge. Ils sont accessibles via l’API (les méthodes sont accessibles sous **FabricClient.HealthManager**), applets de commande PowerShell et reste. Ces requêtes renvoient santé complète d’informations sur l’entité : l’état d’intégrité agrégé, entité santé, États d’intégrité enfant (le cas échéant) et événements évaluations incorrectes lorsque l’entité n’est pas correcte.

> [AZURE.NOTE] Une entité santé est renvoyée lorsqu’il est rempli entièrement dans le magasin d’intégrité. L’entité doit être active (ne pas supprimés) et disposez d’un état système. Ses entités parent de la chaîne de hiérarchie doivent comporter rapports du système. Si une de ces conditions n’est pas remplie, les requêtes d’intégrité retournent une exception indiquant la raison pour laquelle l’entité n’est pas retournée.

Les requêtes d’intégrité doivent passer l’identificateur entité, qui dépend du type d’entité. Les requêtes acceptent les paramètres de stratégie d’intégrité facultatif. Si aucune stratégie d’intégrité n’est spécifiés, les [stratégies d’intégrité](service-fabric-health-introduction.md#health-policies) à partir du manifeste cluster ou une application sont utilisées pour l’évaluation. Les requêtes acceptent également les filtres pour renvoyer uniquement des enfants partielles ou des événements--celles qui respectent les filtres spécifiés.

> [AZURE.NOTE] Les filtres de sortie sont appliquées sur le côté serveur, afin que la taille de la réponse du message est réduite. Il est recommandé que vous utilisez les filtres de sortie pour limiter les données renvoyées, au lieu d’appliquez des filtres sur le côté client.

Au niveau d’une entité contient :

- L’état d’intégrité agrégé de l’entité. Vous souhaitez calculer par le magasin d’intégrité basé sur rapports d’intégrité entité, les États de fonctionnement enfant (le cas échéant) et les stratégies d’intégrité. En savoir plus sur [l’évaluation de santé entité](service-fabric-health-introduction.md#entity-health-evaluation).  

- Les événements d’état de l’entité.

- Collection des États de fonctionnement de tous les enfants pour les entités qui peuvent avoir des enfants. États de fonctionnement contiennent les identificateurs d’entité et l’état d’intégrité agrégé. Pour obtenir d’intégrité complète pour enfant, appelez le fonctionnement de la requête pour le type d’entité enfant et passez l’identificateur enfant.

- Les évaluations défectueuses pointent sur l’état qui a déclenché l’état de l’entité, si l’entité n’est pas correcte.

## <a name="get-cluster-health"></a>Obtenir le bon fonctionnement du cluster
Renvoie l’état de l’entité cluster et contient les États de fonctionnement des applications et des nœuds (enfants du cluster). Entrée :

- [Facultatif] La stratégie d’intégrité de cluster permet d’évaluer les nœuds et les événements de cluster.

- [Facultatif] L’application d’intégrité correspondances des stratégies, avec les stratégies d’intégrité permet de remplacer les stratégies manifeste d’application.

- [Facultatif] Filtres pour les applications qui spécifient les entrées, les nœuds et les événements sont utiles et doivent être retournées dans le résultat (par exemple, uniquement, les erreurs ou avertissements et erreurs). Tous les événements, les nœuds et les applications sont utilisées pour évaluer l’intégrité entité agrégée, quel que soit le filtre.

### <a name="api"></a>API
Pour obtenir le bon fonctionnement du cluster, créez un `FabricClient` et appelez la méthode [GetClusterHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getclusterhealthasync.aspx) sur son **HealthManager**.

L’appel suivant obtient le bon fonctionnement du cluster :

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

Le code suivant obtient le bon fonctionnement du cluster à l’aide d’une stratégie d’intégrité cluster personnalisé et des filtres pour les applications et les nœuds. Il crée [ClusterHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.clusterhealthquerydescription.aspx), qui contient les informations d’entrée.

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error | HealthStateFilter.Warning
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
};

ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
L’applet de commande pour obtenir le bon fonctionnement du cluster est [Get-ServiceFabricClusterHealth](https://msdn.microsoft.com/library/mt125850.aspx). Tout d’abord, connectez-vous au cluster à l’aide de l’applet de commande [ServiceFabricCluster de se connecter](https://msdn.microsoft.com/library/mt125938.aspx) .

L’état du cluster est cinq nœuds, l’application système et tissu : / WordCount configuré comme indiqué.

L’applet de commande suivante obtient santé cluster à l’aide de stratégies d’intégrité par défaut. L’état d’intégrité agrégé est avertissement, car le tissu : / application WordCount se trouve dans avertissement. Remarque : comment les évaluations défectueuses fournissent des détails sur les conditions qui a déclenché l’intégrité agrégée.

```xml
PS C:\> Get-ServiceFabricClusterHealth

AggregatedHealthState   : Warning
UnhealthyEvaluations    :
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.

                              Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                              Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                  Unhealthy event: SourceId='System.PLB',
                          Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                          ConsiderWarningAsError=false.


NodeHealthStates        :
                          NodeName              : _Node_2
                          AggregatedHealthState : Ok

                          NodeName              : _Node_0
                          AggregatedHealthState : Ok

                          NodeName              : _Node_1
                          AggregatedHealthState : Ok

                          NodeName              : _Node_3
                          AggregatedHealthState : Ok

                          NodeName              : _Node_4
                          AggregatedHealthState : Ok

ApplicationHealthStates :
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning

HealthEvents            : None
```

L’applet de commande PowerShell suivante obtient l’état du cluster à l’aide d’une stratégie d’application personnalisée. Il filtre les résultats pour obtenir uniquement d’erreur ou applications avertissement et nœuds. Par conséquent, aucune nœuds ne sont retournés, qu’ils sont tous les correct. Uniquement la structure : / application WordCount respecte le filtre applications. Étant donné que la stratégie personnalisée spécifie à prendre en considération les avertissements comme des erreurs pour tissu : / application WordCount, l’application est évaluée comme dans erreur et n’est donc le cluster.

```powershell
PS c:\> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error"


AggregatedHealthState   : Error
UnhealthyEvaluations    :
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.

                              Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                              Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                  Unhealthy event: SourceId='System.PLB',
                          Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                          ConsiderWarningAsError=true.


NodeHealthStates        : None
ApplicationHealthStates :
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error

HealthEvents            : None

```

### <a name="rest"></a>RESTE
Vous pouvez obtenir le bon fonctionnement du cluster avec un [obtenir demande](https://msdn.microsoft.com/library/azure/dn707669.aspx) ou une [demande de publication](https://msdn.microsoft.com/library/azure/dn707696.aspx) qui inclut les stratégies d’intégrité décrits dans le corps.

## <a name="get-node-health"></a>Obtenir l’état du nœud
Renvoie l’état d’une entité nœud et contient les événements d’état signalés sur le nœud. Entrée :

- [Obligatoire] Le nom du nœud qui identifie le nœud.

- [Facultatif] Les paramètres de stratégie d’intégrité cluster qui permet d’évaluer la santé.

- [Facultatif] Filtres pour les événements qui indiquent les entrées sont utiles et doivent être retournées dans le résultat (par exemple, uniquement, les erreurs ou avertissements et erreurs). Tous les événements sont utilisées pour évaluer la santé entité agrégée, quel que soit le filtre.

### <a name="api"></a>API
Pour obtenir la santé nœud via l’API, créez un `FabricClient` et appelez la méthode [GetNodeHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getnodehealthasync.aspx) sur son HealthManager.

Le code suivant obtient l’état de nœud pour le nom du nœud spécifié :

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

Le code suivant obtient l’état de nœud pour le nom du nœud spécifié et transmission événements filtre et stratégie personnalisée à [NodeHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.nodehealthquerydescription.aspx):

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
L’applet de commande pour obtenir l’état de nœud est [Get-ServiceFabricNodeHealth](https://msdn.microsoft.com/library/mt125937.aspx). Tout d’abord, connectez-vous au cluster à l’aide de l’applet de commande [ServiceFabricCluster de se connecter](https://msdn.microsoft.com/library/mt125938.aspx) .
L’applet de commande suivante obtient l’état de nœud à l’aide de stratégies d’intégrité par défaut :

```powershell
PS C:\> Get-ServiceFabricNodeHealth _Node_1


NodeName              : _Node_1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 6
                        SentAt                : 3/22/2016 7:47:56 PM
                        ReceivedAt            : 3/22/2016 7:48:19 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:48:19 PM, LastWarning = 1/1/0001 12:00:00 AM
```

L’applet de commande suivante obtient l’état de tous les nœuds du cluster :

```powershell
PS C:\> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
_Node_2                     Ok
_Node_0                     Ok
_Node_1                     Ok
_Node_3                     Ok
_Node_4                     Ok
```

### <a name="rest"></a>RESTE
Vous pouvez accéder au niveau de nœud avec une [prise demande](https://msdn.microsoft.com/library/azure/dn707650.aspx) ou une [demande de publication](https://msdn.microsoft.com/library/azure/dn707665.aspx) qui inclut les stratégies d’intégrité décrits dans le corps.

## <a name="get-application-health"></a>Obtenir santé des applications
Renvoie l’état d’une entité d’application. Il contient les États d’intégrité de l’application déployée et enfants de service. Entrée :

- [Obligatoire] Le nom de l’application (URI) qui identifie l’application.

- [Facultatif] La stratégie d’intégrité d’application permet de remplacer les stratégies manifeste d’application.

- [Facultatif] Filtres pour les événements, services et applications déployées qui indiquent les entrées sont utiles et doivent être retournées dans le résultat (par exemple, uniquement, les erreurs ou avertissements et erreurs). Tous les événements, les services et applications déployées servent à évaluer l’intégrité entité agrégée, quel que soit le filtre.

### <a name="api"></a>API
Pour obtenir de l’état de l’application, créez un `FabricClient` et appelez la méthode [GetApplicationHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getapplicationhealthasync.aspx) sur son HealthManager.

Le code suivant obtient l’état de l’application de nom d’application (URI) :

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

Le code suivant obtient l’état de l’application pour le nom de l’application spécifiée (URI), avec les filtres et les stratégies personnalisées spécifiées via [ApplicationHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.applicationhealthquerydescription.aspx).

```csharp
HealthStateFilter warningAndErrors = HealthStateFilter.Error | HealthStateFilter.Warning;
var serviceTypePolicy = new ServiceTypeHealthPolicy()
{
    MaxPercentUnhealthyPartitionsPerService = 0,
    MaxPercentUnhealthyReplicasPerPartition = 5,
    MaxPercentUnhealthyServices = 0,
};
var policy = new ApplicationHealthPolicy()
{
    ConsiderWarningAsError = false,
    DefaultServiceTypeHealthPolicy = serviceTypePolicy,
    MaxPercentUnhealthyDeployedApplications = 0,
};

var queryDescription = new ApplicationHealthQueryDescription(applicationName)
{
    HealthPolicy = policy,
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
};

ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
L’applet de commande pour obtenir l’état de l’application est [Get-ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx). Tout d’abord, connectez-vous au cluster à l’aide de l’applet de commande [ServiceFabricCluster de se connecter](https://msdn.microsoft.com/library/mt125938.aspx) .

L’applet de commande suivante renvoie l’état de la **TISSU : / WordCount** application :

```powershell
PS c:\>
PS C:\WINDOWS\system32>  Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                      Unhealthy event: SourceId='System.PLB',
                                  Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                                  ConsiderWarningAsError=false.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Warning

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
                                  HealthState           : Ok
                                  SequenceNumber        : 131031545225930951
                                  SentAt                : 3/22/2016 9:08:42 PM
                                  ReceivedAt            : 3/22/2016 9:08:42 PM
                                  TTL                   : Infinite
                                  Description           : Availability checked successfully, latency ok
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 8:55:39 PM, LastWarning = 1/1/0001 12:00:00 AM
```

L’applet de commande PowerShell suivante passe stratégies personnalisées. Il filtre également les enfants et les événements.

```powershell
PS C:\> Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesFilter Error -EventsFilter Error -DeployedApplicationsFilter Error

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy event: SourceId='System.PLB',
                                  Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                                  ConsiderWarningAsError=true.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

DeployedApplicationHealthStates : None
HealthEvents                    : None
```

### <a name="rest"></a>RESTE
Vous pouvez obtenir santé des applications avec une [prise demande](https://msdn.microsoft.com/library/azure/dn707681.aspx) ou une [demande de publication](https://msdn.microsoft.com/library/azure/dn707643.aspx) qui inclut les stratégies d’intégrité décrits dans le corps.

## <a name="get-service-health"></a>Obtenir l’état du service
Renvoie l’état d’une entité de service. Il contient les États de santé partition. Entrée :

- [Obligatoire] Le nom du service (URI) qui identifie le service.

- [Facultatif] La stratégie d’intégrité d’application permet de remplacer la stratégie manifeste d’application.

- [Facultatif] Filtres partitions qui indiquent les entrées et les événements sont utiles et doivent être retournées dans le résultat (par exemple, uniquement, les erreurs ou avertissements et erreurs). Tous les événements et partitions servent à évaluer l’intégrité entité agrégée, quel que soit le filtre.

### <a name="api"></a>API
Pour obtenir l’état du service à l’API, créez un `FabricClient` et appelez la méthode [GetServiceHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getservicehealthasync.aspx) sur son HealthManager.

L’exemple suivant obtient l’état d’un service avec le nom de service spécifié (URI) :

```charp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

Le code suivant obtient l’intégrité du service pour le nom du service spécifié (URI), et en indiquant les filtres et stratégie personnalisée via [ServiceHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.servicehealthquerydescription.aspx):

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
L’applet de commande pour obtenir l’état du service est [Get-ServiceFabricServiceHealth](https://msdn.microsoft.com/library/mt125984.aspx). Tout d’abord, connectez-vous au cluster à l’aide de l’applet de commande [ServiceFabricCluster de se connecter](https://msdn.microsoft.com/library/mt125938.aspx) .

L’applet de commande suivante obtient l’état du service à l’aide de stratégies d’intégrité par défaut :

```powershell
PS C:\> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCountService


ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB',
                        Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                        ConsiderWarningAsError=false.

PartitionHealthStates :
                        PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
                        AggregatedHealthState : Warning

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 10
                        SentAt                : 3/22/2016 7:56:53 PM
                        ReceivedAt            : 3/22/2016 7:57:18 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b
                        HealthState           : Warning
                        SequenceNumber        : 131031547693687021
                        SentAt                : 3/22/2016 9:12:49 PM
                        ReceivedAt            : 3/22/2016 9:12:49 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        fabric:/WordCount/WordCountService Secondary Partition a1f83a35-d6bf-4d39-b90d-28d15f39599b could not be placed, possibly,
                        due to the following constraints and properties:  
                        Placement Constraint: N/A
                        Depended Service: N/A

                        Constraint Elimination Sequence:
                        ReplicaExclusionStatic eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        ReplicaExclusionDynamic eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.

                        Nodes Eliminated By Constraints:

                        ReplicaExclusionStatic:
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status:
                        None/None

                        ReplicaExclusionDynamic:
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status:
                        None/None


                        RemoveWhenExpired     : True
                        IsExpired             : False
```

### <a name="rest"></a>RESTE
Vous pouvez obtenir l’état du service avec une [demande de prise](https://msdn.microsoft.com/library/azure/dn707609.aspx) ou une [demande de publication](https://msdn.microsoft.com/library/azure/dn707646.aspx) qui inclut les stratégies d’intégrité décrits dans le corps.

## <a name="get-partition-health"></a>Accéder au niveau partition
Renvoie l’état d’une entité partition. Il contient les États de santé réplica. Entrée :

- [Obligatoire] Partition ID (GUID) qui identifie la partition.

- [Facultatif] La stratégie d’intégrité d’application permet de remplacer la stratégie manifeste d’application.

- [Facultatif] Filtres duplications, spécifiez les entrées et les événements sont utiles et doivent être retournées dans le résultat (par exemple, uniquement, les erreurs ou avertissements et erreurs). Tous les événements et duplications servent à évaluer l’intégrité entité agrégée, quel que soit le filtre.

### <a name="api"></a>API
Pour obtenir la santé partition via l’API, créez un `FabricClient` et appelez la méthode [GetPartitionHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getpartitionhealthasync.aspx) sur son HealthManager. Pour spécifier les paramètres facultatifs, créez [PartitionHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.partitionhealthquerydescription.aspx).

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### <a name="powershell"></a>PowerShell
L’applet de commande pour obtenir l’état de partition est [Get-ServiceFabricPartitionHealth](https://msdn.microsoft.com/library/mt125869.aspx). Tout d’abord, connectez-vous au cluster à l’aide de l’applet de commande [ServiceFabricCluster de se connecter](https://msdn.microsoft.com/library/mt125938.aspx) .

L’applet de commande suivante obtient l’état de toutes les partitions de la **TISSU : / WordCount/WordCountService** service :

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth


PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 131031502143040223
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844060
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844059
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844061
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844058
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 76
                        SentAt                : 3/22/2016 7:57:26 PM
                        ReceivedAt            : 3/22/2016 7:57:48 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 3/22/2016 7:57:48 PM, LastOk = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>RESTE
Vous pouvez accéder au niveau partition avec une [prise demande](https://msdn.microsoft.com/library/azure/dn707683.aspx) ou une [demande de publication](https://msdn.microsoft.com/library/azure/dn707680.aspx) qui inclut les stratégies d’intégrité décrits dans le corps.

## <a name="get-replica-health"></a>Obtenir l’état du réplica
Renvoie l’état d’un service dynamique réplica ou une instance de service sans état. Entrée :

- [Obligatoire] L’ID (GUID) et réplica ID de partition qui identifie le réplica.

- [Facultatif] Les paramètres de stratégie d’intégrité application qui permet de remplacer les stratégies manifeste d’application.

- [Facultatif] Filtres pour les événements qui indiquent les entrées sont utiles et doivent être retournées dans le résultat (par exemple, uniquement, les erreurs ou avertissements et erreurs). Tous les événements sont utilisées pour évaluer la santé entité agrégée, quel que soit le filtre.

### <a name="api"></a>API
Pour obtenir l’état de copie à l’API, créez un `FabricClient` et appelez la méthode [GetReplicaHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getreplicahealthasync.aspx) sur son HealthManager. Pour spécifier les paramètres avancés, utilisez [ReplicaHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.replicahealthquerydescription.aspx).

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### <a name="powershell"></a>PowerShell
L’applet de commande pour obtenir l’état de réplica est [Get-ServiceFabricReplicaHealth](https://msdn.microsoft.com/library/mt125808.aspx). Tout d’abord, connectez-vous au cluster à l’aide de l’applet de commande [ServiceFabricCluster de se connecter](https://msdn.microsoft.com/library/mt125938.aspx) .

L’applet de commande suivante obtient l’état du réplica principal pour toutes les partitions du service :

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth


PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
ReplicaId             : 131031502143040223
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131031502145556748
                        SentAt                : 3/22/2016 7:56:54 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>RESTE
Vous pouvez accéder au niveau réplica avec une [prise demande](https://msdn.microsoft.com/library/azure/dn707673.aspx) ou une [demande de publication](https://msdn.microsoft.com/library/azure/dn707641.aspx) qui inclut les stratégies d’intégrité décrits dans le corps.

## <a name="get-deployed-application-health"></a>Obtenir l’application déployée santé
Renvoie l’état d’une application déployée sur une entité nœud. Il contient les États d’intégrité du service déployé package. Entrée :

- [Obligatoire] Le nom de l’application (URI) et le nœud nom (chaîne) qui identifient l’application déployée.

- [Facultatif] La stratégie d’intégrité d’application permet de remplacer les stratégies manifeste d’application.

- [Facultatif] Filtres pour les événements et les packages service déployé qui indiquent les entrées sont utiles et doivent être retournées dans le résultat (par exemple, uniquement, les erreurs ou avertissements et erreurs). Tous les événements et service déployé packages servent à évaluer l’intégrité entité agrégée, quel que soit le filtre.

### <a name="api"></a>API
Pour obtenir l’état d’une application déployée sur un nœud via l’API, créez un `FabricClient` et appelez la méthode [GetDeployedApplicationHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync.aspx) sur son HealthManager. Pour spécifier les paramètres facultatifs, utilisez [DeployedApplicationHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.deployedapplicationhealthquerydescription.aspx).

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### <a name="powershell"></a>PowerShell
L’applet de commande pour obtenir l’état de l’application déployée est [Get-ServiceFabricDeployedApplicationHealth](https://msdn.microsoft.com/library/mt163523.aspx). Tout d’abord, connectez-vous au cluster à l’aide de l’applet de commande [ServiceFabricCluster de se connecter](https://msdn.microsoft.com/library/mt125938.aspx) . Pour déterminer l’endroit où une application est déployée, exécutez [Get-ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx) et consultez les enfants application déployée.

L’applet de commande suivante obtient l’état de la **TISSU : / WordCount** application déployée sur **_Node_2**.

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName _Node_2


ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_2
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCountServicePkg
                                     NodeName              : _Node_2
                                     AggregatedHealthState : Ok

                                     ServiceManifestName   : WordCountWebServicePkg
                                     NodeName              : _Node_2
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131031502143710698
                                     SentAt                : 3/22/2016 7:56:54 PM
                                     ReceivedAt            : 3/22/2016 7:57:12 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>RESTE
Vous pouvez accéder au niveau application déployée avec une [prise demande](https://msdn.microsoft.com/library/azure/dn707644.aspx) ou une [demande de publication](https://msdn.microsoft.com/library/azure/dn707688.aspx) qui inclut les stratégies d’intégrité décrits dans le corps.

## <a name="get-deployed-service-package-health"></a>Obtenir l’état du service déployé package
Renvoie l’état d’une entité package service déployé. Entrée :

- [Obligatoire] Nom (chaîne) qui identifient le package service déployé du manifeste le nom de l’application (URI), le nom du nœud (chaîne) et le service.

- [Facultatif] La stratégie d’intégrité d’application permet de remplacer la stratégie manifeste d’application.

- [Facultatif] Filtres pour les événements qui indiquent les entrées sont utiles et doivent être retournées dans le résultat (par exemple, uniquement, les erreurs ou avertissements et erreurs). Tous les événements sont utilisées pour évaluer la santé entité agrégée, quel que soit le filtre.

### <a name="api"></a>API
Pour obtenir l’état d’un package de service déployé via l’API, créez un `FabricClient` et appelez la méthode [GetDeployedServicePackageHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync.aspx) sur son HealthManager. Pour spécifier les paramètres facultatifs, utilisez [DeployedServicePackageHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.deployedservicepackagehealthquerydescription.aspx).

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### <a name="powershell"></a>PowerShell
L’applet de commande pour obtenir l’état de package service déployé est [Get-ServiceFabricDeployedServicePackageHealth](https://msdn.microsoft.com/library/mt163525.aspx). Tout d’abord, connectez-vous au cluster à l’aide de l’applet de commande [ServiceFabricCluster de se connecter](https://msdn.microsoft.com/library/mt125938.aspx) . Pour voir où une application est déployée, exécutez [Get-ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx) et consulter les applications déployées. Pour connaître le packages sont dans une application de service, consultez les enfants package service déployé dans le résultat de [Get-ServiceFabricDeployedApplicationHealth](https://msdn.microsoft.com/library/mt163523.aspx) .

L’applet de commande suivante obtient l’état du package de service **WordCountServicePkg** de la **TISSU : / WordCount** application déployée sur **_Node_2**. L’entité a **System.Hosting** rapports pour la réussite du lot de service et l’activation du point d’entrée et l’enregistrement est effectué Type_Service.

```powershell
PS C:\> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName _Node_2 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCountServicePkg


ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCountServicePkg
NodeName              : _Node_2
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 131031502301306211
                        SentAt                : 3/22/2016 7:57:10 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 131031502301568982
                        SentAt                : 3/22/2016 7:57:10 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCountServiceType
                        HealthState           : Ok
                        SequenceNumber        : 131031502314788519
                        SentAt                : 3/22/2016 7:57:11 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>RESTE
Vous pouvez accéder au niveau de package de service déployé avec une [prise demande](https://msdn.microsoft.com/library/azure/dn707677.aspx) ou une [demande de publication](https://msdn.microsoft.com/library/azure/dn707689.aspx) qui inclut les stratégies d’intégrité décrits dans le corps.

## <a name="health-chunk-queries"></a>Requêtes de segment d’intégrité
Les requêtes de segment d’intégrité peuvent renvoyer enfants cluster à plusieurs niveaux (récursive) par les filtres d’entrée. Il prend en charge les filtres avancés qui permettent flexibilité exprimer les enfants spécifiques à retourner, identifiées par leur identificateur unique ou autres identificateur de groupe et/ou l’état d’intégrité. Par défaut, aucune enfants ne sont inclus, contrairement à des commandes d’intégrité qui incluent toujours enfants de premier niveau.

Les [requêtes d’intégrité](service-fabric-view-entities-aggregated-health.md#health-queries) renvoient uniquement les enfants de premier niveau de l’entité spécifiée par les filtres requis. Pour obtenir les enfants des enfants, vous devez appeler supplémentaires d’intégrité API pour chaque entité d’intérêt. De même, pour obtenir l’état d’entités spécifiques, vous devez appeler une santé API pour chaque entité souhaitée. La requête segment filtrage avancée permet de demander plusieurs éléments d’intérêt dans une requête, en réduisant la taille du message et le nombre de messages.

La valeur de la requête segment est que vous pouvez obtenir état d’intégrité pour plusieurs entités cluster (potentiellement toutes les cluster entités en commençant à la racine requis) en un seul appel. Vous pouvez exprimer requête santé complexes tels que :

- Seules les applications en erreur et pour les applications incluent tous les services en avertissement de retour | erreur. Pour les services renvoyées, incluez toutes les partitions.

- Renvoyer uniquement l’état du 4 applications, spécifiées par leur nom.

- Renvoyer uniquement l’état des applications d’un type de l’application souhaitée.

- Retour entités tout déployées sur un nœud. Renvoie toutes les applications, tous les déploiement tous les packages service déployé sur ce nœud et applications sur le nœud spécifié.

- Renvoie tous les réplicas en erreur. Renvoie toutes les applications, services, partitions et uniquement les réplicas à l’erreur.

- Renvoie toutes les applications. Pour un service spécifié, incluez toutes les partitions.

Pour l’instant, la requête de segment d’intégrité est exposée uniquement pour l’entité cluster. Elle retourne un morceau de santé cluster, qui contient :

- L’état d’intégrité cluster agrégé.

- La liste de segment état d’intégrité des nœuds qui respectent les filtres d’entrée.

- La liste de segment état d’intégrité des applications qui respectent les filtres d’entrée. Chaque morceau application santé état contient une liste de segment avec tous les services qui respectent une liste de blocs avec toutes les applications déployées qui respectent les filtres et les filtres d’entrée. Même pour les enfants de services et applications déployées. De cette façon, toutes les entités dans le cluster peuvent être potentiellement retournées si nécessaire, de manière hiérarchique.

### <a name="cluster-health-chunk-query"></a>Requête de segment cluster d’intégrité
Renvoie l’état de l’entité cluster et contient les segments état d’intégrité hiérarchique des enfants requis. Entrée :

- [Facultatif] La stratégie d’intégrité de cluster permet d’évaluer les nœuds et les événements de cluster.

- [Facultatif] L’application d’intégrité correspondances des stratégies, avec les stratégies d’intégrité permet de remplacer les stratégies manifeste d’application.

- [Facultatif] Filtres pour les applications qui indiquent les entrées et les nœuds sont utiles et doivent être retournées dans le résultat. Les filtres sont spécifiques à une entité/groupe d’entités ou ne s’appliquent à toutes les entités à ce niveau. La liste de filtres peut contenir un filtre général et/ou des filtres pour les identificateurs spécifiques aux entités précis renvoyées par la requête. Si elle est vide, les enfants ne sont pas retournés par défaut.
En savoir plus sur les filtres en [NodeHealthStateFilter](https://msdn.microsoft.com/library/azure/system.fabric.health.nodehealthstatefilter.aspx) et [ApplicationHealthStateFilter](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthstatefilter.aspx). La manière application filtres can récursive spécifier les filtres avancés pour les enfants.

Le résultat de segment inclut les enfants qui respectent les filtres.

Pour l’instant, la requête segment ne renvoie pas évaluations défectueuses ou événements entité. Ces informations supplémentaires peuvent être obtenues à l’aide de la requête d’intégrité cluster existante.

### <a name="api"></a>API
Pour obtenir un segment d’intégrité de cluster, créez un `FabricClient` et appelez la méthode [GetClusterHealthChunkAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync.aspx) sur son **HealthManager**. Vous pouvez passer [ClusterHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.clusterhealthchunkquerydescription.aspx) pour décrire les stratégies d’intégrité et filtres avancés.

Le code suivant extrait cluster santé bloc avec les filtres avancés.

```csharp
var queryDescription = new ClusterHealthChunkQueryDescription();
queryDescription.ApplicationFilters.Add(new ApplicationHealthStateFilter()
    {
        // Return applications only if they are in error
        HealthStateFilter = HealthStateFilter.Error
    });

// Return all replicas
var wordCountServiceReplicaFilter = new ReplicaHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };

// Return all replicas and all partitions
var wordCountServicePartitionFilter = new PartitionHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };
wordCountServicePartitionFilter.ReplicaFilters.Add(wordCountServiceReplicaFilter);

// For specific service, return all partitions and all replicas
var wordCountServiceFilter = new ServiceHealthStateFilter()
{
    ServiceNameFilter = new Uri("fabric:/WordCount/WordCountService"),
};
wordCountServiceFilter.PartitionFilters.Add(wordCountServicePartitionFilter);

// Application filter: for specific application, return no services except the ones of interest
var wordCountApplicationFilter = new ApplicationHealthStateFilter()
    {
        // Always return fabric:/WordCount application
        ApplicationNameFilter = new Uri("fabric:/WordCount"),
    };
wordCountApplicationFilter.ServiceFilters.Add(wordCountServiceFilter);

queryDescription.ApplicationFilters.Add(wordCountApplicationFilter);

var result = await fabricClient.HealthManager.GetClusterHealthChunkAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
L’applet de commande pour obtenir le bon fonctionnement du cluster est [Get-ServiceFabricClusterChunkHealth](https://msdn.microsoft.com/library/mt644772.aspx). Tout d’abord, connectez-vous au cluster à l’aide de l’applet de commande [ServiceFabricCluster de se connecter](https://msdn.microsoft.com/library/mt125938.aspx) .

Le code suivant obtient nœuds uniquement s’ils sont par erreur à l’exception d’un nœud spécifique, qui doit toujours être retourné.

```xml
PS C:\> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$nodeFilter1 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{HealthStateFilter=$errorFilter}
$nodeFilter2 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{NodeNameFilter="_Node_1";HealthStateFilter=$allFilter}
# Create node filter list that will be passed in the cmdlet
$nodeFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.NodeHealthStateFilter]
$nodeFilters.Add($nodeFilter1)
$nodeFilters.Add($nodeFilter2)

Get-ServiceFabricClusterHealthChunk -NodeFilters $nodeFilters

HealthState                  : Error
NodeHealthStateChunks        :
                               TotalCount            : 1

                               NodeName              : _Node_1
                               HealthState           : Ok

ApplicationHealthStateChunks : None
```

L’applet de commande suivante extrait bloc cluster avec les filtres d’application.

```xml
$errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

# All replicas
$replicaFilter = New-Object System.Fabric.Health.ReplicaHealthStateFilter -Property @{HealthStateFilter=$allFilter}

# All partitions
$partitionFilter = New-Object System.Fabric.Health.PartitionHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$partitionFilter.ReplicaFilters.Add($replicaFilter)

# For WordCountService, return all partitions and all replicas
$svcFilter1 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{ServiceNameFilter="fabric:/WordCount/WordCountService"}
$svcFilter1.PartitionFilters.Add($partitionFilter)

$svcFilter2 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{HealthStateFilter=$errorFilter}

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{ApplicationNameFilter="fabric:/WordCount"}
$appFilter.ServiceFilters.Add($svcFilter1)
$appFilter.ServiceFilters.Add($svcFilter2)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)

Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters

HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks :
                               TotalCount            : 1

                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               ServiceHealthStateChunks :
                                   TotalCount            : 1

                                   ServiceName           : fabric:/WordCount/WordCountService
                                   HealthState           : Error
                                   PartitionHealthStateChunks :
                                       TotalCount            : 1

                                       PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
                                       HealthState           : Error
                                       ReplicaHealthStateChunks :
                                           TotalCount            : 5

                                           ReplicaOrInstanceId   : 131031502143040223
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844060
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844059
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844061
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844058
                                           HealthState           : Error
```

L’applet de commande suivante renvoie toutes les entités déployées sur un nœud.

```xml
$errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$dspFilter = New-Object System.Fabric.Health.DeployedServicePackageHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$daFilter =  New-Object System.Fabric.Health.DeployedApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter;NodeNameFilter="_Node_2"}
$daFilter.DeployedServicePackageFilters.Add($dspFilter)

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$appFilter.DeployedApplicationFilters.Add($daFilter)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)
Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks :
                               TotalCount            : 2

                               ApplicationName       : fabric:/System
                               HealthState           : Ok
                               DeployedApplicationHealthStateChunks :
                                   TotalCount            : 1

                                   NodeName              : _Node_2
                                   HealthState           : Ok
                                   DeployedServicePackageHealthStateChunks :
                                       TotalCount            : 1

                                       ServiceManifestName   : FAS
                                       HealthState           : Ok



                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               DeployedApplicationHealthStateChunks :
                                   TotalCount            : 1

                                   NodeName              : _Node_2
                                   HealthState           : Ok
                                   DeployedServicePackageHealthStateChunks :
                                       TotalCount            : 2

                                       ServiceManifestName   : WordCountServicePkg
                                       HealthState           : Ok

                                       ServiceManifestName   : WordCountWebServicePkg
                                       HealthState           : Ok
```

### <a name="rest"></a>RESTE
Vous pouvez obtenir cluster santé segment avec une [prise demande](https://msdn.microsoft.com/library/azure/mt656722.aspx) ou une [demande de publication](https://msdn.microsoft.com/library/azure/mt656721.aspx) qui inclut les stratégies d’intégrité et décrits les filtres avancés dans le corps.

## <a name="general-queries"></a>Requêtes générales
Requêtes générales renvoient une liste d’entités tissu de Service d’un type spécifique. Ils sont exposés via l’API (via les méthodes sur **FabricClient.QueryManager**), applets de commande PowerShell et reste. Ces requêtes agrègent sous-requêtes à partir de plusieurs composants. Un d'entre eux est la [santé stocker](service-fabric-health-introduction.md#health-store), qui remplit l’état d’intégrité agrégé pour chaque résultat de la requête.  

> [AZURE.NOTE] Requêtes générales renvoyer l’état d’intégrité agrégé de l’entité et ne contiennent aucune donnée d’intégrité enrichi. Si une entité n’est pas correcte, vous pouvez suivre avec les requêtes d’intégrité pour obtenir toutes ses informations d’intégrité, y compris les évaluations incorrectes, États d’intégrité enfant et événements.

Si les requêtes générales renvoient un état d’intégrité inconnue pour une entité, il est possible que le magasin d’intégrité n’a pas toutes les données relatives l’entité. Il est également possible qu’une sous-requête dans le magasin d’intégrité n’a pas réussie (par exemple, une erreur de communication s’est produite, ou le magasin d’intégrité été limité). Assurer un suivi avec une requête d’intégrité de l’entité. Si la sous-requête a rencontré des erreurs transitoires, tels que des problèmes réseau, cette requête suivie peut réussir. Il peut également vous donner plus de détails à partir du magasin d’intégrité sur pourquoi l’entité n’est pas exposée.

Les requêtes qui contiennent des **HealthState** pour entités sont :

- Liste des nœuds : renvoie les nœuds de liste dans le cluster (envoyé par radiomessagerie).
  - API : [FabricClient.QueryClient.GetNodeListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getnodelistasync.aspx)
  - PowerShell : Get-ServiceFabricNode
- Liste de l’application : renvoie la liste des applications dans le cluster (envoyé par radiomessagerie).
  - API : [FabricClient.QueryClient.GetApplicationListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getapplicationlistasync.aspx)
  - PowerShell : Get-ServiceFabricApplication
- Liste des services : renvoie la liste des services dans une application (envoyé par radiomessagerie).
  - API : [FabricClient.QueryClient.GetServiceListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getservicelistasync.aspx)
  - PowerShell : Get-ServiceFabricService
- Liste de partitions : renvoie la liste des partitions d’un service (envoyé par radiomessagerie).
  - API : [FabricClient.QueryClient.GetPartitionListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getpartitionlistasync.aspx)
  - PowerShell : Get-ServiceFabricPartition
- Liste réplica : renvoie la liste des réplicas dans une partition (envoyé par radiomessagerie).
  - API : [FabricClient.QueryClient.GetReplicaListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getreplicalistasync.aspx)
  - PowerShell : Get-ServiceFabricReplica
- Déployé liste de l’application : renvoie la liste des applications déployées sur un nœud.
  - API : [FabricClient.QueryClient.GetDeployedApplicationListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync.aspx)
  - PowerShell : Get-ServiceFabricDeployedApplication
- Déployé liste de package de service : renvoie la liste des packages de service dans une application déployée.
  - API : [FabricClient.QueryClient.GetDeployedServicePackageListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync.aspx)
  - PowerShell : Get-ServiceFabricDeployedApplication

> [AZURE.NOTE] Certaines des requêtes retournent des résultats paginés. Le retour de ces requêtes est une liste dérivée de [PagedList<T>](https://msdn.microsoft.com/library/azure/mt280056.aspx). Si les résultats ne s’intègrent pas un message, qu’une page est renvoyée et un ContinuationToken qui effectue le suivi où énumération s’est arrêté. Vous devez continuer à appeler la même requête et passez le jeton de continuation à partir de la requête précédente pour obtenir les résultats suivantes.

### <a name="examples"></a>Exemples

Le code suivant obtient les applications incorrectes dans le cluster :

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

L’applet de commande suivante obtient les détails de l’application pour tissu : / application WordCount. Notez que l’état d’intégrité est au message d’avertissement.

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "WordCountWebService_InstanceCount" = "1";
                         "_WFDebugParams_" = "[{"ServiceManifestName":"WordCountWebServicePkg","CodePackageName":"Code","EntryPointType":"Main","Debug
                         ExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {74f7e5d5-71a9-47e2-a8cd-1878ec4734f1} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"},{"ServiceManifestName":"WordCountServicePkg","CodeP
                         ackageName":"Code","EntryPointType":"Main","DebugExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {2ab462e6-e0d1-4fda-a844-972f561fe751} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"}]" }
```

L’applet de commande suivante obtient les services avec un état d’intégrité d’avertissement :

```powershell
PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Warning"}


ServiceName            : fabric:/WordCount/WordCountService
ServiceKind            : Stateful
ServiceTypeName        : WordCountServiceType
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Warning
```

## <a name="cluster-and-application-upgrades"></a>Mises à jour cluster et d’application
Au cours d’une mise à niveau contrôlée du cluster et des applications, Service TISSU vérifie santé pour vous assurer que tout le contenu reste correct. Si une entité ne fonctionne pas correctement comme évaluée à l’aide de stratégies d’intégrité configurée, la mise à niveau s’applique des stratégies de mise à niveau spécifiques pour déterminer la prochaine action. La mise à niveau peut être suspendu pour autoriser l’interaction de l’utilisateur (par exemple, la résolution des conditions d’erreur ou en modifiant les stratégies), ou elle peut automatiquement restaurer la bonne version précédente.

Au cours d’une mise à niveau *cluster* , vous pouvez obtenir l’état de mise à niveau de cluster. L’état de mise à niveau inclut les évaluations incorrectes, point sur ce qui ne fonctionne pas correctement dans le cluster. Si la mise à niveau en raison de problèmes d’intégrité est annulée, l’état de mise à niveau conserve les dernières raisons mauvais état. Ces informations peuvent aider les administrateurs à examiner la cause après que la mise à niveau annulées ou arrêté.

De même, une mise à niveau de *l’application* , les évaluations mauvais état sont contenues dans l’état de mise à niveau d’application.

L’exemple suivant montre l’état de mise à niveau d’application pour un tissu modifié : / application WordCount. Un pilote a signalé une erreur sur l’un de ses réplicas. La mise à niveau est propagée, car les contrôles ne sont pas respectées.

```powershell
PS C:\> Get-ServiceFabricApplicationUpgrade fabric:/WordCount

ApplicationName               : fabric:/WordCount
ApplicationTypeName           : WordCount
TargetApplicationTypeVersion  : 1.0.0.0
ApplicationParameters         : {}
StartTimestampUtc             : 4/21/2015 5:23:26 PM
FailureTimestampUtc           : 4/21/2015 5:23:37 PM
FailureReason                 : HealthCheck
UpgradeState                  : RollingBackInProgress
UpgradeDuration               : 00:00:23
CurrentUpgradeDomainDuration  : 00:00:00
CurrentUpgradeDomainProgress  : UD1

                                NodeName            : _Node_1
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_2
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                      Unhealthy partition: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b', AggregatedHealthState='Error'.

                                          Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                          Unhealthy replica: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b',
                                  ReplicaOrInstanceId='131031502346844058', AggregatedHealthState='Error'.

                                              Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

En savoir plus sur l' [application de Service TISSU mise à niveau](service-fabric-application-upgrade.md).

## <a name="use-health-evaluations-to-troubleshoot"></a>Évaluations d’intégrité permet de résoudre les problèmes
Chaque fois qu’il existe un problème avec le cluster ou une application, consultez l’état de calcul ou d’une application pour identifier quel est le problème. Les évaluations défectueuses fournissent des détails sur ce qui a déclenché l’état d’intégrité actuel. Si vous le souhaitez, vous pouvez Explorer entités enfant défectueux à identifier la cause.

> [AZURE.NOTE] Les évaluations mauvais état affichent que la première raison pour laquelle l’entité est évaluée pour l’état d’intégrité actuel. Il peut y avoir plusieurs autres événements qui déclenchent cet état, mais ils ne sont pas répercutées dans les évaluations. Pour obtenir plus d’informations, extraire vers le bas vers les entités santé à identifier tous les rapports incorrectes dans le cluster.

## <a name="next-steps"></a>Étapes suivantes
[Utiliser des rapports d’intégrité système pour résoudre les problèmes](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Ajouter des rapports d’intégrité TISSU Service personnalisés](service-fabric-report-health.md)

[Comment créer des rapports et vérifiez l’état du service](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Surveiller et diagnostiquer les services localement](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Mise à niveau de service TISSU application](service-fabric-application-upgrade.md)
