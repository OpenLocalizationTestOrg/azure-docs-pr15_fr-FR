<properties
   pageTitle="Conseils techniques : récupération locales vers Azure | Microsoft Azure"
   description="Article sur la présentation et de récupération de conception systèmes à partir de l’infrastructure en local vers Azure"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#<a name="azure-resiliency-technical-guidance-recovery-from-on-premises-to-azure"></a>Conseils techniques résilience Azure : récupération locales vers Azure

Azure propose un ensemble complet de services d’activation de l’extension d’un centre de données locale à Azure fins de haute disponibilité et urgence récupération :

* __Mise en réseau__: avec un réseau privé virtuel, étendre en toute sécurité de votre réseau local dans le cloud.
* __Calculer__: clients à l’aide de Hyper-V locaux peuvent « lever et MAJ » machines virtuelles existantes (machines virtuelles) pour Azure.
* __Stockage__: StorSimple étend votre système de fichiers au stockage Azure. Le service de sauvegarde Azure fournit sauvegarde des fichiers et des bases de données SQL pour le stockage Azure.
* __Réplication de base de données__: avec SQL Server 2014 (ou version ultérieure) groupes de disponibilité, vous pouvez implémenter haute disponibilité et récupération d’urgence pour vos données locales.

##<a name="networking"></a>Mise en réseau

Vous pouvez utiliser le réseau virtuel Azure pour créer une section isolée logiquement dans Azure et la connexion sécurisée à votre centre de données locale ou un seul ordinateur client à l’aide d’une connexion IPsec. Avec le réseau virtuel, vous pouvez tirer parti de l’infrastructure scalable, à la demande dans Azure lors de la connectivité à des données et applications en local, y compris les systèmes s’exécutant sur Windows Server, ordinateurs centraux et UNIX. Pour plus d’informations, consultez la rubrique [Azure réseau documentation](../virtual-network/virtual-networks-overview.md) .

##<a name="compute"></a>Cluster

Si vous utilisez Hyper-V en local, vous pouvez « lever et MAJ » existants machines virtuelles vers Azure et exécutant Windows Server 2012 (ou version ultérieure), sans apporter de modifications de la machine virtuelle ou en convertissant machine virtuelle met en forme des fournisseurs de services. Pour plus d’informations, voir [à propos des disques et des disques durs virtuels pour les machines virtuelles Azure](../virtual-machines/virtual-machines-linux-about-disks-vhds.md).

##<a name="azure-site-recovery"></a>Récupération de Site Azure

