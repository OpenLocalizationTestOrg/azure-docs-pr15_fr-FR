<properties
   pageTitle="Communication avec l’API Web ASP.NET du service | Microsoft Azure"
   description="Découvrez comment mettre en œuvre des communications du service étape par étape à l’aide de l’API Web ASP.NET OWIN hébergement d’un serveur de l’API de Services fiables."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="get-started-service-fabric-web-api-services-with-owin-self-hosting"></a>Prise en main : services de Service TISSU Web API OWIN hébergement d’un serveur

TISSU Service Azure positionne la puissance dans vos mains lorsque vous choisissez comment vous voulez mettre vos services pour communiquer avec les utilisateurs et les uns des autres. Ce didacticiel se concentre sur implémentation de la communication de service à l’aide d’API Web ASP.NET avec Interface Web ouverte pour .NET (OWIN) hébergement d’un serveur fiable Services API de Service tissu. Nous allons approfondir en communication enfichable Services fiables API. Nous utiliserons également des API Web dans un exemple étape par étape pour vous montrer comment configurer un récepteur de communication personnalisé.


## <a name="introduction-to-web-api-in-service-fabric"></a>Présentation des API Web dans tissu de Service

API Web ASP.NET est une infrastructure puissante et populaire pour la création de APIs HTTP en haut du .NET Framework. Si vous n’êtes pas déjà familiarisé avec la structure, voir [prise en main 2 de l’API Web ASP.NET](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) pour en savoir plus.

API Web dans tissu de Service est la même API Web ASP.NET vous connaissez et appréciez. La différence est dans la manière dont vous *hôte* une application Web API. Vous n’utiliserez pas Microsoft Internet Information Services (IIS). Pour mieux comprendre la différence, nous allons de la scinder en deux parties :

 1. L’application Web API (y compris des modèles et des contrôleurs)
 2. L’hôte (le serveur web, généralement IIS)

Une application Web API lui-même ne change pas. Il n’est pas différente à partir des applications Web API que vous avez écrit par le passé, et vous devriez pouvoir simplement déplacer sur la plupart du code de votre application. Mais si vous avez été hébergement sur IIS, où vous hébergez l’application peut être légèrement différente de ce que vous avez l’habitude. Avant de nous accéder à la partie d’hébergement, commençons par quelque chose plus familiers : l’application API Web.


## <a name="create-the-application"></a>Créer l’application

Commencez par créer une nouvelle application de Service tissu avec un seul service sans état dans Visual Studio 2015 :

![Créer une nouvelle application de Service TISSU](media/service-fabric-reliable-services-communication-webapi/webapi-newproject.png)

Un modèle de Visual Studio pour un service sans état à l’aide de l’API Web est disponible pour vous. Dans ce didacticiel, nous allons créer un projet d’API Web à partir de zéro comme résultat que vous pourriez obtenir si vous avez sélectionné ce modèle.

Sélectionnez un projet de Service sans état vide pour apprendre à créer un projet d’API Web à partir de zéro, ou vous pouvez commencer avec le modèle de l’API Web service sans état et il suffit de suivre le long.  

![Créer un service sans état unique](media/service-fabric-reliable-services-communication-webapi/webapi-newproject2.png)

La première étape consiste à extraire dans certains packages NuGet pour API Web. Le package nous à utiliser est Microsoft.AspNet.WebApi.OwinSelfHost. Cette offre comprend tous les packages API Web et les packages *hôte* . Il s’agit importantes ultérieurement.

![Créer des API Web en utilisant le Gestionnaire de Package NuGet](media/service-fabric-reliable-services-communication-webapi/webapi-nuget.png)

Après avoir installé les packages, vous pouvez commencer à créer la structure du projet API Web simple. Si vous avez utilisé l’API Web, la structure du projet doit ressembler très familier. Commencez par ajouter un `Controllers` répertoire et un contrôleur de valeurs simples :

**Fichier ValuesController.cs**

