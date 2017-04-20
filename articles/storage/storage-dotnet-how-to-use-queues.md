<properties
    pageTitle="Prise en main stockage Azure file d’attente à l’aide de .NET | Microsoft Azure"
    description="Files d’attente Azure fournissent une messagerie fiable et asynchrone entre les composants d’application. Nuage de messagerie vous permettent de conserver vos composants d’application à l’échelle séparément."
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/12/2016"
    ms.author="robinsh"/>

# <a name="get-started-with-azure-queue-storage-using-net"></a>Prise en main stockage Azure file d’attente à l’aide de .NET

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Vue d’ensemble

Stockage file d’attente Azure fournit cloud entre les composants d’application de messagerie. Dans la conception d’applications pour échelle, les composants d’application sont souvent découplées, afin qu’ils peuvent faire évoluer indépendamment. File d’attente stockage offre une messagerie asynchrone pour les communications entre les composants d’application, si elles s’exécutent dans le cloud, sur le bureau, sur un serveur local ou sur un appareil mobile. Stockage de file d’attente prend également en charge la gestion des tâches asynchrones et la création de flux de travail de processus.

### <a name="about-this-tutorial"></a>À propos de ce didacticiel

Ce didacticiel montre comment écrire du code .NET pour certains scénarios courants à l’aide de stockage Azure file d’attente. Scénarios couverts incluent la création et suppression des files d’attente et ajout, lecture et suppression des messages de file d’attente.

**Durée estimée d’exécution :** 45 minutes

**Prerequisities :**

