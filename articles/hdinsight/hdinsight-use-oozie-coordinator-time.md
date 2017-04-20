<properties
    pageTitle="Utiliser temporelles Hadoop Oozie coordinateur dans HDInsight | Microsoft Azure"
    description="Suivez temporelles Hadoop Oozie coordinateur HDInsight, un service de données volumineux. Découvrez comment définir les coordinateurs et flux de travail Oozie et soumettre des tâches."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="jgao"/>


# <a name="use-time-based-oozie-coordinator-with-hadoop-in-hdinsight-to-define-workflows-and-coordinate-jobs"></a>Utilisez temporelles Oozie coordinateur avec Hadoop dans HDInsight pour définir le flux de travail et coordonner des tâches

Dans cet article, vous allez découvrir comment définir des flux de travail et les coordinateurs et comment déclencher les tâches coordinateur, en fonction de date. Il est utile de traitée [Oozie utilisation avec HDInsight] [ hdinsight-use-oozie] avant de lire cet article. Outre Oozie, vous pouvez également planifier des tâches à l’aide d’Azure Data Factory. Pour plus d’Azure Data Factory, voir [utiliser cochon et Hive avec Data Factory](../data-factory/data-factory-data-transformation-activities.md).

> [AZURE.NOTE] Cet article suppose qu’un cluster HDInsight fonctionnant sous Windows. Pour plus d’informations sur l’utilisation de Oozie, y compris les travaux temporelles, sur un cluster Linux, voir [Utiliser Oozie avec Hadoop pour définir et exécuter un flux de travail sur HDInsight basé sur Linux](hdinsight-use-oozie-linux-mac.md)

##<a name="what-is-oozie"></a>Quelles sont les Oozie

Oozie Apache est un système de flux de travail/débute qui gère les travaux Hadoop. Il est intégré à la pile Hadoop, et il prend en charge Hadoop travaux pour Apache MapReduce cochon Apache, Apache ruche et Apache Sqoop. Il peut également être utilisé pour planifier des tâches spécifiques à un système, tels que les programmes Java ou des scripts shell.

L’image suivante montre le flux de travail que vous allez implémenter :

![Diagramme de flux de travail][img-workflow-diagram]

Le flux de travail contient deux actions suivantes :

1. Une action Hive exécute un script HiveQL pour compter le nombre d’occurrences de chaque type de niveau de journalisation dans un fichier journal log4j. Chaque journal log4j est constitué d’une ligne de champs qui contient un champ [niveau de journalisation] pour afficher le type et la gravité, par exemple :

        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...

    Le résultat de script Hive est semblable à :

        [DEBUG] 434
        [ERROR] 3
        [FATAL] 1
        [INFO]  96
        [TRACE] 816
        [WARN]  4

    Pour plus d’informations sur Hive, voir [Utiliser la ruche avec HDInsight][hdinsight-use-hive].

2.  Une action Sqoop exporte le résultat de l’action HiveQL à une table dans une base de données SQL Azure. Pour plus d’informations sur Sqoop, voir [Utiliser Sqoop avec HDInsight][hdinsight-use-sqoop].

> [AZURE.NOTE] Versions Oozie prises en charge sur les clusters HDInsight, voir [Quelles sont les nouveautés dans les versions cluster fournies par HDInsight ?] [hdinsight-versions].


##<a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Un poste de travail avec Azure PowerShell**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- **HDInsight un cluster**. Pour plus d’informations sur la création d’un cluster de HDInsight, voir [créer HDInsight clusters][hdinsight-provision], ou [prise en main HDInsight][hdinsight-get-started]. Vous devez les données suivantes pour parcourir le didacticiel :

    <table border = "1">
    <tr><th>Propriété cluster</th><th>Nom de la variable Windows PowerShell</th><th>Valeur</th><th>Description</th></tr>
    <tr><td>Nom du cluster HDInsight</td><td>$clusterName</td><td></td><td>Le cluster HDInsight sur lequel vous exécuterez ce didacticiel.</td></tr>
    <tr><td>Nom d’utilisateur de cluster HDInsight</td><td>$clusterUsername</td><td></td><td>Le nom d’utilisateur cluster HDInsight. </td></tr>
    <tr><td>Mot de passe utilisateur HDInsight cluster </td><td>$clusterPassword</td><td></td><td>Le mot de passe HDInsight cluster utilisateur.</td></tr>
    <tr><td>Nom de compte de stockage Azure</td><td>$storageAccountName</td><td></td><td>Un compte de stockage Azure disponible pour le cluster HDInsight. Pour ce didacticiel, utilisez le compte de stockage par défaut que vous avez spécifié lors du processus de mise en service cluster.</td></tr>
    <tr><td>Nom du conteneur Blob Azure</td><td>$containerName</td><td></td><td>Dans cet exemple, utilisez le conteneur de stockage Blob Azure qui est utilisé pour le système de fichiers de cluster HDInsight par défaut. Par défaut, il a le même nom que le cluster HDInsight.</td></tr>
    </table>

