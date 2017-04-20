<properties
   pageTitle="Présentation du modèle Gestionnaire de ressources | Microsoft Azure"
   description="Procédure étape par étape d’un modèle de gestionnaire de ressources mise en service une architecture Azure IaaS simple."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/04/2016"
   ms.author="navale;tomfitz"/>
   
# <a name="resource-manager-template-walkthrough"></a>Présentation du modèle Gestionnaire de ressources

Un des premières questions lors de la création d’un modèle est « comment démarrer ? ». Il est possible d’un modèle vierge, suivre la structure de base décrite dans [l’article Création d’un modèle](resource-group-authoring-templates.md#template-format)et ajouter les ressources et les paramètres appropriés et les variables. Une bonne alternative serait pour commencer par le biais de la [Galerie de démarrage rapide](https://github.com/Azure/azure-quickstart-templates) et recherchez un scénario similaire à celui que vous essayez de créer. Vous pouvez fusionner plusieurs modèles ou modifier une existante pour l’adapter à votre propre situation spécifique. 

Examinons une infrastructure commune :

* Deux machines virtuelles qui utilisent le même compte de stockage, se trouvent dans le même jeu de disponibilité et sur le même sous-réseau d’un réseau virtuel.
* Une seule carte réseau et VM l’adresse IP pour chaque ordinateur virtuel.
* Un équilibrage de charge une règle sur le port 80 d’équilibrage de charge

![architecture](./media/resource-group-overview/arm_arch.png)

Cette rubrique décrit les étapes de création d’un modèle de gestionnaire de ressources pour que l’infrastructure. Le modèle final que vous créez est basé sur un modèle de démarrage rapide appelé [2 machines virtuelles dans un équilibrage de charge et les règles d’équilibrage de charge](https://azure.microsoft.com/documentation/templates/201-2-vms-loadbalancer-lbrules/).

Mais, qui est un grand nombre de création en une seule fois, nous allons tout d’abord créer un compte de stockage et le déployer. Une fois que vous maîtrisez la création du compte de stockage, vous ajoutez les autres ressources et déployez le modèle pour terminer l’infrastructure de nouveau.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel type de l’éditeur lorsque vous créez le modèle. Visual Studio fournit des outils qui simplifient le développement de modèle, mais vous n’avez pas besoin de Visual Studio pour effectuer ce didacticiel. Pour un didacticiel sur l’utilisation de Visual Studio pour créer un déploiement Web App et base de données SQL, consultez [Création et déploiement de groupes de ressources Azure via Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). 

## <a name="create-the-resource-manager-template"></a>Créer le modèle de gestionnaire de ressources

Le modèle est un fichier JSON qui définit toutes les ressources que vous allez déployer. Il vous permet également de définir des paramètres spécifiés pendant le déploiement, variables créé à partir d’autres valeurs et des expressions et des sorties à partir du déploiement. 

Commençons par le modèle plus simple :

```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {  },
      "variables": {  },
      "resources": [  ],
      "outputs": {  }
    }
 ```

Enregistrer ce fichier en tant que **azuredeploy.json** (Notez que le modèle peut avoir le nom de votre choix, juste ce qu’il doit être un fichier json).

## <a name="create-a-storage-account"></a>Créer un compte de stockage
Dans la section **ressources** , ajoutez un objet qui définit le compte de stockage, comme illustré ci-dessous. 

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[parameters('storageAccountName')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
      "accountType": "Standard_LRS"
    }
  }
]
```

Vous vous demandez peut-être où ces propriétés et les valeurs proviennent. Les propriétés **type**, **nom**, **apiVersion**et **emplacement** sont des éléments standards qui sont disponibles pour tous les types de ressources. Vous pouvez en savoir plus sur les éléments communs à des [ressources](resource-group-authoring-templates.md#resources). **nom** est défini sur une valeur de paramètre que vous passez au cours de déploiement et **l’emplacement** en tant que l’emplacement utilisé par le groupe de ressources. Nous allons aborder comment vous déterminez **type** et **apiVersion** dans les sections ci-dessous.

La section **Propriétés** contient toutes les propriétés qui sont propres à un type particulier. Les valeurs que vous spécifiez dans cette section exactement correspond à l’opération de placement dans l’API REST pour la création de ce type de ressource. Lorsque vous créez un compte de stockage, vous devez fournir un **accountType**. Vous pouvez remarquer dans l' [API REST pour la création d’un compte de stockage](https://msdn.microsoft.com/library/azure/mt163564.aspx) que la section Propriétés de l’opération reste contienne également une propriété **accountType** , les valeurs autorisées sont présentées. Dans cet exemple, le type de compte est défini sur **Standard_LRS**, mais vous pouvez spécifier une autre valeur ou permettre aux utilisateurs de passer dans le type de compte en tant que paramètre.

Maintenant nous allons passer à la section **paramètres** et voir comment vous définissez le nom du compte de stockage. Vous pouvez en savoir plus sur l’utilisation de paramètres [paramètres](resource-group-authoring-templates.md#parameters). 

```json
"parameters" : {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Storage Account Name"
      }
    }
}
```
Ici, vous avez défini un paramètre de type chaîne contenant le nom du compte de stockage. La valeur pour ce paramètre est fournie au cours du déploiement du modèle.

## <a name="deploying-the-template"></a>Déploiement du modèle
Nous avons un modèle pour la création d’un nouveau compte de stockage total. Comme vous le savez, le modèle a été enregistré dans le fichier **azuredeploy.json** :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters" : {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Storage Account Name"
      }
    }
  },  
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[parameters('storageAccountName')]",
      "apiVersion": "2015-06-15",
      "location": "[resourceGroup().location]",
      "properties": {
        "accountType": "Standard_LRS"
      }
    }
  ]
}
```

