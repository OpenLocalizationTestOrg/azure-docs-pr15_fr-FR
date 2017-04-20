<properties
    pageTitle="Gérer les groupes de Hadoop dans un HDInsight avec PowerShell | Microsoft Azure"
    description="Découvrez comment effectuer des tâches d’administration pour les groupes Hadoop dans HDInsight à l’aide de PowerShell Azure."
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    tags="azure-portal"
    authors="mumian"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="jgao"/>

# <a name="manage-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Gérer les groupes de Hadoop dans un HDInsight à l’aide de PowerShell Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell est un environnement de script puissant que vous pouvez utiliser pour contrôler et d’automatiser le déploiement et la gestion de vos charges de travail dans Azure. Dans cet article, vous allez apprendre à gérer les groupes de Hadoop dans Azure HDInsight en utilisant une console PowerShell Azure locale à l’aide de Windows PowerShell. Pour la liste des applets de commande HDInsight PowerShell, voir [informations de référence applet de commande HDInsight][hdinsight-powershell-reference].



**Conditions préalables**

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##<a name="install-azure-powershell"></a>Installer PowerShell Azure

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

Si vous avez installé Azure PowerShell version 0,9 x, vous devez le désinstaller avant d’installer une version plus récente.

Pour vérifier la version de la session PowerShell installée :

    Get-Module *azure*
    
Pour désinstaller la version antérieure, exécutez programmes et fonctionnalités dans le panneau de configuration. 


##<a name="create-clusters"></a>Créer des clusters

