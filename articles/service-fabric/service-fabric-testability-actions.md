<properties
   pageTitle="Action de test | Microsoft Azure"
   description="Cet article traite des actions de test figurant dans Microsoft Azure Service tissu."
   services="service-fabric"
   documentationCenter=".net"
   authors="motanv"
   manager="timlt"
   editor="toddabel"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/03/2016"
   ms.author="motanv;heeldin"/>

# <a name="testability-actions"></a>Actions de test
Afin de simuler une infrastructure de sources non fiable, Azure Service TISSU vous offre, le développeur, avec manières de simuler diverses défaillances réelles et transitions d’état. Ils sont exposés comme actions de test. Les actions sont API de niveau inférieur occasionnant une injection d’erreurs spécifique, une transition d’état ou validation. En combinant ces actions, vous pouvez écrire des scénarios de test complet pour vos services.

Service TISSU fournit que quelques scénarios courants test composée de ces actions. Nous vous recommandons vivement de que vous utilisez ces scénarios intégrés, qui sont choisis avec soin pour tester les transitions d’état courantes et cas d’échec. Toutefois, les actions peuvent être utilisées pour créer des scénarios de test personnalisée lorsque vous souhaitez ajouter la couverture de scénarios qui ne figurent pas par les scénarios intégrés encore ou qui sont personnalisées adaptées à votre application.

C# mises en œuvre des actions sont trouvent dans l’assembly System.Fabric.dll. Le module système TISSU PowerShell se trouve dans l’assembly Microsoft.ServiceFabric.Powershell.dll. Dans le cadre de l’installation d’exécution, le module ServiceFabric PowerShell est installé pour autoriser pour faciliter leur utilisation.

## <a name="graceful-vs-ungraceful-fault-actions"></a>Ordonné et actions anormal de défaillance
Actions de test sont classées en deux plages principales :

* Erreurs anormal : ces erreurs simuler des défaillances comme machine redémarre et processus se bloque. Dans ce cas d’échec, le contexte de l’exécution du processus s’arrête brusquement. Cela signifie qu'aucun nettoyage de l’état ne peut fonctionner avant le démarrage de l’application à nouveau.

* Erreurs ordonnés : ces erreurs de simuler les actions ordonnées comme réplica déplace et projections déclenchées par l’équilibrage de charge. Dans ce cas, le service reçoit une notification de la fermeture et pouvez nettoyer l’état avant de quitter.

Pour la validation une meilleure qualité, exécutez la charge de travail de service et de l’entreprise tout en INDUISANT diverses erreurs ordonnés et anormal. Erreurs anormal exercent scénarios où le processus de service se ferme brusquement au milieu d’un flux de travail. Ce test le chemin d’accès de récupération une fois que le réplica service est restauré en tissu de Service. Cela vous aidera à tester la cohérence des données et si l’état du service est mis à jour correctement après des échecs. L’autre ensemble de test échecs (l’ordonné) que le service correctement réagit à réplicas déplacés en tissu de Service. Cette commande teste la gestion de l’annulation de la méthode RunAsync. Le service doit vérifier pour le jeton d’annulation est définie, enregistrer correctement son état, et quitter la méthode RunAsync.

## <a name="testability-actions-list"></a>Liste d’actions de test

| Action | Description | API managée | Applet de commande PowerShell | Erreurs GRACEFUL/anormal |
|---------|-------------|-------------|-------------------|------------------------------|
|CleanTestState| Supprime l’état de test du cluster dans le cas d’un arrêt incorrect du pilote de test. | CleanTestStateAsync | Supprimer ServiceFabricTestState | Non applicable |
| InvokeDataLoss | Entraîne la perte de données dans une partition de service. | InvokeDataLossAsync | ServiceFabricPartitionDataLoss appeler | Progressive |
| InvokeQuorumLoss | Place une partition service dynamique donné en perte quorum. | InvokeQuorumLossAsync | ServiceFabricQuorumLoss appeler | Progressive |
| Déplacer le moniteur principal | Déplace le réplica spécifié principal d’un service dynamique vers le nœud de cluster spécifié. | MovePrimaryAsync | Déplacement ServiceFabricPrimaryReplica | Progressive |
| Déplacer le moniteur secondaire | Déplace le double secondaire en cours d’un service dynamique vers un nœud de cluster différent. | MoveSecondaryAsync | Déplacement ServiceFabricSecondaryReplica | Progressive |
| RemoveReplica | Imite un échec réplica en supprimer un réplica dans un cluster. Cela se ferme la réplique et passera au rôle « Aucun », supprimer tous les son état du cluster. | RemoveReplicaAsync | Supprimer ServiceFabricReplica | Progressive |
| RestartDeployedCodePackage | Imite un échec de processus de package de code en le redémarrage d’un package de code déployé sur un nœud dans un cluster. Cela interrompt le processus de package code, qui entraînera le redémarrage tous les réplicas service utilisateur hébergés dans ce processus. | RestartDeployedCodePackageAsync | Redémarrage ServiceFabricDeployedCodePackage | Anormal |
| RestartNode | Imite une défaillance de nœud cluster Service tissu en le redémarrage d’un nœud. | RestartNodeAsync | Redémarrage ServiceFabricNode | Anormal |
| RestartPartition | Imite un scénario centre de données indisponibilité ou cluster indisponibilité en redémarrant réplicas tout ou partie d’une partition. | RestartPartitionAsync | Redémarrage ServiceFabricPartition | Progressive |
| RestartReplica | Imite un échec réplica en le redémarrage d’un réplica persistant dans un cluster, fermeture du réplica et puis rouvrir. | RestartReplicaAsync | Redémarrage ServiceFabricReplica | Progressive |
| StartNode | Démarre un nœud dans un cluster est déjà arrêté. | StartNodeAsync | Démarrage ServiceFabricNode | Non applicable |
| StopNode | Imite une défaillance de nœud en arrêt d’un nœud dans un cluster. Le nœud restera vers le bas jusqu'à ce que StartNode est appelée. | StopNodeAsync | Stop ServiceFabricNode | Anormal |
| ValidateApplication | Valide la disponibilité et l’état de tous les services de structure de Service au sein d’une application, généralement après induisant une défaillance dans le système. | ValidateApplicationAsync | Test-ServiceFabricApplication | Non applicable |
| ValidateService | Valide la disponibilité et l’état d’un service Service tissu, généralement après induisant une défaillance dans le système. | ValidateServiceAsync | Test-ServiceFabricService | Non applicable |

