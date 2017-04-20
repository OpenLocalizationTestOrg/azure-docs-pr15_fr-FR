<properties
   pageTitle="Présentation du Gestionnaire de ressources de Service TISSU Cluster | Microsoft Azure"
   description="Introduction au Gestionnaire de ressources Cluster Service tissu."
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/19/2016"
   ms.author="masnider"/>

# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Présentation du Gestionnaire de ressources cluster tissu de Service
Traditionnellement gestion des systèmes informatiques ou un ensemble de services vouliez prise quelques ordinateurs physiques ou virtuels dédiés aux services spécifiques ou aux systèmes. De nombreux services principales ont été divisés en un niveau « web » et un niveau « données » ou « stockage », peut-être avec quelques autres composants spécialisés comme un cache. Autres types d’applications aurait un niveau de messagerie où les demandes passées et arrière, connecté à un niveau de travail pour une analyse ou la transformation nécessaire dans le cadre de la messagerie. Chaque type de charge de travail vous utilisez un machines spécifiques dédiées : la base de données poser quelques machines dédiés, les serveurs web un peu. Si un type particulier de charge de travail a provoqué les machines elle était définie pour exécuter trop chaud, puis vous avez ajouté plusieurs ordinateurs avec ce type de charge de travail configuré pour s’exécuter sur celui-ci ou remplacés quelques-unes des ordinateurs avec machines plus grandes. Facile. Si un ordinateur a échoué, cette partie de l’application globale exécuté au moins de capacité jusqu'à ce que l’ordinateur peut être restauré. Toujours assez simple (si pas nécessairement amusant).

Toutefois, nous allons Supposons que vous avez trouvé nécessaire d’évoluer et effectuent les conteneurs et/ou plongée microservice. Soudain vous vous retrouver avec des dizaines, des centaines et même des milliers de machines, des douzaines de différents types de services, peut-être des centaines de différentes instances de ces services, chacune avec une ou plusieurs instances ou réplicas pour haute disponibilité (HA).

