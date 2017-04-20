<properties
   pageTitle="Défragmentation de mesures dans Azure Service TISSU | Microsoft Azure"
   description="Une vue d’ensemble de l’utilisation de défragmentation ou de livraison comme une stratégie pour indicateurs dans tissu de Service"
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

# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Défragmentation de mesures et de chargement de tissu de Service
Le Gestionnaire de ressources de Service TISSU Cluster concerne principalement avec équilibrage de charge en termes de répartir la charge – s’assurer que tous les nœuds du cluster sont également utilisées. Il s’agit généralement de la mise en page sûre et smartest en termes de survie échecs car elle vérifie que tout échec donné ne prend pas le pourcentage de certaines grandes la charge de travail donnée. Le Gestionnaire de ressources de Service TISSU Cluster prend en charge une stratégie différente, qui est défragmentation. Défragmentation signifie généralement qu’au lieu d’essayer de distribuer le taux d’utilisation d’une métrique au sein du cluster, nous devons essayer de consolidation. Il s’agit d’une chance inversion de notre stratégie normal – au lieu d’optimisation du cluster en fonction de réduction de l’écart type moyen de charge métrique pour une métrique donnée, nous allons commencer optimisation pour les augmentations d’écart-type. Mais pourquoi devrais-je cette stratégie ?

De même, si vous avez aérer la charge de manière égale entre les nœuds du cluster vous avez consommés une partie des ressources qui possèdent des nœuds pour proposer. En règle générale, ce n’est pas un problème, mais parfois certaines charges de travail créent les services qui sont particulièrement importantes et utilisation de la plupart des nœud – Supposons que 75 % à 95 % de ressources d’un nœud retrouvez dédié à une instance de service unique ou réplica. Ce n’est pas un problème, le Gestionnaire de ressources Cluster détecte à l’heure de création de service dont il a besoin pour réorganiser le cluster afin de libérer de l’espace pour cette charge de travail importante et définissez sur ce qui se produire, mais en attendant cette charge de travail a peut-être patienter avant d’être planifiée dans le cluster.

Étant donné que la planification de nouvelles charges de travail est généralement au moins un peu latence sensible, si nous ne rien faire différemment nous peut parfois droite coup par ces SLA s’il existe un grand nombre de services et d’état pour vous déplacer, en particulier si les charges de travail dans le cluster sont généralement volumineux (et donc trop longue pour se déplacer dans le cluster). Bien, lorsque que nous avons mesurées heure de création de simulations basées sur des données réelles cluster, nous avons vu que si services ont été assez grandes et le cluster a été utilisé assez que la création de ces services volumineux est ralentie, et que nous pourrions améliorer cette situation en introduisant de la stratégie de mesures défragmentation.

Comme fichier Création ou accès pourrait obtenir ralentie que si un disque dur a été fragmenté et peut être accéléré en défragmentant le lecteur afin qu’il n’était plus grands blocs contigus disponible, vous pouvez configurer les mesures de défragmentation pour que le Gestionnaire de ressources Cluster pour essayer le fait de condenser la charge des services en moins de nœuds pour qu’il soit (presque) toujours salle pour les services plus importantes , leur permettant de créer rapidement. La plupart des personnes n’est pas nécessaire, car services doivent être généralement petites et, par conséquent, il n’est pas difficile à rechercher salle pour eux, mais si vous avez services volumineux et que vous avez besoin de créer rapidement (et sont prêts à accepter les autres compromis comme impactfulness accrue d’échecs et certaines ressources étant laissé inutilisé lorsqu’ils en attente de charges de travail et peut être planifiée), puis la stratégie défragmentation consiste à votre place.

L’illustration ci-dessous vous donne une représentation visuelle des deux clusters différents, celle qui est défragmenté et l’autre qui n’est pas. Dans le cas équilibré, envisagez les mouvements qui seraient nécessaires à la place un des objets de service plus grandes, si un ont été peuvent être créés, par rapport au cluster défragmenté, où elle peut être placé immédiatement sur nœuds 4 et 5.

![Comparaison d’équilibrée et défragmenté Clusters][Image1]

## <a name="defragmentation-pros-and-cons"></a>Avantages et inconvénients défragmentation
Quelles sont les autres compromis conceptuelles ? Nous vous recommandons de mesure complète de vos charges de travail avant d’activer les mesures de défragmentation. Voici une table rapide des éléments à considérer :

| Professionnels de le défragmentation  | Inconvénients défragmentation |
|----------------------|----------------------|
|Permet de créer plus rapide des services de grande taille | Chargement des concentrés sur moins de nœuds, augmentation de conflit
|Réduction déplacement de données lors de la création    | Échecs peuvent avoir un impact sur plusieurs services et entraîner l’évolution du plus
|Permet de description enrichie de conditions et la récupération d’espace | Configuration de gestion des ressources globale plus complexe

Vous pouvez combiner les mesures défragmentées et normales dans le même cluster et le Gestionnaire de ressources fera de son mieux pour vous assurer que vous obteniez une disposition qui regroupe plus grande partie des mesures de défragmentation comme il peut tandis que vous essayez de faire passer le reste. Les résultats exacts que vous obtiendrez varient selon le nombre d’équilibrage de charge métrique par rapport au nombre d’indicateurs de défragmentation et leur poids, charges actuelles, etc..

## <a name="configuring-defragmentation-metrics"></a>Configuration des indicateurs de défragmentation
Configuration des indicateurs de défragmentation est une décision globale dans le cluster et des indicateurs pouvant être sélectionnés pour la défragmentation :

ClusterManifest.xml :

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Disk" Value="true" />
    <Parameter Name="CPU" Value="false" />
</Section>
```

## <a name="next-steps"></a>Étapes suivantes
- Le Gestionnaire de ressources Cluster comporte beaucoup d’options pour décrire le cluster. Pour en savoir plus sur les consultez cet article sur [décrivant un cluster tissu de Service](service-fabric-cluster-resource-manager-cluster-description.md)
- Métriques sont comment le Gestionnaire de ressources de Service TISSU Cluster gère la consommation et à la capacité dans le cluster. Pour en savoir plus sur les et comment les configurer consultez [cet article](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png
