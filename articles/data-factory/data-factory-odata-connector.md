<properties 
    pageTitle="Déplacer des données à partir de sources OData | Données Azure usine" 
    description="Découvrez comment déplacer des données à partir de sources OData à l’aide de Azure Data Factory." 
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
    ms.date="09/26/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-a-odata-source-using-azure-data-factory"></a>Déplacer des données source à partir d’un OData à l’aide de Azure Data Factory
Cet article explique comment vous pouvez utiliser l’activité de copie dans une usine de données Azure pour déplacer des données à partir d’une source OData vers une autre banque de données. Cet article s’appuie sur l’article [activités de déplacement des données](data-factory-data-movement-activities.md) , qui présente une vue d’ensemble de déplacement de données avec l’activité de copie et les combinaisons de magasin de données prises en charge.

> [AZURE.NOTE] Cette prise en charge connecteur OData copier des données depuis les deux cloud OData et sources OData locales. Pour ce dernier, vous devez installer la passerelle de gestion des données. Voir [déplacer des données entre en local et nuage](data-factory-move-data-between-onprem-and-cloud.md) article pour plus d’informations sur la passerelle de gestion des données.

## <a name="copy-data-wizard"></a>Copier l’Assistant de données
Pour créer une opportunité qui copie les données à partir d’une source OData, le plus simple consiste à utiliser l’Assistant copie de données. Voir [didacticiel : créer une opportunité à l’aide de l’Assistant copie](data-factory-copy-data-wizard-tutorial.md) pour un bref aperçu sur la création d’un pipeline à l’aide de l’Assistant copie de données. 

