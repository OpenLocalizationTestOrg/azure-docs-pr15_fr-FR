<properties
   pageTitle="Vue d’ensemble du réseau virtuel Azure (VNet)"
   description="En savoir plus sur les réseaux virtuels (VNets) dans Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="virtual-network-overview"></a>Vue d’ensemble du réseau virtuel

Un réseau virtuel Azure (VNet) est une représentation de votre réseau dans le cloud.  Il s’agit d’un isolement logique du cloud Azure dédié à votre abonnement. Vous pouvez contrôler totalement les paramètres blocs d’adresses IP, DNS, stratégies de sécurité et tables de routage au sein de ce réseau. Vous pouvez également segmenter votre VNet en sous-réseaux et lancer machines virtuelles Azure IaaS (machines virtuelles) et/ou [les services en nuage (instances de rôle PaaS)](../cloud-services/cloud-services-choose-me.md). En outre, vous pouvez vous connecter au réseau virtuel à votre réseau local à l’aide d’une des [options de connectivité](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site) disponibles dans Azure. En résumé, vous pouvez développer votre réseau vers Azure, un contrôle total sur bloc d’adresse IP avec l’avantage d’échelle de l’entreprise Qu'azure fournit.

Pour mieux comprendre VNets, consultez l’illustration ci-dessous, qui montre un simplifiée réseau local.

![Réseau local](./media/virtual-networks-overview/figure01.png)

La figure ci-dessus illustre un réseau local connecté à Internet public via un routeur. Vous pouvez également afficher un pare-feu entre le routeur et une DMZ héberger un serveur DNS et une batterie de serveurs web. La batterie de serveurs web équilibrée de charge est à l’aide d’équilibrage de charge matérielle qui est exposé à Internet et consomme des ressources à partir du sous-réseau interne. Le sous-réseau interne est séparé par un autre pare-feu et héberge les serveurs de contrôleur de domaine Active Directory, les serveurs de base de données et les serveurs d’applications à partir de la zone DMZ.

Le même réseau peut être hébergé dans Azure comme indiqué dans l’illustration ci-dessous.

![Réseau virtuel Azure](./media/virtual-networks-overview/figure02.png)

Notez comment l’infrastructure Azure joue le rôle du routeur, ce qui permet l’accès à partir de votre VNet à l’Internet public sans qu’il soit nécessaire de n’importe quelle configuration. Pare-feux peut être remplacés par les groupes de sécurité réseau (NSGs) appliquée à chaque sous-réseau individuel. Et programmes d’équilibrage de charge physique sont remplacées par équilibrage de charge des internes et qui fait face à internet dans Azure.

>[AZURE.NOTE] Il existe deux modes de déploiement dans Azure : classique (également appelé gestion des services) et Azure Resource Manager (ARM). VNets classique pourrait ajouté à un groupe affinité ou créé comme un VNet régionaux. Si vous avez un VNet dans un groupe affinité, il est recommandé pour [effectuer une migration à un VNet régionaux](virtual-networks-migrate-to-regional-vnet.md).

## <a name="virtual-network-benefits"></a>Avantages du réseau virtuel

- **Isolement**. VNets sont complètement isolées les unes des autres. Vous permet de créer des réseaux disjoints de développement, de test et de production qui utilisent le même bloc d’adresse CIDR.

- **Accès à l’Internet public**. Machines virtuelles IaaS et PaaS toutes les instances de rôles dans un VNet peuvent accéder à l’Internet public par défaut. Vous pouvez contrôler l’accès à l’aide de groupes de sécurité réseau (NSGs).

- **Accès aux machines virtuelles au sein de la VNet**. Instances de rôle PaaS et machines virtuelles IaaS peuvent être lancé sur le même réseau virtuel, et ils peuvent se connecter à l’aide des adresses IP privées même s’ils sont dans différents sous-réseaux sans avoir besoin de configurer une passerelle ou utiliser des adresses IP publiques.

- **Résolution de noms**. Azure fournit la résolution de noms interne pour IaaS machines virtuelles et des instances de rôle PaaS déployées dans votre VNet. Vous pouvez également déployer vos propres serveurs DNS et configurer le VNet pour les utiliser.

- **Sécurité**. Le trafic entrant et sortant les machines virtuelles et les instances de rôle PaaS dans un VNet peut être contrôlé à l’aide de groupes de sécurité réseau.

- **Connectivité**. VNets pouvant être connectés entre eux à l’aide de passerelles réseau ou VNet peering. VNets pouvant être connectés aux centres de données locale par le biais des réseaux VPN site à ou Azure ExpressRoute. Pour plus d’informations sur la connectivité VPN de site à, visitez [passerelles sur VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site). Pour en savoir plus sur ExpressRoute, consultez [présentation technique ExpressRoute](../expressroute/expressroute-introduction.md). Pour en savoir plus sur VNet peering, visitez [VNet peering](virtual-network-peering-overview.md).

    >[AZURE.NOTE] Vérifiez que vous créez un VNet avant de déployer des instances de rôle PaaS IaaS machines virtuelles à votre environnement Azure. PROCESSEUR machines virtuelles nécessitent une VNet, et si vous ne spécifiez pas une VNet existant, Azure crée une valeur par défaut VNet qui peut-être avoir un conflit de bloc d’adresse CIDR avec votre réseau local. Ce qui empêche de vous connecter votre VNet à votre réseau local.

