<properties
    pageTitle="Répliquer machines virtuelles VMware et des serveurs physiques vers Azure avec récupération de Site Azure | Microsoft Azure"
    description="Cet article décrit comment déployer récupération de Site Azure pour organiser réplication, le basculement et restauration des machines virtuelles de VMware en local et des serveurs physiques Linux/Windows Azure."
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
    ms.date="09/29/2016"
    ms.author="raynew"/>

# <a name="replicate-vmware-virtual-machines-and-physical-servers-to-azure-with-azure-site-recovery"></a>Répliquer machines virtuelles VMware et des serveurs physiques vers Azure avec récupération de Site Azure

> [AZURE.SELECTOR]
- [Portail Azure](site-recovery-vmware-to-azure.md)
- [Portail classique](site-recovery-vmware-to-azure-classic.md)
- [Portail classique (hérité)](site-recovery-vmware-to-azure-classic-legacy.md)


Le service de récupération de Site Azure contribue à votre stratégie d’entreprise continuité et de reprise récupération (BCDR) en organiser réplication, basculement et la restauration des machines virtuelles et des serveurs physiques. Machines peuvent être répliquées vers Azure, ou à un centre de données secondaire en local. Pour un bref aperçu lisez [Nouveautés récupération de Site Azure ?](site-recovery-overview.md).

## <a name="overview"></a>Vue d’ensemble

Cet article décrit comment :

- **Machines virtuelles VMware répliquer Azure**— déployer la récupération de Site pour coordonner la réplication, basculement et la restauration des machines virtuelles de VMware local vers le stockage Azure.
- **Dupliquer des serveurs physiques vers Azure**— déployer la récupération Azure Site pour coordonner la réplication, le basculement et récupération de locaux Windows et Linux serveurs physiques vers Azure.

>[AZURE.NOTE] Cet article décrit comment répliquer sur Azure. Si vous souhaitez répliquer machines virtuelles VMware Windows/serveurs ou Linux physiques vers un centre de données secondaire, suivez les instructions de [cet article](site-recovery-vmware-to-vmware.md).

Publier des commentaires ou des questions en bas de cet article, ou sur le [Forum de Services de récupération Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="enhanced-deployment"></a>Déploiement amélioré

Cet article inclut contient des instructions pour un déploiement améliorée dans le portail Azure classique. Nous vous recommandons de qu'utiliser cette version pour tous les nouveaux déploiements. Si vous avez déjà déployé à l’aide de l’ancienne version antérieure, nous vous recommandons de migrer vers la nouvelle version. En savoir [plus](site-recovery-vmware-to-azure-classic-legacy.md##migrate-to-the-enhanced-deployment) sur la migration.

Le déploiement amélioré est une mise à jour principale. Voici un résumé des améliorations que nous avons apporté :

- **Aucune infrastructure machines virtuelles dans Azure**: répliquent les données directement à un compte de stockage Azure. En outre pour la réplication et basculement, il est inutile pour configurer n’importe quelle infrastructure machines virtuelles (serveur de configuration, serveur cible maître) nécessaire dans le déploiement hérité.  
- **Installation unifiée**: une seule installation fournit simple et extensibilité élevées pour les composants en local.
- **Déploiement sécurisé**: tout le trafic est chiffré et réplication gestion des communications sont envoyées sur HTTPS 443.
- **Points de récupération**: points de prise en charge de blocage et récupération des applications pour les environnements Windows et Linux et machine virtuelle et machine virtuelle à plusieurs configurations cohérentes à un seul prend en charge les deux.
- **Tester le basculement**: prise en charge pour le basculement sans interruption de service de test sur Azure, sans impact sur la production ou la suspension de la réplication.
- **Basculement non planifié**: prise en charge de basculement non planifié pour Azure avec une option améliorée pour arrêter automatiquement machines virtuelles avant le basculement.
- **Retour arrière**: retour arrière intégré qui réplique uniquement les modifications delta vers le site local.
- **vSphere 6.0**: prise en charge pour les déploiements VMware Vsphere 6.0 limitée.


## <a name="how-does-site-recovery-help-protect-virtual-machines-and-physical-servers"></a>En quoi récupération de Site utile protéger machines virtuelles et des serveurs physiques ?


- Les administrateurs VMware peuvent configurer protection hors site vers Azure de charges de travail métier et d’applications en cours d’exécution sur machines virtuelles VMware. Responsables de serveur peuvent répliquer serveurs Windows et Linux physique local vers Azure.
- La console de récupération de Site Azure fournit un emplacement unique pour le programme d’installation simple et la gestion de la réplication, le basculement et processus de récupération.
- Si vous êtes répliquer machines virtuelles VMware qui sont gérés par un serveur vCenter, récupération de Site de découvrir automatiquement ces machines virtuelles. Si machines se trouvent sur un hôte ESXi récupération de Site découvre machines virtuelles sur l’hôte.
- Exécuter basculement facile à partir de votre infrastructure en local pour Azure et de restauration (restauration) à partir d’Azure aux serveurs VMware VM dans le site local.
- Configurer les plans de récupération qui regroupent les charges de travail application hiérarchisées sur plusieurs ordinateurs. Vous pouvez basculer vers ces offres et récupération de Site fournit la cohérence multi-machine virtuelle afin que les ordinateurs qui exécutent les mêmes charges de travail peuvent être récupérés ensemble à un point de données cohérentes.


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

Composants du scénario :

- **Un serveur de gestion locale**: le serveur d’administration exécute des composants de récupération de Site :
    - **Serveur de configuration**: coordonnées de communication et gère les processus de réplication et la restauration des données.
    - **Serveur de traitement**: fonctionne comme une passerelle de réplication. Il reçoit des données à partir d’ordinateurs source protégée, optimise avec la mise en cache, de compression et chiffrement et envoie des données de réplication au stockage Azure. Par ailleurs, gestion de l’installation push de service mobilité machines protégées et effectue la détection automatique des machines virtuelles VMware.
    - **Serveur cible maître**: gère les données de la réplication lors du retour arrière à partir d’Azure.
    Vous pouvez également déployer un serveur de gestion qui se comporte comme un serveur de processus uniquement, afin d’adapter votre déploiement.
- **Service de la mobilité**: ce composant est déployé sur chaque ordinateur (VMware VM ou serveur physique) que vous souhaitez répliquer vers Azure. Il capture écritures de données sur l’ordinateur et les transmet au serveur de processus.
- **Azure**: vous n’avez pas besoin créer les ordinateurs virtuels Azure pour gérer la réplication et le basculement. Le service de récupération de Site assure la gestion des données et répliquent les données directement au stockage Azure. Machines virtuelles Azure répliquées sont empilés automatiquement vers le haut uniquement lorsque le basculement vers Azure se produit. Toutefois, si vous voulez basculer revenir à partir d’Azure au site local, vous devrez configurer une machine virtuelle Azure pour l’utiliser comme un serveur de traitement.


Le graphique montre comment ces composants interagissent.

![architecture](./media/site-recovery-vmware-to-azure/v2a-architecture-henry.png)

**Figure 1 : VMware/physique vers Azure** (créée par Henry Robalino)


## <a name="capacity-planning"></a>Planification de la capacité

Lorsque vous êtes planification de la capacité, ici ce que vous devez envisager :

- **L’environnement source**: planification de la capacité ou les exigences de machine VMware infrastructure et source.
- **Le serveur de gestion**: planification pour les serveurs de gestion locale qui exécutent les composants de récupération de Site.
- **La bande passante réseau de source à cible**-planification de la bande passante réseau requise pour la réplication entre la source et Azure

### <a name="source-environment-considerations"></a>Considérations sur l’environnement source

- **Tous les jours au maximum modifier taux**: un ordinateur protégé peut utiliser uniquement un serveur de traitement et un serveur de processus unique peut prendre jusqu'à 2 To de modification des données par jour. 2 To est donc que les données quotidiennes maximales modifier taux est prise en charge pour un ordinateur protégé.
- **Débit maximum**— une machine répliquée peut appartenir à un seul compte de stockage dans Azure. Un compte de stockage standard peut gérer un maximum de 20 000 requêtes par seconde, et nous vous conseillons de conserver le nombre de sorties par sur un ordinateur source à 20 000. Pour exemple si vous avez une machine source avec 5 disques et chaque disque génère sorties 120 par (taille 8 Ko) sur la source il sera au sein de la Azure par limite de sorties par disque de 500. Le nombre de comptes de stockage requis = source total sorties par/20000.


### <a name="management-server-considerations"></a>Considérations relatives au serveur de gestion

Le serveur d’administration exécute des composants de récupération de Site qui gèrent optimisation des données, réplication et gestion. Il doit être en mesure de gérer la capacité de taux de modification quotidien sur toutes les charges de travail en cours d’exécution sur les ordinateurs protégés et a bande passante suffisante pour réplication continue des données vers le stockage Azure. Plus précisément :

- Le serveur de traitement reçoit les données de la réplication de machines protégées et optimise avec la mise en cache, de compression et chiffrement avant d’envoyer à Azure. Le serveur de gestion doit disposer de suffisamment de ressources pour effectuer ces tâches.
- Le serveur de processus utilise cache disque. Nous vous recommandons d’un disque cache distinct de 600 Go ou plus traiter les modifications apportées aux données stockées dans le cas de critique réseau ou une panne. Au cours du déploiement, vous pouvez configurer le cache sur un lecteur qui comporte au moins 5 Go de stockage disponible mais 600 Go est la recommandation minimale.
- Meilleurs résultats, nous vous recommandons que le serveur de gestion se trouver sur le même réseau et le segment LAN que les ordinateurs à protéger. Il peut se trouver sur un autre réseau mais machines à protéger doivent avoir visibilité de réseau niveau 3.

Recommandations pour la taille du serveur d’administration sont répertoriées dans le tableau suivant.

**Processeur de serveur de gestion** | **Mémoire** | **Taille du cache de disque** | **Taux de modification de données** | **Machines protégées**
--- | --- | --- | --- | ---
8 vCPUs (2 sockets * 4 cœurs @ 2,5 GHz) | 16 GO | 300 GO | 500 Go ou moins | Déployer un serveur de gestion avec ces paramètres pour répliquer moins de 100 ordinateurs.
12 vCPUs (2 sockets * 6 cœurs @ 2,5 GHz) | 18 GO | 600 GO | 500 Go à 1 to | Déployer un serveur de gestion avec ces paramètres pour répliquer entre machines 100-150.
16 vCPUs (2 sockets * 8 cœurs @ 2,5 GHz) | 32 GO | 1 TO | 1 To à 2 To | Déployer un serveur de gestion avec ces paramètres pour répliquer entre machines 150-200.
Déployer un autre serveur de processus | | | > 2 To | Déployer des serveurs de traitement supplémentaires si vous êtes la réplication de plus de 200 ordinateurs, ou si les données quotidiennes changent taux dépasse 2 To.

Dans cet exemple :

- Chaque ordinateur source est configuré avec 3 disques de 100 Go.
- Nous avons utilisé le stockage évaluation à 8 lecteurs associations de sécurité de 10 K t/min avec RAID 10 les mesures de disque du cache.

### <a name="network-bandwidth-from-source-to-target"></a>Bande passante réseau de source à cible
Vérifiez que vous calculez la bande passante qui sera requise pour la réplication initiale et la réplication delta à l’aide de l' [outil de planification de capacité](site-recovery-capacity-planner.md)

#### <a name="throttling-bandwidth-used-for-replication"></a>Limitation de bande passante utilisée pour la réplication

Le trafic VMware répliqué vers Azure accède à un serveur de processus spécifique. Vous pouvez limiter la bande passante qui est disponible pour la réplication de récupération de Site sur ce serveur comme suit :

1. Ouvrir le composant logiciel enfichable Microsoft Azure sauvegarde MMC sur le serveur de gestion principal ou sur un serveur d’administration en cours d’exécution supplémentaires sa mise en service des serveurs de traitement. Par défaut un raccourci pour Microsoft Azure Backup est créée sur bureau, ou vous pouvez trouver dans : C:\Program Files\Microsoft Azure récupération Services Agent\bin\wabadmin.
2. Dans le composant logiciel enfichable, cliquez sur **Modifier les propriétés**.

    ![Limiter la bande passante](./media/site-recovery-vmware-to-azure-classic/throttle1.png)

3. Sous l’onglet **régulation** spécifier la bande passante pouvant être utilisées pour la réplication de récupération de Site et la planification applicable.

    ![Limiter la bande passante](./media/site-recovery-vmware-to-azure-classic/throttle2.png)

Vous pouvez également vous pouvez également définir la limitation de l’utilisation de PowerShell. Voici un exemple :

    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)

