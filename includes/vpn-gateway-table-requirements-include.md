Le tableau suivant répertorie la configuration requise pour les passerelles PolicyBased et RouteBased VPN. Ce tableau s’applique au Gestionnaire de ressources et les modèles de déploiement classique. Pour le modèle classique, passerelles PolicyBased VPN sont les mêmes que passerelles statiques et passerelles basées sur itinéraire sont les mêmes que passerelles dynamiques.


|   | **Passerelle VPN PolicyBased Basic** | **Passerelle VPN RouteBased Basic** | **Passerelle VPN RouteBased Standard**   | **RouteBased haute Performance VPN passerelle** |
|---|---------------------------------------|---------------------------------------|----------------------------|----------------------------------|
|    **Connectivité de Site à (S2S)**  | Configuration PolicyBased VPN        | Configuration RouteBased VPN  | Configuration RouteBased VPN     | Configuration RouteBased VPN    |
| Connectivité **point-à-Site (P2S**)      | Non pris en charge   | Prise en charge (peuvent coexister avec S2S)  | Prise en charge (peuvent coexister avec S2S)  | Prise en charge (peuvent coexister avec S2S) |
| **Méthode d’authentification**                 |    Clé partagée  | Clé partagée pour la connectivité S2S, les certificats pour la connectivité P2S | Clé partagée pour la connectivité S2S, les certificats pour la connectivité P2S | Clé partagée pour la connectivité S2S, les certificats pour la connectivité P2S |
| **Nombre maximal de connexions S2S**       | 1                              | 10                                                                    | 10                                | 30                               |
| **Nombre maximal de connexions P2S**       | Non pris en charge                  | 128                                                                   | 128                               | 128                              |
|**Support de routage active (BGP)**           | Non pris en charge                  | Non pris en charge                                                         | Prise en charge                     | Prise en charge                   |
 
