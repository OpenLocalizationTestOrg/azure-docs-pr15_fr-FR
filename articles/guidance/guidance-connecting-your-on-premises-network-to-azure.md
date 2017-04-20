<properties
   pageTitle="Connecter votre réseau local à Azure | Microsoft Azure"
   description="Explique et compare les différentes méthodes disponibles pour vous connecter aux services de cloud Microsoft tels que Azure, Office 365 et Dynamics CRM Online."
   services=""
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags=""/>
<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/25/2016"
   ms.author="jdial"/>
   
# <a name="connecting-your-on-premises-network-to-azure"></a>Connecter votre réseau local à Azure

Microsoft fournit plusieurs types de services de cloud. Pendant que vous pouvez vous connecter à tous les services sur l’Internet public, vous pouvez également connecter à certaines de ces services à l’aide d’un tunnel de réseau privé virtuel (VPN) via Internet ou via une connexion privée directe à Microsoft. Cet article vous aide à déterminer quelle option connectivité répondra le mieux à vos besoins basées sur les types de services de cloud Microsoft que vous consommer. La plupart des organisations utilisent plusieurs types de connexions décrites ci-dessous.

## <a name="connecting-over-the-public-internet"></a>Connexion via Internet public

Ce type de connexion permet d’accéder aux services cloud Microsoft directement via Internet, comme illustré ci-dessous.

![Internet] (./media/guidance-connecting-your-on-premises-network-to-azure/internet.png "Internet")

Ce type de connexion est généralement première utilisé pour se connecter aux services cloud Microsoft. Le tableau ci-dessous répertorie les avantages et inconvénients de ce type de connexion.



| **Avantages**| **Considérations relatives à la**|
|---------|---------|
|Ne nécessite aucune modification à votre réseau local dans la mesure où tous les périphériques client ont un accès illimité à toutes les adresses IP et ports sur Internet.|Bien que le trafic est chiffré souvent à l’aide de HTTPS, il peut être interception lors des transferts dans la mesure où elle parcourt l’Internet public.|
|Peuvent se connecter à tous les services de cloud Microsoft exposées à l’Internet public.|Latence inattendue parce que la connexion parcourt Internet.|
|Utilise votre connexion Internet existante.||
|Ne nécessite pas de gestion des périphériques connectivity.||

Cette connexion a aucun connectivité ou les coûts de bande passante dans la mesure où vous utilisez votre connexion Internet existante. 

## <a name="connecting-with-a-point-to-site-connection"></a>Connexion avec une connexion point-à-site

Ce type de connexion donne accès à des services cloud Microsoft via un tunnel tunnel protocole SSTP (Secure Socket) via Internet, comme illustré ci-dessous.

![P2S] Connexion (./media/guidance-connecting-your-on-premises-network-to-azure/p2s.png "point-à-site")

La connexion a lieu via votre connexion Internet existante, mais nécessite l’utilisation d’une passerelle VPN Azure. Le tableau ci-dessous répertorie les avantages et inconvénients de ce type de connexion.

| **Avantages**| **Considérations relatives à la**|
|---------|---------|
|Ne nécessite aucune modification à votre réseau local dans la mesure où tous les périphériques client ont un accès illimité à toutes les adresses IP et ports sur Internet.|Bien que le trafic est chiffré à l’aide de IPSec, il peut être interception lors des transferts dans la mesure où elle parcourt l’Internet public.|
|Utilise votre connexion Internet existante.|Latence inattendue parce que la connexion parcourt Internet.|
|Débit jusqu'à 200 Mo/s par passerelle.|Nécessite la création et la gestion des connexions distinctes entre chaque appareil sur votre réseau local et chaque passerelle à que chaque appareil doit se connecter.|
|Peut servir à se connecter aux services Azure pouvant être connectés à un (VNet) des réseaux virtuels Azure comme Machines virtuelles Azure et les Services de Cloud Azure.|Nécessite très peu d’administration en cours d’une passerelle VPN Azure.|
||Ne peuvent pas être utilisés pour se connecter à Microsoft Office 365 ou Dynamics CRM Online.
||Ne peuvent pas être utilisés pour se connecter aux services Azure qui ne peut pas être connectés à un VNet.|

En savoir plus sur le service [Passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) , ses [tarifs](https://azure.microsoft.com/pricing/details/vpn-gateway)et transfert de données sortantes [tarifs](https://azure.microsoft.com/pricing/details/data-transfers).

## <a name="connecting-with-a-site-to-site-connection"></a>Connexion avec une connexion à un site

Ce type de connexion donne accès à des services cloud Microsoft via un tunnel IPSec sur Internet, comme illustré ci-dessous.

![S2S] (./media/guidance-connecting-your-on-premises-network-to-azure/s2s.png "Connexion à un site")

La connexion a lieu via votre connexion Internet existante, mais nécessite l’utilisation d’une passerelle VPN Azure ses tarifs associés et le transfert de données sortant tarifs. Le tableau ci-dessous répertorie les avantages et inconvénients de ce type de connexion.

| **Avantages**| **Considérations relatives à la**|
|---------|---------|
|Tous les périphériques de votre réseau local peuvent communiquer avec les services Azure connectés à un VNet, il est inutile pour configurer les connexions individuelles pour chaque périphérique.|Bien que le trafic est chiffré à l’aide de IPSec, il peut être interception lors des transferts dans la mesure où elle parcourt l’Internet public.|
|Utilise votre connexion Internet existante.|Latence inattendue parce que la connexion parcourt Internet.|
|Peut être utilisé pour vous connecter à des services Azure pouvant être connectés à une VNet tels que des Machines virtuelles et Services Cloud.|Doit configurer et gérer un périphérique VPN validées * en local.|
|Débit jusqu'à 200 Mo/s par passerelle.|Nécessite très peu d’administration en cours d’une passerelle VPN Azure.|
|Pouvez forcer le trafic sortant initié des machines virtuelles de cloud via le réseau local pour l’inspection et journalisation à l’aide de routes définies par l’utilisateur ou la bordure BGP (Gateway Protocol) **.|Ne peuvent pas être utilisés pour se connecter à Microsoft Office 365 ou Dynamics CRM Online.|
||Ne peuvent pas être utilisés pour se connecter aux services Azure qui ne peut pas être connectés à un VNet.|
||Si vous utilisez les services qui génèrent des connexions de revenir aux périphériques en local et vos stratégies de sécurité ont besoin, vous devrez peut-être un pare-feu entre le réseau local et Azure.|

- * Afficher une liste des [périphériques VPN de validation](../vpn-gateway/vpn-gateway-about-vpn-devices.md#validated-vpn-devices).
- ** En savoir plus sur [itinéraires définies par l’utilisateur](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) ou [BGP](../vpn-gateway/vpn-gateway-bgp-overview.md) forcer routage à partir d’Azure VNets sur un appareil local.

## <a name="connecting-with-a-dedicated-private-connection"></a>Connexion avec une connexion dédiée privée

Ce type de connexion permet d’accéder à tous les services de cloud Microsoft via une connexion privée dédiée à Microsoft qui ne parcourt pas Internet, comme illustré ci-dessous.

![ER] (./media/guidance-connecting-your-on-premises-network-to-azure/er.png "ExpressRoute connexion")

La connexion nécessite l’utilisation du service ExpressRoute et une connexion à un fournisseur de connectivité. Le tableau ci-dessous répertorie les avantages et inconvénients de ce type de connexion.

| **Avantages**| **Considérations relatives à la**|
|---------|---------|
|Le trafic ne peut pas être interception lors des transferts via Internet public dans la mesure où une connexion dédiée via un fournisseur de services est utilisée.|Nécessite une gestion routeur local.|
|Bande passante jusqu'à 10 Go/s par ExpressRoute circuit et débit jusqu'à 2 Go/s pour chaque passerelle.|Nécessite une connexion à un fournisseur de connectivité dédiée.|
|Latence prévisible car il s’agit d’une connexion dédiée à Microsoft, qui ne parcourt pas Internet.|Peuvent nécessiter très peu d’administration en cours d’une ou plusieurs passerelles de VPN Azure (si connectez le circuit à VNets).|
|Ne nécessite pas de communication chiffrée, bien que vous pouvez chiffrer le trafic, si vous le souhaitez.| Si vous utilisez des services de cloud initier des connexions revenir aux périphériques en local, vous devrez peut-être un pare-feu entre le réseau local et Azure.|
|Peuvent se connecter directement à tous les services de cloud Microsoft, avec quelques exceptions *.|Nécessite la traduction d’adresses réseau (NAT) d’adresses IP locale saisie des centres de données Microsoft pour les services qui ne peut pas être connectés à un VNet.* *|
|Pouvez forcer le trafic sortant initié des machines virtuelles de cloud via le réseau local pour l’inspection et journalisation à l’aide de BGP.|

- * Afficher une [liste des services](../expressroute/expressroute-faqs.md#supported-services) qui ne peuvent pas être utilisés avec ExpressRoute. Votre abonnement Azure doit être approuvée pour vous connecter à Office 365.  Consultez l’article [Azure ExpressRoute pour Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd?ui=en-US&rs=en-US&ad=US&fromAR=1) pour plus d’informations.
- ** Plus d’informations sur ExpressRoute [NAT](../expressroute/expressroute-nat.md) exigences.

Apprenez-en davantage sur [ExpressRoute](../expressroute/expressroute-introduction.md)son associé [tarifs](https://azure.microsoft.com/pricing/details/expressroute)et des [fournisseurs de connectivité](../expressroute/expressroute-locations.md#connectivity-provider-locations).

## <a name="additional-considerations"></a>Considérations supplémentaires

- Plusieurs des options ci-dessus disposent de différentes limites maximales qu’ils peuvent prendre en charge pour les connexions VNet, connexions passerelle et d’autres critères. Il est recommandé de consulter le Azure [limite de mise en réseau](../azure-subscription-service-limits.md#networking-limits) pour mieux comprendre si un d’eux ont une incidence sur les types de connectivité que vous choisissez d’utiliser. 
- Si vous souhaitez connecter une passerelle à partir d’une connexion VPN de site à la même VNet comme une passerelle ExpressRoute, vous devez vous familiariser avec les contraintes importantes tout d’abord. Voir l’article [configurer ExpressRoute et connexions pouvant coexistence Site à](../expressroute/expressroute-howto-coexist-resource-manager.md#limits-and-limitations) pour plus d’informations.

## <a name="next-steps"></a>Étapes suivantes

Les ressources suivantes expliquent comment mettre en œuvre les types de connexions traitées dans cet article.

-   [Mettre en œuvre une connexion point-à-site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
-   [Mettre en œuvre une connexion à un site](guidance-hybrid-network-vpn.md)
-   [Mettre en œuvre une connexion privée dédiée](guidance-hybrid-network-expressroute.md)
-   [Mettre en œuvre une connexion privée dédiée avec une connexion à un site de disponibilité](guidance-hybrid-network-expressroute-vpn-failover.md)
