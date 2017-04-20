<properties
   pageTitle="Résoudre les problèmes avec les rapports d’intégrité du système | Microsoft Azure"
   description="Décrit les rapports d’intégrité envoyées par les composants de tissu de Service Azure et leur utilisation cluster dépannage ou problèmes des applications."
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

# <a name="use-system-health-reports-to-troubleshoot"></a>Utiliser des rapports d’intégrité système pour résoudre les problèmes

Azure TISSU Service composants rapport prêts à l’emploi sur toutes les entités dans le cluster. La [santé stocker](service-fabric-health-introduction.md#health-store) crée et supprime les entités basées sur les rapports du système. Il organise également les dans une hiérarchie qui capture interactions entité.

> [AZURE.NOTE] Pour comprendre les concepts relatifs à l’état, en savoir plus en [modèle de fonctionnement tissu de Service](service-fabric-health-introduction.md).

Rapports d’intégrité du système offrent une visibilité cluster et les fonctionnalités de l’application et les problèmes indicateur par le biais d’intégrité. Pour les applications et services, rapports d’intégrité du système Vérifiez qu’entités sont implémentées et sont comportent correctement à partir de la perspective tissu de Service. Les rapports ne fournissent pas n’importe quel contrôle du fonctionnement de la logique métier du service ou de la détection des processus bloqués. Services d’utilisateurs peuvent enrichir les données d’intégrité des informations spécifiques à leur logique.

> [AZURE.NOTE] Rapports d’intégrité des agents de surveillance sont visibles uniquement *une fois que* les composants du système créent une entité. Lorsqu’une entité est supprimée, le magasin d’intégrité supprime automatiquement tous les rapports d’intégrité associés. Vaut lors de la création d’une nouvelle instance de l’entité (par exemple, une nouvelle instance de réplica de service est créée). Tous les rapports associés à l’ancienne instance sont supprimés et nettoyés à partir du magasin.

Le composant système signale identifiées par la source, qui commence par la «**système.**» préfixe. Agents de surveillance ne peut pas utiliser le même préfixe pour leurs sources, comme étant rejetés des rapports à l’aide des paramètres non valides.
Nous allons étudier certains rapports système à comprendre ce qui les déclenche et comment résoudre les éventuels problèmes qu’elles représentent.

> [AZURE.NOTE] Service TISSU continue à ajouter des rapports sur les conditions d’intérêt améliorent la visibilité quoi de neuf dans le cluster et l’application.

## <a name="cluster-system-health-reports"></a>Rapports d’intégrité du système cluster
L’entité de santé cluster est créée automatiquement dans le magasin d’intégrité. Si tout fonctionne correctement, il n’a pas un rapport du système.

### <a name="neighborhood-loss"></a>Perte de club
**System.Federation** signale une erreur quand il détecte une perte de club. Le rapport est à partir des nœuds individuels, et l’ID du nœud est inclus dans le nom de la propriété. Si un cercle est perdue dans l’ensemble anneau tissu de Service, vous pouvez généralement vous attendre deux événements (les deux côtés du rapport écart). Si plusieurs cercles sont perdus, il existe des événements supplémentaires.

Le rapport spécifie le délai d’expiration location général en tant que la durée de vie. Le rapport est renvoyé chaque moitié de la durée de vie pour tant que la condition reste active. L’événement est automatiquement supprimé lorsqu’il arrive à expiration. Supprimer lorsque le comportement qui a expiré garantit que le rapport est nettoyé à partir du magasin d’intégrité correctement, même si le nœud de création de rapports est vers le bas.

- **ID source**: System.Federation
- **Propriété**: commence par **Club** et inclut des informations sur les nœuds
- **Étapes suivantes**: examinez pourquoi le cercle est perdu (par exemple, vérifiez la communication entre les nœuds de cluster).

## <a name="node-system-health-reports"></a>Rapports d’intégrité du système nœud
**System.FM**, qui représente le service Gestionnaire de basculement, est l’autorité qui gère les informations sur les nœuds de cluster. Chaque nœud doit avoir un rapport à partir de System.FM avec son état. Les entités nœud sont supprimées lorsque l’état du nœud est supprimé (voir [RemoveNodeStateAsync](https://msdn.microsoft.com/library/azure/mt161348.aspx)).

### <a name="node-updown"></a>Nœud haut/bas
System.FM signale comme OK lorsque le nœud joint à appeler (il est en cours d’exécution). Il signale une erreur lorsque le nœud quitte l’anneau (il s’agit vers le bas, des options de mise à niveau ou simplement, car il a échoué). La hiérarchie d’intégrité générée par le magasin d’intégrité effectue une action sur entités déployées dans corrélation avec des rapports de nœud System.FM. Il considère le nœud un parent virtuel de toutes les entités déployés. Les entités déployées sur ce nœud sont exposées via des requêtes si le nœud est signalé comme étant en service par System.FM, avec la même instance que l’instance associée aux entités. Lorsque System.FM signale que le nœud est arrêté ou redémarré (une nouvelle instance), le magasin d’intégrité efface automatiquement les entités déployées qui peuvent exister uniquement sur le nœud vers le bas ou sur l’occurrence précédente du nœud.

- **ID source**: System.FM
- **Propriété**: état
- **Étapes suivantes**: si le nœud concerne une mise à niveau vers le bas, il doit s’afficher en une fois qu’il a été mis à niveau. Dans ce cas, l’état d’intégrité doit être changé sur OK. Si le nœud ne revenez ou elle échoue, le problème doit plus enquête.

L’exemple suivant montre l’événement System.FM avec un état d’intégrité de OK pour nœud vers le haut :

```powershell

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 2
                        SentAt                : 4/24/2015 5:27:33 PM
                        ReceivedAt            : 4/24/2015 5:28:50 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 5:28:50 PM

```


### <a name="certificate-expiration"></a>Expiration du certificat
**System.FabricNode** signale un avertissement s’affiche lorsque les certificats utilisés par le nœud sont près d’expiration. Il existe trois certificats par nœud : **Certificate_cluster**, **Certificate_server**et **Certificate_default_client**. Lorsque la date d’expiration est au moins deux semaines, l’état d’intégrité de rapport est OK. Lorsque la date d’expiration est dans les deux semaines, le type de rapport est un avertissement s’affiche. Durée de vie de ces événements est infinie, et ils sont supprimés lorsqu’un nœud quitte le cluster.

- **ID source**: System.FabricNode
- **Propriété**: commence par **certificat** et contient plus d’informations sur le type de certificat
- **Étapes suivantes**: mettre à jour les certificats s’ils sont près d’expiration.

### <a name="load-capacity-violation"></a>Charger violation de capacité
L’équilibrage de charge TISSU Service signale un avertissement s’il détecte une violation de capacité nœud.

 - **ID source**: System.PLB
 - **Propriété**: commence par **capacité**
 - **Étapes suivantes**: vérifier fourni métriques et afficher la capacité actuelle sur le nœud.

## <a name="application-system-health-reports"></a>Rapports d’intégrité du système des applications
**System.CM**, qui représente le service Cluster Manager, est l’autorité qui gère les informations relatives à une application.

### <a name="state"></a>État
System.CM signale comme OK lorsque l’application a été créée ou mis à jour. Il vous informe le magasin d’intégrité lorsque l’application a été supprimée, afin qu’il peut être supprimé de la banque.

- **ID source**: System.CM
- **Propriété**: état
- **Étapes suivantes**: si l’application a été créée, il doit inclure l’état d’intégrité Cluster Manager. Dans le cas contraire, vérifiez l’état de l’application à l’aide d’une requête (par exemple, l’applet de commande PowerShell * *Get-ServiceFabricApplication ApplicationName *applicationName***).

L’exemple suivant illustre l’événement état sur le **TISSU : / WordCount** application :

```powershell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesFilter None -DeployedApplicationsFilter None

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 82
                                  SentAt                : 4/24/2015 6:12:51 PM
                                  ReceivedAt            : 4/24/2015 6:12:51 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/24/2015 6:12:51 PM
```

## <a name="service-system-health-reports"></a>Rapports d’intégrité du service système
**System.FM**, qui représente le service Gestionnaire de basculement, est l’autorité qui gère les informations sur les services.

### <a name="state"></a>État
System.FM signale comme OK lorsque le service a été créé. Il supprime l’entité dans le magasin d’intégrité lorsque le service a été supprimé.

- **ID source**: System.FM
- **Propriété**: état

L’exemple suivant illustre l’événement état sur le service **TISSU : / WordCount/WordCountService**:

```powershell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountService

ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Ok
PartitionHealthStates :
                        PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:01 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:01 PM
```

### <a name="unplaced-replicas-violation"></a>Violation de réplicas non placées
**System.PLB** signale un avertissement s’il ne peut pas trouver un emplacement pour un ou plusieurs réplicas de service. Le rapport est supprimé lorsqu’il arrive à expiration.

- **ID source**: System.FM
- **Propriété**: état
- **Étapes suivantes**: vérifier les contraintes de service et l’état actuel de la position.

L’exemple suivant montre une violation d’un service configuré avec 7 réplicas cible dans un cluster à 5 nœuds :

```xml
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountService


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
                        SequenceNumber        : 131032232425505477
                        SentAt                : 3/23/2016 4:14:02 PM
                        ReceivedAt            : 3/23/2016 4:14:03 PM
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
                        Transitions           : Error->Warning = 3/22/2016 7:57:48 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="partition-system-health-reports"></a>Rapports d’intégrité du système partition
**System.FM**, qui représente le service Gestionnaire de basculement, est l’autorité qui gère les informations sur les partitions de service.

### <a name="state"></a>État
System.FM signale comme OK lorsque la partition a été créée et est correcte. Il supprime l’entité dans le magasin d’intégrité lorsque la partition est supprimée.

Si la partition est inférieure au nombre minimal réplica, il signale une erreur. Si la partition n’est pas sous le nombre minimal réplica, mais il est en dessous du réplica nombre de la cible, il affiche un avertissement s’affiche. Si la partition est perte quorum, System.FM signale une erreur.

Autres événements importants incluent un message d’avertissement lors de reconfigurer prend plus de temps que prévu et lorsque la génération prend plus longtemps que prévu. Attendus heures pour la création et la reconfiguration sont configurables basant sur des scénarios de service. Par exemple, si un service a un to d’état, par exemple de base de données SQL, la génération prend plue d’un service avec un peu d’état.

- **ID source**: System.FM
- **Propriété**: état
- **Étapes suivantes**: si l’état d’intégrité n’est pas OK, il est possible que certains réplicas n’ont pas été créés, ouvert ou la promotion principal ou secondaire correctement. Dans de nombreux cas, la cause est un bogue de service dans l’implémentation ouvrir ou modifier le rôle.

L’exemple suivant montre une partition correcte :

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/StatelessPiApplication/StatelessPiService | Get-ServiceFabricPartitionHealth
PartitionId           : 29da484c-2c08-40c5-b5d9-03774af9a9bf
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 38
                        SentAt                : 4/24/2015 6:33:10 PM
                        ReceivedAt            : 4/24/2015 6:33:31 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:33:31 PM
```

L’exemple suivant montre l’état d’une partition qui est inférieure à compter du réplica cible. L’étape suivante consiste à obtenir la description de la partition, qui montre comment elle est configurée : **MinReplicaSetSize** est deux et **TargetReplicaSetSize** est sept. Puis obtenir le nombre de nœuds dans le cluster : cinq. Donc dans ce cas, deux réplicas ne peuvent pas être placés.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None

PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 37
                        SentAt                : 4/24/2015 6:13:12 PM
                        ReceivedAt            : 4/24/2015 6:13:31 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 4/24/2015 6:13:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService

PartitionId            : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
PartitionKind          : Int64Range
PartitionLowKey        : 1
PartitionHighKey       : 26
PartitionStatus        : Ready
LastQuorumLossDuration : 00:00:00
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 7
HealthState            : Warning
DataLossNumber         : 130743727710830900
ConfigurationNumber    : 8589934592


PS C:\> @(Get-ServiceFabricNode).Count
5
```

### <a name="replica-constraint-violation"></a>Violation de contrainte réplica
**System.PLB** signale un avertissement s’il détecte une violation de contrainte réplica et que vous ne pouvez pas placer des copies de la partition.

- **ID source**: System.PLB
- **Propriété**: commence par **ReplicaConstraintViolation**

## <a name="replica-system-health-reports"></a>Rapports d’intégrité du système réplica
**System.RA**, qui représente le composant agent reconfiguration, est l’autorité de l’état de copie.

### <a name="state"></a>État
**System.RA** signale comme OK lorsque le réplica a été créé.

- **ID source**: System.RA
- **Propriété**: état

L’exemple suivant montre un réplica exact :

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth
PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
ReplicaId             : 130743727717237310
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743727718018580
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:02 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:02 PM
```

### <a name="replica-open-status"></a>Statut en cours réplica
La description de ce rapport d’intégrité contient l’heure de début (temps universel) lors de l’appel API l’appel.

**System.RA** signale un avertissement si le réplica ouvert dure plu de la période configurée (par défaut : 30 minutes). Si l’API impact sur la disponibilité de service, le rapport est émis beaucoup plus rapidement (un intervalle configurable, avec une valeur par défaut de 30 secondes). Le temps mesuré inclut la durée de l’ouverture replicator et le service étant ouvert. La propriété devient OK si l’ouverture est terminée.

- **ID source**: System.RA
- **Propriété**: **ReplicaOpenStatus**
- **Étapes suivantes**: si l’état d’intégrité n’est pas OK, examinez pourquoi le réplica ouvert prend plus longtemps que prévu.

### <a name="slow-service-api-call"></a>Appel de l’API de service lent
Rapport **System.RAP** et **System.Replicator** un message d’avertissement si un appel vers le code de service utilisateur prend plu le temps configuré. L’avertissement est désactivée lors de l’appel est terminé.

- **ID source**: System.RAP ou System.Replicator
- **Propriété**: le nom de l’API lente. La description fournit plus d’informations sur l’heure de que l’API a été en attente.
- **Étapes suivantes**: examinez pourquoi l’appel prend plus longtemps que prévu.

L’exemple suivant montre une partition dans perte quorum et les étapes d’enquête terminés pour identifier le pourquoi. Un réplica propose un état d’intégrité d’avertissement, de sorte que vous obtenez son état. Il indique que l’opération de service prend plus longtemps que prévu, un événement signalé par System.RAP. Une fois ces informations reçues, l’étape suivante consiste à examiner le code de service et examinez il. Dans ce cas, l’implémentation **RunAsync** du service dynamique lève une exception non gérée. Les réplicas sont recyclage, afin que vous ne voyez ne peut-être pas tous les réplicas dans l’état d’avertissement. Vous pouvez réessayer l’obtention de l’état d’intégrité et voir les différences dans le code de copie. Dans certains cas, les tentatives peuvent vous fournir indices.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful | Get-ServiceFabricPartitionHealth

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
AggregatedHealthState : Error
UnhealthyEvaluations  :
                        Error event: SourceId='System.FM', Property='State'.

ReplicaHealthStates   :
                        ReplicaId             : 130743748372546446
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746168084332
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746195428808
                        AggregatedHealthState : Warning

                        ReplicaId             : 130743746195428807
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Error
                        SequenceNumber        : 182
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:31 PM
                        TTL                   : Infinite
                        Description           : Partition is in quorum loss.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Warning->Error = 4/24/2015 6:51:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful

PartitionId            : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
PartitionKind          : Int64Range
PartitionLowKey        : -9223372036854775808
PartitionHighKey       : 9223372036854775807
PartitionStatus        : InQuorumLoss
LastQuorumLossDuration : 00:00:13
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 3
HealthState            : Error
DataLossNumber         : 130743746152927699
ConfigurationNumber    : 227633266688

PS C:\> Get-ServiceFabricReplica 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

ReplicaId           : 130743746195428808
ReplicaAddress      : PartitionId: 72a0fb3e-53ec-44f2-9983-2f272aca3e38, ReplicaId: 130743746195428808
ReplicaRole         : Primary
NodeName            : Node.3
ReplicaStatus       : Ready
LastInBuildDuration : 00:00:01
HealthState         : Warning

PS C:\> Get-ServiceFabricReplicaHealth 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
ReplicaId             : 130743746195428808
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.RAP', Property='ServiceOpenOperationDuration', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743756170185892
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:33 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 7:00:33 PM

                        SourceId              : System.RAP
                        Property              : ServiceOpenOperationDuration
                        HealthState           : Warning
                        SequenceNumber        : 130743756399407044
                        SentAt                : 4/24/2015 7:00:39 PM
                        ReceivedAt            : 4/24/2015 7:00:59 PM
                        TTL                   : Infinite
                        Description           : Start Time (UTC): 2015-04-24 19:00:17.019
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/24/2015 7:00:59 PM
```

Lorsque vous démarrez l’application câblage sous le débogueur, les fenêtres des événements de diagnostic affichent l’exception levée à partir de RunAsync :

![Visual Studio 2015 des événements de diagnostic : Échec de RunAsync TISSU : / HelloWorldStatefulApplication.][1]

Visual Studio 2015 des événements de diagnostic : Échec de RunAsync **TISSU : / HelloWorldStatefulApplication**.

[1]: ./media/service-fabric-understand-and-troubleshoot-with-system-health-reports/servicefabric-health-vs-runasync-exception.png


### <a name="replication-queue-full"></a>File d’attente de réplication pleine
**System.Replicator** signale un avertissement si la file d’attente de réplication est saturé. Sur le serveur principal, cela se produit généralement, car un ou plusieurs doubles secondaires sont lentes accuse opérations. Sur le site secondaire, cela se produit généralement lorsque le service est lent auquel appliquer les opérations. L’avertissement est désactivée lorsque la file d’attente n’est plus complète.

- **ID source**: System.Replicator
- **Propriété**: **PrimaryReplicationQueueStatus** ou **SecondaryReplicationQueueStatus**, selon le rôle de réplica

### <a name="slow-naming-operations"></a>Opérations d’appellation lentes

**System.NamingService** des rapports d’intégrité son réplica principal lorsqu’une opération d’appellation prend plue d’acceptable. Exemples d’opérations d’attribution de noms sont [CreateServiceAsync](https://msdn.microsoft.com/library/azure/mt124028.aspx) ou [DeleteServiceAsync](https://msdn.microsoft.com/library/azure/mt124029.aspx). Plusieurs méthodes sont accessibles sous FabricClient, par exemple sous [méthodes de gestion de service](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.aspx) ou [méthodes de gestion des propriétés](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.propertymanagementclient.aspx).

> [AZURE.NOTE] Le service d’appellation résout les noms de service vers un emplacement dans le cluster et permet aux utilisateurs de gérer les propriétés et les noms de service. Il s’agit d’un tissu de Service partition persistante service. Une des partitions représente le propriétaire de l’autorité, qui contient les métadonnées sur tous les noms de Service tissu et services. Les noms de Service tissu sont mappés à des partitions différentes, appelées partitions de nom du propriétaire, afin que le service est extensible. En savoir plus sur le [service de noms](service-fabric-architecture.md).

Lorsqu’une opération d’appellation prend plus longtemps que prévu, l’opération marquée d’un rapport d’avertissement sur le *réplica principal de la partition de service d’appellation qui gère l’opération*. Si l’opération réussit, l’avertissement est désactivée. Si l’opération se termine par une erreur, l’état d’intégrité inclut plus d’informations sur l’erreur.

- **ID source**: System.NamingService
- **Propriété**: commence par préfixe **Duration_** et identifie l’opération lente et le nom de structure de Service sur lequel l’opération est appliquée. Par exemple, si créer service auprès de tissu nom : / MyApp/MyService prend beaucoup de temps, la propriété est Duration_AOCreateService.fabric:/MyApp/MyService. AO pointe vers le rôle de la partition d’appellation pour ce nom et l’opération.
- **Étapes suivantes**: vérifier pourquoi l’opération d’appellation échoue. Chaque opération peut avoir différentes causes premières. Par exemple, supprimer service peut être bloqué sur un nœud, car l’hôte d’application conserve cesse-t-il de fonctionner sur un nœud en raison d’un bogue utilisateur dans le code de service.

L’exemple suivant montre une opération de service de créer. L’opération a eu plue de la durée configurée. AO tentatives et envoie le travail sur non. NON terminée la dernière opération avec délai d’expiration. Dans ce cas, le même réplica est prioritaire pour les AO et aucun rôle.

```powershell
PartitionId           : 00000000-0000-0000-0000-000000001000
ReplicaId             : 131064359253133577
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.NamingService', Property='Duration_AOCreateService.fabric:/MyApp/MyService', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131064359308715535
                        SentAt                : 4/29/2016 8:38:50 PM
                        ReceivedAt            : 4/29/2016 8:39:08 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 4/29/2016 8:39:08 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_AOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064359526778775
                        SentAt                : 4/29/2016 8:39:12 PM
                        ReceivedAt            : 4/29/2016 8:39:38 PM
                        TTL                   : 00:05:00
                        Description           : The AOCreateService started at 2016-04-29 20:39:08.677 is taking longer than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_NOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064360657607311
                        SentAt                : 4/29/2016 8:41:05 PM
                        ReceivedAt            : 4/29/2016 8:41:08 PM
                        TTL                   : 00:00:15
                        Description           : The NOCreateService started at 2016-04-29 20:39:08.689 completed with FABRIC_E_TIMEOUT in more than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="deployedapplication-system-health-reports"></a>Rapports d’intégrité du système DeployedApplication
**System.Hosting** est l’autorité sur entités déployées.

### <a name="activation"></a>Activation
System.Hosting signale comme OK lorsqu’une application a été correctement activée sur le nœud. Dans le cas contraire, il signale une erreur.

- **ID source**: System.Hosting
- **Propriété**: l’Activation, y compris la version de déploiement
- **Étapes suivantes**: si l’application ne fonctionne pas correctement, recherchez pourquoi l’activation a échoué.

L’exemple suivant illustre l’activation réussie :

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName Node.1 -ApplicationName fabric:/WordCount

ApplicationName                    : fabric:/WordCount
NodeName                           : Node.1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCountServicePkg
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 130743727751144415
                                     SentAt                : 4/24/2015 6:12:55 PM
                                     ReceivedAt            : 4/24/2015 6:13:03 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : ->Ok = 4/24/2015 6:13:03 PM
```

### <a name="download"></a>Télécharger
**System.Hosting** signale une erreur en cas d’échec du téléchargement du package application.

- **ID source**: System.Hosting
- **Propriété**: * *télécharger :*RolloutVersion***
- **Étapes suivantes**: examinez pourquoi le téléchargement a échoué sur le nœud.

## <a name="deployedservicepackage-system-health-reports"></a>Rapports d’intégrité du système DeployedServicePackage
**System.Hosting** est l’autorité sur entités déployées.

### <a name="service-package-activation"></a>Activation du service de package
System.Hosting signale comme OK si l’activation du lot service sur le nœud a réussi. Dans le cas contraire, il signale une erreur.

- **ID source**: System.Hosting
- **Propriété**: Activation
- **Étapes suivantes**: examinez pourquoi l’activation a échoué.

### <a name="code-package-activation"></a>Activation du package code
**System.Hosting** signale comme OK pour chaque package code si l’activation a réussi. Si l’activation échoue, il signale un avertissement s’affiche comme configuré. Si **CodePackage** ne parvient pas à activer ou se termine par une erreur supérieure à la configuration **CodePackageHealthErrorThreshold**, hébergement signale une erreur. Si un package de service contient plusieurs packages de code, un état de l’activation est généré pour chacun d’eux.

- **ID source**: System.Hosting
- **Propriété**: utilise le préfixe **CodePackageActivation** et contient le nom du package code et le point d’entrée en tant que * *CodePackageActivation :*CodePackageName*:*SetupEntryPoint/point d’entrée* ** (par exemple, **CodePackageActivation:Code:SetupEntryPoint**)

### <a name="service-type-registration"></a>Inscription du type de service
**System.Hosting** signale comme OK si le type de service a été correctement enregistré. Il signale une erreur si l’inscription n’a pas été effectuée dans le temps (telle que définie à l’aide de **ServiceTypeRegistrationTimeout**). Si le type de service est annulé à partir du nœud, c’est parce que le temps d’exécution a été fermé. Rapports d’hébergement un avertissement.

- **ID source**: System.Hosting
- **Propriété**: utilise le préfixe **ServiceTypeRegistration** et contient le nom de type de service (par exemple, **ServiceTypeRegistration:FileStoreServiceType**)

L’exemple suivant montre un package de service déployé exact :

```powershell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName Node.1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCountServicePkg
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 130743727751456915
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 130743727751613185
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCountServiceType
                        HealthState           : Ok
                        SequenceNumber        : 130743727753644473
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM
```

### <a name="download"></a>Télécharger
**System.Hosting** signale une erreur en cas d’échec du téléchargement du package du service.

- **ID source**: System.Hosting
- **Propriété**: * *télécharger :*RolloutVersion***
- **Étapes suivantes**: examinez pourquoi le téléchargement a échoué sur le nœud.

### <a name="upgrade-validation"></a>Mise à niveau de validation
**System.Hosting** signale une erreur si la validation pendant la mise à niveau échoue ou si la mise à niveau ne fonctionne pas sur le nœud.

- **ID source**: System.Hosting
- **Propriété**: utilise le préfixe **FabricUpgradeValidation** et contient la version mise à niveau
- **Description**: pointe vers l’erreur s’est produite

## <a name="next-steps"></a>Étapes suivantes
[Afficher les rapports d’intégrité tissu de Service](service-fabric-view-entities-aggregated-health.md)

[Comment créer des rapports et vérifiez l’état du service](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Surveiller et diagnostiquer les services localement](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Mise à niveau de service TISSU application](service-fabric-application-upgrade.md)
