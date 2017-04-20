<properties
   pageTitle="Créer un site web frontal pour votre application à l’aide de ASP.NET Core | Microsoft Azure"
   description="Exposer votre application de Service tissu sur le web à l’aide d’un projet ASP.NET Core Web API et communication entre service via ServiceProxy."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/11/2016"
   ms.author="seanmck"/>


# <a name="build-a-web-service-front-end-for-your-application-using-aspnet-core"></a>Créer frontal service web pour votre application à l’aide de Core ASP.NET

Par défaut, les services de structure de Service Azure ne fournissent pas une interface publique sur le web. Pour exposer des fonctionnalités de votre application pour les clients HTTP, vous avez besoin créer un projet web pour agir en tant que point d’entrée et puis communiquer à partir de là à vos services individuels.

Dans ce didacticiel, nous décrochez où nous étiez arrêté le didacticiel [créer votre première application dans Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md) et ajouter un service web devant le service de compteur avec état. Si vous ne le n'avez pas déjà fait, vous devez revenir en arrière et lancer tout d’abord ce didacticiel.

## <a name="add-an-aspnet-core-service-to-your-application"></a>Ajouter un service ASP.NET Core à votre application

Cœur ASP.NET est une structure de développement web léger et disponibilité sur plusieurs plateformes que vous pouvez utiliser pour créer l’interface utilisateur web moderne et web API. Nous allons ajouter un projet d’API Web ASP.NET à notre application existante.

>[AZURE.NOTE] Pour effectuer ce didacticiel, vous devez [installer .NET Core 1.0][dotnetcore-install].

1. Dans l’Explorateur, avec le bouton droit de **Services** du projet de l’application et sélectionnez **Ajouter > nouveau Service TISSU Service**.

    ![Ajout d’un nouveau service à une application existante][vs-add-new-service]

2. Dans la page **créer un Service** , choisissez **ASP.NET Core** et lui donner un nom.

    ![Sélection d’un service web ASP.NET sur la nouvelle boîte de dialogue service][vs-new-service-dialog]

3. La page suivante fournit un ensemble d’ASP.NET Core modèles de projet. Notez qu’il s’agit des mêmes modèles qui s’affichent si vous avez créé un projet ASP.NET Core en dehors d’une application de Service tissu. Pour ce didacticiel, nous allons choisir **API Web**. Toutefois, vous pouvez appliquer les mêmes concepts à la création d’une application web complète.

    ![Choisir le type de projet ASP.NET][vs-new-aspnet-project-dialog]

    Une fois votre projet API Web créé, vous aurez deux services dans votre application. Tout en continuant à créer votre application, vous allez ajouter plusieurs services de la même manière. Chacune peut être indépendamment gérées et mis à niveau.

