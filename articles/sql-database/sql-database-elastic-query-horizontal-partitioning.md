<properties
    pageTitle="Création de rapports sur les bases de données plus grande échelle cloud | Microsoft Azure"
    description="comment configurer des requêtes élastiques sur partitions horizontales"    
    services="sql-database"
    documentationCenter=""  
    manager="jhubbard"
    authors="torsteng"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/27/2016"
    ms.author="torsteng" />

# <a name="reporting-across-scaled-out-cloud-databases-preview"></a>Création de rapports entre les bases de données plus grande échelle cloud (preview)

![Interrogation de milieu des fragments][1]

Bases de données sharded distribuer des lignes dans une mise à l’échelle des données niveau. Le schéma est identique sur toutes les bases de données participants, également connus sous le partition horizontale. Utiliser une requête élastique, vous pouvez créer des rapports qui s’étalent sur toutes les bases de données dans une base de données sharded.

Pour un démarrage rapide, voir [Création de rapports sur les bases de données plus grande échelle cloud](sql-database-elastic-query-getting-started.md).

Pour les bases de données non sharded, voir [requête entre bases de données cloud avec différents schémas](sql-database-elastic-query-vertical-partitioning.md). 

 
## <a name="prerequisites"></a>Conditions préalables

* Créer un mappage partagé à l’aide de la bibliothèque de client élastique de base de données. voir [Éclater mapper gestion](sql-database-elastic-scale-shard-map-management.md). Ou utilisez l’application d’exemple dans [prise en main des outils de base de données élastique](sql-database-elastic-scale-get-started.md).
* Par ailleurs, voir [migrer des bases de données existantes aux bases de données plus grande échelle](sql-database-elastic-convert-to-use-elastic-tools.md).
* L’utilisateur doit disposer d’autorisations modifier n’importe quelle SOURCE de données externe. Cette autorisation est incluse avec l’autorisation de modifier une base de données.
* Autorisations de modifier n’importe quelle SOURCE de données externes sont nécessaires pour faire référence à la source de données sous-jacente.

## <a name="overview"></a>Vue d’ensemble

Ces instructions créent la représentation des métadonnées de votre couche de données sharded dans la base de données de requête élastique. 


