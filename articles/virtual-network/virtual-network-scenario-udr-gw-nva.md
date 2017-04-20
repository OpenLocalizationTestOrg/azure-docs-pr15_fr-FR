<properties 
   pageTitle="Connexion hybride avec application de niveau 2 | Microsoft Azure"
   description="Découvrez comment déployer authentification multifacteur et UDR pour créer un environnement d’application à plusieurs niveaux dans Azure"
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
   ms.date="05/05/2016"
   ms.author="jdial" />

# <a name="virtual-appliance-scenario"></a>Scénario d’équipement virtuel

Un scénario courant parmi les plus grand client Azure est nécessaire pour fournir une application à deux niveaux visible sur Internet, tout en autorisant l’accès à la couche précédent à partir d’un centre de données locale. Ce document vous guidera dans un scénario à l’aide d’authentification multifacteur réseau utilisateur définis itinéraires (UDR) et une passerelle VPN pour déployer un environnement à deux niveaux qui remplit les conditions suivantes :

- Application Web doit être accessible à partir d’Internet public uniquement.
- Serveur Web qui héberge l’application doit être en mesure d’accéder à un serveur d’applications serveur principal.
- Tout le trafic Internet vers l’application web doit passer par un dispositif virtuel pare-feu. Cette solution virtuelle sera utilisée pour le trafic Internet uniquement.
- Tout le trafic vers le serveur de l’application doit passer par un dispositif virtuel pare-feu. Cette solution virtuelle est utilisée pour accéder au serveur principal fin et l’accès en provenance du réseau local via une passerelle VPN.
- Les administrateurs doivent pouvoir gérer l’authentification multifacteur pare-feu à partir de leurs ordinateurs locaux, en utilisant un tiers du pare-feu matériel virtuel est utilisé en mode exclusif à des fins de gestion.

Il s’agit d’un scénario DMZ standard avec une DMZ et un réseau protégé. Ce scénario peut être créé dans Azure à l’aide de NSGs, authentification multifacteur pare-feu ou une combinaison des deux. Le tableau ci-dessous indique certains des avantages et inconvénients entre NSGs et les appareils virtuel pare-feu.

||Professionnels de l’informatique|Inconvénients|
|---|---|---|
|NSG|Aucun coût. <br/>Intégré à RBAC Azure. <br/>Règles peuvent être créées dans les modèles de processeur.|La complexité peut varier dans les environnements étendus. |
|Pare-feu|Contrôle total sur le plan de données. <br/>Gestion centralisée via console de pare-feu.|Coût du matériel de pare-feu. <br/>Pas intégré RBAC Azure.|

La solution ci-dessous utilise l’authentification multifacteur pare-feu pour implémenter un scénario de réseau DMZ/protégé.

## <a name="considerations"></a>Considérations relatives à la

Vous pouvez déployer l’environnement expliqué ci-dessus dans Azure à l’aide des fonctionnalités disponibles aujourd'hui, comme suit.

- **Réseau virtuelle (VNet)**. Un VNet Azure se comporte de manière semblable à un réseau local et peuvent être placé dans un ou plusieurs sous-réseaux pour fournir isolement le trafic et la séparation des problèmes.
- **Application virtuelle**. Plusieurs partenaires fournissent authentification multifacteur dans Azure Marketplace pouvant être utilisées pour les trois pare-feu ci-dessus. 
- **Itinéraires (UDR) définies par l’utilisateur**. Tables de routage peuvent contenir UDRs utilisés par Azure mise en réseau pour contrôler le flux de paquets dans un VNet. Ces tableaux itinéraire peut être appliqués aux sous-réseaux. Une des fonctionnalités plus récentes dans Azure consiste à appliquer une table d’itinéraires à GatewaySubnet, ce qui permet de transférer tout le trafic entrant la VNet Azure à partir d’une connexion hybride à un matériel virtuel.
- **Transfert IP**. Par défaut, le moteur de mise en réseau Azure transmettre les paquets au réseau virtuel interface cartes uniquement si l’adresse IP de destination paquets correspond à l’adresse IP NIC. Par conséquent, si un UDR définit qu’un paquet doit être envoyé à un appareil virtuel donné, le moteur de mise en réseau Azure serait déposez paquet. Pour vous assurer que le paquet a remis à une machine virtuelle (dans ce cas, un appareil virtuel) qui n’est pas la destination réelle pour le paquet, vous devez activer le transfert d’IP pour l’application virtuelle.
- **Groupes de sécurité réseau (NSGs)**. L’exemple ci-dessous ne rend pas utiliser de NSGs, mais vous pouvez utiliser NSGs appliqués aux cartes réseau et/ou au sous-réseaux dans cette solution pour filtrer davantage le trafic entrant et sortant ces sous-réseaux et cartes réseau.


