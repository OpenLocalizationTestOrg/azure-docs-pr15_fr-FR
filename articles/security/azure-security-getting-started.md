<properties
   pageTitle="Prise en main sécurité Microsoft Azure | Microsoft Azure"
   description="Cet article fournit une vue d’ensemble des fonctionnalités de Microsoft Azure sécurité et Généralités pour les organisations qui effectuent une migration leurs ressources à un fournisseur de cloud."
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/19/2016"
   ms.author="yuridio"/>

#<a name="getting-started-with-microsoft-azure-security"></a>Prise en main sécurité Microsoft Azure

Lorsque vous voulez créer ou déplacer des ressources informatiques à un fournisseur de cloud, vous comptez sur les capacités de cette organisation pour protéger les applications et les données de confier à leurs services et les contrôles de sécurité qu’ils vous fournissent pour contrôler la sécurité de vos ressources sur le nuage.

Infrastructure d’Azure est conçu de la fonctionnalité d’applications pour l’hébergement des millions de clients simultanément et les fondations fiable sur lequel les entreprises peuvent répondre à leurs besoins de sécurité. En outre, Azure vous propose une large gamme d’options de sécurité configurable et la possibilité de contrôler les afin que vous pouvez personnaliser la sécurité afin de répondre aux besoins uniques pour vos déploiements.

Dans cet article de la vue d’ensemble sur la sécurité Azure, nous allons aborder :

-   Fonctionnalités et services azure vous pouvez utiliser pour sécuriser vos services et des données au sein d’Azure

-   Comment Microsoft protège l’infrastructure pour mieux vous protéger vos données et applications Azure

##<a name="identity-and-access-management"></a>Gestion des identités et access


Il est essentiel de contrôler l’accès à l’infrastructure informatique, les données et les applications. Dans Microsoft Azure, ces fonctionnalités sont remies par services tels que Azure Active Directory, le stockage Azure et prise en charge pour les nombreuses standards et API.

