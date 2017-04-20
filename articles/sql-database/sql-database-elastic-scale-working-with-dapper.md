<properties 
    pageTitle="À l’aide de la bibliothèque de client de base de données élastique Dapper | Microsoft Azure" 
    description="Utilisation de bibliothèque de client de base de données élastique avec Dapper." 
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
    ms.author="torsteng"/>

# <a name="using-elastic-database-client-library-with-dapper"></a>À l’aide de la bibliothèque de client de base de données élastique Dapper 

Ce document est destiné aux développeurs qui s’appuient sur Dapper pour créer des applications, mais que vous souhaitez également utiliser les [Outils de base de données élastique](sql-database-elastic-scale-introduction.md) pour créer des applications mettant en œuvre ont pour horizontale leur niveau de données.  Ce document illustre les modifications dans les applications Dapper qui sont nécessaires pour intégrer des outils de base de données élastique. Notre objectif est sur la composition de la gestion des éclater élastique de base de données et les données dépendantes routage avec Dapper. 

**Exemples de Code**: [Outils de base de données élastique pour la base de données SQL Azure - intégration Dapper](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f).
 
Il est facile d’intégration **Dapper** et **DapperExtensions** de la bibliothèque de client élastique de base de données pour la base de données SQL Azure. Vos applications peuvent utiliser des données dépendantes routage en modifiant la création et l’ouverture de nouveaux objets [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) à utiliser l’appel [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) à partir de la [bibliothèque de client](http://msdn.microsoft.com/library/azure/dn765902.aspx). Cela limite des modifications dans votre application seulement lorsque les nouvelles connexions sont créées et ouvert. 

## <a name="dapper-overview"></a>Vue d’ensemble dapper
**Dapper** est un mapper relationnel objet. Il mappe les objets .NET à partir de votre application à une base de données relationnel (et inversement). La première partie de l’exemple de code montre comment vous pouvez intégrer la bibliothèque de client élastique de base de données avec les applications Dapper. La deuxième partie de l’exemple de code montre comment intégrer lorsque vous utilisez Dapper et DapperExtensions.  

La fonctionnalité mapper dans Dapper fournit les méthodes d’extension sur des connexions de base de données qui simplifient l’émetteur instructions T-SQL pour l’exécution ou l’interrogation de la base de données. Par exemple, Dapper facilite établir une correspondance entre vos objets .NET et les paramètres d’instructions SQL pour les appels **d’exécuter** ou d’utiliser les résultats de vos requêtes SQL en objets .NET à l’aide d’appels de **requête** à partir de Dapper. 

Lorsque vous utilisez DapperExtensions, vous n’avez plus besoin fournir les instructions SQL. Les méthodes d’extensions comme **GetList** ou **Insérer** sur la connexion de base de données créent les instructions SQL en arrière-plan.
 
Avantage de Dapper et également DapperExtensions est que l’application contrôle la création de la connexion de base de données. Cela permet d’interagir avec la bibliothèque de client de base de données élastique qui agents connexions basées sur le mappage des shardlets aux bases de données de base de données.

Pour obtenir les assemblys Dapper, voir [point Dapper net](http://www.nuget.org/packages/Dapper/). Pour les extensions Dapper, voir [DapperExtensions](http://www.nuget.org/packages/DapperExtensions).

## <a name="a-quick-look-at-the-elastic-database-client-library"></a>Aperçu de la bibliothèque de client de base de données élastique

Avec la bibliothèque de client élastique de base de données, vous définissez les partitions de vos données d’application appelées *shardlets* , établir une correspondance entre les bases de données et leur identifiez par *clés ont*. Vous pouvez avoir autant de bases de données que vous avez besoin et distribuez votre shardlets sur ces bases de données. Le mappage ont des valeurs de clé aux bases de données est stocké par un mappage éclater fourni par API de la bibliothèque. Cette fonctionnalité est appelée **Éclater mapper gestion**. La carte éclater sert également l’intermédiaire des connexions de base de données pour les requêtes comportant une clé ont. Cette fonctionnalité est appelée **routage dépendant des données**.

![Éclater cartes et routage dépendant des données][1]

Le Gestionnaire de carte éclater protège les utilisateurs des affichages incohérentes dans les données shardlet pouvant se produire lors des opérations de gestion shardlet simultanées sont produisent sur les bases de données. Pour ce faire, les cartes éclater broker les connexions de base de données pour une application créée avec la bibliothèque. Lorsque des opérations de gestion partagé peuvent avoir un impact sur la shardlet, cette option permet la fonctionnalité carte éclater automatiquement arrêter une connexion de base de données. 

Au lieu d’utiliser la méthode classique pour créer des connexions pour Dapper, nous devons utiliser la [méthode OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn824099.aspx). Ainsi, tous les la validation a lieu et connexions sont gérées correctement lorsque toutes les données se déplacent entre milieu des fragments.

### <a name="requirements-for-dapper-integration"></a>Configuration requise pour l’intégration Dapper

Lorsque vous travaillez avec la bibliothèque de client de base de données élastique et les API Dapper, nous souhaitons conserver les propriétés suivantes :

* **Évolution**: nous voulons ajouter ou supprimer des bases de données de la couche de données de l’application sharded comme bon vous semble pour les demandes de capacité de l’application. 

-    **Cohérence**: dans la mesure où notre application est mise à l’échelle à l’aide d’ont, nous avons besoin pour effectuer le routage dépendant des données. Nous à utiliser les fonctionnalités de routage dépendantes des données de la bibliothèque pour le faire. En particulier, nous souhaitons conserver la validation et la cohérence garanties fournies par les connexions sont demandées par le biais du Gestionnaire de carte éclater afin d’éviter les corruption ou les résultats de la requête incorrect. Ceci garantit que les connexions à un shardlet donné sont rejetées ou arrêtées si (par exemple) le shardlet est actuellement déplacé vers un autre partagé à l’aide des API de fusionner/fractionner.

-    **Mappage d’objet**: nous voulons conservent la convivialité des mappages fournis par Dapper à traduire entre les classes dans l’application et les structures de base de données sous-jacentes. 

La section suivante fournit des conseils pour ces exigences pour les applications basées sur **Dapper** et **DapperExtensions**.

## <a name="technical-guidance"></a>Assistance technique
### <a name="data-dependent-routing-with-dapper"></a>Données dépendantes routage avec Dapper 

Avec Dapper, l’application est généralement responsable de la création et l’ouverture des connexions à la base de données sous-jacente. Un type T donné par l’application, Dapper renvoie les résultats de la requête comme collections .NET de type T. Dapper effectue le mappage à partir des lignes de résultat T-SQL aux objets de type T. De même, Dapper mappe les objets .NET dans SQL valeurs ou des paramètres d’instructions data manipulation language (DML). Dapper offre cette fonctionnalité via les méthodes d’extension sur l’objet [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) régulière dans les bibliothèques ADO .NET SQL Client. La connexion SQL renvoyée par les API échelle élastique pour DDR sont également des objets [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) normales. Cela permet d’utiliser directement les extensions Dapper sur le type retourné par DDR API de la bibliothèque client, comme c’est également une connexion SQL Client simple.

Ces observations simplifier l’utilisent des connexions demandées par la bibliothèque de client de base de données élastique pour Dapper.

Cet exemple de code (à partir de l’échantillon accompagnement) illustre l’approche où la clé ont est fournie par l’application à la bibliothèque à négocier la connexion vers la droite partagé.   

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                     key: tenantId1, 
                     connectionString: connStrBldr.ConnectionString, 
                     options: ConnectionOptions.Validate))
    {
        var blog = new Blog { Name = name };
        sqlconn.Execute(@"
                      INSERT INTO
                            Blog (Name)
                            VALUES (@name)", new { name = blog.Name }
                        );
    }