![Connectivité IPv6](./media/virtual-network-scenario-udr-gw-nva/figure01.png)

Dans cet exemple, il est un abonnement qui inclut les éléments suivants :

- 2 groupes de ressources, n’apparaît ne pas dans le diagramme. 
    - **ONPREMRG**. Contient toutes les ressources nécessaires pour simuler un réseau local.
    - **AZURERG**. Contient toutes les ressources nécessaires pour l’environnement de réseau virtuel Azure. 
- Un VNet nommé **onpremvnet** utilisé pour simuler un centre de données locale segmenté comme indiqué ci-dessous.
    - **onpremsn1**. Sous-réseau contenant une machine virtuelle (machine virtuelle) en cours d’exécution Ubuntu pour simuler un serveur local.
    - **onpremsn2**. Sous-réseau contenant un ordinateur exécutant Ubuntu pour simuler un ordinateur local utilisé par un administrateur.
- Il existe un dispositif virtuel pare-feu nommé **OPFW** sur **onpremvnet** permettent de conserver un tunnel vers **azurevnet**.
- Un VNet nommé **azurevnet** segmentée comme indiqué ci-dessous.
    - **azsn1**. Sous-réseau pare-feu externe utilisé en mode exclusif pour le pare-feu externe. Tout le trafic Internet arriveront à ce sous-réseau. Ce sous-réseau contient uniquement une carte réseau liée au pare-feu externe.
    - **azsn2**. Sous-réseau frontal hébergeant un ordinateur exécutant comme un serveur web qui est accessibles à partir d’Internet.
    - **azsn3**. Sous-réseau version serveur qui héberge un ordinateur exécutant un serveur d’applications serveur principal qui est accessibles par le serveur web frontal.
    - **azsn4**. Sous-réseau gestion utilisé en mode exclusif à fournir un accès de gestion à tous les pare-feu/authentification multifacteur. Ce sous-réseau contient uniquement une carte réseau pour chaque matériel virtuel pare-feu utilisé dans la solution.
    - **GatewaySubnet**. Sous-réseau de connexion hybride Azure requis pour ExpressRoute et passerelle VPN pour fournir une connectivité entre Azure VNets et d’autres réseaux. 
- Il existe 3 pare-feu/authentification multifacteur dans le réseau **azurevnet** . 
    - **AZF1**. Pare-feu externe exposé à l’Internet public en utilisant une ressource d’adresse IP publique dans Azure. Vous devez vous assurer que vous disposez d’un modèle à partir de la place de marché, ou directement à partir de votre fournisseur de matériel, cette dispositions un appareil virtuel 3-carte réseau.
    - **AZF2**. Pare-feu interne permettent de contrôler le trafic entre **azsn2** et **azsn3**. C’est également un dispositif virtuel 3-carte réseau.
    - **AZF3**. Pare-feu gestion accessible aux administrateurs du centre de données en local et connecté à un sous-réseau gestion permet de gérer tous les appareils de pare-feu. Vous pouvez rechercher des modèles de matériel virtuel carte réseau 2 sur le marché, ou demander une directement à partir de votre fournisseur de matériel.

## <a name="user-defined-routing-udr"></a>Routage (UDR) définies par l’utilisateur

