<properties
   pageTitle="Vue d’ensemble des communications fiable Services | Microsoft Azure"
   description="Vue d’ensemble du modèle de communication des Services fiables, y compris les récepteurs d’ouverture sur les services et résolution des points de terminaison de communication entre les services."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="BharatNarasimman"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="how-to-use-the-reliable-services-communication-apis"></a>Comment utiliser l’interfaces API de communication des Services fiables

Azure tissu de Service en tant que plate-forme est complètement indépendante sur les communications entre les services. Tous les protocoles et piles sont acceptables, UDP à HTTP. Il est le développeur du service pour choisir le mode de communication de services. L’infrastructure d’application Services fiables fournit piles de communication intégrés, ainsi que des API que vous pouvez utiliser pour créer vos composants de communication personnalisé. 

## <a name="set-up-service-communication"></a>Configurer les communications de service

L’API de Services fiable utilise une interface simple pour les communications du service. Pour ouvrir un point de terminaison de votre service, il vous suffit implémenter cette interface :

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

Vous pouvez ensuite ajouter votre implémentation de récepteur de communication en renvoyant dans une méthode de classe basée sur un service de remplacement.

Pour les services sans état :

```csharp
class MyStatelessService : StatelessService
{
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        ...
    }
    ...
}
```

Pour les services avec état :

```csharp
class MyStatefulService : StatefulService
{
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        ...
    }
    ...
}
```

Dans les deux cas, vous retournez une collection de récepteurs. Cela permet à votre service écouter sur plusieurs points de terminaison, en utilisant éventuellement différents protocoles, à l’aide de plusieurs récepteurs. Par exemple, vous devrez un récepteur HTTP et une écoute WebSocket distincte. Chaque récepteur Obtient un nom et la collection résultante de *nom : adresse* paires est représenté par un objet JSON lorsqu’un client demande les adresses à l’écoute pour une instance de service ou d’une partition.

Dans un service sans état, le remplacement retourne une collection de ServiceInstanceListeners. Un ServiceInstanceListener contient une fonction pour créer un ICommunicationListener et lui attribuer un nom. Pour les services avec état, le remplacement retourne une collection de ServiceReplicaListeners. Ceci est légèrement différente de son homologue sans état, car un ServiceReplicaListener a une option pour ouvrir un ICommunicationListener sur les doubles secondaires. Non seulement utiliser plusieurs récepteurs de communication dans un service, mais vous pouvez également spécifier les récepteurs acceptent des demandes sur les doubles secondaires et quels sont ceux qui écoute uniquement sur les doubles principales.

Par exemple, vous pouvez avoir une ServiceRemotingListener qui conduit les appels RPC uniquement sur les doubles principales et les demandes d’un récepteur deuxième, personnalisé qui vous permet d’accéder en savoir sur les doubles secondaires sur HTTP :

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[]
    {
        new ServiceReplicaListener(context =>
            new MyCustomHttpListener(context),
            "HTTPReadonlyEndpoint",
            true),

        new ServiceReplicaListener(context =>
            this.CreateServiceRemotingListener(context),
            "rpcPrimaryEndpoint",
            false)
    };
}
```

> [AZURE.NOTE] Lorsque vous créez plusieurs récepteurs d’un service, chaque récepteur **doit** être donné un nom unique.

Enfin, décrivez les points de terminaison requis pour le service dans le [service manifeste](service-fabric-application-model.md) sous la section points de terminaison.

```xml
<Resources>
    <Endpoints>
      <Endpoint Name="WebServiceEndpoint" Protocol="http" Port="80" />
      <Endpoint Name="OtherServiceEndpoint" Protocol="tcp" Port="8505" />
    <Endpoints>
</Resources>

```

Le récepteur de communication peut accéder aux ressources de point de terminaison affectés à partir de la `CodePackageActivationContext` dans les `ServiceContext`. Que l’auditeur peut alors commencer à écouter les requêtes lorsqu’il est ouvert.

```csharp
var codePackageActivationContext = serviceContext.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```

> [AZURE.NOTE] Ressources de point de terminaison sont communes au package d’ensemble du service, et ils sont attribuées par tissu de Service lorsque le package de service est activé. Plusieurs réplicas service hébergés dans le même ServiceHost peuvent partager le même port. Cela signifie que le récepteur de communication doit prendre en charge le partage de port. Il est recommandé d’effectuer cette opération pour le récepteur de communication à utiliser la partition ID et réplica/instance lorsqu’il génère l’adresse écouter.

### <a name="service-address-registration"></a>Inscription adresse du service

Un service système appelé le *Service de dénomination* s’exécute sur clusters tissu de Service. Le Service de dénomination est un bureau d’enregistrement pour les services et leurs adresses écoute chaque instance ou réplica du service. Lorsque la `OpenAsync` méthode d’un `ICommunicationListener` est terminée, son retour valeur est enregistrée dans le Service de dénomination. Cette valeur de retour est publiée dans le Service de dénomination est une chaîne dont la valeur peut être quelconque du tout. Cette valeur de chaîne est celui que verront les clients lorsqu’ils demanderont une adresse pour le service du Service de dénomination.

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = string.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/",
                port);
                        
    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
            
    this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));
    
    // the string returned here will be published in the Naming Service.
    return Task.FromResult(this.publishAddress);
}
```