```csharp
using System.Collections.Generic;
using System.Web.Http;
    
namespace WebService.Controllers
{
    public class ValuesController : ApiController
    {
        // GET api/values 
        public IEnumerable<string> Get()
        {
            return new string[] { "value1", "value2" };
        }

        // GET api/values/5 
        public string Get(int id)
        {
            return "value";
        }

        // POST api/values 
        public void Post([FromBody]string value)
        {
        }

        // PUT api/values/5 
        public void Put(int id, [FromBody]string value)
        {
        }

        // DELETE api/values/5 
        public void Delete(int id)
        {
        }
    }
}

```

Ensuite, ajoutez une classe de démarrage à la racine du projet pour enregistrer le routage, formateurs et n’importe quel autre programme d’installation de configuration. Il s’agit également où API Web se branche à l' *hôte*, qui sera revue à nouveau ultérieurement. 

**Startup.cs**

```csharp
using System.Web.Http;
using Owin;

namespace WebService
{
    public static class Startup
    {
        public static void ConfigureApp(IAppBuilder appBuilder)
        {
            // Configure Web API for self-host. 
            HttpConfiguration config = new HttpConfiguration();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );

            appBuilder.UseWebApi(config);
        }
    }
}
```

C’est tout pour le composant d’application. À ce stade, nous avons configurer simplement la disposition du projet API Web simple. Jusque là, il ne doit pas présenter très différent à partir de projets d’API Web que vous avez écrit par le passé ou à partir du modèle API Web simple. Votre logique métier se trouvent dans les modèles et des contrôleurs comme d’habitude.

Maintenant que nous faire sur l’hébergement afin que nous puissions réellement exécutez-le ?

## <a name="service-hosting"></a>Service d’hébergement

Dans Service tissu, votre service s’exécute dans un *processus hôte de service*, un fichier exécutable qui exécute votre code de service. Lorsque vous rédigez un service à l’aide de l’API de Services fiable, votre projet de service compile simplement à un fichier exécutable qui inscrit votre type de service et exécute votre code. C’est vrai dans la plupart des cas lorsque vous écrivez un service sur tissu de Service dans .NET. Lorsque vous ouvrez Program.cs dans le projet de service sans état, vous devez voir :

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric.Services.Runtime;

internal static class Program
{
    private static void Main()
    {
        try
        {
            ServiceRuntime.RegisterServiceAsync("WebServiceType",
                context => new WebService(context)).GetAwaiter().GetResult();

            ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(WebService).Name);

            // Prevents this host process from terminating so services keeps running. 
            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
            throw;
        }
    }
}

```

Si qui ressemble étrangement au point d’entrée pour une application console, c’est parce qu’il s’agit.

Plus de détails sur le processus hôte de service et de l’inscription du service n’entrent pas dans le cadre de cet article. Mais il est important de connaître pour l’instant *que votre code de service est en cours d’exécution dans son propre processus*.

## <a name="self-host-web-api-with-an-owin-host"></a>Hébergez vos propres API Web avec un hôte OWIN

Étant donné que le code de votre application Web API est hébergé dans son propre processus, comment vous connecter à un serveur web ? Entrez [OWIN](http://owin.org/). OWIN est simplement un contrat entre les applications web .NET et les serveurs web. Traditionnellement lorsque ASP.NET (jusqu'à 5 MVC) est utilisée, l’application web est étroitement à IIS via System.Web. Toutefois, API Web met en œuvre OWIN, afin de pouvoir écrire une application web qui est dissociée à partir du serveur web qui héberge. Pour cette raison, vous pouvez utiliser un serveur *hébergé automatique* de web OWIN que vous pouvez démarrer dans votre propre processus. Cette option s’adapte parfaitement avec le modèle d’hébergement TISSU Service que nous décrites ci-dessus.

Dans cet article, nous allons utiliser interconnexions que l’hôte OWIN pour l’application Web API. Interconnexions sont un open source OWIN hôte mise en œuvre basé sur Windows [API de serveur HTTP](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)et [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener.aspx) .

> [AZURE.NOTE] Pour en savoir plus sur interconnexions, accédez au [site interconnexions](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana). Pour un bref aperçu de l’utilisation des interconnexions hébergez vos propres API Web, voir [Utiliser OWIN Self-Host ASP.NET Web API 2](http://www.asp.net/web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api).


## <a name="set-up-the-web-server"></a>Configurer le serveur web

L’API de Services fiable fournit un point d’entrée de communication dans laquelle vous pouvez plug-in piles de communication qui permettent aux utilisateurs et clients pour vous connecter au service :

```csharp

protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}

```

Le serveur web (et une autre pile de communication qu'utilisez-vous à l’avenir, par exemple WebSocket) doivent utiliser l’interface ICommunicationListener intégrer correctement avec le système. Les raisons devient voir plus facilement dans les étapes suivantes.

Tout d’abord, créez une classe appelée OwinCommunicationListener qui mettent en œuvre, ICommunicationListener :

**OwinCommunicationListener.cs**

```csharp
using Microsoft.Owin.Hosting;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Owin;
using System;
using System.Fabric;
using System.Globalization;
using System.Threading;
using System.Threading.Tasks;

namespace WebService
{
    internal class OwinCommunicationListener : ICommunicationListener
    {
        public void Abort()
        {
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
        }
    }
}
```

L’interface ICommunicationListener fournit trois méthodes pour gérer un récepteur de communication pour votre service :

 - *OpenAsync*. Démarrer l’écoute des demandes.
 - *CloseAsync*. Arrêter l’écoute des demandes de fin des requêtes en cours d’exécution et s’arrête.
 - *Abandonner*. Annuler tout ce qu’et arrêter immédiatement.

Pour commencer, ajouter des membres de classe privée pour les tâches que l’auditeur devra fonctionner. Il seront initialisés via le constructeur et utilisés ultérieurement lorsque vous configurez l’URL à l’écoute.

```csharp
internal class OwinCommunicationListener : ICommunicationListener
{
    private readonly ServiceEventSource eventSource;
    private readonly Action<IAppBuilder> startup;
    private readonly ServiceContext serviceContext;
    private readonly string endpointName;
    private readonly string appRoot;

    private IDisposable webApp;
    private string publishAddress;
    private string listeningAddress;

    public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName)
        : this(startup, serviceContext, eventSource, endpointName, null)
    {
    }

    public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName, string appRoot)
    {
        if (startup == null)
        {
            throw new ArgumentNullException(nameof(startup));
        }

        if (serviceContext == null)
        {
            throw new ArgumentNullException(nameof(serviceContext));
        }

        if (endpointName == null)
        {
            throw new ArgumentNullException(nameof(endpointName));
        }

        if (eventSource == null)
        {
            throw new ArgumentNullException(nameof(eventSource));
        }

        this.startup = startup;
        this.serviceContext = serviceContext;
        this.endpointName = endpointName;
        this.eventSource = eventSource;
        this.appRoot = appRoot;
    }
   

    ...

```

## <a name="implement-openasync"></a>Mettre en œuvre OpenAsync

Pour configurer le serveur web, vous avez besoin de deux informations :

 - *Préfixe de chemin d’accès d’une URL*. Bien que cette étape est facultative, il est préférable pour vous permettent de configurer cette maintenant afin que vous pouvez héberger en toute sécurité plusieurs services web dans votre application.
 - *Un port*.

Avant de vous obtenez un port pour le serveur web, il est important que vous savez que Service TISSU fournit une couche d’application qui se comporte comme un tampon entre votre application et le système d’exploitation sous-jacent il s’exécute sur. Par conséquent, tissu de Service fournit une façon de configurer des *points de terminaison* de vos services. Service TISSU garantit que les points de terminaison sont disponibles pour votre service à utiliser. Ainsi, vous n’êtes pas obligé de les configurer vous-même dans l’environnement du système d’exploitation sous-jacent. Vous pouvez facilement héberger votre application de Service tissu dans différents environnements sans avoir à apporter des modifications à votre application. (Par exemple, vous pouvez héberger la même application dans Azure ou dans votre propre centre de données).

Configurer un point de terminaison HTTP PackageRoot\ServiceManifest.xml :

```xml

