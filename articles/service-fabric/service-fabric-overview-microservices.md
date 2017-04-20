<properties
   pageTitle="Présentation microservices | Microsoft Azure"
   description="Découvrez pourquoi il est important de développement d’applications modernes de création d’applications cloud avec une approche microservices et comment Azure Service TISSU fournit une plateforme pour y parvenir"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/20/2016"
   ms.author="mfussell"/>

# <a name="why-a-microservices-approach-to-building-applications"></a>Pourquoi un microservices approche à la création d’applications ?
En tant que les développeurs de logiciels, il n’est pas nouveau dans comment nous envisager tenant compte d’une application en plusieurs parties composant. Il est le paradigme central d’orientation objet abstractions logicielles et des composants réutilisables. Aujourd'hui, cette factorisation tendance à prendre la forme de classes et d’interfaces entre des bibliothèques partagées et les couches de technologie. En règle générale, une approche hiérarchisée est considérée avec un magasin principale et une interface utilisateur frontale logique métier intermédiaire. Quel *a* changé durant les dernières années est que nous, en tant que développeurs, créons applications distribuées pour le cloud, pilotées par l’entreprise.

L’évolution des besoins professionnels sont :

- Créer et utiliser un service à l’échelle afin de communiquer aux clients de nouvelles zones géographiques (par exemple).
- Remise plus rapide de fonctionnalités pour être en mesure de répondre aux demandes des clients d’une manière agile.
- Meilleure utilisation des ressources pour réduire les coûts.

Ces besoins professionnels ont une incidence sur *comment* créer des applications.

