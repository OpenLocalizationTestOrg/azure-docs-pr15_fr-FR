<properties
   pageTitle="Architecture du service fiable | Microsoft Azure"
   description="Vue d’ensemble de l’architecture du Service fiable pour les services avec ou sans état"
   services="service-fabric"
   documentationCenter=".net"
   authors="AlanWarwick"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/30/2016"
   ms.author="alanwar"/>

# <a name="architecture-for-stateful-and-stateless-reliable-services"></a>Architecture de Services fiable avec ou sans état

Un Service fiable Azure Service TISSU peut être avec ou sans. Chaque type de service s’exécute dans une architecture spécifique. Ces architectures sont décrites dans cet article.
Consultez la [Présentation du Service fiable](service-fabric-reliable-services-introduction.md) pour plus d’informations sur les différences entre les services avec ou sans état.

## <a name="stateful-reliable-services"></a>Services fiables avec état

### <a name="architecture-of-a-stateful-service"></a>Architecture d’un service dynamique
![Diagramme d’architecture d’un service dynamique](./media/service-fabric-reliable-services-platform-architecture/reliable-stateful-service-architecture.png)

### <a name="stateful-reliable-service"></a>Service fiable avec état

Un Service fiable dynamique peut dérivent de classe StatefulServiceBase ou StatefulService. Ces deux base classes sont fournis par tissu de Service. Ils offrent différents niveaux de prise en charge et abstraction pour le service dynamique à l’interface avec tissu de Service--et participer en tant que service au sein du cluster tissu de Service.

StatefulService dérivée de StatefulServiceBase. StatefulServiceBase propose des services de plus de flexibilité, mais requiert plus comprendre les données internes de tissu de Service.
Pour plus d’informations sur les caractéristiques de l’écriture de services en utilisant les classes StatefulService et StatefulServiceBase, consultez la [vue d’ensemble du Service fiable](service-fabric-reliable-services-introduction.md) et [l’utilisation avancée de Service fiable](service-fabric-reliable-services-advanced-usage.md) .

Les deux classes de base de gérer la durée de vie et le rôle de l’implémentation du service. L’implémentation du service peut substituer des méthodes virtuelles des deux classes base si l’implémentation du service a travail à effectuer à ces points du cycle de vie d’implémentation de service--ou s’il souhaite créer un objet récepteur de communication. Notez que bien que l’implémentation de service peut implémenter son propre objet récepteur de communication exposant ICommunicationListener, dans le schéma ci-dessus, le récepteur de communication est implémenté par tissu de Service--de l’implémentation du service qui utilise un récepteur de communication qui est implémenté par tissu de Service.

Un Service fiable dynamique utilise le Gestionnaire d’état fiable pour tirer parti des collections fiables. Collections fiables sont des structures de données locales hautement disponibles pour le service--, ils sont toujours disponibles, quel que soit le basculement de service. Chaque type de collection fiable est implémentée par un fournisseur d’état fiable.
Pour plus d’informations sur les collections fiables, consultez la [vue d’ensemble des collections fiable](service-fabric-reliable-services-reliable-collections.md).

### <a name="reliable-state-manager-and-state-providers"></a>Gestionnaire d’état fiable et fournisseurs d’état

Le Gestionnaire d’état fiable est l’objet qui gère les fournisseurs d’état fiable. Il a la possibilité de créer, supprimer, énumérer et vérifier que les fournisseurs état fiable persistante et hautement disponible. Une instance de fournisseur état fiable représente une instance de structure de données persistante et disponibilité, par exemple un dictionnaire ou une file d’attente.

Chaque fournisseur état fiable expose une interface qui est utilisée par un service dynamique pour interagir avec le fournisseur d’état fiable. Par exemple, IReliableDictionary est utilisé pour communiquer avec le dictionnaire fiable, tandis que IReliableQueue est utilisé pour communiquer avec la file d’attente fiable. Tous les fournisseurs état fiable implémenter l’interface IReliableState.

Le Gestionnaire d’état fiable comporte une interface nommée IReliableStateManager, qui permet d’accéder à celui-ci à partir d’un service dynamique. Interfaces pour les fournisseurs d’état fiable sont retournés via IReliableStateManager.

Le Gestionnaire d’état fiable utilise une architecture de plug-in afin que les nouveaux types de collections fiables peuvent être branchés dynamiquement.

Le dictionnaire fiable et la file d’attente fiable reposent sur la mise en œuvre d’un magasin différentiel High performance, version.

### <a name="transactional-replicator"></a>Transactions replicator

Le composant replicator transactions est chargé de s’assurer que l’état d’un service (autrement dit, l’état dans le Gestionnaire d’état fiable et les collections fiables) est cohérent entre tous les réplicas du service en cours d’exécution. Elle garantit également que l’état est conservé dans le journal. Les interfaces Gestionnaire état fiable avec le réplicateur transactions via un mécanisme privé.

