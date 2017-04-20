<properties
    pageTitle="Prise en main stockage d’objets Blob Azure (stockage d’objets) à l’aide de .NET | Microsoft Azure"
    description="Stocker des données non structurées dans le nuage avec stockage d’objets Blob Azure (stockage d’objets)."
    services="storage"
    documentationCenter=".net"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="get-started-with-azure-blob-storage-using-net"></a>Prise en main stockage d’objets Blob Azure à l’aide de .NET

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Vue d’ensemble

Stockage d’objets Blob Azure est un service qui stocke des données non structurées dans le cloud comme objets/des objets BLOB. Stockage d’objets BLOB peut stocker n’importe quel type de texte ou des données binaires, tel qu’un document, un fichier multimédia ou un programme d’installation d’application. Stockage d’objets BLOB est également appelée stockage d’objets.

### <a name="about-this-tutorial"></a>À propos de ce didacticiel

Ce didacticiel montre comment écrire du code .NET pour certains scénarios courants à l’aide de stockage d’objets Blob Azure. Scénarios couvertes incluent téléchargement, liste, le téléchargement et suppression des objets BLOB.

**Durée estimée d’exécution :** 45 minutes

**Prerequisities :**

- [Microsoft Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx)
- [Bibliothèque de Client de stockage Azure pour .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Gestionnaire de Configuration Azure pour .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
- Un [compte de stockage Azure](storage-create-storage-account.md#create-a-storage-account)


[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>Autres exemples

Pour obtenir des exemples supplémentaires à l’aide de stockage d’objets Blob, voir [Prise en main stockage d’objets Blob Azure dans .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/). Vous pouvez télécharger l’application échantillon et exécutez-le ou recherchez le code de GitHub.


[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### <a name="add-namespace-declarations"></a>Ajouter des déclarations d’espace de noms

Ajoutez le code suivant `using` instructions en haut de la `program.cs` fichier :

    using Microsoft.Azure; // Namespace for CloudConfigurationManager
    using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
    using Microsoft.WindowsAzure.Storage.Blob; // Namespace for Blob storage types

### <a name="parse-the-connection-string"></a>Analyser la chaîne de connexion

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-blob-service-client"></a>Créer le client de service d’objets Blob

La classe **CloudBlobClient** vous permet de récupérer des conteneurs et des objets BLOB stockées dans le stockage Blob. Voici un moyen de créer le client de service :

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

Vous êtes maintenant prêt à écrire du code qui lit les données à partir d’et écrit des données dans le stockage Blob.

## <a name="create-a-container"></a>Créer un conteneur

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Cet exemple montre comment créer un conteneur s’il n’existe pas déjà :

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it doesn't already exist.
    container.CreateIfNotExists();

Par défaut, le nouveau conteneur est privé, ce qui signifie que vous devez spécifier votre clé d’accès de stockage pour télécharger des objets BLOB à partir de ce conteneur. Si vous voulez rendre les fichiers au sein du conteneur pour tout le monde, vous pouvez définir le conteneur public en utilisant le code suivant :

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = BlobContainerPublicAccessType.Blob });

Toute personne sur Internet peut voir objets BLOB dans un conteneur public, mais vous pouvez modifier ou les supprimer uniquement si vous avez touche d’accès rapide compte approprié ou d’une signature d’un accès partagé.

## <a name="upload-a-blob-into-a-container"></a>Télécharger un blob dans un conteneur

Stockage d’objets Blob Azure prend en charge des objets BLOB de page et les objets BLOB bloc.  Dans la plupart des cas, blob bloc est le type recommandé à utiliser.

Pour télécharger un fichier dans un blob bloc, obtenir une référence de conteneur et utilisez-le pour obtenir une référence blob bloc. Une fois que vous avez une référence blob, vous pouvez télécharger les flux de données, en appelant la méthode **UploadFromStream** . Cette opération créera le blob s’il n’a pas été précédemment existe, ou la remplacer s’il existe.

L’exemple suivant montre comment télécharger un blob dans un conteneur et suppose que le conteneur a été déjà créé.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## <a name="list-the-blobs-in-a-container"></a>Répertorier les objets BLOB dans un conteneur

Pour répertorier les objets BLOB dans un conteneur, commencez par obtenir une référence de conteneur. Vous pouvez ensuite utiliser méthode de **ListBlobs** du conteneur pour récupérer les objets BLOB et/ou les répertoires qu’il contient. Pour accéder à l’ensemble complet de propriétés et les méthodes pour un retourné **IListBlobItem**, vous devez effectuer un cast à un objet **CloudBlockBlob**, **CloudPageBlob**ou **CloudBlobDirectory** .  Si le type est inconnu, vous pouvez utiliser un contrôle de type pour lesquelles vous pouvez effectuer un cast en déterminer.  Le code suivant montre comment récupérer et sortir URI de chaque élément de la `photos` conteneur :

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("photos");

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, false))
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;

            Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);

        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob pageBlob = (CloudPageBlob)item;

            Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory directory = (CloudBlobDirectory)item;

            Console.WriteLine("Directory: {0}", directory.Uri);
        }
    }

