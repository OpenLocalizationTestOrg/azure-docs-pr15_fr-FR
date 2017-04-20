<properties
   pageTitle="Gestion des statistiques sur les tables dans SQL Data Warehouse | Microsoft Azure"
   description="Mise en route des statistiques sur les tables dans SQL Azure Data Warehouse."
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
   ms.date="06/30/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="managing-statistics-on-tables-in-sql-data-warehouse"></a>Gestion des statistiques sur les tables dans Data Warehouse SQL

> [AZURE.SELECTOR]
- [Vue d’ensemble][]
- [Types de données][]
- [Distribuer][]
- [Index][]
- [Partition][]
- [Statistiques][]
- [Temporaire][]

Plus SQL Data Warehouse est informé de vos données, plus vite qu’il peut exécuter des requêtes sur vos données.  Le dire Data Warehouse SQL sur vos données, consiste à collecte de statistiques sur vos données.  Vous rencontrez des statistiques sur vos données sont une des actions les plus importantes que vous pouvez faire pour optimiser vos requêtes.  Statistiques aident à créer le plan optimal pour vos requêtes SQL Data Warehouse.  C’est parce que l’optimiseur de requêtes SQL Data Warehouse est un optimiseur coût basé.  En d’autres termes, elle compare le coût de différents plans de requête, puis choisit le plan avec un coût minimal, qui doit également être du plan s’exécutent le plus rapide.

Des statistiques peuvent être créées sur une seule colonne, plusieurs colonnes ou sur un index d’un tableau.  Statistiques sont stockées dans un histogramme qui capture la sélectivité des valeurs et plage.  Il s’agit d’un intérêt particulier lorsque l’optimiseur a besoin évaluer les jointures, GROUP BY, HAVING et la clause WHERE dans une requête.  Par exemple, si l’optimiseur estime que la date que vous filtrez dans votre requête renvoie 1 ligne, il peut choisir un autre plan qu’if il estime qu’ils date que vous avez sélectionné sera renvoyer 1 million de lignes.  Création de statistiques est très important, il est également important de ce statistiques *précisément* refléter l’état actuel de la table.  Statistiques récentes permet de garantir qu’un bon plan est sélectionné par l’optimiseur.  Les plans créés par l’optimiseur ne sont pas aussi bien que les statistiques sur vos données.

Le processus de création et de mise à jour des statistiques est actuellement un processus manuel, mais il est très simple à réaliser.  Il s’agit Contrairement à SQL Server qui crée automatiquement et met à jour les statistiques sur les colonnes uniques et index.  En utilisant les informations ci-dessous, vous pouvez automatiser considérablement la gestion des statistiques sur vos données. 

## <a name="getting-started-with-statistics"></a>Mise en route des statistiques

 Création de statistiques échantillonnées sur toutes les colonnes est un moyen facile de commencer à utiliser les statistiques.  Dans la mesure où il est également important de tenir à jour une statistiques, une méthode plus classique peut être mise à jour vos statistiques quotidiennement ou après chaque chargement. Il existe toujours des compromis entre les performances et le coût pour créer et mettre à jour les statistiques.  Si vous trouvez qu'il prend trop de temps pour conserver toutes vos statistiques, vous souhaiterez peut-être tenter de définir davantage les colonnes qui ont des statistiques ou les colonnes qui doivent souvent mises à jour.  Par exemple, vous pouvez mettre à jour colonnes de date chaque jour nouvelles valeurs peuvent être ajoutés au lieu d’après chaque chargement de la. Là encore, qui permet des plus intéressantes en demandant statistiques sur des colonnes impliquées dans les jointures, GROUP BY, HAVING la clause WHERE.  Si vous avez une table avec un grand nombre de colonnes qui ne sont utilisées dans la clause SELECT, statistiques sur ces colonnes ne peuvent pas vous aider, et dépenses un peu plus d’efforts pour identifier uniquement les colonnes où statistiques aidera, peut réduire le temps pour gérer vos statistiques.

## <a name="multi-column-statistics"></a>Statistiques de plusieurs colonnes

Outre la création de statistiques sur les colonnes unique, il est possible que vos requêtes lui facilitera à partir des statistiques de plusieurs colonnes.  Statistiques de plusieurs colonnes sont les statistiques créées sur une liste de colonnes.  Ils incluent des statistiques de colonne sur la première colonne dans la liste, ainsi que des informations de corrélation entre colonne appelée densité.  Par exemple, si vous avez une table qui joint à un autre sur deux colonnes, il est possible que SQL Data Warehouse peut améliorer l’offre s’il comprend la relation entre deux colonnes.   Statistiques de colonne multiples peuvent améliorer les performances de requête pour certaines opérations, telles que les jointures composites et regrouper par.

