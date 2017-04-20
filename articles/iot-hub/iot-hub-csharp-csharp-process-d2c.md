<properties
    pageTitle="Traiter les messages d’appareil-nuage IoT concentrateur (.Net) | Microsoft Azure"
    description="Suivez ce didacticiel pour découvrir des motifs utiles pour traiter les messages d’appareil-nuage IoT concentrateur."
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="csharp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="10/05/2016"
     ms.author="dobett"/>

# <a name="tutorial-how-to-process-iot-hub-device-to-cloud-messages-using-net"></a>Didacticiel : Comment traiter les messages de périphérique-nuage IoT concentrateur à l’aide de .net

[AZURE.INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

## <a name="introduction"></a>Introduction

Concentrateur IoT Azure est un service entièrement géré qui permet aux fiable et sécuriser les communications bidirectionnelle entre millions d’appareils IoT et une application back-end. Autres didacticiels ([prise en main IoT concentrateur] et [Envoyer des messages de cloud vers le périphérique avec concentrateur IoT][lnk-c2d]) vous montrer comment utiliser la fonctionnalité de messagerie appareil-nuage et cloud vers le périphérique base du concentrateur IoT.

Ce didacticiel s’appuie sur le code indiqué dans le didacticiel [prise en main IoT concentrateur] et indique les deux modèles scalable que vous pouvez utiliser pour traiter les messages de l’appareil-nuage :

- Le stockage fiable de messages appareil-nuage dans le [stockage blob Azure]. Un scénario courant est *chemin d’accès à froid* analytique, dans lequel vous stockez les données de télémétrie dans des objets BLOB à utiliser comme entrée dans les processus analytique. Ces processus peuvent être pilotées par les outils tels que [Azure Data Factory] ou la pile [HDInsight (Hadoop)] .

- Le traitement fiable des messages d’appareil-nuage *interactive* . Messages de l’appareil-nuage sont interactifs lorsqu’elles sont immédiates déclencheurs pour un ensemble d’actions dans l’application principale. Par exemple, un appareil peut envoyer un message d’alarme déclenchant l’insertion d’un bon dans un système CRM. En revanche, les messages *du point de données* flux simplement à un moteur analytique. Par exemple, télémétrie température d’un appareil qui doit être stockées pour une analyse ultérieure est un message de point de données.

Comme concentrateur IoT expose un [Événement concentrateur][lnk-event-hubs]-point de terminaison compatible pour recevoir des messages appareil-nuage ce didacticiel utilise une instance de [EventProcessorHost] . Cette instance :

* Fiable stocke les messages *du point de données* dans le stockage blob Azure.
* Transfère *interactifs* appareil-nuage des messages à une [file d’attente Bus des services] Azure pour un traitement immédiat.

Bus des services permet de garantir traitement fiable de messages interactifs, étant donné qu’il fournit les points de contrôle par message et heure, basées sur fenêtre Suppression des doublons.

> [AZURE.NOTE] Une instance de **EventProcessorHost** est la seule façon de traiter les messages interactifs. D’autres options incluent [Azure Service TISSU] [ lnk-service-fabric] et [Azure flux Analytique][lnk-stream-analytics].

À la fin de ce didacticiel, vous exécutez trois applications console Windows :

* **SimulatedDevice**, une version modifiée de l’application créé dans le didacticiel [prise en main IoT concentrateur] , envoie des données point appareil-nuage messages chaque deuxième et interactive appareil-nuage des messages de toutes les 10 secondes. Cette application utilise le protocole AMQP pour communiquer avec IoT concentrateur.
* **ProcessDeviceToCloudMessages** utilise la classe [EventProcessorHost] pour extraire les messages à partir du point de terminaison compatible événement concentrateur. Il puis fiable stocke les messages de point de données dans le stockage blob Azure et transfert des messages interactifs dans une file d’attente Bus des services.
* **ProcessD2CInteractiveMessages** désélectionnez en file d’attente les messages interactifs à partir de la file d’attente Bus des services.

> [AZURE.NOTE] Concentrateur IoT prend en charge SDK pour de nombreux plateformes d’appareil et langues, y compris C, Java et JavaScript. Pour savoir comment remplacer le périphérique simulé dans ce didacticiel avec un appareil physique et comment établir une connexion à un concentrateur IoT appareils, voir le [Centre de développement IoT Azure].

Ce didacticiel s’applique directement à d’autres modes de consommation de messages événement concentrateur compatibles, telles que les projets [HDInsight (Hadoop)] . Pour plus d’informations, voir le [guide du développeur Azure IoT concentrateur - appareil vers le cloud].

Pour effectuer ce didacticiel, vous devez les éléments suivants :

+ Microsoft Visual Studio 2015.

+ Un compte Azure actif. <br/>Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/) en quelques minutes.

