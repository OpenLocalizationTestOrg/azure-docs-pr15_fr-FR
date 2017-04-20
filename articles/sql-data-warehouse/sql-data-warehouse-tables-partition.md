<properties
   pageTitle="Partition tables SQL Data Warehouse | Microsoft Azure"
   description="Prise en main table partition dans SQL Azure Data Warehouse."
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
   ms.date="07/18/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="partitioning-tables-in-sql-data-warehouse"></a>Tableaux de partition dans Data Warehouse SQL

> [AZURE.SELECTOR]
- [Vue d’ensemble][]
- [Types de données][]
- [Distribuer][]
- [Index][]
- [Partition][]
- [Statistiques][]
- [Temporaire][]

Partition est pris en charge sur tous les types de table SQL Data Warehouse ; notamment columnstore groupé, index groupé et segment.  Partition est également prise en charge sur tous les types de distribution, y compris hachage ou alternées distribué.  Partition permet de diviser vos données en petits groupes de données et dans la plupart des cas, partition est effectué sur une colonne de dates.

## <a name="benefits-of-partitioning"></a>Avantages de partition

Partition peut bénéficier des performances de requête et de la maintenance des données.  Si elle avantages à la fois ou une seule est dépendent de la façon dont les données sont chargées et si la même colonne peut être utilisée pour les deux fonctions, dans la mesure où partition ne peut être effectuée que sur une seule colonne.

### <a name="benefits-to-loads"></a>Avantages au chargement

Le principal avantage de partition dans SQL Data Warehouse est améliorer l’efficacité et les performances de chargement des données via l’utilisation de la suppression de partition, passer et de la fusion.  Dans la plupart des cas partition des données dans une colonne de date qui est étroitement liée à la séquence qui les données chargées à la base de données.  Parmi les principaux avantages de l’utilisation de partitions pour gérer les données qu’il la prévention de journalisation des transactions.  Tandis que simplement insertion, la mise à jour ou supprimer des données peut être l’approche plus simple, avec un peu d’imagination et effort, utilisant la partition pendant le processus de chargement peut sensiblement améliorer les performances.

Changement de partition peut servir à rapidement supprimer ou remplacer une section d’un tableau.  Par exemple, une table de faits ventes peut contenir uniquement les données pour les derniers mois 36.  À la fin de chaque mois, le mois de données de ventes plus ancien est supprimé de la table.  Ces données peuvent être supprimées à l’aide d’une instruction delete pour supprimer les données pour le mois plus ancien.  Toutefois, la suppression d’une grande quantité de données en ligne avec une instruction delete peut prendre beaucoup de temps, ainsi que créer le risque de transactions volumineuses qui peut prendre beaucoup de temps pour restaurer si un problème survient.  Une meilleure approche consiste à simplement le faire glisser la partition la plus ancienne de données.  Suppression des lignes individuelles dans lequel peut prendre heures, la suppression d’une partition entière peut prendre secondes.

### <a name="benefits-to-queries"></a>Avantages des requêtes

Partition peut également être utilisé pour améliorer les performances de requête.  Si une requête s’applique un filtre sur une colonne partitionnée, cela peut limiter l’analyse uniquement les partitions éligibles qui peut être un quantité inférieure sous-ensemble des données, pour éviter d’obtenir une analyse de table complète.  Avec l’introduction d’index columnstore groupé, les avantages de performances suppression prédicats sont moins utiles, mais dans certains cas, il peut y avoir des avantages des requêtes.  Par exemple, si la table de faits ventes est répartie en 36 mois en utilisant le champ date de vente, puis filtre les requêtes sur la date de vente pouvez doit effectuer des recherches dans les partitions qui ne correspondent pas le filtre.

## <a name="partition-sizing-guidance"></a>Conseils de redimensionnement partition

