<properties
   pageTitle="Applets de commande PowerShell pour entrepôt de données SQL Azure"
   description="Rechercher les applets de commande PowerShell supérieure pour entrepôt de données SQL Azure, y compris comment interrompre et reprendre une base de données."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="sonyama;barbkess;mausher"/>

# <a name="powershell-cmdlets-and-rest-apis-for-sql-data-warehouse"></a>Applets de commande PowerShell et des API REST pour Data Warehouse SQL

Nombreuses tâches d’administration SQL Data Warehouse peuvent être gérés à l’aide des applets de commande PowerShell Azure ou API REST.  Voici quelques exemples d’utilisation des commandes PowerShell pour automatiser les tâches courantes dans votre Data Warehouse SQL.  Pour obtenir des exemples reste bonne, voir l’article [Gérer extensibilité du reste][].

> [AZURE.NOTE]  Pour pouvoir utiliser PowerShell Azure avec SQL Data Warehouse, vous devez Azure PowerShell version 1.0.3 ou version ultérieure.  Vous pouvez vérifier votre version en exécutant **Get-Module - ListAvailable-nom Azure**.  La version la plus récente peut être installée à partir de [Microsoft Web Platform Installer][].  Pour plus d’informations sur l’installation de la dernière version, voir [comment installer et configurer Azure PowerShell][].

## <a name="get-started-with-azure-powershell-cmdlets"></a>Prise en main applets de commande PowerShell Azure

1. Ouvrez Windows PowerShell. 
2. À l’invite PowerShell, exécutez ces commandes pour se connecter au Gestionnaire de ressources Azure et sélectionnez votre abonnement.

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-sql-data-warehouse-example"></a>Exemple de magasin de données SQL pause

Placez le curseur une base de données nommée « Base de données02 « hébergé sur un serveur nommé « Serveur01. »  Le serveur est dans un groupe de ressources Azure nommé « ResourceGroup1 ». 

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
Une variation de cet exemple injecte l’objet récupéré pour [Suspendre AzureRmSqlDatabase][].  Par conséquent, la base de données est suspendu. La commande finale affiche les résultats.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

## <a name="start-sql-data-warehouse-example"></a>Démarrer l’exemple de magasin de données SQL

Reprendre l’opération d’une base de données nommée « Base de données02 « hébergé sur un serveur nommé « Serveur01. » Le serveur se trouve dans un groupe de ressources nommé « ResourceGroup1 ».

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Une variation de cet exemple récupère une base de données nommée « Base de données02 » à partir d’un serveur nommé « Server01 » se trouvant dans un groupe de ressources nommé « ResourceGroup1 ». Il injecte l’objet récupéré pour [Reprendre AzureRmSqlDatabase][].

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
```

> [AZURE.NOTE] Notez que si votre serveur est foo.database.windows.net, utilisez « foo » comme le nom du serveur - dans les applets de commande PowerShell.

## <a name="frequently-used-powershell-cmdlets"></a>Applets de commande PowerShell utilisés fréquemment

Ces applets de commande PowerShell sont fréquemment utilisés avec Azure SQL Data Warehouse.

- [Get-AzureRmSqlDatabase][]
- [Get-AzureRmSqlDeletedDatabaseBackup][]
- [Get-AzureRmSqlDatabaseRestorePoints][]
- [Nouvelle AzureRmSqlDatabase][]
- [Supprimer AzureRmSqlDatabase][]
- [Restaurer AzureRmSqlDatabase][] 
- [CV AzureRmSqlDatabase][]
- [Sélectionnez AzureRmSubscription][]
- [Jeu de AzureRmSqlDatabase][]
- [AzureRmSqlDatabase suspendre][]

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir d’autres exemples de PowerShell, voir :

- [Créer un SQL Data Warehouse à l’aide de PowerShell][]
- [Restauration de la base de données][]

Pour obtenir une liste de toutes les tâches qui peuvent être automatisées avec PowerShell, voir [Applets de commande de base de données de SQL Azure][].  Pour obtenir la liste des tâches qui peuvent être automatisées avec reste, consultez [opérations de bases de données SQL Azure][].

<!--Image references-->

<!--Article references-->
[Comment installer et configurer PowerShell Azure]: ./powershell-install-configure.md
[Créer un SQL Data Warehouse à l’aide de PowerShell]: ./sql-data-warehouse-get-started-provision-powershell.md
[Restauration de la base de données]: ./sql-data-warehouse-restore-database-powershell.md
[Gérer les extensibilité du reste]: ./sql-data-warehouse-manage-compute-rest-api.md

<!--MSDN references-->
[Applets de commande de base de données SQL Azure]: https://msdn.microsoft.com/library/mt574084.aspx
[Opérations de bases de données SQL Azure]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[Get-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt603648.aspx
[Get-AzureRmSqlDeletedDatabaseBackup]: https://msdn.microsoft.com/library/mt693387.aspx
[Get-AzureRmSqlDatabaseRestorePoints]: https://msdn.microsoft.com/library/mt603642.aspx
[Nouvelle AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Supprimer AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619368.aspx
[Restaurer AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx
[CV AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
<!-- It appears that Select-AzureRmSubscription isn't documented, so this points to Select-AzureSubscription -->
[Sélectionnez AzureRmSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Jeu de AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx
[AzureRmSqlDatabase suspendre]: https://msdn.microsoft.com/library/mt619337.aspx

<!--Other Web references-->
[Programme d’installation de la plateforme Microsoft Web]: https://aka.ms/webpi-azps