Pour plus d’informations sur approche d’Azure microservices, lisez [Microservices : une révolution application optimisée par le cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="monolithic-vs-microservice-design-approach"></a>Monolithique comparatif approche de conception microservice
Toutes les applications évoluer au fil du temps. Applications réussies évoluent lorsqu’ils sont utiles aux personnes. Les applications échouées ne pas évoluer et finalement sont déconseillées. La question devient : quantité vous connaissez sur vos besoins d’aujourd'hui, et qu’ils seront à l’avenir ? Par exemple, supposons que vous créez une application de création de rapports pour un service. Vous êtes certain que l’application reste dans le cadre de votre entreprise et que les rapports sera courtes. Le choix de l’approche est différent, par exemple, création d’un service de diffusion d’un contenu vidéo à des millions de clients. Parfois, quelque chose sortir prise en tant que preuve de concept est le facteur déterminant, sachant que l’application peut être modifiée ultérieurement. Il est peu point excessive les ingénieurs quelque chose qui ne sont jamais utilisée. Il s’agit du compromis ingénierie habituel. En revanche, lorsque sociétés parlent de construction pour le cloud, l’attente est croissance et l’utilisation. Le problème est que croissance et l’échelle sont inattendus. Nous aimerions puissent prototype rapidement lors de connaître également que nous se trouvent sur un chemin d’accès de traiter réussite future. Il s’agit de l’approche démarrage épuré : créer, la mesure, en savoir plus, Parcourir.

Pendant l’era client-serveur, nous avaient tendance à se concentrer sur la création d’applications hiérarchisées à l’aide des technologies spécifiques de chaque niveau. Le terme « monolithiques » application est apparu pour ces approches. Les interfaces avaient tendance à entre les niveaux, et plus étroitement création utilisée entre des composants au sein de chaque niveau. Les développeurs conçu et classes compilées dans des bibliothèques, liés entre eux quelques fichiers exécutables et DLL pris en compte. Il existe des avantages offerts par cette approche monolithique création. Il est souvent plus simple concevoir et possède des appels plus rapides entre les composants, étant donné que ces appels dépassent souvent IPC. En outre, tout le monde teste un seul produit, qui est généralement plusieurs personnes-ressources efficace. L’inconvénient est qu’une association étroite entre les résultats de couches hiérarchisé et vous ne peuvent pas évoluer composants individuels. Si vous avez besoin effectuer des mises à jour ou correctifs, vous devez attendre que d’autres personnes à leur test terminé. Il est plus difficile à être agile.

Microservices adresse ces inconvénients et plus adaptés à la configuration requise pour business précédent, mais ils ont également à la fois des avantages que représenterait et des inconvénients. Les avantages de microservices sont que chacun d'entre eux compte généralement plus simple fonctionnalité de business, ce qui vous mettre à l’échelle vers le haut ou vers le bas, test, déployez et gérer de manière indépendante. L’un des avantages d’une approche microservice important sont qu’équipes sont plus par scénarios professionnels que pilotées par technologie, qui encourage l’approche par niveau. Dans la pratique, petites équipes développent un microservice basée sur un scénario de client, à l’aide des technologies qu'ils le souhaitent. En d’autres termes, l’organisation ne doit pas normaliser tech pour mettre à jour des applications monolithiques. Équipes individuelles propres services peuvent faire ce qui est pertinent pour leur basée sur les connaissances d’équipe ou ce qui est plus approprié pour le problème à résoudre. Dans la pratique, ayant un ensemble de technologies d’assistance recommandées, tel qu’un stockage NoSQL ou web particulier infrastructure d’application, est préférable.

L’inconvénient de microservices est fourni dans la gestion du nombre élevé d’entités distinctes et gérer les deuxièmes déploiements plus complexes et contrôle de version. Le trafic réseau entre le microservices augmente ainsi que la latence réseau correspondante. Un nombre important d’amenés, services granulaires est une recette pour un cauchemar en termes de performances. Sans outils qui vous permettent d’afficher ces dépendances, il est difficile de « voir » l’ensemble du système. Normes sont les apporter l’approche microservice Professionnel en acceptant sur la façon de communiquer et en cours tolérance des choses que vous avez besoin auprès d’un service, plutôt que rigide contrats. Il est important de définir ces contacts en amont dans la conception, dans la mesure où les services mises à jour indépendamment de celles uns. Une autre description origine pour la conception d’une approche microservices est « SOA Permissions ».


***Dans le bloc-notes, l’approche de conception microservices concerne une fédération découplée de services, avec les modifications indépendamment à chacun et respect des normes pour les communications.***


Lorsque plus d’applications cloud sont produites, personnes découvrez que cette décomposition de l’application globale services indépendantes, en fonction des scénarios est une meilleure approche à long terme.

## <a name="comparison-between-application-development-approaches"></a>Comparaison entre les approches de développement d’applications

![Développement d’applications de service TISSU plateforme][Image1]

1. Une application monolithique contient des fonctionnalités spécifiques au domaine et est normalement divisée par couches fonctionnelles, tels que web et données métier.

2. Vous adapter une application monolithique en clonant sur plusieurs serveurs/machines virtuelles/conteneurs.

3. Une application microservice sépare fonctionnalité en services plus petits distincts.

4. Cette approche peut évoluer en déployant chaque service séparément, créer des instances de ces services entre les serveurs/machines virtuelles/conteneurs.


Conception avec un microservice approche n’est pas la panacée pour tous les projets, mais il n’est plus étroitement aligné avec les objectifs décrites plus haut. En commençant par une approche monolithique peut être acceptable si vous savez que plus tard vous n’aurez pas la possibilité de reprendre le code dans une conception microservice si nécessaire. Plus couramment, vous commencez avec une application monolithique et lentement la fractionner en plusieurs étapes, en commençant par les domaines fonctionnels qui doivent être plus scalable ou agile.

En résumé, l’approche microservice consiste à composer votre application de services plus petits nombre.  Les services s’exécutent dans des conteneurs déployés sur un cluster d’ordinateurs. Petites équipes développement d’un service qui se concentre sur un scénario et tester séparément, version, déploiement et redimensionner chaque service afin de l’application dans son ensemble peut évoluer.

## <a name="what-is-a-microservice"></a>Qu’est un microservice ?

Il existe différentes définitions de microservices et recherche sur Internet fournit de nombreuses bonnes ressources qui fournissent leurs propres points de vue et les définitions. Toutefois, la plupart des caractéristiques suivantes des microservices est largement convenue :

- Encapsuler un scénario client ou entreprise. Quel est le problème à résoudre ?
- Développé par une petite équipe Ingénierie.
- Écrites dans n’importe quel langage de programmation et utiliser n’importe quelle infrastructure.
- Composé de code et l’état (le cas échéant), qui sont tous deux séparément une version déployée et mise à l’échelle.
- Interagir avec d’autres microservices via les protocoles et d’interfaces bien définies.
- Portent des noms uniques (URL) permet de résoudre leur emplacement.
- Restent cohérentes et disponible en présence d’erreurs.

Vous pouvez résumer ces caractéristiques dans :

***Applications Microservice sont composées de petites, indépendamment de différentes versions et scalable orientée client services communiquent avec eux via les protocoles standard avec des interfaces bien définies.***


Nous avons abordé les deux premiers points dans la section précédente et maintenant nous développer et à clarifier les autres colonnes.

### <a name="written-in-any-programming-language-and-use-any-framework"></a>Écrites dans n’importe quel langage de programmation et utiliser n’importe quelle infrastructure
En tant que développeurs, nous devons être libres de choisir quelle langue ou framework nous voulons, en fonction de nos compétences ou les besoins du service. Dans certains services, les avantages de C++ avant tout autre peut plus précieux. Dans d’autres services, la facilité de développement géré dans c# ou Java peut être plus importante. Dans certains cas, vous devrez peut-être utiliser une bibliothèque spécifique de tiers, technologie de stockage de données ou moyens d’exposer le service aux clients.

Une fois que vous avez choisi une technologie, vous accédez à la gestion du cycle de vie et opérationnelle et mise à l’échelle du service.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Permet de code et l’état à être gérées séparément, déployée et mise à l’échelle  

Toutefois vous choisissez d’écrire votre microservices, le code et vous pouvez également l’état doit indépendamment déployer, mettre à niveau et mettre à l’échelle. Il s’agit en réalité un des problèmes plus difficiles à résoudre, car il se résume à votre choix de technologies d’assistance. Pour la mise à l’échelle, comprendre comment partition (ou éclater) le code et l’état est difficile. Lorsque le code et l’état utilisent technologies distinctes (qui aujourd'hui, ils sont généralement faire), les scripts de déploiement pour votre microservice doivent être en mesure de faire face à l’échelle toutes les deux. Cela est également sur souplesse et la flexibilité, vous pouvez mettre à niveau certains la microservices sans avoir à les mettre à niveau en même temps.
Revenir à la monolithiques et microservice approche quelques instants, le diagramme suivant montre les différences entre l’approche pour le stockage de l’état.

#### <a name="state-storage-between-application-styles"></a>Stockage de l’état entre les styles d’application
![Stockage de l’état plateforme tissu de service][Image2]

***Sur la gauche est l’approche monolithique, avec une seule base de données et les niveaux de technologies spécifiques.***

***Sur la droite est l’approche microservices, un graphique de microservices interconnecté où état est généralement inclus dans l’étendue la microservice et différentes technologies sont utilisées.***

Dans une approche monolithique, généralement présente une seule base de données utilisée par l’application. L’avantage est qu’il s’agit d’un emplacement unique, ce qui facilite la déployer. Chaque composant peut avoir une seule table pour stocker son état. Équipes devront être strict dans séparer un état, ce qui est un défi. Il existe inévitablement temptations pour ajouter une nouvelle colonne à une table clientèle existante, effectuez une jointure entre les tables et créer des interdépendances au niveau de la couche de stockage. Une fois que cela se produit, vous ne pouvez pas mettre à l’échelle des composants individuels. Dans l’approche microservices, chaque service gère et stocke son propre état. Chaque service est responsable de la mise à l’échelle de code et état afin de répondre aux exigences du service. Un inconvénient est que lorsqu’il est nécessaire pour créer des vues ou des requêtes, des données de votre application vous deviez interrogation État hétérogènes stores. En règle générale, cela est résolu en demandant une microservice distinct qui crée une vue au sein d’une collection de microservices. Si vous devez effectuer plusieurs requêtes ad-hoc sur les données, chaque microservice envisagez d’écrire ses données dans un data warehouse service pour analytique en mode hors connexion.

Le contrôle de version est spécifique à la version déployée d’un microservice ainsi que plusieurs, différentes versions déploiement et l’exécution côte à côte. Le contrôle de version aborde les scénarios où une nouvelle version d’un microservice échoue au cours de la mise à niveau et doit être annulée dans une version antérieure. L’autre scénario pour le contrôle de version effectue les tests de B/A-style, où différents utilisateurs rencontrent différentes versions du service. Par exemple, il est courant pour mettre à niveau un microservice pour un ensemble spécifique de clients pour tester la nouvelle fonctionnalité avant de le déployer plus largement. Après la gestion du cycle de vie des microservices, est désormais ramène à la communication entre elles.


### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Interagit avec les autres microservices sur les protocoles et les interfaces bien définies

Cette rubrique une attention peu ici, dans la mesure où il existe une documentation complète sur architecture orientée services publié dans les 10 dernières années décrivant les modèles de communication. En règle générale, communications service utilisant une approche reste avec les protocoles HTTP et TCP et XML ou JSON en tant que le format de sérialisation. Du point de vue de l’interface, il s’agit adoptent l’approche de conception web. Mais rien ne vous empêche à l’aide des protocoles binaires ou des formats de vos propres données. Préparez-vous les personnes présentant une heure plus difficile à l’aide de votre microservices si celles-ci sont librement accessibles.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>A un nom unique (URL) permet de résoudre son emplacement

N’oubliez pas comment nous utiliserons indiquant que l’approche microservice est comme le web ? Comme le web, votre microservice doit être dédié à l’endroit où elle s’exécute. Si vous envisagez de machines et celui qui est en cours d’exécution un microservice particulier, éléments accède rapidement incorrectes. Dans la même façon que DNS est résolu en une URL spécifique d’un ordinateur particulier, votre microservice doit avoir un nom unique pour que son emplacement actuel peut être découvert. Microservices besoin de noms dédiés qui rendent indépendantes de l’infrastructure qu’ils exécutent sur. Cela signifie qu’il existe une interaction entre la façon dont votre service est déployé et comment il est détecté, car il doit être un Registre de service. De même, lorsqu’une machine échoue le Registre service doit vous indiquer l’endroit où le service est maintenant en cours d’exécution. Ce qui nous amène à la rubrique suivante : résilience et la cohérence.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Reste cohérente et disponible en présence d’échecs

En matière de gestion des défaillances inattendues est un des problèmes plus difficiles à résoudre, particulièrement dans un système distribué. Grande partie du code que nous écrivez en tant que développeurs est gestion des exceptions, et c’est également l’endroit où le plus de temps passé en phase de test. Mais le problème est plus complexe que d’écrire du code pour gérer les échecs. Que se passe-t-il en cas d’échec de l’ordinateur qui exécute la microservice ? Non seulement devez-vous détecter cet échec microservice (un problème difficile dans sa propre), mais vous devez également quelque chose à redémarrer votre microservice. Un microservice doit être résistant aux échecs et redémarrez souvent sur un autre ordinateur pour des raisons de disponibilité. Il est également fourni vers le bas jusqu'à l’état a été enregistré au nom de la microservice, où pouvez il récupérer cet état à partir de, et qu’il soit en mesure de redémarrer correctement. En d’autres termes, il doit être résilience dans le cluster (le processus redémarre) ainsi que la résilience dans l’état ou des données (sans perte de données et les données reste cohérent).

Résoudre les problèmes de résilience sont composés au cours d’autres scénarios, tels que lorsque des échecs de se produisent pendant une mise à niveau de l’application. Microservice, utiliser le système de déploiement, ne doit pas récupérer. Il doit également puis décider si elle peut continuer avancer vers la nouvelle version ou à la place de restaurer une version précédente pour maintenir un état cohérent. Questions telles que s’il existe suffisamment d’ordinateurs continuer transférer et comment récupérer des versions précédentes de la nécessité de microservice à prendre en compte. Cette fonctionnalité nécessite le microservice pour émettre des informations sur l’état pour pouvoir ces décisions.

### <a name="reports-health-and-diagnostics"></a>État de rapports et les diagnostics

Il peut sembler évident et il est souvent ignoré, mais il est essentiel qu’un microservice signale sa santé et les diagnostics de. Dans le cas contraire, il est peu d’informations à partir d’un point de vue opérations. Corrélation des événements de diagnostic sur un ensemble de services indépendants et les deuxièmes machine mauvais positionnement de horloge accélérer la lecture de l’ordre des événements sont difficile. Dans la même façon que vous interagissez avec un microservice sur les protocoles convenu et les formats de données, il se pose nécessité d’une standardisation dans comment ouvrir une session santé et des événements de diagnostic finissent dans un magasin d’événement pour interroger et l’affichage. Dans une approche microservices, il convient de clé que les différentes équipes s’accordent sur un format d’enregistrement unique.  Il doit être une approche cohérente à l’affichage des événements de diagnostic dans l’application dans son ensemble.

Santé est différente de diagnostics. Se trouve sur le microservice reporting son état actuel pour prendre des mesures appropriées. Un bon exemple fonctionne avec les mécanismes de déploiement et de mise à niveau pour assurer la disponibilité. Un service peut être actuellement en mauvais état en raison d’un blocage de processus ou redémarrage, mais toujours opérationnel de l’ordinateur. La dernière étape consiste à définir cet pire en effectuant une mise à niveau. La meilleure approche consiste à effectuer une enquête, au préalable, ou attendez que la microservice à récupérer. Événements d’état à partir d’un microservice permettent de prendre des décisions avisées et, en vigueur, aider à créer des services de réparation automatique.

## <a name="service-fabric-as-a-microservices-platform"></a>TISSU de service comme une plateforme microservices

TISSU Service Azure apparu se déconnecter de transition de Microsoft à partir de proposer des produits de zone, qui sont généralement monolithiques dans le style, à offrir des services. L’expérience d’utilisation des services volumineux, tels que des bases de données SQL Azure et DocumentDB, en forme de tissu de Service. La plateforme au fil du temps comme services plus adopté. Par ailleurs, Service TISSU dû exécuter où que vous soyez : pas seulement dans Azure, mais également dans les déploiements de Windows Server autonome.

***L’objectif du Service tissu consiste à résoudre les problèmes de disque durs de création et l’exécution d’un service et efficacement, l’utilisation des ressources de l’infrastructure pour équipes peuvent résoudre les problèmes d’entreprise à l’aide d’une approche microservices.***

Service TISSU fournit deux grands domaines pour vous aider à créer des applications avec une approche microservices :

- Une plateforme qui fournit des services pour déployer, mettre à niveau, détecter et redémarrez échec des services, découvrir l’emplacement du service, gérer l’état et surveiller l’intégrité du système. Ces services système permettent en vigueur la plupart des caractéristiques de microservices décrits précédemment.

-  API de programmation ou cadres, pour vous aider à créer des applications comme microservices : [intervenants fiables et services fiables](service-fabric-choose-framework.md). Bien entendu, vous pouvez utiliser de code de votre choix pour créer votre microservice. Mais ces API rendre la tâche plus simple, et qu’ils s’intègrent à la plate-forme à un niveau inférieur. Ainsi, par exemple, vous pouvez obtenir des informations d’intégrité et les diagnostics ou vous pouvez tirer parti de haute disponibilité intégrée.

***Service est considérée comme indépendante sur la façon dont vous créez votre service, et vous pouvez utiliser n’importe quelle technologie. Cependant, il fournit des API de programmation intégrés qui facilitent la création de microservices.***

### <a name="are-microservices-right-for-my-application"></a>Sont microservices vers la droite pour mon application ?

Peut-être. Ce que nous avons rencontré a été que lorsque les équipes plus dans Microsoft ont commencé à créer pour le nuage pour des raisons professionnelles, bon nombre d'entre eux bénéficié d’une approche de type microservice. Bing, par exemple, a développé microservices dans Rechercher les années. Pour les autres équipes, l’approche microservices était nouveau. Ils qui y trouvent pose difficile à résoudre en dehors de leurs zones principales de la puissance. C’est pourquoi Service TISSU acquise traction en tant que la technologie de choix pour créer des services.

L’objectif du Service tissu consiste à réduire la complexité de la création d’applications avec une approche microservice, afin que vous n’ayez pas traitée en tant que nombre coûteuses redesigns. Commencez petit, mettre à l’échelle lorsque cela est nécessaire, déconseiller services et ajouter de nouveaux évoluer avec l’utilisation du client, qui est l’approche. Nous avons également savoir qu’en réalité il existe de nombreux autres problèmes encore à résoudre afin de microservices plus accessible pour la plupart des développeurs. Conteneurs et le modèle de programmation acteur exemples de petites étapes dans cette direction, et nous vous assurer que plus innovations verront le jour pour simplifier ce processus.
 
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations :
    * [Vue d’ensemble de la terminologie tissu de service](service-fabric-technical-overview.md)
    * [Microservices : Révolution une application optimisée par le cloud](https://azure.microsoft.com/en-us/blog/microservices-an-application-revolution-powered-by-the-cloud/)


[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
