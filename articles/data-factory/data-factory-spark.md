<properties 
    pageTitle="Appeler des programmes d’explosion provenant d’une usine de données Azure" 
    description="Apprenez à appeler des programmes d’explosion provenant d’une usine de données Azure à l’aide de l’activité MapReduce." 
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
    ms.date="08/25/2016" 
    ms.author="spelluru"/>

# <a name="invoke-spark-programs-from-data-factory"></a>Appeler des programmes d’explosion provenant d’une usine de données
## <a name="introduction"></a>Introduction
Vous pouvez utiliser l’activité MapReduce dans un pipeline de données par défaut pour exécuter des programmes explosion sur votre cluster HDInsight Spark. Reportez-vous à l’article [MapReduce activité](data-factory-map-reduce.md) pour plus d’informations sur l’utilisation de l’activité avant la lecture de cet article. 

## <a name="spark-sample-on-github"></a>Exemple d’explosion sur GitHub
[Explosion - exemple Data Factory sur GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) montre comment utiliser MapReduce activité pour appeler un programme explosion. Le programme explosion copie simplement les données d’un seul conteneur Blob Azure vers un autre. 

## <a name="data-factory-entities"></a>Entités Factory de données
Le dossier **Explosion-chargeur/src/ADFJsons** contient des fichiers pour entités Data Factory (services liés, jeux de données, pipeline).  

Il existe deux **services liées** dans cet exemple : stockage Azure et Azure HDInsight. Spécifiez votre nom de stockage Azure et les valeurs de clé dans **StorageLinkedService.json** et clusterUri, nom d’utilisateur et mot de passe dans **HDInsightLinkedService.json**.

Il existe deux **jeux de données** dans cet exemple : **input.json** et **output.json**. Ces fichiers sont situés dans le dossier **jeux de données** .  Ces fichiers représentent des jeux de données d’entrée et de sortie pour l’activité MapReduce

Pipelines exemple se trouvent dans le dossier **ADFJsons/Pipeline** . Passez en revue un pipeline pour mieux comprendre comment appeler un programme explosion à l’aide de l’activité MapReduce. 

L’activité MapReduce est configurée pour appeler **com.adf.sparklauncher.jar** dans le conteneur **adflibs** dans votre espace de stockage Azure (spécifié dans le StorageLinkedService.json). Le code source pour ce programme est en explosion-chargeur/src/principaux/java/com/chargeur/dossier et il appelle soumettre explosion et exécuter explosion travaux. 

> [AZURE.IMPORTANT] 
> Lisez [Lisezmoi. TXT](https://github.com/Azure/Azure-DataFactory/blob/master/Samples/Spark/README.txt) pour obtenir les informations supplémentaires et les plus récentes avant d’utiliser l’échantillon. 
>  
> Utiliser votre propre cluster HDInsight Spark avec cette approche pour appeler des programmes d’explosion à l’aide de l’activité MapReduce. À l’aide d’un cluster de HDInsight à la demande n’est pas pris en charge.   


## <a name="see-also"></a>Voir aussi
- [Activité Hive](data-factory-hive-activity.md)
- [Cochon activité](data-factory-pig-activity.md)
- [Activité MapReduce](data-factory-map-reduce.md)
- [Activité de diffusion en continu Hadoop](data-factory-hadoop-streaming-activity.md)
- [Appeler scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)
