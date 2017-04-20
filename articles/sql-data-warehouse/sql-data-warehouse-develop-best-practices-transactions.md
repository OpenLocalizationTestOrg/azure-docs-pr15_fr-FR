<properties
   pageTitle="Optimisation des transactions pour SQL Data Warehouse | Microsoft Azure"
   description="Meilleures pratiques recommandées sur l’écriture de mises à jour des transactions efficace dans SQL Azure Data Warehouse"
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
   ms.date="07/31/2016"
   ms.author="jrj;barbkess"/>

# <a name="optimizing-transactions-for-sql-data-warehouse"></a>Optimisation de transactions pour Data Warehouse SQL

Cet article explique comment optimiser les performances de votre code transactions en limitant les risques les annulations long.

## <a name="transactions-and-logging"></a>Transactions et journalisation

Les transactions sont un composant important d’un moteur de base de données relationnelle. Data Warehouse SQL utilise des transactions pendant la modification des données. Ces transactions peuvent être explicites ou implicites. Seul `INSERT`, `UPDATE` et `DELETE` instructions sont des exemples de transactions implicites. Transactions explicites sont écrits explicitement par un développeur à l’aide de `BEGIN TRAN`, `COMMIT TRAN` ou `ROLLBACK TRAN` et sont généralement utilisés lorsque plusieurs instructions de modification doivent être liés entre eux dans une seule unité atomique. 

Data Warehouse SQL Azure valide les modifications apportées à la base de données à l’aide des journaux des transactions. Chaque distribution possède son propre journal des transactions. Écritures de journal des transactions sont automatiques. Il n’existe aucune configuration requise. Toutefois, tandis que ce processus garantit l’écriture il présente une surcharge dans le système. Vous pouvez réduire cet impact en écrivant du code transactionnel efficace. Code transactionnel efficace se situe largement en deux catégories.

- Tirer parti des constructions de journalisation minimale lorsque cela est possible
- Traiter les données à l’aide de feuilles dans l’étendue afin d’éviter les transactions à long terme au singulier
- Arrêter une partition passer motif pour des modifications volumineux à une partition donnée

## <a name="minimal-vs-full-logging"></a>Minimales par rapport à la journalisation complète

Contrairement aux opérations entièrement consignées, qui utilisent le journal des transactions pour effectuer le suivi de chaque modification de ligne, les opérations journalisées effectuer le suivi des affectations de mesure et que les modifications de métadonnées. Par conséquent, la journalisation minimale implique journalisation uniquement les informations requises pour annuler la transaction en cas d’une défaillance ou une requête explicite (`ROLLBACK TRAN`). Comme beaucoup moins informations sont suivies dans le journal des transactions, une opération journalisée effectue mieux qu’une opération entièrement enregistrée de même taille. En outre, étant donné que moins écritures vont le journal des transactions, une quantité plus petite quantité de données du journal est générée et est donc e/s plus efficace.

Les limites de sécurité transaction s’applique uniquement aux opérations entièrement consignées.

>[AZURE.NOTE] Les opérations journalisées peuvent participer à des transactions explicites. Comme toutes les modifications dans les structures de répartition sont suivies, il est possible de restaurer les opérations journalisées. Il est important de comprendre que la modification est » « journalisée il n’est pas non connecté.

## <a name="minimally-logged-operations"></a>Opérations journalisées

Les opérations suivantes sont capables de cours journalisées :

- CRÉER la TABLE en tant que SELECT ([SACT][])
- INSÉRER... SÉLECTIONNEZ
- CRÉER UN INDEX
- ALTER RECONSTRUCTION D’INDEX
- SUPPRIMER L’INDEX
- TRONQUER LA TABLE
- SUPPRESSION DE TABLE
- ALTER TABLE COMMUTATEUR PARTITION

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

>[AZURE.NOTE] Opérations de déplacement de données internes (tel que `BROADCAST` et `SHUFFLE`) ne sont pas concernés par la limite de sécurité transaction.