<Resources>
    <Endpoints>
        <Endpoint Name="ServiceEndpoint" Type="Input" Protocol="http" Port="8281" />
    </Endpoints>
</Resources>

```

Cette étape est importante, car le processus hôte du service s’exécute sous informations d’identification à accès restreint (Service réseau sur Windows). Cela signifie que votre service n’ont accès configurer un point de terminaison HTTP dans sa propre. À l’aide de la configuration du point de terminaison, tissu de Service sait pour configurer la liste de contrôle d’accès (et) pour l’URL que le service écoute sur. Service TISSU fournit également un emplacement standard pour configurer les points de terminaison.


Dans OwinCommunicationListener.cs, vous pouvez commencer la mise en œuvre OpenAsync. Il s’agit de l’endroit où vous démarrez le serveur web. Tout d’abord, obtenir les informations de point de terminaison et créer l’URL que le service écoute sur. L’URL sera diffèrent selon que le récepteur est utilisé dans un service sans état ou un état. D’un service avec état, le récepteur doit créer une adresse unique pour chaque réplica service dynamique il écoute. Pour les services sans état, l’adresse peut être beaucoup plus simple. 

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    var serviceEndpoint = this.serviceContext.CodePackageActivationContext.GetEndpoint(this.endpointName);
    var protocol = serviceEndpoint.Protocol;
    int port = serviceEndpoint.Port;

    if (this.serviceContext is StatefulServiceContext)
    {
        StatefulServiceContext statefulServiceContext = this.serviceContext as StatefulServiceContext;

        this.listeningAddress = string.Format(
            CultureInfo.InvariantCulture,
            "{0}://+:{1}/{2}{3}/{4}/{5}",
            protocol,
            port,
            string.IsNullOrWhiteSpace(this.appRoot)
                ? string.Empty
                : this.appRoot.TrimEnd('/') + '/',
            statefulServiceContext.PartitionId,
            statefulServiceContext.ReplicaId,
            Guid.NewGuid());
    }
    else if (this.serviceContext is StatelessServiceContext)
    {
        this.listeningAddress = string.Format(
            CultureInfo.InvariantCulture,
            "{0}://+:{1}/{2}",
            protocol,
            port,
            string.IsNullOrWhiteSpace(this.appRoot)
                ? string.Empty
                : this.appRoot.TrimEnd('/') + '/');
    }
    else
    {
        throw new InvalidOperationException();
    }
    
    ...

```

Notez que « http://+ » est utilisé ici. Il s’agit pour vous assurer que le serveur web écoute sur toutes les adresses disponibles, y compris la période d’enquête machine hôte local et nom de domaine complet.

L’implémentation OpenAsync est une des raisons plus importantes pourquoi le serveur web (ou une pile de communications) est implémentée comme un ICommunicationListener, au lieu de simplement lui ouvre directement à partir de `RunAsync()` dans le service. La valeur renvoyée par OpenAsync est l’adresse qui est à l’écoute sur le serveur web. Lorsque cette adresse est renvoyée au système, il enregistre l’adresse auprès du service. Service TISSU fournit une API qui permet aux clients et autres services demander puis cette adresse par nom de service. Ceci est important, car l’adresse du service n’est pas statique. Services sont déplacés dans le cluster pour fins d’équilibrage de charge et disponibilité des ressources. Il s’agit le mécanisme qui permet aux clients de résoudre l’adresse d’écoute d’un service.

