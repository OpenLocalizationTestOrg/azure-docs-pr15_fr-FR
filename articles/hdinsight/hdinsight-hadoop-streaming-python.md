<properties
   pageTitle="Développer des travaux Python MapReduce avec HDInsight | Microsoft Azure"
   description="Découvrez comment créer et exécuter des travaux MapReduce Python sur clusters HDInsight basé sur Linux."
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
   ms.date="10/11/2016"
   ms.author="larryfr"/>

#<a name="develop-python-streaming-programs-for-hdinsight"></a>Développer les Python diffusion en continu de programmes pour HDInsight

Hadoop fournit une API de flux pour MapReduce qui vous permet d’écrire carte et réduire des fonctions dans une langue différente Java. Dans cet article, vous allez apprendre à utiliser Python pour effectuer des opérations MapReduce.

> [AZURE.NOTE] Si le code Python dans ce document peut être utilisé avec un cluster HDInsight fonctionnant sous Windows, les étapes décrites dans ce document sont spécifiques aux clusters basés sur Linux.

Cet article est basé sur des informations et des exemples publiés par Michael Noll à [écrire un programme MapReduce Hadoop dans Python](http://www.michael-noll.com/tutorials/writing-an-hadoop-mapreduce-program-in-python/).

##<a name="prerequisites"></a>Conditions préalables

Pour effectuer les étapes décrites dans cet article, vous devez les éléments suivants :

* Un Hadoop basé sur Linux sur cluster HDInsight

* Un éditeur de texte

    > [AZURE.IMPORTANT] L’éditeur de texte doit utiliser saut de ligne que la fin de ligne. Si elle utilise CRLF, cela provoquera des erreurs lors de l’exécution du travail MapReduce sur les clusters basés sur Linux HDInsight. Si vous n’êtes pas sûr, utilisez l’étape facultative dans la section [MapReduce exécuter](#run-mapreduce) pour convertir les CRLF saut de ligne.

* Pour les clients de Windows, PuTTY et PSCP. Ces utilitaires sont disponibles à partir de la <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">Page de téléchargement PuTTY</a>.

##<a name="word-count"></a>Nombre de mots

Dans cet exemple, vous allez implémenter un compteur de base de word en utilisant un mapper et réducteur. La mapper décompose phrases en mots individuels, et le réducteur regroupe les mots et compte pour produire la sortie.

Le diagramme suivant illustre ce que se passe-t-il lors de la carte et réduire les phases.

![illustration de carte réduire](./media/hdinsight-hadoop-streaming-python/HDI.WordCountDiagram.png)

##<a name="why-python"></a>Pourquoi Python ?

Python est un langage de programmation à usage général, de haut niveau qui vous permet de concepts express dans moins de lignes de code que de nombreuses autres langues. Il a récemment est devenu populaires avec scientifiques données comme langue de prototypage car sa nature interprété, en tapant dynamique et élégante syntaxe appropriée pour le développement d’applications rapide.

Python est installé sur tous les clusters HDInsight.

##<a name="streaming-mapreduce"></a>MapReduce diffusion en continu

Hadoop permet de vous permettent de spécifier un fichier qui contient le mappage et réduire la logique qui est utilisée par une tâche. Les exigences spécifiques de la carte et réduire logique sont :

* **Saisie**: la carte et réduire les composants doivent lire les données d’entrée de STDIN.

* **Résultat**: la carte et réduire les composants doivent écrire des données de sortie à STDOUT.

* **Format des données**: les données consommées et produites doivent être une paire clé/valeur, séparée par un caractère de tabulation.

Python pouvez facilement répondre à ces exigences en utilisant le module **sys** lire à partir de STDIN et **Imprimer** pour imprimer sur STDOUT. La tâche restante d’est simplement mise en forme les données avec un onglet (`\t`) caractère entre la clé et la valeur.

##<a name="create-the-mapper-and-reducer"></a>Créer la mapper et réducteur

Mapper les réducteur sont des fichiers texte dans ce cas **mapper.py** et **reducer.py** (pour indiquer clairement qui fait quoi). Vous pouvez créer à l’aide de l’éditeur de votre choix.

###<a name="mapperpy"></a>Mapper.py

Créer un nouveau fichier nommé **mapper.py** et utilisez le code suivant en tant que le contenu :

    #!/usr/bin/env python

    # Use the sys module
    import sys

    # 'file' in this case is STDIN
    def read_input(file):
        # Split each line into words
        for line in file:
            yield line.split()

    def main(separator='\t'):
        # Read the data using read_input
        data = read_input(sys.stdin)
        # Process each words returned from read_input
        for words in data:
            # Process each word
            for word in words:
                # Write to STDOUT
                print '%s%s%d' % (word, separator, 1)

    if __name__ == "__main__":
        main()

Prenez quelques instants pour la lecture du code afin de vous aider à comprendre le rôle.

###<a name="reducerpy"></a>REDUCER.py

Créer un nouveau fichier nommé **reducer.py** et utilisez le code suivant en tant que le contenu :

    #!/usr/bin/env python

    # import modules
    from itertools import groupby
    from operator import itemgetter
    import sys

    # 'file' in this case is STDIN
    def read_mapper_output(file, separator='\t'):
        # Go through each line
        for line in file:
            # Strip out the separator character
            yield line.rstrip().split(separator, 1)

    def main(separator='\t'):
        # Read the data using read_mapper_output
        data = read_mapper_output(sys.stdin, separator=separator)
        # Group words and counts into 'group'
        #   Since MapReduce is a distributed process, each word
        #   may have multiple counts. 'group' will have all counts
        #   which can be retrieved using the word as the key.
        for current_word, group in groupby(data, itemgetter(0)):
            try:
                # For each word, pull the count(s) for the word
                #   from 'group' and create a total count
                total_count = sum(int(count) for current_word, count in group)
                # Write to stdout
                print "%s%s%d" % (current_word, separator, total_count)
            except ValueError:
                # Count was not a number, so do nothing
                pass

    if __name__ == "__main__":
        main()

##<a name="upload-the-files"></a>Téléchargez les fichiers

**Mapper.py** et **reducer.py** doivent se trouver sur le nœud de tête du cluster avant de pouvoir exécuter les. Pour les télécharger, le plus simple consiste à utiliser **scp** (**pscp** si vous utilisez un client Windows).

À partir du client, dans le même répertoire que **mapper.py** et **reducer.py**, utilisez la commande suivante. Remplacez **nom_utilisateur** par un utilisateur SSH et **nomducluster** avec le nom de votre cluster.

    scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:

Cette action copie les fichiers à partir du système local dans le nœud de tête.

> [AZURE.NOTE] Si vous avez utilisé un mot de passe pour protéger votre compte SSH, le système vous demandera le mot de passe. Si vous avez utilisé un code SSH, vous devrez peut-être utiliser les `-i` paramètre et le chemin d’accès à la clé privée, par exemple, `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`.

##<a name="run-mapreduce"></a>Exécuter MapReduce

1. Connectez-vous au cluster à l’aide de SSH :

        ssh username@clustername-ssh.azurehdinsight.net

    > [AZURE.NOTE] Si vous avez utilisé un mot de passe pour protéger votre compte SSH, le système vous demandera le mot de passe. Si vous avez utilisé un code SSH, vous devrez peut-être utiliser les `-i` paramètre et le chemin d’accès à la clé privée, par exemple, `ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`.

2. (Facultatif) Si vous avez utilisé un éditeur de texte qui utilise CRLF comme finissant lorsque vous créez les fichiers mapper.py et reducer.py, ou ne pas savoir ce que votre éditeur de fin de ligne utilise, utilisez la commande suivante commandes pour convertir occurrences de CRLF dans mapper.py et reducer.py saut de ligne.

        perl -pi -e 's/\r\n/\n/g' mappery.py
        perl -pi -e 's/\r\n/\n/g' reducer.py

2. Utilisez la commande suivante pour démarrer le travail MapReduce.

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input wasbs:///example/data/gutenberg/davinci.txt -output wasbs:///example/wordcountout

    Cette commande compose les composants suivants :

    * **Hadoop streaming.jar**: utilisé lors de la diffusion en continu MapReduce opérations. Il s’interface Hadoop avec le code MapReduce externe que vous avez défini.

    * **-fichiers**: Hadoop indique que les fichiers spécifiés sont nécessaires pour cette tâche MapReduce, et ils doivent être copiés sur tous les nœuds de travail.

    * **-mapper**: indique Hadoop le fichier à utiliser en tant que la mapper.

    * **-réducteur**: indique Hadoop le fichier à utiliser en tant que du réducteur.

    * **-Saisie**: le fichier d’entrée que nous devons compter les mots à partir de.

    * **-sortie**: le répertoire la sortie est écrites dans.

        > [AZURE.NOTE] Ce répertoire est créé par la tâche.

Que doit quelques instructions **informations** en tant que la tâche démarre et enfin voir l’opération de **carte** et **réduire** affichée sous forme de pourcentage.

    15/02/05 19:01:04 INFO mapreduce.Job:  map 0% reduce 0%
    15/02/05 19:01:16 INFO mapreduce.Job:  map 100% reduce 0%
    15/02/05 19:01:27 INFO mapreduce.Job:  map 100% reduce 100%

Vous recevrez les informations d’état sur la tâche lorsqu’elle est terminée.

##<a name="view-the-output"></a>Afficher les résultats

Lorsque la tâche est terminée, utilisez la commande suivante pour afficher le résultat :

    hdfs dfs -text /example/wordcountout/part-00000

Cela doit s’afficher une liste de mots et le nombre de fois le mot s’est produite. Voici un exemple des données de sortie :

    wrenching       1
    wretched        6
    wriggling       1
    wrinkled,       1
    wrinkles        2
    wrinkling       2

##<a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris à utiliser la diffusion en continu MapRedcue travaux avec HDInsight, utilisez les liens suivants pour Explorer d’autres façons de travailler avec Azure HDInsight.

* [Utiliser Hive avec HDInsight](hdinsight-use-hive.md)
* [Utiliser cochon avec HDInsight](hdinsight-use-pig.md)
* [Utiliser des travaux MapReduce avec HDInsight](hdinsight-use-mapreduce.md)