## <a name="minimal-logging-with-bulk-load"></a>Journalisation minimale avec chargement en bloc

`CTAS`et `INSERT...SELECT` sont tous deux en bloc des opérations de chargement. Toutefois, les deux dépendent de la définition de la table cible et dépendent le scénario de charge. Vous trouverez ci-dessous une table qui explique si votre opération en bloc est entièrement ou au minimum enregistrée :  

| Index primaire               | Scénario de charge                                            | Mode de journalisation |
| --------------------------- | -------------------------------------------------------- | ------------ |
| Segment                        | Tout                                                      | **Minimale**  |
| Index groupé             | Table cible vide                                       | **Minimale**  |
| Index groupé             | Lignes chargées ne se chevauchent pas avec des pages existantes dans cible | **Minimale**  |
| Index groupé             | Lignes chargées en conflit avec des pages existantes dans cible        | Complète         |
| Index Columnstore groupé | Taille de lot > = 102,400 par distribution partition alignée | **Minimale**  |
| Index Columnstore groupé | Traitement par lots de taille < 102,400 par distribution partition alignée  | Complète         |

Il est important de noter que toutes les écritures pour mettre à jour à partir d’index secondaire ou non cluster sera toujours opérations entièrement consignées.

> [AZURE.IMPORTANT] Data Warehouse SQL présente 60 distribution. Par conséquent, en supposant que toutes les lignes sont répartis et ne soient insérés dans une seule partition, votre lot doit contenir 6,144,000 lignes ou plus grand pour être journalisées lors de l’écriture dans un Columnstore Index groupé. Si la table est segmentée et frontières partition s’étalent sur les lignes à insérer, vous devrez 6,144,000 lignes par limite partition en supposant que même répartition des données. Chaque partition dans chaque distribution doit dépasser indépendamment le seuil de 102 400 ligne d’insertion à être journalisées dans la distribution.

Chargement de données dans une table non vide avec un index cluster peut contenir souvent une combinaison de lignes entièrement connectés et journalisées. Un index cluster est une arborescence équilibrée (arborescence b) des pages. Si la page écrits dans contient déjà des lignes à partir d’une autre transaction, puis ces écritures seront entièrement connectés. Toutefois, si la page est vide puis l’écriture à cette page est consignées dans le journal.

## <a name="optimizing-deletes"></a>Optimisation de suppressions

`DELETE`est une opération entièrement enregistrée.  Si vous avez besoin de supprimer une grande quantité de données dans une table ou d’une partition, il est souvent judicieux `SELECT` les données que vous souhaitez conserver, lequel peuvent être exécutées en tant qu’une opération journalisée.  Pour ce faire, créez une nouvelle table avec [SACT][].  Une fois créé, cliquez sur [Renommer][] pour remplacer votre ancienne table avec la table nouvellement créée.

```sql
-- Delete all sales transactions for Promotions except PromotionKey 2.

--Step 01. Create a new table select only the records we want to kep (PromotionKey 2)
CREATE TABLE [dbo].[FactInternetSales_d]
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES  (   20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,   20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,   20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,   20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,   20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [PromotionKey] = 2
OPTION (LABEL = 'CTAS : Delete')
;

--Step 02. Rename the Tables to replace the 
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_d] TO [FactInternetSales];
```

## <a name="optimizing-updates"></a>Optimisation des mises à jour

`UPDATE`est une opération entièrement enregistrée.  Si vous devez mettre à jour un grand nombre de lignes dans une table ou une partition il est souvent beaucoup plus efficace d’utiliser une opération journalisée comme [SACT][] pour le faire.

Dans l’exemple ci-dessous, un tableau complet mise à jour a été converti en une `CTAS` afin que journalisation minimale est possible.

Dans ce cas nous ajoutons façon rétrospective un montant d’escompte pour les ventes dans le tableau :

