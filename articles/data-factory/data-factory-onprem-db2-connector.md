<properties 
    pageTitle="Déplacer des données de DB2 | Données Azure usine" 
    description="Découvrez comment déplacer des données à partir de la base de données DB2 à l’aide d’usine de données Azure" 
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
    ms.date="09/06/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-db2-using-azure-data-factory"></a>Déplacer des données à partir de DB2 à l’aide d’usine de données Azure
Cet article explique comment vous pouvez utiliser l’activité de copie dans une usine de données Azure pour déplacer des données à partir de DB2 vers une autre données stocker. Cet article s’appuie sur l’article [activités de déplacement des données](data-factory-data-movement-activities.md) , qui présente une vue d’ensemble de déplacement de données avec l’activité de copie et les combinaisons de magasin de données prises en charge.

Usine de données prend en charge la connexion aux sources de DB2 local à l’aide de la passerelle de gestion des données. Consultez l’article de [déplacement des données entre des emplacements de locaux et cloud](data-factory-move-data-between-onprem-and-cloud.md) pour en savoir plus sur la passerelle de gestion des données et des instructions détaillées sur la configuration de la passerelle. 

> [AZURE.NOTE]
Utiliser la passerelle pour se connecter à DB2 même s’il est hébergé dans Azure IaaS machines virtuelles. Si vous essayez de vous connecter à une instance de DB2 hébergé dans le cloud, vous pouvez également installer l’instance passerelle dans le IaaS VM.

Usine données prend actuellement en charge uniquement les données déplacement de DB2 aux autres banques de données, mais pas d’autres banques de données DB2. 

## <a name="installation"></a>Installation 

La passerelle de gestion des données fournit un pilote DB2 intégré qui prend en charge les éléments suivants : 

- SQLAM 9 / 10 / 11
- DB2 pour LUW (Linux, Unix, Windows)
- DB2 pour z/OS et DB2 pour je (ou en tant que / 400)

Par conséquent, vous n’avez plus besoin installer manuellement les pilotes lorsque vous copiez des données à partir de DB2.

> [AZURE.NOTE] Voir [passerelle résoudre des problèmes](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) pour obtenir des conseils sur la résolution des problèmes de connexion/passerelle problèmes correspondants. 

## <a name="copy-data-wizard"></a>Copier l’Assistant de données
Pour créer une opportunité qui copie les données à partir d’une base de données DB2, le plus simple consiste à utiliser l’Assistant copie de données. Voir [didacticiel : créer une opportunité à l’aide de l’Assistant copie](data-factory-copy-data-wizard-tutorial.md) pour un bref aperçu sur la création d’un pipeline à l’aide de l’Assistant copie de données. 

Les exemples suivants fournissent des exemples de définitions JSON que vous pouvez utiliser pour créer une opportunité à l’aide de [portail Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [PowerShell Azure](data-factory-copy-activity-tutorial-using-powershell.md). Ils montrent comment copier des données à partir de la base de données DB2 et stockage d’objets Blob Azure. Toutefois, les données peuvent être copiées à un des récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.

## <a name="sample-copy-data-from-db2-to-azure-blob"></a>Exemple : Copier des données à partir de DB2 vers Blob Azure

Cet exemple montre comment copier des données à partir d’une base de données DB2 local vers un stockage d’objets Blob Azure. Toutefois, les données peuvent être copiées **directement** à un des récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.  
 
L’exemple comporte les entités usine données suivantes :

1.  Un service de type [OnPremisesDb2](data-factory-onprem-db2-connector.md#db2-linked-service-properties)lié.
2.  Un service de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)lié. 
3.  Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [RelationalTable](data-factory-onprem-db2-connector.md#db2-dataset-type-properties).
4.  Un résultat [jeu de données](data-factory-create-datasets.md) de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties). 
5.  [Pipeline](data-factory-create-pipelines.md) avec une activité copie qui utilise [RelationalSource](data-factory-onprem-db2-connector.md#db2-copy-activity-type-properties) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties). 

L’exemple copie les données à partir d’un résultat de la requête dans une base de données DB2 à un blob Azure toutes les heures. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivantes les exemples. 

D’abord, installer et configurer une passerelle de gestion des données. Instructions se trouvent dans l’article de [déplacement des données entre des emplacements de locaux et cloud](data-factory-move-data-between-onprem-and-cloud.md) .

**Service DB2 lié :**

    {
        "name": "OnPremDb2LinkedService",
        "properties": {
            "type": "OnPremisesDb2",
            "typeProperties": {
                "server": "<server>",
                "database": "<database>",
                "schema": "<schema>",
                "authenticationType": "<authentication type>",
                "username": "<username>",
                "password": "<password>",
                "gatewayName": "<gatewayName>"
            }
        }
    }


**Stockage d’objets Blob Azure lié service :**

    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorageLinkedService",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
            }
        }
    }

