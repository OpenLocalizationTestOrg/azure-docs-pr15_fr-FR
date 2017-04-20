<properties
   pageTitle="Créer un Data Warehouse SQL avec TSQL | Microsoft Azure"
   description="Découvrez comment créer un magasin de données SQL Azure avec TSQL"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/24/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="create-a-sql-data-warehouse-database-by-using-transact-sql-tsql"></a>Créer une base de données SQL Data Warehouse à l’aide de Transact-SQL (TSQL).

> [AZURE.SELECTOR]
- [Portail Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Cet article vous explique comment créer un SQL Data Warehouse à l’aide de T-SQL.

## <a name="prerequisites"></a>Conditions préalables

Pour commencer, vous devez : 

- **Compte Azure**: visite [Azure la version d’évaluation gratuite][] ou [MSDN Azure crédits][] pour créer un compte.
- **Azure SQL server**: pour plus d’informations, consultez [créer un serveur logique de base de données SQL Azure avec le portail Azure][] ou [créer un serveur logique de base de données SQL Azure avec PowerShell][] .
- **Groupe de ressources**: utilisez le même groupe de ressources que votre serveur SQL Azure ou Découvrez [comment créer un groupe de ressources][].
- **Environnement d’exécution T-SQL**: vous pouvez utiliser [Visual Studio][Installing Visual Studio and SSDT], [sqlcmd][]ou [SSMS][] pour exécuter T-SQL.

> [AZURE.NOTE] Création d’un Data Warehouse SQL peut entraîner un nouveau service facturable.  Pour plus d’informations sur les tarifs, consultez [Data Warehouse SQL tarification][] .

## <a name="create-a-database-with-visual-studio"></a>Créer une base de données avec Visual Studio

Si vous débutez dans Visual Studio, voir l’article [Requête Azure SQL Data Warehouse (Visual Studio)][].  Pour commencer, ouvrez l’Explorateur d’objets SQL Server dans Visual Studio et se connecter au serveur qui héberge votre base de données SQL Data Warehouse.  Une fois connecté, vous pouvez créer un SQL Data Warehouse en exécutant la commande SQL suivante sur la base de données **principale** .  Cette commande crée la base de données MySqlDwDb avec un objectif de DW400 de Service et autoriser la base de données atteindre une taille maximale de 10 To.

```sql
CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## <a name="create-a-database-with-sqlcmd"></a>Créer une base de données avec sqlcmd

Par ailleurs, vous pouvez exécuter la même commande avec sqlcmd en exécutant la commande suivante à l’invite.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

Le classement par défaut n’est ne pas spécifié est SQL_Latin1_General_CP1_CI_AS assembler.  La `MAXSIZE` peut être compris entre 250 Go et 240 to.  La `SERVICE_OBJECTIVE` peut être compris entre DW100 et DW2000 [DWU][].  Pour obtenir la liste de toutes les valeurs valides, consultez la documentation MSDN pour [Créer une base de données][].  Le MAXSIZE et SERVICE_OBJECTIVE peuvent être modifiées avec une commande T-SQL [ALTER DATABASE][] .  Le classement d’une base de données ne peut pas être modifié après sa création.   Faire preuve de prudence doit être utilisé lors de la modification de la SERVICE_OBJECTIVE que la modification DWU provoque un redémarrage des services, qui annule toutes les requêtes à la volée.  Modification MAXSIZE ne redémarre pas les services qu’il est juste une opération de métadonnées simple.

## <a name="next-steps"></a>Étapes suivantes

Une fois votre Data Warehouse SQL a terminé la mise en service que vous pouvez [charger les exemples de données][] ou Découvrez comment [développer][], [charger][]ou [migrer][].

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[how to create a SQL Data Warehouse from the Azure portal]: sql-data-warehouse-get-started-provision.md
[Requête SQL Azure Data Warehouse (Visual Studio)]: sql-data-warehouse-query-visual-studio.md
[migrer]: sql-data-warehouse-overview-migrate.md
[développer]: sql-data-warehouse-overview-develop.md
[charger]: sql-data-warehouse-overview-load.md
[charger les exemples de données]: sql-data-warehouse-load-sample-databases.md
[Création d’un serveur logique de base de données SQL Azure avec le portail Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Création d’un serveur logique de base de données SQL Azure avec PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[comment créer un groupe de ressources]: ../resource-group-template-deploy-portal.md#create-resource-group
[Installing Visual Studio and SSDT]: sql-data-warehouse-install-visual-studio.md
[Sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--MSDN references--> 
[CRÉER LA BASE DE DONNÉES]: https://msdn.microsoft.com/library/mt204021.aspx
[MODIFIER LA BASE DE DONNÉES]: https://msdn.microsoft.com/library/mt204042.aspx
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx

<!--Other Web references-->
[Tarifs Data Warehouse SQL]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Version d’évaluation gratuite Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN crédits Azure]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
