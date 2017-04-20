<properties
   pageTitle="Exemple de DMZ – créer une DMZ pour protéger les réseaux avec un pare-feu, UDR et NSG | Microsoft Azure"
   description="Créer une DMZ avec un pare-feu, définies par l’utilisateur (UDR) routage et des groupes de sécurité réseau (NSG)"
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/01/2016"
   ms.author="jonor;sivae"/>

# <a name="example-3--build-a-dmz-to-protect-networks-with-a-firewall-udr-and-nsg"></a>Exemple 3 : créer une DMZ pour protéger les réseaux avec un pare-feu, UDR et NSG

[Revenir à la Page de pratiques meilleure sécurité limite][HOME]

Cet exemple crée une DMZ avec un pare-feu, quatre serveurs windows, le routage définies par l’utilisateur, de transfert IP et groupes de sécurité réseau. Il va également guider dans chacune des commandes pertinentes pour fournir une bonne compréhension de chaque étape. Il existe également une section scénario le trafic pour fournir une profondeur étape par étape comment le trafic traversent les couches de défense dans la zone DMZ. Enfin, dans les références de section est le code complet et les instructions permettant de créer cet environnement pour tester et expérimenter différents scénarios. 

![DMZ bidirectionnelle avec à couloirs, NSG et UDR][1]

## <a name="environment-setup"></a>Configuration de l’environnement
Dans cet exemple, il est un abonnement qui inclut les éléments suivants :

- Trois services en nuage : « SecSvc001 », « FrontEnd001 » et « BackEnd001 »
- Un réseau virtuel « CorpNetwork », avec trois sous-réseaux : « SecNet », « FrontEnd » et « Et »
- Un matériel virtuel réseau, dans cet exemple, un pare-feu, connecté au sous-réseau SecNet
- Windows Server qui représente un serveur web d’application (« IIS01 »)
- Serveurs deux fenêtres qui représentent application revenir terminer serveurs (« AppVM01 », « AppVM02 »)
- Windows server qui représente un serveur DNS (« DNS01 »)

Dans la section références ci-dessous présente un script PowerShell qui générera la plupart de l’environnement ci-dessus. Construction du machines virtuelles et des réseaux virtuels, bien que sont effectuées par l’exemple de script, ne sont pas décrites en détail dans ce document.

Pour créer l’environnement :

  1.    Enregistrer le fichier xml de configuration réseau inclus dans la section Références (mis à jour avec les noms, l’emplacement et IP adresses pour faire correspondre le scénario donné)
  2.    Mettre à jour les variables d’utilisateur dans le script pour correspondre à l’environnement que le script doit être exécuté sur (abonnements, les noms de service, etc.)
  3.    Exécuter le script dans PowerShell

**Remarque**: la région indiquée dans le script PowerShell doit correspondre à la région indiquée dans le fichier xml de configuration réseau.

Une fois que le script s’exécute correctement les étapes PostScript suivantes peuvent être prises :

1.  Configurer les règles de pare-feu, il est traité dans la section intitulée : Description de la règle pare-feu.
2.  Vous pouvez également dans la section références sont des deux scripts pour configurer le serveur web et le serveur de l’application avec une application web simple pour permettre le test avec cette configuration DMZ.

Une fois que le script s’exécute correctement le pare-feu règles doit être terminée, il est traité dans la section intitulée : les règles de pare-feu.

## <a name="user-defined-routing-udr"></a>Routage (UDR) définies par l’utilisateur
Par défaut, les itinéraires système suivants sont définies en tant que :

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

Le VNETLocal est toujours le prefix(es) adressage par défaut de la VNet pour ce réseau spécifique (Internet Explorer elle remplacera à partir de VNet par VNet selon la définition de chaque VNet spécifique). Les itinéraires système restantes sont statiques et par défaut comme indiqué ci-dessus.

En ce qui concerne la priorité, itinéraires sont traités via la méthode la plus longue préfixe EQUIV (locales), et donc l’itinéraire plus spécifique dans la table s’appliquent à une adresse de destination donnée.

Par conséquent, le trafic (par exemple sur le serveur DNS01, 10.0.2.4) prévu pour le réseau local (10.0.0.0/16) serait routé sur le VNet atteindre sa destination en raison de l’itinéraire 10.0.0.0/16. En d’autres termes, pour 10.0.2.4, l’itinéraire 10.0.0.0/16 est l’itinéraire plus spécifique, même si la 10.0.0.0/8 et 0.0.0.0/0 peuvent s’appliquent également, mais dans la mesure où ils sont inférieures spécifique qu’ils n’affectent ce trafic. Ainsi, le trafic vers 10.0.2.4 aurait saut suivant de la VNet local et transmettre simplement à la destination.

Si le trafic était destiné à 10.1.1.1 par exemple, l’itinéraire 10.0.0.0/16 n’est pas appliqué, mais la 10.0.0.0/8 serait plus spécifique et le trafic serait supprimée (« dipositif noir ») dans la mesure où le saut suivant est Null. 

Si la destination n’a pas été s’appliquent à un des préfixes Null ou les préfixes VNETLocal, puis il s’agit de la moins spécifique Router 0.0.0.0/0 et être routés vers Internet comme saut suivant et ainsi de sortie périmètre internet d’Azure.

S’il existe deux préfixes identiques dans la table d’itinéraires, voici l’ordre de préférence basée sur l’attribut « source » itinéraires :

1.  « VirtualAppliance » = un itinéraire définie par l’utilisateur ajoutée manuellement à la table
2.  « VPNGateway » = un itinéraire dynamique BGP (lorsqu’il est utilisé avec les réseaux hybride), ajouté par un protocole réseau dynamiques, ces itinéraires peuvent changer dans le temps que le protocole dynamique reflète automatiquement les modifications de ressources réseau
3.  « Par défaut » = itinéraires système, le VNet local et les entrées statiques comme indiqué dans la table d’itinéraires ci-dessus.

>[AZURE.NOTE] Vous pouvez désormais utiliser routage définies par l’utilisateur (UDR) des passerelles VPN ExpressRoute pour forcer le de trafic entrant et sortant entre les différents locaux routés vers un appareil virtuel réseau (à couloirs).

#### <a name="creating-the-local-routes"></a>Création de la route local

Dans cet exemple, deux tables de routage sont nécessaires, un pour chacun des sous-réseaux Frontend et serveur principal. Chaque table est chargée avec des itinéraires statiques appropriées pour le sous-réseau donné. Pour cet exemple, chaque table possède trois routes :

1. Le trafic sous-réseau local avec aucun saut suivant défini pour autoriser le trafic sous-réseau local à contourner le pare-feu
2. Trafic réseau virtuel avec un saut suivant défini comme pare-feu, ce paramètre remplace la règle par défaut qui autorise le trafic VNet local acheminer directement
3. Tout restant le trafic (0/0) avec un saut suivant défini comme le pare-feu

Après avoir créé les tables de routage elles sont liées à leurs sous-réseaux. Pour le sous-réseau Frontend routage table, une fois créé et lié au sous-réseau doit ressembler à ceci :

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active


Dans cet exemple, les commandes suivantes sont utilisées pour générer la table itinéraire, ajoutez un itinéraire définis par l’utilisateur, puis lier la table de routage à un sous-réseau (Note ; tous les éléments en dessous commençant par un signe dollar (par exemple : $BESubnet) sont des variables définies par l’utilisateur à partir du script dans la section Références de ce document) :

1.  Tout d’abord la table de routage base doit être créée. Cet extrait montre la création de la table pour le sous-réseau principal. Dans le script, une table correspondante est également créée pour le sous-réseau Frontend.

        New-AzureRouteTable -Name $BERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $BESubnet subnet"

2.  Une fois que la table de routage est créée, itinéraires spécifiques définies par l’utilisateur peuvent être ajoutés. Dans ce extraites, tout le trafic (0.0.0.0/0) sera routé via l’application virtuelle (une variable, $VMIP [0], est utilisée pour passer de l’adresse IP affectée lorsque l’application virtuelle a été créée précédemment dans le script). Dans le script, une règle correspondante est également créée dans la table Frontend.

        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]

