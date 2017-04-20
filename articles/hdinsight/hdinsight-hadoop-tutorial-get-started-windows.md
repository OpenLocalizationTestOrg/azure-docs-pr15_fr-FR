<properties
   pageTitle="Hadoop didacticiel : prise en main Hadoop sur Windows | Microsoft Azure"
   description="Prise en main Hadoop dans HDInsight. Apprenez à créer des clusters Hadoop sur Windows, exécutez une requête Hive sur données et analyser le résultat dans Excel."
   keywords="didacticiel Hadoop, hadoop sur windows, cluster hadoop, découvrez hadoop, requête hive"
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
   ms.date="03/07/2016"
   ms.author="nitinme"/>


# <a name="hadoop-tutorial-get-started-using-hadoop-in-hdinsight-on-windows"></a>Didacticiel Hadoop : commencer à utiliser Hadoop dans HDInsight sous Windows

> [AZURE.SELECTOR]
- [Basé sur Linux](../hdinsight-hadoop-linux-tutorial-get-started.md)
- [Fonctionnant sous Windows](../hdinsight-hadoop-tutorial-get-started-windows.md)

Pour vous aider à découvrir Hadoop sur Windows et commencer à utiliser HDInsight, ce didacticiel vous montre comment exécuter une requête Hive sur des données non structurées dans un cluster Hadoop et puis analyser les résultats dans Microsoft Excel.

>[AZURE.NOTE] Les informations de ce document sont spécifiques aux clusters HDInsight fonctionnant sous Windows. Pour plus d’informations sur les clusters basés sur Linux, consultez [Hadoop didacticiel : commencer à utiliser Hadoop basé sur Linux dans HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).

Supposons que vous avez un jeu de données non structuré volumineux et que vous voulez exécuter une requête Hive sur lui permettant d’extraire des informations utiles. Il s’agit exactement ce que vous vous apprêtez à faire dans ce didacticiel. Voici comment vous y parvenir :

   ![« Hadoop didacticiel : créer un compte ; créer un cluster Hadoop ; soumettre une requête Hive ; analyser des données dans Excel.][image-hdi-getstarted-flow]

Regarder une vidéo de démonstration de ce didacticiel pour en savoir plus Hadoop sur HDInsight :

![Vidéo d’un didacticiel Hadoop première : soumettre une requête Hive sur un cluster Hadoop et analyser les résultats dans Excel.][img-hdi-getstarted-video]

**[Recherchez le didacticiel Hadoop HDInsight sur YouTube](https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS)**

Conjointement avec la disponibilité générale de Azure HDInsight, Microsoft fournit également émulateur HDInsight pour Azure, auparavant connu sous *Aperçu pour les développeurs Microsoft HDInsight*. L’émulateur cible scénarios développeur et prend uniquement en charge les déploiements nœud unique. Pour plus d’informations sur l’utilisation d’émulateur HDInsight, voir [Prise en main l’émulateur HDInsight][hdinsight-emulator].

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel pour Hadoop sur Windows, vous devez disposer des éléments suivants :

- **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Un poste de travail ordinateur** avec Office Professionnel Plus 2013, Office 365 ProPlus, version autonome d’Excel 2013 ou Office 2010 Professionnel Plus.

### <a name="access-control-requirements"></a>Exigences de contrôle d’accès

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

##<a name="create-hadoop-clusters"></a>Créer des clusters Hadoop

Lorsque vous créez un cluster, vous créez ressources cluster Azure qui contiennent Hadoop et applications associées. Dans cette section, vous créez un cluster de version 3.2 HDInsight. Vous pouvez également créer des clusters Hadoop pour d’autres versions. Pour obtenir des instructions, voir [clusters de créer HDInsight à l’aide des options personnalisées][hdinsight-provision]. Pour plus d’informations sur les versions de HDInsight et de niveau de service souscrit, voir [le contrôle de version composant HDInsight](hdinsight-component-versioning.md).


**Pour créer un cluster Hadoop**

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Cliquez sur **Nouveau**et cliquez sur **Données Analytique**, puis cliquez sur **HDInsight**. Le portail s’ouvre une carte de **Nouveau HDInsight Cluster** .

    ![Créer un nouveau cluster dans le portail Azure] (./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.CreateCluster.1.png "Créer un nouveau cluster dans le portail Azure")

