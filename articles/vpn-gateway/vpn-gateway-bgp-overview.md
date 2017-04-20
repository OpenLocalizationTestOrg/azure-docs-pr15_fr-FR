<properties
   pageTitle="Vue d’ensemble des BGP avec les passerelles VPN Azure | Microsoft Azure"
   description="Cet article fournit une vue d’ensemble de BGP avec des passerelles VPN Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags=""/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/16/2016"
   ms.author="yushwang"/>

# <a name="overview-of-bgp-with-azure-vpn-gateways"></a>Vue d’ensemble des BGP avec les passerelles VPN Azure

Cet article fournit une vue d’ensemble du support BGP (bordure passerelle Protocol) dans des passerelles VPN Azure.

## <a name="about-bgp"></a>À propos de BGP

BGP est le protocole de routage standard fréquemment utilisé dans Internet pour échanger des informations de routage et à l’accessibilité entre deux ou plusieurs réseaux. Dans le contexte des réseaux virtuels Azure, BGP permet les passerelles VPN Azure et vos périphériques VPN en local, appelés BGP homologues ou voisin, pour échanger des « achemine » qui informe les deux passerelles sur la disponibilité et l’accessibilité de ces préfixes traitée les passerelles ou routeurs impliqués. BGP pouvez également activer voies routage entre plusieurs réseaux par propagation itinéraires qu'une passerelle BGP apprend à partir d’un homologue BGP à tous les autres homologues BGP.
 
### <a name="why-use-bgp"></a>Pourquoi utiliser BGP ?

BGP est une fonctionnalité facultative, que vous pouvez utiliser avec les passerelles VPN basée sur un itinéraire Azure. Vous devez également vous assurer que vos appareils VPN local prend en charge BGP avant d’activer la fonctionnalité. Vous pouvez continuer à utiliser des passerelles VPN Azure et vos appareils VPN local sans BGP. Il est l’équivalent de l’utilisation des itinéraires statiques (sans BGP) *par rapport* à l’aide de routage dynamique avec BGP entre vos réseaux et Azure.

Il existe plusieurs avantages et des nouvelles fonctionnalités avec BGP :

#### <a name="support-automatic-and-flexible-prefix-updates"></a>Prise en charge des mises à jour automatiques et flexible préfixe

Avec BGP, il vous suffit de déclarer un préfixe minimal à un homologue BGP spécifique sur le tunnel IPsec S2S VPN. Il peut être aussi petite qu’un préfixe host (/ 32) de l’adresse BGP homologue IP de votre appareil VPN en local. Vous pouvez contrôler qui locaux préfixes réseau que vous voulez publier aux Azure pour permettre à votre réseau virtuel Azure pour accéder à.
    
Vous pouvez également publier une plus grande préfixes qui peuvent inclure certains de vos préfixes d’adresses VNet, par exemple un grand privé IP espace d’adressage (par exemple, 10.0.0.0/8). Remarque Bien que les préfixes ne peut pas être identiques à l’une de vos préfixes VNet. Ces itinéraires identiques à votre préfixes VNet seront rejetées.

>[AZURE.IMPORTANT] Pour l’instant, publicité l’itinéraire par défaut (0.0.0.0/0) vers des passerelles VPN Azure est bloqué. Mise à jour complémentaire est fournie une fois que cette fonctionnalité est activée.

#### <a name="support-multiple-tunnels-between-a-vnet-and-an-on-premises-site-with-automatic-failover-based-on-bgp"></a>Prend en charge plusieurs tunnel entre un VNet et un site en local avec basculement automatique basé sur BGP

Vous pouvez établir plusieurs connexions entre votre VNet Azure et vos appareils VPN local au même emplacement. Cette fonctionnalité fournit plusieurs tunnel (chemins d’accès) entre les deux réseaux dans une configuration actif. Si un du tunnel est déconnecté, les gammes correspondantes seront supprimées via BGP et le trafic se déplace automatiquement à la tunnel restant.
    
Le diagramme suivant montre un exemple simple de cette configuration hautement disponible :
    
![Plusieurs chemins actifs](./media/vpn-gateway-bgp-overview/multiple-active-tunnels.png)

#### <a name="support-transit-routing-between-your-on-premises-networks-and-multiple-azure-vnets"></a>Prise en charge lors des transferts routage entre vos réseaux locaux et plusieurs VNets Azure

BGP permet plusieurs passerelles pour en savoir plus et propager les préfixes de réseaux différents, qu’ils sont directement ou indirectement connectés. Cela peut permettre de voies routage avec des passerelles VPN Azure entre vos sites en local ou entre plusieurs réseaux virtuels Azure.
    
Le diagramme suivant montre un exemple d’une topologie de sauts multiples avec plusieurs chemins d’accès qui peuvent transit le trafic entre les deux réseaux locaux via des passerelles VPN Azure dans le Microsoft Networks :

![Voies sauts multiples](./media/vpn-gateway-bgp-overview/full-mesh-transit.png)

## <a name="bgp-faqs"></a>Forum aux questions BGP


[AZURE.INCLUDE [vpn-gateway-bgp-faq-include](../../includes/vpn-gateway-bpg-faq-include.md)] 




## <a name="next-steps"></a>Étapes suivantes

Voir [prise en main BGP sur passerelles VPN Azure](./vpn-gateway-bgp-resource-manager-ps.md) pour obtenir la procédure pour configurer BGP pour votre croisée locaux et connexions VNet-VNet.

