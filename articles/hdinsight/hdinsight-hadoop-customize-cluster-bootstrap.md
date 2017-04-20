<properties
    pageTitle="Personnaliser les HDInsight à l’aide de démarrage | Microsoft Azure"
    description="Apprenez à personnaliser les HDInsight à l’aide de démarrage."
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/02/2016"
    ms.author="jgao"/>

# <a name="customize-hdinsight-clusters-using-bootstrap"></a>Personnaliser les HDInsight à l’aide de démarrage

Parfois, vous souhaitez configurer les fichiers de configuration qui incluent :

- clusterIdentity.xml
- Core site.xml
- Gateway.Xml
- hbase env.xml
- hbase site.xml
- Hadoop site.xml
- Hive env.xml
- Hive site.xml
- mapred-site
- oozie site.xml
- oozie env.xml
- vague de site.xml
- tez site.xml
- webhcat site.xml
- fils site.xml

Les groupes ne peut pas conserver les modifications en raison de la création d’images. Pour plus d’informations sur la création d’images, voir [Rôle Instance redémarre en raison de mises à niveau du système d’exploitation](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx). Pour conserver les modifications apportées à la durée de vie des groupes, vous pouvez utiliser la personnalisation de cluster HDInsight pendant le processus de création. Il s’agit de la procédure recommandée pour modifier la configuration d’un cluster et persistent ces événements créer une nouvelle image Azure redémarrage redémarrer. Ces modifications de configuration sont appliquées avant le démarrage du service, donc services pas besoin d’être redémarrés. 

Il existe 3 méthodes à utiliser démarrage :

- Utiliser PowerShell Azure

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]
    
- Utiliser le Kit de développement .NET
- Utiliser un modèle de gestionnaire de ressources Azure

Pour plus d’informations sur l’installation d’autres composants sur cluster HDInsight lors de l’heure de création, voir :

- [Personnaliser les HDInsight à l’aide de Script Action (Linux)](hdinsight-hadoop-customize-cluster-linux.md)
- [Personnaliser les HDInsight à l’aide de Script Action (Windows)](hdinsight-hadoop-customize-cluster.md)

## <a name="use-azure-powershell"></a>Utiliser PowerShell Azure

Le code suivant PowerShell personnalise une configuration Hive :

    # hive-site.xml configuration
    $hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }
    
    $config = New-AzureRmHDInsightClusterConfig `
        | Set-AzureRmHDInsightDefaultStorage `
            -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
            -StorageAccountKey $defaultStorageAccountKey `
        | Add-AzureRmHDInsightConfigValues `
            -HiveSite $hiveConfigValues 
    
    New-AzureRmHDInsightCluster `
        -ResourceGroupName $existingResourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $clusterSizeInNodes `
        -ClusterType Hadoop `
        -OSType Windows `
        -Version "3.2" `
        -HttpCredential $httpCredential `
        -Config $config 

