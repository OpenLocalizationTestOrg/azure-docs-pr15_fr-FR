<properties
   pageTitle="Responsable de ressources Cluster Service TISSU - stratégies de positionnement | Microsoft Azure"
   description="Vue d’ensemble de règles pour tissu de Services et des stratégies de placement supplémentaires"
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

# <a name="placement-policies-for-service-fabric-services"></a>Stratégies de placement pour les services de structure de service
Il existe différentes autres règles qui vous risquez de soins sur si votre cluster Service tissu est fractionné sur une distance géographiques, par exemple plusieurs centres de données ou régions Azure, ou si votre environnement s’étend sur plusieurs zones de contrôle géopolitique (ou d’autres cas dans lequel vous avez frontières juridique ou cette stratégie qui vous intéressent, ou la distance impliquées ont réel performances/latence impact). La plupart d'entre elles pu être configurée via les propriétés du nœud et les contraintes de placement, mais certains sont plus complexes. Pour rendre les choses plus simple, nous fournissons ces commmands supplémentaires. Comme avec d’autres contraintes de placement, les stratégies de placement peuvent être configurées sur base d’une instance nommée par le service.

## <a name="specifying-invalid-domains"></a>Spécification des domaines non valides
La stratégie de placement InvalidDomain permet de spécifier qu’un domaine particulier d’erreur n’est pas valide pour cette charge de travail. Cette stratégie garantit qu’un service particulier s’exécute jamais dans une zone spécifique, par exemple pour des raisons de stratégie géopolitiques ou d’entreprise. Plusieurs domaines non valides peuvent être spécifiés par le biais des stratégies distinctes.

![Exemple de domaine non valide][Image1]

Code :

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
invalidDomain.DomainName = "fd:/DCEast"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell :

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/DCEast”)
```
## <a name="specifying-required-domains"></a>Spécification des domaines requis
La stratégie de placement de domaine requis requiert que tous les réplicas avec état ou instances de service sans état pour le service dans le domaine spécifié. Plusieurs domaines requis peuvent être spécifiés par le biais des stratégies distinctes.

![Exemple de domaine requis][Image2]

Code :

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

PowerShell :

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## <a name="specifying-a-preferred-domain-for-the-primary-replicas"></a>Spécifier un domaine par défaut pour les réplicas principales
Le domaine principal par défaut est un contrôle intéressant, car elle permet de sélectionner le domaine d’erreur dans laquelle le serveur principal doit être placé s’il est possible pour le faire. Lorsque tout est correct primaire correspondra dans ce domaine. Si le domaine ou l’échec réplica principal ou être arrêté pour une raison quelconque le serveur principal sera déplacée vers un autre emplacement. Si cet emplacement n’est pas dans le domaine par défaut, puis lorsque cela est possible le Gestionnaire de ressources Cluster sera déplacez-le vers le domaine par défaut. Naturellement ce paramètre convient uniquement pour les services avec état. Cette stratégie est très utile dans les clusters qui sont fractionnés sur Azure régions ou plusieurs centres de données. Dans ces situations vous utilisez tous les emplacements pour redondance, mais préférez que les réplicas primaires soient placés dans un emplacement spécifique pour fournir une latence inférieure pour les opérations atteindre le serveur principal (écrit et également par défaut toutes les lectures sont pris en charge par le serveur principal).

![Basculement et domaines principaux par défaut][Image3]

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell :

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replicas-to-be-distributed-among-all-domains-and-disallowing-packing"></a>Pour demander une réplicas soit réparti entre tous les domaines et emballage refusant
Une autre stratégie que vous pouvez spécifier consiste à imposer réplicas toujours soit réparti entre les domaines d’erreur disponibles. Cela se produit par défaut dans la plupart des cas où le cluster est correct, cependant il existe dégénérées cas où réplicas pour une partition donnée peut amener temporairement placées dans une défaillance ou un domaine de mise à niveau. Par exemple, supposons que bien que le cluster ayant 9 nœuds dans 3 domaines d’erreur (0, 1 et 2) et votre service 3 réplicas, les nœuds qui ont été utilisés pour ces réplicas défaillance domaines 1 et 2 s’est arrêté ou en raison de problèmes de capacité aucun des autres nœuds dans ces domaines ont été valide. Si tissu de Service ont été pour générer remplacements pour ces réplicas, le Gestionnaire de ressources Cluster devra placez-les entre domaine d’erreur 0, mais qui crée une situation où de violation de la contrainte de domaine d’erreur. Il augmente également le risque réplica entier pouvant être perdue (si FD 0 doit être permananently perdu). (Pour plus d’informations sur les contraintes et contrainte priorités en règle générale, consultez [cette rubrique](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities) )

Si vous n’avez jamais lu un message d’avertissement d’intégrité comme « l’équilibrage de charge a détecté une Violation de contrainte pour ce réplica : tissu : /<some service name> Partition secondaire <some partition ID> est enfreint la contrainte : FaultDomain « vous avez atteint cette condition ou quelque chose de semblable. En règle générale, ces situations sont transitoires (les nœuds ne restent vers le bas le long, ou si c’est le cas, nous avons besoin de créer remplacements il sont a autres nœuds dans les domaines de défaillance correcte qui sont valides), mais il existe certaines charges de travail qui seraient échanges plutôt disponibilité du risque de perdre toutes leurs duplications. Pour cela, nous pouvons spécifiant la stratégie de « RequireDomainDistribution », ce qui permet de garantir qu’aucun deux réplica à partir de la même partition n’est jamais autorisés dans le même domaine défaillance ou mettre à niveau.

Certaines charges de travail c’est plutôt le nombre cible de duplications (copies d’état) à tout moment (Paris contre les échecs de domaine total et en sachant qu’ils peuvent récupérer généralement état local), alors que d’autres utilisateurs devez procéder plutôt le temps d’arrêt plus tôt que les problèmes de vérification et la perte des risques. Étant donné que la plupart des charges de travail exécuter avec plus de 3 réplicas, la valeur par défaut est de ne pas exiger de distribution de domaine et vous permettent d’équilibrage de charge et basculement gérer des cas en règle générale, même si cela signifie que temporairement un domaine a plusieurs réplicas compressés dedans.

Code :

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell :

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

À présent, il serait possible d’utiliser ces configurations pour les services dans un cluster qui n’était pas géographiquement fractionnés ? Vous pouvez bien sûr ! Mais il n’est pas une seule raison de trop, en particulier les configurations requises, non valide et préférée domaine doivent être évitées, sauf si vous exécutez en fait un cluster géographiquement fractionné - il ne fait pas appel de sens pour tenter de forcer une charge de travail à exécuter dans un seul rack ou à préférer certaines segment de votre cluster local plutôt qu’un autre sauf s’il existe différents types de segments de matériel ou de la charge de travail en , et ces cas peuvent être résolus via les contraintes de placement normal.

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur les autres options disponibles pour la configuration des services d’extraction la rubrique sur les autres configurations Cluster le Gestionnaire de ressources disponibles [en savoir plus sur la configuration des Services](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png
