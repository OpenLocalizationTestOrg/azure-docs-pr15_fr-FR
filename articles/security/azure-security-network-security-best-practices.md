<properties
   pageTitle="Meilleures pratiques de sécurité réseau Azure | Microsoft Azure"
   description="Cet article fournit un ensemble de meilleures pratiques pour l’utilisation de sécurité réseau Azure capacités intégrées."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="swadhwa"
   editor="TomShinder"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/25/2016"
   ms.author="TomSh"/>

# <a name="azure-network-security-best-practices"></a>Meilleures pratiques de sécurité réseau Azure

Microsoft Azure vous permet de vous connecter machines virtuelles et des appareils avec d’autres appareils en réseau en les plaçant sur réseaux virtuels Azure. Un réseau virtuel Azure est une construction de réseau virtuel qui permet de vous connecter cartes réseau virtuel à un réseau virtuel pour autoriser les communications basées sur IP entre les périphériques réseau activé. Machines virtuelles Azure connecté à un réseau virtuel Azure sont en mesure de se connecter aux périphériques sur le même Azure réseau virtuel, différents réseaux virtuel Azure, sur Internet ou même sur vos réseaux locaux.

Dans cet article, nous allons étudier un ensemble de meilleures pratiques de sécurité réseau Azure. Les meilleures pratiques suivantes sont dérivées de notre expérience avec Azure mise en réseau et l’expérience des clients comme vous-même.

Pour chaque meilleure pratique, nous allons expliquer :

- Quelle est la meilleure pratique
- Raison pour laquelle vous souhaitez activer que les meilleures pratiques
- Quelle peut être le résultat si vous ne parvenez pas à activer les meilleures pratiques
- Alternatives possibles pour les meilleures pratiques
- Comment vous pouvez en savoir activer la meilleure pratique

Cet article Azure réseau sécurité recommandations est basé sur un avis consensus et les fonctionnalités de plateforme Azure et les ensembles de fonctionnalités, telles qu’elles existent au moment de la que rédaction de cet article. Avis et technologies changent au fil du temps et de cet article est mise à jour régulièrement afin de refléter ces modifications.

Azure réseau meilleures pratiques de sécurité mentionnés dans cet article sont les suivantes :

- Sous-réseaux de segment logiquement
- Contrôler le comportement de routage
- Activer tunnel forcé
- Utiliser des équipements réseau virtuelle
- Déployer DMZ pour le zonage de sécurité
- Éviter l’exposition à Internet avec liaisons WAN dédiées
- Optimiser les performances et disponibilité
- Utiliser l’équilibrage de charge global
- Désactiver l’accès aux Machines virtuelles Azure RDP
- Activer le centre de sécurité Azure
- Étendre votre centre de données dans Azure


## <a name="logically-segment-subnets"></a>Sous-réseaux de segment logiquement

