<properties 
    pageTitle="Déplacer des données d’HADOOP local | Données Azure usine" 
    description="Découvrez comment déplacer des données d’HADOOP locales à l’aide d’Azure Data Factory." 
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

# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Déplacer des données d’HADOOP locales à l’aide d’usine de données Azure
Cet article explique comment vous pouvez utiliser l’activité de copie dans une usine de données Azure pour déplacer des données à partir d’un HADOOP local vers une autre banque de données. Cet article s’appuie sur l’article [activités de déplacement de données](data-factory-data-movement-activities.md) qui présente une vue d’ensemble de déplacement de données avec activité de copie et combinaisons de magasin de données prises en charge.

Usine données prend actuellement en charge uniquement les données à partir d’un HADOOP locaux déplacement aux autres banques de données, mais ne pas de déplacement des données provenant d’autres banques de données vers un HADOOP locaux.


## <a name="enabling-connectivity"></a>Activation de la connectivité
Un service de données usine prend en charge la connexion à HADOOP locales à l’aide de la passerelle de gestion des données. Consultez l’article de [déplacement des données entre des emplacements de locaux et cloud](data-factory-move-data-between-onprem-and-cloud.md) pour en savoir plus sur la passerelle de gestion des données et des instructions détaillées sur la configuration de la passerelle. Utiliser la passerelle pour vous connecter à HADOOP, même s’il est hébergé dans un ordinateur virtuel IaaS Azure. 

Lorsque vous installez passerelle sur le même ordinateur local ou la machine virtuelle Azure comme la HADOOP, nous vous recommandons d’installer la passerelle sur un ordinateur distinct/Azure IaaS machine virtuelle. Vous rencontrez des passerelle sur un ordinateur distinct réduit les conflits de ressources et améliore les performances. Lorsque vous installez la passerelle sur un ordinateur distinct, l’ordinateur doit être en mesure d’accéder à l’ordinateur avec la HADOOP. 


## <a name="copy-data-wizard"></a>Copier l’Assistant de données
Pour créer un pipeline qui copie les données locales HADOOP, le plus simple consiste à utiliser l’Assistant copie de données. Voir [didacticiel : créer une opportunité à l’aide de l’Assistant copie](data-factory-copy-data-wizard-tutorial.md) pour un bref aperçu sur la création d’un pipeline à l’aide de l’Assistant copie de données. 

