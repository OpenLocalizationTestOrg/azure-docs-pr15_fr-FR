<properties 
    pageTitle="Créer une application logique à l’aide de modèles Azure le Gestionnaire de ressources dans le Service d’application Azure | Microsoft Azure" 
    description="Utiliser un modèle Azure le Gestionnaire de ressources pour déployer une application logique vide pour la définition de flux de travail." 
    services="logic-apps" 
    documentationCenter="" 
    authors="MSFTMan" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/25/2016" 
    ms.author="deonhe"/>

# <a name="create-a-logic-app-using-a-template"></a>Créer une application logique à l’aide d’un modèle

Utiliser un modèle Azure le Gestionnaire de ressources pour créer une application vide logique pouvant être utilisées pour définir le flux de travail. Vous pouvez définir les ressources sont déployés et comment définir les paramètres spécifiés lorsque le déploiement est exécuté. Vous pouvez utiliser ce modèle pour vos propres déploiements ou le personnaliser selon vos besoins.

Pour plus d’informations sur les propriétés de l’application logique, voir [API de gestion des flux de travail logique d’application](https://msdn.microsoft.com/library/azure/mt643788.aspx). 

Pour obtenir des exemples de la définition de lui-même, voir [définitions auteur logique d’application](app-service-logic-author-definitions.md). 

Pour plus d’informations sur la création de modèles, voir [Création de modèles de gestionnaire de ressources Azure](../resource-group-authoring-templates.md).

Pour le modèle terminé, voir [modèle d’application logique](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json).

## <a name="what-you-will-deploy"></a>Vous allez déployer

Grâce à ce modèle vous déployez une application logique.

Pour exécuter automatiquement le déploiement, sélectionnez le bouton suivant :  

[![Déploiement d’Azure](media/app-service-logic-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

## <a name="parameters"></a>Paramètres

[AZURE.INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

### <a name="testuri"></a>testUri

     "testUri": {
        "type": "string",
        "defaultValue": "http://azure.microsoft.com/en-us/status/feed/"
      }
    
## <a name="resources-to-deploy"></a>Ressources pour le déploiement

### <a name="logic-app"></a>Application logique

Crée l’application logique.

Les modèles utilise une valeur de paramètre pour le nom de l’application logique. Il définit l’emplacement de l’application logique sur le même emplacement que le groupe de ressources. 

Cette définition particulier s’exécute une fois par heure et connecte à l’emplacement spécifié dans le paramètre **testUri** . 

    {
      "type": "Microsoft.Logic/workflows",
      "apiVersion": "2016-06-01",
      "name": "[parameters('logicAppName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "displayName": "LogicApp"
      },
      "properties": {
        "definition": {
          "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "testURI": {
              "type": "string",
              "defaultValue": "[parameters('testUri')]"
            }
          },
          "triggers": {
            "recurrence": {
              "type": "recurrence",
              "recurrence": {
                "frequency": "Hour",
                "interval": 1
              }
            }
          },
          "actions": {
            "http": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('testUri')"
              },
              "runAfter": {}
            }
          },
          "outputs": {}
        },
        "parameters": {}
      }
    }


## <a name="commands-to-run-deployment"></a>Commandes pour exécuter le déploiement

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>Azure infrastructure du langage commun

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup


 
