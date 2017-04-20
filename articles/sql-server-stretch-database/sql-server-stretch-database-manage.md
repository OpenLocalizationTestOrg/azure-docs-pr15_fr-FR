<properties
    pageTitle="Gérer et résoudre les problèmes de base de données étirement | Microsoft Azure"
    description="Découvrez comment gérer et résoudre les problèmes de base de données étirement."
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
    ms.date="06/27/2016"
    ms.author="douglasl"/>

# <a name="manage-and-troubleshoot-stretch-database"></a>Gérer et résoudre les problèmes de base de données étirement

Pour gérer et résoudre les problèmes de base de données étirer, utilisez les outils et les méthodes décrites dans cette rubrique.

## <a name="manage-local-data"></a>Gérer les données locales

### <a name="LocalInfo"></a>Obtenir des informations sur les bases de données locales et les tables activées pour la base de données étirement
Ouvrez le catalogue affichages **sys.databases** et **sys.tables** pour afficher des informations sur étirement\-activé les tables et les bases de données SQL Server. Pour plus d’informations, voir [sys.databases (Transact-SQL)](https://msdn.microsoft.com/library/ms178534.aspx) et [sys.tables (Transact-SQL)](https://msdn.microsoft.com/library/ms187406.aspx).

Pour afficher la quantité d’espace un étirement\-à l’aide de table activée dans SQL Server, exécutez l’instruction suivante.

 ```tsql
USE <Stretch-enabled database name>;
GO
EXEC sp_spaceused '<Stretch-enabled table name>', 'true', 'LOCAL_ONLY';
GO
 ```
## <a name="manage-data-migration"></a>Gérer la migration de données

### <a name="check-the-filter-function-applied-to-a-table"></a>Vérifiez la fonction filtre appliquée à un tableau
Ouvrir la vue de catalogue **sys.remote\_données\_archive\_tables** et vérifiez la valeur de la **filtre\_prédicat** colonne pour identifier la fonction à l’aide de base de données étirement pour sélectionner des lignes à déplacer. Si la valeur est null, l’intégralité du tableau est éligible à migrer. Pour plus d’informations, voir [sys.remote_data_archive_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx) et [Sélectionner des lignes pour migrer en utilisant une fonction de filtre](sql-server-stretch-database-predicate-function.md).

### <a name="Migration"></a>Vérifier l’état de la migration
Sélectionnez tâches **| Étirer | Moniteur** pour une base de données dans SQL Server Management Studio pour contrôler la migration des données dans l’Analyseur de base de données étirement. Pour plus d’informations, voir [moniteur et résoudre les problèmes de migration de données (base de données étirement)](sql-server-stretch-database-monitor.md).

Ou bien, ouvrez la vue de gestion dynamique **sys.dm\_db\_rda\_migration\_état** pour afficher le nombre de lignes de données et lots ont été migrés.

### <a name="Firewall"></a>Résoudre les problèmes de migration des données
Pour corriger les suggestions, voir [moniteur et résoudre les problèmes de migration de données (base de données étirement)](sql-server-stretch-database-monitor.md).

## <a name="manage-remote-data"></a>Gérer les données distantes

### <a name="RemoteInfo"></a>Obtenir des informations sur les bases de données distantes et tables utilisés par étirement de base de données
Ouvrir les vues de catalogue **sys.remote\_données\_archive\_bases de données** et **sys.remote\_données\_archive\_tableaux** à consulter les informations sur les bases de données distantes et les tables dans lequel les données migrées sont stockées. Pour plus d’informations, voir [sys.remote_data_archive_databases (Transact-SQL)](https://msdn.microsoft.com/library/dn934995.aspx) et [sys.remote_data_archive_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

Pour afficher la quantité d’espace utilise une table compatibles avec étirement dans Azure, exécutez l’instruction suivante.

 ```tsql
USE <Stretch-enabled database name>;
GO
EXEC sp_spaceused '<Stretch-enabled table name>', 'true', 'REMOTE_ONLY';
GO
 ```

### <a name="delete-migrated-data"></a>Supprimer des données a été déplacées  
Si vous voulez supprimer des données qui ont déjà été migrées vers Azure, suivez les étapes décrites dans [sys.sp_rda_reconcile_batch](https://msdn.microsoft.com/library/mt707768.aspx).

## <a name="manage-table-schema"></a>Gérer le schéma de table

### <a name="dont-change-the-schema-of-the-remote-table"></a>Ne modifiez pas le schéma de la table distante
Ne pas modifier le schéma d’une table Azure distante associée à une table SQL Server configurée pour étirement de base de données. En particulier, ne modifiez pas le nom ou le type de données d’une colonne. La fonctionnalité de base de données étirement hypothèses différentes à propos du schéma de la table distante en ce qui concerne le schéma de la table SQL Server. Si vous modifiez le schéma distant, base de données étirement cesse de fonctionner pour le tableau modifié.

### <a name="reconcile-table-columns"></a>Rapprocher les colonnes d’un tableau  
Si vous avez accidentellement supprimé des colonnes de la table distante, exécutez **sp_rda_reconcile_columns** pour ajouter des colonnes à la table distante qui existent dans l’étirement\-activé table SQL Server, mais pas dans la table distante. Pour plus d’informations, voir [sys.sp_rda_reconcile_columns](https://msdn.microsoft.com/library/mt707765.aspx).  

  > [!IMPORTANT] Lorsque **sp_rda_reconcile_columns** recrée colonnes que vous avez accidentellement supprimé à partir de la table distante, il ne restaure pas les données qui figuraient auparavant dans les colonnes supprimées.

**sp_rda_reconcile_columns** ne supprime pas les colonnes de la table distante qui existent dans la table distante mais pas dans l’étirement\-activé table SQL Server. Si des colonnes dans la table Azure distante n’existent plus dans l’étirement\-activé SQL Server table, ces colonnes supplémentaires n’empêchent pas de base de données étirement fonctionne normalement. Vous pouvez éventuellement supprimer manuellement des colonnes supplémentaires.  

## <a name="manage-performance-and-costs"></a>Gérer les performances et les coûts  

### <a name="troubleshoot-query-performance"></a>Résoudre les problèmes de performances des requêtes
Requêtes qui incluent étirement\-tables activées devront exécuter plus lentement qu’avant les tables ont été activés pour étirement. Si les performances des requêtes diminuent considérablement, passez en revue les problèmes suivants.

-   Est votre serveur Azure dans une région géographique différente à votre serveur SQL Server ? Configurer votre serveur Azure pour être dans la même région géographique que votre SQL Server pour réduire la latence du réseau.

-   Conditions de votre réseau peuvent avoir altérées. Contactez votre administrateur réseau pour plus d’informations sur les problèmes de récentes ou défaillances.

### <a name="increase-azure-performance-level-for-resource-intensive-operations-such-as-indexing"></a>Augmenter le niveau de performance Azure pour la ressource\-intensives opérations telles que l’indexation
Lorsque vous voulez créer, reconstruire ou réorganiser un index sur une table qui est configuré pour l’étirement de base de données et que vous envisagez de lourds interroger des données a été déplacées dans Azure pendant ce temps, vous pouvez augmenter le niveau de performance de la base de données Azure à distance correspondant pendant la durée de l’opération. Pour plus d’informations sur les niveaux de performances et les prix, voir [Tarifs de base de données SQL Server étirement](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### <a name="you-cant-pause-the-sql-server-stretch-database-service-on-azure"></a>Vous ne pouvez pas interrompre le service de base de données SQL Server étirer sur Azure  
 Assurez-vous que vous sélectionnez les performances appropriée et le niveau de prix. Si vous augmentez le niveau de performance temporairement pour une ressource\-opération intensive, restaurez-la à un niveau précédent une fois l’opération terminée. Pour plus d’informations sur les niveaux de performances et les prix, voir [Tarifs de base de données SQL Server étirement](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).  

## <a name="change-the-scope-of-queries"></a>Modifier l’étendue de requêtes  
 Requêtes sur étirement\-tables activées retournent des données locales et à distance par défaut. Vous pouvez modifier l’étendue de requêtes pour toutes les requêtes par tous les utilisateurs, ou uniquement pour une seule requête par un administrateur.  

### <a name="change-the-scope-of-queries-for-all-queries-by-all-users"></a>Modifier l’étendue de requêtes pour toutes les requêtes par tous les utilisateurs  
 Pour modifier l’étendue de toutes les requêtes par tous les utilisateurs, exécutez la procédure stockée **sys.sp_rda_set_query_mode**. Vous pouvez réduire l’étendue pour que les données locales de la requête, désactivez toutes les requêtes ou restaurer le paramètre par défaut. Pour plus d’informations, voir [sys.sp_rda_set_query_mode](https://msdn.microsoft.com/library/mt703715.aspx).  

### <a name="queryHints"></a>Modifier l’étendue de requêtes pour une seule requête par un administrateur  
 Pour modifier l’étendue d’une seule requête par un membre du rôle db_owner, ajoutez la * *WITH \( REMOTE_DATA_ARCHIVE_OVERRIDE = *valeur* \)** indicateur de requête à l’instruction SELECT. L’indicateur de requête REMOTE_DATA_ARCHIVE_OVERRIDE peut avoir les valeurs suivantes.  
 -   **LOCAL_ONLY**. Interroger que les données locales.  

 -   **REMOTE_ONLY**. Interroger des données à distance uniquement.  

 -   **STAGE_ONLY**. Seules les données de la table dans laquelle les étapes de base de données étirement lignes éligibles à la migration et conserve les lignes migrés pour la période spécifiée après la migration de la requête. Cette option de requête est le seul moyen de la table intermédiaire de la requête.  

Par exemple, la requête suivante renvoie uniquement les résultats locaux.  

 ```tsql  
 USE <Stretch-enabled database name>;
 GO
 SELECT * FROM <Stretch_enabled table name> WITH (REMOTE_DATA_ARCHIVE_OVERRIDE = LOCAL_ONLY) WHERE ... ;
 GO
```  

## <a name="adminHints"></a>Vérifiez les suppressions et mises à jour administratives  
 Par défaut, vous ne pouvez pas mettre à jour ou supprimer des lignes qui sont éligibles pour la migration ou de lignes qui ont déjà été migrés, dans un étirement\-activé table. Lorsque vous devez résoudre un problème, un membre du rôle db_owner peuvent exécuter une opération de mise à jour ou supprimer en ajoutant le * *WITH \( REMOTE_DATA_ARCHIVE_OVERRIDE = *valeur* \)** indicateur de requête à la déclaration. L’indicateur de requête REMOTE_DATA_ARCHIVE_OVERRIDE peut avoir les valeurs suivantes.  
 -   **LOCAL_ONLY**. Mettre à jour ou supprimer des données locales.  

 -   **REMOTE_ONLY**. Mettre à jour ou supprimer uniquement les données distantes.  

 -   **STAGE_ONLY**. Mettre à jour ou supprimer uniquement les données de la table dans laquelle les étapes de base de données étirement lignes éligibles à la migration et conserve les lignes migrés pour la période spécifiée après la migration.  

## <a name="see-also"></a>Voir aussi

[Base de données étirement moniteur](sql-server-stretch-database-monitor.md)

[Bases de données compatibles avec étirement de sauvegarde](sql-server-stretch-database-backup.md)

[Restaurer des bases de données compatibles avec étirement](sql-server-stretch-database-restore.md)