Si partition peut être utilisé pour améliorer les performances des scénarios, création d’une table avec des partitions **trop** peut réduire les performances dans certaines circonstances.  Ces problèmes sont particulièrement vrais pour les tableaux columnstore groupé.  De division pour être utile, il est important de comprendre quand utiliser partition et le nombre de partitions à créer.  Aucune règle rapide difficile à combien de partitions est trop nombreux, il dépend de vos données et combien de partitions vous chargez à simultanément.  Mais en règle générale, pensez de l’ajout de 10 s à 100 s des partitions, pas 1000 s.

Lorsque vous créez partition sur les tables **columnstore groupé** , il est important de prendre en compte le nombre de lignes est alors placé dans chaque partition.  Pour compression optimal et les performances de tables columnstore groupé, un minimum de 1 million de lignes par distribution et partition est nécessaire.  Avant la création des partitions, SQL Data Warehouse divise déjà chaque table en 60 bases de données distribuées.  Toute partition ajoutée à un tableau en plus de la distribution créée en arrière-plan.  À l’aide de cet exemple, si la table de faits ventes contenu 36 partitions mensuelles et étant donné que SQL Data Warehouse présente 60 distribution, puis la table de faits ventes doit contenir 60 millions de lignes par mois ou de lignes 2,1 milliards lorsque tous les mois sont remplis.  Si une table contient beaucoup moins de lignes que le nombre minimal recommandé de lignes par partition, envisagez d’utiliser un nombre moins élevé partitions afin de pouvoir pour modifier augmenter le nombre de lignes par partition.  Consultez également l’article[d’Index] de [l’indexation]qui inclut les requêtes qui peuvent être exécutés sur SQL Data Warehouse afin d’évaluer la qualité de l’index de columnstore cluster.

## <a name="syntax-difference-from-sql-server"></a>Différence de syntaxe à partir de SQL Server

Data Warehouse SQL présente une définition simplifiée des partitions qui est légèrement différente de SQL Server.  Partition des fonctions et des jeux n’est pas utilisées dans SQL Data Warehouse comme elles se trouvent dans SQL Server.  Au lieu de cela, il vous souhaitez est identifier colonne partitionnée et les points limites.  La syntaxe de partition peut être légèrement différente de SQL Server, les concepts de base sont identiques.  Prise en charge de SQL Server et SQL Data Warehouse une colonne partition par table, ce qui peut être exhaustive, contrairement aux partition.  Pour en savoir plus sur partition, voir [partition Tables et index][].

L’exemple ci-dessous d’une instruction SQL Data Warehouse partition [créer tableau][] , des partitions la table FactInternetSales sur la colonne OrderDateKey :

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

## <a name="migrating-partitioning-from-sql-server"></a>Migration partition à partir de SQL Server

Pour migrer des définitions de partition SQL Server à SQL Data Warehouse simplement :

- Éliminer le [schéma de partition][]de SQL Server.
- Ajouter la définition de la [fonction partition][] à votre créer une TABLE.

Si vous migrez une table partitionnée à partir d’une instance SQL Server la ci-dessous SQL peut vous aider à interroger le nombre de lignes qui se trouvent dans chaque partition.  N’oubliez pas que si la précision partition même est utilisée sur SQL Data Warehouse, le nombre de lignes par partition diminuer par un facteur de 60.  

