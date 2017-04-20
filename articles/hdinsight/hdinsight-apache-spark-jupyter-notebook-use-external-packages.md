<properties 
    pageTitle="Utiliser les packages externes avec des blocs-notes Jupyter dans Apache explosion clusters sur HDInsight | Azure"
    description="Obtenir des instructions détaillées sur la configuration des blocs-notes Jupyter disponibles avec les clusters HDInsight Spark pour utiliser les packages explosion externes." 
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
    ms.date="10/28/2016" 
    ms.author="nitinme"/>


# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight-linux"></a>Utiliser les packages externes avec des blocs-notes Jupyter dans Apache explosion clusters sur HDInsight Linux

>[AZURE.NOTE] Cet article s’applique aux explosion 1.5.2 sur HDInsight 3.3 et explosion 1.6.2 sur HDInsight 3.4. 

Découvrez comment configurer un bloc-notes Jupyter cluster explosion Apache sur HDInsight (Linux) à utiliser externe, packages collaboré à la Communauté qui ne sont pas inclus de prédéfinies dans le cluster. 

Vous pouvez rechercher dans le [référentiel Maven](http://search.maven.org/) pour obtenir la liste complète des packages disponibles. Vous pouvez également obtenir une liste des packages disponibles provenant d’autres sources. Par exemple, une liste complète des packages collaboré à la Communauté est disponible dans [Les Packages explosion](http://spark-packages.org/).

Dans cet article, vous allez apprendre à utiliser le package [d’explosion csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) avec le bloc-notes Jupyter.

##<a name="prerequisites"></a>Conditions préalables

Vous devez disposer des éléments suivants :

- Un abonnement Azure. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster explosion Apache sur HDInsight Linux. Pour plus d’informations, voir [groupes de créer Apache explosion dans Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Utiliser les packages externes avec Jupyter blocs-notes 

1. À partir du [Portail Azure](https://portal.azure.com/), à partir de la startboard, cliquez sur la vignette pour votre cluster explosion (si vous l’épinglées à la startboard). Vous pouvez également accéder à votre cluster sous **Rechercher tout** > **Clusters HDInsight**.   

2. À partir de la carte de cluster explosion, cliquez sur **Liens rapides**, puis à partir de la carte **Du tableau de bord Cluster** , **Jupyter bloc-notes**. Si vous y êtes invité, entrez les informations d’identification d’administration pour le cluster.

    > [AZURE.NOTE] Le bloc-notes Jupyter peut également atteindre pour votre cluster en ouvrant l’URL suivante dans votre navigateur. Remplacez __NOMDUCLUSTER__ par le nom de votre cluster :
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Créer un bloc-notes. Cliquez sur **Nouveau**, puis cliquez sur **explosion**.

    ![Créer un bloc-notes Jupyter] (./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/hdispark.note.jupyter.createnotebook.png "Créer un bloc-notes Jupyter")

3. Un bloc-notes est créé et ouvert avec le nom Untitled.pynb. Cliquez sur le nom du bloc-notes dans la partie supérieure, puis entrez un nom convivial.

    ![Fournir un nom pour le bloc-notes] (./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/hdispark.note.jupyter.notebook.name.png "Fournir un nom pour le bloc-notes")

4. Vous allez utiliser la `%%configure` magie pour configurer le bloc-notes pour utiliser un package externes. Dans les blocs-notes qui utilisent des lots externes, vérifiez que vous appelez le `%%configure` magique dans la première cellule de code. Cela garantit que le noyau est configuré pour utiliser le package avant le démarrage de la session.

        %%configure
        { "packages":["com.databricks:spark-csv_2.10:1.4.0"] }


    >[AZURE.IMPORTANT] Si vous oubliez configurer le noyau dans la première cellule, vous pouvez utiliser la `%%configure` avec la `-f` paramètre, mais qui va redémarrer la session et tous les cours seront perdues.

5. Dans l’extrait ci-dessus, `packages` attend la liste des coordonnées maven dans un référentiel Central Maven. Dans cet extrait, `com.databricks:spark-csv_2.10:1.4.0` est les coordonnées maven **explosion csv** package. Voici comment vous construisez les coordonnées d’un package.

    un. Recherchez le package dans le référentiel Maven. Pour ce didacticiel, nous utilisons [explosion csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
    
    b. À partir du référentiel, rassembler les valeurs pour **GroupId**, **ArtifactId**et la **Version**.

    ![Lots externes utilisation avec Jupyter bloc-notes] (./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "Lots externes utilisation avec Jupyter bloc-notes")

    c. Concaténer trois valeurs, séparés par un signe deux-points ( :)****).

        com.databricks:spark-csv_2.10:1.4.0

6. Exécuter la cellule de code avec la `%%configure` magie. Cela configurera la session Livy sous-jacente pour utiliser le package que vous avez fourni. Dans les cellules suivantes dans le bloc-notes, vous pouvez désormais utiliser le package, comme illustré ci-dessous.

        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

7. Vous pouvez ensuite exécuter les extraits, comme indiqué ci-dessous, pour afficher les données à partir de la dataframe que vous avez créé à l’étape précédente.

        df.show()

        df.select("Time").count()


## <a name="seealso"></a>Voir aussi


* [Vue d’ensemble : Apache explosion sur Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scénarios

* [Explosion avec BI : effectuer une analyse de données interactives à l’aide d’explosion dans HDInsight avec les outils de décisionnel](hdinsight-apache-spark-use-bi-tools.md)

* [Explosion avec apprentissage automatique : utilisation explosion dans HDInsight pour analyser la température de construction à l’aide de données HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Explosion avec apprentissage automatique : utilisation explosion dans HDInsight pour prévoir des résultats de l’inspection alimentaires](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Diffusion en continu explosion : Utilisation explosion dans HDInsight pour la création d’applications en continu en temps réel](hdinsight-apache-spark-eventhub-streaming.md)

* [Analyse de journal de site Web à l’aide d’explosion dans HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Créer et exécuter des applications

* [Créer une application autonome à l’aide de Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Exécution de tâches à distance sur un cluster explosion à l’aide de Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Outils et extensions

* [Plug-in des outils HDInsight IntelliJ idée permet de créer et soumettre des applications Scala d’explosion](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Plug-in utilisation HDInsight outils idée IntelliJ déboguer applications explosion à distance](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Utiliser les blocs-notes Zeppelin avec un cluster explosion sur HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Noyaux disponibles pour Jupyter bloc-notes cluster explosion pour HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Installer Jupyter sur votre ordinateur et vous connecter à un cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gérer les ressources

* [Gestion des ressources pour le cluster Apache explosion dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Suivre et débogage travaux s’exécutant sur un cluster Apache explosion dans HDInsight](hdinsight-apache-spark-job-debugging.md)