#### <a name="maximizing-bandwidth-usage"></a>Optimiser l’utilisation de la bande passante
Pour augmenter la bande passante utilisée pour la réplication par récupération de Site Azure nécessaires pour modifier une clé de Registre.

La clé suivante détermine le nombre de threads par réplication de disque qui sont utilisés lors de la réplication

    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM

 Dans un réseau « overprovisioned », cette clé de Registre doit être modifié à partir de ses valeurs par défaut. Nous prenons en charge un maximum de 32.  


[En savoir plus](site-recovery-capacity-planner.md) sur la planification détaillée de la capacité.

### <a name="additional-process-servers"></a>Serveurs de traitement supplémentaires

Si vous avez besoin de protéger les plus de 200 ordinateurs ou taux de modification quotidien est supérieure que 2 To vous pouvez ajouter des serveurs supplémentaires pour gérer la charge. Pour évoluer vous pouvez :

- Augmenter le nombre de serveurs d’administration. Par exemple, vous pouvez protéger jusqu'à 400 machines avec deux serveurs d’administration.
- Ajout de serveurs supplémentaires processus et les utiliser pour gérer le trafic à la place (ou en plus de) le serveur d’administration.

Le tableau suivant décrit un scénario dans lequel :

- Vous configurez le serveur de gestion d’origine à l’utiliser comme un serveur de configuration uniquement.
- Vous configurez un serveur de processus supplémentaires.
- Vous configurez protégées machines virtuelles pour utiliser le serveur de traitement supplémentaires.
- Chaque ordinateur protégé source est configuré avec trois disques de 100 Go.

**Serveur de gestion d’origine**<br/><br/>(serveur de configuration) | **Serveur de traitement supplémentaires**| **Taille du cache de disque** | **Taux de modification de données** | **Machines protégées**
--- | --- | --- | --- | ---
8 vCPUs (2 sockets * 4 cœurs @ 2,5 GHz), 16 Go de mémoire | 4 vCPUs (2 sockets * 2 cœurs @ 2,5 GHz), 8 Go de mémoire | 300 GO | 250 Go ou moins | Vous pouvez répliquer machines 85 ou inférieur.
8 vCPUs (2 sockets * 4 cœurs @ 2,5 GHz), 16 Go de mémoire | 8 vCPUs (2 sockets * 4 cœurs @ 2,5 GHz), 12 Go de mémoire | 600 GO | 250 Go à 1 to | Vous pouvez répliquer entre machines 85-150.
12 vCPUs (2 sockets * 6 cœurs @ 2,5 GHz), 18 Go de mémoire | 12 vCPUs (2 sockets * 6 cœurs @ 2,5 GHz) 24 Go de mémoire | 1 TO | 1 To à 2 To | Vous pouvez répliquer entre 150-225 machines.


Dans celle dans laquelle vous adapter vos serveurs sera dépendent de vos préférences pour une échelle de configuration ou évoluer modèle.  Vous évoluez en déployant quelques gestion haut de gamme et les serveurs de traitement ou évoluer en déployant plus de serveurs avec moins de ressources. Par exemple : Si vous avez besoin protéger des 220 machines vous pourriez effectuez une des opérations suivantes :

- Configurer le serveur de gestion d’origine avec 12vCPU, 18 Go de mémoire, un serveur de processus supplémentaires avec 12vCPU, 24 Go de mémoire et configurer des ordinateurs protégées pour utiliser uniquement le serveur de processus supplémentaires.
- Ou vous pouvez également configurer les deux serveurs d’administration (2 x 8vCPU, 16 Go de RAM) et les deux serveurs de traitement supplémentaires (1 x 8vCPU) et 4vCPU x 1 pour gérer 135 + 85 machines (220) et configurer des ordinateurs protégées pour utiliser les serveurs de traitement supplémentaires uniquement.


