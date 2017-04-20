<properties
   pageTitle="Créer la table comme sélectionnez (SACT) dans Data Warehouse SQL | Microsoft Azure"
   description="Conseils pour codage avec la créer une table en tant qu’instruction (SACT) select dans SQL Azure Data Warehouse pour développer des solutions."
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

# <a name="create-table-as-select-ctas-in-sql-data-warehouse"></a>Créer la Table comme sélectionner (SACT) dans Data Warehouse SQL
Créer la table sous Sélectionnez ou `CTAS` l’une des fonctionnalités les plus importantes T-SQL n’est disponible. Il est une opération entièrement parallélisée qui crée une nouvelle table basée sur la sortie d’une instruction SELECT. `CTAS`est le moyen le plus rapide pour créer une copie d’un tableau. Vous pouvez considérer qu’il s’agit d’une version SURALIMENTE de `SELECT..INTO` si vous le souhaitez. Ce document fournit des exemples et meilleures pratiques pour `CTAS`.

## <a name="using-ctas-to-copy-a-table"></a>Utilisation de SACT pour copier un tableau

Par exemple le plus souvent utilisé pour `CTAS` consiste à créer une copie d’un tableau afin que vous puissiez modifier le DDL. Si par exemple vous avez créé votre tableau en tant que `ROUND_ROBIN` et à présent que vous souhaitez modifiez-le en une table distribuée sur une colonne, `CTAS` est la façon de modifier la colonne de distribution. `CTAS`peut également servir à modifier les types de partition, l’indexation ou de colonne.

Supposons que vous avez créé cette table en utilisant le type par défaut de `ROUND_ROBIN` distribué car aucune colonne distribution a été spécifié dans le `CREATE TABLE`.

```sql
CREATE TABLE FactInternetSales
(
    ProductKey int NOT NULL,
    OrderDateKey int NOT NULL,
    DueDateKey int NOT NULL,
    ShipDateKey int NOT NULL,
    CustomerKey int NOT NULL,
    PromotionKey int NOT NULL,
    CurrencyKey int NOT NULL,
    SalesTerritoryKey int NOT NULL,
    SalesOrderNumber nvarchar(20) NOT NULL,
    SalesOrderLineNumber tinyint NOT NULL,
    RevisionNumber tinyint NOT NULL,
    OrderQuantity smallint NOT NULL,
    UnitPrice money NOT NULL,
    ExtendedAmount money NOT NULL,
    UnitPriceDiscountPct float NOT NULL,
    DiscountAmount float NOT NULL,
    ProductStandardCost money NOT NULL,
    TotalProductCost money NOT NULL,
    SalesAmount money NOT NULL,
    TaxAmt money NOT NULL,
    Freight money NOT NULL,
    CarrierTrackingNumber nvarchar(25),
    CustomerPONumber nvarchar(25)
);
```

Maintenant, vous souhaitez créer une nouvelle copie de ce tableau avec un indice de Columnstore groupé afin que vous pouvez tirer parti des performances de tables Columnstore groupé. Vous souhaitez distribuer le tableau de ProductKey dans la mesure où vous comptez jointures dans cette colonne également et pour éviter de déplacement de données au cours de jointures sur ProductKey. Enfin que vous voulez également ajouter partition sur OrderDateKey afin que vous pouvez rapidement supprimer les anciennes données en déposant partitions anciennes. Voici l’instruction SACT qui serait copiez votre ancienne table dans une nouvelle table.

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

Enfin, vous pouvez renommer vos tables pour échanger dans votre nouvelle table et faites-le glisser votre ancienne table.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

> [AZURE.NOTE] Azure SQL Data Warehouse ne prend pas encore prise en charge automatique créer ou mettre à jour les statistiques automatiquement chaque année.  Afin d’obtenir les meilleures performances de vos requêtes, il est important que créer des statistiques sur toutes les colonnes de toutes les tables après le premier chargement ou des modifications substantielles apparaissent dans les données.  Pour une explication détaillée des statistiques, consultez la rubrique [statistiques][] dans le groupe développer des rubriques.

## <a name="using-ctas-to-work-around-unsupported-features"></a>À l’aide de SACT contourner des fonctionnalités non prises en charge

`CTAS`peut également servir à contourner un certain nombre des fonctionnalités non prises en charge ci-dessous. Ceci peut s’avérer souvent à une situation gagnant/gagnant comme non seulement votre code soit conforme, mais elle s’exécute souvent plus rapidement sur Data Warehouse SQL. Il s’agit de son design entièrement parallélisée. Scénarios qui peuvent être contournées avec SACT sont les suivantes :

- SÉLECTIONNEZ... DANS
- JOINTURES ANSI sur les mises à jour
- Jointures ANSI sur les suppressions
- FUSIONNER instruction