Vous devez connaître certaines base de [Stockage Azure] et [Bus des services Azure].


## <a name="send-interactive-messages-from-a-simulated-device"></a>Envoyer des messages interactifs à partir d’un périphérique simulé

Dans cette section, vous modifiez l’application de périphérique simulé que vous avez créé dans le didacticiel [prise en main IoT concentrateur] pour envoyer des messages appareil-nuage interactifs au concentrateur IoT.

1. Dans Visual Studio, dans le projet **SimulatedDevice** , ajoutez la méthode suivante à la classe **Program** .

    ```
    private static async void SendDeviceToCloudInteractiveMessagesAsync()
    {
      while (true)
      {
        var interactiveMessageString = "Alert message!";
        var interactiveMessage = new Message(Encoding.ASCII.GetBytes(interactiveMessageString));
        interactiveMessage.Properties["messageType"] = "interactive";
        interactiveMessage.MessageId = Guid.NewGuid().ToString();

        await deviceClient.SendEventAsync(interactiveMessage);
        Console.WriteLine("{0} > Sending interactive message: {1}", DateTime.Now, interactiveMessageString);

        Task.Delay(10000).Wait();
      }
    }
    ```

    Cette méthode est similaire à la méthode **SendDeviceToCloudMessagesAsync** dans le projet **SimulatedDevice** . Les seules différences sont que vous maintenant définir la propriété système **MessageId** et une propriété utilisateur appelé **messageType**.
    Le code assigne un identificateur unique global (GUID) à la propriété **MessageId** . Le Bus de Service peut utiliser cet identificateur pour supprime les messages qu’elle reçoit les doublons. L’exemple utilise la propriété **messageType** pour distinguer interactive des messages de point de données. L’application transmet ces informations dans les propriétés du message, plutôt que dans le corps du message, afin que le processeur d’événements n’a pas besoin désérialiser le message pour effectuer le routage des messages.

    > [AZURE.NOTE] Il est important de créer le **MessageId** utilisé pour la suppression des doublons messages interactifs dans le code de l’appareil. Communications réseau intermittents ou autres échecs, peuvent entraîner plusieurs retransmission du même message à partir de cet appareil. Vous pouvez également utiliser un ID de message sémantique, par exemple un hachage des champs de données pertinents message, à la place d’un GUID.

2. Ajoutez la méthode suivante dans la méthode **Main** , juste avant le `Console.ReadLine()` ligne :

    ````
    SendDeviceToCloudInteractiveMessagesAsync();
    ````

    > [AZURE.NOTE] Pour simplifier la démonstration, ce didacticiel ne pas implémenter une stratégie de réessayer. Dans le code de production, vous devez implémenter une stratégie de nouvelles tentatives comme intervalle exponentielle, comme indiqué dans l’article MSDN [Transitoires de gestion des pannes].

## <a name="process-device-to-cloud-messages"></a>Traiter les messages appareil-nuage

Dans cette section, vous créez une application de console Windows qui traite les messages appareil-nuage IoT concentrateur. Concentrateur IOT expose un [concentrateur événement]-point de terminaison compatible pour activer une application lire les messages de l’appareil-nuage. Ce didacticiel utilise la classe [EventProcessorHost] pour traiter ces messages dans une application console. Pour plus d’informations sur la façon de traiter les messages à partir de l’événement Hubs, consultez le didacticiel [Prise en main avec Hubs de l’événement] .