Le réplicateur transactions utilise un protocole réseau pour communiquer état avec d’autres réplicas de l’instance de service afin que tous les réplicas ont des informations d’état à jour.

Le réplicateur transactions utilise un journal pour conserver les informations d’état afin que les informations d’état survit processus ou le nœud se bloque. L’interface dans le journal est via un mécanisme privé.

### <a name="log"></a>Journal

Le composant journal fournit un magasin permanent High performance qui peut être optimisé pour l’écriture de rotation ou SSD disques.  La conception du journal est pour le stockage permanent (c'est-à-dire, disques durs) sont en local pour les nœuds qui exécutent le service dynamique. Cela permet de latence bas et haut débit, par rapport à stockage permanent à distance, ce qui n’est pas local vers le nœud.

Le composant journal utilise plusieurs fichiers journaux. Il existe un fichier journal partagé à l’échelle du nœud que tous les réplicas utilisent comme il peut fournir la latence les plus basses et les plus haut débit pour stocker les données d’état. Par défaut le journal partagé est placé dans le répertoire de travail nœud tissu de Service, mais il peut également être configuré pour être placé à un autre emplacement, préférence sur un disque réservé pour que le journal partagé. Chaque réplica pour le service a également un fichier journal dédié et le journal dédié est placé dans le répertoire du service travail. Il n’existe aucun mécanisme pour configurer le journal dédié à placer à un emplacement différent.

Le journal partagé est une zone de transition pour les informations d’état du réplica, tandis que le fichier journal dédié est la destination finale où il est conservé. Dans cette conception, les informations d’état sont d’abord écrits dans le fichier journal partagé et puis différée déplacées vers le fichier journal dédié en arrière-plan. De cette façon, l’écriture dans le journal partagé aurait la latence les plus basses et les plus haut débit qui permet au service de progresser plus rapidement.

Lit et écriture dans le journal partagé est effectuées via IO direct vers préallouée espace sur le disque pour le fichier journal partagé. Pour autoriser une utilisation optimale de l’espace disque sur le disque avec journaux dédiés, le fichier journal dédié est créé comme un fichier incomplet NTFS. Notez que cette option permet d’overprovisioning d’espace disque et le système d’exploitation affiche les fichiers journaux dédié à l’aide de davantage d’espace disque que soit utilisé.

En dehors d’une interface utilisateur en mode minimal dans le journal, le journal est écrit comme un pilote en mode noyau. En tant qu’un pilote en mode noyau, le journal peut vous fournir les meilleures performances à tous les services qui l’utilisent.

Pour plus d’informations sur la configuration du journal, voir [Configuration des Services fiables avec état](service-fabric-reliable-services-configuration.md).

## <a name="stateless-reliable-service"></a>Service fiable sans état

### <a name="architecture-of-a-stateless-service"></a>Architecture d’un service sans état
![Diagramme d’architecture d’un service sans état](./media/service-fabric-reliable-services-platform-architecture/reliable-stateless-service-architecture.png)

### <a name="stateless-reliable-service"></a>Service fiable sans état

Mises en œuvre de service sans état dérivent de la classe StatelessService ou StatelessServiceBase. La classe StatelessServiceBase permet davantage de flexibilité que la classe StatelessService.
Les deux classes de base de gérer la durée de vie et le rôle d’un service.

L’implémentation du service peut substituer des méthodes virtuelles des deux classes base si le service a travail à effectuer à ces points du cycle de vie service--ou s’il souhaite créer un objet récepteur de communication. Notez que bien que le service peut implémenter son propre objet récepteur de communication exposant ICommunicationListener, dans le schéma ci-dessus, le récepteur de communication est implémenté par tissu de Service, comme l’implémentation de ce service utilise un récepteur de communication qui est implémenté par tissu de Service.

Pour plus d’informations sur les caractéristiques de l’écriture de services en utilisant les classes StatelessService et StatelessServiceBase, consultez la [vue d’ensemble du Service fiable](service-fabric-reliable-services-introduction.md) et [l’utilisation avancée de Service fiable](service-fabric-reliable-services-advanced-usage.md) .

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur tissu de Service, voir :

[Présentation du service fiable](service-fabric-reliable-services-introduction.md)

[Démarrage rapide](service-fabric-reliable-services-quick-start.md)

[Vue d’ensemble des collections fiable](service-fabric-reliable-services-reliable-collections.md)

[Service fiable utilisation avancée](service-fabric-reliable-services-advanced-usage.md)

[Configuration du service fiable](service-fabric-reliable-services-configuration.md)  
