Vous devez créer un VNet et un sous-réseau passerelle en premier lieu, avant d’effectuer les tâches suivantes. Voir l’article [configurer un réseau virtuel à l’aide du portail classique](../articles/expressroute/expressroute-howto-vnet-portal-classic.md) pour plus d’informations.   

## <a name="add-a-gateway"></a>Ajouter une passerelle

Utilisez la commande suivante pour créer une passerelle. Veillez à remplacer toutes les valeurs de votre propre.

    New-AzureVirtualNetworkGateway -VNetName "MyAzureVNET" -GatewayName "ERGateway" -GatewayType Dedicated -GatewaySKU  Standard

## <a name="verify-the-gateway-was-created"></a>Vérifier que la passerelle a été créée

Utilisez la commande suivante pour vérifier que la passerelle a été créée. Cette commande récupère également l’ID de passerelle, dont vous aurez besoin pour d’autres opérations.

    Get-AzureVirtualNetworkGateway

## <a name="resize-a-gateway"></a>Redimensionner une passerelle

Il existe un certain nombre de [Passerelle références SKU](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Vous pouvez utiliser la commande suivante pour modifier la passerelle de référence (SKU) à tout moment.

>[AZURE.IMPORTANT] Cette commande ne fonctionne pas pour la passerelle UltraPerformance. Pour modifier votre passerelle vers une passerelle UltraPerformance, supprimez d’abord la passerelle ExpressRoute existante, puis créez une nouvelle passerelle UltraPerformance. Pour mettre à niveau votre passerelle à partir d’une passerelle UltraPerformance, supprimez d’abord la passerelle UltraPerformance, puis créez une nouvelle passerelle. 

    Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance

## <a name="remove-a-gateway"></a>Supprimer une passerelle

Utilisez la commande suivante pour supprimer une passerelle

    Remove-AzureVirtualNetworkGateway -GatewayId <Gateway ID>