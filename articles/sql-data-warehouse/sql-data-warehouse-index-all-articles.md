<properties
    pageTitle="Toutes les rubriques pour le service SQL Data Warehouse | Microsoft Azure"
    description="Table de toutes les rubriques pour le service Azure nommée SQL Data Warehouse qui existent sur http://azure.microsoft.com/documentation/articles/, titre et description."
    services="sql-data-warehouse"
    documentationCenter=""
    authors="barbkess"
    manager="jhubbard"
    editor="MightyPen"/>

<tags
    ms.service="sql-data-warehouse"
    ms.workload="sql-data-warehouse"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="barbkess"/>


# <a name="all-topics-for-azure-sql-data-warehouse-service"></a>Toutes les rubriques pour le service d’entrepôt de données SQL Azure

Cette rubrique répertorie toutes les rubriques qui s’applique directement au service de Azure **SQL Data Warehouse** . Vous pouvez rechercher cette page Web pour les mots clés à l’aide de **Ctrl + F**, pour trouver les sujets qui intéressent.




## <a name="new"></a>Nouveau

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 1 | [Sauvegardes SQL Data Warehouse](sql-data-warehouse-backups.md) | Obtenir des informations à propos des sauvegardes intégrés de la base de données SQL Data Warehouse qui vous permettent de restaurer une SQL Azure Data Warehouse sur un point de restauration ou une région géographique différente. |


## <a name="updated-articles-sql-data-warehouse"></a>Articles mis à jour, Data Warehouse SQL

Cette section répertorie les articles qui ont été mis à jour récemment, où la mise à jour a été grandes ou significative. Pour chaque article mis à jour, un extrait de code approximative du texte ajouté démarque est affichée. Les articles ont été mis à jour dans la plage de dates de **2016-08-22** à **2016-10-05**.

| &nbsp; | Article | Texte mis à jour, l’extrait de code | Mise à jour lorsque |
| --: | :-- | :-- | :-- |
| 2 | [Charger les données depuis le stockage blob Azure dans SQL Data Warehouse (PolyBase)](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) | /-Pour effectuer le suivi des octets et des fichiers r.command SELECT, s.request_id, r.status, count (distinct input_name) en tant que nbr_files, additionner (s.bytes_processed) / 1024/1024 en tant que gb_processed de sys.dm_pdw_exec_requests r jointure interne sys.dm_pdw_dms_external_work s sur r.request_id = s.request_id où r. étiquette = ' SACT : charge responsable de la sécurité. DimProduct ' OR r. étiquette = ' SACT : charge responsable de la sécurité. GROUP BY r.command des FactOnlineSales, s.request_id, r.status ORDER BY nbr_files desc, gb_processed desc ;  | 2016-09-07 |
| 3 | [Restaurer Data Warehouse SQL](sql-data-warehouse-restore-database-overview.md) | **Puis-je restaurer un suspendus data warehouse ?** Pour restaurer un data warehouse qui est mis en pause, vous devez tout d’abord mettez-le en ligne. Une fois que le data warehouse est en ligne, vous avez des points de restauration pour choisir parmi sept jours. **Restaurer à une zone géographique redondants** Si vous utilisez le stockage geo redondantes, vous pouvez restaurer le data warehouse à votre centre de données pour dans une région géographique différente. Le data warehouse est restauré à partir de la dernière sauvegarde quotidienne. **Restaurer la barre de planning** Vous pouvez restaurer une base de données à tout moment restaurer depuis les sept derniers jours. Instantanés toutes les heures quatre à huit et sont disponibles pour les sept jours. Lorsqu’un instantané est plu de sept jours, il arrive à expiration et son point de restauration n’est plus disponible. **Restaurer les coûts** Les frais de stockage pour le magasin de données restaurées sont facturée au taux le stockage Azure Premium. Si vous interrompez un restauré data warehouse, vous êtes chargé de stockage au taux d’espace de stockage Azure Premium. L’avantage de la pause est que vous n’êtes pas frais | 2016-09-29 |





