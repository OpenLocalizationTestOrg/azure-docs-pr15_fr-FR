<properties 
   pageTitle="À propos des paramètres de passerelle VPN pour les passerelles réseau virtuel | Microsoft Azure"
   description="Découvrez les paramètres de la passerelle VPN pour réseau virtuel Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="cherylmc" />

# <a name="about-vpn-gateway-settings"></a>À propos des paramètres de la passerelle VPN

Une solution de connexion VPN passerelle repose sur la configuration de plusieurs ressources afin de pouvoir pour envoyer le trafic réseau entre les réseaux virtuels et emplacements local. Chaque ressource contient des paramètres configurables. La combinaison des ressources et des paramètres détermine le résultat de la connexion.

Les sections dans cet article décrivent les ressources et les paramètres relatifs à une passerelle VPN dans le modèle de déploiement du **Gestionnaire de ressources** . Il peut s’avérer utile pour afficher les configurations disponibles en utilisant les diagrammes de topologie de connexion. Vous pouvez trouver les descriptions et les diagrammes de la topologie pour chaque solution de connexion dans l’article [Sur VPN passerelle](vpn-gateway-about-vpngateways.md) . 

## <a name="gwtype"></a>Types de passerelle

Chaque réseau virtuel ne peut avoir une passerelle réseau virtuel de chaque type. Lorsque vous créez une passerelle réseau virtuel, assurez-vous que le type de passerelle est correct pour votre configuration.

Les valeurs disponibles pour - GatewayType sont : 

- VPN
- ExpressRoute

Une passerelle VPN requiert la `-GatewayType` *Vpn*.  

Exemple :

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
    -VpnType RouteBased
 

## <a name="gwsku"></a>Références SKU passerelle


[AZURE.INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configuring-the-gateway-sku"></a>Configuration de la passerelle de référence (SKU)

**Spécification de la passerelle de référence (SKU) dans le portail Azure**

Si vous utilisez le portail Azure pour créer une passerelle réseau virtuel Gestionnaire de ressources, vous pouvez sélectionner la passerelle de référence (SKU) à l’aide de la liste déroulante. Les options que vous soyez proposées correspondent au type de passerelle et tapez VPN que vous sélectionnez.

Par exemple, si vous sélectionnez le type de passerelle « VPN » et le type « stratégie basées sur VPN », vous voyez uniquement la référence SKU 'Base' car c’est le seul SKU disponible pour les PolicyBased VPN. Si vous sélectionnez 'Basée sur un itinéraire', vous pouvez sélectionner dans basique, Standard et bien références SKU. 


**Spécification de la passerelle de référence (SKU) à l’aide de PowerShell**


L’exemple suivant PowerShell spécifie la `-GatewaySku` comme *Standard*.

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard `
    -GatewayType Vpn -VpnType RouteBased

**Modification d’une passerelle de référence (SKU)**

Si vous voulez mettre à niveau votre passerelle de référence (SKU) vers une référence SKU plus puissante (Basic Standard à bien) vous pouvez utiliser la `Resize-AzureRmVirtualNetworkGateway` applet de commande PowerShell. Vous pouvez également mettre à niveau la passerelle taille de référence (SKU) à l’aide de cette applet de commande.

L’exemple de PowerShell suivant montre une passerelle de référence (SKU) redimensionnée à bien.

    $gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
    Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance

### <a name="estimated-aggregate-throughput-by-gateway-sku-and-type"></a>Estimée type et débit agrégation par la passerelle de référence (SKU)

Le tableau suivant montre les types de passerelle et le débit agrégation estimé. Ce tableau s’applique au Gestionnaire de ressources et les modèles de déploiement classique.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)] 


## <a name="connectiontype"></a>Types de connexion

Dans le modèle de déploiement Gestionnaire de ressources, chaque configuration nécessite un type de connexion de passerelle réseau virtuel spécifique. Valeurs de la session PowerShell Gestionnaire de ressources disponibles pour `-ConnectionType` sont :

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient

Dans l’exemple suivant PowerShell, nous créer une connexion S2S qui nécessite le type de connexion *IPsec*.

    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
    -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
    -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## <a name="vpntype"></a>Types de VPN

Lorsque vous créez la passerelle réseau virtuel pour une configuration de passerelle VPN, vous devez spécifier un type de VPN. Le type VPN que vous choisissez dépend de la topologie de connexion que vous voulez créer. Par exemple, une connexion P2S nécessite un type de RouteBased VPN. Un type de VPN peut également dépendre du matériel que vous souhaitez utiliser. Configurations S2S nécessitent un périphérique VPN. Certains appareils VPN prend uniquement en charge un certain type VPN.

Le type VPN que vous sélectionnez doit satisfaire toutes les conditions de connexion pour la solution que vous voulez créer. Par exemple, si vous voulez créer une connexion de passerelle S2S VPN et une connexion de passerelle VPN P2S pour le même réseau virtuel, vous utiliseriez type VPN *RouteBased* car P2S nécessite un type de RouteBased VPN. Vous devez également vérifier que votre appareil VPN pris en charge une connexion RouteBased VPN. 

Après avoir créé une passerelle réseau virtuel, vous ne pouvez pas modifier le type VPN. Vous devez supprimer la passerelle réseau virtuel et créer un nouveau. Il existe deux types VPN :

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]


L’exemple suivant PowerShell spécifie la `-VpnType` en tant que *RouteBased*. Lorsque vous créez une passerelle, assurez-vous que la VpnType - est correcte pour votre configuration. 

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig `
    -GatewayType Vpn -VpnType RouteBased

##  <a name="requirements"></a>Configuration requise de passerelle

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)] 


