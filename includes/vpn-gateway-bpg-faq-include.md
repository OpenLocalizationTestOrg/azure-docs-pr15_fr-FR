### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>BGP est pris en charge sur toutes les références SKU Azure VPN passerelle ?

Non, BGP est pris en charge sur Azure passerelles VPN **Standard** et **bien** . **Base** Référence (SKU) n’est pas pris en charge.

### <a name="can-i-use-bgp-with-azure-policy-based-vpn-gateways"></a>Puis-je utiliser BGP avec les passerelles VPN Azure Policy-Based ?

Non, BGP est pris en charge sur les passerelles VPN basée sur un itinéraire uniquement.

### <a name="can-i-use-private-asns-autonomous-system-numbers"></a>Puis-je utiliser les APE privés (nombres système autonome) ?

Oui, vous pouvez utiliser votre propre APE publics ou privés APE pour vos réseaux locaux et les réseaux virtuels Azure.

#### <a name="are-there-asns-reserved-by-azure"></a>Quels sont les APE réservés par Azure ?

Oui, les APE suivants sont réservés par Azure pour peerings internes et externes :

- Les APE publics : 8075, 8076, 12076
- Les APE privé : 65515, 65517, 65518, 65519, 65520

Vous ne pouvez pas spécifier ces APE pour vos locaux sur les périphériques VPN lorsque vous vous connectez à passerelles VPN Azure.

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-vnets"></a>Puis-je utiliser la même ASN pour réseaux VPN en local et VNets Azure ?

Non, vous devez affecter les APE différents entre vos réseaux en local et votre VNets Azure si vous vous les connectez avec BGP. Azure passerelles VPN ont une valeur par défaut ASN de 65515 affecté, si BGP est activé pour pas pour votre connectivité entre locaux. Vous pouvez remplacer cette valeur par défaut en affectant une autre ASN lors de la création de la passerelle VPN, ou modifier l’ASN après la création de la passerelle. Vous devrez affecter votre APE en local pour les passerelles réseau Local Azure correspondante.

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Les préfixes d’adresse passerelles VPN Azure annonce pour moi ?

Passerelle VPN Azure annonce les itinéraires à vos périphériques BGP locaux suivants :

- Votre VNet les préfixes d’adresse
- Les préfixes d’adresse pour chaque passerelles réseau Local connecté à la passerelle VPN Azure
- Itinéraires appris à partir d’autres sessions homologation BGP connectées à la passerelle VPN Azure, **sauf par défaut ou plusieurs itinéraires qui se chevauchent avec n’importe quel préfixe VNet**.

#### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>Puis-je publier itinéraire par défaut (0.0.0.0/0) vers des passerelles VPN Azure ?

Pas pour le moment.

#### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>Puis-je annonce les préfixes exactes comme mon préfixes réseau virtuel ?

Non, publicité les mêmes préfixes comme n’importe lequel d'entre votre réseau virtuel préfixes d’adresse est bloqués ou filtrées par la plateforme Azure. Toutefois, vous pouvez publier un préfixe qui est un sur-ensemble des éléments que vous avez à l’intérieur de votre réseau virtuel. Par exemple, votre réseau virtuel peuvent utiliser la 10.10.0.0/16 espace adresse et vous pouvez publier 10.0.0.0/8.

### <a name="can-i-use-bgp-with-my-vnet-to-vnet-connections"></a>Puis-je utiliser BGP avec mes connexions VNet-VNet ?

Oui, vous pouvez utiliser BGP pour les connexions de VNet-VNet et connexions entre locaux.

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>Puis-je combiner BGP avec les connexions non BGP pour mon passerelles VPN Azure ?

Oui, vous pouvez combiner les connexions BGP et non BGP pour la même passerelle VPN Azure.

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>Signifie Azure VPN passerelle prise en charge BGP voies routage ?

