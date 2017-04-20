<properties
   pageTitle="Utiliser Hadoop Sqoop bouclé dans HDInsight | Microsoft Azure"
   description="Apprenez à soumettre à distance des travaux de Sqoop à HDInsight à l’aide d’ourlée."
   services="hdinsight"
   documentationCenter=""
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/21/2016"
   ms.author="jgao"/>

#<a name="run-sqoop-jobs-with-hadoop-in-hdinsight-with-curl"></a>Exécuter des travaux Sqoop avec Hadoop dans HDInsight bouclé

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Apprenez à utiliser ourlée pour exécuter des travaux de Sqoop sur un cluster Hadoop dans HDInsight.

Curl est utilisé pour montrer comment vous pouvez interagir avec HDInsight à l’aide de demandes HTTP brutes pour exécuter, surveiller et extraire les résultats des travaux Sqoop. Cette méthode fonctionne à l’aide de l’API de reste WebHCat (auparavant appelé Templeton) fournie par votre cluster HDInsight.

> [AZURE.NOTE] Si vous êtes déjà familiarisé avec l’utilisation de serveurs basés sur Linux Hadoop, mais sont nouvelles à HDInsight, voir [informations sur l’utilisation de HDInsight sous Linux](hdinsight-hadoop-linux-information.md).

##<a name="prerequisites"></a>Conditions préalables

Pour effectuer les étapes décrites dans cet article, vous devez les éléments suivants :

* Un Hadoop sur cluster HDInsight (Linux ou fonctionnant sous Windows)

* [Coin](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a name="submit-sqoop-jobs-by-using-curl"></a>Soumettre des travaux de Sqoop à l’aide de Curl

> [AZURE.NOTE] Lorsque vous utilisez recourbées ou toute autre communication reste avec WebHCat, vous devez vous authentifier les demandes en fournissant le nom d’utilisateur et mot de passe pour l’administrateur de cluster HDInsight. Vous devez également utiliser le nom du cluster dans le cadre de la ressource identificateur URI (Uniform) utilisé pour envoyer les demandes sur le serveur.
>
> Pour les commandes dans cette section, remplacez le **nom d’utilisateur** par l’utilisateur s’authentifier au cluster et remplacez le **mot de passe** par le mot de passe pour le compte d’utilisateur. Remplacez **NOMDUCLUSTER** par le nom de votre cluster.
>
> L’API REST est sécurisé via [l’authentification de base](http://en.wikipedia.org/wiki/Basic_access_authentication). Vous devez toujours vous demandes à l’aide de HTTP sécurisé (HTTPS) pour vous assurer que vos informations d’identification sont envoyées en toute sécurité sur le serveur.

1. À partir d’une ligne de commande, utilisez la commande suivante pour vérifier que vous pouvez vous connecter à votre cluster HDInsight :

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Vous recevrez une réponse similaire à ce qui suit :

        {"status":"ok","version":"v1"}

    Les paramètres utilisés dans cette commande sont les suivantes :

    * **-u** - le nom d’utilisateur et mot de passe utilisés pour authentifier la demande.
    * **+ G** - indique qu’il s’agit d’une requête GET.

    Début de l’URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, sera la même pour toutes les requêtes. Le chemin d’accès, **/status**, indique que la requête doit renvoyer un état WebHCat (également appelé Templeton) pour le serveur. 

2. Utilisez ce qui suit pour soumettre une tâche de sqoop :


        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /tutorials/usesqoop/data --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasbs:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop

    Les paramètres utilisés dans cette commande sont les suivantes :

    * **-d** - dans la mesure où `-G` n’est pas utilisée, la requête par défaut, la méthode POST. `-d`Spécifie les valeurs de données qui sont envoyées avec la demande.

        * **User.nom** - l’utilisateur qui exécute la commande.

        * **commande** - Sqoop la commande à exécuter.

        * **statusdir** - le répertoire le statut de cette tâche est écrites dans.

    Cette commande doit renvoyer un ID de tâche qui peut être utilisé pour vérifier l’état de la tâche.

        {"id":"job_1415651640909_0026"}

3. Pour vérifier l’état de la tâche, utilisez la commande suivante. Remplacez **identificateur** par la valeur renvoyée dans l’étape précédente. Par exemple, si la valeur de retour a été `{"id":"job_1415651640909_0026"}`, puis **identificateur** serait `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

    Si la tâche est terminée, l’état sera **a réussi**.

    > [AZURE.NOTE] Cette demande ourlée renvoie un document Notation JSON (JavaScript Object) avec les informations relatives au projet ; jq est utilisée pour récupérer uniquement la valeur d’état.

4. Une fois que l’état de la tâche est devenu **a réussi**, vous pouvez récupérer les résultats de la tâche à partir du stockage Blob Azure. La `statusdir` paramètre passé avec la requête contient l’emplacement du fichier de sortie ; Dans ce cas, **wasbs : / / exemple/ourlée**. Cette adresse stocke la sortie de la tâche dans le répertoire **exemple/ourlée** sur le conteneur de stockage par défaut utilisé par votre cluster HDInsight.

    Vous pouvez répertorier et télécharger ces fichiers à l’aide de l' [Infrastructure du langage commun Azure](../xplat-cli-install.md). Par exemple, pour répertorier les fichiers d' **exemple/ourlée**, utilisez la commande suivante :

        azure storage blob list <container-name> example/curl

    Pour télécharger un fichier, procédez comme suit :

        azure storage blob download <container-name> <blob-name> <destination-file>

    > [AZURE.NOTE] Vous devez spécifier le nom du compte de stockage qui contient le blob à l’aide de la `-a` et `-k` paramètres ou définir la **AZURE\_stockage\_compte** et **AZURE\_stockage\_ACCESS\_clé** variables d’environnement. Voir < un href = « hdinsight-téléchargement-data.md » target = « _blank » pour plus d’informations.

##<a name="limitations"></a>Limitations

* Exportation en bloc - basé sur Linux avec HDInsight, le connecteur Sqoop permet d’exporter des données vers Microsoft SQL Server ou de base de données SQL Azure ne prend pas en charge les insertions en bloc.

* Le traitement par lots - avec basé sur Linux HDInsight, lorsque vous utilisez la `-batch` commutateur lors de l’exécution d’insertions, Sqoop effectue plusieurs insertions au lieu du traitement par lots les opérations d’insertion.

##<a name="summary"></a>Résumé

Comme illustré dans ce document, vous pouvez utiliser une requête HTTP brute pour exécuter, surveiller et afficher les résultats des travaux Sqoop sur votre cluster HDInsight.

Pour plus d’informations sur l’interface reste utilisée dans cet article, consultez le <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">guide de l’API REST Sqoop</a>.

##<a name="next-steps"></a>Étapes suivantes

Pour obtenir des informations générales sur Hive avec HDInsight :

* [Utiliser Sqoop avec Hadoop sur HDInsight](hdinsight-use-sqoop.md)

Pour plus d’informations sur les autres façons, vous pouvez travailler avec Hadoop sur HDInsight :

* [Utiliser Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)

* [Utiliser cochon avec Hadoop sur HDInsight](hdinsight-use-pig.md)

* [Utiliser MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md




[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


