<properties 
    pageTitle="Activité Hive" 
    description="Découvrez comment vous pouvez utiliser l’activité ruche dans une usine de données Azure pour exécuter des requêtes Hive sur un cluster de HDInsight sur-à la demande/votre propre." 
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
    ms.date="10/11/2016" 
    ms.author="shlo"/>

# <a name="hive-activity"></a>Activité Hive
> [AZURE.SELECTOR]
[Hive](data-factory-hive-activity.md)  
[Cochon](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Diffusion en continu Hadoop](data-factory-hadoop-streaming-activity.md)
[Apprentissage automatique](data-factory-azure-ml-batch-execution-activity.md) 
[Procédure stockée](data-factory-stored-proc-activity.md)
[Données Lake Analytique U SQL](data-factory-usql-activity.md)
[.NET personnalisé](data-factory-use-custom-activities.md)

L’activité HDInsight Hive dans une usine de données [pipeline](data-factory-create-pipelines.md) exécute des requêtes Hive sur cluster fonctionnant sous Windows/Linux HDInsight [votre propre](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou [à la demande](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . Cet article s’appuie sur l’article [activités de transformation de données](data-factory-data-transformation-activities.md) , qui présente une vue d’ensemble de la transformation de données et les activités de transformation pris en charge.

## <a name="syntax"></a>Syntaxe

    {
        "name": "Hive Activity",
        "description": "description",
        "type": "HDInsightHive",
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
          "script": "Hive script",
          "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
          "defines": {
            "param1": "param1Value"
          }
        },
       "scheduler": {
          "frequency": "Day",
          "interval": 1
        }
    }
    
## <a name="syntax-details"></a>Détails de syntaxe

Propriété | Description | Obligatoire
-------- | ----------- | --------
nom | Nom de l’activité | Oui
Description | Texte décrivant à quoi sert l’activité | N°
type | HDinsightHive | Oui
entrées | Entrées consommées par l’activité Hive | N°
sorties | Sorties produites par l’activité Hive | Oui 
linkedServiceName | Référence au cluster HDInsight enregistré comme un service lié dans Data Factory | Oui 
script | Spécifier le script de Hive en ligne | N°
chemin d’accès de script | Stockez le script Hive dans un espace de stockage blob Azure et fournissez le chemin d’accès au fichier. Utilisez « script » ou « scriptPath ». Les deux ne peuvent pas être utilisés ensemble. Le nom de fichier respecte la casse. | N° 
définit | Spécifier des paramètres sous forme de paires clé/valeur pour référencer dans le script Hive à l’aide de « hiveconf »  | N°

## <a name="example"></a>Exemple

Prenons un exemple de jeu journaux analytique à l’endroit où vous voulez identifier le temps passé par les utilisateurs jouer lancé par votre entreprise. 

Le journal suivant est un exemple jeu de journal, c'est-à-dire une virgule (`,`) séparées et contient les champs suivants – ProfileID, SessionStart, durée, SrcIPAddress et le type de partie.

    1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
    1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
    1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
    1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
    .....

La **ruche script** pour traiter ces données :

    DROP TABLE IF EXISTS HiveSampleIn; 
    CREATE EXTERNAL TABLE HiveSampleIn 
    (
        ProfileID       string, 
        SessionStart    string, 
        Duration        int, 
        SrcIPAddress    string, 
        GameType        string
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/samplein/'; 
    
    DROP TABLE IF EXISTS HiveSampleOut; 
    CREATE EXTERNAL TABLE HiveSampleOut 
    (   
        ProfileID   string, 
        Duration    int
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/sampleout/';
    
    INSERT OVERWRITE TABLE HiveSampleOut
    Select 
        ProfileID,
        SUM(Duration)
    FROM HiveSampleIn Group by ProfileID

Pour exécuter ce script Hive dans un pipeline de données par défaut, vous devez effectuer les opérations suivantes

1. Création d’un service lié pour enregistrer [vos propres HDInsight cluster de calcul](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou configurer [cluster de calcul à la demande HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Appelons ce service lié « HDInsightLinkedService ».
2. Création d’un [service liée](data-factory-azure-blob-connector.md) pour configurer la connexion au stockage d’objets Blob Azure qui héberge les données. Nous allons appeler ce service lié « StorageLinkedService »
3. Créer [des groupes de données](data-factory-create-datasets.md) en pointant sur l’entrée et les données de sortie. Nous allons appeler le jeu de données d’entrée « HiveSampleIn » et le jeu de données de sortie « HiveSampleOut »
4. Copier la requête Hive en tant que fichier de stockage d’objets Blob Azure configuré à l’étape 2 #. Si le stockage pour héberger les données est différent de celui qui héberge ce fichier de requête, créez un service de stockage Azure lié distinct et y faire de l’activité. Utilisez **scriptPath **pour spécifier le chemin d’accès au fichier de requête hive et **scriptLinkedService** pour spécifier le stockage Azure qui contient le fichier de script. 

    > [AZURE.NOTE] Vous pouvez également fournir l’inline de script Hive dans la définition d’activité à l’aide de la propriété de **script** . Nous ne recommandons pas cette approche en tant que tous les caractères spéciaux dans le script dans le JSON document doit être d’échappement et peut entraîner des problèmes de débogage. La meilleure solution consiste à suivez l’étape 4 #.
5.  Créer une opportunité avec l’activité HDInsightHive. Processus de l’activité/transformations les données.

        {
          "name": "HiveActivitySamplePipeline",
          "properties": {
            "activities": [
              {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                  {
                    "name": "HiveSampleIn"
                  }
                ],
                "outputs": [
                  {
                    "name": "HiveSampleOut"
                  }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                  "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                  "scriptLinkedService": "StorageLinkedService"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
              }
            ]
          }
        }

6.  Déployer le pipeline. Consultez l’article de [pipelines de création](data-factory-create-pipelines.md) pour plus d’informations. 
7.  Surveiller le pipeline utilisant les vues données usine gestion et de contrôle. Voir [surveillance et gérer les pipelines Data Factory](data-factory-monitor-manage-pipelines.md) article pour plus d’informations. 


## <a name="specifying-parameters-for-a-hive-script"></a>Spécification des paramètres d’un script Hive  
Dans cet exemple, les journaux jeu sont ingérés quotidiennement en stockage d’objets Blob Azure et sont stockées dans un dossier partitionné avec la date et l’heure. Vous souhaitez paramétrer le script Hive et passer de l’emplacement du dossier d’entrée dynamiquement lors de l’exécution et produire également la sortie partitionnée avec la date et l’heure.

Pour utiliser le script Hive paramétré, procédez comme suit

- Définir les paramètres de **définit**.

        {
            "name": "HiveActivitySamplePipeline",
            "properties": {
            "activities": [
                {
                    "name": "HiveActivitySample",
                    "type": "HDInsightHive",
                    "inputs": [
                        {
                            "name": "HiveSampleIn"
                          }
                    ],
                    "outputs": [
                        {
                            "name": "HiveSampleOut"
                        }
                    ],
                    "linkedServiceName": "HDInsightLinkedService",
                    "typeproperties": {
                        "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                        "scriptLinkedService": "StorageLinkedService",
                        "defines": {
                            "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)",
                            "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)"
                        },
                        "scheduler": {
                            "frequency": "Hour",
                            "interval": 1
                        }
                    }
                }
            ]
          }
        }

- Dans le Script ruche, consultez le paramètre à l’aide de **${hiveconf:parameterName}**. 

        DROP TABLE IF EXISTS HiveSampleIn; 
        CREATE EXTERNAL TABLE HiveSampleIn 
        (
            ProfileID   string, 
            SessionStart    string, 
            Duration    int, 
            SrcIPAddress    string, 
            GameType    string
        ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Input}'; 
        
        DROP TABLE IF EXISTS HiveSampleOut; 
        CREATE EXTERNAL TABLE HiveSampleOut 
        (
            ProfileID   string, 
            Duration    int
        ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Output}';
        
        INSERT OVERWRITE TABLE HiveSampleOut
        Select 
            ProfileID,
            SUM(Duration)
        FROM HiveSampleIn Group by ProfileID


## <a name="see-also"></a>Voir aussi
- [Cochon activité](data-factory-pig-activity.md)
- [Activité MapReduce](data-factory-map-reduce.md)
- [Activité de diffusion en continu Hadoop](data-factory-hadoop-streaming-activity.md)
- [Appeler des programmes d’explosion](data-factory-spark.md)
- [Appeler scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)









