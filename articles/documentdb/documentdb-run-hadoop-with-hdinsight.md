<properties
    pageTitle="Exécuter une tâche Hadoop à l’aide de DocumentDB et HDInsight | Microsoft Azure"
    description="Découvrez comment exécuter une tâche Hive cochon et MapReduce simple avec DocumentDB et Azure HDInsight."
    services="documentdb"
    authors="dennyglee"
    manager="jhubbard"
    editor="mimig"
    documentationCenter=""/>


<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="denlee"/>

#<a name="DocumentDB-HDInsight"></a>Exécuter une tâche Hadoop à l’aide de DocumentDB et HDInsight

Ce didacticiel vous montre comment exécuter [Apache ruche][apache-hive], [Apache cochon][apache-pig]et [Apache Hadoop] [ apache-hadoop] travaux MapReduce sur Azure HDInsight avec connecteur de Hadoop de DocumentDB. Le connecteur de DocumentDB Hadoop permet DocumentDB pour l’utiliser comme une source et le récepteur pour les travaux Hive cochon et MapReduce. Ce didacticiel utilise DocumentDB en tant que source de données et de destination pour les travaux Hadoop.

Au terme de ce didacticiel, vous pourrez répondre aux questions suivantes :

- Comment charger les données à partir de DocumentDB à l’aide d’une tâche Hive, cochon ou MapReduce ?
- Comment stocker des données dans DocumentDB à l’aide d’une tâche Hive, cochon ou MapReduce ?

Nous vous recommandons de mise en route en regardant la vidéo suivante, où nous exécuter une tâche Hive à l’aide de DocumentDB et HDInsight.

> [AZURE.VIDEO use-azure-documentdb-hadoop-connector-with-azure-hdinsight]

Revenez ensuite à cet article, où vous recevrez les détails complets sur l’exécution de tâches analytique sur vos données DocumentDB.

> [AZURE.TIP] Ce didacticiel suppose que vous avez expérience antérieure à l’aide de Apache Hadoop, Hive et/ou cochon. Si vous débutez dans Apache Hadoop, Hive et cochon, nous vous recommandons de consulter la [documentation Apache Hadoop][apache-hadoop-doc]. Ce didacticiel suppose également que vous avez une connaissance préalable de DocumentDB et que vous disposez d’un compte DocumentDB. Si vous ne connaissez pas DocumentDB ou vous n’avez pas un compte DocumentDB, consultez notre [Mise en route] [ getting-started] page.

Ne pas avoir le temps pour terminer le didacticiel et vous voulez simplement obtenir des exemples complète de scripts PowerShell pour Hive cochon et MapReduce ? Pas un problème, les obtenir [ici][documentdb-hdinsight-samples]. Le téléchargement contienne également les fichiers hql, cochon et java pour ces exemples.

## <a name="NewestVersion"></a>Version la plus récente

<table border='1'>
    <tr><th>Connecteur Hadoop Version</th>
        <td>1.2.0</td></tr>
    <tr><th>Uri de script</th>
        <td>https://portalcontent.BLOB.Core.Windows.NET/scriptaction/documentdb-Hadoop-installer-v04.ps1</td></tr>
    <tr><th>Date de modification</th>
        <td>26/04/2016</td></tr>
    <tr><th>Versions de HDInsight pris en charge</th>
        <td>3.1, 3.2</td></tr>
    <tr><th>Journal des modifications</th>
        <td>Mise à jour DocumentDB Java SDK pour 1.6.0</br>
            Prise en charge pour les collections partitionnées comme une source et le récepteur</br>
        </td></tr>
</table>

## <a name="Prerequisites"></a>Conditions préalables
Avant de suivre les instructions dans ce didacticiel, assurez-vous que vous avez les éléments suivants :

- Un compte DocumentDB, une base de données et une collection de sites avec des documents à l’intérieur. Pour plus d’informations, voir [Prise en main DocumentDB][getting-started]. Importer des exemples de données dans votre compte DocumentDB avec l' [outil d’importation de DocumentDB][documentdb-import-data].
- Débit. Lit et écrit à partir de HDInsight à compter vers vos unités demande alloué pour vos collections. Pour plus d’informations, voir [Provisioned débit, unités de demande et opérations de base de données][documentdb-manage-throughput].
- Capacité pour une procédure stockée supplémentaire au sein de chaque collection de sites de sortie. Les procédures stockées sont utilisés pour le transfert de documents qui en résulte. Pour plus d’informations, consultez [Collections et débit généré][documentdb-manage-document-storage].
- Capacité pour les documents à partir de tâches Hive, cochon ou MapReduce. Pour plus d’informations, consultez [performances et la capacité de gérer DocumentDB][documentdb-manage-collections].
- [*Facultatif*] Capacité pour une collection de sites supplémentaires. Pour plus d’informations, voir [stockage de documents Provisioned et index aérienne][documentdb-manage-document-storage].

