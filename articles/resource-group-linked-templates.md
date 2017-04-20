<properties
   pageTitle="Lié modèles avec le Gestionnaire de ressources | Microsoft Azure"
   description="Décrit comment utiliser des modèles liées dans un modèle de gestionnaire de ressources Azure pour créer une solution modulaire modèle. Montre comment passer des valeurs de paramètres, spécifiez un fichier de paramètres et les URL créés dynamiquement."
   services="azure-resource-manager"
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
   ms.date="09/02/2016"
   ms.author="tomfitz"/>

# <a name="using-linked-templates-with-azure-resource-manager"></a>Utilisation des modèles liées avec le Gestionnaire de ressources Azure

À partir d’au sein d’un modèle de gestionnaire de ressources Azure, vous pouvez lier à un autre modèle, ce qui permet de décomposer votre déploiement en un jeu de ciblées, modèles spécifiques à la fin. Comme avec la décomposition d’une application en plusieurs classes code, décomposition fournit des avantages en termes de test, la réutilisation et la lisibilité.  

Vous pouvez passer des paramètres à partir d’un modèle principal à un modèle lié, et ces paramètres peuvent mapper directement aux paramètres ou les variables exposés par le modèle d’appel. Le modèle lié peut également passer d’une variable de sortie dans le modèle source, l’activation un échange de données à double sens entre modèles.

## <a name="linking-to-a-template"></a>Liaison à un modèle

Vous créez un lien entre deux modèles en ajoutant une ressource de déploiement au sein du modèle principale qui pointe vers le modèle lié. Vous définissez la propriété **templateLink** à l’URI du modèle lié. Vous pouvez fournir des valeurs de paramètre pour le modèle lié en spécifiant les valeurs directement dans votre modèle ou en créant un lien vers un fichier de paramètre. L’exemple suivant utilise la propriété de **paramètres** pour spécifier une valeur de paramètre directement.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "linkedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri": "https://www.contoso.com/AzureTemplates/newStorageAccount.json",
              "contentVersion": "1.0.0.0"
           }, 
           "parameters": { 
              "StorageAccountName":{"value": "[parameters('StorageAccountName')]"} 
           } 
         } 
      } 
    ] 

Le service Gestionnaire de ressources doit être en mesure d’accéder au modèle lié. Vous ne pouvez pas spécifier un fichier local ou un fichier qui n’est disponible sur votre réseau local pour le modèle lié. Vous pouvez fournir uniquement une valeur d’URI incluant **http** ou **https**. Une seule option consiste à placer votre modèle liée dans un compte de stockage, utilisez l’URI pour cet élément, comme illustré dans l’exemple suivant.

    "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0",
    }

Bien que le modèle lié doit être externes disponible, il n’a pas besoin de sera-t-il disponible au public. Vous pouvez ajouter votre modèle à un compte de stockage privé est accessible à un seul le propriétaire du compte de stockage. Ensuite, vous créez un jeton de signature (sa) accès partagé pour activer l’accès au cours du déploiement. Vous ajoutez ce jeton associations de sécurité à l’URI pour le modèle lié. Pour savoir comment configurer un modèle dans un compte de stockage et générer un jeton associations de sécurité, consultez [ressources déployer des modèles de gestionnaire de ressources et Azure PowerShell](resource-group-template-deploy.md) ou [déployer des modèles de gestionnaire de ressources et Azure infrastructure du langage commun](resource-group-template-deploy-cli.md). 

L’exemple suivant montre un modèle parent que les liens vers un autre modèle. Le modèle lié est accessible avec un jeton de sa qui est passé comme un paramètre.

    "parameters": {
        "sasToken": { "type": "securestring" }
    },
    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
              "mode": "incremental",
              "templateLink": {
                "uri": "[concat('https://storagecontosotemplates.blob.core.windows.net/templates/helloworld.json', parameters('sasToken'))]",
                "contentVersion": "1.0.0.0"
              }
            }
        }
    ],