Il existe quelques façons de déployer un modèle, comme vous pouvez le voir dans l' [article de déploiement de ressources](resource-group-template-deploy.md). Pour déployer le modèle à l’aide de PowerShell Azure, utilisez :

```powershell
# create a new resource group
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West Europe"

# deploy the template to the resource group
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile azuredeploy.json
```

Ou, pour déployer le modèle à l’aide d’Azure infrastructure du langage commun, utilisez :

```
azure group create -n ExampleResourceGroup -l "West Europe"

azure group deployment create -f azuredeploy.json -g ExampleResourceGroup -n ExampleDeployment
```

Vous êtes maintenant l’heureux propriétaire d’un compte de stockage !

Étapes suivantes sera pour ajouter toutes les ressources nécessaires au déploiement de l’architecture décrite au début de ce didacticiel. Vous allez ajouter ces ressources dans le même modèle que vous avez travaillé.

## <a name="availability-set"></a>Jeu de disponibilité
Après la définition du compte de stockage, ajoutez un attirer défini pour les ordinateurs virtuels. Dans ce cas, il n’existe aucune propriété supplémentaire requise, afin de sa définition est assez simple. Consultez l' [API REST pour la création d’un ensemble de disponibilité](https://msdn.microsoft.com/library/azure/mt163607.aspx) de la section Propriétés complètes, au cas où vous souhaitez définir les mise à jour de domaine Nb et défaillance domaine compter des valeurs.

```json
{
   "type": "Microsoft.Compute/availabilitySets",
   "name": "[variables('availabilitySetName')]",
   "apiVersion": "2015-06-15",
   "location": "[resourceGroup().location]",
   "properties": {}
}
```

Notez que le **nom** est défini sur la valeur d’une variable. Pour ce modèle, le nom de l’ensemble de disponibilité est nécessaire à plusieurs endroits différents. Vous pouvez gérer plus facilement votre modèle en définissant une fois cette valeur et son utilisation dans plusieurs emplacements.

La valeur que vous spécifiez pour **type** contient le fournisseur de ressources et le type de ressource. Pour les jeux de disponibilité, le fournisseur de ressources est **Microsoft.Compute** et le type de ressource est **availabilitySets**. Vous pouvez obtenir la liste de fournisseurs de ressources disponibles en exécutant la commande PowerShell suivante :

```powershell
    Get-AzureRmResourceProvider -ListAvailable
```

Ou, si vous utilisez Azure infrastructure du langage commun, vous pouvez exécuter la commande suivante :
```
    azure provider list
```
Étant donné que dans cette rubrique vous créez avec des comptes de stockage, machines virtuelles et mise en réseau virtuelle, vous allez utiliser :

- Microsoft.Storage
- Microsoft.Compute
- Microsoft.Network

Pour afficher les types de ressources d’un fournisseur particulier, exécutez la commande PowerShell suivante :

```powershell
    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute).ResourceTypes
```

Ou, pour Azure infrastructure du langage commun, la commande suivante sera retourner les types disponibles au format JSON et enregistrez-le dans un fichier.

```
    azure provider show Microsoft.Compute --json > c:\temp.json
```

Vous devriez voir **availabilitySets** dans l’un des types de **Microsoft.Compute**. Le nom complet du type d’est **Microsoft.Compute/availabilitySets**. Vous pouvez déterminer le nom du type de ressource pour les ressources dans le modèle que vous.

