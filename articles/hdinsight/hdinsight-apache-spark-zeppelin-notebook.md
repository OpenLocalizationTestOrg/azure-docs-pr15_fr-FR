<properties 
    pageTitle="Utiliser les blocs-notes Zeppelin avec cluster explosion sur HDInsight Linux | Microsoft Azure" 
    description="Obtenir des instructions détaillées sur l’utilisation des blocs-notes Zeppelin avec clusters explosion sur HDInsight Linux." 
    services="hdinsight" 
    documentationCenter="" 
    authors="nitinme" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/05/2016" 
    ms.author="nitinme"/>


# <a name="use-zeppelin-notebooks-with-apache-spark-cluster-on-hdinsight-linux"></a>Utiliser les blocs-notes Zeppelin avec cluster explosion Apache sur HDInsight Linux

HDInsight Spark clusters incluent blocs-notes Zeppelin que vous pouvez utiliser pour exécuter des travaux explosion. Dans cet article, vous allez apprendre à utiliser le bloc-notes Zeppelin sur un cluster HDInsight.


**Conditions requises :**

* Un abonnement Azure. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Un cluster explosion Apache. Pour plus d’informations, voir [groupes de créer Apache explosion dans Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="launch-a-zeppelin-notebook"></a>Lancer un bloc-notes Zeppelin

1. À partir de la carte de cluster explosion, cliquez sur **Tableau de bord Cluster**, puis cliquez sur **Bloc-notes Zeppelin**. Si vous y êtes invité, entrez les informations d’identification d’administration pour le cluster.

    > [AZURE.NOTE] Vous pouvez également atteindre le bloc-notes Zeppelin pour votre cluster en ouvrant l’URL suivante dans votre navigateur. Remplacez __NOMDUCLUSTER__ par le nom de votre cluster :
    >
    > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Créer un bloc-notes. Dans le volet d’en-tête, cliquez sur **le bloc-notes**, puis cliquez sur **Créer une Note**.

    ![Créer un bloc-notes Zeppelin] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.createnewnote.png "Créer un bloc-notes Zeppelin")

    Entrez un nom pour le bloc-notes, puis cliquez sur **Créer une Note**.

3. Assurez-vous également que l’en-tête du bloc-notes affiche un état connecté. Il est indiqué par un point vert dans le coin supérieur droit.

    ![État des blocs-notes Zeppelin] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.newnote.connected.png "État des blocs-notes Zeppelin")

4. Charger les exemples de données dans une table temporaire. Lorsque vous créez un cluster explosion dans HDInsight, l’exemple de fichier de données, **hvac.csv**, est copié dans le compte de stockage associé sous **\HdiSamples\SensorSampleData\hvac**.

    Dans le paragraphe vide est créé par défaut dans le bloc-notes, collez l’extrait de code suivante.

        %livy.spark
        //The above magic instructs Zeppelin to use the Livy Scala interpreter

        // Create an RDD using the default Spark context, sc
        val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
        
        // Define a schema
        case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
        
        // Map the values in the .csv file to the schema
        val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
            s => Hvac(s(0), 
                    s(1),
                    s(2).toInt,
                    s(3).toInt,
                    s(6)
            )
        ).toDF()
        
        // Register as a temporary table called "hvac"
        hvac.registerTempTable("hvac")
        
    Appuyez sur **MAJ + ENTRÉE** ou cliquez sur le bouton **lecture** pour le paragraphe exécuter l’extrait de code. L’état dans le coin droit du paragraphe doit vont de prêt, en attente, en cours d’exécution sur terminé. Le résultat s’affiche en bas de la même paragraphe. La capture d’écran ressemble à ceci :

    ![Créer une table à partir des données brutes temporaire] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.note.loaddDataintotable.png "Créer une table à partir des données brutes temporaire")

    Vous pouvez également fournir un titre à chaque paragraphe. Dans le coin droit, cliquez sur l’icône **paramètres** , puis cliquez sur **Afficher le titre**.

5. Vous pouvez maintenant exécuter des instructions SQL explosion sur la table **hvac** . Collez la requête suivante dans un nouveau paragraphe. La requête extrait l’identificateur de bâtiment et la différence entre la cible et les températures réels pour chaque bâtiment à une date donnée. Appuyez sur **MAJ + ENTRÉE**.

        %sql
        select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 

    L’instruction **sql %** au début indique le bloc-notes à utiliser le relais Livy Scala.

    La capture d’écran suivante montre la sortie.

    ![Exécuter une instruction SQL à explosion en utilisant le bloc-notes] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.note.sparksqlquery1.png "Exécuter une instruction SQL à explosion en utilisant le bloc-notes")

     Cliquez sur les options d’affichage (mis en surbrillance dans rectangle) pour basculer entre différentes représentations pour le même résultat. Cliquez sur **paramètres** pour choisir quels constitue la clé et les valeurs dans les résultats. La capture d’écran ci-dessus utilise **buildingID** comme clé et la moyenne de **temp_diff** comme valeur.

    
6. Vous pouvez également exécuter des instructions SQL explosion à l’aide de variables dans la requête. L’extrait de code suivant montre comment définir une variable, **Temp**, dans la requête avec les valeurs possibles que vous voulez interroger avec. Lorsque vous exécutez tout d’abord la requête, une liste déroulante est remplie automatiquement avec les valeurs spécifiées pour la variable.

        %sql
        select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}" 

    Collez cet extrait de code dans un nouveau paragraphe, puis appuyez sur **MAJ + ENTRÉE**. La capture d’écran suivante montre la sortie.

    ![Exécuter une instruction SQL à explosion en utilisant le bloc-notes] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.note.sparksqlquery2.png "Exécuter une instruction SQL à explosion en utilisant le bloc-notes")

    Pour les requêtes ultérieures, vous pouvez sélectionner une nouvelle valeur dans la liste déroulante et exécutez à nouveau la requête. Cliquez sur **paramètres** pour choisir quels constitue la clé et les valeurs dans les résultats. La capture d’écran ci-dessus utilise **buildingID** comme clé, la moyenne de **temp_diff** comme valeur et **targettemp** en tant que groupe.

