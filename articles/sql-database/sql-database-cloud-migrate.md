<properties
   pageTitle="Migration de base de données SQL Server pour la base de données SQL | Microsoft Azure"
   description="Découvrez qu’en est-il local SQL Server migration base de données à la base de données SQL Azure dans le cloud. Utilisez les outils de migration de base de données pour tester la compatibilité avant la migration de la base de données."
   keywords="migration, migration de base de données sql server, les outils de migration de base de données de base de données, migrer base de données, effectuer une migration de la base de données sql"
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

# <a name="sql-server-database-migration-to-sql-database-in-the-cloud"></a>Migration de base de données SQL Server à SQL de base de données dans le cloud

Dans cet article, vous découvrez comment migrer une base de données ultérieure ou local SQL Server 2005 à base de données SQL Azure. Dans ce processus de migration de base de données, vous migrez votre schéma et vos données à partir de la base de données SQL Server dans votre environnement actuel dans la base de données SQL. Pour réussir, la base de données existante doit passer tout d’abord un test de compatibilité. Avec [V12 de base de données SQL](sql-database-v12-whats-new.md), il existe quelques problèmes de compatibilité restants, différent de problème liés aux opérations au niveau du serveur et les bases de données croisées. Bases de données et les applications qui s’appuient sur [partiellement ou non prises en charge des fonctions](sql-database-transact-sql-information.md) besoin d’une nouvelle ingénierie pour résoudre ces incompatibilité avant que la base de données SQL Server peut être migrée.

Pour migrer, voici les étapes vous permettent de tirer :

- **Test de compatibilité**: valider compatibilité de base de données avec [V12 de base de données SQL](sql-database-v12-whats-new.md). 
- **Corriger les problèmes de compatibilité, le cas échéant**: si la validation échoue, vous devez corriger les erreurs de validation.  
- **Effectuer la migration** Une fois que votre base de données est compatible, vous pouvez utiliser une ou plusieurs méthodes pour effectuer la migration. 