Service TISSU fournit des API qui permet aux clients et autres services demander puis cette adresse par nom de service. Ceci est important, car l’adresse du service n’est pas statique. Services sont déplacés dans le cluster pour fins d’équilibrage de charge et disponibilité des ressources. Il s’agit le mécanisme qui permet aux clients de résoudre l’adresse d’écoute d’un service.

> [AZURE.NOTE] Pour une vue d’ensemble complet de rédaction un `ICommunicationListener`, consultez [services Web API de Service TISSU OWIN hébergement d’un serveur](service-fabric-reliable-services-communication-webapi.md)

## <a name="communicating-with-a-service"></a>Communication avec un service
L’API de Services fiable fournit les bibliothèques suivantes pour écrire des clients communiquent avec les services.

### <a name="service-endpoint-resolution"></a>Résolution de point de terminaison de service
La première étape pour la communication avec un service est pour résoudre une adresse de point de terminaison de la partition ou l’instance du service que vous souhaitez discuter. La `ServicePartitionResolver` classe utility se trouve une primitive base permettant de détecter les clients le point de terminaison d’un service en cours d’exécution. Dans la terminologie tissu de Service, le processus de déterminer le point de terminaison d’un service est appelé la *résolution de point de terminaison de service*.

Se connecter aux services au sein d’un cluster, `ServicePartitionResolver` pouvant être créées à l’aide des paramètres par défaut. Il s’agit généralement recommandé pour la plupart des cas :

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();
```

Se connecter aux services dans un cluster différent, un `ServicePartitionResolver` peuvent être créées avec un jeu de points de terminaison de passerelle cluster. Notez que les points de terminaison de passerelle sont simplement différents points de terminaison de connexion à la même cluster. Par exemple :

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```

Vous pouvez également `ServicePartitionResolver` peut être donné une fonction pour la création d’un `FabricClient` à utiliser en interne : 
 
```csharp
public delegate FabricClient CreateFabricClientDelegate();
```

`FabricClient`est l’objet qui est utilisé pour communiquer avec le cluster tissu de Service pour diverses opérations de gestion sur le cluster. Ceci est utile lorsque vous souhaitez contrôler la façon `ServicePartitionResolver` interagit avec votre cluster. `FabricClient`exécute la mise en cache en interne et est généralement complexes à créer, il est important de réutiliser `FabricClient` instances autant que possibles. 

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver(() => CreateMyFabricClient());
```

Une méthode de résolution, est utilisée pour récupérer l’adresse d’un service ou d’une partition de service pour les services partitionnées.

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();

ResolvedServicePartition partition =
    await resolver.ResolveAsync(new Uri("fabric:/MyApp/MyService"), new ServicePartitionKey(), cancellationToken);
```

Une adresse de service peut être résolue aisément en utilisant un `ServicePartitionResolver`, mais plus de travail est nécessaire pour garantir l’adresse résolue peut être utilisé correctement. Votre client devra détecter si la tentative de connexion a échoué en raison d’une erreur temporaire et peut être retentée (par exemple, service déplacé ou est temporairement indisponible), ou une erreur permanente (par exemple, le service a été supprimé ou la ressource demandée n’existe plus). Instances de service ou réplicas peuvent se déplacer dans du nœud nœud à tout moment pour plusieurs raisons. L’adresse du service résolu via `ServicePartitionResolver` peut être obsolète au moment où votre code client tente de se connecter. Dans ce cas à nouveau le client devrez résoudre l’adresse. Fournir la précédente `ResolvedServicePartition` indique que le solveur doit réessayez au lieu de simplement les récupérer une adresse mis en cache.

En règle générale, le code client ne besoin pas fonctionner avec la `ServicePartitionResolver` directement. Il est créé et transmis à références de client de communication de l’API de Services fiables. Les références utilisent l’utilitaire de résolution en interne pour générer un objet client pouvant être utilisées pour communiquer avec les services.

### <a name="communication-clients-and-factories"></a>Références et clients de communication

