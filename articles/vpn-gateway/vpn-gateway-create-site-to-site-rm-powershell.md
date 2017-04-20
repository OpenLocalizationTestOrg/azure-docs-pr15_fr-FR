<properties
   pageTitle="Créer un réseau virtuel avec une connexion de Site à Site VPN à l’aide du Gestionnaire de ressources Azure et PowerShell | Microsoft Azure"
   description="Cet article décrit la création d’un VNet à l’aide du modèle de déploiement Gestionnaire de ressources et de se connecter à votre réseau local en local à l’aide d’une connexion de passerelle VPN S2S."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/14/2016"
   ms.author="cherylmc"/>

# <a name="create-a-vnet-with-a-site-to-site-connection-using-powershell"></a>Créer un VNet avec une connexion de Site à l’aide de PowerShell

> [AZURE.SELECTOR]
- [Responsable de ressources - portail Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Responsable de ressources - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
- [Classique - portail classique](vpn-gateway-site-to-site-create.md)

Cet article vous guide dans la création d’un réseau virtuel et une connexion de passerelle VPN de Site à votre réseau local en utilisant le modèle de déploiement d’Azure le Gestionnaire de ressources. Connexions de Site à peuvent être utilisées pour croisée local et hybride configurations.

![Diagramme de Site à] (./media/vpn-gateway-create-site-to-site-rm-powershell/s2srmps.png "site à") 


### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Modèles de déploiement et méthodes pour les connexions de Site à

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

Le tableau suivant indique les modèles de déploiement actuellement disponibles et les méthodes pour les configurations de Site à. Lorsqu’un article avec des étapes de configuration est disponible, nous attacher directement à partir de ce tableau. 

[AZURE.INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Configurations supplémentaires

Si vous voulez vous connecter VNets ensemble, mais ne créez pas d’une connexion à un emplacement local, voir [configurer une connexion VNet-VNet](vpn-gateway-vnet-vnet-rm-ps.md). Si vous souhaitez ajouter une connexion à un Site à un VNet contenant déjà une connexion, voir [Ajouter une connexion S2S à un VNet avec une connexion de passerelle VPN existante](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).

## <a name="before-you-begin"></a>Avant de commencer

Vérifiez que vous avez les éléments suivants avant de commencer la configuration.

- Un périphérique VPN compatible et une personne qui est en mesure de configurer. Voir [à propos des appareils VPN](vpn-gateway-about-vpn-devices.md). Si vous ne familiarisé avec la configuration de votre appareil VPN, ou que vous ne connaissez pas l’adresse IP plages situées dans vos locaux configuration du réseau, vous devez coordonner avec une personne qui pourra vous fournir ces informations.

- Adresse IP publique tourné vers l’extérieur de votre périphérique VPN. Cette adresse IP ne peut pas être située derrière un NAT.
    
- Un abonnement Azure. Si vous n’avez pas un abonnement Azure, vous pouvez activer votre [avantages d’abonné MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrivez-vous vers le haut pour un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).
    
- La dernière version des applets de commande PowerShell de gestionnaire de ressources Azure. Découvrez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) pour plus d’informations sur l’installation les applets de commande PowerShell.


## <a name="Login"></a>1. Connectez-vous à votre abonnement 

Vérifiez que vous passez en mode de PowerShell pour utiliser les applets de commande Gestionnaire de ressources. Pour plus d’informations, voir [Utilisation de Windows PowerShell avec le Gestionnaire de ressources](../powershell-azure-resource-manager.md).

Ouvrez votre console PowerShell et connectez-vous à votre compte. Pour vous aider à vous connecter, utilisez l’exemple suivant :

    Login-AzureRmAccount

Vérifier les abonnements pour le compte.

    Get-AzureRmSubscription 

Spécifier l’abonnement que vous voulez utiliser.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## <a name="VNet"></a>2. créer un réseau virtuel et un sous-réseau passerelle

Les exemples utilisent un sous passerelle réseau de /28. Bien qu’il soit possible de créer un sous-réseau passerelle aussi petite que /29, nous vous recommandons de créer un sous-réseau plus grand incluant plusieurs adresses en sélectionnant au moins /28 ou /27. Cette option permet de suffisamment d’adresses pour accueillir les configurations possibles supplémentaires que vous souhaiterez peut-être à l’avenir.

Si vous disposez déjà d’un réseau virtuel avec un sous-réseau de passerelle est/29 ou plus, vous pouvez passer directement à [Ajouter votre passerelle réseau local](#localnet).


[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]  

### <a name="to-create-a-virtual-network-and-a-gateway-subnet"></a>Pour créer un réseau virtuel et un sous-réseau passerelle

L’exemple suivant permet de créer un réseau virtuel et un sous-réseau passerelle. Remplacez les valeurs pour le vôtre. 

Tout d’abord, créez un groupe de ressources :
    
    New-AzureRmResourceGroup -Name testrg -Location 'West US'

Ensuite, créez votre réseau virtuel. Vérifiez que les espaces d’adressage que vous spécifiez ne chevauchent pas un des espaces d’adressage que vous avez sur votre réseau local.

L’exemple suivant crée un réseau virtuel nommé *testvnet* et des deux sous réseaux, appelé un *GatewaySubnet* et l’autre appelé *Subnet1*. Il est important de créer un sous-réseau nommé spécifiquement *GatewaySubnet*. Si vous nommez autre chose, la configuration de votre connexion échouera. 

Définissez les variables.

    $subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
    $subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'

Créer la VNet.

    New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg `
    -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="gatewaysubnet"></a>Pour ajouter un sous-réseau passerelle à un réseau virtuel que vous avez déjà créé

Cette étape est requise uniquement si vous avez besoin ajouter un sous-réseau passerelle à un VNet que vous avez créé précédemment.

Vous pouvez créer votre sous-réseau passerelle à l’aide de l’exemple suivant. Veillez à nommer le sous-réseau passerelle « GatewaySubnet ». Si vous nommez autre chose, vous créez un sous-réseau, mais Azure ne traite comme un sous-réseau passerelle.

Définissez les variables.

    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet

Créer le sous-réseau passerelle.

    Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

Définir la configuration. 

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

## 3. <a name="localnet"> </a>ajouter votre passerelle réseau local

Dans un réseau virtuel, la passerelle réseau local signifie généralement votre emplacement local. Vous nommez le site par lequel Azure peut faire et également spécifier le préfixe d’espace adresse de la passerelle réseau local. 

Azure utilise le préfixe d’adresse IP que vous spécifiez pour identifier le trafic pour envoyer à votre emplacement local. Cela signifie que vous devez spécifier chaque préfixe d’adresse que vous souhaitez associer à la passerelle de votre réseau local. Vous pouvez facilement mettre à jour ces préfixes si votre réseau local est modifié. 

Lorsque vous utilisez les exemples de PowerShell, notez les points suivants :
    
- Le *GatewayIPAddress* est l’adresse IP de votre appareil VPN en local. Votre appareil VPN ne peut pas être situé derrière un NAT. 
- *AddressPrefix* est votre espace d’adressage en local.

Pour ajouter une passerelle réseau local avec un préfixe d’adresse unique :

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Pour ajouter une passerelle réseau local avec plusieurs préfixes d’adresses :

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')

### <a name="to-modify-ip-address-prefixes-for-your-local-network-gateway"></a>Pour modifier les préfixes d’adresse IP de la passerelle réseau local

Parfois, votre préfixes passerelle réseau local changent. Les étapes à que suivre pour modifier votre préfixes d’adresses IP dépendent si vous avez créé une connexion de passerelle VPN. Consultez la section [préfixes d’adresses IP modifier pour une passerelle réseau local](#modify) de cet article.


## <a name="PublicIP"></a>4. demande une adresse IP publique pour la passerelle VPN

Ensuite, demander une adresse IP publique à allouer à votre passerelle Azure VNet VPN. Il s’agit pas de la même adresse IP est affectée à votre appareil VPN ; au lieu de cela, il est affecté à la passerelle VPN Azure elle-même. Vous ne pouvez pas spécifier l’adresse IP que vous souhaitez utiliser. Il est affectée dynamiquement à votre passerelle. Vous utilisez cette adresse IP lorsque vous configurez votre appareil VPN en local pour se connecter à la passerelle.

La passerelle VPN Azure pour le modèle de déploiement du Gestionnaire de ressources actuellement prend uniquement en charge les adresses IP publiques en utilisant la méthode d’Allocation dynamique. Toutefois, cela signifie pas que l’adresse IP changera. Le seul moment où les modifications d’adresses IP VPN Azure passerelle est lors de la passerelle est supprimée et recréée. L’adresse IP passerelle ne change pas dans l’ensemble de redimensionnement, la réinitialisation ou autres maintenance/mises à niveau internes de votre passerelle VPN Azure.

Utiliser l’exemple de PowerShell suivant :

    $gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## <a name="GatewayIPConfig"></a>5. créer la configuration d’adressage IP passerelle

La configuration de passerelle définit le sous-réseau et l’adresse IP à utiliser. L’exemple suivant permet de créer votre configuration de la passerelle.

    $vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
    $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## <a name="CreateGateway"></a>6. créer la passerelle réseau virtuel

Dans cette étape, vous créez la passerelle réseau virtuel. Création d’une passerelle peut prendre beaucoup de temps. Fréquence de 45 minutes ou plus. 

Utilisez les valeurs suivantes :

- *GatewayType -* configuration d’un Site à Site est *Vpn*. La passerelle est de type toujours spécifique à la configuration que vous implémentez. Par exemple, d’autres configurations de la passerelle peuvent nécessiter - GatewayType ExpressRoute. 

- La *-VpnType* peut être *RouteBased* (appelées une passerelle dynamique dans une documentation) ou *PolicyBased* (appelées une passerelle statique dans une documentation). Pour plus d’informations sur les types de passerelle VPN, voir [Passerelles sur VPN](vpn-gateway-about-vpngateways.md#vpntype).
- La *-GatewaySku* peut être *simple*, *Standard*ou *bien*.   

        New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
        -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku Standard

## <a name="ConfigureVPNDevice"></a>7. configurer votre appareil VPN

À ce stade, vous devez l’adresse IP de la passerelle réseau virtuel pour configurer votre appareil VPN en local. Travailler avec le fabricant de votre appareil pour plus d’informations de configuration spécifiques. Vous pouvez faire référence aux [Périphériques VPN](vpn-gateway-about-vpn-devices.md) pour plus d’informations.

Pour rechercher l’adresse IP de votre passerelle réseau virtuel, utilisez l’exemple suivant :

    Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg

## <a name="CreateConnection"></a>8. créer la connexion VPN

Ensuite, créer la connexion VPN de Site à entre votre passerelle réseau virtuel et votre appareil VPN. N’oubliez pas de remplacer les valeurs par votre propre. La clé partagée doit correspondre à la valeur que vous avez utilisé pour la configuration de vos périphériques VPN. Notez que la `-ConnectionType` de Site à est *IPsec*. 

Définissez les variables.

    $gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
    $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

Créer la connexion.

    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
    -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
    -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

Après un bref délai, la connexion est établi. 

## <a name="toverify"></a>Pour vérifier une connexion VPN

Il existe plusieurs façons pour vérifier votre connexion VPN.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="modify"></a>Pour modifier les préfixes d’adresses IP pour une passerelle de réseau local

Si vous avez besoin de modifier les préfixes de la passerelle réseau local, procédez comme suit. Deux ensembles d’instructions sont fournis. Les instructions que vous choisissez dépendent si vous avez déjà créé votre connexion passerelle. 

[AZURE.INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modifygwipaddress"></a>Pour modifier l’adresse IP de passerelle pour une passerelle de réseau local

[AZURE.INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Étapes suivantes

- Vous pouvez ajouter des machines virtuelles à vos réseaux virtuel. Pour obtenir la procédure, voir [créer une Machine virtuelle](../virtual-machines/virtual-machines-windows-hero-tutorial.md) .

- Pour plus d’informations sur BGP, consultez la [Vue d’ensemble BGP](vpn-gateway-bgp-overview.md) et [comment configurer BGP](vpn-gateway-bgp-resource-manager-ps.md).

