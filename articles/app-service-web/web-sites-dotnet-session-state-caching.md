<properties 
    pageTitle="État de la session avec Azure Redis cache dans le Service d’application Azure" 
    description="Découvrez comment utiliser le Service de Cache Azure pour prendre en charge la mise en cache de ASP.NET session état." 
    services="app-service\web" 
    documentationCenter=".net" 
    authors="Rick-Anderson" 
    manager="wpickett" 
    editor="none"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="get-started-article" 
    ms.date="06/27/2016" 
    ms.author="riande"/>


# <a name="session-state-with-azure-redis-cache-in-azure-app-service"></a>État de la session avec Azure Redis cache dans le Service d’application Azure


Cette rubrique explique comment utiliser le Service de Cache Redis Azure pour l’état de session.

Si votre application web ASP.NET utilise l’état de session, vous devrez configurer un fournisseur d’état de session externes (le Service de Cache Redis ou un fournisseur d’état de session SQL Server). Si vous utilisez l’état de session et que vous n’utilisez pas un fournisseur externe, vous sont limités à une instance de votre application web. Le Service de Cache Redis est la plus rapide et plus simple à activer.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

##<a id="createcache"></a>Créer le Cache
Suivez [les instructions suivantes](../cache-dotnet-how-to-use-azure-redis-cache.md#create-cache) pour créer le cache.

##<a id="configureproject"></a>Ajouter le package RedisSessionStateProvider NuGet à votre application web
Installer le NuGet `RedisSessionStateProvider` package.  Utilisez la commande suivante pour installer à partir de la console du Gestionnaire de package (**Outils** > **Gestionnaire de Package NuGet** > **Console du Gestionnaire de Package**) :

  `PM> Install-Package Microsoft.Web.RedisSessionStateProvider`
  
Installer à partir des **Outils** > **Gestionnaire de Package NuGet** > **Gérer les Packages pépite pour la Solution**, recherchez `RedisSessionStateProvider`.

Pour plus d’informations, voir la [page NuGet RedisSessionStateProvider](http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/ ) et [configurer le client de cache](../cache-dotnet-how-to-use-azure-redis-cache.md#NuGet).

##<a id="configurewebconfig"></a>Modifier le fichier Web.Config
En plus de la création de références d’assemblage pour le Cache, le package NuGet ajoute stub entrées dans le fichier *web.config* . 

1. Ouvrez le *web.config* et recherchez le **l’élément** .

1. Entrez les valeurs pour `host`, `accessKey`, `port` (le port SSL doit être 6380) et définissez `SSL` à `true`. Ces valeurs peuvent être obtenues à partir de la carte de [Portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715) pour votre instance de cache. Pour plus d’informations, voir [se connecter au cache](../cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-cache). Notez que le port non SSL est désactivé par défaut pour les nouveaux cache. Pour plus d’informations sur l’activation du port non SSL, consultez la section [Ports d’accès](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts) dans la rubrique [configurer un cache dans Azure Redis Cache](https://msdn.microsoft.com/library/azure/dn793612.aspx) . Le balisage suivant indique les modifications apportées au fichier *web.config* , en particulier les modifications apportées au *port*, *hôte*, accessKey*, et *ssl *.

          <system.web>;
            <customErrors mode="Off" />;
            <authentication mode="None" />;
            <compilation debug="true" targetFramework="4.5" />;
            <httpRuntime targetFramework="4.5" />;
            <sessionState mode="Custom" customProvider="RedisSessionProvider">;
              <providers>;  
                  <!--<add name="RedisSessionProvider" 
                    host = "127.0.0.1" [String]
                    port = "" [number]
                    accessKey = "" [String]
                    ssl = "false" [true|false]
                    throwOnError = "true" [true|false]
                    retryTimeoutInMilliseconds = "0" [number]
                    databaseId = "0" [number]
                    applicationName = "" [String]
                  />;-->;
                 <add name="RedisSessionProvider" 
                      type="Microsoft.Web.Redis.RedisSessionStateProvider" 
                      port="6380"
                      host="movie2.redis.cache.windows.net" 
                      accessKey="m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=" 
                      ssl="true" />;
              <!--<add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />;-->;
              </providers>;
            </sessionState>;
          </system.web>;


##<a id="usesessionobject"></a>Utilisez l’objet Session dans le Code
La dernière étape consiste à commencer à utiliser l’objet Session dans votre code ASP.NET. Ajouter des objets à l’état de session en utilisant la méthode **méthode Session.Add** . Cette méthode utilise paires clé-valeur pour stocker des éléments dans le cache d’état de session.

    string strValue = "yourvalue";
    Session.Add("yourkey", strValue);

Le code suivant extrait cette valeur de l’état de session.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)objValue; 

Vous pouvez également utiliser le Cache Redis aux objets de cache dans votre application web. Pour plus d’informations, voir [MVC film application avec Azure Redis Cache en seulement 15 minutes](https://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/).
Pour plus d’informations sur l’utilisation de l’état de session ASP.NET, voir [ASP.NET Session State Overview][].

>[AZURE.NOTE] Si vous voulez commencer à utiliser le Service d’application Azure avant de vous inscrire pour un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web starter courtes immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; Aucune engagements.

## <a name="whats-changed"></a>Ce qui a changé
* Pour un guide à la modification de sites Web Application Service voir : [Azure Application Service et son Impact sur les Services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)

  *Par [Anderson RTF](https://twitter.com/RickAndMSFT)*
  
  [installed the latest]: http://www.windowsazure.com/downloads/?sdk=net  
  [Vue d’ensemble de l’état de Session ASP.NET]: http://msdn.microsoft.com/library/ms178581.aspx

  [NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
  [NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
  [CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
  [NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
  [OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
  [CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
  [EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
  [ManageKeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png
 
