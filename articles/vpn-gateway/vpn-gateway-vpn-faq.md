<properties 
   pageTitle="Réseau virtuel VPN passerelle FAQ | Microsoft Azure"
   description="La passerelle VPN Forum aux questions. Forum aux questions sur les connexions réseau virtuel Microsoft Azure croisée locaux, les connexions de configuration hybride et passerelles VPN"
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/10/2016"
   ms.author="yushwang" />

# <a name="vpn-gateway-faq"></a>Passerelle VPN Forum aux questions

## <a name="connecting-to-virtual-networks"></a>Se connecter à des réseaux virtuels

### <a name="can-i-connect-virtual-networks-in-different-azure-regions"></a>Puis-je me connecter de réseaux virtuels dans différentes régions Azure ?
Oui. En fait, il n’existe aucune contrainte de région. Un réseau virtuel peut se connecter à un autre réseau virtuel dans la même région ou dans une zone différente Azure.

### <a name="can-i-connect-virtual-networks-in-different-subscriptions"></a>Puis-je me connecter de réseaux virtuels dans différents abonnements ?
Oui.

### <a name="can-i-connect-to-multiple-sites-from-a-single-virtual-network"></a>Puis-je me connecter à plusieurs sites à partir d’un seul réseau virtuel ?

Vous pouvez vous connecter à plusieurs sites à l’aide de Windows PowerShell et des API REST Azure. Consultez la section [comportant plusieurs sites et connectivité VNet-VNet](#multi-site-and-vnet-to-vnet-connectivity) Forum aux questions.
## <a name="what-are-my-cross-premises-connection-options"></a>Quelles sont mes options de connexion cross local ?

Les connexions entre sites suivantes sont prises en charge :

- [Site à](vpn-gateway-site-to-site-create.md) : connexion VPN sur IPsec (IKE v1 et v2 IKE). Ce type de connexion nécessite un périphérique VPN ou RRAS.

- [Point-à-Site](vpn-gateway-point-to-site-create.md) – connexion VPN sur SSTP (Secure Socket protocole). Cette connexion ne nécessite pas un périphérique VPN.

- [VNet-VNet](virtual-networks-configure-vnet-to-vnet-connection.md) – ce type de connexion est identique à une configuration de Site à. VNet à VNet est une connexion VPN sur IPsec (IKE v1 et v2 IKE). Il ne nécessite pas un périphérique VPN.

- [Plusieurs sites](vpn-gateway-multi-site.md) – il s’agit de la variation d’une configuration de Site à qui vous permet de vous connecter plusieurs sites en local à un réseau virtuel.

- [ExpressRoute](../expressroute/expressroute-introduction.md) – ExpressRoute est une connexion directe à Azure à partir de votre réseau étendu, pas sur l’Internet public. Consultez [Présentation technique ExpressRoute](../expressroute/expressroute-introduction.md) et le [Forum aux questions sur ExpressRoute](../expressroute/expressroute-faqs.md) pour plus d’informations.

Pour plus d’informations sur les connexions, voir [à propos de VPN passerelle](vpn-gateway-about-vpngateways.md).

### <a name="what-is-the-difference-between-a-site-to-site-connection-and-point-to-site"></a>Quelle est la différence entre une connexion à un Site et Point-à-Site ?

Connexions de **Site à** vous permettent de vous connecter entre les ordinateurs situés dans vos locaux à n’importe quel ordinateur virtuel ou instance de rôle au sein de votre réseau virtuel, selon la façon dont vous choisissez configurer le routage. Il est une option intéressante pour une connexion cross locaux toujours disponibles et est adapté à des configurations hybrides. Ce type de connexion s’appuie sur un appareil IPsec VPN (matériel ou appareil bordures), qui doit être déployé sur le bord de votre réseau. Pour créer ce type de connexion, vous devez disposer de la configuration matérielle requise VPN et une adresse IPv4 tourné vers l’extérieur.

**Point-à-Site** connexions vous permettent de vous connecter à partir d’un seul ordinateur depuis n’importe où sur une autre valeur situé dans votre réseau virtuel. Il utilise le client VPN de boîte aux lettres Windows. Dans le cadre de la configuration du Point-à-Site, vous installez un certificat et un package de configuration de client VPN, qui contient les paramètres qui permettent à votre ordinateur pour vous connecter à n’importe quel ordinateur virtuel ou instance de rôle au sein du réseau virtuel. Il est idéale lorsque vous voulez vous connecter à un réseau virtuel, mais ne sont pas situé en local. Il est également une option intéressante lorsque vous n’avez pas accès à matériel VPN ou une adresse IPv4 tourné vers l’extérieur, qui sont tous deux requis pour une connexion à un Site. 

Vous pouvez configurer votre réseau virtuel utiliser simultanément, Site à et Point-à-Site autant que vous créez votre connexion à un Site à l’aide d’un type VPN basé sur un itinéraire de la passerelle. Types VPN basés sur des itinéraire sont appelés passerelles dynamiques dans le modèle de déploiement classique.

### <a name="what-is-expressroute"></a>Quelles sont les ExpressRoute ?

ExpressRoute vous permet de créer des connexions privées entre centres de données Microsoft et de l’infrastructure dans vos locaux ou dans un environnement de co-création emplacement. Avec ExpressRoute, vous pouvez établir des connexions aux services de cloud Microsoft tels que Microsoft Azure et Office 365 sur un site de collaboration emplacement partenaire ExpressRoute, ou se connecter directement à partir de votre réseau étendu existant (par exemple, un MPLS VPN fournis par un fournisseur de services de réseau).

ExpressRoute connexions offrent une meilleure sécurité, plus la fiabilité, bande passante et latence inférieur que les connexions classiques via Internet. Dans certains cas, à l’aide de connexions ExpressRoute pour transférer des données entre votre réseau local et Azure peut également produire avantages inédite. Si vous avez déjà créé une connexion cross locaux à partir de votre réseau local vers Azure, vous pouvez migrer vers une connexion ExpressRoute tout en conservant votre réseau virtuel.

Consultez le [Forum aux questions sur ExpressRoute](../expressroute/expressroute-faqs.md) pour plus d’informations.

## <a name="site-to-site-connections-and-vpn-devices"></a>Connexions site à Site et périphériques VPN

### <a name="what-should-i-consider-when-selecting-a-vpn-device"></a>Que dois-je prendre en compte lors de la sélection d’un périphérique VPN ?

Nous avons validé un ensemble de périphériques VPN de Site à standard en partenariat avec des fournisseurs de l’appareil. Une liste des périphériques VPN compatibles connus, leurs instructions de configuration correspondantes ou des exemples et caractéristiques du périphérique sont accessibles [ici](vpn-gateway-about-vpn-devices.md). Tous les périphériques dans les familles appareil répertoriés en tant que compatible connu doivent collaborer avec réseau virtuel. Pour aider à configurer votre appareil VPN, reportez-vous à l’exemple de configuration de périphérique ou le lien qui correspond à la famille périphérique approprié.

### <a name="what-do-i-do-if-i-have-a-vpn-device-that-isnt-in-the-known-compatible-device-list"></a>Que faire si j’utilise un périphérique VPN qui ne figure pas dans la liste des périphériques compatibles connus ?

Si vous ne voyez pas votre périphérique est répertorié comme un périphérique VPN compatible et que vous voulez utiliser pour votre connexion VPN, vous devez vérifier qu’il répond à la prise en charge IPsec/IKE configuration options et paramètres répertoriés [ici](vpn-gateway-about-vpn-devices.md#devices-not-on-the-compatible-list). Appareils la configuration minimale requise de la réunion fonctionne parfaitement avec les passerelles VPN. Contactez le fabricant de votre appareil pour obtenir des instructions supplémentaires prise en charge et la configuration.

### <a name="why-does-my-policy-based-vpn-tunnel-go-down-when-traffic-is-idle"></a>Pourquoi mon tunnel VPN basée a-t-elle vers le bas lorsque le trafic est inactif ?

Ce comportement est attendu pour le routage par stratégie (également appelé statique) passerelles VPN. Lorsque le trafic via le tunnel est inactif pendant plus de 5 minutes, le tunnel sera supprimé vers le bas. Lorsque le trafic démarre s’étalant dans les deux sens, le tunnel sera rétabli immédiatement.

### <a name="can-i-use-software-vpns-to-connect-to-azure"></a>Puis-je utiliser le logiciel VPN pour vous connecter à Azure ?

Nous prend en charge le routage Windows Server 2012 et les serveurs d’accès distant (RRAS) pour une configuration Site à effectuer locaux.

Autres solutions VPN logiciel doivent collaborer avec notre passerelle dans la mesure où ils sont conformes aux mises en œuvre IPsec standard du secteur. Contactez le fournisseur du logiciel pour obtenir des instructions de configuration et la prise en charge.

## <a name="point-to-site-connections"></a>Connexions point-à-Site

### <a name="what-operating-systems-can-i-use-with-point-to-site"></a>Quels sont les systèmes d’exploitation puis-je utiliser avec Point-à-Site ?

Les systèmes d’exploitation suivants sont pris en charge :

- Windows 7 (32 bits et 64 bits)

- Windows Server 2008 R2 (64 bits uniquement)

- Windows 8 (32 bits et 64 bits)

- Windows 8.1 (32 bits et 64 bits)

- Windows Server 2012 (64 bits uniquement)

- Windows Server 2012 R2 (64 bits uniquement)

- Windows 10

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp"></a>Puis-je utiliser n’importe quel client VPN logiciel pour Point au Site qui prend en charge SSTP ?

Non. Prise en charge est limitée uniquement aux versions de système d’exploitation Windows répertoriées ci-dessus.

### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>Points de terminaison de client VPN combien puis-je avoir dans ma configuration Point-à-Site ?

Nous prenons en charge jusqu'à 128 clients VPN pour pouvoir vous connecter à un réseau virtuel en même temps.

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Puis-je utiliser mon propre autorité de certification de racine infrastructure de clé publique interne pour la connectivité Point-à-Site ?

Oui. Précédemment, seuls les certificats racine auto-signé peuvent être utilisées. Vous pouvez toujours télécharger 20 certificats racine.

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>Puis-je Parcourir proxys et pare-feu à l’aide de la fonctionnalité de Point-à-Site ?

Oui. Nous utilisons SSTP (Secure Socket protocole) au tunnel au moyen de pare-feu. Ce tunnel s’affichent sous forme d’une connexion HTTPs.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Si le redémarrage d’un ordinateur client configuré pour Point-à-Site, le réseau privé virtuel se reconnectera automatiquement ?

Par défaut, l’ordinateur client va rétablir pas automatiquement la connexion VPN.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>Est prise en charge de Point-à-Site reconnexion automatique et DDNS sur les clients VPN ?

Reconnexion automatique et DDNS actuellement pas pris en charge VPN Point-à-Site.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>Puis-je avoir Site à et configurations Point-à-Site coexistent pour le même réseau virtuel ?

Oui. Ces deux solutions fonctionnera que si vous disposez de type RouteBased VPN de la passerelle. Pour le modèle de déploiement classique, vous avez besoin d’une passerelle dynamique. Nous ne faisons pas prise en charge Point-à-Site pour des passerelles VPN routage statiques ou des passerelles à l’aide de - VpnType PolicyBased.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Puis-je configurer un client Point-à-Site pour vous connecter à plusieurs réseaux virtuels en même temps ?

Oui, il est possible. Mais les réseaux virtuels ne peuvent pas porter superposés IP préfixes et les espaces d’adressage Point-à-Site doivent se chevauchent pas entre les réseaux virtuels.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Quantité débit puis-je attendre via des connexions à un Site ou Point-à-Site ?

Il est difficile à maintenir le débit exact de la tunnel VPN. IPsec et SSTP sont protocoles VPN et au lourds. Débit est également limité par la latence et la bande passante entre vos locaux et Internet.

## <a name="gateways"></a>Passerelles

### <a name="what-is-a-policy-based-static-routing-gateway"></a>Qu’est une passerelle (routage statique) basée sur des règles ?

Les passerelles basées sur une stratégie de mise en œuvre de VPN basée sur des règles. VPN stratégie chiffrement et dirigent paquets via tunnel IPsec basée sur les combinaisons de préfixes d’adresses entre votre réseau local et le VNet Azure. La stratégie (ou le trafic sélecteur) est généralement définie comme une liste d’accès dans la configuration VPN.

### <a name="what-is-a-route-based-dynamic-routing-gateway"></a>Qu’est une gamme (routage dynamique) passerelle ?

Les passerelles basées sur itinéraire implémentent les VPN basée sur un itinéraire. VPN basée sur un itinéraire utilisent « itinéraires » dans la période d’enquête transfert ou le routage de table pour les paquets directs dans leurs interfaces tunnel correspondantes. Les interfaces tunnel puis chiffrer ou déchiffrement les paquets déconnecter le tunnel. Le sélecteur de stratégie ou le trafic de réseau VPN itinéraire en fonction sont configurés comme pour tout (ou les caractères génériques).

### <a name="can-i-get-my-vpn-gateway-ip-address-before-i-create-it"></a>Puis-je obtenir mon adresse IP de passerelle VPN avant de créer il ?

Non. Vous devez créer votre passerelle tout d’abord pour obtenir l’adresse IP. L’adresse IP change si vous supprimez et recréez votre passerelle VPN.

### <a name="how-does-my-vpn-tunnel-get-authenticated"></a>Comment mon tunnel VPN s’authentifier ?

Azure VPN utilise l’authentification PSK (-clé). Nous générer une clé communiquée (PSK) lorsque vous créez le tunnel VPN. Vous pouvez modifier cette clé générée automatiquement à votre propre avec l’applet de commande PowerShell de clé communiquée à l’avance de définir ou API REST.

### <a name="can-i-use-the-set-pre-shared-key-api-to-configure-my-policy-based-static-routing-gateway-vpn"></a>Puis-je utiliser l’API définir de clé communiquée à l’avance pour configurer mon basée (routage statique) passerelle VPN ?

Oui, l’applet de commande PowerShell et de définir des API de clé communiquée à l’avance peut servir à configurer Azure (statiques) VPN basée sur des règles et basée sur l’itinéraire de VPN routage (dynamiques).

### <a name="can-i-use-other-authentication-options"></a>Puis-je utiliser d’autres options d’authentification ?

Nous sommes limités à l’aide des touches partagées (PSK) pour l’authentification.

### <a name="what-is-the-gateway-subnet-and-why-is-it-needed"></a>Quelle est la « sous-réseau passerelle » et pourquoi est-elle nécessaire ?

Nous avons un service passerelle que nous exécuter pour activer la connectivité entre locaux. 

Vous devez créer un sous-réseau passerelle pour votre VNet configurer une passerelle VPN. Tous les sous-réseaux passerelle doivent être nommés GatewaySubnet fonctionne correctement. Ne nommez votre sous-réseau passerelle autre chose. Et ne déployer des machines virtuelles ou tout autre élément au sous-réseau passerelle.

La taille minimale de passerelle sous-réseau dépend entièrement de la configuration que vous souhaitez créer. Bien qu’il soit possible de créer un sous-réseau passerelle aussi petite que /29 pour certaines configurations, nous vous recommandons de créer un sous-réseau passerelle de /28 ou plus grand (/ 28, /27, /26 etc..). 

### <a name="can-i-deploy-virtual-machines-or-role-instances-to-my-gateway-subnet"></a>Puis-je déployer des Machines virtuelles ou des instances de rôle sur mon sous-réseau passerelle ?

Non.

### <a name="how-do-i-specify-which-traffic-goes-through-the-vpn-gateway"></a>Comment spécifier qui ouvre le trafic via la passerelle VPN ?

Si vous utilisez le portail classique Azure, ajoutez chaque plage que vous souhaitez envoyé via la passerelle pour votre réseau dans la page réseaux sous réseaux locaux virtuel.

### <a name="can-i-configure-forced-tunneling"></a>Puis-je configurer forcé tunnel ?

Oui. Voir [configurer forcé tunnel](vpn-gateway-about-forced-tunneling.md).

### <a name="can-i-set-up-my-own-vpn-server-in-azure-and-use-it-to-connect-to-my-on-premises-network"></a>Puis-je configurer mon propre serveur VPN dans Azure et utilisez-le pour vous connecter à mon réseau local ?

Oui, vous pouvez déployer votre propre passerelles VPN ou des serveurs dans Azure à partir de la Azure Marketplace ou créer votre propre routeurs VPN. Vous devrez configurer des itinéraires définis par l’utilisateur de votre réseau virtuel pour garantir le trafic est acheminé correctement entre vos réseaux en local et sous-réseaux de votre réseau virtuel.

### <a name="why-are-certain-ports-opened-on-my-vpn-gateway"></a>Pourquoi certains ports ouverts sur mon passerelle VPN ?

Ils sont requis pour les communications Azure infrastructure. Ils sont protégés (verrouillé) par certificats Azure. Sans certificats NOMPROPRE, entités externes, y compris les clients de ces passerelles ne sera pas en mesure d’entraîner aucun effet sur les points de terminaison.

Une passerelle VPN est fondamentalement un appareil multi-hébergés avec une sélection de carte réseau dans le réseau des clients privée et une carte réseau les en regard du réseau public. Entités Azure infrastructure ne peut pas tirer parti de réseaux privés client pour des raisons de conformité, sorte qu’ils utilisent les points de terminaison publics pour la communication d’infrastructure. Les points de terminaison publics sont régulièrement analysés par audit de sécurité Azure.


### <a name="more-information-about-gateway-types-requirements-and-throughput"></a>Plus d’informations sur les types de passerelle, les exigences et débit

Pour plus d’informations, voir à [Propos des paramètres VPN passerelle](vpn-gateway-about-vpn-gateway-settings.md).

## <a name="multi-site-and-vnet-to-vnet-connectivity"></a>Plusieurs sites et connectivité VNet-VNet

### <a name="which-type-of-gateways-can-support-multi-site-and-vnet-to-vnet-connectivity"></a>Quel type de passerelles peut prendre en charge plusieurs sites et VNet-VNet connectivité ?

Uniquement en fonction itinéraire VPN (routage dynamique).

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>Puis-je connecter un VNet à un Type de VPN RouteBased à un autre VNet à un type de PolicyBased VPN ?

Non, les deux réseaux virtuels doivent utiliser basée sur un itinéraire (routage dynamique) VPN.

### <a name="is-the-vnet-to-vnet-traffic-secure"></a>Le trafic VNet-VNet est sécurisé ?

Oui, il est protégé par le chiffrement IPsec/IKE.

### <a name="does-vnet-to-vnet-traffic-travel-over-the-azure-backbone"></a>Le trafic VNet-VNet voyage via le réseau principal Azure ?

Oui.

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>Le nombre de sites en local et réseaux virtuels un réseau virtuel puissent participer aux ?

Max. 10 combinées pour les passerelles Basic et le routage dynamique Standard ; 30 pour les passerelles haute Performance VPN.

### <a name="can-i-use-point-to-site-vpns-with-my-virtual-network-with-multiple-vpn-tunnels"></a>J’ai peut utiliser Point-à-Site VPN avec mon réseau virtuel avec plusieurs tunnel VPN ?

Oui, VPN Point-à-Site (P2S) peuvent être utilisés avec les passerelles VPN se connecter à plusieurs sites en local et d’autres réseaux virtuel.

### <a name="can-i-configure-multiple-tunnels-between-my-virtual-network-and-my-on-premises-site-using-multi-site-vpn"></a>Puis-je configurer plusieurs tunnel entre mon réseau virtuel et mon site local à l’aide de plusieurs sites VPN ?

Non, redondantes tunnel entre un réseau virtuel Azure et un site local n’est pas pris en charge.

### <a name="can-there-be-overlapping-address-spaces-among-the-connected-virtual-networks-and-on-premises-local-sites"></a>Peuvent il se superposer espaces d’adressage parmi les réseaux virtuels connectés et les sites locaux en local ?

Non. Chevauchement des espaces d’adressage entraînera le téléchargement de fichier de configuration de réseau ou « Création de réseau virtuel « échec.

### <a name="do-i-get-more-bandwidth-with-more-site-to-site-vpns-than-for-a-single-virtual-network"></a>Je reçois plus de bande passante plus VPN de Site à que pour un seul réseau virtuel

Non, toutes les tunnel VPN, y compris les Point-à-Site VPN, partage la même passerelle VPN Azure et la bande passante disponible.

### <a name="can-i-use-azure-vpn-gateway-to-transit-traffic-between-my-on-premises-sites-or-to-another-virtual-network"></a>Puis-je utiliser passerelle VPN Azure pour le trafic de transit entre les sites Mes sites en local ou à un autre réseau virtuel ?

**Modèle de déploiement classique**<br>
Voies le trafic via une passerelle VPN Azure est possible en utilisant le modèle de déploiement classique, mais s’appuie sur les espaces d’adressage statique défini dans le fichier de configuration réseau. BGP n’est pas encore prise en charge des réseaux virtuels Azure passerelles VPN à l’aide du modèle de déploiement classique. Sans BGP, définissant manuellement des espaces d’adressage voies est très source d’erreurs et non recommandé.<br>
**Modèle de déploiement Gestionnaire de ressources**<br>
Si vous utilisez le modèle de déploiement du Gestionnaire de ressources, consultez la section [BGP](#bgp) pour plus d’informations.

### <a name="does-azure-generate-the-same-ipsecike-pre-shared-key-for-all-my-vpn-connections-for-the-same-virtual-network"></a>Azure génère la même clé partagée IPsec/IKE pour toutes les connexions de mon VPN pour le même réseau virtuel ?

Non, Azure par défaut génère différentes clés pour des connexions VPN différentes. Toutefois, vous pouvez utiliser l’applet de commande PowerShell ou de définir des API REST VPN passerelle clé pour définir la valeur de clé que vous préférez. La clé doit être alphanumérique chaîne de longueur comprise entre 1 à 128 caractères.

### <a name="does-azure-charge-for-traffic-between-virtual-networks"></a>Azure facture pour le trafic entre les réseaux virtuels ?

Pour le trafic entre les différents réseaux virtuels Azure, Azure frais uniquement pour le trafic parcourir d’une région Azure à l’autre. Le taux de frais est répertorié dans la page Azure [VPN passerelle tarifs](https://azure.microsoft.com/pricing/details/vpn-gateway/) .


### <a name="can-i-connect-a-virtual-network-with-ipsec-vpns-to-my-expressroute-circuit"></a>Puis-je me connecter un réseau virtuel avec IPsec VPN à mon circuit ExpressRoute ?

Oui, il est pris en charge. Pour plus d’informations, voir [configurer ExpressRoute et connexions VPN de Site à qui coexistent](../expressroute/expressroute-howto-coexist-classic.md).

## <a name="bgp"></a>BGP

[AZURE.INCLUDE [vpn-gateway-bgp-faq-include](../../includes/vpn-gateway-bpg-faq-include.md)] 



## <a name="cross-premises-connectivity-and-vms"></a>Connectivité entre locaux et des ordinateurs virtuels

### <a name="if-my-virtual-machine-is-in-a-virtual-network-and-i-have-a-cross-premises-connection-how-should-i-connect-to-the-vm"></a>Si ma machine virtuelle se trouve dans un réseau virtuel et que j’ai une connexion cross local, comment dois-je me connecter à la machine virtuelle ?

Plusieurs options s’offrent à vous. Si vous avez RDP activé et que vous avez créé un point de terminaison, vous pouvez vous connecter à votre machine virtuelle à l’aide de l’adresse VIP. Dans ce cas, vous devez spécifier l’adresse IP virtuelle et le port que vous voulez vous connecter. Vous devez configurer le port sur votre ordinateur virtuel pour le trafic. En règle générale, vous accédez au portail classique Azure et enregistrer les paramètres pour la connexion RDP sur votre ordinateur. Les paramètres contiennent les informations de connexion nécessaires.

Si vous avez un réseau virtuel avec la connectivité entre locaux configurée, vous pouvez vous connecter à votre machine virtuelle en utilisant le fondu interne ou l’adresse IP privée. Vous pouvez également vous connecter à votre machine virtuelle en fondu interne à partir d’un autre ordinateur virtuel qui se trouve sur le même réseau virtuel. Vous ne pouvez pas RDP à votre machine virtuelle en utilisant le fondu si vous vous connectez à partir d’un emplacement en dehors de votre réseau virtuel. Par exemple, si vous avez un réseau virtuel Point-à-Site configuré et vous n’établir une connexion à partir de votre ordinateur, vous ne pouvez pas vous connecter à la machine virtuelle en fondu.

### <a name="if-my-virtual-machine-is-in-a-virtual-network-with-cross-premises-connectivity-does-all-the-traffic-from-my-vm-go-through-that-connection"></a>Si ma machine virtuelle se trouve dans un réseau virtuel avec la connectivité entre local, tout le trafic à partir de mon ordinateur virtuel a-t-elle via cette connexion ?

Non. Seul le trafic qui comporte une destination IP se trouvant dans les plages d’adresses IP du réseau Local réseau virtuel que vous avez spécifié accède via la passerelle réseau virtuel. Le trafic a une destination IP situé au sein du réseau virtuel reste au sein du réseau virtuel. Le reste du trafic est envoyé via l’équilibrage de charge vers les réseaux publics, ou si tunnel forcé est utilisée, envoyé via la passerelle VPN Azure. Si vous effectuez un dépannage, il est important de vous assurer que vous disposez de toutes les plages répertoriées dans votre réseau Local que vous voulez envoyer via la passerelle. Vérifiez que les plages d’adresses réseau Local ne se chevauchent pas d’un des plages d’adresses dans le réseau virtuel. En outre, vous souhaitez vérifier que le serveur DNS que vous utilisez est résout le nom à l’adresse IP appropriée.


## <a name="virtual-network-faq"></a>Réseau virtuel Forum aux questions

Vous permet d’afficher des informations réseau virtuel supplémentaires dans le [Forum aux questions sur réseau virtuel](../virtual-network/virtual-networks-faq.md).
 
