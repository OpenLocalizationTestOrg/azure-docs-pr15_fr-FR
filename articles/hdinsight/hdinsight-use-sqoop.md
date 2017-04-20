<properties
    pageTitle="Utiliser Hadoop Sqoop dans HDInsight | Microsoft Azure"
    description="Découvrez comment utiliser PowerShell Azure à partir d’un poste de travail à exécuter Sqoop d’importation et exportation entre un cluster Hadoop et une base de données SQL Azure."
    editor="cgronlun"
    manager="jhubbard"
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/02/2016"
    ms.author="jgao"/>

#<a name="use-sqoop-with-hadoop-in-hdinsight"></a>Utiliser Sqoop avec Hadoop dans HDInsight

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Découvrez comment utiliser Sqoop HDInsight pour importer et exporter entre HDInsight cluster et base de données SQL Azure ou base de données SQL Server.

Bien que Hadoop est un choix naturel pour le traitement des données structurées et semi-structurées, tels que les journaux et les fichiers, il peut également être nécessaire de traiter des données structurées qui sont stockées dans des bases de données relationnelles.

[Sqoop] [ sqoop-user-guide-1.4.4] est un outil conçu pour transférer des données entre clusters Hadoop et bases de données relationnelles. Vous pouvez l’utiliser pour importer des données à partir d’un système de gestion de base de données relationnelle (SGBDR) telles que SQL Server, MySQL ou Oracle dans le système de fichiers Hadoop distributed (HDFS), transformer les données dans Hadoop avec MapReduce ou Hive et puis exporter les données dans un SGBDR. Dans ce didacticiel, vous utilisez une base de données SQL Server pour votre base de données relationnelle.

Pour les versions Sqoop prises en charge sur les clusters HDInsight, voir [Quelles sont les nouveautés dans les versions cluster fournies par HDInsight ?] [hdinsight-versions].

##<a name="understand-the-scenario"></a>Comprendre le scénario

HDInsight cluster est fourni avec des exemples de données. Vous allez utiliser les deux exemples suivants :

- Un fichier journal log4j, qui se trouve à */example/data/sample.log*. Les fichiers journaux suivants sont extraites du fichier :

        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...

- Une table Hive nommée *hivesampletable*qui fait référence au fichier de données situé à */hive/warehouse/hivesampletable*. La table contient des données de l’appareil mobile. 

  	| Champ | Type de données |
  	| ----- | --------- |
  	| identifiant du client | chaîne |
  	| querytime | chaîne |
  	| marché | chaîne |
  	| deviceplatform | chaîne |
  	| devicemake | chaîne |
  	| devicemodel | chaîne |
  	| état | chaîne |
  	| pays | chaîne |
  	| querydwelltime | Double |
  	| ID de session | bigint |
  	| sessionpagevieworder | bigint |

Vous sera tout d’abord exporter *exemple.log* et *hivesampletable* dans la base de données SQL Azure ou SQL Server, puis les importer la table qui contient les données de l’appareil mobile à HDInsight à l’aide de l’emplacement suivant :

    /tutorials/usesqoop/importeddata

## <a name="create-cluster-and-sql-database"></a>Créer le cluster et base de données SQL

