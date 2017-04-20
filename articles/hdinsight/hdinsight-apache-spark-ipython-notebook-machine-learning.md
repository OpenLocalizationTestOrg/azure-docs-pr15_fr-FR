<properties 
    pageTitle="Permet de créer des applications de formation machine sur HDInsight Apache explosion | Microsoft Azure" 
    description="Instructions détaillées sur l’utilisation des blocs-notes avec Apache explosion pour créer des applications d’apprentissage automatique" 
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
    ms.date="10/05/2016" 
    ms.author="nitinme"/>


# <a name="build-machine-learning-applications-to-run-on-apache-spark-clusters-on-hdinsight-linux"></a>Créer des applications d’apprentissage automatique pour s’exécuter sur clusters explosion Apache sur HDInsight Linux

Apprenez à créer une machine d’apprentissage application à l’aide d’un cluster Apache explosion dans HDInsight. Cet article vous explique comment utiliser le bloc-notes Jupyter disponible avec le cluster pour créer et tester l’application. L’application utilise les données d’exemple HVAC.csv disponible dans tous les clusters par défaut.

**Conditions requises :**

Vous devez disposer des éléments suivants :

- Un abonnement Azure. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster explosion Apache sur HDInsight Linux. Pour plus d’informations, voir [groupes de créer Apache explosion dans Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md). 

##<a name="data"></a>Afficher les données

Avant de commencer à créer l’application, laissez-nous comprendre la structure des données et le type de l’analyse de que faire, nous allons sur les données. 

Dans cet article, nous utilisons l’exemple de fichier de données **HVAC.csv** qui est disponible dans le compte de stockage Azure que vous avez associé le cluster HDInsight. Dans le compte de stockage, le fichier est en **\HdiSamples\HdiSamples\SensorSampleData\hvac**. Télécharger et ouvrir le fichier CSV pour obtenir un instantané des données.  

![Instantané de données HVAC] (./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.ml.show.data.png "Instantané des données HVAC")

Les données montrent la température cible et la température réelle d’un bâtiment comportant des systèmes HVAC installés. Supposons que la colonne **système** représente l’ID du système et la colonne **SystemAge** représente le nombre d’années que le système HVAC a été en place chez le bâtiment.

Nous utilisons ces données pour prédire si une construction seront chaudes ou surgelé basée sur la température cible, étant donnée un ID système et l’âge de système.

##<a name="app"></a>Écrire une application d’apprentissage machine à l’aide d’explosion MLlib

Dans cette application, nous utilisons un pipeline ML explosion pour effectuer une classification de document. Dans le pipeline, nous fractionner le document en mots, de convertir les mots dans un vecteur fonctionnalité numérique et enfin créer un modèle de prévision en utilisant les vecteurs fonctionnalité et les étiquettes. Procédez comme suit pour créer l’application.