> [AZURE.WARNING] Afin d’éviter la création d’une nouvelle collection au cours d’une des tâches, vous pouvez imprimer les résultats à stdout, enregistrer le résultat dans le conteneur de votre WASB ou spécifier une collection de sites existante. Dans le cas de spécifiant une collection de sites existante, nouveaux documents seront créés à l’intérieur de la collection de sites et documents déjà existants ne seront supprimés s’il existe un conflit dans *ID*. **Le connecteur remplacera automatiquement des documents existants avec id est en conflit**. Vous pouvez désactiver cette fonctionnalité en définissant l’option upsert sur false. Si upsert est faux et un conflit se produit, le travail Hadoop échoue ; signale une erreur de conflit de code.


## <a name="ProvisionHDInsight"></a>Étape 1 : Créer un nouveau cluster HDInsight
Ce didacticiel utilise Action de Script à partir du portail Azure pour personnaliser votre cluster HDInsight. Dans ce didacticiel, nous utiliserons le portail Azure pour créer votre cluster HDInsight. Pour obtenir des instructions sur l’utilisation des applets de commande PowerShell ou le Kit de développement .NET HDInsight, consultez les [clusters HDInsight personnaliser à l’aide de Script Action] [ hdinsight-custom-provision] article.

1. Connectez-vous au [portail Azure][azure-portal].

2. Cliquez sur **+ Nouveau** en haut de la navigation à gauche, recherchez **HDInsight** dans la barre de recherche sur la nouvelle carte.

3. **HDInsight** publié par **Microsoft** s’affichent en haut des résultats. Cliquez sur ce dernier, puis sur **créer**.

4. Sur le nouveau HDInsight Cluster créer carte, entrez votre **Nom de Cluster** et sélectionnez l' **abonnement** que vous voulez configurer cette ressource sous.

    <table border='1'>
        <tr><td>Nom du cluster</td><td>Nom du cluster.<br/>
   DNS nom doit commencer et se termine par un caractère alphanumérique et peut contenir des tirets.<br/>
   Le champ doit être une chaîne de caractères 3 à 63.</td></tr>
        <tr><td>Nom de l’abonnement</td>
            <td>Si vous avez plusieurs abonnements Azure, sélectionnez l’abonnement qui héberge votre cluster HDInsight. </td></tr>
    </table>

5. Cliquez sur **Sélectionner un Type de Cluster** et définissez les propriétés suivantes avec les valeurs spécifiées.

    <table border='1'>
        <tr><td>Type de cluster</td><td><strong>Hadoop</strong></td></tr>
        <tr><td>Cluster de niveau</td><td><strong>Standard</strong></td></tr>
        <tr><td>Système d'exploitation</td><td><strong>Windows</strong></td></tr>
        <tr><td>Version</td><td>version la plus récente</td></tr>
    </table>

    À présent, cliquez sur **Sélectionner**.

    ![Fournir des détails de cluster initial Hadoop HDInsight][image-customprovision-page1]

6. Cliquez sur **informations d’identification** pour configurer votre connexion et les informations d’identification d’accès à distance. Choisissez votre **nom d’utilisateur de connexion Cluster** et **Cluster mot de passe**.

    Si vous voulez distant dans votre cluster, sélectionnez *Oui* dans la partie inférieure de la cuillère et fournissez un nom d’utilisateur et mot de passe.

7. Cliquez sur **Source de données** pour définir votre emplacement principal pour accéder aux données. Choisissez la **Méthode de sélection** et spécifiez un compte de stockage déjà existant ou créez-en un.

8. Sur la même carte, spécifiez un **Conteneur par défaut** et un **emplacement**. Et, cliquez sur **Sélectionner**.

    > [AZURE.NOTE] Sélectionnez un emplacement près de votre région compte DocumentDB pour améliorer les performances

