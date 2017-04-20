<properties
    pageTitle="Test de performances et les proportions de résultats pour local à la réplication de Hyper-V en local avec récupération de Site | Microsoft Azure"
    description="Cet article fournit des informations sur les performances des tests pour local à la réplication en local à l’aide de la récupération de Site Azure."
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
    ms.date="07/06/2016"
    ms.author="raynew"/>

# <a name="performance-test-and-scale-results-for-on-premises-to-on-premises-hyper-v-replication-with-site-recovery"></a>Performances tester et échelle des résultats pour local à la réplication de Hyper-V en local avec récupération de Site

Vous pouvez utiliser Microsoft Azure Site récupération pour organiser et gérer la réplication de machines virtuelles et des serveurs physiques à Azure ou à un centre de données secondaire. Cet article fournit les résultats des tests de performances que nous l’avons fait lors de la réplication des ordinateurs virtuels Hyper-V entre deux locaux centres de données.



## <a name="overview"></a>Vue d’ensemble

L’objectif des tests était pour examiner comment Azure Site récupération effectue lors de la réplication de l’état stable. État stable réplication se produit lorsque machines virtuelles ont terminé la réplication initiale et synchronisez les modifications delta. Il est important de mesurer les performances à l’aide de l’état stable car il s’agit de l’état dans lequel la plupart des machines virtuelles restent à moins que des pannes inattendues se produisent.


Le déploiement de test composé de deux sites en local avec un serveur VMM dans chaque site. Ce déploiement de test est caractéristique d’un déploiement de tête office/succursales, dont le siège social agissant en tant que le site principal et la succursale que le site secondaire ou la récupération.

### <a name="what-we-did"></a>Ce que nous l’avons fait

Voici ce que nous dans le test de passer :

1. Créé des machines virtuelles à l’aide de modèles VMM.

1. Mise en route des machines virtuelles et indicateurs de performance de référence capture sur 12 heures.

1. Nuages créés sur des serveurs VMM principaux et de restauration.

1. Protection de cloud configuré dans Azure Site récupération, y compris le mappage de nuages rognée source et de restauration.

1. Activé la protection des machines virtuelles et leur permettre de réplication initiale complet.

1. Attendu quelques heures pour une stabilisation système.

1. Capturé supérieure à 12 heures, les indicateurs de performance s’assurer que toutes les machines virtuelles est resté dans un état de réplication prévue pour ces 12 heures.

1. Mesurez le delta entre les mesures de performances de référence et les mesures de performances de réplication.

## <a name="test-deployment-results"></a>Résultats de déploiement de test

### <a name="primary-server-performance"></a>Performances du serveur principal

- Hyper-V réplica asynchrone effectue le suivi des modifications dans un fichier journal avec une charge minimale de stockage sur le serveur principal.

- Réplica Hyper-V utilise la mise à jour automatique mémoire cache pour réduire le sorties par surcharge d’effectuer le suivi. Il stocke écrit dans la VHDX en mémoire et les vide dans le fichier journal avant de l’heure auxquelles le journal est envoyé sur le site de récupération. Un disque est aligné à se trouve également si les opérations d’écriture atteint une limite prédéfinie.

- Le graphique ci-dessous présente la charge sorties par état stable de la réplication. Nous pouvons voir que les sorties par aérienne en raison de la réplication est environ 5 % qui est relativement faible.

![Résultats principaux](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744913.png)

Réplica Hyper-V utilise la mémoire sur le serveur principal pour optimiser les performances de disque. Comme indiqué dans le graphique suivant, mémoire aérienne sur tous les serveurs dans le cluster principal est moyenne, La mémoire aérienne indiquée est le pourcentage de mémoire utilisée par la réplication par rapport à la mémoire totale installée sur le serveur Hyper-V.

![Résultats principaux](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744914.png)

Réplica Hyper-V a une charge du processeur minimale. Comme indiqué dans le graphique, surcharge de réplication est dans la plage de 2-3 %.

![Résultats principaux](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744915.png)

### <a name="secondary-recovery-server-performance"></a>Performances du serveur secondaire (récupération)

Réplica Hyper-V utilise une petite quantité de mémoire sur le serveur de récupération pour optimiser le nombre d’opérations de stockage. Le graphique résume l’utilisation de la mémoire sur le serveur de récupération. La mémoire aérienne indiquée est le pourcentage de mémoire utilisée par la réplication par rapport à la mémoire totale installée sur le serveur Hyper-V.

![Résultats secondaires](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744916.png)

La quantité d’opérations e/s sur le site de récupération est une fonction du nombre d’opérations d’écriture sur le site principal. Nous allons examiner les opérations d’e/s total sur le site de récupération attributs les totaux des opérations d’e/s et écrire des opérations sur le site principal. Les graphiques indiquent le total des sorties par sur le site de récupération est

