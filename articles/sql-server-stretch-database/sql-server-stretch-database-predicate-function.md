<properties
    pageTitle="Sélectionner les lignes à migrer en utilisant une fonction de filtre (base de données étirement) | Microsoft Azure"
    description="Découvrez comment sélectionner des lignes pour migrer en utilisant une fonction de filtre."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/28/2016"
    ms.author="douglasl"/>

# <a name="select-rows-to-migrate-by-using-a-filter-function-stretch-database"></a>Sélectionner des lignes pour migrer en utilisant une fonction de filtre (étirement de base de données)

Si vous stockez ces données dans une table distincte, vous pouvez configurer étirement de base de données pour migrer l’intégralité du tableau. En revanche, si votre tableau contient des données chaudes et froides, vous pouvez spécifier une fonction de filtre pour sélectionner les lignes à déplacer. Le prédicat de filtre correspond à une table inline\-à valeur de fonction. Cette rubrique explique comment écrire un tableau inline\-évaluer la fonction pour sélectionner les lignes à migrer.

>   [AZURE.NOTE] Si vous fournissez une fonction de filtre qui fonctionne mal, migration des données est fonctionne mal. Base de données étirement applique la fonction de filtre à la table en utilisant l’opérateur CROSS APPLY.

Si vous ne spécifiez pas une fonction de filtre, l’intégralité du tableau est migré.

Lorsque vous exécutez la base de données de l’activer pour étirement de l’Assistant, vous pouvez déplacer un tableau entier ou vous pouvez spécifier une fonction de filtre simple dans l’Assistant. Si vous voulez utiliser un autre type de la fonction filter pour sélectionner des lignes à déplacer, effectuez l’une des manières suivantes.

-   Quitter l’Assistant et exécutez l’instruction ALTER TABLE pour activer l’étirement pour le tableau et spécifier une fonction de filtre.

-   Exécutez l’instruction ALTER TABLE pour spécifier une fonction de filtre lorsque vous quittez l’Assistant.

La syntaxe ALTER TABLE permettant d’ajouter une fonction est décrite plus loin dans cette rubrique.

## <a name="basic-requirements-for-the-filter-function"></a>Configuration minimale requise pour la fonction filter
Le tableau inline\-fonction obligatoire pour un attribut de filtre de base de données étirement ressemble à l’exemple suivant.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate(@column1 datatype1, @column2 datatype2 [, ...n])
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE <predicate>
```
Les paramètres de la fonction doivent se trouver les identificateurs de colonnes de la table.

Liaison de schéma est nécessaire pour empêcher les colonnes qui sont utilisées par la fonction filter d’être supprimé ou modifié.

### <a name="return-value"></a>Valeur de retour
Si la fonction retourne un non\-résultat vide, la ligne est autorisée à être migrées. Dans le cas contraire \- autrement dit, si la fonction ne renvoie pas un résultat \- la ligne n’est pas autorisée à être migrées.

### <a name="conditions"></a>Conditions
La &lt; *prédicat* &gt; peut être constitué d’une condition ou d’une ou plusieurs conditions rejoint avec l’opérateur logique et.

```
<predicate> ::= <condition> [ AND <condition> ] [ ...n ]
```
Chaque condition à son tour peut compter d’une condition primitif ou de plusieurs conditions primitif rejoint avec l’opérateur logique ou.

```
<condition> ::= <primitive_condition> [ OR <primitive_condition> ] [ ...n ]
```

### <a name="primitive-conditions"></a>Conditions primitifs
Une condition primitif pouvez effectuez l’une des comparaisons suivantes.

```
<primitive_condition> ::=
{
<function_parameter> <comparison_operator> constant
| <function_parameter> { IS NULL | IS NOT NULL }
| <function_parameter> IN ( constant [ ,...n ] )
}
```

-   Comparer un paramètre de fonction pour une expression de constante. Par exemple, `@column1 < 1000`.

    Voici un exemple qui vérifie si la valeur d’une colonne de *date* est &lt; 1/1/2016.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
    GO

    ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(date),
        MIGRATION_STATE = OUTBOUND
    ) )
    ```

