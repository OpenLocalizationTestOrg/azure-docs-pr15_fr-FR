<properties
   pageTitle="Types de données pour les tables SQL Data Warehouse | Microsoft Azure"
   description="Prise en main des types de données pour les tableaux de magasin de données SQL Azure."
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
   ms.date="06/29/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="data-types-for-tables-in-sql-data-warehouse"></a>Types de données pour les tables dans Data Warehouse SQL

> [AZURE.SELECTOR]
- [Vue d’ensemble][]
- [Types de données][]
- [Distribuer][]
- [Index][]
- [Partition][]
- [Statistiques][]
- [Temporaire][]

Data Warehouse SQL prend en charge les plus fréquemment utilisés des types de données.  Voici une liste des types de données pris en charge par Data Warehouse SQL.  Pour plus d’informations sur le type de données prennent en charge, voir [créer la table][].

|**Types de données pris en charge**|||
|---|---|---|
[bigint][]|[Decimal][]|[smallint][]|
[en nombre binaire.][]|[Flottement][]|[smallmoney][]|
[bit][]|[ent][]|[sysname][]|
[car][]|[argent][]|[heure][]|
[date][]|[NCHAR][]|[tinyint][]|
[date/heure][]|[nvarchar][]|[uniqueidentifier][]|
[datetime2][]|[réel][]|[varbinary][]|
[DateTimeOffset][]|[smalldatetime][]|[varchar][]|


## <a name="data-type-best-practices"></a>Meilleures pratiques de type de données

 Lorsque vous définissez vos types de colonnes, à l’aide du plus petit type de données qui prend en charge vos données pour améliorer les performances des requêtes. Ceci est particulièrement important pour les colonnes car et VARCHAR. Si la valeur la plus longue dans une colonne est 25 caractères, puis définissez votre colonne en tant que VARCHAR(25). Évitez de définir toutes les colonnes de caractères à une longueur de grande taille par défaut. En outre, de définir des colonnes en tant que VARCHAR lorsque c’est tout ce qui est nécessaire au lieu d’utiliser [NVARCHAR][].  Utilisez nvarchar (4000) ou VARCHAR(8000) lorsque cela est possible à la place nvarchar (max) ou varchar (max).

## <a name="polybase-limitation"></a>Limitation Polybase

Si vous utilisez Polybase pour charger vos tables, définissez vos tables la taille maximale de lignes possibles, y compris la longueur totale des colonnes de longueur variable, ne doit pas dépasser 32 767 octets.  Pendant que vous pouvez définir une ligne avec les données de longueur variable qui peuvent dépasser cette largeur et charger les lignes avec BCP, vous ne serez pas en mesure d’utiliser Polybase pour charger ces données.  Prise en charge Polybase de lignes large est bientôt ajouté.

## <a name="unsupported-data-types"></a>Types de données non prises en charge

Si vous migrez votre base de données à partir d’une autre plateforme SQL comme base de données SQL Azure, que vous migrez, vous pouvez rencontrer certains types de données qui ne sont pas prises en charge sur Data Warehouse SQL.  Voici les types de données non prises en charge ainsi que d’autres solutions que vous pouvez utiliser à la place de types de données non prises en charge.

|Type de données|Solution de contournement|
|---|---|
|[géométrie][]|[varbinary][]|
|[emplacement géographique][]|[varbinary][]|
|[hierarchyid][]|[nvarchar][] (4 000)|
|[image][ntext,text,image]|[varbinary][]|
|[texte][ntext,text,image]|[varchar][]|
|[ntext][ntext,text,image]|[nvarchar][]|
|[sql_variant][]|Fractionner la colonne en plusieurs colonnes fortement typées.|
|[table][]|Convertir en tables temporaires.|
|[horodatage][]|Retravaillez code afin d’utiliser [datetime2][] et `CURRENT_TIMESTAMP` fonction.  Seules les constantes sont prises en charge en tant que paramètres par défaut, par conséquent current_timestamp ne peut pas être définie comme une contrainte par défaut. Si vous avez besoin migrer les valeurs de version de ligne à partir d’une colonne d’horodatage tapé ensuite utiliser [binaire][](8) ou [VARBINARY][binaire](8) pour les valeurs de la version de ligne pas NULL ou NULL.|
|[XML][]|[varchar][]|
|[types définis par l’utilisateur][]|reconvertir en leurs types natifs lorsque cela est possible|
|valeurs par défaut|valeurs par défaut prend en charge littéraux et des constantes uniquement.  Consultez la rubrique non ou des fonctions, telles que `GETDATE()` ou `CURRENT_TIMESTAMP`, ne sont pas pris en charge.|

