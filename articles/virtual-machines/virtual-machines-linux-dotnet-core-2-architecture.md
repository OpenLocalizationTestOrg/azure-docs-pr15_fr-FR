<properties
   pageTitle="Déploiement de calculer les ressources avec les modèles de gestionnaire de ressources Azure | Microsoft Azure"
   description="Machine virtuelle Azure DotNet Core didacticiel"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/21/2016"
   ms.author="nepeters"/>

# <a name="application-architecture-with-azure-resource-manager-templates"></a>Architecture de l’application avec les modèles de gestionnaire de ressources Azure

Lorsque vous développez un déploiement d’Azure le Gestionnaire de ressources, cluster exigences doivent être mappés aux services et ressources Azure. Si une application est constituée de plusieurs points de terminaison http, une base de données et une mise en cache de service de données, les ressources Azure cet hôte de ces composants doit être version rationalisé. Par exemple, l’application de magasin de musique exemple inclut une application web qui est hébergée sur un ordinateur virtuel et une base de données SQL, qui est hébergé dans la base de données SQL Azure. 

Ce document en détail comment les ressources de cluster magasin de musique sont configurés dans l’exemple de modèle Azure le Gestionnaire de ressources. Toutes les dépendances et configurations uniques sont mis en surbrillance. Pour optimiser les performances, avant de déployer une instance de la solution à votre abonnement Azure et le travail ainsi que le modèle Azure le Gestionnaire de ressources. Le modèle complète sont accessibles ici – [Déploiement de magasin de musique sur Ubuntu](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

## <a name="virtual-machine"></a>Machine virtuelle

L’application de magasin de musique inclut une application web dans lequel les clients peuvent parcourir et acheter de la musique. Il existe plusieurs services Azure hébergeant des applications web, pour cet exemple, une Machine virtuelle est utilisé. À l’aide de l’exemple de modèle de magasin de musique, une machine virtuelle est déployée, installer un serveur web et le site Web de magasin de musique installé et configuré. Pour des raisons de cet article uniquement le déploiement machine virtuelle est détaillé. La configuration du serveur web et l’application est détaillée dans un article d’une version ultérieure.

Une machine virtuelle peuvent être ajoutée à un modèle à l’aide de l’Assistant Visual Studio ajouter une nouvelle ressource, ou en insérant JSON valide dans le modèle de déploiement. Lorsque vous déployez une machine virtuelle, plusieurs ressources connexes sont également nécessaires. Si vous utilisez Visual Studio pour créer le modèle, ces ressources sont créées pour vous. Si construire manuellement le modèle, ces ressources doivent être insérés et configuré.

Suivez ce lien pour afficher l’échantillon JSON dans le modèle de gestionnaire de ressources – [Machine virtuelle JSON](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L295).

```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[concat(variables('vmName'),copyindex())]",
  "location": "[resourceGroup().location]",
  "copy": {
    "name": "virtualMachineLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "tags": {
    "displayName": "virtual-machine"
  },
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('vhdStorageName'))]",
    "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]",
    "nicLoop"
  ],
  "properties": {
    "availabilitySet": {
      "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('availabilitySetName'))]"
    },
      ........<truncated>  
    }
```

Une fois déployé, les propriétés de la machine virtuelle peuvent être vues dans le portail Azure.

![Machine virtuelle](./media/virtual-machines-linux-dotnet-core/vm.png)

## <a name="storage-account"></a>Compte de stockage

Comptes de stockage ont beaucoup d’options de stockage et fonctionnalités. Pour le contexte de machines virtuelles Azure, un compte de stockage conserve des disques durs virtuels de la machine virtuelle et des disques de données supplémentaires. L’exemple de magasin de musique inclut un seul compte de stockage pour mettre en attente le disque dur virtuel de chaque machine virtuelle dans le déploiement. 

Suivez ce lien pour afficher l’échantillon JSON dans le modèle de gestionnaire de ressources – [Compte de stockage](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L109).


```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[variables('vhdStorageName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "storage-account"
  },
  "properties": {
    "accountType": "[variables('vhdStorageType')]"
  }
}
```

Un compte de stockage est associée avec une machine virtuelle à l’intérieur de la déclaration de modèle Gestionnaire de ressources de la machine virtuelle. 

Suivez ce lien pour afficher l’échantillon JSON dans le modèle de gestionnaire de ressources – [association Machine virtuelle et compte de stockage](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L341).

```none
"osDisk": {
  "name": "osdisk",
  "vhd": {
    "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/',variables('vhdStorageName')), '2015-06-15').primaryEndpoints.blob,'vhds/osdisk', copyindex(), '.vhd')]"
  },
  "caching": "ReadWrite",
  "createOption": "FromImage"
}
```

Après le déploiement, le compte de stockage peut être affiché dans le portail Azure.

![Compte de stockage](./media/virtual-machines-linux-dotnet-core/storacct.png)

Cliquer dans le conteneur de blob de compte de stockage, le fichier du disque dur virtuel pour chaque ordinateur virtuel déployé avec le modèle est visible.

![Disques durs virtuels](./media/virtual-machines-linux-dotnet-core/vhd.png)

Pour plus d’informations sur le stockage Azure, voir la [documentation du stockage Azure](https://azure.microsoft.com/documentation/services/storage/).

## <a name="virtual-network"></a>Réseau virtuel

Si une machine virtuelle requiert une mise en réseau interne telles que la possibilité de communiquer avec d’autres machines virtuelles et des ressources Azure, un réseau virtuel Azure est requis.  Un réseau virtuel n’effectue pas la machine virtuelle accessible via internet. La connectivité publique requiert une adresse IP publique, qui est détaillée plus loin dans cette série.

Suivez ce lien pour afficher l’échantillon JSON dans le modèle de gestionnaire de ressources – [réseau virtuel et sous-réseaux](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L136).

```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[variables('virtualNetworkName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroup'))]"
  ],
  "tags": {
    "displayName": "virtual-network"
  },
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "10.0.0.0/24"
      ]
    },
    "subnets": [
      {
        "name": "[variables('subnetName')]",
        "properties": {
          "addressPrefix": "10.0.0.0/24",
          "networkSecurityGroup": {
            "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroup'))]"
          }
        }
      }
    ]
  }
}
```

À partir du portail Azure, le réseau virtuel ressemble à l’image suivante. Notez que tous les ordinateurs virtuels déployés avec le modèle sont connectés au réseau virtuel.

![Réseau virtuel](./media/virtual-machines-linux-dotnet-core/vnet.png)

## <a name="network-interface"></a>Interface réseau

 Une interface de réseau connecte à une machine virtuelle à un réseau virtuel, et plus particulièrement à un dont a été défini dans le réseau virtuel. 
 
 Suivez ce lien pour afficher l’échantillon JSON dans le modèle de gestionnaire de ressources – [Interface réseau](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L166).
 
```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/networkInterfaces",
  "name": "[concat(variables('networkInterfaceNamePrefix'), copyindex())]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "network-interface"
  },
  "copy": {
    "name": "nicLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "dependsOn": [
    "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
    "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIpAddressName'))]",
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'), '/inboundNatRules/', 'SSH-VM', copyIndex())]"
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
              "id": "[variables('lbPoolID')]"
            }
          ],
          "loadBalancerInboundNatRules": [
            {
              "id": "[concat(variables('lbID'),'/inboundNatRules/SSH-VM', copyIndex())]"
            }
          ]
        }
      }
    ]
  }
}
```

Chaque ressource machine virtuelle comprend un profil de réseau. L’interface réseau est associé à la machine virtuelle dans ce profil.  

Suivez ce lien pour afficher l’échantillon JSON dans le modèle de gestionnaire de ressources – [Profil réseau Machine virtuelle](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L350).


```none
"networkProfile": {
  "networkInterfaces": [
    {
      "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('networkInterfaceNamePrefix'), copyindex()))]"
    }
  ]
}
```

À partir du portail Azure, l’interface réseau ressemble à l’image suivante. L’adresse IP interne et l’association machine virtuelle peuvent être vu sur la ressource d’interface réseau.

![Interface réseau](./media/virtual-machines-linux-dotnet-core/nic.png)

Pour plus d’informations sur les réseaux virtuel Azure, voir [documentation réseau virtuel Azure](https://azure.microsoft.com/documentation/services/virtual-network/).

## <a name="azure-sql-database"></a>Base de données SQL Azure

Outre une machine virtuelle hébergement le magasin de musique, une base de données SQL Azure est déployé pour héberger la base de données de magasin de musique. L’avantage de l’utilisation de base de données SQL Azure ici est un second jeu de machines virtuelles n’est pas nécessaire qu’échelle et disponibilité intégré au service.

Une base de données SQL Azure peut être ajouté à l’aide de la Visual Studio ajouter une nouvelle ressource Assistant, ou en insérant JSON valide dans un modèle. La ressource SQL Server inclut un nom d’utilisateur et mot de passe disposant de droits d’administration de l’instance SQL. En outre, une ressource de pare-feu SQL est ajoutée. Par défaut, les applications hébergées dans Azure sont en mesure de vous connecter à l’instance SQL. Un studio de gestion de SQL Server pour vous connecter à l’instance SQL, le pare-feu doit être configuré pour autoriser l’application externe. Pour la démonstration de magasin de musique, la configuration par défaut est prêt. 

Suivez ce lien pour afficher l’échantillon JSON dans le modèle de gestionnaire de ressources – [Base de données SQL Azure](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L401).


```none
{
  "apiVersion": "2014-04-01-preview",
  "type": "Microsoft.Sql/servers",
  "name": "[variables('musicStoreSqlName')]",
  "location": "[resourceGroup().location]",

  "dependsOn": [],
  "tags": {
    "displayName": "sql-music-store"
  },
  "properties": {
    "administratorLogin": "[parameters('adminUsername')]",
    "administratorLoginPassword": "[parameters('sqlAdminPassword')]"
  },
  "resources": [
    {
      "apiVersion": "2014-04-01-preview",
      "type": "firewallrules",
      "name": "firewall-allow-azure",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Sql/servers/', variables('musicStoreSqlName'))]"
      ],
      "properties": {
        "startIpAddress": "0.0.0.0",
        "endIpAddress": "0.0.0.0"
      }
    }
  ]
}
```

Une vue de SQL server et MusicStore de base de données comme indiqué dans le portail Azure.

![SQL Server](./media/virtual-machines-linux-dotnet-core/sql.png)

Pour plus d’informations sur le déploiement de base de données SQL Azure, consultez la [documentation de base de données SQL Azure](https://azure.microsoft.com/documentation/services/sql-database/).

## <a name="next-step"></a>Étape suivante

<hr>

[Étape 2 : Access et la sécurité dans Azure modèles Gestionnaire de ressources](./virtual-machines-linux-dotnet-core-3-access-security.md)
