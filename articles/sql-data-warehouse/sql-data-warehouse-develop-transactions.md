<properties
   pageTitle="Transactions SQL Data Warehouse | Microsoft Azure"
   description="Conseils pour l’implémentation des transactions dans SQL Azure Data Warehouse pour développer des solutions."
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
   ms.date="07/31/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="transactions-in-sql-data-warehouse"></a>Transactions dans Data Warehouse SQL

Comme prévu, SQL Data Warehouse prend en charge les transactions dans le cadre de la charge de travail de magasin de données. Toutefois, pour vous assurer que les performances de Data Warehouse SQL sont conservée à l’échelle certaines fonctionnalités sont limitées par rapport à SQL Server. Cet article met en évidence les différences et répertorie les autres colonnes. 

## <a name="transaction-isolation-levels"></a>Niveaux d’isolement transaction
Data Warehouse SQL met en œuvre des transactions ACID. Toutefois, l’isolement de la prise en charge transactions est limitée aux `READ UNCOMMITTED` et il ne peut pas être modifiée. Vous pouvez mettre en œuvre un certain nombre de codage de méthodes pour éviter des lectures incorrectes de données s’il s’agit d’un problème pour vous. Les méthodes les plus populaires exploitent SACT et changement de partition table (souvent appelée le modèle de fenêtre coulissante) pour empêcher les utilisateurs d’interrogation de données sont toujours en cours de préparation. Les vues préalable filtrer les données est également une approche populaires.  

## <a name="transaction-size"></a>Taille des transactions
La taille est limitée une opération de modification de données unique. La limite est appliquée aujourd'hui « par distribution ». Par conséquent, l’allocation de total peut être calculée à multiplier la limite par le nombre de distribution. Pour approximative le nombre maximal de lignes de la transaction par lequel diviser la lettrine distribution la taille totale de chaque ligne. Pour les colonnes de longueur variable envisagez prendre une longueur de colonne moyenne plutôt que d’utiliser la taille maximale.

Dans le tableau ci-dessous les hypothèses suivantes ont été apportées :

* Une répartition uniforme des données s’est produite 
* La longueur moyenne ligne est de 250 octets

| [DWU][]    | Limiter par distribution (talon) | Nombre de distribution | Taille des transactions MAX (talon) | # Lignes par répartition | Nombre maximal de lignes par transaction |
| ------ | -------------------------- | ----------------------- | -------------------------- | ----------------------- | ------------------------ |
| DW100  |  1                         | 60                      |   60                       |   4,000,000             |    240,000,000           |
| DW200  |  1,5                       | 60                      |   90                       |   6,000,000             |    360,000,000           |
| DW300  |  2.25                      | 60                      |  135                       |   9,000,000             |    540,000,000           |
| DW400  |  3                         | 60                      |  180                       |  12,000,000             |    720,000,000           |
| DW500  |  3,75                      | 60                      |  225                       |  15,000,000             |    900,000,000           |
| DW600  |  4.5                       | 60                      |  270                       |  18,000,000             |  1,080,000,000           |
| DW1000 |  7.5                       | 60                      |  450                       |  30 000 000             |  1,800,000,000           |
| DW1200 |  9                         | 60                      |  540                       |  36,000,000             |  2,160,000,000           |
| DW1500 | 11,25                      | 60                      |  675                       |  45,000,000             |  2,700,000,000           |
| DW2000 | 15                         | 60                      |  900                       |  60 000 000             |  3,600,000,000           |
| DW3000 | 22.5                       | 60                      |  1,350                     |  90,000,000             |  5,400,000,000           |
| DW6000 | 45                         | 60                      |  2700                     | 180,000,000             | 10,800,000,000           |

La limite de taille de transaction est appliquée par transaction ou opération. Il n’est pas appliqué au sein de toutes les transactions simultanées. Par conséquent, chaque transaction est autorisée à écrire cette quantité de données dans le journal. 

Pour optimiser et réduire la quantité de données écrites dans le journal, consultez l’article [meilleures pratiques concernant les Transactions][] .

> [AZURE.WARNING] La taille maximale de transactions ne peut être obtenue pour hachage ou même de tables ROUND_ROBIN distribué où se trouve la répartition des données. Si la transaction écrit des données de manière inclinée vers les répartitions la limite est susceptible d’être contacté avant la taille maximale de transactions.
<!--REPLICATED_TABLE-->

