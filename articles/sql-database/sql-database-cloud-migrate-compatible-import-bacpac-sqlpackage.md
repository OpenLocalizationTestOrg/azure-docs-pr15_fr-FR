<properties
   pageTitle="Importer dans la base de données SQL à partir d’un fichier à DOS à l’aide de SqlPackage"
   description="Base de données de SQL Microsoft Azure, migration de base de données, Importer base de données, importer un fichier à DOS, sqlpackage"
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

# <a name="import-to-sql-database-from-a-bacpac-file-using-sqlpackage"></a>Importer dans la base de données SQL à partir d’un fichier à DOS à l’aide de SqlPackage

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Portail Azure](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

Cet article vous explique comment importer dans la base de données SQL à partir d’un fichier [à DOS](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) à l’aide de l’utilitaire [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) . Cet utilitaire est fourni avec les dernières versions de [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) et [SQL Server Data Tools pour Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), ou vous pouvez télécharger la version la plus récente de [SqlPackage](https://www.microsoft.com/en-us/download/details.aspx?id=53876) directement à partir du centre de téléchargement Microsoft.


> [AZURE.NOTE] Les étapes suivantes impliquent que vous avez déjà déployé un serveur de base de données SQL, avez besoin les informations de connexion et avez vérifié que votre base de données source est compatible.

## <a name="import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage"></a>Importer à partir d’un fichier à DOS dans la base de données SQL Azure à l’aide de SqlPackage

Procédez comme suit pour utiliser l’utilitaire de ligne de commande [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) pour importer une base de données SQL Server compatible (ou une base de données SQL Azure) à partir d’un fichier à DOS.

> [AZURE.NOTE] Les étapes suivantes impliquent que vous avez déjà déployé un serveur de base de données SQL Azure et que vous avez besoin des informations de connexion.

1. Ouvrez une invite de commandes et modifier un répertoire contenant l’utilitaire de ligne de commande sqlpackage.exe - cet utilitaire est fourni avec Visual Studio et SQL Server.
2. Exécutez la commande suivante sqlpackage.exe avec les arguments suivants pour votre environnement :

    `sqlpackage.exe /Action:Import /tsn:< server_name > /tdn:< database_name > /tu:< user_name > /tp:< password > /sf:< source_file >`

  	| Argument  | Description  |
  	|---|---|
  	| < nom_serveur >  | nom du serveur cible  |
  	| < nom_base_de_données >  | nom de base de données cible  |
  	| < nom_utilisateur >  | le nom d’utilisateur dans le serveur cible |
  	| < mot de passe >  | mot de passe de l’utilisateur  |
  	| < source_file >  | le nom de fichier et un emplacement pour le fichier à DOS importé  |

    ![Exporter une application couche de données dans le menu de tâches](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01c.png)

## <a name="next-steps"></a>Étapes suivantes

- [Version la plus récente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Version la plus récente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Ressources supplémentaires

- [V12 de base de données SQL](sql-database-v12-whats-new.md)
- [Transact-SQL partiellement ou non prises en charge de fonctions](sql-database-transact-sql-information.md)
- [Migrer des bases de données SQL Server à l’aide de l’Assistant Migration SQL Server](http://blogs.msdn.com/b/ssma/)
