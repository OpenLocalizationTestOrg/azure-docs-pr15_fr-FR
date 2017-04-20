<properties
    pageTitle="Exécuter des exemples Hadoop MapReduce sur basé sur Linux HDInsight | Microsoft Azure"
    description="Commencer à utiliser MapReduce exemples avec HDInsight basé sur Linux. Utiliser SSH pour vous connecter au cluster, puis utilisez la commande Hadoop pour exécuter des travaux de l’échantillon."
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
    ms.date="09/27/2016"
    ms.author="larryfr"/>




#<a name="run-the-hadoop-samples-in-hdinsight"></a>Exécution des exemples Hadoop dans HDInsight

[AZURE.INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

Basé sur Linux HDInsight clusters offrent un ensemble d’exemples MapReduce qui peut être utilisée pour vous familiariser avec l’exécution des travaux Hadoop MapReduce. Dans ce document, vous en savoir plus sur les exemples disponibles et passez en revue quelques d'entre eux en cours d’exécution.

##<a name="prerequisites"></a>Conditions préalables

- **Abonnement un Azure**: voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)

- **HDInsight basé sur Linux A cluster**: voir [commencer à utiliser Hadoop avec Hive dans HDInsight sous Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

- **Un SSH client**: pour plus d’informations sur l’utilisation de SSH avec HDInsight, voir les articles suivants :

    - [Utiliser le protocole SSH avec basé sur Linux Hadoop sur HDInsight de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Utiliser le protocole SSH avec basé sur Linux Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

## <a name="the-samples"></a>Les exemples ##

**Emplacement**: les exemples sont situés dans le cluster HDInsight à **/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar**

**Contenu**: les exemples suivants sont contenues dans cette archive :

- **aggregatewordcount**: un agrégat en fonction de programme réduire/carte qui compte les mots dans les fichiers d’entrée
- **aggregatewordhist**: un agrégat en fonction de programme réduire/carte qui calcule l’histogramme des mots dans les fichiers d’entrée
- **BBP**: un programme réduire/carte qui utilise Bailey-Borwein-Plouffe pour calculer des chiffres exactes de pi.
- **dbcount**: une tâche d’exemple qui compte les journaux de la page d’affichage stockées dans un une base de données
- **distbbp**: un programme réduire/carte qui utilise une formule BBP type pour calculer bits exactes de pi.
- **GREP**: un programme réduire/carte qui compte les correspondances des expressions régulières dans l’entrée
- **jointure**: une tâche qui affecte une jointure plus triées, tout aussi partition jeux de données
- **multifilewc**: une tâche qui compte les mots à partir de plusieurs fichiers
- **pentomino**: une carte/réduire vignette portant programme pour rechercher des solutions aux problèmes pentomino
- **pi**: un programme réduire/carte qui détermine Pi à l’aide d’un Monte quasi-coupon méthode Carlo
- **randomtextwriter**: un programme réduire/carte qui écrit 10 Go de données textuelles aléatoires par nœud
- **randomwriter**: un programme réduire/carte qui écrit 10 Go de données aléatoires par nœud
- **secondarySort**: exemple définissant un tri secondaire pour la réduire
- **Trier**: un programme réduire/carte qui trie les données écrites par le writer aléatoire
- **Sudoku**: un solveur sudoku
- **teragen**: générer des données pour la terasort
- **terasort**: exécuter le terasort
- **teravalidate**: vérifier les résultats des terasort
- **WordCount**: un programme réduire/carte qui compte les mots dans les fichiers d’entrée
- **wordmean**: un programme réduire/carte qui détermine la longueur moyenne des mots dans les fichiers d’entrée
- **wordmedian**: un programme réduire/carte qui détermine la longueur MEDIANE des mots dans les fichiers d’entrée
- **wordstandarddeviation**: un programme réduire/carte qui détermine l’écart type de la longueur des mots dans les fichiers d’entrée

**Code source**: code Source pour ces exemples est inclus dans le cluster HDInsight à **/usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples**

> [AZURE.NOTE] La `2.2.4.9-1` dans le chemin d’accès est la version de la plateforme de données Hortonworks pour le cluster HDInsight et peut changer car HDInsight est mis à jour.

## <a name="how-to-run-the-samples"></a>Comment faire pour exécuter les exemples ##

1. Se connecter à HDInsight à l’aide de SSH comme décrit dans les articles suivants :

    - [Utiliser le protocole SSH avec basé sur Linux Hadoop sur HDInsight de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Utiliser le protocole SSH avec basé sur Linux Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. À partir de la `username@#######:~$` invite, utilisez la commande suivante pour répertorier les exemples :

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar

    Cette action génère la liste des exemples de la section précédente de ce document.

3. Utilisez la commande suivante pour obtenir de l’aide sur un échantillon spécifique. Dans ce cas, l’exemple **wordcount** :

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount

    Vous devez recevoir le message suivant :

        Usage: wordcount <in> [<in>...] <out>

    Cela signifie que vous pouvez fournir plusieurs chemins d’accès d’entrée pour les documents d’origine. Le chemin d’accès final est l’endroit où le résultat (nombre de mots dans les documents d’origine,) est stocké.

4. Pour compter tous les mots dans les blocs-notes de Leonardo Da Vinci, qui sont fournies comme exemples de données avec votre cluster, utilisez ce qui suit :

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount

    Entrée pour cette tâche est lue à partir de **wasbs:///example/data/gutenberg/davinci.txt**.

    Sortie de cet exemple est stocké dans **wasbs : / / / exemple/données/davinciwordcount**.

    > [AZURE.NOTE] Comme indiqué dans l’aide de l’exemple wordcount, vous pouvez également spécifier plusieurs fichiers d’entrée. Par exemple, `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` serait compter les mots dans davinci.txt et ulysses.txt.

5. Une fois que la tâche est terminée, utilisez la commande suivante pour afficher le résultat :

        hdfs dfs -cat /example/data/davinciwordcount/*

    Cela concatène tous les fichiers de sortie produits par la tâche et les afficher. Cet exemple de base est enregistrée qu’un seul fichier, cependant s’il y a plusieurs cette commande serait parcourir tous les.

    Le résultat est semblable à ce qui suit :

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    Chaque ligne représente un mot et le nombre de fois qu’il est survenu dans les données d’entrée.

## <a name="sudoku"></a>Sudoku

L’exemple Sudoku compose des instructions d’utilisation quelque peu inutile ; « Inclure un puzzle sur la ligne de commande ».

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) est un puzzle logique composé de 3 x neuf 3 grilles. Certaines des cellules de la grille des numéros, tandis que d’autres personnes sont vides, l’objectif consiste à résoudre pour les cellules vides. Le lien ci-dessus comporte plus d’informations sur le puzzle, mais l’objectif de cet exemple consiste à résoudre pour les cellules vides. Donc notre entrée doit être un fichier qui se trouve dans le format suivant :

- Neuf lignes de neuf colonnes

- Chaque colonne peut contenir un nombre ou `?` (qui indique une cellule vide)

- Cellules sont séparées par un espace

Il existe une certaine manière afin de créer des jeux de Sudoku ; Vous ne pouvez pas répéter un nombre dans une colonne ou une ligne. Voici un exemple sur le cluster HDInsight qui est correctement construit. Il est situé dans **/usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta** et contient les éléments suivants :

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

> [AZURE.NOTE] La `2.2.4.9-1` partie du chemin peut-être changer lorsque des mises à jour sont apportées au cluster HDInsight.

Pour exécuter cette via l’exemple Sudoku, utilisez la commande suivante :

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/2.2.9.1-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta

Les résultats doivent ressembler à ce qui suit :

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## <a name="pi-"></a>PI (π)

L’exemple de pi utilise une statistique (quasi-coupon Monte Carlo) méthode pour évaluer la valeur de pi. Points placés au hasard à l’intérieur d’une unité carrée également relèvent au sein d’un cercle inscrit dans ce carré avec une probabilité égale à la zone du cercle, pi/4. La valeur de pi peut être estimée de la valeur de 4R, où R est le rapport entre le nombre de points à l’intérieur du cercle pour le nombre total de points qui se trouvent dans le carré. Obtenir l’exemple de points utilisés est grande, plus l’estimation est.

La mapper de cet exemple génère un nombre de points de manière aléatoire à l’intérieur d’un carré unité, puis compte le nombre de ces points situés à l’intérieur du cercle.

Le réducteur sont additionnés points comptés en les mappeurs puis et calcule la valeur de pi à partir de la formule 4R, où R est le rapport entre le nombre de points comptés dans le cercle et le nombre total de points qui se trouvent dans le carré.

Utiliser la commande suivante pour exécuter cet exemple. Cet exemple utilise 16 cartes avec les 10 000 000 exemples chaque pour évaluer la valeur de pi :

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000

La valeur renvoyée par cette doit être semblable à **3.14159155000000000000**. Pour les références, les 10 premières décimales de pi sont 3.1415926535.

##<a name="10gb-greysort"></a>10 Go Greysort

GraySort est un tri de test d’évaluation dont métrique est le taux de tri (to/minute) obtenu lors du tri de très grandes quantités de données, généralement un 100 To minimale.

Cet exemple utilise un petit 10 Go de données afin qu’elle puisse être exécutée relativement rapidement. Il utilise les applications MapReduce développées par Owen O'Malley et Arun Murthy qui a remporté la référence de tri to (« daytona ») à usage général annuel en 2009 avec un taux de 0.578 to/min (100 To en minutes 173). Pour plus d’informations sur cette et d’autres critères de tri, consultez le site [Sortbenchmark](http://sortbenchmark.org/) .

Cet exemple utilise trois séries de programmes MapReduce :

- **TeraGen**: un programme MapReduce qui génère des lignes de données à trier

- **TeraSort**: exemples de données d’entrée et utilise MapReduce pour trier les données dans un ordre total

    TeraSort est un tri standard des fonctions MapReduce, à l’exception d’un partitionneur personnalisé qui utilise une liste triée des clés N-1 échantillonnée qui définissent la plage de clés pour chaque réduire. En particulier, toutes les clés de ces exemples [i-1] < = clé < exemple [i] sont envoyées à réduire i. Cela garanties que les sorties de réduisent i sont tous les inférieure à la sortie de réduire i + 1.

- **TeraValidate**: un programme MapReduce qui valide que le résultat est globalement trié

    Il crée un mappage par fichier dans le répertoire de sortie, et chaque carte garantit que chaque clé est inférieure ou égale à la précédente. La fonction carte génère également des enregistrements des premier et derniers clés de chaque fichier et la fonction réduire garantit que la première clé de fichier i est supérieure à la dernière clé d’i-1 de fichier. Des problèmes sont signalés comme résultat de la réduction avec les touches qui sont désordre.

Utilisez ce qui suit étapes pour générer des données, trier et contrôlez ensuite le résultat :

1. Générer 10 Go de données, ce qui est stockées dans un espace de stockage du cluster HDInsight par défaut en **wasbs : / / / exemple / / 10 Go tri-d’entrée de données**:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input

    La `-Dmapred.map.tasks` indique Hadoop combien de tâches carte à utiliser pour cette tâche. Les deux paramètres finales demander à la tâche pour créer de 10 Go de données et stocker sur **wasbs : / / / exemple / / 10 Go tri-d’entrée de données**.

2. Pour trier les données, utilisez la commande suivante :

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output

    La `-Dmapred.reduce.tasks` indique Hadoop combien réduire les tâches à utiliser pour la tâche. Les deux paramètres finales sont uniquement les emplacements d’entrée et de sortie des données.

3. Pour valider les données générées par le tri, utilisez ce qui suit :

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate

##<a name="next-steps"></a>Étapes suivantes ##

À partir de cet article, vous avez appris exécuter les exemples inclus dans les groupes HDInsight basé sur Linux. Pour les didacticiels sur l’utilisation de cochon Hive et MapReduce avec HDInsight, consultez les rubriques suivantes :

* [Utiliser cochon avec Hadoop sur HDInsight][hdinsight-use-pig]
* [Utiliser Hive avec Hadoop sur HDInsight][hdinsight-use-hive]
* [Utiliser MapReduce avec Hadoop sur HDInsight] [hdinsight-use-mapreduce]



[hdinsight-errors]: hdinsight-debug-jobs.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-introduction]: hdinsight-hadoop-introduction.md



[hdinsight-samples]: hdinsight-run-samples.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