3. L’entrée d’itinéraire ci-dessus remplace l’itinéraire par défaut 0.0.0.0 / « 0 », mais la règle 10.0.0.0/16 par défaut qui existent encore permettant au trafic au sein de la VNet pour acheminer directement à la destination et pas au réseau virtuel matériel. Pour correcte ce comportement de la règle suivre doit être ajouté.

        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]

4. À ce stade présente un choix à effectuer. Avec les gammes de deux ci-dessus achemine tout le trafic vers le pare-feu pour l’évaluation, le trafic pair dans un seul sous-réseau. Cela peut être vous le souhaitez, mais pour autoriser le trafic au sein d’un sous-réseau pour acheminer localement sans l’intervention du pare-feu une troisième, règle très spécifique peut être ajouté. Cet itinéraire États n’importe quelle adresse destine pour le sous-réseau local peut juste Router il directement (NextHopType = VNETLocal).

        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal

5.  Enfin, avec la table de routage créé et rempli avec une itinéraires définis par l’utilisateur, le tableau doit maintenant lié à un sous-réseau. Dans le script, la table d’itinéraires frontal est également liée au sous-réseau Frontend. Voici le script de liaison pour le sous-réseau principal.

        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
           -SubnetName $BESubnet `
           -RouteTableName $BERouteTableName

## <a name="ip-forwarding"></a>Transfert IP
Fonctionnalité à UDR, est de transfert IP. Il s’agit d’un paramètre sur un appareil virtuel qui permet de recevoir du trafic ne sont pas spécifiquement adressé à l’application et puis transférer que le trafic vers sa destination finale.

Par exemple, si le trafic de AppVM01 effectue une requête sur le serveur DNS01, UDR achemine ceci pour le pare-feu. Avec le routage IP activé, le trafic pour la destination DNS01 (10.0.2.4) sera accepté par l’application (10.0.0.4), puis transféré vers sa destination intégrale (10.0.2.4). Sans le routage IP activé sur le pare-feu, le trafic pas accepté par l’application alors que la table de routage comporte le pare-feu comme saut suivant. 

>[AZURE.IMPORTANT] Il est essentiel de penser à activer le transfert d’IP conjointement avec le routage définies par l’utilisateur.

La configuration du transfert IP est une commande unique et peut être effectuée à l’heure de création d’une machine virtuelle. Pour le flux de cet exemple, l’extrait de code est vers la fin du script et groupé avec les commandes UDR :

1.  L’instance de machine virtuelle qui est votre matériel virtuel, le pare-feu dans ce cas d’appel et activer le transfert IP (Note ; n’importe quel élément en rouge commençant par un signe dollar (par exemple : $VMName[0]) est une variable définie par l’utilisateur à partir du script dans la section Références de ce document. Le zéro entre crochets, [0], représente la première machine virtuelle dans le tableau des machines virtuelles, pour l’exemple de script sans modification, la première machine virtuelle (machine virtuelle 0) doit être le pare-feu) :

        Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] | `
           Set-AzureIPForwarding -Enable

## <a name="network-security-groups-nsg"></a>Groupes de sécurité réseau (NSG)
Dans cet exemple, un groupe NSG est créé et puis chargé avec une seule règle. Ce groupe est ensuite lié uniquement aux sous-réseaux Frontend et serveur principal (pas le SecNet). Manière déclarative est en cours de génération la règle suivante :

1.  Tout le trafic (tous les ports) à partir d’Internet à l’entier VNet (tous les sous-réseaux) est refusé

Bien que NSGs sont utilisés dans cet exemple, il est objectif principal est une couche secondaire de défense par rapport à une mauvaise configuration manuelle. Nous à bloquer tout trafic provenant d’internet à des options Frontend ou sous-réseaux principal, le trafic doivent flux uniquement par le biais du sous-réseau SecNet vers le pare-feu (et ensuite si vous voulez à la version serveur-Frontend ou sous-réseaux). En outre, avec les règles UDR en place, tout le trafic qui transformez-les en les sous-réseaux Frontend ou serveur principal serait dirigé arrière vers le pare-feu (grâce aux UDR). Le pare-feu serait voir ceci comme flux asymétrique et serait déposez le trafic sortant. Par conséquent, il existe trois niveaux de sécurité protégeant les sous-réseaux Frontend et serveur principal ; 1) aucun point de terminaison ouvert sur le FrontEnd001 et BackEnd001 services en nuage, 2) NSGs reçois-je le trafic à partir d’Internet, 3) le trafic asymétriques de suppression de pare-feu.

Un point intéressant en ce qui concerne le groupe de sécurité réseau dans cet exemple est qu’elle contient une seule règle, ci-dessous, afin de refuser le trafic internet vers l’ensemble du réseau virtuel qui inclut le sous-réseau de sécurité. 

    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet `
        from the Internet" `
        -Type Inbound -Priority 100 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK `
        -DestinationPortRange '*' `
        -Protocol *

Toutefois, étant donné que la NSG n’est liée aux sous-réseaux Frontend et serveur principal, la règle n’est pas traitée sur le trafic entrant au sous-réseau sécurité. Par conséquent, même si la règle NSG n’indique aucun le trafic Internet vers n’importe quelle adresse sur la VNet, car le NSG était jamais lié au sous-réseau sécurité, le trafic passera au sous-réseau sécurité.

    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $FESubnet -VirtualNetworkName $VNetName
    
    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $BESubnet -VirtualNetworkName $VNetName

## <a name="firewall-rules"></a>Règles de pare-feu
Sur le pare-feu, règles de transfert devra être créé. Étant donné que le pare-feu est le trafic blocage ou le transfert de tout entrant, sortant et interne VNet plusieurs règles de pare-feu sont nécessaires. En outre, tout le trafic entrant est atteint l’adresse IP publique de Service de sécurité (sur différents ports), qui sont traitées par le pare-feu. Il est recommandé de créer un diagramme des flux logiques avant de configurer les sous-réseaux et retraitement les règles de pare-feu afin d’éviter ultérieurement. La figure suivante est une vue logique de règles de pare-feu pour que cet exemple :
 
![Vue logique de règles de pare-feu][2]

>[AZURE.NOTE] En fonction de l’application virtuelle réseau utilisé, les ports de gestion peuvent varier. Dans cet exemple, qu'un pare-feu NextGen Barracuda est référencé qui utilise les ports 22 et 801 807. Consultez la documentation de fournisseur de matériel pour rechercher les ports exacts utilisés pour la gestion de l’appareil utilisé.

### <a name="logical-rule-description"></a>Description de la règle logique
Dans le diagramme logique ci-dessus, le sous-réseau sécurité n’est pas visible dans la mesure où le pare-feu est la seule ressource sur ce sous-réseau, et ce diagramme s’affiche les règles de pare-feu et comment ils logiquement autoriser ou refuser les flux de trafic et non le chemin routé réel. En outre, les ports externes sélectionnées pour le trafic RDP sont supérieures plage de ports (8014 – 8026) et ont été sélectionnées pour un peu aligner avec les deux derniers octets de l’adresse IP locale pour être plus lisibles (par exemple, l’adresse du serveur local 10.0.1.4 est associé à port externe 8014), cependant les ports non conflictuelles supérieurs peuvent être utilisées.

Dans cet exemple, nous avons besoin de 7 types de règles, ces types de règles sont décrits comme suit :

- Règles externes (pour le trafic entrant) :
  1.    Règle de gestion de pare-feu : Cette règle redirection d’application permet le trafic vers les ports de gestion du matériel réseau virtuel.
  2.    Règles de RDP (pour chaque windows server) : gestion des serveurs individuels via RDP permettra à ces quatre règles (soit un pour chaque serveur). Cela pourrait également être regroupé dans une seule règle selon les fonctionnalités du matériel réseau virtuel en cours d’utilisation.
  3.    Règles de trafic d’application : Il existe deux règles de trafic Application, la première pour le trafic web frontal et la seconde pour le trafic de serveur principal (par exemple, serveur web au niveau de données). La configuration de ces règles sera dépendent de l’architecture du réseau (où vos serveurs sont placés) et les flux de trafic (sens les flux de trafic, ainsi que les ports sont utilisés).
      - La première règle permettra le trafic d’application réel atteindre le serveur d’applications. Tandis que les autres règles permettent de sécurité, gestion, etc., règles d’Application sont qu’Autoriser les utilisateurs externes ou des services accéder à l’ou les applications. Dans cet exemple, il est un serveur web sur le port 80, et donc une règle d’application unique pare-feu redirigera le trafic entrant vers l’adresse IP externe, à l’adresse IP web les serveurs interne. La session de rediriger le trafic serait être NAT avait vers le serveur interne.
      - La deuxième règle le trafic Application est la règle principal pour permettre au serveur Web communiquer avec le serveur AppVM01 (mais pas AppVM02) via n’importe quel port.
