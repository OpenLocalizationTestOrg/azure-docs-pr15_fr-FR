<properties
    pageTitle="Action de Script permet d’installer explosion sur cluster Hadoop | Microsoft Azure"
    description="Découvrez comment personnaliser un cluster HDInsight avec explosion à l’aide d’Action de Script."
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/05/2016"
    ms.author="nitinme"/>

# <a name="install-and-use-spark-on-hdinsight-hadoop-clusters-using-script-action"></a>Installer et utiliser explosion sur clusters HDInsight Hadoop via l’Action de Script

> [AZURE.IMPORTANT] Cet article est déconseillé maintenant. HDInsight propose désormais explosion comme type de première classe cluster pour clusters fonctionnant sous Windows, ce qui signifie que vous pouvez désormais directement créer un cluster explosion sans modifier un cluster Hadoop utilisant l’action de Script. Utilisez le type de cluster explosion, vous offre un cluster de version 3.2 HDInsight explosion version 1.3.1.  Pour installer des versions différentes d’explosion, vous pouvez utiliser l’action de Script. HDInsight fournit un exemple de script d’Action de Script.

Découvrez comment installer explosion sur Windows basée HDInsight à l’aide de Script Action, et comment faire pour exécuter des requêtes explosion sur HDInsight clusters.


**Articles connexes**

- [Hadoop créer des groupes dans un HDInsight](hdinsight-provision-clusters.md): obtenir des informations générales sur la création de clusters HDInsight.

- [Prise en main explosion Apache sur HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md): créez un cluster HDInsight Spark.

- [Personnaliser cluster HDInsight à l’aide de Script Action][hdinsight-cluster-customize]: obtenir des informations générales sur la personnalisation des clusters HDInsight à l’aide de Script Action.

- [Action de Script développer des scripts pour HDInsight](hdinsight-hadoop-script-actions.md).

## <a name="what-is-spark"></a>Qu’est explosion ?

<a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Explosion Apache</a> est un traitement framework qui prend en charge de traitement en mémoire pour améliorer les performances des applications analytiques données volumineuses en parallèle open source. Fonctionnalités de calcul en mémoire d’explosion rendent un bon choix pour algorithmes itératifs dans les calculs de graphique et l’apprentissage machine.

Explosion permet également d’effectuer le traitement des données de sur le disque conventionnelle. Explosion améliore la structure de MapReduce traditionnelle en évitant écrit sur le disque dans les phases intermédiaires. En outre, explosion est compatible avec le système de fichier distribué Hadoop (HDFS) et Blob Azure le stockage afin que les données existantes peuvent être facilement traitées via explosion.

Cette rubrique fournit des instructions sur la personnalisation d’un cluster HDInsight pour installer explosion.

## <a name="install-spark-using-the-azure-portal"></a>Installer explosion à l’aide du portail Azure

Un exemple de script pour installer explosion sur un cluster HDInsight est disponible à partir d’un blob de stockage Azure en lecture seule en [https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1](https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1). Ce script peut installer explosion 1.2.0 ou explosion 1.0.2 selon la version du cluster HDInsight que vous créez.

- Si vous utilisez le script lors de la création d’un cluster **3.2 HDInsight** , il installe **explosion 1.2.0**.
- Si vous utilisez le script lors de la création d’un cluster **3.1 HDInsight** , il installe **explosion 1.0.2**.

Vous pouvez modifier ce script ou créer votre propre script pour installer d’autres versions d’explosion.

> [AZURE.NOTE] L’exemple de script fonctionne uniquement avec HDInsight 3.1 et 3,2 clusters. Pour plus d’informations sur les versions de cluster HDInsight, voir [HDInsight cluster versions](hdinsight-component-versioning.md).