[Suivez ces instructions](#deploy-additional-process-servers) pour configurer un serveur de processus supplémentaires.

## <a name="before-you-start-deployment"></a>Avant de commencer le déploiement

Les tableaux résument les conditions préalables pour le déploiement de ce scénario.


### <a name="azure-prerequisites"></a>Conditions préalables Azure

**Conditions préalables** | **Plus d’informations**
--- | ---
**Compte Azure**| Vous avez besoin d’un compte [Microsoft Azure](https://azure.microsoft.com/) . Vous pouvez commencer avec une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur les tarifs de récupération de Site.
**Stockage Azure** | Vous devez posséder un compte de stockage Azure pour stocker les données dupliquées. Données répliquées sont stockées dans le stockage Azure et machines virtuelles Azure sont empilés des cas de basculement. <br/><br/>Vous avez besoin d’un [compte de stockage geo redondants standard](../storage/storage-redundancy.md#geo-redundant-storage). Le compte doit être dans la même région en tant que le service de récupération de Site et être associés à la même abonnement. Notez que la réplication aux comptes de stockage premium n’est pas actuellement pris en charge et ne doit pas être utilisée.<br/><br/>Nous ne prennent pas en charge le déplacement des comptes de stockage créés à l’aide du [nouveau portail Azure](../storage/storage-create-storage-account.md) au sein de groupes de ressources. [En savoir plus sur](../storage/storage-introduction.md) Stockage Azure.<br/><br/>
**Réseau Azure** | Vous avez besoin d’un réseau virtuel Azure qui machines virtuelles Azure se connecteront à basculement. Le réseau virtuel Azure doit être placé dans la même région en tant que l’archivage sécurisé récupération de Site.<br/><br/>Notez que vous aurez besoin un réseau VPN pour restauré après le basculement vers Azure connexion (ou Azure ExpressRoute) configurer à partir du réseau Azure au site local.


### <a name="on-premises-prerequisites"></a>Conditions préalables pour les locaux

**Conditions préalables** | **Plus d’informations**
--- | ---
**Serveur de gestion** | Vous avez besoin d’un serveur Windows 2012 R2 local s’exécutant sur un ordinateur virtuel ou le serveur physique. Tous les composants de récupération de Site locaux sont installées sur ce serveur d’administration<br/><br/> Nous vous recommandons de que vous déployez le serveur comme un VM VMware hautement disponible. Retour arrière vers le site local à partir d’Azure est toujours être aux machines virtuelles VMware indépendamment si vous vous basculé machines virtuelles ou serveurs physiques. Si vous ne configurez pas le serveur d’administration comme un VMware VM, vous devez configurer un serveur cible maître distinct comme un VMware VM pour recevoir le trafic de retour arrière.<br/><br/>Le serveur ne doit pas être un contrôleur de domaine.<br/><br/>Le serveur doit disposer d’une adresse IP statique.<br/><br/>Le nom d’hôte du serveur doit être 15 caractères ou moins.<br/><br/>Paramètres régionaux du système d’exploitation doivent être en anglais uniquement.<br/><br/>Le serveur de gestion nécessite un accès à internet.<br/><br/>Vous devez accéder à partir du serveur comme suit : accès temporaire sur HTTP 80 pendant l’installation des composants de récupération de Site (à télécharger MySQL) ; Accès sortant en continu sur 443 HTTPS pour la gestion de la réplication ; Accès sortant en continu sur 9443 HTTPS pour le trafic de réplication (ce port peut être modifié)<br/><br/> Vérifiez que ces URL est accessibles à partir du serveur de gestion : <br/>- \*. hypervrecoverymanager.windowsazure.com<br/>- \*. accesscontrol.windows.net<br/>- \*. backup.windowsazure.com<br/>- \*. blob.core.windows.net<br/>- \*. store.core.windows.net<br/>-https://www.msftncsi.com/ncsi.txt<br/>- [https://dev.MySQL.com/Get/archives/MySQL-5.5/MySQL-5.5.37-Win32.msi]( https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi " https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi")<br/><br/>Si vous avez des règles de pare-feu basé sur l’adresse IP sur le serveur, vérifiez que les règles permettent communications vers Azure. Vous devez autoriser les [Plages d’adresses IP Azure centre de données](https://www.microsoft.com/download/details.aspx?id=41653) et le port HTTPS (443). Vous devez également liste blanche plages d’adresses IP pour la région Azure de votre abonnement et pour les États-Unis Ouest. L' URL [https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi](https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi " https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi") est pour le téléchargement MySQL.
**VCenter/ESXi hôteVMware**: | Vous devez au moins un vSphere ESX/ESXi les hyperviseurs vMware gérer vos ordinateurs virtuels VMware, en cours d’exécution ESX/ESXi version 6.0, 5.5 ou 5.1 avec les dernières mises à jour.<br/><br/> Nous vous recommandons de que vous déployez un serveur vCenter VMware pour gérer vos hôtes ESXi. Il doit s’exécuter vCenter version 6.0 ou 5.5 avec les dernières mises à jour.<br/><br/>Notez que récupération de Site ne prend pas en charge les nouveaux vCenter et fonctionnalités vSphere 6.0 croisées telles que vMotion vCenter, volumes virtuels et stockage DRS. Prise en charge de la récupération de site est limitée aux fonctionnalités qui étaient également disponibles dans la version 5.5.
**Machines protégé**: | **AZURE**<br/><br/>Machines à protéger doivent être conformes avec [Azure conditions préalables](site-recovery-best-practices.md#azure-virtual-machine-requirements) pour la création de machines virtuelles Azure.<br><br/>Si vous voulez vous connecter aux ordinateurs virtuels Azure après le basculement vous devez activer des connexions de bureau à distance sur le pare-feu local.<br/><br/>Capacité disque individuel sur machines protégées ne doivent pas être plus de 1023 go. Une machine virtuelle peut avoir jusqu'à 64 disques (et donc jusqu'à 64 To). Si vous avez disques plus de 1 to pensez à l’aide de la réplication de base de données telles que SQL Server toujours sur ou protection des données Oracle.<br/><br/>Minimal 2 Go d’espace disponible sur le lecteur d’installation pour l’installation des composants.<br/><br/>Partagé invité disque clusters ne sont pas pris en charge. Si vous disposez d’un déploiement groupé envisagez d’utiliser la réplication de base de données telles que SQL Server toujours sur ou protection des données Oracle.<br/><br/>Unifiée microprogramme UEFI (Extensible Interface) / démarrage Extensible Interface(EFI) microprogramme n’est pas pris en charge.<br/><br/>Noms de machine doivent contenir entre 1 et 63 caractères (lettres, chiffres et des traits d’union). Le nom doit commencer par une lettre ou un chiffre et se terminent par une lettre ou un chiffre. Après qu’une machine est protégée, vous pouvez modifier le nom Azure.<br/><br/>**Machines virtuelles VMware**<br/><br>Vous devez installer VMware vSphere PowerCLI 6.0. sur le serveur de gestion (serveur de configuration).<br/><br/>Machines virtuelles VMware à protéger doit avoir installé et exécuté les outils VMware.<br/><br/>Si la source machine virtuelle a cartes, il est converti en une seule carte réseau après le basculement vers Azure.<br/><br/>Si machines virtuelles protégés ont un disque iSCSI récupération de Site convertit le disque iSCSI machine virtuelle protégé dans un fichier de disque dur virtuel lorsque la machine virtuelle bascule vers Azure. Si cible iSCSI peut être atteintes par la machine virtuelle Azure puis il se connecter à la cible iSCSI et voir essentiellement deux disques – le disque dur sur l’ordinateur virtuel Azure et le disque iSCSI source. Dans ce cas, vous devez déconnecter la cible iSCSI qui s’affiche sur l’échec au-dessus machine virtuelle Azure.<br/><br/>[En savoir plus](#vmware-permissions-for-vcenter-access) sur les autorisations utilisateur VMware nécessaire lors de la récupération de Site.<br/><br/> **WINDOWS SERVER MACHINES (sur VMware VM ou serveur physique)**<br/><br/>Le serveur doit exécuter un système d’exploitation 64 bits pris en charge : Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2 avec au moins SP1.<br/><br/>Le système d’exploitation doivent être installé sur le lecteur C:\ et le disque du système d’exploitation doit être un disque de base Windows (système d’exploitation ne doivent pas être installé sur un disque dynamique Windows).<br/><br/>Pour les ordinateurs Windows Server 2008 R2, vous devez disposer de .NET Framework 3.5.1 installé.<br/><br/>Vous devez fournir un compte d’administrateur (doit être un administrateur local sur l’ordinateur Windows) pour l’installation push le Service mobilité sur des serveurs Windows. Si le compte fourni est un compte de domaine, vous devez désactiver le contrôle d’accès utilisateur à distance sur l’ordinateur local. [En savoir plus](#install-the-mobility-service-with-push-installation).<br/><br/>Récupération de site est prise en charge machines virtuelles disque RDM.  Lors du retour arrière récupération de Site réutilise le disque RDM si le disque de machine virtuelle et RDM source d’origine est disponible. Si elles ne sont pas disponibles, lors du retour arrière récupération de Site crée un nouveau fichier VMDK pour chaque disque.<br/><br/>**ORDINATEURS LINUX**<br/><br/>Vous avez besoin d’un système d’exploitation 64 bits pris en charge : rouge chapeau Enterprise Linux 6,7 ; CentOS 6.5, 6.6,6.7 ; Oracle Enterprise Linux 6.4, 6.5 en cours d’exécution Unbreakable entreprise noyau version 3 (UEK3), SUSE Linux Enterprise Server 11 SP3 ou noyau compatible chapeau rouge.<br/><br/>fichiers/etc/hosts sur machines protégées doivent contenir des entrées correspondant au nom d’hôte local aux adresses IP associées à toutes les cartes réseau. <br/><br/>Si vous voulez vous connecter à une machine virtuelle Azure exécutant Linux après le basculement à l’aide d’un Secure Shell client (ssh), vérifiez que le service banque d’informations sécurisé Shell sur l’ordinateur protégé est défini pour démarrer automatiquement au démarrage du système, et que les règles de pare-feu autorisent un ssh connexion à ce dernier.<br/><br/>Protection ne peut être activée pour les ordinateurs Linux avec le stockage suivant : NFS (EXT3, ETX4, ReiserFS, XFS) ; Chemins multiples-périphérique logiciel Mapper (chemins multiples)) ; Gestionnaire de volume : (LVM2). Serveurs physiques avec stockage du contrôleur HP CCISS ne sont pas pris en charge. Le système de fichiers ReiserFS est pris en charge uniquement sur SUSE Linux Enterprise Server 11 SP3.<br/><br/>Récupération de site est prise en charge machines virtuelles disque RDM.  Lors du retour arrière pour Linux, récupération de Site ne réutiliser le disque RDM. À la place qu’il crée un nouveau fichier VMDK pour chaque disque RDM correspondante.

Uniquement pour Linux VM - Vérifiez que vous définissez le paramètre disk.enableUUID=true dans les paramètres de Configuration de la machine virtuelle dans VMware. Si cette ligne n’existe pas, l’ajouter. Cela est nécessaire pour fournir un UUID cohérent au VMDK pour qu’il charge correctement. Notez également que sans ce paramètre, retour arrière entraîne un téléchargement complet même si la machine virtuelle est Montant disponible Ajout de ce paramètre garantit que seules les modifications delta sont transférées précédent lors du retour arrière.

## <a name="step-1-create-a-vault"></a>Étape 1 : Créer un archivage sécurisé

1. Connectez-vous au [portail de gestion](https://manage.windowsazure.com/).
2. Développez **Data Services** > **Services de récupération** et cliquez sur **L’archivage sécurisé récupération de Site**.
3. Cliquez sur **créer une nouvelle** > **Création rapide**.
4. Dans la zone **nom**, entrez un nom convivial pour identifier l’archivage sécurisé.
5. Dans **la région**, sélectionnez la région géographique pour l’archivage sécurisé. Pour rester informé des régions pris en charge Voir disponibilité géographique dans [Les détails du prix Azure Site récupération](https://azure.microsoft.com/pricing/details/site-recovery/)
6. Cliquez sur **créer l’archivage sécurisé**.
    ![Archivage sécurisé nouveau](./media/site-recovery-vmware-to-azure-classic/quick-start-create-vault.png)

Vérifiez la barre d’état pour confirmer que l’archivage sécurisé a été créée. L’archivage sécurisé est indiquée comme étant **actif** sur la page principale dans **Les Services de récupération** .

## <a name="step-2-set-up-an-azure-network"></a>Étape 2 : Configurer un réseau Azure

Configurer un réseau Azure afin que machines virtuelles Azure sera connecté à un réseau après le basculement, et que retour arrière vers le site local peut fonctionne comme prévu.

1. Dans le portail Azure > **réseau virtuel créer** spécifier le nom du réseau. Nom sous-réseau et de plage d’adresses IP.
2. Vous devrez ajouter VPN/ExpressRoute au réseau si vous devez effectuer retour arrière. VPN/ExpressRoute peuvent être ajouté au réseau même après le basculement.

[Pour en savoir plus](../virtual-network/virtual-networks-overview.md) sur les réseaux Azure.

> [AZURE.NOTE] [Migration des réseaux](../resource-group-move-resources.md) au sein de groupes de ressources dans le même abonnement ou abonnements n’est pas prise en charge pour les réseaux utilisés pour le déploiement de récupération de Site.

## <a name="step-3-install-the-vmware-components"></a>Étape 3 : Installer les composants VMware

Si vous voulez répliquer virtuel VMware machines installer les composants VMware suivants sur le serveur de gestion :

1. [Téléchargez](https://developercenter.vmware.com/tool/vsphere_powercli/6.0) et installez VMware vSphere PowerCLI 6.0.
2. Redémarrez le serveur.


## <a name="step-4-download-a-vault-registration-key"></a>Étape 4 : Téléchargez une clé d’inscription de l’archivage sécurisé

1. À partir de la gestion serveur ouvrir la console de récupération de Site dans Azure. Dans les **Services de récupération de** page, cliquez sur l’archivage sécurisé pour ouvrir la page de démarrage rapide. Démarrage rapide peut également être ouvert à tout moment à l’aide de l’icône.

    ![Icône de démarrage rapide](./media/site-recovery-vmware-to-azure-classic/quick-start-icon.png)

2. Dans la page de **Démarrage rapide** , cliquez sur **Préparer les ressources cible** > **télécharger une clé d’enregistrement**. Le fichier d’enregistrement est généré automatiquement. Il est valable pendant 5 jours après sa création.


## <a name="step-5-install-the-management-server"></a>Étape 5 : Installer le serveur de gestion
> [AZURE.TIP] Vérifiez que ces URL est accessibles à partir du serveur de gestion :
>
- *. hypervrecoverymanager.windowsazure.com
- *. accesscontrol.windows.net
- *. backup.windowsazure.com
- *. blob.core.windows.net
- *. store.core.windows.net
- https://dev.MySQL.com/Get/archives/MySQL-5.5/MySQL-5.5.37-Win32.msi
- https://www.msftncsi.com/ncsi.txt




[AZURE.VIDEO enhanced-vmware-to-azure-setup-registration]

1. Dans la page de **Démarrage rapide** télécharger le fichier d’installation unifiée sur le serveur.

2. Exécutez le programme d’installation pour démarrer l’installation de l’Assistant Configuration d’unifiée de récupération de Site.

3.  Dans **avant de commencer** , sélectionnez **installer le serveur de configuration et le processus serveur**.

    ![Avant de commencer](./media/site-recovery-vmware-to-azure-classic/combined-wiz1.png)
4. Dans la **Licence du logiciel tiers** , cliquez sur **J’accepte** pour télécharger et installer MySQL. 

    ![Troisième = logiciels tiers](./media/site-recovery-vmware-to-azure-classic/combined-wiz105.PNG)

5. Dans **l’enregistrement** Parcourir et sélectionnez la clé d’inscription que vous avez téléchargé depuis l’archivage sécurisé.

    ![Enregistrement](./media/site-recovery-vmware-to-azure-classic/combined-wiz3.png)

6. Dans **Les paramètres d’Internet** spécifiez comment le fournisseur en cours d’exécution sur le serveur de configuration se connecteront aux récupération de Site Azure via internet.

    - Si vous voulez vous connecter avec le proxy est configuré sur l’ordinateur sélectionnez **se connecter avec les paramètres de proxy existants**.
    - Si vous souhaitez que le fournisseur de se connecter directement sélectionnez **se connecter directement sans proxy**.
    - Si le proxy existant requiert une authentification, ou vous souhaitez utiliser un proxy personnalisé pour la connexion du fournisseur, sélectionnez **se connecter avec les paramètres de proxy personnalisés**.
        - Si vous utilisez un proxy personnalisé, que vous devez spécifier l’adresse, les ports et les informations d’identification
        - Si vous utilisez un proxy il devez que vous avez déjà accepté les URL suivantes :
            - *. hypervrecoverymanager.windowsazure.com ;    
            - *. accesscontrol.windows.net ; 
            - *. backup.windowsazure.com ; 
            - *. blob.core.windows.net ; 
            - *. store.core.windows.net
            

    ![Pare-feu](./media/site-recovery-vmware-to-azure-classic/combined-wiz4.png)

7. Dans les **Conditions préalables pour vérifier** le programme d’installation s’exécute une vérification pour vous assurer que l’installation peut s’exécuter. 

    
    ![Conditions préalables](./media/site-recovery-vmware-to-azure-classic/combined-wiz5.png)

     Si un message d’avertissement apparaît à propos de la **synchronisation de temps globales vérifier** Vérifiez que l’heure de l’horloge système (paramètres de**Date et heure** ) est différente de celle dans le fuseau horaire.

    ![TimeSyncIssue](./media/site-recovery-vmware-to-azure-classic/time-sync-issue.png)

8. Dans la **Configuration de MySQL** créer des informations d’identification pour vous connecter à l’instance du serveur MySQL qui est installé.

    ![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz6.png)

9. Dans Sélectionner **Détails de l’environnement** si vous allez répliquer les machines virtuelles VMware. Si vous êtes, le programme d’installation vérifie que PowerCLI 6.0 est installé.

    ![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz7.png)

10. Dans **L’emplacement d’installation** , sélectionnez l’endroit où vous voulez installer les fichiers binaires et stocker le cache. Vous pouvez sélectionner un lecteur qui comporte au moins 5 Go de stockage disponible, mais nous vous recommandons d’un lecteur de cache au moins 600 Go d’espace libre.

    ![Emplacement d’installation](./media/site-recovery-vmware-to-azure-classic/combined-wiz8.png)

11. Dans **Sélection du réseau** , spécifiez le récepteur (carte réseau et le port SSL) sur lequel le serveur de configuration sera envoyer et recevoir des données de la réplication. Vous pouvez modifier la valeur par défaut le port (9443). En plus de ce port, port 443 sera utilisé par un serveur web orchestrant opérations de réplication. 443 ne doit pas être utilisé pour recevoir le trafic de réplication.


    ![Sélection du réseau](./media/site-recovery-vmware-to-azure-classic/combined-wiz9.png)



12.  En **Résumé** Vérifiez les informations, puis cliquez sur **installer**. Lorsque l’installation se termine à un mot de passe est générée. Vous en aurez besoin lorsque vous activez la réplication donc copiez-le et conserver dans un emplacement sécurisé.

    ![Résumé](./media/site-recovery-vmware-to-azure-classic/combined-wiz10.png)



13.  En **Résumé** , vérifiez les informations.

    ![Résumé](./media/site-recovery-vmware-to-azure-classic/combined-wiz10.png)

>[AZURE.WARNING]Proxy Microsoft Azure Service l’Agent de récupération doit être le programme d’installation.
>Une fois l’installation terminée lancer une application nommée « Microsoft Azure récupération Services Shell » dans le menu Démarrer de Windows. Dans la fenêtre de commande qui ouvre exécuter le jeu de commandes pour configurer les paramètres du serveur proxy suivant.
>
    $pwd = ConvertTo-SecureString -String ProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumb – ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine



### <a name="run-setup-from-the-command-line"></a>Exécutez le programme d’installation à partir de la ligne de commande

Vous pouvez également exécuter l’Assistant unifié à partir de la ligne de commande, comme suit :

    UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

Dans cet exemple :

- / ServerMode : obligatoire. Indique si l’installation doit installer les serveurs de configuration et processus ou uniquement au serveur de processus (utilisé pour installer les serveurs de traitement supplémentaires). Valeurs d’entrée : SC, PM.
- InstallDrive : obligatoire. Spécifie le dossier dans lequel les composants sont installés.
- / MySQLCredFilePath. Obligatoire. Spécifie le chemin d’accès à un fichier dans lequel les informations d’identification du serveur MySQL sont récit. Obtenir le modèle pour créer le fichier.
- / VaultCredFilePath. Obligatoire. Emplacement du fichier d’informations d’identification de l’archivage sécurisé
- / EnvType. Obligatoire. Type d’installation. Valeurs : VMware, NonVMware
- / PSIP et /CSIP. Obligatoire. Adresse IP du serveur de traitement et du serveur de configuration.
- / PassphraseFilePath. Obligatoire. Emplacement du fichier de mot de passe.
- / ByPassProxy. Facultatif. Spécifie que le serveur de gestion se connecte à Azure sans proxy.
- / ProxySettingsFilePath. Facultatif. Spécifie les paramètres pour un proxy personnalisé (proxy par défaut sur le serveur qui requiert une authentification), ou proxy personnalisé




## <a name="step-6-set-up-credentials-for-the-vcenter-server"></a>Étape 6 : Configurer les informations d’identification pour le serveur vCenter

> [AZURE.VIDEO enhanced-vmware-to-azure-discovery]

Le serveur de traitement de découvrir automatiquement machines virtuelles VMware qui sont gérés par un serveur vCenter. Pour la découverte automatique récupération de Site nécessite un compte et les informations d’identification qui peuvent accéder au serveur vCenter. Ce n’est pas pertinent si vous êtes la réplication des serveurs physiques.

Procédez comme suit :

1. Sur la vCenter server crée un rôle (**Azure_Site_Recovery**) au niveau du vCenter avec les [autorisations requises](#vmware-permissions-for-vcenter-access).
2. Attribuer le rôle **Azure_Site_Recovery** à un utilisateur vCenter.

    >[AZURE.NOTE] Un compte d’utilisateur vCenter dont le rôle en lecture seule peut exécuter basculement sans arrêter machines d’origine protégée. Si vous voulez arrêter ces ordinateurs, vous devez le rôle Azure_Site_Recovery. Notez que si vous migrez uniquement machines virtuelles VMware vers Azure et que vous n’avez pas besoin retour arrière le rôle en lecture seule est suffisant.

3. Pour ajouter le compte ouvrir **cspsconfigtool**. Il est disponible en tant qu’un raccourci sur le bureau et situé dans le dossier \home\svsystems\bin [emplacement d’installation].
2. dans l’onglet **Gérer les comptes** , cliquez sur **Ajouter un compte**.

    ![Ajouter un compte](./media/site-recovery-vmware-to-azure-classic/credentials1.png)

3. Dans les **Détails d’un compte** ajouter des informations d’identification pouvant être utilisées pour accéder au serveur vCenter. Notez que cela peut prendre plus de 15 minutes pour que le nom du compte doit apparaître dans le portail. Pour mettre à jour immédiatement, cliquez sur Actualiser sous l’onglet **Serveurs de Configuration** .

    ![Plus d’informations](./media/site-recovery-vmware-to-azure-classic/credentials2.png)

## <a name="step-7-add-vcenter-servers-and-esxi-hosts"></a>Étape 7 : Ajouter des serveurs vCenter et ESXi hosts

Si vous êtes réplication machines virtuelles VMware vous devez ajouter un serveur vCenter (ou hôte ESXi).

1. Sur les **serveurs** > **Serveurs de Configuration** , sélectionnez le serveur de configuration > **Ajouter un serveur vCenter**.

    ![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter1.png)

2. Ajouter le serveur vCenter ou détails de l’hôte ESXi, le nom du compte que vous avez spécifié pour accéder au serveur vCenter à l’étape précédente et le serveur de processus qui permet de découvrir les machines virtuelles VMware qui sont gérés par le serveur vCenter. Notez que le serveur vCenter ou l’hôte ESXi doit se trouver dans le même réseau que le serveur sur lequel le serveur de traitement est installé.

    >[AZURE.NOTE] Si vous voulez ajouter le serveur vCenter ou l’hôte ESXi avec un compte qui ne dispose pas des droits d’administrateur sur le serveur vCenter ou hôte, vérifiez que la vCenter ou comptes ESXi portent les privilèges activés : centre de données, magasin de données, dossier, Jost, réseau, les ressources, virtuelle d’ordinateur, vSphere commutateur distribué. En outre le serveur vCenter doit le privilège affichages de stockage.

    ![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter2.png)

3. Une fois terminée la découverte le serveur vCenter apparaîtront dans l’onglet **Serveurs de Configuration** .

    ![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter3.png)


## <a name="step-8-create-a-protection-group"></a>Étape 8 : Créer un groupe de protection

> [AZURE.VIDEO enhanced-vmware-to-azure-protection]


Groupes de protection sont des groupes logiques d’ordinateurs virtuels ou des serveurs physiques que vous souhaitez protéger à l’aide des mêmes paramètres de protection. Vous appliquez les paramètres de protection à un groupe de protection, et ces paramètres sont appliqués à tous les ordinateurs machines virtuelles/physique que vous ajoutez au groupe.

1. Ouvrir les **Éléments protégé** > **Groupe de Protection** et cliquez sur Ajouter un groupe de protection.

    ![Créer le groupe de protection](./media/site-recovery-vmware-to-azure-classic/protection-groups1.png)

2. Dans la page **Spécifier les paramètres du groupe de Protection** spécifier un nom pour le groupe, dans la **zone de** , sélectionnez le serveur de configuration sur lequel vous voulez créer le groupe. **Cible** est Azure.

    ![Paramètres de protection de groupe](./media/site-recovery-vmware-to-azure-classic/protection-groups2.png)

3. Dans la page **Spécifier les paramètres de réplication** configurer les paramètres de réplication qui seront utilisés pour tous les ordinateurs dans le groupe.

    ![Réplication de groupe protection](./media/site-recovery-vmware-to-azure-classic/protection-groups3.png)

    - **Machine virtuelle à plusieurs cohérence**: Si vous désactivez ce qu’il crée des points de récupération cohérents avec les applications partagées sur les ordinateurs dans le groupe de protection. Ce paramètre est plus pertinent lorsque tous les ordinateurs dans le groupe de protection utilisent la même charge de travail. Tous les ordinateurs seront récupérés au même point de données. Cette fonction est disponible si vous êtes la réplication machines virtuelles VMware Windows/serveurs ou Linux physiques.
    - **Seuil RPO**: définit le RPO. Alertes seront générées lors de la réplication de protection continue des données dépasse la valeur de seuil RPO configurée.
    - **Rétention des points de récupération**: Spécifie la fenêtre de rétention. Machines protégées peuvent être récupérés à tout moment dans cette fenêtre.
    - **Application cohérente fréquence instantané**: Spécifie la fréquence de création des points de récupération contenant des instantanés cohérents avec les applications.

Lorsque vous cliquez sur la coche un groupe de protection est créé avec le nom que vous avez spécifié. In addition un groupe de protection deuxième est créé avec le nom < protection groupe-nom retour arrière). Ce groupe de protection est utilisé si vous ne parvenez pas à son site local après basculement vers Azure. Vous pouvez surveiller les groupes de protection elles sont créées dans la page **Éléments protégé** .

## <a name="step-9-install-the-mobility-service"></a>Étape 9 : Installer le service mobilité

La première étape de l’activation de la protection de machines virtuelles et des serveurs physiques consiste à installer le service mobilité. Vous pouvez le faire de deux façons :

- Notifications push et installez le service sur chaque ordinateur à partir du serveur de processus automatiquement. Notez que lorsque vous ajoutez des ordinateurs à un groupe de protection et ils se trouvent déjà une version appropriée de l’installation du push mobilité service en cours d’exécution n’apparaît.
- Installer automatiquement le service à l’aide de votre méthode push enterprise comme WSUS ou Gestionnaire de Configuration de System Center. Vérifiez que vous avez configuré le serveur d’administration avant de le faire.
- Installer manuellement sur chaque ordinateur à protéger. aire que vous avez configuré le serveur de gestion avant de le faire.


### <a name="install-the-mobility-service-with-push-installation"></a>Installer le service mobilité installation push

Lorsque vous ajoutez des ordinateurs à un groupe de protection le service mobilité est automatiquement poussé et installé sur chaque ordinateur par le serveur de traitement.


#### <a name="prepare-for-automatic-push-on-windows-machines"></a>Préparer pour la distribution automatique sur les ordinateurs Windows

Voici comment procéder pour préparer des ordinateurs Windows afin que le service mobilité peut être installé automatiquement par le serveur de traitement.

1.  Créez un compte qui peut être utilisé par le serveur de traitement pour accéder à l’ordinateur. Le compte doit disposer de droits d’administrateur (local ou domaine). Notez que ces informations sont uniquement utilisées pour l’installation push du service mobilité.

    >[AZURE.NOTE] Si vous n’utilisez pas un compte de domaine, vous devez désactiver le contrôle d’accès utilisateur à distance sur l’ordinateur local. Pour ce faire, dans le Registre sous HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System, ajoutez l’entrée DWORD LocalAccountTokenFilterPolicy avec une valeur de 1 sous. Pour ajouter le Registre entrée de la commande Ouvrir commande ou à l’aide de PowerShell entrez **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.

2.  Sur le pare-feu Windows de l’ordinateur sur lequel vous voulez protéger, sélectionnez **Autoriser une application ou une fonctionnalité via le pare-feu** et activer le **fichier et le partage d’imprimante** et **Windows Management Instrumentation**. Pour les ordinateurs qui appartiennent à un domaine, vous pouvez configurer la stratégie de pare-feu avec un objet de stratégie de groupe.

    ![Paramètres du pare-feu](./media/site-recovery-vmware-to-azure-classic/mobility1.png)

2. Ajoutez le compte que vous avez créé :

    - Ouvrez **cspsconfigtool**. Il est disponible en tant qu’un raccourci sur le bureau et situé dans le dossier \home\svsystems\bin [emplacement d’installation].
    - Dans l’onglet **Gérer les comptes** , cliquez sur **Ajouter un compte**.
    - Ajoutez le compte que vous avez créé. Après avoir ajouté le compte, vous devez fournir les informations d’identification lorsque vous ajoutez un ordinateur à un groupe de protection.


#### <a name="prepare-for-automatic-push-on-linux-servers"></a>Préparer pour la distribution automatique sur des serveurs Linux

1.  Assurez-vous que l’ordinateur Linux à protéger est pris en charge comme décrit dans les [conditions préalables pour les locaux](#on-premises-prerequisites). Vérifiez qu’il existe une connectivité réseau entre l’ordinateur à protéger et le serveur de gestion qui s’exécute sur le serveur de traitement.

2.  Créez un compte qui peut être utilisé par le serveur de traitement pour accéder à l’ordinateur. Le compte doit être un utilisateur racine sur le serveur Linux source. Notez que ces informations sont uniquement utilisées pour l’installation push du service mobilité.

    - Ouvrez **cspsconfigtool**. Il est disponible en tant qu’un raccourci sur le bureau et situé dans le dossier \home\svsystems\bin [emplacement d’installation].
    - Dans l’onglet **Gérer les comptes** , cliquez sur **Ajouter un compte**.
    - Ajoutez le compte que vous avez créé. Après avoir ajouté le compte, vous devez fournir les informations d’identification lorsque vous ajoutez un ordinateur à un groupe de protection.

3.  Vérifiez que le fichier/etc/hosts sur le serveur Linux source contienne des entrées établir une correspondance entre le nom d’hôte local adresses IP associées à toutes les cartes réseau.
4.  Installer la dernière openssh, openssh server, les logiciels d’openssl sur l’ordinateur à protéger.
5.  Vérifiez que SSH est activée et en cours d’exécution sur le port 22.
6.  Activez l’authentification SFTP sous-système et mot de passe dans le fichier sshd_config comme suit :

    - Connectez-vous en tant que racine.
    - Dans le fichier /etc/ssh/sshd_config de fichier, recherchez la ligne qui commence par PasswordAuthentication.
    - Supprimez les marques de la ligne et changez la valeur **Aucune** valeur **Oui**.
    - Recherchez la ligne qui commence par **sous-système** et supprimez la ligne.

        ![Linux](./media/site-recovery-vmware-to-azure-classic/mobility2.png)


### <a name="install-the-mobility-service-manually"></a>Installer manuellement le service mobilité

Les programmes d’installation sont disponibles dans C:\Program Files (x86) \Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository.

Système d’exploitation source | Fichier d’installation service mobilité
--- | ---
Windows Server (64 bits uniquement) | Microsoft-ASR_UA_9. *.0.0_Windows_* release.exe
CentOS 6.4, 6.5, 6.6 (64 bits uniquement) | Microsoft-ASR_UA_9. *.0.0_RHEL6-64_*release.tar.gz
SUSE Linux Enterprise Server 11 SP3 (64 bits uniquement) | Microsoft-ASR_UA_9. *.0.0_SLES11-SP3-64_*release.tar.gz
Oracle Enterprise Linux 6.4, 6.5 (64 bits uniquement) | Microsoft-ASR_UA_9. *.0.0_OL6-64_*release.tar.gz


#### <a name="install-manually-on-a-windows-server"></a>Installer manuellement sur Windows server


1. Téléchargez et exécutez le programme d’installation approprié.
2. Dans **avant de commencer **, sélectionnez **service mobilité**.

    ![Service de mobilité](./media/site-recovery-vmware-to-azure-classic/mobility3.png)

3. Dans **Détails du serveur Configuration** spécifier l’adresse IP du serveur de gestion et le mot de passe qui a été généré lorsque vous avez installé les composants de serveur de gestion. Vous pouvez récupérer le mot de passe à en cours d’exécution : ** <SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe – n** sur le serveur d’administration.

    ![Service de mobilité](./media/site-recovery-vmware-to-azure-classic/mobility6.png)

4. Dans **L’emplacement d’installation** , conservez l’emplacement par défaut, puis cliquez sur **suivant** pour commencer l’installation.
5. En **Cours d’Installation** surveiller l’installation et redémarrez l’ordinateur si vous y êtes invité.

Vous pouvez également installer à partir de la ligne de commande :

UnifiedAgent.exe [/ rôle < Agent/MasterTarget >] [/ InstallLocation <Installation Directory>] [/ CSIP <IP address of CS to be registered with>] [/ PassphraseFilePath <Passphrase file path>] [/ LogFilePath <Log File Path>]

Dans cet exemple :

- / Rôle : obligatoire. Indique si le service mobilité doit être installé.
- / InstallLocation : obligatoire. Spécifie l’emplacement installer le service.
- / PassphraseFilePath : obligatoire. Spécifie le mot de passe de serveur de configuration.
- / LogFilePath : obligatoire. Spécifie l’emplacement des fichiers journaux d’installation

#### <a name="uninstall-mobility-service-manually"></a>Désinstaller manuellement le service de mobilité

Mobilité Service peut être désinstallé à l’aide de l’ajouter supprimer un programme dans le panneau de configuration ou à l’aide de la ligne de commande.

La commande pour désinstaller le Service mobilité à l’aide de la ligne de commande est

    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1}

#### <a name="modify-the-ip-address-of-the-management-server"></a>Modifier l’adresse IP du serveur d’administration

Après avoir exécuté l’Assistant vous pouvez modifier l’adresse IP du serveur d’administration comme suit :

1. Ouvrez la hostconfig.exe fichier (situé sur le bureau).
2. Sous l’onglet **Global** , vous pouvez modifier l’adresse IP du serveur d’administration.

    >[AZURE.NOTE] Vous ne devez modifier l’adresse IP du serveur d’administration. Le numéro de port pour les communications server management doit être 443 et utiliser HTTPS doit être activée. Le mot de passe ne doivent pas être modifié.

    ![Adresse IP du serveur gestion](./media/site-recovery-vmware-to-azure-classic/host-config.png)

#### <a name="install-manually-on-a-linux-server"></a>Installer manuellement sur un serveur Linux :

1. Copiez l’archive tar appropriés en fonction du tableau ci-dessus sur l’ordinateur Linux à protéger.
2. Ouvrez un programme shell et extraire l’archive compressé tar dans un chemin d’accès local en exécutant :`tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. Créer un fichier passphrase.txt dans le répertoire local dans lequel vous avez extrait le contenu de l’archive tar. Pour effectuer ceci copiez le mot de passe C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase sur le serveur de gestion et enregistrez-le dans passphrase.txt en exécutant *`echo <passphrase> >passphrase.txt`* dans shell.
4. Installer le service mobilité en entrant *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Spécifier l’adresse IP interne du serveur d’administration et vérifiez que le port 443 est sélectionné.

**Vous pouvez également installer à partir de la ligne de commande**:

1. Copier le mot de passe à partir de C:\Program Files (x86) \InMage Systems\private\connection sur le serveur de gestion, puis enregistrez-le en tant que « passphrase.txt » sur le serveur de gestion. Puis exécuter ces commandes. Dans notre exemple, l’adresse IP du serveur gestion est 104.40.75.37 et le port HTTPS doit être 443 :

Pour installer sur un serveur de production :

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

Installer sur le serveur cible maître :


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt


## <a name="step-10-enable-protection-for-a-machine"></a>Étape 10 : Activer la protection pour une machine

Pour activer la protection ajouter machines virtuelles et des serveurs physiques à un groupe de protection. Avant de commencer, notez les points suivants si vous êtes protection des machines virtuelles VMware :

- Machines virtuelles VMware sont découvertes toutes les 15 minutes et cela peut prendre plus de 15 minutes pour qu’ils apparaissent dans le portail de récupération de Site après la découverte.
- Modifications de l’environnement sur l’ordinateur virtuel (par exemple, installation des outils VMware) peuvent également prendre plus de 15 minutes à mettre à jour dans récupération de Site.
- Vous pouvez vérifier la dernière fois découverte pour les machines virtuelles VMware dans le champ **Contact dernière en** pour l’hôte de serveur/ESXi vCenter sous l’onglet **Serveurs de Configuration** .
- Si vous avez déjà créé un groupe de protection et ajouter un hôte serveur ou ESXi vCenter après cela, il peut prendre plus de 15 minutes pour le portail de récupération de Site Azure actualiser et pour les machines virtuelles soit répertoriée dans la boîte de dialogue **Ajouter des ordinateurs à un groupe de protection** .
- Si vous voulez continuer immédiatement à l’ajout d’ordinateurs au groupe de protection sans attendre la découverte planifiée, mettez en surbrillance le serveur de configuration (ne cliquez pas dessus) et cliquez sur le bouton **Actualiser** .

En outre, notez que :

- Nous vous recommandons de concevoir vos groupes de protection afin qu’ils reflètent vos charges de travail. Par exemple ajouter des ordinateurs qui exécutent une application spécifique au même groupe.
- Lorsque vous ajoutez des ordinateurs à un groupe de protection, le serveur de traitement pousse automatiquement et installe le service mobilité s’il n’est pas déjà installé. Notez que vous doivent être le mécanisme push préparer comme décrit dans l’étape précédente.


Ajouter des ordinateurs à un groupe de protection :

1. Cliquez sur **éléments protégés** > **Groupe Protection** > **Machines** > ajouter des ordinateurs. \As une meilleure pratique
2. Dans **Sélectionner des Machines virtuelles** si vous êtes protection des machines virtuelles VMware, sélectionnez un serveur vCenter qui gère votre machines virtuelles (ou l’hôte EXSi sur lequel ils exécutent), puis les ordinateurs.

    ![Activer la protection](./media/site-recovery-vmware-to-azure-classic/enable-protection2.png)

3.  Dans **Sélectionner des Machines virtuelles** si vous êtes protection des serveurs physiques, dans l’Assistant **Ajouter des Machines physiques** fournir l’adresse IP et un nom convivial. Sélectionnez ensuite la famille de systèmes d’exploitation.

    ![Activer la protection](./media/site-recovery-vmware-to-azure-classic/enable-protection1.png)

4. Dans **Spécifier les ressources cible** sélectionnez le compte de stockage que vous utilisez pour la réplication et indiquez si les paramètres doivent être utilisés pour toutes les charges de travail. Notez que les comptes de stockage premium ne sont pas actuellement pris en charge.

    >[AZURE.NOTE] 1.Cliquez nous ne prennent pas en charge le déplacement des comptes de stockage créés à l’aide du [nouveau portail Azure](../storage/storage-create-storage-account.md) au sein de groupes de ressources.                           2.[la migration des comptes de stockage](../resource-group-move-resources.md) au sein de groupes de ressources dans le même abonnement ou abonnements n’est pas prise en charge pour les comptes de stockage utilisés pour le déploiement de récupération de Site.

    ![Activer la protection](./media/site-recovery-vmware-to-azure-classic/enable-protection3.png)

5. **Spécifier** les comptes, sélectionnez le compte [configuré](#install-the-mobility-service-with-push-installation) à utiliser pour l’installation automatique du service mobilité.

    ![Activer la protection](./media/site-recovery-vmware-to-azure-classic/enable-protection4.png)

6. Activez la case à cocher pour finaliser l’ajout des ordinateurs au groupe de protection et lancer la réplication initiale pour chaque ordinateur.

    >[AZURE.NOTE] Si l’installation push a été préparée la mobilité service s’installe automatiquement sur les ordinateurs qui ne l’avez pas qu’elles sont ajoutées au groupe de protection. Une fois le service installation une tâche de protection démarre et échoue. Après l’échec, vous devez redémarrer manuellement chaque ordinateur qui a été installé le service mobilité. Après le redémarrage la tâche de protection commence à nouveau et la réplication initiale se produit.

Vous pouvez surveiller l’état dans la page **tâches** .

![Activer la protection](./media/site-recovery-vmware-to-azure-classic/enable-protection5.png)

En outre, l’état de protection peut être contrôlé en **Éléments protégé** > <protection group name> > **Machines virtuelles**. Après l’exécution de la réplication initiale, et les données sont synchronisées, machine statut devient** protégé**.

![Activer la protection](./media/site-recovery-vmware-to-azure-classic/enable-protection6.png)


## <a name="step-11-set-protected-machine-properties"></a>Étape 11 : Ensemble protégé des propriétés de l’ordinateur

1. Une fois un ordinateur a un état **protégé** , vous pouvez configurer ses propriétés basculement. Dans les détails du groupe protection sélectionnez l’ordinateur et ouvrez l’onglet **configurer** .
2. Récupération de site suggère des propriétés de la machine virtuelle Azure automatiquement et détecte que les locaux paramètres réseau.

    ![Définir les propriétés de la machine virtuelle](./media/site-recovery-vmware-to-azure-classic/vm-properties1.png)

3. Vous pouvez modifier ces paramètres :

    -  **Nom de la machine virtuelle Azure**: il s’agit du nom qui sera attribué à l’ordinateur dans Azure après le basculement. Le nom doit respecter les exigences Azure.

    -  **Taille de mémoire virtuelle Azure**: le nombre de cartes réseau est dépend de la taille que vous spécifiez pour la machine virtuelle cible. [Pour en savoir plus](../virtual-machines/virtual-machines-linux-sizes.md/#size-tables) sur les tailles et les cartes. Notez que :

        - Si vous modifiez la taille pour une machine virtuelle et enregistrez les paramètres, le numéro de carte réseau changent lorsque vous ouvrez l’onglet **configurer** la prochaine fois. Le nombre de cartes réseau des machines virtuelles cible est au minimum le nombre de cartes réseau sur un ordinateur virtuel source et nombre maximal de cartes réseau pris en charge par la taille de la machine virtuelle choisie.
            - Si le nombre de cartes réseau sur l’ordinateur source est inférieur ou égal au nombre de cartes autorisé pour la taille de l’ordinateur cible, la cible ont le même nombre de cartes comme source.
            - Si le nombre de cartes sur l’ordinateur virtuel source dépasse le nombre autorisé pour la taille de la cible avant la taille maximale des cible sera utilisée.
            - Par exemple, si une machine source a deux cartes réseau et la taille de l’ordinateur cible prend en charge quatre, l’ordinateur cible comporte deux cartes. Si l’ordinateur source possède deux cartes, mais la taille cible pris en charge n’accepte qu’une l’ordinateur cible aura qu’une carte.
        - Si la machine virtuelle contient plusieurs cartes réseau que doivent toutes les cartes connecté au même réseau Azure.
    - **Azure réseau**: vous devez spécifier un réseau Azure Azure machines virtuelles sera connecté au après le basculement. Si vous n’en spécifiez aucune les ordinateurs virtuels Azure n’est pas connectés à un réseau. En outre, vous devez spécifier un réseau Azure si vous souhaitez effectuer un retour arrière à partir d’Azure au site local. Retour arrière nécessite une connexion VPN entre un réseau Azure et un réseau local.
    - **Adresse IP Azure/sous-réseau**: pour chaque carte réseau que vous sélectionnez le sous-réseau auquel la machine virtuelle Azure doit se connecter. Notez que :
        - Si la carte réseau de l’ordinateur source est configurée pour utiliser une adresse IP statique que vous pouvez spécifier une adresse IP statique de la machine virtuelle Azure. Si vous ne fournissez pas une adresse IP statique n’importe quelle adresse IP disponible est affecté. Si l’adresse IP cible est spécifié, mais il est déjà utilisé par un autre ordinateur virtuel dans Azure basculement échouera. Si la carte réseau de l’ordinateur source est configurée pour utiliser le protocole DHCP puis avoir DHCP en tant que le paramètre pour Azure.

## <a name="step-12-create-a-recovery-plan-and-run-a-failover"></a>Étape 12 : Créer un plan de récupération et exécuter un basculement

> [AZURE.VIDEO enhanced-vmware-to-azure-failover]

Vous pouvez exécuter un basculement pour un seul ordinateur, ou basculer sur plusieurs machines virtuelles qui effectuer la même tâche ou exécuter la même charge de travail. Pour basculer sur plusieurs ordinateurs en même temps ajoutez-les à un plan de récupération.

### <a name="create-a-recovery-plan"></a>Créer un plan de récupération

1. Dans la page **Récupération Plans** cliquez sur **Ajouter un Plan de récupération** et ajoutez un plan de récupération. Spécifier les détails pour le plan, puis sélectionnez **Azure** comme cible.

    ![Configurer le plan de récupération](./media/site-recovery-vmware-to-azure-classic/recovery-plan1.png)

2. Dans **Sélectionner l’ordinateur virtuel** sélectionnez un groupe de protection, puis sélectionnez machines dans le groupe auquel ajouter pour le plan de récupération.

    ![Ajouter des machines virtuelles](./media/site-recovery-vmware-to-azure-classic/recovery-plan2.png)

Vous pouvez personnaliser le plan pour créer des groupes et séquence l’ordre dans lequel sont basculés machines dans le plan de récupération. Vous pouvez également ajouter des scripts et les invites pour les actions manuelles. Les scripts peuvent être créés manuellement ou à l’aide en [Procédures opérationnelles Automation Azure](site-recovery-runbook-automation.md). [En savoir plus](site-recovery-create-recovery-plans.md) sur la personnalisation des plans de récupération.

## <a name="run-a-failover"></a>Exécuter un basculement

Avant d’exécuter une note de basculement qui :


- Assurez-vous que le serveur d’administration est en cours d’exécution et disponible - sinon basculement échouera.
- Si vous exécutez une note basculement non planifié qui :

    - Si possible, vous devez fermer vers le bas machines principales avant d’exécuter un basculement non planifié. Cela garantit que vous n’avez pas la source et la copie machines en cours d’exécution en même temps. Si vous êtes réplication machines virtuelles VMware puis lorsque vous exécutez un basculement non planifié vous pouvez spécifier que récupération de Site fasse mieux pour arrêter les ordinateurs source. En fonction de l’état du site principal Ceci peut ou ne fonctionne pas. Si vous êtes réplication serveurs physiques récupération de Site ne propose pas cette option.
    - Lorsque vous effectuez un basculement non planifié l’arrêt réplication des données à partir d’ordinateurs principales afin de n’importe quel delta de données ne seront pas transférée après qu’un basculement non planifié commence.

- Si vous voulez vous connecter à la machine virtuelle réplica dans Azure après le basculement, activer connexion Bureau à distance sur l’ordinateur source avant d’exécuter le basculement et autoriser la connexion RDP via le pare-feu. Vous devez également autoriser RDP sur le point de terminaison public de la machine virtuelle Azure après le basculement. Suivez ces [pratiques recommandées](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx) pour vous assurer que RDP fonctionne après un basculement.

>[AZURE.NOTE] Pour optimiser les performances lorsque vous effectuez un basculement vers Azure, vérifiez que vous avez installé l’Agent Azure sur l’ordinateur protégé. Cela permet de démarrage plus rapide et permet également de diagnostic en cas de problèmes. Agent de Linux peut être trouvé [ici](https://github.com/Azure/WALinuxAgent) - et Windows agent sont accessibles [ici](http://go.microsoft.com/fwlink/?LinkID=394789)

### <a name="run-a-test-failover"></a>Exécuter un basculement de test

Exécuter un basculement de test pour simuler votre processus de basculement et de restauration dans un réseau isolé qui n’affecte pas votre environnement de production et réplication régulières continue normalement. Basculement de test démarre sur la source et exécutez-le dans de deux façons :

- **Ne pas spécifier un réseau Azure**: Si vous exécutez un basculement test sans réseau le test vérifie simplement que machines virtuelles démarrer et s’affichent correctement dans Azure. Machines virtuelles ne va pas être connectés à un réseau Azure après le basculement.
- **Spécifier un réseau Azure**: ce type de basculement vérifie que l’environnement de réplication dans son intégralité est fourni comme prévu et que Azure machines virtuelles sont connectés au réseau spécifié.


1. Dans la page **Plans de** sélectionner le plan, puis cliquez sur **Test basculement**.

    ![Ajouter des machines virtuelles](./media/site-recovery-vmware-to-azure-classic/test-failover1.png)

2. **Confirmer** le basculement de Test, sélectionnez **Aucun** pour indiquer que vous ne voulez pas utiliser un réseau Azure pour le basculement de test, ou sélectionnez le réseau auquel le test machines virtuelles sera connecté après le basculement. Activez la case à cocher pour démarrer le basculement.

    ![Ajouter des machines virtuelles](./media/site-recovery-vmware-to-azure-classic/test-failover2.png)

3. Surveiller l’avancement de basculement sous l’onglet **tâches** .

    ![Ajouter des machines virtuelles](./media/site-recovery-vmware-to-azure-classic/test-failover3.png)

4. Une fois le basculement terminée, vous devriez pouvoir également afficher le réplica Azure machine s’affichent dans le portail Azure > **Machines virtuelles**. Si vous souhaitez établir une connexion RDP de la machine virtuelle Azure, vous devez ouvrir le port 3389 sur le point de terminaison machine virtuelle.

5. Une fois que vous avez terminé, lorsque le basculement atteint la terminer test phase, cliquez sur Test complet pour terminer. Dans Notes enregistrer et enregistrer des observations associées le basculement de test.

6. Cliquez sur **le basculement de test est achevé** pour nettoyer automatiquement l’environnement de test. Une fois que cela le basculement de test affiche un état **achevé** . Tous les éléments ou machines virtuelles créées automatiquement pendant le basculement de test sont supprimés. Notez que si un basculement test persiste plus de deux semaines, il est terminée en vigueur.



### <a name="run-an-unplanned-failover"></a>Exécuter un basculement non planifié

Basculement non planifié est établie à partir d’Azure et peut être effectuée même si le site principal n’est pas disponible.


1. Dans la page **Récupération Plans** , sélectionnez le plan, cliquez sur **Basculer** > **Basculement non planifié**.

    ![Ajouter des machines virtuelles](./media/site-recovery-vmware-to-azure-classic/unplanned-failover1.png)

2. Si vous êtes réplication machines virtuelles VMware, vous pouvez sélectionner pour essayer d’arrêter machines virtuelles en local. Il s’agit de mieux et basculement continuera si le travail a réussi ou non. Si elle n’aboutit pas détails de l’erreur seront affichent dans l’onglet **tâches **> **Tâches basculement non prévues**.

    ![Ajouter des machines virtuelles](./media/site-recovery-vmware-to-azure-classic/unplanned-failover2.png)

    >[AZURE.NOTE] Cette option n’est pas disponible si vous êtes la réplication des serveurs physiques. Vous devez essayer et arrêté ceux manuellement si possible.

3. Dans le **Basculement confirmer** vérifier la direction de basculement (vers Azure), puis sélectionnez le point de récupération à utiliser pour le basculement. Si vous avez activé multi-machine virtuelle lorsque vous avez configuré les propriétés de réplication, vous pouvez récupérer pour le dernier point de récupération application ou blocage cohérentes. Vous pouvez également sélectionner **des points de récupération personnalisée** à récupérer à un point antérieur dans le temps. Activez la case à cocher pour démarrer le basculement.

    ![Ajouter des machines virtuelles](./media/site-recovery-vmware-to-azure-classic/unplanned-failover3.png)

3. Attendez que la tâche basculement non planifié terminer. Vous pouvez surveiller l’avancement de basculement sous l’onglet **tâches** . Notez que même si des erreurs se produisent pendant le basculement non planifié le plan de récupération s’exécute jusqu'à ce qu’elle soit terminée. Vous devez également être en mesure de voir le réplica Azure machine apparaissent dans des Machines virtuelles dans le portail Azure.

### <a name="connect-to-replicated-azure-virtual-machines-after-failover"></a>Se connecter à répliquées Azure machines virtuelles après le basculement

Pour vous connecter à répliquée machines virtuelles dans Azure après que basculement ici est ce que vous devez :

1. Une connexion Bureau à distance doit être activée sur l’ordinateur principal.
2. Le pare-feu Windows sur l’ordinateur principal pour permettre à RDP.
3. Après le basculement, vous devez ajouter RDP au point de terminaison public pour Azure machine virtuelle.

[Pour en savoir plus](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx) sur la configuration de ces fonctionnalités.


## <a name="deploy-additional-process-servers"></a>Déployer les serveurs de traitement supplémentaires

Si vous avez à l’échelle hors votre déploiement au-delà de 200 machines source ou votre taux évolution du quotidien total dépasse 2 To, vous devez les serveurs de traitement supplémentaires pour gérer le volume de trafic. Pour configurer un serveur de processus supplémentaires Vérifiez la configuration requise de [serveurs supplémentaires processus](#additional-process-servers) et puis suivez les instructions pour configurer le serveur de traitement. Après avoir configuré le serveur, vous pouvez configurer machines source pour l’utiliser.

### <a name="set-up-an-additional-process-server"></a>Configurer un serveur de processus supplémentaires

Vous avez configuré un serveur de processus supplémentaires comme suit :

- Exécuter l’Assistant unifié pour configurer un serveur d’administration en tant que processus serveur uniquement.
- Si vous souhaitez gérer la réplication de données à l’aide du nouveau serveur processus, vous devrez migrer vos ordinateurs protégées pour effectuer cette opération.

### <a name="install-the-process-server"></a>Installer le serveur de traitement

1. Sur la page de démarrage rapide Téléchargez le fichier d’installation unifiée pour l’installation du composant récupération de Site. Exécutez le programme d’installation.
2. Dans **avant de commencer** , sélectionnez **Ajouter des serveurs supplémentaires processus à l’échelle déploiement**.

    ![Ajouter un serveur de processus](./media/site-recovery-vmware-to-azure-classic/add-ps1.png)

3. Exécuter l’Assistant de la même façon que vous l’avez fait lorsque vous [Configurez](#step-5-install-the-management-server) le premier serveur d’administration.

4. Dans **Détails du serveur Configuration** spécifier l’adresse IP du serveur d’administration d’origine sur lequel vous avez installé le serveur de configuration et le mot de passe. Sur le serveur de gestion d’origine exécuter ** <SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe – n** pour obtenir le mot de passe.

    ![Ajouter un serveur de processus](./media/site-recovery-vmware-to-azure-classic/add-ps2.png)

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migrer les machines pour utiliser le nouveau serveur de processus

1. Ouvrir les **Serveurs de Configuration** > **serveur** > nom du serveur d’administration d’origine > **Détails du serveur**.

    ![Mettre à jour le serveur de traitement](./media/site-recovery-vmware-to-azure-classic/update-process-server1.png)

2. Dans la liste **Des serveurs de traitement** cliquez sur **Changer de serveur de processus** en regard du serveur que vous souhaitez modifier.

    ![Mettre à jour le serveur de traitement](./media/site-recovery-vmware-to-azure-classic/update-process-server2.png)

3. Dans **Modifier les serveurs de processus** > **Serveur processus cible** sélectionnez Nouveau serveur d’administration, puis les machines virtuelles qui gère le nouveau serveur de processus. Cliquez sur l’icône d’information pour obtenir des informations sur le serveur. L’espace moyen que nécessaire pour répliquer chaque ordinateur virtuel sélectionné vers le nouveau serveur de processus s’affiche pour vous aider à prendre des décisions de charger. Activez la case à cocher pour commencer la réplication vers le nouveau serveur de processus.

    ![Mettre à jour le serveur de traitement](./media/site-recovery-vmware-to-azure-classic/update-process-server3.png)




## <a name="vmware-permissions-for-vcenter-access"></a>Autorisations de VMware pour l’accès vCenter

Le serveur de traitement de découvrir automatiquement machines virtuelles sur un serveur vCenter. Pour effectuer la découverte automatique, vous devez définir un rôle (Azure_Site_Recovery) au niveau vCenter afin de permettre la récupération de Site accéder au serveur vCenter. Notez que si vous ne devez migrer des machines VMware à Azure et ne devez retour arrière à partir d’Azure, vous pouvez définir un rôle en lecture seule est suffisant. Configurer les autorisations comme décrit dans [étape 6 : configurer les informations d’identification pour le serveur vCenter](#step-6-set-up-credentials-for-the-vcenter-server) les autorisations du rôle sont répertoriées dans le tableau suivant.

**Rôle** | **Plus d’informations** | **Autorisations**
--- | --- | ---
Rôle Azure_Site_Recovery | Découverte de VMware VM |Attribuer ces privilèges pour le serveur du centre de v :<br/><br/>Magasin de données -> affecter espace, parcourir le magasin de données, fichier de niveau faible opérations., suppression d’un fichier, mise à jour des fichiers de machine virtuelle<br/><br/>Réseau -> attribuer réseau<br/><br/>Ressources -> machine virtuelle d’attribuer au pool de ressources, migrer tension machine virtuelle, migrer machine virtuelle sous tension<br/><br/>Tâches -> créer une tâche, tâche mise à jour<br/><br/>Machine virtuelle -> Configuration<br/><br/>Machine virtuelle -> interagir -> répondre à la question, connexion périphérique., configurer un CD, supports de disquette configurer, éteindre Power sur, installez les outils VMware<br/><br/>Machine virtuelle -> stock -> créer, Registre, Unregister<br/><br/>Machine virtuelle -> Provisioning -> Télécharger la machine virtuelle autoriser, téléchargement des fichiers de machines virtuelles autoriser<br/><br/>Machine virtuelle -> instantanés -> supprimer des instantanés
rôle d’utilisateur vCenter | Découverte de VMware VM/basculement sans arrêt de source de machine virtuelle | Attribuer ces privilèges pour le serveur du centre de v :<br/><br/>Objet Data Center -> propager à l’objet enfant, rôle = en lecture seule <br/><br/>L’utilisateur est attribuée au niveau du centre de données, ce qui a accès à tous les objets dans le centre de données.  Si vous voulez limiter l’accès, attribuer le rôle **Aucun accès** avec l’objet **propager à enfant** aux objets enfants (ESX hosts, banques de données, machines virtuelles et réseaux).
rôle d’utilisateur vCenter | Basculement et restauration | Attribuer ces privilèges pour le serveur du centre de v :<br/><br/>Centre de données objet – propager à l’objet enfant, rôle = Azure_Site_Recovery<br/><br/>L’utilisateur est attribuée au niveau du centre de données, ce qui a accès à tous les objets dans le centre de données.  Si vous voulez limiter l’accès, attribuer le rôle **Aucun accès **avec la **propager vers objet enfant** à l’objet enfant (ESX hosts, banques de données, machines virtuelles et réseaux).  



## <a name="third-party-software-notices-and-information"></a>Informations relatives aux logiciels tiers et des informations

Ne pas traduire ou Localize

Le logiciel et le microprogramme en cours d’exécution dans le produit ou service Microsoft repose sur ou incorpore matériel à partir des projets ci-dessous (collectivement, « Code tiers »).  Microsoft est l’auteur pas d’origine du Code tiers.  La mention d’origine et la licence, sous lequel Microsoft reçu ce Code tiers, sont définis ci-dessous.

Les informations dans la Section A sont en ce qui concerne les composants de Code tiers à partir des projets présentées ci-après. Ces licences et ces informations sont fournies uniquement à des fins d’information.  Ce Code tiers est en cours relicensed pour vous par Microsoft sous les termes de licence logiciel de Microsoft pour le produit ou service Microsoft.  

Les informations dans la Section B sont en ce qui concerne les composants de Code tiers qui sont en cours mis à votre disposition par Microsoft sous les termes de licence d’origine.

Le fichier complet peut apparaître dans le [Centre de téléchargement Microsoft](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft réserve tous les droits non expressément concédés, que ce soit par implication, estoppel ou dans le cas contraire.

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur retour arrière](site-recovery-failback-azure-to-vmware-classic.md) pour faire apparaître votre échec machines en cours d’exécution dans Azure revenir à votre environnement local.