```sql
--Step 01. Create a new table containing the "Update". 
CREATE TABLE [dbo].[FactInternetSales_u]
WITH
(   CLUSTERED INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES  (   20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,   20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,   20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,   20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,   20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,   [OrderDateKey] 
,   [DueDateKey]  
,   [ShipDateKey] 
,   [CustomerKey] 
,   [PromotionKey] 
,   [CurrencyKey] 
,   [SalesTerritoryKey]
,   [SalesOrderNumber]
,   [SalesOrderLineNumber]
,   [RevisionNumber]
,   [OrderQuantity]
,   [UnitPrice]
,   [ExtendedAmount]
,   [UnitPriceDiscountPct]
,   ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,   [ProductStandardCost]
,   [TotalProductCost]
,   ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,   [TaxAmt]
,   [Freight]
,   [CarrierTrackingNumber] 
,   [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
OPTION (LABEL = 'CTAS : Update')
;

--Step 02. Rename the tables
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_u] TO [FactInternetSales];

--Step 03. Drop the old table
DROP TABLE [dbo].[FactInternetSales_old]
```

> [AZURE.NOTE] Recréer les tables volumineuses peut bénéficier de l’utilisation des fonctionnalités de gestion de la charge de travail Data Warehouse SQL. Pour plus d’informations reportez-vous à la section Gestion de la charge de travail dans l’article de la [concurrence][] .

## <a name="optimizing-with-partition-switching"></a>Optimisation de changement de partition

Soumettre à examen modifications grande échelle à l’intérieur d’une [partition de table][], une partition passer motif rend très utile. Si la modification des données est importante et s’étend sur plusieurs partitions, puis simplement une itération sur les partitions donne le même résultat.

Les étapes pour effectuer un commutateur partition sont les suivantes :
1. Créer un vide partition
2. Effectuer la « mise à jour » comme un SACT
3. Éliminer les données existantes à la table de sortie
4. Basculer les nouvelles données
5. Nettoyer les données

Toutefois, pour aider à identifier les partitions pour basculer nous devez tout d’abord créer une procédure d’assistance comme celui ci-dessous. 

```sql
CREATE PROCEDURE dbo.partition_data_get
    @schema_name           NVARCHAR(128)
,   @table_name            NVARCHAR(128)
,   @boundary_value        INT
AS
IF OBJECT_ID('tempdb..#ptn_data') IS NOT NULL
BEGIN
    DROP TABLE #ptn_data
END
CREATE TABLE #ptn_data
WITH    (   DISTRIBUTION = ROUND_ROBIN
        ,   HEAP
        )
AS
WITH CTE
AS
(
SELECT  s.name                          AS [schema_name]
,       t.name                          AS [table_name]
,       p.partition_number              AS [ptn_nmbr]
,       p.[rows]                        AS [ptn_rows]
,       CAST(r.[value] AS INT)          AS [boundary_value]
FROM        sys.schemas                 AS s
JOIN        sys.tables                  AS t    ON  s.[schema_id]       = t.[schema_id]
JOIN        sys.indexes                 AS i    ON  t.[object_id]       = i.[object_id]
JOIN        sys.partitions              AS p    ON  i.[object_id]       = p.[object_id] 
                                                AND i.[index_id]        = p.[index_id] 
JOIN        sys.partition_schemes       AS h    ON  i.[data_space_id]   = h.[data_space_id]
JOIN        sys.partition_functions     AS f    ON  h.[function_id]     = f.[function_id]
LEFT JOIN   sys.partition_range_values  AS r    ON  f.[function_id]     = r.[function_id] 
                                                AND r.[boundary_id]     = p.[partition_number]
WHERE i.[index_id] <= 1
)
SELECT  *
FROM    CTE
WHERE   [schema_name]       = @schema_name
AND     [table_name]        = @table_name
AND     [boundary_value]    = @boundary_value
OPTION (LABEL = 'dbo.partition_data_get : CTAS : #ptn_data')
;
GO
```