Le défi lors de la mise en œuvre d’un stockage fiable des messages de point de données ou le transfert de messages interactifs est ce traitement de l’événement s’appuie sur le consommateur message permettent de fournir des points de contrôle pour sa progression. En outre, pour obtenir un haut débit, lorsque vous lisez à partir de l’événement Hubs vous devez fournir des points de contrôle par lots importants. Cette approche crée la possibilité de transformation en double pour un grand nombre de messages, s’il y a une défaillance et vous rétablissez le point de contrôle précédent. Dans ce didacticiel, vous allez apprendre à synchroniser le stockage Azure écrit et windows de suppression des doublons Bus des services avec points de contrôle **EventProcessorHost** .

Pour écrire des messages dans le stockage Azure fiable, l’exemple utilise la fonctionnalité de valider bloc individuel des [objets BLOB bloc][Azure Block Blobs]. Le processeur d’événements sont additionnés messages en mémoire jusqu'à ce qu’il est temps de fournir un point de contrôle. Par exemple, après que la mémoire tampon cumulée des messages atteigne la taille maximale de bloc de 4 Mo, ou après le Bus des services suppression des doublons fenêtre délai s’écoule. Ensuite, avant le point de contrôle, le code valide un nouveau bloc au blob.

Le processeur d’événements utilise Hubs événement décale message en tant que bloc ID. Ce mécanisme permet le processeur d’événements effectuer une vérification de la suppression des doublons avant qu’il valide le nouveau bloc de stockage, en prenant soin de cas de panne entre commettre un bloc ainsi qu’au point de contrôle.

> [AZURE.NOTE] Ce didacticiel utilise un seul compte de stockage Azure pour écrire tous les messages extraites IoT concentrateur. Pour décider si vous avez besoin utiliser plusieurs comptes de stockage Azure dans votre solution, voir [extensibilité du stockage Azure instructions].

L’application utilise la fonctionnalité de suppression des doublons Bus des services afin d’éviter les doublons lorsqu’il traite les messages interactifs. Le périphérique simulé affecte à chaque message interactif uniques **MessageId**. Ces noms d’activer Bus des services pour vous assurer que, dans la fenêtre de temps spécifié suppression des doublons, aucun deux message avec le même **MessageId** est remis vers les récepteurs. Cette suppression des doublons, ainsi que la sémantique de saisie semi-automatique par message fournie par files d’attente Bus des services, facilite la mise en œuvre le traitement fiable des messages interactifs.

Pour vous assurer qu’aucun message n’est renvoyé en dehors de la fenêtre de suppression des doublons, le code synchronise le mécanisme de point de contrôle **EventProcessorHost** avec la fenêtre de suppression des doublons de file d’attente Bus des services. Cette synchronisation est émise par forcer un point de contrôle au moins une fois chaque fois que la fenêtre de suppression des doublons s’écoule (dans ce didacticiel, la fenêtre est une heure).

> [AZURE.NOTE] Ce didacticiel utilise une seule file Service Bus partitionnée au processus de tous les messages interactifs extraites IoT concentrateur. Pour plus d’informations sur l’utilisation des files d’attente Bus des services afin de respecter les exigences extensibilité élevées de votre solution, consultez la documentation de [Bus des services Azure] .

### <a name="provision-an-azure-storage-account-and-a-service-bus-queue"></a>Configurer un compte de stockage Azure et une file d’attente Bus des services
Pour utiliser la classe [EventProcessorHost] , vous devez disposer d’un compte de stockage Azure pour activer **EventProcessorHost** enregistrer les informations de point de contrôle. Vous pouvez utiliser un compte de stockage Azure existant, ou suivez les instructions dans le [Stockage sur Azure] pour créer un nouveau. Prenez note de la chaîne de connexion de compte de stockage Azure.

> [AZURE.NOTE] Lorsque vous copiez et collez la chaîne de connexion de compte de stockage Azure, vérifiez qu’il n’y a aucuns espaces inclus.

Vous devez également une file d’attente de Bus des services pour activer le traitement fiable de messages interactifs. Vous pouvez créer une file d’attente par programme, avec une fenêtre de suppression des doublons une heure, comme indiqué dans [l’utilisation de files d’attente de Service Bus][file d’attente Bus des services]. Vous pouvez également utiliser le [portail classique Azure][lnk-classic-portal], en procédant comme suit :

