<properties
   pageTitle="Extensibilité élevées de services de structure de Service | Microsoft Azure"
   description="Décrit comment mettre à l’échelle de services de structure de Service"
   services="service-fabric"
   documentationCenter=".net"
   authors="appi101"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/10/2016"
   ms.author="aprameyr"/>

# <a name="scaling-service-fabric-applications"></a>Mise à l’échelle des applications de Service TISSU
TISSU Service Azure facilite la création d’applications scalable par l’équilibrage de charge services, partitions et duplications sur tous les nœuds dans un cluster. Cela permet l’utilisation des ressources maximale.

Grande échelle pour les applications de Service TISSU il est possible de deux façons :

1. Mise à l’échelle au niveau de la partition

2. Mise à l’échelle au niveau du nom de service

## <a name="scaling-at-the-partition-level"></a>Mise à l’échelle au niveau de la partition
Service tissu prend en charge partition chaque service en plusieurs partitions plus petites. La [vue d’ensemble de partition](service-fabric-concepts-partitioning.md) fournit des informations sur les types de schémas de partition pris en charge. Les réplicas de chaque partition sont réparties sur les nœuds dans un cluster. Envisagez d’un service qui utilise un schéma de partition sont avec une clé faible de 0, une clé de haute de 99 et quatre partitions. Dans un cluster à trois nœuds, le service peut être disposé avec quatre réplicas qui partagent les ressources de chaque nœud comme illustré ici :

![Disposition des partitions avec trois nœuds](./media/service-fabric-concepts-scalability/layout-three-nodes.png)

Augmenter le nombre de nœuds permet de tissu de Service d’utiliser les ressources sur les nouveaux nœuds en déplaçant certaines réplica aux nœuds vides. En augmentant le nombre de nœuds à quatre, le service a maintenant trois répliques s’exécutant sur chaque nœud (de différentes partitions), ce qui permet de performances et meilleure utilisation des ressources.

![Disposition des partitions avec quatre nœuds](./media/service-fabric-concepts-scalability/layout-four-nodes.png)

## <a name="scaling-at-the-service-name-level"></a>Mise à l’échelle au niveau du nom de service
Une instance de service est une instance spécifique d’un nom d’application et un nom de type de service (voir [cycle de vie d’application Service TISSU](service-fabric-application-lifecycle.md)). Lors de la création d’un service, vous spécifiez la partition devant servir de modèle (voir [partition Service TISSU services](service-fabric-concepts-partitioning.md)).

Il est le premier niveau de mise à l’échelle par nom de service. Vous pouvez créer de nouvelles instances d’un service, avec différents niveaux de partition, comme votre anciennes instances de service deviennent occupé (e). Cela permet de nouveaux consommateurs de service à utiliser des instances de service moins occupé, plutôt que ceux occupé.

Une option pour augmenter la capacité, ainsi que les nombres de partition croissante ou décroissante, consiste à créer une nouvelle instance de service avec un nouveau jeu de partition. Cela complique, cependant, comme n’importe quel beaucoup de clients ont besoin de savoir quand et comment utiliser le service nommé différemment.

### <a name="example-scenario-embedded-dates"></a>Exemple de scénario : incorporé des dates
Un scénario possible serait d’utiliser les informations de date dans le cadre du nom du service. Par exemple, vous pouvez aussi utiliser une instance de service avec un nom spécifique pour tous les clients qui rejoint en 2013 et un autre nom pour les clients qui rejoint en 2014. Permet de ce schéma d’appellation pour augmenter par programme les noms selon la date (comme approches 2014, l’instance du service de 2014 peut être créé à la demande).

Toutefois, cette approche est basée sur les clients qui utilisent les informations d’affectation de noms spécifiques à l’application qui sont trouve en dehors de l’étendue des connaissances tissu de Service.

- *En utilisant une convention d’appellation*: dans 2013, lorsque votre application passe live, vous créez un service appelé tissu : / application/service2013. Près du deuxième trimestre 2013, vous créez un autre service, appelé tissu : / application/service2014. Ces deux services sont du même type de service. Dans cette approche, votre client devra utiliser une logique à créer le nom de service appropriés en fonction de l’année.

- *À l’aide d’un service de recherche*: un autre modèle consiste à fournir un service de recherche secondaire, qui peut fournir le nom du service pour une clé de votre choix. Nouvelle instance de service peut ensuite être créé par le service de recherche. Le service de recherche lui-même ne conserve pas les données d’application, seules les données relatives aux noms de service qu’il crée. Par conséquent, par exemple basée sur une année ci-dessus, le client serait tout d’abord contacter le service de recherche pour trouver le nom du service de gestion des données pour une année donnée et ensuite utiliser ce nom de service pour effectuer l’opération réelle. Le résultat de la première recherche peut être mis en cache.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les concepts de tissu de Service, voir les rubriques suivantes :

- [Disponibilité des services de structure de Service](service-fabric-availability-services.md)

- [Partition des services de structure de Service](service-fabric-concepts-partitioning.md)

- [Création et gestion de l’état](service-fabric-concepts-state.md)
