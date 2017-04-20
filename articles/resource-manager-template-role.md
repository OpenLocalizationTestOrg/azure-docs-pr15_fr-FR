<properties
   pageTitle="Modèle de gestionnaire de ressources des affectations de rôle | Microsoft Azure"
   description="Montre le schéma de gestionnaire de ressources pour le déploiement d’une attribution de rôle à un modèle."
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
   ms.date="10/03/2016"
   ms.author="tomfitz"/>

# <a name="role-assignments-template-schema"></a>Schéma de modèle attributions de rôle

Affecte un utilisateur, groupe ou principal du service à un rôle à une portée spécifiée.

## <a name="resource-format"></a>Format de ressources

Pour créer une attribution de rôle, ajoutez le schéma suivant à la section de ressources de votre modèle.
    
    {
        "type": string,
        "apiVersion": "2015-07-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "roleDefinitionId": string,
            "principalId": string,
            "scope": string
        }
    }

## <a name="values"></a>Valeurs

Les tableaux suivants décrivent les valeurs que vous devez définir dans le schéma.

| Nom | Obligatoire | Description |
| ---- | -------- | ----------- |
| type | Oui    | Le type de ressource à créer.<br /><br /> Pour le groupe de ressources :<br />**Microsoft.Authorization/roleAssignments**<br /><br />Pour une ressource :<br />**{noms fournisseur} / {type de ressource} / fournisseurs/roleAssignments** |
| apiVersion |Oui | La version de l’API à utiliser pour créer la ressource.<br /><br /> Utilisez **2015-07-01**. | 
| nom | Oui | Un identificateur globalement unique pour la nouvelle attribution de rôle. |
| dependsOn | N° | Une matrice séparées par des virgules d’une ressource de noms ou les identificateurs uniques de la ressource.<br /><br />La collection de ressources que dépend de cette attribution de rôle. Si l’attribution d’un rôle qui portée à une ressource et déployées dans le modèle même ressource, inclure ce nom de la ressource dans cet élément afin de garantir que la ressource est déployée tout d’abord. |
| propriétés | Oui | L’objet de propriétés qui identifie la définition de rôle, principal et étendue. |

### <a name="properties-object"></a>objet de propriétés

| Nom | Obligatoire | Description |
| ---- | -------- | ----------- |
| roleDefinitionId | Oui |  L’identificateur d’une définition de rôle existante à utiliser dans l’attribution de rôle.<br /><br /> Utilisez le format suivant :<br /> **/ subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}** |
| principalId | Oui | L’identificateur unique pour une identité existante. Cette valeur correspond à l’id de l’annuaire et peut pointer vers un utilisateur, principal de service ou groupe de sécurité. |
| étendue | N° | L’étendue à laquelle cette attribution de rôle s’applique à.<br /><br />Pour les groupes de ressources, utilisez :<br />**/resourceGroups/ /Subscriptions/ {-id de l’abonnement} {nom de groupe ressource}**  <br /><br />Pour les ressources, utilisez :<br />**/ subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}** |  |


## <a name="how-to-use-the-role-assignment-resource"></a>L’utilisation de la ressource d’affectation de rôles

Vous ajoutez une attribution de rôle à votre modèle lorsque vous devez ajouter un utilisateur, un groupe ou un service principal à un rôle pendant le déploiement. Attributions de rôle sont héritées de niveaux supérieurs d’étendue, si vous avez déjà ajouté une entité à un rôle au niveau de l’abonnement, vous n’avez pas besoin de réaffecter pour le groupe de ressources ou la ressource.

Il existe plusieurs valeurs de l’identificateur que vous devez fournir lorsque vous travaillez avec les attributions de rôle. Vous pouvez récupérer les valeurs via PowerShell ou Azure infrastructure du langage commun.

### <a name="powershell"></a>PowerShell

Le nom de l’attribution de rôle requiert un identificateur unique. Vous pouvez générer un nouvel identificateur pour **nom** avec :

    $name = [System.Guid]::NewGuid().toString()

Vous pouvez récupérer l’identificateur de définition de rôle avec :

    $roledef = (Get-AzureRmRoleDefinition -Name Reader).id

Vous pouvez récupérer l’identificateur de l’identité avec l’une des commandes suivantes.

Pour un groupe nommé **auditeurs**:

    $principal = (Get-AzureRmADGroup -SearchString Auditors).id

