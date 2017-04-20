<properties
   pageTitle="Créer des clusters Hadoop fonctionnant sous Windows dans HDInsight à l’aide d’infrastructure du langage commun Azure"
    description="Apprenez à créer des clusters pour Azure HDInsight à l’aide de Azure infrastructure du langage commun."
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
   ms.date="09/02/2016"
   ms.author="jgao"/>

# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-azure-cli"></a>Créer des clusters Hadoop fonctionnant sous Windows dans HDInsight à l’aide d’infrastructure du langage commun Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Apprenez à créer des clusters HDInsight à l’aide d’Azure infrastructure du langage commun. Pour d’autres création d’un cluster les fonctionnalités et outils cliquez sur l’onglet, sélectionnez en haut de cette page ou consultez [méthodes de création de Cluster](hdinsight-provision-clusters.md#cluster-creation-methods).

##<a name="prerequisites"></a>Conditions requises :

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


Avant de commencer les instructions fournies dans cet article, vous devez disposer des éléments suivants :

- **Abonnement azure**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Azure infrastructure du langage commun**.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

### <a name="access-control-requirements"></a>Exigences de contrôle d’accès

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

##<a name="connect-to-azure"></a>Se connecter à Azure

Utilisez la commande suivante pour vous connecter à Azure :

    azure login

Pour plus d’informations sur l’authentification à l’aide d’un compte professionnel ou scolaire, voir [se connecter à un abonnement Azure depuis l’interface Azure](../xplat-cli-connect.md).

Pour basculer vers le mode processeur, utilisez la commande suivante :

    azure config mode arm

Pour obtenir de l’aide, utilisez le commutateur **-h** .  Par exemple :

    azure hdinsight cluster create -h

##<a name="create-clusters"></a>Créer des clusters

Vous devez disposer une gestion de ressources Azure (ARM) et un compte de stockage Blob Azure avant de pouvoir créer un cluster HDInsight. Pour créer un cluster de HDInsight, vous devez spécifier les éléments suivants :

- **Groupe de ressources Azure**: A données Lake Analytique compte doit être créé dans un groupe de ressources Azure. Le Gestionnaire de ressources Azure permet de vous permettent de travailler avec les ressources dans votre application en tant que groupe. Vous pouvez déployer, mettre à jour ou supprimer toutes les ressources pour votre application dans une seule opération coordonnée.

    Pour répertorier les groupes de ressources dans votre abonnement :

        azure group list

    Pour créer un nouveau groupe de ressources :

        azure group create -n "<Resource Group Name>" -l "<Azure Location>"

- **Nom du cluster HDInsight**

