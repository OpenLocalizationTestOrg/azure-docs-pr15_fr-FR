<properties
    pageTitle="Former un nouveau service web utilisant les applets de commande PowerShell de gestion d’apprentissage Machine | Microsoft Azure"
    description="Découvrez comment former un modèle et mettre à jour le service web pour utiliser le modèle que vous venez formé dans l’apprentissage automatique Azure à l’aide des applets de commande PowerShell de gestion d’apprentissage Machine par programme."
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="raymondlaghaeian"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="v-donglo"/>

# <a name="retrain-a-new-web-service-using-the-machine-learning-management-powershell-cmdlets"></a>Former un nouveau service web utilisant les applets de commande PowerShell de gestion d’apprentissage Machine

Lorsque vous former un nouveau service web, vous mettez à jour la définition du service web prédictive pour faire référence au nouveau modèle formé.  

## <a name="prerequisites"></a>Conditions préalables

Vous devez avoir configurer une expérience de formation et une expérience prédictive évolue [former Machine apprentissage modèles par programme](machine-learning-retrain-models-programmatically.md). 

>[AZURE.IMPORTANT] L’expérience prédictive doit être déployé comme un ordinateur Azure le Gestionnaire de ressources (nouveau) en fonction de service web de formation. 
 
Pour plus d’informations sur les services web de déploiement, voir [déploiement d’un service web d’apprentissage automatique Azure](machine-learning-publish-a-machine-learning-web-service.md).

Ce processus requiert que vous avez installé les applets de commande Azure Machine apprentissage. Pour installer les applets de commande apprentissage automatique d’informations, consultez la référence [Applets de commande Azure Machine apprentissage](https://msdn.microsoft.com/library/azure/mt767952.aspx) sur MSDN.

Sortie de la reconversion, copiés les informations suivantes :

* BaseLocation
* RelativeLocation

Les étapes à que suivre sont :

1.  Connectez-vous à votre compte Azure le Gestionnaire de ressources.
2.  Obtenir la définition du service web
3.  Exporter la définition du Service Web en tant que JSON
4.  Mettre à jour la référence au blob ilearner dans le JSON.
5.  Importer le JSON dans une définition de Service Web
6.  Mise à jour du service web avec la nouvelle définition du Service Web

## <a name="sign-in-to-your-azure-resource-manager-account"></a>Connectez-vous à votre compte Azure le Gestionnaire de ressources

Vous devez tout d’abord vous connecter à votre compte Azure à partir de l’environnement PowerShell à l’aide de l’applet de commande [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) .

## <a name="get-the-web-service-definition"></a>Obtenir la définition du Service Web

Ensuite, obtenez le Service Web en appelant l’applet de commande [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx) . La définition du Service Web est une représentation interne du modèle formé du service web et n’est pas directement modifiable. Vérifiez que vous souhaitez récupérer la définition du Service Web pour votre expérience prédictive et pas votre expérience de formation.

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Pour déterminer le nom du groupe de ressources d’un service web existant, exécutez l’applet de commande Get-AzureRmMlWebService sans paramètres pour afficher les services web dans votre abonnement. Recherchez le service web et consultez son ID de service web. Le nom du groupe de ressources est le quatrième élément dans le code, juste après l’élément *resourceGroups* . Dans l’exemple suivant, le nom du groupe ressource est par défaut-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Par ailleurs, pour déterminer le nom du groupe de ressources d’un service web existant, connectez-vous au portail Microsoft Azure Machine Learning Web Services. Sélectionnez le service web. Le nom de groupe de ressources est la cinquième élément de l’URL du service web, juste après l’élément *resourceGroups* . Dans l’exemple suivant, le nom du groupe ressource est par défaut-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-as-json"></a>Exporter la définition du Service Web en tant que JSON

Pour modifier la définition au modèle formé à utiliser nouvellement formé modèle, vous devez d’abord utiliser l’applet de commande [Exporter AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767935.aspx) exporter vers un fichier au format JSON.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob-in-the-json"></a>Mettre à jour la référence au blob ilearner dans le JSON.

Dans les biens, localisez le [modèle formé], mettre à jour la valeur *uri* dans le nœud *locationInfo* avec l’URI du blob ilearner. L’URI est générée en combinant les *BaseLocation* et *RelativeLocation* à partir du résultat de la BES reconversion appel.

     "asset3": {
        "name": "Retrain Samp.le [trained model]",
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

## <a name="import-the-json-into-a-web-service-definition"></a>Importer le JSON dans une définition de Service Web

Vous devez utiliser l’applet de commande [Importer AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767925.aspx) pour convertir le fichier JSON modifié en une définition de Service Web que vous pouvez utiliser pour mettre à jour de l’expérience Predicative.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service-with-new-web-service-definition"></a>Mise à jour du service web avec la nouvelle définition du Service Web

Enfin, vous utilisez applet de commande de [Mise à jour AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767922.aspx) pour mettre à jour de l’expérience prédictive.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'  -ServiceUpdates $wsd

## <a name="summary"></a>Résumé

Utilise les applets de commande PowerShell d’apprentissage Machine gestion, vous pouvez mettre à jour le modèle formé d’un Service Web prédictive l’activation de scénarios tels que :

* Modèle périodique reconversion avec de nouvelles données.
* Distribution d’un modèle pour les clients dans le but de vous permettant de les former le modèle à l’aide de leurs propres données.
