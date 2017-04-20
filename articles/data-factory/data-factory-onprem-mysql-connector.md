<properties 
    pageTitle="Déplacer des données de MySQL | Données Azure usine" 
    description="Découvrez comment déplacer des données à partir de la base de données MySQL avec Azure Data Factory." 
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
    ms.date="09/20/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-mysql-using-azure-data-factory"></a>Déplacer des données à partir de MySQL à l’aide d’usine de données Azure

Cet article explique comment vous pouvez utiliser l’activité de copie dans une usine de données Azure pour déplacer des données de MySQL vers une autre données stocker. Cet article s’appuie sur l’article [activités de déplacement des données](data-factory-data-movement-activities.md) , qui présente une vue d’ensemble de déplacement de données avec l’activité de copie et les combinaisons de magasin de données prises en charge.

Un service de données usine prend en charge la connexion aux sources de MySQL en local à l’aide de la passerelle de gestion des données. Consultez l’article de [déplacement des données entre des emplacements de locaux et cloud](data-factory-move-data-between-onprem-and-cloud.md) pour en savoir plus sur la passerelle de gestion des données et des instructions détaillées sur la configuration de la passerelle. 

> [AZURE.NOTE] Passerelle est requise même si la base de données MySQL est hébergé dans une Azure IaaS machine virtuelle (). Vous pouvez installer la passerelle sur le même ordinateur virtuel comme magasin de données ou sur un autre ordinateur virtuel dans la mesure où la passerelle peut se connecter à la base de données.  

Usine données prend actuellement en charge uniquement les données de MySQL déplacement aux autres banques de données, mais ne pas permettant de supprimer des données provenant d’autres banques de données MySQL.