## <a name="public-ip"></a>Adresse IP publique
Définir une adresse IP publique. Là encore, examinez l' [API REST pour les adresses IP publiques](https://msdn.microsoft.com/library/azure/mt163590.aspx) pour les propriétés à définir.

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[parameters('publicIPAddressName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('dnsNameforLBIP')]"
    }
  }
}
```

La méthode d’allocation est définie sur **dynamique** , mais vous pouvez affecter la valeur que vous avez besoin ou le configurer pour accepter une valeur de paramètre. Vous avez activé les utilisateurs de votre modèle pour passer une valeur pour l’étiquette de nom de domaine.

À présent, nous allons voir comment vous déterminez **apiVersion**. La valeur que vous spécifiez simplement correspond à la version de l’API REST que vous souhaitez utiliser lors de la création de la ressource. Par conséquent, vous pouvez consulter la documentation de l’API REST de ce type de ressource. Ou bien, vous pouvez exécuter la commande PowerShell suivante pour un type particulier.

```powershell
    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Network).ResourceTypes | Where-Object ResourceTypeName -eq publicIPAddresses).ApiVersions
```
Qui renvoie les valeurs suivantes :

    2015-06-15
    2015-05-01-preview
    2014-12-01-preview

Pour afficher les versions de l’API avec Azure infrastructure du langage commun, exécutez la même commande **Afficher azure fournisseur** mentionnée précédemment.

Lorsque vous créez un nouveau modèle, sélectionnez la dernière version de l’API.

## <a name="virtual-network-and-subnet"></a>Sous-réseau et réseau virtuel
Créer un réseau virtuel avec un seul sous-réseau. Observez l' [API REST pour les réseaux virtuels](https://msdn.microsoft.com/library/azure/mt163661.aspx) pour toutes les propriétés définir.

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/virtualNetworks",
   "name": "[parameters('vnetName')]",
   "location": "[resourceGroup().location]",
   "properties": {
     "addressSpace": {
       "addressPrefixes": [
         "10.0.0.0/16"
       ]
     },
     "subnets": [
       {
         "name": "[variables('subnetName')]",
         "properties": {
           "addressPrefix": "10.0.0.0/24"
         }
       }
     ]
   }
}
```

## <a name="load-balancer"></a>Équilibrage de charge
Maintenant, vous allez créer un équilibrage de charge qui fait face à externes. Étant donné que ce programme d’équilibrage de charge utilise l’adresse IP, vous devez déclarer une dépendance sur l’adresse IP publique dans la section **dependsOn** . Cela signifie que l’équilibrage de charge n’est pas déployée jusqu'à ce que l’adresse IP a terminé de déploiement. Sans définir cette dépendance, vous recevrez un message d’erreur, car le Gestionnaire de ressources tentera de déployer les ressources en parallèle et va tenter de définir l’équilibrage de charge à l’adresse IP publique qui n’existe pas encore. 

Vous allez également créer un groupe d’adresses principal, quelques règles NAT entrants RDP dans les ordinateurs virtuels et une règle équilibrage de charge avec une sonde tcp sur le port 80 dans la définition de cette ressource. Extraction de l' [API REST pour équilibrage de charge](https://msdn.microsoft.com/library/azure/mt163574.aspx) pour toutes les propriétés.

```json
{
   "apiVersion": "2015-06-15",
   "name": "[parameters('lbName')]",
   "type": "Microsoft.Network/loadBalancers",
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]"
   ],
   "properties": {
     "frontendIPConfigurations": [
       {
         "name": "LoadBalancerFrontEnd",
         "properties": {
           "publicIPAddress": {
             "id": "[variables('publicIPAddressID')]"
           }
         }
       }
     ],
     "backendAddressPools": [
       {
         "name": "BackendPool1"
       }
     ],
     "inboundNatRules": [
       {
         "name": "RDP-VM0",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50001,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       },
       {
         "name": "RDP-VM1",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50002,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       }
     ],
     "loadBalancingRules": [
       {
         "name": "LBRule",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "backendAddressPool": {
             "id": "[variables('lbPoolID')]"
           },
           "protocol": "tcp",
           "frontendPort": 80,
           "backendPort": 80,
           "enableFloatingIP": false,
           "idleTimeoutInMinutes": 5,
           "probe": {
             "id": "[variables('lbProbeID')]"
           }
         }
       }
     ],
     "probes": [
       {
         "name": "tcpProbe",
         "properties": {
           "protocol": "tcp",
           "port": 80,
           "intervalInSeconds": 5,
           "numberOfProbes": 2
         }
       }
     ]
   }
}
```

