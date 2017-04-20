<properties
    pageTitle="Préparer le mappage de réseau pour la protection de machine virtuelle Hyper-V avec VMM dans Azure Site récupération | Microsoft Azure"
    description="Configurer la correspondance de réseau pour la réplication machine virtuelle Hyper-V à partir d’un centre de données locale pour Azure, ou vers un site secondaire."
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


# <a name="prepare-network-mapping-for-hyper-v-virtual-machine-protection-with-vmm-in-azure-site-recovery"></a>Préparer le mappage de réseau pour la protection de machine virtuelle Hyper-V avec VMM dans récupération de Site Azure

Récupération de Site Azure contribue à votre stratégie d’entreprise continuité et de reprise récupération (BCDR) en organiser réplication, basculement et la restauration des machines virtuelles et des serveurs physiques.

Cet article décrit le mappage réseau, qui vous permet de paramètres réseau configurer façon optimale lorsque vous utilisez récupération de Site pour répliquer des ordinateurs virtuels Hyper-V situé dans nuages VMM entre deux centres de données locales ou un centre de données en local et Azure. Notez que si vous êtes la réplication machines virtuelles Hyper-V sans un nuage VMM ou réplication machines virtuelles VMware ou serveurs physiques, cet article n’est pas pertinent.

Publier des commentaires ou des questions en bas de cet article, ou sur le [Forum de Services de récupération Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="overview"></a>Vue d’ensemble

Le mappage réseau est utilisé lors de la récupération de Site Azure est déployée à répliquer machines virtuelles Hyper-V à Azure ou à un centre de données secondaire, à l’aide de la réplication SAN ou réplica Hyper-V.

- **Machines virtuelles de réplication Hyper-V dans nuages VMM entre deux locaux centres de données**— réseau cartes mappage entre réseaux machine virtuelle sur un serveur VMM source et machine virtuelle sur un serveur VMM cible, procédez comme suit :

    - **Machines virtuelles de se connecter après le basculement**— garantit que machines virtuelles sera connectés aux réseaux appropriés après le basculement. La machine virtuelle réplica est connectée au réseau cible qui est mappé au réseau source.
    - **Machines virtuelles place réplica serveurs hôtes**— optimale placer machines virtuelles réplica sur vos serveurs hôtes Hyper-V. Machines virtuelles réplica sera placés sur hôtes pouvant accéder les réseaux machine virtuelle mappés.
    - **Aucun mappage réseau**— si vous ne configurez mappage réseau, machines virtuelles répliquées n’est pas connectés à un réseau machine virtuelle après le basculement.

- **Machines virtuelles de réplication Hyper-V dans un VMM locaux cloud vers Azure**: réseau de cartes de mappage entre les réseaux machine virtuelle sur le serveur VMM source et cible réseaux Azure pour effectuer les opérations suivantes :
    - **Connecter des machines virtuelles après le basculement**— tous les ordinateurs où le basculement sur le même réseau peut se connecter à d’autres, quel que soit le plan de récupération elles se trouvent dans.
    - **Passerelle réseau**— si une passerelle réseau a été configurée sur la cible du réseau Azure, machines virtuelles peuvent se connecter aux autres machines virtuelles en local.
    - **Aucun mappage réseau**— si vous ne configurez mappage réseau, uniquement des machines virtuelles que le basculement dans le même plan de récupération pourront se connecter à l’autre après fail sur vers Azure.


## <a name="network-mapping-example"></a>Exemple de mappage réseau

Mappage du réseau peut être configuré entre les réseaux machine virtuelle sur deux serveurs VMM ou sur un seul serveur VMM si deux sites sont gérés par le même serveur. Lorsque le mappage est configuré correctement et la réplication est activée, une machine virtuelle à l’emplacement principal sera connectée à un réseau et son réplica à l’emplacement cible sera connecté à son réseau mappé.

Si les réseaux ont été correctement configurés dans VMM, lorsque vous sélectionnez un réseau machine virtuelle cible lors du mappage réseau, les nuages VMM source qui utilisent le réseau machine virtuelle source seront affichera, ainsi que les réseaux de machine virtuelle cible disponible sur les nuages cible qui sont utilisés pour la protection.

Voici un exemple pour illustrer ce mécanisme. Prenons une organisation avec deux emplacements à New York et Chicago.

**Emplacement** | **Serveur VMM** | **Réseaux de machine virtuelle** | **Mappées**
---|---|---|---
Saint-Étienne | VMM NewYork| VMNetwork1 NewYork | Mappées VMNetwork1 Chicago
 |  | VMNetwork2 NewYork | Non mappés
Chicago | VMM Chicago| VMNetwork1 Chicago | Mappées VMNetwork1 NewYork
 | | VMNetwork2 Chicago | Non mappés

Dans cet exemple :

- Lorsqu’une machine virtuelle réplica est créée pour n’importe quel ordinateur virtuel qui est connecté à VMNetwork1 NewYork, il sera connecté à VMNetwork1 Chicago.
- Lorsqu’une machine virtuelle réplica est créée pour VMNetwork2 NewYork ou VMNetwork2 Chicago, il ne pourra pas connecter à un réseau.

Voici comment nuages VMM sont configurés dans notre exemple d’entreprise et les réseaux logiques associés aux nuages.

### <a name="cloud-protection-settings"></a>Paramètres de protection du cloud

**Cloud protégée** | **Protection de cloud** | **Réseau logique (New York)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>NA</p><p></p> | <p>LogicalNetwork1 NewYork</p><p>LogicalNetwork1 Chicago</p>
SilverCloud2 | <p>NA</p><p></p> | <p>LogicalNetwork1 NewYork</p><p>LogicalNetwork1 Chicago</p>

### <a name="logical-and-vm-network-settings"></a>Paramètres du réseau logique et machine virtuelle

**Emplacement** | **Réseau logique** | **Réseau machine virtuelle associé**
---|---|---
Saint-Étienne | LogicalNetwork1 NewYork | VMNetwork1 NewYork
Chicago | LogicalNetwork1 Chicago | VMNetwork1 Chicago
 | LogicalNetwork2Chicago | VMNetwork2 Chicago

### <a name="target-networks"></a>Réseaux cible

En fonction de ces paramètres, lorsque vous sélectionnez le réseau machine virtuelle cible, le tableau suivant répertorie les choix qui seront disponibles.

**Sélectionnez** | **Cloud protégée** | **Protection de cloud** | **Réseau cible disponible**
---|---|---|---
VMNetwork1 Chicago | SilverCloud1 | SilverCloud2 | Disponible
 | GoldCloud1 | GoldCloud2 | Disponible
VMNetwork2 Chicago | SilverCloud1 | SilverCloud2 | Non disponible
 | GoldCloud1 | GoldCloud2 | Disponible



## <a name="multiple-subnets"></a>Plusieurs sous réseaux

Si le réseau cible comporte plusieurs sous réseaux de ces sous-réseaux ait le même nom que le sous-réseau sur lequel se trouve l’ordinateur virtuel source, la machine virtuelle réplica est connectée à ce sous-réseau cible après le basculement. S’il n’existe aucun sous-réseau cible ayant un nom correspondant, la machine virtuelle est connectée au premier sous-réseau dans le réseau.


### <a name="failback"></a>Retour arrière

Pour voir ce qui se produit dans le cas de retour arrière (réplication inverse), supposons que VMNetwork1 NewYork est mappé à VMNetwork1-Chicago, avec les paramètres suivants.


**Machine virtuelle** | **Connecté au réseau de machine virtuelle**
---|---
VM1 | VMNetwork1 réseau
Ordinateur virtuel 2 (réplica de VM1) | VMNetwork1 Chicago

Avec ces paramètres, passons en revue ce qui se passe en quelques scénarios possibles.

**Scénario** | **Résultat**
---|---
Aucune modification dans les propriétés du réseau de machine virtuelle 2 après le basculement. | Machine virtuelle-1 reste connecté au réseau source.
Propriétés du réseau de machine virtuelle-2 sont modifiées après le basculement et est déconnecté. | Machine virtuelle-1 est déconnecté.
Propriétés du réseau de machine virtuelle-2 sont modifiées après le basculement et est connecté à VMNetwork2 Chicago. | Si VMNetwork2 Chicago n’est pas mappé, machine virtuelle-1 est déconnecté.
Mappage réseau de VMNetwork1 Chicago est modifié. | Machine virtuelle-1 sera connecté au réseau maintenant mappé à VMNetwork1 Chicago.


## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez une meilleure compréhension de mappage réseau, [prise en main déploiement de la récupération de Site](site-recovery-best-practices.md).