3. Entrez ou sélectionnez les options suivantes :

    ![Type et entrez le nom de cluster] (./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.CreateCluster.2.png "Type et entrez le nom de cluster")
    
  	|Nom de champ| Valeur|
  	|----------|------|
  	|Nom du cluster| Un nom unique pour identifier le cluster|
  	|Type de cluster| Sélectionnez **Hadoop** pour ce didacticiel. |
  	|Système d’exploitation cluster| Sélectionnez **Windows Server 2012 R2 centre de données** pour ce didacticiel.|
  	|Version HDInsight| Sélectionnez la dernière version de ce didacticiel.|
  	|Abonnement| Sélectionnez l’abonnement Azure qui sera utilisé pour le cluster.|
  	|Groupe de ressources | Sélectionnez un groupe de ressources Azure existant ou créer un nouveau groupe de ressources. Un cluster HDInsight base contient un cluster et son compte de stockage par défaut.  Vous pouvez regrouper les deux dans un groupe de ressources pour faciliter la gestion.|
  	|Informations d’identification| Entrez le nom de connexion cluster et le mot de passe. Sur Windows cluster peut avoir 2 comptes d’utilisateur.  L’utilisateur cluster (ou utilisateur HTTP) est utilisé pour gérer le cluster et soumettre des tâches.  Vous pouvez également créer un bureau à distance (RDP) compte d’utilisateur à distance connecter au cluster. Si vous choisissez d’activer Bureau à distance, vous allez créer le compte d’utilisateur RDP.|
  	|Source de données| Cliquez sur Créer pour créer un nouveau compte de stockage Azure par défaut. Utiliser le nom du cluster comme le nom du conteneur par défaut. Chaque cluster HDinsight a un conteneur Blob par défaut sur un compte de stockage Azure.  L’emplacement du compte de stockage Azure par défaut détermine l’emplacement du cluster HDInsight.|
  	|Nœud tarifs niveaux| Utiliser des nœuds de travail 1 ou 2 avec la note de tête et nœud de travail par défaut tarifs couche pour ce didacticiel.|
  	|Configuration facultative| Ignorer cette partie.|

9. Dans la carte de **Nouveau HDInsight Cluster** , vérifiez que l’option **attacher aux Startboard** est sélectionnée, puis cliquez sur **créer**. Cela crée le cluster et ajouter une vignette pour qu’elle à la Startboard de votre portail Azure. L’icône indique que le cluster consiste à créer et modifiera pour afficher l’icône HDInsight une fois terminée la création.

  	| Lors de la création | Création terminée |
  	| ------------------ | --------------------- |
  	| ![Création de l’indicateur sur startboard](./media/hdinsight-hadoop-tutorial-get-started-windows/provisioning.png) | ![Créé vignette cluster](./media/hdinsight-hadoop-tutorial-get-started-windows/provisioned.png) |

    > [AZURE.NOTE] Il prend un certain temps pour le cluster doit être créée, généralement environ 15 minutes. Utiliser la vignette sur la Startboard ou l’entrée de **Notifications** à gauche de la page pour vérifier le processus de création.

10. Une fois la création terminée, cliquez sur la vignette pour le cluster à partir de la Startboard pour lancer la carte cluster.


## <a name="run-a-hive-query-from-the-portal"></a>Exécuter une requête Hive à partir du portail
Maintenant que vous avez créé un cluster de HDInsight, l’étape suivante consiste à exécuter une tâche Hive pour interroger un exemple de table Hive. Nous utiliserons *hivesampletable*, qui est fourni avec clusters HDInsight. La table contient des données sur les fabricants d’appareil mobile, plateformes et modèles. Une requête Hive sur cette table extrait les données pour les appareils mobiles par un fabricant spécifique.

> [AZURE.NOTE] HDInsight Tools pour Visual Studio est fourni avec le Kit de développement Azure pour .NET version 2.5 ou version ultérieure. En utilisant les outils dans Visual Studio, vous pouvez vous connecter à HDInsight cluster, créer des tables Hive et exécuter des requêtes Hive. Pour plus d’informations, voir [prise en main de l’utilisation HDInsight Hadoop Tools pour Visual Studio][1].

**Pour exécuter une tâche Hive du tableau de bord cluster**

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Cliquez sur **Rechercher tout** , puis sur **Clusters HDInsight** pour afficher la liste des clusters, y compris le cluster que vous venez de créer dans la section précédente.
3. Cliquez sur le nom du cluster que vous voulez utiliser pour exécuter la tâche Hive, puis cliquez sur **tableau de bord** en haut de la carte.
4. Une page Web s’ouvre dans un onglet du navigateur différent. Entrez la Hadoop compte d’utilisateur et mot de passe. Le nom d’utilisateur par défaut est **administrateur**; le mot de passe est ce que vous avez entré lors de la création du cluster.
5. Dans le tableau de bord, cliquez sur l’onglet **Éditeur ruche** . La page web suivante s’ouvre.

    ![Onglet de l’éditeur Hive dans le tableau de bord cluster HDInsight.][img-hdi-dashboard]

    Il existe plusieurs onglets en haut de la page. L’onglet par défaut est **L’éditeur de la ruche**et les autres onglets sont **L’historique des travaux** et **Explorateur de fichiers**. En utilisant le tableau de bord, vous pouvez soumettre des requêtes Hive, vérifiez les journaux de travail Hadoop et parcourir les fichiers dans le stockage.

    > [AZURE.NOTE] Notez que l’URL de la page Web est * &lt;nomducluster&gt;. azurehdinsight.net*. Donc au lieu d’ouvrir le tableau de bord à partir du portail, vous pouvez ouvrir le tableau de bord à partir d’un navigateur web à l’aide de l’URL.