## <a name="running-a-testability-action-using-powershell"></a>Exécuter une action de test à l’aide de PowerShell

Ce didacticiel montre comment exécuter une action de test à l’aide de PowerShell. Vous allez apprendre à utiliser pour exécuter une action de test un cluster (une case) local ou sur un cluster Azure. Microsoft.Fabric.Powershell.dll--le module Service TISSU PowerShell--est installé automatiquement lorsque vous installez Microsoft Service TISSU MSI. Le module est chargé automatiquement lorsque vous ouvrez une invite PowerShell.

Didacticiels segments :

- [Exécuter une action sur un cluster une case](#run-an-action-against-a-one-box-cluster)
- [Exécuter une action sur un cluster Azure](#run-an-action-against-an-azure-cluster)

### <a name="run-an-action-against-a-one-box-cluster"></a>Exécuter une action sur un cluster une case

Pour exécuter une action de test par rapport à un cluster local, tout d’abord vous connecter au cluster et ouvrez l’invite PowerShell en mode administrateur. Examinons l’action **Redémarrer ServiceFabricNode** .

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

Ici l’action **Redémarrer ServiceFabricNode** est exécutée sur un nœud nommé nœud « 1 ». Le mode de saisie semi-automatique indique qu’il ne doit pas vérifier si l’action redémarrer nœud réussi. Spécifiant le mode de saisie semi-automatique dans la mesure où « Vérifier » pour vérifier si l’action redémarrage réussi. Au lieu de spécifier directement le nœud par son nom, vous pouvez la spécifier via une clé de partition et le type de réplica, comme suit :

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**Redémarrage ServiceFabricNode** doit être utilisée pour redémarrer un nœud tissu de Service dans un cluster. Cette commande arrête le processus Fabric.exe, qui va redémarrer tout système services et utilisateurs service réplica hébergés sur ce nœud. À l’aide de cette API pour tester votre service permet de découvrir des bogues long les chemins d’accès de récupération de basculement. Il vous permet de simuler les échecs de nœuds dans le cluster.

La capture d’écran suivante montre la commande de test **Redémarrer ServiceFabricNode** en action.

![](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

Le résultat de la première **Get-ServiceFabricNode** (une applet de commande à partir du module Service TISSU PowerShell) montre que le cluster local comporte cinq nœuds : Node.1 à Node.5. Une fois que l’action de test (applet de commande) **Redémarrage ServiceFabricNode** est exécutée sur le nœud, nommé Node.4, nous voyons que disponibilité du nœud a été réinitialisée.

### <a name="run-an-action-against-an-azure-cluster"></a>Exécuter une action sur un cluster Azure

Exécuter une action de test (à l’aide de PowerShell) par rapport à un cluster Azure est semblable à l’action en cours d’exécution par rapport à un cluster local. La seule différence est qu’avant de pouvoir exécuter l’action, au lieu de connexion vers le cluster local, vous devez d’abord vous connecter au cluster Azure.

## <a name="running-a-testability-action-using-c35"></a>Exécuter une action de test à l’aide de C & #35 ;

Pour exécuter une action de test à l’aide de c#, vous devez d’abord connecter au cluster à l’aide de FabricClient. Obtenir les paramètres nécessaires pour exécuter l’action. Paramètres différents peuvent être utilisés pour exécuter l’action même.
Prenons l’action RestartServiceFabricNode, une pour l’exécuter consiste en utilisant les informations de nœud (nom de nœud et ID de l’instance nœud) dans le cluster.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

Explication des paramètres :

- **CompleteMode** indique que le mode ne doit pas vérifier si l’action redémarrage réussi. Spécifiant le mode de saisie semi-automatique dans la mesure où « Vérifier » pour vérifier si l’action redémarrage réussi.  
- **OperationTimeout** définit le nombre d’heures pour l’opération de se terminer avant qu’une exception TimeoutException est levée.
- **CancellationToken** permet à un appel en attente d’être annulée.

Au lieu de spécifier directement le nœud par son nom, vous pouvez la spécifier via une clé de partition et le type de réplica.

Pour plus d’informations, voir [PartitionSelector et ReplicaSelector](#partition_replica_selector).


```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Fabric.Testability;
using System.Fabric;
using System.Threading;
using System.Numerics;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");
        string nodeName = "N0040";
        BigInteger nodeInstanceId = 130743013389060139;

        Console.WriteLine("Starting RestartNode test");
        try
        {
            //Restart the node by using ReplicaSelector
            RestartNodeAsync(clusterConnection, serviceName).Wait();

            //Another way to restart node is by using nodeName and nodeInstanceId
            RestartNodeAsync(clusterConnection, nodeName, nodeInstanceId).Wait();
        }
        catch (AggregateException exAgg)
        {
            Console.WriteLine("RestartNode did not complete: ");
            foreach (Exception ex in exAgg.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("RestartNode completed.");
        return 0;
    }

    static async Task RestartNodeAsync(string clusterConnection, Uri serviceName)
    {
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);
        ReplicaSelector primaryofReplicaSelector = ReplicaSelector.PrimaryOf(randomPartitionSelector);

        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(primaryofReplicaSelector, CompletionMode.Verify);
    }

    static async Task RestartNodeAsync(string clusterConnection, string nodeName, BigInteger nodeInstanceId)
    {
        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(nodeName, nodeInstanceId, CompletionMode.Verify);
    }
}
```

## <a name="partitionselector-and-replicaselector"></a>PartitionSelector et ReplicaSelector

### <a name="partitionselector"></a>PartitionSelector
PartitionSelector est une assistance présentée dans possibilités de test et est utilisée pour sélectionner une partition spécifique sur lequel effectuer les actions de test. Il peut être utilisé pour sélectionner une partition spécifique si l’ID de partition est connu à l’avance. Ou, vous pouvez fournir la clé de partition et l’opération peut résoudre l’ID de partition en interne. Vous avez également la possibilité de sélection d’une partition aléatoire.

Pour utiliser cette application d’assistance, créez l’objet PartitionSelector et sélectionnez la partition à l’aide d’une des méthodes Select *. Passez dans l’objet PartitionSelector à l’API qui en a besoin. Si aucune option est sélectionnée, la valeur par défaut est une partition aléatoires.

```csharp
Uri serviceName = new Uri("fabric:/samples/InMemoryToDoListApp/InMemoryToDoListService");
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
string partitionName = "Partition1";
Int64 partitionKeyUniformInt64 = 1;

// Select a random partition
PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

// Select a partition based on ID
PartitionSelector partitionSelectorById = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);

// Select a partition based on name
PartitionSelector namedPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionName);

// Select a partition based on partition key
PartitionSelector uniformIntPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionKeyUniformInt64);
```

### <a name="replicaselector"></a>ReplicaSelector
ReplicaSelector est une assistance présentée dans possibilités de test et est utilisé pour aider à choisir un réplica sur lequel effectuer les actions de test. Il peut être utilisé pour sélectionner un réplica spécifique si l’ID de réplica est connu à l’avance. En outre, vous avez la possibilité de sélectionner un réplica principal ou secondaire aléatoires. ReplicaSelector dérivé de PartitionSelector, vous devez sélectionner la copie et la partition sur lequel vous souhaitez effectuer l’opération de test.

Pour utiliser cette application d’assistance, créez un objet ReplicaSelector et définissez comme vous le souhaitez sélectionner la copie et la partition. Vous pouvez ensuite le passer à l’API qui en a besoin. Si aucune option est sélectionnée, par défaut à un réplica aléatoire et partition aléatoires.

```csharp
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);
long replicaId = 130559876481875498;

// Select a random replica
ReplicaSelector randomReplicaSelector = ReplicaSelector.RandomOf(partitionSelector);

// Select the primary replica
ReplicaSelector primaryReplicaSelector = ReplicaSelector.PrimaryOf(partitionSelector);

// Select the replica by ID
ReplicaSelector replicaByIdSelector = ReplicaSelector.ReplicaIdOf(partitionSelector, replicaId);

// Select a random secondary replica
ReplicaSelector secondaryReplicaSelector = ReplicaSelector.RandomSecondaryOf(partitionSelector);
```

## <a name="next-steps"></a>Étapes suivantes

- [Scénarios de test](service-fabric-testability-scenarios.md)
- Comment faire pour tester votre service
   - [Simuler des défaillances au cours de charges de travail de service](service-fabric-testability-workload-tests.md)
   - [Échecs de communication de service à service](service-fabric-testability-scenarios-service-communication.md)
