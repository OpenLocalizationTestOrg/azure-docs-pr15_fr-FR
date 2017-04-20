<properties
   pageTitle="Requête avec des outils Hadoop la ruche pour Visual Studio | Microsoft Azure"
   description="Découvrez comment utiliser Hive avec Hadoop dans HDInsight à l’aide de Visual Studio Hadoop outils."
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

#<a name="run-hive-queries-using-the-hdinsight-tools-for-visual-studio"></a>Exécuter des requêtes Hive l’aide des outils HDInsight pour Visual Studio

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Dans cet article, vous allez apprendre à soumettre des requêtes Hive un cluster HDInsight l’aide des outils HDInsight pour Visual Studio.

> [AZURE.NOTE] Ce document ne fournit pas une description détaillée de ce que font les instructions HiveQL utilisées dans les exemples. Pour plus d’informations sur la HiveQL est utilisée dans cet exemple, voir [Utiliser la ruche avec Hadoop sur HDInsight](hdinsight-use-hive.md).

##<a id="prereq"></a>Conditions préalables

Pour effectuer les étapes décrites dans cet article, vous devez les éléments suivants.

* Un cluster Azure HDInsight (Hadoop sur HDInsight) (Linux ou fonctionnant sous Windows)

* Visual Studio (une des versions suivantes) :

    Visual Studio 2013 Communauté/Professionnel/Premium/intégrale avec la [mise à jour 4](https://www.microsoft.com/download/details.aspx?id=44921)

    Visual Studio 2015 (Communauté/grandes entreprises)

- HDInsight tools pour Visual studio. Pour plus d’informations sur l’installation et configuration des outils, voir [commencer à utiliser les outils de Visual Studio Hadoop pour HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md) .

##<a id="run"></a>Exécuter des requêtes Hive l’aide des outils HDInsight pour Visual Studio

1. Ouvrir **Visual Studio** et sélectionnez **Nouveau** > **projet** > **HDInsight** > **Application ruche**. Attribuez un nom pour ce projet.

2. Ouvrez le fichier **Script.hql** qui est créé avec ce projet et coller dans les instructions HiveQL suivantes :

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Ces instructions effectuent les opérations suivantes :

    * **DROP TABLE**: supprime la table et le fichier de données si la table existe déjà.
    * **Créer une TABLE externe**: crée une nouvelle table « externe » dans Hive. Tables externes ne stockent la définition de table Hive (les données restent dans l’emplacement d’origine).

        > [AZURE.NOTE] Tables externes doivent être utilisés lorsque vous pensez que les données sous-jacentes mettre à jour par une source externe (par exemple, un processus de téléchargement automatique des données) ou par une autre opération MapReduce, mais vous voulez toujours Hive requêtes à utiliser les données les plus récentes.
        >
        > Suppression d’une table externe signifie **pas** supprimer les données, la définition de la table.

    * **FORMAT de ligne**: indique la façon dont les données sont formatées ruche. Dans ce cas, les champs dans chaque journal sont séparées par un espace.
    * **Stockées en tant que fichier texte emplacement**: indique la ruche où les données sont stockées (le répertoire de données d’exemple) et stockée en tant que texte.
    * **Sélectionnez**: sélectionnez un compteur de toutes les lignes où colonne **t4** contient la valeur **[erreur]**. Cela doit renvoyer une valeur de **3** , car il existe trois lignes contenant cette valeur.
    * **INPUT__FILE__NAME comme '%.log'** - indique qui nous devons renvoient uniquement des données à partir de fichiers se terminant par ruche. journal. Cela limite la recherche dans le fichier exemple.log qui contient les données et conserve de renvoyer des données à partir d’autres exemple fichiers de données qui ne correspondent pas au schéma que nous avons définie.

3. À partir de la barre d’outils, sélectionnez le **HDInsight Cluster** que vous voulez utiliser pour cette requête, puis **Envoyer à WebHCat** pour exécuter les instructions en tant que Hive tâche à l’aide de WebHCat. Vous pouvez également envoyer la tâche à l’aide du bouton __Execute via HiveServer2__ si HiveServer2 est disponible dans votre version de cluster. **Récapitulatif des ruche** apparaît et affiche des informations sur la tâche en cours d’exécution. Cliquez sur le lien **Actualiser** pour actualiser les informations de tâche, jusqu'à ce que le **Statut de la tâche** devient **terminé**.

4. Cliquez sur le lien de **Sortie des tâches** pour afficher le résultat de cette tâche. Il doit s’afficher `[ERROR] 3`, qui représente la valeur renvoyée par l’instruction SELECT.

5. Vous pouvez également exécuter des requêtes Hive sans créer de projet. À l’aide de l' **Explorateur de serveurs**, développez **Azure** > **HDInsight**, avec le bouton droit de votre serveur HDInsight et sélectionnez **écrire une requête ruche**.

6. Dans le document **temp.hql** qui s’affiche, ajoutez les instructions HiveQL suivantes :

        set hive.execution.engine=tez;
        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Ces instructions effectuent les opérations suivantes :

    * **Créer tableau IF NOT EXISTS**: crée un tableau s’il n’existe pas déjà. Étant donné que le mot clé **externes** n’est pas utilisé, il s’agit d’une table interne, qui est stockée dans le data warehouse Hive et est gérée complètement par Hive.

        > [AZURE.NOTE] Contrairement aux tables **externes** , supprimez une table interne supprime également les données sous-jacentes.

    * **Stockées en tant que ORC**: stocke les données ligne optimisée (ORC) sous forme de colonnes. Il s’agit d’un format hautement optimisé et efficace pour le stockage de données Hive.
    * REMPLACER **Insertion... Sélectionnez**: sélectionne les lignes de la table **log4jLogs** qui contiennent **[erreur]**, puis insère les données dans la table des **journaux d’erreurs** .

7. Dans la barre d’outils, sélectionnez **Envoyer** pour exécuter la tâche. Utiliser le **Statut de la tâche** pour déterminer que la tâche est terminée avec succès.

8. Pour vérifier que la tâche terminée et créé une nouvelle table, utilisez **l’Explorateur de serveurs** et développez **Azure** > **HDInsight** > votre cluster HDInsight > **Ruche les bases de données** > et **par défaut**. Vous devriez voir les **journaux d’erreurs de** tables et le **log4jLogs** .

##<a id="summary"></a>Résumé

Comme vous pouvez le voir, les outils HDInsight pour Visual Studio offrent un moyen simple pour exécuter des requêtes Hive sur un cluster de HDInsight, surveiller l’état du travail et récupérer la sortie.

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur Hive dans HDInsight :

* [Utiliser Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)

Pour plus d’informations sur les autres méthodes, vous pouvez travailler avec Hadoop sur HDInsight :

* [Utiliser cochon avec Hadoop sur HDInsight](hdinsight-use-pig.md)

* [Utiliser MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

Pour plus d’informations sur les outils HDInsight pour Visual Studio :

* [Prise en main HDInsight outils pour Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md)


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

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png
