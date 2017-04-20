<properties 
    pageTitle="Cochon activité" 
    description="Découvrez comment vous pouvez utiliser l’activité cochon dans une usine de données Azure pour exécuter des scripts cochon sur un cluster de HDInsight sur-à la demande/votre propre." 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/31/2016" 
    ms.author="shlo"/>

# <a name="pig-activity"></a>Cochon activité
> [AZURE.SELECTOR]
[Hive](data-factory-hive-activity.md)  
[Cochon](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Diffusion en continu Hadoop](data-factory-hadoop-streaming-activity.md)
[Apprentissage automatique](data-factory-azure-ml-batch-execution-activity.md) 
[Procédure stockée](data-factory-stored-proc-activity.md)
[Données Lake Analytique U SQL](data-factory-usql-activity.md)
[.NET personnalisé](data-factory-use-custom-activities.md)

L’activité HDInsight cochon dans une usine de données [pipeline](data-factory-create-pipelines.md) exécute des requêtes de cochon sur [vos propres](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou [à la demande](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) cluster fonctionnant sous Windows/Linux HDInsight. Cet article s’appuie sur l’article [activités de transformation de données](data-factory-data-transformation-activities.md) , qui présente une vue d’ensemble de la transformation de données et les activités de transformation pris en charge.

## <a name="syntax"></a>Syntaxe

    {
        "name": "HiveActivitySamplePipeline",
        "properties": {
        "activities": [
            {
                "name": "Pig Activity",
                "description": "description",
                "type": "HDInsightPig",
                "inputs": [
                    {
                        "name": "input tables"
                    }
                ],
                "outputs": [
                    {
                        "name": "output tables"
                    }
                ],
                "linkedServiceName": "MyHDInsightLinkedService",
                "typeProperties": {
                    "script": "Pig script",
                    "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
                    "defines": {
                        "param1": "param1Value"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                }
            }
        ]
      }
    }

## <a name="syntax-details"></a>Détails de syntaxe

Propriété | Description | Obligatoire
-------- | ----------- | --------
nom | Nom de l’activité | Oui
Description | Texte décrivant à quoi sert l’activité | N°
type | HDinsightPig | Oui
entrées | Une ou plusieurs entrées consommées par l’activité cochon | N°
sorties | Une ou plusieurs sorties obtenus par l’activité cochon | Oui
linkedServiceName | Référence au cluster HDInsight enregistré comme un service lié dans Data Factory | Oui
script | Spécifier le script de cochon en ligne | N°
chemin d’accès de script | Stockez le script cochon dans un espace de stockage blob Azure et fournissez le chemin d’accès au fichier. Utilisez « script » ou « scriptPath ». Les deux ne peuvent pas être utilisés ensemble. Le nom de fichier respecte la casse. | N°
définit | Spécifier des paramètres sous forme de paires clé/valeur pour référencer dans le script cochon | N°

## <a name="example"></a>Exemple

Prenons un exemple de jeu journaux analytique à l’endroit où vous voulez identifier le temps passé par les joueurs jouer lancé par votre entreprise.
 
Le journal de jeu exemple suivant est un fichier séparées par des virgules (,). Il contient les champs suivants – ProfileID, SessionStart, durée, SrcIPAddress et le type de partie.

    1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
    1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
    1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
    1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
    .....

Le **script cochon** pour traiter ces données :

    PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
    
    GroupProfile = Group PigSampleIn all;
    
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
    
    Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');

Pour exécuter ce script cochon dans un pipeline de données par défaut, procédez comme suit :

1. Création d’un service lié pour enregistrer [vos propres HDInsight cluster de calcul](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou configurer [cluster de calcul à la demande HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Appelons ce service lié **HDInsightLinkedService**.
2.  Création d’un [service liée](data-factory-azure-blob-connector.md) pour configurer la connexion au stockage d’objets Blob Azure qui héberge les données. Appelons ce service lié **StorageLinkedService**.
3.  Créer [des groupes de données](data-factory-create-datasets.md) en pointant sur l’entrée et les données de sortie. Nous allons appeler le jeu de données d’entrée **PigSampleIn** et le jeu de données de sortie **PigSampleOut**.
4.  Copiez la requête cochon dans un fichier du stockage d’objets Blob Azure configuré à l’étape 2 #. Si le stockage Azure qui héberge les données est différent de celui qui héberge le fichier de requête, créez un service de stockage Azure lié distinct. Reportez-vous au service lié dans la configuration des activités. Utilisez **scriptPath **pour spécifier le chemin d’accès au fichier de script cochon et **scriptLinkedService**. 
    
    > [AZURE.NOTE] Vous pouvez également fournir l’inline de script cochon dans la définition d’activité à l’aide de la propriété de **script** . Toutefois, nous ne recommandons pas cette approche en tant que tous les caractères spéciaux dans le script doit être d’échappement et peut entraîner des problèmes de débogage. La meilleure solution consiste à suivez l’étape 4 #.
5. Créer le pipeline avec l’activité HDInsightPig. Cette activité traite les données d’entrée en exécutant le script cochon sur cluster HDInsight.

        {
          "name": "PigActivitySamplePipeline",
          "properties": {
            "activities": [
              {
                "name": "PigActivitySample",
                "type": "HDInsightPig",
                "inputs": [
                  {
                    "name": "PigSampleIn"
                  }
                ],
                "outputs": [
                  {
                    "name": "PigSampleOut"
                  }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                  "scriptPath": "adfwalkthrough\\scripts\\enrichlogs.pig",
                  "scriptLinkedService": "StorageLinkedService"
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                }
              }
            ]
          }
        } 
6. Déployer le pipeline. Consultez l’article de [pipelines de création](data-factory-create-pipelines.md) pour plus d’informations. 
7. Surveiller le pipeline utilisant les vues données usine gestion et de contrôle. Voir [surveillance et gérer les pipelines Data Factory](data-factory-monitor-manage-pipelines.md) article pour plus d’informations.

## <a name="specifying-parameters-for-a-pig-script"></a>Spécification des paramètres d’un script cochon 

Prenons l’exemple suivant : les journaux jeu sont ingérés quotidiennement en stockage d’objets Blob Azure et stockées dans un dossier partitionnée date en fonction d’et l’heure. Vous souhaitez paramétrer le script cochon et passer de l’emplacement du dossier d’entrée dynamiquement lors de l’exécution et produire également la sortie partitionnée avec la date et l’heure.
 
Pour utiliser le script cochon paramétré, procédez comme suit :

- Définir les paramètres de **définit**.

        {
            "name": "PigActivitySamplePipeline",
            "properties": {
            "activities": [
                {
                    "name": "PigActivitySample",
                    "type": "HDInsightPig",
                    "inputs": [
                        {
                            "name": "PigSampleIn"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "PigSampleOut"
                        }
                    ],
                    "linkedServiceName": "HDInsightLinkedService",
                    "typeproperties": {
                        "scriptPath": "adfwalkthrough\\scripts\\samplepig.hql",
                        "scriptLinkedService": "StorageLinkedService",
                        "defines": {
                            "Input": "$$Text.Format('wasb: //adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0: yyyy}/monthno={0: %M}/dayno={0: %d}/',SliceStart)",
                            "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)"
                        }
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    }
                }
            ]
          }
        }  
      
- Dans le Script cochon, reportez-vous à ces paramètres en utilisant «**$parameterName**» comme le montre l’exemple suivant :

        PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);   
        GroupProfile = Group PigSampleIn all;       
        PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);      
        Store PigSampleOut into '$Output' USING PigStorage (','); 


## <a name="see-also"></a>Voir aussi
- [Activité Hive](data-factory-hive-activity.md)
- [Activité MapReduce](data-factory-map-reduce.md)
- [Activité de diffusion en continu Hadoop](data-factory-hadoop-streaming-activity.md)
- [Appeler des programmes d’explosion](data-factory-spark.md)
- [Appeler scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)


