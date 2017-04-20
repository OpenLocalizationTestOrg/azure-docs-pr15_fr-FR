<properties
   pageTitle="Boucles dans Data Warehouse SQL | Microsoft Azure"
   description="Conseils pour Transact-SQL boucles et curseurs de remplacement dans SQL Azure Data Warehouse pour développer des solutions."
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

# <a name="loops-in-sql-data-warehouse"></a>Boucles dans Data Warehouse SQL
Data Warehouse SQL prend en charge la boucle [tandis que][] pour l’exécution des blocs d’instructions à plusieurs reprises. Cela continuera pour dans la mesure où les conditions spécifiées sont remplies ou jusqu'à ce que le code spécifiquement termine la boucle à l’aide du `BREAK` mot clé. Boucles sont particulièrement utiles pour le remplacement de curseurs définis dans le code SQL. Peut être effectué en, presque toutes les curseurs qui sont écrits en code SQL de l’avance, lecture diverses uniquement. Par conséquent, [tandis que] boucles sont une alternative intéressante si vous vous trouvez avoir à remplacer une.

## <a name="leveraging-loops-and-replacing-cursors-in-sql-data-warehouse"></a>Exploiter les boucles et au remplacement de curseurs dans Data Warehouse SQL
Toutefois, avant de plonger tête tout d’abord vous devez vous poser la question suivante : « Pourrait ce curseur être modifié pour utiliser les opérations de jeu en ? ». Dans de nombreux cas, la réponse sera Oui et est souvent la meilleure approche. Une opération de jeu en souvent s’exécute plus rapidement à une approche itérative, ligne par ligne.

Avance rapide curseurs en lecture seule peuvent être facilement remplacées par une construction en boucle. Voici un exemple simple. Cet exemple de code met à jour les statistiques pour chaque table dans la base de données. En effectuant une itération sur les tables dans des échanges nous sont en mesure d’exécuter chaque commande dans l’ordre.

Tout d’abord, créez une table temporaire contenant un nombre de lignes uniques permettant d’identifier les instructions individuelles :

```
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

Ensuite, initialisation les variables nécessaires pour exécuter la boucle :

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Maintenant une boucle sur instructions exécuter une à la fois :

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Pour finir supprimer la table temporaire créée dans la première étape

```
DROP TABLE #tbl;
```


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir d’autres conseils de développement, voir [vue d’ensemble du développement][].

<!--Image references-->

<!--Article references-->
[vue d’ensemble de développement]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[CERTAIN]: https://msdn.microsoft.com/library/ms178642.aspx


<!--Other Web references-->
