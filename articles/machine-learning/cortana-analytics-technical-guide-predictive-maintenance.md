<properties
    pageTitle="Guide technique au modèle de Solution Intelligence Cortana pour maintenance prédictive dans aerospace et d’autres entreprises | Microsoft Azure"
    description="Guide technique au modèle de Solution avec Microsoft Cortana Intelligence pour maintenance prédictive dans aerospace, utilitaires et transport."
    services="cortana-analytics"
    documentationCenter=""
    authors="fboylu"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cortana-analytics"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="fboylu" />

# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-predictive-maintenance-in-aerospace-and-other-businesses"></a>Guide technique au modèle de Solution Intelligence Cortana pour maintenance prédictive dans aerospace et d’autres entreprises

## <a name="acknowledgements"></a>**Accusés de réception**
Cet article est créé par scientifiques données Yan Zhang, Gauher Shaheen, Fidan Boylu Uz et technicien logiciel Dan Grecoe auprès de Microsoft.

## <a name="overview"></a>**Vue d’ensemble**

Modèles de solutions sont conçues pour accélérer le processus de création d’une démonstration E2E en haut de gamme d’Intelligence Cortana. Un modèle déployé sera mise en service de votre abonnement avec des composants Cortana Intelligence nécessaires et créer les relations entre elles. Il est également basée sur le pipeline de données avec les données d’exemple générées à partir d’une application de générateur de données qui vous serez télécharger et installer sur votre ordinateur local une fois que vous déployez le modèle de solution. Les données générées à partir du générateur seront hydrate le pipeline de données et commencer à générer des prévisions d’apprentissage machine qui peuvent ensuite être affichées dans le tableau de bord Power BI. Le processus de déploiement vous guidera tout au long de plusieurs étapes pour configurer vos informations d’identification de la solution. Vérifiez que vous enregistrez ces informations d’identification telles que le nom de la solution, nom d’utilisateur et mot de passe que vous fournissez durant le déploiement.  

L’objectif de ce document est pour décrire l’architecture de référence et les différents composants mis en service dans votre abonnement dans le cadre de ce modèle de solution. Le document parle également comment remplacer les exemples de données avec des données réelles de votre propre à être en mesure de voir des idées et des prévisions à partir de vos propres données. En outre, le document aborde les parties du modèle de Solution qui devront être modifié si vous souhaitez personnaliser la solution avec vos propres données. Vous trouverez des instructions sur la façon de créer le tableau de bord Power BI pour ce modèle de Solution à la fin.

