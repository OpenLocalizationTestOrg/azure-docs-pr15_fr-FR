<properties 
    pageTitle="Qu’est Azure Machine apprentissage Studio ? | Microsoft Azure"
    description="Vue d’ensemble de Azure ML Studio, un outil de glisser-déplacer pour créer rapidement des modèles à partir d’une bibliothèque de prêt à l’emploi des algorithmes et des modules."
    keywords="machine Azure d’apprentissage, ml azure, studio ml"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/09/2016"
    ms.author="garye"/>

# <a name="what-is-azure-machine-learning-studio"></a>Qu’est Azure Machine apprentissage Studio ?

Microsoft Azure Machine apprentissage Studio est un outil de collaboration, faites glisser-déplacer que vous pouvez utiliser pour créer, tester et déployer des solutions analytique prédictive sur vos données. Machine apprentissage Studio publie des modèles en tant que services web qui peuvent aisément être utilisés par des applications personnalisées ou outils de décisionnel comme Excel.

Machine apprentissage Studio est où scientifique de données, prédictive analytique, ressources de cloud et vos données répondent.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="the-machine-learning-studio-interactive-workspace"></a>L’espace de travail interactif Machine apprentissage Studio

Pour développer un modèle d’analyse prédictive, vous en général utilisez les données d’une ou plusieurs sources, transformez et analysez ces données à travers les différentes fonctions statistiques et manipulation des données et générez un jeu de résultats. Développement d’un modèle à ceci est un processus itératif. Lorsque vous modifiez les différentes fonctions et leurs paramètres, vos résultats convergent jusqu'à ce que vous êtes satisfait que vous disposez d’un modèle formé et efficace.

**Azure Machine apprentissage Studio** vous donne un espace de travail visual, interactif pour créer, tester et modifions un modèle d’analyse prédictive facilement. Vous glisser-déplacer ***des groupes de données*** et analyse ***modules*** sur une zone de dessin interactif, reliant entre eux pour former un ***tester***, que vous lancez dans Machine apprentissage Studio. Pour effectuer une itération sur la conception du modèle, que vous modifiez l’expérience, enregistrez une copie si vous le souhaitez, puis exécutez à nouveau. Lorsque vous êtes prêt, vous pouvez convertir votre ***formation essayer*** une ***expérience prédictive***et publier en tant que ***service web*** afin que votre modèle est accessible par d’autres personnes.

>[AZURE.TIP] Pour télécharger et imprimer un diagramme qui offre une vue d’ensemble des fonctionnalités de Machine apprentissage Studio, voir le [diagramme de vue d’ensemble des fonctionnalités Azure Machine apprentissage Studio](machine-learning-studio-overview-diagram.md).

Il n’existe aucune programmation, connexion simplement visuellement des jeux de données et des modules pour créer votre modèle d’analyse prédictive.

![Diagramme ML Studio Azure : créer des expériences, lire les données de nombreuses sources, écrire des données évaluées, écrire des modèles.][ml-studio-overview]

## <a name="get-started-with-machine-learning-studio"></a>Prise en main Machine apprentissage Studio

