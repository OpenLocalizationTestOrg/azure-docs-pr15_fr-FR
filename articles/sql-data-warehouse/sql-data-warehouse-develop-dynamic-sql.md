<properties
   pageTitle="SQL dynamique SQL Data Warehouse | Microsoft Azure"
   description="Conseils pour l’utilisation dynamique SQL Azure SQL Data Warehouse pour développer des solutions."
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

# <a name="dynamic-sql-in-sql-data-warehouse"></a>Instructions SQL dynamiques dans Data Warehouse SQL
Lorsque vous développez le code de l’application pour Data Warehouse SQL, vous devrez peut-être utiliser sql dynamique afin de fournir des solutions flexibles, génériques et modulaires. SQL Data Warehouse ne reconnaît pas les types de données blob pour le moment. Cela peut limiter la taille de votre chaînes sous forme de types d’objets blob sont types varchar (max) et nvarchar (max). Si vous avez utilisé ces types de code de l’application lors de la création de très grandes chaînes, vous devez scinder le code en segments et utilisez plutôt l’instruction d’exécution.

Un exemple simple :

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Si la chaîne est courte, vous pouvez utiliser [sp_executesql][] comme d’habitude.

> [AZURE.NOTE] Instructions exécutées en tant que SQL dynamique seront toujours soumis à toutes les règles de validation TSQL.

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir d’autres conseils de développement, voir [vue d’ensemble du développement][].

<!--Image references-->

<!--Article references-->
[vue d’ensemble de développement]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[sp_executesql]: https://msdn.microsoft.com/library/ms188001.aspx

<!--Other Web references-->
