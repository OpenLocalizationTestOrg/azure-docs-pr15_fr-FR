<properties
   pageTitle="Corriger les problèmes de compatibilité de base de données SQL Server à l’aide de SQL Server Management Studio avant la migration de base de données SQL | Microsoft Azure"
   description="Microsoft Azure SQL base de données, migration de base de données, sa compatibilité, l’Assistant Migration SQL Azure"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-migrate"
   ms.date="08/24/2016"
   ms.author="carlrab"/>

# <a name="fix-sql-server-database-compatibility-issues-using-sql-server-management-studio-before-migration-to-sql-database"></a>Corriger les problèmes de compatibilité de base de données SQL Server à l’aide de SQL Server Management Studio avant la migration de base de données SQL

> [AZURE.SELECTOR]
- Utiliser l' [Assistant Migration Azure SQL](sql-database-cloud-migrate-fix-compatibility-issues.md)
- Utiliser [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- Utiliser [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)

Les utilisateurs expérimentés peuvent résoudre les problèmes de compatibilité de base de données SQL Server à l’aide de SQL Server Management Studio avant la migration de base de données SQL Azure.


> [AZURE.IMPORTANT] Il est recommandé d’utiliser toujours la dernière version de Management Studio doit pour rester synchronisé avec les mises à jour Microsoft Azure et base de données SQL. [Mettre à jour de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="using-sql-server-management-studio"></a>Avec SQL Server Management Studio

SQL Server Management Studio permet de résoudre les problèmes de compatibilité liés à l’aide des commandes Transact-SQL différents, tels que de **Modifier une base de données**. Cette méthode est principalement pour les utilisateurs expérimentés qui sont à l’aise fonctionne Transact-SQL sur une base de données active. Dans le cas contraire, il est recommandé d’utiliser SSDT. 



## <a name="next-steps"></a>Étapes suivantes

- [Version la plus récente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Version la plus récente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Migrer une base de données SQL Server compatible base de données SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Ressources supplémentaires

- [V12 de base de données SQL](sql-database-v12-whats-new.md)
- [Transact-SQL partiellement ou non prises en charge de fonctions](sql-database-transact-sql-information.md)
- [Migrer des bases de données SQL Server à l’aide de l’Assistant Migration SQL Server](http://blogs.msdn.com/b/ssma/)
