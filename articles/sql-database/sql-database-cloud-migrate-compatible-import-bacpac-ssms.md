<properties
   pageTitle="Migrer une base de données SQL Server pour la base de données SQL Azure | Microsoft Azure"
   description="Base de données Microsoft Azure SQL, base de données déployez, migration de base de données, base de données de l’importation, exportation de base de données, l’Assistant migration"
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

# <a name="import-from-bacpac-to-sql-database-using-ssms"></a>Importer à partir d’à DOS à l’aide de SSMS de la base de données SQL

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Portail Azure](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

Cet article vous explique comment importer à partir d’un fichier [à DOS](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) dans la base de données SQL à l’aide de l’Assistant Exportation d’une Application de niveau données dans SQL Server Management Studio.

> [AZURE.NOTE] Les étapes suivantes impliquent que vous avez déjà déployé votre instance logique SQL Azure et que vous avez besoin les informations de connexion.

1. Vérifiez que vous disposez de la dernière version de SQL Server Management Studio. Nouvelles versions de Management Studio sont mises à jour mensuelles doit rester synchronisé avec les mises à jour au portail Azure.

     > [AZURE.IMPORTANT] Il est recommandé d’utiliser toujours la dernière version de Management Studio doit pour rester synchronisé avec les mises à jour Microsoft Azure et base de données SQL. [Mettre à jour de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Se connecter à votre serveur de base de données SQL Azure, cliquez sur le dossier **bases de données** , cliquez sur **Importer des données de couche Application...**

    ![Élément de menu Importer des données de couche application](./media/sql-database-cloud-migrate/MigrateUsingBACPAC03.png)

3.  Pour créer la base de données dans la base de données SQL Azure, importer un fichier à dos de votre disque local ou sélectionnez le compte de stockage Azure et le conteneur sur lequel vous avez téléchargé votre fichier à DOS.

    ![Paramètres d’importation](./media/sql-database-cloud-migrate/MigrateUsingBACPAC04.png)

     > [AZURE.IMPORTANT] Lorsque vous importez un à DOS depuis le stockage blob Azure, utilisez le stockage standard. L’importation à un DOS à partir du stockage premium n’est pas pris en charge.

4.  Indiquez le **nouveau nom de base de données** pour la base de données sur base de données SQL Azure, définissez l' **Édition de Microsoft Azure SQL de base de données** (niveau de service), **la taille maximale de base de données**et **Objectif de Service** (niveau de performance).

    ![Paramètres de base de données](./media/sql-database-cloud-migrate/MigrateUsingBACPAC05.png)

5.  Cliquez sur **suivant** , puis sur **Terminer** pour importer le fichier à DOS dans une base de données sur le serveur de base de données SQL Azure.

6. Explorateur d’objets, vous connecter à votre base de données a été déplacée de votre serveur de base de données SQL Azure.

6.  À l’aide du portail Azure, permet d’afficher votre base de données et à ses propriétés.

## <a name="next-steps"></a>Étapes suivantes

- [Version la plus récente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Version la plus récente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Ressources supplémentaires

- [V12 de base de données SQL](sql-database-v12-whats-new.md)
- [Transact-SQL partiellement ou non prises en charge de fonctions](sql-database-transact-sql-information.md)
- [Migrer des bases de données SQL Server à l’aide de l’Assistant Migration SQL Server](http://blogs.msdn.com/b/ssma/)
