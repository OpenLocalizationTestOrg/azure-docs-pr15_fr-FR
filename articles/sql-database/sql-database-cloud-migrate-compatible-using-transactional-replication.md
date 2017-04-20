<properties
   pageTitle="Migrer vers la base de données SQL utilisant la réplication transactions | Microsoft Azure"
   description="Base de données de SQL Microsoft Azure, migration de base de données, Importer base de données, transactions réplication"
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
   ms.date="08/23/2016"
   ms.author="carlrab"/>

# <a name="migrate-sql-server-database-to-azure-sql-database-using-transactional-replication"></a>Migrer la base de données SQL Server à la base de données SQL Azure à l’aide de la réplication de transactions

> [AZURE.SELECTOR]
- [Assistant Migration SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Exporter vers un fichier à DOS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Importer depuis un fichier à DOS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Transactions réplication](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

Dans cet article, vous découvrez comment migrer une base de données SQL Server compatible à base de données SQL Azure très peu de temps à l’aide de la réplication de transactions SQL Server.

## <a name="understanding-the-transactional-replication-architecture"></a>Présentation de l’architecture de réplication transactions

Lorsque vous ne pouvez pas vous permettre de supprimer votre base de données SQL Server de production pendant la migration, vous pouvez utiliser la réplication transactions SQL Server comme votre solution de migration. Pour utiliser cette solution, vous configurez votre base de données SQL Azure en tant qu’abonné à l’instance SQL Server locale que vous voulez migrer. Les locaux distributeur de réplication transactions synchronise les données à partir de la base de données locale à synchroniser (l’éditeur) tandis que les nouvelles transactions continuent à se produire. 

Vous pouvez également utiliser réplication transactions pour migrer un sous-ensemble de votre base de données locale. La composition dans laquelle vous répliquez sur base de données SQL Azure peut être limitée à un sous-ensemble des tables de la base de données répliquée. Pour chaque table répliquée, vous pouvez limiter les données à un sous-ensemble des lignes et/ou un sous-ensemble des colonnes.

Avec la réplication de transactions, toutes les modifications apportées à vos données ou le schéma apparaissent dans votre base de données SQL Azure. Une fois que la synchronisation est terminée et vous êtes prêt à migrer, modifiez la chaîne de connexion de vos applications pour faire pointer vers votre base de données SQL Azure. Une fois la réplication transactions disparaît des modifications reste sur votre base de données locale et toutes vos applications pointez sur base de données Azure, vous pouvez désinstaller la réplication transactions. Votre base de données SQL Azure devient votre système de production.

 ![Diagramme SeedCloudTR](./media/sql-database-cloud-migrate/SeedCloudTR.png)

## <a name="transactional-replication-requirements"></a>Configuration requise de réplication transactions

Transactions réplication est une technologie intégrées avec SQL Server depuis SQL Server 6.5. Il est une technologie mature et la plus performante que la plupart des bases de données savent avec lesquelles ils ont expérience. Avec les [SQL Server 2016](https://www.microsoft.com/en-us/cloud-platform/sql-server), il est possible de configurer votre base de données SQL Azure en tant qu' [abonné de réplication transactions](https://msdn.microsoft.com/library/mt589530.aspx) à votre composition en local. L’expérience que vous obtenez la configuration de Management Studio est le même que si vous avez configuré un abonné de réplication transactions sur un serveur local. Prise en charge de ce scénario est pris en charge lors de l’éditeur et le distributeur sont au moins une des versions de SQL Server suivantes :

 - SQL Server 2016 et versions ultérieures 
 - SQL Server 2014 SP1 CU3 et versions ultérieures
 - SQL Server 2014 RTM CU10 et versions ultérieures
 - SQL Server 2012 Service Pack 2 CU8 et versions ultérieures
 - SQL Server 2012 Service Pack 3 et versions ultérieures


> [AZURE.IMPORTANT] Utiliser la dernière version de SQL Server Management Studio doit pour rester synchronisé avec les mises à jour Microsoft Azure et base de données SQL. Les versions antérieures de SQL Server Management Studio ne peut pas configurer la base de données SQL en tant qu’abonné. [Mettre à jour de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="next-steps"></a>Étapes suivantes

- [Version la plus récente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Version la plus récente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [SQL Server 2016](https://www.microsoft.com/en-us/cloud-platform/sql-server)

## <a name="additional-resources"></a>Ressources supplémentaires

- [Transactions réplication](https://msdn.microsoft.com/library/mt589530.aspx)
- [V12 de base de données SQL](sql-database-v12-whats-new.md)
- [Transact-SQL partiellement ou non prises en charge de fonctions](sql-database-transact-sql-information.md)
- [Migrer des bases de données SQL Server à l’aide de l’Assistant Migration SQL Server](http://blogs.msdn.com/b/ssma/)
