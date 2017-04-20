<properties
    pageTitle="Former les modèles d’apprentissage automatique par programme | Microsoft Azure"
    description="Découvrez comment former un modèle et mettre à jour le service web pour utiliser le modèle que vous venez formé dans l’apprentissage automatique Azure par programme."
    services="machine-learning"
    documentationCenter=""
    authors="raymondlaghaeian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="raymondl;garye;v-donglo"/>


# <a name="retrain-machine-learning-models-programmatically"></a>Former les modèles d’apprentissage automatique par programme  

Dans cette procédure pas à pas, vous allez apprendre à par programme former un Azure Machine apprentissage Service Web c# et le service d’exécution du lot apprentissage Machine.

Une fois que vous avez s le modèle, les procédures suivantes expliquent comment mettre à jour le modèle dans votre service web prédictive :

- Si vous avez déployé un service web classique dans le portail Machine Learning Web Services, voir [former un service web classique](machine-learning-retrain-a-classic-web-service.md). 
- Si vous avez déployé un service web, voir [former un nouveau service web utilisant les applets de commande de gestion d’apprentissage Machine](machine-learning-retrain-new-web-service-using-powershell.md).

Pour une vue d’ensemble du processus de reconversion, voir [former un modèle d’apprentissage automatique](machine-learning-retrain-machine-learning-model.md).

Si vous voulez commencer avec votre service web nouveau gestionnaire de ressources Azure basé existant, voir [former un service web prédictive existant](machine-learning-retrain-existing-resource-manager-based-web-service.md).

## <a name="create-a-training-experiment"></a>Créer une expérience de formation
 
Dans cet exemple, vous allez utiliser « exemple 5 : évaluer Train, Test, pour le classement binaire : gros Dataset » à partir des échantillons Microsoft Azure Machine Learning. 
    
Pour créer l’expérience :

1.  Connectez-vous à Microsoft Azure Studio d’apprentissage de l’ordinateur. 
2.  Dans le coin inférieur droit du tableau de bord, cliquez sur **Nouveau**.
3.  Dans le Microsoft Samples, sélectionnez exemple 5.
4.  Pour renommer l’expérience, en haut de la zone de dessin expérience, sélectionnez le nom de l’expérience « exemple 5 : évaluer Train, Test, pour le classement binaire : gros Dataset ».
5.  Modèle de recensement type.
6.  Dans la partie inférieure de l’expérience la zone de dessin, cliquez sur **exécuter**.
7.  Cliquez sur **configurer le service web** et sélectionnez **service web Retraining**. 

    ![Expérience initial.][2]

Diagramme 2 : Initiale expérience.

## <a name="create-a-predictive-experiment-and-publish-as-a-web-service"></a>Créer une expérience prédictive et publier en tant qu’un service web  

Ensuite, vous créez une expérience Predicative.

1.  En bas de l’expérience de dessin, cliquez sur **Définir le serviceWeb** , puis sélectionnez **Prédictive Service Web**. Cela enregistre le modèle comme modèle formé et ajoute des modules d’entrée et sortie du service web. 
2.  Cliquez sur **exécuter**. 
3.  Une fois que l’expérience est terminée, cliquez sur **Déployer du Service Web [classique]** ou un **Service Web déployer [nouveau]**.

## <a name="deploy-the-training-experiment-as-a-training-web-service"></a>Déploiement de l’expérience de formation comme un service web de formation

Pour former le modèle expérimenté, vous devez déployer l’expérience de formation que vous avez créé comme un service web Retraining. Ce service web nécessite un module de *Sortie du Service Web* connecté à la * [Modèle Train] [ train-model] * module, pour être en mesure de présenter des nouveaux modèles formés.

