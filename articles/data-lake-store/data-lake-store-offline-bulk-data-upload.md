<properties
   pageTitle="Chargez grandes quantités de données dans le magasin de Lake de données à l’aide des méthodes en mode hors connexion | Microsoft Azure"
   description="Utilisez l’outil AdlCopy pour copier les données d’objets BLOB Azure stockage au magasin Lake des données"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/07/2016"
   ms.author="nitinme"/>

# <a name="use-azure-import-export-service-for-offline-copy-of-data-to-data-lake-store"></a>Utiliser le Service d’importation / exportation de Azure pour copie hors connexion de données au magasin Lake des données

Dans cet article, vous allez découvrir comment copier des jeux de données volumineux (> 200 Go) dans un magasin Lake de données Azure à l’aide des méthodes de copie hors connexion, comme le [Service d’importation/exportation Azure](../storage/storage-import-export-service.md). Plus précisément, le fichier utilisé comme exemple dans cet article est 339,420,860,416 octets c'est-à-dire 319 Go sur le disque. Nous allons appeler ce fichier 319GB.tsv.

Service d’importation/exportation Azure permet de transférer en toute sécurité de grandes quantités de données à Azure blob storage par livraison lecteurs de disques durs à un centre de données Azure.

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

- **Compte de stockage azure**.

- **Compte azure données Lake Analytique (facultatif)** - en voir [prise en main Azure données Lake Analytique](../data-lake-analytics/data-lake-analytics-get-started-portal.md) pour obtenir des instructions sur la création d’un compte de données Lake Store.


## <a name="preparing-the-data"></a>Préparation des données

