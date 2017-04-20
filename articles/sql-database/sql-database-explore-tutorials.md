<properties
   pageTitle="Explorer les didacticiels de base de données SQL Azure | Microsoft Azure"
   description="Découvrez les fonctionnalités et les fonctionnalités de base de données SQL"
   keywords=""
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
   ms.workload="data-management"
   ms.date="08/24/2016"
   ms.author="carlrab"/>
   
# <a name="explore-azure-sql-database-tutorials"></a>Explorer les didacticiels de base de données SQL Azure

Les liens ci-dessous vous dirigent vers une présentation de chaque zone de fonctionnalité répertoriées et un didacticiel étape par Démarrer simple pour chaque zone. Pour les Démarrages rapides à portée de solution qui illustrent l’utilisation de la base de données SQL dans une solution complète basée sur des scénarios réels, voir [Démarrage rapide d’Azure SQL de base de données Solution](sql-database-solution-quick-starts.md).

## <a name="using-sql-server-management-studio"></a>Avec SQL Server Management Studio

Dans les didacticiels suivants, vous allez découvrir à l’aide de SQL Server Management Studio pour administrer et de la requête de base de données SQL Azure.


> [AZURE.IMPORTANT] Il est recommandé d’utiliser toujours la dernière version de Management Studio doit pour rester synchronisé avec les mises à jour Microsoft Azure et base de données SQL. [Mettre à jour de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


| Didacticiel  | Description  |
|---|---|---|
| [Se connecter à la base de données SQL Azure à l’aide d’une connexion d’accès au niveau du serveur principale](sql-database-get-started-security.md#connect-to-azure-sql-database-using-a-server-level-principal-login)| Dans ce didacticiel, vous allez apprendre à vous connecter à la base de données SQL Azure à l’aide d’une connexion d’accès au niveau du serveur principale.|
| [Se connecter à la base de données SQL Azure en tant qu’utilisateur](sql-database-get-started-security.md#connect-to-azure-sql-database-as-a-user) | Dans ce didacticiel, vous allez apprendre à vous connecter à une base de données SQL Azure à l’aide d’un compte d’utilisateur au niveau de la base de données.|
||||

## <a name="elastic-pools"></a>Pools élastiques

Dans les didacticiels suivants, vous allez apprendre sur l’utilisation [des pools élastiques](sql-database-elastic-pool.md) pour gérer les objectifs de performance pour plusieurs bases de données qui ont des modèles d’utilisation inattendus et largement variés.

| Didacticiel  | Description  |
|---|---|---|
| [Créer un pool élastique](sql-database-elastic-pool-create-portal.md) | Dans ce didacticiel, vous allez apprendre à créer un regroupement scalable de bases de données SQL Azure. |
| [Surveiller une base de données élastique](sql-database-elastic-pool-manage-portal.md#elastic-database-monitoring) | Dans ce didacticiel, vous allez apprendre à surveiller une base de données élastique individuel pour des problèmes potentiels. |
| [Ajouter une alerte pour une ressource pool](sql-database-elastic-pool-manage-portal.md#add-an-alert-to-a-pool-resource) | Dans ce didacticiel, vous découvrez comment ajouter des règles aux ressources envoient des messages électroniques à des personnes ou des chaînes d’alerte aux points de terminaison URL lors de la ressource atteint un seuil de l’utilisation que vous avez configurées. |
| [Déplacer une base de données dans un pool élastique](sql-database-elastic-pool-manage-portal.md#move-a-database-into-an-elastic-pool) | Dans ce didacticiel, vous découvrez comment déplacer une base de données dans un pool élastique. |
| [Retirer une base de données d’un pool élastique](sql-database-elastic-pool-manage-portal.md#move-a-database-out-of-an-elastic-pool) | Dans ce didacticiel, vous allez apprendre à retirer une base de données d’un pool élastique. |
| [Modifier les paramètres de performances d’un pool](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) | Dans ce didacticiel, vous allez apprendre à ajuster les limites de stockage et de performances d’un pool. |
||||

## <a name="elastic-database-jobs"></a>Tâches de base de données élastique

Dans les didacticiels suivants, vous allez découvrir utilisant [des tâches de base de données élastique](sql-database-elastic-jobs-overview.md).

| Didacticiel  | Description  |
|---|---|---|
| [Prise en main des outils de base de données élastique](sql-database-elastic-scale-get-started.md) | Dans ce didacticiel, vous allez apprendre à utiliser les fonctionnalités des outils de base de données élastique à l’aide d’une application sharded simple. |
| [Prise en main travaux élastique de base de données SQL Azure](sql-database-elastic-jobs-getting-started.md)  | Dans ce didacticiel, vous découvrez comment pour comment créer et gérer des travaux qui gérer un groupe de bases de données liées.  | 
||||

## <a name="elastic-queries"></a>Requêtes élastiques

Dans les didacticiels suivants, vous allez découvrir en cours d’exécution [des requêtes élastiques](sql-database-elastic-query-overview.md). 

| Didacticiel  | Description  |
|---|---|---|
| [Requêtes sur une base de données (sharded) partitionnée horizontalement)](sql-database-elastic-query-getting-started.md) | Dans ce didacticiel, vous apprenez à créer des rapports à partir de toutes les bases de données dans une base de données (sharded) partitionnée horizontalement à l’aide de la [requête élastique](sql-database-elastic-query-overview.md) |
| [Requêtes sur une base de données partitionnée verticalement)](sql-database-elastic-query-getting-started-vertical.md#create-database-objects) | Dans ce didacticiel, vous apprenez à créer des rapports à partir de toutes les bases de données dans un verticalement base de données à l’aide de la [requête élastique](sql-database-elastic-query-overview.md) |
| [Migrer une base de données existante à l’horizontale](sql-database-elastic-convert-to-use-elastic-tools.md)| Dans ce didacticiel, vous apprenez à mettre à l’échelle de la base de données (partagé) SQL Azure horizontalement. |
||||

## <a name="performance-optimization"></a>Optimisation des performances

Dans les didacticiels suivants, vous allez découvrir optimisation des [performances des bases de données unique](sql-database-performance-guidance.md). Pour optimiser les performances des bases de données, voir [pools élastiques](#elastic-pools).

| Didacticiel  | Description  |
|---|---|---|
| [Modifier le niveau de service niveau et les performances de votre base de données](sql-database-scale-up.md#change-the-service-tier-and-performance-level-of-your-database) | Dans ce didacticiel, vous allez apprendre à l’échelle vers le haut ou mettre à l’échelle vers le bas les performances d’une base de données SQL Azure à l’aide de niveaux de service. |
| [Aperçu de performances requête SQL de base de données Advisor](sql-database-performance.md#performance-overview) | Dans ce didacticiel, vous découvrez comment ouvrir et utiliser un aperçu de SQL de base de données Advisor requête performances.|
| [Recommandations sur les performances Conseiller de base de données SQL](sql-database-advisor.md#viewing-recommendations) | Dans ce didacticiel, vous allez apprendre à afficher et appliquer des recommandations sur les performances Conseiller de base de données SQL. |
| [Passez en revue processeur supérieur par d’autres programmes requêtes](sql-database-query-performance.md#review-top-cpu-consuming-queries)| Dans ce didacticiel, vous allez apprendre à utiliser SQL de base de données Advisor requête performances Aperçu pour consulter processeur supérieur par d’autres programmes requêtes.|
| [Affichage des détails de la requête individuelle](sql-database-query-performance.md#viewing-individual-query-details)| Dans ce didacticiel, vous découvrez comment utiliser SQL de base de données Advisor requête performances un aperçu pour afficher les détails des performances de requête individuelle.|
||||

## <a name="sql-database-migration-and-archive"></a>Archivage et Migration de base de données SQL 

Dans les didacticiels suivants, vous apprendrez à [migrer une base de données SQL Server existante à la base de données SQL Azure](sql-database-cloud-migrate.md).

| Didacticiel  | Description  |
|---|---|---|
| [Détecter les problèmes de compatibilité liés à l’aide de SQL Server Data Tools pour Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md#detecting-compatibility-issues-using-sql-server-data-tools-for-visual-studio) | Dans ce didacticiel, vous découvrez comment utiliser SQL Server Data Tools pour Visual Studio pour déterminer la compatibilité de base de données SQL Azure. |
| [Résolution des problèmes de compatibilité liés à l’aide de SQL Server Data Tools pour Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt#fixing-compatibility-issues-using-sql-server-data-tools-for-visual-studio) | Dans ce didacticiel, vous découvrez comment utiliser SQL Server Data Tools pour Visual Studio pour corriger les problèmes de compatibilité de base de données SQL Azure. |
| [Déterminer la compatibilité de base de données SQL à l’aide de SqlPackage.exe](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md#using-sqlpackageexe) | Dans ce didacticiel, vous allez apprendre à utiliser l’utilitaire de ligne de commande SQLPackage.exe pour déterminer la compatibilité de base de données SQL Azure.|
| [Déterminer la compatibilité de base de données SQL à l’aide de SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md#using-sql-server-management-studio) |Dans ce didacticiel, vous découvrez comment utiliser SQL Server Management Studio pour déterminer la compatibilité de base de données SQL Azure.|
| [Migrer la base de données SQL Server à SQL de base de données à l’aide de déploiement d’une base de données à l’Assistant de base de données Microsoft Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md#use-the-deploy-database-to-microsoft-azure-database-wizard) | Dans ce didacticiel, vous allez apprendre à migrer une base de données SQL Server compatible à base de données SQL Azure à l’aide de la base de données Microsoft Azure de base de données Assistant déployer dans SQL Server Management Studio.
| [Exporter une base de données SQL Server vers un fichier à DOS à l’aide de SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) | Dans ce didacticiel, vous allez apprendre comment exporter une base de données SQL Server compatible vers un fichier à DOS à l’aide de l’Assistant Exportation d’une Application de niveau données dans SQL Server Management Studio.|
| [Exporter une base de données SQL Server vers un fichier à DOS à l’aide de SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md) | Dans ce didacticiel, vous allez apprendre comment exporter une base de données SQL Server compatible vers un fichier à DOS à l’aide de l’utilitaire SQLPackage.exe.|
| [Importer un fichier à DOS dans base de données SQL Azure à l’aide de SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) | Dans ce didacticiel, vous allez apprendre à importer une base de données de la base de données SQL Azure à partir d’un fichier à DOS à l’aide de l’Assistant Exportation d’une Application de niveau données dans SQL Server Management Studio. |
| [Importer un fichier à DOS dans base de données SQL Azure à l’aide de SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md#import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage) | Dans ce didacticiel, vous allez apprendre à importer une base de données de la base de données SQL Azure à partir d’un fichier à DOS à l’aide de l’utilitaire SQLPackage. |
| [Importer un fichier à DOS dans base de données SQL Azure à l’aide du portail Azure](sql-database-import.md) | Dans ce didacticiel, vous allez apprendre à importer une base de données de la base de données SQL Azure à partir d’un fichier à DOS qui est stocké dans un blob Azure à l’aide du portail Azure.|
| [Importer un fichier à DOS dans base de données SQL Azure à l’aide de PowerShell](sql-database-import-powershell.md) | Dans ce didacticiel, vous allez apprendre à importer une base de données de la base de données SQL Azure à partir d’un fichier à DOS à l’aide de PowerShell.|
| [Archiver une base de données SQL Azure à l’aide du portail Azure](sql-database-export.md#export-your-database) | Dans ce didacticiel, vous allez apprendre à archiver une base de données SQL Azure vers un fichier à DOS à l’aide du portail Azure. |
| [Archiver une base de données SQL Azure à l’aide de PowerShell](sql-database-export-powershell.md) | Dans ce didacticiel, vous allez apprendre à archiver une base de données SQL Azure vers un fichier à DOS à l’aide de PowerShell. |
| [Copier une base de données SQL Azure à l’aide du portail Azure](sql-database-copy.md#copy-your-sql-database) | Dans ce didacticiel, vous allez apprendre à copier une base de données SQL Azure à l’aide du portail Azure. |
| [Copier une base de données SQL Azure à l’aide de PowerShell](sql-database-copy-powershell#copy-your-sql-database) | Dans ce didacticiel, vous allez apprendre à copier une base de données SQL Azure à l’aide de PowerShell. |
| [Copier une base de données SQL Azure à l’aide de Transact-SQL](sql-database-copy-transact-sql.md#copy-your-sql-database) | Dans ce didacticiel, vous allez apprendre à copier une base de données SQL Azure à l’aide de Transact-SQL. |
||||

##<a name="develop"></a>Développer

Dans les didacticiels suivants, vous allez découvrir [Développement de base de données SQL](sql-database-develop-overview.md) et l’utilisation de [bibliothèques de connexion](sql-database-libraries.md).

| Didacticiel  | Description  |
|---|---|---|
| [Se connecter à la base de données SQL à l’aide de .NET (c#)](sql-database-develop-dotnet-simple.md) | Dans ce didacticiel, vous allez apprendre à vous connecter à une base de données SQL Azure à l’aide de c#. |
| [Se connecter à la base de données SQL à l’aide de Java](sql-database-develop-java-simple.md) | Dans ce didacticiel, vous allez apprendre à vous connecter à une base de données SQL Azure à l’aide de Java. |
| [Se connecter à la base de données SQL à l’aide de Node.js](sql-database-develop-nodejs-simple.md) | Dans ce didacticiel, vous allez apprendre à vous connecter à une base de données SQL Azure à l’aide de Node.js. |
| [Se connecter à la base de données SQL à l’aide de PHP](sql-database-develop-php-simple.md) | Dans ce didacticiel, vous allez apprendre à vous connecter à une base de données SQL Azure à l’aide de PHP. |
| [Se connecter à la base de données SQL à l’aide de Python](sql-database-develop-python-simple.md) | Dans ce didacticiel, vous allez apprendre à vous connecter à une base de données SQL Azure à l’aide de Python. |
| [Se connecter à la base de données SQL à l’aide de Ruby](sql-database-develop-ruby-simple.md) | Dans ce didacticiel, vous allez apprendre à vous connecter à une base de données SQL Azure à l’aide de Ruby. |
||||
 
## <a name="database-access"></a>Base de données Access

Dans les didacticiels suivants, vous apprendrez à [créer et gérer les connexions et les utilisateurs](sql-database-manage-logins.md).

| Didacticiel  | Description  |
|---|---|---|
| [Créer une règle de pare-feu au niveau du serveur de base de données SQL Azure à l’aide du portail Azure](sql-database-configure-firewall-settings.md)  | Dans ce didacticiel, vous allez apprendre à configurer un pare-feu au niveau du serveur de base de données SQL à l’aide du portail Azure.  |
| [Créer une règle de pare-feu au niveau de la base de données à l’aide de Transact-SQL](sql-database-configure-firewall-settings-tsql.md#database-level-firewall-rules) | Dans ce didacticiel, vous allez apprendre à créer une règle de pare-feu au niveau de la base de données à l’aide de Transact-SQL.|
| [Gérer les règles de pare-feu au niveau du serveur à l’aide de Transact-SQL](sql-database-configure-firewall-settings-tsql.md#manage-server-level-firewall-rules-through-transact-sql) | Dans ce didacticiel, vous allez apprendre à gérer un pare-feu au niveau du serveur de base de données SQL Azure à l’aide de Transact-SQL.|
| [Gérer les règles de pare-feu au niveau du serveur à l’aide de PowerShell](sql-database-configure-firewall-settings-powershell.md#manage-firewall-rules-using-powershell) | Dans ce didacticiel, vous allez apprendre à gérer un pare-feu au niveau du serveur de base de données SQL Azure à l’aide de PowerShell.|
| [Gérer les règles de pare-feu au niveau du serveur à l’aide de l’API REST](sql-database-configure-firewall-settings-rest.md#manage-firewall-rules-using-the-service-management-rest-api) | Dans ce didacticiel, vous allez apprendre à gérer un pare-feu au niveau du serveur de base de données SQL Azure à l’aide de l’API réinitialiser.|
| [Se connecter à la base de données SQL Azure à l’aide d’une connexion d’accès au niveau du serveur principale](sql-database-get-started-security.md#connect-to-azure-sql-database-using-a-server-level-principal-login)| Dans ce didacticiel, vous allez apprendre à vous connecter à la base de données SQL Azure à l’aide d’une connexion d’accès au niveau du serveur principale.|
| [Accorder l’accès de base de données à une connexion] (sql-database-manage-logins.md#granting-database-access-to-a-login() | Dans ce didacticiel, vous allez apprendre à accorder l’accès à une connexion au niveau du serveur de base de données.|
| [Créer un nouvel utilisateur de base de données à l’aide de SSMS](sql-database-get-started-security.md#create-new-database-user-using-ssms) | Dans ce didacticiel, vous allez apprendre à créer un nouvel utilisateur de base de données dans une base de données existante à l’aide de SSMS.|
| [Octroyer des autorisations db_owner de nouvelle base de données utilisateur](sql-database-get-started-security.md#grant-new-database-user-dbowner-permissions) | Dans ce didacticiel, vous allez apprendre à accorder des autorisations des utilisateurs db_owner une base de données existante.|
| [Se connecter à la base de données SQL Azure en tant qu’utilisateur](sql-database-get-started-security.md#connect-to-azure-sql-database-as-a-user) | Dans ce didacticiel, vous allez apprendre à vous connecter à une base de données SQL Azure à l’aide d’un compte d’utilisateur au niveau de la base de données.|
||||


## <a name="data-security"></a>Sécurité des données

Dans les didacticiels suivants, vous allez découvrir [la sécurisation de données de base de données SQL Azure](sql-database-security.md). 

| Didacticiel  | Description  |
|---|---|---|
| [Activer la détection de menace pour votre base de données à l’aide du portail Azure](sql-database-threat-detection-get-started.md#set-up-threat-detection-for-your-database) | Dans ce didacticiel, vous allez apprendre à configurer la détection des menaces dans le portail Azure pour votre base de données.|
| [Banque d’informations sécurisé en utilisant des données sensibles toujours chiffrées](sql-database-always-encrypted-azure-key-vault.md) | Dans ce didacticiel, vous allez utiliser l’Assistant toujours chiffrées pour sécuriser les données sensibles dans une base de données SQL Azure.|
| [Sécuriser les données senstive à l’aide de chiffrement de données transparent](https://msdn.microsoft.com/library/dn948096.aspx)| Dans ce didacticiel, vous allez apprendre à utiliser le chiffrement de données transparent pour sécuriser les données senstive.|
| [Chiffrer une colonne de données](https://msdn.microsoft.com/library/ms179331.aspx)| Dans ce didacticiel, vous découvrez comment chiffrer une colonne de données à l’aide de Transact-SQL.|
| [Configurer le masquage de l’échange dynamique de données](sql-database-dynamic-data-masking-get-started.md#set-up-dynamic-data-masking-for-your-database-using-the-azure-portal)  | Dans ce didacticiel, vous allez apprendre à configurer le masquage de l’échange dynamique de données pour votre base de données SQL Azure. |
||||

## <a name="business-continuity-and-query-scale-out"></a>Continuité des activités et requête horizontale

Dans les didacticiels suivants, vous apprendrez à utiliser [Geo restaurer et la réplication de Geo Active](sql-database-business-continuity.md) à reccover des erreurs, de continuité des activités et des requêtes horizontale.

| Didacticiel  | Description  |
|---|---|---|
| [Restaurer une base de données SQL Azure à un point antérieur dans le temps grâce au portail d’Azure](sql-database-point-in-time-restore-portal.md)| Dans ce didacticiel, vous allez apprendre à restaurer votre base de données à un point antérieur dans le temps à l’aide du portail Azure.|
| [Restaurer une base de données SQL Azure à un point antérieur dans le temps avec PowerShell](sql-database-point-in-time-restore-powershell.md) | Dans ce didacticiel, vous découvrez comment restaurer votre base de données à un point antérieur dans le temps à l’aide de PowerShell|
| [Restaurer une base de données SQL Azure supprimé à l’aide du portail Azure](sql-database-restore-deleted-database-portal.md) | Dans ce didacticiel, vous découvrez comment restaurer une base de données supprimé à l’aide du portail Azure.|
| [Restaurer une base de données SQL Azure supprimé à l’aide de la session PowerShell](sql-database-restore-deleted-database-powershell.md) | Dans ce didacticiel, vous découvrez comment restaurer une base de données supprimé à l’aide de PowerShell.|
| [Configurer la Geo-réplication de base de données SQL Azure à l’aide du portail Azure](sql-database-geo-replication-portal.md)| Dans ce didacticiel, vous allez apprendre à configurer la réplication de Geo Active à l’aide du portail Azure.|
| [Configurer Geo réplication de base de données SQL Azure à l’aide de PowerShell](sql-database-geo-replication-powershell.md)| Dans ce didacticiel, vous découvrez comment configurer la réplication Geo Active à l’aide de PowerShell.|
| [Configurer la Geo-réplication de base de données SQL Azure à l’aide de Transact-SQL](sql-database-geo-replication-transact-sql.md)| Dans ce didacticiel, vous découvrez comment configurer la réplication Geo Active à l’aide de Transact-SQL.|
| [Lancer un basculement planifié ou de base de données SQL Azure à l’aide du portail Azure](sql-database-geo-replication-failover-portal.md) | Dans ce didacticiel, vous découvrez comment basculer vers un réplica secondaire répliquées geo à l’aide du portail Azure.|
| [Lancer un basculement planifié ou de base de données SQL Azure à l’aide de PowerShell](sql-database-geo-replication-failover-powershell.md) | Dans ce didacticiel, vous découvrez comment basculer vers un réplica secondaire répliquées geo à l’aide de PowerShell.|
| [Lancer un basculement planifié ou de base de données SQL Azure à l’aide de Transact-SQL](sql-database-geo-replication-failover-transact-sql.md) | Dans ce didacticiel, vous découvrez comment basculer vers un réplica secondaire répliquées geo à l’aide de Transact-SQL.|
||||

## <a name="data-sync"></a>Synchronisation de données

Dans ce didacticiel, vous allez découvrir la [Synchronisation de données](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf).

| Didacticiel  | Description  |
|---|---|---| 
| [Mise en route de la synchronisation de données SQL Azure (Preview)](sql-database-get-started-sql-data-sync.md)  | Dans ce didacticiel, vous découvrez les principes de base de la synchronisation de données SQL Azure à l’aide du portail classique Azure. |
||||

## <a name="next-steps"></a>Étapes suivantes

[Explorer les Démarrages rapides de Solution de base de données SQL Azure](sql-database-solution-quick-starts.md)