Même si le jeton est passé dans sous forme de chaîne sécurisée, l’URI du modèle lié, y compris le jeton associations de sécurité, est enregistrée dans les opérations de déploiement de ce groupe de ressources. Pour limiter l’exposition, définissez une date d’expiration pour le jeton.

## <a name="linking-to-a-parameter-file"></a>Un lien vers un fichier de paramètre

L’exemple suivant utilise la propriété **parametersLink** pour créer un lien vers un fichier de paramètres.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "linkedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri":"https://www.contoso.com/AzureTemplates/newStorageAccount.json",
              "contentVersion":"1.0.0.0"
           }, 
           "parametersLink": { 
              "uri":"https://www.contoso.com/AzureTemplates/parameters.json",
              "contentVersion":"1.0.0.0"
           } 
         } 
      } 
    ] 

La valeur URI pour le fichier de paramètres liée ne peut pas être un fichier local et doit inclure **http** ou **https**. Le fichier de paramètres peut également être limité à access via un jeton associations de sécurité.

## <a name="using-variables-to-link-templates"></a>Utilisation des variables pour lier des modèles

Les exemples précédents a montré valeurs d’URL codé en dur pour les liens de modèle. Cette approche peut fonctionner pour un modèle simple, mais il ne fonctionne pas correctement lorsque vous travaillez avec un grand ensemble de modèles modulaires. À la place, vous pouvez créer une variable statique qui stocke les URL de base pour le modèle principale et créer dynamiquement des URL pour les modèles liées à partir de cette URL de base. L’avantage de cette approche est vous pouvez facilement déplacer ou une branche dans le modèle, car vous ne devez modifier la variable statique dans le modèle principal. Le modèle principal transmet les URI correcte dans l’ensemble du modèle décomposé.

L’exemple suivant montre comment utiliser une URL de base pour créer deux URL des modèles liées (**sharedTemplateUrl** et **vmTemplate**). 

    "variables": {
        "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
        "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
        "tshirtSizeSmall": {
            "vmSize": "Standard_A1",
            "diskSize": 1023,
            "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
            "vmCount": 2,
            "slaveCount": 1,
            "storage": {
                "name": "[parameters('storageAccountNamePrefix')]",
                "count": 1,
                "pool": "db",
                "map": [0,0],
                "jumpbox": 0
            }
        }
    }

Vous pouvez également utiliser [le déploiement()](resource-group-template-functions.md#deployment) pour obtenir l’URL de base pour le modèle actuel et qui permet d’obtenir l’URL pour d’autres modèles dans le même emplacement. Cette approche est utile si votre emplacement modèle change (peut-être en raison d’un contrôle de version) ou si vous voulez éviter irréversible URL dans le fichier de modèle. 

    "variables": {
        "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
    }

## <a name="conditionally-linking-to-templates"></a>Liaison conditionnelle aux modèles

Vous pouvez lier à différents modèles en passant une valeur de paramètre qui est utilisée pour construire l’URI du modèle lié. Cette méthode fonctionne bien lorsque vous devez spécifier au cours du déploiement qui liés modèle à utiliser. Par exemple, vous pouvez spécifier un modèle à utiliser pour un compte de stockage existant et un autre modèle à utiliser pour un nouveau compte de stockage.

L’exemple suivant montre un paramètre pour un nom de compte de stockage et un paramètre pour spécifier si le compte de stockage est nouvelle ou existante.

    "parameters": {
        "storageAccountName": {
            "type": "String"
        },
        "newOrExisting": {
            "type": "String",
            "allowedValues": [
                "new",
                "existing"
            ]
        }
    },

Vous créez une variable pour le modèle URI qui inclut la valeur du paramètre nouvelle ou existante.

    "variables": {
        "templatelink": "[concat('https://raw.githubusercontent.com/exampleuser/templates/master/',parameters('newOrExisting'),'StorageAccount.json')]"
    },

Vous fournissez cette valeur de la variable de la ressource de déploiement.

    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "incremental",
                "templateLink": {
                    "uri": "[variables('templatelink')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "StorageAccountName": {
                        "value": "[parameters('storageAccountName')]"
                    }
                }
            }
        }
    ],

