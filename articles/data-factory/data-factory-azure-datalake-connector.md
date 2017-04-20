<properties
    pageTitle="Déplacer des données vers/à partir d’Azure données Lake Store | Données Azure usine"
    description="Découvrez comment déplacer des données vers ou à partir d’Azure données Lake Store à l’aide d’usine de données Azure"
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
    ms.date="09/27/2016"
    ms.author="jingwang"/>

# <a name="move-data-to-and-from-azure-data-lake-store-using-azure-data-factory"></a>Déplacer des données vers et depuis Azure données Lake Store à l’aide d’usine de données Azure
Cet article explique comment vous pouvez utiliser l’activité de copie dans une usine de données Azure pour déplacer des données depuis/vers Azure données Lake Store vers ou à partir d’une autre banque de données. Cet article s’appuie sur l’article [activités de déplacement des données](data-factory-data-movement-activities.md) , qui présente une vue d’ensemble de déplacement de données avec l’activité de copie et les combinaisons de magasin de données prises en charge.

> [AZURE.NOTE]
> Créez un compte Azure données Lake Store avant de créer un pipeline avec une activité de copie pour déplacer des données vers ou à partir d’un magasin Lake de données Azure. Pour en savoir plus sur Azure données Lake Store, voir [prise en main Azure données Lake Store](../data-lake-store/data-lake-store-get-started-portal.md).
>  
> Passez en revue [créer votre premier didacticiel pipeline](data-factory-build-your-first-pipeline.md) pour obtenir la procédure détaillée pour créer une usine de données, services liés, jeux de données et une opportunité. Utiliser les extraits JSON avec l’éditeur de données par défaut ou Visual Studio ou Azure PowerShell pour créer les entités Data Factory.

## <a name="copy-data-wizard"></a>Copier l’Assistant de données
Pour créer une opportunité qui copie les données vers ou à partir d’Azure données Lake Store, le plus simple consiste à utiliser l’Assistant copie de données. Voir [didacticiel : créer une opportunité à l’aide de l’Assistant copie](data-factory-copy-data-wizard-tutorial.md) pour un bref aperçu sur la création d’un pipeline à l’aide de l’Assistant copie de données. 