- **Base de données SQL Azure un**. Vous devez configurer une règle de pare-feu pour le serveur de base de données SQL autoriser l’accès à partir de votre poste de travail. Pour obtenir des instructions sur la création d’un SQL Azure de base de données et la configuration du pare-feu, voir [prise en main à l’aide de la base de données SQL Azure][sqldatabase-get-started]. Cet article fournit un script Windows PowerShell pour la création de la table de base de données SQL Azure dont vous avez besoin de ce didacticiel.

    <table border = "1">
    <tr><th>Propriété de base de données SQL</th><th>Nom de la variable Windows PowerShell</th><th>Valeur</th><th>Description</th></tr>
    <tr><td>Nom du serveur de base de données SQL</td><td>$sqlDatabaseServer</td><td></td><td>Le serveur de base de données SQL auquel Sqoop exporter des données. </td></tr>
    <tr><td>Nom de connexion de base de données SQL</td><td>$sqlDatabaseLogin</td><td></td><td>Nom de connexion de base de données SQL.</td></tr>
    <tr><td>Mot de passe de connexion de base de données SQL</td><td>$sqlDatabaseLoginPassword</td><td></td><td>Mot de passe de base de données SQL.</td></tr>
    <tr><td>Nom de base de données SQL</td><td>$sqlDatabaseName</td><td></td><td>La base de données SQL Azure auquel Sqoop exporter des données. </td></tr>
    </table>

    > [AZURE.NOTE] Par défaut une base de données SQL Azure autorise les connexions à partir d’Azure Services, tels que Azure HDInsight. Si ce paramètre de pare-feu est désactivé, vous devez l’activer à partir du portail Azure. Pour obtenir des instructions sur la création d’une base de données SQL et en configurant des règles de pare-feu, voir [créer et configurer la base de données SQL][sqldatabase-get-started].


> [AZURE.NOTE] Remplir les valeurs dans les tableaux. Il peut être utile pour passant par ce didacticiel.


##<a name="define-oozie-workflow-and-the-related-hiveql-script"></a>Définir des flux de travail Oozie et le script HiveQL connexe

Définitions des flux de travail Oozie sont écrits en hPDL (un processus définition du langage XML). Le nom de fichier de flux de travail par défaut est *workflow.xml*.  Vous enregistrez le fichier de flux de travail localement et déployer puis sur le cluster HDInsight à l’aide de PowerShell Azure plus loin dans ce didacticiel.

L’action Hive dans le flux de travail appelle un fichier de script HiveQL. Ce fichier de script contient trois HiveQL instructions :

1. **Instruction DROP la TABLE** supprime la table Hive log4j s’il existe.
2. **Instruction CREATE la TABLE** crée une table externe Hive du log4j, qui pointe vers l’emplacement du fichier journal log4j ;
3.  **L’emplacement du fichier journal log4j**. Le séparateur de champs est «, ». Le séparateur de ligne par défaut est « \n ». Une table externe Hive est utilisée pour éviter le fichier de données est supprimé de son emplacement d’origine, au cas où vous souhaitez exécuter le flux de travail Oozie plusieurs fois.
3. **Instruction insérer le remplacer** compte les occurrences de chaque type de niveau de journalisation de la table Hive log4j et il enregistre le résultat dans un emplacement de stockage Blob Azure.

**Remarque**: il existe un problème de chemin d’accès Hive connu. Vous exécuterez ce problème lors de l’envoi d’une tâche Oozie. Vous trouverez les instructions pour la résolution du problème Wiki TechNet : [HDInsight Hive erreur : Impossible de renommer][technetwiki-hive-error].

**Pour définir le fichier de script HiveQL devant être appelé par le flux de travail**

