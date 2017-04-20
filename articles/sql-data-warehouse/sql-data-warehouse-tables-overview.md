<properties
   pageTitle="Vue d’ensemble des tables SQL Data Warehouse | Microsoft Azure"
   description="Mise en route des Tables de magasin de données SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/04/2016"
   ms.author="sonyama;barbkess;jrj"/>

# <a name="overview-of-tables-in-sql-data-warehouse"></a>Vue d’ensemble des tables SQL Data Warehouse

> [AZURE.SELECTOR]
- [Vue d’ensemble][]
- [Types de données][]
- [Distribuer][]
- [Index][]
- [Partition][]
- [Statistiques][]
- [Temporaire][]

Prise en main créez des tables dans SQL Data Warehouse est simple.  La syntaxe de [Créer une TABLE][] de base suit la syntaxe courantes sans doute déjà familiarisé avec de travailler avec d’autres bases de données.  Pour créer un tableau, vous devez simplement un nom à votre table, le nom de vos colonnes et définir les types de données pour chaque colonne.  Si vous avez créer les tables dans d’autres bases de données, cela doit ressembler très familier.

```sql  
CREATE TABLE Customers (FirstName VARCHAR(25), LastName VARCHAR(25))
 ``` 

L’exemple ci-dessus crée une table nommée Customers avec deux colonnes, prénom et nom.  Chaque colonne est définie avec un type de données de VARCHAR(25), qui limite les données à 25 caractères.  Les attributs d’une table, ainsi que d’autres personnes, fondamentaux sont principalement différente de celle des autres bases de données.  Types de données sont définis pour chaque colonne et garantissant l’intégrité des données.  À partir d’index peut être ajoutés pour améliorer les performances en réduisant e/s.  Partition peut être ajouté pour améliorer les performances lorsque vous avez besoin modifier les données.

[Renommer] [ RENAME] une table SQL Data Warehouse ressemble à ceci :

```sql  
RENAME OBJECT Customer TO CustomerOrig; 
 ```

## <a name="distributed-tables"></a>Tables distribués

Un nouvel attribut fondamentaux introduit par des systèmes distribués comme SQL Data Warehouse est la **colonne de distribution**.  La colonne distribution est très qu’il ressemble.  Il s’agit de la colonne qui détermine comment distribuer ou diviser vos données en coulisses.  Lorsque vous créez une table sans en spécifiant la colonne de distribution, la table est distribuée automatiquement à l’aide de **alternées**.  Tandis qu’alternées tables peuvent être nécessaires dans certains scénarios, définir des colonnes de distribution peut réduire déplacement de données au cours des requêtes, et donc optimisation des performances.  Voir [distribuer une Table][répartir] pour en savoir plus sur la sélection d’une colonne de distribution.

## <a name="indexing-and-partitioning-tables"></a>Indexation et partition tables

Lorsque vous devenir plus avancées à l’aide de Data Warehouse SQL et que vous voulez optimiser les performances, vous souhaiterez en savoir plus sur la création de tableau.  Pour plus d’informations, voir les articles sur [Les Types de données de tableau][Types de données], [distribuer une Table][répartir], [l’indexation d’une Table][Index] et [partition d’une Table de][Partition].

## <a name="table-statistics"></a>Statistiques de la table

Statistiques sont un extrêmement importants pour obtenir les meilleures performances déconnecter votre Data Warehouse SQL.  Étant donné que SQL Data Warehouse ne pas encore crée automatiquement et statistiques de mise à jour pour vous, comme vous pouvez provenir d’attendre dans la base de données SQL Azure, lisez notre article sur les [statistiques][] peuvent être un des articles les plus importantes vous lisez afin d’optimiser les performances de vos requêtes.

## <a name="temporary-tables"></a>Tables temporaires

Tables temporaires sont des tables qui existent pendant la durée de l’ouverture de session uniquement et ne peut pas être vu par les autres utilisateurs.  Tables temporaires peuvent être un bon moyen pour empêcher d’autres personnes à voir vos résultats temporaires et également réduire la nécessité de nettoyage de disque.  Dans la mesure où les tables temporaires utilisent également stockage local, ils peuvent proposer des performances plus rapides pour certaines opérations.  Voir les articles[temporaire] [Table temporaire]pour plus d’informations sur les tables temporaires.

## <a name="external-tables"></a>Tables externes

