<properties 
    pageTitle="Déplacer des données vers ou depuis DocumentDB | Microsoft Azure" 
    description="Découvrez comment déplacer des données vers/à partir de la collection de DocumentDB Azure à l’aide d’usine de données Azure" 
    services="data-factory, documentdb" 
    documentationCenter="" 
    authors="linda33wj" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="multiple" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="jingwang"/>

# <a name="move-data-to-and-from-documentdb-using-azure-data-factory"></a>Déplacer des données vers et depuis DocumentDB à l’aide d’usine de données Azure

Cet article explique comment vous pouvez utiliser l’activité de copie dans une usine de données Azure pour déplacer des données Azure DocumentDB à partir des données d’un autre stockent et déplacement des données de DocumentDB vers une autre banque de données. Cet article s’appuie sur l’article [activités de déplacement des données](data-factory-data-movement-activities.md) , qui présente une vue d’ensemble de déplacement de données avec l’activité de copie et les combinaisons de magasin de données prises en charge.

Les exemples suivants illustrent comment copier des données vers et depuis DocumentDB Azure et de stockage d’objets Blob Azure. Toutefois, les données peuvent être copiées **directement** à partir des sources à un des récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.  

> [AZURE.NOTE] Copie des données à partir des données Azure/le locaux IaaS stocke à Azure DocumentDB et inversement sont prises en charge avec la passerelle de gestion des données version 2.1 et au-dessus.

## <a name="sample-copy-data-from-documentdb-to-azure-blob"></a>Exemple : Copier des données à partir de DocumentDB vers Blob Azure

L’exemple ci-dessous montre :

