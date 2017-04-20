<properties 
    pageTitle="Cache de migrer vers Redis | Microsoft Azure"
    description="Découvrez comment migrer des applications de Service de Cache gérées pour mettre en Cache Redis Azure"
    services="redis-cache"
    documentationCenter="na"
    authors="steved0x"
    manager="douge"
    editor="tysonn" />
<tags 
    ms.service="cache"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="09/30/2016"
    ms.author="sdanie" />

# <a name="migrate-from-managed-cache-service-to-azure-redis-cache"></a>Migrer à partir de Service de Cache gérées pour mettre en Cache Redis Azure

Il est possible de migration de vos applications qui utilisent le Service de Cache géré Azure Azure Redis cache avec peu de modifications de votre application, selon les fonctionnalités du Service de Cache gérées utilisée par votre application mise en cache. Pendant l’API ne sont pas exactement le même qu’elles sont similaires et grande partie de votre code existant qui utilise le Service de Cache gérées pour accéder à un cache peut être réutilisée avec peu de modifications. Cette rubrique montre comment effectuer la configuration nécessaire et les modifications d’application à migrer vos applications de Service de Cache géré d’utiliser le Cache Redis Azure et montre comment certaines des fonctionnalités de Cache Redis Azure peuvent être utilisée pour implémenter les fonctionnalités d’un cache de Service de Cache géré.

## <a name="migration-steps"></a>Étapes de la migration

Les étapes suivantes sont nécessaires pour migrer une application de Service de Cache géré d’utiliser le Cache Redis Azure.

-   Établir une correspondance entre les fonctionnalités de Service de Cache géré Azure Redis Cache
-   Choisissez une offre de Cache
-   Créer un Cache
-   Configurer les Clients de Cache
    -   Supprimer la Configuration de Service de Cache gérées
    -   Configurer un client de cache en utilisant le NuGet Package StackExchange.Redis
-   Migrer le code de Service de Cache gérés
    -   Se connecter au cache à l’aide de la classe ConnectionMultiplexer
    -   Types de données primitifs Access dans le cache
    -   Travailler avec les objets .NET dans le cache
-   Migrer l’état de Session ASP.NET et la mise en cache de sortie pour mettre en Cache Redis Azure 

## <a name="map-managed-cache-service-features-to-azure-redis-cache"></a>Établir une correspondance entre les fonctionnalités de Service de Cache géré Azure Redis Cache

Azure Service de Cache gérées et Azure Redis Cache sont similaires, mais certaines des fonctionnalités de leurs mise en œuvre de différentes manières. Cette section décrit quelques-unes des différences et fournit des conseils sur l’implémentation des fonctionnalités de Service de Cache géré dans Azure Redis Cache.

