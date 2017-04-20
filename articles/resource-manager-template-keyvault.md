<properties
   pageTitle="Modèle de gestionnaire de ressources pour l’archivage sécurisé clé | Microsoft Azure"
   description="Montre le schéma de gestionnaire de ressources pour le déploiement de clé chambres fortes via un modèle."
   services="azure-resource-manager,key-vault"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="tomfitz"/>

# <a name="key-vault-template-schema"></a>Schéma de modèle de l’archivage sécurisé clés

Crée un archivage sécurisé clé.

## <a name="schema-format"></a>Format de schéma

Pour créer un archivage sécurisé clé, ajoutez le schéma suivant à la section de ressources de votre modèle.

    {
        "type": "Microsoft.KeyVault/vaults",
        "apiVersion": "2015-06-01",
        "name": string,
        "location": string,
        "properties": {
            "enabledForDeployment": bool,
            "enabledForTemplateDeployment": bool,
            "enabledForVolumeEncryption": bool,
            "tenantId": string,
            "accessPolicies": [
                {
                    "tenantId": string,
                    "objectId": string,
                    "permissions": {
                        "keys": [ keys permissions ],
                        "secrets": [ secrets permissions ]
                    }
                }
            ],
            "sku": {
                "name": enum,
                "family": "A"
            }
        },
        "resources": [
             child resources
        ]
    }

## <a name="values"></a>Valeurs

Les tableaux suivants décrivent les valeurs que vous devez définir dans le schéma.

