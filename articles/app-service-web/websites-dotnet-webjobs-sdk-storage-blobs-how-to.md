<properties 
    pageTitle="L’utilisation de stockage d’objets blob Azure avec WebJobs SDK" 
    description="Apprenez à utiliser le stockage blob Azure avec WebJobs SDK. Déclencher la lecture d’un processus lorsqu’un nouveau blob apparaît dans un conteneur et gérer « BLOB poison »." 
    services="app-service\web, storage" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="06/01/2016" 
    ms.author="tdykstra"/>

# <a name="how-to-use-azure-blob-storage-with-the-webjobs-sdk"></a>L’utilisation de stockage d’objets blob Azure avec WebJobs SDK

## <a name="overview"></a>Vue d’ensemble

Ce guide fournit des exemples de code c# qui montrent comment déclencher un processus lorsqu’un blob Azure est créé ou mis à jour. Les exemples de code utilisent [WebJobs SDK](websites-dotnet-webjobs-sdk.md) version 1.x.

Exemples de code qui montrent comment créer des objets BLOB, voir [comment utiliser stockage Azure file d’attente avec le Kit de développement WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 
        
Part du principe que vous savez [comment créer un projet WebJob dans Visual Studio avec les chaînes de connexion qui pointent vers votre compte de stockage](websites-dotnet-webjobs-sdk-get-started.md) ou entre [plusieurs comptes de stockage](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/MultipleStorageAccountsEndToEndTests.cs).

## <a id="trigger"></a>Comment faire pour déclencher la lecture d’une fonction lorsqu’un blob est créé ou mis à jour

Cette section montre comment utiliser le `BlobTrigger` attribut. 

> [AZURE.NOTE] Le SDK WebJobs analyse les fichiers journaux pour surveiller les objets BLOB nouveaux ou modifiés. Ce processus n’est pas en temps réel ; une fonction longues ne pouvant pas être déclenchée jusqu'à ce que quelques minutes ou plus après avoir créé le blob. En outre, base de [stockage journaux créés sur un « efforts meilleures »](https://msdn.microsoft.com/library/azure/hh343262.aspx) ; Il n’existe aucune garantie que tous les événements seront capturées. Dans certaines conditions, les journaux peuvent être manqués. Si les limites de vitesse et la fiabilité de déclencheurs blob ne sont pas acceptables pour votre application, la méthode recommandée consiste à créer un message file d’attente lorsque vous créez le blob et utilisez l’attribut [QueueTrigger](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#trigger) au lieu de la `BlobTrigger` attribut sur la fonction qui traite le blob.

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

## <a id="types"></a>Types de que vous pouvez lier à des objets BLOB

Vous pouvez utiliser la `BlobTrigger` attribut sur les types suivants :

* `string`
* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `IEnumerable<CloudBlockBlob>`
* `IEnumerable<CloudPageBlob>`
* Autres types désérialisés par [ICloudBlobStreamBinder](#icbsb) 

Si vous voulez travailler directement avec le compte de stockage Azure, vous pouvez également ajouter un `CloudStorageAccount` paramètre à la signature de méthode.

Pour obtenir des exemples, voir le [code de liaison de blob dans le référentiel azure-webjobs-sdk sur GitHub.com](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/BlobBindingEndToEndTests.cs).

## <a id="string"></a>Mise en route du contenu texte blob par la liaison de chaîne

Si vous prévoyez des objets BLOB de texte, `BlobTrigger` peuvent être appliqués à une `string` paramètre. L’exemple de code suivant lie un blob de texte dans un `string` paramètre nommé `logMessage`. La fonction utilise ce paramètre pour écrire le contenu de l’objet blob dans le tableau de bord WebJobs SDK. 
 
        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name, 
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a id="icbsb"></a>Prise sérialisé blob du contenu à l’aide de ICloudBlobStreamBinder

L’exemple de code suivant utilise une classe qui mettent en œuvre, `ICloudBlobStreamBinder` pour activer la `BlobTrigger` attribut pour lier un objet blob à la `WebImage` type.

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

La `WebImage` code de liaison est fourni dans un `WebImageBinder` classe dérivée de `ICloudBlobStreamBinder`.

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

## <a name="getting-the-blob-path-for-the-triggering-blob"></a>Obtenir le chemin d’accès blob pour le déclenchement blob

Pour obtenir le nom du conteneur et le nom d’objets blob du blob qui a déclenché la fonction, incluez un `blobTrigger` paramètre dans la signature de la fonction de chaîne.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            string blobTrigger,
            TextWriter logger)
        {
             logger.WriteLine("Full blob path: {0}", blobTrigger);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }


## <a id="poison"></a>Comment gérer les objets BLOB poison

Lorsqu’un `BlobTrigger` Échec de la fonction, le Kit de développement appelle à nouveau, au cas où l’échec a été provoqué par une erreur temporaire. Si l’erreur est provoquée par le contenu de l’objet blob, la fonction échoue chaque fois qu’il tente de traiter le blob. Par défaut, le Kit de développement appelle une fonction jusqu'à 5 fois pour un blob donné. Si la cinquième tentative échoue, le Kit de développement ajoute un message à une file d’attente nommée *webjobs-blobtrigger-poison*.

Le nombre maximal de nouvelles tentatives est configurable. Le même paramètre [MaxDequeueCount](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#configqueue) est utilisé pour la gestion des objets blob poison et gestion des messages poison file d’attente. 

Le message file d’attente pour les objets BLOB poison est un objet JSON qui contient les propriétés suivantes :

* FunctionId (dans le format *{nom WebJob}*. Fonctions. *{Nom de la fonction}*, par exemple : WebJob1.Functions.CopyBlob)
* BlobType (« BlockBlob » ou « PageBlob »)
* ContainerName
* BlobName
* ETag (un identificateur de version blob, par exemple : « 0x8D1DC6E70A277EF »)

Dans l’exemple suivant, la `CopyBlob` fonction possède un code qui provoque l’échec chaque fois qu’elle est appelée. Une fois que le Kit de développement les appels pour le nombre maximal de nouvelles tentatives, un message est créé dans la file d’attente blob poison et ce message est traité par le `LogPoisonBlob` fonction. 

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

Le Kit de développement désérialise automatiquement le message JSON. Voici le `PoisonBlobMessage` cours : 

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a id="polling"></a>Algorithme de sondage BLOB

Le SDK WebJobs analyse tous les conteneurs spécifiés par `BlobTrigger` attributs au démarrage de l’application. Dans un compte de stockage grande cette analyse peut prendre du temps, il peut être un certain temps avant de nouveaux objets BLOB sont trouvées et `BlobTrigger` fonctions sont exécutées.

Pour détecter les objets BLOB nouvelles ou modifiées après le démarrage de l’application, le Kit de développement lit régulièrement les journaux de stockage blob. Les journaux d’objets blob sont mis en mémoire tampon et uniquement obtenir écrite physiquement 10 minutes ou, si bien qu’il peut être retard important après un blob est créé ou mis à jour avant le correspondant `BlobTrigger` fonction s’exécute. 

Il existe une exception pour les objets BLOB que vous créez à l’aide de la `Blob` attribut. Lorsque le SDK WebJobs crée un nouveau blob, il passe le nouveau blob immédiatement à n’importe quel correspondant à `BlobTrigger` fonctions. Par conséquent, si vous avez une chaîne d’objets blob entrées et les sorties, le Kit de développement peut les traiter efficacement. Mais si vous voulez exécuter votre blob les fonctions de traitement des objets BLOB qui sont créés ou mis à jour par d’autres moyens de faible latence, nous vous recommandons d’utiliser `QueueTrigger` plutôt que `BlobTrigger`.

### <a id="receipts"></a>Confirmations de BLOB

Le SDK WebJobs permet de s’assurer qu’aucun `BlobTrigger` fonction est appelée plusieurs fois pour le même blob nouveau ou mis à jour. Pour cela, la conservation de *confirmations blob* afin de déterminer si une version blob donné a été traitée.

Confirmations de BLOB sont stockées dans un conteneur nommé *azure-webjobs-hôtes* dans le compte de stockage Azure spécifié par la chaîne de connexion AzureWebJobsStorage. Un accusé de réception blob comporte les informations suivantes :

* La fonction qui a été appelée le blob («*{nom WebJob}*. Fonctions. *{Nom de la fonction}*», par exemple : « WebJob1.Functions.CopyBlob »)
* Le nom du conteneur
* Le type de blob (« BlockBlob » ou « PageBlob »)
* Le nom d’objets blob
* L’ETag (un identificateur de version blob, par exemple : « 0x8D1DC6E70A277EF »)

Si vous voulez forcer nouveau traitement d’un objet blob, vous pouvez supprimer manuellement la réception d’objets blob pour ce blob à partir du conteneur *azure-webjobs-hosts* .

## <a id="queues"></a>Rubriques connexes couverts par l’article files d’attente

Pour plus d’informations sur la façon de traiter les traitement blob déclenché par un message file d’attente, ou pour WebJobs SDK scénarios non spécifiques aux blob de traitement, voir [comment utiliser stockage Azure file d’attente avec le Kit de développement WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 

Rubriques connexes décrites dans cet article sont les suivants :

* Fonctions asynchrones
* Plusieurs instances
* Arrêt
* Utiliser les attributs WebJobs SDK dans le corps d’une fonction
* Définir les chaînes de connexion SDK dans le code.
* Définir les valeurs pour WebJobs SDK paramètres constructeur dans le code
* Configurer `MaxDequeueCount` pour la gestion des objets blob poison.
* Déclencher manuellement une fonction
* Écrire des journaux

## <a id="nextsteps"></a>Étapes suivantes

Ce guide a fourni des exemples de code qui montrent comment gérer des scénarios courants permettant de manipuler des objets BLOB Azure. Pour plus d’informations sur l’utilisation d’Azure WebJobs et WebJobs SDK, voir [Azure WebJobs recommandé de ressources](http://go.microsoft.com/fwlink/?linkid=390226).
 
