<properties 
   pageTitle="Configurer forcé tunnel pour les connexions de Site à l’aide du modèle de déploiement du Gestionnaire de ressources | Microsoft Azure"
   description="Comment rediriger ou « force » tout le trafic Internet liées aux revenir à votre emplacement local."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/10/2016"
   ms.author="cherylmc" />

# <a name="configure-forced-tunneling-using-the-azure-resource-manager-deployment-model"></a>Configurer tunnel forcé à l’aide du modèle de déploiement d’Azure le Gestionnaire de ressources

> [AZURE.SELECTOR]
- [PowerShell - classique](vpn-gateway-about-forced-tunneling.md)
- [PowerShell - Gestionnaire de ressources](vpn-gateway-forced-tunneling-rm.md)

Tunnel forcé vous permet de rediriger ou « force « tout le trafic Internet liées aux retourner à votre emplacement local via un tunnel VPN de Site à pour inspection et l’audit. Il s’agit d’une condition de sécurité critique pour la plupart des entreprises informatiques stratégies.

Sans tunnel forcé, le trafic Internet lié à partir de vos ordinateurs virtuels dans Azure parcourt toujours à partir de l’infrastructure réseau Azure directement à Internet, sans l’option vous permet d’inspecter ou de vérifier le trafic. Accès Internet non autorisés susceptibles d’entraîner divulgation d’informations ou d’autres types de violations de sécurité

Cet article vous guide dans la configuration forcé tunnel pour les réseaux virtuels créés à l’aide du modèle de déploiement du Gestionnaire de ressources.

**À propos des modèles de déploiement d’Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

**Modèles de déploiement et d’outils pour tunnel forcé**

Une connexion tunnel forcée peut être configurée pour le modèle de déploiement classique et le modèle de déploiement du Gestionnaire de ressources. Consultez le tableau suivant pour plus d’informations. Nous apportez ce tableau nouveaux articles, des nouveaux modèles de déploiement et des outils supplémentaires sont disponibles pour cette configuration. Lorsqu’un article est disponible, nous attacher directement à partir de la table.

[AZURE.INCLUDE [vpn-gateway-table-forced-tunneling](../../includes/vpn-gateway-table-forcedtunnel-include.md)] 


## <a name="about-forced-tunneling"></a>Environ forcé tunnel


Le diagramme suivant illustre comment forcé works tunnel. 