## <a name="get-started"></a>Prise en main

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 4 | [Authentification SQL Azure Data Warehouse](sql-data-warehouse-authentication.md) | Authentification Azure Active Directory (DAS) et SQL Server Azure SQL Data Warehouse. |
| 5 | [Meilleures pratiques pour le magasin de données SQL Azure](sql-data-warehouse-best-practices.md) | Recommandations et des pratiques recommandées que vous devez savoir que vous développement des solutions pour Azure SQL Data Warehouse. Ils vous aideront à être réussie. |
| 6 | [Pilotes pour Data Warehouse SQL Azure](sql-data-warehouse-connection-strings.md) | Chaînes de connexion et les pilotes SQL Data Warehouse |
| 7 | [Se connecter à Data Warehouse SQL Azure](sql-data-warehouse-connect-overview.md) | Comment trouver le serveur de chaîne de connexion et de nom pour votre vers Azure SQL Data Warehouse |
| 8 | [Analyser des données avec apprentissage automatique Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md) | Utilisez Azure Machine d’apprentissage pour créer une machine prédictive apprentissage du modèle basé sur des données stockées dans le magasin de données SQL Azure. |
| 9 | [Requête SQL Azure Data Warehouse (sqlcmd)](sql-data-warehouse-get-started-connect-sqlcmd.md) | Interrogation de magasin de données SQL Azure avec l’utilitaire de ligne de commande sqlcmd. |
| 10 | [Créer une base de données SQL Data Warehouse à l’aide de Transact-SQL (TSQL).](sql-data-warehouse-get-started-create-database-tsql.md) | Découvrez comment créer un magasin de données SQL Azure avec TSQL |
| 11 | [Comment créer une demande d’assistance pour Data Warehouse SQL](sql-data-warehouse-get-started-create-support-ticket.md) | Comment créer une demande d’assistance dans SQL Azure Data Warehouse. |
| 12 | [Charger les données avec des données Azure usine](sql-data-warehouse-get-started-load-with-azure-data-factory.md) | Apprenez à charger les données avec Azure Data Factory |
| 13 | [Charger les données avec PolyBase dans Data Warehouse SQL](sql-data-warehouse-get-started-load-with-polybase.md) | Découvrez quelles sont les PolyBase et comment l’utiliser pour data warehouse scénarios. |
| 14 | [Créer un magasin de données SQL Azure](sql-data-warehouse-get-started-provision.md) | Découvrez comment créer un magasin de données SQL Azure dans le portail Azure |
| 15 | [Créer SQL Data Warehouse à l’aide de PowerShell](sql-data-warehouse-get-started-provision-powershell.md) | Créer SQL Data Warehouse à l’aide de PowerShell |
| 16 | [Visualiser des données avec Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md) | Visualiser des données SQL Data Warehouse avec Power BI |
| 17 | [Requête SQL Azure Data Warehouse (Visual Studio)](sql-data-warehouse-query-visual-studio.md) | Requête SQL Data Warehouse avec Visual Studio. |



