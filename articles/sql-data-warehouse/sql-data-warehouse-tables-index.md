<properties
   pageTitle="L’indexation tables SQL Data Warehouse | Microsoft Azure"
   description="Prise en main table l’indexation en entrepôt de données SQL Azure."
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
   ms.date="07/12/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="indexing-tables-in-sql-data-warehouse"></a>Indexation tables SQL Data Warehouse

> [AZURE.SELECTOR]
- [Vue d’ensemble][]
- [Types de données][]
- [Distribuer][]
- [Index][]
- [Partition][]
- [Statistiques][]
- [Temporaire][]

Data Warehouse SQL offre plusieurs options d’indexation incluant des [index columnstore groupé][], [index groupés et index non cluster][].  Il propose également une option d’index sans également connu sous le [segment][].  Cet article aborde les avantages de chaque type d’index, ainsi que des conseils pour obtenir les performances de la plupart des déconnecter votre index. Voir [créer une syntaxe de tableau][] pour plus d’informations sur la création d’une table SQL Data Warehouse.

## <a name="clustered-columnstore-indexes"></a>Index columnstore groupé

Par défaut, SQL Data Warehouse crée un index columnstore groupé lorsque aucune option d’index n’est spécifiées dans une table. Tables columnstore groupé offrent le plus haut niveau de compression des données, ainsi que les performances globales requête.  Les tables columnstore groupé seront maintiennent généralement groupés tables index ou le segment et sont généralement le meilleur choix pour les tables volumineuses.  C’est pourquoi columnstore groupé est le meilleur endroit pour démarrer lorsque vous ne savez pas comment indexer votre tableau.  

Pour créer une table columnstore groupé, spécifier des INDEX COLUMNSTORE groupé dans la clause WITH, ou quitter la clause WITH :

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Il existe quelques scénarios où columnstore groupé peut ne pas être une bonne option :

- ColumnStore tables ne prennent pas en charge les index non cluster secondaires.  Envisagez de segment ou tables index groupé à la place.
- Tables ColumnStore ne prennent pas en charge varchar (max), nvarchar (max) et varbinary (max).  Prendre en considération segment ou index ordonné à la place.
- Tables ColumnStore peut s’avérer moins efficaces pour les données transitoires.  Envisagez de segment et tables voire temporaires.
- Tables de petite taille avec moins de 100 millions de lignes.  Prendre en compte les tables de segments.

## <a name="heap-tables"></a>Tables de segments

Lorsque vous soyez temporairement lancement des données sur SQL Data Warehouse, il est possible que l’utilisation d’une table de segment apportera l’ensemble du processus plus rapide.  C’est parce que les charges pour les segments sont plus rapides que les tables index et dans certains cas, que la lecture suivante peut faire à partir du cache.  Si vous chargez des données uniquement pour l’étape avant d’exécuter des transformations plus, chargement de la table à table segment sera beaucoup plus rapide que le chargement des données à une table columnstore groupé. En outre, le chargement des données à une [table temporaire][temporaires] est également chargés beaucoup plus rapidement que le chargement d’un tableau dans un stockage permanent.  

Pour les tables de choix petite, 100 millions de lignes, souvent des tables de segments représentatives.  Cluster columnstore tables commencent à atteindre compression optimal une fois qu’il est plus de 100 millions de lignes.