## <a name="network-interface"></a>Interface réseau
Vous allez créer 2 interfaces réseau, une pour chaque machine virtuelle. Plutôt que d’avoir à inclure des entrées en double pour les interfaces réseau, vous pouvez utiliser la [fonction copyIndex()](resource-group-create-multiple.md) pour parcourir la boucle copie (appelée nicLoop) et créer les interfaces réseau nombre, telle que définie dans le `numberOfInstances` variables. L’interface réseau dépend de la création du réseau virtuel et l’équilibrage de charge. Il utilise le sous-réseau défini dans la création de réseau virtuel et l’id d’équilibrage de charge pour configurer le pool d’adresses équilibrage de la charge et les règles NAT entrants.
Observez l' [API REST pour les interfaces réseau](https://msdn.microsoft.com/library/azure/mt163668.aspx) pour toutes les propriétés.

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/networkInterfaces",
   "name": "[concat(parameters('nicNamePrefix'), copyindex())]",
   "location": "[resourceGroup().location]",
   "copy": {
     "name": "nicLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "dependsOn": [
     "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]",
     "[concat('Microsoft.Network/loadBalancers/', parameters('lbName'))]"
   ],
   "properties": {
     "ipConfigurations": [
       {
         "name": "ipconfig1",
         "properties": {
           "privateIPAllocationMethod": "Dynamic",
           "subnet": {
             "id": "[variables('subnetRef')]"
           },
           "loadBalancerBackendAddressPools": [
             {
               "id": "[concat(variables('lbID'), '/backendAddressPools/BackendPool1')]"
             }
           ],
           "loadBalancerInboundNatRules": [
             {
               "id": "[concat(variables('lbID'),'/inboundNatRules/RDP-VM', copyindex())]"
             }
           ]
         }
       }
     ]
   }
}
```

## <a name="virtual-machine"></a>Machine virtuelle
Vous allez créer 2 machines virtuelles, à l’aide de la fonction copyIndex(), comme vous le faisiez dans la création des [interfaces réseau](#network-interface).
La création de machine virtuelle dépend du compte de stockage, un ensemble d’interface et la disponibilité de réseaux. Cet ordinateur virtuel est créé à partir d’une image marketplace, telle que définie dans le `storageProfile` , propriété - `imageReference` est utilisé pour définir l’éditeur d’images, offre, référence (SKU) et version. Enfin, un profil de diagnostic est configuré pour activer les diagnostics de la machine virtuelle. 

Pour trouver les propriétés pertinentes pour une image marketplace, suivez les articles [Sélectionnez Linux machine virtuelle images](./virtual-machines/virtual-machines-linux-cli-ps-findimage.md) ou [images de machine virtuelle Windows](./virtual-machines/virtual-machines-windows-cli-ps-findimage.md) .

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Compute/virtualMachines",
   "name": "[concat(parameters('vmNamePrefix'), copyindex())]",
   "copy": {
     "name": "virtualMachineLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
     "[concat('Microsoft.Network/networkInterfaces/', parameters('nicNamePrefix'), copyindex())]",
     "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]"
   ],
   "properties": {
     "availabilitySet": {
       "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
     },
     "hardwareProfile": {
       "vmSize": "[parameters('vmSize')]"
     },
     "osProfile": {
       "computerName": "[concat(parameters('vmNamePrefix'), copyIndex())]",
       "adminUsername": "[parameters('adminUsername')]",
       "adminPassword": "[parameters('adminPassword')]"
     },
     "storageProfile": {
       "imageReference": {
         "publisher": "[parameters('imagePublisher')]",
         "offer": "[parameters('imageOffer')]",
         "sku": "[parameters('imageSKU')]",
         "version": "latest"
       },
       "osDisk": {
         "name": "osdisk",
         "vhd": {
           "uri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net/vhds/','osdisk', copyindex(), '.vhd')]"
         },
         "caching": "ReadWrite",
         "createOption": "FromImage"
       }
     },
     "networkProfile": {
       "networkInterfaces": [
         {
           "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'),copyindex()))]"
         }
       ]
     },
     "diagnosticsProfile": {
       "bootDiagnostics": {
          "enabled": "true",
          "storageUri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net')]"
       }
     }
}
```

>[AZURE.NOTE] Pour les images publiés par des **fournisseurs tiers 3e**, vous devez spécifier une autre propriété nommée `plan`. Vous trouverez un exemple dans [ce modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/checkpoint-single-nic) à partir de la galerie de démarrage rapide. 