8. Cliquez sur **tarification** pour sélectionner le nombre et le type de nœuds. Vous pouvez conserver la configuration par défaut et augmenter le nombre de nœuds de travail ultérieurement.

9. Cliquez sur **Configuration facultative**, puis sur **Actions de Script** dans la carte de Configuration facultatives.

    Dans Script Actions, entrez les informations suivantes pour personnaliser votre cluster HDInsight.

    <table border='1'>
        <tr><th>Propriété</th><th>Valeur</th></tr>
        <tr><td>Nom</td>
            <td>Attribuez un nom à l’action de script.</td></tr>
        <tr><td>Script URI</td>
            <td>Spécifiez l’URI pour le script qui est appelé pour personnaliser le cluster.</br></br>
   Entrez : </br> <strong>https://portalcontent.blob.core.windows.net/scriptaction/documentdb-hadoop-installer-v04.ps1</strong>.</td></tr>
        <tr><td>Tête</td>
            <td>Cliquez sur la case à cocher pour exécuter le script PowerShell sur le serveur de nœud.</br></br>
            <strong>Activez cette case à cocher</strong>.</td></tr>
        <tr><td>Employé consultant</td>
            <td>Cliquez sur la case à cocher pour exécuter le script PowerShell sur le nœud concerné.</br></br>
            <strong>Activez cette case à cocher</strong>.</td></tr>
        <tr><td>Soigneur</td>
            <td>Cliquez sur la case à cocher pour exécuter le script PowerShell sur la soigneur.</br></br>
            <strong>Pas nécessaire</strong>.
            </td></tr>
        <tr><td>Paramètres</td>
            <td>Spécifier les paramètres, le cas échéant par le script.</br></br>
            <strong>Les paramètres non nécessaires</strong>.</td></tr>
    </table>

10. Créer un nouveau **Groupe de ressources** ou utiliser un groupe de ressources existant sous votre abonnement Azure.

11. À présent, **code confidentiel au tableau de bord** pour effectuer le suivi de son déploiement et cliquez sur **créer**!

## <a name="InstallCmdlets"></a>Étape 2 : Installer et configurer PowerShell Azure

1. Installez PowerShell Azure. Instructions sont accessibles [ici][powershell-install-configure].

    > [AZURE.NOTE] Par ailleurs, seulement les requêtes Hive, vous pouvez utiliser l’éditeur des ruche de HDInsight en ligne. Pour cela, connectez-vous au [Portail Azure][azure-portal], cliquez sur **HDInsight** dans le volet gauche pour afficher une liste de vos clusters HDInsight. Cliquez sur le cluster que vous voulez exécuter des requêtes Hive, puis cliquez sur **Console de requête**.

2. Ouvrez l’environnement d’écriture de script intégrée Azure PowerShell :
    - Sur un ordinateur exécutant Windows 8 ou Windows Server 2012 ou une version ultérieure, vous pouvez utiliser la recherche intégrée. À partir de l’écran Démarrer, tapez **powershell ise** , puis cliquez sur **entrée**.
    - Sur un ordinateur exécutant une version antérieure à Windows 8 ou Windows Server 2012, utilisez le menu Démarrer. Dans le menu Démarrer, tapez **invite de commandes** dans la zone de recherche, puis dans la liste des résultats, cliquez sur **invite de commandes**. Dans l’invite de commandes, tapez **powershell_ise** et appuyez sur **entrée**.

3. Ajouter votre compte Azure.
    1. Dans le volet Console, tapez **Ajouter AzureAccount** et appuyez sur **entrée**.
    2. Entrez l’adresse de messagerie associée à votre abonnement Azure, puis cliquez sur **Continuer**.
    3. Tapez le mot de passe pour votre abonnement Azure.
    4. Cliquez sur **se connecter**.

4. Le diagramme suivant identifie les parties importantes de votre environnement de l’écriture de script PowerShell Azure.

    ![Diagramme pour PowerShell Azure][azure-powershell-diagram]

## <a name="RunHive"></a>Étape 3 : Exécuter une tâche Hive à l’aide de DocumentDB et HDInsight

> [AZURE.IMPORTANT] Toutes les variables indiqués par < > doivent être renseignées à l’aide de vos paramètres de configuration.

1. Définir les variables suivantes dans votre volet PowerShell Script.

        # Provide Azure subscription name, the Azure Storage account and container that is used for the default HDInsight file system.
        $subscriptionName = "<SubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureStorageContainerName>"

        # Provide the HDInsight cluster name where you want to run the Hive job.
        $clusterName = "<HDInsightClusterName>"

