<properties 
   pageTitle="Conception et planification de la passerelle VPN | Microsoft Azure"
   description="En savoir plus sur la planification de la passerelle VPN et du style entre local, hybride et les connexions de VNet-VNet"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="cherylmc"/>

# <a name="planning-and-design-for-vpn-gateway"></a>Planification et la conception pour la passerelle VPN

Planifier et concevoir votre croisée locaux et des configurations VNet-VNet peuvent être simple ou complexe, selon vos besoins réseau. Cet article vous guide tout au long de base considérations relatives à la planification et la conception.

## <a name="planning"></a>Planification


### <a name="compare"></a>Options de connectivité entre locaux

Si vous voulez vous connecter en toute sécurité de vos sites en local à un réseau virtuel, vous avez trois façons d’y parvenir : sites, Point-à-Site et ExpressRoute. Comparer les connexions de site entre différentes sont disponibles. L’option que vous choisissez peut dépendre considérations différents, tels que :


- Quel type de débit votre solution nécessite-t-il ?
- Vous souhaitez communiquer sur l’Internet public via VPN sécurisée, ou via une connexion privée ?
- Avez-vous une adresse IP publique utilisée ?
- Vous envisagez d’utiliser un appareil VPN ? Si c’est le cas, est-il compatible ?
- Vous vous connectez quelques ordinateurs ou si vous souhaitez une connexion permanente pour votre site ?
- Quels types de passerelle VPN est requis pour la solution que vous voulez créer ?
- Quelle passerelle SKU devez-vous utiliser ?


Le tableau suivant peut vous aider à décider la meilleure option de votre solution de connexion.


[AZURE.INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]



### <a name="gwrequire"></a>Configuration requise de passerelle par type VPN et de référence (SKU)

[AZURE.INCLUDE [vpn-gateway-gwsku](../../includes/vpn-gateway-gwsku-include.md)]

Pour plus d’informations sur les références SKU passerelle, voir [paramètres de la passerelle VPN](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

#### <a name="aggregate-throughput-by-sku-and-vpn-type"></a>Débit total par type de référence (SKU) et VPN

Le tableau suivant montre les types de passerelle et le débit agrégation estimé. Le débit estimé agrégation peut être décisive pour votre conception.
Tarifs différence entre les références SKU passerelle. Pour plus d’informations sur les tarifs, consultez [Tarification de passerelle VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/). Ce tableau s’applique au Gestionnaire de ressources et les modèles de déploiement classique.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggtput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)] 

#### <a name="supported-configurations-by-sku-and-vpn-type"></a>Configurations prises en charge par type de référence (SKU) et VPN

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)] 

### <a name="wf"></a>Flux de travail

La liste suivante présente le flux de travail courantes pour la connectivité cloud :

1.  Concevoir et planifier votre topologie de la connectivité et répertorier les espaces d’adressage pour tous les réseaux que vous voulez vous connecter.
2.  Créer un réseau virtuel Azure. 
3.  Créer une passerelle VPN pour le réseau virtuel.
4.  Créer et configurer les connexions pour locaux ou d’autres réseaux virtuels (le cas échéant).
5.  Créer et configurer une connexion Point-à-Site pour votre passerelle VPN Azure (le cas échéant).
 

## <a name="design"></a>Création

### <a name="topologies"></a>Topologies de connexions

Commencez par examiner les diagrammes dans l’article [Sur VPN passerelle](vpn-gateway-about-vpngateways.md) . Cet article contient des diagrammes de base, les modèles de déploiement pour chaque topologie (Gestionnaire de ressources ou classique) et le déploiement des outils que vous pouvez utiliser pour déployer votre configuration.   

### <a name="designbasics"></a>Concepts de base

