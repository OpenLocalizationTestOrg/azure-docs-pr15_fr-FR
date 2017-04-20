<properties
    pageTitle="Restaurer une base de données SQL Azure à partir d’une sauvegarde geo redondantes (PowerShell) | Microsoft Azure"
    description="Restaurer une base de données SQL Azure dans un nouveau serveur à partir d’une sauvegarde geo redondants"
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

# <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-by-using-powershell"></a>Restaurer une base de données SQL Azure à partir d’une sauvegarde geo redondants à l’aide de PowerShell


> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-database-recovery-using-backups.md)
- [Geo-restaurer : Portail Azure](sql-database-geo-restore-portal.md)

Cet article vous explique comment restaurer votre base de données dans un nouveau serveur à l’aide geo restaurer. Cela peut être effectuée via PowerShell.

[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="geo-restore-your-database-into-a-standalone-database"></a>Geo-restaurer votre base de données dans une base de données autonome

1. Obtenir la sauvegarde geo redondants de votre base de données que vous souhaitez restaurer à l’aide de la [Get-AzureRmSqlDatabaseGeoBackup] (applet de commande https://msdn.microsoft.com/library/azure/mt693388(v=azure.300\).aspx).

        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Démarrer la restauration de la sauvegarde geo redondants à l’aide de la [restaurer-AzureRmSqlDatabase] (applet de commande https://msdn.microsoft.com/library/azure/mt693390(v=azure.300\).aspx).

        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID -Edition "Standard" -RequestedServiceObjectiveName "S2"


## <a name="geo-restore-your-database-into-an-elastic-database-pool"></a>Geo-restaurer votre base de données dans un pool élastique de base de données

1. Obtenir la sauvegarde geo redondants de votre base de données que vous souhaitez restaurer à l’aide de la [Get-AzureRmSqlDatabaseGeoBackup] (applet de commande https://msdn.microsoft.com/library/azure/mt693388(v=azure.300\).aspx).

        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Démarrer la restauration de la sauvegarde geo redondants à l’aide de la [restaurer-AzureRmSqlDatabase] (applet de commande https://msdn.microsoft.com/library/azure/mt693390(v=azure.300\).aspx). Spécifiez le nom du pool que vous voulez restaurer votre base de données dans.

        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID –ElasticPoolName "elasticpool01"  


## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble de la continuité de gestion et les scénarios, voir [vue d’ensemble de la continuité de l’activité entreprise](sql-database-business-continuity.md).
- Pour en savoir plus sur les sauvegardes de base de données SQL Azure automatisé, voir [sauvegardes automatisées de base de données SQL](sql-database-automated-backups.md).
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, voir [restaurer une base de données à partir des sauvegardes exécutée par le service](sql-database-recovery-using-backups.md).
- Pour en savoir plus sur les options de récupération plus rapides, voir [Réplication de Geo actif](sql-database-geo-replication-overview.md).  
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées d’archivage, voir [Copier de base de données](sql-database-copy.md).
