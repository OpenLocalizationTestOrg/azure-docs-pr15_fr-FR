<properties
    pageTitle="Surveiller et gérer un pool de base de données élastique avec c# | Microsoft Azure"
    description="Utiliser des techniques de développement c# de base de données pour gérer un regroupement élastique de base de données de base de données SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="csharp"
    ms.workload="data-management"
    ms.date="10/04/2016"
    ms.author="sstein"/>

# <a name="monitor-and-manage-an-elastic-database-pool-with-cx23"></a>Surveiller et gérer un pool de base de données élastique avec C & #x 23 ; 

> [AZURE.SELECTOR]
- [Portail Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)


Apprenez à gérer un [pool élastique de base de données](sql-database-elastic-pool.md) à l’aide de C & #x 23 ;. 

>[AZURE.NOTE] Plusieurs nouvelles fonctionnalités de base de données SQL sont uniquement pris en charge lorsque vous utilisez le [modèle de déploiement d’Azure le Gestionnaire de ressources](../azure-resource-manager/resource-group-overview.md), vous devez toujours utiliser plus tard **bibliothèque de gestion des base de données SQL Azure pour .NET ([documents](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [NuGet Package](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))**. Les anciennes [bibliothèques basée sur le modèle de déploiement classique](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql) sont prises en charge pour la compatibilité descendante, afin que nous vous recommandons d’utiliser les bibliothèques de gestionnaire de ressources en fonction de plus récentes.

Pour effectuer les étapes décrites dans cet article, vous devez les éléments suivants :

- Un pool élastique (le pool que vous voulez gérer). Pour créer un regroupement, voir [créer un pool de base de données élastique avec c#](sql-database-elastic-pool-create-csharp.md).
- Visual Studio. Pour obtenir une copie gratuite de Visual Studio, consultez la page [Téléchargements de Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) .


## <a name="move-a-database-into-an-elastic-pool"></a>Déplacer une base de données dans un pool élastique

Vous pouvez déplacer une base de données autonome dans un pool facteur.  

    // Retrieve current database properties.

    currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;

    // Configure create or update parameters with existing property values, override those to be changed.
    DatabaseCreateOrUpdateParameters updatePooledDbParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentDatabase.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
            Collation = currentDatabase.Properties.Collation,
        }
    };

    // Update the database.
    var dbUpdateResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updatePooledDbParameters);

## <a name="list-databases-in-an-elastic-pool"></a>Liste des bases de données dans un pool élastique

Pour extraire tous les bases de données dans un pool, appelez la méthode [ListDatabases](https://msdn.microsoft.com/library/microsoft.azure.management.sql.elasticpooloperationsextensions.listdatabases) .

    //List databases in the elastic pool
    DatabaseListResponse dbListInPool = sqlClient.ElasticPools.ListDatabases("resourcegroup-name", "server-name", "ElasticPool1");
    Console.WriteLine("Databases in Elastic Pool {0}", "server-name.ElasticPool1");
    foreach (Database db in dbListInPool)
    {
        Console.WriteLine("  Database {0}", db.Name);
    }

## <a name="change-performance-settings-of-a-pool"></a>Modifier les paramètres de performances d’un pool

Récupérer existant les propriétés du pool. Modifiez les valeurs et exécuter la méthode CreateOrUpdate.

    var currentPool = sqlClient.ElasticPools.Get("resourcegroup-name", "server-name", "ElasticPool1").ElasticPool;

    // Configure create or update parameters with existing property values, override those to be changed.
    ElasticPoolCreateOrUpdateParameters updatePoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = currentPool.Location,
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = currentPool.Properties.Edition,
            DatabaseDtuMax = 50, /* Setting DatabaseDtuMax to 50 limits the eDTUs that any one database can consume */
            DatabaseDtuMin = 10, /* Setting DatabaseDtuMin above 0 limits the number of databases that can be stored in the pool */
            Dtu = (int)currentPool.Properties.Dtu,
            StorageMB = currentPool.Properties.StorageMB,  /* For a Standard pool there is 1 GB of storage per eDTU. */
        }
    };

    newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);


## <a name="latency-of-elastic-pool-operations"></a>Latence des opérations sur les listes élastique

- Modification de l’eDTUs min par base de données ou max eDTUs par base de données en général se termine en cinq minutes ou moins.
- Durée pour modifier la taille du pool (eDTUs) dépend de la taille combinée de toutes les bases de données dans le pool. Modifications moyenne 90 minutes ou moins par 100 Go. Par exemple, si l’espace total de toutes les bases de données dans le pool est 200 Go, puis le temps de latence escompté pour modifier l’eDTU pool par pool est de 3 heures ou moins.




## <a name="additional-resources"></a>Ressources supplémentaires

- [Codes d’erreur SQL pour les applications clientes de base de données SQL : erreur de connexion et d’autres problèmes de base de données](sql-database-develop-error-messages.md).
- [Base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)
- [API de gestion des ressources Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx)
- [Créer un nouveau pool de base de données élastique avec c#](sql-database-elastic-pool-create-csharp.md)
- [Quand un pool élastique de base de données doit être utilisé ?](sql-database-elastic-pool-guidance.md)
- Voir [mise à l’échelle avec la base de données SQL Azure](sql-database-elastic-scale-introduction.md): outils de base de données élastique permet de horizontale, de déplacer des données, de la requête, ou créer des transactions.

