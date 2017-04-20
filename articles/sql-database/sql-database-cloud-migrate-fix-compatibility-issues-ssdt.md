<properties
   pageTitle="Corriger les problèmes de compatibilité de base de données SQL Server avant la migration de base de données SQL | Microsoft Azure"
   description="Microsoft Azure SQL base de données, migration de base de données, sa compatibilité, Assistant de Migration SQL Azure, SSDT"
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

# <a name="migrate-a-sql-server-database-to-azure-sql-database-using-sql-server-data-tools-for-visual-studio"></a>Migrer une base de données SQL Server à l’aide de SQL Server Data Tools pour Visual Studio de la base de données SQL Azure 

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Conseiller de mise à niveau](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

Dans cet article, vous apprenez à détecter et corriger les problèmes de compatibilité de base de données SQL Server à l’aide de SQL Server Data Tools pour Visual Studio avant la migration de base de données SQL Azure.

## <a name="using-sql-server-data-tools-for-visual-studio"></a>À l’aide de SQL Server Data Tools pour Visual Studio

Utiliser SQL Server Data Tools pour Visual Studio (« SSDT ») pour importer le schéma de base de données dans un projet de base de données Visual Studio pour l’analyse. Pour analyser, vous spécifiez la plateforme cible pour le projet en tant que V12 de base de données SQL et générez le projet. Si la génération réussit, la base de données est compatible. Si la génération échoue, vous pouvez résoudre les erreurs de SSDT (ou un des autres outils mentionnés dans cette rubrique). Une fois que le projet est généré avec succès, vous pouvez le publier en tant qu’une copie de la base de données source. Vous pouvez puis utiliser la fonctionnalité de comparaison des données dans SSDT pour copier les données à partir de la base de données source à la base de données Azure SQL V12 compatible. Vous pouvez alors migrer cette base de données mis à jour. Pour utiliser cette option, télécharger la [version la plus récente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx).

  ![Diagramme de migration VSSSDT](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

  > [AZURE.NOTE] Si la migration de schéma uniquement est requise, le schéma peut être publié directement à partir de Visual Studio directement à la base de données SQL Azure. Utilisez cette méthode lorsque le schéma de base de données requiert plus de modifications pouvant être géré par l’Assistant migration uniquement.

## <a name="detecting-compatibility-issues-using-sql-server-data-tools-for-visual-studio"></a>Détecter les problèmes de compatibilité liés à l’aide de SQL Server Data Tools pour Visual Studio
   
1.  Ouvrez l' **Explorateur d’objets SQL Server** dans Visual Studio. Utiliser **Ajouter SQL Server** pour vous connecter à l’instance SQL Server contenant la base de données en cours de migration. Recherchez la base de données dans l’Explorateur d’objets, avec le bouton droit de la base de données, puis sélectionnez **Créer un nouveau projet...**     
    
    ![Nouveau projet](./media/sql-database-migrate-visualstudio-ssdt/02MigrateSSDT.png)    
   
2.  Configurer les paramètres d’importation pour **Importer des objets de portée application**. Désactivez les options pour importer les éléments suivants : référencées connexions, les autorisations et les paramètres de base de données.    

    ![texte de remplacement](./media/sql-database-migrate-visualstudio-ssdt/03MigrateSSDT.png)    

3.  Cliquez sur **Démarrer** pour importer la base de données et créer le projet contenant un fichier de script T-SQL pour chaque objet dans la base de données. Les fichiers de script imbriquées dans des dossiers au sein du projet.    

    ![texte de remplacement](./media/sql-database-migrate-visualstudio-ssdt/04MigrateSSDT.png)    

4.  Dans l’Explorateur de solutions Visual Studio, avec le bouton droit de la base de données projet et sélectionnez Propriétés. Dans la page **Paramètres du projet** , configurez la plateforme cible à V12 de base de données SQL Microsoft Azure.    
    
    ![texte de remplacement](./media/sql-database-migrate-visualstudio-ssdt/05MigrateSSDT.png)    
    
5.  Droit du projet, puis cliquez sur **créer** pour générer le projet.    
    
    ![texte de remplacement](./media/sql-database-migrate-visualstudio-ssdt/06MigrateSSDT.png)    
    
6.  La **Liste d’erreurs** affiche chaque incompatibilité. Dans ce cas, le nom d’utilisateur identité est incompatible. Dans la mesure où il est incompatible, vous pouvez transformer en commentaire ou supprimez-le (et adresse implications de la suppression de cette connexion et le rôle de la solution de base de données).     
    
    ![texte de remplacement](./media/sql-database-migrate-visualstudio-ssdt/07MigrateSSDT.png)    
    
## <a name="fixing-compatibility-issues-using-sql-server-data-tools-for-visual-studio"></a>Résolution des problèmes de compatibilité liés à l’aide de SQL Server Data Tools pour Visual Studio

1.  Double-cliquez sur le premier script pour ouvrir le script dans une fenêtre de requête et commentez le script et l’exécuter le script.     
    ![texte de remplacement](./media/sql-database-migrate-visualstudio-ssdt/08MigrateSSDT.png)

2.  Répétez cette procédure pour chaque script contenant incompatibilité jusqu'à ce qu’aucune erreur ne reste.    
    ![texte de remplacement](./media/sql-database-migrate-visualstudio-ssdt/09MigrateSSDT.png)
    
3.  Lorsque la base de données ne c'est-à-dire pas d’erreurs, cliquez sur le projet et sélectionnez **Publier**. Une copie de la base de données source est créée et publiée (il est fortement recommandé d’utiliser une copie, au moins au début).     
 - Avant de publier, en fonction de la version de SQL Server source (antérieures à SQL Server 2014), vous devrez peut-être réinitialiser la plateforme du projet cible pour activer le déploiement.     
 - Si vous migrez une ancienne base de données SQL Server, ne présentez pas toutes les fonctionnalités dans le projet ne sont pas prises en charge dans la source de SQL Server jusqu'à migrer la base de données vers une nouvelle version de SQL Server.     

        ![alt text](./media/sql-database-migrate-visualstudio-ssdt/10MigrateSSDT.png)    
    
        ![alt text](./media/sql-database-migrate-visualstudio-ssdt/11MigrateSSDT.png)    
        
4.  Dans l’Explorateur d’objets SQL Server, avec le bouton droit de votre base de données source, puis cliquez sur **Comparaison de données**. Comparaison du projet à la base de données d’origine permet d’identifier quelles modifications ont été apportées par l’Assistant. Sélectionnez votre version Azure SQL V12 de la base de données, puis sur **Terminer**.    
    
    ![texte de remplacement](./media/sql-database-migrate-visualstudio-ssdt/12MigrateSSDT.png)    
    
    ![texte de remplacement](./media/sql-database-migrate-visualstudio-ssdt/13MigrateSSDT.png)    

5.  Passez en revue les différences détectées, puis sur **Mettre à jour cible** pour migrer les données de la base de données source dans la base de données Azure SQL V12.     
    
    ![texte de remplacement](./media/sql-database-migrate-visualstudio-ssdt/14MigrateSSDT.png)    
    
6.  Choisissez une méthode de déploiement. Voir [migrer une base de données SQL Server compatible base de données SQL.](sql-database-cloud-migrate.md)  

## <a name="next-steps"></a>Étapes suivantes

- [Version la plus récente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Version la plus récente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Ressources supplémentaires

- [V12 de base de données SQL](sql-database-v12-whats-new.md)
- [Transact-SQL partiellement ou non prises en charge de fonctions](sql-database-transact-sql-information.md)
- [Migrer des bases de données SQL Server à l’aide de l’Assistant Migration SQL Server](http://blogs.msdn.com/b/ssma/)
