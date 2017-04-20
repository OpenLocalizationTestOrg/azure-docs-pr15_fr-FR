<properties
   pageTitle="L’appel de perte de données sur les Services de structure de Service | Microsoft Azure"
   description="Décrit comment utiliser la perte de données api"
   services="service-fabric"
   documentationCenter=".net"
   authors="LMWF"
   manager="rsinha"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/19/2016"
   ms.author="lemai"/>
   
# <a name="how-to-invoke-data-loss-on-services"></a>L’appel de perte de données sur les Services

>[AZURE.WARNING] Ce document décrivent comment entraîner une perte de données dans vos services et doit être utilisé avec soin.

## <a name="introduction"></a>Introduction
Vous pouvez appeler la perte de données sur une partition de votre Service de tissu en appelant StartPartitionDataLossAsync().  Cette api utilise l’injection d’erreurs et le Service d’analyse pour effectuer le travail pour provoquer des conditions de perte de données.

## <a name="using-the-fault-injection-and-analysis-service"></a>À l’aide de l’injection d’erreurs et le Service d’analyse

Le Service d’analyse et d’injection d’erreurs prend actuellement en charge les API suivantes dans le tableau ci-dessous.  Le côté droit du graphique affiche l’applet de commande PowerShell correspondante.  Reportez-vous à la documentation msdn sur chaque API pour plus d’informations sur chacun d’eux.

|           API C#                    |         Applet de commande PowerShell                      |
|-------------------------------------|-----------------------------------------------:|
|[StartPartitionDataLossAsync] [dl]   |[Démarrage ServiceFabricPartitionDataLoss] [psdl]   |
|[StartPartitionQuorumLossAsync] [ql] |[Démarrage ServiceFabricPartitionQuorumLoss] [psql] |
|[StartPartitionRestartAsync] [rp]    |[Démarrage ServiceFabricPartitionRestart] [psrp]    |

## <a name="conceptual-overview-of-running-a-command"></a>Vue d’ensemble conceptuelle de l’exécution d’une commande

Le Service d’analyse et d’injection d’erreurs utilise un modèle asynchrone où vous lancez la commande avec une API, appelées l’API « Début » dans ce document, puis vérifie l’avancement de cette commande à l’aide de l’API « GetProgress » jusqu'à ce qu’il atteigne un état terminal, ou jusqu'à ce que vous l’annuler.
Pour commencer une commande, appelez l’API « Début » à l’API correspondante.  Cette API quand renvoie la probabilité d’Injection et le Service d’analyse a accepté la demande.  Toutefois, il n’indique pas une commande a combien exécuté, ou même si elle a encore commencé.  Afin de vérifier la progression d’une commande, appelez API qui correspond à l’API « Début » précédemment appelé « GetProgress ».  API « GetProgress » renvoie un objet qui indique l’état actuel de la commande à l’intérieur de sa propriété State.  Exécution d’une commande indéfiniment jusqu'à ce que :

1.  Il se termine avec succès.  Si vous appelez « GetProgress » dessus dans ce cas, état de l’objet de la progression sera terminée.
2.  Il rencontre une erreur critique.  Si vous appelez « GetProgress » dessus dans ce cas, état de l’objet de l’avancement générera une erreur
3.  Vous annulez via la [CancelTestCommandAsync]  [ cancel] API ou [Arrêter ServiceFabricTestCommand]  [ cancelps] applet de commande PowerShell.  Si vous appelez « GetProgress » dessus dans ce cas, état de l’objet de la progression sera annulé ou ForceCancelled, en fonction d’un argument à cette API.  Consultez la documentation de [CancelTestCommandAsync]  [ cancel] pour plus d’informations.


## <a name="details-of-running-a-command"></a>Détails de l’exécution d’une commande

