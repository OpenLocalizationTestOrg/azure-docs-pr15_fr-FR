
<properties
   pageTitle="Exporter une base de données SQL Server vers un fichier à DOS à l’aide de SQL Server Management Studio | Microsoft Azure"
   description="Base de données de SQL Microsoft Azure, migration de base de données, base de données d’exportation, exporter le fichier à DOS, Assistant Exporter les données couche Application"
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
   ms.workload="data-management"
   ms.date="08/16/2016"
   ms.author="carlrab"/>

# <a name="export-a-sql-server-database-to-a-bacpac-file-using-sql-server-management-studio"></a>Exporter une base de données SQL Server vers un fichier à DOS à l’aide de SQL Server Management Studio

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)

 
Cet article vous explique comment exporter une base de données SQL Server vers un fichier [à DOS](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) à l’aide de l’Assistant Exportation d’une Application de niveau données dans SQL Server Management Studio. 

1. Vérifiez que vous disposez de la dernière version de SQL Server Management Studio. Nouvelles versions de Management Studio sont mises à jour mensuelles doit rester synchronisé avec les mises à jour au portail Azure.

     > [AZURE.IMPORTANT] Il est recommandé d’utiliser toujours la dernière version de Management Studio doit pour rester synchronisé avec les mises à jour Microsoft Azure et base de données SQL. [Mettre à jour de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Ouvrez Management Studio et connectez-vous à votre base de données source dans l’Explorateur d’objets.

    ![Exporter une application couche de données dans le menu de tâches](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

3. Avec le bouton droit de la base de données source dans l’Explorateur d’objets, pointez sur **tâches**, puis cliquez sur **Exporter les applications de niveau données**

    ![Exporter une application couche de données dans le menu de tâches](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. Dans l’Assistant Exportation, configurez l’exportation pour enregistrer le fichier à DOS soit un emplacement de disque local ou sur un Azure blob. L’exporté à DOS inclut toujours le schéma de base de données complète et, par défaut, les données à partir de toutes les tables. Utilisez l’onglet Avancé si vous voulez exclure des données à partir des certaines ou toutes les tables. Vous pouvez, par exemple, choisissez Exporter uniquement les données pour les tables de référence, plutôt que de toutes les tables.

***Important*** Lorsque vous exportez un à DOS à Azure blob storage, utilisez le stockage standard. L’importation à un DOS à partir du stockage premium n’est pas pris en charge.

    ![Export settings](./media/sql-database-cloud-migrate/MigrateUsingBACPAC02.png)


## <a name="next-steps"></a>Étapes suivantes

- [Version la plus récente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Version la plus récente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Importer un à DOS sur base de données SQL Azure à l’aide de SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Importer un à DOS à SqlPackage de base de données SQL Azure](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Importer un à DOS au portail Azure de base de données SQL Azure](sql-database-import.md)
- [Importer un à DOS PowerShell de base de données SQL Azure](sql-database-import-powershell.md)

## <a name="additional-resources"></a>Ressources supplémentaires

- [V12 de base de données SQL](sql-database-v12-whats-new.md)
- [Transact-SQL partiellement ou non prises en charge de fonctions](sql-database-transact-sql-information.md)
- [Migrer des bases de données SQL Server à l’aide de l’Assistant Migration SQL Server](http://blogs.msdn.com/b/ssma/)
