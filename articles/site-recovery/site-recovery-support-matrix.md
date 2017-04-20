<properties
    pageTitle="Matrice de support de récupération de Site Azure | Microsoft Azure"
    description="Résume les systèmes d’exploitation pris en charge et les composants Azure récupération de Site"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/05/2016"
    ms.author="raynew"/>

# <a name="azure-site-recovery-support-matrix"></a>Matrice de support Azure récupération de Site

Cet article présente les systèmes d’exploitation pris en charge et des composants pour les déploiements de récupération de Site Azure. Liste des composants pris en charge et les conditions préalables n’est disponible pour chaque scénario de déploiement de chaque l’article déploiement correspondant et ce document résume les.

## <a name="supported-operating-systems-for-virtualization-servers"></a>Systèmes d’exploitation pris en charge pour les serveurs de virtualisation


**Source** | **Cible** | **Système d’exploitation hôte**
---|---|---|--- 
**Hôtes Hyper-V (sans VMM)** | Azure | Windows Server 2012 R2 avec les dernières mises à jour
**Hôtes Hyper-V (avec VMM)** | Azure | Windows Server 2012 R2 avec les dernières mises à jour
**Hôtes Hyper-V (avec VMM)** | Site VMM secondaire | Au minimum Windows Server 2012 avec les dernières mises à jour
**VMware hosts/vCenter** | Azure | vCenter 5.5 ou 6.0 (prise en charge des 5,5 fonctionnalités uniquement) <br/><br/> vSphere 6.0, 5.5 ou 5.1 avec les dernières mises à jour
**VMware hosts/vCenter** | Site VMware secondaire | vCenter 5.5 ou 6.0 (prise en charge des 5,5 fonctionnalités uniquement) <br/><br/> vSphere 6.0, 5.5 ou 5.1 avec les dernières mises à jour

## <a name="supported-requirements-for-replicated-machines"></a>Configuration requise pris en charge pour les ordinateurs répliquées