## <a name="installation"></a>Installation 
Pour la passerelle de gestion des données pour vous connecter à la base de données MySQL, vous devez installer le [Connecteur/Net MySQL 6.6.5 pour Microsoft Windows](http://go.microsoft.com/fwlink/?LinkId=278885) sur le même système que la passerelle de gestion des données.

> [AZURE.NOTE] Voir [passerelle résoudre des problèmes](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) pour obtenir des conseils sur la résolution des problèmes de connexion/passerelle problèmes correspondants. 

## <a name="copy-data-wizard"></a>Copier l’Assistant de données
Pour créer une opportunité qui copie les données à partir d’une base de données MySQL à une des banques de données pris en charge récepteur, le plus simple consiste à utiliser l’Assistant copie de données. Voir [didacticiel : créer une opportunité à l’aide de l’Assistant copie](data-factory-copy-data-wizard-tutorial.md) pour un bref aperçu sur la création d’un pipeline à l’aide de l’Assistant copie de données. 

L’exemple suivant fournit des exemples de définitions JSON que vous pouvez utiliser pour créer une opportunité. Pour une procédure pas à pas complète des instructions étape par étape, consultez l’article de [déplacement des données entre des emplacements de locaux et cloud](data-factory-move-data-between-onprem-and-cloud.md) . Données peuvent être copiées sur un des récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.   

## <a name="sample-copy-data-from-mysql-to-azure-blob"></a>Exemple : Copier des données de MySQL vers Blob Azure
Cet exemple montre comment copier des données à partir d’une base de données MySQL local vers un stockage d’objets Blob Azure. Toutefois, les données peuvent être copiées **directement** à un des récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.  

> [AZURE.IMPORTANT] Cet exemple fournit extraits JSON. Il n’inclut pas les instructions détaillées pour créer le factory de données. Consultez l’article de [déplacement des données entre des emplacements de locaux et cloud](data-factory-move-data-between-onprem-and-cloud.md) pour obtenir des instructions étape par étape. 
 
L’exemple comporte les entités usine données suivantes :

1.  Un service de type [OnPremisesMySql](data-factory-onprem-mysql-connector.md#mysql-linked-service-properties)lié.
2.  Un service de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)lié.
3.  Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [RelationalTable](data-factory-onprem-mysql-connector.md#mysql-dataset-type-properties).
4.  Un résultat [jeu de données](data-factory-create-datasets.md) de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  [Pipeline](data-factory-create-pipelines.md) avec une activité copie qui utilise [RelationalSource](data-factory-onprem-mysql-connector.md#mysql-copy-activity-type-properties) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L’exemple copie les données à partir des résultats d’une requête de base de données MySQL dans un blob toutes les heures. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivantes les exemples. 

D’abord, configurez la passerelle de gestion des données. Les instructions sont dans l’article de [déplacement des données entre des emplacements de locaux et cloud](data-factory-move-data-between-onprem-and-cloud.md) . 

**Service MySQL lié**

    {
      "name": "OnPremMySqlLinkedService",
      "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
          "server": "<server name>",
          "database": "<database name>",
          "schema": "<schema name>",
          "authenticationType": "<authentication type>",
          "userName": "<user name>",
          "password": "<password>",
          "gatewayName": "<gateway>"
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

**Jeu de données MySQL d’entrée**

L’exemple suppose que vous avez créé une table « MyTable » dans MySQL et il contient une colonne nommée « timestampcolumn » pour les données de série d’heure.

Définition de « externes » : « true » vous informe le service de données usine que la table en dehors de l’usine de données et n’est pas produite par une activité dans le factory de données.
    
    {
        "name": "MySqlDataSet",
        "properties": {
            "published": false,
            "type": "RelationalTable",
            "linkedServiceName": "OnPremMySqlLinkedService",
            "typeProperties": {},
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }



**Jeu de données de sortie Blob Azure**

Les données sont écrites dans un nouveau blob toutes les heures (fréquence : heure, l’intervalle : 1). Le chemin du dossier pour le blob est évalué dynamiquement en fonction de l’heure de début de la section en cours de traitement. Le chemin du dossier utilise l’année, mois, jour et composants heures de l’heure de début.

    {
        "name": "AzureBlobMySqlDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/mysql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
        "name": "CopyMySqlToBlob",
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
                            "name": "MySqlDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobMySqlDataSet"
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
                    "name": "MySqlToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }



## <a name="mysql-linked-service-properties"></a>Propriétés du service MySQL lié

Le tableau suivant fournit la description pour les éléments JSON spécifiques au service MySQL lié.

| Propriété | Description | Obligatoire |
| -------- | ----------- | -------- | 
| type | La propriété doit être définie : **OnPremisesMySql** | Oui |
| serveur | Nom du serveur MySQL. | Oui |
| base de données | Nom de la base de données MySQL. | Oui | 
| schéma  | Nom du schéma dans la base de données. | N° | 
| authenticationType | Type d’authentification utilisé pour se connecter à la base de données MySQL. Les valeurs possibles sont : anonyme, Basic et Windows. | Oui | 
| nom d’utilisateur | Spécifiez le nom d’utilisateur si vous utilisez l’authentification de base ou Windows. | N° | 
| mot de passe | Spécifiez le mot de passe pour le compte d’utilisateur que vous avez spécifié pour le nom d’utilisateur. | N° | 
| nom de la passerelle | Nom de la passerelle que le service de données usine doit utiliser pour se connecter à la base de données MySQL en local. | Oui |

Pour plus d’informations sur la définition des informations d’identification pour une source de données MySQL en local, voir [définir ces informations et sécurité](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) .

## <a name="mysql-dataset-type-properties"></a>Propriétés du type de jeu de données MySQL

Pour une liste complète des sections et les propriétés disponibles pour la définition des groupes de données, voir l’article [Création de groupes de données](data-factory-create-datasets.md) . Sections telles que la structure, la disponibilité et stratégie d’un dataset JSON sont identiques pour tous les types de jeu de données (SQL Azure, blob Azure, table Azure, etc..).

La section **typeProperties** est différente pour chaque type de jeu de données et fournit des informations sur l’emplacement des données dans le magasin de données. La section typeProperties pour le jeu de données de type **RelationalTable** (qui inclut le jeu de données MySQL) comporte les propriétés suivantes

| Propriété | Description | Obligatoire |
| -------- | ----------- | -------- |
| tableName | Nom de la table dans l’instance de base de données MySQL service liée fait référence à. | N° (si la **requête** de **RelationalSource** est définie) | 

## <a name="mysql-copy-activity-type-properties"></a>Propriétés du type MySQL copie activité

Pour une liste complète des sections et les propriétés disponibles pour la définition des activités, voir l’article [Création Pipelines](data-factory-create-pipelines.md) . Propriétés telles que nom, description, des tableaux d’entrée et de sortie sont stratégies sont disponibles pour tous les types d’activités. 

Propriétés disponibles dans la section **typeProperties** de l’activité dépendent quant à eux de chaque type d’activité. Activité de copie, ils varient selon les types de sources et les récepteurs.

Lors de la source dans l’activité de copie est de type **RelationalSource** (y compris MySQL) les propriétés suivantes sont disponibles dans la section typeProperties :

| Propriété | Description | Valeurs autorisées | Obligatoire |
| -------- | ----------- | -------------- | -------- |
| requête | Utiliser la requête personnalisée pour lire les données. | Chaîne de requête SQL. Par exemple : sélectionnez * from MyTable. | N° (si **tableName** du **jeu de données** n’est spécifié) | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-mysql"></a>Mappage du type pour MySQL

Comme indiqué dans l’article [activités de déplacement des données](data-factory-data-movement-activities.md) , activité de copie exécute des conversions de type automatique à partir de types de sources pour recevoir les types de l’approche en deux étapes suivantes :

1. Conversion de types de sources native en type .NET
2. Conversion de type .NET en type récepteur natif

Lorsque vous déplacez des données à MySQL, les mappages suivants sont utilisés à partir de types de MySQL types .NET.

| Type de base de données MySQL | Type .NET framework |
| ------------------- | ------------------- | 
| bigint non signé | Decimal |
| bigint | Int64 |
| bit | Decimal |
| objets BLOB | Byte] |
| bool |  Valeur booléenne | 
| car | Chaîne |
| date | Date/heure |
| date/heure | Date/heure |
| Decimal | Decimal |
| double précision | Double |
| Double | Double |
| énumération | Chaîne |
| Flottement | Unique |
| Ent non signé | Int64 |
| ent | Int32 |
| entier non signé | Int64 |
| nombre entier | Int32 | 
| varbinary long | Byte] |
| LONGVARCHAR | Chaîne |
| longblob | Byte] |
| LONGTEXT | Chaîne | 
| mediumblob |  Byte] | 
| mediumint non signé | Int64 |
| mediumint | Int32 | 
| mediumtext | Chaîne |
| numérique | Decimal |
| réel |  Double |
| définir | Chaîne |
| smallint non signé | Int32 |
| smallint | Int16 |
| texte | Chaîne |
| heure | TimeSpan |
| horodatage | Date/heure |
| tinyblob | Byte] |
| tinyint non signé |  Int16 | 
| tinyint | Int16 |
| tinytext | Chaîne | 
| varchar | Chaîne | 
| année | Ent | 


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Performances et réglage  
Voir [Guide d’optimisation et des performances d’activité de copie](data-factory-copy-activity-performance.md) pour en savoir plus sur les principaux facteurs impact sur les performances de déplacement de données (copie activité) dans Azure Data Factory et de différentes manières d’optimiser.

