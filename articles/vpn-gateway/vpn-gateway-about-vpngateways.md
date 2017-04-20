<properties 
   pageTitle="À propos de la passerelle VPN | Microsoft Azure"
   description="En savoir plus sur les connexions VPN passerelle pour les réseaux virtuels Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="cherylmc" />

# <a name="about-vpn-gateway"></a>À propos de la passerelle VPN


Une passerelle réseau virtuel est utilisée pour acheminer le trafic réseau entre réseaux virtuels Azure et emplacements en local et également entre les réseaux virtuels dans Azure (VNet à VNet). Lorsque vous configurez une passerelle VPN, vous devez créer et configurer une passerelle réseau virtuel et une connexion de passerelle réseau virtuel.

Dans le modèle de déploiement Gestionnaire de ressources, lorsque vous créez une ressource de passerelle réseau virtuel, vous spécifiez plusieurs paramètres. Un des paramètres requis est «-GatewayType ». Il existe deux types de passerelle réseau virtuel : Vpn et ExpressRoute. 

Lorsque le trafic réseau est envoyé sur une connexion privée dédiée, vous utilisez le type de passerelle « ExpressRoute ». Il est également appelé une passerelle ExpressRoute. Lorsque le trafic réseau est envoyé chiffré via une connexion publique, vous utilisez le type de passerelle « Vpn ». Ceci est appelé une passerelle VPN. Sites, Point-à-Site et VNet-VNet connexions utilisent une passerelle VPN.

Chaque réseau virtuel peut avoir qu’une seule passerelle réseau virtuel par type de passerelle. Par exemple, vous pouvez avoir un réseau virtuel la passerelle qui utilise - GatewayType ExpressRoute et celui qui utilise - GatewayType Vpn. Cet article se concentre principalement sur passerelle VPN. Pour plus d’informations sur ExpressRoute, consultez la [Présentation technique ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="pricing"></a>Tarifs

[AZURE.INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)] 


## <a name="gateway-skus"></a>Références SKU passerelle

[AZURE.INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

Pour plus d’informations sur les références SKU passerelle, voir [Références SKU passerelle](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

### <a name="estimated-aggregate-throughput-by-sku"></a>Estimée débit agrégation par référence (SKU)

Le tableau suivant montre les types de passerelle et le débit agrégation estimé. Ce tableau s’applique au Gestionnaire de ressources et les modèles de déploiement classique.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)] 

## <a name="configuring-a-vpn-gateway"></a>Configurez une passerelle VPN

Lorsque vous configurez une passerelle VPN, les instructions que vous utilisez dépendent du modèle de déploiement que vous avez utilisé pour créer votre réseau virtuel. Par exemple, si vous avez créé votre VNet à l’aide du modèle de déploiement classique, vous utilisez les instructions pour le modèle de déploiement classique et les instructions pour créer et configurer vos paramètres de la passerelle VPN. Pour plus d’informations sur les modèles de déploiement, voir [Présentation du gestionnaire ressources et modèles de déploiement classique](../resource-manager-deployment-model.md).

Une connexion de passerelle VPN repose sur plusieurs ressources qui sont configurés avec des paramètres spécifiques. La plupart des ressources peut être configurée séparément, bien qu’ils doivent être configurés dans un certain ordre dans certains cas. Vous pouvez commencer à créer et configurer des ressources à l’aide d’un outil de configuration, tels que le portail Azure. Vous pouvez puis décidez basculer vers un autre outil, tels que PowerShell, pour configurer des ressources supplémentaires, ou pour modifier des ressources existantes, le cas échéant. Pour l’instant, vous ne pouvez pas configurer chaque ressource et paramètre de la ressource dans le portail Azure. Les instructions fournies dans les articles pour chaque topologie de connexion Spécifiez lorsqu’il manque un outil de configuration spécifiques. Pour plus d’informations sur les ressources individuels et les paramètres pour la passerelle VPN, voir [paramètres sur VPN passerelle](vpn-gateway-about-vpn-gateway-settings.md).

Les sections suivantes contiennent des tables de la liste :

- modèle de déploiement disponibles
- outils de configuration disponibles
- liens qui vous permettront directement vers un article, le cas échéant

Utilisez les diagrammes et les descriptions pour aider à choisir la topologie de connexion à vos besoins spécifiques. Les schémas montrent les topologies référence principale, mais il est possible de créer des configurations plus complexes à l’aide de diagrammes en règle générale.

## <a name="site-to-site-and-multi-site"></a>Site-à-Site ou de plusieurs sites

### <a name="site-to-site"></a>Site à

Une connexion de passerelle VPN de Site à Site (S2S) est une connexion sur tunnel VPN IPsec/IKE (IKEv1 ou IKEv2). Ce type de connexion nécessite un périphérique VPN situé en local qui contient une adresse IP publique est affectée à et n’est pas situé derrière un NAT. Connexions S2S peuvent être utilisées pour effectuer local et hybride configurations.   

![Connexion S2S] (./media/vpn-gateway-about-vpngateways/demos2s.png "site à")


### <a name="multi-site"></a>Plusieurs sites

Vous pouvez créer et configurer une connexion de passerelle VPN entre votre VNet et plusieurs réseaux locaux. Lorsque vous travaillez avec plusieurs connexions, vous devez utiliser un type de RouteBased VPN (passerelle dynamique pour VNets classique). Un VNet ne peut avoir une passerelle VPN, toutes les connexions via la passerelle partagent la bande passante disponible. Cette option est souvent appelée une connexion « multi-site ».
 

