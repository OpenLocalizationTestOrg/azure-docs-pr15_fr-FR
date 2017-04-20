<properties
   pageTitle="Créer des clusters Hadoop fonctionnant sous Windows dans HDInsight à l’aide de PowerShell Azure | Microsoft Azure"
    description="Apprenez à créer des clusters pour Azure HDInsight à l’aide de PowerShell Azure."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/10/2016"
   ms.author="jgao"/>

# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-azure-powershell"></a>Créer des clusters Hadoop fonctionnant sous Windows dans HDInsight à l’aide de PowerShell Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Apprenez à créer des clusters HDInsight à l’aide de PowerShell Azure. PowerShell Azure est un module qui fournit des applets de commande pour gérer Azure avec Windows PowerShell. Pour d’autres création d’un cluster les fonctionnalités et outils cliquez sur l’onglet, sélectionnez en haut de cette page ou consultez [méthodes de création de Cluster](hdinsight-provision-clusters.md#cluster-creation-methods).


##<a name="prerequisites"></a>Conditions requises :

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Avant de commencer les instructions fournies dans cet article, vous devez disposer des éléments suivants :

- Abonnement Azure. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- PowerShell Azure.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

### <a name="access-control-requirements"></a>Exigences de contrôle d’accès

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>Créer des clusters
Azure PowerShell est un environnement de script puissant que vous pouvez utiliser pour contrôler et d’automatiser le déploiement et la gestion de vos charges de travail dans Azure. Cette section fournit des instructions sur la façon de créer un cluster HDInsight à l’aide de PowerShell Azure. Pour plus d’informations sur la configuration d’un poste de travail pour exécuter les applets de commande HDInsight Windows PowerShell, voir [installer et configurer Azure PowerShell](../powershell-install-configure.md). Pour plus d’informations sur l’utilisation de PowerShell Azure avec HDInsight, voir [administrer HDInsight à l’aide de PowerShell](hdinsight-administer-use-powershell.md). Pour la liste des applets de commande HDInsight Windows PowerShell, voir [informations de référence applet de commande HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).


Les procédures suivantes sont nécessaires pour créer un cluster HDInsight à l’aide de PowerShell Azure :

    ####################################
    # Set these variables
    ####################################
    #region - used for creating Azure service names
    $nameToken = "<Enter an Alias>" 
    #endregion

    #region - cluster user accounts
    $httpUserName = "admin"  #HDInsight cluster username
    $httpPassword = "<Enter a Password>"
    #endregion

    ###########################################
    # Service names and varialbes
    ###########################################
    #region - service names
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

    $resourceGroupName = $namePrefix + "rg"
    $hdinsightClusterName = $namePrefix + "hdi"
    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $hdinsightClusterName

    $location = "East US 2"
    $clusterSizeInNodes = 1
    #endregion

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    ###########################################
    # Connect to Azure
    ###########################################
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    ###########################################
    # Create the resource group
    ###########################################
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    ###########################################
    # Preapre default storage account and container
    ###########################################
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Type Standard_GRS `
        -Location $location

    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer `
        -Name $hdinsightClusterName -Context $defaultStorageContext 

    ###########################################
    # Create the cluster
    ###########################################

    $httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -Location $location `
        -ClusterSizeInNodes $clusterSizeInNodes `
        -ClusterType Hadoop `
        -OSType Windows `
        -Version "3.2" `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $hdinsightClusterName 

    ####################################
    # Verify the cluster
    ####################################
    Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName 

## <a name="create-clusters-using-arm-template"></a>Créer des clusters à l’aide du modèle de processeur

Vous pouvez utiliser PowerShell Azure pour déployer un modèle de processeur qui crée un cluster HDInsight.  Voir [des modèles à l’aide de PowerShell Azure d’appel](hdinsight-hadoop-create-windows-clusters-arm-templates.md#call-templates-using-powershell).

##<a name="customize-clusters"></a>Personnaliser les clusters

- Voir [clusters HDInsight personnaliser à l’aide de démarrage](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
- Voir [clusters HDInsight fonctionnant sous Windows personnaliser à l’aide de Script Action](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).


##<a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris différentes façons pour créer un cluster HDInsight. Pour plus d’informations, consultez les articles suivants :

* [Prise en main Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) - Apprenez à commencer à travailler avec votre cluster HDInsight
* [Hadoop soumettre des travaux par programme](hdinsight-submit-hadoop-jobs-programmatically.md) - Apprenez à soumettre des tâches à HDInsight par programme
* [Hadoop gérer les groupes dans un HDInsight à l’aide de PowerShell](hdinsight-administer-use-powershell.md) - apprendre à utiliser avec HDInsight à l’aide de PowerShell Azure
* [Documentation présentée sous d’Azure HDInsight SDK]  [ hdinsight-sdk-documentation] -découvrir le SDK HDInsight




[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[azure-preview-portal]: https://manage.windowsazure.com
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[ssisclustercreate]: http://msdn.microsoft.com/library/mt146774(v=sql.120).aspx
[ssisclusterdelete]: http://msdn.microsoft.com/library/mt146778(v=sql.120).aspx
