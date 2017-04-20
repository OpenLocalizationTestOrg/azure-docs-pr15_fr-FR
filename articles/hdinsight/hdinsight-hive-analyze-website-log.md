<properties 
    pageTitle="Utiliser Hive avec Hadoop pour l’analyse des journaux site Web | Microsoft Azure" 
    description="Découvrez comment utiliser Hive avec HDInsight pour analyser les journaux de site Web. Vous devez utiliser un fichier journal comme entrées dans un tableau de HDInsight et utilisez HiveQL pour interroger les données." 
    services="hdinsight" 
    documentationCenter="" 
    authors="nitinme" 
    manager="jhubbard" 
    editor="cgronlun"
    tags="azure-portal"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/17/2016" 
    ms.author="nitinme"/>

# <a name="use-hive-with-hdinsight-to-analyze-logs-from-websites"></a>Utiliser Hive avec HDInsight pour analyser les journaux des sites Web

Découvrez comment utiliser HiveQL avec HDInsight pour analyser les journaux d’un site Web. Analyse de journal de site Web peut être utilisée pour segmenter votre audience en fonction d’activités similaires, classer les visiteurs du site par données démographiques et pour déterminer le contenu leur affichage, qu’ils proviennent de sites Web et ainsi de suite.

Dans cet exemple, vous allez utiliser un cluster HDInsight pour analyser les fichiers journaux de site Web pour obtenir claires sur la fréquence des visites sur le site Web à partir des sites Web externes dans un jour. Vous allez également générer un résumé des erreurs de site Web que les utilisateurs rencontrent. Vous allez apprendre comment :

- Se connecter à un espace de stockage Blob Azure, qui contient les fichiers journaux de site Web.
- Créer des tables HIVE pour interroger ces fichiers journaux.
- Créer des requêtes HIVE pour analyser les données.
- Utiliser Microsoft Excel pour vous connecter à HDInsight (à l’aide (ODBC) open database connectivity pour récupérer les données analysées.

![HDI. Samples.Website.Log.Analysis][img-hdi-weblogs-sample]

##<a name="prerequisites"></a>Conditions préalables

- Vous devez avoir configuré un cluster Hadoop sur Azure HDInsight. Pour plus d’informations, voir [Mise en service HDInsight Clusters][hdinsight-provision]. 
- Vous devez disposer de Microsoft Excel 2013 ou Excel 2010 est installé.
- Vous devez disposer de [Microsoft le pilote ODBC ruche](http://www.microsoft.com/download/details.aspx?id=40886) pour importer des données à partir de Hive dans Excel.


##<a name="to-run-the-sample"></a>Pour exécuter l’exemple

1. À partir du [Portail Azure](https://portal.azure.com/), à partir de la Startboard (si vous épinglé le cluster il), cliquez sur la vignette cluster sur lequel vous voulez exécuter l’exemple.

2. À partir de la carte cluster, sous **Liens rapides**, cliquez sur **Tableau de bord Cluster**, puis cliquez sur **Tableau de bord HDInsight Cluster**à partir de la carte **Du tableau de bord Cluster** . Par ailleurs, vous pouvez ouvrir directement le tableau de bord à l’aide de l’URL suivante :

        https://<clustername>.azurehdinsight.net
    
    Lorsque vous y êtes invité, s’authentifier en utilisant le nom d’utilisateur administrateur et le mot de passe utilisé lors de la configuration du cluster.
  
2. Dans la page web qui s’ouvre, cliquez sur l’onglet **Prise en main galerie** et puis, sous la catégorie **Solutions avec des exemples de données** , cliquez sur l’exemple de **Site Web journal d’analyse** .

3. Suivez les instructions fournies dans la page web pour terminer l’échantillon.

##<a name="next-steps"></a>Étapes suivantes
Essayez de l’exemple suivant : [analyse des données de capteur à l’aide de Hive avec HDInsight](hdinsight-hive-analyze-sensor-data.md).


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-sensor-data-sample]: ../hdinsight-use-hive-sensor-data-analysis.md

[img-hdi-weblogs-sample]: ./media/hdinsight-hive-analyze-website-log/hdinsight-weblogs-sample.png
 
