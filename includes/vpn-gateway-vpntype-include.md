- **PolicyBased :** PolicyBased VPN ont été précédemment appelés passerelles routage statiques dans le modèle de déploiement classique. VPN stratégie chiffrement et dirigent paquets via tunnel IPsec basée sur les stratégies IPsec configurées avec les combinaisons de préfixes d’adresses entre votre réseau local et le VNet Azure. La stratégie (ou un sélecteur de trafic) est généralement définie comme une liste d’accès dans la configuration du périphérique VPN. La valeur d’un type de PolicyBased VPN est *PolicyBased*. Lorsque vous utilisez un PolicyBased VPN, gardez à l’esprit les limitations suivantes :

    - PolicyBased VPN peuvent **seulement** être utilisés sur la passerelle base référence (SKU). Ce type VPN n’est pas compatible avec les autres références SKU passerelle.
    - Vous pouvez avoir uniquement 1 tunnel lorsque vous utilisez un PolicyBased VPN.
    - Vous ne pouvez utiliser PolicyBased VPN pour les connexions de S2S et seulement pour certains configurations. La plupart des configurations de passerelle VPN nécessitent une RouteBased VPN.

- **RouteBased**: RouteBased VPN ont été précédemment appelés passerelles routage dynamiques dans le modèle de déploiement classique. RouteBased VPN utilisent « itinéraires » dans la période d’enquête transfert ou le routage de table pour les paquets directs dans leurs interfaces tunnel correspondantes. Les interfaces tunnel puis chiffrer ou déchiffrement les paquets déconnecter le tunnel. La stratégie (ou un sélecteur de trafic) pour les RouteBased VPN sont configurés comme pour tout (ou les caractères génériques). La valeur d’un type de RouteBased VPN est *RouteBased*.