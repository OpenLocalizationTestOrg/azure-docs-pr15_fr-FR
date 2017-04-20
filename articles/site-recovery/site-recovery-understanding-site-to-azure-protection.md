<properties
    pageTitle="Réplication Hyper-V avec récupération de Site Azure | Microsoft Azure"
    description="Utilisez cet article pour comprendre les concepts techniques qui vous aideront à installer, configurer et gérer la récupération de Site Azure."
    services="site-recovery"
    documentationCenter=""
    authors="Rajani-Janaki-Ram"
    manager="mkjain"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="09/12/2016"
    ms.author="rajanaki"/>  


# <a name="hyper-v-replication-with-azure-site-recovery"></a>Réplication Hyper-V avec récupération de Site Azure

Cet article décrit les concepts techniques qui peuvent vous aider à configurer et gérer un site Hyper-V ou sur un site de System Center Virtual Machine Manager (VMM) protection par Azure à l’aide de récupération de Site Azure.

## <a name="setting-up-the-source-environment-for-replication-between-on-premises-and-azure"></a>La configuration de l’environnement source pour la réplication entre en local et Azure

Dans le cadre de la configuration de sinistre entre en local et Azure, veillez à télécharger et installer le fournisseur de récupération de Site Azure sur le serveur VMM. Installer un Agent de Services de récupération Azure sur chaque hôte Hyper-V.

![Déploiement d’un site VMM pour la réplication entre en local et Azure](media/site-recovery-understanding-site-to-azure-protection/image00.png)

La configuration de l’environnement source dans une infrastructure Hyper-V gérée est similaire à la configuration de l’environnement source dans une infrastructure gérée VMM. La seule différence est que le fournisseur et l’agent sont installés sur l’hôte Hyper-V lui-même. Dans l’environnement VMM, le fournisseur est installé sur le serveur VMM et l’agent est installé sur les hôtes Hyper-V (en cas de la réplication Azure).

## <a name="workflows"></a>Flux de travail

### <a name="enable-protection"></a>Activer la protection
Une fois que vous protégez une machine virtuelle à partir du portail Azure ou en local, une opération de récupération de Site nommée **Activer la protection** démarre. Vous pouvez surveiller sous l’onglet **tâches** .

![Travail « Activer la protection » dans la liste des tâches](media/site-recovery-understanding-site-to-azure-protection/image001.PNG)

Le travail **d’Activer la protection** vérifie les conditions préalables avant d’appeler la méthode [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) . Cette méthode crée la réplication Azure à l’aide des entrées qui sont configurées pendant la protection.

Le travail **d’Activer la protection** lance la réplication initiale locales en appelant la méthode [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) . Cette méthode envoie disques virtuels de la machine virtuelle à Azure.

![Détails de la tâche « Activer la protection »](media/site-recovery-understanding-site-to-azure-protection/IMAGE002.PNG)

### <a name="finalize-protection-on-the-virtual-machine"></a>Finalisation de la protection de la machine virtuelle
Un [instantané d’ordinateur virtuel Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) est considérée au déclenchement de la réplication initiale. Disques durs virtuels sont traités un par un jusqu'à ce que tous les disques sont téléchargés vers Azure. Cette procédure prend généralement un certain temps, en fonction de la taille du disque et la bande passante. Pour optimiser votre utilisation du réseau, voir [comment gérer local pour l’utilisation de la bande passante réseau protection Azure](https://support.microsoft.com/kb/3056159).

Une fois la réplication initiale terminée, la tâche de **protection Finalize sur l’ordinateur virtuel** configure les paramètres réseau et après la réplication. Pendant la réplication initiale :

- Toutes les modifications apportées aux disques sont suivies. 
- Plus d’espace disque est utilisé pour la capture instantanée et les fichiers journaux de réplica Hyper-V (HRL).

À la fin de la réplication initiale, l’instantané d’ordinateur virtuel Hyper-V est supprimé. Suppression de ce produit comme résultat de la fusion des modifications apportées aux données après réplication initiale sur le disque parent.

![Travail « Finaliser la protection de la machine virtuelle » dans la liste des tâches](media/site-recovery-understanding-site-to-azure-protection/image03.png)