Comme indiqué ci-dessus, vous pouvez nommer des objets BLOB avec des informations de chemin d’accès dans leur nom. Cela crée une structure de répertoire virtuel que vous pouvez organiser et parcourir comme vous le feriez pour un système de fichiers traditionnel. Notez que la structure du répertoire est virtuelle uniquement les seules ressources disponibles dans le stockage Blob sont des conteneurs et des objets BLOB. Toutefois, la bibliothèque de client de stockage offre un objet **CloudBlobDirectory** pour faire référence à un répertoire virtuel et de simplifier le processus de l’utilisation d’objets BLOB qui sont organisées de cette manière.

Par exemple, considérez le jeu d’objets BLOB bloc dans un conteneur nommé suivant `photos`:

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

Lorsque vous appelez **ListBlobs** sur le conteneur « photos » (comme dans l’exemple ci-dessus), une liste hiérarchique est renvoyée. Il contient des objets **CloudBlobDirectory** et **CloudBlockBlob** , qui représente les répertoires et les objets BLOB dans le conteneur, respectivement. Le résultat obtenu ressemble à :

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Si vous le souhaitez, vous pouvez définir le paramètre **UseFlatBlobListing** de la méthode **ListBlobs** **true**. Dans ce cas, chaque blob dans le conteneur est renvoyée en tant qu’objet **CloudBlockBlob** . L’appel vers **ListBlobs** pour renvoyer une liste à deux dimensions ressemble à ceci :

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

et les résultats ressemblent à ceci :

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


## <a name="download-blobs"></a>Télécharger des objets BLOB

Pour télécharger des objets BLOB, tout d’abord extraire une référence blob, puis appelez la méthode **DownloadToStream** . L’exemple suivant utilise la méthode **DownloadToStream** pour transférer le contenu d’objets blob à un objet de flux de données que vous pouvez conserver puis vers un fichier local.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

Vous pouvez également utiliser la méthode **DownloadToStream** pour télécharger le contenu d’un objet blob comme une chaîne de texte.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"></a>Supprimer des objets BLOB

Pour supprimer un blob, commencez par obtenir une référence blob, puis appelez la méthode **Delete** dessus.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## <a name="list-blobs-in-pages-asynchronously"></a>Liste des objets BLOB dans les pages en mode asynchrone

Si vous mettez en vente un grand nombre d’objets BLOB, ou vous souhaitez contrôler le nombre de résultats que revenir en une seule opération de liste, vous pouvez afficher des objets BLOB dans les pages de résultats. Cet exemple montre comment renvoyer les résultats dans les pages en mode asynchrone, afin que l’exécution n’est pas bloquée pendant l’attente pour renvoyer un grand jeu de résultats.

Cet exemple montre un blob plat répertoriant, mais vous pouvez également effectuer une liste hiérarchique, en définissant la `useFlatBlobListing` paramètre de la méthode **ListBlobsSegmentedAsync** à `false`.

Étant donné que la méthode sample appelle une méthode asynchrone, il doit commencer avec les `async` mot-clé et il doivent retourner un objet de la **tâche** . Le mot clé await spécifié pour la méthode **ListBlobsSegmentedAsync** interrompt l’exécution de l’exemple de méthode jusqu'à la fin de la tâche de la liste.

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        //List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        //When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            //or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
        while (continuationToken != null);
    }

## <a name="writing-to-an-append-blob"></a>Écriture dans un blob Ajout

Un blob ajout est un nouveau type d’objet blob introduite avec la version 5.x de la bibliothèque de client de stockage Azure pour .NET. Un blob ajout est optimisé pour les opérations d’ajout, telles que l’enregistrement. Comme un blob bloc, un blob Ajout se compose de blocs, mais lorsque vous ajoutez un nouveau bloc à un blob Ajout, il est toujours ajouté à la fin de l’objet blob. Vous ne pouvez pas mettre à jour ou supprimer un bloc existant dans un blob Ajout. Le bloc ID pour un blob ajout ne sont pas exposés comme pour un blob bloc.

Chaque bloc dans un blob ajout peut être une autre taille, avec un maximum de 4 Mo, et un blob ajout peut inclure un maximum de 50 000 blocs. La taille maximale d’un blob ajout est donc légèrement plus de 195 Go (4 Mo X 50 000 blocs).

