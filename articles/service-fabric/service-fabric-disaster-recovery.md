<properties
   pageTitle="Reprise Service tissu Azure | Microsoft Azure"
   description="TISSU Service Azure offre les fonctions nécessaires pour traiter tous les types d’incidents. Cet article décrit les types de sinistre qui peut se produire et comment traiter les."
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
   ms.date="08/10/2016"
   ms.author="seanmck"/>

# <a name="disaster-recovery-in-azure-service-fabric"></a>Récupération d’urgence dans Azure Service TISSU

Une partie essentielle de l’exécution d’une application en nuage de disponibilité consiste à s’assurer qu’il peut survie tous les différents types d’échecs, y compris ceux qui sont en dehors de votre contrôle. Cet article décrit la mise en page physique d’un cluster Azure Service tissu dans le contexte d’incidents potentiels et fournit des conseils sur la façon de traiter ces incidents pour limiter ou éliminer les risques en matière de temps d’arrêt ou perte de données.

## <a name="physical-layout-of-service-fabric-clusters-in-azure"></a>Agencement de Service TISSU clusters dans Azure

Pour mieux comprendre le risque posé par différents types d’échecs, il est utile de savoir comment clusters sont physiquement disposés dans Azure.

Lorsque vous créez un cluster de Service tissu dans Azure, vous devez choisir une région dans lequel il sera hébergé. L’infrastructure Azure configure ensuite les ressources pour ce cluster dans la région, notamment le nombre de machines virtuelles (machines virtuelles) demandé. Nous allons étudier plus en détail comment et où ces machines virtuelles sont mis en service.

### <a name="fault-domains"></a>Domaines d’erreur

Par défaut, les ordinateurs virtuels du cluster sont réparties uniformément au sein de groupes logiques appelés domaines d’erreur (FDs), qui segment les ordinateurs en fonction des défaillances potentielles au niveau du matériel hôte. Plus précisément, si deux machines virtuelles résident dans deux FDs distincts, vous pouvez être sûr qu’ils ne pas partagent la même transition de power source ou réseau. Par conséquent, un réseau local ou coupure de courant affectant une machine virtuelle n’affecte pas l’autre, ce qui permet de Service TISSU rééquilibrer la charge de travail de l’ordinateur ne répond pas au sein du cluster.

Vous pouvez visualiser la mise en page de votre cluster domaines défaillance à l’aide de la carte cluster fournie dans [l’Explorateur de tissu de Service](service-fabric-visualizing-your-cluster.md):

![Nœuds répartir les domaines d’erreur dans l’Explorateur de tissu de Service][sfx-cluster-map]

>[AZURE.NOTE] L’autre axe dans la carte de cluster affiche domaines mise à niveau, qui regroupent les nœuds basés sur des activités de maintenance planifiée. Groupes de service tissu dans Azure sont toujours disposés sur cinq domaines mise à niveau.

### <a name="geographic-distribution"></a>Distribution géographique

Il existe actuellement [26 Azure régions du monde][azure-regions], avec plusieurs autres annoncé. Une région peut contenir un ou plusieurs centres de données physiques en fonction de la demande et la disponibilité des emplacements appropriés, entre autres facteurs. Notez que même dans régions qui contiennent plusieurs centres de données physiques, il n’existe aucune garantie que machines virtuelles de votre cluster seront réparties uniformément sur ces emplacements physiques. En effet, pour l’instant, tous les ordinateurs virtuels pour un cluster donné sont sa mise en service au sein d’un seul site physique.

## <a name="dealing-with-failures"></a>En matière de gestion des échecs

Il existe plusieurs types d’échecs qui peuvent avoir un impact sur votre cluster, chacune avec sa propre atténuation. Nous allons examiner les dans l’ordre de probabilité se produise.

### <a name="individual-machine-failures"></a>Échecs d’ordinateur individuels

Comme indiqué, échecs d’ordinateur individuels, au sein de la machine virtuelle ou dans le matériel ou le logiciel d’hébergement au sein d’un domaine d’erreur, ne poser aucun problème tout seul ?. TISSU de service sont généralement détecter l’échec quelques secondes et y répondre en conséquence basée sur l’état du cluster. Par exemple, si le nœud a été hébergeant les réplicas principales pour une partition, une nouvelle primaire est choisi à partir de duplications secondaire la partition. Lorsque Azure donne l’ordinateur a échoué sauvegarder, il sera rejoindre le cluster automatiquement et prenez une nouvelle fois sur sa part de la charge de travail.

### <a name="multiple-concurrent-machine-failures"></a>Plusieurs échecs d’ordinateur simultanées

Tandis que les domaines d’erreur réduisant les risques d’échec de l’ordinateur simultanées, il est toujours possible pour plusieurs échecs aléatoires interrompre simultanément plusieurs ordinateurs dans un cluster.

En règle générale, dans la mesure où la majorité des nœuds restent disponibles, le cluster continueront à fonctionner, même à capacité inférieure comme réplicas dynamique obtenir compressées dans un plus petit ensemble d’ordinateurs et moins d’instances sans état sont disponibles pour répartir la charge.

#### <a name="quorum-loss"></a>Perte de quorum