6. Sous l’onglet **Éditeur ruche** , pour **Nom de la requête**, entrez **HTC20**.  Le nom de requête est le titre de la tâche. Dans le volet requête, entrez la requête Hive comme indiqué dans l’image :

    ![Requête Hive entrée dans le volet de requête de l’éditeur de ruche.][img-hdi-dashboard-query-select]

4. Cliquez sur **Envoyer**. Il faut quelques instants pour obtenir le résultat précédent. L’écran s’actualise toutes les 30 secondes. Vous pouvez également cliquer sur **Actualiser** pour actualiser l’écran.

    ![Résultats d’une requête Hive dans apparaît en bas du tableau de bord cluster.][img-hdi-dashboard-query-select-result]

5. Une fois que la colonne Statut indique que la tâche est terminée, cliquez sur le nom de la requête à l’écran pour voir le résultat. Prenez note de **Travail Démarrer heure (UTC)**. Vous en aurez besoin ultérieurement.

    ![Fonction heure de début répertoriées dans l’onglet Historique des travaux du tableau de bord cluster HDInsight.][img-hdi-dashboard-query-select-result-output]

    La page affiche également la **Sortie des tâches** et dans le **Journal de travail**. Vous avez également la possibilité de télécharger le fichier de sortie (\_stdout) et le fichier journal \(_stderr).


**Pour rechercher le fichier de copie**

1. Dans le tableau de bord cluster, cliquez sur **Explorateur de fichiers**.
2. Cliquez sur le nom de votre compte de stockage et cliquez sur votre nom de conteneur (qui est identique à votre nom de cluster), puis cliquez sur **l’utilisateur**.
3. Cliquez sur **administrateur** , puis sur le GUID qui comporte l’heure de dernière modification (un peu après le travail démarré heure que vous avez noté plus haut). Copiez ce GUID. Vous en aurez besoin dans la section suivante.


    ![La requête Hive représentation fichier que GUID répertorié dans l’onglet Explorateur de fichiers.][img-hdi-dashboard-query-browse-output]


##<a name="connect-to-microsoft-business-intelligence-tools-for-excel"></a>Se connecter à des outils d’analyse décisionnelle Microsoft Excel

Vous pouvez utiliser le complément Power Query pour Microsoft Excel pour importer la sortie des tâches à partir de HDInsight dans Excel, où les outils d’analyse décisionnelle Microsoft peuvent servir à analyser les résultats.

Vous devez disposer d’Excel 2013 ou 2010 est installé pour cette partie du didacticiel.

**Télécharger Microsoft Power Query pour Excel**

