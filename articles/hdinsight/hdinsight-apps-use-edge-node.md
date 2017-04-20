<properties
    pageTitle="Utiliser des nœuds de bord vide dans HDInsight | Microsoft Azure"
    description="Comment faire pour ajouter un nœud de bord ampty à HDInsight cluster pouvant être utilisées comme un client et test/hôte vos applications HDInsight."
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
    ms.date="09/14/2016"
    ms.author="jgao"/>

# <a name="use-empty-edge-nodes-in-hdinsight"></a>Utiliser des nœuds de bord vide dans HDInsight

Découvrez comment ajouter un nœud vide bord vers un cluster basé sur Linux HDInsight. Un nœud bord vide est une machine virtuelle Linux avec les outils clients même installé et configuré comme dans les headnodes, mais sans aucun service hadoop en cours d’exécution. Vous pouvez utiliser le nœud de bord pour accéder au cluster, test de vos applications clientes et l’hébergement de vos applications clientes. 

Vous pouvez ajouter un nœud vide à un cluster HDInsight existant, à un nouveau cluster lorsque vous créez le cluster. Ajout d’un nœud vide s’effectue à l’aide du Gestionnaire de ressources Azure modèle.  L’exemple suivant montre comment procéder à l’aide d’un modèle :

    "resources": [
        {
            "name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
            "type": "Microsoft.HDInsight/clusters/applications",
            "apiVersion": "[variables('clusterApiVersion')]",
            "properties": {
                "marketPlaceIdentifier": "EmptyNode",
                "computeProfile": {
                    "roles": [{
                        "name": "edgenode",
                        "targetInstanceCount": 1,
                        "hardwareProfile": {
                            "vmSize": "[parameters('edgeNodeSize')]"
                        }
                    }]
                },
                "installScriptActions": [{
                    "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                    "uri": "https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh",
                    "roles": ["edgenode"]
                }],
                "uninstallScriptActions": [],
                "httpsEndpoints": [],
                "applicationType": "CustomApplication"
            }
        }
    ],

Comme indiqué dans l’échantillon, vous pouvez éventuellement appeler une [action de script](hdinsight-hadoop-customize-cluster-linux.md) pour effectuer une configuration supplémentaire, telles que l’installation [d’Apache teinte](hdinsight-hadoop-hue-linux.md) dans le nœud de bord.

Après avoir créé un nœud de bord, vous pouvez vous connecter au nœud de bord à l’aide de SSH et exécuter les outils clients pour accéder au cluster Hadoop dans HDInsight.

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Ajouter un nœud à un cluster existant

