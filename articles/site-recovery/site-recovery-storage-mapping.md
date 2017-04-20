<properties
    pageTitle="Mapper stockage dans Azure Site récupération pour la réplication de machine virtuelle Hyper-V entre centres de données locale | Microsoft Azure"
    description="Préparer les correspondances de stockage pour la réplication machine virtuelle Hyper-V entre deux centres de données locales avec Azure Site récupération."
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
    ms.date="07/06/2016"
    ms.author="raynew"/>


# <a name="prepare-storage-mapping-for-hyper-v-virtual-machine-replication-between-two-on-premises-datacenters-with-azure-site-recovery"></a>Préparer les correspondances de stockage pour la réplication machine virtuelle Hyper-V entre deux centres de données locales avec Azure de récupération de Site


Récupération de Site Azure contribue à votre stratégie d’entreprise continuité et de reprise récupération (BCDR) en organiser réplication, basculement et la restauration des machines virtuelles et des serveurs physiques. Cet article décrit le mappage de stockage, ce qui permet de vous rendre optimale utilise de stockage lorsque vous utilisez récupération de Site pour répliquer des ordinateurs virtuels Hyper-V entre centres de données VMM deux locaux.

Publier des commentaires ou des questions en bas de cet article, ou sur le [Forum de Services de récupération Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Vue d’ensemble

Mappage d’espace de stockage est pertinente uniquement lorsque vous êtes réplication machines virtuelles Hyper-V qui se trouvent dans nuages VMM à partir d’un centre de données principal à un centre de données secondaire à l’aide de la réplication SAN ou réplica Hyper-V, comme suit :


- **Local à la réplication en local avec réplica Hyper-V)** : Vous configurez stockage mappage par mappage classifications de stockage sur une source et VMM serveurs ciblez pour effectuer les opérations suivantes :

    - **Stockage de cible identifier des machines virtuelles réplica**— machines virtuelles seront copiées à une cible de stockage (PME partager ou cluster volumes partagés (CSVs)) que vous choisissez.
    - **Placement des machines virtuelles réplica**— stockage mappage est utilisé pour placer optimale machines virtuelles réplica sur vos serveurs hôtes Hyper-V. Machines virtuelles réplica sera placés sur des hôtes qui peuvent accéder à la classification de stockage mappé.
    - **Aucun mappage de stockage**— si vous ne configurez mappage de stockage, machines virtuelles seront répliquées vers l’emplacement de stockage par défaut spécifié sur le serveur hôte Hyper-V associé à la machine virtuelle réplica.

- **Local à la réplication en local avec SAN**: vous avez configuré stockage mappage par mappage pools matrices de stockage sur une source et VMM serveurs cibles.
    - **Spécifier pool**— Spécifie le pool de stockage secondaire reçoit les données de réplication du pool principal.
    - **Identifier les pools de stockage cible**— garantit que LUN dans un groupe de réplication source est répliquées au pool de stockage cible mappé de votre choix.

## <a name="set-up-storage-classifications-for-hyper-v-replication"></a>Configurer les classifications de stockage de la réplication Hyper-V

Lorsque vous utilisez Hyper-V réplica répliquer avec récupération de Site, vous mappez entre les classifications de stockage sur des serveurs VMM source et cible, ou sur un seul serveur VMM si deux sites sont gérés par le même serveur VMM. Notez que :

