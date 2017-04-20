<properties
   pageTitle="Charger les données depuis le stockage blob Azure dans SQL Data Warehouse (PolyBase) | Microsoft Azure"
   description="Découvrez comment utiliser PolyBase pour charger les données depuis le stockage blob Azure dans Data Warehouse SQL. Charger plusieurs tables à partir des données publiques dans le schéma de Contoso au détail Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ckarst"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/25/2016"
   ms.author="cakarst;barbkess;sonyama"/>


# <a name="load-data-from-azure-blob-storage-into-sql-data-warehouse-polybase"></a>Charger les données depuis le stockage blob Azure dans SQL Data Warehouse (PolyBase)

> [AZURE.SELECTOR]
- [Données par défaut](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md)
- [PolyBase](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

Utilisez les commandes PolyBase et T-SQL pour charger les données depuis le stockage blob Azure dans SQL Azure Data Warehouse. 

Pour qu’elle soit simple, ce didacticiel charge deux tables à partir d’un objet public Azure stockage Blob dans le schéma de Contoso au détail Data Warehouse. Pour charger l’ensemble de données, exécutez l’exemple [charger le complète Data Warehouse Contoso vente au détail][] à partir du référentiel exemples Microsoft SQL Server.

Dans ce didacticiel, vous allez :

1. Configurer PolyBase charger depuis le stockage blob Azure
2. Charger des données publiques dans votre base de données
3. Effectuer des optimisations lorsque le chargement est terminé.


## <a name="before-you-begin"></a>Avant de commencer
Pour exécuter ce didacticiel, vous devez disposer d’un compte Azure comporte déjà une base de données SQL Data Warehouse. Si vous n’avez pas cela, voir [créer un Data Warehouse SQL][].

## <a name="1-configure-the-data-source"></a>1. configurer la source de données

PolyBase utilise les objets externes T-SQL pour définir l’emplacement et les attributs des données externes. Les définitions d’objets externes sont stockées dans Data Warehouse SQL. Les données sont stockées avec l’extérieur.

### <a name="11-create-a-credential"></a>1.1. Créer des informations d’identification

**Ignorer cette étape** si vous chargez les données publiques de Contoso. Vous n’avez pas besoin un accès sécurisé aux données publics car il est déjà accessible à quiconque.

**Ne pas ignorer cette étape** si vous utilisez ce didacticiel en tant que modèle pour le chargement de vos propres données. Pour accéder aux données via une information d’identification, utilisez le script suivant pour créer des informations d’identification à portée de base de données et utilisez-le lors de la définition de l’emplacement de la source de données.


```sql
-- A: Create a master key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.


CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;


-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure blob storage.
-- LOCATION: Provide Azure storage account name and blob container name.
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'wasbs://<blob_container_name>@<azure_storage_account_name>.blob.core.windows.net',
    CREDENTIAL = AzureStorageCredential
);
```

Passez à l’étape 2.

### <a name="12-create-the-external-data-source"></a>1.2. Créer la source de données externes

Utilisez cette commande [Créer une SOURCE de données externe][] pour stocker l’emplacement des données et le type de données. 

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH 
(  
    TYPE = Hadoop 
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
); 
```

> [AZURE.IMPORTANT] Si vous choisissez de publier votre blob azure conteneurs de stockage, n’oubliez pas que le propriétaire de données vous êtes facturée pour les données de frais de sortie lorsque des données quittent le centre de données. 

## <a name="2-configure-data-format"></a>2. configurer le format des données

Les données sont stockées dans des fichiers texte dans le stockage blob Azure et chaque champ est séparé par un délimiteur. Exécutez cette commande [Créer un FORMAT de fichier externe][] pour spécifier le format des données dans les fichiers texte. Les données de Contoso sont compressées et barre verticale délimité.

```sql
CREATE EXTERNAL FILE FORMAT TextFileFormat 
WITH 
(   FORMAT_TYPE = DELIMITEDTEXT
,   FORMAT_OPTIONS  (   FIELD_TERMINATOR = '|'
                    ,   STRING_DELIMITER = ''
                    ,   DATE_FORMAT      = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,   USE_TYPE_DEFAULT = FALSE 
                    )
);
``` 

## <a name="3-create-the-external-tables"></a>3. créer les tables externes

Maintenant que vous avez spécifié le format source et fichier de données, vous êtes prêt à créer les tables externes. 

### <a name="31-create-a-schema-for-the-data"></a>3.1. Créez un schéma pour les données. 

Pour créer un emplacement pour stocker les données de Contoso dans votre base de données, créez un schéma.

```sql
CREATE SCHEMA [asb]
GO
```

### <a name="32-create-the-external-tables"></a>3.2. Créer les tables externes. 

Exécuter ce script pour créer les tables externes DimProduct et FactOnlineSales. Nous essayons ici est définition des noms de colonnes et des types de données et leur liaison à l’emplacement et le format des fichiers de stockage blob Azure. La définition est stockée dans SQL Data Warehouse et les données sont toujours au stockage Blob Azure.

Le paramètre **emplacement** est le dossier dans le dossier racine de stockage Blob Azure. Chaque table est dans un autre dossier.


```sql

--DimProduct
CREATE EXTERNAL TABLE [asb].DimProduct (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL,
    [ProductDescription] [nvarchar](400) NULL,
    [ProductSubcategoryKey] [int] NULL,
    [Manufacturer] [nvarchar](50) NULL,
    [BrandName] [nvarchar](50) NULL,
    [ClassID] [nvarchar](10) NULL,
    [ClassName] [nvarchar](20) NULL,
    [StyleID] [nvarchar](10) NULL,
    [StyleName] [nvarchar](20) NULL,
    [ColorID] [nvarchar](10) NULL,
    [ColorName] [nvarchar](20) NOT NULL,
    [Size] [nvarchar](50) NULL,
    [SizeRange] [nvarchar](50) NULL,
    [SizeUnitMeasureID] [nvarchar](20) NULL,
    [Weight] [float] NULL,
    [WeightUnitMeasureID] [nvarchar](20) NULL,
    [UnitOfMeasureID] [nvarchar](10) NULL,
    [UnitOfMeasureName] [nvarchar](40) NULL,
    [StockTypeID] [nvarchar](10) NULL,
    [StockTypeName] [nvarchar](40) NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [AvailableForSaleDate] [datetime] NULL,
    [StopSaleDate] [datetime] NULL,
    [Status] [nvarchar](7) NULL,
    [ImageURL] [nvarchar](150) NULL,
    [ProductURL] [nvarchar](150) NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/DimProduct/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;
 
--FactOnlineSales
CREATE EXTERNAL TABLE [asb].FactOnlineSales 
(
    [OnlineSalesKey] [int]  NOT NULL,
    [DateKey] [datetime] NOT NULL,
    [StoreKey] [int] NOT NULL,
    [ProductKey] [int] NOT NULL,
    [PromotionKey] [int] NOT NULL,
    [CurrencyKey] [int] NOT NULL,
    [CustomerKey] [int] NOT NULL,
    [SalesOrderNumber] [nvarchar](20) NOT NULL,
    [SalesOrderLineNumber] [int] NULL,
    [SalesQuantity] [int] NOT NULL,
    [SalesAmount] [money] NOT NULL,
    [ReturnQuantity] [int] NOT NULL,
    [ReturnAmount] [money] NULL,
    [DiscountQuantity] [int] NULL,
    [DiscountAmount] [money] NULL,
    [TotalCost] [money] NOT NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/FactOnlineSales/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;
```

## <a name="4-load-the-data"></a>4. charger les données
Il existe différentes façons d’accéder aux données externes.  Vous pouvez interroger des données directement à partir de la table externe, charger les données dans des tables de base de données ou ajouter des données externes à des tables de base de données existantes.  


### <a name="41-create-a-new-schema"></a>4.1. Créer un schéma

SACT crée une nouvelle table qui contient des données.  Tout d’abord, créez un schéma pour les données de contoso.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="42-load-the-data-into-new-tables"></a>4.2. Charger les données dans des tables

Pour charger les données depuis le stockage blob Azure et enregistrez-le dans un tableau à l’intérieur de votre base de données, utilisez l’instruction [CREATE TABLE AS SELECT (Transact-SQL)][] . Chargement avec SACT s’appuie sur les tables externes fortement typées que vous venez de créer. Pour charger les données dans des tables, utilisez une instruction [SACT][] par table. 

SACT crée une nouvelle table et remplit avec les résultats d’une instruction select. SACT définit la nouvelle table pour que les mêmes colonnes et les types de données en tant que les résultats de l’instruction select. Si vous sélectionnez toutes les colonnes d’une table externe, la nouvelle table sera une copie des colonnes et des types de données dans la table externe.

Dans cet exemple, nous créons la dimension et la table de faits en tant que hachage distribués tables. 


```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="43-track-the-load-progress"></a>4.3 suivre la progression du chargement

Vous pouvez suivre la progression de votre charge à l’aide des vues de gestion dynamiques (DMV). 

```sql
-- To see all requests
SELECT * FROM sys.dm_pdw_exec_requests;

-- To see a particular request identified by its label
SELECT * FROM sys.dm_pdw_exec_requests as r;
WHERE r.[label] = 'CTAS : Load [cso].[DimProduct]             '
      OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
;

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files, 
    sum(s.bytes_processed)/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
WHERE 
    r.[label] = 'CTAS : Load [cso].[DimProduct]             '
    OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="5-optimize-columnstore-compression"></a>5. optimiser la compression columnstore

Par défaut, SQL Data Warehouse stocke la table sous forme d’un index columnstore groupé. Une fois une charge terminée, certaines des lignes de données peuvent être pas compressé dans le columnstore.  Il existe de nombreuses raisons pourquoi cela peut se produire. Pour plus d’informations, voir [Gérer les index columnstore][].

Pour optimiser les performances des requêtes et la compression columnstore après une charge, reconstruisez la table pour forcer l’index columnstore pour compresser toutes les lignes. 

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Pour plus d’informations sur la gestion d’index columnstore, voir l’article [gérer des index columnstore][] .

## <a name="6-optimize-statistics"></a>6. optimiser les statistiques

Il est conseillé de créer une seule colonne statistiques immédiatement après un chargement. Il existe certains choix pour les statistiques. Par exemple, si vous créez une seule colonne statistiques sur toutes les colonnes, il peut prendre beaucoup de temps pour reconstruire toutes les statistiques. Si vous connaissez que certaines colonnes ne passent pas d’être présent prédicats de la requête, vous pouvez ignorer les statistiques de création sur les colonnes.

Si vous décidez de créer des statistiques d’une seule colonne sur chaque colonne de chaque table, vous pouvez utiliser l’exemple de code procédure stockée `prc_sqldw_create_stats` dans l’article [statistiques][] .

L’exemple suivant est un bon point de départ pour la création de statistiques. Il crée des statistiques d’une seule colonne sur chaque colonne dans la table de dimension et chaque colonne de jointure dans la table de faits. Vous pouvez toujours ajouter ultérieurement statistiques unique ou plusieurs colonnes à d’autres colonnes de table de faits.


```sql
CREATE STATISTICS [stat_cso_DimProduct_AvailableForSaleDate] ON [cso].[DimProduct]([AvailableForSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_BrandName] ON [cso].[DimProduct]([BrandName]);
CREATE STATISTICS [stat_cso_DimProduct_ClassID] ON [cso].[DimProduct]([ClassID]);
CREATE STATISTICS [stat_cso_DimProduct_ClassName] ON [cso].[DimProduct]([ClassName]);
CREATE STATISTICS [stat_cso_DimProduct_ColorID] ON [cso].[DimProduct]([ColorID]);
CREATE STATISTICS [stat_cso_DimProduct_ColorName] ON [cso].[DimProduct]([ColorName]);
CREATE STATISTICS [stat_cso_DimProduct_ETLLoadID] ON [cso].[DimProduct]([ETLLoadID]);
CREATE STATISTICS [stat_cso_DimProduct_ImageURL] ON [cso].[DimProduct]([ImageURL]);
CREATE STATISTICS [stat_cso_DimProduct_LoadDate] ON [cso].[DimProduct]([LoadDate]);
CREATE STATISTICS [stat_cso_DimProduct_Manufacturer] ON [cso].[DimProduct]([Manufacturer]);
CREATE STATISTICS [stat_cso_DimProduct_ProductDescription] ON [cso].[DimProduct]([ProductDescription]);
CREATE STATISTICS [stat_cso_DimProduct_ProductKey] ON [cso].[DimProduct]([ProductKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductLabel] ON [cso].[DimProduct]([ProductLabel]);
CREATE STATISTICS [stat_cso_DimProduct_ProductName] ON [cso].[DimProduct]([ProductName]);
CREATE STATISTICS [stat_cso_DimProduct_ProductSubcategoryKey] ON [cso].[DimProduct]([ProductSubcategoryKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductURL] ON [cso].[DimProduct]([ProductURL]);
CREATE STATISTICS [stat_cso_DimProduct_Size] ON [cso].[DimProduct]([Size]);
CREATE STATISTICS [stat_cso_DimProduct_SizeRange] ON [cso].[DimProduct]([SizeRange]);
CREATE STATISTICS [stat_cso_DimProduct_SizeUnitMeasureID] ON [cso].[DimProduct]([SizeUnitMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_Status] ON [cso].[DimProduct]([Status]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeID] ON [cso].[DimProduct]([StockTypeID]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeName] ON [cso].[DimProduct]([StockTypeName]);
CREATE STATISTICS [stat_cso_DimProduct_StopSaleDate] ON [cso].[DimProduct]([StopSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_StyleID] ON [cso].[DimProduct]([StyleID]);
CREATE STATISTICS [stat_cso_DimProduct_StyleName] ON [cso].[DimProduct]([StyleName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitCost] ON [cso].[DimProduct]([UnitCost]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureID] ON [cso].[DimProduct]([UnitOfMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureName] ON [cso].[DimProduct]([UnitOfMeasureName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitPrice] ON [cso].[DimProduct]([UnitPrice]);
CREATE STATISTICS [stat_cso_DimProduct_UpdateDate] ON [cso].[DimProduct]([UpdateDate]);
CREATE STATISTICS [stat_cso_DimProduct_Weight] ON [cso].[DimProduct]([Weight]);
CREATE STATISTICS [stat_cso_DimProduct_WeightUnitMeasureID] ON [cso].[DimProduct]([WeightUnitMeasureID]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CurrencyKey] ON [cso].[FactOnlineSales]([CurrencyKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CustomerKey] ON [cso].[FactOnlineSales]([CustomerKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_DateKey] ON [cso].[FactOnlineSales]([DateKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_OnlineSalesKey] ON [cso].[FactOnlineSales]([OnlineSalesKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_ProductKey] ON [cso].[FactOnlineSales]([ProductKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_PromotionKey] ON [cso].[FactOnlineSales]([PromotionKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_StoreKey] ON [cso].[FactOnlineSales]([StoreKey]);
```

## <a name="achievement-unlocked"></a>Réussite déverrouillée !

Vous avez correctement chargé des données publiques dans Azure SQL Data Warehouse. Bon travail !

Vous pouvez maintenant démarrer interroger les tables à l’aide de requêtes tels que les suivants :

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>Étapes suivantes
Pour charger les données de Contoso au détail Data Warehouse complets, utilisez le script dans conseils supplémentaires sur le développement, voir [vue d’ensemble du développement Data Warehouse SQL][].

<!--Image references-->

<!--Article references-->
[Créer un Data Warehouse SQL]: sql-data-warehouse-get-started-provision.md
[Load data into SQL Data Warehouse]: sql-data-warehouse-overview-load.md
[Vue d’ensemble du développement Data Warehouse SQL]: sql-data-warehouse-overview-develop.md
[gérer les index columnstore]: sql-data-warehouse-tables-index.md
[Statistiques]: sql-data-warehouse-tables-statistics.md
[SACT]: sql-data-warehouse-develop-ctas.md
[label]: sql-data-warehouse-develop-label.md

<!--MSDN references-->
[CRÉER LA SOURCE DE DONNÉES EXTERNE]: https://msdn.microsoft.com/en-us/library/dn935022.aspx
[CRÉER LE FORMAT DE FICHIER EXTERNE]: https://msdn.microsoft.com/en-us/library/dn935026.aspx
[CRÉER la TABLE en tant que SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[REBUILD]: https://msdn.microsoft.com/library/ms188388.aspx

<!--Other Web references-->
[Microsoft Download Center]: http://www.microsoft.com/download/details.aspx?id=36433
[Charger le complète Data Warehouse Contoso vente au détail]: https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md
