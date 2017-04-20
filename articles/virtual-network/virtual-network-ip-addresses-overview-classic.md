<properties
   pageTitle="Publiques et privées IP adressage (classique) dans Azure | Microsoft Azure"
   description="En savoir plus sur publiques et privées adressage IP dans Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/11/2016"
   ms.author="jdial" />

# <a name="ip-addresses-classic-in-azure"></a>Adresses IP (classique) dans Azure
Vous pouvez affecter des adresses IP aux ressources Azure pour communiquer avec d’autres ressources Azure, votre réseau local et Internet. Il existe deux types d’adresses IP que vous pouvez utiliser dans Azure : publiques et privées.

Adresses IP publiques sont utilisées pour la communication avec Internet, y compris les services au public Azure.

Adresses IP privées sont utilisées pour les communications dans un réseau virtuel Azure (VNet), un service cloud et sur votre réseau local lorsque vous utilisez une passerelle VPN ou circuit ExpressRoute pour étendre votre réseau à Azure.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Découvrez comment [effectuer ces étapes en utilisant le modèle de déploiement du Gestionnaire de ressources](virtual-network-ip-addresses-overview-arm.md).

## <a name="public-ip-addresses"></a>Adresses IP publiques
Adresses IP publiques autoriser les ressources Azure communiquer avec les services Internet et Azure not tels que [stockage Azure](../storage/storage-introduction.md), [bases de données SQL](../sql-database/sql-database-technical-overview.md), [Azure Redis Cache](https://azure.microsoft.com/services/cache/)et [Azure événement Hubs](https://azure.microsoft.com/services/event-hubs/).

Une adresse IP publique est associé à des types de ressources suivants :

- Services en nuage
- Machines virtuelles IaaS (machines virtuelles)
- Instances de rôle PaaS
- Passerelles VPN
- Passerelles d’application

### <a name="allocation-method"></a>Méthode de répartition
Lorsqu’une adresse IP publique doit être affectée à une ressource Azure, il est *dynamiquement* allouées à partir d’un pool d’adresses IP public disponibles dans l’emplacement de que la ressource est créée. Cette adresse IP est publiée lorsque la ressource est arrêtée. En cas d’un service cloud, cela se produit lorsque toutes les instances de rôle sont arrêtés, qui peut être évité en utilisant une adresse IP *statique* (réservée) (voir [Services Cloud](#Cloud-services)).

>[AZURE.NOTE] La liste des plages d’adresses IP à partir de laquelle les adresses IP publiques sont affectés à des ressources Azure est publiée en [plages d’adresses IP Azure centre de données](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="dns-hostname-resolution"></a>Résolution du nom d’hôte DNS
Lorsque vous créez un service cloud ou un IaaS VM, vous devez fournir un nom DNS service cloud qui est unique pour toutes les ressources dans Azure. Cela crée un mappage dans les serveurs DNS géré Azure pour *NomDNS*. cloudapp.net vers l’adresse IP de la ressource. Par exemple, lorsque vous créez un service cloud avec un nom DNS service cloud de **contoso**, le nom (FQDN) de domaine complet **contoso.cloudapp.net** résoudra vers une adresse IP (VIP) publique du service cloud. Vous pouvez utiliser ce nom de domaine complet pour créer un enregistrement CNAME de domaine personnalisé en pointant sur l’adresse IP publique dans Azure.

### <a name="cloud-services"></a>Services en nuage
Un service cloud comporte toujours une adresse IP publique appelée une adresse IP virtuelle (VIP). Vous pouvez créer des points de terminaison dans un service cloud pour associer des ports différents dans l’adresse VIP pour les ports internes des machines virtuelles et des instances de rôles au sein du service cloud. 

Un service cloud peut contenir plusieurs machines virtuelles IaaS ou instances de rôle PaaS, tout exposées via la même adresse VIP cloud service. Vous pouvez également attribuer [plusieurs VIP sur un service cloud](../load-balancer/load-balancer-multivip.md), qui permet de scénarios multi-VIP comme environnement client multiples avec des sites Web SSL.

Vous pouvez vous assurer que l’adresse IP d’un service cloud reste la même, même lorsque toutes les instances de rôle sont arrêtés, à l’aide d’une adresse IP publique *statique* , appelée [IP réservée](virtual-networks-reserved-public-ip.md). Vous pouvez créer une ressource IP statique (réservée) dans un emplacement spécifique et affectez-le à n’importe quel service de nuage à cet emplacement. Vous ne pouvez pas spécifier l’adresse IP réelle pour les adresses IP réservées, il est attribué à partir du pool d’adresses IP disponibles dans l’emplacement de que sa création. Cette adresse IP n’est pas publiée jusqu'à ce que vous le supprimiez explicitement.

Statique des adresses IP publiques (réservées) sont fréquemment utilisées dans les situations dans lesquelles un service cloud :

- nécessite des règles de pare-feu pour être configuré par les utilisateurs finaux.
- dépend de résolution de noms DNS externe, et une adresse IP dynamique verser mise à jour des enregistrements A.
- consomme des services web externe qui utilisent le modèle de sécurité basé sur IP.
- utilise les certificats SSL liés à une adresse IP.

>[AZURE.NOTE] Lorsque vous créez une machine virtuelle classique, un conteneur *service cloud* est créé par Azure, qui comporte une adresse IP virtuelle (VIP). Lorsque la création s’effectue via le portail, un par défaut RDP ou SSH *point de terminaison* est configuré par le portail pour pouvoir vous connecter à la machine virtuelle via le service de nuage VIP publique. Cette VIP service cloud peut être réservé, qui fournit une adresse IP réservée pour vous connecter à la machine virtuelle. Vous pouvez ouvrir les ports supplémentaires en configurant plusieurs points de terminaison.

### <a name="iaas-vms-and-paas-role-instances"></a>Machines virtuelles IaaS et PaaS instances de rôles
Vous pouvez affecter une adresse IP publique directement à un [ordinateur virtuel](../virtual-machines/virtual-machines-linux-about.md) IaaS ou une instance de rôle PaaS au sein d’un service cloud. Ceci est appelé une adresse IP publique au niveau de l’instance ([ILPIP](virtual-networks-instance-level-public-ip.md)). Cette adresse IP publique peut uniquement être dynamique.

>[AZURE.NOTE] Ceci est différent de la VIP publique du service cloud, qui est un conteneur pour machines virtuelles IaaS ou PaaS instances de rôle, dans la mesure où un service cloud peut contenir plusieurs machines virtuelles IaaS ou instances de rôle PaaS, tout exposés via la même adresse VIP cloud service.

### <a name="vpn-gateways"></a>Passerelles VPN
Une [passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) peut être utilisé pour connecter un VNet Azure vers d’autres VNets Azure ou les réseaux locaux. Une passerelle VPN est affectée à un public IP adresse *dynamiquement*, qui permet de communication avec le réseau à distance.

### <a name="application-gateways"></a>Passerelles d’application
Une [passerelle d’Application](../application-gateway/application-gateway-introduction.md) Azure peut être utilisée pour Layer7 équilibrage de charge rediriger le trafic réseau basé sur HTTP. Passerelle d’application est affecté un public IP adresse *dynamiquement*, qui sert de l’adresse IP virtuelle équilibrage de charge.

### <a name="at-a-glance"></a>En un clin de œil
Le tableau ci-dessous indique chaque type de ressource avec les méthodes d’allocation possible (dynamique/statique) et permet d’attribuer plusieurs adresses IP publiques.

|Ressource|Dynamique|Statique|Plusieurs adresses IP|
|---|---|---|---|
|Service de nuage|Oui|Oui|Oui|
|IaaS VM ou PaaS instance de rôle|Oui|N°|N°|
|Passerelle VPN|Oui|N°|N°|
|Passerelle d’application|Oui|N°|N°|

## <a name="private-ip-addresses"></a>Adresses IP privées
Adresses IP privées autorisent les ressources Azure à communiquer avec d’autres ressources dans un service cloud ou un [réseau virtuel](virtual-networks-overview.md)(VNet), ou au réseau local (via une passerelle VPN ou circuit ExpressRoute), sans l’aide d’une adresse IP Internet accessible.

Dans le modèle de déploiement d’Azure classique, une adresse IP privée peut être affectée aux ressources Azure suivantes :

- Machines virtuelles IaaS et PaaS instances de rôles
- Équilibrage de charge interne
- Passerelle d’application

### <a name="iaas-vms-and-paas-role-instances"></a>Machines virtuelles IaaS et PaaS instances de rôles
Machines virtuelles (machines virtuelles) créées avec le modèle de déploiement classique sont toujours placés dans un service cloud, similaire aux instances de rôle PaaS. Le comportement d’adresses IP privées sont donc similaires pour ces ressources.

Il est important de noter qu’un service cloud peut être déployé de deux façons :

- Comme un service cloud *autonome* , où il n’est pas au sein d’un réseau virtuel.
- Dans le cadre d’un réseau virtuel.

#### <a name="allocation-method"></a>Méthode de répartition
Dans le cas d’un service cloud *autonome* , ressources obtenir un privé IP adresse alloué *dynamiquement* à partir de la plage d’adresses IP privées centre de données Azure. Il peut être utilisé uniquement pour communiquer avec d’autres ordinateurs virtuels au sein du même service cloud. Cette adresse IP peut changer lorsque la ressource est arrêtée et démarrée.

Dans le cas d’un service cloud déployé au sein d’un réseau virtuel, ressources obtenir privé ou les adresses IP allouées à partir de la plage d’adresses de la sous-réseau associés (comme indiqué dans sa configuration réseau). Cette ou les adresses IP privé peut être utilisé pour les communications entre tous les ordinateurs virtuels au sein de la VNet.

En outre, en cas de services en nuage au sein d’un VNet, une adresse IP privée allouée *dynamiquement* (à l’aide de DHCP) par défaut. Il peut changer lorsque la ressource est arrêtée et démarrée. Pour vous assurer que l’adresse IP reste la même, vous devez définir la méthode d’allocation *statique*, fournir une adresse IP au sein de la plage d’adresses correspondant.

Adresses IP privées statiques soient fréquemment utilisés pour :

 - Machines virtuelles agissent comme domaine ou des serveurs DNS.
 - Machines virtuelles nécessitant des règles de pare-feu à l’aide d’adresses IP.
 - Machines virtuelles exécutant les services accédés par les autres applications via une adresse IP.

#### <a name="internal-dns-hostname-resolution"></a>Résolution de nom d’hôte DNS interne
Toutes les machines virtuelles Azure et instances de rôle PaaS sont configurés avec les [serveurs DNS géré Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) par défaut, sauf si vous configurez explicitement serveurs DNS personnalisés. Ces serveurs DNS assurer la résolution interne machines virtuelles et des instances de rôle qui résident dans le même service VNet ou sur le cloud.

Lorsque vous créez une machine virtuelle, un mappage pour le nom d’hôte à son adresse IP privée est ajouté aux serveurs DNS géré Azure. Dans le cas d’un ordinateur virtuel plusieurs cartes, le nom d’hôte est mappé à l’adresse IP privée de la carte principale. Toutefois, ces informations de mappage sont limitées aux ressources au sein du même service de nuage ou VNet.

Dans le cas d’un service cloud *autonome* , vous serez en mesure de résoudre les noms d’hôtes de toutes les instances de machines virtuelles/rôle dans le même service cloud. Dans le cas d’un service cloud au sein d’un VNet, vous serez en mesure de résoudre les noms d’hôtes de toutes les instances de machines virtuelles/rôle dans la VNet.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Programmes d’équilibrage de charge interne (ILB) et passerelles d’Application
Vous pouvez affecter une adresse IP privée à la configuration du **serveur frontal** d’un [Programme d’équilibrage de charge interne Azure](../load-balancer/load-balancer-internal-overview.md) (ILB) ou d’une [Passerelle d’Application Azure](../application-gateway/application-gateway-introduction.md). Cette adresse IP privée sert à un point final interne, accessible uniquement aux ressources dans son réseau virtuel (VNet) et les réseaux distants connectés à la VNet. Vous pouvez affecter soit une adresse IP statique ou dynamique privée à la configuration du serveur frontal. Vous pouvez également affecter plusieurs adresses IP privées multi-vip scénarios.

### <a name="at-a-glance"></a>En un clin de œil
Le tableau ci-dessous indique chaque type de ressource avec les méthodes d’allocation possible (dynamique/statique) et permet d’attribuer plusieurs adresses IP privées.

|Ressource|Dynamique|Statique|Plusieurs adresses IP|
|---|---|---|---|
|Machine virtuelle (dans un service cloud *autonome* )|Oui|Oui|Oui|
|Instance de rôle PaaS (dans un service cloud *autonome* )|Oui|N°|Oui|
|Machine virtuelle ou PaaS instance de rôle (dans un VNet)|Oui|Oui|Oui|
|Frontal équilibrage de charge interne|Oui|Oui|Oui|
|Serveur frontal de la passerelle application|Oui|Oui|Oui|

## <a name="limits"></a>Limites

Le tableau ci-dessous indique les limites imposées IP adressage dans Azure par abonnement. Vous pouvez [contacter le support technique](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour augmenter les limites par défaut jusqu'à atteindre les limites maximales selon vos besoins professionnels.

||Limite par défaut|Limite maximale|
|---|---|---|
|Adresses IP publiques (dynamique)|5|contacter le support technique|
|Adresses IP publiques réservées|20|contacter le support technique|
|VIP publique par déploiement (service cloud)|5|contacter le support technique|
|Privé VIP (ILB) par déploiement (service cloud)|1|1|

Vérifiez que vous lisez l’ensemble des [limites de mise en réseau](azure-subscription-service-limits.md#networking-limits) dans Azure.

## <a name="pricing"></a>Tarifs

Dans la plupart des cas, les adresses IP publiques sont gratuites. Est payante nominal à utiliser des adresses IP publiques supplémentaires et/ou statiques. Assurez-vous que vous comprenez la [tarification pour les adresses IP public](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="differences-between-resource-manager-and-classic-deployments"></a>Différences entre le Gestionnaire de ressources et les déploiements classiques
Voici une comparaison des fonctionnalités d’adressage IP dans le Gestionnaire de ressources et le modèle de déploiement classique.

||Ressource|Classique|Gestionnaire de ressources|
|---|---|---|---|
|**Adresse IP publique**|MACHINE VIRTUELLE|Appelées une ILPIP (dynamique uniquement)|Appelées une adresse IP publique (dynamique ou statique)|
|||Affecté à une VM IaaS ou une instance de rôle PaaS|Associé à la carte réseau de l’ordinateur|
||Équilibrage de charge qui fait face à Internet|Appelés VIP (dynamique) ou IP réservée (statique)|Appelées une adresse IP publique (dynamique ou statique)|
|||Affecté à un service cloud|Associé à la configuration de la frontal de l’équilibrage de charge|
||||
|**Adresse IP privée**|MACHINE VIRTUELLE|Souvent un fondu|Appelées une adresse IP privée|
|||Affecté à une VM IaaS ou une instance de rôle PaaS|Affecté à la carte réseau de l’ordinateur|
||Équilibrage de charge interne (ILB)|Affecté à la ILB (dynamique ou statique)|Affecté à la configuration du serveur frontal du ILB (statique ou dynamique)|

## <a name="next-steps"></a>Étapes suivantes
- [Déployer une machine virtuelle avec une adresse IP privée statique](virtual-networks-static-private-ip-classic-pportal.md) à l’aide du portail classique.