1. Dans le coin inférieur gauche, cliquez sur **Nouveau** . Puis cliquez sur **Services d’application** > **Service Bus** > **file d’attente** > **Créer personnalisé**. Entrez le nom **d2ctutorial**, sélectionnez une région et utiliser un espace de noms existant ou créez-en un. Dans la page suivante, sélectionnez **Activer la détection des doublons**et définir la **fenêtre de délai de l’historique de détection de dupliquer** une heure. Puis cliquez sur la coche située dans le coin inférieur droit pour enregistrer votre configuration file d’attente.

    ![Créer une file d’attente portail Azure][30]

2. Dans la liste des files d’attente Bus des services, cliquez sur **d2ctutorial**, puis cliquez sur **configurer**. Créer deux stratégies d’accès partagé, un appelée **Envoyer** avec des autorisations **Envoyer** et un appelée **écouter** avec **écouter** des autorisations. Lorsque vous avez terminé, cliquez sur **Enregistrer** dans la partie inférieure.

    ![Configurer une file d’attente portail Azure][31]

3. Cliquez sur **tableau de bord** dans la partie supérieure et **informations de connexion** dans la partie inférieure. Prenez note des deux chaînes de connexion.

    ![Tableau de bord file d’attente portail Azure][32]

### <a name="create-the-event-processor"></a>Créer le processeur d’événements

1. Dans la solution Visual Studio actuelle, pour créer un projet Visual c# Windows à l’aide du modèle de projet **Application Console** , cliquez sur **fichier** > **Ajouter** > **Nouveau projet**. Assurez-vous que la version .NET Framework est 4.5.1 ou ultérieur. Nommez le projet **ProcessDeviceToCloudMessages**, puis cliquez sur **OK**.

    ![Nouveau projet dans Visual Studio][10]

2. Dans l’Explorateur, droit sur le projet **ProcessDeviceToCloudMessages** , puis cliquez sur **Gérer les Packages NuGet**. La boîte de dialogue **Gestionnaire de Package NuGet** s’affiche.

3. Recherchez **WindowsAzure.ServiceBus**, cliquez sur **l’installation**et acceptez les conditions d’utilisation. Cette opération télécharge, installations et ajoute une référence dans le [package Azure Service Bus NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus), avec toutes ses dépendances.

4. Recherchez **Microsoft.Azure.ServiceBus.EventProcessorHost**, cliquez sur **l’installation**et acceptez les conditions d’utilisation. Cette opération télécharge, installations et ajoute une référence à la [Azure Service événement concentrateur - package EventProcessorHost NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), avec toutes ses dépendances.

5. Droit sur le projet **ProcessDeviceToCloudMessages** et cliquez sur **Ajouter**, puis cliquez sur **cours**. Nommez la nouvelle classe **StoreEventProcessor**, puis cliquez sur **OK** pour créer la classe.

