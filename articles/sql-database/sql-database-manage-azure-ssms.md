<properties 
    pageTitle="Gérer une base de données SQL SSMS | Microsoft Azure" 
    description="Découvrez comment utiliser SQL Server Management Studio pour gérer les bases de données et les serveurs de base de données SQL." 
    services="sql-database" 
    documentationCenter=".net" 
    authors="stevestein" 
    manager="jhubbard" 
    editor="tysonn"/>

<tags 
    ms.service="sql-database" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/29/2016" 
    ms.author="sstein"/>


# <a name="managing-azure-sql-database-using-sql-server-management-studio"></a>Gestion de base de données SQL Azure avec SQL Server Management Studio 


> [AZURE.SELECTOR]
- [Portail Azure](sql-database-manage-portal.md)
- [SSMS](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-manage-powershell.md)

Vous pouvez utiliser SQL Server Management Studio (SSMS) pour administrer les bases de données et les serveurs de base de données SQL Azure. Cette rubrique décrit les tâches courantes avec SSMS. Vous disposez déjà d’un serveur et la base de données créée dans une base de données SQL Azure avant de commencer. Pour plus d’informations, consultez [créer votre première base de données SQL Azure](sql-database-get-started.md) et [connexion et requête à l’aide de SSMS](sql-database-connect-query-ssms.md) .

Il est recommandé d’utiliser la dernière version de SSMS chaque fois que vous travaillez avec la base de données SQL Azure. 

> [AZURE.IMPORTANT] Toujours utiliser la dernière version de SSMS, car il est une amélioration continue pour travailler avec les dernières mises à jour et base de données SQL Azure. Pour obtenir la dernière version, voir [Télécharger SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).



## <a name="create-and-manage-azure-sql-databases"></a>Créer et gérer des bases de données SQL Azure

Lorsque vous êtes connecté à la base de données **principale** , vous pouvez créer des bases de données sur le serveur et modifier ou supprimer des bases de données existantes. Les étapes suivantes décrivent comment effectuer plusieurs tâches courantes de gestion de base de données via Management Studio. Pour effectuer ces tâches, vérifiez que vous êtes connecté à la base de données **principale** avec la connexion principale au niveau du serveur que vous avez créé lorsque vous configurez votre serveur.

Pour ouvrir une fenêtre de requête dans Management Studio, ouvrez le dossier de bases de données, développez le dossier **Bases de données système** , avec le bouton droit sur **maître**, puis cliquez sur **Nouvelle requête**.

