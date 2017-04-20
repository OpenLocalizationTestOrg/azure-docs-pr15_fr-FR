<properties
   pageTitle="Accès et la sécurité dans les modèles de gestionnaire de ressources Azure | Microsoft Azure" 
   description="Machine virtuelle Azure DotNet Core didacticiel"
   services="virtual-machines-windows"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/21/2016"
   ms.author="nepeters"/>

# <a name="access-and-security-in-azure-resource-manager-templates"></a>Accès et la sécurité dans le Gestionnaire de ressources Azure modèles

Applications hébergées dans Azure probablement doivent être access via internet ou un réseau privé virtuel / connexion itinéraire Express avec Azure. Avec l’exemple d’application magasin de musique, le site web soient accessibles sur internet avec une adresse IP publique. Avec l’accès établie, les connexions à l’application et l’accès aux ressources machine virtuelle doivent être sécurisés. Cette sécurité d’accès est fournie avec un groupe de sécurité réseau. 

Ce document en détail comment l’application musique du magasin sécurisée dans l’exemple de modèle Azure le Gestionnaire de ressources. Toutes les dépendances et configurations uniques sont mis en surbrillance. Pour optimiser les performances, avant de déployer une instance de la solution à votre abonnement Azure et le travail ainsi que le modèle Azure le Gestionnaire de ressources. Le modèle complète sont accessibles ici – [Musique Store déploiement sous Windows](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).


## <a name="public-ip-address"></a>Adresse IP publique

Pour fournir un accès public à une ressource Azure, une ressource d’adresse IP publique peut être utilisée. Adresse IP publique peut être configuré avec une adresse IP statique ou dynamique. Si une adresse dynamique est utilisée et la machine virtuelle est arrêtée et libérée, les adresses est supprimé. Quand l’ordinateur est démarré à nouveau, il peut être affectée une autre adresse IP publique. Pour empêcher une adresse IP de modifier, une adresse IP réservée peut être utilisée. 

Une adresse IP publique peuvent être ajoutée à un modèle de gestionnaire de ressources Azure à l’aide de l’Assistant Visual Studio ajouter nouvelle ressource, ou en insérant JSON valide dans un modèle. 

Suivez ce lien pour afficher l’échantillon JSON dans le modèle de gestionnaire de ressources – [Adresse IP publique](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L110).


```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[variables('publicIpAddressName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [],
  "tags": {
    "displayName": "public-ip"
  },
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('publicipaddressDnsName')]"
    }
  }
}
```

Une adresse IP publique peut être associée à une carte réseau virtuelle, ou un programme d’équilibrage de charge. Dans cet exemple, étant donné que le site Web de magasin de musique équilibrée de charge est sur plusieurs machines virtuelles, l’adresse IP est connecté à l’équilibrage de charge.

Suivez ce lien pour afficher l’échantillon JSON dans le modèle de gestionnaire de ressources – [adresse IP publique association avec équilibrage de charge](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L211).

```none
"frontendIPConfigurations": [
  {
    "properties": {
      "publicIPAddress": {
        "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIpAddressName'))]"
      }
    },
    "name": "LoadBalancerFrontend"
  }
]
```

L’adresse IP publique Guide à partir du portail Azure. Notez que l’adresse IP est associé à un équilibrage de charge et non une machine virtuelle. Programmes d’équilibrage de charge réseau sont détaillées dans le document suivant de cette série.

![Adresse IP publique](./media/virtual-machines-windows-dotnet-core/pubip-win.png)

Pour plus d’informations sur les adresses IP publiques Azure, voir [adresses IP dans Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md).

## <a name="network-security-group"></a>Groupe de sécurité réseau

Une fois que l’accès a été établie aux ressources Azure, cet accès doit être limité. Pour les machines virtuelles Azure, un accès sécurisé s’effectue à l’aide d’un groupe de sécurité réseau. Avec l’exemple d’application magasin de musique, tous les accès à la machine virtuelle est limité à l’exception de sur le port 80 pour l’accès http et le port 3389 pour l’accès RDP. Un groupe de sécurité réseau peuvent être ajouté à un modèle de gestionnaire de ressources Azure à l’aide de l’Assistant Visual Studio ajouter nouvelle ressource, ou en insérant JSON valide dans un modèle.

Suivez ce lien pour afficher l’échantillon JSON dans le modèle de gestionnaire de ressources – [Groupe de sécurité réseau](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L57).

```none
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Network/networkSecurityGroups",
  "name": "[variables('networkSecurityGroup')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "network-security-group"
  },
  "properties": {
    "securityRules": [
      {
        "name": "http",
        "properties": {
          "description": "http endpoint",
          "protocol": "Tcp",
          "sourcePortRange": "*",
          "destinationPortRange": "80",
          "sourceAddressPrefix": "*",
          "destinationAddressPrefix": "*",
          "access": "Allow",
          "priority": 100,
          "direction": "Inbound"
        }
      },
      ........<truncated> 
    ]
  }
},
```

Dans cet exemple, le groupe de sécurité réseau est associer à l’objet sous-réseau déclaré dans la ressource réseau virtuel. 

Suivez ce lien pour afficher l’échantillon JSON dans le modèle de gestionnaire de ressources – [association de groupe de sécurité réseau avec le réseau virtuel](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L143).


```none
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
```

Voici à quoi ressemble le groupe de sécurité réseau à partir du portail Azure. Notez qu’un NSG peut être associer une interface sous-réseau et / ou un réseau. Dans ce cas, le NSG est associé à un sous-réseau. Dans cette configuration, les règles de trafic entrant s’appliquent à tous les ordinateurs virtuels connectés au sous-réseau.

![Groupe de sécurité réseau](./media/virtual-machines-windows-dotnet-core/nsg-win.png)

Pour obtenir des informations détaillées sur les groupes de sécurité réseau, voir [qu’est un groupe de sécurité réseau]( https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/).

## <a name="next-step"></a>Étape suivante

<hr>

[Étape 3 : disponibilité et l’échelle dans Azure modèles Gestionnaire de ressources](./virtual-machines-windows-dotnet-core-4-availability-scale.md)
