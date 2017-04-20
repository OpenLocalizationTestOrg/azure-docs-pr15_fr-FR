<properties
    pageTitle="Comment utiliser le stockage de fichiers à partir de Java | Microsoft Azure"
    description="Découvrez comment utiliser le service Azure fichier à télécharger, liste et supprimer des fichiers. Exemples écrits en Java."
    services="storage"
    documentationCenter="java"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn" />

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robinsh"/>

# <a name="how-to-use-file-storage-from-java"></a>Comment utiliser le stockage de fichiers à partir de Java

[AZURE.INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-files.md)]

## <a name="overview"></a>Vue d’ensemble

Dans ce guide vous allez découvrir comment effectuer des opérations de base sur le service de stockage Microsoft Azure. Via exemples écrits en Java, vous allez apprendre à créer des actions et des répertoires, charger, de liste et supprimer des fichiers. Si vous débutez au service de stockage de fichiers de Microsoft Azure, passant par les concepts dans les sections suivantes sera très utile pour comprendre les exemples.

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Créer une application Java

Pour générer les exemples, vous devez le Kit de développement Java (JDK) et les crochets [] [Azure stockage SDK pour Java]. Vous devez également créer un compte de stockage Azure.

## <a name="setup-your-application-to-use-file-storage"></a>Configurer votre application pour utiliser le stockage de fichiers

Pour utiliser les API de stockage Azure, ajoutez l’instruction suivante en haut du fichier Java dans laquelle vous souhaitez accéder au service de stockage à partir de.

    // Include the following imports to use blob APIs.
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.file.*;

## <a name="setup-an-azure-storage-connection-string"></a>Programme d’installation une chaîne de connexion de stockage Azure

Pour utiliser le stockage de fichiers, vous devez vous connecter à votre compte de stockage Azure. La première étape serait pour configurer une chaîne de connexion qui nous utiliserons pour vous connecter à votre compte de stockage. Nous allons définir une variable statique pour ce faire.

    // Configure the connection-string with your values
    public static final String storageConnectionString =
        "DefaultEndpointsProtocol=http;" +
        "AccountName=your_storage_account_name;" +
        "AccountKey=your_storage_account_key";

> [AZURE.NOTE] Remplacez your_storage_account_name et your_storage_account_key par les valeurs réelles pour votre compte de stockage.

## <a name="connecting-to-an-azure-storage-account"></a>Connexion à un compte de stockage Azure

Pour vous connecter à votre compte de stockage, vous devez utiliser l’objet **CloudStorageAccount** , en passant une chaîne de connexion à sa méthode **analyser** .

    // Use the CloudStorageAccount object to connect to your storage account
    try {
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
    } catch (InvalidKeyException invalidKey) {
        // Handle the exception
    }

**CloudStorageAccount.parse** lève une InvalidKeyException afin que vous devez placer à l’intérieur d’un bloc try/captures.

## <a name="how-to-create-a-share"></a>Comment : créer un partage

Tous les fichiers et des répertoires de stockage de fichiers se trouvent dans un conteneur appelé un **partage**. Votre compte de stockage peut avoir autant partages que permet la capacité de votre compte. Pour obtenir l’accès à un partage de sites et son contenu, vous devez utiliser un client de stockage de fichier.

    // Create the file storage client.
    CloudFileClient fileClient = storageAccount.createCloudFileClient();

Utilise le client de stockage de fichier, vous pouvez ensuite obtenir une référence à un partage de.

    // Get a reference to the file share
    CloudFileShare share = fileClient.getShareReference("sampleshare");

Pour créer réellement le partage, utilisez la méthode **createIfNotExists** de l’objet CloudFileShare.

    if (share.createIfNotExists()) {
        System.out.println("New share created");
    }

À ce stade, **partager** contient une référence à un partage nommé **sampleshare**.

## <a name="how-to-upload-a-file"></a>Comment : télécharger un fichier

Un partage de stockage de fichiers Azure contient au moins un répertoire racine dans lequel les fichiers peuvent résider. Dans cette section, vous allez découvrir comment télécharger un fichier à partir du stockage local vers le répertoire racine d’un partage.

La première étape de téléchargement d’un fichier est d’obtenir une référence à l’annuaire dans lequel il doit se trouver. Pour cela, en appelant la méthode **getRootDirectoryReference** de l’objet partage.

    //Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