1. Commencer à créer un cluster à l’aide de l’option **Créer personnalisés** , tels que décrits sur [Hadoop créer des groupes dans un HDInsight](hdinsight-provision-clusters.md#portal). Sélectionnez la version cluster selon les éléments suivants :

    - Si vous voulez installer **explosion 1.2.0**, créez un cluster 3.2 HDInsight.
    - Si vous voulez installer **explosion 1.0.2**, créez un cluster 3.1 HDInsight.


2. Dans la page **Actions de Script** de l’Assistant, cliquez sur **Ajouter une action de script** pour fournir des détails sur l’action script, comme indiqué ci-dessous :

    ![Action de Script d’utilisation pour personnaliser un cluster] (./media/hdinsight-hadoop-spark-install/HDI.CustomProvision.Page6.png "Action de Script d’utilisation pour personnaliser un cluster")

    <table border='1'>
        <tr><th>Propriété</th><th>Valeur</th></tr>
        <tr><td>Nom</td>
            <td>Attribuez un nom à l’action de script. Par exemple, <b>Explosion installer</b>.</td></tr>
        <tr><td>Script URI</td>
            <td>Spécifier l’identificateur URI (Uniform Resource) pour le script qui est appelé pour personnaliser le cluster. Par exemple, <i>https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1</i></td></tr>
        <tr><td>Type de nœud</td>
            <td>Spécifier les nœuds sur lequel s’exécute le script de personnalisation. Vous pouvez choisir <b>tous les nœuds</b>, <b>nœuds de tête uniquement</b>ou <b>uniquement les nœuds de travail</b>.
        <tr><td>Paramètres</td>
            <td>Spécifier les paramètres, le cas échéant par le script. Le script installer explosion ne nécessite pas tous les paramètres afin que vous pouvez laisser cette est vide.</td></tr>
    </table>

    Vous pouvez ajouter plusieurs actions de script pour installer plusieurs composants sur le cluster. Une fois que vous avez ajouté les scripts, cliquez sur la case à cocher pour commencer à créer le cluster.

Vous pouvez également utiliser le script installer explosion sur HDInsight à l’aide de PowerShell Azure ou le Kit de développement .NET HDInsight. Vous trouverez des instructions pour ces procédures plus loin dans cette rubrique.

## <a name="use-spark-in-hdinsight"></a>Utiliser explosion dans HDInsight
Explosion fournit des API dans Scala, Python et Java. Vous pouvez également utiliser au shell explosion interactif pour exécuter des requêtes explosion. Cette section fournit des instructions sur la façon d’utiliser les différentes approches pour l’utiliser avec explosion :

- [Utilisation d’explosion shell pour exécuter des requêtes interactifs](#sparkshell)
- [Utilisation d’explosion shell pour exécuter des requêtes SQL explosion](#sparksql)
- [Utiliser un programme Scala autonome](#standalone)

###<a name="sparkshell"></a>Utilisation d’explosion shell pour exécuter des requêtes interactifs
Effectuez les opérations suivantes pour exécuter des requêtes explosion à partir d’un shell explosion interactif. Dans cette section, nous exécuter une requête explosion sur un exemple de fichier de données (/ example/data/gutenberg/davinci.txt) qui est disponible sur les clusters HDInsight par défaut.

1. À partir du portail Azure, activer le Bureau à distance pour le cluster que vous avez créé avec explosion installé et remote puis dans le cluster. Pour obtenir des instructions, voir [se connecter à clusters HDInsight à l’aide de RDP](hdinsight-administer-use-management-portal.md#rdp).

2. Dans la session RDP Remote Desktop Protocol (), à partir du bureau, ouvrez la ligne de commande Hadoop (à partir d’un raccourci sur le bureau) et accédez à l’emplacement où explosion est installée ; par exemple, **C:\apps\dist\spark-1.2.0**.


3. Exécutez la commande suivante pour démarrer l’interface explosion :

         .\bin\spark-shell --master yarn

    Une fois la commande terminée, vous devez obtenir une Scala invite :

         scala>

5. À l’invite Scala, entrez la requête explosion illustrée ci-dessous. Cette requête détermine l’occurrence de chaque mot dans le fichier davinci.txt qui est disponible à le/exemple / / gutenberg/emplacement des données sur le stockage Blob Azure associé au cluster.

        val file = sc.textFile("/example/data/gutenberg/davinci.txt")
        val counts = file.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey(_ + _)
        counts.toArray().foreach(println)

6. La sortie doit ressembler à ceci :

    ![Résultat de l’exécution Scala shell interactif dans un cluster HDInsight](./media/hdinsight-hadoop-spark-install/hdi-scala-interactive.png)


7. Entrez : q pour quitter l’invite Scala.

        :q

###<a name="sparksql"></a>Utilisation d’explosion shell pour exécuter des requêtes SQL explosion

Explosion SQL vous permet d’utiliser explosion pour exécuter des requêtes relationnelles exprimés en langage SQL (Structured Query), HiveQL ou Scala. Dans cette section, nous examinons à l’aide d’explosion pour exécuter une requête Hive sur un exemple de table Hive. La table Hive utilisée dans cette section (appelée **hivesampletable**) est disponible par défaut lorsque vous créez un cluster.

>[AZURE.NOTE] L’exemple ci-dessous a été créé par rapport à **créer du 1.2.0**, qui est installé si vous exécutez l’action script lors de la création de cluster 3.2 HDInsight.

1. À partir du portail Azure, activer le Bureau à distance pour le cluster que vous avez créé avec explosion installé et remote puis dans le cluster. Pour obtenir des instructions, voir [se connecter à clusters HDInsight à l’aide de RDP](hdinsight-administer-use-management-portal.md#rdp).

2. Dans la session RDP, à partir du bureau, ouvrez la ligne de commande Hadoop (à partir d’un raccourci sur le bureau) et accédez à l’emplacement où explosion est installée ; par exemple, **C:\apps\dist\spark-1.2.0**.


3. Exécutez la commande suivante pour démarrer l’interface explosion :

         .\bin\spark-shell --master yarn

    Une fois la commande terminée, vous devez obtenir une Scala invite :

         scala>

4. À l’invite Scala, définissez le contexte Hive. Cela est nécessaire pour utiliser des requêtes Hive à l’aide d’explosion.

        val hiveContext = new org.apache.spark.sql.hive.HiveContext(sc)

    Notez que **sc** est contexte explosion par défaut défini lorsque vous démarrez le shell explosion.

5. Exécuter une requête Hive en utilisant le contexte Hive et imprimer la sortie à la console. La requête extrait les données sur les périphériques d’une marque spécifique et limite le nombre d’enregistrements extraits à 20.

        hiveContext.sql("""SELECT * FROM hivesampletable WHERE devicemake LIKE "HTC%" LIMIT 20""").collect().foreach(println)

6. Vous devriez voir un résultat comme suit :

    ![Résultat de l’exécution d’explosion SQL sur un cluster HDInsight](./media/hdinsight-hadoop-spark-install/hdi-spark-sql.png)

7. Entrez : q pour quitter l’invite Scala.

        :q

### <a name="standalone"></a>Utiliser un programme Scala autonome

Dans cette section, nous écrire une application Scala qui détermine le nombre de lignes qui contiennent les lettres « a » et « b » dans un exemple de fichier de données (/ example/data/gutenberg/davinci.txt) qui est disponible sur les clusters HDInsight par défaut. Pour écrire et utiliser un programme Scala autonome avec un cluster personnalisé avec l’installation d’explosion, vous devez effectuer les opérations suivantes :

- Écrire un programme Scala
- Créer le programme Scala pour obtenir le fichier .jar
- Exécuter la tâche sur le cluster

#### <a name="write-a-scala-program"></a>Écrire un programme Scala
Dans cette section, vous écrivez un programme Scala qui détermine le nombre de lignes qui contiennent « un » et « b » dans l’exemple de fichier de données.

1. Ouvrez un éditeur de texte et collez le code suivant :


        /* SimpleApp.scala */
        import org.apache.spark.SparkContext
        import org.apache.spark.SparkContext._
        import org.apache.spark.SparkConf

        object SimpleApp {
          def main(args: Array[String]) {
            val logFile = "/example/data/gutenberg/davinci.txt"         //Location of the sample data file on Azure Blob storage
            val conf = new SparkConf().setAppName("SimpleApplication")
            val sc = new SparkContext(conf)
            val logData = sc.textFile(logFile, 2).cache()
            val numAs = logData.filter(line => line.contains("a")).count()
            val numBs = logData.filter(line => line.contains("b")).count()
            println("Lines with a: %s, Lines with b: %s".format(numAs, numBs))
          }
        }

2. Enregistrez le fichier avec le nom **SimpleApp.scala**.

#### <a name="build-the-scala-program"></a>Créer le programme Scala
Dans cette section, utilisez <a href="http://www.scala-sbt.org/0.13/docs/index.html" target="_blank">l’Outil de génération Simple</a> (ou sbt) pour générer le programme Scala. SBT nécessite Java 1,6 ou version ultérieure, vérifiez que vous disposez de la version de Java installé avant de poursuivre cette section.

1. Installez sbt à partir de http://www.scala-sbt.org/0.13/tutorial/Installing-sbt-on-Windows.html.
2. Créer un dossier nommé **SimpleScalaApp**et inclus dans ce dossier, créez un fichier nommé **simple.sbt**. Il s’agit d’un fichier de configuration qui contient des informations sur la version Scala, dépendances de bibliothèque, etc.. Collez ce qui suit dans le fichier simple.sbt et enregistrez-le :


        name := "SimpleApp"

        version := "1.0"

        scalaVersion := "2.10.4"

        libraryDependencies += "org.apache.spark" %% "spark-core" % "1.2.0"



    >[AZURE.NOTE] Vérifiez que vous conservez les lignes vides dans le fichier.


3. Sous le dossier **SimpleScalaApp** , créez une structure de répertoire **\src\main\scala** et collez le programme Scala (**SimpleApp.scala**) que vous avez créée dans le dossier \src\main\scala.
4. Ouvrez une invite de commandes, accédez au répertoire SimpleScalaApp et entrez la commande suivante :


        sbt package


    Une fois que l’application est compilée, vous verrez un fichier **simpleapp_2.10 1.0.jar** créé sous le répertoire **\target\scala-2.10** dans le dossier racine du SimpleScalaApp.


#### <a name="run-the-job-on-the-cluster"></a>Exécuter la tâche sur le cluster
Dans cette section, vous à distance dans le cluster qui a explosion installés, puis copiez le SimpleScalaApp dossier du projet cible. Puis, vous utilisez la commande **Envoyer explosion** à soumettre la tâche sur le cluster.

1. Distance dans le cluster qui a explosion installée. À partir de l’ordinateur où vous avez écrit et créé le programme SimpleApp.scala, copiez le dossier **SimpleScalaApp\target** et collez-le dans un emplacement sur le cluster.
2. Dans la session RDP, à partir du bureau, ouvrez la ligne de commande Hadoop et accédez à l’emplacement où vous avez collé le dossier **cible** .
3. Entrez la commande suivante pour exécuter le programme SimpleApp.scala :


        C:\apps\dist\spark-1.2.0\bin\spark-submit --class "SimpleApp" --master local target/scala-2.10/simpleapp_2.10-1.0.jar

4. Lorsque le programme termine en cours d’exécution, le résultat s’affiche dans la console.


        Lines with a: 21374, Lines with b: 11430

## <a name="install-spark-using-azure-powershell"></a>Installer explosion à l’aide de PowerShell Azure

Dans cette section, nous utilisons l’applet de commande **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** pour appeler des scripts à l’aide d’Action de Script pour personnaliser un cluster. Avant de commencer, vérifiez que vous avez installé et configuré Azure PowerShell. Pour plus d’informations sur la configuration d’un poste de travail pour exécuter les applets de commande PowerShell Azure pour HDInsight, voir [installer et configurer Azure PowerShell](../powershell-install-configure.md).

Effectuez les opérations suivantes :

1. Ouvrez une fenêtre PowerShell Azure et déclarez les variables suivantes :

        # Provide values for these variables
        $subscriptionName = "<SubscriptionName>"        # Name of the Azure subscription
        $clusterName = "<HDInsightClusterName>"         # HDInsight cluster name
        $storageAccountName = "<StorageAccountName>"    # Azure Storage account that hosts the default container
        $storageAccountKey = "<StorageAccountKey>"      # Key for the Storage account
        $containerName = $clusterName
        $location = "<MicrosoftDataCenter>"             # Location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $clusterNodes = <ClusterSizeInNumbers>          # Number of nodes in the HDInsight cluster
        $version = "<HDInsightClusterVersion>"          # For example, "3.2"

2. Spécifier les valeurs de configuration tels que des nœuds dans le cluster et le stockage par défaut à utiliser.

        # Specify the configuration options
        Select-AzureSubscription $subscriptionName
        $config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
        $config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
        $config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
        $config.DefaultStorageAccount.StorageContainerName=$containerName

3. Utiliser l’applet de commande **Add-AzureHDInsightScriptAction** pour ajouter une action de script à la configuration du cluster. Plus tard, lorsque le cluster est créé, l’action de script est exécutée.

        # Add a script action to the cluster configuration
        $config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Spark" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1

    Applet de commande **Add-AzureHDInsightScriptAction** accepte les paramètres suivants :

    <table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
    <tr>
    <th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Paramètre</th>
    <th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">Définition</th></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Configuration de la</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">L’objet de configuration pour le script d’informations sur l’action sont ajoutées.</td></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nom</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nom de l’action de script.</td></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Spécifie les nœuds sur lequel s’exécute le script de personnalisation. Les valeurs valides sont HeadNode (pour l’installer sur le nœud de tête) ou DataNode (pour une installation sur tous les nœuds de données). Vous pouvez utiliser une ou les deux valeurs.</td></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">URI</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Spécifie l’URI pour le script qui s’exécute.</td></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Paramètres</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Paramètres requis par le script. L’exemple de script utilisé dans cette rubrique ne nécessite pas tous les paramètres et, par conséquent, vous ne voyez pas ce paramètre dans l’extrait ci-dessus.
    </td></tr>
    </table>

4. Pour finir, commencez à créer un cluster personnalisée avec explosion installée.  

        # Start creating a cluster with Spark installed
        New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version

Lorsque vous y êtes invité, entrez les informations d’identification pour le cluster. Il peut prendre plusieurs minutes avant que le cluster est créé.

## <a name="install-spark-using-powershell"></a>Installer explosion à l’aide de PowerShell

Voir [clusters HDInsight personnaliser à l’aide de Script Action](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell).

## <a name="install-spark-using-net-sdk"></a>Installer explosion à l’aide du Kit de développement .NET

Voir [clusters HDInsight personnaliser à l’aide de Script Action](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell).


## <a name="see-also"></a>Voir aussi

- [Hadoop créer des groupes dans un HDInsight](hdinsight-provision-clusters.md): créer des clusters HDInsight.
- [Prise en main explosion Apache sur HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md): prise en main explosion sur HDInsight.
- [Personnaliser cluster HDInsight à l’aide de Script Action][hdinsight-cluster-customize]: personnaliser les HDInsight à l’aide d’Action de Script.
- [Action de Script développer des scripts pour HDInsight](hdinsight-hadoop-script-actions.md): développer les scripts d’Action de Script.
- [Installer R sur clusters HDInsight] [ hdinsight-install-r] fournit des instructions sur l’utilisation de personnalisation cluster pour installer et utiliser R sur HDInsight Hadoop clusters. R est un environnement pour calculer des statistiques et langue open source. Il propose des centaines de fonctions statistiques intégrées et sa propre langage de programmation qui combine les aspects de la programmation orientée objet et fonctionnelle. Il fournit également des fonctions graphiques étendues.
- [Installer Giraph sur les clusters HDInsight](hdinsight-hadoop-giraph-install.md). Personnalisation de cluster permet d’installer Giraph sur HDInsight Hadoop clusters. Giraph, vous pouvez exécuter graph traitement à l’aide Hadoop et peut être utilisé avec Azure HDInsight.
- [Installer mode série sur LAN.r sur clusters HDInsight](hdinsight-hadoop-solr-install.md). Personnalisation de cluster permet d’installer mode série sur LAN.r sur HDInsight Hadoop clusters. Mode série sur LAN.r vous permet d’effectuer des opérations de recherche puissante sur des données stockées.

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[powershell-install-configure]: powershell-install-configure.md
