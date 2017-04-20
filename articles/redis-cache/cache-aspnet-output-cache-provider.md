<properties
    pageTitle="Fournisseur de Cache de cache de sortie ASP.NET"
    description="Découvrez comment mettre en cache de sortie d’une Page ASP.NET à l’aide de Cache Redis Azure"
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
    ms.date="09/27/2016"
    ms.author="sdanie" />

# <a name="aspnet-output-cache-provider-for-azure-redis-cache"></a>Fournisseur de Cache de sortie ASP.NET pour Azure Redis Cache

Le fournisseur de Cache de sortie Redis est un mécanisme de stockage hors du processus de données du cache de sortie. Ces données sont spécialement pour les réponses HTTP complètes (page mise en cache de sortie). Le fournisseur se branche sur le nouveau cache fournisseur extensibilité point de sortie qui a été introduit dans ASP.NET 4.

Pour utiliser le fournisseur de Cache de sortie Redis, tout d’abord configurer le cache, puis configurez votre application ASP.NET en utilisant le package Redis sortie Cache fournisseur NuGet. Cette rubrique explique comment configurer votre application pour utiliser le fournisseur de Cache de sortie Redis. Pour plus d’informations sur la création et la configuration d’une instance de Cache Redis Azure, voir [créer un cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Stocker la sortie d’une page ASP.NET dans le cache

Pour configurer une application cliente dans Visual Studio en utilisant le package Redis sortie Cache fournisseur NuGet, droit sur le projet dans **L’Explorateur de solutions** , puis sélectionnez **Manage NuGet Packages**.

![Gérer les Packages NuGet Cache Redis Azure](./media/cache-aspnet-output-cache-provider/redis-cache-manage-nuget-menu.png)

Tapez **RedisOutputCacheProvider** dans la zone de texte Rechercher, sélectionnez à partir des résultats, puis cliquez sur **installer**.

![Azure Redis fournisseur de Cache de sortie du Cache](./media/cache-aspnet-output-cache-provider/redis-cache-page-output-provider.png)

Le package Redis sortie Cache fournisseur NuGet a une dépendance sur le package StackExchange.Redis.StrongName. Si le package StackExchange.Redis.StrongName ne figure pas dans votre projet, qu'il est installé. Notez qu’en plus du package StackExchange.Redis.StrongName fort il existe également la version non-fort StackExchange.Redis. Si votre projet utilise la version de StackExchange.Redis non-fort que vous devez désinstaller, avant ou après avoir installé le package Redis sortie Cache fournisseur NuGet, sinon vous sera obtenir des conflits de noms dans votre projet. Pour plus d’informations sur ces packages, consultez [configurer .NET cache clients](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

Le package NuGet télécharge et ajoute les références assembly requis et ajoute la section suivante dans votre fichier web.config qui contient la configuration requise pour votre application ASP.NET pour utiliser le fournisseur de Cache de sortie Redis.

    <caching>
      <outputCachedefault Provider="MyRedisOutputCache">
        <providers>
          <!--
          <add name="MyRedisOutputCache"
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
          />
          -->
          <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider" host="127.0.0.1" accessKey="" ssl="false"/>
        </providers>
      </outputCache>
    </caching>

La section commentée fournit un exemple des attributs et des paramètres d’exemple pour chaque attribut.

Configurez les attributs avec les valeurs de votre carte de cache dans le portail Microsoft Azure et configurez les autres valeurs comme vous le souhaitez. Pour obtenir des instructions sur l’accès à vos propriétés de cache, voir [configurer Redis les paramètres du cache](cache-configure.md#configure-redis-cache-settings).

-   **hôte** : spécifiez votre point de terminaison du cache.
-   **port** – utiliser votre port non SSL ou votre SSL port, selon les paramètres ssl.
-   **accessKey** – utiliser la clé primaire ou secondaire pour votre cache.
-   **ssl** – la valeur true si vous voulez sécuriser les communications cache/client avec ssl ; Renvoie la valeur false. Veillez à spécifier le port correct.
    -   Le port non SSL est désactivé par défaut pour les nouveaux cache. Indiquez la valeur true pour ce paramètre pour utiliser le port SSL. Pour plus d’informations sur l’activation du port non SSL, consultez la section [Ports d’accès](cache-configure.md#access-ports) dans la rubrique [configurer un cache](cache-configure.md) .
-   **databaseId** – spécifié la base de données à utiliser pour les données de sortie du cache. Le cas contraire, la valeur par défaut de 0 est utilisée.
-   **applicationName** – clés sont stockés dans redis en tant que <AppName>_<SessionId>_Data. Cela permet à plusieurs applications de partager la même clé. Ce paramètre est facultatif et si vous ne fournissez pas il une valeur par défaut est utilisée.
-   **connectionTimeoutInMilliseconds** – ce paramètre vous permet de remplacer le paramètre connectTimeout dans le client StackExchange.Redis. Si ne pas spécifié, le paramètre connectTimeout par défaut de 5 000 est utilisé. Pour plus d’informations, voir le [modèle de configuration de StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).
-   **operationTimeoutInMilliseconds** – ce paramètre vous permet de remplacer le paramètre syncTimeout dans le client StackExchange.Redis. Si ne pas spécifié, le paramètre par défaut syncTimeout 1000 est utilisé. Pour plus d’informations, voir le [modèle de configuration de StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).

Ajouter une OutputCache à chaque page dont vous souhaitez en cache de sortie.

    <%@ OutputCache Duration="60" VaryByParam="*" %>

Dans cet exemple montre comment les données de la page mise en cache restent dans le cache pendant 60 secondes et une autre version de la page est mis en cache pour chaque combinaison de paramètre. Pour plus d’informations sur l’OutputCache, voir [@OutputCache](http://go.microsoft.com/fwlink/?linkid=320837).

Une fois ces étapes sont effectuées, votre application est configurée pour utiliser le fournisseur de Cache de sortie Redis.

## <a name="next-steps"></a>Étapes suivantes

Consultez le [Fournisseur d’état de Session ASP.NET pour Azure Redis Cache](cache-aspnet-session-state-provider.md).