Maintenant que vous avez une référence à la racine du partage, vous pouvez télécharger un fichier sur ce dernier en utilisant le code suivant.

    // Define the path to a local file.
    final String filePath = "C:\\temp\\Readme.txt";

    CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
    cloudFile.uploadFromFile(filePath);

## <a name="how-to-create-a-directory"></a>Comment : créer un répertoire

Vous pouvez également organiser le stockage en plaçant les fichiers à l’intérieur de sous-répertoires plutôt que de les laisser dans le répertoire racine. Le service de stockage de fichiers Azure permet de créer autant de répertoires que permettra à votre compte. Le code suivant crée un répertoire sous-adresse nommé **sampledir** sous le répertoire racine.

    //Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    //Get a reference to the sampledir directory
    CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

    if (sampleDir.createIfNotExists()) {
        System.out.println("sampledir created");
    } else {
        System.out.println("sampledir already exists");
    }

## <a name="how-to-list-files-and-directories-in-a-share"></a>Comment : la liste des fichiers et des répertoires dans un partage

Obtention d’une liste de fichiers et des répertoires au sein d’un partage facilement exécuté en appelant **listFilesAndDirectories** sur une référence CloudFileDirectory. La méthode renvoie une liste d’objets ListFileItem qui vous pouvez modifions. Par exemple, le code suivant permet de répertorier les fichiers et des répertoires dans le répertoire racine.

    //Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
        System.out.println(fileItem.getUri());
    }


## <a name="how-to-download-a-file"></a>Comment : télécharger un fichier

Une des opérations plus fréquentes que vous effectuerez contre le stockage de fichiers consiste à télécharger des fichiers. Dans l’exemple suivant, le code télécharge SampleFile.txt et affiche son contenu.

    //Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    //Get a reference to the directory that contains the file
    CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

    //Get a reference to the file you want to download
    CloudFile file = sampleDir.getFileReference("SampleFile.txt");

    //Write the contents of the file to the console.
    System.out.println(file.downloadText());

## <a name="how-to-delete-a-file"></a>Comment : supprimer un fichier

Une autre opération de stockage de fichier courantes est la suppression du fichier. Le code suivant supprime un fichier nommé SampleFile.txt stocké à l’intérieur d’un répertoire nommé **sampledir**.


    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    // Get a reference to the directory where the file to be deleted is in
    CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

    String filename = "SampleFile.txt"
    CloudFile file;

    file = containerDir.getFileReference(filename)
    if ( file.deleteIfExists() ) {
        System.out.println(filename + " was deleted");
    }


## <a name="how-to-delete-a-directory"></a>Comment : supprimer un répertoire

Suppression d’un répertoire est une tâche relativement simple, bien qu’il convient de noter que vous ne pouvez pas supprimer un répertoire contenant des fichiers ou autres répertoires.

    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    // Get a reference to the directory you want to delete
    CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

    // Delete the directory
    if ( containerDir.deleteIfExists() ) {
        System.out.println("Directory deleted");
    }


## <a name="how-to-delete-a-share"></a>Comment : supprimer un partage

Suppression d’un partage résulte de l’appel de la méthode **deleteIfExists** un objet CloudFileShare. Voici des exemples de code qui a.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

        // Create the file client.
       CloudFileClient fileClient = storageAccount.createCloudFileClient();

       // Get a reference to the file share
       CloudFileShare share = fileClient.getShareReference("sampleshare");

       if (share.deleteIfExists()) {
           System.out.println("sampleshare deleted");
       }
    } catch (Exception e) {
        e.printStackTrace();
    }

## <a name="next-steps"></a>Étapes suivantes

Si vous voulez en savoir plus sur les autres API de stockage Azure, suivez ces liens.

- [Centre de développement Java](http://azure.microsoft.com/develop/java/)
- [Stockage Azure SDK pour Java](https://github.com/azure/azure-storage-java)
- [Stockage Azure SDK pour Android](https://github.com/azure/azure-storage-android)
- [Référence du Kit de développement logiciel Client stockage Azure](http://dl.windowsazure.com/storage/javadoc/)
- [Services de stockage Azure API REST](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Blog de l’équipe stockage Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- [Transférer des données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md)