## <a name="subnets"></a>Sous-réseaux

Constitue une plage d’adresses IP dans le VNet, vous pouvez diviser une VNet en plusieurs sous réseaux pour l’organisation et sécurité. Machines virtuelles et des instances de rôle PaaS déployés sur les sous-réseaux (ou plusieurs) au sein d’un VNet peuvent communiquer avec eux sans aucune configuration supplémentaire. Vous pouvez également configurer des tables de routage et NSGs à un sous-réseau.

## <a name="ip-addresses"></a>Adresses IP


Il existe deux types d’adresses IP affectées à des ressources dans Azure : *publiques* et *privées*. Adresses IP publiques autoriser les ressources Azure à communiquer avec Internet et d’autres services au public Azure comme le [Cache Redis Azure](https://azure.microsoft.com/services/cache/), [Azure événement Hubs](https://azure.microsoft.com/documentation/services/event-hubs/). Adresses IP privé permet la communication entre les ressources dans un réseau virtuel, ainsi que ceux connectés via un réseau privé virtuel, sans l’aide d’adresses IP pouvant être routés Internet.

Pour plus d’informations sur les adresses IP dans Azure, visitez [adresses IP dans le réseau virtuel](virtual-network-ip-addresses-overview-arm.md)

## <a name="azure-load-balancers"></a>Programmes d’équilibrage de charge Azure

Machines virtuelles et services de cloud dans un réseau virtuelles peuvent être exposées à Internet à l’aide des programmes d’équilibrage de charge Azure. Des applications métier qui sont internes accès peuvent uniquement être charge équilibrée à l’aide d’équilibrage de charge interne.

- **Équilibrage de charge externe**. Vous pouvez utiliser un programme d’équilibrage de charge externe pour augmenter la disponibilité des ordinateurs virtuels IaaS et PaaS instances de rôle accédé à partir de l’Internet public.

- **Équilibrage de charge interne**. Vous pouvez utiliser un programme d’équilibrage de charge interne pour augmenter la disponibilité des machines virtuelles IaaS instances de rôle PaaS accédés à partir d’autres services dans votre VNet.

Pour en savoir plus sur l’équilibrage de charge en Azure, consultez [Présentation de l’équilibrage de la charge](../load-balancer/load-balancer-overview.md).

## <a name="network-security-group-nsg"></a>Groupe de sécurité réseau (NSG)

Vous pouvez créer NSGs pour contrôler l’accès entrant et sortant à interfaces réseau (cartes réseau), machines virtuelles et les sous-réseaux. Chaque NSG contient une ou plusieurs règles spécifiant ou non le trafic soit approuvé ou refusé selon votre adresse IP source, port source, adresse IP de destination et port de destination. Pour en savoir plus sur NSGs, consultez [qu’est un groupe de sécurité réseau](virtual-networks-nsg.md).

## <a name="virtual-appliances"></a>Authentification multifacteur

Un appareil virtuel correspond à une autre machine virtuelle dans votre VNet qui exécute une fonction de matériel logiciels en se basant, notamment les pare-feu, optimisation WAN ou détection. Vous pouvez créer un itinéraire dans Azure pour acheminer le trafic de votre VNet via une application virtuelle à utiliser ses fonctions.

Par exemple, NSGs peut servir pour assurer la sécurité dans votre VNet. Toutefois, NSGs fournir couche 4 contrôle de liste d’accès aux paquets entrants et sortants. Si vous souhaitez utiliser un modèle de sécurité 7 couche, vous devez utiliser un pare-feu.

Authentification multifacteur dépendre de la [route et transfert IP définis par l’utilisateur](virtual-networks-udr-overview.md).

## <a name="limits"></a>Limites
Il existe des limites du nombre de réseaux virtuels autorisée dans un abonnement, pour plus d’informations, reportez-vous à la [mise en réseau Azure limites](../azure-subscription-service-limits.md#networking-limits) .

## <a name="pricing"></a>Tarifs
Il est sans frais supplémentaires pour l’utilisation de réseaux virtuels dans Azure. Les instances cluster lancés au sein de la Vnet seront facturés les taux standards comme décrit dans [Azure machine virtuelle tarifs](https://azure.microsoft.com/pricing/details/virtual-machines/). Les [Passerelles VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/) et [des adresses IP publiques] (https://azure.microsoft.com/pricing/details/ip-addresses/) utilisé dans le VNet seront également facturés taux standards.

## <a name="next-steps"></a>Étapes suivantes

- [Créer un VNet](virtual-networks-create-vnet-arm-pportal.md) et sous-réseaux.
- [Créer une machine virtuelle dans un VNet](../virtual-machines/virtual-machines-windows-hero-tutorial.md).
- En savoir plus sur [NSGs](virtual-networks-nsg.md).
- Découvrez les [itinéraires et transfert IP définis par l’utilisateur](virtual-networks-udr-overview.md).
