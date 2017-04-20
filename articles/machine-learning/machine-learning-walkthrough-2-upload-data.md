<properties
    pageTitle="Étape 2 : Télécharger des données dans une expérience d’apprentissage automatique | Microsoft Azure"
    description="Étape 2 de la développer une procédure pas à pas solution prédictive : téléchargement de stockage des données publiques dans Azure Machine apprentissage Studio."
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016" 
    ms.author="garye"/>


# <a name="walkthrough-step-2-upload-existing-data-into-an-azure-machine-learning-experiment"></a>Procédure pas à pas étape 2 : Télécharger des données existantes dans une expérience d’apprentissage automatique Azure

Il s’agit de la deuxième étape de la procédure pas à pas, [développer une solution analytique prédictive dans l’apprentissage automatique Azure](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Créer un espace de travail d’apprentissage automatique](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  **Télécharger des données existantes**
3.  [Créer une nouvelle expérience](machine-learning-walkthrough-3-create-new-experiment.md)
4.  [Former et évaluer les modèles](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [Déployer le service web](machine-learning-walkthrough-5-publish-web-service.md)
6.  [Accéder au service web](machine-learning-walkthrough-6-access-web-service.md)

----------

Pour développer un modèle de prévision pour risque de crédit, nous avons besoin de données que nous pouvons utiliser pour former puis testez le modèle. Pour cette procédure, nous allons utiliser « Jeu de données UCI Statlog (données bancaire allemand) » à partir du référentiel d’apprentissage automatique UCI. Vous trouverez ici :  
<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://archive.ICS.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

Nous utiliserons le fichier nommé **german.data**. Téléchargez ce fichier sur votre disque dur local.  

Ce groupe de données contient les lignes de 20 variables de 1000 candidats passées de crédit. Ces 20 variables représentent vecteur de fonctionnalité du groupe de données, qui fournit des caractéristiques d’identification pour chaque demandeur de crédit. Une colonne supplémentaire dans chaque ligne représente le risque de crédit calculé du demandeur, avec les 700 candidats identifiés comme un risque de crédit faible et 300 comme un risque élevé.

Le site Web UCI fournit une description des attributs du vecteur fonctionnalité, qui incluent des informations financières, l’historique de crédit, le statut d’emploi et informations personnelles. Pour chaque demandeur, une évaluation binaire a été donnée indiquant si elles sont un faible ou élevé risque de crédit.  

Nous allons utiliser ces données pour former un modèle analytique prédictive. Quand nous avons terminé, notre modèle doit être en mesure d’accepter les informations pour d’autres personnes et prédire s’ils sont un risque de crédit haute ou faible.  

Voici un petit changement intéressant ici. La description du jeu de données explique que classer par erreur entrées d’une personne comme un risque de crédit faible lorsqu’ils sont en réalité un risque élevé crédit est 5 fois plus coûteux à l’établissement financier à classer par erreur entrées un risque de crédit faible plus haut. Dupliquer (5 fois) ces entrées représentant une personne présentant un risque élevé crédit constitue un moyen simple à prendre en compte dans notre expérience. Puis, si le modèle de misclassifies un risque élevé crédit comme faible, il ne cette mauvaise classification 5 fois, une fois pour chaque doublon. Cela augmente le coût de cette erreur dans les résultats de la formation.  

##<a name="convert-the-dataset-format"></a>Convertir le format de jeu de données
Le jeu de données d’origine utilise un format séparé par vide. Machine apprentissage Studio fonctionne mieux avec un fichier de valeurs séparées par des virgules (CSV), afin que nous allons convertir le jeu de données en remplaçant les espaces par des virgules.  

Il existe plusieurs manières de convertir ces données. La première consiste à l’aide de la commande Windows PowerShell suivante :   

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

Une autre méthode consiste à l’aide de la commande sed Unix :  

    sed 's/ /,/g' german.data > german.csv  

Dans les deux cas, nous avons créé une version des données dans un fichier nommé **german.csv** que nous utiliserons dans notre expérience séparées par des virgules.

##<a name="upload-the-dataset-to-machine-learning-studio"></a>Télécharger le jeu de données sur ordinateur apprentissage Studio

Une fois que les données ont été converties au format CSV, nous avons besoin de télécharger à Machine apprentissage Studio. Pour plus d’informations sur la prise en main Machine apprentissage Studio, voir [Microsoft Azure Machine apprentissage Studio famille](https://studio.azureml.net/).

1.  Ouvrez apprentissage Studio ([https://studio.azureml.net](https://studio.azureml.net)) de l’ordinateur. Lorsque vous êtes invité à se connecter, utilisez le compte que vous avez spécifié en tant que le propriétaire de l’espace de travail.
1.  Cliquez sur l’onglet **Studio** en haut de la fenêtre.
1.  Cliquez sur **+ Nouveau** en bas de la fenêtre.
1.  Sélectionnez le **jeu de données**.
1.  Sélectionnez **fichier LOCAL FROM**.
1.  Dans la boîte de dialogue **télécharger un nouveau groupe de données** , cliquez sur **Parcourir** et recherchez le fichier **german.csv** que vous avez créé.
1.  Entrez un nom pour le dataset. Pour cette procédure, nous allons l’appeler « Données de carte de crédit UCI allemand ».
1.  Type de données, sélectionnez **générique fichier CSV sans en-tête (. nh.csv)**.
1.  Ajoutez une description si vous le souhaitez.
1.  Cliquez sur **OK**.  

![Télécharger le jeu de données][1]  


Ceci met à jour les données dans un module dataset que nous pouvons utiliser d’une expérience.

> [AZURE.TIP] Pour gérer les jeux de données que vous avez téléchargées sur Studio, cliquez sur l’onglet **jeux de données** vers la gauche de la fenêtre Studio.

Pour plus d’informations sur l’importation de différents types de données dans une expérience, voir [importer vos données formation dans Azure Machine apprentissage Studio](machine-learning-data-science-import-data.md).

**Suivant : [créer une nouvelle expérience](machine-learning-walkthrough-3-create-new-experiment.md)**

[1]: ./media/machine-learning-walkthrough-2-upload-data/upload1.png
