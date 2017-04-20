<properties
    pageTitle="Exploration de données et de modélisation avec explosion | Microsoft Azure"
    description="Présente les fonctionnalités d’exploration et modélisation de données du Kit de ressources MLlib explosion."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/07/2016"
    ms.author="deguhath;bradsev;gokuma" />

# <a name="data-exploration-and-modeling-with-spark"></a>Exploration de données et de modélisation avec explosion

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

Cette procédure pas à pas utilise HDInsight Spark afin d’effectuer des données et régression modélisation des tâches sur un échantillon de la NYC classification binaire taxi voyage et dataset 2013 du prix.  Il vous guide dans les étapes du [Processus de scientifique des données](http://aka.ms/datascienceprocess), de bout en bout, à l’aide de cluster une HDInsight Spark pour le traitement et Azure BLOB pour stocker les données et les modèles. Le processus examine et visualise des données importées à partir d’un Blob de stockage Azure et puis prépare les données pour créer des modèles prédictives. Ces modèles sont build à l’aide de la boîte à outils MLlib explosion d’effectuer des tâches de modélisation de régression et la classification binaire.

- La tâche de **classification binaire** est de prédire ou non une info-bulle est payée pour le voyage. 
- La tâche de **régression** consiste à prévoir la quantité de l’info-bulle basée sur d’autres fonctionnalités de Conseil. 

Les modèles que nous utilisons : régression linéaire et logistique, aléatoires forêts et les arborescences augmentées dégradés

- [Régression linéaire avec SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) est un modèle de régression linéaire qui utilise une méthode descente dégradé stochastique (SGD) et d’optimisation et de la fonctionnalité de mise à l’échelle afin de prévoir les montants Conseil payé. 
- Régression [régression logistique avec LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) ou « logit », est un modèle de régression qui peut être utilisé lorsque la variable dépendante est par catégorie pour effectuer la classification des données. LBFGS est un algorithme d’optimisation Newton quasi-coupon qui est proche de l’algorithme de Broyden – Fletcher – Goldfarb – Shanno (BFGS) en utilisant une quantité de mémoire vive limitée et qui est utilisée dans l’apprentissage automatique.
- [Forêt aléatoire](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) est ensembles arbres de décision.  Ils combinent plusieurs arborescences de décision pour réduire les risques en matière de dépassement de. Forêts aléatoires servent à droite de régression et classification et peuvent gérer les fonctions par catégorie et peuvent être prolongées sur le paramètre de classification multiclass. Ils ne nécessitent pas de mise à l’échelle de fonctionnalité et sont en mesure de capturer non linéarité et ainsi que des interactions. Forêts aléatoires sont un de l’ordinateur plus réussie d’apprentissage pour la classification et de régression.
- [Dégradé augmentée arborescences](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) sont ensembles arbres de décision. GBTs former arbres de décision itératif pour réduire une fonction perte. GBTs servent à droite de régression et classification peuvent gérer des fonctionnalités par catégorie, ne nécessitent pas de mise à l’échelle de fonctionnalité et sont en mesure de capturer non linéarité et ainsi que des interactions. Ils permettent également un paramètre multiclass classification.

Les étapes de modélisation également contient du code montrant comment former, évaluer et enregistrer chaque type de modèle. Python a été utilisé pour la solution de code et pour afficher les tracés pertinents.   


>[AZURE.NOTE] Bien que la boîte à outils MLlib explosion est conçu pour fonctionner de grands volumes de données, un échantillon relativement petit (~ 30 Mo à l’aide de lignes 170K, environ 0,1 % du dataset NYC d’origine) est utilisé ici pour faciliter la tâche. L’exercice indiqué ici s’exécute efficacement (en environ 10 minutes) sur un cluster HDInsight avec 2 nœuds de travail. Le même code, avec des modifications mineures, peut être utilisé pour traiter les jeux de données volumineux avec les modifications nécessaires pour la mise en cache de données en mémoire et en modifiant la taille de cluster.

## <a name="prerequisites"></a>Conditions préalables

Vous avez besoin d’un compte Azure et un cluster nécessité un 1,6 HDInsight 3.4 explosion explosion vous HDInsight pour effectuer cette procédure. Consultez la [Vue d’ensemble des données sciences à l’aide d’explosion sur Azure HDInsight](machine-learning-data-science-spark-overview.md) pour obtenir des instructions sur la façon conforme à ces exigences. Cette rubrique contient également une description des données NYC 2013 Taxi utilisées ici et des instructions sur la façon d’exécuter du code d’un bloc-notes Jupyter sur le cluster explosion. Le bloc-notes **pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb** qui contient les exemples de code dans cette rubrique est disponible dans [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark). 


[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Configuration : le contexte explosion prédéfini, des bibliothèques et des emplacements de stockage

Explosion est en mesure de lire et écrire dans Azure stockage Blob (également appelé WASB). Afin que vos données existantes stockées il peuvent être traitées à l’aide d’explosion ainsi que les résultats stockés à nouveau dans WASB.

Pour enregistrer les modèles ou les fichiers WASB, le chemin d’accès doit être indiquée correctement. Le conteneur par défaut lié au cluster explosion peut être référencé à l’aide d’un chemin commençant par : « wasb : / / / ». D’autres emplacements sont référencées par « wasb : / / ».


### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Définir les chemins d’accès pour les emplacements de stockage dans WASB

L’exemple de code suivant indique l’emplacement des données à lire et le chemin d’accès du répertoire de stockage de modèle dans lequel la sortie de modèle est enregistrée :


    # SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

    # LOCATION OF TRAINING DATA
    taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 


### <a name="import-libraries"></a>Bibliothèques d’importation

Configurer requiert également l’importation de bibliothèques nécessaires. Définir le contexte d’explosion et importez les bibliothèques nécessaires par le code suivant :


    # IMPORT LIBRARIES
    import pyspark
    from pyspark import SparkConf
    from pyspark import SparkContext
    from pyspark.sql import SQLContext
    import matplotlib
    import matplotlib.pyplot as plt
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    import atexit
    from numpy import array
    import numpy as np
    import datetime


### <a name="preset-spark-context-and-pyspark-magics"></a>Contexte explosion prédéfini et PySpark magics

Les noyaux PySpark qui sont fournis avec des blocs-notes Jupyter possèdent un contexte prédéfini. Si vous n’avez pas besoin de définir l’explosion ou contextes Hive explicitement avant de commencer à utiliser l’application que vous développez. Les contextes sont disponibles pour vous par défaut. Les contextes sont :

- SC - d’explosion 
- sqlContext - pour Hive

Le noyau PySpark fournit certaines prédéfinis « magics », qui sont des commandes spéciales que vous pouvez appeler avec %%. Il existe deux ces commandes qui sont utilisées dans ces exemples de code.

- **%% local** Spécifie que le code dans les lignes suivantes doit être exécutées localement. Code doit être code Python valide.
- **%%sql -o <variable name>** Exécute une requête Hive par rapport à la sqlContext. Si le paramètre-o est passé, le résultat de la requête est conservé dans le %% contexte Python local comme un DataFrame Pandas.
 

Pour plus d’informations sur les noyaux pour prédéfinis et blocs-notes Jupyter « magics » qui ils fournissent, voir [noyaux disponibles pour les blocs-notes Jupyter avec des clusters HDInsight explosion Linux sur HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-notebook-kernels.md).
 

## <a name="data-ingestion-from-public-blob"></a>Réception de données à partir d’objets blob public

La première étape du processus de données scientifique consiste aux données à analyser à partir de sources d’acquisition où est réside dans votre environnement d’exploration et modélisation de données. L’environnement est explosion dans cette procédure. Cette section contient le code pour effectuer une série de tâches :

- acquisition de l’échantillon de données à modéliser
- lu dans le groupe de données d’entrée (stocké sous forme de fichier .tsv)
- mettre en forme et effacer les données
- créer et mettre en cache des objets (RDDs ou trames de données) dans la mémoire
- Enregistrer en tant que d’une table temporaire dans SQL contexte.

Voici le code de réception de données.

    # INGEST DATA

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT FILE FROM PUBLIC BLOB
    taxi_train_file = sc.textFile(taxi_train_file_loc)
    
    # GET SCHEMA OF THE FILE FROM HEADER
    schema_string = taxi_train_file.first()
    fields = [StructField(field_name, StringType(), True) for field_name in schema_string.split('\t')]
    fields[7].dataType = IntegerType() #Pickup hour
    fields[8].dataType = IntegerType() # Pickup week
    fields[9].dataType = IntegerType() # Weekday
    fields[10].dataType = IntegerType() # Passenger count
    fields[11].dataType = FloatType() # Trip time in secs
    fields[12].dataType = FloatType() # Trip distance
    fields[19].dataType = FloatType() # Fare amount
    fields[20].dataType = FloatType() # Surcharge
    fields[21].dataType = FloatType() # Mta_tax
    fields[22].dataType = FloatType() # Tip amount
    fields[23].dataType = FloatType() # Tolls amount
    fields[24].dataType = FloatType() # Total amount
    fields[25].dataType = IntegerType() # Tipped or not
    fields[26].dataType = IntegerType() # Tip class
    taxi_schema = StructType(fields)
    
    # PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
    taxi_header = taxi_train_file.filter(lambda l: "medallion" in l)
    taxi_temp = taxi_train_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
            .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                            float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                            float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))
    
    
    # CREATE DATA FRAME
    taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)
    
    # CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
    taxi_df_train_cleaned = taxi_train_df.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
        .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
        .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
        .drop('direct_distance').drop('surcharge')\
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )

    
    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()
    
    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")
    
    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**RÉSULTAT :**