**Source** | **Qu’est répliqué** | **Cible** | **Système d’exploitation hôte**
---|---|---|--- 
**Machines virtuelles Hyper-V** | Les charges de travail | Azure | N’importe quel système d’exploitation invité [pris en charge par Azure](https://technet.microsoft.com/library/cc794868.aspx)<br/><br/> Machines virtuelles doivent répondre aux [exigences Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**Machines virtuelles Hyper-V (avec VMM)** | Les charges de travail | Azure | N’importe quel système d’exploitation invité [pris en charge par Azure](https://technet.microsoft.com/library/cc794868.aspx)<br/><br/> Machines virtuelles doivent répondre aux [exigences Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**Machines virtuelles Hyper-V (avec VMM)** | Les charges de travail | Site VMM secondaire | N’importe quel système d’exploitation invité [pris en charge par Hyper-V](https://technet.microsoft.com/library/mt126277.aspx)
**Machines virtuelles VMware** | Les charges de travail en cours d’exécution sur machine virtuelle Windows | Azure | 64 bits Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 avec au moins SP1<br/><br/> Machines virtuelles doivent répondre aux [exigences Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**Machines virtuelles VMware** | Les charges de travail en cours d’exécution sur Linux VM | Azure | Chapeau rouge Enterprise Linux 6,7, 7.1, 7.2 <br/><br/> CentOS 6.5, 6.6, 6,7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 exécutant le noyau compatible chapeau rouge ou un Unbreakable entreprise noyau version 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Espace de stockage requis : NFS (EXT3, ETX4, ReiserFS, XFS) ; Chemins multiples-périphérique logiciel Mapper (chemins multiples)) ; Gestionnaire de volume : (LVM2). Serveurs physiques avec stockage du contrôleur HP CCISS ne sont pas pris en charge. Le système de fichiers ReiserFS est pris en charge uniquement sur SUSE Linux Enterprise Server 11 SP3.<br/><br/> Machines virtuelles doivent répondre aux [exigences Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**Machines virtuelles VMware** | Les charges de travail en cours d’exécution sur machine virtuelle Windows | Site VMware secondaire | 64 bits Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 avec au moins SP1
**Machines virtuelles VMware** | Les charges de travail en cours d’exécution sur Linux VM | Site VMware secondaire | Chapeau rouge Enterprise Linux 6,7, 7.1, 7.2 <br/><br/> CentOS 6.5, 6.6, 6,7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 exécutant le noyau compatible chapeau rouge ou un Unbreakable entreprise noyau version 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Espace de stockage requis : NFS (EXT3, ETX4, ReiserFS, XFS) ; Chemins multiples-périphérique logiciel Mapper (chemins multiples)) ; Gestionnaire de volume : (LVM2). Serveurs physiques avec stockage du contrôleur HP CCISS ne sont pas pris en charge. Le système de fichiers ReiserFS est pris en charge uniquement sur SUSE Linux Enterprise Server 11 SP3.
**Serveurs physiques** | Les charges de travail en cours d’exécution sur Windows | Azure | 64 bits Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 avec au moins SP1
**Serveurs physiques** | Les charges de travail en cours d’exécution sur Linux | Azure | Chapeau rouge Enterprise Linux 6.7,7.1,7.2 <br/><br/> CentOS 6.5, 6.6,6.7,7.0,7.1,7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 exécutant le noyau compatible chapeau rouge ou un Unbreakable entreprise noyau version 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Espace de stockage requis : NFS (EXT3, ETX4, ReiserFS, XFS) ; Chemins multiples-périphérique logiciel Mapper (chemins multiples)) ; Gestionnaire de volume : (LVM2). Serveurs physiques avec stockage du contrôleur HP CCISS ne sont pas pris en charge. Le système de fichiers ReiserFS est pris en charge uniquement sur SUSE Linux Enterprise Server 11 SP3.
**Serveurs physiques** | Les charges de travail en cours d’exécution sur Windows | Site secondaire | 64 bits Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 avec au moins SP1
**Serveurs physiques** | Les charges de travail en cours d’exécution sur Linux | Site secondaire | Chapeau rouge Enterprise Linux 6.7,7.1,7.2 <br/><br/> CentOS 6.5, 6.6,6.7,7.0,7.1,7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 exécutant le noyau compatible chapeau rouge ou un Unbreakable entreprise noyau version 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Espace de stockage requis : NFS (EXT3, ETX4, ReiserFS, XFS) ; Chemins multiples-périphérique logiciel Mapper (chemins multiples)) ; Gestionnaire de volume : (LVM2). Serveurs physiques avec stockage du contrôleur HP CCISS ne sont pas pris en charge. Le système de fichiers ReiserFS est pris en charge uniquement sur SUSE Linux Enterprise Server 11 SP3.


## <a name="provider-versions"></a>Versions de fournisseur

**Nom** | **Description** | **Version la plus récente** | **Prise en charge** | **Plus d’informations**
---|---|---|---| ---
**Fournisseur de récupération de Site Azure** | Communications coordonnées entre serveurs en local et un site Azure/secondaire <br/><br/> Installé sur les serveurs VMM locaux ou serveurs Hyper-V si aucun serveur VMM | 5.1.1700 (disponible à partir du portail) | [Dernières fonctionnalités et solutions](https://support.microsoft.com/kb/3155002)
**Récupération de Site Azure unifiée le programme d’installation (VMware vers Azure)** | Communications coordonnées entre les serveurs VMware en local et Azure <br/><br/> Installé sur des serveurs VMware en local | 9.3.4246.1 (disponible à partir du portail) | [Dernières fonctionnalités et solutions](https://support.microsoft.com/kb/3155002)
**Service de mobilité** | Réplication de coordonnées entre les serveurs de serveurs/physique VMware en local et un site Azure/secondaire | NA (disponible à partir du portail) | Installé sur chaque VMware VM ou serveur physique que vous souhaitez répliquer. **Agent de Microsoft Azure récupération Services (MARS)** | Réplication de coordonnées entre machines virtuelles Hyper-V et Azure<br/><br/> Installés sur des serveurs locaux Hyper-V (avec ou sans un serveur VMM) | 2.0.8689.0 (disponible à partir du portail) | Cet agent est utilisé par Azure sauvegarde et restauration de Site de Azure). [En savoir plus] (https://support.microsoft.com/en-us/kb/2997692)

## <a name="next-steps"></a>Étapes suivantes

[Préparer pour le déploiement](site-recovery-best-practices.md)

