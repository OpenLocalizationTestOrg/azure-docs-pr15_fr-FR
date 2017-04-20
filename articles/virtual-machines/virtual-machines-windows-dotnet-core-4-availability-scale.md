<properties
   pageTitle="Disponibilité et l’échelle dans les modèles de gestionnaire de ressources Azure | Microsoft Azure"
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

# <a name="availability-and-scale-in-azure-resource-manager-templates"></a>Disponibilité et l’échelle dans le Gestionnaire de ressources Azure modèles

Disponibilité et les proportions de faire référence à la disponibilité et la possibilité de répondre à la demande. Si une application doit être activées 99,9 % du temps, elle doit comporter une architecture qui permet plusieurs ressources cluster simultanées. Par exemple, plutôt que d’avoir un site Web, une configuration avec un niveau supérieur de disponibilité comprend plusieurs instances du même site, avec l’équilibrage de la technologie devant les. Dans cette configuration, une instance de l’application peut être prise vers le bas pour maintenance, tandis que les autres continuent à fonctionner. Échelle quant à eux fait référence à la capacité applications pour répondre à la demande. Avec une charge une application équilibrée, ajoutant ou en supprimant des instances du pool permet à une application à l’échelle pour répondre à la demande.

Ce document en détail comment le déploiement d’exemple de magasin de musique est configuré pour la disponibilité et l’échelle. Toutes les dépendances et configurations uniques sont mis en surbrillance. Pour optimiser les performances, avant de déployer une instance de la solution à votre abonnement Azure et le travail ainsi que le modèle Azure le Gestionnaire de ressources. Le modèle complète sont accessibles ici – [Musique Store déploiement sous Windows](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="availability-set"></a>Jeu de disponibilité

Un ensemble de disponibilité étendues logiquement Machines virtuelles Azure travers hôtes physiques et d’autres composants infrastructures tels que blocs d’alimentation et du matériel réseau physique. Jeux de disponibilité Assurez-vous qu’au cours de maintenance, échec du périphérique ou autres temps d’arrêt, pas toutes les machines virtuelles sont effectuées. Un ensemble de disponibilité peuvent être ajouté à un modèle de gestionnaire de ressources Azure à l’aide de l’Assistant Visual Studio ajouter nouvelle ressource ou insertion JSON valide dans un modèle.

Suivez ce lien pour afficher l’exemple JSON dans le modèle de gestionnaire de ressources – [Disponibilité défini](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L368).


```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/availabilitySets",
  "name": "[variables('availabilitySetName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "availability-set"
  },
  "properties": {
  }
}
```

Un ensemble de disponibilité est déclaré comme une propriété d’une ressource Machine virtuelle. 

Suivez ce lien pour afficher l’échantillon JSON dans le modèle de gestionnaire de ressources – [disponibilité définir une association avec Machine virtuelle](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L302).

```none
"properties": {
  "availabilitySet": {
    "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('availabilitySetName'))]"
  }
```
La disponibilité définie comme indiqué à partir du portail Azure. Chaque machine virtuelle et des détails sur la configuration sont décrites ci-après.

![Jeu de disponibilité](./media/virtual-machines-windows-dotnet-core/ase-win.png)

Pour obtenir des informations détaillées sur les jeux de disponibilité, voir [Gérer la disponibilité des machines virtuelles](./virtual-machines-windows-manage-availability.md). 

## <a name="network-load-balancer"></a>Équilibrage de charge réseau

Qu’un ensemble de disponibilité fournit la tolérance de panne, un équilibrage de charge rend nombre d’instances de l’application disponible sur une seule adresse de réseau. Plusieurs instances d’une application peuvent être hébergés sur plusieurs machines virtuelles, chacun d'entre eux connecté à un équilibrage de charge. Comme l’application est accessible, l’équilibrage de charge achemine la demande entrante tous les membres joints. Un équilibrage de charge peuvent être ajouté à l’aide de l’Assistant Visual Studio ajouter nouvelle ressource ou en insérant correctement mis en forme ressource JSON dans le modèle Azure le Gestionnaire de ressources.

Suivez ce lien pour afficher l’échantillon JSON dans le modèle de gestionnaire de ressources – [Équilibrage de charge réseau](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L198).

```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/loadBalancers",
  "name": "[variables('loadBalancerName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "load-balancer"
  },
  ........<truncated>
}
```

Étant donné que l’exemple d’application est visible sur internet avec une adresse IP publique, cette adresse est associée à l’équilibrage de charge. 

Suivez ce lien pour afficher l’échantillon JSON dans le modèle de gestionnaire de ressources – [association d’équilibrage de charge réseau avec adresse IP publique](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L211).

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

À partir du portail Azure, la vue d’ensemble d’équilibrage de charge réseau affiche l’association avec l’adresse IP.

![Équilibrage de charge réseau](./media/virtual-machines-windows-dotnet-core/nlb-win.png)

## <a name="load-balancer-rule"></a>Règle d’équilibrage de charge

Lorsque vous utilisez un programme d’équilibrage de charge, les règles sont configurées qui contrôlent comment le trafic est équilibré entre les ressources initial. Avec l’application de magasin de musique exemple, le trafic arrive sur le port 80 de l’adresse IP publique et est répercuté sur le port 80 de toutes les machines virtuelles. 

Suivez ce lien pour afficher l’échantillon JSON dans le modèle de gestionnaire de ressources – [Règle d’équilibrage de charge](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L226).


```none
"loadBalancingRules": [
  {
    "name": "[variables('loadBalencerRule')]",
    "properties": {
      "frontendIPConfiguration": {
        "id": "[concat(resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName')), '/frontendIPConfigurations/LoadBalancerFrontend')]"
      },
      "backendAddressPool": {
        "id": "[variables('lbPoolID')]"
      },
      "protocol": "Tcp",
      "frontendPort": 80,
      "backendPort": 80,
      "enableFloatingIP": false,
      "idleTimeoutInMinutes": 5,
      "probe": {
        "id": "[variables('lbProbeID')]"
      }
    }
  }
]
```

Une vue de la règle d’équilibrage de charge réseau à partir du portail.

![Règle d’équilibrage de charge réseau](./media/virtual-machines-windows-dotnet-core/lbrule-win.png)

## <a name="load-balancer-probe"></a>Sonde d’équilibrage de charge

L’équilibrage de charge doit également surveiller chaque machine virtuelle afin que les demandes sont pris en charge uniquement à l’exécution des systèmes. Cette surveillance s’effectue par la détection constante d’un port prédéfini. Le déploiement de magasin de musique est configuré pour sonde port 80 sur toutes les machines virtuelles inclus. 

Suivez ce lien pour afficher l’échantillon JSON dans le modèle de gestionnaire de ressources – [Équilibrage de charge sonde](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L247).


```none
"probes": [
  {
    "properties": {
      "protocol": "Tcp",
      "port": 80,
      "intervalInSeconds": 15,
      "numberOfProbes": 2
    },
    "name": "lbprobe"
  }
]
```

La sonde d’équilibrage de charge vue à partir du portail Azure.

![Sonde équilibrage de charge réseau](./media/virtual-machines-windows-dotnet-core/lbprobe-win.png)

## <a name="inbound-nat-rules"></a>NAT règles de trafic entrant

Lorsque vous utilisez un équilibrage de charge, les règles doivent être mis en place pour fournir un accès non ajustées non charge sur chaque ordinateur virtuel. Par exemple, lorsque vous créez une connexion RDP avec chaque machine virtuelle, ce trafic ne doit pas être équilibrage de charge, au lieu de cela un chemin d’accès prédéterminé doit être configuré. Chemins d’accès prédéterminés sont configurés à l’aide d’une ressource entrante NAT règle. À l’aide de cette ressource, la communication entrante peut être mappée à des Machines virtuelles individuelles. 

Avec l’application magasin de musique, un port commençant sur 5 000 est mappé au port 3389 sur chaque ordinateur virtuel pour l’accès RDP. La `copyindex()` fonction est utilisée pour incrémenter le port entrant, telles que la deuxième Machine virtuelle reçoit un port entrant de 5001, la troisième 5002 et ainsi de suite.

Suivez ce lien pour afficher l’échantillon JSON dans le modèle de gestionnaire de ressources – [Règles NAT de trafic entrant](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L260). 

```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/loadBalancers/inboundNatRules",
  "name": "[concat(variables('loadBalancerName'), '/', 'RDP-VM', copyIndex())]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "load-balancer-nat-rule"
  },
  "copy": {
    "name": "lbNatLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "dependsOn": [
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
  ],
  "properties": {
    "frontendIPConfiguration": {
      "id": "[variables('ipConfigID')]"
    },
    "protocol": "tcp",
    "frontendPort": "[copyIndex(5000)]",
    "backendPort": 3389,
    "enableFloatingIP": false
  }
}
```

Exemple d’une règle de trafic entrant NAT comme indiqué dans le portail Azure. Une règle RDP NAT est créée pour chaque machine virtuelle dans le déploiement.

![Règle NAT de trafic entrant](./media/virtual-machines-windows-dotnet-core/natrule-win.png)

Pour obtenir des informations détaillées sur l’équilibrage de charge réseau Azure, voir [équilibrage de charge pour les services d’infrastructure Azure](./virtual-machines-windows-load-balance.md).

## <a name="deploy-multiple-vms"></a>Déployer plusieurs ordinateurs virtuels

Enfin, pour une disponibilité définir ou équilibrage de charge pour fonctionner, plusieurs machines virtuelles sont nécessaires. Plusieurs ordinateurs virtuels peuvent être déployés à l’aide de la fonction de copie de modèle Azure le Gestionnaire de ressources. À l’aide de la fonction de copie, il n’est pas nécessaire de définir un nombre limité de Machines virtuelles, au lieu de cela cette valeur peut être fournie dynamiquement au moment de déploiement. La fonction de copie consomme le nombre d’instances à créer et les poignées de déploiement d’un nombre suffisant de machines virtuelles et des ressources associées.

Dans le modèle de musique Store exemple, un paramètre est défini qui conduit dans un nombre d’instances. Ce nombre est utilisé dans le modèle lors de la création des machines virtuelles et les ressources associées.

```none
"numberOfInstances": {
  "type": "int",
  "minValue": 1,
  "defaultValue": 2,
  "metadata": {
    "description": "Number of VM instances to be created behind load balancer."
  }
},
```

Sur la ressource Machine virtuelle, échanges copie est remplacé par un nom et le numéro du paramètre instances permettent de contrôler le nombre de copies qui en résulte.

Suivez ce lien pour afficher l’échantillon JSON dans le modèle de gestionnaire de ressources – [Machine virtuelle copie fonction](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L290). 


```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[concat(variables('vmName'),copyindex())]",
  "location": "[resourceGroup().location]",
  "copy": {
    "name": "virtualMachineLoop",
    "count": "[parameters('numberOfInstances')]"
  }
```

L’itération en cours de la fonction de copie est accessible avec le `copyIndex()` fonction. La valeur de la fonction index copie peut être utilisée nommer machines virtuelles et autres ressources. Par exemple, si les deux instances d’une machine virtuelle sont déployés, dont elles ont besoin des noms différents. La `copyIndex()` fonction pouvant être utilisée dans le cadre du nom de la machine virtuelle pour créer un nom unique. Exemple de la `copyindex()` utilisée pour nommer à des fins de fonction peut être vues dans la ressource Machine virtuelle. Ici, le nom d’ordinateur est une concaténation de la `vmName` paramètre et la `copyIndex()` fonction. 

Suivez ce lien pour afficher l’échantillon JSON dans le modèle de gestionnaire de ressources – [Fonction Index copie](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L309). 


```none
"osProfile": {
  "computerName": "[concat(variables('vmName'),copyindex())]",
  "adminUsername": "[parameters('adminUsername')]",
  "adminPassword": "[parameters('adminPassword')]"
}
```

La `copyIndex` fonction est utilisée à plusieurs reprises dans l’exemple de modèle de magasin de musique. Ressources et des fonctions utilisant `copyIndex` contenir tous les éléments spécifiques à une seule instance de la machine virtuelle ce et l’interface réseau, les règles d’équilibrage de charge, et les dépend de fonctions. 

Pour plus d’informations sur la fonction de copie, voir [créer plusieurs instances de ressources dans le Gestionnaire de ressources Azure](../resource-group-create-multiple.md).

## <a name="next-step"></a>Étape suivante

<hr>

[Étape 4 : déploiement d’applications avec les modèles de gestionnaire de ressources Azure](./virtual-machines-windows-dotnet-core-5-app-deployment.md)