<properties
   pageTitle="Configurer une passerelle VNet pour ExpressRoute à l’aide de PowerShell | Microsoft Azure"
   description="Configurer une passerelle VNet pour un déploiement classique VNet à l’aide de PowerShell pour une configuration ExpressRoute du modèle."
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/03/2016"
   ms.author="charwen"/>

# <a name="configure-a-virtual-network-gateway-for-expressroute-using-the-classic-deployment-model-and-powershell"></a>Configurer une passerelle réseau virtuel pour ExpressRoute l’aide du modèle de déploiement classique et PowerShell

> [AZURE.SELECTOR]
- [PowerShell - Gestionnaire de ressources](expressroute-howto-add-gateway-resource-manager.md)
- [PowerShell - classique](expressroute-howto-add-gateway-classic.md)

Cet article vous guidera tout au long de la procédure pour ajouter, redimensionner et supprimer une passerelle réseau virtuel (VNet) pour un VNet existant. Les étapes pour cette configuration sont spécialement pour VNets qui ont été créés à l’aide du **modèle de déploiement classique** et qui sera être utilisée dans une configuration ExpressRoute. 

**À propos des modèles de déploiement d’Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="before-beginning"></a>Avant de commencer

Vérifiez que vous avez installé les applets de commande PowerShell Azure nécessaire pour cette configuration (1.0.2 ou version ultérieure). Si vous n’avez pas installé les applets de commande, vous devez faire avant de commencer les étapes de configuration. Pour plus d’informations sur l’installation d’Azure PowerShell, voir [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).


[AZURE.INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

    
## <a name="next-steps"></a>Étapes suivantes

Après avoir créé la passerelle VNet, vous pouvez lier vos VNet à un circuit ExpressRoute. [Lien un réseau virtuel pour un circuit ExpressRoute](expressroute-howto-linkvnet-classic.md)apparaît.