```sql
-- Partition information for a SQL Server Database
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]      = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

## <a name="workload-management"></a>Gestion de la charge de travail

Une dernière pièce considération à prendre en compte à la décision partition table est la [gestion de la charge de travail][].  Gestion de la charge de travail dans SQL Data Warehouse est principalement la gestion de mémoire et la concurrence.  La quantité de mémoire maximale allouée à chaque distribution pendant l’exécution des requêtes SQL Data Warehouse est régi ressource classes.  Dans l’idéal sont la taille vos partitions prenant en compte d’autres facteurs, tels que les besoins en mémoire de la création d’index columnstore groupé.  Groupé considérablement columnstore index avantage lorsqu’ils sont alloués plus de mémoire.  Par conséquent, vous souhaiterez peut-être vérifier qu’une reconstruction partition d’index n’est pas manque de mémoire. Augmentation de la quantité de mémoire disponible pour votre requête peut être obtenue en à partir de rôle par défaut, smallrc, à un des autres rôles tels que largerc.

Vous trouverez des informations sur l’allocation de mémoire par distribution en demandant les affichages de gestion dynamique de l’administrateur de ressources. En réalité, votre allocation de mémoire sera inférieure aux chiffres ci-dessous. Toutefois, cette offre un niveau de conseils que vous pouvez utiliser lorsque vos partitions pour les opérations de gestion des données de redimensionnement.  Tenter d’éviter que vos partitions au-delà de l’allocation de mémoire fournies par la classe très grande ressource de redimensionnement. Si vos partitions croître au-delà de cette illustration vous risquez de pression de mémoire qui à son tour permet d’accéder à moins compression optimal.

```sql
SELECT  rp.[name]                               AS [pool_name]
,       rp.[max_memory_kb]                      AS [max_memory_kb]
,       rp.[max_memory_kb]/1024                 AS [max_memory_mb]
,       rp.[max_memory_kb]/1048576              AS [mex_memory_gb]
,       rp.[max_memory_percent]                 AS [max_memory_percent]
,       wg.[name]                               AS [group_name]
,       wg.[importance]                         AS [group_importance]
,       wg.[request_max_memory_grant_percent]   AS [request_max_memory_grant_percent]
FROM    sys.dm_pdw_nodes_resource_governor_workload_groups  wg
JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools   rp ON wg.[pool_id] = rp.[pool_id]
WHERE   wg.[name] like 'SloDWGroup%'
AND     rp.[name]    = 'SloDWPool'
;
```

## <a name="partition-switching"></a>Changement de partition

Data Warehouse SQL prend en charge partition fractionnement, la fusion et le basculement. Chacun de ces fonctions est incapable à l’aide de l’instruction [ALTER TABLE][] .

Pour basculer des partitions entre deux tables, vous devez vous assurer que les partitions aligneront sur leurs limites respectives et que les définitions de table correspondent à. Comme contraintes de vérification ne sont pas disponibles pour appliquer la plage de valeurs dans une table de la table source doit contenir les mêmes limites partition comme étant la table cible. Si ce n’est pas le cas, le commutateur partition échoue comme les métadonnées partition ne seront pas synchronisées.

### <a name="how-to-split-a-partition-that-contains-data"></a>Comment faire pour fractionner une partition qui contient des données

La méthode la plus efficace pour fractionner une partition qui contient déjà des données consiste à utiliser un `CTAS` instruction. Si la table partitionnée est un columnstore groupé puis la table partition doit être vide avant qu’il peut être fractionnée.

Voici un exemple de tableau columnstore partitionnée contenant une seule ligne dans chaque partition :

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,19990101,1,1,1,1,1,1);
INSERT INTO dbo.FactInternetSales
VALUES (1,20000101,1,1,1,1,1,1);


CREATE STATISTICS Stat_dbo_FactInternetSales_OrderDateKey ON dbo.FactInternetSales(OrderDateKey);
```

> [AZURE.NOTE] Création de l’objet statistique, nous s’assurer que les métadonnées de cette table sont plus précise. Si nous omettez création de statistiques, SQL Data Warehouse utilisera les valeurs par défaut. Pour plus d’informations sur les statistiques, veuillez consulter [les statistiques][].

Nous pouvons puis rechercher le nombre de ligne à l’aide de la `sys.partitions` affichage du catalogue :

```sql
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
;
```

Si vous essayez de fractionner ce tableau, nous allons un message d’erreur :

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Message 35346, niveau 15, état 1, ligne 44 fractionnée clause d’instruction ALTER PARTITION a échoué car la partition n’est pas vide.  Seules les partitions vides peuvent être divisées dans lorsqu’un index columnstore existe sur la table. Vous pouvez désactiver l’index columnstore avant l’instruction ALTER PARTITION, puis reconstruction de l’index columnstore après que ALTER PARTITION est terminée.

Toutefois, nous pouvons utiliser `CTAS` pour créer une nouvelle table pour mettre en attente de nos données.

```sql
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    FactInternetSales
WHERE   1=2
;
```

Comme les limites de partition sont alignés un commutateur est autorisé. Ceci laisse la table source avec une partition vide que nous pouvons fractionner par la suite.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Tout ce qu’il reste à faire est d’aligner nos données pour les nouvelles limites partition à l’aide de `CTAS` et le commutateur nos données nouveau dans la table principale

```sql
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales_20000101]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

ALTER TABLE dbo.FactInternetSales_20000101_20010101 SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2;
```

Une fois que vous avez terminé le déplacement des données, il est recommandé d’actualiser les statistiques sur la table cible pour vous assurer qu’ils reflètent précisément la nouvelle répartition des données dans leurs partitions correspondantes :

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="table-partitioning-source-control"></a>Table partition contrôle de code source

Pour éviter la définition de votre tableau de **soufflures** dans votre système de contrôle de code source que vous souhaitiez à prendre en considération la solution suivante :

1. Créer la table comme une table partitionnée mais sans valeurs partition

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    ()
                )
)
;
```

2. `SPLIT`le tableau dans le cadre du processus de déploiement :

```sql
-- Create a table containing the partition boundaries

CREATE TABLE #partitions
WITH
(
    LOCATION = USER_DB
,   DISTRIBUTION = HASH(ptn_no)
)
AS
SELECT  ptn_no
,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
        ) a
;

-- Iterate over the partition boundaries and split the table

DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
,       @i INT = 1                                 --iterator for while loop
,       @q NVARCHAR(4000)                          --query
,       @p NVARCHAR(20)     = N''                  --partition_number
,       @s NVARCHAR(128)    = N'dbo'               --schema
,       @t NVARCHAR(128)    = N'FactInternetSales' --table
;

WHILE @i <= @c
BEGIN
    SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
    SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

    -- PRINT @q;
    EXECUTE sp_executesql @q;

    SET @i+=1;
END

-- Code clean-up

DROP TABLE #partitions;
```

Cette approche le code de contrôle de code source reste statique et les valeurs de limite partition sont autorisés à être dynamiques ; évoluer avec l’entrepôt au fil du temps.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, voir les articles sur la [Vue d’ensemble de la Table][vue d’ensemble], [Types de données de tableau][Types de données], [distribuer une Table][répartir], [indexation d’une Table],[Index], [Statistiques de la conservation de la Table][statistiques] et [Tables temporaires][temporaire].  Pour plus d’informations sur les meilleures pratiques, voir [Recommandations entrepôt de données SQL][].

<!--Image references-->

<!--Article references-->
[Vue d’ensemble]: ./sql-data-warehouse-tables-overview.md
[Types de données]: ./sql-data-warehouse-tables-data-types.md
[Distribuer]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistiques]: ./sql-data-warehouse-tables-statistics.md
[Temporaire]: ./sql-data-warehouse-tables-temporary.md
[gestion de la charge de travail]: ./sql-data-warehouse-develop-concurrency.md
[Meilleures pratiques SQL données entrepôt]: ./sql-data-warehouse-best-practices.md

<!-- MSDN Articles -->
[Tables et index partitionnés]: https://msdn.microsoft.com/library/ms190787.aspx
[ALTER TABLE]: https://msdn.microsoft.com/en-us/library/ms190273.aspx
[CRÉER LA TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[partition, fonction]: https://msdn.microsoft.com/library/ms187802.aspx
[schéma de partition]: https://msdn.microsoft.com/library/ms179854.aspx


<!-- Other web references -->
