<properties
   pageTitle="Distribution de tables SQL Data Warehouse | Microsoft Azure"
   description="Prise en main à la distribution de tables dans SQL Azure Data Warehouse."
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
   ms.date="08/30/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="distributing-tables-in-sql-data-warehouse"></a>Distribution de tables SQL Data Warehouse

> [AZURE.SELECTOR]
- [Vue d’ensemble][]
- [Types de données][]
- [Distribuer][]
- [Index][]
- [Partition][]
- [Statistiques][]
- [Temporaire][]

SQL Data Warehouse est qu'un traitement hautement parallèle (MPP) distribué système de base de données.  En divisant les données et capacités de traitement entre plusieurs nœuds, SQL Data Warehouse peuvent proposer d’extensibilité élevées considérable - au-delà de n’importe quel système unique.  Choix du mode de distribution de vos données dans votre Data Warehouse SQL est un des facteurs plus importants pour obtenir des performances optimales.   La touche pour optimiser les performances est de minimiser déplacement de données et à son tour pour réduire le déplacement de données consiste à sélectionner la stratégie de distribution approprié.

## <a name="understanding-data-movement"></a>Présentation de déplacement de données

Dans un système MPP, les données de chaque table sont réparties sur plusieurs bases de données sous-jacentes.  Les requêtes plus optimisées sur un système MPP peuvent simplement être passés par le biais d’exécuter sur les bases de données distribuées sans interaction entre autres bases de données.  Par exemple, supposons que vous avez une base de données avec des données de vente qui contient deux tables, ventes et clients.  Si vous disposez d’une requête qui doit joindre votre table sales à votre table clients et divisez vos ventes et les tables du client vers le haut par numéro de client, plaçant chaque client dans une base de données distinct, toutes les requêtes qui participer à la vente et le client peuvent être résolus au sein de chaque base de données sans connaissance des autres bases de données.  En revanche, si vous divisé vos données de vente par numéro de commande et de vos données client par numéro de client, puis une base de données donnée n’ont pas les données correspondantes pour chaque client et donc si vous souhaitez participer à vos données de vente à vos données client, vous devez obtenir les données pour chaque client à partir d’autres bases de données.  Dans cet exemple deuxième, déplacement de données devront se produire pour déplacer les données du client vers les données de ventes, afin que les deux tables peuvent être jointes.  

Déplacement de données n’est pas toujours une mauvaise chose, il est parfois nécessaire résoudre une requête.  Mais lorsque cette étape supplémentaire peut être évitée, naturellement votre requête s’exécute plus rapidement.  Déplacement de données survient généralement lorsque les tables sont jointes ou agrégations sont effectuées.  Fréquence à laquelle vous devez effectuer les deux, tandis que vous pourrez optimiser pour un scénario, comme une jointure, vous devez toujours déplacement de données pour vous aider à résoudre pour le scénario comme une agrégation.  Obtenir ce résultat est de savoir qui est moins de travail.  Dans la plupart des cas, distribuer des tables de faits importantes sur une colonne fréquemment jointe est la méthode la plus efficace pour réduire le déplacement des données la plupart des.  Distribution des données sur les colonnes de la jointure est une méthode beaucoup plus courante pour réduire le déplacement de données de distribution des données sur les colonnes impliquées dans une agrégation.

## <a name="select-distribution-method"></a>Sélectionnez la méthode de distribution

Les coulisses, SQL Data Warehouse divise vos données en 60 bases de données.  Chaque base de données individuel est appelé une **distribution**.  Quand des données sont chargées dans chaque table, SQL Data Warehouse a savoir comment diviser vos données entre ces 60 répartitions.  

La méthode de distribution est définie au niveau de la table et actuellement, il existe deux possibilités :

1. **Alternées** qui distribuer les données uniformément mais aléatoirement.
2. **Hachage distribué** qui distribue des données en fonction des valeurs dans une seule colonne de hachage

Par défaut, lorsque vous ne définissez pas d’une méthode de distribution de données, votre tableau sera distribué à l’aide de la méthode de distribution **alternées** .  Toutefois, lorsque vous serez plus sophistiqués dans votre implémentation, vous souhaiterez à prendre en compte à l’aide de tables **hachage distribué** pour réduire le déplacement de données qui peut à son tour optimiser les performances de requête.

### <a name="round-robin-tables"></a>Tables alternées

En utilisant la méthode alternées de distribution des données est très quoi il sons.  Lors du chargement de vos données, chaque ligne est envoyée simplement à la distribution suivante.  Cette méthode de distribution des données est toujours aléatoirement uniformiser les données très sur l’ensemble de la distribution.  Qu’il n’existe aucun tri terminé au cours du processus alternées qui place vos données.  Une distribution alternées est parfois appelée hachage aléatoire pour cette raison.  Avec une table distribuée cyclique il n’est pas nécessaire de comprendre les données.  Pour cette raison, tables cyclique s’avérer chargement bonnes cibles.

