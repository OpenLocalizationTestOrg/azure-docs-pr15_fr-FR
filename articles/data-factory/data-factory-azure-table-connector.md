<properties 
    pageTitle="Déplacer des données vers/à partir de la Table Azure | Microsoft Azure" 
    description="Découvrez comment déplacer des données vers ou à partir d’Azure Table Storage à l’aide d’Azure Data Factory." 
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
    ms.date="09/13/2016" 
    ms.author="jingwang"/>

# <a name="move-data-to-and-from-azure-table-using-azure-data-factory"></a>Déplacer des données vers et depuis la Table Azure à l’aide d’usine de données Azure

Cet article explique comment vous pouvez utiliser l’activité de copie dans une usine de données Azure pour déplacer des données vers ou à partir d’Azure Table à partir de/vers une autre banque de données. Cet article s’appuie sur l’article [activités de déplacement des données](data-factory-data-movement-activities.md) , qui présente une vue d’ensemble des combinaisons de magasin de données prises en charge et de déplacement de données avec une activité copie.

## <a name="copy-data-wizard"></a>Copier l’Assistant de données
Pour créer une opportunité qui copie les données vers ou à partir de stockage de Table Azure, le plus simple consiste à utiliser l’Assistant copie de données. Voir [didacticiel : créer une opportunité à l’aide de l’Assistant copie](data-factory-copy-data-wizard-tutorial.md) pour un bref aperçu sur la création d’un pipeline à l’aide de l’Assistant copie de données. 

