<properties
    pageTitle="Préparer pour le déploiement de récupération de Site | Microsoft Azure"
    description="Cet article décrit comment se préparer à déployer la réplication avec récupération de Site Azure."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/05/2016"
    ms.author="raynew"/>

# <a name="prepare-for-azure-site-recovery-deployment"></a>Préparer pour le déploiement de récupération de Site Azure

Lisez cet article pour une vue d’ensemble détaillée de la configuration requise de déploiement pour chaque scénario de réplication pris en charge par le service de récupération de Site Azure. Une fois que vous lisez la configuration requise pour chaque scénario, vous pouvez lier à détails de déploiement spécifiques pour chaque déploiement.

Après lecture cet article publier des commentaires ou des questions dans la partie inférieure de l’article ou sur le [Forum de Services de récupération Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Vue d’ensemble

Les organisations doivent une stratégie BCDR qui détermine la façon applications, les charges de travail et les données restent en cours d’exécution et disponible au cours des interruptions liées aux tâches planifiées et et récupérer à des conditions de travail normal dès que possible. Votre stratégie BCDR doit conserver les données métiers approuvés et peuvent être restaurées et vous assurer que les charges de travail restent disponibles en permanence cas d’urgence. 

Récupération de site est un service Azure qui contribue à votre stratégie de BCDR en organiser réplication des serveurs physiques en local et machines virtuelles dans le cloud (Azure) ou un centre de données secondaire. Lorsque des défaillances se trouvent dans votre emplacement principal, vous basculer vers l’emplacement secondaire afin que les applications et les charges de travail disponibles. Vous ne parvenez pas à votre emplacement principal lorsqu’elle retourne à la normale. Pour plus d’informations [Nouveautés récupération de Site ?](site-recovery-overview.md)

## <a name="select-your-deployment-model"></a>Sélectionnez votre modèle de déploiement

Azure comporte deux différents [modèles de déploiement](../resource-manager-deployment-model.md) pour la création et utilisation des ressources : le modèle Azure le Gestionnaire de ressources et le modèle de gestion des services classique. Azure également comporte deux portails – le [portail classique Azure](https://manage.windowsazure.com/) qui prend en charge le modèle de déploiement classique, le [portail Azure](https://ms.portal.azure.com/) avec prise en charge pour les deux modèles de déploiement.

Récupération de site est disponible dans le portail classique et le portail Azure. Dans le portail classique Azure vous pouvez prendre en charge de récupération de Site avec le modèle de gestion des services classique. Dans le portail Azure vous pouvez prendre en charge le modèle classique ou les déploiements du Gestionnaire de ressources. [Pour en savoir plus](site-recovery-overview.md#site-recovery-in-the-azure-portal) sur le déploiement grâce au portail Azure.

Lorsque vous choisissez une note de modèle de déploiement qui :

- Nous vous recommandons de qu'utiliser le [portail Azure](https://portal.azure.com) et le modèle de gestionnaire de ressources lorsque cela est possible.
- Récupération de site fournit un plus simple et intuitive mise en route expérience dans le portail Azure.
- À l’aide du portail Azure, vous pouvez répliquer machines classique et Gestionnaire de ressources du stockage dans Azure. En outre, dans le portail Azure, vous pouvez utiliser LRS ou GRS comptes de stockage.
- Le portail Azure combine les services de sauvegarde et restauration de Site dans un archivage sécurisé aux Services de récupération unique afin que vous pouvez configurer et gérer les services BCDR depuis un emplacement unique.
- Opérations de récupération de Site dans le portail Azure peuvent maintenant gérer les utilisateurs disposant d’un abonnement Azure mis en service avec le programme Cloud Solution fournisseur (fournisseur).
- Dans le portail Azure la réplication machines virtuelles VMware ou machines physiques vers Azure fournit un certain nombre de nouvelles fonctionnalités ainsi prise en charge pour le stockage premium et la possibilité de disques spécifiques à exclure de la réplication.


## <a name="select-your-deployment-scenario"></a>Sélectionnez votre scénario de déploiement

**Déploiement** | **Plus d’informations** | **Portail Azure** | **Portail classique** | **PowerShell**
---|---|---|---|---
**Machines virtuelles VMware vers Azure** | Répliquer les machines virtuelles VMware au stockage Azure | Dans la Azure portal machines virtuelles peut basculer vers classique ou le Gestionnaire de ressources de stockage<br/><br/> Déploiement dans le [portail Azure](site-recovery-vmware-to-azure.md) fournit une expérience de rationaliser le déploiement et de nombreux avantages de la fonctionnalité. | Dans le portail Azure classique, vous pouvez déployer avec le [modèle amélioré](site-recovery-vmware-to-azure-classic.md) et basculer vers le stockage Azure classique.<br/><br/> Vous trouverez également un modèle hérité qui ne doivent pas être utilisé pour les nouveaux déploiements. |  PowerShell n’est pas actuellement pris en charge.
**Machines virtuelles Hyper-V vers Azure** | Ordinateurs virtuels Hyper-V au stockage Azure. Machines virtuelles peuvent être sur hôtes Hyper-V gérés dans nuages System Center VMM ou sans VMM. | Dans la Azure portal machines virtuelles peut basculer vers classique ou le stockage de gestionnaire de ressources.<br/><br/> Le portail Azure fournit une expérience de rationaliser le déploiement. [En savoir plus](site-recovery-vmm-to-azure.md) sur la réplication des machines virtuelles Hyper-V dans nuages VMM. [En savoir plus](site-recovery-hyper-v-site-to-azure.md) sur la réplication des ordinateurs virtuels Hyper-V (sans VMM).| Dans le portail Azure classique vous pouvez basculer machines virtuelles au stockage Azure classique | Déploiement de PowerShell est pris en charge.
**Serveurs physiques vers Azure** | Dupliquer des serveurs Windows/Linux physiques au stockage Azure | Dans la Azure serveurs de portail peuvent basculer vers classique ou le stockage de gestionnaire de ressources.<br/><br/> Déploiement dans le [portail Azure](site-recovery-vmware-to-azure.md) fournit une expérience de rationaliser le déploiement et de nombreux avantages de la fonctionnalité. | Dans le portail Azure classique, vous pouvez déployer avec le [modèle amélioré](site-recovery-vmware-to-azure-classic.md) et basculer vers le stockage Azure classique.<br/><br/> Vous trouverez également un modèle hérité qui ne doivent pas être utilisé pour les nouveaux déploiements. | Déploiement de PowerShell n’est pas actuellement pris en charge.
**Serveurs VMware machines virtuelles/physique vers le site secondaire* | Répliquer machines virtuelles VMware physique Windows/serveurs ou Linux sur un site secondaire. [En savoir plus et télécharger](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf) le guide de l’utilisateur InMage Scout. | Non disponible dans le portail Azure | Dans le portail classique, vous allez télécharger InMage Scout à partir d’un archivage sécurisé récupération de Site. | Déploiement de PowerShell n’est pas pris en charge actuellement
**Machines virtuelles Hyper-V sur un site secondaire** | Répliquer les machines virtuelles Hyper-V dans nuages VMM au cloud secondaire | Dans le [portail Azure](site-recovery-vmm-to-vmm.md) , vous pouvez répliquer machines virtuelles Hyper-V dans nuages VMM sur un site secondaire à l’aide de Hyper-V réplica uniquement. SAN n’est pas actuellement pris en charge. | Dans le portail classique Azure, vous pouvez répliquer machines virtuelles Hyper-V dans nuages VMM sur un site secondaire à l’aide de [Hyper-V réplica](site-recovery-vmm-to-vmm-classic.md) ou [réplication SAN](site-recovery-vmm-san.md) | Déploiement de PowerShell est pris en charge



## <a name="check-what-you-need-for-deployment"></a>Vérifiez que vous avez besoin pour le déploiement

### <a name="replicate-to-azure"></a>Répliquer vers Azure

**Configuration requise** | **Plus d’informations** 
---|---
**Compte Azure** | Vous avez besoin d’un compte [Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> Vous pouvez commencer avec une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur les tarifs de récupération de Site.
**Stockage Azure** | Données répliquées sont stockées dans le stockage Azure et Azure machines virtuelles sont créées lors du basculement. Pour répliquer sur Azure, vous devez un [compte de stockage Azure](../storage/storage-introduction.md).<br/><br/>Si vous déployez récupération de Site dans le portail classique, vous devez un ou plusieurs [comptes de stockage GRS standards](..storage/storage-redundancy.md#geo-redundant-storage).<br/><br/> Si vous déployez dans le portail Azure, vous pouvez utiliser stockage GRS ou LRS.<br/><br/> Si vous êtes la réplication machines virtuelles VMware ou serveurs physiques dans le stockage Azure premium portail est pris en charge. Notez que si vous utilisez un compte de stockage premium vous devez également un compte de stockage standard pour stocker les journaux de réplication capturer les modifications en cours apportées aux données locales. [Stockage Premium](../storage/storage-premium-storage.md) est généralement utilisée pour les machines virtuelles ayant besoin d’une manière cohérente à travers haute performance IO et faible latence aux charges de travail intensives hôte IO.<br/><br/> Si vous souhaitez utiliser un compte premium pour stocker les données dupliquées, vous devez également un compte de stockage standard pour stocker les journaux de réplication capturer les modifications en cours apportées aux données locales.
**Réseau Azure** | Pour répliquer sur Azure, vous devez un réseau Azure qui machines virtuelles Azure se connecteront à lorsqu’elles sont créées après le basculement.<br/><br/> Si vous déployez dans le portail classique, vous allez utiliser un réseau classique. Si vous déployez dans le portail Azure, vous pouvez utiliser un classique ou le Gestionnaire de ressources réseau.<br/><br/> Le réseau doit être placé dans la même région en tant que l’archivage sécurisé.
**Mappage réseau (Gestionnaire de mémoire virtuelle Azure)** | Si vous êtes réplication à partir de VMM vers Azure, [mappage réseau](site-recovery-network-mapping.md) garantit que machines virtuelles Azure sont connectés aux réseaux correctes après le basculement.<br/><br/> Pour configurer le mappage réseau, vous devez configurer les réseaux de machine virtuelle dans le portail VMM.
**En local** | **Machines virtuelles VMware**: vous avez besoin d’un ordinateur local exécutant composants de récupération de Site, VMware vSphere hosts/vCenter server et machines virtuelles que vous souhaitez répliquer. [Pour en savoir plus](site-recovery-vmware-to-azure.md#configuration-server-prerequisites).<br/><br/> **Serveurs physiques**: Si vous êtes réplication serveurs physiques vous devez un ordinateurs locaux serveurs physiques que vous souhaitez répliquer et les composants de récupération de Site. [Pour en savoir plus](site-recovery-vmware-to-azure.md#configuration-server-prerequisites). Si vous voulez [être restauré](site-recovery-failback-azure-to-vmware.md) après le basculement vers Azure, vous devez une infrastructure VMware à le faire.<br/><br/> **Machines virtuelles Hyper-V**: Si vous souhaitez répliquer les machines virtuelles Hyper-V dans nuages VMM vous aurez besoin d’un serveur VMM et hôtes Hyper-V sur les ordinateurs virtuels vous voulez protéger se trouvent. [Pour en savoir plus](site-recovery-vmm-to-azure.md#on-premises-prerequisites).<br/><br/> Si vous souhaitez répliquer les machines virtuelles Hyper-V sans VMM vous devez hôtes Hyper-V sur lequel se trouvent machines virtuelles. [Pour en savoir plus](site-recovery-hyper-v-site-to-azure.md#on-premises-prerequisites).
**Machines protégées** | Machines protégées répliquent vers Azure doivent répondre aux [conditions préalables Azure](#azure-virtual-machine-requirements) décrite ci-dessous.


### <a name="replicate-to-a-secondary-site"></a>Répliquer sur un site secondaire

**Configuration requise** | **Plus d’informations** 
---|---
**Compte Azure** | Vous avez besoin d’un compte [Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> Vous pouvez commencer avec une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur les tarifs de récupération de Site.
**En local** | **Machines virtuelles VMware**: vous devez un serveur de traitement pour la mise en cache, compression et le chiffrement des données de réplication avant l’envoi vers le site secondaire dans le site principal. Dans le site secondaire, vous devez installer un serveur de configuration pour gérer et surveiller le déploiement et un serveur principal cible. Nous vous recommandons également un serveur vContinuum pour faciliter la gestion. En outre, vous avez besoin d’un ou plusieurs hôtes vSphere VMware et éventuellement un serveur vCenter. [En savoir plus](site-recovery-vmware-to-vmware.md)<br/><br/> **Machines virtuelles Hyper-V dans nuages VMM**: vous avez besoin de configurer des serveurs VMM et hôtes Hyper-V contenant des machines virtuelles que vous souhaitez répliquer. [Pour en savoir plus](site-recovery-vmm-to-vmm.md#on-premises-prerequisites).
**Mappage réseau (Gestionnaire de mémoire virtuelle VMM)** | Si vous êtes réplication à partir de VMM à VMM, [mappage réseau](site-recovery-network-mapping.md) garantit que machines virtuelles réplica sont connectés à des réseaux appropriés après le basculement et mieux placés sur hôtes Hyper-V. Pour configurer le mappage réseau, vous devez configurer les réseaux de machine virtuelle sur vos serveurs VMM.
**Mappage d’espace de stockage** | Si vous êtes la réplication à partir de VMM à VMM vous pouvez éventuellement configurer [mappage de stockage](site-recovery-storage-mapping.md) pour spécifier la cible de stockage pour les machines virtuelles répliquées. Mappage d’espace de stockage peut être configuré pour la réplication réplica Hyper-V et SAN.<br/><br/> Mappage d’espace de stockage n’est pas actuellement pris en charge dans le portail Azure.


## <a name="verify-url-access"></a>Vérifier l’accès de l’URL

Assurez-vous que ces URL est accessibles à partir de serveurs.

**URL** | **Gestionnaire de mémoire virtuelle VMM** | **Gestionnaire de mémoire virtuelle Azure** | **Hyper-V pour Azure** | **VMware pour Azure**
---|---|---|---|---
*. accesscontrol.windows.net | Autoriser | Autoriser | Autoriser | Autoriser
*. backup.windowsazure.com | Non requis | Autoriser | Autoriser | Autoriser
*. hypervrecoverymanager.windowsazure.com | Autoriser | Autoriser | Autoriser | Autoriser
*. store.core.windows.net | Autoriser | Autoriser | Autoriser | Autoriser
*. blob.core.windows.net | Non requis | Autoriser | Autoriser | Autoriser
https://www.msftncsi.com/ncsi.txt | Autoriser | Autoriser | Autoriser | Autoriser
https://dev.MySQL.com/Get/archives/MySQL-5.5/MySQL-5.5.37-Win32.msi | Non requis | Non requis | Non requis | Autoriser

## <a name="azure-virtual-machine-requirements"></a>Configuration requise Azure machine virtuelle

Vous pouvez déployer récupération de Site pour répliquer machines virtuelles et des serveurs physiques exécutant un système d’exploitation pris en charge par Azure. Cela inclut la plupart des versions de Windows et Linux. Vous devez pour vous assurer que local machines virtuelles que vous voulez protéger sont conformes aux exigences Azure.


**Fonctionnalité** | **Configuration requise** | **Plus d’informations**
---|---|---
Hôte Hyper-V | Doit être exécutant Windows Server 2012 R2 | Conditions préalables vérification échouera si le système d’exploitation non pris en charge
Hyperviseur VMware  | Système d’exploitation pris en charge | [Vérifier la configuration requise](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)
Système d’exploitation invité | Hyper-V pour réplication Azure : récupération de Site prend en charge tous les systèmes d’exploitation [pris en charge par Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Pour VMware et la réplication de serveur physique : vérifier les [conditions préalables pour](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) Windows et Linux | Conditions préalables vérification échouera si non prises en charge.
Architecture de système d’exploitation invité | 64 bits | Conditions préalables vérification échouera si non prises en charge
Taille du disque système d’exploitation |  Atteindre 1023 Go | Conditions préalables vérification échouera si non prises en charge
Nombre de disque du système d’exploitation | 1 | Conditions préalables vérification échouera si non prises en charge.
Nombre de disques de données | 16 ou inférieure (valeur maximale est une fonction de la taille de la machine virtuelle en cours de création. 16 = XL) | Conditions préalables vérification échouera si non prises en charge
Taille des données disque dur | Jusqu'à 1023 Go | Conditions préalables vérification échouera si non prises en charge
Cartes réseau | Plusieurs cartes sont prises en charge |
Adresse IP statique | Prise en charge | Si la machine virtuelle primaire utilise une adresse IP statique, vous pouvez spécifier l’adresse IP statique de la machine virtuelle qui seront créée dans Azure. Notez qu’adresse IP statique pour une machine virtuelle linux s’exécutant sur Hyper-v n’est pas pris en charge.
disque iSCSI | Non pris en charge | Conditions préalables vérification échouera si non prises en charge
Disque dur virtuel partagé | Non pris en charge | Conditions préalables vérification échouera si non prises en charge
Disque Fibre Channel | Non pris en charge | Conditions préalables vérification échouera si non prises en charge
Format du disque dur| DISQUE DUR VIRTUEL <br/><br/> VHDX | Bien que VHDX n’est pas actuellement pris en charge dans Azure, récupération de Site convertit automatiquement VHDX disque dur virtuel lorsque vous basculer vers Azure. Lorsque vous ne parvenez pas à en local les machines virtuelles continuent à utiliser le format VHDX.
BitLocker | Non pris en charge | BitLocker doit être désactivé avant de protéger une machine virtuelle.
Nom de la machine virtuelle| Entre 1 et 63 caractères. Limité à des lettres, des chiffres et des traits d’union. Doit commencer et se terminent par une lettre ou un numéro | Mettre à jour la valeur dans les propriétés de la machine virtuelle de récupération de Site
Type de machine virtuelle | <p>Génération 1</p> <p>Génération 2 - Windows</p> |  Machine virtuelle de génération 2 avec le type de disque du système d’exploitation de disque de base qui inclut 1 ou 2 volumes de données avec mise en forme disque comme VHDX qui est inférieur à 300 Go est prise en charge. Machines virtuelles Linux génération 2 ne sont pas pris en charge. [En savoir plus](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)



## <a name="optimizing-your-deployment"></a>Optimisation de votre déploiement

Utilisez les conseils suivants pour vous aider à optimiser et mettre à l’échelle votre déploiement.

- **Taille du volume système d’exploitation**: lors de la réplication une machine virtuelle vers Azure le volume de système d’exploitation doit être inférieure à 1 To. Si vous avez plusieurs volumes que cela vous pouvez manuellement les déplacer vers un autre disque avant de commencer le déploiement.
- **Taille du disque données**: Si vous êtes réplication vers Azure vous pouvez avoir jusqu'à 32 disques de données sur une machine virtuelle, chacune avec un maximum de 1 To. Vous pouvez efficacement répliquer et basculer une machine virtuelle ~ 32 To.
- **Limites de plan de récupération**: récupération de Site peut s’adapter à des milliers de machines virtuelles. Plans de récupération sont conçues en tant que modèle pour les applications qui ne doit basculer ensemble afin que nous limiter le nombre d’ordinateurs dans un plan de récupération à 50.
- **Limites de service Azure**: Azure chaque abonnement comprend un ensemble de limites par défaut sur cœurs, etc. services cloud. Nous vous conseillons d’exécuter un basculement test pour valider la disponibilité des ressources dans votre abonnement. Vous pouvez modifier ces limites via Azure prise en charge.
- **Planification de la capacité**: en savoir plus sur [la planification des capacités](site-recovery-capacity-planner.md) récupération de Site.
- **Bande passante de réplication**: Si vous êtes courte sur la bande passante de réplication Notez que :
    - **ExpressRoute**: récupération de Site fonctionne avec Azure ExpressRoute et WAN optimiseurs comme Riverbed. [Pour en savoir plus](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) sur les ExpressRoute.
    - **Le trafic de réplication**: récupération de Site utilise effectue une réplication initiale active en utilisant uniquement des blocs de données et pas le disque dur virtuel entière. Seules les modifications sont répliquées lors de la réplication en cours.
    - **Le trafic réseau**: vous pouvez contrôler le trafic réseau utilisé pour la réplication en définissant la [Qualité de service Windows](https://technet.microsoft.com/library/hh967468.aspx) avec une stratégie basée sur l’adresse IP de destination et le port.  En outre, si vous êtes réplication vers Azure récupération de Site à l’aide de l’agent de sauvegarde Azure vous pouvez configurer la limitation pour que l’agent. [Pour en savoir plus](https://support.microsoft.com/kb/3056159).
- **RTO**: mesurer la récupération heure objectifs de que vous pouvez attendre avec récupération de Site, nous vous recommandons vous exécutez un basculement de test et afficher les tâches de récupération de Site pour analyser combien fois qu’elle permet d’accéder à effectuer les opérations. Si vous êtes basculement vers Azure, le meilleur RTO nous recommandons automatiser toutes les actions manuelles grâce à l’intégration avec Azure plans automation et de récupération.
- **RPO**: récupération de Site prend en charge une récupération près synchrone point l’objectif lors de la réplication vers Azure. Cela suppose suffisamment de bande passante entre votre centre de données et Azure.


##<a name="service-urls"></a>URL du service
Vérifiez que ces URL est accessibles à partir du serveur


**URL** | **Gestionnaire de mémoire virtuelle VMM** | **Gestionnaire de mémoire virtuelle Azure** | **Site Hyper-V vers Azure** | **VMware pour Azure**
---|---|---|---|---
 \*. accesscontrol.windows.net | Accès requis  | Accès requis  | Accès requis  | Accès requis
 \*. backup.windowsazure.com |  | Accès requis  | Accès requis  | Accès requis
 \*. hypervrecoverymanager.windowsazure.com | Accès requis  | Accès requis  | Accès requis  | Accès requis
 \*. store.core.windows.net | Accès requis  | Accès requis  | Accès requis  | Accès requis
 \*. blob.core.windows.net |  | Accès requis  | Accès requis  | Accès requis
 https://www.msftncsi.com/ncsi.txt | Accès requis  | Accès requis  | Accès requis  | Accès requis
 https://dev.MySQL.com/Get/archives/MySQL-5.5/MySQL-5.5.37-Win32.msi | | | | Accès requis


## <a name="next-steps"></a>Étapes suivantes

Après avoir d’apprentissage et de comparaison des exigences de déploiement général, vous pouvez lire les conditions préalables détaillées et commencer à déployer chaque scénario.

- [Répliquer machines virtuelles VMware sur Azure](site-recovery-vmware-to-azure-classic.md)
- [Dupliquer des serveurs physiques vers Azure](site-recovery-vmware-to-azure-classic.md)
- [Répliquer serveur Hyper-V dans nuages VMM vers Azure](site-recovery-vmm-to-azure.md)
- [Répliquer machines virtuelles de Hyper-V (sans VMM) vers Azure](site-recovery-hyper-v-site-to-azure.md)
- [Répliquer machines virtuelles Hyper-V sur un site secondaire](site-recovery-vmm-to-vmm.md)
- [Répliquer des ordinateurs virtuels Hyper-V sur un site secondaire avec SAN](site-recovery-vmm-san.md)
- [Répliquer les machines virtuelles Hyper-V avec un seul serveur VMM](site-recovery-single-vmm.md)
