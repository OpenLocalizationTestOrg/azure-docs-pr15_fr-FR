<properties
   pageTitle="Comment utiliser Azure Redis Cache avec Java | Microsoft Azure"
    description="Prise en main Cache Redis Azure à l’aide de Java"
    services="redis-cache"
    documentationCenter=""
    authors="steved0x"
    manager="douge"
    editor=""/>

<tags
    ms.service="cache"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="08/24/2016"
    ms.author="sdanie"/>

# <a name="how-to-use-azure-redis-cache-with-java"></a>Comment utiliser Azure Redis Cache avec Java

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Azure Cache Redis vous donne qu'accès à un dédié Redis cache, géré par Microsoft. Le cache est accessible à partir de n’importe quelle application au sein de Microsoft Azure.

Cette rubrique vous montre comment prise en main Azure Redis Cache à l’aide de Java.

## <a name="prerequisites"></a>Conditions préalables

[Jedis](https://github.com/xetorthio/jedis) - client Java pour Redis

Ce didacticiel utilise Jedis, mais vous pouvez utiliser n’importe quel client Java répertorié au [http://redis.io/clients](http://redis.io/clients).

## <a name="create-a-redis-cache-on-azure"></a>Créer un cache Redis sur Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Récupérer les clés d’accès et le nom d’hôte

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-the-non-ssl-endpoint"></a>Activer le point de terminaison non SSL

Certains clients Redis ne prend en charge SSL et par défaut le [port non SSL est désactivé pour les nouvelles instances de Cache Redis Azure](cache-configure.md#access-ports). Au moment de la rédaction de ce document, le client [Jedis](https://github.com/xetorthio/jedis) non prises en charge SSL. 

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]




## <a name="add-something-to-the-cache-and-retrieve-it"></a>Ajouter un élément dans le cache et de le retrouver

    package com.mycompany.app;
    import redis.clients.jedis.Jedis;
    import redis.clients.jedis.JedisShardInfo;

    /* Make sure you turn on non-SSL port in Azure Redis using the Configuration section in the Azure Portal */
    public class App
    {
      public static void main( String[] args )
      {
        /* In this line, replace <name> with your cache name: */
        JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6379);
        shardInfo.setPassword("<key>"); /* Use your access key. */
        Jedis jedis = new Jedis(shardInfo);
        jedis.set("foo", "bar");
        String value = jedis.get("foo");
      }
    }


## <a name="next-steps"></a>Étapes suivantes

- [Activer les diagnostics de cache](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) afin que vous pouvez [surveiller](https://msdn.microsoft.com/library/azure/dn763945.aspx) l’état du cache.
- Lisez le officiel [Redis documentation](http://redis.io/documentation).