1. À partir du [Portail Azure](https://portal.azure.com/), à partir de la startboard, cliquez sur la vignette pour votre cluster explosion (si vous l’épinglées à la startboard). Vous pouvez également accéder à votre cluster sous **Rechercher tout** > **Clusters HDInsight**.   

2. À partir de la carte de cluster explosion, cliquez sur **Tableau de bord Cluster**, puis cliquez sur **Bloc-notes Jupyter**. Si vous y êtes invité, entrez les informations d’identification d’administration pour le cluster.

    > [AZURE.NOTE] Vous pouvez également atteigne le bloc-notes Jupyter pour votre cluster en ouvrant l’URL suivante dans votre navigateur. Remplacez __NOMDUCLUSTER__ par le nom de votre cluster :
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Créer un bloc-notes. Cliquez sur **Nouveau**, puis cliquez sur **PySpark**.

    ![Créer un bloc-notes Jupyter] (./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.note.jupyter.createnotebook.png "Créer un bloc-notes Jupyter")

3. Un bloc-notes est créé et ouvert avec le nom Untitled.pynb. Cliquez sur le nom du bloc-notes dans la partie supérieure, puis entrez un nom convivial.

    ![Fournir un nom pour le bloc-notes] (./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.note.jupyter.notebook.name.png "Fournir un nom pour le bloc-notes")

3. Étant donné que vous avez créé un bloc-notes à l’aide du noyau PySpark, vous n’avez pas besoin créer les contextes explicitement. Les contextes explosion et Hive est automatiquement créées pour vous lors de l’exécution de la première cellule de code. Vous pouvez commencer en important les types requis pour ce scénario. Collez l’extrait de code suivante dans une cellule vide, puis appuyez sur **MAJ + ENTRÉE**. 

        from pyspark.ml import Pipeline
        from pyspark.ml.classification import LogisticRegression
        from pyspark.ml.feature import HashingTF, Tokenizer
        from pyspark.sql import Row
        
        import os
        import sys
        from pyspark.sql.types import *
        
        from pyspark.mllib.classification import LogisticRegressionWithSGD
        from pyspark.mllib.regression import LabeledPoint
        from numpy import array
        
        
     
4. Vous devez maintenant charger les données (hvac.csv), l’analyser et utilisez-le pour former le modèle. Pour ce faire, vous définissez une fonction qui vérifie si la température réelle du bâtiment est supérieure à la température cible. Si la température réelle est supérieure, la construction est actif, indiqués par la valeur **1.0**. Si la température réelle est plus petit, le bâtiment est froid, indiqués par la valeur **0.0**. 

    Coller l’extrait de code suivante dans une cellule vide et appuyez sur **MAJ + ENTRÉE**.

        
        # List the structure of data for better understanding. Becuase the data will be
        # loaded as an array, this structure makes it easy to understand what each element
        # in the array corresponds to

        # 0 Date
        # 1 Time
        # 2 TargetTemp
        # 3 ActualTemp
        # 4 System
        # 5 SystemAge
        # 6 BuildingID

        LabeledDocument = Row("BuildingID", "SystemInfo", "label")

        # Define a function that parses the raw CSV file and returns an object of type LabeledDocument
        
        def parseDocument(line):
            values = [str(x) for x in line.split(',')]
            if (values[3] > values[2]):
                hot = 1.0
            else:
                hot = 0.0        
    
            textValue = str(values[4]) + " " + str(values[5])
    
            return LabeledDocument((values[6]), textValue, hot)

        # Load the raw HVAC.csv file, parse it using the function
        data = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
        training = documents.toDF()


5. Configurer les opportunités de formation explosion machine qui se compose de trois étapes : Générateur de jetons, hashingTF et longue distance. Pour plus d’informations sur ce qui est un pipeline et son fonctionnement voir <a href="http://spark.apache.org/docs/latest/ml-guide.html#how-it-works" target="_blank">apprentissage pipeline de l’ordinateur explosion</a>.

    Coller l’extrait de code suivante dans une cellule vide et appuyez sur **MAJ + ENTRÉE**.

        tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
        hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
        lr = LogisticRegression(maxIter=10, regParam=0.01)
        pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

6. Ajuster le pipeline au document de formation. Coller l’extrait de code suivante dans une cellule vide et appuyez sur **MAJ + ENTRÉE**.

        model = pipeline.fit(training)

7. Vérifier le document de formation au point de contrôle votre avancement avec l’application. Coller l’extrait de code suivante dans une cellule vide et appuyez sur **MAJ + ENTRÉE**.

        training.show()

    Le résultat semblable au suivant doit s’afficher :

        +----------+----------+-----+
        |BuildingID|SystemInfo|label|
        +----------+----------+-----+
        |         4|     13 20|  0.0|
        |        17|      3 20|  0.0|
        |        18|     17 20|  1.0|
        |        15|      2 23|  0.0|
        |         3|      16 9|  1.0|
        |         4|     13 28|  0.0|
        |         2|     12 24|  0.0|
        |        16|     20 26|  1.0|
        |         9|      16 9|  1.0|
        |        12|       6 5|  0.0|
        |        15|     10 17|  1.0|
        |         7|      2 11|  0.0|
        |        15|      14 2|  1.0|
        |         6|       3 2|  0.0|
        |        20|     19 22|  0.0|
        |         8|     19 11|  0.0|
        |         6|      15 7|  0.0|
        |        13|      12 5|  0.0|
        |         4|      8 22|  0.0|
        |         7|      17 5|  0.0|
        +----------+----------+-----+


    Revenir en arrière et vérifier le résultat par rapport au fichier CSV brut. Par exemple, la première ligne du fichier CSV comporte ces données :

    ![Instantané de données HVAC] (./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.ml.show.data.first.row.png "Instantané des données HVAC")

    Notez que la température réelle est inférieure à la température cible suggérant que le bâtiment est froid. Dans le résultat de la formation, la valeur d' **étiquette** dans la première ligne est donc **0.0**, ce qui signifie que la construction n’est pas active.

8.  Préparer un jeu de données pour exécuter le modèle formé par rapport à. Pour ce faire, nous serait passer sur un ID système et l’âge de système (indiquée dans le résultat de la formation comme **SystemInfo** ) et le modèle serait prédire si le bâtiment avec cet ID système et l’âge de système serait plus chaud (indiqués par 1.0) ou plus froide (indiqués par 0.0).

    Coller l’extrait de code suivante dans une cellule vide et appuyez sur **MAJ + ENTRÉE**.
        
        # SystemInfo here is a combination of system ID followed by system age
        Document = Row("id", "SystemInfo")
        test = sc.parallelize([(1L, "20 25"),
                      (2L, "4 15"),
                      (3L, "16 9"),
                      (4L, "9 22"),
                      (5L, "17 10"),
                      (6L, "7 22")]) \
            .map(lambda x: Document(*x)).toDF() 

9. Pour finir, réaliser des prévisions sur les données de test. Coller l’extrait de code suivante dans une cellule vide et appuyez sur **MAJ + ENTRÉE**.

        # Make predictions on test documents and print columns of interest
        prediction = model.transform(test)
        selected = prediction.select("SystemInfo", "prediction", "probability")
        for row in selected.collect():
            print row

10. Vous devriez voir un résultat semblable à ce qui suit :

        Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
        Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
        Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
        Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
        Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
        Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))

    La première ligne dans la prévision, vous pouvez voir que pour un système HVAC avec ID 20 et l’âge de système de 25 ans, bâtiment sera hot (**prédiction = 1.0**). La première valeur pour DenseVector (0.49999) correspond à la prévision 0.0 et la deuxième valeur (0.5001) correspond à la prévision 1.0. Dans la sortie, même si la deuxième valeur n’est très légèrement plus élevée, le modèle affiche **prédiction = 1.0**.