## <a name="updating-statistics"></a>Mise à jour des statistiques

Mise à jour des statistiques est essentiel de votre routine de gestion de base de données.  Lorsque la répartition des données dans la base de données est modifiée, statistiques doivent être mis à jour.  Statistiques obsolètes, vous aboutissez à performances de requête non optimaux.

Une meilleure solution consiste à mettre à jour les statistiques sur les colonnes date chaque jour lors de l’ajout de nouvelles dates.  Chaque lignes nouvelle heure sont chargées dans le data warehouse, nouvelles dates de chargement ou les dates de transaction sont ajoutées. Ces modifier la distribution des données et apportez les statistiques obsolètes. À l’inverse, statistiques sur une colonne pays dans une table customer devrez jamais mettre à jour, comme la distribution des valeurs généralement ne change pas. En supposant que la distribution est constante entre les clients, ajouter de nouvelles lignes à la variation de table n’est pas va changer la répartition des données. Toutefois, si votre data warehouse ne comporte un pays et que vous importez des données à partir d’un nouveau pays, ce qui crée de données à partir de plusieurs pays stockées, puis vous certainement devez mettre à jour des statistiques sur la colonne pays.

La première question pour demander lors de la résolution des problèmes d’une requête est « Sont les statistiques à jour ? »

Cette question ne fait pas partie qui peuvent être satisfaites par l’âge des données. Un objet statistiques à jour peut être très anciens s’il n’y a eu aucune modification significative aux données sous-jacentes. Lorsque le nombre de lignes a changé de manière significative ou il y a une modification significative la distribution des valeurs d’une colonne donnée *puis* il est temps de mettre à jour les statistiques.  

Pour référence, **SQL Server** (pas SQL Data Warehouse) met automatiquement à jour les statistiques pour ces situations :

- Si vous avez aucune ligne dans la table, lorsque vous ajoutez des lignes, vous obtiendrez une mise à jour automatique des statistiques
- Lorsque vous ajoutez plus de 500 lignes dans une table en commençant par inférieures à 500 lignes (par exemple, au début que vous avez 499, puis ajoutez 500 lignes à un total de 999 lignes), vous recevrez une mise à jour automatique 
- Une fois que vous avez plus de 500 lignes que vous devrez ajouter 500 lignes supplémentaires + 20 % de la taille de la table avant que vous voyez une mise à jour automatique sur les statistiques

Dans la mesure où il n’existe aucune vue afin de déterminer si les données du tableau ont changé depuis les derniers statistiques de temps ont été mises à jour, connaître l’âge de vos statistiques peut vous fournir avec une partie de l’image.  Vous pouvez utiliser la requête suivante pour déterminer la dernière fois vos statistiques où mis à jour sur chaque table.  

> [AZURE.NOTE] N’oubliez pas que s’il existe une modification significative la distribution des valeurs d’une colonne donnée, vous devez mettre à jour les statistiques de quelle que soit la dernière fois qu’ils ont été mis à jour.  

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc    
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co    
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty    
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb    
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm    
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

Colonnes de date dans un data warehouse, par exemple, généralement besoin statistiques mises à jour fréquentes. Chaque lignes nouvelle heure sont chargées dans le data warehouse, nouvelles dates de chargement ou les dates de transaction sont ajoutées. Ces modifier la distribution des données et apportez les statistiques obsolètes.  En revanche, statistiques sur une colonne sexe sur une table clientèle deviez jamais être mis à jour. En supposant que la distribution est constante entre les clients, ajouter de nouvelles lignes à la variation de table n’est pas va changer la répartition des données. Toutefois, si votre data warehouse ne contient qu’une épreuve et une nouvelle exigence se traduit par un des deux sexes plusieurs puis vous certainement devez mettre à jour des statistiques sur la colonne sexe.

Pour plus d’informations, voir [statistiques][] sur MSDN.

## <a name="implementing-statistics-management"></a>Implémentation de la gestion des statistiques

Il est souvent judicieux de sélectionner étendre votre processus pour vous assurer que les statistiques sont mises à jour à la fin de la charge de chargement des données. Le chargement de données est lorsque tables plus fréquemment modifier leur taille et/ou la répartition des valeurs. Par conséquent, il s’agit d’un emplacement logique pour mettre en œuvre des processus de gestion.

Quelques principes sont fournis ci-dessous pour mettre à jour vos statistiques pendant le processus de chargement :

