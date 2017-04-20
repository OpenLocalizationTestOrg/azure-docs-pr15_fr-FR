<properties
   pageTitle="Votre Cluster au Azure TISSU Cluster ressource Gestionnaire de services d’équilibrage | Microsoft Azure"
   description="Introduction à l’équilibrage de votre cluster avec le Gestionnaire de ressources Cluster Service tissu."
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

# <a name="balancing-your-service-fabric-cluster"></a>Votre cluster tissu de service d’équilibrage
Le Gestionnaire de ressource Service TISSU Cluster permet de reporting charge dynamique, réagir aux modifications dans le cluster, correction violations de contrainte et rééquilibrer le cluster si nécessaire. Mais à quelle fréquence effectue-t-elle les éléments suivants, et qu’il déclenche ? Il existe plusieurs contrôles liés à celui-ci.

Le premier ensemble de contrôles autour d’équilibrage de charge sont un ensemble de compteurs. Ces échéances régissent la fréquence à laquelle le Gestionnaire de ressources Cluster examine l’état du cluster pour les tâches qui doivent être résolus. Il existe trois différentes catégories de travail, chacune avec leur propre minuteur correspondant. Ils sont :

1.  Positionnement – cette étape traite placez les réplicas avec état ou sans état instances qui manquent. Il traite de nouveaux services et la gestion des réplicas avec état ou sans état instances qui n’ont pas pu et devront être recréés. La suppression et la suppression de réplicas ou instances sont également géré ici.
2.  Contrainte vérifie – cette étape vérifie et corrige violations des contraintes positionnement différents (règles) au sein du système. Exemples de règles sont des éléments tels que de s’assurer que les nœuds ne sont pas dépassé capacité et que les contraintes de placement d’un service (plus d’informations sur ces versions ultérieures) sont remplies.
3.  Équilibrage de charge – cette étape vérifie si rééquilibrage proactive est nécessaire selon le niveau de balance pour différents indicateurs souhaité configuré, et dans l’affirmative tente de trouver une disposition dans le cluster est plus équilibré.

## <a name="configuring-cluster-resource-manager-steps-and-timers"></a>Configurer le Gestionnaire de ressources Cluster étapes et échéances
Chacun de ces types différents des corrections le Gestionnaire de ressources Cluster possibles est contrôlée par un autre minuteur qui régit sa fréquence. Par exemple, si vous souhaitez uniquement traiter en plaçant les nouvelles charges de travail de service dans le cluster chaque heure (pour les une sélection vers le haut), mais que vous souhaitez équilibrage de charge régulière vérifie toutes les quelques secondes, vous pouvez configurer ce comportement. Lorsque chaque minuteur se déclenche, la tâche est planifiée. Par défaut le Gestionnaire de ressources analyse son état et applique les mises à jour (le traitement par lots toutes les modifications qui se sont produites depuis la dernière analyse, comme remarquer qu’un nœud se trouve vers le bas) chaque 1/10 de seconde, ensembles de la position et la contrainte consultez indicateurs toutes les secondes et l’équilibrage de marquer toutes les 5 secondes.

ClusterManifest.xml :

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

Aujourd'hui nous uniquement effectuez l’une des actions suivantes à la fois, dans un ordre séquentiel (c’est pourquoi nous faire référence à ces configurations en tant que « intervalles minimales »)). Il s’agit de telle sorte que, par exemple, nous avons déjà répondu à toutes les demandes en attente pour créer de nouveaux réplicas avant de continuer au cluster d’équilibrage. Comme vous pouvez le voir par les intervalles de temps par défaut spécifiés, nous pouvons analyser et vérifier la présence de tout ce que nous devons faire très fréquemment, ce qui signifie que l’ensemble des modifications que nous apportez à la fin de chaque étape est généralement plus petite : nous allons parcours pas des heures des modifications dans le cluster et essayez de corriger les tous en même temps, nous essayons de gérer les éléments plus ou moins au fur mais avec certaines traitement par lots lorsque plusieurs éléments se produisent lors de la à la fois. Cela permet la ressource tissu de Service manager très réactive aux événements qui se produisent dans le cluster.

Alors que la plupart de ces tâches sont simples (s’il y a violation de contrainte, de réparation, s’il existe services doit être créée, créez-les), le Gestionnaire de ressources Cluster doit également des informations supplémentaires pour déterminer si le cluster déséquilibré. Pour que nous avons deux autres éléments de configuration : *Seuils d’équilibrage de charge* et les *Seuils d’activité*.