1. [CRÉER LA CLÉ PRINCIPALE](https://msdn.microsoft.com/library/ms174382.aspx)
2. [CRÉER LA BASE DE DONNÉES INCLUS DANS L’ÉTENDUE LES INFORMATIONS D’IDENTIFICATION](https://msdn.microsoft.com/library/mt270260.aspx)
3. [CRÉER LA SOURCE DE DONNÉES EXTERNE](https://msdn.microsoft.com/library/dn935022.aspx)
4. [CRÉER UNE TABLE EXTERNE](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="11-create-database-scoped-master-key-and-credentials"></a>Base de données créer 1.1 inclus dans l’étendue de la clé principale et les informations d’identification 

Les informations d’identification sont utilisées par la requête élastique pour se connecter à vos bases de données distantes.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]
 
>[AZURE.NOTE] Assurez-vous que la *«\<nom d’utilisateur\>«* ne contient aucune *"@servername"* suffixe. 

## <a name="12-create-external-data-sources"></a>1.2 créer des sources de données externes

Syntaxe :

    <External_Data_Source> ::=    
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH                                            
            (TYPE = SHARD_MAP_MANAGER,
                    LOCATION = '<fully_qualified_server_name>',
            DATABASE_NAME = ‘<shardmap_database_name>',
            CREDENTIAL = <credential_name>, 
            SHARD_MAP_NAME = ‘<shardmapname>’ 
                   ) [;] 

### <a name="example"></a>Exemple 

    CREATE EXTERNAL DATA SOURCE MyExtSrc 
    WITH 
    ( 
        TYPE=SHARD_MAP_MANAGER,
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ShardMapDatabase', 
        CREDENTIAL= SMMUser, 
        SHARD_MAP_NAME='ShardMap' 
    );
 
Récupérer la liste des sources de données externes en cours : 

    select * from sys.external_data_sources; 

Votre carte éclater fait référence à la source de données externe. Une requête élastique utilise ensuite la source de données externes et la carte éclater sous-jacente pour énumérer les bases de données qui participent à la couche de données. Les mêmes informations d’identification sont utilisées pour lire le plan partagé et accéder aux données au milieu des fragments pendant le traitement d’une requête élastique. 

## <a name="13-create-external-tables"></a>1.3 créer des tables externes 
 
Syntaxe :  

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name  
        ( { <column_definition> } [ ,...n ])     
        { WITH ( <sharded_external_table_options> ) }
    ) [;]  
    
    <sharded_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>,       
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 
      DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED |ROUND_ROBIN

**Exemple**

    CREATE EXTERNAL TABLE [dbo].[order_line]( 
         [ol_o_id] int NOT NULL, 
         [ol_d_id] tinyint NOT NULL,
         [ol_w_id] int NOT NULL, 
         [ol_number] tinyint NOT NULL, 
         [ol_i_id] int NOT NULL, 
         [ol_delivery_d] datetime NOT NULL, 
         [ol_amount] smallmoney NOT NULL, 
         [ol_supply_w_id] int NOT NULL, 
         [ol_quantity] smallint NOT NULL, 
         [ol_dist_info] char(24) NOT NULL 
    ) 
    
    WITH 
    ( 
        DATA_SOURCE = MyExtSrc, 
        SCHEMA_NAME = 'orders', 
        OBJECT_NAME = 'order_details', 
        DISTRIBUTION=SHARDED(ol_w_id)
    ); 

Récupérer la liste des tables externes à partir de la base de données active : 

    SELECT * from sys.external_tables; 

Pour supprimer les tables externes :

    DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]

### <a name="remarks"></a>Remarques

Les données\_SOURCE clause définit la source de données externes (une carte éclater) qui est utilisée pour la table externe.  

Le schéma\_nom et l’objet\_clauses nom mapper la définition de la table externe à une table dans un schéma différent. En cas d’omission, le schéma de l’objet distant est considéré comme étant « dbo » et son nom est identique au nom de la table externe qui est défini. Ceci est utile si le nom de votre table distante est déjà effectuée dans la base de données dans lequel vous voulez créer la table externe. Par exemple, vous souhaitez définir une table externe pour obtenir une vue de synthèse des vues de catalogue ou DMV sur vos données à l’échelle des niveaux. Dans la mesure où les affichages catalogue et DMV existe déjà localement, vous ne pouvez pas utiliser leur nom pour la définition de la table externe. À la place, utilisez un autre nom et la vue de catalogue ou la vue nom dans le schéma\_nom et/ou objet\_clauses de nom. (Voir l’exemple ci-dessous). 

La clause DISTRIBUTION spécifie la répartition des données utilisée pour cette table. Le processeur de requêtes utilise les informations fournies dans la clause DISTRIBUTION pour générer les plans de requête plus efficaces.  

1. **SHARDED** signifie données sont segmentées horizontalement entre les bases de données. La clé de partition pour la distribution des données est le paramètre **< sharding_column_name >** .
2. **RÉPLIQUÉ** signifie que copies identiques de la table sont présents sur chaque base de données. Il est vous assurer que les réplicas sont identiques entre les bases de données.
3. **Arrondi\_ROBIN** signifie que la table est segmentée horizontalement à l’aide d’une méthode de distribution dépendent de l’application. 

**Référence de couche de données**: la table externe DDL fait référence à une source de données externe. La source de données externe spécifie un mappage partagé qui fournit la table externe avec les informations nécessaires pour identifier les bases de données dans votre couche de données. 


### <a name="security-considerations"></a>Considérations sur la sécurité 

Les utilisateurs ayant accès à la table externe accéder automatiquement à la table sous-jacente à distance sous les informations d’identification fournies dans la définition de source de données externes. Évitez indésirable élévation de privilèges via les informations d’identification de la source de données externes. Utilisez accorder ou RÉVOQUER pour une table externe, comme s’il s’agissait d’une table normale.  

Une fois que vous avez défini vos tables externes et votre source de données externe, vous pouvez désormais utiliser T-SQL complète sur vos tables externes.

## <a name="example-querying-horizontal-partitioned-databases"></a>Exemple : interroger des bases de données partitionnées horizontales 

La requête suivante effectue une jointure trois voies entre magasins, les commandes et lignes de commande et utilise plusieurs agrégats et un filtre sélectif. Il considère partition (1) horizontal (ont) et (2) que magasins, les commandes et lignes de commande sont sharded en fonction de la colonne id entrepôt, et que la requête élastique peut co-création localiser les jointures au milieu des fragments et traiter le composant coûteux de la requête sur les milieu des fragments en parallèle. 

    select  
         w_id as warehouse,
         o_c_id as customer,
         count(*) as cnt_orderline,
         max(ol_quantity) as max_quantity,
         avg(ol_amount) as avg_amount, 
         min(ol_delivery_d) as min_deliv_date
    from warehouse 
    join orders 
    on w_id = o_w_id
    join order_line 
    on o_id = ol_o_id and o_w_id = ol_w_id 
    where w_id > 100 and w_id < 200 
    group by w_id, o_c_id 
 
## <a name="stored-procedure-for-remote-t-sql-execution-spexecuteremote"></a>La procédure stockée pour l’exécution T-SQL à distance : sp\_execute_remote

Requête élastique présente également une procédure stockée qui permet d’accéder directement au milieu des fragments. La procédure stockée est appelée [sp\_exécuter \_distant](https://msdn.microsoft.com/library/mt703714) et peut être utilisé pour exécuter des procédures stockées distantes ou code T-SQL sur les bases de données distantes. Elle accepte les paramètres suivants : 

* Nom de source de données (nvarchar) : le nom de la source de données externes de type SGBDR. 
* Requête (nvarchar) : requête T-SQL à exécuter sur chaque partagé. 
* Déclaration de paramètre (nvarchar) - facultative : chaîne avec les définitions de type de données pour les paramètres utilisés dans le paramètre de requête (par exemple, sp_executesql). 
* Liste de valeurs de paramètre - facultatif : liste séparées par des virgules des valeurs de paramètre (par exemple, sp_executesql).

Le sp\_exécuter\_à distance utilise la source de données externes fournie dans les paramètres d’appel pour exécuter l’instruction T-SQL donnée sur les bases de données distantes. Il utilise les informations d’identification de la source de données externes à se connecter à la base de données du Gestionnaire de shardmap et les bases de données distantes.  

Exemple : 

    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 

## <a name="connectivity-for-tools"></a>Connectivité des outils  

Utiliser des chaînes de connexion SQL Server standard pour connecter votre application, votre outils d’intégration BI et les données à la base de données avec les définitions de votre table externe. Assurez-vous que SQL Server est pris en charge en tant que source de données pour votre outil. Puis référence la base de données de requête élastique comme n’importe quel autre base de données SQL Server connecté à l’outil et utilisez des tableaux externes à partir de votre application ou un outil comme s’il s’agissait de tables locales. 

## <a name="best-practices"></a>Meilleures pratiques 

* Assurez-vous que la base de données du point de terminaison de requête élastique a donné l’accès à la base de données shardmap et tous les milieu des fragments à travers les pare-feu de base de données SQL.  

* Valider ou mettent en œuvre la répartition des données définie par la table externe. Si la distribution de données réelles est différente de la distribution spécifiée dans la définition de votre tableau, vos requêtes peuvent donner des résultats inattendus. 

* Requête élastique actuellement n’effectue pas de suppression éclater lorsqu’il prédicats sur la touche ont permettant d’en toute sécurité exclure certains milieu des fragments de traitement.

* Requête élastique convient le mieux pour les requêtes où la plupart du calcul peut être effectuée sur au milieu des fragments. En règle générale, vous obtenez les meilleures performances de requête avec les prédicats filtre sélectif qui peut être évaluée sur le milieu des fragments ou les jointures via les touches partition qui peuvent être effectuées de façon alignés sur les partitions sur milieu des fragments tous les. Autres modèles de requête doivent charger de grandes quantités de données à partir d’au milieu des fragments pour le nœud de tête et peuvent être lent

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->
