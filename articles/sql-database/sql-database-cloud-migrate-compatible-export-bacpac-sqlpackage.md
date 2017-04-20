<properties
   pageTitle="Exporter une base de données SQL Server vers un fichier à DOS à l’aide de SqlPackage | Microsoft Azure"
   description="Base de données de SQL Microsoft Azure, migration de base de données, base de données d’exportation, exporter le fichier à DOS, sqlpackage"
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

# <a name="export-a-sql-server-database-to-a-bacpac-file-using-sqlpackage"></a>Exporter une base de données SQL Server vers un fichier à DOS à l’aide de SqlPackage

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)

Cet article vous explique comment exporter une base de données SQL Server vers un fichier [à DOS](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) à l’aide de l’utilitaire [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) . Cet utilitaire est fourni avec les dernières versions de [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) et [SQL Server Data Tools pour Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), ou vous pouvez télécharger la version la plus récente de [SqlPackage](https://www.microsoft.com/en-us/download/details.aspx?id=53876) directement à partir du centre de téléchargement Microsoft.

1. Ouvrez une invite de commandes et modifier un répertoire contenant l’utilitaire de ligne de commande sqlpackage.exe - cet utilitaire est fourni avec Visual Studio et SQL Server. Utiliser la recherche sur votre ordinateur pour rechercher le chemin d’accès dans votre environnement.
2. Exécutez la commande suivante sqlpackage.exe avec les arguments suivants pour votre environnement :

    « sqlpackage.exe /Action:Export /ssn : /sdn < nom_serveur > : /tf < nom_base_de_données > : < target_file >

  	| Argument  | Description  |
  	|---|---|
  	| < nom_serveur >  | nom du serveur source  |
  	| < nom_base_de_données >  | nom de la base de données source  |
  	| < target_file >  | nom de fichier et un emplacement pour le fichier à DOS  |

    ![Exporter une application couche de données dans le menu de tâches](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

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
