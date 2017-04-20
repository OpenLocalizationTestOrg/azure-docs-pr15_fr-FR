<properties
    pageTitle="Prise en main blob storage et Visual Studio connectés services (WebJob projets) | Microsoft Azure"
    description="Comment procéder à l’aide de stockage d’objets Blob dans un projet WebJob après vous être connecté à un espace de stockage Azure à l’aide de Visual Studio services connectés."
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

# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-webjob-projects"></a>Prise en main Azure Blob storage et Visual Studio connectés services (WebJob projets)

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Vue d’ensemble

Cet article fournit des exemples de code c# qui montrent comment déclencher un processus lorsqu’un blob Azure est créé ou mis à jour. Les exemples de code utilisent la version [SDK WebJobs](../app-service-web/websites-dotnet-webjobs-sdk.md) 1.x. Lorsque vous ajoutez un compte de stockage à un projet WebJob à l’aide de la boîte de dialogue Visual Studio **Ajouter les Services connectés** , le package NuGet de stockage Azure approprié est installé, les références .NET appropriées sont ajoutés au projet et chaînes de connexion pour le compte de stockage sont mis à jour dans le fichier App.config.



## <a name="how-to-trigger-a-function-when-a-blob-is-created-or-updated"></a>Comment faire pour déclencher la lecture d’une fonction lorsqu’un blob est créé ou mis à jour

Cette section montre comment utiliser l’attribut **BlobTrigger** .

 **Remarque :** Le SDK WebJobs analyse les fichiers journaux pour surveiller les objets BLOB nouveaux ou modifiés. Ce processus est lent ; une fonction longues ne pouvant pas être déclenchée jusqu'à ce que quelques minutes ou plus après avoir créé le blob.  Si votre application doit traiter des objets BLOB immédiatement, la méthode recommandée consiste à créer un message file d’attente lorsque vous créez le blob et utilisez l’attribut [QueueTrigger](../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md#trigger) au lieu de l’attribut **BlobTrigger** sur la fonction qui traite le blob.

### <a name="single-placeholder-for-blob-name-with-extension"></a>Espace réservé unique pour nom blob avec l’extension  

L’exemple de code suivant copie des objets BLOB de texte qui s’affichent dans le conteneur *d’entrée* pour le conteneur de *sortie* :

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Le constructeur attribut prend un paramètre de chaîne qui spécifie le nom du conteneur et un espace réservé pour le nom d’objets blob. Dans cet exemple, si un blob appelé *Blob1.txt* est créé dans le conteneur *d’entrée* , la fonction crée un blob appelé *Blob1.txt* dans le conteneur de *sortie* .

Vous pouvez spécifier un modèle de nom avec l’espace réservé de nom blob, comme illustré dans l’exemple suivant :

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Ce code copie uniquement des objets BLOB qui portent des noms commençant par « d’origine- ». Par exemple, *original Blob1.txt* dans le conteneur *d’entrée* est copiée dans la *Copie Blob1.txt* dans le conteneur de *sortie* .

Si vous avez besoin spécifier un modèle de nom pour les noms d’objets blob qui ont des accolades dans le nom, double-cliquez sur les accolades. Par exemple, si vous souhaitez rechercher des objets BLOB dans le conteneur *images* qui portent des noms à ceci :

        {20140101}-soundfile.mp3

Utilisez cette option pour votre modèle :

        images/{{20140101}}-{name}

Dans l’exemple, la valeur *nom* d’espace réservé serait *soundfile.mp3*.

### <a name="separate-blob-name-and-extension-placeholders"></a>Espaces réservés de nom et l’extension blob distinct

L’exemple de code suivant modifie l’extension de fichier pendant la copie des objets BLOB qui s’affichent dans le conteneur *d’entrée* pour le conteneur de *sortie* . Le code enregistre l’extension du blob *d’entrée* et affecte l’extension du blob *sortie* *.txt*.

        public static void CopyBlobToTxtFile([BlobTrigger("input/{name}.{ext}")] TextReader input,
            [Blob("output/{name}.txt")] out string output,
            string name,
            string ext,
            TextWriter logger)
        {
            logger.WriteLine("Blob name:" + name);
            logger.WriteLine("Blob extension:" + ext);
            output = input.ReadToEnd();
        }

## <a name="types-that-you-can-bind-to-blobs"></a>Types de que vous pouvez lier à des objets BLOB

Vous pouvez utiliser l’attribut **BlobTrigger** sur les types suivants :