Avant d’utiliser le Service d’importation/exportation, nous devons interrompre le fichier de données pour être transférés **dans les copies qui sont inférieure à 200 Go** dans taille. C’est parce que l’outil d’importation ne fonctionne pas avec les fichiers dépasse 200 Go. Pour se conformer, dans ce didacticiel, nous fractionner le fichier en blocs de 100 Go octets chaque. Vous pouvez le faire aisément en utilisant [Cygwin](https://cygwin.com/install.html). Cygwin prend en charge la commande Linux qui permet aux utilisateurs effectuer cette action facilement. Pour notre exemple, nous utilisons la commande suivante.

    split -b 100m 319GB.tsv

L’opération de fractionnement crée des fichiers avec les noms ci-dessous.

    319GB.tsv-part-aa

    319GB.tsv-part-ab

    319GB.tsv-part-ac

    319GB.tsv-part-ad

## <a name="get-disks-ready-with-data"></a>Préparer des disques avec des données

Suivez les instructions à [L’aide Azure importer/exporter Service](../storage/storage-import-export-service.md) (sous la section **préparer vos lecteurs** ) pour préparer vos disques durs. Voici le flux global comment préparer le disque.

1. Se procurer un disque dur qui répond à la configuration minimale requise pour être utilisé pour le Service d’importation/exportation Auzre.

2. Identifier un compte de stockage Azure dans lequel les données seront copiées une seule fois dessus si expédiés au centre de données Azure.

3. Utilisez l' [Outil d’importation/exportation Azure](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409), un utilitaire de ligne de commande. Voici un extrait de l’exemple sur la façon d’utiliser l’outil.

    ````
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ````
    Voir [à l’aide de Azure importer/exporter Service](../storage/storage-import-export-service.md) pour plusieurs exemples d’extraits sur l’utilisation de l' **Outil d’importation/exportation Azure**.

4. La commande ci-dessus crée un fichier journal à l’emplacement spécifié. Ce fichier journal vous permet de créer une tâche d’importation à partir du [Portail classique Azure](https://manage.windowsazure.com).

## <a name="create-an-import-job"></a>Créer une tâche d’importation

Vous pouvez désormais créer une tâche d’importation en suivant les instructions à [L’aide Azure importer/exporter Service](../storage/storage-import-export-service.md) (sous la section **créer la tâche d’importation** ). Pour cette tâche d’importation, avec d’autres détails, également fournir le fichier journal créé lors de la préparation les lecteurs de disques.

## <a name="physically-ship-the-disks"></a>Physiquement livrer les disques

Vous pouvez maintenant livrer physiquement les disques à un centre de données Azure dans lequel les données sont copiées dans le BLOB de stockage Azure vous avez fourni lors de la création de la tâche d’importation. En outre, lors de la création de la tâche, si vous choisi pour fournir les informations de suivi des versions ultérieures, vous pouvez désormais revenir à votre tâche d’importation et mises à jour le numéro de suivi.

## <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-store"></a>Copier des données d’objets BLOB Azure stockage au magasin Lake des données Azure

Une fois que l’état de la tâche d’importation affiche terminée, vous pouvez vérifier si les données sont disponibles dans le BLOB de stockage Azure vous aviez spécifié. Vous pouvez utiliser puis diverses méthodes pour déplacer ces données dans les objets BLOB stockage Azure au magasin Lake des données Azure. Pour toutes les options disponibles pour le téléchargement des données, voir [Ingesting des données dans le magasin de Lake de données](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-store).

Dans cette section, nous vous fournissons les définitions JSON que vous pouvez utiliser pour créer un pipeline Azure Data Factory pour copier les données. Vous pouvez utiliser les définitions JSON à partir du [portail Azure](../data-factory/data-factory-copy-activity-tutorial-using-azure-portal.md) ou de [Visual Studio](../data-factory/data-factory-copy-activity-tutorial-using-visual-studio.md) ou de [PowerShell Azure](../data-factory/data-factory-copy-activity-tutorial-using-powershell.md).

### <a name="source-linked-service-azure-storage-blob"></a>Source liée Service (stockage Azure Blob)

````
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
````

### <a name="target-linked-service-azure-data-lake-store"></a>Cible liée Service (Azure données Lake Store)

````
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "description": "",
        "typeProperties": {
            "authorization": "<Click 'Authorize' to allow this data factory and the activities it runs to access this Data Lake Store with your access rights>",
            "dataLakeStoreUri": "https://<adls_account_name>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<OAuth session id from the OAuth authorization session. Each session id is unique and may only be used once>"
        }
    }
}
````
### <a name="input-data-set"></a>Jeu de données d’entrée
````
{
    "name": "InputDataSet",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "importcontainer/vf1/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
````
### <a name="output-data-set"></a>Jeu de données de sortie
````
{
"name": "OutputDataSet",
"properties": {
  "published": false,
  "type": "AzureDataLakeStore",
  "linkedServiceName": "AzureDataLakeStoreLinkedService",
  "typeProperties": {
    "folderPath": "/importeddatafeb8job/"
    },
  "availability": {
    "frequency": "Hour",
    "interval": 1
    }
  }
}
````
### <a name="pipeline-copy-activity"></a>Pipeline (copie activité)
````
{
    "name": "CopyImportedData",
    "properties": {
        "description": "Pipeline with copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity"
            }
        ],
        "start": "2016-02-08T22:00:00Z",
        "end": "2016-02-08T23:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
````
Pour plus d’informations sur l’utilisation d’Azure Data Factory pour déplacer des données à partir d’Azure stockage Blob et Azure Data Lake Store, voir [déplacer des données à partir d’Azure stockage Blob à Azure données Lake Store à l’aide d’Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store).

## <a name="reconstruct-the-data-files-in-azure-data-lake-store"></a>Le recréer les fichiers de données Azure données Lake Store

Nous avons commencé avec un fichier qui a été Go 319 taille et tirez-en dans des fichiers de plus petite taille afin qu’il peut être transféré au moyen du Service d’importation/exportation Azure. À présent que les données se trouvent dans le magasin de Lake données Azure, nous pouvons reconstrcut le fichier à sa taille d’origine. Vous pouvez utiliser la cmldts Azure PowerShell suivante pour le faire.

````
# Login to our account
Login-AzureRmAccount

# List your subscriptions
Get-AzureRmSubscription

# Switch to the subscription you want to work with
Set-AzureRmContext –SubscriptionId
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzureRmDataLakeStoreItem -AccountName "<adls_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv”
````

## <a name="next-steps"></a>Étapes suivantes

- [Sécuriser les données dans les données Lake Store](data-lake-store-secure-data.md)
- [Utiliser des données Azure Lake Analytique avec données Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utiliser Azure HDInsight avec données Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