- Règles internes (pour le trafic interne VNet)
  4.    Sortant à Internet règle : cette règle autorise le trafic à partir de n’importe quel réseau à transmettre aux réseaux sélectionnés. Cette règle est généralement une règle par défaut déjà sur le pare-feu, mais dans un état désactivé. Cette règle doit être activée pour que cet exemple.
  5.    Règle DNS : Cette règle autorise uniquement le trafic DNS (port 53) passer au serveur DNS. Pour cet environnement que la plupart des trafic Frontend vers le serveur principal est bloqué, cette règle permet spécifiquement DNS à partir de n’importe quel sous-réseau local.
  6.    Sous-réseau à un règle : cette règle est pour permettre à tous les serveurs du sous-réseau principal pour vous connecter à n’importe quel serveur sur le sous-réseau frontal (mais pas l’inverse).
- Règle de sécurité intégrée (pour le trafic qui ne répond pas à toutes les situations ci-dessus) :
  7.    Refuser toutes les règles de trafic : Cette doit toujours être la règle finale (en termes de priorité) et en tant que tel si un trafic passe ne correspond pas à une des règles précédentes qu'il sera déplacée par cette règle. Il s’agit d’une règle par défaut et aucune modification n’est généralement n’activé, est généralement nécessaires.

>[AZURE.TIP] Dans la deuxième règle du trafic de l’application, n’importe quel port est autorisé pour facile de cet exemple, dans un scénario réel le port plus spécifique et plages d’adresses doivent être utilisés pour réduire la surface d’attaque de cette règle.

<br />

>[AZURE.IMPORTANT] Une fois que toutes les règles ci-dessus sont créées, il est important de réviser la priorité de chaque règle pour garantir le trafic est autorisé ou refusé comme vous le souhaitez. Dans cet exemple, les règles sont dans l’ordre de priorité. Il est facile d’être verrouillés déconnecter le pare-feu en raison de règles mal ordonnées. Au minimum, assurez-vous que la gestion du pare-feu lui-même est toujours la règle de priorité plus élevée absolue.

### <a name="rule-prerequisites"></a>Conditions préalables pour les règles
Un composant requis pour la Machine virtuelle en cours d’exécution le pare-feu sont les points de terminaison publics. Pour le pare-feu pour le trafic de traiter les points de terminaison publics appropriés doivent être ouverts. Il existe trois types de trafic dans cet exemple ; Trafic RDP (1) le trafic de gestion pour contrôler le pare-feu et les règles de pare-feu, 2) pour contrôler les serveurs windows et le trafic Application 3). Voici les trois colonnes de types de trafic dans le coin supérieur milieu de la vue logique de règles de pare-feu au-dessus.

>[AZURE.IMPORTANT] Une clé takeway ici consiste à n’oubliez pas que **l’ensemble** du trafic sera communiquée à travers le pare-feu. Donc au Bureau à distance sur le serveur IIS01, même s’il est dans le Service de nuage fin avant et sur le sous-réseau frontal, pour accéder à ce serveur nous sera peut-être RDP pour le pare-feu sur le port 8014 et puis autoriser le pare-feu acheminer la demande RDP en interne vers le Port RDP IIS01. Bouton « Connexion » du portail Azure ne fonctionne pas, car il n’existe aucun chemin RDP direct vers IIS01 (pour autant que le portail qui est visible). Cela signifie que toutes les connexions à partir d’internet sont au Service de sécurité et un Port, par exemple, secscv001.cloudapp.net:xxxx (référence le schéma ci-dessus pour le mappage de Port externe et IP interne et le Port).

Un point de terminaison peut être ouvert soit au moment de la création d’une machine virtuelle ou posez build comme est terminé dans l’exemple de script et ci-dessous dans cet extrait de code (Note ; n’importe quel élément en commençant par un signe dollar (par exemple : $VMName[$i]) est une variable définie par l’utilisateur à partir du script dans la section Références de ce document. Le « $i » entre crochets, [$i], représente le nombre de tableau d’une machine virtuelle spécifique dans un tableau de machines virtuelles) :

    Add-AzureEndpoint -Name "HTTP" -Protocol tcp -PublicPort 80 -LocalPort 80 `
        -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | `
        Update-AzureVM

Bien que pas clairement montré ici en raison de l’utilisation de variables, mais les points de terminaison sont **uniquement** ouvert sur le Service en nuage sécurité. Il s’agit de garantir que tout le trafic entrant est géré (routés, NAT avait, supprimés à) par le pare-feu.

Une gestion des devra être installée sur un PC pour gérer le pare-feu et créer les configurations requises. Consultez le fournisseur de documentation de votre pare-feu (ou à d’autres couloirs) sur la gestion de l’appareil. Le reste de cette section et la section suivante, création des règles de pare-feu, décrira la configuration du pare-feu lui-même, via le client de gestion des fournisseurs (et non le portail Azure ou PowerShell).

Instructions pour le téléchargement du client et la connexion à la Barracuda utilisés dans cet exemple sont accessibles ici : [Barracuda naturel d’administration](https://techlib.barracuda.com/NG61/NGAdmin)

Une fois connecté sur le pare-feu, mais avant de créer des règles de pare-feu, il existe deux catégories d’objets prérequises qui facilitent la création de règles plus facile ; Objets de réseau et de Service.

Dans cet exemple, trois objets réseau nommée doivent être défini (un pour le sous-réseau Frontend et le sous-réseau principal, également un objet de réseau pour l’adresse IP du serveur DNS). Pour créer un réseau nommé ; à partir du tableau de bord d’administration Barracuda naturel client, accédez à l’onglet configuration, dans la section Configuration opérationnelles, cliquez sur règles, puis cliquez sur « Réseaux » dans le menu objets pare-feu, puis cliquez sur Nouveau dans le menu Modifier les réseaux. L’objet réseau peut désormais être créée, en ajoutant le nom et le préfixe :

![Créer un objet FrontEnd réseau][3]
 
Cela créera un réseau nommé pour le sous-réseau FrontEnd, un objet similaire doit être créé pour ce sous-réseau principal. Maintenant les sous-réseaux peuvent être plus facilement référencés par son nom dans les règles de pare-feu.

Pour l’objet de serveur DNS :

![Créer un objet de serveur DNS][4]

Cette référence adresse IP unique est utilisée dans une règle DNS plus loin dans le document.

Les objets prérequises deuxième sont Services. Celles-ci représentent les ports de connexion RDP pour chaque serveur. Étant donné que l’objet de service RDP existant est lié au port RDP par défaut, 3389, nouveaux Services peuvent être créés pour autoriser le trafic depuis les ports externes (8014-8026). Les nouveaux ports peuvent également être ajoutés au service RDP existant, mais pour faciliter la démonstration, une règle pour chaque serveur peut être créée. Pour créer une règle RDP d’un serveur ; à partir du tableau de bord d’administration Barracuda naturel client, accédez à l’onglet configuration, dans la section Configuration opérationnelles, cliquez sur règles, puis cliquez sur « Services » dans le menu objets pare-feu, faites défiler la liste des services et le service « RDP ». Avec le bouton droit et sélectionnez Copier, puis avec le bouton droit et sélectionnez Coller. Il existe désormais un objet de Service RDP Copie1 qui peuvent être modifiées. Avec le bouton droit RDP Copie1, sélectionnez Modifier, l’objet du Service modifier fenêtre s’affiche comme illustré ici :

![Copie de règle RDP par défaut][5]

Les valeurs puis peuvent être modifiées pour représenter le service RDP pour un serveur spécifique. Pour AppVM01 cette règle RDP par défaut doit être modifiée pour refléter un nouveau nom de Service, la Description et le Port RDP externe utilisés dans le diagramme Figure 8 (Remarque : les ports sont modifiées à partir de la valeur par défaut RDP 3389 en le port externe utilisé pour ce serveur spécifique, dans le cas AppVM01 le Port externe est 8025) le service modifié est indiqué ci-dessous :

![Règle AppVM01][6]
 
Ce processus doit être répété pour créer des Services RDP sur les autres serveurs ; AppVM02, DNS01 et IIS01. La création de ces Services permettra que la création d’une règle plus simple et plus évidente dans la section suivante.

>[AZURE.NOTE] Un service RDP pour le pare-feu n’est pas nécessaire pour deux raisons ; premier (1) le pare-feu machine virtuelle est une image Linux en fonction de sorte que SSH serait utilisée sur le port 22 pour la gestion des machines virtuelles plutôt que RDP et 2) port 22 et les deux autres ports de gestion sont autorisés dans la première règle d’administration décrite ci-dessous pour autoriser pour la connectivité de gestion des.

