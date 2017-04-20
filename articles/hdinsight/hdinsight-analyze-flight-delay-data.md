<properties
    pageTitle="Analyser des données de retard aérienne avec Hadoop dans HDInsight | Microsoft Azure"
    description="Découvrez comment utiliser un script Windows PowerShell pour créer un cluster de HDInsight, exécuter une tâche Hive, exécution d’une tâche Sqoop et supprimer le cluster."
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="jgao"/>

#<a name="analyze-flight-delay-data-by-using-hive-in-hdinsight"></a>Analyser des données de vol retard à l’aide Hive dans HDInsight

Hive fournit un moyen de l’exécution de tâches Hadoop MapReduce via un langage de script similaire à SQL appelé * [HiveQL][hadoop-hiveql]*, qui peuvent être appliquées vers résumer, l’interrogation et l’analyse de grands volumes de données.

> [AZURE.NOTE] Les étapes décrites dans ce document nécessitent un cluster HDInsight fonctionnant sous Windows. Pour connaître les étapes qui fonctionnent avec un cluster Linux, voir [analyse des données de retard aérienne à l’aide de Hive dans HDInsight (Linux)](hdinsight-analyze-flight-delay-data-linux.md).

Un des principaux avantages de Azure HDInsight est la séparation du stockage des données et cluster. HDInsight utilise le stockage Blob Azure pour stocker les données. Une tâche typique implique trois parties :

1. **Stocker des données dans le stockage Blob Azure.**  Météo, par exemple, des données, des données de capteur journaux web, et dans ce cas, données aérienne sont enregistrées dans le stockage Blob Azure.
2. **Exécuter des tâches.** Lorsqu’il est temps de traiter les données, vous exécuter un script Windows PowerShell (ou une application cliente) pour créer un cluster de HDInsight, exécution de tâches et supprimer le cluster. Les tâches enregistrement les données de sortie au stockage d’objets Blob Azure. Les données de sortie sont conservées même après que le cluster est supprimé. Ainsi, vous payez ne que vous avez utilisé.
3. **Récupérer la sortie depuis le stockage Blob Azure**, ou dans ce didacticiel, exporter les données dans une base de données SQL Azure.

Le diagramme suivant illustre le scénario et la structure de ce didacticiel :

![HDI. FlightDelays.flow][img-hdi-flightdelays-flow]

**Remarque**: les numéros dans le diagramme correspondent aux titres de section. **M** représente le processus principal. **A** représente le contenu de l’appendice.

La partie principale du didacticiel vous montre comment utiliser un script Windows PowerShell pour effectuer les tâches suivantes :

- Créer un cluster HDInsight.
- Exécuter une tâche Hive sur le cluster pour calculer la moyennes retards dans les aéroports. Les données de retard aérienne sont stockées dans un compte de stockage Blob Azure.
- Exécuter une tâche Sqoop pour exporter la sortie des tâches Hive vers une base de données SQL Azure.
- Supprimer le cluster HDInsight.

Dans les annexes, vous trouverez les instructions pour télécharger des données aérienne, création/transfert d’une chaîne de requête Hive et préparer la base de données SQL Azure pour le travail Sqoop.

> [AZURE.NOTE] Les étapes décrites dans ce document sont spécifiques aux clusters HDInsight fonctionnant sous Windows. Pour connaître les étapes qui fonctionnent avec un cluster Linux, voir [analyse des données de retard aérienne à l’aide de Hive dans HDInsight (Linux)](hdinsight-analyze-flight-delay-data-linux.md)

###<a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Un poste de travail avec Azure PowerShell**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

**Fichiers utilisés dans ce didacticiel**