## <a name="develop"></a>Développer

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 18 | [Optimisation de transactions pour Data Warehouse SQL](sql-data-warehouse-develop-best-practices-transactions.md) | Meilleures pratiques recommandées sur l’écriture de mises à jour des transactions efficace dans SQL Azure Data Warehouse |
| 19 | [Gestion des accès concurrentiels au et la charge de travail dans Data Warehouse SQL](sql-data-warehouse-develop-concurrency.md) | Comprendre la gestion de la concurrence et la charge de travail dans SQL Azure Data Warehouse pour développer des solutions. |
| 20 | [Créer la Table comme sélectionner (SACT) dans Data Warehouse SQL](sql-data-warehouse-develop-ctas.md) | Conseils pour codage avec la créer une table en tant qu’instruction (SACT) select dans SQL Azure Data Warehouse pour développer des solutions. |
| 21 | [Instructions SQL dynamiques dans Data Warehouse SQL](sql-data-warehouse-develop-dynamic-sql.md) | Conseils pour l’utilisation dynamique SQL Azure SQL Data Warehouse pour développer des solutions. |
| 22 | [Regrouper par options dans Data Warehouse SQL](sql-data-warehouse-develop-group-by-options.md) | Conseils pour l’implémentation de groupe par options dans SQL Azure Data Warehouse pour développer des solutions. |
| 23 | [Utiliser des étiquettes pour les requêtes instrument dans Data Warehouse SQL](sql-data-warehouse-develop-label.md) | Conseils pour l’utilisation des étiquettes de requêtes instrument dans SQL Azure Data Warehouse pour développer des solutions. |
| 24 | [Boucles dans Data Warehouse SQL](sql-data-warehouse-develop-loops.md) | Conseils pour Transact-SQL boucles et curseurs de remplacement dans SQL Azure Data Warehouse pour développer des solutions. |
| 25 | [Procédures stockées dans Data Warehouse SQL](sql-data-warehouse-develop-stored-procedures.md) | Conseils pour l’implémentation des procédures stockées dans SQL Azure Data Warehouse pour développer des solutions. |
| 26 | [Transactions dans Data Warehouse SQL](sql-data-warehouse-develop-transactions.md) | Conseils pour l’implémentation des transactions dans SQL Azure Data Warehouse pour développer des solutions. |
| 27 | [Schémas définis par l’utilisateur dans Data Warehouse SQL](sql-data-warehouse-develop-user-defined-schemas.md) | Conseils pour l’utilisation des schémas Transact-SQL Azure SQL Data Warehouse pour développer des solutions. |
| 28 | [Affecter des variables dans Data Warehouse SQL](sql-data-warehouse-develop-variable-assignment.md) | Conseils permettant d’affecter des variables Transact-SQL Azure SQL Data Warehouse pour développer des solutions. |
| 29 | [Affichages dans Data Warehouse SQL](sql-data-warehouse-develop-views.md) | Conseils pour l’utilisation d’affichages Transact-SQL Azure SQL Data Warehouse pour développer des solutions. |
| 30 | [Décisions de conception et techniques de codage pour Data Warehouse SQL](sql-data-warehouse-overview-develop.md) | Concepts de développement, décisions de conception, recommandations et techniques de codage pour Data Warehouse SQL. |



## <a name="manage"></a>Gérer

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 31 | [Gérer l’alimentation cluster dans SQL Azure Data Warehouse (vue d’ensemble)](sql-data-warehouse-manage-compute-overview.md) | Échelle de performances des fonctionnalités dans le magasin de données SQL Azure. Évoluer en ajustant DWUs ou interrompre et reprendre cluster ressources pour réduire les coûts. |
| 32 | [Gérer l’alimentation cluster dans SQL Azure Data Warehouse (Azure portal)](sql-data-warehouse-manage-compute-portal.md) | Tâches du portail Azure pour gérer calculent power. Échelle de ressources de calcul en ajustant DWUs. Ou, interrompre et reprendre cluster ressources pour réduire les coûts. |
| 33 | [Gérer l’alimentation cluster dans SQL Azure Data Warehouse (PowerShell)](sql-data-warehouse-manage-compute-powershell.md) | Tâches de PowerShell pour gérer calculent power. Échelle de ressources de calcul en ajustant DWUs. Ou, interrompre et reprendre cluster ressources pour réduire les coûts. |
| 34 | [Gérer la puissance de calcul dans Azure SQL données Warehouse (REST)](sql-data-warehouse-manage-compute-rest-api.md) | Tâches de PowerShell pour gérer calculent power. Échelle de ressources de calcul en ajustant DWUs. Ou, interrompre et reprendre cluster ressources pour réduire les coûts. |
| 35 | [Gérer l’alimentation cluster dans SQL Azure Data Warehouse (T-SQL)](sql-data-warehouse-manage-compute-tsql.md) | Tâches de Transact-SQL (T-SQL) horizontale les performances en ajustant DWUs. Réduire les coûts en redimensionnant précédent pendant les heures de pointe. |
| 36 | [Surveiller votre charge de travail à l’aide de DMV](sql-data-warehouse-manage-monitor.md) | Apprenez à surveiller votre charge de travail à l’aide de DMV. |
| 37 | [Gérer les bases de données dans le magasin de données SQL Azure](sql-data-warehouse-overview-manage.md) | Vue d’ensemble de la gestion des bases de données SQL Data Warehouse. Inclut les outils d’administration, DWUs et performances horizontale, résolution des problèmes de performances des requêtes, définition de stratégies de sécurité et de restaurer une base de données à partir de l’altération des données ou une panne régionale. |
| 38 | [Surveiller les requêtes d’utilisateur dans le magasin de données SQL Azure](sql-data-warehouse-overview-manage-user-queries.md) | Vue d’ensemble des considérations, meilleures pratiques et les tâches pour surveiller les requêtes de l’utilisateur dans le magasin de données SQL Azure |
| 39 | [Restaurer Data Warehouse SQL](sql-data-warehouse-restore-database-overview.md) | Vue d’ensemble des options de restauration de base de données pour restaurer une base de données SQL Azure Data Warehouse. |
| 40 | [Restaurer un SQL Azure Data Warehouse (portail)](sql-data-warehouse-restore-database-portal.md) | Tâches du portail Azure pour restaurer un magasin de données SQL Azure. |
| 41 | [Restaurer un SQL Azure Data Warehouse (PowerShell)](sql-data-warehouse-restore-database-powershell.md) | Tâches de PowerShell pour restaurer un magasin de données SQL Azure. |
| 42 | [Restaurer un SQL Azure Data Warehouse (API REST)](sql-data-warehouse-restore-database-rest-api.md) | Tâches de l’API REST de restauration d’un magasin de données SQL Azure. |