### <a name="firewall-rules-creation"></a>Création des règles de pare-feu
Il existe trois types de règles de pare-feu utilisés dans cet exemple, ils sont tous ont des icônes distinctes :

La règle Application rediriger : ![Icône de rediriger l’Application][7]

La règle NAT de Destination : ![Icône de NAT de Destination][8]

La règle de test : ![Icône passer][9]

Vous trouverez plus d’informations sur les règles suivantes sur le site web Barracuda.

Pour créer les règles suivantes (ou vérifier les règles existantes par défaut), en commençant par le tableau de bord d’administration Barracuda naturel client, accédez à l’onglet configuration, dans la Configuration opérationnelle de section, cliquez sur ensemble de règles. Une grille appelée, « Règles principaux » affiche les règles actifs et désactivés existants sur ce pare-feu. Dans le coin supérieur droit de cette grille est un petit, vert « + » bouton, cliquez sur ce bouton pour créer une règle (Remarque : votre pare-feu peut être « verrouillée » pour les modifications, si vous voyez un bouton marqué « Verrouiller » et vous ne pouvez pas créer ou modifier des règles, cliquez sur ce bouton pour « déverrouiller » l’ensemble de règles et autoriser la modification). Si vous souhaitez modifier une règle existante, sélectionnez cette règle, avec le bouton droit et sélectionnez Modifier la règle.

Une fois que vos règles sont créés et/ou modifiés, ils doivent être envoyées vers le pare-feu et puis activés, si ce n’est pas terminé les modifications de règle ne prendront pas effet. Le processus d’activation et de push est décrite ci-dessous les descriptions de règles de détails.

Les détails de chaque règle nécessaire pour terminer cet exemple sont décrites comme suit :

