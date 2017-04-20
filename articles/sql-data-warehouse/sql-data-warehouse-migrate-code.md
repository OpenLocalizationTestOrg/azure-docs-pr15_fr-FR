<properties
   pageTitle="Migrer votre code SQL pour SQL Data Warehouse | Microsoft Azure"
   description="Conseils pour migrer votre code SQL Azure SQL Data Warehouse pour développer des solutions."
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
   ms.date="08/02/2016"
   ms.author="lodipalm;barbkess;sonyama;jrj"/>

# <a name="migrate-your-sql-code-to-sql-data-warehouse"></a>Migrer votre code SQL pour Data Warehouse SQL

Lorsque vous migrez votre code à partir d’une autre base de données vers SQL Data Warehouse, vous devrez probablement apporter des modifications à votre base de code. Certaines fonctionnalités Data Warehouse SQL peuvent considérablement améliorer les performances qu’ils sont conçus pour fonctionner en mode distribué. Toutefois, pour maintenir des performances et échelle, certaines fonctionnalités ne sont également pas disponibles.

## <a name="common-t-sql-limitations"></a>Limitations T-SQL courantes

La liste suivante résume la fonctionnalité courante qui ne sont pas prises en charge dans le magasin de données SQL Azure. Les liens vous dirigent vers les solutions de contournement pour la fonctionnalité non prises en charge :

