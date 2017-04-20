<properties
    pageTitle="Restaurer une base de données SQL Azure supprimé (Azure portal) | Microsoft Azure"
    description="Restaurer une base de données SQL Azure supprimé (Azure portal)."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/12/2016"
    ms.author="sstein"
    ms.workload="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="restore-a-deleted-azure-sql-database-using-the-azure-portal"></a>Restaurer une base de données SQL Azure supprimé à l’aide du portail Azure

> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-database-recovery-using-backups.md)
- [**Restaurer supprimés DB : portail**](sql-database-restore-deleted-database-portal.md)
- [Restaurer supprimés DB : PowerShell](sql-database-restore-deleted-database-powershell.md)

## <a name="select-the-database-to-restore"></a>Sélectionnez la base de données à restaurer 

Pour restaurer une base de données supprimé dans le portail Azure :

1.  Dans le [portail Azure](https://portal.azure.com), cliquez sur **plusieurs services de** > **serveurs SQL Server**.
3.  Sélectionnez le serveur contenant la base de données que vous souhaitez restaurer.
4.  Faites défiler jusqu'à la section **opérations** de votre carte de serveur, puis sélectionnez les **bases de données supprimés**: ![restaurer une base de données SQL Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
5.  Sélectionnez la base de données que vous souhaitez restaurer.
6.  Spécifiez un nom de base de données, puis cliquez sur **OK**:

    ![Restaurer une base de données SQL Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)


## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble de la continuité de gestion et scénarios, voir [vue d’ensemble de la continuité de gestion](sql-database-business-continuity.md)
- Pour en savoir plus sur les sauvegardes de base de données SQL Azure automatisé, voir [sauvegardes automatisées de base de données SQL](sql-database-automated-backups.md)
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, voir [restaurer une base de données à partir des sauvegardes exécutée par le service](sql-database-recovery-using-backups.md)
- Pour en savoir plus sur les options de récupération plus rapides, voir [Actif-Geo-réplication](sql-database-geo-replication-overview.md)  
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées d’archivage, voir [Copier de base de données](sql-database-copy.md)
