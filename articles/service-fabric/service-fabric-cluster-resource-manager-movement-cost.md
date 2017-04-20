<properties
   pageTitle="Gestionnaire de ressources Cluster Service TISSU : mouvement coût | Microsoft Azure"
   description="Vue d’ensemble du coût de mouvement pour les services de structure de Service"
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

# <a name="service-movement-cost-for-influencing-cluster-resource-manager-choices"></a>Coût de mouvement de service pour influencer le Gestionnaire de ressources Cluster choix
Un facteur important à prendre en considération lorsque vous essayez de déterminer les modifications à apporter à un cluster et le score d’une solution est le coût total d’atteindre cette solution.

Déplacement de service instances ou réplicas coûts processeur heure et la bande passante au minimum. Pour les services avec état, il coûts également la quantité d’espace disque dont vous avez besoin pour créer une copie de l’état avant d’éteindre anciens réplicas. Vous pouvez clairement réduire le coût de toute solution Gestionnaire de ressources Cluster Azure Service TISSU soulevée avec. Mais vous ne voulez également ignorer les solutions susceptibles d’améliorer considérablement la répartition des ressources dans le cluster.

Gestionnaire de ressources cluster comporte deux façons de coûts informatiques et en limitant les, même s’il tente de gérer le cluster en fonction de ses autres objectifs. La première est le Gestionnaire de ressources Cluster prévoit une nouvelle mise en page pour le cluster, compte chaque déplacement serait. Dans un cas simple, si vous obtenez deux solutions à l’aide de la même manière globale équilibrer (résultat) à la fin, puis prenez l’option la plus économique (nombre total de déplacements).

Cela fonctionne assez bien. Mais comme avec la valeur par défaut ou charges statiques, il est probablement pas dans n’importe quel système complexe que tous les déplacements sont égales. Certains sont susceptibles d’être beaucoup plus cher.

## <a name="changing-a-replicas-move-cost-and-factors-to-consider"></a>Modifier le coût de déplacement ou les facteurs à prendre en considération un réplica
Comme avec la création de rapports charge (une autre fonctionnalité du Gestionnaire de ressources Cluster), vous donner le service une pratique de reporting automatique comment coûteuses le service consiste à déplacer à un moment donné.

Code :

```csharp
this.ServicePartition.ReportMoveCost(MoveCost.Medium);
```

MoveCost comporte quatre niveaux : zéro, faible, moyenne et haute. Il s’agit par rapport aux autres, à l’exception de zéro. Zéro signifie que déplacez un réplica est gratuit et ne doit pas imputés le score de la solution. Définition de vos frais de déplacement élevé est *pas* une garantie que la réplique ne déplacez, juste qu’il n’est pas déplacé sauf s’il existe une bonne raison.

![Déplacer le coût comme un facteur dans la sélection réplica pour mouvement][Image1]

MoveCost vous permet de trouver les solutions qui provoque l’interruption minimum globale et sont plus faciles à atteindre tout en arrive toujours pas solde équivalent. Notion d’un service de coût peut être par rapport à nombreux éléments. Les facteurs les plus courants dans le calcul de vos frais de déplacement sont :

- La quantité d’état ou des données ayant pour déplacer le service.
- Coût de déconnexion des clients. Le coût de déplacer un réplica principal est généralement supérieur au coût de déplacer un réplica secondaire.
- Le coût d’interrompre une opération en cours d’exécution. Niveau de la banque quelques opérations sur les données ou opérations effectuées en réponse à un appel client sont coûteuses. Après un moment donné, vous ne voulez pas empêcher les si vous n’êtes pas obligé. Donc pendant la durée de l’opération, vous en relief le coût pour réduire la probabilité que la copie de service ou l’instance se déplace vers le haut. Une fois l’opération terminée, placez-le sur normal.

## <a name="next-steps"></a>Étapes suivantes
- Gestionnaire de ressources de service TISSU Cluster utilise des mesures pour gérer la consommation et à la capacité dans le cluster. Pour en savoir plus sur les indicateurs et comment les configurer, consultez [la consommation de gestion des ressources et de charge dans Service tissu avec indicateurs](service-fabric-cluster-resource-manager-metrics.md).
- Pour savoir comment le Gestionnaire de ressources Cluster gère et équilibre charge dans le cluster, consultez [votre cluster tissu de Service d’équilibrage](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png
