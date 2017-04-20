<properties
   pageTitle="Mettre à l’échelle un cluster tissu de Service ou l’arrière | Microsoft Azure"
   description="Mettre à l’échelle un cluster tissu de Service ou l’arrière pour faire correspondre à la demande en définissant des règles d’échelle automatique pour chaque ensemble nœud type/machine virtuelle échelle. Ajouter ou supprimer des nœuds à un cluster de tissu de Service"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/09/2016"
   ms.author="chackdan"/>


# <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules"></a>Mettre à l’échelle un cluster tissu de Service ou l’arrière à l’aide de règles échelle automatique

Machine virtuelle échelle jeux sont une ressource cluster Azure que vous pouvez utiliser pour déployer et gérer une collection de machines virtuelles comme un ensemble. Chaque type de nœud défini dans un cluster de tissu de Service a été configurée comme un ensemble d’échelle machine virtuelle distinct. Chaque type de nœud peut ensuite être mise à l’échelle dans ou déconnecter séparément, présentent différents jeux de ports ouverts et peut contenir des indicateurs de capacité différente. En savoir plus à ce sujet dans le document de [Service TISSU nodetypes](service-fabric-cluster-nodetypes.md) . Étant donné que la structure de Service types de nœuds dans votre cluster sont constitués d’échelle machine virtuelle définit sur le serveur principal, vous devez configurer des règles pour chaque ensemble d’échelle de type/machine virtuelle nœud échelle automatique.

>[AZURE.NOTE] Votre abonnement doit avoir suffisamment cœurs pour ajouter les nouvelles machines virtuelles qui composent ce cluster. Il n’existe aucune validation modèle actuellement, et vous offre une défaillance de temps de déploiement, si un des limites de quota de positionnement.

## <a name="choose-the-node-typevm-scale-set-to-scale"></a>Cliquez sur l’échelle de type/machine virtuelle nœud défini à l’échelle

Pour l’instant, vous n’êtes pas en mesure de spécifier les règles d’échelle automatique de jeux d’échelle machine virtuelle à l’aide du portail, donc laissez-nous utiliser PowerShell Azure (1.0 +) pour répertorier les types de nœuds et leur ajouter des règles d’échelle automatique.

Pour obtenir la liste du jeu d’échelle machine virtuelle qui composent votre cluster, exécutez les applets de commande suivantes :

