<properties 
    pageTitle="Mise en service un Cache Redis | Microsoft Azure" 
    description="Utiliser le Gestionnaire de ressources Azure modèle pour déployer un Cache Redis Azure." 
    services="app-service" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="web" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/27/2016" 
    ms.author="sdanie"/>

# <a name="create-a-redis-cache-using-a-template"></a>Créer un Cache Redis à l’aide d’un modèle

Dans cette rubrique, vous allez apprendre à créer un modèle de gestionnaire de ressources Azure qui déploie un Cache Redis Azure. Le cache peut être utilisé avec un compte de stockage existant pour conserver les données de diagnostic. Vous découvrez également comment définir les ressources sont déployés et comment définir des paramètres qui sont spécifiées lorsque le déploiement est exécuté. Vous pouvez utiliser ce modèle pour vos propres déploiements ou le personnaliser selon vos besoins.

Pour l’instant, les paramètres de diagnostic sont partagés pour tous les cache dans la même région pour un abonnement. Mise à jour un cache dans la région affecte tous les autres cache dans la région.

Pour plus d’informations sur la création de modèles, voir [Création de modèles de gestionnaire de ressources Azure](../resource-group-authoring-templates.md).

Pour le modèle terminé, voir [modèle Redis Cache](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json).

>[AZURE.NOTE] Modèles de gestionnaire de ressources pour la nouvelle [couche Premium](cache-premium-tier-intro.md) sont disponibles. 
>
>-    [Créer un Cache Redis Premium avec cluster](https://azure.microsoft.com/documentation/templates/201-redis-premium-cluster-diagnostics/)
>-    [Créer un Cache Redis Premium avec permanence des données](https://azure.microsoft.com/documentation/templates/201-redis-premium-persistence/)
>-    [Créez Premium Redis Cache avec VNet et cluster facultatif](https://azure.microsoft.com/documentation/templates/201-redis-premium-vnet-cluster-diagnostics/)
>
>Pour vérifier les derniers modèles, voir [Modèles de démarrage rapide d’Azure](https://azure.microsoft.com/documentation/templates/) et recherchez `Redis Cache`.

## <a name="what-you-will-deploy"></a>Vous allez déployer

Dans ce modèle, vous allez déployer un Cache Azure Redis qui utilise un compte de stockage existant pour les données de diagnostic.

Pour exécuter le déploiement automatiquement, cliquez sur le bouton suivant :

[![Déploiement d’Azure](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>Paramètres

Avec le Gestionnaire de ressources Azure, vous définissez les paramètres pour les valeurs que vous souhaitez spécifier lorsque le modèle est déployé. Le modèle inclut une section intitulée paramètres qui contient toutes les valeurs de paramètre.
Vous devez définir un paramètre pour les valeurs qui varient basée sur le projet que vous déployez ou sur l’environnement que vous effectuez le déploiement. Ne définissez pas les paramètres pour les valeurs qui toujours restent les mêmes. Chaque valeur de paramètre permet de définir les ressources qui sont déployés dans le modèle. 


[AZURE.INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLocation

L’emplacement du Cache Redis. Pour de meilleures performances, utiliser le même emplacement que l’application à utiliser avec le cache.

    "redisCacheLocation": {
      "type": "string"
    }

### <a name="existingdiagnosticsstorageaccountname"></a>existingDiagnosticsStorageAccountName

Le nom du compte de stockage existant à utiliser pour les diagnostics. 

    "existingDiagnosticsStorageAccountName": {
      "type": "string"
    }

### <a name="enablenonsslport"></a>enableNonSslPort

Valeur booléenne qui indique s’il faut autoriser l’accès par le biais de ports non SSL.

    "enableNonSslPort": {
      "type": "bool"
    }

### <a name="diagnosticsstatus"></a>diagnosticsStatus

Une valeur qui indique si les diagnostics est activée. Utiliser activé ou désactivé.

    "diagnosticsStatus": {
      "type": "string",
      "defaultValue": "ON",
      "allowedValues": [
            "ON",
            "OFF"
        ]
    }
    
## <a name="resources-to-deploy"></a>Ressources pour le déploiement

### <a name="redis-cache"></a>Redis Cache

Crée le Cache Redis Azure.

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('redisCacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[parameters('redisCacheLocation')]",
      "properties": {
        "enableNonSslPort": "[parameters('enableNonSslPort')]",
        "sku": {
          "capacity": "[parameters('redisCacheCapacity')]",
          "family": "[parameters('redisCacheFamily')]",
          "name": "[parameters('redisCacheSKU')]"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-07-01",
          "type": "Microsoft.Cache/redis/providers/diagnosticsettings",
          "name": "[concat(parameters('redisCacheName'), '/Microsoft.Insights/service')]",
          "location": "[parameters('redisCacheLocation')]",
          "dependsOn": [
            "[concat('Microsoft.Cache/Redis/', parameters('redisCacheName'))]"
          ],
          "properties": {
            "status": "[parameters('diagnosticsStatus')]",
            "storageAccountName": "[parameters('existingDiagnosticsStorageAccountName')]"
          }
        }
      ]
    }



## <a name="commands-to-run-deployment"></a>Commandes pour exécuter le déploiement

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)] 

### <a name="powershell"></a>PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache -redisCacheLocation "West US"

### <a name="azure-cli"></a>Azure infrastructure du langage commun

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup


