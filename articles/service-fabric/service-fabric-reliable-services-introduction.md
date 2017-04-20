<properties
   pageTitle="Vue d’ensemble du modèle de programmation Service TISSU fiable Service | Microsoft Azure"
   description="En savoir plus sur le modèle de programmation du Service TISSU Service fiable et commencer à écrire vos propres services."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor="vturecek; mani-ramaswamy"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/25/2016"
   ms.author="masnider;vturecek"/>

# <a name="reliable-services-overview"></a>Vue d’ensemble des Services fiable
TISSU Service Azure simplifie l’écriture et la gestion des Services fiable avec et sans état. Parle de ce document :

- Le modèle de programmation fiable Services pour les services avec et sans état.
- Les choix que vous devez effectuer lors de la rédaction d’un Service fiable.
- Certains scénarios et des exemples de conditions d’utilisation des Services fiables et comment ils sont écrits.

Services fiables est un des modèles de programmation disponibles sur tissu de Service. Pour plus d’informations sur le modèle de programmation intervenants fiable, voir [Introduction aux intervenants fiable de tissu de Service](service-fabric-reliable-actors-introduction.md).

Dans Service tissu, un service se compose de configuration, code de l’application et l’état (le cas échéant).

Service TISSU gère la durée de vie des services de mise en service et déploiement grâce à la mise à niveau et la suppression, via la [Gestion des applications de Service TISSU](service-fabric-deploy-remove-applications.md).

## <a name="what-are-reliable-services"></a>Que sont les Services fiables ?
Services fiables vous donne un modèle de programmation simple, puissant niveau supérieur pour vous aider à exprimer les points importants à votre application. Avec le modèle de programmation des Services fiables, vous obtenez :

- Pour les services avec état, le modèle de programmation fiable Services vous permet de manière cohérente et fiable stocker votre droit d’état à l’intérieur de votre service à l’aide des Collections fiable. Il s’agit d’un ensemble de classes collection hautement disponible qui seront familières à toute personne qui a utilisé c# collections simple. En règle générale, services nécessaires pour la gestion d’état fiable les systèmes externes. Avec les Collections fiable, vous pouvez stocker votre état en regard de votre cluster avec la même disponibilité et fiabilité que vous avez habitué à partir de disponibilité magasins externes et les latence supplémentaire améliorations apportées par la recherche le cluster et l’état.

- Un modèle simple d’exécution de votre propre code qui ressemble à ceci modèles que vous permettent de programmation. Votre code doit avoir un point d’entrée bien définis et le cycle de vie facile à gérer.

- Un modèle de communication enfichable. Utilisez le transport de votre choix, tels que HTTP avec [L’API Web](service-fabric-reliable-services-communication-webapi.md), WebSocket, protocoles TCP personnalisés, etc.. Les Services fiables fournissent certains rédaction de prédéfinies options que vous pouvez utiliser, ou vous pouvez fournir votre propre.

## <a name="what-makes-reliable-services-different"></a>En quoi les Services fiable différents ?
Services fiables dans tissu de Service est différent de services que vous avez écrit avant. Service TISSU fournit la fiabilité, disponibilité, la cohérence et extensibilité élevées.  

- **Fiabilité**--votre service reste la même dans les environnements non fiables où vos ordinateurs peuvent échouer ou appuyez sur les problèmes réseau.

- **Disponibilité**--votre service seront inaccessibles et injoignable. (Cela ne signifie que vous ne peut pas contenir des services qui ne peut pas être trouvés ou accéder à partir d’à l’extérieur.)

- **Extensibilité du**--Services sont découplées du matériel spécifique, et ils peuvent agrandir ou réduire comme bon vous semble via l’ajout ou la suppression de matériel ou de ressources virtuelles. Services sont facilement répartis (en particulier dans le cas dynamique) pour vous assurer que des parties indépendamment du service peuvent mettre à l’échelle et répondre aux échecs séparément. Pour finir, Service TISSU encourage services pour être léger en autorisant des milliers de services à mettre en service au sein d’un processus unique, plutôt que pour demander une ou en consacrer instances du système d’exploitation entiers à une seule instance de chaque charge de travail.

- **Cohérence**--toutes les informations stockées dans ce service peut être garantie cohérents (s’applique uniquement aux services avec état - informations supplémentaires sur cette version ultérieure)

## <a name="service-lifecycle"></a>Cycle de vie service
Si votre service est avec ou sans, fiable Services fournissent un cycle de vie simple qui vous permet de rapidement Branchez votre code et prise en main.  Il existe une ou deux méthodes dont vous avez besoin pour mettre en œuvre pour créer votre service et en cours d’exécution.