Cette section vous montre comment créer un cluster et les schémas de base de données SQL pour exécuter le didacticiel à l’aide du portail Azure et un modèle Azure le Gestionnaire de ressources.  Si vous préférez utiliser Azure PowerShell, consultez [l’annexe A](#appendix-a---a-powershell-sample).

1. Cliquez sur l’image suivante pour ouvrir un modèle de gestionnaire de ressources dans le portail Azure.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fusesqoop%2Fcreate-linux-based-hadoop-cluster-in-hdinsight-and-sql-database.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Le modèle de gestionnaire de ressources se trouve dans un conteneur blob publique, *https://hditutorialdata.blob.core.windows.net/usesqoop/create-linux-based-hadoop-cluster-in-hdinsight-and-sql-database.json*. 
    
    Le modèle de gestionnaire de ressources appelle un package à DOS pour déployer les schémas de tables de base de données SQL.  Le package à DOS se trouve également dans un conteneur blob publique, https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac. Si vous souhaitez utiliser un conteneur privé pour les fichiers à DOS, utilisez les valeurs suivantes dans le modèle :
    
        "storageKeyType": "Primary",
        "storageKey": "<TheAzureStorageAccountKey>",
    
2. À partir de la carte de paramètres, entrez les informations suivantes :

    - **Nomducluster**: entrez un nom pour le cluster Hadoop que vous allez créer.
    - **Mot de passe et le nom de connexion cluster**: le nom de connexion par défaut est administrateur.
    - **SSH nom d’utilisateur et mot de passe**.
    - **Mot de passe et le nom de connexion de serveur de base de données SQL**.

    Les valeurs suivantes sont codé en dur dans la section variables :
    
  	|Nom de compte de stockage par défaut|<CluterName>stocker|
  	|----------------------------|-----------------|
  	|Nom du serveur de base de données SQL Azure|<ClusterName>DBServer|
  	|Nom de base de données SQL Azure|<ClusterName>base de données|
    
    Veuillez noter ces valeurs.  Vous en aurez besoin plus loin dans le didacticiel.
    
3. Cliquez sur **OK** pour enregistrer les paramètres.

4.la à partir de la carte de **déploiement personnalisé** , cliquez sur zone de liste déroulante **groupe de ressources** , puis cliquez sur **Nouveau** pour créer un nouveau groupe de ressources. Le groupe de ressources est un conteneur qui regroupe cluster, le compte de stockage dépendantes et autres ressources liées.

5 Cliquez sur **conditions juridiques**, puis cliquez sur **créer**.

6. Cliquez sur **créer**. Vous verrez une vignette intitulée déploiement d’envoi pour le déploiement du modèle. Il est nécessaire à environ 20 minutes pour créer le cluster et la base de données SQL.

Si vous choisissez d’utiliser la base de données SQL Azure existante ou Microsoft SQL Server

- **Base de données SQL Azure**: vous devez configurer une règle de pare-feu pour le serveur de base de données SQL Azure autoriser l’accès à partir de votre poste de travail. Pour obtenir des instructions sur la création d’un SQL Azure de base de données et la configuration du pare-feu, voir [prise en main à l’aide de la base de données SQL Azure][sqldatabase-get-started]. 

    > [AZURE.NOTE] Par défaut une base de données SQL Azure autorise les connexions à partir d’Azure services, tels que Azure HDInsight. Si ce paramètre de pare-feu est désactivé, vous devez l’activer à partir du portail Azure. Pour obtenir des instructions sur la création d’une base de données SQL Azure et en configurant des règles de pare-feu, voir [créer et configurer la base de données SQL][sqldatabase-create-configue].

- **SQL Server**: Si votre cluster HDInsight se trouve sur le même réseau virtuel dans Azure que SQL Server, vous pouvez utiliser les étapes décrites dans cet article pour importer et exporter des données à une base de données SQL Server.

    > [AZURE.NOTE] HDInsight prend en charge uniquement les réseaux virtuels basés sur un emplacement, et elle ne fonctionne pas avec réseaux virtuels affinité du groupe.

    * Pour créer et configurer un réseau virtuel, voir [créer un réseau virtuel à l’aide du portail Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

        * Lorsque vous utilisez SQL Server dans votre centre de données, vous devez configurer le réseau virtuel comme *site à* ou *point-à-site*.

            > [AZURE.NOTE] Pour les réseaux virtuels **point-à-site** , SQL Server doit exécuter le client VPN application de configuration, qui est disponible à partir du **tableau de bord** de votre configuration réseau virtuel Azure.

        * Lorsque vous utilisez SQL Server sur un ordinateur virtuel Azure, toute configuration réseau virtuel peut être utilisée si la machine virtuelle qui héberge SQL Server est membre du même réseau virtuel en tant que HDInsight.

    * Pour créer un cluster HDInsight sur un réseau virtuel, voir [Hadoop créer des groupes dans un HDInsight à l’aide des options personnalisées](hdinsight-provision-clusters.md)

    > [AZURE.NOTE] SQL Server doit également autoriser l’authentification. Vous devez utiliser une connexion SQL Server pour effectuer les étapes décrites dans cet article.


## <a name="run-sqoop-jobs"></a>Exécution de tâches Sqoop

HDInsight pouvez exécuter Sqoop travaux à l’aide de plusieurs méthodes. Utilisez le tableau suivant pour déterminer quelle méthode vous consiste, puis cliquez sur le lien pour une procédure pas à pas.

| **Utilisez cette option** si vous souhaitez...                                   | .. shell **interactif** .an | ... traitement **par lots** | .. .avec ce **système d’exploitation cluster** | .. contre ce **système d’exploitation client** |
|:--------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-use-sqoop-mac-linux.md)                        |              ✔              |            ✔            | Linux                                     | Linux, Unix, Mac OS X ou Windows        |
| [Kit de développement .NET pour Hadoop](hdinsight-hadoop-use-sqoop-dotnet-sdk.md) |           &nbsp;            |            ✔            | Linux ou Windows                          | Windows (pour l’instant)                        |
| [PowerShell Azure](hdinsight-hadoop-use-sqoop-powershell.md)  |           &nbsp;            |            ✔            | Linux ou Windows                          | Windows                                  |

##<a name="limitations"></a>Limitations

* Exportation en bloc - basé sur Linux avec HDInsight, le connecteur Sqoop permet d’exporter des données vers Microsoft SQL Server ou de base de données SQL Azure ne prend pas en charge les insertions en bloc.

* Le traitement par lots - avec basé sur Linux HDInsight, lorsque vous utilisez la `-batch` commutateur lors de l’exécution d’insertions, Sqoop effectue plusieurs insertions au lieu du traitement par lots les opérations d’insertion.

##<a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à utiliser Sqoop. Pour plus d’informations, voir :

- [Utiliser Hive avec HDInsight](hdinsight-use-hive.md)
- [Utiliser cochon avec HDInsight](hdinsight-use-pig.md)
- [Utiliser Oozie avec HDInsight][hdinsight-use-oozie]: action Sqoop utilisation dans un flux de travail Oozie.
- [Analyser des données de retard aérienne à l’aide de HDInsight][hdinsight-analyze-flight-data]: utiliser la ruche pour analyser aérienne retarder des données et ensuite utiliser Sqoop pour exporter des données dans une base de données SQL Azure.
- [Télécharger des données à HDInsight][hdinsight-upload-data]: trouver d’autres méthodes pour le téléchargement des données à HDInsight/Azure Blob storage.


## <a name="appendix-a---a-powershell-sample"></a>Appendice A - un échantillon de PowerShell

L’exemple de PowerShell effectue les opérations suivantes :

1. Se connecter à Azure.
2. Créer un groupe de ressources Azure. Pour plus d’informations, voir [Utilisation de PowerShell Azure avec le Gestionnaire de ressources Azure](../powershell-azure-resource-manager.md)
3. Créer un serveur de base de données SQL Azure, une base de données SQL Azure et deux tables. 

    Si vous utilisez SQL Server à la place, utilisez les instructions suivantes pour créer les tables :
    
        CREATE TABLE [dbo].[log4jlogs](
         [t1] [nvarchar](50),
         [t2] [nvarchar](50),
         [t3] [nvarchar](50),
         [t4] [nvarchar](50),
         [t5] [nvarchar](50),
         [t6] [nvarchar](50),
         [t7] [nvarchar](50))

        CREATE TABLE [dbo].[mobiledata](
         [clientid] [nvarchar](50),
         [querytime] [nvarchar](50),
         [market] [nvarchar](50),
         [deviceplatform] [nvarchar](50),
         [devicemake] [nvarchar](50),
         [devicemodel] [nvarchar](50),
         [state] [nvarchar](50),
         [country] [nvarchar](50),
         [querydwelltime] [float],
         [sessionid] [bigint],
         [sessionpagevieworder][bigint])

    Pour examiner la base de données et les tables, le plus simple consiste à utiliser Visual Studio. Le serveur de base de données et la base de données peuvent être examinés à l’aide du portail Azure.

4. Créer un cluster HDInsight.

    Pour examiner le cluster, vous pouvez utiliser le portail Azure ou PowerShell Azure.

5. Prétraitement du fichier de données source.

    Dans ce didacticiel, vous allez exporter un fichier de journal log4j (un fichier délimité) et une table Hive dans une base de données SQL Azure. Le fichier délimité par des tabulations est appelé */example/data/sample.log*. Précédemment dans le didacticiel, vous avez vu quelques exemples de log4j journaux. Dans le fichier journal, il existe certaines lignes vides et des lignes similaires à ceux-ci :
    
        java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
            at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)
    
    Il s’agit d’une qualité suffisante pour d’autres exemples d’utilisation de ces données, mais nous devons supprimer ces exceptions avant que nous puissions importer dans la base de données SQL Azure ou SQL Server. Exportation de Sqoop échoue s’il existe une chaîne vide ou une ligne avec un moins nombre d’éléments que le nombre de champs définis dans la table de base de données SQL Azure. La table log4jlogs comporte 7 champs de type chaîne.

    Cette procédure crée un nouveau fichier sur le cluster : tutorials/usesqoop/data/sample.log. Pour examiner le fichier de données modifiées, vous pouvez utiliser le portail Azure, un outil Explorateur de stockage Azure ou PowerShell Azure. [Prise en main HDInsight] [ hdinsight-get-started] contient un exemple de code pour l’utilisation de PowerShell Azure pour télécharger un fichier et afficher le contenu du fichier.

