<properties
   pageTitle="Surveiller votre charge de travail à l’aide de DMV | Microsoft Azure"
   description="Apprenez à surveiller votre charge de travail à l’aide de DMV."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/08/2016"
   ms.author="sonyama;barbkess"/>

# <a name="monitor-your-workload-using-dmvs"></a>Surveiller votre charge de travail à l’aide de DMV

Cet article décrit comment utiliser des vues de gestion dynamique (DMV) pour surveiller votre charge de travail et d’observer l’exécution des requêtes dans le magasin de données SQL Azure.

## <a name="permissions"></a>Autorisations

Pour interroger les DMV dans cet article, vous devez l’état de base de données d’affichage ou de contrôle d’autorisation. En règle générale, l’octroi de l’état de base de données d’affichage est l’autorisation par défaut qu’il est beaucoup plus restrictive.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Surveiller les connexions

Toutes les connexions à SQL Data Warehouse sont enregistrées dans [sys.dm_pdw_exec_sessions][].  Cette vue contient les 10 000 dernières connexions.  L’ID de session est la clé primaire et assignés de façon séquentielle pour chaque nouvelle ouverture de session.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>Surveiller l’exécution des requêtes

Toutes les requêtes exécutées sur SQL Data Warehouse sont enregistrées dans [sys.dm_pdw_exec_requests][].  Cette vue contient les 10 000 dernières requêtes exécutées.  Request_id identifie chaque requête aurait et est la clé primaire pour cette vue.  Request_id assignés de façon séquentielle pour chaque nouvelle requête et est précédés QID, ce qui signifie ID de requête.  Interroger cette vue pour un ID de session donné affiche toutes les requêtes pour une connexion donnée.

>[AZURE.NOTE] Les procédures stockées utilisent plusieurs ID demander.  ID de demande est affectées dans un ordre séquentiel. 

Voici les étapes à suivre pour analyser les plans d’exécution des requêtes et les heures pour une requête.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>ÉTAPE 1 : Identifier la requête que vous souhaitez examiner

```sql
-- Monitor active queries
SELECT * 
FROM sys.dm_pdw_exec_requests 
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 * 
FROM sys.dm_pdw_exec_requests 
ORDER BY total_elapsed_time DESC;

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

Dans les résultats de requête précédent, **Notez l’ID de demande** de la requête que vous voulez examiner.

Requêtes dans l’état **suspendu** sont en file d’attente en raison des limites de la concurrence. Ces requêtes apparaissent également dans la requête attend sys.dm_pdw_waits avec un type de UserConcurrencyResourceType. Voir [Gestion des accès concurrentiels au et la charge de travail][] pour plus d’informations sur les limites de la concurrence. Les requêtes peuvent également attendre pour d’autres raisons telles que les verrous d’objet.  Si votre requête est en attente pour une ressource, voir [requêtes Investigating en attente de ressources][] plus loin dans cet article.

Pour simplifier la recherche d’une requête dans la table sys.dm_pdw_exec_requests, utilisez [l’étiquette][] pour affecter un commentaire à votre requête peut être recherché dans la vue sys.dm_pdw_exec_requests.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;
```

### <a name="step-2-investigate-the-query-plan"></a>ÉTAPE 2 : Examinez le plan de requête

Utiliser l’ID de demande d’extraire un plan SQL (DSQL) distribué de la requête à partir de [sys.dm_pdw_request_steps][].

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Lorsqu’un plan DSQL prend plus longtemps que prévu, la raison peut être un plan complexe avec plusieurs étapes DSQL ou qu’une seule étape met beaucoup de temps.  Si le plan est plusieurs étapes avec plusieurs opérations de déplacement, envisagez d’optimiser vos répartitions de table pour réduire le déplacement de données. L’article de la [distribution de tables][] explique pourquoi les données doivent être déplacées pour résoudre une requête et explique certaines stratégies de distribution pour réduire le déplacement de données.

Pour plus d’informations sur une seule opération, la colonne *type_opération* de l’étape de requête longue approfondir l’analyse et notez l' **Index étape**:

- Poursuivre l’étape 3 a pour les **opérations SQL**: OnOperation, RemoteOperation, ReturnOperation.
- Poursuivre l’étape 3 b pour les **opérations de déplacement de données**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### <a name="step-3a-investigate-sql-on-the-distributed-databases"></a>ÉTAPE 3 : étudier SQL sur les bases de données distribués

Utiliser l’ID de demande et de l’Index étape pour extraire les détails de [sys.dm_pdw_sql_requests][], qui contient des informations sur l’exécution de l’étape de requête sur toutes les bases de données distribuées.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Lorsque l’étape de requête s’exécute, [PDW_SHOWEXECUTIONPLAN DBCC][] peut servir à récupérer le plan estimé SQL Server à partir du cache de plan de SQL Server pour l’étape en cours d’exécution d’une distribution particulière.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-3b-investigate-data-movement-on-the-distributed-databases"></a>ÉTAPE 3 b : examinez le déplacement de données sur les bases de données distribués

Utilisez l’ID de demande et de l’Index étape pour récupérer des informations sur une étape de déplacement de données en cours d’exécution sur chaque distribution à partir de [sys.dm_pdw_dms_workers][].

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

- Consultez la colonne *total_elapsed_time* pour voir si une diffusion particulière est-elle beaucoup plus longue que d’autres personnes pour le déplacement de données.
- Pour la distribution longue, consultez la colonne *rows_processed* pour voir si le nombre de lignes est déplacé à partir de cette distribution est sensiblement plus grand que d’autres personnes. Si c’est le cas, cela peut indiquer inclinaison de vos données sous-jacentes.

Si la requête est en cours d’exécution, [PDW_SHOWEXECUTIONPLAN DBCC][] peut servir à récupérer le plan estimé SQL Server à partir du cache de plan SQL Server pour l’étape SQL en cours d’exécution au sein d’une distribution particulière.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>
## <a name="monitor-waiting-queries"></a>Surveiller les requêtes en attente

Si vous découvrez que votre requête ne fait pas l’avancement car il est en attente pour une ressource, voici une requête qui affiche toutes les ressources de qu'une requête est en attente de.

```sql
-- Find queries 
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

Si la requête est en attente activement sur les ressources à partir d’une autre requête, l’état sera **AcquireResources**.  Si la requête comporte toutes les ressources nécessaires, l’état sera **autorisé**.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur DMV, consultez [vues système][] .
Pour plus d’informations sur les meilleures pratiques, consultez [Data Warehouse SQL meilleures pratiques][]

<!--Image references-->

<!--Article references-->
[Manage overview]: ./sql-data-warehouse-overview-manage.md
[Meilleures pratiques SQL Data Warehouse]: ./sql-data-warehouse-best-practices.md
[Vues système]: ./sql-data-warehouse-reference-tsql-system-views.md
[Distribution de tables]: ./sql-data-warehouse-tables-distribute.md
[Gestion des accès concurrentiels au et la charge de travail]: ./sql-data-warehouse-develop-concurrency.md
[Rechercher des requêtes en attente de ressources]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[Sys.dm_pdw_dms_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[Sys.dm_pdw_exec_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[Sys.dm_pdw_exec_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[Sys.dm_pdw_request_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[Sys.dm_pdw_sql_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx
[ÉTIQUETTE]: https://msdn.microsoft.com/library/ms190322.aspx
