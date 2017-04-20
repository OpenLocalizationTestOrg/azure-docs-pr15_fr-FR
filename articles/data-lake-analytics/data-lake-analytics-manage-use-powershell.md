<properties 
   pageTitle="Gérer les Azure données Lake Analytique à l’aide de PowerShell Azure | Azure" 
   description="Découvrez comment gérer les travaux de données Lake Analytique, les sources de données, les utilisateurs. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Gérer les Azure données Lake Analytique à l’aide de PowerShell Azure

[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Découvrez comment gérer les comptes Azure données Lake Analytique, des sources de données, des utilisateurs et des tâches à l’aide de la session PowerShell Azure. Pour afficher les rubriques gestion à l’aide d’autres outils, cliquez sur l’onglet, sélectionnez ci-dessus.

**Conditions préalables**

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).


<!-- ################################ -->
<!-- ################################ -->


##<a name="install-azure-powershell-10-or-greater"></a>Installer Azure PowerShell 1.0 ou version ultérieure

Consultez la section prérequises [d’Utilisation de PowerShell Azure avec le Gestionnaire de ressources Azure](powershell-azure-resource-manager.md#prerequisites).
    
## <a name="manage-accounts"></a>Gérer les comptes

Avant l’exécution de toutes les tâches données Lake Analytique, vous devez disposer d’un compte de données Lake Analytique. Contrairement à Azure HDInsight, vous ne pas payer pour un compte Analytique lorsqu’elle s’exécute pas une tâche.  Vous payez uniquement l’heure lorsqu’il s’exécute une tâche.  Pour plus d’informations, voir [Vue d’ensemble du Analytique Lake données Azure](data-lake-analytics-overview.md).  

###<a name="create-accounts"></a>Créer des comptes

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeStoreName = "<DataLakeAccountName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $location = "<Microsoft Data Center>"
    
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
        -Name $dataLakeAnalyticsAccountName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultDataLake $dataLakeStoreName
    
    Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
    Get-AzureRmDataLakeAnalyticsAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $dataLakeAnalyticsAccountName  

Vous pouvez également utiliser un modèle de groupe de ressources Azure. Un modèle pour la création d’un compte Analytique Lake de données et le compte données Lake dépendant est dans [l’annexe A](#appendix-a). Enregistrer le modèle vers un fichier de modèle .json, puis utilisez le script PowerShell suivant pour pouvoir l’appeler :


    $AzureSubscriptionID = "<Your Azure Subscription ID>"
    
    $ResourceGroupName = "<New Azure Resource Group Name>"
    $Location = "EAST US 2"
    $DefaultDataLakeStoreAccountName = "<New Data Lake Store Account Name>"
    $DataLakeAnalyticsAccountName = "<New Data Lake Analytics Account Name>"
    
    $DeploymentName = "MyDataLakeAnalyticsDeployment"
    $ARMTemplateFile = "E:\Tutorials\ADL\ARMTemplate\azuredeploy.json"  # update the Json template path 
    
    Login-AzureRmAccount
    
    Select-AzureRmSubscription -SubscriptionId $AzureSubscriptionID
    
    # Create the resource group
    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
    
    # Create the Data Lake Analytics account with the default Data Lake Store account.
    $parameters = @{"adlAnalyticsName"=$DataLakeAnalyticsAccountName; "adlStoreName"=$DefaultDataLakeStoreAccountName}
    New-AzureRmResourceGroupDeployment -Name $DeploymentName -ResourceGroupName $ResourceGroupName -TemplateFile $ARMTemplateFile -TemplateParameterObject $parameters 

 
###<a name="list-account"></a>Liste de compte

Comptes d’Analytique liste données Lake au sein de l’abonnement en cours

    Get-AzureRmDataLakeAnalyticsAccount
    
Le résultat :

    Id         : /subscriptions/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/resourceGroups/learn1021rg/providers/Microsoft.DataLakeAnalytics/accounts/learn1021adla
    Location   : eastus2
    Name       : learn1021adla
    Properties : Microsoft.Azure.Management.DataLake.Analytics.Models.DataLakeAnalyticsAccountProperties
    Tags       : {}
    Type       : Microsoft.DataLakeAnalytics/accounts

Comptes d’Analytique Lake de données de liste dans un groupe de ressources spécifique

    Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName

Obtenir des détails d’un compte de données Lake Analytique spécifique

    Get-AzureRmDataLakeAnalyticsAccount -Name $adlAnalyticsAccountName

Tester l’existence d’un compte de données Lake Analytique spécifique

    Test-AzureRmDataLakeAnalyticsAccount -Name $adlAnalyticsAccountName

L’applet de commande retourne **True** ou **False**.

###<a name="delete-data-lake-analytics-accounts"></a>Supprimer des comptes de données Lake Analytique

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    
    Remove-AzureRmDataLakeAnalyticsAccount -Name $dataLakeAnalyticsAccountName 

Compte Analytique de supprimer des données Lake ne supprime pas le compte de stockage des données Lake dépendant. L’exemple suivant supprime le compte Analytique Lake de données et le compte de données Lake Store par défaut

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.DefaultDataLakeAccount

    Remove-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName 
    Remove-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-account-data-sources"></a>Gérer les sources de données de compte

Données Lake Analytique prend actuellement en charge les sources de données suivantes :

- [Magasin de Lake données Azure](../data-lake-store/data-lake-store-overview.md)
- [Stockage Azure](storage-introduction.md)

Lorsque vous créez un compte Analytique, vous devez désigner un compte de stockage Lake des données Azure le compte de stockage par défaut. Le compte par défaut données Lake permet de stocker le travail métadonnées et tâche les journaux d’audit. Après avoir créé un compte Analytique, vous pouvez ajouter des comptes supplémentaires de stockage des données Lake et/ou compte de stockage Azure. 

### <a name="find-the-default-data-lake-store-account"></a>Trouver le compte de données Lake Store par défaut

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.DefaultDataLakeAccount


### <a name="add-additional-azure-blob-storage-accounts"></a>Ajouter d’autres comptes de stockage Blob Azure

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $AzureStorageAccountName = "<AzureStorageAccountName>"
    $AzureStorageAccountKey = "<AzureStorageAccountKey>"
    
    Add-AzureRmDataLakeAnalyticsDataSource -ResourceGroupName $resourceGroupName -Account $dataLakeAnalyticAccountName -AzureBlob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

### <a name="add-additional-data-lake-store-accounts"></a>Ajouter d’autres comptes de données Lake Store

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $AzureDataLakeName = "<DataLakeStoreName>"
    
    Add-AzureRmDataLakeAnalyticsDataSource -ResourceGroupName $resourceGroupName -Account $dataLakeAnalyticAccountName -DataLake $AzureDataLakeName 

### <a name="list-data-sources"></a>Liste des sources de données :

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

    (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.DataLakeStoreAccounts
    (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.StorageAccounts
    


<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-jobs"></a>Gérer les travaux

Vous devez disposer un compte données Lake Analytique avant de pouvoir créer une tâche.  Pour plus d’informations, voir [Gérer les données Lake Analytique comptes](#manage-data-lake-analytics-accounts).

### <a name="list-jobs"></a>Répertorier les tâches

    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName
    
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -State Running, Queued
    #States: Accepted, Compiling, Ended, New, Paused, Queued, Running, Scheduling, Starting
    
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -Result Cancelled
    #Results: Cancelled, Failed, None, Successed 
    
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -Name <Job Name>
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -Submitter <Job submitter>

    # List all jobs submitted on January 1 (local time)
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -SubmittedAfter "2015/01/01"
        -SubmittedBefore "2015/01/02"   

    # List all jobs that succeeded on January 1 after 2 pm (UTC time)
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -State Ended
        -Result Succeeded
        -SubmittedAfter "2015/01/01 2:00 PM -0"
        -SubmittedBefore "2015/01/02 -0"

    # List all jobs submitted in the past hour
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -SubmittedAfter (Get-Date).AddHours(-1)

### <a name="get-job-details"></a>Obtenir des détails d’une tâche

    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -JobID <Job ID>
    
### <a name="submit-jobs"></a>Soumettre des tâches

    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

    #Pass script via path
    Submit-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -Name $jobName `
        -ScriptPath $scriptPath

    #Pass script contents
    Submit-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -Name $jobName `
        -Script $scriptContents

> [AZURE.NOTE] La priorité par défaut d’une tâche est de 1 000 et degré de parallélisme pour une tâche par défaut est 1.


### <a name="cancel-jobs"></a>Annuler les travaux

    Stop-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -JobID $jobID


## <a name="manage-catalog-items"></a>Gérer les éléments du catalogue

Le catalogue U-SQL est utilisé pour structurer des données et le code afin qu’ils peuvent être partagés par les scripts U-SQL. Le catalogue permet les meilleures performances possibles avec des données dans Azure données Lake. Pour plus d’informations, voir [utiliser U-SQL catalogue](data-lake-analytics-use-u-sql-catalog.md).

###<a name="list-catalog-items"></a>Liste des éléments du catalogue

    #List databases
    Get-AzureRmDataLakeAnalyticsCatalogItem `
        -Account $adlAnalyticsAccountName `
        -ItemType Database
    
    
    
    #List tables
    Get-AzureRmDataLakeAnalyticsCatalogItem `
        -Account $adlAnalyticsAccountName `
        -ItemType Table `
        -Path "master.dbo"

###<a name="get-catalog-item-details"></a>Obtenez des informations d’élément de catalogue 

    #Get a database
    Get-AzureRmDataLakeAnalyticsCatalogItem `
        -Account $adlAnalyticsAccountName `
        -ItemType Database `
        -Path "master"
    
    #Get a table
    Get-AzureRmDataLakeAnalyticsCatalogItem `
        -Account $adlAnalyticsAccountName `
        -ItemType Table `
        -Path "master.dbo.mytable"

###<a name="test-existence-of--catalog-item"></a>Tester l’existence d’un élément de catalogue

    Test-AzureRmDataLakeAnalyticsCatalogItem  `
        -Account $adlAnalyticsAccountName `
        -ItemType Database `
        -Path "master"

###<a name="create-catalog-secret"></a>Créer le code secret de catalogue
    New-AzureRmDataLakeAnalyticsCatalogSecret  `
            -Account $adlAnalyticsAccountName `
            -DatabaseName "master" `
            -Secret (Get-Credential -UserName "username" -Message "Enter the password")

### <a name="modify-catalog-secret"></a>Modifier le code secret catalogue
    Set-AzureRmDataLakeAnalyticsCatalogSecret  `
            -Account $adlAnalyticsAccountName `
            -DatabaseName "master" `
            -Secret (Get-Credential -UserName "username" -Message "Enter the password")



###<a name="delete-catalog-secret"></a>Supprimer le code secret de catalogue
    Remove-AzureRmDataLakeAnalyticsCatalogSecret  `
            -Account $adlAnalyticsAccountName `
            -DatabaseName "master"


## <a name="use-azure-resource-manager-groups"></a>Utiliser des groupes de gestionnaire de ressources Azure

Applications sont généralement constituées de nombreux composants, par exemple une application web, base de données, serveur de base de données, stockage et 3e partie services. Azure Resource Manager (ARM) vous permet de travailler avec les ressources dans votre application en tant que groupe, appelée un groupe de ressources Azure. Vous pouvez déployer, mettre à jour, surveiller ou supprimer toutes les ressources pour votre application dans une seule opération coordonnée. Vous utilisez un modèle pour le déploiement et ce modèle peut utiliser pour les différents environnements telles que le test, intermédiaire et production. Vous pouvez clarifier facturation pour votre organisation en affichant les coûts reportées pour l’ensemble du groupe. Pour plus d’informations, voir [Vue d’ensemble du Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md). 

Un service de données Lake Analytique peut inclure les éléments suivants :

- Compte d’Analytique Lake de données Azure
- Compte de stockage Lake des données Azure requis par défaut
- Comptes de stockage supplémentaire Azure données Lake
- Autres comptes de stockage Azure

Vous pouvez créer tous ces composants sous un seul groupe processeur afin de les retrouver plus facile à gérer.

![Stockage et compte Analytique de Lake de données azure](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Un compte de données Lake Analytique et les comptes de stockage dépendant doivent être placés dans le centre de données Azure même.
Le groupe processeur peut toutefois résider dans un centre de données différents.  

##<a name="see-also"></a>Voir aussi 

- [Vue d’ensemble de Microsoft Azure données Lake Analytique](data-lake-analytics-overview.md)
- [Prise en main données Lake Analytique à l’aide du portail Azure](data-lake-analytics-get-started-portal.md)
- [Gérer les Azure données Lake Analytique à l’aide du portail Azure](data-lake-analytics-manage-use-portal.md)
- [Surveiller et résoudre les problèmes de travaux Azure données Lake Analytique à l’aide du portail Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

##<a name="appendix-a---data-lake-analytics-arm-template"></a>Appendice A - modèle de données Lake Analytique ARM

Le modèle de processeur suivant peut servir à déployer un compte données Lake Analytique et son compte données Lake Store dépendante.  Enregistrer comme fichier json, puis utilisez le script PowerShell pour appeler le modèle. Pour plus d’informations, voir [déployer une application avec le Gestionnaire de ressources Azure modèle](../resource-group-template-deploy.md#deploy-with-powershell) et [Gestionnaire de ressources Azure de création de modèles](../resource-group-authoring-templates.md).

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adlAnalyticsName": {
          "type": "string",
          "metadata": {
            "description": "The name of the Data Lake Analytics account to create."
          }
        },
        "adlStoreName": {
          "type": "string",
          "metadata": {
            "description": "The name of the Data Lake Store account to create."
          }
        }
      },
      "resources": [
        {
          "name": "[parameters('adlStoreName')]",
          "type": "Microsoft.DataLakeStore/accounts",
          "location": "East US 2",
          "apiVersion": "2015-10-01-preview",
          "dependsOn": [ ],
          "tags": { }
        },
        {
          "name": "[parameters('adlAnalyticsName')]",
          "type": "Microsoft.DataLakeAnalytics/accounts",
          "location": "East US 2",
          "apiVersion": "2015-10-01-preview",
          "dependsOn": [ "[concat('Microsoft.DataLakeStore/accounts/',parameters('adlStoreName'))]" ],
          "tags": { },
          "properties": {
            "defaultDataLakeStoreAccount": "[parameters('adlStoreName')]",
            "dataLakeStoreAccounts": [
              { "name": "[parameters('adlStoreName')]" }
            ]
          }
        }
      ],
      "outputs": {
        "adlAnalyticsAccount": {
          "type": "object",
          "value": "[reference(resourceId('Microsoft.DataLakeAnalytics/accounts',parameters('adlAnalyticsName')))]"
        },
        "adlStoreAccount": {
          "type": "object",
          "value": "[reference(resourceId('Microsoft.DataLakeStore/accounts',parameters('adlStoreName')))]"
        }
      }
    }

