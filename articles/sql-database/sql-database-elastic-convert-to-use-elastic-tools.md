<properties
   pageTitle="Migrer des bases de données existantes à horizontale | Microsoft Azure"
   description="Convertir des bases de données sharded pour utiliser les outils de base de données élastique en créant un éclater Gestionnaire de carte"
   services="sql-database"
   documentationCenter=""
   authors="ddove"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="10/24/2016"
   ms.author="ddove"/>

# <a name="migrate-existing-databases-to-scale-out"></a>Migrer des bases de données existantes à l’horizontale

Gérer facilement votre plus grande échelle sharded bases de données existantes à l’aide des outils de base de données de base de données SQL Azure (par exemple, la [bibliothèque de client de base de données élastique](sql-database-elastic-database-client-library.md)). Vous devez d’abord convertir un jeu de bases de données à utiliser le [Gestionnaire de cartes partagé](sql-database-elastic-scale-shard-map-management.md)existant. 

## <a name="overview"></a>Vue d’ensemble
Pour migrer une base de données sharded existante : 

1. Préparer la [base de données Gestionnaire de carte partagé](sql-database-elastic-scale-shard-map-management.md).
2. Créer le mappage partagé.
3. Préparer les milieu des fragments individuels.  
2. Ajouter des mappages au mappage partagé.

Ces techniques peuvent être implémentées à l’aide de la [bibliothèque cliente .NET Framework](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)ou les scripts PowerShell, consultez [DB pour SQL Azure - scripts d’outils de base de données élastique](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db). Ces exemples utilisent les scripts PowerShell.

Pour plus d’informations sur la ShardMapManager, voir [Éclater mapper gestion](sql-database-elastic-scale-shard-map-management.md). Pour une vue d’ensemble des outils de base de données élastique, voir [vue d’ensemble des fonctionnalités de base de données élastique](sql-database-elastic-scale-introduction.md).

## <a name="prepare-the-shard-map-manager-database"></a>Préparer la base de données Gestionnaire de carte partagé

Le Gestionnaire de carte partagé est une base de données spécial qui contient les données pour gérer les bases de données plus grande échelle. Vous pouvez utiliser une base de données existante ou créer une nouvelle base de données. Notez qu’une base de données agissant en tant que gestionnaire de carte partagé ne doit pas être la même base de données comme un partagé. Notez également que le script PowerShell ne crée pas la base de données pour vous. 

## <a name="step-1-create-a-shard-map-manager"></a>Étape 1 : créer un éclater Gestionnaire de carte

    # Create a shard map manager. 
    New-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 
    #<server_name> and <smm_db_name> are the server name and database name 
    # for the new or existing database that should be used for storing 
    # tenant-database mapping information.

### <a name="to-retrieve-the-shard-map-manager"></a>Pour récupérer le Gestionnaire de carte partagé

Après sa création, vous pouvez récupérer le Gestionnaire de carte partagé avec cette applet de commande. Cette étape est nécessaire chaque fois que vous avez besoin d’utiliser l’objet ShardMapManager.

    # Try to get a reference to the Shard Map Manager  
    $ShardMapManager = Get-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 

  
## <a name="step-2-create-the-shard-map"></a>Étape 2 : créer le mappage partagé

Vous devez sélectionner le type de carte éclater pour créer. Le choix dépend de l’architecture de base de données : 

1. Seul client par base de données (pour les termes, voir le [Glossaire](sql-database-elastic-scale-glossary.md)). 
2. Plusieurs clients par base de données (deux types) :
    3. Mappage de liste
    4. Mappage de plage
 

Pour un modèle unique client, créez un mappage d’éclater **mappage de liste** . Le modèle unique client attribue une base de données par client. Il s’agit d’un modèle d’efficace pour les développeurs SaaS comme il simplifie la gestion.

![Mappage de liste][1]

Le modèle client multiples attribue plusieurs clients à une seule base de données (et vous pouvez distribuer des groupes de clients dans plusieurs bases de données). Utilisez ce modèle lorsque vous pensez que chaque client dont les données de petite taille besoins ont. Dans ce modèle, nous affecter une plage de clients à une base de données en utilisant le **mappage de plage**. 
 

![Mappage de plage][2]

Ou vous pouvez implémenter un modèle de base de données client multiples à l’aide d’un *mappage de liste* pour affecter plusieurs clients à une seule base de données. Par exemple, 1 est utilisé pour stocker des informations sur les id de client 1 et 5, et DB2 stocke les données pour le client 7 et client 10. 

![Plusieurs clients sur base de données unique][3] 