- [Microsoft Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx)
- [Bibliothèque de Client de stockage Azure pour .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Gestionnaire de Configuration Azure pour .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
- Un [compte de stockage Azure](storage-create-storage-account.md#create-a-storage-account)


[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### <a name="add-namespace-declarations"></a>Ajouter des déclarations d’espace de noms

Ajoutez le code suivant `using` instructions en haut de la `program.cs` fichier :

    using Microsoft.Azure; // Namespace for CloudConfigurationManager
    using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
    using Microsoft.WindowsAzure.Storage.Queue; // Namespace for Queue storage types

### <a name="parse-the-connection-string"></a>Analyser la chaîne de connexion

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-queue-service-client"></a>Créer le client du service file d’attente

La classe **CloudQueueClient** vous permet de récupérer des files d’attente stockées dans le stockage de file d’attente. Voici un moyen de créer le client de service :

    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Vous êtes maintenant prêt à écrire du code qui lit les données à partir d’et écrit des données vers le stockage de file d’attente.

## <a name="create-a-queue"></a>Créer une file d’attente

Cet exemple montre comment créer une file d’attente s’il n’existe pas déjà :

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a container.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

## <a name="insert-a-message-into-a-queue"></a>Insérer un message dans une file d’attente

Pour insérer un message dans une file d’attente existante, commencez par créer un nouveau **CloudQueueMessage**. Ensuite, appelez la méthode **AddMessage** . Un **CloudQueueMessage** peut être créé à partir d’une chaîne (au format UTF-8) ou un tableau **d’octets** . Voici le code qui crée une file d’attente (s’il n’existe pas) et insère le message « Hello, World » :

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.CreateIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

## <a name="peek-at-the-next-message"></a>Lire le message suivant

Vous pouvez lire le message situé à l’avant une file d’attente sans le supprimer de la file d’attente en appelant la méthode **PeekMessage** .

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Peek at the next message
    CloudQueueMessage peekedMessage = queue.PeekMessage();

    // Display message.
    Console.WriteLine(peekedMessage.AsString);

## <a name="change-the-contents-of-a-queued-message"></a>Modifier le contenu d’un message en file d’attente

Vous pouvez modifier le contenu d’un message sur place dans la file d’attente. Si le message représente une tâche, vous pouvez utiliser cette fonctionnalité pour mettre à jour l’état de la tâche. Le code suivant met à jour le message file d’attente avec le nouveau contenu et définit le délai d’expiration de visibilité pour étendre une autre 60 secondes. Cela enregistre l’état du travail associé au message et vous donne le client un autre minute pour continuer à travailler sur le message. Vous pouvez utiliser cette technique pour effectuer le suivi de plusieurs étapes de flux de travail sur les messages file d’attente, sans avoir à recommencer depuis le début si une étape de traitement échoue en raison de panne logicielle ou matérielle. En règle générale, vous conservez un nombre de tentatives également, et si le message est retentée plus de *n* fois, vous devez le supprimer. Cette option empêche un message qui déclenche une erreur d’application chaque fois qu’il est traité.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Get the message from the queue and update the message contents.
    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.");
    queue.UpdateMessage(message,
        TimeSpan.FromSeconds(60.0),  // Make it visible for another 60 seconds.
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

## <a name="de-queue-the-next-message"></a>File d’attente le message suivant

Votre code files d’attente désélectionnez les messages provenant d’une file d’attente en deux étapes. Lorsque vous appelez **GetMessage**, vous obtenez le message suivant dans une file d’attente. Un message retourné par **GetMessage** devienne invisible à n’importe quel autre code lire des messages de cette file d’attente. Par défaut, ce message reste invisible pendant 30 secondes. Pour terminer la suppression du message de la file d’attente, vous devez également appeler **DeleteMessage**. Ce processus en deux étapes de la suppression d’un message garantit que si votre code ne parvient pas à traiter un message en raison de panne logicielle ou matérielle, une autre instance de votre code pouvez obtenir le même message et essayez à nouveau. Votre code appelle **DeleteMessage** droite une fois que le message a été traité.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Get the next message
    CloudQueueMessage retrievedMessage = queue.GetMessage();

    //Process the message in less than 30 seconds, and then delete the message
    queue.DeleteMessage(retrievedMessage);

## <a name="use-async-await-pattern-with-common-queue-storage-apis"></a>Utiliser motif attendre asynchrone avec courantes API de stockage file d’attente

Cet exemple montre comment utiliser le modèle attendre asynchrone avec courantes API de stockage file d’attente. L’exemple appelle la version asynchrone de chacune des méthodes donnés, comme indiqué par le suffixe *asynchrone* de chaque méthode. Lorsqu’une méthode asynchrone est utilisée, l’asynchrone-attendre motif interrompt exécution locale jusqu'à ce que l’appel est terminé. Ce comportement permet au thread en cours d’effectuer d’autres tâches, qui permet d’éviter les dégradation des performances et d’améliorer la réactivité de votre application. Pour plus d’informations sur l’utilisation du modèle asynchrone Await dans .NET voir [asynchrone et Await (c# et Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)

    // Create the queue if it doesn't already exist
    if(await queue.CreateIfNotExistsAsync())
    {
        Console.WriteLine("Queue '{0}' Created", queue.Name);
    }
    else
    {
        Console.WriteLine("Queue '{0}' Exists", queue.Name);
    }

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Async enqueue the message
    await queue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Async delete the message
    await queue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## <a name="leverage-additional-options-for-de-queuing-messages"></a>Tirer parti des options supplémentaires pour les messages files d’attente

Il existe deux façons, vous pouvez personnaliser la récupération des messages à partir d’une file d’attente.
Tout d’abord, vous pouvez obtenir un lot de messages (jusqu'à 32). Ensuite, vous pouvez définir un délai d’expiration plus ou moins longtemps éléments temporairement, ce qui permet de votre code plus ou moins de temps à traiter entièrement chaque message. L’exemple suivant utilise la méthode **GetMessages** pour obtenir 20 messages en un seul appel. Il traite ensuite chaque message à l’aide d’une boucle **foreach** . Il définit également un délai éléments temporairement de cinq minutes pour chaque message. Notez que le démarrage de 5 minutes pour tous les messages en même temps, passé après avoir 5 minutes dans la mesure où l’appel vers **GetMessages**, tous les messages qui n’ont pas été supprimés deviennent visible à nouveau.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

## <a name="get-the-queue-length"></a>Obtenir la file d’attente

Vous pouvez obtenir une estimation du nombre de messages dans une file d’attente. La méthode **FetchAttributes** vous invite à fournir le service de file d’attente pour récupérer les attributs de file d’attente, y compris le nombre de messages. La propriété **ApproximateMessageCount** retourne la dernière valeur récupérée par la méthode **FetchAttributes** , sans faire appel du service de file d’attente.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Fetch the queue attributes.
    queue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = queue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="delete-a-queue"></a>Supprimer une file d’attente

Pour supprimer une file d’attente et tous les messages qu’elle contient, appelez la méthode **Delete** sur l’objet file d’attente.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Delete the queue.
    queue.Delete();

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris les notions de base de stockage file d’attente, suivez ces liens pour en savoir plus sur les tâches de stockage plus complexes.

- Consultez la documentation de référence de service file d’attente pour plus d’informations sur l’API disponibles :
    - [Bibliothèque de Client de stockage pour référence .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
    - [Référence de l’API REST](http://msdn.microsoft.com/library/azure/dd179355)
- Découvrez comment simplifier le code que vous écrivez pour l’utiliser avec le stockage Azure à l’aide du [Kit de développement logiciel Azure WebJobs](../app-service-web/websites-dotnet-webjobs-sdk.md).
- Afficher d’autres guides de fonctionnalité pour en savoir plus sur les autres options de stockage des données dans Azure.
    - [Prise en main stockage de Table Azure à l’aide de .NET](storage-dotnet-how-to-use-tables.md) pour stocker des données structurées.
    - [Prise en main stockage d’objets Blob Azure à l’aide de .NET](storage-dotnet-how-to-use-blobs.md) pour stocker des données non structurées.
    - [Se connecter à la base de données SQL à l’aide de .NET (c#)](../sql-database/sql-database-develop-dotnet-simple.md) pour stocker des données relationnelles.

  [Download and install the Azure SDK for .NET]: /develop/net/
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Creating a Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx
  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
