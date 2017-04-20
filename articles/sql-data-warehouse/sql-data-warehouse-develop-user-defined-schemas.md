<properties
   pageTitle="Schémas définis par l’utilisateur dans Data Warehouse SQL | Microsoft Azure"
   description="Conseils pour l’utilisation des schémas Transact-SQL Azure SQL Data Warehouse pour développer des solutions."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/14/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="user-defined-schemas-in-sql-data-warehouse"></a>Schémas définis par l’utilisateur dans Data Warehouse SQL

Traditionnel data warehouse utilise souvent des bases de données séparées pour créer des limites d’application basées sur la charge de travail, domaine ou sécurité. Par exemple, un magasin de données SQL Server traditionnel peut-être inclure une base de données intermédiaire, une base de données data warehouse et certaines données mart les bases de données. Dans la topologie de chaque base de données fonctionne comme une charge de travail et une limite de sécurité dans l’architecture.

En revanche, SQL Data Warehouse s’exécute la charge de travail de magasin de données entière au sein d’une base de données. Jointures ne sont pas autorisés croisée de base de données. Par conséquent, SQL Data Warehouse attend toutes les tables utilisées par le magasin pour être stockées dans la base d’un données.

> [AZURE.NOTE] SQL Data Warehouse ne reconnaît pas les requêtes de base de données croisée d’aucune sorte. Par conséquent, les mises en œuvre entrepôt des données qui s’appuient sur ce modèle doit être mis à jour.

## <a name="recommendations"></a>Recommandations

Il s’agit des recommandations pour la consolidation de charges de travail, la sécurité, domaine et des limites fonctionnelles à l’aide de schémas définis par l’utilisateur

1. Utiliser une base de données SQL Data Warehouse pour exécuter votre charge de travail de magasin de données entier
2. Consolider votre environnement de data warehouse existante pour utiliser une base de données SQL Data Warehouse
3. Exploiter les **schémas définis par l’utilisateur** pour fournir la limite précédemment implémentée à l’aide de bases de données.

Si schémas définis par l’utilisateur n’ont pas été utilisés précédemment vous avez un projet vide. Simplement utiliser l’ancien nom de base de données comme base pour vos schémas définis par l’utilisateur dans la base de données SQL Data Warehouse.

Si les schémas ont déjà été utilisés vous avez plusieurs options :

1. Supprimer les noms de schéma hérité et mettre à jour
2. Conserver les noms de schéma héritées en attente avant le nom du schéma héritées au nom de table
3. Conserver les noms de schéma héritées en mettant en œuvre des vues sur la table dans un schéma supplémentaire pour recréer l’ancienne structure de schéma.

> [AZURE.NOTE] Première inspection option 3 peut ressembler à l’option plus attrayante. Toutefois, complique est dans le détail. Affichages sont en lecture seule dans SQL Data Warehouse. Toute modification des données ou du tableau devront être effectuée par rapport à la table de base. L’option 3 inclut également un calque de vues dans votre système. Vous souhaiterez peut-être réfléchir cela supplémentaires si vous utilisez déjà des vues dans votre architecture.


### <a name="examples"></a>Exemples :

Mettre en œuvre des schémas définis par l’utilisateur basées sur des noms de base de données

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the data warehouse
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create data warehouse tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Conserver les noms de schémas hérités par pré-mise en attente au nom de table. Utilisez des schémas pour la limite de charge de travail.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the data warehouse boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Conserver les noms de schéma héritées à l’aide des affichages

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the data warehouse boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base data warehouse tables in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM    [edw].customer
;
```

> [AZURE.NOTE] Les modifications apportées à la stratégie de schéma devant consulter le modèle de sécurité pour la base de données. Dans de nombreux cas vous pourrez peut-être simplifier le modèle de sécurité en définissant des autorisations au niveau du schéma. Si les autorisations plus granulaires sont requises vous pouvez utiliser les rôles de base de données.

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir d’autres conseils de développement, voir [vue d’ensemble du développement][].

<!--Image references-->

<!--Article references-->
[vue d’ensemble de développement]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