- Environ 1,5 fois l’écriture sorties par sur le serveur principal.

- Environ 37 % de l’ES/s total sur le site principal.

![Résultats secondaires](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744917.png)

![Résultats secondaires](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744918.png)

### <a name="effect-of-replication-on-network-utilization"></a>Effet de la réplication sur l’utilisation du réseau

Moyenne de 275 Mo par seconde de bande passante réseau a été utilisée par rapport à une bande passante existante de 5 Go par seconde entre les nœuds principaux et de restauration (avec la compression activée).

![Résultats de l’utilisation du réseau](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744919.png)

### <a name="effect-of-replication-on-virtual-machine-performance"></a>Effet de la réplication sur les performances des machines virtuelles

Prendre en considération est l’impact de la réplication sur les charges de travail en cours d’exécution sur les ordinateurs virtuels. Si le site principal est correctement configuré pour la réplication, les charges de travail ne doit pas être d’aucun impact. Lightweight du réplica Hyper-V mécanisme de suivi garantit que les charges de travail en cours d’exécution sur les ordinateurs virtuels ne sont pas affectés lors de la réplication état stable. Ceci est illustré dans les graphiques suivants.

Ce graphique présente des sorties par effectué par des machines virtuelles en cours d’exécution différentes charges de travail avant et après que la réplication a été activée. Vous pouvez observer ce qu’il n’existe aucune différence entre les deux.

![Résultats d’effet réplica](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744920.png)

Le graphique suivant montre le débit des machines virtuelles exécutées différentes charges de travail avant et après que la réplication a été activée. Vous pouvez observer ce que la réplication n’a aucun impact significatif.

![Effets de réplica de résultats](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744921.png)

### <a name="conclusion"></a>Conclusion

Les résultats montrent clairement nuances de récupération de Site Azure, associée à réplica Hyper-V, bien avec minimum aérienne pour un grand cluster.  Récupération de Site Azure fournit déploiement simple, de réplication, de gestion et de suivi. Réplica Hyper-V fournit l’infrastructure nécessaire pour suivre l’évolution de réplication réussie. Pour planifier un déploiement optimal nous suggérer que vous téléchargez le [Planificateur de capacité réplica Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057).

## <a name="test-environment-details"></a>Détails de l’environnement de test

### <a name="primary-site"></a>Site principal

- Le site principal a un cluster contenant cinq serveurs Hyper-V 470 machines virtuelles en cours d’exécution.

- Machines virtuelles exécuter différentes charges de travail et leur protection récupération de Site Azure activée.

- Espace de stockage pour le nœud de cluster est fournie par un SAN iSCSI. Modèle – Hitachi HUS130.

- Chaque cluster comporte quatre réseau cartes de Go/une s chaque.

- Deux cartes réseau sont connectés à un réseau privé iSCSI et deux sont connectés à un réseau d’entreprise externes. Un des réseaux externes est réservé aux communications cluster uniquement.

![Configuration matérielle principal](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744922.png)

|Serveur|RAM|Modèle|Processeur|Nombre de processeurs|CARTE RÉSEAU|Logiciel|
|---|---|---|---|---|---|---|
|Serveurs Hyper-V cluster : <br />ESTLAB HOST11<br />ESTLAB HOST12<br />ESTLAB HOST13<br />ESTLAB HOST14<br />ESTLAB HOST25|128ESTLAB HOST25 comporte 256|Dell™ PowerEdge™ R820|Intel Xeon processeur E5-4620 0 @ 2,20 GHz|4|J’ai Go/s x 4|Centre de données Windows Server 2012 R2 (x64) + rôle Hyper-V|
|Serveur VMM|2|||2|1 Go/s|Windows Server 2012 de la base de données R2 (x 64) + VMM 2012 R2|

### <a name="secondary-recovery-site"></a>Site secondaire (récupération)

- Le site secondaire nécessite un cluster de basculement six nœuds.

- Espace de stockage pour le nœud de cluster est fournie par un SAN iSCSI. Modèle – Hitachi HUS130.

![Configuration matérielle principal](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744923.png)

