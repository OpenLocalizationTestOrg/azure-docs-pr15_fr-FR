<properties 
    pageTitle="Appeler programme MapReduce provenant d’une usine de données Azure" 
    description="Découvrez comment traiter les données en exécutant des programmes MapReduce sur un cluster Azure HDInsight à partir d’une usine de données Azure." 
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
    ms.date="09/12/2016" 
    ms.author="shlo"/>

# <a name="invoke-mapreduce-programs-from-data-factory"></a>Appeler des programmes MapReduce provenant d’une usine de données
> [AZURE.SELECTOR]
[Hive](data-factory-hive-activity.md)  
[Cochon](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Diffusion en continu Hadoop](data-factory-hadoop-streaming-activity.md)
[Apprentissage automatique](data-factory-azure-ml-batch-execution-activity.md) 
[Procédure stockée](data-factory-stored-proc-activity.md)
[Données Lake Analytique U SQL](data-factory-usql-activity.md)
[.NET personnalisé](data-factory-use-custom-activities.md)

L’activité HDInsight MapReduce dans une usine de données [pipeline](data-factory-create-pipelines.md) exécute MapReduce programmes sur [votre propre](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou [à la demande](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) cluster fonctionnant sous Windows/Linux HDInsight. Cet article s’appuie sur l’article [activités de transformation de données](data-factory-data-transformation-activities.md) , qui présente une vue d’ensemble de la transformation de données et les activités de transformation pris en charge.

## <a name="introduction"></a>Introduction 
Un pipeline dans une usine de données Azure traite les données dans les services de stockage liées à l’aide liée cluster services. Il contient une séquence d’activités dans lequel chaque activité effectue une opération de traitement spécifiques. Cet article décrit l’utilisation de l’activité MapReduce HDInsight.
 
Pour plus d’informations sur l’exécution de scripts cochon/Hive sur un cluster HDInsight fonctionnant sous Windows/Linux à partir d’un pipeline à l’aide des activités HDInsight cochon et Hive, voir [cochon](data-factory-pig-activity.md) et [ruche](data-factory-hive-activity.md) . 

## <a name="json-for-hdinsight-mapreduce-activity"></a>JSON HDInsight MapReduce activité 

Dans la définition de JSON pour l’activité HDInsight : 
 
1. Définir le **type** de l' **activité** à **HDInsight**.
3. Spécifiez le nom de la classe de propriété **NomClasse** .
4. Spécifiez le chemin d’accès au fichier JAR, y compris le nom du fichier de propriété **jarFilePath** .
5. Spécifiez le service lié qui fait référence au stockage Blob Azure qui contient le fichier JAR de propriété **jarLinkedService** .   
6. Spécifier des arguments pour le programme MapReduce dans la section **arguments** . En cours d’exécution, vous voyez plusieurs arguments supplémentaires (par exemple : mapreduce.job.tags) à partir de l’infrastructure MapReduce. Pour différencier vos arguments avec les arguments MapReduce, envisagez d’utiliser option et valeur en tant qu’arguments comme le montre l’exemple suivant (- d,--entrée,--sortie etc., sont options immédiatement suivies de leurs valeurs).

        {
            "name": "MahoutMapReduceSamplePipeline",
            "properties": {
                "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
                "activities": [
                    {
                        "type": "HDInsightMapReduce",
                        "typeProperties": {
                            "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                            "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                            "jarLinkedService": "StorageLinkedService",
                            "arguments": [
                                "-s",
                                "SIMILARITY_LOGLIKELIHOOD",
                                "--input",
                                "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
                                "--output",
                                "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
                                "--maxSimilaritiesPerItem",
                                "500",
                                "--tempDir",
                                "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
                            ]
                        },
                        "inputs": [
                            {
                                "name": "MahoutInput"
                            }
                        ],
                        "outputs": [
                            {
                                "name": "MahoutOutput"
                            }
                        ],
                        "policy": {
                            "timeout": "01:00:00",
                            "concurrency": 1,
                            "retry": 3
                        },
                        "scheduler": {
                            "frequency": "Hour",
                            "interval": 1
                        },
                        "name": "MahoutActivity",
                        "description": "Custom Map Reduce to generate Mahout result",
                        "linkedServiceName": "HDInsightLinkedService"
                    }
                ],
                "start": "2014-01-03T00:00:00Z",
                "end": "2014-01-04T00:00:00Z",
                "isPaused": false,
                "hubName": "mrfactory_hub",
                "pipelineMode": "Scheduled"
            }
        }
    
    

Vous pouvez utiliser l’activité MapReduce HDInsight pour exécuter n’importe quel fichier jar MapReduce sur un cluster HDInsight. Dans l’exemple suivant JSON de définition d’un pipeline, l’activité HDInsight est configurée pour exécuter un fichier JAR Mahout.

## <a name="sample-on-github"></a>Exemple de GitHub
Vous pouvez télécharger un exemple d’utilisation de l’activité MapReduce HDInsight à partir : [Données usine exemples sur GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON/MapReduce_Activity_Sample).  

## <a name="running-the-word-count-program"></a>L’exécution du programme de mots
Le pipeline dans cet exemple exécute le programme de mots carte/réduire sur votre cluster Azure HDInsight.   

### <a name="linked-services"></a>Services liés
Tout d’abord, vous créez un service lié pour créer un lien au stockage Azure qui est utilisé par le cluster le factory de données Azure Azure HDInsight. Si vous copiez/collez le code suivant, n’oubliez pas de remplacer le **nom de compte** et **clé de compte** avec le nom et la clé de votre stockage Azure. 

#### <a name="azure-storage-linked-service"></a>Service de stockage lié Azure

    {
        "name": "StorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
            }
        }
    }

