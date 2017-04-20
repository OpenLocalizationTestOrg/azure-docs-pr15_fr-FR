<properties 
    pageTitle="Comment utiliser le Cache Azure Redis | Microsoft Azure" 
    description="Découvrez comment faire pour améliorer les performances de vos applications Azure avec Azure Redis Cache" 
    services="redis-cache,app-service" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="dotnet" 
    ms.topic="hero-article" 
    ms.date="08/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-use-azure-redis-cache"></a>Comment utiliser le Cache Redis Azure

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Ce guide vous montre comment commencer à utiliser **Azure Redis Cache**. Microsoft Azure Redis Cache est basé sur la source ouverte populaires Redis Cache. Il permet d’accéder à un cache Redis sûr et dédié, géré par Microsoft. Un cache créé à l’aide de Cache Redis Azure est accessible à partir de n’importe quelle application au sein de Microsoft Azure.

Microsoft Azure Redis Cache est disponible dans les niveaux suivants :

-   **Base** : nœud unique. Multiple taille jusqu'à 53 go.
-   **Standard** – deux nœuds principal/réplica. Multiple taille jusqu'à 53 go. 99,9 SLA %.
-   **Premium** – deux nœuds principal/réplica avec jusqu'à 10 milieu des fragments. Différentes tailles de 6 Go à 530 Go (nous contacter pour plus d’informations). Toutes les fonctionnalités de couche Standard et plus prise en charge, y compris pour [Redis cluster](cache-how-to-premium-clustering.md) [Redis persistance](cache-how-to-premium-persistence.md)et [Réseau virtuel Azure](cache-how-to-premium-vnet.md). 99,9 SLA %.

Chaque niveau est différent en termes de fonctionnalités et les prix. Pour plus d’informations sur les prix, voir [Détails tarifs du Cache][].

Ce guide vous montre comment utiliser le client [StackExchange.Redis][] à l’aide de C\# code. Les scénarios présentés incluent **Création et configuration d’un cache**, **Configuration des clients de cache**et **Ajout et suppression d’objets à partir du cache**. Pour plus d’informations sur l’utilisation de Cache Redis Azure, reportez-vous à la section [Étapes suivantes][] . Didacticiel de la création d’un ASP.NET MVC web app avec Cache Redis, Découvrez [comment créer une application Web avec Cache Redis](cache-web-app-howto.md).

<a name="getting-started-cache-service"></a>
## <a name="get-started-with-azure-redis-cache"></a>Prise en main du Cache Redis Azure

Il est facile de prise en main Azure Redis Cache. Pour commencer, vous mise en service et configurez un cache. Ensuite, vous configurez les clients de cache afin qu’ils peuvent accéder au cache. Une fois que les clients de cache sont configurés, vous pouvez commencer à travailler avec eux.

-   [Créer le cache][]
-   [Configurer les clients de cache][]

<a name="create-cache"></a>
## <a name="create-a-cache"></a>Créer un cache

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

### <a name="to-access-your-cache-after-its-created"></a>Pour accéder à votre cache après celui-ci est créé.

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Pour plus d’informations sur la configuration de votre cache, voir [comment configurer Azure Redis Cache](cache-configure.md).

<a name="NuGet"></a>
## <a name="configure-the-cache-clients"></a>Configurer les clients de cache

