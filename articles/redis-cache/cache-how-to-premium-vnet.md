<properties 
    pageTitle="Comment configurer la prise en charge de réseau virtuel pour un Cache de Redis Premium Azure | Microsoft Azure" 
    description="Découvrez comment créer et gérer des prise en charge de réseau virtuel pour vos instances de Cache Redis Azure niveau Premium" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016" 
    ms.author="sdanie"/>

# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-redis-cache"></a>Comment configurer la prise en charge réseau virtuel pour un Cache de Redis Premium Azure
Azure Cache Redis comporte des offres cache différent flexibilité lors du choix de la taille de cache et les fonctionnalités, y compris la nouvelle couche Premium.

Les fonctionnalités de niveau premium Azure Redis Cache incluent cluster, persistance et prise en charge réseau virtuel (VNet). Un VNet est un réseau privé dans le cloud. Lorsqu’une instance Azure Redis Cache est configurée avec un VNet, il n’est pas publiquement dédié et sont accessibles à partir de machines virtuelles et applications au sein de la VNet. Cet article décrit comment configurer la prise en charge de réseau virtuel pour une instance de Cache Redis Azure premium.

>[AZURE.NOTE] Azure Cache Redis prend en charge les deux classique et processeur VNets.

Pour plus d’informations sur d’autres fonctionnalités de cache premium, voir [Introduction à la couche Azure Redis Cache Premium](cache-premium-tier-intro.md).

