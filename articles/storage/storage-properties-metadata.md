<properties
    pageTitle="Définir et récupérer les propriétés et les métadonnées d’objets dans le stockage Azure | Microsoft Azure"
    description="Stocker les métadonnées personnalisée sur des objets dans le stockage Azure et définir et récupérer les propriétés du système."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="set-and-retrieve-properties-and-metadata"></a>Définir et récupérer les métadonnées et propriétés #

## <a name="overview"></a>Vue d’ensemble

Objets dans les propriétés du système de prise en charge le stockage Azure et les métadonnées définis par l’utilisateur, outre les données qu’ils contiennent :

*   **Propriétés du système.** Propriétés système existent sur chaque ressource de stockage. Certaines d'entre elles peuvent être lire ou définir, tandis que d’autres personnes sont en lecture seule. En coulisses, certaines propriétés système correspondent à certains en-têtes HTTP standards. La bibliothèque de client de stockage Azure conserve pour vous.  

*   **Métadonnées définis par l’utilisateur.** Métadonnées définis par l’utilisateur sont métadonnées que vous spécifiez sur une ressource donnée, sous la forme d’une paire nom-valeur. Vous pouvez utiliser des métadonnées pour stocker des valeurs supplémentaires avec une ressource de stockage ; Ces valeurs sont selon vos besoins uniquement et ne concernent pas le comportement de la ressource.  

Extraction des valeurs de propriété et les métadonnées pour une ressource de stockage est un processus en deux étapes. Que vous puissiez lire ces valeurs, vous devez explicitement les récupérer en appelant la méthode **FetchAttributes** .

> [AZURE.IMPORTANT] Valeurs des propriétés et des métadonnées pour une ressource de stockage ne sont pas remplies sauf si vous appelez une des méthodes **FetchAttributes** . 

## <a name="setting-and-retrieving-properties"></a>Définition et récupération de propriétés

Pour récupérer des valeurs de propriétés, appelez la méthode **FetchAttributes** sur votre blob ou du conteneur pour remplir les propriétés, puis lisez les valeurs.

Pour définir des propriétés sur un objet, indiquez la valeur de propriété, puis appelez la méthode **SetProperties** .

L’exemple suivant crée un conteneur et certaines de ses valeurs de propriété écrit dans une fenêtre de la console :

    //Parse the connection string for the storage account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    
    //Create the service client object for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it does not already exist.
    container.CreateIfNotExists();

    // Fetch container properties and write out their values.
    container.FetchAttributes();
    Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
    Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
    Console.WriteLine("ETag: {0}", container.Properties.ETag);
    Console.WriteLine();

## <a name="setting-and-retrieving-metadata"></a>Définition et récupération de métadonnées

Vous pouvez spécifier des métadonnées comme une ou plusieurs paires nom-valeur sur une ressource blob ou du conteneur. Pour définir les métadonnées, ajoutez des paires nom-valeur à la collection de **métadonnées** sur la ressource, puis appelez la méthode **SetMetadata** pour enregistrer les valeurs dans le service.

> [AZURE.NOTE] Le nom de votre métadonnées doit respecter les conventions d’appellation pour les identificateurs c#.
 
L’exemple suivant définit les métadonnées sur un conteneur. Une valeur est définie à l’aide de la méthode **Add** de la collection de sites. L’autre valeur est définie à l’aide de la syntaxe de la valeur/clé implicite. Les deux sont valides.

    public static void AddContainerMetadata(CloudBlobContainer container)
    {
        //Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        //Set the container's metadata.
        container.SetMetadata();
    }

Pour extraire les métadonnées, appelez la méthode **FetchAttributes** sur votre blob ou du conteneur pour remplir la collection de **métadonnées** , puis lire les valeurs, comme illustré dans l’exemple ci-dessous.

    public static void ListContainerMetadata(CloudBlobContainer container)
    {
        //Fetch container attributes in order to populate the container's properties and metadata.
        container.FetchAttributes();

        //Enumerate the container's metadata.
        Console.WriteLine("Container metadata:");
        foreach (var metadataItem in container.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
    }

## <a name="see-also"></a>Voir aussi  

- [Bibliothèque de Client de stockage Azure pour référence .NET](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
- [Bibliothèque de Client stockage Azure package .NET](https://www.nuget.org/packages/WindowsAzure.Storage/) 