Tables externes, également connu sous le nom des tables Polybase, sont des tables qui peuvent être interrogées à partir de SQL Data Warehouse, mais point à des données externes à partir de SQL Data Warehouse.  Par exemple, vous pouvez créer une table externe qui pointe vers des fichiers sur le stockage d’objets Blob Azure.  Pour plus d’informations sur la création et la requête d’une table externe, voir [charger les données avec Polybase][].  

## <a name="unsupported-table-features"></a>Fonctionnalités de tableau non prises en charge

Tandis que SQL Data Warehouse contient de nombreuses fonctionnalités de tableau même offertes par d’autres bases de données, il existe certaines fonctionnalités qui ne sont pas pris en charge.  Voici une liste de certaines des fonctionnalités de tableaux qui ne sont pas pris en charge.

| Fonctionnalités non prises en charge |
| --- |
|[Propriété d’identité][] (voir [Attribution de solution de contournement substitution clé][])|
|Clé primaire, les clés étrangères, Unique et vérifier [Les contraintes de Table][]|
|[Index uniques][]|
|[Colonnes calculées][]|
|[Colonnes éparses][]|
|[Types définis par l’utilisateur][]|
|[Séquence][]|
|[Déclencheurs][]|
|[Vues indexées][]|
|[Synonymes][]|

## <a name="table-size-queries"></a>Requêtes de taille de tableau

Une méthode simple pour identifier espace et les lignes consommées par une table dans chacune des 60 répartitions, consiste à utiliser [DBCC PDW_SHOWSPACEUSED][].

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Toutefois, à l’aide des commandes DBCC peut être très limitée.  Vues de gestion dynamiques (DMV) vous permettra d’afficher davantage de détails ainsi que de vous donner le contrôle de plus de résultats de la requête.  Commencez par créer cet affichage, qui sera désigné par un grand nombre de nos exemples dans ce guide et d’autres articles.

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT 
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count] 
    + nps.[row_overflow_used_page_count] 
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count] 
 - (nps.[reserved_page_count] - nps.[used_page_count]) 
 - ([in_row_data_page_count] 
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from 
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT * 
FROM size
;
```

### <a name="table-space-summary"></a>Espace de table de synthèse

Cette requête renvoie les lignes et l’espace en tableau.  Il s’agit d’une requête excellente pour afficher les tables sont vos tableaux plus grande et qu’ils soient alternées ou hachage distribué.  Tables de hachage distribué apparaissent également la colonne de distribution.  Dans la plupart des cas vos tableaux plus grande doit être hachage distribué à l’aide d’un index columnstore groupé.

```sql
SELECT 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,     distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM 
    dbo.vTableSizes
GROUP BY 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,     distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### <a name="table-space-by-distribution-type"></a>Espace de table par type de distribution

```sql
SELECT 
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### <a name="table-space-by-index-type"></a>Espace de tableau par type d’index

```sql
SELECT 
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### <a name="distribution-space-summary"></a>Espace de distribution Résumé

```sql
SELECT 
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, voir les articles sur les [Types de données de tableau][Types de données], [distribuer une Table][répartir], [indexation d’une Table],[Index], [partition une Table][Partition], [Statistiques de la conservation de la Table][statistiques] et [Tables temporaires][temporaire].  Pour plus d’informations sur les meilleures pratiques, voir [Recommandations entrepôt de données SQL][].

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
[Charger les données avec Polybase]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md

<!--MSDN references-->
[CRÉER LA TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[RENAME]: https://msdn.microsoft.com/library/mt631611.aspx
[DBCC PDW_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx
[Propriété d’identité]: https://msdn.microsoft.com/library/ms186775.aspx
[Attribution de solution de contournement clés de substitution]: https://blogs.msdn.microsoft.com/sqlcat/2016/02/18/assigning-surrogate-key-to-dimension-tables-in-sql-dw-and-aps/
[Contraintes de table]: https://msdn.microsoft.com/library/ms188066.aspx
[Colonnes calculées]: https://msdn.microsoft.com/library/ms186241.aspx
[Colonnes éparses]: https://msdn.microsoft.com/library/cc280604.aspx
[Types définis par l’utilisateur]: https://msdn.microsoft.com/library/ms131694.aspx
[Séquence]: https://msdn.microsoft.com/library/ff878091.aspx
[Déclencheurs]: https://msdn.microsoft.com/library/ms189799.aspx
[Vues indexées]: https://msdn.microsoft.com/library/ms191432.aspx
[Synonymes]: https://msdn.microsoft.com/library/ms177544.aspx
[Index uniques]: https://msdn.microsoft.com/library/ms188783.aspx

<!--Other Web references-->
