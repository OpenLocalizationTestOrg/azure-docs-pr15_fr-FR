<properties
   pageTitle="Vue d’ensemble des configurations hautement disponibles avec les passerelles VPN Azure | Microsoft Azure"
   description="Cet article fournit une vue d’ensemble des options de configuration hautement disponible à l’aide de passerelles VPN Azure."
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
   ms.date="09/24/2016"
   ms.author="yushwang"/>

# <a name="highly-available-cross-premises-and-vnet-to-vnet-connectivity"></a>Disponibilité croisée locaux et connectivité VNet-VNet

Cet article fournit une vue d’ensemble des options de configuration hautement disponible pour votre croisée locaux et connectivité VNet-VNet à l’aide de passerelles VPN Azure.

## <a name = "activestandby"></a>À propos de redondance de passerelle VPN Azure

Chaque passerelle VPN Azure se compose de deux instances dans une configuration active-de réserve. Une maintenance planifiée ou non planifiée interruption qui se trouve l’instance active, l’instance de secours serait prendre le contrôle (basculement) automatiquement et reprendre la S2S VPN ou une connexion VNet-VNet. Le commutateur sur provoquera une brève interruption. Pour la maintenance planifiée, la connectivité doit être restaurée au sein de 10 à 15 secondes. Pour des problèmes non planifiées, la récupération de connexion sera plus longue, environ une minute à 1 et demi minutes dans le pire des cas. Les connexions de client VPN P2S à la passerelle, les connexions P2S seront déconnectées et les utilisateurs doivent se reconnecter à partir des ordinateurs client.

![Mise en veille actif](./media/vpn-gateway-highlyavailable/active-standby.png)

## <a name="highly-available-cross-premises-connectivity"></a>Connectivité entre locaux hautement disponible

Pour fournir une plus grande disponibilité pour vos connexions croisée local, un certain nombre d’options sont disponibles :

- Plusieurs périphériques VPN en local
- Passerelle de VPN Azure actif
- Combinaison des deux

### <a name = "activeactiveonprem"></a>Plusieurs périphériques VPN en local

Vous pouvez utiliser plusieurs appareils VPN à partir de votre réseau local pour se connecter à votre passerelle VPN Azure, comme le montre l’illustration suivante :

![Plusieurs locaux VPN](./media/vpn-gateway-highlyavailable/multiple-onprem-vpns.png)

Cette configuration offre plusieurs tunnel active à partir de la même passerelle VPN Azure à vos périphériques locaux au même emplacement. Il existe certaines exigences et les contraintes :

1. Vous devez créer plusieurs connexions VPN S2S à partir de vos appareils VPN vers Azure. Lorsque vous vous connectez plusieurs appareils VPN à partir du même réseau local vers Azure, vous devez créer une passerelle réseau local pour chaque périphérique VPN et une connexion à partir de la passerelle VPN Azure de la passerelle réseau local.

2. Les passerelles réseau local correspondant à vos appareils VPN doivent avoir des adresses IP publiques uniques dans la propriété « GatewayIpAddress ».

3. BGP est nécessaire pour cette configuration. Chaque passerelle réseau local représentant un périphérique VPN doit avoir une BGP homologue adresse IP spécifiée dans la propriété « BgpPeerIpAddress ».

4. Le champ de propriété AddressPrefix dans chaque passerelle réseau local doit se chevauchent pas. Vous devez indiquer le « BgpPeerIpAddress » dans /32 format CIDR dans le champ AddressPrefix, par exemple, 10.200.200.254/32.

5. Vous devez utiliser BGP pour annoncer les mêmes préfixes du même local préfixes réseau à votre passerelle VPN Azure et le trafic est transféré via ces tunnel simultanément.

6. Chaque connexion est imputée sur le nombre maximal de tunnel pour votre passerelle VPN Azure, 10 pour Basic et références SKU Standard et 30 pour HighPerformance SKU. 