* **chaîne**
* **TextReader**
* **Flux de données**
* **ICloudBlob**
* **CloudBlockBlob**
* **CloudPageBlob**
* Autres types désérialisés par [ICloudBlobStreamBinder](#getting-serialized-blob-content-by-using-icloudblobstreambinder)

Si vous souhaitez travailler directement avec le compte de stockage Azure, vous pouvez également ajouter un paramètre **CloudStorageAccount** à la signature de méthode.

## <a name="getting-text-blob-content-by-binding-to-string"></a>Mise en route du contenu texte blob par la liaison de chaîne

Si vous prévoyez des objets BLOB de texte, **BlobTrigger** peuvent être appliqués à un paramètre de **chaîne** . L’exemple de code suivant lie un blob de texte à un paramètre de **chaîne** nommé **logMessage**. La fonction utilise ce paramètre pour écrire le contenu de l’objet blob dans le tableau de bord WebJobs SDK.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a name="getting-serialized-blob-content-by-using-icloudblobstreambinder"></a>Prise sérialisé blob du contenu à l’aide de ICloudBlobStreamBinder

L’exemple de code suivant utilise une classe qui mettent en œuvre, **ICloudBlobStreamBinder** pour activer l’attribut **BlobTrigger** lier un blob au type **WebImage** .

        public static void WaterMark(
            [BlobTrigger("images3/{name}")] WebImage input,
            [Blob("images3-watermarked/{name}")] out WebImage output)
        {
            output = input.AddTextWatermark("WebJobs SDK",
                horizontalAlign: "Center", verticalAlign: "Middle",
                fontSize: 48, opacity: 50);
        }
        public static void Resize(
            [BlobTrigger("images3-watermarked/{name}")] WebImage input,
            [Blob("images3-resized/{name}")] out WebImage output)
        {
            var width = 180;
            var height = Convert.ToInt32(input.Height * 180 / input.Width);
            output = input.Resize(width, height);
        }

Le code de liaison **WebImage** est fourni dans une classe **WebImageBinder** qui est dérivée de **ICloudBlobStreamBinder**.

        public class WebImageBinder : ICloudBlobStreamBinder<WebImage>
        {
            public Task<WebImage> ReadFromStreamAsync(Stream input,
                System.Threading.CancellationToken cancellationToken)
            {
                return Task.FromResult<WebImage>(new WebImage(input));
            }
            public Task WriteToStreamAsync(WebImage value, Stream output,
                System.Threading.CancellationToken cancellationToken)
            {
                var bytes = value.GetBytes();
                return output.WriteAsync(bytes, 0, bytes.Length, cancellationToken);
            }
        }

## <a name="how-to-handle-poison-blobs"></a>Comment gérer les objets BLOB poison

Lorsqu’une fonction **BlobTrigger** échoue, le Kit de développement appelle à nouveau, au cas où l’échec a été provoqué par une erreur temporaire. Si l’erreur est provoquée par le contenu de l’objet blob, la fonction échoue chaque fois qu’il tente de traiter le blob. Par défaut, le Kit de développement appelle une fonction jusqu'à 5 fois pour un blob donné. Si la cinquième tentative échoue, le Kit de développement ajoute un message à une file d’attente nommée *webjobs-blobtrigger-poison*.

Le nombre maximal de nouvelles tentatives est configurable. Le même paramètre [MaxDequeueCount](../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md#configqueue) est utilisé pour la gestion des objets blob poison et gestion des messages poison file d’attente.

Le message file d’attente pour les objets BLOB poison est un objet JSON qui contient les propriétés suivantes :

* FunctionId (dans le format *{nom WebJob}*. Fonctions. *{Nom de la fonction}*, par exemple : WebJob1.Functions.CopyBlob)
* BlobType (« BlockBlob » ou « PageBlob »)
* ContainerName
* BlobName
* ETag (un identificateur de version blob, par exemple : « 0x8D1DC6E70A277EF »)

Dans l’exemple suivant, la fonction **CopyBlob** a code qui provoque l’échec chaque fois qu’elle est appelée. Une fois le Kit de développement les appels pour le nombre maximal de nouvelles tentatives, un message est créé dans la file d’attente blob poison, et ce message est traité par la fonction **LogPoisonBlob** .

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("textblobs/output-{name}")] out string output)
        {
            throw new Exception("Exception for testing poison blob handling");
            output = input.ReadToEnd();
        }

        public static void LogPoisonBlob(
        [QueueTrigger("webjobs-blobtrigger-poison")] PoisonBlobMessage message,
            TextWriter logger)
        {
            logger.WriteLine("FunctionId: {0}", message.FunctionId);
            logger.WriteLine("BlobType: {0}", message.BlobType);
            logger.WriteLine("ContainerName: {0}", message.ContainerName);
            logger.WriteLine("BlobName: {0}", message.BlobName);
            logger.WriteLine("ETag: {0}", message.ETag);
        }

