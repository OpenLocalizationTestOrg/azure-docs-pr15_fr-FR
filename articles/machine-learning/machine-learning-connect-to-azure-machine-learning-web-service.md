<properties
    pageTitle="Se connecter à un Service Web de formation Machine | Microsoft Azure"
    description="Avec c# ou Python, vous connecter à un service Web de formation Azure Machine en utilisant une clé d’autorisation."
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016" 
    ms.author="garye" />


# <a name="connect-to-an-azure-machine-learning-web-service"></a>Se connecter à un Service Web de formation Machine Azure

L’expérience de développement apprentissage automatique Azure est une API de service Web à réaliser des prévisions à partir des données d’entrée en temps réel ou en mode de traitement par lots. Azure Machine apprentissage Studio vous permet de créer des prédictions et déploiement d’un service Web de formation Machine Azure.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Pour découvrir comment créer et déployer un service Web de formation Machine à l’aide de la Machine apprentissage Studio :

- Pour un didacticiel sur la création d’une expérience dans Machine apprentissage Studio, consultez [créer votre première expérience](machine-learning-create-experiment.md).
- Pour plus d’informations sur le déploiement d’un service Web, voir [déploiement d’un service Web de formation Machine](machine-learning-publish-a-machine-learning-web-service.md).
- Pour plus d’informations sur l’apprentissage automatique en règle générale, visitez le [Poste de Documentation de formation](https://azure.microsoft.com/documentation/services/machine-learning/).

## <a name="azure-machine-learning-web-service"></a>Service Web de formation Machine Azure ##

Avec le service Web de formation Azure Machine, une application externe communique avec un modèle de flux de travail score apprentissage automatique en temps réel. Un appel de service Web de formation Machine renvoie des résultats de prévision pour une application externe. Pour appeler un service Web de formation Machine, vous passez d’une clé d’API est créée lorsque vous déployez une prévision. Le service Web de formation Machine est basé sur reste, un choix architecture courante pour les projets de programmation web.

Azure apprentissage automatique contient deux types de services :

- Réponse à la demande de Service (enregistrements de ressource) – une latence faible, le service hautement scalable qui fournit une interface pour les modèles sans état créés et déployés à partir de la Studio apprentissage Machine.
- Lot Service d’exécution (BES) – asynchrone de service ce scores un lot d’enregistrements de données.

Pour plus d’informations sur les services Web de formation Machine, voir [déploiement d’un service Web de formation Machine](machine-learning-publish-a-machine-learning-web-service.md).

## <a name="get-an-azure-machine-learning-authorization-key"></a>Obtenir une clé d’autorisation apprentissage automatique Azure ##

Lorsque vous déployez votre expérience, API clés sont générées pour le service Web. Vous pouvez récupérer les clés de plusieurs emplacements.

## <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>À partir du portail Microsoft Azure Machine Learning Web Services

Connectez-vous au portail [Microsoft Azure Machine Learning Web Services](https://services.azureml.net) .

Pour récupérer la clé API d’un service Web de formation nouvel ordinateur :

1. Dans le portail Azure Machine Learning Services Web, cliquez sur **Services Web** du menu supérieur.
2. Cliquez sur le service Web pour lequel vous souhaitez récupérer la clé.
3. Dans le menu supérieur, cliquez sur **consommer**.
4. Copiez et enregistrez la **Clé primaire**.


Pour récupérer la clé API d’un service Web de formation classique Machine :

1. Dans le portail Azure Machine Learning Services Web, cliquez sur **Services Web classique** du menu supérieur.
2. Cliquez sur le service Web sur lequel vous travaillez.
3. Cliquez sur le point de terminaison pour lequel vous souhaitez récupérer la clé.
3. Dans le menu supérieur, cliquez sur **consommer**.
4. Copiez et enregistrez la **Clé primaire**.

## <a name="classic-web-service"></a>Service Web classique ##

 Vous pouvez également récupérer une clé d’un service Web classique de Machine apprentissage Studio ou du portail Azure.

### <a name="machine-learning-studio"></a>Machine apprentissage Studio ###

1. Dans Studio d’apprentissage Machine, cliquez sur **SERVICES WEB** à gauche.
2. Cliquez sur un service Web. La **clé d’API** est sous l’onglet **tableau de bord** .

### <a name="azure-portal"></a>Portail Azure ###

1. Cliquez sur **Apprentissage automatique** à gauche.
2. Cliquez sur l’espace de travail dans lequel se trouve votre service Web.
3. Cliquez sur **SERVICES WEB**.
4. Cliquez sur un service Web.
5. Cliquez sur un point de terminaison. « CLÉ API » est vers le bas dans l’angle inférieur droit.

## <a id="connect"></a>Se connecter à un service Web de formation Machine

Vous pouvez vous connecter à un service Web de formation Machine à l’aide de n’importe quel langage de programmation qui prend en charge de réponse et une demande HTTP. Vous pouvez consulter les exemples dans c#, Python et R à partir d’une page de l’aide du service Web de formation Machine.

**API d’apprentissage machine aide** Aide de l’API apprentissage machine est créée lorsque vous déployez un service Web. Voir [apprentissage automatique Azure procédure pas à pas déployer Service Web](machine-learning-walkthrough-5-publish-web-service.md).
L’aide de l’API apprentissage Machine contient des détails sur une service Web de prévision.

2. Cliquez sur le service Web sur lequel vous travaillez.
3. Cliquez sur le point de terminaison pour lequel vous souhaitez afficher la Page aide de l’API.
3. Dans le menu supérieur, cliquez sur **consommer**.
3. Cliquez sur **page d’aide de l’API** sous la requête-réponse ou points de terminaison de l’exécution du lot.

**En mode API d’apprentissage automatique l’aide d’un service de nouveau site Web**

Dans la Machine Azure Web Services portail d’apprentissage :

1. Cliquez sur **SERVICES WEB** dans le menu supérieur.
2. Cliquez sur le service Web pour lequel vous souhaitez récupérer la clé.

Cliquez sur **consommer** pour obtenir les URI d’exemples de code et Services d’exécution de commandes et demande Reposonse de c#, R et Python.

Cliquez sur **L’API Swagger** pour obtenir la documentation Swagger en fonction de l’API appelées à partir de l’URI fourni.

### <a name="c-sample"></a>Exemple c# ###

Pour vous connecter à un service Web de formation Machine, utilisez un **HttpClient** passant ScoreData. ScoreData contient un FeatureVector, un vecteur n dimensions des fonctionnalités numériques qui représente le ScoreData. Vous authentifiez vers le service d’apprentissage automatique avec une clé d’API.

Pour vous connecter à un service Web de formation Machine, le package NuGet **Microsoft.AspNet.WebApi.Client** doit être installé.

**Installer Microsoft.AspNet.WebApi.Client NuGet dans Visual Studio**

1. Publier le dataset de télécharger à partir de UCI : 2 gros classe dataset Service Web.
2. Cliquez sur **Outils** > **Gestionnaire de Package NuGet** > **Console du Gestionnaire de Package**.
2. Choisissez **Microsoft.AspNet.WebApi.Client Package d’installation**.

**Pour exécuter l’exemple de code**

1. Publier « exemple 1 : télécharger le jeu de données à partir de UCI : adulte 2 classe dataset « expérience, partie de la collection d’exemple apprentissage automatique.
2. Affecter apiKey avec la touche à partir d’un service Web. Voir **obtenir une clé d’autorisation apprentissage automatique Azure** ci-dessus.
3. Affecter serviceUri avec l’URI demander.


### <a name="python-sample"></a>Exemple Python ###

Pour vous connecter à un service Web de formation Machine, utilisez la bibliothèque **urllib2** transmission ScoreData. ScoreData contient un FeatureVector, un vecteur n dimensions des fonctionnalités numériques qui représente le ScoreData. Vous authentifiez vers le service d’apprentissage automatique avec une clé d’API.


**Pour exécuter l’exemple de code**

1. Déployer « exemple 1 : télécharger le jeu de données à partir de UCI : adulte 2 classe dataset « expérience, partie de la collection d’exemple apprentissage automatique.
2. Affecter apiKey avec la touche à partir d’un service Web. Consultez la section **obtenir une clé d’autorisation apprentissage automatique Azure** vers le début de cet article.
3. Affecter serviceUri avec l’URI demander.