- **Règle de gestion des pare-feu**: cette application rediriger règle autorise le trafic à passer aux ports de gestion du matériel réseau virtuel, dans cet exemple, un pare-feu NextGen Barracuda. Les ports de gestion sont 801, 807 et éventuellement 22. Les ports internes et externes sont identiques (c'est-à-dire aucune traduction de port). Cette règle, le programme d’installation-gestion-ACCESS, est une règle par défaut et activée par défaut (de la version de Barracuda NextGen pare-feu 6.1).

    ![Règle de gestion de pare-feu][10]

>[AZURE.TIP] L’espace d’adressage source dans cette règle est Any, si les plages d’adresses IP gestion sont connus, réduire cette étendue aurait également réduire la surface d’attaque pour les ports de gestion.

- **Règles de RDP**: règles ces NAT de Destination permettra de gestion des serveurs individuels via RDP.
Il existe quatre champs critique nécessaires pour créer la règle :
  1.    Source – pour permettre le RDP n’importe où, la référence « Une » est utilisée dans le champ Source.
  2.    Service – utiliser l’objet Service approprié créé précédemment, dans ce cas, « AppVM01 RDP », les ports externes rediriger vers l’adresse IP locale serveurs et sur le port 3386 (le port RDP par défaut). Cette règle spécifique est pour l’accès RDP à AppVM01.
  3.    Destination – doit être le *port local sur le pare-feu*, le « DCHP 1 IP locale » ou eth0 si vous utilisez des adresses IP statique. Le nombre ordinal (eth0, eth1, etc.) peut être différent si votre matériel réseau possède plusieurs interfaces locales. C’est le port le pare-feu envoie déconnecter à partir de (peut être identique à la réception port), la destination routée réelle se trouve dans le champ de liste cible.
  4.    Redirection – cette section indique le matériel virtuel où finalement rediriger ce trafic. La redirection la plus simple consiste à placer l’adresse IP et le Port (facultatif) dans le champ de liste cible. Si aucun port n’est utilisé le port de destination dans la requête entrante sera utilisé (Internet Explorer sans traduction), si un port est désigné le port seront également NAT ainsi que l’adresse IP réponde.

    ![Pare-feu RDP règle][11]

    Au total, quatre règles RDP devra être créé : 

  	|   Nom de la règle    | Serveur  |   Service   |  Liste cible  |
  	|----------------|---------|-------------|---------------|
  	| RDP-IIS01   | IIS01   | IIS01 RDP   | 10.0.1.4:3389 |
  	| RDP pour DNS01   | DNS01   | DNS01 RDP   | 10.0.2.4:3389 |
  	| RDP pour AppVM01 | AppVM01 | AppVM01 RDP | 10.0.2.5:3389 |
  	| RDP pour AppVM02 | AppVM02 | AppVm02 RDP | 10.0.2.6:3389 |
  
>[AZURE.TIP] Restreindre les résultats de l’étendue des champs Source et Service permet de réduire la surface d’attaque. La portée plus limitée qui permet de fonctionnalité doit être utilisée.

- **Règles de trafic d’application**: il existe deux règles de trafic Application, la première pour le trafic web frontal et la seconde pour le trafic de serveur principal (par exemple, serveur web au niveau de données). Les règles suivantes sont dépendent de l’architecture du réseau (où vos serveurs sont placés) et les flux de trafic (sens les flux de trafic, ainsi que les ports sont utilisés).

    Tout d’abord vous trouverez la règle frontal pour le trafic web :

    ![Règle de pare-feu Web][12]
 
    Cette règle NAT de Destination permet le trafic d’application réel atteindre le serveur d’applications. Tandis que les autres règles permettent de sécurité, gestion, etc., règles d’Application sont qu’Autoriser les utilisateurs externes ou des services accéder à l’ou les applications. Dans cet exemple, il est un serveur web sur le port 80, et donc la règle d’application unique pare-feu redirigera le trafic entrant vers l’adresse IP externe, à l’adresse IP web les serveurs interne.

    **Remarque**: qu’il n’existe aucun port affecté dans le champ de liste cible, et donc le port d’entrée 80 (ou 443 pour le Service sélectionné) sera utilisé dans la redirection du serveur web. Si le serveur web est à l’écoute sur un autre port, par exemple le port 8080, le champ de liste cible pu être mis à jour à 10.0.1.4:8080 afin de permettre la redirection de Port également.

    La règle le trafic Application suivante est serveur principal pour permettre au serveur Web communiquer avec le serveur AppVM01 (mais pas AppVM02) via n’importe quel service :

    ![Pare-feu AppVM01 règle][13]

    Cette règle passe permet de n’importe quel serveur IIS sur le sous-réseau Frontend pour atteindre le AppVM01 (adresse IP 10.0.2.5) sur n’importe quel port, à l’aide de n’importe quel protocole pour accéder aux données nécessaires à l’application web.

    Dans cette capture d’écran un «\<explicite dest\>» est utilisé dans le champ de Destination pour indiquer 10.0.2.5 comme destination. Il peut s’agir explicite comme indiqué ou a nommé objet de réseau (tel qu’il a été effectuée dans les conditions préalables pour le serveur DNS). Il s’agit de devenir l’administrateur du pare-feu qui méthode sera utilisée. Pour ajouter un Explict Desitnation 10.0.2.5, double-cliquez sur la première ligne vide sous \<explicite dest\> , entrez l’adresse dans la fenêtre qui s’affiche.

    Cette règle passer aucun NAT n’est nécessaire dans la mesure où il s’agit du trafic interne, la méthode de connexion peut être définie sur « Non SNAT ».

    **Remarque**: réseau de la Source dans cette règle est une ressource sur le sous-réseau FrontEnd, si il sera uniquement une, ou un nombre spécifique connu de serveurs web, une ressource objet réseau peut être créée pour être plus précis aux adresses IP exactes au lieu du sous-réseau Frontend entière.

>[AZURE.TIP] Cette règle utilise le service « Tout » pour faciliter l’installation et utilisez l’exemple d’application, cela permettra également ICMPv4 (ping) dans une seule règle. Toutefois, il n’est pas recommandé. Les ports et protocoles (« Services ») doivent être réduites au minimum envisageable qui autorise l’opération d’application réduire la surface d’attaque au-delà de cette limite.

<br />

>[AZURE.TIP] Bien que cette règle affiche une référence explicite dest en cours d’utilisation, une approche cohérente doit être utilisée dans l’ensemble de la configuration du pare-feu. Il est recommandé d’utiliser l’objet réseau nommé dans l’ensemble de prise en charge et la lisibilité. La dest-explicite est utilisée ici uniquement pour afficher une méthode autre référence et n’est généralement pas recommandée (en particulier pour des configurations complexes).

- **Sortant à Internet règle**: passer cette règle autorise le trafic à partir de n’importe quel réseau Source à transmettre aux réseaux de Destination sélectionnés. Cette règle est une règle par défaut généralement déjà sur le pare-feu NextGen Barracuda, mais il est dans un état désactivé. Clic droit sur cette règle peut accéder à la commande Activer la règle. La règle illustrée ici a été modifiée pour ajouter les deux sous-réseaux locales qui ont été créées en tant que références dans la section prérequises de ce document à l’attribut Source de cette règle.

    ![Règle sortante pare-feu][14]

- **Règle DNS**: passer cette règle autorise uniquement le trafic DNS (port 53) passer au serveur DNS. Pour cet environnement que la plupart des trafic FrontEnd vers le serveur principal est bloqué, cette règle permet spécifiquement DNS.

    ![Pare-feu DNS règle][15]

    **Remarque**: capture la méthode de connexion est inclus dans cet écran. Cette règle étant pour IP interne pour le trafic des adresses IP interne, aucune utilisation n’est requise, cela la méthode de connexion est définie sur « Non SNAT » à cette règle de test.

- **Règle sous-réseau à un**: passer cette règle est une règle par défaut qui a été activée et modifiée pour permettre à n’importe quel serveur sur le sous-réseau principal pour vous connecter à n’importe quel serveur sur le sous-réseau frontal. Cette règle est le trafic tout interne afin que la méthode de connexion peut être définie sur non SNAT.

    ![Pare-feu interne VNet règle][16]

    **Remarque**: la case à cocher bidirectionnelle n’est pas sélectionnée (et n’est pas activée dans la plupart des règles), il s’agit significative à cette règle dans la mesure où il facilite cette règle « à une seule direction », une connexion peut être lancée à partir du sous-réseau principal au réseau frontal, mais non l’inverse. Si cette case à cocher a été activée, cette règle permet le trafic bidirectionnelle, ce qui n’est pas souhaité à partir de notre diagramme logique.

- **Refuser tout le trafic règle**: cela doit toujours être la règle finale (en termes de priorité) et en tant que tel si un trafic passe ne parvient pas à correspond à aucune des précédentes règles à elle sera déplacée par cette règle. Il s’agit d’une règle par défaut et aucune modification n’est généralement n’activé, est généralement nécessaires. 

    ![Règle de refus de pare-feu][17]

>[AZURE.IMPORTANT] Une fois que toutes les règles ci-dessus sont créées, il est important de réviser la priorité de chaque règle pour garantir le trafic est autorisé ou refusé comme vous le souhaitez. Dans cet exemple, les règles sont dans l’ordre qu’ils doivent apparaître dans la grille de principaux de règles dans le Client de gestion des Barracuda de transfert.

## <a name="rule-activation"></a>Activation de la règle
Avec l’ensemble de règles de modification à la spécification du diagramme logique, l’ensemble de règles doit être téléchargé dans le pare-feu et puis activé.

![Activation de règles de pare-feu][18]
 
Dans le coin supérieur droit du client management sont un cluster de boutons. Cliquez sur le bouton « Envoyer des modifications » pour envoyer les règles modifiées pour le pare-feu, puis cliquez sur le bouton « Activer ».
 
La procédure d’activation de l’ensemble de règles de pare-feu de création de l’environnement cet exemple est terminée.

## <a name="traffic-scenarios"></a>Scénarios de trafic
>[AZURE.IMPORTANT] Une clé takeway est à l’esprit que **tout** le trafic est transférés à travers le pare-feu. Donc au Bureau à distance sur le serveur IIS01, même s’il est dans le Service de nuage fin avant et sur le sous-réseau frontal, pour accéder à ce serveur nous sera peut-être RDP pour le pare-feu sur le port 8014 et puis autoriser le pare-feu acheminer la demande RDP en interne vers le Port RDP IIS01. Bouton « Connexion » du portail Azure ne fonctionne pas, car il n’existe aucun chemin RDP direct vers IIS01 (pour autant que le portail qui est visible). Cela signifie que toutes les connexions à partir d’internet sont au Service de sécurité et un Port, par exemple, secscv001.cloudapp.net:xxxx.

Pour ces scénarios, les règles de pare-feu suivantes doivent être en place :

1.  Gestion de pare-feu
2.  RDP pour IIS01
3.  RDP pour DNS01
4.  RDP pour AppVM01
5.  RDP pour AppVM02
6.  Trafic d’application sur le Web
7.  Application le trafic vers AppVM01
8.  Sortant à Internet
9.  Frontend à DNS01
10. Trafic interne sous-réseau (principal à frontal uniquement)
11. Refuser tout

L’ensemble de règles de pare-feu réel aura probablement bien d’autres règles de plus, les règles sur n’importe quel pare-feu donnée aura également les numéros de priorité différentes de celles qui sont répertoriés ici. Cette liste et numéros associés consistent à fournir pertinence entre uniquement ces 11 règles et la priorité relative entre eux. En d’autres termes ; sur le pare-feu réel, la « RDP à IIS01 » peut être règle nombre 5, mais tant qu’elle est inférieure à la règle « Gestion du pare-feu » et au-dessus de la règle « RDP DNS01 » il serait aligner dans le but de cette liste. Vous aideront également à la liste l’en dessous de scénarios permettant de concision ; par exemple, « FW règle 9 (DNS) ». Également pour les raisons de concision, les quatre règles RDP sera collectivement appelée, « les règles RDP » quand le scénario le trafic n’est pas lié à RDP.

Également Rappelez-vous que les groupes de sécurité réseau sont en place pour le trafic internet entrant sur les sous-réseaux Frontend et serveur principal.

#### <a name="allowed-internet-to-web-server"></a>(Autorisé) Internet au serveur Web
1.  Page Internet utilisateur demandes HTTP à partir de SecSvc001.CloudApp.Net (Facing Cloud Service Internet)
2.  Service de nuage passe le trafic via le point de terminaison ouvert sur le port 80 interface pare-feu sur 10.0.0.4:80
3.  Aucune NSG affectée au sous-réseau de sécurité, c’est le cas règles du NSG système autorisent le trafic vers pare-feu
4.  Le trafic accède à une adresse IP interne du pare-feu (10.0.1.4)
5.  Pare-feu commence le traitement des règles :
  1.    FW règle 1 (gestion du FW) ne s’appliquent, atteindre la règle suivante
  2.    Règles FW 2 à 5 (règles RDP) ne s’appliquent, atteindre la règle suivante
  3.    FW règle 6 (application : Web) s’applique, le trafic est autorisé, pare-feu NAT pour 10.0.1.4 (IIS01)
6.  Le sous-réseau Frontend commence le traitement de règle de trafic entrant :
  1.    NSG règle 1 (bloc Internet) ne s’applique pas (ce trafic a été NAT serait par le pare-feu, et donc l’adresse source est maintenant le pare-feu qui se trouve sur le sous-réseau de sécurité et vus par le sous-réseau Frontend NSG être le trafic « local » et n’est donc autorisé), atteindre la règle suivante
  2.    Par défaut NSG règles Autoriser le trafic de sous-réseau à un, le trafic est autorisé, arrêter le traitement des règles NSG
7.  IIS01 est à l’écoute pour le trafic web, reçoit cette requête et démarre le traitement de la demande
8.  Tentatives d’IIS01 lance une session FTP à AppVM01 sur sous-réseau principal
9.  L’itinéraire UDR sur sous-réseau Frontend rend le pare-feu le saut suivant
10. Aucune règle sortante sur sous-réseau Frontend, le trafic est autorisé
11. Pare-feu commence le traitement des règles :
  1.    FW règle 1 (gestion du FW) ne s’appliquent, atteindre la règle suivante
  2.    Règle FW 2 à 5 (règles RDP) ne s’appliquent, atteindre la règle suivante
  3.    FW règle 6 (application : Web) ne s’appliquent, atteindre la règle suivante
  4.    FW règle 7 (application : principal) s’applique, le trafic est autorisé, le pare-feu transfère le trafic vers 10.0.2.5 (AppVM01)
12. Le sous-réseau principal commence le traitement de règle de trafic entrant :
  1.    NSG règle 1 (bloc Internet) ne s’appliquent, atteindre la règle suivante
  2.    Par défaut NSG règles Autoriser le trafic de sous-réseau à un, le trafic est autorisé, arrêter le traitement des règles NSG
13.  AppVM01 reçoit la demande et lance la session et répond
14. L’itinéraire UDR sur serveur principal sous-réseau rend le pare-feu le saut suivant
15. Étant donné qu’aucune règle NSG sortant sur le serveur principal sous-réseau la réponse n’est autorisé
16. Étant donné que cela revient le trafic sur une session établie le pare-feu réussit la réponse sur le serveur web (IIS01)
17. Sous-réseau Frontend commence le traitement de règle de trafic entrant :
  1.    NSG règle 1 (bloc Internet) ne s’appliquent, atteindre la règle suivante
  2.    Par défaut NSG règles Autoriser le trafic de sous-réseau à un, le trafic est autorisé, arrêter le traitement des règles NSG
18. Le serveur IIS reçoit la réponse, effectue la transaction en AppVM01 et effectue ensuite créer la réponse HTTP, cette réponse HTTP est envoyée au demandeur
19. Dans la mesure où il n’existe aucune règle NSG sortant sur le sous-réseau Frontend que la réponse est autorisée
20. La réponse HTTP atteint le pare-feu et, car il s’agit de la réponse à une session de NAT établie est acceptée par le pare-feu
21. Le pare-feu redirige ensuite la réponse à l’utilisateur Internet
22. Dans la mesure où il existe ne sortant NSG règles ou sauts UDR sur le sous-réseau Frontend la réponse est autorisée et l’utilisateur Internet reçoit la page web demandée.

#### <a name="allowed-internet-rdp-to-backend"></a>(Autorisé) Internet RDP au serveur principal
1.  Administrateur de serveur sur internet demande session RDP AppVM01 via SecSvc001.CloudApp.Net:8025, où 8025 correspond au numéro de port utilisateur affecté pour la règle de pare-feu « RDP à AppVM01 »
2.  Le service cloud transfère le trafic via le point de terminaison ouvert sur le port 8025 interface pare-feu sur 10.0.0.4:8025
3.  Aucune NSG affectée au sous-réseau de sécurité, c’est le cas règles du NSG système autorisent le trafic vers pare-feu
4.  Pare-feu commence le traitement des règles :
  1.    FW règle 1 (gestion du FW) ne s’appliquent, atteindre la règle suivante
  2.    FW règle 2 (RDP IIS) ne s’appliquent, atteindre la règle suivante
  3.    FW règle 3 (RDP DNS01) ne s’appliquent, atteindre la règle suivante
  4.    FW règle 4 (RDP AppVM01) s’applique, le trafic est autorisé, pare-feu NAT pour 10.0.2.5:3386 (port RDP sur AppVM01)
5.  Le sous-réseau principal commence le traitement de règle de trafic entrant :
  1.    NSG règle 1 (bloc Internet) ne s’applique pas (ce trafic a été NAT serait par le pare-feu, et donc l’adresse source est maintenant le pare-feu qui se trouve sur le sous-réseau de sécurité et vus par le serveur principal sous-réseau NSG être le trafic « local » et n’est donc autorisé), atteindre la règle suivante
  2.    Par défaut NSG règles Autoriser le trafic de sous-réseau à un, le trafic est autorisé, arrêter le traitement des règles NSG
6.  AppVM01 est à l’écoute pour le trafic RDP et répond
7.  Qu’aucune règle NSG sortant, appliquent des règles par défaut et le trafic de retour est autorisé
8.  UDR achemine le trafic sortant vers le pare-feu comme saut suivant
9.  Étant donné que cela revient le trafic sur une session établie le pare-feu réussit la réponse à l’utilisateur internet
10. Session RDP est activée
11. AppVM01 vous invite à mot de passe utilisateur

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Autorisé) Recherche du serveur DNS Web sur serveur DNS
1.  Web serveur, IIS01, besoins un données flux à www.data.gov, mais qu’il faut résoudre l’adresse.
2.  La configuration réseau pour les listes VNet DNS01 (10.0.2.4 sur le serveur principal sous-réseau) en tant que le serveur DNS principal, IIS01 envoie la demande DNS DNS01
3.  UDR achemine le trafic sortant vers le pare-feu comme saut suivant
4.  Aucune règle NSG sortant n’est liés au sous-réseau Frontend, le trafic est autorisé
5.  Pare-feu commence le traitement des règles :
  1.    FW règle 1 (gestion du FW) ne s’appliquent, atteindre la règle suivante
  2.    Règle FW 2 à 5 (règles RDP) ne s’appliquent, atteindre la règle suivante
  3.    FW règles 6 et 7 (règles d’application) ne s’appliquent, atteindre la règle suivante
  4.    FW règle 8 (à Internet) ne s’appliquent, atteindre la règle suivante
  5.    S’applique-t-elle FW règle 9 (DNS), le trafic est autorisé, le pare-feu transfère le trafic vers 10.0.2.4 (DNS01)
