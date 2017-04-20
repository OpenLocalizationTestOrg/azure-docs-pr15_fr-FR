<properties 
    pageTitle="Déplacer des données vers ou depuis Oracle à l’aide de Data Factory | Microsoft Azure" 
    description="Découvrez comment déplacer des données vers ou à partir d’une base de données Oracle qui se trouve en local à l’aide d’Azure Data Factory." 
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

# <a name="move-data-tofrom-on-premises-oracle-using-azure-data-factory"></a>Déplacer des données vers ou à partir d’Oracle en local à l’aide de Azure Data Factory 

Cet article explique comment vous pouvez utiliser des données usine copie activité pour déplacer des données vers ou à partir d’Oracle à partir de vers une autre données stocker. Cet article s’appuie sur l’article [activités de déplacement des données](data-factory-data-movement-activities.md) , qui présente une vue d’ensemble de déplacement de données avec l’activité de copie et les combinaisons de magasin de données prises en charge.

## <a name="installation"></a>Installation 
Service Azure Data Factory pouvoir vous connecter à votre base de données Oracle en local, vous devez installer les composants suivants : 

- Passerelle gestion des données sur le même ordinateur qui héberge la base de données ou sur un ordinateur distinct afin d’éviter la concurrence des ressources avec la base de données. Passerelle de gestion des données est un agent de client qui se connecte des sources de données locales aux services cloud de façon sécurisée et gérée. Voir [déplacer des données entre en local et nuage](data-factory-move-data-between-onprem-and-cloud.md) article pour plus d’informations sur la passerelle de gestion des données. 
- Fournisseur de données Oracle pour .NET. Ce composant est inclus dans [Les composants Oracle données Access pour Windows](http://www.oracle.com/technetwork/topics/dotnet/downloads/). Installer la version appropriée (32/64 bits) sur l’ordinateur hôte dans lequel est installée la passerelle. [Oracle données fournisseur .NET 12.1](http://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) peut accéder à la base de données Oracle 10 g Release 2 ou version ultérieure.

    Si vous choisissez « Installation XCopy », suivez les étapes dans le fichier Lisez-moi.htm. Nous vous recommandons de que choisir du programme d’installation avec une interface utilisateur (non-XCopy une). 
 
    Après avoir installé le fournisseur, redémarrez le service hôte de passerelle de gestion des données sur votre ordinateur à l’aide des Services applet (ou) Gestionnaire de Configuration de passerelle de gestion des données.  

> [AZURE.NOTE] Voir [passerelle résoudre des problèmes](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) pour obtenir des conseils sur la résolution des problèmes de connexion/passerelle problèmes correspondants. 

## <a name="copy-data-wizard"></a>Copier l’Assistant de données
Pour créer une opportunité qui copie les données depuis/vers une base de données Oracle à une des banques de données pris en charge récepteur, le plus simple consiste à utiliser l’Assistant copie de données. Voir [didacticiel : créer une opportunité à l’aide de l’Assistant copie](data-factory-copy-data-wizard-tutorial.md) pour un bref aperçu sur la création d’un pipeline à l’aide de l’Assistant copie de données. 

L’exemple suivant fournit des exemples de définitions JSON que vous pouvez utiliser pour créer une opportunité à l’aide de [portail Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [PowerShell Azure](data-factory-copy-activity-tutorial-using-powershell.md). Ils montrent comment copier des données depuis/vers une base de données Oracle vers ou à partir de stockage d’objets Blob Azure. Toutefois, les données peuvent être copiées à un des récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.   

## <a name="sample-copy-data-from-oracle-to-azure-blob"></a>Exemple : Copier des données à partir d’Oracle vers Blob Azure
Cet exemple montre comment copier des données à partir d’une base de données Oracle local vers un stockage d’objets Blob Azure. Toutefois, les données peuvent être copiées **directement** à un des récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.  
 
L’exemple comporte les entités usine données suivantes :

1.  Un service de type [OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties)lié.
2.  Un service de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)lié.
3.  Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties). 
4.  Un résultat [jeu de données](data-factory-create-datasets.md) de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5.  [Pipeline](data-factory-create-pipelines.md) avec une activité copie qui utilise [OracleSource](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) comme source et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) en tant que récepteur.

