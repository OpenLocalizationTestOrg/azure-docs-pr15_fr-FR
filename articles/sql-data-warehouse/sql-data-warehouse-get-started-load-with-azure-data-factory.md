<properties
   pageTitle="Charger les données avec Azure Data Factory | Microsoft Azure"
   description="Apprenez à charger les données avec Azure Data Factory"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>
<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/13/2016"
   ms.author="mausher;barbkess"/>

# <a name="load-data-with-azure-data-factory"></a>Charger les données avec des données Azure usine 

> [AZURE.SELECTOR]
- [Redgate](sql-data-warehouse-load-with-redgate.md)  
- [Données par défaut](sql-data-warehouse-get-started-load-with-azure-data-factory.md)  
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)  
- [BCP](sql-data-warehouse-load-with-bcp.md)  

Ce didacticiel montre comment créer un pipeline dans Azure Data Factory pour déplacer des données à partir d’Azure stockage Blob Azure SQL Data Warehouse. Les étapes suivantes, vous pourrez :

+ Définir des exemples de données dans un Blob de stockage Azure.
+ Connecter des ressources à Azure Data Factory.
+ Créer une opportunité pour déplacer des données d’objets BLOB Storage à Data Warehouse SQL.

>[AZURE.VIDEO loading-azure-sql-data-warehouse-with-azure-data-factory]


## <a name="before-you-begin"></a>Avant de commencer

Pour vous familiariser avec Azure Data Factory, voir [présentation Azure Data Factory][].

### <a name="create-or-identify-resources"></a>Créez ou identifiez les ressources

Avant de commencer ce didacticiel, vous devez disposer les ressources suivantes :

   + **Azure stockage Blob**: ce didacticiel utilise Azure stockage Blob comme source de données pour le pipeline de Azure Data Factory, et donc une disponible pour stocker les données d’exemple. Si vous n’avez pas encore, découvrez comment [créer un compte de stockage][].

   + **Data Warehouse SQL**: ce didacticiel déplace les données à partir d’Azure stockage Blob vers Data Warehouse SQL et qu’il soit nécessaire d’avoir un data warehouse en ligne qui est chargé avec les données d’exemple AdventureWorksDW c’est le cas. Si vous n’avez pas déjà un data warehouse, découvrez comment [configurer une][Create a SQL Data Warehouse]. Si vous avez un data warehouse mais n’a pas été lui associer les données d’exemple, vous pouvez [charger manuellement][Load sample data into SQL Data Warehouse].

   + **Azure Data Factory**: Azure Data Factory se termine la charge réelle et que vous devez donc une que vous pouvez utiliser pour générer le pipeline de déplacement de données. Si vous n’avez pas encore, découvrez comment en créer un à l’étape 1 de [prise en main Azure Data Factory (données usine éditeur)][].

   + **AZCopy**: vous devez AZCopy pour copier les données d’exemple à partir de votre client local vers votre Blob de stockage Azure. Pour obtenir des instructions d’installation, consultez la [documentation de AZCopy][].

## <a name="step-1-copy-sample-data-to-azure-storage-blob"></a>Étape 1 : Copier des données d’exemple dans Azure stockage Blob

Une fois que tous les éléments prêts, vous êtes prêt à copier les données d’exemple dans votre Blob de stockage Azure.

1. [Télécharger des exemples de données][]. Ces données ajoute une autre trois ans de données de ventes à vos données exemple AdventureWorksDW.

2. Utilisez cette commande AZCopy pour copier les trois années de données dans votre Blob de stockage Azure.

    ````
    AzCopy /Source:<Sample Data Location>  /Dest:https://<storage account>.blob.core.windows.net/<container name> /DestKey:<storage key> /Pattern:FactInternetSales.csv
    ````


## <a name="step-2-connect-resources-to-azure-data-factory"></a>Étape 2 : Connecter les ressources à Azure Data Factory

À présent que les données sont en place, nous pouvons créer le pipeline Azure Data Factory pour déplacer les données depuis le stockage blob Azure dans SQL Data Warehouse.

Pour commencer, ouvrez le [portail Azure][] et sélectionnez votre usine de données dans le menu de gauche.

### <a name="step-21-create-linked-service"></a>Étape 2.1 : Créer des services liés

Lien de votre compte de stockage Azure et SQL Data Warehouse vers votre usine de données.  

1. Tout d’abord, commencez le processus d’inscription en cliquant sur la section « Services liées » de votre usine de données, puis cliquez sur « stockent de nouvelles données ». Choisissez un nom pour enregistrer votre stockage azure sous, sélectionnez le stockage Azure lors de la frappe et puis entrez votre clé de compte et le nom du compte.

2. Pour vous inscrire SQL Data Warehouse accédez à la section « Auteur et déployer », sélectionnez « Nouveau données Store », puis Azure SQL Data Warehouse. Copier et coller dans ce modèle, puis renseignez vos informations spécifiques.

    ```JSON
    {
        "name": "<Linked Service Name>",
        "properties": {
            "description": "",
            "type": "AzureSqlDW",
            "typeProperties": {
                 "connectionString": "Data Source=tcp:<server name>.database.windows.net,1433;Initial Catalog=<server name>;Integrated Security=False;User ID=<user>@<servername>;Password=<password>;Connect Timeout=30;Encrypt=True"
             }
        }
    }
    ```