## <a name="transaction-state"></a>État de transaction
Data Warehouse SQL utilise la fonction XACT_STATE() pour signaler l’échec d’une transaction à l’aide de la valeur -2. Cela signifie que la transaction a échoué et est marquée pour restaurer uniquement

> [AZURE.NOTE] L’utilisation de-2 supérieur par la fonction XACT_STATE pour indiquer l’échec d’une transaction représente un comportement différent à SQL Server. SQL Server utilise la valeur -1 pour représenter une transaction validable non. SQL Server peut tolérer des erreurs à l’intérieur d’une transaction sans qu’il ait été marqué comme non validable. Par exemple `SELECT 1/0` serait génèrent une erreur mais pas forcer une transaction dans un état non validable. SQL Server permet également de lectures dans la transaction validable non. Toutefois, SQL Data Warehouse ne permet pas vous procédez comme suit. Si une erreur se produit à l’intérieur d’une transaction SQL Data Warehouse il insère automatiquement l’état de-2 et vous ne pourrez pas apporter les instructions select davantage jusqu'à ce que l’instruction a été annulée. Il est donc important de vérifier que votre code d’application pour voir si elle utilise XACT_STATE() comme vous devrez peut-être apporter des modifications de code.

Par exemple, SQL Server peut s’afficher une transaction qui ressemble à ceci :

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
        
        IF @@TRANCOUNT > 0
        BEGIN
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Si vous conservez votre code tel quel ci-dessus vous obtenez le message d’erreur suivant :

Message 111233, niveau 16, état 1, ligne 1 111233 ; La transaction actuelle a été annulée, et toutes les modifications en attente ont été annulées. Cause : Une transaction dans un état restauration seule a été pas explicitement annulée avant une DDL, DML ou une instruction SELECT.

Vous obtiendrez également pas la sortie des fonctions ERROR_ *.

Le code doit être légèrement modifiées dans Data Warehouse SQL :

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();
        
        IF @@TRANCOUNT > 0
        BEGIN
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
        END

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Le comportement attendu est observé maintenant. L’erreur dans la transaction est géré et les fonctions ERROR_ * Indiquez les valeurs comme prévu.

Tout ce qui a changé est que le `ROLLBACK` de la transaction devaient se produire avant la lecture des informations d’erreur dans le `CATCH` bloc.

## <a name="errorline-function"></a>Fonction Error_Line()
Il est également à noter que SQL Data Warehouse ne pas mettre en œuvre ou prennent en charge de la fonction ERROR_LINE(). Si vous avez cela dans votre code, vous devrez supprimer pour être compatibles avec Data Warehouse SQL. Utiliser des étiquettes de requête dans votre code à la place pour implémenter une fonctionnalité équivalente. Consultez l’article [intitulé][] pour plus d’informations sur cette fonctionnalité.

## <a name="using-throw-and-raiserror"></a>À l’aide de lever et RAISERROR
LEVER est l’implémentation plus moderne pour lever des exceptions SQL Data Warehouse mais RAISERROR prend également en charge. Il existe quelques différences valent attention à toutefois.

- Messages d’erreur ne doit pas être dans la plage de 150 000 100,000 pour lever définies par l’utilisateur
- Messages d’erreur RAISERROR sont fixés à 50 000
- Utilisation des sys.messages n’est pas pris en charge

## <a name="limitiations"></a>Limitiations
Data Warehouse SQL a-t-il quelques autres restrictions relatives aux transactions.

Elles se présentent comme suit :

- Aucune des transactions distribuées
- Aucune transactions imbriquées autorisées
- Ne pas enregistrer points autorisé
- Aucune transactions nommées
- Aucune transactions marquées
- Aucune prise en charge pour DDL telles que `CREATE TABLE` à l’intérieur d’un utilisateur défini transaction

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur l’optimisation des transactions, voir [meilleures pratiques concernant les Transactions][].  Pour connaître les autres recommandations Data Warehouse SQL, voir [meilleures pratiques SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[development overview]: ./sql-data-warehouse-overview-develop.md
[Meilleures pratiques de transactions]: ./sql-data-warehouse-develop-best-practices-transactions.md
[Meilleures pratiques SQL Data Warehouse]: ./sql-data-warehouse-best-practices.md
[ÉTIQUETTE]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->

<!--Other Web references-->
