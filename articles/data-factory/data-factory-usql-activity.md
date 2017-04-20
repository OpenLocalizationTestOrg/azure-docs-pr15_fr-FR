<properties 
    pageTitle="Exécuter un script U SQL sur Azure données Lake Analytique provenant d’une usine de données Azure" 
    description="Découvrez comment traiter les données en exécutant des scripts U-SQL Azure données Lake Analytique cluster service." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="spelluru"/>

# <a name="run-u-sql-script-on-azure-data-lake-analytics-from-azure-data-factory"></a>Exécuter un script U SQL sur Azure données Lake Analytique provenant d’une usine de données Azure
> [AZURE.SELECTOR]
[Hive](data-factory-hive-activity.md)  
[Cochon](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Diffusion en continu Hadoop](data-factory-hadoop-streaming-activity.md)
[Apprentissage automatique](data-factory-azure-ml-batch-execution-activity.md) 
[Procédure stockée](data-factory-stored-proc-activity.md)
[Données Lake Analytique U SQL](data-factory-usql-activity.md)
[.NET personnalisé](data-factory-use-custom-activities.md)
 
Un pipeline dans une usine de données Azure traite les données dans les services de stockage liées à l’aide liée cluster services. Il contient une séquence d’activités dans lequel chaque activité effectue une opération de traitement spécifiques. Cet article décrit l' **Activité des données Lake Analytique U-SQL** qui s’exécute un script **U-SQL** sur un service de cluster lié **Azure données Lake Analytique** . 

> [AZURE.NOTE] 
> Créez un compte Azure données Lake Analytique avant de créer un pipeline avec une activité données Lake Analytique U-SQL. Pour en savoir plus sur Azure données Lake Analytique, voir [prise en main Azure données Lake Analytique](../data-lake-analytics/data-lake-analytics-get-started-portal.md).
>  
> Passez en revue [créer votre premier didacticiel pipeline](data-factory-build-your-first-pipeline.md) pour obtenir la procédure détaillée pour créer une usine de données, services liés, jeux de données et une opportunité. Utiliser extraits JSON avec l’éditeur de données par défaut ou Visual Studio ou Azure PowerShell pour créer des entités Data Factory.

## <a name="azure-data-lake-analytics-linked-service"></a>Données Azure Lake Analytique lié Service
Vous créez un service **Azure données Lake Analytique** lié pour créer un lien à une usine de données Azure un service de cluster Azure données Lake Analytique. L’activité de données Lake Analytique U-SQL dans le pipeline fait référence à ce service lié. 

L’exemple suivant fournit la définition de JSON pour un service Azure données Lake Analytique liées. 

    {
        "name": "AzureDataLakeAnalyticsLinkedService",
        "properties": {
            "type": "AzureDataLakeAnalytics",
            "typeProperties": {
                "accountName": "adftestaccount",
                "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
                "authorization": "<authcode>",
                "sessionId": "<session ID>", 
                "subscriptionId": "<subscription id>",
                "resourceGroupName": "<resource group name>"
            }
        }
    }


Le tableau suivant décrit les propriétés utilisées dans la définition de JSON. 

Propriété | Description | Obligatoire
-------- | ----------- | --------
Type | La propriété type doit être définie sur : **AzureDataLakeAnalytics**. | Oui
nom de compte | Données Azure Lake Analytique le nom du compte. | Oui
dataLakeAnalyticsUri | Données Azure Lake Analytique URI. |  N° 
autorisation | Code d’autorisation est automatiquement extrait après en cliquant sur le bouton **Autoriser** dans l’éditeur par défaut des données et l’exécution de la connexion d’accès OAuth.  | Oui 
subscriptionId | Id de l’abonnement Azure | Non (le cas contraire, abonnement d’usine données est utilisé). 
resourceGroupName | Nom du groupe ressource Azure |  Non (le cas contraire, groupe de ressources de la factory de données est utilisé).
ID de session | id de session à partir de la session d’autorisation OAuth. Chaque id de session est unique et ne peut être utilisé une seule fois. La session Id est généré automatiquement dans l’éditeur par défaut des données. | Oui

Le code d’autorisation que vous avez généré à l’aide du bouton **Autoriser** expire après un certain temps. Consultez le tableau suivant pour les horaires d’expiration pour les différents types de comptes d’utilisateurs. Vous pouvez rencontrer l’erreur suivante message lorsque l’authentification **jeton a expiré**: erreur d’opération des informations d’identification : invalid_grant - AADSTS70002 : erreur de validation des informations d’identification. AADSTS70008 : Le droit d’accès fourni est expiré ou révoqué. ID de trace : ID de corrélation d18629e8-af88-43c5-88e3-d8419eb1fca1 : fac30a0c-6be6-4e02-8d69-a776d2ffefd7 horodatage : 2015-12-15 21:09:31Z

 
| Type d’utilisateur | Expire après |
| :-------- | :----------- | 
| Comptes d’utilisateurs non gérés par Azure Active Directory (@hotmail.com, @live.com, etc..) | 12 heures |
| Comptes utilisateurs gérés par Azure Active Directory (DAS) | 14 jours après la dernière tranche exécuter. <br/><br/>90 jours, si un secteur basé sur service lié basé sur un jeton s’exécute au moins une fois tous les 14 jours. |

Pour éviter/résoudre cette erreur, autoriser à l’aide de l' **Autoriser** à nouveau le bouton lorsque le **jeton a expiré** et redéploiement le service lié. Vous pouvez également générer des valeurs pour les propriétés de **l’ID de session** et **d’autorisation** par programme à l’aide de code dans la section suivante. 

  
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

