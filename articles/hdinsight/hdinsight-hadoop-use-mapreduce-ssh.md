<properties
   pageTitle="Connexion MapReduce et SSH avec Hadoop dans HDInsight | Microsoft Azure"
   description="Découvrez comment utiliser SSH pour exécuter des travaux MapReduce à l’aide de Hadoop sur HDInsight."
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
   ms.date="08/23/2016"
   ms.author="larryfr"/>

# <a name="use-mapreduce-with-hadoop-on-hdinsight-with-ssh"></a>Utiliser MapReduce avec Hadoop sur HDInsight avec SSH

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Dans cet article, vous allez apprendre à utiliser SSH (Secure Shell) pour se connecter à un sur cluster HDInsight, puis soumettez MapReduce travaux à l’aide des commandes Hadoop.

> [AZURE.NOTE] Si vous êtes déjà familiarisé avec l’utilisation de serveurs basés sur Linux Hadoop, mais vous ne connaissez pas HDInsight, voir [conseils HDInsight basé sur Linux](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Conditions préalables

Pour effectuer les étapes décrites dans cet article, vous devez les éléments suivants :

* Un cluster basé sur Linux HDInsight (Hadoop sur HDInsight)

* Un clientSSH. Systèmes d’exploitation Linux, Unix et Mac doit être accompagnée d’un clientSSH. Utilisateurs Windows doivent télécharger un client, tel que [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Se connecter avec SSH

Se connecter au nom de domaine complet (FQDN) de votre cluster HDInsight à l’aide de la commande SSH. Le nom de domaine complet sera le nom que vous avez attribué le cluster suivi **. azurehdinsight.net**. Par exemple, les éléments suivants seraient se connecter à un cluster nommé **myhdinsight**:

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**Si vous avez fourni une clé de certificat pour l’authentification SSH** lorsque vous avez créé le cluster HDInsight, vous devrez peut-être spécifier l’emplacement de la clé privée sur votre système client, par exemple :

    ssh -i ~/mykey.key admin@myhdinsight-ssh.azurehdinsight.net

**Si vous disposez d’un mot de passe pour l’authentification SSH** lorsque vous avez créé le cluster HDInsight, vous devrez fournir le mot de passe lorsque vous y êtes invité.

Pour plus d’informations sur l’utilisation de SSH avec HDInsight, voir [Utiliser SSH avec basé sur Linux Hadoop sur HDInsight de Linux, OS X et Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###<a name="putty-windows-clients"></a>PuTTY (clients Windows)

Windows ne fournit pas un clientSSH intégré. Nous vous recommandons d’utiliser **PuTTY**, qui peut être téléchargé à partir de [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Pour plus d’informations sur l’utilisation de PuTTY, voir [Utiliser SSH avec basé sur Linux Hadoop sur HDInsight à partir de Windows ](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="hadoop"></a>Utilisez les commandes Hadoop

1. Une fois que vous êtes connecté au cluster HDInsight, utilisez la commande **Hadoop** suivante pour démarrer un travail MapReduce :

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

    Cette opération démarre la classe **wordcount** , qui est contenue dans le fichier **hadoop-mapreduce-examples.jar** . En tant qu’entrée, elle utilise le document **wasbs://example/data/gutenberg/davinci.txt** , et sortie est stockée en **wasbs : / / / exemple/données/WordCountOutput**.

    > [AZURE.NOTE] Pour plus d’informations sur cette tâche MapReduce et les données d’exemple, voir [Utiliser MapReduce dans Hadoop sur HDInsight](hdinsight-use-mapreduce.md).

2. La tâche émet détails comme il traite, elle renvoie des informations similaires à ce qui suit lorsque la tâche se termine :

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. Lorsque la tâche est terminée, utilisez la commande suivante pour répertorier les fichiers de sortie qui sont stockés à **wasbs://example/data/WordCountOutput**:

        hdfs dfs -ls wasbs:///example/data/WordCountOutput

    Cela doit afficher deux fichiers, **_SUCCESS** et **composant-r-00000**. Le fichier **composant-r-00000** contient le résultat de cette tâche.

    > [AZURE.NOTE] Certaines tâches MapReduce peuvent réparties les résultats entre plusieurs fichiers **composant-r-###** . Si c’est le cas, utilisez le ## suffixe pour indiquer l’ordre des fichiers.

4. Pour afficher les résultats, utilisez la commande suivante :

        hdfs dfs -cat wasbs:///example/data/WordCountOutput/part-r-00000

    Cela permet d’afficher une liste de mots contenus dans le fichier **wasbs://example/data/gutenberg/davinci.txt** et le nombre d’occurrences de chaque mot. Voici un exemple des données contenues dans le fichier :

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

##<a id="summary"></a>Résumé

Comme vous pouvez le voir, commandes Hadoop offrent un moyen facile à exécuter des travaux MapReduce dans un cluster HDInsight puis visualiser la sortie des tâches.

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur les tâches MapReduce dans HDInsight :

* [Utiliser MapReduce sur HDInsight Hadoop](hdinsight-use-mapreduce.md)

Pour plus d’informations sur les autres méthodes, vous pouvez travailler avec Hadoop sur HDInsight :

* [Utiliser Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)

* [Utiliser cochon avec Hadoop sur HDInsight](hdinsight-use-pig.md)
