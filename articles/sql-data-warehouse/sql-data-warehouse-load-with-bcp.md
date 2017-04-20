<properties
   pageTitle="Utiliser bcp pour charger les données dans Data Warehouse SQL | Microsoft Azure"
   description="Découvrez quels bcp et comment l’utiliser pour data warehouse scénarios."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/10/2016"
   ms.author="mausher;barbkess;sonyama"/>


# <a name="load-data-with-bcp"></a>Charger les données avec bcp

> [AZURE.SELECTOR]
- [Redgate](sql-data-warehouse-load-with-redgate.md)  
- [Données par défaut](sql-data-warehouse-get-started-load-with-azure-data-factory.md)  
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)  
- [BCP](sql-data-warehouse-load-with-bcp.md)


**[bcp][]** est un utilitaire de chargement de ligne de commande en bloc vous permet de copier des données entre SQL Server, fichiers de données et Data Warehouse SQL. Utilisez bcp pour importer un grand nombre de lignes dans des tables SQL Data Warehouse ou pour exporter les données de tables SQL Server dans les fichiers de données. À l’exception lorsque utilisée avec l’option queryout, bcp requiert aucune connaissance de Transact-SQL.

bcp est un moyen rapide et facile de déplacer les jeux de données plus petits vers et depuis une base de données SQL Data Warehouse. Le nombre exact de données qu’il sont recommandés de charge/extrait via bcp dépendra sur votre connexion au centre de données Azure au réseau.  En règle générale, les tables de dimension peuvent être chargées et extraits facilement avec bcp, cependant, bcp n’est pas recommandé de chargement ou de l’extraction de grands volumes de données.  Polybase est l’outil recommandé de chargement et d’extraction de grands volumes de données comme nous le faisons un meilleur travail exploite l’architecture de traitement hautement parallèle de Data Warehouse SQL.

Avec bcp, vous pouvez :

- Utiliser un utilitaire de ligne de commande simple pour charger les données dans Data Warehouse SQL.
- Utiliser un utilitaire de ligne de commande simple pour extraire les données de Data Warehouse SQL.

Ce didacticiel vous montrent comment à :

- Importer des données dans une table à l’aide du bcp commande
- Exporter des données à partir d’un tableau, en utilisant le bcp commande Extraire

>[AZURE.VIDEO loading-data-into-azure-sql-data-warehouse-with-bcp]

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