- Lorsque le mappage est configuré correctement et la réplication est activée, disque dur une machine virtuelle à l’emplacement principal seront répliquée vers le stockage dans l’emplacement cible mappé.
- Classifications de stockage doivent être disponibles pour les groupes hôte situés dans des nuages sources et cibles.
- Classifications n’êtes pas obligé d’avoir le même type de stockage. Par exemple, vous pouvez mapper une classification de source qui contient des partages de PME une classification cible qui contient CSVs.
- En savoir plus sur la [façon de créer des classifications de stockage dans VMM](https://technet.microsoft.com/library/gg610685.aspx).

## <a name="example"></a>Exemple

Si les classifications sont correctement configurées dans VMM lorsque vous sélectionnez source et cible serveur VMM lors du mappage de stockage, les classifications des sources et cibles seront affiche. Voici un exemple de partages de fichiers de stockage et de classifications pour une organisation avec deux emplacements à New York et Chicago.

**Emplacement** | **Serveur VMM** | **Partage de fichiers (source)** | **Classification (source)** | **Mappées** | **Partage de fichiers (cible)**
---|---|--- |---|---|---
Saint-Étienne | VMM_Source| SourceShare1 | OR | GOLD_TARGET | TargetShare1
 |  | SourceShare2 | ARGENT | SILVER_TARGET | TargetShare2
 | | SourceShare3 | BRONZE | BRONZE_TARGET | TargetShare3
Chicago | VMM_Target |  | GOLD_TARGET | Non mappés |
| | | SILVER_TARGET | Non mappés |
 | | | BRONZE_TARGET | Non mappés

Vous pouvez configurer ces sous l’onglet **Stockage de serveur** dans la page de **ressources** du portail récupération de Site.

![Configurer le mappage de stockage](./media/site-recovery-storage-mapping/storage-mapping1.png)

Dans cet exemple :
- Lorsqu’une une machine virtuelle réplica est créée pour toutes les machines virtuelles sur le stockage or (SourceShare1), il sera répliquée à un stockage GOLD_TARGET (TargetShare1).
- Lorsqu’une machine virtuelle réplica est créée pour toutes les machines virtuelles sur le stockage argent (SourceShare2), il sera répliquée à un stockage SILVER_TARGET (TargetShare2) et ainsi de suite.

Les partages de fichiers réel et leurs classifications affectées dans VMM apparaissent dans la capture d’écran suivante.

![Classifications de stockage dans VMM](./media/site-recovery-storage-mapping/storage-mapping2.png)

## <a name="multiple-storage-locations"></a>Plusieurs emplacements de stockage

Si la classification cible est affectée à plusieurs actions PME ou CSVs, l’emplacement de stockage optimal est sélectionné automatiquement lorsque la machine virtuelle est protégée. Si aucun espace de stockage cible appropriée n’est disponible avec le classement spécifié, l’emplacement de stockage par défaut spécifié sur l’hôte Hyper-V est utilisé pour placer les disques durs virtuels réplica.

Le tableau suivant affiche comment classification de stockage et volumes partagés en cluster sont configurés dans notre exemple.

**Emplacement** | **Classification** | **Stockage associé**
---|---|---
Saint-Étienne | OR | <p>C:\ClusterStorage\SourceVolume1</p><p>\\FileServer\SourceShare1</p>
 | ARGENT | <p>C:\ClusterStorage\SourceVolume2</p><p>\\FileServer\SourceShare2</p>
Chicago | GOLD_TARGET | <p>C:\ClusterStorage\TargetVolume1</p><p>\\FileServer\TargetShare1</p>
 | SILVER_TARGET| <p>C:\ClusterStorage\TargetVolume2</p><p>\\FileServer\TargetShare2</p>

Le tableau suivant résume le comportement lorsque vous activez la protection des machines virtuelles (VM1 - VM5) dans cet exemple d’environnement.

**Machine virtuelle** | **Stockage source** | **Classification de source** | **Stockage cibles mappés**
---|---|---|---
VM1 | C:\ClusterStorage\SourceVolume1 | OR | <p>C:\ClusterStorage\SourceVolume1</p><p>\\\FileServer\SourceShare1</p><p>Les deux GOLD_TARGET</p>
ORDINATEUR VIRTUEL 2 | \\FileServer\SourceShare1 | OR | <p>C:\ClusterStorage\SourceVolume1</p><p>\\FileServer\SourceShare1</p> <p>Les deux GOLD_TARGET</p>
VM3 | C:\ClusterStorage\SourceVolume2 | ARGENT | <p>C:\ClusterStorage\SourceVolume2</p><p>\FileServer\SourceShare2</p>
VM4 | \FileServer\SourceShare2 | ARGENT |<p>C:\ClusterStorage\SourceVolume2</p><p>\\FileServer\SourceShare2</p><p>Les deux SILVER_TARGET</p>
VM5 | C:\ClusterStorage\SourceVolume3 | N/A | Aucun mappage, pour l’emplacement de stockage par défaut de l’hôte Hyper-V est utilisé

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez une meilleure compréhension de mappage de stockage, [Préparez-vous à déployer récupération de Site Azure](site-recovery-best-practices.md).
