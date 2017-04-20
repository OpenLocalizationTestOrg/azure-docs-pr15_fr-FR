<properties
    pageTitle="Comment fonctionne la récupération de Site ? | Microsoft Azure"
    description="Cet article fournit une vue d’ensemble de l’architecture de récupération de Site"
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
    ms.topic="get-started-article"
    ms.date="10/05/2016"
    ms.author="raynew"/>

# <a name="how-does-azure-site-recovery-work"></a>Comment fonctionne la récupération de Site Azure ?

Lisez cet article pour mieux comprendre l’architecture sous-jacente du service de récupération de Site Azure et les composants qui le rendent fonctionnent. 

Publier des commentaires ou des questions en bas de cet article, ou sur le [Forum de Services de récupération Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="overview"></a>Vue d’ensemble

Les organisations doivent une stratégie BCDR qui détermine la façon applications, les charges de travail et les données restent en cours d’exécution et disponible au cours des interruptions liées aux tâches planifiées et et récupérer à des conditions de travail normal dès que possible. Votre stratégie BCDR doit conserver les données métiers approuvés et peuvent être restaurées et vous assurer que les charges de travail restent disponibles en permanence cas d’urgence. 

Récupération de site est un service Azure qui contribue à votre stratégie de BCDR en organiser réplication des serveurs physiques en local et machines virtuelles dans le cloud (Azure) ou un centre de données secondaire. Lorsque des défaillances se trouvent dans votre emplacement principal, vous basculer vers l’emplacement secondaire afin que les applications et les charges de travail disponibles. Vous ne parvenez pas à votre emplacement principal lorsqu’elle retourne à la normale. Pour plus d’informations [Nouveautés récupération de Site ?](site-recovery-overview.md)

## <a name="site-recovery-in-the-azure-portal"></a>Récupération de site dans le portail Azure

Azure comporte deux différents [modèles de déploiement](../resource-manager-deployment-model.md) pour la création et utilisation des ressources : le modèle Azure le Gestionnaire de ressources et le modèle de gestion des services classique. Azure également comporte deux portails – le [portail classique Azure](https://manage.windowsazure.com/) qui prend en charge le modèle de déploiement classique, le [portail Azure](https://portal.azure.com) avec prise en charge pour les deux modèles de déploiement.

Récupération de site est disponible dans le portail classique et le portail Azure. Dans le portail classique Azure vous pouvez prendre en charge de récupération de Site avec le modèle de gestion des services classique. Dans le portail Azure vous pouvez prendre en charge le modèle classique ou les déploiements de modèle de ressources. [Pour en savoir plus](site-recovery-overview.md#site-recovery-in-the-azure-portal) sur le déploiement grâce au portail Azure.

Les informations contenues dans cet article s’applique aux déploiements portails classiques et Azure. Différences sont indiquées le cas échéant.

## <a name="deployment-scenarios"></a>Scénarios de déploiement

Récupération de site peut être déployée pour organiser la réplication dans un certain nombre de scénarios :

- **Machines virtuelles VMware répliquer**: vous pouvez répliquer des machines virtuelles VMware local vers Azure ou à un centre de données secondaire.
- - **Répliquer machines physiques**: vous pouvez répliquer machines physiques exécutant Windows ou Linux vers Azure ou d’un centre de données secondaire. Le processus de réplication des machines physiques est presque la même que le processus de réplication des machines virtuelles VMware
- **Dupliquer des machines virtuelles Hyper-V (sans VMM)**: vous pouvez répliquer les machines virtuelles Hyper-V qui ne sont pas gérés par le Gestionnaire de mémoire virtuelle Azure.
- **Dupliquer des ordinateurs virtuels Hyper-V géré dans System Center VMM nuages**: vous pouvez répliquer machines virtuelles de locaux Hyper-V en cours d’exécution sur des serveurs de hôte Hyper-V en nuages VMM à Azure ou à un centre de données secondaire. Vous pouvez répliquer standard réplica Hyper-V et l’utilisation de la réplication SAN.
- **Machines virtuelles migrer**: vous pouvez utiliser la récupération de Site pour [migrer Azure IaaS machines virtuelles](site-recovery-migrate-azure-to-azure.md) entre les régions, ou pour [migrer des instances AWS Windows](site-recovery-migrate-aws-to-azure.md) aux machines virtuelles IaaS Azure. Migration actuellement uniquement est prise en charge ce qui signifie que vous pouvez basculer vers ces ordinateurs virtuels, mais vous ne pouvez pas les restaurées.

Récupération de site peut répliquer la plupart des applications s’exécutant sur ces machines virtuelles et des serveurs physiques. Vous pouvez obtenir un plein résumé des applications pris en charge dans [pouvez récupération de Site Azure protéger les charges de travail ?](site-recovery-workload.md)


## <a name="replicate-to-azure-vmware-virtual-machines-or-physical-windowslinux-servers"></a>Réplication sur Azure : VMware machines virtuelles physique Windows/serveurs ou Linux

Il existe plusieurs façons de répliquer les machines virtuelles VMware avec récupération de Site.

- **À l’aide du portail Azure**-lorsque vous déployez récupération de Site dans le portail Azure, vous pouvez basculer machines virtuelles au stockage de gestionnaire de service classique ou au Gestionnaire de ressources. Réplication des machines virtuelles VMware dans le portail Azure offre de nombreux avantages, y compris la possibilité de répliquer vers classique ou le Gestionnaire de ressources stockage dans Azure. [En savoir plus](site-recovery-vmware-to-azure.md).
- **À l’aide du portail classique**-vous pouvez déployer récupération de Site dans le portail classique à l’aide d’une expérience améliorée. Il doit être utilisé pour tous les nouveaux déploiements dans le portail classique. Dans ce déploiement vous pouvez uniquement basculer machines virtuelles au stockage classique dans Azure et non vers le Gestionnaire de ressources de stockage. [En savoir plus](site-recovery-vmware-to-azure-classic.md). Il existe également une [expérience héritée](site-recovery-vmware-to-azure-classic-legacy.md) de configuration de la réplication VMware dans le portail classique. Cela ne doivent pas être utilisé pour les nouveaux déploiements.  Si vous avez déjà déployé à l’aide de l’expérience héritées [en savoir plus sur la migration](site-recovery-vmware-to-azure-classic-legacy.md#migrate-to-the-enhanced-deployment) au déploiement amélioré.



Les exigences architecturales pour le déploiement de récupération de Site pour répliquer serveurs VMware machines virtuelles/physique dans le portail Azure ou le portail classique Azure (amélioré) sont similaires, avec quelques différences :

- Si vous déployez dans le portail Azure, vous pouvez répliquer au stockage basé sur le Gestionnaire de ressources et utiliser le Gestionnaire de ressources réseaux pour connecter les ordinateurs virtuels Azure après le basculement.
- Lorsque vous déployez dans le portail Azure les deux LRS et stockage GRS est pris en charge. Dans le portail classique GRS est requis.
- Le processus de déploiement est simplifié et plus convivial dans le portail Azure.


Voici ce que vous devez :

- **Compte Azure**: vous devez disposer d’un compte Microsoft Azure.
- **Stockage Azure**: vous devez posséder un compte de stockage Azure pour stocker les données dupliquées. Vous pouvez utiliser un compte classique ou un compte de stockage du Gestionnaire de ressources. Le compte peut être LRS ou GRS lorsque vous déployez dans le portail Azure. Données répliquées sont stockées dans le stockage Azure et machines virtuelles Azure sont empilés des cas de basculement. 
- **Azure réseau**: vous devez un réseau virtuel Azure qui machines virtuelles Azure se connecteront à lorsqu’elles sont créées à basculement. Dans le portail Azure elles peuvent être créés dans le modèle de gestionnaire de service classique ou dans le modèle de gestionnaire de ressources de réseaux.
- **Serveur de configuration local**: vous avez besoin d’un ordinateur Windows Server 2012 R2 local qui s’exécute le serveur de configuration et d’autres composants de récupération de Site. Si vous êtes réplication machines virtuelles VMware ce doit être une VM VMware hautement disponible. Si vous voulez répliquer serveurs physiques l’ordinateur peut être physique. Ces composants de récupération de Site seront installés sur l’ordinateur :
    - **Serveur de configuration**: coordonnées communication entre votre environnement local et Azure et gère la réplication des données et restauration.
    - **Serveur de traitement**: fonctionne comme une passerelle de réplication. Il reçoit les données de réplication à partir d’ordinateurs source protégée, optimise avec la mise en cache, de compression et de chiffrement et envoie les données au stockage Azure. Il gère installation push du service mobilité à machines protégées et vous effectue la détection automatique des machines virtuelles VMware. Fur et à votre déploiement vous pouvez ajouter des serveurs supplémentaires processus dédié distinct pour gérer l’augmentation du volume de trafic de réplication.
    - **Serveur cible maître**: gère les données de la réplication lors du retour arrière à partir d’Azure. 
- **Machines virtuelles VMware ou serveurs physiques répliquer**: chaque ordinateur sur lequel vous souhaitez répliquer vers Azure devront du composant de service mobilité. Ce service capture écritures de données sur l’ordinateur et les transmet au serveur de processus. Ce composant peut être installé manuellement, ou peut être poussé, automatiquement par le serveur de processus lorsque vous activez la réplication pour une machine.
- **vSPhere hosts/vCenter server**: vous devez une ou plusieurs vSphere héberger des serveurs exécutant machines virtuelles VMware. Nous vous recommandons de déployer un serveur de vCenter pour gérer ces hôtes.
- **Retour arrière**: Voici ce que vous devez :
    - **Retour arrière vers physique n’est pas pris en charge**: cela signifie que si vous basculez sur des serveurs physiques vers Azure et que vous voulez faire rebasculer, vous devez basculer vers un VMware VM. Vous ne pouvez pas restauré sur un serveur physique. Vous devez une machine virtuelle Azure basculer sur et si vous n’avez pas déployez le serveur de configuration comme un VMware VM vous devez configurer un serveur cible maître distinct comme un VMware VM. Cela est nécessaire, car le serveur cible maître interagit et joint au stockage VMware afin de restaurer les disques à une VMware VM.
    - - **Serveur de traitement temporaire dans Azure**: Si vous voulez basculer revenir à partir d’Azure après le basculement, vous devez définir une machine virtuelle Azure configuré comme un serveur de traitement pour gérer la réplication à partir d’Azure. Vous pouvez supprimer cette machine virtuelle une fois terminée retour arrière.
    - **Connexion VPN**: de la restauration, vous devez une connexion VPN (ou Azure ExpressRoute) configurer à partir du réseau Azure au site local.
    - **Locaux distincte maîtres serveur cible**: le serveur de base cible local gère retour arrière. Le serveur cible maître est installé par défaut sur le serveur de gestion, mais si vous êtes ignore revenir plus grands volumes de trafic vous devez configurer un serveur de base cible distincte en local à cet effet.

**Architecture générale**

![Amélioré](./media/site-recovery-components/arch-enhanced.png)

**Composants de déploiement**

![Amélioré](./media/site-recovery-components/arch-enhanced2.png)

**Retour arrière**

![Retour arrière améliorée](./media/site-recovery-components/enhanced-failback.png)


- [En savoir plus](site-recovery-vmware-to-azure.md#azure-prerequisites) sur la configuration requise pour le déploiement du portail Azure.
- [En savoir plus](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) sur la configuration requise de déploiement améliorée dans le portail classique.
- [En savoir plus](site-recovery-failback-azure-to-vmware.md) sur retour arrière dans le portail Auzre.
- [En savoir plus](site-recovery-failback-azure-to-vmware-clas- [Learn more](site-recovery-failback-azure-to-vmware-classic.md) about failback in the Auzre portal.sic.md) sur retour arrière dans le portail classique.

## <a name="replicate-to-azure-hyper-v-vms-not-managed-by-vmm"></a>Réplication sur Azure : machines virtuelles Hyper-V ne pas gérés par VMM

Vous pouvez répliquer machines virtuelles Hyper-V qui ne sont pas gérés par système Centre Gestionnaire de mémoire virtuelle Azure avec récupération de Site comme suit :

- **À l’aide du portail Azure**-lorsque vous déployez récupération de Site dans le portail Azure, vous pouvez basculer machines virtuelles au stockage classique ou au Gestionnaire de ressources. [En savoir plus](site-recovery-hyper-v-site-to-azure.md).
- **À l’aide du portail classique**-vous pouvez déployer récupération de Site dans le portail classique. Dans ce déploiement vous pouvez uniquement basculer machines virtuelles au stockage classique dans Azure et non vers le Gestionnaire de ressources de stockage. [En savoir plus](site-recovery-hyper-v-site-to-azure-classic.md).

L’architecture pour les déploiements est similaire, sauf que :

- Si vous déployez dans le portail Azure, vous pouvez répliquer vers le Gestionnaire de ressources stockage et utiliser le Gestionnaire de ressources réseaux pour connecter les ordinateurs virtuels Azure après le basculement.
- Le processus de déploiement est simplifié et plus convivial dans le portail Azure.

Voici ce que vous devez :

- **Compte Azure**: vous devez disposer d’un compte Microsoft Azure.
- **Stockage Azure**: vous devez posséder un compte de stockage Azure pour stocker les données dupliquées. Dans le portail Azure, vous pouvez utiliser un compte classique ou un compte de stockage du Gestionnaire de ressources. Dans le portail classique, vous pouvez utiliser un compte classique uniquement. Données répliquées sont stockées dans le stockage Azure et Azure machines virtuelles sont créées lors du basculement.
- **Azure réseau**: vous devez un réseau Azure qui machines virtuelles Azure se connecteront à lorsqu’elles sont créées après le basculement. 
- **Hôte Hyper-v**: vous devez au moins un serveur hôte de Windows Server 2012 R2 Hyper-V. Au cours du déploiement de récupération de Site que vous allez installer le fournisseur de récupération de Site Azure et de l’agent de Services de récupération de Microsoft Azure sur l’hôte.
- **Machines virtuelles Hyper-V**: vous devez un ou plusieurs ordinateurs virtuels sur le serveur hôte Hyper-V. Azure fournisseur de récupération de Site et de l’agent de Services de récupération Azure sur l’hôte Hyper-V pendant le déploiement de récupération de Site. Le fournisseur de coordonnées et orchestre réplication avec le service de récupération de Site via internet. L’agent gère les données de réplication de données sur HTTPS 443. Communications à partir du fournisseur et l’agent sont sécurisés et chiffrés. Les données dupliquées dans le stockage Azure sont également chiffrées.

**Architecture générale**

![Site Hyper-V vers Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)


- [En savoir plus](site-recovery-hyper-v-site-to-azure.md#azure-prerequisites) sur la configuration requise pour le déploiement du portail Azure.
- [En savoir plus](site-recovery-hyper-v-site-to-azure-classic.md#azure-prerequisites) sur la configuration requise pour le déploiement du portail classique.



## <a name="replicate-to-azure-hyper-v-vms-managed-by-vmm"></a>Réplication sur Azure : machines virtuelles Hyper-V gérés par VMM

Vous pouvez répliquer machines virtuelles Hyper-V dans nuages VMM sur Azure avec récupération de Site comme suit :

- **À l’aide du portail Azure**-lorsque vous déployez récupération de Site dans le portail Azure, vous pouvez basculer machines virtuelles au stockage classique ou au Gestionnaire de ressources. [En savoir plus](site-recovery-vmm-to-azure.md).
- **À l’aide du portail classique**-vous pouvez déployer récupération de Site dans le portail classique. Dans ce déploiement vous pouvez uniquement basculer machines virtuelles au stockage classique dans Azure et non vers le Gestionnaire de ressources de stockage. [En savoir plus](site-recovery-vmm-to-azure-classic.md).

L’architecture pour les déploiements est similaire, sauf que :

- Si vous déployez dans le portail Azure, vous pouvez répliquer au stockage basé sur le Gestionnaire de ressources et utiliser le Gestionnaire de ressources réseaux pour connecter les ordinateurs virtuels Azure après le basculement.
- Le processus de déploiement est simplifié et plus convivial dans le portail Azure.


Voici ce que vous devez :

- **Compte Azure**: vous devez disposer d’un compte Microsoft Azure.
- **Stockage Azure**: vous devez posséder un compte de stockage Azure pour stocker les données dupliquées. Dans le portail Azure, vous pouvez utiliser un compte classique ou un compte de stockage du Gestionnaire de ressources. Dans le portail classique, vous pouvez utiliser un compte classique uniquement. Données répliquées sont stockées dans le stockage Azure et Azure machines virtuelles sont créées lors du basculement.
- **Azure réseau**: vous devez configurer réseau mappage afin que les ordinateurs virtuels Azure sont connectés à des réseaux appropriées lorsqu’elles sont créées après le basculement. 
- **Serveur VMM**: vous devez besoin d’un ou plusieurs serveurs locale VMM s’exécutant sur System Center 2012 R2 et configuré avec un ou plusieurs nuages privés. Si vous déployez dans le Azure portail vous aurez besoin logique et réseaux machine virtuelle configurer de sorte que vous pouvez configurer le mappage réseau. Dans le portail classique cette étape est facultative.  Un réseau machine virtuelle doit être lié à un réseau logique associée le cloud.
- **Hôte Hyper-v**: vous devez un ou plusieurs des serveurs hôte Windows Server 2012 R2 Hyper-V dans le nuage VMM.
- **Machines virtuelles Hyper-V**: vous devez un ou plusieurs ordinateurs virtuels sur le serveur hôte Hyper-V.

**Architecture générale**

![Gestionnaire de mémoire virtuelle Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

- [En savoir plus](site-recovery-vmm-to-azure.md#azure-requirements) sur la configuration requise pour le déploiement du portail Azure.
- [En savoir plus](site-recovery-vmm-to-azure-classic.md#before-you-start) sur la configuration requise pour le déploiement du portail classique.




## <a name="replicate-to-a-secondary-site-vmware-virtual-machines-or-physical-servers"></a>Réplication sur un site secondaire : VMware machines virtuelles ou des serveurs physiques 

Pour répliquer machines virtuelles VMware ou serveurs physiques vers un site secondaire en téléchargement InMage Scout qui est inclus dans l’abonnement de récupération de Site Azure. Il peut être téléchargé à partir du portail Azure ou à partir du portail classique Azure. 

Vous configurez les serveurs de composants dans chaque site (configuration, processus, maître cible) et installez l’Agent unifiée sur les ordinateurs que vous souhaitez répliquer. Après la réplication initiale l’agent sur chaque ordinateur envoie les modifications de réplication delta au serveur de processus. Le serveur de traitement optimise les données et les transfère au serveur cible maître sur le site secondaire. Le serveur de configuration gère le processus de réplication.

Voici ce dont vous avez besoin :

**Compte Azure**: vous déployez ce scénario à l’aide de InMage Scout. Pour les obtenir, vous devez un abonnement Azure. Après avoir créé un archivage sécurisé récupération de Site que vous téléchargez InMage Scout et installez les dernières mises à jour pour configurer le déploiement.
**Serveur de traitement (site principal)**: configurer le composant de serveur de processus dans votre site principal pour gérer la mise en cache, de compression et optimisation des données. Il gère également installation push de l’Agent unifiée aux machines à protéger. 
**VMware ESX/ESXi et vCenter server (site principal)**: Si vous êtes protection des machines virtuelles VMware vous devez un hyperviseur VMware équivalente de l’objet/ESXi et éventuellement un serveur vCenter VMware pour gérer les hyperviseurs.
- **Machines virtuelles/physique serveurs (site principal)**: machines virtuelles VMware ou Windows/Linux serveurs physiques à protéger will doivent l’Agent unifiée est installé. L’Agent unifiée est également installé sur les ordinateurs agissant en tant que le serveur maître cible. L’agent se comporte comme un fournisseur de communication entre tous les composants. 
- - **Serveur de configuration (site secondaire)**: le serveur de configuration est le premier composant que vous installez, et il est installé sur le site secondaire pour gérer, configurer et surveiller votre déploiement, soit à l’aide du site Web de gestion ou de la console vContinuum. Il est uniquement un serveur de configuration unique dans un déploiement et il doit être installé sur un ordinateur exécutant Windows Server 2012 R2.
- **vContinuum serveur (site secondaire)**: il est installé au même endroit (site secondaire) en tant que le serveur de configuration. Il fournit une console de gestion et de surveillance votre environnement protégé. Dans une installation par défaut le serveur vContinuum est le premier serveur de base cible et a installé l’Agent unifiée.
- **Serveur cible (site secondaire) maître**: le serveur cible maître conserve les données répliquées. Il reçoit des données à partir du serveur de processus, crée une machine réplica dans le site secondaire et conserve les points de rétention des données. Le nombre de serveurs maître cible que vous avez besoin dépend du nombre d’ordinateurs que vous êtes protection. Si vous souhaitez basculer sur le site principal vous devez trop il un serveur principal cible. 

**Architecture générale**

![VMware pour VMware](./media/site-recovery-components/vmware-to-vmware.png)


## <a name="replicate-to-a-secondary-site-hyper-v-vms-managed-by-vmm"></a>Réplication sur un site secondaire : machines virtuelles Hyper-V géré par VMM


Vous pouvez répliquer machines virtuelles Hyper-V qui sont gérés par System Center VMM sur un centre de données secondaire avec récupération de Site comme suit :

- **À l’aide du portail Azure**-lorsque vous déployez récupération de Site dans le portail Azure. [En savoir plus](site-recovery-hyper-v-site-to-azure.md).
- **À l’aide du portail classique**-vous pouvez déployer récupération de Site dans le portail classique. [En savoir plus](site-recovery-hyper-v-site-to-azure-classic.md).

L’architecture pour les déploiements est similaire, sauf que :

- Si vous déployez dans le portail Azure, que vous devez configurer mappage réseau. Cette étape est facultative dans le portail classique.
- Le processus de déploiement est simplifié et plus convivial dans le portail Azure.
- - Si vous déployez dans le Azure portail classique [mappage de stockage](site-recovery-storage-mapping.md) est disponible.

Voici ce que vous devez :

- **Compte Azure**: vous devez disposer d’un compte Microsoft Azure.
- **Serveur VMM**: nous vous recommandons d’un serveur VMM dans le site principal et l’autre dans le site secondaire, chacun contenant au moins un cloud privé VMM. Le serveur doit être en cours d’exécution au moins System Center 2012 SP1 avec les dernières mises à jour et connecté à internet. Nuages doivent avoir le profil de fonctionnalité Hyper-V à définir. Vous devez installer le fournisseur de récupération Azure Site sur le serveur VMM. Le fournisseur de coordonnées et orchestre réplication avec le service de récupération de Site via internet. Communications entre le fournisseur et Azure sont sécurisés et chiffrés.
- **Serveur Hyper-V**: serveurs hôtes Hyper-V doivent se trouver dans les nuages VMM principales et secondaires. L’hôte de serveurs doivent s’exécuter au moins Windows Server 2012 avec les dernières mises à jour installé et connecté à internet. Données sont répliquées entre les serveurs hôtes Hyper-V principales et secondaires sur le réseau local ou VPN à l’aide de l’authentification Kerberos ou certificat.  
- **Machines protégé**: le serveur hôte source Hyper-V doit avoir au moins une machine virtuelle que vous voulez protéger.

**Architecture générale**

![Local vers local](./media/site-recovery-components/arch-onprem-onprem.png)


- [En savoir plus](site-recovery-vmm-to-vmm.md#azure-prerequisites) sur la configuration requise de déploiement dans le portail Azure.
- - [En savoir plus](site-recovery-vmm-to-vmm-classic.md#before-you-start) sur la configuration requise de déploiement dans le portail classique Azure.




## <a name="replicate-to-a-secondary-site-with-san-replication-hyper-v-vms-managed-by-vmm"></a>Réplication sur un site secondaire avec réplication SAN : machines virtuelles Hyper-V géré par VMM

Vous pouvez répliquer machines virtuelles Hyper-V géré dans nuages VMM sur un site secondaire à l’aide de la réplication SAN à l’aide du portail classique Azure. Ce scénario n’est pas actuellement pris en charge dans le nouveau portail Azure. 

Dans ce scénario pendant le déploiement de récupération de Site que vous devez installer le fournisseur de récupération de Site Azure sur des serveurs VMM. Le fournisseur de coordonnées et orchestre réplication avec le service de récupération de Site via internet. Données sont répliquées entre les tableaux de stockage principaux et secondaires à l’aide de la réplication SAN synchrone.

Voici ce que vous devez :

**Compte Azure**: vous avez besoin d’un abonnement Azure
- **Tableau de SAN**: une [prise en charge de la matrice SAN](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx) gérés par le serveur VMM principal. Le SAN partage une infrastructure réseau avec un autre tableau SAN dans le site secondaire.
- **Serveur VMM**: nous vous recommandons d’un serveur VMM dans le site principal et l’autre dans le site secondaire, chacun contenant au moins un cloud privé VMM. Le serveur doit être en cours d’exécution au moins System Center 2012 SP1 avec les dernières mises à jour et connecté à internet. Nuages doivent avoir le profil de fonctionnalité Hyper-V à définir.
- **Serveur Hyper-V**: serveurs hôtes Hyper-V situés dans les nuages VMM principales et secondaires. L’hôte de serveurs doivent s’exécuter au moins Windows Server 2012 avec les dernières mises à jour installé et connecté à internet.
- **Machines protégé**: le serveur hôte source Hyper-V doit avoir au moins une machine virtuelle que vous voulez protéger.

**Architecture de réplication SAN**

![Réplication SAN](./media/site-recovery-components/arch-onprem-onprem-san.png)

[En savoir plus](site-recovery-vmm-san.md#before-you-start) sur la configuration requise de déploiement.
### <a name="on-premises"></a>En local



## <a name="hyper-v-protection-lifecycle"></a>Cycle de vie protection Hyper-V

Ce flux de travail montre le processus de protection, réplication et basculement d’ordinateurs virtuels Hyper-V. 

1. **Activer la protection**: vous configurez l’archivage sécurisé récupération de Site, configurer les paramètres de réplication pour un nuage VMM ou un site de Hyper-V et activer la protection pour les machines virtuelles. Un travail appelé **Activer la Protection** est établie et peut être contrôlé dans l’onglet **tâches** . La tâche vérifie que l’ordinateur est conforme aux conditions préalables et puis appelle la méthode [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) qui configure la réplication Azure avec les paramètres que vous avez configuré. Le travail **d’Activer la protection** appelle également la méthode [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) pour initialisation une réplication machine virtuelle complète.
2. **Réplication initiale**: un instantané de machine virtuelle et des disques durs virtuels sont répliquées un par un jusqu'à ce qu’ils sont tous copiés sur Azure ou au centre de données secondaire. Le temps nécessaire pour terminer ce dépend de la taille de la mémoire virtuelle, la bande passante réseau et la méthode de réplication initial. Si les modifications du disque se produisent pendant la réplication initiale est en cours le suivi de réplication réplica Hyper-V effectue le suivi de ces modifications en tant que journaux de réplication Hyper-V (.hrl) qui se trouvent dans le même dossier que les disques. Chaque disque possède un fichier associé .hrl qui sera envoyé vers le stockage secondaire. Notez que les fichiers journaux et instantané consomment les ressources de disque alors que la réplication initiale est en cours. Une fois la réplication initiale l’instantané machine virtuelle est supprimée et les modifications de disque delta dans le journal sont synchronisées et fusionnées.
3. **Protection Finalize**: une fois la réplication initiale termine à la tâche de **protection Finalize** configure réseau et autres paramètres après la réplication afin que la machine virtuelle est protégée. Si vous êtes réplication vers Azure, vous devrez peut-être ajuster les paramètres de la machine virtuelle afin qu’il soit prêt pour le basculement. À ce stade, vous pouvez exécuter un basculement test pour vérifier que tout fonctionne comme prévu.
4. **Réplication**: après la réplication initiale commence la synchronisation delta, selon les paramètres de réplication. 
    - **Échec de la réplication**: si la réplication delta échoue et une réplication complète seraient coûteuse en termes de bande passante ou l’heure, puis resynchronisation se produit. Pour exemple si les fichiers .hrl atteignent 50 % de la taille du disque la machine virtuelle est marqué pour resynchronisation. Resynchronisation permet de réduire la quantité de données envoyées en informatique les totaux de contrôle des machines virtuelles sources et cibles et en envoyant uniquement le delta. Une fois terminée resynchronisation réplication delta va reprendre. Par défaut resynchronisation est planifiée pour s’exécuter automatiquement en dehors des heures de bureau, mais vous pouvez resynchroniser une machine virtuelle manuellement.
    - **Erreur de réplication**: si une erreur de réplication se présente une nouvelle tentative intégrée. Si elle est une erreur non récupérable comme une erreur d’authentification ou d’autorisation, ou une machine réplica est dans un état incorrect, puis aucune nouvelle tentative ne tente. Si elle est une erreur récupérable tel qu’une erreur de réseau ou d’espace disque faible/mémoire, puis une nouvelle tentative se produit avec augmentation des intervalles entre les tentatives (1, 2, 4, 8, 10 et puis toutes les 30 minutes).
4. **Basculement planifié/non planifiée**: vous pouvez exécuter basculement planifié ou selon vos besoins. Si vous exécutez un basculement planifié puis source machines virtuelles sont arrêtés pour éviter toute perte de données. Après la création de machines virtuelles réplica ils sont placés dans une validation état d’attente. Vous avez besoin valider les pour exécuter le basculement, sauf si vous êtes la réplication avec SAN auquel cas valider est automatique. Une fois que le site principal est en cours d’exécution retour arrière peut se produire. Si vous avez répliquées vers Azure inverse la réplication est automatique. Dans le cas contraire vous démarrez réplication inverse manuellement.
 

![flux de travail](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## <a name="next-steps"></a>Étapes suivantes

[Préparer pour le déploiement](site-recovery-best-practices.md)