6. Ajoutez les instructions suivantes en haut du fichier StoreEventProcessor.cs :

    ```
    using System.IO;
    using System.Diagnostics;
    using System.Security.Cryptography;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

7. Remplacez le code suivant pour le corps du cours :

    ```
    class StoreEventProcessor : IEventProcessor
    {
      private const int MAX_BLOCK_SIZE = 4 * 1024 * 1024;
      public static string StorageConnectionString;
      public static string ServiceBusConnectionString;

      private CloudBlobClient blobClient;
      private CloudBlobContainer blobContainer;
      private QueueClient queueClient;

      private long currentBlockInitOffset;
      private MemoryStream toAppend = new MemoryStream(MAX_BLOCK_SIZE);

      private Stopwatch stopwatch;
      private TimeSpan MAX_CHECKPOINT_TIME = TimeSpan.FromHours(1);

      public StoreEventProcessor()
      {
        var storageAccount = CloudStorageAccount.Parse(StorageConnectionString);
        blobClient = storageAccount.CreateCloudBlobClient();
        blobContainer = blobClient.GetContainerReference("d2ctutorial");
        blobContainer.CreateIfNotExists();
        queueClient = QueueClient.CreateFromConnectionString(ServiceBusConnectionString);
      }

      Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
      {
        Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
        return Task.FromResult<object>(null);
      }

      Task IEventProcessor.OpenAsync(PartitionContext context)
      {
        Console.WriteLine("StoreEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);

        if (!long.TryParse(context.Lease.Offset, out currentBlockInitOffset))
        {
          currentBlockInitOffset = 0;
        }
        stopwatch = new Stopwatch();
        stopwatch.Start();

        return Task.FromResult<object>(null);
      }

      async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
      {
        foreach (EventData eventData in messages)
        {
          byte[] data = eventData.GetBytes();

          if (eventData.Properties.ContainsKey("messageType") && (string) eventData.Properties["messageType"] == "interactive")
          {
            var messageId = (string) eventData.SystemProperties["message-id"];

            var queueMessage = new BrokeredMessage(new MemoryStream(data));
            queueMessage.MessageId = messageId;
            queueMessage.Properties["messageType"] = "interactive";
            await queueClient.SendAsync(queueMessage);

            WriteHighlightedMessage(string.Format("Received interactive message: {0}", messageId));
            continue;
          }

          if (toAppend.Length + data.Length > MAX_BLOCK_SIZE || stopwatch.Elapsed > MAX_CHECKPOINT_TIME)
          {
            await AppendAndCheckpoint(context);
          }
          await toAppend.WriteAsync(data, 0, data.Length);

          Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
            context.Lease.PartitionId, Encoding.UTF8.GetString(data)));
        }
      }

      private async Task AppendAndCheckpoint(PartitionContext context)
      {
        var blockIdString = String.Format("startSeq:{0}", currentBlockInitOffset.ToString("0000000000000000000000000"));
        var blockId = Convert.ToBase64String(ASCIIEncoding.ASCII.GetBytes(blockIdString));
        toAppend.Seek(0, SeekOrigin.Begin);
        byte[] md5 = MD5.Create().ComputeHash(toAppend);
        toAppend.Seek(0, SeekOrigin.Begin);

        var blobName = String.Format("iothubd2c_{0}", context.Lease.PartitionId);
        var currentBlob = blobContainer.GetBlockBlobReference(blobName);

        if (await currentBlob.ExistsAsync())
        {
          await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
          var blockList = await currentBlob.DownloadBlockListAsync();
          var newBlockList = new List<string>(blockList.Select(b => b.Name));

          if (newBlockList.Count() > 0 && newBlockList.Last() != blockId)
          {
            newBlockList.Add(blockId);
            WriteHighlightedMessage(String.Format("Appending block id: {0} to blob: {1}", blockIdString, currentBlob.Name));
          }
          else
          {
            WriteHighlightedMessage(String.Format("Overwriting block id: {0}", blockIdString));
          }
          await currentBlob.PutBlockListAsync(newBlockList);
        }
        else
        {
          await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
          var newBlockList = new List<string>();
          newBlockList.Add(blockId);
          await currentBlob.PutBlockListAsync(newBlockList);

          WriteHighlightedMessage(String.Format("Created new blob", currentBlob.Name));
        }

        toAppend.Dispose();
        toAppend = new MemoryStream(MAX_BLOCK_SIZE);

        // checkpoint.
        await context.CheckpointAsync();
        WriteHighlightedMessage(String.Format("Checkpointed partition: {0}", context.Lease.PartitionId));

        currentBlockInitOffset = long.Parse(context.Lease.Offset);
        stopwatch.Restart();
      }

      private void WriteHighlightedMessage(string message)
      {
        Console.ForegroundColor = ConsoleColor.Yellow;
        Console.WriteLine(message);
        Console.ResetColor();
      }
    }
    ```

    La classe **EventProcessorHost** appelle ce cours pour traiter les messages appareil-nuage reçus IoT concentrateur. Le code dans ce cours met en œuvre la logique pour stocker les messages parviennent dans un conteneur blob et transférer des messages interactifs dans la file d’attente Bus des services.

    La méthode **OpenAsync** initialise la variable **currentBlockInitOffset** , qui effectue le suivi de l’offset du premier message lu par ce processeur événement actuel. N’oubliez pas que chaque processeur est responsable d’une partition unique.

    La méthode **ProcessEventsAsync** reçoit un lot de messages à partir du IoT Hub et les traite comme suit : il envoie des messages interactifs à la file d’attente Bus des services et ajoute les messages de point de données à la mémoire tampon appelée **toAppend**. Si la mémoire tampon atteint la limite de 4 Mo, ou que les fenêtres de délai de suppression des doublons s’écoule (une heure après un point de contrôle dans ce didacticiel), l’application déclenche un point de contrôle.

    La méthode **AppendAndCheckpoint** génère tout d’abord un ID de bloc pour le bloc à ajouter. Stockage Azure requiert tout bloquent ID pour avoir la même longueur, afin que la méthode remplit le décalage de zéros non significatifs - `currentBlockInitOffset.ToString("0000000000000000000000000")`. Ensuite, si un bloc de cette ID est déjà dans le blob, la méthode remplace par le contenu actuel de la mémoire tampon.

    > [AZURE.NOTE] Pour simplifier le code, ce didacticiel utilise un seul blob par partition pour stocker les messages. Une solution réelle est mise en œuvre yeux en créant des fichiers supplémentaires après un certain laps de temps, ou lorsqu’ils atteignent une certaine taille de fichier. N’oubliez pas qu’un blob Azure bloc peut contenir au maximum 195 Go de données.

8. Dans la classe **programme** , ajoutez l’instruction **using** suivante en haut :

    ```
    using Microsoft.ServiceBus.Messaging;
    ```

9. Modifier la méthode **Main** dans la classe **Program** comme suit. Remplacez **{chaîne de connexion d’un concentrateur d’iot}** par la chaîne de connexion **iothubowner** depuis le didacticiel [prise en main IoT concentrateur] . Remplacez la chaîne de connexion de stockage par la chaîne de connexion que vous avez noté au début de cette section. Remplacez la chaîne de connexion de Service Bus dotés des autorisations **Envoyer** de la file d’attente nommée **d2ctutorial** que vous avez noté au début de cette section :

    ```
    static void Main(string[] args)
    {
      string iotHubConnectionString = "{iot hub connection string}";
      string iotHubD2cEndpoint = "messages/events";
      StoreEventProcessor.StorageConnectionString = "{storage connection string}";
      StoreEventProcessor.ServiceBusConnectionString = "{service bus send connection string}";

      string eventProcessorHostName = Guid.NewGuid().ToString();
      EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, iotHubD2cEndpoint, EventHubConsumerGroup.DefaultGroupName, iotHubConnectionString, StoreEventProcessor.StorageConnectionString, "messages-events");
      Console.WriteLine("Registering EventProcessor...");
      eventProcessorHost.RegisterEventProcessorAsync<StoreEventProcessor>().Wait();

      Console.WriteLine("Receiving. Press enter key to stop worker.");
      Console.ReadLine();
      eventProcessorHost.UnregisterEventProcessorAsync().Wait();
    }
    ```

    > [AZURE.NOTE] Pour simplifier la démonstration, ce didacticiel utilise une seule instance de la classe [EventProcessorHost] . Pour plus d’informations, voir le [Guide de programmation Hubs événement].

## <a name="receive-interactive-messages"></a>Recevoir des messages interactifs
Dans cette section, vous écrivez une application de console Windows qui reçoit les messages interactifs à partir de la file d’attente Bus des services. Pour plus d’informations sur l’architecture d’une solution à l’aide de Bus des services, voir [créer des applications à plusieurs niveaux à Service][].

1. Dans la solution Visual Studio actuelle, créez un projet Visual c# Windows à l’aide du modèle de projet **Application Console** . Nommez le projet **ProcessD2CInteractiveMessages**.

2. Dans l’Explorateur, droit sur le projet **ProcessD2CInteractiveMessages** , puis cliquez sur **Gérer les Packages NuGet**. Cette opération affiche la fenêtre **Gestionnaire de Package NuGet** .

3. Recherchez **WindowsAzure.ServiceBus**, cliquez sur **l’installation**et acceptez les conditions d’utilisation. Cette opération télécharge, installations et ajoute une référence au [Bus des services Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus), avec toutes ses dépendances.

4. Ajoutez les instructions suivantes **à l’aide** en haut du fichier **Program.cs** :

    ```
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```

5. Enfin, ajoutez les lignes suivantes à la méthode **Main** . Remplacez par la chaîne de connexion avec des autorisations **écouter** pour la file d’attente nommée **d2ctutorial**:

    ```
    Console.WriteLine("Process D2C Interactive Messages app\n");

    string connectionString = "{service bus listen connection string}";
    QueueClient Client = QueueClient.CreateFromConnectionString(connectionString);

    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = false;
    options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

    Client.OnMessage((message) =>
    {
      try
      {
        var bodyStream = message.GetBody<Stream>();
        bodyStream.Position = 0;
        var bodyAsString = new StreamReader(bodyStream, Encoding.ASCII).ReadToEnd();

        Console.WriteLine("Received message: {0} messageId: {1}", bodyAsString, message.MessageId);

        message.Complete();
      }
      catch (Exception)
      {
        message.Abandon();
      }
    }, options);

    Console.WriteLine("Receiving interactive messages from SB queue...");
    Console.WriteLine("Press any key to exit.");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1.  Dans Visual Studio, dans l’Explorateur de solutions, avec le bouton droit de votre solution, puis sélectionnez **Définir les projets de démarrage**. Sélectionnez **plusieurs projets de démarrage**, puis sélectionnez **Démarrer** l’action pour les projets **ProcessDeviceToCloudMessages**, **SimulatedDevice**et **ProcessD2CInteractiveMessages** .