Si vous voulez sinistre en tant que service (DRaaS), Azure assure la [Restauration de Site Azure](https://azure.microsoft.com/services/site-recovery/). Récupération de Site Azure offre une protection complète des serveurs Hyper-V et physiques VMware. Avec Azure récupération de Site, vous pouvez utiliser un autre serveur local ou Azure que votre site de récupération. Pour plus d’informations sur la récupération de Site Azure, consultez la [documentation de récupération de Site Azure](https://azure.microsoft.com/documentation/services/site-recovery/).

##<a name="storage"></a>Espace de stockage

Il existe plusieurs options pour l’utilisation Azure comme site de sauvegarde de données locales.

###<a name="storsimple"></a>StorSimple

StorSimple en toute sécurité et en toute transparence intègre le stockage cloud pour les applications en local. Il propose également une solution unique qui offre High performance hiérarchisé local et stockage cloud, l’archivage live, protection des données sur le nuage et reprise. Pour plus d’informations, consultez la [page du produit StorSimple](https://azure.microsoft.com/services/storsimple/).

###<a name="azure-backup"></a>Sauvegarde Azure

Sauvegarde Azure sauvegardes nuage en utilisant les outils familiers de sauvegarde dans Windows Server 2012 (ou version ultérieures), Windows Server 2012 Essentials (ou version ultérieure) et System Center 2012 Data Protection Manager (ou version ultérieure). Ces outils fournissent un flux de travail pour la gestion de la sauvegarde qui dépend de l’emplacement de stockage des sauvegardes, si un disque local ou un stockage Azure. Une fois que les données sont sauvegardées sur le nuage, les utilisateurs autorisés peuvent récupérer facilement des sauvegardes à n’importe quel serveur.

Avec des sauvegardes incrémentielles, uniquement les modifications apportées aux fichiers sont transférées vers le cloud. Cela permet efficacement utiliser espace de stockage, réduire la consommation de bande passante et prend en charge de la récupération de point dans le temps de plusieurs versions des données. Vous pouvez également choisir d’utiliser des fonctionnalités supplémentaires, telles que les stratégies de rétention des données, la compression des données et la limitation de transfert de données. À l’aide d’Azure comme emplacement de sauvegarde a l’avantage évident que les sauvegardes sont automatiquement « hors site ». Cela supprime la configuration requise supplémentaire pour sécuriser et protéger les supports de sauvegarde sur site.

Pour plus d’informations, voir [Nouveautés sauvegarde Azure ?](../backup/backup-introduction-to-azure-backup.md) et [Configurer la sauvegarde Azure pour les données DPM](https://technet.microsoft.com/library/jj728752.aspx).

##<a name="database"></a>Base de données

Vous pouvez avoir une solution de récupération d’urgence pour vos bases de données SQL Server dans un environnement hybride informatique à l’aide de groupes de disponibilité AlwaysOn, la mise en miroir de base de données, d’envoi des journaux et sauvegarde et restauration du stockage d’objets Blob Azure. Toutes ces solutions utilisent SQL Server Azure machines virtuelles en fonctionnement en cours d’exécution.

Groupes de disponibilité AlwaysOn peuvent être utilisés dans un environnement hybride informatique où réplicas de base de données existent à la fois en local et dans le cloud. Celle-ci apparaît dans le diagramme suivant.

![Groupes de disponibilité AlwaysOn SQL Server dans une architecture de cloud hybride](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-3.png)

La mise en miroir de base de données peut également couvrir serveurs locale et du cloud dans une installation basée sur le certificat. Le diagramme suivant illustre ce concept.

![La mise en miroir de base de données SQL Server dans une architecture de cloud hybride](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-4.png)

Envoi des journaux peut être utilisé pour synchroniser une base de données locales avec une base de données SQL Server sur une machine virtuelle Azure.

![SQL Server envoi des journaux dans une architecture de cloud hybride](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-5.png)

Enfin, vous pouvez sauvegarder une base de données locale directement au stockage d’objets Blob Azure.

![Sauvegarder SQL Server à Azure Blob storage dans une architecture de cloud hybride](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-6.png)

Pour plus d’informations, voir [haute disponibilité et récupération après incident pour SQL Server dans Azure machines virtuelles](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md) et [sauvegarde et restauration pour SQL Server dans des machines virtuelles Azure](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md).

##<a name="checklists-for-on-premises-recovery-in-microsoft-azure"></a>Listes de vérification pour la récupération en local dans Microsoft Azure

###<a name="networking"></a>Mise en réseau

  1. Passez en revue la section mise en réseau de ce document.
  2. Utilisez réseau virtuel pour connecter de façon sécurisée en local dans le cloud.

###<a name="compute"></a>Cluster

  1. Passez en revue la section cluster de ce document.
  2. Déplacer des machines virtuelles entre Hyper-V et Azure.

###<a name="storage"></a>Espace de stockage

  1. Passez en revue la section de stockage de ce document.
  2. Tirer parti des services StorSimple pour l’utilisation du stockage cloud.
  3. Utiliser le service de sauvegarde Azure.

###<a name="database"></a>Base de données

  1. Passez en revue la section de base de données de ce document.
  2. Envisagez d’utiliser SQL Server sur Azure machines virtuelles que la sauvegarde.
  3. Définir des groupes de disponibilité AlwaysOn.
  4. Configurer la mise en miroir de base de données basée sur le certificat.
  5. Utiliser l’envoi de journal.
  6. Sauvegarder des bases de données locales au stockage d’objets Blob Azure.

##<a name="next-steps"></a>Étapes suivantes

Cet article fait partie d’une série de [conseils techniques résilience Azure](./resiliency-technical-guidance.md). L’article suivant de cette série est la [restauration d’altération des données ou d’une suppression accidentelle](./resiliency-technical-guidance-recovery-data-corruption.md).