Par défaut, si aucune méthode de distribution n’est accepté, la méthode de distribution alternées servira.  Toutefois, tandis que les tables alternées sont faciles à utiliser, car les données sont réparties au hasard dans le système, que cela signifie que le système ne peut pas garantir la distribution chaque ligne est.  Par conséquent, le système doit parfois appeler une opération de déplacement de données pour mieux organiser vos données avant qu’il peut résoudre une requête.  Cette étape supplémentaire peut ralentir vos requêtes.

Vous pouvez utiliser alternées distribution de votre table dans les scénarios suivants :

- Lors de la mise en route comme point de départ simple
- Si aucune clé de jointure évident
- Si n’est pas colonne bon candidat pour hachage distribution de la table
- Si la table ne partage pas une clé de jointure courantes avec d’autres tables
- Si la jointure est moins importante que les autres jointures dans la requête
- Lorsque la table est une table intermédiaire temporaire

Ces deux exemples crée une Table alternées :

```SQL
-- Round Robin created by default
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
;

-- Explicitly Created Round Robin Table
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
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
,   DISTRIBUTION = ROUND_ROBIN
)
;
```

> [AZURE.NOTE] Tandis qu’alternées est le type de tableau par défaut soient explicites dans votre DDL est considéré comme une meilleure pratique afin que les intentions de disposition de votre tableau sont désactivées à d’autres personnes.

### <a name="hash-distributed-tables"></a>Hachage distribués Tables

À l’aide d’un algorithme de **hachage distribué** pour distribuer vos tables peut améliorer les performances dans de nombreux scénarios en réduisant le déplacement de données au moment de la requête.  Tables de hachage distribué sont des tables qui sont divisées entre les bases de données distribués à l’aide d’un algorithme de hachage sur une seule colonne que vous sélectionnez.  La colonne distribution est ce qui détermine la façon dont les données sont réparties sur vos bases de données distribuées.  La fonction de hachage utilise la colonne de distribution pour attribuer des lignes à la distribution.  L’algorithme de hachage et de distribution qui en résulte est déterministe.  Qui est la même valeur avec le même type de données est toujours la même distribution.    

Cet exemple crée une table distribuée sur id :

```SQL
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
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
,  DISTRIBUTION = HASH([ProductKey])
)
;
```

## <a name="select-distribution-column"></a>Sélectionnez la colonne de distribution

Lorsque vous choisissez de **hachage distribuer** une table, vous devrez sélectionner une colonne de distribution unique.  Lorsque vous sélectionnez une colonne de distribution, il existe trois principaux facteurs à prendre en compte.  

Sélectionnez une colonne unique qui sera :

1. Ne pas être mis à jour
2. Uniformiser la largeur des données, éviter les données coefficient.ASYMETRIE
3. Réduire le déplacement de données

### <a name="select-distribution-column-which-will-not-be-updated"></a>Sélectionnez la colonne de distribution qui n’est pas mise à jour

Colonnes de distribution ne sont pas modifiables, par conséquent, sélectionnez une colonne avec des valeurs statiques.  Si une colonne doivent être mis à jour, il n’est généralement pas un candidat bonne distribution.  S’il existe un cas où vous devez mettre à jour une colonne de distribution, il est possible par tout d’abord supprimer la ligne, puis en insérant une nouvelle ligne.

### <a name="select-distribution-column-which-will-distribute-data-evenly"></a>Sélectionnez la colonne de distribution qui sera Uniformiser la largeur des données

Dans la mesure où un système distribué effectue uniquement aussi rapidement que sa répartition plus lente, il est important de répartir uniformément le travail sur les répartitions afin de parvenir à exécution équilibrée dans le système.  Image de la que division le travail sur un système distribué est basé sur l’endroit où résident les données de chaque distribution.  Il est donc très important sélectionner la colonne de distribution approprié pour la distribution des données pour permettre à chaque distribution a le même travail et prendra la fois pour terminer la partie du travail.  Lorsque le travail est divisé également dans le système, les données sont équilibrées par les répartitions.  Lorsque les données ne sont pas équilibrées, nous appelons cette **incliner de données**.  

Pour répartir uniformément des données et éviter d’inclinaison de données, considérez les éléments suivants lorsque vous sélectionnez votre colonne de distribution :

1. Sélectionnez une colonne qui contient un nombre important de valeurs distinctes.
2. Évitez de distribution des données sur les colonnes avec quelques valeurs distinctes. 
3. Évitez de distribution des données sur les colonnes présentant une fréquence élevée de valeurs NULL.
4. Évitez de distribution des données sur les colonnes de date.

