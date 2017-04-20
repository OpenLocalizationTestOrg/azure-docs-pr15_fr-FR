<properties
   pageTitle="Gestion des accès concurrentiels au et la charge de travail dans SQL Data Warehouse | Microsoft Azure"
   description="Comprendre la gestion de la concurrence et la charge de travail dans SQL Azure Data Warehouse pour développer des solutions."
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
   ms.date="09/27/2016"
   ms.author="sonyama;barbkess;jrj"/>

# <a name="concurrency-and-workload-management-in-sql-data-warehouse"></a>Gestion des accès concurrentiels au et la charge de travail dans Data Warehouse SQL

Pour présenter des performances prévisibles à l’échelle, vous aide à Microsoft Azure SQL Data Warehouse vous contrôlez les niveaux d’accès concurrentiels au et affectations de ressources telles que la quantité de mémoire et hiérarchisation des vitesses. Cet article présente les concepts de gestion de la concurrence et la charge de travail, expliquant comment ces deux fonctionnalités ont été apportées et comment vous pouvez les contrôler dans votre data warehouse. Gestion de la charge de travail SQL Data Warehouse est là pour vous aider à prendre en charge des environnements multi-utilisateurs. Il n’est pas destiné charges de travail clients multiples.

## <a name="concurrency-limits"></a>Limites de la concurrence

Data Warehouse SQL permet jusqu'à 1 024 connexions simultanées. Toutes les connexions de 1 024 peuvent soumettre des requêtes simultanément. Toutefois, pour optimiser le débit, SQL Data Warehouse peut en file d’attente des requêtes pour vous assurer que chaque requête reçoit une allocation de mémoire minimale. Queuing se produit lors de l’exécution des requêtes. En requêtes files d’attente lors de la concurrence limites sont atteintes, SQL Data Warehouse améliorerez débit total en veillant à ce que requêtes actives accéder aux ressources mémoire sont-ils nécessaires.  

Limites de la concurrence sont régis par deux concepts : *demandes simultanées* et *emplacements de la concurrence*. Pour exécuter une requête, il doit être exécuté dans la limite de la concurrence requête et l’allocation de connecteur concurrence.

