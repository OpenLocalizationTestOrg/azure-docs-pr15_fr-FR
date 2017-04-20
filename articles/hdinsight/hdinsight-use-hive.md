<properties
    pageTitle="Découvrez les nouveautés Hive et comment utiliser HiveQL | Microsoft Azure"
    description="Découvrez les Apache ruche et comment l’utiliser avec Hadoop dans HDInsight. Choisissez comment exécuter votre tâche Hive et HiveQL permet d’analyser un exemple de fichier log4j Apache."
    keywords="hiveql, ce qui est hive"
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
    ms.date="09/19/2016"
    ms.author="larryfr"/>

# <a name="use-hive-and-hiveql-with-hadoop-in-hdinsight-to-analyze-a-sample-apache-log4j-file"></a>Utiliser des Hive et HiveQL avec Hadoop dans HDInsight pour analyser un exemple de fichier log4j Apache

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]


Dans ce didacticiel, vous allez apprendre à utiliser Apache ruche dans Hadoop sur HDInsight et choisissez comment exécuter votre tâche Hive. Vous découvrirez également HiveQL et d’analyser un exemple de fichier log4j Apache.

##<a id="why"></a>Qu’est Hive et pourquoi l’utiliser ?
[Apache ruche](http://hive.apache.org/) est un système data warehouse pour Hadoop, qui permet de synthèse des données, l’interrogation et analyse des données à l’aide de HiveQL (un langage de requête similaire à SQL). Hive peut servir à Explorer vos données de manière interactive ou pour créer des tâches de traitement par lots réutilisable.

Hive vous permet de structure de projet sur données largement non structurées. Après avoir défini la structure, vous pouvez utiliser Hive pour interroger ces données sans connaissance de Java ou MapReduce. **HiveQL** (le langage de requête Hive) vous permet d’écrire des requêtes avec les instructions qui sont similaires aux T-SQL.

Hive prend en charge l’utilisation des données structurées et semi-structurées, tels que des fichiers texte dans lequel les champs sont délimités par des caractères spécifiques. Hive prend également en charge personnalisé **sérialiseur/désérialiseurs (SerDe)** pour les données complexes ou irrégulière structurées. Pour plus d’informations, voir [comment utiliser un SerDe JSON personnalisé avec HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight.aspx).

## <a name="user-defined-functions-udf"></a>Fonctions définies par l’utilisateur (UDF)

Hive peut également être étendu via des **fonctions définies par l’utilisateur (UDF)**. Un fichier UDF vous permet d’implémenter des fonctionnalités ou la logique qui n’est pas facilement appuient sur dans HiveQL. Pour obtenir un exemple d’utilisation des UDF avec Hive, voir les rubriques suivantes :

* [Fonction avec Hive définie par l’utilisation d’un utilisateur Java](hdinsight-hadoop-hive-java-udf.md)

* [Utilisation de Python avec Hive et cochon dans HDInsight](hdinsight-python.md)

* [Utiliser le langage c# avec Hive et cochon dans HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Comment ajouter un fichier UDF ruche personnalisé à HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [Exemple de ruche UDF personnalisé pour convertir des formats de date/heure en horodatage](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a name="hive-internal-tables-vs-external-tables"></a>Ruche internes et externes tableaux

Il existe plusieurs choses que vous devez savoir sur la table interne Hive et table externe :

- La commande **CREATE TABLE** crée une table interne. Le fichier de données doit se trouver dans le conteneur par défaut.
- La commande **Créer un tableau** déplace le fichier de données vers le /hive/entrepôt/<TableName> dossier.
- La commande **Créer une TABLE externe** crée une table externe. Le fichier de données peut résider en dehors du conteneur par défaut.
- La commande **Créer une TABLE externe** ne bouge pas le fichier de données.
- La commande **Créer une TABLE externe** ne permet pas de tous les dossiers dans l’emplacement. Il s’agit de la raison pour laquelle pourquoi le didacticiel effectue une copie du fichier exemple.log.

Pour plus d’informations, voir [HDInsight : ruche internes et externes Tables introduction][cindygross-hive-tables].


##<a id="data"></a>Sur les données d’exemple, un fichier log4j Apache

Cet exemple utilise un exemple de fichier *log4j* , qui est stocké en **/example/data/sample.log** dans le conteneur de stockage blob. Chaque journal dans le fichier est constitué d’une ligne de champs contenant un `[LOG LEVEL]` champ pour indiquer le type et la gravité, par exemple :

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

Dans l’exemple précédent, le niveau de journalisation est erreur.

> [AZURE.NOTE] Vous pouvez également générer un fichier log4j à l’aide de l’outil de journalisation [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) et puis téléchargez ce fichier dans le conteneur blob. Pour obtenir des instructions, voir [Télécharger les données à HDInsight](hdinsight-upload-data.md) . Pour plus d’informations sur l’utilisation du stockage d’objets Blob Azure avec HDInsight, voir [Utiliser Azure Blob Storage avec HDInsight](hdinsight-hadoop-use-blob-storage.md).

Les exemples de données sont stockées dans le stockage Blob Azure HDInsight utilise en tant que le système de fichiers par défaut. HDInsight peut accéder aux fichiers stockés dans des objets BLOB en utilisant le préfixe **wasb** . Par exemple, pour accéder au fichier exemple.log, vous utiliseriez la syntaxe suivante :

    wasbs:///example/data/sample.log

Stockage d’objets Blob Azure étant l’espace de stockage par défaut pour HDInsight, vous pouvez également accéder le fichier à l’aide de **/example/data/sample.log** de HiveQL.

> [AZURE.NOTE] La syntaxe de la **wasbs : / / /**, est utilisé pour accéder aux fichiers stockés dans le conteneur de stockage par défaut pour votre cluster HDInsight. Si vous avez spécifié des comptes d’espace de stockage supplémentaire lorsque vous votre cluster sa mise en service, et que vous voulez accéder aux fichiers stockés dans ces comptes, vous pouvez accéder aux données en spécifiant le conteneur nom et stockage compte l’adresse, par exemple, **wasbs://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**.

##<a id="job"></a>Travail exemple : projeter colonnes sur données délimité

Les instructions suivantes HiveQL seront projeter colonnes sur délimitée par des données qui sont stockées dans le **wasbs : / / exemple/données** répertoire :

    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

Dans l’exemple précédent, les instructions HiveQL effectuent les opérations suivantes :

* __définir hive.execution.engine=tez ;__: définit le moteur d’exécution à utiliser Tez. À l’aide de Tez au lieu de MapReduce peut fournir une augmentation des performances des requêtes. Pour plus d’informations sur Tez, consultez la section [Utilisation Apache Tez pour améliorer les performances](#usetez) .

    > [AZURE.NOTE] Cette déclaration n’est obligatoire si vous utilisez un cluster HDInsight fonctionnant sous Windows ; Tez est le moteur d’exécution par défaut pour HDInsight basé sur Linux.

* **DROP TABLE**: supprime la table et le fichier de données si la table existe déjà.
* **Créer une TABLE externe**: crée une nouvelle table **externe** dans Hive. Tables externes ne stockent la définition de table Hive ; les données restant dans l’emplacement d’origine et dans son format d’origine.
* **FORMAT de ligne**: indique la façon dont les données sont formatées ruche. Dans ce cas, les champs dans chaque journal sont séparées par un espace.
* **Stockées en tant que fichier texte emplacement**: indique la ruche où les données sont stockées (le répertoire de données d’exemple) et stockée en tant que texte. Les données peuvent se trouver dans un fichier ou étendues à plusieurs fichiers dans l’annuaire.
* **Sélectionnez**: sélectionne un compteur de toutes les lignes dont la colonne **t4** contient la valeur **[erreur]**. Cela doit renvoyer une valeur de **3** , car il existe trois lignes contenant cette valeur.
* **INPUT__FILE__NAME comme '%.log'** - indique qui nous devons renvoient uniquement des données à partir de fichiers se terminant par ruche. journal. Cela limite la recherche dans le fichier exemple.log qui contient les données et conserve de renvoyer des données à partir d’autres exemple fichiers de données qui ne correspondent pas au schéma que nous avons définie.

> [AZURE.NOTE] Tables externes doivent être utilisés lorsque vous attendez les données sous-jacentes mettre à jour par une source externe, par exemple, un processus de téléchargement automatique des données, ou par une autre opération MapReduce et si vous souhaitez toujours Hive requêtes à utiliser les données les plus récentes.
>
> Suppression d’une table externe signifie **pas** supprimer les données, mais uniquement la définition de table.

Après avoir créé la table externe, les instructions suivantes permettent de créer une table **interne** .

    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs
    SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

Ces instructions effectuent les opérations suivantes :

* **Créer tableau IF NOT EXISTS**: crée une table, s’il n’existe pas déjà. Étant donné que le mot clé **externes** n’est pas utilisé, il s’agit d’une table interne, qui est stockée dans le data warehouse Hive et est gérée complètement par Hive.
* **Stockées en tant que ORC**: stocke les données au format optimisé ligne en colonnes (ORC). Il s’agit d’un format hautement optimisé et efficace pour le stockage de données Hive.
* REMPLACER **Insertion... Sélectionnez**: sélectionne les lignes dans la table **log4jLogs** qui contient **[erreur]**, puis insère les données dans la table des **journaux d’erreurs** .

> [AZURE.NOTE] Contrairement aux tables externes, supprimez une table interne supprime également les données sous-jacentes.

##<a id="usetez"></a>Utiliser Tez Apache pour améliorer les performances

[Apache Tez](http://tez.apache.org) est une structure permettant aux applications intensive de données, tels que Hive, peuvent fonctionner beaucoup plus efficacement à l’échelle. Dans la dernière version de HDInsight, Hive prend en charge en cours d’exécution sur Tez. Tez est activée par défaut pour les clusters basés sur Linux HDInsight.

> [AZURE.NOTE] Tez est désactivée par défaut pour les clusters HDInsight fonctionnant sous Windows et il doit être activé. Pour tirer parti de Tez, la valeur suivante doit être définie pour une requête Hive :
>
> ```set hive.execution.engine=tez;```
>
>Cela peut être soumis sur une base par requête en plaçant au début de votre requête. Vous pouvez également définir cette option pour être par défaut sur un cluster en définissant la valeur de configuration lorsque vous créez le cluster. Vous trouverez plus d’informations de [Mise en service des Clusters HDInsight](hdinsight-provision-clusters.md).

La [ruche sur des documents de conception Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) contiennent un nombre de plus d’informations sur les configurations réglage et la mise en œuvre.

Pour faciliter le débogage des travaux exécuté à l’aide de Tez, HDInsight propose le web interfaces utilisateur qui vous permettent d’afficher les détails des travaux Tez suivant :

* [Utiliser l’interface utilisateur Tez sur HDInsight fonctionnant sous Windows](hdinsight-debug-tez-ui.md)

* [Utiliser l’affichage Ambari Tez sur HDInsight basé sur Linux](hdinsight-debug-ambari-tez-view.md)

##<a id="run"></a>Choisissez comment exécuter la tâche HiveQL

HDInsight pouvez exécuter tâches HiveQL à l’aide de plusieurs méthodes. Utilisez le tableau suivant pour déterminer quelle méthode vous consiste, puis cliquez sur le lien pour une procédure pas à pas.

| **Utilisez cette option** si vous souhaitez...                                                     | .. shell **interactif** .an | ... traitement **par lots** | .. .avec ce **système d’exploitation cluster** | .. contre ce **système d’exploitation client** |
|:--------------------------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [Affichage Hive](hdinsight-hadoop-use-hive-ambari-view.md) | ✔ | ✔ | Linux | N’importe quel (navigateur en fonction) |
| [Commande beeline (à partir d’une session SSH)](hdinsight-hadoop-use-hive-beeline.md)                                         |              ✔              |            ✔            | Linux                                     | Linux, Unix, Mac OS X ou Windows        |
| [Commande Hive (à partir d’une session SSH)](hdinsight-hadoop-use-hive-ssh.md)                                         |              ✔              |            ✔            | Linux                                     | Linux, Unix, Mac OS X ou Windows        |
| [Coin](hdinsight-hadoop-use-hive-curl.md)                                       |           &nbsp;            |            ✔            | Linux ou Windows                          | Linux, Unix, Mac OS X ou Windows        |
| [Console de requête](hdinsight-hadoop-use-hive-query-console.md)                     |           &nbsp;            |            ✔            | Windows                                   | N’importe quel (navigateur en fonction)                            |
| [HDInsight tools pour Visual Studio](hdinsight-hadoop-use-hive-visual-studio.md) |           &nbsp;            |            ✔            | Linux ou Windows                          | Windows                                  |
| [Windows PowerShell](hdinsight-hadoop-use-hive-powershell.md)                   |           &nbsp;            |            ✔            | Linux ou Windows                          | Windows                                  |
| [Bureau à distance](hdinsight-hadoop-use-hive-remote-desktop.md)                   |              ✔              |            ✔            | Windows                                   | Windows                                  |

## <a name="running-hive-jobs-on-azure-hdinsight-using-on-premises-sql-server-integration-services"></a>Exécution des travaux Hive sur Azure HDInsight à l’aide en local SQL Server Integration Services

Vous pouvez également utiliser SQL Server Integration Services (SSIS) pour exécuter une tâche Hive. Le Azure Feature Pack pour SSIS fournit les composants suivants qui fonctionnent avec Hive travaux sur HDInsight.


- [Tâche Azure HDInsight Hive][hivetask]
- [Gestionnaire de connexions abonnement Azure][connectionmanager]


En savoir plus sur le Feature Pack Azure pour SSIS [ici][ssispack].


##<a id="nextsteps"></a>Étapes suivantes

À présent que vous avez appris qu’Hive est et comment l’utiliser avec Hadoop dans HDInsight, utilisez les liens suivants pour Explorer d’autres façons de travailler avec Azure HDInsight.


- [Télécharger des données à HDInsight][hdinsight-upload-data]
- [Utiliser cochon avec HDInsight][hdinsight-use-pig]
- [Utiliser Sqoop avec HDInsight](hdinsight-use-sqoop.md)
- [Utiliser Oozie avec HDInsight](hdinsight-use-oozie.md)
- [Utiliser des travaux MapReduce avec HDInsight][hdinsight-use-mapreduce]

[check]: ./media/hdinsight-use-hive/hdi.checkmark.png

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/
[hivetask]: http://msdn.microsoft.com/library/mt146771(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-get-started.md

[Powershell-install-configure]: ../powershell-install-configure.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png


[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