La ci-dessous SQL peuvent être exécutés sur votre base de données SQL pour identifier les colonnes qui sont ne pas prise en charge par Azure SQL Data Warehouse :

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','timestamp','xml')
 AND  y.[is_user_defined] = 1;
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, voir les articles sur la [Vue d’ensemble de la Table][vue d’ensemble], [distribuer une Table][répartir], [indexation d’une Table],[Index], [partition une Table][Partition], [Statistiques de la conservation de la Table][statistiques] et [Tables temporaires][temporaire].  Pour plus d’informations sur les meilleures pratiques, voir [Recommandations entrepôt de données SQL][].

<!--Image references-->

<!--Article references-->
[Vue d’ensemble]: ./sql-data-warehouse-tables-overview.md
[Types de données]: ./sql-data-warehouse-tables-data-types.md
[Distribuer]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistiques]: ./sql-data-warehouse-tables-statistics.md
[Temporaire]: ./sql-data-warehouse-tables-temporary.md
[Meilleures pratiques SQL données entrepôt]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->

<!--Other Web references-->
[créer la table]: https://msdn.microsoft.com/library/mt203953.aspx
[bigint]: https://msdn.microsoft.com/library/ms187745.aspx
[en nombre binaire.]: https://msdn.microsoft.com/library/ms188362.aspx
[bit]: https://msdn.microsoft.com/library/ms177603.aspx
[car]: https://msdn.microsoft.com/library/ms176089.aspx
[date]: https://msdn.microsoft.com/library/bb630352.aspx
[date/heure]: https://msdn.microsoft.com/library/ms187819.aspx
[datetime2]: https://msdn.microsoft.com/library/bb677335.aspx
[DateTimeOffset]: https://msdn.microsoft.com/library/bb630289.aspx
[Decimal]: https://msdn.microsoft.com/library/ms187746.aspx
[Flottement]: https://msdn.microsoft.com/library/ms173773.aspx
[géométrie]: https://msdn.microsoft.com/library/cc280487.aspx
[emplacement géographique]: https://msdn.microsoft.com/library/cc280766.aspx
[hierarchyid]: https://msdn.microsoft.com/library/bb677290.aspx
[ent]: https://msdn.microsoft.com/library/ms187745.aspx
[argent]: https://msdn.microsoft.com/library/ms179882.aspx
[NCHAR]: https://msdn.microsoft.com/library/ms186939.aspx
[nvarchar]: https://msdn.microsoft.com/library/ms186939.aspx
[ntext,text,image]: https://msdn.microsoft.com/library/ms187993.aspx
[réel]: https://msdn.microsoft.com/library/ms173773.aspx
[smalldatetime]: https://msdn.microsoft.com/library/ms182418.aspx
[smallint]: https://msdn.microsoft.com/library/ms187745.aspx
[smallmoney]: https://msdn.microsoft.com/library/ms179882.aspx
[sql_variant]: https://msdn.microsoft.com/library/ms173829.aspx
[sysname]: https://msdn.microsoft.com/library/ms186939.aspx
[table]: https://msdn.microsoft.com/library/ms175010.aspx
[heure]: https://msdn.microsoft.com/library/bb677243.aspx
[horodatage]: https://msdn.microsoft.com/library/ms182776.aspx
[tinyint]: https://msdn.microsoft.com/library/ms187745.aspx
[uniqueidentifier]: https://msdn.microsoft.com/library/ms187942.aspx
[varbinary]: https://msdn.microsoft.com/library/ms188362.aspx
[varchar]: https://msdn.microsoft.com/library/ms186939.aspx
[XML]: https://msdn.microsoft.com/library/ms187339.aspx
[types définis par l’utilisateur]: https://msdn.microsoft.com/library/ms131694.aspx