- Demandes simultanées sont les requêtes en cours d’exécution en même temps. Data Warehouse SQL prend en charge jusqu'à 32 demandes simultanées sur les tailles DWU plus grandes.
- Emplacements concurrence sont alloués basé sur DWU. Chaque DWU 100 fournit 4 emplacements concurrence. Par exemple, un DW100 attribue 4 emplacements concurrence et DW1000 attribue 40. Chaque requête consomme un ou plusieurs concurrence emplacements, dépendants de la [classe de ressources](#resource-classes) de la requête. Requêtes en cours d’exécution de la classe de ressources smallrc consomment un emplacement de la concurrence. Requêtes en cours d’exécution dans une classe supérieure de la ressource consomment plus de connecteurs concurrence.

Le tableau suivant décrit les limites pour les demandes simultanées et les emplacements d’accès concurrentiels aux différentes tailles DWU.

### <a name="concurrency-limits"></a>Limites de la concurrence

|  DWU   | Demandes simultanées max  | Emplacements concurrence alloués |
| :----  | :---------------------: | :-------------------------: |
| DW100  |            4            |                4            |
| DW200  |            8            |                8            |
| DW300  |           12            |               12            |
| DW400  |           16            |               16            |
| DW500  |           20            |               20            |
| DW600  |           24            |               24            |
| DW1000 |           32            |               40            |
| DW1200 |           32            |               48            |
| DW1500 |           32            |               60            |
| DW2000 |           32            |               80            |
| DW3000 |           32            |              120            |
| DW6000 |           32            |              240            |

Lorsque l’une de ces seuils est remplie, les nouvelles requêtes sont en file d’attente et exécutés sur une base de sorti première connexion.  Que se termine à une requêtes et le nombre de requêtes et emplacements inférieure à la limite, des requêtes en file d’attente sont publiées. 

> [AZURE.NOTE]  *Sélectionnez* des requêtes en cours d’exécution en mode exclusif sur les vues de gestion dynamiques (DMV) ou des vues de catalogue ne sont pas régis par les limites concurrence. Vous pouvez contrôler le système quel que soit le nombre de requêtes s’exécutant sur celui-ci.

## <a name="resource-classes"></a>Classes de ressources

Ressource classes vous permettent de que contrôler l’allocation de mémoire et cycles processeur données à une requête. Vous pouvez affecter quatre catégories de ressources à un utilisateur dans le formulaire des *rôles de base de données*. Les classes quatre ressources sont **smallrc**, **mediumrc**, **largerc**et **xlargerc**. Les utilisateurs de smallrc reçoivent une plus petite quantité de mémoire et tirer parti des concurrence plus élevé. En revanche, les utilisateurs affectés à xlargerc figurent de grandes quantités de mémoire et par conséquent moins de leurs requêtes peuvent être exécutées simultanément.

Par défaut, chaque utilisateur est membre de la classe resource petite, smallrc. La procédure `sp_addrolemember` est utilisé pour augmenter la classe de la ressource, et `sp_droprolemember` est utilisé pour réduire la classe de ressources. Par exemple, cette commande serait augmenter la classe de ressources de loaduser à largerc :

```sql
EXEC sp_addrolemember 'largerc', 'loaduser'
```

Une bonne pratique consiste à définitivement assigner des utilisateurs à un cours de ressources plutôt que de modifier leurs classes de ressources. Par exemple, chargement des tables columnstore groupé créer des index de qualité supérieure lorsque affecté plus de mémoire. Pour vous assurer que les charges ont accès à une mémoire plus haute, créez un utilisateur spécifiquement pour charger des données et définitivement affectez cet utilisateur à une classe supérieure de la ressource.

Il existe plusieurs types de requêtes sans bénéficier d’une plus grande allocation de mémoire. Le système ignore leur allocation de classe de ressource et sont toujours exécutées ces requêtes dans la classe de ressources petite à la place. Si ces requêtes toujours exécuter dans la classe de ressources petite, ils peuvent exécuter lors de la concurrence emplacements sont sous pression et qu’ils n’utilisent plusieurs emplacements que nécessaire. Pour plus d’informations, consultez [exceptions de classe de ressources](#query-exceptions-to-concurrency-limits) .

Quelques plus d’informations sur le cours de la ressource :

- Autorisation de *Modifier le rôle* est nécessaire pour modifier la classe de ressources d’un utilisateur.  
- Bien que vous pouvez ajouter un utilisateur à un ou plusieurs des catégories de ressources supérieures, les utilisateurs aura les attributs de la classe resource plus élevée à laquelle elles sont affectées. Autrement dit, si un utilisateur est affecté à mediumrc et largerc, la classe supérieure de la ressource (largerc) est la classe de ressource qui sera respectée.  
- Impossible de modifier la classe de ressources de l’administrateur système.

Pour obtenir un exemple détaillé, consultez [modification utilisateur ressource classe exemple](#changing-user-resource-class-example).

## <a name="memory-allocation"></a>Allocation de mémoire

Il existe des avantages et inconvénients à l’augmentation de classe de ressources d’un utilisateur. Augmentation de classe de ressources pour un utilisateur donne leurs requêtes aient accès à plus de mémoire, c'est-à-dire les requêtes s’exécutent plus rapidement.  Toutefois, les catégories de ressources supérieures également réduisent le nombre de requêtes simultanées qui peuvent être exécutées. Il s’agit le compromis entre l’allocation de grandes quantités de mémoire à une requête unique ou d’autoriser d’autres requêtes, qui doivent également allocation de mémoire, d’exécuter simultanément. Si un utilisateur est donné haute allocation de mémoire d’une requête, d’autres utilisateurs n’ont pas accès à cette mémoire même pour exécuter une requête.

Le tableau suivant mappe la mémoire allouée à chaque distribution en cours DWU et des ressources.

### <a name="memory-allocations-per-distribution-mb"></a>Allocation de mémoire par distribution (Mo)

|  DWU   | smallrc | mediumrc | largerc | xlargerc |
| :----- | :-----: | :------: | :-----: | :------: |
| DW100  |   100   |    100   |    200  |    400   |
| DW200  |   100   |    200   |    400  |    800   |
| DW300  |   100   |    200   |    400  |    800   |
| DW400  |   100   |    400   |    800  |  1 600   |
| DW500  |   100   |    400   |    800  |  1 600   |
| DW600  |   100   |    400   |    800  |  1 600   |
| DW1000 |   100   |    800   |  1 600  |  3 200   |
| DW1200 |   100   |    800   |  1 600  |  3 200   |
| DW1500 |   100   |    800   |  1 600  |  3 200   |
| DW2000 |   100   |  1 600   |  3 200  |  6,400   |
| DW3000 |   100   |  1 600   |  3 200  |  6,400   |
| DW6000 |   100   |  3 200   |  6,400  |  12,800  |

Dans le tableau ci-dessus, vous pouvez voir qu’une requête en cours d’exécution sur un DW2000 de la classe de ressources xlargerc auront accès 6 400 Mo de mémoire dans chacune des bases de données distribuées 60.  Dans SQL Data Warehouse, il existe 60 répartitions. Par conséquent, pour calculer l’allocation de mémoire total d’une requête dans une classe ressource donnée, les valeurs ci-dessus doivent être multipliés par 60.

### <a name="memory-allocations-system-wide-gb"></a>Mémoire affectations au niveau du système (Go)

|  DWU   | smallrc | mediumrc | largerc | xlargerc |
| :----- | :-----: | :------: | :-----: | :------: |
| DW100  |    6    |    6     |    12   |    23    |
| DW200  |    6    |    12    |    23   |    47    |
| DW300  |    6    |    12    |    23   |    47    |
| DW400  |    6    |    23    |    47   |    94    |
| DW500  |    6    |    23    |    47   |    94    |
| DW600  |    6    |    23    |    47   |    94    |
| DW1000 |    6    |    47    |    94   |   188    |
| DW1200 |    6    |    47    |    94   |   188    |
| DW1500 |    6    |    47    |    94   |   188    |
| DW2000 |    6    |    94    |   188   |   375    |
| DW3000 |    6    |    94    |   188   |   375    |
| DW6000 |    6    |   188    |   375   |   750    |

Dans cette table d’allocation de mémoire au niveau du système, vous pouvez voir qu’une requête en cours d’exécution sur un DW2000 de la classe de ressources xlargerc allouée au total, 375 Go de mémoire (6 400 Mo * 60 répartitions / 1 024 à convertir en Go) sur l’intégralité de votre Data Warehouse SQL.

## <a name="concurrency-slot-consumption"></a>Consommation de connecteur concurrence

Data Warehouse SQL accorde plus de mémoire requêtes en cours d’exécution dans les catégories de ressources supérieures. Mémoire est une ressource fixe.  Par conséquent, plus la mémoire affectée par la requête, les requêtes simultanées moins peuvent exécuter. Le tableau suivant réitère tous les concepts précédents dans un affichage unique qui indique le nombre d’emplacements concurrence disponibles par DWU et les emplacements utilisés par chaque classe de ressources.

### <a name="allocation-and-consumption-of-concurrency-slots"></a>Attribution et la consommation d’emplacements concurrence

|  DWU   | Nombre maximal de requêtes simultané  | Emplacements concurrence alloués | Emplacements utilisés par smallrc |  Emplacements utilisés par mediumrc |  Emplacements utilisés par largerc |  Emplacements utilisés par xlargerc |
| :----  | :---------------------: | :-------------------------: | :-----: | :------: | :-----: | :------: |
| DW100  |            4            |                4            |    1    |     1    |    2    |    4     |
| DW200  |            8            |                8            |    1    |     2    |    4    |    8     |
| DW300  |           12            |               12            |    1    |     2    |    4    |    8     |
| DW400  |           16            |               16            |    1    |     4    |    8    |   16     |
| DW500  |           20            |               20            |    1    |     4    |    8    |   16     |
| DW600  |           24            |               24            |    1    |     4    |    8    |   16     |
| DW1000 |           32            |               40            |    1    |     8    |   16    |   32     |
| DW1200 |           32            |               48            |    1    |     8    |   16    |   32     |
| DW1500 |           32            |               60            |    1    |     8    |   16    |   32     |
| DW2000 |           32            |               80            |    1    |    16    |   32    |   64     |
| DW3000 |           32            |              120            |    1    |    16    |   32    |   64     |
| DW6000 |           32            |              240            |    1    |    32    |   64    |  128     |


Dans ce tableau, vous pouvez voir exécutant SQL Data Warehouse comme DW1000 attribue un maximum de 32 requêtes simultanées et un total de 40 emplacements concurrence. Si tous les utilisateurs sont en cours d’exécution dans smallrc, 32 demandes simultanées seraient autorisées, car chaque requête consommer 1 emplacement de la concurrence. Si tous les utilisateurs sur un DW1000 ont été en cours d’exécution dans mediumrc, chaque requête serait alloué 800 Mo par distribution total l’allocation de mémoire de 47 Go par requête et concurrence serait limité à 5 utilisateurs (40 emplacements concurrence / 8 emplacements par utilisateur mediumrc).

## <a name="query-importance"></a>Importance de la requête

Data Warehouse SQL met en œuvre classes de ressources à l’aide de groupes de la charge de travail. Il existe un total de huit groupes de charge de travail qui contrôlent le comportement des classes ressources entre les différentes tailles DWU. Pour n’importe quel DWU SQL Data Warehouse utilise quatre groupes huit la charge de travail. Cela est pertinent pour car chaque groupe de travail est affecté à une des quatre catégories de ressources : smallrc, mediumrc, largerc, ou xlargerc. Important de comprendre les groupes de la charge de travail est que certaines de ces groupes la charge de travail sont définis supérieure *importance*. Importance est utilisé pour l’UC de planification. Requêtes exécutées avec une importance haute obtiendrez trois fois plus de cycles processeur que ceux avec une importance moyenne. Par conséquent, les mappages des accès concurrentiels au connecteur déterminent également priorité du processeur. Si une requête consomme 16 ou plusieurs emplacements, il s’exécute avec une importance haute.

Le tableau suivant montre les mappages d’importance pour chaque groupe de travail.

### <a name="workload-group-mappings-to-concurrency-slots-and-importance"></a>Mappages de groupe la charge de travail vers des emplacements concurrence et importance

| Groupes de charge de travail | Mappage d’emplacement concurrence | Mo / Distribution | Mappage d’importance |
| :-------------- | :----------------------: | :---------------: | :----------------- |
| SloDWGroupC00   |            1             |         100       |       Moyenne       |
| SloDWGroupC01   |            2             |         200       |       Moyenne       |
| SloDWGroupC02   |            4             |         400       |       Moyenne       |
| SloDWGroupC03   |            8             |         800       |       Moyenne       |
| SloDWGroupC04   |           16             |       1 600       |       Élevé         |
| SloDWGroupC05   |           32             |       3 200       |       Élevé         |
| SloDWGroupC06   |           64             |       6,400       |       Élevé         |
| SloDWGroupC07   |          128             |      12,800       |       Élevé         |

Dans le graphique **attribution et la consommation d’emplacements concurrence** , vous pouvez voir qu’un DW500 utilise 1, 4, 8 ou emplacements d’accès concurrentiels au 16 pour smallrc, mediumrc, largerc et xlargerc, respectivement. Vous pouvez rechercher ces valeurs dans le graphique ci-dessus pour trouver l’importance pour chaque classe de ressources.

### <a name="dw500-mapping-of-resource-classes-to-importance"></a>Mappage DW500 de classes ressource importance

| Cours de ressources | Groupe de travail | Emplacements concurrence utilisés | Mo / Distribution | Importance |
| :------------- | :------------- | :--------------------: | :---------------: | :--------- |
| smallrc        | SloDWGroupC00  |           1            |         100       |   Moyenne   |
| mediumrc       | SloDWGroupC02  |           4            |         400       |   Moyenne   |
| largerc        | SloDWGroupC03  |           8            |         800       |   Moyenne   |
| xlargerc       | SloDWGroupC04  |          16            |        1 600      |   Élevé     |


Vous pouvez utiliser la requête suivante de la vue pour examiner les différences dans répartition des ressources mémoire en détail à partir du point de vue de l’administrateur de la ressource, ou pour analyser historique et active l’utilisation des groupes de la charge de travail lors de la résolution des problèmes.

```sql
WITH rg
AS
(   SELECT  
     pn.name                        AS node_name
    ,pn.[type]                      AS node_type
    ,pn.pdw_node_id                 AS node_id
    ,rp.name                        AS pool_name
    ,rp.max_memory_kb*1.0/1024              AS pool_max_mem_MB
    ,wg.name                        AS group_name
    ,wg.importance                  AS group_importance
    ,wg.request_max_memory_grant_percent        AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                     AS group_max_dop
    ,wg.effective_max_dop               AS group_effective_max_dop
    ,wg.total_request_count             AS group_total_request_count
    ,wg.total_queued_request_count          AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON  wg.pdw_node_id  = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
        AND     rp.name = 'SloDWPool'
)
SELECT  pool_name
,       pool_max_mem_MB
,       group_name
,       group_importance
,       (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,       node_name
,       node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
    node_name
,   group_request_max_memory_grant_pcnt
,   group_importance
;
```

## <a name="queries-that-honor-concurrency-limits"></a>Requêtes qui respectent les limites de la concurrence

La plupart des requêtes sont régis par classes de ressources. Ces requêtes doivent tenir dans les deux simultanées requête et accès concurrentiels au connecteur seuils. Un utilisateur ne peut pas choisir d’exclure une requête à partir du modèle de connecteur concurrence.

Pour rappel, les instructions suivantes respectent classes de ressource :

- INSERTION-SÉLECTION
- MISE À JOUR
- SUPPRIMER
- Sélectionnez (lors de l’interrogation de tables utilisateur)
- ALTER RECONSTRUCTION D’INDEX
- ALTER INDEX RÉORGANISER
- ALTER TABLE RECONSTRUCTION
- CRÉER UN INDEX
- CRÉER UN INDEX COLUMNSTORE GROUPÉ
- CRÉER LA TABLE EN TANT QUE SELECT (SACT)
- Chargement de données
- Opérations de déplacement de données effectuées par le Service de mouvement de données (DMS)

## <a name="query-exceptions-to-concurrency-limits"></a>Exceptions de requête aux limites de la concurrence

Certaines requêtes n’effectue pas de la classe de la ressource à laquelle l’utilisateur est affectée. Ces exceptions aux concurrence limites sont effectuées lorsque les ressources nécessaires pour une commande particulière sont faibles, souvent parce que la commande est une opération de métadonnées. L’objectif de ces exceptions consiste à éviter l’allocation de mémoire plus grandes requêtes que vous en aurez jamais besoin. Dans ce cas, la classe de ressources petite par défaut (smallrc) est toujours utilisée quelle que soit la classe réel ressource affectée à l’utilisateur. Par exemple, `CREATE LOGIN` pourront toujours exécuter dans smallrc. Les ressources nécessaires pour répondre à cette opération étant très faibles, il est inutile d’inclure la requête dans le modèle de connecteur concurrence.  Ces requêtes ne sont également pas limités par la limite d’accès concurrentiels au 32 utilisateur, un nombre illimité de ces requêtes peut s’exécutent dans la limite de session de 1 024 sessions.

Les instructions suivantes n’effectue pas de classes de ressources :

- CRÉER ou supprimer un tableau
- ALTER TABLE... COMMUTATEUR, fractionner ou fusionner PARTITION
- ALTER INDEX DÉSACTIVER
- SUPPRIMER L’INDEX
- CRÉER, mettre à jour ou DROP STATISTICS
- TRONQUER LA TABLE
- MODIFIER L’AUTORISATION
- CRÉER UNE CONNEXION
- CREATE, ALTER ou DROP USER
- CREATE, ALTER ou DROP PROCEDURE
- CRÉER ou supprimer un affichage
- INSÉRER DES VALEURS
- Sélectionnez à partir de vues système et DMV
- EXPLIQUEZ
- DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="change-a-user-resource-class-example"></a>Modifier un exemple de classe ressource utilisateur

1. **Connexion créer :** Ouvrez une connexion à votre base de données **maître** sur le serveur SQL qui héberge votre base de données SQL Data Warehouse et exécutez la commande suivante.

    ```sql
    CREATE LOGIN newperson WITH PASSWORD = 'mypassword';
    CREATE USER newperson for LOGIN newperson;
    ```

    > [AZURE.NOTE] Il est conseillé de créer un utilisateur dans la base de données principale pour les utilisateurs de magasin de données SQL Azure. Création d’un utilisateur principale permet à un utilisateur pour se connecter à l’aide d’outils tels que SSMS sans spécifier un nom de base de données.  Il leur permet également d’utiliser l’Explorateur d’objets pour afficher toutes les bases de données sur un serveur SQL server.  Pour plus d’informations sur la création et gestion des utilisateurs, voir [sécuriser une base de données SQL Data Warehouse][].

2. **Utilisateur créer SQL Data Warehouse :** Ouvrez une connexion à la base de données **SQL Data Warehouse** et exécutez la commande suivante.

    ```sql
    CREATE USER newperson FOR LOGIN newperson;
    ```

3. **Accorder des autorisations :** L’exemple suivant accorde `CONTROL` sur la base de données **SQL Data Warehouse** . `CONTROL`à la base de données au niveau de revient db_owner dans SQL Server.

    ```sql
    GRANT CONTROL ON DATABASE::MySQLDW to newperson;
    ```

4. **Augmenter classe de ressources :** La requête suivante permet d’ajouter un utilisateur à un rôle de gestion de la charge de travail plus élevé.

    ```sql
    EXEC sp_addrolemember 'largerc', 'newperson'
    ```

5. **Diminuer classe de ressources :** Utiliser la requête suivante pour supprimer un utilisateur d’un rôle de gestion de la charge de travail.

    ```sql
    EXEC sp_droprolemember 'largerc', 'newperson';
    ```

    > [AZURE.NOTE] Il n’est pas possible de supprimer un utilisateur de smallrc.

## <a name="queued-query-detection-and-other-dmvs"></a>Détection de la requête en file d’attente et autres DMV

Vous pouvez utiliser la `sys.dm_pdw_exec_requests` vue pour identifier les requêtes qui sont en attente dans une file d’attente de la concurrence. Requêtes en attente d’un emplacement concurrence aura un état **suspendu**.

```sql
SELECT   r.[request_id]              AS Request_ID
        ,r.[status]              AS Request_Status
        ,r.[submit_time]             AS Request_SubmitTime
        ,r.[start_time]              AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time]) AS Request_InitiateDuration_ms
        ,r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r;
```

Rôles de gestion de la charge de travail peuvent être affichés avec `sys.database_principals`.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0;
```

La requête suivante montre le rôle est affecté à chaque utilisateur.

```sql
SELECT   r.name AS role_principal_name
        ,m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id     = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id   = m.principal_id
WHERE   r.name IN ('mediumrc','largerc', 'xlargerc');
```

Data Warehouse SQL contient les éléments suivants attendre types :

- **LocalQueriesConcurrencyResourceType**: les requêtes qui se trouvent en dehors de l’infrastructure de connecteur concurrence. Fonctions telles que des requêtes de vue et système `SELECT @@VERSION` sont des exemples de requêtes locales.
- **UserConcurrencyResourceType**: les requêtes qui se trouvent à l’intérieur de l’infrastructure de connecteur concurrence. Requêtes sur les tables de l’utilisateur final représentent exemples qu’utilisent ce type de ressource.
- **DmsConcurrencyResourceType**: attend résultant d’opérations de déplacement de données.
- **BackupConcurrencyResourceType**: cette attente indique qu’une base de données est sauvegardé. La valeur maximale pour ce type de ressource est 1. Si plusieurs sauvegardes aient été demandés en même temps, les autres colonnes met file d’attente.

La `sys.dm_pdw_waits` vue peut être utilisée pour connaître les ressources qu’une demande est en attente de.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]            AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,   SESSION_ID()            AS Current_session
,   s.[status]          AS Session_status
,   s.[login_name]
,   s.[query_count]
,   s.[client_id]
,   s.[sql_spid]
,   r.[command]         AS Request_command
,   r.[label]
,   r.[status]          AS Request_status
,   r.[submit_time]
,   r.[start_time]
,   r.[end_compile_time]
,   r.[end_time]
,   DATEDIFF(ms,r.[submit_time],r.[start_time])     AS Request_queue_time_ms
,   DATEDIFF(ms,r.[start_time],r.[end_compile_time])    AS Request_compile_time_ms
,   DATEDIFF(ms,r.[end_compile_time],r.[end_time])      AS Request_execution_time_ms
,   r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE   w.[session_id] <> SESSION_ID();
```

La `sys.dm_pdw_resource_waits` vue affiche uniquement l’attend de ressources utilisé par une requête donnée. Délai d’attente ressource mesure uniquement la durée en attente de ressources servies, contrairement à délai d’attente signal, ce qui correspond à la durée que nécessaire pour les serveurs SQL sous-jacente planifier la requête sur le processeur.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE   [session_id] <> SESSION_ID();
```

La `sys.dm_pdw_wait_stats` vue peut être utilisée pour l’analyse des tendances historiques d’attentes.

```sql
SELECT  w.[pdw_node_id]
,       w.[wait_name]
,       w.[max_wait_time]
,       w.[request_count]
,       w.[signal_time]
,       w.[completed_count]
,       w.[wait_time]
FROM    sys.dm_pdw_wait_stats w;
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la gestion des utilisateurs de base de données et de sécurité, voir [sécuriser une base de données SQL Data Warehouse][]. Pour plus d’informations sur la ressource comment plus grande classes peut améliorer la qualité des index columnstore groupé, voir [reconstruction d’index pour améliorer la qualité du segment].

<!--Image references-->

<!--Article references-->
[Sécuriser une base de données SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Reconstruction d’index pour améliorer la qualité du segment]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Sécuriser une base de données SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
