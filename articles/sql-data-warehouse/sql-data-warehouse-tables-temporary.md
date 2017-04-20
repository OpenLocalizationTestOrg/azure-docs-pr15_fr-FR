<properties
   pageTitle="Tables temporaires dans SQL Data Warehouse | Microsoft Azure"
   description="Prise en main des tables temporaires dans SQL Azure Data Warehouse."
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

# <a name="temporary-tables-in-sql-data-warehouse"></a>Tables temporaires dans Data Warehouse SQL

> [AZURE.SELECTOR]
- [Vue d’ensemble][]
- [Types de données][]
- [Distribuer][]
- [Index][]
- [Partition][]
- [Statistiques][]
- [Temporaire][]

Les tables temporaires sont très utiles lorsqu’il traite des données - en particulier pendant la transformation où les résultats intermédiaires sont transitoires. Dans SQL Data Warehouse tables temporaires existent au niveau de la session.  Ils sont uniquement visibles à la session dans lequel ils ont été créés et sont automatiquement supprimés lorsque cette session ferme la session.  Tables temporaires offrent un gain de performances parce que leurs résultats sont écrits sur local plutôt que de stockage étendu.  Les tables temporaires sont légèrement différentes dans SQL Azure Data Warehouse de base de données SQL Azure comme elles sont accessibles à partir de n’importe où à l’intérieur de la session, y compris à l’intérieur et en dehors d’une procédure stockée.

Cet article contient des conseils essentiels pour l’utilisation de tables temporaires et met en surbrillance les principes de tables temporaires au niveau de session. En utilisant les informations dans cet article peut vous aider à organiser modules votre code, améliorer la réutilisation et simplifier la maintenance de votre code.

## <a name="create-a-temporary-table"></a>Créer une table temporaire

Tables temporaires sont créées en simplement faisant précéder votre nom de la table avec une `#`.  Par exemple :

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]       NVARCHAR(128) NOT NULL
,   [table_name]            NVARCHAR(128) NOT NULL
,   [stats_name]            NVARCHAR(128) NOT NULL
,   [stats_is_filtered]     BIT           NOT NULL
,   [seq_nmbr]              BIGINT        NOT NULL
,   [two_part_name]         NVARCHAR(260) NOT NULL
,   [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,   HEAP
)
```

Vous pouvez également créer des tables temporaires avec un `CTAS` à l’aide de la même approche :

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,   HEAP
)
AS
(
SELECT
        sm.[name]                                                               AS [schema_name]
,       tb.[name]                                                               AS [table_name]
,       st.[name]                                                               AS [stats_name]
,       st.[has_filter]                                                         AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects         AS ob
JOIN    sys.stats           AS st   ON  ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc   ON  st.[stats_id]       = sc.[stats_id]
                                    AND st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co   ON  sc.[column_id]      = co.[column_id]
                                    AND sc.[object_id]      = co.[object_id]
JOIN    sys.tables          AS tb   ON  co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm   ON  tb.[schema_id]      = sm.[schema_id]
WHERE   1=1
AND     st.[user_created]   = 1
GROUP BY
        sm.[name]
,       tb.[name]
,       st.[name]
,       st.[filter_definition]
,       st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
``` 

>[AZURE.NOTE] `CTAS`est une commande puissante et a l’avantage d’être très efficace dans son utilisation de l’espace du journal. 


## <a name="dropping-temporary-tables"></a>Suppression des tables temporaires

Lorsqu’une nouvelle session est créée, aucune table temporaire ne doit exister.  Toutefois, si vous appelez la même procédure stockée, ce qui crée un fichier temporaire avec le même nom, pour vous assurer que votre `CREATE TABLE` instructions sont réussies une vérification de l’existence préalable simple avec un `DROP` peut être utilisé comme dans l’exemple ci-dessous :

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Pour la cohérence de codage, il est recommandé d’utiliser ce modèle pour les tables et les tables temporaires.  Il est également judicieux de sélectionner utiliser `DROP TABLE` pour supprimer les tables temporaires lorsque vous avez fini de les utiliser dans votre code.  Dans la procédure stockée développement il est assez courant pour afficher les commandes de menu déroulant regroupés à la fin d’une procédure afin de ces objets sont supprimés.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Code modularizing

Dans la mesure où les tables temporaires peuvent être vues n’importe où dans une session utilisateur, cela peut être exploitée pour vous aider à organiser modules de code de votre application.  Par exemple, la procédure stockée ci-dessous regroupe les pratiques recommandées à partir du haut pour générer DDL qui met à jour toutes les statistiques dans la base de données par nom statistique.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                               AS [schema_name]
,       tb.[name]                                                               AS [table_name]
,       st.[name]                                                               AS [stats_name]
,       st.[has_filter]                                                         AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects         AS ob
JOIN    sys.stats           AS st   ON  ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc   ON  st.[stats_id]       = sc.[stats_id]
                                    AND st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co   ON  sc.[column_id]      = co.[column_id]
                                    AND sc.[object_id]      = co.[object_id]
JOIN    sys.tables          AS tb   ON  co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm   ON  tb.[schema_id]      = sm.[schema_id]
WHERE   1=1
AND     st.[user_created]   = 1
GROUP BY
        sm.[name]
,       tb.[name]
,       st.[name]
,       st.[filter_definition]
,       st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
GO
```

À ce stade la seule action qui s’est produite est la création d’une procédure stockée qui sera simplement généré une table temporaire, stats_ddl #, avec les instructions DDL.  Cette procédure stockée supprime #stats_ddl s’il existe déjà pour vous assurer que ce dernier ne bascule pas si vous exécutez plusieurs fois au sein d’une session.  Toutefois, étant donné qu’aucun `DROP TABLE` à la fin de la procédure stockée, lorsque la procédure stockée se termine, il laisse le tableau afin qu’il puisse être lu en dehors de la procédure stockée.  Dans Data Warehouse SQL, contrairement à d’autres bases de données SQL Server, il est possible d’utiliser la table temporaire en dehors de la procédure qui l’a créé.  Tables temporaires Data Warehouse SQL peuvent être utilisés **n’importe où** à l’intérieur de la session. Cela peut entraîner davantage de code modulaire et facile à gérer comme dans l’exemple ci-dessous :

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

## <a name="temporary-table-limitations"></a>Limitations de la table temporaire

Data Warehouse SQL impose quelques limitations lors de l’implémentation des tables temporaires.  Pour l’instant, session uniquement dans l’étendue des tables temporaires sont prises en charge.  Les Tables temporaires globales ne sont pas pris en charge.  En outre, affichages ne peut pas être créés sur les tables temporaires.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, voir les articles sur la [Vue d’ensemble de la Table][vue d’ensemble], [Types de données de tableau][Types de données], [distribuer une Table][répartir], [indexation d’une Table],[Index], [partition une Table][Partition] ainsi que les [Statistiques de la conservation de la Table][statistiques].  Pour plus d’informations sur les meilleures pratiques, voir [Recommandations entrepôt de données SQL][].

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