- Télécharger Microsoft Power Query pour Excel Microsoft à partir du [Centre de téléchargement Microsoft](http://www.microsoft.com/download/details.aspx?id=39379) et l’installer.

**Pour importer des données HDInsight**

1. Ouvrez Excel et créez un nouveau classeur.
3. Cliquez sur le menu de **Power Query** , cliquez sur **à partir d’autres Sources**, puis cliquez sur **à partir d’Azure HDInsight**.

    ![Menu Excel PowerQuery importation ouvert pour Azure HDInsight.][image-hdi-gettingstarted-powerquery-importdata]

3. Entrez le **Nom du compte** du compte de stockage d’objets Blob Azure associé à votre cluster, puis cliquez sur **OK**. (Il s’agit du compte de stockage que vous avez créée précédemment dans le didacticiel).
4. Entrez la **Clé de compte** pour le compte de stockage d’objets Blob Azure, puis cliquez sur **Enregistrer**.
5. Dans le volet droit, double-cliquez sur le nom d’objets blob. Par défaut le nom d’objets blob est le même que le nom du cluster.

6. Dans la colonne **nom** , recherchez **stdout** . Vérifiez que le GUID dans la colonne correspondante **Chemin du dossier** correspond le GUID que vous avez copiée précédemment. Une correspondance suggère que les données de sortie correspondant à la tâche que vous avez envoyé. Cliquez sur **binaire** dans la colonne à gauche de **stdout**.

    ![Recherche de la sortie des données par GUID dans la liste de contenu.][image-hdi-gettingstarted-powerquery-importdata2]

9. Cliquez sur **Fermer et charger** dans le coin supérieur gauche pour importer le travail Hive sortie dans Excel.

##<a name="run-samples"></a>Exécuter des exemples

HDInsight cluster fournit une console de requête qui inclut une galerie mise en route pour exécuter les exemples directement à partir du portail. Vous pouvez utiliser les exemples pour apprendre à utiliser HDInsight à étude de quelques scénarios de base. Ces exemples fournis avec tous les composants requis, tels que les données à analyser et les requêtes à s’exécuter sur les données. Pour en savoir plus sur les exemples dans la galerie mise en route, voir [En savoir Hadoop dans HDInsight à l’aide de la galerie HDInsight prise en main](hdinsight-learn-hadoop-use-sample-gallery.md).

**Pour exécuter l’exemple**

1. Dans le portail Azure startboard, cliquez sur la vignette pour le cluster que vous venez de créer.
 
2. Dans la nouvelle carte cluster, cliquez sur **tableau de bord**. Lorsque vous y êtes invité, entrez le nom d’utilisateur admin et le mot de passe pour le cluster.

    ![Lancer tableau de bord cluster] (./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.Cluster.Dashboard.png "Lancer tableau de bord cluster")
 
3. À partir de la page Web qui s’ouvre, cliquez sur l’onglet **Prise en main galerie** et puis, sous la catégorie **Solutions avec des exemples de données** , cliquez sur l’échantillon que vous souhaitez exécuter. Suivez les instructions sur la page Web pour terminer l’échantillon. Le tableau suivant répertorie quelques exemples et donne des informations supplémentaires sur chaque exemple de quel effectue.

Exemple | Ce que cela peut-il ?
------ | ---------------
[Analyse des données capteur][hdinsight-sensor-data-sample] | Découvrez comment utiliser HDInsight pour traiter les données historiques qui sont générées par chauffage, ventilation et les systèmes de conditionnement d’air (HVAC) pour identifier les systèmes qui ne sont pas en mesure de fiable maintenir une température de jeu.
[Analyse des journaux de site Web][hdinsight-weblogs-sample] | Découvrez comment utiliser HDInsight pour analyser les fichiers journaux de site Web pour obtenir claires sur la fréquence des visites sur le site Web dans un jour à partir des sites Web externes, ainsi qu’un résumé des erreurs de site Web que les utilisateurs rencontrent.
[Analyse des tendances Twitter](hdinsight-analyze-twitter-data.md) | Découvrez comment utiliser HDInsight pour analyser les tendances dans Twitter.

##<a name="delete-the-cluster"></a>Supprimer le cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##<a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel Hadoop, vous avez appris comment créer un cluster Hadoop sur Windows dans HDInsight, exécutez une requête Hive sur les données et importer les résultats dans Excel, où ils peuvent être plus traitement et affiché sous forme graphique avec les outils d’analyse décisionnelle. Pour plus d’informations, consultez les didacticiels suivants :

- [Prise en main avec HDInsight Hadoop Tools pour Visual Studio][1]
- [Prise en main l’émulateur HDInsight][hdinsight-emulator]
- [Associer stockage d’objets Blob Azure HDInsight][hdinsight-storage]
- [Administrer HDInsight à l’aide de PowerShell][hdinsight-admin-powershell]
- [Télécharger des données à HDInsight][hdinsight-upload-data]
- [Utiliser MapReduce avec HDInsight][hdinsight-use-mapreduce]
- [Utiliser Hive avec HDInsight][hdinsight-use-hive]
- [Utiliser cochon avec HDInsight][hdinsight-use-pig]
- [Utiliser Oozie avec HDInsight][hdinsight-use-oozie]
- [Développer des programmes Java MapReduce pour HDInsight][hdinsight-develop-mapreduce]


[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-versions]: hdinsight-component-versioning.md


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-emulator]: hdinsight-hadoop-emulator-get-started.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hadoop-hdinsight-intro]: hdinsight-hadoop-introduction.md
[hdinsight-weblogs-sample]: hdinsight-hive-analyze-website-log.md
[hdinsight-sensor-data-sample]: hdinsight-hive-analyze-sensor-data.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[apache-hive]: http://go.microsoft.com/fwlink/?LinkId=510085
[apache-mapreduce]: http://go.microsoft.com/fwlink/?LinkId=510086
[apache-hdfs]: http://go.microsoft.com/fwlink/?LinkId=510087
[hdinsight-hbase-custom-provision]: hdinsight-hbase-tutorial-get-started.md


[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: powershell-install-configure.md
[powershell-open]: powershell-install-configure.md#step-1-install


[img-hdi-dashboard]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.browse.output.png

[img-hdi-getstarted-video]: ./media/hdinsight-hadoop-tutorial-get-started-windows/hdi-get-started-video.png


[image-hdi-storageaccount-quickcreate]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.StorageAccount.QuickCreate.png
[image-hdi-clusterstatus]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.ClusterStatus.png
[image-hdi-quickcreatecluster]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.QuickCreateCluster.png
[image-hdi-getstarted-flow]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GetStartedFlow.png

[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData2.png
 