11. Une fois que vous avez terminé d’exécuter l’application, vous devez arrêter le bloc-notes pour libérer les ressources. Pour ce faire, dans le menu **fichier** sur le bloc-notes, cliquez sur **Fermer et arrêter**. Cette s’arrêter et fermer le bloc-notes.
           

##<a name="anaconda"></a>Utiliser Anaconda scikit-plus d’une bibliothèque pour apprentissage automatique

Clusters explosion Apache sur HDInsight incluent bibliothèques Anaconda. Cela inclut également la **scikit-Découvrez** bibliothèque d’apprentissage automatique. La bibliothèque inclut également des divers jeux de données que vous pouvez utiliser pour créer des applications exemple directement à partir d’un bloc-notes Jupyter. Pour obtenir des exemples sur l’utilisation de la scikit-apprendre bibliothèque, voir [http://scikit-learn.org/stable/auto_examples/index.html](http://scikit-learn.org/stable/auto_examples/index.html).

##<a name="seealso"></a>Voir aussi

* [Vue d’ensemble : Apache explosion sur Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scénarios

* [Explosion avec BI : effectuer une analyse de données interactives à l’aide d’explosion dans HDInsight avec les outils de décisionnel](hdinsight-apache-spark-use-bi-tools.md)

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

* [Utiliser les packages externes avec Jupyter blocs-notes](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Installer Jupyter sur votre ordinateur et vous connecter à un cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gérer les ressources

* [Gestion des ressources pour le cluster Apache explosion dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Suivre et débogage travaux s’exécutant sur un cluster Apache explosion dans HDInsight](hdinsight-apache-spark-job-debugging.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-weblogs-sample]: hdinsight-hive-analyze-website-log.md
[hdinsight-sensor-data-sample]: hdinsight-hive-analyze-sensor-data.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md