Le Kit de développement désérialise automatiquement le message JSON. Voici la classe **PoisonBlobMessage** :

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a name="blob-polling-algorithm"></a>Algorithme de sondage BLOB

Le SDK WebJobs analyse tous les conteneurs spécifiés par attributs **BlobTrigger** au démarrage de l’application. Dans un compte de stockage grande cette analyse peut prendre un certain temps, il peut être un certain temps avant de nouveaux objets BLOB sont trouvées et **BlobTrigger** fonctions sont exécutées.

Pour détecter les objets BLOB nouvelles ou modifiées après le démarrage de l’application, le Kit de développement lit régulièrement les journaux de stockage blob. Les journaux d’objets blob sont mis en mémoire tampon et uniquement obtenir écrite physiquement 10 minutes ou par conséquent, il peut être retard important après qu’un blob est créé ou mis à jour avant le correspondant **BlobTrigger** fonction s’exécute.

Il existe une exception pour les objets BLOB que vous créez à l’aide de l’attribut **Blob** . Lorsque le SDK WebJobs crée un nouveau blob, il passe le nouveau blob immédiatement à toutes les fonctions **BlobTrigger** correspondantes. Par conséquent, si vous avez une chaîne d’objets blob entrées et les sorties, le Kit de développement peut les traiter efficacement. Mais si vous voulez faible latence votre blob fonctions pour les objets BLOB qui sont créés ou mis à jour par d’autres moyens de traitement en cours d’exécution, nous vous recommandons d’utiliser **QueueTrigger** plutôt que **BlobTrigger**.

### <a name="blob-receipts"></a>Confirmations de BLOB

Le SDK WebJobs permet de s’assurer qu’aucune fonction **BlobTrigger** n’est appelée plusieurs fois pour le même blob nouveau ou mis à jour. Pour cela, la conservation de *confirmations blob* afin de déterminer si une version blob donné a été traitée.

Confirmations de BLOB sont stockées dans un conteneur nommé *azure-webjobs-hôtes* dans le compte de stockage Azure spécifié par la chaîne de connexion AzureWebJobsStorage. Un accusé de réception blob comporte les informations suivantes :

* La fonction qui a été appelée le blob («*{nom WebJob}*. Fonctions. *{Nom de la fonction}*», par exemple : « WebJob1.Functions.CopyBlob »)
* Le nom du conteneur
* Le type de blob (« BlockBlob » ou « PageBlob »)
* Le nom d’objets blob
* L’ETag (un identificateur de version blob, par exemple : « 0x8D1DC6E70A277EF »)

Si vous voulez forcer nouveau traitement d’un objet blob, vous pouvez supprimer manuellement la réception d’objets blob pour ce blob à partir du conteneur *azure-webjobs-hosts* .

## <a name="related-topics-covered-by-the-queues-article"></a>Rubriques connexes couverts par l’article files d’attente

Pour plus d’informations sur la façon de traiter les traitement blob déclenché par un message file d’attente, ou pour WebJobs SDK scénarios non spécifiques aux blob de traitement, voir [comment utiliser stockage Azure file d’attente avec le Kit de développement WebJobs](../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md).

Rubriques connexes décrites dans cet article sont les suivants :

* Fonctions asynchrones
* Plusieurs instances
* Arrêt
* Utiliser les attributs WebJobs SDK dans le corps d’une fonction
* Définir les chaînes de connexion SDK dans le code.
* Définir les valeurs pour WebJobs SDK paramètres constructeur dans le code
* Configurer **MaxDequeueCount** pour la gestion des objets blob poison.
* Déclencher manuellement une fonction
* Écrire des journaux

## <a name="next-steps"></a>Étapes suivantes

Cet article fournit des exemples de code qui montrent comment gérer des scénarios courants permettant de manipuler des objets BLOB Azure. Pour plus d’informations sur l’utilisation d’Azure WebJobs et WebJobs SDK, consultez [ressources de documentation WebJobs Azure](http://go.microsoft.com/fwlink/?linkid=390226).
