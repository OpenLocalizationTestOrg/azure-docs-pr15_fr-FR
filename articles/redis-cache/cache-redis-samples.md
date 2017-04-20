<properties 
    pageTitle="Exemples de Cache Redis Azure | Microsoft Azure" 
    description="Découvrez comment utiliser Azure Redis Cache" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="08/30/2016" 
    ms.author="sdanie"/>

# <a name="azure-redis-cache-samples"></a>Exemples de Cache Redis Azure 

Cette rubrique fournit une liste des exemples de Cache Redis Azure, couvrant scénarios tels que la connexion à un cache, la lecture et l’écriture des données vers et à partir d’un cache et utiliser les fournisseurs de Cache Redis ASP.NET. Certains exemples sont téléchargeables projets et certains fournissent des instructions et incluent des extraits de code, mais ne sont pas liés à un projet téléchargeable.

## <a name="hello-world-samples"></a>Exemples de world Hello

Les exemples de cette section décrivent les bases de connexion à une instance de Cache Redis Azure et en train de lire et écrire des données dans le cache à l’aide d’une variété de langues et Redis clients.

[Bonjour](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) montre comment effectuer diverses opérations de cache en utilisant le client .NET [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) .

Cet exemple montre comment :

-   Utilisez les diverses options de connexion
-   Lire et écrire des objets dans le cache de l’aide d’opérations synchrones et asynchrones
-   Utiliser les commandes MGET/MSET Redis pour renvoyer des valeurs de clés spécifiées.
-   Effectuer des opérations de transactions Redis
-   Utiliser des listes Redis et jeux trié
-   Stocker des objets .NET à l’aide de sérialiseurs JsonConvert
-   Utiliser des ensembles de Redis pour implémenter marquage
-   Travailler avec Cluster Redis

Pour plus d’informations, consultez la documentation [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) sur github et pour des scénarios d’utilisation plus voir les tests d’unité [StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/StackExchange.Redis.Tests) .

[Comment utiliser Azure Redis Cache avec Python](cache-python-get-started.md) montre comment prise en main Azure Redis Cache à l’aide de Python et le client [redis copier](https://github.com/andymccurdy/redis-py) .

[Travailler avec des objets dans le cache .NET](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) vous montre un moyen de sérialiser objets .NET afin de pouvoir les écrire et les lire à partir d’une instance de Cache Redis Azure. 

## <a name="use-redis-cache-as-a-scale-out-backplane-for-aspnet-signalr"></a>Utiliser Redis Cache comme une échelle à panier pour SignalR ASP.NET

[Utiliser le Cache de Redis comme une échelle à panier pour ASP.NET SignalR](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) illustre comment vous pouvez utiliser Azure Redis Cache comme SignalR panier. Pour plus d’informations sur fond de panier, voir [Évolution SignalR avec Redis](http://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="redis-cache-customer-query-sample"></a>Redis exemple de requête de Cache client

Cet exemple montre les performances compare entre l’accès aux données d’un cache et accès aux données à partir du stockage persistance. Cet exemple comporte deux projets.

-   [Démonstration comment Redis Cache peut améliorer les performances en mettant en cache de données](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
-   [Valeur de départ la base de données et le Cache pour la démo](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>État de Session ASP.NET et la mise en cache de sortie

[Utiliser le Azure Redis Cache pour stocker SessionState ASP.NET et OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) illustre comment vous pouvez utiliser Azure Redis Cache pour stocker la Session ASP.NET et Cache de sortie à l’aide de fournisseurs SessionState et OutputCache pour Redis.

## <a name="manage-azure-redis-cache-with-maml"></a>Gérer le Cache Redis Azure avec MAML

[Gérer le Azure Redis Cache à l’aide de bibliothèques de gestion Azure](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) illustre comment pouvez-vous utiliser Azure Gestion des bibliothèques pour gérer - (créer / mettre à jour / Supprimer) le Cache. 

## <a name="custom-monitoring-sample"></a>Personnalisé surveillance exemple

L’exemple de [données de contrôle d’accès Redis Cache](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) montre comment vous pouvez accéder à des données d’analyse pour votre Cache Azure Redis en dehors du portail Azure.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>Un cloner Twitter style écrite à l’aide de PHP et Redis

L’exemple [Retwis](https://github.com/SyntaxC4-MSFT/retwis) est la Redis Hello World. Il est un cloner de réseau social Twitter style minimal écrite à l’aide de Redis et PHP utilise le client [Predis](https://github.com/nrk/predis) . Le code source est conçu pour être très simple et en même temps pour afficher différentes Redis des structures de données.

## <a name="bandwidth-monitor"></a>Analyseur de bande passante

L’exemple [Analyseur de bande passante](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) permet de vous permettent de contrôler la bande passante utilisée sur le client. Afin de mesurer la bande passante, exécuter l’exemple de l’ordinateur client cache, passer des appels vers le cache et observez la bande passante signalée par l’échantillon moniteur de bande passante.