```powershell
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzureRmVmss -ResourceGroupName <RGname> -VMScaleSetName <VM Scale Set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevm-scale-set"></a>Définir des règles d’échelle automatique pour l’ensemble des nœuds type/machine virtuelle échelle

Si votre cluster comporte plusieurs types de nœud, puis répétez que ceci pour chaque échelle types/machine virtuelle nœud définit que vous voulez mettre à l’échelle (ou l’arrière). Prendre en compte le nombre de nœuds que vous devez disposer avant de configurer mise à l’échelle. Le nombre minimal de nœuds dont vous devez disposer pour le type de nœud principal est piloté par le niveau de fiabilité que vous avez choisi. En savoir plus sur les [niveaux de fiabilité](service-fabric-cluster-capacity.md).

>[AZURE.NOTE]  Mise à l’échelle vers le bas le nœud principal tapez inférieure à la marque de nombre minimale le cluster instable ou avancer vers le bas. Cela peut entraîner la perte de données pour vos applications et pour les services système.

Actuellement la fonctionnalité échelle automatique n’est pas pilotée par les charges que vos applications peuvent signaler pour tissu de Service. Donc pour le moment l’échelle automatique pouvez-vous est purement pilotée par les performances compteurs qui sont émises par chacun de la machine virtuelle échelle instances ensemble.  

Suivez ces instructions [pour configurer échelle automatique pour chaque ensemble d’échelle machine virtuelle](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

>[AZURE.NOTE] Dans une échelle vers le bas scénario, à moins que votre type de nœud ayant un niveau de durabilité d’OR ou en argent vous devez appeler l' [applet de commande Supprimer ServiceFabricNodeState](https://msdn.microsoft.com/library/azure/mt125993.aspx) avec le nom de nœud approprié.

## <a name="manually-add-vms-to-a-node-typevm-scale-set"></a>Ajouter manuellement des machines virtuelles à un ensemble d’échelle de type/machine virtuelle nœud

Suivez les instructions/exemple dans la [Galerie de modèles de démarrage rapide](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) pour modifier le nombre de machines virtuelles dans chaque Nodetype. 

>[AZURE.NOTE] Ajout de machines virtuelles prend du temps, afin de ne prévoyez ne pas les ajouts à être instantanée. Pour planifier l’ajouter capacité bien dans le temps, afin de permettre plus de 10 minutes avant de la capacité de mémoire virtuelle est disponible pour les réplicas / instances à placer du service.

## <a name="manually-remove-vms-from-the-primary-node-typevm-scale-set"></a>Supprimer manuellement des machines virtuelles à partir du jeu d’échelle de type/machine virtuelle nœud principal

>[AZURE.NOTE] Les services de système de tissu de service s’exécutent dans le type de nœud principal dans votre cluster. Afin de ne doivent jamais arrêter ou réduire le nombre d’instances dans les types de nœud que l’échelle inférieure à quel niveau de fiabilité le justifie. Consultez [les détails sur les niveaux de fiabilité ici](service-fabric-cluster-capacity.md). 

Vous devez exécuter des étapes une machine virtuelle dans l’exemple suivant à la fois. Ainsi, pour les services système (et vos services avec état) arrêter normalement dans les nouveaux réplicas créés sur d’autres nœuds instance machine virtuelle à supprimer.

1. Exécuter [Désactiver ServiceFabricNode](https://msdn.microsoft.com/library/mt125852.aspx) avec l’intention « RemoveNode » pour désactiver le nœud que vous allez supprimer (l’instance la plus élevée dans ce type de nœud).

2. Exécutez [Get-ServiceFabricNode](https://msdn.microsoft.com/library/mt125856.aspx) pour vous assurer que le nœud a bien été déplacé sur désactivé. Dans le cas contraire, patientez jusqu'à ce que le nœud est désactivé. Vous ne pouvez pas lancer rapidement cette étape.

2. Suivez les instructions/exemple dans la [Galerie de modèles de démarrage rapide](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) pour modifier le nombre de machines virtuelles par une dans ce Nodetype. L’instance supprimé est l’instance de machine virtuelle plus élevée. 

3. Répétez les étapes 1 à 3 selon vos besoins, mais jamais réduire le nombre d’instances dans les types de nœud principal inférieure à ce que le niveau de fiabilité certifie l’échelle. Consultez [les détails sur les niveaux de fiabilité ici](service-fabric-cluster-capacity.md). 

## <a name="manually-remove-vms-from-the-non-primary-node-typevm-scale-set"></a>Supprimer manuellement des machines virtuelles à partir du jeu d’échelle de type/machine virtuelle nœud principal

>[AZURE.NOTE] Pour un service dynamique, vous avez besoin d’un certain nombre de nœuds pour être toujours au maintenir la disponibilité et conserver l’état de votre service. Au minimum, vous devez le nombre de nœuds égal à l’ensemble du réplica cible nombre du partition/service. 

Vous devez l’exécuter étapes une machine virtuelle dans l’exemple suivant à la fois. Ainsi, pour les services système (et vos services avec état) arrêter normalement dans l’instance de machine virtuelle à supprimer et nouveaux réplicas créée où dois-je.

1. Exécuter [Désactiver ServiceFabricNode](https://msdn.microsoft.com/library/mt125852.aspx) avec l’intention « RemoveNode » pour désactiver le nœud que vous allez supprimer (l’instance la plus élevée dans ce type de nœud).

2. Exécutez [Get-ServiceFabricNode](https://msdn.microsoft.com/library/mt125856.aspx) pour vous assurer que le nœud a bien été déplacé sur désactivé. Si ce n’est pas le cas, attendez que le nœud est désactivé. Vous ne pouvez pas lancer rapidement cette étape.

2. Suivez les instructions/exemple dans la [Galerie de modèles de démarrage rapide](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) pour modifier le nombre de machines virtuelles par une dans ce Nodetype. Cela va supprimer l’instance de machine virtuelle plus élevée. 

3. Répétez les étapes 1 à 3 selon vos besoins, mais jamais réduire le nombre d’instances dans les types de nœud principal inférieure à ce que le niveau de fiabilité certifie l’échelle. Consultez [les détails sur les niveaux de fiabilité ici](service-fabric-cluster-capacity.md).

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Comportements que vous pouvez observer dans l’Explorateur de tissu de Service

Lorsque vous mettez l’échelle d’un cluster de l’Explorateur de tissu Service refléteront le nombre de nœuds (échelle de machine virtuelle définie instances) qui font partie du cluster.  Toutefois, lorsque vous redimensionnez un cluster, vous verrez l’instance supprimée nœud/machine virtuelle affichée dans un état incorrect, sauf si vous appelez [cmd supprimer ServiceFabricNodeState](https://msdn.microsoft.com/library/mt125993.aspx) avec le nom de nœud approprié.   

Voici l’explication de ce comportement.

Les nœuds répertoriés dans l’Explorateur de tissu de Service sont une réflexion des quels services système tissu de Service (FM spécifiquement) connaît le nombre de nœuds cluster avait/a. Lorsque vous redimensionnez l’échelle machine virtuelle vers le bas, la machine virtuelle a été supprimée mais service système FM pense toujours que le nœud (qui a été mappé à la machine virtuelle qui a été supprimée) est transférés précédent. Donc Service TISSU Explorer continue à afficher ce nœud (même si l’état d’intégrité peut être erreur ou inconnu).

Afin de vous assurer qu’un nœud est supprimé lorsqu’une machine virtuelle est supprimée, vous avez deux possibilités :

1) Choisissez un niveau de durabilité d’or ou en argent (disponible bientôt) pour les types de nœuds dans votre cluster, ce qui vous donne l’intégration de l’infrastructure. Qui puis supprime automatiquement les nœuds à partir de notre état du système services (FM) lorsque vous redimensionnez vers le bas.
Consultez [les détails sur les niveaux de durabilité ici](service-fabric-cluster-capacity.md)

2) Une fois que l’instance machine virtuelle a été mise à l’échelle vers le bas, vous devez appeler l' [applet de commande Supprimer ServiceFabricNodeState](https://msdn.microsoft.com/library/mt125993.aspx).

>[AZURE.NOTE] Service TISSU clusters nécessitent un certain nombre de nœuds à être opérationnel à tout moment afin de maintenir la disponibilité et conserver l’état - appelée « maintenance quorum ». Dès lors, il est généralement dangereux pour arrêter tous les ordinateurs du cluster, sauf si vous avez effectué tout d’abord une [sauvegarde de votre état](service-fabric-reliable-services-backup-restore.md).

## <a name="next-steps"></a>Étapes suivantes
Lire les informations suivantes pour également en savoir plus sur la planification de la capacité de cluster et la mise à niveau un cluster partition services :

- [Planifier la capacité de votre cluster](service-fabric-cluster-capacity.md)
- [Mises à niveau cluster](service-fabric-cluster-upgrade.md)
- [Services avec état partition pour échelle maximale](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png