![Forcé tunnel](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

Dans l’exemple ci-dessus, Frontend sous-réseau n’est pas forcé en tunnel. Les charges de travail dans le sous-réseau Frontend peuvent continuer à accepter et répondre aux demandes des clients directement à partir d’Internet. Les sous-réseaux milieu et le serveur principal sont forcés en tunnel. Toutes les connexions sortantes de ces deux sous réseaux à Internet doit être forcées ou redirigées vers un site local via une du tunnel VPN S2S.

Cela vous permet restreindre et inspecter accès à Internet à partir de vos machines virtuelles ou cloud services dans Azure, tout en continuant à activer votre architecture à plusieurs niveaux service obligatoire. Vous pouvez également appliquer tunnel forcé vers les réseaux virtuels entières si il n’y a aucune charges de travail avec accès Internet dans vos réseaux virtuel.

## <a name="requirements-and-considerations"></a>Configuration requise et remarques

Tunnel forcé dans Azure est configuré par le biais des itinéraires réseau virtuel définis par l’utilisateur. Rediriger le trafic vers un site local est exprimé sous forme d’un itinéraire par défaut à la passerelle VPN Azure. Pour plus d’informations sur le routage de définis par l’utilisateur et les réseaux virtuels, consultez [itinéraires et transfert IP définis par l’utilisateur](../virtual-network/virtual-networks-udr-overview.md).

- Chaque sous-réseau réseau virtuel a une table de routage système prédéfini. La table de routage système comporte les trois groupes d’itinéraires suivants :

    - **VNet local achemine :** Directement à la destination machines virtuelles dans le même réseau virtuel
    
    - **Itinéraires local :** À la passerelle VPN Azure
    
    - **Itinéraire par défaut :** Directement à Internet. Paquets destinés aux adresses IP privés non couverts par les deux itinéraires précédente seront supprimées.

-  Cette procédure utilise itinéraires définis par l’utilisateur (UDR) pour créer une table de routage pour ajouter un itinéraire par défaut, puis associer la table de routage pour votre sous-réseau VNet pour activer tunnel forcé sur ces sous-réseaux.

- Tunnel forcé doit être associée à un VNet qui comporte une passerelle VPN itinéraire. Vous devez définir un « site par défaut « parmi les sites locaux croisée local connecté au réseau virtuel.

- ExpressRoute forcé tunnel n’est pas configuré grâce à ce mécanisme, mais à la place, est activé en publiant un itinéraire par défaut via les sessions homologation ExpressRoute BGP. Consultez la [Documentation ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) pour plus d’informations.

## <a name="configuration-overview"></a>Présentation de la configuration

La procédure suivante permet de créer un groupe de ressources et un VNet. Vous devez ensuite créer une passerelle VPN et configurez tunnel forcé. Dans cette procédure, le réseau virtuel « MULTICOUCHE VNet » comporte 3 sous-réseaux : *Frontend*, *milieu de gamme*et *serveur principal*, avec 4 croisée locaux connexions : *DefaultSiteHQ*et 3 *Branches*.

Les étapes de la procédure la *DefaultSiteHQ* en tant que la connexion de site par défaut pour forcé tunnel et configurer le milieu de gamme et sous-réseaux principal à utiliser forcé tunnel.

    
## <a name="before-you-begin"></a>Avant de commencer

Vérifiez que vous avez les éléments suivants avant de commencer votre configuration.

- Un abonnement Azure. Si vous n’avez pas un abonnement Azure, vous pouvez activer votre [avantages d’abonné MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrivez-vous vers le haut pour un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).

- Vous devez installer la dernière version des applets de commande PowerShell de gestionnaire de ressources Azure (1.0 ou version ultérieure). Découvrez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) pour plus d’informations sur l’installation les applets de commande PowerShell.


## <a name="configure-forced-tunneling"></a>Configurer tunnel forcé

1. Dans la console PowerShell, connectez-vous à votre compte Azure. Cette applet de commande vous demande les informations d’identification pour votre compte Azure. Après la connexion, il télécharge vos paramètres de compte afin qu’elles soient disponibles à Azure PowerShell.

        Login-AzureRmAccount 

2. Obtenir une liste de vos abonnements Azure.

        Get-AzureRmSubscription

2. Spécifier l’abonnement que vous voulez utiliser. 

        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
        
3. Créer un groupe de ressources.

        New-AzureRmResourceGroup -Name "ForcedTunneling" -Location "North Europe"

4. Créez un réseau virtuel et spécifiez sous-réseaux. 

        $s1 = New-AzureRmVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
        $s2 = New-AzureRmVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
        $s3 = New-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
        $s4 = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
        $vnet = New-AzureRmVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4

5. Créer les passerelles réseau local.

        $lng1 = New-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
        $lng2 = New-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
        $lng3 = New-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
        $lng4 = New-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
        
6. Créer la règle d’acheminer et de table de routage.

        New-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
        $rt = Get-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
        Add-AzureRmRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
        Set-AzureRmRouteTable -RouteTable $rt


7. Associer la table d’itinéraires vers les sous-réseaux du milieu de gamme et serveur principal.

        $vnet = Get-AzureRmVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
        Set-AzureRmVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
        Set-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

8. Créer la passerelle avec un site par défaut. Cette étape gagner du temps pour s’exécuter, parfois 45 minutes ou plus, étant donné que vous créez et configuration de la passerelle.<br> La `-GatewayDefaultSite` est le paramètre applet de commande qui permet de la configuration du routage forcée à utiliser, prenez donc soin de configurer correctement ce paramètre. Ce paramètre est disponible dans PowerShell 1.0 ou version ultérieure.

        $pip = New-AzureRmPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
        $gwsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
        $ipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
        New-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewayDefaultSite $lng1 -EnableBgp $false

9. Établir des connexions VPN de Site à.

        $gateway = Get-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
        $lng1 = Get-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
        $lng2 = Get-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
        $lng3 = Get-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
        $lng4 = Get-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 

        New-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
        New-AzureRmVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
        New-AzureRmVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
        New-AzureRmVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"

        Get-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
        



