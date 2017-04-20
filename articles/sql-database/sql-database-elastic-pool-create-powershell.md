<properties
    pageTitle="Créer un nouveau pool de base de données élastique avec PowerShell | Microsoft Azure"
    description="Découvrez comment utiliser PowerShell pour la base de données SQL Azure horizontale ressources en créant un pool élastique scalable de la base de données pour gérer plusieurs bases de données."
    services="sql-database"
    documentationCenter=""
    authors="srinia"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="05/27/2016"
    ms.author="srinia"/>

# <a name="create-a-new-elastic-database-pool-with-powershell"></a>Créer un nouveau pool de base de données élastique avec PowerShell

> [AZURE.SELECTOR]
- [Portail Azure](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


Apprenez à créer un [pool élastique de base de données](sql-database-elastic-pool.md) à l’aide des applets de commande PowerShell. 

Pour les codes d’erreur courants, voir [codes d’erreur SQL pour les applications clientes de base de données SQL : erreur de connexion et d’autres problèmes de base de données](sql-database-develop-error-messages.md).

> [AZURE.NOTE] Pools élastiques sont généralement disponibles dans toutes les régions Azure à l’exception du Nord centrale nous et Ouest Inde où il est actuellement en mode Aperçu.  Disponibilité générale des pools élastiques dans ces régions est fournie dès que possible. En outre, pools élastiques ne prennent pas en charge bases de données à l’aide [en mémoire OLTP ou analytique en mémoire](sql-database-in-memory.md).


Vous devez être en cours d’exécution PowerShell Azure 1.0 ou version ultérieure. Pour plus d’informations, voir [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

## <a name="create-a-new-pool"></a>Créer un nouveau pool

L’applet de commande [New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx) crée un nouveau pool. Les valeurs d’eDTU par pool, min et max Dtus sont limitées par la valeur de niveau de service (basic, standard ou premium). Voir [eDTU et stockage limite pour des pools élastiques et élastiques bases de données](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases).

    New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


## <a name="create-a-new-elastic-database-in-a-pool"></a>Créer une nouvelle base de données élastique dans un pool

Utiliser l’applet de commande [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) et définissez le paramètre **ElasticPoolName** sur le pool cible. Pour déplacer une base de données existante dans un pool, voir [déplacer une base de données dans un pool élastique](sql-database-elastic-pool-manage-powershell.md#Move-a-database-into-an-elastic-pool).

    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="create-a-pool-and-populate-it-with-multiple-new-databases"></a>Créer un pool et remplissez-la avec plusieurs nouvelles bases de données 

La création d’un grand nombre de bases de données dans un pool peut prendre temps lorsque terminé à l’aide du portail ou applets de commande PowerShell que créez uniquement une seule base de données à la fois. Pour automatiser la création dans un nouveau pool, voir [CreateOrUpdateElasticPoolAndPopulate ](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).   

## <a name="example-create-a-pool-using-powershell"></a>Exemple : création d’un pool à l’aide de PowerShell 

Ce script crée un nouveau groupe de ressources Azure et un nouveau serveur. Lorsque vous y êtes invité, fournissez un nom d’administrateur et le mot de passe pour le nouveau serveur (pas vos informations d’identification Azure).

    $subscriptionId = '<your Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $location -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

    New-AzureRmSqlElasticPool -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100

    New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -ElasticPoolName $poolName -MaxSizeBytes 10GB



## <a name="next-steps"></a>Étapes suivantes

- [Gérer votre pool](sql-database-elastic-pool-manage-powershell.md)
- [Créer des travaux élastiques](sql-database-elastic-jobs-overview.md) Tâches élastiques vous permettent d’exécuter des scripts T-SQL par rapport à un nombre quelconque de bases de données dans le pool.
- [Évolution horizontale avec la base de données SQL Azure](sql-database-elastic-scale-introduction.md): utiliser les outils de base de données élastique pour horizontale.