2. <p>C’est parti construire votre chaîne de requête. Nous allons écrire une requête Hive qui conduit les horodatages généré par le système de tous les documents (DTS) et identificateurs uniques (_rid) à partir d’une collection de sites DocumentDB, comptabilise tous les documents par les minutes, et puis stocke les résultats à nouveau dans une nouvelle collection DocumentDB.</p>

    <p>Tout d’abord, nous allons créer une table Hive à partir de notre collection DocumentDB. Ajouter l’extrait de code suivantes au PowerShell Script volet <strong>après</strong> l’extrait de code à partir de 1 #. Veillez à inclure facultatif DocumentDB.query paramètre t SUPPRESPACE nos documents à DTS simplement et _rid.</p>

    > [AZURE.NOTE]**DocumentDB.inputCollections dénomination n’a pas d’erreur.** Oui, nous autorisent l’ajout de plusieurs collections comme entrée : </br>

        '*DocumentDB.inputCollections*' = '*\<DocumentDB Input Collection Name 1\>*,*\<DocumentDB Input Collection Name 2\>*' A1A</br> The collection names are separated without spaces, using only a single comma.


        # Create a Hive table using data from DocumentDB. Pass DocumentDB the query to filter transferred data to _rid and _ts.
        $queryStringPart1 = "drop table DocumentDB_timestamps; "  +
                            "create external table DocumentDB_timestamps(id string, ts BIGINT) "  +
                            "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' "  +
                            "tblproperties ( " +
                                "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                "'DocumentDB.key' = '<DocumentDB Primary Key>', " +
                                "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                "'DocumentDB.inputCollections' = '<DocumentDB Input Collection Name>', " +
                                "'DocumentDB.query' = 'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "

3.  Ensuite, nous allons créer une table Hive pour la collection de sortie. Les propriétés de document de sortie sera le mois, jour, heure, minute et le nombre total d’occurrences.

    > [AZURE.NOTE]**Encore, de la dénomination DocumentDB.outputCollections n’a pas d’erreur.** Oui, nous autoriser Ajout de plusieurs collections en tant que résultat : </br>
«*DocumentDB.outputCollections*'='*\<nom de la Collection DocumentDB sortie 1\>*,*\<nom de la Collection DocumentDB sortie 2\>*» </br> Les noms de collection de sites sont séparés sans espaces, à l’aide d’uniquement une virgule unique. </br></br>
Documents seront alternées distribuée sur plusieurs collections. Un lot de documents sera stocké dans une collection de sites, puis un deuxième lot de documents sera stocké dans la collection suivante et ainsi de suite.

        # Create a Hive table for the output data to DocumentDB.
        $queryStringPart2 = "drop table DocumentDB_analytics; " +
                              "create external table DocumentDB_analytics(Month INT, Day INT, Hour INT, Minute INT, Total INT) " +
                              "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' " +
                              "tblproperties ( " +
                                  "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                  "'DocumentDB.key' = '<DocumentDB Primary Key>', " +  
                                  "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                  "'DocumentDB.outputCollections' = '<DocumentDB Output Collection Name>' ); "

4. Pour finir, nous allons calculer les documents par mois, jour, heure et minute et réinsérez les résultats dans le résultat de la table Hive.

        # GROUP BY minute, COUNT entries for each, INSERT INTO output Hive table.
        $queryStringPart3 = "INSERT INTO table DocumentDB_analytics " +
                              "SELECT month(from_unixtime(ts)) as Month, day(from_unixtime(ts)) as Day, " +
                              "hour(from_unixtime(ts)) as Hour, minute(from_unixtime(ts)) as Minute, " +
                              "COUNT(*) AS Total " +
                              "FROM DocumentDB_timestamps " +
                              "GROUP BY month(from_unixtime(ts)), day(from_unixtime(ts)), " +
                              "hour(from_unixtime(ts)) , minute(from_unixtime(ts)); "

5. Ajouter l’extrait de script suivant pour créer une définition de travail Hive à partir de la requête précédente.

        # Create a Hive job definition.
        $queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
        $hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString

    Vous pouvez également utiliser le fichier commutateur - pour spécifier un fichier de script HiveQL sur HDFS.

6. Ajouter l’extrait suivant pour enregistrer l’heure de début et soumettre le travail Hive.

        # Save the start time and submit the job to the cluster.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition

