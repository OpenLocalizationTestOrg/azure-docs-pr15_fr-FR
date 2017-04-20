<properties
   pageTitle="Charger les données à partir de SQL Server dans SQL Azure Data Warehouse (bcp) | Microsoft Azure"
   description="Pour une taille de données de petite taille, utilise bcp pour exporter des données à partir de SQL Server aux fichiers plats et importer les données directement dans le magasin de données SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/30/2016"
   ms.author="lodipalm;barbkess;sonyama"/>


# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-flat-files"></a>Charger les données à partir de SQL Server dans SQL Azure Data Warehouse (fichiers plats)

> [AZURE.SELECTOR]
- [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
- [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
- [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)

Pour les petits groupes de données, vous pouvez utiliser l’utilitaire de ligne de commande bcp pour exporter des données à partir de SQL Server, puis le charger directement au magasin de données SQL Azure.

Dans ce didacticiel, vous allez utiliser bcp pour :

- Exporter un tableau à partir de SQL Server à l’aide du bcp commande (ou créer un exemple simple de fichier)
- Importer la table à partir d’un fichier plat à SQL Data Warehouse.
- Créer des statistiques sur les données chargées.

>[AZURE.VIDEO loading-data-into-azure-sql-data-warehouse-with-bcp]

## <a name="before-you-begin"></a>Avant de commencer

### <a name="prerequisites"></a>Conditions préalables

Pour lancer ce didacticiel, vous devez :

- Une base de données SQL Data Warehouse
- L’utilitaire de ligne de commande bcp installé
- L’utilitaire de ligne de commande sqlcmd installé

Vous pouvez télécharger les utilitaires bcp et sqlcmd à partir du [Centre de téléchargement Microsoft][].

### <a name="data-in-ascii-or-utf-16-format"></a>Données au format ASCII ou UTF-16

Si vous essayez de ce didacticiel avec vos propres données, vos données doivent utiliser le ASCII ou UTF-16 codage bcp ne prenant pas en charge UTF-8. 

PolyBase prend en charge UTF-8 mais ne prend en charge UTF-16. Notez que si vous voulez combiner bcp avec PolyBase vous devrez transformer les données en UTF-8 après avoir est exporté à partir de SQL Server. 


## <a name="1-create-a-destination-table"></a>1. créer une table de destination

Définir une table SQL Data Warehouse qui sera la table de destination pour la charge. Les colonnes du tableau doivent correspondre aux données dans chaque ligne de votre fichier de données.

Pour créer une table, ouvrez une invite de commande et sqlcmd.exe permet d’exécuter la commande suivante :


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


## <a name="2-create-a-source-data-file"></a>2. créer un fichier de données source

Ouvrez le bloc-notes et copiez les lignes de données suivantes dans un nouveau fichier texte, puis enregistrez ce fichier à votre dossier temporaire local, C:\Temp\DimDate2.txt. Ces données sont au format ASCII.

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

(Facultatif) Pour exporter vos données à partir d’une base de données SQL Server, ouvrez une invite de commandes et exécutez la commande suivante. Remplacez TableName, nom du serveur, DatabaseName, nom d’utilisateur et mot de passe par vos propres informations.

```sql
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t ','
```



## <a name="3-load-the-data"></a>3. charger les données
Pour charger les données, ouvrez une invite de commande et exécutez la commande suivante en remplaçant les valeurs pour nom du serveur de base de données nom, nom d’utilisateur et mot de passe par vos propres informations.

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ','
```

Utilisez cette commande pour vérifier que les données a été chargées correctement

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Les résultats doivent ressembler à ceci :

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

## <a name="4-create-statistics"></a>4. créer des statistiques

SQL Data Warehouse ne prend pas encore prise en charge Création automatique ou statistiques mises à jour automatiques. Pour obtenir les meilleurs résultats de requête, il est important de créer des statistiques sur toutes les colonnes de toutes les tables après le premier chargement ou des modifications substantielles apparaissent dans les données. Pour une explication détaillée des statistiques, voir [statistiques][]. 

Exécutez la commande suivante pour créer des statistiques sur votre table nouvellement chargée.

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="5-export-data-from-sql-data-warehouse"></a>5. exporter les données de Data Warehouse SQL
Pour vous amuser, vous pouvez exporter les données que vous venez de charger revenir parti de Data Warehouse SQL.  La commande Exporter est exactement identique à l’exportation à partir de SQL Server.

Toutefois, il existe une différence dans les résultats. Dans la mesure où les données sont stockées dans des emplacements distribués dans SQL Data Warehouse, lorsque vous exportez des données chaque nœud de calcul écrit des données dans le fichier de copie. L’ordre des données dans le fichier de copie est susceptible d’être différent de l’ordre des données dans le fichier d’entrée.

### <a name="export-a-table-and-compare-exported-results"></a>Exporter un tableau et comparer les résultats exportés

Pour afficher les données exportées, ouvrez une invite de commandes et exécutez cette commande à l’aide de vos propres paramètres. Nom du serveur est le nom de votre serveur Azure logiques SQL Server.

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Vous pouvez vérifier que les données ont été correctement exportées en ouvrant le nouveau fichier. Les données dans le fichier doivent correspondre le texte ci-dessous, mais probablement sont triées dans un ordre différent :

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

### <a name="export-the-results-of-a-query"></a>Exporter les résultats d’une requête

Vous pouvez utiliser la fonction **queryout** de bcp pour exporter les résultats d’une requête à la place de l’exportation de l’intégralité du tableau. 

## <a name="next-steps"></a>Étapes suivantes
Pour une vue d’ensemble de chargement, voir [charger des données dans Data Warehouse SQL][].
Pour obtenir d’autres conseils de développement, voir [vue d’ensemble du développement Data Warehouse SQL][].
Pour plus d’informations sur la création d’une table sur Data Warehouse SQL, voir [Vue d’ensemble du tableau][] ou de [syntaxe CREATE TABLE][] .

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
