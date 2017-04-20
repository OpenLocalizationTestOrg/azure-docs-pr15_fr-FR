<properties 
    pageTitle="Gestion des informations d’identification dans la bibliothèque de client de base de données élastique | Microsoft Azure" 
    description="Comment définir le niveau d’informations d’identification, d’administration en lecture seule, pour les applications de base de données élastique" 
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

# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>Informations d’identification utilisées pour accéder à la bibliothèque de client de base de données élastique

La [bibliothèque de client de base de données élastique](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) utilise trois différents types d’informations d’identification pour accéder au [Gestionnaire de carte partagé](sql-database-elastic-scale-shard-map-management.md). Selon qu’il soit nécessaire, utilisez les informations d’identification avec le niveau le plus bas d’accès possibles.

* **Informations d’identification de gestion**: pour créer ou manipuler un responsable de carte partagé. (Voir le [Glossaire](sql-database-elastic-scale-glossary.md)). 
* **Informations d’identification Access**: pour accéder à un manager carte éclater existant pour obtenir des informations sur milieu des fragments.
* **Informations d’identification de connexion**: se connecter à milieu des fragments. 

Voir aussi [bases de gestion des données et connexions dans la base de données SQL Azure](sql-database-manage-logins.md). 
 
## <a name="about-management-credentials"></a>Informations d’identification d’administration

Informations d’identification d’administration sont utilisées pour créer un objet [**ShardMapManager**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) pour les applications qui manipuler des cartes partagé. (Par exemple, voir [Ajouter un partagé à l’aide des outils de base de données élastique](sql-database-elastic-scale-add-a-shard.md) et [routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md)) L’utilisateur de la bibliothèque de client échelle élastique crée les utilisateurs SQL et les connexions SQL et vérifie que chacun reçoit les autorisations en lecture/écriture sur la base de données de carte éclater global et tous les éclater bases de données. Ces informations d’identification sont utilisées pour mettre à jour la table éclater global et les cartes éclater local lorsque les modifications apportées à la carte éclater sont effectuées. Par exemple, utiliser les informations d’identification d’administration pour créer l’objet de gestionnaire de carte partagé (à l’aide de [**GetSqlShardMapManager**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx): 

    // Obtain a shard map manager. 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
            smmAdminConnectionString, 
            ShardMapManagerLoadPolicy.Lazy 
    ); 

La variable **smmAdminConnectionString** est une chaîne de connexion qui contient les informations d’identification d’administration. L’ID utilisateur et mot de passe offre un accès en lecture/écriture à base de données de carte partagé et milieu des fragments individuels. La chaîne de connexion gestion inclut également le nom du serveur et le nom de base de données pour identifier la base de données de carte global partagé. Voici une chaîne de connexion classique à cet effet :

     "Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;” 

N’utilisez pas de valeurs sous la forme de "username@server"—instead contenter d’utiliser la valeur « username ».  C’est parce que les informations d’identification doivent travailler par rapport à la base de données Gestionnaire de carte partagé et le milieu des fragments individuels, qui peuvent être sur des serveurs différents.

## <a name="access-credentials"></a>Informations d’accès
  
Lorsque vous créez un éclater Gestionnaire de carte dans une application qui administrer pas éclater cartes, utilisez des références disposant d’autorisations en lecture seule sur la carte global partagé. Les informations récupérées à partir de la carte global partagé sous ces informations d’identification sont utilisées pour le [routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md) et pour remplir le cache de mappage partagé sur le client. Les informations d’identification sont fournies par le biais du même modèle d’appel pour **GetSqlShardMapManager** comme indiqué ci-dessus : 

    // Obtain shard map manager. 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
            smmReadOnlyConnectionString, 
            ShardMapManagerLoadPolicy.Lazy
    );  

Notez l’utilisation de la **smmReadOnlyConnectionString** afin de refléter l’utilisation des informations d’identification différentes pour cet accès au nom d’utilisateurs **non administrateurs** : ces informations d’identification ne doivent pas fournir autorisations d’écriture sur la carte global partagé. 

## <a name="connection-credentials"></a>Informations d’identification de connexion 

Informations d’identification supplémentaires sont nécessaires lorsque vous utilisez la méthode [**OpenConnectionForKey**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx) pour accéder à un partagé associé à une clé ont. Ces informations d’identification doivent fournir des autorisations pour l’accès en lecture seule aux tables carte éclater local résidant sur le partagé. Cela est nécessaire pour effectuer une validation de connexion pour le routage dépendant des données sur le partagé. Cet extrait de code permet d’accès aux données dans le contexte de routage dépendant des données : 
 
    using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>( 
    targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate)) 

Dans cet exemple, **smmUserConnectionString** contient la chaîne de connexion pour les informations d’identification utilisateur. Pour la base de données SQL Azure, voici une chaîne de connexion par défaut pour les informations d’identification utilisateur : 

    "User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  

Comme avec les informations d’identification administrateur, procédez comme valeurs pas sous la forme de "username@server". Utilisez simplement le « nom d’utilisateur ».  Notez également que la chaîne de connexion ne contient pas un nom de serveur et un nom de base de données. C’est parce que l’appel **OpenConnectionForKey** dirigera automatiquement la connexion à l’éclater correct en fonction de la clé. Par conséquent, le nom de la base de données et le nom du serveur ne sont pas fournis. 

## <a name="see-also"></a>Voir aussi
[Gestion des bases de données et connexions dans la base de données SQL Azure](sql-database-manage-logins.md)

[Protéger votre base de données SQL](sql-database-security.md)

[Mise en route des tâches de base de données élastique](sql-database-elastic-jobs-getting-started.md)

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 