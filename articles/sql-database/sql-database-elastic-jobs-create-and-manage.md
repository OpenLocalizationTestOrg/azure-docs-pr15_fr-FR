<properties
    pageTitle="Créer et gérer à l’échelle des bases de données SQL Azure à l’aide de travaux élastique | Microsoft Azure"
    description="Passez en revue la création et la gestion d’un travail élastique de base de données."
    services="sql-database"
    documentationCenter=""
    manager="jhubbard"
    authors="ddove"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/27/2016"
    ms.author="ddove"/>

# <a name="create-and-manage-scaled-out-azure-sql-databases-using-elastic-jobs-preview"></a>Créer et gérer à l’échelle des bases de données SQL Azure à l’aide de travaux élastique (preview)

> [AZURE.SELECTOR]
- [Portail Azure](sql-database-elastic-jobs-create-and-manage.md)
- [PowerShell](sql-database-elastic-jobs-powershell.md)


**Tâches de base de données élastique** simplifient la gestion des groupes de bases de données en exécutant les opérations d’administration tels que les modifications de schéma, gestion des informations d’identification, mises à jour des données de référence, collecte de données de performance ou collection de télémétrie client (client). Tâches de base de données élastiques est actuellement disponibles via le portail Azure et les applets de commande PowerShell. Toutefois, les surfaces portails Azure réduite fonctionnalité limitée à l’exécution sur toutes les bases de données dans une [base de données élastique pool (preview)](sql-database-elastic-pool.md). Pour accéder à des fonctionnalités supplémentaires et l’exécution de scripts sur un groupe de bases de données, y compris une collection de sites personnalisée ou un jeu de partagé (créé à l’aide de la [bibliothèque de client de base de données élastique](sql-database-elastic-scale-introduction.md)), voir [Création et gestion des tâches à l’aide de PowerShell](sql-database-elastic-jobs-powershell.md). Pour plus d’informations sur les tâches, voir [vue d’ensemble des tâches de base de données élastique](sql-database-elastic-jobs-overview.md). 

## <a name="prerequisites"></a>Conditions préalables

* Un abonnement Azure. Pour une version d’évaluation gratuite, voir [version d’évaluation gratuite d’un mois](https://azure.microsoft.com/pricing/free-trial/).
* Un pool élastique de base de données. Consultez [sur élastique pools de base de données](sql-database-elastic-pool.md)
* Installation de composants de service de travail élastique de base de données. Voir [installer le service de tâches de base de données élastique](sql-database-elastic-jobs-service-installation.md).

## <a name="creating-jobs"></a>Création de tâches

1. À l’aide du [portail Azure](https://portal.azure.com), à partir d’un pool de travail de base de données élastique existant, cliquez sur **Créer tâche**.
2. Tapez le nom d’utilisateur et mot de passe de l’administrateur de base de données (créé à l’installation de tâches) pour la base de données de contrôle de travaux (stockage de métadonnées pour les travaux).

    ![Nommez la tâche, tapez ou collez code et cliquez sur Exécuter][1]
2. Dans la carte de **Créer une tâche** , tapez un nom pour la tâche.
3. Tapez le nom d’utilisateur et mot de passe pour vous connecter aux bases de données cible avec les autorisations suffisantes pour l’exécution de scripts réussir.
4. Collez ou tapez dans le script T-SQL.
5. Cliquez sur **Enregistrer** , puis sur **exécuter**.

    ![Créer des tâches et exécuter][5]

## <a name="run-idempotent-jobs"></a>Exécuter des travaux idempotent

Lorsque vous exécutez un script par rapport à un ensemble de bases de données, vous devez être sûr que le script est idempotent. Autrement dit, le script doit être en mesure d’exécuter à plusieurs reprises, même si elle a échoué avant dans un état incomplet. Par exemple, lorsqu’un script échoue, le travail sera automatiquement retentée jusqu'à ce qu’il a réussi (limites, en tant que la nouvelle tentative logique finalement cesse de la nouvelle tentative). La façon de procéder consiste à utiliser l’une clause « IF EXISTS » et supprimer toute instance trouvée avant de créer un nouvel objet. Voici un exemple :

    IF EXISTS (SELECT name FROM sys.indexes
            WHERE name = N'IX_ProductVendor_VendorID')
    DROP INDEX IX_ProductVendor_VendorID ON Purchasing.ProductVendor;
    GO
    CREATE INDEX IX_ProductVendor_VendorID
    ON Purchasing.ProductVendor (VendorID);

Vous pouvez également utiliser une clause « IF NOT EXISTS » avant de créer une nouvelle instance :

    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
     CREATE TABLE TestTable(
      TestTableId INT PRIMARY KEY IDENTITY,
      InsertionTime DATETIME2
     );
    END
    GO

    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO

Ce script met à jour la table créée précédemment.

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN

    ALTER TABLE TestTable

    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO


## <a name="checking-job-status"></a>Vérifier le statut de tâche

Une fois un travail a commencé, vous pouvez vérifier la progression.

1. Dans la page pool élastique de base de données, cliquez sur **Gérer les travaux**.

    ![Cliquez sur « Gérer les travaux »][2]

2. Cliquez sur le nom (a) d’une tâche. L' **état** peut être « Terminé » ou « A échoué. » Détails de la tâche apparaissent (b) avec la date et l’heure de création et l’exécution. La liste (c) ci-dessous qui indique la progression du script sur chaque base de données dans le pool, précisant la date et l’heure.

    ![Vérification d’un travail terminé][3]


## <a name="checking-failed-jobs"></a>Échec de la vérification des tâches

Si une tâche échoue, un journal de son exécution accessibles. Cliquez sur le nom de la tâche a échoué pour afficher les détails.

![Vérifier l’échec d’un travail][4]


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-create-and-manage/screen-1.png
[2]: ./media/sql-database-elastic-jobs-create-and-manage/click-manage-jobs.png
[3]: ./media/sql-database-elastic-jobs-create-and-manage/running-jobs.png
[4]: ./media/sql-database-elastic-jobs-create-and-manage/failed.png
[5]: ./media/sql-database-elastic-jobs-create-and-manage/screen-2.png

 