>[AZURE.TIP] Vous pouvez télécharger et imprimer une [version PDF de ce document](http://download.microsoft.com/download/F/4/D/F4D7D208-D080-42ED-8813-6030D23329E9/cortana-analytics-technical-guide-predictive-maintenance.pdf).

## <a name="big-picture"></a>**Vue d’ensemble**

![](media/cortana-analytics-technical-guide-predictive-maintenance\predictive-maintenance-architecture.png)

Lorsque la solution est déployée, les différents services Azure au sein de Cortana Analytique Suite sont activées (*c'est-à-dire* Hub de l’événement, flux Analytique, HDInsight, Data Factory, apprentissage automatique, *etc.*). Le diagramme architecture ci-dessus indique, à un niveau élevé, comment la Maintenance prédictive pour aéronautique Solution modèle est créée à partir de bout en bout. Vous ne pourrez pas étudier ces services dans le portail azure en cliquant dessus sur le diagramme de modèle de solution créé avec le déploiement de la solution à l’exception de HDInsight, comme ce service est mis en service à la demande lorsque les activités pipeline associées sont requises pour exécuter et supprimées par la suite.
Vous pouvez télécharger une [version en taille normale du diagramme](http://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

Les sections suivantes décrivent chaque élément.

## <a name="data-source-and-ingestion"></a>**Réception et source de données**

### <a name="synthetic-data-source"></a>Source de données de synthèse

Pour ce modèle de la source de données utilisée est générée à partir d’une application de bureau que vous allez télécharger et exécuter localement après le déploiement réussi. Vous trouverez les instructions pour télécharger et installer cette application dans la barre de propriétés lorsque vous sélectionnez le premier nœud appelé prédictive Générateur de données de Maintenance sur le diagramme de modèle de solution. Cette application flux le service [Azure événement concentrateur](#azure-event-hub) avec points de données ou des événements, qui seront utilisées dans le reste du flux de solution. Cette source de données est constituée ou dérivée de données accessibles à partir du [référentiel de données NASA](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/) en utilisant le [jeu de données à double flux moteur dégradation Simulation](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan).

L’application de génération événement insère le Hub événement Azure uniquement pendant qu’il s’exécute sur votre ordinateur.

### <a name="azure-event-hub"></a>Concentrateur événement Azure

Le service [Azure événement concentrateur](https://azure.microsoft.com/services/event-hubs/) est le destinataire de l’entrée fournie par la Source de données synthétique ci-dessus.

## <a name="data-preparation-and-analysis"></a>**Analyse et préparation des données**


### <a name="azure-stream-analytics"></a>Flux de données Azure Analytique

Le service [Azure flux Analytique](https://azure.microsoft.com/services/stream-analytics/) est utilisé pour fournir près analytique en temps réel sur le flux d’entrée à partir du service [Azure événement concentrateur](#azure-event-hub) et publier des résultats dans un tableau de bord [Power BI](https://powerbi.microsoft.com) , ainsi que l’archivage de tous les événements entrants brutes au service de [Stockage Azure](https://azure.microsoft.com/services/storage/) pour un traitement ultérieur par le service [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) .

### <a name="hd-insights-custom-aggregation"></a>Agrégation personnalisée HD perspectives

Le service Azure HD aperçu est utilisé pour exécuter des scripts [ruche](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) (déclenchés par Azure Data Factory) afin de fournir des agrégations sur les événements brutes qui ont été archivés au moyen du service Azure flux Analytique.

### <a name="azure-machine-learning"></a>Apprentissage automatique Azure

Utilisation du service [d’Apprentissage automatique Azure](https://azure.microsoft.com/services/machine-learning/) (déclenché par Azure Data Factory) pour réaliser des prévisions sur la durée d’utilité restante (RUL) d’un moteur d’avion particulier étant donné les entrées reçues.

## <a name="data-publishing"></a>**Publication de données**


### <a name="azure-sql-database-service"></a>Service de base de données SQL Azure

Le service de [Base de données SQL Azure](https://azure.microsoft.com/services/sql-database/) permet de stocker (gérés par Azure Data Factory) les prévisions reçues par le service d’apprentissage automatique Azure qui est consommé dans le tableau de bord [Power BI](https://powerbi.microsoft.com) .

## <a name="data-consumption"></a>**Consommation de données**

### <a name="power-bi"></a>Power BI

Le service [Power BI](https://powerbi.microsoft.com) est utilisé pour afficher un tableau de bord qui contient des agrégations et alertes fournies par le service [Azure flux Analytique](https://azure.microsoft.com/services/stream-analytics/) ainsi que les prévisions RUL stockées dans la [Base de données SQL Azure](https://azure.microsoft.com/services/sql-database/) qui ont été produites au moyen du service [d’Apprentissage automatique Azure](https://azure.microsoft.com/services/machine-learning/) . Pour obtenir des Instructions sur la façon de créer le tableau de bord Power BI pour ce modèle de Solution, reportez-vous à la section ci-dessous.

## <a name="how-to-bring-in-your-own-data"></a>**Comment faire pour importer vos données**

Cette section décrit comment importer vos propres données Azure, et quels sont les domaines de verser modifications pour les données que vous importez dans cette architecture.

Il est probable qu’un dataset que vous amener correspond le jeu de données utilisée par le [jeu de données à double flux moteur dégradation Simulation](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan) utilisé pour ce modèle de solution. Présentation de vos données et la configuration requise sera crucial pour comment vous modifiez ce modèle pour l’utiliser avec vos propres données. S’il s’agit de votre première exposition au service apprentissage automatique Azure, vous pouvez obtenir une introduction à celui-ci à l’aide de l’exemple de la rubrique [comment créer votre première expérience](machine-learning-create-experiment.md).

Les sections suivantes aborderons les sections du modèle qui requièrent des modifications lors de l’introduction d’un nouveau groupe de données.

### <a name="azure-event-hub"></a>Concentrateur événement Azure

Le service Azure événement concentrateur est très générique, telles que les données peuvent être validées dans le hub au format CSV ou JSON. Aucun traitement spécial se produit dans le Hub événement Azure, mais il est important de comprendre les données qui sont chargées dans celle-ci.

Ce document ne décrit pas comment faire pour ajouter vos données, mais vous pouvez facilement envoyer événements ou des données à un concentrateur événement Azure à l’aide de l’API de concentrateur événement.

### <a name="azure-stream-analytics"></a>Flux de données Azure Analytique

Le service Azure flux Analytique sert à fournir près analytique en temps réel en lisant à partir de flux de données et de sortie des données vers n’importe quel nombre de sources.

La maintenance prédictive pour aéronautique Solution modèle, la requête Azure flux Analytique est constitué de quatre sous-requêtes, chaque événements beaucoup à partir du service Azure événement concentrateur et avoir des sorties à quatre emplacements distincts. Ces sorties composent de trois jeux de données Power BI et un emplacement de stockage Azure.

La requête Azure flux Analytique soient retrouvée en :

-   Se connecter au portail Azure

-   Vous avez trouvé les travaux analytique flux ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-stream-analytics.png) qui ont été générées lors du déployée de la solution (*par exemple*, **maintenancesa02asapbi** et **maintenancesa02asablob** pour la solution de gestion prédictive)

-   Sélection

    -   ***Entrées*** pour afficher l’entrée de requête

    -   ***Requête*** pour afficher la requête elle-même

    -   ***EXPORTE*** pour afficher les différentes sorties

Vous trouverez des informations à propos de construction d’une requête Analytique de flux de données Azure dans la [Référence de requête Analytique flux](https://msdn.microsoft.com/library/azure/dn834998.aspx) sur MSDN.

Dans cette solution, les requêtes de sortie trois jeux de données avec près informations analytique en temps réel sur le flux de données entrant à un tableau de bord Power BI est fourni dans le cadre de ce modèle de solution. Car il existe une connaissance implicite sur le format de données entrants, ces requêtes devront être modifiées en fonction de votre format de données.

La requête dans le second flux analytique travail **maintenancesa02asablob** simplement renvoie tous les événements [d’Événement concentrateur](https://azure.microsoft.com/services/event-hubs/) au [Stockage Azure](https://azure.microsoft.com/services/storage/) et ne requiert, par conséquent, aucune modification quel que soit votre format de données comme les informations d’événement complète sont en continu au stockage.

### <a name="azure-data-factory"></a>Données Azure usine

Le service [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) orchestre le mouvement et le traitement des données. Dans la mise à jour prédictive pour aéronautique Solution modèle le factory de données est composé de trois [pipelines](../data-factory/data-factory-create-pipelines.md) déplacer et traiter les données à l’aide de différentes technologies.  Vous pouvez accéder à votre usine de données en ouvrant la le nœud Data Factory en bas du diagramme de modèle de solution créé avec le déploiement de la solution. Vous accédez alors à l’usine de données sur votre portail Azure. Si vous rencontrez des erreurs sous jeux de vos données, vous pouvez ignorer ceux qu’ils sont en raison d’usine données déployée avant le Générateur de données a été démarré. Ces erreurs n’empêchent pas votre usine de données de fonctionner.

![](media/cortana-analytics-technical-guide-predictive-maintenance/data-factory-dataset-error.png)

Cette section décrit les nécessaires [pipelines](../data-factory/data-factory-create-pipelines.md) et les [activités](../data-factory/data-factory-create-pipelines.md) contenues dans [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Voici la vue de diagramme de la solution.

![](media/cortana-analytics-technical-guide-predictive-maintenance/azure-data-factory.png)

Deux des pipelines de cette usine contiennent des scripts [ruche](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) qui sont utilisés pour partition et agréger les données. Une fois que noté, les scripts seront situées dans le compte de [Stockage Azure](https://azure.microsoft.com/services/storage/) créé pendant l’installation. Leur emplacement sera : maintenancesascript\\\\script\\\\hive\\ \\ (ou https://[Your solution name].blob.core.windows.net/maintenancesascript).

Comme pour les requêtes [Azure flux Analytique](#azure-stream-analytics-1) , les scripts [ruche](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) que connaissance implicite sur le format de données entrants, ces requêtes devront être modifiées en fonction de vos besoins en matière de [ingénierie fonctionnalité](machine-learning-feature-selection-and-engineering.md) et le format des données.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*

Ce [pipeline](../data-factory/data-factory-create-pipelines.md) contient une seule activité - une activité [HDInsightHive](../data-factory/data-factory-hive-activity.md) à l’aide d’un [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) qui exécute un script [ruche](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) partition les données placer dans le [Stockage Azure](https://azure.microsoft.com/services/storage/) lors de la tâche [Azure flux Analytique](https://azure.microsoft.com/services/stream-analytics/) .

Le script [ruche](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) pour cette tâche partition est ***AggregateFlightInfo.hql***

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*

Ce [pipeline](../data-factory/data-factory-create-pipelines.md) contient plusieurs activités et dont le résultat final est que tester les prévisions à partir de l' [Apprentissage automatique Azure](https://azure.microsoft.com/services/machine-learning/) évaluées associé à ce modèle de solution.

Les activités contenues dans cette sont :

-   Activité de [HDInsightHive](../data-factory/data-factory-hive-activity.md) à l’aide d’un [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) qui exécute un script [ruche](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) pour effectuer des agrégations et ingénierie fonctionnalité nécessaire à l’expérience [d’Apprentissage automatique Azure](https://azure.microsoft.com/services/machine-learning/) .
    Le script [ruche](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) pour cette tâche partition est ***PrepareMLInput.hql***.

-   Activité de [copie](https://msdn.microsoft.com/library/azure/dn835035.aspx) qui déplace les résultats de l’activité [HDInsightHive](../data-factory/data-factory-hive-activity.md) vers blob [Azure stockage](https://azure.microsoft.com/services/storage/) unique qui peut être accès par l’activité [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) .

-   Activité de [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) qui appelle l' [Apprentissage automatique Azure](https://azure.microsoft.com/services/machine-learning/) tester entraînant dans les résultats dans un seul blob [Azure stockage](https://azure.microsoft.com/services/storage/) .

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*

Ce [pipeline](../data-factory/data-factory-create-pipelines.md) contient une seule activité - une activité de [copie](https://msdn.microsoft.com/library/azure/dn835035.aspx) qui déplace que les résultats de l' [Apprentissage automatique Azure](#azure-machine-learning) essayer à partir de la ***MLScoringPipeline*** à la [Base de données SQL Azure](https://azure.microsoft.com/services/sql-database/) qui a été mis en service dans le cadre de l’installation de modèle de solution.

### <a name="azure-machine-learning"></a>Apprentissage automatique Azure

L’expérience [d’Apprentissage automatique Azure](https://azure.microsoft.com/services/machine-learning/) utilisé pour ce modèle de solution fournit la restant utile vie (RUL) un moteur d’avion. L’expérience est spécifique à l’ensemble de données consommée et aura donc besoin modification ou remplacement spécifique aux données qui sont affiche dans.

Pour plus d’informations sur les modalités de création de l’expérience d’apprentissage automatique Azure, consultez [Maintenance prédictive : étape 1 sur 3, préparer les données et ingénierie fonctionnalité](http://gallery.cortanaanalytics.com/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2).

## <a name="monitor-progress"></a>**Surveiller l’avancement**
 Une fois que le Générateur de données est lancé, le pipeline de commence à obtenir hydraté et commencent à différents composants de votre solution de frappe en action suivant les commandes émises par le Factory de données. Il existe deux façons, vous pouvez surveiller le pipeline.

1. Une de la tâche de flux de données Analytique écrit les données brutes entrantes dans stockage d’objets blob. Si vous cliquez sur composant stockage Blob de votre solution à partir de l’écran vous correctement déployé la solution et puis cliquez sur Ouvrir dans le volet de droite, il vous faudra au [portail de gestion](https://portal.azure.com/). Une fois, cliquez sur des objets BLOB. Dans l’écran suivant, vous verrez une liste de conteneurs. Cliquez sur **maintenancesadata**. Dans l’écran suivant, vous verrez le dossier **rawdata** . Dans le dossier rawdata, vous verrez des dossiers avec des noms tels que les heures = 17, heure = etc. 18. Si vous voyez ces dossiers, elle indique que les données brutes soient correctement générée sur votre ordinateur et stockées dans le stockage blob. Vous devriez voir fichiers csv qui sont autorisés à déterminée taille en Mo dans ces dossiers.

2. La dernière étape du pipeline consiste à écrire des données (par exemple, des prévisions à partir d’apprentissage automatique) dans la base de données SQL. Vous devrez peut-être patienter un maximum de trois heures pour les données s’affichent dans la base de données SQL. Un moyen de contrôler la quantité de données est disponible dans votre base de données SQL est via [azure portail](https://manage.windowsazure.com/). Dans le volet gauche, recherchez bases de données SQL ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-SQL-databases.png) et cliquez dessus. Ensuite, recherchez votre base de données **pmaintenancedb** et cliquez dessus. Dans la page suivante dans la partie inférieure, cliquez sur Gérer

    ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-manage.png).

    Ici, vous pouvez cliquer sur Nouvelle requête et pour rechercher le nombre de lignes (par exemple, sélectionnez Count à partir de PMResult). Au fur et à mesure du votre base de données, le nombre de lignes dans la table doit augmenter.


## <a name="power-bi-dashboard"></a>**Tableau de bord Power BI**

### <a name="overview"></a>Vue d’ensemble

Cette section décrit comment configurer des tableaux de bord Power BI pour visualiser vos données en temps réel à partir d’analytique flux Azure (chemin réactif), ainsi que les résultats des prévisions lot à partir d’Azure d’apprentissage automatique (chemin d’accès à froid).

### <a name="setup-cold-path-dashboard"></a>Tableau de bord de chemin d’accès à froid le programme d’installation

Dans le pipeline de données chemin d’accès à froid, l’objectif essentiel consiste à obtenir la RUL prédictive (durée de vie utile restante) de chaque moteur d’avion une fois qu’elle termine à un vol (cycle). Le résultat de prévision est mis à jour toutes les 3 heures pour prévoir les moteurs qui ont terminé un vol pendant les heures passées 3.

Power BI se connecte à une base de données SQL Azure comme source de données, où se trouvent les résultats de la prévision. Remarque : 1) au moment de déploiement de votre solution, une prévision réelle apparaît dans la base de données dans les 3 heures.
Le fichier pbix qui accompagnent le téléchargement générateur contenait des données de valeur de départ afin que vous pouvez créer le tableau de bord Power BI immédiatement. (2) dans cette étape, la condition préalable consiste à télécharger et installer le logiciel gratuit [Power BI desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/).

Les étapes suivantes vous guide sur la connexion le fichier pbix à la base de données SQL qui a été lancé au moment de déploiement de solution contenant des données (*par exemple*. résultats de la prévision) pour la visualisation.

1.  Obtenir les informations d’identification de base de données.

    Vous devez le **nom du serveur de base de données, nom de la base de données, nom d’utilisateur et mot de passe** avant de passer aux étapes suivantes. Voici les étapes pour vous aider comment retrouver facilement.

    -   Une fois que **« de base de données SQL Azure »** dans votre diagramme de modèle de solution s’affiche en vert, cliquez dessus, puis cliquez sur **« Ouvrir »**.

    -   Vous verrez une nouvel onglet/fenêtre de navigateur qui affiche la page du portail Azure. Dans le volet gauche, cliquez sur **« Groupes de ressources »** .

    -   Sélectionnez l’abonnement que vous utilisez pour déployer la solution, puis **' YourSolutionName\_ResourceGroup'**.

    -   Dans la nouvelle fenêtre indépendante Panneau de configuration, cliquez sur le ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-sql.png) icône pour accéder à votre base de données. Nom de votre base de données est en regard de cette icône (*par exemple*, **« pmaintenancedb »**) et le **nom du serveur de base de données** est répertorié sous la propriété name Server et doit ressembler à **YourSoutionName.database.windows.net**.

    -   Votre base de données **nom d’utilisateur** et **mot de passe** sont les mêmes que le nom d’utilisateur et mot de passe enregistré précédemment au cours de déploiement de la solution.

2.  Mettre à jour la source de données le froid chemin d’accès du fichier de rapport avec Power BI Desktop.

    -   Dans le dossier sur votre PC dans lequel vous avez téléchargé et décompressé le fichier générateur, double-cliquez sur la **PowerBI\\PredictiveMaintenanceAerospace.pbix** fichier. Si vous voyez des messages d’avertissement lorsque vous ouvrez le fichier, les ignorer. Dans la partie supérieure du fichier, cliquez sur **Modifier les requêtes**.

        ![](media\cortana-analytics-technical-guide-predictive-maintenance\edit-queries.png)

    -   Vous verrez deux tables, **RemainingUsefulLife** et **PMResult**. Sélectionnez la première table et cliquez sur ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-query-settings.png) en regard de **« Source »** sous **« étapes appliquées »** dans le volet droit **Les paramètres de requête** . Ignorer les messages d’avertissement qui s’affichent.

    -   Dans la fenêtre indépendante, remplacez **« Serveur »** et **« Database »** par votre propre nom du serveur et de base de données, puis cliquez sur **« OK »**. Nom du serveur, vérifiez que vous spécifiez le port 1433 (**YourSoutionName.database.windows.net, 1433**). Laissez le champ de base de données en tant que **pmaintenancedb**. Ignorer les messages d’avertissement qui s’affichent à l’écran.

    -   Dans la fenêtre suivante indépendante fenêtre, vous verrez deux options dans le volet gauche (**Windows** et **base de données**). Cliquez sur **« Database »**, renseignez vos **« Username »** et **« Password »** (il s’agit du nom d’utilisateur et mot de passe entré lors de la première déployé la solution et de la création d’une base de données SQL Azure). Dans ***Sélectionnez le niveau à appliquer ces paramètres***, case d’option de niveau de base de données. Cliquez ensuite sur **« Se connecter »**.

    -   Cliquez sur la deuxième table **PMResult** , puis cliquez sur ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-navigation.png) en regard de **« Source »** sous **« étapes appliquées »** dans le volet droit **Les paramètres de requête** , cliquez sur OK et mettre à jour les noms de serveur et la base de données comme dans les étapes ci-dessus.

    -   Une fois que vous êtes guidé revenir à la page précédente, fermez la fenêtre. Un message s’affiche out - cliquez sur **Appliquer**. Enfin, cliquez sur le bouton **Enregistrer** pour enregistrer les modifications. Votre fichier de Power BI a maintenant déterminé connexion au serveur. Si votre visualisations sont vides, veillez à que désactiver les options de visualisation pour visualiser toutes les données en cliquant sur l’icône de gomme dans le coin supérieur droit de légendes. Utilisez le bouton Actualiser pour refléter les nouvelles données sur les visualisations. Au départ, seuls apparaissent les données de départ sur vos visualisations que le factory de données est prévu pour actualiser toutes les 3 heures. Après 3 heures, vous verrez nouvelles prévisions reflétées dans vos visualisations lorsque vous actualisez les données.

3.  (Facultatif) Publier le tableau de bord du chemin d’accès à froid sur [Power BI en ligne](http://www.powerbi.com/). Notez que cette étape a besoin d’un compte de Power BI (ou compte Office 365).

    -   Cliquez sur **« Publier »** et quelques secondes plus tard une fenêtre apparaît et affiche « Publication sur Power BI succès ! » avec une coche verte. Cliquez sur le lien situé sous « Ouvrir PredictiveMaintenanceAerospace.pbix dans Power BI ». Pour trouver des instructions détaillées, voir [publier à partir de Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).

    -   Pour créer un tableau de bord : cliquez sur le **+** signe en regard de la section **tableaux de bord** dans le volet gauche. Entrez le nom « Prédictive Maintenance démo » de ce tableau de bord.

    -   Une fois que vous ouvrez le rapport, cliquez sur ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-pin.png) pour épingler toutes les visualisations à votre tableau de bord. Pour trouver des instructions détaillées, voir [épingler une vignette pour un tableau de bord Power BI à partir d’un rapport](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
    Accédez à la page de tableau de bord et ajuster la taille et l’emplacement de votre visualisations et modifier les titres. Pour trouver des instructions détaillées sur la façon de modifier vos vignettes, voir [Modifier une vignette--redimensionner, déplacer, renommer, code confidentiel, supprimer, ajouter un lien hypertexte](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Voici un tableau de bord exemple avec certaines visualisations chemin d’accès à froid épinglées à celui-ci.  En fonction de leur durée d’exécution votre générateur de données, vos numéros sur les visualisations peut être différent.
    <br/>
    ![](media\cortana-analytics-technical-guide-predictive-maintenance\final-view.png)
<br/>
    -   Pour planifier l’actualisation des données, pointez la souris sur le dataset **PredictiveMaintenanceAerospace** , cliquez sur ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-elipsis.png) puis cliquez sur **Planifier l’actualisation**.
<br/>
        **Remarque :** Si vous voyez un message d’avertissement, cliquez sur **Modifier les informations d’identification** et vérifiez que vos informations d’identification de base de données sont les mêmes que celles qui sont décrites dans l’étape 1.
<br/>
    ![](media\cortana-analytics-technical-guide-predictive-maintenance\schedule-refresh.png)
<br/>
    -   Développez la section **Planifier l’actualisation** . Activez l’option « conserver vos données à jour ».
    <br/>
    -   Planifier l’actualisation selon vos besoins. Pour trouver plus d’informations, voir [l’actualisation des données dans Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### <a name="setup-hot-path-dashboard"></a>Tableau de bord d’installation chemin réactif

Les étapes suivantes vous aidera à visualiser la sortie des données en temps réel à partir de tâches de flux Analytique qui ont été générés au moment de déploiement de la solution. Un compte [En ligne de Power BI](http://www.powerbi.com/) est requis pour effectuer les étapes suivantes. Si vous n’avez pas un compte, vous pouvez [créer un](https://powerbi.microsoft.com/pricing).

1.  Ajoutez sortie de Power BI dans Azure flux Analytique (ASA).

    -  Vous devez suivre les instructions dans [Azure flux Analytique et Power BI : un tableau de bord analytique en temps réel pour la visibilité des données de flux de données en temps réel](stream-analytics-power-bi-dashboard.md) pour configurer le résultat de votre travail Azure flux Analytique en tant que votre tableau de bord Power BI.
    - La requête ASA a trois sorties qui sont **aircraftmonitor**, **aircraftalert**et **flightsbyhour**. Vous pouvez afficher la requête en cliquant sur l’onglet requête. Correspondant à chacune de ces tables, vous devrez ajouter une sortie vers ASA. Lorsque vous ajoutez le premier résultat (*par exemple,* **aircraftmonitor**) Vérifiez que **l’Alias de sortie**, le **Nom du jeu de données** et le **Nom de la Table** sont les mêmes (**aircraftmonitor**). Répétez les étapes pour ajouter des sorties pour **aircraftalert**et **flightsbyhour**. Une fois que vous avez ajouté les trois tables de sortie et a démarré la tâche ASA, vous devez obtenir un message de confirmation (*par exemple*, « Départ flux analytique travail maintenancesa02asapbi est a réussi »).

2. Se connecter à [Power BI en ligne](http://www.powerbi.com)

    -   Dans le volet gauche section jeux de données dans mon espace de travail, les noms **aircraftmonitor**, **aircraftalert**et **flightsbyhour** ***DATASET*** doivent apparaître. Il s’agit le flux des données que vous poussé à partir d’Azure flux Analytique à l’étape précédente. Le jeu de données **flightsbyhour** n’apparaîtra pas en même temps que d’autres jeux de deux données en raison de la nature de la requête SQL derrière lui. Toutefois, il doit s’afficher après une heure.
    -   Vérifiez que le volet de ***visualisation*** est ouvert et s’affiche sur le côté droit de l’écran.

3. Une fois que les données s’étalant dans Power BI, vous pouvez commencer à visualiser le flux des données. Ci-dessous un tableau de bord exemple avec des visualisations de certains chaud chemin d’accès est épinglée à celui-ci. Vous pouvez créer d’autres vignettes du tableau de bord en fonction des groupes de données appropriées. En fonction de leur durée d’exécution votre générateur de données, vos numéros sur les visualisations peut être différent.


    ![](media\cortana-analytics-technical-guide-predictive-maintenance\dashboard-view.png)

4. Voici quelques étapes de création d’une des vignettes ci-dessus, la « flotte affichage de capteur 11 et seuil 48,26 » vignette :

    -   Cliquez sur jeu de données **aircraftmonitor** dans le volet gauche section jeux de données.

    -   Cliquez sur l’icône de **Graphique en courbes** .

    -   Cliquez sur **traitées** dans le volet **champs de** sorte qu’elle affiche sous « Axes » dans le volet de **visualisation** .

    -   Cliquez sur « F11 » et « F11\_alerte » afin qu’ils sont tous deux apparaissent sous « Valeurs ». Cliquez sur la petite flèche en regard de **F11** et **F11\_alerte**, remplacez « Somme » par « Moyenne ».

    -   Cliquez sur **Enregistrer** dans la partie supérieure et nommez le rapport « aircraftmonitor ». Le rapport nommé « aircraftmonitor » est affiché dans la section **rapports** dans le volet de **navigation** gauche.

    -   Cliquez sur l’icône **Visuelle de code confidentiel** dans le coin supérieur droit de ce graphique en courbes. Une fenêtre « Épingler au tableau de bord » peut apparaître pour vous permet de choisir un tableau de bord. Sélectionnez « Prédictive Maintenance démo », puis cliquez sur « Épingler ».

    -   Faites glisser la souris sur cette vignette du tableau de bord, cliquez sur l’icône « Modifier » dans le coin supérieur droit pour modifier son titre à « Flotte affichage de capteur 11 et seuil 48,26 » et sous-titre à « Moyenne de flotte au fil du temps ».

## <a name="how-to-delete-your-solution"></a>**Comment supprimer votre solution**
Vérifiez qu’arrêter le Générateur de données lorsque vous N'utilisez pas activement la solution en exécutant le Générateur de données implique des frais plus élevés. Si vous ne l’utilisez pas, supprimez la solution. Votre solution vous supprimez tous les composants mis en service dans votre abonnement lorsque vous avez déployé la solution. Pour supprimer la solution, cliquez sur le nom de votre solution dans le volet gauche du modèle de solution et cliquez sur Supprimer.

## <a name="cost-estimation-tools"></a>**Outils d’estimation des coûts**

Les deux outils suivants sont disponibles pour vous aider à mieux comprendre les coûts totaux impliqués dans l’exécution de la Maintenance prédictive pour aéronautique Solution modèle dans votre abonnement :

-   [Microsoft Azure coût outil estimateur (en ligne)](https://azure.microsoft.com/pricing/calculator/)

-   [Microsoft Azure coût estimateur outil (bureau)](http://www.microsoft.com/download/details.aspx?id=43376)