1. Créer un fichier texte avec le contenu suivant :

        DROP TABLE ${hiveTableName};
        CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
        INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

    Il existe trois variables utilisées dans le script :

    - ${hiveTableName}
    - ${hiveDataFolder}
    - ${hiveOutputFolder}

    Le fichier de définition du flux de travail (workflow.xml dans ce didacticiel) passe ces valeurs à ce script HiveQL en cours d’exécution.

2. Enregistrez le fichier en tant que **C:\Tutorials\UseOozie\useooziewf.hql** à l’aide de codage ANSI (ASCII). (Utilisez le bloc-notes si votre éditeur de texte ne fournit pas cette option). Ce fichier de script sera déployé sur le cluster HDInsight plus loin dans le didacticiel.



**Pour définir un flux de travail**

1. Créer un fichier texte avec le contenu suivant :

        <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
            <start to = "RunHiveScript"/>

            <action name="RunHiveScript">
                <hive xmlns="uri:oozie:hive-action:0.2">
                    <job-tracker>${jobTracker}</job-tracker>
                    <name-node>${nameNode}</name-node>
                    <configuration>
                        <property>
                            <name>mapred.job.queue.name</name>
                            <value>${queueName}</value>
                        </property>
                    </configuration>
                    <script>${hiveScript}</script>
                    <param>hiveTableName=${hiveTableName}</param>
                    <param>hiveDataFolder=${hiveDataFolder}</param>
                    <param>hiveOutputFolder=${hiveOutputFolder}</param>
                </hive>
                <ok to="RunSqoopExport"/>
                <error to="fail"/>
            </action>

            <action name="RunSqoopExport">
                <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                    <job-tracker>${jobTracker}</job-tracker>
                    <name-node>${nameNode}</name-node>
                    <configuration>
                        <property>
                            <name>mapred.compress.map.output</name>
                            <value>true</value>
                        </property>
                    </configuration>
                <arg>export</arg>
                <arg>--connect</arg>
                <arg>${sqlDatabaseConnectionString}</arg>
                <arg>--table</arg>
                <arg>${sqlDatabaseTableName}</arg>
                <arg>--export-dir</arg>
                <arg>${hiveOutputFolder}</arg>
                <arg>-m</arg>
                <arg>1</arg>
                <arg>--input-fields-terminated-by</arg>
                <arg>"\001"</arg>
                </sqoop>
                <ok to="end"/>
                <error to="fail"/>
            </action>

            <kill name="fail">
                <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
            </kill>

           <end name="end"/>
        </workflow-app>

    Il existe deux actions définies dans le flux de travail. L’action à démarrer est *RunHiveScript*. Si l’action s’exécute *OK*, la prochaine action sera *RunSqoopExport*.

    La RunHiveScript comporte plusieurs variables. Vous allez passer les valeurs lorsque vous envoyez le travail Oozie à partir de votre poste de travail à l’aide de PowerShell Azure.

    <table border = "1">
    <tr><th>Variables du flux de travail</th><th>Description</th></tr>
    <tr><td>${jobTracker}</td><td>Spécifiez l’URL du dispositif de suivi du travail Hadoop. Utiliser <strong>jobtrackerhost:9010</strong> sur HDInsight cluster version 3.0 et 2.0.</td></tr>
    <tr><td>${nameNode}</td><td>Spécifiez l’URL du nœud nom Hadoop. Utiliser le wasbs de système de fichier par défaut : / / d’adresses, par exemple, <i>wasbs : / /&lt;containerName&gt;@&lt;storageAccountName&gt;. blob.core.windows.net</i>.</td></tr>
    <tr><td>${Queue}</td><td>Spécifie le nom de file d’attente qui sera envoyée à la tâche. Utilisez la <strong>valeur par défaut</strong>.</td></tr>
    </table>


    <table border = "1">
    <tr><th>Variable d’action Hive</th><th>Description</th></tr>
    <tr><td>${hiveDataFolder}</td><td>Le répertoire source de la commande ruche créer une Table.</td></tr>
    <tr><td>${hiveOutputFolder}</td><td>Le dossier de sortie pour l’instruction Insérer remplacer.</td></tr>
    <tr><td>${hiveTableName}</td><td>Le nom de la table Hive qui fait référence aux fichiers de données log4j.</td></tr>
    </table>


    <table border = "1">
    <tr><th>Variable d’action Sqoop</th><th>Description</th></tr>
    <tr><td>${sqlDatabaseConnectionString}</td><td>Chaîne de connexion de base de données SQL.</td></tr>
    <tr><td>${sqlDatabaseTableName}</td><td>La table de base de données SQL Azure à l’endroit où les données seront exportées.</td></tr>
    <tr><td>${hiveOutputFolder}</td><td>Le dossier de sortie pour l’instruction ruche insérer remplacer. C’est le même dossier pour l’exportation Sqoop (exporter-dir).</td></tr>
    </table>

    Pour plus d’informations sur les flux de travail Oozie et en utilisant les actions de flux de travail, voir [documentation Apache Oozie 4.0] [ apache-oozie-400] (pour la version de cluster HDInsight 3.0) ou la [documentation Apache Oozie 3.3.2] [ apache-oozie-332] (pour la version de cluster HDInsight 2.1).

