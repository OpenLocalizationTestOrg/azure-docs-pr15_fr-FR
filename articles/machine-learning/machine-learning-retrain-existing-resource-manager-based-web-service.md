<properties
    pageTitle="Former un service web prédictive existant | Microsoft Azure"
    description="Découvrez comment faire pour former un modèle et mettre à jour le service web pour utiliser le modèle que vous venez formé dans l’apprentissage automatique Azure."
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="raymondl"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/06/2016"
    ms.author="v-donglo"/>


# <a name="retrain-an-existing-predictive-web-service"></a>Former un service web prédictive existant

Ce document décrit le processus de reconversion pour le scénario suivant :

* Vous avez une expérience de formation et une expérience prédictive que vous avez déployé comme un service web operationalized.
* Vous disposez de nouvelles données que vous voulez votre service web prédictive à utiliser pour effectuer son score.

Commençant par votre service web existant et expériences, vous devez procédez comme suit :

1. Mettre à jour le modèle.
    1. Modifier votre expérience de formation afin de permettre sorties et les entrées de service web.
    2. Déployer l’expérience de formation comme un service web reconversion.
    3. Utilisez lot de l’exécution du Service (BES de l’expérience formation) pour former le modèle.
2. Utiliser les applets de commande PowerShell d’apprentissage Azure Machine pour mettre à jour de l’expérience prédictive.
    1.  Connectez-vous à votre compte Azure le Gestionnaire de ressources.
    2.  Obtenir la définition du service web.
    3.  Exporter la définition du service web en tant que JSON.
    4.  Mettre à jour la référence au blob ilearner dans le JSON.
    5.  Importer le JSON dans une définition de service web.
    6.  Mettre à jour le service web avec une nouvelle définition de service web.

## <a name="deploy-the-training-experiment"></a>Déploiement de l’expérience de formation

Pour déployer l’expérience de formation comme un service web reconversion, vous devez ajouter des entrées de service web et des sorties au modèle. En vous connectant à un module de *Sortie du Service Web* à l’expérience * [Modèle Train] [ train-model] * module, activer l’expérience de formation générer un nouveau modèle formé que vous pouvez utiliser dans votre expérience prédictive. Si vous avez un module *d’Évaluer le modèle* , vous pouvez également joindre sortie du service web pour obtenir les résultats d’évaluation sous forme de sortie.

Pour mettre à jour votre expérience de formation :

1. Connexion d’un module *Web Service d’entrée* à votre entrée de données (par exemple, un module de *Données manquants nettoyer* ). En règle générale, vous souhaitez vous assurer que vos données d’entrée sont traitées de la même façon que vos données formation d’origine.
2. Connexion d’un module de *Sortie du Service Web* à la sortie de votre *Modèle de Train* module.
3. Si vous avez un module *Modèle évaluer* et que vous voulez générer les résultats de l’évaluation, connexion d’un module de *Sortie du Service Web* à la sortie de votre module *Évaluer le modèle* .

Exécuter votre expérience.

Ensuite, vous devez déployer l’expérience de formation comme un service web produisant un modèle formé et résultats de l’évaluation modèle.  

En bas de l’expérience de dessin, cliquez sur **Définir le serviceWeb**, puis sur **Déployer du Service Web [nouveau]**. Le portail Azure Machine Learning Services Web s’ouvre à la page de **Déploiement d’un Service Web** . Tapez un nom pour votre service web, choisissez un mode de paiement, puis cliquez sur **déployer**. Vous pouvez utiliser uniquement la méthode de l’exécution du lot pour la création de modèles formés.


## <a name="retrain-the-model-with-new-data-by-using-bes"></a>Former le modèle avec les nouvelles données à l’aide de BES

Cet exemple, nous utilisons c# pour créer l’application reconversion. Vous pouvez également utiliser des exemples de code Python ou R pour effectuer cette tâche.

Pour appeler les reconversion API :

1. Créer une application console c# dans Visual Studio (**Nouveau** > **projet** > **Windows Desktop** > **Application Console**).
2.  Connectez-vous au portail Machine Learning Web Services.
3.  Cliquez sur le service web que vous travaillez.
2.  Cliquez sur **utilisation**.
3.  En bas de la page de **consommer** , dans la section **Exemples de Code** , cliquez sur **commandes**.
5.  Copier le code c# pour l’exécution du lot et collez-le dans le fichier Program.cs. Assurez-vous que l’espace de noms reste intacte.