#### <a name="azure-hdinsight-linked-service"></a>Azure HDInsight lié service
Ensuite, vous créez un service lié pour créer un lien à l’usine données Azure votre cluster Azure HDInsight. Si vous copiez/collez le code suivant, remplacez le **nom du cluster HDInsight** par le nom de votre cluster HDInsight et modifier les valeurs de nom et mot de passe utilisateur.   

    {
        "name": "HDInsightLinkedService",
        "properties": {
            "type": "HDInsight",
            "typeProperties": {
                "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
                "userName": "admin",
                "password": "**********",
                "linkedServiceName": "StorageLinkedService"
            }
        }
    }

### <a name="datasets"></a>Jeux de données

#### <a name="output-dataset"></a>Jeu de données de sortie
Le pipeline dans cet exemple ne prend pas les entrées. Vous spécifiez un jeu de données de sortie pour l’activité MapReduce HDInsight. Ce groupe de données est simplement un dataset factice qui est nécessaire pour réaliser la planification pipeline.  

    {
        "name": "MROutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "fileName": "WordCountOutput1.txt",
                "folderPath": "example/data/",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

### <a name="pipeline"></a>Pipeline
Le pipeline dans cet exemple n'a qu’une seule activité qui est de type : HDInsightMapReduce. Certaines des propriétés importantes dans le JSON sont : 

Propriété | Notes
:-------- | :-----
type | Le type doit être défini à **HDInsightMapReduce**. 
NomClasse | Nom du cours est : **wordcount**
jarFilePath | Chemin d’accès au fichier jar contenant la classe. Si vous copiez/collez le code suivant, n’oubliez pas de modifier le nom du cluster. 
jarLinkedService | Stockage Azure liée service qui contient le fichier jar. Ce service lié fait référence à l’espace de stockage est associé au cluster HDInsight. 
arguments | Le programme wordcount prend deux arguments : une entrée et sortie. Le fichier d’entrée est le fichier davinci.txt.
fréquence/intervalle | Les valeurs de ces propriétés correspondent le jeu de données de sortie. 
linkedServiceName | fait référence au service HDInsight lié créé précédemment.   

    {
        "name": "MRSamplePipeline",
        "properties": {
            "description": "Sample Pipeline to Run the Word Count Program",
            "activities": [
                {
                    "type": "HDInsightMapReduce",
                    "typeProperties": {
                        "className": "wordcount",
                        "jarFilePath": "<HDInsight cluster name>/example/jars/hadoop-examples.jar",
                        "jarLinkedService": "StorageLinkedService",
                        "arguments": [
                            "/example/data/gutenberg/davinci.txt",
                            "/example/data/WordCountOutput1"
                        ]
                    },
                    "outputs": [
                        {
                            "name": "MROutput"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "MRActivity",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2014-01-03T00:00:00Z",
            "end": "2014-01-04T00:00:00Z"
        }
    }

## <a name="run-spark-programs"></a>Exécuter des programmes d’explosion
Vous pouvez utiliser MapReduce activité pour exécuter des programmes explosion sur votre cluster HDInsight Spark. Pour plus d’informations, voir [programmes appeler explosion d’Azure Data Factory](data-factory-spark.md) .  

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]:data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines 

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com
 
## <a name="see-also"></a>Voir aussi
- [Activité Hive](data-factory-hive-activity.md)
- [Cochon activité](data-factory-pig-activity.md)
- [Activité de diffusion en continu Hadoop](data-factory-hadoop-streaming-activity.md)
- [Appeler des programmes d’explosion](data-factory-spark.md)
- [Appeler scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)
