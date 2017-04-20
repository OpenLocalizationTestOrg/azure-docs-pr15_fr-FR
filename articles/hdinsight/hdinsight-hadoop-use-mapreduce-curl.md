<properties
   pageTitle="Utiliser MapReduce et ourlée avec Hadoop dans HDInsight | Microsoft Azure"
   description="Découvrez comment effectuer à distance MapReduce travaux avec Hadoop sur HDInsight à l’aide d’ourlée."
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

#<a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-curl"></a>Exécution des tâches MapReduce avec Hadoop sur HDInsight à l’aide de Curl

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Dans ce document, vous allez apprendre à utiliser ourlée pour exécuter des travaux de MapReduce sur un Hadoop sur cluster HDInsight.

Curl est utilisé pour montrer comment vous pouvez interagir avec HDInsight en utilisant les demandes HTTP brutes pour exécuter des travaux MapReduce. Cette méthode fonctionne à l’aide de l’API de reste WebHCat (auparavant appelé Templeton) fournie par votre cluster HDInsight.

> [AZURE.NOTE] Si vous êtes déjà familiarisé avec l’utilisation de serveurs basés sur Linux Hadoop, mais vous ne connaissez pas HDInsight, voir [ce que vous devez savoir sur basé sur Linux Hadoop sur HDInsight](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Conditions préalables

Pour effectuer les étapes décrites dans cet article, vous devez les éléments suivants :

* Un Hadoop sur cluster HDInsight (Linux ou fonctionnant sous Windows)

* [Coin](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a id="curl"></a>Exécuter des travaux MapReduce à l’aide de Curl

> [AZURE.NOTE] Lorsque vous utilisez recourbées ou toute autre communication reste avec WebHCat, vous devez vous authentifier les demandes en spécifiant le nom d’utilisateur HDInsight cluster administrateur et mot de passe. Vous devez également utiliser le nom du cluster dans le cadre de l’URI qui est utilisé pour envoyer les demandes sur le serveur.
>
> Pour les commandes dans cette section, remplacez le **nom d’utilisateur** par l’utilisateur pour l’authentification pour le cluster et le **mot de passe** avec le mot de passe pour le compte d’utilisateur. Remplacez **NOMDUCLUSTER** par le nom de votre cluster.
>
> L’API REST est sécurisé à l’aide de [l’authentification d’accès de base](http://en.wikipedia.org/wiki/Basic_access_authentication). Vous devez toujours vous demandes en utilisant HTTPS pour vous assurer que vos informations d’identification sont envoyées en toute sécurité sur le serveur.

1. À partir d’une ligne de commande, utilisez la commande suivante pour vérifier que vous pouvez vous connecter à votre cluster HDInsight :

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Vous recevrez une réponse similaire à ce qui suit :

        {"status":"ok","version":"v1"}

    Les paramètres utilisés dans cette commande sont les suivantes :

    * **-u**: indique le nom d’utilisateur et mot de passe utilisés pour authentifier la demande
    * **+ G**: indique qu’il s’agit d’une requête GET

    Début de l’URI, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, est la même pour toutes les requêtes.

2. Pour envoyer un travail MapReduce, utilisez la commande suivante :

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d jar=wasbs:///example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=wasbs:///example/data/gutenberg/davinci.txt -d arg=wasbs:///example/data/CurlOut https://CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar

    La fin de l’URI (/ mapreduce/jar) indique WebHCat que cette demande démarre un travail MapReduce à partir d’une classe dans un fichier jar. Les paramètres utilisés dans cette commande sont les suivantes :

    * **-d**: `-G` n’est pas utilisée, afin que la requête par défaut, la méthode POST. `-d`Spécifie les valeurs de données qui sont envoyées avec la demande.

        * **User.nom**: l’utilisateur qui exécute la commande
        * **JAR**: l’emplacement du fichier jar qui contient les cours pour être exécuté
        * **cours**: la classe qui contient la logique MapReduce
        * **argument**: les arguments à transmettre à la tâche MapReduce ; Dans ce cas, le fichier d’entrée de texte et le répertoire qui sont utilisés pour la sortie

    Cette commande doit renvoyer un ID de tâche qui peut être utilisé pour vérifier l’état de la tâche :

        {"id":"job_1415651640909_0026"}

3. Pour vérifier l’état de la tâche, utilisez la commande suivante. Remplacez l' **identificateur** par la valeur renvoyée dans l’étape précédente. Par exemple, si la valeur de retour a été `{"id":"job_1415651640909_0026"}`, puis l’identificateur serait `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

    Si la tâche est terminée, l’état sera être « a réussi ».

    > [AZURE.NOTE] Cette demande ourlée renvoie un document JSON contenant les informations relatives au projet ; jq est utilisée pour récupérer uniquement la valeur d’état.

4. Lorsque l’état de la tâche est devenu **a réussi**, vous pouvez récupérer les résultats de la tâche à partir du stockage Blob Azure. La `statusdir` paramètre passé avec la requête contient l’emplacement du fichier de sortie ; Dans ce cas, **wasbs : / / exemple/ourlée**. Cette adresse stocke la sortie de la tâche dans le répertoire **exemple/ourlée** dans le conteneur de stockage par défaut utilisé par votre cluster HDInsight.

Vous pouvez répertorier et télécharger ces fichiers à l’aide de l' [Infrastructure du langage commun Azure](../xplat-cli-install.md). Par exemple, pour répertorier les fichiers dans l' **exemple/ourlée**, utilisez la commande suivante :

    azure storage blob list <container-name> example/curl

Pour télécharger un fichier, procédez comme suit :

    azure storage blob download <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] Vous devez spécifier le nom de compte de stockage qui contient le blob à l’aide de la `-a` et `-k` paramètres ou définir la **AZURE\_stockage\_compte** et **AZURE\_stockage\_ACCESS\_clé** variables d’environnement. Découvrez [comment télécharger des données à HDInsight](hdinsight-upload-data.md) pour plus d’informations.

##<a id="summary"></a>Résumé

Comme illustré dans ce document, vous pouvez utiliser la requête brute HTTP pour exécuter, surveiller et afficher les résultats des travaux Hive dans votre cluster HDInsight.

Pour plus d’informations sur l’interface REST qui est utilisée dans cet article, consultez la [WebHCat référence](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur les tâches MapReduce dans HDInsight :

* [Utiliser MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

Pour plus d’informations sur les autres méthodes, vous pouvez travailler avec Hadoop sur HDInsight :

* [Utiliser Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)

* [Utiliser cochon avec Hadoop sur HDInsight](hdinsight-use-pig.md)