Ajoutez le package NuGet Microsoft.AspNet.WebApi.Client, comme indiqué dans les commentaires. Pour ajouter la référence à Microsoft.WindowsAzure.Storage.dll, vous devrez tout d’abord installer la [bibliothèque du client pour les services de stockage Azure](https://www.nuget.org/packages/WindowsAzure.Storage).

La capture d’écran suivante montre la page de **consommer** dans le portail Azure Machine Learning Web Services.

![Utiliser à la page][1]

### <a name="update-the-apikey-declaration"></a>Mise à jour de la déclaration apikey

Recherchez la déclaration **apikey** :

    const string apiKey = "abc123"; // Replace this with the API key for the web service

Dans la section **informations de consommation de base** de la page de **consommer** , recherchez la clé primaire et copiez-le à la déclaration **apikey** .

### <a name="update-the-azure-storage-information"></a>Mettre à jour les informations de stockage Azure

L’exemple de code BES télécharge un fichier à partir d’un lecteur local (par exemple, « C:\temp\CensusIpnput.csv ») au stockage Azure, il traite et réinscrit les résultats au stockage Azure.  

Pour mettre à jour les informations de stockage Azure, vous devez extraire le nom de compte de stockage, clé et informations du conteneur pour votre compte de stockage à partir du portail classique Azure et puis mise à jour la correspondi après l’exécution de votre expérience, le flux de travail qui en résulte doit être semblable à ce qui suit :

![Flux de travail qui en résulte après l’exécution][4]valeurs naturel dans le code.

1. Connectez-vous au portail classique Azure.
1. Dans la colonne de navigation de gauche, cliquez sur **espace de stockage**.
1. Dans la liste des comptes de stockage, sélectionnez-en un pour stocker le modèle retrained.
1. Dans la partie inférieure de la page, cliquez sur **Gérer les touches d’accès rapide**.
1. Copiez et enregistrez la **Clé primaire de Access** et fermez la boîte de dialogue.
1. En haut de la page, cliquez sur **les conteneurs**.
1. Sélectionner un conteneur existant ou créez-en un et enregistrer le nom.

Recherchez les *StorageAccountName*, *StorageAccountKey*et déclarations *StorageContainerName* et mettre à jour les valeurs que vous avez enregistré à partir du portail classique.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

Vous devez également vous assurer que le fichier d’entrée est disponible à l’emplacement que vous spécifiez dans le code.

### <a name="specify-the-output-location"></a>Spécifier l’emplacement de sortie

Lorsque vous spécifiez l’emplacement de sortie dans la charge utile de demander, l’extension du fichier spécifié dans *RelativeLocation* doit être spécifiée en tant que `ilearner`. Consultez l’exemple suivant :

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

Voici un exemple de sortie reconversion : ![reconversion sortie][6]

## <a name="evaluate-the-retraining-results"></a>Évaluer les résultats de la reconversion

Lorsque vous exécutez l’application, la sortie inclut l’URL et le jeton de signatures accès partagé qui sont nécessaires pour accéder aux résultats de l’évaluation.

Vous pouvez afficher les résultats de performances du modèle retrained en combinant les *BaseLocation*, *RelativeLocation*et *SasBlobToken* dans les résultats de sortie pour *output2* (comme illustré dans l’image de sortie reconversion précédente) et en collant l’URL complète dans la barre d’adresse du navigateur.  

Examinez les résultats pour déterminer si le modèle que vous venez formé fonctionne encore suffisamment pour remplacer le précédent.

Copiez les *BaseLocation*, *RelativeLocation*et *SasBlobToken* les résultats.

## <a name="retrain-the-web-service"></a>Former le service web

Lorsque vous former un nouveau service web, vous mettez à jour la définition du service web prédictive pour faire référence au nouveau modèle formé. La définition du service web est une représentation interne du modèle formé du service web et n’est pas directement modifiable. Vérifiez que vous souhaitez récupérer la définition du service web pour votre expérience prédictive et pas votre expérience de formation.

## <a name="sign-in-to-azure-resource-manager"></a>Se connecter au Gestionnaire de ressources Azure

Vous devez tout d’abord se connecter à votre compte Azure à partir de l’environnement PowerShell à l’aide de l’applet de commande [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) .

## <a name="get-the-web-service-definition-object"></a>Obtenir l’objet de définition de Service Web

Ensuite, obtenez l’objet de définition de Service Web en appelant l’applet de commande [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx) .

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Pour déterminer le nom du groupe de ressources d’un service web existant, exécutez l’applet de commande Get-AzureRmMlWebService sans paramètres pour afficher les services web dans votre abonnement. Recherchez le service web et consultez son ID de service web. Le nom du groupe de ressources est le quatrième élément dans le code, juste après l’élément *resourceGroups* . Dans l’exemple suivant, le nom du groupe ressource est par défaut-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Par ailleurs, pour déterminer le nom du groupe de ressources d’un service web existant, connectez-vous au portail Azure Machine Learning Web Services. Sélectionnez le service web. Le nom de groupe de ressources est la cinquième élément de l’URL du service web, juste après l’élément *resourceGroups* . Dans l’exemple suivant, le nom du groupe ressource est par défaut-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-object-as-json"></a>Exporter l’objet de définition de Service Web en tant que JSON

Pour modifier la définition du modèle qualifié pour utiliser le modèle que vous venez de formation, vous devez d’abord utiliser l’applet de commande [Exporter AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767935.aspx) exporter vers un fichier au format JSON.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob"></a>Mettre à jour la référence au blob ilearner

Dans les biens, localisez le [modèle formé], mettre à jour la valeur *uri* dans le nœud *locationInfo* avec l’URI du blob ilearner. L’URI est générée en combinant les *BaseLocation* et *RelativeLocation* à partir du résultat de la BES reconversion appel.

     "asset3": {
        "name": "Retrain Sample [trained model]",
        "type": "Resource",
        "locationInfo": {
          "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
        },
        "outputPorts": {
          "Results dataset": {
            "type": "Dataset"
          }
        }
      },

## <a name="import-the-json-into-a-web-service-definition-object"></a>Importer le JSON dans un objet de définition de Service Web

Vous devez utiliser l’applet de commande [Importer AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767925.aspx) pour convertir le fichier JSON modifié dans un objet de définition de Service Web que vous pouvez utiliser pour mettre à jour de l’expérience predicative.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service"></a>Mise à jour du service web

Enfin, utilisez l’applet de commande [AzureRmMlWebService de mise à jour](https://msdn.microsoft.com/library/azure/mt767922.aspx) pour mettre à jour de l’expérience prédictive.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -

[1]: ./media/machine-learning-retrain-existing-arm-web-service/machine-learning-retrain-models-consume-page.png
[4]: ./media/machine-learning-retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE04.png
[6]: ./media/machine-learning-retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE06.png

<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
