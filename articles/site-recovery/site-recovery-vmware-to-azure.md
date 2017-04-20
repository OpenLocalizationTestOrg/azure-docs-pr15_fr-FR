<properties
    pageTitle="Répliquer machines virtuelles VMware et des serveurs physiques vers Azure avec récupération de Site Azure dans le portail Azure | Microsoft Azure"
    description="Décrit comment déployer récupération de Site Azure pour organiser réplication, le basculement et restauration des machines virtuelles de VMware en local et des serveurs physiques Linux/Windows Azure à l’aide du portail Azure"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="raynew"/>

# <a name="replicate-vmware-virtual-machines-and-physical-machines-to-azure-with-azure-site-recovery-using-the-azure-portal"></a>Répliquer machines virtuelles VMware et machines physiques sur Azure avec Azure récupération de Site à l’aide du portail Azure

> [AZURE.SELECTOR]
- [Portail Azure](site-recovery-vmware-to-azure.md)
- [Azure classique](site-recovery-vmware-to-azure-classic.md)
- [Azure classique (hérité)](site-recovery-vmware-to-azure-classic-legacy.md)

Bienvenue dans récupération de Site Azure ! Utilisez cet article si vous souhaitez répliquer locaux des machines virtuelles VMware Windows/serveurs ou Linux physiques vers Azure à l’aide de la récupération de Site Azure dans le portail Azure.

> [AZURE.NOTE] Azure comporte deux différents [modèles de déploiement](../resource-manager-deployment-model.md) pour la création et utilisation des ressources : Azure Resource Manager (ARM) et classique. Azure est également équipé de deux portails – le portail classique Azure qui prend en charge le modèle de déploiement classique et le portail Azure avec prise en charge pour les deux modèles de déploiement.

Récupération de site dans le portail Azure fournit un certain nombre de nouvelles fonctionnalités :

- Les services Azure sauvegarde et restauration de Site Azure sont combinées dans un archivage sécurisé aux Services de récupération unique afin que vous pouvez configurer et gérer continuité des activités et récupération d’urgence (BCDR) à partir d’un emplacement unique. Dans le tableau de bord unifiée, vous pouvez surveiller et gérer les opérations sur vos sites en local et le nuage public Azure.
- Opérations de récupération de Site dans le portail Azure peuvent maintenant gérer les utilisateurs disposant d’un abonnement Azure mis en service avec le programme Cloud Solution fournisseur (fournisseur).
- Récupération de site dans le portail Azure pouvez répliquer machines aux comptes de stockage processeur. Lors du basculement, récupération de Site crée machines virtuelles processeur ARM dans Azure.
- Récupération de site continue à prendre en charge les comptes de stockage classique de la réplication. Lors du basculement, récupération de Site crée des machines virtuelles à l’aide du modèle classique.