- Assurez-vous que chaque table chargé comporte au moins un objet statistiques mis à jour. Cela met à jour les informations de taille (nombre de lignes et le nombre de pages) de tables dans le cadre de la mise à jour les statistiques.
- Se concentrer sur les colonnes qui participent clauses JOIN, GROUP BY, ORDER BY et DISTINCT
- Pensez « croissant clé » colonnes tels que transaction dates plus souvent que ces valeurs ne pas être inclus dans l’histogramme statistiques.
- Vous pouvez mettre à jour les colonnes distribution statique moins fréquemment.
- N’oubliez pas de que chaque objet statistique est mis à jour de série. Mise en œuvre simplement `UPDATE STATISTICS <TABLE_NAME>` peut ne pas être idéale - notamment pour les tables large avec un grand nombre d’objets statistiques.

> [AZURE.NOTE] Pour plus d’informations sur [croissant clé], veuillez consulter le livre blanc de SQL Server 2014 cardinalité estimation modèle.

Pour plus d’informations, voir [Estimation de cardinalité][] sur MSDN.

## <a name="examples-create-statistics"></a>Exemples : Créer des statistiques

Ces exemples montrent comment utiliser différentes options pour créer des statistiques. Les options que vous utilisez pour chaque colonne dépendent les caractéristiques de vos données et l’utilisation de la colonne dans les requêtes.

### <a name="a-create-single-column-statistics-with-default-options"></a>A. Créer des statistiques d’une seule colonne avec les options par défaut

Pour créer des statistiques sur une colonne, il vous suffit de fournir un nom pour l’objet statistiques et le nom de la colonne.

La syntaxe suivante utilise toutes les options par défaut. Par défaut, SQL Data Warehouse exemples 20 % de la table lorsqu’il crée des statistiques.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Par exemple :

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="b-create-single-column-statistics-by-examining-every-row"></a>B. Créer des statistiques d’une seule colonne en examinant toutes les lignes

Le taux d’échantillonnage par défaut de 20 % est suffisant pour la plupart des cas. Toutefois, vous pouvez ajuster le taux d’échantillonnage.

Pour effectuer un échantillonnage le tableau complet, utilisez la syntaxe suivante :

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Par exemple :

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="c-create-single-column-statistics-by-specifying-the-sample-size"></a>C. Créer des statistiques d’une seule colonne en spécifiant la taille de l’échantillon

Vous pouvez également spécifier la taille de l’échantillon selon un pourcentage :

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="d-create-single-column-statistics-on-only-some-of-the-rows"></a>D. Créer des statistiques d’une seule colonne sur uniquement certaines lignes

Une autre option, vous pouvez créer des statistiques sur une partie des lignes dans votre tableau. Cette option est appelée une statistique filtrée.

Par exemple, vous pouvez utiliser statistiques filtrés lorsque vous planifiez interroger une partition spécifique d’une table partitionnée importante. En créant des statistiques sur uniquement les valeurs de partition, la précision des statistiques améliorer et par conséquent améliorer les performances des requêtes.

Cet exemple crée des statistiques sur une plage de valeurs. Les valeurs peuvent être facilement définies pour correspondre à la plage de valeurs d’une partition.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [AZURE.NOTE] L’optimiseur de requête à prendre en compte à l’aide des statistiques filtrées lorsqu’il choisit le plan de requête distribué, la requête doit tenir à l’intérieur de la définition de l’objet statistiques. À l’aide de l’exemple précédent, la requête où la clause doit spécifier des valeurs de col1 entre 2000101 et 20001231.

### <a name="e-create-single-column-statistics-with-all-the-options"></a>E. Créer des statistiques d’une seule colonne avec toutes les options

Vous pouvez, bien entendu, combiner les options. L’exemple suivant crée un objet statistiques filtrée avec une taille de l’échantillon personnalisé :

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Pour la référence complète, voir [Créer des statistiques][] sur MSDN.

### <a name="f-create-multi-column-statistics"></a>F. Créer des statistiques de plusieurs colonnes

Pour créer une statistique de plusieurs colonne, utilisez les exemples précédents, mais spécifier davantage de colonnes.

> [AZURE.NOTE] L’histogramme, qui est utilisé pour estimer le nombre de lignes dans le résultat de la requête, est disponible uniquement pour la première colonne répertoriée dans la définition d’objet statistiques.

