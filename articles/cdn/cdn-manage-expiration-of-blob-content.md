<properties
 pageTitle="Gérer l’expiration de contenu d’objets blob Azure stockage dans Azure CDN | Microsoft Azure"
 description="Découvrez les options permettant de contrôler la durée de vie des objets BLOB dans Azure CDN mise en cache."
 services="cdn"
 documentationCenter=""
 authors="camsoper"
 manager="erikre"
 editor=""/>
<tags
 ms.service="cdn"
 ms.workload="media"
 ms.tgt_pltfrm="na"
 ms.devlang="multiple"
 ms.topic="article"
 ms.date="09/15/2016"
 ms.author="casoper"/>


# <a name="manage-expiration-of-azure-storage-blob-content-in-azure-cdn"></a>Gérer l’expiration de contenu d’objets blob Azure stockage dans Azure CDN

> [AZURE.SELECTOR]
- [Services de Cloud/applications Web Azure, ASP.NET ou IIS](cdn-manage-expiration-of-cloud-service-content.md)
- [Service de stockage sur Azure blob](cdn-manage-expiration-of-blob-content.md)

Le [service blob](../storage/storage-introduction.md#blob-storage) dans le [Stockage Azure](../storage/storage-introduction.md) fait partie de différentes origines basée sur Azure intégrées à Azure CDN.  N’importe quel contenu blob accessible au public peut être mis en cache dans Azure CDN jusqu'à ce que sa durée de vie (TTL) s’écoule.  La durée de vie est déterminée par l' [en-tête de *Contrôle du Cache* ](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) dans la réponse HTTP à partir du stockage Azure.

>[AZURE.TIP] Vous pouvez choisir de ne définir aucun TTL sur un blob.  Dans ce cas, Azure CDN applique automatiquement une durée de vie de sept jours par défaut.
>
>Pour plus d’informations sur le fonctionnement de Azure CDN pour accélérer l’accès à des objets BLOB et d’autres fichiers, consultez la [Vue d’ensemble de Azure CDN](./cdn-overview.md).
>
>Pour plus d’informations sur le service d’objets blob Azure stockage, voir [Concepts de Service Blob](https://msdn.microsoft.com/library/dd179376.aspx). 

Ce didacticiel décrit les différentes manières que vous pouvez définir la durée de vie sur un blob dans le stockage Azure.  

## <a name="azure-powershell"></a>PowerShell Azure

[Azure PowerShell](../powershell-install-configure.md) est une des façons le plus rapide et la plus puissantes pour administrer vos services Azure.  Utiliser la `Get-AzureStorageBlob` applet de commande pour obtenir une référence à l’objet blob, puis définissez la `.ICloudBlob.Properties.CacheControl` propriété. 

```powershell
# Create a storage context
$context = New-AzureStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzureStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "public, max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

>[AZURE.TIP] Vous pouvez également utiliser PowerShell pour [gérer vos profils CDN et les points de terminaison](./cdn-manage-powershell.md).

## <a name="azure-storage-client-library-for-net"></a>Bibliothèque de Client de stockage Azure pour .NET

Pour définir la durée de vie d’un objet blob à l’aide de .NET, utilisez la [Bibliothèque de Client de stockage Azure pour .NET](../storage/storage-dotnet-how-to-use-blobs.md) pour définir la propriété [CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx) .

```csharp
class Program
{
    const string connectionString = "<storage connection string>";
    static void Main()
    {
        // Retrieve storage account information from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
        
        // Create a blob client for interacting with the blob service.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        
        // Create a reference to the container
        CloudBlobContainer container = blobClient.GetContainerReference("<container name>");

        // Create a reference to the blob
        CloudBlob blob = container.GetBlobReference("<blob name>");

        // Set the CacheControl property to expire in 1 hour (3600 seconds)
        blob.Properties.CacheControl = "public, max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

>[AZURE.TIP] Il existe de nombreuses autres exemples de code .NET disponibles dans les [Exemples de stockage d’objets Blob Azure pour .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/).

## <a name="other-methods"></a>Autres méthodes

- [Interface de ligne de commande Azure](../xplat-cli-install.md)

    Lorsque vous transférez le blob, définissez la propriété *cacheControl* en utilisant la `-p` basculer.  Cet exemple montre la durée de vie d’une heure (3 600 secondes).

    ```text
    azure storage blob upload -c <connectionstring> -p cacheControl="public, max-age=3600" .\test.txt myContainer test.txt
    ```

- [Services de stockage Azure API REST](https://msdn.microsoft.com/library/azure/dd179355.aspx)

    Définissez explicitement la propriété de *contrôle du cache blob x ms* sur une demande de [Placer les objets Blob](https://msdn.microsoft.com/en-us/library/azure/dd179451.aspx), [Placer la liste de blocage](https://msdn.microsoft.com/en-us/library/azure/dd179467.aspx)ou [Définition des propriétés d’objets Blob](https://msdn.microsoft.com/library/azure/ee691966.aspx) .

- Outils de gestion du stockage tiers

    Certains outils de gestion du stockage Azure tiers permettent de définir la propriété *CacheControl* d’objets BLOB. 

## <a name="testing-the-cache-control-header"></a>Test de l’en-tête de *Contrôle du Cache*

Vous pouvez facilement vérifier la durée de vie de vos objets BLOB.  À l’aide [d’Outils de développement](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/), test que votre blob est, y compris l’en-tête de réponse *Cache-Control de votre navigateur* .  Vous pouvez également utiliser un outil comme **wget**, [Postman](https://www.getpostman.com/)ou [Fiddler](http://www.telerik.com/fiddler) pour examiner les en-têtes de réponse.

## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur l’en-tête de *Contrôle du Cache*](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
- [Découvrez comment gérer l’expiration du contenu de Service Cloud dans Azure CDN](./cdn-manage-expiration-of-cloud-service-content.md)

