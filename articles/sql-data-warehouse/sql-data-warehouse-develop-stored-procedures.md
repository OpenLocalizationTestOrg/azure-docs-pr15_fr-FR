<properties
   pageTitle="Procédures stockées dans SQL Data Warehouse | Microsoft Azure"
   description="Conseils pour l’implémentation des procédures stockées dans SQL Azure Data Warehouse pour développer des solutions."
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
   ms.date="06/30/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="stored-procedures-in-sql-data-warehouse"></a>Procédures stockées dans Data Warehouse SQL

Data Warehouse SQL prend en charge la plupart des fonctionnalités Transact-SQL figurant dans SQL Server. Il existe plus important échelle des fonctionnalités spécifiques qui nous souhaitons tirer parti de ressources pour optimiser les performances de votre solution.

Pour conserver l’échelle et les performances de Data Warehouse SQL il existe cependant également certaines fonctionnalités et les fonctionnalités qui ont des différences de comportement et d’autres personnes qui ne sont pas pris en charge.

Cet article explique comment mettre en œuvre des procédures stockées dans SQL Data Warehouse.

## <a name="introducing-stored-procedures"></a>Présentation des procédures stockées
Les procédures stockées constituent un excellent moyen d’encapsuler votre code SQL ; stocker près de vos données dans le data warehouse. En encapsulant le code en unités gérables procédures stockées aident les développeurs à organiser modules leurs solutions ; faciliter la plus grande réutilisation de code. Chaque procédure stockée peut également accepter des paramètres pour qu’ils soient encore plus flexible.

SQL Data Warehouse fournit une mise en œuvre simplifiées et simplifiée procédure stockée. La plus grande différence par rapport à SQL Server est que la procédure stockée n’est pas code préalable compilé. Dans data warehouse nous sont généralement moins concernées par le moment de la compilation. Il est plus important que le code de la procédure stockée est optimisé correctement lorsqu’il fonctionne par rapport à grands volumes de données. L’objectif consiste à enregistrer les heures, minutes et secondes pas aux millisecondes. Il est donc plus utile de considérer les procédures stockées en tant que conteneurs pour la logique SQL.     

Lorsque SQL Data Warehouse exécute votre procédure stockée les instructions SQL sont analysées traduit et optimisée en cours d’exécution. Pendant ce processus chaque instruction est convertie en requêtes distribuées. Le code SQL qui est exécuté en fait les données est différent de la requête envoyés.

## <a name="nesting-stored-procedures"></a>Procédures d’imbrication de stockage
Lorsque les procédures stockées appellent d’autres procédures stockées ou exécuter sql dynamique l’appel de procédure ou code stockée interne est dit être imbriquées.

Data Warehouse SQL prend en charge un maximum de 8 niveaux d’imbrication. Ceci est légèrement différente pour SQL Server. Le niveau d’imbrication dans SQL Server est 32.

L’appel de procédure stockée de niveau supérieur est égale pour imbriquer niveau 1

```sql
EXEC prc_nesting
```
Si la procédure stockée effectue également exécution un autre appel puis Ceci augmentera le niveau d’imbrication à 2
```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
Si la deuxième procédure puis exécute certains sql dynamique puis Ceci augmentera le niveau d’imbrication à 3
```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Remarque SQL Data Warehouse ne prend pas en charge actuellement @@NESTLEVEL. Vous devrez conserver une trace de votre niveau d’imbrication vous-même. Il est probable que vous atteignez la limite de niveau 8 imbriquer, mais si vous ne vous devrez ré-utiliser votre code et « fusionner » afin qu’il tienne dans cette limite.

## <a name="insertexecute"></a>INSÉRER... EXÉCUTER
SQL Data Warehouse ne vous permet pas d’utiliser le jeu de résultats d’une procédure stockée avec une instruction INSERT. Cependant, il est une approche alternative, que vous pouvez utiliser.

Reportez-vous à l’article suivant sur les [tables temporaires] pour obtenir un exemple sur la façon de procéder.

## <a name="limitations"></a>Limitations

Il existe certains aspects des procédures stockées Transact-SQL qui ne sont pas implémentées dans Data Warehouse SQL.

Ils sont :

- procédures stockées temporaires
- procédures stockées numérotées
- procédures stockées étendues
- Procédures stockées CLR
- option de chiffrement
- option de réplication
- Paramètres table
- paramètres en lecture seule
- paramètres par défaut
- contextes d’exécution
- instruction return

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir d’autres conseils de développement, voir [vue d’ensemble du développement][].

<!--Image references-->

<!--Article references-->
[tables temporaires]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[vue d’ensemble de développement]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[nest level]: https://msdn.microsoft.com/library/ms187371.aspx

<!--Other Web references-->
