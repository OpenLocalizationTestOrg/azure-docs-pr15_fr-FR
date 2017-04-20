## <a name="virtual-network-basics"></a>Concepts de base réseau virtuel

### <a name="what-is-an-azure-virtual-network-vnet"></a>Qu’est un réseau virtuel Azure (VNet) ?

Vous pouvez utiliser VNets mise en service et de gestion des réseaux privés virtuels (VPN) de Azure et, vous pouvez également lier la VNets avec d’autres VNets dans Azure, ou vos localement l’infrastructure pour créer des solutions hybride ou croisée locaux. Chaque VNet que vous créez possède son propre bloc CIDR et peut être lié à d’autres réseaux VNets et en local dans la mesure où les blocs CIDR ne pas entrent en conflit. Vous avez également des contrôles de paramètres du serveur DNS pour VNets et segmentation de la VNet en sous-réseaux.

Utilisez VNets à :

- Créer un réseau privé dédié exclusivement le nuage, virtuel

    Parfois vous ne nécessitent pas une configuration croisée locaux pour votre solution. Lorsque vous créez un VNet, vos services et machines virtuelles au sein de votre VNet peuvent communiquer directement et en toute sécurité entre eux dans le cloud. Cela conserve le trafic en toute sécurité au sein de la VNet, mais toujours vous permet de configurer des connexions de point de terminaison pour les ordinateurs virtuels et les services qui requièrent une communication Internet dans le cadre de votre solution.

- Étendre en toute sécurité votre centre de données

    Avec VNets, vous pouvez générer traditionnels VPN (S2S) de site à pour ajuster la taille en toute sécurité la capacité de votre centre de données. S2S VPN utilisent IPSEC pour fournir une connexion sécurisée entre votre passerelle VPN d’entreprise et Azure.

- Activer les scénarios de cloud hybride

    VNets vous permettent de gérer un éventail de scénarios de cloud hybride. Vous pouvez connecter de façon sécurisée applications basées sur le cloud à n’importe quel type de système local tels que des ordinateurs centraux et les systèmes Unix.

### <a name="how-do-i-know-if-i-need-a-virtual-network"></a>Comment savoir si j’ai besoin d’un réseau virtuel ?

Consultez la [Vue d’ensemble du réseau virtuel](../articles/virtual-network/virtual-networks-overview.md) pour obtenir une table de décision qui vous permet de déterminer la meilleure option de création de réseau pour vous.

### <a name="how-do-i-get-started"></a>Comment puis-je commencer ?