Les exemples suivants fournissent des exemples de définitions JSON que vous pouvez utiliser pour créer une opportunité à l’aide de [portail Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [PowerShell Azure](data-factory-copy-activity-tutorial-using-powershell.md). Ils montrent comment copier des données à partir d’une source OData vers un stockage d’objets Blob Azure. Toutefois, les données peuvent être copiées à un des récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.


## <a name="sample-copy-data-from-odata-source-to-azure-blob"></a>Exemple : Copier des données à partir d’OData source vers Blob Azure

Cet exemple montre comment copier des données à partir d’une source OData au stockage d’objets Blob Azure. Toutefois, les données peuvent être copiées **directement** à un des récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.  
 
L’exemple comporte les entités usine données suivantes :

1.  Un service de type [OData](#odata-linked-service-properties)lié.
2.  Un service de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)lié.
3.  Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [ODataResource](#odata-dataset-type-properties).
4.  Un résultat [jeu de données](data-factory-create-datasets.md) de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  [Pipeline](data-factory-create-pipelines.md) avec une activité copie qui utilise [RelationalSource](#odata-copy-activity-type-properties) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L’exemple copie les données à partir d’une source OData à un blob Azure interroger chaque heure. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivantes les exemples. 

**OData lié service** Cet exemple utilise l’authentification de base. Consultez la section [OData lié service](#odata-linked-service-properties) pour différents types d’authentification, vous pouvez utiliser. 

    {
        "name": "ODataLinkedService",
        "properties": 
        {
            "type": "OData",
            "typeProperties": 
            {
                "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Anonymous"
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

**Jeu de données d’entrée OData**

Définition de « externes » : « true » vous informe le service de données par défaut que le jeu de données externe à l’usine de données et n’est pas produit par une activité dans le factory de données.
    
    {
        "name": "ODataDataset",
        "properties": 
        {
            "type": "ODataResource",
            "typeProperties": 
            {
                "path": "Products" 
            },
            "linkedServiceName": "ODataLinkedService",
            "structure": [],
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3               
            }
        }
    }

Définition spécifiant le **chemin d’accès** dans le groupe de données est facultative. 


**Jeu de données de sortie Blob Azure**

Les données sont écrites dans un nouveau blob toutes les heures (fréquence : heure, l’intervalle : 1). Le chemin du dossier pour le blob est évalué dynamiquement en fonction de l’heure de début de la section en cours de traitement. Le chemin du dossier utilise l’année, mois, jour et composants heures de l’heure de début.

    {
        "name": "AzureBlobODataDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/odata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

Le pipeline contient une activité de copie qui est configuré pour utiliser les jeux de données d’entrée et de sortie et planifiée pour chaque heure. Dans le pipeline de définition de JSON, le type de **source** est défini sur **RelationalSource** et **récepteur de** type est défini sur **BlobSink**. La requête SQL spécifiée pour la propriété de **requête** sélectionne les données les plus récentes (les plus récentes) de la source OData.
    
    {
        "name": "CopyODataToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "?$select=Name, Description&$top=5",
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "ODataDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobODataDataSet"
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
                    "name": "ODataToBlob"
                }
            ],
            "start": "2016-02-01T18:00:00Z",
            "end": "2016-02-03T19:00:00Z"
        }
    }


Spécification de **requête** dans la définition du pipeline est facultative. Est l' **URL** que le service Data Factory utilise pour extraire des données : URL spécifiée dans le service lié (obligatoire) + chemin d’accès spécifié dans le jeu de données (facultatif) + requête dans le pipeline (facultatif). 

## <a name="odata-linked-service-properties"></a>OData liées propriétés du Service

Le tableau suivant fournit la description pour les éléments JSON spécifiques au service OData lié.

| Propriété | Description | Obligatoire |
| -------- | ----------- | -------- | 
| type | La propriété doit être définie : **OData** | Oui |
| URL| URL du service OData. | Oui |
| authenticationType | Type d’authentification utilisé pour se connecter à la source OData. <br/><br/> Pour cloud OData, les valeurs possibles sont anonyme et Basic. Pour OData en local, les valeurs possibles sont anonyme, Basic et Windows. | Oui | 
| nom d’utilisateur | Spécifiez le nom d’utilisateur si vous utilisez l’authentification de base. | Oui (uniquement si vous utilisez l’authentification de base) | 
| mot de passe | Spécifiez le mot de passe pour le compte d’utilisateur que vous avez spécifié pour le nom d’utilisateur. | Oui (uniquement si vous utilisez l’authentification de base) | 
| nom de la passerelle | Nom de la passerelle que le service de données usine doit utiliser pour se connecter au service OData en local. Spécifiez uniquement si vous copiez des données à partir de la source d’OData local. | N° |

### <a name="using-basic-authentication"></a>À l’aide de l’authentification de base

    {
        "name": "inputLinkedService",
        "properties": 
        {
            "type": "OData",
            "typeProperties": 
            {
               "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Basic",
                "username": "username",
               "password": "password"
           }
       }
    }

### <a name="using-anonymous-authentication"></a>À l’aide de l’authentification anonyme
    
    {
        "name": "ODataLinkedService",
        "properties": 
        {
            "type": "OData",
            "typeProperties": 
            {
               "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Anonymous"
           }
       }
    }

### <a name="using-windows-authentication-accessing-on-premises-odata-source"></a>En utilisant l’authentification Windows l’accès à la source d’OData locale

    {
        "name": "inputLinkedService",
        "properties": 
        {
            "type": "OData",
            "typeProperties": 
            {
               "url": "<endpoint of on-premises OData source e.g. Dynamics CRM>",
               "authenticationType": "Windows",
                "username": "domain\\user",
               "password": "password",
               "gatewayName": "mygateway"
           }
       }
    }



## <a name="odata-dataset-type-properties"></a>Propriétés du type de jeu de données OData

Pour une liste complète des sections et les propriétés disponibles pour la définition des groupes de données, voir l’article [Création de groupes de données](data-factory-create-datasets.md) . Sections telles que la structure, la disponibilité et stratégie d’un dataset JSON sont identiques pour tous les types de jeu de données (SQL Azure, blob Azure, table Azure, etc..).

La section **typeProperties** est différente pour chaque type de jeu de données et fournit des informations sur l’emplacement des données dans le magasin de données. La section typeProperties pour le jeu de données de type **ODataResource** (qui inclut le jeu de données OData) comporte les propriétés suivantes

| Propriété | Description | Obligatoire |
| -------- | ----------- | -------- |
| chemin d’accès | Chemin d’accès à la ressource OData | N° | 

## <a name="odata-copy-activity-type-properties"></a>Propriétés de type d’activité de copie OData

Pour une liste complète des sections et les propriétés disponibles pour la définition des activités, voir l’article [Création Pipelines](data-factory-create-pipelines.md) . Propriétés telles que nom, description, entrée et sortie tables et stratégie sont disponibles pour tous les types d’activités. 

Propriétés disponibles dans la section typeProperties de l’activité dépendent quant à eux de chaque type d’activité. Activité de copie, ils varient selon les types de sources et les récepteurs.

Lors de la source est de type **RelationalSource** (y compris OData) les propriétés suivantes sont disponibles dans la section typeProperties :

| Propriété | Description | Exemple | Obligatoire |
| -------- | ----------- | -------------- | -------- |
| requête | Utiliser la requête personnalisée pour lire les données. | « ? $select = nom, Description et $top = 5 » | N° | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-odata"></a>Mappage des types d’OData

Comme indiqué dans l’article [activités de déplacement des données](data-factory-data-movement-activities.md) , activité de copie exécute des conversions de type automatique à partir de types de sources pour recevoir les types de l’approche 2-étape suivante :

1. Conversion de types de sources native en type .NET
2. Conversion de type .NET en type récepteur natif

Lorsque le déplacement des données à partir de données OData stocke, types de données OData sont mappés aux types .NET.


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Performances et réglage  
Voir [Guide d’optimisation et des performances d’activité de copie](data-factory-copy-activity-performance.md) pour en savoir plus sur les principaux facteurs impact sur les performances de déplacement de données (copie activité) dans Azure Data Factory et de différentes manières d’optimiser.