Si la majorité réplica de partition d’un service dynamique aller vers le bas, cette partition entre dans un état appelé « perte quorum ». À ce stade, Service TISSU arrête autorisant écritures sur cette partition pour vous assurer que son état reste cohérente et fiable. En effet, nous sommes en choisissant accepter une période d’indisponibilité pour vous assurer que les clients ne sont pas dit que leurs données a été enregistrées alors qu’il n’est pas. Notez que si vous avez choisi de participer au autorisant lectures à partir de réplica secondaire pour ce service avec état, vous pouvez continuer à effectuer les opérations dans cet état de lecture. Une partition est conservé dans perte quorum jusqu'à ce qu’un certain nombre de réplicas y revenir ou jusqu'à ce que l’administrateur de cluster force le système à passer à l’aide de l' [API de réparation ServiceFabricPartition][repair-partition-ps].

>[AZURE.WARNING] Exécution d’une action de réparation alors que le réplica principal est vers le bas entraîne la perte de données.

Services système peuvent également se dégrader perte quorum, avec l’impact étant spécifiques au service en question. Par exemple, perte quorum dans le service d’appellation aura un impact sur résolution de noms, alors que la perte de quorum dans le service de gestionnaire de basculement bloquera basculement et création d’un nouveau service. Notez que contrairement à vos propres services, tentative de réparation services système n’est *pas* recommandé. Au lieu de cela, il est préférable d’attendre que renvoyer les réplicas vers le bas.

#### <a name="minimizing-the-risk-of-quorum-loss"></a>Limiter le risque de perte de quorum

Vous pouvez réduire les risques de perte de quorum en augmentant la taille du jeu de réplica cible pour votre service. Il est utile de considérer le nombre de réplicas il vous faut en ce qui concerne le nombre de nœuds indisponibles, que vous pouvez tolérer en une fois que tout en restant disponibles pour les écritures, en gardant à l’esprit cette application ou mises à niveau cluster possibles nœuds temporairement indisponible, en plus des défaillances matérielles.

Considérez les exemples suivants en supposant que vous avez configuré vos services pour avoir un MinReplicaSetSize de trois, le plus petit nombre recommandé pour les services de production. Avec un TargetReplicaSetSize de trois (un serveur principal et deux secondaires), une défaillance matérielle au cours d’une mise à niveau (deux réplicas vers le bas) entraînera quorum perte et votre service deviendra en lecture seule. Par ailleurs, si vous avez cinq réplicas, vous ne pourrez pas résistance deux défaillances pendant la mise à niveau (trois répliques vers le bas), comme les deux réplicas restants peuvent toujours former un quorum au sein du jeu de réplica minimale.

### <a name="data-center-outages-or-destruction"></a>Défaillances de centre de données ou de destruction

Rarement, centres de données physiques peuvent devenir temporairement indisponibles en raison de perte de la connectivité réseau ou d’alimentation. Dans ce cas, vos clusters tissu de Service et les applications de la même manière ne seront pas disponibles, mais vos données seront conservées. Pour les clusters en cours d’exécution dans Azure, vous pouvez afficher les mises à jour dans défaillances dans la [page État Azure][azure-status-dashboard].

Dans l’événement très peu probable qu’un centre de données physique destruction, aucun cluster Service TISSU hébergé il seront perdues, ainsi que leur état.

Pour vous protéger contre cette possibilité, il est très important de régulièrement [sauvegarder votre état](service-fabric-reliable-services-backup-restore.md) à un magasin geo redondants et vérifiez que vous avez validé la possibilité de restaurer. La fréquence à laquelle vous effectuez une sauvegarde sera-t-il lié à votre récupération objectifs de point (). Même si vous n’avez pas entièrement implémenté de sauvegarde et restauration encore, vous devez implémenter un gestionnaire pour la `OnDataLoss` événement afin que vous pouvez vous connecter lorsqu’il se produit comme suit :

```c#
protected virtual Task<bool> OnDataLoss(CancellationToken cancellationToken)
{
  ServiceEventSource.Current.ServiceMessage(this, "OnDataLoss event received.");
  return Task.FromResult(true);
}
```


### <a name="software-failures-and-other-sources-of-data-loss"></a>Échecs de logiciels et d’autres sources de perte de données

En raison de perte de données, les erreurs de code dans les services, les erreurs opérationnelles humaines et les violations de sécurité sont plus courantes que des échecs de centre de données étendu. Toutefois, dans tous les cas, la stratégie de récupération est le même : effectuez des sauvegardes régulières de tous les services avec état et exercer votre capacité à restaurer cet état.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment faire pour simuler différentes défaillances à l’aide de l' [infrastructure de test](service-fabric-testability-overview.md)
- Lire d’autres ressources reprise et disponibilité. Microsoft a publié une grande quantité de conseils sur ces sujets. Tandis que certains de ces documents font référence à des techniques spécifiques pour une utilisation dans d’autres produits, qu’ils contiennent plusieurs méthodes conseillées générales que vous pouvez appliquer dans le contexte de Service tissu ainsi :
 - [Liste de vérification de disponibilité](../best-practices-availability-checklist.md)
 - [Effectuer une exploration de récupération d’urgence](../sql-database/sql-database-disaster-recovery-drills.md)
 - [Reprise d’activité et disponibilité des applications Azure][dr-ha-guide]


<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
