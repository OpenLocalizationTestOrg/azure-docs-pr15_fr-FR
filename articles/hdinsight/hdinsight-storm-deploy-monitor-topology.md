<properties
   pageTitle="Déployer et gérer des topologies vague Apache sur HDInsight | Microsoft Azure"
   description="Découvrez comment déployer, surveiller et gérer des topologies Apache vague à l’aide du tableau de bord vague de sur HDInsight. Utiliser Hadoop tools pour Visual Studio."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/11/2016"
   ms.author="larryfr"/>

#<a name="deploy-and-manage-apache-storm-topologies-on-windows-based-hdinsight"></a>Déployer et gérer des topologies vague Apache sur HDInsight fonctionnant sous Windows

Le tableau de bord vague de vous permet facilement déployer et exécuter des topologies vague Apache à votre cluster HDInsight à l’aide de votre navigateur web. Vous pouvez également utiliser le tableau de bord pour surveiller et gérer des topologies en cours d’exécution. Si vous utilisez Visual Studio, les outils de HDInsight pour Visual Studio fournir des fonctionnalités similaires dans Visual Studio.

Le tableau de bord vague d’et les fonctionnalités vague dans les outils de HDInsight appuient sur l’API REST vague de, qui peuvent servir à créer votre propre surveillance et les solutions de gestion.

> [AZURE.IMPORTANT] Les étapes décrites dans ce document nécessitent une vague de fonctionnant sous Windows sur cluster HDInsight. Pour plus d’informations sur l’utilisation d’un cluster Linux, consultez [déployer et gérer des topologies vague Apache sur HDInsight basé sur Linux](hdinsight-storm-deploy-monitor-topology-linux.md)

##<a name="prerequisites"></a>Conditions préalables

* **Vague d’Apache sur HDInsight** - voir <a href="../hdinsight-storm-getting-started/" target="_blank">prise en main vague d’Apache sur HDInsight</a> les étapes de création d’un cluster

* Pour le **Tableau de bord vague de**: un navigateur web moderne qui prend en charge HTML5

* Pour **Visual Studio** – Azure SDK 2.5.1 ou version ultérieure et Tools pour Visual Studio HDInsight. Voir <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">prise en main de l’utilisation HDInsight Tools pour Visual Studio</a> pour installer et configurer les outils HDInsight pour Visual Studio.

    Une des versions suivantes de Visual Studio :

    * Visual Studio 2012 avec la <a href="http://www.microsoft.com/download/details.aspx?id=39305" target="_blank">mise à jour 4</a>

    * Visual Studio 2013 avec la <a href="http://www.microsoft.com/download/details.aspx?id=44921" target="_blank">mise à jour 4</a> ou <a href="http://go.microsoft.com/fwlink/?LinkId=517284" target="_blank">Visual Studio 2013 Communauté</a>

    * <a href="http://visualstudio.com/downloads/visual-studio-2015-ctp-vs" target="_blank">Visual Studio 2015 CTP6</a>

    > [AZURE.NOTE] Les outils de HDInsight pour Visual Studio acceptent actuellement que vague d’HDInsight la version cluster 3,2.

##<a name="storm-dashboard"></a>Tableau de bord vague

Le tableau de bord vague d’est une page web disponible sur votre cluster vague. L’URL est **https://&lt;nom_cluster >.azurehdinsight.net/**, où **clustername** est le nom de votre vague de sur cluster HDInsight.

Dans la partie supérieure du tableau de bord vague de, sélectionnez **Envoyer la topologie**. Suivez les instructions sur la page pour exécuter un exemple de topologie ou pour télécharger et exécuter une topologie que vous avez créé.

![la page de la topologie envoyer][storm-dashboard-submit]

###<a name="storm-ui"></a>Vague d’interface utilisateur

Dans le tableau de bord vague de, sélectionnez le lien **Vague d’interface utilisateur** . Ceci affiche des informations sur le cluster, en plus de n’importe quel topologies en cours d’exécution.

![l’interface utilisateur vague][storm-dashboard-ui]

> [AZURE.NOTE] Avec certaines versions d’Internet Explorer, vous pouvez constater que l’interface utilisateur vague de n’est pas actualisée une fois que vous avez visités tout d’abord à celui-ci. Par exemple, il peut ne pas affiche les nouvelles topologies vous avez envoyé, ou il peut présenter une topologie comme étant actif lorsque vous avez précédemment désactivée. Microsoft connaît ce problème et travaille sur une solution.

