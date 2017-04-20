<properties
    pageTitle="Installer des applications Hadoop sur HDInsight | Microsoft Azure"
    description="Découvrez comment installer des applications de HDInsight les applications HDInsight."
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/14/2016"
    ms.author="jgao"/>

# <a name="install-hdinsight-applications"></a>Installer des applications HDInsight

Une application HDInsight est une application que les utilisateurs peuvent installer sur un cluster HDInsight basé sur Linux. Ces applications peuvent être développées par Microsoft, éditeurs de logiciels (fil) ou par vous-même. Dans cet article, vous allez apprendre à installer une application publiée. Pour l’installation de votre propre application, voir [installer des applications personnalisées HDInsight](hdinsight-apps-install-custom-applications.md). 

Il est actuellement une application publiée :

- **Datameer**: [Datameer](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft) propose analystes un moyen interactif pour découvrir, analyser et visualiser les résultats sur Big Data. Extraire des sources de données plus facilement pour découvrir des relations et obtenir des réponses que vous avez besoin rapidement.

>[AZURE.NOTE] Datameer est actuellement pris en charge uniquement dans les clusters de version 3.2 Azure HDInsight.

Les instructions fournies dans cet article utilisent portail Azure. Vous pouvez également exporter le modèle Azure le Gestionnaire de ressources à partir du portail ou obtenir une copie du modèle de gestionnaire de ressources à partir de fournisseurs et utiliser PowerShell Azure et Azure infrastructure du langage commun pour déployer le modèle.  Voir [Hadoop basé sur Linux créer des clusters dans HDInsight à l’aide de modèles de gestionnaire de ressources](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Conditions préalables

Si vous voulez installer des applications HDInsight sur un cluster HDInsight existant, vous devez disposer un cluster HDInsight. Pour en créer un, consultez [créer clusters](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster). Vous pouvez également installer des applications de HDInsight lorsque vous créez un cluster HDInsight.

## <a name="install-applications-to-existing-clusters"></a>Installer des applications à des clusters existants

La procédure suivante vous montre comment installer des applications HDInsight à un cluster HDInsight existant.

**Pour installer une application HDInsight**

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu de gauche, cliquez sur **Clusters HDInsight** .  Si vous ne voyez pas, cliquez sur **Parcourir**, puis cliquez sur **Clusters HDInsight**.
3. Cliquez sur un cluster HDInsight.  Si vous n’en avez pas, vous devez créer un premier.  consultez [créer clusters](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster).
4. À partir de la carte de **paramètres** , cliquez sur **Applications** sous la catégorie **Général** . La carte **Installé les applications** répertorie toutes les applications installées. 

    ![menu du portail d’applications hdinsight](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)

5. Dans le menu de carte, cliquez sur **Ajouter** . 

    ![hdinsight applications installées applications](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps.png)

    Vous doit afficher la liste des applications HDInsight existantes.

    ![applications disponibles hdinsight applications](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)

6. Cliquez sur une des applications, acceptez les conditions juridiques et puis cliquez sur **Sélectionner**.

Vous pouvez consulter l’état de l’installation des notifications portails (cliquez sur l’icône en cloche dans la partie supérieure du portail). Une fois l’application est installée, l’application apparaît sur la carte applications installées.

## <a name="install-applications-during-cluster-creation"></a>Installer des applications lors de la création de cluster

Vous avez la possibilité d’installer des applications de HDInsight lorsque vous créez un cluster. Pendant le processus, HDInsight applications sont installées une fois que le cluster est créé et est en cours d’exécution. La procédure suivante vous montre comment installer des applications de HDInsight lorsque vous créez un cluster.

**Pour installer une application HDInsight**

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Nouveau**et cliquez sur **données + Analytique**, puis cliquez sur **HDInsight**.
3. Entrez le **Nom de Cluster**: ce nom doit être unique.
4. Cliquez sur l' **abonnement** pour sélectionner l’abonnement Azure qui sera utilisé pour le cluster.
5. Cliquez sur **Sélectionner cluster Type**, puis sélectionnez :

    - **Type de cluster**: Si vous ne savez pas quelle solution choisir, sélectionnez **Hadoop**. Il s’agit du type de cluster les plus populaires.
    - **Système d’exploitation**: sélectionnez **Linux**.
    - **Version**: utiliser la version par défaut si vous ne savez pas quelle solution choisir. Pour plus d’informations, voir [les versions cluster HDInsight](hdinsight-component-versioning.md).
    - **Cluster de niveau**: Azure HDInsight fournit les offres de cloud données volumineuses dans deux catégories : couches Standard et Premium. Pour plus d’informations, voir [niveaux Cluster](hdinsight-hadoop-provision-linux-clusters.md#cluster-tiers).
6. Cliquez sur **Applications**, cliquez sur une des applications publiées, puis cliquez sur **Sélectionner**.
6. Cliquez sur **informations d’identification** , puis entrez un mot de passe pour l’utilisateur d’administration. Vous devez également entrer un **Nom d’utilisateur SSH** et un **mot de passe** ou **Clé publique**, qui sera utilisé pour authentifier l’utilisateur SSH. Il est recommandé à l’aide d’une clé publique. Cliquez sur **Sélectionner** dans la partie inférieure pour enregistrer la configuration des informations d’identification.
8. Cliquez sur **Source de données**, sélectionnez une de du compte de stockage existant ou créer un nouveau compte de stockage à utiliser comme le compte de stockage par défaut pour le cluster.
9. Cliquez sur un **Groupe de ressources** pour sélectionner un groupe de ressources existant, ou cliquez sur **Nouveau** pour créer un nouveau groupe de ressources

10. Dans la carte de **Nouveau HDInsight Cluster** , vérifiez que l’option **attacher aux Startboard** est sélectionnée, puis cliquez sur **créer**. 

## <a name="list-installed-hdinsight-apps-and-properties"></a>Liste des applications HDInsight installées et propriétés

Le portail affiche une liste des applications installées HDInsight pour un cluster et les propriétés de chaque application installée.

**Application HDInsight de la liste et afficher les propriétés**

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu de gauche, cliquez sur **Clusters HDInsight** .  Si vous ne voyez pas, cliquez sur **Parcourir**, puis cliquez sur **Clusters HDInsight**.
3. Cliquez sur un cluster HDInsight.
4. À partir de la carte de **paramètres** , cliquez sur **Applications** sous la catégorie **Général** . La carte installé les applications répertorie toutes les applications installées. 

    ![hdinsight applications installées applications](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)

5. Cliquez sur l’une des applications installées pour afficher la propriété. Les listes de cartes propriété :

    - Nom de l’application : nom de l’application.
    - État : état de l’application. 
    - Page Web : L’URL de l’application web que vous avez déployée vers le nœud de bord cas échéant. Les informations d’identification sont la même que les informations d’identification utilisateur HTTP que vous avez configurée pour le cluster.
    - Point de terminaison HTTP : les informations d’identification sont le même que les informations d’identification utilisateur HTTP que vous avez configurée pour le cluster. 
    - Point de terminaison SSH : vous pouvez utiliser [SSH](hdinsight-hadoop-linux-use-ssh-unix.md) pour se connecter au nœud de bord. Les informations d’identification SSH sont les mêmes que les informations d’identification utilisateur SSH que vous avez configurée pour le cluster.

6. Pour supprimer une application, avec le bouton droit de l’application, puis cliquez sur **Supprimer** dans le menu contextuel.

## <a name="connect-to-the-edge-node"></a>Se connecter au nœud de bord

Vous pouvez vous connecter pour le nœud de bord à l’aide de HTTP et SSH. Les informations de point de terminaison sont accessibles à partir du [portail](#list-installed-hdinsight-apps-and-properties). Pour plus d’informations sur l’utilisation de SSH, voir [Utiliser SSH avec basé sur Linux Hadoop sur HDInsight de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md). 

Les informations d’identification du point de terminaison HTTP sont les informations d’identification utilisateur HTTP que vous avez configurée pour le cluster HDInsight ; les informations d’identification du point de terminaison SSH sont les informations d’identification SSH que vous avez configurée pour le cluster HDInsight.

## <a name="troubleshoot"></a>Résoudre les problèmes

Voir [résoudre les problèmes de l’installation](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>Étapes suivantes

- [Installer des applications personnalisées HDInsight](hdinsight-apps-install-custom-applications.md): Découvrez comment déployer une application HDInsight non publiée sur HDInsight.
- [Applications HDInsight publier](hdinsight-apps-publish-applications.md): Découvrez comment publier vos applications personnalisées HDInsight à Azure Marketplace.
- [MSDN : installer une application HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Découvrez comment définir des applications HDInsight.
- [Clusters HDInsight basé sur Linux personnaliser à l’aide de Script Action](hdinsight-hadoop-customize-cluster-linux.md): Découvrez comment utiliser l’Action de Script à installer des applications supplémentaires.
- [Basé sur Linux créer Hadoop clusters dans HDInsight à l’aide de modèles de gestionnaire de ressources](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Apprenez à appeler le Gestionnaire de ressources modèles pour créer des clusters HDInsight.
- [Utiliser des nœuds de bord vide dans HDInsight](hdinsight-apps-use-edge-node.md): Découvrez comment utiliser un nœud vide pour accéder à HDInsight cluster, test d’applications HDInsight et hébergement d’applications HDInsight.