Cette procédure optimise la réutilisation de code et conserve la partition passer exemple plus compact.

Le code ci-dessous montre les cinq étapes mentionnés ci-dessus pour obtenir une partition complète passer routine.

```sql
--Create a partitioned aligned empty table to switch out the data 
IF OBJECT_ID('[dbo].[FactInternetSales_out]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_out]
END

CREATE TABLE [dbo].[FactInternetSales_out]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED COLUMNSTORE INDEX
,   PARTITION   (   [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES  (   20020101, 20030101
                                                )
                )
)
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE 1=2
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Create a partitioned aligned table and update the data in the select portion of the CTAS
IF OBJECT_ID('[dbo].[FactInternetSales_in]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_in]
END

CREATE TABLE [dbo].[FactInternetSales_in]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED COLUMNSTORE INDEX
,   PARTITION   (   [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES  (   20020101, 20030101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,   [OrderDateKey] 
,   [DueDateKey]  
,   [ShipDateKey] 
,   [CustomerKey] 
,   [PromotionKey] 
,   [CurrencyKey] 
,   [SalesTerritoryKey]
,   [SalesOrderNumber]
,   [SalesOrderLineNumber]
,   [RevisionNumber]
,   [OrderQuantity]
,   [UnitPrice]
,   [ExtendedAmount]
,   [UnitPriceDiscountPct]
,   ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,   [ProductStandardCost]
,   [TotalProductCost]
,   ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,   [TaxAmt]
,   [Freight]
,   [CarrierTrackingNumber] 
,   [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
WHERE   OrderDateKey BETWEEN 20020101 AND 20021231
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Use the helper procedure to identify the partitions
--The source table
EXEC dbo.partition_data_get 'dbo','FactInternetSales',20030101
DECLARE @ptn_nmbr_src INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_src

--The "in" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_in',20030101
DECLARE @ptn_nmbr_in INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_in

--The "out" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_out',20030101
DECLARE @ptn_nmbr_out INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_out

--Switch the partitions over
DECLARE @SQL NVARCHAR(4000) = '
ALTER TABLE [dbo].[FactInternetSales]   SWITCH PARTITION '+CAST(@ptn_nmbr_src AS VARCHAR(20))   +' TO [dbo].[FactInternetSales_out] PARTITION ' +CAST(@ptn_nmbr_out AS VARCHAR(20))+';
ALTER TABLE [dbo].[FactInternetSales_in] SWITCH PARTITION '+CAST(@ptn_nmbr_in AS VARCHAR(20))   +' TO [dbo].[FactInternetSales] PARTITION '     +CAST(@ptn_nmbr_src AS VARCHAR(20))+';'
EXEC sp_executesql @SQL

--Perform the clean-up
TRUNCATE TABLE dbo.FactInternetSales_out;
TRUNCATE TABLE dbo.FactInternetSales_in;

DROP TABLE dbo.FactInternetSales_out
DROP TABLE dbo.FactInternetSales_in
DROP TABLE #ptn_data
```

## <a name="minimize-logging-with-small-batches"></a>Réduire la journalisation à partir de petits lots

Pour les opérations de modification de données de grande taille, il peut être préférable de diviser l’opération en segments ou des lots pour définir la portée l’unité de travail.

Vous trouverez ci-dessous un exemple pratique. La taille du lot a été définie pour un nombre simple pour mettre en évidence la technique. En réalité, la taille du lot serait beaucoup plus important. 