- **Emplacement**: parmi les centres de données Azure qui prend en charge clusters HDInsight. Pour obtenir la liste des emplacements pris en charge, voir [HDInsight tarifs](https://azure.microsoft.com/pricing/details/hdinsight/).

- **Compte de stockage par défaut**: HDInsight utilise un conteneur de stockage Blob Azure en tant que le système de fichiers par défaut. Un compte de stockage Azure est requis avant de pouvoir créer un cluster HDInsight.

    Pour créer un nouveau compte de stockage Azure :

        azure storage account create "<Azure Storage Account Name>" -g "<Resource Group Name>" -l "<Azure Location>" --type LRS

    > [AZURE.NOTE]Le compte de stockage doit être colocalisé avec HDInsight dans le centre de données.
    > Le type de compte de stockage ne peut pas être ZRS, car ZRS ne prend pas en charge table.

    Pour plus d’informations sur la création d’un compte de stockage Azure à l’aide du portail Azure, voir [créer], gérer ou supprimer un compte de stockage [azure-créer-storageaccount].

    Si vous possédez un compte de stockage déjà mais ne connaissez pas le nom du compte et la clé de compte, vous pouvez utiliser les commandes suivantes pour récupérer les informations :

        -- Lists Storage accounts
        azure storage account list
        -- Shows a Storage account
        azure storage account show "<Storage Account Name>"
        -- Lists the keys for a Storage account
        azure storage account keys list "<Storage Account Name>" -g "<Resource Group Name>"

    Pour plus d’informations sur l’obtention des informations à l’aide du portail Azure, consultez la section « Gérer votre compte de stockage » de [comptes de stockage sur Azure](../storage/storage-create-storage-account#manage-your-storage-account).

- **Conteneur Blob par défaut (facultatif)**: la commande **cluster azure hdinsight create** crée le conteneur s’il n’existe pas. Si vous choisissez créer le conteneur au préalable, vous pouvez utiliser la commande suivante :

    conteneur de stockage Azure créer : nom de compte «<Storage Account Name>»--clé de compte <Storage Account Key> [ContainerName]

Une fois que le compte de stockage préparé, vous êtes prêt à créer un cluster :


    azure hdinsight cluster create -g <Resource Group Name> -c <HDInsight Cluster Name> -l <Location> --osType <Windows | Linux> --version <Cluster Version> --clusterType <Hadoop | HBase | Spark | Storm> --workerNodeCount 2 --headNodeSize Large --workerNodeSize Large --defaultStorageAccountName <Azure Storage Account Name>.blob.core.windows.net --defaultStorageAccountKey "<Default Storage Account Key>" --defaultStorageContainer <Default Blob Storage Container> --userName admin --password "<HTTP User Password>" --rdpUserName <RDP Username> --rdpPassword "<RDP User Password" --rdpAccessExpiry "03/01/2016"


##<a name="create-clusters-using-configuration-files"></a>Créer des clusters à l’aide de fichiers de configuration
En règle générale, vous créez un cluster de HDInsight, exécutez de tâches sur l’et puis supprimez le cluster pour réduire le coût. L’interface de ligne de commande vous donne la possibilité d’enregistrer les configurations vers un fichier de sorte que vous pouvez la réutiliser chaque fois que vous créez un cluster.  

    azure hdinsight config create [options ] <Config File Path> <overwirte>
    azure hdinsight config add-config-values [options] <Config File Path>
    azure hdinsight config add-script-action [options] <Config File Path>

Exemple : Créer un fichier de configuration qui contient une action à exécuter lors de la création d’un cluster de script.

    azure hdinsight config create "C:\myFiles\configFile.config"
    azure hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <Script Action URI> --name myScriptAction --parameters "-param value"
    azure hdinsight cluster create --configurationPath "C:\myFiles\configFile.config"

##<a name="create-clusters-with-script-action"></a>Créer des clusters avec l’action de script

Créer un fichier de configuration qui contient une action à exécuter lors de la création d’un cluster de script.

    azure hdinsight config create "C:\myFiles\configFile.config"
    azure hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <scriptActionURI> --name myScriptAction --parameters "-param value"

Créer un cluster avec une action de script

    azure hdinsight cluster create -g myarmgroup01 -l westus -y Windows --clusterType Hadoop --version 3.2 --defaultStorageAccountName mystorageaccount --defaultStorageAccountKey <defaultStorageAccountKey> --defaultStorageContainer mycontainer --userName admin --password <clusterPassword> --sshUserName sshuser --sshPassword <sshPassword> --workerNodeCount 1 –configurationPath "C:\myFiles\configFile.config" myNewCluster01


Pour des informations sur l’action script général, voir [clusters HDInsight personnaliser à l’aide de Script Action (Linux)](hdinsight-hadoop-customize-cluster.md).


## <a name="create-clusters-using-arm-templates"></a>Créer des clusters à l’aide de modèles de processeur

Vous pouvez utiliser l’infrastructure du langage commun pour créer des clusters en appelant modèles processeur. Voir [déployer avec Azure infrastructure du langage commun](hdinsight-hadoop-create-windows-clusters-arm-templates.md#deploy-with-azure-cli).

## <a name="see-also"></a>Voir aussi

- [Prise en main Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) - Apprenez à commencer à travailler avec votre cluster HDInsight
- [Hadoop soumettre des travaux par programme](hdinsight-submit-hadoop-jobs-programmatically.md) - Apprenez à soumettre des tâches à HDInsight par programme
- [Gérer les groupes de Hadoop dans un HDInsight à l’aide de l’infrastructure du langage commun Azure](hdinsight-administer-use-command-line.md)
- [À l’aide de l’infrastructure du langage commun Azure pour Mac, Linux et Windows avec la gestion des services Azure](../virtual-machines-command-line-tools.md)
