## <a name="vpn-gateway"></a>Passerelle VPN 
Une ressource de passerelle VPN permet de créer une connexion sécurisée entre leur centre de données en local et Azure. Une ressource de passerelle VPN peut être configurée de différentes manières :
 
- **Pointez sur Site** – vous pouvez accéder en toute sécurité de vos ressources Azure hébergés dans un VNET à l’aide d’un client VPN à partir de n’importe quel ordinateur. 
- **Connexion de plusieurs sites** – vous pouvez connecter de façon sécurisée à partir de vos centres de données locales aux ressources en cours d’exécution dans un VNET. 
- **VNET à VNET** – vous pouvez vous connecter en toute sécurité travers VNETS Azure au sein de la même région, ou régions pour générer les charges de travail avec geo redondance.

Propriétés de clé d’une passerelle VPN incluent :
 
- **Type de passerelle** - routé dynamiquement ou une passerelle routée statique. 
- **Préfixe du Pool d’adresse VPN Client** – adresses IP attribuée aux clients qui se connectent à un point à configuration du site.