Les exemples suivants fournissent des exemples de définitions JSON que vous pouvez utiliser pour créer une opportunité à l’aide de [portail Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [PowerShell Azure](data-factory-copy-activity-tutorial-using-powershell.md). Ils montrent comment copier des données vers et depuis le stockage de Table Azure et base de données Azure Blob. Toutefois, les données peuvent être copiées **directement** à partir des sources à un des récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.

## <a name="sample-copy-data-from-azure-table-to-azure-blob"></a>Exemple : Copier des données de Table Azure vers Blob Azure

L’exemple suivant montre :

1.  Un service lié du type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) (utilisé pour la table et blob).
2.  Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [Azure](#azure-table-dataset-type-properties).
3.  Un résultat [jeu de données](data-factory-create-datasets.md) de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties). 
3.  [Pipeline](data-factory-create-pipelines.md) avec une activité copie qui utilise [AzureTableSource](#azure-table-copy-activity-type-properties) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties). 

L’exemple de copie des données appartenant à la partition par défaut dans une Table Azure dans un blob chaque heure. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivantes les exemples.

**Service de stockage Azure lié :**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

Azure Data Factory prend en charge les deux types de services de stockage Azure liées : **AzureStorage** et **AzureStorageSas**. Pour la première partie, vous spécifiez la chaîne de connexion qui inclut la clé de compte et pour une version ultérieure, vous spécifiez l’Uri partagés accès Signature (sa). Pour plus d’informations, reportez-vous à la section [Services liés](#linked-services) .  

**Azure dataset d’entrée de Table :**

L’exemple suppose que vous avez créé une table « MyTable » dans la Table Azure.
 
Définition de « externes » : « true » vous informe le service de données par défaut que le jeu de données externe à l’usine de données et n’est pas produit par une activité dans le factory de données.

    {
      "name": "AzureTableInput",
      "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "tableName": "MyTable"
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

**Jeu de données de sortie Blob Azure :**

Les données sont écrites dans un nouveau blob toutes les heures (fréquence : heure, l’intervalle : 1). Le chemin du dossier pour le blob est évalué dynamiquement en fonction de l’heure de début de la section en cours de traitement. Le chemin du dossier utilise l’année, mois, jour et composants heures de l’heure de début. 

    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": "\t",
            "rowDelimiter": "\n"
          }
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

**Pipeline avec l’activité de copie :**

Le pipeline contient une activité de copie qui est configuré pour utiliser les jeux de données d’entrée et de sortie et planifiée pour chaque heure. Dans le pipeline de définition de JSON, le type de **source** est défini sur **AzureTableSource** et **récepteur de** type est défini sur **BlobSink**. La requête SQL spécifiée avec propriété **AzureTableSourceQuery** sélectionne les données à partir de la partition par défaut chaque heure à copier.

    {  
        "name":"SamplePipeline",
        "properties":{  
            "start":"2014-06-01T18:00:00",
            "end":"2014-06-01T19:00:00",
            "description":"pipeline for copy activity",
            "activities":[  
                {
                    "name": "AzureTabletoBlob",
                    "description": "copy activity",
                    "type": "Copy",
                    "inputs": [
                        {
                            "name": "AzureTableInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "AzureTableSource",
                            "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },              
                    "policy": {
                        "concurrency": 1,
                        "executionPriorityOrder": "OldestFirst",
                        "retry": 0,
                        "timeout": "01:00:00"
                    }
                }
             ]  
        }
    }

## <a name="sample-copy-data-from-azure-blob-to-azure-table"></a>Exemple : Copier des données d’objets Blob Azure à Table Azure

L’exemple suivant montre :

1.  Un service lié du type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) (utilisé pour la table et blob)
3.  Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  Un résultat [jeu de données](data-factory-create-datasets.md) de type [Azure](#azure-table-dataset-type-properties). 
4.  [Pipeline](data-factory-create-pipelines.md) avec une activité copie qui utilise [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) et [AzureTableSink](#azure-table-copy-activity-type-properties). 


Les copies exemple série chronologique des données à partir d’un Azure blob vers un Azure table toutes les heures. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivantes les exemples.

**Service de stockage Azure (par Table Azure et Blob) lié :**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

Azure Data Factory prend en charge les deux types de services de stockage Azure lié : **AzureStorage** et **AzureStorageSas**. Pour la première partie, vous spécifiez la chaîne de connexion qui inclut la clé de compte et pour une version ultérieure, vous spécifiez l’Uri partagés accès Signature (sa). Pour plus d’informations, reportez-vous à la section [Services liés](#linked-services) . 

**Dataset d’entrée des objets Blob Azure :**

Données sont provenant d’un objet blob de nouveau toutes les heures (fréquence : heure, l’intervalle : 1). Le nom du fichier et le chemin dossier pour l’objets blob sont évaluées dynamiquement en fonction de l’heure de début de la section en cours de traitement. Le chemin du dossier utilise année, mois et partie jour de l’heure de début et nom de fichier utilise la partie heure de l’heure de début. « externes » : paramètre « true » vous informe le service de données par défaut que le jeu de données externe à l’usine de données et n’est pas produit par une activité dans le factory de données.
    
    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
          "fileName": "{Hour}.csv",
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
          ],
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

**Jeu de données de sortie de Table Azure :**

L’exemple de copie des données dans une table nommée « MyTable » dans la Table Azure. Créer une table Azure avec le même nombre de colonnes comme prévu le fichier CSV Blob contenir. Nouvelles lignes sont ajoutées à la table chaque heure. 

    {
      "name": "AzureTableOutput",
      "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "tableName": "MyOutputTable"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

**Pipeline avec l’activité de copie :**

Le pipeline contient une activité de copie qui est configuré pour utiliser les jeux de données d’entrée et de sortie et planifiée pour chaque heure. Dans le pipeline de définition de JSON, le type de **source** est défini sur **BlobSource** et **récepteur de** type est défini sur **AzureTableSink**. 


    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
          {
            "name": "AzureBlobtoTable",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureTableOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "AzureTableSink",
                "writeBatchSize": 100,
                "writeBatchTimeout": "01:00:00"
              }
            },
            "scheduler": {
              "frequency": "Hour",
              "interval": 1
            },                      
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "OldestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
          ]
       }
    }

## <a name="linked-services"></a>Services liés
Il existe deux types de services liées, que vous pouvez utiliser pour créer un lien à une usine de données Azure un stockage d’objets blob Azure. Ils sont : **AzureStorage** lié services et **AzureStorageSas** lié. Le service de stockage Azure lié fournit le factory de données avec accès global au stockage Azure. Alors que le Azure stockage associations de sécurité (partagés accès Signature) lié service fournit le factory de données avec l’accès restreint/limitées dans le temps pour le stockage Azure. Il n’existe pas de différences entre ces deux services liées. Choisissez le service lié adapté à vos besoins. Les sections suivantes fournissent des informations plus détaillées sur ces deux services liées.

[AZURE.INCLUDE [data-factory-azure-storage-linked-services](../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="azure-table-dataset-type-properties"></a>Propriétés du type de jeu de données de Table Azure

Pour une liste complète des sections et les propriétés disponibles pour la définition des groupes de données, voir l’article [Création de groupes de données](data-factory-create-datasets.md) . Sections telles que la structure, la disponibilité et stratégie d’un dataset JSON sont identiques pour tous les types de jeu de données (SQL Azure, blob Azure, table Azure, etc..).

La section typeProperties est différente pour chaque type de jeu de données et fournit des informations sur l’emplacement des données dans le magasin de données. La section **typeProperties** pour le jeu de données de type **Azure** possède les propriétés suivantes.

| Propriété | Description | Obligatoire |
| -------- | ----------- | -------- |
| tableName | Nom de la table dans l’instance de base de données Azure Table liée service fait référence à. | Oui. Lorsqu’un tableName est spécifié sans un azureTableSourceQuery, tous les enregistrements de la table sont copiées vers la destination. Si un azureTableSourceQuery est également spécifiée, les enregistrements de la table qui répond à la requête sont copiées vers la destination. |

### <a name="schema-by-data-factory"></a>Schéma de données par défaut
Pour les banques de données sans schéma comme Table Azure, le service de données usine déduit le schéma d’une des façons suivantes :

1.  Si vous spécifiez la structure des données à l’aide de la propriété **structure** dans la définition du jeu de données, le service de données usine respecte cette structure que le schéma. Dans ce cas, si une ligne ne contient pas une valeur pour une colonne, une valeur null est fournie pour lui.
2. Si vous ne spécifiez pas la structure des données à l’aide de la propriété **structure** dans la définition du jeu de données, Data Factory déduit le schéma à l’aide de la première ligne dans les données. Dans ce cas, si la première ligne ne contient pas le schéma complet, certaines colonnes sont ignorés dans le résultat de l’opération de copie.

Par conséquent, pour les sources de données sans schéma, la meilleure solution consiste à définir la structure de données à l’aide de la propriété **structure** .

## <a name="azure-table-copy-activity-type-properties"></a>Propriétés de type tableau copie activité Azure

Pour une liste complète des sections et les propriétés disponibles pour la définition des activités, voir l’article [Création Pipelines](data-factory-create-pipelines.md) . Propriétés telles que nom, description, entrée et sortie des jeux de données et stratégies sont disponibles pour tous les types d’activités. 

Propriétés disponibles dans la section typeProperties de l’activité dépendent quant à eux de chaque type d’activité. Activité de copie, ils varient selon les types de sources et les récepteurs.

**AzureTableSource** prend en charge les propriétés suivantes dans la section typeProperties :

Propriété | Description | Valeurs autorisées | Obligatoire
-------- | ----------- | -------------- | -------- 
azureTableSourceQuery | Utiliser la requête personnalisée pour lire les données. | Chaîne de requête de table Azure. Consultez des exemples de la section suivante. | Non. Lorsqu’un tableName est spécifié sans un azureTableSourceQuery, tous les enregistrements de la table sont copiées vers la destination. Si un azureTableSourceQuery est également spécifiée, les enregistrements de la table qui répond à la requête sont copiées vers la destination.
azureTableSourceIgnoreTableNotFound | Indiquer si a l’exception de table n’existe pas. | VRAI<br/>FAUX | N° |

### <a name="azuretablesourcequery-examples"></a>exemples d’azureTableSourceQuery

Si la colonne de Table Azure est de type chaîne : 

    azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"

Si la colonne de Table Azure est de type date/heure : 

    "azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"


**AzureTableSink** prend en charge les propriétés suivantes dans la section typeProperties :


Propriété | Description | Valeurs autorisées | Obligatoire  
-------- | ----------- | -------------- | -------- 
azureTableDefaultPartitionKeyValue | Valeur par défaut partition clé qui peut être utilisée par le récepteur. | Valeur de chaîne. | N° 
azureTablePartitionKeyName | Spécifiez le nom de la colonne dont les valeurs sont utilisées comme partition clés. Le cas contraire, AzureTableDefaultPartitionKeyValue est utilisé comme clé de partition. | Un nom de colonne. | N° |
azureTableRowKeyName | Spécifiez le nom de la colonne dont les valeurs de colonne sont utilisées comme clé de ligne. Le cas contraire, utilisez un GUID pour chaque ligne. | Un nom de colonne. | N°  
azureTableInsertType | Le mode à insérer des données dans la table Azure.<br/><br/>Cette propriété contrôle si les lignes existantes dans la table de sortie avec les clés de partition et des lignes correspondantes ont leurs valeurs remplacer ou à fusionner. <br/><br/>Pour en savoir plus sur le fonctionnement de ces paramètres (fusionner et remplacer), consultez rubriques [Insertion ou l’entité de fusion](https://msdn.microsoft.com/library/azure/hh452241.aspx) et [Insérer ou remplacer une entité](https://msdn.microsoft.com/library/azure/hh452242.aspx) . <br/><br> Ce paramètre s’applique au niveau de la ligne, pas le niveau de table et aucune de ces options supprime les lignes dans la table de sortie qui n’existent pas dans l’entrée. | Fusionner (par défaut)<br/>remplacer | N° 
writeBatchSize | Insère des données dans la table Azure lorsque la writeBatchSize ou writeBatchTimeout est atteint. | Nombre entier (nombre de lignes)| Non (par défaut : 10000) 
writeBatchTimeout | Insère des données dans la table Azure lorsque la writeBatchSize ou writeBatchTimeout est atteint. | TimeSpan<br/><br/>Exemple : « 00 : 20:00 » (20 minutes) | Non (sec 90 la valeur par défaut pour le délai d’expiration de stockage client par défaut)

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName
Mapper une colonne source à une colonne de destination à l’aide de la propriété JSON translator avant de pouvoir utiliser la colonne de destination en tant que l’azureTablePartitionKeyName.

Dans l’exemple suivant, la colonne source DivisionID est mappé à la colonne cible : DivisionID.  

    "translator": {
        "type": "TabularTranslator",
        "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
    } 

Le DivisionID est spécifiée comme clé de partition. 

    "sink": {
        "type": "AzureTableSink",
        "azureTablePartitionKeyName": "DivisionID",
        "writeBatchSize": 100,
        "writeBatchTimeout": "01:00:00"
    }


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-azure-table"></a>Mappage du type de Table Azure

Comme indiqué dans l’article [activités de déplacement des données](data-factory-data-movement-activities.md) , activité de copie effectue les conversions de type automatique à partir de types de sources pour recevoir les types de l’approche en deux étapes suivantes.

1. Conversion de types de sources native en type .NET
2. Conversion de type .NET en type récepteur natif

Lors du déplacement de données et Table Azure, le suivant [mappages définis par Table Azure service](https://msdn.microsoft.com/library/azure/dd179338.aspx) servent à partir d’Azure Table OData types type .NET et vice versa. 

| Type de données OData | Type .NET | Plus d’informations |
| --------------- | --------- | ------- |
| Edm.Binary | Byte] | Un tableau d’octets jusqu'à 64 Ko. |
| Edm.Boolean | bool | Valeur booléenne. |
| Edm.DateTime | Date/heure | Une valeur de 64 bits exprimée en tant que le temps universel coordonné (UTC). La plage de date/heure pris en charge commence à partir de 12:00 janvier 1 année, minuit 1601 (C.E.), AU FORMAT UTC. La plage se termine à 31 décembre 9999. |
| Edm.Double | Double | Valeur à virgule flottante 64 bits. |
| Edm.Guid | GUID | Un identificateur unique global 128 bits. |
| Edm.Int32 | Int32 ou int | Un nombre entier 32 bits. |
| Edm.Int64 | Int64 ou long | Un nombre entier 64 bits. |
| Edm.String | Chaîne | Une valeur encodés UTF-16. Valeurs de chaîne peuvent être jusqu'à 64 Ko. |

### <a name="type-conversion-sample"></a>Exemples de Conversion de type

L’exemple suivant est pour copier les données à partir d’un Blob Azure Azure table avec les conversions de types. 

Supposons que le jeu de données Blob est au format CSV et contient trois colonnes. Un d'entre eux est une colonne datetime avec un format de date/heure personnalisés à l’aide de noms Français abrégés pour le jour de la semaine. 

Définir le jeu de données Source Blob comme suit ainsi que des définitions de type pour les colonnes.
    
    {
        "name": " AzureBlobInput",
        "properties":
        {
             "structure": 
              [
                    { "name": "userid", "type": "Int64"},
                    { "name": "name", "type": "String"},
                    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
              ],
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/myfolder",
                "fileName":"myfile.csv",
                "format":
                {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "external": true,
            "availability":
            {
                "frequency": "Hour",
                "interval": 1,
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

Étant donné le mappage de type de type Azure Table OData à type .NET, vous définissez la table dans la Table Azure par le schéma suivant. 

**Schéma de Table Azure :**

Nom de colonne | Type
----------- | --------
ID d’utilisateur | Edm.Int64
nom | Edm.String 
LastLoginDate | Edm.DateTime

Ensuite, définissez le jeu de données de Table Azure comme suit. Vous n’avez pas besoin spécifier la section « structure » avec les informations de type dans la mesure où les informations de type sont déjà spécifiées dans le magasin de données sous-jacentes.

    {
      "name": "AzureTableOutput",
      "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "tableName": "MyOutputTable"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

Dans ce cas, Data Factory automatiquement conversions de types, y compris le champ date/heure au format de date/heure personnalisés à l’aide de la culture « fr-fr » lors du déplacement de données à partir d’objets Blob Azure table.



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>Performances et réglage  
Pour en savoir plus sur les principaux facteurs impact sur les performances de déplacement de données (copie activité) dans Azure Data Factory et de différentes manières d’optimiser, voir [Guide d’optimisation et des performances d’activité de copie](data-factory-copy-activity-performance.md).







