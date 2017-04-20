<properties
   pageTitle="Créer un réseau virtuel avec une connexion de Site à Site VPN à l’aide du Gestionnaire de ressources Azure et le portail Azure | Microsoft Azure"
   description="Comment créer VNet à l’aide du modèle de déploiement Gestionnaire de ressources et vous connecter à votre imprimante locale locale réseau à l’aide d’une connexion de passerelle VPN S2S."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/14/2016"
   ms.author="cherylmc"/>

# <a name="create-a-vnet-with-a-site-to-site-connection-using-the-azure-portal"></a>Créer un VNet avec une connexion de Site à l’aide du portail Azure

> [AZURE.SELECTOR]
- [Responsable de ressources - portail Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Responsable de ressources - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
- [Classique - portail classique](vpn-gateway-site-to-site-create.md)


Cet article vous guide dans la création d’un réseau virtuel et une connexion de passerelle VPN de Site à votre réseau local à l’aide du portail Azure et le modèle de déploiement d’Azure le Gestionnaire de ressources. Connexions de Site à peuvent être utilisées pour croisée local et hybride configurations.

![Diagramme](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/s2srmportal.png)


### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Modèles de déploiement et méthodes pour les connexions de Site à

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

Le tableau suivant indique les modèles de déploiement actuellement disponibles et les méthodes pour les configurations de Site à. Lorsqu’un article avec des étapes de configuration est disponible, nous attacher directement à partir de ce tableau.

[AZURE.INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Configurations supplémentaires 

Si vous voulez vous connecter VNets ensemble, mais ne créez pas d’une connexion à un emplacement local, voir [configurer une connexion VNet-VNet](vpn-gateway-vnet-vnet-rm-ps.md). Si vous souhaitez ajouter une connexion à un Site à un VNet contenant déjà une connexion, voir [Ajouter une connexion S2S à un VNet avec une connexion de passerelle VPN existante](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).

## <a name="before-you-begin"></a>Avant de commencer

Vérifiez que vous avez les éléments suivants avant de commencer votre configuration :

- Un périphérique VPN compatible et une personne qui est en mesure de configurer. Voir [à propos des appareils VPN](vpn-gateway-about-vpn-devices.md). Si vous ne familiarisé avec la configuration de votre appareil VPN, ou que vous ne connaissez pas l’adresse IP plages situées dans vos locaux configuration du réseau, vous devez coordonner avec une personne qui pourra vous fournir ces informations.

- Adresse IP publique tourné vers l’extérieur de votre périphérique VPN. Cette adresse IP ne peut pas être située derrière un NAT.
    
- Un abonnement Azure. Si vous n’avez pas un abonnement Azure, vous pouvez activer votre [avantages d’abonné MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrivez-vous vers le haut pour un [compte gratuit](http://azure.microsoft.com/pricing/free-trial/).

### <a name="values"></a>Exemples de valeurs de configuration pour cet exercice


Lorsque vous utilisez ces étapes en guise d’exercice, vous pouvez utiliser les exemples de valeurs de configuration :

- **VNet nom :** TestVNet1
- **Espace d’adressage :** 10.11.0.0/16 et 10.12.0.0/16
- **Sous-réseaux :**
    - FrontEnd : 10.11.0.0/24
    - Serveur principal : 10.12.0.0/24
    - GatewaySubnet : 10.12.255.0/27
- **Groupe de ressources :** TestRG1
- **Emplacement :** États-Unis Extrême-Orient
- **Serveur DNS :** 8.8.8.8
- **Nom de la passerelle :** VNet1GW
- **IP publique :** VNet1GWIP
- **Type VPN :** En fonction de gamme
- **Type de connexion :** Site-à-site (IPsec)
- **Type de passerelle :** VPN
- **Nom de la passerelle réseau local :** Site2
- **Nom de la connexion :** VNet1toSite2


## <a name="CreatVNet"></a>1. créer un réseau virtuel 

Si vous disposez déjà d’un VNet, vérifiez que les paramètres sont compatibles avec votre conception de passerelle VPN. Une attention particulière à n’importe quel sous-réseaux qui peuvent se superposer avec d’autres réseaux. Si vous avez superposés sous-réseaux, votre connexion ne fonctionne pas correctement. Si votre VNet est configuré avec les paramètres corrects, vous pouvez commencer les étapes décrites dans la section [spécifier un serveur DNS](#dns) .

### <a name="to-create-a-virtual-network"></a>Pour créer un réseau virtuel

[AZURE.INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]  

## <a name="subnets"></a>2. Ajoutez sous-réseaux et espace d’adressage supplémentaires

Vous pouvez ajouter des sous-réseaux et espace d’adressage supplémentaires à votre VNet une fois qu’il a été créé.

[AZURE.INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)] 

## <a name="dns"></a>3. spécifier un serveur DNS

### <a name="to-specify-a-dns-server"></a>Pour spécifier un serveur DNS

[AZURE.INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="gatewaysubnet"></a>4. créer un sous-réseau passerelle

Avant de vous connecter à votre réseau virtuel à une passerelle, vous devez tout d’abord créer le sous-réseau passerelle pour le réseau virtuel auquel vous voulez vous connecter. Si possible, il est conseillé de créer un sous-réseau passerelle à l’aide d’un bloc CIDR de /28 ou /27 afin de fournir suffisamment d’adresses IP pour répondre aux exigences de configuration futures supplémentaires.

Si vous créez cette configuration en guise d’exercice, reportez-vous à ces [valeurs](#values) lorsque vous créez votre sous-réseau passerelle.

### <a name="to-create-a-gateway-subnet"></a>Pour créer un sous-réseau passerelle


[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="VNetGateway"></a>5. créer une passerelle réseau virtuel

Si vous créez cette configuration en guise d’exercice, vous pouvez faire référence aux [valeurs de configuration d’exemple](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Pour créer une passerelle réseau virtuel

[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="LocalNetworkGateway"></a>6. créer une passerelle réseau local

La passerelle de réseau local fait référence à votre emplacement local. Donnez un nom par lequel Azure y référer à la passerelle réseau local. 

Si vous créez cette configuration en guise d’exercice, vous pouvez faire référence aux [valeurs de configuration d’exemple](#values).

### <a name="to-create-a-local-network-gateway"></a>Pour créer une passerelle réseau local

[AZURE.INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="VPNDevice"></a>7. configurer votre appareil VPN

[AZURE.INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="CreateConnection"></a>8. créer une connexion VPN de Site à

Créer la connexion VPN de Site à entre votre passerelle réseau virtuel et votre appareil VPN. N’oubliez pas de remplacer les valeurs par votre propre. La clé partagée doit correspondre à la valeur que vous avez utilisé pour la configuration de vos périphériques VPN. 

Avant de commencer cette section, vérifiez que votre passerelle réseau virtuel et passerelles réseau local ont terminé de créer. Si vous créez cette configuration en guise d’exercice, reportez-vous à ces [valeurs](#values) lorsque vous créez votre connexion.

### <a name="to-create-the-vpn-connection"></a>Pour créer la connexion VPN


[AZURE.INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-rm-portal-include.md)]

## <a name="VerifyConnection"></a>9. Vérifiez que la connexion VPN

Vous pouvez vérifier votre connexion VPN dans le portail ou à l’aide de PowerShell.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="next-steps"></a>Étapes suivantes

- Une fois que votre connexion est terminée, vous pouvez ajouter des machines virtuelles à vos réseaux virtuel. Voir les machines virtuelles [rubriques d’apprentissage](https://azure.microsoft.com/documentation/learning-paths/virtual-machines) pour plus d’informations.

- Pour plus d’informations sur BGP, consultez la [Vue d’ensemble BGP](vpn-gateway-bgp-overview.md) et [comment configurer BGP](vpn-gateway-bgp-resource-manager-ps.md).