- **CreateServiceReplicaListeners/CreateServiceInstanceListeners** - il s’agit de l’endroit où le service définit la pile de communication qu’il souhaite utiliser. La pile de communication, par exemple [API Web](service-fabric-reliable-services-communication-webapi.md), est ce qui définit le point de terminaison écoute ou les points de terminaison du service (comment les clients atteignent il). Il définit également la façon dont les messages qui apparaissent finissent interaction avec le reste du code de service.

- **RunAsync** - il s’agit de l’endroit où votre service s’exécute sa logique métier. Le jeton d’annulation qui est fourni est un signal pour le moment acceptés doivent s’arrêter. Par exemple, si vous avez un service qui doit extraire des messages d’une file d’attente fiable en permanence et les traiter, il s’agit de l’endroit où se produit acceptés.

### <a name="service-startup"></a>Démarrage du service

Les événements du cycle de vie d’un Service fiable majeures sont :

1. L’objet de service (la chose qui est dérivée de la service sans état ou avec état) est construite.

2. La `CreateServiceReplicaListeners` / `CreateServiceInstanceListeners` méthode est appelée, ce qui donne le service une chance pour renvoyer une ou plusieurs récepteurs de communication de son choix.
  - Notez que cette étape est facultative, bien que la plupart des services expose directement un point de terminaison.

3. Une fois que les récepteurs de communication créés, il est ouvert.
  - Récepteurs de communication ont une méthode appelée `OpenAsync()`, qui est appelée à ce stade et qui retourne l’adresse d’écoute pour le service. Si votre Service fiable utilise une de la ICommunicationListeners intégrés, cela est géré à votre place.

4. Une fois que le récepteur de communication est ouvert, le `RunAsync()` méthode sur le service principal est appelée.
  - Notez que `RunAsync()` est facultative. Si le service effectue toutes ses travaux en réponse à un utilisateur appelle directement uniquement, il est inutile pour pouvoir implémenter `RunAsync()`.

### <a name="service-shutdown"></a>Arrêt du service

Lorsque le service est arrêté (doit être supprimée, mis à niveau ou a été déplacé) l’ordre d’appel apparaît en miroir : tout d’abord, le jeton d’annulation émanant `RunAsync()` est annulée ; puis `CloseAsync()` est appelée sur l’écoute de communication.

Il existe quelques points importants à noter à propos arrêt pour les services avec état :

- Service tissu ne promeut pas une autre copie de votre service statut principal jusqu'à `CloseAsync` et `RunAsync` ont renvoyé. Si vous utilisez un récepteur de communication intégrée, la `CloseAsync` méthode est géré à votre place.

- Il n’existe aucune limite de temps au retour de ces méthodes, vous perdez immédiatement la possibilité d’écrire aux Collections fiable et par conséquent ne pourrez pas terminer votre travail réel. Il est recommandé de revenir aussi rapidement que possible à la réception de la demande d’annulation.

## <a name="example-services"></a>Services d’exemple
Savoir ce modèle de programmation, jetons un coup rapide en deux différents services pour voir comment ces imbrication.

### <a name="stateless-reliable-services"></a>Services fiables sans état
Un service sans état est celui dans lequel il n’est littéralement aucun état maintenu au sein du service, ou l’état dans lequel se trouve peut être supprimé entièrement et ne nécessite pas de synchronisation, réplication, persistance ou disponibilité.

Par exemple, considérez une calculatrice qui n’a aucune mémoire et reçoit toutes les conditions et opérations à effectuer en même temps.

Dans ce cas, le RunAsync() du service peut être vide, car il n’existe aucun arrière-plan-traitement des tâches que le service doit faire. Lorsque le service de calculatrice est créé, il renverra une ICommunicationListener qui ouvre un point de terminaison écoute sur un certain port (par exemple [API Web](service-fabric-reliable-services-communication-webapi.md)). Ce point de terminaison écoute intègre aux différentes méthodes (exemple : « Ajouter (n1, n2) ») qui définissent public API la calculatrice.

Lorsqu’un appel est effectué à partir d’un client, la méthode appropriée est appelée, et le service de calculatrice effectue les opérations sur les données fournies et renvoie le résultat. Il ne stocke un état.

Stockez ne pas un état interne rend cette calculatrice exemple très simple. Mais la plupart des services ne sont pas réellement sans état. En revanche, ils externaliser leur état à un autre magasin. (Par exemple, une application web qui s’appuie sur tout en conservant l’état de session dans un magasin de stockage ou un cache n’est pas complètement sans état.)