####<a name="main-page"></a>Page principale

La page principale de l’interface utilisateur vague de fournit les informations suivantes :

* **Cluster résumé**: informations de base concernant le cluster vague.

* **Topologie de synthèse**: une liste de l’exécution de topologies. Utilisez les liens de cette section pour afficher plus d’informations sur les topologies spécifiques.

* **Responsable résumé**: informations sur le contrôleur vague.

* **Configuration Nimbus**: configuration Nimbus pour le cluster.

####<a name="topology-summary"></a>Topologie de synthèse

Sélectionner un lien de la section **topologie de synthèse** affiche les informations suivantes sur la topologie :

* **Topologie de synthèse**: informations de base sur la topologie.

* **Actions de topologie**: actions de gestion que vous pouvez effectuer pour la topologie.

    * **Activer**: traitement de CV d’une topologie désactivée.

    * **Désactiver**: interrompt une topologie en cours d’exécution.

    * **Rééquilibrer**: ajuste le parallélisme de la topologie. Vous devez rééquilibrer topologies en cours d’exécution après avoir modifié le nombre de nœuds dans le cluster. Cela permet de la topologie d’ajustez le parallélisme à compense le nombre d’augmentation ou de diminution des nœuds dans le cluster.

        Pour plus d’informations, voir <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Présentation du parallélisme d’une topologie vague</a>.

    * **Supprimer**: termine une topologie vague après le délai spécifié.

* **Statistiques de topologie**: statistiques de la topologie. Utilisez les liens dans la colonne de la **fenêtre** pour définir la durée des autres entrées dans la page.

* **Becs verseurs amovibles**: les becs verseurs utilisées par la topologie. Utilisez les liens de cette section pour afficher plus d’informations sur becs verseurs spécifiques.

* **Boulons**: les boulons utilisées par la topologie. Utilisez les liens de cette section pour afficher plus d’informations sur les boulons spécifiques.

* **Configuration de la topologie**: la configuration de la topologie sélectionnée.

####<a name="spout-and-bolt-summary"></a>Bec et boulons résumé

Sélection d’un bec des sections **becs verseurs amovibles** ou **boulons** affiche les informations suivantes sur l’élément sélectionné :

* **Composant Résumé**: informations de base sur les boulons bec.

* **Statistiques bec/boulons**: statistiques sur les boulons bec. Utilisez les liens dans la colonne de la **fenêtre** pour définir la durée des autres entrées dans la page.

* **Statistiques de saisie** (boulons uniquement) : informations sur les flux d’entrée utilisés par les boulons.

* **Statistiques de sortie**: informations sur les flux de données émis par cet goulotte ou boulons.

* **Les exécuteurs**: informations sur les instances du bec ou boulons. Sélectionnez le **Port** entrée d’un administrateur afficher le journal des informations de diagnostic produites pour cette instance spécifique.

* **Erreurs**: les informations d’erreur pendant ce goulotte ou boulons.

##<a name="hdinsight-tools-for-visual-studio"></a>HDInsight Tools pour Visual Studio

Les outils HDInsight peuvent servir à soumettre des topologies c# ou hybride à votre cluster vague. Les étapes suivantes utilisent un exemple d’application. Pour plus d’informations sur la création de votre propre topologies en utilisant les outils de HDInsight, voir [développer c# topologies à l’aide des outils HDInsight pour Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

Suivez les étapes suivantes pour déployer un exemple sur votre vague de sur cluster HDInsight, puis afficher et gérer la topologie.

1. Si vous n’avez pas déjà installé la dernière version des outils HDInsight pour Visual Studio, voir <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">prise en main de l’utilisation HDInsight Tools pour Visual Studio</a>.

2. Ouvrir Visual Studio, sélectionnez **fichier** > **Nouveau** > **projet**.

3. Dans la boîte de dialogue **Nouveau projet** , développez **installé** > **modèles**et puis sélectionnez **HDInsight**. Dans la liste des modèles, sélectionnez **Vague d’exemple**. Dans la partie inférieure de la boîte de dialogue, tapez un nom pour l’application.

    ![image](./media/hdinsight-storm-deploy-monitor-topology/sample.png)