Dans cette optique, OpenAsync démarre le serveur web et retourne l’adresse qu’il écoute. Notez qu’il est à l’écoute sur « http://+ », mais avant OpenAsync renvoie l’adresse de la « + » est remplacé par l’adresse IP ou le nom de domaine complet du nœud il se trouve sur. L’adresse qui est retourné par cette méthode est ce qui est enregistré dans le système. Il est également que les clients et les autres services voient quand ils demandez à pour l’adresse d’un service. Pour les clients à correctement s’y connecter, dont elles ont besoin un IP ou le nom de domaine complet réel dans l’adresse.

```csharp
    ...

    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    try
    {
        this.eventSource.Message("Starting web server on " + this.listeningAddress);

        this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

        this.eventSource.Message("Listening on " + this.publishAddress);

        return Task.FromResult(this.publishAddress);
    }
    catch (Exception ex)
    {
        this.eventSource.Message("Web server failed to open endpoint {0}. {1}", this.endpointName, ex.ToString());

        this.StopWebServer();

        throw;
    }
}

```

Notez que cela fait référence à la classe de démarrage qui a été transmise à la OwinCommunicationListener dans le constructeur. Cette instance de démarrage est utilisée par le serveur web pour démarrer l’application Web API.

La `ServiceEventSource.Current.Message()` ligne s’affichent dans la fenêtre des événements de Diagnostic plus tard, lorsque vous exécutez l’application pour vérifier que le serveur web a démarré.

## <a name="implement-closeasync-and-abort"></a>Mettre en œuvre CloseAsync et abandon

Enfin, implémentez CloseAsync et abandon pour arrêter le serveur web. Le serveur web peut être arrêté en supprimant la poignée de serveur qui a été créée pendant OpenAsync.

```csharp
public Task CloseAsync(CancellationToken cancellationToken)
{
    this.eventSource.Message("Closing web server on endpoint {0}", this.endpointName);
            
    this.StopWebServer();

    return Task.FromResult(true);
}

public void Abort()
{
    this.eventSource.Message("Aborting web server on endpoint {0}", this.endpointName);
    
    this.StopWebServer();
}

private void StopWebServer()
{
    if (this.webApp != null)
    {
        try
        {
            this.webApp.Dispose();
        }
        catch (ObjectDisposedException)
        {
            // no-op
        }
    }
}
```

Dans cet exemple de mise en œuvre, CloseAsync et abandon simplement arrêtent le serveur web. Vous pouvez choisir pour effectuer un arrêt du serveur web plus en douceur coordonné de CloseAsync. Par exemple, l’arrêt peut attendre pour les demandes en vol se termine avant le retour.

## <a name="start-the-web-server"></a>Démarrer le serveur web

Vous êtes maintenant prêt à créer et retourner une instance de OwinCommunicationListener pour démarrer le serveur web. Dans la classe de Service (WebService.cs), remplacer la `CreateServiceInstanceListeners()` méthode :

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    var endpoints = Context.CodePackageActivationContext.GetEndpoints()
                           .Where(endpoint => endpoint.Protocol == EndpointProtocol.Http || endpoint.Protocol == EndpointProtocol.Https)
                           .Select(endpoint => endpoint.Name);

    return endpoints.Select(endpoint => new ServiceInstanceListener(
        serviceContext => new OwinCommunicationListener(Startup.ConfigureApp, serviceContext, ServiceEventSource.Current, endpoint), endpoint));
}
```

Il s’agit de l' API Web *application* et l' OWIN *hôte* enfin intersection. L’hôte (OwinCommunicationListener) reçoit une instance de l' *application* (API Web) via la classe de démarrage. Service TISSU gère ensuite son cycle de vie. Ce même modèle peut être suivi généralement avec une pile de communications.

## <a name="put-it-all-together"></a>Combiner toutes les options

Dans cet exemple, vous ne devez rien faire la `RunAsync()` méthode, afin que cette substitution simplement peut être supprimée.

L’implémentation du service finale doit être très simple. Il suffit de créer le récepteur de communication :

```csharp
using System;
using System.Collections.Generic;
using System.Fabric;
using System.Fabric.Description;
using System.Linq;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

