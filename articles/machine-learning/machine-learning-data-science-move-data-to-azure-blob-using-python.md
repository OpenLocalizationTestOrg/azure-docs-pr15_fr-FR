<properties
    pageTitle="Déplacer des données vers et depuis le stockage Blob Azure à l’aide de Python | Microsoft Azure"
    description="Déplacer des données vers et depuis le stockage Blob Azure à l’aide de Python"
    services="machine-learning,storage"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="bradsev" />

# <a name="move-data-to-and-from-azure-blob-storage-using-python"></a>Déplacer des données vers et depuis le stockage Blob Azure à l’aide de Python

Cette rubrique décrit comment la liste, charger et télécharger des objets BLOB à l’aide de l’API Python. Avec l’API Python fournis dans Azure SDK, vous pouvez :

- Créer un conteneur
- Télécharger un blob dans un conteneur
- Télécharger des objets BLOB
- Répertorier les objets BLOB dans un conteneur
- Supprimer un blob

Pour plus d’informations sur l’utilisation de l’API Python, voir [comment utiliser le Service de stockage Blob de Python](../storage/storage-python-how-to-use-blob-storage.md).

Conseils sur les technologies utilisées pour déplacer des données à portée de main et/ou depuis le stockage Blob Azure sont liées ici :

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


> [AZURE.NOTE] Si vous utilisez un ordinateur virtuel qui a été créée avec les scripts fournis par [données scientifique Virtual machines dans Azure](machine-learning-data-science-virtual-machines.md), AzCopy est déjà installé sur l’ordinateur virtuel.

> [AZURE.NOTE] Pour une présentation complète de stockage d’objets blob Azure, reportez-vous à [Concepts de base Blob Azure](../storage/storage-dotnet-how-to-use-blobs.md) et au [Service d’objets Blob Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).


## <a name="prerequisites"></a>Conditions préalables

Ce document suppose que vous avez un abonnement Azure, un compte de stockage et la clé de stockage correspondante pour ce compte. Avant de télécharger/télécharger des données, vous devez connaître votre clé de compte et nom de compte stockage Azure.

- Pour configurer un abonnement Azure, consultez la [version d’évaluation gratuite d’un mois](https://azure.microsoft.com/pricing/free-trial/).

- Pour obtenir des instructions sur la création d’un compte de stockage et pour obtenir des informations de compte et clées, voir [comptes de stockage sur Azure](../storage/storage-create-storage-account.md).


## <a name="upload-data-to-blob"></a>Télécharger des données sur les objets Blob

Ajouter l’extrait suivant dans la partie supérieure de tout code Python dans lequel vous souhaitez accéder par programme stockage Azure :

    from azure.storage.blob import BlobService

L’objet **BlobService** vous permet d’utiliser des conteneurs et des objets BLOB. Le code suivant crée un objet BlobService à l’aide de la clé de compte et nom de compte de stockage. Remplacez le nom de compte et clé de compte avec votre compte réel et la clé.

    blob_service = BlobService(account_name="<your_account_name>", account_key="<your_account_key>")

Pour télécharger des données dans un blob, appliquez les méthodes suivantes :

1. placer\_bloc\_blob\_de\_chemin d’accès (télécharge le contenu d’un fichier à partir du chemin spécifié)
2. placer\_block_blob\_de\_fichier (télécharge le contenu à partir d’un fichier déjà ouvert/flux)
3. placer\_bloc\_blob\_de\_octets (chargements un tableau d’octets)
4. placer\_bloc\_blob\_de\_texte (télécharge la valeur de texte spécifiée en utilisant le codage spécifié)

Le code suivant télécharge un fichier local à un conteneur :

    blob_service.put_block_blob_from_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

Le code suivant télécharge tous les fichiers (à l’exception des répertoires) dans un répertoire local vers le stockage blob :

    from azure.storage.blob import BlobService
    from os import listdir
    from os.path import isfile, join

    # Set parameters here
    ACCOUNT_NAME = "<your_account_name>"
    ACCOUNT_KEY = "<your_account_key>"
    CONTAINER_NAME = "<your_container_name>"
    LOCAL_DIRECT = "<your_local_directory>"     

    blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)
    # find all files in the LOCAL_DIRECT (excluding directory)
    local_file_list = [f for f in listdir(LOCAL_DIRECT) if isfile(join(LOCAL_DIRECT, f))]

    file_num = len(local_file_list)
    for i in range(file_num):
        local_file = join(LOCAL_DIRECT, local_file_list[i])
        blob_name = local_file_list[i]
        try:
            blob_service.put_block_blob_from_path(CONTAINER_NAME, blob_name, local_file)
        except:
            print "something wrong happened when uploading the data %s"%blob_name


## <a name="download-data-from-blob"></a>Télécharger des données à partir d’objets Blob

Utiliser les méthodes suivantes pour télécharger des données depuis un blob :
1. obtenir\_blob\_à\_chemin d’accès
2. obtenir\_blob\_à\_fichier
3. obtenir\_blob\_à\_octets
4. obtenir\_blob\_à\_texte

Ces méthodes qui exécutent segmentation nécessaire lorsque la taille des données dépasse 64 Mo.

Le code suivant télécharge le contenu d’un objet blob dans un conteneur vers un fichier local :

    blob_service.get_blob_to_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

Le code suivant télécharge tous les objets BLOB à partir d’un conteneur. Elle utilise liste\_objets BLOB pour obtenir la liste des objets BLOB disponibles dans le conteneur et les télécharge dans un répertoire local.

    from azure.storage.blob import BlobService
    from os.path import join

    # Set parameters here
    ACCOUNT_NAME = "<your_account_name>"
    ACCOUNT_KEY = "<your_account_key>"
    CONTAINER_NAME = "<your_container_name>"
    LOCAL_DIRECT = "<your_local_directory>"     

    blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

    # List all blobs and download them one by one
    blobs = blob_service.list_blobs(CONTAINER_NAME)
    for blob in blobs:
        local_file = join(LOCAL_DIRECT, blob.name)
        try:
            blob_service.get_blob_to_path(CONTAINER_NAME, blob.name, local_file)
        except:
            print "something wrong happened when downloading the data %s"%blob.name
