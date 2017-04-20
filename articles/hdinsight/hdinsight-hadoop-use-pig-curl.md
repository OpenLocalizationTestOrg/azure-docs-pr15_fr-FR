<properties
   pageTitle="Utiliser Hadoop cochon bouclé dans HDInsight | Microsoft Azure"
   description="Apprenez à utiliser ourlée pour exécuter des travaux de cochon Latin sur un cluster Hadoop dans Azure HDInsight."
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

#<a name="run-pig-jobs-with-hadoop-on-hdinsight-by-using-curl"></a>Exécution de tâches cochon avec Hadoop sur HDInsight à l’aide de Curl

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Dans ce document, vous allez apprendre à utiliser ourlée pour exécuter des travaux de cochon Latin sur un cluster Azure HDInsight. Le langage de programmation cochon Latin vous permet de décrire les transformations qui s’appliquent aux données d’entrée pour générer la sortie désirée.

Curl est utilisé pour montrer comment vous pouvez interagir avec HDInsight à l’aide de demandes HTTP brutes pour exécuter, surveiller et extraire les résultats des travaux cochon. Cette méthode fonctionne à l’aide de la WebHCat l’API REST (auparavant appelé Templeton) qui est fourni par votre cluster HDInsight.

> [AZURE.NOTE] Si vous êtes déjà familiarisé avec l’utilisation de serveurs basés sur Linux Hadoop, mais sont nouvelles à HDInsight, voir [Conseils de HDInsight basé sur Linux](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Conditions préalables

Pour effectuer les étapes décrites dans cet article, vous devez les éléments suivants :

* Un cluster Azure HDInsight (Hadoop sur HDInsight) (basé sur Linux ou fonctionnant sous Windows)

* [Coin](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a id="curl"></a>Exécuter des travaux cochon à l’aide d’ourlée

> [AZURE.NOTE] Lorsque vous utilisez recourbées ou toute autre communication reste avec WebHCat, vous devez vous authentifier les demandes en fournissant le nom d’utilisateur administrateur et le mot de passe pour le cluster HDInsight. Vous devez également utiliser le nom du cluster dans le cadre de la ressource identificateur URI (Uniform) qui est utilisé pour envoyer les demandes sur le serveur.
>
> Pour les commandes dans cette section, remplacez le **nom d’utilisateur** par l’utilisateur s’authentifier au cluster et remplacez le **mot de passe** par le mot de passe pour le compte d’utilisateur. Remplacez **NOMDUCLUSTER** par le nom de votre cluster.
>
> L’API REST est sécurisé via [l’authentification d’accès de base](http://en.wikipedia.org/wiki/Basic_access_authentication). Vous devez toujours vous demandes à l’aide de HTTP sécurisé (HTTPS) pour vous assurer que vos informations d’identification sont envoyées en toute sécurité sur le serveur.

1. À partir d’une ligne de commande, utilisez la commande suivante pour vérifier que vous pouvez vous connecter à votre cluster HDInsight :

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Vous recevrez une réponse similaire à ce qui suit :

        {"status":"ok","version":"v1"}

    Les paramètres utilisés dans cette commande sont les suivantes :

    * **-u**: nom d’utilisateur et mot de passe utilisés pour authentifier la demande
    * **+ G**: indique qu’il s’agit d’une requête GET

    Début de l’URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, sera la même pour toutes les requêtes. Le chemin d’accès, **/status**, indique que la requête doit renvoyer l’état du WebHCat (également appelé Templeton) pour le serveur.

2. Soumettre un travail cochon Latin pour le cluster, utilisez le code suivant :

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'wasbs:///example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="wasbs:///example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig

    Les paramètres utilisés dans cette commande sont les suivantes :

    * **-d**: car `-G` n’est pas utilisée, la requête par défaut, la méthode POST. `-d`Spécifie les valeurs de données qui sont envoyées avec la demande.

        * **User.nom**: l’utilisateur qui exécute la commande
        * **exécuter**: Latin cochon les instructions à exécuter
        * **statusdir**: le répertoire le statut de cette tâche est écrites dans

    > [AZURE.NOTE] Notez que les espaces dans les instructions cochon Latin sont remplacées par les `+` lorsqu’il est utilisé bouclé de caractères.

    Cette commande doit renvoyer un ID de tâche qui peut être utilisé pour vérifier l’état de la tâche, par exemple :

        {"id":"job_1415651640909_0026"}

3. Pour vérifier l’état de la tâche, utilisez la commande suivante. Remplacez **identificateur** par la valeur renvoyée dans l’étape précédente. Par exemple, si la valeur de retour a été `{"id":"job_1415651640909_0026"}`, puis **identificateur** serait `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

    Si la tâche est terminée, l’état sera **a réussi**.

    > [AZURE.NOTE] Cette demande ourlée renvoie un document Notation JSON (JavaScript Object) des informations sur la tâche et jq est utilisée pour récupérer uniquement la valeur d’état.

##<a id="results"></a>Afficher les résultats

Lorsque l’état de la tâche est devenu **a réussi**, vous pouvez récupérer les résultats de la tâche à partir du stockage Blob Azure. La `statusdir` paramètre passé avec la requête contient l’emplacement du fichier de sortie ; Dans ce cas, **wasbs : / / exemple/pigcurl**. Cette adresse stocke la sortie de la tâche dans le répertoire **exemple/pigcurl** dans le conteneur de stockage par défaut utilisé par votre cluster HDInsight.

Vous pouvez répertorier et télécharger ces fichiers à l’aide de l' [Infrastructure du langage commun Azure](../xplat-cli-install.md). Par exemple, pour répertorier les fichiers d' **exemple/pigcurl**, utilisez la commande suivante :

    azure storage blob list <container-name> example/pigcurl

Pour télécharger un fichier, procédez comme suit :

    azure storage blob download <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] Vous devez spécifier le nom de compte de stockage qui contient le blob à l’aide de la `-a` et `-k` paramètres ou définir la **AZURE\_stockage\_compte** et **AZURE\_stockage\_ACCESS\_clé** variables d’environnement.

##<a id="summary"></a>Résumé

Comme illustré dans ce document, vous pouvez utiliser une requête HTTP brute pour exécuter, surveiller et afficher les résultats des travaux cochon sur votre cluster HDInsight.

Pour plus d’informations sur l’interface REST utilisée dans cet article, consultez la [WebHCat référence](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur cochon sur HDInsight :

* [Utiliser cochon avec Hadoop sur HDInsight](hdinsight-use-pig.md)

Pour plus d’informations sur les autres méthodes, vous pouvez travailler avec Hadoop sur HDInsight :

* [Utiliser Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)

* [Utiliser MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)