2.  Appuyez sur **F5** pour démarrer les trois applications console. L’application **ProcessD2CInteractiveMessages** doit traiter chaque message interactif envoyé à partir de l’application **SimulatedDevice** .

  ![Trois applications console][50]

> [AZURE.NOTE] Pour afficher les mises à jour dans votre blob, vous devrez peut-être réduire la constante **MAX_BLOCK_SIZE** de la classe **StoreEventProcessor** à une valeur inférieure, par exemple **1024**. Cette modification est utile, car il prend un certain temps pour atteindre la limite de taille de bloc avec les données envoyées par le périphérique simulé. Avec une plus petite taille de bloc, vous n’avez pas besoin d’attendre si longtemps pour afficher le blob créées et mis à jour. Toutefois, à l’aide d’une plus grande taille de bloc rend l’application plus adaptée.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris comment traiter fiable point de données et les messages d’appareil-nuage interactive à l’aide de la classe [EventProcessorHost] .

[Comment envoyer des messages de cloud vers le périphérique avec concentrateur IoT] [ lnk-c2d] vous montre comment envoyer des messages à vos périphériques à partir de votre serveur principal.

Pour voir des exemples de solutions de bout en bout complètes qui utilisent IoT concentrateur, voir [Azure IoT Suite][lnk-suite].