1. Pour revenir à l’expérience de formation, cliquez sur l’icône expériences dans le volet gauche, puis cliquez sur l’expérience nommé modèle recensement.  
2. Dans la zone de recherche éléments expérience de recherche, tapez service web. 
3. Faites glisser un module *D’entrée de Service Web* sur la zone de dessin expérience et d’atteindre sa sortie le module de *Données manquants nettoyer* .  Cela garantit que vos données reconversion sont traitées de la même manière que vos données formation d’origine.
4. Faites glisser deux modules de *service web sortie* dans la zone de dessin expérience. Se connecter à la sortie du module *Train modèle* à un, le résultat du module *Évaluer un modèle* à l’autre. La sortie du service web pour **Train modèle** permet de créer le nouveau modèle formé. La sortie liée au **Modèle évaluer** renvoie la sortie de ce module, c'est-à-dire les résultats de performances.
5. Cliquez sur **exécuter**. 

Vous devez ensuite déployer l’expérience de formation comme un service web produisant un modèle formé et résultats de l’évaluation modèle. Pour ce faire, votre prochaine ensemble d’actions varient selon que vous travaillez avec un service web classique ou un service web.  
  
**Service web classique**

En bas de l’expérience de dessin, cliquez sur **Définir le serviceWeb** et sélectionnez **Déployer du Service Web [classique]**. Le **tableau de bord** de Service Web s’affiche avec la clé API et de la page de l’aide de l’API pour l’exécution du lot. Uniquement la méthode de l’exécution du lot peut être utilisée pour la création de modèles formé.

**Nouveau service web**

En bas de l’expérience de dessin, cliquez sur **Définir le serviceWeb** et sélectionnez **Déployer du Service Web [nouveau]**. Le portail d’apprentissage de Machine Azure Service Web, Services Web s’ouvre à la page de service web déployer. Tapez un nom pour votre service web et choisissez un plan de paiement, puis cliquez sur **déployer**. Uniquement la méthode de l’exécution du lot peut être utilisée pour la création de modèles de formation

Dans les deux cas, une fois l’expérience terminée en cours d’exécution, le flux de travail qui en résulte doit se présenter comme suit :

![Flux de travail qui en résulte après l’exécution.][4]

Figure 3 : Résultant après l’exécution du flux de travail.

## <a name="retrain-the-model-with-new-data-using-bes"></a>Former le modèle avec les nouvelles données à l’aide de BES

Dans cet exemple, vous utilisez c# pour créer l’application reconversion. Vous pouvez également utiliser l’exemple de code Python ou R pour effectuer cette tâche.

Pour appeler les API reconversion :

1. Créer une Application Console c# dans Visual Studio (Nouveau -> projet -> Windows Desktop -> Application Console).
2.  Connectez-vous au portail de Service Web de formation Machine.
3.  Si vous travaillez avec un service web classique, cliquez sur **Services Web classique**.
    1.  Cliquez sur le service web que vous travaillez.
    2.  Cliquez sur le point de terminaison par défaut.
    3.  Cliquez sur **utilisation**.
    4.  En bas de la page de **consommer** , dans la section **Exemples de Code** , cliquez sur **commandes**.
    5.  Passez à l’étape 5 de cette procédure.
4.  Si vous travaillez avec un nouveau service web, cliquez sur **Services Web**.
    1.  Cliquez sur le service web que vous travaillez.
    2.  Cliquez sur **utilisation**.
    3.  En bas de la page de consommer, dans la section **Exemples de Code** , cliquez sur **commandes**.
5.  Copier le code c# pour l’exécution du lot et collez-le dans le fichier Program.cs, en prenant soin de que l’espace de noms reste intacte.