Les sections suivantes décrivent les notions de base de passerelle VPN. Envisagez également de [mise en réseau limitations services](../articles/azure-subscription-service-limits.md#networking-limits).


#### <a name="subnets"></a>À propos de sous-réseaux

Lorsque vous créez des connexions, vous devez prendre en compte vos plages sous-réseau. Vous ne peuvent pas porter le chevauchement des plages d’adresses sous-réseau. Un qui se chevauchent constitue lorsqu’un réseau virtuel ou emplacement local contient le même espace d’adressage qui contient l’autre emplacement. Cela signifie que vous devez vos ingénieurs réseau pour vos réseaux locaux locale d’imaginer une plage pour pouvoir utiliser pour votre adresse IP Azure sous-réseaux/espace d’adressage. Vous avez besoin d’espace d’adressage qui n’est pas utilisé sur le réseau local en local. 

Pour éviter d’obtenir superposés sous-réseaux est également important lorsque vous travaillez avec des connexions VNet-VNet. Si vos sous-réseaux se chevauchent et une adresse IP existe dans l’envoi et de destination VNets, les connexions de VNet-VNet échouer. Azure ne peut pas acheminer les données à l’autre VNet, car l’adresse de destination fait partie de l’envoi VNet. 

Passerelles VPN nécessitent un sous-réseau spécifique appelé un sous-réseau passerelle. Tous les sous-réseaux passerelle doivent être nommés GatewaySubnet fonctionne correctement. N’oubliez pas ne pas d’un autre nom de votre sous-réseau passerelle et ne déployer des machines virtuelles ou tout autre élément au sous-réseau passerelle. Consultez [les sous-réseaux passerelle](vpn-gateway-about-vpn-gateway-settings.md#gwsub).

#### <a name="local"></a>À propos des passerelles réseau local

La passerelle réseau local signifie généralement votre emplacement local. Dans le modèle de déploiement classique, la passerelle réseau local est appelée un Site de réseau Local. Lorsque vous configurez une passerelle réseau local, vous lui donnez un nom, spécifiez l’adresse IP du périphérique VPN en local et spécifiez les préfixes d’adresses qui se trouvent dans l’emplacement local. Azure examine les préfixes d’adresse de destination pour le trafic réseau, consulte la configuration que vous avez spécifiée pour la passerelle réseau local et achemine les paquets en conséquence. Vous pouvez modifier ces préfixes d’adresse selon vos besoins. Pour plus d’informations, voir [passerelles réseau Local](vpn-gateway-about-vpn-gateway-settings.md#lng).


#### <a name="gwtype"></a>À propos des types de passerelle

Il est essentiel de sélectionner le type de passerelle correct pour votre topologie. Si vous sélectionnez le type est incorrect, votre passerelle ne fonctionne pas correctement. Le type de passerelle spécifie comment la passerelle elle-même se connecte et une configuration requise pour le modèle de déploiement du Gestionnaire de ressources.

Les types de passerelle sont :

- VPN
- ExpressRoute

#### <a name="connectiontype"></a>À propos des types de connexion

Chaque configuration nécessite un type de connexion spécifiques. Les types de connexion sont :

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient


#### <a name="vpntype"></a>À propos des types de VPN

Chaque configuration nécessite un type VPN spécifique. Si vous voulez combiner deux configurations, telles que la création d’une connexion à un Site et une connexion Point-à-Site à la même VNet, vous devez utiliser un type VPN qui remplit les deux conditions de connexion.

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)] 

Les tableaux suivants illustrent le type VPN car il correspond à chaque configuration de la connexion. Vérifiez que le type de VPN pour votre passerelle correspond à la configuration que vous souhaitez créer. 


[AZURE.INCLUDE [vpn-gateway-table-vpntype](../../includes/vpn-gateway-table-vpntype-include.md)] 

### <a name="devices"></a>Appareils VPN pour les connexions de Site à

Pour configurer une connexion à un Site, quel que soit le modèle de déploiement, vous devez les éléments suivants :

- Un périphérique VPN compatible avec les passerelles VPN Azure
- Une adresse IP IPv4 NOT qui n’est pas derrière un NAT

Vous devez disposent de configurer votre appareil VPN ou d’une personne que vous pouvez configurer le périphérique pour vous. Pour plus d’informations sur les périphériques VPN, voir [VPN sur les appareils](vpn-gateway-about-vpn-devices.md). L’article appareils VPN contient des informations sur les appareils validés, configuration requise pour les périphériques qui n’ont pas été validées et des liens vers des documents de configuration appareil s’il est disponible.

### <a name="forcedtunnel"></a>Prendre en considération le routage tunnel forcé

Pour la plupart des configurations, vous pouvez configurer tunnel forcé. Tunnel forcé vous permet de rediriger ou « force « tout le trafic Internet liées aux retourner à votre emplacement local via un tunnel VPN de Site à pour inspection et l’audit. Il s’agit d’une condition de sécurité critiques pour la plupart des entreprises informatiques stratégies. 

Sans tunnel forcé, le trafic Internet lié à partir de vos ordinateurs virtuels dans Azure parcourt toujours à partir de l’infrastructure réseau Azure directement à Internet, sans l’option vous permet d’inspecter ou de vérifier le trafic. Accès Internet non autorisés susceptibles d’entraîner divulgation d’informations ou d’autres types de violations de sécurité.

**Obligation de diagramme tunnel**

![Connexion forcé tunnel] (./media/vpn-gateway-plan-design/forced-tunnel.png "forcé tunnel")

Une connexion tunnel forcée peut être configurée dans les deux modèles de déploiement et à l’aide des différents outils. Consultez le tableau suivant pour plus d’informations. Nous apportez ce tableau nouveaux articles, des nouveaux modèles de déploiement et des outils supplémentaires sont disponibles pour cette configuration. Lorsqu’un article est disponible, nous attacher directement à partir de la table.

[AZURE.INCLUDE [vpn-gateway-table-forcedtunnel](../../includes/vpn-gateway-table-forcedtunnel-include.md)] 



## <a name="next-steps"></a>Étapes suivantes

Voir les articles [Forum aux questions de la passerelle VPN](vpn-gateway-vpn-faq.md) et [Sur VPN passerelle](vpn-gateway-about-vpngateways.md) pour plus d’informations pour vous aider à votre conception.

Pour plus d’informations sur les paramètres de la passerelle spécifiques, voir [à propos de VPN paramètres de la passerelle](vpn-gateway-about-vpn-gateway-settings.md).