Temps nécessaire pour exécuter au-dessus de cellule : 51.72 secondes


## <a name="data-exploration--visualization"></a>Visualisation et exploration de données

Une fois les données a été mises en explosion, l’étape suivante du processus de données scientifique consiste à comprendre plus approfondie des données par le biais de détails et de visualisation. Dans cette section, nous examiner les données taxi à l’aide de requêtes SQL et tracer les variables cibles et les fonctionnalités potentiels d’inspection visuelle. Plus précisément, nous tracer la fréquence des voyageurs décomptes dans voyages taxi, la fréquence des montants de Conseil et comment conseils peuvent varier selon le type et le montant du paiement.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Tracer un histogramme de fréquence de Nb voyageurs dans l’échantillon de déplacements taxi

Ce code et les autres extraits utilisent magie SQL pour l’échantillon et magie local pour tracer les données de la requête.

- **Magie SQL (`%%sql`)** Le noyau HDInsight PySpark prend en charge inline facile HiveQL requêtes contre la sqlContext. La (-o nom_de_variable) argument persiste le résultat de la requête SQL comme un DataFrame Pandas sur le serveur Jupyter. Cela signifie qu’il est disponible en mode local.
- La ** `%%local` magie** est utilisé pour exécuter du code localement sur le serveur Jupyter, ce qui correspond à la headnode du cluster HDInsight. En règle générale, vous utilisez `%%local` magique conjointement avec la `%%sql` magique avec le paramètre -o. Le paramètre-o serait conserver le résultat de la requête SQL localement, puis %% magie local susceptibles de déclencher l’ensemble d’extrait de code pour exécuter localement sur la sortie des requêtes SQL qui est conservée localement suivant