L’exemple copie les données d’un tableau dans une base de données Oracle en local pour un blob toutes les heures. Pour plus d’informations sur les différentes propriétés utilisées dans l’échantillon, consultez la documentation dans les sections suivantes les exemples.

**Service Oracle lié :**

    {
      "name": "OnPremisesOracleLinkedService",
      "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
          "ConnectionString": "data source=<data source>;User Id=<User Id>;Password=<Password>;",
          "gatewayName": "<gateway name>"
        }
      }
    }

**Stockage d’objets Blob Azure lié service :**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
      }
    }

**Jeu de données Oracle d’entrée :**

L’exemple suppose que vous avez créé une table « MyTable » dans Oracle et il contient une colonne nommée « timestampcolumn » pour les données de série d’heure. 

Définition de « externes » : « true » vous informe le service de données par défaut que le jeu de données externe à l’usine de données et n’est pas produit par une activité dans le factory de données.

    {
        "name": "OracleInput",
        "properties": {
            "type": "OracleTable",
            "linkedServiceName": "OnPremisesOracleLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
               "external": true,
            "availability": {
                "offset": "01:00:00",
                "interval": "1",
                "anchorDateTime": "2014-02-27T12:00:00",
                "frequency": "Hour"
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

Les données sont écrites dans un nouveau blob toutes les heures (fréquence : heure, l’intervalle : 1). Le nom du fichier et le chemin dossier pour l’objets blob sont évaluées dynamiquement en fonction de l’heure de début de la section en cours de traitement. Le chemin du dossier utilise l’année, mois, jour et composants heures de l’heure de début.
    
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


**Pipeline avec une activité copier :**

Le pipeline contient une activité de copie qui est configuré pour utiliser les jeux de données d’entrée et de sortie et est planifiée toutes les heures. Dans le pipeline de définition de JSON, le type de **source** est défini sur **OracleSource** et **récepteur de** type est défini sur **BlobSink**.  La requête SQL spécifiée avec propriété **oracleReaderQuery** sélectionne les données dans la dernière heure à copier.

    
    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
          {
            "name": "OracletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [
              {
                "name": " OracleInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "OracleSource",
                "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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


Vous avez besoin d’ajuster la chaîne de requête basée sur la façon dont les dates sont configurés dans votre base de données Oracle. Si vous voyez le message d’erreur suivant : 

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

Vous devrez peut-être modifier la requête comme le montre l’exemple suivant (à l’aide de la fonction to_date) :

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\')  AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"

## <a name="sample-copy-data-from-azure-blob-to-oracle"></a>Exemple : Copier des données d’objets Blob Azure pour Oracle
Cet exemple montre comment copier des données à partir d’un stockage d’objets Blob Azure à une base de données Oracle en local. Toutefois, les données peuvent être copiées **directement** depuis n’importe quelle les sources indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.  
 
L’exemple comporte les entités usine données suivantes :

1.  Un service de type [OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties)lié.
2.  Un service de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)lié.
3.  Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  Un résultat [jeu de données](data-factory-create-datasets.md) de type [OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties). 
5.  [Pipeline](data-factory-create-pipelines.md) avec une activité copie qui utilise [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) comme source [OracleSink](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) en tant que récepteur.

L’exemple copie les données à partir d’un objet blob à une table dans une base de données Oracle local chaque heure. Pour plus d’informations sur les différentes propriétés utilisées dans l’échantillon, consultez la documentation dans les sections suivantes les exemples.

**Service Oracle lié :**

    {
      "name": "OnPremisesOracleLinkedService",
      "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
          "ConnectionString": "data source=<data source>;User Id=<User Id>;Password=<Password>;",
          "gatewayName": "<gateway name>"
        }
      }
    }

**Stockage d’objets Blob Azure lié service :**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
      }
    }

**Jeu de données d’entrée Azure Blob**

Données sont provenant d’un objet blob de nouveau toutes les heures (fréquence : heure, l’intervalle : 1). Le nom du fichier et le chemin dossier pour l’objets blob sont évaluées dynamiquement en fonction de l’heure de début de la section en cours de traitement. Le chemin du dossier utilise année, mois et partie jour de l’heure de début et nom de fichier utilise la partie heure de l’heure de début. « externes » : paramètre « true » vous informe le service de données usine que ce tableau en dehors de l’usine de données et n’est pas produit par une activité dans le factory de données.

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