|Serveur|RAM|Modèle|Processeur|Nombre de processeurs|CARTE RÉSEAU|Logiciel|
|---|---|---|---|---|---|---|
|Serveurs Hyper-V cluster : <br />ESTLAB HOST07<br />ESTLAB HOST08<br />ESTLAB HOST09<br />ESTLAB HOST10|96|Dell™ PowerEdge™ R720|Intel Xeon processeur E5-2630 0 @ 2,30 GHz|2|J’ai Go/s x 4|Centre de données Windows Server 2012 R2 (x64) + rôle Hyper-V|
|ESTLAB HOST17|128|Dell™ PowerEdge™ R820|Intel Xeon processeur E5-4620 0 @ 2,20 GHz|4||Centre de données Windows Server 2012 R2 (x64) + rôle Hyper-V|
|ESTLAB HOST24|256|Dell™ PowerEdge™ R820|Intel Xeon processeur E5-4620 0 @ 2,20 GHz|2||Centre de données Windows Server 2012 R2 (x64) + rôle Hyper-V|
|Serveur VMM|2|||2|1 Go/s|Windows Server 2012 de la base de données R2 (x 64) + VMM 2012 R2|

### <a name="server-workloads"></a>Charges de travail

- À des fins de test nous avons choisi charges de travail fréquemment utilisées dans les scénarios client d’entreprise.

- Nous utilisons [IOMeter](http://www.iometer.org) avec les caractéristiques de la charge de travail indiquées dans le tableau pour la simulation.

- Tous les profils IOMeter sont définis pour écrire des modèles pour les charges de travail d’écriture octets aléatoires pour simuler le pire.

|Charge de travail|Taille d’e (Ko)|% Access|% Lu|E/s en attente|Modèle d’e/s|
|---|---|---|---|---|---|
|Serveur de fichiers|48163264|60 20 %5 %5 % 10 %|80 % 80 % 80 % 80 % 80 %|88888|Toutes les 100 % aléatoires|
|SQL Server (volume 1) SQL Server (volume 2)|864|100 % 100 %|70 %0 %|88|100 % random100 % séquentiel|
|Exchange|32|100 %|67 %|8|100 % aléatoires|
|Poste de travail/VDI|464|66 % 34 %|95 70 %|11|Les deux aléatoire 100 %|
|Serveur de fichiers Web|4864|33 % 34 % 33 %|95 % de 95 95 %|888|Tous les 75 % aléatoire|

### <a name="virtual-machine-configuration"></a>Configuration de machine virtuelle

- machines virtuelles 470 sur le cluster principal.

- Tous les ordinateurs virtuels avec disque VHDX.

- Machines virtuelles charges de travail indiquées dans le tableau en cours d’exécution. Toutes ont été créées avec les modèles VMM.

|Charge de travail|# Machines virtuelles|RAM minimum (Go)|RAM maximal (Go)|Taille du disque logique (Go) par machine virtuelle|Sorties par maximale|
|---|---|---|---|---|---|
|SQL Server|51|1|4|167|10|
|Exchange Server|71|1|4|552|10|
|Serveur de fichiers|50|1|2|552|22|
|VDI|149|.5|1|80|6|
|Serveur Web|149|.5|1|80|6|
|TOTAL|470|||96.83 TO|4108|

### <a name="azure-site-recovery-settings"></a>Paramètres de récupération de Site Azure

- Azure récupération de Site a été configurée pour local sur protection en local

- Le serveur VMM comporte quatre nuages configurés, contenant les serveurs de cluster Hyper-V et leurs machines virtuelles.

|Nuage VMM principal|Machines virtuelles protégées dans le cloud|Fréquence de réplication|Points de récupération supplémentaires|
|---|---|---|---|
|PrimaryCloudRpo15m|142|15 minutes|Aucun|
|PrimaryCloudRpo30s|47|30 secondes|Aucun|
|PrimaryCloudRpo30sArp1|47|30 secondes|1|
|PrimaryCloudRpo5m|235|5 minutes|Aucun|

### <a name="performance-metrics"></a>Indicateurs de performance

Le tableau récapitule les mesures de performances et les compteurs qui ont été mesurées dans le déploiement.

|Métrique|Compteur|
|---|---|
|PROCESSEUR|\Processor(_Total)\% temps processeur|
|Quantité de mémoire disponible|\Memory\Available Mo|
|SORTIES PAR|\PhysicalDisk (_Total) \Disk transferts/s|
|Machine virtuelle opérations de lecture (sorties par) / s|Dispositif de stockage virtuel \Hyper-V (<VHD>) \Read opérations/s|
|Opérations d’écriture (sorties par) machine virtuelle/s|Dispositif de stockage virtuel \Hyper-V (<VHD>) \Write opérations/S|
|Machine virtuelle lire débit|Dispositif de stockage virtuel \Hyper-V (<VHD>) \Read octets/s|
|Débit d’écriture machine virtuelle|Dispositif de stockage virtuel \Hyper-V (<VHD>) \Write octets/s|


## <a name="next-steps"></a>Étapes suivantes

- [Configurer la protection entre deux sites VMM en local](site-recovery-vmm-to-vmm.md)
