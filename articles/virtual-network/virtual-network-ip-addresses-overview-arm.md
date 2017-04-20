<properties
   pageTitle="Publiques et privées dans le Gestionnaire de ressources Azure adressage IP | Microsoft Azure"
   description="En savoir plus sur privée et les adresses IP dans le Gestionnaire de ressources Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/27/2016"
   ms.author="jdial" />

# <a name="ip-addresses-in-azure"></a>Adresses IP dans Azure
Vous pouvez affecter des adresses IP aux ressources Azure pour communiquer avec d’autres ressources Azure, votre réseau local et Internet. Il existe deux types d’adresses IP que vous pouvez utiliser dans Azure :

- **Adresses IP public**: utilisé pour communiquer avec Internet, y compris les services au public Azure
- **Adresses IP privées**: utilisé pour la communication au sein d’un réseau virtuel Azure (VNet) et vos localement réseau lorsque vous utilisez une passerelle VPN ou circuit ExpressRoute pour étendre votre réseau à Azure.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modèle de déploiement classique](virtual-network-ip-addresses-overview-classic.md).

Si vous êtes familiarisé avec le modèle de déploiement classique, vérifiez la [différences entre classique d’adressage IP et le Gestionnaire de ressources](virtual-network-ip-addresses-overview-classic.md#Differences-between-Resource-Manager-and-classic-deployments).

## <a name="public-ip-addresses"></a>Adresses IP publiques
Adresses IP publiques autoriser les ressources Azure communiquer avec les services Internet et Azure not tels que [stockage Azure](../storage/storage-introduction.md), [bases de données SQL](../sql-database/sql-database-technical-overview.md), [Azure Redis Cache](https://azure.microsoft.com/services/cache/)et [Azure événement Hubs](https://azure.microsoft.com/services/event-hubs/).

Dans le Gestionnaire de ressources Azure, une adresse [IP publique](resource-groups-networking.md#public-ip-address) est une ressource qui possède ses propres propriétés. Vous pouvez associer une ressource d’adresse IP publique à une des ressources suivantes :

- Machines virtuelles (machine virtuelle)
- Programmes d’équilibrage de charge via Internet
- Passerelles VPN
- Passerelles d’application

### <a name="allocation-method"></a>Méthode de répartition
Il existe deux manières dans lequel une adresse IP est affectée à une ressource IP *public* - *statique*ou *dynamique* . La méthode d’allocation par défaut est *dynamique*, où une adresse IP **pas** alloué au moment de sa création. En revanche, l’adresse IP est attribué lorsque vous démarrez (ou créez) la ressource associée (par exemple, un équilibrage de la machine virtuelle ou charge). L’adresse IP est publié lorsque vous arrêtez (ou supprimez) la ressource. Ainsi, l’adresse IP à modifier lorsque vous arrêtez et démarrez une ressource.

Pour vous assurer que l’adresse IP de la ressource associée reste la même, vous pouvez définir la méthode d’allocation explicitement *statique*. Dans ce cas, une adresse IP est attribuée immédiatement. Il est publié uniquement lorsque vous supprimez la ressource ou modifiez sa méthode d’allocation *dynamique*.

>[AZURE.NOTE] Même si vous définissez la méthode d’allocation *statique*, vous ne pouvez pas spécifier l’adresse IP réelle affectée à cette *ressource IP public*. Au lieu de cela, il est alloué à partir d’un pool d’adresses IP disponibles à l’emplacement Azure dans que la ressource est créée.

Statique des adresses IP publiques sont fréquemment utilisés dans les scénarios suivants :

- Les utilisateurs finaux doivent mettre à jour des règles de pare-feu pour communiquer avec vos ressources Azure.
- Résolution de noms DNS, où une modification dans la zone adresse IP requiert la mise à jour des enregistrements A.
- Vos ressources Azure permet de communiquer avec d’autres applications ou services qui utilisent un modèle de sécurité basée sur les adresses IP.
- Vous utilisez des certificats SSL liés à une adresse IP.

>[AZURE.NOTE] La liste des plages d’adresses IP à partir de laquelle des adresses IP publiques (dynamique/statique) sont affectés à des ressources Azure est publiée en [plages d’adresses IP Azure centre de données](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="dns-hostname-resolution"></a>Résolution du nom d’hôte DNS
Vous pouvez spécifier une étiquette de nom de domaine DNS pour une ressource IP publique, ce qui crée un mappage pour *domainnamelabel*. *emplacement*. cloudapp.azure.com vers l’adresse IP dans les serveurs DNS géré Azure. Par exemple, si vous créez une ressource IP publique avec **contoso** comme un *domainnamelabel* aux **États-Unis Ouest** Azure *emplacement*, le nom (FQDN) de domaine complet **contoso.westus.cloudapp.azure.com** résoudra vers l’adresse IP de la ressource. Vous pouvez utiliser ce nom de domaine complet pour créer un enregistrement CNAME de domaine personnalisé en pointant sur l’adresse IP publique dans Azure.

>[AZURE.IMPORTANT] Chaque étiquette de nom de domaine créée doit être unique au sein de son emplacement Azure.  

### <a name="virtual-machines"></a>Machines virtuelles
Vous pouvez associer une adresse IP publique à un [Windows](../virtual-machines/virtual-machines-windows-about.md) ou [Linux](../virtual-machines/virtual-machines-linux-about.md) machine virtuelle en affectant à son **interface réseau**. Dans le cas d’une interface multi-réseau machine virtuelle, vous pouvez l’affecter à l’interface de réseau *principal* uniquement. Vous pouvez affecter un dynamiques ou une adresse IP publique statique pour une machine virtuelle.

### <a name="internet-facing-load-balancers"></a>Programmes d’équilibrage de charge via Internet
Vous pouvez associer une adresse IP publique à un [Équilibrage de charge Azure](../load-balancer/load-balancer-overview.md), en attribuant à la configuration de **frontend** d’équilibrage de charge. Cette adresse IP publique sert une équilibrage de charge adresse IP virtuelle (VIP). Vous pouvez affecter un dynamiques ou une adresse IP publique statique à un équilibrage de charge frontal. Vous pouvez également affecter plusieurs adresses IP publiques à un équilibrage de charge frontal, qui permet de scénarios [multi-VIP](../load-balancer/load-balancer-multivip.md) comme un environnement client multiples avec des sites Web SSL.

### <a name="vpn-gateways"></a>Passerelles VPN
[Azure VPN passerelle](../vpn-gateway/vpn-gateway-about-vpngateways.md) est utilisée pour vous connecter à un réseau virtuel Azure (VNet) à d’autres VNets Azure ou à un réseau local. Vous devez attribuer une adresse IP publique à sa **configuration IP** pour lui permettre de communiquer avec le réseau à distance. Pour l’instant, vous pouvez uniquement affecter une adresse IP publique *dynamique* vers une passerelle VPN.

### <a name="application-gateways"></a>Passerelles d’application
Vous pouvez associer une adresse IP publique avec une Azure [Passerelle d’Application](../application-gateway/application-gateway-introduction.md), en attribuant à la configuration de **site Web frontal** de la passerelle. Cette adresse IP publique sert un VIP équilibrage de charge. Pour l’instant, vous pouvez uniquement affecter une adresse IP publique *dynamique* à une configuration application passerelle frontend.

### <a name="at-a-glance"></a>Un coup de œil
Le tableau ci-dessous indique la propriété spécifique à laquelle une adresse IP publique peut être associée à une ressource de niveau supérieur et les méthodes d’allocation possible (statiques ou dynamiques) qui peuvent être utilisés.

|Ressources de niveau supérieur|Association de l’adresse IP|Dynamique|Statique|
|---|---|---|---|
|Machine virtuelle|Interface réseau|Oui|Oui|
|Équilibrage de charge|Configuration du serveur frontal|Oui|Oui|
|Passerelle VPN|Configuration de la passerelle IP|Oui|N°|
|Passerelle d’application|Configuration du serveur frontal|Oui|N°|

## <a name="private-ip-addresses"></a>Adresses IP privées
Adresses IP privées autoriser les ressources Azure à communiquer avec d’autres ressources dans un [réseau virtuel](virtual-networks-overview.md) ou un réseau local via une passerelle VPN ou circuit ExpressRoute, sans l’aide d’une adresse IP Internet accessible.

Dans le modèle de déploiement Azure le Gestionnaire de ressources, une adresse IP privée est associée aux types de ressources Azure suivants :

- Machines virtuelles
- Programmes d’équilibrage de charge interne (ILBs)
- Passerelles d’application

### <a name="allocation-method"></a>Méthode de répartition
Une adresse IP privée est attribuée à partir de la plage d’adresses du sous-réseau à laquelle la ressource est associée. La plage d’adresses du sous-réseau elle-même est un composant de plage d’adresses de VNet.

Il existe deux manières dans lequel une adresse IP privée allouée : *statique*ou *dynamique* . La méthode d’allocation par défaut est *dynamique*, où l’adresse IP est alloué automatiquement à partir de sous-réseau de la ressource (à l’aide de DHCP). Cette adresse IP peut changer lorsque vous arrêtez et démarrez la ressource.

Vous pouvez définir la méthode d’allocation *statique* afin de garantir que l’adresse IP reste la même. Dans ce cas, vous devez également fournir une adresse IP valide qui fait partie du sous-réseau de la ressource.

Adresses IP privées statiques soient fréquemment utilisés pour :

- Machines virtuelles agissent comme domaine ou des serveurs DNS.
- Ressources qui ont besoin de règles de pare-feu à l’aide d’adresses IP.
- Ressources accédés par d’autres applications/ressources via une adresse IP.

### <a name="virtual-machines"></a>Machines virtuelles
Une adresse IP privée est affectée à l' **interface du réseau** d’une [Windows](../virtual-machines/virtual-machines-windows-about.md) ou [Linux](../virtual-machines/virtual-machines-linux-about.md) machine virtuelle. Dans le cas d’une interface multi-réseau machine virtuelle, chaque interface Obtient une adresse IP privée affectée. Vous pouvez spécifier la méthode d’allocation comme dynamique ou statique pour une interface réseau.

#### <a name="internal-dns-hostname-resolution-for-vms"></a>Résolution de nom d’hôte DNS interne (pour les machines virtuelles)
Tous les ordinateurs virtuels Azure sont configurés avec les [serveurs DNS géré Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) par défaut, sauf si vous configurez explicitement serveurs DNS personnalisés. Ces serveurs DNS fournissent la résolution de noms interne pour les machines virtuelles qui résident dans le même VNet.

Lorsque vous créez une machine virtuelle, un mappage pour le nom d’hôte à son adresse IP privée est ajouté aux serveurs DNS géré Azure. Dans le cas d’une interface multi-réseau machine virtuelle, le nom d’hôte est mappé à l’adresse IP privée de l’interface réseau principale.

Machines virtuelles configurés avec des serveurs DNS gérés Azure seront en mesure de résoudre les noms d’hôtes de tous les ordinateurs virtuels au sein de leur VNet à leurs adresses IP privés.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Programmes d’équilibrage de charge interne (ILB) et passerelles d’Application
Vous pouvez affecter une adresse IP privée à la configuration du **serveur frontal** d’un [Programme d’équilibrage de charge interne Azure](../load-balancer/load-balancer-internal-overview.md) (ILB) ou d’une [Passerelle d’Application Azure](../application-gateway/application-gateway-introduction.md). Cette adresse IP privée sert à un point final interne, accessible uniquement aux ressources dans son réseau virtuel (VNet) et les réseaux distants connectés à la VNet. Vous pouvez affecter soit une adresse IP statique ou dynamique privée à la configuration du serveur frontal.

### <a name="at-a-glance"></a>Un coup de œil
Le tableau ci-dessous indique la propriété spécifique à laquelle une adresse IP privée peut être associée à une ressource de niveau supérieur et les méthodes d’allocation possible (statiques ou dynamiques) qui peuvent être utilisés.

|Ressources de niveau supérieur|Association d’adresses IP|Dynamique|Statique|
|---|---|---|---|
|Machine virtuelle|Interface réseau|Oui|Oui|
|Équilibrage de charge|Configuration du serveur frontal|Oui|Oui|
|Passerelle d’application|Configuration du serveur frontal|Oui|Oui|

## <a name="limits"></a>Limites

Les limites imposées aux adresses IP sont indiquées dans l’ensemble des [limites de mise en réseau](azure-subscription-service-limits.md#networking-limits) dans Azure. Ces limites s’appliquent par région et par abonnement. Vous pouvez [contacter le support technique](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour augmenter les limites par défaut jusqu'à atteindre les limites maximales selon vos besoins professionnels.

## <a name="pricing"></a>Tarifs

Adresses IP publiques peuvent avoir une charge nominale. Pour en savoir plus sur les tarifs d’adresses IP dans Azure, examinez la page [tarifs d’adresse IP](https://azure.microsoft.com/pricing/details/ip-addresses) .

## <a name="next-steps"></a>Étapes suivantes
- [Déployer un ordinateur virtuel avec une adresse IP statique public à l’aide du portail Azure](virtual-network-deploy-static-pip-arm-portal.md)
- [Déployer un ordinateur virtuel avec une adresse IP statique public à l’aide d’un modèle](virtual-network-deploy-static-pip-arm-template.md)
- [Déployer une machine virtuelle avec une adresse IP privée statique à l’aide du portail Azure](virtual-networks-static-private-ip-arm-pportal.md)
