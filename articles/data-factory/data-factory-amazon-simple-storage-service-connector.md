<properties 
    pageTitle="Déplacer des données d’Amazon Simple Storage Service à l’aide de Data Factory | Microsoft Azure" 
    description="Découvrez comment déplacer des données à partir d’Amazon Simple Storage Service (S3) à l’aide d’Azure Data Factory." 
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
    ms.date="10/24/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Déplacer des données à partir d’Amazon Simple Storage Service à l’aide d’usine de données Azure

Cet article explique comment vous pouvez utiliser l’activité de copie dans une usine de données Azure pour déplacer des données à partir d’Amazon Simple Storage Service (S3) vers une autre données stocker. Cet article s’appuie sur l’article [activités de déplacement de données](data-factory-data-movement-activities.md) qui présente une vue d’ensemble de déplacement de données et une liste de banques de données pris en charge source/récepteur avec une activité copie.  

Usine données prend actuellement en charge uniquement les données à partir d’Amazon S3 déplacement aux autres banques de données, mais ne pas pour le déplacement de données provenant d’autres banques de données à Amazon S3.

## <a name="required-permissions"></a>Autorisations requises

Pour copier des données à partir d’Amazon S3, vérifiez que vous avez obtenu autorisations ci-dessous :

- **S3:GetObject** et **s3:GetObjectVersion** pour des opérations d’objet S3 Amazon
- **S3:ListBucket** et **s3:ListAllMyBuckets** (utilisé dans l’Assistant copie uniquement) pour les opérations de peinture S3 Amazon 

