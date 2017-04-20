<properties
   pageTitle="Charger les données à partir de SQL Server dans SQL Azure Data Warehouse (PolyBase) | Microsoft Azure"
   description="Utilise bcp pour exporter des données à partir de SQL Server vers des fichiers plats, AZCopy pour importer des données à Azure blob storage et PolyBase pour ajouter les données dans Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ckarst"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/30/2016"
   ms.author="cakarst;barbkess;sonyama"/>


# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-azcopy"></a>Charger les données à partir de SQL Server dans SQL Azure Data Warehouse (AZCopy)

Utilisez bcp et utilitaires de ligne de commande AZCopy pour charger les données à partir de SQL Server à Azure blob storage. Ensuite utiliser PolyBase ou Azure Data Factory pour charger les données dans le magasin de données SQL Azure. 


## <a name="prerequisites"></a>Conditions préalables

Pour lancer ce didacticiel, vous devez :

- Une base de données SQL Data Warehouse
- L’utilitaire de ligne de commande bcp installé
- L’utilitaire de ligne de commande SQLCMD installé

>[AZURE.NOTE] Vous pouvez télécharger les utilitaires bcp et sqlcmd à partir du [Centre de téléchargement Microsoft][].

## <a name="import-data-into-sql-data-warehouse"></a>Importer des données dans Data Warehouse SQL

Dans ce didacticiel, vous créez une table dans SQL Azure Data Warehouse et importer des données dans la table.

### <a name="step-1-create-a-table-in-azure-sql-data-warehouse"></a>Étape 1 : Créer une table dans le magasin de données SQL Azure

À partir d’une invite de commandes, utilisez sqlcmd pour exécuter la requête suivante pour créer un tableau dans votre instance :

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    WITH
    (
        CLUSTERED COLUMNSTORE INDEX,
        DISTRIBUTION = ROUND_ROBIN
    );
"
```

>[AZURE.NOTE] Pour plus d’informations sur la création d’une table SQL Data Warehouse et les options disponibles dans la clause WITH, voir [Vue d’ensemble du tableau][] ou de [syntaxe CREATE TABLE][] .

### <a name="step-2-create-a-source-data-file"></a>Étape 2 : Créer un fichier de source de données

Ouvrez le bloc-notes et copiez les lignes de données suivantes dans un nouveau fichier texte, puis enregistrez ce fichier à votre dossier temporaire local, C:\Temp\DimDate2.txt.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

> [AZURE.NOTE] Il est important de se rappeler que ce bcp.exe ne prend pas en charge le codage UTF-8 du fichier. Utilisez des fichiers ASCII ou codée UTF-16 fichiers lors de l’utilisation de bcp.exe.

### <a name="step-3-connect-and-import-the-data"></a>Étape 3 : Se connecter et importer les données
À l’aide de bcp, vous pouvez vous connecter et importer les données à l’aide de la commande suivante en remplaçant les valeurs le cas échéant :

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

Vous pouvez vérifier que les données ont été chargées en exécutant la requête suivante à l’aide de sqlcmd :

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Cela doit renvoyer les résultats suivants :

DateId |CalendarQuarter |FiscalQuarter
----------- |--------------- |-------------
20150101 |1 |3
20150201 |1 |3
20150301 |1 |3
20150401 |2 |4
20150501 |2 |4
20150601 |2 |4
20150701 |3 |1
20150801 |3 |1
20150801 |3 |1
20151001 |4 |2
20151101 |4 |2
20151201 |4 |2

### <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>Étape 4 : Créer des statistiques sur vos données chargées

Azure SQL Data Warehouse ne prend pas encore prise en charge automatique créer ou mettre à jour les statistiques automatiquement chaque année. Afin d’obtenir les meilleures performances de vos requêtes, il est important que créer des statistiques sur toutes les colonnes de toutes les tables après le premier chargement ou des modifications substantielles apparaissent dans les données. Pour une explication détaillée des statistiques, consultez la rubrique [statistiques][] dans le groupe développer des rubriques. Voici un exemple rapide montrant comment créer des statistiques sur le tableau chargé dans cet exemple

Exécutez les instructions CREATE STATISTICS suivantes depuis une invite sqlcmd :

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="export-data-from-sql-data-warehouse"></a>Exporter des données de Data Warehouse SQL
Dans ce didacticiel, vous allez créer un fichier de données à partir d’une table SQL Data Warehouse. Nous va exporter les données que nous avons créé ci-dessus dans un nouveau fichier de données appelé DimDate2_export.txt.

### <a name="step-1-export-the-data"></a>Étape 1 : Exporter les données

À l’aide de l’utilitaire bcp, vous pouvez vous connecter et exporter des données à l’aide de la commande suivante en remplaçant les valeurs le cas échéant :

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Vous pouvez vérifier que les données ont été correctement exportées en ouvrant le nouveau fichier. Les données dans le fichier doivent correspondre le texte ci-dessous :

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

>[AZURE.NOTE] En raison de la nature des systèmes distribués, l’ordre des données peut ne pas être la même dans les bases de données SQL Data Warehouse. Une autre option consiste à utiliser la fonction **queryout** de bcp pour écrire un extrait de requête au lieu d’exporter l’intégralité du tableau.

## <a name="next-steps"></a>Étapes suivantes
Pour une vue d’ensemble de chargement, voir [charger des données dans Data Warehouse SQL][].
Pour obtenir d’autres conseils de développement, voir [vue d’ensemble du développement Data Warehouse SQL][].

<!--Image references-->

<!--Article references-->

[Charger les données dans Data Warehouse SQL]: ./sql-data-warehouse-overview-load.md
[Vue d’ensemble du développement Data Warehouse SQL]: ./sql-data-warehouse-overview-develop.md
[Vue d’ensemble du tableau]: ./sql-data-warehouse-tables-overview.md
[Statistiques]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[Syntaxe CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Centre de téléchargement Microsoft]: https://www.microsoft.com/download/details.aspx?id=36433