L’exemple suivant crée un nouveau blob Ajout et ajoute des données, il simulation d’une opération de journalisation simple.

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create service client for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("my-append-blobs");

    //Create the container if it does not already exist.
    container.CreateIfNotExists();

    //Get a reference to an append blob.
    CloudAppendBlob appendBlob = container.GetAppendBlobReference("append-blob.log");

    //Create the append blob. Note that if the blob already exists, the CreateOrReplace() method will overwrite it.
    //You can check whether the blob exists to avoid overwriting it by using CloudAppendBlob.Exists().
    appendBlob.CreateOrReplace();

    int numBlocks = 10;

    //Generate an array of random bytes.
    Random rnd = new Random();
    byte[] bytes = new byte[numBlocks];
    rnd.NextBytes(bytes);

    //Simulate a logging operation by writing text data and byte data to the end of the append blob.
    for (int i = 0; i < numBlocks; i++)
    {
        appendBlob.AppendText(String.Format("Timestamp: {0:u} \tLog Entry: {1}{2}",
            DateTime.UtcNow, bytes[i], Environment.NewLine));
    }

    //Read the append blob to the console window.
    Console.WriteLine(appendBlob.DownloadText());

Pour plus d’informations sur les différences entre les trois types d’objets BLOB, voir [présentation bloc BLOB, des objets BLOB de la Page et ajouter des objets BLOB](https://msdn.microsoft.com/library/azure/ee691964.aspx) .

## <a name="managing-security-for-blobs"></a>Gestion de la sécurité pour les objets BLOB

Par défaut, le stockage Azure conserve vos données sécurisées en limitant l’accès au propriétaire du compte qui dispose de touches d’accès rapide compte. Lorsque vous avez besoin de partager des données blob dans votre compte de stockage, il est important de le faire sans nuire à la sécurité de touches d’accès de votre compte. En outre, vous pouvez chiffrer données blob pour vous assurer qu’elle est sûre accédant via le câble et dans le stockage Azure.

[AZURE.INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

### <a name="controlling-access-to-blob-data"></a>Contrôler l’accès aux données blob

Par défaut, les données blob dans votre compte de stockage sont accessibles qu’aux propriétaire du compte de stockage. L’authentification des demandes auprès de stockage d’objets Blob nécessite la touche d’accès compte par défaut. Toutefois, vous pouvez choisir de mettre certaines données blob disponibles à d’autres utilisateurs. Vous avez deux possibilités :

- **L’accès anonyme :** Vous pouvez laisser un conteneur ou ses objets BLOB accessibles pour l’accès anonyme. Pour plus d’informations, voir [Gérer les accès en lecture anonymes à des conteneurs et des objets BLOB](storage-manage-access-to-resources.md) .
- **Shared access signatures :** Vous pouvez fournir des clients avec une signature accès partagé (sa), qui offre un accès délégué à une ressource dans votre compte de stockage, avec des autorisations que vous spécifiez et sur un intervalle que vous spécifiez. Pour plus d’informations, reportez-vous [à l’aide de partagés accès Signatures (sa)](storage-dotnet-shared-access-signature-part-1.md) .

### <a name="encrypting-blob-data"></a>Le chiffrement des données blob

Stockage Azure prend en charge le chiffrement des données blob à la fois au niveau du client et sur le serveur :

- **Chiffrement côté client :** La bibliothèque de Client de stockage pour .NET prend en charge le chiffrement des données dans les applications clientes avant de télécharger vers le stockage Azure et déchiffrer des données pendant leur téléchargement vers le client. La bibliothèque prend également en charge l’intégration avec l’archivage sécurisé de clé Azure pour la gestion de clé de compte de stockage. Pour plus d’informations, consultez [Chiffrement côté Client avec .NET pour le stockage Microsoft Azure](storage-client-side-encryption.md) . Voir également [didacticiel : chiffrer et déchiffrer des objets BLOB Microsoft Azure Storage à l’aide de l’archivage sécurisé de clé Azure](storage-encrypt-decrypt-blobs-key-vault.md).
- **Chiffrement côté serveur**: stockage Azure prend désormais en charge le chiffrement côté serveur. Voir [stockage Azure Service chiffrement des données au repos (Preview)](storage-service-encryption.md).

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris les notions de base de stockage d’objets Blob, suivez ces liens pour en savoir plus.

### <a name="microsoft-azure-storage-explorer"></a>Explorateur de stockage de Microsoft Azure
- [L’Explorateur d’espace de stockage de Microsoft Azure (MASE)](../vs-azure-tools-storage-manage-with-storage-explorer.md) est une application gratuite, autonome de Microsoft qui vous permet d’utiliser visuellement des données de stockage Azure dans Windows, OS X et Linux.

### <a name="blob-storage-samples"></a>Exemples de stockage BLOB

- [Prise en main stockage d’objets Blob Azure dans .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)

### <a name="blob-storage-reference"></a>Référence du stockage BLOB

- [Bibliothèque de Client de stockage pour référence .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
- [Référence de l’API REST](http://msdn.microsoft.com/library/azure/dd179355)

### <a name="conceptual-guides"></a>Guides conceptuelles

- [Transférer des données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md)
- [Prise en main le stockage de fichiers pour .NET](storage-dotnet-how-to-use-files.md)
- [L’utilisation de stockage d’objets blob Azure avec WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)

  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png

  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configuring Connection Strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [REST API reference]: http://msdn.microsoft.com/library/azure/dd179355
