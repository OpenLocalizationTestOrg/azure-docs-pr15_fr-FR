<properties
    pageTitle="Comment utiliser le stockage de fichiers Azure à partir de Python | Microsoft Azure"
    description="Découvrez comment utiliser le stockage de fichiers Azure à partir de Python à télécharger, liste, téléchargement et supprimer des fichiers."
    services="storage"
    documentationCenter="python"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="robinsh"/>

# <a name="how-to-use-azure-file-storage-from-python"></a>Comment utiliser le stockage de fichiers Azure à partir de Python

[AZURE.INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-files](../../includes/storage-try-azure-tools-files.md)]

## <a name="overview"></a>Vue d’ensemble

Cet article vous explique comment effectuer des scénarios courants à l’aide de stockage de fichiers. Les exemples sont écrits dans Python et utilisent le [Kit de développement de Microsoft Azure stockage pour Python]. Les scénarios présentés incluent télécharger, liste, le téléchargement et suppression de fichiers.

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-share"></a>Créer un partage

L’objet **FileService** vous permet de travailler avec les partages, répertoires et fichiers. Le code suivant crée un objet **FileService** . Ajoutez le code suivant dans la partie supérieure de n’importe quel fichier Python dans laquelle vous souhaitez accéder par programme stockage Azure.

    from azure.storage.file import FileService

Le code suivant crée un objet **FileService** à l’aide de la clé de compte et nom de compte de stockage.  Remplacez « mon compte » et « mykey » par votre nom de compte et la clé.

    file_service = **FileService** (account_name='myaccount', account_key='mykey')

Dans l’exemple suivant, vous pouvez utiliser un objet **FileService** pour créer le partage s’il n’existe pas.

    file_service.create_share('myshare')

## <a name="upload-a-file-into-a-share"></a>Télécharger un fichier dans un partage

Un partage de stockage de fichiers Azure contient au moins un répertoire racine dans lequel les fichiers peuvent résider. Dans cette section, vous allez découvrir comment télécharger un fichier à partir du stockage local vers le répertoire racine d’un partage.

Pour créer un fichier et télécharger des données, utilisez la **créer\_fichier\_à partir de\_chemin d’accès**, **créer\_fichier\_à partir de\_flux**, **créer\_fichier\_à partir de\_octets** ou **créer\_fichier\_à partir de\_texte** méthodes. Ils sont des méthodes qui exécutent segmentation nécessaire lorsque la taille des données dépasse 64 Mo.

**créer\_fichier\_de\_chemin d’accès** télécharge le contenu d’un fichier à partir du chemin spécifié, et **créer\_fichier\_de\_flux** télécharge le contenu à partir d’un fichier déjà ouvert/flux. **créer\_fichier\_de\_octets** télécharge un tableau d’octets, et **créer\_fichier\_de\_texte** télécharge la valeur de texte spécifiée en utilisant le codage spécifié (par défaut UTF-8).

L’exemple suivant télécharge le contenu du fichier **sunset.png** dans le fichier **myfile** .

    from azure.storage.file import ContentSettings
    file_service.create_file_from_path(
        'myshare',
        None, # We want to create this blob in the root directory, so we specify None for the directory_name
        'myfile',
        'sunset.png',
        content_settings=ContentSettings(content_type='image/png'))

## <a name="how-to-create-a-directory"></a>Comment : créer un répertoire

Vous pouvez également organiser le stockage en plaçant les fichiers à l’intérieur de sous-répertoires plutôt que de les laisser dans le répertoire racine. Le service de stockage de fichiers Azure permet de créer autant de répertoires que permettra à votre compte. Le code suivant crée un répertoire sous-adresse nommé **sampledir** sous le répertoire racine.

    file_service.create_directory('myshare', 'sampledir')

## <a name="how-to-list-files-and-directories-in-a-share"></a>Comment : la liste des fichiers et des répertoires dans un partage

Pour répertorier les fichiers et des répertoires dans un partage, utilisez la **liste\_répertoires\_et\_fichiers** méthode. Cette méthode retourne un générateur. Le code suivant renvoie le **nom** de chaque fichier et répertoire dans un partage à la console.

    generator = file_service.list_directories_and_files('myshare')
    for file_or_dir in generator:
        print(file_or_dir.name)

## <a name="download-files"></a>Télécharger des fichiers

Pour télécharger des données à partir d’un fichier, utilisez **obtenir\_fichier\_à\_chemin d’accès**, **obtenir\_fichier\_à\_flux**, **obtenir\_fichier\_à\_octets**, ou **obtenir\_fichier\_à\_texte**. Ils sont des méthodes qui exécutent segmentation nécessaire lorsque la taille des données dépasse 64 Mo.

L’exemple suivant illustre l’utilisation de **obtenir\_fichier\_à\_chemin d’accès** à télécharger le contenu du fichier **myfile** et le stocker dans le fichier de **sortie sunset.png** .

    file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')

## <a name="delete-a-file"></a>Supprimer un fichier

Enfin, pour supprimer un fichier, appelez **delete_file**.

    file_service.delete_file('myshare', None, 'myfile')

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris les notions de base de stockage de fichiers, suivez ces liens pour en savoir plus.

- [Centre de développement Python](/develop/python/)
- [Services de stockage Azure API REST](http://msdn.microsoft.com/library/azure/dd179355)
- [Blog de l’équipe stockage Azure]
- [Stockage de Microsoft Azure SDK pour Python]

[Blog de l’équipe stockage Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Stockage de Microsoft Azure SDK pour Python]: https://github.com/Azure/azure-storage-python
