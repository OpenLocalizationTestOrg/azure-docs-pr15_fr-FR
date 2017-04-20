<properties
    pageTitle="Créer des clusters Hadoop, HBase ou vague de sur Linux dans HDInsight à l’aide de l’infrastructure du langage commun Azure disponibilité sur plusieurs plateformes | Microsoft Azure"
    description="Apprenez à créer des clusters basés sur Linux HDInsight à l’aide de l’infrastructure du langage commun Azure disponibilité sur plusieurs plateformes et l’API REST Azure modèles Azure le Gestionnaire de ressources. Vous pouvez spécifier le type de cluster (Hadoop, HBase ou vague de), ou utiliser des scripts pour installer des composants personnalisés..."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/20/2016"
    ms.author="larryfr"/>

#<a name="create-linux-based-clusters-in-hdinsight-using-the-azure-cli"></a>Créer des clusters basés sur Linux dans HDInsight à l’aide de l’infrastructure du langage commun Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

L’infrastructure du langage commun Azure est un utilitaire de ligne de commande disponibilité sur plusieurs plateformes qui vous permet de gérer les Services Azure. Il peut être utilisé, ainsi que des modèles de gestion des ressources Azure, pour créer un cluster de HDInsight, ainsi que des comptes de stockage associés et d’autres services.

Modèles de gestion des ressources Azure sont des documents JSON qui décrivent un __groupe de ressources__ et toutes les ressources qu’il contient (par exemple, HDInsight.) Cette approche basée sur le modèle vous permet de définir toutes les ressources dont vous avez besoin pour HDInsight dans un modèle. Il vous permet également de gérer les modifications apportées au groupe entier par le biais __des déploiements__auquel appliquer les modifications à l’ensemble du groupe.

Les étapes décrites dans ce document parcourez le processus de création d’un nouveau cluster HDInsight à l’aide de l’infrastructure du langage commun Azure et un modèle.

> [AZURE.IMPORTANT] Les étapes de ce document utilisent le nombre de nœuds de travail (4) par défaut pour un cluster HDInsight. Si vous prévoyez de plus de 32 nœuds de travail (lors de la création de cluster ou en redimensionnant le cluster), vous devez sélectionner une taille de nœud de tête au moins 8 cœurs et 14 Go de ram.
>
> Pour plus d’informations sur les tailles de nœud et les coûts associés, voir [HDInsight tarifs](https://azure.microsoft.com/pricing/details/hdinsight/).

##<a name="prerequisites"></a>Conditions préalables

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

- **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- __Azure infrastructure du langage commun__. Les étapes décrites dans ce document ont été testés dernière avec Azure infrastructure du langage commun version 0.10.1.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 


### <a name="access-control-requirements"></a>Exigences de contrôle d’accès

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

##<a name="log-in-to-your-azure-subscription"></a>Connectez-vous à votre abonnement Azure

Suivez les étapes présentées dans [se connecter à un abonnement Azure à partir de l’Interface de ligne de Azure (commande Azure)](../xplat-cli-connect.md) et se connecter à votre abonnement à l’aide de la méthode de __connexion__ .

##<a name="create-a-cluster"></a>Créer un cluster

Les étapes suivantes doivent être effectuées à partir d’une invite de commandes, shell ou une session Terminal Server après l’installation et configuration de l’infrastructure du langage commun Azure.

1. Utilisez la commande suivante s’authentifier à votre abonnement Azure :

        azure login

    Vous êtes invité à fournir votre nom et le mot de passe. Si vous avez plusieurs abonnements Azure, utilisez `azure account set <subscriptionname>` pour définir l’abonnement qui utilisent les commandes Azure infrastructure du langage commun.

3. Basculez en mode Azure le Gestionnaire de ressources à l’aide de la commande suivante :

        azure config mode arm

4. Créer un groupe de ressources. Ce groupe de ressources contient le cluster HDInsight et compte de stockage associé.

        azure group create groupname location
        
    * Remplacez le __nom de groupe__ par un nom unique pour le groupe. 
    * Remplacez __emplacement__ par la région géographique que vous voulez créer le groupe dans. 
    
        Pour obtenir la liste des emplacements valides, utilisez la `azure location list` de commande et utilisez un des emplacements dans la colonne __nom__ .

5. Créer un compte de stockage. Ce compte de stockage va être utilisé comme le stockage par défaut pour le cluster HDInsight.

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename
        
     * Remplacez le __nom de groupe__ par le nom du groupe créé à l’étape précédente.
     * Remplacez __l’emplacement__ avec le même emplacement utilisé à l’étape précédente. 
     * Remplacez __storagename__ par un nom unique pour le compte de stockage.
     
     > [AZURE.NOTE] Pour plus d’informations sur les paramètres utilisés dans cette commande, utilisez `azure storage account create -h` pour afficher l’aide de cette commande.

5. Récupérer la clé utilisée pour accéder au compte de stockage.

        azure storage account keys list -g groupname storagename
        
    * Remplacez le __nom de groupe__ par le nom de groupe de ressources.
    * Remplacez __storagename__ par le nom du compte de stockage.
    
    Dans les données qui sont renvoyées, enregistrez la valeur de __clé__ de __touche1__.

6. Créer un cluster HDInsight.

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 2 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * Remplacez le __nom de groupe__ par le nom de groupe de ressources.

    * Remplacez __Hadoop__ par le type de cluster que vous souhaitez créer. Par exemple, `Hadoop`, `HBase`, `Storm` ou `Spark`.

        > [AZURE.IMPORTANT] HDInsight clusters se présentent sous divers types, qui correspondent à la charge de travail ou la technologie réglé pour le cluster. Il n’est pas pris en charge de la méthode pour créer un cluster qui combine plusieurs types, tels que vague d’et HBase sur un cluster. 

    * Remplacez __l’emplacement__ avec le même emplacement utilisé dans les étapes précédentes.

    * Remplacez __storagename__ par le nom de compte de stockage.

    * Remplacez la __clé de stockage__ par la clé obtenue à l’étape précédente. 

    * Pour le `--defaultStorageContainer` paramètre, utilisez le même nom que vous utilisez pour le cluster.

    * Remplacez __administrateur__ et __httppassword__ par le nom et le mot de passe que vous souhaitez utiliser pour accéder au cluster via HTTPS.

    * Remplacez __sshuser__ et __sshuserpassword__ par le nom d’utilisateur et mot de passe que vous souhaitez utiliser lors de l’accès au cluster en utilisant SSH

    Il peut prendre plusieurs minutes pour que le processus de création de cluster terminer. En règle générale, environ 15.

##<a name="next-steps"></a>Étapes suivantes

À présent que vous avez créé un cluster HDInsight à l’aide de l’infrastructure du langage commun Azure, utilisez ce qui suit pour apprendre à utiliser avec votre cluster :

###<a name="hadoop-clusters"></a>Hadoop clusters

* [Utiliser Hive avec HDInsight](hdinsight-use-hive.md)
* [Utiliser cochon avec HDInsight](hdinsight-use-pig.md)
* [Utiliser MapReduce avec HDInsight](hdinsight-use-mapreduce.md)

###<a name="hbase-clusters"></a>Clusters HBase

* [Prise en main HBase sur HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Développement d’applications Java HBase sur HDInsight](hdinsight-hbase-build-java-maven-linux.md)

###<a name="storm-clusters"></a>Clusters vague

* [Développer des topologies Java pour vague d’HDInsight](hdinsight-storm-develop-java-topology.md)
* [Utiliser des composants Python dans vague d’HDInsight](hdinsight-storm-develop-python-topology.md)
* [Déployer et surveiller topologies avec vague d’HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)
