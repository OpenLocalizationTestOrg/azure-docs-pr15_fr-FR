<properties
   pageTitle="Restaurer un SQL Azure Data Warehouse (PowerShell) | Microsoft Azure"
   description="Tâches de PowerShell pour restaurer un magasin de données SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="Lakshmi1812"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/21/2016"
   ms.author="lakshmir;barbkess;sonyama"/>

# <a name="restore-an-azure-sql-data-warehouse-powershell"></a>Restaurer un SQL Azure Data Warehouse (PowerShell)

> [AZURE.SELECTOR]
- [Vue d’ensemble][]
- [Portail][]
- [PowerShell][]
- [RESTE][]

Dans cet article, vous allez apprendre à restaurer un magasin de données SQL Azure à l’aide de PowerShell.

## <a name="before-you-begin"></a>Avant de commencer

**Vérifiez votre capacité DTU.** Chaque Data Warehouse SQL est hébergé par un serveur SQL (par exemple, myserver.database.windows.net) qui dispose d’un quota DTU par défaut.  Avant de pouvoir restaurer un Data Warehouse SQL, vérifiez que la votre serveur SQL server est suffisamment quota DTU restant pour la base de données en cours de restauration. Pour apprendre à calculer DTU nécessaire ou pour demander DTU plus, voir [demander une modification de quota DTU][].

### <a name="install-powershell"></a>Installer PowerShell

Pour pouvoir utiliser PowerShell Azure avec SQL Data Warehouse, vous devez installer Azure PowerShell 1.0 ou version ultérieure.  Vous pouvez vérifier votre version en exécutant **Get-Module - ListAvailable-AzureRM nom**.  La version la plus récente peut être installée à partir de [Microsoft Web Platform Installer][].  Pour plus d’informations sur l’installation de la dernière version, voir [comment installer et configurer Azure PowerShell][].

## <a name="restore-an-active-or-paused-database"></a>Restaurer une base de données active ou suspendu

Pour restaurer une base de données à partir d’un instantané utiliser l’applet de commande PowerShell [Restaurer AzureRmSqlDatabase][] .

1. Ouvrez Windows PowerShell.
2. Se connecter à votre compte Azure et répertorier tous les abonnements associés à votre compte.
3. Sélectionnez l’abonnement qui contient la base de données à restaurer.
4. Répertorier les points de restauration de la base de données.
5. Sélectionnez le point de restauration à l’aide de la RestorePointCreationDate.
6. Restaurer la base de données au point de restauration souhaité.
7. Vérifiez que la base de données restaurée est en ligne.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.$ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

>[AZURE.NOTE] Une fois la restauration terminée, vous pouvez configurer votre base de données récupéré en suivant [configurer votre base de données après restauration][].


## <a name="restore-a-deleted-database"></a>Restaurer une base de données supprimé

Pour restaurer une base de données supprimé, utilisez l’applet de commande [Restaurer AzureRmSqlDatabase][] .

1. Ouvrez Windows PowerShell.
2. Se connecter à votre compte Azure et répertorier tous les abonnements associés à votre compte.
3. Sélectionnez l’abonnement qui contient la base de données supprimé à restaurer.
4. Obtenir les bases de données supprimées spécifique.
5. Restaurer la base de données supprimé.
6. Vérifiez que la base de données restaurée est en ligne.

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupNam -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

>[AZURE.NOTE] Une fois la restauration terminée, vous pouvez configurer votre base de données récupéré en suivant [configurer votre base de données après restauration][].


## <a name="restore-from-an-azure-geographical-region"></a>Restaurer à partir d’une région géographique Azure

Pour restaurer une base de données, utilisez l’applet de commande [Restaurer AzureRmSqlDatabase][] .

1. Ouvrez Windows PowerShell.
2. Se connecter à votre compte Azure et répertorier tous les abonnements associés à votre compte.
3. Sélectionnez l’abonnement qui contient la base de données à restaurer.
4. Obtenir la base de données que vous souhaitez récupérer.
5. Créez la demande de récupération de la base de données.
6. Vérifiez l’état de la base de données restauré geo.

```Powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

>[AZURE.NOTE] Pour configurer votre base de données une fois la restauration terminée, voir [configurer votre base de données après restauration][]. 


La base de données récupérée est activé TDE si la base de données source est activée TDE.


## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les fonctionnalités continuité des éditions de base de données SQL Azure, veuillez lire la [vue d’ensemble de la continuité de gestion de base de données SQL Azure][].

<!--Image references-->

<!--Article references-->
[Vue d’ensemble du continuité de gestion de la base de données SQL Azure]: sql-database-business-continuity.md
[Demander une modification de quota DTU]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[Configurer votre base de données après restauration]: ./sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Comment installer et configurer PowerShell Azure]: powershell-install-configure.md
[Vue d’ensemble]: ./sql-data-warehouse-restore-database-overview.md
[Portail]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[RESTE]: ./sql-data-warehouse-restore-database-rest-api.md
[Configurer votre base de données après restauration]: ./sql-database-disaster-recovery.md#configure-your-database-after-recovery

<!--MSDN references-->
[Restaurer AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Programme d’installation de la plateforme Microsoft Web]: https://aka.ms/webpi-azps
