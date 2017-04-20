<properties
    pageTitle="Prévisions dans Guide technique énergétique de la demande | Microsoft Azure"
    description="Guide technique au modèle de Solution avec Microsoft Cortana Intelligence pour réaliser des prévisions dans énergétique de la demande."
    services="cortana-analytics"
    documentationCenter=""
    authors="yijichen"
    manager="ilanr9"
    editor="yijichen"/>

<tags
    ms.service="cortana-analytics"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/16/2016"
    ms.author="inqiu;yijichen;ilanr9"/>

# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-demand-forecast-in-energy"></a>Guide technique au modèle de Solution Intelligence Cortana pour réaliser des prévisions dans énergétique de la demande

## <a name="overview"></a>**Vue d’ensemble**

Modèles de solutions sont conçues pour accélérer le processus de création d’une démonstration E2E en haut de gamme d’Intelligence Cortana. Un modèle déployé sera mise en service de votre abonnement avec composant Cortana Intelligence nécessaire et créer les relations entre. Il est également basée sur le pipeline de données avec des exemples de données prise générés à partir d’une application de simulation de données. Télécharger le simulator de données à partir du lien fourni et installez-le sur votre ordinateur local, reportez-vous au fichier Lisezmoi pour obtenir des instructions sur l’utilisation de la simulator. Données générées par le simulator seront hydrate le pipeline de données et commencer à générer des prédiction de formation machine puis peut être affichée dans le tableau de bord Power BI.