Avant de lancer une commande, appelez l’API commencer avec les arguments attendus.  Toutes les API démarrer ont un argument Guid nommé operationId.  Vous devez effectuer le suivi de l’argument operationId, car il est utilisé pour suivre la progression de cette commande.  Cela doit être passé à l’API « GetProgress » pour suivre la progression de la commande.  L’operationId doit être unique.

Après l’appel à l’API démarrer correctement, l’API GetProgress doit être appelée dans une boucle jusqu'à ce que la progression retourné propriété l’objet état est terminée.  Tous les [de FabricTransientException]  [ fte] et OperationCanceledException doit être retentée.
Lorsque la commande a atteint un état terminal (terminé, Faulted ou annulée), propriété de résultat de l’objet de l’avancement retourné aura des informations supplémentaires.  Si l’état est terminée, Result.SelectedPartition.PartitionId contient l’id de partition qui a été activé.  Result.Exception est null.  Si l’état est défectueux, Result.Exception aura la raison pour laquelle la probabilité d’Injection et Service d’analyse de la commande une erreur.  Result.SelectedPartition.PartitionId a l’id de partition qui a été activé.  Dans certains cas, la commande ne peut pas avez effectué suffisamment pour choisir une partition.  Dans ce cas, l’IDPartition est égal à 0.  Si l’état est annulée, Result.Exception est null.  Comme le cas Faulted, Result.SelectedPartition.PartitionId auront l’id de partition qui a été choisie, mais si la commande n’a pas été suffisamment écoulée pour le faire, il est égal à 0.  Reportez-vous à l’exemple ci-dessous.

L’exemple de code ci-dessous montre comment démarrer, puis vérifier la progression d’une commande à l’origine de perte de données sur une partition spécifique.

```csharp
    static async Task PerformDataLossSample()
    {
        // Create a unique operation id for the command below
        Guid operationId = Guid.NewGuid();

        // Note: Use the appropriate overload for your configuration
        FabricClient fabricClient = new FabricClient();

        // The name of the target service
        Uri targetServiceName = new Uri("fabric:/MyService");

        // The id of the target partition inside the target service
        Guid targetPartitionId = new Guid("00000000-0000-0000-0000-000002233445");

        PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(targetServiceName, targetPartitionId);

        // Start the command.  Retry OperationCanceledException and all FabricTransientException's.  Note when StartPartitionDataLossAsync completes
        // successfully it only means the Fault Injection and Analysis Service has saved the intent to perform this work.  It does not say anything about the progress
        // of the command.
        while (true)
        {
            try
            {
                await fabricClient.TestManager.StartPartitionDataLossAsync(operationId, partitionSelector, DataLossMode.FullDataLoss).ConfigureAwait(false);
                break;
            }
            catch (OperationCanceledException)
            {
            }
            catch (FabricTransientException)
            {
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }

        PartitionDataLossProgress progress = null;

        // Poll the progress using GetPartitionDataLossProgressAsync until it is either Completed or Faulted.  In this example, we're assuming
        // the command won't be cancelled.        

        while (true)
        {
            try
            {
                progress = await fabricClient.TestManager.GetPartitionDataLossProgressAsync(operationId).ConfigureAwait(false);
            }
            catch (OperationCanceledException)
            {
                continue;
            }
            catch (FabricTransientException)
            {
                continue;
            }

            if (progress.State == TestCommandProgressState.Completed)
            {
                Console.WriteLine("Command '{0}' completed successfully", operationId);

                // In a terminal state .Result.SelectedPartition.PartitionId will have the chosen partition
                Console.WriteLine("  Printing selected partition='{0}'", progress.Result.SelectedPartition.PartitionId);
                break;
            }
            else if (progress.State == TestCommandProgressState.Faulted)
            {
                // If State is Faulted, the progress object's Result property's Exception property will have the reason why.
                Console.WriteLine("Command '{0}' failed with '{1}'", operationId, progress.Result.Exception);
                break;
            }
            else
            {
                Console.WriteLine("Command '{0}' is currently Running", operationId);
            }

            await Task.Delay(TimeSpan.FromSeconds(5.0d)).ConfigureAwait(false);
        }
    }
```

