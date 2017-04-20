<properties
   pageTitle="Découvrez Hadoop dans HDInsight avec la galerie d’exemples de | Microsoft Azure"
   description="Découvrez rapidement Hadoop en exécutant les exemples d’applications à partir de la galerie HDInsight prise en main. Utilisez les exemples de données ou fournir vos propres."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.workload="big-data"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="10/21/2016"
   ms.author="jgao"/>

# <a name="learn-hadoop-by-using-the-azure-hdinsight-getting-started-gallery"></a>Découvrez Hadoop à l’aide de la galerie Azure HDInsight prise en main

La galerie de mise en route de mise en route est disponible uniquement pour les clusters HDInsight fonctionnant sous Windows. La galerie fournit un simple et une méthode rapide Découvrez Hadoop en exécutant les exemples d’applications dans HDInsight. Certains exemples fournis avec des exemples de données. Vous pouvez fournir vos propres données pour les exemples restants. Il existe pour l’instant, les exemples suivants six (avec les autres sont en préparation) :

- Solutions avec vos données Azure
    - Analyse des journaux de site Web Microsoft Azure
    - Analytique de stockage de Microsoft Azure
- Solutions avec des exemples de données
    - Analyse des données capteur
    - Analyse des tendances Twitter
    - Analyse des journaux de site Web
    - Recommandation de film mahout

![HDInsight Hadoop, vague d’et HBase prise en main galerie de solutions, y compris des exemples de données.][hdinsight.sample.gallery]

La vidéo suivante montre comment exécuter l’exemple d’analyse de tendance Twitter :

<center><a href="https://www.youtube.com/embed/7ePbHot1SN4">https://www.YouTube.com/EMBED/7ePbHot1SN4></a></center>

Le tableau de bord sont accessibles en accédant à http://<YourHDInsightClusterName>.azurehdinsight.net/ ou à partir du portail Azure.

**Pour exécuter un exemple de la galerie de mise en route de mise en route**

1. Connectez-vous au [portail Azure][azure.portal].
2. Cliquez sur **Parcourir** dans le menu gauche et cliquez sur **Clusters HDInsight**, puis cliquez sur le nom de votre cluster.
3. Cliquez sur **tableau de bord** dans le menu supérieur.
4. Entrez le nom d’utilisateur et mot de passe pour l’utilisateur HTTP (également appelé l’utilisateur cluster).
6. Cliquez sur la **Prise en main galerie** en haut de la page.
7. Cliquez sur un des exemples. Chaque exemple indique la procédure détaillée pour l’exécuter. L’image suivante montre l’exemple d’analyse de tendance Twitter :

    ![Exemple d’analyse HDInsight Twitter tendance.][hdinsight.twitter.sample]

## <a name="next-steps"></a>Étapes suivantes
Autres méthodes pour en savoir plus sur HDInsight sont les suivantes :

- [Plan d’apprentissage HDInsight][hdinsight.learn.map]
- [HDInsight infographic][hdinsight.infographic]

<!--Image references-->
[hdinsight.sample.gallery]: ./media/hdinsight-learn-hadoop-use-sample-gallery/HDInsight-Getting-Started-Gallery.png
[hdinsight.twitter.sample]: ./media/hdinsight-learn-hadoop-use-sample-gallery/HDInsight-Twitter-Trend-Analysis-sample.png

<!--Link references-->
[hdinsight.learn.map]: https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/
[hdinsight.infographic]: http://go.microsoft.com/fwlink/?linkid=523960
[azure.portal]:https://portal.azure.com
