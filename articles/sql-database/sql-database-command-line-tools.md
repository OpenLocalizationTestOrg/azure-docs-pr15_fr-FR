<properties
    pageTitle="Gérer la base de données SQL Azure avec PowerShell | Microsoft Azure"
    description="Gestion de la base de données SQL Azure avec PowerShell."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="sstein"/>

# <a name="manage-azure-sql-database-with-powershell"></a>Gérer la base de données SQL Azure avec PowerShell


> [AZURE.SELECTOR]
- [Portail Azure](sql-database-manage-portal.md)
- [Transact-SQL (SSMS)](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-manage-powershell.md)

Cette rubrique indique les applets de commande PowerShell qui sont utilisés pour effectuer de nombreuses tâches de base de données SQL Azure. Pour obtenir une liste complète, voir [Applets de base de données SQL Azure](https://msdn.microsoft.com/library/mt574084.aspx).


## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créer un groupe de ressources pour notre base de données SQL et les ressources connexes Azure avec l’applet de commande [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt759837.aspx) .

```
$resourceGroupName = "resourcegroup1"
$resourceGroupLocation = "northcentralus"
New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
```

Pour plus d’informations, voir [Utilisation de PowerShell Azure avec le Gestionnaire de ressources Azure](../powershell-azure-resource-manager.md).
Pour un exemple de script, voir [créer une base de données SQL script PowerShell](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).

## <a name="create-a-sql-database-server"></a>Créer un serveur de base de données SQL

Créer un serveur de base de données SQL avec l’applet de commande [New-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603715.aspx) . Remplacez *server1* par le nom de votre serveur. Nom du serveur doit être unique sur tous les serveurs de base de données SQL Azure. Si le nom du serveur est déjà effectuée, vous obtenez une erreur. Cette commande peut prendre plusieurs minutes. Le groupe de ressources doit déjà exister dans votre abonnement.

```
$resourceGroupName = "resourcegroup1"

$sqlServerName = "server1"
$sqlServerVersion = "12.0"
$sqlServerLocation = "northcentralus"
$serverAdmin = "loginname"
$serverPassword = "password" 
$securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
$creds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword
    

$sqlServer = New-AzureRmSqlServer -ServerName $sqlServerName `
 -SqlAdministratorCredentials $creds -Location $sqlServerLocation `
 -ResourceGroupName $resourceGroupName -ServerVersion $sqlServerVersion
```

Pour plus d’informations, voir [Nouveautés de la base de données SQL](sql-database-technical-overview.md). Pour un exemple de script, voir [créer une base de données SQL script PowerShell](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).


## <a name="create-a-sql-database-server-firewall-rule"></a>Créer une règle de pare-feu de base de données SQL server

Créer une règle de pare-feu pour accéder au serveur avec l’applet de commande [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860.aspx) . Exécutez la commande suivante en remplaçant les adresses IP de début et de fin avec des valeurs valides pour votre client. Le groupe de ressources et le serveur doivent déjà exister dans votre abonnement.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$firewallRuleName = "firewallrule1"
$firewallStartIp = "0.0.0.0"
$firewallEndIp = "255.255.255.255"

New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -FirewallRuleName $firewallRuleName `
 -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp
```

Pour permettre à d’autres services Azure un accès à votre serveur, créer une règle de pare-feu et définissez à la fois le `-StartIpAddress` et `-EndIpAddress` **0.0.0.0**. Cette règle de pare-feu spécial autorise tout le trafic Azure accéder au serveur.

Pour plus d’informations, voir [Pare-feu de base de données SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx). Pour un exemple de script, voir [créer une base de données SQL script PowerShell](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).


## <a name="create-a-sql-database-blank"></a>Créer une base de données SQL (vierge)

Créer une base de données avec l’applet de commande [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) . Le groupe de ressources et le serveur doivent déjà exister dans votre abonnement. 

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Standard"
$databaseServiceLevel = "S0"

$currentDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

Pour plus d’informations, voir [Nouveautés de la base de données SQL](sql-database-technical-overview.md). Pour un exemple de script, voir [créer une base de données SQL script PowerShell](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).


## <a name="change-the-performance-level-of-a-sql-database"></a>Modifier le niveau de performances d’une base de données SQL

Mettre à l’échelle votre base de données vers le haut ou vers le bas avec l’applet de commande [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) . Le groupe de ressources, le serveur et la base de données doivent déjà exister dans votre abonnement. Définir la `-RequestedServiceObjectiveName` en un seul espace (par exemple, l’extrait de code ci-dessous) pour le niveau de base. Attribuez-lui *S0*, *S1*, *P1*, *P6*, etc., comme dans l’exemple précédent pour d’autres niveaux.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Basic"
$databaseServiceLevel = " "

Set-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

Pour plus d’informations, voir [performances et options de base de données SQL : comprendre ce qui est disponible dans chaque niveau de service](sql-database-service-tiers.md). Pour un exemple de script, voir [script PowerShell exemple pour modifier le niveau de service niveau et les performances de votre base de données SQL](sql-database-scale-up-powershell.md#sample-powershell-script-to-change-the-service-tier-and-performance-level-of-your-sql-database).

## <a name="copy-a-sql-database-to-the-same-server"></a>Copier une base de données SQL sur le même serveur

Copier une base de données SQL sur le même serveur avec l’applet de commande [New-AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/azure/mt603644.aspx) . Définir la `-CopyServerName` et `-CopyResourceGroupName` sur les mêmes valeurs en tant que votre groupe de serveur et des ressources de base de données source.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

$copyDatabaseName = "database1_copy"
$copyServerName = $sqlServerName
$copyResourceGroupName = $resourceGroupName

New-AzureRmSqlDatabaseCopy -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName `
 -CopyDatabaseName $copyDatabaseName -CopyServerName $sqlServerName `
 -CopyResourceGroupName $copyResourceGroupName
```

Pour plus d’informations, voir [Copier une base de données SQL Azure](sql-database-copy.md). Pour un exemple de script, voir [Copier une base de données SQL script PowerShell](sql-database-copy-powershell.md#example-powershell-script).


## <a name="delete-a-sql-database"></a>Supprimer une base de données SQL

Supprimer une base de données SQL avec l’applet de commande [Supprimer AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619368.aspx) . Le groupe de ressources, le serveur et la base de données doivent déjà exister dans votre abonnement.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

Remove-AzureRmSqlDatabase -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## <a name="delete-a-sql-database-server"></a>Supprimer un serveur de base de données SQL

Supprimer un serveur avec l’applet de commande [Supprimer AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603488.aspx) .

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

Remove-AzureRmSqlServer -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## <a name="create-and-manage-elastic-database-pools-using-powershell"></a>Créer et gérer des pools élastique de base de données à l’aide de PowerShell

Pour plus d’informations sur la création des pools élastique de base de données à l’aide de PowerShell, voir [créer un nouveau pool de base de données élastique avec PowerShell](sql-database-elastic-pool-create-powershell.md).

Pour plus d’informations sur la gestion des pools élastique de base de données à l’aide de PowerShell, voir [moniteur et gérer un pool de base de données élastique avec PowerShell](sql-database-elastic-pool-manage-powershell.md).



## <a name="related-information"></a>Informations connexes

- [Applets de commande de base de données SQL Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx)
- [Azure référence applet de commande](https://msdn.microsoft.com/library/azure/dn708514.aspx)