6. Exporter un fichier de données dans la base de données SQL Azure.

    Le fichier source est tutorials/usesqoop/data/sample.log. La table dans laquelle les données sont exportées vers est appelée log4jlogs.
    
    > [AZURE.NOTE] Autres que les informations de chaîne de connexion, les étapes décrites dans cette section doivent utiliser pour une base de données SQL Azure ou SQL Server. Ces étapes ont été testés à l’aide de la configuration suivante :
    >
    > * **Configuration du point-à-site Azure réseau virtuel**: un réseau virtuel connecté le cluster HDInsight à un serveur SQL Server dans un centre de données privée. Pour plus d’informations, voir [configurer un réseau privé virtuel Point-à-Site du portail de gestion](../vpn-gateway/vpn-gateway-point-to-site-create.md) .
    > * **Azure HDInsight 3.1**: voir [Hadoop créer des groupes dans un HDInsight à l’aide des options personnalisées](hdinsight-provision-clusters.md) pour plus d’informations sur la création d’un cluster sur un réseau virtuel.
    > * **SQL Server 2014**: configuré pour permettre l’authentification et en exécutant le client VPN colis configuration pour vous connecter en toute sécurité au réseau virtuel.

7. Exporter une table Hive vers la base de données SQL Azure.

8. Importer la table mobiledata pour le cluster HDInsight.

    Pour examiner le fichier de données modifiées, vous pouvez utiliser le portail Azure, un outil Explorateur de stockage Azure ou PowerShell Azure.  [Prise en main HDInsight] [ hdinsight-get-started] contient un exemple de code sur l’utilisation de PowerShell Azure pour télécharger un fichier et afficher le contenu du fichier.


### <a name="the-powershell-sample"></a>L’exemple de PowerShell

    # Prepare an Azure SQL database to be used by the Sqoop tutorial
    
    #region - provide the following values
    
    $subscriptionID = "<Enter your Azure Subscription ID>"
    
    $sqlDatabaseLogin = "<Enter a SQL Database Login name>" #SQL Database server login
    $sqlDatabasePassword = "<Enter a Password>"
    
    $httpUserName = "admin"  #HDInsight cluster username
    $httpPassword = "<Enter a Password>"
    
    # used for creating Azure service names
    $nameToken = "<Enter an alias>" 
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
    #endregion
    
    #region - variables
    
    # Resource group variables
    $resourceGroupName = $namePrefix + "rg"
    $location = "East US 2" # used by all Azure services defined in this tutorial
    
    # SQL database varialbes
    $sqlDatabaseServerName = $namePrefix + "sqldbserver"
    $sqlDatabaseName = $namePrefix + "sqldb"
    $sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
    $sqlDatabaseMaxSizeGB = 10
    
    # Used for retrieving external IP address and creating firewall rules
    $ipAddressRestService = "http://bot.whatismyipaddress.com"
    $fireWallRuleName = "UseSqoop"
    
    # Used for creating tables and clustered indexes
    $cmdCreateLog4jTable = "CREATE TABLE [dbo].[log4jlogs](
        [t1] [nvarchar](50),
        [t2] [nvarchar](50),
        [t3] [nvarchar](50),
        [t4] [nvarchar](50),
        [t5] [nvarchar](50),
        [t6] [nvarchar](50),
        [t7] [nvarchar](50))"
    
    $cmdCreateLog4jClusteredIndex = "CREATE CLUSTERED INDEX log4jlogs_clustered_index on log4jlogs(t1)"
    
    $cmdCreateMobileTable = " CREATE TABLE [dbo].[mobiledata](
    [clientid] [nvarchar](50),
    [querytime] [nvarchar](50),
    [market] [nvarchar](50),
    [deviceplatform] [nvarchar](50),
    [devicemake] [nvarchar](50),
    [devicemodel] [nvarchar](50),
    [state] [nvarchar](50),
    [country] [nvarchar](50),
    [querydwelltime] [float],
    [sessionid] [bigint],
    [sessionpagevieworder][bigint])"
    
    $cmdCreateMobileDataClusteredIndex = "CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)"
    
    # HDInsight variables
    $hdinsightClusterName = $namePrefix + "hdi"
    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $hdinsightClusterName
    #endregion
    
    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"
    
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion
    
    #region - Create Azure resouce group
    Write-Host "`nCreating an Azure resource group ..." -ForegroundColor Green
    try{
        Get-AzureRmResourceGroup -Name $resourceGroupName
    }
    catch{
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    }
    #endregion
    
    #region - Create Azure SQL database server
    Write-Host "`nCreating an Azure SQL Database server ..." -ForegroundColor Green
    try{
        Get-AzureRmSqlServer -ServerName $sqlDatabaseServerName -ResourceGroupName $resourceGroupName}
    catch{
        Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
    
        $sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
        $credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)
    
        $sqlDatabaseServerName = (New-AzureRmSqlServer `
                                    -ResourceGroupName $resourceGroupName `
                                    -ServerName $sqlDatabaseServerName `
                                    -SqlAdministratorCredentials $credential `
                                    -Location $location).ServerName
        Write-Host "`tThe new SQL database server name is $sqlDatabaseServerName." -ForegroundColor Cyan
    
        Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
        $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
        New-AzureRmSqlServerFirewallRule `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -FirewallRuleName "$fireWallRuleName-workstation" `
            -StartIpAddress $workstationIPAddress `
            -EndIpAddress $workstationIPAddress
    
        #To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. 
        #Note that this allows Azure traffic from any Azure subscription to access the server.
        New-AzureRmSqlServerFirewallRule `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -FirewallRuleName "$fireWallRuleName-Azureservices" `
            -StartIpAddress "0.0.0.0" `
            -EndIpAddress "0.0.0.0"
    }
    
    #endregion
    
    #region - Create and validate Azure SQL database
    Write-Host "`nCreating an Azure SQL database ..." -ForegroundColor Green
    
    try {
        Get-AzureRmSqlDatabase `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -DatabaseName $sqlDatabaseName
    }
    catch {
        Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
        New-AzureRMSqlDatabase `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -DatabaseName $sqlDatabaseName `
            -Edition "Standard" `
            -RequestedServiceObjectiveName "S1"
    }
    
    #endregion
    
    #region - Create tables
    Write-Host "Creating the log4jlogs table and the mobiledata table ..." -ForegroundColor Green
    
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = $sqlDatabaseConnectionString
    $conn.Open()
    
    # Create the log4jlogs table and index
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.Connection = $conn
    $cmd.CommandText = $cmdCreateLog4jTable
    $ret = $cmd.ExecuteNonQuery()
    $cmd.CommandText = $cmdCreateLog4jClusteredIndex
    $cmd.ExecuteNonQuery()
    
    # Create the mobiledata table and index
    $cmd.CommandText = $cmdCreateMobileTable
    $cmd.ExecuteNonQuery()
    $cmd.CommandText = $cmdCreateMobileDataClusteredIndex
    $cmd.ExecuteNonQuery()
    
    $conn.close()
    
    #endregion
    
    
    #region - Create HDInsight cluster
    
    Write-Host "Creating the HDInsight cluster and the dependent services ..." -ForegroundColor Green
    
    # Create the default storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_LRS
    
    # Create the default Blob container
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext `
                                        -StorageAccountName $defaultStorageAccountName `
                                        -StorageAccountKey $defaultStorageAccountKey 
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageAccountContext 
    
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
        -DefaultStorageContainer $defaultBlobContainerName 
    
    # Validate the cluster
    Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName
    #endregion
    
    #region - pre-process the source file
    
    Write-Host "Preprocessing the source file ..." -ForegroundColor Green
    
    # This procedure creates a new file with $destBlobName
    $sourceBlobName = "example/data/sample.log"
    $destBlobName = "tutorials/usesqoop/data/sample.log"
    
    # Define the connection string
    $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"
    
    # Create block blob objects referencing the source and destination blob.
    $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
    $storageClient = $storageAccount.CreateCloudBlobClient();
    $storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
    $sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
    $destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
    
    # Define a MemoryStream and a StreamReader for reading from the source file
    $stream = New-Object System.IO.MemoryStream
    $stream = $sourceBlob.OpenRead()
    $sReader = New-Object System.IO.StreamReader($stream)
    
    # Define a MemoryStream and a StreamWriter for writing into the destination file
    $memStream = New-Object System.IO.MemoryStream
    $writeStream = New-Object System.IO.StreamWriter $memStream
    
    # Pre-process the source blob
    $exString = "java.lang.Exception:"
    while(-Not $sReader.EndOfStream){
        $line = $sReader.ReadLine()
        $split = $line.Split(" ")
    
        # remove the "java.lang.Exception" from the first element of the array
        # for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
        if ($split[0] -eq $exString){
            #create a new ArrayList to remove $split[0]
            $newArray = [System.Collections.ArrayList] $split
            $newArray.Remove($exString)
    
            # update $split and $line
            $split = $newArray
            $line = $newArray -join(" ")
        }
    
        # remove the lines that has less than 7 elements
        if ($split.count -ge 7){
            write-host $line
            $writeStream.WriteLine($line)
        }
    }
    
    # Write to the destination blob
    $writeStream.Flush()
    $memStream.Seek(0, "Begin")
    $destBlob.UploadFromStream($memStream)
    
    #endregion
    
    #region - export a log file from the cluster to the SQL database
    
    Write-Host "Preprocessing the source file ..." -ForegroundColor Green
    
    $tableName_log4j = "log4jlogs"
    
    # Connection string for Azure SQL Database.
    # Comment if using SQL Server
    $connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"
    # Connection string for SQL Server.
    # Uncomment if using SQL Server.
    #$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$sqlDatabaseName"
    
    $exportDir_log4j = "/tutorials/usesqoop/data"
    
    # Submit a Sqoop job
    $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
        -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
    $sqoopJob = Start-AzureRmHDInsightJob `
                    -ClusterName $hdinsightClusterName `
                    -HttpCredential $httpCredential `
                    -JobDefinition $sqoopDef #-Debug -Verbose
    Wait-AzureRmHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId
    
    Write-Host "Standard Error" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardError
    Write-Host "Standard Output" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardOutput
    
    #endregion
    
    #region - export a Hive table
    
    $tableName_mobile = "mobiledata"
    $exportDir_mobile = "/hive/warehouse/hivesampletable"
    
    $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
        -Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"
    $sqoopJob = Start-AzureRmHDInsightJob `
                    -ClusterName $hdinsightClusterName `
                    -HttpCredential $httpCredential `
                    -JobDefinition $sqoopDef #-Debug -Verbose
    
    Wait-AzureRmHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId
    
    Write-Host "Standard Error" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -DefaultStorageAccountName $defaultStorageAccountName `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultContainer $defaultBlobContainerName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId `
        -DisplayOutputType StandardError
    
    Write-Host "Standard Output" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -DefaultStorageAccountName $defaultStorageAccountName `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultContainer $defaultBlobContainerName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId `
        -DisplayOutputType StandardOutput
    
    #endregion
    
    #region - import a database
    
    $targetDir_mobile = "/tutorials/usesqoop/importeddata/"
    
    $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
        -Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"
    
    $sqoopJob = Start-AzureRmHDInsightJob `
                    -ClusterName $hdinsightClusterName `
                    -HttpCredential $httpCredential `
                    -JobDefinition $sqoopDef #-Debug -Verbose
    
    Wait-AzureRmHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId
    
    Write-Host "Standard Error" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -DefaultStorageAccountName $defaultStorageAccountName `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultContainer $defaultBlobContainerName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId `
        -DisplayOutputType StandardError
    
    Write-Host "Standard Output" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -DefaultStorageAccountName $defaultStorageAccountName `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultContainer $defaultBlobContainerName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId `
        -DisplayOutputType StandardOutput
    
    #endregion



[azure-management-portal]: https://portal.azure.com/

[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database/sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database/sql-database-get-started.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
