<properties
   pageTitle="Gérer les données historiques dans Tables temporelles avec la stratégie de rétention | Microsoft Azure"
   description="Découvrez comment utiliser une stratégie de rétention temporel pour conserver les données historiques sous votre contrôle."
   services="sql-database"
   documentationCenter=""
   authors="bonova"
   manager="drasumic"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sql-database"
   ms.date="10/12/2016"
   ms.author="bonova"/>

#<a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>Gérer les données historiques dans Tables temporelles avec la stratégie de rétention

Tables temporelles peuvent augmenter la taille de la base de données plus de tables régulières, surtout si vous conservez les données historiques pour une période de temps plus longue. Par conséquent, la stratégie de rétention pour les données historiques est un aspect important de planification et la gestion du cycle de vie de chaque table temporel. Tables temporelles dans la base de données SQL Azure fournis avec mécanisme de rétention facile à utiliser qui vous permet d’effectuer cette tâche.

Conservation de l’historique temporel peut être configuré au niveau de la table individuelle, qui permet aux utilisateurs de créer vieillissants flexibles stratégies. Application de la rétention temporelle est simple : elle ne requiert qu’un seul paramètre à être définie au tableau création ou un schéma différent.

Après avoir défini une stratégie de rétention, base de données SQL Azure commencera à vérifier régulièrement s’il existe des lignes historiques qui sont éligibles pour le nettoyage automatique des données. Identification des lignes correspondantes et leur suppression de la table historique se produisent en toute transparence, dans la tâche d’arrière-plan est planifiée et exécutée par le système. Condition d’âge pour les lignes de tableau d’historique est activée en fonction de la colonne représentant la fin de période SYSTEM_TIME. Si la période de rétention, par exemple, est défini sur six mois, lignes éligibles pour le nettoyage de la table répondent à la condition suivante :

````
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
````

Dans l’exemple ci-dessus, nous avons supposé que **ValidTo** colonne correspond à la fin de période SYSTEM_TIME.

##<a name="how-to-configure-retention-policy"></a>Comment configurer la stratégie de rétention ?

Avant de configurer stratégie de rétention pour une table temporelle, vérifiez tout d’abord si temporelle rétention historique est activée *au niveau de la base de données*.

````
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
````

Indicateur de base de données **is_temporal_history_retention_enabled** est défini sur activé par défaut, mais les utilisateurs peuvent les modifier avec instruction ALTER DATABASE. Il est également automatiquement défini sur désactivé après [restauration instantanée](sql-database-point-in-time-restore-portal.md) opération. Pour activer le nettoyage de rétention de l’historique temporel pour votre base de données, exécutez l’instruction suivante :

````
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
````

> [AZURE.IMPORTANT] Vous pouvez configurer la rétention pour les tableaux temporel même si **is_temporal_history_retention_enabled** est désactivé, mais le nettoyage automatique pour les lignes anciennes ne sera pas déclenché dans ce cas.


Stratégie de rétention est configurée lors de la création de table en spécifiant la valeur du paramètre HISTORY_RETENTION_PERIOD :

````
CREATE TABLE dbo.WebsiteUserInfo
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH
 (
     SYSTEM_VERSIONING = ON
     (
        HISTORY_TABLE = dbo.WebsiteUserInfoHistory,
        HISTORY_RETENTION_PERIOD = 6 MONTHS
     )
 );
````

Base de données SQL Azure permet de spécifier la période de rétention à l’aide des unités de temps différente : jours, semaines, mois et années. Si HISTORY_RETENTION_PERIOD est omis, rétention infinie est utilisée. Vous pouvez également utiliser explicitement mot-clé infini.

Dans certains cas, vous souhaiterez peut-être configurer rétention après la création de la table, ou pour modifier précédemment configuré valeur. Dans ce cas utiliser instruction ALTER TABLE :

````
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
````

> [AZURE.IMPORTANT]  Paramétrer le SYSTEM_VERSIONING OFF *ne conserve pas* de valeur période de rétention. Définir SYSTEM_VERSIONING ON sans HISTORY_RETENTION_PERIOD spécifié explicitement résultats dans la période de rétention infini.

Pour consulter l’état actuel de la stratégie de rétention, utilisez la requête suivante qui joint indicateur d’activation temporel rétention au niveau de la base de données avec des périodes de rétention pour des tables individuelles :

