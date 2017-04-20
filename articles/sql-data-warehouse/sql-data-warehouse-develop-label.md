<properties
   pageTitle="Utiliser des étiquettes aux requêtes instrument dans SQL Data Warehouse | Microsoft Azure"
   description="Conseils pour l’utilisation des étiquettes de requêtes instrument dans SQL Azure Data Warehouse pour développer des solutions."
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

# <a name="use-labels-to-instrument-queries-in-sql-data-warehouse"></a>Utiliser des étiquettes pour les requêtes instrument dans Data Warehouse SQL
Data Warehouse SQL prend en charge un concept appelé étiquettes de requête. Avant d’être placés dans n’importe quelle profondeur nous allons examiner un exemple :

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Cette dernière ligne balises la chaîne « Mes requête étiquetage » à la requête. Il s’agit particulièrement utile comme l’étiquette n’est pas requête via les DMV. Nous fournit un mécanisme pour effectuer le suivi de requêtes problème et également pour aider à identifier la progression dans une série de ETL.

Une bonne convention d’appellation permet vraiment ici. Par exemple quelque chose comme « projet : procédure : instruction : commentaire » peut aider à identifier de façon unique la requête avec tous les codes de contrôle de code source.

Pour effectuer une recherche par étiquette, vous pouvez utiliser la requête suivante qui utilise les vues de gestion dynamique :

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [AZURE.NOTE] Il est essentiel que vous renvoyer à la ligne des crochets ou des guillemets autour de l’étiquette word lors de la recherche. Étiquette est un mot réservé et se provoqué une erreur si elle n’a pas été délimité.


## <a name="next-steps"></a>Étapes suivantes
Pour obtenir d’autres conseils de développement, voir [vue d’ensemble du développement][].

<!--Image references-->

<!--Article references-->
[vue d’ensemble de développement]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
