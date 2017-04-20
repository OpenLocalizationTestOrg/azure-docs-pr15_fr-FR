<properties
    pageTitle="Requête sur bases de données cloud schéma différents | Microsoft Azure"
    description="comment configurer des requêtes de bases de données croisées sur partitions verticales"    
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

# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>Interrogation de bases de données cloud avec différents schémas (preview)

![Interrogation de tables dans différentes bases de données][1]

Bases de données verticalement utilisent différents jeux de tables différentes bases de données. Cela signifie que le schéma est différent sur différentes bases de données. Par exemple, toutes les tables d’inventaire sont dans une base de données alors que toutes les tables de comptabilité sont dans une seconde base de données. 

## <a name="prerequisites"></a>Conditions préalables

* L’utilisateur doit disposer d’autorisations modifier n’importe quelle SOURCE de données externe. Cette autorisation est incluse avec l’autorisation de modifier une base de données.
* Autorisations de modifier n’importe quelle SOURCE de données externes sont nécessaires pour faire référence à la source de données sous-jacente.

## <a name="overview"></a>Vue d’ensemble

**Remarque**: contrairement avec partition horizontale, ces instructions DDL ne dépendent pas sur la définition d’un niveau de données avec une carte éclater via la bibliothèque de client élastique de base de données.

