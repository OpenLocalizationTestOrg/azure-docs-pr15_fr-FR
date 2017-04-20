<properties
   pageTitle="Utiliser Hadoop Hive dans la Console de requête en HDInsight | Microsoft Azure"
   description="Découvrez comment utiliser la Console de requête basée sur le web pour exécuter des requêtes Hive sur un cluster HDInsight Hadoop depuis votre navigateur."
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
   ms.date="09/20/2016"
   ms.author="larryfr"/>

# <a name="run-hive-queries-using-the-query-console"></a>Exécuter des requêtes Hive à l’aide de la Console de requête

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Dans cet article, vous allez apprendre à utiliser la Console de requête HDInsight pour exécuter des requêtes Hive sur un cluster HDInsight Hadoop depuis votre navigateur.

> [AZURE.IMPORTANT] La Console de requête HDInsight n’est disponible sur les clusters HDInsight fonctionnant sous Windows. Si vous utilisez un cluster basé sur Linux HDInsight, voir [ruche exécuter des requêtes à l’aide de l’affichage de la ruche](hdinsight-hadoop-use-hive-ambari-view.md).


##<a id="prereq"></a>Conditions préalables

Pour effectuer les étapes décrites dans cet article, vous devez les éléments suivants.

* Un cluster fonctionnant sous Windows HDInsight Hadoop

* Un navigateur web moderne

##<a id="run"></a>Exécuter des requêtes Hive à l’aide de la Console de requête

1. Ouvrez un navigateur web et accédez à __https://CLUSTERNAME.azurehdinsight.net__, où __CLUSTERNAME__ est le nom de votre cluster HDInsight. Si vous y êtes invité, entrez le nom d’utilisateur et mot de passe que vous avez utilisé lorsque vous avez créé le cluster.


2. Dans les liens situés en haut de la page, sélectionnez **Éditeur ruche**. Cela permet d’afficher un formulaire qui peut être utilisé pour entrer les instructions HiveQL que vous souhaitez exécuter dans le cluster HDInsight.

    ![l’éditeur hive](./media/hdinsight-hadoop-use-hive-query-console/queryconsole.png)

    Remplacez le texte `Select * from hivesampletable` avec les instructions HiveQL suivantes :

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Ces instructions effectuent les opérations suivantes :

    * **DROP TABLE**: supprime la table et le fichier de données si la table existe déjà.
    * **Créer une TABLE externe**: crée une nouvelle table « externe » dans Hive. Tables externes stockent uniquement la définition de table dans Hive ; les données restent dans l’emplacement d’origine.

    > [AZURE.NOTE] Tables externes doivent être utilisés lorsque vous pensez que les données sous-jacentes mettre à jour par une source externe (par exemple, un processus de téléchargement automatique des données) ou par une autre opération MapReduce, mais vous voulez toujours Hive requêtes à utiliser les données les plus récentes.
    >
    > Suppression d’une table externe signifie **pas** supprimer les données, la définition de la table.

    * **FORMAT de ligne**: indique la façon dont les données sont formatées ruche. Dans ce cas, les champs dans chaque journal sont séparées par un espace.
    * **Stockées en tant que fichier texte emplacement**: indique la ruche où les données sont stockées (le répertoire de données d’exemple) et stockée en tant que texte
    * **Sélectionnez**: sélectionnez un compteur de toutes les lignes où colonne **t4** contient la valeur **[erreur]**. Cela doit renvoyer une valeur de **3** , car il existe trois lignes contenant cette valeur.
    * **INPUT__FILE__NAME comme '%.log'** - indique qui nous devons renvoient uniquement des données à partir de fichiers se terminant par ruche. journal. Cela limite la recherche dans le fichier exemple.log qui contient les données et conserve de renvoyer des données à partir d’autres exemple fichiers de données qui ne correspondent pas au schéma que nous avons définie.

2. Cliquez sur **Envoyer**. La **Session de travail** dans la partie inférieure de la page doit afficher les détails de la tâche.

3. Lorsque le champ **état** passe à **terminé**, sélectionnez **Afficher les détails** de la tâche. Dans la page Détails, la **Sortie des tâches** contient `[ERROR]   3`. Vous pouvez utiliser le bouton **Télécharger** sous ce champ pour télécharger un fichier qui contient le résultat de la tâche.


##<a id="summary"></a>Résumé

Comme vous pouvez le voir, la Console de requête fournit un moyen facile pour exécuter des requêtes Hive dans un cluster de HDInsight, surveiller l’état du travail et récupérer la sortie.

Pour en savoir plus sur l’utilisation de la Console de requête ruche pour exécuter les tâches Hive, sélectionnez **Mise en route** en haut de la Console de requête, puis utilisez les exemples sont fournis. Chaque exemple décrit le processus d’utilisation Hive pour analyser des données, notamment des explications sur les instructions HiveQL utilisées dans l’échantillon.

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur Hive dans HDInsight :

* [Utiliser Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)

Pour plus d’informations sur les autres méthodes, vous pouvez travailler avec Hadoop sur HDInsight :

* [Utiliser cochon avec Hadoop sur HDInsight](hdinsight-use-pig.md)

* [Utiliser MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

Si vous utilisez Tez avec Hive, consultez les documents suivants pour les informations de débogage :

* [Utiliser l’interface utilisateur Tez sur HDInsight fonctionnant sous Windows](hdinsight-debug-tez-ui.md)

* [Utiliser l’affichage Ambari Tez sur HDInsight basé sur Linux](hdinsight-debug-ambari-tez-view.md)

[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

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



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[Powershell-install-configure]: powershell-install-configure.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