Les exemples suivants fournissent des exemples de définitions JSON que vous pouvez utiliser pour créer une opportunité à l’aide de [portail Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [PowerShell Azure](data-factory-copy-activity-tutorial-using-powershell.md). Ils montrent comment copier des données à partir d’un HADOOP local vers un stockage d’objets Blob Azure. Toutefois, les données peuvent être copiées à un des récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.

## <a name="sample-copy-data-from-on-premises-hdfs-to-azure-blob"></a>Exemple : Copier des données d’HADOOP locaux au Blob Azure

Cet exemple montre comment copier des données à partir d’un HADOOP local au stockage d’objets Blob Azure. Toutefois, les données peuvent être copiées **directement** à un des récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.  
 
L’exemple comporte les entités usine données suivantes :

1.  Un service de type [OnPremisesHdfs](#hdfs-linked-service-properties)lié.
2.  Un service de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)lié.
3.  Un [jeu de données](data-factory-create-datasets.md) d’entrée de type de [partage de fichiers](#hdfs-dataset-type-properties).
4.  Un résultat [jeu de données](data-factory-create-datasets.md) de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  [Pipeline](data-factory-create-pipelines.md) avec une activité copie qui utilise [FileSystemSource](#hdfs-copy-activity-type-properties) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L’exemple copie les données à partir d’un HADOOP en local pour un blob Azure toutes les heures. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivantes les exemples. 

D’abord, configurez la passerelle de gestion des données. Les instructions présentées dans l’article de [déplacement des données entre des emplacements de locaux et cloud](data-factory-move-data-between-onprem-and-cloud.md) . 

**HADOOP lié service** Cet exemple utilise l’authentification Windows. Consultez la section [HADOOP lié service](#hdfs-linked-service-properties) pour différents types d’authentification, vous pouvez utiliser. 

    {
        "name": "HDFSLinkedService",
        "properties":
        {
            "type": "Hdfs",
            "typeProperties":
            {
                "authenticationType": "Windows",
                "userName": "Administrator",
                "password": "password",
                "url" : "http://<machine>:50070/webhdfs/v1/",
                "gatewayName": "mygateway"
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

**HADOOP entrée dataset** Ce dataset fait référence au dossier HADOOP DataTransfer/UnitTest /. Le pipeline de copie tous les fichiers dans ce dossier vers la destination. 

Définition de « externes » : « true » vous informe le service de données par défaut que le jeu de données externe à l’usine de données et n’est pas produit par une activité dans le factory de données.
    
    {
        "name": "InputDataset",
        "properties": {
            "type": "FileShare",
            "linkedServiceName": "HDFSLinkedService",
            "typeProperties": {
                "folderPath": "DataTransfer/UnitTest/"
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval":  1
            }
        }
    }




**Jeu de données de sortie Blob Azure**

Les données sont écrites dans un nouveau blob toutes les heures (fréquence : heure, l’intervalle : 1). Le chemin du dossier pour le blob est évalué dynamiquement en fonction de l’heure de début de la section en cours de traitement. Le chemin du dossier utilise l’année, mois, jour et composants heures de l’heure de début.

    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

Le pipeline contient une activité de copie qui est configuré pour utiliser ces jeux de données d’entrée et de sortie et planifiée pour chaque heure. Dans le pipeline de définition de JSON, le type de **source** est défini sur **FileSystemSource** et **récepteur de** type est défini sur **BlobSink**. La requête SQL spécifiée pour la propriété de **requête** sélectionne les données dans la dernière heure à copier.
    
    {
        "name": "pipeline",
        "properties":
        {
            "activities":
            [
                {
                    "name": "HdfsToBlobCopy",
                    "inputs": [ {"name": "InputDataset"} ],
                    "outputs": [ {"name": "OutputDataset"} ],
                    "type": "Copy",
                    "typeProperties":
                    {
                        "source":
                        {
                            "type": "FileSystemSource"
                        },
                        "sink":
                        {
                            "type": "BlobSink"
                        }
                    },
                    "policy":
                    {
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1,
                        "timeout": "00:05:00"
                    }
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }



## <a name="hdfs-linked-service-properties"></a>Propriétés du Service liées HADOOP

Le tableau suivant fournit la description pour les éléments JSON spécifiques à HADOOP lié service.

| Propriété | Description | Obligatoire |
| -------- | ----------- | -------- | 
| type | La propriété doit être définie : **Hadoop** | Oui | 
| URL | URL pour le HADOOP | Oui |
| encryptedCredential | [Nouveau AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) sortie de l’information d’identification access. | N° |
| nom d’utilisateur | Nom d’utilisateur pour l’authentification Windows. | Oui (pour l’authentification Windows)
| mot de passe | Mot de passe pour l’authentification Windows. | Oui (pour l’authentification Windows)
| authenticationType | Windows, ou anonymes. | Oui |
| nom de la passerelle | Nom de la passerelle que le service de données usine doit utiliser pour se connecter à la HDFS. | Oui |   

Pour plus d’informations sur la définition des informations d’identification pour HADOOP en local, voir [définir ces informations et sécurité](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) .

### <a name="using-anonymous-authentication"></a>À l’aide de l’authentification anonyme

    {
        "name": "hdfs",
        "properties":
        {
            "type": "Hdfs",
            "typeProperties":
            {
                "authenticationType": "Anonymous",
                "userName": "hadoop",
                "url" : "http://<machine>:50070/webhdfs/v1/",
                "gatewayName": "mygateway"
            }
        }
    }


### <a name="using-windows-authentication"></a>À l’aide de l’authentification Windows
    
    {
        "name": "hdfs",
        "properties":
        {
            "type": "Hdfs",
            "typeProperties":
            {
                "authenticationType": "Windows",
                "userName": "Administrator",
                "password": "password",
                "url" : "http://<machine>:50070/webhdfs/v1/",
                "gatewayName": "mygateway"
            }
        }
    }
 


## <a name="hdfs-dataset-type-properties"></a>Propriétés du type Dataset HADOOP

Pour une liste complète des sections et les propriétés disponibles pour la définition des groupes de données, voir l’article [Création de groupes de données](data-factory-create-datasets.md) . Sections telles que la structure, la disponibilité et stratégie d’un dataset JSON sont identiques pour tous les types de jeu de données (SQL Azure, blob Azure, table Azure, etc..).

La section **typeProperties** est différente pour chaque type de jeu de données et fournit des informations sur l’emplacement des données dans le magasin de données. La section typeProperties pour le jeu de données de type de **partage de fichiers** (y compris HADOOP dataset) comporte les propriétés suivantes

Propriété | Description | Obligatoire
-------- | ----------- | --------
folderPath | Chemin d’accès au dossier. Exemple :`myfolder`<br/><br/>Utiliser le caractère d’échappement ' \ ' des caractères spéciaux dans la chaîne. Par exemple : pour dossier\sous-dossier, spécifiez le dossier\\\\sous-dossier et pour d:\samplefolder, spécifiez d\\\\samplefolder.<br/><br/>Vous pouvez combiner cette propriété avec **partitionBy** pour que les chemins d’accès basés sur secteur date et l’heure de début/fin. | Oui
nom de fichier | Spécifiez le nom du fichier dans la **folderPath** si vous souhaitez que le tableau pour faire référence à un fichier spécifique dans le dossier. Si vous ne spécifiez pas une valeur de cette propriété, la table pointe vers tous les fichiers dans le dossier.<br/><br/>Lorsque le nom de fichier n’est pas spécifié pour un dataset de sortie, le nom du fichier généré serait dans ce qui suit ce format : <br/><br/>Données. <Guid>.txt (par exemple : : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt | N°
partitionedBy | partitionedBy peut servir à spécifier une dynamique folderPath, nom de fichier de données de série de date. Exemple : folderPath paramétrée pour toutes les heures de données. | N°
fileFilter | Spécifier un filtre à utiliser pour sélectionner un sous-ensemble de fichiers dans le folderPath plutôt que tous les fichiers. <br/><br/>Valeurs autorisées sont : `*` (plusieurs caractères) et `?` (un seul caractère).<br/><br/>Exemple 1 :`"fileFilter": "*.log"`<br/>Exemple 2 :`"fileFilter": 2014-1-?.txt"`<br/><br/>**Remarque**: FiltreFichier est applicable pour un partage de fichiers de données d’entrée | N°
| mettre en forme | Les types de formats suivants sont pris en charge : **format du texte**, **AvroFormat**, **JsonFormat**, **OrcFormat**et **ParquetFormat**. Définissez **la propriété sous format** à un de ces valeurs. Voir [Format du texte spécifiant](#specifying-textformat), [AvroFormat spécifiant](#specifying-avroformat), [JsonFormat spécifiant](#specifying-jsonformat), [OrcFormat spécifiant](#specifying-orcformat), [ParquetFormat spécifiant](#specifying-parquetformat) sections et pour plus d’informations. Si vous voulez copier des fichiers en tant que-est entre banques basée sur le fichier (copie binaire), vous pouvez ignorer la section format dans des définitions de jeu de données d’entrée et de sortie. | N° 
| compression | Spécifier le type et le niveau de compression pour les données. Types pris en charge sont : **GZip**, **Deflate**et **BZip2** et les niveaux de prise en charge : **Optimal** et **plus rapide**. Pour l’instant, les paramètres de compression ne sont pas prises en charge des données dans **AvroFormat** ou **OrcFormat**. Pour plus d’informations, reportez-vous à la section [prise en charge de la Compression](#compression-support) .  | N° |



> [AZURE.NOTE] nom de fichier et FiltreFichier ne peuvent pas être utilisés simultanément.


### <a name="using-partionedby-property"></a>À l’aide de la propriété partionedBy

Comme indiqué dans la section précédente, vous pouvez spécifier une dynamique folderPath, nom de fichier de données de série de date avec partitionedBy. Vous pouvez le faire avec les macros de données usine et la variable système SliceStart, SliceEnd qui indiquent la logique période pour une section donnée. 

Pour en savoir plus sur les jeux de données de série heure, planification et secteurs, voir [Créer des groupes de données](data-factory-create-datasets.md), [planification et exécution](data-factory-scheduling-and-execution.md)et [Pipelines de création](data-factory-create-pipelines.md) d’articles. 

#### <a name="sample-1"></a>Exemple 1 :

    "folderPath": "wikidatagateway/wikisampledataout/{Slice}",
    "partitionedBy": 
    [
        { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
    ],

Dans cet exemple {section} est remplacée par la valeur de la variable Data Factory système SliceStart au format (YYYYMMDDHH) spécifiée. La SliceStart fait référence pour l’heure de début de la section. La folderPath est différente pour chaque secteur. Par exemple : wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Exemple 2 :

    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
     [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

Dans cet exemple, année, mois, jour et l’heure de SliceStart sont extraits dans des variables distinctes qui sont utilisées par les propriétés folderPath et nom de fichier.

[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]   
[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="hdfs-copy-activity-type-properties"></a>Propriétés de type HADOOP copie activité

Pour une liste complète des sections et les propriétés disponibles pour la définition des activités, voir l’article [Création Pipelines](data-factory-create-pipelines.md) . Propriétés telles que nom, description, entrée et sortie des tables et les stratégies sont disponibles pour tous les types d’activités. 

Propriétés disponibles dans la section typeProperties de l’activité dépendent quant à eux de chaque type d’activité. Activité de copie, ils varient selon les types de sources et les récepteurs.

Activité de copie, lors de la source est de type **FileSystemSource** les propriétés suivantes sont disponibles dans la section typeProperties :

**FileSystemSource** prend en charge les propriétés suivantes :

| Propriété | Description | Valeurs autorisées | Obligatoire |
| -------- | ----------- | -------------- | -------- |
| récursives | Indique si les données soient lues de manière récursive dans les sous-dossiers ou uniquement dans le dossier spécifié. | Vrai, faux (par défaut)| N° |

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Performances et réglage  
Voir [Guide d’optimisation et des performances d’activité de copie](data-factory-copy-activity-performance.md) pour en savoir plus sur les principaux facteurs impact sur les performances de déplacement de données (copie activité) dans Azure Data Factory et de différentes manières d’optimiser.