Un exemple courant d’utilisation des services sans état dans tissu de Service est comme un serveur frontal qui expose l’API Not pour une application web. Le service frontal puis communique avec les services avec état pour terminer une demande de l’utilisateur. Dans ce cas, les appels à partir de clients sont dirigés vers un port connu, par exemple 80, où le service sans état est à l’écoute. Ce service sans état reçoit l’appel et détermine si l’appel est d’un tiers approuvé, comme ainsi que le service auquel il est destiné.  Ensuite, le service sans état transfère l’appel vers la partition correcte du service avec état et attend une réponse. Lorsque le service sans état reçoit une réponse, il répond au client d’origine.

### <a name="stateful-reliable-services"></a>Services fiables avec état
Un service avec état est une valeur qui peut comporter qu’une partie de l’état tenue cohérente et présenter afin que le service de la fonction. Envisagez d’un service qui constamment calcule une moyenne mobile de certains valeur basée sur les mises à jour qu’il reçoit. Pour cela, il doit avoir l’ensemble actuel des demandes entrantes qu’il faut processus, ainsi que la moyenne en cours. N’importe quel service qui récupère, traite et stocke les informations dans un magasin externe (par exemple, un Azure blob ou table magasin aujourd'hui) est dynamique. Il reste simplement son état dans le magasin d’état externe.

La plupart des services stockent aujourd'hui leur état avec l’extérieur, étant donné que le magasin externe est ce que fournit la fiabilité, disponibilité, la cohérence et extensibilité élevées pour cet état. Dans Service tissu, services avec état ne sont pas requis pour stocker leur état avec l’extérieur ; Service tissu prend en charge ces conditions requises pour le code de service et l’état du service.

Supposons que nous souhaitons écrire un service qui conduit les demandes d’une série de conversions qui doivent être effectuées sur une image et l’image qui doit être convertie.  Pour ce service, elle retournerait un récepteur de communication (supposons API Web) qui ouvre un port de communication et permet de soumissions via une API comme `ConvertImage(Image i, IList<Conversion> conversions)`. Dans cette API, le service peut prendre les informations et stocker la demande dans une file d’attente fiable et puis renvoyer un jeton au client afin qu’il peut effectuer le suivi de la demande (dans la mesure où les demandes peuvent prendre du temps).

Dans ce service, RunAsync peut être plus complexe. Le service peut avoir une boucle à l’intérieur de ses RunAsync qui extrait les demandes de IReliableQueue, effectue les conversions répertoriées et stocke les résultats dans IReliableDictionary, afin que lorsque le client est renvoyée, qu’elles puissent accéder à leurs images converties. Pour vous assurer que même en cas de problème l’image n’est perdue, ce Service fiable serait Tirez parti de la file d’attente, effectuer les conversions et stocker le résultat dans une transaction. Dans ce cas, le message n’est supprimé uniquement la file d’attente et les résultats sont stockés dans le dictionnaire résultat lorsque les conversions sont terminées. En cas de problème au milieu (par exemple, l’ordinateur qu'est exécute cette instance du code), la demande est conservé dans la file d’attente soient traités à nouveau.

Une chose à noter à propos de ce service est qu’il ressemble un service .NET normal. La seule différence est que les structures de données en cours d’utilisation (IReliableQueue et IReliableDictionary) sont fournis par tissu de Service et sont donc apportées hautement fiables, disponibles et cohérentes.

## <a name="when-to-use-reliable-services-apis"></a>Quand utiliser fiable API des Services
Si une des opérations suivantes caractérisation vos besoins de service d’application, vous devez prendre en compte fiable API des Services :

- Vous devez fournir le comportement de l’application dans plusieurs unités d’état (par exemple, des commandes et des lignes de facturation).

- État de votre application peut être naturellement modélisation comme dictionnaires fiable et files d’attente.

- Votre état doit être hautement disponible avec access faible latence.

- Votre application a besoin contrôler l’accès concurrentiels au ou la précision d’opérations traitées sur une ou plusieurs Collections fiable.

- Vous souhaitez gérer les communications ou contrôler le jeu de partition de votre service.

- Votre code a besoin d’un environnement d’exécution de threads libres.

- Votre application a besoin pour dynamiquement créer ou supprimer des dictionnaires fiable ou files d’attente en cours d’exécution.

- Vous devez contrôler sauvegarde Service TISSU communiquée par programme et de restaurer les fonctionnalités pour votre service état *.

- Votre application doit conserver l’historique des modifications de ses unités de l’état *.

- Vous souhaitez développer ou consommer état personnalisé, troisième partie avancés fournisseurs *.

> [AZURE.NOTE] * Fonctionnalités disponibles lors de la disponibilité générale du Kit de développement logiciel.


## <a name="next-steps"></a>Étapes suivantes
+ [Démarrage rapide Services fiable](service-fabric-reliable-services-quick-start.md)
+ [Utilisation avancée des Services fiables](service-fabric-reliable-services-advanced-usage.md)
+ [Le modèle de programmation intervenants fiable](service-fabric-reliable-actors-introduction.md)
