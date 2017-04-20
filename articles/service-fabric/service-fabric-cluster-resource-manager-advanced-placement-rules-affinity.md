<properties
   pageTitle="Responsable de ressources Cluster Service TISSU - affinité | Microsoft Azure"
   description="Présentation de la configuration utilisée pour tissu de Services"
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

# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Configuration et utilisation affinité service dans tissu de Service

Affinité est un contrôle qui est fourni principalement pour aider à faciliter la transition des applications monolithiques plus grandes dans le monde cloud et microservices. Ceci étant dit qu'il permet également dans certains cas, comme une optimisation légitime pour améliorer les performances des services, même si cela peut avoir des effets secondaires.

Supposons que vous importez une application plus grande ou qui n’a pas été simplement conçu avec microservices à l’esprit, pour tissu de Service. Cette transition est en réalité courantes, et nous avons plusieurs clients (internes et externes) dans cette situation. Vous commencez par soulevant l’ensemble de votre application dans l’environnement, il empaqueté prise et en cours d’exécution. Ensuite, vous commencez qu’elle décompose en différents services plus petites que tous les communiquer entre eux.

Il y a un « Oops... ». Le « Oops » se trouve généralement dans un de ces catégories :

1. Certains composant X de l’application monolithique n’a une dépendance non documentée sur composant Y et nous désactivé uniquement ceux en services distincts. Dans la mesure où ils sont en cours d’exécution sur différents nœuds dans le cluster, elles sont rompus.
2.  Les éléments suivants à communiquent par (local canaux nommé | mémoire partagée | fichiers sur disque), mais je dois être en mesure de mettre à jour indépendamment accélérer le processus un peu. Je vais supprimer la dépendance dure ultérieurement.
3.  Tout fonctionne correctement, mais il s’avère que ces deux composants sont très amenés/performances sensibles. Lorsqu’ils les déplacés en services distincts global performances de l’application tanked ou latence est augmenté. Par conséquent, l’application globale ne répond pas aux attentes.

Dans ce cas nous ne voulez pas perdre votre travail refactorisation et ne voulez pas que revenir au modèle monolithique, mais nous avons besoin d’une certaine façon de localisation. Il est conservé jusqu'à ce que nous puissions redéfinir les composants pour fonctionner naturellement en tant que services ou jusqu'à ce que nous pouvons résoudre aux attentes de performance un autre moyen, si possible.

Que faire ? Ainsi, vous pouvez essayer affinité sous tension.

## <a name="how-to-configure-affinity"></a>Comment configurer affinité
Pour définir une affinité, vous définissez une relation affinité entre deux services. Vous pouvez considérer comme « pointant » un service à un autre et indiquant « ce service peut uniquement s’exécuter où service est en cours d’exécution. » Parfois nous appelons affinité une relation parent/enfant (où vous pointez sur l’enfant sur le site parent). Affinité garantit que les réplicas ou les instances d’un service sont placés sur les nœuds du mêmes en tant que le réplicas ou les instances d’une autre.

``` csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

## <a name="different-affinity-options"></a>Options affinité différents
Affinité est représentée par le biais parmi plusieurs modèles de corrélation et comporte deux modes différents. Le mode plus courante d’affinité est ce que nous appelons NonAlignedAffinity. Dans NonAlignedAffinity les réplicas ou les instances dans lesquelles les différents services sont placés sur les nœuds du mêmes. L’autre mode est AlignedAffinity. Affinité alignée est utile uniquement avec les services avec état. Configuration des deux services avec état pour sont alignés affinité garantit que les couleurs primaires de ces services sont placés sur les nœuds de mêmes que les uns des autres. Il entraîne également chaque paire de secondaires pour être placés sur les nœuds du mêmes de ces services. Il est également possible (bien que moins courant) pour configurer NonAlignedAffinity pour les services avec état. Pour NonAlignedAffinity les réplicas différents des deux services avec état seraient être concernant les nœuds de mêmes, mais aucune tentative ne sont effectuée sur Aligner leurs primaires et secondaires.

![Modes d’affinité et leurs effets][Image1]

### <a name="best-effort-desired-state"></a>Meilleur état effort vous le souhaitez
Il existe quelques différences entre affinité et architectures monolithiques. Bon nombre d'entre eux sont une relation affinité étant mieux. Les services d’une relation affinité sont fondamentalement différentes entités qui peuvent échouer et être déplacées séparément. Il existe également causes pour pourquoi pu rompre une relation affinité. Par exemple, les limites de capacité where uniquement une partie du service des objets dans la relation affinité pouvant être affiché sur un nœud donné. Dans ce cas même s’il existe une relation affinité en place, il ne peut pas être appliquée en raison des autres contraintes. S’il est possible d’appliquer toutes les autres contraintes et affinité ultérieurement la violation de la contrainte affinité sera corrigée automatiquement.  

### <a name="chains-vs-stars"></a>Chaînes et étoiles
Aujourd'hui nous ne sont pas en mesure de chaînes de modèle de relations affinité. Ce que cela signifie qui est un service qui est l’enfant dans une relation affinité ne peut pas être un parent dans une autre relation affinité. Si vous voulez modéliser ce type de relation, vous devez efficace d’un modèle comme une étoile, plutôt qu’une chaîne. Pour ce faire, l’enfant plus bas serait être parents au parent de l’enfant « deuxième prénom » à la place. En fonction de l’organisation de vos services, cela peut nécessiter la création d’un service « espace réservé » à utiliser comme parent pour divers éléments enfants.

![Chaînes et étoiles dans le contexte des relations affinité][Image2]

Une autre chose à noter à propos de relations affinité aujourd'hui est qu’ils sont orientation. Cela signifie que la règle « affinité » applique seulement que l’enfant est où se trouve le parent. Si par exemple le parent soudainement bascule vers un autre nœud puis le Gestionnaire de ressources Cluster ne réellement pense qu’il existe un problème jusqu'à ce qu’il remarque que l’enfant ne trouve pas avec un parent ; la relation n’est pas appliquée immédiatement.

### <a name="partitioning-support"></a>Partition prise en charge
La dernière chose à noter à propos affinité est qu’affinité relations ne sont pas prises en charge dans laquelle le parent est répartie. Il s’agit de quelque chose que nous pouvons finalement prend en charge, mais aujourd'hui non autorisé.

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur les autres options disponibles pour la configuration des services d’extraction la rubrique sur les autres configurations Cluster le Gestionnaire de ressources disponibles [en savoir plus sur la configuration des Services](service-fabric-cluster-resource-manager-configure-services.md)
- Nombreuses raisons où les personnes utilisent affinité, par exemple pour limiter les services à un petit ensemble de machines et essayez d’agréger le chargement d’un ensemble de services, mieux prises en charge par le biais des groupes d’applications. Consultez [Les groupes d’Application](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png
