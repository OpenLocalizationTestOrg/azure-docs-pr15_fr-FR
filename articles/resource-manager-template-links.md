<properties
   pageTitle="Modèle de gestionnaire de ressources pour la liaison des ressources | Microsoft Azure"
   description="Montre le schéma de gestionnaire de ressources pour le déploiement des liens entre les ressources associées à un modèle."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/05/2016"
   ms.author="tomfitz"/>

# <a name="resource-links-template-schema"></a>Schéma de modèle de liens ressources

Crée une liaison entre deux ressources. Le lien est appliqué à une ressource appelée la ressource de code source. La deuxième ressource dans le lien est appelée la ressource cible.

## <a name="schema-format"></a>Format de schéma

Pour créer un lien, ajoutez le schéma suivant à la section de ressources de votre modèle.
    
    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "targetId": string,
            "notes": string
        }
    }



## <a name="values"></a>Valeurs

Les tableaux suivants décrivent les valeurs que vous devez définir dans le schéma.

| Nom | Valeur |
| ---- | ---- |
| type | Énumération<br />Obligatoire<br />**{espace de noms} / {type} / fournisseurs/des liens**<br /><br />Le type de ressource à créer. Les noms {} et {type} valeurs faire référence au type d’espace de noms et des ressources fournisseur de la ressource de code source. |
| apiVersion | Énumération<br />Obligatoire<br />**01 / 01/2015**<br /><br />La version de l’API à utiliser pour créer la ressource. |  
| nom | Chaîne<br />Obligatoire<br />**{resouce}/Microsoft.Resources/{linkname}**<br /> jusqu'à 64 caractères et ne peut pas contenir <>, %, &, ?, ou les caractères de contrôle.<br /><br />Valeur qui spécifie le nom de ressource source et un nom pour le lien. |
| dependsOn | Tableau<br />Facultatif<br />Une liste séparée par des virgules d’une ressource de noms ou les identificateurs uniques de la ressource.<br /><br />La collection de ce lien dépend des ressources. Si les ressources que vous souhaitez lier sont déployés dans le même modèle, inclure les noms de ressources dans cet élément pour vous assurer qu’ils sont déployés en premier. | 
| propriétés | Objet<br />Obligatoire<br />[objet de propriétés](#properties)<br /><br />Un objet qui identifie la ressource à lier à et remarques sur le lien. |  

<a id="properties" />
### <a name="properties-object"></a>objet de propriétés

| Nom | Valeur |
| ------- | ---- |
| targetId | Chaîne<br />Obligatoire<br />**{Nº ressource}**<br /><br />L’identificateur de la ressource cible à lier. |
| Notes | Chaîne<br />Facultatif<br />512 caractères maximum<br /><br />Description du verrou. |


## <a name="how-to-use-the-link-resource"></a>L’utilisation de la ressource de lien

Vous appliquez un lien entre deux ressources lorsque les ressources ont une relation qui persiste après le déploiement. Par exemple, une application peut se connecter à une base de données dans un groupe de ressources différent. Vous pouvez définir cette dépendance en créant un lien à partir de l’application à la base de données. Les liens vous permettent de vous permet de documenter la relation entre deux ressources. Plus tard, vous ou quelqu'un de votre organisation peut demander une ressource pour les liens découvrir comment la ressource interagissent avec d’autres ressources.

Toutes les ressources liées doivent appartenir à la même abonnement. Chaque ressource peut être lié à 50 d’autres ressources. Si les ressources liées sont supprimés ou déplacés, le propriétaire du lien doit nettoyer le lien restant.

Pour travailler avec des liens à travers reste, voir [Les ressources liées](https://msdn.microsoft.com/library/azure/mt238499.aspx).

Utilisez la commande Azure PowerShell suivante pour afficher tous les liens dans votre abonnement. Vous pouvez fournir d’autres paramètres pour limiter les résultats.

    Get-AzureRmResource -ResourceType Microsoft.Resources/links -isCollection -ResourceGroupName <YourResourceGroupName>

## <a name="examples"></a>Exemples

L’exemple suivant applique un verrou en lecture seule pour une application web.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "hostingPlanName": {
                "type": "string"
            }
        },
        "variables": {
            "siteName": "[concat('site',uniqueString(resourceGroup().id))]"
        },
        "resources": [
            {
                "apiVersion": "2015-08-01",
                "type": "Microsoft.Web/serverfarms",
                "name": "[parameters('hostingPlanName')]",
                "location": "[resourceGroup().location]",
                "sku": {
                    "tier": "Free",
                    "name": "f1",
                    "capacity": 0
                },
                "properties": {
                    "numberOfWorkers": 1
                }
            },
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "dependsOn": [ "[parameters('hostingPlanName')]" ],
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                }
            },
            {
                "type": "Microsoft.Web/sites/providers/links",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Resources/SiteToStorage')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties": {
                    "targetId": "[resourceId('Microsoft.Storage/storageAccounts','storagecontoso')]",
                    "notes": "This web site uses the storage account to store user information."
                }
            }
        ],
        "outputs": {}
    }

## <a name="quickstart-templates"></a>Modèles de démarrage rapide

Les modèles de démarrage rapide suivants déploiement les ressources avec un lien.

- [Alerte à file d’attente avec l’application logique](https://azure.microsoft.com/documentation/templates/201-alert-to-queue-with-logic-app)
- [Alerte de marge avec l’application logique](https://azure.microsoft.com/documentation/templates/201-alert-to-slack-with-logic-app)
- [Configurer une application API avec une passerelle existante](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-existing)
- [Configurer une application API avec une nouvelle passerelle](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-new)
- [Créer une application de l’application logique et API à l’aide d’un modèle](https://azure.microsoft.com/documentation/templates/201-logic-app-api-app-create)
- [Application logique qui envoie un message texte lorsqu’une alerte se déclenche](https://azure.microsoft.com/documentation/templates/201-alert-to-text-message-with-logic-app)


## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la structure du modèle, voir [Gestionnaire de ressources Azure de création de modèles](resource-group-authoring-templates.md).
