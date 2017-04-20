<properties
    pageTitle="Créer des clusters Hadoop, HBase, vague d’ou explosion sur Linux dans HDInsight à l’aide de PowerShell Azure | Microsoft Azure"
    description="Apprenez à créer des clusters Hadoop, HBase, vague d’ou explosion sur Linux pour HDInsight à l’aide de PowerShell Azure."
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="10/05/2016"
    ms.author="nitinme"/>

# <a name="create-linux-based-clusters-in-hdinsight-by-using-azure-powershell"></a>Créer des clusters basés sur Linux dans HDInsight à l’aide de PowerShell Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Azure PowerShell est un environnement de script puissant que vous pouvez utiliser pour contrôler et d’automatiser le déploiement et la gestion de vos charges de travail dans Microsoft Azure. Ce document fournit des informations sur la création d’un cluster basé sur Linux HDInsight à l’aide de PowerShell Azure. Il inclut également un exemple de script.

> [AZURE.NOTE] PowerShell Azure n’est disponible sur les clients Windows. Si vous utilisez un client Linux, Unix ou Mac OS X, voir [créer un cluster basé sur Linux HDInsight à l’aide de Azure infrastructure du langage commun](hdinsight-hadoop-create-linux-clusters-azure-cli.md) pour plus d’informations sur l’utilisation de l’infrastructure du langage commun Azure pour créer un cluster.

## <a name="prerequisites"></a>Conditions préalables
Vous devez disposer des éléments suivants avant de démarrer cette procédure :