6.  Le sous-réseau principal commence le traitement de règle de trafic entrant :
  1.    NSG règle 1 (bloc Internet) ne s’appliquent, atteindre la règle suivante
  2.    Par défaut NSG règles Autoriser le trafic de sous-réseau à un, le trafic est autorisé, arrêter le traitement des règles NSG
7.  Serveur DNS reçoit la demande
8.  Serveur DNS n’a pas l’adresse mis en cache et vous invite à fournir un serveur DNS racine sur internet
9.  UDR achemine le trafic sortant vers le pare-feu comme saut suivant
10. Aucune règle NSG sortant sur sous-réseau principal, le trafic est autorisé
11. Pare-feu commence le traitement des règles :
  1.    FW règle 1 (gestion du FW) ne s’appliquent, atteindre la règle suivante
  2.    Règle FW 2 à 5 (règles RDP) ne s’appliquent, atteindre la règle suivante
  3.    FW règles 6 et 7 (règles d’application) ne s’appliquent, atteindre la règle suivante
  4.     FW règle 8 (à Internet) s’applique, le trafic est autorisé, session est SNAT absence de serveur DNS racine sur Internet
12. Serveur DNS Internet répond, étant donné que cette session a été lancée dans le pare-feu, la réponse est acceptée par le pare-feu
13. Comme il s’agit d’une session établie, le pare-feu transmet la réponse sur le serveur d’origine, DNS01
14. Le sous-réseau principal commence le traitement de règle de trafic entrant :
  1.    NSG règle 1 (bloc Internet) ne s’appliquent, atteindre la règle suivante
  2.    Par défaut NSG règles Autoriser le trafic de sous-réseau à un, le trafic est autorisé, arrêter le traitement des règles NSG
