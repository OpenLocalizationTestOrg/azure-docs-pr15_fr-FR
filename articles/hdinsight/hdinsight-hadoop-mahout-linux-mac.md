<properties
    pageTitle="Générer des recommandations à l’aide de Mahout et basé sur Linux HDInsight | Microsoft Azure"
    description="Découvrez comment utiliser l’ordinateur Apache Mahout bibliothèque d’apprentissage pour générer des recommandations vidéo avec basé sur Linux HDInsight (Hadoop)."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/30/2016"
    ms.author="larryfr"/>

#<a name="generate-movie-recommendations-by-using-apache-mahout-with-linux-based-hadoop-in-hdinsight"></a>Générer des recommandations de vidéo à l’aide de Apache Mahout avec Hadoop basé sur Linux dans HDInsight

[AZURE.INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Apprenez à utiliser la machine [Mahout Apache](http://mahout.apache.org) bibliothèque avec Azure HDInsight d’apprentissage pour générer des recommandations vidéo.

Mahout est une [machine d’apprentissage] [ ml] bibliothèque pour Apache Hadoop. Mahout contient des algorithmes pour le traitement des données, telles que le filtrage, classement et cluster. Dans cet article, vous allez utiliser un moteur de recommandation pour générer des recommandations vidéo basés sur films que vos amis ont vu.

> [AZURE.NOTE] Les étapes décrites dans ce document nécessitent une Hadoop basé sur Linux sur cluster HDInsight. Pour plus d’informations sur l’utilisation de Mahout avec un cluster Windows, voir [recommandations de film générer à l’aide de Mahout Apache avec Hadoop fonctionnant sous Windows dans HDInsight](hdinsight-mahout.md)

##<a name="prerequisites"></a>Conditions préalables

* Hadoop basé sur Linux sur cluster HDInsight. Pour plus d’informations sur la création d’une, voir [commencer à utiliser Hadoop basé sur Linux dans HDInsight][getstarted]

##<a name="mahout-versioning"></a>Contrôle de version mahout

Pour plus d’informations sur la version de Mahout inclus avec votre cluster HDInsight, voir [HDInsight versions et composants Hadoop](hdinsight-component-versioning.md).

> [AZURE.WARNING] S’il est possible de télécharger une version différente du Mahout vers le cluster HDInsight, seuls les composants fournis avec le cluster HDInsight entièrement prises en charge et Support Microsoft aidera à identifier et résoudre les problèmes liés à ces composants.
>
> Composants personnalisés recevront RCSI prise en charge pour vous aider à résoudre le problème. Cela peut provoquer de résoudre le problème ou vous demandant de s’engager canaux disponibles pour les technologies d’ouvrir la source où se trouve une grande expertise pour que la technologie. Par exemple, de nombreux sites communautaires qui peuvent être utilisés, comme : [forum MSDN pour HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Projets Apache contient des sites de projet sur [http://apache.org](http://apache.org), par exemple : [Hadoop](http://hadoop.apache.org/), [commandé](http://spark.apache.org/).

##<a name="recommendations"></a>Recommandations de présentation

Un des fonctions qui est fourni par Mahout est un moteur de recommandation. Ce moteur accepte les données dans le format de `userID`, `itemId`, et `prefValue` (la préférence sur les utilisateurs de l’élément). Mahout pouvez puis effectuer une analyse de co-création occurrence pour déterminer : _les utilisateurs qui ont une préférence pour un élément disposez également d’une préférence pour ces autres éléments_. Mahout détermine ensuite les utilisateurs grâce aux préférences élément semblable, qui peuvent servir à faire des recommandations.

Voici un exemple très simple qui utilise des vidéos :

* __Co-création occurrence__: Jean, Alice et Bob aime _étoile de fichiers_, _L’Empire avertissements précédent_et _revenir de la Jedi_. Mahout détermine que les utilisateurs qui aiment également l’une de ces vidéos comme les deux autres.

* __Co-création occurrence__: Bob et Alice aime également _La Menace fantôme_, _attaque des Clones_et _Vengeance de la Sith_. Mahout détermine que les utilisateurs qui ont aimé les trois films précédents également comme ces trois.

* __Recommandation similitudes entre__: Jean, car aime les trois premières films, Mahout examine que d’autres personnes grâce aux préférences similaires aime films, mais Jean n’a pas observé (aime/nominal). Dans ce cas, Mahout recommande _Le Menace fantôme_, _attaque des Clones_et _Vengeance de la Sith_.

###<a name="understanding-the-data"></a>Comprendre les données

Facilement, [Recherche GroupLens] [ movielens] fournit des données de l’évaluation des vidéos dans un format compatible avec Mahout. Ces données sont disponibles sur le stockage par défaut de votre cluster en `/HdiSamples/HdiSamples/MahoutMovieData`.

Il existe deux fichiers, `moviedb.txt` (informations sur les films) et `user-ratings.txt`. Le fichier utilisateur ratings.txt est utilisé lors de l’analyse, tandis que moviedb.txt est utilisée pour fournir des informations de texte convivial lorsque vous affichez les résultats de l’analyse.

Les données contenues dans utilisateur ratings.txt possèdent une structure de `userID`, `movieID`, `userRating`, et `timestamp`, qui nous indique comment recommandé de chaque utilisateur avec une vidéo. Voici un exemple de données :


    196 242 3   881250949
    186 302 3   891717742
    22  377 1   878887116
    244 51  2   880606923
    166 346 1   886397596

##<a name="run-the-analysis"></a>Exécuter l’analyse

Utilisez la commande suivante pour exécuter la tâche recommandation :

    mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp

> [AZURE.NOTE] La tâche peut prendre quelques minutes et peut être exécutée plusieurs tâches MapReduce.

##<a name="view-the-output"></a>Afficher les résultats

1. Une fois que la tâche est terminée, utilisez la commande suivante pour afficher la sortie générée :

        hdfs dfs -text /example/data/mahoutout/part-r-00000

    Le résultat apparaît comme suit :

        1   [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
        2   [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
        3   [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
        4   [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

    La première colonne contient la `userID`. Les valeurs contenues dans ' [ » et «] » sont `movieId`:`recommendationScore`.

2. Vous pouvez utiliser la sortie, ainsi que la moviedb.txt, pour afficher davantage d’informations utilisateur convivial. Tout d’abord, nous avons besoin de copier les fichiers localement à l’aide des commandes suivantes :

        hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
        hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .

    Cela copie les données de sortie dans un fichier nommé **recommendations.txt** dans le répertoire actif, ainsi que les fichiers de données vidéo.

3. Utilisez la commande suivante pour créer un nouveau script de Python aperçu des noms de film pour les données dans la sortie de recommandations :

        nano show_recommendations.py

    Ouverture de l’éditeur, utilisez ce qui suit le contenu du fichier :

        #!/usr/bin/env python

        import sys

        if len(sys.argv) != 5:
                print "Arguments: userId userDataFilename movieFilename recommendationFilename"
                sys.exit(1)

        userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]

        print "Reading Movies Descriptions"
        movieFile = open(movieFilename)
        movieById = {}
        for line in movieFile:
                tokens = line.split("|")
                movieById[tokens[0]] = tokens[1:]
        movieFile.close()

        print "Reading Rated Movies"
        userDataFile = open(userDataFilename)
        ratedMovieIds = []
        for line in userDataFile:
                tokens = line.split("\t")
                if tokens[0] == userId:
                        ratedMovieIds.append((tokens[1],tokens[2]))
        userDataFile.close()

        print "Reading Recommendations"
        recommendationFile = open(recommendationFilename)
        recommendations = []
        for line in recommendationFile:
                tokens = line.split("\t")
                if tokens[0] == userId:
                        movieIdAndScores = tokens[1].strip("[]\n").split(",")
                        recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
                        break
        recommendationFile.close()

        print "Rated Movies"
        print "------------------------"
        for movieId, rating in ratedMovieIds:
                print "%s, rating=%s" % (movieById[movieId][0], rating)
        print "------------------------"

        print "Recommended Movies"
        print "------------------------"
        for movieId, score in recommendations:
                print "%s, score=%s" % (movieById[movieId][0], score)
        print "------------------------"

    Appuyez sur **Ctrl-X**, **Y**et enfin **entrée** pour enregistrer les données.

3. Pour rendre le fichier exécutable, utilisez la commande suivante :

        chmod +x show_recommendations.py

4. Exécuter le script Python. La procédure suivante suppose que vous êtes dans le répertoire où tous les fichiers téléchargés :

        ./show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt

    Cela va examiner les recommandations générées pour utilisateur ID 4.

    * Le fichier **utilisateur ratings.txt** est utilisé pour récupérer les vidéos que l’utilisateur a classés
    * Le fichier **moviedb.txt** est utilisé pour récupérer les noms des films
    * La **recommendations.txt** est utilisée pour récupérer les recommandations film pour cet utilisateur

    Le résultat de cette commande sera semblable à ce qui suit :

        Reading Movies Descriptions
        Reading Rated Movies
        Reading Recommendations
        Rated Movies
        ------------------------
        Mimic (1997), rating=3
        Ulee's Gold (1997), rating=5
        Incognito (1997), rating=5
        One Flew Over the Cuckoo's Nest (1975), rating=4
        Event Horizon (1997), rating=4
        Client, The (1994), rating=3
        Liar Liar (1997), rating=5
        Scream (1996), rating=4
        Star Wars (1977), rating=5
        Wedding Singer, The (1998), rating=5
        Starship Troopers (1997), rating=4
        Air Force One (1997), rating=5
        Conspiracy Theory (1997), rating=3
        Contact (1997), rating=5
        Indiana Jones and the Last Crusade (1989), rating=3
        Desperate Measures (1998), rating=5
        Seven (Se7en) (1995), rating=4
        Cop Land (1997), rating=5
        Lost Highway (1997), rating=5
        Assignment, The (1997), rating=5
        Blues Brothers 2000 (1998), rating=5
        Spawn (1997), rating=2
        Wonderland (1997), rating=5
        In & Out (1997), rating=5
        ------------------------
        Recommended Movies
        ------------------------
        Seven Years in Tibet (1997), score=5.0
        Indiana Jones and the Last Crusade (1989), score=5.0
        Jaws (1975), score=5.0
        Sense and Sensibility (1995), score=5.0
        Independence Day (ID4) (1996), score=5.0
        My Best Friend's Wedding (1997), score=5.0
        Jerry Maguire (1996), score=5.0
        Scream 2 (1997), score=5.0
        Time to Kill, A (1996), score=5.0
        Rock, The (1996), score=5.0
        ------------------------

##<a name="delete-temporary-data"></a>Supprimer les données temporaires

Tâches mahout ne suppriment pas de données temporaires sont créé pendant le traitement de la tâche. La `--tempDir` paramètre est spécifié dans le travail d’exemple à isoler les fichiers temporaires dans un chemin d’accès spécifique pour la suppression simple. Pour supprimer les fichiers temporaires, utilisez la commande suivante :

    hdfs dfs -rm -f -r /temp/mahouttemp

> [AZURE.WARNING] Si vous souhaitez exécuter la commande à nouveau, vous devez également supprimer le répertoire de sortie. Pour supprimer ce répertoire, utilisez ce qui suit :
>
> ```hdfs dfs -rm -f -r /example/data/mahoutout```

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris à utiliser Mahout, découvrez d’autres façons de travailler avec des données sur HDInsight :

* [Ruche avec HDInsight](hdinsight-use-hive.md)
* [Cochon avec HDInsight](hdinsight-use-pig.md)
* [MapReduce avec HDInsight](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
 
