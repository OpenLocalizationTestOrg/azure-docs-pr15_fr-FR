<properties
   pageTitle="L’utilisation des Services fiables avancée | Microsoft Azure"
   description="En savoir plus sur l’utilisation avancée des Services fiables du Service tissu pour plus de souplesse dans vos services."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="masnider"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="advanced-usage-of-the-reliable-services-programming-model"></a>L’utilisation des Services fiable modèle de programmation avancée
TISSU Service Azure simplifie l’écriture et la gestion des services avec et sans état fiables. Ce guide parler d’utilisations avancées de Services fiables pour obtenir plus de contrôle et de flexibilité sur vos services. Avant de lire ce guide, familiarisez-vous avec [Le modèle de programmation des Services fiables](service-fabric-reliable-services-introduction.md).

Les services avec ou sans état ont deux points d’entrée principal pour le code utilisateur :

 - `RunAsync`est un point d’entrée à usage général pour votre code de service.
 - `CreateServiceReplicaListeners`et `CreateServiceInstanceListeners` est d’ouverture écoute de communication pour les demandes des clients.
 
Ces points de deux entrée suffisent à la plupart des services. Dans certains cas rares lorsque davantage de contrôle sur le cycle de vie d’un service est requise, les événements de cycle de vie supplémentaires sont disponibles.

## <a name="stateless-service-instance-lifecycle"></a>Cycle de vie instance service sans état

Cycle de vie d’un service sans état est très simple. Un service sans état peut uniquement être ouvert, fermé ou abandonné. `RunAsync`dans un service sans état est exécuté lorsqu’une instance de service est ouvert et annulée lorsqu’une instance de service est fermée ou abandonnée. 

Bien que `RunAsync` doit être suffisamment dans presque tous les cas, ouvrir, fermer et événements abandon dans un service sans état sont également disponibles :

- `Task OnOpenAsync(IStatelessServicePartition, CancellationToken)`
  OnOpenAsync est appelé lorsque l’instance de service sans état est sur le point d’être utilisée. Tâches de l’initialisation de service étendu peuvent être démarrés pour le moment.

- `Task OnCloseAsync(CancellationToken)`
  OnCloseAsync est appelé lorsque l’instance de service sans état va être arrêter correctement. Cela peut se produire lors de la mise à niveau de code du service, l’instance du service est déplacé en raison de l’équilibrage de charge ou une défaillance transitoire est détectée. OnCloseAsync peut servir à fermer toutes les ressources, arrêter tout traitement d’arrière-plan, terminer l’enregistrement état externe ou fermer les connexions existantes en toute sécurité.

- `void OnAbort()`
  OnAbort est appelée lorsque l’instance de service sans état est forcer arrêté. Il s’agit généralement lorsqu’une défaillance permanente est détectée sur le nœud, ou lorsque tissu de Service ne peuvent pas gérer fiable du cycle de vie de l’instance du service en raison d’échecs internes.

## <a name="stateful-service-replica-lifecycle"></a>Cycle de vie réplica service dynamique

Cycle de vie d’un réplica service avec état est beaucoup plus complexe qu’une instance de service sans état. En outre pour ouvrir, fermer et abandonner événements, un réplica service avec état est soumis à modifications apportées aux rôles pendant sa durée de vie. Lorsqu’un réplica service dynamique modifie le rôle, le `OnChangeRoleAsync` est déclenché :

- `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)`
  OnChangeRoleAsync est appelé lorsque le réplica service dynamique change le rôle, par exemple pour principal ou secondaire. Réplicas primaires figurent état écriture (sont autorisés à créer et écrire dans Collections fiable). Réplicas secondaires figurent état de lecture (peut lire uniquement à partir des Collections fiable existant). La plupart du travail dans un service avec état est effectué au réplica principal. Réplicas secondaires peuvent effectuer validation en lecture seule, génération de rapports, l’exploration de données ou d’autres tâches en lecture seule.

Dans un service dynamique, réplica principal uniquement a accès en écriture à l’état et par conséquent est généralement que le service effectue travail réel. La `RunAsync` méthode dans un service avec état est exécutée uniquement lorsque le réplica service avec état est principal. La `RunAsync` méthode est annulé lorsque le rôle d’un réplica principal change en s’éloignant de moniteur principal, ainsi que pendant les événements Fermer et abandon. 

À l’aide de la `OnChangeRoleAsync` événement permet d’effectuer le travail en fonction de rôle réplica également exemple en réponse aux changements de rôle.

Un service dynamique fournit également les mêmes événements du cycle de vie quatre qu’un service sans état, avec la même sémantique et le cas d’utilisation :

- `Task OnOpenAsync(IStatefulServicePartition, CancellationToken)`
- `Task OnCloseAsync(CancellationToken)`
- `void OnAbort()`



## <a name="next-steps"></a>Étapes suivantes
Pour des rubriques plus avancées liées au Service tissu, consultez les articles suivants :

- [Configuration des Services fiable avec état](service-fabric-reliable-services-configuration.md)

- [Introduction au niveau de service TISSU](service-fabric-health-introduction.md)

- [À l’aide de rapports d’intégrité du système de résoudre les problèmes](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

- [Configuration des Services avec le Gestionnaire de ressources Cluster Service TISSU](service-fabric-cluster-resource-manager-configure-services.md)
