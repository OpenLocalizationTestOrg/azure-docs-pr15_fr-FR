<properties 
    pageTitle="Déplacer des données d’Amazon Redshift à l’aide de Data Factory | Microsoft Azure" 
    description="Découvrez comment déplacer des données d’Amazon Redshift à l’aide d’Azure Data Factory." 
    services="data-factory" 
    documentationCenter="" 
    authors="linda33wj" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/23/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Déplacer des données à partir d’Amazon Redshift à l’aide d’usine de données Azure

Cet article explique comment vous pouvez utiliser l’activité de copie dans une usine de données Azure pour déplacer des données à partir d’Amazon Redshift vers une autre données stocker. Cet article s’appuie sur l’article [activités de déplacement des données](data-factory-data-movement-activities.md) , qui présente une vue d’ensemble de déplacement de données avec activité de copie et une liste source/récepteur de banques de données.  

Usine données prend actuellement en charge uniquement les données à partir d’Amazon Redshift déplacement aux autres banques de données, mais ne pas de déplacement des données provenant d’autres banques de données vers Redshift Amazon.

## <a name="prerequisites"></a>Conditions préalables

- Si vous déplacez des données dans un magasin de données locale, accorder passerelle de gestion des données (utilisez l’adresse IP de l’ordinateur) l’accès à cluster Redshift Amazon. Pour obtenir des instructions, voir [Autoriser l’accès au cluster](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) . 
- Si vous déplacez des données dans un magasin de données Azure, voir [Plages d’adresses IP Azure données centre](https://www.microsoft.com/download/details.aspx?id=41653) pour calculer les plages d’adresses IP (y compris les plages SQL) utilisés par les centres de données Microsoft Azure. 

## <a name="copy-data-wizard"></a>Copier l’Assistant de données
Pour créer une opportunité qui copie les données à partir d’Amazon Redshift, le plus simple consiste à utiliser l’Assistant copie de données. Voir [didacticiel : créer une opportunité à l’aide de l’Assistant copie](data-factory-copy-data-wizard-tutorial.md) pour un bref aperçu sur la création d’un pipeline à l’aide de l’Assistant copie de données. 

L’exemple suivant fournit des exemples de définitions JSON que vous pouvez utiliser pour créer une opportunité à l’aide de [portail Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [PowerShell Azure](data-factory-copy-activity-tutorial-using-powershell.md). Il vous montre comment copier des données à partir d’Amazon Redshift au stockage d’objets Blob Azure. Toutefois, les données peuvent être copiées à un des récepteurs indiqué [ici](data-factory-data-movement-activities.md#supported-data-stores).

## <a name="sample-copy-data-from-amazon-redshift-to-azure-blob"></a>Exemple : Copier des données à partir d’Amazon Redshift vers Blob Azure
Cet exemple montre comment copier des données à partir d’une base de données Redshift Amazon vers un stockage d’objets Blob Azure. Toutefois, les données peuvent être copiées **directement** à un des récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.  
 
L’exemple comporte les entités usine données suivantes :

- Un service de type [AmazonRedshift](#linked-service-properties)lié.
- Un service de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)lié.
- Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [RelationalTable](#dataset-type-properties).
- Un résultat [jeu de données](data-factory-create-datasets.md) de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- [Pipeline](data-factory-create-pipelines.md) avec une activité copie qui utilise [RelationalSource](#copy-activity-type-properties) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L’exemple copie les données à partir d’un résultat de la requête dans Redshift Amazon dans un blob chaque heure. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivantes les exemples. 

**Service Redshift Amazon lié**

    {
        "name": "AmazonRedshiftLinkedService",
        "properties":
        {
            "type": "AmazonRedshift",
            "typeProperties":
            {
                "server": "< The IP address or host name of the Amazon Redshift server >",
                "port": <The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>,
                "database": "<The database name of the Amazon Redshift database>",
                "username": "<username>",
                "password": "<password>"
            }
        }
    }


**Service de stockage lié Azure**

    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Jeu de données d’entrée Redshift Amazon**

Définition de **« externes » : true** vous informe le service de données par défaut que le jeu de données externe à l’usine de données et n’est pas produit par une activité dans le factory de données. Définir cette propriété sur true sur un jeu de données d’entrée n’est pas produit par une activité dans le pipeline.

    {
        "name": "AmazonRedshiftInputDataset",
        "properties": {
            "type": "RelationalTable",
            "linkedServiceName": "AmazonRedshiftLinkedService",
            "typeProperties": {
                "tableName": "<Table name>"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }


**Jeu de données de sortie Blob Azure**

Les données sont écrites dans un nouveau blob toutes les heures (fréquence : heure, l’intervalle : 1). Le chemin du dossier pour le blob est évalué dynamiquement en fonction de l’heure de début de la section en cours de traitement. Le chemin du dossier utilise l’année, mois, jour et composants heures de l’heure de début.

    {
        "name": "AzureBlobOutputDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
                },
                "partitionedBy": [
                    {
                        "name": "Year",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "yyyy"
                        }
                    },
                    {
                        "name": "Month",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "MM"
                        }
                    },
                    {
                        "name": "Day",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "dd"
                        }
                    },
                    {
                        "name": "Hour",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "HH"
                        }
                    }
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }



**Pipeline avec une activité copie**

Le pipeline contient une activité de copie qui est configuré pour utiliser les jeux de données d’entrée et de sortie et planifiée pour chaque heure. Dans le pipeline de définition de JSON, le type de **source** est défini sur **RelationalSource** et **récepteur de** type est défini sur **BlobSink**. La requête SQL spécifiée pour la propriété de **requête** sélectionne les données dans la dernière heure à copier.
    
    {
        "name": "CopyAmazonRedshiftToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AmazonRedshiftInputDataset"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutputDataSet"
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
                    "name": "AmazonRedshiftToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }



## <a name="linked-service-properties"></a>Propriétés du service liées

Le tableau suivant fournit la description pour les éléments JSON spécifiques au service Redshift Amazon lié.

| Propriété | Description | Obligatoire |
| -------- | ----------- | -------- | 
| type | La propriété doit être définie : **AmazonRedshift**. | Oui |
| serveur | Adresse IP ou hôte nom du serveur Redshift Amazon. | Oui |
| port | Le nombre de ports TCP que le serveur Redshift Amazon utilise pour écouter les connexions client. | Non, valeur par défaut : 5439 |
| base de données | Nom de la base de données Redshift Amazon. | Oui |
| nom d’utilisateur | Nom d’utilisateur qui a accès à la base de données.| Oui |
| mot de passe | Mot de passe pour le compte d’utilisateur.| Oui |


## <a name="dataset-type-properties"></a>Propriétés du type de jeu de données

Pour une liste complète des sections et les propriétés disponibles pour la définition des groupes de données, voir l’article [Création de groupes de données](data-factory-create-datasets.md) . Sections telles que la structure, la disponibilité et stratégie sont identiques pour tous les types de jeu de données (SQL Azure, blob Azure, table Azure, etc..).

La section **typeProperties** est différente pour chaque type de jeu de données et fournit des informations sur l’emplacement des données dans le magasin de données. La section typeProperties pour le jeu de données de type **RelationalTable** (y compris Redshift Amazon dataset) comporte les propriétés suivantes

| Propriété | Description | Obligatoire |
| -------- | ----------- | -------- |
| tableName | Nom de la table dans la base de données Redshift Amazon lié service fait référence à. | N° (si la **requête** de **RelationalSource** est définie) | 

## <a name="copy-activity-type-properties"></a>Copier les propriétés de type d’activité

Pour une liste complète des sections et les propriétés disponibles pour la définition des activités, voir l’article [Création Pipelines](data-factory-create-pipelines.md) . Propriétés telles que nom, description, entrée et sortie des tables et les stratégies sont disponibles pour tous les types d’activités. 

Propriétés disponibles dans la section **typeProperties** de l’activité dépendent quant à eux de chaque type d’activité. Activité de copie, ils varient selon les types de sources et les récepteurs.

Lors de la source d’activité copie est de type **RelationalSource** (y compris Redshift Amazon) les propriétés suivantes sont disponibles dans la section typeProperties :

| Propriété | Description | Valeurs autorisées | Obligatoire |
| -------- | ----------- | -------------- | -------- |
| requête | Utiliser la requête personnalisée pour lire les données. | Chaîne de requête SQL. Par exemple : sélectionnez * from MyTable. | N° (si **tableName** du **jeu de données** n’est spécifié) | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-amazon-redshift"></a>Mappage du type pour Redshift Amazon

Comme indiqué dans l’article [activités de déplacement des données](data-factory-data-movement-activities.md) , activité de copie exécute des conversions de type automatique à partir de types de sources pour recevoir les types de l’approche en deux étapes suivantes :

1. Conversion de types de sources native en type .NET
2. Conversion de type .NET en type récepteur natif

Lors du déplacement de données à Amazon Redshift, les mappages suivants seront utilisées à partir de types de Redshift Amazon types .NET.

Type de Redshift Amazon | Type basé sur .NET
-------------------- | ---------------
SMALLINT | Int16
NOMBRE ENTIER | Int32
BIGINT | Int64
DECIMAL | Decimal
RÉEL | Unique
DOUBLE PRÉCISION | Double
VALEUR BOOLÉENNE | Chaîne
CAR | Chaîne
VARCHAR | Chaîne
DATE | Date/heure
HORODATAGE | Date/heure
TEXTE | Chaîne



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Performances et réglage  
Voir [Guide d’optimisation et des performances d’activité de copie](data-factory-copy-activity-performance.md) pour en savoir plus sur les principaux facteurs impact sur les performances de déplacement de données (copie activité) dans Azure Data Factory et de différentes manières d’optimiser.

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants : 
- [Activité de copie didacticiel](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) pour obtenir des instructions étape par étape pour la création d’un pipeline avec une activité de copie. 