<properties
    pageTitle="Créer une règle d’autorisation Bus des services à l’aide d’un modèle de gestionnaire de ressources Azure | Microsoft Azure"
    description="Créer une règle d’autorisation Bus des services pour l’espace de noms et file d’attente à l’aide du Gestionnaire de ressources Azure modèle"
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

# <a name="create-a-service-bus-authorization-rule-for-namespace-and-queue-using-an-azure-resource-manager-template"></a>Créer une règle d’autorisation Bus des services pour l’espace de noms et file d’attente à l’aide d’un modèle de gestionnaire de ressources Azure

Cet article vous explique comment utiliser un modèle de gestionnaire de ressources Azure qui crée une [règle d’autorisation](service-bus-authentication-and-authorization.md#shared-access-signature-authentication) pour un espace de noms Bus des services et file d’attente. Vous allez découvrir comment définir les ressources sont déployés et comment définir des paramètres qui sont spécifiées lorsque le déploiement est exécuté. Vous pouvez utiliser ce modèle pour vos propres déploiements ou le personnaliser selon vos besoins.

Pour plus d’informations sur la création de modèles, voir [Gestionnaire de ressources Azure de création de modèles][].

Pour le modèle terminé, voir le [modèle de règle auth Bus des services][] sur GitHub.

>[AZURE.NOTE] Les modèles de gestionnaire de ressources Azure suivants sont disponibles pour le téléchargement et déploiement.
>
>-    [Créer un espace de noms Hubs événement avec un groupe d’événement concentrateur et consommateur](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)
>-    [Créer un espace de noms Bus des services avec file d’attente](service-bus-resource-manager-namespace-queue.md)
>-    [Créer un espace de noms Bus des services avec la rubrique et d’abonnement](service-bus-resource-manager-namespace-topic.md)
>-    [Créer un espace de noms Bus des services](service-bus-resource-manager-namespace.md)
>
>Pour vérifier les derniers modèles, visitez la galerie de [Modèles de démarrage rapide d’Azure][] et effectuez une recherche « Service marché »

## <a name="what-will-you-deploy"></a>Ce que vous allez déployer ?

Avec ce modèle, vous allez déployer une règle d’autorisation Bus des services pour un espace de noms et messagerie entité (dans ce cas, une file d’attente).

Ce modèle utilise [Partagés accès Signature (sa)](service-bus-sas-overview.md) pour l’authentification. Associations de sécurité permet aux applications de s’authentifier sur Bus des services à l’aide d’une touche d’accès configurée sur l’espace de noms, ou sur l’entité de messagerie (file d’attente ou rubrique) sont associés à des droits spécifiques. Vous pouvez ensuite utiliser cette clé pour générer un jeton de sa clients à son tour permet de s’authentifier sur Bus des services.

Pour exécuter le déploiement automatiquement, cliquez sur le bouton suivant :

[![Déploiement d’Azure](./media/service-bus-resource-manager-namespace-auth-rule/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F301-servicebus-create-authrule-namespace-and-queue%2Fazuredeploy.json)

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

### <a name="namespaceauthorizationrulename"></a>namespaceAuthorizationRuleName 

Le nom de la règle d’autorisation pour l’espace de noms.

```
"namespaceAuthorizationRuleName ": {
"type": "string"
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName

Le nom de la file d’attente dans l’espace de noms Bus des services.

```
"serviceBusQueueName": {
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

Crée un espace de noms Bus des services standard de type **messagerie**et une règle d’autorisation Bus des services pour l’espace de noms et entité.

```
"resources": [
        {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "location": "[variables('location')]",
            "kind": "Messaging",
            "sku": {
                "name": "StandardSku",
                "tier": "Standard"
            },
            "resources": [
                {
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusQueueName')]",
                    "type": "Queues",
                    "dependsOn": [
                        "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('serviceBusQueueName')]"
                    },
                    "resources": [
                        {
                            "apiVersion": "[variables('sbVersion')]",
                            "name": "[parameters('queueAuthorizationRuleName')]",
                            "type": "authorizationRules",
                            "dependsOn": [
                                "[parameters('serviceBusQueueName')]"
                            ],
                            "properties": {
                                "Rights": ["Listen"]
                            }
                        }
                    ]
                }
            ]
        }, {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[variables('namespaceAuthRuleName')]",
            "type": "Microsoft.ServiceBus/namespaces/authorizationRules",
            "dependsOn": ["[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"],
            "location": "[resourceGroup().location]",
            "properties": {
                "Rights": ["Send"]
            }
        }
    ]
```

## <a name="commands-to-run-deployment"></a>Commandes pour exécuter le déploiement

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure infrastructure du langage commun

```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé et déployé des ressources à l’aide du Gestionnaire de ressources Azure, découvrez comment gérer ces ressources en affichant les articles suivants :

- [Gérer le Service Bus avec PowerShell](service-bus-powershell-how-to-provision.md)
- [Gérer les ressources de Service Bus avec l’Explorateur de Bus de Service](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)
- [Autorisation et Bus de Service d’authentification](service-bus-authentication-and-authorization.md)

  [Création de modèles de gestionnaire de ressources Azure]: ../resource-group-authoring-templates.md
  [Modèles de démarrage rapide Azure]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
  [Modèle de règle auth Bus des services]: https://github.com/Azure/azure-quickstart-templates/blob/master/301-servicebus-create-authrule-namespace-and-queue/
