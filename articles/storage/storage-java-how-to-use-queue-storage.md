<properties
    pageTitle="Comment utiliser le stockage de file d’attente à partir de Java | Microsoft Azure"
    description="Découvrez comment utiliser le service de file d’attente Azure pour créer et supprimer des files d’attente et insérer, obtenir et supprimer des messages. Exemples écrits en Java."
    services="storage"
    documentationCenter="java"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robinsh"/>

# <a name="how-to-use-queue-storage-from-java"></a>Comment utiliser le stockage de file d’attente à partir de Java

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Vue d’ensemble

Ce guide vous montrent comment effectuer des scénarios courants au moyen du service de stockage Azure file d’attente. Les exemples sont écrits en Java et utilisent le [Kit de développement logiciel pour Java stockage Azure][]. Les scénarios présentés comprennent **Insertion**, la **lecture**, **prise**et **suppression de** messages de file d’attente, ainsi que des files d’attente de **Création** et **suppression** . Pour plus d’informations sur les files d’attente, consultez la section [étapes suivantes](#Next-Steps) .

Remarque : Un kit de développement est disponible pour les développeurs qui utilisent le stockage Azure sur les appareils Android. Pour plus d’informations, voir le [Kit de développement logiciel pour Android stockage Azure][].

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Créer une application Java

Dans ce guide, vous allez utiliser les fonctionnalités de stockage pouvant être exécutées dans une application Java localement, ou dans le code qui s’exécutent dans un rôle web ou collaborateur dans Azure.

Pour ce faire, vous devez installer le Kit de développement Java (JDK) et créer un compte de stockage Azure dans votre abonnement Azure. Une fois que vous l’avez fait, vous avez besoin vérifier que votre système de développement répond à la configuration minimale requise et les dépendances qui sont répertoriés dans le référentiel [Azure stockage SDK pour Java][] sur GitHub. Si votre système est conforme à ces exigences, vous pouvez suivre les instructions pour télécharger et installer les bibliothèques de stockage Azure pour Java sur votre système à partir de ce référentiel. Une fois que vous avez terminé ces tâches, vous ne pourrez pas créer une application Java qui utilise les exemples de cet article.

## <a name="configure-your-application-to-access-queue-storage"></a>Configurez votre application pour accéder au stockage de file d’attente

Ajoutez les instructions d’importation suivantes en haut du fichier Java où vous voulez API de stockage Azure permet d’accéder aux files d’attente :

    // Include the following imports to use queue APIs.
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.queue.*;

## <a name="setup-an-azure-storage-connection-string"></a>Programme d’installation une chaîne de connexion de stockage Azure

Un client de stockage Azure utilise une chaîne de connexion de stockage pour stocker les points de terminaison et les informations d’identification pour l’accès aux services de gestion des données. Lors de l’exécution d’une application cliente, vous devez fournir la chaîne de connexion de stockage au format suivant, en utilisant le nom de votre compte de stockage et de la clé primaire access pour le compte de stockage répertorié dans le [Portail Azure](https://portal.azure.com) pour les valeurs de *nom de compte* et *AccountKey* . Cet exemple montre comment vous pouvez déclarer un champ statique pour contenir la chaîne de connexion :

    // Define the connection-string with your values.
    public static final String storageConnectionString =
        "DefaultEndpointsProtocol=http;" +
        "AccountName=your_storage_account;" +
        "AccountKey=your_storage_account_key";

Dans une application qui s’exécutent dans un rôle dans Microsoft Azure, cette chaîne pouvant être stockée dans le fichier de configuration de service, *ServiceConfiguration.cscfg*et sont accessibles via un appel à la méthode **RoleEnvironment.getConfigurationSettings** . Voici un exemple de l’obtention de la chaîne de connexion à partir d’un élément de **paramètre** nommé *StorageConnectionString* dans le fichier de configuration de service :

    // Retrieve storage account from connection-string.
    String storageConnectionString =
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

Les exemples suivants part du principe que vous avez utilisé une de ces deux méthodes pour obtenir la chaîne de connexion de stockage.

## <a name="how-to-create-a-queue"></a>Comment : créer une file d’attente

Un objet **CloudQueueClient** vous permet d’obtenir des objets de référence des files d’attente. Le code suivant crée un objet **CloudQueueClient** . (Remarque : il existe d’autres moyens de créer des objets **CloudStorageAccount** ; pour plus d’informations, voir **CloudStorageAccount** dans la [Référence du Kit de développement logiciel Client Azure stockage].)

Utilisez l’objet **CloudQueueClient** pour obtenir une référence à la file d’attente que vous souhaitez utiliser. Vous pouvez créer la file d’attente s’il n’existe pas.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

       // Create the queue client.
       CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

       // Retrieve a reference to a queue.
       CloudQueue queue = queueClient.getQueueReference("myqueue");

       // Create the queue if it doesn't already exist.
       queue.createIfNotExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-add-a-message-to-a-queue"></a>Comment : ajouter un message à une file d’attente

Pour insérer un message dans une file d’attente existante, commencez par créer un nouveau **CloudQueueMessage**. Ensuite, appelez la méthode **addMessage** . Un **CloudQueueMessage** peut être créé à partir d’une chaîne (au format UTF-8) ou un tableau d’octets. Voici le code qui crée une file d’attente (s’il n’existe pas) et insère le message « Hello, World ».

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Create the queue if it doesn't already exist.
        queue.createIfNotExists();

        // Create a message and add it to the queue.
        CloudQueueMessage message = new CloudQueueMessage("Hello, World");
        queue.addMessage(message);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-peek-at-the-next-message"></a>Comment : lire le message suivant

Vous pouvez lire le message situé à l’avant une file d’attente sans le supprimer de la file d’attente en appelant **peekMessage**.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Peek at the next message.
        CloudQueueMessage peekedMessage = queue.peekMessage();

        // Output the message value.
        if (peekedMessage != null)
        {
          System.out.println(peekedMessage.getMessageContentAsString());
       }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Comment : modifier le contenu d’un message en file d’attente

Vous pouvez modifier le contenu d’un message sur place dans la file d’attente. Si le message représente une tâche, vous pouvez utiliser cette fonctionnalité pour mettre à jour l’état de la tâche. Le code suivant met à jour le message file d’attente avec le nouveau contenu et définit le délai d’expiration de visibilité pour étendre une autre 60 secondes. Cela enregistre l’état du travail associé au message et vous donne le client un autre minute pour continuer à travailler sur le message. Vous pouvez utiliser cette technique pour effectuer le suivi de plusieurs étapes de flux de travail sur les messages file d’attente, sans avoir à recommencer depuis le début si une étape de traitement échoue en raison de panne logicielle ou matérielle. En règle générale, vous conservez un nombre de tentatives également, et si le message est retentée plus de *n* fois, vous devez le supprimer. Cette option empêche un message qui déclenche une erreur d’application chaque fois qu’il est traité.

Le code suivant recherche via la file d’attente de messages, exemple localise le premier message qui correspond à « Bonjour, monde » pour le contenu, puis modifie le contenu de message et a quitté.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // The maximum number of messages that can be retrieved is 32.
        final int MAX_NUMBER_OF_MESSAGES_TO_PEEK = 32;

        // Loop through the messages in the queue.
        for (CloudQueueMessage message : queue.retrieveMessages(MAX_NUMBER_OF_MESSAGES_TO_PEEK,1,null,null))
        {
            // Check for a specific string.
            if (message.getMessageContentAsString().equals("Hello, World"))
            {
                // Modify the content of the first matching message.
                message.setMessageContent("Updated contents.");
                // Set it to be visible in 30 seconds.
                EnumSet<MessageUpdateFields> updateFields =
                    EnumSet.of(MessageUpdateFields.CONTENT,
                    MessageUpdateFields.VISIBILITY);
                // Update the message.
                queue.updateMessage(message, 30, updateFields, null, null);
                break;
            }
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

Par ailleurs, l’exemple de code suivant met à jour uniquement le premier message visible dans la file d’attente.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Retrieve the first visible message in the queue.
        CloudQueueMessage message = queue.retrieveMessage();

        if (message != null)
        {
            // Modify the message content.
            message.setMessageContent("Updated contents.");
            // Set it to be visible in 60 seconds.
            EnumSet<MessageUpdateFields> updateFields =
                EnumSet.of(MessageUpdateFields.CONTENT,
                MessageUpdateFields.VISIBILITY);
            // Update the message.
            queue.updateMessage(message, 60, updateFields, null, null);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-get-the-queue-length"></a>Comment : obtenir la file d’attente

Vous pouvez obtenir une estimation du nombre de messages dans une file d’attente. La méthode **downloadAttributes** vous invite à fournir le service de file d’attente pour plusieurs valeurs en cours, y compris un compteur d’est le nombre de messages dans une file d’attente. Le nombre est uniquement approximatif, car les messages peuvent être ajoutés ou supprimés une fois que le service de file d’attente répond à votre requête. La méthode **getApproximateMessageCount** retourne la dernière valeur récupérée par l’appel à **downloadAttributes**, sans faire appel du service de file d’attente.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

       // Download the approximate message count from the server.
        queue.downloadAttributes();

        // Retrieve the newly cached approximate message count.
        long cachedMessageCount = queue.getApproximateMessageCount();

        // Display the queue length.
        System.out.println(String.format("Queue length: %d", cachedMessageCount));
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-dequeue-the-next-message"></a>Comment : file d’attente le message suivant

Votre code retire un message à partir d’une file d’attente en deux étapes. Lorsque vous appelez **retrieveMessage**, vous obtenez le message suivant dans une file d’attente. Un message retourné par **retrieveMessage** devienne invisible à n’importe quel autre code lire des messages de cette file d’attente. Par défaut, ce message reste invisible pendant 30 secondes. Pour terminer la suppression du message de la file d’attente, vous devez également appeler **deleteMessage**. Ce processus en deux étapes de la suppression d’un message garantit que si votre code ne parvient pas à traiter un message en raison de panne logicielle ou matérielle, une autre instance de votre code pouvez obtenir le même message et essayez à nouveau. Votre code appelle **deleteMessage** droite une fois que le message a été traité.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Retrieve the first visible message in the queue.
        CloudQueueMessage retrievedMessage = queue.retrieveMessage();

        if (retrievedMessage != null)
        {
            // Process the message in less than 30 seconds, and then delete the message.
            queue.deleteMessage(retrievedMessage);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }


## <a name="additional-options-for-dequeuing-messages"></a>Options supplémentaires pour les messages de retrait

Il existe deux façons, vous pouvez personnaliser la récupération des messages à partir d’une file d’attente. Tout d’abord, vous pouvez obtenir un lot de messages (jusqu'à 32). Ensuite, vous pouvez définir un délai d’expiration plus ou moins longtemps éléments temporairement, ce qui permet de votre code plus ou moins de temps à traiter entièrement chaque message.

L’exemple suivant utilise la méthode **retrieveMessages** pour obtenir 20 messages en un seul appel. Il traite ensuite chaque message à l’aide d’une boucle **for** . Il définit également un délai éléments temporairement de cinq minutes (300 secondes) pour chaque message. Notez que les cinq minutes démarre pour tous les messages en même temps, donc lorsque cinq minutes se sont écoulées depuis l’appel vers **retrieveMessages**, tous les messages qui n’ont pas été supprimés deviendra visibles à nouveau.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
        for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
            // Do processing for all messages in less than 5 minutes,
            // deleting each message after processing.
            queue.deleteMessage(message);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-list-the-queues"></a>Comment : répertorier les files d’attente

Pour obtenir une liste des files d’attente en cours, appelez la méthode **CloudQueueClient.listQueues()** , qui retourne une collection d’objets **CloudQueue** .

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient =
            storageAccount.createCloudQueueClient();

        // Loop through the collection of queues.
        for (CloudQueue queue : queueClient.listQueues())
        {
            // Output each queue name.
            System.out.println(queue.getName());
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-delete-a-queue"></a>Comment : supprimer une file d’attente

Pour supprimer une file d’attente et tous les messages qu’elle contient, appelez la méthode **deleteIfExists** sur l’objet **CloudQueue** .

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Delete the queue if it exists.
        queue.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris les notions de base de stockage file d’attente, suivez ces liens pour en savoir plus sur les tâches de stockage plus complexes.

- [Stockage Azure SDK pour Java][]
- [Référence du Kit de développement logiciel Client stockage Azure][]
- [Services de stockage Azure API REST][]
- [Blog de l’équipe stockage Azure][]

[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Stockage Azure SDK pour Java]: https://github.com/azure/azure-storage-java
[Stockage Azure SDK pour Android]: https://github.com/azure/azure-storage-android
[Référence du Kit de développement logiciel Client stockage Azure]: http://dl.windowsazure.com/storage/javadoc/
[Services de stockage Azure API REST]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Blog de l’équipe stockage Azure]: http://blogs.msdn.com/b/windowsazurestorage/
