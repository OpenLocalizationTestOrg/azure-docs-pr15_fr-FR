## <a name="receive-messages-with-eventprocessorhost"></a>Recevoir des messages avec EventProcessorHost

[EventProcessorHost][] est une classe .NET qui simplifie la réception d’événements à partir de l’événement Hubs par gestion des points de contrôle permanente et parallèle reçoit ces Hubs événement. À l’aide de [EventProcessorHost][], vous pouvez répartir des événements sur plusieurs destinataires, même lorsque hébergé dans des nœuds différents. Cet exemple montre comment utiliser [EventProcessorHost][] pour un seul destinataire. L’exemple de [mise à l’échelle des événements de traitement][] montre comment utiliser [EventProcessorHost][] avec plusieurs destinataires.

Pour utiliser [EventProcessorHost][], vous devez disposer d’un [compte de stockage Azure][]:

1. Connectez-vous au [portail Azure][], puis cliquez sur **Nouveau** dans la partie supérieure gauche de l’écran.

2. Cliquez sur **données + stockage**, puis cliquez sur **compte de stockage**.

    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-storage1.png)

3. Dans la carte de **créer un compte de stockage** , tapez un nom pour le compte de stockage. Sélectionnez un abonnement Azure, le groupe de ressources et l’emplacement dans lequel vous souhaitez créer la ressource. Puis cliquez sur **créer**.

    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-storage2.png)

4. Dans la liste des comptes de stockage, cliquez sur le compte de stockage nouvellement créé.

5. Dans la carte de compte de stockage, cliquez sur **les touches d’accès**. Copiez la valeur de **touche1** d’utiliser plus loin dans ce didacticiel.

    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-storage3.png)

4. Dans Visual Studio, créez un nouveau projet application de bureau Visual c# à l’aide du modèle de projet **Application Console** . Nommez le projet **combiné**.

    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-receiver-csharp1.png)

5. Dans l’Explorateur de solutions, avec le bouton droit de la solution, puis cliquez sur **Gérer les Packages NuGet pour la Solution**.

6. Cliquez sur l’onglet **Parcourir** , puis recherchez `Microsoft Azure Service Bus Event Hub - EventProcessorHost`. Vérifiez que le nom du projet (**récepteur**) est spécifié dans la zone **ou les versions** . Cliquez sur **l’installation**et acceptez les conditions d’utilisation.

    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-eph-csharp1.png)

    Visual Studio télécharge, installations et ajoute une référence à la [Azure Service événement concentrateur - package EventProcessorHost NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), avec toutes ses dépendances.

7. Droit sur le projet **récepteur** , cliquez sur **Ajouter**, puis cliquez sur **cours**. Nommez la nouvelle classe **SimpleEventProcessor**, puis cliquez sur **Ajouter** pour créer la classe.

    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-receiver-csharp2.png)

8. Ajoutez les instructions suivantes en haut du fichier SimpleEventProcessor.cs :

    ```
    using Microsoft.ServiceBus.Messaging;
    using System.Diagnostics;
    ```

    Ensuite, remplacer le code suivant pour le corps du cours :

    ```
    class SimpleEventProcessor : IEventProcessor
    {
        Stopwatch checkpointStopWatch;

        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }

        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }

        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());

                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                    context.Lease.PartitionId, data));
            }

            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
    }
    ```

    Ce cours sera appelée par **EventProcessorHost** pour traiter les événements reçus à partir du Hub de l’événement. Notez que la `SimpleEventProcessor` classe utilise un chronomètre pour appeler régulièrement la méthode de point de contrôle sur le contexte **EventProcessorHost** . Cela garantit que, si le destinataire est redémarré, vous perdrez pas plus de cinq minutes de traitement de la tâche.

9. Dans la classe **Program** , ajoutez le code suivant `using` instruction en haut du fichier :

    ```
    using Microsoft.ServiceBus.Messaging;
    ```

    Ensuite, remplacez le `Main` méthode dans le `Program` cours avec le code suivant, en remplaçant le nom de l’événement concentrateur et la chaîne de connexion au niveau de l’espace de noms que vous avez enregistré précédemment, et le compte de stockage et la clé que vous avez copiée dans les sections précédentes. 

    ```
    static void Main(string[] args)
    {
      string eventHubConnectionString = "{Event Hub connection string}";
      string eventHubName = "{Event Hub name}";
      string storageAccountName = "{storage account name}";
      string storageAccountKey = "{storage account key}";
      string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);

      string eventProcessorHostName = Guid.NewGuid().ToString();
      EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
      Console.WriteLine("Registering EventProcessor...");
      var options = new EventProcessorOptions();
      options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
      eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();

      Console.WriteLine("Receiving. Press enter key to stop worker.");
      Console.ReadLine();
      eventProcessorHost.UnregisterEventProcessorAsync().Wait();
    }
    ```

> [AZURE.NOTE] Ce didacticiel utilise une seule instance de [EventProcessorHost][]. Pour augmenter le débit, il est recommandé d’exécuter plusieurs instances [EventProcessorHost][], comme le montre l’exemple de [mise à l’échelle des traitement de l’événement][] . Dans ce cas, les instances différentes automatiquement coordonnent avec d’autres équilibrer les événements reçus. Si vous voulez plusieurs récepteurs chaque processus *tous* les événements, vous devez utiliser le concept de **ConsumerGroup** . Lorsque vous recevez des événements à partir d’ordinateurs différents, il peut être utile spécifier les noms d’instances [EventProcessorHost][] basées sur les ordinateurs (ou les rôles) dans lequel ils sont déployés. Pour plus d’informations sur ces rubriques, consultez les rubriques [Événement Hubs présentation][] générale et [Événement Hubs Programming Guide][] .

<!-- Links -->
[Vue d’ensemble des Hubs événement]: ../articles/event-hubs/event-hubs-overview.md
[Événement Hubs Guide de programmation]: ../articles/event-hubs/event-hubs-programming-guide.md
[Mise à l’échelle des traitement de l’événement]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[Compte de stockage Azure]: ../articles/storage/storage-create-storage-account.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[Portail Azure]: https://portal.azure.com