## <a name="balancing-thresholds"></a>Équilibrage de seuils
Un seuil d’équilibrage de charge est le contrôle principal pour le déclenchement de rééquilibrage proactive (n’oubliez pas que le minuteur est uniquement pour la fréquence à laquelle le Gestionnaire de ressources Cluster doit vérifier - il ne signifie que tout se produit). Le seuil d’équilibrage de charge définit comment déséquilibré le cluster doit être pour une mesure spécifique dans le Gestionnaire de ressources Cluster à prendre en considération il déséquilibré et équilibrage de déclencheur.

Seuils d’équilibrage de charge sont définies sur une base par métrique dans le cadre de la définition du cluster. Pour plus d’informations sur les indicateurs d’extraction [cet article](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

``` xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

Le seuil équilibrage de charge d’une métrique est un ratio. Si le montant de la charge sur le nœud plus chargé divisé par le montant de la charge sur le nœud moins chargé dépasse ce nombre, puis le cluster est considéré comme déséquilibré et équilibrage de charge sera déclenchée la prochaine fois que le Gestionnaire de ressources Cluster vérifie (par défaut, jamais 5 secondes, sous la forme régis par MinLoadBalancingInterval, ci-dessus).

![Exemple de seuil équilibrage de charge][Image1]

Dans cet exemple simple chaque service consomme une unité de certains métrique. Dans l’exemple supérieure, la charge maximale sur un nœud est 5 et la valeur minimale est 2. Supposons que le seuil d’équilibrage de charge pour cette mesure est 3. Par conséquent, le cluster est considéré comme dans l’exemple supérieure, équilibrée et aucun équilibrage ne sera déclenchée lorsque le Gestionnaire de ressources Cluster vérifie (dans la mesure où le rapport dans le cluster est 5/2 = 2,5 et qui est inférieure au seuil d’équilibrage de charge spécifié de 3).

Dans l’exemple bas, la charge max sur un nœud est 10, tandis que la valeur minimale est 2, résultant dans un rapport de 5. Cette opération place le cluster dépassé le seuil d’équilibrage de charge conçu de 3 pour cette métrique. Par conséquent, une exécution rebalancing globale sera planifiée prochaine fois que le minuteur équilibrage de charge se déclenche. Notez que le fait que coup d’une recherche d’équilibrage de charge ne signifie rien se déplace - parfois le cluster est déséquilibré mais la situation ne peut pas être améliorée - mais dans une situation comme celle-ci (au moins par défaut) certaines la charge sera certainement distribuée Node3. Notez qu’étant donné que nous n’utilisons pas une approche calcul des ressources nécessaires certaines charge pourrait également être distribuée à nœud 2 dans la mesure où qui entraînerait minimisation des différences entre les nœuds globales, mais nous attendez que la majorité de la charge serait flux à Node3.

![Équilibrage de charge seuil exemple Actions][Image2]

Notez que la prise en dessous le seuil d’équilibrage de charge n’est pas un objectif explicit – seuils d’équilibrage de charge sont simplement un *déclencheur* qui indique le Gestionnaire de ressources Cluster il doit ressembler au cluster afin de déterminer quelles améliorations possibles, le cas échéant.

## <a name="activity-thresholds"></a>Seuils d’activité
Parfois, bien que les nœuds sont relativement déséquilibrés, le montant *total* de la charge du cluster est faible. Cela peut être, en raison de l’heure du jour, ou car le cluster est les nouveautés et mise en route simplement amorcé. Dans les deux cas, vous souhaiterez pas le cluster d’équilibrage, car il existe réellement très peu à être acquise – vous allez simplement passer ressources réseau et de calcul pour déplacer des éléments, sans modifier accidentellement absolue de la différence de temps. Étant donné que nous voulons éviter cela, il existe un autre contrôle dans le Gestionnaire de ressources, appelé seuils d’activité, qui vous permet de spécifier certaines absolue la limite inférieure d’activité – si aucun nœud ne comporte au moins cette quantité de charge puis équilibrage de charge ne sera pas déclenchée même si le seuil d’équilibrage de charge est remplie.

Supposons que nous avons des rapports sur ces nœuds avec les totaux suivants pour la consommation par exemple. Supposons également que nous conserver notre seuil équilibrage de 3 pour cette mesure, mais également maintenant que nous avons un seuil d’activité 1 536. Dans le premier cas, alors que le cluster est déséquilibré par le seuil d’équilibrage de charge aucun nœud répond à ce seuil activité minimale, afin que nous ne touchez choses. Dans l’exemple bas, nœud 1 consiste dépassé le seuil d’activité, afin d’équilibrage de charge sera exécutée (étant donné que le seuil d’équilibrage de charge et le seuil d’activité pour la mesure sont dépassé)

![Exemple de seuil d’activité][Image3]

À l’instar des seuils d’équilibrage de charge, seuils d’activité sont définies par métrique via la définition du cluster :

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

Notez que les seuils d’équilibrage de charge et activité sont tous deux liés à la métrique - équilibrage de charge sont déclenchés en cas de dépassement des seuils d’équilibrage de charge et des activités pour la même mesure. Par conséquent, si nous dépassent le seuil d’équilibrage de charge pour la mémoire et le seuil d’activité de l’UC, l’équilibrage pas déclenche dans la mesure où les seuils restants (seuil d’équilibrage de charge de l’UC) et seuil d’activité pour la mémoire ne sont pas dépassés.

## <a name="balancing-services-together"></a>Équilibrage de services ensemble
Ce qui est intéressant de noter est que si le cluster est déséquilibré ou non est une décision cluster à l’échelle, mais la façon dont nous allez résolution elle se déplace doubles de chaque service et instances autour. Cela est pertinent pour, droite ? Si la quantité de mémoire est empilée sur un nœud, plusieurs réplicas ou instances peuvent contribuer à celui-ci, il pourrait exigent déplacement de réplicas avec état ou sans état instances qui utilisent la métrique concernée, déséquilibrée.

Il peut arriver que si un client doit appeler nous vers le haut ou le fichier un tickets indiquant qu’un service qui n’a pas été déséquilibré a été déplacée. Comment il survienne qu’un service est déplacé même si tous les indicateurs de ce service ont été équilibrées, même parfaitement c’est le cas, au moment de l’autre déséquilibre ? Voyons !

Prenons l’exemple quatre services, Service1, Service2, Service3 et Service4. Service1 rapports contre les mesures Metric1 et Metric2, Service2 contre Metric2 et Mmetric3, Service3 contre Metric3 et Metric4 et Service4 par rapport à certaines Metric99 métrique. Vous pouvez certainement voir où nous allons ici. Nous avons une chaîne ! Du point de vue du Gestionnaire de ressources Cluster, nous n’avons pas vraiment quatre services indépendants, nous disposons d’un ensemble de services qui sont liées (Service1, Service2 et Service3) et celui qui est désactivée dans sa propre.

![Équilibrage de Services ensemble][Image4]

Il est possible qu’une mauvaise répartition de Metric1 peut entraîner réplicas ou instances appartenant à Service3 à déplacer. En règle générale, ces mouvements sont assez limitées, mais peuvent être plus grandes selon exactement comment déséquilibré Metric1 vous utilisez et quelles modifications ont permis dans le cluster afin de corriger le problème. Nous pouvons également dire avec s’assurer qu’une mauvaise répartition de mesures 1, 2 ou 3 entraînera jamais mouvements dans Service4 – il ne serait aucun point depuis le déplacement des réplicas ou instances appartenant à Service4 autour can ne font absolument rien pour avoir un impact sur le solde des indicateurs 1, 2 ou 3.

Le Gestionnaire de ressources Cluster effectue automatiquement à quels services sont associées, dans la mesure où les services ont été ajoutées, supprimé, ou avaient la modification de leur configuration métrique – par exemple, entre deux séries de l’équilibrage de Service2 peuvent ont été reconfigurés pour supprimer Metric2. Ceci permet de rompre la chaîne entre Service1 et même2. Au lieu de deux groupes de services, vous avez maintenant trois :

![Équilibrage de Services ensemble][Image5]

## <a name="next-steps"></a>Étapes suivantes
- Métriques sont comment le Gestionnaire de ressources de Service TISSU Cluster gère la consommation et à la capacité dans le cluster. Pour en savoir plus sur les et comment les configurer consultez [cet article](service-fabric-cluster-resource-manager-metrics.md)
- Coût de mouvement est un moyen de signalisation au Gestionnaire de ressources Cluster que certains services sont plus complexes à accéder à d’autres personnes. Pour en savoir plus sur les coûts de déplacement, reportez-vous à [cet article](service-fabric-cluster-resource-manager-movement-cost.md)
- Le Gestionnaire de ressources Cluster contient plusieurs limitations que vous pouvez configurer pour ralentir évolution dans le cluster. Elles ne sont pas normalement nécessaires, mais si vous avez besoin vous pouvez en savoir plus sur les [ici](service-fabric-cluster-resource-manager-advanced-throttling.md)


[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png