SQL Server fournit plusieurs méthodes pour accomplir chacune de ces tâches. Cet article fournit une vue d’ensemble des méthodes disponibles pour chaque tâche. Le diagramme suivant illustre les étapes et les méthodes.

  ![Diagramme de migration VSSSDT](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)
  
 > [AZURE.NOTE] Pour migrer une base de données SQL Server, y compris Microsoft Access, Sybase, MySQL Oracle et DB2 à base de données SQL Azure, voir [Assistant Migration SQL Server](http://blogs.msdn.com/b/ssma/).

## <a name="database-migration-tools-test-sql-server-database-compatibility-with-sql-database"></a>Outils de migration de base de données testent de la compatibilité de base de données SQL Server avec la base de données SQL

Pour tester les problèmes de compatibilité de base de données SQL avant de commencer le processus de migration de base de données, utilisez une des méthodes suivantes :

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Conseiller de mise à niveau](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

- [SQL Server Data Tools pour Visual Studio (« SSDT »)](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md): SSDT utilise les plus récentes règles de compatibilité pour détecter incompatibilité V12 de base de données SQL. Si l’incompatibilité n’est détectée, vous pouvez corriger les problèmes détectés directement dans cet outil. Cette méthode est la méthode recommandée pour tester et corriger les problèmes de compatibilité V12 de base de données SQL. 
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md): SqlPackage est un utilitaire de ligne de commande qui teste pour les problèmes de compatibilité et génère un rapport contenant des problèmes de compatibilité détectés. Si vous utilisez cet outil, vérifiez que vous utilisez la version la plus récente à utiliser les règles de compatibilité plus récentes. Si des erreurs sont détectées, vous devez utiliser un autre outil pour résoudre les problèmes de compatibilité détectés - SSDT est recommandé.  
- [Assistant Création d’applications exporter la couche des données dans SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md): cet Assistant détecte et signale les erreurs dans l’écran. Si des erreurs sont détectées, vous pouvez continuer et effectuer la migration de base de données SQL. Si des erreurs sont détectées, vous devez utiliser un autre outil pour résoudre les problèmes de compatibilité détectés - SSDT est recommandé.
- [Le Microsoft SQL Server 2016 mise à niveau Advisor Preview](http://www.microsoft.com/download/details.aspx?id=48119): cet outil autonome, qui se trouve actuellement dans l’aperçu, détecte et génère un rapport d’incompatibilité entre ces V12 de base de données SQL. Cet outil n’a pas encore les règles de compatibilité plus récentes. Si aucune erreur n’est détectée, vous pouvez continuer et effectuer la migration de base de données SQL. Si des erreurs sont détectées, vous devez utiliser un autre outil pour résoudre les problèmes de compatibilité détectés - SSDT est recommandé. 
- [L’Assistant Migration SQL Azure (« SAMW »)](sql-database-cloud-migrate-fix-compatibility-issues.md): SAMW est un outil codeplex qui utilise les règles de compatibilité 11 de base de données SQL Azure pour détecter incompatibilité V12 de base de données SQL Azure. Si l’incompatibilité n’est détectée, certains problèmes peuvent être résolus directement dans cet outil. Cet outil possible incompatibilité qui ne dois-je pas être corrigé. Il a été le première base de données SQL Azure migration outil d’assistance disponible et est activement pris en charge par la Communauté SQL Server. En outre, cet outil sont habilités à effectuer la migration à partir de l’outil de lui-même. 

## <a name="fix-database-migration-compatibility-issues"></a>Corriger les problèmes de compatibilité de migration de base de données

Si des problèmes de compatibilité sont détectées, vous devez les corriger avant de procéder à la migration de base de données SQL Server. Il existe un large éventail de problèmes de compatibilité que vous pouvez rencontrer, selon les deux dans la version de SQL Server dans la base de données source et la complexité de la base de données que vous effectuez la migration. Les anciennes versions de SQL Server comporter plusieurs problèmes de compatibilité. Utilisez les ressources suivantes, en plus d’une recherche sur Internet ciblée à l’aide de votre moteur de recherche de choix :

- [Fonctionnalités de base de données SQL Server non pris en charge dans la base de données SQL Azure](sql-database-transact-sql-information.md)
- [Fonctionnalité de moteur de base de données abandonnées dans SQL Server 2016](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
- [Fonctionnalité de moteur de base de données abandonnées dans SQL Server 2014](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
- [Fonctionnalité de moteur de base de données abandonnées dans SQL Server 2012](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
- [Fonctionnalité de moteur de base de données abandonnées dans SQL Server 2008 R2](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
- [Fonctionnalité de moteur de base de données abandonnées dans SQL Server 2005](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Outre les recherches sur Internet et l’utilisation de ces ressources, utilisez les [forums de la Communauté MSDN SQL Server](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver) ou [StackOverflow](http://stackoverflow.com/).

Pour résoudre les problèmes détectés, utilisez un des outils de migration de base de données suivants :

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

- Utilisez [SQL Server Data Tools pour Visual Studio (« SSDT »)](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md): pour utiliser SSDT, vous importez votre schéma de base de données dans SQL Server Data Tools pour Visual Studio « SSDT ») et générer le projet pour un déploiement V12 de base de données SQL. Résoudre tous les problèmes de compatibilité détectés dans SSDT. Lorsque vous avez terminé, vous synchronisez les modifications dans la base de données source (ou une copie de la base de données source. SSDT est actuellement la méthode recommandée pour tester et corriger les problèmes de compatibilité V12 de base de données SQL. Suivez le lien d’une [étape par étape à l’aide de SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md).
- Utiliser [SQL Server Management Studio (« SSMS »)](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md): pour utiliser SSMS, vous exécutez commandes Transact-SQL pour corriger les erreurs détectées à l’aide d’un autre outil. Cette méthode est principalement pour les utilisateurs expérimentés de modifier le schéma de base de données directement dans la base de données source. 
- Utiliser [L’Assistant Migration SQL Azure (« SAMW »)](sql-database-cloud-migrate-fix-compatibility-issues.md): pour utiliser SAMW, vous générez un script Transact-SQL à partir de la base de données source. L’Assistant transforme le script, si possible, afin que le schéma compatible avec la V12 de base de données SQL. Lorsque vous avez terminé, SAMW pouvez vous connecter à V12 de base de données SQL pour exécuter le script. Cet outil analyse également les fichiers de suivi pour déterminer les problèmes de compatibilité. Le script peut être généré avec le schéma uniquement ou peut inclure des données au format BCP.

## <a name="migrate-a-compatible-sql-server-database-to-sql-database"></a>Migrer une base de données SQL Server compatible base de données SQL

Pour migrer une base de données SQL Server compatible, Microsoft fournit plusieurs méthodes de migration pour différents scénarios. La méthode choisie dépend de votre tolérance de temps d’arrêt, la taille et la complexité de votre base de données SQL Server et votre connectivité dans le cloud Microsoft Azure.  

> [AZURE.SELECTOR]
- [Assistant Migration SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Exporter vers un fichier à DOS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Importer depuis un fichier à DOS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Transactions réplication](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

Pour choisir votre méthode de migration, la première question à poser est si vous pouvez vous permettre de prendre la base de données se déconnecter de production lors de la migration. Migrer une base de données pendant des transactions actives peut entraîner des incohérences de base de données et la base de données possible. Il existe plusieurs méthodes pour mettre en veille une base de données, à partir de la désactivation de la connectivité des clients à la création d’un [instantané de base de données](https://msdn.microsoft.com/library/ms175876.aspx).

Pour migrer très peu de temps, utilisez [la réplication de transactions SQL Server](sql-database-cloud-migrate-compatible-using-transactional-replication.md) si votre base de données répond à la configuration requise pour la réplication de transactions. Si vous pouvez vous permettre une interruption de service ou vous effectuez une migration de test d’une base de données de production pour la migration d’une version ultérieure, vous pouvez une des trois méthodes suivantes :

- [L’Assistant Migration SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md): pour les petites et moyennes bases de données, la migration d’une compatible SQL Server 2005 ou version ultérieure de base de données est aussi simple qu’exécutant le [Déploiement d’une base de données à l’Assistant de base de données Microsoft Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) dans SQL Server Management Studio.
- [Exporter vers un fichier à DOS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) , puis sur [Importer depuis un fichier à DOS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md): Si vous avez problèmes de connectivité (aucune connectivité, faible bande passante ou des problèmes de délai d’expiration) et pour les moyennes et grandes bases de données, utilisez un fichier [à DOS](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) . Avec cette méthode, vous exportez le schéma de SQL Server et les données dans un fichier à DOS. Vous ensuite importer le fichier à DOS dans SQL de base de données à l’aide de l’Assistant Exporter les données couche Application dans SQL Server Management Studio ou l’utilitaire d’invite de commandes [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) .
- Utiliser conjointement à DOS et BCP : utiliser un fichier [à DOS](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) et [BCP](https://msdn.microsoft.com/library/ms162802.aspx) des bases de données volumineux pour obtenir parallélisation supérieure pour améliore les performances, bien qu’avec plus complexe. Avec cette méthode, déplacer le schéma et les données séparément.
 - [Exporter le schéma uniquement vers un fichier à DOS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md).
 - [Importer le schéma uniquement à partir du fichier à DOS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) dans la base de données SQL.
 - Utilisez [BCP](https://msdn.microsoft.com/library/ms162802.aspx) pour extraire les données dans les fichiers plats, puis [charge parallèle](https://technet.microsoft.com/library/dd425070.aspx) ces fichiers dans une base de données SQL Azure.

     ![SQL Server migration de base de données : migration de base de données SQL dans le cloud.](./media/sql-database-cloud-migrate/01SSMSDiagram_new.png)

## <a name="next-steps"></a>Étapes suivantes

- [L’aperçu de mise à niveau Advisor Microsoft SQL Server 2016](http://www.microsoft.com/download/details.aspx?id=48119)
- [Version la plus récente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Version la plus récente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

##<a name="additional-resources"></a>Ressources supplémentaires

- [V12 de base de données SQL](sql-database-v12-whats-new.md)
[Transact-SQL partiellement ou non prises en charge des fonctions](sql-database-transact-sql-information.md)
- [Migrer des bases de données SQL Server à l’aide de l’Assistant Migration SQL Server](http://blogs.msdn.com/b/ssma/)
