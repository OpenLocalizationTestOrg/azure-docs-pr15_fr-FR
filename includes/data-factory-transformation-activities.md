Azure Data Factory prend en charge les activités transformation suivantes qui peuvent être ajoutées aux canalisations soit individuellement ou en chaîne avec une autre activité.

Activité de transformation de données |  Environnement informatique 
:----------------------- | :--------------------
[Hive](../articles/data-factory/data-factory-hive-activity.md) | HDInsight [Hadoop] 
[Cochon](../articles/data-factory/data-factory-pig-activity.md) | HDInsight [Hadoop]  
[MapReduce](../articles/data-factory/data-factory-map-reduce.md) | HDInsight [Hadoop]  
[Hadoop diffusion en continu](../articles/data-factory/data-factory-hadoop-streaming-activity.md) | HDInsight [Hadoop]
[Machine d’activités d’apprentissage : l’exécution du lot et des ressources de mise à jour](../articles/data-factory/data-factory-azure-ml-batch-execution-activity.md) | Machine virtuelle Azure 
[Procédure stockée](../articles/data-factory/data-factory-stored-proc-activity.md) | SQL Azure, Data Warehouse SQL Azure ou SQL Server |
[Données Analytique Lake U-SQL](../articles/data-factory/data-factory-usql-activity.md) | Données Azure Lake Analytique 
[DotNet](../articles/data-factory/data-factory-use-custom-activities.md) | HDInsight [Hadoop] ou lot Azure
   
> [AZURE.NOTE] 
> Vous pouvez utiliser MapReduce activité pour exécuter des programmes explosion sur votre cluster HDInsight Spark. Pour plus d’informations, voir [programmes appeler explosion d’Azure Data Factory](../articles/data-factory/data-factory-spark.md) .
> Vous pouvez créer une activité personnalisée pour exécuter des scripts R sur votre cluster HDInsight avec R installé. Voir [exécuter le Script R utilisation Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample).