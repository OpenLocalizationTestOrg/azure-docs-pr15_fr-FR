<properties 
   pageTitle="Prise en main Azure données Lake Analytique à l’aide de PowerShell Azure | Azure" 
   description="Découvrez comment utiliser la session PowerShell Azure pour créer un compte Data Lake Store, créez une tâche de données Lake Analytique à l’aide de U-SQL et soumettre la tâche. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/21/2016"
   ms.author="edmaca"/>

# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Didacticiel : prise en main Azure données Lake Analytique à l’aide de PowerShell Azure

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Découvrez comment utiliser la session PowerShell Azure pour créer des comptes Azure données Lake Analytique, définir des tâches de données Lake Analytique en [U-SQL](data-lake-analytics-u-sql-get-started.md)et soumettre des travaux aux données Lake analytique comptes. Pour plus d’informations sur les données Lake Analytique, voir [vue d’ensemble Azure données Lake Analytique](data-lake-analytics-overview.md).

Dans ce didacticiel, vous allez développer une tâche qui lit un onglet fichier CSV (TSV) et la convertit dans un fichier CSV (valeurs) séparées par des virgules. Pour ouvrir le même didacticiel à l’aide d’autres outils pris en charge, cliquez sur les onglets situés en haut de cette section.

##<a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).
- **Un poste de travail avec Azure PowerShell**. Découvrez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).
    
##<a name="create-data-lake-analytics-account"></a>Créer le compte de données Lake Analytique

Vous devez disposer un compte données Lake Analytique avant de pouvoir exécuter toutes les tâches. Pour créer un compte de données Lake Analytique, vous devez spécifier les éléments suivants :

- **Groupe de ressources Azure**: A données Lake Analytique compte doit être créé dans un groupe de ressources Azure. [Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md) permet de vous permettent de travailler avec les ressources dans votre application en tant que groupe. Vous pouvez déployer, mettre à jour ou supprimer toutes les ressources pour votre application dans une seule opération coordonnée.  

    Énumérer les groupes de ressources dans votre abonnement :
    
        Get-AzureRmResourceGroup
    
    Pour créer un nouveau groupe de ressources :

        New-AzureRmResourceGroup `
            -Name "<Your resource group name>" `
            -Location "<Azure Data Center>" # For example, "East US 2"

- **Nom du compte Analytique Lake de données**
- **Emplacement**: parmi les centres de données Azure qui prend en charge les données Lake Analytique.
- **Compte par défaut données Lake**: chaque compte données Lake Analytique possède un compte de données Lake par défaut.

    Pour créer un nouveau compte Lake de données :

        New-AzureRmDataLakeStoreAccount `
            -ResourceGroupName "<Your Azure resource group name>" `
            -Name "<Your Data Lake account name>" `
            -Location "<Azure Data Center>"  # For example, "East US 2"

    > [AZURE.NOTE] Le nom du compte données Lake doit contenir uniquement les lettres minuscules et numéros.



**Pour créer un compte de données Lake Analytique**

1. Ouvrez PowerShell ISE à partir de votre poste de travail Windows.
2. Exécutez le script suivant :

        $resourceGroupName = "<ResourceGroupName>"
        $dataLakeStoreName = "<DataLakeAccountName>"
        $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
        $location = "East US 2"
        
        Write-Host "Create a resource group ..." -ForegroundColor Green
        New-AzureRmResourceGroup `
            -Name  $resourceGroupName `
            -Location $location
        
        Write-Host "Create a Data Lake account ..."  -ForegroundColor Green
        New-AzureRmDataLakeStoreAccount `
            -ResourceGroupName $resourceGroupName `
            -Name $dataLakeStoreName `
            -Location $location 
        
        Write-Host "Create a Data Lake Analytics account ..."  -ForegroundColor Green
        New-AzureRmDataLakeAnalyticsAccount `
            -Name $dataLakeAnalyticsName `
            -ResourceGroupName $resourceGroupName `
            -Location $location `
            -DefaultDataLake $dataLakeStoreName
        
        Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
        Get-AzureRmDataLakeAnalyticsAccount `
            -ResourceGroupName $resourceGroupName `
            -Name $dataLakeAnalyticsName  

##<a name="upload-data-to-data-lake"></a>Télécharger des données sur Lake de données

Dans ce didacticiel, vous traitera des journaux de recherche.  Le journal de recherche pouvant être stocké dans le magasin de données Lake ou stockage d’objets Blob Azure. 

Un exemple de fichier journal recherche a été copié dans un conteneur Blob Azure public. Utilisez le script PowerShell suivant pour télécharger le fichier sur votre poste de travail, puis téléchargez le fichier sur le compte de données Lake Store par défaut de votre compte de données Lake Analytique.

    $dataLakeStoreName = "<The default Data Lake Store account name>"
    
    $localFolder = "C:\Tutorials\Downloads\" # A temp location for the file. 
    $storageAccount = "adltutorials"  # Don't modify this value.
    $container = "adls-sample-data"  #Don't modify this value.

    # Create the temp location  
    New-Item -Path $localFolder -ItemType Directory -Force 

    # Download the sample file from Azure Blob storage
    $context = New-AzureStorageContext -StorageAccountName $storageAccount -Anonymous
    $blobs = Azure\Get-AzureStorageBlob -Container $container -Context $context
    $blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

    # Upload the file to the default Data Lake Store account    
    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $localFolder"SearchLog.tsv" -Destination "/Samples/Data/SearchLog.tsv"

Le script PowerShell suivant vous montre comment obtenir le nom du magasin de Lake de données par défaut pour un compte Analytique Lake de données :


    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticsName).Properties.DefaultDataLakeAccount
    echo $dataLakeStoreName

>[AZURE.NOTE] Le portail Azure fournit une interface utilisateur pour copier les fichiers d’exemples de données sur le compte de données Lake Store par défaut. Pour plus d’informations, voir [Prise en main Azure données Lake Analytique à l’aide du portail Azure](data-lake-analytics-get-started-portal.md#upload-data-to-the-default-data-lake-store-account).

Données Lake Analytique peut également accéder à stockage d’objets Blob Azure.  Pour le téléchargement des données vers le stockage Blob Azure, voir [Utilisation de PowerShell Azure avec stockage Azure](../storage/storage-powershell-guide-full.md).

##<a name="submit-data-lake-analytics-jobs"></a>Soumettre les tâches de données Lake Analytique

Les tâches de données Lake Analytique sont écrits en langage SQL-U. Pour en savoir plus sur U-SQL, voir [prise en main U-SQL langue](data-lake-analytics-u-sql-get-started.md) et [référence du langage SQL-U](http://go.microsoft.com/fwlink/?LinkId=691348).

**Pour créer un script de travail données Lake Analytique**

- Créer un fichier texte contenant script U-SQL suivant et enregistrez le fichier texte sur votre poste de travail :

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

    Ce script U SQL lit le fichier de données source à l’aide de **Extractors.Tsv()**, puis crée un fichier csv à l’aide de **Outputters.Csv()**. 
    
    Ne modifiez pas les deux chemins d’accès, sauf si vous copiez le fichier source dans un autre emplacement.  Données Lake Analytique créera le dossier de sortie s’il n’existe pas.
    
    Il est plus simple d’utiliser les chemins d’accès relatifs pour les fichiers stockés par défaut données comptes Lake. Vous pouvez également utiliser les chemins d’accès absolus.  Par exemple 
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    Vous devez utiliser des chemins d’accès absolus pour accéder aux fichiers dans les comptes de stockage liés.  La syntaxe pour les fichiers stockés dans un compte de stockage Azure lié est la suivante :
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] Conteneur Blob Azure disposant d’autorisations d’accès conteneurs public ou des objets BLOB publics ne sont actuellement pas pris en charge.    
    
    
**Soumettre le travail**

1. Ouvrez PowerShell ISE à partir de votre poste de travail de Windows.
2. Exécutez le script suivant :

        $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
        $usqlScript = "c:\tutorials\data-lake-analytics\copyFile.usql"
        
        $job = Submit-AzureRmDataLakeAnalyticsJob -Name "convertTSVtoCSV" -AccountName $dataLakeAnalyticsName –ScriptPath $usqlScript 

        Wait-AdlJob -Account $dataLakeAnalyticsName -JobId $job.JobId

        Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticsName -JobId $job.JobId

    Dans le script, le fichier de script U-SQL est stocké en c:\tutorials\data-lake-analytics\copyFile.usql. Mettre à jour le chemin d’accès en conséquence.
 
Une fois que la tâche est terminée, vous pouvez utiliser les applets de commande suivantes pour le fichier s’affiche et téléchargez le fichier :
    
    $resourceGroupName = "<Resource Group Name>"
    $dataLakeAnalyticName = "<Data Lake Analytic Account Name>"
    $destFile = "C:\tutorials\data-lake-analytics\SearchLog-from-Data-Lake.csv"
    
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount
    
    Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -path "/Output"
    
    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "/Output/SearchLog-from-Data-Lake.csv" -Destination $destFile

## <a name="see-also"></a>Voir aussi

- Pour afficher le même didacticiel à l’aide d’autres outils, cliquez sur les sélecteurs onglet en haut de la page.
- Pour plus d’une requête plus complexe, voir [journaux d’analyse Web à l’aide de Azure données Lake Analytique](data-lake-analytics-analyze-weblogs.md).
- Pour commencer à développer des applications U-SQL, voir [scripts U-SQL développer à l’aide de données Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Pour plus d’U-SQL, voir [prise en main langue données Lake Analytique U-SQL Azure](data-lake-analytics-u-sql-get-started.md).
- Pour les tâches de gestion, voir [Gérer des Analytique des Lake Azure données à l’aide du portail Azure](data-lake-analytics-manage-use-portal.md).
- Pour obtenir une vue d’ensemble de données Lake Analytique, voir [vue d’ensemble Azure données Lake Analytique](data-lake-analytics-overview.md).

