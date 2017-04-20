<properties 
    pageTitle="Déployer une application web qui est liée à un référentiel GitHub" 
    description="Utiliser un modèle Azure le Gestionnaire de ressources pour déployer une application web qui contient un projet à partir d’un référentiel GitHub." 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/27/2016" 
    ms.author="cephalin"/>

# <a name="deploy-a-web-app-linked-to-a-github-repository"></a>Déployer une application web liée à un référentiel GitHub

Dans cette rubrique, vous allez apprendre à créer un modèle de gestionnaire de ressources Azure qui déploie une application web qui est liée à un projet dans un référentiel GitHub. Vous allez découvrir comment définir les ressources sont déployés et comment définir des paramètres qui sont spécifiées lorsque le déploiement est exécuté. Vous pouvez utiliser ce modèle pour vos propres déploiements ou le personnaliser selon vos besoins.

Pour plus d’informations sur la création de modèles, voir [Création de modèles de gestionnaire de ressources Azure](../resource-group-authoring-templates.md).

Pour le modèle terminé, voir [Web application liée à un modèle de GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-github-deploy/azuredeploy.json).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="what-you-will-deploy"></a>Vous allez déployer

Avec ce modèle, vous allez déployer une application web qui contient le code à partir d’un projet dans GitHub.

Pour exécuter le déploiement automatiquement, cliquez sur le bouton suivant :

[![Déploiement d’Azure](./media/app-service-web-arm-from-github-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-github-deploy%2Fazuredeploy.json)

## <a name="parameters"></a>Paramètres

[AZURE.INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

### <a name="repourl"></a>repoURL

L’URL de référentiel GitHub qui contient le projet à déployer. Ce paramètre contient une valeur par défaut, mais cette valeur est uniquement destinée à vous montrer comment fournir l’URL pour le référentiel. Vous pouvez utiliser cette valeur lorsque vous testez le modèle mais que vous souhaitiez fournir l’URL de votre propre référentiel lorsque vous travaillez avec le modèle.

    "repoURL": {
        "type": "string",
        "defaultValue": "https://github.com/davidebbo-test/Mvc52Application.git"
    }

### <a name="branch"></a>branche

Branche du référentiel à utiliser lors de déploiement de l’application. La valeur par défaut est maître, mais vous pouvez fournir le nom de n’importe quelle branche dans le référentiel que vous voulez déployer.

    "branch": {
        "type": "string",
        "defaultValue": "master"
    }
    
## <a name="resources-to-deploy"></a>Ressources pour le déploiement

[AZURE.INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### <a name="web-app"></a>Dans le navigateur

Crée l’application web qui est liée au projet dans GitHub. 

Vous spécifiez le nom de l’application web via le paramètre de **nom du site** et l’emplacement de l’application web via le paramètre **siteLocation** . Dans l’élément **dependsOn** , le modèle définit l’application web en tant que dépendant du service d’hébergement de plan. Comme il est dépendant de l’offre d’hébergement, l’application web n’est pas créée jusqu'à ce que le plan d’hébergement terminée en cours de création. L’élément **dependsOn** sert uniquement à spécifier l’ordre de déploiement. Si vous n’activez pas l’application web en tant que dépendant de l’offre d’hébergement, Gestionnaire de ressources Azure tentera de créer les deux ressources en même temps et vous pouvez recevoir un message d’erreur si l’application web est créée avant l’offre d’hébergement.

L’application web est également une ressource enfant qui est définie dans la section **ressources** ci-dessous. Cette ressource enfant définit le contrôle de code source pour le projet déployé avec l’application web. Dans ce modèle, le contrôle de source est lié à un référentiel GitHub particulier. Le référentiel GitHub est défini avec le code **« RepoUrl » : « https://github.com/davidebbo-test/Mvc52Application.git »** peut coder en dur l’URL de référentiel lorsque vous voulez créer un modèle qui déploie à plusieurs reprises un projet unique tout en nécessitant le nombre minimal de paramètres.
Au lieu de manière irréversible l’URL de référentiel, vous pouvez ajouter un paramètre pour l’URL du référentiel et utiliser cette valeur pour la propriété **RepoUrl** .

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/sites",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ],
      "properties": {
        "serverFarmId": "[parameters('hostingPlanName')]"
      },
      "resources": [
        {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/Sites', parameters('siteName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('repoURL')]",
            "branch": "[parameters('branch')]",
            "IsManualIntegration": true
          }
        }
      ]
    }

## <a name="commands-to-run-deployment"></a>Commandes pour exécuter le déploiement

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json -siteName ExampleSite -hostingPlanName ExamplePlan -siteLocation "West US" -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>Azure infrastructure du langage commun

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json


 