Dans la mesure où chaque valeur est haché 1 de 60 répartitions, pour obtenir la répartition égale vous souhaiterez sélectionner une colonne qui est très unique et contient plus de 60 valeurs uniques.  Pour illustrer, pensez à un cas où une colonne comporte seulement 40 valeurs uniques.  Si cette colonne a été sélectionnée comme la clé de distribution, les données de la table seraient placer sur 40 répartitions au maximum, en laissant 20 répartitions avec aucune donnée et aucun traitement faire.  En revanche, les autres 40 répartitions aurait plus de travail que faire si les données a été réparties de plus de 60 répartitions.  Ce scénario est un exemple de données coefficient.ASYMETRIE.

Dans le système MPP, chaque étape de requête attend de toutes les répartitions terminer leur part du travail.  Si une distribution qui fait plus de travail que les autres colonnes, puis la ressource des autres répartitions sont essentiellement perdue qui attend sur la distribution occupé (e).  Lorsque le travail n’est pas uniformément réparti sur toutes les répartitions, nous appelons cette **inclinaison de traitement**.  Traitement inclinaison entraînera requêtes à exécuter plus lentement que si la charge de travail peut être répartie de manière égale sur les répartitions.  Données coefficient.ASYMETRIE, vous aboutissez à traitement inclinaison.

Éviter de distribuer sur colonne hautement nullable comme les valeurs null est alors placé sur la même répartition. Distribution sur une colonne de date peut également entraîner traitement inclinaison, car toutes les données pour une date donnée est alors placé sur la même distribution. Si plusieurs utilisateurs sont en cours d’exécution des requêtes tout le filtrage à la même date, puis 1 uniquement sur les 60 répartitions effectueront tout le travail depuis la date indiquée sera uniquement sur une distribution. Dans ce scénario, les requêtes seront exécutera probablement 60 fois plus lentes que si les données ont été réparties également sur l’ensemble de la distribution. 

Lorsqu’il n’existe aucune colonne bon candidat, envisagez alternées en tant que la méthode de distribution.

### <a name="select-distribution-column-which-will-minimize-data-movement"></a>Sélectionnez la colonne de distribution qui permet de minimiser le déplacement de données

Réduction de déplacement de données en sélectionnant la colonne de droite distribution est une des stratégies les plus importantes pour l’optimisation des performances de votre Data Warehouse SQL.  Déplacement de données survient généralement lorsque les tables sont jointes ou agrégations sont effectuées.  Colonnes utilisées dans `JOIN`, `GROUP BY`, `DISTINCT`, `OVER` et `HAVING` clauses tous passer pour **bon** hachage candidats de distribution. 

En revanche, colonnes dans les `WHERE` clause **pas** pour les candidats de colonne hachage adéquate, car ils limitent les globales pour participent à la requête, à l’origine de traitement incliner.  Un bon exemple d’une colonne qui peut être tentées par les solutions à distribuer dans, mais souvent peut provoquer ce traitement inclinaison est une colonne de dates.

En règle générale, si vous avez deux tables de faits importantes souvent impliquées dans une jointure, vous allez acquérir les performances de la plupart par distribuer les deux tables sur une des colonnes de la jointure.  Si vous avez une table qui n’est jamais joint à une autre table de faits volumineuse, puis regardez aux colonnes qui sont fréquemment dans le `GROUP BY` clause.

Il existe plusieurs critères clés qui doivent être remplies pour éviter de déplacement de données lors d’une jointure :

1. Les tables impliquées dans la jointure doivent être hachage distribué sur **une** des colonnes participant à la jointure.
2. Les types de données des colonnes de jointure doivent correspondre entre les deux tables.
3. Les colonnes doivent être jointes avec un opérateur est égal à.
4. Le type de jointure peut ne pas être une `CROSS JOIN`.


## <a name="troubleshooting-data-skew"></a>Résolution des problèmes de données coefficient.ASYMETRIE

Lorsque les données de la table sont distribuées à l’aide de la méthode de distribution hachage, il est possible que certains répartitions seront erronées pour les données sans respecter les proportions plus que d’autres. Inclinaison trop de données peut affecter les performances de requête, car le résultat final d’une requête distribuée doit attendre la distribution plus longue en cours d’exécution terminer. Selon le degré de l’inclinaison de données, vous devrez peut-être corriger le problème.

### <a name="identifying-skew"></a>Identification inclinaison

