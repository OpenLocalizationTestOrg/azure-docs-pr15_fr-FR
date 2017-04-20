<properties
    pageTitle="Comment utiliser Azure Redis Cache avec Python | Microsoft Azure"
    description="Prise en main Cache Redis Azure à l’aide de Python"
    services="redis-cache"
    documentationCenter=""
    authors="steved0x"
    manager="douge"
    editor="v-lincan"/>

<tags
    ms.service="cache"
    ms.devlang="python"
    ms.topic="hero-article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="08/16/2016"
    ms.author="sdanie"/>

# <a name="how-to-use-azure-redis-cache-with-python"></a>Comment utiliser Azure Redis Cache avec Python

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Cette rubrique vous montre comment prise en main Azure Redis Cache à l’aide de Python.


## <a name="prerequisites"></a>Conditions préalables

Installez [redis copier](https://github.com/andymccurdy/redis-py).


## <a name="create-a-redis-cache-on-azure"></a>Créer un cache Redis sur Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Récupérer les clés d’accès et le nom d’hôte

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-the-non-ssl-endpoint"></a>Activer le point de terminaison non SSL

Certains clients Redis ne prend en charge SSL et par défaut le [port non SSL est désactivé pour les nouvelles instances de Cache Redis Azure](cache-configure.md#access-ports). Au moment de la rédaction de ce document, le client [par exercice redis](https://github.com/andymccurdy/redis-py) non prises en charge SSL. 

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]


## <a name="add-something-to-the-cache-and-retrieve-it"></a>Ajouter un élément dans le cache et de le retrouver


    >>> import redis
    >>> r = redis.StrictRedis(host='<name>.redis.cache.windows.net',
          port=6380, db=0, password='<key>', ssl=True)
    >>> r.set('foo', 'bar')
    True
    >>> r.get('foo')
    b'bar'


Remplacer `<name>` avec votre nom de cache et `key` avec votre touche d’accès rapide.


<!--Image references-->
[1]: ./media/cache-python-get-started/redis-cache-new-cache-menu.png
[2]: ./media/cache-python-get-started/redis-cache-cache-create.png
