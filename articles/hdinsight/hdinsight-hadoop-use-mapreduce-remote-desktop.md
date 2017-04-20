<properties
   pageTitle="MapReduce et Bureau à distance avec Hadoop dans HDInsight | Microsoft Azure"
   description="Découvrez comment utiliser Bureau à distance pour se connecter à sur HDInsight et exécuter des travaux MapReduce."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/27/2016"
   ms.author="larryfr"/>

# <a name="use-mapreduce-in-hadoop-on-hdinsight-with-remote-desktop"></a>Utiliser MapReduce dans Hadoop sur HDInsight avec Bureau à distance

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Dans cet article, vous allez apprendre à se connecter à un sur cluster HDInsight à l’aide de bureau à distance, puis exécutez MapReduce travaux à l’aide de la commande Hadoop.

##<a id="prereq"></a>Conditions préalables

Pour effectuer les étapes décrites dans cet article, vous devez les éléments suivants :

* Un cluster fonctionnant sous Windows HDInsight (Hadoop sur HDInsight)

* Un ordinateur client exécutant Windows 10, Windows 8 ou Windows 7

##<a id="connect"></a>Connexion Bureau à distance

Activer le Bureau à distance pour le cluster HDInsight, puis vous y connecter en suivant les instructions à [se connecter à clusters HDInsight à l’aide de RDP](hdinsight-administer-use-management-portal.md#rdp).

##<a id="hadoop"></a>Utilisez la commande Hadoop

Lorsque vous êtes connecté sur le bureau pour le cluster HDInsight, procédez comme suit pour exécuter une tâche MapReduce à l’aide de la commande Hadoop :

1. À partir du bureau HDInsight, commencer la **ligne de commande Hadoop**. Cette action ouvre une nouvelle invite dans la **c:\apps\dist\hadoop-&lt;numéro de version >** répertoire.

    > [AZURE.NOTE] Le numéro de version change à mesure que Hadoop est mis à jour. La variable d’environnement **HADOOP_HOME** peut être utilisée pour rechercher le chemin d’accès. Par exemple, `cd %HADOOP_HOME%` répertoires pour passer au répertoire Hadoop, sans avoir à connaître le numéro de version.

2. Pour utiliser la commande **Hadoop** pour exécuter une tâche MapReduce exemple, utilisez la commande suivante :

        hadoop jar hadoop-mapreduce-examples.jar wordcount wasbs:///example/data/gutenberg/davinci.txt wasbs:///example/data/WordCountOutput

    Cette opération démarre la classe **wordcount** , qui est contenue dans le fichier **hadoop-mapreduce-examples.jar** dans le répertoire actif. En tant qu’entrée, elle utilise le document **wasbs://example/data/gutenberg/davinci.txt** et sortie se trouve au : **wasbs : / / / exemple/données/WordCountOutput**.

    > [AZURE.NOTE] Pour plus d’informations sur cette tâche MapReduce et les données d’exemple, voir <a href="hdinsight-use-mapreduce.md">Utiliser MapReduce dans HDInsight Hadoop</a>.

2. La tâche émet détails comme il est traité, elle renvoie des informations similaires à ce qui suit lorsque la tâche est terminée :

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. Lorsque la tâche est terminée, utilisez la commande suivante pour répertorier les fichiers de sortie stockées à **wasbs://example/data/WordCountOutput**:

        hadoop fs -ls wasbs:///example/data/WordCountOutput

    Cela doit afficher deux fichiers, **_SUCCESS** et **composant-r-00000**. Le fichier **composant-r-00000** contient le résultat de cette tâche.

    > [AZURE.NOTE] Certaines tâches MapReduce peuvent réparties les résultats entre plusieurs fichiers **composant-r-###** . Si c’est le cas, utilisez le ## suffixe pour indiquer l’ordre des fichiers.

4. Pour afficher les résultats, utilisez la commande suivante :

        hadoop fs -cat wasbs:///example/data/WordCountOutput/part-r-00000

    Cela permet d’afficher une liste de mots contenus dans le fichier **wasbs://example/data/gutenberg/davinci.txt** , ainsi que le nombre d’occurrences de chaque mot. Voici un exemple des données contenues dans le fichier :

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

##<a id="summary"></a>Résumé

Comme vous pouvez le voir, la commande Hadoop fournit un moyen facile à exécuter des tâches MapReduce sur un cluster HDInsight puis visualiser la sortie des tâches.

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur les tâches MapReduce dans HDInsight :

* [Utiliser MapReduce sur HDInsight Hadoop](hdinsight-use-mapreduce.md)

Pour plus d’informations sur les autres méthodes, vous pouvez travailler avec Hadoop sur HDInsight :

* [Utiliser Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)

* [Utiliser cochon avec Hadoop sur HDInsight](hdinsight-use-pig.md)