Vous pouvez rechercher la liste complète des autorisations d’Amazon S3 minutieusement [Spécifier des autorisations dans une stratégie](http://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="copy-data-wizard"></a>Copier l’Assistant de données
Pour créer une opportunité qui copie les données à partir d’Amazon S3, le plus simple consiste à utiliser l’Assistant copie de données. Voir [didacticiel : créer une opportunité à l’aide de l’Assistant copie](data-factory-copy-data-wizard-tutorial.md) pour un bref aperçu sur la création d’un pipeline à l’aide de l’Assistant copie de données. 

L’exemple suivant fournit des exemples de définitions JSON que vous pouvez utiliser pour créer une opportunité à l’aide de [portail Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [PowerShell Azure](data-factory-copy-activity-tutorial-using-powershell.md). Il vous montre comment copier des données à partir d’Amazon S3 au stockage d’objets Blob Azure. Toutefois, les données peuvent être copiées à un des récepteurs indiqué [ici](data-factory-data-movement-activities.md#supported-data-stores).

## <a name="sample-copy-data-from-amazon-s3-to-azure-blob"></a>Exemple : Copier des données à partir d’Amazon S3 dans Blob Azure
Cet exemple montre comment copier des données à partir d’un S3 Amazon vers un stockage d’objets Blob Azure. Toutefois, les données peuvent être copiées **directement** à un des récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.  
 
L’exemple comporte les entités usine données suivantes :

- Un service de type [AwsAccessKey](#linked-service-properties)lié.
- Un service de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)lié.
- Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [AmazonS3](#dataset-type-properties).
- Un résultat [jeu de données](data-factory-create-datasets.md) de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- [Pipeline](data-factory-create-pipelines.md) avec une activité copie qui utilise [FileSystemSource](#copy-activity-type-properties) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L’exemple copie les données à partir d’Amazon S3 à un blob Azure chaque heure. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivantes les exemples. 

**Service S3 Amazon lié**

    {
        "name": "AmazonS3LinkedService",
        "properties": {
            "type": "AwsAccessKey",
            "typeProperties": {
                "accessKeyId": "<access key id>",
                "secretAccessKey": "<secret access key>"
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

**Jeu de données d’entrée S3 Amazon**

Définition de **« externes » : true** vous informe le service de données par défaut que le jeu de données externe à l’usine de données et n’est pas produit par une activité dans le factory de données. Définir cette propriété sur true sur un jeu de données d’entrée n’est pas produit par une activité dans le pipeline.

    {
        "name": "AmazonS3InputDataset",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "AmazonS3LinkedService",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
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
                "folderPath": "mycontainer/fromamazons3/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

Le pipeline contient une activité de copie qui est configuré pour utiliser les jeux de données d’entrée et de sortie et planifiée pour chaque heure. Dans le pipeline de définition de JSON, le type de **source** est défini sur **FileSystemSource** et **récepteur de** type est défini sur **BlobSink**. 
    
    {
        "name": "CopyAmazonS3ToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "FileSystemSource",
                            "recursive": true
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AmazonS3InputDataset"
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
                    "name": "AmazonS3ToBlob"
                }
            ],
            "start": "2014-08-08T18:00:00Z",
            "end": "2014-08-08T19:00:00Z"
        }
    }



## <a name="linked-service-properties"></a>Propriétés du service liées

Le tableau suivant fournit la description pour les éléments JSON spécifiques à Amazon S3 (**AwsAccessKey**) lié service.

| Propriété | Description | Valeurs autorisées | Obligatoire |
| -------- | ----------- | -------- | ------- |  
| accessKeyID | ID de la touche d’accès secrète. | chaîne | Oui |
| secretAccessKey | Touche d’accès secrète lui-même. | Chaîne secrète cryptée | Oui | 


## <a name="dataset-type-properties"></a>Propriétés du type de jeu de données

Pour une liste complète des sections et les propriétés disponibles pour la définition des groupes de données, voir l’article [Création de groupes de données](data-factory-create-datasets.md) . Sections telles que la structure, la disponibilité et stratégie sont identiques pour tous les types de jeu de données (SQL Azure, blob Azure, table Azure, etc..).

La section **typeProperties** est différente pour chaque type de jeu de données et fournit des informations sur l’emplacement des données dans le magasin de données. La section typeProperties pour le jeu de données de type **AmazonS3** (y compris S3 Amazon dataset) comporte les propriétés suivantes

| Propriété | Description | Valeurs autorisées | Obligatoire |
| -------- | ----------- | -------- | ------ | 
| bucketName | Le nom de peinture S3. | Chaîne | Oui |
| clé | La clé de l’objet S3. | Chaîne | N° | 
| préfixe | Préfixe de la clé de l’objet S3. Objets dont les clés commencent par ce préfixe sont sélectionnés. S’applique uniquement lorsque la clé est vide. | Chaîne | N° | 
| Version | La version d’objet S3 si le contrôle de version S3 est activée. | Chaîne | N° |  
| mettre en forme | Les types de formats suivants sont pris en charge : **format du texte**, **AvroFormat**, **JsonFormat**, **OrcFormat**, **ParquetFormat**. Définissez **la propriété sous format** à un de ces valeurs. Voir [Format du texte spécifiant](#specifying-textformat), [AvroFormat spécifiant](#specifying-avroformat), [JsonFormat spécifiant](#specifying-jsonformat), [OrcFormat spécifiant](#specifying-orcformat), [ParquetFormat spécifiant](#specifying-parquetformat) sections et pour plus d’informations. Si vous voulez copier des fichiers en tant que-est entre banques basée sur le fichier (copie binaire), vous pouvez ignorer la section format dans des définitions de jeu de données d’entrée et de sortie.| N°
| compression | Spécifier le type et le niveau de compression pour les données. Types pris en charge sont : **GZip**, **Deflate**et **BZip2** et les niveaux de prise en charge : **Optimal** et **plus rapide**. Pour l’instant, les paramètres de compression ne sont pas prises en charge des données dans **AvroFormat** ou **OrcFormat**. Pour plus d’informations, reportez-vous à la section [prise en charge de la Compression](#compression-support) .  | N° |

> [AZURE.NOTE] bucketName + touche indique l’emplacement de l’objet S3 où pot est le conteneur racine pour les objets S3 et clé est le chemin d’accès complet à objet S3.

### <a name="sample-dataset-with-prefix"></a>Exemple de dataset avec préfixe

    {
        "name": "dataset-s3",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "link- testS3",
            "typeProperties": {
                "prefix": "testFolder/test",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }

### <a name="sample-data-set-with-version"></a>Jeu de données d’exemple (avec la version)

    {
        "name": "dataset-s3",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "link- testS3",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "version": "WBeMIxQkJczm0CJajYkHf0_k6LhBmkcL",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }


### <a name="dynamic-paths-for-s3"></a>Chemins d’accès dynamiques pour S3

Dans l’échantillon, nous utilisons des valeurs fixes pour les propriétés clé et bucketName dans le groupe de données Amazon S3. 

    "key": "testFolder/test.orc",
    "bucketName": "testbucket",

Vous pouvez avoir Data Factory calculer la clé et bucketName dynamiquement lors de l’exécution en utilisant des variables système tels que SliceStart.

    "key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
    "bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"

Vous pouvez effectuer les mêmes pour la propriété préfixe d’un dataset Amazon S3. Pour obtenir la liste des variables et des fonctions prises en charge, voir [Data Factory fonctions et les variables système](data-factory-functions-variables.md) . 


[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]
[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]


## <a name="copy-activity-type-properties"></a>Copier les propriétés de type d’activité

Pour une liste complète des sections et les propriétés disponibles pour la définition des activités, voir l’article [Création Pipelines](data-factory-create-pipelines.md) . Propriétés telles que nom, description, entrée et sortie des tables et les stratégies sont disponibles pour tous les types d’activités. 

Propriétés disponibles dans la section **typeProperties** de l’activité dépendent quant à eux de chaque type d’activité. Activité de copie, ils varient selon les types de sources et les récepteurs.

Lors de la source dans l’activité de copie est de type **FileSystemSource** (y compris S3 Amazon), les propriétés suivantes sont disponibles dans la section typeProperties :

| Propriété | Description | Valeurs autorisées | Obligatoire |
| -------- | ----------- | -------------- | -------- | 
| récursives | Spécifie la manière récursive S3 de répertorier les objets dans le répertoire. | Vrai/faux | N° | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Performances et réglage  
Voir [Guide d’optimisation et des performances d’activité de copie](data-factory-copy-activity-performance.md) pour en savoir plus sur les principaux facteurs impact sur les performances de déplacement de données (copie activité) dans Azure Data Factory et de différentes manières d’optimiser.

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants : 
- [Activité de copie didacticiel](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) pour obtenir des instructions étape par étape pour la création d’un pipeline avec une activité de copie. 