Voir [basé sur Linux créer des groupes dans un HDInsight à l’aide de PowerShell Azure](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

##<a name="list-clusters"></a>Liste clusters
La commande suivante permet de répertorier tous les groupes dans un l’abonnement actif :

    Get-AzureRmHDInsightCluster

##<a name="show-cluster"></a>Afficher le cluster

Utiliser la commande suivante pour afficher les détails d’un cluster spécifique dans l’abonnement actif :

    Get-AzureRmHDInsightCluster -ClusterName <Cluster Name>

##<a name="delete-clusters"></a>Supprimer les clusters

Utilisez la commande suivante pour supprimer un cluster :

    Remove-AzureRmHDInsightCluster -ClusterName <Cluster Name>

Vous pouvez également supprimer un cluster en supprimant le groupe de ressources qui contient le cluster. Veuillez noter, cette opération va supprimer toutes les ressources dans le groupe, y compris le compte de stockage par défaut.

    Remove-AzureRmResourceGroup -Name <Resource Group Name>
            
##<a name="scale-clusters"></a>Enverguez
Le cluster mise à l’échelle fonctionnalité permet de modifier le nombre de nœuds de travail utilisés par un cluster qui s’exécute dans Azure HDInsight sans avoir à recréer le cluster.

>[AZURE.NOTE] Clusters uniquement avec HDInsight version 3.1.3 ou version ultérieure sont prises en charge. Si vous n’êtes pas sûr de la version de votre cluster, vous pouvez vérifier la page Propriétés.  Voir la [liste et afficher clusters](hdinsight-administer-use-portal-linux.md#list-and-show-clusters).

L’impact de la modification du nombre de nœuds de données pour chaque type de cluster pris en charge par HDInsight :

- Hadoop

    Vous pouvez en toute transparence augmenter le nombre de nœuds de travail dans un cluster Hadoop qui s’exécute sans ayant un impact sur toutes les tâches en attente ou en cours d’exécution. Nouvelles tâches peuvent également être soumises lorsque l’opération est en cours. Échecs dans une opération de mise à l’échelle sont gérées correctement afin que le cluster est toujours vers la gauche dans un état de fonctionnalité.

    Lorsqu’un cluster Hadoop est mise à l’échelle en réduisant le nombre de nœuds de données vers le bas, certains des services dans le cluster redémarrage. Ainsi, en cours d’exécution et les tâches en attente échec à la fin de l’opération de mise à l’échelle. Vous pouvez, cependant, renvoyez les tâches une fois l’opération terminée.

- HBase

    Vous pouvez en toute transparence ajouter ou supprimer des nœuds à votre cluster HBase en cours d’exécution. Les serveurs régionaux sont réparties automatiquement après quelques minutes de fin de l’opération de mise à l’échelle. Toutefois, vous pouvez également manuellement équilibrer les serveurs régionaux en se connectant à la headnode de cluster et en exécutant les commandes suivantes dans une fenêtre d’invite de commandes :

        >pushd %HBASE_HOME%\bin
        >hbase shell
        >balancer

- Vague d'

    Vous pouvez en toute transparence ajouter ou supprimer des nœuds de données à votre cluster vague en cours d’exécution. Mais après la réussite de l’opération de mise à l’échelle, vous devrez rééquilibrer la topologie.

    Il est possible de rééquilibrage de deux façons :

    * Interface utilisateur du web vague
    * Outil de l’interface de ligne (commande)

    Reportez-vous à la [documentation de vague d’Apache](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) pour plus d’informations.

    L’interface utilisateur du web vague est disponible sur le cluster HDInsight :

    ![Rééquilibrage d’échelle HDInsight vague](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

    Voici un exemple de l’utilisation de la commande de l’infrastructure du langage commun rééquilibrer la topologie vague :

        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors

        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

Pour modifier la taille de cluster Hadoop à l’aide de PowerShell Azure, exécutez la commande suivante à partir d’un ordinateur client :

    Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
    

##<a name="grantrevoke-access"></a>Accès grant/revoke

HDInsight clusters comprennent les services web HTTP suivants (tous ces services ont des extrémités RESTful) :

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


Par défaut, ces services sont accordées pour l’accès. Vous pouvez révoquer/accorder l’accès. Pour révoquer :

    Revoke-AzureRmHDInsightHttpServicesAccess -ClusterName <Cluster Name>

Pour accorder :

    $clusterName = "<HDInsight Cluster Name>"

    # Credential option 1
    $hadoopUserName = "admin"
    $hadoopUserPassword = "<Enter the Password>"
    $hadoopUserPW = ConvertTo-SecureString -String $hadoopUserPassword -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$hadoopUserPW)

    # Credential option 2
    #$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"
    
    Grant-AzureRmHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential

>[AZURE.NOTE] Par l’octroi/révoquer l’accès, vous allez réinitialiser le cluster nom utilisateur et mot de passe.

Il est également possible via le portail. Voir [Administrer HDInsight à l’aide du portail Azure][hdinsight-admin-portal].

##<a name="update-http-user-credentials"></a>Mettre à jour les informations d’identification utilisateur HTTP

Il s’agit de la même procédure que [Grant/revoke HTTP access](#grant/revoke-access). Si le cluster a été accordé l’accès par HTTP, vous devez tout d’abord annuler il.  Puis accorder le niveau d’accès avec les nouvelles informations d’identification utilisateur HTTP.


##<a name="find-the-default-storage-account"></a>Trouver le compte de stockage par défaut

Le script Powershell suivant montre comment obtenir le nom de compte de stockage par défaut et la clé de compte de stockage par défaut pour un cluster.

    $clusterName = "<HDInsight Cluster Name>"
    
    $cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $cluster.ResourceGroup
    $defaultStorageAccountName = ($cluster.DefaultStorageAccount).Replace(".blob.core.windows.net", "")
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey 

##<a name="find-the-resource-group"></a>Recherchez le groupe de ressources

Dans le mode directeur des ressources, chaque cluster HDInsight appartient à un groupe de ressources Azure.  Pour trouver le groupe de ressources :

    $clusterName = "<HDInsight Cluster Name>"
    
    $cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $cluster.ResourceGroup


##<a name="submit-jobs"></a>Soumettre des tâches

**Soumettre des travaux MapReduce**

Consultez [des exemples d’exécuter Hadoop MapReduce dans HDInsight fonctionnant sous Windows](hdinsight-run-samples.md).

**Soumettre des travaux Hive** 

Voir [la ruche exécuter des requêtes à l’aide de PowerShell](hdinsight-hadoop-use-hive-powershell.md).

**Soumettre des travaux cochon**

Voir [cochon exécuter des travaux à l’aide de PowerShell](hdinsight-hadoop-use-pig-powershell.md).

**Soumettre des travaux Sqoop**

Voir [utiliser Sqoop avec HDInsight](hdinsight-use-sqoop.md).

**Soumettre des travaux Oozie**

Voir [Utiliser Oozie avec Hadoop pour définir et exécuter un flux de travail dans HDInsight](hdinsight-use-oozie.md).

##<a name="upload-data-to-azure-blob-storage"></a>Télécharger des données à Azure Blob storage
Voir [télécharger des données à HDInsight][hdinsight-upload-data].


## <a name="see-also"></a>Voir aussi
* [Documentation de référence HDInsight applet de commande][hdinsight-powershell-reference]
* [Administrer HDInsight à l’aide du portail Azure][hdinsight-admin-portal]
* [Administrer HDInsight à l’aide d’une interface de ligne de commande][hdinsight-admin-cli]
* [Créer des clusters HDInsight][hdinsight-provision]
* [Télécharger des données à HDInsight][hdinsight-upload-data]
* [Soumettre des travaux d’Hadoop par programme][hdinsight-submit-jobs]
* [Prise en main Azure HDInsight][hdinsight-get-started]


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-provision-custom-options]: hdinsight-provision-clusters.md#configuration
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: powershell-install-configure.md

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png
