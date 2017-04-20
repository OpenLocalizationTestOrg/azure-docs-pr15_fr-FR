<properties
    pageTitle="Gérer l’accès anonyme en lecture à des conteneurs et des objets BLOB | Microsoft Azure"
    description="Découvrez comment créer des conteneurs et des objets BLOB disponibles pour l’accès anonyme et comment y accéder par programme."
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

# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Gérer l’accès anonyme en lecture à des conteneurs et des objets BLOB

## <a name="overview"></a>Vue d’ensemble

Par défaut, seul le propriétaire du compte de stockage peut-être accéder à des ressources de stockage au sein de ce compte. Pour le stockage Blob uniquement, vous pouvez définir des autorisations d’un conteneur pour autoriser l’accès anonyme en lecture au conteneur et à ses objets BLOB, afin que vous pouvez accorder l’accès à ces ressources sans partager votre clé de compte.

L’accès anonyme est préférable dans les cas où vous souhaitez certains objets BLOB sont toujours disponibles pour l’accès anonyme en lecture. Pour un contrôle plus grande ampleur, vous pouvez créer une signature accès partagé, qui vous permet de l’accès délégué limité à l’aide des autorisations différentes et sur un intervalle de temps spécifié. Pour plus d’informations sur la création d’un accès partagé signatures, reportez-vous [à l’aide de l’accès des Signatures (sa partagées)](storage-dotnet-shared-access-signature-part-1.md).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Accorder aux utilisateurs anonymes à des conteneurs et des objets BLOB

Par défaut, un conteneur et les objets BLOB qu’il contient sont accessibles uniquement par le propriétaire du compte de stockage. Pour donner aux utilisateurs anonymes des autorisations pour un conteneur et ses objets BLOB de lecture, vous pouvez définir les autorisations du conteneur pour autoriser l’accès public. Utilisateurs anonymes peuvent lire des objets BLOB dans un conteneur accessible au public sans s’authentifier la demande.

Conteneurs proposent les options suivantes pour gérer l’accès de conteneur :

- **Un accès en lecture public complet :** Conteneur et blob des données peuvent être lus par le biais demande anonyme. Les clients peuvent énumérer les objets BLOB dans le conteneur via demande anonyme, mais Impossible d’énumérer les conteneurs à l’intérieur du compte de stockage.

- **Public un accès pour les objets BLOB uniquement en lecture :** Données BLOB dans ce conteneur peuvent être lus par le biais demande anonyme, mais les données de conteneur ne seront pas disponibles. Clients Impossible d’énumérer les objets BLOB dans le conteneur via demande anonyme.

- **Aucun public un accès en lecture :** Conteneur et blob des données peuvent être lus par le propriétaire du compte uniquement.

Vous pouvez définir des autorisations du conteneur des façons suivantes :

