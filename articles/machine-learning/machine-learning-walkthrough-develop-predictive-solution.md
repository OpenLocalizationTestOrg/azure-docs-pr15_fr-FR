<properties
    pageTitle="Une solution prédictive pour risque de crédit avec apprentissage automatique | Microsoft Azure"
    description="Une procédure pas à pas détaillé montrant comment créer une solution analytique prédictive pour l’analyse de risque de crédit dans Azure Machine apprentissage Studio."
    keywords="risque de crédit, solution prédictive analytique, évaluation des risques"
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
    ms.date="09/16/2016"
    ms.author="garye"/>


# <a name="walkthrough-develop-a-predictive-analytics-solution-for-credit-risk-assessment-in-azure-machine-learning"></a>Procédure pas à pas : Développer une solution analytique prédictive pour l’analyse de risque de crédit dans l’apprentissage automatique Azure

Supposons que vous deviez prévoir risque de crédit d’un individu en fonction des informations qu’ils donnent sur une application de crédit.  

Évaluation des risques crédit est un problème complexe, bien entendu, mais nous allons un peu simplifier les paramètres de la question. Ensuite, nous pouvons l’utiliser comme exemple de comment vous pouvez utiliser Microsoft Azure Machine Learning avec Studio d’apprentissage automatique et le service web apprentissage automatique pour créer une telle solution prédictive analytique.  

Dans cette procédure pas à pas détaillées, nous allons suivent le processus de développement d’un modèle analytique prédictive dans Machine apprentissage Studio et son déploiement comme un service web apprentissage automatique Azure. Nous commencez par les données de risque de crédit disponible publiquement, développer et former un modèle de prévision basé sur ces données et puis déployez le modèle comme un service web qui peut être utilisé par d’autres personnes pour l’analyse de risque de crédit.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

<!-- -->

>[AZURE.TIP] Pour télécharger et imprimer un diagramme qui offre une vue d’ensemble des fonctionnalités de Machine apprentissage Studio, voir le [diagramme de vue d’ensemble des fonctionnalités Azure Machine apprentissage Studio](machine-learning-studio-overview-diagram.md).

Pour créer une solution d’évaluation des risques crédit, nous allons procédez comme suit :  

1.  [Créer un espace de travail d’apprentissage automatique](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Télécharger des données existantes](machine-learning-walkthrough-2-upload-data.md)
3.  [Créer une nouvelle expérience](machine-learning-walkthrough-3-create-new-experiment.md)
4.  [Former et évaluer les modèles](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [Déployer le service web](machine-learning-walkthrough-5-publish-web-service.md)
6.  [Accéder au service web](machine-learning-walkthrough-6-access-web-service.md)

Cette procédure pas à pas est basé sur une version simplifiée de la [le classement binaire : prédiction de risque de crédit](http://go.microsoft.com/fwlink/?LinkID=525270) expérience d’exemple dans la [Galerie d’Intelligence Cortana](http://gallery.cortanaintelligence.com/).
