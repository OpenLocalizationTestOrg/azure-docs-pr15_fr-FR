<properties
    pageTitle="Soumettre des travaux d’explosion à distance en utilisant Livy | Microsoft Azure"
    description="Découvrez comment utiliser Livy avec clusters HDInsight à soumettre des travaux d’explosion à distance."
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/28/2016"
    ms.author="nitinme"/>


# <a name="submit-spark-jobs-remotely-to-an-apache-spark-cluster-on-hdinsight-linux-using-livy"></a>Soumettre des travaux d’explosion à distance à un cluster explosion Apache sur HDInsight Linux à l’aide de Livy

Cluster explosion Apache sur Azure HDInsight inclut Livy, une interface REST pour soumettre des tâches à distance à un cluster explosion. Pour des informations détaillées, consultez [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server).

Vous pouvez utiliser Livy d’exécuter explosion interactif shell ou soumettre des travaux lot à être exécuté sur explosion. Cet article traite de l’utilisation de Livy pour envoyer des lots. La syntaxe ci-dessous utilise ourlée pour émettre des appels reste au point de terminaison Livy.

**Conditions requises :**

Vous devez disposer des éléments suivants :

- Un abonnement Azure. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster explosion Apache sur HDInsight Linux. Pour plus d’informations, voir [groupes de créer Apache explosion dans Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="submit-a-batch-job-the-cluster"></a>Soumettre un cluster par lots

Avant d’envoyer un traitement par lots, vous devez télécharger le fichier jar application sur le stockage de cluster associé au cluster. Vous pouvez utiliser [**AzCopy**](../storage/storage-use-azcopy.md), un utilitaire de ligne de commande pour le faire. Il existe un grand nombre d’autres clients, que vous pouvez utiliser pour télécharger des données. Pour en savoir plus sur les à [télécharger des données pour les projets Hadoop de HDInsight](hdinsight-upload-data.md).

    curl -k --user "<hdinsight user>:<user password>" -v -H <content-type> -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches'

**Exemples**:

* Si le fichier jar se trouve sur le stockage de cluster (WASB)

        curl -k --user "admin:mypassword1!" -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches"

* Si vous souhaitez passer le nom de fichier jar et la classe dans le cadre d’un fichier d’entrée (dans cet exemple, input.txt)

        curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

## <a name="get-information-on-batches-running-on-the-cluster"></a>Obtenir des informations sur les lots en cours d’exécution sur le cluster

    curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"

**Exemples**:

* Si vous voulez extraire tous les lots en cours d’exécution sur le cluster :

        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"

* Si vous souhaitez récupérer un lot spécifique avec un batchId donnée

        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"


## <a name="delete-a-batch-job"></a>Supprimer un traitement par lots

    curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"

**Exemple**:

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="livy-and-high-availability"></a>Livy et disponibilité

Livy prévoit disponibilité explosion travaux s’exécutant sur le cluster. Voici quelques exemples.

* Si le service Livy s’arrête après l’avoir envoyé une tâche à distance à un cluster explosion, le travail continue à s’exécuter en arrière-plan. Lorsque Livy est sauvegarder, il restaure l’état de la tâche et rapports de nouveau.

* Blocs-notes Jupyter pour HDInsight sont optimisés par Livy dans le système principal. Si un bloc-notes est en cours d’exécution un travail explosion et le service Livy obtient redémarré, le bloc-notes continuent à exécuter les cellules de code. 

## <a name="show-me-an-example"></a>Afficher un exemple

Dans cette section, nous examinons exemples sur l’utilisation Livy pour remplir une demande d’explosion, surveiller l’avancement de l’application, puis supprimer le travail. L’application que nous utilisons dans cet exemple est le périphérique de développé dans l’article [créer une application Scala autonome et à exécuter sur cluster explosion HDInsight](hdinsight-apache-spark-create-standalone-application.md). Les étapes ci-dessous supposent ce qui suit :

* Vous avez déjà copié sur le fichier jar application sur le compte de stockage associé au cluster.
* Vous avez ourlée installée sur l’ordinateur que vous essayez de ces étapes.

Procédez comme suit.

1. Laissez-nous Vérifiez d’abord que Livy s’exécute sur le cluster. Pour ce faire, nous pouvons obtenir une liste des lots en cours d’exécution. Si c’est la première fois que vous exécutez une tâche à l’aide de Livy, cela doit retourner zéro.

        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"

    Vous devez obtenir un résultat semblable à ce qui suit :

        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:47:53 GMT
        < Content-Length: 34
        <
        {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

    Notez comment la dernière ligne de la sortie indique **total : 0**, ce qui ne suggère aucun lot en cours d’exécution.

2. Laissez-nous désormais soumettre un traitement par lots. L’extrait de code ci-dessous utilise un fichier d’entrée (input.txt) pour passer le nom de fichier jar et le nom du cours en tant que paramètres. Il s’agit de l’approche recommandée si vous exécutez ces étapes d’un ordinateur Windows.

        curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

    Les paramètres dans fichier **input.txt** sont définies comme suit :

        { "file":"wasbs:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }

    Vous devriez voir un résultat semblable à ce qui suit :

        < HTTP/1.1 201 Created
        < Content-Type: application/json; charset=UTF-8
        < Location: /0
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:51:30 GMT
        < Content-Length: 36
        <
        {"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

    Notez la façon dont la dernière ligne de la sortie indique **état : démarrage**. Également indiqué, **id : 0**. Il s’agit de l’ID du lot.

3. Vous pouvez maintenant récupérer la l’état de ce lot spécifique à l’aide de l’ID du lot.

        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/0"

    Vous devriez voir un résultat semblable à ce qui suit :

        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:54:42 GMT
        < Content-Length: 509
        <
        {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://hn0-myspar.lpel1gnnvxne3gwzqkfq5u5uzh.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

    Le résultat affiche désormais **état : succès**qui suggère que le travail a réussi.

4. Si vous le souhaitez, vous pouvez maintenant supprimer le lot.

        curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/0"

    Vous devriez voir un résultat semblable à ce qui suit :

        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Sat, 21 Nov 2015 18:51:54 GMT
        < Content-Length: 17
        <
        {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

    La dernière ligne de la sortie indique que le lot a été supprimé. Si vous supprimez une tâche en cours d’exécution, il va essentiellement arrêter la tâche. Si vous supprimez une tâche est terminée avec succès ou dans le cas contraire, il supprime complètement les informations du projet.

## <a name="seealso"></a>Voir aussi


* [Vue d’ensemble : Apache explosion sur Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scénarios

* [Explosion avec BI : effectuer une analyse de données interactives à l’aide d’explosion dans HDInsight avec les outils de décisionnel](hdinsight-apache-spark-use-bi-tools.md)

* [Explosion avec apprentissage automatique : utilisation explosion dans HDInsight pour analyser la température de construction à l’aide de données HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Explosion avec apprentissage automatique : utilisation explosion dans HDInsight pour prévoir des résultats de l’inspection alimentaires](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Diffusion en continu explosion : Utilisation explosion dans HDInsight pour la création d’applications en continu en temps réel](hdinsight-apache-spark-eventhub-streaming.md)

* [Analyse de journal de site Web à l’aide d’explosion dans HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Créer et exécuter des applications

* [Créer une application autonome à l’aide de Scala](hdinsight-apache-spark-create-standalone-application.md)

### <a name="tools-and-extensions"></a>Outils et extensions

* [Plug-in des outils HDInsight IntelliJ idée permet de créer et soumettre des applications Scala d’explosion](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Plug-in utilisation HDInsight outils idée IntelliJ déboguer applications explosion à distance](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Utiliser les blocs-notes Zeppelin avec un cluster explosion sur HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Noyaux disponibles pour Jupyter bloc-notes cluster explosion pour HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Utiliser les packages externes avec Jupyter blocs-notes](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Installer Jupyter sur votre ordinateur et vous connecter à un cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gérer les ressources

* [Gestion des ressources pour le cluster Apache explosion dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Suivre et débogage travaux s’exécutant sur un cluster Apache explosion dans HDInsight](hdinsight-apache-spark-job-debugging.md)