## <a name="tables-and-indexes"></a>Les tables et index

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 43 | [Types de données pour les tables dans Data Warehouse SQL](sql-data-warehouse-tables-data-types.md) | Prise en main des types de données pour les tableaux de magasin de données SQL Azure. |
| 44 | [Distribution de tables SQL Data Warehouse](sql-data-warehouse-tables-distribute.md) | Prise en main à la distribution de tables dans SQL Azure Data Warehouse. |
| 45 | [Indexation tables SQL Data Warehouse](sql-data-warehouse-tables-index.md) | Prise en main table l’indexation en entrepôt de données SQL Azure. |
| 46 | [Vue d’ensemble des tables SQL Data Warehouse](sql-data-warehouse-tables-overview.md) | Mise en route des Tables de magasin de données SQL Azure. |
| 47 | [Tableaux de partition dans Data Warehouse SQL](sql-data-warehouse-tables-partition.md) | Prise en main table partition dans SQL Azure Data Warehouse. |
| 48 | [Gestion des statistiques sur les tables dans Data Warehouse SQL](sql-data-warehouse-tables-statistics.md) | Mise en route des statistiques sur les tables dans SQL Azure Data Warehouse. |
| 49 | [Tables temporaires dans Data Warehouse SQL](sql-data-warehouse-tables-temporary.md) | Prise en main des tables temporaires dans SQL Azure Data Warehouse. |



## <a name="integrate"></a>Intégrer

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 50 | [Utiliser des données Azure usine avec Data Warehouse SQL](sql-data-warehouse-integrate-azure-data-factory.md) | Conseils pour l’utilisation d’usine de données Azure (chargeur) avec Azure SQL Data Warehouse pour développer des solutions. |
| 51 | [Utiliser l’apprentissage Machine Azure avec SQL Data Warehouse](sql-data-warehouse-integrate-azure-machine-learning.md) | Didacticiel pour l’utilisation d’apprentissage automatique Azure avec Azure SQL Data Warehouse pour développer des solutions. |
| 52 | [Utiliser des flux de données Azure Analytique avec Data Warehouse SQL](sql-data-warehouse-integrate-azure-stream-analytics.md) | Conseils pour l’utilisation d’Azure flux Analytique avec Azure SQL Data Warehouse pour développer des solutions. |
| 53 | [Utiliser Power BI avec SQL Data Warehouse](sql-data-warehouse-integrate-power-bi.md) | Conseils pour l’utilisation de Power BI avec Azure SQL Data Warehouse pour développer des solutions. |
| 54 | [Exploiter les autres services avec SQL Data Warehouse](sql-data-warehouse-overview-integrate.md) | Outils et partenaires avec les solutions qui s’intègrent à Data Warehouse SQL.  |