7. Redémarrez le relais Livy pour quitter l’application. Pour ce faire, ouvrez Paramètres de relais en cliquant sur le nom d’utilisateur à partir du coin supérieur droit, puis cliquez sur **relais**.

    ![Barre de lancement relais] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Ruche sortie")

2. Faites défiler jusqu'à Livy relais paramètres, puis sur **redémarrer**.

    ![Redémarrez l’intepreter Livy] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.zeppelin.restart.interpreter.png "Redémarrez l’intepreter Zeppelin")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Comment utiliser des lots externes avec le bloc-notes ?

Vous pouvez configurer le bloc-notes Zeppelin cluster explosion Apache sur HDInsight (Linux) pour utiliser les packages externes, collaboré à la Communauté qui ne sont pas inclus-de-l’emploi du cluster. Vous pouvez rechercher dans le [référentiel Maven](http://search.maven.org/) pour obtenir la liste complète des packages disponibles. Vous pouvez également obtenir une liste des packages disponibles provenant d’autres sources. Par exemple, une liste complète des packages collaboré à la Communauté est disponible dans [Les Packages explosion](http://spark-packages.org/).

Dans cet article, vous allez apprendre à utiliser le package [d’explosion csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) avec le bloc-notes Jupyter.

1. Ouvrir les paramètres de relais. À partir du coin supérieur droit, cliquez sur le nom d’utilisateur, puis cliquez sur **relais**.

    ![Barre de lancement relais] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Ruche sortie")

2. Faites défiler jusqu'à Livy relais paramètres, puis sur **Modifier**.

    ![Modifier les paramètres de relais] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "Modifier les paramètres de relais")

3. Ajouter une nouvelle clé, appelée **livy.spark.jars.packages** et définissez sa valeur dans le format `group:id:version`. Par conséquent, si vous souhaitez utiliser le package [explosion csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) , vous devez définir la valeur de la clé à `com.databricks:spark-csv_2.10:1.4.0`.

    ![Modifier les paramètres de relais] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "Modifier les paramètres de relais")

    Cliquez sur **Enregistrer** , puis redémarrez le relais Livy.

4. **Conseil**: Si vous voulez savoir comment arrivent à la valeur de la touche entrée ci-dessus, voici comment.

    un. Recherchez le package dans le référentiel Maven. Pour ce didacticiel, nous avons utilisé [explosion csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
    
    b. À partir du référentiel, rassembler les valeurs pour **GroupId**, **ArtifactId**et la **Version**.

    ![Lots externes utilisation avec Jupyter bloc-notes] (./media/hdinsight-apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Lots externes utilisation avec Jupyter bloc-notes")

    c. Concaténer trois valeurs, séparés par un signe deux-points ( :)****).

        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Où sont enregistrés les blocs-notes Zeppelin ?

Les blocs-notes Zeppelin sont enregistrées dans le headnodes cluster. Par conséquent, si vous supprimez le cluster, les blocs-notes seront supprimées également. Si vous souhaitez conserver vos blocs-notes pour une utilisation ultérieure sur autre cluster, vous devez les exporter une fois que vous avez terminé d’exécuter les tâches. Pour exporter un bloc-notes, cliquez sur l’icône **Exporter** comme le montre l’image ci-dessous.

![Télécharger le bloc-notes] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Télécharger le bloc-notes")

Cela enregistre le bloc-notes comme un fichier JSON dans l’emplacement de téléchargement.

## <a name="livy-session-management"></a>Gestion des sessions Livy

Lorsque vous exécutez le premier paragraphe de code dans votre bloc-notes Zeppelin, une nouvelle session Livy est créée dans votre cluster HDInsight Spark. Cette session est partagée sur tous les blocs-notes Zeppelin que vous créez par la suite. Si pour une raison quelconque le Livy session est supprimé (redémarrage cluster, etc.), vous ne serez pas en mesure d’exécuter des travaux du bloc-notes Zeppelin.

Dans ce cas, vous devez effectuer les étapes suivantes avant de commencer à exécuter les tâches d’un bloc-notes Zeppelin. 

1. Redémarrez le relais Livy du bloc-notes Zeppelin. Pour ce faire, ouvrez Paramètres de relais en cliquant sur le nom d’utilisateur à partir du coin supérieur droit, puis cliquez sur **relais**.

    ![Barre de lancement relais] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Ruche sortie")

2. Faites défiler jusqu'à Livy relais paramètres, puis sur **redémarrer**.

    ![Redémarrez l’intepreter Livy] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.zeppelin.restart.interpreter.png "Redémarrez l’intepreter Zeppelin")

3. Exécuter une cellule de code à partir d’un bloc-notes existant Zeppelin. Cela crée une nouvelle session Livy du cluster HDInsight.

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

* [Noyaux disponibles pour Jupyter bloc-notes cluster explosion pour HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Utiliser les packages externes avec Jupyter blocs-notes](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Installer Jupyter sur votre ordinateur et vous connecter à un cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gérer les ressources

* [Gestion des ressources pour le cluster Apache explosion dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Suivre et débogage travaux s’exécutant sur un cluster Apache explosion dans HDInsight](hdinsight-apache-spark-job-debugging.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md 