- [Jointures ANSI sur les mises à jour][]
- [Jointures ANSI sur les suppressions][]
- [déclaration de fusion][]
- jointures croisées-base de données
- [curseurs][]
- [SÉLECTIONNEZ... DANS][]
- [INSÉRER... EXÉCUTION][]
- clause de sortie
- fonctions définies par l’utilisateur en ligne
- déclaration de plusieurs fonctions
- [expressions de table communes](#Common-table-expressions)
- [expressions de table communes (CTE)] (#Recursive-common-table-expressions-(CTE)
- Procédures et fonctions CLR
- fonction $partition
- variables de table
- paramètres de valeur de table
- transactions distribuées
- valider / travail de restauration
- Enregistrer transaction
- contextes d’exécution (EXECUTE AS)
- [Group by, clause avec rollup / cube / jeux d’options de regroupement][]
- [niveaux d’imbrication au-delà de 8][]
- [mise à jour entre les affichages][]
- [utilisation de l’instruction select à affecter une variable][]
- [aucune donnée MAX ne tapez de chaînes SQL dynamiques][]

Peut être effectué en la plupart de ces limitations peut être contournée. Explications sont fournies dans les articles de développement pertinents mentionnés ci-dessus.

## <a name="supported-cte-features"></a>Fonctionnalités CTE prises en charge

Expressions de table communes (CTE) sont partiellement prises en charge dans Data Warehouse SQL.  Les fonctionnalités CTE suivantes sont actuellement prises en charge :

- Une CTE peut être indiquée dans une instruction SELECT.
- Une CTE peut être indiquée dans une instruction CREATE VIEW.
- Une CTE peut être indiquée dans une instruction créer tableau en tant que sélectionner (SACT).
- Une CTE peut être indiquée dans une instruction de créer à distance tableau en tant que sélectionner (CRTAS).
- Une CTE peut être indiquée dans une instruction créer externes tableau en tant que sélectionner (CETAS).
- Une table distante peut être référencée à partir d’une CTE.
- Une table externe peut être référencée à partir d’une CTE.
- Plusieurs définitions de requête CTE peuvent être définies dans une CTE.

## <a name="cte-limitations"></a>Limitations CTE

Expressions de table communes présentent des limites de Data Warehouse SQL, notamment :

- Une CTE doit être suivie d’une instruction SELECT unique. Insertion, mise à jour, supprimer, et les instructions de fusion ne sont pas pris en charge.
- Une expression de table commune qui comprend des références à elle-même (expression de table commune récursive) n’est pas pris en charge (voir ci-dessous section).
- Spécifier plusieurs avec la clause dans une CTE n’est pas autorisée. Par exemple, si un CTE_query_definition contient une sous-requête, cette sous-requête ne peut pas contenir un imbriquées avec clause qui définit une autre CTE.
- Une clause ORDER BY ne peuvent pas être utilisée dans CTE_query_definition, à l’exception lorsqu’une clause TOP est spécifiée.
- Lorsqu’une CTE est utilisée dans une instruction qui fait partie d’un lot, l’instruction avant qu’il doit être suivie par un point-virgule.
- Lorsqu’il est utilisé dans les instructions préparées par sp_prepare, CTE comportera la même façon que les autres instructions SELECT dans PDW. Toutefois, si CTE sont utilisés dans le cadre de CETAS préparé par sp_prepare, le comportement peut différer à partir de SQL Server et les autres instructions PDW en raison de la façon de liaison est activée pour sp_prepare. Si l’option que références que CTE utilise une colonne incorrecte qui n’existe pas dans CTE, le sp_prepare passera sans détecter l’erreur, mais l’erreur est levée pendant sp_execute à la place.

## <a name="recursive-ctes"></a>CTE récursives

CTE récursive ne sont pas prises en charge dans Data Warehouse SQL.  La migraion de CTE récursive peut être quelque peu complète et la meilleure procédure est pour décomposer les en plusieurs étapes. Vous pouvez généralement utiliser une boucle et remplir une table temporaire que vous parcourez les requêtes intermédiaires récursive. Une fois que la table temporaire est remplie vous pouvez puis renvoyer les données sous forme d’un jeu de résultats unique. Une approche similaire a été utilisée pour résoudre `GROUP BY WITH CUBE` dans l’article [group by, clause avec rollup / cube / jeux d’options de regroupement][] .

## <a name="unsupported-system-functions"></a>Fonctions non prises en charge système

Il existe également certaines fonctions système qui ne sont pas pris en charge. Les principales que vous pouvez généralement trouver utilisées dans data warehouse sont notamment :

- NEWSEQUENTIALID()
- @@NESTLEVEL()
- @@IDENTITY()
- @@ROWCOUNT()
- ROWCOUNT_BIG
- ERROR_LINE()

Certains de ces problèmes peuvent être contournées.

## <a name="rowcount-workaround"></a>@@ROWCOUNTsolution de contournement

Pour contourner l’absence de prise en charge pour @@ROWCOUNT, créer une procédure stockée qui sera récupérer le dernier nombre de lignes à partir de sys.dm_pdw_request_steps, puis exécutez `EXEC LastRowCount` après une instruction DML.

```sql
CREATE PROCEDURE LastRowCount AS
WITH LastRequest as 
(   SELECT TOP 1    request_id
    FROM            sys.dm_pdw_exec_requests
    WHERE           session_id = SESSION_ID()
    AND             resource_class IS NOT NULL
    ORDER BY end_time DESC
),
LastRequestRowCounts as
(
    SELECT  step_index, row_count
    FROM    sys.dm_pdw_request_steps
    WHERE   row_count >= 0
    AND     request_id IN (SELECT request_id from LastRequest)
)
SELECT TOP 1 row_count FROM LastRequestRowCounts ORDER BY step_index DESC
;
```

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir une liste complète de toutes les instructions T-SQL pris en charge, consultez [rubriques Transact-SQL][].

<!--Image references-->

<!--Article references-->
[Jointures ANSI sur les mises à jour]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[Jointures ANSI sur les suppressions]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[déclaration de fusion]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[INSÉRER... EXÉCUTION]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[Rubriques Transact-SQL]: ./sql-data-warehouse-reference-tsql-statements.md

[curseurs]: ./sql-data-warehouse-develop-loops.md
[SÉLECTIONNEZ... DANS]: ./sql-data-warehouse-develop-ctas.md#selectinto
[Group by, clause avec rollup / cube / jeux d’options de regroupement]: ./sql-data-warehouse-develop-group-by-options.md
[niveaux d’imbrication au-delà de 8]: ./sql-data-warehouse-develop-transactions.md
[mise à jour entre les affichages]: ./sql-data-warehouse-develop-views.md
[utilisation de l’instruction select à affecter une variable]: ./sql-data-warehouse-develop-variable-assignment.md
[aucune donnée MAX ne tapez de chaînes SQL dynamiques]: ./sql-data-warehouse-develop-dynamic-sql.md

<!--MSDN references-->

<!--Other Web references-->