**Jeu de données Oracle sortie :**

L’exemple suppose que vous avez créé une table « MyTable » dans Oracle. Créer une table dans Oracle avec le même nombre de colonnes comme prévu le fichier CSV Blob contenir. Nouvelles lignes sont ajoutées à la table chaque heure.

    {
        "name": "OracleOutput",
        "properties": {
            "type": "OracleTable",
            "linkedServiceName": "OnPremisesOracleLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": "1"
            }
        }
    }


**Pipeline avec une activité copier :**

Le pipeline contient une activité de copie qui est configuré pour utiliser les jeux de données d’entrée et de sortie et planifiée pour chaque heure. Dans le pipeline de définition de JSON, le type de **source** est défini sur **BlobSource** et le type de **récepteur** est défini sur **OracleSink**.  

    
    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
          {
            "name": "AzureBlobtoOracle",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": "OracleOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "OracleSink"
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


## <a name="oracle-linked-service-properties"></a>Propriétés du service Oracle lié

Le tableau suivant fournit la description pour les éléments JSON spécifiques au service Oracle lié. 

Propriété | Description | Obligatoire
-------- | ----------- | --------
type | La propriété doit être définie : **OnPremisesOracle** | Oui
connectionString | Indiquez les informations nécessaires pour vous connecter à l’instance de base de données Oracle pour la propriété connectionString. | Oui 
nom de la passerelle | Nom de la passerelle qui est utilisé pour la connexion au serveur Oracle en local | Oui

Pour plus d’informations sur la définition des informations d’identification pour une source de données Oracle en local, voir [définir ces informations et sécurité](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) .
## <a name="oracle-dataset-type-properties"></a>Propriétés du type de jeu de données Oracle

Pour une liste complète des sections et les propriétés disponibles pour la définition des groupes de données, voir l’article [Création de groupes de données](data-factory-create-datasets.md) . Sections telles que la structure, la disponibilité et stratégie d’un dataset JSON sont similaires pour tous les types de jeu de données (Oracle, blob Azure, table Azure, etc.).
 
La section typeProperties est différente pour chaque type de jeu de données et fournit des informations sur l’emplacement des données dans le magasin de données. La section typeProperties pour le jeu de données de type OracleTable comporte les propriétés suivantes :

Propriété | Description | Obligatoire
-------- | ----------- | --------
tableName | Nom de la table dans la base de données Oracle le service lié fait référence à. | N° (si **oracleReaderQuery** de **OracleSource** n’est spécifié)

## <a name="oracle-copy-activity-type-properties"></a>Propriétés du type Oracle copie activité

Pour une liste complète des sections et les propriétés disponibles pour la définition des activités, voir l’article [Création Pipelines](data-factory-create-pipelines.md) . Propriétés telles que nom, description, entrée et sortie tables et stratégie sont disponibles pour tous les types d’activités. 

> [AZURE.NOTE]
L’activité de copie vous permet d’accéder qu’une seule entrée et produit une sortie qu’un seul.

Propriétés disponibles dans la section typeProperties de l’activité dépendent quant à eux de chaque type d’activité. Activité de copie, ils varient selon les types de sources et les récepteurs.

### <a name="oraclesource"></a>OracleSource
Dans l’activité de copie, lorsque la source est de type **OracleSource** les propriétés suivantes sont disponibles dans la section **typeProperties** :

Propriété | Description |Valeurs autorisées | Obligatoire
-------- | ----------- | ------------- | --------
oracleReaderQuery | Utiliser la requête personnalisée pour lire les données. | Chaîne de requête SQL. Par exemple : sélectionnez *from MyTable <br/> <br/>le cas contraire, l’instruction SQL qui s’exécute : sélectionnez* from MyTable | N° (si **tableName** du **jeu de données** n’est spécifié)

### <a name="oraclesink"></a>OracleSink
**OracleSink** prend en charge les propriétés suivantes :

Propriété | Description | Valeurs autorisées | Obligatoire
-------- | ----------- | -------------- | --------
writeBatchTimeout | Temps d’attente pour l’opération d’insertion de lot se termine avant son expiration. | TimeSpan<br/><br/> Exemple : 00:30:00 (30 minutes). | N°
writeBatchSize | Insère des données dans la table SQL lorsque la taille de la mémoire tampon atteint writeBatchSize.   | Nombre entier (nombre de lignes)| Non (par défaut : 10000)  
sqlWriterCleanupScript | Spécifier une requête pour l’activité de copie à exécuter telles que les données d’une section spécifique sont nettoyées. | Une instruction de requête. | N°
sliceIdentifierColumnName | Spécifiez le nom de colonne pour l’activité de copie remplir avec identificateur de secteur généré automatiquement, qui est utilisé pour effacer les données d’une section spécifique lorsque exécutez à nouveau. | Nom de colonne d’une colonne par type de données de binary(32). | N°


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-oracle"></a>Mappage du type pour Oracle

Comme indiqué dans l’activité [des activités de mouvement données](data-factory-data-movement-activities.md) article copie exécute des conversions de type automatique à partir de types de sources pour recevoir les types de l’approche 2-étape suivante :

1. Conversion de types de sources native en type .NET
2. Conversion de type .NET en type récepteur natif

Lors du déplacement de données à partir d’Oracle, les correspondances suivantes servent à partir du type de données Oracle au type .NET et vice versa.

Type de données Oracle | Type de données .NET framework
---------------- | ------------------------
BFILE | Byte]
OBJETS BLOB | Byte]
CAR | Chaîne
CLOB | Chaîne
DATE | Date/heure
RENDRE FLOTTANT | Decimal
NOMBRE ENTIER | Decimal
INTERVALLE ANNEE A MOIS | Int32
INTERVALLE JOUR A SECONDE | TimeSpan
LONG | Chaîne
DURÉE BRUTE | Byte]
NCHAR | Chaîne
NCLOB | Chaîne
NOMBRE | Decimal
NVARCHAR2 | Chaîne
BRUT | Byte]
ID DE LIGNE | Chaîne
HORODATAGE | Date/heure
HORODATAGE AVEC FUSEAU HORAIRE LOCAL | Date/heure
HORODATAGE AVEC FUSEAU HORAIRE | Date/heure
ENTIER NON SIGNÉ | Nombre
VARCHAR2 | Chaîne
XML | Chaîne