Les exemples suivants fournissent des exemples de définitions JSON que vous pouvez utiliser pour créer une opportunité à l’aide de [portail Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [PowerShell Azure](data-factory-copy-activity-tutorial-using-powershell.md). Ils montrent comment copier des données vers et depuis Azure Data Lake Store et stockage d’objets Blob Azure. Toutefois, les données peuvent être copiées **directement** à partir des sources à un des récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.  


## <a name="sample-copy-data-from-azure-blob-to-azure-data-lake-store"></a>Exemple : Copier des données d’objets Blob Azure au magasin Lake des données Azure
L’exemple suivant montre :

1.  Un service de type [AzureStorage](#azure-storage-linked-service-properties)lié.
2.  Un service de type [AzureDataLakeStore](#azure-data-lake-linked-service-properties)lié.
3.  Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [AzureBlob](#azure-blob-dataset-type-properties).
4.  Un résultat [jeu de données](data-factory-create-datasets.md) de type [AzureDataLakeStore](#azure-data-lake-dataset-type-properties).
4.  [Pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [BlobSource](#azure-blob-copy-activity-type-properties) et [AzureDataLakeStoreSink](#azure-data-lake-copy-activity-type-properties).

L’exemple copie temps série de données à partir d’un stockage d’objets Blob Azure au magasin Lake des données Azure chaque heure. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivantes les exemples.


**Service de stockage lié Azure :**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Azure données Lake lié service :**

    {
        "name": "AzureDataLakeStoreLinkedService",
        "properties": {
            "type": "AzureDataLakeStore",
            "typeProperties": {
                "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
                "sessionId": "<session ID>",
                "authorization": "<authorization URL>"
            }
        }
    }

### <a name="to-create-azure-data-lake-linked-service-using-data-factory-editor"></a>Pour créer Azure Service données Lake liées à l’aide de l’éditeur de données par défaut
La procédure suivante explique comment créer un service d’Azure données Lake banque d’informations liées à l’aide de l’éditeur d’usine de données.

1. Cliquez sur **nouveau magasin de données** dans la barre de commandes et sélectionnez **Azure données Lake Store**.
2. Dans l’éditeur de JSON, pour la propriété **dataLakeStoreUri** , entrez l’URI pour la lake de données.
3. Cliquez sur le bouton **Autoriser** la barre de commandes. Vous devriez voir une fenêtre contextuelle.

    ![Autorisez les bouton](./media/data-factory-azure-data-lake-connector/authorize-button.png)

4. Utilisez vos informations d’identification pour vous connecter et la propriété de **l’autorisation** dans le JSON doit être affectée à une valeur maintenant.
5. (facultatif) Spécifier des valeurs pour les paramètres facultatifs tels que **nom de compte**et **subscriptionID** **resourceGroupName** dans le JSON (ou) supprimer ces propriétés à partir de la JSON.
6. Dans la barre de commande pour déployer le service lié, cliquez sur **déployer** .

> [AZURE.IMPORTANT] Le code d’autorisation que vous avez généré à l’aide du bouton **Autoriser** expire après un certain temps. **Autoriser à nouveau** à l’aide de l' **Autoriser** bouton lorsque le **jeton a expiré** et redéploiement le service lié. Pour plus d’informations, reportez-vous à la section [Azure données Lake Store Service liées](#azure-data-lake-store-linked-service-properties) . 



**Dataset d’entrée des objets Blob Azure :**

Données sont provenant d’un objet blob de nouveau toutes les heures (fréquence : heure, l’intervalle : 1). Le nom du fichier et le chemin dossier pour l’objets blob sont évaluées dynamiquement en fonction de l’heure de début de la section en cours de traitement. Le chemin du dossier utilise année, mois et partie jour de l’heure de début et nom de fichier utilise la partie heure de l’heure de début. « externes » : paramètre « true » vous informe service Data Factory que la table en dehors de l’usine de données et n’est pas produite par une activité dans le factory de données.

    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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


**Jeu de données de sortie Lake de données Azure :**

L’exemple de copie des données dans un magasin Lake de données Azure. Nouvelles données sont copie aux données Lake stocker chaque heure.

    {
        "name": "AzureDataLakeStoreOutput",
        "properties": {
            "type": "AzureDataLakeStore",
            "linkedServiceName": "AzureDataLakeStoreLinkedService",
            "typeProperties": {
                "folderPath": "datalake/output/"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }



**Pipeline avec une activité de copie :**

Le pipeline contient une activité de copie qui est configuré pour utiliser les jeux de données d’entrée et de sortie et planifiée pour chaque heure. Dans le pipeline de définition de JSON, le type de **source** est défini sur **BlobSource** et **récepteur de** type est défini sur **AzureDataLakeStoreSink**.

    {  
        "name":"SamplePipeline",
        "properties":
        {  
            "start":"2014-06-01T18:00:00",
            "end":"2014-06-01T19:00:00",
            "description":"pipeline with copy activity",
            "activities":
            [  
                {
                    "name": "AzureBlobtoDataLake",
                    "description": "Copy Activity",
                    "type": "Copy",
                    "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                    ],
                    "outputs": [
                    {
                        "name": "AzureDataLakeStoreOutput"
                    }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource",
                            "treatEmptyAsNull": true,
                            "blobColumnSeparators": ","
                        },
                        "sink": {
                            "type": "AzureDataLakeStoreSink"
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

## <a name="sample-copy-data-from-azure-data-lake-store-to-azure-blob"></a>Exemple : Copier des données à partir d’Azure données Lake Store dans Blob Azure
L’exemple suivant montre :

1.  Un service de type [AzureDataLakeStore](#azure-data-lake-linked-service-properties)lié.
2.  Un service de type [AzureStorage](#azure-storage-linked-service-properties)lié.
3.  Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [AzureDataLakeStore](#azure-data-lake-dataset-type-properties).
4.  Un résultat [jeu de données](data-factory-create-datasets.md) de type [AzureBlob](#azure-blob-dataset-type-properties).
5.  Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [AzureDataLakeStoreSource](#azure-data-lake-copy-activity-type-properties) et [BlobSink](#azure-blob-copy-activity-type-properties)

L’exemple copie temps série de données à partir d’un magasin Lake de données Azure dans un blob Azure toutes les heures. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivantes les exemples.

**Magasin de Lake données Azure lié service :**

    {
        "name": "AzureDataLakeStoreLinkedService",
        "properties": {
            "type": "AzureDataLakeStore",
            "typeProperties": {
                "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
                "sessionId": "<session ID>",
                "authorization": "<authorization URL>"
            }
        }
    }

> [AZURE.NOTE] Consultez les étapes décrites dans l’exemple précédent pour obtenir l’URL d’autorisation.  

**Service de stockage lié Azure :**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Azure données Lake dataset d’entrée :**

Définition de **« externes » : true** vous informe le service de données usine que la table en dehors de l’usine de données et n’est pas produite par une activité dans le factory de données.

    {
        "name": "AzureDataLakeStoreInput",
        "properties":
        {
            "type": "AzureDataLakeStore",
            "linkedServiceName": "AzureDataLakeStoreLinkedService",
            "typeProperties": {
                "folderPath": "datalake/input/",
                "fileName": "SearchLog.tsv",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
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

Le pipeline contient une activité de copie qui est configuré pour utiliser les jeux de données d’entrée et de sortie et planifiée pour chaque heure. Dans le pipeline de définition de JSON, le type de **source** est défini sur **AzureDataLakeStoreSource** et **récepteur de** type est défini sur **BlobSink**.


    {  
        "name":"SamplePipeline",
        "properties":{  
            "start":"2014-06-01T18:00:00",
            "end":"2014-06-01T19:00:00",
            "description":"pipeline for copy activity",
            "activities":[  
                {
                    "name": "AzureDakeLaketoBlob",
                    "description": "copy activity",
                    "type": "Copy",
                    "inputs": [
                      {
                        "name": "AzureDataLakeStoreInput"
                      }
                    ],
                    "outputs": [
                      {
                        "name": "AzureBlobOutput"
                      }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "AzureDataLakeStoreSource",
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


## <a name="azure-data-lake-store-linked-service-properties"></a>Propriétés de Service de données Lake banque liées Azure

Vous pouvez lier un compte de stockage Azure à une usine de données Azure à l’aide d’un service de stockage Azure liées. Le tableau suivant fournit la description pour les éléments JSON spécifiques au service de stockage Azure lié.

| Propriété | Description | Obligatoire |
| :-------- | :----------- | :-------- |
| type | La propriété doit être définie : **AzureDataLakeStore** | Oui |
| dataLakeStoreUri | Spécifier des informations sur le compte Azure données Lake Store. Il est au format suivant : https://<Azure Data Lake account name>.azuredatalakestore.net/webhdfs/v1 | Oui |
| autorisation | Cliquez sur le bouton **Autoriser** dans l' **Éditeur de Factory de données** et entrez vos informations d’identification attribué par l’URL d’autorisation généré à cette propriété.  | Oui |
| ID de session | Id de session OAuth à partir de la session d’autorisation oauth. Chaque id de session est unique et ne peut être utilisé une seule fois. Ce paramètre est généré automatiquement lorsque vous utilisez l’éditeur de données par défaut. | Oui |  
| nom de compte | Nom du compte lake données | N° |
| subscriptionId | Abonnement Azure identifiant. | Non (le cas contraire, abonnement d’usine données est utilisé). |
| resourceGroupName |  Nom du groupe ressource Azure | Non (le cas contraire, groupe de ressources de la factory de données est utilisé). |

## <a name="token-expiration"></a>Expiration du jeton 
Le code d’autorisation que vous générez à l’aide du bouton **Autoriser** expire après un certain temps. Consultez le tableau suivant pour les horaires d’expiration pour les différents types de comptes d’utilisateurs. Vous pouvez rencontrer l’erreur suivante message lorsque l’authentification **jeton a expiré**: « erreur d’opération des informations d’identification : invalid_grant - AADSTS70002 : erreur de validation des informations d’identification. AADSTS70008 : Le droit d’accès fourni est expiré ou révoqué. ID de suivi : ID de corrélation d18629e8-af88-43c5-88e3-d8419eb1fca1 : horodatage fac30a0c-6be6-4e02-8d69-a776d2ffefd7 : 2015-12-15 21-09-31Z ».


| Type d’utilisateur | Expire après |
| :-------- | :----------- | 
| Comptes d’utilisateurs non gérés par Azure Active Directory (@hotmail.com, @live.com, etc..). | 12 heures |
| Comptes utilisateurs gérés par Azure Active Directory (DAS) | 14 jours après la dernière tranche exécuter. <br/><br/>90 jours, si un secteur basé sur service lié basé sur un jeton s’exécute au moins une fois tous les 14 jours. |

Si vous changez votre mot de passe avant cette date d’expiration du jeton, le jeton expire immédiatement et vous voyez l’erreur mentionné dans cette section. 

Pour éviter/résoudre cette erreur, autoriser à l’aide de l' **Autoriser** à nouveau le bouton lorsque le **jeton a expiré** et redéploiement le service lié. Vous pouvez également générer des valeurs pour les propriétés de **l’ID de session** et **d’autorisation** par programme à l’aide de code dans la section suivante :

### <a name="to-programmatically-generate-sessionid-and-authorization-values"></a>Pour générer des valeurs d’ID de session et d’autorisation par programme 

    if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
        linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
    {
        AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

        WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
        string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

        AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
        if (azureDataLakeStoreProperties != null)
        {
            azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeStoreProperties.Authorization = authorization;
        }

        AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
        if (azureDataLakeAnalyticsProperties != null)
        {
            azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeAnalyticsProperties.Authorization = authorization;
        }
    }

Consultez les rubriques [AzureDataLakeStoreLinkedService cours](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [Classe AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)et [Classe AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) pour plus d’informations sur les classes usine de données utilisées dans le code. Ajoutez une référence à la version **2.9.10826.1824** de **Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll** de la classe WindowsFormsWebAuthenticationDialog utilisée dans le code. 
 

## <a name="azure-data-lake-dataset-type-properties"></a>Propriétés du type Dataset Lake de données Azure

Pour une liste complète des sections JSON et les propriétés disponibles pour la définition des groupes de données, voir l’article [Création de groupes de données](data-factory-create-datasets.md) . Sections telles que la structure, la disponibilité et stratégie d’un dataset JSON sont identiques pour tous les types de jeu de données (SQL Azure, blob Azure, table Azure, etc..).

La section **typeProperties** est différente pour chaque type de jeu de données et fournit des informations à propos de l’emplacement, format etc., des données dans le magasin de données. La section typeProperties pour le jeu de données de type dataset **AzureDataLakeStore** comporte les propriétés suivantes :

| Propriété | Description | Obligatoire |
| :-------- | :----------- | :-------- |
| folderPath | Chemin d’accès au conteneur et au dossier dans le Lake données Azure stocker. | Oui |
| nom de fichier | Nom du fichier dans le magasin Lake de données Azure. nom de fichier est facultative et la casse. <br/><br/>Si vous spécifiez un nom de fichier, l’activité (y compris les copier) fonctionne sur le fichier spécifique.<br/><br/>Lorsque le nom de fichier n’est pas spécifié, copie comprend tous les fichiers dans le folderPath pour le jeu de données d’entrée.<br/><br/>Lorsque le nom de fichier n’est pas spécifié pour un dataset de sortie, le nom du fichier généré susceptible de figurer dans ce qui suit ce format : données. <Guid>.txt (par exemple : : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt | N° |
| partitionedBy | partitionedBy est une propriété facultative. Vous pouvez l’utiliser pour spécifier un folderPath dynamique et un nom de fichier de données de série de date. Par exemple, folderPath peut être paramétrée pour toutes les heures de données. Consultez la section [utilisation partitionedBy propriété](#using-partitionedby-property) des détails et des exemples. | N° |
| mettre en forme | Les types de formats suivants sont pris en charge : **format du texte**, **AvroFormat**, **JsonFormat**, **OrcFormat**, **ParquetFormat**. Définissez **la propriété sous format** à un de ces valeurs. Voir [Format du texte spécifiant](#specifying-textformat), [AvroFormat spécifiant](#specifying-avroformat), [JsonFormat spécifiant](#specifying-jsonformat), [OrcFormat spécifiant](#specifying-orcformat), [ParquetFormat spécifiant](#specifying-parquetformat) sections et pour plus d’informations. Si vous voulez copier des fichiers en tant que-est entre banques basée sur le fichier (copie binaire), vous pouvez ignorer la section format dans des définitions de jeu de données d’entrée et de sortie.| N°
| compression | Spécifier le type et le niveau de compression pour les données. Types pris en charge sont : **GZip**, **Deflate**et **BZip2** et les niveaux de prise en charge : **Optimal** et **plus rapide**. Pour l’instant, les paramètres de compression ne sont pas prises en charge des données dans **AvroFormat** ou **OrcFormat**. Pour plus d’informations, reportez-vous à la section [prise en charge de la Compression](#compression-support) .  | N° |

### <a name="using-partitionedby-property"></a>À l’aide de la propriété partitionedBy
Vous pouvez spécifier un folderPath dynamique et un nom de fichier de données de série de date avec la section **partitionedBy** , les macros de données usine et les variables système : SliceStart et SliceEnd, qui indique les heures de début et de fin pour une section donnée.

Voir les articles [Créer des groupes de données](data-factory-create-datasets.md) et la [planification et l’exécution](data-factory-scheduling-and-execution.md) de comprendre plus d’informations sur les jeux de données de série heure, en planifiant et secteurs.

#### <a name="sample-1"></a>Exemple 1

    "folderPath": "wikidatagateway/wikisampledataout/{Slice}",
    "partitionedBy":
    [
        { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
    ],

Dans cet exemple, {section} est remplacée par la valeur de la variable Data Factory système SliceStart au format (YYYYMMDDHH) spécifiée. La SliceStart fait référence pour l’heure de début de la section. La folderPath est différente pour chaque secteur. Par exemple : wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104

#### <a name="sample-2"></a>Exemple 2

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
 

### <a name="compression-support"></a>Prise en charge de la compression  
Traitement de grands ensembles de données peut provoquer des engorgements e/s et du réseau. Par conséquent, les données compressées dans stores pouvant non seulement accélérer le transfert de données sur le réseau et économiser de l’espace disque, mais également apportent des améliorations de performances de manière significative dans le traitement des données volumineuses. Pour l’instant, banques de données orientés fichier tels que les objets Blob Azure ou de système de fichiers en local est prise en charge la compression.  

Pour spécifier la compression d’un jeu de données, utilisez la propriété **compression** du DataSet JSON comme dans l’exemple suivant :   

    {  
        "name": "AzureDatalakeStoreDataSet",  
        "properties": {  
            "availability": {  
                "frequency": "Day",  
                "interval": 1  
            },  
            "type": "AzureDatalakeStore",  
            "linkedServiceName": "DataLakeStoreLinkedService",  
            "typeProperties": {  
                "fileName": "pagecounts.csv.gz",  
                "folderPath": "compression/file/",  
                "compression": {  
                    "type": "GZip",  
                    "level": "Optimal"  
                }  
            }  
        }  
    }  
 
La section **compression** comporte deux propriétés :  
  
- **Type :** le codec de compression, qui peut être **GZIP**, **Deflate** ou **BZIP2**.  
- **Niveau :** le taux de compression, qui peut être **optimale** ou **plus rapide**. 
    - **Plus rapide :** L’opération de compression doit se terminer aussi rapidement que possible, même si le fichier qui en résulte n’est pas optimale compressé. 
    - **Optimale**: l’opération de compression doit être optimale compressée, même si l’opération prend plus de temps. 
    
    Pour plus d’informations, voir la rubrique [Niveau de Compression](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . 

Supposons que le jeu de données d’exemple est utilisée comme la sortie d’une activité de copie. L’activité de copie compresse les données de sortie avec codec GZIP à l’aide de rapport optimal, puis écrit les données compressées dans un fichier nommé pagecounts.csv.gz dans le magasin Lake de données Azure.   

Lorsque vous spécifiez propriété compression dans un jeu de données d’entrée JSON, le pipeline de lit les données compressées dans la source. Lorsque vous spécifiez la propriété dans un jeu de données de sortie JSON, l’activité de copie pouvez écrire des données compressées à la destination. Voici quelques exemples de scénarios : 

- Lecture GZIP compressées les données à partir d’un magasin Lake de données Azure, décompresser et écrire des données de résultat dans une base de données SQL Azure. Vous définissez le dataset Azure données Lake Store d’entrée avec la compression propriété JSON dans ce cas. 
- Lire les données à partir d’un fichier texte brut à partir de système de fichiers en local, à l’aide du format GZip compresser et écrire les données compressées dans un magasin Lake de données Azure. Vous définissez un dataset sortie données Azure Lake avec la compression propriété JSON dans ce cas.  
- Lire un compressés GZIP de données à partir d’un magasin Azure données Lake décompresser, à l’aide de BZIP2 compresser et écrire des données de résultat dans un magasin Lake de données Azure. Vous définissez compression type défini comme GZIP et BZIP2 pour l’entrée et sortie jeux de données, respectivement.   


## <a name="azure-data-lake-copy-activity-type-properties"></a>Propriétés du type de données Lake copie activité Azure  
Pour une liste complète des sections et les propriétés disponibles pour la définition des activités, voir l’article [Création Pipelines](data-factory-create-pipelines.md) . Propriétés telles que nom, description, entrée et sortie tables et stratégie sont disponibles pour tous les types d’activités.

Propriétés disponibles dans la section typeProperties de l’activité dépendent quant à eux de chaque type d’activité. Activité de copie, ils peuvent varier selon les types de sources et les récepteurs

**AzureDataLakeStoreSource** prend en charge la section **typeProperties** propriétés suivantes :

| Propriété | Description | Valeurs autorisées | Obligatoire |
| -------- | ----------- | -------------- | -------- |
| récursives | Indique si les données soient lues de manière récursive dans les sous-dossiers ou uniquement dans le dossier spécifié. | Vrai (valeur par défaut), faux | N° |



**AzureDataLakeStoreSink** prend en charge la section **typeProperties** propriétés suivantes :

| Propriété | Description | Valeurs autorisées | Obligatoire |
| -------- | ----------- | -------------- | -------- |
| copyBehavior | Spécifie le comportement de copie. | **PreserveHierarchy :** conserve la hiérarchie des fichiers dans le dossier cible. Le chemin d’accès relatif du fichier source à dossier source est identique à la relative chemin d’accès du fichier cible dans le dossier cible.<br/><br/>**FlattenHierarchy :** tous les fichiers à partir du dossier source sont créés dans le premier niveau du dossier cible. Les fichiers cible sont créées avec nom générée automatiquement.<br/><br/>**MergeFiles :** fusionne tous les fichiers à partir du dossier source vers un fichier. Si le nom de fichier/Blob n’est spécifié, le nom de fichier fusionné serait le nom spécifié ; dans le cas contraire, serait nom de fichier généré automatiquement. | N° |


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>Performances et réglage  
Voir [Guide d’optimisation et des performances d’activité de copie](data-factory-copy-activity-performance.md) pour en savoir plus sur les principaux facteurs impact sur les performances de déplacement de données (copie activité) dans Azure Data Factory et de différentes manières d’optimiser.
