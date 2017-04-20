<properties 
   pageTitle="À quoi correspondent itinéraires définies par l’utilisateur et transfert IP ?"
   description="Découvrez comment utiliser utilisateur définis itinéraires (UDR) et le routage IP à acheminer le trafic réseau authentification multifacteur dans Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="what-are-user-defined-routes-and-ip-forwarding"></a>À quoi correspondent itinéraires définies par l’utilisateur et transfert IP ?
Lorsque vous ajoutez des machines virtuelles (machines virtuelles) à un réseau virtuel (VNet) dans Azure, vous remarquerez que les ordinateurs virtuels sont en mesure de communiquer avec eux via le réseau, automatiquement. Vous n’avez pas besoin spécifier une passerelle, même si les ordinateurs virtuels se trouvent dans des sous-réseaux différents. Est de même pour les communications à partir d’ordinateurs virtuels à l’Internet public et même vers votre local réseau lorsqu’une connexion hybride à partir d’Azure à votre propre centre de données est présente.

Ce flux de communication est possible car Azure utilise une série d’itinéraires système pour définir le flux du trafic IP. Itinéraires système contrôlent le flux de communication dans les scénarios suivants :

- À partir d’au sein du même sous-réseau.
- À partir d’un sous-réseau à un autre dans une VNet.
- À partir d’ordinateurs virtuels à Internet.
- À partir d’un VNet à un autre VNet via une passerelle VPN.
- À partir d’un VNet à votre réseau local via une passerelle VPN.

L’illustration suivante montre une configuration simple avec un VNet, deux sous-réseaux et quelques machines virtuelles, ainsi que les itinéraires système autorisent le trafic IP.

![Itinéraires système dans Azure](./media/virtual-networks-udr-overview/Figure1.png)

Bien que l’utilisation des itinéraires système facilite le trafic automatiquement de votre déploiement, il existe des cas dans lequel vous souhaitez contrôler le routage de paquets via une application virtuelle. Vous pouvez donc en créant des itinéraires définis par l’utilisateur qui spécifier le saut suivant pour les paquets se déplaçant vers un sous-réseau spécifique pour accéder à votre matériel virtuel au lieu de cela et l’activation de transfert IP pour la machine virtuelle en cours d’exécution en tant que le matériel virtuel.

L’illustration suivante montre un exemple d’itinéraires définis par l’utilisateur et le transfert IP pour forcer paquets envoyées à un seul sous-réseau d’une autre traitée un dispositif virtuel sur un troisième sous-réseau.

![Itinéraires système dans Azure](./media/virtual-networks-udr-overview/Figure2.png)

>[AZURE.IMPORTANT] Itinéraires définis par l’utilisateur sont appliquent uniquement au trafic qui quitte un sous-réseau. Vous ne pouvez pas créer de route pour spécifier la manière dont le trafic est fourni dans un sous-réseau à partir d’Internet, par exemple. En outre, l’application vers que si vous transférez le trafic ne peut pas être dans le même sous-réseau d'où provient le trafic. Toujours créer un sous-réseau distinct pour vos applications. 

## <a name="route-resource"></a>Ressource de l’itinéraire
Les paquets sont routés via un réseau TCP/IP basé sur une table itinéraire définie sur chaque nœud sur le réseau physique. Une table de routage est un ensemble d’itinéraires individuels utilisés pour choisir l’emplacement transférer les paquets en fonction de l’adresse IP de destination. Un itinéraire comprend les éléments suivants :

