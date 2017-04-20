<properties
   pageTitle="Ouvrez les ports pour une machine virtuelle à l’aide de PowerShell | Microsoft Azure"
   description="Découvrez comment ouvrir un port / créer un point de terminaison de votre machine virtuelle Windows à l’aide du mode de déploiement de gestionnaire de ressources Azure et Azure PowerShell"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="opening-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>Ouverture de ports et les points de terminaison pour une machine virtuelle dans Azure à l’aide de PowerShell
[AZURE.INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Commandes rapides
Pour créer des règles d’un groupe de sécurité réseau et et vous devez [la dernière version d’Azure PowerShell installé](../powershell-install-configure.md). Vous pouvez également [effectuer ces étapes à l’aide du portail Azure](virtual-machines-windows-nsg-quickstart-portal.md).

Connectez-vous à votre compte Azure :

```powershell
Login-AzureRmAccount
```

Dans les exemples suivants, remplacez les noms de paramètres exemple avec vos propres valeurs. Exemples de noms de paramètre inclus `myResourceGroup`, `myNetworkSecurityGroup`, et `myVnet`.

Créer une règle. L’exemple suivant crée une règle nommée `myNetworkSecurityGroupRule` pour autoriser le trafic TCP sur le port 80 :

```powershell
$httprule = New-AzureRmNetworkSecurityRuleConfig -Name "myNetworkSecurityGroupRule" `
    -Description "Allow HTTP" -Access "Allow" -Protocol "Tcp" -Direction "Inbound" `
    -Priority "100" -SourceAddressPrefix "Internet" -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 80
```

Ensuite, créez votre groupe de sécurité réseau et attribuer la règle HTTP que vous venez de créer comme suit. L’exemple suivant crée un groupe de sécurité réseau nommé `myNetworkSecurityGroup`:

```powershell
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNetworkSecurityGroup" -SecurityRules $httprule
```

Maintenant nous allons affecter votre groupe de sécurité réseau à un sous-réseau. L’exemple suivant assigne un réseau virtuel nommé `myVnet` à la variable `$vnet`:

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Associer votre groupe de sécurité réseau à votre sous-réseau. L’exemple suivant associe le sous-réseau nommé `mySubnet` avec votre groupe de sécurité réseau :

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Enfin, mettez à jour votre réseau virtuel afin que vos modifications soient prises en compte :

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>Plus d’informations sur les groupes de sécurité réseau
Les commandes rapides ici permettent d’être opérationnel avec le trafic vers votre ordinateur virtuel. Groupes de sécurité réseau fournissent des nombreuses nouvelles fonctionnalités et précision pour contrôler l’accès à vos ressources. Vous pouvez en savoir plus sur la [Création d’un groupe de sécurité réseau et les règles et ici](../virtual-network/virtual-networks-create-nsg-arm-ps.md).

Vous pouvez définir des groupes de sécurité réseau et les règles et dans le cadre de modèles Azure le Gestionnaire de ressources. En savoir plus sur la [Création de groupes de sécurité réseau avec les modèles](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Si vous devez utiliser transfert de port pour mapper un port externe unique à un port interne sur votre ordinateur virtuel, utilisez un programme d’équilibrage de charge et les règles de la traduction d’adresses réseau (NAT). Par exemple, vous souhaiterez peut-être exposer le port TCP 8080 avec l’extérieur et que le trafic dirigé vers le port TCP 80 sur un ordinateur virtuel. Vous pouvez en savoir plus sur la [Création d’un équilibrage de charge via Internet](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>Étapes suivantes
Dans cet exemple, vous avez créé une règle simple pour autoriser le trafic HTTP. Vous pouvez trouver des informations sur la création des environnements plus détaillées dans les articles suivants :

- [Vue d’ensemble du Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md)
- [Qu’est un groupe de sécurité réseau (NSG) ?](../virtual-network/virtual-networks-nsg.md)
- [Azure vue d’ensemble du Gestionnaire de ressources pour les programmes d’équilibrage de charge](../load-balancer/load-balancer-arm.md)