<properties
    pageTitle="Créer un espace de noms Bus des services avec la rubrique d’abonnement et la règle à l’aide d’un modèle de gestionnaire de ressources Azure | Microsoft Azure"
    description="Créer un espace de noms Bus des services avec la rubrique abonnement et la règle à l’aide du Gestionnaire de ressources Azure modèle"
    services="service-bus"
    documentationCenter=".net"
    authors="ShubhaVijayasarathy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="10/25/2016"
    ms.author="ShubhaVijayasarathy"/>

# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>Créer un espace de noms Bus des services avec la rubrique abonnement et la règle à l’aide d’un modèle de gestionnaire de ressources Azure

Cet article vous explique comment utiliser un modèle de gestionnaire de ressources Azure qui crée un espace de noms Bus des services avec une rubrique, abonnement et règle (filtre). Vous découvrez comment définir les ressources sont déployés et comment définir des paramètres qui sont spécifiées lorsque le déploiement est exécuté. Vous pouvez utiliser ce modèle pour vos propres déploiements ou le personnaliser selon vos besoins

Pour plus d’informations sur la création de modèles, voir [Gestionnaire de ressources Azure de création de modèles][].

Pour plus d’informations sur les pratiques et les motifs de ressources Azure conventions d’affectation de noms, consultez [Conventions d’affectation des noms de ressources Azure][].

Pour le modèle terminé, voir le modèle [d’espace de noms de Service Bus avec sujet, abonnement et règle][] .

>[AZURE.NOTE] Les modèles de gestionnaire de ressources Azure suivants sont disponibles pour le téléchargement et déploiement.
>
>-    [Créer un espace de noms Bus des services avec règle file d’attente et d’autorisation](service-bus-resource-manager-namespace-auth-rule.md)
>-    [Créer un espace de noms Bus des services avec file d’attente](service-bus-resource-manager-namespace-queue.md)
>-    [Créer un espace de noms Bus des services](service-bus-resource-manager-namespace.md)
>-    [Créer un espace de noms Bus des services avec la rubrique et d’abonnement](service-bus-resource-manager-namespace-topic.md)
>
>Pour vérifier les derniers modèles, visitez la galerie de [Modèles de démarrage rapide d’Azure][] et recherche Bus des services.

## <a name="what-will-you-deploy"></a>Ce que vous allez déployer ?

Avec ce modèle, vous déployez un espace de noms Bus des services avec la rubrique abonnement et règle (filtre).

[Abonnements et les rubriques de Bus des services](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) fournissent un formulaire un-à-plusieurs de communication, dans un modèle de *publication et d’abonnement* . Lorsque vous utilisez des rubriques et des abonnements et composants d’une application distribuée ne pas communiquent directement entre eux, à la place qu’ils échangent des messages via la rubrique qui se comporte comme un intermédiaire. Un abonnement à un sujet ressemble à une file d’attente virtuelle qui reçoit une copie des messages qui ont été envoyés à la rubrique. Un filtre sur abonnement permet de spécifier lesquelles messages envoyés à un sujet de doivent apparaître un abonnement sujet spécifique.

## <a name="what-are-rules-filters"></a>Quelles sont les règles (filtres) ?

Dans de nombreux scénarios, les messages qui ont des caractéristiques spécifiques doivent être traités de différentes manières. Pour ce faire, vous pouvez configurer des abonnements pour rechercher les messages qui ont besoin de propriétés, puis effectuez certaines modifications aux propriétés. Bien que les abonnements Bus des services afficher tous les messages envoyés à la rubrique, vous pouvez copier uniquement un sous-ensemble de ces messages dans la file d’attente virtuelle abonnement. Cela revient à l’aide de filtres de l’abonnement. Pour plus d’informations sur rules(filters), voir [abonnements, rubriques et les files d’attente Bus des services][].

Pour exécuter le déploiement automatiquement, cliquez sur le bouton suivant :

[![Déploiement d’Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>Paramètres

Avec le Gestionnaire de ressources Azure, vous devez définir des paramètres pour les valeurs que vous souhaitez spécifier lorsque le modèle est déployé. Le modèle comprend une section intitulée `Parameters` qui contient toutes les valeurs de paramètre. Vous devez définir un paramètre pour les valeurs qui varient basée sur le projet que vous déployez ou sur l’environnement que vous effectuez le déploiement. Ne définissez pas les paramètres pour les valeurs qui toujours restent les mêmes. Chaque valeur de paramètre permet de définir les ressources qui sont déployés dans le modèle.

Le modèle définit les paramètres suivants :

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
### <a name="servicebusrulename"></a>serviceBusRuleName

Le nom de la rule(filter) créé dans l’espace de noms Bus des services.

```
   "serviceBusRuleName": {
   "type": "string",
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

Crée un espace de noms Bus des services standard de type **messagerie**, avec la rubrique et les règles et abonnement.

```
 "resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "sku": {
            "name": "Standard",
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
                "path": "[parameters('serviceBusTopicName')]"
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {},
                "resources": [{
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusRuleName')]",
                    "type": "Rules",
                    "dependsOn": [
                        "[parameters('serviceBusSubscriptionName')]"
                    ],
                    "properties": {
                        "filter": {
                            "sqlExpression": "StoreName = 'Store1'"
                        },
                        "action": {
                            "sqlExpression": "set FilterTag = 'true'"
                        }
                    }
                }]
            }]
        }]
    }]
```

## <a name="commands-to-run-deployment"></a>Commandes pour exécuter le déploiement

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure infrastructure du langage commun

```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé et déployé des ressources à l’aide du Gestionnaire de ressources Azure, découvrez comment gérer ces ressources en affichant les articles suivants :

- [Gérer les Bus des services Azure à l’aide de Automation Azure](service-bus-automation-manage.md)
- [Gérer le Service Bus avec PowerShell](service-bus-powershell-how-to-provision.md)
- [Gérer les ressources de Service Bus avec l’Explorateur de Bus de Service](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)


  [Création de modèles de gestionnaire de ressources Azure]: ../resource-group-authoring-templates.md
  [Modèles de démarrage rapide Azure]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
  [Conventions d’appellation de ressources Azure]: https://azure.microsoft.com/en-us/documentation/articles/guidance-naming-conventions/
  [Espace de noms de service Bus avec rubrique abonnement et la règle]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-subscription-rule/
  [Abonnements, rubriques et files d’attente Bus des services]:service-bus-queues-topics-subscriptions.md
  
