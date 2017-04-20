<properties
    pageTitle="Gérer la base de données SQL Azure à l’aide du portail Azure | Microsoft Azure"
    description="Découvrez comment utiliser le portail Azure pour gérer une base de données relationnel dans le cloud à l’aide du portail Azure."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.date="09/19/2016"
    ms.author="sstein"/>


# <a name="managing-azure-sql-databases-using-the-azure-portal"></a>Gestion des bases de données SQL Azure à l’aide du portail Azure


> [AZURE.SELECTOR]
- [Portail Azure](sql-database-manage-portal.md)
- [SSMS](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-manage-powershell.md)

Le [portail Azure](https://portal.azure.com/) permet de créer, surveiller et gérer les serveurs et les bases de données SQL Azure. Cet article fournit une rapide description et des liens vers les détails des tâches plus courantes.

## <a name="view-your-azure-sql-databases-servers-and-pools"></a>Afficher vos bases de données SQL Azure, des serveurs et des pools

Pour afficher les services de base de données SQL disponibles, cliquez sur **plusieurs services de**, puis tapez **SQL** dans la zone de recherche :

![Base de données SQL](./media/sql-database-manage-portal/sql-services.png)


## <a name="how-do-i-create-or-view-azure-sql-databases"></a>Comment créer ou afficher les bases de données SQL Azure ?

Pour ouvrir la carte de **bases de données SQL** , cliquez sur **bases de données SQL**, puis cliquez sur la base de données que vous souhaitez utiliser ou cliquez sur **+ Ajouter** pour créer une base de données SQL. Pour plus d’informations, voir [créer une base de données SQL en minutes à l’aide du portail Azure](sql-database-get-started.md).


![Bases de données SQL](./media/sql-database-manage-portal/sql-databases.png)


## <a name="how-do-i-create-or-view-azure-sql-servers"></a>Comment créer ou afficher les serveurs SQL Azure ?

Pour ouvrir la carte de **serveurs SQL Server** , cliquez sur **SQL Server**, puis cliquez sur le serveur que vous souhaitez utiliser ou cliquez sur **+ Ajouter** pour créer un serveur SQL server. Pour plus d’informations, voir [créer une base de données SQL en minutes à l’aide du portail Azure](sql-database-get-started.md).

![Serveurs SQL Server](./media/sql-database-manage-portal/sql-servers.png)


## <a name="how-do-i-create-or-view-sql-elastic-pools"></a>Comment créer ou afficher des pools élastiques SQL ?

Pour ouvrir la carte **pools élastiques SQL** , cliquez sur **pools élastiques SQL**, puis cliquez sur le pool que vous voulez travailler avec ou cliquez sur **+ Ajouter** pour créer un pool. Pour plus d’informations, voir [créer un pool de base de données élastique grâce au portail Azure](sql-database-elastic-pool-create-portal.md).

![Pools élastiques SQL](./media/sql-database-manage-portal/elastic-pools.png)



## <a name="how-do-i-update-or-view-sql-database-settings"></a>Comment mettre à jour ou afficher les paramètres de base de données SQL ?

Pour afficher ou mettre à jour vos paramètres de base de données, cliquez sur le paramètre souhaité dans la carte de base de données SQL :


![Paramètres de base de données SQL](./media/sql-database-manage-portal/settings.png)


## <a name="how-do-i-find-a-sql-databases-fully-qualified-server-name"></a>Comment trouver un nom de serveur complet de bases de données SQL ?

Pour afficher le nom de votre serveur de bases de données, cliquez sur **vue d’ensemble** sur la carte de **base de données SQL** et notez le nom du serveur :


![Paramètres de base de données SQL](./media/sql-database-manage-portal/server-name.png)


## <a name="how-do-i-manage-firewall-rules-to-control-access-to-my-sql-server-and-database"></a>Comment gérer les règles de pare-feu pour contrôler l’accès à mon SQL server et la base de données ?

Pour afficher, créer ou mettre à jour les règles de pare-feu, cliquez sur **définir pare-feu server** dans la carte de **base de données SQL** . Pour plus d’informations, voir [configurer une règle de pare-feu au niveau du serveur de base de données SQL Azure à l’aide du portail Azure](sql-database-configure-firewall-settings.md).


![règles de pare-feu](./media/sql-database-manage-portal/sql-database-firewall.png)


## <a name="how-do-i-change-my-sql-database-service-tier-or-performance-level"></a>Comment modifier mes SQL de base de données niveau ou les performances de niveau de service ?


Pour mettre à jour le niveau de service niveau ou les performances d’une base de données SQL, cliquez sur **couche de tarification (échelle DTUs)** sur la carte de **base de données SQL** . Pour plus d’informations, voir [Modifier le niveau et les performances niveau de service (tarification couche) d’une base de données SQL](sql-database-scale-up.md).


![tarifs niveaux](./media/sql-database-manage-portal/pricing-tier.png)


## <a name="how-do-i-configure-auditing-and-threat-detection-for-a-sql-database"></a>Comment configurer un audit et détection pour une base de données SQL des menaces ?

Pour configurer l’audit et détection pour une base de données SQL, cliquez sur **détection audit des menaces,** dans la carte de **base de données SQL** . Pour plus d’informations, voir [prise en main d’audit de base de données SQL](sql-database-auditing-get-started.md)et [prise en main de détection de base de données SQL](sql-database-threat-detection-get-started.md).


## <a name="how-do-i-configure-dynamic-data-masking-for-a-sql-database"></a>Comment configurer échange dynamique de données pour une base de données SQL de masquage ?

Pour configurer le masquage pour une base de données SQL échange dynamique de données, cliquez sur **échange dynamique de données masking** sur la carte de **base de données SQL** . Pour plus d’informations, voir [prise en main Masking données dynamiques SQL de base de données](sql-database-dynamic-data-masking-get-started.md).


## <a name="how-do-i-configure-transparent-data-encryption-tde-for-a-sql-database"></a>Comment configurer le chiffrement transparent des données (TDE) pour une base de données SQL ?

Pour configurer le chiffrement transparent des données pour une base de données SQL, cliquez sur **chiffrement de données Transparent** dans la carte de **base de données SQL** . Pour plus d’informations, voir [Activer TDE sur une base de données à l’aide du portail](https://msdn.microsoft.com/library/dn948096#Anchor_1).

## <a name="how-do-i-view-or-change-the-max-size-of-a-sql-database"></a>Comment afficher ou modifier la taille maximale d’une base de données SQL ?

Pour afficher ou modifier la taille une base de données SQL, cliquez sur **taille de la base de données** dans la carte de **base de données SQL** . Mettre à jour la taille maximale d’une base de données en modifiant le niveau de performances ou de niveau de service. Pour plus d’informations, voir [Modifier le niveau et les performances niveau de service (tarification couche) d’une base de données SQL](sql-database-scale-up.md).

## <a name="how-do-i-monitor-and-improve-the-performance-of-a-sql-database"></a>Comment surveiller et améliorer les performances d’une base de données SQL ?

Pour contrôler et améliorer les caractéristiques de performances d’une base de données SQL, cliquez sur **vue d’ensemble des performances** dans la carte de **base de données SQL** . Pour plus d’informations, consultez [Performances un aperçu de la base de données SQL](sql-database-performance.md).


## <a name="how-do-i-configure-geo-replication"></a>Comment configurer la réplication Geo ?

Pour configurer la réplication Geo pour une base de données SQL, cliquez sur **Geo réplication** dans la carte de **base de données SQL** . Pour plus d’informations, voir [Configurer la Geo-réplication de base de données SQL Azure avec le portail Azure](sql-database-geo-replication-portal.md).


## <a name="how-do-i-failover-to-a-geo-replicated-sql-database"></a>Comment basculer vers une base de données SQL répliquées geo ?

Pour basculer vers un secondaire répliquées geo, cliquez sur **Geo réplication** sur la carte de **base de données SQL** , puis cliquez sur **Basculer**. Pour plus d’informations, voir [lancer un basculement planifié ou de base de données SQL Azure avec le portail Azure](sql-database-geo-replication-failover-portal.md).


## <a name="how-do-i-copy-a-sql-database"></a>Comment copier une base de données SQL ?

Pour copier une base de données SQL, cliquez sur **Copier** dans la carte de **base de données SQL** . Pour plus d’informations, voir [Copier une base de données SQL Azure à l’aide du portail Azure](sql-database-copy-portal.md).


![Paramètres de base de données SQL](./media/sql-database-manage-portal/sql-database-copy.png)

## <a name="how-do-i-archive-an-azure-sql-database-to-a-bacpac-file"></a>Comment archiver une base de données SQL Azure vers un fichier à DOS ?

Pour créer un à DOS d’une base de données SQL, cliquez sur **Exporter** dans la carte de **base de données SQL** . Pour plus d’informations, voir [Archiver une base de données SQL Azure vers un fichier à DOS à l’aide du portail Azure](sql-database-export.md).


![Exportation de base de données SQL](./media/sql-database-manage-portal/sql-database-export.png)



## <a name="how-do-i-restore-a-sql-database-to-a-previous-point-in-time"></a>Comment restaurer une base de données SQL à un point antérieur dans le temps ?

Pour restaurer une base de données SQL, cliquez sur **restaurer** dans la carte de **base de données SQL** . Pour plus d’informations, voir [restaurer une base de données SQL Azure à un point antérieur dans le temps grâce au portail Azure](sql-database-point-in-time-restore-portal.md).


![Paramètres de base de données SQL](./media/sql-database-manage-portal/sql-database-restore.png)


## <a name="how-do-i-create-an-azure-sql-database-from-a-bacpac-file"></a>Comment créer une base de données SQL Azure à partir d’un fichier à DOS ?

Pour créer une base de données SQL à partir d’un fichier à DOS, cliquez sur **Importer base de données** dans la carte **SQL server** . Pour plus d’informations, voir [Importer un fichier à DOS pour créer une base de données SQL Azure](sql-database-import.md).


![Serveur SQL](./media/sql-database-manage-portal/server-commands.png)


## <a name="how-do-i-restore-a-deleted-sql-database"></a>Comment restaurer une base de données SQL supprimé ?

Pour restaurer une base de données SQL supprimé, cliquez sur **supprimé bases de données** dans la carte **SQL server** (le serveur SQL contenant la base de données a été supprimé). Pour plus d’informations, voir [restaurer une base de données SQL Azure supprimé à l’aide du portail Azure](sql-database-restore-deleted-database-portal.md).

## <a name="how-do-i-delete-a-sql-database"></a>Comment supprimer une base de données SQL ?

Pour supprimer une base de données SQL, cliquez sur **Supprimer** dans la carte de **base de données SQL** . 

![Paramètres de base de données SQL](./media/sql-database-manage-portal/sql-database-delete.png)



## <a name="additional-resources"></a>Ressources supplémentaires

- [Base de données SQL](sql-database-technical-overview.md)
- [Surveiller et gérer un pool de base de données élastique grâce au portail Azure](sql-database-elastic-pool-manage-portal.md)
