<properties
    pageTitle="Déplacer des données vers et depuis le stockage Blob Azure à l’aide de AzCopy | Microsoft Azure"
    description="Déplacer des données vers et depuis le stockage Blob Azure à l’aide de AzCopy"
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

# <a name="move-data-to-and-from-azure-blob-storage-using-azcopy"></a>Déplacer des données vers et depuis le stockage Blob Azure à l’aide de AzCopy

AzCopy est un utilitaire de ligne de commande conçu pour télécharger le téléchargement et copier des données vers et depuis Microsoft Azure blob fichier et stockage de tables.

Pour obtenir des instructions sur l’installation de AzCopy et des informations supplémentaires sur l’utilisation avec la plateforme Azure, voir [Prise en main l’utilitaire de ligne de commande AzCopy](../storage/storage-use-azcopy.md).

Conseils sur les technologies utilisées pour déplacer des données à portée de main et/ou depuis le stockage Blob Azure sont liées ici :

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


> [AZURE.NOTE] Si vous utilisez un ordinateur virtuel qui a été créée avec les scripts fournis par [données scientifique Virtual machines dans Azure](machine-learning-data-science-virtual-machines.md), AzCopy est déjà installé sur l’ordinateur virtuel.

> [AZURE.NOTE] Pour une présentation complète de stockage d’objets blob Azure, reportez-vous à [Concepts de base Blob Azure](../storage/storage-dotnet-how-to-use-blobs.md) et au [Service d’objets Blob Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).


## <a name="prerequisites"></a>Conditions préalables

Ce document suppose que vous avez un abonnement Azure, un compte de stockage et la clé de stockage correspondante pour ce compte. Avant de télécharger/télécharger des données, vous devez connaître votre clé de compte et nom de compte stockage Azure.

- Pour configurer un abonnement Azure, consultez la [version d’évaluation gratuite d’un mois](https://azure.microsoft.com/pricing/free-trial/).

- Pour obtenir des instructions sur la création d’un compte de stockage et pour obtenir des informations de compte et clées, voir [comptes de stockage sur Azure](../storage/storage-create-storage-account.md).


## <a name="run-azcopy-commands"></a>Exécuter des commandes AzCopy

Pour exécuter les commandes AzCopy, ouvrez une fenêtre de commande et accédez au répertoire d’installation AzCopy sur votre ordinateur, où se trouve l’exécutable AzCopy.exe. 

La syntaxe de la base de commandes AzCopy est la suivante :

    AzCopy /Source:<source> /Dest:<destination> [Options]

>[AZURE.NOTE] Vous pouvez ajouter l’emplacement d’installation AzCopy chemin sur votre système et puis exécutez les commandes à partir de n’importe quel répertoire. Par défaut, AzCopy est installé *% ProgramFiles(x86) %\Microsoft SDKs\Azure\AzCopy* ou *%ProgramFiles%\Microsoft SDKs\Azure\AzCopy*.

## <a name="upload-files-to-an-azure-blob"></a>Télécharger des fichiers sur un blob Azure

Pour télécharger un fichier, utilisez la commande suivante :

    # Upload from local file system
    AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S


## <a name="download-files-from-an-azure-blob"></a>Télécharger des fichiers à partir d’un blob Azure

Pour télécharger un fichier à partir d’un blob Azure, utilisez la commande suivante :

    # Downloading blobs to local file system
    AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S


## <a name="transfer-blobs-between-azure-containers"></a>Transférer des objets BLOB entre conteneurs Azure

Pour transférer des objets BLOB entre conteneurs Azure, utilisez la commande suivante :

    # Transferring blobs between Azure containers
    AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S

    <your_account_name>: your storage account name
    <your_account_key>: your storage account key
    <your_container_name>: your container name
    <your_sub_directory_at_blob>: the sub directory in the container
    <your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
    <file_pattern>: pattern of file names to be transferred. The standard wildcards are supported


## <a name="tips-for-using-azcopy"></a>Conseils pour l’utilisation AzCopy

> [AZURE.TIP]   
> 1. Lorsque le **téléchargement** de fichiers, */S* des téléchargements récursive de fichiers. Sans ce paramètre, ses sous-répertoires ne sont pas téléchargés.  
> 2. Lorsque le **téléchargement de** fichier, */S* recherche dans le conteneur de manière récursive jusqu'à ce que tous les fichiers dans le répertoire spécifié et ses sous-répertoires ou tous les fichiers qui correspondent au modèle spécifié dans le répertoire donné et ses sous-répertoires, sont téléchargés.  
> 3.  Vous ne pouvez pas spécifier un **fichier blob spécifiques** à télécharger en utilisant le paramètre */Source* . Pour télécharger un fichier spécifique, spécifiez le nom du fichier à télécharger à l’aide du paramètre */Pattern* blob. Paramètre **/S** peut être utilisé pour que AzCopy recherchez un nom de fichier modèle manière récursive. Sans le paramètre de motif, AzCopy télécharge tous les fichiers dans ce répertoire.
