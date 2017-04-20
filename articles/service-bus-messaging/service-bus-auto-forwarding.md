<properties 
    pageTitle="Transfert automatique Bus des services de messagerie entités | Microsoft Azure"
    description="Comment faire pour un abonnement à un autre file d’attente ou rubrique ou file d’attente de la chaîne."
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
    ms.date="09/29/2016"
    ms.author="sethm" />

# <a name="chaining-service-bus-entities-with-auto-forwarding"></a>Chaîne entités Bus des services avec transfert automatique

La fonctionnalité de *transfert automatique* vous permet de la chaîne de file d’attente ou d’abonnement à un autre file d’attente ou la rubrique qui fait partie de l’espace de noms. Lorsque le transfert automatique est activé, Bus des services supprime les messages qui sont placées dans la file d’attente ou d’abonnement (source) première automatiquement et les place dans la deuxième file d’attente ou rubrique (destination). Notez qu’il est toujours possible d’envoyer un message à l’entité de destination directement. En outre, il n’est pas possible de bicyclette une file d’attente secondaire, par exemple une file d’attente lettres mortes, dans une autre file d’attente ou rubrique.

## <a name="using-auto-forwarding"></a>À l’aide de transfert automatique

Vous pouvez activer le transfert automatique en définissant les propriétés de [SubscriptionDescription.ForwardTo][] ou [QueueDescription.ForwardTo][] sur les objets [QueueDescription][] ou [SubscriptionDescription][] pour la source, comme dans l’exemple suivant.

```
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

L’entité de destination doit exister au moment de que la création de l’entité source. Si l’entité de destination n’existe pas, Service Bus renvoie une exception lorsque vous êtes invité à créer l’entité source.

Vous pouvez utiliser le transfert automatique à l’échelle une rubrique individuelle. Service Bus limite le [nombre d’abonnements sur un sujet donné](service-bus-quotas.md) à 2 000. Vous pouvez prendre en charge des abonnements supplémentaires en créant des rubriques de second niveau. Notez que même si vous ne sont pas liés à la limitation de Bus des services sur le nombre d’abonnements, ajout d’un deuxième niveau de rubriques peut améliorer la vitesse globale de votre sujet.

![Scénario de transfert automatique][0]

Vous pouvez également utiliser transfert automatique de dissocier les expéditeurs de messages à partir de récepteurs. Prenons l’exemple d’un système de planification qui se compose de trois modules : traitement des commandes, la gestion des stocks et la gestion des Relations client. Chacun de ces modules génère des messages qui sont en attente dans un sujet correspondant. Alice et Bob sont commerciaux qui intéressent dans tous les messages relatives à leurs clients. Pour recevoir les messages, Alice et Bob créent des queue personnel et un abonnement sur chacune des rubriques planification qui transfèrent automatiquement tous les messages à leur file d’attente.

![Scénario de transfert automatique][1]

Si Alice part en vacances, son file d’attente personnelle, plutôt que la rubrique Planification, remplit. Dans ce scénario, car un commercial n’a pas reçu tous les messages, aucun des rubriques planification atteigne jamais quota.

## <a name="auto-forwarding-considerations"></a>Considérations sur le transfert automatique

Si l’entité de destination accumulé grand nombre de messages et dépasse le quota d’ou l’entité de destination est désactivée, l’entité source ajoute les messages à sa [file d’attente suspendue](service-bus-dead-letter-queues.md) jusqu'à ce que l’espace dans la destination est (ou l’entité est réactivée). Ces messages continuent à résident dans la file d’attente suspendue, afin que vous devez recevoir explicitement et les traiter à partir de la file d’attente suspendue.

Lors de la chaîne ensemble des rubriques pour obtenir une rubrique composite avec plusieurs abonnements, il est recommandé que vous disposez d’un nombre modéré d’abonnements sur le sujet de premier niveau et nombre d’abonnements dans les rubriques de second niveau. Par exemple, un sujet de premier niveau avec 20 abonnements, chacun d’eux lié à un sujet de deuxième niveau avec 200 abonnements, permet de débit à un sujet de premier niveau avec 200 abonnements, chacun lié à un sujet de deuxième niveau avec 20 abonnements.

Service Bus échéances une opération pour chaque message transféré. Par exemple, envoyer un message à un sujet avec 20 abonnements, chacun d’eux configuré pour automatique transférer des messages vers un autre file d’attente ou rubrique est facturé 21 opérations si tous les abonnements de premier niveau reçoivent une copie du message.

Pour créer un abonnement qui est lié à une autre file d’attente ou rubrique, le créateur de l’abonnement doivent **Gérer** les autorisations sur la source et l’entité de destination. Envoi de messages à la rubrique source requiert uniquement des autorisations **Envoyer** dans la rubrique source.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le transfert d’automatique, consultez les rubriques de référence suivantes :

- [SubscriptionDescription.ForwardTo][]
- [QueueDescription][]
- [SubscriptionDescription][]

Pour en savoir plus sur les améliorations des performances Bus des services, consultez [partitionnées messagerie entités][].

  [QueueDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.forwardto.aspx
  [SubscriptionDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.forwardto.aspx
  [QueueDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx
  [SubscriptionDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.aspx
  [0]: ./media/service-bus-auto-forwarding/IC628631.gif
  [1]: ./media/service-bus-auto-forwarding/IC628632.gif
  [Entités messageries partitionnées]: service-bus-partitioning.md