<properties
    pageTitle="Utilisation de balises pour organiser vos ressources Azure | Microsoft Azure"
    description="Montre comment appliquer des balises pour organiser les ressources de facturation et de gestion."
    services="azure-resource-manager"
    documentationCenter=""
    authors="tfitzmac"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="AzurePortal"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/08/2016"
    ms.author="tomfitz"/>


# <a name="using-tags-to-organize-your-azure-resources"></a>Utilisation de balises pour organiser vos ressources Azure

Gestionnaire de ressources permet d’organiser logiquement les ressources à l’aide d’indicateurs. Les balises sont constitués de paires clé/valeur qui identifient les ressources avec les propriétés que vous définissez. Pour marquer des ressources comme appartenant à la même catégorie, appliquer la même balise à ces ressources.

Lorsque vous affichez des ressources avec une balise spécifique, vous voyez des ressources et tous les groupes de ressources. Vous n’êtes pas limités à uniquement des ressources dans le même groupe de ressources, qui vous permet d’organiser vos ressources d’une manière indépendante des relations déploiement. Balises peuvent être utiles lorsque vous avez besoin organiser des ressources pour facturation ou la gestion.

Chaque balise que vous ajoutez à une ressource ou un groupe de ressources est automatiquement ajouté à la taxonomie à l’échelle de l’abonnement. Vous pouvez également préremplir la taxonomie pour votre abonnement avec les noms de balises et les valeurs que vous voulez utiliser comme ressources sont balisés à l’avenir.

Chaque ressource ou un groupe de ressources peut contenir un maximum de 15 mots clés. Le nom de balise est limité à 512 caractères, et la valeur de balise est limitée à 256 caractères.

> [AZURE.NOTE] Vous pouvez uniquement appliquer des balises à des ressources qui prennent en charge les opérations de gestionnaire de ressources. Si vous avez créé une Machine virtuelle, le réseau virtuel ou le stockage via le modèle de déploiement classique (tel que via le portail classique), vous ne pouvez pas appliquer un indicateur à cette ressource. Pour prendre en charge le marquage, redéployez ces ressources par le biais du Gestionnaire de ressources. Toutes les autres ressources prend en charge le marquage.

## <a name="templates"></a>Modèles

Pour ajouter des balises à une ressource au cours du déploiement, ajoutez l’élément de **balises** pour la ressource que vous déployez et indiquez le nom de balise et valeur. Le nom de balise et la valeur n’avez pas besoin au préalable présents dans votre abonnement. Vous pouvez fournir jusqu'à 15 mots-clés pour chaque ressource.

L’exemple suivant montre un compte de stockage avec une balise.

    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2015-06-15",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "tags": {
                "dept": "Finance"
            },
            "properties": 
            {
                "accountType": "Standard_LRS"
            }
        }
    ]

Pour l’instant, le Gestionnaire de ressources ne reconnaît pas un objet pour les noms de balises et les valeurs de traitement. Passer à la place, un objet pour les valeurs de la balise, mais vous devez spécifier les noms des balises, comme le montre l’exemple suivant.

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "tagvalues": {
          "type": "object",
          "defaultValue": {
            "dept": "Finance",
            "project": "Test"
          }
        }
      },
      "resources": [
      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Storage/storageAccounts",
        "name": "examplestorage",
        "tags": {
          "dept": "[parameters('tagvalues').dept]",
          "project": "[parameters('tagvalues').project]"
        },
        "location": "[resourceGroup().location]",
        "properties": {
          "accountType": "Standard_LRS"
        }
      }]
    }


## <a name="portal"></a>Portail

[AZURE.INCLUDE [resource-manager-tag-resource](../includes/resource-manager-tag-resources.md)]

## <a name="powershell"></a>PowerShell

[AZURE.INCLUDE [resource-manager-tag-resources](../includes/resource-manager-tag-resources-powershell.md)]

## <a name="azure-cli"></a>Azure infrastructure du langage commun

[AZURE.INCLUDE [resource-manager-tag-resources-cli](../includes/resource-manager-tag-resources-cli.md)]

## <a name="rest-api"></a>API REST

Le portail et PowerShell utilisent l' [REST API Gestionnaire de ressources](https://msdn.microsoft.com/library/azure/dn848368.aspx) en coulisses. Si vous avez besoin intégrer le marquage dans un autre environnement, vous pouvez obtenir des balises avec une commande GET sur l’id de ressource et mettre à jour le jeu de balises avec un appel correctif.


## <a name="tags-and-billing"></a>Balises et facturation

Pour les services pris en charge, vous pouvez utiliser des balises pour regrouper les données de facturation. Par exemple, [Machines virtuelles intégré avec le Gestionnaire de ressources Azure](./virtual-machines/virtual-machines-windows-compare-deployment-models.md) permettent de définir et appliquer des balises pour organiser l’utilisation de la facturation des machines virtuelles. Si vous exécutez plusieurs machines virtuelles pour différentes organisations, vous pouvez utiliser les balises pour l’utilisation des groupes par département.  
Vous pouvez également utiliser des indicateurs pour classer les coûts par environnement d’exécution ; par exemple, l’utilisation de facturation pour les machines virtuelles en cours d’exécution dans un environnement de production.

Vous pouvez récupérer des informations sur les balises via l' [utilisation des ressources Azure et API RateCard](billing-usage-rate-card-overview.md) ou le fichier de valeurs séparées par des virgules (CSV) l’utilisation. Vous téléchargez le fichier de l’utilisation du [portail de comptes Azure](https://account.windowsazure.com/) ou [portail EA](https://ea.azure.com). Pour plus d’informations sur l’accès par programme aux informations de facturation, voir [Familiarisez-vous dans votre consommation de ressources de Microsoft Azure](billing-usage-rate-card-overview.md). Pour les opérations de l’API REST, voir [Référence des API REST facturation Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Lorsque vous téléchargez l’utilisation CSV pour les services qui prennent en charge les balises de facturation, les balises apparaissent dans la colonne **indicateurs** . Pour plus d’informations, voir [comprendre votre facture pour Microsoft Azure](billing/billing-understand-your-bill.md).

![Voir les indicateurs dans facturation](./media/resource-group-using-tags/billing_csv.png)

## <a name="next-steps"></a>Étapes suivantes

- Vous pouvez appliquer des restrictions et conventions au sein de votre abonnement avec des stratégies personnalisées. La stratégie que vous définissez peut nécessiter que toutes les ressources ont une valeur d’une balise particulière. Pour plus d’informations, voir [Utiliser une stratégie pour gérer les ressources et contrôler l’accès](resource-manager-policy.md).
- Pour une présentation à l’aide de PowerShell Azure lors du déploiement de ressources, voir [Utilisation de PowerShell Azure avec le Gestionnaire de ressources Azure](./powershell-azure-resource-manager.md).
- Pour une présentation à l’aide de Azure infrastructure du langage commun lors du déploiement de ressources, reportez-vous [à l’aide de l’infrastructure du langage commun Azure pour Mac, Linux et Windows Azure Gestion des ressources](./xplat-cli-azure-resource-manager.md).
- Pour une présentation à l’aide du portail, consultez [l’aide du portail pour gérer vos ressources Azure Azure](./azure-portal/resource-group-portal.md)  
