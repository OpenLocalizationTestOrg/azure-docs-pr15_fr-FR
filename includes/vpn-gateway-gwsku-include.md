Lorsque vous créez une passerelle réseau virtuel, vous devez spécifier la passerelle de référence (SKU) que vous voulez utiliser. Lorsque vous sélectionnez une passerelle plus élevée de référence (SKU), plusieurs processeurs et la bande passante réseau sont affectés à la passerelle, et par conséquent, la passerelle peut prendre en charge débit réseau au réseau virtuel.

Passerelle VPN peut utiliser les références suivantes :

- Base
- Standard
- Bien

Lorsque vous sélectionnez une référence (SKU), procédez comme suit :

- Si vous souhaitez utiliser un type de PolicyBased VPN, vous devez utiliser la référence (SKU) de base. VPN PolicyBased (anciennement appelé routage statique) ne sont pas pris en charge sur n’importe quel autre SKU.
- BGP n’est pas pris en charge sur la base.
- Passerelle ExpressRoute VPN coexister configurations ne sont pas prises en charge sur la base.
- Connexions de passerelle VPN S2S actif peuvent être configurées sur le SKU HighPerformance uniquement.
