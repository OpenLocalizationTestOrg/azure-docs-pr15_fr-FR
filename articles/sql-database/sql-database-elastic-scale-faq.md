<properties 
    pageTitle="Échelle élastique SQL Azure FAQ | Microsoft Azure" 
    description="Forum aux Questions sur échelle élastique de base de données SQL Azure." 
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
    ms.date="05/03/2016" 
    ms.author="ddove"/>

# <a name="elastic-database-tools-faq"></a>Outils de base de données élastique Forum aux questions 

#### <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>Si j’utilise un client d’unique par partagé et aucune clé ont, comment remplir la clé ont pour les informations de schéma ?

L’objet info schéma sert uniquement à fractionner les scénarios de fusion. Si une application est, par nature, seul-client, puis il ne nécessite pas de l’outil de fractionnement fusionner et par conséquent, il est inutile pour remplir l’objet d’informations de schéma.

#### <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>J’ai configuré une base de données et j’ai déjà un responsable de carte éclater, comment enregistrer cette nouvelle base de données comme un partagé ?

Voir **[Ajouter un partagé à l’application à l’aide de la bibliothèque de client élastique de base de données](sql-database-elastic-scale-add-a-shard.md)**. 

#### <a name="how-much-do-elastic-database-tools-cost"></a>Les outils de base de données élastique coût ?

À l’aide de la bibliothèque de client de base de données élastique n’entraîne pas les coûts. Allocation des coûts uniquement pour les bases de données SQL Azure que vous utilisez pour milieu des fragments et le Gestionnaire de carte éclater, ainsi que les rôles d’administrateur web/travail que vous mise en service pour l’outil fractionnée fusionner.

#### <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>Pourquoi mes informations d’identification ne fonctionnent pas lorsque j’ajoute un partagé à partir d’un autre serveur ?
N’utilisez pas les informations d’identification sous forme de « utilisateur ID=username@servername”, à la place utilise simplement « ID utilisateur = nom d’utilisateur ».  En outre, n’oubliez pas que le nom de connexion « nom d’utilisateur » dispose des autorisations sur le partagé.

#### <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>Ai-je besoin créer une carte partagé et remplir milieu des fragments chaque fois que vous lancez mes applications ?

Non, la création du Gestionnaire de carte partagé (par exemple, **[ShardMapManagerFactory.CreateSqlShardMapManager](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager.aspx)**) est une opération unique.  Votre application doit utiliser l’appel **[ShardMapManagerFactory.TryGetSqlShardMapManager()](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx)** au moment de démarrage d’application.  Il ne convient qu’un tel appel par domaine d’application.

#### <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>J’ai des questions sur l’utilisation des outils de base de données élastique, comment obtenir les réponses ? 

Veuillez contacter nous sur le [forum de la base de données SQL Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted).

#### <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>Lorsque j’obtiens une connexion de base de données en utilisant une clé ont, je peux toujours interroger les données d’autres touches ont dans le même partagé.  S’agit-il par la conception ?

Les API échelle élastique vous offre une connexion à la base de données appropriée pour votre clé ont, mais ne fournissent pas le filtrage clés ont.  Ajouter des clauses **WHERE** à votre requête pour limiter l’étendue à la clé ont fourni, si nécessaire.

#### <a name="can-i-use-a-different-azure-database-edition-for-each-shard-in-my-shard-set"></a>Puis-je utiliser une édition de base de données Azure différente pour chaque partagé dans mon ensemble partagé ?

Oui, un partagé est une base de données individuel, et donc un partagé peut être une édition Premium tandis qu’un autre une édition Standard. En outre, l’édition d’un partagé peut évoluer vers le haut ou vers le bas plusieurs fois pendant la durée de vie de la partagé.

#### <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>Ne prend la mise en service de fractionnement fusion outil (ou supprimer) une base de données pendant une opération de fractionnement ou de fusion ? 

Non. Pour les opérations de **Fractionner** la base de données cible doit exister avec le schéma approprié et être inscrit avec le Gestionnaire de carte partagé.  Pour les opérations de **fusion** , vous devez supprimer le partagé à partir du Gestionnaire de carte partagé et puis supprimez la base de données.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 