[Azure Active Directory](../active-directory/active-directory-whatis.md) (Azure AD) est un référentiel d’identité et le moteur qui fournit l’authentification, l’autorisation et contrôle d’accès pour les utilisateurs, les groupes et les objets d’une organisation. Azure AD offre également aux développeurs un moyen efficace d’intégrer la gestion des identités dans leurs applications. Protocoles standard tels que [SAML 2.0](https://en.wikipedia.org/wiki/SAML_2.0), [La fédération Web](https://msdn.microsoft.com/library/bb498017.aspx)et [Se connecter OpenID](http://openid.net/connect/) rend la connexion sur un grand nombre de plateformes telles que .NET, Java, Node.js et PHP.

L’API Graph basée sur REST permet aux développeurs de lire et écrire dans l’annuaire à partir de n’importe quelle plate-forme. Prise en charge pour [OAuth 2.0](http://oauth.net/2/), les développeurs peuvent créer mobile et sécurisent les applications web qui s’intègrent à Microsoft et web tiers API et créer leurs propres API web. Bibliothèques d’ouvrir la source du client sont disponibles pour .net, du Windows Store, iOS et Android avec les bibliothèques supplémentaires en cours de développement.

### <a name="how-azure-enables-identity-and-access-management"></a>Comment Azure Active la gestion des identités et des accès

Azure AD peut être utilisé sous forme d’un répertoire de cloud autonome pour votre organisation ou d’une solution intégrée avec votre domaine local Active Directory existant. Certaines fonctionnalités d’intégration incluent la synchronisation d’annuaires et authentification unique (SSO). Ces étendent la portée de vos identités locales existant dans le nuage et améliorent l’expérience d’administration et de l’utilisateur final.

Certaines autres fonctions pour la gestion des identités et des accès sont les suivantes :

-   Azure AD permet [l’authentification unique](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) pour les applications SaaS, quelle que soit l’endroit où ils sont hébergés. Certaines applications fédérées avec Azure AD et d’autres personnes utilisent votre mot de passe l’authentification unique. Applications fédérées pouvant prennent également en charge la configuration des utilisateurs et stockage de mot de passe.

-   Accès aux données dans le [Stockage Azure](https://azure.microsoft.com/services/storage/) est contrôlée via l’authentification. Chaque compte de stockage comporte une clé primaire ([Clé de compte de stockage](https://msdn.microsoft.com/library/azure/ee460785.aspx)ou SAK) et la clé secrète secondaire (partagés accès Signature ou associations de sécurité).

-   Azure AD fournit identité en tant que Service via la fédération (en utilisant [Active Directory Federation Services](../active-directory/fundamentals-identity.md), la synchronisation et la réplication avec les répertoires locaux.

-   [Authentification multifacteur Azure Multifacteur](../multi-factor-authentication/multi-factor-authentication.md) est le service d’authentification multifacteur nécessite que les utilisateurs Vérifiez également les connexions à l’aide d’une application mobile, un appel téléphonique ou un message texte. Il est utilisé avec Azure AD, pour sécuriser les ressources locales avec le serveur de l’authentification Multifacteur Azure et les applications personnalisées et des répertoires à l’aide du Kit de développement.

-   [Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/) vous permet de joindre Azure machines virtuelles à un domaine sans avoir à déployer superflus. Les utilisateurs peuvent se connecter à ces machines virtuelles à l’aide de leurs informations d’identification Active Directory d’entreprise et administration des machines virtuelles à un domaine à l’aide de la stratégie de groupe pour appliquer des bases de sécurité sur tous vos ordinateurs virtuels Azure.

-   [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) fournit un service de gestion des identités globale, très disponible pour les applications pour les consommateurs qui s’adapte à des centaines de millions d’identités. Il peut être intégré au sein de mobile et web plateformes. Vos consommateurs peuvent vous connecter à toutes vos applications par le biais des expériences personnalisables à l’aide de leurs comptes mise en réseau existants ou en créant des nouvelles informations d’identification.

##<a name="data-access-control-and-encryption"></a>Contrôle d’accès de données et de chiffrement

Microsoft utilise les principes de séparation des droits et [Moindre privilège](https://en.wikipedia.org/wiki/Principle_of_least_privilege) dans l’ensemble des opérations Azure. Accès aux données par le personnel de support Azure requiert une autorisation explicite et est accordé sur une base « juste-à-temps » qui est connectée et audit, puis révoqué après l’exécution de l’engagement.

En outre, Azure doté de nombreuses fonctionnalités de protection des données dans voies et au repos, y compris le chiffrement des données, fichiers, applications, services, communications et lecteurs cachés. Vous avez la possibilité de chiffrer les informations avant de placer dans Azure, ainsi que le stockage de clés dans vos centres de données locale.

![Microsoft contre les logiciels malveillants dans Azure](./media/azure-security-getting-started/sec-azgsfig1.PNG)

### <a name="azure-encryption-technologies"></a>Technologies de chiffrement Azure

Vous pouvez obtenir des détails sur l’accès administratif à votre environnement d’abonnement à l’aide [des rapports AD Azure](../active-directory/active-directory-reporting-audit-events.md). Vous avez la possibilité de configurer [Lecteur BitLocker](https://technet.microsoft.com/library/cc732774.aspx) sur disques durs virtuels contenant des informations sensibles dans Azure.

Autres fonctionnalités dans Azure qui vous aidera à maintenir la sécurité de vos données :

-   Les développeurs peuvent créer chiffrement dans les applications qu'ils déploient dans Azure à l’aide de Windows [CryptoAPI](https://msdn.microsoft.com/library/ms867086.aspx) et .NET Framework.

- Chiffrement de côté client pour le stockage blob Microsoft permettent de contrôler complètement les touches.  Le service de stockage voit les touches jamais et ne peut pas déchiffrer les données.

-   [Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) (avec le [Kit de développement logiciel RMS](https://msdn.microsoft.com/library/dn758244.aspx) fournit de fichiers et le chiffrement de niveau de données et prévention de perte de données grâce à la gestion d’accès basé sur la stratégie.

-   Azure prend en charge [le chiffrement au niveau des tables et au niveau des colonnes (TDE/effacer)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database.aspx) dans SQL Server virtuelle Machines, et tiers prend en charge en local des serveurs de gestion de clés de centres de données clients.

-   Stockage compte, partagés accès Signatures, certificats de gestion et d’autres touches sont uniques pour chaque client Azure.

-   Stockage de hybride [StorSimple](http://www.microsoft.com/server-cloud/products/storsimple/overview.aspx) Azure chiffre les données via une 128 bits publique / privée paire de clés avant de le télécharger vers le stockage Azure.

-   Azure prend en charge et utilise de nombreux mécanismes de chiffrement, y compris SSL/TLS et IPsec AES, selon les types de données et conteneurs et les transports.

##<a name="virtualization"></a>Virtualisation

La plateforme Azure utilise un environnement virtualisé. Instances utilisateur fonctionnent comme des machines virtuelles autonome qui n’ont pas accès à un serveur hôte physique et cet isolement est appliqué au moyen de physique [niveaux de privilège (simultanée-0/simultanée-3) processeur](https://en.wikipedia.org/wiki/Protection_ring).

Anneau 0 est plus privilégié et 3 le plus petit. La système d’exploitation invité s’exécute dans un 1 simultanée moindre privilège et applications dans la dernière privilégié simultanée 3. Cette virtualisation des ressources physiques permet d’accéder à une séparation claire entre système d’exploitation invité et hyperviseur, ce qui crée de séparation de sécurité supplémentaire entre les deux.

Hyperviseur de Azure se comporte comme un noyau micro et passe toutes les demandes d’accès matériel d’invitées à l’hôte de traitement à l’aide d’une interface de mémoire partagée appelée VMBus. Cela empêche les utilisateurs d’obtenir l’accès en lecture/écriture/exécution brut pour le système et le risque de partage des ressources système.

![Microsoft contre les logiciels malveillants dans Azure](./media/azure-security-getting-started/sec-azgsfig2.PNG)

### <a name="how-azure-implements-virtualization"></a>Comment Azure met en œuvre la virtualisation

Azure utilise un pare-feu hyperviseur (filtre de paquets), qui est activée dans l’hyperviseur et configuré par un agent contrôleur tissu. Cela permet de protéger les clients contre tout accès non autorisé. Par défaut, lorsqu’une machine virtuelle est créée, tout le trafic est bloqué, puis l’agent contrôleur TISSU configure le filtre de paquets pour ajouter des *règles et des exceptions* pour autoriser le trafic autorisé.

Il existe deux catégories de règles qui sont programmés ici :

-   **Configuration de l’ordinateur ou Infrastructure règles**: par défaut, toutes les communications sont bloquée. Il existe des exceptions pour permettre à un ordinateur virtuel envoyer et recevoir le trafic DHCP et DNS. Machines virtuelles peuvent également acheminer le trafic vers l’internet « public » et acheminer le trafic vers d’autres ordinateurs virtuels dans le cluster et le serveur de l’Activation du système d’exploitation. Les ordinateurs virtuels autorisé liste des destinations sortantes n’inclut pas sous-réseaux routeur Azure, principaux gestion Azure et autres propriétés de Microsoft.

-   **Fichier de Configuration de rôle**: définit les utilisateurs entrants en fonction de modèle de service des clients. Par exemple, si un client a un les serveurs Web frontaux au port 80 sur une certaine machine virtuelle, Azure ouvre le port TCP 80 à toutes les adresses IP si vous configurez un point de terminaison dans le modèle de [Gestion des services Azure](../resource-manager-deployment-model.md) . Si la machine virtuelle a un rôle serveur principal ou de travail en cours d’exécution, nous allons ouvrir le rôle de collaborateur uniquement à la machine virtuelle dans le même client.

##<a name="isolation"></a>Isolement

La conservation de séparation pour empêcher le transfert non autorisé et accidentel des informations entre les déploiements dans une architecture de cliente multiples partagée est une autre exigence de sécurité cloud importantes.

Met en œuvre Azure [contrôle d’accès réseau](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/) et répartition par le biais d’isolement réseau local virtuel, utilisateurs, chargent programmes d’équilibrage et les filtres IP. Externe de trafic entrant à vos ordinateurs virtuels est limité à la rubrique ports et protocoles vous définissez. Filtrage réseau est activée pour empêcher le trafic usurpé et limite le trafic entrant et sortant aux composants de la plateforme approuvé. Stratégies de flux de trafic sont implémentées sur les périphériques de protection limite qui refuser le trafic par défaut.

![Microsoft contre les logiciels malveillants dans Azure](./media/azure-security-getting-started/sec-azgsfig3.PNG)

Traduction d’adresses réseau (NAT) est utilisé pour séparer le trafic réseau interne du trafic externe. Le trafic interne n’est pas pouvant être routé avec l’extérieur. [Adresses IP virtuelles](http://blogs.msdn.com/b/cloud_solution_architect/archive/2014/11/08/vips-dips-and-pips-in-microsoft-azure.aspx) qui se trouvent en externe prenant sont converties en adresses [IP dynamique interne](http://blogs.msdn.com/b/cloud_solution_architect/archive/2014/11/08/vips-dips-and-pips-in-microsoft-azure.aspx) qui se trouvent uniquement pouvant être routés dans Azure.

Le trafic externe vers des machines virtuelles Azure est protégée par un pare-feu via accès aux listes de contrôle () sur les routeurs, équilibrage de charge et commutateurs Layer 3. Uniquement les protocoles connus spécifiques sont autorisées. Utilisateurs sont en place pour limiter le trafic en provenance invitées à d’autres réseaux virtuels utilisés pour la gestion. En outre, le trafic filtré via filtres IP sur l’hôte du système d’exploitation, limiter le trafic sur les deux couches de lien et de réseau de données.

### <a name="how-azure-implements-isolation"></a>Comment Azure met en œuvre isolement

Contrôleur de tissu Azure est responsable de l’affectation de ressources de l’infrastructure pour les charges de travail du client et gère les communications unidirectionnelles à partir de l’hôte de machines virtuelles. L’hyperviseur Azure force la mémoire et processus séparation entre machines virtuelles et en toute sécurité route le trafic réseau vers clients de système d’exploitation invité. Azure également met en œuvre isolement pour les clients, stockage et réseaux virtuels :

-   Chaque client Azure AD est logiquement isolé à l’aide des limites de sécurité.

-   Comptes de stockage Azure sont propres à chaque abonnement, et access doit être authentifié à l’aide d’une clé de compte de stockage.

-   Réseaux virtuels soient isolées logiquement grâce à une combinaison des adresses IP privées uniques, les pare-feu et les utilisateurs IP. Charger les programmes d’équilibrage acheminer le trafic vers les clients appropriés en fonction des définitions de point de terminaison.

##<a name="virtual-network-and-firewall"></a>Virtuel réseau et pare-feu

Les [réseaux virtuels et distribuées](http://download.microsoft.com/download/4/3/9/43902EC9-410E-4875-8800-0788BE146A3D/Windows%20Azure%20Network%20Security%20Whitepaper%20-%20FINAL.docx) dans Azure vous assurer que le trafic de votre réseau privé est isolé logiquement du trafic sur d’autres réseaux virtuel Azure.

![Microsoft contre les logiciels malveillants dans Azure](./media/azure-security-getting-started/sec-azgsfig4.PNG)

Votre abonnement peut contenir plusieurs réseaux privés isolées (et inclure les pare-feu, l’équilibrage de charge et traduction des adresses réseau).

Azure fournit trois niveaux principales de répartition dans chaque cluster Azure pour logiquement répartir le trafic réseau. [Réseaux locaux virtuels](https://azure.microsoft.com/services/virtual-network/) (Réseaux virtuels) sont utilisées pour séparer le trafic du client du reste du réseau Azure. Accès au réseau Azure à partir de l’extérieur du cluster est limité à des programmes d’équilibrage de charge.

Le trafic réseau vers et depuis machines virtuelles doit passer par le commutateur virtuel hyperviseur. Le composant de filtre IP dans le système d’exploitation racine isole la racine machine virtuelle à partir des invitées et les invitées à partir d’un autre. Elle effectue un filtrage du trafic limiter la communication entre les nœuds du client et l’Internet public (basée sur la configuration du service du client), les séparer à partir d’autres clients.

Le filtre IP empêche invitées à partir de :

- Générer un trafic usurpé

- Recevoir du trafic ne pas adressé

- Diriger le trafic aux points de terminaison infrastructure protégée

- Envoyer ou recevoir le trafic de diffusion inapproprié

Vous pouvez placer vos machines virtuelles sur [Réseaux virtuels Azure](https://azure.microsoft.com/documentation/services/virtual-network/). Ces réseaux virtuels est similaires aux réseaux vous configurez dans les environnements locaux, où ils sont généralement associées à un commutateur virtuel. Machines virtuelles connectés au réseau virtuel Azure même puissent communiquer avec eux sans configuration supplémentaire. Vous avez également la possibilité pour configurer les différents sous-réseaux au sein de votre réseau virtuel Azure.

Vous pouvez utiliser les technologies de réseau virtuel Azure suivantes afin de sécuriser les communications sur votre réseau virtuel Azure :

-   [**Groupes de sécurité réseau (NSG)**](../virtual-network/virtual-networks-nsg.md). Vous pouvez utiliser un NSG pour contrôler le trafic à une ou plusieurs instances de machine virtuelle () dans votre réseau virtuel. Un NSG contient des règles de contrôle d’accès autoriser ou refuser le trafic en fonction de la direction du trafic, protocole, adresse source et de port et adresse de destination et port.

-   [**Routage définis par l’utilisateur**](../virtual-network/virtual-networks-udr-overview.md). Vous pouvez contrôler le routage de paquets via une application virtuelle en créant des chemins définis par l’utilisateur qui spécifient le saut suivant pour les paquets se déplaçant vers un sous-réseau spécifique pour accéder un votre solution de sécurité réseau virtuel.

-   [**Transfert IP**](../virtual-network/virtual-networks-udr-overview.md). Une solution de sécurité réseau virtuel doit être en mesure de recevoir le trafic entrant qui n’est pas adressé à lui-même. Pour permettre à un ordinateur virtuel recevoir le trafic adressé vers d’autres destinations, vous activez le routage IP pour la machine virtuelle.

-   [**Forcé tunnel**](../vpn-gateway/vpn-gateway-about-forced-tunneling.md). Permet de tunnel forcé rediriger ou « forcer » l’ensemble du trafic Internet liées aux générés par vos machines virtuelles dans un virtuel Azure en retour vers votre emplacement en local via un tunnel VPN site à pour inspection et d’audit

-   [Utilisateurs de **point de terminaison** ](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md). Vous pouvez contrôler quels ordinateurs autorisés les connexions entrantes à partir d’Internet à une machine virtuelle sur votre réseau virtuel Azure en définissant des utilisateurs de point de terminaison.

-   [**Solutions de sécurité réseau partenaire**](https://azure.microsoft.com/marketplace/). Il existe un certain nombre de solution de sécurité réseau partenaire que vous pouvez accéder à partir de la Azure Marketplace.

### <a name="how-azure-implements-virtual-networks-and-firewall"></a>Comment Azure met en œuvre le pare-feu et des réseaux virtuels

Azure met en œuvre pare-feu filtrage de paquets sur tous les hôtes et invités machines virtuelles par défaut. Images de système d’exploitation Windows à partir de la galerie Azure disposez également de pare-feu Windows est activé par défaut. Programmes d’équilibrage de charge au niveau du périmètre public qui fait face à réseaux contrôler les communications d’Azure basé sur les utilisateurs IP gérés par les administrateurs de clients.

Si Azure déplace les données d’un client dans le cadre d’opérations normales ou durant un incident, il le fait sur des canaux des communications privées chiffré. Autres fonctionnalités exploitées par Azure à utiliser dans le pare-feu et des réseaux virtuels sont :

-   **Pare-feu hôte native**: tissu Azure et stockage s’exécutent sur un système d’exploitation natif qui n’a aucune hyperviseur et par conséquent, le pare-feu windows est configuré avec les deux ensembles de règles ci-dessus. Stockage s’exécute natif pour optimiser les performances.

-   **Pare-feu Host**: le pare-feu hôte consiste à protéger le système d’exploitation hôte qui s’exécute l’hyperviseur. Les règles sont programmés pour autoriser uniquement le contrôleur de tissu et accéder zones à parler à l’hôte du système d’exploitation sur un port spécifique. Les autres exceptions sont pour autoriser la réponse DHCP et réponses DNS. Règles de pare-feu pour le système d’exploitation hôte Azure utilise une configuration de l’ordinateur qu’elle a le modèle de fichier. L’hôte lui-même est protégé par un pare-feu Windows configuré pour autoriser uniquement les communications à partir de sources connus, authentifiés contre les attaques externes.

-   **Pare-feu invité**: réplique les règles dans le filtre de paquets commutateur machine virtuelle mais programmés autre logiciel (c'est-à-dire l’élément pare-feu Windows de l’invité du système d’exploitation). Le pare-feu de machine virtuelle invité peut être configuré pour limiter les communications vers ou à partir de l’invité machine virtuelle, même si la communication est autorisée par la configuration de l’hôte de filtre IP. Par exemple, vous pouvez choisir d’utiliser le pare-feu de machine virtuelle invité pour limiter la communication entre deux de vos VNets qui ont été configurés pour vous connecter à un autre.

-   **Stockage pare-feu (FW)**: le pare-feu sur le stockage frontal filtre le trafic uniquement sur les ports 80/443 et autres utilitaire nécessaire. Le pare-feu sur le stockage principale restreint communications venir uniquement à partir de serveurs frontaux de stockage.

-   **Passerelle réseau virtuel**: [Passerelles réseau virtuel Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) servir de la croix locaux passerelles connectez vos charges de travail dans le réseau virtuel Azure à vos sites locaux activé. Il est nécessaire pour se connecter à sur les sites locaux via [tunnel VPN de site à IPsec](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)ou circuits [ExpressRoute](../expressroute/expressroute-introduction.md) . Tunnel VPN IPsec/IKE, les passerelles effectuer des négociations IKE et établissement le tunnel IPsec S2S VPN entre les réseaux virtuels et sur les sites locaux. Passerelles réseau virtuel résilier également [point-à-site VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md).

##<a name="secure-remote-access"></a>Sécuriser l’accès à distance

Données stockées dans le cloud peut comporter des garanties suffisantes activés pour empêcher les attaques et de mettre à jour de la confidentialité et l’intégrité lors dans voies. Cela inclut les contrôles réseau qui lient à l’aide basée sur des règles et contrôlable identités et des accès mécanismes de gestion d’une organisation.

Technologie de chiffrement intégrée permet de chiffrer les communications au sein et entre les déploiements entre Azure régions et à partir d’Azure aux centres de données locale. Droits d’administrateur aux machines virtuelles via [sessions Bureau à distance](../virtual-machines/virtual-machines-windows-classic-connect-logon.md), [Distant Windows PowerShell](http://blogs.technet.com/b/heyscriptingguy/archive/2013/09/07/weekend-scripter-remoting-the-cloud-with-windows-azure-and-powershell.aspx)et le portail de gestion Azure est toujours chiffré.

Pour étendre en toute sécurité votre centre de données locales dans le cloud, Azure fournit à la fois [VPN de site à](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md) et [point-à-site VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md), ainsi que des liens dédiés avec [ExpressRoute](../expressroute/expressroute-introduction.md) (connexions à des réseaux virtuels Azure sur réseau privé virtuel sont chiffrées).

### <a name="how-azure-implements-secure-remote-access"></a>Comment Azure offre un accès à distance sécurisé

Connexions au portail Azure doivent toujours être authentifiées et nécessitent SSL/TLS. Vous pouvez configurer les certificats de gestion de pour activer la gestion sécurisée. Protocoles sécurisés standard tels que [SSTP](https://technet.microsoft.com/magazine/2007.06.cableguy.aspx) et [IPsec](https://en.wikipedia.org/wiki/IPsec) sont entièrement prises en charge.

[Azure ExpressRoute](../expressroute/expressroute-introduction.md) vous permet de créer des connexions entre Azure centres de données et l’infrastructure dans vos locaux ou dans un environnement de co-création emplacement. ExpressRoute connexions ne passent pas sur l’Internet public. Ils offrent davantage fiabilité, de vitesses, de latence inférieur et de sécurité plus élevée que liens classiques basées sur Internet. Dans certains cas, à l’aide de connexions ExpressRoute pour transférer des données entre en local et Azure peut également produire avantages inédite.

##<a name="logging-and-monitoring"></a>Journalisation et la surveillance

Azure fournit authentifiée journalisation des événements liées à la sécurité qui génèrent un journal d’audit et est conçu pour être résistant à la falsification. Cela inclut les informations système, telles que les journaux d’événements sécurité dans Azure infrastructure machines virtuelles et Azure AD. Surveillance des événements de sécurité inclut la collecte des événements tels que les modifications dans DHCP ou DNS server adresses IP, tentative d’accès à des ports et protocoles ou adresses IP bloquées par la conception, dans les paramètres de stratégie ou un pare-feu de sécurité, la création de compte ou un groupe, processus inattendus ou l’installation du pilote.

![Microsoft contre les logiciels malveillants dans Azure](./media/azure-security-getting-started/sec-azgsfig5.PNG)

Des journaux d’audit l’enregistrement de l’accès des utilisateurs dotés de privilèges et activités, tentatives d’accès autorisés et non autorisés, système des exceptions et les événements de sécurité sont conservées pendant un laps de temps. La conservation de vos journaux est à votre convenance, car vous configurez journal collecte et rétention à vos besoins.

### <a name="how-azure-implements-logging-and-monitoring"></a>Comment Azure met en œuvre journalisation et la surveillance

Azure déploie les agents Management Agents (MA) et Azure sécurité moniteur automatisée sur chaque cluster, de stockage ou nœud tissu sous gestion qu’ils soient native ou virtuel. Chaque Agent de gestion est configuré pour s’authentifier à un compte de stockage de service d’équipe avec un certificat obtenu à partir du magasin de certificats Azure et transférer préconfigurée diagnostic et les données d’événement pour le compte de stockage. Ces agents ne sont pas déployées aux machines virtuelles clients.

Les administrateurs Azure accéder aux journaux via un portail web pour un accès contrôlé et authentifié dans les journaux. Un administrateur peut analyser, filtrer, corrélation et analyser les journaux. Les comptes de stockage Azure service d’équipe pour les journaux sont protégés à partir de droits d’administrateur direct afin d’éviter contre la falsification journal.

Microsoft collecte des journaux à partir d’appareils réseau à l’aide du protocole journal système et de serveurs hôte à l’aide de Microsoft Audit Collection Services (ACS). Ces fichiers journaux est placées dans une base de données du journal à partir duquel les alertes sont générées pour les événements suspects directement à un administrateur de Microsoft. L’administrateur peut accéder et analyser ces fichiers journaux.

[Diagnostics Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx) est une fonctionnalité de Azure qui vous permet de collecter les données de diagnostic d’une application en cours d’exécution dans Azure. Il s’agit de données de diagnostic pour débogage et résolution des problèmes, mesurer les performances, utilisation des ressources, l’analyse du trafic et planification de la capacité de surveillance et d’audit. Une fois les données de diagnostic recueillies, il peut être transféré à un compte de stockage Azure de fichier. Transferts peuvent être planifiées ou à la demande.

##<a name="threat-mitigation"></a>Atténuation des menaces

Outre isolement, du chiffrement et le filtrage, Azure utilise un certain nombre de mécanismes d’atténuation menace pour protéger des services d’infrastructure et processus. Ils incluent des contrôles internes et technologies permettant de détecter et de correction des menaces avancés tels que les [10 premiers OWASP avoir](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)DDoS et élévation des privilèges.

Les contrôles de sécurité et les processus de gestion des risques que Microsoft a mis en place pour sécuriser son infrastructure cloud attaques des incidents de sécurité. Mais, en cas d’incident, l’équipe de sécurité Incident Management (Assistant) de l’équipe Microsoft Online Services de sécurité et conformité (OSSC) est prêt 24 x 7 pour répondre.

### <a name="how-azure-implements-threat-mitigation"></a>Comment Azure met en œuvre atténuation des menaces

Azure a sécurité contrôles en place pour implémenter l’atténuation des menaces ainsi que pour aider les clients atténuer les menaces potentielles dans leur environnement. La liste ci-dessous récapitule les fonctionnalités d’atténuation menace offertes par Azure :

-   [Azure logiciels malveillants](../security/azure-security-antimalware.md) est activée par défaut sur tous les serveurs d’infrastructure. Vous pouvez éventuellement activer au sein de votre propre machines virtuelles.

-   Microsoft gère continue de surveillance des serveurs, réseaux et applications de détecter les menaces et empêche actions. Alertes automatiques informent les administrateurs des comportements anormales, leur permettant de prendre des mesures verres menaces internes et externes.

-   Vous avez la possibilité de déployer des solutions de sécurité 3e partie au sein de vos abonnements, telles que pare-feu, application web du [Barracuda](https://techlib.barracuda.com/ng54/deployonazure).

-   Tests de pénétration de l’approche de Microsoft inclut « d'[Agrégation rouge](http://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)», qui comprend les professionnels de la sécurité Microsoft attaquer les systèmes de production en direct (non client) dans Azure pour tester la protection contre les menaces permanentes réelles et avancées.

-   Systèmes de déploiement intégré de gérer la distribution et l’installation de correctifs de sécurité au sein de la plateforme Azure.

##<a name="next-steps"></a>Étapes suivantes

[Centre de gestion de la confidentialité d’Azure](https://azure.microsoft.com/support/trust-center/)

[Blog de l’équipe sécurité Azure](http://blogs.msdn.com/b/azuresecurity/)

[Centre de sécurité Microsoft](https://technet.microsoft.com/library/dn440717.aspx)

[Blog Active Directory](http://blogs.technet.com/b/ad/)
