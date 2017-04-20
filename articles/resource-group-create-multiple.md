<properties
   pageTitle="Déployer plusieurs Instances de ressources | Microsoft Azure"
   description="Utiliser des opération de copie et les matrices dans un modèle de gestionnaire de ressources Azure pour parcourir plusieurs fois lorsque vous déployez des ressources."
   services="azure-resource-manager"
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
   ms.date="06/30/2016"
   ms.author="tomfitz"/>

# <a name="create-multiple-instances-of-resources-in-azure-resource-manager"></a>Créer plusieurs instances de ressources dans le Gestionnaire de ressources Azure

Cette rubrique vous montre comment effectuer une itération dans votre modèle Azure le Gestionnaire de ressources pour créer plusieurs instances d’une ressource.

## <a name="copy-copyindex-and-length"></a>copie, copyIndex et la durée

Dans la ressource pour créer plusieurs fois, vous pouvez définir un **Copier** l’objet qui spécifie le nombre de fois pour effectuer une itération. La copie vous permet d’accéder au format suivant :

    "copy": { 
        "name": "websitescopy", 
        "count": "[parameters('count')]" 
    } 

Vous pouvez accéder à la valeur d’itération en cours avec la fonction **copyIndex()** , comme illustré ci-dessous au sein de la fonction concat.

    [concat('examplecopy-', copyIndex())]

Lorsque vous créez plusieurs ressources à partir d’un tableau de valeurs, vous pouvez utiliser la fonction de **longueur** pour spécifier le nombre. Vous fournir le tableau en tant que le paramètre de la fonction de longueur.

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

## <a name="use-index-value-in-name"></a>Utilisez la valeur d’index dans la zone Nom

Vous pouvez utiliser la copie opération créer plusieurs instances d’une ressource qui sont des noms uniques basée sur l’index incrémentiel. Par exemple, vous souhaiterez peut-être ajouter un numéro unique à la fin de chaque nom de la ressource qui est déployé. Pour déployer trois sites web nommé :

- examplecopy-0
- examplecopy-1
- examplecopy-2.

Utilisez le modèle suivant :

    "parameters": { 
      "count": { 
        "type": "int", 
        "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', copyIndex())]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2015-08-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {
              "serverFarmId": "hostingPlanName"
          }
      } 
    ]

## <a name="offset-index-value"></a>Valeur de l’index de décalage

Vous remarquerez que dans l’exemple précédent la valeur d’index accède à partir de zéro à 2. Pour décaler la valeur d’index, vous pouvez passer une valeur de la fonction **copyIndex()** , par exemple **copyIndex(1)**. Le nombre d’itérations pour exécuter est encore spécifié dans l’élément de copie, mais la valeur de copyIndex est décalée par la valeur spécifiée. Par conséquent, à l’aide du même modèle de l’exemple précédent, mais spécifiant **copyIndex(1)** serait déployer des trois sites web nommé :

- examplecopy-1
- examplecopy-2
- examplecopy-3

## <a name="use-copy-with-array"></a>Utiliser une copie avec tableau
   
L’opération de copie est particulièrement utile lorsque vous travaillez avec des tableaux, car vous pouvez parcourir chaque élément dans le tableau. Pour déployer trois sites web nommé :

- examplecopy Contoso
- examplecopy Fabrikam
- examplecopy Coho

Utilisez le modèle suivant :

    "parameters": { 
      "org": { 
         "type": "array", 
             "defaultValue": [ 
             "Contoso", 
             "Fabrikam", 
             "Coho" 
          ] 
      }
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2015-08-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[length(parameters('org'))]" 
          }, 
          "properties": {
              "serverFarmId": "hostingPlanName"
          } 
      } 
    ]

Bien entendu, vous définissez le nombre de copies à une valeur autre que la longueur du tableau. Par exemple, vous pourriez créer un tableau avec le nombre de valeurs et passez dans une valeur de paramètre qui spécifie le nombre des éléments du tableau pour déployer. Dans ce cas, vous définissez le nombre de copies comme indiqué dans le premier exemple. 

## <a name="depending-on-resources-in-a-loop"></a>En fonction de ressources d’une boucle

Vous pouvez spécifier qu’une ressource être déployé après une autre ressource à l’aide de l’élément **dependsOn** . Lorsque vous déployez une ressource qui dépend de la collection de ressources d’une boucle, vous pouvez utiliser fournir le nom de la boucle copie dans l’élément **dependsOn** . L’exemple suivant montre comment déployer 3 comptes de stockage avant de déployer la Machine virtuelle. La définition de Machine virtuelle complète n’est pas affichée. Notez que l’élément copie a **nom** défini sur **storagecopy** et l’élément **dependsOn** pour les ordinateurs virtuels est également défini sur **storagecopy**.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "resources": [
            {
                "apiVersion": "2015-06-15",
                "type": "Microsoft.Storage/storageAccounts",
                "name": "[concat('storage', uniqueString(resourceGroup().id), copyIndex())]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "accountType": "Standard_LRS"
                 },
                "copy": { 
                    "name": "storagecopy", 
                    "count": 3 
                }
            },
           {
               "apiVersion": "2015-06-15", 
               "type": "Microsoft.Compute/virtualMachines", 
               "name": "[concat('VM', uniqueString(resourceGroup().id))]",  
               "dependsOn": ["storagecopy"],
               ...
           }
        ],
        "outputs": {}
    }

