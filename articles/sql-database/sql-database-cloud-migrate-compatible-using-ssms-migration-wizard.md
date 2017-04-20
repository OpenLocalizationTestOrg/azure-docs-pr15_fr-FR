<properties
   pageTitle="Migrer la base de données SQL Server à SQL de base de données à l’aide de déploiement d’une base de données à l’Assistant de base de données Microsoft Azure | Microsoft Azure"
   description="Base de données de SQL Microsoft Azure, migration de base de données, l’Assistant de base de données Microsoft Azure"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-migrate"
   ms.date="08/24/2016"
   ms.author="carlrab"/>

# <a name="migrate-sql-server-database-to-sql-database-using-deploy-database-to-microsoft-azure-database-wizard"></a>Migrer la base de données SQL Server à SQL de base de données à l’aide de déploiement d’une base de données à l’Assistant de base de données Microsoft Azure


> [AZURE.SELECTOR]
- [Assistant Migration SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Exporter vers un fichier à DOS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Importer depuis un fichier à DOS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Transactions réplication](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

La base de données déployer Assistant de base de données Microsoft Azure dans SQL Server Management Studio permet de migrer une [base de données SQL Server compatible](sql-database-cloud-migrate.md) directement dans votre serveur de base de données SQL Azure.

## <a name="use-the-deploy-database-to-microsoft-azure-database-wizard"></a>Utiliser la base de données de déploiement Assistant bases de données Microsoft Azure

> [AZURE.NOTE] La procédure suivante part du principe que vous disposez d’une [base de données SQL server de sa mise en service](https://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database/).

1. Vérifiez que vous disposez de la dernière version de SQL Server Management Studio. Nouvelles versions de Management Studio sont mises à jour mensuelles doit rester synchronisé avec les mises à jour au portail Azure.

    > [AZURE.IMPORTANT] Il est recommandé d’utiliser toujours la dernière version de Management Studio doit pour rester synchronisé avec les mises à jour Microsoft Azure et base de données SQL. [Mettre à jour de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Ouvrez Management Studio et connectez-vous à votre base de données SQL Server à migrer dans l’Explorateur d’objets.
3. Avec le bouton droit de la base de données dans l’Explorateur d’objets, pointez sur **tâches**, puis cliquez sur **Déployer la base de données à Microsoft Azure SQL de base de données...**

    ![Déployer sur Azure à partir de menu tâches](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard01.png)

4.  Dans l’Assistant de déploiement, cliquez sur **suivant**, puis cliquez sur **se connecter** pour configurer la connexion à votre serveur de base de données SQL.

    ![Déployer sur Azure à partir de menu tâches](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard002.png)

5. Dans la connexion à la boîte de dialogue serveur, entrez vos informations de connexion pour vous connecter à votre serveur de base de données SQL.

    ![Déployer sur Azure à partir de menu tâches](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard00.png)

5.  Fournir les informations suivantes pour le fichier [à DOS](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) que cet Assistant crée pendant le processus de migration :

 - Le **nouveau nom de base de données** 
 - L' **Édition de Microsoft Azure SQL de base de données** ([niveau de service](sql-database-service-tiers.md))
 - La **taille maximale de base de données**
 - L' **Objectif de Service** (niveau de performance)
 - Le **nom de fichier temporaire**  

    ![Exporter les paramètres](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard02.png)

6.  Exécuter l’Assistant. Selon la taille et la complexité de la base de données, déploiement peut prendre quelques minutes à plusieurs heures. Si cet Assistant détecte des problèmes de compatibilité, les erreurs sont affichées à l’écran et la migration ne peut continuer. Pour des instructions sur la façon de résoudre les problèmes de compatibilité de base de données, accédez à [corriger les problèmes de compatibilité de base de données](sql-database-cloud-migrate-fix-compatibility-issues.md).

7.  Explorateur d’objets, vous connecter à votre base de données a été déplacée de votre serveur de base de données SQL Azure.
8.  À l’aide du portail Azure, permet d’afficher votre base de données et à ses propriétés.

## <a name="next-steps"></a>Étapes suivantes

- [Version la plus récente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Version la plus récente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Ressources supplémentaires

- [V12 de base de données SQL](sql-database-v12-whats-new.md)
- [Transact-SQL partiellement ou non prises en charge de fonctions](sql-database-transact-sql-information.md)
- [Migrer des bases de données SQL Server à l’aide de l’Assistant Migration SQL Server](http://blogs.msdn.com/b/ssma/)