1. Un service de type [DocumentDb](#azure-documentdb-linked-service-properties)lié.
2. Un service de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)lié. 
3. Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [DocumentDbCollection](#azure-documentdb-dataset-type-properties). 
4. Un résultat [jeu de données](data-factory-create-datasets.md) de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. [Pipeline](data-factory-create-pipelines.md) avec une activité copie qui utilise [DocumentDbCollectionSource](#azure-documentdb-copy-activity-type-properties) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L’exemple copie Azure DocumentDB vers Blob Azure. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivantes les exemples.

**Service DocumentDB lié Azure :**

    {
      "name": "DocumentDbLinkedService",
      "properties": {
        "type": "DocumentDb",
        "typeProperties": {
          "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
      }
    }

**Stockage d’objets Blob Azure lié service :**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Azure dataset d’entrée de Document de base de données :**

L’exemple suppose que vous disposez d’une collection nommée **personne** dans une base de données Azure DocumentDB.
 
Définition de « externes » : « true » et en spécifiant des informations de stratégie externalData le service Azure Data Factory que la table en dehors de l’usine de données et ne présenté par une activité dans le factory de données.

    {
      "name": "PersonDocumentDbTable",
      "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "DocumentDbLinkedService",
        "typeProperties": {
          "collectionName": "Person"
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }


**Jeu de données de sortie Blob Azure :**

Données sont copiées dans un nouveau blob toutes les heures par le chemin d’accès pour l’objets blob qui reflète la date/heure spécifique avec précision de l’heure.

    {
      "name": "PersonBlobTableOut",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "docdb",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "nullValue": "NULL"
          }
        },
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

Exemple de document JSON dans la collection de la personne dans une base de données DocumentDB : 

    {
      "PersonId": 2,
      "Name": {
        "First": "Jane",
        "Middle": "",
        "Last": "Doe"
      }
    }

DocumentDB prend en charge les documents interrogation à l’aide d’un SQL comme syntaxe des documents JSON hiérarchiques. 

Exemple : Sélectionnez Person.Name.Middle Person.PersonId, Person.Name.First comme prénom, comme MiddleName, Person.Name.Last comme nom de personne

Le pipeline suivant copie les données à partir de la collection de la personne dans la base de données DocumentDB à un blob Azure. Dans le cadre de l’activité de copier l’entrée et sortie jeux de données a été spécifiée.  
    
    {
      "name": "DocDbToBlobPipeline",
      "properties": {
        "activities": [
          {
            "type": "Copy",
            "typeProperties": {
              "source": {
                "type": "DocumentDbCollectionSource",
                "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
                "nestingSeparator": "."
              },
              "sink": {
                "type": "BlobSink",
                "blobWriterAddHeader": true,
                "writeBatchSize": 1000,
                "writeBatchTimeout": "00:00:59"
              }
            },
            "inputs": [
              {
                "name": "PersonDocumentDbTable"
              }
            ],
            "outputs": [
              {
                "name": "PersonBlobTableOut"
              }
            ],
            "policy": {
              "concurrency": 1
            },
            "name": "CopyFromDocDbToBlob"
          }
        ],
        "start": "2015-04-01T00:00:00Z",
        "end": "2015-04-02T00:00:00Z"
      }
    }

## <a name="sample-copy-data-from-azure-blob-to-azure-documentdb"></a>Exemple : Copier des données d’objets Blob Azure à DocumentDB Azure

L’exemple ci-dessous montre :

1. Un service de type [DocumentDb](#azure-documentdb-linked-service-properties)lié.
2. Un service de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)lié.
3. Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Un résultat [jeu de données](data-factory-create-datasets.md) de type [DocumentDbCollection](#azure-documentdb-dataset-type-properties). 
4. [Pipeline](data-factory-create-pipelines.md) avec une activité copie qui utilise [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) et [DocumentDbCollectionSink](#azure-documentdb-copy-activity-type-properties).


L’exemple de copie des données à partir d’Azure blob à Azure DocumentDB. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivantes les exemples.

**Stockage d’objets Blob Azure lié service :**
    
    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Service DocumentDB lié Azure :**
    
    {
      "name": "DocumentDbLinkedService",
      "properties": {
        "type": "DocumentDb",
        "typeProperties": {
          "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
      }
    }

**Dataset d’entrée des objets Blob Azure :**

    {
      "name": "PersonBlobTableIn",
      "properties": {
        "structure": [
          {
            "name": "Id",
            "type": "Int"
          },
          {
            "name": "FirstName",
            "type": "String"
          },
          {
            "name": "MiddleName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
          }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "fileName": "input.csv",
          "folderPath": "docdb",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "nullValue": "NULL"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

**Jeu de données de sortie DocumentDB Azure :**

L’exemple de copie des données dans une collection nommée « Personne ».

    {
      "name": "PersonDocumentDbTableOut",
      "properties": {
        "structure": [
          {
            "name": "Id",
            "type": "Int"
          },
          {
            "name": "Name.First",
            "type": "String"
          },
          {
            "name": "Name.Middle",
            "type": "String"
          },
          {
            "name": "Name.Last",
            "type": "String"
          }
        ],
        "type": "DocumentDbCollection",
        "linkedServiceName": "DocumentDbLinkedService",
        "typeProperties": {
          "collectionName": "Person"
        },
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

Le pipeline suivant copie des données d’objets Blob Azure à la collection de la personne dans la DocumentDB. Dans le cadre de l’activité de copier l’entrée et sortie jeux de données a été spécifiée. 
    
    {
      "name": "BlobToDocDbPipeline",
      "properties": {
        "activities": [
          {
            "type": "Copy",
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "DocumentDbCollectionSink",
                "nestingSeparator": ".",
                "writeBatchSize": 2,
                "writeBatchTimeout": "00:00:00"
              }
              "translator": {
                  "type": "TabularTranslator",
                  "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix, EmailPromotion: EmailPromotion, rowguid: rowguid, ModifiedDate: ModifiedDate"
              }
            },
            "inputs": [
              {
                "name": "PersonBlobTableIn"
              }
            ],
            "outputs": [
              {
                "name": "PersonDocumentDbTableOut"
              }
            ],
            "policy": {
              "concurrency": 1
            },
            "name": "CopyFromBlobToDocDb"
          }
        ],
        "start": "2015-04-14T00:00:00Z",
        "end": "2015-04-15T00:00:00Z"
      }
    }
 
Si l’entrée d’objets blob exemple est en tant que 

    1,John,,Doe

Puis le résultat JSON dans DocumentDB sera en tant que :

    {
      "Id": 1,
      "Name": {
        "First": "John",
        "Middle": null,
        "Last": "Doe"
      },
      "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
    }
    
DocumentDB est un magasin NoSQL pour les documents JSON, où des structures imbriquées sont autorisées. Azure Data Factory permet à l’utilisateur indiquer la hiérarchie via **nestingSeparator**, ce qui correspond à «. » Dans cet exemple. Avec le séparateur, l’activité de copie générera l’objet « Nom » avec tous les éléments trois enfants tout d’abord, deuxième prénom et, en fonction de « Name.First », « Name.Middle » et « Name.Last » dans la définition de table.

## <a name="azure-documentdb-linked-service-properties"></a>Propriétés du Service de liées DocumentDB Azure

Le tableau suivant fournit la description pour les éléments JSON spécifiques au service DocumentDB Azure lié. 

| **Propriété** | **Description** | **Obligatoire** |
| -------- | ----------- | --------- |
| type | La propriété doit être définie : **DocumentDb** | Oui |
| connectionString | Indiquez les informations nécessaires pour vous connecter à la base de données Azure DocumentDB. | Oui |

## <a name="azure-documentdb-dataset-type-properties"></a>Propriétés de type DocumentDB Dataset Azure

Pour une liste complète des sections et les propriétés disponibles pour la définition des groupes de données, veuillez consulter l’article [Création de groupes de données](data-factory-create-datasets.md) . Sections, tels que structure disponibilité et la stratégie d’un dataset JSON sont identiques pour tous les types de jeu de données (SQL Azure, blob Azure, table Azure, etc..).
 
La section typeProperties est différente pour chaque type de jeu de données et fournit des informations sur l’emplacement des données dans le magasin de données. La section typeProperties pour le jeu de données de type **DocumentDbCollection** possède les propriétés suivantes.

| **Propriété** | **Description** | **Obligatoire** |
| -------- | ----------- | -------- |
| collectionName | Nom de la collection de documents DocumentDB. | Oui |


Exemple :

    {
      "name": "PersonDocumentDbTable",
      "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "DocumentDbLinkedService",
        "typeProperties": {
          "collectionName": "Person"
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

### <a name="schema-by-data-factory"></a>Schéma de données par défaut
Pour les données sans schéma banques tels que DocumentDB, le service de données usine déduit le schéma d’une des façons suivantes :  

1.  Si vous spécifiez la structure des données à l’aide de la propriété **structure** dans la définition du jeu de données, le service de données usine respecte cette structure que le schéma. Dans ce cas, si une ligne ne contient pas une valeur pour une colonne, une valeur null sera fournie pour lui.
2.  Si vous ne spécifiez pas la structure des données à l’aide de la propriété **structure** dans la définition du jeu de données, le service de données usine déduit le schéma à l’aide de la première ligne dans les données. Dans ce cas, si la première ligne ne contient pas le schéma complet, certaines colonnes seront manquantes dans le résultat de l’opération de copie.

Par conséquent, pour les sources de données sans schéma, la meilleure solution consiste à définir la structure de données à l’aide de la propriété **structure** .

## <a name="azure-documentdb-copy-activity-type-properties"></a>Propriétés de type DocumentDB copie activité Azure

Pour une liste complète des sections et les propriétés disponibles pour la définition des activités, veuillez consulter l’article [Création Pipelines](data-factory-create-pipelines.md) . Propriétés telles que nom, description, entrée et sortie tables et stratégie sont disponibles pour tous les types d’activités.
 
**Remarque :** L’activité de copie vous permet d’accéder qu’une seule entrée et produit une sortie qu’un seul.

Propriétés disponibles dans la section typeProperties de l’activité dépendent quant à eux de chaque type d’activité et en cas d’activité de copie qu’ils peuvent varier selon les types de sources et les récepteurs.

En cas d’activité de copie lors de la source est de type **DocumentDbCollectionSource** les propriétés suivantes sont disponibles dans la section **typeProperties** :

| **Propriété** | **Description** | **Valeurs autorisées** | **Obligatoire** |
| ------------ | --------------- | ------------------ | ------------ |
| requête | Spécifiez la requête pour lire les données. | Prise en charge par DocumentDB de chaîne de requête. <br/><br/>Exemple :`SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` | N° <br/><br/>Le cas contraire, l’instruction SQL qui s’exécute :`select <columns defined in structure> from mycollection` 
| nestingSeparator | Caractère spécial pour indiquer que le document est imbriqué | Tout caractère. <br/><br/>DocumentDB est un magasin NoSQL pour les documents JSON, où des structures imbriquées sont autorisées. Azure Data Factory permet à l’utilisateur indiquer la hiérarchie via nestingSeparator, ce qui correspond à «. » dans les exemples ci-dessus. Avec le séparateur, l’activité de copie générera l’objet « Nom » avec tous les éléments trois enfants tout d’abord, deuxième prénom et, en fonction de « Name.First », « Name.Middle » et « Name.Last » dans la définition de table. | N°

**DocumentDbCollectionSink** prend en charge les propriétés suivantes :

| **Propriété** | **Description** | **Valeurs autorisées** | **Obligatoire** |
| -------- | ----------- | -------------- | -------- |
| nestingSeparator | Un caractère spécial dans le nom de colonne source pour indiquer que ce document imbriqué est nécessaire. <br/><br/>Par exemple ci-dessus : `Name.First` dans le résultat de la table génère la structure JSON suivante dans le document DocumentDB :<br/><br/>« Nom » : {}<br/>  « Prénom » : « John »<br/>}, | Caractère qui est utilisé pour séparer les niveaux d’imbrication.<br/><br/>Valeur par défaut est `.` (point). | Caractère qui est utilisé pour séparer les niveaux d’imbrication. <br/><br/>Valeur par défaut est `.` (point). | N° | 
| writeBatchSize | Nombre de requêtes en parallèle au service DocumentDB pour créer des documents.<br/><br/>Vous pouvez affiner les performances lors de la copie de données vers ou à partir de DocumentDB à l’aide de cette propriété. Vous pouvez attendre de meilleures performances lorsque vous augmentez writeBatchSize, car plus de requêtes en parallèle à DocumentDB sont envoyés. Toutefois, vous devez éviter les limitations qui peut lever le message d’erreur : « Demander taux est grande ».<br/><br/>La limitation est déterminée par un certain nombre de facteurs, y compris la taille des documents, nombre de termes dans les documents, l’indexation de la stratégie de collection de sites cible, etc.. Pour les opérations de copie, vous pouvez utiliser une collection de sites une meilleure (par exemple, S3) d’avoir le meilleur parti de débit disponible (2 500 demande unités/seconde). | Nombre entier | Non (par défaut : 10000) |
| writeBatchTimeout | Temps d’attente pour l’opération se termine avant son expiration. | TimeSpan<br/><br/> Exemple : « 00 : 30:00 » (30 minutes). | N° |
 
## <a name="appendix"></a>Appendice
1. **Question :**  
    quoi la mise à jour de la prise en charge activité de copie des enregistrements existants ?

    **Answer :**  
    non.

2. **Question :**  
    en quoi une nouvelle tentative d’une copie à DocumentDB affaire avec déjà copié enregistrements ?

    **Answer :**  
    si enregistrements avec un champ « ID » et que l’opération de copie essaie d’insérer un enregistrement avec le même ID, l’opération de copie génère une erreur.  
 
3. **Question :** 
    Data Factory prend-il [plage ou partitions de données basé sur hachage](https://azure.microsoft.com/documentation/articles/documentdb-partition-data/)? 

    **Answer :** 
    non. 
4. **Question :** 
    puis-je spécifier plusieurs collections DocumentDB pour une table ?
    
    **Answer :** 
    non. Une collection de sites peut être spécifié pour le moment.
     
## <a name="performance-and-tuning"></a>Performances et réglage  
Voir [Guide d’optimisation et des performances d’activité de copie](data-factory-copy-activity-performance.md) pour en savoir plus sur les principaux facteurs impact sur les performances de déplacement de données (copie activité) dans Azure Data Factory et de différentes manières d’optimiser.