### <a name="delta-replication"></a>Réplication delta
Hyper-V réplica réplication suivi fait partie du moteur Hyper-V réplica réplication, pistes les modifications à un disque dur virtuel en tant que fichiers journaux réplica Hyper-V (*.hrl). Fichiers HRL se trouvent dans le même répertoire que les disques associés.

Chaque disque est configuré pour la réplication a un fichier HRL associé. Ce journal est envoyé au compte de stockage du client après que la réplication initiale est terminée. Lorsque le journal est en cours de transfert vers Azure, les modifications dans le serveur principal sont suivies dans un autre fichier journal dans le même répertoire.

Au cours de la réplication initiale ou delta, vous pouvez surveiller l’intégrité du réplication de machine virtuelle dans l’affichage de la machine virtuelle, comme indiqué dans [surveiller l’intégrité du réplication pour machine virtuelle](./site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machine).  

### <a name="resynchronization"></a>Resynchronisation
Une machine virtuelle est marquée pour la resynchronisation lors du Échec de la réplication delta et de réplication initiale complet est coûteuse en termes de bande passante réseau ou une heure. Par exemple, lorsque la taille du fichier HRL accumule jusqu'à 50 % de la taille totale du disque, la machine virtuelle est marquée pour la resynchronisation. Resynchronisation permet de réduire la quantité de données envoyées sur le réseau en informatique les totaux de contrôle des disques machine virtuelle sources et cibles et en envoyant uniquement l’écart.

Une fois resynchronisation terminée, réplication delta normal doit reprendre. Vous pouvez reprendre resynchronisation si une panne de réseau ou un autre panne se produit.

Par défaut, planifiée automatiquement resynchronisation est configurée pour se produire en dehors des heures de travail. Si la machine virtuelle doit être resynchroniser manuellement, sélectionnez la machine virtuelle à partir du portail et cliquez sur **resynchroniser**.

![Resynchronisation manuelle](media/site-recovery-understanding-site-to-azure-protection/image04.png)

Resynchronisation utilise un algorithme de segmentation blocs fixes où les fichiers sources et cibles sont divisées en segments fixes. Les totaux de contrôle pour chaque segment est générées et ensuite comparés afin de déterminer qui se bloque à partir de la source doit être appliqués à la cible.

### <a name="retry-logic"></a>Logique de nouvelle tentative
Il est logique de relance intégrée pour des erreurs de réplication. Cette logique peut être classée selon deux catégories :

| Catégorie                  | Scénarios                                    |
|---------------------------|----------------------------------------------|
| Erreur non récupérable     | Aucune nouvelle ne tentative. État de la réplication machine virtuelle est **critique**et intervention de l’administrateur est requis. Voici quelques exemples : <ul><li>Rompre la chaîne disque dur virtuel</li><li>État non valide pour la machine virtuelle réplica</li><li>Erreur d’authentification réseau</li><li>Erreur d’autorisation</li><li>Machine virtuelle qui n’est pas trouvée, dans le cas d’un serveur autonome Hyper-V</li></ul>|
| Erreur récupérable         | Nouvelles tentatives produisent chaque intervalle de réplication, à l’aide un exponentielle recul qui augmente l’intervalle recommencer depuis le début de la première tentative (1, 2, 4, 8, 10 minutes). Si un message d’erreur persiste, recommencez toutes les 30 minutes. Voici quelques exemples : <ul><li>Erreur réseau</li><li>Manque d’espace disque</li><li>Condition de la mémoire</li></ul>|

## <a name="hyper-v-virtual-machine-protection-and-recovery-life-cycle"></a>Cycle de vie de protection et de restauration machine virtuelle Hyper-V

![Cycle de vie de protection et de restauration machine virtuelle Hyper-V](media/site-recovery-understanding-site-to-azure-protection/image05.png)

## <a name="other-references"></a>Autres références

- [Surveiller et résoudre les problèmes de protection pour les sites Hyper-V et physiques VMware, VMM,](./site-recovery-monitoring-and-troubleshooting.md)
- [Un public plus large du Support Microsoft](./site-recovery-monitoring-and-troubleshooting.md#reaching-out-for-microsoft-support)
- [Erreurs de récupération de Site Azure courantes et leurs solutions](./site-recovery-monitoring-and-troubleshooting.md#common-asr-errors-and-their-resolutions)
