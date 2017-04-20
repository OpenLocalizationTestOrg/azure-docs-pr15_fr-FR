<properties
    pageTitle="Restaurer une base de données SQL Azure à un point antérieur dans le temps (Azure portal) | Microsoft Azure"
    description="Restaurer une base de données SQL Azure à un point antérieur dans le temps."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/18/2016"
    ms.author="sstein"
    ms.workload="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time-with-the-azure-portal"></a>Restaurer une base de données SQL Azure à un point antérieur dans le temps grâce au portail Azure


> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-database-recovery-using-backups.md)
- [Restauration de point-à-temps : PowerShell](sql-database-point-in-time-restore-powershell.md)

Cet article vous explique comment restaurer votre base de données à un point antérieur dans le temps à partir de la [que base de données SQL des sauvegardes automatisées](sql-database-automated-backups.md) à l’aide du portail Azure.

## <a name="restore-a-sql-database-to-a-previous-point-in-time"></a>Restaurer une base de données SQL à un point antérieur dans le temps

Sélectionnez une base de données à restaurer dans le portail Azure :

1.  Ouvrez le [portail Azure](https://portal.azure.com).
2.  Sur le côté gauche de l’écran, sélectionnez **plusieurs services de** > **bases de données SQL**.
3.  Cliquez sur la base de données que vous souhaitez restaurer.
4.  En haut de la page de votre base de données, sélectionnez **restaurer**:

    ![Restaurer une base de données SQL Azure](./media/sql-database-point-in-time-restore-portal/restore.png)

5.  Dans la page **restaurer** , sélectionnez la date et l’heure (au format UTC) pour restaurer la base de données, puis cliquez sur **OK**:

    ![Restaurer une base de données SQL Azure](./media/sql-database-point-in-time-restore-portal/restore-details.png)

## <a name="monitor-the-restore-operation"></a>Surveiller l’opération de restauration

1. Après avoir cliqué sur **OK** dans l’étape précédente, cliquez sur l’icône de notification dans le coin supérieur droit de la page, puis cliquez sur la notification de **base de données SQL restauration** pour plus d’informations.

    ![Restaurer une base de données SQL Azure](./media/sql-database-point-in-time-restore-portal/notification-icon.png)

2. La page de base de données SQL en cours de restauration s’ouvre avec des informations sur l’état de la restauration. Vous pouvez cliquer sur la ligne de facturation pour plus d’informations :

    ![Restaurer une base de données SQL Azure](./media/sql-database-point-in-time-restore-portal/inprogress.png)

 

## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble de la continuité de gestion et scénarios, voir [vue d’ensemble de la continuité de gestion](sql-database-business-continuity.md)
- Pour en savoir plus sur les sauvegardes de base de données SQL Azure automatisé, voir [sauvegardes automatisées de base de données SQL](sql-database-automated-backups.md)
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, voir [restaurer une base de données à partir des sauvegardes exécutée par le service](sql-database-recovery-using-backups.md)
- Pour en savoir plus sur les options de récupération plus rapides, voir [Actif-Geo-réplication](sql-database-geo-replication-overview.md)  
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées d’archivage, voir [Copier de base de données](sql-database-copy.md)