- Un abonnement Azure. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- PowerShell Azure.
    Pour plus d’informations sur l’utilisation de PowerShell Azure avec HDInsight, voir [administrer HDInsight à l’aide de PowerShell](hdinsight-administer-use-powershell.md). Pour la liste des applets de commande HDInsight Windows PowerShell, voir [informations de référence applet de commande HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

### <a name="access-control-requirements"></a>Exigences de contrôle d’accès

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>Créer des clusters

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Pour créer un cluster HDInsight à l’aide de PowerShell Azure, vous devez effectuer les procédures suivantes :

- Créer un groupe de ressources Azure
- Créer un compte de stockage Azure
- Créer un conteneur Blob Azure
- Créer un cluster HDInsight

Les deux paramètres les plus importantes que vous devez définir pour créer des clusters Linux sont ceux qui spécifient le type de système d’exploitation et les détails de l’utilisateur SSH :

- Vérifiez que vous spécifiez le paramètre **- OSType** **Linux**.
- Pour utiliser le protocole SSH pour les sessions à distance sur les groupes, vous pouvez spécifier le mot de passe utilisateur SSH ou la clé publique SSH. Si vous spécifiez le mot de passe utilisateur SSH et la clé publique SSH, la touche sont ignorée. Si vous souhaitez utiliser le code SSH pour les sessions à distance, vous devez spécifier un mot de passe SSH vide lorsque vous y êtes invité pour un. Pour plus d’informations sur l’utilisation de SSH avec HDInsight, voir un des articles suivants :

    * [Utiliser le protocole SSH avec basé sur Linux Hadoop sur HDInsight de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Utiliser le protocole SSH avec basé sur Linux Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

Le script suivant montre comment créer un nouveau cluster :

    $token ="<SpecifyAnUniqueString>"

    $resourceGroupName = $token + "rg"      # Provide a Resource Group name
    $clusterName = $token
    $defaultStorageAccountName = $token + "store"   # Provide a Storage account name
    $defaultStorageContainerName = $token + "container"
    $location = "East US 2"     # Change the location if needed
    $clusterNodes = 1           # The number of nodes in the HDInsight cluster

    # Sign in to Azure
    Login-AzureRmAccount

    # Select the subscription to use if you have multiple subscriptions
    #$subscriptionID = "<SubscriptionName>"        # Provide your Subscription Name
    #Select-AzureRmSubscription -SubscriptionId $subscriptionID

    # Create an Azure Resource Group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create an Azure Storage account and container used as the default storage
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -StorageAccountName $defaultStorageAccountName `
        -Location $location `
        -Type Standard_LRS
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -Name $defaultStorageAccountName -ResourceGroupName $resourceGroupName)[0].Key1
    $destContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer -Name $defaultStorageContainerName -Context $destContext

    # Create an HDInsight cluster
    $credentials = Get-Credential -Message "Enter Cluster user credentials" -UserName "admin"
    $sshCredentials = Get-Credential -Message "Enter SSH user credentials"

    # The location of the HDInsight cluster must be in the same data center as the Storage account.
    $location = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $defaultStorageAccountName | %{$_.Location}

    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -HttpCredential $credentials `
        -Location $location `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultStorageContainerName  `
        -ClusterSizeInNodes $clusterNodes `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.4" `
        -SshCredential $sshCredentials

Les valeurs que vous spécifiez pour **$clusterCredentials** sont utilisées pour créer le compte d’utilisateur Hadoop pour le cluster. Utiliser ce compte pour vous connecter au cluster.

Les valeurs que vous spécifiez pour **$sshCredentials** sont utilisées pour créer l’utilisateur SSH pour le cluster. Utilisez ce compte pour démarrer une session distante SSH sur le cluster et exécuter des tâches.

> [AZURE.IMPORTANT] Dans ce script, vous devez spécifier le nombre de nœuds de travail qui sera dans le cluster. Si vous prévoyez d’utiliser plus de 32 nœuds de travail (soit à la création d’un cluster ou en redimensionnant le cluster après la création de), vous devez également spécifier une taille de nœud de tête au moins 8 cœurs et 14 Go de RAM.
>
> Pour plus d’informations sur les tailles de nœud et les coûts associés, voir [HDInsight tarifs](https://azure.microsoft.com/pricing/details/hdinsight/).

Il peut prendre jusqu'à 20 minutes pour créer un cluster.

L’exemple suivant montre comment ajouter un compte de stockage supplémentaire :

    # Create another storage account used as additional storage account
    $additionalStorageAccountName = $token + "store2"
    New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $additionalStorageAccountName -Location $location -Type Standard_LRS
    $additionalStorageAccountKey = (Get-AzureRmStorageAccountKey -Name $additionalStorageAccountName -ResourceGroupName $resourceGroupName)[0].Value

    $config = New-AzureRmHDInsightClusterConfig
    Add-AzureRmHDInsightStorage -Config $config -StorageAccountName "$additionalStorageAccountName.blob.core.windows.net" -StorageAccountKey $additionalStorageAccountKey

    # Create a new HDInsight cluster
    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -HttpCredential $credentials `
        -Location $location `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultStorageContainerName  `
        -ClusterSizeInNodes $clusterNodes `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.4" `
        -SshCredential $sshCredentials `
        -Config $config

## <a name="customize-clusters"></a>Personnaliser les clusters

- Voir [clusters HDInsight personnaliser à l’aide de démarrage](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
- Voir [clusters HDInsight fonctionnant sous Windows personnaliser à l’aide de Script Action](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).

## <a name="delete-the-cluster"></a>Supprimer le cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez créé un cluster de HDInsight, utilisez les ressources suivantes pour apprendre à utiliser avec votre cluster.

### <a name="hadoop-clusters"></a>Hadoop clusters

* [Utiliser Hive avec HDInsight](hdinsight-use-hive.md)
* [Utiliser cochon avec HDInsight](hdinsight-use-pig.md)
* [Utiliser MapReduce avec HDInsight](hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Clusters HBase

* [Prise en main HBase sur HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Développement d’applications Java HBase sur HDInsight](hdinsight-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Clusters vague

* [Développer des topologies Java pour vague d’HDInsight](hdinsight-storm-develop-java-topology.md)
* [Utiliser des composants Python dans vague d’HDInsight](hdinsight-storm-develop-python-topology.md)
* [Déployer et surveiller topologies avec vague d’HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

### <a name="spark-clusters"></a>Clusters d’explosion

* [Créer une application autonome à l’aide de Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Exécution de tâches à distance sur un cluster explosion à l’aide de Livy](hdinsight-apache-spark-livy-rest-interface.md)
* [Explosion avec BI : effectuer une analyse de données interactives à l’aide d’explosion dans HDInsight avec les outils de décisionnel](hdinsight-apache-spark-use-bi-tools.md)
* [Explosion avec apprentissage automatique : utilisation explosion dans HDInsight pour prévoir des résultats de l’inspection alimentaires](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Diffusion en continu explosion : Utilisation explosion dans HDInsight pour la création d’applications en continu en temps réel](hdinsight-apache-spark-eventhub-streaming.md)
