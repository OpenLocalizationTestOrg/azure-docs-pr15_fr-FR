<properties
   pageTitle="Présentation de la sécurité réseau Azure | Microsoft Azure"
   description=" Cet article facilite la comprendre que Microsoft Azure a à offrir en matière de sécurité réseau. Nous fournissons des explications base sur les concepts de sécurité réseau et des exigences et des informations complémentaires sur qu’Azure a à offrir dans chacun de ces domaines. "
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/09/2016"
   ms.author="terrylan"/>

# <a name="azure-network-security-overview"></a>Présentation de la sécurité réseau Azure

Microsoft Azure inclut une infrastructure réseau robuste pour prendre en charge votre application et les exigences de connectivité de service. La connectivité réseau est possible entre les ressources situées dans Azure, entre en local et Azure hébergé ressources et vers et depuis Internet et Azure.

L’objectif de cet article consiste à rendre plus facile à comprendre que Microsoft Azure a à offrir en matière de sécurité réseau. Ici, nous fournissons des explications base sur les concepts de sécurité réseau et configuration requise. Nous également vous fournir des informations sur qu’Azure a à offrir dans chacun de ces domaines. Il existe comporte de nombreux liens vers d’autres contenus qui vous permettra de mieux comprendre pour les domaines qui vous intéressent.

Cet article de présentation de la sécurité réseau Azure sera centrée sur les points suivants :

- Mise en réseau Azure
- Contrôle d’accès réseau
- Connectivité à distance access et croisée locaux de banque d’informations sécurisé
- Disponibilité
- Journalisation
- Résolution de noms
- Architecture DMZ
- Centre de sécurité Azure

## <a name="azure-networking"></a>Mise en réseau Azure

Machines virtuelles besoin de la connectivité réseau. Pour prendre en charge cette exigence, Azure nécessite machines virtuelles être connecté à un réseau virtuel Azure. Un réseau virtuel Azure est une construction logique intégrée en haut de la structure de réseau Azure physique. Chaque réseau virtuel Azure logique est isolé de tous les autres réseaux virtuels Azure. Cela permet de s’assurer que le trafic réseau dans vos déploiements n’est pas accessible aux autres utilisateurs de Microsoft Azure.

Pour en savoir plus :

- [Vue d’ensemble du réseau virtuel](../virtual-network/virtual-networks-overview.md)

## <a name="network-access-control"></a>Contrôle d’accès réseau
Contrôle d’accès réseau consiste à limiter la connectivité vers et depuis des appareils spécifiques ou sous-réseaux au sein d’un réseau virtuel Azure. L’objectif de contrôle d’accès réseau est pour vous assurer que vos machines virtuelles et services sont accessibles aux utilisateurs et aux appareils vers laquelle vous souhaitez les accessible uniquement. Accéder aux contrôles basés sur Autoriser ou refuser des décisions de connexions vers et depuis votre ordinateur virtuel ou le service.

Azure prend en charge plusieurs types de contrôle d’accès réseau. Parmi lesquels :

- Contrôle de la couche réseau
- Router contrôle et forcé tunnel
- Appareils de sécurité réseau virtuel

### <a name="network-layer-control"></a>Contrôle de la couche réseau
Un déploiement sécurisé nécessite une mesure de contrôle d’accès réseau. L’objectif de contrôle d’accès réseau est pour vous assurer que vos machines virtuelles et les services de réseau qui s’exécutent sur ces machines virtuelles peuvent communiquer uniquement avec d’autres périphériques en réseau que dont elles ont besoin communiquer avec et toutes les autres tentatives de connexion sont bloqués.