L’exemple ci-dessous montre comment utiliser le PartitionSelector pour choisir une partition aléatoire d’un service spécifié :

```csharp
    static async Task PerformDataLossUseSelectorSample()
    {
        // Create a unique operation id for the command below
        Guid operationId = Guid.NewGuid();

        // Note: Use the appropriate overload for your configuration
        FabricClient fabricClient = new FabricClient();

        // The name of the target service
        Uri targetServiceName = new Uri("fabric:/SampleService ");

        // Use a PartitionSelector that will have the Fault Injection and Analysis Service choose a random partition of “targetServiceName”
        PartitionSelector partitionSelector = PartitionSelector.RandomOf(targetServiceName);

        // Start the command.  Retry OperationCanceledException and all FabricTransientException's.  Note when StartPartitionDataLossAsync completes
        // successfully it only means the Fault Injection and Analysis Service has saved the intent to perform this work.  It does not say anything about the progress
        // of the command.
        while (true)
        {
            try
            {
                await fabricClient.TestManager.StartPartitionDataLossAsync(operationId, partitionSelector, DataLossMode.FullDataLoss).ConfigureAwait(false);
                break;
            }
            catch (OperationCanceledException)
            {
            }
            catch (FabricTransientException)
            {
            }
            catch (Exception e)
            {
                Console.WriteLine("Unexpected exception '{0}'", e);
                throw;
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }

        PartitionDataLossProgress progress = null;

        // Poll the progress using GetPartitionDataLossProgressAsync until it is either Completed or Faulted.  In this example, we're assuming
        // the command won't be cancelled.

        while (true)
        {
            try
            {
                progress = await fabricClient.TestManager.GetPartitionDataLossProgressAsync(operationId).ConfigureAwait(false);
            }
            catch (OperationCanceledException)
            {
                continue;
            }
            catch (FabricTransientException)
            {
                continue;
            }

            if (progress.State == TestCommandProgressState.Completed)
            {
                Console.WriteLine("Command '{0}' completed successfully", operationId);

                Console.WriteLine("Printing progress.Result:");
                Console.WriteLine("  Printing selected partition='{0}'", progress.Result.SelectedPartition.PartitionId);

                break;
            }
            else if (progress.State == TestCommandProgressState.Faulted)
            {
                // If State is Faulted, the progress object's Result property's Exception property will have the reason why.
                Console.WriteLine("Command '{0}' failed with '{1}', SelectedPartition {2}", operationId, progress.Result.Exception, progress.Result.SelectedPartition);
                break;
            }
            else
            {
                Console.WriteLine("Command '{0}' is currently Running", operationId);
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }
    }
```

## <a name="history-and-truncation"></a>Historique et troncature

Une fois une commande a atteint un état terminal, ses métadonnées restera dans l’injection d’erreurs et le Service d’analyse à une certaine heure, avant d’être supprimée pour libérer de l’espace.  Si « GetProgress » est appelée à l’aide de l’operationId d’une commande une fois qu’il a été supprimée, elle retourne une FabricException avec un code d’erreur de KeyNotFound.

[dl]: https://msdn.microsoft.com/library/azure/mt693569.aspx
[ql]: https://msdn.microsoft.com/library/azure/mt693558.aspx
[rp]: https://msdn.microsoft.com/library/azure/mt645056.aspx
[psdl]: https://msdn.microsoft.com/library/mt697573.aspx
[psql]: https://msdn.microsoft.com/library/mt697557.aspx
[psrp]: https://msdn.microsoft.com/library/mt697560.aspx
[cancel]: https://msdn.microsoft.com/library/azure/mt668910.aspx
[cancelps]: https://msdn.microsoft.com/library/mt697566.aspx
[fte]: https://msdn.microsoft.com/library/azure/system.fabric.fabrictransientexception.aspx
