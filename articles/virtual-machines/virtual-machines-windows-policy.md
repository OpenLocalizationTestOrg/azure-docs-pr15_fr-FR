<properties
    pageTitle="Appliquer des stratégies pour Azure Gestionnaire de ressources virtuelles Machines | Microsoft Azure"
    description="Comment appliquer une stratégie pour une Machine virtuelle Azure Gestionnaire de ressources Windows"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/13/2016"
    ms.author="singhkay"/>

# <a name="apply-policies-to-azure-resource-manager-virtual-machines"></a>Appliquer des stratégies pour ordinateurs de virtuels Gestionnaire de ressources Azure

En utilisant des stratégies, une organisation peut appliquer différents conventions et règles dans l’ensemble de l’entreprise. Application du comportement souhaité permettent de réduire les risques tout en faisant partie de la réussite de l’organisation. Cet article décrit comment vous pouvez utiliser le Gestionnaire de ressources Azure stratégies pour définir le comportement souhaité pour les Machines virtuelles de votre organisation.

Le plan pour les étapes de cette phase est comme ci-dessous

1. Stratégie de gestionnaire de ressources Azure 101
2. Définir une stratégie pour votre Machine virtuelle
3. Créer la stratégie
4. Appliquer la stratégie

## <a name="azure-resource-manager-policy-101"></a>Stratégie de gestionnaire de ressources Azure 101

Pour se familiariser avec le Gestionnaire de ressources Azure stratégies, nous vous recommandons de lire l’article ci-dessous et puis poursuivre les étapes décrites dans cet article. L’article ci-dessous décrit la définition de base et la structure d’une stratégie, comment stratégies évaluée et vous propose divers exemples de définitions de stratégie.

* [Utiliser la stratégie pour gérer les ressources et contrôler l’accès](../resource-manager-policy.md)

## <a name="define-a-policy-for-your-virtual-machine"></a>Définir une stratégie pour votre Machine virtuelle

Parmi les scénarios courants pour une entreprise peut-être être pour autoriser uniquement les utilisateurs à créer des Machines virtuelles à partir des systèmes d’exploitation spécifiques qui ont été testés pour être compatibles avec une application métier. À l’aide d’une stratégie de gestionnaire de ressources Azure cette tâche peut être accomplie en quelques étapes. Dans cet exemple de stratégie, nous allons autoriser uniquement Windows Server 2012 R2 centre de données Machines virtuelles à créer. La définition de stratégie ressemble à ci-dessous

```
"if": {
  "allOf": [
    {
      "field": "type",
      "equals": "Microsoft.Compute/virtualMachines"
    },
    {
      "not": {
        "allOf": [
          {
            "field": "Microsoft.Compute/virtualMachines/imagePublisher",
            "equals": "MicrosoftWindowsServer"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageOffer",
            "equals": "WindowsServer"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageSku",
            "equals": "2012-R2-Datacenter"
          }
        ]
      }
    }
  ]
},
"then": {
  "effect": "deny"
}
```

La stratégie ci-dessus peut être modifiée facilement à un scénario où vous pouvez souhaiter autoriser n’importe quelle image centre de données Windows Server à utiliser pour un déploiement Machine virtuelle avec l’en dessous de modification

```
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*Datacenter"
}
```

#### <a name="virtual-machine-property-fields"></a>Champs de propriété Machine virtuelle

Le tableau ci-dessous décrit les propriétés de la Machine virtuelle qui peuvent être utilisées en tant que champs dans la définition de votre stratégie. Pour plus d’informations sur les champs de la stratégie, consultez l’article ci-dessous :

* [Champs et Sources](../resource-manager-policy.md#fields-and-sources)


| Nom de champ     | Description                                        |
|----------------|----------------------------------------------------|
| imagePublisher | Spécifie l’éditeur de l’image               |
| imageOffer     | Spécifie l’offre pour l’éditeur d’image sélectionné |
| imageSku       | Spécifie la référence (SKU) pour l’offre choisie             |
| imageVersion   | Spécifie la version de l’image pour la référence (SKU) choisie     |

## <a name="create-the-policy"></a>Créer la stratégie

Une stratégie peut être facilement créée à l’aide de l’API REST directement ou les applets de commande PowerShell. Pour créer la stratégie, consultez l’article ci-dessous :

* [Création d’une stratégie](../resource-manager-policy.md#creating-a-policy)


## <a name="apply-the-policy"></a>Appliquer la stratégie

Après avoir créé la stratégie, vous devez appliquer sur l’étendue. L’étendue peut être un abonnement, groupe de ressources ou même la ressource. Pour appliquer la stratégie, consultez l’article ci-dessous :

* [Création d’une stratégie](../resource-manager-policy.md#applying-a-policy)