- À partir du [portail Azure](https://portal.azure.com).
- Par programme, à l’aide de la bibliothèque de stockage client ou l’API REST.
- À l’aide de PowerShell. Pour en savoir plus sur la définition d’autorisations du conteneur à partir d’Azure PowerShell, voir [Utilisation de PowerShell Azure avec stockage Azure](storage-powershell-guide-full.md#how-to-manage-azure-blobs).

### <a name="setting-container-permissions-from-the-azure-portal"></a>Définition des autorisations de conteneur à partir du portail Azure

Pour définir les autorisations du conteneur à partir du [Portail Azure](https://portal.azure.com), procédez comme suit :

1. Accédez au tableau de bord pour votre compte de stockage.
2. Sélectionnez le nom du conteneur dans la liste. Cliquer sur le nom expose les objets BLOB dans le conteneur choisi
3. Sélectionnez la **stratégie d’accès** à partir de la barre d’outils.
4. Dans le champ **type d’accès** , sélectionnez votre niveau d’autorisation souhaité, comme le montre la capture d’écran ci-dessous.

    ![Boîte de dialogue conteneur métadonnées modifier](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

### <a name="setting-container-permissions-programmatically-using-net"></a>Définition des autorisations de conteneur par programme à l’aide de .NET

Pour définir des autorisations pour un conteneur à l’aide de la bibliothèque cliente .NET, vous devez tout d’abord extraire les autorisations existantes du conteneur en appelant la méthode **GetPermissions** . Définissez la propriété **PublicAccess** pour l’objet **BlobContainerPermissions** qui est renvoyée par la méthode **GetPermissions** . Pour finir, appelez la méthode **SetPermissions** avec les autorisations mis à jour.

L’exemple suivant définit les autorisations du conteneur pour l’accès en lecture publique complète. Pour définir les autorisations public accès en lecture pour les objets BLOB uniquement, définissez la propriété **PublicAccess** à **BlobContainerPublicAccessType.Blob**. Pour supprimer toutes les autorisations pour les utilisateurs anonymes, définissez la propriété sur **BlobContainerPublicAccessType.Off**.

    public static void SetPublicContainerPermissions(CloudBlobContainer container)
    {
        BlobContainerPermissions permissions = container.GetPermissions();
        permissions.PublicAccess = BlobContainerPublicAccessType.Container;
        container.SetPermissions(permissions);
    }

## <a name="access-containers-and-blobs-anonymously"></a>Accéder aux conteneurs et des objets BLOB de manière anonyme

Un client qui accède à des conteneurs et des objets BLOB de manière anonyme peut utiliser constructeurs qui ne nécessitent pas les informations d’identification. Les exemples ci-dessous illustrent plusieurs façons pour référencer des ressources de service Blob de manière anonyme.

### <a name="create-an-anonymous-client-object"></a>Créer un objet client anonyme

Vous pouvez créer un nouvel objet de service client pour l’accès anonyme en fournissant le point de terminaison du service Blob du compte. Toutefois, vous devez également connaître le nom d’un conteneur dans ce compte n’est disponible pour l’accès anonyme.

    public static void CreateAnonymousBlobClient()
    {
        // Create the client object using the Blob service endpoint.
        CloudBlobClient blobClient = new CloudBlobClient(new Uri(@"https://storagesample.blob.core.windows.net"));

        // Get a reference to a container that's available for anonymous access.
        CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

        // Read the container's properties. Note this is only possible when the container supports full public read access.
        container.FetchAttributes();
        Console.WriteLine(container.Properties.LastModified);
        Console.WriteLine(container.Properties.ETag);
    }

### <a name="reference-a-container-anonymously"></a>Faire référence à un conteneur de manière anonyme

Si vous avez l’URL dans un conteneur de manière anonyme disponible, vous pouvez l’utiliser pour référencer le conteneur directement.

    public static void ListBlobsAnonymously()
    {
        // Get a reference to a container that's available for anonymous access.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(@"https://storagesample.blob.core.windows.net/sample-container"));

        // List blobs in the container.
        foreach (IListBlobItem blobItem in container.ListBlobs())
        {
            Console.WriteLine(blobItem.Uri);
        }
    }


### <a name="reference-a-blob-anonymously"></a>Faire référence à un objet blob de manière anonyme

Si vous avez l’URL pour un blob qui est disponible pour l’accès anonyme, vous pouvez faire référence au blob directement à l’aide de cette URL :

    public static void DownloadBlobAnonymously()
    {
        CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
        blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
    }

## <a name="features-available-to-anonymous-users"></a>Fonctionnalités disponibles pour les utilisateurs anonymes

Le tableau suivant répertorie les opérations qui peuvent être appelées par les utilisateurs anonymes lors et d’un conteneur est configuré pour autoriser l’accès du public.

| Opération reste                                         | Autorisation avec un accès en lecture publique complète | Autorisation avec un accès en lecture public pour les objets BLOB uniquement |
|--------------------------------------------------------|-----------------------------------------|---------------------------------------------------|
| Liste des conteneurs                                        | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Créer le conteneur                                       | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Obtenir les propriétés du conteneur                               | Tous les                                     | Propriétaire uniquement                                        |
| Obtenir des métadonnées conteneur                                 | Tous les                                     | Propriétaire uniquement                                        |
| Définir les métadonnées de conteneur                                 | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Obtenir le conteneur et                                      | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Définir et conteneur                                      | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Supprimer le conteneur                                       | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Liste des objets BLOB                                             | Tous les                                     | Propriétaire uniquement                                        |
| Placer des objets Blob                                               | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Obtenir des objets Blob                                               | Tous les                                     | Tous les                                               |
| Obtenir les propriétés d’objets Blob                                    | Tous les                                     | Tous les                                               |
| Définir les propriétés d’objets Blob                                    | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Obtenir des métadonnées d’objets Blob                                      | Tous les                                     | Tous les                                               |
| Définir les métadonnées d’objets Blob                                      | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Placer le bloc                                              | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Obtenir la liste de blocs (blocs validées uniquement)                 | Tous les                                     | Tous les                                               |
| Obtenir la liste de blocs (blocs non validées uniquement ou tous les blocs) | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Placer la liste de blocs                                         | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Supprimer des objets Blob                                            | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Copier des objets Blob                                              | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Instantané Blob                                          | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Location Blob                                             | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Placer la Page                                               | Propriétaire uniquement                              | Propriétaire uniquement                                        |
| Obtenir les plages de pages                                        | Tous les                                     | Tous les                                                  |
| Ajouter des objets Blob                                            | Propriétaire uniquement                              | Propriétaire uniquement                                                  |


## <a name="see-also"></a>Voir aussi

- [Authentification pour les Services de stockage Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx)
- [Utilisation de Signatures accès partagé (sa)](storage-dotnet-shared-access-signature-part-1.md)
- [Délégation d’accès avec une Signature accès partagé](https://msdn.microsoft.com/library/azure/ee395415.aspx)