Un moyen simple à déterminer si une table incliné consiste à utiliser `DBCC PDW_SHOWSPACEUSED`.  Il s’agit d’un moyen très simple et rapide pour afficher le nombre de lignes de la table qui sont stockées dans chacune des 60 répartitions de votre base de données.  N’oubliez pas que pour les performances plus équilibrée, les lignes de votre table distribué doivent être répartis à égalité sur toutes les répartitions.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Toutefois, si vous interrogez les vues de gestion dynamique Azure SQL Data Warehouse (vue) vous pouvez effectuer une analyse plus détaillée.  Pour commencer, créez l’affichage [dbo.vTableSizes][] affichage à l’aide de l’instruction SQL à partir de l’article[vue d’ensemble] de [Vue d’ensemble du tableau].  Une fois que la vue est créée, exécutez cette requête pour identifier les tables d’avoir plus de 10 % données inclinaison.

```sql
select *
from dbo.vTableSizes 
where two_part_name in 
    (
    select two_part_name
    from dbo.vTableSizes
    where row_count > 0
    group by two_part_name
    having min(row_count * 1.000)/max(row_count * 1.000) > .10
    )
order by two_part_name, row_count
;
```

### <a name="resolving-data-skew"></a>Résolution des données coefficient.ASYMETRIE

Pas tous les coefficient.ASYMETRIE est suffisamment pour justifier un correctif.  Dans certains cas, les performances d’un tableau dans des requêtes peuvent annuler les dommages de données coefficient.ASYMETRIE.  Pour déterminer si vous devez résoudre les données dans une table, vous devez comprendre autant que possible sur les volumes de données et les requêtes dans votre charge de travail.   Une des façons d’examiner l’impact d’inclinaison consiste à utiliser les étapes décrites dans l’article de la [Requête analyse][] pour surveiller l’impact d’inclinaison sur les performances de requête et spécifiquement l’impact sur les requêtes combien prendre sur les répartitions individuelles.

Distribution des données est une question de trouver le bon équilibre entre la réduction des données inclinaison et minimisation des mouvements de données. Il peuvent être opposées objectifs et parfois vous souhaitez conserver les données coefficient.ASYMETRIE afin de réduire le déplacement de données. Par exemple, lorsque la colonne distribution est souvent à la colonne partagée de jointures et agrégations, vous sera réduction déplacement de données. L’avantage de disposer le déplacement des données minimales peut-être l’emporter sur l’impact de son données incliner. 

La façon de résoudre inclinaison de données typique consiste à recréer la table avec une colonne de distribution différente. Dans la mesure où il est inutile de modifier la colonne de distribution dans une table existante, la façon de modifier la distribution d’un tableau pour recréer avec un [[SACT]].  Voici deux exemples de la façon de résoudre les données inclinaison :

### <a name="example-1-re-create-the-table-with-a-new-distribution-column"></a>Exemple 1 : Recréez la table avec une nouvelle colonne de distribution

Cet exemple utilise [] [SACT] pour recréer une table avec une colonne de distribution hachage différent. 

```sql
CREATE TABLE [dbo].[FactInternetSales_CustomerKey] 
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  HASH([CustomerKey])
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_CustomerKey')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_CustomerKey] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_CustomerKey] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_CustomerKey] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_CustomerKey] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_CustomerKey] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_CustomerKey] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_CustomerKey] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_CustomerKey] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

### <a name="example-2-re-create-the-table-using-round-robin-distribution"></a>Exemple 2 : Recréez le tableau à l’aide de la distribution alternées

Cet exemple utilise [] [SACT] pour recréer une table avec alternées au lieu d’une distribution hachage. Cette modification produira même répartition des données au détriment de déplacement de données accrue. 

```sql
CREATE TABLE [dbo].[FactInternetSales_ROUND_ROBIN] 
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  ROUND_ROBIN
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_ROUND_ROBIN')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_ROUND_ROBIN] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_ROUND_ROBIN] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_ROUND_ROBIN] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_ROUND_ROBIN] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_ROUND_ROBIN] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_ROUND_ROBIN] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_ROUND_ROBIN] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_ROUND_ROBIN] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_HASH];
RENAME OBJECT [dbo].[FactInternetSales_ROUND_ROBIN] TO [FactInternetSales];
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la création de table, voir les [distribuer][], [Index][], [Partition][], [Types de données][], [statistiques][] et articles[temporaire] de [Tables temporaires].

Pour une vue d’ensemble des recommandations, voir [Recommandations entrepôt de données SQL][].


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
[Analyse de la requête]: ./sql-data-warehouse-manage-monitor.md
[dbo.vTableSizes]: ./sql-data-warehouse-tables-overview.md#querying-table-sizes

<!--MSDN references-->
[DBCC PDW_SHOWSPACEUSED()]: https://msdn.microsoft.com/library/mt204028.aspx

<!--Other Web references-->
