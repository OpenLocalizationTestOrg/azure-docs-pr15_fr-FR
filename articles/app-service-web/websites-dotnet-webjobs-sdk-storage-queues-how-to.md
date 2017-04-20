<properties 
    pageTitle="Comment utiliser le stockage Azure file d’attente avec WebJobs SDK" 
    description="Apprenez à utiliser le stockage Azure file d’attente avec WebJobs SDK. Créer et supprimer des files d’attente. Insérer, lire, obtenir et supprimer des messages file d’attente et bien plus encore." 
    services="app-service\web, storage" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="06/01/2016" 
    ms.author="tdykstra"/>

# <a name="how-to-use-azure-queue-storage-with-the-webjobs-sdk"></a>Comment utiliser le stockage Azure file d’attente avec WebJobs SDK

## <a name="overview"></a>Vue d’ensemble

Ce guide fournit des exemples de code c# qui montrent comment utiliser la version Azure WebJobs SDK 1.x avec le service de stockage Azure file d’attente.

Part du principe que vous savez [comment créer un projet WebJob dans Visual Studio avec les chaînes de connexion qui pointent vers votre compte de stockage](websites-dotnet-webjobs-sdk-get-started.md#configure-storage) ou entre [plusieurs comptes de stockage](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/MultipleStorageAccountsEndToEndTests.cs).

La plupart des extraits de code affiche uniquement les fonctions, pas le code qui crée le `JobHost` objet comme dans cet exemple :

        static void Main(string[] args)
        {
            JobHost host = new JobHost();
            host.RunAndBlock();
        }
        
Le guide comprend les rubriques suivantes :

-   [Comment faire pour déclencher la lecture d’une fonction lors de la réception d’un message file d’attente](#trigger)
    - Messages de chaîne file d’attente
    - Messages de file d’attente POCO
    - Fonctions asynchrones
    - Types d’avec que l’attribut QueueTrigger fonctionne
    - Algorithme de sondage
    - Plusieurs instances
    - Exécution en parallèle
    - Obtenir la file d’attente ou les métadonnées de message file d’attente
    - Arrêt
-   [Comment créer un message file d’attente pendant le traitement d’un message file d’attente](#createqueue)
    - Messages de chaîne file d’attente
    - Messages de file d’attente POCO
    - Créer plusieurs messages ou dans des fonctions asynchrones
    - Types d’avec que l’attribut file d’attente fonctionne
    - Utiliser les attributs WebJobs SDK dans le corps d’une fonction
-   [Comment lire et écrire des objets BLOB pendant le traitement d’un message file d’attente](#blobs)
    - Messages de chaîne file d’attente
    - Messages de file d’attente POCO
    - Types d’avec que l’attribut Blob fonctionne
-   [Comment gérer les messages poison](#poison)
    - Gestion des messages poison automatique
    - Gestion des messages poison manuel
-   [Comment définir les options de configuration](#config)
    - Définir des chaînes de connexion SDK dans le code
    - Configurer les paramètres de QueueTrigger
    - Définir les valeurs pour WebJobs SDK paramètres constructeur dans le code
-   [Comment faire pour déclencher manuellement une fonction](#manual)
-   [Comment écrire des journaux](#logs) 
-   [Comment gérer les erreurs et configurer des délais d’expiration](#errors)
-   [Étapes suivantes](#nextsteps)

## <a id="trigger"></a>Comment faire pour déclencher la lecture d’une fonction lors de la réception d’un message file d’attente

Pour écrire une fonction qui le WebJobs SDK appelle lors de la réception d’un message file d’attente, utilisez la `QueueTrigger` attribut. Le constructeur attribut prend un paramètre de chaîne qui spécifie le nom de la file d’attente d’interrogation. Vous pouvez également [définir le nom de la file d’attente dynamiquement](#config).

### <a name="string-queue-messages"></a>Messages de chaîne file d’attente

Dans l’exemple suivant, la file d’attente contient un message de type chaîne, donc `QueueTrigger` est appliqué à un paramètre de chaîne nommé `logMessage` qui contient le contenu du message file d’attente. La fonction [écrit un message de journal au tableau de bord](#logs).
 

        public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
        {
            logger.WriteLine(logMessage);
        }

En outre `string`, le paramètre peut être un tableau d’octets, un `CloudQueueMessage` objet ou un POCO que vous définissez.

### <a name="poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO [(simple objet CLR ancien](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) messages en file d’attente

Dans l’exemple suivant, le message file d’attente contient JSON pour un `BlobInformation` objet qui inclut un `BlobName` propriété. Le Kit de développement désérialise automatiquement l’objet.

        public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
        {
            logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
        }

Le Kit de développement utilise le [package Newtonsoft.Json NuGet](http://www.nuget.org/packages/Newtonsoft.Json) pour sérialiser et désérialiser les messages. Si vous créez des messages file d’attente dans un programme qui n’utilise pas le WebJobs SDK, vous pouvez écrire du code à l’exemple suivant pour créer un message de file d’attente POCO le Kit de développement peut analyser. 

        BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
        var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
        logQueue.AddMessage(queueMessage);

### <a name="async-functions"></a>Fonctions asynchrones

Le suivant asynchrone fonction [écrit un journal au tableau de bord](#logs).

        public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
        {
            await logger.WriteLineAsync(logMessage);
        }

Fonctions asynchrones peuvent prendre un [jeton d’annulation](http://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken), comme le montre l’exemple suivant qui copie un blob. (Pour une explication de le `queueTrigger` espace réservé, reportez-vous à la section [des objets BLOB](#blobs) .)

        public async static Task ProcessQueueMessageAsyncCancellationToken(
            [QueueTrigger("blobcopyqueue")] string blobName, 
            [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
            CancellationToken token)
        {
            await blobInput.CopyToAsync(blobOutput, 4096, token);
        }

### <a id="qtattributetypes"></a>Types d’avec que l’attribut QueueTrigger fonctionne

Vous pouvez utiliser `QueueTrigger` avec les types suivants :

* `string`
* Un type de POCO sérialisé au format JSON
* `byte[]`
* `CloudQueueMessage`

### <a id="polling"></a>Algorithme de sondage

Le Kit de développement met en œuvre un exponentielle recul aléatoires pour réduire l’effet d’idle-file d’attente d’interrogation sur les coûts de stockage transaction.  Lorsqu’un message est détecté, le Kit de développement attend deux secondes, puis vérifie pour un autre message ; Lorsqu’aucun message n’est trouvé il attend environ quatre secondes avant de réessayer. Après avoir d’autres tentatives échecs pour obtenir un message file d’attente, le délai d’attente continue d’augmenter jusqu'à ce qu’elle atteigne la durée maximale par défaut, une minute. [Le délai d’attente maximal peut être configuré](#config).

### <a id="instances"></a>Plusieurs instances

Si votre application web s’exécute sur plusieurs instances, une WebJob continue s’exécute sur chaque ordinateur et chaque ordinateur s’attendre déclencheurs et essayez d’exécuter des fonctions. Le déclencheur file d’attente WebJobs SDK empêche automatiquement une fonction de traiter un message file d’attente plusieurs fois ; fonctions ne doivent pas être écrites pour être équipotentes. Toutefois, si vous voulez vous assurer que seule une instance d’une fonction s’exécute même lorsqu’il existe plusieurs instances de l’application web hôte, vous pouvez utiliser la `Singleton` attribut. 

### <a id="parallel"></a>Exécution en parallèle

Si vous avez plusieurs fonctions à l’écoute sur différentes files d’attente, le Kit de développement appelle les en parallèle lorsque les messages sont reçus simultanément. 

Vaut lorsque plusieurs messages sont reçus pour une seule file d’attente. Par défaut, le Kit de développement Obtient un lot de 16 messages file d’attente à un moment et exécute la fonction qui les traite en parallèle. [La taille du lot est configurable](#config). Lorsque le nombre en cours de traitement obtient vers le bas à la moitié de la taille du lot, le Kit de développement Obtient un autre lot et démarre le traitement des messages. Le nombre maximal de messages simultanées traitées par la fonction est donc la taille du lot une fois et demie. Cette limite s’applique séparément à chaque fonction qui a un `QueueTrigger` attribut. 

Si vous ne voulez pas l’exécution en parallèle pour les messages reçus sur une file d’attente, vous pouvez définir la taille du lot 1. Voir aussi **plus de contrôle sur le traitement des files d’attente** dans la [version commerciale Azure WebJobs SDK 1.1.0](/blog/azure-webjobs-sdk-1-1-0-rtm/).

### <a id="queuemetadata"></a>Obtenir la file d’attente ou les métadonnées de message file d’attente

Vous pouvez obtenir des propriétés de message suivantes en ajoutant des paramètres pour la signature de méthode :

* `DateTimeOffset`expirationTime
* `DateTimeOffset`insertionTime
* `DateTimeOffset`nextVisibleTime
* `string`queueTrigger (contient le texte du message)
* `string`ID
* `string`popReceipt
* `int`dequeueCount

Si vous voulez travailler directement avec l’API de stockage Azure, vous pouvez également ajouter un `CloudStorageAccount` paramètre.

L’exemple suivant écrit toutes ces métadonnées à un enregistrement d’informations. Dans l’exemple, logMessage et queueTrigger contiennent le contenu du message file d’attente.

        public static void WriteLog([QueueTrigger("logqueue")] string logMessage,
            DateTimeOffset expirationTime,
            DateTimeOffset insertionTime,
            DateTimeOffset nextVisibleTime,
            string id,
            string popReceipt,
            int dequeueCount,
            string queueTrigger,
            CloudStorageAccount cloudStorageAccount,
            TextWriter logger)
        {
            logger.WriteLine(
                "logMessage={0}\n" +
            "expirationTime={1}\ninsertionTime={2}\n" +
                "nextVisibleTime={3}\n" +
                "id={4}\npopReceipt={5}\ndequeueCount={6}\n" +
                "queue endpoint={7} queueTrigger={8}",
                logMessage, expirationTime,
                insertionTime,
                nextVisibleTime, id,
                popReceipt, dequeueCount,
                cloudStorageAccount.QueueEndpoint,
                queueTrigger);
        }

Voici un exemple de journal rédigé par l’exemple de code :

        logMessage=Hello world!
        expirationTime=10/14/2014 10:31:04 PM +00:00
        insertionTime=10/7/2014 10:31:04 PM +00:00
        nextVisibleTime=10/7/2014 10:41:23 PM +00:00
        id=262e49cd-26d3-4303-ae88-33baf8796d91
        popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
        dequeueCount=1
        queue endpoint=https://contosoads.queue.core.windows.net/
        queueTrigger=Hello world!

### <a id="graceful"></a>Arrêt

Une fonction qui s’exécute dans une WebJob continue peut accepter une `CancellationToken` paramètre qui permet au système d’exploitation informer la fonction lorsque la WebJob est sur le point d’être arrêté. Vous pouvez utiliser cette notification pour vous assurer que la fonction n’arrêt inattendu d’une manière qui laisse les données dans un état n’est pas cohérente.

L’exemple suivant montre comment vérifier les frais de résiliation WebJob imminents dans une fonction.

    public static void GracefulShutdownDemo(
                [QueueTrigger("inputqueue")] string inputText,
                TextWriter logger,
                CancellationToken token)
    {
        for (int i = 0; i < 100; i++)
        {
            if (token.IsCancellationRequested)
            {
                logger.WriteLine("Function was cancelled at iteration {0}", i);
                break;
            }
            Thread.Sleep(1000);
            logger.WriteLine("Normal processing for queue message={0}", inputText);
        }
    }

**Remarque :** Le tableau de bord ne peut pas afficher correctement l’état et la sortie de fonctions qui ont été arrêtés.
 
Pour plus d’informations, voir [Arrêt WebJobs](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).   

## <a id="createqueue"></a>Comment créer un message file d’attente pendant le traitement d’un message file d’attente

Pour écrire une fonction qui crée un nouveau message file d’attente, utilisez la `Queue` attribut. Comme `QueueTrigger`, vous passez le nom de la file d’attente sous forme de chaîne ou vous pouvez [définir le nom de la file d’attente dynamiquement](#config).

### <a name="string-queue-messages"></a>Messages de chaîne file d’attente

L’exemple de code non asynchrone suivant crée un nouveau message file d’attente dans la file d’attente nommée « outputqueue » avec le même contenu que le message file d’attente reçu dans la file d’attente nommée « inputqueue ». (Pour une opération asynchrone fonctions utilisent `IAsyncCollector<T>` comme indiqué plus loin dans cette section.)


        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] string queueMessage,
            [Queue("outputqueue")] out string outputQueueMessage )
        {
            outputQueueMessage = queueMessage;
        }
  
### <a name="poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO [(simple objet CLR ancien](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) messages en file d’attente

Pour créer un message file d’attente qui contient un POCO plutôt qu’une chaîne, passez le type POCO comme paramètre de sortie pour le `Queue` constructeur d’attribut.
 
        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
            [Queue("outputqueue")] out BlobInformation blobInfoOutput )
        {
            blobInfoOutput = blobInfoInput;
        }

Le Kit de développement sérialise automatiquement l’objet en JSON. Un message file d’attente est toujours créé, même si l’objet est null.

### <a name="create-multiple-messages-or-in-async-functions"></a>Créer plusieurs messages ou dans des fonctions asynchrones

Pour créer plusieurs messages, vérifiez le type de paramètre pour la file d’attente de sortie `ICollector<T>` ou `IAsyncCollector<T>`, comme illustré dans l’exemple suivant.

        public static void CreateQueueMessages(
            [QueueTrigger("inputqueue")] string queueMessage,
            [Queue("outputqueue")] ICollector<string> outputQueueMessage,
            TextWriter logger)
        {
            logger.WriteLine("Creating 2 messages in outputqueue");
            outputQueueMessage.Add(queueMessage + "1");
            outputQueueMessage.Add(queueMessage + "2");
        }

Chaque message file d’attente est créée immédiatement lors de la `Add` méthode est appelée.

### <a name="types-that-the-queue-attribute-works-with"></a>Types de fonctionnant avec l’attribut file d’attente

Vous pouvez utiliser la `Queue` attribut sur les types de paramètre suivants :

* `out string`(crée message file d’attente si la valeur du paramètre n’est pas null expiration de la fonction)
* `out byte[]`(fonctionne comme `string`) 
* `out CloudQueueMessage`(fonctionne comme `string`) 
* `out POCO`(un type sérialisable, crée un message avec un objet null si le paramètre est null à la fin de la fonction)
* `ICollector`
* `IAsyncCollector`
* `CloudQueue`(pour la création des messages manuellement à l’aide de l’API de stockage Azure directement)

### <a id="ibinder"></a>Utiliser les attributs WebJobs SDK dans le corps d’une fonction

Si vous devez effectuer certaines tâches dans votre fonction avant d’utiliser un attribut WebJobs SDK tel que `Queue`, `Blob`, ou `Table`, vous pouvez utiliser la `IBinder` interface.

L’exemple suivant prend un message file d’attente d’entrée et crée un nouveau message avec le même contenu dans une file d’attente de sortie. Le nom de file d’attente de sortie est défini par le code dans le corps de la fonction.

        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] string queueMessage,
            IBinder binder)
        {
            string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
            QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
            CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
            outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
        }

La `IBinder` interface peut également être utilisée avec la `Table` et `Blob` attributs.

## <a id="blobs"></a>Comment lire et écrire des BLOB et les tables pendant le traitement d’un message file d’attente

La `Blob` et `Table` attributs permettent de lire et écrire des tables et des objets BLOB. Les exemples de cette section s’applique aux objets BLOB. Pour les exemples de code qui montrent comment déclencher processus lorsque des objets BLOB sont créés ou mis à jour, voir [l’utilisation de stockage d’objets blob Azure avec le Kit de développement WebJobs](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)et pour des exemples de code que lire et écrire des tables, voir [comment utiliser le stockage de table Azure avec le Kit de développement WebJobs](websites-dotnet-webjobs-sdk-storage-tables-how-to.md).

### <a name="string-queue-messages-triggering-blob-operations"></a>Messages de file d’attente chaîne déclenchement blob opérations

Pour un message file d’attente qui contient une chaîne `queueTrigger` est un espace réservé que vous pouvez utiliser dans le `Blob` l’attribut `blobPath` paramètre qui contient le texte du message. 

L’exemple suivant utilise `Stream` objets pour lire et écrire des objets BLOB. Le message file d’attente est le nom d’un objet blob situé dans le conteneur textblobs. Une copie du blob avec «-nouveau « ajouté au nom est créé dans le même conteneur. 

        public static void ProcessQueueMessage(
            [QueueTrigger("blobcopyqueue")] string blobName, 
            [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
        {
            blobInput.CopyTo(blobOutput, 4096);
        }

La `Blob` attribut constructeur prend une `blobPath` paramètre qui spécifie le nom du conteneur et blob. Pour plus d’informations sur cet espace réservé, voir [comment utiliser le stockage blob Azure avec le Kit de développement WebJobs](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md), 

Lorsque l’attribut décoration un `Stream` objet, un autre constructeur paramètre spécifie la `FileAccess` en mode lecture, écriture ou en lecture/écriture. 

L’exemple suivant utilise un `CloudBlockBlob` objet à supprimer un blob. Le message file d’attente est le nom de l’objet blob.

        public static void DeleteBlob(
            [QueueTrigger("deleteblobqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
        {
            blobToDelete.Delete();
        }

### <a id="pocoblobs"></a>POCO [(simple objet CLR ancien](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) messages en file d’attente

Pour un POCO stocké en tant que JSON dans le message file d’attente, vous pouvez utiliser des espaces réservés que nom des propriétés de l’objet dans le `Queue` l’attribut `blobPath` paramètre. Vous pouvez également utiliser les [noms de propriété de métadonnées files d’attente](#queuemetadata) comme espaces réservés. 

L’exemple suivant copie un blob dans un nouveau blob avec une extension différente. Le message file d’attente est un `BlobInformation` objet qui inclut `BlobName` et `BlobNameWithoutExtension` propriétés. Les noms de propriété sont utilisés comme espaces réservés dans le chemin d’accès de blob pour la `Blob` attributs. 
 
        public static void CopyBlobPOCO(
            [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
            [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
        {
            blobInput.CopyTo(blobOutput, 4096);
        }

Le Kit de développement utilise le [package Newtonsoft.Json NuGet](http://www.nuget.org/packages/Newtonsoft.Json) pour sérialiser et désérialiser les messages. Si vous créez des messages file d’attente dans un programme qui n’utilise pas le WebJobs SDK, vous pouvez écrire du code à l’exemple suivant pour créer un message de file d’attente POCO le Kit de développement peut analyser.

        BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
        var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
        logQueue.AddMessage(queueMessage);

Si vous devez effectuer certaines tâches dans votre fonction avant de le lier un blob à un objet, vous pouvez utiliser l’attribut dans le corps de la fonction, [comme indiqué précédemment pour l’attribut file d’attente](#ibinder).

### <a id="blobattributetypes"></a>Vous pouvez utiliser l’attribut Blob avec les types
 
La `Blob` attribut peut être utilisé avec les types suivants :

* `Stream`(lecture ou écriture, spécifié en utilisant le paramètre de constructeur FileAccess)
* `TextReader`
* `TextWriter`
* `string`(lecture)
* `out string`(écrire ; crée un blob uniquement si le paramètre de chaîne n’est pas null lorsque la fonction retourne une valeur)
* POCO (lecture)
* absence POCO (écrire ; toujours crée un blob, crée en tant qu’objet null si POCO paramètre est null lorsque la fonction retourne une valeur)
* `CloudBlobStream`(écriture)
* `ICloudBlob`(lecture ou écriture)
* `CloudBlockBlob`(lecture ou écriture) 
* `CloudPageBlob`(lecture ou écriture) 

## <a id="poison"></a>Comment gérer les messages poison

Messages dont le contenu permet à une fonction échec sont appelés *messages poison*. En cas d’échec de la fonction, le message file d’attente n’est pas supprimé et finalement est sélectionné, à l’origine du cycle à répéter. Le Kit de développement peut interrompre automatiquement le cycle d’après un nombre limité d’itérations, ou vous pouvez le faire manuellement.

### <a name="automatic-poison-message-handling"></a>Gestion des messages poison automatique

Le Kit de développement appelle une fonction jusqu'à 5 fois pour traiter un message file d’attente. Si la cinquième tentative échoue, le message est déplacé vers une file d’attente poison. [Le nombre maximal de nouvelles tentatives est configurable](#config). 

La file d’attente poison nommé *{originalqueuename}*-poison. Vous pouvez écrire une fonction pour traiter les messages à partir de la file d’attente poison en vous connectant les ou envoyer une notification que l’attention manuelle est nécessaire. 

Dans l’exemple suivant le `CopyBlob` fonction échoue lorsqu’un message file d’attente contient le nom d’un objet blob qui n’existe pas. Dans ce cas, le message est déplacé vers la file d’attente copyblobqueue poison à partir de la file d’attente copyblobqueue. La `ProcessPoisonMessage` puis consigne le message poison.

        public static void CopyBlob(
            [QueueTrigger("copyblobqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput)
        {
            blobInput.CopyTo(blobOutput, 4096);
        }
        
        public static void ProcessPoisonMessage(
            [QueueTrigger("copyblobqueue-poison")] string blobName, TextWriter logger)
        {
            logger.WriteLine("Failed to copy blob, name=" + blobName);
        }

L’illustration suivante montre la sortie de la console à partir de ces fonctions lorsqu’un message poison est traité.

![Sortie de la console de gestion des messages incohérents](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/poison.png)

### <a name="manual-poison-message-handling"></a>Gestion des messages poison manuel

Vous pouvez obtenir le nombre de fois un message a été sélectionné pour le traitement en ajoutant une `int` paramètre nommé `dequeueCount` à votre fonction. Vous pouvez vérifier le nombre de dequeue dans le code de la fonction et effectuer votre propre poison gestion des messages lorsque le nombre dépasse un seuil, comme le montre l’exemple suivant.

        public static void CopyBlob(
            [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
            [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
            TextWriter logger)
        {
            if (dequeueCount > 3)
            {
                logger.WriteLine("Failed to copy blob, name=" + blobName);
            }
            else
            {
            blobInput.CopyTo(blobOutput, 4096);
            }
        }

## <a id="config"></a>Comment définir les options de configuration

Vous pouvez utiliser la `JobHostConfiguration` type pour définir les options de configuration suivantes :

* Définir les chaînes de connexion SDK dans le code.
* Configurer `QueueTrigger` paramètres tels que le nombre maximal retirer le nombre.
* Obtenir les noms de file d’attente de configuration.

### <a id="setconnstr"></a>Définir des chaînes de connexion SDK dans le code

Définissant les chaînes de connexion SDK dans le code vous permet d’utiliser vos propres noms de chaîne de connexion dans les fichiers de configuration ou de variables d’environnement, comme le montre l’exemple suivant.

        static void Main(string[] args)
        {
            var _storageConn = ConfigurationManager
                .ConnectionStrings["MyStorageConnection"].ConnectionString;
        
            var _dashboardConn = ConfigurationManager
                .ConnectionStrings["MyDashboardConnection"].ConnectionString;
        
            var _serviceBusConn = ConfigurationManager
                .ConnectionStrings["MyServiceBusConnection"].ConnectionString;
        
            JobHostConfiguration config = new JobHostConfiguration();
            config.StorageConnectionString = _storageConn;
            config.DashboardConnectionString = _dashboardConn;
            config.ServiceBusConnectionString = _serviceBusConn;
            JobHost host = new JobHost(config);
            host.RunAndBlock();
        }

### <a id="configqueue"></a>Configurer les paramètres de QueueTrigger

Vous pouvez configurer les paramètres suivants s’appliquent au traitement file d’attente message :

- Le nombre maximal de file d’attente les messages qui sont pris en charge simultanément à exécuter en parallèle (valeur par défaut est 16).
- Le nombre maximal de nouvelles tentatives avant l’envoi d’un message file d’attente à une file d’attente poison (valeur par défaut est 5).
- La valeur maximale délai avant d’interroger à nouveau lorsqu’une file d’attente est vide (valeur par défaut est une minute).

L’exemple suivant montre comment configurer ces paramètres :

        static void Main(string[] args)
        {
            JobHostConfiguration config = new JobHostConfiguration();
            config.Queues.BatchSize = 8;
            config.Queues.MaxDequeueCount = 4;
            config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
            JobHost host = new JobHost(config);
            host.RunAndBlock();
        }

### <a id="setnamesincode"></a>Définir les valeurs pour WebJobs SDK paramètres constructeur dans le code

Parfois vous souhaitez spécifier un nom de file d’attente, un nom blob ou un conteneur ou un tableau nommez-la dans code plutôt que de coder en dur. Par exemple, vous souhaiterez peut-être spécifier le nom de la file d’attente de `QueueTrigger` dans une variable d’environnement ou fichier de configuration. 

Vous pouvez le faire en passant un `NameResolver` l’objet vers le `JobHostConfiguration` type. Inclure des espaces réservés spéciales délimitées par des signes de pourcentage (%) dans les paramètres de constructeur WebJobs SDK attribut et votre `NameResolver` code spécifie les valeurs réelles devant être utilisé à la place de ces espaces réservés.

Par exemple, supposons que vous souhaitez utiliser une file d’attente nommé logqueuetest dans l’environnement de test et une logqueueprod nommée en production. Au lieu d’un nom codé en dur la file d’attente, vous souhaitez spécifier le nom d’une entrée dans la `appSettings` collection que c’est le nom de la file d’attente réelle. Si la `appSettings` clé est logqueue, votre fonction peut ressembler à l’exemple suivant.

        public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
        {
            Console.WriteLine(logMessage);
        }

Votre `NameResolver` classe deviendrait puis le nom de la file d’attente de `appSettings` comme le montre l’exemple suivant :

        public class QueueNameResolver : INameResolver
        {
            public string Resolve(string name)
            {
                return ConfigurationManager.AppSettings[name].ToString();
            }
        }

Vous passez le `NameResolver` cours dans la `JobHost` objet comme illustré dans l’exemple suivant.

        static void Main(string[] args)
        {
            JobHostConfiguration config = new JobHostConfiguration();
            config.NameResolver = new QueueNameResolver();
            JobHost host = new JobHost(config);
            host.RunAndBlock();
        }
 
**Remarque :** File d’attente, table et les noms d’objets blob sont résolus chaque fois qu’une fonction est appelée, mais blob conteneur noms sont résolus uniquement lorsque l’application démarre. Vous ne pouvez pas modifier le nom du conteneur blob pendant l’exécution de la tâche. 

## <a id="manual"></a>Comment faire pour déclencher manuellement une fonction

Pour déclencher manuellement une fonction, utilisez la `Call` ou `CallAsync` méthode sur le `JobHost` objet et le `NoAutomaticTrigger` attribut sur la fonction, comme le montre l’exemple suivant. 

        public class Program
        {
            static void Main(string[] args)
            {
                JobHost host = new JobHost();
                host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
            }
        
            [NoAutomaticTrigger]
            public static void CreateQueueMessage(
                TextWriter logger, 
                string value, 
                [Queue("outputqueue")] out string message)
            {
                message = value;
                logger.WriteLine("Creating queue message: ", message);
            }
        }

## <a id="logs"></a>Comment écrire des journaux

Le tableau de bord affiche les journaux à deux endroits : la page de la WebJob et la page pour un appel WebJob particulier. 

![Ouvre une page WebJob](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

![Ouvre une page d’appel de fonction](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

Sortie des méthodes de Console que vous appelez dans une fonction ou dans le `Main()` méthode s’affiche dans la page tableau de bord pour la WebJob, et non dans la page à un appel particulier. Sortie de l’objet TextWriter que vous obtenez à partir d’un paramètre dans votre signature de méthode apparaît dans la page tableau de bord à un appel.

Sortie de la console ne peut pas être liée à un appel de méthode particulier, car la Console est thread unique, tandis que de nombreuses fonctions peuvent être en cours d’exécution en même temps. C’est pourquoi le Kit de développement fournit chaque appel de la fonction avec son propre objet writer journal unique.

Pour écrire les [journaux de suivi d’application](web-sites-dotnet-troubleshoot-visual-studio.md#logsoverview), utilisez `Console.Out` (crée des journaux marquées comme INFO) et `Console.Error` (crée des journaux marquées comme erreur). Une alternative consiste à utiliser [Trace ou TraceSource](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx), qui fournit des niveaux de commentaires, d’avertissement et critique en plus des informations et d’erreur. Journaux de suivi application apparaissent dans les fichiers journaux application web, tables Azure, ou objets BLOB Azure selon la configuration de votre application web Azure. Comme c’est vrai pour toute la sortie Console, les journaux des 100 applications dernières apparaissent également dans la page tableau de bord pour la WebJob, et non dans la page pour un appel de fonction. 

Sortie de la console s’affiche dans le tableau de bord uniquement si le programme est en cours d’exécution dans un WebJob Azure, pas si le programme s’exécute localement ou tout autre environnement.

Désactiver la journalisation de tableau de bord des scénarios haut débit. Par défaut, le Kit de développement écrit des journaux dans l’espace de stockage, et cette activité pourrait dégrader les performances lorsque vous traitez un grand nombre de messages. Pour désactiver la journalisation, définissez la chaîne de connexion du tableau de bord à la valeur null comme le montre l’exemple suivant.

        JobHostConfiguration config = new JobHostConfiguration();       
        config.DashboardConnectionString = "";        
        JobHost host = new JobHost(config);
        host.RunAndBlock();

L’exemple suivant affiche plusieurs méthodes pour écrire des journaux :

        public static void WriteLog(
            [QueueTrigger("logqueue")] string logMessage,
            TextWriter logger)
        {
            Console.WriteLine("Console.Write - " + logMessage);
            Console.Out.WriteLine("Console.Out - " + logMessage);
            Console.Error.WriteLine("Console.Error - " + logMessage);
            logger.WriteLine("TextWriter - " + logMessage);
        }

Tableau de bord SDK WebJobs, le résultat de la `TextWriter` objet s’affiche lorsque vous accédez à la page pour des options fonctionnent appel et cliquez sur **Activer/désactiver sortie**:

![Cliquez sur le lien d’appel de fonction](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardinvocations.png)

![Ouvre une page d’appel de fonction](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

Dans le tableau de bord SDK WebJobs, les 100 dernières lignes de Console représentation afficher vers le haut lorsque vous accédez à la page pour la WebJob (et non pour l’appel de la fonction) et cliquez sur **Activer/désactiver sortie**.
 
![Cliquez sur Activer/désactiver sortie](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

Dans une WebJob continue, journaux des applications s’affichent dans/données/travaux/continue /*{webjobname}*/job_log.txt dans le système de fichiers d’application web.

        [09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
        [09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
        [09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

Dans un Azure blob l’application journaux doit ressembler à cela : 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Bonjour !, 2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Bonjour !, 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Bonjour !,

Et dans un tableau Azure la `Console.Out` et `Console.Error` journaux ressemblent à ceci :

![Infos de table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableinfo.png)

![Journal des erreurs de table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableerror.png)

Si vous voulez Branchez votre propre journal, voir [Cet exemple](http://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/MiscOperations/Program.cs).

## <a id="errors"></a>Comment gérer les erreurs et configurer des délais d’expiration

Le SDK WebJobs inclut également un [délai d’expiration](http://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/MiscOperations/Functions.cs) attribut que vous pouvez utiliser pour générer une fonction pour être facturés si ne se termine dans un laps de temps. Et si vous souhaitez déclencher une alerte lorsque trop d’erreurs se produire au sein d’un certain temps, vous pouvez utiliser la `ErrorTrigger` attribut. Voici un [exemple de ErrorTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Error-Monitoring).

```
public static void ErrorMonitor(
[ErrorTrigger("00:01:00", 1)] TraceFilter filter, TextWriter log,
[SendGrid(
    To = "admin@emailaddress.com",
    Subject = "Error!")]
 SendGridMessage message)
{
    // log last 5 detailed errors to the Dashboard
   log.WriteLine(filter.GetDetailedMessage(5));
   message.Text = filter.GetDetailedMessage(1);
}
```

Vous pouvez également dynamiquement désactiver et activer les fonctions au contrôle si elles peuvent être déclenchées, à l’aide d’un commutateur de configuration susceptibles d’entraîner un paramètre d’application ou le nom de la variable. Pour plus d’exemples de code, consultez la `Disable` attribut dans [le référentiel exemples SDK WebJobs](https://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/MiscOperations/Functions.cs).

## <a id="nextsteps"></a>Étapes suivantes

Ce guide a fourni des exemples de code qui montrent comment gérer des scénarios courants pour l’utilisation de files d’attente Azure. Pour plus d’informations sur l’utilisation d’Azure WebJobs et WebJobs SDK, voir [Azure WebJobs recommandé de ressources](http://go.microsoft.com/fwlink/?linkid=390226).
 
