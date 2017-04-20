<properties
   pageTitle="Vue d’ensemble du fournisseur de ressources réseau | Microsoft Azure"
   description="En savoir plus sur le nouveau fournisseur de ressources de réseau dans le Gestionnaire de ressources Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="network-resource-provider"></a>Fournisseur de ressources de réseau
Besoin de sous-jacente de réussite de l’entreprise d’aujourd'hui, est la possibilité de créer et gérer des applications prenant en charge du réseau grande échelle d’une manière agile, flexible, sécurisée et répétitif. Azure Resource Manager (ARM) vous permet de créer des applications, telles qu’un ensemble unique de ressources dans les groupes de ressources. Ces ressources sont gérés par le biais des fournisseurs de ressources différents sous processeur.

Le Gestionnaire de ressources Azure repose sur les fournisseurs de ressources différents pour fournir un accès à vos ressources. Il existe trois fournisseurs de ressources principal : réseau, de stockage et de calcul. Ce document présente les caractéristiques et les avantages de fournisseur de ressources réseau, y compris :

- **Métadonnées** – vous pouvez ajouter des informations à des ressources à l’aide de balises. Ces balises peuvent être utilisées pour effectuer le suivi de l’utilisation des ressources dans plusieurs abonnements et les groupes de ressources.
- **Meilleur contrôle de votre réseau** - réseau ressources sont asynchrones et que vous pouvez contrôler les de manière plus précise. Cela signifie que vous avez plus de souplesse dans la gestion des ressources réseau.
- **Configuration plus rapide** -, car les ressources du réseau sont asynchrones, vous pouvez créer et organiser des ressources du réseau en parallèle. Cela a considérablement réduit le temps de configuration.
- **Contrôle d’accès rôle** - RBAC fournit des rôles par défaut, avec une étendue de sécurité spécifique, en plus de permettre la création de rôles personnalisés pour la gestion sécurisée.
- **Plus facile de gestion et de déploiement** , il est plus facile à déployer et gérer des applications dans la mesure où vous pouvez pouvez créer une pile d’ensemble de l’application comme un ensemble unique de ressources dans un groupe de ressources. Et plus rapides à déployer, dans la mesure où vous pouvez déployer en utilisant simplement une charge utile JSON modèle.
- **Personnalisation rapide** - vous pouvez utiliser des modèles de style déclarative pour activer la personnalisation rapide et répétitive des déploiements.
- **Personnalisation répétitif** - vous pouvez utiliser des modèles de style déclarative pour activer la personnalisation rapide et répétitive des déploiements.
- **Interfaces de gestion** , vous pouvez utiliser une des interfaces suivantes pour gérer vos ressources :
    - RESTE en fonction de l’API
    - PowerShell
    - KIT DE DÉVELOPPEMENT .NET
    - Kit de développement logiciel Node.JS
    - Java SDK
    - Azure infrastructure du langage commun
    - Portail Preview
    - PROCESSEUR modèle langue

## <a name="network-resources"></a>Ressources du réseau
Vous pouvez maintenant gérer les ressources réseau séparément, plutôt que de laisser les géré tout au long une ressource cluster unique (une machine virtuelle). Ainsi, un degré plus élevé de flexibilité et de souplesse dans une infrastructure complexe et grande échelle dans un groupe de ressources de rédaction.

Vue conceptuelle d’un exemple de déploiement impliquant une application à plusieurs niveaux est présentée ci-dessous. Chaque ressource que vous voyez, tels que cartes réseau et adresses IP publiques machines virtuelles, peut être gérée séparément.

![Modèle de ressources réseau](./media/resource-groups-networking/Figure2.png)

Chaque ressource contient un ensemble de propriétés communes, et définissez leur propriété individuelle. Les propriétés communes sont :

|Propriété|Description|Exemples de valeurs|
|---|---|---|
|**nom**|Nom de la ressource unique. Chaque type de ressource possède son propre restrictions de noms.|PIP01, VM01, NIC01|
|**emplacement**|Azure région dans lequel se trouve la ressource|westus, eastus|
|**ID**|Identification en fonction des URI unique|/Subscriptions/<subGUID>/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP|

Vous pouvez vérifier les propriétés individuelles des ressources dans les sections ci-dessous.