Pour plus d’informations sur le développement de solutions avec concentrateur IoT, consultez le [Guide du développeur IoT concentrateur].

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png
[10]: ./media/iot-hub-csharp-csharp-process-d2c/create-identity-csharp1.png

[30]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue2.png
[31]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue3.png
[32]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue4.png

<!-- Links -->

[Stockage d’objets blob Azure]: ../storage/storage-dotnet-how-to-use-blobs.md
[Données Azure usine]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[File d’attente Bus des services]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md

[Guide du développeur IoT concentrateur Azure - appareil vers le cloud]: iot-hub-devguide-messaging.md

[Stockage Azure]: https://azure.microsoft.com/documentation/services/storage/
[Bus des services Azure]: https://azure.microsoft.com/documentation/services/service-bus/

[Guide du développeur IoT concentrateur]: iot-hub-devguide.md
[Prise en main IoT concentrateur]: iot-hub-csharp-csharp-getstarted.md
[Centre de développement IoT Azure]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Gestion des erreurs transitoires]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[À propos du stockage Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Prise en main avec Hubs d’événement]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[Extensibilité élevées de stockage Azure instructions]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Event Hubs]: ../event-hubs/event-hubs-overview.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[Événement Hubs Guide de programmation]: ../event-hubs/event-hubs-programming-guide.md
[Gestion des erreurs transitoires]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Créer des applications à plusieurs niveaux à Service]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md

[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-c2d]: iot-hub-csharp-csharp-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