Après lecture cet article publier des commentaires dans la partie inférieure du Disqus des commentaires. Poser des questions techniques sur le [Forum de Services de récupération Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Vue d’ensemble

Les organisations doivent une stratégie BCDR qui détermine la façon applications, les charges de travail et les données restent en cours d’exécution et disponible au cours des interruptions liées aux tâches planifiées et et récupérer à des conditions de travail normal dès que possible. Votre stratégie BCDR doit conserver les données métiers approuvés et peuvent être restaurées et vous assurer que les charges de travail restent disponibles en permanence cas d’urgence.

Récupération de site est un service Azure qui contribue à votre stratégie de BCDR en organiser réplication des serveurs physiques en local et machines virtuelles dans le cloud (Azure) ou un centre de données secondaire. Lorsque des défaillances se trouvent dans votre emplacement principal, vous basculer vers l’emplacement secondaire afin que les applications et les charges de travail disponibles. Vous ne parvenez pas à votre emplacement principal lorsqu’elle retourne à la normale. Pour plus d’informations [Nouveautés récupération de Site Azure ?](site-recovery-overview.md)

Cet article fournit toutes les informations que vous devez répliquer locaux machines virtuelles VMware et les serveurs physiques Linux/Windows Azure. Il inclut une présentation de l’architecture, planification des informations et des étapes de déploiement de configuration Azure, sur les serveurs locaux, les paramètres de réplication et planification de la capacité. Une fois que vous avez configuré l’infrastructure que vous pouvez activer la réplication sur les ordinateurs que vous voulez protéger et vérifier que le basculement fonctionne.

## <a name="business-advantages"></a>Avantages de l’entreprise

- Récupération de site offre une protection hors site pour les charges de travail métier et applications s’exécutant sur VMware machines virtuelles et des serveurs physiques.
- Le portail de Services de récupération fournit un emplacement unique pour configurer, gérer et surveiller réplication, le basculement et récupération.
- Récupération de site de découvrir automatiquement machines virtuelles VMware ajouté à vSphere hosts.
- Vous pouvez facilement exécuter basculement à partir de votre infrastructure en local pour Azure et de restauration (restauration) à partir d’Azure aux serveurs VMware VM dans votre site local.
- Vous pouvez activer la mémoire virtuelle à plusieurs et créer des groupes de réplication afin que les charges de travail application hiérarchisé sur plusieurs ordinateurs répliquer en même temps. Tous les ordinateurs dans un groupe de réplication disposent des points de récupération de blocage cohérentes et cohérente à l’échelle de l’application en cas d’échec sur. Pour le basculement, vous pouvez regrouper plusieurs ordinateurs dans les plans de récupération afin que les charges de travail hiérarchisé d’applications basculent entre eux.

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

### <a name="windows64-bit-only"></a>Windows (64 bits uniquement)
- Windows Server 2008 R2 SP1 +
- Windows Server 2012
- Windows Server 2012 R2

### <a name="linux-64-bit-only"></a>Linux (64 bits uniquement)
- Chapeau rouge Enterprise Linux 6,7, 7.1, 7.2
- CentOS 6.5, 6.6, 6,7, 7.0, 7.1, 7.2
- Oracle Enterprise Linux 6.4, 6.5 exécutant le noyau compatible chapeau rouge ou un Unbreakable entreprise noyau version 3 (UEK3)
- SUSE Linux Enterprise Server 11 SP3

## <a name="scenario-architecture"></a>Architecture de scénario

Voici les composants du scénario :

- **Serveur de configuration**: un ordinateur local qui coordonnées de communication et gère les processus de réplication et la restauration des données. Sur cet ordinateur, vous exécutez un fichier d’installation unique pour installer le serveur de configuration et de ces composants supplémentaires :
    - **Serveur de traitement**: fonctionne comme une passerelle de réplication. Il reçoit les données de réplication à partir d’ordinateurs source protégée, optimise avec la mise en cache, de compression et chiffrement et envoie au stockage Azure. Il gère installation push du service mobilité à machines protégées et vous effectue la détection automatique des machines virtuelles VMware. Le serveur de traitement par défaut est installé sur le serveur de configuration. Vous pouvez déployer des serveurs de processus autonomes supplémentaires pour redimensionner votre déploiement.
    - **Serveur cible maître**: gère les données de la réplication lors du retour arrière à partir d’Azure.

- **Service mobilité**: ce composant est déployé sur chaque ordinateur (VMware VM ou serveur physique) que vous souhaitez répliquer vers Azure. Il capture écritures de données sur l’ordinateur et les transmet au serveur de processus.
- **Azure**: vous n’avez pas besoin créer les ordinateurs virtuels Azure pour gérer la réplication et le basculement vers Azure.  Vous avez besoin d’un abonnement Azure, un compte de stockage Azure pour stocker les données dupliquées et un réseau virtuel Azure afin que les ordinateurs virtuels Azure sont connectés à un réseau après le basculement. Le compte de stockage et le réseau doivent être placé dans la même région en tant que l’archivage sécurisé aux Services de récupération.
- **Retour arrière**: lorsque vous êtes prêt à revenir à partir d’Azure échouer à votre site local après un basculement, vous devez créer une machine virtuelle Azure comme un serveur de traitement temporaire. Vous pouvez le supprimer une fois la restauration terminée. Retour arrière, vous devez également une connexion de réseau privé virtuel (ou Azure ExpressRoute) entre votre site local et le réseau Azure dans lequel se trouvent vos ordinateurs virtuels Azure. Si le trafic retour arrière est élevé, vous devrez également définir un masque de dédié serveur cible ordinateur local. Le serveur cible maître par défaut en cours d’exécution sur le serveur de configuration peut servir pour le trafic soit plus clair.


Le graphique montre comment ces composants interagissent.

![architecture](./media/site-recovery-vmware-to-azure/v2a-architecture-henry.png)

**Figure 1 : VMware/physique vers Azure**

## <a name="azure-prerequisites"></a>Conditions préalables Azure

Voici ce que vous devez dans Azure déployer ce scénario.

**Conditions préalables** | **Plus d’informations**
--- | ---
**Compte Azure**| Vous avez besoin d’un compte [Microsoft Azure](http://azure.microsoft.com/) . Vous pouvez commencer avec une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur les tarifs de récupération de Site.
**Stockage Azure** | Données répliquées sont stockées dans le stockage Azure et Azure machines virtuelles sont créées lors du basculement. <br/><br/>Pour stocker des données vous aurez besoin d’un compte de stockage standard ou premium dans la même région en tant que l’archivage sécurisé aux Services de récupération.<br/><br/>Vous pouvez utiliser un compte de stockage LRS ou GRS. Nous vous recommandons de GRS afin que les données sont résistants si une panne régionale se produit ou si la région primaire ne peuvent pas être récupérée. [En savoir plus](../storage/storage-redundancy.md).<br/><br/> [Stockage Premium](../storage/storage-premium-storage.md) est généralement utilisée pour les machines virtuelles ayant besoin d’une manière cohérente à travers haute performance IO et faible latence aux charges de travail intensives hôte IO.<br/><br/> Si vous souhaitez utiliser un compte premium pour stocker les données dupliquées, vous devez également un compte de stockage standard pour stocker les journaux de réplication capturer les modifications en cours apportées aux données locales.<br/><br/> Notez que les comptes de stockage créés dans le portail Azure ne peut pas être déplacés entre les groupes de ressources. Également une protection aux comptes de stockage premium dans Inde centrale et du Sud Inde est actuellement pas pris en charge.<br/><br/> [En savoir plus sur](../storage/storage-introduction.md) Stockage Azure.
**Réseau Azure** | Vous avez besoin d’un réseau virtuel Azure qui machines virtuelles Azure se connecteront à basculement. Le réseau virtuel Azure doit être placé dans la même région en tant que l’archivage sécurisé aux Services de récupération.
**Retour arrière à partir d’Azure** | Vous devez temporaire server processus configurée en tant qu’une machine virtuelle Azure. Vous pouvez le créer lorsque vous êtes prêt à être restauré et supprimez-le après que fail précédent est terminée.<br/><br/> Pour faire échouer retour vous devez une connexion VPN (ou Azure ExpressRoute) à partir du réseau Azure au site local.

## <a name="configuration-server--scale-out-process-prerequisites"></a>Serveur de configuration / évoluer conditions préalables pour les processus

Vous devez configurer un ordinateur local que le serveur de configuration échelle extraction / serveur de traitement.

**Conditions préalables** | **Plus d’informations**
--- | ---
**Serveur de configuration**| Vous devez un physique locale ou une machine virtuelle exécutant Windows Server 2012 R2. Tous les composants de récupération de Site locaux sont installés sur cet ordinateur.<br/><br/>Pour la réplication VMware VM, nous vous recommandons de que vous déployez le serveur comme un VM VMware hautement disponible. Si vous êtes réplication machines physiques l’ordinateur peut être un serveur physique.<br/><br/> Retour arrière vers le site local à partir d’Azure est toujours aux machines virtuelles VMware indépendamment si vous basculé machines virtuelles ou serveurs physiques. Si vous ne déployez le serveur de configuration comme un VMware VM vous devez configurer un serveur cible maître distinct comme un VMware VM pour recevoir le trafic de retour arrière.<br/><br/>Si le serveur est un VMware VM, le type de carte réseau doit être VMXNET3. Si vous utilisez un autre type de carte réseau, vous devez installer une [VMware mettre à jour](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1) sur le serveur vSphere 5.5.<br/><br/>Le serveur doit disposer d’une adresse IP statique.<br/><br/>Le serveur ne doit pas être un contrôleur de domaine.<br/><br/>Le nom d’hôte du serveur doit être 15 caractères ou moins.<br/><br/>Le système d’exploitation doit être en anglais uniquement.<br/><br/> Vous devez installer VMware vSphere PowerCLI 6.0. sur le serveur de configuration.<br/><br/>Le serveur de configuration nécessite un accès internet. Accès sortant est requis comme suit :<br/><br/>Accès temporaire sur HTTP 80 pendant l’installation des composants de récupération de Site (à télécharger MySQL)<br/><br/>Accès sortant en continu sur 443 HTTPS pour la gestion de la réplication<br/><br/>Accès sortant en continu sur 9443 HTTPS pour le trafic de réplication (ce port peut être modifié)<br/><br/>Le serveur devront également accéder aux URL suivantes afin qu’il puisse se connecter à Azure : *. hypervrecoverymanager.windowsazure.com ; *. AccessControl.Windows.NET ; *. backup.windowsazure.com ; *. BLOB.Core.Windows.NET ; *. store.core.windows.net<br/><br/>Si vous avez des règles de pare-feu basé sur l’adresse IP sur le serveur, vérifiez que les règles permettent communications vers Azure. Vous devez autoriser les [Plages d’adresses IP Azure centre de données](https://www.microsoft.com/download/confirmation.aspx?id=41653) et le protocole HTTPS (443).<br/><br/>Autoriser les plages d’adresses IP pour la région Azure de votre abonnement et pour les États-Unis Ouest.<br/><br/>Autoriser cette URL pour le téléchargement MySQL :.http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi


## <a name="vmware-vcentervsphere-host-prerequisites"></a>Conditions préalables vCenter/vSphere hôte VMware

**Conditions préalables** | **Plus d’informations**
--- | ---
**vSphere**| Vous avez besoin d’un ou plusieurs les hyperviseurs VMware vSphere.<br/><br/>Hyperviseurs doivent s’exécuter vSphere version 6.0, 5.5 ou 5.1 avec les dernières mises à jour.<br/><br/>Nous vous recommandons que votre vSphere hosts et les serveurs vCenter sont situés dans le même réseau que le serveur de processus (il s’agit du réseau qui se trouve le serveur de configuration, sauf si vous avez configuré un serveur dédié).
**vCenter** | Nous vous recommandons de déployer un serveur de vCenter VMware pour gérer vos hôtes vSphere. Il doit s’exécuter vCenter version 6.0 ou 5.5 avec les dernières mises à jour.<br/><br/>Notez que récupération de Site ne prend pas en charge les nouveaux vCenter et fonctionnalités vSphere 6.0 croisées telles que vMotion vCenter, volumes virtuels et stockage DRS. Prise en charge de la récupération de site est limitée aux fonctionnalités qui étaient également disponibles dans la version 5.5.


## <a name="protected-machine-prerequisites"></a>Ordinateur protégé conditions préalables

**Conditions préalables** | **Plus d’informations**
--- | ---
**Locaux (machines virtuelles VMware)** | Machines virtuelles VMware à protéger doit avoir installé et exécuté les outils VMware.<br/><br/> Machines à protéger doivent être conformes avec [Azure conditions préalables](site-recovery-best-practices.md#azure-virtual-machine-requirements) pour la création de machines virtuelles Azure.<br/><br/>Capacité disque individuel sur machines protégées ne doivent pas être plus de 1023 go. Une machine virtuelle peut avoir jusqu'à 64 disques (et donc jusqu'à 64 To). <br/><br/>Minimal 2 Go d’espace disponible sur le lecteur d’installation pour l’installation des composants.<br/><br/>Protection des machines virtuelles avec disques chiffrés n’est pas pris en charge.<br/><br/>Partagé invité disque clusters ne sont pas pris en charge.<br/><br/>**Port 20004** doit être ouvert sur pare-feu local de la machine virtuelle protégée, si vous souhaitez activer **la cohérence des applications**.<br/><br/>Ordinateurs sur lesquels Extensible microprogramme UEFI (Unified Interface) / démarrage Extensible Interface(EFI) microprogramme n’est pas pris en charge.<br/><br/>Noms de machine doivent contenir entre 1 et 63 caractères (lettres, chiffres et des traits d’union). Le nom doit commencer par une lettre ou un chiffre et se terminent par une lettre ou un chiffre. Après avoir activé la réplication pour un ordinateur, vous pouvez modifier le nom Azure.<br/><br/>Si la source machine virtuelle a cartes, il est converti en une seule carte réseau après le basculement vers Azure.<br/><br/>Si protégées machines virtuelles ont un disque iSCSI récupération de Site convertit le disque iSCSI machine virtuelle protégé dans un fichier de disque dur virtuel lorsque la machine virtuelle bascule vers Azure. Si la cible iSCSI peut être atteintes par la machine virtuelle Azure il s’y connecter et essentiellement voir deux disques – le disque dur sur l’ordinateur virtuel Azure et le disque iSCSI source. Dans ce cas, vous devez déconnecter la cible iSCSI qui s’affiche sur l’ordinateur virtuel Azure.
**Machines Windows (physique ou VMware)** | L’ordinateur doit être exécutant un système d’exploitation 64 bits pris en charge : Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2 avec au moins SP1.<br/><br/> Le système d’exploitation doit être installé sur le lecteur C:\. Le disque du système d’exploitation doit être un disque de base Windows et non dynamique. Le disque de données peut être dynamique.<br/><br/>Récupération de site prend en charge les machines virtuelles avec un disque RDM. Lors du retour arrière, récupération de Site réutilise le disque RDM si le disque de machine virtuelle et RDM source d’origine est disponible. Si elles ne sont pas disponibles, lors du retour arrière récupération de Site crée un nouveau fichier VMDK pour chaque disque.
**Ordinateurs Linux** (phyical ou VMware)|  Vous avez besoin d’un système d’exploitation 64 bits pris en charge : Red chapeau Enterprise Linux 6.7,7.1,7.2 ; CentOS 6.5, 6.6,6.7,7.0,7.1,7.2 ; Oracle Enterprise Linux 6.4, 6.5 en cours d’exécution Unbreakable entreprise noyau version 3 (UEK3), SUSE Linux Enterprise Server 11 SP3 ou noyau compatible chapeau rouge.<br/><br/>fichiers/etc/hosts sur machines protégées doivent contenir des entrées correspondant au nom d’hôte local aux adresses IP associées à toutes les cartes réseau.<br/><br/>Si vous voulez vous connecter à une machine virtuelle Azure exécutant Linux après le basculement à l’aide d’un Secure Shell client (ssh), vérifiez que le service banque d’informations sécurisé Shell sur l’ordinateur protégé est défini pour démarrer automatiquement au démarrage du système, et que les règles de pare-feu autorisent un ssh connexion à ce dernier.<br/><br/>Nom d’hôte, les points de montage, noms de périphériques et chemins d’accès du système Linux et les noms de fichier (par exemple/etc / ; /usr) doivent être en anglais uniquement.<br/><br/>Protection ne peut être activée pour les ordinateurs Linux avec le stockage suivant : NFS (EXT3, ETX4, ReiserFS, XFS) ; Chemins multiples-périphérique logiciel Mapper (chemins multiples)) ; Gestionnaire de volume : (LVM2). Serveurs physiques avec stockage du contrôleur HP CCISS ne sont pas pris en charge. Le système de fichiers ReiserFS est pris en charge uniquement sur SUSE Linux Enterprise Server 11 SP3.<br/><br/>Récupération de site prend en charge les machines virtuelles avec un disque RDM.  Lors du retour arrière pour Linux, récupération de Site ne réutiliser le disque RDM. À la place qu’il crée un nouveau fichier VMDK pour chaque disque RDM correspondante.<br/><br/>Assurez-vous que vous définissez le paramètre disk.enableUUID=true dans les paramètres de configuration de la machine virtuelle dans VMware. Créez l’entrée s’il n’existe pas. Il est nécessaire de fournir un UUID cohérent au VMDK pour qu’il charge correctement. Ajout de ce paramètre garantit également que les modifications delta uniquement sont transférées en local au cours de restauration et pas une réplication complète.
**Service de mobilité** |  **Windows**: vous devez pour envoyer automatiquement le service mobilité aux machines virtuelles exécutant Windows, vous devez fournir un compte d’administrateur (administrateur local sur l’ordinateur Windows) afin que le serveur de traitement peut effectuer une installation push.<br/><br/>**Linux**: pour diffuser automatiquement le service mobilité aux machines virtuelles exécutant Linux, vous devez créer un compte qui peut être utilisé par le serveur de traitement à effectuer une installation push.<br/><br/> Par défaut, tous les disques sur un ordinateur sont répliquées. Pour [exclure un disque de la réplication](#exclude-disks-from-replication), la mobilité service doit être installé manuellement sur l’ordinateur avant d’activer la réplication.<br/>

## <a name="prepare-for-deployment"></a>Préparer pour le déploiement

Pour vous préparer pour le déploiement, vous devez :

1. [Configurer un réseau Azure](#set-up-an-azure-network) dans lequel machines virtuelles Azure seront situés lorsqu’ils êtes lancés après le basculement. En outre, de la restauration vous devez configurer une connexion VPN (ou Azure ExpressRoute) à partir du réseau Azure à votre site local.
2. [Configurer un compte de stockage Azure](#set-up-an-azure-storage-account) pour les données dupliquées.
3. [Préparer un compte](#prepare-an-account-for-automatic-discovery) sur le serveur de vCenter ou vSphere héberge afin que récupération de Site peut détecter automatiquement les machines virtuelles VMware qui sont ajoutés.
4. [Préparer le serveur de configuration](#prepare-the-configuration-server) pour s’assurer qu’il peut accéder aux URL obligatoires et installez vSphere PowerCLI 6.0.


### <a name="set-up-an-azure-network"></a>Configurer un réseau Azure

- Le réseau doit être dans la même région Azure que celle dans laquelle vous allez déployer l’archivage sécurisé aux Services de récupération.
- Selon le modèle de ressources que vous souhaitez utiliser pour basculé machines virtuelles Azure, vous devez configurer le réseau Azure en [mode processeur](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) ou [en mode classique](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
- Pour basculer revenir à partir d’Azure à votre site VMware en local, vous devez une connexion VPN (ou une connexion Azure ExpressRoute) à partir du réseau Azure dans lequel les ordinateurs virtuels Azure répliquée se trouvent, au réseau local dans lequel se trouve le serveur de configuration.
- [Découvrez](../vpn-gateway/vpn-gateway-site-to-site-create.md) le déploiement pris en charge modèles pour les connexions de site à VPN et explique comment [configurer une connexion](../vpn-gateway/vpn-gateway-site-to-site-create.md#create-your-virtual-network).
- Vous pouvez également configurer [Azure ExpressRoute](../expressroute/expressroute-introduction.md). [En savoir plus](../expressroute/expressroute-howto-vnet-portal-classic.md) sur la configuration d’un réseau Azure ExpressRoute.

> [AZURE.NOTE] [Migration des réseaux](../resource-group-move-resources.md) au sein de groupes de ressources dans le même abonnement ou abonnements n’est pas prise en charge pour les réseaux utilisés pour le déploiement de récupération de Site.

### <a name="set-up-an-azure-storage-account"></a>Configurer un compte de stockage Azure

- Vous devez standard ou un compte de stockage Azure premium pour conserver les données répliquées vers Azure. Le compte doit être placé dans la même région en tant que l’archivage sécurisé aux Services de récupération. Selon le modèle de ressources que vous souhaitez utiliser pour basculé machines virtuelles Azure, vous devez configurer un compte en [mode processeur](../storage/storage-create-storage-account.md) ou [en mode classique](../storage/storage-create-storage-account-classic-portal.md).
- Si vous utilisez un compte premium pour les données répliquées que nécessaires pour créer un compte supplémentaire standard pour stocker les journaux de réplication capturer les modifications en cours apportées aux données locales.  

> [AZURE.NOTE] [Migration des comptes de stockage](../resource-group-move-resources.md) au sein de groupes de ressources dans le même abonnement ou abonnements n’est pas prise en charge pour les comptes de stockage utilisés pour le déploiement de récupération de Site.

### <a name="prepare-an-account-for-automatic-discovery"></a>Préparer un compte pour la découverte automatique

Le serveur de traitement de récupération de Site de découvrir automatiquement machines virtuelles VMware vSphere hôtes ou sur un serveur vCenter qui gère hosts. Pour effectuer automatique découverte des informations d’identification de récupération de Site qui peut accéder au serveur VMware. Ce n’est pas pertinent si vous êtes la réplication machines physiques uniquement.

1. Pour utiliser un compte dédié pour la découverte automatique créer un rôle (par exemple Azure_Site_Recovery) au niveau du vCenter avec les [autorisations requises](#vmware-account-permissions).
2. Créer un nouvel utilisateur sur le serveur hôte ou vCenter vSphere et attribuer le rôle à l’utilisateur. Plus tard vous allez informer récupération de Site ces informations d’identification afin qu’il puisse effectuer la découverte automatique.

    >[AZURE.NOTE] Un compte d’utilisateur vCenter avec un rôle en lecture seule peut être exécutées basculement mais Impossible d’arrêter machines d’origine protégée. Si vous voulez arrêter ces ordinateurs, vous devez le rôle [Azure_Site_Recovery](#vmware-account-permissions) . Si vous migrez uniquement machines virtuelles VMware vers Azure et que vous n’êtes pas obligé de retour arrière, le rôle en lecture seule est suffisant.

### <a name="prepare-the-configuration-server"></a>Préparer le serveur de configuration

1.  Assurez-vous que l’ordinateur que vous utilisez pour le serveur de configuration est conforme à la [Configuration requise](#configuration-server-prerequisites). En particulier, vérifiez que l’ordinateur est connecté à internet avec ces paramètres :

    - Autoriser l’accès à ces URL : *. hypervrecoverymanager.windowsazure.com ; *. AccessControl.Windows.NET ; *. backup.windowsazure.com ; *. BLOB.Core.Windows.NET ; *. store.core.windows.net
    - Autoriser l’accès à [http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi](http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi) télécharger MySQL.
    - Autoriser la communication de pare-feu pour Azure avec les [plages d’adresses IP Azure centre de données](https://www.microsoft.com/download/confirmation.aspx?id=41653) et le protocole HTTPS (443).

2.  Téléchargez et installez [VMware vSphere PowerCLI 6.0](https://developercenter.vmware.com/tool/vsphere_powercli/6.0) sur le serveur de configuration. (Actuellement d’autres versions de PowerCLI ne sont pas pris en charge, y compris les versions R de version 6.0.)


## <a name="create-a-recovery-services-vault"></a>Créer un archivage sécurisé aux Services de récupération

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Nouveau** > **gestion** > **sauvegarde et restauration de Site (OMS)**. Vous pouvez également cliquer sur **Parcourir** > **L’archivage sécurisé des Services de récupération** > **Ajouter**.

    ![Archivage sécurisé nouveau](./media/site-recovery-vmware-to-azure/new-vault3.png)

3. Dans la zone **nom** , spécifiez un nom convivial pour identifier l’archivage sécurisé. Si vous avez plusieurs abonnements, sélectionnez un d’eux.
4. [Créer un nouveau groupe de ressources](../resource-group-template-deploy-portal.md) ou sélectionnez-en un. Spécifier une région Azure. Machines seront répliquées à cette zone. Notez que les réseaux utilisés pour la récupération de Site et stockage Azure devront être dans la même région. Pour rester informé des régions pris en charge Voir disponibilité géographique dans [Les détails du prix Azure Site récupération](https://azure.microsoft.com/pricing/details/site-recovery/)
4. Si vous souhaitez accéder rapidement à l’archivage sécurisé du tableau de bord cliquez sur **Ajouter au tableau de bord** , puis sur **créer**.

    ![Archivage sécurisé nouveau](./media/site-recovery-vmware-to-azure/new-vault-settings.png)

L’archivage sécurisé nouveau s’affichent dans le **tableau de bord** > **toutes les ressources**et sur le principaux **Services de récupération de chambres fortes** carte.

## <a name="getting-started"></a>Prise en main

Récupération de site fournit une expérience de mise en route conçu pour vous aider à vous et en cours d’exécution aussi rapidement que possible. Il vérifie les conditions préalables et vous guide dans les étapes que requises pour utiliser la récupération de Site déployé.

Vous pouvez de sélectionner le type d’ordinateurs que vous souhaitez répliquer et l’endroit où vous souhaitez répliquer à. Vous configurez l’infrastructure, y compris les serveurs en local, paramètres Azure, des stratégies de réplication et planification de la capacité. Une fois l’infrastructure en place vous permettent de dupliquer pour machines virtuelles et des serveurs physiques. Vous pouvez ensuite exécuter basculement pour les ordinateurs spécifiques, ou créer des plans de récupération à basculer sur plusieurs ordinateurs.

Commencer mise en route en choisissant la manière dont vous souhaitez déployer récupération de Site. Le flux de mise en route change légèrement selon vos besoins en matière de réplication.


## <a name="step-1-choose-your-protection-goals"></a>Étape 1 : Choisir vos objectifs de protection

Sélectionnez ce que vous voulez répliquer et l’endroit où vous souhaitez répliquer à.

1. Dans la carte de **Services de récupération de chambres fortes** Sélectionnez votre l’archivage sécurisé et cliquez sur **paramètres**.
2. Dans **paramètres** > **Prise en main** cliquez sur **Récupération de Site** > **étape 1 : préparer une Infrastructure** > **objectif de Protection**.

    ![Sélectionnez des objectifs](./media/site-recovery-vmware-to-azure/choose-goals.png)

3. Dans **l’objectif de Protection** sélectionnez **Sur Azure**, puis sélectionnez **Oui, avec hyperviseur VMware vSphere**. Cliquez ensuite sur **OK**.

    ![Sélectionnez des objectifs](./media/site-recovery-vmware-to-azure/choose-goals2.png)


## <a name="step-2-set-up-the-source-environment"></a>Étape 2 : Configurer l’environnement source

Configurer le serveur de configuration et l’enregistrer dans l’archivage sécurisé aux Services de récupération. Si vous êtes réplication machines virtuelles VMware spécifier le compte VMware que vous utilisez pour la découverte automatique.

1. Cliquez sur **étape 1 : préparer Infrastructure** > **Source**. Dans **préparer source** si vous n’avez pas un serveur de configuration cliquez sur **+ serveur de Configuration** pour ajouter une.

    ![Configurer la source](./media/site-recovery-vmware-to-azure/set-source1.png)

2. Dans la vérification de la carte **Serveur ajouter** ce **Serveur de Configuration** s’affiche dans **serveur type**.
3. Avant de configurer le serveur de configuration vérifier les [conditions préalables](#configuration-server-prerequisites). Dans la vérification particulière que l’ordinateur peut accéder aux URL requis.
4.  Téléchargez le fichier d’installation le programme d’installation de Site récupération unifiée.
5.  Téléchargez la clé d’inscription de l’archivage sécurisé. Vous devez ceci lorsque vous exécutez le programme d’installation unifiée. La clé est valide pour 5 jours après la générer.

    ![Configurer la source](./media/site-recovery-vmware-to-azure/set-source2.png)

6.  Sur l’ordinateur que vous utilisez en tant que le serveur de configuration, exécutez l’installation unifiée pour installer le serveur de configuration, le serveur de traitement et le serveur maître cible.


### <a name="run-site-recovery-unified-setup"></a>Site d’exécution récupération unifiée le programme d’installation

1.  Exécuter le fichier installation unifiée.
2.  Dans **avant de commencer** , sélectionnez **installer le serveur de configuration et le processus serveur**.

    ![Avant de commencer](./media/site-recovery-vmware-to-azure/combined-wiz1.png)

3. Dans la **Licence du logiciel tiers** , cliquez sur **J’accepte** pour télécharger et installer MySQL.

    ![Troisième = logiciels tiers](./media/site-recovery-vmware-to-azure/combined-wiz105.PNG)

4. Dans **l’enregistrement** Parcourir et sélectionnez la clé d’inscription que vous avez téléchargé depuis l’archivage sécurisé.

    ![Enregistrement](./media/site-recovery-vmware-to-azure/combined-wiz3.png)

5. Dans **Les paramètres d’Internet** spécifiez comment le fournisseur en cours d’exécution sur le serveur de configuration se connecteront aux récupération de Site Azure via internet.

    - Si vous voulez vous connecter avec le proxy est configuré sur l’ordinateur sélectionnez **se connecter avec les paramètres de proxy existants**.
    - Si vous souhaitez que le fournisseur de se connecter directement sélectionnez **se connecter directement sans proxy**.
    - Si le proxy existant requiert une authentification, ou vous souhaitez utiliser un proxy personnalisé pour la connexion du fournisseur, sélectionnez **se connecter avec les paramètres de proxy personnalisés**.
        - Si vous utilisez un proxy personnalisé, que vous devez spécifier l’adresse, les ports et les informations d’identification
        - Si vous utilisez un proxy vous avez devez déjà autorisé les URL décrites dans les [conditions préalables](#configuration-server-prerequisites).

    ![Pare-feu](./media/site-recovery-vmware-to-azure/combined-wiz4.png)

6. Dans les **Conditions préalables pour vérifier** le programme d’installation s’exécute une vérification pour vous assurer que l’installation peut s’exécuter. Si un message d’avertissement apparaît à propos de la **synchronisation de temps globales vérifier** Vérifiez que l’heure de l’horloge système (paramètres de**Date et heure** ) est différente de celle dans le fuseau horaire.

    ![Conditions préalables](./media/site-recovery-vmware-to-azure/combined-wiz5.png)

7. Dans la **Configuration de MySQL** créer des informations d’identification pour vous connecter à l’instance du serveur MySQL qui est installé.

    ![MySQL](./media/site-recovery-vmware-to-azure/combined-wiz6.png)

8. Dans Sélectionner **Détails de l’environnement** si vous allez répliquer les machines virtuelles VMware. Si vous êtes, le programme d’installation vérifie que PowerCLI 6.0 est installé.

    ![MySQL](./media/site-recovery-vmware-to-azure/combined-wiz7.png)

9. Dans **L’emplacement d’installation** , sélectionnez l’endroit où vous voulez installer les fichiers binaires et stocker le cache. Vous pouvez sélectionner un lecteur qui comporte au moins 5 Go de stockage disponible, mais nous vous recommandons d’un lecteur de cache au moins 600 Go d’espace libre.

    ![Emplacement d’installation](./media/site-recovery-vmware-to-azure/combined-wiz8.png)

10. Dans **Sélection du réseau** , spécifiez le récepteur (carte réseau et le port SSL) sur lequel le serveur de configuration sera envoyer et recevoir des données de la réplication. Vous pouvez modifier la valeur par défaut le port (9443). En plus de ce port, port 443 sera utilisé par un serveur web orchestrant opérations de réplication. 443 ne doit pas être utilisé pour recevoir le trafic de réplication.


    ![Sélection du réseau](./media/site-recovery-vmware-to-azure/combined-wiz9.png)



11.  En **Résumé** Vérifiez les informations, puis cliquez sur **installer**. Lorsque l’installation se termine à un mot de passe est générée. Vous en aurez besoin lorsque vous activez la réplication donc copiez-le et conserver dans un emplacement sécurisé.

    ![Résumé](./media/site-recovery-vmware-to-azure/combined-wiz10.png)

12.  Une fois l’enregistrement terminé le serveur est affichée dans les **paramètres** > **serveurs** carte dans l’archivage sécurisé.



#### <a name="run-setup-from-the-command-line"></a>Exécutez le programme d’installation à partir de la ligne de commande

Vous pouvez configurer le serveur de configuration à partir de la ligne de commande :

    UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

Paramètres :

- / ServerMode : obligatoire. Indique si la configuration et le processus de serveurs doivent être installées, ou uniquement au serveur de processus. Valeurs d’entrée : SC, PM.
- InstallLocation : obligatoire. Le dossier dans lequel les composants sont installés.
- / MySQLCredsFilePath. Obligatoire. Le chemin d’accès du fichier dans lequel les informations d’identification du serveur MySQL sont stockées. Le fichier doit être au format suivant :
    - [MySQLCredentials]
    - MySQLRootPassword = «<Password>»
    - MySQLUserPassword = «<Password>»
- / VaultCredsFilePath. Obligatoire. L’emplacement du fichier d’informations d’identification de l’archivage sécurisé
- / EnvType. Obligatoire. Le type d’installation. Valeurs : VMware, NonVMware
- / PSIP et /CSIP. Obligatoire. L’adresse IP du serveur de traitement et du serveur de configuration.
- / PassphraseFilePath. Obligatoire. L’emplacement du fichier de mot de passe.
- / BypassProxy. Facultatif. Spécifie que le serveur de configuration se connecte à Azure sans proxy.
- / ProxySettingsFilePath. Facultatif. Paramètres de proxy (le proxy par défaut requiert une authentification, ou un proxy personnalisé). Le fichier doit être au format suivant :
    - [ProxySettings]
    - ProxyAuthentication = « Oui/non »
    - Proxy IP = « adresse IP > »
    - ProxyPort = «<Port>»
    - ProxyUserName = «<User Name>»
    - ProxyPassword = «<Password>»
- DataTransferSecurePort. Facultatif. Numéro de port à utiliser pour les données de réplication.
- SkipSpaceCheck. Facultatif. Ignorer la vérification de l’espace pour le cache.
- AcceptThirdpartyEULA. Obligatoire. Indicateur implique l’acceptation de tiers CLUF.
- ShowThirdpartyEULA. Obligatoire. Affiche CLUF tiers. Si fournies comme entrée tous les autres paramètres sont ignorés.

### <a name="add-the-vmware-account-used-for-automatic-discovery"></a>Ajoutez le compte VMware utilisé pour la découverte automatique

 Lorsque vous avez préparé pour le déploiement, vous devez avoir [créé un compte VMware](#prepare-an-account-for-automatic-discovery) et récupération de Site peuvent utiliser pour la découverte automatique. Ajoutez ce compte comme suit :

1. Ouvrez **CSPSConfigtool.exe**. Il est disponible en tant qu’un raccourci sur le bureau et situé dans le dossier \home\svsystems\bin [emplacement d’installation].
2. Cliquez sur **Gérer les comptes** > **Ajouter un compte**.

    ![Ajouter un compte](./media/site-recovery-vmware-to-azure/credentials1.png)

3. Dans **Détails du compte** , ajoutez le compte qui sera utilisé pour la découverte automatique. Notez qu’il peut prendre 15 minutes ou davantage pour le nom du compte doit apparaître dans le portail. Pour mettre à jour immédiatement, cliquez sur **Les serveurs de Configuration** > nom du serveur > **Actualiser le serveur**.

    ![Plus d’informations](./media/site-recovery-vmware-to-azure/credentials2.png)

### <a name="connect-to-vsphere-hosts-and-vcenter-servers"></a>Se connecter à vSphere hôtes et les serveurs vCenter

Si vous êtes réplication machines virtuelles VMware se connecter aux serveurs vCenter et vSphere hosts.

1. Vérifiez que le serveur de configuration a accès réseau aux hôtes vSphere et aux serveurs vCenter.
2. Cliquez sur **préparer infrastructure** > **Source**. **Préparer source** de sélectionner le serveur de configuration, puis cliquez sur **+ vCenter** pour ajouter un serveur hôte ou vCenter vSphere.
3. Dans **Ajouter vCenter** spécifier un nom convivial pour le serveur hôte ou vCenter vSphere, puis spécifiez l’adresse IP ou le nom de domaine complet du serveur. Conservez le port 443, à moins que vos serveurs VMware sont configurés pour écouter les demandes sur un autre port. Sélectionnez le compte qui sera utilisé pour la connexion au serveur VMware. Cliquez sur **OK**.

    ![VMware](./media/site-recovery-vmware-to-azure/vmware-server.png)

    >[AZURE.NOTE] Si vous voulez ajouter le serveur vCenter ou l’hôte vSphere avec un compte qui ne dispose pas des droits d’administrateur sur le serveur vCenter ou hôte, puis vérifiez que le compte possède ces privilèges activés : centre de données, magasin de données, dossier, hôte, réseau, les ressources, virtuelle d’ordinateur, vSphere commutateur distribué. En outre le serveur vCenter doit le privilège affichages de stockage.

Récupération de site se connecte aux serveurs VMware en utilisant les paramètres spécifiés et découvre des machines virtuelles.

## <a name="step-3-set-up-the-target-environment"></a>Étape 3 : Configurer l’environnement cible

Assurez-vous de qu'avoir un compte de stockage pour la réplication et un réseau Azure auquel machines virtuelles Azure va se connecter après le basculement.

1.  Cliquez sur **préparer infrastructure** > **cible** et sélectionnez l’abonnement Azure que vous voulez utiliser.
2.  Spécifiez le modèle de déploiement que vous voulez utiliser pour les machines virtuelles après le basculement.
3.  Récupération de site vérifie que vous avez un ou plusieurs comptes de stockage Azure compatible et réseaux.

    ![Cible](./media/site-recovery-vmware-to-azure/gs-target.png)

4.  Si vous n’avez pas créé un compte de stockage et que vous voulez créer un à l’aide du processeur cliquez sur **+ compte de stockage** pour effectuer cette inline.  Sur la carte de **créer un compte de stockage** , spécifiez un nom de compte, type, abonnement et emplacement. Le compte doit être dans la même région en tant que l’archivage sécurisé aux Services de récupération.

    ![Espace de stockage](./media/site-recovery-vmware-to-azure/gs-createstorage.png)

    Notez que :

    - Si vous voulez créer un compte de stockage à l’aide du modèle classique vous ferez que dans le portail Azure. [Pour en savoir plus](../storage/storage-create-storage-account-classic-portal.md)
    - Si vous utilisez un compte de stockage premium pour les données dupliquées, à que vous devez configurer un compte espace de stockage supplémentaire standard réplication de magasin ouvre une session qui changent en cours de capture données locales.

    > [AZURE.NOTE] Protection aux comptes de stockage premium dans Inde centrale et du Sud Inde n’est actuellement pas pris en charge.

4.  Sélectionnez un réseau Azure. Si vous n’avez pas créé un réseau et que vous voulez procéder à l’aide du processeur cliquez sur **+ réseau** pour effectuer cette inline. Sur la carte **réseau virtuel créer** spécifiez un nom de réseau, plage d’adresses, détails du sous-réseau, abonnement et un emplacement. Le réseau doit être dans le même emplacement que l’archivage sécurisé aux Services de récupération.

    ![Réseau](./media/site-recovery-vmware-to-azure/gs-createnetwork.png)

    Si vous voulez créer un réseau à l’aide du modèle classique vous ferez que dans le portail Azure. [En savoir plus](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).

## <a name="step-4-set-up-replication-settings"></a>Étape 4 : Configurer les paramètres de réplication

1. Pour créer une nouvelle réplication stratégie, cliquez sur **préparer infrastructure** > **Paramètres de réplication** > **+ créer et associer**.
2. Dans **créer et stratégie associer** spécifiez un nom de la stratégie.
3. Dans le **seuil RPO**: spécifiez la limite RPO. Alertes seront générées lors de la réplication continue dépasse cette limite.
5. Dans la **rétention des points de récupération**, spécifiez heures combien de temps la fenêtre de rétention seront pour chaque point de récupération. Machines protégées peuvent être récupérés à tout moment dans une fenêtre. Jusqu'à rétention 24 heures est pris en charge pour les ordinateurs répliquées au stockage premium.
6. Dans **fréquence instantané cohérente à l’échelle de l’application**, spécifiez la fréquence (en minutes) des points de récupération contenant des instantanés cohérents au niveau de l’application est créées.
7. Lorsque vous créez une stratégie de réplication, par défaut une stratégie correspondante est automatiquement créée pour retour arrière. Pour exemple, si la stratégie de réplication est **stratégie de représentant** la stratégie de restauration seront **représentant-stratégie-retour arrière**. Cette stratégie n’est pas utilisée jusqu'à ce que vous lancez un retour arrière.  
8. Cliquez sur **OK** pour créer la stratégie.

    ![Stratégie de réplication](./media/site-recovery-vmware-to-azure/gs-replication2.png)

9. Lorsque vous créez une nouvelle stratégie il associée automatiquement le serveur de configuration. Cliquez sur **OK**.

    ![Stratégie de réplication](./media/site-recovery-vmware-to-azure/gs-replication3.png)


## <a name="step-5-capacity-planning"></a>Étape 5 : Planification de la capacité

Maintenant que vous avez votre basic infrastructure configurer vous pouvez envisager de planification de la capacité et déterminer si vous avez besoin des ressources supplémentaires.

Récupération de site fournit un planificateur de capacité pour vous aider à allouer les ressources de votre environnement source, composants de récupération de site, mise en réseau et de stockage. Vous pouvez exécuter le planificateur en mode rapide pour estimations selon un nombre moyen de machines virtuelles, des disques et stockage ou en mode détaillé dans laquelle vous allez saisir les chiffres au niveau de la charge de travail. Avant de commencer, vous devez à :

- Collecter les informations relatives à votre environnement de réplication, y compris les machines virtuelles, disques par machines virtuelles et stockage par disque.
- Estimer le taux de modification (évolution du) quotidien qu'avoir pour les données répliquées. Vous pouvez utiliser l' [application de planification de la capacité vSphere](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance) pour vous aider à procédez comme suit.

1.  Cliquez sur **Télécharger** pour télécharger l’outil, puis exécutez-le. [Lisez l’article intitulé](site-recovery-capacity-planner.md) qui accompagne l’outil.
2.  Lorsque vous avez terminé sélectionnez **Oui** **vous avez terminé la planification des capacités ?**

    ![Planification de la capacité](./media/site-recovery-vmware-to-azure/gs-capacity-planning.png)

Le tableau suivant illustre un nombre de points pour vous aider à planifier la capacité de ce scénario.


**Composant** | **Plus d’informations**
--- | --- | ---
**Réplication** | **Tous les jours au maximum modifier taux**: un ordinateur protégé peut utiliser uniquement un serveur de traitement et un serveur de processus unique peut gérer une quotidienne modifier évaluer jusqu'à 2 To. 2 To est donc que les données quotidiennes maximales modifier taux est prise en charge pour un ordinateur protégé.<br/><br/> **Débit maximum**— une machine répliquée peut appartenir à un seul compte de stockage dans Azure. Un compte de stockage standard peut gérer un maximum de 20 000 requêtes par seconde, et nous vous conseillons de conserver le nombre de sorties par sur un ordinateur source à 20 000. Pour exemple si vous avez une machine source avec 5 disques et chaque disque génère sorties 120 par (taille 8 Ko) sur la source il sera au sein de la Azure par limite de sorties par disque de 500. Le nombre de comptes de stockage requis = source total sorties par/20000.
**Serveur de configuration** | Le serveur de configuration doit être en mesure de gérer la capacité de taux de modification quotidien sur toutes les charges de travail en cours d’exécution sur les ordinateurs protégés et a besoin de bande passante suffisante pour réplication continue des données vers le stockage Azure.<br/><br/> Meilleurs résultats, nous vous recommandons que le serveur de configuration se trouver sur le même réseau et le segment LAN que les ordinateurs à protéger. Il peut se trouver sur un autre réseau mais machines à protéger doivent avoir visibilité de réseau niveau 3.<br/><br/> Recommandations pour la taille du serveur de configuration sont répertoriées dans le tableau ci-dessous.
**Serveur de traitement** | Le premier serveur de processus est installé par défaut sur le serveur de configuration. Vous pouvez déployer des serveurs de traitement supplémentaires pour redimensionner votre environnement. Notez que :<br/><br/> Le serveur de traitement reçoit les données de la réplication de machines protégées et optimise avec la mise en cache, de compression et chiffrement avant d’envoyer à Azure. L’ordinateur serveur processus doit avoir des ressources suffisantes pour effectuer ces tâches.<br/><br/> Le serveur de processus utilise cache disque. Nous vous recommandons d’un disque cache distinct de 600 Go ou plus traiter les modifications apportées aux données stockées dans le cas de critique réseau ou une panne.

### <a name="size-recommendations-for-the-configuration-server"></a>Recommandations pour la taille du serveur de configuration

**PROCESSEUR** | **Mémoire** | **Taille du cache de disque** | **Taux de modification de données** | **Machines protégées**
--- | --- | --- | --- | ---
8 vCPUs (2 sockets * 4 cœurs @ 2,5 GHz) | 16 GO | 300 GO | 500 Go ou moins | Répliquer moins de 100 ordinateurs.
12 vCPUs (2 sockets * 6 cœurs @ 2,5 GHz) | 18 GO | 600 GO | 500 Go à 1 to | Répliquer entre machines 100-150.
16 vCPUs (2 sockets * 8 cœurs @ 2,5 GHz) | 32 GO | 1 TO | 1 To à 2 To | Répliquer entre machines 150-200.
Déployer un autre serveur de processus | | | > 2 To | Déployer des serveurs de traitement supplémentaires si vous êtes la réplication de plus de 200 ordinateurs, ou si les données quotidiennes changent taux dépasse 2 To.

Dans cet exemple :

- Chaque ordinateur source est configuré avec 3 disques de 100 Go.
- Nous avons utilisé le stockage évaluation à 8 lecteurs associations de sécurité de 10 K t/min avec RAID 10 les mesures de disque du cache.

### <a name="size-recommendations-for-the-process-server"></a>Recommandations pour la taille du serveur de processus

Si vous avez besoin de protéger les plus de 200 ordinateurs ou taux de modification quotidien est supérieure à 2 To, vous pouvez ajouter des serveurs de traitement supplémentaires pour gérer la charge de réplication. Pour évoluer vous pouvez :

- Augmenter le nombre de serveurs de configuration. Par exemple, vous pouvez protéger jusqu'à 400 machines avec deux serveurs de configuration.
- Ajout de serveurs supplémentaires processus et les utiliser pour gérer le trafic à la place (ou en plus de) du serveur de configuration.

Le tableau suivant décrit un scénario dans lequel :

- Que vous voulez pas utiliser le serveur de configuration comme un serveur de traitement.
- Vous avez configuré un serveur de processus supplémentaires.
- Vous avez configurer protégées machines virtuelles à utiliser le serveur de traitement supplémentaires.
- Chaque ordinateur protégé source est configuré avec trois disques de 100 Go.

**Serveur de configuration** | **Serveur de traitement supplémentaires**| **Taille du cache de disque** | **Taux de modification de données** | **Machines protégées**
--- | --- | --- | --- | ---
8 vCPUs (2 sockets * 4 cœurs @ 2,5 GHz), 16 Go de mémoire | 4 vCPUs (2 sockets * 2 cœurs @ 2,5 GHz), 8 Go de mémoire | 300 GO | 250 Go ou moins | Répliquer machines 85 ou inférieur.
8 vCPUs (2 sockets * 4 cœurs @ 2,5 GHz), 16 Go de mémoire | 8 vCPUs (2 sockets * 4 cœurs @ 2,5 GHz), 12 Go de mémoire | 600 GO | 250 Go à 1 to | Répliquer entre machines 85-150.
12 vCPUs (2 sockets * 6 cœurs @ 2,5 GHz), 18 Go de mémoire | 12 vCPUs (2 sockets * 6 cœurs @ 2,5 GHz) 24 Go de mémoire | 1 TO | 1 To à 2 To | Répliquer entre 150-225 machines.


Dans celle dans laquelle vous adapter vos serveurs sera dépendent de vos préférences pour une échelle de configuration ou évoluer modèle.  Vous évoluez en déployant quelques configuration haut de gamme et les serveurs de traitement ou évoluer en déployant plus de serveurs avec moins de ressources. Par exemple : Si vous avez besoin protéger des 220 machines vous pourriez effectuez une des opérations suivantes :

- Configurer le serveur de configuration avec 12vCPU, 18 Go de mémoire, un serveur de processus supplémentaires avec 12vCPU, 24 Go de mémoire et configurer des ordinateurs protégées pour utiliser uniquement le serveur de processus supplémentaires.
- Vous pouvez également vous pouvez configurer deux serveurs de configuration (8vCPU x 2, 16 Go de RAM) et deux serveurs de traitement supplémentaires (1 x 8vCPU) et 4vCPU x 1 pour gérer 135 + 85 machines (220) et configurer des ordinateurs protégées pour utiliser les serveurs de traitement supplémentaires uniquement.

[Suivez ces instructions](#deploy-additional-process-servers) pour configurer un serveur de processus supplémentaires.

### <a name="network-bandwidth-considerations"></a>Considérations relatives à la bande passante réseau

Vous pouvez utiliser l’outil de planificateur de capacité pour calculer la bande passante que vous avez besoin de la réplication (réplication initiale, puis delta). Pour contrôler la quantité d’utilisation de la bande passante de la réplication plusieurs options s’offrent à vous :

- **Limiter la bande passante**: le trafic VMware qui réplique vers Azure accède à un serveur de processus spécifique. Vous pouvez limiter la bande passante sur les ordinateurs qui exécutent en tant que serveurs de traitement.
- **Influencer la bande passante**: vous pouvez influencer la bande passante utilisée pour la réplication à l’aide de quelques clés de Registre :
    - La valeur de Registre **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\UploadThreadsPerVM** Spécifie le nombre de threads qui sont utilisés pour le transfert de données (réplication initiale ou delta) d’un disque. Une valeur plus élevée augmente la bande passante réseau utilisée pour la réplication.
    - La **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DownloadThreadsPerVM** Spécifie le nombre de threads utilisés pour le transfert de données lors du retour arrière.

#### <a name="throttle-bandwidth"></a>Limiter la bande passante

1. Ouvrez le composant logiciel enfichable Microsoft Azure sauvegarde MMC sur l’ordinateur agissant en tant que le serveur de traitement. Par défaut, un raccourci pour Microsoft Azure Backup est disponible sur le bureau ou dans C:\Program Files\Microsoft Azure récupération Services Agent\bin\wabadmin.
2. Dans le composant logiciel enfichable, cliquez sur **Modifier les propriétés**.

    ![Limiter la bande passante](./media/site-recovery-vmware-to-azure/throttle1.png)

3. Sous l’onglet **régulation** sélectionnez **Activer l’utilisation de la bande passante internet la limitation pour les opérations de sauvegarde**et définir les limites de travail et non liés au travail heures. Les plages valides sont compris 512 Kb/s et 102 Mo/s par seconde.

    ![Limiter la bande passante](./media/site-recovery-vmware-to-azure/throttle2.png)

Vous pouvez également utiliser l’applet de commande [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) pour définir la limitation. Voici un exemple :

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Jeu-OBMachineSetting NoThrottle** indique qu’aucune limitation n’est requise.


#### <a name="influence-network-bandwidth"></a>Influencer la bande passante réseau

1. Dans le Registre atteindre **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
    - Pour influencer le trafic de bande passante sur un disque réplication, modifiez la valeur de **UploadThreadsPerVM**, ou créez la clé s’il n’existe pas.
    - Pour influencer la bande passante pour le trafic de retour arrière à partir d’Azure, modifiez la valeur **DownloadThreadsPerVM**.
2. La valeur par défaut est 4. Dans un réseau « overprovisioned », ces clés de Registre par les valeurs par défaut doivent être modifiées. La valeur maximale est 32. Surveiller le trafic afin d’optimiser la valeur.

## <a name="step-6-replicate-applications"></a>Étape 6 : Réplication applications

Assurez-vous que machines que vous voulez répliquer préparés pour l’installation du service mobilité et puis activer la réplication.

### <a name="install-the-mobility-service"></a>Installer le service mobilité

La première étape de l’activation de la protection de machines virtuelles et des serveurs physiques consiste à installer le service mobilité. Vous pouvez faire de deux façons :

- **Push de serveur de processus**: lorsque vous activez la réplication sur un ordinateur, transmission et installez le composant de service mobilité du serveur de traitement. Notez que l’installation push ne se produire si machines sont déjà en cours d’exécution une version à distance-date de fin du composant.
- **Push entreprise**: automatiquement installer le composant à l’aide de votre processus de push enterprise comme WSUS ou Gestionnaire de Configuration de System Center ou [Automation Azure et configuration de l’état souhaité](./site-recovery-automate-mobility-service-install.md). Configurer le serveur de configuration avant de le faire.
- **Installation manuelle**: installer le composant manuellement sur chaque ordinateur sur lequel vous souhaitez répliquer. Configurer le serveur de configuration avant de le faire.


#### <a name="prepare-for-automatic-push-on-windows-machines"></a>Préparer pour la distribution automatique sur les ordinateurs Windows

Voici comment procéder pour préparer des ordinateurs Windows afin que le service mobilité peut être installé automatiquement par le serveur de traitement.

1.  Créez un compte qui peut être utilisé par le serveur de traitement pour accéder à l’ordinateur. Le compte doit disposer de droits d’administrateur (local ou domaine), et qu’il est utilisé uniquement pour l’installation push.

    >[AZURE.NOTE] Si vous n’utilisez pas un compte de domaine, vous devez désactiver le contrôle d’accès utilisateur à distance sur l’ordinateur local. Pour ce faire, dans le Registre sous HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System ajoutez l’entrée DWORD LocalAccountTokenFilterPolicy avec une valeur de 1. Pour ajouter l’entrée de Registre à partir d’un type d’infrastructure du langage commun **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.

2.  Sur le pare-feu Windows de l’ordinateur sur lequel vous voulez protéger, sélectionnez **Autoriser une application ou une fonctionnalité via le pare-feu**. Activer le **partage de fichiers et imprimante** et **Windows Management Instrumentation**. Pour les ordinateurs qui appartiennent à un domaine, vous pouvez configurer les paramètres du pare-feu avec un objet de stratégie de groupe.

    ![Paramètres du pare-feu](./media/site-recovery-vmware-to-azure/mobility1.png)

2. Ajoutez le compte que vous avez créé :

    - Ouvrez **cspsconfigtool**. Il est disponible en tant qu’un raccourci sur le bureau et situé dans le dossier \home\svsystems\bin [emplacement d’installation].
    - Dans l’onglet **Gérer les comptes** , cliquez sur **Ajouter un compte**.
    - Ajoutez le compte que vous avez créé. Après avoir ajouté le compte, vous devez fournir les informations d’identification lorsque vous activez la réplication pour une machine.


#### <a name="prepare-for-automatic-push-on-linux-servers"></a>Préparer pour la distribution automatique sur des serveurs Linux

1.  Assurez-vous que l’ordinateur Linux à protéger est pris en charge comme décrit dans les [conditions préalables machine protégée](#protected-machine-prerequisites). Vérifiez qu’il existe connectivité entre la machine Linux et le serveur de traitement du réseau.

2.  Créez un compte qui peut être utilisé par le serveur de traitement pour accéder à l’ordinateur. Le compte doit être un utilisateur racine sur le serveur Linux source et qu’il est utilisé uniquement pour l’installation push.

    - Ouvrez **cspsconfigtool**. Il est disponible en tant qu’un raccourci sur le bureau et situé dans le dossier \home\svsystems\bin [emplacement d’installation].
    - Dans l’onglet **Gérer les comptes** , cliquez sur **Ajouter un compte**.
    - Ajoutez le compte que vous avez créé. Après avoir ajouté le compte, vous devez fournir les informations d’identification lorsque vous activez la réplication pour une machine.

3.  Vérifiez que le fichier/etc/hosts sur le serveur Linux source contienne des entrées établir une correspondance entre le nom d’hôte local adresses IP associées à toutes les cartes réseau.
4.  Installer la dernière openssh, openssh server, les logiciels d’openssl sur l’ordinateur que vous souhaitez répliquer.
5.  Vérifiez que SSH est activée et en cours d’exécution sur le port 22.
6.  Activez l’authentification SFTP sous-système et mot de passe dans le fichier sshd_config comme suit :

    - Connectez-vous en tant que racine.
    - Dans le fichier /etc/ssh/sshd_config de fichier, recherchez la ligne qui commence par **PasswordAuthentication**.
    - Supprimez les marques de la ligne et changez la valeur **Aucune** valeur **Oui**.
    - Recherchez la ligne qui commence par **sous-système** et supprimez la ligne.

        ![Linux](./media/site-recovery-vmware-to-azure/mobility2.png)


### <a name="install-the-mobility-service-manually"></a>Installer manuellement le Service mobilité

Les programmes d’installation sont disponibles sur le serveur de Configuration dans **C:\Program Files (x86) \Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**.

Système d’exploitation source | Fichier d’installation service mobilité
--- | ---
Windows Server (64 bits uniquement) | Microsoft-ASR_UA_9. *.0.0_Windows_* release.exe
CentOS 6.4, 6.5, 6.6 (64 bits uniquement) | Microsoft-ASR_UA_9. *.0.0_RHEL6-64_*release.tar.gz
SUSE Linux Enterprise Server 11 SP3 (64 bits uniquement) | Microsoft-ASR_UA_9. *.0.0_SLES11-SP3-64_*release.tar.gz
Oracle Enterprise Linux 6.4, 6.5 (64 bits uniquement) | Microsoft-ASR_UA_9. *.0.0_OL6-64_*release.tar.gz


#### <a name="install-mobility-service-on-a-windows-server"></a>Installer le Service mobilité sur Windows Server


1. Téléchargez et exécutez le programme d’installation approprié.
2. Dans **avant de commencer** , sélectionnez **service mobilité**.

    ![Service de mobilité](./media/site-recovery-vmware-to-azure/mobility3.png)

3. Dans **Détails du serveur Configuration** spécifier l’adresse IP du serveur de configuration et le mot de passe qui a été généré lorsque vous avez exécuté le programme d’installation unifiée. Vous pouvez récupérer le mot de passe à en cours d’exécution : ** <SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe – v** sur le serveur de configuration.

    ![Service de mobilité](./media/site-recovery-vmware-to-azure/mobility6.png)

4. Dans **L’emplacement d’installation** , laissez le paramètre par défaut, puis cliquez sur **suivant** pour commencer l’installation.
5. En **Cours d’Installation** surveiller l’installation et redémarrez l’ordinateur si vous y êtes invité. Après avoir installé le service peut prendre environ 15 minutes pour que l’état mettre à jour dans le portail.

#### <a name="install-mobility-service-on-a-windows-server-using-the-command-prompt"></a>Installer le Service mobilité sur Windows server à l’aide de l’invite de commandes

1. Copier le programme d’installation dans un dossier local (par exemple C:\Temp) sur le serveur que vous voulez protéger. Le programme d’installation, vous pouvez trouver sur le serveur de Configuration sous la **\home\svsystems\pushinstallsvc\repository [emplacement d’installation]**. Le package pour les systèmes d’exploitation Windows aura un nom similaire à Microsoft ASR_UA_9.3.0.0_Windows_GA_17thAug2016_release.exe
2. **Renommer** ce fichier MobilitySvcInstaller.exe
3. Exécutez la commande suivante pour extraire le programme d’installation MSI </br>

        C:\> cd C:\Temp
        C:\Temp> MobilitySvcInstaller.exe /q /xC:\Temp\Extracted
        C:\Temp> cd Extracted
        C:\Temp\Extracted> UnifiedAgent.exe /Role "Agent" /CSEndpoint "IP Address of Configuration Server" /PassphraseFilePath <Full path to the passphrase file>

#####<a name="full-command-line-syntax"></a>Syntaxe de la ligne de commande complet

    UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation Directory>] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log File Path>]<br/>

**Paramètres**

- **/Role :** Obligatoire. Indique si le service mobilité doit être installé. Agent de valeurs d’entrée | MasterTarget
- **/InstallLocation :** Obligatoire. Spécifie l’emplacement installer le service.
- **/PassphraseFilePath :** Obligatoire. Le mot de passe du serveur de configuration.
- **/LogFilePath :** Obligatoire. Emplacement dans lequel les fichiers journaux d’installation doivent être créés.



#### <a name="uninstall-mobility-service-manually"></a>Désinstaller manuellement le service de mobilité

Mobilité Service peut être désinstallé à l’aide de l’ajouter supprimer un programme dans le panneau de configuration ou à l’aide de la ligne de commande.

La commande pour désinstaller le Service mobilité à l’aide de la ligne de commande est

    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1}


#### <a name="install-mobility-service-on-a-linux-server-using-command-line"></a>Installer le Service de mobilité sur un serveur Linux à l’aide de la ligne de commande

1. Copiez l’archive tar appropriés en fonction du tableau ci-dessus sur l’ordinateur Linux que vous souhaitez répliquer.
2. Ouvrez un programme shell et extraire l’archive compressé tar dans un chemin d’accès local en exécutant :`tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. Créer un fichier passphrase.txt dans le répertoire local dans lequel vous avez extrait le contenu de l’archive tar. Pour effectuer ceci copiez le mot de passe C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase sur le serveur de configuration et enregistrez-le dans passphrase.txt en exécutant *`echo <passphrase> >passphrase.txt`* dans shell.
4. Installer le service mobilité en exécutant *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Spécifier l’adresse IP interne du serveur de configuration et vérifiez que le port 443 est sélectionné. Après avoir installé le service peut prendre environ 15 minutes pour que l’état mettre à jour dans le portail.

**Vous pouvez également installer à partir de la ligne de commande**:

1. Copier le mot de passe à partir de C:\Program Files (x86) \InMage Systems\private\connection sur le serveur de configuration, puis enregistrez-le en tant que « passphrase.txt » sur le serveur de configuration. Puis exécuter ces commandes. Dans notre exemple, l’adresse IP du serveur configuration est 104.40.75.37 et le port HTTPS doit être 443 :

Pour installer sur un serveur de production :

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

Installer sur le serveur cible maître :


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt


### <a name="enable-replication"></a>Activer la réplication

#### <a name="before-you-start"></a>Avant de commencer

Si vous êtes la réplication virtuel VMware machines Notez les points suivants :

- Machines virtuelles VMware sont découvertes toutes les 15 minutes et peut prendre 15 minutes ou plus de temps pour qu’ils apparaissent dans le portail après la découverte. De même découverte peut prendre 15 minutes ou plus lorsque vous ajoutez un nouvel hôte serveur ou vSphere vCenter.
- Modifications de l’environnement sur l’ordinateur virtuel (par exemple, installation des outils VMware) peuvent également prendre 15 minutes ou plus être mis à jour dans le portail.
- Vous pouvez vérifier la dernière fois découverte pour les machines virtuelles VMware dans le champ **Contact dernière en** pour l’hôte de serveur/vSphere vCenter sur la carte de **Serveurs de Configuration** .
- Pour ajouter des ordinateurs pour la réplication sans attendre la découverte planifiée, mettez en surbrillance le serveur de configuration (ne cliquez pas dessus) et cliquez sur le bouton **Actualiser** .
- Lorsque vous activez la réplication, si l’ordinateur est préparé automatiquement le serveur de processus installe le service de mobilité dessus.

#### <a name="exclude-disks-from-replication"></a>Exclure des disques de la réplication

Lorsque vous activez la réplication, tous les disques sur un ordinateur sont répliquées par défaut. Vous pouvez exclure des disques de la réplication. Par exemple, vous pouvez souhaiter pas répliquer disques avec des données temporaires ou des données a mis à jour chaque fois qu’un ordinateur ou application redémarre (par exemple pagefile.sys ou tempdb de SQL Server). Si vous voulez exclure disques Notez que :

- Vous pouvez uniquement permet d’exclure disques qui ont déjà installé le service mobilité. Vous devez [installer manuellement le service mobilité](#install-the-mobility-service-manually) car le service mobilité est installé uniquement en utilisant le mécanisme push après que la réplication est activée.
- Seuls les disques de base peuvent être exclus de la réplication. Vous ne pouvez pas exclure OS ou disques dynamiques.
- Après que la réplication est activée vous ne pouvez pas ajouter ou supprimer des disques de la réplication. Si vous voulez ajouter ou exclure un disque, vous devez désactiver la protection de l’ordinateur et puis le réactiver.
- Si vous permet d’exclure un disque que nécessaire pour une application à fonctionner, après le basculement vers Azure vous devez créer manuellement dans Azure afin que l’application répliquée peut s’exécuter. Par ailleurs, vous pouvez intégrer automation Azure dans un plan de récupération pour créer le disque pendant le basculement de l’ordinateur.
- Disques que vous créez manuellement dans Azure va échouer précédent. Par exemple si vous échouer trois disques et créez deux directement dans Azure, les cinq être échec précédent. Vous ne pouvez pas exclure les disques créés manuellement à partir de retour arrière.

**Activez maintenant réplication comme suit**:

1. Cliquez sur **étape 2 : répliquer application** > **Source**. Après avoir activé la réplication pour la première fois que vous cliquerez sur **+ répliquer** dans l’archivage sécurisé pour activer la réplication pour d’autres ordinateurs.
2. Dans la carte **Source** > **Source** sélectionner le serveur de configuration.
3. Dans **type d’ordinateur** sélectionnez **Machines virtuelles** ou **Ordinateurs physiques**.
4. Dans **vCenter/vSphere hyperviseur** sélectionner le serveur vCenter qui gère l’hôte vSphere, ou sélectionnez l’hôte. Ce paramètre n’est pas pertinent si vous êtes la réplication machines physiques.
5. Sélectionnez le serveur de traitement. Si vous n’avez pas créé tous les serveurs processus supplémentaire cela sera le nom du serveur de configuration. Cliquez ensuite sur **OK**.

    ![Activer la réplication](./media/site-recovery-vmware-to-azure/enable-replication2.png)

6. Dans **cible** sélectionnez l’abonnement de l’archivage sécurisé, puis dans le **modèle de déploiement Post-basculement** , sélectionnez le modèle (gestion classique ou une ressource) que vous souhaitez utiliser dans Azure après le basculement.
7. Sélectionnez le compte de stockage Azure que vous utiliserez pour la réplication de données. Notez que :

    - Vous pouvez sélectionner un compte de stockage standard ou premium. Si vous sélectionnez un compte premium, vous devez spécifier un compte espace de stockage supplémentaire standard pour les journaux de réplication en cours. Comptes doivent être placé dans la même région en tant que l’archivage sécurisé aux Services de récupération.
    - Si vous souhaitez utiliser un compte de stockage autre que celles vous devez vous pouvez [en créer un](#set-up-an-azure-storage-account). Pour créer un espace de stockage compte à l’aide du modèle de processeur, cliquez sur **Créer nouveau**. Si vous voulez créer un compte de stockage à l’aide du modèle classique vous ferez que [dans le portail Azure](../storage/storage-create-storage-account-classic-portal.md).

8. Sélectionnez le réseau Azure et sous-réseau auquel machines virtuelles Azure va se connecter lorsqu’ils êtes lancés après le basculement. Le réseau doit être placé dans la même région en tant que l’archivage sécurisé aux Services de récupération. Cliquez sur **configurer maintenant pour les ordinateurs sélectionnés** pour appliquer le paramètre de réseau à tous les ordinateurs que vous sélectionnez pour la protection. Sélectionnez **configurer ultérieurement** pour sélectionner le réseau Azure par ordinateur. Si vous n’avez pas un réseau, vous devez en [créer un](#set-up-an-azure-network). Pour créer une connexion réseau à l’aide du modèle de processeur, cliquez sur **Créer nouveau**. Si vous voulez créer un réseau à l’aide du modèle classique vous ferez que [dans le portail Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Sélectionner un sous-réseau le cas échéant. Cliquez ensuite sur **OK**.

    ![Activer la réplication](./media/site-recovery-vmware-to-azure/enable-replication3.png)

9. Machines **virtuelles** > **Sélectionnez machines virtuelles** cliquez puis sélectionnez chaque ordinateur que vous voulez dupliquer. Vous ne pouvez sélectionner machines pour lesquelles la réplication peut être activée. Cliquez ensuite sur **OK**.

    ![Activer la réplication](./media/site-recovery-vmware-to-azure/enable-replication5.png)

10. Dans les **Propriétés** > **configurer les propriétés**, sélectionnez le compte qui sera utilisé automatiquement par le serveur de processus pour installer le service mobilité sur l’ordinateur. Par défaut, tous les disques sont répliquées. Cliquez sur **Tous les disques** et désactivez les disques que vous ne voulez pas répliquer. Cliquez ensuite sur **OK**. Vous pouvez définir des propriétés supplémentaires ultérieurement.

    ![Activer la réplication](./media/site-recovery-vmware-to-azure/enable-replication6.png)

11. Dans **les paramètres de réplication** > **configurer des paramètres de réplication** Vérifiez que la stratégie de réplication correcte est sélectionnée. Vous pouvez modifier les paramètres de stratégie de réplication dans **les paramètres** > **stratégies de réplication** > nom de la stratégie > **Modifier les paramètres**. Permet d’appliquer les modifications que vous appliquez à une stratégie de réplication et les nouvelles machines.

12. Activer **la cohérence Multi-machine virtuelle** si vous voulez rassembler des machines dans un groupe de réplication, puis spécifiez un nom pour le groupe. Cliquez ensuite sur **OK**. Notez que :

    - Ordinateurs de réplication regroupement répliquer et partagent des points de récupération de blocage cohérentes et cohérente à l’échelle de l’application en cas d’échec sur.
    - Nous vous conseillons de recueillir machines virtuelles et des serveurs physiques ensemble afin qu’ils reflètent vos charges de travail. L’activation cohérence multi-machine virtuelle peut affecter les performances de la charge de travail et doit être utilisée uniquement si les ordinateurs la même charge de travail et que vous devez la cohérence.

    ![Activer la réplication](./media/site-recovery-vmware-to-azure/enable-replication7.png)

13. Cliquez sur **Activer la réplication**. Vous pouvez suivre la progression de la tâche **d’Activation de la Protection** dans **les paramètres** > **travaux** > **Tâches de récupération de Site**. Après l’exécution de la tâche de **Protection finaliser** l’ordinateur est prêt pour le basculement.

> [AZURE.NOTE] Si l’ordinateur est prêt pour l’installation push que le composant de service mobilité sera installé lorsque la protection est activée. Après le composant est installé sur l’ordinateur qu'une tâche de protection démarre et échoue. Après l’échec, vous devez redémarrer manuellement chaque ordinateur. Après le redémarrage la tâche de protection commence à nouveau et la réplication initiale se produit.

### <a name="view-and-manage-vm-properties"></a>Afficher et gérer les propriétés de la machine virtuelle

Nous vous recommandons de vérifier les propriétés de l’ordinateur source. N’oubliez pas que le nom de la machine virtuelle Azure doit être conformes aux [exigences de machine virtuelle Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).

1. Cliquez sur **paramètres** > **éléments répliqué** > et sélectionnez l’ordinateur. La carte **Essentials** affiche des informations sur les paramètres des ordinateurs et l’état.

2. Dans les **Propriétés** vous pouvez afficher les informations de réplication et le basculement de la machine virtuelle.

    ![Activer la réplication](./media/site-recovery-vmware-to-azure/test-failover2.png)

3. Dans le **cluster et réseau** > **Calculer propriétés** que vous pouvez spécifier la taille de nom et cibles machine virtuelle Azure. Modifier le nom conformes aux exigences Azure si vous avez besoin.
Vous pouvez également afficher et ajouter des informations sur le réseau cible, le sous-réseau et l’adresse IP qui doivent être affectée à la machine virtuelle Azure. Notez les points suivants :

    - Vous pouvez définir l’adresse IP cible. Si vous ne fournissez pas une adresse de l’échec de la sur ordinateur utilise DHCP. Si vous définissez une adresse qui n’est pas disponible lors du basculement, le basculement ne fonctionne pas. La même adresse IP cible peut servir pour le basculement de test si l’adresse est disponible dans le réseau de basculement test.
    - Le nombre de cartes réseau est dépend de la taille que vous spécifiez pour la machine virtuelle cible, comme suit :

        - Si le nombre de cartes réseau sur l’ordinateur source est inférieur ou égal au nombre de cartes autorisé pour la taille de l’ordinateur cible, la cible ont le même nombre de cartes comme source.
        - Si le nombre de cartes sur l’ordinateur virtuel source dépasse le nombre autorisé pour la taille de la cible avant la taille maximale des cible sera utilisée.
        - Par exemple, si une machine source a deux cartes réseau et la taille de l’ordinateur cible prend en charge quatre, l’ordinateur cible comporte deux cartes. Si l’ordinateur source possède deux cartes, mais la taille cible pris en charge n’accepte qu’une l’ordinateur cible aura qu’une carte.     
    - Si la machine virtuelle possède plusieurs cartes réseau qu’ils seront tous se connecter au même réseau.

    ![Activer la réplication](./media/site-recovery-vmware-to-azure/test-failover4.png)

4. Dans **disques** , vous pouvez voir le système d’exploitation et les données disques sur l’ordinateur virtuel qui sera répliquée.


## <a name="step-7-test-the-deployment"></a>Étape 7 : Tester le déploiement

Afin de tester le déploiement, vous pouvez exécuter un basculement de test pour une seule machine virtuelle ou un plan de récupération qui contient une ou plusieurs machines virtuelles.


### <a name="prepare-for-failover"></a>Préparer pour le basculement

- Pour exécuter un basculement de test, nous vous recommandons de créer un nouveau réseau Azure a isolé à partir de votre réseau de production Azure (c’est le comportement par défaut lorsque vous créez un nouveau réseau dans Azure). [En savoir plus](site-recovery-failover.md#run-a-test-failover) sur l’exécution de basculement test.
- Pour obtenir les meilleures performances lorsque vous basculer vers Azure, installez l’Agent Azure sur l’ordinateur protégé. Il crée démarrage plus rapides et aide à la résolution des problèmes. Installez l’agent [Linux](https://github.com/Azure/WALinuxAgent) ou [Windows](http://go.microsoft.com/fwlink/?LinkID=394789) .
- Pour tester entièrement votre déploiement, vous devez une infrastructure pour la machine répliquée fonctionne comme prévu. Si vous souhaitez tester Active Directory et DNS vous pouvez créer une machine virtuelle comme contrôleur de domaine avec le système DNS et répliquer ces paramètres sur Azure à l’aide de la récupération de Site Azure. Lisez en autres [Considérations relatives à basculement de test pour Active Directory](site-recovery-active-directory.md#considerations-for-test-failover).
- Vérifiez que le serveur de configuration est en cours d’exécution. Dans le cas contraire basculement échouera.
- Si vous avez exclu disques de la réplication, vous devrez peut-être créer ces disques manuellement dans Azure après le basculement afin qu’elle s’exécute comme prévu.
- Si vous souhaitez exécuter un basculement non planifié au lieu de basculement test Notez les points suivants :

    - Si possible, vous devez fermer vers le bas machines principales avant d’exécuter un basculement non planifié. Cela garantit que vous n’avez pas la source et la copie machines en cours d’exécution en même temps. Si vous êtes réplication machines virtuelles VMware vous pouvez spécifier que récupération de Site fasse de son mieux pour arrêter les ordinateurs source. En fonction de l’état du site principal Ceci peut ou ne fonctionne pas. Si vous êtes réplication serveurs physiques récupération de Site ne propose pas cette option.
    - Lorsque vous exécutez un basculement non planifié l’arrêt réplication des données à partir d’ordinateurs principales afin de n’importe quel delta de données ne seront pas transférée après qu’un basculement non planifié commence. En outre si vous exécutez un basculement non planifié sur un plan de récupération il s’exécutera jusqu'à la fin, même si une erreur se produit.

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Préparer pour vous connecter aux machines virtuelles Azure après le basculement

Si vous voulez vous connecter aux machines virtuelles Azure à l’aide de RDP après le basculement, vérifiez que vous effectuez les opérations suivantes :

**Sur l’ordinateur local avant le basculement**:

- Pour l’accès par le biais d’internet activer RDP, vous assurer que les règles TCP et UDP sont ajoutés pour le **Public**et faire en sorte que RDP est autorisé dans le **Pare-feu Windows** -> **autorisés applications et fonctionnalités** pour tous les profils.
- Pour l’accès via une connexion de site à activer RDP sur l’ordinateur et faire en sorte que RDP est autorisé dans le **Pare-feu Windows** -> **autorisés applications et fonctionnalités** pour les réseaux de **domaine** et **privées** .
- Installez l' [agent Azure machine virtuelle](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) sur l’ordinateur local.
- [Installer manuellement le service mobilité](#install-the-mobility-service-manually) sur les ordinateurs au lieu d’utiliser le serveur de traitement pour envoyer automatiquement le service. C’est parce que l’installation push se produit uniquement une fois que l’ordinateur est activée pour la réplication.
- Assurez-vous que la stratégie SAN du système d’exploitation est définie sur OnlineAll. [Pour en savoir plus]( https://support.microsoft.com/kb/3031135)
- Désactiver le service IPSec avant d’exécuter le basculement.

**Sur le Azure machine virtuelle après le basculement**:

- Ajouter un point de terminaison public pour le protocole RDP (Port3389) et spécifiez des informations d’identification pour la connexion.
- Vérifiez que vous n’avez pas toutes les stratégies de domaine que vous empêchent de se connecter à une machine virtuelle à l’aide d’une adresse publique.
- Essayez de vous connecter. Si vous ne pouvez pas vous connecter Vérifiez que la machine virtuelle s’exécute. Pour obtenir d’autres conseils de dépannage Lisez cet [article](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


Si vous voulez accéder à une machine virtuelle Azure exécutant Linux après le basculement à l’aide d’un Secure Shell client (ssh), procédez comme suit :

**Sur l’ordinateur local avant le basculement**:

- Assurez-vous que le service banque d’informations sécurisé Shell sur l’ordinateur virtuel Azure est défini pour démarrer automatiquement au démarrage du système.
- Vérifiez que les règles de pare-feu permettent une connexion SSH à ce dernier.

**Sur le Azure machine virtuelle après le basculement**:

- Les règles de groupe de sécurité réseau de l’échec de la machine virtuelle et le sous-réseau Azure auquel il est connecté devront autoriser les connexions entrantes à la voie SSH.
- Un point de terminaison public doit être créée pour autoriser les connexions entrantes sur le port SSH (port TCP 22 par défaut).
- Si la machine virtuelle est accessible via une connexion VPN (itinéraire Express ou un site à VPN) le client peut être utilisé pour se connecter directement à la machine virtuelle sur SSH.

**Sur le Azure Windows/Linux machine virtuelle après le basculement**:

Si vous avez associé à la Machine virtuelle ou le sous réseau auquel l’ordinateur appartient à un groupe de sécurité réseau, assurez-vous que le groupe de sécurité réseau a une règle sortante pour autoriser HTTP/HTTPS. Assurez-vous également que le DNS du réseau afin du machine virtuelle est prise a échoué plus est configuré correctement. Autre le basculement peut expirer avec l’erreur-« Tâche PreFailoverWorkflow WaitForScriptExecutionTask dépassé ». Pour comprendre cela dans le détail, reportez-vous à la section sur la récupération dans [surveillance et le guide de dépannage](site-recovery-monitoring-and-troubleshooting.md#recovery).

## <a name="run-a-test-failover"></a>Exécuter un basculement de test

1. Basculer sur un seul ordinateur, dans **les paramètres** > **Répliquées des éléments**, cliquez sur la machine virtuelle > icône **+ basculement de Test** .

    ![Basculement de test](./media/site-recovery-vmware-to-azure/test-failover1.png)

2. Basculer sur un plan de récupération, dans **les paramètres** > de**Récupération Plans**, avec le bouton droit de l’offre > **Basculement de Test**. Pour créer une récupération planifier [Suivez ces instructions](site-recovery-create-recovery-plans.md).

3. Dans le **Test de basculement** sélectionnez le réseau Azure auquel machines virtuelles Azure sera connecté après basculement.
4. Cliquez sur **OK** pour lancer le basculement. Vous pouvez suivre la progression en cliquant sur l’ordinateur virtuel pour ouvrir ses propriétés ou sur le projet **Basculement de Test** dans la zone Nom de l’archivage sécurisé > **paramètres** > **travaux** > **tâches de récupération de Site**.
5. Lorsque le basculement atteint l’état de **test achevé** , procédez comme suit :

    1. Afficher la machine virtuelle réplica dans le portail Azure. Vérifiez que la machine virtuelle démarre correctement.
    2. Si vous êtes configuré pour machines virtuelles access à partir de votre réseau local, vous pouvez lancer une connexion Bureau à distance sur l’ordinateur virtuel.
    3. Cliquez sur **tester achevé** pour terminer.

        ![Basculement de test](./media/site-recovery-vmware-to-azure/test-failover6.png)


    4. Cliquez sur **Notes** pour enregistrer et enregistrer des observations associées le basculement de test.
    5. Cliquez sur **le basculement de test est achevé** pour nettoyer automatiquement l’environnement de test. Une fois que cela le basculement de test affiche un état **achevé** .
    6.  À ce stade tous les éléments ou machines virtuelles créées automatiquement par la récupération de Site pendant le basculement de test sont supprimés. Tous les éléments que vous avez créé pour le basculement de test ne sont pas supprimées.

    > [AZURE.NOTE] Si un basculement test persiste plus de deux semaines qu’elle est terminée en vigueur.


6. Une fois le basculement terminée, vous devriez pouvoir également afficher le réplica Azure machine s’affichent dans le portail Azure > **Machines virtuelles**. Vous devez vous assurer que la machine virtuelle est la taille appropriée, qui s’il est connecté au réseau approprié, et qu’il s’exécute.
7. Si vous [préparé pour les connexions après le basculement](#prepare-to-connect-to-azure-vms-after-failover) vous devriez pouvoir se connecter à la machine virtuelle Azure.

## <a name="monitor-your-deployment"></a>Surveiller votre déploiement

Voici comment vous pouvez contrôler les paramètres de configuration, état et la santé pour votre déploiement de récupération de Site :

1. Cliquez sur le nom de l’archivage sécurisé pour accéder au tableau de bord **Essentials** . Dans ce tableau de bord, vous pouvez travaux, état de la réplication, les plans de récupération, l’intégrité du serveur et événements récupération de Site.  Vous pouvez personnaliser Essentials pour afficher les vignettes et les dispositions qui sont plus utiles, y compris l’état d’autres chambres fortes sauvegarde et restauration de Site.<br>
![Essentials](./media/site-recovery-vmware-to-azure/essentials.png)

2. Dans la vignette de **l’état** , vous pouvez surveiller les serveurs de site (serveurs VMM ou configuration) qui en cas de problème et les événements déclenchés par récupération de Site dans les dernières 24 heures.
3. Vous pouvez gérer et analyser la réplication dans les **Éléments répliquées**, **Plans de récupération**, et **Les tâches de récupération de Site** vignettes. Vous pouvez explorer les travaux dans **les paramètres** -> **travaux** -> **Tâches de récupération de Site**.


## <a name="deploy-additional-process-servers"></a>Déployer les serveurs de traitement supplémentaires

Si vous avez faire pour évoluer votre déploiement au-delà de 200 machines source ou un taux évolution du quotidien total de plus de 2 To, vous devez les serveurs de traitement supplémentaires pour gérer le volume de trafic.

Vérifier les [recommandations de taille pour les serveurs de processus](#size-recommendations-for-the-process-server) , puis suivez les instructions suivantes pour configurer le serveur de traitement. Après avoir configuré le serveur, vous devez migrer machines source pour l’utiliser.

### <a name="install-an-additional-process-server"></a>Installer un serveur de processus supplémentaires

1. Dans **paramètres** > **serveurs de récupération de Site** , cliquez sur le serveur de configuration > **serveur de traitement**.

    ![Ajouter un serveur de processus](./media/site-recovery-vmware-to-azure/migrate-ps1.png)

2. Dans **Type de serveur** cliquez sur **processus server (en local)**.

    ![Ajouter un serveur de processus](./media/site-recovery-vmware-to-azure/migrate-ps2.png)

3. Téléchargez le fichier d’installation Site récupération unifiée et exécutez-le pour installer le serveur de traitement et l’enregistrer dans l’archivage sécurisé.
4. Dans **avant de commencer** , sélectionnez **Ajouter des serveurs supplémentaires processus à l’échelle déploiement**.
5. Exécuter l’Assistant de la même façon que vous l’avez fait lorsque vous [Configurez](#step-2-set-up-the-source-environment) le serveur de configuration.

    ![Ajouter un serveur de processus](./media/site-recovery-vmware-to-azure/add-ps1.png)

6. Dans **Détails du serveur Configuration** spécifier l’adresse IP du serveur de configuration et le mot de passe. Pour obtenir le mot de passe exécuter ** <SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe – n** sur le serveur de configuration.

    ![Ajouter un serveur de processus](./media/site-recovery-vmware-to-azure/add-ps2.png)

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migrer les machines pour utiliser le nouveau serveur de processus

1. Dans **paramètres** > **serveurs de récupération de Site** cliquez sur le serveur de configuration, puis développez **serveurs de traitement**.

    ![Mettre à jour le serveur de traitement](./media/site-recovery-vmware-to-azure/migrate-ps2.png)

2. Cliquez sur le serveur de traitement en cours d’utilisation, cliquez sur **changer**.

    ![Mettre à jour le serveur de traitement](./media/site-recovery-vmware-to-azure/migrate-ps3.png)

3. Dans la zone **Sélectionner le serveur de processus cible**, sélectionnez le nouveau serveur de processus que vous voulez utiliser, puis sélectionnez les machines virtuelles qui gère le nouveau serveur de processus. Cliquez sur l’icône d’information pour obtenir des informations sur le serveur. Pour vous aider à prendre les décisions de charger, l’espace moyen que nécessaire pour répliquer chaque ordinateur virtuel sélectionné vers le nouveau serveur de processus s’affiche. Activez la case à cocher pour commencer la réplication vers le nouveau serveur de processus.

## <a name="vmware-account-permissions"></a>Autorisations de compte VMware

Le serveur de traitement de découvrir automatiquement machines virtuelles sur un serveur vCenter. Pour effectuer la découverte automatique vous devez [définir un rôle (Azure_Site_Recovery)](#prepare-an-account-for-automatic-discovery) permettre la récupération de Site accéder au serveur VMware. Notez que si vous ne devez migrer des machines VMware à Azure et ne devez retour arrière à partir d’Azure, vous pouvez définir un rôle en lecture seule est suffisant. Les autorisations de rôle requis sont répertoriées dans le tableau suivant.

**Rôle** | **Plus d’informations** | **Autorisations**
--- | --- | ---
Rôle Azure_Site_Recovery | Découverte de VMware VM |Attribuer ces privilèges pour le serveur du centre de v :<br/><br/>Magasin de données -> affecter espace, parcourir le magasin de données, fichier de niveau faible opérations., suppression d’un fichier, mise à jour des fichiers de machine virtuelle<br/><br/>Réseau -> attribuer réseau<br/><br/>Ressources -> machine virtuelle d’attribuer au pool de ressources, migrer tension machine virtuelle, migrer machine virtuelle sous tension<br/><br/>Tâches -> créer une tâche, tâche mise à jour<br/><br/>Machine virtuelle -> Configuration<br/><br/>Machine virtuelle -> interagir -> répondre à la question, connexion périphérique., configurer un CD, supports de disquette configurer, éteindre Power sur, installez les outils VMware<br/><br/>Machine virtuelle -> stock -> créer, Registre, Unregister<br/><br/>Machine virtuelle -> Provisioning -> Télécharger la machine virtuelle autoriser, téléchargement des fichiers de machines virtuelles autoriser<br/><br/>Machine virtuelle -> instantanés -> supprimer des instantanés
rôle d’utilisateur vCenter | Découverte de VMware VM/basculement sans arrêt de source de machine virtuelle | Attribuer ces privilèges pour le serveur du centre de v :<br/><br/>Objet Data Center -> propager à l’objet enfant, rôle = en lecture seule <br/><br/>L’utilisateur est attribuée au niveau du centre de données, ce qui a accès à tous les objets dans le centre de données.  Si vous voulez limiter l’accès, attribuer le rôle **Aucun accès** avec l’objet **propager à enfant** aux objets enfants (vSphere hosts, banques de données, machines virtuelles et réseaux).
rôle d’utilisateur vCenter | Basculement et restauration | Attribuer ces privilèges pour le serveur du centre de v :<br/><br/>Centre de données objet – propager à l’objet enfant, rôle = Azure_Site_Recovery<br/><br/>L’utilisateur est attribuée au niveau du centre de données, ce qui a accès à tous les objets dans le centre de données.  Si vous voulez limiter l’accès, attribuer le rôle **Aucun accès** avec la **propager vers objet enfant** à l’objet enfant (vSphere hosts, banques de données, machines virtuelles et réseaux).  
## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus](site-recovery-failover.md) sur les différents types de basculement.
- [En savoir plus sur retour arrière](site-recovery-failback-azure-to-vmware.md) pour faire apparaître votre échec machines en cours d’exécution dans Azure revenir à votre environnement local.

## <a name="third-party-software-notices-and-information"></a>Informations relatives aux logiciels tiers et des informations

Ne pas traduire ou Localize

Le logiciel et le microprogramme en cours d’exécution dans le produit ou service Microsoft repose sur ou incorpore matériel à partir des projets ci-dessous (collectivement, « Code tiers »).  Microsoft est l’auteur pas d’origine du Code tiers.  La mention d’origine et la licence, sous lequel Microsoft reçu ce Code tiers, sont définis ci-dessous.

Les informations dans la Section A sont en ce qui concerne les composants de Code tiers à partir des projets présentées ci-après. Ces licences et ces informations sont fournies uniquement à des fins d’information.  Ce Code tiers est en cours relicensed pour vous par Microsoft sous les termes de licence logiciel de Microsoft pour le produit ou service Microsoft.  

Les informations dans la Section B sont en ce qui concerne les composants de Code tiers qui sont en cours mis à votre disposition par Microsoft sous les termes de licence d’origine.

Le fichier complet peut apparaître dans le [Centre de téléchargement Microsoft](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft réserve tous les droits non expressément concédés, que ce soit par implication, estoppel ou dans le cas contraire.
