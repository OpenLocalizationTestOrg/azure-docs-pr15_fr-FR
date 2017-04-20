<properties 
    pageTitle="Activité de diffusion en continu Hadoop" 
    description="Découvrez comment vous pouvez utiliser l’activité de diffusion en continu Hadoop dans une usine de données Azure pour exécuter des programmes Hadoop diffusion en continu sur un cluster de HDInsight sur-à la demande/votre propre." 
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
    ms.date="09/20/2016" 
    ms.author="shlo"/>

# <a name="hadoop-streaming-activity"></a>Activité de diffusion en continu Hadoop
> [AZURE.SELECTOR]
[Hive](data-factory-hive-activity.md)  
[Cochon](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Diffusion en continu Hadoop](data-factory-hadoop-streaming-activity.md)
[Apprentissage automatique](data-factory-azure-ml-batch-execution-activity.md) 
[Procédure stockée](data-factory-stored-proc-activity.md)
[Données Lake Analytique U SQL](data-factory-usql-activity.md)
[.NET personnalisé](data-factory-use-custom-activities.md)

Vous pouvez utiliser l’activité HDInsightStreamingActivity appeler un travail Hadoop diffusion en continu à partir d’un pipeline Azure Data Factory. L’extrait de JSON suivant illustre la syntaxe pour l’utilisation de la HDInsightStreamingActivity dans un fichier de JSON pipeline. 