**Jeu de données d’entrée DB2 :**

L’exemple suppose que vous avez créé une table « MyTable » dans DB2 et il contient une colonne nommée « horodatage » pour les données de série d’heure.

Définition de « externes » : vrai informe le service de données usine que ce groupe de données externe à l’usine de données et n’est pas produit par une activité dans le factory de données. Notez que le **type** est défini sur **RelationalTable**. 

    {
        "name": "Db2DataSet",
        "properties": {
            "type": "RelationalTable",
            "linkedServiceName": "OnPremDb2LinkedService",
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


**Jeu de données de sortie Blob Azure :**

Les données sont écrites dans un nouveau blob toutes les heures (fréquence : heure, l’intervalle : 1). Le chemin du dossier pour le blob est évalué dynamiquement en fonction de l’heure de début de la section en cours de traitement. Le chemin du dossier utilise l’année, mois, jour et composants heures de l’heure de début.

    {
        "name": "AzureBlobDb2DataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Pipeline avec une activité copier :**

Le pipeline contient une activité de copie qui est configuré pour utiliser les jeux de données d’entrée et de sortie et planifiée pour chaque heure. Dans le pipeline de définition de JSON, le type de **source** est défini sur **RelationalSource** et **récepteur de** type est défini sur **BlobSink**. La requête SQL spécifiée pour la propriété de **requête** sélectionne les données de la table commandes.

    {
        "name": "CopyDb2ToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "select * from \"Orders\""
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "inputs": [
                        {
                            "name": "Db2DataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobDb2DataSet"
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
                    "name": "Db2ToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }


## <a name="db2-linked-service-properties"></a>Propriétés du service DB2 lié

Le tableau suivant fournit la description pour les éléments JSON spécifiques au service DB2 lié. 

| Propriété | Description | Obligatoire |
| -------- | ----------- | -------- | 
| type | La propriété doit être définie : **OnPremisesDB2** | Oui |
| serveur | Nom du serveur DB2. | Oui |
| base de données | Nom de la base de données DB2. | Oui |
| schéma | Nom du schéma dans la base de données. Le nom de schéma respecte la casse. | N° |
| authenticationType | Type d’authentification utilisé pour se connecter à la base de données DB2. Les valeurs possibles sont : anonyme, Basic et Windows. | Oui |
| nom d’utilisateur | Spécifiez le nom d’utilisateur si vous utilisez l’authentification de base ou Windows. | N° |
| mot de passe | Spécifiez le mot de passe pour le compte d’utilisateur que vous avez spécifié pour le nom d’utilisateur. | N° |
| nom de la passerelle | Nom de la passerelle que le service de données usine doit utiliser pour se connecter à la base de données locale DB2. | Oui |

Pour plus d’informations sur la définition des informations d’identification pour une source de données DB2 en local, voir [définir ces informations et sécurité](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) . 


## <a name="db2-dataset-type-properties"></a>Propriétés du type dataset DB2

Pour une liste complète des sections et les propriétés disponibles pour la définition des groupes de données, voir l’article [Création de groupes de données](data-factory-create-datasets.md) . Sections telles que la structure, la disponibilité et stratégie d’un dataset JSON sont identiques pour tous les types de jeu de données (SQL Azure, blob Azure, table Azure, etc..).

La section typeProperties est différente pour chaque type de jeu de données et fournit des informations sur l’emplacement des données dans le magasin de données. La section typeProperties pour le jeu de données de type RelationalTable (y compris DB2 dataset) comporte les propriétés suivantes.

| Propriété | Description | Obligatoire |
| -------- | ----------- | -------- | 
| tableName | Nom de la table dans l’instance de base de données DB2 renvoyant à des services liés. Le paramètre tableName respecte la casse. | N° (si la **requête** de **RelationalSource** est définie) |

## <a name="db2-copy-activity-type-properties"></a>Propriétés du type DB2 copie activité

Pour une liste complète des sections et les propriétés disponibles pour la définition des activités, voir l’article [Création Pipelines](data-factory-create-pipelines.md) . Propriétés telles que nom, description, entrée et sortie des tables et les stratégies sont disponibles pour tous les types d’activités. 

Propriétés disponibles dans la section typeProperties de l’activité dépendent quant à eux de chaque type d’activité. Activité de copie, ils varient selon les types de sources et les récepteurs.

Activité de copie, lors de la source est de type **RelationalSource** (y compris DB2) les propriétés suivantes sont disponibles dans typeProperties section :


| Propriété | Description | Valeurs autorisées | Obligatoire |
| -------- | ----------- | -------- | -------------- |
| requête | Utiliser la requête personnalisée pour lire les données. | Chaîne de requête SQL. Par exemple : `"query": "select * from "MySchema"."MyTable""`. | N° (si **tableName** du **jeu de données** n’est spécifié)|

> [AZURE.NOTE] Noms des tables et de schéma respectent la casse. Placez les noms dans « » (guillemets doubles) dans la requête.  

**Exemple :**

    "query": "select * from "DB2ADMIN"."Customers""


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="type-mapping-for-db2"></a>Mappage du type pour DB2
Comme indiqué dans l’article [activités de déplacement des données](data-factory-data-movement-activities.md) , l’activité de copie effectue type automatique conversions de types de sources pour recevoir les types de l’approche 2-étape suivante :

1. Conversion de types de sources native en type .NET
2. Conversion de type .NET en type récepteur natif

Lors du déplacement de données DB2, les mappages suivants permettent de type DB2 type .NET.

Type de base de données DB2 | Type .NET framework 
----------------- | ------------------- 
SmallInt | Int16
Nombre entier | Int32
BigInt | Int64
Réel | Unique
Double | Double
Rendre flottant | Double
Decimal | Decimal
DecimalFloat | Decimal
Numérique | Decimal
Date | Date/heure
Heure | TimeSpan
Horodatage | Date/heure
XML | Byte]
Car | Chaîne
VarChar | Chaîne
LongVarChar | Chaîne
DB2DynArray | Chaîne
Binaire | Byte]
VarBinary | Byte]
LongVarBinary | Byte]
Graphique | Chaîne
VarGraphic | Chaîne
LongVarGraphic | Chaîne
CLOB | Chaîne
Objets BLOB | Byte]
DbClob | Chaîne
SmallInt | Int16
Nombre entier | Int32
BigInt | Int64
Réel | Unique
Double | Double
Rendre flottant | Double
Decimal | Decimal
DecimalFloat | Decimal
Numérique | Decimal
Date | Date/heure
Heure | TimeSpan
Horodatage | Date/heure
XML | Byte]
Car | Chaîne


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]


[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Performances et réglage  
Voir [Guide d’optimisation et des performances d’activité de copie](data-factory-copy-activity-performance.md) pour en savoir plus sur les principaux facteurs impact sur les performances de déplacement de données (copie activité) dans Azure Data Factory et de différentes manières d’optimiser.


