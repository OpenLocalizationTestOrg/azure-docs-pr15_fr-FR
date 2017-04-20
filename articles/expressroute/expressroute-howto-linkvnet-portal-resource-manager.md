<properties
   pageTitle="Lier un réseau virtuel pour un circuit ExpressRoute à l’aide du modèle de déploiement du Gestionnaire de ressources et le portail Azure | Microsoft Azure"
   description="Ce document fournit une vue d’ensemble de la façon d’établir une liaison réseaux virtuels (VNets) vers ExpressRoute circuits."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="cherylmc" />

# <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Lier un réseau virtuel à un circuit ExpressRoute

> [AZURE.SELECTOR]
- [Portail Azure - Gestionnaire de ressources](expressroute-howto-linkvnet-portal-resource-manager.md)
- [PowerShell - Gestionnaire de ressources](expressroute-howto-linkvnet-arm.md)
- [PowerShell - classique](expressroute-howto-linkvnet-classic.md)



Cet article vous aidera à lier réseaux virtuels (VNets) aux circuits Azure ExpressRoute à l’aide du portail Azure et le modèle de déploiement du Gestionnaire de ressources. Réseaux virtuels peuvent être dans le même abonnement ou qu’ils peuvent faire partie d’un autre abonnement.


**À propos des modèles de déploiement d’Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Conditions préalables de configuration

- Vérifiez que vous avez examiné les [conditions préalables](expressroute-prerequisites.md), [Configuration requise pour le routage](expressroute-routing.md)et [flux de travail](expressroute-workflows.md) avant de commencer la configuration.
- Vous devez avoir un circuit ExpressRoute actif.
    - Suivez les instructions pour [créer un circuit ExpressRoute](expressroute-howto-circuit-arm.md) et avoir le circuit activé par votre fournisseur de connectivité.

    - Assurez-vous d’avoir Azure peering privé configuré pour votre circuit. Voir l’article [configurer Routage](expressroute-howto-routing-portal-resource-manager.md) pour obtenir des instructions routage.

    - Vérifiez que Azure peering privé est configuré et la peering BGP entre votre réseau et Microsoft est l’afin que vous pouvez activer la connectivité de bout en bout.

    - Assurez-vous d’avoir un réseau virtuel et une passerelle réseau virtuel créé et entièrement mis en service. Suivez les instructions pour créer une [passerelle VPN](../articles/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) (suivre uniquement étapes 1 à 5).

Vous pouvez lier jusqu'à 10 réseaux virtuels à un circuit ExpressRoute standard. Tous les réseaux virtuels doivent être placé dans la même région géopolitique lorsque vous utilisez un circuit ExpressRoute standard. Vous pouvez lier un réseaux virtuel en dehors de la région géopolitique du circuit ExpressRoute, ou connectez un grand nombre de réseaux virtuels votre circuit ExpressRoute si vous avez activé le module complémentaire premium ExpressRoute. Consultez le [Forum aux questions](expressroute-faqs.md) pour plus d’informations sur le module complémentaire premium.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Connectez un réseau virtuel dans le même abonnement à un circuit


### <a name="to-create-a-connection"></a>Pour créer une connexion

1. Assurez-vous que votre circuit ExpressRoute et Azure peering privé ont été correctement configurés. Suivez les instructions de [créer un circuit ExpressRoute](expressroute-howto-circuit-arm.md) et [configurer le routage](expressroute-howto-routing-arm.md). Votre circuit ExpressRoute doit ressembler à l’image suivante.

    ![Capture d’écran de circuit ExpressRoute](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png)

    >[AZURE.NOTE] Informations de configuration BGP s’affichera pas si le fournisseur layer 3 configuré votre peerings. Si votre circuit est dans un état généré, vous devriez pouvoir créer des connexions.

2. Vous pouvez démarrer maintenant une connexion entre votre passerelle réseau virtuel vers votre circuit ExpressRoute de mise en service. Cliquez sur **la connexion** > **Ajouter** pour ouvrir la carte de **Ajouter une connexion** , puis configurer les valeurs. Consultez l’exemple de référence suivant.


    ![Ajouter la capture d’écran de connexion](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png)  


3. Une fois que votre connexion a été correctement configurée, votre objet de connexion affiche les informations pour la connexion.

    ![Capture d’écran de connexion objet](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)


### <a name="to-delete-a-connection"></a>Pour supprimer une connexion

Vous pouvez supprimer une connexion en sélectionnant l’icône **Supprimer** dans la carte pour votre connexion.

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Connectez un réseau virtuel dans un autre abonnement à un circuit

Pour le moment, vous ne peut pas se connecter réseaux virtuels abonnements à l’aide du portail Azure. Toutefois, vous pouvez utiliser PowerShell pour effectuer cette action. Consultez l’article [PowerShell](expressroute-howto-linkvnet-arm.md) pour plus d’informations.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur ExpressRoute, consultez le [Forum aux questions sur ExpressRoute](expressroute-faqs.md).