Ajoutez le package Nuget Microsoft.AspNet.WebApi.Client comme indiqué dans les commentaires. Pour ajouter la référence à Microsoft.WindowsAzure.Storage.dll, vous devrez tout d’abord installer la bibliothèque de client pour les services de stockage de Microsoft Azure. Pour plus d’informations, consultez [Services de stockage Windows](https://www.nuget.org/packages/WindowsAzure.Storage).

### <a name="update-the-apikey-declaration"></a>Mise à jour de la déclaration apikey

Localisez la déclaration **apikey** .

    const string apiKey = "abc123"; // Replace this with the API key for the web service

Dans la section **informations de consommation de base** de la page de **consommer** , recherchez la clé primaire et copiez-le à la déclaration **apikey** .

### <a name="update-the-azure-storage-information"></a>Mettre à jour les informations de stockage Azure

L’exemple de code BES télécharge un fichier à partir d’un lecteur local (par exemple « C:\temp\CensusIpnput.csv ») au stockage Azure, il traite et réinscrit les résultats au stockage Azure.  

Pour effectuer cette tâche, vous devez récupérer les informations de nom, clé et un conteneur de compte de stockage pour votre compte de stockage à partir du portail Azure classique et les mise à jour les valeurs correspondantes dans le code. 

1. Connectez-vous au portail Azure classique.
1. Dans la colonne de navigation de gauche, cliquez sur **espace de stockage**.
1. Dans la liste des comptes de stockage, sélectionnez-en un pour stocker le modèle retrained.
1. Dans la partie inférieure de la page, cliquez sur **Gérer les touches d’accès rapide**.
1. Copiez et enregistrez la **Clé primaire de Access** et fermez la boîte de dialogue. 
1. En haut de la page, cliquez sur **les conteneurs**.
1. Sélectionnez un conteneur existant ou créez-en un et enregistrez le nom.

Recherchez les déclarations *StorageAccountName*, *StorageAccountKey*et *StorageContainerName* et mettre à jour les valeurs que vous avez enregistré à partir du portail Azure.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure Storage Account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage Key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage Container name
            
Vous devez également vérifier que le fichier d’entrée n’est disponible à l’emplacement que vous spécifiez dans le code. 

### <a name="specify-the-output-location"></a>Spécifier l’emplacement de sortie

Lorsque vous spécifiez l’emplacement de sortie dans la charge utile de demander, l’extension du fichier spécifié dans *RelativeLocation* doit être spécifiée en tant qu’ilearner. 

Consultez l’exemple suivant :

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you would like to use for your output file, and valid file extension (usually .csv for scoring results, or .ilearner for trained models)*/
            }
        },

>[AZURE.NOTE] Les noms de vos emplacements de sortie peuvent différer de celles de cette procédure selon l’ordre dans lequel vous avez ajouté les modules de sortie de service web. Étant donné que vous configurez cette formation Expérimentez deux sorties, les résultats incluent les informations d’emplacement de stockage pour chacun d’eux.  

![Résultat de la reconversion][6]

Figure 4 : Former sortie.

## <a name="evaluate-the-retraining-results"></a>Évaluer les résultats de la reconversion
 
Lorsque vous exécutez l’application, la sortie inclut le jeton URL et les associations de nécessaires pour accéder aux résultats de l’évaluation.

Vous pouvez afficher les résultats de performances du modèle retrained en combinant les *BaseLocation*, *RelativeLocation*et *SasBlobToken* dans les résultats de sortie pour *output2* (comme illustré dans l’image de sortie reconversion précédente) et en collant l’URL complète dans la barre d’adresse du navigateur.  

Examinez les résultats pour déterminer si le modèle que vous venez formé fonctionne encore suffisamment pour remplacer le précédent.

Copiez les *BaseLocation*, *RelativeLocation*et *SasBlobToken* les résultats, vous allez utiliser les pendant le processus de reconversion.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez déployé le service web prédictive en cliquant sur **Déployer du Service Web [classique]**, voir [former un service web classique](machine-learning-retrain-a-classic-web-service.md).

Si vous avez déployé le service web prédictive en cliquant sur **Déployer du Service Web [nouveau]**, voir [former un nouveau service web utilisant les applets de commande de gestion d’apprentissage Machine](machine-learning-retrain-new-web-service-using-powershell.md).

<!-- Retrain a New web service using the Machine Learning Management REST API -->


[1]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png
[7]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE07.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/