Dans cette section, vous utilisez un modèle de gestionnaire de ressources pour ajouter un nœud de bord à un cluster HDInsight existant.  Le modèle de gestionnaire de ressources sont accessibles dans [GitHub](https://github.com/hdinsight/Iaas-Applications/tree/master/EmptyNode). Le modèle de gestionnaire de ressources appelle un script d’action script situé à https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh. Le script n’effectue les actions.  Il s’agit pour illustrer appelant action de script à partir d’un modèle de gestionnaire de ressources.

**Pour ajouter un nœud bord vide à un cluster existant**

1. Créer un cluster HDInsight si vous n’en avez pas encore.  Voir [Hadoop didacticiel : commencer à utiliser Hadoop basé sur Linux dans HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Cliquez sur l’image suivante pour se connecter à Azure et ouvrez le modèle Azure le Gestionnaire de ressources dans le portail Azure. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FEmptyNode%2Fazuredeploy.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

3. Configurer les propriétés suivantes :

    - NOMDUCLUSTER : Entrez le nom d’un cluster HDInsight existant.
    - EDGENODESIZE : Sélectionnez une des tailles de machine virtuelle.
    - EDGENODEPREFIX : La valeur par défaut est les **Nouveautés**.  À l’aide de la valeur par défaut, le nom du nœud bord est **Nouveau edgenode**.  Vous pouvez personnaliser le préfixe à partir du portail. Vous pouvez également personnaliser le nom complet à partir du modèle.


4. Cliquez sur **OK** pour enregistrer les modifications.
5. Dans le **groupe de ressources**, sélectionnez un groupe de ressources.
6. Cliquez sur **les conditions juridiques révision**, puis cliquez sur **acheter**.
7. Sélectionnez **épingler au tableau de bord**, puis cliquez sur **créer**.

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Ajouter un nœud bord lors de la création d’un cluster

Dans cette section, vous utilisez un modèle de gestionnaire de ressources pour créer un cluster HDInsight avec un nœud de bord.  Le modèle de gestionnaire de ressources sont accessibles dans un [conteneur de stockage Blob Azure](http://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight-with-edge-node.json)public. Le modèle de gestionnaire de ressources appelle un script d’action script situé à https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh. Le script n’effectue les actions.  Il s’agit pour illustrer appelant action de script à partir d’un modèle de gestionnaire de ressources.

**Pour ajouter un nœud bord vide à un cluster existant**

1. Créer un cluster HDInsight si vous n’en avez pas encore.  Voir [Hadoop didacticiel : commencer à utiliser Hadoop basé sur Linux dans HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Cliquez sur l’image suivante pour se connecter à Azure et ouvrez le modèle Azure le Gestionnaire de ressources dans le portail Azure. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hadoop-cluster-in-hdinsight-with-edge-node.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

3. Configurer les propriétés suivantes :
        
    - NOMDUCLUSTER : Entrez un nom pour le nouveau cluster à créer.
    - CLUSTERLOGINUSERNAME : Entrez le nom d’utilisateur HTTP Hadoop.  Le nom par défaut est **admin**.
    - CLUSTERLOGINPASSWORD : Entrez le mot de passe utilisateur Hadoop HTTP.
    - SSHUSERNAME : Entrez le nom d’utilisateur SSH. Le nom par défaut est **sshuser**.
    - SSHPASSWORD : Entrez le mot de passe utilisateur SSH.
    - EMPLACEMENT : Entrez l’emplacement du nom du groupe de ressources, le cluster et le compte de stockage par défaut.
    - CLUSTERTYPE : La valeur par défaut est **hadoop**.
    - CLUSTERWORKERNODECOUNT : La valeur par défaut est 2.
    - EDGENODESIZE : Sélectionnez une des tailles de machine virtuelle.
    - EDGENODEPREFIX : La valeur par défaut est les **Nouveautés**.  À l’aide de la valeur par défaut, le nom du nœud bord est **Nouveau edgenode**.  Vous pouvez personnaliser le préfixe à partir du portail. Vous pouvez également personnaliser le nom complet à partir du modèle.

4. Cliquez sur **OK** pour enregistrer les modifications.
5. Dans le **groupe de ressources**, entrez un nouveau nom de groupe de ressources.
6. Cliquez sur **les conditions juridiques révision**, puis cliquez sur **acheter**.
7. Sélectionnez **épingler au tableau de bord**, puis cliquez sur **créer**. 


## <a name="access-an-edge-node"></a>Accéder à un nœud de bord

Le nœud de bord ssh point de terminaison est <EdgeNodeName>. <ClusterName>-ssh.azurehdinsight.net:22.  Par exemple, nouvelle-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

Le nœud bord apparaît sous la forme d’une application sur le portail Azure.  Le portail fournit les informations pour accéder au nœud de bord à l’aide de SSH.

**Pour vérifier le point de terminaison de bord nœud SSH**

1. Ouverture de session sur le [portail Azure](https://portal.azure.com).
2. Ouvrez le cluster HDInsight avec un nœud de bord.
3. Cliquez sur **Applications** à partir de la carte cluster. Vous doit afficher le nœud de bord.  Le nom par défaut est **Nouveau edgenode**.
4. Cliquez sur le nœud de bord. Vous doit afficher le point de terminaison SSH.

**Utiliser Hive sur le nœud de bord**

5. Utiliser le protocole SSH pour vous connecter au nœud de bord.  Voir [utiliser SSH avec basé sur Linux Hadoop sur HDInsight de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md) ou [utiliser le protocole SSH avec basé sur Linux Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md).
6. Une fois que vous avez connectés au nœud de bord à l’aide de SSH, utilisez la commande suivante pour ouvrir la console Hive :

        hive
7. Exécutez la commande suivante pour afficher les tables Hive dans le cluster :

        show tables;

## <a name="delete-an-edge-node"></a>Supprimer un nœud de bord

Vous pouvez supprimer un nœud de bord à partir du portail Azure.

**Pour accéder à un nœud de bord**

1. Ouverture de session sur le [portail Azure](https://portal.azure.com).
2. Ouvrez le cluster HDInsight avec un nœud de bord.
3. Cliquez sur **Applications** à partir de la carte cluster. Vous doit afficher la liste des nœuds de bord.  
4. Cliquez sur le nœud de bord que vous voulez supprimer, puis cliquez sur **Supprimer**.
5. Cliquez sur **Oui** pour confirmer.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris comment ajouter un nœud de bord et comment accéder au nœud de bord. Pour plus d’informations, consultez les articles suivants :

- [HDInsight installer les applications](hdinsight-apps-install-applications.md): Découvrez comment installer une application HDInsight à vos clusters.
- [Installer des applications personnalisées HDInsight](hdinsight-apps-install-custom-applications.md): Découvrez comment déployer une application HDInsight publication a été retirée à HDInsight.
- [Applications HDInsight publier](hdinsight-apps-publish-applications.md): Découvrez comment publier vos applications personnalisées HDInsight à Azure Marketplace.
- [MSDN : installer une application HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Découvrez comment définir des applications HDInsight.
- [Clusters HDInsight basé sur Linux personnaliser à l’aide de Script Action](hdinsight-hadoop-customize-cluster-linux.md): Découvrez comment utiliser l’Action de Script à installer des applications supplémentaires.
- [Basé sur Linux créer Hadoop clusters dans HDInsight à l’aide de modèles de gestionnaire de ressources](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Apprenez à appeler le Gestionnaire de ressources modèles pour créer des clusters HDInsight.