## <a name="load"></a>Chargement

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 55 | [Charger les données depuis le stockage blob Azure dans SQL Azure Data Warehouse (Azure Data Factory)](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md) | Apprenez à charger les données avec Azure Data Factory |
| 56 | [Charger les données depuis le stockage blob Azure dans SQL Data Warehouse (PolyBase)](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) | Découvrez comment utiliser PolyBase pour charger les données depuis le stockage blob Azure dans Data Warehouse SQL. Charger plusieurs tables à partir des données publiques dans le schéma de Contoso au détail Data Warehouse. |
| 57 | [Charger les données à partir de SQL Server dans SQL Azure Data Warehouse (AZCopy)](sql-data-warehouse-load-from-sql-server-with-azcopy.md) | Utilise bcp pour exporter des données à partir de SQL Server vers des fichiers plats, AZCopy pour importer des données à Azure blob storage et PolyBase pour ajouter les données dans Azure SQL Data Warehouse. |
| 58 | [Charger les données à partir de SQL Server dans SQL Azure Data Warehouse (fichiers plats)](sql-data-warehouse-load-from-sql-server-with-bcp.md) | Pour une taille de données de petite taille, utilise bcp pour exporter des données à partir de SQL Server aux fichiers plats et importer les données directement dans le magasin de données SQL Azure. |
| 59 | [Charger les données à partir de SQL Server dans Azure SQL données Warehouse (SSIS)](sql-data-warehouse-load-from-sql-server-with-integration-services.md) | Vous montre comment créer un package SQL Server Integration Services (SSIS) pour déplacer des données d’un large éventail de sources de données vers SQL Data Warehouse. |
| 60 | [Charger les données avec PolyBase dans Data Warehouse SQL](sql-data-warehouse-load-from-sql-server-with-polybase.md) | Utilise bcp pour exporter des données à partir de SQL Server vers des fichiers plats, AZCopy pour importer des données à Azure blob storage et PolyBase pour ajouter les données dans Azure SQL Data Warehouse. |
| 61 | [Guide d’utilisation des PolyBase dans Data Warehouse SQL](sql-data-warehouse-load-polybase-guide.md) | Instructions et recommandations sur l’utilisation de PolyBase dans les scénarios Data Warehouse SQL. |
| 62 | [Charger les exemples de données dans Data Warehouse SQL](sql-data-warehouse-load-sample-databases.md) | Charger les exemples de données dans Data Warehouse SQL |
| 63 | [Charger les données avec bcp](sql-data-warehouse-load-with-bcp.md) | Découvrez quels bcp et comment l’utiliser pour data warehouse scénarios. |
| 64 | [Charger les données dans le magasin de données SQL Azure](sql-data-warehouse-overview-load.md) | Découvrez les scénarios courants pour les données de chargement dans SQL Data Warehouse. Celles-ci incluent l’utilisation PolyBase, stockage d’objets blob Azure, fichiers plats et envoi de disque. Vous pouvez également utiliser les outils tiers. |



## <a name="migrate"></a>Migrer

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 65 | [Migrer votre code SQL pour Data Warehouse SQL](sql-data-warehouse-migrate-code.md) | Conseils pour migrer votre code SQL Azure SQL Data Warehouse pour développer des solutions. |
| 66 | [Migrer vos données](sql-data-warehouse-migrate-data.md) | Conseils pour une migration de vos données vers SQL Azure Data Warehouse pour développer des solutions. |
| 67 | [Utilitaire de Migration de magasin de données (Preview)](sql-data-warehouse-migrate-migration-utility.md) | Migrer vers Data Warehouse SQL. |
| 68 | [Migrer votre schéma vers Data Warehouse SQL](sql-data-warehouse-migrate-schema.md) | Conseils pour migrer votre schéma vers Azure SQL Data Warehouse pour développer des solutions. |
| 69 | [Migrer votre solution vers Data Warehouse SQL](sql-data-warehouse-overview-migrate.md) | Guide de migration pour mettre votre solution sur plateforme Azure SQL Data Warehouse. |