15. Le serveur DNS reçoit et met en cache la réponse et puis répond à la requête initiale en IIS01
16. L’itinéraire UDR sur serveur principal sous-réseau rend le pare-feu le saut suivant
17. Il n’existe aucune règle NSG sortant sur le sous-réseau principal, le trafic est autorisé
18. Il s’agit d’une session établie sur le pare-feu, la réponse est transmise par le pare-feu sur le serveur IIS
19. Sous-réseau Frontend commence le traitement de règle de trafic entrant :
  1.    Il n’existe aucune règle NSG qui s’applique à entrant le trafic du sous-réseau principal au sous-réseau Frontend, afin qu’aucune de la NSG appliquer des règles
  2.    La règle de système par défaut autorisant le trafic entre sous-réseaux serait autoriser ce trafic afin que le trafic est autorisé
20. IIS01 reçoit la réponse DNS01

#### <a name="allowed-backend-server-to-frontend-server"></a>(Autorisé) Serveur principal pour le serveur frontal
1.  Un administrateur connecté au AppVM02 via RDP requiert un fichier directement depuis le serveur IIS01 via l’Explorateur de fichiers windows
2.  L’itinéraire UDR sur serveur principal sous-réseau rend le pare-feu le saut suivant
3.  Étant donné qu’aucune règle NSG sortant sur le serveur principal sous-réseau la réponse n’est autorisé
4.  Pare-feu commence le traitement des règles :
  1.    FW règle 1 (gestion du FW) ne s’appliquent, atteindre la règle suivante
  2.    Règle FW 2 à 5 (règles RDP) ne s’appliquent, atteindre la règle suivante
  3.    FW règles 6 et 7 (règles d’application) ne s’appliquent, atteindre la règle suivante
  4.    FW règle 8 (à Internet) ne s’appliquent, atteindre la règle suivante
  5.    FW règle 9 (DNS) ne s’appliquent, atteindre la règle suivante
  6.    FW règle 10 (sous-réseau interne) s’applique, le trafic est autorisé, pare-feu transmet le trafic vers 10.0.1.4 (IIS01)
5.  Sous-réseau Frontend commence le traitement de règle de trafic entrant :
  1.    NSG règle 1 (bloc Internet) ne s’appliquent, atteindre la règle suivante
  2.    Par défaut NSG règles Autoriser le trafic de sous-réseau à un, le trafic est autorisé, arrêter le traitement des règles NSG
6.  En supposant que l’autorisation et authentification appropriée, IIS01 accepte la demande et répond
7.  L’itinéraire UDR sur sous-réseau Frontend rend le pare-feu le saut suivant
8.  Dans la mesure où il n’existe aucune règle NSG sortant sur le sous-réseau Frontend que la réponse est autorisée
9.  Comme il s’agit d’une session existante sur le pare-feu cette réponse est autorisée et le pare-feu renvoie la réponse au AppVM02
10. Serveur principal sous-réseau commence le traitement de règle de trafic entrant :
  1.    NSG règle 1 (bloc Internet) ne s’appliquent, atteindre la règle suivante
  2.    Par défaut NSG règles Autoriser le trafic de sous-réseau à un, le trafic est autorisé, arrêter le traitement des règles NSG
11. AppVM02 reçoit la réponse

#### <a name="denied-internet-direct-to-web-server"></a>(Refusé) Internet direct au serveur Web
1.  Utilisateur Internet tente d’accéder au serveur web, IIS01, via le service FrontEnd001.CloudApp.Net
2.  Étant donné qu’aucun point de terminaison ouvert pour le trafic HTTP, cela n’aurait pas passer via le Service Cloud et n’est pas accéder au serveur
3.  Si les points de terminaison étaient ouverts pour une raison quelconque, NSG (bloc Internet) sur le sous-réseau Frontend serait bloquer ce trafic
4.  Enfin, l’itinéraire Frontend sous-réseau UDR devra envoyer tout le trafic sortant de IIS01 au pare-feu comme saut suivant, et le pare-feu serait voir ceci sous forme de trafic asymétrique et déposez la réponse sortante par conséquent, il existe au moins trois couches indépendantes de défense entre internet et IIS01 via son service cloud empêchent l’accès non autorisé/inapproprié.

#### <a name="denied-internet-to-backend-server"></a>(Refusé) Internet au serveur principal
1.  Utilisateur Internet tente d’accéder à un fichier sur AppVM01 via le service BackEnd001.CloudApp.Net
2.  Étant donné qu’aucun point de terminaison ouvert pour le partage de fichiers, cela n’aurait pas passer le Service Cloud et n’est pas accéder au serveur
3.  Si les points de terminaison étaient ouverts pour une raison quelconque, la NSG (bloc Internet) serait bloquer ce trafic
4.  Enfin, l’itinéraire UDR devra envoyer tout le trafic sortant de AppVM01 au pare-feu comme saut suivant, et le pare-feu serait voir ceci sous forme de trafic asymétrique et déposez la réponse sortante par conséquent, il existe au moins trois couches indépendantes de défense entre internet et AppVM01 via son service cloud empêchent l’accès non autorisé/inapproprié.

#### <a name="denied-frontend-server-to-backend-server"></a>(Refusé) Serveur frontal au serveur principal
1.  Supposons IIS01 a été déchiffré et essayez d’analyser les serveurs sous-réseau principaux programmes malveillants est en cours d’exécution.
2.  L’itinéraire Frontend sous-réseau UDR envoie tout le trafic sortant de IIS01 au pare-feu comme saut suivant. Ce n’est pas quelque chose qui peut être modifié par la machine virtuelle compromise.
3.  Le pare-feu traite donc le trafic, si la demande a été pour AppVM01, ou pour le serveur DNS pour les recherches DNS le trafic peut potentiellement par le pare-feu (en raison de FW règles 7 et 9). Tout autre trafic serait bloqué par FW règle 11 (refuser tout).
4.  Si la détection de menace avancée a été activé sur le pare-feu (qui n’est pas traitée dans ce document, consultez la documentation de fournisseur de votre matériel réseau spécifique des menaces fonctionnalités avancées), le trafic même qui serait autorisé par les règles de transfert base mentionnés dans ce document ne soient pas si le trafic contenues signatures connus ou des motifs qui marquer une règle de menace avancées.

#### <a name="denied-internet-dns-lookup-on-dns-server"></a>(Refusé) Recherche Internet DNS sur serveur DNS
1.  Utilisateur Internet tente de rechercher un enregistrement DNS interne sur DNS01 via le service BackEnd001.CloudApp.Net 
2.  Étant donné qu’aucun point de terminaison ouvert pour le trafic DNS, cela n’aurait pas passer via le Service Cloud et n’est pas accéder au serveur
3.  Si les points de terminaison étaient ouverts pour une raison quelconque, la règle NSG (bloc Internet) sur le sous-réseau Frontend serait bloquer ce trafic
4.  Enfin, l’itinéraire principal sous-réseau UDR devra envoyer tout le trafic sortant de DNS01 au pare-feu comme saut suivant, et le pare-feu serait voir ceci sous forme de trafic asymétrique et déposez la réponse sortante par conséquent, il existe au moins trois calques indépendants de défense entre internet et DNS01 via son service cloud empêchent l’accès non autorisé/inapproprié.

#### <a name="denied-internet-to-sql-access-through-firewall"></a>(Refusé) Internet vers access SQL via le pare-feu
1.  Utilisateur Internet demande des données SQL à partir de SecSvc001.CloudApp.Net (Facing Cloud Service Internet)
2.  Étant donné qu’aucun point de terminaison ouvert pour SQL, cela ne sera pas considérée le Service Cloud et n’est pas atteindre le pare-feu
3.  Si les points de terminaison SQL étaient ouverts pour une raison quelconque, le pare-feu commencez traitement des règles :
  1.    FW règle 1 (gestion du FW) ne s’appliquent, atteindre la règle suivante
  2.    Règles FW 2 à 5 (règles RDP) ne s’appliquent, atteindre la règle suivante
  3.    FW règle 6 et 7 (règles d’Application) ne s’appliquent, atteindre la règle suivante
  4.    FW règle 8 (à Internet) ne s’appliquent, atteindre la règle suivante
  5.    FW règle 9 (DNS) ne s’appliquent, atteindre la règle suivante
  6.    FW règle 10 (sous-réseau interne) ne s’appliquent, atteindre la règle suivante
  7.    FW règle 11 (refuser tout) s’applique, le trafic est traitement des règles bloqués, arrêter


