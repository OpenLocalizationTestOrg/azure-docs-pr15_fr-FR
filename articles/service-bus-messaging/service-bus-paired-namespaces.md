<properties 
    pageTitle="Service Bus par paires espaces de noms | Microsoft Azure"
    description="Détails de l’espace de noms pour implémentation et coût"
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/04/2016"
    ms.author="sethm" />

# <a name="paired-namespace-implementation-details-and-cost-implications"></a>Par paires détails d’implémentation espace de noms et des coûts implications

La méthode [PairNamespaceAsync][] , en utilisant une instance [SendAvailabilityPairedNamespaceOptions][] , effectue les tâches visibles à votre place. Étant donné que des coûts sont Considérations lors de l’utilisation de la fonctionnalité, il est utile de comprendre ces tâches afin que vous attendez le comportement lorsque ce problème survient. L’API engage le comportement automatique suivant à votre place :

-   Création de files d’attente en retard.
-   Création d’un objet [MessageSender][] qui communique avec files d’attente ou rubriques.
-   Lorsqu’une entité messagerie deviendrait indisponible, envoie une commande ping messages à l’entité lors d’une tentative de détecter lorsque cette entité redevienne disponible.
-   Crée éventuellement d’un ensemble de « pompes de messages » qui déplacer les messages les files d’attente file d’attente les files d’attente principal.
-   Coordonnées cas/de défaillance clôture des instances [MessagingFactory][] principaux et secondaires.

À un niveau élevé, la fonctionnalité fonctionne comme suit : lorsque l’entité principale fonctionne correctement, aucune modification comportement se produire. Lorsque la durée [FailoverInterval][] s’écoule et l’entité principale voit réussie n’envoie après un non transitoires [MessagingException][] ou une [TimeoutException][], le comportement suivant se produit :

1.  Envoyer des opérations à l’entité principale sont désactivées et le système connecte à l’entité principale jusqu'à ce que les requêtes ping peuvent être remis.

2.  Une file d’attente en retard aléatoire est sélectionné.

3.  Objets [BrokeredMessage][] sont routés vers la file d’attente file d’attente choisie.

1.  Si une opération d’envoi dans la file d’attente choisie file d’attente échoue, cette file d’attente est extrait à partir de la rotation et une nouvelle file d’attente est sélectionnée. Tous les expéditeurs de l’instance [MessagingFactory][] obtenir des informations de l’échec.

Les figures suivantes décrivent la séquence. Tout d’abord, l’expéditeur envoie des messages.

![Espaces de noms appariés][0]

En cas de panne pour envoyer à la file d’attente principale, l’expéditeur commence envoyant des messages à une file d’attente choisi au hasard en retard. Simultanément, il démarre une tâche ping.

![Espaces de noms appariés][1]

À ce stade, les messages sont toujours dans la file d’attente secondaire et n’ont pas été remis à la file d’attente principale. Une fois que la file d’attente principal est correct à nouveau, au moins un processus doit s’exécuter le syphon. La syphon propose les messages à partir de toutes les files d’attente en retard différents aux entités destination NOMPROPRE (files d’attente et rubriques).

![Espaces de noms appariés][2]

Le reste de cette rubrique décrit les détails spécifiques du fonctionnement de ces éléments.

## <a name="creation-of-backlog-queues"></a>Création de files d’attente en retard

L’objet [SendAvailabilityPairedNamespaceOptions][] passé à la méthode [PairNamespaceAsync][] indique le nombre de files d’attente file d’attente que vous souhaitez utiliser. Chaque file d’attente en retard est créé avec les propriétés suivantes explicitement ensemble (tous les autres valeurs sont définies sur les valeurs par défaut [QueueDescription][] ) :

| Chemin d’accès                                   | [espace de noms principal] / x-servicebus-transfert / [index] [index] étant une valeur dans [0, BacklogQueueCount) |
|----------------------------------------|------------------------------------------------------------------------------------------------------|
| MaxSizeInMegabytes                     | 5120                                                                                                 |
| MaxDeliveryCount                       | int. MaxValue                                                                                         |
| DefaultMessageTimeToLive               | TimeSpan.MaxValue                                                                                    |
| AutoDeleteOnIdle                       | TimeSpan.MaxValue                                                                                    |
| LockDuration                           | minute                                                                                             |
| EnableDeadLetteringOnMessageExpiration | Vrai                                                                                                 |
| EnableBatchedOperations                | Vrai                                                                                                 |

Par exemple, la première file d’attente file d’attente créée pour l' espace de noms **contoso** est nommé `contoso/x-servicebus-transfer/0`.

Lorsque vous créez les files d’attente, le code vérifie d’abord s’il existe une file d’attente. Si la file d’attente n’existe pas, la file d’attente est créée. Le code ne pas nettoyer les files d’attente file d’attente « supplémentaire ». Plus précisément, si l’application avec l' espace de noms principal **contoso** demande cinq file d’attente files d’attente, mais une file file d’attente avec le chemin d’accès `contoso/x-servicebus-transfer/7` existe, cette file d’attente en retard supplémentaire est toujours présente, mais n’est pas utilisé. Le système permet explicitement supplémentaire file d’attente files d’attente d’exister qui ne serait pas utilisés. En tant que propriétaire de l’espace de noms, vous êtes chargé de nettoyer les files d’attente inutilisés/indésirables en retard. La raison pour laquelle cette décision est que Bus des services ne peut pas savoir quelles fins existent pour toutes les files d’attente dans votre espace de noms. En outre, si une file d’attente existe avec le nom donné, mais ne répond pas à la supposé [QueueDescription][], puis vos raisons sont les vôtres permettant de modifier le comportement par défaut. Aucune garantie n’est conçues pour les modifications les files d’attente en retard par votre code. Veillez à tester vos modifications de manière approfondie.