7. Ajoutez le code suivant pour attendre la fin du travail Hive.

        # Wait for the Hive job to complete.
        Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600

8. Ajoutez le code suivant pour imprimer la sortie standard et les heures de début et de fin.

        # Print the standard error, the standard output of the Hive job, and the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green

9. **Exécuter** le script nouveau ! **Cliquez sur** le bouton Exécuter vert.

10. Vérifiez les résultats. Se connecter au [portail Azure][azure-portal].
    1. Dans le volet gauche, cliquez sur <strong>Parcourir</strong> . </br>
    2. Cliquez sur <strong>tout</strong> en haut à droite du volet de navigation. </br>
    3. Recherchez et cliquez sur <strong>Comptes DocumentDB</strong>. </br>
    4. Ensuite, recherchez votre <strong>Compte DocumentDB</strong>, puis <strong>DocumentDB de base de données</strong> et de votre <strong>Collection de sites DocumentDB</strong> associée à la collection de sortie spécifiée dans votre requête Hive.</br>
    5. Pour finir, cliquez sur <strong>Explorateur de Document</strong> , sous <strong>Outils de développement</strong>.</br></p>

    Vous verrez les résultats de votre requête Hive.

    ![Résultats de la requête Hive][image-hive-query-results]

## <a name="RunPig"></a>Étape 4 : Exécuter une tâche cochon avec DocumentDB et HDInsight

> [AZURE.IMPORTANT] Toutes les variables indiqués par < > doivent être renseignées à l’aide de vos paramètres de configuration.

1. Définir les variables suivantes dans votre volet PowerShell Script.

        # Provide Azure subscription name.
        $subscriptionName = "Azure Subscription Name"

        # Provide HDInsight cluster name where you want to run the Pig job.
        $clusterName = "Azure HDInsight Cluster Name"

2. <p>C’est parti construire votre chaîne de requête. Nous allons écrire une requête cochon qui conduit les horodatages généré par le système de tous les documents (DTS) et identificateurs uniques (_rid) à partir d’une collection de sites DocumentDB, comptabilise tous les documents par les minutes, et puis stocke les résultats à nouveau dans une nouvelle collection DocumentDB.</p>
    <p>Tout d’abord, chargez des documents à partir de DocumentDB à HDInsight. Ajouter l’extrait de code suivantes au PowerShell Script volet <strong>après</strong> l’extrait de code à partir de 1 #. N’oubliez pas d’ajouter une requête DocumentDB pour le paramètre de requête DocumentDB facultatif pour découper nos documents à DTS simplement et _rid.</p>

    > [AZURE.NOTE]Oui, nous autorisent l’ajout de plusieurs collections comme entrée : </br>
«*\<Nom de la Collection d’entrée DocumentDB 1\>*,*\<nom de la Collection d’entrée DocumentDB 2\>*»</br> Les noms de collection de sites sont séparés sans espaces, à l’aide d’uniquement une virgule unique. </b>

    Documents seront alternées distribuée sur plusieurs collections. Un lot de documents sera stocké dans une collection de sites, puis un deuxième lot de documents sera stocké dans la collection suivante et ainsi de suite.

        # Load data from DocumentDB. Pass DocumentDB query to filter transferred data to _rid and _ts.
        $queryStringPart1 = "DocumentDB_timestamps = LOAD '<DocumentDB Endpoint>' USING com.microsoft.azure.documentdb.pig.DocumentDBLoader( " +
                                                        "'<DocumentDB Primary Key>', " +
                                                        "'<DocumentDB Database Name>', " +
                                                        "'<DocumentDB Input Collection Name>', " +
                                                        "'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "

3.  Ensuite, nous allons calculer les documents par mois, jour, heure, minute et le nombre total d’occurrences.

        # GROUP BY minute and COUNT entries for each.
        $queryStringPart2 = "timestamp_record = FOREACH DocumentDB_timestamps GENERATE `$0#'id' as id:int, ToDate((long)(`$0#'ts') * 1000) as timestamp:datetime; " +
                            "by_minute = GROUP timestamp_record BY (GetYear(timestamp), GetMonth(timestamp), GetDay(timestamp), GetHour(timestamp), GetMinute(timestamp)); " +
                            "by_minute_count = FOREACH by_minute GENERATE FLATTEN(group) as (Year:int, Month:int, Day:int, Hour:int, Minute:int), COUNT(timestamp_record) as Total:int; "