## <a name="references"></a>Références
### <a name="main-script-and-network-config"></a>Script principal et configuration réseau
Enregistrer le Script complet dans un fichier de script PowerShell. Enregistrer la configuration réseau dans un fichier nommé « NetworkConf2.xml ».
Modifiez les variables définies par l’utilisateur selon vos besoins. Exécuter le script, puis suivez les instructions de configuration de règle pare-feu ci-dessus.

#### <a name="full-script"></a>Script complet
Ce script est, selon les variables définies par l’utilisateur :

1.  Se connecter à un abonnement Azure
2.  Créer un nouveau compte de stockage
3.  Créer un nouveau VNet et des trois sous de réseaux, telle que définie dans le fichier de configuration de réseau
4.  Créer des cinq machines virtuelles ; 1 pare-feu et 4 windows server machines virtuelles
5.  Configuration de UDR tels que :
  1.    Créer les deux tables itinéraire
  2.    Ajouter des itinéraires dans les tables
  3.    Lier des tables à sous-réseaux appropriés
6.  Activer le transfert IP sur l’à couloirs
7.  Configuration de NSG tels que :
  1.    Création d’un NSG
  2.    Ajout d’une règle
  3.    Liaison du NSG à sous-réseaux appropriés

Ce script PowerShell doit être exécuté localement sur qu'internet connecté PC ou serveur.

>[AZURE.IMPORTANT] Lorsque ce script est exécuté, il peut être avertissements ou autres messages d’information pop dans PowerShell. Seuls les messages d’erreur en rouge sont raison de vous inquiéter.

    <# 
     .SYNOPSIS
      Example of DMZ and User Defined Routing in an isolated network (Azure only, no hybrid connections)
    
     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Three new cloud services
       - Three Subnets (SecNet, FrontEnd, and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet
       - Three Servers on the BackEnd Subnet
       - IP Forwading from the FireWall out to the internet
       - User Defined Routing FrontEnd and BackEnd Subnets to the NVA
    
      Before running script, ensure the network configuration file is created in
      the directory referenced by $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).
    
     .Notes
      Everyone's security requirements are different and can be addressed in a myriad of ways.
      Please be sure that any sensitive data or applications are behind the appropriate
      layer(s) of protection. This script serves as an example of some of the techniques
      that can be used, but should not be used for all scenarios. You are responsible to
      assess your security needs and the appropriate protections needed, and then effectively
      implement those protections.
    
      Security Service (SecNet subnet 10.0.0.0/24)
       myFirewall - 10.0.0.4
     
      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       IIS01      - 10.0.1.4
     
      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6
    
    #>
    
    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()
    
    # User Defined Global Variables
      # These should be changes to reflect your subscription and services
      # Invalid options will fail in the validation section
    
      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    
      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"
    
      # Service Details
        $SecureService = "SecSvc001"
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"
    
      # Network Details
        $VNetName = "CorpNetwork"
        $VNetPrefix = "10.0.0.0/16"
        $SecNet = "SecNet"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf3.xml"
    
      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
    
      # UDR Details
        $FERouteTableName = "FrontEndSubnetRouteTable"
        $BERouteTableName = "BackEndSubnetRouteTable"
    
      # NSG Details
        $NSGName = "MyVNetSG"
    
    # User Defined VM Specific Config
        # Note: To ensure UDR and IP forwarding is setup
        # properly this script requires VM 0 be the NVA.
    
        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $SecureService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $SecNet
          $VMIP += "10.0.0.4"
    
        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"
    
        # VM 2 - The First Appliaction Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"
    
        # VM 3 - The Second Appliaction Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"
    
        # VM 4 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"
    
    # ----------------------------- #
    # No User Defined Varibles or   #
    # Configuration past this point #
    # ----------------------------- #
    
      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop
    
      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}
    
      # Update Subscription Pointer to New Storage Account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop
    
    # Validation
    $FatalError = $false
    
    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}
    
    If (Test-AzureName -Service -Name $SecureService) { 
        Write-Host "The SecureService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}
    
    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use" -ForegroundColor Green}
    
    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}
    
    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation varible is correct and the netowrk config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}
    
    If ($FatalError) {
        Write-Host "A fatal error has occured, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}
    
    # Create VNET
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop
    
    # Create Services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $SecureService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop
    
    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the EndPoints we'll need once we're up and running
                # Note: All traffic goes through the firewall, so we'll need to set up all ports here.
                #       Also, the firewall will be redirecting traffic to a new IP and Port in a forwarding
                #       rule, so all of these endpoint have the same public and local port and the firewall
                #       will do the mapping, NATing, and/or redirection as declared in the firewall rules.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPWeb"    -Protocol tcp -PublicPort 8014 -LocalPort 8014 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp1"   -Protocol tcp -PublicPort 8025 -LocalPort 8025 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp2"   -Protocol tcp -PublicPort 8026 -LocalPort 8026 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPDNS01"  -Protocol tcp -PublicPort 8024 -LocalPort 8024 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "RemoteDesktop" | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                }
            $i++
        }
    
    # Configure UDR and IP Forwarding
        Write-Host "Configuring UDR" -ForegroundColor Cyan
    
      # Create the Route Tables
        Write-Host "Creating the Route Tables" -ForegroundColor Cyan 
        New-AzureRouteTable -Name $BERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $BESubnet subnet"
        New-AzureRouteTable -Name $FERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $FESubnet subnet"
    
      # Add Routes to Route Tables
        Write-Host "Adding Routes to the Route Tables" -ForegroundColor Cyan 
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $FEPrefix `
            -NextHopType VNETLocal
    
      # Assoicate the Route Tables with the Subnets
        Write-Host "Binding Route Tables to the Subnets" -ForegroundColor Cyan 
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $BESubnet `
            -RouteTableName $BERouteTableName
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $FESubnet `
            -RouteTableName $FERouteTableName
    
     # Enable IP Forwarding on the Virtual Appliance
        Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] `
            |Set-AzureIPForwarding -Enable
    
    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan
    
      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"
    
      # Add NSG Rule
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 100 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *
    
      # Assign the NSG to two Subnets
        # The NSG is *not* bound to the Security Subnet. The result
        # is that internet traffic flows only to the Security subnet
        # since the NSG bound to the Frontend and Backback subnets
        # will Deny internet traffic to those subnets.
        Write-Host "Binding the NSG to two subnets" -ForegroundColor Cyan
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName
    
    # Optional Post-script Manual Configuration
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install Backend resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
    

#### <a name="network-config-file"></a>Fichier de configuration du réseau
Enregistrez ce fichier xml avec l’emplacement mis à jour et ajouter le lien vers ce fichier à la variable $NetworkConfigFile dans le script ci-dessus.

    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="SecNet">
                <AddressPrefix>10.0.0.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>

#### <a name="sample-application-scripts"></a>Exemples de Scripts Application
Si vous voulez installer un exemple d’application pour cela et d’autres exemples DMZ, un a été fourni à l’adresse suivante : [Exemple de Script Application][SampleApp]

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3design.png "DMZ bidirectionnelle avec à couloirs, NSG et UDR"
[2]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3firewalllogical.png "Vue logique de règles de pare-feu"
[3]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectfrontend.png "Créer un objet FrontEnd réseau"
[4]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectdns.png "Créer un objet de serveur DNS"
[5]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpa.png "Copie de règle RDP par défaut"
[6]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpb.png "Règle AppVM01"
[7]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconapplicationredirect.png "Icône d’application de redirection"
[8]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/icondestinationnat.png "Icône NAT de destination"
[9]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconpass.png "Icône de test"
[10]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulefirewall.png "Règle de gestion de pare-feu"
[11]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulerdp.png "Pare-feu RDP règle"
[12]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleweb.png "Règle de pare-feu Web"
[13]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleappvm01.png "Pare-feu AppVM01 règle"
[14]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleoutbound.png "Règle sortante pare-feu"
[15]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledns.png "Pare-feu DNS règle"
[16]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleintravnet.png "Pare-feu interne VNet règle"
[17]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledeny.png "Règle de refus de pare-feu"
[18]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/firewallruleactivate.png "Activation de règles de pare-feu"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