Pour créer une table de segment, spécifiez simplement segment dans la clause WITH :

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( HEAP );
```

## <a name="clustered-and-nonclustered-indexes"></a>Index

Index groupés peuvent maintiennent columnstore groupé tables lorsqu’une seule ligne doit être récupérées rapidement.  Pour les requêtes où une seule ou très peu recherche ligne est nécessaire pour performances avec vitesse extrême, envisagez d’un index cluster ou ordonné index secondaire.  L’inconvénient d’à l’aide d’un index cluster est que lui facilitera l’uniquement les requêtes qui utilisent un filtre hautement sélectif sur la colonne d’index groupé.  Pour améliorer le filtre sur les autres colonnes un index non ordonné peut être ajouté à d’autres colonnes.  Toutefois, chaque index qui est ajoutée à une table ajoutera espace et temps de traitement au chargement.

Pour créer une table d’index groupé, spécifiez simplement INDEX groupé dans la clause WITH :

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

Pour ajouter un index non cluster sur une table, utilisez simplement la syntaxe suivante :

```SQL
CREATE INDEX zipCodeIndex ON t1 (zipCode);
```

> [AZURE.NOTE] Un index non cluster est créé par défaut lorsque CREATE INDEX est utilisé. En outre, un index non cluster est autorisé uniquement sur une table de stockage de ligne (segment ou groupé INDEX). Index non groupés en haut d’un INDEX cluster COLUMNSTORE n’est pas autorisée pour le moment.


## <a name="optimizing-clustered-columnstore-indexes"></a>Optimisation des index columnstore groupé

Columnstore groupé tables sont organisées dans les données en segments.  Qualité segment haute est critique pour obtenir des performances optimales requête sur une table columnstore.  Segment qualité peut être mesurée par le nombre de lignes dans un groupe de lignes compressé.  Qualité de segment est plus optimale lorsqu’il existe au moins lignes 100 Ko par ligne compressé regroupement et de gagner en performances comme étant le nombre de lignes par approche de groupe ligne 1 048 576 lignes, c'est-à-dire les lignes de la plupart de qu'un groupe de lignes peut contenir.

La sous affichage peut être créé et permet de calculer les lignes moyennes par ligne regroupement et identifient des index columnstore cluster optimale sur votre système.  La dernière colonne sur cette vue générera comme instruction SQL qui peut servir à reconstruire votre index.

```sql
CREATE VIEW dbo.vColumnstoreDensity
AS
SELECT
        GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       s.name                                                                  AS [schema_name]