Si vous avez besoin de contrôle d’accès au niveau de réseau simple (basé sur l’adresse IP et les protocoles TCP ou UDP), vous pouvez utiliser des groupes de sécurité réseau. Un groupe de sécurité réseau (NSG) est un pare-feu de filtrage de base de paquets avec état et vous permet de contrôler l’accès basé sur un [uplet à 5](https://www.techopedia.com/definition/28190/5-tuple). NSGs ne fournissent pas d’inspection de la couche application ou authentifié accéder aux contrôles.

Pour en savoir plus :

- [Groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md)

### <a name="route-control-and-forced-tunneling"></a>Contrôle de l’itinéraire et tunnel forcé
La possibilité de contrôler le comportement de routage sur vos réseaux virtuel Azure est une fonctionnalité de contrôle de sécurité et d’accès réseau critiques. Si le routage est configuré correctement, applications et services hébergés sur votre ordinateur virtuel peuvent se connecter aux périphériques que vous ne voulez pas pour vous connecter à, y compris les périphériques appartenant et exploité par les pirates potentiels.

Mise en réseau Azure prend en charge la possibilité de personnaliser le comportement de routage pour le trafic réseau de vos réseaux virtuel Azure. Cela vous permet de modifier les entrées du tableau routage par défaut de votre réseau virtuel Azure. Contrôle du comportement de routage vous permet de s’assurer que tout le trafic à partir d’un appareil ou d’un groupe de périphériques certaines entre ou quitte votre réseau virtuel Azure via un emplacement spécifique.

Par exemple, vous devrez un dispositif de sécurité réseau virtuel sur votre réseau virtuel Azure. Vous souhaitez vous assurer que tout le trafic vers et depuis votre réseau virtuel Azure parcourt ce dispositif de sécurité virtuel. Vous pouvez le faire en configurant des [Itinéraires définies par l’utilisateur](../virtual-network/virtual-networks-udr-overview.md) dans Azure.

[Forcé tunnel](https://www.petri.com/azure-forced-tunneling) est un mécanisme que vous pouvez utiliser pour vous assurer que vos services ne sont pas autorisés pour établir une connexion aux périphériques sur Internet. Notez qu’il s’agit différente des accepter les connexions entrantes et la façon d’y répondre. Serveurs web frontaux doivent répondre à la demande à partir d’hôtes Internet, et donc provenant de Internet le trafic est autorisé entrante sur ces serveurs web et les serveurs web sont autorisés à répondre.

Ce que vous ne voulez pas autoriser est un serveur web frontaux pour demander un sortant. Ces demandes peuvent représenter un risque de sécurité, car ces connexions peuvent être utilisées pour télécharger des logiciels malveillants. Même si vous ne voulez pas que ces serveurs frontaux pour lancer des requêtes sortantes vers Internet, vous souhaiterez peut-être la forcez traitée votre proxys web local de sorte que vous pouvez tirer parti des URL filtrage et la journalisation.

À la place, vous pouvez utiliser tunnel forcé pour éviter ce problème. Lorsque vous activez tunnel forcé, toutes les connexions à Internet sont forcées via votre passerelle locale. Vous pouvez configurer tunnel forcé en tirant parti des itinéraires définies par l’utilisateur.

Pour en savoir plus :

- [À quoi correspondent itinéraires définies par l’utilisateur et transfert IP](../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Réseau virtuel sécurité appareils
Bien que les groupes de sécurité réseau, les gammes de définies par l’utilisateur et tunnel forcé offrent un niveau de sécurité au niveau des couches réseau et transport du [modèle OSI](https://en.wikipedia.org/wiki/OSI_model), il peut arriver lorsque vous souhaitez activer la sécurité au niveau supérieur dans le réseau.

Par exemple, vos exigences de sécurité peuvent inclure :

- L’authentification et l’autorisation avant permettant d’accéder à votre application
- Détection et réponse intrusion
- Inspection de la couche application pour les protocoles de haut niveau
- Filtrage d’URL
- Contre les logiciels malveillants et antivirus au niveau du réseau
- Protection contre robot
- Contrôle d’accès application
- Protection DDoS supplémentaire (au-dessus de la DDoS protection fournie le tissu Azure lui-même)

Vous pouvez accéder à ces fonctionnalités de sécurité réseau améliorée à l’aide d’une solution partenaire Azure. Vous pouvez trouver des solutions de sécurité réseau le partenaire Azure plus récent en visitant la [Azure Marketplace](https://azure.microsoft.com/marketplace/) et recherchez « sécurité » et « sécurité du réseau ».

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Accès à distance sécurisé et locaux croisée connectivité
Le programme d’installation, la configuration et gestion de vos besoins en ressources Azure être effectuées à distance. En outre, vous souhaiterez peut-être déployer des solutions [informatiques hybrides](http://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) ayant des composants en local et dans le nuage public Azure. Ces scénarios nécessitent un accès à distance sécurisé.

Mise en réseau Azure prend en charge les scénarios d’accès à distance sécurisé suivants :

- Connecter les postes de travail individuels à un réseau virtuel Azure
- Connecter votre réseau local à un réseau virtuel Azure un réseau VPN
- Connecter votre réseau local à un réseau virtuel Azure avec une liaison réseau étendue dédiée
- Connecter les réseaux virtuels Azure entre eux

### <a name="connect-individual-workstations-to-an-azure-virtual-network"></a>Connecter les postes de travail individuels à un réseau virtuel Azure
Il peut arriver lorsque vous souhaitez activer individuel personnel les développeurs ou les opérations gérer les machines virtuelles et services dans Azure. Par exemple, vous devez accéder à une machine virtuelle sur un réseau virtuel Azure et votre stratégie de sécurité n’autorise pas l’accès à distance RDP ou SSH pour machines virtuelles. Dans ce cas, vous pouvez utiliser une connexion VPN point-à-site.

La connexion VPN point-à-site utilise le protocole [VPN SSTP](https://technet.microsoft.com/library/cc731352.aspx) pour vous permettent de configurer une connexion privée et sécurisée entre l’utilisateur et le réseau virtuel Azure. Une fois la connexion VPN est établie, l’utilisateur pourrez RDP ou SSH sur la liaison VPN dans n’importe quel ordinateur virtuel sur le réseau virtuel Azure (en supposant que l’utilisateur peut s’authentifier et est autorisé).

Pour en savoir plus :

- [Configurer une connexion Point-à-Site à un réseau virtuel à l’aide de PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-an-azure-virtual-network-with-a-vpn"></a>Connecter votre réseau local à un réseau virtuel Azure un réseau VPN
Vous souhaiterez peut-être vous connecter votre réseau d’entreprise ou des parties de celle-ci, à un réseau virtuel Azure. Il s’agit courante dans informatiques hybrides scénarios où sociétés [étendre leur centre de données locales dans Azure](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84). Dans de nombreux cas sociétés héberge parties d’un service Azure et composants en local, par exemple quand une solution inclut des serveurs web frontaux dans Azure et bases de données principale en local. Ces types de connexions « croisées locaux » également facilitent la gestion des Azure situé ressources plus sécurisé et activer des scénarios tels que l’extension de domaine Active Directory dans Azure.

Une façon d’y parvenir consiste à utiliser un [site à VPN](https://www.techopedia.com/definition/30747/site-to-site-vpn). La différence entre un réseau privé virtuel de site à et un réseau privé virtuel point-à-site est qu’un réseau privé virtuel point-à-site connecte un appareil unique à un réseau virtuel Azure, pendant un réseau privé virtuel de site à connecte à un réseau entier (par exemple, votre réseau local) à un réseau virtuel Azure. VPN de site à un réseau virtuel Azure utilisent le mode de tunnel IPsec protocole VPN hautement sécurisé.

Pour en savoir plus :

- [Créer un gestionnaire de ressources VNet avec une connexion VPN site à l’aide du portail Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Planification et la conception pour la passerelle VPN](../vpn-gateway/vpn-gateway-plan-design.md)

### <a name="connect-your-on-premises-network-to-an-azure-virtual-network-with-a-dedicated-wan-link"></a>Connecter votre réseau local à un réseau virtuel Azure avec une liaison réseau étendu dédiée
Connexions point-à-site et de site à VPN sont effectives pour activation de la connectivité entre locaux. Toutefois, certaines organisations estiment qu’elles les inconvénients suivants :

- Connexions VPN déplacement de données via Internet – cela expose ces connexions aux problèmes potentiels de sécurité liés aux déplacements de données sur un réseau public. En outre, la fiabilité et la disponibilité de connexions Internet ne peut pas être garanties.
- Connexions VPN aux réseaux virtuels Azure peuvent être considérées comme limitées par la bande passante pour certaines applications et fins, telles qu’elles max absence en autour de 200Mbps.

Organisations qui doivent généralement le niveau de sécurité et la disponibilité de leurs connexions entre locaux utilisent liaisons WAN dédiées pour vous connecter à des sites distants. Azure vous offre la possibilité d’utiliser une liaison réseau étendue dédiée que vous pouvez utiliser pour connecter votre réseau local à un réseau virtuel Azure. Cette option est activée par le biais Azure ExpressRoute.

Pour en savoir plus :

- [Présentation technique ExpressRoute](../expressroute/expressroute-introduction.md)

### <a name="connect-azure-virtual-networks-to-each-other"></a>Connecter les réseaux virtuels Azure entre eux
Il est possible d’utiliser plusieurs réseaux virtuels Azure pour vos déploiements. Il existe de nombreuses raisons pourquoi vous pouvez le faire. Une des raisons peut-être être pour simplifier la gestion ; un autre peut-être pour des raisons de sécurité. Quelle que soit la motivation ou justification pour utiliser des ressources sur différents réseaux virtuels Azure, il peut arriver lorsque vous souhaitez que les ressources sur chacun des réseaux pour communiquer avec eux.

Une seule option serait pour les services sur un réseau virtuel Azure se connecter aux services sur un autre réseau virtuel Azure par « en boucle arrière » via Internet. La connexion serait démarrer sur un réseau virtuel Azure, accédez via Internet et puis revenez à la destination du réseau virtuel Azure. Cette option expose la connexion pour les problèmes de sécurité intégrées à toutes les communications basées sur Internet.

Une meilleure option consiste à créer un virtuel Azure réseau-Azure virtuelle réseau VPN site à. Cette virtuel Azure réseau-Azure virtuelle réseau VPN site à utilise le protocole de [mode de tunnel IPsec](https://technet.microsoft.com/library/cc786385.aspx) même en tant que la connexion VPN de site à entre sites mentionnée ci-dessus.

L’avantage de l’utilisation de virtuel Azure réseau-Azure virtuelle réseau VPN site à est que la connexion VPN est établie via le tissu Azure réseau ; Il ne se connecte pas via Internet. Cela vous offre un niveau supplémentaire de sécurité par rapport aux VPN de site à qui se connectent via Internet.

Pour en savoir plus :

- [Configurer une connexion VNet à VNet en utilisant le Gestionnaire de ressources Azure et PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## <a name="availability"></a>Disponibilité
Disponibilité est un composant clé de n’importe quel programme de sécurité. Si vos utilisateurs et les systèmes ne peuvent pas accéder à ce qu’ils doivent accéder sur le réseau, le service peut être considéré comme compromis. Azure comporte les technologies de réseau qui prennent en charge les mécanismes de disponibilité suivants :

- Équilibrage de charge basé sur HTTP
- Équilibrage de charge niveau réseau
- Équilibrage de charge global

Équilibrage de charge est un mécanisme destiné à distribuer également des connexions entre plusieurs appareils. Les objectifs de l’équilibrage de charge sont :

- Augmenter la disponibilité – lorsque vous chargez équilibrer les connexions sur plusieurs périphériques, un ou plusieurs des périphériques peuvent devenir indisponible et les services en cours d’exécution sur les périphériques en ligne restants peuvent continuer traiter le contenu à partir du service
- Améliorer les performances : lorsque vous chargez équilibrer les connexions sur plusieurs périphériques, un seul périphérique n’a pas de prendre l’atteint processeur. En revanche, les demandes de traitement et la mémoire pour traiter le contenu sont réparties sur plusieurs appareils.

### <a name="http-based-load-balancing"></a>Équilibrage de charge basé sur HTTP
Organisations qui exécutent les services sur le web souvent souhaitent ont un équilibrage de charge basé sur HTTP devant garantir un niveau adéquat de performances et la disponibilité de ces services web. Contrairement à équilibrage de charge basée sur réseau traditionnel l’équilibrage de charge décisions basé sur HTTP programmes d’équilibrage de charge sont basées sur les caractéristiques du protocole HTTP, pas sur les protocoles de couche réseau et transport.

Pour vous permettre de basés sur HTTP équilibrage de charge pour vos services sur le web, Azure fournit la passerelle d’Application Azure. La passerelle d’Application Azure prend en charge :

- Basé sur HTTP équilibrage de charge – décisions équilibrage de charge sont effectuées sur base caractéristique spéciales pour le protocole HTTP
- Affinité session basée sur les cookies – cette fonctionnalité permet de s’assure que les connexions établies à un des serveurs derrière ce équilibrage de charge reste intacte entre le client et le serveur. Cela garantit stabilité des transactions.
- Déchargement SSL – lorsqu’une connexion de client est établie avec l’équilibrage de charge, que session entre le client et l’équilibrage de charge est chiffrée à l’aide de la HTTPS (SSL /) protocole. Toutefois, afin d’améliorer les performances, vous avez la possibilité d’avoir la connexion entre l’équilibrage de charge et le serveur web derrière l’utilisation d’équilibrage de charge le protocole HTTP (non chiffré). Ceci est appelé « Décharger SSL », car les serveurs web derrière l’équilibrage de charge ne rencontrer la surcharge du processeur figurant dans le chiffrement et doivent donc être en mesure de demandes de service plus rapidement.
- Basée sur URL acheminement du contenu : cette fonctionnalité permet à l’équilibrage de charge prendre des décisions sur où transférer des connexions en fonction de l’URL cible. Cela fournit une plus grande flexibilité que les solutions qui vous chargez équilibrage de charge des décisions fondées sur des adresses IP.

Pour en savoir plus :

- [Vue d’ensemble de la passerelle application](../application-gateway/application-gateway-introduction.md)

### <a name="network-level-load-balancing"></a>Équilibrage de charge niveau réseau
Contrairement à l’équilibrage de charge basé sur HTTP équilibrage de charge niveau décisions charge équilibrage de charge basée sur les adresses IP adresse et le port (TCP ou UDP) numéros.
Vous pouvez bénéficier des avantages réseau niveau d’équilibrage de charge dans Azure à l’aide de l’équilibrage de charge Azure. Certaines caractéristiques clés de l’équilibrage de charge Azure sont les suivantes :

- Équilibrage de charge niveau basée sur les numéros d’adresse et le port IP
- Prise en charge de n’importe quel protocole de couche d’application
- Machines virtuelles Azure l’équilibrage et instances de rôles des services cloud
- Peut être utilisée pour Internet public (équilibrage de charge externe) et non Internet machines virtuelles et les applications (équilibrage de charge interne)
- Point de terminaison de surveillance, qui est utilisée pour déterminer si un des services derrière l’équilibrage de charge soient plus disponible

Pour en savoir plus :

- [Équilibrage de charge Internet Facing entre plusieurs Machines virtuelles ou services](../load-balancer/load-balancer-internet-overview.md)
- [Vue d’ensemble des équilibrage de charge interne](../load-balancer/load-balancer-internal-overview.md)

### <a name="global-load-balancing"></a>Équilibrage de charge global
Certaines organisations souhaiteront le plus haut niveau de disponibilité possible. Un moyen pour atteindre l’objectif consiste à héberger des applications dans globalement distribué centres de données. Lorsqu’une application est hébergée dans des centres de données situés dans le monde entier, il est possible pour un ensemble de la région géopolitique ne sont plus disponibles et disposer toujours de l’application vers le haut et en cours d’exécution.

Outre les avantages de la disponibilité que pouvez-vous en hébergeant des applications dans globalement distribué centres de données, vous pouvez également obtenir des avantages de performance. Les avantages de performances peuvent être obtenus à l’aide d’un mécanisme qui achemine des demandes de service pour le centre de données est plus proche de l’appareil qui effectue la demande.

Équilibrage de charge global peut vous fournir de ces avantages. Dans Azure, vous pouvez bénéficier d’équilibrage global à l’aide du Gestionnaire de trafic Azure.

Pour en savoir plus :

- [Quel est le trafic Manager ?](../traffic-manager/traffic-manager-overview.md)

## <a name="logging"></a>Journalisation
La journalisation à un niveau de réseau est une fonctionnalité de clé dans un scénario de sécurité réseau. Dans Azure, vous pouvez enregistrer les informations obtenues pour les groupes de sécurité réseau obtenir le niveau du réseau enregistrement des informations. Avec l’enregistrement NSG, vous obtenez des informations à partir de :

- Journaux d’audit – ces journaux sont utilisées pour afficher toutes les opérations soumises à vos abonnements Azure. Ces fichiers journaux est activées par défaut et peut être utilisés dans le portail Azure.
- Les journaux d’événements – ces journaux fournit des informations sur les règles NSG ont été appliqués.
- Les journaux de compteur – ces journaux vous permettre de savoir combien de fois chaque règle NSG a été appliquée pour refuser ou autoriser le trafic.

Vous pouvez également utiliser [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/), un outil de visualisation puissantes, pour afficher et analyser ces fichiers journaux.

Pour en savoir plus :

- [Journal Analytique pour les groupes de sécurité réseau (NSGs)](../virtual-network/virtual-network-nsg-manage-log.md)

## <a name="name-resolution"></a>Résolution de noms
Résolution de noms est une fonction critique pour tous les services que vous hébergez dans Azure. Du point de vue sécurité, compromis de la fonction de résolution de nom peut entraîner un intrus redirection des requêtes à partir de vos sites à un site. Résolution de noms sécurisé est une obligation pour tous vos services cloud hébergé.

Il existe deux types de résolution de noms que vous faudra éventuellement résoudre :

- Résolution de noms interne – interne est utilisée par les services de vos réseaux virtuel Azure, vos réseaux locaux ou les deux. Noms utilisés pour la résolution de noms internes ne sont pas accessibles via Internet. Pour une sécurité optimale, il est important que votre schéma de résolution de noms interne n’est pas accessible à des utilisateurs externes.
- Résolution de noms externes – externe est utilisée par les personnes et les périphériques en dehors de votre en local et les réseaux virtuels Azure. Voici les noms qui sont visibles sur Internet et sont utilisées pour diriger connexion à vos services sur le nuage.

Résolution de noms internes, vous avez deux possibilités :

- Un serveur DNS réseau virtuel Azure – lorsque vous créez un nouveau réseau virtuel Azure, un serveur DNS est créé pour vous. Ce serveur DNS peut résoudre les noms des ordinateurs situés sur ce réseau virtuel Azure. Ce serveur DNS n’est pas configurable et est géré par le Gestionnaire de tissu Azure, ce qui rend une solution de résolution du nom sécurisé.
- Mettre votre serveur DNS : vous avez la possibilité de placer un serveur DNS de votre choix sur votre réseau virtuel Azure. Ce serveur DNS peut être intégré à un Active Directory serveur DNS ou une solution de serveur DNS dédiée fourni par un partenaire Azure, que vous pouvez obtenir de l’Azure Marketplace.

Pour en savoir plus :

- [Vue d’ensemble du réseau virtuel](../virtual-network/virtual-networks-overview.md)
- [Gérer les serveurs DNS utilisés par un réseau virtuel (VNet)](../virtual-network/virtual-networks-manage-dns-in-vnet.md)

Pour la résolution DNS externe, vous avez deux possibilités :

- Héberger votre propre externes DNS server en local
- Héberger votre propre serveur DNS externe avec un fournisseur de services

Nombreuses grandes entreprises héberge leur propre DNS servers local. Pour cela, ils peuvent parce qu’ils ont l’expertise mise en réseau et la présence globale pour le faire.

Dans la plupart des cas, il est préférable d’héberger vos services de résolution de noms DNS avec un fournisseur de services. Ces fournisseurs de services ont la connaissance des réseaux et présence globale à garantir très haute disponibilité pour vos services de résolution de noms. Disponibilité est essentielle pour les services DNS, car si vos services de résolution de noms échouent, l’abri seront en mesure d’atteindre votre confronté à services Internet.

Azure vous donne un hautement disponible et performant une solution DNS externe sous la forme d’Azure DNS. Cette solution de résolution du nom externe tire parti de l’infrastructure DNS Azure dans le monde. Il vous permet d’héberger votre domaine dans Azure utilisant les mêmes informations d’identification, API, outils et à la facturation en tant que les autres services Azure. Dans le cadre d’Azure, il hérite également les contrôles de sécurité renforcée intégrées à la plate-forme.

Pour en savoir plus :

- [Vue d’ensemble DNS Azure](../dns/dns-overview.md)

## <a name="dmz-architecture"></a>Architecture DMZ
De nombreuses entreprises utilisent DMZ pour segmenter leurs réseaux pour créer une zone tampon entre Internet et les services. La partie DMZ du réseau est considéré comme une zone de sécurité de faible et aucune ressources de valeur élevée ne sont placées dans ce segment du réseau. Vous verrez généralement les périphériques de sécurité réseau qui ont une interface réseau sur le segment DMZ et une autre interface réseau connecté à un réseau qui comporte des machines virtuelles et services qui acceptent les connexions entrantes à partir d’Internet.

Il existe un certain nombre de variantes de conception DMZ et à la décision pour déployer une DMZ, puis type de DMZ à utiliser si vous décidez d’utiliser un, est basée sur vos exigences de sécurité réseau.

Pour en savoir plus :

- [Services de Cloud Microsoft et la sécurité de réseau](../best-practices-network-security.md)

## <a name="azure-security-center"></a>Centre de sécurité Azure
Centre de sécurité vous aide à prévenir, détecter et répondre aux menaces et fournit augmentée de visibilité et contrôler, la sécurité de vos ressources Azure. Il assure la gestion de surveillance et la stratégie de sécurité intégrée sur vos abonnements Azure, permettant de détecter les menaces qui pourraient passer inaperçus et fonctionne avec une large réseau de solutions de sécurité.

Centre de sécurité Azure vous permet d’optimiser et contrôler la sécurité du réseau en :

- Fournir des recommandations de sécurité réseau
- Contrôle de l’état de la configuration de sécurité de votre réseau
- Vous alerter à réseau en fonction des menaces à la fois au niveau du point de terminaison et réseau

Pour en savoir plus :

- [Introduction au centre de sécurité Azure](../security-center/security-center-intro.md)
