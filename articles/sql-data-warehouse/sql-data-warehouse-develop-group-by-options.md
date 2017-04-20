<properties
   pageTitle="Regrouper par options SQL Data Warehouse | Microsoft Azure"
   description="Conseils pour l’implémentation de groupe par options dans SQL Azure Data Warehouse pour développer des solutions."
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

# <a name="group-by-options-in-sql-data-warehouse"></a>Regrouper par options dans Data Warehouse SQL

La clause [GROUP BY][] est utilisée pour agréger des données à un ensemble de synthèse de lignes. Il est également plusieurs options qui étendent ses fonctionnalités qui doivent être contournées qu’ils ne sont pas directement pris en charge par Azure SQL Data Warehouse.

Ces options sont
- GROUP BY avec ROLLUP
- JEUX DE REGROUPEMENT
- GROUP BY avec CUBE

## <a name="rollup-and-grouping-sets-options"></a>Définit les options report et de regroupement
L’option la plus simple consiste à utiliser `UNION ALL` à la place pour effectuer le cumul plutôt que de laisser la syntaxe explicite. Le résultat est exactement le même

Voici un exemple d’un groupe à l’aide de déclaration la `ROLLUP` option :

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount)             AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t       ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY ROLLUP (
                        [SalesTerritoryCountry]
                ,       [SalesTerritoryRegion]
                )
;
```

À l’aide de report nous avons demandé les agrégations suivantes :
- Nationaux et régionaux
- Pays
- Total général

Pour remplacer ce que vous devrez utiliser `UNION ALL`; spécifier les agrégations explicitement obligatoire pour renvoyer les mêmes résultats :

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
UNION ALL
SELECT [SalesTerritoryCountry]
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
UNION ALL
SELECT NULL
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey;
```

Pour les jeux de regroupement tout ce que nous suffit est adoptent la même entité mais ne créer que UNION toutes les sections pour les niveaux d’agrégation que nous souhaitons pouvoir consulter

## <a name="cube-options"></a>Options de cube
Il est possible de créer un GROUP BY avec CUBE à l’aide de l’approche UNION ALL. Le problème est que le code peut devenir rapidement encombrant et encombrant. Pour atténuer ce, vous pouvez utiliser cette plus avancée approche.

Nous allons utiliser l’exemple ci-dessus.

La première étape consiste à définir le cube' ' qui définit tous les niveaux d’agrégation que nous voulons créer. Il est important de prendre note de la jointure croisée des deux tables dérivées. Cette action génère tous les niveaux pour nous. Le reste du code est-il vraiment de mise en forme.

```sql
CREATE TABLE #Cube
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
WITH GrpCube AS
(SELECT    CAST(ISNULL(Country,'NULL')+','+ISNULL(Region,'NULL') AS NVARCHAR(50)) as 'Cols'
,          CAST(ISNULL(Country+',','')+ISNULL(Region,'') AS NVARCHAR(50))  as 'GroupBy'
,          ROW_NUMBER() OVER (ORDER BY Country) as 'Seq'
FROM       ( SELECT 'SalesTerritoryCountry' as Country
             UNION ALL
             SELECT NULL
           ) c
CROSS JOIN ( SELECT 'SalesTerritoryRegion' as Region
             UNION ALL
             SELECT NULL
           ) r
)
SELECT Cols
,      CASE WHEN SUBSTRING(GroupBy,LEN(GroupBy),1) = ','
            THEN SUBSTRING(GroupBy,1,LEN(GroupBy)-1)
            ELSE GroupBy
       END AS GroupBy  --Remove Trailing Comma
,Seq
FROM GrpCube;
```

Les résultats de la SACT peuvent être vu ci-dessous :

![][1]

La deuxième étape consiste à spécifier une table cible pour stocker les résultats temporaires :

```sql
DECLARE
 @SQL NVARCHAR(4000)
,@Columns NVARCHAR(4000)
,@GroupBy NVARCHAR(4000)
,@i INT = 1
,@nbr INT = 0
;
CREATE TABLE #Results
(
 [SalesTerritoryCountry] NVARCHAR(50)
,[SalesTerritoryRegion]  NVARCHAR(50)
,[TotalSalesAmount]      MONEY
)
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
;
```

La troisième étape consiste à effectuer une boucle sur notre cube de colonnes effectuer l’agrégation. La requête est exécutée une fois pour chaque ligne dans la table temporaire #Cube et stocker les résultats de la table temporaire #Results

```sql
SET @nbr =(SELECT MAX(Seq) FROM #Cube);

WHILE @i<=@nbr
BEGIN
    SET @Columns = (SELECT Cols    FROM #Cube where seq = @i);
    SET @GroupBy = (SELECT GroupBy FROM #Cube where seq = @i);

    SET @SQL ='INSERT INTO #Results
              SELECT '+@Columns+'
              ,      SUM(SalesAmount) AS TotalSalesAmount
              FROM  dbo.factInternetSales s
              JOIN  dbo.DimSalesTerritory t  
              ON s.SalesTerritoryKey = t.SalesTerritoryKey
              '+CASE WHEN @GroupBy <>''
                     THEN 'GROUP BY '+@GroupBy ELSE '' END

    EXEC sp_executesql @SQL;
    SET @i +=1;
END
```

Enfin, nous pouvons renvoyer les résultats en lisant simplement de la table temporaire #Results

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Par divisions le code en sections et générer une construction en boucle le code devient plus gérable et facile à gérer.


## <a name="next-steps"></a>Étapes suivantes
Pour obtenir d’autres conseils de développement, voir [vue d’ensemble du développement][].

<!--Image references-->
[1]: media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png

<!--Article references-->
[vue d’ensemble de développement]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[REGROUPER PAR]: https://msdn.microsoft.com/library/ms177673.aspx


<!--Other Web references-->