```sql
SET NO_COUNT ON;
IF OBJECT_ID('tempdb..#t') IS NOT NULL
BEGIN
    DROP TABLE #t;
    PRINT '#t dropped';
END

CREATE TABLE #t
WITH    (   DISTRIBUTION = ROUND_ROBIN
        ,   HEAP
        )
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS seq_nmbr
,       SalesOrderNumber
,       SalesOrderLineNumber
FROM    dbo.FactInternetSales
WHERE   [OrderDateKey] BETWEEN 20010101 and 20011231
;

DECLARE @seq_start      INT = 1
,       @batch_iterator INT = 1
,       @batch_size     INT = 50
,       @max_seq_nmbr   INT = (SELECT MAX(seq_nmbr) FROM dbo.#t)
;

DECLARE @batch_count    INT = (SELECT CEILING((@max_seq_nmbr*1.0)/@batch_size))
,       @seq_end        INT = @batch_size
;

SELECT COUNT(*)
FROM    dbo.FactInternetSales f

PRINT 'MAX_seq_nmbr '+CAST(@max_seq_nmbr AS VARCHAR(20))
PRINT 'MAX_Batch_count '+CAST(@batch_count AS VARCHAR(20))

WHILE   @batch_iterator <= @batch_count
BEGIN
    DELETE
    FROM    dbo.FactInternetSales
    WHERE EXISTS
    (
            SELECT  1
            FROM    #t t
            WHERE   seq_nmbr BETWEEN  @seq_start AND @seq_end
            AND     FactInternetSales.SalesOrderNumber      = t.SalesOrderNumber
            AND     FactInternetSales.SalesOrderLineNumber  = t.SalesOrderLineNumber
    )
    ;

    SET @seq_start = @seq_end
    SET @seq_end = (@seq_start+@batch_size);
    SET @batch_iterator +=1;
END
```

## <a name="pause-and-scaling-guidance"></a>Pause et la même échelle recommandations

Data Warehouse SQL Azure vous permet de suspendre, reprendre ou mettre à l’échelle votre data warehouse à la demande. Lorsque vous placez le curseur ou mettre à l’échelle votre Data Warehouse SQL, il est important de comprendre que toutes les transactions en vol sont terminées immédiatement ; à l’origine de toutes les transactions ouvertes à restaurer. Si votre charge de travail avait délivré une modification des données depuis longtemps et incomplète avant l’opération pause ou échelle, ce travail devez être annulées. Cela peut affecter le temps que nécessaire pour suspendre ou mettre à l’échelle votre base de données SQL Azure Data Warehouse. 

> [AZURE.IMPORTANT] Les deux `UPDATE` et `DELETE` sont opérations entièrement consignées et afin d’annuler/rétablir ces opérations peuvent prendre beaucoup plus longtemps qu’équivalent journalisées opérations. 

Le meilleur scénario consiste à laisser dans les transactions de modification des données aérienne complètes avant la pause ou mise à l’échelle Data Warehouse SQL. Toutefois, cela peut ne pas toujours être pratique. Pour limiter le risque d’annulation d’un long, envisagez l’une des options suivantes :

- Réécriture d’opérations longues à l’aide de [SACT][]
- Répartir l’opération en segments ; fonctionne sur un sous-ensemble des lignes

## <a name="next-steps"></a>Étapes suivantes

Voir la rubrique [Transactions dans Data Warehouse SQL][] pour en savoir plus sur les niveaux d’isolement et les limites de transactions.  Pour une vue d’ensemble des autres recommandations, voir [Recommandations entrepôt de données SQL][].

<!--Image references-->

<!--Article references-->
[Transactions dans Data Warehouse SQL]: ./sql-data-warehouse-develop-transactions.md
[partition de table]: ./sql-data-warehouse-tables-partition.md
[Concurrence]: ./sql-data-warehouse-develop-concurrency.md
[SACT]: ./sql-data-warehouse-develop-ctas.md
[Meilleures pratiques SQL données entrepôt]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->
[alter index]:https://msdn.microsoft.com/library/ms188388.aspx
[RENOMMER]: https://msdn.microsoft.com/library/mt631611.aspx

<!-- Other web references -->