2. Enregistrez le fichier en tant que **C:\Tutorials\UseOozie\workflow.xml** à l’aide de codage ANSI (ASCII). (Utilisez le bloc-notes si votre éditeur de texte ne fournit pas cette option).

**Pour définir coordinateur**

1. Créer un fichier texte avec le contenu suivant :

        <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
           <action>
              <workflow>
                 <app-path>${wfPath}</app-path>
              </workflow>
           </action>
        </coordinator-app>

    Il existe cinq variables utilisées dans le fichier de définition :

  	| Variable          | Description |
  	| ------------------|------------ |
  	| ${coordFrequency} | Durée de pause la tâche. Fréquence est toujours exprimée en minutes. |
  	| ${coordStart}     | Heure de début du projet. |
  	| ${coordEnd}       | Heure de fin de tâche. |
  	| ${coordTimezone}  | Oozie traite les travaux coordinateur dans un fuseau horaire fixe avec aucune heure (généralement représentée à l’aide de UTC). Ce fuseau horaire est appelé « horaire de traitement Oozie ». |
  	| ${wfPath}         | Le chemin d’accès pour workflow.xml.  Si le nom du fichier du flux de travail n’est pas le nom de fichier par défaut (workflow.xml), vous devez le spécifier. |

2. Enregistrez le fichier en tant que **C:\Tutorials\UseOozie\coordinator.xml** en utilisant le codage ANSI (ASCII). (Utilisez le bloc-notes si votre éditeur de texte ne fournit pas cette option).

##<a name="deploy-the-oozie-project-and-prepare-the-tutorial"></a>Déployer le projet Oozie et préparer le didacticiel

Vous allez exécuter un script PowerShell Azure pour effectuer les opérations suivantes :

- Copiez le script HiveQL (useoozie.hql) vers le stockage Blob Azure, wasbs:///tutorials/useoozie/useoozie.hql.
- Copiez workflow.xml vers wasbs:///tutorials/useoozie/workflow.xml.
- Copiez coordinator.xml dans wasbs:///tutorials/useoozie/coordinator.xml.
- Copier le fichier de données (/ example/data/sample.log) à wasbs:///tutorials/useoozie/data/sample.log.
- Créer une table de base de données SQL Azure pour stocker les données d’exportation Sqoop. Le nom de table est *log4jLogCount*.

**Comprendre le stockage de HDInsight**

HDInsight utilise le stockage Blob Azure pour stocker les données. wasbs : / / est l’implémentation Microsoft du système de fichiers Hadoop distribué (HDFS) dans le stockage Blob Azure. Pour plus d’informations, voir [utiliser Azure Blob storage avec HDInsight][hdinsight-storage].