namespace WebService
{
    internal sealed class WebService : StatelessService
    {
        public WebService(StatelessServiceContext context)
            : base(context)
        { }

        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
            var endpoints = Context.CodePackageActivationContext.GetEndpoints()
                                   .Where(endpoint => endpoint.Protocol == EndpointProtocol.Http || endpoint.Protocol == EndpointProtocol.Https)
                                   .Select(endpoint => endpoint.Name);

            return endpoints.Select(endpoint => new ServiceInstanceListener(
                serviceContext => new OwinCommunicationListener(Startup.ConfigureApp, serviceContext, ServiceEventSource.Current, endpoint), endpoint));
        }
    }
}
```

L’ensemble `OwinCommunicationListener` cours :

```csharp
using System;
using System.Diagnostics;
using System.Fabric;
using System.Globalization;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Owin.Hosting;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Owin;

namespace WebService
{
    internal class OwinCommunicationListener : ICommunicationListener
    {
        private readonly ServiceEventSource eventSource;
        private readonly Action<IAppBuilder> startup;
        private readonly ServiceContext serviceContext;
        private readonly string endpointName;
        private readonly string appRoot;

        private IDisposable webApp;
        private string publishAddress;
        private string listeningAddress;

        public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName)
            : this(startup, serviceContext, eventSource, endpointName, null)
        {
        }