Le résultat est relativement automatiquement une fois que vous exécutez le code.

Cette requête extrait les déplacements en fonction du nombre voyageurs. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # HIVEQL QUERY AGAINST THE sqlContext
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts 
    FROM taxi_train 
    WHERE passenger_count > 0 and passenger_count < 7 
    GROUP BY passenger_count 

Ce code crée une trame de données locale à partir de la sortie de requête et trace les données. La `%%local` magie crée un graphique de données locale, `sqlResults`, qui peut être utilisée pour le tracé avec matplotlib. 

>[AZURE.NOTE] Cette magie PySpark est utilisé plusieurs fois dans cette procédure. Si la quantité de données est volumineuse, vous devez exemples pour créer une trame de données pouvant être affiché dans la mémoire locale.

    #CREATE LOCAL DATA-FRAME AND USE FOR MATPLOTLIB PLOTTING

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local
    
    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Voici le code pour tracer les déplacements en nombre voyageurs

    # PLOT PASSENGER NUMBER VS. TRIP COUNTS
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline
    
    x_labels = sqlResults['passenger_count'].values
    fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
    fig.set_xticklabels(x_labels)
    fig.set_title('Counts of trips by passenger count')
    fig.set_xlabel('Passenger count in trips')
    fig.set_ylabel('Trip counts')
    plt.show()

**RÉSULTAT :**

![Fréquence de voyage en fonction du nombre voyageurs](./media/machine-learning-data-science-spark-data-exploration-modeling/trip-freqency-by-passenger-count.png)

Vous pouvez sélectionner parmi les différents types de visualisations (Table, en secteurs, courbes, zone ou barre) en utilisant les boutons de menu **Type** dans le bloc-notes. Le tracé barre est indiqué ici.
    
### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Tracer un histogramme des montants de Conseil et comment Conseil varie selon les valeurs NB et tarif voyageurs.

Utiliser une requête SQL aux exemples de données.

    #PLOT HISTOGRAM OF TIP AMOUNTS AND VARIATION BY PASSENGER COUNT AND PAYMENT TYPE
    
    # HIVEQL QUERY AGAINST THE sqlContext
    %%sql -q -o sqlResults
    SELECT fare_amount, passenger_count, tip_amount, tipped 
    FROM taxi_train 
    WHERE passenger_count > 0 
    AND passenger_count < 7 
    AND fare_amount > 0 
    AND fare_amount < 200 
    AND payment_type in ('CSH', 'CRD') 
    AND tip_amount > 0 
    AND tip_amount < 25


Cette cellule code utilise la requête SQL pour créer trois tracés les données.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local
    
    # HISTOGRAM OF TIP AMOUNTS AND PASSENGER COUNT
    ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()
    
    # TIP BY PASSENGER COUNT
    ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()
    
    # TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
    ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
    ax.set_title('Tip amount by Fare amount')
    ax.set_xlabel('Fare Amount ($)')
    ax.set_ylabel('Tip Amount ($)')
    plt.axis([-2, 100, -2, 20])
    plt.show()


**RÉSULTAT :** 

![Conseil répartition montant](./media/machine-learning-data-science-spark-data-exploration-modeling/tip-amount-distribution.png)

![Conseil montant en fonction du nombre voyageurs](./media/machine-learning-data-science-spark-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Conseil montant par montant tarif](./media/machine-learning-data-science-spark-data-exploration-modeling/tip-amount-by-fare-amount.png)


## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Préparation d’ingénierie, de transformation et de données pour une modélisation des fonctionnalités
Cette section décrit et fournit le code pour les procédures utilisées pour préparer des données à utiliser dans la modélisation ML. Il montre comment effectuer les tâches suivantes :

- Créer une nouvelle fonctionnalité de mise au rebus heures dans les plages de temps le trafic
- Indexer et coder les fonctionnalités par catégorie
- Créer des objets point libellée pour l’entrée dans les fonctions ML
- Créer un sondage sous-adresse des données et la fractionner en formation et de test jeux
- Mise à l’échelle de fonctionnalité
- Objets cache en mémoire


### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Créer une nouvelle fonctionnalité de mise au rebus heures dans les plages de temps le trafic

