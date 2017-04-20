<properties 
    pageTitle="La Transformation de données : Données de processus et transformer | Microsoft Azure" 
    description="Découvrez comment transformer les données ou processus dans Azure Data Factory à l’aide de Hadoop, apprentissage automatique ou Azure données Lake Analytique." 
    keywords="transformation de données, les données de processus, transformer les données, activité de transformation"
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
    ms.date="09/23/2016" 
    ms.author="shlo"/>

# <a name="transform-data-in-azure-data-factory"></a>Transformer des données dans une usine de données Azure
> [AZURE.SELECTOR]
[Hive](data-factory-hive-activity.md)  
[Cochon](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Diffusion en continu Hadoop](data-factory-hadoop-streaming-activity.md)
[Apprentissage automatique](data-factory-azure-ml-batch-execution-activity.md) 
[Procédure stockée](data-factory-stored-proc-activity.md)
[Données Lake Analytique U SQL](data-factory-usql-activity.md)
[.NET personnalisé](data-factory-use-custom-activities.md)
   

## <a name="overview"></a>Vue d’ensemble 
Cet article explique les activités de transformation des données dans Azure Data Factory que vous pouvez utiliser pour transformer et traite vos données brutes dans des prédictions et les idées. Une activité de transformation s’exécute dans un environnement informatique comme cluster Azure HDInsight ou un lot Azure. Il fournit des liens vers des articles avec des informations détaillées sur chaque activité de transformation.
 
Données usine prend en charge les données transformation activités suivantes qui peuvent être ajoutées aux [canalisations](data-factory-create-pipelines.md) soit individuellement ou en chaîne avec une autre activité.

> [AZURE.NOTE] Pour une présentation détaillée des instructions détaillées, reportez-vous à l’article [créer une opportunité avec Hive transformation](data-factory-build-your-first-pipeline.md) .  

## <a name="hdinsight-hive-activity"></a>Activité HDInsight Hive
L’activité HDInsight Hive dans un pipeline Data Factory exécute des requêtes de Hive vos propres ou cluster de fonctionnant sous Windows/Linux HDInsight à la demande. Consultez l’article de [La ruche activité](data-factory-hive-activity.md) pour plus d’informations sur cette activité. 

## <a name="hdinsight-pig-activity"></a>Activité HDInsight cochon
L’activité HDInsight cochon dans un pipeline Data Factory exécute des requêtes de cochon vos propres ou cluster de fonctionnant sous Windows/Linux HDInsight à la demande. Consultez l’article de [Cochon activité](data-factory-pig-activity.md) pour plus d’informations sur cette activité. 

## <a name="hdinsight-mapreduce-activity"></a>Activité HDInsight MapReduce
L’activité HDInsight MapReduce dans un pipeline Data Factory exécute programmes MapReduce vos propres ou un cluster de fonctionnant sous Windows/Linux HDInsight à la demande. Reportez-vous à l’article [MapReduce activité](data-factory-map-reduce.md) pour plus d’informations sur cette activité.

Vous pouvez utiliser MapReduce activité pour exécuter des programmes explosion sur votre cluster HDInsight Spark. Pour plus d’informations, voir [programmes appeler explosion d’Azure Data Factory](data-factory-spark.md) .

## <a name="hdinsight-streaming-activity"></a>Activité de diffusion en continu HDInsight
L’activité de diffusion en continu HDInsight dans un pipeline Data Factory exécute programmes Hadoop diffusion en continu vos propres ou un cluster de fonctionnant sous Windows/Linux HDInsight à la demande. Voir [l’activité de diffusion en continu HDInsight](data-factory-hadoop-streaming-activity.md) pour plus d’informations sur cette activité.

## <a name="machine-learning-activities"></a>Activités d’apprentissage automatique
Azure Data Factory vous permet de créer aisément des pipelines qui utilisent un service web apprentissage automatique Azure publié pour prédictive analytique. Utilisation de l' [Activité des lots de l’exécution](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) dans un pipeline Azure Data Factory, vous pouvez appeler un service web apprentissage automatique pour réaliser des prévisions sur les données de lot.

