<properties
    pageTitle="Créer un espace de noms Hubs événement avec concentrateur événement et consommateur le groupe à l’aide d’un modèle de gestionnaire de ressources Azure | Microsoft Azure"
    description="Créer un espace de noms Hubs événement avec concentrateur de l’événement et un groupe de fournisseurs à l’aide du Gestionnaire de ressources Azure modèle"
    services="event-hubs"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="08/31/2016"
    ms.author="sethm;shvija"/>

# <a name="create-an-event-hubs-namespace-with-event-hub-and-consumer-group-using-an-azure-resource-manager-template"></a>Créer un espace de noms Hubs événement avec concentrateur événement et consommateur le groupe à l’aide d’un modèle de gestionnaire de ressources Azure

Cet article vous explique comment utiliser un modèle de gestionnaire de ressources Azure qui crée un espace de noms Hubs événement avec un concentrateur de l’événement et un groupe grand public. Vous allez découvrir comment définir les ressources sont déployés et comment définir des paramètres qui sont spécifiées lorsque le déploiement est exécuté. Vous pouvez utiliser ce modèle pour vos propres déploiements ou le personnaliser selon vos besoins

Pour plus d’informations sur la création de modèles, voir [Gestionnaire de ressources Azure de création de modèles][].

Pour le modèle terminé, voir [consommateur groupe modèle et un concentrateur événement][] sur GitHub.

>[AZURE.NOTE]
>Pour vérifier les derniers modèles, visitez la galerie de [Modèles de démarrage rapide d’Azure][] et effectuez une recherche Hubs événement.

## <a name="what-will-you-deploy"></a>Ce que vous allez déployer ?

Avec ce modèle, vous allez déployer un espace de noms Hubs événement avec un concentrateur de l’événement et un groupe grand public.

[Événement Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) est un service utilisé pour fournir des événements et télémétrie pénétration Azure au grande échelle avec faible latence et haute fiabilité de traitement des événements.

Pour exécuter le déploiement automatiquement, cliquez sur le bouton suivant :

[![Déploiement d’Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

## <a name="parameters"></a>Paramètres

Avec le Gestionnaire de ressources Azure, vous définissez les paramètres pour les valeurs que vous souhaitez spécifier lorsque le modèle est déployé. Le modèle comprend une section intitulée `Parameters` qui contient toutes les valeurs de paramètre. Vous devez définir un paramètre pour les valeurs qui varient basée sur le projet que vous déployez ou sur l’environnement que vous effectuez le déploiement. Ne définissez pas les paramètres pour les valeurs qui sera toujours la même. Chaque valeur de paramètre permet de définir les ressources qui sont déployés dans le modèle.

Le modèle définit les paramètres suivants.

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

Le nom de l’espace de noms Hubs événement à créer.

```
"eventHubNamespaceName": {
"type": "string"
}
```

### <a name="eventhubname"></a>eventHubName

Le nom du concentrateur événement créé dans l’événement Hubs espace de noms.

```
"eventHubName": {
"type": "string"
}
```

### <a name="eventhubconsumergroupname"></a>eventHubConsumerGroupName

Le nom du groupe consommateur créé pour le Hub de l’événement.

```
"eventHubConsumerGroupName": {
"type": "string"
}
```

### <a name="apiversion"></a>apiVersion

La version de l’API du modèle.

```
"apiVersion": {
"type": "string"
}
```

## <a name="resources-to-deploy"></a>Ressources pour le déploiement

Crée un espace de noms du type **EventHubs**, avec un concentrateur de l’événement et un groupe grand public.

```
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('namespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources":[  
            {  
               "apiVersion":"[variables('ehVersion')]",
               "name":"[parameters('eventHubName')]",
               "type":"EventHubs",
               "dependsOn":[  
                  "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]"
               },
               "resources":[  
                  {  
                     "apiVersion":"[variables('ehVersion')]",
                     "name":"[parameters('consumerGroupName')]",
                     "type":"ConsumerGroups",
                     "dependsOn":[  
                        "[parameters('eventHubName')]"
                     ],
                     "properties":{  

                     }
                  }
               ]
            }
         ]
      }
   ],
```

## <a name="commands-to-run-deployment"></a>Commandes pour exécuter le déploiement

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json
```

## <a name="azure-cli"></a>Azure infrastructure du langage commun

```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json][]
```

[Création de modèles de gestionnaire de ressources Azure]: ../resource-group-authoring-templates.md
[Modèles de démarrage rapide Azure]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Modèle de l’événement concentrateur et consommateur du groupe]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
