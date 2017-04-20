<properties
    pageTitle="SQL en mémoire, prise en main | Microsoft Azure"
    description="Technologies SQL en mémoire améliorent considérablement les performances des transactions et les charges de travail analytique. Découvrez comment tirer parti de ces technologies."
    services="sql-database"
    documentationCenter=""
    authors="jodebrui"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="jodebrui"/>


# <a name="get-started-with-in-memory-preview-in-sql-database"></a>Prise en main en mémoire (Preview) dans la base de données SQL

Fonctionnalités en mémoire améliorent considérablement les performances des transactions et les charges de travail analytique dans les situations de droite.

Cette rubrique met l’accent sur deux démonstrations, une pour OLTP en mémoire et un pour Analytique en mémoire. Chaque démonstration est fourni avec les étapes et le code que vous devez exécuter la démonstration. Vous pouvez :

- Utiliser le code pour tester les variantes pour voir les différences dans les résultats de performances ; ou
- Lisez le code pour comprendre le scénario et pour savoir comment créer et utiliser les objets en mémoire.

> [AZURE.VIDEO azure-sql-database-in-memory-technologies]

- [1 de démarrage rapide : en mémoire OLTP Technologies plus rapidement les performances T-SQL](http://msdn.microsoft.com/library/mt694156.aspx) -est un autre article pour vous aider à commencer.

#### <a name="in-memory-oltp"></a>OLTP en mémoire

Les fonctionnalités d’en mémoire [OLTP](#install_oltp_manuallink) (traitement des transactions en ligne) sont :

- Tables de mémoire optimisée.
- En mode natif compilé des procédures stockées.


Une table de mémoire optimisée comporte une représentation de lui-même dans la mémoire active, en plus de la représentation standard sur un disque dur. Transactions d’entreprise par rapport à la table s’exécuter plus vite, car ils interagissent directement avec uniquement la représentation qui se trouve dans la mémoire active.

Avec OLTP en mémoire, vous pouvez obtenir des jusqu'à 30 fois quant débit des transactions, selon les caractéristiques de la charge de travail.


Procédures stockées en mode natif compilées nécessitent moins instructions machine pendant l’exécution qu’interprété traditionnels des procédures stockées. Nous avons vu résultat de la compilation native durées qui sont comprises entre 1/100 de la durée interprétée.


#### <a name="in-memory-analytics"></a>Analytique en mémoire 

La fonctionnalité en mémoire [Analytique](#install_analytics_manuallink) est la suivante :

Index ColumnStore améliorent les performances d’analytique et requêtes de rapport. 


#### <a name="real-time-analytics"></a>Analytique en temps réel

Pour [En temps réel Analytique](http://msdn.microsoft.com/library/dn817827.aspx) vous combinez OLTP en mémoire et Analytique pour obtenir :

- Compétences professionnelles en temps réel basés sur des données opérationnelles.


#### <a name="availability"></a>Disponibilité


Disponibilité générale, officielle :

- [Index Columnstore](http://msdn.microsoft.com/library/dn817827.aspx) qui se trouvent *sur le disque*.


Aperçu :

- OLTP en mémoire
- Analytique opérationnels en temps réel


Considérations tandis que les fonctionnalités en mémoire sont dans l’aperçu sont décrites [plus loin dans cette rubrique](#preview_considerations_for_in_memory).


> [AZURE.NOTE] Ces fonctionnalités sont disponibles uniquement pour les bases de données [*Premium*](sql-database-service-tiers.md) pas dans des pools élastiques et non disponible pour les bases de données Basic ou Standard.  Prise en charge des bases de données Premium dans des pools élastiques sera bientôt disponible. 


<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="a-install-the-in-memory-oltp-sample"></a>A. Installer l’échantillon OLTP en mémoire

Vous pouvez créer la base de données exemple AdventureWorksLT [V12] en quelques clics dans le [portail Azure](https://portal.azure.com/). Puis les étapes décrites dans cette section expliquent comment vous pouvez enrichir votre base de données AdventureWorksLT avec :

- Tables en mémoire.
- Une procédure stockée en mode natif compilée.


#### <a name="installation-steps"></a>Étapes d’installation

1. Dans le [portail Azure](https://portal.azure.com/), créer une base de données Premium sur un serveur V12. Définir la **Source** à la base de données exemple AdventureWorksLT [V12].
 - Pour obtenir des instructions détaillées, vous pouvez voir [créer votre première base de données SQL Azure](sql-database-get-started.md).

2. Se connecter à la base de données avec SQL Server Management Studio [(SSMS.exe)](http://msdn.microsoft.com/library/mt238290.aspx).

3. Copiez le [script en mémoire OLTP Transact-SQL](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_oltp_sample.sql) dans votre Presse-papiers.
 - Le script T-SQL crée les objets nécessaires en mémoire dans la base de données exemple AdventureWorksLT créée à l’étape 1.

4. Collez le script T-SQL dans SSMS, puis exécutez le script.
 - Crucial est la `MEMORY_OPTIMIZED = ON` instructions CREATE TABLE clause, comme dans :


```
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>Erreur 40536


Si vous obtenez une erreur 40536 lorsque vous exécutez le script T-SQL, exécutez le script T-SQL suivant pour vérifier si la base de données prend en charge en mémoire :


```
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


Un résultat de **0** signifie en mémoire n’est pas prise en charge et 1 indique qu’il est pris en charge. Pour identifier le problème :

- Vérifiez que la base de données a été créé après que les fonctionnalités en mémoire OLTP est devenue actives pour l’aperçu.
- Assurez-vous que la base de données est au niveau de service Premium.


#### <a name="about-the-created-memory-optimized-items"></a>Sur les éléments de mémoire optimisée créés

**Tables**: l’échantillon contienne les tables de mémoire optimisée suivantes :

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


Vous pouvez inspecter mémoire optimisée des tables au moyen de l' **Explorateur d’objets** dans SSMS par :

- Avec le bouton droit de **Tables** > **filtre** > **Paramètres de filtrage** > **Est la mémoire optimisée** est égale à 1.


Ou vous pouvez interroger les vues de catalogue telles que :


```
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**Procédure stockée en mode natif compilée**: SalesLT.usp_InsertSalesOrder_inmem pouvez inspecter via une requête de vue de catalogue :


```
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

## <a name="run-the-sample-oltp-workload"></a>Exécuter la charge de travail OLTP exemple

La seule différence entre les deux suivantes *procédures stockées* est que la première procédure utilise versions mémoire optimisée des tables, tandis que la seconde procédure utilise les tables sur le disque standard :

- SalesLT**.** usp_InsertSalesOrder**_inmem**
- SalesLT**.** usp_InsertSalesOrder**_ondisk**


Dans cette section, vous allez apprendre à utiliser l’utilitaire pratique **ostress.exe** pour exécuter les procédures stockées deux niveaux stressantes. Vous pouvez comparer combien de temps prend l’exécution de deux contrainte pour terminer.


Lorsque vous exécutez ostress.exe, nous vous recommandons de transmettre les valeurs de paramètre conçus pour les deux :

- Exécuter un grand nombre de connexions simultanées, en utilisant - n100.
- Ont par chaque boucle de connexion des centaines de fois, à l’aide de-r500.


Toutefois, vous souhaiterez peut-être démarrer avec beaucoup plus petites valeurs comme - n10 et - r 50 pour garantir la tout fonctionne.


### <a name="script-for-ostressexe"></a>Script de ostress.exe


Cette section affiche le script T-SQL incorporée dans notre ligne de commande ostress.exe. Le script utilise des éléments qui ont été créés par le script T-SQL que vous avez installé précédemment.


Le script suivant insère une commande client exemple avec cinq postes dans la mémoire optimisée *tableaux*suivants :

- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem


```
DECLARE
    @i int = 0,
    @od SalesLT.SalesOrderDetailType_inmem,
    @SalesOrderID int,
    @DueDate datetime2 = sysdatetime(),
    @CustomerID int = rand() * 8000,
    @BillToAddressID int = rand() * 10000,
    @ShipToAddressID int = rand() * 10000;
    
INSERT INTO @od
    SELECT OrderQty, ProductID
    FROM Demo.DemoSalesOrderDetailSeed
    WHERE OrderID= cast((rand()*60) as int);
    
WHILE (@i < 20)
begin;
    EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
        @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
    SET @i = @i + 1;
end
```


Pour rendre la version _ondisk de T-SQL précédente pour ostress.exe, vous devez simplement la remplacer les deux occurrences de la sous-chaîne *_inmem* avec *_ondisk*. Ces remplace affecte les noms des tables et des procédures stockées.


### <a name="install-rml-utilities-and-ostress"></a>Installer ostress et utilitaires recommandés par


Dans l’idéal vous le feriez envisagez d’exécuter ostress.exe sur un ordinateur virtuel Azure. Vous devez créer une [Machine virtuelle Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) dans la même région géographique Azure où réside votre base de données AdventureWorksLT. Mais vous pouvez exécuter ostress.exe sur votre ordinateur portable à la place.


Sur la machine virtuelle, ou sur tout ce que vous hébergez choisir, installez les utilitaires langue recommandés (relecture balisage par), qui incluent ostress.exe.

- Consultez la [Base de données exemple OLTP en mémoire](http://msdn.microsoft.com/library/mt465764.aspx)ostress.exe.
 - Ou consultez la [Base de données exemple OLTP en mémoire](http://msdn.microsoft.com/library/mt465764.aspx).
 - Ou consultez [le Blog pour l’installation ostress.exe](http://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx)



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(http://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(http://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-inmem-stress-workload-first"></a>Exécutez d’abord la charge de travail de contrainte _inmem


Vous pouvez utiliser une fenêtre *d’Invite de commande recommandés par* pour exécuter notre ligne de commande ostress.exe. Les paramètres de ligne de commande direct ostress à :

- Exécuter 100 connexions simultanément (-n100).
- Chaque connexion exécuter le script T-SQL 50 fois (-r 50).


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


Pour exécuter la ligne de commande ostress.exe précédente :


1. Réinitialiser le contenu des données de base de données en exécutant la commande suivante dans SSMS, pour supprimer toutes les données qui a été insérées par toutes les séries de précédents :
```
EXECUTE Demo.usp_DemoReset;
```

2. Copier le texte de la ligne de commande ostress.exe précédente dans votre Presse-papiers.

3. Remplacer le `<placeholders>` pour les paramètres -S - U -P -d avec les valeurs correctes réels.

4. Exécuter votre ligne de commande modifié dans une fenêtre Cmd recommandés par.


#### <a name="result-is-a-duration"></a>Résultat est une durée


Lorsque ostress.exe est terminée, il écrit la durée d’exécution en tant que sa dernière ligne de sortie dans la fenêtre Cmd recommandés par. Par exemple, une série de tests plus courte et dure environ 1,5 minutes :

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-ondisk-then-rerun"></a>Réinitialiser, modifier pour _ondisk, puis exécutez à nouveau


Une fois que vous avez le résultat de l’exécution de _inmem, procédez comme suit pour le _ondisk exécuter :


1. Réinitialiser la base de données en exécutant la commande suivante dans SSMS, pour supprimer toutes les données qui a été insérées par l’exécution précédente :
```
EXECUTE Demo.usp_DemoReset;
```

2. Modifiez la ligne de commande ostress.exe pour remplacer toutes les *_inmem* par *_ondisk*.

3. Exécutez à nouveau ostress.exe pour la deuxième fois et capture le résultat de durée.

4. Réinitialiser à nouveau la base de données pour la suppression responsable de ce qui peut être une grande quantité de données de test.


#### <a name="expected-comparison-results"></a>Résultats de la comparaison attendu

Notre en mémoire des tests ont montré une amélioration des performances **9 fois** pour cette charge de travail simple, avec ostress en cours d’exécution sur un ordinateur virtuel Azure dans la même région Azure en tant que la base de données.



<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;


## <a name="b-install-the-in-memory-analytics-sample"></a>B. Installer l’échantillon Analytique en mémoire


Dans cette section, vous comparez les résultats IO ainsi que les statistiques lors de l’utilisation d’un index columnstore par rapport à un index arbre-b traditionnel.


Pour analytique en temps réel sur une charge de travail OLTP, il est souvent préférable d’utiliser un index columnstore ordonné. Pour plus d’informations, consultez [Columnstore index décrites](http://msdn.microsoft.com/library/gg492088.aspx).



### <a name="prepare-the-columnstore-analytics-test"></a>Préparer le test analytique columnstore


1. Utiliser le portail Azure pour créer une nouvelle base de données AdventureWorksLT à partir de l’échantillon.
 - Utilisez ce nom exact.
 - Sélectionnez n’importe quel niveau de service Premium.

2. Copier la [sql_in memory_analytics_sample](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_analytics_sample.sql) dans votre Presse-papiers.
 - Le script T-SQL crée les objets nécessaires en mémoire dans la base de données exemple AdventureWorksLT créée à l’étape 1.
 - Le script crée la table de Dimension et deux tables de faits. Les tables de faits sont remplis avec 3,5 millions de lignes chaque.
 - Le script peut prendre 15 minutes.

3. Collez le script T-SQL dans SSMS, puis exécutez le script.
 - Essentiel est le mot-clé **COLUMNSTORE** dans une instruction **CREATE INDEX** , comme dans :<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Définir AdventureWorksLT à un niveau de compatibilité 130 :<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`
 - Niveau 130 n’est pas directement liés aux fonctionnalités en mémoire. Mais niveau 130 offre généralement de meilleures performances de requête que 120.


#### <a name="crucial-tables-and-columnstore-indexes"></a>Essentiels tables et index columnstore


- dbo. FactResellerSalesXL_CCI est une table qui comporte un index groupé **columnstore** , ce qui a avancé de compression au niveau des *données* .

- dbo. FactResellerSalesXL_PageCompressed est une table qui comporte un équivalent index groupé normal, qui est compressé uniquement au niveau de la *page* .


#### <a name="crucial-queries-to-compare-the-columnstore-index"></a>Requêtes cruciales pour comparer l’index columnstore


[Voici](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/clustered_columnstore_sample_queries.sql) plusieurs types de requête T-SQL que vous pouvez exécuter pour afficher les performances par rapport aux. À l’étape 2 dans le script T-SQL, il existe une paire de requêtes qui intéressent direct. Les deux requêtes diffèrent uniquement sur une seule ligne :


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


Un index groupé columnstore se trouve dans la table**_CCI** FactResellerSalesXL.

L’extrait de script T-SQL suivant imprime statistiques pour IO et l’heure de la requête de chaque table.


```
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
WHERE e.ProductCategoryKey =2
    AND c.FullDateAlternateKey BETWEEN '1/1/2014' AND '1/1/2015'
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a Clustered Columnstore index CCI 
-- The comparison numbers are even more dramatic the larger the table is, this is a 11 million row table only.
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
WHERE e.ProductCategoryKey =2
    AND c.FullDateAlternateKey BETWEEN '1/1/2014' AND '1/1/2015'
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```



<a id="preview_considerations_for_in_memory" name="preview_considerations_for_in_memory"></a>


## <a name="preview-considerations-for-in-memory-oltp"></a>Considérations sur Aperçu OLTP en mémoire


Les fonctionnalités OLTP en mémoire de base de données SQL Azure ne sont [active pour preview sur le 28 octobre 2015](https://azure.microsoft.com/updates/public-preview-in-memory-oltp-and-real-time-operational-analytics-for-azure-sql-database/).


Dans l’aperçu en cours, en mémoire OLTP est pris en charge uniquement pour :

- Bases de données qui se trouvent à un niveau de service *Premium* .

- Bases de données qui ont été créées après les fonctionnalités en mémoire OLTP ne sont actives.
 - Une nouvelle base de données ne reconnaît pas OLTP en mémoire si elle est restauré à partir d’une base de données qui a été créé avant que les fonctionnalités en mémoire OLTP ne s’actives.


En cas de doute, vous pouvez toujours exécuter le sélectionnez T-SQL suivant pour déterminer si votre base de données prend en charge en mémoire OLTP. Un résultat de **1** signifie que la base de données ne prend pas en charge en mémoire OLTP :

```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```


Si la requête renvoie la valeur **1**, OLTP en mémoire est pris en charge dans cette base de données et une copie de la base de données et restauration de base de données créée en fonction de cette base de données.


#### <a name="objects-allowed-only-at-premium"></a>Objets n'autorisées qu’à Premium


Si une base de données contient un des types d’objets en mémoire OLTP ou types suivants, la mise à niveau de service de la base de données à partir de Premium base ou Standard n’est pas pris en charge. Pour mettre à niveau la base de données, tout d’abord supprimer ces objets :

- Tables de mémoire optimisée
- Types de mémoire optimisée de table
- Modules compilés en mode natif


#### <a name="other-relationships"></a>D’autres relations


- Fonctionnalités à l’aide en mémoire OLTP les bases de données dans des pools élastiques n’est pas pris en charge pendant l’aperçu.
 - Pour déplacer une base de données a ou a eu des objets en mémoire OLTP à un pool élastique, procédez comme suit :
  - 1. Supprimer toute mémoire optimisée tables, types de tables et en mode natif compilés modules T-SQL dans la base de données
  - 2. Changer le niveau de service de la base de données en standard
  - 3. Déplacer la base de données vers le pool élastique

- À l’aide de OLTP en mémoire avec SQL Data Warehouse n’est pas pris en charge.
 - La fonctionnalité d’index columnstore d’Analytique en mémoire est pris en charge dans Data Warehouse SQL.

- Le magasin de requête ne capture pas les requêtes intérieur compilé en mode natif modules.

- Certaines fonctionnalités Transact-SQL ne sont pas prises en charge avec OLTP en mémoire. Cela s’applique à Microsoft SQL Server et base de données SQL Azure. Pour plus d’informations, voir :
 - [Prise en charge de Transact-SQL pour OLTP en mémoire](http://msdn.microsoft.com/library/dn133180.aspx)
 - [Constructions Transact-SQL non pris en charge par OLTP en mémoire](http://msdn.microsoft.com/library/dn246937.aspx)


## <a name="next-steps"></a>Étapes suivantes


- Essayez [utiliser en mémoire OLTP dans une Application SQL Azure existante.](sql-database-in-memory-oltp-migration.md)


## <a name="additional-resources"></a>Ressources supplémentaires

#### <a name="deeper-information"></a>Informations approfondies

- [En savoir plus sur OLTP en mémoire, qui s’applique à Microsoft SQL Server et base de données SQL Azure](http://msdn.microsoft.com/library/dn133186.aspx)

- [En savoir plus sur Analytique opérationnels en temps réel sur MSDN](http://msdn.microsoft.com/library/dn817827.aspx)

- Livre blanc sur [courantes la charge de travail des motifs et considérations relatives à la Migration](http://msdn.microsoft.com/library/dn673538.aspx), qui décrit les modèles de charge de travail où OLTP en mémoire fournit fréquemment des gains de performances significatifs.

#### <a name="application-design"></a>Conception de l’application

- [En mémoire OLTP (optimisation en mémoire)](http://msdn.microsoft.com/library/dn133186.aspx)

- [Utiliser en mémoire OLTP dans une Application SQL Azure existante.](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Outils

- [SQL Server Data Tools Preview (SSDT)](http://msdn.microsoft.com/library/mt204009.aspx), de la dernière version mensuelle.

- [Description des relecture Markup Language (par Intel) utilitaires pour SQL Server](http://support.microsoft.com/en-us/kb/944837)

- [Stockage en mémoire moniteur](sql-database-in-memory-oltp-monitoring.md) pour OLTP en mémoire.