Dans le temps, les modèles prédictives dans l’apprentissage automatique score expériences doivent être s à l’aide de nouveaux jeux de données d’entrée. Une fois que vous avez fini avec reconversion, à mettre à jour le service web score avec le modèle d’apprentissage automatique retrained. Vous pouvez utiliser l' [Activité des ressources mettre à jour](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) pour mettre à jour le service web avec le modèle que vous venez formé.  

Afficher les [activités d’apprentissage automatique utiliser](data-factory-azure-ml-batch-execution-activity.md) pour plus d’informations sur ces activités d’apprentissage automatique. 

## <a name="stored-procedure-activity"></a>Procédure stockée activité
Vous pouvez utiliser l’activité de procédure stockée SQL Server dans un pipeline de données par défaut pour appeler une procédure stockée dans un des magasins de données suivants : Azure base de données SQL, entrepôt de données SQL Azure, base de données SQL Server dans votre entreprise ou une machine virtuelle Azure. Consultez l’article [d’Activité de procédure stockée](data-factory-stored-proc-activity.md) pour plus d’informations.  

## <a name="data-lake-analytics-u-sql-activity"></a>Activité de Lake Analytique U-SQL des données
Données Lake Analytique U-SQL activité s’exécute un script U-SQL sur un cluster Azure données Lake Analytique. Consultez l’article de [Données Analytique U-SQL activité](data-factory-usql-activity.md) pour plus d’informations. 

## <a name="net-custom-activity"></a>Activité personnalisée .NET
Si vous avez besoin transformer les données d’une manière qui n’est pas pris en charge par Data Factory, vous pouvez créer une activité personnalisée avec votre propre logique de traitement des données et utiliser l’activité dans le pipeline. Vous pouvez configurer l’activité .NET personnalisée à exécuter à l’aide d’un service d’Azure lot ou un cluster Azure HDInsight. Consultez l’article [d’activités personnalisées utiliser](data-factory-use-custom-activities.md) pour plus d’informations. 

Vous pouvez créer une activité personnalisée pour exécuter des scripts R sur votre cluster HDInsight avec R installé. Voir [exécuter le Script R utilisation Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). 

## <a name="compute-environments"></a>Calculer les environnements mixtes
Vous créez un service pour l’environnement cluster lié, puis utilisez le service lié lorsque vous définissez une activité de transformation. Il existe deux types d’environnements cluster pris en charge par Data Factory. 

1. **À la demande**: dans ce cas, l’environnement informatique est entièrement géré par Data Factory. Il est automatiquement créé par le service de données usine avant d’une tâche est envoyée pour traiter les données et supprimée lors de la tâche est terminée. Vous pouvez configurer et contrôler granulaires paramètres de l’environnement cluster à la demande pour l’exécution du travail, la gestion de cluster et démarrage actions. 
2. **Mettre votre propre**: dans ce cas, vous pouvez enregistrer votre propre environnement informatique (par exemple HDInsight cluster) comme service lié dans Data Factory. L’environnement informatique est gérée par vous et le service Data Factory utilise pour exécuter les activités. 

Consultez l’article de [Calculer les Services liés](data-factory-compute-linked-services.md) pour en savoir plus sur Calculer services pris en charge par Data Factory. 


## <a name="summary"></a>Résumé
Azure Data Factory prend en charge les activités de transformation de données suivantes et les environnements de calcul pour les activités. Les activités de transformation peuvent être ajoutées à pipelines soit individuellement ou en chaîne avec une autre activité.

Activité de transformation de données |  Environnement informatique 
:----------------------- | :--------------------
[Hive](data-factory-hive-activity.md) | HDInsight [Hadoop] 
[Cochon](data-factory-pig-activity.md) | HDInsight [Hadoop]  
[MapReduce](data-factory-map-reduce.md) | HDInsight [Hadoop]  
[Hadoop diffusion en continu](data-factory-hadoop-streaming-activity.md) | HDInsight [Hadoop]
[Machine activités d’apprentissage : l’exécution du lot et des ressources de mise à jour](data-factory-azure-ml-batch-execution-activity.md) | Machine virtuelle Azure 
[Procédure stockée](data-factory-stored-proc-activity.md) | SQL Azure, Data Warehouse SQL Azure ou SQL Server |
[Données Analytique Lake U-SQL](data-factory-usql-activity.md) | Données Azure Lake Analytique 
[DotNet](data-factory-use-custom-activities.md) | HDInsight [Hadoop] ou lot Azure
   