Pour un utilisateur nommé **exampleperson**:

    $principal = (Get-AzureRmADUser -SearchString exampleperson).id

D’un service principal nommé **exampleapp**:

    $principal = (Get-AzureRmADServicePrincipal -SearchString exampleapp).id 

### <a name="azure-cli"></a>Azure infrastructure du langage commun

Vous pouvez récupérer l’identificateur de définition de rôle avec :

    azure role show Reader --json | jq .[].Id -r

Vous pouvez récupérer l’identificateur de l’identité avec l’une des commandes suivantes.

Pour un groupe nommé **auditeurs**:

    azure ad group show --search Auditors --json | jq .[].objectId -r

Pour un utilisateur nommé **exampleperson**:

    azure ad user show --search exampleperson --json | jq .[].objectId -r

D’un service principal nommé **exampleapp**:

    azure ad sp show --search exampleapp --json | jq .[].objectId -r

## <a name="examples"></a>Exemples

Le modèle suivant reçoit un identificateur pour un rôle et un identificateur pour un utilisateur, groupe ou principal du service. Il attribue le rôle au niveau du groupe de ressources.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "roleDefinitionId": {
                "type": "string"
            },
            "roleAssignmentId": {
                "type": "string"
            },
            "principalId": {
                "type": "string"
            }
        },
        "resources": [
            {
              "type": "Microsoft.Authorization/roleAssignments",
              "apiVersion": "2015-07-01",
              "name": "[parameters('roleAssignmentId')]",
              "properties":
              {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', parameters('roleDefinitionId'))]",
                "principalId": "[parameters('principalId')]",
                "scope": "[concat(subscription().id, '/resourceGroups/', resourceGroup().name)]"
              }
            }
        ],
        "outputs": {}
    }



Le modèle suivant crée un compte de stockage et attribue le rôle de lecteur au compte de stockage. Les identificateurs de deux groupes et le rôle de lecteur ont été inclus dans le modèle pour simplifier le déploiement. Ces valeurs pourraient récupérées au moment du déploiement par le biais de script et passés en tant que paramètres.

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "roleName": {
          "type": "string"
        },
        "groupToAssign": {
          "type": "string",
          "allowedValues": [
            "Auditors",
            "Limited"
          ]
        }
      },
      "variables": {
        "readerRole": "[concat('/subscriptions/',subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
        "storageName": "[concat('storage', uniqueString(resourceGroup().id))]",
        "scope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Storage/storageAccounts/', variables('storageName'))]",
        "Auditors": "1c272299-9729-462a-8d52-7efe5ece0c5c",
        "Limited": "7c7250f0-7952-441c-99ce-40de5e3e30b5",
        "fullRoleName": "[concat(variables('storageName'), '/Microsoft.Authorization/', parameters('roleName'))]"
      },
      "resources": [
        {
          "apiVersion": "2016-01-01",
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[variables('storageName')]",
          "location": "[resourceGroup().location]",
          "sku": {
            "name": "Standard_LRS"
          },
          "kind": "Storage",
          "tags": {
            "displayName": "MyStorageAccount"
          },
          "properties": {}
        },
        {
          "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
          "apiVersion": "2015-07-01",
          "name": "[variables('fullRoleName')]",
          "dependsOn": [
            "[variables('storageName')]"
          ],
          "properties": {
            "roleDefinitionId": "[variables('readerRole')]",
            "principalId": "[variables(parameters('groupToAssign'))]"
          }
        }
      ]
    }

## <a name="quickstart-templates"></a>Modèles de démarrage rapide

Les modèles suivants montrent comment utiliser la ressource attribution de rôle :

- [Attribuer un rôle intégré au groupe de ressources](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-resourcegroup)
- [Attribuer un rôle intégré à machine virtuelle existant](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-virtualmachine)
- [Attribuer un rôle intégré à plusieurs machines virtuelles existantes](https://azure.microsoft.com/documentation/templates/201-rbac-builtinrole-multipleVMs)

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la structure du modèle, voir [Gestionnaire de ressources Azure de création de modèles](resource-group-authoring-templates.md).
- Pour plus d’informations sur le contrôle d’accès basé sur un rôle, consultez [Contrôle d’accès basé sur Azure Active Directory rôle](./active-directory/role-based-access-control-configure.md).
