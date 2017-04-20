<properties
   pageTitle="Modèle de gestionnaire de ressources pour un code secret dans un archivage sécurisé clé | Microsoft Azure"
   description="Montre le schéma de gestionnaire de ressources pour le déploiement secrets de l’archivage sécurisé clés via un modèle."
   services="azure-resource-manager,key-vault"
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
   ms.date="06/23/2016"
   ms.author="tomfitz"/>

# <a name="key-vault-secret-template-schema"></a>Schéma de modèle secrète de l’archivage sécurisé clés

Crée un code secret qui est stocké dans un archivage sécurisé clé. Ce type de ressource est souvent déployé comme une ressource enfant de [l’archivage sécurisé clé](resource-manager-template-keyvault.md).

## <a name="schema-format"></a>Format de schéma

Pour créer un code secret l’archivage sécurisé clés, ajoutez le schéma suivant à votre modèle. Le mot de passe peut être défini soit une ressource enfant d’un archivage sécurisé clé ou en tant que ressources de niveau supérieur. Vous pouvez le définir comme une ressource enfant lorsque l’archivage sécurisé clé est déployé dans le même modèle. Vous devez définir le code secret comme une ressource de niveau supérieur lorsque l’archivage sécurisé clé n’est pas déployée dans le même modèle, ou lorsque vous avez besoin créer des codes secrets plusieurs en effectuant une boucle sur le type de ressource. 

    {
        "type": enum,
        "apiVersion": "2015-06-01",
        "name": string,
        "properties": {
            "value": string
        },
        "dependsOn": [ array values ]
    }

## <a name="values"></a>Valeurs

Les tableaux suivants décrivent les valeurs que vous devez définir dans le schéma.

| Nom | Valeur |
| ---- | ---- | 
| type | Énumération<br />Obligatoire<br />**secrets** (lorsque le déployé comme une ressource enfant de l’archivage sécurisé clé) ou<br /> **Microsoft.KeyVault/vaults/secrets** (lorsque le déployé comme une ressource de niveau supérieur)<br /><br />Le type de ressource à créer. |
| apiVersion | Énumération<br />Obligatoire<br />**2015-06-01** ou **2014-12-19-aperçu**<br /><br />La version de l’API à utiliser pour créer la ressource. | 
| nom | Chaîne<br />Obligatoire<br />Un seul mot lorsque déployé comme une ressource enfant d’un archivage sécurisé clé, ou dans le format **{nom de l’archivage sécurisé clé} / {nom secret}** lorsque déployé comme une ressource de niveau supérieur à ajouter à un archivage sécurisé clé existant.<br /><br />Le nom du code secret pour créer. |
| propriétés | Objet<br />Obligatoire<br />[objet de propriétés](#properties)<br /><br />Un objet qui spécifie la valeur du code secret pour créer. |
| dependsOn | Tableau<br />Facultatif<br />Une liste séparée par des virgules d’une ressource de noms ou les identificateurs uniques de la ressource.<br /><br />La collection de ce lien dépend des ressources. Si l’archivage sécurisé clé pour le code secret est déployé dans le même modèle, inclure le nom de l’archivage sécurisé clé dans cet élément pour vous assurer qu’il est déployé en premier. |

<a id="properties" />
### <a name="properties-object"></a>objet de propriétés

| Nom | Valeur |
| ---- | ---- | 
| valeur | Chaîne<br />Obligatoire<br /><br />Valeur à stocker dans l’archivage sécurisé clé secrète. Lorsque vous passez d’une valeur de cette propriété, utilisez un paramètre de type **securestring**.  |

    
## <a name="examples"></a>Exemples

Le premier exemple déploie un code secret comme une ressource enfant d’un archivage sécurisé clé.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the vault"
                }
            },
            "tenantId": {
                "type": "string",
                "metadata": {
                   "description": "Tenant ID for the subscription and use assigned access to the vault. Available from the Get-AzureRmSubscription PowerShell cmdlet"
                }
            },
            "objectId": {
                "type": "string",
                "metadata": {
                    "description": "Object ID of the AAD user or service principal that will have access to the vault. Available from the Get-AzureRmADUser or the Get-AzureRmADServicePrincipal cmdlets"
                }
            },
            "keysPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to keys in the vault. Valid values are: all, create, import, update, get, list, delete, backup, restore, encrypt, decrypt, wrapkey, unwrapkey, sign, and verify."
                }
            },
            "secretsPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to secrets in the vault. Valid values are: all, get, set, list, and delete."
                }
            },
            "vaultSku": {
                "type": "string",
                "defaultValue": "Standard",
                "allowedValues": [
                    "Standard",
                    "Premium"
                ],
                "metadata": {
                    "description": "SKU for the vault"
                }
            },
            "enabledForDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for VM or Service Fabric deployment"
                }
            },
            "enabledForTemplateDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for ARM template deployment"
                }
            },
            "enableVaultForVolumeEncryption": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for volume encryption"
                }
            },
            "secretName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the secret to store in the vault"
                }
            },
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        },
        "resources": [
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[parameters('keyVaultName')]",
            "apiVersion": "2015-06-01",
            "location": "[resourceGroup().location]",
            "tags": {
                "displayName": "KeyVault"
            },
            "properties": {
                "enabledForDeployment": "[parameters('enabledForDeployment')]",
                "enabledForTemplateDeployment": "[parameters('enabledForTemplateDeployment')]",
                "enabledForVolumeEncryption": "[parameters('enableVaultForVolumeEncryption')]",
                "tenantId": "[parameters('tenantId')]",
                "accessPolicies": [
                {
                    "tenantId": "[parameters('tenantId')]",
                    "objectId": "[parameters('objectId')]",
                    "permissions": {
                        "keys": "[parameters('keysPermissions')]",
                        "secrets": "[parameters('secretsPermissions')]"
                    }
                }],
                "sku": {
                    "name": "[parameters('vaultSku')]",
                    "family": "A"
                }
            },
            "resources": [
            {
                "type": "secrets",
                "name": "[parameters('secretName')]",
                "apiVersion": "2015-06-01",
                "properties": {
                    "value": "[parameters('secretValue')]"
                },
                "dependsOn": [
                    "[concat('Microsoft.KeyVault/vaults/', parameters('keyVaultName'))]"
                ]
            }]
        }]
    }

Le second exemple déploie le code secret comme une ressource de niveau supérieur qui est stockée dans un archivage sécurisé clé existant.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the existing vault"
                }
            },
            "secretName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the secret to store in the vault"
                }
            },
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.KeyVault/vaults/secrets",
                "apiVersion": "2015-06-01",
                "name": "[concat(parameters('keyVaultName'), '/', parameters('secretName'))]",
                "properties": {
                    "value": "[parameters('secretValue')]"
                }
            }
        ],
        "outputs": {}
    }


## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir des informations générales relatives clés, voir [prise en main l’archivage sécurisé de clé Azure](./key-vault/key-vault-get-started.md).
- Pour obtenir un exemple de référencement d’un code secret l’archivage sécurisé clés lors du déploiement de modèles, voir [passer des valeurs sécurisés pendant le déploiement](resource-manager-keyvault-parameter.md).


