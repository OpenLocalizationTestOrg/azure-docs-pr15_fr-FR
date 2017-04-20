<properties
    pageTitle="Prise en main blob storage et Visual Studio connectés services (ASP.NET 5) | Microsoft Azure"
    description="Comment procéder à l’aide de stockage d’objets Blob Azure dans un projet Visual Studio ASP.NET 5 après avoir créé un compte de stockage à l’aide de Visual Studio services connectés"
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

# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-5"></a>Prise en main Azure Blob storage et Visual Studio connectés services (ASP.NET 5)

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

##<a name="overview"></a>Vue d’ensemble

Cet article décrit comment procéder à l’aide de stockage d’objets Blob Azure dans Visual Studio une fois que vous avez créé ou référencé un compte de stockage Azure dans un projet ASP.NET 5 à l’aide de la boîte de dialogue Visual Studio ajouter Services connectés.

Stockage d’objets Blob Azure est un service de stockage de grandes quantités de données non structurées qui sont accessibles à partir de n’importe où dans le monde via HTTP ou HTTPS. Un seul blob peut être de toute taille. Objets BLOB peuvent être des éléments tels que des images, des fichiers audio et vidéo, des données brutes et des fichiers de document. Cet article décrit comment se familiariser avec le stockage blob après avoir créé un compte de stockage Azure à l’aide de la boîte de dialogue Visual Studio **Ajouter les Services connectés** dans un projet ASP.NET 5.

Comme fichiers résident dans des dossiers, des objets BLOB storage live dans des conteneurs. Après avoir créé un espace de stockage, vous créez un ou plusieurs conteneurs dans le stockage. Par exemple, dans un espace de stockage appelée « Album », vous pouvez créer des conteneurs dans le stockage appelé « images » pour stocker des images et autre nommé « audio » pour stocker des fichiers audio. Après avoir créé les conteneurs, vous pouvez télécharger des fichiers individuels blob leur. Pour plus d’informations sur manipuler des objets BLOB, voir [prise en main stockage d’objets Blob Azure à l’aide de .NET](storage-dotnet-how-to-use-blobs.md) .

##<a name="access-blob-containers-in-code"></a>Conteneurs d’objets blob Access dans le code

Pour accéder à des objets BLOB dans les projets ASP.NET 5 par programme, vous devez ajouter les éléments suivants, si elles ne sont pas déjà présentes.

1. Ajoutez les déclarations d’espace de noms de code suivantes à la partie supérieure de n’importe quel fichier c# dans lequel vous souhaitez accéder par programme stockage Azure.

        using Microsoft.Extensions.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Extensions.Logging.LogLevel;

2. Obtenir un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Utilisez le code suivant pour obtenir la votre chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

    **Remarque :** Utiliser tout le code ci-dessus devant le code dans les sections suivantes.


3. Utiliser un objet **CloudBlobClient** pour obtenir une référence **CloudBlobContainer** à un conteneur existant dans votre compte de stockage.

        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Get a reference to a container named “mycontainer.”
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");



##<a name="create-a-container-in-code"></a>Créer un conteneur dans le code

Vous pouvez également utiliser la **CloudBlobClient** pour créer un conteneur dans votre compte de stockage. Il vous souhaitez consiste à ajouter un appel à **CreateIfNotExistsAsync** comme dans le code suivant :

    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named “my-new-container.”
    CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();


**Remarque :** Les API effectuer des appels vers le stockage Azure dans ASP.NET 5 sont asynchrones. Pour plus d’informations, voir [la programmation asynchrone avec asynchrone et Await](http://msdn.microsoft.com/library/hh191443.aspx) . Le code ci-dessous suppose asynchrone des méthodes de programmation sont utilisés.

Pour rendre les fichiers au sein du conteneur pour tout le monde, vous pouvez définir le conteneur comme étant public en utilisant le code suivant.

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });

##<a name="upload-a-blob-into-a-container"></a>Télécharger un blob dans un conteneur

Pour télécharger un fichier blob dans un conteneur, obtenir une référence de conteneur et utilisez-le pour obtenir une référence blob. Une fois que vous avez une référence blob, vous pouvez télécharger les flux de données, en appelant la méthode **UploadFromStreamAsync** . Cette opération crée le blob si ce n’est déjà fait, ou le remplace s’il existe. L’exemple suivant montre comment télécharger un blob dans un conteneur et suppose que le conteneur a été déjà créé.

    // Get a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with the contents of a local file
    // named “myfile”.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        await blockBlob.UploadFromStreamAsync(fileStream);
    }

##<a name="list-the-blobs-in-a-container"></a>Répertorier les objets BLOB dans un conteneur
Pour répertorier les objets BLOB dans un conteneur, commencez par obtenir une référence de conteneur. Vous pouvez ensuite appeler la méthode de **ListBlobsSegmentedAsync** du conteneur pour récupérer les objets BLOB et/ou les répertoires qu’il contient. Pour accéder à l’ensemble complet de propriétés et les méthodes pour un retourné **IListBlobItem**, vous devez effectuer un cast à un objet **CloudBlockBlob**, **CloudPageBlob**ou **CloudBlobDirectory** . Si vous ne connaissez pas le type de blob, vous pouvez utiliser un contrôle de type pour lesquelles vous pouvez effectuer un cast en déterminer. Le code suivant montre comment récupérer et l’URI de chaque élément dans un conteneur de sortie.

    BlobContinuationToken token = null;
    do
    {
        BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(token);
        token = resultSegment.ContinuationToken;

        foreach (IListBlobItem item in resultSegment.Results)
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
    } while (token != null);

Il existe d’autres façons d’afficher le contenu d’un conteneur blob. Pour plus d’informations, voir [prise en main stockage d’objets Blob Azure à l’aide de .NET](storage-dotnet-how-to-use-blobs.md#list-the-blobs-in-a-container) .

##<a name="download-a-blob"></a>Télécharger un blob
Pour télécharger un blob, tout d’abord obtenir une référence à l’objet blob, puis appelez la méthode **DownloadToStreamAsync** . L’exemple suivant utilise la méthode **DownloadToStreamAsync** pour transférer le contenu d’objets blob à un objet de flux de données que vous pouvez ensuite enregistrer comme un fichier local.

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save the blob contents to a file named “myfile”.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        await blockBlob.DownloadToStreamAsync(fileStream);
    }

Il existe d’autres façons d’enregistrer des objets BLOB en tant que fichiers. Pour plus d’informations, voir [prise en main stockage d’objets Blob Azure à l’aide de .NET](storage-dotnet-how-to-use-blobs.md#download-blobs) .

##<a name="delete-a-blob"></a>Supprimer un blob
Pour supprimer un blob, tout d’abord obtenir une référence à l’objet blob, puis appelez la méthode **DeleteAsync** dessus.

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    await blockBlob.DeleteAsync();

## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