## <a name="troubleshooting-tips"></a>Conseils de dépannage

**Problème :** Vous voyez le **message d’erreur**suivant : activité de copie remplie paramètres non valides : 'UnknownParameterName', message détaillé : Impossible de trouver le .net demandées fournisseur de données .NET Framework. Il ne peut pas être installé ».  

**Causes possibles :**

1. Le fournisseur de données .NET Framework pour Oracle n’a pas été installé.
2. Le fournisseur de données .NET Framework pour Oracle a été installé à .NET Framework 2.0 et ne trouve pas dans les dossiers .NET Framework 4.0. 

**Résolution/solution de contournement :**

1. Si vous n’avez pas installé le fournisseur .NET pour Oracle, [installez-la](http://www.oracle.com/technetwork/topics/dotnet/downloads/) et réessayez le scénario. 
2. Si vous obtenez le message d’erreur après l’installation du fournisseur, procédez comme suit : 
    1. Ouvrir la configuration de la machine de .NET 2.0 à partir du dossier : <system disk>: \Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
    2. Recherche pour **Le fournisseur de données Oracle pour .NET**et que vous devriez pouvoir rechercher une entrée comme le montre l’unwn exemple suivantes dans le code suivant exemple désactiverder **system.data** -> **DbProviderFactories**:
            “<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />”
2.  Copiez cette entrée dans le fichier machine.config dans le dossier v4.0 suivant : <system disk>: \Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config et changez la version à 4.xxx.x.x.
3.  Installer « < chemin d’accès ODP.NET installé > \11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll » dans le cache d’assembly global (GAC) en exécutant `gacutil /i [provider path]`.



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]


## <a name="performance-and-tuning"></a>Performances et réglage  
Voir [Guide d’optimisation et des performances d’activité de copie](data-factory-copy-activity-performance.md) pour en savoir plus sur les principaux facteurs impact sur les performances de déplacement de données (copie activité) dans Azure Data Factory et de différentes manières d’optimiser.
