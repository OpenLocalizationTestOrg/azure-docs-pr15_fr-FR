<properties
   pageTitle="Code secret de l’archivage sécurisé clé avec le Gestionnaire de ressources modèle | Microsoft Azure"
   description="Montre comment passer un code secret à partir d’un archivage sécurisé clé en tant que paramètre pendant le déploiement."
   services="azure-resource-manager,key-vault"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="tomfitz"/>

# <a name="pass-secure-values-during-deployment"></a>Passer de valeurs sécurisé pendant le déploiement

Lorsque vous avez besoin de passer d’une valeur sécurisée (par exemple, un mot de passe) en tant que paramètre au cours du déploiement, vous pouvez stocker cette valeur en tant que code secret utilisé dans un [L’archivage sécurisé de clé Azure](./key-vault/key-vault-whatis.md) et faire référence à la valeur dans d’autres modèles Gestionnaire de ressources. Vous incluez uniquement une référence aux données secrètes dans votre modèle afin que le mot de passe n’est jamais exposée, et vous n’avez pas besoin d’entrer manuellement la valeur pour le code secret chaque fois que vous déployez les ressources. Vous spécifiez les utilisateurs ou les identités de service peuvent accéder le code secret.  

## <a name="deploy-a-key-vault-and-secret"></a>Déployer une archivage sécurisé clé et un code secret

Pour créer l’archivage sécurisé clé qui peut être référencée dans d’autres modèles Gestionnaire de ressources, vous devez définir la propriété **enabledForTemplateDeployment** **true**, et vous devez accorder l’accès à l’utilisateur ou le principal de service qui s’exécute le déploiement qui fait référence au code secret.

Pour savoir comment déployer un archivage sécurisé clé et un code secret, voir [schémas de l’archivage sécurisé clé](resource-manager-template-keyvault.md) et [clé l’archivage sécurisé secrète](resource-manager-template-keyvault-secret.md).

## <a name="reference-a-secret-with-static-id"></a>Faire référence à un code secret avec l’id statique

Vous référencez le mot de passe à partir d’un fichier de paramètres qui passe des valeurs à votre modèle. Vous référencez le code secret en passant l’identificateur de ressource de l’archivage sécurisé clé et le nom du code secret. Dans cet exemple, le code secret l’archivage sécurisé clé doit déjà exister, et vous utilisez une valeur statique pour qu’elle Nº ressource.

    "parameters": {
      "adminPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
          }, 
          "secretName": "sqlAdminPassword" 
        } 
      }
    }

Un fichier d’ensemble du paramètre peut ressembler à :

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "sqlsvrAdminLogin": {
          "value": ""
        },
        "sqlsvrAdminLoginPassword": {
          "reference": {
            "keyVault": {
              "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
            },
            "secretName": "adminPassword"
          }
        }
      }
    }

Le paramètre qui accepte le code secret doit être une **securestring**. L’exemple suivant montre les sections correspondantes d’un modèle qui déploie un serveur SQL qui requiert un mot de passe administrateur.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "sqlsvrAdminLogin": {
                "type": "string",
                "minLength": 4
            },
            "sqlsvrAdminLoginPassword": {
                "type": "securestring"
            },
            ...
        },
        "resources": [
        {
            "name": "[variables('sqlsvrName')]",
            "type": "Microsoft.Sql/servers",
            "location": "[resourceGroup().location]",
            "apiVersion": "2014-04-01-preview",
            "properties": {
                "administratorLogin": "[parameters('sqlsvrAdminLogin')]",
                "administratorLoginPassword": "[parameters('sqlsvrAdminLoginPassword')]"
            },
            ...
        }],
        "variables": {
            "sqlsvrName": "[concat('sqlsvr', uniqueString(resourceGroup().id))]"
        },
        "outputs": { }
    }

## <a name="reference-a-secret-with-dynamic-id"></a>Faire référence à un code secret avec l’id dynamique

La section précédente a montré comment passer d’un id de ressource statique pour le code secret l’archivage sécurisé clés. Toutefois, dans certains cas, vous devez faire référence à un secret de l’archivage sécurisé clé varie en fonction du déploiement actuel. Dans ce cas, vous ne pouvez pas coder l’id de la ressource dans le fichier de paramètres. Malheureusement, vous ne pouvez pas générer dynamiquement l’id de ressource dans le fichier de paramètres, car les expressions de modèle ne sont pas autorisées dans le fichier de paramètres.

Pour générer l’id de ressource pour un secret l’archivage sécurisé clés de façon dynamique, vous devez déplacer la ressource nécessitant le mot de passe dans un modèle imbriqué. Dans votre modèle de base, vous ajoutez le modèle imbriqué et passez un paramètre qui contient l’id de ressource générée dynamiquement.

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "vaultName": {
          "type": "string"
        },
        "secretName": {
          "type": "string"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-01-01",
          "name": "nestedTemplate",
          "type": "Microsoft.Resources/deployments",
          "properties": {
            "mode": "incremental",
            "templateLink": {
              "uri": "https://www.contoso.com/AzureTemplates/newVM.json",
              "contentVersion": "1.0.0.0"
            },
            "parameters": {
              "adminPassword": {
                "reference": {
                  "keyVault": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.KeyVault/vaults/', parameters('vaultName'))]"
                  },
                  "secretName": "[parameters('secretName')]"
                }
              }
            }
          }
        }
      ],
      "outputs": {}
    }


## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir des informations générales sur chambres fortes clés, voir [prise en main l’archivage sécurisé de clé Azure](./key-vault/key-vault-get-started.md).
- Pour plus d’informations sur l’utilisation d’un archivage sécurisé clé avec une Machine virtuelle, voir [Considérations relatives à la sécurité pour le Gestionnaire de ressources Azure](best-practices-resource-manager-security.md).
- Pour obtenir des exemples complètes de clé secrets de référence, voir des [exemples de clé de l’archivage sécurisé](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).

