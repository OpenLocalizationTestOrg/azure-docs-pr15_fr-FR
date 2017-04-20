<properties
    pageTitle="Prise en main blob storage et Visual Studio connectés services (services cloud) | Microsoft Azure"
    description="Comment procéder à l’aide de stockage d’objets Blob Azure dans un projet de service cloud dans Visual Studio après vous être connecté à un compte de stockage à l’aide de Visual Studio services connectés"
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

# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Prise en main stockage d’objets Blob Azure et Visual Studio connecté services (cloud services projets)

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Vue d’ensemble

Cet article décrit comment se familiariser avec le stockage Blob Azure une fois que vous avez créé ou référencé un compte de stockage Azure à l’aide de la boîte de dialogue Visual Studio **Ajouter les Services connectés** dans un projet Visual Studio cloud services. Nous allons vous montrer comment récupérer et créer des conteneurs blob et comment effectuer des tâches courantes, tels que téléchargement de liste et le téléchargement des objets BLOB. Les exemples sont écrits en C\# et utilisation de la [Bibliothèque Microsoft Azure stockage Client pour .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Stockage d’objets Blob Azure est un service de stockage de grandes quantités de données non structurées qui sont accessibles à partir de n’importe où dans le monde via HTTP ou HTTPS. Un seul blob peut être de toute taille. Objets BLOB peuvent être des éléments tels que des images, des fichiers audio et vidéo, des données brutes et des fichiers de document.

Comme fichiers résident dans des dossiers, des objets BLOB storage live dans des conteneurs. Après avoir créé un espace de stockage, vous créez un ou plusieurs conteneurs dans le stockage. Par exemple, dans un espace de stockage appelée « Album », vous pouvez créer des conteneurs dans le stockage appelé « images » pour stocker des images et autre nommé « audio » pour stocker des fichiers audio. Après avoir créé les conteneurs, vous pouvez télécharger des fichiers individuels blob leur.

- Pour plus d’informations sur manipuler des objets BLOB, voir [prise en main stockage d’objets Blob Azure à l’aide de .NET](storage-dotnet-how-to-use-blobs.md).
- Pour obtenir des informations générales sur le stockage Azure, consultez la [documentation du stockage](https://azure.microsoft.com/documentation/services/storage/).
- Pour obtenir des informations générales sur les Services en nuage Azure, voir [la documentation de Services de Cloud](https://azure.microsoft.com/documentation/services/cloud-services/).
- Pour plus d’informations sur la programmation d’applications ASP.NET, consultez [ASP.NET](http://www.asp.net).

## <a name="access-blob-containers-in-code"></a>Conteneurs d’objets blob Access dans le code

Pour accéder par programme des objets BLOB dans les projets de service cloud, vous devez ajouter les éléments suivants, si elles ne sont pas déjà présentes.

1. Ajoutez les déclarations d’espace de noms de code suivantes à la partie supérieure de n’importe quel fichier c# dans lequel vous souhaitez accéder par programme stockage Azure.

        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;

2. Obtenir un objet **CloudStorageAccount** qui représente les informations de votre compte de stockage. Utilisez le code suivant pour obtenir la votre chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure.

        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));

3. Obtenir un objet **CloudBlobClient** pour faire référence à un conteneur existant dans votre compte de stockage.

        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

4. Obtenir un objet **CloudBlobContainer** pour faire référence à un conteneur blob spécifique.

        // Get a reference to a container named “mycontainer.”
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

> [AZURE.NOTE] Utiliser tout le code indiqué dans la procédure précédente devant le code indiqué dans les sections suivantes.

## <a name="create-a-container-in-code"></a>Créer un conteneur dans le code

> [AZURE.NOTE] Certaines API qui effectuer des appels en absence au stockage Azure dans ASP.NET sont asynchrone. Pour plus d’informations, voir [la programmation asynchrone avec asynchrone et Await](http://msdn.microsoft.com/library/hh191443.aspx) . Le code dans l’exemple suivant suppose que vous utilisez des méthodes de programmation asynchrone.

Pour créer un conteneur dans votre compte de stockage, il vous souhaitez est d’ajouter un appel à **CreateIfNotExistsAsync** comme dans le code suivant :

    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();


Pour rendre les fichiers au sein du conteneur pour tout le monde, vous pouvez définir le conteneur comme étant public en utilisant le code suivant.

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });


Toute personne sur Internet peut voir objets BLOB dans un conteneur public, mais vous pouvez modifier ou supprimer les uniquement si vous disposez de la clé d’accès appropriés.

