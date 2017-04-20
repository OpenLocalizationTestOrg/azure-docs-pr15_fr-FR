<properties
   pageTitle="Charger les données à partir d’un fichier CSV dans Azure SQL Databaase (bcp) | Microsoft Azure"
   description="Pour une taille de données de petite taille, utilise bcp pour importer des données dans une base de données SQL Azure."
   services="sql-database"
   documentationCenter="NA"
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/13/2016"
   ms.author="carlrab"/>


# <a name="load-data-from-csv-into-azure-sql-data-warehouse-flat-files"></a>Charger les données d’un CSV dans SQL Azure Data Warehouse (fichiers plats)

Vous pouvez utiliser l’utilitaire de ligne de commande bcp pour importer des données à partir d’un fichier CSV dans la base de données SQL Azure.

## <a name="before-you-begin"></a>Avant de commencer

### <a name="prerequisites"></a>Conditions préalables

Pour lancer ce didacticiel, vous devez :

- Un serveur logique de base de données SQL Azure et la base de données
- L’utilitaire de ligne de commande bcp installé
- L’utilitaire de ligne de commande sqlcmd installé

Vous pouvez télécharger les utilitaires bcp et sqlcmd à partir du [Centre de téléchargement Microsoft][].

### <a name="data-in-ascii-or-utf-16-format"></a>Données au format ASCII ou UTF-16

Si vous essayez de ce didacticiel avec vos propres données, vos données doivent utiliser le ASCII ou UTF-16 codage bcp ne prenant pas en charge UTF-8. 

## <a name="1-create-a-destination-table"></a>1. créer une table de destination

Définir une table de base de données SQL en tant que la table de destination. Les colonnes du tableau doivent correspondre aux données dans chaque ligne de votre fichier de données.

Pour créer une table, ouvrez une invite de commande et sqlcmd.exe permet d’exécuter la commande suivante :


```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    ;
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


## <a name="next-steps"></a>Étapes suivantes

Pour migrer une base de données SQL Server, voir [migration de base de données SQL Server](sql-database-cloud-migrate.md).

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Centre de téléchargement Microsoft]: https://www.microsoft.com/download/details.aspx?id=36433
