<properties
    pageTitle="Prise en main de stockage file d’attente et Visual Studio connecté services (services cloud) | Microsoft Azure"
    description="Comment faire pour commencer à utiliser stockage Azure file d’attente dans un projet de service cloud dans Visual Studio après vous être connecté à un compte de stockage à l’aide de Visual Studio services connectés"
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
    ms.date="07/18/2016"
    ms.author="tarcher"/>

# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Prise en main de stockage Azure file d’attente et Visual Studio connecté services (cloud services projets)

[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Vue d’ensemble

Cet article décrit comment commencer à utiliser stockage Azure file d’attente dans Visual Studio une fois que vous avez créé ou référencé un compte de stockage Azure dans un projet de services cloud à l’aide de la boîte de dialogue Visual Studio **Ajouter les Services connectés** .

Nous allons vous montrer comment créer une file d’attente dans le code. Nous allons également vous montrer comment effectuer des opérations de base file d’attente, telles que l’ajout, modification, lecture et suppression des messages de file d’attente. Les exemples sont écrits en code c# et utilisent de la [Bibliothèque Microsoft Azure stockage Client pour .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

L’opération **d’Ajout de Services connectés** installe les packages NuGet appropriées pour accéder au stockage Azure dans votre projet et ajoute la chaîne de connexion pour le compte de stockage pour vos fichiers de configuration de projet.

 - Pour plus d’informations sur les files d’attente manipulation dans le code, voir [prise en main stockage Azure file d’attente à l’aide de .NET](storage-dotnet-how-to-use-queues.md) .
 - Consultez la [documentation du stockage](https://azure.microsoft.com/documentation/services/storage/) des informations générales sur le stockage Azure.
 - Voir [la documentation de Services de Cloud](https://azure.microsoft.com/documentation/services/cloud-services/) pour des informations générales sur les services en nuage Azure.
 - Pour plus d’informations sur la programmation d’applications ASP.NET, consultez [ASP.NET](http://www.asp.net) .


Stockage de file d’attente Azure est un service pour stocker un grand nombre de messages qui sont accessibles à partir de n’importe où dans le monde via des appels authentifiés à l’aide de HTTP ou HTTPS. Un message file d’attente unique peut contenir jusqu'à 64 Ko et une file d’attente peut contenir des millions de messages, jusqu'à la limite de la capacité totale d’un compte de stockage.


## <a name="access-queues-in-code"></a>Files d’attente Access dans le code

Pour accéder aux files d’attente dans les projets Visual Studio Cloud Services, vous devez inclure les éléments suivants à n’importe quel fichier source c# qui accèdent à stockage Azure file d’attente.

1. Vérifiez que les déclarations d’espace de noms en haut du fichier c# incluent ces instructions **à l’aide** .

        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Obtenir un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Utilisez le code suivant pour obtenir la votre chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenir un objet **CloudQueueClient** pour référencer les objets file d’attente dans votre compte de stockage.  

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Obtenir un objet **CloudQueue** pour faire référence à une file d’attente spécifique.

        // Get a reference to a queue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");


**Remarque :** Utiliser tout le code ci-dessus devant le code dans les exemples suivants.

## <a name="create-a-queue-in-code"></a>Créer une file d’attente dans le code

Pour créer la file d’attente dans le code, il suffit d’ajouter un appel à **CreateIfNotExists**.

    // Create the CloudQueue if it does not exist
    messageQueue.CreateIfNotExists();

## <a name="add-a-message-to-a-queue"></a>Ajouter un message à une file d’attente

Pour insérer un message dans une file d’attente existante, créez un nouvel objet **CloudQueueMessage** , puis appelez la méthode **AddMessage** .

Un objet **CloudQueueMessage** peut être créé à partir d’une chaîne (au format UTF-8) ou un tableau d’octets.

Voici un exemple qui insère le message « Hello, World ».

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    messageQueue.AddMessage(message);

## <a name="read-a-message-in-a-queue"></a>Lire un message dans une file d’attente

Vous pouvez lire le message situé à l’avant une file d’attente sans le supprimer de la file d’attente en appelant la méthode **PeekMessage** .

    // Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

## <a name="read-and-remove-a-message-in-a-queue"></a>Lire et supprimer un message dans une file d’attente

Votre code peut supprimer (file d’attente) un message à partir d’une file d’attente en deux étapes.

1. Contactez le support **GetMessage** pour obtenir le message suivant dans une file d’attente. Un message retourné par **GetMessage** devienne invisible à n’importe quel autre code lire des messages de cette file d’attente. Par défaut, ce message reste invisible pendant 30 secondes.
2.  Pour terminer la suppression du message de la file d’attente, appelez **DeleteMessage**.

Ce processus en deux étapes de la suppression d’un message garantit que si votre code ne parvient pas à traiter un message en raison de panne logicielle ou matérielle, une autre instance de votre code pouvez obtenir le même message et essayez à nouveau. Le code suivant appelle **DeleteMessage** droite une fois que le message a été traité.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

    // Process the message in less than 30 seconds

    // Then delete the message.
    await messageQueue.DeleteMessage(retrievedMessage);


## <a name="use-additional-options-to-process-and-remove-queue-messages"></a>Utiliser des options supplémentaires pour traiter et supprimer des messages de file d’attente

Il existe deux façons, vous pouvez personnaliser la récupération des messages à partir d’une file d’attente.

 - Vous pouvez obtenir un lot de messages (jusqu'à 32).
 - Vous pouvez définir un délai d’expiration plus ou moins longtemps éléments temporairement, ce qui permet de votre code plus ou moins de temps à traiter entièrement chaque message. L’exemple suivant utilise la méthode **GetMessages** pour obtenir 20 messages en un seul appel. Il traite ensuite chaque message à l’aide d’une boucle **foreach** . Il définit également un délai éléments temporairement de cinq minutes pour chaque message. Notez que le démarrage de 5 minutes pour tous les messages en même temps, passé après avoir 5 minutes dans la mesure où l’appel vers **GetMessages**, tous les messages qui n’ont pas été supprimés deviennent visible à nouveau.

Voici un exemple :

    foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.

        // Then delete the message after processing
        messageQueue.DeleteMessage(message);

    }

## <a name="get-the-queue-length"></a>Obtenir la file d’attente

Vous pouvez obtenir une estimation du nombre de messages dans une file d’attente. La méthode **FetchAttributes** vous invite à fournir le service de file d’attente pour récupérer les attributs de file d’attente, y compris le nombre de messages. La propriété **ApproximateMethodCount** retourne la dernière valeur récupérée par la méthode **FetchAttributes** , sans faire appel du service de file d’attente.

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-the-async-await-pattern-with-common-azure-queue-apis"></a>Utiliser le modèle attendre asynchrone avec API de file d’attente Azure courantes

Cet exemple montre comment utiliser le modèle attendre asynchrone avec API de file d’attente Azure courantes. L’exemple appelle la version asynchrone de chacune des méthodes donnés, il puisse être vu par le post-correctif **asynchrone** de chaque méthode. Lorsqu’une méthode asynchrone est utilisée l’asynchrone-attendre motif interrompt exécution locale jusqu'à ce que l’appel est terminé. Ce comportement permet au thread en cours d’effectuer une autre tâche qui permet d’éviter les dégradation des performances et d’améliore la réactivité de votre application. Pour plus d’informations sur l’utilisation du modèle asynchrone Await dans .NET, consultez [asynchrone et Await (c# et Visual Basic)] (https://msdn.microsoft.com/library/hh191443.aspx)

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Add the message asynchronously
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Delete the message asynchronously
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## <a name="delete-a-queue"></a>Supprimer une file d’attente

Pour supprimer une file d’attente et tous les messages qu’elle contient, appelez la méthode **Delete** sur l’objet file d’attente.

    // Delete the queue.
    messageQueue.Delete();

## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