,       t.name                                                                  AS [table_name]
,   COUNT(DISTINCT rg.[partition_number])                   AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,   CEILING ((SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id]))/1048576) AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 0 THEN 1                   ELSE 0    END)    AS [INVISIBLE_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE 0    END)    AS [INVISIBLE_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
,       'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;'             AS [Rebuild_Index_SQL]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
JOIN    sys.[schemas] s                             ON t.[schema_id]            = s.[schema_id]
GROUP BY
        s.[name]
,       t.[name]
;
```

Maintenant que vous avez créé l’affichage, exécutez cette requête pour identifier les tables avec des groupes de lignes avec inférieur à 100 Ko lignes.  Bien entendu, vous souhaiterez peut-être augmenter le seuil de 100 Ko si vous recherchez plus qualité optimale segment. 

```sql
SELECT  *
FROM    [dbo].[vColumnstoreDensity]
WHERE   COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

Une fois que vous avez exécuté la requête que vous pouvez commencer à examiner les données et analyser vos résultats. Ce tableau décrit ce que vous recherchez dans votre analyse de groupe de ligne.


| Colonne                             | Comment utiliser ces données                                                                                                                                                                      |
| ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [table_partition_count]            | Si la table est segmentée, vous devriez voir compte groupe alternées plus élevé. Chaque partition de la distribution impossible dans principes ont un groupe d’alternées associé. Tenir compte de cela dans votre analyse. Un petit tableau qui a été partition pourrait être optimisé en supprimant la partition complètement car cela améliore la compression.                                                                        |
| [row_count_total]                  | Nombre total de lignes de la table. Par exemple, vous pouvez utiliser cette valeur pour calculer le pourcentage de lignes dans un état compressé.                                                                      |
| [row_count_per_distribution_MAX]   | Si toutes les lignes sont répartis cette valeur serait le nombre cible de lignes par distribution. Comparez cette valeur avec la compressed_rowgroup_count.                                 |
| [COMPRESSED_rowgroup_rows]         | Nombre total de lignes au format columnstore pour la table.                                                                                                                                 |
| [COMPRESSED_rowgroup_rows_AVG]     | Si le nombre moyen de lignes est sensiblement inférieur au nombre maximal de lignes pour un groupe de lignes, puis vous pouvez utiliser SACT ou ALTER INDEX reconstruire à recompresser les données                     |
| [COMPRESSED_rowgroup_count]        | Nombre de groupes de lignes au format columnstore. Si ce nombre est très haut en relation avec la table, il est un indicateur que la densité de columnstore est faible.                                  |
| [COMPRESSED_rowgroup_rows_DELETED] | Lignes sont supprimées logiquement au format columnstore. Si le nombre est élevé par rapport à la taille du tableau, vous pouvez recréer la partition ou reconstruction de l’index comme cela supprime le physiquement. |
| [COMPRESSED_rowgroup_rows_MIN]     | Utilisez cette conjointement avec les colonnes moyenne et MAX à comprendre la plage de valeurs pour les groupes de lignes dans votre columnstore. Un nombre faible dépassé le seuil de chargement (102,400 par distribution partition alignée) suggère qu’optimisations sont disponibles dans le chargement de données                                                                                                                                                 |
| [COMPRESSED_rowgroup_rows_MAX]     | Comme ci-dessus                                                                                                                                                                                  |
| [OPEN_rowgroup_count]              | Groupes de lignes ouverts sont normales. On peut raisonnablement s’attendre un groupe de ligne ouvert par distribution de tables (60). Nombres excessives suggérant entre partitions de chargement des données. Vérifiez la stratégie de partition pour vous assurer qu’il est judicieuse                                                                                                                                                                                                |
| [OPEN_rowgroup_rows]               | Chaque groupe de lignes peut avoir 1 048 576 lignes qu’il contient comme maximum. Utiliser cette valeur pour déterminer comment complète les groupes de lignes ouverts sont actuellement                                                                 |
| [OPEN_rowgroup_rows_MIN]           | Groupes ouverts indiquent que les données sont soit Percoler chargées dans la table ou que la charge précédente renversé les lignes restantes dans ce groupe de ligne. Vous pouvez utiliser la MIN, MAX, groupes de lignes colonnes AVG pour afficher la quantité de données est sat dans la zone Ouvrir. Cela peut s’expliquer 100 % de toutes les données pour les petites tables ! Auquel cas ALTER INDEX reconstruire pour obliger les données à columnstore.                                                                       |
| [OPEN_rowgroup_rows_MAX]           | Comme ci-dessus                                                                                                                                                                                  |
| [OPEN_rowgroup_rows_AVG]           | Comme ci-dessus                                                                                                                                                                                  |
| [CLOSED_rowgroup_rows]             | Examinez les lignes de groupe ligne fermées comme un contrôle de validité.                                                                                                                                       |
| [CLOSED_rowgroup_count]            | Le nombre de groupes de lignes fermées doit être faible si un sont visibles à tout. Groupes de lignes fermé peuvent être convertis en roups rowg compressé à l’aide de l’instruction ALTER INDEX... RÉORGANISER commande. Toutefois, il n’est pas nécessaire normalement. Groupes fermés sont automatiquement converties en groupes de lignes columnstore par le processus de « tuple mover » en arrière-plan.                                                                                               |
| [CLOSED_rowgroup_rows_MIN]         | Ligne privée groupes doivent disposer d’un taux de remplissage très haute. Si le taux de remplissage d’un groupe de lignes fermé est faible, approfondir l’analyse de la columnstore est requis.                                   |
| [CLOSED_rowgroup_rows_MAX]         | Comme ci-dessus                                                                                                                                                                                  |
| [CLOSED_rowgroup_rows_AVG]         | Comme ci-dessus                                                                                                                                                                                  |
| [Rebuild_Index_SQL]         | SQL reconstruire columnstore index pour une table                                                                                                                                                     |

## <a name="causes-of-poor-columnstore-index-quality"></a>Causes de columnstore mauvaise qualité des index

Si vous avez identifié les tables avec la qualité du segment un problème, vous souhaiterez identifier la cause.  Voici d’autres les causes fréquentes de mauvaise segment quaility :

1. Sollicitation de la mémoire lors de l’index a été créé
2. Volume élevé d’opérations DML
3. Petite ou segmenter les opérations de chargement
4. Trop de partitions

Ces facteurs peuvent provoquer un index columnstore d’avoir sensiblement inférieur à l’optimale 1 million de lignes par groupe de lignes.  Ils peuvent également entraîner des lignes accéder au groupe delta ligne au lieu d’un groupe de lignes compressé. 

### <a name="memory-pressure-when-index-was-built"></a>Sollicitation de la mémoire lors de l’index a été créé

Le nombre de lignes par groupe de lignes compressé directement lié à la largeur de la ligne et la quantité de mémoire disponible pour le traitement au groupe de lignes.  Lorsque les lignes sont écrits dans les tables columnstore sous la pression de mémoire, la qualité de segment columnstore risquent d’être affectées.  Par conséquent, la meilleure solution consiste à donner à la session qui écrit sur votre accès columnstore index tables pour autant de mémoire que possible.  Dans la mesure où il y a un compromis entre la quantité de mémoire et concurrence, les instructions fournies pour l’allocation de mémoire droite dépend des données dans chaque ligne de votre tableau, la quantité de DWU que vous avez affecté à votre système et le montant d’emplacements concurrence que vous pouvez donner à la session qui est écrire des données dans votre tableau.  Pour obtenir les meilleurs résultats, nous vous recommandons d’avec xlargerc si vous utilisez DW300 ou moins, largerc si vous utilisez DW400 DW600 et mediumrc si vous utilisez DW1000 et au-dessus.

### <a name="high-volume-of-dml-operations"></a>Volume élevé d’opérations DML

Un volume élevé d’opérations DML que mettre à jour et supprimer des lignes peut introduire inefficace dans la columnstore. Cela est particulièrement vrai lorsque la majorité des lignes dans un groupe de lignes sont modifiés.

- Suppression d’une ligne à partir d’un groupe de lignes compressé uniquement logiquement marque la ligne comme supprimé. La ligne est conservé dans le groupe compressé ligne jusqu'à ce que la partition ou le tableau est reconstruit.
- Insertion d’une ligne ajoute la ligne à une table rowstore interne appelée un groupe de lignes delta. La ligne insérée n’est pas convertie en columnstore jusqu'à ce que le groupe ligne delta est saturé et est marqué comme étant fermé. Groupes de lignes sont fermées une fois qu’ils arrivent à la capacité maximale de 1 048 576 lignes. 
- Mise à jour d’une ligne au format columnstore est traité comme une suppression logique, puis une instruction insert. La ligne insérée peut-être être stockée dans le magasin delta.

Mise à jour groupée opérations et d’insertion qui dépassent le seuil en bloc de 102 400 lignes par partition distribution alignée sera écrit directement dans le format columnstore. Toutefois, en supposant qu’une répartition uniforme, vous devez modifier plus 6.144 millions de lignes en une seule opération pour ce problème. Si le nombre de lignes d’une partition donnée alignés distribution est inférieure à 102,400 alors les lignes seront dirigés vers le magasin delta et il restera jusqu'à ce que suffisamment de lignes ont été insérées ou modifiées pour fermer le groupe de ligne ou l’index a été reconstruit.

### <a name="small-or-trickle-load-operations"></a>Petite ou segmenter les opérations de chargement

Petite charge que flux dans SQL Data Warehouse sont également parfois appelés segmenter les charges. Elles représentent généralement un flux constant proche de données en cours ingérés par le système. Toutefois, comme ce flux de données se trouve à proximité continue le volume de lignes n’est pas très grand. La plupart du temps, les données se trouve sensiblement sous le seuil requis pour un chargement direct au format columnstore.

Dans ce cas, il est souvent judicieux de terre tout d’abord les données dans le stockage blob Azure et laissez-le s’accumuler avant le chargement. Cette méthode est souvent connue sous *le traitement par lots micro*.

### <a name="too-many-partitions"></a>Trop de partitions

Une autre chose à prendre en considération est l’impact de partition sur vos tables columnstore groupé.  Avant de partition, SQL Data Warehouse divise déjà vos données en 60 bases de données.  Partition divise encore vos données.  Si vous partitionnez vos données, vous devez prendre en considération que **chaque** partition doit avoir au moins 1 million de lignes pour bénéficier d’un index columnstore groupé.  Si vous partitionnez votre tableau en 100 partitions, votre table doit avoir au moins 6 milliards lignes pour bénéficier d’un index columnstore groupé (lignes de *100 partitions* 1 million de 60 répartitions). Si votre tableau 100 partition n’a pas de lignes 6 milliards, réduisez le nombre de partitions ou envisagez d’utiliser une table de segment à la place.

Une fois que vos tables ont été chargés avec des données, suivez les opérations suivantes pour identifier et régénérer les tables ayant des indices de columnstore cluster optimaux.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Reconstruction d’index pour améliorer la qualité du segment

### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>Étape 1 : Identifiez ou créez utilisateur qui utilise la classe ressource droite

Un moyen rapide d’immédiatement améliorer la qualité du segment consiste à reconstruisez l’index.  L’instruction SQL renvoyée par la vue ci-dessus renverra une instruction ALTER INDEX reconstruire qui peut être utilisée pour reconstruire votre index.  Lors de la reconstruction votre index, n’oubliez pas que vous allouez suffisamment de mémoire à la session qui votre index est reconstruit.  Pour ce faire, augmenter la classe de ressources d’un utilisateur qui dispose des autorisations pour reconstruire l’index sur ce tableau à la valeur minimale recommandée.  La classe de ressources de l’utilisateur propriétaire de la base de données ne peuvent pas être modifiée, donc si vous n’avez pas créé un utilisateur sur le système, vous devrez faire en premier.  Le minimum recommandé est xlargerc si vous utilisez DW300 inférieur ou égal à, largerc si vous utilisez DW400 DW600 et mediumrc si vous utilisez DW1000 et au-dessus.

Voici un exemple montrant comment allouer plus de mémoire à un utilisateur en augmentant leur classe de ressources.  Pour plus d’informations sur la ressource classes et comment créer un nouvel utilisateur vous pouvez trouver dans la [concurrence et la charge de travail Gestion des] [ Concurrency] article.

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>Étape 2 : Reconstruire à partir d’index columnstore groupé avec une version ultérieure utilisateur de classe de ressources
Ouvrez une session en tant que l’utilisateur de l’étape 1 (par exemple, LoadUser), qui est maintenant à l’aide d’une classe supérieure de la ressource, puis exécutez les instructions ALTER INDEX.  N’oubliez pas que cet utilisateur dispose d’autorisation ALTER les tables dans lequel l’index est en cours de reconstruction.  Ces exemples montrent comment reconstruisez l’index columnstore entière ou reconstruire une partition unique. Dans les tables volumineuses, il est plus pratique de ne pas reconstruire index une seule partition à la fois.

Par ailleurs, au lieu de reconstruction de l’index, vous pouvez copier le tableau vers une nouvelle table à l’aide de [SACT][].  De quelle façon consiste le mieux ? Pour des volumes importants de données, [SACT][] est généralement plus rapide [ALTER INDEX][]. Pour les plus petits volumes de données, [ALTER INDEX][] est plus facile à utiliser et aurez pas besoin de remplacer la table.  Voir **index reconstruction avec SACT et changement de partition** ci-dessous pour plus d’informations sur la façon de reconstruire des index avec SACT.

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

```sql
-- Rebuild a single partition with archival compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE_ARCHIVE)
```

```sql
-- Rebuild a single partition with columnstore compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE)
```

Reconstruction d’un index dans SQL Data Warehouse est une opération en mode hors connexion.  Pour plus d’informations sur la reconstruction d’index, consultez la section ALTER INDEX reconstruire dans [Columnstore index défragmentation][] et la rubrique syntaxe [ALTER INDEX][].
 
### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>Étape 3 : Vérifier la qualité de segment columnstore groupé a amélioré
Relancer la requête quelle table identifié avec une mauvaise qualité de segments et vérifier la qualité de segment a amélioré.  Si vous n’êtes pas améliore la qualité du segment, cela peut s’expliquer que les lignes de votre tableau sont très grande.  Envisagez d’utiliser une classe ressource supérieure ou DWU lors de la reconstruction de votre index.

 
## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>Reconstruction des index avec SACT et changement de partition

Cet exemple utilise [SACT][] et partition passer pour reconstruire une partition de table. 

```sql
-- Step 1: Select the partition of data and write it out to a new table using CTAS
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
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 2: Create a SWITCH out table
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
FROM    [dbo].[FactInternetSales]
WHERE   1=2 -- Note this table will be empty

-- Step 3: Switch OUT the data 
ALTER TABLE [dbo].[FactInternetSales] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales_20000101] PARTITION 2;

-- Step 4: Switch IN the rebuilt data
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2;
```

Pour plus d’informations sur la ré-création des partitions à l’aide de `CTAS`, voir l’article [Partition][] .

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, voir les articles sur la [Vue d’ensemble de la Table][vue d’ensemble], [Types de données de tableau][Types de données], [distribuer une Table][répartir], [partition une Table][Partition], [Statistiques de la conservation de la Table][statistiques] et [Tables temporaires][temporaire].  Pour en savoir plus sur les meilleures pratiques, voir [Meilleures pratiques SQL données entrepôt][].

<!--Image references-->

<!--Article references-->
[Vue d’ensemble]: ./sql-data-warehouse-tables-overview.md
[Types de données]: ./sql-data-warehouse-tables-data-types.md
[Distribuer]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistiques]: ./sql-data-warehouse-tables-statistics.md
[Temporaire]: ./sql-data-warehouse-tables-temporary.md
[Concurrency]: ./sql-data-warehouse-develop-concurrency.md
[SACT]: ./sql-data-warehouse-develop-ctas.md
[Meilleures pratiques SQL données entrepôt]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->
[MODIFIER DES INDEX]: https://msdn.microsoft.com/library/ms188388.aspx
[segment]: https://msdn.microsoft.com/library/hh213609.aspx
[Index groupés et index ordonnés]: https://msdn.microsoft.com/library/ms190457.aspx
[créer une syntaxe de tableau]: https://msdn.microsoft.com/library/mt203953.aspx
[ColumnStore index défragmentation]: https://msdn.microsoft.com/library/dn935013.aspx#Anchor_1
[index columnstore groupé]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