Ce code montre comment créer une nouvelle fonctionnalité de mise au rebus heures dans les plages de temps le trafic, puis comment mettre en cache la trame de données qui en résulte en mémoire. L’emplacement dans lequel résistant distribué jeux de données (RDDs) et de trames de données sont utilisés à plusieurs reprises, la mise en cache permet d’accéder à amélioré temps d’exécution. En conséquence, nous cache RDDs et trames de données en plusieurs étapes de la procédure. 

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_train 
    """
    taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)
    
    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    # THE .COUNT() GOES THROUGH THE ENTIRE DATA-FRAME,
    # MATERIALIZES IT IN MEMORY, AND GIVES THE COUNT OF ROWS.
    taxi_df_train_with_newFeatures.cache()
    taxi_df_train_with_newFeatures.count()

**RÉSULTAT :** 

126050

### <a name="index-and-encode-categorical-features-for-input-into-modeling-functions"></a>Indexer et encoder des fonctionnalités par catégorie pour l’entrée en fonctions de modélisation

Cette section montre comment indexer ou coder des fonctionnalités par catégorie pour les entrées dans les fonctions de modélisation. La modélisation et prévoir les fonctions de MLlib nécessitent des fonctionnalités avec des données d’entrée par catégorie être indexées ou codé avant d’être utilisés. En fonction du modèle, vous devez indexer ou les coder de différentes manières :  

- **Modélisation basés sur arborescence** nécessite des catégories pour être codée sous forme de valeurs numériques (par exemple, une fonctionnalité avec trois catégories peut être codée avec 0 ; 1 ; 2). Ceci est fourni en fonction de [StringIndexer](http://spark.apache.org/docs/latest/ml-features.html#stringindexer) de MLlib. Cette fonction code une colonne de type chaîne d’étiquettes à une colonne d’index d’étiquette qui sont classés par fréquence d’étiquette. Bien qu’indexée avec des valeurs numériques pour l’entrée et la gestion des données, les algorithmes basés sur arborescence peuvent être spécifiés pour les traiter correctement comme catégories. 

- **Modèles de logistique et de régression linéaire** nécessitent hot un codage, où, par exemple, une fonctionnalité avec trois catégories peut être développée dans trois colonnes fonctionnalité, avec chaque contenant 0 ou 1 selon la catégorie d’une observation. MLlib fournit la fonction [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) pour faire hot un codage. Cet encodeur mappe une colonne d’index de l’étiquette à une colonne de vecteurs binaires, avec au moins une celui-valeur unique. Ce codage permet aux algorithmes qu’attendez des fonctionnalités à plusieurs valeurs numériques, telles que de régression logistique, à appliquer aux fonctionnalités par catégorie.

Voici le code d’indexation et de coder des fonctionnalités par catégorie :


    # INDEX AND ENCODE CATEGORICAL FEATURES

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES    
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer
    
    # INDEX AND ENCODE VENDOR_ID
    stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
    model = stringIndexer.fit(taxi_df_train_with_newFeatures) # Input data-frame is the cleaned one from above
    indexed = model.transform(taxi_df_train_with_newFeatures)
    encoder = OneHotEncoder(dropLast=False, inputCol="vendorIndex", outputCol="vendorVec")
    encoded1 = encoder.transform(indexed)
    
    # INDEX AND ENCODE RATE_CODE
    stringIndexer = StringIndexer(inputCol="rate_code", outputCol="rateIndex")
    model = stringIndexer.fit(encoded1)
    indexed = model.transform(encoded1)
    encoder = OneHotEncoder(dropLast=False, inputCol="rateIndex", outputCol="rateVec")
    encoded2 = encoder.transform(indexed)
    
    # INDEX AND ENCODE PAYMENT_TYPE
    stringIndexer = StringIndexer(inputCol="payment_type", outputCol="paymentIndex")
    model = stringIndexer.fit(encoded2)
    indexed = model.transform(encoded2)
    encoder = OneHotEncoder(dropLast=False, inputCol="paymentIndex", outputCol="paymentVec")
    encoded3 = encoder.transform(indexed)
    
    # INDEX AND TRAFFIC TIME BINS
    stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
    model = stringIndexer.fit(encoded3)
    indexed = model.transform(encoded3)
    encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
    encodedFinal = encoder.transform(indexed)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**RÉSULTAT :**

Temps nécessaire pour exécuter au-dessus de cellule : 1,28 secondes

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Créer des objets point libellée pour l’entrée dans les fonctions ML

Cette section contient de code qui montre comment indexer des données de texte par catégorie comme type de données point libellée et comment l’encoder afin qu’il puisse être utilisé pour former et tester régression logistique MLlib et autres modèles de classement. Objets point libellée sont résistant distribué jeux de données (RDD) mis en forme d’une manière qui est nécessaire en tant que données d’entrée par la plupart des algorithmes ML dans MLlib. Un [point avec légende](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) est un vecteur local, dense ou incomplet, associé à une étiquette/réponse.  

Cette section contient de code qui montre comment indexer des données de texte par catégorie comme type de données [intitulée point](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) et comment l’encoder afin qu’il puisse être utilisé pour former et tester régression logistique MLlib et autres modèles de classement. Objets point libellée sont résistant distribué jeux de données (RDD) composée d’une étiquette (variable cible/réponse) et un vecteur de fonctionnalité. Ce format est nécessaire comme entrée par les nombreux algorithmes ML dans MLlib.

Voici le code d’indexation et coder les fonctionnalités de texte pour la classification binaire.

    # FUNCTIONS FOR BINARY CLASSIFICATION

    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        labPt = LabeledPoint(line.tipped, features)
        return  labPt
    
    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        labPt = LabeledPoint(line.tipped, features)
        return  labPt


Voici le code pour coder et indexer les fonctionnalités de texte par catégorie pour une analyse de régression linéaire.

    # FUNCTIONS FOR REGRESSION WITH TIP AMOUNT AS TARGET VARIABLE

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingRegression(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])

        labPt = LabeledPoint(line.tip_amount, features)
        return  labPt
    
    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
    def parseRowOneHotRegression(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        labPt = LabeledPoint(line.tip_amount, features)
        return  labPt


### <a name="create-a-random-sub-sampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>Créer un sondage sous-adresse des données et la fractionner en formation et de test jeux

Ce code crée un sondage des données (25 % est utilisé ici). Bien qu’il n’est pas obligatoire dans cet exemple en raison de la taille du jeu de données, nous vous montrer comment vous pouvez exemple ici afin de déterminer comment l’utiliser pour votre propre problème lorsque cela est nécessaire. Lorsque des échantillons sont volumineux, cela peut gagner du temps significative et modèles de formation. Nous fractionner ensuite l’échantillon dans un composant de formation (75 % ici) et un composant de test (25 % ici) à utiliser dans la classification et la modélisation de régression.


    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.sql.functions import rand

    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    samplingFraction = 0.25;
    trainingFraction = 0.75; testingFraction = (1-trainingFraction);
    seed = 1234;
    encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)
    
    # SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST
    # INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS (FOR USE LATER IN AN ADVANCED TOPIC)
    dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
    trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);
    
    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary = trainData.map(parseRowIndexingBinary)
    indexedTESTbinary = testData.map(parseRowIndexingBinary)
    oneHotTRAINbinary = trainData.map(parseRowOneHotBinary)
    oneHotTESTbinary = testData.map(parseRowOneHotBinary)
    
    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg = trainData.map(parseRowIndexingRegression)
    indexedTESTreg = testData.map(parseRowIndexingRegression)
    oneHotTRAINreg = trainData.map(parseRowOneHotRegression)
    oneHotTESTreg = testData.map(parseRowOneHotRegression)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**RÉSULTAT :**

Temps nécessaire pour exécuter au-dessus de cellule : pas de 0,24 secondes


### <a name="feature-scaling"></a>Mise à l’échelle de fonctionnalité

Fonctionnalité mise à l’échelle, également appelée normalisation des données, permet de garantir que fonctionnalités avec des valeurs largement exécution sont pas autorisé à excessive évaluer vos dans la fonction de l’objectif. Le code pour suivre l’évolution de la fonction utilise la [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) à l’échelle les fonctionnalités pour écart unitaire. Il est fourni par MLlib pour une utilisation de régression linéaire avec stochastique dégradé descente (SGD), un algorithme populaire pour former un large éventail d’autres apprentissage modèles de régression REGULARISES ou machines vecteur prise en charge (SVM) de l’ordinateur.

>[AZURE.NOTE] Nous avons trouvé l’algorithme LinearRegressionWithSGD être sensible à la fonctionnalité de mise à l’échelle.

Voici le code aux variables d’échelle pour une utilisation avec l’algorithme SGD linéaire REGULARISES.

    # FEATURE SCALING

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from pyspark.mllib.linalg import Vectors
    from pyspark.mllib.feature import StandardScaler, StandardScalerModel
    from pyspark.mllib.util import MLUtils
    
    # SCALE VARIABLES FOR REGULARIZED LINEAR SGD ALGORITHM
    label = oneHotTRAINreg.map(lambda x: x.label)
    features = oneHotTRAINreg.map(lambda x: x.features)
    scaler = StandardScaler(withMean=False, withStd=True).fit(features)
    dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
    oneHotTRAINregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))
    
    label = oneHotTESTreg.map(lambda x: x.label)
    features = oneHotTESTreg.map(lambda x: x.features)
    scaler = StandardScaler(withMean=False, withStd=True).fit(features)
    dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
    oneHotTESTregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**RÉSULTAT :**

Temps nécessaire pour exécuter au-dessus de cellule : 13.17 secondes


### <a name="cache-objects-in-memory"></a>Objets cache en mémoire

La durée de formation et de test des algorithmes ML peut être réduite par la trame de données d’entrée utilisés pour la classification, régression, objets et des fonctionnalités de mise à l’échelle de la mise en cache.

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary.cache()
    indexedTESTbinary.cache()
    oneHotTRAINbinary.cache()
    oneHotTESTbinary.cache()
    
    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg.cache()
    indexedTESTreg.cache()
    oneHotTRAINreg.cache()
    oneHotTESTreg.cache()
    
    # SCALED FEATURES
    oneHotTRAINregScaled.cache()
    oneHotTESTregScaled.cache()
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**RÉSULTAT :** 

Temps nécessaire pour exécuter au-dessus de cellule : 0,15 secondes


## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Prévoir ou non une info-bulle est payée avec les modèles de classement binaire

Cette section explique comment utiliser trois modèles pour la tâche de classification binaire de prédiction ou non une info-bulle est payée pour un voyage taxi. Les modèles présentés sont :

- REGULARISES régression logistique 
- Modèle de forêt aléatoire
- Arborescences augmentation dégradés

Chaque modèle de création de section de code est fractionné en étapes : 

1. Données **d’apprentissage sur le modèle** avec un paramètre défini
2. **Évaluation de modèle** sur un jeu de données de test avec indicateurs
3. **L’enregistrement modèle** d’objets blob de consommation future

### <a name="classification-using-logistic-regression"></a>Classification à l’aide de la régression logistique

Le code dans cette section montre comment former, évaluer et enregistrer un modèle de régression logistique avec [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) que prévoit est ou non une info-bulle est payée pour un voyage dans le dataset NYC taxi voyage et tarif.

**Former le modèle de régression logistique VC et balayage hyperparameter**

    # LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

    # GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
    from sklearn.metrics import roc_curve,auc
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics
    
    
    # CREATE MODEL WITH ONE SET OF PARAMETERS
    logitModel = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, iterations=20, initialWeights=None, 
                                                   regParam=0.01, regType='l2', intercept=True, corrections=10, 
                                                   tolerance=0.0001, validateData=True, numClasses=2)
    
    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(logitModel.weights))
    print("Intercept: " + str(logitModel.intercept))
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**RÉSULTAT :** 

Les coefficients : [0.0082065285375,-0.0223675576104,-0.0183812028036, - 3.48124578069e-05,-0.00247646947233,-0.00165897881503, 0.0675394837328,-0.111823113101,-0.324609912762,-0.204549780032,-1.36499216354, 0.591088507921,-0.664263411392,-1.00439726852, 3.46567827545,-3.51025855172,-0.0471341112232,-0.043521833294, 0.000243375810385, 0.054518719222]

Ordonnée à l’origine :-0.0111216486893

Temps nécessaire pour exécuter au-dessus de cellule : 14.43 secondes

**Évaluer le modèle de classement binaire avec des mesures standards**

    #EVALUATE LOGISTIC REGRESSION MODEL WITH LBFGS

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # PREDICT ON TEST DATA WITH MODEL
    predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitModel.predict(lp.features)), lp.label))
    
    # INSTANTIATE METRICS OBJECT
    metrics = BinaryClassificationMetrics(predictionAndLabels)

    # AREA UNDER PRECISION-RECALL CURVE
    print("Area under PR = %s" % metrics.areaUnderPR)

    # AREA UNDER ROC CURVE
    print("Area under ROC = %s" % metrics.areaUnderROC)
    metrics = MulticlassMetrics(predictionAndLabels)

    # OVERALL STATISTICS
    precision = metrics.precision()
    recall = metrics.recall()
    f1Score = metrics.fMeasure()
    print("Summary Stats")
    print("Precision = %s" % precision)
    print("Recall = %s" % recall)
    print("F1 Score = %s" % f1Score)


    ## SAVE MODEL WITH DATE-STAMP
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
    dirfilename = modelDir + logisticregressionfilename;
    logitModel.save(sc, dirfilename);
    
    # OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
    logitModel.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
    predictionAndLabelsDF = predictionAndLabels.toDF()
    predictionAndLabelsDF.registerTempTable("tmp_results");
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**RÉSULTAT :** 

Zone sous Valeur_nominale = 0.985297691373

Zone sous ROC = 0.983714670256

Statistiques de synthèse

Précision = 0.984304060189

Rappeler = 0.984304060189

F1 Score = 0.984304060189

Temps nécessaire pour exécuter au-dessus de cellule : 57.61 secondes

**Tracer la courbe ROC.**

Le *predictionAndLabelsDF* est enregistré sous forme de tableau, *tmp_results*, dans la cellule précédente. *tmp_results* peut servir à requêtes et les résultats dans le cadre de données sqlResults pour le tracé. Voici le code.


    # QUERY RESULTS                              
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Voici le code pour réaliser des prévisions et tracer la courbe ROC.

    # MAKE PREDICTIONS AND PLOT ROC-CURVE

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    from sklearn.metrics import roc_curve,auc

    # MAKE PREDICTIONS
    predictions_pddf = test_predictions.rename(columns={'_1': 'probability', '_2': 'label'})
    prob = predictions_pddf["probability"] 
    fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    # PLOT ROC CURVE
    plt.figure(figsize=(5,5))
    plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
    plt.plot([0, 1], [0, 1], 'k--')
    plt.xlim([0.0, 1.0])
    plt.ylim([0.0, 1.05])
    plt.xlabel('False Positive Rate')
    plt.ylabel('True Positive Rate')
    plt.title('ROC Curve')
    plt.legend(loc="lower right")
    plt.show()
    

**RÉSULTAT :**

![Régression logistique ROC curve.png](./media/machine-learning-data-science-spark-data-exploration-modeling/logistic-regression-roc-curve.png)


### <a name="random-forest-classification"></a>Classification aléatoire forêt

Le code dans cette section montre comment former, évaluer et enregistrer un modèle de forêt aléatoire qui prévoit est ou non une info-bulle est payée pour un voyage dans le dataset NYC taxi voyage et tarif.
    
    #PREDICT WHETHER A TIP IS PAID OR NOT USING RANDOM FOREST

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics
    
    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    
    # TRAIN RANDOMFOREST MODEL
    rfModel = RandomForest.trainClassifier(indexedTRAINbinary, numClasses=2, 
                                           categoricalFeaturesInfo=categoricalFeaturesInfo,
                                           numTrees=25, featureSubsetStrategy="auto",
                                           impurity='gini', maxDepth=5, maxBins=32)
    ## UN-COMMENT IF YOU WANT TO PRINT TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())
    
    # PREDICT ON TEST DATA AND EVALUATE
    predictions = rfModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)
    
    # AREA UNDER ROC CURVE
    metrics = BinaryClassificationMetrics(predictionAndLabels)
    print("Area under ROC = %s" % metrics.areaUnderROC)
    
    # PERSIST MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfclassificationfilename = "RandomForestClassification_" + datestamp;
    dirfilename = modelDir + rfclassificationfilename;
    
    rfModel.save(sc, dirfilename);
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**RÉSULTAT :**

Zone sous ROC = 0.985297691373

Temps nécessaire pour exécuter au-dessus de cellule : 31.09 secondes


### <a name="gradient-boosting-trees-classification"></a>Dégradé classification arborescences augmentation

Le code dans cette section montre comment former, évaluer et enregistrer un modèle arborescences augmentation dégradé que prévoit est ou non une info-bulle est payée pour un voyage dans le voyage taxi NYC et dataset du prix.

    #PREDICT WHETHER A TIP IS PAID OR NOT USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    
    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    
    gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo, numIterations=5)
    ## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
    #print('Learned classification GBT model:')
    #print(bgtModel.toDebugString())
    
    # PREDICT ON TEST DATA AND EVALUATE
    predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)
    
    # AREA UNDER ROC CURVE
    metrics = BinaryClassificationMetrics(predictionAndLabels)
    print("Area under ROC = %s" % metrics.areaUnderROC)
    
    # PERSIST MODEL IN A BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp;
    dirfilename = modelDir + btclassificationfilename;
    
    gbtModel.save(sc, dirfilename)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**RÉSULTAT :**

Zone sous ROC = 0.985297691373

Temps nécessaire pour exécuter au-dessus de cellule : 19.76 secondes


## <a name="predict-tip-amounts-for-taxi-trips-with-regression-models"></a>Prévoir des montants de Conseil de voyages taxi avec les modèles de régression

Cette section explique comment utiliser les trois modèles pour la tâche de régression de prédiction de la quantité de l’info-bulle payé pour un voyage taxi basé sur d’autres fonctionnalités de Conseil. Les modèles présentés sont :

- REGULARISES régression linéaire.
- Aléatoire forêt
- Arborescences augmentation dégradés

Ces modèles ont été décrites dans l’introduction. Chaque modèle de création de section de code est fractionné en étapes : 

1. Données **d’apprentissage sur le modèle** avec un paramètre défini
2. **Évaluation de modèle** sur un jeu de données de test avec indicateurs
3. **L’enregistrement modèle** d’objets blob de consommation future

### <a name="linear-regression-with-sgd"></a>Régression linéaire avec SGD 

Le code dans cette section montre comment utiliser les fonctionnalités de mise à l’échelle pour former une régression linéaire qui utilise descente dégradé stochastique (SGD) pour l’optimisation et comment score général, évaluer et enregistrez le modèle dans Azure Blob Storage (WASB).

>[AZURE.TIP] Dans notre expérience, il peut y avoir des problèmes avec la convergence des modèles LinearRegressionWithSGD et paramètres doivent être modifiées/optimisée avec soin pour obtenir un modèle valide. Mise à l’échelle des variables de manière significative permet convergence. 


    #PREDICT TIP AMOUNTS USING LINEAR REGRESSION WITH SGD

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel
    from pyspark.mllib.evaluation import RegressionMetrics
    from scipy import stats
    
    # USE SCALED FEATURES TO TRAIN MODEL
    linearModel = LinearRegressionWithSGD.train(oneHotTRAINregScaled, iterations=100, step = 0.1, regType='l2', regParam=0.1, intercept = True)

    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(linearModel.weights))
    print("Intercept: " + str(linearModel.intercept))
    
    # SCORE ON SCALED TEST DATA-SET & EVALUATE
    predictionAndLabels = oneHotTESTregScaled.map(lambda lp: (float(linearModel.predict(lp.features)), lp.label))
    testMetrics = RegressionMetrics(predictionAndLabels)
    
    # PRINT TEST METRICS
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)
    
    # SAVE MODEL WITH DATE-STAMP IN THE DEFAULT BLOB FOR THE CLUSTER
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = modelDir + linearregressionfilename;
    
    linearModel.save(sc, dirfilename)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**RÉSULTAT :**

Les coefficients : [0.00457675809917,-0.0226314167349,-0.0191910355236, 0.246793409578, 0.312047890459, 0.359634405999, 0.00928692253981,-0.000987181489428,-0.0888306617845, 0.0569376211553, 0.115519551711, 0.149250164995,-0.00990211159703,-0.00637410344522, 0.545083566179,-0.536756072402, 0.0105762393099,-0.0130117577055, 0.0129304737772,-0.00171065945959]

ORDONNEE.origine : 0.853872718283

RMSE = 1.24190115863

R-sqr = 0.608017146081

Temps nécessaire pour exécuter au-dessus de cellule : 58,42 secondes


### <a name="random-forest-regression"></a>Régression aléatoire forêt

Le code dans cette section montre comment former, évaluer et enregistrer une régression forêt aléatoire prévoit est Montant Conseil pour les données de voyage NYC taxi.


    #PREDICT TIP AMOUNTS USING RANDOM FOREST

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics
    
    
    ## TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=25, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=10, maxBins=32)
    ## UN-COMMENT IF YOU WANT TO PRING TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())
    
    ## PREDICT AND EVALUATE ON TEST DATA-SET
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

    # TEST METRICS
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)
    
    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfregressionfilename = "RandomForestRegression_" + datestamp;
    dirfilename = modelDir + rfregressionfilename;
    
    rfModel.save(sc, dirfilename);
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**RÉSULTAT :**

RMSE = 0.891209218139

R-sqr = 0.759661334921

Temps nécessaire pour exécuter au-dessus de cellule : 49.21 secondes


### <a name="gradient-boosting-trees-regression"></a>Dégradé régression arborescences augmentation

Le code dans cette section montre comment former, évaluer et enregistrer un modèle arborescences augmentation dégradé prévoit est Montant Conseil pour les données de voyage NYC taxi.

**Former et évaluer**

    #PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart= datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    from pyspark.mllib.util import MLUtils
    
    ## TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                    numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)
    
    ## EVALUATE A TEST DATA-SET
    predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

    # TEST METRICS
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
    dirfilename = modelDir + btregressionfilename;
    gbtModel.save(sc, dirfilename)
    
    # CONVER RESULTS TO DF AND REGISER TEMP TABLE
    test_predictions = sqlContext.createDataFrame(predictionAndLabels)
    test_predictions.registerTempTable("tmp_results");
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**RÉSULTAT :**

RMSE = 0.908473148639

R-sqr = 0.753835096681

Temps nécessaire pour exécuter au-dessus de cellule : 34.52 secondes

**Traçage**

*tmp_results* est enregistré comme une table Hive dans la cellule précédente. Résultats de la table sont affichés dans le cadre de données *sqlResults* pour le tracé. Voici le code

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results

Voici le code pour tracer les données à l’aide du serveur Jupyter.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    import numpy as np

    # PLOT 
    ax = test_predictions_pddf.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(test_predictions_pddf['_1'], test_predictions_pddf['_2'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    ax.plot(test_predictions_pddf['_1'], fit[0] * test_predictions_pddf['_1'] + fit[1], color='magenta')
    plt.axis([-1, 20, -1, 20])
    plt.show(ax)
    

**RÉSULTAT :**

![Réel-vs-prédites-Conseil-montants](./media/machine-learning-data-science-spark-data-exploration-modeling/actual-vs-predicted-tips.png)

    
## <a name="clean-up-objects-from-memory"></a>Nettoyer les objets de la mémoire

Utiliser `unpersist()` pour supprimer des objets dans la mémoire cache.
        
    # REMOVE ORIGINAL DFs
    taxi_df_train_cleaned.unpersist()
    taxi_df_train_with_newFeatures.unpersist()
    
    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary.unpersist()
    indexedTESTbinary.unpersist()
    oneHotTRAINbinary.unpersist()
    oneHotTESTbinary.unpersist()
    
    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg.unpersist()
    indexedTESTreg.unpersist()
    oneHotTRAINreg.unpersist()
    oneHotTESTreg.unpersist()
    
    # SCALED FEATURES
    oneHotTRAINregScaled.unpersist()
    oneHotTESTregScaled.unpersist()


## <a name="record-storage-locations-of-the-models-for-consumption-and-scoring"></a>Enregistrement des emplacements de stockage des modèles pour la consommation et de notation

Consommer et score un dataset indépendant décrit dans la [Score et évaluer des modèles d’apprentissage automatique intégré à explosion](machine-learning-data-science-spark-model-consumption.md) rubrique, vous devez copier et coller ces noms de fichier contenant les modèles enregistrés créés ici dans le bloc-notes de consommation Jupyter. Voici le code pour imprimer les chemins d’accès aux fichiers de modèle que vous devez il.

    # MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**SORTIE**

logisticRegFileLoc = modelDir + « LogisticRegressionWithLBFGS_2016-05-0317_03_23.516568 »

linearRegFileLoc = modelDir + « LinearRegressionWithSGD_2016-05-0317_05_21.577773 »

randomForestClassificationFileLoc = modelDir + « RandomForestClassification_2016-05-0317_04_11.950206 »

randomForestRegFileLoc = modelDir + « RandomForestRegression_2016-05-0317_06_08.723736 »

BoostedTreeClassificationFileLoc = modelDir + « GradientBoostingTreeClassification_2016-05-0317_04_36.346583 »

BoostedTreeRegressionFileLoc = modelDir + « GradientBoostingTreeRegression_2016-05-0317_06_51.737282 »


## <a name="whats-next"></a>Quoi d’autre ?

À présent que vous avez créé des modèles de régression et classification avec la MlLib explosion, vous êtes prêt à apprendre à score général et évaluer ces modèles. L’exploration de données avancées et de modélisation bloc-notes ouvre plus approfondies, y compris la validation croisée, hyper-paramètre balayant et d’évaluation du modèle. 

**Consommation de modèle :** Pour découvrir comment score général et évaluer les modèles de classement et de régression créés dans cette rubrique, voir [Score et évaluer des modèles d’apprentissage machine intégré à explosion](machine-learning-data-science-spark-model-consumption.md).

**Validation croisée et hyperparameter balayant**: afficher [des données et modélisation avec explosion avancées](machine-learning-data-science-spark-advanced-data-exploration-modeling.md) sur la façon dont les modèles peuvent être formées à l’aide de validation croisée et hyper-paramètre balayage