L’activité de diffusion en continu HDInsight dans une usine de données [pipeline](data-factory-create-pipelines.md) exécute des programmes Hadoop diffusion en continu sur [votre propre](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou [à la demande](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) cluster fonctionnant sous Windows/Linux HDInsight. Cet article s’appuie sur l’article [activités de transformation de données](data-factory-data-transformation-activities.md) , qui présente une vue d’ensemble de la transformation de données et les activités de transformation pris en charge.

## <a name="json-sample"></a>Exemple JSON
Le cluster HDInsight est rempli automatiquement avec les exemples de programmes (wc.exe et cat.exe) et des données (davinci.txt). Par défaut, le nom du conteneur qui est utilisé par le cluster HDInsight est le nom du cluster lui-même. Par exemple, si votre nom de cluster est myhdicluster, nom du conteneur blob associé serait myhdicluster. 

    {
        "name": "HadoopStreamingPipeline",
        "properties": {
            "description": "Hadoop Streaming Demo",
            "activities": [
                {
                    "type": "HDInsightStreaming",
                    "typeProperties": {
                        "mapper": "cat.exe",
                        "reducer": "wc.exe",
                        "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                        "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                        "filePaths": [
                            "<nameofthecluster>/example/apps/wc.exe",
                            "<nameofthecluster>/example/apps/cat.exe"
                        ],
                        "fileLinkedService": "StorageLinkedService",
                        "getDebugInfo": "Failure"
                    },
                    "outputs": [
                        {
                            "name": "StreamingOutputDataset"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "RunHadoopStreamingJob",
                    "description": "Run a Hadoop streaming job",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2014-01-04T00:00:00Z",
            "end": "2014-01-05T00:00:00Z"
        }
    }

Notez les points suivants :

1. Définissez la **linkedServiceName** sur le nom du service lié qui pointe vers votre cluster HDInsight sur lequel s’exécute le travail mapreduce diffusion en continu.
2. Définissez le type de l’activité sur **HDInsightStreaming**.
3. Pour la propriété **mapper** , indiquez le nom de mapper exécutable. Dans l’exemple, cat.exe est la mapper exécutable.
4. Pour la propriété **réducteur** , indiquez le nom de réducteur exécutable. Dans l’exemple, wc.exe est le réducteur exécutable.
5. Pour la propriété de type **d’entrée** , spécifiez le fichier d’entrée (y compris l’emplacement) pour la mapper. Dans l’exemple : "wasb://adfsample@ <account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt » : adfsample est le conteneur blob, exemple/données/Gutenberg est le dossier et davinci.txt est le blob.
6. Pour la propriété de type de **sortie** , spécifiez le fichier de sortie (y compris l’emplacement) pour le réducteur. Le résultat de la tâche Hadoop diffusion en continu est écrite à l’emplacement spécifié pour cette propriété.
7. Dans la section **filePaths** , spécifiez les chemins d’accès pour les exécutables mapper et réducteur. Dans l’exemple : « adfsample/example/apps/wc.exe », adfsample est le conteneur blob, exemple/applications est le dossier et wc.exe est le fichier exécutable.
8. Pour la propriété **fileLinkedService** , spécifiez le service de stockage Azure liées qui représente le stockage Azure qui contient les fichiers spécifiés dans la section filePaths.
9. Pour la propriété **arguments** , spécifiez les arguments de la tâche de diffusion en continu.
10. La propriété **getDebugInfo** est un élément facultatif. Lorsqu’elle est définie à l’échec, les journaux sont téléchargés uniquement en cas d’échec. Lorsqu’elle est définie à tous, les journaux sont toujours téléchargés, quelle que soit l’état d’exécution.

> [AZURE.NOTE] Comme le montre l’exemple, vous spécifiez un jeu de données de sortie pour l’activité de diffusion en continu Hadoop pour la propriété **sorties** . Ce groupe de données est simplement un dataset factice qui est nécessaire pour réaliser la planification pipeline. Vous n’avez pas besoin spécifier un dataset d’entrée pour l’activité pour la propriété **entrées** .  

    
## <a name="example"></a>Exemple
Le pipeline de cette procédure s’exécute le programme carte/réduire de la diffusion en continu de mots sur votre cluster Azure HDInsight. 

### <a name="linked-services"></a>Services liés

#### <a name="azure-storage-linked-service"></a>Service de stockage lié Azure
Tout d’abord, vous créez un service lié pour créer un lien au stockage Azure qui est utilisé par le cluster le factory de données Azure Azure HDInsight. Si vous copiez/collez le code suivant, n’oubliez pas de remplacer le nom de compte et clé de compte avec le nom et la clé de votre stockage Azure. 

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
Ensuite, vous créez un service lié pour créer un lien à l’usine données Azure votre cluster Azure HDInsight. Si vous copier/coller le code suivant, remplacez le nom du cluster HDInsight par le nom de votre cluster HDInsight et modifier les valeurs de nom et mot de passe utilisateur. 
    
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
Le pipeline dans cet exemple ne prend pas les entrées. Vous spécifiez un jeu de données de sortie pour l’activité de diffusion en continu HDInsight. Ce groupe de données est simplement un dataset factice qui est nécessaire pour réaliser la planification pipeline. 

    {
        "name": "StreamingOutputDataset",
        "properties": {
            "published": false,
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/streamingdata/",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                },
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

### <a name="pipeline"></a>Pipeline

Le pipeline dans cet exemple n'a qu’une seule activité qui est de type : **HDInsightStreaming**. 

Le cluster HDInsight est rempli automatiquement avec les exemples de programmes (wc.exe et cat.exe) et des données (davinci.txt). Par défaut, le nom du conteneur qui est utilisé par le cluster HDInsight est le nom du cluster lui-même. Par exemple, si votre nom de cluster est myhdicluster, nom du conteneur blob associé serait myhdicluster.  

    {
        "name": "HadoopStreamingPipeline",
        "properties": {
            "description": "Hadoop Streaming Demo",
            "activities": [
                {
                    "type": "HDInsightStreaming",
                    "typeProperties": {
                        "mapper": "cat.exe",
                        "reducer": "wc.exe",
                        "input": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                        "output": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                        "filePaths": [
                            "<blobcontainer>/example/apps/wc.exe",
                            "<blobcontainer>/example/apps/cat.exe"
                        ],
                        "fileLinkedService": "StorageLinkedService"
                    },
                    "outputs": [
                        {
                            "name": "StreamingOutputDataset"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "RunHadoopStreamingJob",
                    "description": "Run a Hadoop streaming job",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2014-01-04T00:00:00Z",
            "end": "2014-01-05T00:00:00Z"
        }
    }

## <a name="see-also"></a>Voir aussi
- [Activité Hive](data-factory-hive-activity.md)
- [Cochon activité](data-factory-pig-activity.md)
- [Activité MapReduce](data-factory-map-reduce.md)
- [Appeler des programmes d’explosion](data-factory-spark.md)
- [Appeler scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

