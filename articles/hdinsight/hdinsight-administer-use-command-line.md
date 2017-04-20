<properties
    pageTitle="Gérer les Hadoop à l’aide d’Azure infrastructure du langage commun | Microsoft Azure"
    description="Comment utiliser l’infrastructure du langage commun Azure pour gérer les groupes de Hadoop dans un HDIsight"
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    authors="mumian"
    tags="azure-portal"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="jgao"/>

# <a name="manage-hadoop-clusters-in-hdinsight-using-the-azure-cli"></a>Gérer les groupes de Hadoop dans un HDInsight à l’aide de l’infrastructure du langage commun Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Découvrez comment utiliser l' [Interface de ligne Azure](../xplat-cli-install.md) pour gérer les groupes de Hadoop dans Azure HDInsight. L’infrastructure du langage commun Azure est activée dans Node.js. Il peut être utilisé dans n’importe quelle plate-forme qui prend en charge Node.js, y compris Windows, Mac et Linux.

Cet article traite uniquement à l’aide de l’infrastructure du langage commun Azure avec HDInsight. Pour un guide général sur l’utilisation Azure infrastructure du langage commun, voir [installer et configurer Azure infrastructure du langage commun][azure-command-line-tools].

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

##<a name="prerequisites"></a>Conditions préalables

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Azure infrastructure du langage commun** - voir [installer et configurer l’infrastructure du langage commun Azure](../xplat-cli-install.md) pour plus d’informations d’installation et de configuration.
- **Se connecter à Azure**, en utilisant la commande suivante :

        azure login

    Pour plus d’informations sur l’authentification à l’aide d’un compte professionnel ou scolaire, voir [se connecter à un abonnement Azure depuis l’interface Azure](xplat-cli-connect.md).
    
- **Basculer vers le mode directeur des ressources Azure**, en utilisant la commande suivante :

        azure config mode arm

Pour obtenir de l’aide, utilisez le commutateur **-h** .  Par exemple :

    azure hdinsight cluster create -h
    
##<a name="create-clusters"></a>Créer des clusters

Voir [basé sur Linux créer des groupes dans un HDInsight à l’aide de l’infrastructure du langage commun Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md).

##<a name="list-and-show-cluster-details"></a>Liste et afficher les détails de cluster
Utilisez les commandes suivantes pour répertorier et afficher les détails de cluster :

    azure hdinsight cluster list
    azure hdinsight cluster show <Cluster Name>

![HDI. CLIListCluster][image-cli-clusterlisting]


##<a name="delete-clusters"></a>Supprimer les clusters

Utilisez la commande suivante pour supprimer un cluster :

    azure hdinsight cluster delete <Cluster Name>

Vous pouvez également supprimer un cluster en supprimant le groupe de ressources qui contient le cluster. Veuillez noter, cette opération va supprimer toutes les ressources dans le groupe, y compris le compte de stockage par défaut.

    azure group delete <Resource Group Name>

##<a name="scale-clusters"></a>Enverguez

Pour modifier la taille de cluster Hadoop :

    azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>


## <a name="enabledisable-http-access-for-a-cluster"></a>Activer/désactiver l’accès HTTP pour un cluster

    azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
    azure hdinsight cluster disable-http-access [options] <Cluster Name>

## <a name="enabledisable-rdp-access-for-a-cluster"></a>Activer/désactiver access RDP pour un cluster

    azure hdinsight cluster enable-rdp-access [options] <Cluster Name> <rdpUserName> <rdpPassword> <rdpExpiryDate>
    azure hdinsight cluster disable-rdp-access [options] <Cluster Name>


##<a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à effectuer différentes tâches administratives cluster HDInsight. Pour plus d’informations, consultez les articles suivants :

* [Administrer HDInsight à l’aide du portail Azure] [hdinsight-admin-portal]
* [Administrer HDInsight à l’aide de PowerShell Azure] [hdinsight-admin-powershell]
* [Prise en main Azure HDInsight] [hdinsight-get-started]
* [L’utilisation de l’infrastructure du langage commun Azure] [azure-command-line-tools]


[azure-command-line-tools]: ../xplat-cli-install.md
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "Liste et afficher les clusters"
