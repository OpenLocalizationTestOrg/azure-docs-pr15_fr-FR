<properties 
    pageTitle="Création ou déplacement d’une base de données SQL Azure dans un pool élastique à l’aide de T-SQL | Microsoft Azure" 
    description="T-SQL permet de créer une base de données SQL Azure dans un pool élastique. Ou, utilisez T-SQL pour déplacer la datbase et déconnexion des pools." 
    services="sql-database" 
    documentationCenter="" 
    authors="srinia" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="05/27/2016"
    ms.author="srinia"/>

# <a name="monitor-and-manage-an-elastic-database-pool-with-transact-sql"></a>Surveiller et gérer un pool de base de données élastique avec Transact-SQL  

> [AZURE.SELECTOR]
- [Portail Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)

Utiliser les commandes [Créer une base de données (de base de données SQL Azure)](https://msdn.microsoft.com/library/dn268335.aspx) et [Database(Azure SQL Database) Alter](https://msdn.microsoft.com/library/mt574871.aspx) pour créer et déplacer des bases de données vers et depuis des pools élastiques. Le pool élastique doit exister avant de pouvoir utiliser ces commandes. Ces commandes affectent uniquement les bases de données. La création de nouveaux pools et le paramétrage des propriétés du pool (par exemple, eDTUs min et max) ne peut pas être modifiés avec des commandes T-SQL.

## <a name="create-a-new-database-in-an-elastic-pool"></a>Créer une nouvelle base de données dans un pool élastique
Utilisez la commande Créer une base de données avec l’option SERVICE_OBJECTIVE.   

    CREATE DATABASE db1 ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3M100] ));
    -- Create a database named db1 in a pool named S3M100.

Toutes les bases de données dans un pool élastique héritent le niveau de service du pool élastique (Basic, Standard, Premium). 


## <a name="move-a-database-between-elastic-pools"></a>Déplacer une base de données entre des pools élastiques
Utilisez la commande modifier la base de données avec le modifier et définir le SERVICE\_option OBJECTIVE comme élastique\_du POOL ; Définissez le nom le nom de la liste cible.

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [PM125] ));
    -- Move the database named db1 to a pool named P1M125  

## <a name="move-a-database-into-an-elastic-pool"></a>Déplacer une base de données dans un pool élastique 
Utilisez la commande modifier la base de données avec le modifier et définir le SERVICE\_option OBJECTIVE comme ELASTIC_POOL ; Définissez le nom le nom de la liste cible.

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3100] ));
    -- Move the database named db1 to a pool named S3100.

## <a name="move-a-database-out-of-an-elastic-pool"></a>Retirer une base de données d’un pool élastique
Utilisez la commande modifier la base de données et définissez la SERVICE_OBJECTIVE à l’un des niveaux de performances (S0, S1, etc.).

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = 'S1');
    -- Changes the database into a stand-alone database with the service objective S1.

## <a name="list-databases-in-an-elastic-pool"></a>Liste des bases de données dans un pool élastique
Utiliser la [sys.database\_service \_affichage objectifs](https://msdn.microsoft.com/library/mt712619) pour répertorier toutes les bases de données dans un pool élastique. Connectez-vous au masque de base de données à l’affichage de la requête.

    SELECT d.name, slo.*  
    FROM sys.databases d 
    JOIN sys.database_service_objectives slo  
    ON d.database_id = slo.database_id
    WHERE elastic_pool_name = 'MyElasticPool'; 

## <a name="get-resource-usage-data-for-a-pool"></a>Obtenir des données d’utilisation des ressources pour un pool

Utiliser la [sys.elastic\_pool \_ressource \_affichage statistiques](https://msdn.microsoft.com/library/mt280062.aspx) pour examiner les statistiques d’utilisation de ressources d’un pool élastique sur un serveur logique. Connectez-vous au masque de base de données à l’affichage de la requête.

    SELECT * FROM sys.elastic_pool_resource_stats 
    WHERE elastic_pool_name = 'MyElasticPool'
    ORDER BY end_time DESC;

## <a name="get-resource-usage-for-an-elastic-database"></a>Obtenir de l’utilisation des ressources pour une base de données élastique

Utiliser la [sys.dm\_ db\_ ressource\_affichage statistiques](https://msdn.microsoft.com/library/dn800981.aspx) ou [sys.resource \_affichage statistiques](https://msdn.microsoft.com/library/dn269979.aspx) pour examiner les statistiques d’utilisation de ressources d’une base de données dans un pool élastique. Ce processus est similaire à interroger à l’utilisation des ressources pour une seule base de données.

## <a name="next-steps"></a>Étapes suivantes

Après avoir créé un pool élastique de base de données, vous pouvez gérer élastiques bases de données dans le pool en créant des travaux élastique. Travaux élastique facilite l’exécution des scripts T-SQL par rapport à un nombre quelconque de bases de données dans le pool. Pour plus d’informations, voir [vue d’ensemble des tâches de base de données élastique](sql-database-elastic-jobs-overview.md). 

Voir [mise à l’échelle avec la base de données SQL Azure](sql-database-elastic-scale-introduction.md): outils de base de données élastique permet de horizontale, de déplacer des données, de la requête, ou créer des transactions.