**En fonction de votre choix, choisissez une des options suivantes :**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>Option 1 : créer un mappage partagé pour un mappage de liste
Créer un mappage partagé à l’aide de l’objet ShardMapManager. 

    # $ShardMapManager is the shard map manager object. 
    $ShardMap = New-ListShardMap -KeyType $([int]) 
    -ListShardMapName 'ListShardMap' 
    -ShardMapManager $ShardMapManager 
 
 
### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>Option 2 : créer un mappage partagé pour un mappage de plage

Notez que pour utiliser ce modèle de mappage, l’id de client valeurs doit être des plages en continu, et il est acceptable d’avoir intervalle dans la zone plages en ignorant simplement la plage lorsque vous créez les bases de données.

    # $ShardMapManager is the shard map manager object 
    # 'RangeShardMap' is the unique identifier for the range shard map.  
    $ShardMap = New-RangeShardMap 
    -KeyType $([int]) 
    -RangeShardMapName 'RangeShardMap' 
    -ShardMapManager $ShardMapManager 

### <a name="option-3-list-mappings-on-a-single-database"></a>Option 3 : Répertorier les mappages sur une seule base de données
La configuration ce modèle nécessite également la création d’une carte de liste comme indiqué à l’étape 2, l’option 1.

## <a name="step-3-prepare-individual-shards"></a>Étape 3 : Préparer milieu des fragments individuels

Ajoutez chaque partagé (base de données) au Gestionnaire de carte partagé. Cela prépare les bases de données individuels pour stocker les informations de mappage. Exécuter cette méthode sur chaque partagé.
     
    Add-Shard 
    -ShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>'
    # The $ShardMap is the shard map created in step 2.
 

## <a name="step-4-add-mappings"></a>Étape 4 : Ajouter des mappages

L’ajout des mappages dépend du type de carte partagé que vous avez créé. Si vous avez créé une carte de la liste, vous ajoutez des mappages de liste. Si vous avez créé une carte de plage, vous ajoutez les mappages de plage.

### <a name="option-1-map-the-data-for-a-list-mapping"></a>Option 1 : mapper les données pour un mappage de liste

Mapper les données en ajoutant un mappage de liste pour chaque client.  

    # Create the mappings and associate it with the new shards 
    Add-ListMapping 
    -KeyType $([int]) 
    -ListPoint '<tenant_id>' 
    -ListShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 

### <a name="option-2-map-the-data-for-a-range-mapping"></a>Option 2 : mapper les données pour un mappage de plage

Ajouter les mappages de plage pour tous les la plage d’id client – associations de base de données :

    # Create the mappings and associate it with the new shards 
    Add-RangeMapping 
    -KeyType $([int]) 
    -RangeHigh '5' 
    -RangeLow '1' 
    -RangeShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 


### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-a-single-database"></a>Étape 4 option 3 : faire correspondre les données de plusieurs clients sur une seule base de données

Pour chaque client, exécutez la ListMapping ajouter (option 1, ci-dessus). 


## <a name="checking-the-mappings"></a>Les mappages de la vérification

Informations sur les milieu des fragments existants et les mappages associés peuvent être interrogées à l’aide de commandes suivantes :  

    # List the shards and mappings 
    Get-Shards -ShardMap $ShardMap 
    Get-Mappings -ShardMap $ShardMap 

## <a name="summary"></a>Résumé

Une fois que vous avez terminé la configuration, vous pouvez commencer à utiliser la bibliothèque de client élastique de base de données. Vous pouvez également utiliser [routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md) et [requête éclater multiples](sql-database-elastic-scale-multishard-querying.md).

## <a name="next-steps"></a>Étapes suivantes


Obtenez les scripts PowerShell de [sripts des outils de base de données-ELASTIQUE base de données SQL Azure](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

Les outils sont également sur GitHub : [Outils Azure/élastique-db](https://github.com/Azure/elastic-db-tools).

Utilisez l’outil de fusion de fractionnement pour déplacer des données vers ou à partir d’un modèle de plusieurs utilisateurs à un modèle de client unique. Voir [outil de fusion fractionnée](sql-database-elastic-scale-get-started.md).

## <a name="additional-resources"></a>Ressources supplémentaires

Pour plus d’informations sur les modèles d’architecture données courantes des applications de base de données clients à plusieurs logiciels en tant que service (SaaS), voir [Modèles de conception pour les Applications de SaaS client à plusieurs avec la base de données SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

## <a name="questions-and-feature-requests"></a>Questions et des demandes de fonctionnalités

Vous avez des questions, veuillez contacter nous sur le [forum de la base de données SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) et les requêtes de fonctionnalité, veuillez les ajouter à la [base de données SQL évaluations](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/sql-database-elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/sql-database-elastic-convert-to-use-elastic-tools/multipleonsingledb.png
 