> [AZURE.NOTE] Essayez de vous demander « SACT premier ». Si vous pensez que vous pouvez résoudre un problème à l’aide `CTAS` , qui est généralement le meilleur moyen d’une approche, même si vous écrivez davantage de données en conséquence.
>

## <a name="selectinto"></a>SÉLECTIONNEZ... DANS
Vous trouverez peut-être `SELECT..INTO` apparaît dans un nombre d’emplacements dans votre solution.

Voici un exemple d’un `SELECT..INTO` instruction :

```sql
SELECT *
INTO    #tmp_fct
FROM    [dbo].[FactInternetSales]
```

Pour convertir la ci-dessus pour `CTAS` est assez directe :

```sql
CREATE TABLE #tmp_fct
WITH
(
    DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

> [AZURE.NOTE] SACT requiert actuellement qu'une colonne de distribution soit spécifié.  Si vous n’essayez pas intentionnellement modifier la colonne de distribution, votre `CTAS` effectuera la plus rapide si vous sélectionnez une colonne de distribution qui est identique à la table sous-jacente car cette stratégie évite le déplacement de données.  Si vous créez une petite table où performances ne sont pas un facteur, puis vous pouvez spécifier `ROUND_ROBIN` pour éviter de devoir décide sur une colonne de distribution.

## <a name="ansi-join-replacement-for-update-statements"></a>Remplacement de jointure ANSI pour les instructions de mise à jour

Il est possible que vous avez une mise à jour complexe qui joint les tables plus de deux collaborer à l’aide de ANSI rejoindre syntaxe pour effectuer la mise à jour ou la suppression.

Supposons que vous deviez mettre à jour de ce tableau :

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
(   [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
,   [CalendarYear]                  SMALLINT        NOT NULL
,   [TotalSalesAmount]              MONEY           NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
)
;
```

La requête d’origine peut avoir trouvé ressembler à ceci :

```sql
UPDATE  acs
SET     [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT  [EnglishProductCategoryName]
        ,       [CalendarYear]
        ,       SUM([SalesAmount])              AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]       AS s
        JOIN    [dbo].[DimDate]                 AS d    ON s.[OrderDateKey]             = d.[DateKey]
        JOIN    [dbo].[DimProduct]              AS p    ON s.[ProductKey]               = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]   AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]      AS c    ON u.[ProductCategoryKey]       = c.[ProductCategoryKey]
        WHERE   [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,       [CalendarYear]
        ) AS fis
ON  [acs].[EnglishProductCategoryName]  = [fis].[EnglishProductCategoryName]
AND [acs].[CalendarYear]                = [fis].[CalendarYear]
;
```

Dans la mesure où SQL Data Warehouse ne prend pas en charge ANSI joint dans la `FROM` clause d’un `UPDATE` instruction, vous ne pouvez pas copier ce code sur sans modifier légèrement.

Vous pouvez utiliser une combinaison d’une `CTAS` et une jointure implicite pour remplacer ce code :

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT  ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0)    AS [EnglishProductCategoryName]
,       ISNULL(CAST([CalendarYear] AS SMALLINT),0)                      AS [CalendarYear]
,       ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)                     AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]       AS s
JOIN    [dbo].[DimDate]                 AS d    ON s.[OrderDateKey]             = d.[DateKey]
JOIN    [dbo].[DimProduct]              AS p    ON s.[ProductKey]               = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]   AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]      AS c    ON u.[ProductCategoryKey]       = c.[ProductCategoryKey]
WHERE   [CalendarYear] = 2004
GROUP BY
        [EnglishProductCategoryName]
,       [CalendarYear]
;

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]               = AnnualCategorySales.[CalendarYear]
;

--Drop the interim table
DROP TABLE CTAS_acs
;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>Remplacement de jointure ANSI pour supprimer des instructions
La meilleure approche pour supprimer des données est parfois utiliser `CTAS`. Au lieu de supprimer les données simplement sélectionner les données que vous souhaitez conserver. Cette particulièrement vrai pour `DELETE` les instructions qui utilisent ansi syntaxe joint depuis SQL Data Warehouse ne prend pas en charge ANSI joint dans la `FROM` clause d’un `DELETE` instruction.

Exemple d’une instruction DELETE converti est disponible ci-dessous :

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you wish to keep
SELECT     p.ProductKey
,          p.EnglishProductName
,          p.Color
FROM       dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON         p.ProductKey = s.ProductKey
;

RENAME OBJECT dbo.DimProduct        TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>Remplacez les instructions de fusion
Instructions de fusion pouvant être remplacées, au moins composant, en utilisant `CTAS`. Vous pouvez consolider les `INSERT` et la `UPDATE` dans une instruction unique. Tous les enregistrements supprimés devront être fermé désactiver dans une deuxième instruction.

