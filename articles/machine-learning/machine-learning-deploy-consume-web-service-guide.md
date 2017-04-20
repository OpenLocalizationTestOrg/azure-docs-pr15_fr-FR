<properties
    pageTitle="Services Web d’apprentissage ordinateurs Azure : Déploiement et la consommation | Microsoft Azure"
    description="Ressources pour le déploiement et l’utilisation des services web."
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
    ms.date="10/12/2016"
    ms.author="v-donglo"/>

# <a name="azure-machine-learning-web-services-deployment-and-consumption"></a>Azure Machine Learning Web Services : Déploiement et consommation

Vous pouvez utiliser Azure Machine d’apprentissage pour déployer des flux de travail et les modèles que les services web apprentissage automatique. Ces services web puis peuvent servir à appeler les modèles d’apprentissage automatique à partir d’applications via Internet pour effectuer les prévisions en temps réel ou en mode de traitement par lots. Étant donné que les services web sont RESTful, vous pouvez les appeler à partir de divers langages de programmation et plateformes, telles que .NET et Java et d’applications, comme Excel.

Les sections suivantes fournissent des liens vers des procédures pas à pas, code et la documentation afin de vous aider à démarrer.

## <a name="deploy-a-web-service"></a>Déployer un service web

### <a name="with-azure-machine-learning-studio"></a>Avec apprentissage automatique Azure Studio

Machine apprentissage Studio et le portail Microsoft Azure Machine Learning Web Services vous aider à déployer et gérer un service web sans écrire de code.

Les liens suivants fournissent des informations générales sur le déploiement d’un nouveau service web :

* Découvrez comment déployer un nouveau service web qui est basé sur le Gestionnaire de ressources Azure, voir [déploiement d’un nouveau service web](machine-learning-webservice-deploy-a-web-service.md).
* Pour une procédure pas à pas sur le déploiement d’un service web, voir [déploiement d’un service web d’apprentissage automatique Azure](machine-learning-publish-a-machine-learning-web-service.md).
* Pour une procédure pas à pas complète sur la façon de créer et déployer un service web, voir [procédure pas à pas étape 1 : créer un espace de travail d’apprentissage automatique](machine-learning-walkthrough-1-create-ml-workspace.md).
* Pour obtenir des exemples spécifiques déploiement un service web, voir :

    * [Procédure pas à pas étape 5 : Déployer le service web apprentissage automatique Azure](machine-learning-walkthrough-5-publish-web-service.md)
    * [Comment déployer un service web sur plusieurs régions](machine-learning-how-to-deploy-to-multiple-regions.md)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Avec le fournisseur de ressources de services web API (API du Gestionnaire de ressources Azure)

Le fournisseur de ressources d’apprentissage automatique Azure pour les services web permet de déploiement et la gestion des services web à l’aide de l’API REST appels. Pour plus d’informations, voir la référence [Machine apprentissage Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) sur MSDN.

### <a name="with-powershell-cmdlets"></a>Avec les applets de commande PowerShell

Azure fournisseur de ressources d’apprentissage automatique pour les services web permet de déploiement et la gestion des services web à l’aide des applets de commande PowerShell.

Pour utiliser les applets de commande, vous devez tout d’abord connectez-vous à votre compte Azure à partir de l’environnement PowerShell à l’aide de l’applet de commande [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) . Si vous ne connaissez pas l’appel des commandes PowerShell basés sur le Gestionnaire de ressources, voir [Utilisation de PowerShell Azure avec le Gestionnaire de ressources Azure](../powershell-azure-resource-manager.md#login-to-your-azure-account).

Pour exporter votre expérience prédictive, utilisez [Cet exemple de code](https://github.com/ritwik20/AzureML-WebServices). Après avoir créé le fichier .exe à partir du code, vous pouvez taper :

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

Exécution de l’application crée un modèle JSON de service web. Pour utiliser le modèle pour déployer un service web, vous devez ajouter les informations suivantes :

* Nom de compte de stockage et de clé

    Vous pouvez obtenir le nom de compte de stockage et la clé à partir du [portail Azure](https://portal.azure.com/) ou le [portail classique Azure](http://manage.windowsazure.com/).
* ID de plan d’engagement

    Vous pouvez obtenir l’ID de plan à partir du portail [Azure Machine Learning Web Services](https://services.azureml.net) en vous connectant en cliquant sur un nom de plan.

Les ajouter au modèle JSON comme enfants du nœud *Propriétés* au même niveau que le nœud *MachineLearningWorkspace* .

Voici un exemple :

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Voir les exemples de code pour plus d’informations et les articles suivants :

* Référence des [Applets de commande Azure Machine apprentissage]( https://msdn.microsoft.com/library/azure/mt767952.aspx) sur MSDN
* Exemple [procédure pas à pas](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) sur GitHub

## <a name="consume-the-web-services"></a>Utiliser les services web

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>À partir des Services Web apprentissage Machine Azure (test) l’interface utilisateur

Vous pouvez tester votre service web à partir du portail Azure Machine Learning Web Services. Cela inclut le test du service de requête-réponse (a) et interfaces de service de l’exécution par lots (BES).

* [Déployer un nouveau service web](machine-learning-webservice-deploy-a-web-service.md)
* [Déploiement d’un service web apprentissage automatique Azure](machine-learning-publish-a-machine-learning-web-service.md)
* [Procédure pas à pas étape 5 : Déployer le service web apprentissage automatique Azure](machine-learning-walkthrough-5-publish-web-service.md)

### <a name="from-excel"></a>À partir d’Excel

Vous pouvez télécharger un modèle Excel qui utilise le service web :

* [Utilisation d’un service web apprentissage automatique Azure à partir d’Excel](machine-learning-consuming-from-excel.md)
* [Complément Excel pour Azure Machine Learning Web Services](machine-learning-excel-add-in-for-web-services.md)


### <a name="from-a-rest-based-client"></a>À partir d’un client reste

Azure Machine Learning Web Services sont des API RESTful. Vous pouvez utiliser ces API à partir de diverses plates-formes, tels que .NET, Python, R, Java, etc.. La page de **consommer** de votre service web sur le [portail Microsoft Azure Machine Learning Web Services](https://services.azureml.net) comporte des exemples de code qui peuvent vous aider à commencer. Pour plus d’informations, voir [comment utiliser un service web Azure apprentissage automatique qui a été déployé à partir d’un ordinateur expérience d’apprentissage](machine-learning-consume-web-services.md).