1. Dans l' **Explorateur de solutions**, droit sur le projet, puis sélectionnez **Envoyer à vague d’HDInsight**.

    > [AZURE.NOTE] Si vous y êtes invité, entrez les informations d’identification pour votre abonnement Azure. Si vous avez plusieurs abonnements, connectez-vous à celui qui contient votre vague de sur cluster HDInsight.

2. Sélectionnez votre vague de sur HDInsight cluster dans la liste déroulante **Cluster vague de** , puis **Envoyer**. Vous pouvez contrôler si la présentation a réussi à l’aide de la fenêtre de **sortie** .

3. Lorsque la topologie a été envoyée, **Vague de Topologies** pour le cluster doit apparaître. Sélectionnez la topologie dans la liste pour afficher des informations sur la topologie en cours d’exécution.

    ![Analyseur de Visual studio](./media/hdinsight-storm-deploy-monitor-topology/vsmonitor.png)

    > [AZURE.NOTE] Vous pouvez également afficher **Topologies vague** **d’Explorateur** de serveurs en développement **Azure** > **HDInsight**, puis clic droit sur une vague de sur cluster HDInsight et sélection **Topologies de vague d’affichage**.

    Sélectionnez la forme pour la becs verseurs ou détaillée pour afficher des informations sur ces composants. Une nouvelle fenêtre s’ouvre pour chaque élément sélectionné.
    
    > [AZURE.NOTE] Le nom de la topologie est le nom de la topologie de classe (dans ce cas, `HelloWord`,) avec un horodatage ajouté.

4. À partir de la vue **Topologie de résumé** , sélectionnez **Arrêter** pour arrêter la topologie.

    > [AZURE.NOTE] Topologies vague continuer à exécuter jusqu'à ce qu’ils sont arrêtés ou le cluster est supprimé.

##<a name="rest-api"></a>API REST

L’interface utilisateur vague d’intégré en haut de l’API REST, pour vous pouvez d’effectuer la gestion similaires et la fonctionnalité d’analyse à l’aide de l’API REST. Vous pouvez utiliser l’API REST pour créer des outils personnalisés pour gérer et surveiller topologies vague.

Pour plus d’informations, voir [L’API REST vague d’interface utilisateur](https://github.com/apache/storm/blob/0.9.3-branch/STORM-UI-REST-API.md). Les informations suivantes sont spécifiques à l’utilisation de l’API REST avec vague d’Apache sur HDInsight.

###<a name="base-uri"></a>URI de base

URI de base pour l’API REST sur clusters HDInsight est **https://&lt;nom_cluster >.azurehdinsight.net/stormui/api/v1/**, où **clustername** est le nom de votre vague de sur cluster HDInsight.

###<a name="authentication"></a>Authentification

Demandes à l’API REST doivent utiliser **l’authentification de base**, afin que vous utilisez le nom de l’administrateur cluster HDInsight et le mot de passe.

> [AZURE.NOTE] Étant donné que l’authentification de base est envoyée à l’aide de texte clair, vous devez **toujours** utiliser HTTPS pour sécuriser les communications avec le cluster.

###<a name="return-values"></a>Valeurs de retour

Informations sont renvoyées à partir de l’API REST peuvent uniquement être utilisables à partir de dans le cluster ou machines virtuelles sur le même réseau virtuel Azure que le cluster. Par exemple, le nom de domaine complet (FQDN) renvoyé pour les serveurs soigneur ne sera pas accessible à partir d’Internet.

##<a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris à déployer et surveiller topologies à l’aide du tableau de bord vague de, découvrez comment :

* [Développer c# topologies à l’aide des outils HDInsight pour Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

* [Développer des topologies basé sur Java à l’aide de Maven](hdinsight-storm-develop-java-topology.md)

Pour une liste des exemples de topologies plus, voir [exemples de topologies pour vague d’HDInsight](hdinsight-storm-example-topology.md).

[hdinsight-dashboard]: ./media/hdinsight-storm-deploy-monitor-topology/dashboard-link.png
[storm-dashboard-submit]: ./media/hdinsight-storm-deploy-monitor-topology/submit.png
[storm-dashboard-ui]: ./media/hdinsight-storm-deploy-monitor-topology/storm-ui-summary.png