Lorsque vous configurez un cluster de HDInsight, un compte de stockage Blob Azure et un conteneur spécifique de ce compte est désigné comme le système de fichiers par défaut, comme dans HDFS. En plus de ce compte de stockage, vous pouvez ajouter des comptes de stockage supplémentaire à partir de la même abonnement Azure ou différents abonnements Azure pendant le processus de mise en service. Pour des instructions sur l’ajout de comptes d’espace de stockage supplémentaire, voir [mise en service HDInsight clusters][hdinsight-provision]. Pour simplifier le script PowerShell Azure utilisé dans ce didacticiel, tous les fichiers sont stockés dans le conteneur de système de fichier par défaut situé à */tutorials/useoozie*. Par défaut, ce conteneur a le même nom que le nom du cluster HDInsight.
La syntaxe est la suivante :

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [AZURE.NOTE] Uniquement la *wasb : / /* syntaxe est prise en charge dans la version de cluster HDInsight 3.0. L’ancien *asv : / /* syntaxe est prise en charge dans HDInsight 2.1 et 1,6 clusters, mais il n’est pas pris en charge dans les clusters HDInsight 3.0.

> [AZURE.NOTE] Le wasb : / / chemin d’accès est un chemin d’accès virtuel. Pour plus d’informations, voir [utiliser Azure Blob storage avec HDInsight][hdinsight-storage].

Un fichier stocké dans le conteneur de système de fichier par défaut est accessible à partir de HDInsight à l’aide d’un des URI suivants (j’utilise workflow.xml par exemple) :

    wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
    wasbs:///tutorials/useoozie/workflow.xml
    /tutorials/useoozie/workflow.xml

Si vous voulez accéder au fichier directement à partir du compte de stockage, le nom du fichier blob est la suivante :

    tutorials/useoozie/workflow.xml

**Comprendre les tables internes et externes Hive**

Il existe plusieurs choses que vous devez savoir sur les tables internes et externes Hive :

- La commande CREATE TABLE crée une table interne, également connu sous le nom d’une table gérée. Le fichier de données doit se trouver dans le conteneur par défaut.
- La commande Créer un tableau déplace le fichier de données vers le /hive/entrepôt/<TableName> dossier dans le conteneur par défaut.
- La commande Créer une TABLE externe crée une table externe. Le fichier de données peut résider en dehors du conteneur par défaut.
- La commande Créer une TABLE externe ne bouge pas le fichier de données.
- La commande Créer une TABLE externe ne permet pas de tous les sous-dossiers sous le dossier spécifié dans la clause d’emplacement. Il s’agit de la raison pour laquelle pourquoi le didacticiel effectue une copie du fichier exemple.log.

Pour plus d’informations, voir [HDInsight : ruche internes et externes Tables introduction][cindygross-hive-tables].

**Pour préparer le didacticiel**

1. Ouvrir Windows PowerShell ISE (dans l’écran de démarrage de Windows 8, tapez **PowerShell_ISE**, puis sur **Windows PowerShell ISE**. Pour plus d’informations, consultez [Démarrer de Windows PowerShell sur Windows 8 et Windows][powershell-start]).
2. Dans le volet inférieur, exécutez la commande suivante pour vous connecter à votre abonnement Azure :

        Add-AzureAccount

    Vous devrez entrer vos informations d’identification de compte Azure. Cette méthode de l’ajout d’une connexion abonnement arrive à expiration et après le 12 heures, vous devez exécuter l’applet de commande à nouveau.

    > [AZURE.NOTE] Si vous avez plusieurs abonnements Azure et l’abonnement par défaut n’est pas celui que vous voulez faire, utilisez l’applet de commande <strong>Sélectionner AzureSubscription</strong> pour sélectionner un abonnement.

3. Copiez le script suivant dans la fenêtre de script et définissez les six premiers variables :

        # WASB variables
        $storageAccountName = "<StorageAccountName>"
        $containerName = "<BlobStorageContainerName>"

        # SQL database variables
        $sqlDatabaseServer = "<SQLDatabaseServerName>"  
        $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
        $sqlDatabaseLoginPassword = "SQLDatabaseLoginPassword>"
        $sqlDatabaseName = "<SQLDatabaseName>"  
        $sqlDatabaseTableName = "log4jLogsCount"

        # Oozie files for the tutorial
        $hiveQLScript = "C:\Tutorials\UseOozie\useooziewf.hql"
        $workflowDefinition = "C:\Tutorials\UseOozie\workflow.xml"
        $coordDefinition =  "C:\Tutorials\UseOozie\coordinator.xml"

        # WASB folder for storing the Oozie tutorial files.
        $destFolder = "tutorials/useoozie"  # Do NOT use the long path here


    Pour obtenir une description plus des variables, consultez la section [conditions préalables](#prerequisites) dans ce didacticiel.

3. Ajoutez le code suivant pour le script dans le volet de script :

        # Create a storage context object
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        function uploadOozieFiles()
        {
            Write-Host "Copy HiveQL script, workflow definition and coordinator definition ..." -ForegroundColor Green
            Set-AzureStorageBlobContent -File $hiveQLScript -Container $containerName -Blob "$destFolder/useooziewf.hql" -Context $destContext
            Set-AzureStorageBlobContent -File $workflowDefinition -Container $containerName -Blob "$destFolder/workflow.xml" -Context $destContext
            Set-AzureStorageBlobContent -File $coordDefinition -Container $containerName -Blob "$destFolder/coordinator.xml" -Context $destContext
        }

        function prepareHiveDataFile()
        {
            Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green
            Start-CopyAzureStorageBlob -SrcContainer $containerName -SrcBlob "example/data/sample.log" -Context $destContext -DestContainer $containerName -destBlob "$destFolder/data/sample.log" -DestContext $destContext
        }

        function prepareSQLDatabase()
        {
            # SQL query string for creating log4jLogsCount table
            $cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                    [Level] [nvarchar](10) NOT NULL,
                    [Total] float,
                CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
                (
                [Level] ASC
                )
                )"

            #Create the log4jLogsCount table
            Write-Host "Create Log4jLogsCount table ..." -ForegroundColor Green
            $conn = New-Object System.Data.SqlClient.SqlConnection
            $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
            $conn.open()
            $cmd = New-Object System.Data.SqlClient.SqlCommand
            $cmd.connection = $conn
            $cmd.commandtext = $cmdCreateLog4jCountTable
            $cmd.executenonquery()

            $conn.close()
        }

        # upload workflow.xml, coordinator.xml, and ooziewf.hql
        uploadOozieFiles;

        # make a copy of example/data/sample.log to example/data/log4j/sample.log
        prepareHiveDataFile;

        # create log4jlogsCount table on SQL database
        prepareSQLDatabase;

4. Cliquez sur **Exécuter le Script** ou appuyez sur **F5** pour exécuter le script. Le résultat sera semblable à :

    ![Résultat de la préparation du didacticiel][img-preparation-output]

##<a name="run-the-oozie-project"></a>Exécutez le projet Oozie

PowerShell Azure actuellement ne fournit les applets de commande pour définir des tâches Oozie. Vous pouvez utiliser l’applet de commande **Appeler RestMethod** pour appeler des services web Oozie. L’API de services web Oozie est une API HTTP REST JSON. Pour plus d’informations sur les services web Oozie API, voir [documentation Apache Oozie 4.0] [ apache-oozie-400] (pour la version de cluster HDInsight 3.0) ou la [documentation Apache Oozie 3.3.2] [ apache-oozie-332] (pour la version de cluster HDInsight 2.1).

**Soumettre une tâche Oozie**

1. Ouvrir Windows PowerShell ISE (dans l’écran de démarrage de Windows 8, tapez **PowerShell_ISE**, puis sur **Windows PowerShell ISE**. Pour plus d’informations, consultez [Démarrer de Windows PowerShell sur Windows 8 et Windows][powershell-start]).

3. Copiez le script suivant dans la fenêtre de script et définissez les variables d’abord 14 (Toutefois, ignorer **$storageUri**).

        #HDInsight cluster variables
        $clusterName = "<HDInsightClusterName>"
        $clusterUsername = "<HDInsightClusterUsername>"
        $clusterPassword = "<HDInsightClusterUserPassword>"

        #Azure Blob storage (WASB) variables
        $storageAccountName = "<StorageAccountName>"
        $storageContainerName = "<BlobContainerName>"
        $storageUri="wasbs://$storageContainerName@$storageAccountName.blob.core.windows.net"

        #Azure SQL database variables
        $sqlDatabaseServer = "<SQLDatabaseServerName>"
        $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
        $sqlDatabaseLoginPassword = "<SQLDatabaseloginPassword>"
        $sqlDatabaseName = "<SQLDatabaseName>"  

        #Oozie WF/coordinator variables
        $coordStart = "2014-03-21T13:45Z"
        $coordEnd = "2014-03-21T13:45Z"
        $coordFrequency = "1440"    # in minutes, 24h x 60m = 1440m
        $coordTimezone = "UTC"  #UTC/GMT

        $oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
        $waitTimeBetweenOozieJobStatusCheck=10

        #Hive action variables
        $hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
        $hiveTableName = "log4jlogs"
        $hiveDataFolder = "$storageUri/tutorials/useoozie/data"
        $hiveOutputFolder = "$storageUri/tutorials/useoozie/output"

        #Sqoop action variables
        $sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServer.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServer;password=$sqlDatabaseLoginPassword;database=$sqlDatabaseName"
        $sqlDatabaseTableName = "log4jLogsCount"

        $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
        $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    Pour obtenir une description plus des variables, consultez la section [conditions préalables](#prerequisites) dans ce didacticiel.

    $coordstart et $coordend sont le flux de travail début et heure de fin. Pour déterminer l’heure au format UTC/GMT, recherchez « heure utc » sur bing.com. La $coordFrequency est la fréquence à laquelle vous souhaitez exécuter le flux de travail de minutes.

3. Ajoutez le code suivant pour le script. Cet article définit la charge utile Oozie :

        #OoziePayload used for Oozie web service submission
        $OoziePayload =  @"
        <?xml version="1.0" encoding="UTF-8"?>
        <configuration>

           <property>
               <name>nameNode</name>
               <value>$storageUrI</value>
           </property>

           <property>
               <name>jobTracker</name>
               <value>jobtrackerhost:9010</value>
           </property>

           <property>
               <name>queueName</name>
               <value>default</value>
           </property>

           <property>
               <name>oozie.use.system.libpath</name>
               <value>true</value>
           </property>

           <property>
               <name>oozie.coord.application.path</name>
               <value>$oozieWFPath</value>
           </property>

           <property>
               <name>wfPath</name>
               <value>$oozieWFPath</value>
           </property>

           <property>
               <name>coordStart</name>
               <value>$coordStart</value>
           </property>

           <property>
               <name>coordEnd</name>
               <value>$coordEnd</value>
           </property>

           <property>
               <name>coordFrequency</name>
               <value>$coordFrequency</value>
           </property>

           <property>
               <name>coordTimezone</name>
               <value>$coordTimezone</value>
           </property>

           <property>
               <name>hiveScript</name>
               <value>$hiveScript</value>
           </property>

           <property>
               <name>hiveTableName</name>
               <value>$hiveTableName</value>
           </property>

           <property>
               <name>hiveDataFolder</name>
               <value>$hiveDataFolder</value>
           </property>

           <property>
               <name>hiveOutputFolder</name>
               <value>$hiveOutputFolder</value>
           </property>

           <property>
               <name>sqlDatabaseConnectionString</name>
               <value>&quot;$sqlDatabaseConnectionString&quot;</value>
           </property>

           <property>
               <name>sqlDatabaseTableName</name>
               <value>$SQLDatabaseTableName</value>
           </property>

           <property>
               <name>user.name</name>
               <value>admin</value>
           </property>

        </configuration>
        "@

    >[AZURE.NOTE] La seule différence par rapport à du fichier de charge de soumission du flux de travail est variable **oozie.coord.application.path**. Lorsque vous envoyez une tâche de flux de travail, vous utilisez **oozie.wf.application.path** à la place.

4. Ajoutez le code suivant pour le script. Ce composant vérifie l’état du service web Oozie :

        function checkOozieServerStatus()
        {
            Write-Host "Checking Oozie server status..." -ForegroundColor Green
            $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus

            $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
            $oozieServerSatus = $jsonResponse[0].("systemMode")
            Write-Host "Oozie server status is $oozieServerSatus..."

            if($oozieServerSatus -notmatch "NORMAL")
            {
                Write-Host "Oozie server status is $oozieServerSatus...cannot submit Oozie jobs. Check the server status and re-run the job."
                exit 1
            }
        }

5. Ajoutez le code suivant pour le script. Ce composant crée une tâche Oozie :

        function createOozieJob()
        {
            # create Oozie job
            Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
            Write-Host "`n--------`n$OoziePayload`n--------"
            $clusterUriCreateJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
            $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $creds -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName -debug -Verbose

            $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
            $oozieJobId = $jsonResponse[0].("id")
            Write-Host "Oozie job id is $oozieJobId..."

            return $oozieJobId
        }

    > [AZURE.NOTE] Lorsque vous envoyez une tâche de flux de travail, vous devez effectuer un autre service web appeler pour démarrer le travail une fois que le travail est créé. Dans ce cas, le travail coordinateur est déclenché par heure. La tâche démarre automatiquement.

6. Ajoutez le code suivant pour le script. Ce composant vérifie l’état du travail Oozie :

        function checkOozieJobStatus($oozieJobId)
        {
            # get job status
            Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
            Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck

            Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
            $clusterUriGetJobStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds
            $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
            $JobStatus = $jsonResponse[0].("status")

            while($JobStatus -notmatch "SUCCEEDED|KILLED")
            {
                Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
                Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
                $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds
                $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
                $JobStatus = $jsonResponse[0].("status")
            }

            Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!"
            if($JobStatus -notmatch "SUCCEEDED")
            {
                Write-Host "Check logs at http://headnode0:9014/cluster for detais."
                exit -1
            }
        }

7. (Facultatif) Ajoutez le code suivant pour le script.

        function listOozieJobs()
        {
            Write-Host "Listing Oozie jobs..." -ForegroundColor Green
            $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds

            write-host "Job ID                                   App Name        Status      Started                         Ended"
            write-host "----------------------------------------------------------------------------------------------------------------------------------"
            foreach($job in $response.workflows)
            {
                Write-Host $job.id "`t" $job.appName "`t" $job.status "`t" $job.startTime "`t" $job.endTime
            }
        }

        function ShowOozieJobLog($oozieJobId)
        {
            Write-Host "Showing Oozie job info..." -ForegroundColor Green
            $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/$oozieJobId" + "?show=log"
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds
            write-host $response
        }

        function killOozieJob($oozieJobId)
        {
            Write-Host "Killing the Oozie job $oozieJobId..." -ForegroundColor Green
            $clusterUriStartJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=kill" #Valid values for the 'action' parameter are 'start', 'suspend', 'resume', 'kill', 'dryrun', 'rerun', and 'change'.
            $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $creds | Format-Table -HideTableHeaders -debug
        }

7. Ajoutez le code suivant pour le script :

        checkOozieServerStatus
        # listOozieJobs
        $oozieJobId = createOozieJob($oozieJobId)
        checkOozieJobStatus($oozieJobId)
        # ShowOozieJobLog($oozieJobId)
        # killOozieJob($oozieJobId)

    Supprimez les signes # si vous souhaitez exécuter les fonctions supplémentaires.

7. Si votre cluster HDinsight version 2.1, remplacez « https://$clusterName.azurehdinsight.net:443/oozie/v2/ » par « https://$clusterName.azurehdinsight.net:443/oozie/v1/ ». HDInsight cluster version 2.1 ne pas prend en charge version 2 des services web.

7. Cliquez sur **Exécuter le Script** ou appuyez sur **F5** pour exécuter le script. Le résultat sera semblable à :

    ![Didacticiel exécutez sortie de flux de travail][img-runworkflow-output]

8. Se connecter à votre base de données SQL pour afficher les données exportées.

**Pour vérifier le journal des erreurs de travail**

Pour résoudre les problèmes d’un flux de travail, le fichier journal Oozie trouverez C:\apps\dist\oozie-3.3.2.1.3.2.0-05\oozie-win-distro\logs\Oozie.log à partir de la headnode cluster. Pour plus d’informations sur RDP, consultez [clusters administration HDInsight à l’aide du portail Azure][hdinsight-admin-portal].

**Réexécuter le didacticiel**

Pour réexécuter le flux de travail, vous devez effectuer les tâches suivantes :

- Supprimez le fichier de sortie script Hive.
- Supprimer les données dans la table log4jLogsCount.

Voici un exemple de script Windows PowerShell que vous pouvez utiliser :

    $storageAccountName = "<AzureStorageAccountName>"
    $containerName = "<ContainerName>"

    #SQL database variables
    $sqlDatabaseServer = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"
    $sqlDatabaseTableName = "log4jLogsCount"

    Write-host "Delete the Hive script output file ..." -ForegroundColor Green
    $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
    Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $containerName

    Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
    $conn.open()
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.connection = $conn
    $cmd.commandtext = "delete from $sqlDatabaseTableName"
    $cmd.executenonquery()

    $conn.close()


##<a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris comment définir un flux de travail Oozie et un coordinateur Oozie et comment exécuter une tâche de coordinateur Oozie à l’aide de PowerShell Azure. Pour plus d’informations, consultez les articles suivants :

- [Prise en main HDInsight][hdinsight-get-started]
- [Associer stockage d’objets Blob Azure HDInsight][hdinsight-storage]
- [Administrer HDInsight à l’aide de PowerShell Azure][hdinsight-admin-powershell]
- [Télécharger des données à HDInsight][hdinsight-upload-data]
- [Utiliser Sqoop avec HDInsight][hdinsight-use-sqoop]
- [Utiliser Hive avec HDInsight][hdinsight-use-hive]
- [Utiliser cochon avec HDInsight][hdinsight-use-pig]
- [Développer des programmes Java MapReduce pour HDInsight][hdinsight-develop-java-mapreduce]



[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563


[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md


[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-develop-java-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md

[sqldatabase-get-started]: ../sql-database/sql-database-get-started.md

[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: ../powershell-install-configure.md
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Preparation.Output1.png  
[img-runworkflow-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.RunCoord.Output.png  

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