L’appel à l’API [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) remplace la création par défaut et l’ouverture d’une connexion SQL Client. L’appel [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) accepte les arguments requis pour le routage dépendant des données : 

-    La carte éclater pour accéder aux données interfaces routage dépendantes
-    La touche ont pour identifier le shardlet
-    Les informations d’identification (nom d’utilisateur et mot de passe) pour vous connecter à la partagé

L’objet map éclater crée une connexion à partagé contenant le shardlet pour la clé ont donné. Le client de base de données élastique API également baliser la connexion pour implémenter son garantie de la cohérence. Étant donné que l’appel à [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) renvoie un objet de connexion SQL Client normal, le prochain appel à la méthode d’extension **exécuter** à partir de Dapper suit la pratique Dapper standard.

Les requêtes fonctionnent beaucoup de la même façon : vous ouvrez la première fois la connexion à l’aide de [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) à partir du client API. Puis vous utilisez les méthodes d’extension Dapper normal pour mapper les résultats de votre requête SQL en objets .NET :

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId1, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate ))
    {    
           // Display all Blogs for tenant 1
           IEnumerable<Blog> result = sqlconn.Query<Blog>(@"
                                SELECT * 
                                FROM Blog
                                ORDER BY Name");

           Console.WriteLine("All blogs for tenant id {0}:", tenantId1);
           foreach (var item in result)
           {
                Console.WriteLine(item.Name);
            }
    }

Notez que la **à l’aide** du bloc avec la connexion DDR portant sur toutes les opérations de base de données au sein du bloc pour l’une éclater où tenantId1 est conservé. La requête renvoie uniquement les blogs que qui se trouve sur le partagé actuel, mais pas ceux qui sont stockés sur n’importe quel autres milieu des fragments. 

## <a name="data-dependent-routing-with-dapper-and-dapperextensions"></a>Données dépendantes routage avec Dapper et DapperExtensions

Dapper est fourni avec succès de des extensions supplémentaires qui peuvent fournir des autres commodité et abstraction à partir de la base de données lorsque vous développez des applications de base de données. DapperExtensions est un exemple. 

À l’aide de DapperExtensions dans votre application ne modifie pas la façon dont les connexions de base de données sont créées et gérées. Il est toujours responsabilité de l’application pour ouvrir des connexions, et des objets de connexion SQL Client normales sont attendus par les méthodes d’extension. Nous pouvons compter sur [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) comme indiqué ci-dessus. Exemples de code suivants montrent la seule modification est que nous n’avez n’est plus écrire les instructions T-SQL :

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }

Et Voici l’exemple de code de la requête : 

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           // Display all Blogs for tenant 2
           IEnumerable<Blog> result = sqlconn.GetList<Blog>();
           Console.WriteLine("All blogs for tenant id {0}:", tenantId2);
           foreach (var item in result)
           {
               Console.WriteLine(item.Name);
           }
    }

### <a name="handling-transient-faults"></a>Gestion des défaillances temporaires

L’équipe Microsoft Patterns & Practices publié le [Bloc d’Application de gestion des pannes transitoires](http://msdn.microsoft.com/library/hh680934.aspx) pour aider les développeurs d’applications atténuer les conditions de panne transitoires courants rencontrées lors de l’exécution dans le cloud. Pour plus d’informations, voir [Perseverance, code Secret de tous les luttes : à l’aide du bloc d’Application de gestion des pannes transitoires](http://msdn.microsoft.com/library/dn440719.aspx).

L’exemple de code s’appuie sur la bibliothèque de pannes transitoires pour protéger des défaillances temporaires. 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn = 
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });

**SqlDatabaseUtils.SqlRetryPolicy** dans le code ci-dessus est définie comme un **SqlDatabaseTransientErrorDetectionStrategy** avec un nombre de tentatives de 10 et délai d’attente 5 secondes entre les essais. Si vous utilisez des transactions, assurez-vous que la portée de votre nouvelle tentative revenir au début de la transaction dans le cas d’une défaillance temporaire.

## <a name="limitations"></a>Limitations

Les approches décrites dans ce document entraînent quelques limitations :

* L’exemple de code pour ce document n’indique pas comment gérer le schéma sur milieu des fragments.
* Étant donné une demande, nous part du principe que tous les son traitement de base de données est contenue dans une seule partagé comme identifié par la clé ont fournie par la demande. Toutefois, cette hypothèse n'est pas toujours vérifiée, par exemple, lorsqu’elle n’est pas possible d’apporter une touche ont disponibles. Pour contourner ce problème, la bibliothèque de client de base de données élastique inclut la [classe MultiShardQuery](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardexception.aspx). La classe met en œuvre une abstraction de connexion pour l’interrogation sur plusieurs milieu des fragments. L’utilisation de MultiShardQuery en combinaison avec Dapper est au-delà de la portée de ce document.

## <a name="conclusion"></a>Conclusion

Applications à l’aide de Dapper et DapperExtensions peuvent bénéficier facilement dans les outils de base de données élastique pour la base de données SQL Azure. Les étapes décrites dans ce document, ces applications peuvent utiliser les capacités de l’outil pour dépendante routage en modifiant la création et l’ouverture de nouveaux objets [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) des données à utiliser l’appel [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) de la bibliothèque de client de base de données élastique. Cela limite les modifications d’application requises pour les emplacements où les nouvelles connexions sont créées et ouvert. 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-working-with-dapper/dapperimage1.png
 