## <a name="gwsub"></a>Sous-réseau passerelle

Pour configurer une passerelle réseau virtuel, vous devez tout d’abord créer un sous-réseau passerelle pour votre VNet. Le sous-réseau passerelle doit être nommé *GatewaySubnet* fonctionne correctement. Ce nom permet Azure savoir que ce sous-réseau doit être utilisé pour la passerelle.

La taille minimale de votre sous-réseau passerelle dépend entièrement de la configuration que vous souhaitez créer. Bien qu’il soit possible de créer un sous-réseau passerelle aussi petite que /29, nous vous recommandons de créer un sous-réseau passerelle de /28 ou plus grand (/ 28, /27, /26, etc..). 

Création d’une plus grande taille de passerelle empêche l’exécution contre les limites de taille de passerelle. Par exemple, il est possible que créées une passerelle réseau virtuel avec une taille de sous-réseau passerelle /29 d’une connexion S2S. Vous souhaitez maintenant configurer un S2S/ExpressRoute coexister configuration. Cette configuration nécessite une taille minimale de passerelle sous-réseau /28. Pour créer votre configuration, vous devez modifier le sous-réseau passerelle pour s’adapter à la configuration minimale requise pour la connexion, ce qui correspond à /28.

Le Gestionnaire de ressources PowerShell suivant montre un sous-réseau passerelle nommé GatewaySubnet. Vous pouvez voir que la notation CIDR spécifie un /27, qui permet de suffisamment d’adresses IP pour la plupart des configurations qui existent actuellement.

    Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)] 


## <a name="lng"></a>Passerelles réseau local

Lorsque vous créez une configuration de passerelle VPN, la passerelle réseau local représente souvent votre emplacement local. Dans le modèle de déploiement classique, la passerelle réseau local était appelée un Site Local. 

Vous nommez la passerelle réseau local, l’adresse IP de l’appareil VPN en local et spécifiez les préfixes d’adresses qui sont trouvent sur l’emplacement local. Azure examine les préfixes d’adresse de destination pour le trafic réseau, consulte la configuration que vous avez spécifiée pour la passerelle de votre réseau local et achemine les paquets en conséquence. Vous spécifiez également passerelles réseau local pour les configurations VNet-VNet qui utilisent une connexion de passerelle VPN.

L’exemple de PowerShell suivant crée une nouvelle passerelle réseau local :

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Parfois, vous devez modifier les paramètres de la passerelle réseau local. Par exemple, lorsque vous ajoutez ou modifiez la plage d’adresses, ou si l’adresse IP du périphérique VPN change. Pour un VNet classique, vous pouvez modifier ces paramètres dans le portail classique dans la page réseaux locaux. Pour le Gestionnaire de ressources, voir [Modifier les paramètres passerelle réseau local à l’aide de PowerShell](vpn-gateway-modify-local-network-gateway.md).

## <a name="resources"></a>Applets de commande API REST et PowerShell

Pour les ressources techniques supplémentaires en matière de syntaxe spécifique lors de l’utilisation des API REST et les applets de commande PowerShell pour les configurations de passerelle VPN, consultez les pages suivantes :

|**Classique** | **Gestionnaire de ressources**|
|-----|----|
|[PowerShell](https://msdn.microsoft.com/library/mt270335.aspx)|[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx)|
|[API REST](https://msdn.microsoft.com/library/jj154113.aspx)|[API REST](https://msdn.microsoft.com/library/mt163859.aspx)|


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les configurations de connexion disponibles, voir [à propos VPN passerelle](vpn-gateway-about-vpngateways.md) . 







 