4. Pour finir, nous allons stocker les résultats dans notre nouvelle collection de sortie.

    > [AZURE.NOTE]Oui, nous autoriser Ajout de plusieurs collections en tant que résultat : </br>
«\<Nom de la Collection DocumentDB sortie 1\>,\<nom de la Collection DocumentDB sortie 2\>»</br> Les noms de collection de sites sont séparés sans espaces, à l’aide d’uniquement une virgule unique.</br>
Documents seront alternées distribuée sur les plusieurs collections. Un lot de documents sera stocké dans une collection de sites, puis un deuxième lot de documents sera stocké dans la collection suivante et ainsi de suite.

        # Store output data to DocumentDB.
        $queryStringPart3 = "STORE by_minute_count INTO '<DocumentDB Endpoint>' " +
                            "USING com.microsoft.azure.documentdb.pig.DocumentDBStorage( " +
                                "'<DocumentDB Primary Key>', " +
                                "'<DocumentDB Database Name>', " +
                                "'<DocumentDB Output Collection Name>'); "

5. Ajouter l’extrait de script suivant pour créer une définition de travail cochon à partir de la requête précédente.

        # Create a Pig job definition.
        $queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
        $pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $queryString -StatusFolder $statusFolder

    Vous pouvez également utiliser le fichier commutateur - pour spécifier un fichier de script cochon sur HADOOP.

6. Ajouter l’extrait suivant pour enregistrer l’heure de début et soumettre le travail cochon.

        # Save the start time and submit the job to the cluster.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition

7. Ajoutez le code suivant pour attendre la fin du travail cochon.

        # Wait for the Pig job to complete.
        Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

8. Ajoutez le code suivant pour imprimer la sortie standard et les heures de début et de fin.

        # Print the standard error, the standard output of the Hive job, and the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green

9. **Exécuter** le script nouveau ! **Cliquez sur** le bouton Exécuter vert.

10. Vérifiez les résultats. Se connecter au [portail Azure][azure-portal].
    1. Dans le volet gauche, cliquez sur <strong>Parcourir</strong> . </br>
    2. Cliquez sur <strong>tout</strong> en haut à droite du volet de navigation. </br>
    3. Recherchez et cliquez sur <strong>Comptes DocumentDB</strong>. </br>
    4. Ensuite, recherchez votre <strong>Compte DocumentDB</strong>, puis <strong>DocumentDB de base de données</strong> et de votre <strong>Collection de sites DocumentDB</strong> associée à la collection de sortie spécifiée dans votre requête cochon.</br>
    5. Pour finir, cliquez sur <strong>Explorateur de Document</strong> , sous <strong>Outils de développement</strong>.</br></p>

    Vous verrez les résultats de votre requête cochon.

    ![Résultats de la requête cochon][image-pig-query-results]

## <a name="RunMapReduce"></a>Étape 5 : Exécuter une tâche MapReduce à l’aide de DocumentDB et HDInsight

1. Définir les variables suivantes dans votre volet PowerShell Script.

        $subscriptionName = "<SubscriptionName>"   # Azure subscription name
        $clusterName = "<ClusterName>"             # HDInsight cluster name

2. Nous allons exécuter une tâche MapReduce qui compte le nombre d’occurrences de chaque propriété du Document à partir de votre collection de sites DocumentDB. Ajouter ce script extrait **après** l’extrait de code ci-dessus.

        # Define the MapReduce job.
        $TallyPropertiesJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/TallyProperties-v01.jar" -ClassName "TallyProperties" -Arguments "<DocumentDB Endpoint>","<DocumentDB Primary Key>", "<DocumentDB Database Name>","<DocumentDB Input Collection Name>","<DocumentDB Output Collection Name>","<[Optional] DocumentDB Query>"

    > [AZURE.NOTE] TallyProperties v01.jar est fourni avec l’installation du connecteur Hadoop DocumentDB personnalisée.

3. Ajouter la commande suivante pour soumettre la tâche MapReduce.

        # Save the start time and submit the job.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $TallyPropertiesJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $TallyPropertiesJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

    Outre la définition du travail MapReduce, vous indiquez également le nom de cluster HDInsight où vous souhaitez exécuter la tâche MapReduce et les informations d’identification. Le début-AzureHDInsightJob est un appel asynchrone. Pour vérifier l’achèvement de la tâche, utilisez l’applet de commande *Attente AzureHDInsightJob* .

