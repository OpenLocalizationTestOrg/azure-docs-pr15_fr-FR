<properties
    pageTitle="Publier des applications HDInsight | Microsoft Azure"
    description="Découvrez comment créer et publier des applications HDInsight."
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
    ms.date="10/18/2016"
    ms.author="jgao"/>

# <a name="publish-hdinsight-applications-into-the-azure-marketplace"></a>Publier des applications HDInsight dans Azure Marketplace

Une application HDInsight est une application que les utilisateurs peuvent installer sur un cluster HDInsight basé sur Linux. Ces applications peuvent être développées par Microsoft, éditeurs de logiciels (fil) ou par vous-même. Dans cet article, vous allez apprendre à publier une application HDInsight dans Azure Marketplace.  Pour obtenir des informations générales sur la publication dans Azure Marketplace, voir [publier une offre à la Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md).

HDInsight applications utilisent le modèle de *Mettre votre propre licence (BYOL)* , où fournisseur de l’application est responsable de la gestion des licences aux utilisateurs finaux de l’application et les utilisateurs finaux sont uniquement pratiqués par Azure pour les ressources qu'ils créent, tels que le cluster HDInsight et ses machines virtuelles/nœuds. Pour l’instant, facturation pour l’application proprement dite n’est pas effectuée par le biais Azure.

Associés aux autres applications HDInsight, article :

- [HDInsight installer les applications](hdinsight-apps-install-applications.md): Découvrez comment installer une application HDInsight à vos clusters.
- [Installer des applications personnalisées HDInsight](hdinsight-apps-install-custom-applications.md): Découvrez comment installer et tester des applications personnalisées HDInsight.

 
## <a name="prerequisites"></a>Conditions préalables

Pour envoyer votre application personnalisée à la place de marché, vous devez avoir créé et testé votre application personnalisée. Consultez les articles suivants :

- [Installer des applications personnalisées HDInsight](hdinsight-apps-install-custom-applications.md): Découvrez comment installer et tester des applications personnalisées HDInsight.

Vous devez avoir également enregistrer votre compte développeur. Voir [créer un compte Microsoft Developer](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md)et [publier une offre à la Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md) .

## <a name="define-application"></a>Définir l’application

Implique deux étapes pour publier des applications à la Azure Marketplace.  Tout d’abord vous définissez un fichier **createUiDef.json** pour indiquer quels clusters votre application est compatible avec ; et que vous publiez le modèle à partir du portail Azure. Voici un exemple de fichier createUiDef.json.

    {
        "handler": "Microsoft.HDInsight",
        "version": "0.0.1-preview",
        "clusterFilters": {
            "types": ["Hadoop", "HBase", "Storm", "Spark"],
            "tiers": ["Standard", "Premium"],
            "versions": ["3.4"]
        }
    }


|Champ  | Description   | Valeurs possibles|
|-------|---------------|----------------|
|types de  | Les types de cluster que l’application est compatible avec.    |Hadoop, HBase, vague de, explosion (ou n’importe quelle combinaison d'entre eux)|
|niveaux  | Les niveaux de cluster que l’application est compatible avec.    |Standard, Premium (ou les deux)|
|versions|  Les types de cluster HDInsight que l’application est compatible avec.    |3.4|

## <a name="package-application"></a>Application du package

Créer un fichier zip qui contient tous les fichiers requis pour l’installation de vos applications HDInsight. Vous devez le fichier zip dans [l’application de publication](#publish-application).

- [createUiDefinition.json](#define-application).
- mainTemplate.json. Afficher un exemple à [installer des applications personnalisées HDInsight](hdinsight-apps-install-custom-applications.md).

    >[AZURE.IMPORTANT] Le nom des noms de script installer application doit être unique pour un cluster avec le format ci-dessous. En outre les installer et désinstaller des actions de script doivent être idempotents, ce qui signifie que les scripts peut être appelé repeatly tout en produisant le même résultat.
    
    >   nom » : « [concat (' teinte-installer-v0 ','-', uniquestring('applicationName')] »
        
    >Remarque Il existe trois parties au nom du script :
        
    >   1. Un préfixe du nom de script, qui comporte le nom de l’application ou un nom approprié à l’application.
    >   2. A «- » pour améliorer la lisibilité.
    >   3. Fonction de chaîne unique avec le nom de l’application comme paramètre.

    >   Un exemple est le ci-dessus finit par devenir : teinte-installer-v0-4wkahss55hlas dans la liste des actions de script persistante. Pour une charge utile JSON d’exemple, voir [https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).

- Toutes les scripts.

> [AZURE.NOTE] Les fichiers d’application (y compris les fichiers d’application web cas échéant) peuvent être situés sur un point de terminaison accessible au public.

## <a name="publish-application"></a>Publier des applications

Suivez les étapes suivantes pour publier une application HDInsight :

1. Ouverture de session sur le [portail de publication Azure](https://publish.windowsazure.com/).
2. Cliquez sur **modèles de Solution** à partir de la gauche pour créer un modèle de solution.
3. Entrez un titre, puis cliquez sur **créer un nouveau modèle de solution**.
3. Cliquez sur **Centre de développement de créer du compte et de participer au programme d’Azure** pour inscrire votre société si vous n’avez pas fait.  Voir [Création d’un compte Microsoft Developer](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).
4. Cliquez sur **définir des Topologies pour commencer**. Un modèle de solution est un « parent » à l’ensemble de ses topologies. Vous pouvez définir plusieurs topologies dans un modèle offre/solution. Lorsqu’une offre est poussée mis en œuvre, il est déplacé avec toutes ses topologies. 
4. Entrez un nom de la topologie, puis cliquez sur le signe plus.
5. Entrez une nouvelle version, puis cliquez sur le signe Plus.
6. Téléchargez le fichier zip préparé dans [l’application de Package](#package-application).  
7. Cliquez sur **demande de Certification**. L’équipe de certification Microsoft passez en revue les fichiers et certifier la topologie.

## <a name="next-steps"></a>Étapes suivantes

- [HDInsight installer les applications](hdinsight-apps-install-applications.md): Découvrez comment installer une application HDInsight à vos clusters.
- [Installer des applications personnalisées HDInsight](hdinsight-apps-install-custom-applications.md): Découvrez comment déployer une application HDInsight non publiée sur HDInsight.
- [Clusters HDInsight basé sur Linux personnaliser à l’aide de Script Action](hdinsight-hadoop-customize-cluster-linux.md): Découvrez comment utiliser l’Action de Script à installer des applications supplémentaires.
- [Basé sur Linux créer Hadoop clusters dans HDInsight à l’aide de modèles de gestionnaire de ressources Azure](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Apprenez à appeler le Gestionnaire de ressources modèles pour créer des clusters HDInsight.
- [Utiliser des nœuds de bord vide dans HDInsight](hdinsight-apps-use-edge-node.md): Découvrez comment utiliser un nœud vide pour accéder à HDInsight cluster, test d’applications HDInsight et hébergement d’applications HDInsight.

