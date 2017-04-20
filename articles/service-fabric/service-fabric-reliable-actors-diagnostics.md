<properties
   pageTitle="Diagnostics intervenants et surveillance | Microsoft Azure"
   description="Cet article décrit les diagnostics et fonctionnalités dans le runtime Service TISSU fiable intervenants, y compris les événements et les compteurs émis par celle-ci l’analyse des performances."
   services="service-fabric"
   documentationCenter=".net"
   authors="abhishekram"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/05/2016"
   ms.author="abhisram"/>

# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>Diagnostics et l’analyse des performances pour intervenants fiable
Le runtime intervenants fiable émet des [compteurs de performance](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx)et des événements de [source d’événement](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) . Fournir des informations sur le fonctionne de l’exécution et les aider à résoudre les problèmes et analyse des performances.

## <a name="eventsource-events"></a>Événements de la source d’événement
Le nom du fournisseur source d’événement pour le runtime intervenants fiable est « Microsoft-ServiceFabric-intervenants ». Événements appartenant à cette source d’événements apparaisse dans la fenêtre [Événements Diagnostics](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) lorsque l’application d’un acteur est en cours [de débogage dans Visual Studio](service-fabric-debugging-your-application.md).

Exemples d’outils et de technologies d’assistance qui vous aident dans collecte et/ou l’affichage des événements de source d’événement sont [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md), [Journalisation sémantique](https://msdn.microsoft.com/library/dn774980.aspx)et la [Bibliothèque TraceEvent Microsoft](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

### <a name="keywords"></a>Mots clés
Tous les événements appartenant à la source d’événement intervenants fiable sont associés à un ou plusieurs mots clés. Cela permet de filtrer les événements qui sont collectées. Les bits de mot clé suivants sont définies.

|Bit|Description|
|---|---|
|0 x 1|Définir des événements importants qui résument le fonctionnement de l’exécution de tissu intervenants.|
|0 x 2|Ensemble d’événements qui décrivent les appels de méthode acteur. Pour plus d’informations, consultez la [rubrique d’introduction sur intervenants](service-fabric-reliable-actors-introduction.md#actors).|
|0 x 4|Ensemble d’événements liés à l’état d’un acteur. Pour plus d’informations, consultez la rubrique sur [la gestion d’état acteur](service-fabric-reliable-actors-state-management.md).|
|0 x 8|Ensemble d’événements liés à la concurrence en alternance dans l’acteur. Pour plus d’informations, consultez la rubrique sur la [concurrence](service-fabric-reliable-actors-introduction.md#concurrency).|

## <a name="performance-counters"></a>Compteurs de performance
Le runtime intervenants fiable définit les catégories de compteur de performances suivantes.

|Catégorie|Description|
|---|---|
|Service TISSU acteur|Compteurs spécifiques à Azure Service TISSU intervenants, par exemple, temps nécessaire pour enregistrer l’état d’un acteur|
|Méthode de service TISSU acteur|Compteurs spécifiques aux méthodes implémentées par intervenants tissu de Service, par exemple, la fréquence à laquelle une méthode acteur est appelée|

Chacune des catégories ci-dessus contient un ou plusieurs compteurs.

L’application [Analyseur de performances Windows](https://technet.microsoft.com/library/cc749249.aspx) qui est disponible par défaut dans le système d’exploitation Windows peut être utilisée pour collecter et afficher des données compteur de performance. [Diagnostics Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) est une autre option pour la collecte de données compteur de performance et téléchargement aux tables Azure.

### <a name="performance-counter-instance-names"></a>Noms des instances compteur performance
Un cluster qui compte un grand nombre de services acteur ou partitions du service d’un acteur aura un grand nombre d’instances de compteur de performances acteur. Les noms d’instance de performances compteur peuvent aider à identifier la méthode [partition](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) et acteur spécifique (le cas échéant) que l’instance de compteur de performance est associé.

#### <a name="service-fabric-actor-category"></a>Catégorie acteur tissu de service
Pour la catégorie `Service Fabric Actor`, les noms d’instance compteur sont au format suivant :

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* est la chaîne de l’ID de partition tissu de Service associé à l’instance de compteur de performance. L’ID de partition est un GUID et représentation de type chaîne est générée par le biais du [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) méthode avec spécificateur de format « J ».

*ActorRuntimeInternalID* est la chaîne d’un nombre entier 64 bits qui est généré par le runtime TISSU intervenants pour son usage interne. Ceci est inclus dans le nom de l’instance compteur performances afin de garantir son unicité et éviter les conflits avec d’autres noms instance compteurs de performance. Les utilisateurs ne doivent pas tenter d’interpréter cette partie du nom de l’instance de compteur performances.

Voici un exemple d’un nom de l’instance compteur d’un compteur auquel appartient la `Service Fabric Actor` catégorie :

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

Dans l’exemple ci-dessus, `2740af29-78aa-44bc-a20b-7e60fb783264` est la représentation de chaîne de l’ID de partition tissu de Service, et `635650083799324046` est d’utiliser l’ID de 64 bits qui est généré pour l’exécution d’interne.

#### <a name="service-fabric-actor-method-category"></a>Catégorie de service TISSU acteur méthode
Pour la catégorie `Service Fabric Actor Method`, les noms d’instance compteur sont au format suivant :

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* est le nom de la méthode acteur associé à l’instance de compteur de performance. Le format du nom de la méthode est déterminé selon une logique dans le runtime TISSU intervenants qui équilibre la lisibilité du nom avec les contraintes sur la longueur maximale des noms instance de compteur de performance Windows.

*ActorsRuntimeMethodId* est la chaîne d’un nombre entier 32 bits qui est généré par le runtime TISSU intervenants pour son usage interne. Ceci est inclus dans le nom de l’instance compteur performances afin de garantir son unicité et éviter les conflits avec d’autres noms instance compteurs de performance. Les utilisateurs ne doivent pas tenter d’interpréter cette partie du nom de l’instance de compteur performances.

*ServiceFabricPartitionID* est la chaîne de l’ID de partition tissu de Service associé à l’instance de compteur de performance. L’ID de partition est un GUID et représentation de type chaîne est générée par le biais du [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) méthode avec spécificateur de format « J ».

*ActorRuntimeInternalID* est la chaîne d’un nombre entier 64 bits qui est généré par le runtime TISSU intervenants pour son usage interne. Ceci est inclus dans le nom de l’instance compteur performances afin de garantir son unicité et éviter les conflits avec d’autres noms instance compteurs de performance. Les utilisateurs ne doivent pas tenter d’interpréter cette partie du nom de l’instance de compteur performances.

Voici un exemple d’un nom de l’instance compteur d’un compteur auquel appartient la `Service Fabric Actor Method` catégorie :

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

Dans l’exemple ci-dessus, `ivoicemailboxactor.leavemessageasync` est le nom de la méthode `2` est l’ID 32 bits généré pour un usage interne du runtime, `89383d32-e57e-4a9b-a6ad-57c6792aa521` est la représentation de chaîne de l’ID de partition tissu de Service, et `635650083804480486` est d’utiliser l’ID de 64 bits généré pour l’exécution d’interne.

## <a name="list-of-events-and-performance-counters"></a>Liste des événements et compteurs de performance

### <a name="actor-method-events-and-performance-counters"></a>Compteurs de performance et des événements de méthode acteur
Le runtime intervenants fiable émet les événements suivants liés aux [méthodes acteur](service-fabric-reliable-actors-introduction.md#actors).

|Nom de l’événement|ID de l’événement|Niveau|Mots clés|Description|
|---|---|---|---|---|
|ActorMethodStart|7|Commentaires|0 x 2|Intervenants runtime est sur le point d’appeler une méthode d’un acteur.|
|ActorMethodStop|8|Commentaires|0 x 2|Une méthode d’un acteur est terminée. Autrement dit, appel asynchrone du runtime à la méthode acteur a renvoyé, et la tâche retournée par la méthode acteur est terminée.|
|ActorMethodThrewException|9|Avertissement|0 x 3|Une exception a été levée pendant l’exécution d’une méthode acteur, au cours d’appel asynchrone du runtime à la méthode acteur ou pendant l’exécution de la tâche retourné par la méthode acteur. Cet événement indique une sorte de défaillance dans le code acteur nécessitant l’enquête.|

Le runtime intervenants fiable publie des compteurs de performance suivants relatifs à l’exécution des méthodes acteur.

|Nom de la catégorie|Nom du compteur|Description|
|---|---|---|
|Méthode de service TISSU acteur|Appels/Sec|Nombre de fois où la méthode de service acteur est appelée par seconde|
|Méthode de service TISSU acteur|Moyennes millisecondes par appel|Temps nécessaire pour exécuter la méthode de service acteur en millisecondes|
|Méthode de service TISSU acteur|Exceptions levée/Sec|Nombre de fois que l’acteur service méthode a levé une exception par seconde|

### <a name="concurrency-events-and-performance-counters"></a>Compteurs de performance et des événements de la concurrence
Le runtime intervenants fiable émet les événements suivants liés à la [concurrence](service-fabric-reliable-actors-introduction.md#concurrency).

|Nom de l’événement|ID de l’événement|Niveau|Mots clés|Description|
|---|---|---|---|---|
|ActorMethodCallsWaitingForLock|12|Commentaires|0 x 8|Cet événement est enregistré au début de chaque nouveau soumettre un acteur. Il affiche le nombre d’appels acteur en attente d’acquérir le verrou par acteur propice à la concurrence en alternance en attente.|

Le runtime intervenants fiable publie des compteurs de performance suivants liés à la concurrence.

|Nom de la catégorie|Nom du compteur|Description|
|---|---|---|
|Service TISSU acteur|# d’un acteur appels en attente de verrouillage acteur|Nombre d’appels acteur en attente d’acquérir le verrou par acteur propice à la concurrence en alternance en attente|
|Service TISSU acteur|Attendre la moyennes millisecondes par verrou|Temps écoulé (en millisecondes) d’acquérir le verrou par acteur propice à la concurrence en alternance|
|Service TISSU acteur|Moyenne millisecondes acteur verrou|Durée (en millisecondes) pour laquelle se trouve le verrou par acteur|

### <a name="actor-state-management-events-and-performance-counters"></a>Compteurs de performance et des événements de gestion des États acteur
Le runtime intervenants fiable émet les événements suivants liés à la [Gestion d’état acteur](service-fabric-reliable-actors-state-management.md).

|Nom de l’événement|ID de l’événement|Niveau|Mots clés|Description|
|---|---|---|---|---|
|ActorSaveStateStart|10|Commentaires|0 x 4|Intervenants runtime est sur le point d’enregistrer l’état d’un acteur.|
|ActorSaveStateStop|11|Commentaires|0 x 4|Intervenants runtime a terminé l’enregistrement de l’état d’un acteur.|

Le runtime intervenants fiable publie des compteurs de performance suivants liés à la gestion d’état acteur.

|Nom de la catégorie|Nom du compteur|Description|
|---|---|---|
|Service TISSU acteur|Moyennes millisecondes par enregistrement l’opération d’état|Temps nécessaire pour enregistrer l’état d’un acteur en millisecondes|
|Service TISSU acteur|Moyennes millisecondes par opération de chargement d’état|Temps nécessaire pour charger l’état d’un acteur en millisecondes|

### <a name="events-related-to-actor-replicas"></a>Événements liés aux réplicas acteur
Le runtime intervenants fiable émet les événements suivants liés aux [réplicas acteur](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-stateful-actors).

|Nom de l’événement|ID de l’événement|Niveau|Mots clés|Description|
|---|---|---|---|---|
|ReplicaChangeRoleToPrimary|1|D’information|0 x 1|Copie d’un acteur modifiées rôle en principal. Cela signifie que les intervenants pour cette partition est créées à l’intérieur de ce réplica.|
|ReplicaChangeRoleFromPrimary|2|D’information|0 x 1|Copie d’un acteur modifiées rôle en secondaires. Cela signifie que les intervenants pour cette partition n’est plus est créées à l’intérieur de ce réplica. Aucune nouvelle demande n’est remises à intervenants déjà créés dans ce réplica. Les intervenants seront détruites après avoir réalisé toutes les demandes en cours.|

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>Compteurs de performance et des événements de l’activation et désactivation acteur
Le runtime intervenants fiable émet les événements suivants liés à [un acteur activation et désactivation](service-fabric-reliable-actors-lifecycle.md).

|Nom de l’événement|ID de l’événement|Niveau|Mots clés|Description|
|---|---|---|---|---|
|ActorActivated|5|D’information|0 x 1|Un acteur a été activé.|
|ActorDeactivated|6|D’information|0 x 1|Un acteur a été désactivé.|

Le runtime intervenants fiable publie des compteurs de performance suivants liés à un acteur activation et désactivation.

|Nom de la catégorie|Nom du compteur|Description|
|---|---|---|
|Service TISSU acteur|Trouver la moyenne OnActivateAsync millisecondes|Temps nécessaire pour exécuter la méthode OnActivateAsync en millisecondes|

### <a name="actor-request-processing-performance-counters"></a>Traitement des demandes acteur compteurs
Lorsqu’un client appelle une méthode via un objet proxy acteur, elle produit comme résultat dans un message de demande envoyé sur le réseau au service acteur. Le service traite le message de demande et envoie une réponse au client. Le runtime intervenants fiable publie des compteurs de performance suivants liés au traitement des demandes acteur.

|Nom de la catégorie|Nom du compteur|Description|
|---|---|---|
|Service TISSU acteur|# de demandes en attente|Nombre de requêtes traitées dans le service|
|Service TISSU acteur|Moyennes millisecondes par demande|Temps écoulé (en millisecondes) par le service pour traiter une requête|
|Service TISSU acteur|Moyenne en millisecondes pour la désérialisation demande|Temps écoulé (en millisecondes) pour désérialiser le message de demande d’un acteur correctement après réception au niveau du service|
|Service TISSU acteur|Moyenne en millisecondes pour sérialisation de réponse|Temps écoulé (en millisecondes) pour vérifier le message de réponse acteur au niveau du service avant la réponse est envoyée au client|

## <a name="next-steps"></a>Étapes suivantes
 - [Comment intervenants fiable utiliser la plateforme tissu de Service](service-fabric-reliable-actors-platform.md)
 - [Documentation de référence acteur API](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [Exemples de code](https://github.com/Azure/servicefabric-samples)