-   Appliquer l’opérateur est nul ou n’est pas NULL à un paramètre de fonction.

-   Utiliser l’opérateur IN pour comparer un paramètre de fonction dans une liste de valeurs de constante.

    Voici un exemple vérifie si la valeur d’un *expédition\_état* colonne est `IN (N'Completed', N'Returned', N'Cancelled')`.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate(@column1 nvarchar(15))
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 IN (N'Completed', N'Returned', N'Cancelled')
    GO

    ALTER TABLE table1 SET ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(shipment_status),
        MIGRATION_STATE = OUTBOUND
    ) )
    ```

### <a name="comparison-operators"></a>Opérateurs de comparaison
Les opérateurs de comparaison suivants sont pris en charge.

`<, <=, >, >=, =, <>, !=, !<, !>`

```
<comparison_operator> ::= { < | <= | > | >= | = | <> | != | !< | !> }
```

### <a name="constant-expressions"></a>Expressions de constante
L’une des constantes que vous utilisez dans une fonction de filtre peuvent être n’importe quelle expression déterministe qui peut être évaluée lorsque vous définissez la fonction. Les expressions de constante peuvent contenir des manières suivantes.

-   Littéraux. Par exemple, `N’abc’, 123`.

-   Expressions algébriques. Par exemple, `123 + 456`.

-   Fonctions déterministes. Par exemple, `SQRT(900)`.

-   Conversions déterministes qui utilisent CAST ou CONVERT. Par exemple, `CONVERT(datetime, '1/1/2016', 101)`.

### <a name="other-expressions"></a>D’autres expressions
Vous pouvez utiliser la BETWEEN opérateurs et NOT BETWEEN si la fonction qui en résulte est conforme aux règles décrites ici une fois que vous remplacez le BETWEEN et pas entre les opérateurs avec l’équivalent et et ou d’expressions.

Vous ne pouvez pas utiliser des sous-requêtes ou non\-déterministes fonctions telles qu’ALEA () ou GETDATE().

## <a name="add-a-filter-function-to-a-table"></a>Ajouter une fonction de filtre à une table
Ajouter une fonction de filtre à une table en exécutant l’instruction **ALTER TABLE** et en spécifiant une table existante inline\-évaluée fonction à la valeur de la **filtre\_PRÉDICATS** paramètre. Par exemple :

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE = dbo.fn_stretchpredicate(column1, column2),
    MIGRATION_STATE = <desired_migration_state>
) )
```
Une fois que vous liez la fonction à la table en tant que prédicat, les actions suivantes sont remplies.

-   La migration de données heure suivante se produit, seules les lignes pour lesquelles la fonction retourne une non\-valeur vide sont migrés.

-   Les colonnes utilisées par la fonction sont liée au schéma. Vous ne pouvez pas modifier ces colonnes dans la mesure où une table est à l’aide de la fonction en tant que sa prédicat de filtre.

Vous ne pouvez pas supprimer la table inline\-à valeur de fonction dans la mesure où une table est à l’aide de la fonction en tant que sa prédicat de filtre.

>   [AZURE.NOTE] Pour améliorer les performances de la fonction filter, créer un index sur les colonnes utilisées par la fonction.

### <a name="passing-column-names-to-the-filter-function"></a>Transmission des noms de colonne pour la fonction filter
Lorsque vous attribuez une fonction de filtre à une table, spécifiez les noms de colonne passé à la fonction de filtre avec un nom d’une seule partie. Si vous spécifiez un nom en trois parties lorsque vous passez de la colonne des noms, des requêtes ultérieures par rapport à l’étirement\-table activée échouera.

