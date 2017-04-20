<properties
    pageTitle="Créer un espace de noms Bus des services à l’aide d’un modèle de gestionnaire de ressources | Microsoft Azure"
    description="Utiliser le Gestionnaire de ressources Azure modèle pour créer un espace de noms Bus des services"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="10/04/2016"
    ms.author="sethm;shvija"/>

# <a name="create-a-service-bus-namespace-using-an-azure-resource-manager-template"></a>Créer un espace de noms Bus des services à l’aide d’un modèle de gestionnaire de ressources Azure

Cet article décrit comment utiliser un modèle de gestionnaire de ressources Azure qui crée un espace de noms Bus des services de type de **messagerie** avec une référence SKU Standard/Basic. L’article définit également les paramètres qui sont spécifiés pour l’exécution du déploiement. Vous pouvez utiliser ce modèle pour vos propres déploiements ou le personnaliser selon vos besoins.

Pour plus d’informations sur la création de modèles, voir [Gestionnaire de ressources Azure de création de modèles][].

Pour le modèle terminé, voir le [modèle d’espace de noms de Service Bus][] sur GitHub.

>[AZURE.NOTE] Les modèles de gestionnaire de ressources Azure suivants sont disponibles pour le téléchargement et déploiement. 
>
>-    [Créer un espace de noms Hubs événement avec un groupe d’événement concentrateur et consommateur](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)
>-    [Créer un espace de noms Bus des services avec file d’attente](service-bus-resource-manager-namespace-queue.md)
>-    [Créer un espace de noms Bus des services avec la rubrique et d’abonnement](service-bus-resource-manager-namespace-topic.md)
>-    [Créer un espace de noms Bus des services avec règle file d’attente et d’autorisation](service-bus-resource-manager-namespace-auth-rule.md)
>
>Pour vérifier les derniers modèles, visitez la galerie de [Modèles de démarrage rapide d’Azure][] et recherche Bus des services.

## <a name="what-will-you-deploy"></a>Ce que vous allez déployer ?

Avec ce modèle, vous allez déployer un espace de noms Bus des services avec un [simple, Standard ou Premium](https://azure.microsoft.com/pricing/details/service-bus/) référence (SKU).

Pour exécuter le déploiement automatiquement, cliquez sur le bouton suivant :

[![Déploiement d’Azure](./media/service-bus-resource-manager-namespace/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)

## <a name="parameters"></a>Paramètres

Avec le Gestionnaire de ressources Azure, vous définissez les paramètres pour les valeurs que vous souhaitez spécifier lorsque le modèle est déployé. Le modèle comprend une section intitulée `Parameters` qui contient toutes les valeurs de paramètre. Vous devez définir un paramètre pour les valeurs qui varient basée sur le projet que vous déployez ou sur l’environnement que vous effectuez le déploiement. Ne définissez pas les paramètres pour les valeurs qui sera toujours la même. Chaque valeur de paramètre permet de définir les ressources qui sont déployés dans le modèle.

Ce modèle définit les paramètres suivants.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

Le nom de l’espace de noms Bus des services pour créer.

```
"serviceBusNamespaceName": {
"type": "string",
"metadata": { 
    "description": "Name of the Service Bus namespace" 
    }
}
```

### <a name="servicebussku"></a>serviceBusSKU

Le nom de la [référence (SKU)](https://azure.microsoft.com/pricing/details/service-bus/) de Bus des services pour créer.

```
"serviceBusSku": { 
    "type": "string", 
    "allowedValues": [ 
        "Basic", 
        "Standard",
        "Premium" 
    ], 
    "defaultValue": "Standard", 
    "metadata": { 
        "description": "The messaging tier for service Bus namespace" 
    } 

```

Le modèle définit les valeurs autorisées pour ce paramètre (Basic, Standard ou Premium) et affecte une valeur par défaut (Standard) si aucune valeur n’est spécifié.

Pour plus d’informations sur les tarifs Bus des services, voir [Service Bus tarifs et facturation][].

### <a name="servicebusapiversion"></a>serviceBusApiVersion

La version de l’API de Service Bus du modèle.

```
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2015-08-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       } 
```

## <a name="resources-to-deploy"></a>Ressources pour le déploiement

### <a name="service-bus-namespace"></a>Espace de noms Bus des services

Crée un espace de noms Bus des services standard de type de **messagerie**.

```
"resources": [
    {
        "apiVersion": "[parameters('serviceBusApiVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "properties": {
        }
    }
]
```

## <a name="commands-to-run-deployment"></a>Commandes pour exécuter le déploiement

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

### <a name="azure-cli"></a>Azure infrastructure du langage commun

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé et déployé des ressources à l’aide du Gestionnaire de ressources Azure, découvrez comment gérer les ressources, lisez les articles suivants :

- [Gérer le Service Bus avec PowerShell](service-bus-powershell-how-to-provision.md)
- [Gérer les ressources de Service Bus avec l’Explorateur de Bus de Service](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)

  [Création de modèles de gestionnaire de ressources Azure]: ../resource-group-authoring-templates.md
  [Modèle d’espace de noms Bus des services]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
  [Modèles de démarrage rapide Azure]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [Service Bus tarifs et facturation]: https://azure.microsoft.com/documentation/articles/service-bus-pricing-billing/
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