Dans cet exemple, l’histogramme est activé *produit\_catégorie*. Statistiques de colonne de manière croisée sont calculées sur *produit\_catégorie* et *produit\_sub_c\ategory*:

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Dans la mesure où il existe une corrélation entre *produit\_catégorie* et *produit\_sub\_catégorie*, un jours fériés multicolonne peut être utile si vous accédez à ces colonnes en même temps.

### <a name="g-create-statistics-on-all-the-columns-in-a-table"></a>G. Créer des statistiques sur toutes les colonnes dans un tableau

Permet de créer des statistiques consiste à problèmes commandes CREATE STATISTICS après la création de la table.

```sql
CREATE TABLE dbo.table1
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

### <a name="h-use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>H. Utiliser une procédure stockée pour créer des statistiques sur toutes les colonnes dans une base de données

Data Warehouse SQL n’a pas une procédure stockée système équivalente à [] [sp_create_stats] dans SQL Server. Cette procédure stockée crée un objet statistiques colonne unique sur toutes les colonnes de la base de données qui n’a pas encore statistiques.

Cela vous aidera à commencer à utiliser votre conception de base de données. N’hésitez pas à l’adapter à vos besoins.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
    DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN   sys.[external_tables] e ON  e.[object_id]       = t.[object_id]
WHERE       l.[object_id] IS NULL
AND         e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+@sample_pct+'PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

Pour créer des statistiques sur toutes les colonnes dans la table avec cette procédure, appelez simplement la procédure.

```sql
prc_sqldw_create_stats;
```

## <a name="examples-update-statistics"></a>Exemples : mise à jour statistiques

Pour mettre à jour les statistiques, vous pouvez :

1. Mettre à jour un objet statistiques. Spécifiez le nom de l’objet statistiques que vous souhaitez mettre à jour.
2. Mettre à jour tous les objets statistiques sur une table. Spécifiez le nom de la table au lieu d’un objet statistiques spécifiques.


### <a name="a-update-one-specific-statistics-object"></a>A. Mettre à jour un objet statistiques spécifiques ###
Utilisez la syntaxe suivante pour mettre à jour un objet statistiques spécifique :

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Par exemple :

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

En mettant à jour les objets statistiques spécifiques, vous pouvez réduire le temps et les ressources nécessaires pour gérer des statistiques. Cette fonctionnalité nécessite réflexion, cependant, pour choisir les objets statistiques meilleures pour mettre à jour.


### <a name="b-update-all-statistics-on-a-table"></a>B. Mettre à jour toutes les statistiques sur une table ###
Voici une méthode simple pour mettre à jour tous les objets statistiques sur une table.

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Par exemple :

```sql
UPDATE STATISTICS dbo.table1;
```

Cette déclaration soit facile à utiliser. N’oubliez pas à jour toutes les statistiques sur la table et par conséquent peuvent effectuer plus de travail qu’il n’est nécessaire. Si les performances ne sont pas un problème, il s’agit certainement la méthode plus simple et plus complète pour garantir statistiques sont à jour.

> [AZURE.NOTE] Lors de la mise à jour toutes les statistiques sur une table, SQL Data Warehouse effectue une analyse à la table pour chaque statistiques échantillon. Si la table est volumineuse, contient le nombre de colonnes et nombre statistiques, il peut être plus efficace pour mettre à jour des statistiques individuelles en fonction des besoins.

Pour l’implémentation d’un `UPDATE STATISTICS` procédure, voir l’article[temporaire] [Des Tables temporaires]. La méthode d’implémentation est légèrement différente pour les `CREATE STATISTICS` procédure ci-dessus, mais le résultat final est le même.

Pour la syntaxe complète, consultez [Les statistiques de mise à jour][] sur MSDN.

## <a name="statistics-metadata"></a>Statistiques métadonnées
Il existe plusieurs fonctions que vous pouvez utiliser pour trouver des informations à propos des statistiques et affichage système. Par exemple, vous pouvez voir si un objet statistiques peut être obsolète à l’aide de la fonction statistiques date pour afficher lorsque les statistiques de dernière création ou mise à jour.

### <a name="catalog-views-for-statistics"></a>Vues pour les statistiques du catalogue
Ces vues système fournissent des informations sur les statistiques de :

| Vue de catalogue | Description |
| :----------- | :---------- |
| [Sys.Columns][]  | Une seule ligne pour chaque colonne. |
| [Sys.Objects][]  | Une seule ligne pour chaque objet dans la base de données. |  |
| [Sys.Schemas][]  | Une seule ligne pour chaque schéma de la base de données. |  |
| [Sys.stats][] | Une seule ligne pour chaque objet statistiques. |
| [Sys.stats_columns][] | Une seule ligne pour chaque colonne dans l’objet statistiques. Liens vers sys.columns. |
| [Sys.tables][] | Une seule ligne pour chaque table (y compris les tables externes). |
| [Sys.table_types][] | Une seule ligne pour chaque type de données. |


### <a name="system-functions-for-statistics"></a>Fonctions système pour les statistiques
Ces fonctions système sont utiles pour travailler avec des statistiques :

| Fonction système | Description |
| :-------------- | :---------- |
| [FONCTION STATS_DATE][]    | Date de que dernière mise à jour de l’objet statistiques. |
| [DBCC SHOW_STATISTICS][] | Fournit des informations de synthèse et détaillées sur la distribution des valeurs comme comprises par l’objet statistiques. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Combiner des colonnes de statistiques et des fonctions dans un seul affichage

Cet affichage met colonnes relatives aux statistiques et les résultats de la fonction de [] [STATS_DATE()] solidaires.

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_defiinition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

## <a name="dbcc-showstatistics-examples"></a>Exemples de DBCC SHOW_STATISTICS()

DBCC SHOW_STATISTICS() affiche les données stockées dans un objet statistiques. Ces données sont divisée en trois parties.

1. En-tête
2. Densité vecteur
3. Histogramme

Les métadonnées de l’en-tête sur les statistiques. L’histogramme affiche la distribution des valeurs dans la première colonne de clé de l’objet statistiques. Le vecteur densité mesures de corrélation entre colonnes. SQLDW calcule estimations de cardinalité avec n’importe lequel des données dans l’objet statistiques.

### <a name="show-header-density-and-histogram"></a>Afficher l’en-tête, densité et histogramme

Cet exemple simple montre tous les trois parties d’un objet statistiques.

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Par exemple :

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-showstatistics"></a>Afficher un ou plusieurs composants de DBCC SHOW_STATISTICS() ;

Si vous souhaitez uniquement dans l’affichage des composants spécifiques, utilisez la `WITH` clause et spécifient les éléments que vous voulez voir :

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Par exemple :

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-showstatistics-differences"></a>Différences DBCC SHOW_STATISTICS()
DBCC SHOW_STATISTICS() n’est plus strictement activée dans SQL Data Warehouse par rapport à SQL Server.

1. Fonctionnalités non documentées ne sont pas prises en charge
- Impossible d’utiliser Stats_stream
- Impossible de rejoindre des résultats pour des sous-groupes spécifiques des données statistiques, par exemple (STAT_HEADER jointure DENSITY_VECTOR)
2. NO_INFOMSGS ne peut pas être définie pour la suppression de message
3. Crochets autour des noms de statistiques ne peuvent pas être utilisés
4. Impossible d’utiliser les noms de colonne pour identifier les objets statistiques
5. Erreur personnalisée 2767 n’est pas pris en charge

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, voir [DBCC SHOW_STATISTICS][] sur MSDN.  Pour plus d’informations, voir les articles sur la [Vue d’ensemble de la Table][vue d’ensemble], [Types de données de tableau][Types de données], [distribuer une Table][répartir], [indexation d’une Table],[Index], [partition une Table][Partition] et [Tables temporaires][temporaire].  Pour plus d’informations sur les meilleures pratiques, voir [Recommandations entrepôt de données SQL][].  

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
[Estimation de cardinalité]: https://msdn.microsoft.com/library/dn600374.aspx
[CRÉER DES STATISTIQUES]: https://msdn.microsoft.com/library/ms188038.aspx
[DBCC SHOW_STATISTICS]:https://msdn.microsoft.com/library/ms174384.aspx
[Statistiques]: https://msdn.microsoft.com/library/ms190397.aspx
[FONCTION STATS_DATE]: https://msdn.microsoft.com/library/ms190330.aspx
[Sys.Columns]: https://msdn.microsoft.com/library/ms176106.aspx
[Sys.Objects]: https://msdn.microsoft.com/library/ms190324.aspx
[Sys.Schemas]: https://msdn.microsoft.com/library/ms190324.aspx
[Sys.stats]: https://msdn.microsoft.com/library/ms177623.aspx
[Sys.stats_columns]: https://msdn.microsoft.com/library/ms187340.aspx
[Sys.tables]: https://msdn.microsoft.com/library/ms187406.aspx
[Sys.table_types]: https://msdn.microsoft.com/library/bb510623.aspx
[METTRE À JOUR LES STATISTIQUES]: https://msdn.microsoft.com/library/ms187348.aspx

<!--Other Web references-->  
