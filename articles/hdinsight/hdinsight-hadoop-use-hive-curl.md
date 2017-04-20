<properties
   pageTitle="Utiliser Hadoop Hive bouclé dans HDInsight | Microsoft Azure"
   description="Apprenez à soumettre à distance des travaux de cochon à HDInsight à l’aide d’ourlée."
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
   ms.date="09/07/2016"
   ms.author="larryfr"/>

#<a name="run-hive-queries-with-hadoop-in-hdinsight-with-curl"></a>Exécuter des requêtes Hive avec Hadoop dans HDInsight bouclé

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Dans ce document, vous allez apprendre à utiliser ourlée pour exécuter des requêtes Hive sur un Hadoop sur cluster Azure HDInsight.

Curl est utilisé pour montrer comment vous pouvez interagir avec HDInsight à l’aide de demandes HTTP brutes pour exécuter, surveiller et extraire les résultats de requêtes Hive. Cette méthode fonctionne à l’aide de l’API de reste WebHCat (auparavant appelé Templeton) fournie par votre cluster HDInsight.

> [AZURE.NOTE] Si vous êtes déjà familiarisé avec l’utilisation de serveurs basés sur Linux Hadoop, mais sont nouvelles à HDInsight, voir [ce que vous devez savoir sur Hadoop sur HDInsight basé sur Linux](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Conditions préalables

Pour effectuer les étapes décrites dans cet article, vous devez les éléments suivants :

* Un Hadoop sur cluster HDInsight (Linux ou fonctionnant sous Windows)

* [Coin](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a id="curl"></a>Exécuter des requêtes Hive à l’aide de Curl

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

    Début de l’URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, sera la même pour toutes les requêtes. Le chemin d’accès, **/status**, indique que la requête doit renvoyer un état WebHCat (également appelé Templeton) pour le serveur. Vous pouvez également demander la version de Hive à l’aide de la commande suivante :

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive

    Cela doit renvoyer une réponse similaire à ce qui suit :

        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}

2. Utilisez ce qui suit pour créer une nouvelle table nommée **log4jLogs**:

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="set+hive.execution.engine=tez;DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'wasbs:///example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="wasbs:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    Les paramètres utilisés dans cette commande sont les suivantes :

    * **-d** - dans la mesure où `-G` n’est pas utilisée, la requête par défaut, la méthode POST. `-d`Spécifie les valeurs de données qui sont envoyées avec la demande.

        * **User.nom** - l’utilisateur qui exécute la commande.

        * **exécuter** - HiveQL les instructions à exécuter.

        * **statusdir** - le répertoire le statut de cette tâche est écrites dans.

    Ces instructions effectuent les opérations suivantes :

    * **DROP TABLE** - supprime la table et le fichier de données, si la table existe déjà.

    * **Créer une TABLE externe** - crée une nouvelle table « externe » dans Hive. Tables externes stockent uniquement la définition de table dans Hive. Les données restent dans l’emplacement d’origine.

        > [AZURE.NOTE] Tables externes doivent être utilisés lorsque vous pensez que les données sous-jacentes pour être mis à jour par une source externe, par exemple, un processus de téléchargement automatique des données, ou par une autre opération MapReduce, mais toujours Hive requêtes à utiliser les données les plus récentes.
        >
        > Suppression d’une table externe signifie **pas** supprimer les données, la définition de la table.

    * **FORMAT de ligne** indique la ruche mise en forme les données. Dans ce cas, les champs dans chaque journal sont séparées par un espace.

    * **Stockées en tant que fichier texte emplacement** - indique la ruche où les données sont stockées (le répertoire de données d’exemple), et qu’il est stocké en tant que texte.

    * **Sélectionnez** - sélectionne un compteur de toutes les lignes dont la colonne **t4** contient la valeur **[erreur]**. Cela doit renvoyer une valeur de **3** qu’il sont a trois lignes contenant cette valeur.

    > [AZURE.NOTE] Notez que les espaces entre les instructions HiveQL sont remplacées par les `+` lorsqu’il est utilisé bouclé de caractères. Les valeurs entre guillemets contenant un espace, telles que le délimiteur ne doivent pas être remplacés par `+`.

    * **INPUT__FILE__NAME comme '% 25.log'** - présent limite la recherche seulement utiliser les fichiers se terminant. journal. Si ce n’est pas présent, Hive tentera de rechercher tous les fichiers dans ce répertoire et ses sous-répertoires, y compris les fichiers qui ne correspondent pas au schéma de colonne défini pour cette table.

    > [AZURE.NOTE] Notez que 25 % est l’URL codé formulaire %, afin que la condition réelle est `like '%.log'`. Le % doit être URL codée, comme il est considéré comme un caractère spécial dans les URL.

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

6. Utilisez les instructions suivantes pour créer une nouvelle table « interne » nommée **journaux d’erreurs**:

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="set+hive.execution.engine=tez;CREATE+TABLE+IF+NOT+EXISTS+errorLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+STORED+AS+ORC;INSERT+OVERWRITE+TABLE+errorLogs+SELECT+t1,t2,t3,t4,t5,t6,t7+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log';SELECT+*+from+errorLogs;" -d statusdir="wasbs:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    Ces instructions effectuent les opérations suivantes :

    * **Créer tableau IF NOT EXISTS** - crée une table, s’il n’existe pas déjà. Dans la mesure où le mot-clé **externes** n’est pas utilisé, il s’agit d’une table interne, qui est stockée dans le data warehouse Hive et est gérée complètement par Hive.

        > [AZURE.NOTE] Contrairement aux tables externes, vous supprimez une table interne pour supprimer ainsi que les données sous-jacentes.

    * **Stockées en tant que ORC** - stocke les données au format optimisé ligne en colonnes (ORC). Il s’agit d’un format hautement optimisé et efficace pour le stockage de données Hive.
    * REMPLACER **Insertion... Sélectionnez** - sélectionne les lignes dans la table **log4jLogs** qui contiennent **[erreur]**, puis insère les données dans la table des **journaux d’erreurs** .
    * **Sélectionnez** - sélectionne toutes les lignes de la nouvelle table **journaux d’erreurs** .

7. Utilisez l’ID du travail renvoyée pour vérifier l’état de la tâche. Une fois qu’il a réussi, utilisez Azure infrastructure du langage commun pour Mac, Linux et Windows comme décrit précédemment pour télécharger et afficher les résultats. La sortie doit contenir trois lignes, ce qui contiennent **[erreur]**.


##<a id="summary"></a>Résumé

Comme illustré dans ce document, vous pouvez utiliser une requête HTTP brute pour exécuter, surveiller et afficher les résultats des travaux Hive sur votre cluster HDInsight.

Pour plus d’informations sur l’interface reste utilisée dans cet article, consultez la <a href="https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference" target="_blank">WebHCat référence</a>.

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur Hive avec HDInsight :

* [Utiliser Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)

Pour plus d’informations sur les autres façons, vous pouvez travailler avec Hadoop sur HDInsight :

* [Utiliser cochon avec Hadoop sur HDInsight](hdinsight-use-pig.md)

* [Utiliser MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

Si vous utilisez Tez avec Hive, consultez les documents suivants pour les informations de débogage :

* [Utiliser l’interface utilisateur Tez sur HDInsight fonctionnant sous Windows](hdinsight-debug-tez-ui.md)

* [Utiliser l’affichage Ambari Tez sur HDInsight basé sur Linux](hdinsight-debug-ambari-tez-view.md)

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


