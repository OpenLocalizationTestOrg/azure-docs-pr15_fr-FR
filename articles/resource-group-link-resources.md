<properties 
    pageTitle="Liaison de ressources dans le Gestionnaire de ressources Azure | Microsoft Azure" 
    description="Créer un lien entre les ressources associées dans différents groupes de ressources dans le Gestionnaire de ressources Azure." 
    services="azure-resource-manager" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="timlt" 
    editor="tysonn"/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/01/2016" 
    ms.author="tomfitz"/>

# <a name="linking-resources-in-azure-resource-manager"></a>Liaison de ressources dans le Gestionnaire de ressources Azure

Au cours du déploiement, vous pouvez marquer une ressource comme dépendant d’une autre ressource, mais ce cycle de vie se termine au déploiement. Après le déploiement, il n’existe aucune relation identifiée entre les ressources dépendantes. Gestionnaire de ressources fournit une fonctionnalité appelée ressource liaison pour établir des relations permanentes entre les ressources.

Avec la liaison de la ressource, vous pouvez documenter relations qui s’étalent sur groupes de ressources. Par exemple, il est courant pour une base de données avec son propre cycle de vie résident dans un groupe de ressources, et une application avec un autre cycle de vie dans un groupe de ressources différent. L’application se connecte à la base de données afin que vous souhaitez marquer un lien entre l’application et la base de données. 

Toutes les ressources liées doivent appartenir à la même abonnement. Chaque ressource peut être lié à 50 d’autres ressources. La seule façon pour interroger des ressources connexes consiste à l’API REST. Si les ressources liées sont supprimés ou déplacés, le propriétaire du lien doit nettoyer le lien restant. Vous êtes **pas** un message d’avertissement quand la suppression d’une ressource qui est liée à d’autres ressources.

## <a name="linking-in-templates"></a>Liaison dans les modèles

Pour définir un lien dans un modèle, vous incluez un type de ressource qui combine l’espace de noms de fournisseur de ressources et type de la ressource de code source avec **/providers/links**. Le nom doit inclure le nom de la ressource de code source. Vous fournir l’id de la ressource de la ressource cible. L’exemple suivant établit une liaison entre un site web et un compte de stockage.

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


Pour une description complète du format de modèle, voir [liens vers les ressources - schéma de modèle](resource-manager-template-links.md).

## <a name="linking-with-rest-api"></a>Liaison avec l’API REST

Pour définir un lien entre les ressources déployées, exécutez :

    PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/providers/Microsoft.Resources/links/{link-name}?api-version={api-version}

Remplacez {-id de l’abonnement} avec votre id de l’abonnement. Remplacer {-groupe de ressources}, {fournisseur-espace de noms, {-type de ressource} et {-nom de la ressource} avec les valeurs qui identifient la première ressource dans le lien. Remplacez {nom du lien} par le nom de la liaison à créer. Utiliser 2015-01-01 pour la version de l’api.

Dans la demande, incluez un objet qui définit la deuxième ressource dans le lien :

    {
        "name": "{new-link-name}",
        "properties": {
            "targetId": "subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/",
            "notes": "{link-description}",
        }
    }

L’élément properties contient l’identificateur pour la deuxième ressource.

Vous pouvez interroger des liens dans votre abonnement avec :

    https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Resources/links?api-version={api-version}

Pour plus d’exemples, y compris comment récupérer les informations relatives aux liens, consultez [Les ressources liées](https://msdn.microsoft.com/library/azure/mt238499.aspx).

## <a name="next-steps"></a>Étapes suivantes

- Vous pouvez également organiser vos ressources avec des balises. Pour en savoir plus sur les ressources liaison, voir [en utilisant des balises pour organiser vos ressources](resource-group-using-tags.md).
- Pour obtenir une description de la création de modèles et définir les ressources à déployer, voir [modèles de création](resource-group-authoring-templates.md).
