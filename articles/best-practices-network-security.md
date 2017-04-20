<properties
   pageTitle="Sécurité de réseau et de services de cloud Microsoft | Microsoft Azure"
   description="Découvrez les principales fonctionnalités disponibles dans Azure pour aider à créer des environnements réseau sécurisés"
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/14/2016"
   ms.author="jonor;sivae"/>

# <a name="microsoft-cloud-services-and-network-security"></a>Sécurité de réseau et de services de cloud Microsoft

Services cloud Microsoft fournir des services hyperscale et infrastructure, capacités de niveau entreprise et nombreuses options sont disponibles pour la connectivité hybride. La possibilité de clients accéder à ces services via Internet ou avec Azure ExpressRoute, qui fournit la connectivité réseau privé. La plateforme Microsoft Azure permet aux clients en toute transparence étendre leur infrastructure dans le nuage et concevoir des architectures à plusieurs couches. En outre, tiers peuvent activer des fonctionnalités améliorées en proposant des services de sécurité et l’authentification multifacteur. Ce livre blanc fournit une vue d’ensemble de la sécurité et les problèmes d’architecture clients doivent prendre en compte lors de l’utilisation de services de cloud Microsoft accédés via ExpressRoute. Il traite également de la création de services plus sécurisées dans des réseaux virtuels Azure.

## <a name="fast-start"></a>Démarrage rapide
Le graphique de logique suivants vous oriente vers un exemple spécifique sur les différentes techniques de sécurité disponibles avec la plateforme Azure. Pour un aide-mémoire, recherchez l’exemple mieux adaptée à votre cas. Pour obtenir une description plus complète, poursuivez la lecture à travers le papier.
![Organigramme des options de sécurité][0]

