<properties 
    pageTitle="Interroger plusieurs éclater | Microsoft Azure" 
    description="Exécuter des requêtes sur milieu des fragments à l’aide de la bibliothèque de client élastique de base de données." 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="torsteng" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/12/2016" 
    ms.author="torsteng"/>

# <a name="multi-shard-querying"></a>Interrogation de plusieurs partagé

## <a name="overview"></a>Vue d’ensemble

Avec les [Outils de base de données élastique](sql-database-elastic-scale-introduction.md), vous pouvez créer des solutions de base de données sharded. **Interroger plusieurs partagé** est utilisé pour les tâches telles que la collection de sites/rapport de données qui nécessitent l’exécution d’une requête qui s’étend sur plusieurs milieu des fragments. (Comparez ceci au [routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md), qui effectue tout le travail sur un seul partagé.) 

## <a name="overview"></a>Vue d’ensemble

1. Obtenir un [**RangeShardMap**](https://msdn.microsoft.com/library/azure/dn807318.aspx) ou [**ListShardMap**](https://msdn.microsoft.com/library/azure/dn807370.aspx) à l’aide de la [**TryGetRangeShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx), la [**TryGetListShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx)ou la méthode [**GetShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx) . Voir [**construire une ShardMapManager**](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) et [**obtenir un RangeShardMap ou ListShardMap**](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap).
2. Créer un objet **[MultiShardConnection](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection.aspx)** .
2. Créer un **[MultiShardCommand](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.aspx)**. 
3. Définissez la **[propriété CommandText](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.commandtext.aspx#P:Microsoft.Azure.SqlDatabase.ElasticScale.Query.MultiShardCommand.CommandText)** sur une commande T-SQL.
3. Exécuter la commande en appelant la **[méthode ExecuteReader](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.executereader.aspx)**.
4. Afficher les résultats à l’aide de la **[classe MultiShardDataReader](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader.aspx)**. 

## <a name="example"></a>Exemple

Le code suivant illustre l’utilisation de multiples éclater interroger à l’aide d’une donnée **ShardMap** nommé *myShardMap*. 

    using (MultiShardConnection conn = new MultiShardConnection( 
                                        myShardMap.GetShards(), 
                                        myShardConnectionString) 
          ) 
    { 
    using (MultiShardCommand cmd = conn.CreateCommand())
           { 
            cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable"; 
            cmd.CommandType = CommandType.Text; 
            cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn; 
            cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults; 

            using (MultiShardDataReader sdr = cmd.ExecuteReader()) 
                { 
                    while (sdr.Read())
                        { 
                            var c1Field = sdr.GetString(0); 
                            var c2Field = sdr.GetFieldValue<int>(1); 
                            var c3Field = sdr.GetFieldValue<Int64>(2);
                        } 
                } 
           } 
    } 

 
Une différence essentielle est la construction de partagé à plusieurs connexions. L’emplacement dans lequel **SqlConnection** fonctionne sur une seule base de données, la **MultiShardConnection** prend une ***collection de sites du milieu des fragments*** comme entrée. Remplir la collection de milieu des fragments d’un mappage partagé. La requête est exécutée puis sur la collection de milieu des fragments à l’aide de **UNION ALL** sémantique disposer d’un seul résultat global. Vous pouvez également le nom de l’éclater où provient de la ligne peut être ajouté au résultat de la commande à l’aide de la propriété **ExecutionOptions** . 

Notez l’appel à **myShardMap.GetShards()**. Cette méthode récupère milieu des fragments tous les la carte partagé et fournit un moyen facile pour exécuter une requête sur toutes les bases de données pertinentes. La collection de milieu des fragments d’une requête à plusieurs partagée peut être affinée davantage en effectuant une requête LINQ sur la collection retournée à partir de l’appel vers **myShardMap.GetShards()**. En combinaison avec la stratégie de résultats partielle, la fonctionnalité actuelle dans l’interrogation de plusieurs éclater a été conçue pour adaptés aux dizaines des centaines de milieu des fragments.

Une limitation avec interroger plusieurs partagé est actuellement l’absence de validation pour milieu des fragments et shardlets sont interrogées. Pendant que le routage dépendant des données vérifie qu’un éclater donné fait partie de la carte partagé au moment de l’interrogation, éclater à plusieurs requêtes sans effectuer ce test. Cela peut affecter les requêtes multiples partagés en cours d’exécution sur les bases de données qui ont été supprimés de la carte partagé.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Éclater à plusieurs requêtes et les opérations de fusion de fractionnement

Éclater à plusieurs requêtes ne pas vérifient si shardlets sur la base de données faisant participent à des opérations de fusion et fractionnement en cours. (Voir [mise à l’échelle à l’aide de l’outil de fusion et fractionnement élastique de base de données](sql-database-elastic-scale-overview-split-and-merge.md)). Cela peut entraîner des incohérences dans laquelle affichent les lignes de la même shardlet pour plusieurs bases de données dans la même requête partagée à plusieurs. N’oubliez pas de ces limitations et en considération écoulement opérations split-fusion en cours et les modifications apportées au mappage d’éclater lors de l’exécution des requêtes multiples partagés.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

## <a name="see-also"></a>Voir aussi
Les classes **[System.Data.SqlClient](http://msdn.microsoft.com/library/System.Data.SqlClient.aspx)** et méthodes.


Gérer le milieu des fragments à l’aide de la [bibliothèque de client de base de données élastique](sql-database-elastic-database-client-library.md). Inclut un espace de noms appelé [Microsoft.Azure.SqlDatabase.ElasticScale.Query](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.aspx) qui fournit la capacité à plusieurs milieu des fragments à l’aide d’une requête unique et le résultat de la requête. Il fournit une abstraction de requête dans une collection de milieu des fragments. Il fournit également des stratégies de remplacement d’exécution, notamment partielles résultats, de traiter les échecs lors de la recherche sur plusieurs milieu des fragments.  

 