Soudainement gestion de votre environnement n’est pas aussi simple que de gérer plusieurs ordinateurs sur des types de charges de travail unique. Virtuels et de vos serveurs n’est plus portent des noms (que vous *avez* changé mentalités [d’animaux à animaux](http://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) une fois tous les). Configuration est inférieure sur les ordinateurs et plus sur les services eux-mêmes. Matériel dédié est un appartient désormais au passé et services sont devenus des systèmes distribués petites, étalés sur plusieurs éléments plus petits du matériel.

En raison de l’annulation de votre application anciennement monolithique, hiérarchisée en services distincts s’exécutant sur matériel, vous avez à présent plusieurs combinaisons plus de traiter. Qui détermine les types de charges de travail peuvent s’exécutent sur le matériel, ou nombre ? Quelles charges de travail fonctionnent correctement sur le même matériel, et qui sont en conflit ? Lorsqu’un PC s’arrête... ce qui a été encore en cours d’exécution il ? Qui est chargé de s’assurer que la charge de travail s’exécute à nouveau ? Pour l’ordinateur (virtuel ?) revenir attente ou vos charges de travail basculer automatiquement sur d’autres ordinateurs et conserver en cours d’exécution ? Est intervention humaine obligatoire ? Qu’en est-il des mises à niveau dans ce type d’environnement ?

En tant que les développeurs et les opérateurs courantes dans cette trier du monde, nous aurons besoin d’aide pour gérer cette complexité, et vous obtenez le sens qu’un binge embauche et essayez de papier via la complexité avec des personnes n’est pas la bonne réponse.

Que faire ?

## <a name="introducing-orchestrators"></a>Présentation d’orchestrators
Un « Orchestrator » est le terme général pour un élément du logiciel qui permet aux administrateurs de gérer les types d’environnements. Orchestrators sont les composants qui prennent dans les requêtes telles que « Je souhaite 5 copies de ce service en cours d’exécution dans mon environnement », vérifiez la valeur true, puis (essayez) Laissez-la.

Orchestrators (pas l’homme) sont que Plongez-vous dans action lorsque échoue un ordinateur ou une charge de travail prend fin pour une raison quelconque. Orchestrators la plupart des traiter plus qu’échec, tels que contraire avec les nouveaux déploiements, gestion des mises à jour et les deuxièmes consommation des ressources, mais tous sont fondamentalement sur la gestion de que certaines souhaité état de configuration dans l’environnement. Vous souhaitez pouvoir indiquer un Orchestrator que vous souhaitez et faire l’essentiel. Chronos ou Marathon en haut Mesos flotte, essaim, Kubernetes et tissu de Service sont des exemples de Orchestrators (ou demandez-leur intégré). Tout le temps que la complexité de la gestion des déploiements réels dans différents types d’environnement plus sont créées et conditions agrandir et modifient.

## <a name="orchestration-as-a-service"></a>Orchestration en tant que service
La tâche de la Orchestrator au sein d’un cluster de Service tissu est gérée principalement par le Gestionnaire de ressources de Cluster. Le Gestionnaire de ressources de Service TISSU Cluster est un des Services système dans tissu de Service et est démarré automatiquement au sein de chaque cluster.  En règle générale, travail du Gestionnaire de ressources Cluster est divisée en trois parties :

1. Application des règles
2. Optimisation de votre environnement
3. Assistance dans d’autres processus

### <a name="what-it-isnt"></a>Qu’il n’est pas
Dans la couche N traditionnel web-applications est survenu toujours certaines notion de « Équilibrage de charge », généralement appelé un équilibrage de charge réseau (NLB) ou un équilibrage de charge Application (ALB) en fonction de l’endroit où il sat dans la pile réseau. Certains programmes d’équilibrage de charge sont matériel comme l’offre de F5 BigIP, d’autres logiciels en se basant tels que de Microsoft équilibrage de charge réseau. Dans d’autres environnements peuvent survenir s’intitule HAProxy dans ce rôle. Dans ces architectures de la tâche d’équilibrage de charge consiste à s’assurer que tous les ordinateurs différents frontal sans état ou les différents ordinateurs du cluster (approximativement) la même quantité de travail. Stratégies pour ce variés, d’envoyer chaque appel vers un autre serveur différent à session épinglage/caractère collant, à l’allocation d’estimation et appel réelle en fonction de son coût prévu charge machine en cours.

Notez qu’il s’agissait au mieux le mécanisme de veiller à ce que le niveau web est resté à peu près équilibrée. Stratégies d’équilibrage de la couche données ont été complètement différentes et dépendent le mécanisme de stockage des données, généralement centrage autour ont des données, la mise en cache, vues de base de données gérée et les procédures stockées, etc..

Alors que certains de ces stratégies sont intéressantes, le Gestionnaire de ressources de Service TISSU Cluster n’est pas tout comme un équilibrage de charge réseau ou d’un cache. Tandis qu’un équilibrage de charge réseau garantit que les extrémités avant sont équilibrées en déplaçant le trafic vers l’endroit où les services fonctionnent, le Gestionnaire de ressources de Service TISSU Cluster prend une stratégie complètement différente – fondamentalement, Service TISSU déplace les *services* à l’endroit où sont les plus adaptés (et attend le trafic ou les charger à suivre). Cela peut être, par exemple, les nœuds qui sont actuellement froids, car les services qui sont a-t-il font pas beaucoup de travail immédiatement, ou qui ont été supprimé ou déplacé un autre emplacement. Autre exemple le Gestionnaire de ressources Cluster pourrait également déplacer un service en dehors d’une machine qui doit être mis à niveau ou qui est surchargé en raison d’une pointe de consommation par les services qui ont été en cours d’exécution dessus. Car le Gestionnaire de ressources Cluster est responsable de déplacement des services autour (ne pas proposer le trafic réseau à l’endroit où les services sont déjà), il contient un ensemble de fonctionnalités significatives par rapport à ce que vous souhaitez trouver dans un programme d’équilibrage de charge réseau et emploie des stratégies fondamentalement différents pour s’assurer que les ressources matérielles dans le cluster sont bien utilisées.

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur le flux architecture et des informations dans le Gestionnaire de ressources de Cluster, consultez [cet article](service-fabric-cluster-resource-manager-architecture.md)
- Le Gestionnaire de ressources Cluster comporte beaucoup d’options pour décrire le cluster. Pour en savoir plus sur les consultez cet article sur [décrivant un cluster tissu de Service](service-fabric-cluster-resource-manager-cluster-description.md)
- Pour plus d’informations sur les autres options disponibles pour la configuration des services d’extraction la rubrique sur les autres configurations Cluster le Gestionnaire de ressources disponibles [en savoir plus sur la configuration des Services](service-fabric-cluster-resource-manager-configure-services.md)
- Métriques sont comment le Gestionnaire de ressources de Service TISSU Cluster gère la consommation et à la capacité dans le cluster. Pour en savoir plus sur les et comment les configurer consultez [cet article](service-fabric-cluster-resource-manager-metrics.md)
- Le Gestionnaire de ressources Cluster fonctionne avec les fonctionnalités de gestion du Service tissu. Pour en savoir plus sur cette intégration, lisez [cet article](service-fabric-cluster-resource-manager-management-integration.md)
- Pour savoir plus sur comment le Gestionnaire de ressources Cluster gère les soldes et chargement du cluster, consultez l’article sur [l’équilibrage de charge](service-fabric-cluster-resource-manager-balancing.md)