| Nom | Valeur |
| ---- | ---- | 
| type | Énumération<br />Obligatoire<br />**Microsoft.KeyVault/vaults**<br /><br />Le type de ressource à créer. |
| apiVersion | Énumération<br />Obligatoire<br />**2015-06-01** ou **2014-12-19-aperçu**<br /><br />La version de l’API à utiliser pour créer la ressource. | 
| nom | Chaîne<br />Obligatoire<br />Un nom qui est unique dans Azure.<br /><br />Le nom de l’archivage sécurisé clé à créer. Envisagez d’utiliser la fonction [uniqueString](resource-group-template-functions.md#uniquestring) avec votre convention d’appellation pour créer un nom unique, comme illustré dans l’exemple ci-dessous. |
| emplacement | Chaîne<br />Obligatoire<br />Une région valide pour chambres fortes clés. Pour déterminer les régions valides, voir [prise en charge des régions](resource-manager-supported-services.md#supported-regions).<br /><br />La région pour héberger l’archivage sécurisé clé. |
| propriétés | Objet<br />Obligatoire<br />[objet de propriétés](#properties)<br /><br />Objet qui spécifie le type de l’archivage sécurisé clé à créer. |
| ressources | Tableau<br />Facultatif<br />Autorisés valeurs : [ressources secrètes clé l’archivage sécurisé](resource-manager-template-keyvault-secret.md)<br /><br />Ressources enfants pour l’archivage sécurisé clé. |

<a id="properties" />
### <a name="properties-object"></a>objet de propriétés

| Nom | Valeur |
| ---- | ---- | 
| enabledForDeployment | Valeur booléenne<br />Facultatif<br />**Vrai** ou **faux**<br /><br />Indique si l’archivage sécurisé est activé pour le déploiement Machine virtuelle ou tissu de Service. |
| enabledForTemplateDeployment | Valeur booléenne<br />Facultatif<br />**Vrai** ou **faux**<br /><br />Indique si l’archivage sécurisé est activé pour une utilisation dans les déploiements de modèle Gestionnaire de ressources. Pour plus d’informations, voir [passer des valeurs sécurisés pendant le déploiement](resource-manager-keyvault-parameter.md) |
| enabledForVolumeEncryption | Valeur booléenne<br />Facultatif<br />**Vrai** ou **faux**<br /><br />Indique si l’archivage sécurisé est activé pour le chiffrement du volume. |
| tenantId | Chaîne<br />Obligatoire<br />**Identificateur unique global**<br /><br />L’identificateur de client de l’abonnement. Vous pourrez le retrouver avec l’applet de commande PowerShell [Get-AzureRmSubscription](https://msdn.microsoft.com/library/azure/mt619284.aspx) ou le **compte azure afficher** commande Azure infrastructure du langage commun. |
| accessPolicies | Tableau<br />Obligatoire<br />[objet accessPolicies](#accesspolicies)<br /><br />Tableau de jusqu'à 16 objets qui spécifient les autorisations pour l’utilisateur ou principal du service. |
| référence (SKU) | Objet<br />Obligatoire<br />[objet de référence (SKU)](#sku)<br /><br />La référence (SKU) pour l’archivage sécurisé clé. |

<a id="accesspolicies" />
### <a name="propertiesaccesspolicies-object"></a>objet properties.accessPolicies

| Nom | Valeur |
| ---- | ---- | 
| tenantId | Chaîne<br />Obligatoire<br />**Identificateur unique global**<br /><br />L’identificateur client du client Azure Active Directory contenant **objectId** dans cette stratégie d’accès |
| objectId | Chaîne<br />Obligatoire<br />**Identificateur unique global**<br /><br />L’identificateur d’objet de l’utilisateur Azure Active Directory ou principal du service qui a accès à l’archivage sécurisé. Vous pouvez récupérer la valeur de [Get-AzureRmADUser](https://msdn.microsoft.com/library/azure/mt679001.aspx) ou les applets de commande PowerShell [Get-AzureRmADServicePrincipal](https://msdn.microsoft.com/library/azure/mt678992.aspx) ou les commandes Azure infrastructure du langage commun **utilisateur azure ad** ou **sp azure ad** . |
| autorisations | Objet<br />Obligatoire<br />[autorisations objet](#permissions)<br /><br />Les autorisations accordées sur ce archivage sécurisé à l’objet Active Directory. |

<a id="permissions" />
### <a name="propertiesaccesspoliciespermissions-object"></a>objet properties.accessPolicies.permissions

| Nom | Valeur |
| ---- | ---- | 
| touches | Tableau<br />Obligatoire<br />**toutes les**, **sauvegarde**, **créer**, **déchiffrer**, **Supprimer**, **chiffrer**, **obtenir**, **Importer**, **liste**, **restaurer**, **se**, **unwrapkey**, **mettre à jour**, **Vérifiez**, **wrapkey**<br /><br />Les autorisations accordées sur les clés dans cette archivage sécurisé à cet objet Active Directory. Cette valeur doit être spécifiée comme un tableau d’une ou plusieurs valeurs autorisées. |
| secrets | Tableau<br />Obligatoire<br />**toutes les**, **Supprimer**, **obtenir**, **liste**, **définir**<br /><br />Les autorisations accordées sur secrets dans cette archivage sécurisé à cet objet Active Directory. Cette valeur doit être spécifiée comme un tableau d’une ou plusieurs valeurs autorisées. |

<a id="sku" />
### <a name="propertiessku-object"></a>objet Properties.SKU

| Nom | Valeur |
| ---- | ---- | 
| nom | Énumération<br />Obligatoire<br />**standard**ou **premium** <br /><br />Niveau de service de KeyVault à utiliser.  Standard prend en charge secrets et clés protégées par un logiciel.  Premium prend en charge pour les clés protégées par HSM. |
| famille | Énumération<br />Obligatoire<br />**A** <br /><br />Famille de référence (SKU) à utiliser. |
 
    
## <a name="examples"></a>Exemples

L’exemple suivant déploie un archivage sécurisé clé et un code secret.

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

## <a name="quickstart-templates"></a>Modèles de démarrage rapide

Le modèle de démarrage rapide suivant déploie un archivage sécurisé clé.

- [Créer l’archivage sécurisé clé](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)


## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir des informations générales sur chambres fortes clés, voir [prise en main l’archivage sécurisé de clé Azure](./key-vault/key-vault-get-started.md).
- Pour obtenir un exemple de référencement d’un code secret l’archivage sécurisé clés lors du déploiement de modèles, voir [passer des valeurs sécurisés pendant le déploiement](resource-manager-keyvault-parameter.md).

