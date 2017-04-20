<properties 
    pageTitle="Utiliser des bibliothèques personnalisées avec un cluster HDInsight Spark analyser les journaux de site Web | Microsoft Azure" 
    description="Utiliser des bibliothèques personnalisées avec un cluster HDInsight Spark analyser les journaux de site Web" 
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

# <a name="analyze-website-logs-using-a-custom-library-with-apache-spark-cluster-on-hdinsight-linux"></a>Analyser les journaux de site Web à l’aide d’une bibliothèque personnalisée avec cluster explosion Apache sur HDInsight Linux

Ce bloc-notes montre comment analyser les données du journal à l’aide d’une bibliothèque personnalisée avec explosion sur HDInsight. La bibliothèque personnalisée, nous utilisons est une bibliothèque Python appelée **iislogparser.py**.

> [AZURE.TIP] Ce didacticiel est également disponible en tant que Jupyter bloc-notes sur un cluster explosion (Linux) que vous créez dans HDInsight. L’expérience de bloc-notes vous permet d’exécuter les extraits Python dans le bloc-notes. Pour effectuer le didacticiel à partir d’un bloc-notes, créez un cluster explosion, lancer un bloc-notes Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`), puis exécutez le bloc-notes **ouvre une analyse avec explosion à l’aide d’un library.ipynb personnalisé** sous le dossier **PySpark** .

**Conditions requises :**

Vous devez disposer des éléments suivants :

- Un abonnement Azure. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster explosion Apache sur HDInsight Linux. Pour plus d’informations, voir [groupes de créer Apache explosion dans Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="save-raw-data-as-an-rdd"></a>Enregistrer les données brutes comme un RDD

Dans cette section, nous utilisons le bloc-notes [Jupyter](https://jupyter.org) associé à un cluster Apache explosion dans HDInsight pour exécuter les tâches qui traitent vos données exemple brute et l’enregistrement comme une table Hive. Les exemples de données est un fichier .csv (hvac.csv) disponible sur tous les clusters par défaut.

Une fois que vos données sont enregistrées dans une table Hive, nous se connecte à la table Hive à l’aide des outils de décisionnel tels que Power BI et Tableau dans la section suivante.

1. À partir du [Portail Azure](https://portal.azure.com/), à partir de la startboard, cliquez sur la vignette pour votre cluster explosion (si vous l’épinglées à la startboard). Vous pouvez également accéder à votre cluster sous **Rechercher tout** > **Clusters HDInsight**.   

2. À partir de la carte de cluster explosion, cliquez sur **Tableau de bord Cluster**, puis cliquez sur **Bloc-notes Jupyter**. Si vous y êtes invité, entrez les informations d’identification d’administration pour le cluster.

    > [AZURE.NOTE] Le bloc-notes Jupyter peut également atteindre pour votre cluster en ouvrant l’URL suivante dans votre navigateur. Remplacez __NOMDUCLUSTER__ par le nom de votre cluster :
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Créer un bloc-notes. Cliquez sur **Nouveau**, puis cliquez sur **PySpark**.

    ![Créer un bloc-notes Jupyter] (./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdispark.note.jupyter.createnotebook.png "Créer un bloc-notes Jupyter")

3. Un bloc-notes est créé et ouvert avec le nom Untitled.pynb. Cliquez sur le nom du bloc-notes dans la partie supérieure, puis entrez un nom convivial.

    ![Fournir un nom pour le bloc-notes] (./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdispark.note.jupyter.notebook.name.png "Fournir un nom pour le bloc-notes")

4. Étant donné que vous avez créé un bloc-notes à l’aide du noyau PySpark, vous n’avez pas besoin créer les contextes explicitement. Les contextes explosion et Hive est automatiquement créées pour vous lors de l’exécution de la première cellule de code. Vous pouvez commencer en important les types requis pour ce scénario. Collez l’extrait de code suivante dans une cellule vide, puis appuyez sur **MAJ + ENTRÉE**.


        from pyspark.sql import Row
        from pyspark.sql.types import *


5. Créer un RDD avec les données journal déjà disponibles sur le cluster. Vous pouvez accéder aux données dans le compte de stockage par défaut associé au cluster en **\HdiSamples\HdiSamples\WebsiteLogSampleData\SampleLog\909f2b.log**.


        logs = sc.textFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')


6. Récupérer un exemple de journal définir pour vous assurer que l’étape précédente terminée avec succès.

        logs.take(5)

    Vous devriez voir un résultat semblable à ce qui suit :

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [u'#Software: Microsoft Internet Information Services 8.0',
         u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']

## <a name="analyze-log-data-using-a-custom-python-library"></a>Analyser les données du journal à l’aide d’une bibliothèque de Python personnalisée

7. Dans la sortie ci-dessus, les premières lignes rapidement et ajoutez les informations d’en-tête et chaque ligne restante correspond au schéma décrit dans cet en-tête. Ces journaux d’analyse peut être compliqué. Par conséquent, nous utilisons une bibliothèque Python personnalisée (**iislogparser.py**) qui facilite l’analyse ces journaux beaucoup plus faciles. Par défaut, cette bibliothèque est incluse avec votre cluster explosion sur HDInsight en **/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py**.

    Toutefois, cette bibliothèque n’est pas dans le `PYTHONPATH` afin que nous ne pouvons pas l’utiliser à l’aide d’une instruction import comme `import iislogparser`. Pour utiliser cette bibliothèque, nous devons distribuer à tous les nœuds de travail. Exécuter l’extrait de code suivante.


        sc.addPyFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')


9. `iislogparser`Fournit une fonction `parse_log_line` qui retourne `None` si une ligne de journal est une ligne d’en-tête et retourne une instance de le `LogLine` classe s’il rencontre une ligne de journal. Utiliser la `LogLine` cours pour extraire uniquement les lignes de journal à partir de la RDD :

        def parse_line(l):
            import iislogparser
            return iislogparser.parse_log_line(l)
        logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()


10. Récupérer quelques lignes journal extraits pour vous assurer que l’étape terminée avec succès.

        logLines.take(2)

    La sortie doit être semblable à ce qui suit :

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
        2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]


11. La `LogLine` cours, à son tour, a plusieurs méthodes utiles, comme `is_error()`, qui retourne une valeur si une entrée de journal possède un code d’erreur. Utilisez cette option pour calculer le nombre d’erreurs dans les lignes de journal extraites et puis consigner toutes les erreurs dans un autre fichier.

        errors = logLines.filter(lambda p: p.is_error())
        numLines = logLines.count()
        numErrors = errors.count()
        print 'There are', numErrors, 'errors and', numLines, 'log entries'
        errors.map(lambda p: str(p)).saveAsTextFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')

    Vous devriez voir un résultat comme suit :

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        There are 30 errors and 646 log entries

12. Vous pouvez également utiliser **Matplotlib** pour construire une visualisation des données. Par exemple, si vous voulez isoler la cause de requêtes qui s’exécutent pendant une longue période, vous souhaiterez peut-être rechercher les fichiers qui prennent le plus de temps pour traiter en moyenne.
L’extrait de code ci-dessous récupère les ressources de 25 supérieure ayant essentiel du temps pour répondre à une requête.

        def avgTimeTakenByKey(rdd):
            return rdd.combineByKey(lambda line: (line.time_taken, 1),
                                    lambda x, line: (x[0] + line.time_taken, x[1] + 1),
                                    lambda x, y: (x[0] + y[0], x[1] + y[1]))\
                      .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))
            
        avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])

    Vous devriez voir un résultat comme suit :

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [(u'/blogposts/mvc4/step13.png', 197.5),
         (u'/blogposts/mvc2/step10.jpg', 179.5),
         (u'/blogposts/extractusercontrol/step5.png', 170.0),
         (u'/blogposts/mvc4/step8.png', 159.0),
         (u'/blogposts/mvcrouting/step22.jpg', 155.0),
         (u'/blogposts/mvcrouting/step3.jpg', 152.0),
         (u'/blogposts/linqsproc1/step16.jpg', 138.75),
         (u'/blogposts/linqsproc1/step26.jpg', 137.33333333333334),
         (u'/blogposts/vs2008javascript/step10.jpg', 127.0),
         (u'/blogposts/nested/step2.jpg', 126.0),
         (u'/blogposts/adminpack/step1.png', 124.0),
         (u'/BlogPosts/datalistpaging/step2.png', 118.0),
         (u'/blogposts/mvc4/step35.png', 117.0),
         (u'/blogposts/mvcrouting/step2.jpg', 116.5),
         (u'/blogposts/aboutme/basketball.jpg', 109.0),
         (u'/blogposts/anonymoustypes/step11.jpg', 109.0),
         (u'/blogposts/mvc4/step12.png', 106.0),
         (u'/blogposts/linq8/step0.jpg', 105.5),
         (u'/blogposts/mvc2/step18.jpg', 104.0),
         (u'/blogposts/mvc2/step11.jpg', 104.0),
         (u'/blogposts/mvcrouting/step1.jpg', 104.0),
         (u'/blogposts/extractusercontrol/step1.png', 103.0),
         (u'/blogposts/sqlvideos/sqlvideos.jpg', 102.0),
         (u'/blogposts/mvcrouting/step21.jpg', 101.0),
         (u'/blogposts/mvc4/step1.png', 98.0)]


13. Vous pouvez également présenter ces informations sous forme de traçage. La première étape consiste à créer un tracé, laissez-nous d’abord créer une table temporaire **AverageTime**. Le tableau regroupe les journaux en temps pour voir si les pointes de latence inhabituels à un moment donné.

        avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
        schema = StructType([StructField('Minutes', IntegerType(), True),
                             StructField('Time', FloatType(), True)])
                             
        avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
        avgTimeTakenByMinuteDF.registerTempTable('AverageTime')

14. Vous pouvez ensuite exécuter la requête SQL suivante pour extraire tous les enregistrements dans la table **AverageTime** .

        %%sql -o averagetime
        SELECT * FROM AverageTime

    La `%%sql` magie suivie `-o averagetime` garantit que le résultat de la requête est conservé localement sur le serveur Jupyter (généralement la headnode du cluster). Le résultat est conservé comme un dataframe [Pandas](http://pandas.pydata.org/) avec nom spécifié **averagetime**.

    Vous devriez voir un résultat comme suit :

    ![Résultat de la requête SQL] (./media/hdinsight-apache-spark-custom-library-website-log-analysis/sql.output.png "Résultat de la requête SQL")

    Pour plus d’informations sur la `%%sql` magie, ainsi que d’autres magics disponibles avec le noyau PySpark, voir [noyaux disponibles sur les ordinateurs portables Jupyter avec clusters explosion HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).

15. Vous pouvez désormais utiliser Matplotlib, une bibliothèque permet de créer la visualisation des données pour créer un tracé. Étant donné que le tracé doit être créé à partir de la dataframe conservé localement **averagetime** , l’extrait de code doit commencer par le `%%local` magie. Cela garantit que le code est exécuté localement sur le serveur Jupyter.

        %%local
        %matplotlib inline
        import matplotlib.pyplot as plt
        
        plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
        plt.xlabel('Time (min)')
        plt.ylabel('Average time taken for request (ms)')

    Vous devriez voir un résultat comme suit :

    ![Sortie Matplotlib] (./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdi-apache-spark-web-log-analysis-plot.png "Sortie Matplotlib")

16. Une fois que vous avez terminé d’exécuter l’application, vous devez arrêter le bloc-notes pour libérer les ressources. Pour ce faire, dans le menu **fichier** sur le bloc-notes, cliquez sur **Fermer et arrêter**. Cette s’arrêter et fermer le bloc-notes.
    

## <a name="seealso"></a>Voir aussi


* [Vue d’ensemble : Apache explosion sur Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scénarios

* [Explosion avec BI : effectuer une analyse de données interactives à l’aide d’explosion dans HDInsight avec les outils de décisionnel](hdinsight-apache-spark-use-bi-tools.md)

* [Explosion avec apprentissage automatique : utilisation explosion dans HDInsight pour analyser la température de construction à l’aide de données HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Explosion avec apprentissage automatique : utilisation explosion dans HDInsight pour prévoir des résultats de l’inspection alimentaires](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Diffusion en continu explosion : Utilisation explosion dans HDInsight pour la création d’applications en continu en temps réel](hdinsight-apache-spark-eventhub-streaming.md)

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