[Exemple 1 : Créer un réseau de périmètre (également appelé DMZ, de périmètre et sous-réseau filtré) pour mieux vous protéger applications des groupes de sécurité réseau (NSGs).](#example-1-build-a-simple-dmz-with-nsgs)</br>
[Exemple 2 : Créer un réseau de périmètre pour mieux vous protéger applications avec un pare-feu et NSGs.](#example-2-build-a-dmz-to-protect-applications-with-a-firewall-and-nsgs)</br>
[Exemple 3 : Créer un réseau de périmètre pour mieux vous protéger des réseaux avec un pare-feu, itinéraire définies par l’utilisateur (UDR) et NSG.](#example-3-build-a-dmz-to-protect-networks-with-a-firewall-udr-and-nsg)</br>
[Exemple 4 : Ajouter une connexion hybride avec un réseau privé virtuel matériel virtuelle, site à site (VPN).](#example-4-adding-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn)</br>
[Exemple 5 : Ajouter une connexion hybride avec une site à Azure passerelle VPN.](#example-5-adding-a-hybrid-connection-with-a-site-to-site-azure-gateway-vpn)</br>
[Exemple 6 : Ajouter une connexion hybride avec ExpressRoute.](#example-6-adding-a-hybrid-connection-with-expressroute)</br>
Exemples d’ajout des connexions entre les réseaux virtuels, la disponibilité et la chaîne de service sont ajoutés à ce document dans les prochains mois.

## <a name="microsoft-compliance-and-infrastructure-protection"></a>Protection de la conformité et infrastructure de Microsoft
Microsoft a pris une position de leader prenant en charge des initiatives de conformité requis par les clients d’entreprise. Voici quelques-unes de la certification de conformité pour Azure : ![badges de conformité Azure][1]

Pour plus d’informations, voir les informations de conformité dans le [Centre de gestion de la confidentialité de Microsoft](https://azure.microsoft.com/support/trust-center/compliance/).

Microsoft fournit une approche complète pour protéger infrastructure cloud nécessaire à l’exécution hyperscale globales services. Infrastructure cloud Microsoft inclut le matériel, logiciels, réseaux et d’administration et le personnel opérations, outre les centres de données physique.

![Fonctionnalités de sécurité Azure][2]

Cette approche fournit une base plus sécurisée pour les clients à déployer leurs services, dans le cloud Microsoft. L’étape suivante concerne les clients à concevoir et créer une architecture de sécurité pour protéger ces services.

## <a name="traditional-security-architectures-and-perimeter-networks"></a>Architectures de sécurité traditionnel et les réseaux de périmètre
Bien que Microsoft investissements intensément dans la protection de l’infrastructure cloud, clients doivent également protéger leurs services cloud et groupes de ressources. Une approche multicouche sécurité fournit la meilleure défense. Une zone de sécurité du réseau périmètre protège les ressources de votre réseau interne à partir d’un réseau non approuvé. Un réseau de périmètre fait référence à la bords ou les parties du réseau qui se trouvent entre Internet et la protection de l’entreprise infrastructure informatique.

Dans les réseaux standard de l’entreprise, l’infrastructure de base est intensément enrichi en le périmètre, avec plusieurs couches de périphériques de sécurité. La limite de chaque calque se compose des appareils mobiles et les points de l’application de stratégie. Appareils peuvent inclure les éléments suivants : pare-feu, prévention de refus de Service distribuées (DDoS), détection ou des systèmes de Protection (ID/adresses IP) et les périphériques VPN. Application de la stratégie peut prendre la forme de stratégies de pare-feu, de listes de contrôle d’accès (utilisateurs) ou de routage spécifique. La première ligne de défense du réseau, directement accepter le trafic entrant à partir d’Internet, est une combinaison de ces mécanismes pour bloquer les attaques et le trafic dommageable tout en permettant aux demandes légitimes davantage connecter au réseau. Ce trafic achemine directement aux ressources du réseau de périmètre. Cette ressource peut puis « parler » aux ressources approfondies sur le réseau, transitant la limite suivante de validation première. La couche externe est appelée le réseau de périmètre car cette partie du réseau est exposée à Internet, généralement avec une forme de protection des deux côtés. La figure suivante montre un exemple d’un réseau de périmètre sous-réseau unique dans un réseau d’entreprise, avec deux limites de sécurité.

![Un réseau de périmètre dans un réseau d’entreprise][3]

Il existe plusieurs architectures permettent d’implémenter un réseau de périmètre, à partir d’un programme d’équilibrage de charge simple devant une batterie de serveurs web, à un réseau de périmètre sous-réseau plusieurs avec mécanismes variées à chaque limite pour bloquer le trafic et protéger les couches plus approfondies du réseau d’entreprise. Comment le réseau de périmètre est généré dépend des besoins spécifiques de l’organisation et sa tolérance de risque globale.

Clients atteindre leurs charges de travail nuages publics, il est essentiel pour prendre en charge des fonctionnalités similaires pour l’architecture de réseau de périmètre dans Azure pour répondre aux exigences de sécurité et conformité. Ce document fournit des instructions sur la façon dont les utilisateurs peuvent générer un environnement de réseau sécurisé dans Azure. Il se concentre sur le réseau de périmètre, mais inclut également des informations complètes sur de nombreux aspects de la sécurité du réseau. Les questions suivantes informent cette discussion :

- Comment un réseau de périmètre dans Azure peut être développée ?
- Quelles sont les fonctionnalités Azure disponibles pour générer le réseau de périmètre ?
- Comment les charges de travail principale peuvent être protégés ?
- Comment les communications Internet sont contrôlées pour les charges de travail dans Azure ?
- Comment les réseaux locaux peuvent être protégés des déploiements dans Azure ?
- Quand les fonctionnalités de sécurité Azure native doivent être utilisées par rapport aux appareils tiers ou services ?

L’illustration ci-dessous présente les différentes couches de sécurité Qu'azure fournit aux clients. Ces couches sont natif sur la plateforme Azure lui-même et fonctions définies par le client :

![Architecture de sécurité Azure][4]

Entrant à partir d’Internet, DDoS Azure vous aide à protéger contre les attaques à grande échelle par rapport à Azure. La couche suivante est défini par le client publics points de terminaison, qui sont utilisées pour déterminer le trafic qui peut passer par le service en nuage au réseau virtuel. Azure native virtual isolement réseau garantit isolement complet de tous les autres réseaux, et que le trafic passe uniquement par les méthodes et les chemins d’accès de l’utilisateur configuré. Ces chemins d’accès et les méthodes sont la couche suivante, où NSGs UDR et authentification multifacteur réseau pouvant être utilisées pour créer des limites de sécurité pour protéger les déploiements d’application dans le réseau protégé.

La section suivante fournit une vue d’ensemble des réseaux virtuels Azure. Ces réseaux virtuels est créés par les clients et est leurs déployés charges de travail qui sont connectés à. Réseaux virtuels sont la base de toutes les fonctionnalités de sécurité réseau requis pour établir un réseau de périmètre pour protéger des déploiements clients dans Azure.

## <a name="overview-of-azure-virtual-networks"></a>Vue d’ensemble des réseaux virtuels Azure
Avant le trafic Internet accessible aux réseaux virtuels Azure, il existe deux couches de sécurité intégrées à la plateforme Azure :

1.  **Protection DDoS**: protection DDoS est un calque du réseau physique Azure qui protège la plateforme Azure lui-même contre les attaques basées sur Internet à grande échelle. Ces attaques permet de plusieurs nœuds de « robots » dans une tentative de surcharger un service Internet. Azure a une protection DDoS robuste maillage sur toute la connectivité Internet entrante. Cette couche de protection DDoS ne comporte aucun attribut configurable utilisateur et n’est pas accessible au client. Cela protège Azure en tant que plate-forme contre les attaques à grande échelle, mais ne sera pas directement application client individuels. Couches de résilience supplémentaires peuvent être configurés par le client contre une attaque localisé. Par exemple, si un client a été attaque avec une grande attaque DDoS sur un point de terminaison public, Azure bloque les connexions à ce service. Client A pu basculer vers un autre réseau virtuel ou point de terminaison pas figurant dans l’attaque pour restaurer le service du service. Il convient de noter que bien que le client A peut-être être affectée sur ce point de terminaison, aucuns autres services en dehors de ce point de terminaison ne seraient affectés. En outre, d’autres clients et les services ne voyez aucun impact à partir de cette attaque.
2.  **Points de terminaison de service**: points de terminaison autoriser cloud services ou ressource groupes pour que les ports exposés et des adresses IP Internet public. Le point de terminaison utilise la traduction d’adresses réseau (NAT) pour acheminer le trafic vers l’adresse interne et le port sur le réseau virtuel Azure. Il s’agit du chemin d’accès principal pour le trafic externe à passer dans le réseau virtuel. Les points de terminaison de service sont configurable par l’utilisateur pour déterminer le trafic qui est passé et comment et où elle est convertie en sur le réseau virtuel.

Une fois que le trafic atteint le réseau virtuel, il existe de nombreuses fonctionnalités qui entrent en jeu. Réseaux virtuels Azure constituent la base des clients joindre leurs charges de travail et l’endroit où la sécurité au niveau du réseau simple s’applique. Il est un réseau privé (une superposition de réseau virtuel) dans Azure pour les clients dont les caractéristiques et les fonctionnalités suivantes :
 
- **Isolement le trafic**: un réseau virtuel est la limite d’isolement le trafic sur la plateforme Azure. Machines virtuelles (machines virtuelles) dans un réseau virtuel ne peut pas communiquer directement aux machines virtuelles dans un autre réseau virtuel, même si les deux réseaux virtuels est créés par le même client. Il s’agit d’une propriété critique qui garantit machines virtuelles client et communication reste privée au sein d’un réseau virtuel.
- **Topologie multiniveau**: réseaux virtuels permettent aux clients à définir topologie multicouche à allouer sous-réseaux et en spécifiant des espaces d’adressage distincts pour différents éléments ou « niveaux » de leurs charges de travail. Ces groupes logiques topologies aider les clients à définir une stratégie d’accès différents selon les types de charge de travail et également contrôlent le flux de trafic entre les niveaux.
- **Connectivité entre locaux**: clients pouvant établir la connectivité entre locaux entre un réseau virtuel et plusieurs sites locaux ou d’autres réseaux virtuel dans Azure. Pour ce faire, les clients peuvent utiliser passerelles VPN Azure ou authentification multifacteur réseau tiers. Azure prend en charge VPN (S2S) de site à l’aide des protocoles IPsec/IKE standard et connectivité privée ExpressRoute.
- **NSG** permet aux clients de créer des règles (utilisateurs) au niveau de précision souhaité : réseau interfaces, machines virtuelles individuels ou sous-réseaux virtuels. Les clients peuvent contrôler l’accès en autorisant ou en refusant les communications entre les charges de travail au sein d’un réseau virtuel, sur les réseaux du client via la connectivité entre local, des systèmes ou rediriger des communications Internet.
- **UDR** et **Transfert IP** permettent aux clients de définir les chemins d’accès de communication entre les différents niveaux au sein d’un réseau virtuel. Les clients peuvent déployer un pare-feu, ID/adresses IP et autres appareils virtuels et acheminer le trafic réseau via ces appareils de sécurité pour les stratégies de limite de sécurité, l’audit et inspection.
- **Authentification multifacteur réseau** dans la Azure Marketplace : appareils de sécurité tels que les pare-feu, les programmes d’équilibrage de charge et ID/adresses IP sont disponibles dans la Azure Marketplace et des bibliothèques d’images machine virtuelle. Les clients peuvent déployer ces appareils dans leurs réseaux virtuel et plus précisément, leurs limites de sécurité (y compris des sous-réseaux périmètre) pour effectuer un environnement de réseau sécurisé multi-niveaux.

Avec ces fonctionnalités et fonctionnalités, un exemple de la façon dont une architecture de réseau de périmètre peut être créée Azure est la suivante :

![Un réseau de périmètre dans un réseau virtuel Azure][5]

## <a name="perimeter-network-characteristics-and-requirements"></a>Configuration requise et les caractéristiques de réseau de périmètre
Le réseau de périmètre est conçu pour être le frontal du réseau, utilisez directement des communications à partir d’Internet. Les paquets entrants doivent se déplacent dans les applications de sécurité, tels que les pare-feu, ID et adresses IP, avant d’atteindre les serveurs principale. Paquets liées aux Internet à partir de charges de travail peuvent également se dérouler via les appareils de sécurité dans le réseau de périmètre pour l’application des stratégies, inspection et à des fins d’audit, avant de quitter le réseau. En outre, le réseau de périmètre peut héberger passerelles VPN locaux croisées entre réseaux virtuel client et en local.

### <a name="perimeter-network-characteristics"></a>Caractéristiques de réseau de périmètre
Référence à l’illustration précédente, certaines des caractéristiques d’un réseau de périmètre bonne sont les suivantes :

- Pour Internet :
    - Le périmètre réseau lui-même constitue via Internet, communiquer directement avec Internet.
    - Adresses IP public, VIP et/ou points de terminaison de service achemine le trafic Internet vers le réseau frontale et les périphériques.
    - Le trafic entrant à partir d’Internet passe par le biais des périphériques de sécurité avant d’autres ressources sur le réseau frontal.
    - Si la sécurité sortante est activée, le trafic passe au moyen de périphériques de sécurité, comme la dernière étape, avant de passer à Internet.
- Réseau protégé :
    - Il n’existe aucun chemin d’accès direct à partir d’Internet à l’infrastructure principale.
    - Canaux à l’infrastructure principale doivent parcourir au moyen de périphériques de sécurité tels que NSGs, pare-feux ou appareils VPN.
    - Autres appareils doivent utiliser pas un pont Internet et l’infrastructure principale.
    - Périphériques de sécurité sur la côté Internet et réseau protégé face à des limites du réseau de périmètre (par exemple, les deux pare-feu icônes montrés dans l’illustration précédente) peuvent être une solution virtuelle unique et différenciées règles ou interfaces pour chaque limite. (Autrement dit, un périphérique, séparé logiquement, gérer la charge pour les deux limites du réseau de périmètre.)
- Autres pratiques et les contraintes courants :
    - Charges de travail ne doivent pas stocker les informations stratégiques.
    - Accès et les mises à jour des configurations de réseau de périmètre et les déploiements sont limités à seuls les administrateurs autorisés.

### <a name="perimeter-network-requirements"></a>Configuration requise de périmètre réseau
Pour activer ces caractéristiques, suivez ces instructions besoins réseau virtuel pour implémenter un réseau de périmètre réussie :

- **Architecture sous-réseau :** Spécifiez le réseau virtuel tel qu’un sous-réseau entier est dédié comme le réseau de périmètre, séparé de sous d’autres réseaux dans le même réseau virtuel. Ainsi, le trafic entre le réseau de périmètre et d’autres flux niveaux sous-réseau interne ou privé via un pare-feu ou un dispositif virtuel ID/adresses IP sur les limites de sous-réseau avec itinéraires définies par l’utilisateur.
- **NSG :** Le sous-réseau périphérique lui-même doit être ouvert pour autoriser la communication avec Internet, mais qui n’est pas signifie clients doivent être ignorer NSGs. Suivez les pratiques de sécurité classiques pour réduire les surfaces réseau visible sur Internet. Verrouiller les plages d’adresses à distance autorisés à accéder aux déploiements ou les protocoles d’application spécifique et les ports ouverts. Il peut y avoir des circonstances dans lesquelles il n’est pas toujours possible. Par exemple, si les clients ont un site Web externe dans Azure, le réseau de périmètre devrait permettre de requêtes web entrantes à partir des adresses IP publics, mais doit ouvrir uniquement les ports d’application web : TCP : 80 et TCP:443.
- **Table gamme :** Le sous-réseau périphérique lui-même doit être en mesure de communiquer directement à Internet, mais ne doit pas autoriser une communication directe vers et depuis les réseaux de fin ou locaux précédent sans passer par un appareil de pare-feu ou de sécurité.
- **Configuration du matériel sécurité :** Pour acheminer les messages et examinez les paquets entre le réseau de périmètre et le reste des réseaux protégées, les appareils de sécurité tels que pare-feu ID et adresses IP appareils peuvent être multi-hébergés. Ils peuvent avoir des cartes réseau distinctes pour le réseau de périmètre et les sous-réseaux principale. Les cartes réseau dans le réseau de périmètre communiquent directement vers et depuis Internet, avec les NSGs correspondantes et la table de routage de réseau de périmètre. Les cartes réseau connexion aux sous-réseaux principale ont plus restreint NSGs et tables de routage des sous-réseaux principale correspondantes.
- **Fonctionnalités sécurité :** Les appareils de sécurité déployées dans le réseau de périmètre généralement exécutent les fonctionnalités suivantes :
    - Pare-feu : Appliquer des règles de pare-feu ou des stratégies de contrôle d’accès pour les demandes entrantes.
    - Détection et prévention des menaces : détecter et atténuer les attaques à partir d’Internet.
    - Audit et journalisation : gestion des détaillées journaux d’audit et d’analyse.
    - Proxy inverse : Rediriger les demandes entrantes aux serveurs principale correspondantes. Cette requiert le mappage et traduction les adresses de destination sur les appareils frontale, généralement pare-feu, à l’adresse de serveur principal.
    - Transférer proxy : fournissant NAT et l’audit pour la communication établie à partir d’au sein du réseau virtuel à Internet.
    - Routeur : Transférer le trafic entrant et entre sous-réseaux au sein du réseau virtuel.
    - Périphérique VPN : agissant en tant que les passerelles VPN entre locaux pour la connectivité VPN locaux croisées entre client local réseaux et Azure virtuel.
    - Serveur VPN : acceptation des clients VPN qui se connectent à des réseaux virtuels Azure.

>[AZURE.TIP] Séparer deux groupes suivants : toutes les personnes autorisés à accéder à l’engrenage de sécurité de réseau de périmètre et les personnes autorisées en tant qu’administrateur de développement, de déploiement ou opérations application. Tout en conservant ces groupes distinct permet d’une répartition des tâches et une seule personne empêche d’ignorer la sécurité des applications et des contrôles de sécurité réseau.

### <a name="questions-to-be-asked-when-building-network-boundaries"></a>Questions pour qu’un message lors de la création des limites du réseau
Dans cette section, à moins d’avoir spécifiquement mentionné, le terme « réseaux » fait référence à des réseaux virtuels privés Azure créés par un administrateur de l’abonnement. Le terme ne fait pas référence aux réseaux physiques sous-jacentes dans Azure.

En outre, réseaux virtuels Azure sont souvent utilisés pour étendre réseaux traditionnel en local. Il est possible d’incorporer à un site ou ExpressRoute des solutions réseau hybride avec des architectures de réseau périmètre. Il s’agit d’un facteur important pour créer des limites de sécurité réseau.

Les trois questions suivantes sont essentielles pour répondre à lorsque vous créez un réseau avec un réseau de périmètre et plusieurs limites de sécurité.

#### <a name="1-how-many-boundaries-are-needed"></a>(1) limites combien sont nécessaires ?
Le premier point de décision consiste à décider combien des limites de sécurité sont nécessaires dans un scénario donné :

- Une seule limite : une sur le réseau de périmètre frontale, entre le réseau virtuel et Internet.
- Deux frontières : une sur le côté Internet de réseau de périmètre et l’autre entre le sous-réseau du réseau périmètre et les sous-réseaux principale dans les réseaux virtuels Azure.
- Trois limites : celui sur le côté Internet du réseau de périmètre entre le réseau de périmètre et sous-réseaux principale et entre les sous-réseaux principale et le réseau local.
- Limites de N : un nombre variable. En fonction des exigences de sécurité, il est vraiment n’importe quel nombre de limites de sécurité qui peuvent être appliquées dans un réseau donné.

Le nombre et le type de limites de nécessaire varie selon la tolérance au risque d’une société et le scénario spécifique en cours d’implémentation. Il s’agit souvent décision commune apportée par plusieurs groupes au sein d’une organisation, notamment souvent un risque et équipe de conformité, un réseau et équipe platform et une équipe de développement d’applications. Personnes atteintes de connaissances de sécurité, les données participant et les technologies utilisées doit avoir un dites dans la présente décision pour garantir l’attitude de sécurité appropriés pour chaque implémentation.

>[AZURE.TIP] Utilisez le plus petit nombre de limites qui satisfont les exigences de sécurité dans une situation donnée. Avec les autres limites plus difficiles opérations et résolution des problèmes peuvent être, ainsi que les frais de gestion figurant dans gérer les stratégies de limite plusieurs au fil du temps. Toutefois, frontières insuffisante pour accroître le risque associé. Trouver le solde est critique.

![Réseau hybride avec trois limites de sécurité][6]

L’illustration précédente affiche une vue d’ensemble d’un réseau de limite de trois sécurité. Les limites sont comprises entre le réseau de périmètre et Internet, les Azure principaux et frontaux sous-réseaux privés et le sous-réseau principale Azure et au réseau local.

#### <a name="2-where-are-the-boundaries-located"></a>2) où se trouvent les limites ?
Une fois que le nombre de limites est décidé, où les mettre en œuvre est le point de décision suivant. Il existe généralement trois options :
- Utilisation d’un service intermédiaire basées sur Internet (par exemple, un nuage Web application pare-feu, qui n’est pas abordé dans ce document)
- À l’aide des fonctionnalités natives et/ou authentification multifacteur réseau dans Azure
- À l’aide de périphériques physiques sur le réseau local

Sur les réseaux purement Azure, les options sont des fonctionnalités Azure natives (par exemple, équilibrage de charge Azure) ou réseau authentification multifacteur à partir de la partenaires enrichi d’Azure (par exemple, pare-feux de Point de vérification).

Si une limite est nécessaire entre Azure et un réseau local, les périphériques de sécurité peuvent résider sur les côtés de la connexion (ou les deux côtés). Par conséquent une décision doit être prise sur l’emplacement pour placer engrenage de sécurité.

Dans l’illustration précédente, le réseau Internet périmètre des formes et les limites de premier plan-à-principale sont entièrement contenues dans Azure et doivent être natives fonctionnalités Azure ou réseau authentification multifacteur. Périphériques de sécurité sur la limite entre Azure (sous-réseau principale) et le réseau d’entreprise peuvent être sur le côté Azure ou le côté local ou même une combinaison des appareils des deux côtés. Il peut être significatives avantages et inconvénients liés à l’option qui doit être considéré comme grave.

Par exemple, à l’aide d’engrenage sécurité physique existant sur le côté de réseau local a l’avantage qu’aucune nouvelle ENGRENAGE n’est nécessaire. Il suffit reconfiguration. L’inconvénient, cependant, est que tout le trafic doit être renvoyée par Azure au réseau local pour être vu par l’engrenage de sécurité. Donc le trafic Azure à Azure pourrait être latence significative, puis sur les performances et utilisateur expérience, si elle a été forcé sur le réseau local pour l’application de stratégie de sécurité.

#### <a name="3-how-are-the-boundaries-implemented"></a>3) comment les limites sont implémentées ?
Chaque limite de sécurité aurez probablement exigences différentes (par exemple, ID et règles de pare-feu sur le côté Internet du réseau de périmètre, mais uniquement les utilisateurs entre le réseau de périmètre et sous-réseau principale). Choisir les périphériques à utiliser dépend des exigences scénario et sécurité. Dans la section suivante, exemples 1, 2 et 3 discuter certaines options peuvent être utilisées. Examiner les fonctionnalités de réseau natif Azure et les périphériques disponibles dans Azure à partir de la partenaires affiche les options multitude disponibles pour résoudre pratiquement n’importe quel scénario.

Un autre point de décision implémentation de clé consiste à se connecter au réseau locales avec Azure. Devez-vous utiliser la passerelle virtuelle Azure ou un dispositif virtuel réseau ? Ces options sont traitées plus en détail dans la section suivante (exemples 4, 5 et 6).

En outre, le trafic entre les réseaux virtuels dans Azure peut-être être nécessaires. Ces scénarios sont ajoutés à une date ultérieure.

Une fois que vous connaissez les réponses aux questions précédentes, la section [Fast Start](#fast-start) peut aider à identifier les exemples les plus appropriées pour un scénario donné.

## <a name="examples-building-security-boundaries-with-azure-virtual-networks"></a>Exemples : Créer des limites de sécurité avec les réseaux virtuels Azure
### <a name="example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs"></a>Exemple 1 : Créer un réseau de périmètre pour mieux vous protéger applications avec NSGs
[Retour au début rapide](#fast-start) | [instructions pour que cet exemple de génération détaillé][Example1]

![Réseau de périmètre entrant avec NSG][7]

#### <a name="environment-description"></a>Description de l’environnement
Dans cet exemple, il est un abonnement qui inclut les éléments suivants :

- Deux services en nuage : « FrontEnd001 » et « BackEnd001 »
- Un réseau virtuel, « CorpNetwork », avec deux sous-réseaux : « FrontEnd » et « Et »
- Un groupe de sécurité réseau qui est appliqué à deux sous-réseaux
- Windows server qui représente un serveur web d’application (« IIS01 »)
- Deux serveurs Windows qui représentent des serveurs principaux application (« AppVM01 », « AppVM02 »)
- Windows server qui représente un serveur DNS (« DNS01 »)

Pour les scripts et un modèle de gestionnaire de ressources Azure, consultez les [instructions détaillées build][Example1].

#### <a name="nsg-description"></a>Description NSG
Dans cet exemple, un groupe NSG est créé et puis chargé avec six règles.

>[AZURE.TIP] En règle générale, vous devez créer vos règles « Autoriser » spécifiques tout d’abord, suivis par les règles de « Refuser » plus génériques. La priorité donnée détermine les règles sont évaluées en premier. Une fois que le trafic a été trouvé s’appliquent à une règle spécifique, aucune règle supplémentaire n’est évaluées. Règles NSG peuvent s’appliquer dans la direction entrante ou sortante (du point de vue du sous-réseau).

Manière déclarative, les règles suivantes sont générés pour le trafic entrant :

1.  Trafic DNS interne (port 53) est autorisé.
2.  Le trafic RDP (Port3389) à partir d’Internet à toute Machine virtuelle est autorisé.
3.  Le trafic HTTP (port 80) à partir d’Internet au serveur web (IIS01) est autorisé.
4.  Tout le trafic (tous les ports) entre IIS01 et AppVM1 est autorisé.
5.  Tout le trafic (tous les ports) à partir d’Internet pour l’ensemble du réseau virtuel (les deux sous-réseaux) est refusé.
6.  Tout le trafic (tous les ports) à partir du sous-réseau frontal au sous-réseau principale est refusé.

Avec ces règles lié à chaque sous-réseau, si une requête HTTP a été entrante à partir d’Internet vers le serveur web, les deux règles 3 (autoriser) et 5 (refuser) s’appliquent. Mais règle 3 ayant une priorité plus élevée, seulement elle s’appliquera et règle 5 vient pas à lire. Par conséquent la requête HTTP serait autorisée au serveur web. Si ce même trafic a essayé d’accéder au serveur DNS01, la règle 5 (refuser) serait le premier à appliquer, et le trafic n'aurait pas autorisé à passer au serveur. Règle 6 (refuser) bloque le sous-réseau frontal de communiquer au sous-réseau principale (à l’exception du trafic autorisé dans les règles 1 et 4). Ceci protège le réseau principal au cas où un pirate se l’application web sur le serveur frontal. Accès au réseau principale « protégé » (uniquement pour les ressources exposées sur le serveur AppVM01) serait est limité à l’utilisateur malveillant.

Il existe une règle sortante par défaut qui autorise l’absence de trafic à Internet. Dans cet exemple, nous allons autoriser le trafic sortant et ne pas modifier les règles de trafic sortant. Pour verrouiller le trafic dans les deux sens, définies par l’utilisateur routage est-il nécessaire (voir exemple 3).

#### <a name="conclusion"></a>Conclusion
Il s’agit d’un moyen relativement simple et rapide d’isoler le sous-réseau principale à partir de trafic entrant. Pour plus d’informations, consultez les [instructions détaillées build][Example1]. Ces instructions sont les suivantes :

- Découvrez comment créer ce réseau périmètre des formes avec des scripts PowerShell.
- Découvrez comment créer ce réseau périmètre des formes avec un modèle d’Azure le Gestionnaire de ressources.
- Description détaillée de chaque commande NSG.
- Scénarios de flux détaillée du trafic, montrant comment le trafic est autorisé ou refusé dans chaque calque.


 ### <a name="example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs"></a>Exemple 2 : Créer un réseau de périmètre pour mieux vous protéger des applications avec un pare-feu NSGs
[Retour au début rapide](#fast-start) | [détaillé des instructions pour que cet exemple de génération][Example2]

![Réseau périmètre entrant à couloirs et NSG][8]

#### <a name="environment-description"></a>Description de l’environnement
Dans cet exemple, il est un abonnement qui inclut les éléments suivants :

- Deux services en nuage : « FrontEnd001 » et « BackEnd001 »
- Un réseau virtuel, « CorpNetwork », avec deux sous-réseaux : « FrontEnd » et « Et »
- Un groupe de sécurité réseau qui est appliqué à deux sous-réseaux
- Une application virtuel réseau, un pare-feu, dans ce cas connectée au sous-réseau frontal
- Windows server qui représente un serveur web d’application (« IIS01 »)
- Deux serveurs Windows qui représentent des serveurs principaux application (« AppVM01 », « AppVM02 »)
- Windows server qui représente un serveur DNS (« DNS01 »)

Pour les scripts et un modèle de gestionnaire de ressources Azure, consultez les [instructions détaillées build][Example2].

#### <a name="nsg-description"></a>Description NSG
Dans cet exemple, un groupe NSG est créé et puis chargé avec six règles.

>[AZURE.TIP] En règle générale, vous devez créer vos règles « Autoriser » spécifiques tout d’abord, suivis par les règles de « Refuser » plus génériques. La priorité donnée détermine les règles sont évaluées en premier. Une fois que le trafic a été trouvé s’appliquent à une règle spécifique, aucune règle supplémentaire n’est évaluées. Règles NSG peuvent s’appliquer dans la direction entrante ou sortante (du point de vue du sous-réseau).

Manière déclarative, les règles suivantes sont générés pour le trafic entrant :

1.  Trafic DNS interne (port 53) est autorisé.
2.  Le trafic RDP (Port3389) à partir d’Internet à toute Machine virtuelle est autorisé.
3.  Tout le trafic Internet (tous les ports) à l’application virtuelle réseau (pare-feu) est autorisé.
4.  Tout le trafic (tous les ports) entre IIS01 et AppVM1 est autorisé.
5.  Tout le trafic (tous les ports) à partir d’Internet pour l’ensemble du réseau virtuel (les deux sous-réseaux) est refusé.
6.  Tout le trafic (tous les ports) à partir du sous-réseau frontal au sous-réseau principale est refusé.

Avec ces règles lié à chaque sous-réseau, si une requête HTTP a été entrante à partir d’Internet dans le pare-feu, les deux règles 3 (autoriser) et 5 (refuser) s’appliquent. Mais règle 3 ayant une priorité plus élevée, seulement elle s’appliquera et règle 5 vient pas à lire. Par conséquent la requête HTTP serait autorisée au pare-feu. Si ce même trafic a essayé de contacter le serveur IIS01, même si elle se trouve sur le sous-réseau frontal, la règle 5 (refuser) s’appliquent, et le trafic n'aurait pas autorisé à passer au serveur. Règle 6 (refuser) bloque le sous-réseau frontal de communiquer au sous-réseau principale (à l’exception du trafic autorisé dans les règles 1 et 4). Ceci protège le réseau principal au cas où un pirate se l’application web sur le serveur frontal. Accès au réseau principale « protégé » (uniquement pour les ressources exposées sur le serveur AppVM01) serait est limité à l’utilisateur malveillant.

Il existe une règle sortante par défaut qui autorise l’absence de trafic à Internet. Dans cet exemple, nous allons autoriser le trafic sortant et ne pas modifier les règles de trafic sortant. Pour verrouiller le trafic dans les deux sens, définies par l’utilisateur routage est-il nécessaire (voir exemple 3).

#### <a name="firewall-rule-description"></a>Description de la règle pare-feu
Sur le pare-feu, règles de transfert doivent être créés. Étant donné que cet exemple uniquement achemine le trafic Internet entrant pour le pare-feu et puis sur le serveur web, le transfert une de traduction des adresses réseau (NAT) règle est nécessaire.

La règle de transfert accepte n’importe quelle adresse origine entrant qui accède le pare-feu essayez d’atteindre HTTP (port 80 et 443 pour HTTPS). Il a envoyé se déconnecter de l’interface du pare-feu local et redirigé vers le serveur web avec l’adresse IP de 10.0.1.5.

#### <a name="conclusion"></a>Conclusion
Il s’agit d’une méthode relativement simple de protection de votre application avec un pare-feu et isoler le sous-réseau principale à partir de trafic entrant. Pour plus d’informations, consultez les [instructions détaillées build][Example2]. Ces instructions sont les suivantes :

- Découvrez comment créer ce réseau périmètre des formes avec des scripts PowerShell.
- Découvrez comment créer cet exemple avec un modèle d’Azure le Gestionnaire de ressources.
- Descriptions détaillées de chaque règle NSG commande et de pare-feu.
- Scénarios de flux détaillée du trafic, montrant comment le trafic est autorisé ou refusé dans chaque calque.

### <a name="example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-udr-and-nsg"></a>Exemple 3 : Créer un réseau de périmètre pour mieux vous protéger des réseaux avec un pare-feu, UDR et NSG
[Retour au début rapide](#fast-start) | [instructions pour que cet exemple de génération détaillé][Example3]

![Réseau de périmètre bidirectionnelle avec à couloirs, NSG et UDR][9]

#### <a name="environment-description"></a>Description de l’environnement
Dans cet exemple, il est un abonnement qui inclut les éléments suivants :

- Trois services en nuage : « SecSvc001 », « FrontEnd001 » et « BackEnd001 »
- Un réseau virtuel, « CorpNetwork », avec trois sous-réseaux : « SecNet », « FrontEnd » et « Et »
- Une application virtuelle réseau, un pare-feu, dans ce cas connectée au sous-réseau SecNet
- Windows server qui représente un serveur web d’application (« IIS01 »)
- Deux serveurs Windows qui représentent des serveurs principaux application (« AppVM01 », « AppVM02 »)
- Windows server qui représente un serveur DNS (« DNS01 »)

Pour les scripts et un modèle de gestionnaire de ressources Azure, consultez les [instructions détaillées build][Example3].

#### <a name="udr-description"></a>Description UDR
Par défaut, les itinéraires système suivants sont définies en tant que :

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

Le VNETLocal est toujours le prefix(es) adressage par défaut du réseau virtuel pour ce réseau spécifique (autrement dit, il se transforme à partir du réseau virtuel en réseau virtuel, selon la manière dont chaque réseau virtuel spécifique est défini). Les itinéraires système restantes sont statiques et par défaut comme indiqué dans le tableau.

Dans cet exemple, deux tables de routage sont créés, une pour les sous-réseaux frontale et principale. Chaque table est chargée avec des itinéraires statiques appropriées pour le sous-réseau donné. Pour cet exemple, chaque table possède trois itinéraires diriger tout le trafic (0.0.0.0/0) via le pare-feu (saut suivant = adresse IP matériel virtuelle) :

1. Le trafic sous-réseau local avec aucun saut suivant défini pour autoriser le trafic sous-réseau local à contourner le pare-feu.
2. Trafic réseau virtuel avec un saut suivant défini comme pare-feu ; Ce paramètre remplace la règle par défaut qui autorise virtuel le trafic réseau local acheminer directement.
3. Tout restant le trafic (0/0) avec un saut suivant défini comme le pare-feu.

>[AZURE.TIP] N’ayant ne pas l’entrée sous-réseau local dans la UDR sauts de page communications sous-réseau local. 
> - Dans notre exemple, 10.0.1.0/24 en pointant sur VNETLocal est critique que dans le cas contraire, paquet quitter le serveur Web (10.0.1.4) destiné à un autre serveur local (par exemple) 10.0.1.25 échoue comme elles seront envoyés aux couloirs, qui vous envoie un il au sous-réseau, et le sous-réseau vous ré-l’enverrons aux couloirs et ainsi de suite.
> - Il est probable que d’une boucle de routage est généralement très élevés sur les appareils plusieurs cartes qui sont connectés directement à chaque sous-réseau qu’ils sont communiquer avec, ce qui est souvent traditionnel, en local, appareils électroménagers. 

Une fois que les tables de routage sont créées, elles sont liées à leurs sous-réseaux. La table de routage sous-réseau frontal, une fois créé et lié au sous-réseau, présente comme suit :

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active

>[AZURE.NOTE] UDR peut désormais être appliquée au sous-réseau passerelle sur lequel le circuit ExpressRoute est connecté.
>
> Exemples de l’activation de votre réseau de périmètre avec ExpressRoute ou le réseau de site à sont affichées dans les exemples 3 et 4.


#### <a name="ip-forwarding-description"></a>Description de transfert IP
Transfert IP est une fonctionnalité le Guide UDR. Il s’agit d’un paramètre sur un appareil virtuel qui permet de recevoir le trafic ne sont pas spécifiquement adressé à l’application et puis transférer que le trafic vers sa destination finale.

Par exemple, si le trafic de AppVM01 effectue une requête sur le serveur DNS01, UDR acheminez cela le pare-feu. Avec le routage IP activé, le trafic pour la destination DNS01 (10.0.2.4) est accepté par l’application (10.0.0.4), puis transféré vers sa destination intégrale (10.0.2.4). Sans le routage IP activé sur le pare-feu, le trafic pas accepté par l’application alors que la table de routage comporte le pare-feu comme saut suivant. Pour utiliser un appareil virtuel, il est essentiel de penser à activer le transfert d’IP conjointement avec UDR.

#### <a name="nsg-description"></a>Description NSG
Dans cet exemple, un groupe NSG est créé et puis chargé avec une seule règle. Ce groupe est ensuite lié uniquement aux sous-réseaux principaux et frontaux (pas le SecNet). Manière déclarative est en cours de génération la règle suivante :

- Tout le trafic (tous les ports) à partir d’Internet pour l’ensemble du réseau virtuel (tous les sous-réseaux) est refusé.

Bien que NSGs sont utilisés dans cet exemple, son objectif principal est une couche secondaire de défense par rapport à une mauvaise configuration manuelle. L’objectif consiste à bloquer le trafic entrant à partir d’Internet vers les sous-réseaux frontale ou principale. Le trafic doit passer uniquement par le biais du sous-réseau SecNet vers le pare-feu (puis, le cas échéant, aux sous-réseaux frontale ou principale). En outre, avec les règles UDR en place, tout le trafic qui transformez-les en les sous-réseaux frontale ou principale serait dirigé arrière vers le pare-feu (grâce aux UDR). Le pare-feu serait voir ceci comme flux asymétrique et serait déposez le trafic sortant. Ainsi, il existe trois niveaux de sécurité protection sous-réseaux :
- Aucun point de terminaison ouvert sur le FrontEnd001 et BackEnd001 services ne cloud.
- NSGs refusant le trafic à partir d’Internet.
- La suppression asymétriques le trafic de pare-feu.

Un point intéressant en ce qui concerne la NSG dans cet exemple est qu’elle contient une seule règle, qui consiste à refuser le trafic Internet vers le réseau virtuel entière, y compris le sous-réseau de sécurité. Toutefois, étant donné que la NSG est lié uniquement aux sous-réseaux frontale et principale, la règle n’est pas traitée sur le trafic entrant au sous-réseau sécurité. Par conséquent, le trafic passera au sous-réseau sécurité.

#### <a name="firewall-rules"></a>Règles de pare-feu
Sur le pare-feu, règles de transfert doivent être créés. Dans la mesure où le pare-feu est blocage ou transfert tout entrant, sortant et le trafic réseau internes virtuel, plusieurs règles de pare-feu sont nécessaires. En outre, tout le trafic entrant est atteint l’adresse IP publique de Service de sécurité (sur différents ports), qui sont traitées par le pare-feu. Il est recommandé de créer un diagramme des flux logiques avant de configurer les sous-réseaux et retraitement les règles de pare-feu, afin d’éviter ultérieurement. La figure suivante est une vue logique de règles de pare-feu pour que cet exemple :
 
![Vue logique de règles de pare-feu][10]

>[AZURE.NOTE] En fonction de l’application virtuelle réseau utilisé, les ports de gestion peuvent varier. Dans cet exemple, un pare-feu NextGen Barracuda est référencé, qui utilise les ports 22 et 801 807. Consultez la documentation de fournisseur matériel pour rechercher les ports exacts utilisés pour la gestion de l’appareil utilisé.

#### <a name="firewall-rules-description"></a>Description des règles de pare-feu
Dans le diagramme logique précédent, le sous-réseau sécurité n’est pas affiché. C’est parce que le pare-feu est la seule ressource sur ce sous-réseau, et ce diagramme s’affiche les règles de pare-feu et comment ils logiquement autoriser ou refuser des flux de trafic, pas le chemin routé réel. En outre, les ports externes sélectionnées pour le trafic RDP sont supérieures plage de ports (8014 – 8026) et ont été sélectionnées pour un peu aligner avec les deux derniers octets de l’adresse IP locale pour être plus lisibles (par exemple, l’adresse du serveur local 10.0.1.4 est associé au port externe 8014). Les ports non conflictuelles supérieurs, cependant, peuvent être utilisées.

Dans cet exemple, nous avons besoin de sept types de règles :

- Règles externes (pour le trafic entrant) :
  1.    Règle de gestion des pare-feu : cette application rediriger règle autorise le trafic à passer aux ports de gestion du matériel réseau virtuel.
  2.    Règles RDP (pour chaque Windows server) : ces quatre règles (soit un pour chaque serveur) autorisent la gestion des serveurs individuels via RDP. Cela pourrait également être regroupé dans une règle, selon les fonctionnalités du matériel réseau virtuel en cours d’utilisation.
  3.    Règles de trafic d’application : il existe deux de ces règles, la première pour le trafic web frontaux et la seconde pour le trafic principale (par exemple, serveur web au niveau de données). La configuration de ces règles varie selon l’architecture du réseau (où vos serveurs sont placés) et les flux de trafic (sens les flux de trafic, ainsi que les ports sont utilisés).
      - La première règle permet le trafic d’application réel atteindre le serveur d’applications. Tandis que les autres règles permettent de sécurité et de gestion, règles de trafic d’application sont qu’Autoriser les utilisateurs externes ou des services accéder à l’ou les applications. Dans cet exemple, il est un serveur web sur le port 80. Par conséquent une règle d’application unique pare-feu redirige le trafic entrant vers l’adresse IP externe, à l’adresse IP web les serveurs interne. La session de rediriger le trafic serait traduite via NAT vers le serveur interne.
      - La deuxième règle est la règle principale pour permettre au serveur web communiquer avec le serveur AppVM01 (mais pas AppVM02) via n’importe quel port.
- Règles internes (pour le trafic réseau internes virtuel)
  4.    Sortant règle Internet : cette règle autorise le trafic à partir de n’importe quel réseau à transmettre aux réseaux sélectionnés. Cette règle est généralement une règle par défaut déjà sur le pare-feu, mais dans un état désactivé. Cette règle doit être activée pour que cet exemple.
  5.    Règle DNS : cette règle permet uniquement le trafic DNS (port 53) passer au serveur DNS. La plupart du trafic à partir de la partie frontale au serveur principal est bloqué pour cet environnement. Cette règle permet spécifiquement DNS à partir de n’importe quel sous-réseau local.
  6.    Sous-réseau règle sous-réseau : cette règle est pour permettre à tous les serveurs du sous-réseau principale pour vous connecter à n’importe quel serveur sur le sous-réseau frontal (mais pas l’inverse).
- Règle de sécurité intégrée (pour le trafic qui ne répond pas à un des précédent) :
  7.    Refuser toutes les règles de trafic : cela doit toujours être la règle finale (en termes de priorité) et en tant que tel si un flux de trafic ne correspond pas à un de ce qui précède règles à il sera déplacée par cette règle. Il s’agit d’une règle par défaut et généralement activé. Aucune modification n’est généralement nécessaires.

>[AZURE.TIP] Sur la deuxième règle du trafic de l’application, pour simplifier cet exemple, n’importe quel port est autorisé. Dans un scénario réel, le port plus spécifique et les plages d’adresses doivent être utilisés pour réduire la surface d’attaque de cette règle.

Une fois que toutes les règles précédentes sont créées, il est important de réviser la priorité de chaque règle pour garantir le trafic est autorisé ou refusé comme vous le souhaitez. Dans cet exemple, les règles sont dans l’ordre de priorité.

#### <a name="conclusion"></a>Conclusion
Il s’agit d’un moyen plus complexe, mais plus complète de protection et d’isoler le réseau que les exemples précédents. (Exemple 2 protège simplement l’application et 1 exemple isoler simplement sous-réseaux). Ce modèle permet analyse du trafic dans les deux sens et protège pas seulement le serveur entrant application mais applique la stratégie de sécurité de réseau pour tous les serveurs sur ce réseau. En outre, selon l’appareil utilisé, le trafic complè l’audit et sensibilisation peut être effectué. Pour plus d’informations, consultez les [instructions détaillées build][Example3]. Ces instructions sont les suivantes :

- Découvrez comment créer cet exemple de réseau de périmètre avec des scripts PowerShell.
- Découvrez comment créer cet exemple avec un modèle d’Azure le Gestionnaire de ressources.
- Description détaillée du chaque UDR, NSG commande et la règle de pare-feu.
- Scénarios de flux détaillée du trafic, montrant comment le trafic est autorisé ou refusé dans chaque calque.

### <a name="example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-virtual-private-network-vpn"></a>Exemple 4 : Ajouter une connexion hybride avec un réseau privé virtuel matériel virtuelle, site à site (VPN)
[Retour au début rapide](#fast-start) | Instructions de génération disponibles de détaillées dès

![Réseau de périmètre avec à couloirs connecté réseau hybride][11]

#### <a name="environment-description"></a>Description de l’environnement
Mise en réseau hybride à l’aide d’un appareil virtuel réseau (à couloirs) peut être ajoutée à tous les types de réseau de périmètre décrits dans les exemples 1, 2 ou 3.

Comme le montre l’illustration précédente, une connexion VPN via Internet (site-à-site) est utilisée pour connecter un réseau local à un réseau virtuel Azure via un à couloirs.

>[AZURE.NOTE] Si vous utilisez ExpressRoute avec l’option Peering Public Azure est activée, un itinéraire statique doit être créé. Cela doit acheminer les messages vers l’adresse IP VPN à couloirs votre entreprise Internet et non via le WAN ExpressRoute. Le NAT requis sur l’option ExpressRoute Azure Public Peering peut interrompre la session VPN.

Une fois le réseau privé virtuel est en place, aux couloirs devient le concentrateur central pour tous les réseaux et sous-réseaux. Les règles de transfert de pare-feu déterminent quels flux de trafic est autorisés, traduits via NAT, sont redirigés ou sont supprimées (même pour les flux de trafic entre le réseau local et Azure).

Flux de trafic doivent être considérés avec soin, comme elles peuvent être optimisées ou dégradée à ce modèle de conception, selon la spécifique cas d’utilisation.

À l’aide de l’environnement intégré dans l’exemple 3, puis en ajoutant une connexion de réseau de site à site VPN hybride, génère la conception suivante :

![Réseau de périmètre avec à couloirs connecté à l’aide d’un réseau privé virtuel de site à][12]

Le routeur en local, ou tout autre dispositif de réseau est compatible avec votre à couloirs pour VPN, serait le client VPN. Cet appareil physique serait responsable de lancement et maintenir la connexion VPN avec votre à couloirs.

Logiquement aux couloirs, le réseau ressemble à quatre distinct « zones de sécurité » avec les règles sur l’à couloirs en cours le directeur principal du trafic entre ces zones :

![Réseau logique du point de vue à couloirs][13]

#### <a name="conclusion"></a>Conclusion
L’ajout d’une connexion de réseau de site à VPN hybride à un réseau virtuel Azure peut étendre le réseau local dans Azure de façon sécurisée. En utilisant une connexion VPN, le trafic est chiffré et achemine via Internet. À la couloirs dans cet exemple fournit un emplacement central pour appliquer et gérer la stratégie de sécurité. Pour plus d’informations, consultez les instructions détaillées génération (à venir). Ces instructions sont les suivantes :

- Découvrez comment créer cet exemple de réseau de périmètre avec des scripts PowerShell.
- Découvrez comment créer cet exemple avec un modèle d’Azure le Gestionnaire de ressources.
- Scénarios de flux détaillée du trafic, montrant comment le trafic passe par cette création.

### <a name="example-5-add-a-hybrid-connection-with-a-site-to-site-azure-gateway-vpn"></a>Exemple 5 : Ajouter une connexion hybride avec une site à Azure passerelle VPN
[Retour au début rapide](#fast-start) | Instructions de génération disponibles de détaillées dès

![Réseau de périmètre avec passerelle le réseau connecté hybride][14]

#### <a name="environment-description"></a>Description de l’environnement
Mise en réseau hybride à l’aide d’une passerelle VPN Azure peut être ajouté à des deux types de réseau de périmètre décrit dans les exemples 1 ou 2.

Comme le montre l’illustration précédente, une connexion VPN via Internet (site-à-site) est utilisée pour connecter un réseau local à un réseau virtuel Azure via une passerelle VPN Azure.

>[AZURE.NOTE] Si vous utilisez ExpressRoute avec l’option Peering Public Azure est activée, un itinéraire statique doit être créé. Cela doit acheminer les messages vers l’adresse IP VPN à couloirs votre entreprise Internet et non via le WAN ExpressRoute. Le NAT requis sur l’option ExpressRoute Azure Public Peering peut interrompre la session VPN.

La figure suivante illustre les deux bords réseau dans cette option. Sur le bord premier, l’à couloirs et NSGs le trafic le flux de contrôle des réseaux internes Azure et entre Azure et Internet. Le bord deuxième est la passerelle VPN Azure, qui est un contour réseau complètement séparé et isolées entre en local et Azure.

Flux de trafic doivent être considérés avec soin, comme elles peuvent être optimisées ou dégradée à ce modèle de conception, selon la spécifique cas d’utilisation.

À l’aide de l’environnement intégré dans l’exemple 1, puis en ajoutant une connexion de réseau de site à site VPN hybride, génère la conception suivante :

![Réseau de périmètre avec passerelle connecté à l’aide d’une connexion ExpressRoute][15]

#### <a name="conclusion"></a>Conclusion
L’ajout d’une connexion de réseau de site à VPN hybride à un réseau virtuel Azure peut étendre le réseau local dans Azure de façon sécurisée. À l’aide de la passerelle VPN Azure native, le trafic de votre est IPSec chiffré et achemine via Internet. En outre, à l’aide de la passerelle VPN Azure peut fournir une option de coût (aucune licence supplémentaire de coûts à l’instar de tiers NVAs). Il s’agit plus économique dans l’exemple 1, où aucun à couloirs n’est utilisée. Pour plus d’informations, consultez les instructions détaillées génération (à venir). Ces instructions sont les suivantes :

- Découvrez comment créer cet exemple de réseau de périmètre avec des scripts PowerShell.
- Découvrez comment créer cet exemple avec un modèle d’Azure le Gestionnaire de ressources.
- Scénarios de flux détaillée du trafic, montrant comment le trafic passe par cette création.

### <a name="example-6-add-a-hybrid-connection-with-expressroute"></a>Exemple 6 : Ajouter une connexion hybride avec ExpressRoute
[Retour au début rapide](#fast-start) | Instructions de génération disponibles de détaillées dès

![Réseau de périmètre avec passerelle le réseau connecté hybride][16]

#### <a name="environment-description"></a>Description de l’environnement
Mise en réseau hybride via une connexion homologation privée ExpressRoute peut être ajouté à des deux types de réseau de périmètre décrit dans les exemples 1 ou 2.

Comme le montre l’illustration précédente, ExpressRoute privé peering fournit une connexion entre votre réseau local et le réseau virtuel Azure. Le trafic ne transitent que le réseau de fournisseur de service et le réseau de Microsoft Azure, jamais toucher Internet.

>[AZURE.NOTE] Il existe certaines restrictions lorsque vous utilisez UDR avec ExpressRoute, en raison de la complexité de routage dynamique utilisée dans la passerelle virtuelle Azure. Il s’agit des comme suit :
>
>- UDR ne doit pas être appliqué au sous-réseau passerelle sur lequel la passerelle virtuelle ExpressRoute liée Azure est connectée.
>- La passerelle virtuelle ExpressRoute liée Azure ne peut pas être que le périphérique saut suivant pour les autres UDR lié sous-réseaux.
>
>
<br />

>[AZURE.TIP] À l’aide de ExpressRoute conserve le trafic réseau d’entreprise sur Internet pour renforcer la sécurité et beaucoup de meilleures performances. Il permet également de contrats de niveau de service à partir de votre fournisseur de ExpressRoute. La passerelle Azure peut passer jusqu'à 2 Go/s avec ExpressRoute, alors que VPN de site à la débit maximum Azure passerelle est de 200 Mo/s.

Comme indiqué dans le diagramme suivant, avec cette option l’environnement comporte maintenant deux bords du réseau. L’à couloirs et NSG le trafic le flux de contrôle des réseaux internes Azure et entre Azure et Internet, tandis que la passerelle est un bord réseau complètement séparé et isolées entre en local et Azure.

Flux de trafic doivent être considérés avec soin, comme elles peuvent être optimisées ou dégradée à ce modèle de conception, selon la spécifique cas d’utilisation.

À l’aide de l’environnement intégré dans l’exemple 1, puis en ajoutant une connexion de réseau ExpressRoute hybride, génère la conception suivante :

![Réseau de périmètre avec passerelle connecté à l’aide d’une connexion ExpressRoute][17]

#### <a name="conclusion"></a>Conclusion
L’ajout d’une connexion de réseau privé ExpressRoute Peering pouvez étendre le réseau local dans Azure dans une latence sécurisée, inférieure, exécution d’une valeur supérieure manière. En outre, à l’aide de la passerelle Azure native, comme dans cet exemple, fournit une option de coût (ne plue de gestion des licences à l’instar de tiers NVAs). Pour plus d’informations, consultez les instructions détaillées génération (à venir). Ces instructions sont les suivantes :

- Découvrez comment créer cet exemple de réseau de périmètre avec des scripts PowerShell.
- Découvrez comment créer cet exemple avec un modèle d’Azure le Gestionnaire de ressources.
- Scénarios de flux détaillée du trafic, montrant comment le trafic passe par cette création.

## <a name="references"></a>Références
### <a name="helpful-websites-and-documentation"></a>Documentation et sites Web utiles
- Azure Access avec le Gestionnaire de ressources Azure :
- Accéder à Azure avec PowerShell : [https://azure.microsoft.com/documentation/articles/powershell-install-configure/](./powershell-install-configure.md)
- Documentation de mise en réseau virtuelle : [https://azure.microsoft.com/documentation/services/virtual-network/](https://azure.microsoft.com/documentation/services/virtual-network/)
- Documentation de groupe de sécurité du réseau : [https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/](./virtual-network/virtual-networks-nsg.md)
- Documentation routage définies par l’utilisateur : [https://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/](./virtual-network/virtual-networks-udr-overview.md)
- Azure passerelles virtuels : [https://azure.microsoft.com/documentation/services/vpn-gateway/](https://azure.microsoft.com/documentation/services/vpn-gateway/)
- [Https://azure.microsoft.com/documentation/articles/vpn-gateway-create-site-to-site-rm-powershell](./vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md) VPN de Site à :
- Documentation ExpressRoute (veillez à consulter les sections « Mise en route » et « How To ») : [https://azure.microsoft.com/documentation/services/expressroute/](https://azure.microsoft.com/documentation/services/expressroute/)

<!--Image References-->
[0]: ./media/best-practices-network-security/flowchart.png "Organigramme des Options de sécurité"
[1]: ./media/best-practices-network-security/compliancebadges.png "Badges de conformité Azure"
[2]: ./media/best-practices-network-security/azuresecurityfeatures.png "Fonctionnalités de sécurité Azure"
[3]: ./media/best-practices-network-security/dmzcorporate.png "Une DMZ dans un réseau d’entreprise"
[4]: ./media/best-practices-network-security/azuresecurityarchitecture.png "Architecture de sécurité Azure"
[5]: ./media/best-practices-network-security/dmzazure.png "Une DMZ dans un réseau virtuel Azure"
[6]: ./media/best-practices-network-security/dmzhybrid.png "Réseau hybride avec trois limites de sécurité"
[7]: ./media/best-practices-network-security/example1design.png "Entrant DMZ avec NSG"
[8]: ./media/best-practices-network-security/example2design.png "Entrant DMZ avec à couloirs et NSG"
[9]: ./media/best-practices-network-security/example3design.png "DMZ bidirectionnelle avec à couloirs, NSG et UDR"
[10]: ./media/best-practices-network-security/example3firewalllogical.png "Vue logique de règles de pare-feu"
[11]: ./media/best-practices-network-security/example4designoptions.png "DMZ avec à couloirs connecté réseau hybride"
[12]: ./media/best-practices-network-security/example4designs2s.png "DMZ avec à couloirs connecté à l’aide d’un réseau privé virtuel de Site à"
[13]: ./media/best-practices-network-security/example4networklogical.png "Réseau logique du point de vue à couloirs"
[14]: ./media/best-practices-network-security/example5designoptions.png "DMZ avec passerelle Azure connecté réseau de Site à hybride"
[15]: ./media/best-practices-network-security/example5designs2s.png "DMZ avec Azure passerelle à l’aide de Site à Site VPN"
[16]: ./media/best-practices-network-security/example6designoptions.png "DMZ avec passerelle Azure connecté réseau ExpressRoute hybride"
[17]: ./media/best-practices-network-security/example6designexpressroute.png "DMZ avec passerelle Azure à l’aide d’une connexion ExpressRoute"

<!--Link References-->
[Example1]: ./virtual-network/virtual-networks-dmz-nsg-asm.md
[Example2]: ./virtual-network/virtual-networks-dmz-nsg-fw-asm.md
[Example3]: ./virtual-network/virtual-networks-dmz-nsg-fw-udr-asm.md
[Example4]: ./virtual-network/virtual-networks-hybrid-s2s-nva-asm.md
[Example5]: ./virtual-network/virtual-networks-hybrid-s2s-agw-asm.md
[Example6]: ./virtual-network/virtual-networks-hybrid-expressroute-asm.md
[Example7]: ./virtual-network/virtual-networks-vnet2vnet-direct-asm.md
[Example8]: ./virtual-network/virtual-networks-vnet2vnet-transit-asm.md