1. [CRÉER LA CLÉ PRINCIPALE](https://msdn.microsoft.com/library/ms174382.aspx)
2. [CRÉER LA BASE DE DONNÉES INCLUS DANS L’ÉTENDUE LES INFORMATIONS D’IDENTIFICATION](https://msdn.microsoft.com/library/mt270260.aspx)
3. [CRÉER LA SOURCE DE DONNÉES EXTERNE](https://msdn.microsoft.com/library/dn935022.aspx)
4. [CRÉER UNE TABLE EXTERNE](https://msdn.microsoft.com/library/dn935021.aspx) 


## <a name="create-database-scoped-master-key-and-credentials"></a>Créer la clé inclus dans l’étendue de la base de données principale et les informations d’identification 

Les informations d’identification sont utilisées par la requête élastique pour se connecter à vos bases de données distantes.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]
 
**Remarque**    Assurez-vous que la *<username>* ne contient aucune *"@servername"* suffixe. 

## <a name="create-external-data-sources"></a>Créer des sources de données externes

Syntaxe :

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH 
               (TYPE = RDBMS,
                LOCATION = ’<fully_qualified_server_name>’,
                DATABASE_NAME = ‘<remote_database_name>’,  
                CREDENTIAL = <credential_name> 
                ) [;] 

**Important**   Le paramètre de TYPE doit être défini à **SGBDR**. 

### <a name="example"></a>Exemple 

L’exemple suivant illustre l’utilisation de l’instruction CREATE pour les sources de données externes. 

    CREATE EXTERNAL DATA SOURCE RemoteReferenceData 
    WITH 
    ( 
        TYPE=RDBMS, 
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ReferenceData', 
        CREDENTIAL= SqlUser 
    ); 
 
Pour récupérer la liste des sources de données externes en cours : 

    select * from sys.external_data_sources; 

### <a name="external-tables"></a>Tables externes 

Syntaxe :

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name . ] table_name  
    ( { <column_definition> } [ ,...n ])     
    { WITH ( <rdbms_external_table_options> ) } 
    )[;] 
    
    <rdbms_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>, 
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 

### <a name="example"></a>Exemple  

    CREATE EXTERNAL TABLE [dbo].[customer]( 
        [c_id] int NOT NULL, 
        [c_firstname] nvarchar(256) NULL, 
        [c_lastname] nvarchar(256) NOT NULL, 
        [street] nvarchar(256) NOT NULL, 
        [city] nvarchar(256) NOT NULL, 
        [state] nvarchar(20) NULL, 
        [country] nvarchar(50) NOT NULL, 
    ) 
    WITH 
    ( 
           DATA_SOURCE = RemoteReferenceData 
    ); 

L’exemple suivant montre comment récupérer la liste des tables externes à partir de la base de données active : 

    select * from sys.external_tables; 

### <a name="remarks"></a>Remarques

Requête élastique étend la syntaxe de la table externe existante pour définir des tables externes qui utilisent des sources de données externes de type SGBDR. Une définition de la table externe pour une partition verticale aborde les points suivants : 

* **Schéma**: la table externe DDL définit un schéma qui permettent de vos requêtes. Le schéma fourni dans la définition de votre table externe doit correspondre au schéma des tables de la base de données distante où sont stockées les données réelles. 

* **Référence sur la base de données distante**: la table externe DDL fait référence à une source de données externe. La source de données externe indique le nom du serveur logique et le nom de base de données de la base de données distante où sont stockées les données réelles du tableau. 

Utilisation d’une source de données externes comme indiqué dans la section précédente, la syntaxe pour créer des tables externes est la suivante : 

La clause source_de_données définit la source de données externes (c'est-à-dire que la base de données distante en cas de partition verticale) qui est utilisée pour la table externe.  

Les clauses SCHEMA_NAME et nom_objet permettent de mapper la définition de la table externe à une table dans un schéma différent sur la base de données distante, ou à une table avec un nom différent, respectivement. Ceci est utile si vous souhaitez définir une table externe à une vue de catalogue ou vue sur votre base de données distante – ou toute autre situation où le nom de table distante est déjà extrait localement.  

L’instruction DDL suivante supprime une définition de table externe existante à partir du catalogue local. Il n’influe pas sur la base de données distante. 

    DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  

**Autorisations pour créer/supprimer une TABLE externe**: autorisations ALTER n’importe quelle SOURCE de données externes sont nécessaires pour table externe DDL qui est également nécessaire pour faire référence à la source de données sous-jacente.  

## <a name="security-considerations"></a>Considérations sur la sécurité
Les utilisateurs ayant accès à la table externe accéder automatiquement à la table sous-jacente à distance sous les informations d’identification fournies dans la définition de source de données externes. Vous devez avec soin gérer l’accès à la table externe afin d’éviter les indésirable élévation de privilèges via les informations d’identification de la source de données externes. Autorisations SQL standard peuvent être utilisées pour accorder ou RÉVOQUER l’accès à une table externe comme s’il s’agissait d’une table normale.  


## <a name="example-querying-vertically-partitioned-databases"></a>Exemple : interroger verticalement partition bases de données 

La requête suivante effectue une jointure trois voies entre les deux tables locales des commandes et des lignes de commande et la table à distance pour les clients. Voici un exemple du cas d’utilisation de données de référence pour élastique requête : 

    SELECT      
     c_id as customer,
     c_lastname as customer_name,
     count(*) as cnt_orderline, 
     max(ol_quantity) as max_quantity,
     avg(ol_amount) as avg_amount,
     min(ol_delivery_d) as min_deliv_date
    FROM customer 
    JOIN orders 
    ON c_id = o_c_id
    JOIN  order_line 
    ON o_id = ol_o_id and o_c_id = ol_c_id
    WHERE c_id = 100


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

Vous pouvez utiliser des chaînes de connexion SQL Server standard pour vous connecter vos outils d’intégration BI et les données aux bases de données sur le serveur de base de données SQL qui a query élastique activé et tables externes définies. Assurez-vous que SQL Server est pris en charge en tant que source de données pour votre outil. Puis reportez-vous à la base de données de requête élastique et ses tables externes comme n’importe quel autre base de données de SQL Server vous devez vous connecter à avec votre outil. 

## <a name="best-practices"></a>Meilleures pratiques 
 
* Assurez-vous que la base de données du point de terminaison de requête élastique est autorisé à accéder à la base de données distante en permettant l’accès pour les Services Azure dans sa configuration de pare-feu de base de données SQL. Vérifiez également que les informations d’identification fournies dans la définition de source de données externes peuvent se connecter à la base de données distante et disposant des autorisations pour accéder à la table distante.  

* Requête élastique convient le mieux pour les requêtes où la plupart du calcul peut être effectuée sur les bases de données distantes. Vous obtenez généralement les meilleures performances de requête avec les prédicats filtre sélectif qui peut être évaluée sur les bases de données distantes ou des jointures qui peuvent être exécutées complètement sur la base de données distante. Autres modèles de requête doivent charger de grandes quantités de données à partir de la base de données distante et peuvent être lent. 


## <a name="next-steps"></a>Étapes suivantes

Pour interroger partitionnées horizontalement bases de données (également appelé sharded), voir [des requêtes dans les bases de données sharded cloud (partitionnées horizontalement)](sql-database-elastic-query-horizontal-partitioning.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Image references-->
[1]: ./media/sql-database-elastic-query-vertical-partitioning/verticalpartitioning.png


<!--anchors-->