Consultez [la documentation de réseau virtuel](https://azure.microsoft.com/documentation/services/virtual-network/) pour commencer. Cette page comporte des liens vers des communs des étapes de configuration ainsi que des informations qui vous aidera à comprendre les choses que vous devez prendre en considération lors de la conception de votre réseau virtuel.

### <a name="what-services-can-i-use-with-vnets"></a>À quels services puis-je utiliser avec VNets ?

VNets peut être utilisé avec une variété de différents services Azure, tels que les Services en nuage (PaaS) Machines virtuelles et applications Web. Toutefois, il existe plusieurs services qui ne sont pas prises en charge sur une VNet. Veuillez vérifier le service spécifique que vous souhaitez utiliser et vérifiez qu’il est compatible.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>Puis-je utiliser VNets sans connectivité entre local ?

Oui. Vous pouvez utiliser un VNet sans en utilisant la connectivité à un site. Ceci est particulièrement utile si vous souhaitez exécuter le domaine et batteries de serveurs SharePoint dans Azure.

## <a name="virtual-network-configuration"></a>Configuration du réseau virtuel

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Quels outils puis-je utiliser pour créer un VNet ?

Vous pouvez utiliser les outils suivants pour créer ou configurer un réseau virtuel :

- Portail Azure (pour classique et VNets Gestionnaire de ressources).

- Fichier de configuration réseau (netcfg - pour VNets classique uniquement). Consultez [configurer un réseau virtuel à l’aide d’un fichier de configuration réseau](../articles/virtual-network/virtual-networks-using-network-configuration-file.md).

- PowerShell (pour classique et VNets Gestionnaire de ressources).

- Azure infrastructure du langage commun (pour classique et VNets Gestionnaire de ressources).

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Les plages d’adresses puis-je utiliser dans mon VNets ?

Vous pouvez utiliser les plages d’adresses IP publics et une plage d’adresses IP défini dans [RFC 1918](http://tools.ietf.org/html/rfc1918).

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>Puis-je avoir des adresses IP publiques dans mon VNets ?

Oui. Pour plus d’informations sur les plages d’adresses IP publics, voir [espace d’adressage IP publique dans un réseau virtuel (VNet)](../articles/virtual-network/virtual-networks-public-ip-within-vnet.md). N’oubliez pas que votre public adresses IP ne sera pas directement accessible à partir d’Internet.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-virtual-network"></a>Y a-t-il une limite au nombre de sous-réseaux dans mon réseau virtuel ?

Il n’existe aucune limite sur le nombre de sous réseaux que vous utilisez un VNet. Tous les sous-réseaux doivent être entièrement contenus dans l’espace d’adressage réseau virtuel et doivent se chevauchent pas entre eux.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Existe-t-il des restrictions sur l’utilisation des adresses IP au sein des sous-réseaux ?

Azure réserve certaines adresses IP au sein de chaque sous-réseau. Les adresses IP premier et derniers des sous-réseaux sont réservés pour la conformité protocole, ainsi que de 3 adresses plus utilisées pour les services Azure.

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Comment les petites et la taille peuvent VNets et des sous réseaux être ?

Le plus petit que nous prenons en charge constitue un /29 et le plus grand est un /8 (à l’aide des définitions de sous-réseau CIDR).

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>Puis-je importer mes réseaux virtuels vers Azure à l’aide de VNets ?

Non. VNets sont superpositions Layer 3. Azure ne prend pas en charge la sémantique quelconque Layer 2.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>Puis-je spécifier des stratégies de routage personnalisés sur mon VNets et sous-réseaux ?

Oui. Vous pouvez utiliser le routage définies par l’utilisateur (UDR). Pour plus d’informations sur UDR, visitez [itinéraires définies par l’utilisateur et transfert IP](../articles/virtual-network/virtual-networks-udr-overview.md).

### <a name="do-vnets-support-multicast-or-broadcast"></a>VNets prennent en charge de multidiffusion ou de diffusion ?

Non. Nous ne prennent pas en charge multidiffusion ou de diffusion.

### <a name="what-protocols-can-i-use-within-vnets"></a>Quels protocoles puis-je utiliser dans VNets ?

Vous pouvez utiliser des protocoles IP standard dans VNets. Toutefois, multidiffusion, diffusion, IP dans IP paquets encapsulés et générique routage (GRE Encapsulation) sont bloqués dans VNets. Les protocoles standards qui fonctionnent incluent :

- TCP
- UDP
- ICMP

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>Puis-je ping mon routeurs par défaut dans un VNet ?

Non.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Puis-je utiliser tracert pour diagnostiquer connectivité ?

Non.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>Puis-je ajouter des sous-réseaux après avoir créé le VNet ?

Oui. Sous-réseaux peuvent être ajoutés à VNets à tout moment dans la mesure où l’adresse ne fait pas partie d’un autre sous-réseau dans la VNet.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>Puis-je modifier la taille de mon sous-réseau après que j’ai créé ?

Vous pouvez ajouter, supprimer, développer ou réduire un sous-réseau si aucun machines virtuelles ou services déployés à l’aide des applets de commande PowerShell ou le fichier NETCFG qu’il contient. Vous pouvez également ajouter, supprimer, développer ou réduire tous les préfixes dans la mesure où les sous-réseaux qui contiennent des machines virtuelles ou services ne sont pas concernés par la modification.

### <a name="can-i-modify-subnets-after-i-created-them"></a>Puis-je modifier sous-réseaux une fois que j’ai créé les ?

Oui. Vous pouvez ajouter, supprimer et modifier les blocs CIDR utilisés par un VNet.

### <a name="can-i-connect-to-the-internet-if-i-am-running-my-services-in-a-vnet"></a>Puis-je me connecter à internet si j’exécute mes services dans un VNet ?

Oui. Tous les services déployées au sein d’un VNet peuvent vous connecter à internet. Chaque service cloud déployée dans Azure a un VIP publiquement dédié est affecté à. Vous devrez définir des points de terminaison d’entrée pour les rôles PaaS et des points de terminaison pour machines virtuelles activer ces services accepter les connexions à partir d’internet.

### <a name="do-vnets-support-ipv6"></a>VNets prennent en charge du protocole IPv6 ?

Non. Vous ne pouvez pas utiliser le protocole IPv6 avec VNets pour le moment.

### <a name="can-a-vnet-span-regions"></a>Un VNet peut couvrir régions ?

Non. Un VNet est limité à une seule région.

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Puis-je me connecter un VNet à un autre VNet dans Azure ?

Oui. Vous pouvez créer VNet VNet communication à l’aide des API REST ou Windows PowerShell. Vous pouvez également connecter VNets via VNet Peering. Afficher plus de détails peering [ici.](../articles/virtual-network/virtual-network-peering-overview.md)

## <a name="name-resolution-dns"></a>Résolution de noms (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Quelles sont mes options de DNS pour VNets ?

Consultez le tableau dans la page de [Résolution de noms pour machines virtuelles et des Instances de rôle](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) et vous guide dans toutes les options de DNS disponibles.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>Puis-je spécifier des serveurs DNS pour un VNet ?

Oui. Vous pouvez spécifier des adresses IP du serveur DNS dans les paramètres VNet. Cela sera appliqué en tant que l’ou les serveurs DNS par défaut pour tous les ordinateurs virtuels dans le VNet.

### <a name="how-many-dns-servers-can-i-specify"></a>Combien de serveurs DNS puis-je spécifier ?

Vous pouvez spécifier jusqu'à 12 serveurs DNS.

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>Puis-je modifier mes serveurs DNS une fois que j’ai créé le réseau ?

Oui. Vous pouvez modifier la liste des serveurs DNS pour votre VNet à tout moment. Si vous modifiez votre liste de serveurs DNS, vous devez redémarrer chacun des ordinateurs virtuels dans votre VNet tapées à Décrochez le nouveau serveur DNS.


### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>Qu’est DNS fournies par Azure et avec VNets cela fonctionne-t-il ?

DNS fournies par Azure est un service DNS client multiples offert par Microsoft. Azure enregistre toutes vos machines virtuelles et les instances de rôles dans ce service. Ce service fournit la résolution de noms par nom d’hôte de machines virtuelles et des instances de rôle contenus dans le même service cloud et par nom de domaine complet de machines virtuelles et des instances de rôle dans la même VNet.

> [AZURE.NOTE] Il existe une limitation pour l’instant aux services 100 cloud premières dans le réseau virtuel pour résoudre les noms de client de manière croisée à l’aide de DNS fournies par Azure. Si vous utilisez votre propre serveur DNS, cette limitation ne s’applique pas.

### <a name="can-i-override-my-dns-settings-on-a-per-vm--service-basis"></a>Puis-je remplacer mes paramètres DNS sur par virtuelle / base du service ?

Oui. Vous pouvez configurer des serveurs DNS sur une base de service par cloud pour remplacer les paramètres de réseau par défaut. Toutefois, nous vous recommandons d’utiliser DNS au niveau du réseau autant que possible.

### <a name="can-i-bring-my-own-dns-suffix"></a>Puis-je importer mon propre suffixe DNS ?

Non. Vous ne pouvez pas spécifier un suffixe DNS personnalisé pour votre VNets.

## <a name="vnets-and-vms"></a>VNets et machines virtuelles

### <a name="can-i-deploy-vms-to-a-vnet"></a>Puis-je déployer des machines virtuelles sur un VNet ?

Oui.

### <a name="can-i-deploy-linux-vms-to-a-vnet"></a>Puis-je déployer Linux machines virtuelles sur un VNet ?

Oui. Vous pouvez déployer les distro de Linux pris en charge par Azure.

### <a name="what-is-the-difference-between-a-public-vip-and-an-internal-ip-address"></a>Quelle est la différence entre un VIP publique et une adresse IP interne ?

- Une adresse IP interne est une adresse IP qui est affectée à chaque machine virtuelle au sein d’un VNet par DHCP. Il n’est pas public public. Si vous avez créé un VNet, l’adresse IP interne est affectée à partir de la plage que vous avez spécifié dans les paramètres de sous-réseau de votre VNet. Si vous n’avez pas une VNet, une adresse IP interne doivent toujours être affectée. L’adresse IP interne restera avec la machine virtuelle pour sa durée de vie, à moins d’avoir qui est libéré machine virtuelle.

- Un VIP publique est l’adresse IP publique est affectée à votre équilibrage cloud service ou les charger. Il n’est pas affecté directement à votre NIC. VM L’adresse VIP reste avec le service de nuage qu'il est affecté à jusqu'à ce que tous les ordinateurs virtuels dans la mesure où des services cloud sont libéré ou supprimé. À ce stade, il est publié.

### <a name="what-ip-address-will-my-vm-receive"></a>Quelle adresse IP mon machine virtuelle reçoivent-ils ?

- **Adresse IP interne :** Si vous déployez une machine virtuelle sur un VNet, la machine virtuelle reçoit une adresse IP interne à partir d’un pool d’adresses IP internes que vous spécifiez. Machines virtuelles permet de communiquer au sein de la VNets à l’aide des adresses IP internes. Bien que Azure attribue une adresse IP interne dynamique, vous pouvez demander une adresse statique pour votre machine virtuelle. Pour en savoir plus sur les adresses IP internes statiques, visitez [comment définir une adresse IP interne statique](../articles/virtual-network/virtual-networks-reserved-private-ip.md).

- **VIP-** Votre machine virtuelle est également associé à une adresse VIP, même si un VIP n’est jamais assigné directement à la machine virtuelle. Une adresse VIP est une adresse IP publique qui peut être affectée à votre service cloud. Vous pouvez, si vous le souhaitez, réserver un VIP pour votre service cloud.

- **ILPIP-** Vous pouvez également configurer une adresse IP publique au niveau de l’instance (ILPIP). ILPIPs sont directement associé à la machine virtuelle, plutôt que les services cloud. Pour en savoir plus sur ILPIPs, visitez [L’occurrence de vue d’ensemble IP Public](../articles/virtual-network/virtual-networks-instance-level-public-ip.md).

### <a name="can-i-reserve-an-internal-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>Puis-je réserver une adresse IP interne d’un ordinateur virtuel que j’ai créerez ultérieurement ?

Non. Vous ne pouvez pas réserver une adresse IP interne. Si une adresse IP interne n’est disponible qu’il doivent être affectée à une instance de machine virtuelle ou rôle par celui-ci. Cette dernière peut ou peut ne pas être celui que vous souhaitez que l’adresse IP interne pour être assignée à. Vous pouvez, cependant, modifier l’adresse IP interne d’un ordinateur virtuel déjà créé à l’adresse IP interne disponible.

### <a name="do-internal-ip-addresses-change-for-vms-in-a-vnet"></a>Effectuez la modification d’adresses IP interne pour les machines virtuelles dans un VNet ?

Oui. Adresses IP internes restent avec la machine virtuelle pour sa durée de vie, à moins que la machine virtuelle est libérée. Lorsqu’une machine virtuelle est libérée, l’adresse IP interne est publié, sauf si vous avez défini une adresse IP interne de votre ordinateur virtuel. Si la machine virtuelle est simplement arrêtée (et placez pas dans l’état **arrêt (Deallocated)**), l’adresse IP restera affectée à la machine virtuelle.

### <a name="can-i-manually-assign-ip-addresses-to-nics-in-vms"></a>Puis-je attribuer manuellement des adresses IP aux cartes réseau de machines virtuelles ?

Non. Vous ne devez pas modifier les propriétés de l’interface de machines virtuelles. Les modifications peuvent entraîner potentiellement perte de la connectivité de la machine virtuelle.

### <a name="what-happens-to-my-ip-addresses-if-i-shut-down-a-vm"></a>Qu’arrive-t-il à mes adresses IP si j’arrêter une machine virtuelle ?

Ne rien. Les adresses IP (VIP publique et adresse IP interne) restera avec un service cloud ou machine virtuelle.

> [AZURE.NOTE] Si vous voulez simplement arrêter la machine virtuelle, n’utilisez pas le portail de gestion pour le faire. Pour l’instant, le bouton Arrêter libérer la la machine virtuelle.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-re-deploying"></a>Puis-je déplacer machines virtuelles à partir d’un sous-réseau vers un autre sous-réseau dans un VNet sans ré-déployer ?

Oui. Vous pouvez en savoir plus [ici](../articles/virtual-network/virtual-networks-move-vm-role-to-subnet.md).

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>Puis-je configurer une adresse MAC statique pour mon machine virtuelle ?

Non. Une adresse MAC ne peut pas être configurée de manière statique.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-it-has-been-created"></a>L’adresse MAC restera identique pour mon machine virtuelle une fois qu’il a été créé ?

Oui, l’adresse MAC restera identique pour une machine virtuelle alors que la machine virtuelle a été arrêtée (désalloué) et relancée.

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>Puis-je me connecter à internet à partir d’un ordinateur virtuel dans un VNet ?

Oui. Tous les services déployées au sein d’un VNet peuvent vous connecter à Internet. En outre, chaque service cloud déployée dans Azure a un VIP publiquement dédié est affecté à. Vous devez définir des points de terminaison d’entrée pour les rôles PaaS et des points de terminaison pour les machines virtuelles activer ces services accepter les connexions à partir d’Internet.

## <a name="vnets-and-services"></a>VNets et Services

### <a name="what-services-can-i-use-with-vnets"></a>À quels services puis-je utiliser avec VNets ?

Vous ne pouvez utiliser les services cluster dans VNets. Cluster sont limités aux Services en nuage (rôles web et de travail) et machines virtuelles.

### <a name="can-i-use-web-apps-with-virtual-network"></a>Puis-je utiliser des applications Web avec réseau virtuel ?

Oui. Vous pouvez déployer des applications Web à l’intérieur d’un VNet à l’aide de ASE (environnement de Service d’application). Ajouter à ceci, applications Web peuvent en toute sécurité se connecter et accéder aux ressources dans votre VNet Azure si vous avez configuré pour votre VNet point-à-site. Pour plus d’informations, voir les rubriques suivantes :


- [Création d’applications Web dans un environnement de Service d’application](../articles/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)

- [Intégration des réseaux virtuel applications Web](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/)

- [À l’aide de l’intégration VNet et connexions hybride avec Web Apps](https://azure.microsoft.com/blog/2014/10/30/using-vnet-or-hybrid-conn-with-websites/)

- [Intégrer une application web à un réseau virtuel Azure](../articles/app-service-web/web-sites-integrate-with-vnet.md)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Puis-je déployer les services en nuage avec des rôles web et de travail (PaaS) dans un VNet ?

Oui. Vous pouvez déployer les services PaaS dans VNets.

### <a name="how-do-i-deploy-paas-roles-to-a-vnet"></a>Comment déployer des rôles PaaS à un VNet ?

Vous pouvez y parvenir en spécifiant le nom VNet et les mappages de /subnet rôle dans la section configuration réseau de votre configuration du service. Vous n’avez pas besoin de mettre à jour un de vos fichiers binaires.

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>Puis-je déplacer mes services et déconnexion VNets ?

Non. Vous ne pouvez pas déplacer des services et déconnexion VNets. Vous devrez supprimer et redéployez le service pour le déplacer vers un autre VNet.

## <a name="vnets-and-security"></a>VNets et la sécurité

### <a name="what-is-the-security-model-for-vnets"></a>Quel est le modèle de sécurité pour VNets ?

VNets sont complètement isolées les unes des autres et d’autres services hébergés dans l’infrastructure Azure. Un VNet est une limite de confiance.

### <a name="can-i-define-acls-or-nsgs-on-my-vnets"></a>Puis-je définir des utilisateurs ou NSGs sur mon VNets ?

Non. Vous ne pouvez pas associer des utilisateurs ou NSGs à VNets. Toutefois, utilisateurs peuvent être définis sur les points de terminaison d’entrée pour les machines virtuelles qui ont été déployés sur un VNets et NSGs peuvent être associés à sous-réseaux ou cartes réseau.

### <a name="is-there-a-vnet-security-whitepaper"></a>Existe-t-il un livre blanc sécurité VNet ?

Oui. Vous pouvez le télécharger [ici](http://go.microsoft.com/fwlink/?LinkId=386611).

## <a name="apis-schemas-and-tools"></a>API, des schémas et des outils

### <a name="can-i-manage-vnets-from-code"></a>Puis-je gérer VNets à partir de code ?

Oui. Vous pouvez utiliser des API REST pour gérer la connectivité VNets et cross locaux. Plus d’informations sont accessibles [ici](http://go.microsoft.com/fwlink/?LinkId=296833).

### <a name="is-there-tooling-support-for-vnets"></a>Existe-t-il une prise en charge des outils de VNets ?

Oui. Vous pouvez utiliser les outils de PowerShell et de ligne de commande pour une variété de plateformes. Plus d’informations sont accessibles [ici](http://go.microsoft.com/fwlink/?LinkId=317721).
