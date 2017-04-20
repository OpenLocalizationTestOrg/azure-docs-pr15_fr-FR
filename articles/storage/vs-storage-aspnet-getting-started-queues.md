<properties
    pageTitle="Prise en main de stockage file d’attente et Visual Studio connecté services (ASP.NET) | Microsoft Azure"
    description="Comment faire pour commencer à utiliser stockage Azure file d’attente dans un projet ASP.NET dans Visual Studio après vous être connecté à un compte de stockage à l’aide de Visual Studio services connectés"
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-getting-started"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="tarcher"/>

# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services"></a>Prise en main Azure file d’attente de stockage et Visual Studio services connectés

[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Vue d’ensemble

Cet article décrit comment commencer à utiliser stockage Azure file d’attente dans Visual Studio une fois que vous avez créé ou référencé un compte de stockage Azure dans un projet ASP.NET à l’aide de la boîte de dialogue Visual Studio **Ajouter les Services connectés** .

Nous allons vous montrer comment créer et accéder à une file d’attente Azure dans votre compte de stockage. Nous allons également vous montrer comment effectuer des opérations de base file d’attente, telles que l’ajout, modification, lecture et suppression des messages de file d’attente. Les exemples sont écrits en code c# et utilisent de la [Bibliothèque Microsoft Azure stockage Client pour .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx). Pour plus d’informations sur ASP.NET, consultez [ASP.NET](http://www.asp.net).

Stockage de file d’attente Azure est un service pour stocker un grand nombre de messages qui sont accessibles à partir de n’importe où dans le monde via des appels authentifiés à l’aide de HTTP ou HTTPS. Un message file d’attente unique peut contenir jusqu'à 64 Ko et une file d’attente peut contenir des millions de messages, jusqu'à la limite de la capacité totale d’un compte de stockage.

## <a name="access-queues-in-code"></a>Files d’attente Access dans le code

Pour accéder aux files d’attente dans les projets ASP.NET, vous devez inclure les éléments suivants à n’importe quel fichier source c# qui accèdent à stockage Azure file d’attente.

1. Vérifiez que les déclarations d’espace de noms en haut du fichier c# incluent ces instructions **à l’aide** .

        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Obtenir un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Utilisez le code suivant pour obtenir la votre chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenir un objet **CloudQueueClient** pour référencer les objets file d’attente dans votre compte de stockage.  

        // Create the CloudQueueClient object for this storage account.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Obtenir un objet **CloudQueue** pour faire référence à une file d’attente spécifique.

        // Get a reference to a queue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");


**Remarque** Utiliser tout le code ci-dessus devant le code dans les exemples suivants.

## <a name="create-a-queue-in-code"></a>Créer une file d’attente dans le code

Pour créer une file d’attente Azure dans le code, simplement ajouter un appel à **CreateIfNotExists** au code ci-dessus.

    // Create the messageQueue if it does not exist
    messageQueue.CreateIfNotExists();

## <a name="add-a-message-to-a-queue"></a>Ajouter un message à une file d’attente

Pour insérer un message dans une file d’attente existante, créez un nouvel objet **CloudQueueMessage** , puis appelez la méthode **AddMessage** .

Un objet **CloudQueueMessage** peut être créé à partir d’une chaîne (au format UTF-8) ou un tableau d’octets.

Voici un exemple qui insère le message « Hello, World ».

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    messageQueue.AddMessage(message);

## <a name="read-a-message-in-a-queue"></a>Lire un message dans une file d’attente

Vous pouvez lire le message situé à l’avant une file d’attente sans le supprimer de la file d’attente en appelant la méthode PeekMessage ().

    // Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

## <a name="read-and-remove-a-message-in-a-queue"></a>Lire et supprimer un message dans une file d’attente

Votre code peut supprimer (file d’attente) un message à partir d’une file d’attente en deux étapes.
1. Contactez le support GetMessage() pour obtenir le message suivant dans une file d’attente. Un message retourné par GetMessage() devienne invisible à n’importe quel autre code lire des messages de cette file d’attente. Par défaut, ce message reste invisible pendant 30 secondes.
2.  Pour terminer la suppression du message de la file d’attente, appelez **DeleteMessage**.

Ce processus en deux étapes de la suppression d’un message garantit que si votre code ne parvient pas à traiter un message en raison de panne logicielle ou matérielle, une autre instance de votre code pouvez obtenir le même message et essayez à nouveau. Le code suivant appelle **DeleteMessage** droite une fois que le message a été traité.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

    // Process the message in less than 30 seconds

    // Then delete the message.
    await messageQueue.DeleteMessage(retrievedMessage);


## <a name="use-additional-options-for-de-queuing-messages"></a>Utiliser des options supplémentaires pour les messages files d’attente

Il existe deux façons, vous pouvez personnaliser la récupération des messages à partir d’une file d’attente.
Tout d’abord, vous pouvez obtenir un lot de messages (jusqu'à 32). Ensuite, vous pouvez définir un délai d’expiration plus ou moins longtemps éléments temporairement, ce qui permet de votre code plus ou moins de temps à traiter entièrement chaque message. L’exemple suivant utilise la méthode **GetMessages** pour obtenir 20 messages en un seul appel. Il traite ensuite chaque message à l’aide d’une boucle **foreach** . Il définit également un délai éléments temporairement de cinq minutes pour chaque message. Notez que le démarrage de 5 minutes pour tous les messages en même temps, passé après avoir 5 minutes dans la mesure où l’appel vers **GetMessages**, tous les messages qui n’ont pas été supprimés deviendra visible à nouveau.

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

Vous pouvez obtenir une estimation du nombre de messages dans une file d’attente. La méthode **FetchAttributes** vous invite à indiquer le queueservice pour récupérer les attributs de file d’attente, y compris le nombre de messages. La propriété **ApproximateMethodCount** retourne la dernière valeur récupérée par la méthode **FetchAttributes** , sans appeler la queueservice.

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-async-await-pattern-with-common-queueapis"></a>Utiliser motif attendre asynchrone avec queueAPIs courantes

Cet exemple montre comment utiliser le modèle attendre asynchrone avec queueAPIs courantes. L’exemple appelle la version asynchrone de chacune des méthodes donnés, il puisse être vu par le post-correctif asynchrone de chaque méthode. Lorsqu’une méthode asynchrone est utilisée l’asynchrone-attendre motif interrompt exécution locale jusqu'à ce que l’appel est terminé. Ce comportement permet au thread en cours d’effectuer une autre tâche qui permet d’éviter les dégradation des performances et d’améliore la réactivité de votre application. Pour plus d’informations sur l’utilisation du modèle asynchrone Await dans .NET, consultez [asynchrone et Await (c# et Visual Basic)] (https://msdn.microsoft.com/library/hh191443.aspx)

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Async enqueue the message
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Async delete the message
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## <a name="delete-a-queue"></a>Supprimer une file d’attente

Pour supprimer une file d’attente et tous les messages qu’elle contient, appelez la méthode **Delete** sur l’objet file d’attente.

    // Delete the queue.
    messageQueue.Delete();

## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]