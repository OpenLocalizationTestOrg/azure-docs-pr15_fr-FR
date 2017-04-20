<properties
    pageTitle="Comment utiliser le stockage de file d’attente (C++) | Microsoft Azure"
    description="Découvrez comment utiliser le service de stockage file d’attente dans Azure. Exemples sont écrits en C++."
    services="storage"
    documentationCenter=".net"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="dineshm"/>

# <a name="how-to-use-queue-storage-from-c"></a>Comment utiliser le stockage de file d’attente à partir de C++  

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Vue d’ensemble
Ce guide vous montrent comment effectuer des scénarios courants au moyen du service de stockage Azure file d’attente. Les exemples sont écrits en C++ et utilisent la [Bibliothèque de Client de stockage Azure pour C++](http://github.com/Azure/azure-storage-cpp/blob/master/README.md). Les scénarios présentés incluent **Insertion**, **lecture**, **prise**et **Supprimer des** messages de file d’attente, ainsi que **créer et supprimer des files d’attente**.

>[AZURE.NOTE] Ce guide cible la bibliothèque de Client de stockage Azure pour C++ version 1.0.0 et au-dessus. La version recommandée est stockage Client bibliothèque 2.2.0, qui est disponible via [NuGet](http://www.nuget.org/packages/wastorage) ou [GitHub](http://github.com/Azure/azure-storage-cpp/).

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Créer une application C++  
Dans ce guide, vous allez utiliser les fonctionnalités de stockage pouvant être exécutées dans une application C++.

Pour ce faire, vous devez installer la bibliothèque de Client de stockage Azure pour C++ et créer un compte de stockage Azure dans votre abonnement Azure.

Pour installer la bibliothèque de Client de stockage Azure pour C++, vous pouvez utiliser les méthodes suivantes :

-   **Linux :** Suivez les instructions fournies dans la page de [Bibliothèque de Client de stockage Azure pour C++ Lisezmoi](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) .
-   **Windows :** Dans Visual Studio, cliquez sur **Outils > Gestionnaire de Package NuGet > Gestionnaire de Package Console**. Tapez la commande suivante dans la [console Gestionnaire de Package NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) , appuyez sur **entrée**.

        Install-Package wastorage

## <a name="configure-your-application-to-access-queue-storage"></a>Configurez votre application pour accéder au stockage de file d’attente
Ajoutez que les instructions vers le haut du fichier C++ où vous souhaitez utiliser le stockage Azure API pour accéder aux files d’attente include suivantes :  

    #include "was/storage_account.h"
    #include "was/queue.h"

## <a name="set-up-an-azure-storage-connection-string"></a>Configurer une chaîne de connexion de stockage Azure

Un client de stockage Azure utilise une chaîne de connexion de stockage pour stocker les points de terminaison et les informations d’identification pour l’accès aux services de gestion des données. Lors de l’exécution d’une application cliente, vous devez fournir la chaîne de connexion de stockage au format suivant, en utilisant le nom de votre compte de stockage et de la touche d’accès de stockage pour le compte de stockage répertorié dans le [Portail Azure](https://portal.azure.com) pour les valeurs de *nom de compte* et *AccountKey* . Pour plus d’informations sur les comptes de stockage et les touches d’accès rapide, voir [à propos des comptes d’espace de stockage Azure](storage-create-storage-account.md). Cet exemple montre comment vous pouvez déclarer un champ statique pour contenir la chaîne de connexion :  

    // Define the connection-string with your values.
    const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

Pour tester votre application sur votre ordinateur Windows local, vous pouvez utiliser l' Microsoft Azure [émulateur de stockage](storage-use-emulator.md) qui est installé avec le [Kit de développement logiciel Azure](https://azure.microsoft.com/downloads/). L’émulateur de stockage est un utilitaire qui simule les services Blob, file d’attente et Table disponibles dans Azure sur votre ordinateur de développement local. L’exemple suivant montre comment vous pouvez déclarer un champ statique pour contenir la chaîne de connexion à votre émulateur stockage local :  

    // Define the connection-string with Azure Storage Emulator.
    const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  

Pour démarrer l’émulateur de stockage Azure, sélectionnez le bouton **Démarrer** ou appuyez sur la touche **Windows** . Commencez à taper **Émulateur de stockage Azure**, puis sélectionnez **Émulateur de stockage de Microsoft Azure** dans la liste des applications.

Les exemples suivants part du principe que vous avez utilisé une de ces deux méthodes pour obtenir la chaîne de connexion de stockage.

## <a name="retrieve-your-connection-string"></a>Récupérer votre chaîne de connexion
Vous pouvez utiliser la classe **cloud_storage_account** pour représenter vos informations de compte de stockage. Pour récupérer vos informations de compte de stockage de la chaîne de connexion de stockage, vous pouvez utiliser la méthode **analyser** .

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

## <a name="how-to-create-a-queue"></a>Comment : créer une file d’attente
Un objet **cloud_queue_client** vous permet d’obtenir des objets de référence des files d’attente. Le code suivant crée un objet **cloud_queue_client** .

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create a queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

Utilisez l’objet **cloud_queue_client** pour obtenir une référence à la file d’attente que vous souhaitez utiliser. Vous pouvez créer la file d’attente s’il n’existe pas.

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Create the queue if it doesn't already exist.
    queue.create_if_not_exists();  

## <a name="how-to-insert-a-message-into-a-queue"></a>Comment : insérer un message dans une file d’attente
Pour insérer un message dans une file d’attente existante, commencez par créer un nouveau **cloud_queue_message**. Ensuite, appelez la méthode **add_message** . Un **cloud_queue_message** peut être créé à partir d’une chaîne ou un tableau **d’octets** . Voici le code qui crée une file d’attente (s’il n’existe pas) et insère le message « Hello, World » :

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Create the queue if it doesn't already exist.
    queue.create_if_not_exists();

    // Create a message and add it to the queue.
    azure::storage::cloud_queue_message message1(U("Hello, World"));
    queue.add_message(message1);  

## <a name="how-to-peek-at-the-next-message"></a>Comment : lire le message suivant
Vous pouvez lire le message situé à l’avant une file d’attente sans le supprimer de la file d’attente en appelant la méthode **peek_message** .

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Peek at the next message.
    azure::storage::cloud_queue_message peeked_message = queue.peek_message();

    // Output the message content.
    std::wcout << U("Peeked message content: ") << peeked_message.content_as_string() << std::endl;

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Comment : modifier le contenu d’un message en file d’attente
Vous pouvez modifier le contenu d’un message sur place dans la file d’attente. Si le message représente une tâche, vous pouvez utiliser cette fonctionnalité pour mettre à jour l’état de la tâche. Le code suivant met à jour le message file d’attente avec le nouveau contenu et définit le délai d’expiration de visibilité pour étendre une autre 60 secondes. Cela enregistre l’état du travail associé au message et vous donne le client un autre minute pour continuer à travailler sur le message. Vous pouvez utiliser cette technique pour effectuer le suivi de plusieurs étapes de flux de travail sur les messages file d’attente, sans avoir à recommencer depuis le début si une étape de traitement échoue en raison de panne logicielle ou matérielle. En règle générale, vous conservez un nombre de tentatives également, et si le message est retentée n plusieurs fois, vous devez le supprimer. Cette option empêche un message qui déclenche une erreur d’application chaque fois qu’il est traité.

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_conection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Get the message from the queue and update the message contents.
    // The visibility timeout "0" means make it visible immediately.
    // The visibility timeout "60" means the client can get another minute to continue
    // working on the message.
    azure::storage::cloud_queue_message changed_message = queue.get_message();

    changed_message.set_content(U("Changed message"));
    queue.update_message(changed_message, std::chrono::seconds(60), true);

    // Output the message content.
    std::wcout << U("Changed message content: ") << changed_message.content_as_string() << std::endl;  

## <a name="how-to-de-queue-the-next-message"></a>Comment : file d’attente le message suivant
Votre code files d’attente désélectionnez les messages provenant d’une file d’attente en deux étapes. Lorsque vous appelez **get_message**, vous obtenez le message suivant dans une file d’attente. Un message retourné par **get_message** devienne invisible à n’importe quel autre code lire des messages de cette file d’attente. Pour terminer la suppression du message de la file d’attente, vous devez également appeler **delete_message**. Ce processus en deux étapes de la suppression d’un message garantit que si votre code ne parvient pas à traiter un message en raison de panne logicielle ou matérielle, une autre instance de votre code pouvez obtenir le même message et essayez à nouveau. Votre code appelle **delete_message** droite une fois que le message a été traité.

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Get the next message.
    azure::storage::cloud_queue_message dequeued_message = queue.get_message();
    std::wcout << U("Dequeued message: ") << dequeued_message.content_as_string() << std::endl;

    // Delete the message.
    queue.delete_message(dequeued_message);

## <a name="how-to-leverage-additional-options-for-de-queuing-messages"></a>Comment : tirer parti des options supplémentaires pour les messages files d’attente
Il existe deux façons, vous pouvez personnaliser la récupération des messages à partir d’une file d’attente. Tout d’abord, vous pouvez obtenir un lot de messages (jusqu'à 32). Ensuite, vous pouvez définir un délai d’expiration plus ou moins longtemps éléments temporairement, ce qui permet de votre code plus ou moins de temps à traiter entièrement chaque message. L’exemple suivant utilise la méthode **get_messages** pour obtenir 20 messages en un seul appel. Il traite ensuite chaque message à l’aide d’une boucle **for** . Il définit également un délai éléments temporairement de cinq minutes pour chaque message. Notez que le démarrage de 5 minutes pour tous les messages en même temps, passé après avoir 5 minutes dans la mesure où l’appel vers **get_messages**, tous les messages qui n’ont pas été supprimés deviennent visible à nouveau.

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Dequeue some queue messages (maximum 32 at a time) and set their visibility timeout to
    // 5 minutes (300 seconds).
    azure::storage::queue_request_options options;
    azure::storage::operation_context context;

    // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
    std::vector<azure::storage::cloud_queue_message> messages = queue.get_messages(20, std::chrono::seconds(300), options, context);

    for (auto it = messages.cbegin(); it != messages.cend(); ++it)
    {
        // Display the contents of the message.
        std::wcout << U("Get: ") << it->content_as_string() << std::endl;
    }

## <a name="how-to-get-the-queue-length"></a>Comment : obtenir la file d’attente
Vous pouvez obtenir une estimation du nombre de messages dans une file d’attente. La méthode **download_attributes** vous invite à fournir le service de file d’attente pour récupérer les attributs de file d’attente, y compris le nombre de messages. La méthode **approximate_message_count** Obtient le nombre approximatif de messages dans la file d’attente.

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Fetch the queue attributes.
    queue.download_attributes();

    // Retrieve the cached approximate message count.
    int cachedMessageCount = queue.approximate_message_count();

    // Display number of messages.
    std::wcout << U("Number of messages in queue: ") << cachedMessageCount << std::endl;  

## <a name="how-to-delete-a-queue"></a>Comment : supprimer une file d’attente
Pour supprimer une file d’attente et tous les messages qu’elle contient, appelez la méthode **delete_queue_if_exists** sur l’objet file d’attente.

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // If the queue exists and delete it.
    queue.delete_queue_if_exists();  

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris les notions de base de stockage file d’attente, suivez ces liens pour en savoir plus sur le stockage Azure.

-   [L’utilisation de stockage d’objets Blob à partir de C++](storage-c-plus-plus-how-to-use-blobs.md)
-   [Comment utiliser le stockage de Table à partir de C++](storage-c-plus-plus-how-to-use-tables.md)
-   [Liste des ressources de stockage Azure dans C++](storage-c-plus-plus-enumeration.md)
-   [Bibliothèque de Client de stockage pour la référence C++](http://azure.github.io/azure-storage-cpp)
-   [Documentation du stockage Azure](https://azure.microsoft.com/documentation/services/storage/)

