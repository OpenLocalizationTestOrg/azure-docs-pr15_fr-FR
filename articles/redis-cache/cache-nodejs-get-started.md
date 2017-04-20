<properties
    pageTitle="Comment utiliser Azure Redis Cache avec Node.js | Microsoft Azure"
    description="Prise en main Cache Redis Azure à l’aide de Node.js et node_redis."
    services="redis-cache"
    documentationCenter=""
    authors="steved0x"
    manager="douge"
    editor="v-lincan"/>

<tags
    ms.service="cache"
    ms.devlang="nodejs"
    ms.topic="hero-article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="10/25/2016"
    ms.author="sdanie"/>

# <a name="how-to-use-azure-redis-cache-with-nodejs"></a>Comment utiliser Azure Redis Cache avec Node.js

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Cache Redis Azure permet d’accéder à un cache Redis sûr et dédié, géré par Microsoft. Le cache est accessible à partir de n’importe quelle application au sein de Microsoft Azure.

Cette rubrique vous montre comment prise en main Azure Redis Cache à l’aide de Node.js. Pour un autre exemple d’utilisation Azure Redis Cache avec Node.js, voir [créer une Application de conversation Node.js avec Socket.IO sur un site Web Azure](../app-service-web/web-sites-nodejs-chat-app-socketio.md).


## <a name="prerequisites"></a>Conditions préalables

Installer [node_redis](https://github.com/mranney/node_redis):

    npm install redis

Ce didacticiel utilise [node_redis](https://github.com/mranney/node_redis). Pour obtenir des exemples d’utilisation d’autres clients Node.js, consultez la documentation individuelle pour les clients Node.js répertoriés au [Node.js Redis clients](http://redis.io/clients#nodejs).

## <a name="create-a-redis-cache-on-azure"></a>Créer un cache Redis sur Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Récupérer les clés d’accès et le nom d’hôte

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="connect-to-the-cache-securely-using-ssl"></a>Se connecter au cache en toute sécurité en utilisant SSL

Les dernières versions de [node_redis](https://github.com/mranney/node_redis) prennent en charge pour la connexion pour mettre en Cache Redis Azure à l’aide de SSL. L’exemple suivant montre comment se connecter à Azure Redis Cache à l’aide de point de terminaison SSL du 6380. Remplacer `<name>` par le nom du cache et `<key>` avec des options votre clé primaire ou secondaire en tant que décrites dans la section précédente [récupérer les clés d’accès et le nom d’hôte](#retrieve-the-host-name-and-access-keys) .

     var redis = require("redis");
    
      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});


## <a name="add-something-to-the-cache-and-retrieve-it"></a>Ajouter un élément dans le cache et de le retrouver

L’exemple suivant vous montre comment vous connecter à une instance de Cache Redis Azure et de stocker et de récupérer un élément à partir du cache. Pour plus d’exemples d’utilisation de Redis avec le client [node_redis](https://github.com/mranney/node_redis) , voir [http://redis.js.org/](http://redis.js.org/).

     var redis = require("redis");
    
      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});
    
    client.set("key1", "value", function(err, reply) {
            console.log(reply);
        });
    
    client.get("key1",  function(err, reply) {
            console.log(reply);
        });

Résultat :

    OK
    value


## <a name="next-steps"></a>Étapes suivantes

- [Activer les diagnostics de cache](cache-how-to-monitor.md#enable-cache-diagnostics) afin que vous pouvez [surveiller](cache-how-to-monitor.md) l’état du cache.
- Lisez le officiel [Redis documentation](http://redis.io/documentation).



