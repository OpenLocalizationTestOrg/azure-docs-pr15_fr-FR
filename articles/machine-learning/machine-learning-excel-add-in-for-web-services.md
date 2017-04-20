<properties
    pageTitle="Macro complémentaire Excel pour les services Web de formation Machine | Microsoft Azure"
    description="Comment utiliser les services Web de formation Azure Machine directement dans Excel sans écrire de code."
    services="machine-learning"
    documentationCenter=""
    authors="tedway"
    manager="jhubbard"
    editor="cgronlun"
    tags=""/>

<tags
    ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="10/05/2016"
    ms.author="tedway;garye" />

# <a name="excel-add-in-for-azure-machine-learning-web-services"></a>Macro complémentaire Excel pour les services Web de formation Machine Azure

Excel facilite l’appel des services Web directement, sans qu’il soit nécessaire d’écrire du code.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Étapes à suivre pour utiliser un service Web existant dans le classeur

1. Ouvrez l' [exemple de fichier Excel](http://aka.ms/amlexcel-sample-2)qui contient la macro complémentaire Excel et des données sur voyageurs sur le TITANE.
2. Sélectionnez le service Web en cliquant dessus-« Titanic survie Predictor (exemple macro complémentaire Excel) [Score] » dans cet exemple.

    ![Sélectionnez service Web][01]

3. Vous accédez alors à la section **Predict** .  Ce classeur contient déjà des exemples de données, mais pour un classeur vide, vous pouvez sélectionner une cellule dans Excel et cliquez sur **utiliser les données exemple**.
4. Sélectionnez les données avec des en-têtes, puis cliquez sur l’icône de la plage d’entrée de données.  Vérifiez que la case à cocher « mes données ont des en-têtes » est activée.
5. Sous **sortie**, entrez le nombre de la cellule où vous souhaitez que la sortie est, par exemple « H1 » ici.
6. Cliquez sur **prévoir**.

    ![Prédiction de section][02]

## <a name="steps-to-add-a-new-web-service"></a>Étapes à suivre pour ajouter un service de nouveau site Web

Déployer un service Web ou utilisez un service Web existant. Pour plus d’informations sur le déploiement d’un service Web, voir [procédure pas à pas étape 5 : déployer le service Web de formation Azure Machine](machine-learning-walkthrough-5-publish-web-service.md).

Obtenir la clé de l’API de votre service Web. Où vous effectuer cette opération dépend si vous avez publié un service Web de formation Machine classique d’un service Web de formation nouvel ordinateur.

**Utiliser un service Web classique** 

1. Dans Studio d’apprentissage Machine, cliquez sur la section **SERVICES WEB** dans le volet gauche, puis sélectionnez le service Web.

    ![Sélectionnez Studio un service Web][04]

2. Copiez la clé d’API pour le service Web.

    ![Clé Studio API][05]

3. Sous l’onglet **tableau de bord** pour le service Web, cliquez sur le lien **Demande/réponse** .
4. Recherchez la section **URI demandé** .  Copiez et enregistrez l’URL.

>[AZURE.NOTE] Il est désormais possible de se connecter au portail [Azure Machine Learning Web Services](https://services.azureml.net) pour obtenir la clé de l’API d’un service Web de formation Machine classique.

**Utilisez un service de nouveau site Web**

1. Dans le portail [Azure Machine Learning Services Web](https://services.azureml.net) , cliquez sur **Services Web**, puis sélectionnez votre service Web. 
2. Cliquez sur **utilisation**.
3. Recherchez la section **informations de consommation de base** . Copiez et enregistrez la **Clé primaire** et l’URL de **Requête-réponse** .


## <a name="steps-to-add-a-new-web-service"></a>Étapes à suivre pour ajouter un service de nouveau site Web

1. Déployer un service Web ou utilisez un service Web existant. Pour plus d’informations sur le déploiement d’un service Web, voir [procédure pas à pas étape 5 : déployer le service Web de formation Azure Machine](machine-learning-walkthrough-5-publish-web-service.md).
2. Cliquez sur **utilisation**.
3. Recherchez la section **informations de consommation de base** . Copiez et enregistrez la **Clé primaire** et l’URL de **Requête-réponse** .
2. Dans Excel, accédez à la section **Services Web** (si vous êtes dans la section **Predict** , cliquez sur la flèche retour pour accéder à la liste des services Web).

    ![Accédez à la sélection de service Web][03]
    
3. Cliquez sur **Ajouter un Service Web**.
4. Collez l’URL dans la zone de texte de macro complémentaire Excel **URL**.
5. Collez la clé primaire/API dans la zone de texte **clé API**.
6. Cliquez sur **Ajouter**.

    ![Clé URL et API d’un service Web classique.][06]

10. Pour utiliser le service Web, suivez les instructions précédentes, « Étapes pour utiliser un Service Web existant ».

## <a name="sharing-your-workbook"></a>Partage de votre classeur

Si vous enregistrez votre classeur, puis la clé primaire/API pour les services Web que vous avez ajouté est également enregistrée. Cela signifie que vous devez uniquement partager le classeur avec des personnes que vous faites confiance.

Poser des questions dans la section commentaire suivant ou sur notre [forum](http://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).

[01]: ./media/machine-learning-excel-add-in-for-web-services/image1.png
[02]: ./media/machine-learning-excel-add-in-for-web-services/image2.png
[03]: ./media/machine-learning-excel-add-in-for-web-services/image3.png
[04]: ./media/machine-learning-excel-add-in-for-web-services/image4.png
[05]: ./media/machine-learning-excel-add-in-for-web-services/image5.png
[06]: ./media/machine-learning-excel-add-in-for-web-services/image6.png