Vous pouvez trouver le modèle de solution [ici](https://gallery.cortanaintelligence.com/SolutionTemplate/Demand-Forecasting-for-Energy-1) 

Le processus de déploiement vous guidera tout au long de plusieurs étapes pour configurer vos informations d’identification de la solution. Vérifiez que vous enregistrez ces informations d’identification telles que le nom de la solution, nom d’utilisateur et mot de passe que vous fournissez durant le déploiement.

L’objectif de ce document est pour décrire l’architecture de référence et les différents composants mis en service dans votre abonnement dans le cadre de ce modèle de Solution. Le document parle également comment remplacer les données d’exemple, avec des données réelles de votre propre puissent voir insights/prévisions à partir de vous a remporté des données. En outre, le document aborde les parties du modèle de Solution qui devra être modifié si vous souhaitez personnaliser la solution avec vos propres données. Vous trouverez des instructions sur la façon de créer le tableau de bord Power BI pour ce modèle de Solution à la fin.

## <a name="big-picture"></a>**Vue d’ensemble**

![](media\cortana-analytics-technical-guide-demand-forecast\ca-topologies-energy-forecasting.png)

### <a name="architecture-explained"></a>Architecture expliqué
Lorsque la solution est déployée, les différents services Azure au sein de Cortana Analytique Suite sont activées (*c'est-à-dire* Hub de l’événement, flux Analytique, HDInsight, Data Factory, apprentissage automatique, *etc.*). Le diagramme architecture ci-dessus indique, à un niveau élevé, comment la prévision de la demande pour énergétique Solution modèle est construite à partir de bout en bout. Vous ne pourrez pas étudier ces services en cliquant dessus dans le diagramme de modèle de solution créé avec le déploiement de la solution. Les sections suivantes décrivent chaque élément.

## <a name="data-source-and-ingestion"></a>**Réception et Source de données**

### <a name="synthetic-data-source"></a>Source de données de synthèse

Pour ce modèle de la source de données utilisée est générée à partir d’une application de bureau que vous allez télécharger et exécuter localement après le déploiement réussi. Vous trouverez les instructions pour télécharger et installer cette application dans la barre de propriétés lorsque vous sélectionnez le premier nœud appelé énergétique réaliser des prévisions données Simulator sur le diagramme de modèle de solution. Cette application flux le service [Azure événement concentrateur](#azure-event-hub) avec points de données ou des événements, qui seront utilisées dans le reste du flux de solution.

L’application de génération événement insère le Hub événement Azure uniquement pendant qu’il s’exécute sur votre ordinateur.

### <a name="azure-event-hub"></a>Concentrateur événement Azure

Le service [Azure événement concentrateur](https://azure.microsoft.com/services/event-hubs/) est le destinataire de l’entrée fournie par la Source de données synthétique ci-dessus.

## <a name="data-preparation-and-analysis"></a>**Analyse et préparation des données**


### <a name="azure-stream-analytics"></a>Flux de données Azure Analytique

Le service [Azure flux Analytique](https://azure.microsoft.com/services/stream-analytics/) est utilisé pour fournir près analytique en temps réel sur le flux d’entrée à partir du service [Azure événement concentrateur](#azure-event-hub) et publier des résultats dans un tableau de bord [Power BI](https://powerbi.microsoft.com) , ainsi que l’archivage de tous les événements entrants brutes au service de [Stockage Azure](https://azure.microsoft.com/services/storage/) pour un traitement ultérieur par le service [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) .

### <a name="hd-insights-custom-aggregation"></a>HD Insights agrégation personnalisée

Le service Azure HD aperçu est utilisé pour exécuter des scripts [ruche](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) (déclenchés par Azure Data Factory) afin de fournir des agrégations sur les événements brutes qui ont été archivés au moyen du service Azure flux Analytique.

### <a name="azure-machine-learning"></a>Apprentissage automatique Azure

Utilisation du service [d’Apprentissage automatique Azure](https://azure.microsoft.com/services/machine-learning/) (déclenché par Azure Data Factory) pour rendre la prévision sur la consommation électrique futures d’une région particulière, étant donnée les entrées reçues.

## <a name="data-publishing"></a>**Publication de données**


### <a name="azure-sql-database-service"></a>Service de base de données SQL Azure

Le service de [Base de données SQL Azure](https://azure.microsoft.com/services/sql-database/) permet de stocker (gérés par Azure Data Factory) les prévisions reçues par le service d’apprentissage automatique Azure qui est consommé dans le tableau de bord [Power BI](https://powerbi.microsoft.com) .

## <a name="data-consumption"></a>**Consommation de données**

### <a name="power-bi"></a>Power BI

Le service [Power BI](https://powerbi.microsoft.com) est utilisé pour afficher un tableau de bord contient des agrégations fournies par [Azure flux Analytique](https://azure.microsoft.com/services/stream-analytics/) service, ainsi que la demande des prévisions, stockées dans la [Base de données SQL Azure](https://azure.microsoft.com/services/sql-database/) qui ont été produits au moyen du service [d’Apprentissage automatique Azure](https://azure.microsoft.com/services/machine-learning/) . Pour obtenir des Instructions sur la façon de créer le tableau de bord Power BI pour ce modèle de Solution, reportez-vous à la section ci-dessous.

## <a name="how-to-bring-in-your-own-data"></a>**Comment faire pour importer vos données**

Cette section décrit comment importer vos propres données Azure, et quels sont les domaines de verser modifications pour les données que vous importez dans cette architecture.

Il est probable qu’un dataset que vous amener correspond le jeu de données utilisé pour ce modèle de solution. Présentation de vos données et la configuration requise sera crucial pour comment vous modifiez ce modèle pour l’utiliser avec vos propres données. S’il s’agit de votre première exposition au service apprentissage automatique Azure, vous pouvez obtenir une introduction à celui-ci à l’aide de l’exemple de la rubrique [comment créer votre première expérience](machine-learning\machine-learning-create-experiment.md).

Les sections suivantes aborderons les sections du modèle qui requièrent des modifications lors de l’introduction d’un nouveau groupe de données.

### <a name="azure-event-hub"></a>Concentrateur événement Azure

Le service [Azure événement concentrateur](https://azure.microsoft.com/services/event-hubs/) est très générique, telles que les données peuvent être validées dans le hub au format CSV ou JSON. Aucun traitement spécial se produit dans le Hub événement Azure, mais il est important que comprendre les données qui sont chargées dans il.

Ce document ne décrit pas comment faire pour ajouter vos données, mais une peut envoyer facilement événements ou les données à un concentrateur d’événement Azure, à l’aide de l' [Événement concentrateur API](event-hubs\event-hubs-programming-guide.md).

### <a name="azure-stream-analytics"></a>Flux de données Azure Analytique

Le service [Azure flux Analytique](https://azure.microsoft.com/services/stream-analytics/) sert à fournir près analytique en temps réel en lisant à partir de flux de données et de sortie des données vers n’importe quel nombre de sources.

Pour la prévision de la demande pour énergétique Solution modèle, la requête Analytique de flux de données Azure se compose de deux requêtes sous-adresse, chaque consommation d’événements à partir du service Azure événement concentrateur comme entrées et avoir des sorties à deux emplacements distincts. Ces sorties comprennent un jeu de données Power BI et un emplacement de stockage Azure.

La requête [Azure flux Analytique](https://azure.microsoft.com/services/stream-analytics/) soient retrouvée en :

-   Se connecter au [portail de gestion Azure](https://manage.windowsazure.com/)

-   Vous avez trouvé les travaux analytique flux ![](media\cortana-analytics-technical-guide-demand-forecast\icon-stream-analytics.png) qui ont été générées lors du déployée de la solution. Une pour transmettre des données à blob storage (par exemple, mytest1streaming432822asablob), l’autre est pour transmettre des données à Power BI (par exemple, mytest1streaming432822asapbi).


-   Sélection

    -   ***Entrées*** pour afficher l’entrée de requête

    -   ***Requête*** pour afficher la requête elle-même

    -   ***EXPORTE*** pour afficher les différentes sorties

Vous trouverez des informations à propos de construction d’une requête Analytique de flux de données Azure dans la [Référence de requête Analytique flux](https://msdn.microsoft.com/library/azure/dn834998.aspx) sur MSDN.

Dans cette solution, le travail Azure flux Analytique qui génère dataset avec près informations analytique en temps réel sur le flux de données entrant à un tableau de bord Power BI est fourni dans le cadre de ce modèle de solution. Car il existe une connaissance implicite sur le format de données entrantes, ces requêtes devront être modifiées en fonction de votre format de données.

L’autre tâche Azure flux Analytique renvoie tous les événements [d’Événement concentrateur](https://azure.microsoft.com/services/event-hubs/) au [Stockage Azure](https://azure.microsoft.com/services/storage/) et ne requiert, par conséquent, aucune modification quel que soit votre format de données comme les informations d’événement complète sont en continu au stockage.

### <a name="azure-data-factory"></a>Données Azure usine

Le service [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) orchestre le mouvement et le traitement des données. La prévision à la demande pour énergétique Solution modèle le factory de données est composé de douze [pipelines](data-factory\data-factory-create-pipelines.md) qui déplacer et traiter les données à l’aide de différentes technologies.

  Vous pouvez accéder à votre usine de données en ouvrant le nœud Data Factory en bas du diagramme de modèle de solution créé avec le déploiement de la solution. Vous accédez alors à l’usine de données sur le portail de gestion Azure. Si vous rencontrez des erreurs sous jeux de vos données, vous pouvez ignorer ceux qu’ils sont en raison d’usine données déployée avant le Générateur de données a été démarré. Ces erreurs n’empêchent pas votre usine de données de fonctionner.

Cette section décrit les nécessaires [pipelines](data-factory\data-factory-create-pipelines.md) et les [activités](data-factory\data-factory-create-pipelines.md) contenues dans [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Voici la vue de diagramme de la solution.

![](media\cortana-analytics-technical-guide-demand-forecast\ADF2.png)

Cinq des pipelines de cette usine contiennent des scripts [ruche](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) servent à partition et agréger les données. Une fois que noté, les scripts seront situées dans le compte de [Stockage Azure](https://azure.microsoft.com/services/storage/) créé pendant l’installation. Leur emplacement sera : demandforecasting\\\\script\\\\hive\\ \\ (ou https://[Your solution name].blob.core.windows.net/demandforecasting).

Comme pour les requêtes [Azure flux Analytique](#azure-stream-analytics-1) , les scripts [ruche](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) que connaissance implicite sur le format de données entrants, ces requêtes devront être modifiées en fonction de vos besoins en matière de [ingénierie fonctionnalité](machine-learning\machine-learning-feature-selection-and-engineering.md) et le format des données.

#### <a name="aggregatedemanddatato1hrpipeline"></a>*AggregateDemandDataTo1HrPipeline*

Ce pipeline [pipeline](data-factory\data-factory-create-pipelines.md) contient une seule activité : une activité [HDInsightHive](data-factory\data-factory-hive-activity.md) à l’aide d’un [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) qui exécute un script [ruche](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) pour agréger le toutes les 10 secondes diffusés en continu dans les données à la demande dans niveau sous-centrale à un niveau région toutes les heures et le placer dans le [Stockage Azure](https://azure.microsoft.com/services/storage/) la tâche d’Azure flux Analytique.

Le script [ruche](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) pour cette tâche partition est ***AggregateDemandRegion1Hr.hql***


#### <a name="loadhistorydemanddatapipeline"></a>*LoadHistoryDemandDataPipeline*

Ce [pipeline](data-factory\data-factory-create-pipelines.md) contient deux activités :
- Activité [HDInsightHive](data-factory\data-factory-hive-activity.md) à l’aide d’un [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) qui exécute un script Hive pour agréger les données à la demande historique horaire dans niveau sous-centrale à toutes les heures région et la placer dans le stockage Azure lors de la tâche Azure flux Analytique

- Activité de [copie](https://msdn.microsoft.com/library/azure/dn835035.aspx) qui déplace les données agrégées à partir d’objets blob Azure Storage à la base de données SQL Azure qui a été mis en service dans le cadre de l’installation de modèle de solution.

Le script [ruche](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) pour cette tâche est ***AggregateDemandHistoryRegion.hql***.


#### <a name="mlscoringregionxpipeline"></a>*MLScoringRegionXPipeline*

Ces [pipelines](data-factory\data-factory-create-pipelines.md) contiennent plusieurs activités et dont le résultat final est les évaluées prévisions à partir de l’expérience d’apprentissage automatique Azure associé à ce modèle de solution. Ils sont presque identiques sauf chacun d’eux gère uniquement la zone différente qui est faite par différent RegionID passé dans le pipeline de chargeur et le script hive pour chaque région.  
Les activités contenues dans cette sont :
-   Activité de [HDInsightHive](data-factory\data-factory-hive-activity.md) à l’aide d’un [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) qui exécute un script Hive pour effectuer des agrégations et ingénierie fonctionnalité nécessaire à l’expérience d’apprentissage automatique Azure. Les scripts Hive pour cette tâche sont respectifs ***PrepareMLInputRegionX.hql***.

-   Activité de [copie](https://msdn.microsoft.com/library/azure/dn835035.aspx) qui déplace les résultats de l’activité [HDInsightHive](data-factory\data-factory-hive-activity.md) vers blob Azure stockage unique qui peut être accès par l’activité [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) .

-   Activité de [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) qui appelle l’apprentissage automatique Azure tester les résultats dans les résultats dans un seul stockage Azure blob.

#### <a name="copyscoredresultregionxpipeline"></a>*CopyScoredResultRegionXPipeline*
Ces [pipelines](data-factory\data-factory-create-pipelines.md) contiennent une seule activité - une activité de [copie](https://msdn.microsoft.com/library/azure/dn835035.aspx) qui déplace les résultats de l’expérience d’apprentissage automatique Azure de la respectifs ***MLScoringRegionXPipeline*** vers la base de données SQL Azure qui a été mis en service dans le cadre de l’installation de modèle de solution.

#### <a name="copyaggdemandpipeline"></a>*CopyAggDemandPipeline*
Cette [pipelines](data-factory\data-factory-create-pipelines.md) contiennent une seule activité - une activité de [copie](https://msdn.microsoft.com/library/azure/dn835035.aspx) qui déplace les données à la demande en cours agrégé de ***LoadHistoryDemandDataPipeline*** à la base de données SQL Azure qui a été mis en service dans le cadre de l’installation de modèle de solution.

#### <a name="copyregiondatapipeline-copysubstationdatapipeline-copytopologydatapipeline"></a>*CopyRegionDataPipeline, CopySubstationDataPipeline, CopyTopologyDataPipeline*
Ces [pipelines](data-factory\data-factory-create-pipelines.md) contiennent une seule activité - une activité de [copie](https://msdn.microsoft.com/library/azure/dn835035.aspx) qui déplace les données de référence de sous-centrale/région/Topologygeo sont téléchargés vers blob Azure stockage dans le cadre de l’installation du modèle de solution à la base de données SQL Azure qui a été mis en service dans le cadre de l’installation de modèle de solution.

### <a name="azure-machine-learning"></a>Apprentissage automatique Azure
L’expérience [d’Apprentissage automatique Azure](https://azure.microsoft.com/services/machine-learning/) utilisé pour ce modèle de solution fournit la prévision de la demande de région. L’expérience est spécifique à l’ensemble de données consommée et aura donc besoin modification ou remplacement spécifique aux données qui sont affiche dans.

## <a name="monitor-progress"></a>**Surveiller l’avancement**
Une fois que le Générateur de données est lancé, le pipeline de commence à obtenir hydraté et commencent à différents composants de votre solution de frappe en action suivant les commandes émises par le Factory de données. Il existe deux façons, vous pouvez surveiller le pipeline.

1. Vérifiez les données depuis le stockage Blob Azure.

    Une de la tâche de flux de données Analytique écrit les données brutes entrantes dans stockage d’objets blob. Si vous cliquez sur composant de **Stockage d’objets Blob Azure** de votre solution à partir de l’écran vous correctement déployé la solution et cliquez sur **Ouvrir** dans le volet de droite, il vous faudra au [portail de gestion Azure](https://portal.azure.com). Une fois, cliquez sur **des objets BLOB**. Dans l’écran suivant, vous verrez une liste de conteneurs. Cliquez sur **« energysadata »**. Dans l’écran suivant, vous verrez le dossier **« demandongoing »** . Dans le dossier rawdata, vous voyez des dossiers avec des noms comme date = 2016-01-28 etc.. Si vous voyez ces dossiers, elle indique que les données brutes soient correctement générée sur votre ordinateur et stockées dans le stockage blob. Vous devriez voir les fichiers qui sont autorisés à déterminée taille en Mo dans ces dossiers.

2. Vérifier les données à partir de la base de données SQL Azure.

    La dernière étape du pipeline consiste à écrire des données (par exemple, des prévisions à partir d’apprentissage automatique) dans la base de données SQL. Vous devrez peut-être patienter un maximum de 2 heures pour les données s’affichent dans la base de données SQL. Un moyen de contrôler la quantité de données est disponible dans votre base de données SQL est via le [portail de gestion Azure](https://manage.windowsazure.com/). Dans le volet gauche, recherchez bases de données SQL![](media\cortana-analytics-technical-guide-demand-forecast\SQLicon2.png) et cliquez dessus. Ensuite, recherchez votre base de données (c'est-à-dire demo123456db) et cliquez dessus. Dans la page suivante, sous la section **« Se connecter à votre base de données »** , cliquez sur **« Requêtes exécuter Transact-SQL sur votre base de données SQL »**.

    Dans ce cas, vous pouvez cliquer sur Nouvelle requête et pour rechercher le nombre de lignes (par exemple, « select count à partir de DemandRealHourly) » au fur et à mesure du votre base de données, le nombre de lignes dans la table doit augmenter.)

3. Vérifier les données à partir du tableau de bord Power BI.

    Vous pouvez configurer du tableau de bord Power BI chemin réactif pour analyser les données brutes entrantes. Suivez les instructions dans la section « Tableau de bord Power BI ».



## <a name="power-bi-dashboard"></a>**Tableau de bord Power BI**

### <a name="overview"></a>Vue d’ensemble

Cette section décrit comment configurer le tableau de bord Power BI pour visualiser vos données en temps réel à partir d’analytique flux Azure (chemin réactif), ainsi que des prévisions à partir d’Azure d’apprentissage automatique (chemin d’accès à froid).


### <a name="setup-hot-path-dashboard"></a>Tableau de bord d’installation chemin réactif

Les étapes suivantes vous aidera à visualiser la sortie des données en temps réel à partir de tâches de flux Analytique qui ont été générés au moment de déploiement de la solution. Un compte [En ligne de Power BI](http://www.powerbi.com/) est requis pour effectuer les étapes suivantes. Si vous n’avez pas un compte, vous pouvez [créer un](https://powerbi.microsoft.com/pricing).

1.  Ajoutez sortie de Power BI dans Azure flux Analytique (ASA).

    -  Vous devez suivre les instructions dans [Azure flux Analytique et Power BI : un tableau de bord analytique en temps réel pour la visibilité des données de flux de données en temps réel](stream-analytics-power-bi-dashboard.md) pour configurer le résultat de votre travail Azure flux Analytique en tant que votre tableau de bord Power BI.

    - Recherchez la tâche analytique flux dans votre [portail de gestion Azure](https://manage.windowsazure.com). Le nom de la tâche doit être : YourSolutionName + streamingjob » » + nombre aléatoire + « asapbi » (c'est-à-dire demostreamingjob123456asapbi).

    - Ajouter une sortie PowerBI pour le travail ASA. Définir l' **Alias de sortie** comme **« PBIoutput »**. Définir votre **Nom de jeu de données** et le **nom de la Table** en tant que **« EnergyStreamData »**. Une fois que vous avez ajouté la sortie, cliquez sur **« Démarrer »** dans la partie inférieure de la page pour démarrer la tâche de flux de données Analytique. Vous devez obtenir un message de confirmation (*par exemple*, « Départ flux analytique travail myteststreamingjob12345asablob est a réussi »).

2. Se connecter à [Power BI en ligne](http://www.powerbi.com)

    -   Dans le volet gauche section jeux de données dans mon espace de travail, vous devez être en mesure de voir un nouveau jeu de données affichant dans le volet gauche de Power BI. Il s’agit le flux des données que vous poussé à partir d’Azure flux Analytique à l’étape précédente.

    -   Vérifiez que le volet de ***visualisation*** est ouvert et s’affiche sur le côté droit de l’écran.

3. Créer la vignette « À la demande par horodatage » :
    -   Cliquez sur jeu de données **« EnergyStreamData »** dans le volet gauche section jeux de données.

    -   Cliquez sur **« graphique en courbes »** icône ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic8.png).

    -   Cliquez sur « EnergyStreamData » dans le panneau de configuration de **champs** .

    -   Cliquez sur **« Horodatage »** et vérifiez qu’il s’affiche sous « Axes ». Cliquez sur **« Charger »** et vérifiez qu’il s’affiche sous « Valeurs ».

    -   Cliquez sur **Enregistrer** dans la partie supérieure et nommez le rapport en tant que « EnergyStreamDataReport ». Le rapport nommé « EnergyStreamDataReport » est affiché dans la section rapports dans le volet de navigation gauche.

    -   Cliquez sur **« épingler visuel »** ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic6.png) icône dans le coin supérieur droit de ce graphique en courbes, une fenêtre « Épingler au tableau de bord » peut s’afficheront vous permet de choisir un tableau de bord. Sélectionnez « EnergyStreamDataReport », puis cliquez sur « Épingler ».

    -   Faites glisser la souris sur ce titre dans le tableau de bord, cliquez sur « Modifier » icône dans le coin supérieur droit pour modifier son titre comme « À la demande par horodatage »

4.  Créer des autres vignettes du tableau de bord en fonction des groupes de données appropriées. L’affichage tableau de bord final est indiqué ci-dessous.
        ![](media\cortana-analytics-technical-guide-demand-forecast\PBIFullScreen.png)


### <a name="setup-cold-path-dashboard"></a>Tableau de bord de chemin d’accès à froid le programme d’installation
Dans le pipeline de données de chemin d’accès à froid, l’objectif essentiel consiste à obtenir la prévision à la demande de chaque région. Power BI se connecte à une base de données SQL Azure comme source de données, où se trouvent les résultats de la prévision.

> [AZURE.NOTE] 1) prend quelques heures collecter suffisamment prévision les résultats du tableau de bord. Nous vous recommandons de que vous lancer ce processus 2-3 heures une fois que vous déjeuner le Générateur de données. (2) dans cette étape, la condition préalable consiste à télécharger et installer le logiciel gratuit [Power BI desktop](https://powerbi.microsoft.com/desktop).



1.  Obtenir les informations d’identification de base de données.

    Vous devez le **nom du serveur de base de données, nom de la base de données, nom d’utilisateur et mot de passe** avant de passer aux étapes suivantes. Voici les étapes pour vous aider comment retrouver facilement.

    -   Une fois que **« de base de données SQL Azure »** dans votre diagramme de modèle de solution s’affiche en vert, cliquez dessus, puis cliquez sur **« Ouvrir »**. Vous serez guidé au portail de gestion Azure et votre page d’informations de base de données s’ouvre également.

    -   Dans la page, vous pouvez trouver une section « Base de données ». Il répertorie la base de données que vous avez créé. Le nom de votre base de données doit être **« Votre nom de la Solution nombre aléatoire + 'db' »** (par exemple, « mytest12345db »).

    -   Cliquez sur base de données, dans la nouvelle fenêtre indépendante pannel, vous pouvez trouver le nom de votre serveur de base de données dans la partie supérieure. Votre devraient de nom de nom de serveur de base de données comme **« Votre nom de la Solution nombre aléatoire + 'database.windows.net,1433' »** (par exemple, « mytest12345.database.windows.net,1433 »).

    -   Votre base de données **nom d’utilisateur** et **mot de passe** sont les mêmes que le nom d’utilisateur et mot de passe enregistré précédemment au cours de déploiement de la solution.

2.  Mise à jour de la source de données du fichier Power BI froid chemin d’accès
    -  Vérifiez que vous avez installé la dernière version de [bureau de Power BI](https://powerbi.microsoft.com/desktop).

    -   Dans le dossier **« DemandForecastingDataGeneratorv1.0 »** que vous avez téléchargé, double-cliquez sur le fichier **« Power BI Template\DemandForecastPowerBI.pbix »** . Les visualisations initiales sont basées sur des données factices. **Remarque :** Si vous voyez une erreur massage, vérifiez que vous avez installé la dernière version de Power BI Desktop.

        Une fois que vous ouvrez, dans la partie supérieure du fichier, cliquez sur **Modifier les requêtes**. Dans la fenêtre indépendante, double-cliquez sur **« Source »** dans le volet de droite.
    ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic1.png)

    -   Dans la fenêtre indépendante, remplacez **« Server »** et **« Database »** par votre propre nom du serveur et de base de données, puis cliquez sur **« OK »**. Nom du serveur, vérifiez que vous spécifiez le port 1433 (**YourSolutionName.database.windows.net, 1433**). Ignorer les messages d’avertissement qui s’affichent à l’écran.

    -   Dans la fenêtre suivante indépendante fenêtre, vous verrez deux options dans le volet gauche (**Windows** et **base de données**). Cliquez sur **« Base de données »**, renseignez vos **« Username »** et **« Mot de passe »** (il s’agit du nom d’utilisateur et mot de passe entré lors de la première déployé la solution et de la création d’une base de données SQL Azure). Dans ***Sélectionnez le niveau à appliquer ces paramètres***, case d’option de niveau de base de données. Cliquez ensuite sur **« Connexion »**.

    -   Une fois que vous êtes guidé revenir à la page précédente, fermez la fenêtre. Un message s’affiche out - cliquez sur **Appliquer**. Enfin, cliquez sur le bouton **Enregistrer** pour enregistrer les modifications. Votre fichier de Power BI a maintenant déterminé connexion au serveur. Si votre visualisations sont vides, veillez à que désactiver les options de visualisation pour visualiser toutes les données en cliquant sur l’icône de gomme dans le coin supérieur droit de légendes. Utilisez le bouton Actualiser pour refléter les nouvelles données sur les visualisations. Au départ, seuls apparaissent les données de départ sur vos visualisations que le factory de données est prévu pour actualiser toutes les 3 heures. Après 3 heures, vous verrez nouvelles prévisions reflétées dans vos visualisations lorsque vous actualisez les données.

3. (Facultatif) Publier le tableau de bord du chemin d’accès à froid sur [Power BI en ligne](http://www.powerbi.com/). Notez que cette étape a besoin d’un compte de Power BI (ou compte Office 365).

    -   Cliquez sur **« Publier »** et quelques secondes plus tard une fenêtre apparaît et affiche « Publication sur Power BI succès ! » avec une coche verte. Cliquez sur le lien situé sous « Demoprediction.pbix ouvert dans Power BI ». Pour trouver des instructions détaillées, voir [publier à partir de Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).

    -   Pour créer un tableau de bord : cliquez sur le **+** signe en regard de la section **tableaux de bord** dans le volet gauche. Entrez le nom « Démo de réaliser des prévisions à la demande » de ce tableau de bord.

    -   Une fois que vous ouvrez le rapport, cliquez sur ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic6.png) pour épingler toutes les visualisations à votre tableau de bord. Pour trouver des instructions détaillées, voir [épingler une vignette pour un tableau de bord Power BI à partir d’un rapport](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
        Accédez à la page de tableau de bord et ajuster la taille et l’emplacement de votre visualisations et modifier les titres. Pour trouver des instructions détaillées sur la façon de modifier vos vignettes, voir [Modifier une vignette--redimensionner, déplacer, renommer, code confidentiel, supprimer, ajouter un lien hypertexte](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Voici un tableau de bord exemple avec certaines visualisations chemin d’accès à froid épinglées à celui-ci.

        ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic7.png)

4. (Facultatif) Planifier l’actualisation de la source de données.
    -     Pour planifier l’actualisation des données, pointez votre souris sur le dataset **EnergyBPI Final** , cliquez sur ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic3.png) puis cliquez sur **Planifier l’actualisation**.
    **Remarque :** Si vous voyez un message d’avertissement, cliquez sur **Modifier les informations d’identification** et vérifiez que vos informations d’identification de base de données sont les mêmes que celles qui sont décrites dans l’étape 1.

    ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic4.png)

    -   Développez la section **Planifier l’actualisation** . Activez l’option « conserver vos données à jour ».

    -   Planifier l’actualisation selon vos besoins. Pour trouver plus d’informations, voir [l’actualisation des données dans Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/).


## <a name="how-to-delete-your-solution"></a>**Comment supprimer votre solution**
Vérifiez qu’arrêter le Générateur de données lorsque vous N'utilisez pas activement la solution en exécutant le Générateur de données implique des frais plus élevés. Si vous ne l’utilisez pas, supprimez la solution. Votre solution vous supprimez tous les composants mis en service dans votre abonnement lorsque vous avez déployé la solution. Pour supprimer la solution, cliquez sur le nom de votre solution dans le volet gauche du modèle de solution et cliquez sur Supprimer.

## <a name="cost-estimation-tools"></a>**Outils d’Estimation des coûts**

Les deux outils suivants sont disponibles pour vous aider à mieux comprendre les coûts totaux impliqués dans l’exécution la prévision de la demande pour le modèle de Solution énergétique dans votre abonnement :

-   [Microsoft Azure coût outil estimateur (en ligne)](https://azure.microsoft.com/pricing/calculator/)

-   [Microsoft Azure coût estimateur outil (bureau)](http://www.microsoft.com/download/details.aspx?id=43376)

## <a name="acknowledgements"></a>**Accusés de réception**
Cet article est créé par scientifique données Yijing Chen et technicien logiciel Qiu Min à Microsoft.
