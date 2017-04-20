<properties
   pageTitle="Migrer votre schéma vers SQL Data Warehouse | Microsoft Azure"
   description="Conseils pour migrer votre schéma vers Azure SQL Data Warehouse pour développer des solutions."
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
   ms.date="08/25/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="migrate-your-schema-to-sql-data-warehouse"></a>Migrer votre schéma vers Data Warehouse SQL#

Les valeurs de synthèse suivantes vous permettent de comprendre les différences entre SQL Server et Data Warehouse SQL pour vous aider à migrer votre base de données.

## <a name="table-migration"></a>Migration de table

Lors de la migration de vos tables, vous souhaiterez pour vous familiariser avec les fonctions de tableau de tables SQL Data Warehouse.  La [vue d’ensemble du tableau][] est un bon point de départ.  Cet article présente les considérations les plus importantes lorsque vous créez une table tels que les statistiques de la table, distribution, partition et l’indexation.  Il traite également certaines [fonctionnalités de tableau non prises en charge][] et les solutions de contournement.

Data Warehouse SQL prend en charge les types de données d’entreprise courants.  Consultez l’article de [types de données][] pour une liste des prises en charge et les [types de données non prises en charge][].  Cet article de [types de données][] contient également une requête pour identifier les [types de données non prises en charge][].  Lorsque vous convertissez vos types de données, veillez à examiner les [meilleures pratiques de type de données][].

## <a name="next-steps"></a>Étapes suivantes
Une fois que vous avez réussi à migrer votre schéma de base de données vers SQL Data Warehouse, passez à un des articles suivants :

- [Migrer vos données][]
- [Migrer votre code][]

Pour plus d’informations sur les meilleures pratiques Data Warehouse SQL, voir l’article [recommandations][] .

<!--Image references-->

<!--Article references-->
[Migrer votre code]: ./sql-data-warehouse-migrate-code.md
[Migrer vos données]: ./sql-data-warehouse-migrate-data.md
[meilleures pratiques]: ./sql-data-warehouse-best-practices.md
[vue d’ensemble du tableau]: ./sql-data-warehouse-tables-overview.md
[fonctionnalités de tableau non prises en charge]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[types de données]: ./sql-data-warehouse-tables-data-types.md
[types de données non prises en charge]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[meilleures pratiques de type de données]: ./sql-data-warehouse-tables-data-types.md#data-type-best-practices

<!--MSDN references-->


<!--Other Web references-->
