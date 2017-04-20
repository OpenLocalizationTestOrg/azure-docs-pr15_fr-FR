<properties
    pageTitle="Gestionnaire de cartes compteurs de performance pour partagé"
    description="ShardMapManager classe et données dépendantes routage des compteurs de performance"
    services="sql-database"
    documentationCenter=""
    manager="jhubbard"
    authors="SilviaDoomra"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/23/2016"
    ms.author="SilviaDoomra"/>

# <a name="performance-counters-for-shard-map-manager"></a>Gestionnaire de cartes compteurs de performance pour partagé

Vous pouvez capturer les performances du [Gestionnaire de carte éclater](sql-database-elastic-scale-shard-map-management.md), en particulier lorsque vous utilisez le [routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md). Compteurs sont créés avec des méthodes de la classe Microsoft.Azure.SqlDatabase.ElasticScale.Client.  

Compteurs sont utilisés pour suivre les performances des opérations de [routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md) . Ces compteurs sont accessibles dans l’Analyseur de performances, sous la catégorie « Élastique de base de données : éclater gestion ».

**Pour la version la plus récente :** Accédez à [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Voir aussi [mettre à niveau une application pour utiliser la dernière bibliothèque client élastique de base de données](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Conditions préalables

* Pour créer la catégorie performances et compteurs, l’utilisateur doit faire partie du groupe **administrateurs** local sur l’ordinateur qui héberge l’application.  

* Pour créer une instance de compteur de performance et mettre à jour les compteurs, l’utilisateur doit être membre du groupe **administrateurs** ou **Les utilisateurs du moniteur performances** . 

## <a name="create-performance-category-and-counters"></a>Créer des compteurs et catégorie de performance 

Pour créer les compteurs, appelez la méthode CreatePeformanceCategoryAndCounters de la [classe ShardMapManagmentFactory](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.aspx). Seul un administrateur peut exécuter la méthode : 

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

Vous pouvez également utiliser [ce](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) script PowerShell pour exécuter la méthode. La méthode crée des compteurs de performance suivants :  

* **Mappages mis en cache**: nombre de mappages mis en cache pour le mappage partagé.
*  **Opérations de DDR/s**: taux dépendantes routage des opérations de données de la carte partagé. Ce compteur est mis à jour lorsqu’un appel au [OpenConnectionForKey()](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx) se traduit par une connexion à la partagé de destination. 
*  **Mappage du cache de recherche correspondances/sec**: taux d’opérations de recherche réussies sur le cache de mappages dans la carte partagé. 
*  **Mappage du cache de recherche échecs/sec**: taux échec recherche d’opérations de cache pour les mappages du mappage partagé.
*  **Mappages ajouté ou mis à jour dans le cache/s**: fréquence lesquelles mappages sont ajoutés ou mis à jour dans cache du mappage partagé. 
*  **Les mappages supprimés du cache/s**: taux auquel mappages sont supprimés de cache pour le mappage partagé. 

Compteurs de performance sont créés pour chaque carte partagé mis en cache par processus.  


## <a name="notes"></a>Notes
Les événements suivants déclenchent la création des compteurs de performance :  

* Initialisation de la [ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) avec [chargement puissions](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy.aspx), si la ShardMapManager contient les cartes partagé. Cela inclut les [GetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx?f=255&MSPPError=-2147217396#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerFactory.GetSqlShardMapManager%28System.String,Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerLoadPolicy%29) et les méthodes [TryGetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx) .
* Recherche de réussite d’un mappage partagé (à l’aide de [GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx) ou [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx)). 

* Création réussie de carte partagé à l’aide de CreateShardMap().

Les compteurs de performance seront mise à jour par toutes les opérations de cache effectuées sur la carte partagé et les mappages. Suppression réussie de la carte partagé à l’aide de DeleteShardMap () reults à la suppression de l’instance de compteurs de performance.  

## <a name="best-practices"></a>Meilleures pratiques

* Création de la catégorie de performance et de compteurs doit être effectuée qu’une seule fois avant la création d’objet ShardMapManager. Chaque exécution de la commande CreatePerformanceCategoryAndCounters() efface les compteurs précédents (perte de données signalées par toutes les instances) et crée de nouvelles.  

* Instances de compteur de performances créés par processus. Tout blocage d’application ou la suppression d’une carte partagé à partir du cache entraîne la suppression des instances de compteurs de performances.  

### <a name="see-also"></a>Voir aussi

[Vue d’ensemble des fonctionnalités de base de données élastique](sql-database-elastic-scale-introduction.md)  

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->