## <a name="partners"></a>Partenaires

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 70 | [Partenaires de SQL Data Warehouse business intelligence](sql-data-warehouse-partner-business-intelligence.md) | Listes des partenaires d’intelligence métier tiers avec les solutions qui prennent en charge SQL Data Warehouse. |
| 71 | [Partenaires d’intégration des données SQL Data Warehouse](sql-data-warehouse-partner-data-integration.md) | Listes des partenaires tiers avec des solutions d’intégration de données qui prennent en charge de magasin de données SQL Azure. |
| 72 | [Partenaires de gestion des données SQL Data Warehouse](sql-data-warehouse-partner-data-management.md) | Listes des partenaires de gestion des données tierces avec les solutions qui prennent en charge SQL Data Warehouse. |



## <a name="reference"></a>Référence

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 73 | [Rubriques de référence pour Data Warehouse SQL](sql-data-warehouse-overview-reference.md) | Liens de contenu de référence pour SQL Data Warehouse. |
| 74 | [Applets de commande PowerShell et des API REST pour Data Warehouse SQL](sql-data-warehouse-reference-powershell-cmdlets.md) | Rechercher les applets de commande PowerShell supérieure pour entrepôt de données SQL Azure, y compris comment interrompre et reprendre une base de données. |
| 75 | [Éléments du langage](sql-data-warehouse-reference-tsql-language-elements.md) | Liste de liens vers contenu de référence pour les éléments de langage Transact-SQL utilisée pour Data Warehouse SQL. |
| 76 | [Rubriques Transact-SQL](sql-data-warehouse-reference-tsql-statements.md) | Liens vers contenu de référence pour les rubriques Transact-SQL utilisé par SQL Data Warehouse. |
| 77 | [Vues système](sql-data-warehouse-reference-tsql-system-views.md) | Liens vers système affiche le contenu pour Data Warehouse SQL. |



## <a name="security"></a>Sécurité

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 78 | [SQL Data Warehouse - clients de niveau inférieur prennent en charge pour les données d’audit et dynamiques Masking](sql-data-warehouse-auditing-downlevel-clients.md) | En savoir plus sur la prise en charge de clients de niveau inférieur Data Warehouse SQL pour l’audit des données |
| 79 | [Audit dans SQL Azure Data Warehouse](sql-data-warehouse-auditing-overview.md) | Prise en main d’audit dans le magasin de données SQL Azure |
| 80 | [Prise en main avec transparente données chiffrement (TDE) dans Data Warehouse SQL](sql-data-warehouse-encryption-tde.md) | Chiffrement transparent des données (TDE) dans Data Warehouse SQL |
| 81 | [Prise en main avec chiffrement Transparent des données (TDE)](sql-data-warehouse-encryption-tde-tsql.md) | Chiffrement transparent des données (TDE) SQL Data Warehouse (T-SQL) |
| 82 | [Sécuriser une base de données SQL Data Warehouse](sql-data-warehouse-overview-manage-security.md) | Conseils pour sécuriser une base de données SQL Azure Data Warehouse pour développer des solutions. |



## <a name="miscellaneous"></a>Divers

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 83 | [Installer Visual Studio 2015 et SSDT pour Data Warehouse SQL](sql-data-warehouse-install-visual-studio.md) | Installer Visual Studio et outils de développement SQL Server (SSDT) pour Data Warehouse SQL Azure |
| 84 | [Migration vers des détails sur le stockage Premium](sql-data-warehouse-migrate-to-premium-storage.md) | Instructions pour la migration un SQL Data Warehouse existant vers le stockage premium |
| 85 | [Prise en main de détection](sql-data-warehouse-security-threat-detection.md) | Comment prendre en main détection des menaces |
| 86 | [Limites de capacité Data Warehouse SQL](sql-data-warehouse-service-capacity-limits.md) | Valeurs maximales de connexions, les bases de données, les tables et les requêtes SQL Data Warehouse. |
| 87 | [Résolution des problèmes de Data Warehouse SQL Azure](sql-data-warehouse-troubleshoot.md) | Résolution des problèmes de Data Warehouse SQL Azure. |