Dans cette configuration, la passerelle VPN Azure est toujours en mode mise en veille actif, afin que le même comportement de basculement et une brève interruption seront produit toujours comme décrit [ci-dessus](#activestandby). Mais ce programme d’installation protection contre les échecs ou interruptions sur votre réseau local et les appareils VPN.
 
### <a name="active-active-azure-vpn-gateway"></a>Passerelle de VPN Azure actif

Vous pouvez désormais créer une passerelle VPN Azure dans une configuration actif, où les deux instances de passerelle ordinateurs virtuels va établir tunnel VPN S2S sur votre appareil VPN en local, comme le montre l’illustration suivante :

![Actif](./media/vpn-gateway-highlyavailable/active-active.png)

Dans cette configuration, chaque instance Azure passerelle aura une adresse IP publique unique, et chacune va établir un tunnel IPsec/IKE S2S VPN sur votre appareil VPN local spécifié dans votre passerelle réseau local et votre connexion. Notez que les deux tunnel VPN est en fait partie de la même connexion. Vous avez toujours besoin configurer votre périphérique VPN en local pour accepter ou établir deux tunnel S2S VPN pour ces deux Azure VPN passerelle des adresses IP publiques.

Étant donné que les instances de passerelle Azure sont en configuration actif, le trafic de votre réseau virtuel Azure à votre réseau local sera routé via les deux tunnel simultanément, même si votre appareil VPN local peut favoriser un seul tunnel au-dessus de l’autre. Remarque Bien que le même flux TCP ou UDP parcourt toujours la même tunnel ou chemin d’accès, à moins d’avoir un événement de maintenance sur une des instances.

Lorsqu’une maintenance planifiée ou un événement planifié se trouve instance une passerelle, le tunnel IPsec à partir de cette instance sur votre appareil VPN locale sera déconnecté. Les gammes correspondantes sur vos appareils VPN doivent être supprimés ou retirées automatiquement afin que le trafic bascule sur au autres tunnel IPsec actif. Sur le côté Azure, le commutateur sur sera-t-elle automatique de l’instance affecté à l’instance active.

### <a name="dual-redundancy-active-active-vpn-gateways-for-both-azure-and-on-premises-networks"></a>Double-redondance : les passerelles VPN actif pour les réseaux Azure et en local

L’option la plus fiable consiste à combiner les passerelles actif sur votre réseau et Azure, comme le montre l’illustration ci-dessous.

![Redondance double](./media/vpn-gateway-highlyavailable/dual-redundancy.png)

Vous créez et configurez la passerelle VPN Azure dans une configuration actif et créez deux passerelles réseau local et deux connexions pour votre deux locaux périphériques VPN comme décrit ci-dessus. Le résultat est une connectivité maillage complet de 4 tunnel IPsec entre votre réseau virtuel Azure et de votre réseau local.

Toutes les passerelles et tunnel étant active à partir du bord Azure, le trafic est répartie parmi tous les 4 tunnel simultanément, bien que chaque flux TCP ou UDP va suivre à nouveau le même tunnel ou un chemin d’accès à partir du bord Azure. Bien que par le trafic de la diffusion, vous pouvez voir légèrement de meilleurs résultats sur le tunnel IPsec, l’objectif principal de cette configuration est de disponibilité. Et en raison de la nature statistique de répandre, il est difficile à fournir que la mesure sur le fonctionnement de différents routière application affecte le débit global.

Cette topologie nécessite deux passerelles réseau local et deux connexions pour prendre en charge la paire de périphériques VPN en local et BGP est requis pour autoriser les deux connexions au même réseau local. Ces exigences sont les mêmes que l' [ci-dessus](#activeactiveonprem). 

## <a name="highly-available-vnet-to-vnet-connectivity-through-azure-vpn-gateways"></a>Connectivité à forte disponibilité VNet-VNet via des passerelles VPN Azure

La même configuration actif pouvez également appliquer des connexions Azure VNet à VNet. Vous pouvez créer des passerelles VPN actif pour les deux réseaux virtuels et les connecter ensemble au formulaire la même connectivité maillage complet de 4 tunnel entre les deux VNets, comme le montre l’illustration ci-dessous :

![VNet-VNet](./media/vpn-gateway-highlyavailable/vnet-to-vnet.png)

Ainsi, il existe toujours une paire de tunnel entre les deux réseaux virtuels pour tous les événements maintenance planifiée, la disponibilité du indépendante. Même si la même topologie de manière croisée locaux connectivité nécessite deux connexions, la topologie de VNet-VNet ci-dessus auront besoin qu’une connexion pour chaque passerelle. En outre, BGP est facultative, sauf si voies acheminer via la connexion VNet-VNet est requis.


## <a name="next-steps"></a>Étapes suivantes

Voir [Configuration des passerelles VPN actif pour les connexions de VNet-VNet et Cross locaux](vpn-gateway-activeactive-rm-powershell.md) pour obtenir la procédure pour configurer les locaux entre actif et connexions VNet-VNet.