Ce didacticiel utilise les performances dans les temps de données de vol de compagnie aérienne de [recherche et Administration de la technologie innovante, Office des statistiques de transport ou RITA] [rita-website]. Une copie des données a été téléchargée dans un conteneur de stockage Blob Azure avec l’autorisation d’accès Blob Public. Une partie de votre script PowerShell copie les données à partir du conteneur blob public dans le conteneur blob par défaut de votre cluster. Le script HiveQL est également copié dans le même conteneur Blob. Si vous voulez en savoir comment obtenir/charger les données à votre compte de stockage et comment créer/télécharger le fichier de script HiveQL, voir [l’annexe A](#appendix-a) et [B appendice](#appendix-b).

Le tableau suivant répertorie les fichiers utilisés dans ce didacticiel :

<table border="1">
<tr><th>Fichiers</th><th>Description</th></tr>
<tr><td>wasbs://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql</td><td>Le fichier de script HiveQL utilisé par la tâche Hive. Ce script a été téléchargé dans un compte de stockage Blob Azure avec l’accès du public. <a href="#appendix-b">L’annexe B</a> contient des instructions sur la préparation et le téléchargement de ce fichier à votre compte de stockage Blob Azure.</td></tr>
<tr><td>wasbs://flightdelay@hditutorialdata.blob.core.windows.net/2013Data</td><td>Données d’entrée pour la tâche Hive. Les données a été téléchargées dans un compte de stockage Blob Azure avec l’accès du public. <a href="#appendix-a">Appendice A</a> contient des instructions sur l’obtention de données et de télécharger les données à votre compte de stockage Blob Azure.</td></tr>
<tr><td>\tutorials\flightdelays\output</td><td>Le chemin d’accès de sortie pour le travail Hive. Le conteneur par défaut est utilisé pour stocker les données de sortie.</td></tr>
<tr><td>\tutorials\flightdelays\jobstatus</td><td>Le dossier Hive de l’état des tâches sur le conteneur par défaut.</td></tr>
</table>


##<a name="create-cluster-and-run-hivesqoop-jobs"></a>Créer cluster et exécuter des travaux Hive/Sqoop

Hadoop MapReduce est traitement par lots. Le moyen le plus rentable d’exécuter une tâche Hive consiste à créer un cluster de la tâche et supprimer le travail une fois la tâche est terminée. Le script suivant traite de l’ensemble du processus. Pour plus d’informations sur la création d’un cluster HDInsight et en exécutant des travaux Hive, voir [Hadoop créer des groupes dans un HDInsight] [ hdinsight-provision] et [Utilisation ruche avec HDInsight][hdinsight-use-hive].

**Pour exécuter les requêtes Hive par PowerShell Azure**

1. Créer une base de données SQL Azure et de la table pour la sortie des tâches Sqoop en suivant les instructions dans [L’annexe C](#appendix-c).
3. Ouvrez Windows PowerShell ISE, puis exécutez le script suivant :

        $subscriptionID = "<Azure Subscription ID>"
        $nameToken = "<Enter an Alias>" 
        
        ###########################################
        # You must configure the follwing variables
        # for an existing Azure SQL Database
        ###########################################
        $existingSqlDatabaseServerName = "<Azure SQL Database Server>"
        $existingSqlDatabaseLogin = "<Azure SQL Database Server Login>"
        $existingSqlDatabasePassword = "<Azure SQL Database Server login password>"
        $existingSqlDatabaseName = "<Azure SQL Database name>"
        
        $localFolder = "E:\Tutorials\Downloads\" # A temp location for copying files. 
        $azcopyPath = "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy" # depends on the version, the folder can be different
        
        ###########################################
        # (Optional) configure the following variables
        ###########################################
        
        $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

        $resourceGroupName = $namePrefix + "rg"
        $location = "EAST US 2"
        
        $HDInsightClusterName = $namePrefix + "hdi"
        $httpUserName = "admin"
        $httpPassword = "<Enter the Password>"
        
        $defaultStorageAccountName = $namePrefix + "store"
        $defaultBlobContainerName = $HDInsightClusterName # use the cluster name
        
        $existingSqlDatabaseTableName = "AvgDelays"
        $sqlDatabaseConnectionString = "jdbc:sqlserver://$existingSqlDatabaseServerName.database.windows.net;user=$existingSqlDatabaseLogin@$existingSqlDatabaseServerName;password=$existingSqlDatabaseLogin;database=$existingSqlDatabaseName"
        
        $hqlScriptFile = "/tutorials/flightdelays/flightdelays.hql" 
        
        $jobStatusFolder = "/tutorials/flightdelays/jobstatus"
        
        ###########################################
        # Login
        ###########################################
        try{
            $acct = Get-AzureRmSubscription
        }
        catch{
            Login-AzureRmAccount
        }
        Select-AzureRmSubscription -SubscriptionID $subscriptionID
        
        ###########################################
        # Create a new HDInsight cluster
        ###########################################
        
        # Create ARM group
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
        
        # Create the default storage account
        New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName -Location $location -Type Standard_LRS
        
        # Create the default Blob container
        $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
        $defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey 
        New-AzureStorageContainer -Name $defaultBlobContainerName -Context $defaultStorageAccountContext 
        
        # Create the HDInsight cluster
        $pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
        $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)
        
        New-AzureRmHDInsightCluster `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $HDInsightClusterName `
            -Location $location `
            -ClusterType Hadoop `
            -OSType Windows `
            -ClusterSizeInNodes 2 `
            -HttpCredential $httpCredential `
            -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
            -DefaultStorageAccountKey $defaultStorageAccountKey `
            -DefaultStorageContainer $existingDefaultBlobContainerName 
        
        
        ###########################################
        # Prepare the HiveQL script and source data
        ###########################################
        
        # Create the temp location  
        New-Item -Path $localFolder -ItemType Directory -Force 
        
        # Download the sample file from Azure Blob storage
        $context = New-AzureStorageContext -StorageAccountName "hditutorialdata" -Anonymous
        $blobs = Get-AzureStorageBlob -Container "flightdelay" -Context $context
        #$blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder
        
        # Upload data to default container
        
        $azcopycmd = "cmd.exe /C '$azcopyPath\azcopy.exe' /S /Source:'$localFolder' /Dest:'https://$defaultStorageAccountName.blob.core.windows.net/$defaultBlobContainerName/tutorials/flightdelays' /DestKey:$defaultStorageAccountKey"
        
        Invoke-Expression -Command:$azcopycmd
        
        ###########################################
        # Submit the Hive job
        ###########################################
        Use-AzureRmHDInsightCluster -ClusterName $HDInsightClusterName -HttpCredential $httpCredential
        $response = Invoke-AzureRmHDInsightHiveJob `
                        -Files $hqlScriptFile `
                        -DefaultContainer $defaultBlobContainerName `
                        -DefaultStorageAccountName $defaultStorageAccountName `
                        -DefaultStorageAccountKey $defaultStorageAccountKey `
                        -StatusFolder $jobStatusFolder 
        
        write-Host $response
        
        
        ###########################################
        # Submit the Sqoop job
        ###########################################
        $exportDir = "wasbs://$defaultBlobContainerName@$defaultStorageAccountName.blob.core.windows.net/tutorials/flightdelays/output"
        
        $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
                        -Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "
        $sqoopJob = Start-AzureRmHDInsightJob `
                        -ResourceGroupName $resourceGroupName `
                        -ClusterName $hdinsightClusterName `
                        -HttpCredential $httpCredential `
                        -JobDefinition $sqoopDef #-Debug -Verbose
        
        Wait-AzureRmHDInsightJob `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $HDInsightClusterName `
            -HttpCredential $httpCredential `
            -WaitTimeoutInSeconds 3600 `
            -Job $sqoopJob.JobId
        
        
        Get-AzureRmHDInsightJobOutput `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $hdinsightClusterName `
            -HttpCredential $httpCredential `
            -DefaultContainer $existingDefaultBlobContainerName `
            -DefaultStorageAccountName $defaultStorageAccountName `
            -DefaultStorageAccountKey $defaultStorageAccountKey `
            -JobId $sqoopJob.JobId `
            -DisplayOutputType StandardError
        
        ###########################################
        # Delete the cluster
        ###########################################
        Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName

6. Connectez-vous à votre base de données SQL et voir les retards des avions moyenne par ville dans la table AvgDelays :

    ![HDI. FlightDelays.AvgDelays.Dataset][image-hdi-flightdelays-avgdelays-dataset]



---
##<a id="appendix-a"></a>Appendice A - données aérienne téléchargement à Azure Blob storage
Téléchargement du fichier de données et les fichiers de script HiveQL (voir [Appendice B](#appendix-b)) requiert une planification. L’idée consiste à stocker les fichiers de données et le fichier HiveQL avant de créer un cluster HDInsight et exécution de la tâche Hive. Vous avez deux possibilités :

- **Utiliser le même compte de stockage Azure qui servira par le cluster HDInsight en tant que le système de fichiers par défaut.** Étant donné que le cluster HDInsight aura touche d’accès rapide stockage compte, vous n’avez pas besoin d’apporter des modifications supplémentaires.
- **Utiliser un autre compte de stockage Azure HDInsight cluster par défaut système de fichiers.** Si c’est le cas, vous devez modifier la partie de la création de script Windows PowerShell figurant dans [cluster HDInsight créer et exécuter travaux Hive/Sqoop](#runjob) pour lier le compte de stockage comme un compte de stockage supplémentaire. Pour plus d’informations, voir [Hadoop créer des groupes dans un HDInsight][hdinsight-provision]. Le cluster HDInsight sait puis touche d’accès rapide pour le compte de stockage.

>[AZURE.NOTE] Le chemin d’accès du stockage Blob du fichier de données est codé en dur dans le fichier de script HiveQL. Vous devez mettre à jour en conséquence.

**Pour télécharger les données de vol**

1. Accédez à la [recherche et une technologie innovante Administration, Bureau du transport statistiques][rita-website].
2. Dans la page, sélectionnez les valeurs suivantes :

    <table border="1">
    <tr><th>Nom</th><th>Valeur</th></tr>
    <tr><td>Filtre année</td><td>2013 </td></tr>
    <tr><td>Filtre période</td><td>Janvier</td></tr>
    <tr><td>Champs</td><td>*Année*, *FlightDate*, *UniqueCarrier*, *opérateur*, *FlightNum*, *OriginAirportID*, *origine*, *OriginCityName*, *OriginState*, *DestAirportID*, *Dest*, *DestCityName*, *DestState*, *DepDelayMinutes*, *ArrDelay*, *ArrDelayMinutes*, *CarrierDelay*, *WeatherDelay*, *NASDelay*, *SecurityDelay*, *LateAircraftDelay* (Effacer tous les autres champs)</td></tr>
    </table>

3. Cliquez sur **Télécharger**.
4. Décompressez le fichier dans le dossier **C:\Tutorials\FlightDelay\2013Data** . Chaque fichier est un fichier CSV et est environ 60 Go.
5.  Renommez le nom du mois qu’il contient des données pour le fichier. Par exemple, le fichier contenant les données de janvier doit être nommer *January.csv*.
6. Répétez les étapes 2 et 5 pour télécharger un fichier pour chacun des 12 mois dans 2013. Vous devez au moins un fichier pour exécuter le didacticiel.  

**Pour charger les données de retard aérienne à Azure Blob storage**

1. Préparer les paramètres :

    <table border="1">
    <tr><th>Nom d’une variable</th><th>Notes</th></tr>
    <tr><td>$storageAccountName</td><td>Le compte de stockage Azure dans lequel vous voulez télécharger les données à.</td></tr>
    <tr><td>$blobContainerName</td><td>Le conteneur Blob dans lequel vous voulez télécharger les données à.</td></tr>
    </table>
2. Ouvrez Azure PowerShell ISE.
3. Collez le script suivant dans la fenêtre de script :

        [CmdletBinding()]
        Param(

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
            [String]$storageAccountName,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
            [String]$blobContainerName
        )

        #Region - Variables
        $localFolder = "C:\Tutorials\FlightDelay\2013Data"  # The source folder
        $destFolder = "tutorials/flightdelay/2013data"     #The blob name prefix for the files to be uploaded
        #EndRegion
        
        #Region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #EndRegion
        
        #Region - Validate user input
        Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
        # Validate the Storage account
        if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
        {
            Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
            exit
        }
        else{
            $resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
        }
        
        # Validate the container
        $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        
        if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
        {
            Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
            Exit
        }
        #EngRegion
        
        #Region - Copy the file from local workstation to Azure Blob storage  
        if (test-path -Path $localFolder)
        {
            foreach ($item in Get-ChildItem -Path $localFolder){
                $fileName = "$localFolder\$item"
                $blobName = "$destFolder/$item"
        
                Write-Host "Copying $fileName to $blobName" -ForegroundColor Green
        
                Set-AzureStorageBlobContent -File $fileName -Container $blobContainerName -Blob $blobName -Context $storageContext
            }
        }
        else
        {
            Write-Host "The source folder on the workstation doesn't exist" -ForegroundColor Red
        }
        
        # List the uploaded files on HDInsight
        Get-AzureStorageBlob -Container $blobContainerName  -Context $storageContext -Prefix $destFolder
        #EndRegion




4. Appuyez sur **F5** pour exécuter le script.

Si vous choisissez d’utiliser une autre méthode pour le téléchargement de fichiers, vérifiez que le chemin d’accès du fichier est didacticiels/flightdelay/données. La syntaxe pour accéder aux fichiers est la suivante :

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelay/data

Les didacticiels/flightdelay/données chemin d’accès est le dossier virtuel que vous avez créé lorsque vous téléchargez les fichiers. Vérifiez qu’il n’y a 12 fichiers, une pour chaque mois.

>[AZURE.NOTE] Vous devez mettre à jour la requête Hive de lire le nouvel emplacement.

> Vous devez configurer l’autorisation d’accès de conteneur pour être public ou lier le compte de stockage pour le cluster HDInsight. Dans le cas contraire, la chaîne de requête Hive ne seront pas en mesure d’accéder aux fichiers de données.

---
##<a id="appendix-b"></a>Consultez l’annexeB - créer et télécharger un script HiveQL

À l’aide de PowerShell Azure, vous pouvez exécuter plusieurs instructions HiveQL une à la fois, ou l’instruction HiveQL dans un fichier de script du package. Cette section vous montre comment créer un script HiveQL et téléchargez le script à Azure Blob storage à l’aide de PowerShell Azure. Hive requiert les scripts HiveQL stocké dans le stockage Blob Azure.

Le script HiveQL effectue les éléments suivants :

1. **Supprimer la table delays_raw**, dans le cas, la table déjà existe.
2. **Créer une table Hive externes delays_raw** en pointant sur l’emplacement de stockage Blob avec les fichiers de retard aérienne. Cette requête spécifie que les champs sont délimités par «, » et que les lignes sont terminent par « \n ». Ceci pose un problème lorsque les valeurs de champ contiennent des virgules, car Hive ne peut pas faire la différence entre une virgule est un délimiteur de champ et une qui fait partie d’une valeur de champ (qui est le cas de valeurs de champs d’origine\_Ville\_nom et DEST\_Ville\_nom). Pour contourner ce problème, la requête crée des colonnes TEMP pour mettre en attente de données sont incorrectement divisées en colonnes.  
3. **Supprimer la table de retards**, dans le cas, la table déjà existe.
4. **Créer la table des retards**. Il est utile de nettoyer les données avant un traitement approfondi. Cette requête crée une nouvelle table, de *retards*, de la table delays_raw. Notez que les colonnes TEMP (comme mentionné précédemment) ne sont pas copiés, et que la fonction **sous-chaîne** est utilisée pour supprimer des guillemets à partir des données.
5. **Calculer le retard moyen météo et regroupe les résultats par nom de la ville.** Il est également transmettre les résultats au stockage d’objets Blob. Notez que la requête supprime apostrophes à partir des données et exclure les lignes dont la valeur de **weather_delay** est null. Cela est nécessaire parce que Sqoop, utilisée plus loin dans ce didacticiel, ne traite normalement ces valeurs par défaut.

Pour une liste complète des commandes HiveQL, voir [Ruche Data Definition Language][hadoop-hiveql]. Chaque commande HiveQL doit se terminer par un point-virgule.

**Pour créer un fichier de script HiveQL**

1. Préparer les paramètres :

    <table border="1">
    <tr><th>Nom d’une variable</th><th>Notes</th></tr>
    <tr><td>$storageAccountName</td><td>Le compte de stockage Azure dans lequel vous voulez télécharger le script HiveQL.</td></tr>
    <tr><td>$blobContainerName</td><td>Le conteneur Blob dans lequel vous voulez télécharger le script HiveQL.</td></tr>
    </table>
2. Ouvrez Azure PowerShell ISE.

3. Copiez et collez le script suivant dans la fenêtre de script :

        [CmdletBinding()]
        Param(

            # Azure Blob storage variables
            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
            [String]$storageAccountName,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
            [String]$blobContainerName
        )

        #region - Define variables
        # Treat all errors as terminating
        $ErrorActionPreference = "Stop"
        
        # The HiveQL script file is exported as this file before it's uploaded to Blob storage
        $hqlLocalFileName = "e:\tutorials\flightdelay\flightdelays.hql"
        
        # The HiveQL script file will be uploaded to Blob storage as this blob name
        $hqlBlobName = "tutorials/flightdelay/flightdelays.hql"
        
        # These two constants are used by the HiveQL script file
        #$srcDataFolder = "tutorials/flightdelay/data"
        $dstDataFolder = "/tutorials/flightdelay/output"
        #endregion

        #Region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #EndRegion
        
        #Region - Validate user input
        Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
        # Validate the Storage account
        if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
        {
            Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
            exit
        }
        else{
            $resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
        }
        
        # Validate the container
        $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        
        if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
        {
            Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
            Exit
        }
        #EngRegion
        
        #region - Validate the file and file path
        
        # Check if a file with the same file name already exists on the workstation
        Write-Host "`nvalidating the folder structure on the workstation for saving the HQL script file ..."  -ForegroundColor Green
        if (test-path $hqlLocalFileName){
        
            $isDelete = Read-Host 'The file, ' $hqlLocalFileName ', exists.  Do you want to overwirte it? (Y/N)'
        
            if ($isDelete.ToLower() -ne "y")
            {
                Exit
            }
        }
        
        # Create the folder if it doesn't exist
        $folder = split-path $hqlLocalFileName
        if (-not (test-path $folder))
        {
            Write-Host "`nCreating folder, $folder ..." -ForegroundColor Green
        
            new-item $folder -ItemType directory  
        }
        #end region
        
        #region - Write the Hive script into a local file
        Write-Host "`nWriting the Hive script into a file on your workstation ..." `
                    -ForegroundColor Green
        
        $hqlDropDelaysRaw = "DROP TABLE delays_raw;"
        
        $hqlCreateDelaysRaw = "CREATE EXTERNAL TABLE delays_raw (" +
                "YEAR string, " +
                "FL_DATE string, " +
                "UNIQUE_CARRIER string, " +
                "CARRIER string, " +
                "FL_NUM string, " +
                "ORIGIN_AIRPORT_ID string, " +
                "ORIGIN string, " +
                "ORIGIN_CITY_NAME string, " +
                "ORIGIN_CITY_NAME_TEMP string, " +
                "ORIGIN_STATE_ABR string, " +
                "DEST_AIRPORT_ID string, " +
                "DEST string, " +
                "DEST_CITY_NAME string, " +
                "DEST_CITY_NAME_TEMP string, " +
                "DEST_STATE_ABR string, " +
                "DEP_DELAY_NEW float, " +
                "ARR_DELAY_NEW float, " +
                "CARRIER_DELAY float, " +
                "WEATHER_DELAY float, " +
                "NAS_DELAY float, " +
                "SECURITY_DELAY float, " +
                "LATE_AIRCRAFT_DELAY float) " +
            "ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' " +
            "LINES TERMINATED BY '\n' " +
            "STORED AS TEXTFILE " +
            "LOCATION 'wasbs://flightdelay@hditutorialdata.blob.core.windows.net/2013Data';"
        
        $hqlDropDelays = "DROP TABLE delays;"
        
        $hqlCreateDelays = "CREATE TABLE delays AS " +
            "SELECT YEAR AS year, " +
                "FL_DATE AS flight_date, " +
                "substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier, " +
                "substring(CARRIER, 2, length(CARRIER) -1) AS carrier, " +
                "substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num, " +
                "ORIGIN_AIRPORT_ID AS origin_airport_id, " +
                "substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code, " +
                "substring(ORIGIN_CITY_NAME, 2) AS origin_city_name, " +
                "substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr, " +
                "DEST_AIRPORT_ID AS dest_airport_id, " +
                "substring(DEST, 2, length(DEST) -1) AS dest_airport_code, " +
                "substring(DEST_CITY_NAME,2) AS dest_city_name, " +
                "substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr, " +
                "DEP_DELAY_NEW AS dep_delay_new, " +
                "ARR_DELAY_NEW AS arr_delay_new, " +
                "CARRIER_DELAY AS carrier_delay, " +
                "WEATHER_DELAY AS weather_delay, " +
                "NAS_DELAY AS nas_delay, " +
                "SECURITY_DELAY AS security_delay, " +
                "LATE_AIRCRAFT_DELAY AS late_aircraft_delay " +
            "FROM delays_raw;"
        
        $hqlInsertLocal = "INSERT OVERWRITE DIRECTORY '$dstDataFolder' " +
            "SELECT regexp_replace(origin_city_name, '''', ''), " +
                "avg(weather_delay) " +
            "FROM delays " +
            "WHERE weather_delay IS NOT NULL " +
            "GROUP BY origin_city_name;"
        
        $hqlScript = $hqlDropDelaysRaw + $hqlCreateDelaysRaw + $hqlDropDelays + $hqlCreateDelays + $hqlInsertLocal
        
        $hqlScript | Out-File $hqlLocalFileName -Encoding ascii -Force
        #endregion
        
        #region - Upload the Hive script to the default Blob container
        Write-Host "`nUploading the Hive script to the default Blob container ..." -ForegroundColor Green
        
        # Create a storage context object
        $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        
        # Upload the file from local workstation to Blob storage
        Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $blobContainerName -Blob $hqlBlobName -Context $destContext
        #endregion
        
        Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

    Voici les variables utilisées dans le script :

    - **$hqlLocalFileName** - le script enregistre localement le fichier de script HiveQL avant de le télécharger vers le stockage Blob. Il s’agit du nom du fichier. La valeur par défaut est <u>C:\tutorials\flightdelay\flightdelays.hql</u>.
    - **$hqlBlobName** - il s’agit du nom de blob de fichier de script HiveQL utilisé dans le stockage Blob Azure. La valeur par défaut est tutorials/flightdelay/flightdelays.hql. Étant donné que le fichier sera écrit directement au stockage d’objets Blob Azure, il n’est pas un « / » au début du nom du blob. Si vous voulez accéder au fichier à partir du stockage Blob, vous devrez ajouter un « / » au début du nom de fichier.
    - **$srcDataFolder** et **$dstDataFolder** - = « didacticiels/flightdelay/données » = « didacticiels/flightdelay/sortie »


---
##<a id="appendix-c"></a>Annexe C - préparer une base de données SQL Azure pour la sortie des tâches Sqoop
**Pour préparer la base de données SQL (fusionne avec le script Sqoop)**

1. Préparer les paramètres :

    <table border="1">
    <tr><th>Nom d’une variable</th><th>Notes</th></tr>
    <tr><td>$sqlDatabaseServerName</td><td>Le nom du serveur de base de données SQL Azure. Entrez rien pour créer un nouveau serveur.</td></tr>
    <tr><td>$sqlDatabaseUsername</td><td>Nom de connexion pour le serveur de base de données SQL Azure. Si $sqlDatabaseServerName est un serveur existant, la connexion et le mot de passe sont utilisés pour vous authentifier avec le serveur. Dans le cas contraire, ils sont utilisés pour créer un nouveau serveur.</td></tr>
    <tr><td>$sqlDatabasePassword</td><td>Le mot de passe pour le serveur de base de données SQL Azure.</td></tr>
    <tr><td>$sqlDatabaseLocation</td><td>Cette valeur est utilisée uniquement lorsque vous créez un nouveau serveur de base de données Azure.</td></tr>
    <tr><td>$sqlDatabaseName</td><td>La base de données SQL utilisée pour créer la table AvgDelays pour le travail Sqoop. Si vous laissez vide crée une base de données appelée HDISqoop. Le nom de table pour la sortie des tâches Sqoop est AvgDelays. </td></tr>
    </table>
2. Ouvrez Azure PowerShell ISE.
3. Copiez et collez le script suivant dans la fenêtre de script :

        [CmdletBinding()]
        Param(
        
            # Azure Resource group variables
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure resource group name. It will be created if it doesn't exist.")]
            [String]$resourceGroupName,
        
            # SQL database server variables
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure SQL Database Server Name. It will be created if it doesn't exist.")]
            [String]$sqlDatabaseServer, 
        
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure SQL Database admin user.")]
            [String]$sqlDatabaseLogin,
        
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure SQL Database admin user password.")]
            [String]$sqlDatabasePassword,
        
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the region to create the Database in.")]
            [String]$sqlDatabaseLocation,   #For example, West US.
        
            # SQL database variables
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the database name. It will be created if it doesn't exist.")]
            [String]$sqlDatabaseName # specify the database name if you have one created. Otherwise use "" to have the script create one for you.
        )
        
        # Treat all errors as terminating
        $ErrorActionPreference = "Stop"
        
        #region - Constants and variables
        
        # IP address REST service used for retrieving external IP address and creating firewall rules
        [String]$ipAddressRestService = "http://bot.whatismyipaddress.com"
        [String]$fireWallRuleName = "FlightDelay"
        
        # SQL database variables
        [String]$sqlDatabaseMaxSizeGB = 10
        
        #SQL query string for creating AvgDelays table
        [String]$sqlDatabaseTableName = "AvgDelays"
        [String]$sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                    [origin_city_name] [nvarchar](50) NOT NULL,
                    [weather_delay] float,
                CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
                (
                    [origin_city_name] ASC
                )
                )"
        #endregion
        
        #Region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #EndRegion
        
        #region - Create and validate Azure resouce group
        try{
            Get-AzureRmResourceGroup -Name $resourceGroupName
        }
        catch{
            New-AzureRmResourceGroup -Name $resourceGroupName -Location $sqlDatabaseLocation
        }
        
        #EndRegion
        
        #region - Create and validate Azure SQL database server
        try{
            Get-AzureRmSqlServer -ServerName $sqlDatabaseServer -ResourceGroupName $resourceGroupName}
        catch{
            Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
        
            $sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
            $credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)
        
            $sqlDatabaseServer = (New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -SqlAdministratorCredentials $credential -Location $sqlDatabaseLocation).ServerName
            Write-Host "`tThe new SQL database server name is $sqlDatabaseServer." -ForegroundColor Cyan
        
            Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
            $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
            New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-workstation" -StartIpAddress $workstationIPAddress -EndIpAddress $workstationIPAddress
        
            #To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. Note that this allows Azure traffic from any Azure subscription to access the server.
            New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-Azureservices" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
        }
        
        #endregion
        
        #region - Create and validate Azure SQL database
        
        try {
            Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName
        }
        catch {
            Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
            New-AzureRMSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName -Edition "Standard" -RequestedServiceObjectiveName "S1"
        }
        
        #endregion
        
        #region -  Execute an SQL command to create the AvgDelays table
        
        Write-Host "`nCreating SQL Database table ..."  -ForegroundColor Green
        $conn = New-Object System.Data.SqlClient.SqlConnection
        $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
        $conn.open()
        $cmd = New-Object System.Data.SqlClient.SqlCommand
        $cmd.connection = $conn
        $cmd.commandtext = $sqlCreateAvgDelaysTable
        $cmd.executenonquery()
        
        $conn.close()
        
        Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

    >[AZURE.NOTE] Le script utilise un service de transfert (REST) état représentation, http://bot.whatismyipaddress.com, pour récupérer votre adresse IP externe. L’adresse IP est utilisée pour créer une règle de pare-feu pour votre serveur de base de données SQL.  

    Voici quelques variables utilisées dans le script :

    - **$ipAddressRestService** - la valeur par défaut est http://bot.whatismyipaddress.com. Il est une adresse IP publique service REST pour obtenir votre adresse IP externe. Vous pouvez utiliser d’autres services si vous voulez. L’adresse IP externe récupérée via le service servira à créer une règle de pare-feu pour votre serveur de base de données SQL Azure, afin que vous pouvez accéder à la base de données à partir de votre poste de travail (en utilisant un script Windows PowerShell).
    - **$fireWallRuleName** - il s’agit du nom de la règle de pare-feu pour le serveur de base de données SQL Azure. Le nom par défaut est <u>FlightDelay</u>. Vous pouvez la renommer si vous voulez.
    - **$sqlDatabaseMaxSizeGB** - cette valeur est utilisé uniquement lorsque vous créez un nouveau serveur de base de données SQL Azure. La valeur par défaut est 10 Go. 10 Go est suffisant pour ce didacticiel.
    - **$sqlDatabaseName** - cette valeur est utilisé uniquement lorsque vous créez une nouvelle base de données SQL Azure. La valeur par défaut est HDISqoop. Si vous renommez, vous devez mettre à jour le script Sqoop Windows PowerShell en conséquence.

4. Appuyez sur **F5** pour exécuter le script.
5. Valider la sortie du script. Vérifiez que le script a été correctement exécuté.

##<a id="nextsteps"></a>Étapes suivantes
Maintenant que vous comprenez comment télécharger un fichier sur le stockage d’objets Blob Azure, comment faire pour remplir une table Hive en utilisant les données depuis le stockage Blob Azure, comment exécuter des requêtes Hive et l’utilisation de Sqoop pour exporter des données à partir de HDFS à une base de données SQL Azure. Pour plus d’informations, consultez les articles suivants :

* [Prise en main HDInsight][hdinsight-get-started]
* [Utiliser Hive avec HDInsight][hdinsight-use-hive]
* [Utiliser Oozie avec HDInsight][hdinsight-use-oozie]
* [Utiliser Sqoop avec HDInsight][hdinsight-use-sqoop]
* [Utiliser cochon avec HDInsight][hdinsight-use-pig]
* [Développer des programmes Java MapReduce pour HDInsight][hdinsight-develop-mapreduce]



[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[powershell-install-configure]: powershell-install-configure.md

[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
[hadoop-shell-commands]: http://hadoop.apache.org/docs/r0.18.3/hdfs_shell.html

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

[image-hdi-flightdelays-avgdelays-dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
[img-hdi-flightdelays-run-hive-job-output]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.RunHiveJob.Output.png
[img-hdi-flightdelays-flow]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.Flow.png
