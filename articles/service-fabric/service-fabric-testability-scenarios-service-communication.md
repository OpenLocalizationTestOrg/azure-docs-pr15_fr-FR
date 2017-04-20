<properties
   pageTitle="Test : Communication de Service | Microsoft Azure"
   description="Communication de service à service est un point d’intégration importants d’une application de Service tissu. Cet article décrit les éléments de conception et les techniques de test."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/06/2016"
   ms.author="vturecek"/>

# <a name="service-fabric-testability-scenarios-service-communication"></a>Scénarios de test tissu de service : communication de Service

Surface d’orientée services architecturale styles naturellement dans Azure Service tissu et Microservices. Dans ces types d’architectures distribuées, applications basées sur des composants microservice sont généralement composées de plusieurs services que vous avez besoin pour communiquer avec eux. Dans la même le meilleur des cas, vous devez généralement au moins un service web sans état et un service de stockage de données avec état qui doivent communiquer.

Communication de service à service est un point d’intégration importants d’une application, car chaque service expose une API à distance à d’autres services. Travailler avec un jeu de frontières API qui implique généralement e/s nécessite quelques précautions, avec une grande quantité de test et de validation.

De nombreux aspects sont à effectuer lorsque ces limites de service sont reliés dans un système distribué :

 - *Protocole de transport*. Allez-vous utiliser HTTP pour accroître l’interopérabilité ou un protocole binaire personnalisé pour le débit maximal ?
 - *Gestion des erreurs*. Mode de traitement des erreurs permanentes et transitoires ? Que se passe-t-il lorsqu’un service se déplace vers un autre nœud ?
 - *Délais d’expiration et la latence*. Dans les applications multicouches, comment chaque calque service gérera latence dans la pile et à l’utilisateur ?

Si vous utilisez une des composants de communication du service intégrés fournis par le Service tissu ou créer vos propres, les interactions entre vos services tests est essentiel pour garantir la résilience dans votre application.

## <a name="prepare-for-services-to-move"></a>Préparer pour les services à déplacer

Instances de service peuvent se déplacer dans le temps. Cela est particulièrement vrai lorsqu’ils sont configurés avec des indicateurs de chargement pour équilibrage de charge personnalisée optimale des ressources. Service TISSU déplace vos instances de service pour optimiser leur disponibilité même pendant les mises à niveau, basculement, horizontale et autres situations qui se produisent pendant la durée de vie d’un système distribué.

Comme services se déplacement dans le cluster, vos clients et autres services doivent être prêtes à gérer deux scénarios lorsqu’ils parler à un service :

- Le réplica instance ou partition service a été déplacé depuis le dernier que vous parlé à celui-ci. Il s’agit d’une partie intégrante du cycle de vie service, et il doit se produire pendant la durée de vie de votre application.
- Le réplica instance ou partition service est en déplacement. Bien que le basculement d’un service à partir d’un nœud vers un autre se produit très rapidement dans tissu de Service, il peut être un délai de disponibilité si le composant de communication de votre service est lent à démarrer.

La gestion de ces scénarios normalement est important pour un système de durée d’exécution lisse. Pour ce faire, gardez à l’esprit que :

- Chaque service pouvant être connectés à possède une *adresse* lesquels elle écoute (par exemple, HTTP ou WebSocket). Lorsqu’une instance de service ou d’une partition déplacée, son point de terminaison adresse change. (Il déplace vers un autre nœud avec différentes adresses IP). Si vous utilisez les composants de communication intégrés, ils gérera ré-résolution des adresses de service pour vous.
- Il peut y avoir une augmentation temporaire de latence du service en tant que le service instance démarre la son récepteur à nouveau. Cela dépend de la rapidité avec laquelle le service s’ouvre que l’auditeur après le déplacement de l’instance du service.
- Toutes les connexions existantes doivent être fermé et rouvert une fois que le service s’ouvre sur un nouveau nœud. Un arrêt de nœud ordonné ou redémarrage permet de temps pour les connexions existantes arrêter normalement.

### <a name="test-it-move-service-instances"></a>Tester : déplacer des instances de service

Grâce aux outils de test de tissu de Service, vous pouvez créer un scénario de test pour tester ces situations de différentes manières :

1. Déplacer réplica principal d’un service dynamique.

    Réplica principal d’une partition service dynamique peut être déplacé pour différentes raisons. Utilisez cette option pour cibler le réplica principal d’une partition spécifique pour afficher la façon dont vos services réagissent pour le déplacement de façon très contrôlée.

    ```powershell

    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService

    ```

2. Arrêter un nœud.

    Lorsqu’un nœud est arrêté, Service TISSU déplace toutes les instances de service ou partitions qui ont été sur ce nœud à un des autres nœuds disponibles dans le cluster. Ceci permet de tester une situation où un nœud est perdu à partir de votre cluster et toutes les instances de service et réplicas sur ce nœud ont à déplacer.

    Vous pouvez arrêter un nœud à l’aide de l’applet de commande PowerShell **Stop ServiceFabricNode** :

    ```powershell

    PS > Restart-ServiceFabricNode -NodeName Node_1

    ```

## <a name="maintain-service-availability"></a>Maintenir la disponibilité du service

En tant que plate-forme, tissu de Service est conçu pour augmenter la disponibilité de vos services. Mais dans certains cas, des problèmes d’infrastructure sous-jacente peuvent provoquer une indisponibilité. Il est important de tester ces scénarios, trop.

Services dynamique utilisent un système quorum pour répliquer état de disponibilité. Cela signifie qu’un quorum des réplicas doit être disponibles pour effectuer des opérations d’écriture. Rarement, par exemple une défaillance matérielle étendue, un quorum des réplicas ne soient pas disponible. Dans ce cas, vous ne serez pas en mesure d’effectuer les opérations d’écriture, mais vous serez toujours en mesure d’effectuer les opérations de lecture.

### <a name="test-it-write-operation-unavailability"></a>Tester : écrire indisponibilité opération

En utilisant les outils de test de tissu de Service, vous pouvez ajouter une défaillance qui entraîne la perte de quorum en guise de test. Si tel est le cas est rare, il est important que les clients et les services qui dépendent d’un service dynamique êtes prêt pour gérer des situations où ils ne peuvent pas apporter écrire demandes. Il est également important que le service dynamique elle-même est informé de cette possibilité et il communiquer normalement aux appelants.

Vous pouvez entraîner la perte de quorum à l’aide de l’applet de commande PowerShell **Appeler ServiceFabricPartitionQuorumLoss** :

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

Dans cet exemple, nous avons définie `QuorumLossMode` à `QuorumReplicas` pour indiquer que nous voulons entraîner la perte de quorum sans arrêter tous les réplicas. Ainsi, les opérations de lecture sont toujours possibles. Pour tester un scénario où toute une partition n’est pas disponible, vous pouvez définir ce commutateur `AllReplicas`.

## <a name="next-steps"></a>Étapes suivantes

[Pour plus d’informations sur les actions de test](service-fabric-testability-actions.md)

[Pour plus d’informations sur les scénarios de test](service-fabric-testability-scenarios.md)
