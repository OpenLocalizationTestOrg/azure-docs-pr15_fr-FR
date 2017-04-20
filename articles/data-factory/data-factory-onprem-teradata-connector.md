<properties 
    pageTitle="Déplacer des données de Teradata | Données Azure usine" 
    description="En savoir plus sur le connecteur Teradata pour le service de données par défaut qui vous permet de déplacer des données à partir de la base de données Teradata" 
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

# <a name="move-data-from-teradata-using-azure-data-factory"></a>Déplacer des données de Teradata à l’aide d’usine de données Azure

Cet article explique comment vous pouvez utiliser l’activité de copie dans une usine de données Azure pour déplacer des données à partir de Teradata vers une autre données stocker. Cet article s’appuie sur l’article [activités de déplacement des données](data-factory-data-movement-activities.md) , qui présente une vue d’ensemble de déplacement de données avec l’activité de copie et les combinaisons de magasin de données prises en charge.

Usine de données prend en charge la connexion aux sources de Teradata local via la passerelle de gestion des données. Consultez l’article de [déplacement des données entre des emplacements de locaux et cloud](data-factory-move-data-between-onprem-and-cloud.md) pour en savoir plus sur la passerelle de gestion des données et des instructions détaillées sur la configuration de la passerelle. 

> [AZURE.NOTE]
Passerelle est requise même si la Teradata est hébergé dans un ordinateur virtuel IaaS Azure. Vous pouvez installer la passerelle sur le même VM IaaS comme magasin de données ou sur un autre ordinateur virtuel dans la mesure où la passerelle peut se connecter à la base de données. 

Usine de données prend en charge uniquement les données déplacement de Teradata aux autres banques de données, mais pas d’autres banques de données pour Teradata.

## <a name="installation"></a>Installation 

