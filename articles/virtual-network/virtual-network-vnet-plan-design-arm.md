<properties
   pageTitle="Offre de réseau virtuel Azure (VNet) et le Guide de conception | Microsoft Azure"
   description="Apprenez à planifier et concevoir des réseaux virtuels dans Azure selon vos besoins d’isolement, la connectivité et emplacement."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/08/2016"
   ms.author="jdial" />

# <a name="plan-and-design-azure-virtual-networks"></a>Planifier et concevoir des réseaux virtuel Azure

Création d’un VNet pour expérimenter est assez simple, mais sans doute, vous allez déployer plusieurs VNets au fil du temps à répondre aux besoins de production de votre organisation. Avec certains planification et la conception, vous pourrez déployer VNets et connecter des ressources que vous utilisez plus efficacement. Si vous n’êtes pas familiarisé avec VNets, il est recommandé que vous [en savoir plus sur VNets](virtual-networks-overview.md) et [comment déployer](virtual-networks-create-vnet-arm-pportal.md) une avant de poursuivre. 

## <a name="plan"></a>Plan

Une bonne connaissance des abonnements Azure, régions et ressources réseau est essentielle pour réussir. Vous pouvez utiliser la liste des considérations ci-dessous comme point de départ. Après avoir évalué ces considérations, vous pouvez définir la configuration requise pour la conception de votre réseau.

### <a name="considerations"></a>Considérations relatives à la

Avant de répondre à la planification des questions ci-dessous, procédez comme suit :