````
SELECT DB.is_temporal_history_retention_enabled,
SCHEMA_NAME(T1.schema_id) AS TemporalTableSchema,
T1.name as TemporalTableName,  SCHEMA_NAME(T2.schema_id) AS HistoryTableSchema,
T2.name as HistoryTableName,T1.history_retention_period,
T1.history_retention_period_unit_desc
FROM sys.tables T1  
OUTER APPLY (select is_temporal_history_retention_enabled from sys.databases
where name = DB_NAME()) AS DB
LEFT JOIN sys.tables T2   
ON T1.history_table_id = T2.object_id WHERE T1.temporal_type = 2
````


##<a name="how-sql-database-deletes-aged-rows"></a>La base de données SQL supprime âgés de lignes ?

Le processus de nettoyage dépend de la mise en page de l’index de la table historique. Il est important de noter *seules les tables historique avec un index cluster (arborescence ou columnstore) peuvent avoir configuré une stratégie de rétention déterminée*. Une tâche en arrière-plan est créée pour effectuer un nettoyage de données historiques pour tous les tableaux temporelles avec période de rétention déterminée.
Logique de nettoyage de l’index rowstore (arborescence) supprime ligne obsolètes dans les segments volumineux (jusqu'à 10 Ko) en réduisant la pression sur journal de base de données et sous-système e/s. Bien que la logique de nettoyage utilise requis index arbre-B, ordre des suppressions pour les lignes antérieures à la période de rétention ne peut pas être garantie bien. Par conséquent, *ne prennent pas toute dépendance sur l’ordre de nettoyage dans vos applications*.

La tâche de nettoyage de disque pour la columnstore groupé supprime entiers [groupes de lignes](https://msdn.microsoft.com/library/gg492088.aspx) à la fois (en général contenir 1 million de lignes chaque), qui est très efficace, particulièrement quand les données historiques sont générées à un rythme haut.

![Rétention columnstore groupé](./media/sql-database-temporal-tables-retention-policy/cciretention.png)


Compression de données excellente et rétention efficace nettoyage rend groupées columnstore index choix idéal pour les scénarios lorsque votre charge de travail génère rapidement grande quantité de données historiques. Ce modèle est généralement utilisé pour intensive [charges de traitement des transactions qui utilisent des tables temporelles](https://msdn.microsoft.com/library/mt631669.aspx) pour suivi des modifications et l’audit, analyse des tendances ou réception de données IoT.

##<a name="index-considerations"></a>Remarques sur les index

La tâche de nettoyage de disque pour les tables avec index cluster rowstore nécessite qu’index tout d’abord la colonne qui correspond à la fin de période SYSTEM_TIME. Si ce index n’existe pas, il se peut que vous ne pourrez pas à configurer une période de rétention déterminée :

*Message 13765, niveau 16, état 1 <br> </br> période de rétention déterminée Échec de la définition de table temporel version système 'temporalstagetestdb.dbo.WebsiteUserInfo', car le tableau d’historique 'temporalstagetestdb.dbo.WebsiteUserInfoHistory' ne contient pas d’index groupé requis. Créez des index arbre-B commençant par la colonne qui correspond à la fin de SYSTEM_TIME ou un columnstore groupé période, dans le tableau d’historique.*

Il est important de noter que la table de l’historique par défaut créée par base de données SQL Azure déjà a groupé index, qui est conforme pour la stratégie de rétention. Si vous essayez de supprimer cet index sur une table avec la période de rétention déterminée, opération échoue avec l’erreur suivante :

*Message 13766, niveau 16, état 1 <br> </br> Impossible de supprimer l’index cluster 'WebsiteUserInfoHistory.IX_WebsiteUserInfoHistory' car il est utilisé pour le nettoyage automatique des données anciennes. Pensez à définir HISTORY_RETENTION_PERIOD à infini table temporel système version correspondante si vous avez besoin de supprimer cet index.*

Nettoyage de l’index columnstore groupé fonctionne au mieux si lignes historiques sont insérés dans l’ordre croissant (ordonnée à la fin de période de la colonne), qui est toujours le cas lorsque le tableau d’historique est rempli en mode exclusif par le mécanisme SYSTEM_VERSIONIOING. Si les lignes dans le tableau d’historique ne sont pas triées en fin de période de la colonne (qui peut se produire si vous avez migré les données historiques existantes), vous devez recréer columnstore groupé index en haut de l’index arbre-B rowstore correctement triée, pour optimiser les performances.

Évitez de reconstruction de l’index columnstore groupé dans le tableau d’historique avec la période de rétention déterminée, car il peut changer le classement dans les groupes de lignes naturellement imposées par l’opération système-contrôle de version. Si vous devez reconstruire index columnstore groupé sur le tableau d’historique, ce faire, créer à nouveau en haut de respecter les exigences index arbre-B, en conservant le classement dans les rowgroups nécessaires pour le nettoyage de données normal. La même approche convient si vous créez table temporel avec le tableau historique existant qui a groupé index de colonne sans ordre garantie des données :

````
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
````

Lors de la période de rétention déterminée est configuré pour le tableau d’historique avec l’index columnstore groupé, vous ne pouvez pas créer des index non cluster arborescence supplémentaires sur cette table :

````
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
````

Une tentative d’exécution au-dessus instruction échouera avec l’erreur suivante :

*Message 13772, niveau 16, état 1 <br> </br> Impossible de créer index non cluster sur une table historique temporel 'WebsiteUserInfoHistory' car il ne dispose période de rétention déterminée et index groupé columnstore défini.*

##<a name="querying-tables-with-retention-policy"></a>Interrogation de tables avec la stratégie de rétention

Toutes les requêtes sur la table temporelle historiques lignes qui correspondent aux stratégie de rétention déterminée, afin d’éviter des résultats inattendus et n’est pas cohérente, étant donné que les lignes anciennes peuvent être supprimés par la tâche de nettoyage de disque, *à tout moment dans le temps et dans l’ordre arbitraire*filtrer automatiquement.

L’image suivante montre le plan de requête pour une requête simple :

````
SELECT * FROM dbo.WebsiteUserInfo FROM SYSTEM_TIME ALL;
````

Le plan de requête inclut supplémentaire filtre appliqué à la fin de la colonne période (ValidTo) de l’opérateur « Groupé le balayage d’Index » dans le tableau d’historique (en surbrillance). Cet exemple suppose que cette période de rétention 1 mois a été définie sur la table WebsiteUserInfo.

![Filtre de requête de rétention](./media/sql-database-temporal-tables-retention-policy/queryexecplanwithretention.png)

Cependant, si vous interrogez directement tableau d’historique, vous pouvez voir les lignes qui sont plus anciens que rétention spécifiée période, mais sans aucune garantie pour les résultats de la requête répétitifs. L’image suivante montre plan d’exécution de la requête dans le tableau d’historique sans filtres supplémentaires :

![Interrogation de l’historique sans filtre de rétention](./media/sql-database-temporal-tables-retention-policy/queryexecplanhistorytable.png)

Ne comptez pas votre logique métier sur la lecture de tableau d’historique au-delà de la période de rétention que vous pouvez obtenir des résultats inattendus ou n’est pas cohérente. Il est recommandé d’utiliser des requêtes temporelles avec clause FOR SYSTEM_TIME pour l’analyse des données dans des tables temporelles.

##<a name="point-in-time-restore-considerations"></a>Pointez sur dans considérations sur Restaurer les heures

Lorsque vous créez la nouvelle base de données en [restaurant la base de données existante à un point spécifique dans le temps](sql-database-point-in-time-restore-portal.md), il a rétention temporelle désactivée au niveau de la base de données. (indicateur**is_temporal_history_retention_enabled** défini sur désactivé). Cette fonctionnalité permet d’examiner toutes les lignes historiques à restaurer, sans risque que qu’anciennes lignes sont supprimées avant d’atteindre les interroger. Vous pouvez l’utiliser pour *inspecter les données historiques au-delà de période de rétention configurée*.

Supposons qu’une table temporelle comporte rétention mois une période spécifiée. Si votre base de données a été créé dans le niveau de Service Premium, vous ne pourrez pas créer de copie de base de données avec l’état de base de données vers le haut à 35 jours dans le passé. Qui efficacement afin de pouvoir analyser historiques lignes qui sont jusqu'à 65 jours par interroger le tableau d’historique directement.

Si vous voulez activer le nettoyage de rétention temporel, exécutez l’instruction Transact-SQL suivante après le point restauration dans le temps :

````
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
````

##<a name="next-steps"></a>Étapes suivantes

Pour apprendre à utiliser les Tables temporelles dans vos applications consultez [Prise en main des Tables temporelles dans la base de données SQL Azure](sql-database-temporal-tables.md).

Visitez le site Channel 9 pour entendre un [réussite client réel temporel implémentation](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) et regarder une [démonstration temporelle en direct](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

Pour plus d’informations sur les Tables temporelles, passez en revue [documentation MSDN](https://msdn.microsoft.com/library/dn935015.aspx).