|Propriété|Description|Contraintes|Considérations relatives à la|
|---|---|---|---|
| Préfixe d’adresse | CIDR de destination à laquelle s’applique l’itinéraire, par exemple 10.1.0.0/16.|Doit être une plage valide CIDR représentant adresses sur l’Internet public, réseau virtuel Azure ou centre de données locale.|Vérifiez que le **préfixe d’adresse** ne contient pas l’adresse pour l' **adresse du saut suivant**, sinon vos paquets entrerez d’une boucle allant de la source vers le saut suivant sans atteindre la destination. |
| Type du saut suivant | Le type de saut Azure le paquet doit être envoyé à. | Doit être une des valeurs suivantes : <br/> **Réseau virtuel**. Représente le réseau virtuel local. Par exemple, si vous avez deux sous-réseaux, 10.1.0.0/16 et 10.2.0.0/16 dans le même réseau virtuel, l’itinéraire de chaque sous réseau dans la table de routage aura une valeur de saut suivante de *Réseau virtuel*. <br/> **Passerelle réseau virtuel**. Représente une passerelle VPN S2S Azure. <br/> **Internet**. Représente la passerelle Internet par défaut fournie par l’Infrastructure Azure. <br/> **Dispositif virtuel**. Représente un appareil virtuel que vous avez ajouté à votre réseau virtuel Azure. <br/> **Aucun**. Représente un blocage. Paquets transmis à un blocage ne seront pas du tout transmises.| Envisagez d’utiliser un type de **Aucun** pour arrêter les paquets provenant de dirige vers une destination donnée. | 
| Adresse du saut suivant | L’adresse du saut suivant contient l’adresse IP paquets doivent être transférés vers. Valeurs saut suivantes ne sont autorisés dans les gammes dont le type de saut suivant est *Dispositif virtuel*.| Doit être une adresse IP qui n’est accessible au sein du réseau virtuel où l’itinéraire définies par l’utilisateur est appliqué. | Si l’adresse IP représente une machine virtuelle, vérifiez que vous activez l’option [de transfert IP](#IP-forwarding) dans Azure pour la machine virtuelle. |

Dans Azure PowerShell certaines des valeurs « NextHopType » portent des noms différents :
- Réseau virtuel est VnetLocal
- Passerelle réseau virtuel est VirtualNetworkGateway
- Dispositif virtuel est VirtualAppliance
- Internet est Internet
- Aucune ne correspond à aucune

### <a name="system-routes"></a>Itinéraires système
Chaque créé dans un réseau virtuel constitue automatiquement associé à une table d’itinéraires qui contient les règles d’itinéraire système suivantes :

- **Règle Vnet locale**: cette règle est automatiquement créée pour chaque sous-réseau dans un réseau virtuel. Il indique qu’il y a un lien direct entre les ordinateurs virtuels dans le VNet et il n’existe aucun saut intermédiaire suivante.
- **Règle en local**: cette règle s’applique à tout le trafic destiné à la plage d’adresses en local et utilise passerelle VPN comme la destination du saut suivante.
- **Règle Internet**: cette poignées règle tout le trafic destiné à l’Internet public (préfixe d’adresse 0.0.0.0/0) et utilise la passerelle internet infrastructure comme saut suivant pour tout le trafic destinés à Internet.

### <a name="user-defined-routes"></a>Itinéraires définis par l’utilisateur
La plupart des environnements, vous devrez uniquement les itinéraires système déjà définis par Azure. Toutefois, vous devrez peut-être créer une table itinéraire et ajouter un ou plusieurs itinéraires dans des cas particuliers, tels que :

- Forcer tunnel à Internet via votre réseau local.
- Utilisation de l’authentification multifacteur dans votre environnement Azure.

Dans les scénarios ci-dessus, vous devez créer une table itinéraire et ajouter des itinéraires définis par l’utilisateur à celui-ci. Vous pouvez avoir plusieurs tables itinéraire, et la même table itinéraire peut être associée à un ou plusieurs sous-réseaux. Et chaque sous-réseau ne peut être associé à une table itinéraire unique. Tous les ordinateurs virtuels et services cloud en cours d’une utilisation sous-réseau la table de routage associé à ce sous-réseau.

Sous-réseaux s’appuient sur itinéraires système jusqu'à ce qu’une table de routage est associée au sous-réseau. Dès qu’une association existe, routage est effectué en fonction de la plus longue préfixe EQUIV (locales) entre les itinéraires définis par l’utilisateur et des itinéraires système. S’il existe plusieurs itinéraires avec la même correspondance locales un itinéraire est sélectionné en fonction de son origine dans l’ordre suivant :

1. Itinéraire défini utilisateur
1. Acheminer BGP (lorsque ExpressRoute est utilisé)
1. Acheminer système

Pour apprendre à créer des itinéraires définis par l’utilisateur, voir [comment créer des itinéraires et activer le routage IP dans Azure](virtual-network-create-udr-arm-template.md).

>[AZURE.IMPORTANT] Itinéraires définis par l’utilisateur sont appliquent uniquement aux services cloud et de machines virtuelles Azure. Par exemple, si vous voulez ajouter un matériel virtuel pare-feu entre votre réseau local et Azure, vous devez créer un itinéraire définis par l’utilisateur pour vos tableaux itinéraire Azure qui transmet tout le trafic accédant à l’espace d’adressage local à l’application virtuelle. Vous pouvez également ajouter un itinéraire définis par l’utilisateur (UDR) à la GatewaySubnet pour transférer tout le trafic local aux Azure via l’application virtuelle. Il s’agit d’un ajout récent.

### <a name="bgp-routes"></a>Itinéraires BGP
Si vous disposez d’une connexion ExpressRoute entre votre réseau local et Azure, vous pouvez activer BGP de se propager itinéraires à partir de votre réseau local vers Azure. Ces itinéraires BGP sont utilisées dans la même façon que système itinéraires et itinéraires définis par l’utilisateur dans chaque sous-réseau Azure. Pour plus d’informations, voir [Présentation des ExpressRoute](../expressroute/expressroute-introduction.md).

>[AZURE.IMPORTANT] Vous pouvez configurer votre environnement Azure pour utiliser forcer tunnel via votre réseau local en créant un itinéraire défini utilisateur pour 0.0.0.0/0 sous-réseau qui utilise la passerelle VPN comme saut suivant. Toutefois, cela fonctionne uniquement si vous utilisez une passerelle VPN, pas ExpressRoute. ExpressRoute, tunnel forcé est configuré par le biais BGP.

## <a name="ip-forwarding"></a>Transfert IP
Comme décrit ci-dessus, une des raisons pour créer un itinéraire défini utilisateur consiste à transférer le trafic vers une application virtuelle. Un appareil virtuel est rien d’autre qu’un ordinateur virtuel qui exécute une application utilisée pour gérer le trafic réseau d’une façon quelconque, par exemple un pare-feu ou un périphérique NAT.

Cette solution virtual machine virtuelle doit être en mesure de recevoir le trafic entrant qui n’est pas adressé à lui-même. Pour permettre à un ordinateur virtuel recevoir le trafic adressé vers d’autres destinations, vous devez activer le routage IP pour la machine virtuelle. Il s’agit d’un Azure définir, pas de paramètre dans le système d’exploitation invité.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [créer des itinéraires dans le modèle de déploiement du Gestionnaire de ressources](virtual-network-create-udr-arm-template.md) et associez-les à sous-réseaux. 
- Découvrez comment [créer des itinéraires dans le modèle de déploiement classique](virtual-network-create-udr-classic-ps.md) et associez-les à sous-réseaux.
