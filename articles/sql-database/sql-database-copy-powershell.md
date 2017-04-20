<properties 
    pageTitle="Copier une base de données SQL Azure à l’aide de PowerShell | Microsoft Azure" 
    description="Créer une copie d’une base de données SQL Azure à l’aide de PowerShell" 
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="09/08/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="copy-an-azure-sql-database-using-powershell"></a>Copier une base de données SQL Azure à l’aide de PowerShell


> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-database-copy.md)
- [Portail Azure](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

Cet article vous explique comment copier une base de données SQL avec PowerShell sur le même serveur, sur un serveur différent, ou copier une base de données dans un [pool élastique de base de données](sql-database-elastic-pool.md). L’opération de copie de base de données utilise l’applet de commande [New-AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/mt603644.aspx) . 


Pour exécuter cet article, vous devez les éléments suivants :

- Une base de données SQL Azure (une base de données pour copier). Si vous ne disposez pas d’une base de données SQL, créez-en un en suivant les étapes décrites dans cet article : [créer votre première base de données SQL Azure](sql-database-get-started.md).
- La dernière version d’Azure PowerShell. Pour plus d’informations, voir [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).


Plusieurs nouvelles fonctionnalités de base de données SQL sont uniquement pris en charge lorsque vous utilisez le [modèle de déploiement d’Azure le Gestionnaire de ressources](../azure-resource-manager/resource-group-overview.md), donc exemples utilisent les [applets de commande PowerShell de base de données SQL Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx) pour le Gestionnaire de ressources. Le modèle de déploiement classique existant [applets de commande de base de données SQL Azure (classique)](https://msdn.microsoft.com/library/azure/dn546723.aspx) sont prises en charge pour la compatibilité descendante, mais nous vous recommandons de qu'utiliser les applets de commande Gestionnaire de ressources.


>[AZURE.NOTE] Selon la taille de votre base de données, l’opération de copie peut prendre un certain temps pour terminer.


## <a name="copy-a-sql-database-to-the-same-server"></a>Copier une base de données SQL sur le même serveur

Pour créer la copie du même serveur, omettez le `-CopyServerName` paramètre (ou la valeur du même serveur).

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyDatabaseName "database1_copy"

## <a name="copy-a-sql-database-to-a-different-server"></a>Copier une base de données SQL vers un autre serveur

Pour créer la copie sur un serveur différent, incluez la `-CopyServerName` paramètre et définissez-la sur un autre serveur. La *copie* de serveur doit déjà exister. Si elle est dans un groupe de ressources différent, puis vous devez également inclure le `-CopyResourceGroupName` paramètre.

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyServerName "server2" -CopyDatabaseName "database1_copy"


## <a name="copy-a-sql-database-into-an-elastic-database-pool"></a>Copier une base de données SQL dans un pool élastique de base de données

Pour créer une copie d’une base de données SQL dans un pool, définissez la `-ElasticPoolName` paramètre à un pool existant.

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegoup1" -ServerName "server1" -DatabaseName "database1" -CopyResourceGroupName "poolResourceGroup" -CopyServerName "poolServer1" -CopyDatabaseName "database1_copy" -ElasticPoolName "poolName"


## <a name="resolve-logins"></a>Résolution de connexions

Pour résoudre les connexions une fois l’opération de copie terminée, voir [résolution de connexions](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)


## <a name="example-powershell-script"></a>Exemple de script PowerShell

Le script suivant suppose que tous les groupes de ressources, des serveurs, et le pool existe déjà (remplacer les valeurs de variable par vos ressources existantes). Tous les éléments doivent exister, à l’exception de la copie de la base de données.

    # Sign in to Azure and set the subscription to work with
    # ------------------------------------------------------
    $SubscriptionId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    Add-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $SubscriptionId
    
    
    # SQL database source (the existing database to copy)
    # ---------------------------------------------------
    $sourceDbName = "db1"
    $sourceDbServerName = "server1"
    $sourceDbResourceGroupName = "rg1"
    
    # SQL database copy (the new db to be created)
    # --------------------------------------------
    $copyDbName = "db1_copy"
    $copyDbServerName = "server2"
    $copyDbResourceGroupName = "rg2"
    
    # Copy a database to the same server
    # ----------------------------------
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyDatabaseName $copyDbName
    
    # Copy a database to a different server
    # -------------------------------------
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -CopyDatabaseName $copyDbName
    
    # Copy a database into an elastic database pool
    # ---------------------------------------------
    $poolName = "pool1"
    
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -ElasticPoolName $poolName -CopyDatabaseName $copyDbName



    

## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble de la copie d’une base de données SQL Azure, voir [Copier une base de données SQL Azure](sql-database-copy.md) .
- Voir [Copier une base de données SQL Azure à l’aide du portail Azure](sql-database-copy-portal.md) pour copier une base de données à l’aide du portail Azure.
- Voir [Copier une base de données SQL Azure à l’aide de T-SQL](sql-database-copy-transact-sql.md) pour copier une base de données à l’aide de Transact-SQL.
- Découvrez [comment gérer la sécurité de base de données SQL Azure après sinistre](sql-database-geo-replication-security-config.md) pour en savoir plus sur la gestion des utilisateurs et les sessions lors de la copie d’une base de données vers un autre serveur logique.


## <a name="additional-resources"></a>Ressources supplémentaires

- [Nouvelle AzureRmSqlDatabase](https://msdn.microsoft.com/library/mt603644.aspx)
- [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/mt603687.aspx)
- [Gérer les connexions](sql-database-manage-logins.md)
- [Se connecter à la base de données SQL avec SQL Server Management Studio et effectuer un exemple de requête T-SQL](sql-database-connect-query-ssms.md)
- [Exporter la base de données à un DOS](sql-database-export.md)
- [Vue d’ensemble de la continuité de gestion](sql-database-business-continuity.md)
- [Documentation de base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)
- [Référence sur applet de commande PowerShell la base de données SQL Azure](https://msdn.microsoft.com/library/mt574084.aspx)