4. Ajouter la commande suivante pour vérifier les erreurs lors de l’exécution de la tâche MapReduce.

        # Get the job output and print the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $TallyPropertiesJob.JobId -StandardError
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green

5. **Exécuter** le script nouveau ! **Cliquez sur** le bouton Exécuter vert.

6. Vérifiez les résultats. Se connecter au [portail Azure][azure-portal].
    1. Dans le volet gauche, cliquez sur <strong>Parcourir</strong> .
    2. Cliquez sur <strong>tout</strong> en haut à droite du volet de navigation.
    3. Recherchez et cliquez sur <strong>Comptes DocumentDB</strong>.
    4. Ensuite, recherchez votre <strong>Compte DocumentDB</strong>, puis <strong>DocumentDB de base de données</strong> et de votre <strong>Collection de sites DocumentDB</strong> associée à la collection de sortie spécifiée dans votre tâche MapReduce.
    5. Pour finir, cliquez sur <strong>Explorateur de Document</strong> , sous <strong>Outils de développement</strong>.

    Vous verrez les résultats de votre travail MapReduce.

    ![Résultats de la requête MapReduce][image-mapreduce-query-results]

## <a name="NextSteps"></a>Étapes suivantes

Félicitations ! Vous venez d’exécuter votre première travaux Hive cochon et MapReduce à l’aide de Azure DocumentDB et HDInsight.

Nous avons ouvrir provenant de notre connecteur Hadoop. Si vous êtes intéressé, vous pouvez contribuer aux sur [GitHub][documentdb-github].

Pour plus d’informations, consultez les articles suivants :

- [Développer une application Java avec Documentdb][documentdb-java-application]
- [Développer des programmes Java MapReduce pour Hadoop dans HDInsight][hdinsight-develop-deploy-java-mapreduce]
- [Prise en main avec Hadoop Hive dans HDInsight à analyser l’utilisation d’un combiné mobile][hdinsight-get-started]
- [Utiliser MapReduce avec HDInsight][hdinsight-use-mapreduce]
- [Utiliser Hive avec HDInsight][hdinsight-use-hive]
- [Utiliser cochon avec HDInsight][hdinsight-use-pig]
- [Personnaliser les HDInsight à l’aide de Script Action][hdinsight-hadoop-customize-cluster]

[apache-hadoop]: http://hadoop.apache.org/
[apache-hadoop-doc]: http://hadoop.apache.org/docs/current/
[apache-hive]: http://hive.apache.org/
[apache-pig]: http://pig.apache.org/
[getting-started]: documentdb-get-started.md

[azure-portal]: https://portal.azure.com/
[azure-powershell-diagram]: ./media/documentdb-run-hadoop-with-hdinsight/azurepowershell-diagram-med.png

[documentdb-hdinsight-samples]: http://portalcontent.blob.core.windows.net/samples/documentdb-hdinsight-samples.zip
[documentdb-github]: https://github.com/Azure/azure-documentdb-hadoop
[documentdb-java-application]: documentdb-java-application.md
[documentdb-manage-collections]: documentdb-manage.md#Collections
[documentdb-manage-document-storage]: documentdb-manage.md#IndexOverhead
[documentdb-manage-throughput]: documentdb-manage.md#ProvThroughput
[documentdb-import-data]: documentdb-import-data.md

[hdinsight-custom-provision]: ../hdinsight/hdinsight-provision-clusters.md#powershell
[hdinsight-develop-deploy-java-mapreduce]: ../hdinsight/hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-hadoop-customize-cluster]: ../hdinsight/hdinsight-hadoop-customize-cluster.md
[hdinsight-get-started]: ../hdinsight/hdinsight-hadoop-tutorial-get-started-windows.md
[hdinsight-storage]: ../hdinsight/hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: ../hdinsight/hdinsight-use-hive.md
[hdinsight-use-mapreduce]: ../hdinsight/hdinsight-use-mapreduce.md
[hdinsight-use-pig]: ../hdinsight/hdinsight-use-pig.md

[image-customprovision-page1]: ./media/documentdb-run-hadoop-with-hdinsight/customprovision-page1.png
[image-hive-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/hivequeryresults.PNG
[image-mapreduce-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/mapreducequeryresults.PNG
[image-pig-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/pigqueryresults.PNG

[powershell-install-configure]: ../powershell-install-configure.md
