<properties
   pageTitle="Charger les données d’exemple dans SQL Data Warehouse | Microsoft Azure"
   description="Charger les exemples de données dans Data Warehouse SQL"
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
   ms.date="08/16/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

#<a name="load-sample-data-into-sql-data-warehouse"></a>Charger les exemples de données dans Data Warehouse SQL

Suivez ces étapes pour charger et interroger la base de données Adventure Works exemple simples. Ces scripts utilisent tout d’abord sqlcmd pour exécuter SQL qui créera des tables et des vues. Une fois que les tables ont été créées, les scripts va utiliser bcp pour charger les données.  Si vous n’avez pas sqlcmd et bcp installé, suivez ces liens pour [installer bcp][] et [installer sqlcmd][].

##<a name="load-sample-data"></a>Charger les exemples de données

1. Téléchargez le fichier zip [Adventure Works exemples de Scripts pour Data Warehouse SQL][] .

2. Extraire les fichiers zip téléchargés dans un répertoire sur votre ordinateur local.

3. Modifiez le fichier extrait aw_create.bat et définissez les variables suivantes en haut du fichier.  Veillez à ne laisser aucun espace entre le « = » et le paramètre.  Voici quelques exemples de quoi peuvent ressembler vos modifications.

    ```
    server=mylogicalserver.database.windows.net
    user=mydwuser
    password=Mydwpassw0rd
    database=mydwdatabase
    ```

4. À partir d’une invite de commande Windows, exécutez la aw_create.bat modifié.  Assurez-vous que vous êtes dans le répertoire où vous avez enregistré votre version modifiée du aw_create.bat.
Ce script sera...
    * Supprimer les Adventure Works tables ou vues qui existent déjà dans votre base de données
    * Créer des tables Adventure Works et des vues
    * Charger chaque table Adventure Works à l’aide de bcp
    * Valider le nombre de lignes pour chaque table Adventure Works
    * Collecter des statistiques sur toutes les colonnes pour chaque table Adventure Works


##<a name="query-sample-data"></a>Exemples de données de la requête

Une fois que vous avez chargé des exemples de données dans votre Data Warehouse SQL, vous pouvez exécuter rapidement plusieurs requêtes.  Pour exécuter une requête, vous connecter à votre base de données Adventure Works nouvellement créé dans DW de SQL Azure à l’aide de Visual Studio et SSDT, comme décrit dans le document [de la requête avec Visual Studio][] .

Exemple d’instruction select simple pour obtenir toutes les informations des employés :

```sql
SELECT * FROM DimEmployee;
```

Exemple d’une requête plus complexe en utilisant des constructions telles que GROUP BY pour examiner le montant total pour toutes les ventes sur chaque jour :

```sql
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

Exemple d’une instruction SELECT avec une clause WHERE pour filtrer les commandes d’avant une certaine date :

```
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
WHERE OrderDateKey > '20020801'
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

Data Warehouse SQL prend en charge presque toutes les constructions T-SQL qui prend en charge de SQL Server.  Toutes les différences sont décrites dans notre documentation [migrer le code][] .

## <a name="next-steps"></a>Étapes suivantes
À présent que vous avez eu l’occasion les exemples de données avec des requêtes, découvrez comment [développer][], [charger][]ou [migrer][] vers Data Warehouse SQL.

<!--Image references-->

<!--Article references-->
[migrer]: sql-data-warehouse-overview-migrate.md
[développer]: sql-data-warehouse-overview-develop.md
[charger]: sql-data-warehouse-overview-load.md
[requête avec Visual Studio]: sql-data-warehouse-query-visual-studio.md
[migrer du code]: sql-data-warehouse-migrate-code.md
[installer bcp]: sql-data-warehouse-load-with-bcp.md
[installer sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other Web references-->
[Adventure Works exemples de Scripts pour Data Warehouse SQL]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksSQLDW2012.zip