## <a name="looping-on-a-nested-resource"></a>En boucle sur une ressource imbriquée

Vous ne pouvez pas utiliser une boucle de copie pour une ressource imbriquée. Si vous avez besoin créer plusieurs instances d’une ressource que vous définissez en général en tant qu’imbriquée dans une autre ressource, vous devez créer la ressource comme une ressource de niveau supérieur à la place et définissez la relation avec la ressource parent via les propriétés de **type** et le **nom** .

Par exemple, supposons que vous définissez généralement un jeu de données comme une ressource imbriquée dans une usine de données.

    "parameters": {
        "dataFactoryName": {
            "type": "string"
         },
         "dataSetName": {
            "type": "string"
         }
    },
    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "[parameters('dataFactoryName')]",
        ...
        "resources": [
        {
            "type": "datasets",
            "name": "[parameters('dataSetName')]",
            "dependsOn": [
                "[parameters('dataFactoryName')]"
            ],
            ...
        }
    }]
    
Pour créer plusieurs instances de jeux de données, vous devez modifier votre modèle comme illustré ci-dessous. Notez le type qualifié complet et le nom inclut le nom d’usine de données.

    "parameters": {
        "dataFactoryName": {
            "type": "string"
         },
         "dataSetName": {
            "type": "array"
         }
    },
    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "[parameters('dataFactoryName')]",
        ...
    },
    {
        "type": "Microsoft.DataFactory/datafactories/datasets",
        "name": "[concat(parameters('dataFactoryName'), '/', parameters('dataSetName')[copyIndex()])]",
        "dependsOn": [
            "[parameters('dataFactoryName')]"
        ],
        "copy": { 
            "name": "datasetcopy", 
            "count": "[length(parameters('dataSetName'))]" 
        } 
        ...
    }]

## <a name="create-multiple-instances-when-copy-wont-work"></a>Créer plusieurs instances lors de la copie ne fonctionne pas

Vous ne pouvez utiliser **copie** sur types de ressources, et non sur les propriétés d’un type de ressource. Cela peut créer des problèmes pour vous lorsque vous voulez créer plusieurs instances d’un élément qui fait partie d’une ressource. Un scénario courant consiste à créer plusieurs disques de données pour une Machine virtuelle. Vous ne pouvez pas utiliser de **copie** avec les disques de données **dataDisks** étant une propriété sur l’ordinateur virtuel, pas son propre type de ressource. À la place, vous créez un tableau avec autant de disques de données que vous avez besoin et passer le nombre réel de disques de données pour créer. Dans la définition de la machine virtuelle, vous utilisez la fonction **prendre** pour obtenir uniquement le nombre d’éléments que vous avez vraiment besoin à partir du tableau.

Un exemple complet de ce modèle est afficher dans le modèle de [créer une machine virtuelle avec une sélection dynamique de disques de données](https://azure.microsoft.com/documentation/templates/201-vm-dynamic-data-disks-selection/) .

Les sections correspondantes du modèle de déploiement sont présentées ci-dessous. Un grand nombre du modèle a été supprimé pour mettre en surbrillance les sections dynamiquement création d’un nombre de disques de données. Notez le paramètre **numDataDisks** qui vous permet de passer le nombre de disques à créer. 

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
    "numDataDisks": {
      "type": "int",
      "maxValue": 64,
      "metadata": {
        "description": "This parameter allows you to select the number of disks you want"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'dynamicdisk')]",
    "sizeOfDataDisksInGB": 100,
    "diskCaching": "ReadWrite",
    "diskArray": [
      {
        "name": "datadisk1",
        "lun": 0,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk1.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk2",
        "lun": 1,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk2.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk3",
        "lun": 2,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk3.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk4",
        "lun": 3,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk4.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      ...
      {
        "name": "datadisk63",
        "lun": 62,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk63.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk64",
        "lun": 63,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk64.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      }
    ]
  },
  "resources": [
    ...
    {
      "type": "Microsoft.Compute/virtualMachines",
      "properties": {
        ...
        "storageProfile": {
          ...
          "dataDisks": "[take(variables('diskArray'),parameters('numDataDisks'))]"
        },
        ...
      }
      ...
    }
  ]
}
```


## <a name="next-steps"></a>Étapes suivantes
- Si vous voulez en savoir plus sur les sections d’un modèle, voir [Création de modèles de gestionnaire de ressources Azure](./resource-group-authoring-templates.md).
- Pour toutes les fonctions que vous pouvez utiliser dans un modèle, voir [Fonctions de modèle Azure Gestionnaire de ressources](./resource-group-template-functions.md).
- Pour savoir comment déployer votre modèle, voir [déployer une application avec Azure Gestionnaire de ressources du modèle](resource-group-template-deploy.md).