>[AZURE.TIP] Pour plus d’informations sur la création d’ASP.NET Core services, consultez la [Documentation de base ASP.NET](https://docs.asp.net).

## <a name="run-the-application"></a>Exécuter l’application

Pour avoir une idée de ce que nous avons fait, nous allons déployer la nouvelle application et consultez le comportement par défaut qui fournit le modèle ASP.NET Core Web API.

1. Dans Visual Studio pour déboguer l’application, appuyez sur F5.

2. Lorsque le déploiement est terminé, Visual Studio démarre le navigateur à la racine du service API Web ASP.NET--semblable à http://localhost:33003. Le numéro de port est aléatoirement et peut être différent sur votre ordinateur. Le modèle ASP.NET Core Web API ne fournit pas le comportement par défaut pour la racine, afin que vous obtenez une erreur dans le navigateur.

3. Ajouter `/api/values` à l’emplacement dans le navigateur. Cette action ouvre le `Get` méthode sur le ValuesController dans le modèle de l’API Web. Elle renvoie la réponse par défaut qui est fournie par le modèle--un tableau JSON qui contient deux chaînes :

    ![Valeurs par défaut retournées par modèle API Web de base du ASP.NET][browser-aspnet-template-values]

    À la fin du didacticiel, nous avons sera ont remplacé ces valeurs par défaut avec la valeur la plus récente compteur de notre service dynamique.


## <a name="connect-the-services"></a>Connecter les services

Service TISSU offre une flexibilité complète dans vos communications grâce à des services fiables. Dans une seule application, vous devrez services sont accessibles via TCP, d’autres services sont accessibles via une API REST HTTP et toujours d’autres services sont accessibles via des sockets web. Pour l’arrière-plan sur les options disponibles et compromis, consultez [communication avec les services](service-fabric-connect-and-communicate-with-services.md). Dans ce didacticiel, nous suivez l’une des approches plus simples et utiliser la `ServiceProxy` / `ServiceRemotingListener` classes qui sont fournies dans le Kit de développement.

Dans la `ServiceProxy` approche (basée sur les appels de procédure distante ou RPC), vous définissez une interface en tant que le contrat public pour le service. Ensuite, vous utilisez cette interface pour générer une classe proxy pour interagir avec le service.


### <a name="create-the-interface"></a>Créer l’interface

Nous allons commencer par créer l’interface en tant que le contrat entre le service dynamique et de ses clients, y compris le projet ASP.NET Core.

1. Dans l’Explorateur, avec le bouton droit de votre solution, puis cliquez sur **Ajouter** > **Nouveau projet**.

2. Sélectionnez l’entrée **Visual c#** dans le volet de navigation gauche, puis sélectionnez le modèle de **Bibliothèque de classes** . Assurez-vous que la version .NET Framework est définie sur **4.5.2**.

    ![Création d’un projet interface à votre service d’état][vs-add-class-library-project]

3. Dans l’ordre d’une interface puissent être utilisés par `ServiceProxy`, il doit dérivé de l’interface IService. Cette interface est incluse dans une des offres de Service TISSU NuGet. Pour ajouter le package, avec le bouton droit de votre projet de bibliothèque de classes, puis sélectionnez **Manage NuGet Packages**.

4. Rechercher le package **Microsoft.ServiceFabric.Services** et installez-le.

    ![Ajout du package NuGet Services][vs-services-nuget-package]

5. Dans la bibliothèque de classes, créer une interface avec une méthode unique, `GetCountAsync`, et étendre l’interface de IService.

    ```c#
    namespace MyStatefulService.Interfaces
    {
        using Microsoft.ServiceFabric.Services.Remoting;

        public interface ICounter: IService
        {
            Task<long> GetCountAsync();
        }
    }
    ```


### <a name="implement-the-interface-in-your-stateful-service"></a>Mettre en œuvre l’interface dans votre service d’état

Maintenant que nous avons défini l’interface, nous devons implémenter dans le service d’état.

1. Dans votre service avec état, ajoutez une référence au projet de bibliothèque de classes qui contient l’interface.

    ![Ajout d’une référence au projet de bibliothèque de classes dans le service d’état][vs-add-class-library-reference]

2. Localisez la classe héritant de `StatefulService`, tel que `MyStatefulService`, et l’étendre pour mettre en œuvre la `ICounter` interface.

    ```c#
    using MyStatefulService.Interfaces;

    ...

    public class MyStatefulService : StatefulService, ICounter
    {        
          // ...
    }
    ```

3. À présent implémenter la méthode unique qui est définie dans le `ICounter` interface, `GetCountAsync`.

    ```c#
    public async Task<long> GetCountAsync()
    {
      var myDictionary =
        await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

        using (var tx = this.StateManager.CreateTransaction())
        {          
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");
            return result.HasValue ? result.Value : 0;
        }
    }
    ```


### <a name="expose-the-stateful-service-using-a-service-remoting-listener"></a>Exposer le service dynamique à l’aide d’un programme d’écoute service distant

Avec la `ICounter` interface implémentée, la dernière étape de l’activation du service dynamique peut être appelé à partir d’autres services consiste à ouvrir un canal de communication. Pour les services avec état, tissu de Service fournit une méthode non prioritaire appelée `CreateServiceReplicaListeners`. Avec cette méthode, vous pouvez spécifier une ou plusieurs récepteurs de communication, en fonction du type de communication que vous souhaitez activer votre service.

>[AZURE.NOTE] La méthode d’ouverture d’un canal de communication aux services sans état équivalente est appelée `CreateServiceInstanceListeners`.

Dans ce cas, nous remplacent les existantes `CreateServiceReplicaListeners` méthode et fournir une instance de `ServiceRemotingListener`, qui crée un point de terminaison RPC qui peut être appelé à partir de clients via `ServiceProxy`.  

```c#
using Microsoft.ServiceFabric.Services.Remoting.Runtime;

...

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new List<ServiceReplicaListener>()
    {
        new ServiceReplicaListener(
            (context) =>
                this.CreateServiceRemotingListener(context))
    };
}
```


### <a name="use-the-serviceproxy-class-to-interact-with-the-service"></a>Utilisez la classe ServiceProxy pour interagir avec le service

Notre service avec état est maintenant prêt à recevoir le trafic provenant d’autres services. Sorte que reste consiste à ajouter le code de communiquer avec lui à partir du service web ASP.NET.

1. Dans votre projet ASP.NET, ajoutez une référence à la bibliothèque de classes qui contient le `ICounter` interface.

2. Dans le menu **Générer** , ouvrez le **Gestionnaire de Configuration**. Vous devriez voir ressemblant à ceci :

    ![Bibliothèque de classes configuration manager affichant comme AnyCPU][vs-configuration-manager]

    Notez que le projet de bibliothèque de classes, **MyStatefulService.Interface**, est configuré pour générer les UC. Pour fonctionner correctement avec tissu de Service, il doit être explicitement destiné aux x64. Cliquez sur la liste déroulante, cliquez sur **Nouveau**, puis créez une x64 configuration de la plate-forme.

    ![Création de la nouvelle plate-forme de bibliothèque de classes][vs-create-platform]

3. Ajoutez le package Microsoft.ServiceFabric.Services au projet ASP.NET, comme que précédemment pour le projet de bibliothèque de classes. Ceci fournira la `ServiceProxy` cours.

4. Dans le dossier **Controllers** , ouvrez la `ValuesController` cours. Notez que la `Get` méthode actuellement simplement renvoie un tableau codé en dur de « valeur1 » et « valeur2-- » qui correspond à ce que nous l’avons vu plus haut dans le navigateur. Remplacez cette mise en œuvre par le code suivant :

    ```c#
    using MyStatefulService.Interfaces;
    using Microsoft.ServiceFabric.Services.Remoting.Client;

    ...

    public async Task<IEnumerable<string>> Get()
    {
        ICounter counter =
            ServiceProxy.Create<ICounter>(new Uri("fabric:/MyApplication/MyStatefulService"), new ServicePartitionKey(0));

        long count = await counter.GetCountAsync();

        return new string[] { count.ToString() };
    }
    ```

    La première ligne de code est la clé. Pour créer le proxy ICounter au service dynamique, vous devez fournir deux éléments d’information : un ID de partition et le nom du service.

    Vous pouvez utiliser partition aux services avec état échelle en divisant leur état dans différentes plages, selon une clé que vous définissez, tel qu’un ID de client ou code postal. Dans notre application simple, le service dynamique comporte uniquement une partition, afin que la clé n’a pas d’importance. N’importe quelle touche que vous fournissez, vous aboutissez à la même partition. Pour en savoir plus sur partition services, Découvrez [comment partition fiable tissu de Services](service-fabric-concepts-partitioning.md).

    Le nom du service est un URI du tissu formulaire : /&lt;nom de l’application&gt;/&lt;service_name&gt;.

    Avec ces deux éléments d’information, tissu de Service peut identifier de l’ordinateur sur lequel demandes doivent être envoyées à. La `ServiceProxy` classe gère également en toute transparence le cas où l’ordinateur qui héberge la partition de service dynamique échoue et un autre ordinateur doit être promu pour prendre sa place. Cette abstraction rend l’écriture le code du client de traiter d’autres services beaucoup plus simples.

    Une fois que nous avons le proxy, nous suffit d’appeler le `GetCountAsync` méthode et retourner son résultat.

5. Appuyez sur F5 pour exécuter l’application modifiée. Comme avant, Visual Studio se lance automatiquement le navigateur à la racine du projet web. Ajouter le chemin d’accès « api/values », et vous devez voir la valeur actuelle de compteur retournée.

    ![La valeur du compteur avec état affichée dans le navigateur][browser-aspnet-counter-value]

    Actualisez le navigateur régulièrement afin de vérifier la valeur du compteur de mettre à jour.


>[AZURE.WARNING] Le serveur web ASP.NET Core fourni dans le modèle, appelé Kestrel, est [actuellement pas pris en charge pour la gestion des diriger le trafic internet](https://docs.asp.net/en/latest/fundamentals/servers.html#kestrel). Scénarios de production, envisagez d’héberger vos points de terminaison ASP.NET Core derrière la [Gestion des API] [ api-management-landing-page] ou une autre passerelle via internet. Notez que tissu de Service n’est pas pris en charge pour le déploiement au sein de IIS.


## <a name="what-about-actors"></a>Qu’en est-il des intervenants ?

Ce didacticiel axée sur l’ajout d’un site web frontal communiqué avec un service dynamique. Toutefois, vous pouvez suivre un modèle similaire à parler à intervenants. En fait, il est un peu plus simple.

Lorsque vous créez un projet d’un acteur, Visual Studio génère automatiquement un projet d’interface pour vous. Vous pouvez utiliser cette interface pour générer un proxy acteur dans le projet web pour communiquer avec l’acteur. Le canal de communication est fourni automatiquement. Vous n’avez pas besoin de faire tout ce qui équivaut à l’établissement d’une `ServiceRemotingListener` comme vous le faisiez pour le service dynamique dans ce didacticiel.

## <a name="how-web-services-work-on-your-local-cluster"></a>Fonctionnement des services web sur votre cluster local

En règle générale, vous pouvez déployer exactement l’application de Service TISSU même sur un cluster comportant plusieurs ordinateur que vous avez déployée sur votre cluster local et être très sûr qu’il fonctionne comme prévu. C’est parce que votre cluster local est simplement une configuration cinq nœuds qui est réduite à une seule machine.

Lorsqu’il s’agit de services web, cependant, il est une nuance de clé. Lorsque votre cluster se trouve derrière un équilibrage de charge, comme nous le faisons dans Azure, vous devez vous assurer que vos services web sont déployés sur chaque ordinateur dans la mesure où l’équilibrage de charge sera alternées simplement le trafic sur les ordinateurs. Vous pouvez le faire en définissant la `InstanceCount` service pour la valeur spéciale « -1 ».

En revanche, lorsque vous exécutez un service web localement, vous devez vous assurer que seule une instance du service est en cours d’exécution. Dans le cas contraire, vous exécuterez de conflits à partir de plusieurs processus qui écoutent sur le chemin d’accès et le port. Par conséquent, le nombre d’instances de service web doit être défini sur « 1 » pour les déploiements locaux.

Pour apprendre à configurer des valeurs différentes pour environnement différent, voir [Gestion des paramètres de l’application pour les environnements multiples](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="next-steps"></a>Étapes suivantes

- [Créer un cluster dans Azure pour déployer votre application dans le cloud](service-fabric-cluster-creation-via-portal.md)
- [Pour plus d’informations sur la communication avec les services](service-fabric-connect-and-communicate-with-services.md)
- [En savoir plus sur partition services avec état](service-fabric-concepts-partitioning.md)

<!-- Image References -->

[vs-add-new-service]: ./media/service-fabric-add-a-web-frontend/vs-add-new-service.png
[vs-new-service-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-service-dialog.png
[vs-new-aspnet-project-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-aspnet-project-dialog.png
[browser-aspnet-template-values]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-template-values.png
[vs-add-class-library-project]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-project.png
[vs-add-class-library-reference]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-reference.png
[vs-services-nuget-package]: ./media/service-fabric-add-a-web-frontend/vs-services-nuget-package.png
[browser-aspnet-counter-value]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-counter-value.png
[vs-configuration-manager]: ./media/service-fabric-add-a-web-frontend/vs-configuration-manager.png
[vs-create-platform]: ./media/service-fabric-add-a-web-frontend/vs-create-platform.png


<!-- external links -->
[dotnetcore-install]: https://www.microsoft.com/net/core#windows
[api-management-landing-page]: https://azure.microsoft.com/en-us/services/api-management/
