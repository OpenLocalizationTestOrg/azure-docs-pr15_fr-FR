<properties
   pageTitle="La limitation dans le Gestionnaire de ressources de cluster Service TISSU | Microsoft Azure"
   description="Apprenez à configurer les limitations fournies par le Gestionnaire de ressources Cluster Service tissu."
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


# <a name="throttling-the-behavior-of-the-service-fabric-cluster-resource-manager"></a>La limitation le comportement du Gestionnaire de ressources Cluster Service TISSU
Même si vous avez correctement configuré le Gestionnaire de ressources Cluster, le cluster peut obtenir interrompu. Par exemple vous pouvez rencontrer sonnerie nœud ou défaillance échecs de domaine : que se passe-t-il si qui s’est produit pendant une mise à niveau ? Le Gestionnaire de ressources essaiera optimiser pour résoudre tous les éléments, mais les temps à ceci vous souhaiterez une protection afin que le cluster lui-même a une chance de placez son (les nœuds qui passent à venir en faire, les conditions réseau correction eux-mêmes, bits corrigés déployées). Pour vous aider avec ces types de situations, le Gestionnaire de ressources de Service TISSU Cluster inclut-il plusieurs limitations. Notez que ces limitations sont assez interruption de service et généralement ne doivent pas être utilisées, sauf si il a été certains prudent mathématiques terminé autour de la quantité de travail en parallèle réellement pouvant être effectuée le cluster, ainsi qu’un fréquents devez répondre à ces types de (est) événements reconfiguration macroscopiques non planifiée (UE : « Très mauvaises jours »).

En règle générale, il est recommandé pour éviter d’obtenir très mauvais jours par le biais d’autres options (par exemple, code normal mises à jour et éviter surplanification le cluster dans un premier temps) plutôt que la limitation de votre cluster pour empêcher celui-ci de l’utilisation des ressources lorsqu’il tente de résoudre lui-même). Limitations dois-je les valeurs par défaut que nous avons trouvé, grâce à être OK les valeurs par défaut, mais vous devez probablement un coup de œil et les régler votre charge réelle attendue. Tandis que pas trop contraindre ou le chargement que le cluster est une pratique recommandée, que vous pouvez déterminer qu’il existe des cas qui (jusqu'à ce que vous pouvez y remédier les) où vous voulez que quelques limitations en place, même si cela signifie que le cluster prendra plus de temps placez son.

##<a name="configuring-the-throttles"></a>Configuration des limitations
Limitations qui sont incluses par défaut sont les suivantes :

-   GlobalMovementThrottleThreshold – cela détermine le nombre total de mouvements dans le cluster certain temps (défini comme la GlobalMovementThrottleCountingInterval, la valeur en secondes)
-   MovementPerPartitionThrottleThreshold – cela détermine le nombre total de mouvements pour toutes les partitions service certain temps (la MovementPerPartitionThrottleCountingInterval, valeur en secondes)

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThreshold" Value="1000" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
     <Parameter Name="MovementPerPartitionThrottleThreshold" Value="50" />
     <Parameter Name="MovementPerPartitionThrottleCountingInterval" Value="600" />
</Section>
```

N’oubliez pas que la plupart du temps que nous l’avons vu clients utiliser ces limitations qu'il a été, car ils ont été déjà dans un environnement limité en ressources (telles que la bande passante réseau limitée dans les nœuds individuels ou des disques qui n’ont pas été jusqu'à la configuration requise du réplica parallèle crée qui ont été étant placé sur les) qui signifie que ces opérations ne réussite ou serait lente quand même.  Dans ces situations clients ont été à l’aise sachant que qu’ils ont été afin d’augmenter la durée pendant laquelle que le cluster d’atteindre un état stable, y compris sachant qu’ils peuvent finir en cours d’exécution en fiabilité inférieure pendant qu’ils ont été limitées faut-il.

## <a name="next-steps"></a>Étapes suivantes
- Pour savoir plus sur comment le Gestionnaire de ressources Cluster gère les soldes et chargement du cluster, consultez l’article sur [l’équilibrage de charge](service-fabric-cluster-resource-manager-balancing.md)
- Le Gestionnaire de ressources Cluster comporte beaucoup d’options pour décrire le cluster. Pour en savoir plus sur les consultez cet article sur [décrivant un cluster tissu de Service](service-fabric-cluster-resource-manager-cluster-description.md)