Vous pouvez trouver un script PowerShell du travail achevé dans [L’appendice A](#hdinsight-hadoop-customize-cluster-bootstrap.md/appx-a:-powershell-sample).

**Pour vérifier la modification :**

1. Ouverture de session sur le [portail Azure](https://portal.azure.com).
2. Dans le volet gauche, cliquez sur **Parcourir**, puis cliquez sur **Clusters HDInsight**.
3. Cliquez sur le cluster que vous venez de créer en utilisant le script PowerShell.
4. Cliquez sur **tableau de bord** à partir du haut de la cuillère pour ouvrir la Ambari UI.
5. Dans le menu de gauche, cliquez sur **la ruche** .
6. Cliquez sur **HiveServer2** de **synthèse**.
7. Cliquez sur l’onglet **configurations** .
8. Dans le menu de gauche, cliquez sur **la ruche** .
9. Cliquez sur l’onglet **Avancé** .
10. Faites défiler vers le bas, puis développez **hive-site avancée**.
11. Recherchez **hive.metastore.client.socket.timeout** dans la section.

Quelques autres exemples sur la personnalisation des autres fichiers de configuration :

    # hdfs-site.xml configuration
    $HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

    # core-site.xml configuration
    $CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

    # mapred-site.xml configuration
    $MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

    # oozie-site.xml configuration
    $OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120

Pour plus d’informations, voir blog de Azim Uddin intitulé [Création de personnalisation HDInsight Cluster](http://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx).

## <a name="use-net-sdk"></a>Utiliser le Kit de développement .NET

Voir [basé sur Linux créer des groupes dans un HDInsight à l’aide du Kit de développement .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-bootstrap).

## <a name="use-resource-manager-template"></a>Modèle d’utiliser le Gestionnaire de ressources

Vous pouvez utiliser démarrage dans le Gestionnaire de ressources modèle :

    "configurations": {
        …
        "hive-site": {
            "hive.metastore.client.connect.retry.delay": "5",
            "hive.execution.engine": "mr",
            "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
        }
    }


![hdinsight hadoop personnaliser cluster ressource azure démarrage Gestionnaire de modèle](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)



## <a name="see-also"></a>Voir aussi

- [Créer des clusters Hadoop dans HDInsight] [ hdinsight-provision-cluster] fournit des instructions sur la création d’un cluster HDInsight à l’aide d’autres options personnalisées.
- [Développer des scripts d’Action de Script pour HDInsight][hdinsight-write-script]
- [Installer et utiliser explosion sur clusters HDInsight][hdinsight-install-spark]
- [Installer et utiliser R sur clusters HDInsight][hdinsight-install-r]
- [Installer et utiliser le mode série sur LAN.r sur HDInsight clusters](hdinsight-hadoop-solr-install.md).
- [Installer et utiliser les clusters Giraph sur HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: powershell-install-configure.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Étapes de création d’un cluster"

## <a name="appx-a-powershell-sample"></a>Exemple a : AppX PowerShell

Ce script PowerShell crée un cluster HDInsight et personnalise un paramètre Hive :

    ####################################
    # Set these variables
    ####################################
    #region - used for creating Azure service names
    $nameToken = "<ENTER AN ALIAS>" 
    #endregion

    #region - cluster user accounts
    $httpUserName = "admin"  #HDInsight cluster username
    $httpPassword = "<ENTER A PASSWORD>" #"<Enter a Password>"

    $sshUserName = "sshuser" #HDInsight ssh user name
    $sshPassword = "<ENTER A PASSWORD>" #"<Enter a Password>"
    #endregion

    ####################################
    # Service names and varialbes
    ####################################
    #region - service names
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

    $resourceGroupName = $namePrefix + "rg"
    $hdinsightClusterName = $namePrefix + "hdi"
    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $hdinsightClusterName

    $location = "East US 2"
    #endregion

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    ####################################
    # Connect to Azure
    ####################################
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    #region - Create an HDInsight cluster
    ####################################
    # Create dependent components
    ####################################
    Write-Host "Creating a resource group ..." -ForegroundColor Green
    New-AzureRmResourceGroup `
        -Name  $resourceGroupName `
        -Location $location

    Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_GRS

    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageContext #use the cluster name as the container name

    ####################################
    # Create a configuration object
    ####################################
    $hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }
        
    $config = New-AzureRmHDInsightClusterConfig `
        | Set-AzureRmHDInsightDefaultStorage `
            -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
            -StorageAccountKey $defaultStorageAccountKey `
        | Add-AzureRmHDInsightConfigValues `
            -HiveSite $hiveConfigValues 

    ####################################
    # Create an HDInsight cluster
    ####################################
    $httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

    $sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
    $sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -Location $location `
        -ClusterSizeInNodes 1 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -HttpCredential $httpCredential `
        -SshCredential $sshCredential `
        -Config $config

    ####################################
    # Verify the cluster
    ####################################
    Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName

    #endregion