[AZURE.INCLUDE [virtual-networks-nrp-pip-include](../../includes/virtual-networks-nrp-pip-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-nic-include](../../includes/virtual-networks-nrp-nic-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-nsg-include](../../includes/virtual-networks-nrp-nsg-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-udr-include](../../includes/virtual-networks-nrp-udr-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-vnet-include](../../includes/virtual-networks-nrp-vnet-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-dns-include](../../includes/virtual-networks-nrp-dns-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-lb-include](../../includes/virtual-networks-nrp-lb-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-appgw-include](../../includes/virtual-networks-nrp-appgw-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-vpn-include](../../includes/virtual-networks-nrp-vpn-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-tm-include](../../includes/virtual-networks-nrp-tm-include.md)]

## <a name="management-interfaces"></a>Interfaces de gestion
Vous pouvez gérer vos ressources réseau Azure à l’aide des interfaces différentes. Dans ce document nous allons étudier câbles de ces interfaces : API REST et des modèles.

### <a name="rest-api"></a>API REST
Comme mentionné précédemment, ressources réseau peuvent être gérés par le biais de diverses interfaces, y compris l’API REST, Kit de développement .NET, Node.JS SDK, Java SDK, PowerShell, infrastructure du langage commun, portail Azure et modèles.

L’API Rest est conforme à la spécification du protocole HTTP 1.1. La structure URI générale de l’API est présentée ci-dessous :

    https://management.azure.com/subscriptions/{subscription-id}/providers/{resource-provider-namespace}/locations/{region-location}/register?api-version={api-version}

Et les paramètres entre accolades représentent les éléments suivants :

- **id de l’abonnement** - votre id d’abonnement Azure.
- **espace de fournisseur noms ressources** - espace de noms pour le fournisseur en cours d’utilisation. La valeur pour le fournisseur de ressources réseau est *Microsoft.Network*.
- **nom de la région** : nom de la zone Azure

Méthodes HTTP suivantes sont prises en charge lorsque vous faites appel à l’API REST :

- **Placer** - permet de créer une ressource d’un type de donnée, modifier une propriété de ressource ou changer une association entre les ressources.
- **GET** - utilisée pour récupérer des informations pour une ressource mis en service.
- **Supprimer** : permet de supprimer une ressource existante.

La demande et la réponse sont conformes à un format de charge utile JSON. Pour plus d’informations, voir [API de gestion des ressources Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx).

### <a name="arm-template-language"></a>PROCESSEUR modèle langue
En plus de la gestion des ressources impérative (via API ou SDK), vous pouvez également utiliser un style de programmation déclaratif pour créer et gérer des ressources réseau à l’aide de la langue du modèle processeur.

Une représentation de l’exemple d’un modèle est fournie ci-dessous :

    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "<version-number-of-template>",
      "parameters": { <parameter-definitions-of-template> },
      "variables": { <variable-definitions-of-template> },
      "resources": [ { <definition-of-resource-to-deploy> } ],
      "outputs": { <output-of-template> }    
    }

Le modèle est principalement une description JSON des ressources et les valeurs d’instance injectées via paramètres. L’exemple ci-dessous pouvant être utilisée pour créer un réseau virtuel avec 2 sous-réseaux.

    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VNET.json",
        "contentVersion": "1.0.0.0",
        "parameters" : {
          "location": {
            "type": "String",
            "allowedValues": ["East US", "West US", "West Europe", "East Asia", "South East Asia"],
            "metadata" : {
              "Description" : "Deployment location"
            }
          },
          "virtualNetworkName":{
            "type" : "string",
            "defaultValue":"myVNET",
            "metadata" : {
              "Description" : "VNET name"
            }
          },
          "addressPrefix":{
            "type" : "string",
            "defaultValue" : "10.0.0.0/16",
            "metadata" : {
              "Description" : "Address prefix"
            }

          },
          "subnet1Name": {
            "type" : "string",
            "defaultValue" : "Subnet-1",
            "metadata" : {
              "Description" : "Subnet 1 Name"
            }
          },
          "subnet2Name": {
            "type" : "string",
            "defaultValue" : "Subnet-2",
            "metadata" : {
              "Description" : "Subnet 2 name"
            }
          },
          "subnet1Prefix" : {
            "type" : "string",
            "defaultValue" : "10.0.0.0/24",
            "metadata" : {
              "Description" : "Subnet 1 Prefix"
            }
          },
          "subnet2Prefix" : {
            "type" : "string",
            "defaultValue" : "10.0.1.0/24",
            "metadata" : {
              "Description" : "Subnet 2 Prefix"
            }
          }
        },
        "resources": [
        {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[parameters('virtualNetworkName')]",
          "location": "[parameters('location')]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "[parameters('addressPrefix')]"
              ]
            },
            "subnets": [
              {
                "name": "[parameters('subnet1Name')]",
                "properties" : {
                  "addressPrefix": "[parameters('subnet1Prefix')]"
                }
              },
              {
                "name": "[parameters('subnet2Name')]",
                "properties" : {
                  "addressPrefix": "[parameters('subnet2Prefix')]"
                }
              }
            ]
          }
        }
        ]
    }