        public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName, string appRoot)
        {
            if (startup == null)
            {
                throw new ArgumentNullException(nameof(startup));
            }

            if (serviceContext == null)
            {
                throw new ArgumentNullException(nameof(serviceContext));
            }

            if (endpointName == null)
            {
                throw new ArgumentNullException(nameof(endpointName));
            }

            if (eventSource == null)
            {
                throw new ArgumentNullException(nameof(eventSource));
            }

            this.startup = startup;
            this.serviceContext = serviceContext;
            this.endpointName = endpointName;
            this.eventSource = eventSource;
            this.appRoot = appRoot;
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
            var serviceEndpoint = this.serviceContext.CodePackageActivationContext.GetEndpoint(this.endpointName);
            var protocol = serviceEndpoint.Protocol;
            int port = serviceEndpoint.Port;

            if (this.serviceContext is StatefulServiceContext)
            {
                StatefulServiceContext statefulServiceContext = this.serviceContext as StatefulServiceContext;

                this.listeningAddress = string.Format(
                    CultureInfo.InvariantCulture,
                    "{0}://+:{1}/{2}{3}/{4}/{5}",
                    protocol,
                    port,
                    string.IsNullOrWhiteSpace(this.appRoot)
                        ? string.Empty
                        : this.appRoot.TrimEnd('/') + '/',
                    statefulServiceContext.PartitionId,
                    statefulServiceContext.ReplicaId,
                    Guid.NewGuid());
            }
            else if (this.serviceContext is StatelessServiceContext)
            {
                this.listeningAddress = string.Format(
                    CultureInfo.InvariantCulture,
                    "{0}://+:{1}/{2}",
                    protocol,
                    port,
                    string.IsNullOrWhiteSpace(this.appRoot)
                        ? string.Empty
                        : this.appRoot.TrimEnd('/') + '/');
            }
            else
            {
                throw new InvalidOperationException();
            }

            this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

            try
            {
                this.eventSource.Message("Starting web server on " + this.listeningAddress);

                this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

                this.eventSource.Message("Listening on " + this.publishAddress);

                return Task.FromResult(this.publishAddress);
            }
            catch (Exception ex)
            {
                this.eventSource.Message("Web server failed to open endpoint {0}. {1}", this.endpointName, ex.ToString());

                this.StopWebServer();

                throw;
            }
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
            this.eventSource.Message("Closing web server on endpoint {0}", this.endpointName);

            this.StopWebServer();

            return Task.FromResult(true);
        }

        public void Abort()
        {
            this.eventSource.Message("Aborting web server on endpoint {0}", this.endpointName);

            this.StopWebServer();
        }

        private void StopWebServer()
        {
            if (this.webApp != null)
            {
                try
                {
                    this.webApp.Dispose();
                }
                catch (ObjectDisposedException)
                {
                    // no-op
                }
            }
        }
    }
}
```

À présent que vous avez placé tous les éléments en place, votre projet doit ressembler à une application Web API standard avec points d’entrée fiable API de Services et un hôte OWIN :


![API Web avec des points d’entrée fiable API de Services et l’hôte OWIN](media/service-fabric-reliable-services-communication-webapi/webapi-projectstructure.png)

## <a name="run-and-connect-through-a-web-browser"></a>Exécutez et connectez-vous via un navigateur web

Si vous n’avez pas fait, [configurer votre environnement de développement](service-fabric-get-started.md).


Vous pouvez désormais créer et déployer votre service. Dans Visual Studio pour créer et déployer l’application, appuyez sur **F5** . Dans la fenêtre des événements de Diagnostic, vous devez voir un message indiquant que le serveur web ouvert sur http://localhost:8281 /.


![Fenêtre de Visual Studio des événements de Diagnostic](media/service-fabric-reliable-services-communication-webapi/webapi-diagnostics.png)

> [AZURE.NOTE] Si le port a déjà été ouvert par un autre processus sur votre ordinateur, vous pouvez observer une erreur ici. Cela indique que le récepteur n’a pas pu être ouvert. Si c’est le cas, essayez d’utiliser un autre port pour la configuration de point de terminaison dans ServiceManifest.xml.


Une fois que le service est en cours d’exécution, ouvrez un navigateur et accédez à [l’api/http://localhost:8281/valeurs](http://localhost:8281/api/values) pour le tester.

## <a name="scale-it-out"></a>Mettre à l’échelle arrière

Mise à l’échelle des applications web sans état généralement moyennes d’ajout de plusieurs ordinateurs et tournant configurant les applications web sur les. Moteur d’orchestration du service TISSU peut le faire pour vous chaque fois que les nouveaux nœuds sont ajoutés à un cluster. Lorsque vous créez des instances d’un service sans état, vous pouvez spécifier le nombre d’instances que vous voulez créer. Service TISSU place ce nombre d’instances sur nœuds dans le cluster. Et il vérifie que ne pas en créer plusieurs instances sur n’importe quel un nœud. Vous pouvez également demander tissu de Service pour créer une instance sur tous les nœuds en spécifiant **-1** pour le nombre d’instances. Cela garantit que lorsque vous ajoutez des nœuds à évoluer votre cluster, une instance de votre service sans état sont créée sur les nouveaux nœuds. Cette valeur est une propriété de l’instance du service, ainsi qu’il est définie lorsque vous créez une instance de service. Vous pouvez le faire via PowerShell :

```powershell

New-ServiceFabricService -ApplicationName "fabric:/WebServiceApplication" -ServiceName "fabric:/WebServiceApplication/WebService" -ServiceTypeName "WebServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1

```

Vous pouvez également le faire lorsque vous définissez un service par défaut dans un projet de service sans état Visual Studio :

```xml

<DefaultServices>
  <Service Name="WebService">
    <StatelessService ServiceTypeName="WebServiceType" InstanceCount="-1">
      <SingletonPartition />
    </StatelessService>
  </Service>
</DefaultServices>

```

Pour plus d’informations sur la façon de créer des applications et des instances de service, voir [déployer une application](service-fabric-deploy-remove-applications.md).

## <a name="next-steps"></a>Étapes suivantes

[Déboguer votre application de Service tissu à l’aide de Visual Studio](service-fabric-debugging-your-application.md)
