<properties
    pageTitle="Importer des données dans Machine apprentissage Studio à partir d’un fichier local | Microsoft Azure"
    description="Comment importer vos données de formation Azure Machine apprentissage Studio à partir d’un fichier local."
    keywords="importer des données, format des données, les types de données, des sources de données, les données de formation"
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
    ms.date="09/16/2016"
    ms.author="garye;bradsev" />


# <a name="import-your-training-data-into-azure-machine-learning-studio-from-a-local-file"></a>Importer vos données formation dans Azure Machine apprentissage Studio à partir d’un fichier local

[AZURE.INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]


Pour utiliser vos propres données dans Machine apprentissage Studio, vous pouvez télécharger un fichier de données à l’avance à partir de votre disque dur local pour créer un module de jeu de données dans votre espace de travail. 


## <a name="import-data-from-a-local-file"></a>Importer des données à partir d’un fichier local

Vous pouvez importer des données à partir d’un disque dur local en procédant comme suit :

1. Cliquez sur **+ Nouveau** en bas de la fenêtre Machine apprentissage Studio.
2. Sélectionnez **jeu de données** et **de fichier**.
3. Dans la boîte de dialogue **télécharger un nouveau groupe de données** , recherchez le fichier à télécharger
4. Entrez un nom, identifier le type de données et vous pouvez également entrer une description. Une description est recommandée : il vous permet d’enregistrer toutes les caractéristiques sur les données voulues à l’esprit lorsque vous utilisez les données à l’avenir.
5. La case à cocher **il s’agit de la nouvelle version d’un dataset existant** vous permet de mettre à jour un dataset existant avec les nouvelles données. Cliquez simplement sur cette case à cocher, puis entrez le nom d’un dataset existant.

Lors du téléchargement, vous verrez un message que votre fichier est en cours de téléchargement. Télécharger durée dépend de la taille de vos données et la vitesse de votre connexion au service.
Si vous connaissez que le fichier va prendre un certain temps, vous pouvez effectuer d’autres éléments internes Machine apprentissage Studio lors d’une attente. Toutefois, fermeture du navigateur va entraîner l’échec de téléchargement des données.

Une fois vos données sont téléchargées, il est stocké dans un module de jeu de données et n’est disponible pour une expérience dans votre espace de travail.
Lorsque vous modifiez une expérience, vous pouvez trouver les jeux de données que vous avez créé dans la liste **Mes jeux de données** sous la liste des **Jeux de données enregistré** dans la palette de module. Vous pouvez glisser-déplacer le jeu de données sur la zone de dessin expérience lorsque vous voulez utiliser le jeu de données pour les autres analytique et apprentissage automatique.