### <a name="step-22-define-the-dataset"></a>Étape 2.2 : Définissez le jeu de données

Après avoir créé les services liées, nous devons définir les ensembles de données.  Ici, cela signifie définition de la structure des données qu’il est déplacées à partir de votre espace de stockage à votre data warehouse.  Vous pouvez en savoir plus sur la création

1. Lancer ce processus en accédant à la section « Auteur et déployer » de votre usine de données.

2. Cliquez sur « Nouveau groupe de données », puis « Stockage Blob Azure » pour lier votre espace de stockage à votre usine de données.  Vous pouvez utiliser le script pour définir vos données dans le stockage Blob Azure ci-dessous :

    ```JSON
    {
        "name": "<Dataset Name>",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "<linked storage name>",
            "typeProperties": {
                "folderPath": "<containter name>",
                "fileName": "FactInternetSales.csv",
                "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
                }
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }
    ```

3. Maintenant, nous définir notre dataset pour Data Warehouse SQL. Commençons dans la même manière, en cliquant sur « Nouveau groupe de données », puis « Entrepôt de données SQL Azure ».

    ```JSON
    {
        "name": "DWDataset",
        "properties": {
            "type": "AzureSqlDWTable",
            "linkedServiceName": "AzureSqlDWLinkedService",
            "typeProperties": {
                "tableName": "FactInternetSales"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```

## <a name="step-3-create-and-run-your-pipeline"></a>Étape 3 : Créer et exécuter votre pipeline

Pour finir, nous configurer et exécuter le pipeline dans Azure Data Factory.  Il s’agit de l’opération qui se termine le déplacement des données réelles.  Vous pouvez trouver une vue complète des opérations que vous pouvez réaliser des Data Warehouse SQL Azure Data Factory [ici][Move data to and from Azure SQL Data Warehouse using Azure Data Factory].

Dans la section « Auteur et déployer », cliquez sur « Autres commandes », puis « Nouveau Pipeline ».  Après avoir créé le pipeline, vous pouvez utiliser la sous code pour transférer les données à votre data warehouse :

```JSON
{
    "name": "<Pipeline Name>",
    "properties": {
        "description": "<Description>",
        "activities": [
          {
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "skipHeaderLineCount": 1
                },
                "sink": {
                    "type": "SqlDWSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:10"
                }
            },
            "inputs": [
              {
                "name": "<Storage Dataset>"
              }
            ],
            "outputs": [
              {
                "name": "<Data Warehouse Dataset>"
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
            "name": "Sample Copy",
            "description": "Copy Activity"
          }
        ],
        "start": "<Date YYYY-MM-DD>",
        "end": "<Date YYYY-MM-DD>",
        "isPaused": false
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, commencez par l’affichage :

- [Azure Data Factory rubriques d’apprentissage][].
- [Connecteur de magasin de données SQL azure][]. Il s’agit de la rubrique de référence principales pour l’utilisation de Azure Data Factory avec Azure SQL Data Warehouse.


Les rubriques suivantes fournissent des informations détaillées sur Azure Data Factory. Discuter de base de données SQL Azure ou HDInsight, mais les informations s’appliquent également à SQL Azure Data Warehouse.

- [Didacticiel : prise en main Azure Data Factory][] Il s’agit le didacticiel core pour le traitement des données avec Azure Data Factory. Dans ce didacticiel, vous allez créer votre première pipeline qui utilise HDInsight pour transformer et analyser les journaux web sur une base mensuelle. Remarque : il n’existe aucune activité de copie dans ce didacticiel.
- [Didacticiel : copier des données d’objets Blob Azure stockage de base de données SQL Azure][]. Dans ce didacticiel, vous créez un pipeline dans Azure Data Factory pour copier des données à partir d’Azure stockage Blob dans la base de données SQL Azure.

<!--Image references-->

<!--Article references-->
[Documentation AZCopy]: ../storage/storage-use-azcopy.md
[Connecteur de magasin de données SQL Azure]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[BCP]: sql-data-warehouse-load-with-bcp.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Créer un compte de stockage]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Data Factory]: sql-data-warehouse-get-started-load-with-azure-data-factory.md
[Prise en main Azure Data Factory (données usine éditeur)]: ../data-factory/data-factory-build-your-first-pipeline-using-editor.md
[Introduction aux données Azure usine]: ../data-factory/data-factory-introduction.md
[Load sample data into SQL Data Warehouse]: sql-data-warehouse-load-sample-databases.md
[Move data to and from Azure SQL Data Warehouse using Azure Data Factory]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[PolyBase]: sql-data-warehouse-get-started-load-with-polybase.md
[Didacticiel : Copier des données d’objets Blob Azure stockage de base de données SQL Azure]: ../data-factory/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[Didacticiel : Prise en main Azure Data Factory]: ../data-factory/data-factory-build-your-first-pipeline.md

<!--MSDN references-->

<!--Other Web references-->
[Rubriques d’apprentissage Data Factory Azure]: https://azure.microsoft.com/documentation/learning-paths/data-factory
[Portail Azure]: https://portal.azure.com
[Télécharger des exemples de données]: https://migrhoststorage.blob.core.windows.net/adfsample/FactInternetSales.csv
