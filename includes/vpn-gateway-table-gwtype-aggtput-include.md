|    | **Débit passerelle VPN (1)** | **Passerelle VPN max IPsec tunnel (2)** | **Débit ExpressRoute passerelle** | **Passerelle VPN et ExpressRoute coexister**|
|--- |----------------------------|-----------------------------------|-------------------------------------|-----------------------------------------|
| **Référence (SKU) base (3)(5)**              |  100 Mbps | 10                         |  500 Mbps                           | N°   |
| **Référence (SKU) standard (4)(5)**           |  100 Mbps | 10                         | 1000 Mbps                           | Oui  |
| **Haute Performance référence (SKU) (4)**   | 200 Mbps  | 30                         | Mbps 2000                           | Oui  |

- (1) le débit VPN est une estimation approximative basée sur les mesures entre VNets dans la même région Azure. Il n’est pas un débit garanti pour les connexions entre locaux via Internet. Il est la mesure de débit maximal possible.
- (2) le nombre de tunnel consultez VPN RouteBased. Un PolicyBased VPN peut seulement prendre en charge un seul tunnel VPN de Site à.
- (3) BGP n’est pas prise en charge pour la référence (SKU) de base.
- (4) VPN PolicyBased ne sont pas prises en charge pour cette version. Ils sont pris en charge pour la version de base uniquement.
- (5) connexions actif S2S VPN passerelle ne sont pas prises en charge pour cette version. Actif est pris en charge sur le SKU HighPerformance uniquement.