Vous avez terminé de définir les ressources pour votre modèle.

## <a name="parameters"></a>Paramètres

Dans la section Paramètres, définissez les valeurs qui peuvent être spécifiées lorsque vous déployez le modèle. Définissez uniquement les paramètres pour les valeurs que vous pensez que doit varier au cours du déploiement. Vous pouvez fournir une valeur par défaut pour un paramètre qui est utilisé si n’est fournie pendant le déploiement. Vous pouvez également définir les valeurs autorisées comme indiqué pour le paramètre **imageSKU** .

```json
"parameters": {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of storage account"
      }
    },
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Admin username"
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Admin password"
      }
    },
    "dnsNameforLBIP": {
      "type": "string",
      "metadata": {
        "description": "DNS for Load Balancer IP"
      }
    },
    "vmNamePrefix": {
      "type": "string",
      "defaultValue": "myVM",
      "metadata": {
        "description": "Prefix to use for VM names"
      }
    },
    "imagePublisher": {
      "type": "string",
      "defaultValue": "MicrosoftWindowsServer",
      "metadata": {
        "description": "Image Publisher"
      }
    },
    "imageOffer": {
      "type": "string",
      "defaultValue": "WindowsServer",
      "metadata": {
        "description": "Image Offer"
      }
    },
    "imageSKU": {
      "type": "string",
      "defaultValue": "2012-R2-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter"
      ],
      "metadata": {
        "description": "Image SKU"
      }
    },
    "lbName": {
      "type": "string",
      "defaultValue": "myLB",
      "metadata": {
        "description": "Load Balancer name"
      }
    },
    "nicNamePrefix": {
      "type": "string",
      "defaultValue": "nic",
      "metadata": {
        "description": "Network Interface name prefix"
      }
    },
    "publicIPAddressName": {
      "type": "string",
      "defaultValue": "myPublicIP",
      "metadata": {
        "description": "Public IP Name"
      }
    },
    "vnetName": {
      "type": "string",
      "defaultValue": "myVNET",
      "metadata": {
        "description": "VNET name"
      }
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_D1",
      "metadata": {
        "description": "Size of the VM"
      }
    }
  }
```

## <a name="variables"></a>Variables

Dans la section variables, vous pouvez définir les valeurs qui sont utilisées dans plusieurs endroits dans votre modèle, ou des valeurs qui sont générés à partir d’autres expressions ou des variables. Variables sont fréquemment utilisées pour simplifier la syntaxe de votre modèle.

```json
"variables": {
    "availabilitySetName": "myAvSet",
    "subnetName": "Subnet-1",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('vnetName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables ('subnetName'))]",
    "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]",
    "numberOfInstances": 2,
    "lbID": "[resourceId('Microsoft.Network/loadBalancers',parameters('lbName'))]",
    "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/LoadBalancerFrontEnd')]",
    "lbPoolID": "[concat(variables('lbID'),'/backendAddressPools/BackendPool1')]",
    "lbProbeID": "[concat(variables('lbID'),'/probes/tcpProbe')]"
  }
```

Vous avez terminé le modèle ! Vous pouvez comparer votre modèle par rapport au modèle dans la [Galerie de démarrage rapide](https://github.com/Azure/azure-quickstart-templates) sous [2 machines virtuelles avec l’équilibrage de charge et chargement le modèle de règles équilibrage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules)complet. Votre modèle peut être légèrement différent basée sur l’utilisation des numéros de version différents. 

Vous pouvez redéployez le modèle en utilisant les mêmes commandes que vous avez utilisé lorsque vous déployez le compte de stockage. Vous n’avez pas besoin de supprimer le compte de stockage avant de déployer de nouveau, car le Gestionnaire de ressources ignorent les ressources ré-création qui existent déjà et n’ont pas changé.

## <a name="next-steps"></a>Étapes suivantes

- [Azure Gestionnaire de ressources du modèle visualiseur (ARMViz)](http://armviz.io/#/) est un excellent outil pour visualiser les modèles de processeur, dès qu’elles peuvent sont trop volumineux pour comprendre simplement de lire le fichier json.
- Pour en savoir plus sur la structure d’un modèle, voir [Gestionnaire de ressources Azure de création de modèles](resource-group-authoring-templates.md).
- Pour en savoir plus sur le déploiement d’un modèle, voir [déploiement d’un groupe de ressources avec le Gestionnaire de ressources Azure modèle](resource-group-template-deploy.md)
