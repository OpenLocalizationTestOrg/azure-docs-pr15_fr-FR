<properties
    pageTitle="Activer automatiquement les paramètres de Diagnostic à l’aide d’un modèle de gestionnaire de ressources | Microsoft Azure"
    description="Découvrez comment utiliser un modèle de gestionnaire de ressources pour créer les paramètres de diagnostic qui vous permet de diffuser votre les journaux de diagnostic aux événements Hubs ou les stocker dans un compte de stockage."
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="johnkem"/>

# <a name="automatically-enable-diagnostic-settings-at-resource-creation-using-a-resource-manager-template"></a>Activer automatiquement les paramètres de Diagnostic lors de la création de ressources à l’aide d’un modèle de gestionnaire de ressources
Dans cet article, nous affichons comment vous pouvez utiliser un [modèle Azure le Gestionnaire de ressources](../resource-group-authoring-templates.md) pour configurer les paramètres de Diagnostic sur une ressource lorsqu’il est créé. Cela vous permet de démarrer automatiquement la diffusion en continu vos journaux de Diagnostic et les mesures pour événement Hubs, leur archivage dans un compte de stockage ou en les envoyant à Analytique journal lorsqu’une ressource est créée.

La méthode d’activation des journaux de Diagnostic à l’aide d’un modèle de gestionnaire de ressources dépend du type de ressource.

- Ressources **Non cluster** (par exemple, les groupes de sécurité réseau, applications logique, Automation) utilisent [les paramètres de Diagnostic décrites dans cet article](./monitoring-overview-of-diagnostic-logs.md#diagnostic-settings).
- **Calculer** Ressources (WAD/SCÉNARISTE basées) utilisent le [fichier de configuration WAD/SCÉNARISTE décrit dans cet article](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md).

Dans cet article, nous décrivent comment configurer les diagnostics à l’aide de des méthodes.

Les étapes de base sont les suivantes :

1. Créer un modèle sous forme de fichier JSON qui décrit comment créer la ressource et activer des diagnostics.
2. [Déployer le modèle à l’aide de n’importe quelle méthode de déploiement](../resource-group-template-deploy.md).

Ci-dessous nous donnez un exemple de fichier JSON modèle que vous devez pour générer pour non cluster et ressources de calcul.

## <a name="non-compute-resource-template"></a>Modèle de ressource non cluster
Pour les ressources non cluster, vous devez effectuer deux opérations :

1. Ajouter des paramètres pour le blob de paramètres pour le nom de compte de stockage, ID de la règle service bus et/ou les ID d’espace de travail OMS journal Analytique (l’archivage des journaux de Diagnostic dans un compte de stockage, la diffusion en continu de journaux à événement Hubs et/ou envoi des journaux au journal Analytique).

    ```json
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
      }
    },
    "workspaceId":{
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
    ```
2. Dans le tableau des ressources de la ressource pour laquelle vous souhaitez activer les journaux de Diagnostic, ajoutez une ressource de type `[resource namespace]/providers/diagnosticSettings`.

    ```json
    "resources": [
      {
        "type": "providers/diagnosticSettings",
        "name": "Microsoft.Insights/service",
        "dependsOn": [
          "[/*resource Id for which Diagnostic Logs will be enabled>*/]"
        ],
        "apiVersion": "2015-07-01",
        "properties": {
          "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
          "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
          "workspaceId": "[parameters('workspaceId')]",
          "logs": [ 
            {
              "category": "/* log category name */",
              "enabled": true,
              "retentionPolicy": {
                "days": 0,
                "enabled": false
              }
            }
          ]
        }
      }
    ]
    ```

Le blob de propriétés pour le paramètre de Diagnostic suit [le format décrit dans cet article](https://msdn.microsoft.com/library/azure/dn931931.aspx).

Voici un exemple complet qui crée un groupe de sécurité réseau et se transforme en continu sur événement Hubs et le stockage dans un compte de stockage.

```json

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "nsgName": {
      "type": "string",
      "metadata": {
        "description": "Name of the NSG that will be created."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
      }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "[parameters('nsgName')]",
      "apiVersion": "2016-03-30",
      "location": "westus",
      "properties": {
        "securityRules": []
      },
      "resources": [
        {
          "type": "providers/diagnosticSettings",
          "name": "Microsoft.Insights/service",
          "dependsOn": [
            "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('nsgName'))]"
          ],
          "apiVersion": "2015-07-01",
          "properties": {
            "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
            "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
            "workspaceId": "[parameters('workspaceId')]",
            "logs": [
              {
                "category": "NetworkSecurityGroupEvent",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              },
              {
                "category": "NetworkSecurityGroupRuleCounter",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              }
            ]
          }
        }
      ],
      "dependsOn": []
    }
  ]
}

```

## <a name="compute-resource-template"></a>Calculer le modèle de ressources
Pour activer les diagnostics sur une ressource de cluster, par exemple un cluster Machine virtuelle ou tissu de Service, vous devez :

1. Ajouter l’extension Azure Diagnostics à la définition de ressources machine virtuelle.
2. Spécifiez un concentrateur de stockage compte et/ou événement en tant que paramètre.
3. Ajouter le contenu de votre fichier WADCfg XML dans la propriété XMLCfg, la séquence d’échappement tous les caractères XML correctement.

> [AZURE.WARNING] Cette dernière étape peut s’avérer difficile à réaliser correctement. [Consultez cet article](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md#diagnostics-configuration-variables) pour obtenir un exemple qui fractionne le schéma de Configuration Diagnostics en variables d’échappement et correctement mis en forme.

L’ensemble du processus, notamment les exemples, est décrite [dans ce document](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md).


## <a name="next-steps"></a>Étapes suivantes
- [En savoir plus sur les journaux de Diagnostic Azure](./monitoring-overview-of-diagnostic-logs.md)
- [Les journaux de Diagnostic Azure aux événements Hubs de flux](./monitoring-stream-diagnostic-logs-to-event-hubs.md)