## <a name="why-vnet"></a>Pourquoi VNet ?
Déploiement [d’Azure réseau virtuel (VNet)](https://azure.microsoft.com/services/virtual-network/) fournit une sécurité renforcée et niveau d’isolement pour Azure Redis vider le Cache, ainsi que sous-réseaux, stratégies de contrôle d’accès et autres fonctionnalités à limiter l’accès aux Azure Redis Cache.

## <a name="virtual-network-support"></a>Prise en charge réseau virtuel
Prise en charge réseau (VNet) virtuel est configuré sur la carte de **Nouveau Cache Redis** lors de la création du cache. 

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Une fois que vous avez sélectionné une prime tarifs couche, vous pouvez configurer l’intégration Azure Redis Cache VNet en sélectionnant une VNet qui se trouve dans le même abonnement et l’emplacement en tant que le cache de résolution. Pour utiliser un nouveau VNet, créez-le tout d’abord en suivant les étapes de [Création d’un réseau virtuel à l’aide du portail Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) ou [créer un réseau virtuel (classique) à l’aide du portail Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md) , puis revenez à la carte de **Nouveau Cache Redis** pour créer et configurer le cache premium.

Pour configurer le VNet pour votre nouveau cache, cliquez sur la carte de **Nouveau Cache Redis** **Réseau virtuel** , puis sélectionnez le VNet souhaité dans la liste déroulante.

![Réseau virtuel][redis-cache-vnet]

Sélectionnez le sous-réseau souhaité dans la liste déroulante **sous-réseau** , puis spécifiez l' **adresse IP statique**souhaitée. Si vous utilisez un VNet classique le champ **adresse IP statique** est facultatif, et si aucun n’est spécifié, le choix est à partir du sous-réseau sélectionné.

>[AZURE.IMPORTANT] Lorsque vous déployez un Cache Redis Azure à un VNet processeur, le cache doit être placé dans un sous-réseau dédié qui ne contient aucune autre ressource à l’exception des instances de Cache Redis Azure. Si une tentative est effectuée à déployer un Cache Redis Azure un VNet processeur à un sous-réseau qui contient d’autres ressources, le déploiement échoue.

![Réseau virtuel][redis-cache-vnet-ip]

>[AZURE.IMPORTANT] Les quatre premières adresses dans un sous-réseau sont réservés et ne peuvent pas être utilisés. Pour plus d’informations, voir [existe-t-il des restrictions sur l’utilisation des adresses IP au sein des sous-réseaux ?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Une fois que le cache est créé, vous pouvez afficher la configuration de la VNet en cliquant sur **Réseau virtuel** à partir de la carte de **paramètres** .

![Réseau virtuel][redis-cache-vnet-info]


Pour vous connecter à votre instance de cache Azure Redis lorsque vous utilisez un VNet, spécifiez le nom d’hôte du cache dans la chaîne de connexion, comme le montre l’exemple suivant.

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

## <a name="azure-redis-cache-vnet-faq"></a>Azure Redis Cache VNet Forum aux questions

La liste suivante contient des réponses aux questions fréquemment posées sur l’échelle de Cache Redis Azure.

-   [Quels sont certains problèmes courants une configuration incorrecte Azure Redis Cache et VNets ?](#what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets)
-   [Puis-je utiliser VNets avec un cache standard ou base ?](#can-i-use-vnets-with-a-standard-or-basic-cache)
-   [Pourquoi créer un cache Redis échoue dans certains sous-réseaux mais pas d’autres personnes ?](#why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others)
-   [Toutes les fonctionnalités de cache fonctionnent lors de l’hébergement d’un cache dans un VNET ?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)


## <a name="what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets"></a>Quels sont certains problèmes courants une configuration incorrecte Azure Redis Cache et VNets ?

Azure Redis Cache est hébergé dans un VNet, les ports dans le tableau suivant sont utilisés. Si ces ports sont bloqués, le cache peut ne pas fonctionner correctement. Possédant un ou plusieurs de ces ports bloqués est le problème de configuration incorrecte plus courantes lors de l’utilisation Azure Redis Cache dans un VNet.

| Ports     | Direction        | Protocole de transport | Objectif                                                                           | Adresse IP distante                           |
|-------------|------------------|--------------------|-----------------------------------------------------------------------------------|-------------------------------------|
| 80, 443     | Sortant         | TCP                | Redis dépendances sur Azure stockage/infrastructure de clé publique (Internet)                                | *                                   |
| 53          | Sortant         | TCP/UDP            | Redis dépendances sur DNS (Internet/VNet)                                         | *                                   |
| 6379, 6380  | Entrant          | TCP                | Communication avec les clients à Redis, l’équilibrage de charge Azure                               | VIRTUAL_NETWORK, AZURE_LOADBALANCER |
| 8443        | Entrant/sortant | TCP                | Détail d’implémentation pour Redis                                                   | VIRTUAL_NETWORK                     |
| 8500        | Entrant          | TCP/UDP            | Équilibrage de charge Azure                                                              | AZURE_LOADBALANCER                  |
| 10221 10231 | Entrant/sortant | TCP                | Détail d’implémentation pour Redis (peuvent restreindre le point de terminaison distant à VIRTUAL_NETWORK) | VIRTUAL_NETWORK, AZURE_LOADBALANCER |
| 13000 13999 | Entrant          | TCP                | Communication avec les clients à Redis Clusters, l’équilibrage de charge Azure                      | VIRTUAL_NETWORK, AZURE_LOADBALANCER |
| 15000 15999 | Entrant          | TCP                | Communication avec les clients à Redis Clusters, l’équilibrage de charge Azure                      | VIRTUAL_NETWORK, AZURE_LOADBALANCER |
| 16001       | Entrant          | TCP/UDP            | Équilibrage de charge Azure                                                              | AZURE_LOADBALANCER                  |
| 20226       | Entrant + sortant | TCP                | Détail d’implémentation des Clusters Redis                                          | VIRTUAL_NETWORK                     |


Il existe des besoins de connectivité réseau pour Azure Redis Cache qui ne peuvent pas être satisfaites initiale dans un réseau virtuel. Mise en Cache Redis Azure requiert tous les éléments suivants pour fonctionner correctement lorsqu’il est utilisé dans un réseau virtuel.

-  Connectivité réseau sortant aux points de terminaison du stockage Azure dans le monde. Cela inclut les points de terminaison situés dans la même région que l’instance Azure Redis Cache, ainsi que les points de terminaison de stockage situé dans **d’autres** régions Azure. Résoudre les points de terminaison de stockage Azure sous les domaines DNS suivants : *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net*et *file.core.windows.net*. 
-  Connectivité réseau sortant *ocsp.msocsp.com*, *mscrl.microsoft.com* et *crl.microsoft.com*. Cette connectivité est nécessaire pour prendre en charge la fonctionnalité SSL.
-  La configuration de DNS pour le réseau virtuel doit être capable de résoudre tous les points de terminaison et domaines mentionnées dans les points précédents. Ces exigences DNS peuvent être remplies en garantissant une infrastructure DNS valide est configurée et gérée pour le réseau virtuel.



### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Puis-je utiliser VNets avec un cache standard ou base ?

VNets peut uniquement être utilisé avec cache premium.

### <a name="why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others"></a>Pourquoi créer un cache Redis échoue dans certains sous-réseaux mais pas d’autres personnes ?

Si vous déployez un Cache Redis Azure à un VNet processeur, le cache doit être placé dans un sous-réseau dédié qui ne contient aucun autre type de ressource. Si une tentative est effectuée à déployer un Cache Redis Azure un sous-réseau processeur VNet qui contient d’autres ressources, le déploiement échoue. Vous devez supprimer les ressources existantes l’intérieur du sous réseau avant de pouvoir créer un cache Redis.

Vous pouvez déployer plusieurs types de ressources sur un VNet classique tant que vous disposez de suffisamment d’adresses IP disponibles.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Toutes les fonctionnalités de cache fonctionnent lors de l’hébergement d’un cache dans un VNET ?

Lorsque le cache fait partie d’un VNET, seuls les clients dans le VNET peuvent accéder le cache. Par conséquent, les fonctionnalités de gestion du cache suivantes ne fonctionnent pas pour le moment.

-   Redis Console -, car la Console Redis utilise le client redis cli.exe hébergé sur des ordinateurs virtuels qui ne font pas partie de votre VNET, il ne peut pas se connecter à votre cache.


## <a name="use-expressroute-with-azure-redis-cache"></a>Utilisent ExpressRoute avec Cache Redis Azure

Les clients peuvent connecter un circuit [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) pour leur infrastructure réseau virtuel, étendant ainsi leur réseau local vers Azure. 

Par défaut, un circuit ExpressRoute nouvellement créé publie un itinéraire par défaut qui permet la connectivité Internet sortante. Avec cette configuration, les applications clientes sont en mesure de vous connecter à d’autres points de terminaison Azure dont le Cache de Redis Azure.

Toutefois, une configuration client commune consiste à définir leur propre itinéraire par défaut (0.0.0.0/0) qui force le trafic Internet sortant de flux à la place en local. Ce flux de trafic sauts toujours de connectivité avec Azure Redis Cache, car le trafic sortant est bloqué localement ou NAT le feriez avec un ensemble d’adresses qui ne fonctionnent plus avec différents points de terminaison Azure reconnu.

La solution consiste à définir un (ou plusieurs) définies par l’utilisateur itinéraires (UDRs) sur le sous-réseau qui contient le Cache Redis Azure. Un UDR définit itinéraires spécifiques à un sous-réseau qui seront respectées au lieu de l’itinéraire par défaut.

Si possible, il est recommandé d’utiliser la configuration suivante :

- La configuration ExpressRoute annonce 0.0.0.0/0 et par défaut force tunnel tout le trafic sortant en local.
- La UDR appliqué au sous-réseau contenant le Cache Redis Azure définit 0.0.0.0/0 avec un type de saut suivant d’Internet (un exemple est plu bas dans cet article).

L’effet combiné de ces étapes est que le niveau de sous-réseau UDR est prioritaire ExpressRoute forcé tunnel, garantissant ainsi accéder à Internet à partir du Cache Redis Azure.

Bien que la connexion à une instance de Cache Redis Azure à partir d’un local application à l’aide de ExpressRoute n’est pas un scénario d’utilisation classique en raison des raisons de performances (pour optimiser les performances Azure Redis Cache les clients doivent être dans la même région comme Azure Redis Cache), dans ce scénario, que le chemin d’accès réseau sortant ne peut pas traversent les proxys d’entreprise internes, ni qu’il peut être force en tunnel vers local. Cela modifie l’adresse NAT efficace du trafic réseau sortant à partir du Cache Redis Azure. Modification de l’adresse NAT d’un Cache Redis Azure le trafic réseau l’instance entraîne problèmes de connectivité à la plupart des points de terminaison répertorié ci-dessus. Cela entraîne la création de Cache Redis Azure échecs.

**IMPORTANT :**  Les itinéraires définis dans une UDR **doit** être suffisamment prioritaire sur tous les itinéraires publiés par la configuration ExpressRoute. L’exemple suivant utilise la plage d’adresses 0.0.0.0/0 large et en tant que tel peut potentiellement être accidentellement remplacée par les annonces de routage à l’aide de plages d’adresses plus spécifiques.

**Très IMPORTANT :**  Azure Cache Redis n’est pas pris en charge avec des configurations ExpressRoute ce **incorrectement entre-annoncer itinéraires à partir du chemin homologation public le chemin d’accès homologation privé**. Configurations ExpressRoute ayant public peering configuré, recevront les annonces de routage de Microsoft pour un large éventail de Microsoft Azure plages d’adresses. Si ces plages d’adresses sont croisées incorrectement publiés sur le chemin d’accès homologation privé, le résultat final est que tous les paquets réseau sortant sous-réseau l’instance Azure Redis Cache sont acheminés incorrectement forcer à l’infrastructure réseau local du client. Ce flux réseau sauts Azure Redis Cache. La solution à ce problème consiste à arrêter itinéraires entre publicitaires du chemin d’accès homologation public vers le chemin d’accès homologation privé.

Informations générales sur itinéraires définies par l’utilisateur sont disponibles dans cette [vue d’ensemble](../virtual-network/virtual-networks-udr-overview.md). 

Pour plus d’informations sur ExpressRoute, voir [présentation technique ExpressRoute](../expressroute/expressroute-introduction.md)

## <a name="next-steps"></a>Étapes suivantes
Découvrez comment utiliser d’autres fonctionnalités de cache premium.

-   [Introduction à la couche Azure Redis Cache Premium](cache-premium-tier-intro.md)





  
<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

