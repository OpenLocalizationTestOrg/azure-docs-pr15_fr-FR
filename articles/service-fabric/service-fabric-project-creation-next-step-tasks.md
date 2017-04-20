<properties
   pageTitle="Étapes suivantes de la création de projet tissu de service | Microsoft Azure"
   description="Cet article contient des liens vers un ensemble de tâches de développement core pour tissu de Service"
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
   ms.date="07/08/2016"
   ms.author="seanmck"/>

# <a name="your-service-fabric-application-and-next-steps"></a>Votre application de Service tissu et les étapes suivantes
Votre application Azure Service TISSU a été créée. Cet article décrit la création de votre projet et quelques étapes suivantes potentiels.

## <a name="your-application"></a>Votre application
Chaque nouvelle application inclut un projet d’application. Il peut y avoir un ou deux projets supplémentaires, selon le type de service choisi.

### <a name="the-application-project"></a>Le projet d’application
Le projet d’application se compose de :

- Un ensemble de références aux services qui composent votre application.

- Deux publier les profils (locaux et Cloud) que vous pouvez utiliser pour mettre à jour des préférences pour l’utilisation de différents environnements, tels que les préférences liées à un point de terminaison cluster et si vous souhaitez effectuer la mise à niveau des déploiements par défaut.

- Deux paramètre fichiers d’application (locales et Cloud) que vous pouvez utiliser pour gérer des configurations application spécifique à l’environnement, telles que le nombre de partitions à créer d’un service.

- Script de déploiement que vous pouvez utiliser pour déployer votre application à partir de la ligne de commande ou dans le cadre d’un pipeline de déploiement et l’intégration continu automatisé.

- Manifeste d’application, qui décrit l’application. Vous pouvez trouver le manifeste sous le dossier ApplicationPackageRoot.

### <a name="stateless-service"></a>Service sans état
Lorsque vous ajoutez un nouveau service sans état, Visual Studio ajoute un projet de service à votre solution qui inclut un type affilié à `StatelessService`. Le service incrémente une variable locale d’un compteur.

### <a name="stateful-service"></a>Service dynamique
Lorsque vous ajoutez un nouveau service avec état, Visual Studio ajoute un projet de service à votre solution qui inclut un type affilié à `StatefulService`. Le service incrémente un compteur dans son `RunAsync` méthode et stocke le résultat dans un `ReliableDictionary`.

### <a name="actor-service"></a>Service acteur
Lorsque vous ajoutez un nouveau acteur fiable, Visual Studio ajoute deux projets à votre solution : un projet acteur et un projet interface.

Le projet acteur fournit des méthodes de paramètre et obtenez la valeur d’un compteur qui n’est fiable conservée au sein de l’état de l’acteur. Le projet interface fournit une interface que d’autres services peuvent utiliser pour appeler l’acteur.

### <a name="stateless-web-api"></a>API Web sans état
Le projet de l’API Web sans état fournit un service web de base que vous pouvez utiliser pour ouvrir votre application à des clients externes. Pour plus d’informations sur le projet structuré, consultez [services Service TISSU Web API OWIN hébergement d’un serveur](service-fabric-reliable-services-communication-webapi.md).

### <a name="aspnet-core"></a>Core ASP.NET

Le Kit de développement de tissu Service propose le même ensemble d’ASP.NET Core modèles disponibles pour autonome ASP.NET Core projets : vide, [API Web][aspnet-webapi]et [l’Application Web][aspnet-webapp].

## <a name="next-steps"></a>Étapes suivantes
### <a name="create-an-azure-cluster"></a>Créer un cluster Azure
Le Kit de développement de tissu Service fournit un cluster local de développement et de test. Pour créer un cluster dans Azure, voir [configurer un cluster tissu de Service à partir du portail Azure][create-cluster-in-portal].

### <a name="try-deploying-to-azure-for-free-with-party-clusters"></a>Vous pouvez déploiement sur Azure gratuitement avec clusters de fête

Si vous voulez essayer le déploiement et la gestion des applications dans Azure sans la configuration de votre propre clusters, vous pouvez utiliser le [service de cluster tiers](http://aka.ms/tryservicefabric)gratuit.

### <a name="publish-your-application-to-azure"></a>Publier votre application sur Azure
Vous pouvez publier votre application directement à partir de Visual Studio à un cluster Azure. Pour plus d’informations, voir [publication de votre application vers Azure][publish-app-to-azure].

### <a name="use-service-fabric-explorer-to-visualize-your-cluster"></a>Utilisez l’Explorateur TISSU Service pour visualiser votre cluster
Service TISSU Explorer offre un moyen facile de visualiser votre cluster, y compris les applications déployées et disposition physique. Pour plus d’informations, voir [visualiser votre cluster à l’aide de l’Explorateur de Service TISSU][visualize-with-sfx].

### <a name="version-and-upgrade-your-services"></a>Version et mettre à niveau vos services
Service TISSU autorise versioning indépendant et la mise à niveau de services indépendantes dans une application. Pour plus d’informations, voir [le contrôle de version et la mise à niveau vos services][app-upgrade-tutorial].

### <a name="configure-continuous-integration-with-visual-studio-team-services"></a>Configurer l’intégration continue avec Visual Studio Team Services
Pour découvrir comment vous pouvez configurer un processus d’intégration continue pour votre application de Service tissu, voir [configurer l’intégration continue avec Visual Studio Team Services][ci-with-vso].


<!-- Links -->
[add-web-frontend]: service-fabric-add-a-web-frontend.md
[create-cluster-in-portal]: service-fabric-cluster-creation-via-portal.md
[publish-app-to-azure]: service-fabric-publish-app-remote-cluster.md
[visualize-with-sfx]: service-fabric-visualizing-your-cluster.md
[ci-with-vso]: service-fabric-set-up-continuous-integration.md
[reliable-services-webapi]: service-fabric-reliable-services-communication-webapi.md
[app-upgrade-tutorial]: service-fabric-application-upgrade-tutorial.md
[aspnet-webapi]: https://docs.asp.net/en/latest/tutorials/first-web-api.html
[aspnet-webapp]: https://docs.asp.net/en/latest/tutorials/first-mvc-app/index.html
