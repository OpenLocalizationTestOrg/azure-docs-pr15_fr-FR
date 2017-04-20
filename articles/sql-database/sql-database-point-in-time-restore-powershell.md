<properties
    pageTitle="Restaurer une base de données SQL Azure à un point antérieur dans le temps (PowerShell) | Microsoft Azure"
    description="Restaurer une base de données SQL Azure à un point antérieur dans le temps"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="NA"
    ms.date="07/17/2016"
    ms.author="sstein"/>

# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time-with-powershell"></a>Restaurer une base de données SQL Azure à un point antérieur dans le temps avec PowerShell

> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-database-recovery-using-backups.md)
- [Restaurer point-à-temps : Portail Azure](sql-database-point-in-time-restore-portal.md)

Cet article vous explique comment restaurer votre base de données à un point antérieur dans le temps à partir de la [base de données SQL des sauvegardes automatisées](sql-database-automated-backups.md). Vous pouvez le faire à l’aide de PowerShell.

[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="restore-your-database-to-a-point-in-time-as-a-standalone-database"></a>Restaurer votre base de données à un point précis qu’une base de données autonome

1. Obtenir la base de données que vous voulez restaurer à l’aide de la [Get-AzureRmSqlDatabase] (applet de commande https://msdn.microsoft.com/library/azure/mt603648(v=azure.300\).aspx).

        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Restaurer la base de données à un point dans le temps à l’aide de la [restaurer-AzureRmSqlDatabase] (applet de commande https://msdn.microsoft.com/library/azure/mt693390(v=azure.300\).aspx).

        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"


## <a name="restore-your-database-to-a-point-in-time-into-an-elastic-database-pool"></a>Restaurer votre base de données à un point dans le temps dans un pool élastique de base de données

1. Obtenir la base de données que vous voulez restaurer à l’aide de la [Get-AzureRmSqlDatabase] (applet de commande https://msdn.microsoft.com/library/azure/mt603648(v=azure.300\).aspx).

        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Restaurer la base de données à un point dans le temps à l’aide de la [restaurer-AzureRmSqlDatabase] (applet de commande https://msdn.microsoft.com/library/azure/mt693390(v=azure.300\).aspx).

        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID –ElasticPoolName "elasticpool01"


## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble de la continuité de gestion et scénarios, voir [vue d’ensemble de la continuité de gestion](sql-database-business-continuity.md)
- Pour en savoir plus sur les sauvegardes de base de données SQL Azure automatisé, voir [sauvegardes automatisées de base de données SQL](sql-database-automated-backups.md)
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, voir [restaurer une base de données à partir des sauvegardes exécutée par le service](sql-database-recovery-using-backups.md)
- Pour en savoir plus sur les options de récupération plus rapides, voir [Actif-Geo-réplication](sql-database-geo-replication-overview.md)  
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées d’archivage, voir [Copier de base de données](sql-database-copy.md)
