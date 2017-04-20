<properties
   pageTitle="Utiliser Hadoop cochon dans HDInsight | Microsoft Azure"
   description="Découvrez comment utiliser cochon avec Hadoop sur HDInsight."
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
   ms.date="09/14/2016"
   ms.author="larryfr"/>

# <a name="use-pig-with-hadoop-on-hdinsight"></a>Utiliser cochon avec Hadoop sur HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

[Cochon Apache](http://pig.apache.org/) est une plateforme de création de programmes pour Hadoop en utilisant le langage procédural appelé *cochon Latin*. Cochon est une suggestion pour Java pour créer des solutions *MapReduce* , et il est inclus avec Azure HDInsight.

Dans cet article, vous allez apprendre comment vous pouvez utiliser cochon avec HDInsight.

##<a id="why"></a>Pourquoi utiliser cochon ?

L’une des difficultés de traitement des données à l’aide de MapReduce dans Hadoop est mise en œuvre votre logique de traitement en utilisant uniquement une carte et une fonction réduire. Pour un traitement complexe, vous devez souvent pénétrer traitement plusieurs opérations MapReduce qui sont en chaîne pour obtenir le résultat souhaité.

Au lieu de vous obliger à utiliser uniquement le mappage et réduire les fonctions, cochon permet de définir un traitement comme une série de transformations les données circulant sur afin d’obtenir le résultat souhaité.

La langue latine cochon vous permet de décrire le flux de données à partir des données brutes, via une ou plusieurs transformations, afin d’obtenir le résultat souhaité. Cochon Latin programmes suivent ce modèle général :

- **Chargement**: lire les données à manipuler du système de fichiers
- **Transformer**: manipuler les données
- **Vidage ou store**: exporter des données à l’écran ou stocker pour traitement

Cochon Latin prend également en charge les fonctions définies par l’utilisateur (UDF), qui permet d’appeler les composants externes qui implémentent la logique qui est difficile à modéliser dans cochon Latin.

Pour plus d’informations sur cochon Latin, voir [cochon Latin référence manuel 1](http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html) et [Latin cochon référence manuel 2](http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html).

Pour obtenir un exemple d’utilisation des UDF avec cochon, consultez les documents suivants :

* [Utiliser DataFu avec cochon dans HDInsight](hdinsight-hadoop-use-pig-datafu-udf.md) - DataFu est un ensemble d’UDF utiles conservées par Apache

* [Utiliser les Python avec cochon et Hive dans HDInsight](hdinsight-python.md)

* [Utiliser le langage c# avec Hive et cochon dans HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

##<a id="data"></a>À propos de l’exemple de données

Cet exemple utilise un exemple de fichier *log4j* , qui est stocké en **/example/data/sample.log** dans le conteneur de stockage blob. Chaque journal dans le fichier est constitué d’une ligne de champs contenant un `[LOG LEVEL]` champ pour indiquer le type et la gravité, par exemple :

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

Dans l’exemple précédent, le niveau de journalisation est erreur.

> [AZURE.NOTE] Vous pouvez également générer un fichier log4j à l’aide de l’outil de journalisation [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) et puis téléchargez ce fichier dans votre blob. Pour obtenir des instructions, voir [Télécharger les données à HDInsight](hdinsight-upload-data.md) . Pour plus d’informations sur l’utilisation des objets BLOB Azure stockage sont HDInsight, voir [Utiliser Azure Blob Storage avec HDInsight](hdinsight-hadoop-use-blob-storage.md).

Les exemples de données sont stockées dans le stockage Blob Azure HDInsight utilise en tant que le système de fichiers par défaut pour les clusters Hadoop. HDInsight peut accéder aux fichiers stockés dans des objets BLOB en utilisant le préfixe **wasb** . Par exemple, pour accéder au fichier exemple.log, vous utiliseriez la syntaxe suivante :

    wasbs:///example/data/sample.log

WASB étant l’espace de stockage par défaut pour HDInsight, vous pouvez également accéder le fichier à l’aide de **/example/data/sample.log** de cochon Latin.

> [AZURE.NOTE] La syntaxe de la **wasbs : / / /**, est utilisé pour accéder aux fichiers stockés dans le conteneur de stockage par défaut pour votre cluster HDInsight. Si vous avez spécifié des comptes d’espace de stockage supplémentaire lorsque vous votre cluster sa mise en service, et que vous voulez accéder aux fichiers stockés dans ces comptes, vous pouvez accéder aux données en spécifiant l’adresse de compte conteneur nom et de stockage, par exemple : **wasbs://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**.


##<a id="job"></a>Sur la tâche d’exemple

La tâche suivante cochon Latin charge le fichier **exemple.log** à partir du stockage par défaut pour votre cluster HDInsight. Puis effectue une série de transformations qui génèrent un nombre de fois combien chaque niveau de journalisation s’est produite dans les données d’entrée. Les résultats sont exportés vers STDOUT.

    LOGS = LOAD 'wasbs:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;

L’image suivante montre une répartition du rôle de chaque transformation aux données.

![Représentation graphique des transformations][image-hdi-pig-data-transformation]

##<a id="run"></a>Exécuter la tâche cochon Latin

HDInsight pouvez exécuter cochon Latin travaux à l’aide de plusieurs méthodes. Utilisez le tableau suivant pour déterminer quelle méthode vous consiste, puis cliquez sur le lien pour une procédure pas à pas.

| **Utilisez cette option** si vous souhaitez...                                   | .. shell **interactif** .an | ... traitement **par lots** | .. .avec ce **système d’exploitation cluster** | .. contre ce **système d’exploitation client** |
|:--------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-hadoop-use-pig-ssh.md)                        |              ✔              |            ✔            | Linux                                     | Linux, Unix, Mac OS X ou Windows        |
| [Coin](hdinsight-hadoop-use-pig-curl.md)                      |           &nbsp;            |            ✔            | Linux ou Windows                          | Linux, Unix, Mac OS X ou Windows        |
| [Kit de développement .NET pour Hadoop](hdinsight-hadoop-use-pig-dotnet-sdk.md) |           &nbsp;            |            ✔            | Linux ou Windows                          | Windows (pour l’instant)                        |
| [Windows PowerShell](hdinsight-hadoop-use-pig-powershell.md)  |           &nbsp;            |            ✔            | Linux ou Windows                          | Windows                                  |
| [Bureau à distance](hdinsight-hadoop-use-pig-remote-desktop.md)  |              ✔              |            ✔            | Windows                                   | Windows                                  |


## <a name="running-pig-jobs-on-azure-hdinsight-using-on-premises-sql-server-integration-services"></a>Exécution des travaux de cochon sur Azure HDInsight à l’aide en local SQL Server Integration Services

Vous pouvez également utiliser SQL Server Integration Services (SSIS) pour exécuter une tâche cochon. Le Azure Feature Pack pour SSIS fournit les composants suivants qui fonctionnent avec travaux cochon sur HDInsight.


- [Tâche Azure HDInsight cochon][pigtask]
- [Gestionnaire de connexions abonnement Azure][connectionmanager]


En savoir plus sur le Feature Pack Azure pour SSIS [ici][ssispack].


##<a id="nextsteps"></a>Étapes suivantes

À présent que vous avez appris à utiliser cochon avec HDInsight, utilisez les liens suivants pour Explorer d’autres façons de travailler avec Azure HDInsight.

* [Télécharger des données à HDInsight][hdinsight-upload-data]
* [Utiliser Hive avec HDInsight][hdinsight-use-hive]
* [Utiliser Sqoop avec HDInsight](hdinsight-use-sqoop.md)
* [Utiliser Oozie avec HDInsight](hdinsight-use-oozie.md)
* [Utiliser des travaux MapReduce avec HDInsight][hdinsight-use-mapreduce]

[check]: ./media/hdinsight-use-pig/hdi.checkmark.png

[apachepig-home]: http://pig.apache.org/
[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: http://curl.haxx.se/
[pigtask]: http://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: ../powershell-install-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx

[image-hdi-log4j-sample]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
[image-hdi-pig-powershell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png
[image-hdi-pig-architecture]: ./media/hdinsight-use-pig/HDI.Pig.Architecture.png