Chaque sous-réseau dans Azure peut être lié à une table UDR permet de définir comment le trafic initié dans la mesure où sous-réseau est routé. Si aucune UDRs ne sont définis, Azure utilise itinéraires par défaut pour autoriser le trafic d’un sous-réseau à l’autre. Pour mieux comprendre UDRs, visitez le site [à quoi correspondent itinéraires définies par l’utilisateur et transfert IP](./virtual-networks-udr-overview.md#ip-forwarding).

Pour vous assurer que la communication s’effectue via l’application de pare-feu approprié, en fonction de la dernière demande ci-dessus, vous devez créer la table d’itinéraires suivante contenant UDRs dans **azurevnet**.

### <a name="azgwudr"></a>azgwudr

Dans ce scénario, le seul trafic découlant local vers Azure servira à gérer les pare-feu en vous connectant à **AZF3**et que le trafic doit accédez à travers le pare-feu interne, **AZF2**. Par conséquent, qu’une seule route est nécessaire dans **GatewaySubnet** comme illustré ci-dessous.

|Destination|Saut suivant|Explication|
|---|---|---|
|10.0.4.0/24|10.0.3.11|Autorise le trafic local atteindre le pare-feu de gestion des **AZF3**|

### <a name="azsn2udr"></a>azsn2udr

|Destination|Saut suivant|Explication|
|---|---|---|
|10.0.3.0/24|10.0.2.11|Autorise le trafic vers le sous-réseau version serveur qui héberge le serveur d’applications via **AZF2**|
|0.0.0.0/0|10.0.2.10|Permet à tous les autres trafics soit routé via **AZF1**|

### <a name="azsn3udr"></a>azsn3udr

|Destination|Saut suivant|Explication|
|---|---|---|
|10.0.2.0/24|10.0.3.10|Autorise le trafic vers **azsn2** s’écouler de serveur d’applications vers le serveur Web via **AZF2**|

Vous devez également créer les tables itinéraire pour les sous-réseaux dans **onpremvnet** pour simuler le centre de données locale.

### <a name="onpremsn1udr"></a>onpremsn1udr

|Destination|Saut suivant|Explication|
|---|---|---|
|192.168.2.0/24|192.168.1.4|Autorise le trafic vers **onpremsn2** via **OPFW**|

### <a name="onpremsn2udr"></a>onpremsn2udr

|Destination|Saut suivant|Explication|
|---|---|---|
|10.0.3.0/24|192.168.2.4|Autorise le trafic vers le sous-réseau sauvegardé dans Azure via **OPFW**|
|192.168.1.0/24|192.168.2.4|Autorise le trafic vers **onpremsn1** via **OPFW**|

## <a name="ip-forwarding"></a>Transfert IP 

UDR et transfert IP sont des fonctionnalités que vous pouvez utiliser conjointement pour permettre l’authentification multifacteur être utilisés pour contrôler le flux de trafic dans un VNet Azure.  Un appareil virtuel est rien d’autre qu’un ordinateur virtuel qui exécute une application utilisée pour gérer le trafic réseau d’une façon quelconque, par exemple un pare-feu ou un périphérique NAT.

Cette solution virtual machine virtuelle doit être en mesure de recevoir le trafic entrant qui n’est pas adressé à lui-même. Pour permettre à un ordinateur virtuel recevoir le trafic adressé vers d’autres destinations, vous devez activer le routage IP pour la machine virtuelle. Il s’agit d’un Azure définir, pas de paramètre dans le système d’exploitation invité. Votre matériel virtuel reste encore à exécuter un type de l’application de gérer le trafic entrant et acheminer correctement.

Pour en savoir plus sur le routage IP, visitez le site [à quoi correspondent itinéraires définies par l’utilisateur et transfert IP](./virtual-networks-udr-overview.md#ip-forwarding).

Par exemple, supposons que vous avez la configuration suivante dans un vnet Azure :

- Sous-réseau **onpremsn1** contient une machine virtuelle nommée **onpremvm1**.
- Sous-réseau **onpremsn2** contient une machine virtuelle nommée **onpremvm2**.
- Un appareil virtuel nommé **OPFW** est connecté à **onpremsn1** et **onpremsn2**.
- Un itinéraire définis par l’utilisateur lié à **onpremsn1** Spécifie que tout le trafic vers **onpremsn2** doit être envoyé à **OPFW**.

À ce stade, si **onpremvm1** tente d’établir une connexion avec **onpremvm2**, le UDR sera utilisé et le trafic sera envoyé aux **OPFW** comme saut suivant. Gardez à l’esprit que la destination du paquet réelle n’est pas modifiée, elle indique toujours **onpremvm2** correspond à la destination. 

Sans le routage IP activé pour **OPFW**, la logique de mise en réseau virtuelle Azure supprime les paquets, car il permet uniquement les paquets envoyés à une machine virtuelle si l’adresse IP de la machine virtuelle est la destination pour le paquet.

Avec le routage IP, la logique de réseau virtuel Azure transférera les paquets à OPFW, sans modifier son adresse de destination d’origine. **OPFW** doit gérer les paquets et déterminez que faire avec eux.

Pour le scénario ci-dessus pour travailler, vous devez activer le routage IP sur les cartes réseau pour **OPFW**, **AZF1**, **AZF2**et **AZF3** qui sont utilisés pour le routage (toutes les cartes réseau à l’exception de ceux liés au sous-réseau gestion). 

## <a name="firewall-rules"></a>Règles de pare-feu

Comme décrit ci-dessus, transfert IP uniquement garantit paquets sont envoyés à l’authentification multifacteur. Votre appareil reste encore à décider que faire avec les paquets. Dans le scénario ci-dessus, vous devez créer les règles suivantes dans vos appareils :

### <a name="opfw"></a>OPFW

OPFW représente un périphérique local contenant les règles suivantes :

- **Acheminer**: tout le trafic vers 10.0.0.0/16 (**azurevnet**) doit être envoyé par le biais tunnel **ONPREMAZURE**.
- **Stratégie**: autoriser tout le trafic bidirectionnel entre **port2** et **ONPREMAZURE**.
 
### <a name="azf1"></a>AZF1

AZF1 représente un appareil virtuel Azure contenant les règles suivantes :

- **Stratégie**: autoriser le trafic bidirectionnelle entre **port1** et **port2**.

### <a name="azf2"></a>AZF2

AZF2 représente un appareil virtuel Azure contenant les règles suivantes :

- **Acheminer**: tout le trafic vers 10.0.0.0/16 (**onpremvnet**) doit être envoyé à l’adresse IP de passerelle Azure (c'est-à-dire 10.0.0.1) par le biais **port1**.
- **Stratégie**: autoriser le trafic bidirectionnelle entre **port1** et **port2**.

## <a name="network-security-groups-nsgs"></a>Groupes de sécurité réseau (NSGs)

Dans ce scénario, NSGs ne sont pas utilisés. Toutefois, vous pouvez appliquer NSGs à chaque sous-réseau pour restreindre le trafic entrant et sortant. Par exemple, vous pouvez appliquer les règles suivantes NSG au sous-réseau FW externes.

**Entrant**

- Autoriser tout le trafic TCP provenant d’Internet vers le port 80 sur n’importe quel ordinateur virtuel dans le sous-réseau.
- Refuser tous les autres trafics à partir d’Internet.

**Sortant**
- Refuser l’ensemble du trafic à Internet.

## <a name="high-level-steps"></a>Étapes de niveau élevés

Pour déployer ce scénario, suivez les étapes de haut niveau ci-dessous.

1.  Connectez-vous à votre abonnement Azure.
2.  Si vous voulez déployer un VNet pour simuler le réseau local, mise en service les ressources qui font partie **d’ONPREMRG**.
3.  Configurer les ressources qui font partie **d’AZURERG**.
4.  Mise en service au tunnel à partir de **onpremvnet** à **azurevnet**.
5.  Une fois toutes les ressources sont mis en service, ouvrez une session sur **onpremvm2** et ping 10.0.3.101 pour tester la connectivité entre **onpremsn2** et **azsn3**.