## <a name="upload-a-blob-into-a-container"></a>Télécharger un blob dans un conteneur

Stockage Azure prend en charge des objets BLOB de page et les objets BLOB bloc. Dans la plupart des cas, blob bloc est le type recommandé à utiliser.

Pour télécharger un fichier dans un blob bloc, obtenir une référence de conteneur et utilisez-le pour obtenir une référence blob bloc. Une fois que vous avez une référence blob, vous pouvez télécharger les flux de données, en appelant la méthode **UploadFromStream** . Cette opération crée le blob s’il n’existe pas déjà, ou le remplace s’il existe. L’exemple suivant montre comment télécharger un blob dans un conteneur et suppose que le conteneur a été déjà créé.

    // Retrieve a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## <a name="list-the-blobs-in-a-container"></a>Répertorier les objets BLOB dans un conteneur

Pour répertorier les objets BLOB dans un conteneur, commencez par obtenir une référence de conteneur. Vous pouvez ensuite utiliser méthode de **ListBlobs** du conteneur pour récupérer les objets BLOB et/ou les répertoires qu’il contient. Pour accéder à l’ensemble complet de propriétés et les méthodes pour un retourné **IListBlobItem**, vous devez effectuer un cast à un objet **CloudBlockBlob**, **CloudPageBlob**ou **CloudBlobDirectory** . Si le type est inconnu, vous pouvez utiliser un contrôle de type pour lesquelles vous pouvez effectuer un cast en déterminer. Le code suivant montre comment récupérer et sortir URI de chaque élément dans le conteneur de **photos** :

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

Comme le montre l’exemple précédent, le service blob comporte le concept des répertoires au sein de conteneurs, ainsi que. Il s’agit de sorte que vous pouvez organiser vos objets BLOB dans une structure plus sous forme de dossiers. Par exemple, considérez le jeu d’objets BLOB bloc dans un conteneur nommé **photos**suivant :

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

Lorsque vous appelez **ListBlobs** sur le conteneur (comme dans l’exemple précédent), la collection retournée contient des objets **CloudBlobDirectory** et **CloudBlockBlob** représentant les répertoires et les objets BLOB contenues au niveau supérieur. Voici le résultat :

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Si vous le souhaitez, vous pouvez définir le paramètre **UseFlatBlobListing** de la méthode **ListBlobs** **true**. Ainsi, chaque blob renvoyée sous la forme d’un **CloudBlockBlob**, quel que soit répertoire. Voici l’appel vers **ListBlobs**:

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

et Voici le résultat :

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

Pour plus d’informations, voir [CloudBlobContainer.ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx).

## <a name="download-blobs"></a>Télécharger des objets BLOB

Pour télécharger des objets BLOB, tout d’abord extraire une référence blob, puis appelez la méthode **DownloadToStream** . L’exemple suivant utilise la méthode **DownloadToStream** pour transférer le contenu d’objets blob à un objet de flux de données que vous pouvez conserver puis vers un fichier local.

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

Vous pouvez également utiliser la méthode **DownloadToStream** pour télécharger le contenu d’un objet blob comme une chaîne de texte.

    // Get a reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"></a>Supprimer des objets BLOB

Pour supprimer un blob, obtenez d’abord une référence blob, puis appelez la méthode **Delete** .

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## <a name="list-blobs-in-pages-asynchronously"></a>Liste des objets BLOB dans les pages en mode asynchrone

Si vous mettez en vente un grand nombre d’objets BLOB, ou vous souhaitez contrôler le nombre de résultats que revenir en une seule opération de liste, vous pouvez afficher des objets BLOB dans les pages de résultats. Cet exemple montre comment renvoyer les résultats dans les pages en mode asynchrone, afin que l’exécution n’est pas bloquée pendant l’attente pour renvoyer un grand jeu de résultats.

Cet exemple montre un blob plat répertoriant, mais vous pouvez également effectuer une liste hiérarchique, en définissant le paramètre **useFlatBlobListing** de la méthode **ListBlobsSegmentedAsync** sur **false**.

Étant donné que la méthode sample appelle une méthode asynchrone, il doit commencer par le mot clé **asynchrone** , et il doit retourner un objet de la **tâche** . Le mot clé await spécifié pour la méthode **ListBlobsSegmentedAsync** interrompt l’exécution de l’exemple de méthode jusqu'à la fin de la tâche de la liste.

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        // List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        // Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        // When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            // This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            // or by calling a different overload.
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

## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