L’URI correspond à un modèle nommé **existingStorageAccount.json** ou **newStorageAccount.json**. Créer des modèles de ces URI.

L’exemple suivant montre le modèle **existingStorageAccount.json** .

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageAccountName": {
          "type": "String"
        }
      },
      "variables": {},
      "resources": [],
      "outputs": {
        "storageAccountInfo": {
          "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')),providers('Microsoft.Storage', 'storageAccounts').apiVersions[0])]",
          "type" : "object"
        }
      }
    }

L’exemple suivant illustre le modèle **newStorageAccount.json** . Notez que comme le stockage existant modèle compte l’objet de compte de stockage dans les sorties est renvoyée. Le modèle de base fonctionne avec un modèle lié.

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageAccountName": {
          "type": "string"
        }
      },
      "resources": [
        {
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[parameters('StorageAccountName')]",
          "apiVersion": "2016-01-01",
          "location": "[resourceGroup().location]",
          "sku": {
            "name": "Standard_LRS"
          },
          "kind": "Storage",
          "properties": {
          }
        }
      ],
      "outputs": {
        "storageAccountInfo": {
          "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('StorageAccountName')),providers('Microsoft.Storage', 'storageAccounts').apiVersions[0])]",
          "type" : "object"
        }
      }
    }

## <a name="complete-example"></a>Exemple complet

Les modèles d’exemple suivantes donnent une disposition simplifiée des modèles liées à illustrent certains des concepts décrits dans cet article. Il suppose que les modèles ont été ajoutés au même conteneur dans un compte de stockage avec un accès public ou désactivé. Le modèle lié passe une valeur dans le modèle dans la section **sorties** principal.

Le fichier **parent.json** se compose de :

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "containerSasToken": { "type": "string" }
      },
      "resources": [
        {
          "apiVersion": "2015-01-01",
          "name": "linkedTemplate",
          "type": "Microsoft.Resources/deployments",
          "properties": {
            "mode": "incremental",
            "templateLink": {
              "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
              "contentVersion": "1.0.0.0"
            }
          }
        }
      ],
      "outputs": {
        "result": {
          "type": "object",
          "value": "[reference('linkedTemplate').outputs.result]"
        }
      }
    }

Le fichier **helloworld.json** se compose de :

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "variables": {},
      "resources": [],
      "outputs": {
        "result": {
            "value": "Hello World",
            "type" : "string"
        }
      }
    }
    
Dans PowerShell, vous obtenez un jeton pour le conteneur et déployez les modèles avec :

    Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
    $token = New-AzureStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
    New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ("https://storagecontosotemplates.blob.core.windows.net/templates/parent.json" + $token) -containerSasToken $token

Dans Azure infrastructure du langage commun, vous recevez un jeton pour le conteneur et déployez les modèles par le code suivant. Pour l’instant, vous devez fournir un nom pour le déploiement lorsque vous utilisez un modèle URI qui inclut un jeton associations de sécurité.  

    expiretime=$(date -I'minutes' --date "+30 minutes")  
    azure storage container sas create --container templates --permissions r --expiry $expiretime --json | jq ".sas" -r
    azure group deployment create -g ExampleGroup --template-uri "https://storagecontosotemplates.blob.core.windows.net/templates/parent.json?{token}" -n tokendeploy  

Vous êtes invité à fournir le jeton associations de sécurité en tant que paramètre. Vous devez faire précéder le jeton avec **?**.

## <a name="next-steps"></a>Étapes suivantes
- Pour en savoir plus sur la définition de l’ordre de déploiement de vos ressources, voir [définir des dépendances dans le Gestionnaire de ressources Azure modèles](resource-group-define-dependencies.md)
- Pour savoir comment définir une ressource mais créer plusieurs instances de celle-ci, voir [créer plusieurs instances de ressources dans le Gestionnaire de ressources Azure](resource-group-create-multiple.md)