- Tout ce que vous créez dans Azure est composé d’une ou plusieurs ressources. Une machine virtuelle () est une ressource, l’interface de carte réseau (carte réseau) utilisé par une machine virtuelle est une ressource, l’adresse IP utilisée par une carte réseau est une ressource, la VNet la carte réseau est connectée à est une ressource.
- Vous créez des ressources au sein d’une [région Azure](https://azure.microsoft.com/regions/#services) et d’abonnement. Et ressources peuvent uniquement être connectés à un VNet qui existe dans le même région et abonnement dans qu'ils se trouvent. 
- Vous pouvez vous connecter VNets entre eux à l’aide d’une [Passerelle VPN](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)Azure. Vous pouvez également connecter VNets au sein des abonnements et les régions de cette manière.
- Vous pouvez vous connecter VNets à votre réseau local à l’aide d’une des [options de connectivité](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site) disponibles dans Azure. 
- Différentes ressources peuvent être regroupés dans des [groupes de ressources](../azure-resource-manager/resource-group-overview.md#resource-groups), ce qui facilite la gestion de la ressource comme une unité. Un groupe de ressources peut contenir des ressources à partir de plusieurs régions, dans la mesure où les ressources appartiennent à la même abonnement.

### <a name="define-requirements"></a>Définir des besoins

Utilisez les questions ci-dessous comme point de départ pour la conception de votre réseau Azure.  

1. Quels emplacements Azure vous utiliserez pour hôte VNets ?
2. Avez-vous besoin fournir une communication entre ces emplacements Azure ?
3. Avez-vous besoin fournir une communication entre votre VNet(s) Azure et vos centres en local ?
4. Combien d’Infrastructure comme un machines virtuelles Service (IaaS), cloud services rôles, puis procédez d’applications web que vous avez besoin pour votre solution ?
5. Avez-vous besoin d’isoler le trafic basé sur les groupes de machines virtuelles (serveurs web frontaux c'est-à-dire et les serveurs de base de données principale) ?
6. Avez-vous besoin contrôler le flux du trafic à l’aide d’authentification multifacteur ?
7. Les utilisateurs ont-ils besoin différents jeux d’autorisations à différentes ressources Azure ?

### <a name="understand-vnet-and-subnet-properties"></a>Comprendre les propriétés VNet et sous-réseau

Ressources VNet et des sous réseaux permettent de définir une limite de sécurité pour les charges de travail en cours d’exécution dans Azure. Un VNet est caractérisée par un ensemble d’espaces d’adresse, définis sous forme de blocs CIDR. 

>[AZURE.NOTE] Les administrateurs réseau vous connaissez la notation CIDR. Si vous n’êtes pas familiarisé avec CIDR, [en savoir plus sur](http://whatismyipaddress.com/cidr).

VNets contenir les propriétés suivantes.

|Propriété|Description|Contraintes|
|---|---|---|
|**nom**|Nom VNet|Chaîne de caractères jusqu'à 80. Peut contenir des lettres, nombres, de trait de soulignement, points ou des traits d’union. Doit commencer par une lettre ou un chiffre. Doit se terminer par une lettre, un nombre ou un trait de soulignement. Pouvez contient la lettre majuscule ou minuscules.|  
|**emplacement**|Emplacement Azure (également appelée région).|Doit être un des emplacements Azure valides.|
|**à EMC**|Collection de préfixes d’adresses qui constituent le VNet en notation CIDR.|Doit être un tableau de blocs d’adresse CIDR valides, y compris les plages d’adresses IP publics.|
|**sous-réseaux**|Collection des sous-réseaux qui composent le VNet|reportez-vous au tableau de propriétés sous-réseau ci-dessous.||
|**dhcpOptions**|Objet qui contient une seule propriété obligatoire nommé **dnsServers**.||
|**dnsServers**|Tableau des serveurs DNS utilisés par le VNet. Si aucun serveur n’est spécifié, la résolution de noms interne Azure est utilisée.|Doit être un tableau de jusqu'à 10 serveurs DNS, l’adresse IP.| 

Un constitue une ressource enfant d’un VNet et permettant de définir les segments d’espaces adresse au sein d’un bloc CIDR, à l’aide de préfixes d’adresses IP. Cartes réseau peut être ajoutées à sous-réseaux et connectés aux machines virtuelles, spéciale qui permet la connectivité des tâches variées.

Sous-réseaux contient les propriétés suivantes. 

|Propriété|Description|Contraintes|
|---|---|---|
|**nom**|Nom du sous-réseau|Chaîne de caractères jusqu'à 80. Peut contenir des lettres, nombres, de trait de soulignement, points ou des traits d’union. Doit commencer par une lettre ou un chiffre. Doit se terminer par une lettre, un nombre ou un trait de soulignement. Pouvez contient la lettre majuscule ou minuscules.|
|**emplacement**|Emplacement Azure (également appelée région).|Doit être un des emplacements Azure valides.|
|**addressPrefix**|Préfixe d’adresse unique qui constituent le sous-réseau en notation CIDR|Doit être un bloc CIDR unique qui fait partie de l’un des espaces d’adressage de VNet.|
|**networkSecurityGroup**|NSG appliqué au sous-réseau|voir [NSGs](resource-groups-networking.md#Network-Security-Group)|
|**routeTable**|Table de routage appliqué au sous-réseau|voir [UDR](resource-groups-networking.md#Route-table)|
|**ipConfigurations**|Collection d’objets de configuration IP utilisées par carte réseau connectée au sous-réseau|voir [configuration IP](../resource-groups-networking.md#IP-configurations)|

### <a name="name-resolution"></a>Résolution de noms

Par défaut, votre VNet utilise [résolution de noms Azure communiquée.](virtual-networks-name-resolution-for-vms-and-role-instances.md#Azure-provided-name-resolution) Pour résoudre les noms à l’intérieur de la VNet et sur l’Internet public. Toutefois, si vous vous connectez votre VNets à vos centres de données locale, vous devez fournir [votre serveur DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#Name-resolution-using-your-own-DNS-server) pour résoudre les noms entre vos réseaux.  

### <a name="limits"></a>Limites

Passez en revue les limites de mise en réseau dans l’article [Azure limite](../azure-subscription-service-limits.md#networking-limits) pour vous assurer que votre conception n’entrent en conflit avec les limites. Certaines limites peuvent être augmentées en ouvrant un tickets de support.

### <a name="role-based-access-control-rbac"></a>Contrôle d’accès basé sur un rôle (RBAC)

Vous pouvez utiliser [RBAC Azure](../active-directory/role-based-access-built-in-roles.md) pour contrôler le niveau d’accès différents utilisateurs ont peut-être à différentes ressources dans Azure. De cette façon vous pouvez répartir le travail effectué par votre équipe en fonction de leurs besoins. 

Dans le cadre des réseaux virtuels sont concernées, les utilisateurs du rôle de **Collaborateur réseau** disposent du contrôle total sur les ressources de réseau virtuel Azure le Gestionnaire de ressources. De même, les utilisateurs du rôle de **Collaborateur réseau classique** disposent du contrôle total sur les ressources réseau virtuel classique.

>[AZURE.NOTE] Vous pouvez également [créer vos propres rôles](../active-directory/role-based-access-control-configure.md) pour séparer vos besoins d’administration.

## <a name="design"></a>Création

Une fois que vous connaissez les réponses aux questions dans la section [planifier](#Plan) , passez en revue les informations suivantes avant la définition de votre VNets.

### <a name="number-of-subscriptions-and-vnets"></a>Nombre d’abonnements et VNets

Vous devez envisager de créer plusieurs VNets dans les scénarios suivants :

- **Machines virtuelles qui doivent être placés à différents emplacements Azure**. VNets dans Azure sont régionaux. Ils ne comprennent pas emplacements. Par conséquent, vous devez au moins un VNet pour chaque emplacement Azure dans que vous souhaitez machines virtuelles hôte.
- **Charges de travail qui doivent être complètement isolées entre elles**. Vous pouvez créer VNets distincte, que même utilisent les espaces d’adresses IP mêmes, pour isoler des différentes charges de travail à partir d’un autre. 

N’oubliez pas que les limites de que vous voir ci-dessus sont par région, par abonnement. Cela signifie que vous pouvez utiliser plusieurs abonnements pour augmenter la limite de ressources que vous pouvez mettre à jour dans Azure. Vous pouvez utiliser un réseau privé virtuel à un site ou un circuit ExpressRoute, pour vous connecter VNets dans différents abonnements.

### <a name="subscription-and-vnet-design-patterns"></a>Abonnement et les modèles de conception VNet

Le tableau ci-dessous indique certains modèles de conception courants pour l’utilisation des abonnements et VNets.

|Scénario|Diagramme|Professionnels de l’informatique|Inconvénients|
|---|---|---|---|
|Abonnement unique, deux VNets par application|![Abonnement unique](./media/virtual-network-vnet-plan-design-arm/figure1.png)|Un abonnement à gérer.|Nombre maximal de VNets par région Azure. Vous avez besoin de plusieurs abonnements par la suite. Consultez l’article [Azure limite](../azure-subscription-service-limits.md#networking-limits) pour plus d’informations.|
|Un abonnement par application, deux VNets par application|![Abonnement unique](./media/virtual-network-vnet-plan-design-arm/figure2.png)|Utilise uniquement deux VNets par abonnement.|Plus difficile à gérer lorsqu’il y a trop d’applications.|
|Un seul abonnement division, deux VNets par application.|![Abonnement unique](./media/virtual-network-vnet-plan-design-arm/figure3.png)|Équilibre entre le nombre d’abonnements et VNets.|Nombre maximal de VNets par division (abonnement). Consultez l’article [Azure limite](../azure-subscription-service-limits.md#networking-limits) pour plus d’informations.|
|Un seul abonnement division, deux VNets par groupe d’applications.|![Abonnement unique](./media/virtual-network-vnet-plan-design-arm/figure4.png)|Équilibre entre le nombre d’abonnements et VNets.|Applications doivent être isolées à l’aide de sous-réseaux et NSGs.|


### <a name="number-of-subnets"></a>Nombre de sous réseaux

Vous devez prendre en compte plusieurs sous réseaux dans un VNet dans les scénarios suivants :

- **Pas de suffisamment d’adresses IP privé pour toutes les cartes réseau dans un sous-réseau**. Si votre espace d’adressage sous-réseau ne contient pas de suffisamment d’adresses IP pour le nombre de cartes réseau dans le sous-réseau, vous devez créer plusieurs sous réseaux. N’oubliez pas que Azure réserve 5 adresses IP privées à partir de chaque sous-réseau ne peuvent pas être utilisés : les premier et derniers adresses de l’espace d’adressage (pour l’adresse et multidiffusion) et 3 servir en interne (à des fins DHCP et DNS). 
- **Sécurité**. Vous pouvez utiliser sous-réseaux pour séparer les groupes de machines virtuelles à partir d’un autre pour les charges de travail qui ont une structure multicouche et appliquer différents [groupes de sécurité réseau (NSGs)](virtual-networks-nsg.md#subnets) pour ces sous-réseaux.
- **Connectivité hybride**. Vous pouvez utiliser les passerelles VPN et circuits ExpressRoute pour [vous connecter](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site) votre VNets entre eux et à votre center(s) de données locale. Passerelles VPN et circuits ExpressRoute nécessitent un sous réseau de leurs propres à créer.
- **Appareils virtuelle**. Vous pouvez utiliser un matériel virtuel, tel qu’un pare-feu, les accélérateurs WAN ou passerelle VPN dans un VNet Azure. Lorsque vous procédez ainsi, vous avez besoin pour [acheminer le trafic](virtual-networks-udr-overview.md) vers les appareils et les Isolez dans leur propre sous-réseau.

### <a name="subnet-and-nsg-design-patterns"></a>Modèles de conception sous-réseau et NSG

Le tableau ci-dessous indique certains modèles de conception courants pour l’utilisation de sous-réseaux.

|Scénario|Diagramme|Professionnels de l’informatique|Inconvénients|
|---|---|---|---|
|Sous-réseau unique, NSGs par couches d’application, par application|![Sous-réseau unique](./media/virtual-network-vnet-plan-design-arm/figure5.png)|Qu’un seul sous-réseau à gérer.|Plusieurs NSGs nécessaires pour isoler chaque application.|
|Un seul sous-réseau par application, NSGs par couches d’application|![Sous-réseau par application](./media/virtual-network-vnet-plan-design-arm/figure6.png)|Moins de NSGs à gérer.|Plusieurs sous réseaux pour gérer.|
|Un seul sous-réseau par couches d’application, NSGs par application.|![Sous-réseau par calque](./media/virtual-network-vnet-plan-design-arm/figure7.png)|Équilibre entre le nombre de sous réseaux et NSGs.|Nombre maximal de NSGs par abonnement. Consultez l’article [Azure limite](../azure-subscription-service-limits.md#networking-limits) pour plus d’informations.|
|Un seul sous-réseau par couches d’application, par application, NSGs par sous-réseau|![Sous-réseau par couche par application](./media/virtual-network-vnet-plan-design-arm/figure8.png)|Éventuellement plus petit nombre de NSGs.|Plusieurs sous réseaux pour gérer.|

## <a name="sample-design"></a>Exemple de conception

Pour illustrer l’application des informations dans cet article, envisagez le scénario suivant.

Vous travaillez pour une société qui a 2 centres de données en Amérique du Nord et deux centres de données Europe. Que vous avez identifiés 6 applications qui fait face à des clients différents conservées par 2 différentes divisions que vous voulez migrer vers Azure comme un pilote. L’architecture de base pour les applications sont les suivantes :

- App1, App2, App3 et App4 sont des applications web hébergées sur des serveurs Linux Ubuntu en cours d’exécution. Chaque application connecte à un serveur d’application distinct qui héberge les services RESTful sur des serveurs Linux. Les services RESTful se connectent à une base de données MySQL principal.
- App5 et App6 sont des applications web hébergées sur des serveurs Windows exécutant Windows Server 2012 R2. Chaque application se connecte à une base de données SQL Server principal.
- Toutes les applications sont actuellement hébergées dans un des centres de données de la société en Amérique du Nord.
- Les centres de données locale utilisent l’espace d’adressage 10.0.0.0/8.

Vous devez concevoir une solution réseau virtuel qui remplit les conditions suivantes :

- Chaque division ne doit pas affectée par la consommation de ressources d’autres divisions.
- Vous devez réduire la quantité de VNets et sous-réseaux pour faciliter la gestion.
- Chaque division doit avoir un seul test/développement VNet utilisée pour toutes les applications.
- Chaque application est hébergée dans 2 centres de données Azure différents par continent (Amérique du Nord et Europe).
- Chaque application est complètement isolée uns des autres.
- Chaque application sont accessibles par les clients via Internet à l’aide de HTTP.
- Chaque application est accessible par des utilisateurs connectés aux centres de données locale à l’aide d’un tunnel chiffré.
- Connexion aux centres de données locale doit utiliser les périphériques VPN existants.
- Groupe mise en réseau de la société doit avoir un contrôle total sur la configuration VNet.
- Les développeurs dans chaque division uniquement doivent être en mesure de déployer des machines virtuelles à sous-réseaux existants.
- Toutes les applications vont être migrées qu’ils figurent Azure (levée et MAJ).
- Les bases de données dans chaque emplacement devraient répliquer sur d’autres emplacements Azure une fois par jour.
- Chaque application doit utiliser 5 serveurs web frontaux, 2 serveurs d’application (si nécessaire) et 2 serveurs de base de données.

### <a name="plan"></a>Plan

Vous devez commencer la conception de votre planification en répondant à la question dans la section [définir](#Define-requirements) comme illustré ci-dessous.

1. Quels emplacements Azure vous utiliserez pour hôte VNets ?

    2 emplacements en Amérique du Nord et 2 emplacements en Europe. Vous devez choisir celles basées sur l’emplacement physique de vos centres de données locale existant. De cette manière votre connexion entre vos emplacements physiques et Azure aura une latence mieux.

2. Avez-vous besoin fournir une communication entre ces emplacements Azure ?

    Oui. Dans la mesure où les bases de données doivent être répliquées sur tous les emplacements.

3. Avez-vous besoin fournir une communication entre votre VNet(s) Azure et vos localement center(s) de données ?

    Oui. Dans la mesure où les utilisateurs connectés aux centres de données locale doivent être en mesure d’accéder aux applications via un tunnel chiffré.
 
4. Machines virtuelles IaaS combien devez-vous pour votre solution ?

    Machines virtuelles IaaS 200. App1, App2 et App3 requièrent chaque 5 serveurs web, chaque 2 serveurs d’applications et 2 serveurs de base de données. Il s’agit d’un total de 9 IaaS machines virtuelles par application ou 36 machines virtuelles IaaS. App5 et App6 requièrent 5 serveurs web et 2 serveurs de base de données. Il s’agit d’un total de 7 IaaS machines virtuelles par application ou 14 IaaS machines virtuelles. Par conséquent, vous devez 50 IaaS machines virtuelles pour toutes les applications de chaque région Azure. Étant donné que nous devons utiliser 4 régions, il y ait 200 machines virtuelles IaaS.

    Vous avez également besoin fournir des serveurs DNS dans chaque VNet ou dans vos centres de données locale pour résoudre le nom entre vos ordinateurs virtuels IaaS Azure et de votre réseau local. 

5. Avez-vous besoin d’isoler le trafic basé sur les groupes de machines virtuelles (serveurs web frontaux c'est-à-dire et les serveurs de base de données principale) ?

    Oui. Chaque application doit être complètement isolée des autres, et chaque calque application doit également être isolé. 

6. Avez-vous besoin contrôler le flux du trafic à l’aide d’authentification multifacteur ?

    Non. Authentification multifacteur peut servir à mieux contrôler le flux de trafic, y compris l’enregistrement de plan de données plus détaillée. 

7. Les utilisateurs ont-ils besoin différents jeux d’autorisations à différentes ressources Azure ?

    Oui. L’équipe réseau doit contrôle total sur les paramètres de mise en réseau virtuels, alors que les développeurs uniquement doivent être en mesure de déployer leurs machines virtuelles à sous-réseaux existants. 

### <a name="design"></a>Création

Vous devez suivre la conception spécifiant abonnements, VNets, sous-réseaux et NSGs. Nous allons étudier NSGs ici, mais vous devez en savoir plus sur [NSGs](virtual-networks-nsg.md) avant de terminer votre conception.

**Nombre d’abonnements et VNets**

Les conditions suivantes sont liées aux abonnements et VNets :

- Chaque division ne doit pas affectée par la consommation de ressources d’autres divisions.
- Vous devez réduire la quantité de VNets et des sous réseaux.
- Chaque division doit avoir un seul test/développement VNet utilisée pour toutes les applications.
- Chaque application est hébergée dans 2 centres de données Azure différents par continent (Amérique du Nord et Europe).

En fonction de ces exigences, vous devez un abonnement pour chaque division. De cette façon, utilisation des ressources à partir d’une division pas comptera dans limites pour les autres divisions. Et, dans la mesure où vous souhaitez réduire le nombre de VNets, envisagez d’utiliser le modèle **d’un abonnement par division, deux VNets par groupe des applications** comme indiqué ci-dessous.

![Abonnement unique](./media/virtual-network-vnet-plan-design-arm/figure9.png)

Vous devez également indiquer l’espace d’adressage pour chaque VNet. Étant donné que vous devez centres de connectivité entre les données locales et les régions Azure, l’espace d’adressage utilisé pour Azure VNets ne peut pas entrer en conflit avec le réseau local, et l’espace d’adressage utilisé par chaque VNet ne doit pas entrer en conflit avec d’autres VNets existant. Vous pouvez utiliser les espaces d’adressage dans le tableau ci-dessous pour répondre à ces exigences.  

|**Abonnement**|**VNet**|**Région Azure**|**Espace d’adressage**|
|---|---|---|---|
|BU1|ProdBU1US1|États-Unis Ouest|172.16.0.0/16|
|BU1|ProdBU1US2|États-Unis Extrême-Orient|172.17.0.0/16|
|BU1|ProdBU1EU1|Europe du Nord|172.18.0.0/16|
|BU1|ProdBU1EU2|Europe occidentale|172.19.0.0/16|
|BU1|TestDevBU1|États-Unis Ouest|172.20.0.0/16|
|BU2|TestDevBU2|États-Unis Ouest|172.21.0.0/16|
|BU2|ProdBU2US1|États-Unis Ouest|172.22.0.0/16|
|BU2|ProdBU2US2|États-Unis Extrême-Orient|172.23.0.0/16|
|BU2|ProdBU2EU1|Europe du Nord|172.24.0.0/16|
|BU2|ProdBU2EU2|Europe occidentale|172.25.0.0/16|

**Nombre de sous-réseaux et NSGs**

Les conditions suivantes sont liées aux sous-réseaux et NSGs :

- Vous devez réduire la quantité de VNets et des sous réseaux.
- Chaque application est complètement isolée uns des autres.
- Chaque application sont accessibles par les clients via Internet à l’aide de HTTP.
- Chaque application est accessible par des utilisateurs connectés aux centres de données locale à l’aide d’un tunnel chiffré.
- Connexion aux centres de données locale doit utiliser les périphériques VPN existants.
- Les bases de données dans chaque emplacement devraient répliquer sur d’autres emplacements Azure une fois par jour.

En fonction de ces exigences, vous pouvez utiliser un sous-réseau par couches d’application et utilisez NSGs pour filtrer le trafic par application. Ainsi, vous ne devez 3 sous-réseaux dans chaque VNet (frontal, couches d’application et couche de données) et une NSG par application par sous-réseau. Dans ce cas, envisagez d’utiliser le modèle de conception **d’un sous-réseau par couches d’application, NSGs par application** . L’illustration suivante montre l’utilisation du modèle de conception représentant le VNet **ProdBU1US1** .

![Un seul sous-réseau par calque, un NSG par application et par calque](./media/virtual-network-vnet-plan-design-arm/figure11.png)

Toutefois, vous devez également créer un sous-réseau supplémentaire pour la connectivité VPN entre le VNets et vos centres de données locale. Et vous devez spécifier l’espace d’adressage de chaque sous réseau. L’illustration ci-dessous montre un exemple de solution pour **ProdBU1US1** VNet. Vous pouvez répliquer ce scénario pour chaque VNet. Chaque couleur représente une autre application.

![Exemple VNet](./media/virtual-network-vnet-plan-design-arm/figure10.png)

**Contrôle d’accès**

Les conditions suivantes sont liées au contrôle d’accès :

- Groupe mise en réseau de la société doit avoir un contrôle total sur la configuration VNet.
- Les développeurs dans chaque division uniquement doivent être en mesure de déployer des machines virtuelles à sous-réseaux existants.

En fonction de ces exigences, vous pouvez ajouter des utilisateurs à partir de l’équipe de mise en réseau pour le rôle de **Collaborateur réseau** intégré dans chaque abonnement ; et créer un rôle personnalisé pour les développeurs d’applications dans chaque abonnement en leur attribuant des droits pour ajouter des machines virtuelles à sous-réseaux existants.

## <a name="next-steps"></a>Étapes suivantes

- [Déployer un réseau virtuel](virtual-networks-create-vnet-arm-template-click.md) basée sur un scénario.
- Comprendre comment [équilibrer](../load-balancer/load-balancer-overview.md) des machines virtuelles IaaS et [gérer le routage sur plusieurs régions Azure](../traffic-manager/traffic-manager-overview.md).
- Apprenez-en davantage sur [NSGs et comment planifier et concevoir](virtual-networks-nsg.md) une solution NSG.
- En savoir plus sur votre [croisée locaux et options de connectivité VNet](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site).
