<properties
    pageTitle="Fournisseur de l’état de Session ASP.NET cache | Microsoft Azure"
    description="Apprenez à stocker l’état de Session ASP.NET à l’aide de Cache Redis Azure"
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
    ms.date="09/01/2016"
    ms.author="sdanie" />

# <a name="aspnet-session-state-provider-for-azure-redis-cache"></a>Fournisseur d’état de Session ASP.NET pour Azure Redis Cache

Cache Redis Azure fournit un fournisseur d’état de session que vous pouvez utiliser pour stocker votre état de session dans un cache plutôt que d’en mémoire ou dans une base de données SQL Server. Pour utiliser le fournisseur d’état de session mise en cache, tout d’abord configurer le cache, puis configurez votre application ASP.NET pour le cache de l’aide du package Redis Cache Session état NuGet.

Il n’est pas souvent pratique dans une application réelle cloud afin d’éviter le stockage d’une forme d’état pour une session utilisateur, mais les méthodes suivantes pour avoir un impact sur les performances et extensibilité élevées plus que d’autres personnes. Si vous avez stocker l’état, la meilleure solution consiste à conserver la quantité d’état petite et stocker dans des cookies. Si ce n’est pas possible, la meilleure solution suivante consiste à utiliser l’état de session ASP.NET auprès d’un fournisseur de cache distribué, en mémoire. La solution pire à partir d’un point de vue extensibilité élevées et de performances consiste à utiliser une base de données sauvegardée fournisseur d’état de session. Cette rubrique fournit des instructions sur l’utilisation du fournisseur d’état de Session ASP.NET pour Azure Redis Cache. Pour plus d’informations sur les autres options d’état de session, voir [options d’état de Session ASP.NET](#aspnet-session-state-options).

## <a name="store-aspnet-session-state-in-the-cache"></a>Stocker l’état de session ASP.NET dans le cache

Pour configurer une application cliente dans Visual Studio en utilisant le package Redis Cache Session état NuGet, droit sur le projet dans **L’Explorateur de solutions** , puis sélectionnez **Manage NuGet Packages**.

![Gérer les Packages NuGet Cache Redis Azure](./media/cache-aspnet-session-state-provider/redis-cache-manage-nuget-menu.png)

Tapez **RedisSessionStateProvider** dans la zone de texte Rechercher, sélectionnez à partir des résultats, puis cliquez sur **installer**.

>[AZURE.IMPORTANT] Si vous utilisez la fonctionnalité cluster de la couche premium, vous devez utiliser [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 ou version ultérieure ou une exception est levée. Il s’agit d’une modification avec rupture ; Pour plus d’informations, voir [v2.0.0 annulation de modifier les détails](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).

![État de la Session Azure Redis Cache fournisseur](./media/cache-aspnet-session-state-provider/redis-cache-session-state-provider.png)

Le package Redis Session état fournisseur NuGet a une dépendance sur le package StackExchange.Redis.StrongName. Si le package StackExchange.Redis.StrongName ne figure pas dans votre projet, qu'il est installé. Notez qu’en plus du package StackExchange.Redis.StrongName fort il existe également la version non-fort StackExchange.Redis. Si votre projet utilise la version de StackExchange.Redis non-fort que vous devez désinstaller, avant ou après avoir installé le package Redis Session état fournisseur NuGet, sinon vous sera obtenir des conflits de noms de votre projet. Pour plus d’informations sur ces packages, consultez [configurer .NET cache clients](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

Le package NuGet télécharge et ajoute l’assembly requis par les références et ajoute que les éléments suivants ajoute la section suivante dans votre fichier web.config qui contient la configuration requise pour votre application ASP.NET pour utiliser le fournisseur d’état Redis Cache Session.

    <sessionState mode="Custom" customProvider="MySessionStateStore">
        <providers>
        <!--
        <add name="MySessionStateStore"
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
        />
        -->
        <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false"/>
        </providers>
    </sessionState>

La section commentée fournit un exemple des attributs et des paramètres d’exemple pour chaque attribut.

Configurez les attributs avec les valeurs de votre carte de cache dans le portail de Microsoft Azure et configurez les autres valeurs comme vous le souhaitez. Pour obtenir des instructions sur l’accès à vos propriétés de cache, voir [configurer Redis les paramètres du cache](cache-configure.md#configure-redis-cache-settings).

-   **hôte** : spécifiez votre point de terminaison du cache.
-   **port** – utiliser votre port non SSL ou votre SSL port, selon les paramètres ssl.
-   **accessKey** – utiliser la clé primaire ou secondaire pour votre cache.
-   **ssl** – la valeur true si vous voulez sécuriser les communications cache/client avec ssl ; Renvoie la valeur false. Veillez à spécifier le port correct.
    -   Le port non SSL est désactivé par défaut pour les nouveaux cache. Indiquez la valeur true pour ce paramètre pour utiliser le port SSL. Pour plus d’informations sur l’activation du port non SSL, consultez la section [Ports d’accès](cache-configure.md#access-ports) dans la rubrique [configurer un cache](cache-configure.md) .
-   **throwOnError** – true si vous voulez une exception levée en cas d’échec, ou false si vous souhaitez que l’opération échoue silencieusement. Vous pouvez consulter une défaillance en vérifiant la propriété Microsoft.Web.Redis.RedisSessionStateProvider.LastException statique. La valeur par défaut est vrai.
-   **retryTimeoutInMilliseconds** – Échec d’opérations sont retentée pendant cet intervalle, exprimé en millisecondes. La première nouvelle tentative se produit après 20 millisecondes et puis tentatives se produisent chaque seconde jusqu'à ce que l’intervalle retryTimeoutInMilliseconds arrive à expiration. Immédiatement après cet intervalle, l’opération est retentée une dernière fois. Si l’opération ne fonctionne toujours pas, l’exception est levée revenir à l’appelant, en fonction du paramètre throwOnError. La valeur par défaut est égal à 0 ce qui signifie qu’aucune nouvelle tentative.
-   **databaseId** – spécifie la base de données à utiliser pour mettre en cache de sortie des données. Le cas contraire, la valeur par défaut de 0 est utilisée.
-   **applicationName** – clés sont stockés dans redis en tant que `{<Application Name>_<Session ID>}_Data`. Cela permet à plusieurs applications de partager la même clé. Ce paramètre est facultatif et si vous ne fournissez pas il une valeur par défaut est utilisée.
-   **connectionTimeoutInMilliseconds** – ce paramètre vous permet de remplacer le paramètre connectTimeout dans le client StackExchange.Redis. Si ne pas spécifié, le paramètre connectTimeout par défaut de 5 000 est utilisé. Pour plus d’informations, voir le [modèle de configuration de StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).
-   **operationTimeoutInMilliseconds** – ce paramètre vous permet de remplacer le paramètre syncTimeout dans le client StackExchange.Redis. Si ne pas spécifié, le paramètre par défaut syncTimeout 1000 est utilisé. Pour plus d’informations, voir le [modèle de configuration de StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).

Pour plus d’informations sur ces propriétés, consultez la blog d’origine publier une annonce en [Annonce ASP.NET Session State Provider pour Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

N’oubliez pas Commentez la section fournisseur des États de session standard InProc dans votre fichier web.config.

    <!-- <sessionState mode="InProc"
         customProvider="DefaultSessionProvider">
         <providers>
            <add name="DefaultSessionProvider"
                  type="System.Web.Providers.DefaultSessionStateProvider,
                        System.Web.Providers, Version=1.0.0.0, Culture=neutral,
                        PublicKeyToken=31bf3856ad364e35"
                  connectionStringName="DefaultConnection" />
          </providers>
    </sessionState> -->

Une fois ces étapes sont effectuées, votre application est configurée pour utiliser le fournisseur d’état Redis Cache de Session. Lorsque vous utilisez l’état de session dans votre application, il sera stocké dans une instance de Cache Redis Azure.

>[AZURE.NOTE] Notez que les données stockées dans le cache doivent être sérialisables, contrairement aux données pouvant être stockés dans la valeur par défaut en mémoire ASP.NET Session State du fournisseur. Lorsque le fournisseur d’état de Session pour Redis est utilisé, n’oubliez pas que les types de données qui sont stockés dans l’état de session sont sérialisables.

## <a name="aspnet-session-state-options"></a>Options d’état de Session ASP.NET

- Dans l’état de Session mémoire fournisseur - cette fournisseur stocke l’état de Session en mémoire. L’avantage d’utiliser ce fournisseur est simple et rapide. Toutefois, vous ne pouvez pas évoluer vos applications Web si vous utilisez dans le fournisseur de mémoire car il n’est pas distribué.

- État de Session SQL Server - cette fournisseur stocke l’état de Session dans Sql Server. Vous devez utiliser ce fournisseur si vous souhaitez conserver l’état de Session dans un espace de stockage permanente. Vous pouvez mettre à l’échelle votre application Web mais à l’aide de Sql Server pour Session aura un impact sur les performances dans votre application Web.

- Distribué dans mémoire Session état fournisseur comme Redis Cache Session State Provider - ce fournisseur vous donne le meilleur des deux environnements. Votre application Web peuvent avoir un fournisseur d’état de Session simple, rapide et format SVG. Toutefois, vous avez à prendre en considération que ce fournisseur stocke l’état de Session dans un Cache et que votre application doit prendre en considération toutes les caractéristiques associées lorsque vous parlez à un dans la mémoire Cache distribué tels que des échecs de réseau temporaires. Pour recommandations sur l’utilisation de Cache, consultez le [Guide de mise en cache](../best-practices-caching.md) à partir de Microsoft Patterns & pratiques de [conception de l’Application Azure Cloud et directives d’implémentation](https://github.com/mspnp/azure-guidance).

Pour plus d’informations sur l’état de session et d’autres recommandations, voir [Web Development Best Practices (construction réelles Cloud applications Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices).

## <a name="next-steps"></a>Étapes suivantes

Consultez le [Fournisseur de Cache de sortie ASP.NET pour Azure Redis Cache](cache-aspnet-output-cache-provider.md).