-   Utilisez l’instruction **CREATE DATABASE** pour créer une base de données. Pour plus d’informations, voir [Créer une base de données (base de données SQL)](https://msdn.microsoft.com/library/dn268335.aspx). L’instruction suivante crée une base de données nommée **myTestDB** et spécifie qu’il s’agit d’une base de données Standard Edition S0 avec une taille maximale par défaut de 250 Go.

        CREATE DATABASE myTestDB
        (EDITION='Standard',
         SERVICE_OBJECTIVE='S0');

Cliquez sur **exécuter** pour exécuter la requête.

-   L’instruction **ALTER DATABASE** permet de modifier une base de données existante, par exemple, si vous voulez modifier le nom et l’édition de la base de données. Pour plus d’informations, voir [ALTER DATABASE (base de données SQL)](https://msdn.microsoft.com/library/ms174269.aspx). L’instruction suivante modifie la base de données que vous avez créé à l’étape précédente pour modifier édition à S1 Standard.

        ALTER DATABASE myTestDB
        MODIFY
        (SERVICE_OBJECTIVE='S1');

-   Utilisez **L’instruction DROP DATABASE** instruction pour supprimer une base de données existante. Pour plus d’informations, voir [L’instruction DROP DATABASE (base de données SQL)](https://msdn.microsoft.com/library/ms178613.aspx). L’instruction suivante supprime la base de données **myTestDB** , mais ne déposez-le maintenant, car vous l’utiliserez pour créer des connexions de l’étape suivante.

        DROP DATABASE myTestBase;

-   La base de données principale comporte l’affichage **sys.databases** que vous pouvez utiliser pour afficher des détails sur toutes les bases de données. Pour afficher toutes les bases de données existantes, exécutez l’instruction suivante :

        SELECT * FROM sys.databases;

-   Dans la base de données SQL, l’instruction **USE** n’est pas prise en charge pour basculer entre les bases de données. À la place, vous devez établir une connexion directement à la base de données cible.

>[AZURE.NOTE] La plupart des instructions Transact-SQL créer ou modifient une base de données doivent être exécutées dans leur propre lot et ne peuvent pas être groupées avec d’autres instructions Transact-SQL. Pour plus d’informations, voir les informations spécifiques à la déclaration.

## <a name="create-and-manage-logins"></a>Créer et gérer des connexions

La base de données **principale** contient des connexions et les noms de connexion sont autorisé à créer des bases de données ou d’autres connexions. Gérer les connexions en vous connectant à la base de données **principale** avec la connexion principale au niveau du serveur que vous avez créé lorsque vous configurez votre serveur. Vous pouvez utiliser les instructions **CREATE LOGIN**, **ALTER LOGIN**ou **DROP LOGIN** pour exécuter des requêtes sur la base de données principale qui gère les connexions au sein de l’ensemble du serveur. Pour plus d’informations, voir [Gérer les bases de données et les connexions de base de données SQL](http://msdn.microsoft.com/library/azure/ee336235.aspx). 


-   Utilisez l’instruction **CREATE LOGIN** pour créer une connexion au niveau du serveur. Pour plus d’informations, voir [CREATE LOGIN (base de données SQL)](https://msdn.microsoft.com/library/ms189751.aspx). L’instruction suivante crée une connexion appelée **login1**. Remplacez **le mot de passe1** par le mot de passe de votre choix.

        CREATE LOGIN login1 WITH password='password1';

-   Utilisez l’instruction **CREATE USER** pour accorder des autorisations au niveau de la base de données. Toutes les connexions doivent être créées dans la base de données **principale** . Pour une connexion pour vous connecter à une autre base de données, vous devez lui accorder des autorisations au niveau de la base de données à l’aide de l’instruction **CREATE USER** sur cette base de données. Pour plus d’informations, voir [Créer un utilisateur (base de données SQL)](https://msdn.microsoft.com/library/ms173463.aspx). 

-   Pour accorder des autorisations login1 à une base de données appelée **myTestDB**, procédez comme suit :

 1.  Pour actualiser l’Explorateur d’objets pour afficher la base de données **myTestDB** que vous avez créé, cliquez sur le nom du serveur dans l’Explorateur d’objets, puis sur **Actualiser**.  

     Si vous avez fermé la connexion, vous pouvez vous reconnecter en sélectionnant **Se connecter Explorateur d’objets** dans le menu fichier.

 2. Avec le bouton droit de la base de données **myTestDB** et sélectionnez **Nouvelle requête**.

    3.  Exécutez l’instruction suivante sur la base de données myTestDB pour créer un utilisateur de base de données nommé **login1User** qui correspond à la connexion au niveau du serveur **login1**.

            CREATE USER login1User FROM LOGIN login1;

-   Utiliser la **sp\_addrolemember** procédure stockée pour donner le compte d’utilisateur le niveau d’autorisation sur la base de données approprié. Pour plus d’informations, voir [sp_addrolemember (Transact-SQL)](http://msdn.microsoft.com/library/ms187750.aspx). L’instruction suivante vous donne **login1User** autorisations en lecture seule à la base de données en ajoutant **login1User** à la **db\_datareader** rôle.

        exec sp_addrolemember 'db_datareader', 'login1User';    

-   Utilisez l’instruction **ALTER LOGIN** pour modifier une connexion existante, par exemple, si vous voulez modifier le mot de passe pour la connexion. Pour plus d’informations, voir [ALTER LOGIN (base de données SQL)](https://msdn.microsoft.com/library/ms189828.aspx). L’instruction **ALTER LOGIN** doit être exécutée dans la base de données **principale** . Revenez à la fenêtre de requête qui est connectée à cette base de données. L’instruction suivante modifie la connexion **login1** pour réinitialiser le mot de passe. Remplacez **NouveauMotDePasse** par le mot de passe de votre choix et **oldPassword** avec mot de passe actuel pour la connexion.

        ALTER LOGIN login1
        WITH PASSWORD = 'newPassword'
        OLD_PASSWORD = 'oldPassword';

-   Pour supprimer une connexion existante, utilisez instruction **DROP LOGIN** . Supprimer une connexion au niveau du serveur supprime également les comptes d’utilisateur de base de données associée. Pour plus d’informations, voir [L’instruction DROP DATABASE (base de données SQL)](https://msdn.microsoft.com/library/ms178613.aspx). L’instruction **DROP LOGIN** doit être exécutée dans la base de données **principale** . L’instruction supprime la connexion **login1** .

        DROP LOGIN login1;

-   La base de données principale comporte la **sys.sql\_connexions** afficher que vous pouvez utiliser pour afficher les connexions. Pour afficher toutes les connexions existantes, exécutez l’instruction suivante :

        SELECT * FROM sys.sql_logins;

## <a name="monitor-sql-database-using-dynamic-management-views"></a>Surveiller à l’aide des vues de gestion dynamique de la base de données SQL

Base de données SQL prend en charge plusieurs vues de gestion dynamiques que vous pouvez utiliser pour surveiller une base de données individuel. Quelques exemples du type de données du moniteur que vous pourrez le retrouver grâce à ces vues sont les suivants. Pour obtenir des informations détaillées et des exemples d’utilisation plus, voir [analyse de la base de données SQL à l’aide de vues de gestion dynamique](https://msdn.microsoft.com/library/azure/ff394114.aspx).

-   Requête d’une vue de gestion dynamique nécessite des autorisations **d’État de base de données d’affichage** . Pour accorder l’autorisation **Afficher l’état de base de données** à un utilisateur de base de données, vous connecter à la base de données et exécutez l’instruction suivante sur la base de données :

        GRANT VIEW DATABASE STATE TO login1User;

-   Calculer la taille de la base de données à l’aide du **sys.dm\_db\_partition\_statistiques** affichage. La **sys.dm\_db\_partition\_statistiques** affichage renvoie des informations de page et le nombre de lignes pour toutes les partitions dans la base de données, vous pouvez utiliser pour calculer la taille de la base de données. La requête suivante renvoie la taille de votre base de données en mégaoctets :

        SELECT SUM(reserved_page_count)*8.0/1024
        FROM sys.dm_db_partition_stats;   

-   Utiliser la **sys.dm\_exécution\_connexions** et **sys.dm\_exécution\_sessions** vues pour récupérer les informations sur les connexions utilisateur en cours et internes tâches associées à la base de données. La requête suivante renvoie des informations sur la connexion en cours :

        SELECT
            e.connection_id,
            s.session_id,
            s.login_name,
            s.last_request_end_time,
            s.cpu_time
        FROM
            sys.dm_exec_sessions s
            INNER JOIN sys.dm_exec_connections e
              ON s.session_id = e.session_id;

-   Utiliser la **sys.dm\_exécution\_requête\_statistiques** mis en cache qui permet de récupérer les statistiques de performances agrégées pour les plans de requête. La requête suivante renvoie des informations sur les requêtes de cinq supérieure classés selon des temps processeur moyen.

        SELECT TOP 5 query_stats.query_hash AS "Query Hash",
            SUM(query_stats.total_worker_time), SUM(query_stats.execution_count) AS "Avg CPU Time",
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
 
 