Oui, BGP voies routage est pris en charge, à l’exception qu’Azure VPN passerelles va **pas** annonce routes par défaut des autres pairs BGP. Pour activer voies routage entre plusieurs passerelles VPN Azure, vous devez activer BGP sur toutes les connexions VNet-VNet intermédiaires.

### <a name="can-i-have-more-than-one-tunnels-between-azure-vpn-gateway-and-my-on-premises-network"></a>Puis-je avoir plusieurs tunnel entre passerelle VPN Azure et de mon réseau local ?

Oui, vous pouvez définir plusieurs tunnel VPN S2S entre une passerelle VPN Azure et de votre réseau local. Veuillez noter que toutes ces tunnel à compter contre le nombre total de tunnel pour vos passerelles VPN Azure. Par exemple, si vous avez deux tunnel redondants entre votre passerelle Azure VPN et l’autre de votre réseau local, ils utilisera 2 tunnel déconnecter le quota total pour votre passerelle VPN Azure (10 pour Standard) et 30 pour bien.

### <a name="can-i-have-multiple-tunnels-between-two-azure-vnets-with-bgp"></a>Puis-je avoir plusieurs tunnel entre deux VNets Azure avec BGP ?

Non, redondantes tunnel entre deux réseaux virtuels n’est pas pris en charge.

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-expressroutes2s-vpn-co-existence-configuration"></a>Puis-je utiliser BGP pour S2S VPN dans une configuration co-existence ExpressRoute/S2S VPN ?

Pas pour le moment.

### <a name="what-address-does-azure-vpn-gateway-use-for-bgp-peer-ip"></a>Quelle adresse passerelle VPN Azure utilise-t-elle pour BGP homologue IP ?

La passerelle VPN Azure affecte une seule adresse IP à partir de la plage GatewaySubnet définie pour le réseau virtuel. Par défaut, il est la dernière deuxième adresse de la plage. Par exemple, si votre GatewaySubnet est 10.12.255.0/27, allant de 10.12.255.0 à 10.12.255.31, puis l’adresse IP de homologue BGP sur la passerelle VPN Azure sera 10.12.255.30. Vous pouvez trouver ces informations lorsque vous mettez les informations de passerelle VPN Azure.

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>Quelles sont les conditions requises pour les adresses IP d’homologue BGP sur mon appareil VPN ?

Votre BGP local homologue adresse **Ne doit pas** être identique à l’adresse IP de votre appareil VPN. Utilisez une autre adresse IP sur le périphérique VPN pour votre adresse IP homologue BGP. Il peut être une adresse affectée à l’interface de boucle sur l’appareil. Spécifiez cette adresse dans la passerelle réseau Local correspondantes représentant l’emplacement.

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>Que dois-je spécifier comme mon préfixes d’adresse de la passerelle réseau Local lorsque j’utilise BGP ?

Azure passerelle réseau Local spécifie les préfixes d’adresse initiale pour le réseau local. Avec BGP, vous devez allouer le préfixe host (/ 32 préfixe) de votre adresse BGP homologue IP en tant que l’espace d’adressage pour ce réseau local. Si votre adresse IP homologue BGP est 10.52.255.254, vous devez spécifier « 10.52.255.254/32 » comme le localNetworkAddressSpace de la passerelle réseau Local représentant ce réseau local. Il s’agit pour vous assurer que la passerelle VPN Azure établit la session BGP via le tunnel VPN S2S.

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>Que dois-je ajouter à mon périphérique VPN en local pour la session homologation BGP ?

Vous devez ajouter un itinéraire d’hôte de l’adresse IP de homologue Azure BGP sur votre appareil VPN pointant vers le tunnel IPsec S2S VPN. Par exemple, si la période d’enquête Azure VPN homologue est « 10.12.255.30 », vous devez ajouter un itinéraire hôte pour « 10.12.255.30 » avec une interface de saut suivant de l’interface de tunnel IPsec correspondante sur votre appareil VPN.
