<properties 
    pageTitle="Ajout d’un partagé à l’aide des outils de base de données élastique | Microsoft Azure" 
    description="Comment utiliser élastique API échelle pour ajouter nouveau milieu des fragments à un éclater définie." 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="ddove" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove"/>

# <a name="adding-a-shard-using-elastic-database-tools"></a>Ajout d’un partagé à l’aide des outils de base de données élastique

## <a name="to-add-a-shard-for-a-new-range-or-key"></a>Pour ajouter un partagé pour une nouvelle plage ou de la clé  

Les applications doivent souvent simplement ajouter nouveau milieu des fragments pour gérer les données que l'on à partir des clés ou des plages de clés, pour une carte partagé qui existe déjà. Par exemple, une application sharded par ID client peut nécessiter de prévoir un nouveau partagé pour un nouveau client, ou mensuelle sharded données devrez un nouveau partagé mis en service avant le début de chaque nouveau mois. 

Si la nouvelle plage de valeurs de clé n’est pas déjà partie d’un mappage existant, il est très simple ajouter le nouveau partagé et associer la nouvelle clé d’ou la plage à cette partagé. 

### <a name="example--adding-a-shard-and-its-range-to-an-existing-shard-map"></a>Exemple : ajout d’un partagé et sa plage à un mappage partagé existant
Cet exemple utilise la [TryGetShard](https://msdn.microsoft.com/library/azure/dn823929.aspx) la [CreateShard](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard.aspx), méthodes [CreateRangeMapping](https://msdn.microsoft.com/library/azure/dn807221.aspx#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.RangeShardMap`1.CreateRangeMapping(Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.RangeMappingCreationInfo{`0})) et crée une instance de la classe [ShardLocation](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation.shardlocation.aspx#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardLocation.) . Dans l’exemple ci-dessous, une base de données nommée **sample_shard_2** et tous les objets de schéma nécessaires à l’intérieur créées pour mettre en attente de plage [300, 400).  

    // sm is a RangeShardMap object.
    // Add a new shard to hold the range being added. 
    Shard shard2 = null; 

    if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
    { 
        shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
    } 

    // Create the mapping and associate it with the new shard 
    sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                            (new Range<long>(300, 400), shard2, MappingStatus.Online)); 


Comme alternative, vous pouvez utiliser Powershell pour créer une nouvelle carte partagé. Un exemple est disponible [ici](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).
## <a name="to-add-a-shard-for-an-empty-part-of-an-existing-range"></a>Pour ajouter un partagé pour une partie vide d’une plage existante  

Dans certains cas, vous avez déjà mappé une plage à une partagé et partiellement avec les données, mais vous voulez maintenant qu’à venir données dirigé vers une autre partagé. Par exemple, vous partagé par intervalle jours et devez déjà alloués 50 jours un partagé, mais jour 24, vous souhaitez que les données futures page d’arrivée dans une autre partagé. base de données élastique [outil de fusion de fractionnement](sql-database-elastic-scale-overview-split-and-merge.md) peuvent effectuer cette opération, mais si déplacement de données n’est pas nécessaire (par exemple, les données de la plage de jours [25, 50), c'est-à-dire, jour 25 inclus à 50 exclusif, n’existe pas encore) vous pouvez effectuer ceci entièrement en utilisant les API de gestion de carte éclater directement.

### <a name="example-splitting-a-range-and-assigning-the-empty-portion-to-a-newly-added-shard"></a>Exemple : fractionnement d’une plage et en affectant la partie vide à un partagé que vous venez d’ajouter

Une base de données nommée « sample_shard_2 » et tous les objets de schéma nécessaires à l’intérieur ont été créés.  

 
    // sm is a RangeShardMap object.
    // Add a new shard to hold the range we will move 
    Shard shard2 = null; 

    if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
    { 
    
        shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
    } 

    // Split the Range holding Key 25 

    sm.SplitMapping(sm.GetMappingForKey(25), 25); 

    // Map new range holding [25-50) to different shard: 
    // first take existing mapping offline 
    sm.MarkMappingOffline(sm.GetMappingForKey(25)); 
    // now map while offline to a different shard and take online 
    RangeMappingUpdate upd = new RangeMappingUpdate(); 
    upd.Shard = shard2; 
    sm.MarkMappingOnline(sm.UpdateMapping(sm.GetMappingForKey(25), upd)); 

**Important**: utilisez cette méthode uniquement si vous êtes sûr que la plage pour le mappage mis à jour est vide.  Méthodes ci-dessus ne vérifient pas les données de la plage est déplacée, il est préférable d’inclure des vérifications dans votre code.  Si existe des lignes dans la plage est déplacée, la distribution de données réelles ne correspondront pas la carte partagé mis à jour. Utilisez l' [outil de fusion de fractionnement](sql-database-elastic-scale-overview-split-and-merge.md) pour effectuer l’opération à la place dans ce cas.  


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 
