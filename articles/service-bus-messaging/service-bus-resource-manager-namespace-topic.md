<properties
    pageTitle="Créer un espace de noms Bus des services avec la rubrique et d’abonnement à l’aide d’un modèle de gestionnaire de ressources Azure | Microsoft Azure"
    description="Créer un espace de noms Bus des services avec la rubrique et d’abonnement à l’aide du Gestionnaire de ressources Azure modèle"
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
    ms.date="10/14/2016"
    ms.author="sethm;shvija"/>

# <a name="create-a-service-bus-namespace-with-topic-and-subscription-using-an-azure-resource-manager-template"></a>Créer un espace de noms Bus des services avec la rubrique et d’abonnement à l’aide d’un modèle de gestionnaire de ressources Azure

Cet article vous explique comment utiliser un modèle de gestionnaire de ressources Azure qui crée un espace de noms Bus des services avec une rubrique et d’abonnement. Vous allez découvrir comment définir les ressources sont déployés et comment définir des paramètres qui sont spécifiées lorsque le déploiement est exécuté. Vous pouvez utiliser ce modèle pour vos propres déploiements ou le personnaliser selon vos besoins

Pour plus d’informations sur la création de modèles, voir [Gestionnaire de ressources Azure de création de modèles][].

Pour le modèle terminé, voir le modèle [d’espace de noms de Service Bus avec rubrique et d’abonnement][] .

>[AZURE.NOTE] Les modèles de gestionnaire de ressources Azure suivants sont disponibles pour le téléchargement et déploiement.
>
>-    [Créer un espace de noms Bus des services avec règle file d’attente et d’autorisation](service-bus-resource-manager-namespace-auth-rule.md)
>-    [Créer un espace de noms Bus des services avec file d’attente](service-bus-resource-manager-namespace-queue.md)
>-    [Créer un espace de noms Bus des services](service-bus-resource-manager-namespace.md)
>-    [Créer un espace de noms Hubs événement avec un groupe d’événement concentrateur et consommateur](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)
>
>Pour vérifier les derniers modèles, visitez la galerie de [Modèles de démarrage rapide d’Azure][] et effectuez une recherche « Service marché »

## <a name="what-will-you-deploy"></a>Ce que vous allez déployer ?

Avec ce modèle, vous allez déployer un espace de noms Bus des services avec la rubrique et d’abonnement.

[Abonnements et les rubriques de Bus des services](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) fournissent un formulaire un-à-plusieurs de communication, dans un modèle de *publication et d’abonnement* .

Pour exécuter le déploiement automatiquement, cliquez sur le bouton suivant :

[![Déploiement d’Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-and-subscription%2Fazuredeploy.json)

## <a name="parameters"></a>Paramètres

Avec le Gestionnaire de ressources Azure, vous définissez les paramètres pour les valeurs que vous souhaitez spécifier lorsque le modèle est déployé. Le modèle comprend une section intitulée `Parameters` qui contient toutes les valeurs de paramètre. Vous devez définir un paramètre pour les valeurs qui varient basée sur le projet que vous déployez ou sur l’environnement que vous effectuez le déploiement. Ne définissez pas les paramètres pour les valeurs qui sera toujours la même. Chaque valeur de paramètre permet de définir les ressources qui sont déployés dans le modèle.

Le modèle définit les paramètres suivants.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

Le nom de l’espace de noms Bus des services pour créer.

```
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName

Le nom de la rubrique créé dans l’espace de noms Bus des services.

```
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName

Le nom de l’abonnement créé dans l’espace de noms Bus des services.

```
"serviceBusSubscriptionName": {
"type": "string"
}
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion

La version de l’API de Service Bus du modèle.

```
"serviceBusApiVersion": {
"type": "string"
}
```
## <a name="resources-to-deploy"></a>Ressources pour le déploiement

Crée un espace de noms Bus des services standard de type **messagerie**, avec la rubrique et d’abonnement.

```
"resources ": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusTopicName')]",
            "type": "Topics",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusTopicName')]",
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {}
            }]
        }]
    }]
```

## <a name="commands-to-run-deployment"></a>Commandes pour exécuter le déploiement

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure infrastructure du langage commun

```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé et déployé des ressources à l’aide du Gestionnaire de ressources Azure, découvrez comment gérer ces ressources en affichant les articles suivants :

- [Gérer le Service Bus avec PowerShell](service-bus-powershell-how-to-provision.md)
- [Gérer les ressources de Service Bus avec l’Explorateur de Bus de Service](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)


  [Création de modèles de gestionnaire de ressources Azure]: ../resource-group-authoring-templates.md
  [Modèles de démarrage rapide Azure]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
  [Espace de noms de service Bus avec la rubrique et d’abonnement]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-and-subscription/