![Connexion de plusieurs sites] (./media/vpn-gateway-about-vpngateways/demomulti.png "plusieurs sites")

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>Modèles de déploiement et méthodes de Site à Site ou de plusieurs sites

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)] 

## <a name="vnet-to-vnet"></a>VNet-VNet

Connexion d’un réseau virtuel à un autre réseau virtuel (VNet-VNet) est similaire à connecter un VNet à un emplacement de site local. Les deux types de connectivité permet de fournir un tunnel sécurisé à l’aide de IPsec/IKE une passerelle VPN. Vous pouvez même combiner VNet-VNet communication avec les configurations de connexion de plusieurs sites. Cela vous permet d’établir des topologies réseau qui combinent connectivité entre locaux avec la connectivité réseau entre virtuel.

La VNets que vous vous connectez peut être :

- dans les régions identiques ou différentes
- dans les abonnements identiques ou différents 
- dans les même différents modèles d’approvisionnement


![VNet vers VNet connexion] (./media/vpn-gateway-about-vpngateways/demov2v.png "vnet-vnet")

#### <a name="connections-between-deployment-models"></a>Connexions entre les modèles de déploiement

Azure a actuellement deux modèles de déploiement : classique et Gestionnaire de ressources. Si vous utilisez Azure depuis un certain temps, vous avez probablement Azure machines virtuelles et des rôles d’instance en cours d’exécution dans un VNet classique. Votre plus récentes machines virtuelles et des instances de rôle peuvent être exécuté dans un VNet créé dans le Gestionnaire des ressources. Vous pouvez créer une connexion entre le VNets pour autoriser les ressources dans un seul VNet de communiquer avec des ressources dans un autre.

#### <a name="vnet-peering"></a>VNet peering

Vous pouvez éventuellement utiliser VNet peering pour créer votre connexion, dans la mesure où votre réseau virtuel répond à certaines exigences. VNet peering n’utilise pas une passerelle réseau virtuel. Pour plus d’informations, voir [VNet peering](../virtual-network/virtual-network-peering-overview.md).


### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>Méthodes pour VNet-VNet et des modèles de déploiement

[AZURE.INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)] 


## <a name="point-to-site"></a>Point-à-Site

Une connexion de passerelle VPN Point-à-Site (P2S) vous permet de créer une connexion sécurisée à votre réseau virtuel à partir d’un ordinateur client. P2S est une connexion VPN sur SSTP (Secure Socket protocole). Connexions P2S ne nécessitent pas un périphérique VPN ou une adresse IP au public pour l’utiliser. Vous établissez la connexion VPN en commençant à partir de l’ordinateur client. Cette solution est utile lorsque vous voulez vous connecter à votre VNet depuis un emplacement distant, tels que d’accueil ou d’une conférence, ou lorsque vous n’avez que quelques clients qui doivent se connecter à un VNet. Connexions P2S peuvent être utilisées en association avec les connexions S2S via la même passerelle VPN, autant que tous de la configuration requise pour les connexions sont compatibles.


Connexion ![point-à-site] (./media/vpn-gateway-about-vpngateways/demop2s.png "point-à-site")

### <a name="deployment-models-and-methods-for-point-to-site"></a>Méthodes pour Point-à-Site et des modèles de déploiement

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)] 


## <a name="expressroute"></a>ExpressRoute

[AZURE.INCLUDE [expressroute-intro](../../includes/expressroute-intro-include.md)]

Dans une connexion ExpressRoute, une passerelle réseau virtuel est configurée avec le type de passerelle 'ExpressRoute', plutôt que « Vpn ». Pour plus d’informations sur ExpressRoute, consultez la [présentation technique ExpressRoute](../expressroute/expressroute-introduction.md).


## <a name="site-to-site-and-expressroute-coexisting-connections"></a>Connexions pouvant coexistence Site à et ExpressRoute

ExpressRoute est une connexion dédiés à partir de votre réseau étendu (pas sur l’Internet public) aux Services Microsoft, y compris Azure. Site à VPN le trafic est transmis chiffrées via Internet public. Possibilité de configurer les connexions VPN de Site à et ExpressRoute pour le même réseau virtuel présente plusieurs avantages.

Vous pouvez configurer un réseau privé virtuel de Site à comme un chemin d’accès de basculement sécurisé pour ExpressRoute, ou utiliser des VPN de Site à vous connecter aux sites qui ne font pas partie de votre réseau, mais qui sont connectés via ExpressRoute. Notez que cette modification nécessite deux passerelles réseau virtuel pour le même réseau virtuel, une à l’aide de - GatewayType Vpn et l’autre à l’aide de - GatewayType ExpressRoute.


![Connexion coexister] (./media/vpn-gateway-about-vpngateways/demoer.png "expressroute site2site")


### <a name="deployment-models-and-methods-for-s2s-and-expressroute"></a>Méthodes pour S2S et ExpressRoute et des modèles de déploiement

[AZURE.INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)] 


## <a name="next-steps"></a>Étapes suivantes

Planifier votre configuration de passerelle VPN. Voir [Création et planification de la passerelle VPN](vpn-gateway-plan-design.md) et [vous connectez votre réseau local vers Azure](../guidance/guidance-connecting-your-on-premises-network-to-azure.md).








 