[Réseaux virtuels Azure](https://azure.microsoft.com/documentation/services/virtual-network/) sont similaires à un réseau local sur votre réseau local. L’idée derrière un réseau virtuel Azure est que vous créez un seul IP adresse basée sur l’espace réseau privé sur lequel vous pouvez placer tous vos [ordinateurs virtuels Azure](https://azure.microsoft.com/services/virtual-machines/). Les espaces d’adresse IP privées disponibles sont dans la classe A (10.0.0.0/8), la classe B (172.16.0.0/12) et la classe C plages (192.168.0.0/16).

Semblable à ce que vous faites en local, vous souhaiterez segmenter le plus grand espace d’adressage en sous-réseaux. Vous pouvez utiliser [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) basé subnetting principes pour créer vos sous-réseaux.

Routage entre sous-réseaux sera-t-elle automatique et vous n’avez pas besoin de configurer manuellement les tables de routage. Toutefois, le paramètre par défaut est qu’il n’existe aucun contrôle d’accès réseau entre les sous-réseaux que vous créez sur le réseau virtuel Azure. Afin de créer des contrôles d’accès réseau entre sous-réseaux, vous devez faire quelque chose entre les sous-réseaux.

Un des choses que vous pouvez utiliser pour effectuer cette tâche est un [Groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md) (NSG). NSGs sont appareils d’inspection des paquets avec état simple qui utilisent le tuple 5 (l’adresse IP source, port source, adresse IP de destination, port de destination et protocole de la couche 4) approche pour créer autoriser/refuser des règles pour le trafic réseau. Vous pouvez autoriser ou refuser le trafic vers et depuis l’adresse IP unique, vers et depuis plusieurs adresses IP ou même et de sous réseaux entière.

À l’aide de NSGs pour contrôler l’accès réseau entre sous-réseaux vous permet de placer les ressources appartiennent à la même zone de sécurité ou d’un rôle dans leurs propres sous-réseaux. Par exemple, considérez une application de niveau 3 simple qui comporte un niveau web, une couche de logique d’application et un niveau de base de données. Vous stockez machines virtuelles appartenant à chacun de ces niveaux dans leurs propres sous-réseaux. Puis vous utilisez NSGs pour contrôler le trafic entre les sous-réseaux :

- Hiérarchiser les machines virtuelles Web peuvent uniquement établir des connexions pour les ordinateurs de logique d’application et peut accepter uniquement des connexions à partir d’Internet
- Machines virtuelles de logique application permet de lancer des connexions avec niveau base de données et accepte uniquement les connexions depuis le niveau web
- Hiérarchiser les machines virtuelles de base de données ne peut pas démarrer connexion avec rien en dehors de leur propre sous-réseau et accepte uniquement les connexions depuis le niveau logique d’application

Pour en savoir plus sur les groupes de sécurité réseau et comment vous pouvez les utiliser pour segmenter logiquement vos réseaux virtuel Azure, veuillez lire l’article [qu’est un groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md) (NSG).

## <a name="control-routing-behavior"></a>Contrôler le comportement de routage

Lorsque vous insérez une machine virtuelle sur un réseau virtuel Azure, vous pouvez constater que la machine virtuelle pouvez vous connecter à d’autres machines virtuelles sur le même réseau virtuel Azure, même si les autres machines virtuelles se trouvent sur différents sous-réseaux. Pourquoi cela est possible parce qu’il existe une collection d’itinéraires système qui sont activées par défaut qui permettent de ce type de communication. Ces itinéraires par défaut permettent machines virtuelles sur le même réseau virtuel Azure à établir des connexions entre eux et avec Internet (pour les communications sortantes à Internet uniquement).

Tandis que les itinéraires système par défaut sont utiles pour de nombreux scénarios de déploiement, il est parfois lorsque vous souhaitez personnaliser la configuration du routage pour vos déploiements. Ces personnalisations permettent de configurer l’adresse du saut suivant pour accéder à des destinations spécifiques.

Nous vous recommandons de configurer itinéraires définies par l’utilisateur lorsque vous déployez une application de sécurité réseau virtuel, qui nous aborderons dans une version ultérieure est recommandé.

> [AZURE.NOTE] Itinéraires définies par l’utilisateur ne sont pas obligatoires et les itinéraires système par défaut sont prises en charge dans la plupart des cas.

Vous pouvez en savoir plus sur itinéraires définies par l’utilisateur et comment les configurer en lisant l’article [à quoi correspondent itinéraires définies par l’utilisateur et transfert IP](../virtual-network/virtual-networks-udr-overview.md).

## <a name="enable-forced-tunneling"></a>Activer tunnel forcé

Pour mieux comprendre tunnel forcé, il est utile de comprendre les « fractionnées tunnel » sont.
L’exemple le plus courant fractionnée tunnel est vu avec les connexions VPN. Imaginons établir une connexion VPN à partir de votre hôtel à votre réseau d’entreprise. Cette connexion vous permet de vous connecter à des ressources sur votre réseau d’entreprise à emporter toutes les communications vers des ressources sur votre réseau d’entreprise via le tunnel VPN.

Que se passe-t-il lorsque vous voulez vous connecter aux ressources sur Internet ? Lorsque le fractionnement tunnel est activé, les connexions accédez directement à Internet et non via le tunnel VPN. Certains experts envisagez cette option pour être un problème potentiel et par conséquent recommandé que fractionnée tunnel désactivé et toutes les connexions, ceux destinés à Internet et ceux destinés à des ressources d’entreprise, accédez à travers le tunnel VPN. L’avantage d’effectuer cette opération est que les connexions à Internet sont puis forcées via les périphériques de sécurité réseau d’entreprise, qui n’est pas le cas si le client VPN connecté à Internet en dehors du tunnel VPN.

Maintenant nous allons ramener ce aux machines virtuelles sur un réseau virtuel Azure. Les itinéraires par défaut pour un réseau virtuel Azure autoriser machines virtuelles lancer le trafic à Internet. Ceci peut également représenter un risque de sécurité, comme les connexions sortantes peuvent augmenter la surface d’attaque d’une machine virtuelle et être exploitées par les pirates.
Pour cette raison, nous vous recommandons d’activer tunnel forcé sur vos ordinateurs virtuels lorsque vous avez connectivité entre locaux entre votre réseau virtuel Azure et de votre réseau local. Nous allons aborder croisée connectivité locaux plus loin dans ce document pratiques meilleures réseau Azure.

Si vous ne disposez pas d’une connexion locale croisée, vérifiez que vous tirer parti des groupes de sécurité réseau (décrite plus haut) ou Azure virtuel réseau appareils de sécurité (nous l’avons vu suivant) pour éviter les connexions sortantes à Internet à partir de vos Machines virtuelles Azure.

Pour en savoir plus sur forcé tunnel et comment l’activer, consultez l’article [configurer forcé tunnel à l’aide de PowerShell et Azure le Gestionnaire de ressources](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md).

## <a name="use-virtual-network-appliances"></a>Utiliser les appareils réseau virtuel

Tandis que les groupes de sécurité réseau et le routage définies par l’utilisateur peuvent fournir une certaine mesure de sécurité réseau au niveau des couches réseau et transport du [modèle OSI](https://en.wikipedia.org/wiki/OSI_model), il est probable y avoir des situations où vous voulez ou devez activer la sécurité au niveau élevé de la pile. Dans ce cas, nous vous recommandons de déployer les appareils de sécurité réseau virtuel fournis par des partenaires Azure.

Équipements de sécurité réseau Azure pour pouvoir délivrer considérablement améliorer le niveau de sécurité sur ce qui est fourni par contrôles au niveau du réseau. Voici quelques-unes des fonctionnalités de sécurité réseau fournies par les appareils de sécurité réseau virtuel :

- Installation de pare-feu
- Prévention des intrusions Détection/intrusions sur
- Gestion des vulnérabilités
- Contrôle de l’application
- Détection des anomalies sur le réseau
- Filtrage de contenu Web
- Logiciel antivirus
- Protection de réseau de robots

Si vous avez besoin d’un niveau de sécurité réseau que vous pouvez obtenir avec les contrôles d’accès au réseau, nous vous recommandons que vous recherchez et déployez des solutions de sécurité réseau virtuel Azure.

Pour en savoir plus sur les appareils de sécurité réseau virtuel Azure sont disponibles et leurs fonctionnalités, visitez le [Azure Marketplace](https://azure.microsoft.com/marketplace/) et recherchez « sécurité » et « sécurité du réseau ».

##<a name="deploy-dmzs-for-security-zoning"></a>Déployer DMZ pour le zonage de sécurité
Une DMZ ou « réseau de périmètre » est un segment de réseau physique ou logique qui est conçu pour offrir un niveau supplémentaire de sécurité entre vos biens et Internet. L’objectif de la zone DMZ consiste à placer les périphériques de contrôle d’accès réseau spécialisées sur le bord du réseau DMZ afin que seul le trafic souhaité est autorisé après le périphérique de sécurité réseau et à votre réseau virtuel Azure.

DMZ est utiles, car vous pouvez vous concentrer votre gestion contrôle d’accès réseau, surveillance, journalisation et création de rapports sur les appareils sur le bord de votre réseau virtuel Azure. Ici vous devriez généralement autoriser prévention DDoS, systèmes de détection/Intrusion anti-intrusion (ID/adresses IP), les règles de pare-feu et stratégies, filtrage de contenu web, contre les logiciels malveillants réseau et autres éléments. Les périphériques de sécurité réseau réunir entre Internet et votre réseau virtuel Azure et disposent d’une interface sur les deux réseaux.

Pendant la conception de base d’une DMZ, il existe nombreuses conceptions DMZ, tels que DOS à DOS, hébergés brochure, multi-hébergés et d’autres personnes.

Nous vous recommandons de tous les déploiements de sécurité élevée que vous envisagez de déployer une DMZ afin d’améliorer le niveau de sécurité de réseau pour vos ressources Azure.

Pour en savoir plus sur DMZ et comment les déployer dans Azure, veuillez lire l’article [Services Cloud Microsoft et la sécurité du réseau](../best-practices-network-security.md).

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Éviter l’exposition à Internet avec liaisons WAN dédiées
De nombreuses organisations ont choisi l’itinéraire informatique hybride. Hybrides informatique, certains des ressources d’informations de la société sont dans Azure, tandis que d’autres personnes restent en local. Dans de nombreux cas certains composants d’un service seront exécutées Azure tandis que les autres composants restent en local.

Dans le scénario informatique hybride, il est généralement un type de connectivité entre locaux. Cela croisée locaux connectivité permet à l’entreprise pour vous connecter leurs réseaux locaux aux réseaux virtuels Azure. Il existe deux solutions de connectivité entre sites :

- VPN de site à
- ExpressRoute

[VPN de site à](../vpn-gateway/vpn-gateway-site-to-site-create.md) représente une connexion privée virtuelle entre votre réseau local et un réseau virtuel Azure. Cette connexion s’effectue par le biais d’Internet et vous permet d’informations « tunnel » à l’intérieur d’une liaison cryptée entre votre réseau et Azure. Site à VPN est une technologie sécurisée, mature qui a été déployée par les entreprises de toute taille depuis des décennies. Chiffrement tunnel est effectué à l’aide du [mode de tunnel IPsec](https://technet.microsoft.com/library/cc786385.aspx).

Pendant VPN de site à une technologie fiable, fiable et établie, le trafic au sein du tunnel parcourt Internet. En outre, la bande passante est relativement limitée à un maximum de sur 200Mbps.

Si vous avez besoin d’un niveau de sécurité ou les performances exceptionnel pour vos connexions entre local, nous vous recommandons d’utiliser Azure ExpressRoute pour votre connectivité entre locaux. ExpressRoute est un réseau étendu dédié lien entre votre emplacement local ou un fournisseur d’hébergement Exchange. Car il s’agit d’une connexion de télécommunication, vos données ne voyage via Internet et par conséquent ne sont pas exposées aux risques potentiels intrinsèque à communications avec Internet.

Pour en savoir plus sur le fonctionnement de Azure ExpressRoute et comment déployer, veuillez lire l’article [Présentation technique ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="optimize-uptime-and-performance"></a>Optimiser les performances et disponibilité
Confidentialité, intégrité et disponibilité (CIA) comprennent triangle plus influence du aujourd'hui modèle de sécurité. Confidentialité est sur le chiffrement et confidentialité, intégrité référentielle consiste à s’assurer que les données ne sont pas modifiées par des personnes non autorisées et disponibilité consiste à s’assurer que les personnes autorisées sont en mesure d’accéder aux informations qu’ils sont autorisés à accéder. Échec de l’une de ces zones représente une violation potentielle de sécurité.

Disponibilité peut être considérée comme étant sur la disponibilité et les performances. Si un service est vers le bas, informations ne sont pas accessibles. Si les performances sont tellement médiocre sous pour rendre les données inutilisable, nous pouvons prendre en compte les données n’est pas accessible. Par conséquent, un point de vue de sécurité, nous avons besoin de faire tout ce que nous pouvons pour vous assurer que nos services ont des performances et une disponibilité optimale.
Une méthode standard et efficace utilisée pour améliorer la disponibilité et les performances consiste à utiliser l’équilibrage de charge. L’équilibrage de charge est une méthode de distribution du trafic réseau sur les serveurs qui font partie d’un service. Par exemple, si vous avez des serveurs web frontaux dans le cadre de votre service, vous pouvez utiliser l’équilibrage de charge pour distribuer le trafic sur vos serveurs web frontaux plusieurs.

Cette distribution du trafic augmente la disponibilité, car si un des serveurs web devient indisponible, l’équilibrage de charge s’arrêter l’envoi de trafic sur ce serveur et rediriger le trafic vers les serveurs qui sont toujours en ligne. L’équilibrage de charge permet également de performances, car le processeur, le réseau et la mémoire surcharge pour traiter les demandes est répartie sur tous les la charge équilibrée serveurs.

Nous vous recommandons d’utiliser l’équilibrage de charge dès que possible et en fonction de vos services. Nous allons nous en occuper pertinence dans les sections suivantes.
Au niveau du réseau virtuel Azure, Azure fournit qu'avec trois principal chargé options équilibrage de charge :

- Équilibrage de charge basé sur HTTP
- Équilibrage de charge externe
- Équilibrage de charge interne

## <a name="http-based-load-balancing"></a>Équilibrage de charge basé sur HTTP
Équilibrage de charge basé sur HTTP base décisions sur le serveur d’envoyer des connexions à l’aide de caractéristiques du protocole HTTP. Azure a un équilibrage de charge HTTP qui se prolonge par le nom de la passerelle d’Application.

Il est recommandé que vous nous Azure Application passerelle lorsque :

- Applications qui requièrent des requêtes à partir de la même session utilisateur/client pour accéder à la même machine virtuelle principale. Exemples de cet achat panier applications et les serveurs de messagerie web.
- Applications que vous voulez libérer des batteries de serveurs web à partir de frais de résiliation SSL en tirant parti de la fonctionnalité [SSL décharger](https://f5.com/glossary/ssl-offloading) de passerelle d’Application.
- Applications, comme un réseau de distribution de contenu, qui ont besoin de plusieurs requêtes HTTP sur la connexion TCP longue même être routés ou charger équilibrée vers d’autres serveurs de données principale.

Pour en savoir plus sur le fonctionnement de la passerelle d’Application Azure et comment vous pouvez l’utiliser dans votre déploiement, veuillez lire l’article [Présentation de passerelle de l’Application](../application-gateway/application-gateway-introduction.md).

## <a name="external-load-balancing"></a>Équilibrage de charge externe
Équilibrage de charge externe intervient lorsque les connexions entrantes à partir d’Internet sont réparties entre vos serveurs situés dans un réseau virtuel Azure. L’équilibrage de charge externe Azure peut vous fournir cette fonctionnalité et nous vous recommandons d’utiliser lorsque vous ne nécessitent pas les sessions pense-bête ou décharger SSL.

Contrairement à équilibrage de charge basé sur HTTP, l’équilibrage de charge externe utilise les informations au niveau des couches réseau et transport du modèle réseau OSI pour prendre des décisions sur le serveur pour charger solde connexion à.

Nous vous recommandons d’utiliser l’équilibrage de charge externe chaque fois que vous avez des [applications sans état](http://whatis.techtarget.com/definition/stateless-app) accepter des demandes entrantes à partir d’Internet.

Pour en savoir plus sur le fonctionnement de l’équilibrage de charge externe Azure et comment vous pouvez déployer, veuillez lire l’article [premiers pas de création d’un équilibrage de la charge face à Internet dans le Gestionnaire de ressources à l’aide de PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="internal-load-balancing"></a>Équilibrage de charge interne
Équilibrage de charge interne est similaire à l’équilibrage de charge externe et utilise le même mécanisme pour charger équilibrer les connexions aux serveurs derrière eux. La seule différence est que l’équilibrage de charge est dans ce cas accepter les connexions de machines virtuelles qui ne sont pas sur Internet. Dans la plupart des cas, les connexions qui ont été acceptées pour l’équilibrage de charge sont initiées par périphériques sur un réseau virtuel Azure.

Nous vous conseillons d’utiliser interne équilibrage de charge pour les scénarios bénéficiant de cette fonctionnalité, par exemple lorsque vous avez besoin de charger équilibrer les connexions à SQL serveurs ou web interne.

Pour en savoir plus sur le fonctionnement de l’équilibrage de charge interne Azure et comment vous pouvez déployer, veuillez lire l’article [premiers pas de création d’un équilibrage de charge interne à l’aide de PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md#update-an-existing-load-balancer).

## <a name="use-global-load-balancing"></a>Utiliser l’équilibrage de charge global
Rend informatique cloud public est-il possible de déployer globalement distribué applications qui ont des composants situés dans des centres de données dans le monde entier. Cela est possible sur Microsoft Azure en raison de la présence de centre de données global de Azure. Contrairement à l’équilibrage technologies mentionnés précédemment, équilibrage de charge global permet de rendre les services disponibles même lorsque des centres de données entier peut ne sont plus disponibles.

Vous pouvez obtenir ce type global d’équilibrage de charge dans Azure en tirant parti du [Gestionnaire de trafic Azure](https://azure.microsoft.com/documentation/services/traffic-manager/). Facilite le trafic gestionnaire est possible de charger équilibrer les connexions à vos services selon l’emplacement de l’utilisateur.

Par exemple, si l’utilisateur effectue une demande à votre service à partir de l’Union européenne, la connexion est dirigée vers votre services situés dans un centre de données de l’Union européenne. Cette partie du trafic gestionnaire global chargez équilibrage de charge contribue à améliorer les performances, car la connexion au centre de données le plus proche est plus rapidement que la connexion aux centres de données qui sont loin.

Sur le côté de disponibilité, l’équilibrage de charge global permet de s’assurer que votre service est disponible même si un centre de données entier doit deviennent disponible.

Par exemple, si un centre de données Azure deviendrait indisponible en raison de l’environnement raisons ou en raison de pannes (par exemple, des échecs de réseau régionaux), les connexions à votre service seraient redirigées vers le plus proche du centre de données en ligne. Cette équilibrage de charge global consiste à tirer parti des stratégies de DNS que vous pouvez créer dans le Gestionnaire de trafic.

Nous vous recommandons d’utiliser le trafic gestionnaire pour toute solution cloud que vous développez qui comporte une étendue largement distribuée dans plusieurs régions et nécessite le plus haut niveau de disponibilité possible.

Pour en savoir plus sur le Gestionnaire de trafic Azure et son déploiement, veuillez lire l’article [en quoi consiste le trafic Manager](../traffic-manager/traffic-manager-overview.md).

## <a name="disable-rdpssh-access-to-azure-virtual-machines"></a>Désactiver l’accès aux Machines virtuelles Azure RDP/SSH
Il est possible d’atteindre Machines virtuelles Azure à l’aide de [Remote Desktop Protocol](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) et les protocoles [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) (SSH). Ces protocoles permettent de gérer des machines virtuelles à distance et sont standard dans le centre de données informatique.

Le problème de sécurité potentiel d’utiliser ces protocoles via Internet est que les pirates peuvent utiliser différentes techniques [en force](https://en.wikipedia.org/wiki/Brute-force_attack) pour accéder au Machines virtuelles Azure. Une fois que les pirates accèdent, vous pouvez utiliser votre machine virtuelle comme un point de lancement pour compromettre les autres ordinateurs sur votre réseau virtuel Azure ou même attaquer périphériques en réseau en dehors d’Azure.

Pour cette raison, nous vous recommandons de désactiver un accès direct RDP et SSH à vos Machines virtuelles Azure à partir d’Internet. Une fois un accès direct RDP et SSH à partir d’Internet est désactivé, vous disposez d’autres options que vous permet d’accéder à ces machines virtuelles gestion à distance :

- Point-à-site VPN
- Site à VPN
- ExpressRoute

[Point-à-site VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md) est synonyme d’une connexion client/serveur d’accès distant. Un réseau privé virtuel point-à-site permet à un utilisateur pour se connecter à un réseau virtuel Azure via Internet. Une fois la connexion point-à-site est établie, l’utilisateur sera en mesure d’utiliser RDP ou SSH pour vous connecter à n’importe quel machines virtuelles situés sur le réseau virtuel Azure que l’utilisateur connecté par le biais point-à-site VPN. Cette procédure suppose que l’utilisateur est autorisé à communiquer aux machines virtuelles.

Point-à-site VPN est plus sécurisé que les connexions RDP ou SSH directes car l’utilisateur doit s’authentifier à deux reprises avant de vous connecter à une machine virtuelle. Tout d’abord, l’utilisateur doit s’authentifier (et autorisés) pour établir la connexion VPN point-à-site ; Ensuite, l’utilisateur doit s’authentifier (et autorisés) pour établir la session RDP ou SSH.

Un [site à VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) connecte un réseau entier à un autre réseau via Internet. Vous pouvez utiliser un réseau privé virtuel de site à connecter votre réseau local à un réseau virtuel Azure. Si vous déployez un site à VPN, les utilisateurs sur votre réseau local est en mesure de vous connecter à des machines virtuelles sur votre réseau virtuel Azure en utilisant le protocole RDP ou SSH sur la connexion VPN de site à et ne nécessite pas vous permet d’autoriser des accès direct RDP ou SSH via Internet.

Vous pouvez également utiliser une liaison réseau étendue dédiée pour fournir des fonctionnalités similaires au site à VPN. Les principales différences sont 1. la liaison réseau étendu dédiée ne parcourir Internet et 2. liaisons WAN dédiées sont généralement plus stable et performant. Azure fournit une solution de liaison réseau étendu dédiée sous la forme [d’ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="enable-azure-security-center"></a>Activer le centre de sécurité Azure
Centre de sécurité Azure vous aide à prévenir, détecter et répondre aux menaces et fournit augmentée de visibilité et contrôler, la sécurité de vos ressources Azure. Il assure la gestion de surveillance et la stratégie de sécurité intégrée sur vos abonnements Azure, permettant de détecter les menaces qui pourraient passer inaperçus et fonctionne avec une large réseau de solutions de sécurité.

Centre de sécurité Azure vous permet d’optimiser et contrôler la sécurité du réseau en :

- Fournir des recommandations de sécurité réseau
- Contrôle de l’état de la configuration de sécurité de votre réseau
- Vous alerter à réseau en fonction des menaces à la fois au niveau du point de terminaison et réseau

Il est vivement recommandé d’activer le centre de sécurité Azure pour l’ensemble de vos déploiements Azure.

Pour en savoir plus sur le centre de sécurité Azure et comment l’activer pour vos déploiements, veuillez lire l’article [Introduction au centre de sécurité Azure](../security-center/security-center-intro.md).

## <a name="securely-extend-your-datacenter-into-azure"></a>Étendre en toute sécurité votre centre de données dans Azure
INFORMATIQUES de l’entreprise nombreuses organisations recherchées pour développer dans le nuage au lieu de croissance leurs centres de données locale. Cette extension représente une extension d’infrastructure informatique dans le cloud public. En tirant parti des locaux entre les options de connectivité, il est possible de traiter vos réseaux virtuel Azure comme sous-réseau qu’une autre sur votre infrastructure de réseau local.

Cependant, il est un grand nombre de problèmes de planification et de conception qui doivent être résolus tout d’abord. Ceci est particulièrement important dans la zone de sécurité du réseau. Une des meilleures façons de mieux comprendre comment vous approche de ce type de conception consiste à visualiser un exemple.

Microsoft a créé le [Diagramme d’Architecture de référence Extension centre de données](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84#content) et la documentation de prise en charge pour vous aider à comprendre ce que donnera ce une extension de centre de données. Cela fournit un exemple d’implémentation de référence que vous pouvez utiliser pour planifier et concevoir une extension de centre de données d’entreprise sécurisées dans le cloud. Nous vous conseillons d’étudier ce document pour obtenir une idée des composants clés d’une solution sécurisé.

Pour plus d’informations sur la façon d’étendre en toute sécurité votre centre de données dans Azure, veuillez consulter la vidéo [Extension de votre centre de données pour Microsoft Azure](https://www.youtube.com/watch?v=Th1oQQCb2KA).