Vous avez la possibilité de fournir les valeurs de paramètre manuellement lorsque vous utilisez un modèle, ou vous pouvez utiliser un fichier de paramètres. L’exemple ci-dessous montre un ensemble possible des valeurs de paramètre à utiliser avec le modèle ci-dessus :

    {
      "location": {
          "value": "East US"
      },
      "virtualNetworkName": {
          "value": "VNET1"
      },
      "subnet1Name": {
          "value": "Subnet1"
      },
      "subnet2Name": {
          "value": "Subnet2"
      },
      "addressPrefix": {
          "value": "192.168.0.0/16"
      },
      "subnet1Prefix": {
          "value": "192.168.1.0/24"
      },
      "subnet2Prefix": {
          "value": "192.168.2.0/24"
      }
    }


Les principaux avantages de l’utilisation de modèles sont :

- Vous pouvez créer une infrastructure complexe dans un groupe de ressources dans un style déclaratif. L’orchestration de la création de ressources, notamment la gestion des dépendances, est gérée par processeur.
- L’infrastructure peut être créé de manière répétée sur différentes régions et au sein d’une région en modifiant simplement les paramètres.
- Le style déclaratif permet d’accéder à plus courte et temps d’avance dans la création de modèles et déploiement de l’infrastructure.

Pour les exemples de modèles, voir [démarrage rapide Azure modèles](https://github.com/Azure/azure-quickstart-templates).

Pour plus d’informations sur la langue du modèle processeur, voir [Azure Gestionnaire de ressources du modèle de langue](../resource-group-authoring-templates.md).

Le modèle d’exemple ci-dessus utilise le réseau virtuel et les ressources de sous-réseau. Il existe d’autres ressources réseau que vous pouvez utiliser comme indiqué ci-dessous :

### <a name="using-a-template"></a>À l’aide d’un modèle

Vous pouvez déployer des services à Azure à partir d’un modèle à l’aide de PowerShell, AzureCLI, ou en effectuant un clic pour déployer à partir de GitHub. Pour déployer les services à partir d’un modèle dans GitHub, exécutez les étapes suivantes :

1. Ouvrez le fichier template3 GitHub. Par exemple, ouvrez [réseau virtuelle avec deux sous-réseaux](https://github.com/Azure/azure-quickstart-templates/tree/master/101-virtual-network).
2. Cliquez sur **déployer vers Azure**et puis connectez-vous sur au portail Azure avec vos informations d’identification.
3. Vérifier le modèle, puis cliquez sur **Enregistrer**.
4. Cliquez sur **Modifier les paramètres** et sélectionnez un emplacement, comme *États-Unis Ouest*, pour les vnet et les sous-réseaux.
5. Si nécessaire, modifiez les paramètres **ADDRESSPREFIX** et **SUBNETPREFIX** , puis cliquez sur **OK**.
6. Cliquez sur **Sélectionner un groupe de ressources** , puis dans le groupe de ressources que vous souhaitez ajouter les vnet et les sous-réseaux à. Par ailleurs, vous pouvez créer un nouveau groupe de ressources en cliquant sur **ou créer de nouveaux**.
3. Cliquez sur **créer**. Avez-vous remarqué la vignette affichant **déploiement du modèle de mise en service**. Une fois que le déploiement est terminé, vous verrez un écran semblable à l’un ci-dessous.

![Exemple de déploiement de modèle](./media/resource-groups-networking/Figure6.png)


## <a name="next-steps"></a>Étapes suivantes

[Azure langue du modèle de gestionnaire de ressources](../resource-group-authoring-templates.md)

[Mise en réseau Azure – modèles fréquemment utilisés](https://github.com/Azure/azure-quickstart-templates)

[Calculer le fournisseur de ressources](../virtual-machines/virtual-machines-windows-compare-deployment-models.md)

[Vue d’ensemble du Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md)