[AZURE.INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

Une fois que votre projet client est configuré pour la mise en cache, vous pouvez utiliser les techniques décrites dans les sections suivantes pour travailler avec le cache.

<a name="working-with-caches"></a>
## <a name="working-with-caches"></a>Utilisation de cache

Les étapes décrites dans cette section décrivent comment effectuer des tâches courantes à l’aide du Cache.

-   [Se connecter à la mémoire cache][]
-   [Ajouter et récupérer des objets à partir du cache][]
-   [Travailler avec les objets .NET dans le cache](#work-with-net-objects-in-the-cache)

<a name="connect-to-cache"></a>
## <a name="connect-to-the-cache"></a>Se connecter à la mémoire cache

Pour travailler par programmation avec un cache, vous avez besoin d’une référence dans le cache. Ajoutez le code suivant en haut d’un fichier à partir de laquelle vous souhaitez utiliser le client StackExchange.Redis pour accéder à un Cache Redis Azure.

    using StackExchange.Redis;

>[AZURE.NOTE] Le client StackExchange.Redis nécessite .NET Framework 4 ou version ultérieure.

La connexion au Cache Redis Azure est gérée par le `ConnectionMultiplexer` cours. Ce cours est conçu pour partager et réutiliser dans l’ensemble de votre application cliente et n’a pas besoin d’être créés sur une base par opération. 

Se connecter à un Cache Redis Azure et renvoyer une instance de connecté `ConnectionMultiplexer`, appelez statiques `Connect` méthode et passez le point de terminaison de cache et la clé comme l’exemple suivant. Utilisez la touche générée à partir du portail Azure en tant que le paramètre de mot de passe.

    ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

>[AZURE.IMPORTANT] Avertissement : Jamais banque d’informations d’identification dans le code source. Pour simplifier l’exemple, je suis les affichant dans le code source. Pour plus d’informations sur le stockage des informations d’identification, voir [comment chaînes de l’Application et le travail de chaînes de connexion][] .

Si vous ne voulez pas utiliser le protocole SSL, soit définir `ssl=false` ou omettre le `ssl` paramètre.

>[AZURE.NOTE] Le port non SSL est désactivé par défaut pour les nouveaux cache. Pour savoir comment activer le port non SSL, consultez les [Ports d’accès](cache-configure.md#access-ports)...

Une approche de partage un `ConnectionMultiplexer` instance dans votre application est d’avoir une propriété statique qui retourne une instance connectée, similaire à l’exemple suivant. Ainsi, avec des threads initialisation qu’un seul connecté `ConnectionMultiplexer` instance. Dans ces exemples `abortConnect` est définie sur false, ce qui signifie que l’appel échoue même si une connexion dans le Cache Redis Azure n’est pas établie. Une fonctionnalité clé de `ConnectionMultiplexer` est qu’il restaure automatiquement connectivité au cache une fois le problème de réseau ou d’autres causes sont résolus.

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

Pour plus d’informations sur les options de configuration de connexion avancés, voir [modèle de configuration de StackExchange.Redis][].

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

Une fois la connexion établie, renvoyer une référence à la base de données du cache redis en appelant le `ConnectionMultiplexer.GetDatabase` méthode. L’objet retourné par la `GetDatabase` méthode est un objet SQL direct léger et n’a pas besoin d’être stockés.

    // Connection refers to a property that returns a ConnectionMultiplexer
    // as shown in the previous example.
    IDatabase cache = Connection.GetDatabase();

    // Perform cache operations using the cache object...
    // Simple put of integral data types into the cache
    cache.StringSet("key1", "value");
    cache.StringSet("key2", 25);

    // Simple get of data types from the cache
    string key1 = cache.StringGet("key1");
    int key2 = (int)cache.StringGet("key2");

Maintenant que vous savez comment se connecter à une instance de Cache Redis Azure et renvoyer une référence à la base de données de cache, examinons une utilisation avec le cache.

<a name="add-object"></a>
## <a name="add-and-retrieve-objects-from-the-cache"></a>Ajouter et récupérer des objets à partir du cache

Éléments peuvent être stockés dans et extraites d’un cache à l’aide de la `StringSet` et `StringGet` méthodes.

    // If key1 exists, it is overwritten.
    cache.StringSet("key1", "value1");

    string value = cache.StringGet("key1");

Redis stocke la plupart des données sous forme de chaînes Redis, mais ces chaînes peuvent contenir plusieurs types de données, y compris les données binaires séries, ce qui peuvent être utilisées lorsque le stockage .NET des objets dans le cache.

Lors de l’appel `StringGet`, si l’objet existe, il est renvoyée, et si elle n’est pas le cas `null` est renvoyée. Dans ce cas, vous pouvez récupérer la valeur de la source de données et stocker dans le cache pour une utilisation ultérieure. Il s’agit du modèle de cache latérale.

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

Pour spécifier la date d’expiration d’un élément dans le cache, utilisez la `TimeSpan` paramètre de `StringSet`.

    cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

## <a name="work-with-net-objects-in-the-cache"></a>Travailler avec les objets .NET dans le cache

Cache Redis Azure peut mettre en cache les objets .NET ainsi que des types de données primitifs, mais avant un objet .NET peut être mis en cache il doit être sérialisé. Ceci est la responsabilité du développeur de l’application et vous donne la flexibilité du développeur dans le choix du sérialiseur.

Une méthode simple pour sérialiser des objets consiste à utiliser la `JsonConvert` méthodes de sérialisation dans [Newtonsoft.Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/8.0.1-beta1) et sérialiser vers et depuis JSON. L’exemple suivant montre un get et à l’aide de définir un `Employee` instance d’objet.


    class Employee
    {
        public int Id { get; set; }
        public string Name { get; set; }
    
        public Employee(int EmployeeId, string Name)
        {
            this.Id = EmployeeId;
            this.Name = Name;
        }
    }

    // Store to cache
    cache.StringSet("e25", JsonConvert.SerializeObject(new Employee(25, "Clayton Gragg")));

    // Retrieve from cache
    Employee e25 = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e25"));

<a name="next-steps"></a>
## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris les notions de base, suivez ces liens pour en savoir plus sur Azure Redis Cache.

-   Consultez les fournisseurs ASP.NET pour Azure Redis Cache.
    -   [État de la Session Redis Azure fournisseur](cache-aspnet-session-state-provider.md)
    -   [Azure Redis fournisseur de Cache de Cache de sortie ASP.NET](cache-aspnet-output-cache-provider.md)
-   [Activer les diagnostics de cache](cache-how-to-monitor.md#enable-cache-diagnostics) afin que vous pouvez [surveiller](cache-how-to-monitor.md) l’état du cache. Vous pouvez afficher les mesures dans le portail Azure et vous pouvez également [télécharger et passez en revue](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) les aide des outils de votre choix.
-   Consultez la [documentation du client StackExchange.Redis cache][].
    -   Cache Redis Azure sont accessibles à partir de nombreux clients Redis et langages de développement. Pour plus d’informations, voir [http://redis.io/clients][].
-   Azure Redis Cache peut également être utilisé avec les services tiers et outils tels que Redsmin et Redis Desktop Manager.
    -   Pour plus d’informations sur Redsmin, voir [comment récupérer une chaîne de connexion Azure Redis et l’utiliser avec Redsmin][].
    -   Accéder et vérifiez vos données dans Azure Redis Cache avec une graphique à l’aide de [RedisDesktopManager](https://github.com/uglide/RedisDesktopManager).
-   Consultez la documentation [redis][] et en savoir plus sur les [redis des types de données][] et [une présentation de 15 minutes Redis des types de données][].



<!-- INTRA-TOPIC LINKS -->
[Étapes suivantes]: #next-steps
[Introduction to Azure Redis Cache (Video)]: #video
[What is Azure Redis Cache?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Prepare Your Visual Studio Project to Use Azure Caching]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[Get Started with Azure Redis Cache]: #getting-started-cache-service
[Créer le cache]: #create-cache
[Configure the cache]: #enable-caching
[Configurer les clients de cache]: #NuGet
[Working with Caches]: #working-with-caches
[Se connecter à la mémoire cache]: #connect-to-cache
[Ajouter et récupérer des objets à partir du cache]: #add-object
[Specify the expiration of an object in the cache]: #specify-expiration
[Store ASP.NET session state in the cache]: #store-session

  
<!-- IMAGES -->


[StackExchangeNuget]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png

[NuGetMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png

[CacheProperties]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png

[ManageKeys]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png

[SessionStateNuGet]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png

[BrowseCaches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png

[Caches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png






   
<!-- LINKS -->
[http://redis.IO/clients]: http://redis.io/clients
[Develop in other languages for Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[Comment récupérer une chaîne de connexion Azure Redis et l’utiliser avec Redsmin]: https://redsmin.uservoice.com/knowledgebase/articles/485711-how-to-connect-redsmin-to-azure-redis-cache
[Azure Redis Session State Provider]: http://go.microsoft.com/fwlink/?LinkId=398249
[How to: Configure a Cache Client Programmatically]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[Session State Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric Cache: Caching Session State]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Output Cache Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[Team Blog]: http://blogs.msdn.com/b/windowsazure/
[Azure Caching]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[How to Configure Virtual Machine Sizes]: http://go.microsoft.com/fwlink/?LinkId=164387
[Azure Caching Capacity Planning Considerations]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure Caching]: http://go.microsoft.com/fwlink/?LinkId=252658
[How to: Set the Cacheability of an ASP.NET Page Declaratively]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[How to: Set a Page's Cacheability Programmatically]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Configure a cache in Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn793612.aspx

[Modèle de configuration de StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md

[Work with .NET objects in the cache]: http://msdn.microsoft.com/library/dn690521.aspx#Objects


[NuGet Package Manager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[Mettre en cache pour plus d’informations]: http://www.windowsazure.com/pricing/details/cache/
[Azure Portal]: https://portal.azure.com/

[Overview of Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=320830
[Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=398247

[Migrate to Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=317347
[Azure Redis Cache Samples]: http://go.microsoft.com/fwlink/?LinkId=320840
[Using Resource groups to manage your Azure resources]: ../azure-resource-manager/resource-group-overview.md

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[Documentation du client StackExchange.Redis cache]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[Redis des types de données]: http://redis.io/topics/data-types
[une introduction quinze minutes aux types de données Redis]: http://redis.io/topics/data-types-intro

[Fonctionnement des chaînes de l’Application et des chaînes de connexion]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/