Exemple d’un `UPSERT` est le suivant :

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT      s.[ProductKey]
,           s.[EnglishProductName]
,           s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
,           p.[EnglishProductName]
,           p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
)
;

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimpProduct_upsert]  TO [DimProduct];

```

## <a name="ctas-recommendation-explicitly-state-data-type-and-nullability-of-output"></a>Recommandation SACT : déclarer explicitement le type de données et les valeurs NULL de sortie

Lors de la migration de code, vous pouvez trouver que rencontrer ce type de modèle de codage :

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f
;
```

Si vous pensez instinctivement vous devriez passer ce code à un SACT et vous est correcte. Toutefois, il existe un problème masqué ici.

Le code suivant ne produit pas le même résultat :

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455
;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result
;
```

Notez que la colonne « résultat » reprend les valeurs de type et la possibilité de valeur NULL des données de l’expression. Cela peut affecter les variances légères des valeurs si vous ne faites pas attention.

Essayez ce qui suit par exemple :

```sql
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

La valeur stockée pour résultat est différente. L’erreur devient encore plus significative de la valeur persistante dans la colonne résultat est utilisée dans d’autres expressions.

![][1]

Ceci est particulièrement important pour les migrations de données. Même si la deuxième requête est sans doute plus précise présente un problème. Les données doivent être différentes par rapport au système source et qui permet d’accéder à des questions d’intégrité lors de la migration. Il s’agit d’une de ces rares cas où la réponse « incorrecte » est en réalité le bon !

La raison pour laquelle que nous voyons ce disparités entre les deux résultats est vers le bas jusqu'à la conversion de type implicite. Dans le premier exemple la table définit la définition de colonne. Lorsque la ligne est insérée une conversion de type implicite se produit. Dans le second exemple il n’existe aucune conversion implicite comme l’expression définit le type de données de la colonne. Notez également que la colonne dans le second exemple a été définie sous forme de colonne NULLable alors que dans le premier exemple n’a pas. Lorsque la table a été créée dans la première valeur NULL autorisée de colonne exemple a été explicitement définie. Dans le second exemple qu'il a été simplement crochet à l’expression et par défaut cela entraînerait une définition NULL.  

Pour résoudre ces problèmes que vous devez définir explicitement la conversion de type et la possibilité de valeurs NULL dans la `SELECT` partie de la `CTAS` instruction. Vous ne pouvez pas définir ces propriétés dans la partie tableau de créer.

L’exemple ci-dessous montre comment corriger le code :

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Notez les points suivants :
- CAST ou CONVERT aurait pu être utilisé
- ISNULL est utilisée pour forcer la fusion pas null
- ISNULL est la fonction extérieure
- La deuxième partie de la ISNULL est une constante c'est-à-dire 0

> [AZURE.NOTE] La possibilité de valeur null à être est correctement définie est essentiel d’utiliser `ISNULL` et non `COALESCE`. `COALESCE`n’est pas une fonction déterministe et donc le résultat de l’expression sera toujours NULLable. `ISNULL`est différente. Il est déterministe. Par conséquent, lors de la deuxième partie de la `ISNULL` fonction est une constante ou un opérateur, la valeur résultante sera non NULL.

Cette info-bulle n’est pas simplement utile pour garantir l’intégrité de vos calculs. Il est également important pour basculer entre les partition table. Supposons que vous disposiez ce tableau définit comme votre fait :

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
,   [product]   INT     NOT NULL
,   [store]     INT     NOT NULL
,   [quantity]  INT     NOT NULL
,   [price]     MONEY   NOT NULL
,   [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

Toutefois, le champ de valeur est une expression calculée ne fait pas partie de la source de données.

Pour créer votre groupe de données partitionnée, vous souhaiterez peut-être procédez comme suit :

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create')
;
```

La requête s’exécute parfaitement. Un problème se pose lorsque vous essayez d’effectuer la transition partition. Les définitions de table ne correspondent pas. Pour que les définitions de table corresponde à la SACT doit être modifié.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Par conséquent, vous pouvez voir que la cohérence de type et la mise à jour des propriétés de valeurs NULL dans un SACT est une bonne pratique de meilleures ingénierie. Il vous permet de maintenir l’intégrité de vos calculs et également garantit que le changement de partition est possible.

Reportez-vous à MSDN pour plus d’informations sur l’utilisation de [SACT][]. Il s’agit des instructions les plus importantes dans SQL Azure Data Warehouse. Veillez à que bien comprendre.

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir d’autres conseils de développement, voir [vue d’ensemble du développement][].

<!--Image references-->
[1]: media/sql-data-warehouse-develop-ctas/ctas-results.png

<!--Article references-->
[vue d’ensemble de développement]: sql-data-warehouse-overview-develop.md
[Statistiques]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[SACT]: https://msdn.microsoft.com/library/mt204041.aspx

<!--Other Web references-->