Pour la passerelle de gestion des données pour vous connecter à la base de données Teradata, vous devez installer le [Fournisseur de données .NET pour Teradata](http://go.microsoft.com/fwlink/?LinkId=278886) sur le même système que la passerelle de gestion des données.

> [AZURE.NOTE] Voir [passerelle résoudre des problèmes](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) pour obtenir des conseils sur la résolution des problèmes de connexion/passerelle problèmes correspondants. 

## <a name="copy-data-wizard"></a>Copier l’Assistant de données
Pour créer une opportunité qui copie les données à partir de Teradata à n’importe quel récepteur pris en charge banques de données, le plus simple consiste à utiliser l’Assistant copie de données. Voir [didacticiel : créer une opportunité à l’aide de l’Assistant copie](data-factory-copy-data-wizard-tutorial.md) pour un bref aperçu sur la création d’un pipeline à l’aide de l’Assistant copie de données. 

L’exemple suivant fournit des exemples de définitions JSON que vous pouvez utiliser pour créer une opportunité à l’aide de [portail Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [PowerShell Azure](data-factory-copy-activity-tutorial-using-powershell.md). Ils montrent comment copier des données à partir de Teradata au stockage d’objets Blob Azure. Toutefois, les données peuvent être copiées à un des récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.   

### <a name="sample-copy-data-from-teradata-to-azure-blob"></a>Exemple : Copier des données à partir de Teradata vers Blob Azure

Cet exemple montre comment copier des données à partir d’une base de données Teradata vers un stockage d’objets Blob Azure. Toutefois, les données peuvent être copiées **directement** à un des récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.  
 
L’exemple comporte les entités usine données suivantes :

1.  Un service de type [OnPremisesTeradata](data-factory-onprem-teradata-connector.md#teradata-linked-service-properties)lié.
2.  Un service de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)lié.
3.  Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [RelationalTable](data-factory-onprem-teradata-connector.md#teradata-dataset-type-properties).
4.  Un résultat [jeu de données](data-factory-create-datasets.md) de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties). 
4.  [Pipeline](data-factory-create-pipelines.md) avec une activité copie qui utilise [RelationalSource](data-factory-onprem-teradata-connector.md#teradata-copy-activity-type-properties) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L’exemple copie les données à partir d’un résultat de la requête dans la base de données Teradata dans un blob chaque heure. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivantes les exemples. 

D’abord, configurez la passerelle de gestion des données. Les instructions sont dans l’article de [déplacement des données entre des emplacements de locaux et cloud](data-factory-move-data-between-onprem-and-cloud.md) .

**Service Teradata lié :**

    {
        "name": "OnPremTeradataLinkedService",
        "properties": {
            "type": "OnPremisesTeradata",
            "typeProperties": {
                "server": "<server>",
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


**Jeu de données Teradata d’entrée :**

L’exemple suppose que vous avez créé une table « MyTable » dans Teradata et il contient une colonne nommée « horodatage » pour les données de série d’heure.

Définition de « externes » : vrai informe le service de données usine que la table en dehors de l’usine de données et n’est pas produite par une activité dans le factory de données.

    {
        "name": "TeradataDataSet",
        "properties": {
            "published": false,
            "type": "RelationalTable",
            "linkedServiceName": "OnPremTeradataLinkedService",
            "typeProperties": {
            },
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
        "name": "AzureBlobTeradataDataSet",
        "properties": {
            "published": false,
            "location": {
                "type": "AzureBlobLocation",
                "folderPath": "mycontainer/teradata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
                ],
                "linkedServiceName": "AzureStorageLinkedService"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }


**Pipeline avec une activité copier :**

Le pipeline contient une activité de copie qui est configuré pour utiliser les jeux de données d’entrée et de sortie et est planifiée toutes les heures. Dans le pipeline de définition de JSON, le type de **source** est défini sur **RelationalSource** et **récepteur de** type est défini sur **BlobSink**. La requête SQL spécifiée pour la propriété de **requête** sélectionne les données dans la dernière heure à copier.

    {
        "name": "CopyTeradataToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "TeradataDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobTeradataDataSet"
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
                    "name": "TeradataToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z",
            "isPaused": false
        }
    }


## <a name="teradata-linked-service-properties"></a>Propriétés du service Teradata lié

Le tableau suivant fournit la description pour les éléments JSON spécifiques au service Teradata lié. 

Propriété | Description | Obligatoire
-------- | ----------- | --------
type | La propriété doit être définie : **OnPremisesTeradata** | Oui
serveur | Nom du serveur Teradata. | Oui
authenticationType | Type d’authentification utilisé pour se connecter à la base de données Teradata. Les valeurs possibles sont : anonyme, Basic et Windows. | Oui
nom d’utilisateur | Spécifiez le nom d’utilisateur si vous utilisez l’authentification de base ou Windows. | N° 
mot de passe | Spécifiez le mot de passe pour le compte d’utilisateur que vous avez spécifié pour le nom d’utilisateur. | N° 
nom de la passerelle | Nom de la passerelle que le service de données usine doit utiliser pour se connecter à la base de données Teradata locaux. | Oui

Pour plus d’informations sur la définition des informations d’identification pour une source de données Teradata en local, voir [définir ces informations et sécurité](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) .

## <a name="teradata-dataset-type-properties"></a>Propriétés du type de jeu de données Teradata

Pour une liste complète des sections et les propriétés disponibles pour la définition des groupes de données, voir l’article [Création de groupes de données](data-factory-create-datasets.md) . Sections telles que la structure, la disponibilité et stratégie d’un dataset JSON sont identiques pour tous les types de jeu de données (SQL Azure, blob Azure, table Azure, etc..).

La section **typeProperties** est différente pour chaque type de jeu de données et fournit des informations sur l’emplacement des données dans le magasin de données. Il n’existe actuellement aucune propriété de type pris en charge pour le jeu de données Teradata. 


## <a name="teradata-copy-activity-type-properties"></a>Propriétés du type Teradata copie activité

Pour une liste complète des sections et les propriétés disponibles pour la définition des activités, voir l’article [Création Pipelines](data-factory-create-pipelines.md) . Propriétés telles que nom, description, entrée et sortie des tables et les stratégies sont disponibles pour tous les types d’activités. 

Propriétés disponibles dans la section typeProperties de l’activité dépendent quant à eux de chaque type d’activité. Activité de copie, ils varient selon les types de sources et les récepteurs.

Lors de la source est de type **RelationalSource** (y compris Teradata) les propriétés suivantes sont disponibles dans la section **typeProperties** :

Propriété | Description | Valeurs autorisées | Obligatoire
-------- | ----------- | -------------- | --------
requête | Utiliser la requête personnalisée pour lire les données. | Chaîne de requête SQL. Par exemple : sélectionnez * from MyTable. | Oui

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="type-mapping-for-teradata"></a>Mappage du type pour Teradata

Comme indiqué dans l’article [activités de déplacement des données](data-factory-data-movement-activities.md) , l’activité de copie effectue type automatique conversions de types de sources pour recevoir les types de l’approche 2-étape suivante :

1. Conversion de types de sources native en type .NET
2. Conversion de type .NET en type récepteur natif

Lors du déplacement de données pour Teradata, les mappages suivants permettent de type Teradata type .NET.

Type de base de données Teradata | Type .NET framework
----------------- | ---------------------------
Car | Chaîne
CLOB | Chaîne
Graphique | Chaîne
VarChar | Chaîne
VarGraphic | Chaîne
Objets BLOB | Byte]
Sur un octet | Byte]
VarByte | Byte]
BigInt | Int64
ByteInt | Int16
Decimal | Decimal
Double | Double
Nombre entier | Int32
Nombre | Double
SmallInt | Int16
Date | Date/heure
Heure | TimeSpan
Heure avec fuseau horaire | Chaîne
Horodatage | Date/heure
Horodatage avec fuseau horaire | DateTimeOffset
Jour de l’intervalle | TimeSpan
Intervalle jour a heure | TimeSpan
Intervalle jour a Minute | TimeSpan
Intervalle jour a seconde | TimeSpan
Intervalle heure | TimeSpan
Intervalle heure a Minute | TimeSpan
Intervalle heure a seconde | TimeSpan
Intervalle Minute | TimeSpan
Intervalle Minute a seconde | TimeSpan
Intervalle seconde | TimeSpan
Intervalle ANNEE | Chaîne
Intervalle ANNEE a mois | Chaîne
Mois de l’intervalle | Chaîne
Period(date) | Chaîne
Period(Time) | Chaîne
Période (temps et de fuseau horaire) | Chaîne
Period(timestamp) | Chaîne
Période (horodatage avec fuseau horaire) | Chaîne
XML | Chaîne

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Performances et réglage  
Voir [Guide d’optimisation et des performances d’activité de copie](data-factory-copy-activity-performance.md) pour en savoir plus sur les principaux facteurs impact sur les performances de déplacement de données (copie activité) dans Azure Data Factory et de différentes manières d’optimiser.