Lorsque vous entrez tout d’abord [Machine apprentissage Studio](https://studio.azureml.net) vous consultez la page **d’accueil** . À partir de là, vous pouvez afficher la documentation, des vidéos, des webinaires et recherchez d’autres ressources utiles.

Il existe trois onglets dans la partie supérieure : **famille** (où vous démarrez), **Studio**et **Galerie**.

### <a name="studio"></a>Studio

Cliquez sur l’onglet **Studio** et vous serez invité à se connecter à l’aide de votre compte Microsoft ou votre compte professionnel ou scolaire. Une fois connecté, vous verrez les onglets suivants sur la gauche :

- **Projets** - Collections d’expériences, des jeux de données, des blocs-notes et d’autres ressources représentant un projet unique
- **Expériences** - expériences qui ont été créés, exécuter et enregistré en tant que brouillons
- **SERVICES WEB** - services Web que vous avez déployé à partir de vos expériences
- **Blocs-notes** - blocs-notes Jupyter que vous avez créé
- **Jeux de données** - jeux de données que vous avez téléchargées dans Studio
- **Modèles de formation** - modèles que vous avez formé dans des expériences et enregistré dans Studio
- **Paramètres** - un ensemble de paramètres que vous pouvez utiliser pour configurer votre compte et des ressources.

### <a name="gallery"></a>Galerie

Cliquez sur l’onglet **bibliothèque** et vous serez redirigé vers la galerie d’Intelligence Cortana. La galerie est un emplacement dans lequel une Communauté de données scientifiques et les développeurs peut partager les solutions créées à l’aide de composants de la Suite Intelligence Cortana.

Pour plus d’informations sur la galerie, voir [partager et découvrir les solutions de la galerie d’Intelligence Cortana](machine-learning-gallery-how-to-use-contribute-publish.md).

## <a name="components-of-an-experiment"></a>Composants d’une expérience

Une expérience est composée de groupes de données contenant les données des modules analytiques, qui vous connectez ensemble afin de créer un modèle d’analyse prédictive. Plus précisément, une expérience valide comporte ces caractéristiques :

- L’expérience comporte au moins un jeu de données et un module
- Jeux de données peut-être être connectés uniquement aux modules
- Modules peuvent être connectés à des groupes de données ou d’autres modules
- Tous les ports d’entrée pour les modules doivent disposer d’une connexion pour le flux de données
- Tous les requis paramètres pour chaque module doivent être définis

Vous pouvez créer une expérience à partir de zéro, ou vous pouvez utiliser une expérience d’exemple existant en tant que modèle. Pour plus d’informations, voir [expériences sur des échantillons utiliser pour créer de nouvelles expériences](machine-learning-sample-experiments.md).

Pour obtenir un exemple de création d’une expérience simple, voir [créer une expérience simple dans Azure Machine apprentissage Studio](machine-learning-create-experiment.md).

Pour une procédure pas à pas plus complète de création d’une solution prédictive analytique, voir [Création d’une solution prédictive avec apprentissage automatique Azure](machine-learning-walkthrough-develop-predictive-solution.md).

### <a name="datasets"></a>Jeux de données

Un jeu de données est des données qui ont été téléchargées vers Machine apprentissage Studio afin qu’il puisse être utilisé dans le processus de modélisation. Un certain nombre de jeux de données exemple est inclus avec Machine apprentissage Studio afin de pouvoir expérimenter, et vous pouvez télécharger plusieurs jeux de données que vous avez besoin. Voici quelques exemples de jeux de données inclus :

- **Données MPG pour différentes voitures** - Miles par gallon (MPG) les valeurs des voitures identifiées par numéro de cylindres, puissance, etc..
- **Données sur le cancer du sein** - données de diagnostic de cancer du sein.
- **Données de se déclenche la forêt** - tailles de fire forêt dans Portugal mise en correspondance.

À mesure que vous créez une expérience, vous pouvez choisir dans la liste des jeux de données disponibles à gauche de la zone de dessin.

Pour une liste des jeux de données exemple inclus dans Machine apprentissage Studio, voir [utiliser les jeux de données d’exemple dans Azure Machine apprentissage Studio](machine-learning-use-sample-datasets.md).

### <a name="modules"></a>Modules

Un module est un algorithme que vous pouvez effectuer sur vos données. Machine apprentissage Studio contient un nombre de modules allant de fonctions de pénétration de données à des formations, notation et processus de validation. Voici quelques exemples de modules inclus :

- [Convertir en ARFF] [ convert-to-arff] -convertit un jeu de données .NET sérialisé à attribut Relation fichier Format (ARFF).
- [Calculer des statistiques élémentaires] [ elementary-statistics] -calcule les statistiques élémentaires telles que moyenne, l’écart-type, etc..
- [Régression linéaire] [ linear-regression] -crée un modèle en ligne dégradé basée sur descente droite de régression linéaire.
- [Score modèle] [ score-model] -obtenant un modèle de classement ou régression formé.

À mesure que vous créez une expérience, vous pouvez choisir dans la liste des modules disponibles à gauche de la zone de dessin.  

Un module peut avoir un jeu de paramètres que vous pouvez utiliser pour configurer des algorithmes internes du module. Lorsque vous sélectionnez un module dans la zone de dessin, paramètres du module sont affichés dans le volet de **Propriétés** à droite de la zone de dessin. Vous pouvez modifier les paramètres de ce volet pour optimiser votre modèle.

Pour certains aide pour la navigation via la bibliothèque de grande taille des algorithmes d’apprentissage machine disponibles, voir [comment choisir des algorithmes d’apprentissage de Microsoft Azure Machine](machine-learning-algorithm-choice.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>Déploiement d’un service web prédictive analytique

Une fois que votre modèle analytique prédictive est prêt, vous pouvez le déployer comme un service web directement à partir de la Machine apprentissage Studio. Pour plus d’informations sur ce processus, voir [déploiement d’un service web d’apprentissage automatique Azure](machine-learning-publish-a-machine-learning-web-service.md).

[ml-studio-overview]:./media/machine-learning-what-is-ml-studio/azure-ml-studio-diagram.jpg

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
