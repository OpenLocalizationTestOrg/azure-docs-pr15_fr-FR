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


# <a name="machine-learning-predictive-analysis-on-food-inspection-data-using-mllib-with-apache-spark-cluster-on-hdinsight-linux"></a>Machine d’apprentissage : analyse prédictive inspection allouées à l’aide de MLlib avec Apache explosion cluster sur HDInsight Linux

> [AZURE.TIP] Ce didacticiel est également disponible en tant que Jupyter bloc-notes sur un cluster explosion (Linux) que vous créez dans HDInsight. L’expérience de bloc-notes vous permet d’exécuter les extraits Python dans le bloc-notes. Pour effectuer le didacticiel à partir d’un bloc-notes, créez un cluster explosion, lancer un bloc-notes Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`), puis exécutez le bloc-notes **à l’aide d’explosion Machine apprentissage - prédictive analyse de données d’inspection alimentaires MLLib.ipynb** sous le dossier **Python** .


Cet article explique comment **MLLib**, intégrée d’apprentissage automatique d’explosion bibliothèques, permet d’effectuer une analyse prédictive simple dans un jeu de données ouverte. MLLib est une bibliothèque d’explosion core qui fournit un certain nombre d’utilitaires qui sont utiles pour les tâches d’apprentissage machine, y compris les utilitaires qui conviennent pour :

* Classification

* Régression

* Mise en cluster

* Modélisation de rubrique

* Décomposition de valeur au singulier (MVP) et l’analyse de composant principal (Assistant Compatibilité)

* Hypothèse test et calculer des statistiques d’exemple

Cet article présente une approche simple de *classification* de régression logistique.

## <a name="what-are-classification-and-logistic-regression"></a>À quoi correspondent classification et régression logistique ?

*Classification*, une machine très courante tâche, d’apprentissage est le processus de tri des données d’entrée en catégories. Il s’agit de la tâche d’un algorithme de classification de déterminer comment affecter des « étiquettes » d’entrée de données que vous spécifiez. Par exemple, vous pourriez utiliser un algorithme d’apprentissage machine qui accepte les informations sur le stocks en entrée et divise le papier en deux catégories : dont vous devez vendre et des stocks que vous devez conserver.

Régression logistique est l’algorithme que vous utilisez pour le classement. Régression logistique d’explosion API est utile pour *classification binaire*ou la classification des données d’entrée dans un des deux groupes. Pour plus d’informations sur régression logistique, voir [Wikipédia](https://en.wikipedia.org/wiki/Logistic_regression).

En résumé, le processus de régression logistique crée une *fonction logistique* pouvant être utilisées pour prévoir la probabilité qu’un vecteur d’entrée appartient dans un groupe ou l’autre.  

## <a name="what-are-we-trying-to-accomplish-in-this-article"></a>Ce que nous essayons d’accomplir dans cet article ?

Vous allez utiliser explosion pour effectuer une analyse prédictive sur données d’inspection alimentaires (**Food_Inspections1.csv**) qui a été achetées via le [portail de données Ville de Chicago](https://data.cityofchicago.org/). Ce groupe de données contient des informations sur les contrôles alimentaires qui ont été effectués à Chicago, y compris des informations sur chacun d'entre eux alimentation qui a été inspectée, les violations qui ont été trouvées (le cas échéant) ainsi que les résultats de l’inspection. Le fichier de données CSV est déjà disponible dans le compte de stockage associé au cluster en **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv**.

Les étapes suivantes, vous développez un modèle pour voir ce qu’il faut pour transmettre ou échouer une inspection alimentation. 

## <a name="start-building-a-machine-learning-application-using-spark-mllib"></a>Commencer à créer une application d’apprentissage machine à l’aide d’explosion MLlib

1. À partir du [Portail Azure](https://portal.azure.com/), à partir de la startboard, cliquez sur la vignette pour votre cluster explosion (si vous l’épinglées à la startboard). Vous pouvez également accéder à votre cluster sous **Rechercher tout** > **Clusters HDInsight**.   

2. À partir de la carte de cluster explosion, cliquez sur **Tableau de bord Cluster**, puis cliquez sur **Bloc-notes Jupyter**. Si vous y êtes invité, entrez les informations d’identification d’administration pour le cluster.

    > [AZURE.NOTE] Le bloc-notes Jupyter peut également atteindre pour votre cluster en ouvrant l’URL suivante dans votre navigateur. Remplacez __NOMDUCLUSTER__ par le nom de votre cluster :
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Créer un bloc-notes. Cliquez sur **Nouveau**, puis cliquez sur **PySpark**.

    ![Créer un bloc-notes Jupyter] (./media/hdinsight-apache-spark-machine-learning-mllib-ipython/hdispark.note.jupyter.createnotebook.png "Créer un bloc-notes Jupyter")

3. Un bloc-notes est créé et ouvert avec le nom Untitled.pynb. Cliquez sur le nom du bloc-notes dans la partie supérieure, puis entrez un nom convivial.

    ![Fournir un nom pour le bloc-notes] (./media/hdinsight-apache-spark-machine-learning-mllib-ipython/hdispark.note.jupyter.notebook.name.png "Fournir un nom pour le bloc-notes")

3. Étant donné que vous avez créé un bloc-notes à l’aide du noyau PySpark, vous n’avez pas besoin créer les contextes explicitement. Les contextes explosion et Hive est automatiquement créées pour vous lors de l’exécution de la première cellule de code. Vous pouvez commencer à créer votre apprentissage application en important les types requis pour ce scénario de l’ordinateur. Pour ce faire, placez le curseur dans la cellule et appuyez sur **MAJ + ENTRÉE**.


        from pyspark.ml import Pipeline
        from pyspark.ml.classification import LogisticRegression
        from pyspark.ml.feature import HashingTF, Tokenizer
        from pyspark.sql import Row
        from pyspark.sql.functions import UserDefinedFunction
        from pyspark.sql.types import *

## <a name="construct-an-input-dataframe"></a>Construire une dataframe d’entrée

Nous pouvons utiliser `sqlContext` pour effectuer des transformations sur des données structurées. La première tâche consiste à charger les exemples de données ((**Food_Inspections1.csv**)) dans une explosion SQL *dataframe*. 

1. Étant donné que les données brutes sont dans un format CSV, nous devons utiliser le contexte explosion pour extraire chaque ligne du fichier en mémoire sous forme de texte non structurée ; puis, vous utilisez une bibliothèque de Python CSV à analyser chaque ligne individuellement. 


        def csvParse(s):
            import csv
            from StringIO import StringIO
            sio = StringIO(s)
            value = csv.reader(sio).next()
            sio.close()
            return value
        
        inspections = sc.textFile('wasbs:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv')\
                        .map(csvParse)


2. Nous avons maintenant le fichier CSV comme un RDD. Laissez-nous extraient RDD à comprendre le schéma des données à une seule ligne.


        inspections.take(1)


    Vous devriez voir un résultat comme suit :

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [['413707',
          'LUNA PARK INC',
          'LUNA PARK  DAY CARE',
          '2049789',
          "Children's Services Facility",
          'Risk 1 (High)',
          '3250 W FOSTER AVE ',
          'CHICAGO',
          'IL',
          '60625',
          '09/21/2010',
          'License-Task Force',
          'Fail',
          '24. DISH WASHING FACILITIES: PROPERLY DESIGNED, CONSTRUCTED, MAINTAINED, INSTALLED, LOCATED AND OPERATED - Comments: All dishwashing machines must be of a type that complies with all requirements of the plumbing section of the Municipal Code of Chicago and Rules and Regulation of the Board of Health. OBSEVERD THE 3 COMPARTMENT SINK BACKING UP INTO THE 1ST AND 2ND COMPARTMENT WITH CLEAR WATER AND SLOWLY DRAINING OUT. INST NEED HAVE IT REPAIR. CITATION ISSUED, SERIOUS VIOLATION 7-38-030 H000062369-10 COURT DATE 10-28-10 TIME 1 P.M. ROOM 107 400 W. SURPERIOR. | 36. LIGHTING: REQUIRED MINIMUM FOOT-CANDLES OF LIGHT PROVIDED, FIXTURES SHIELDED - Comments: Shielding to protect against broken glass falling into food shall be provided for all artificial lighting sources in preparation, service, and display facilities. LIGHT SHIELD ARE MISSING UNDER HOOD OF  COOKING EQUIPMENT AND NEED TO REPLACE LIGHT UNDER UNIT. 4 LIGHTS ARE OUT IN THE REAR CHILDREN AREA,IN THE KINDERGARDEN CLASS ROOM. 2 LIGHT ARE OUT EAST REAR, LIGHT FRONT WEST ROOM. NEED TO REPLACE ALL LIGHT THAT ARE NOT WORKING. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned. MISSING CEILING TILES WITH STAINS IN WEST,EAST, IN FRONT AREA WEST, AND BY THE 15MOS AREA. NEED TO BE REPLACED. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair. SPLASH GUARDED ARE NEEDED BY THE EXPOSED HAND SINK IN THE KITCHEN AREA | 34. FLOORS: CONSTRUCTED PER CODE, CLEANED, GOOD REPAIR, COVING INSTALLED, DUST-LESS CLEANING METHODS USED - Comments: The floors shall be constructed per code, be smooth and easily cleaned, and be kept clean and in good repair. INST NEED TO ELEVATE ALL FOOD ITEMS 6INCH OFF THE FLOOR 6 INCH AWAY FORM WALL.  ',
          '41.97583445690982',
          '-87.7107455232781',
          '(41.97583445690982, -87.7107455232781)']]


3. La sortie ci-dessus nous donne une idée du schéma de fichier d’entrée ; le fichier inclut le nom de chaque établissement, le type de mise en place, l’adresse, les données de l’inspection et l’emplacement, entre autres choses. Commençons par sélectionner plusieurs colonnes qui sont utiles pour l’analyse de notre prédictive et regrouper les résultats sous la forme d’un dataframe, nous utilisons ensuite pour créer une table temporaire.


        schema = StructType([
        StructField("id", IntegerType(), False), 
        StructField("name", StringType(), False), 
        StructField("results", StringType(), False), 
        StructField("violations", StringType(), True)])

        df = sqlContext.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
        df.registerTempTable('CountResults')

4. Nous ont désormais un *dataframe*, `df` sur lesquels nous pouvons réaliser notre analyse. Nous avons également être appelé par une table temporaire **CountResults**. Nous avons inclus 4 colonnes d’intérêt dans la dataframe : **id**, **nom**, **résultats**et **les violations**. 
    
    Nous allons obtenir un petit exemple de données :

        df.show(5)

    Vous devriez voir un résultat comme suit :

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        +------+--------------------+-------+--------------------+
        |    id|                name|results|          violations|
        +------+--------------------+-------+--------------------+
        |413707|       LUNA PARK INC|   Fail|24. DISH WASHING ...|
        |391234|       CAFE SELMARIE|   Fail|2. FACILITIES TO ...|
        |413751|          MANCHU WOK|   Pass|33. FOOD AND NON-...|
        |413708|BENCHMARK HOSPITA...|   Pass|                    |
        |413722|           JJ BURGER|   Pass|                    |
        +------+--------------------+-------+--------------------+

## <a name="understand-the-data"></a>Comprendre les données

1. Nous allons commencer à faire une idée de ce que contient notre dataset. Par exemple, quelles sont les différentes valeurs dans la colonne de **résultats** ?


        df.select('results').distinct().show()

    
    Vous devriez voir un résultat comme suit :

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------
    
        +--------------------+
        |             results|
        +--------------------+
        |                Fail|
        |Business Not Located|
        |                Pass|
        |  Pass w/ Conditions|
        |     Out of Business|
        +--------------------+
    
2. Une visualisation rapide pour nous aider motif sur la distribution de ces résultats. Nous avons déjà les données dans une table temporaire **CountResults**. Vous pouvez exécuter la requête SQL suivante sur le tableau pour mieux comprendre comment les résultats sont distribuée.

        %%sql -o countResultsdf
        SELECT results, COUNT(results) AS cnt FROM CountResults GROUP BY results

    La `%%sql` magie suivie `-o countResultsdf` garantit que le résultat de la requête est conservé localement sur le serveur Jupyter (généralement la headnode du cluster). Le résultat est conservé comme un dataframe [Pandas](http://pandas.pydata.org/) avec le nom spécifié **countResultsdf**.
    
    Vous devriez voir un résultat comme suit :
    
    ![Résultat de la requête SQL] (./media/hdinsight-apache-spark-machine-learning-mllib-ipython/query.output.png "Résultat de la requête SQL")

    Pour plus d’informations sur la `%%sql` magie, ainsi que d’autres magics disponibles avec le noyau PySpark, voir [noyaux disponibles sur les ordinateurs portables Jupyter avec clusters explosion HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).

3. Vous pouvez également utiliser Matplotlib, une bibliothèque permet de créer la visualisation des données pour créer un tracé. Étant donné que le tracé doit être créé à partir de la dataframe conservé localement **countResultsdf** , l’extrait de code doit commencer par le `%%local` magie. Cela garantit que le code est exécuté localement sur le serveur Jupyter.

        %%local
        %matplotlib inline
        import matplotlib.pyplot as plt
        
        
        labels = countResultsdf['results']
        sizes = countResultsdf['cnt']
        colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
        plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
        plt.axis('equal')

    Vous devriez voir un résultat comme suit :

    ![Présentation des résultats](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/output_13_1.png)


4. Vous constatez qu’il existe 5 résultats distincts qu’une inspection peut avoir :
    
    * Entreprise que ne se trouve ne pas 
    * Fail
    * Passer
    * Services de support technique avec les conditions, et
    * Se déconnecter de l’entreprise 

    Laissez-nous développer un modèle qui peut deviner le résultat d’une inspection alimentaires, étant donné les violations. Dans la mesure où régression logistique est une méthode classification binaire, il est préférable de regrouper nos données en deux catégories : **échouer** et **passez**. Un « passer avec Conditions » est toujours un passage, afin que lorsque nous former le modèle, nous allons étudier les deux résultats équivalent. Données avec les autres résultats (« Entreprise ne se trouve pas », « d’entreprise ») ne sont pas utiles afin que nous supprime les notre ensemble d’apprentissage. Il s’agit OK dans la mesure où ces deux catégories constituent un faible pourcentage des résultats quand même.

5. Laissez-nous alors et convertir notre dataframe existant (`df`) dans une nouvelle dataframe où chaque inspection est représentée par une paire violations de l’étiquette. Dans notre exemple, une étiquette de `0.0` représente un échec, une étiquette de `1.0` représente un succès et une étiquette de `-1.0` représente certains résultats outre ces deux. Nous filtre ces autres résultats lors du calcul le nouveau bloc de données.


        def labelForResults(s):
            if s == 'Fail':
                return 0.0
            elif s == 'Pass w/ Conditions' or s == 'Pass':
                return 1.0
            else:
                return -1.0
        label = UserDefinedFunction(labelForResults, DoubleType())
        labeledData = df.select(label(df.results).alias('label'), df.violations).where('label >= 0')


    Nous allons extraire les données libellées pour voir à quoi il ressemble à une seule ligne.


        labeledData.take(1)


    Vous devriez voir un résultat comme suit :
    
        # -----------------
        # THIS IS AN OUTPUT
        # -----------------
    
        [Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]


## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>Créer un modèle de régression logistique à partir de la dataframe d’entrée

Notre dernière tâche consiste à convertir les données libellées dans un format qui peut être analysé par régression logistique. L’entrée à un algorithme de régression logistique doit être un jeu de *paires de vecteur fonctionnalité d’étiquette*, à l’endroit où le « vecteur fonctionnalité » est un vecteur de nombres qui représente le point d’entrée d’une certaine manière. Par conséquent, nous avons besoin d’un moyen pour convertir la colonne « violations », qui est semi-structurées et contient un grand nombre de commentaires dans le texte libre, dans un tableau de nombres réels comprend facilement un ordinateur. 

Un ordinateur standard approche pour le traitement en langage naturel d’apprentissage consiste à affecter chaque mot distinct un « index », puis passez un vecteur à l’ordinateur d’apprentissage algorithme de telle sorte que la valeur de chaque index contient la fréquence relative de ce mot dans la chaîne de texte. 

MLLib est un moyen simple pour effectuer cette opération. Tout d’abord, nous allons « réinitialiser » chaque chaîne violations pour obtenir les mots individuels dans chaque chaîne et puis nous allons utiliser un `HashingTF` pour convertir chaque jeu de jetons vecteur fonctionnalité qui peut ensuite être passé à l’algorithme de régression logistique pour créer un modèle. Nous allons réaliser toutes ces étapes à l’aide d’un « pipeline ».


    tokenizer = Tokenizer(inputCol="violations", outputCol="words")
    hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
    lr = LogisticRegression(maxIter=10, regParam=0.01)
    pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
    
    model = pipeline.fit(labeledData)


## <a name="evaluate-the-model-on-a-separate-test-dataset"></a>Évaluer le modèle sur un jeu de données de test distinct

Nous pouvons utiliser le modèle que nous avons créé précédemment pour *prévoir* le résultat de nouveaux contrôles seront, selon les violations qui ont été observées. Nous formé ce modèle pour le jeu de données **Food_Inspections1.csv**. Laissez-nous utiliser un second jeu de données, **Food_Inspections2.csv**, pour *évaluer* la puissance de ce modèle sur de nouvelles données. Ce second jeu de données (**Food_Inspections2.csv**) doit déjà être dans le conteneur de stockage par défaut associé au cluster.

1. L’extrait de code ci-dessous crée un nouveau dataframe, **predictionsDf** qui contient la prévision générée par le modèle. L’extrait de code crée également une table temporaire **que des prédictions** basée sur la dataframe.


        testData = sc.textFile('wasbs:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
                 .map(csvParse) \
                 .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
        testDf = sqlContext.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
        predictionsDf = model.transform(testDf)
        predictionsDf.registerTempTable('Predictions')
        predictionsDf.columns


    Vous devriez voir un résultat comme suit :
    
        # -----------------
        # THIS IS AN OUTPUT
        # -----------------
        
        ['id',
         'name',
         'results',
         'violations',
         'words',
         'features',
         'rawPrediction',
         'probability',
         'prediction']

2. Observez un des prévisions. Exécuter cet extrait de code :

        predictionsDf.take(1)

    Vous verrez la prévision à la première entrée dans le jeu de données de test.

3. La `model.transform()` méthode appliquer la même transformation à toutes les nouvelles données possédant le même schéma et soumis à une prévision de la façon de classer les données. Nous pouvons faire certaines statistiques simple pour avoir une idée de l’exactitude nos prévisions étaient :


        numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR 
                                              (prediction = 1 AND (results = 'Pass' OR 
                                                                   results = 'Pass w/ Conditions'))""").count()
        numInspections = predictionsDf.count()
        
        print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
        print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"

    Le résultat se présente comme suit :
    
        # -----------------
        # THIS IS AN OUTPUT
        # -----------------
    
        There were 9315 inspections and there were 8087 successful predictions
        This is a 86.8169618894% success rate


    À l’aide de la régression logistique avec explosion permet de créer un modèle avec une précision de la relation entre une entreprise donnée serait passer ou échouer une inspection alimentaires de descriptions violations en anglais. 

## <a name="create-a-visual-representation-of-the-prediction"></a>Créer une représentation visuelle de la prévision

Nous pouvons maintenant construire une visualisation finale pour nous aider à motif sur les résultats de ce test. 

1. Nous allons commencer par extraire les prévisions différentes et les résultats de la table temporaire **des prédictions** créée précédemment. Les requêtes suivantes séparent le résultat en tant que *true_positive*, *false_positive*, *true_negative*et *false_negative*. Dans les requêtes ci-dessous, nous désactiver visualisation à l’aide de `-q` et également enregistrer la sortie (à l’aide de `-o`) en tant que dataframes peut ensuite être utilisé avec la `%%local` magie. 

        %%sql -q -o true_positive
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND results = 'Fail'

        %%sql -q -o false_positive
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND (results = 'Pass' OR results = 'Pass w/ Conditions')

        %%sql -q -o true_negative
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND results = 'Fail'

        %%sql -q -o false_negative
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND (results = 'Pass' OR results = 'Pass w/ Conditions') 

2. Pour finir, utilisez l’extrait suivant pour générer le tracé à l’aide de **Matplotlib**.

        %%local
        %matplotlib inline
        import matplotlib.pyplot as plt
        
        labels = ['True positive', 'False positive', 'True negative', 'False negative']
        sizes = [true_positive['cnt'], false_positive['cnt'], false_negative['cnt'], true_negative['cnt']]
        colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
        plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
        plt.axis('equal')
    
    Vous devriez voir le résultat suivant.
    
    ![Sortie de prévision](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/output_26_1.png)


    Dans ce graphique, un résultat « positif » fait référence à l’inspection alimentaires échec, tandis qu’un résultat négatif fait référence à une inspection passée.

## <a name="shut-down-the-notebook"></a>Arrêter le bloc-notes

Une fois que vous avez terminé d’exécuter l’application, vous devez arrêter le bloc-notes pour libérer les ressources. Pour ce faire, dans le menu **fichier** sur le bloc-notes, cliquez sur **Fermer et arrêter**. Cette s’arrêter et fermer le bloc-notes.


## <a name="seealso"></a>Voir aussi


* [Vue d’ensemble : Apache explosion sur Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scénarios

* [Explosion avec BI : effectuer une analyse de données interactives à l’aide d’explosion dans HDInsight avec les outils de décisionnel](hdinsight-apache-spark-use-bi-tools.md)

* [Explosion avec apprentissage automatique : utilisation explosion dans HDInsight pour analyser la température de construction à l’aide de données HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

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
