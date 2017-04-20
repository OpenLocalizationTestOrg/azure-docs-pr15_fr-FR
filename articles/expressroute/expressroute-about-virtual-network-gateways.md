<properties 
   pageTitle="À propos des passerelles de réseau virtuel ExpressRoute | Microsoft Azure"
   description="Découvrez les passerelles réseau virtuel pour ExpressRoute."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager, azure-service-management"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/03/2016"
   ms.author="cherylmc" />

# <a name="about-virtual-network-gateways-for-expressroute"></a>À propos des passerelles réseau virtuel pour ExpressRoute


Une passerelle réseau virtuel est utilisée pour acheminer le trafic réseau entre les réseaux virtuels Azure et emplacements local. Lorsque vous configurez une connexion ExpressRoute, vous devez créer et configurer une passerelle réseau virtuel et une connexion de passerelle réseau virtuel.

Lorsque vous créez une passerelle réseau virtuel, vous spécifiez plusieurs paramètres. Si la passerelle doit être utilisée pour le trafic ExpressRoute ou VPN de Site à l’un des paramètres requis spécifie. Dans le modèle de déploiement Gestionnaire de ressources, le paramètre est «-GatewayType ».

Lorsque le trafic réseau est envoyé sur une connexion privée dédiée, vous utilisez le type de passerelle « ExpressRoute ». Il est également appelé une passerelle ExpressRoute. Lorsque le trafic réseau est envoyé chiffré sur Internet, vous utilisez le type de passerelle « Vpn ». Ceci est appelé une passerelle VPN. Sites, Point-à-Site et VNet-VNet connexions utilisent une passerelle VPN. 

Chaque réseau virtuel peut avoir qu’une seule passerelle réseau virtuel par type de passerelle. Par exemple, vous pouvez avoir un réseau virtuel la passerelle qui utilise - GatewayType Vpn et celle qui utilise - GatewayType ExpressRoute. Cet article se concentre sur la passerelle de réseau virtuel ExpressRoute.

## <a name="gwsku"></a>Références SKU passerelle

[AZURE.INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Si vous souhaitez mettre à niveau votre passerelle vers une passerelle plus puissante de référence (SKU), dans la plupart des cas, vous pouvez utiliser l’applet de commande PowerShell 'Redimensionnement-AzureRmVirtualNetworkGateway'. Cela ne fonctionne pas pour les mises à niveau Standard et bien références SKU. Toutefois, pour mettre à niveau vers le UltraPerformance SKU, vous devez recréer la passerelle.

###  <a name="aggthroughput"></a>Estimée débit agrégation par la passerelle de référence (SKU)


Le tableau suivant montre les types de passerelle et le débit agrégation estimé. Ce tableau s’applique au Gestionnaire de ressources et les modèles de déploiement classique.

[AZURE.INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)] 


## <a name="resources"></a>Applets de commande API REST et PowerShell

Pour les ressources techniques supplémentaires en matière de syntaxe spécifique lors de l’utilisation des API REST et les applets de commande PowerShell pour les configurations de la passerelle réseau virtuel, consultez les pages suivantes :

|**Classique** | **Gestionnaire de ressources**|
|-----|----|
|[PowerShell](https://msdn.microsoft.com/library/mt270335.aspx)|[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx)|
|[API REST](https://msdn.microsoft.com/library/jj154113.aspx)|[API REST](https://msdn.microsoft.com/library/mt163859.aspx)|


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les configurations de connexion disponibles, voir [Vue d’ensemble ExpressRoute](expressroute-introduction.md) . 







 