## <a name="custom-messagesender"></a>MessageSender personnalisé

Lors de l’envoi, tous les messages, passez par un objet [MessageSender][] interne qui se comporte normalement lorsque tout fonctionne et que vous redirige vers les files d’attente file d’attente en «. » Lorsque vous recevez une défaillance non transitoires, une horloge démarre. Après une période [TimeSpan][] composé de la valeur de propriété [FailoverInterval][] pendant lesquelles aucun message réussi est envoyé, le basculement est activé. À ce stade, les événements suivants se produisent pour chaque entité :

- Une tâche ping s’exécute chaque [PingPrimaryInterval][] pour vérifier si l’entité est disponible. Une fois que cette tâche a réussi, tout le code client qui utilise l’entité immédiatement démarre envoyer de nouveaux messages à l’espace de noms principal.

- Futures demandes d’envoyer à ce même entité à partir de tous les expéditeurs entraîne la [BrokeredMessage][] envoyé à être modifié dans la file d’attente en retard. La modification supprime certaines propriétés de l’objet [BrokeredMessage][] et stocke les ailleurs. Les propriétés suivantes sont désactivées et ajoutées sous un nouvel alias, ce qui permet de Bus des services et le Kit de développement traiter les messages de manière uniforme :

| Ancien nom de la propriété       | Nouveau nom de propriété |
|-------------------------|-------------------|
| ID de session               | x-ms-ID de la session    |
| Propriété TimeToLive              | x-ms-timetolive   |
| ScheduledEnqueueTimeUtc | x-ms-chemin d’accès         |

Le chemin d’accès de destination d’origine est également stockée dans le message comme une propriété nommée x-ms-chemin d’accès. Ce modèle permet de messages pour de nombreuses entités peuvent coexister dans une file d’attente file d’attente unique. Les propriétés sont converties en la syphon.

L’objet [MessageSender][] personnalisé peut rencontrer des problèmes lorsque les messages approchent de la limite de 256 Ko et basculement est activé. L’objet [MessageSender][] personnalisé stocke les messages pour tous les files d’attente et rubriques ensemble dans les files d’attente en retard. Cet objet contient à la fois les messages à partir de nombreuses couleurs primaires ensemble dans les files d’attente en retard. Pour gérer l’équilibrage de charge entre de nombreux clients qui ne savent pas chacune des autres, le Kit de développement choisit aléatoirement une file d’attente en retard pour chaque [QueueClient][] ou [TopicClient][] vous créez dans le code.

## <a name="pings"></a>Commandes ping

Un message ping est un vide [BrokeredMessage][] avec sa propriété [ContentType][] application/vnd.ms-servicebus-ping et une valeur de [la propriété TimeToLive][] 1 seconde. Ce test ping a une caractéristique spéciale dans Service marché : il livre jamais une commande ping lorsqu’un appelant demande une [BrokeredMessage][]. Par conséquent, vous devrez jamais Apprenez à recevoir et ignorer ces messages. Chaque entité (file d’attente unique ou une rubrique) de chaque instance [MessagingFactory][] par client sera être exécutée sur lorsqu’ils sont considérés comme non disponible. Par défaut, cela se produit une fois par minute. Messages ping sont considérés comme régulier des messages Bus des services et peuvent entraîner des frais de bande passante et les messages. Dès que les clients détectent que le système est disponible, les messages arrêter.

## <a name="the-syphon"></a>La syphon

Au moins un programme exécutable dans l’application doit s’exécuter désencombrer la syphon. La syphon effectue un long sondage recevoir qui dure 15 minutes. Lorsque toutes les entités sont disponibles et que vous avez 10 files d’attente en retard, l’application qui héberge la syphon appelle l’opération de réception 40 fois par heure, 960 heures par jour et 28800 heures dans les 30 jours. Lorsque la syphon est activement déplacement des messages à partir de la file d’attente dans la file d’attente principale, chaque message rencontre les frais suivants (frais standards pour la taille des messages et la bande passante s’appliquent à toutes les étapes) :

1.  Envoyer vers la file d’attente.

2.  Recevoir de la file d’attente.

3.  Envoyer vers le serveur principal.

4.  Recevoir du site principal.

## <a name="closefault-behavior"></a>Fermer/défaillance comportement

Dans une application qui héberge la syphon, une fois les pannes [MessagingFactory][] principales ou secondaires ou s’il est fermée sans son partenaire étant également défectueux/fermées et la syphon détecte cet état, comportements syphon. Si d’autres [MessagingFactory][] n’est pas fermée dans 5 secondes, la syphon sera pannes la [MessagingFactory][]encore ouverte.

## <a name="next-steps"></a>Étapes suivantes

Pour une description détaillée de la messagerie asynchrone Bus des services, voir [modèles de messagerie asynchrones et la disponibilité][] . 

  [PairNamespaceAsync]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.pairnamespaceasync.aspx
  [SendAvailabilityPairedNamespaceOptions]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.aspx
  [MessageSender]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx
  [MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [FailoverInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.pairednamespaceoptions.failoverinterval.aspx
  [MessagingException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
  [TimeoutException]: https://msdn.microsoft.com/library/azure/system.timeoutexception.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [QueueDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx
  [TimeSpan]: https://msdn.microsoft.com/library/azure/system.timespan.aspx
  [PingPrimaryInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.pingprimaryinterval.aspx
  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx
  [ContentType]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx
  [Propriété TimeToLive]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx
  [Modèles de messagerie asynchrones et la disponibilité]: service-bus-async-messaging.md
  [0]: ./media/service-bus-paired-namespaces/IC673405.png
  [1]: ./media/service-bus-paired-namespaces/IC673406.png
  [2]: ./media/service-bus-paired-namespaces/IC673407.png
