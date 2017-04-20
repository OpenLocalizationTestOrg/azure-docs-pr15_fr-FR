<properties
   pageTitle="Attribution de variables dans SQL Data Warehouse | Microsoft Azure"
   description="Conseils permettant d’affecter des variables Transact-SQL Azure SQL Data Warehouse pour développer des solutions."
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

# <a name="assign-variables-in-sql-data-warehouse"></a>Affecter des variables dans Data Warehouse SQL
Variables dans SQL Data Warehouse sont définis à l’aide de la `DECLARE` instruction ou la `SET` instruction.

Toutes les conditions suivantes sont parfaitement valide manières de définir la valeur d’une variable :

## <a name="setting-variables-with-declare"></a>Définition des variables avec DECLARE

L’initialisation de variables avec DECLARE est une des façons plus flexibles pour définir une valeur de la variable dans Data Warehouse SQL.

```sql
DECLARE @v  int = 0
;
```

Vous pouvez également utiliser DECLARE pour définir plusieurs variables à la fois. Vous ne pouvez pas utiliser `SELECT` ou `UPDATE` à procédez comme suit :

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Vous ne pouvez pas initialisation et utiliser une variable dans l’instruction DECLARE même. Pour illustrer le point de l’exemple ci-dessous n’est **pas** autorisé en tant que @p1 est initialisé et utilisé dans la même instruction DECLARE. Cela provoquera une erreur.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Valeurs de paramètre avec jeu
Jeu est une méthode très courante pour définir une seule variable.

Tous les exemples ci-dessous sont valides façons de définir une variable avec jeu :

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Vous pouvez uniquement définir une variable à la fois avec ensemble. Toutefois, comme puisse être vu ci-dessus opérateurs composés de permis associé.

## <a name="limitations"></a>Limitations
Vous ne pouvez pas utiliser SELECT ou UPDATE à affecter une variable.


## <a name="next-steps"></a>Étapes suivantes
Pour obtenir d’autres conseils de développement, voir [vue d’ensemble du développement][].

<!--Image references-->

<!--Article references-->
[vue d’ensemble de développement]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
