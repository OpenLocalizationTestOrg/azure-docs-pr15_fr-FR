<properties
   pageTitle="Utiliser Hadoop Hive et Bureau à distance dans HDInsight | Microsoft Azure"
   description="Découvrez comment vous connecter au cluster Hadoop en HDInsight à l’aide de bureau à distance et puis exécuter des requêtes Hive à l’aide de l’Interface de ligne de commande ruche."
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
   ms.date="09/06/2016"
   ms.author="larryfr"/>

# <a name="use-hive-with-hadoop-on-hdinsight-with-remote-desktop"></a>Utiliser Hive avec Hadoop sur HDInsight avec Bureau à distance

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Dans cet article, vous apprendre à vous connecter à un cluster HDInsight à l’aide de bureau à distance et puis exécuter des requêtes Hive à l’aide de l’Interface de ligne de commande ruche (CLI).

> [AZURE.NOTE] Ce document ne fournit pas une description détaillée de ce que font les instructions HiveQL qui sont utilisées dans les exemples. Pour plus d’informations sur la HiveQL est utilisée dans cet exemple, voir [Utiliser la ruche avec Hadoop sur HDInsight](hdinsight-use-hive.md).

##<a id="prereq"></a>Conditions préalables

Pour effectuer les étapes décrites dans cet article, vous devez les éléments suivants :

* Un cluster fonctionnant sous Windows HDInsight (Hadoop sur HDInsight)

* Un ordinateur client exécutant Windows 10, fenêtre 8 ou Windows 7

##<a id="connect"></a>Connexion Bureau à distance

Activer le Bureau à distance pour le cluster HDInsight, puis vous y connecter en suivant les instructions à [se connecter à clusters HDInsight à l’aide de RDP](hdinsight-administer-use-management-portal.md#rdp).

##<a id="hive"></a>Utilisez la commande Hive

Lorsque vous êtes connecté sur le bureau pour le cluster HDInsight, procédez comme suit pour l’utiliser avec Hive :

1. À partir du bureau HDInsight, commencer la **ligne de commande Hadoop**.

2. Entrez la commande suivante pour démarrer l’infrastructure du langage commun ruche :

        %hive_home%\bin\hive

    Lorsque l’infrastructure du langage commun a déjà commencé, vous verrez l’invite ruche infrastructure du langage commun : `hive>`.

3. À l’aide de l’infrastructure du langage commun, entrez les instructions suivantes pour créer une nouvelle table nommée **log4jLogs** à l’aide des exemples de données :

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Ces instructions effectuent les opérations suivantes :

    * **DROP TABLE**: supprime la table et le fichier de données si la table existe déjà.

    * **Créer une TABLE externe**: crée une nouvelle table « externe » dans Hive. Tables externes stockent uniquement la définition de table dans Hive (les données restent dans l’emplacement d’origine).

        > [AZURE.NOTE] Tables externes doivent être utilisés lorsque vous pensez que les données sous-jacentes mettre à jour par une source externe (par exemple, un processus de téléchargement automatique des données) ou par une autre opération MapReduce, mais vous voulez toujours Hive requêtes à utiliser les données les plus récentes.
        >
        > Suppression d’une table externe signifie **pas** supprimer les données, la définition de la table.

    * **FORMAT de ligne**: indique la façon dont les données sont formatées ruche. Dans ce cas, les champs dans chaque journal sont séparées par un espace.

    * **Stockées en tant que fichier texte emplacement**: indique la ruche où les données sont stockées (le répertoire de données d’exemple) et stockée en tant que texte.

    * **Sélectionnez**: sélectionne un compteur de toutes les lignes dont la colonne **t4** contient la valeur **[erreur]**. Cela doit renvoyer une valeur de **3** , car il existe trois lignes contenant cette valeur.

    * **INPUT__FILE__NAME comme '%.log'** - indique qui nous devons renvoient uniquement des données à partir de fichiers se terminant par ruche. journal. Cela limite la recherche dans le fichier exemple.log qui contient les données et conserve de renvoyer des données à partir d’autres exemple fichiers de données qui ne correspondent pas au schéma que nous avons définie.


4. Utilisez les instructions suivantes pour créer une nouvelle table « interne » nommée **journaux d’erreurs**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Ces instructions effectuent les opérations suivantes :

    * **Créer tableau IF NOT EXISTS**: crée un tableau s’il n’existe pas déjà. Étant donné que le mot clé **externes** n’est pas utilisé, il s’agit d’une table interne, qui est stockée dans le data warehouse Hive et est gérée complètement par Hive.

        > [AZURE.NOTE] Contrairement aux tables **externes** , supprimez une table interne supprime également les données sous-jacentes.

    * **Stockées en tant que ORC**: stocke les données ligne optimisée (ORC) sous forme de colonnes. Il s’agit d’un format hautement optimisé et efficace pour le stockage de données Hive.

    * REMPLACER **Insertion... Sélectionnez**: sélectionne les lignes de la table **log4jLogs** qui contiennent **[erreur]**, puis insère les données dans la table des **journaux d’erreurs** .

    Pour vérifier qu’uniquement les lignes qui contiennent des **[erreur]** dans la colonne t4 ont été stockées dans la table des **journaux d’erreurs** , utilisez l’instruction suivante pour renvoyer toutes les lignes des **journaux d’erreurs**:

        SELECT * from errorLogs;

    Trois lignes de données doit être retournée, contenant tous les **[erreur]** dans la colonne t4.

##<a id="summary"></a>Résumé

Comme vous pouvez le voir, la la commande Hive fournit un moyen facile de façon interactive exécuter des requêtes Hive sur un cluster de HDInsight, surveiller l’état du travail et récupérer la sortie.

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





[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[Powershell-install-configure]: ../powershell-install-configure.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

