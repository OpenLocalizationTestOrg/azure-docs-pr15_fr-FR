<properties
   pageTitle="Surveillance à l’aide des vues de gestion dynamique de la base de données SQL Azure | Microsoft Azure"
   description="Apprenez à détecter et diagnostiquer les problèmes de performances courants à l’aide des vues de gestion dynamiques pour analyser la base de données SQL Microsoft Azure."
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/20/2016"
   ms.author="carlrab"/>

# <a name="monitoring-azure-sql-database-using-dynamic-management-views"></a>Base de données SQL Azure à l’aide des vues de gestion dynamiques de surveillance

Base de données SQL Microsoft Azure Active un sous-ensemble des vues de gestion dynamique pour diagnostiquer les problèmes de performances, qui peuvent être provoquées par bloqués ou longue requêtes, des engorgements de ressources, mauvaise plans de requête et ainsi de suite. Cette rubrique fournit des informations sur la façon de détecter les problèmes de performances courants à l’aide des vues de gestion dynamiques.

Base de données SQL prend en charge partiellement les trois catégories de vues de gestion dynamique :

- Vues de gestion dynamique liés à la base de données.
- Vues de gestion dynamique liés à l’exécution.
- Vues de gestion dynamique relative à la transaction.

Pour plus d’informations sur les vues de gestion dynamique, voir [fonctions (Transact-SQL) et des vues de gestion dynamique](https://msdn.microsoft.com/library/ms188754.aspx) dans la documentation en ligne de SQL Server.

## <a name="permissions"></a>Autorisations

Dans la base de données SQL, requête d’une vue de gestion dynamique nécessite des autorisations de **L’état de base de données d’affichage** . L’autorisation **Afficher l’état de base de données** renvoie des informations sur tous les objets de base de données active.
Pour accorder l’autorisation **Afficher l’état de base de données** à un utilisateur de base de données spécifique, exécutez la requête suivante :

```GRANT VIEW DATABASE STATE TO database_user; ```

Dans une instance de local SQL Server, vues de gestion dynamiques renvoient des informations d’état de serveur. Dans la base de données SQL, ils renvoyant des informations concernant votre logique base de données active uniquement.

## <a name="calculating-database-size"></a>Calcul de la taille de la base de données

La requête suivante renvoie la taille de votre base de données (en mégaoctets) :

```
-- Calculates the size of the database.
SELECT SUM(reserved_page_count)*8.0/1024
FROM sys.dm_db_partition_stats;
GO
```

La requête suivante renvoie la taille des objets individuels (en mégaoctets) dans votre base de données :

```
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>Contrôle des connexions

Vous pouvez utiliser l’affichage [sys.dm_exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) pour récupérer des informations sur les connexions établie à un serveur de base de données SQL Azure spécifique et les détails de chaque connexion. En outre, le mode [sys.dm_exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) est utile pour extraire des informations sur toutes les connexions de l’utilisateur actif et tâches internes.
La requête suivante extrait des informations sur la connexion en cours :

```
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [AZURE.NOTE] Lorsque vous exécutez **l’association** et des **vues sys.dm_exec_sessions**, si vous avez l’autorisation **Afficher l’état de base de données** sur la base de données, voir sessions tout en cours d’exécution sur la base de données ; Sinon, vous verrez uniquement la session active.

## <a name="monitoring-query-performance"></a>Analyse des performances de requête

Lente ou longue exécution des requêtes peut consommer des ressources système significatives. Cette section montre comment utiliser des vues de gestion dynamique pour détecter les quelques problèmes de performances de requête courants. Une référence plus ancienne, mais toujours utile pour la résolution des problèmes, est l’article de [Résolution des problèmes de performances dans SQL Server 2008](http://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) sur Microsoft TechNet.

### <a name="finding-top-n-queries"></a>Recherche de requêtes top N

L’exemple suivant renvoie des informations sur les requêtes de cinq supérieure classés selon des temps processeur moyen. Cet exemple regroupe les requêtes en fonction de leur hachage de requête, afin que les requêtes logiquement équivalentes sont regroupées par leur utilisation des ressources cumulé.

```
SELECT TOP 5 query_stats.query_hash AS "Query Hash",
    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
    MIN(query_stats.statement_text) AS "Statement Text"
FROM
    (SELECT QS.*,
    SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
    ((CASE statement_end_offset
        WHEN -1 THEN DATALENGTH(ST.text)
        ELSE QS.statement_end_offset END
            - QS.statement_start_offset)/2) + 1) AS statement_text
     FROM sys.dm_exec_query_stats AS QS
     CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
GROUP BY query_stats.query_hash
ORDER BY 2 DESC;
```

### <a name="monitoring-blocked-queries"></a>Surveillance des requêtes bloqués

Requêtes lentes ou longue peuvent contribuer à la consommation excessive des ressources et être la conséquence de requêtes bloqués. La cause du blocage peut être conception d’application médiocre, plans de requête incorrect, l’absence d’index utiles et ainsi de suite. Vous pouvez utiliser l’affichage sys.dm_tran_locks pour obtenir des informations sur l’activité de verrouillage en cours dans votre base de données SQL Azure. Par exemple code, voir [sys.dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) dans la documentation en ligne de SQL Server.

### <a name="monitoring-query-plans"></a>Plans de requête de surveillance

Un plan de requête inefficace peut également augmenter l’utilisation de l’UC. L’exemple suivant utilise la vue [sys.dm_exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) pour déterminer quelle requête utilise l’UC plus cumulé.

```
SELECT
    highest_cpu_queries.plan_handle,
    highest_cpu_queries.total_worker_time,
    q.dbid,
    q.objectid,
    q.number,
    q.encrypted,
    q.[text]
FROM
    (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
    ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
    CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
ORDER BY highest_cpu_queries.total_worker_time DESC;
```

## <a name="see-also"></a>Voir aussi

[Introduction à la base de données SQL](sql-database-technical-overview.md)