La bibliothèque d’usine communication met en œuvre un modèle de réessayer de gestion des erreurs typique qui facilite la nouvelle tentative connexions aux points de terminaison de service résolu. La bibliothèque d’usine fournit le mécanisme réessayer pendant que vous fournissez les gestionnaires d’erreurs.

`ICommunicationClientFactory`définit l’interface de base implémentée par un client factory communication produisant clients qui peuvent communiquer avec un service de tissu de Service. L’implémentation de la CommunicationClientFactory dépend de la pile de communication utilisée par le service tissu de Service dans lequel le client souhaite communiquer. L’API de Services fiable fournit un `CommunicationClientFactoryBase<TCommunicationClient>`. Cela fournit une implémentation de base de la `ICommunicationClientFactory` interface et effectue des tâches qui sont communes à toutes les piles de communication. (Ces tâches incluent l’utilisation une `ServicePartitionResolver` pour déterminer le point de terminaison du service). Clients implémentent généralement la classe CommunicationClientFactoryBase résumée pour gérer la logique spécifique à la pile de communication.

Le client de communication reçoit une adresse simplement et utilise pour se connecter à un service. Le client peut utiliser le protocole qu’il souhaite.

```csharp
class MyCommunicationClient : ICommunicationClient
{
    public ResolvedServiceEndpoint Endpoint { get; set; }

    public string ListenerName { get; set; }

    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}
```

Le client factory est principalement responsable de la création de clients de communication. Pour les clients qui ne pas mettre à jour une connexion permanente, par exemple un client HTTP, usine doit uniquement créer et renvoyer le client. Autres protocoles qui maintenir une connexion permanente, telles que certains protocoles binaires, également doivent être validées par l’usine afin de déterminer si la connexion doit être recréée.  

```csharp
public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient client)
    {
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
    {
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
    }

    protected override bool ValidateClient(string endpoint, MyCommunicationClient client)
    {
    }
}
```

Enfin, un gestionnaire d’exceptions est reponsible pour déterminer l’action à effectuer quand une exception se produit. Exceptions sont classées **reproductible** et **non reproductible**. 

 - Exceptions **non reproductible** simplement à nouveau levées à l’appelant. 
 - Exceptions de **Retriable** sont classées davantage **transitoires** et **non transitoires**.
  - Exceptions **transitoires** sont ceux qui peut être retentée simplement sans ré-résoudre l’adresse de point de terminaison du service. Ces inclura des problèmes réseau temporaires ou des réponses d’erreur service autres que ceux qui indiquent que l’adresse de point de terminaison de service n’existe pas. 
  - Exceptions **non transitoires** sont ceux qui nécessitent l’adresse de point de terminaison de service doivent être résolues nouveau. Ces bibliothèques incluent des exceptions qui indiquent que le point de terminaison de service n’est pas disponible, indiquant que le service a déplacé vers un autre nœud. 

La `TryHandleException` prend une décision sur une exception donnée. Si elle **ne sait pas** que les décisions à prendre concernant une exception, elle doit renvoyer **false**. Si elle **détermine-t-il** la décision à apporter, il doit définir le résultat en conséquence et retourne **true**.
 
```csharp
class MyExceptionHandler : IExceptionHandler
{
    public bool TryHandleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings, out ExceptionHandlingResult result)
    {
        // if exceptionInformation.Exception is known and is transient (can be retried without re-resolving)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, true, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;


        // if exceptionInformation.Exception is known and is not transient (indicates a new service endpoint address must be resolved)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, false, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;

        // if exceptionInformation.Exception is unknown (let the next IExceptionHandler attempt to handle it)
        result = null;
        return false;
    }
}
```
### <a name="putting-it-all-together"></a>Vue d’ensemble
Avec un `ICommunicationClient`, `ICommunicationClientFactory`, et `IExceptionHandler` basé sur un protocole de communication, un `ServicePartitionClient` est inclut tous les éléments et fournit la boucle de résolution d’adresse partition de gestion des erreurs et de service autour de ces composants.

```csharp
private MyCommunicationClientFactory myCommunicationClientFactory;
private Uri myServiceUri;

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

var result = await myServicePartitionClient.InvokeWithRetryAsync(async (client) =>
   {
      // Communicate with the service using the client.
   },
   CancellationToken.None);

```

## <a name="next-steps"></a>Étapes suivantes
 - Consultez un exemple de communication HTTP entre les services dans un [exemple de projet sur GitHUb](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Services/WordCount).

 - [Appels de procédure distante avec l’accès distant des Services fiables](service-fabric-reliable-services-communication-remoting.md)

 - [API qui utilise OWIN dans fiable Services Web](service-fabric-reliable-services-communication-webapi.md)

 - [Communication WCF à l’aide des Services fiables](service-fabric-reliable-services-communication-wcf.md)