Consultez les rubriques [AzureDataLakeStoreLinkedService cours](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [Classe AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)et [Classe AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) pour plus d’informations sur les classes usine de données utilisées dans le code. Ajoutez une référence à : Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll pour la classe WindowsFormsWebAuthenticationDialog. 
 
 
## <a name="data-lake-analytics-u-sql-activity"></a>Données Analytique Lake U-SQL activité 

L’extrait de JSON suivant définit un pipeline avec une activité données Lake Analytique U-SQL. La définition d’activité comporte une référence au service Azure données Lake Analytique liée que vous avez créée.   
  

    {
        "name": "ComputeEventsByRegionPipeline",
        "properties": {
            "description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
            "activities": 
            [
                {
                    "type": "DataLakeAnalyticsU-SQL",
                    "typeProperties": {
                        "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                        "scriptLinkedService": "StorageLinkedService",
                        "degreeOfParallelism": 3,
                        "priority": 100,
                        "parameters": {
                            "in": "/datalake/input/SearchLog.tsv",
                            "out": "/datalake/output/Result.tsv"
                        }
                    },
                    "inputs": [
                        {
                            "name": "DataLakeTable"
                        }
                    ],
                    "outputs": 
                    [
                        {
                            "name": "EventsByRegionTable"
                        }
                    ],
                    "policy": {
                        "timeout": "06:00:00",
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "EventsByRegion",
                    "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
                }
            ],
            "start": "2015-08-08T00:00:00Z",
            "end": "2015-08-08T01:00:00Z",
            "isPaused": false
        }
    }


Le tableau suivant décrit les noms et les descriptions de propriétés qui sont spécifiques à cette activité. 

Propriété | Description | Obligatoire
:-------- | :----------- | :--------
type | La propriété type doit être définie à **DataLakeAnalyticsU-SQL**. | Oui
scriptPath | Chemin d’accès au dossier qui contient le script U-SQL. Nom du fichier respecte la casse. | N° (si vous utilisez un script)
scriptLinkedService | Services liés entre le stockage qui contient le script à l’usine de données | N° (si vous utilisez un script)
script | Spécifiez un script inline au lieu de spécifier scriptPath et scriptLinkedService. Par exemple : « script » : « Test créer de base de données ». | N° (si vous utilisez scriptPath et scriptLinkedService)
degreeOfParallelism | Le nombre maximal de nœuds simultanément utilisés pour exécuter la tâche. | N°
priorité | Détermine les tâches qui se déconnecter de tout ce qui sont en attente doit déjà être sélectionnées pour s’exécuter en premier. La plus basse le numéro, plus la priorité. | N° 
paramètres | Paramètres pour le script U-SQL | N° 

Voir la [Définition de Script SearchLogProcessing.txt](#script-definition) pour la définition de script. 

## <a name="sample-input-and-output-datasets"></a>Exemple d’entrée et de sortie des jeux de données

### <a name="input-dataset"></a>Jeu de données d’entrée
Dans cet exemple, les données d’entrée résident dans un magasin de Lake données Azure (fichier SearchLog.tsv dans le dossier datalake/entrée). 

    {
        "name": "DataLakeTable",
        "properties": {
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
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }   

### <a name="output-dataset"></a>Jeu de données de sortie
Dans cet exemple, les données de sortie générées par le script U-SQL sont stockées dans un magasin de Lake données Azure (datalake/sortie dossier). 

    {
        "name": "EventsByRegionTable",
        "properties": {
            "type": "AzureDataLakeStore",
            "linkedServiceName": "AzureDataLakeStoreLinkedService",
            "typeProperties": {
                "folderPath": "datalake/output/"
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

### <a name="sample-data-lake-store-linked-service"></a>Exemple de données Lake Store lié Service
Voici la définition de l’échantillon Qu'azure données Lake Store lié service utilisé par les jeux de données d’entrée/sortie. 

    {
        "name": "AzureDataLakeStoreLinkedService",
        "properties": {
            "type": "AzureDataLakeStore",
            "typeProperties": {
                "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
                "sessionId": "<session ID>",
                "authorization": "<authorization URL>"
            }
        }
    }

Voir [déplacer des données vers et depuis Azure données Lake Store](data-factory-azure-datalake-connector.md) article pour obtenir une description des propriétés JSON. 

## <a name="sample-u-sql-script"></a>Exemple de Script U-SQL 

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv(nullEscape:"#NULL#");
    
    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";
    
    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start <= DateTime.Parse("2012/02/19");
    
    OUTPUT @rs1   
        TO @out
          USING Outputters.Tsv(quoting:false, dateTimeFormat:null);

Les valeurs de **@in** et **@out** paramètres dans le script U-SQL sont passés dynamiquement par définition d’application à l’aide de la section « Paramètres ». Consultez la section « Paramètres » dans la définition du pipeline.

Vous pouvez spécifier ainsi que d’autres propriétés telles que degreeOfParallelism et priorité dans la définition de votre pipeline pour les tâches qui s’exécutent sur le service Azure données Lake Analytique.

## <a name="dynamic-parameters"></a>Paramètres dynamiques
Dans l’exemple de définition de pipeline, et arrière sont affectées aux paramètres avec des valeurs codé en dur. 

    "parameters": {
        "in": "/datalake/input/SearchLog.tsv",
        "out": "/datalake/output/Result.tsv"
    }

Il est possible d’utiliser des paramètres dynamiques à la place. Par exemple : 

    "parameters": {
        "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
        "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
    }

Dans ce cas, les fichiers d’entrée sont toujours extraits à partir du dossier /datalake/input et fichiers de sortie sont générées dans le dossier /datalake/output. Les noms de fichiers sont dynamiques en fonction de l’heure de début de secteur.  