Par exemple, si vous spécifiez un nom de colonne de trois parties comme le montre l’exemple suivant, l’instruction s’exécute correctement, mais les requêtes suivants sur la table échouera.

```tsql
ALTER TABLE SensorTelemetry
  SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE=dbo.fn_stretchpredicate(dbo.SensorTelemetry.ScanDate),
    MIGRATION_STATE = OUTBOUND )
  )
```

Spécifiez à la place, la fonction filter avec un nom de colonne d’une seule partie comme le montre l’exemple suivant.

```tsql
ALTER TABLE SensorTelemetry
  SET ( REMOTE_DATA_ARCHIVE = ON  (
    FILTER_PREDICATE=dbo.fn_stretchpredicate(ScanDate),
    MIGRATION_STATE = OUTBOUND )
  )
```

## <a name="addafterwiz"></a>Ajouter une fonction de filtre après l’exécution de l’Assistant  

Si vous souhaitez utiliser une fonction que vous ne pouvez pas créer de l’Assistant **Activer la base de données pour étirer** , vous pouvez exécuter l’instruction ALTER TABLE pour spécifier une fonction après avoir quitté l’Assistant. Avant d’appliquer une fonction, cependant, vous devez arrêter la migration de données qui se trouve déjà en cours et ramener données migrées. (Pour plus d’informations sur pourquoi cela est nécessaire, voir [Remplacer une fonction de filtre existante](#replacePredicate).  

1. Inverser le sens de migration et restaurer que les données déjà migrées. Vous ne pouvez pas annuler cette opération après son démarrage. Vous également Entraînez Azure les coûts pour les transferts de données sortantes \(sortie\). Pour plus d’informations, voir [comment Azure tarifs works](https://azure.microsoft.com/pricing/details/data-transfers/).  

    ```tsql  
    ALTER TABLE <table name>  
         SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = INBOUND ) ) ;   
    ```  

2. Attendre la migration terminer. Vous pouvez vérifier l’état dans le **Moniteur de base de données étirement** à partir de SQL Server Management Studio, ou vous pouvez interroger la vue **sys.dm_db_rda_migration_status** . Pour plus d’informations, voir [moniteur et résoudre les problèmes de migration des données](sql-server-stretch-database-monitor.md) ou [sys.dm_db_rda_migration_status](https://msdn.microsoft.com/library/dn935017.aspx).  

3. Créez la fonction de filtre que vous souhaitez appliquer à la table.  

4. Ajoutez la fonction dans la table et redémarrez la migration de données vers Azure.  

    ```tsql  
    ALTER TABLE <table name>  
        SET ( REMOTE_DATA_ARCHIVE  
            (           
                FILTER_PREDICATE = <predicate>,  
                MIGRATION_STATE = OUTBOUND  
            )  
        );   
    ```  

## <a name="filter-rows-by-date"></a>Filtrer les lignes par date
L’exemple suivant déplace toutes les lignes dont la colonne **date** contienne une valeur antérieure à 1 janvier 2016.

```tsql
-- Filter by date
--
CREATE FUNCTION dbo.fn_stretch_by_date(@date datetime2)
RETURNS TABLE
WITH SCHEMABINDING
AS
       RETURN SELECT 1 AS is_eligible WHERE @date < CONVERT(datetime2, '1/1/2016', 101)
GO
```

## <a name="filter-rows-by-the-value-in-a-status-column"></a>Filtrer les lignes par la valeur dans une colonne d’état
L’exemple suivant déplace toutes les lignes dont la colonne **statut** contienne une des valeurs spécifiées.

```tsql
-- Filter by status column
--
CREATE FUNCTION dbo.fn_stretch_by_status(@status nvarchar(128))
RETURNS TABLE
WITH SCHEMABINDING
AS
       RETURN SELECT 1 AS is_eligible WHERE @status IN (N'Completed', N'Returned', N'Cancelled')
GO
```

## <a name="filter-rows-by-using-a-sliding-window"></a>Filtrer les lignes à l’aide d’une fenêtre coulissante
Pour filtrer les lignes à l’aide d’une fenêtre coulissante, n’oubliez pas les conditions suivantes pour la fonction filter.

-   La fonction doit être déterministe. Par conséquent, vous ne pouvez pas créer une fonction qui recalcule automatiquement la fenêtre coulissante avec le temps.

-   La fonction utilise la liaison de schéma. Par conséquent, vous ne peut pas simplement mettre à jour la fonction « en place » tous les jours en appelant **ALTER FUNCTION** pour déplacer la fenêtre coulissante.

Commencez par une fonction de filtre à l’exemple suivant, qui permet de migrer des lignes dont la colonne **systemEndTime** contienne une valeur antérieure à 1 janvier 2016.

```tsql
CREATE FUNCTION dbo.fn_StretchBySystemEndTime20160101(@systemEndTime datetime2)
RETURNS TABLE
WITH SCHEMABINDING  
AS  
RETURN SELECT 1 AS is_eligible
  WHERE @systemEndTime < CONVERT(datetime2, '2016-01-01T00:00:00', 101) ;
```

Appliquer la fonction filter à la table.

```tsql
ALTER TABLE <table name>
SET (
        REMOTE_DATA_ARCHIVE = ON
                (
                        FILTER_PREDICATE = dbo.fn_StretchBySystemEndTime20160101 (SysEndTime)
                                , MIGRATION_STATE = OUTBOUND
                )
        )
;
```

Lorsque vous souhaitez mettre à jour de la fenêtre coulissante, effectuer les opérations suivantes.

1.  Créer une fonction qui spécifie la nouvelle fenêtre coulissante. L’exemple suivant sélectionne les dates antérieures au 2 janvier 2016, au lieu de 1 janvier 2016.

2.  Remplacez la fonction filter précédente par la nouvelle en appelant **ALTER TABLE**, comme illustré dans l’exemple suivant.

3. Vous pouvez également supprimer la fonction filtre précédent que vous utilisez n’est plus en appelant **DROP FUNCTION**. (Cette étape n’est pas affichée dans l’exemple).

```tsql
BEGIN TRAN
GO
        /*(1) Create new predicate function definition */
        CREATE FUNCTION dbo.fn_StretchBySystemEndTime20160102(@systemEndTime datetime2)
        RETURNS TABLE
        WITH SCHEMABINDING
        AS
        RETURN SELECT 1 AS is_eligible
               WHERE @systemEndTime < CONVERT(datetime2,'2016-01-02T00:00:00', 101)
        GO

        /*(2) Set the new function as the filter predicate */
        ALTER TABLE <table name>
        SET
        (
               REMOTE_DATA_ARCHIVE = ON
               (
                       FILTER_PREDICATE = dbo.fn_StretchBySystemEndTime20160102(SysEndTime),
                       MIGRATION_STATE = OUTBOUND
               )
        )
COMMIT ;
```

## <a name="more-examples-of-valid-filter-functions"></a>Autres exemples de fonctions de filtre valide

-   L’exemple suivant combine deux conditions primitives à l’aide de l’opérateur logique et.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate((@column1 datetime, @column2 nvarchar(15))
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
      WHERE @column1 < N'20150101' AND @column2 IN (N'Completed', N'Returned', N'Cancelled')
    GO

    ALTER TABLE table1 SET ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(date, shipment_status),
        MIGRATION_STATE = OUTBOUND
    ) )
    ```

-   L’exemple suivant utilise plusieurs conditions et une conversion déterministe avec convertir.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example1(@column1 datetime, @column2 int, @column3 nvarchar)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2015', 101)AND (@column2 < -100 OR @column2 > 100 OR @column2 IS NULL)AND @column3 IN (N'Completed', N'Returned', N'Cancelled')
    GO
    ```

-   L’exemple suivant utilise les fonctions et des opérateurs mathématiques.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example2(@column1 float)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 < SQRT(400) + 10
    GO
    ```

-   L’exemple suivant utilise la BETWEEN opérateurs et NOT BETWEEN. Cette utilisation est valide, car la fonction qui en résulte est conforme aux règles décrites ici une fois que vous remplacez le BETWEEN et pas entre les opérateurs avec l’équivalent et et ou d’expressions.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example3(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 BETWEEN 0 AND 100
                AND (@column2 NOT BETWEEN 200 AND 300 OR @column1 = 50)
    GO
    ```
    La fonction précédente équivaut à la fonction suivante après le remplacement du BETWEEN opérateurs et NOT BETWEEN avec l’équivalent et et ou d’expressions.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example4(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 >= 0 AND @column1 <= 100AND (@column2 < 200 OR @column2 > 300 OR @column1 = 50)
    GO
    ```

## <a name="examples-of-filter-functions-that-arent-valid"></a>Exemples de fonctions de filtrage qui ne sont pas valides

-   La fonction suivante n’est pas valide, car elle contient un non\-conversion déterministe.

    ```tsql
    CREATE FUNCTION dbo.fn_example5(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 < CONVERT(datetime, '1/1/2016')
    GO
    ```

-   La fonction suivante n’est pas valide, car elle contient un non\-appel de fonction déterministe.

    ```tsql
    CREATE FUNCTION dbo.fn_example6(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 < DATEADD(day, -60, GETDATE())
    GO
    ```

-   La fonction suivante n’est pas valide, car elle contient une sous-requête.

    ```tsql
    CREATE FUNCTION dbo.fn_example7(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 IN (SELECT SupplierID FROM Supplier WHERE Status = 'Defunct'))
    GO
    ```

-   Les fonctions suivantes ne sont pas valides, car les expressions qui utilisent des opérateurs algébriques ou intégré\-dans les fonctions doivent avoir pour résultat une constante lorsque vous définissez la fonction. Vous ne peut pas inclure des références à des colonnes dans des expressions algébriques ni d’appels de fonction.

    ```tsql
    CREATE FUNCTION dbo.fn_example8(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 % 2 =  0
    GO

    CREATE FUNCTION dbo.fn_example9(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE SQRT(@column1) = 30
    GO
    ```

-   La fonction suivante n’est pas valide car il ne respecte pas les règles décrites ici une fois que vous remplacez l’opérateur entre l’expression et équivalente.

    ```tsql
    CREATE FUNCTION dbo.fn_example10(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE (@column1 BETWEEN 1 AND 200 OR @column1 = 300) AND @column2 > 1000
    GO
    ```
    La fonction précédente équivaut à la fonction suivante une fois que vous remplacez l’opérateur entre l’expression et équivalente. Cette fonction n’est pas valide, car les conditions primitifs ne peuvent utiliser l’opérateur logique OR.

    ```tsql
    CREATE FUNCTION dbo.fn_example11(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE (@column1 >= 1 AND @column1 <= 200 OR @column1 = 300) AND @column2 > 1000
    GO
    ```

## <a name="how-stretch-database-applies-the-filter-function"></a>Comment étirement de base de données s’applique à la fonction filter
Base de données étirement s’applique la fonction filter à la table et détermine lignes éligibles en utilisant l’opérateur CROSS APPLY. Par exemple :

```tsql
SELECT * FROM stretch_table_name CROSS APPLY fn_stretchpredicate(column1, column2)
```
Si la fonction retourne un non\-de résultats pour la ligne vide, la ligne est autorisée à être migrées.

## <a name="replacePredicate"></a>Remplacer une fonction de filtre existante
Vous pouvez remplacer une fonction de filtre précédemment spécifié en exécutant l’instruction **ALTER TABLE** à nouveau et en spécifiant une nouvelle valeur pour le **filtre\_PRÉDICATS** paramètre. Par exemple :

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE = dbo.fn_stretchpredicate2(column1, column2),
    MIGRATION_STATE = <desired_migration_state>
```
La nouvelle table inline\-fonction comporte les conditions suivantes.

-   La nouvelle fonction doit être moins restrictif que celui de la fonction précédente.

-   Tous les opérateurs présentes dans l’ancienne fonction doivent exister dans la nouvelle fonction.

-   La nouvelle fonction ne peut pas contenir d’opérateurs qui n’existent pas dans l’ancienne fonction.

-   Impossible de modifier l’ordre des arguments de l’opérateur.

-   Uniquement les valeurs de constante qui font partie d’un `<, <=, >, >=` comparaison peut être modifié d’une manière qui rend la fonction moins restrictif.

### <a name="example-of-a-valid-replacement"></a>Exemple de remplacement valide
Supposons que la fonction suivante est le prédicat de filtre en cours.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate_old (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
            AND (@column2 < -100 OR @column2 > 100)
GO
```
La fonction suivante est un remplacement valide, car la constante nouvelle date (qui spécifie une date de démarcation ultérieurement) permet de la fonction moins restrictif.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate_new (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '2/1/2016', 101)
            AND (@column2 < -50 OR @column2 > 50)
GO
```

### <a name="examples-of-replacements-that-arent-valid"></a>Exemples de remplacement qui ne sont pas valides
La fonction suivante n’est pas un remplacement valide, car la constante nouvelle date (qui spécifie une date de démarcation plus haut) ne fait pas appel la fonction moins restrictif.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_1 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2015', 101)
            AND (@column2 < -100 OR @column2 > 100)
GO
```
La fonction suivante n’est pas un remplacement valide, car un opérateur de comparaison a été supprimé.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_2 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
            AND (@column2 < -50)
GO
```
La fonction suivante n’est pas un remplacement valide, car une nouvelle condition a été ajoutée avec l’opérateur logique et.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_3 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
            AND (@column2 < -100 OR @column2 > 100)
            AND (@column2 <> 0)
GO
```

## <a name="remove-a-filter-function-from-a-table"></a>Supprimer une fonction de filtre d’un tableau
Pour migrer l’intégralité du tableau au lieu de lignes sélectionnées, supprimer la fonction existante en définissant **filtre\_PRÉDICATS** avec la valeur null. Par exemple :

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE = NULL,
    MIGRATION_STATE = <desired_migration_state>
) )
```
Après avoir supprimé la fonction filter, toutes les lignes de la table sont éligibles pour la migration. Par conséquent, vous ne pouvez pas spécifier une fonction de filtre pour la même table plus tard, sauf si vous extrayez toutes les données à distance pour la table à partir d’Azure tout d’abord. Cette limitation existe pour éviter la situation où les lignes qui ne sont pas éligibles à la migration lorsque vous fournissez une nouvelle fonction filtre ont déjà été migrés vers Azure.

## <a name="check-the-filter-function-applied-to-a-table"></a>Vérifiez la fonction filtre appliquée à un tableau
Pour vérifier la fonction de filtre appliquée à une table, ouvrez la vue de catalogue **sys.remote\_données\_archive\_tables** et vérifiez la valeur de la **filtre\_prédicat** colonne. Si la valeur est null, l’intégralité du tableau est éligible pour l’archivage. Pour plus d’informations, voir [sys.remote_data_archive_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

## <a name="security-notes-for-filter-functions"></a>Notes de sécurité pour les fonctions de filtre  
Un compte doté de privilèges db_owner compromis peut effectuer les opérations suivantes.  

-   Créer et appliquer une fonction table qui consomme de grandes quantités de ressources du serveur ou attend pendant une période prolongée, ce qui entraîne un refus de service.  

-   Créer et appliquer une fonction table qui permet de déduire le contenu d’une table pour laquelle l’utilisateur a été explicitement refusé accès en lecture.  

## <a name="see-also"></a>Voir aussi

[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)
