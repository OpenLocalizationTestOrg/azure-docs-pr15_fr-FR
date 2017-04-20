<properties
    pageTitle="Étape 6 : Accède au service web apprentissage automatique | Microsoft Azure"
    description="Étape 6 de la développer une procédure pas à pas solution prédictive : accéder à un service web apprentissage automatique Azure actif."
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
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="garye"/>


# <a name="walkthrough-step-6-access-the-azure-machine-learning-web-service"></a>Procédure pas à pas étape 6 : Accède au service web apprentissage automatique Azure

Il s’agit de la dernière étape de la procédure pas à pas, [développer une solution analytique prédictive dans l’apprentissage automatique Azure](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Créer un espace de travail d’apprentissage automatique](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Télécharger des données existantes](machine-learning-walkthrough-2-upload-data.md)
3.  [Créer une nouvelle expérience](machine-learning-walkthrough-3-create-new-experiment.md)
4.  [Former et évaluer les modèles](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [Déployer le service web](machine-learning-walkthrough-5-publish-web-service.md)
6.  **Accéder au service web**

----------

Dans l’étape précédente de cette procédure, nous avons déployé un service web qui utilise notre modèle de prévision de risque de crédit. Maintenant, les utilisateurs doivent pouvoir envoyer des données et recevoir des résultats. 

Le service web est un service web Azure qui peut recevoir et retourner des données à l’aide des API REST de deux façons :  

-   **Demande/réponse** - l’utilisateur envoie une ou plusieurs lignes de données de crédit au service à l’aide d’un protocole HTTP et le service répond avec un ou plusieurs jeux de résultats.
-   **L’exécution du lot** - l’utilisateur enregistre une ou plusieurs lignes de données de crédit dans un blob Azure, puis envoyer l’emplacement d’objets blob au service. Le service évalue toutes les lignes de données dans le blob d’entrée, stocke les résultats dans un autre blob et renvoie l’URL de ce conteneur.  

Le moyen le plus rapide et plus facile accéder au service web consiste à utiliser les modèles d’application Web disponibles sur le [Azure Marketplace des applications Web](https://azure.microsoft.com/marketplace/web-applications/all/).
Ces modèles d’application web peuvent créer une application web personnalisée capable de données d’entrée de votre service web et qu’elle retourne. Il vous souhaitez est permettent d’accéder à votre service web et des données et le modèle fait le reste.

Pour plus d’informations sur l’utilisation de modèles d’application web, voir [consommer un service web d’apprentissage automatique Azure avec un modèle d’application web](machine-learning-consume-web-service-with-web-app-template.md).

Vous pouvez également développer une application personnalisée pour accéder au service web à l’aide d’un code de démarrage fourni pour vous dans R, c# et langages de programmation Python.
Vous trouverez plus de détails de [l’utilisation d’un service web d’apprentissage automatique Azure qui a été publié à partir d’un ordinateur expérience d’apprentissage](machine-learning-consume-web-services.md).
