- Les réseaux virtuels peuvent se trouver dans les régions Azure identiques ou différentes (emplacements).

- Un service cloud ou une point de terminaison d’équilibrage de charge ne peut pas s’étendre sur les réseaux virtuels, même s’ils sont connectés ensemble.

- Relier plusieurs réseaux virtuels Azure ne nécessite pas les passerelles VPN en local, à moins que la connectivité entre local est nécessaire.

- VNet-VNet prend en charge la connexion de réseaux virtuel. Il ne pas en charge des machines virtuelles qui se connecte ou cloud services pas dans un réseau virtuel.

- VNet-VNet nécessite des passerelles VPN Azure avec RouteBased (anciennement appelé routage dynamique) types VPN. 

- La connectivité réseau virtuel peut être utilisée simultanément avec VPN sur plusieurs sites, avec un maximum de 10 (passerelles par défaut/Standard) ou 30 (haute Performance passerelles) VPN tunnel pour une passerelle VPN réseau virtuel se connecter aux deux autres réseaux virtuel ou sites local.

- Les espaces d’adressage des sites réseau local virtuels réseaux et en local doivent se chevauchent pas. Chevauchement des espaces d’adressage entraîne la création de connexions VNet-VNet échec.

- Tunnel redondants entre deux réseaux virtuels n’est pas pris en charge.

- Tous les tunnel VPN du réseau virtuel partage la bande passante disponible sur la passerelle VPN Azure et la disponibilité de passerelle VPN même SLA dans Azure.

- Le trafic VNet-VNet parcourt Microsoft Network, pas à Internet.

- Le trafic VNet-VNet dans la même région est disponible pour les deux sens ; croisée région VNet-VNet sortie le trafic est facturé avec les taux de transfert de données VNet notamment sortant basés sur les zones source. Reportez-vous à la [page de tarification](https://azure.microsoft.com/pricing/details/vpn-gateway/) pour plus d’informations.