|Fonctionnalité de Service de Cache gérée|Prise en charge du Service de Cache gérée|Prise en charge du Cache Redis Azure|
|---|---|---|
|Cache nommé|Un cache par défaut est configuré, et dans le cache Standard et Premium offres, jusqu'à neuf supplémentaires cache nommé peuvent être configurés si vous le souhaitez.|Azure cache Redis ont un nombre configurable des bases de données (par défaut de 16) qui peut servir à mettre en œuvre une fonctionnalité similaire à cache nommé. Pour plus d’informations, voir [configuration du serveur par défaut Redis](cache-configure.md#default-redis-server-configuration).|
|Disponibilité|Fournit la disponibilité des éléments dans le cache dans les offres de cache Standard et Premium. Si les éléments sont perdus en raison d’une défaillance, les copies de sauvegarde des éléments dans le cache sont toujours disponibles. Écritures dans le cache secondaire sont effectuées de façon synchrone.|Disponibilité est disponible dans les offres de cache Standard et Premium, qui ont une configuration primaire/réplica deux nœuds (chaque partagé dans un cache Premium a une paire principal/réplica). Écritures au réplica sont effectuées de façon asynchrone. Pour plus d’informations, voir [Azure Redis Cache tarifs](https://azure.microsoft.com/pricing/details/cache/).|
|Notifications|Permet aux clients de recevoir des notifications asynchrones lorsque diverses opérations de cache se produisent sur un cache nommé.|Applications clientes permet Redis publication/sub ou [notifications Keyspace](cache-configure.md#keyspace-notifications-advanced-settings) pour obtenir une fonctionnalité similaire à notifications.|
|Cache local|Enregistre une copie d’objets mis en cache localement sur le client pour accéder très rapidement.|Applications clientes nécessaires pour implémenter cette fonctionnalité à l’aide d’un dictionnaire ou la structure de données similaires.|
|Stratégie de suppression|Aucun ou LRU. La stratégie par défaut est LRU.|Azure Cache Redis prend en charge les stratégies de suppression suivantes : volatiles lru, allkeys lru, volatiles aléatoire, allkeys aléatoire volatiles-ttl, noeviction. La stratégie par défaut est volatiles lru. Pour plus d’informations, voir [configuration du serveur par défaut Redis](cache-configure.md#default-redis-server-configuration).|
|Stratégie d’expiration|La stratégie d’expiration par défaut est absolue et l’intervalle d’expiration par défaut est 10 minutes. Glissement et jamais stratégies sont également disponibles.|Éléments du cache n’expirent pas par défaut, mais une date d’expiration peut être configuré sur une base par écriture à l’aide de cache ensemble surcharges. Pour plus d’informations, voir [Ajouter et récupérer des objets à partir du cache](cache-dotnet-how-to-use-azure-redis-cache.md#add-and-retrieve-objects-from-the-cache).|
|Régions et marquage|Les régions sont sous-groupes pour les éléments mis en cache. Régions prennent également en charge les annotations des éléments du cache avec des chaînes descriptifs supplémentaires appelés balises. Régions prend en charge la fonctionnalité permettant d’effectuer des opérations de recherche sur les éléments balisés dans cette zone. Tous les éléments dans une région sont situés dans un seul nœud du cluster du cache.|Un cache Redis se compose d’un seul nœud (à moins que Redis cluster est activé) afin que le concept de zones de Service de Cache géré ne s’applique pas. Redis en charge la recherche et opérations génériques lors de la récupération des clés afin de balises descriptives peuvent être incorporés dans les noms de clé et utilisées pour récupérer les éléments plus tard. Pour obtenir un exemple d’implémentation d’une solution de liaison à l’aide de Redis, consultez [implémentation cache marquage avec Redis](http://stackify.com/implementing-cache-tagging-redis/).|
|Sérialisation|Gérées Cache prend en charge NetDataContractSerializer BinaryFormatter et l’utilisation de sérialiseurs personnalisés. La valeur par défaut est NetDataContractSerializer.|Il est la responsabilité de l’application cliente à sérialiser des objets .NET avant de les placer dans le cache, avec le choix du sérialiseur jusqu'à développeur de l’application client. Pour plus d’informations et des exemples de code, voir [utilisation des objets .NET dans le cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).|
| Mettre en cache émulateur | Cache gérée fournit un émulateur cache local. | Cache de Redis Azure ne dispose pas d’un émulateur, mais vous pouvez [exécuter la version MSOpenTech de redis-server.exe localement](cache-faq.md#cache-emulator) pour fournir une expérience émulateur. |

## <a name="choose-a-cache-offering"></a>Choisissez une offre de Cache

Microsoft Azure Redis Cache est disponible dans les niveaux suivants :

-   **Base** : nœud unique. Multiple taille jusqu'à 53 go.
-   **Standard** – deux nœuds principal/réplica. Multiple taille jusqu'à 53 go. 99,9 SLA %.
-   **Premium** – deux nœuds principal/réplica avec jusqu'à 10 milieu des fragments. Différentes tailles de 6 Go à 530 Go (nous contacter pour plus d’informations). Toutes les fonctionnalités de couche Standard et plus prise en charge, y compris pour [Redis cluster](cache-how-to-premium-clustering.md) [Redis persistance](cache-how-to-premium-persistence.md)et [Réseau virtuel Azure](cache-how-to-premium-vnet.md). 99,9 SLA %.

Chaque niveau est différent en termes de fonctionnalités et les prix. Les fonctionnalités sont décrites plus loin dans ce guide et pour plus d’informations sur les prix, voir les [Détails de tarification du Cache](https://azure.microsoft.com/pricing/details/cache/).

Point de départ pour la migration consiste à choisir la taille qui correspond à la taille de votre cache de Service de Cache géré précédente, puis mettre à l’échelle vers le haut ou vers le bas selon les besoins de votre application. Pour obtenir des instructions sur la sélection de l’offre de Cache Redis Azure droite, voir [quelle offre Redis Cache et la taille dois-je utiliser ?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## <a name="create-a-cache"></a>Créer un Cache

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>Configurer les Clients de Cache

Une fois que le cache est créé et configuré, l’étape suivante consiste à supprimer la configuration du Service de Cache gérés, ajoutez l’ajout de la configuration de Cache Redis Azure et référence afin que les clients de cache peuvent accéder à la mémoire cache.

-   Supprimer la Configuration de Service de Cache gérées
-   Configurer un client de cache en utilisant le NuGet Package StackExchange.Redis

### <a name="remove-the-managed-cache-service-configuration"></a>Supprimer la Configuration de Service de Cache gérées

Avant des applications clientes peuvent être configurées pour Azure Redis Cache, la configuration du Service de Cache géré existante et assemblage références doivent être supprimées en désinstallant le package NuGet de Service de Cache géré.

Pour désinstaller le package NuGet de Service de Cache gérés, droit sur le projet dans **L’Explorateur de** client et choisissez **Manage NuGet Packages**. Sélectionnez le nœud **packages installés** , puis tapez W**indowsAzure.Caching** dans la zone de packages installés rechercher. Sélectionnez **Windows** **Azure Cache** (ou **Windows** **Azure mise en cache** en fonction de la version du package NuGet), cliquez sur **désinstaller**, puis sur **Fermer**.

![Désinstaller Azure Cache géré Service NuGet Package](./media/cache-migrate-to-redis/IC757666.jpg)

Le package NuGet de Service de Cache géré en désinstallant les assemblys de Service de Cache gérées et les entrées de Service de Cache gérées dans le fichier app.config ou web.config de l’application cliente. Étant donné que certains paramètres personnalisés ne peuvent pas être supprimés quand vous désinstallez le package NuGet, ouvrez web.config ou app.config et vous assurer que les éléments suivants sont complètement supprimés.

Assurez-vous que la `dataCacheClients` entrée est supprimée de la `configSections` élément. Ne pas supprimer toute la `configSections` élément ; supprimer uniquement les `dataCacheClients` entrée, le cas échéant.

    <configSections>
      <!-- Existing sections omitted for clarity. -->
      <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
    </configSections>

Assurez-vous que la `dataCacheClients` section est supprimée. La `dataCacheClients` section sera semblable à l’exemple suivant.

    <dataCacheClients>
      <dataCacheClientname="default">
        <!--To use the in-role flavor of Azure Cache, set identifier to be the cache cluster role name -->
        <!--To use the Azure Managed Cache Service, set identifier to be the endpoint of the cache cluster -->
        <autoDiscoverisEnabled="true"identifier="[Cache role name or Service Endpoint]"/>

        <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
        <!--Use this section to specify security settings for connecting to your cache. This section is not required if your cache is hosted on a role that is a part of your cloud service. -->
        <!--<securityProperties mode="Message" sslEnabled="true">
          <messageSecurity authorizationInfo="[Authentication Key]" />
        </securityProperties>-->
      </dataCacheClient>
    </dataCacheClients>

Une fois la configuration du Service de Cache géré est supprimée, vous pouvez configurer le client de cache comme décrit dans la section suivante.

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>Configurer un client de cache en utilisant le NuGet Package StackExchange.Redis

[AZURE.INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Migrer le code de Service de Cache gérés

L’API pour le client de cache StackExchange.Redis est similaire au Service de Cache géré. Cette section fournit une vue d’ensemble des différences.

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>Se connecter au cache à l’aide de la classe ConnectionMultiplexer

Dans le Service de Cache géré, les connexions au cache ont été traitées par le `DataCacheFactory` et `DataCache` cours. Dans le Cache de Redis Azure, ces connexions sont gérées par le `ConnectionMultiplexer` cours.

Ajoutez les éléments suivants à l’aide de la déclaration en haut d’un fichier à partir de laquelle vous voulez accéder au cache.

    using StackExchange.Redis
                                
Si cet espace de noms n’est pas résolu, n’oubliez pas que vous avez ajouté le package StackExchange.Redis NuGet conformément à [configurer les clients de cache](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

>[AZURE.NOTE] Notez que le client StackExchange.Redis requiert .NET Framework 4 ou version ultérieure.

Pour vous connecter à une instance de Cache Redis Azure, appelez statiques `ConnectionMultiplexer.Connect` méthode et passez le point de terminaison et la clé. Une approche de partage un `ConnectionMultiplexer` instance dans votre application est d’avoir une propriété statique qui retourne une instance connectée, similaire à l’exemple suivant. Ainsi, avec des threads initialisation qu’un seul connecté `ConnectionMultiplexer` instance. Dans cet exemple `abortConnect` est définie sur false, ce qui signifie que l’appel échoue même si une connexion dans le cache n’est pas établie. Une fonctionnalité clé de `ConnectionMultiplexer` est qu’il restaure automatiquement connectivité au cache une fois le problème de réseau ou d’autres causes sont résolus.

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

Le point de terminaison de cache, les clés et les ports peuvent provenir de la carte de **Cache Redis** pour votre instance de cache. Pour plus d’informations, voir [Propriétés de Cache Redis](cache-configure.md#properties).

Une fois la connexion établie, renvoyer une référence à la base de données du cache Redis en appelant le `ConnectionMultiplexer.GetDatabase` méthode. L’objet retourné par la `GetDatabase` méthode est un objet SQL direct léger et n’a pas besoin d’être stockés.

    IDatabase cache = Connection.GetDatabase();
    
    // Perform cache operations using the cache object...
    // Simple put of integral data types into the cache
    cache.StringSet("key1", "value");
    cache.StringSet("key2", 25);
    
    // Simple get of data types from the cache
    string key1 = cache.StringGet("key1");
    int key2 = (int)cache.StringGet("key2");

Le client StackExchange.Redis utilise la `RedisKey` et `RedisValue` types d’accès et stocker des éléments dans le cache. Ces types mapper sur des types de langue plus primitifs, y compris chaîne et sont souvent pas utilisées directement. Redis chaînes sont le type de valeur Redis plus simple et peuvent contenir plusieurs types de données, y compris les flux de données binaires série, alors que vous ne pouvez pas utiliser le type directement, vous allez utiliser méthodes qui contiennent des `String` dans le nom. Pour les types de données plus base vous stockez et récupérer des éléments du cache à l’aide du `StringSet` et `StringGet` méthodes, sauf si vous stockez des collections de sites ou d’autres types de données Redis dans le cache. 

`StringSet`et `StringGet` sont très semblables au Service de Cache géré `Put` et `Get` méthodes, avec une principales différence étant qu’avant de définir et obtenir un objet .NET dans le cache vous devez sérialiser au préalable. 

Lors de l’appel `StringGet`, si l’objet existe, il est renvoyée, et si elle n’est pas le cas, null est renvoyé. Dans ce cas, vous pouvez récupérer la valeur de la source de données et stocker dans le cache pour une utilisation ultérieure. Il s’agit du modèle de cache latérale.

Pour spécifier la date d’expiration d’un élément dans le cache, utilisez la `TimeSpan` paramètre de `StringSet`.

    cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

Azure Cache Redis pouvez manipuler les objets .NET ainsi que des types de données primitifs, mais avant un objet .NET peut être mis en cache il doit être sérialisé. Il s’agit de la responsabilité du développeur de l’application. Le développeur flexibilité dans le choix du sérialiseur. Pour plus d’informations et des exemples de code, voir [utilisation des objets .NET dans le cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-redis-cache"></a>Migrer l’état de Session ASP.NET et la mise en cache de sortie pour mettre en Cache Redis Azure

Azure Cache Redis comporte des fournisseurs d’état de Session ASP.NET et la mise en cache de sortie de Page. Pour migrer votre application qui utilise les versions de Service de Cache géré de ces fournisseurs, supprimez d’abord les sections de votre fichier web.config, puis configurez les versions Azure Redis Cache des fournisseurs. Pour plus d’informations sur les fournisseurs Azure Redis Cache ASP.NET, voir [Fournisseur d’état de Session ASP.NET pour Azure Redis Cache](cache-aspnet-session-state-provider.md) et [Fournisseur de Cache de sortie ASP.NET pour Azure Redis Cache](cache-aspnet-output-cache-provider.md).

## <a name="next-steps"></a>Étapes suivantes

Explorer la [documentation de Cache Redis Azure](https://azure.microsoft.com/documentation/services/cache/) , exemples, vidéos et didacticiels sur, plus.

