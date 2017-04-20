<properties 
    pageTitle="Partition files d’attente et rubriques | Microsoft Azure"
    description="Décrit comment partition files d’attente Bus des services et les rubriques d’aide de plusieurs agents de message."
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
    ms.date="09/02/2016"
    ms.author="sethm;hillaryc" />

# <a name="partitioned-queues-and-topics"></a>Rubriques et partitionnées files d’attente

Bus des services Azure utilise plusieurs agents de message pour traiter les messages et plusieurs banques de messagerie pour stocker les messages. Un conventionnelle file d’attente ou une rubrique est géré par un agent de message unique et stockée dans un magasin de messagerie. Service Bus permet également de files d’attente ou rubriques répartir sur plusieurs agents de message et banques de messagerie. Cela signifie que la vitesse globale d’un partitionnée file d’attente ou une rubrique n’est plus limitée par les performances d’un intermédiaire seul message ou de la banque d’informations. En outre, une panne temporaire d’une banque de messagerie ne rend pas un partitionnée file d’attente ou une rubrique indisponible. Rubriques et files d’attente partitionnées peuvent contenir toutes les Bus des services fonctionnalités avancées, telles que des transactions et les sessions.

Pour plus d’informations sur les éléments internes Bus des services, consultez la rubrique [architecture Bus des services][] .

## <a name="how-it-works"></a>Mode de fonctionnement

Chaque file d’attente partitionnée ou une rubrique se compose de plusieurs fragments. Chaque fragment est stockée dans une autre banque de messagerie et gérée par un agent de message différent. Lorsqu’un message est envoyé à un partitionnée file d’attente ou une rubrique, Bus des services affecte le message à un des fragments. La sélection est terminée aléatoirement en Bus des services ou en utilisant une clé de partition que l’expéditeur peut spécifier.

Lorsqu’un client souhaite recevoir un message à partir d’une file d’attente partitionnée ou d’un abonnement d’une rubrique partitionnée, les requêtes de Service Bus tous les fragments de messages, puis renvoie le premier message obtenu à partir d’une des banques de messagerie au destinataire. Cache de service Bus l’autre des messages et les renvoie lorsqu’il reçoit supplémentaires reçoivent des demandes. Un client du destinataire n’a pas connaissance de la partition ; le comportement de clients d’une rubrique ou partitionnée file d’attente (par exemple, lire, exécuter, différer, lettres mortes, lecture anticipée) est identique à celui d’une entité normale.

Il est sans frais supplémentaires lorsque envoyer un message à ou recevoir un message à partir d’une rubrique ou partitionnée file d’attente.

## <a name="enable-partitioning"></a>Activer la partition

Pour utiliser partitionnées files d’attente et rubriques à Azure Service, utiliser le Kit de développement Azure 2.2 ou version ultérieure, ou spécifier `api-version=2013-10` dans votre HTTP demande.

Vous pouvez créer des files d’attente Bus des services et les rubriques dans les tailles de 1, 2, 3, 4 ou 5 Go (la valeur par défaut est 1 Go). Avec partition activé, Bus des services crée 16 partitions pour chaque Go que vous spécifiez. Dès lors, si vous créez une file d’attente de 5 Go taille, avec 16 partitions la taille maximale de file d’attente devient (5 \* 16) = 80 Go. Vous pouvez voir la taille maximale de votre file d’attente partitionnée ou une rubrique en consultant son entrée sur le [portail Azure][].

Il existe différentes façons pour créer un partitionnée file d’attente ou une rubrique. Lorsque vous créez la file d’attente ou une rubrique à partir de votre application, vous pouvez activer partition pour la file d’attente ou une rubrique en **respectivement affectant la propriété [QueueDescription.EnablePartitioning][] ou [TopicDescription.EnablePartitioning][] **. Ces propriétés doivent être définies au moment où la file d’attente ou rubrique est créée. Il n’est pas possible de modifier ces propriétés sur un file d’attente existante ou une rubrique. Par exemple :

```
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Par ailleurs, vous pouvez créer un partitionnée file d’attente ou une rubrique dans Visual Studio ou dans le [portail Azure][]. Lorsque vous créez une nouvelle file d’attente ou une rubrique dans le portail, définissez l’option **Activer la partition** dans la carte de **Paramètres généraux** de la rubrique fenêtre **paramètres** **true**ou file d’attente. Dans Visual Studio, cliquez sur la case à cocher **Activer la partition** dans la boîte de dialogue **Nouvelle file d’attente** ou **Nouveau sujet** .

## <a name="use-of-partition-keys"></a>Utiliser des clés de partition

Lorsqu’un message est placé dans une file d’attente partitionnée ou une rubrique, Bus des services vérifie la présence d’une clé de partition. Si elle en trouve, il sélectionne le fragment basé sur cette touche. Si elle ne trouve pas une clé de partition, il sélectionne le fragment basé sur un algorithme interne.

### <a name="using-a-partition-key"></a>À l’aide d’une clé de partition

Certains scénarios, tels que des sessions ou transactions, nécessitent messages soient stockés dans un fragment spécifique. Tous ces scénarios nécessitent l’utilisation d’une clé de partition. Tous les messages qui utilisent la même clé partition affectés à la même fragment. Si le fragment est temporairement indisponible, Service Bus retourne une erreur.

Selon le scénario, les propriétés de message différentes sont utilisées comme clé partition :

**ID de session**: si un message est défini pour la propriété [BrokeredMessage.SessionId][] , puis Bus des services utilise cette propriété comme clé de partition. Ainsi, tous les messages qui appartiennent à la même session sont gérées par l’intermédiaire message même. Cela permet de Bus des services afin de garantir message classement ainsi que la cohérence des États de session.

**PartitionKey**: si un message avec la propriété [BrokeredMessage.PartitionKey][] , mais pas la propriété [BrokeredMessage.SessionId][] définir, puis Bus des services utilise la propriété [PartitionKey][] comme clé de partition. Si le message a l' [ID de session][] et les propriétés définies [PartitionKey][] , les deux propriétés doivent être identiques. Si la propriété [PartitionKey][] est définie sur une valeur différente de la propriété [ID de session][] , Service Bus renvoie une exception **InvalidOperationException** . La propriété [PartitionKey][] doit être utilisée si un expéditeur envoie des messages de transactions prenant en charge non session. Clé de partition garantit que tous les messages sont envoyés au sein d’une transaction sont gérées par l’intermédiaire de messagerie même.

**MessageId**: si la file d’attente ou une rubrique comporte la propriété [QueueDescription.RequiresDuplicateDetection][] la valeur **true** , les propriétés [BrokeredMessage.SessionId][] ou [BrokeredMessage.PartitionKey][] ne sont pas définies, puis la propriété [BrokeredMessage.MessageId][] sert de clé de partition. (Notez que les bibliothèques Microsoft .NET et AMQP affecte automatiquement un ID de message si n’est pas le cas de l’application d’envoi). Dans ce cas, toutes les copies du même message sont gérées par l’intermédiaire message même. Cela permet de Bus des services détecter et éliminer les messages en double. Si la propriété [QueueDescription.RequiresDuplicateDetection][] n’est pas définie sur **true**, Bus des services ne tient pas compte de la propriété [MessageId][] comme clé partition.

### <a name="not-using-a-partition-key"></a>N’utilisez ne pas une clé de partition

En l’absence d’une clé de partition, Bus des services distribue les messages de manière cyclique que tous les fragments de la rubrique ou partitionnée file d’attente. Si le fragment choisi n’est pas disponible, Bus des services affecte le message vers un autre fragment. Ainsi, l’opération d’envoi établie malgré l’indisponibilité provisoire d’une banque de messagerie.

Pour donner aux Bus des services suffisamment de temps pour placer le message dans un autre fragment, la valeur [MessagingFactorySettings.OperationTimeout][] spécifiée par le client qui envoie le message doit être supérieure à 15 secondes. Il est recommandé que vous définissez la propriété [OperationTimeout][] à la valeur par défaut de 60 secondes.

Notez qu’une clé de partition « épingle » un message à un fragment spécifique. Si la banque d’informations qui contient ce fragment n’est pas disponible, Service Bus retourne une erreur. En l’absence d’une clé de partition, Bus des services peuvent choisir un autre fragment et l’opération réussit. Par conséquent, il est recommandé que vous ne spécifiez pas une clé de partition sauf s’il est requis.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Informations complémentaires : utiliser des transactions avec entités partitionnées

Les messages envoyés dans le cadre d’une transaction doivent spécifier une clé de partition. Cela peut être une des propriétés suivantes : [BrokeredMessage.SessionId][], [BrokeredMessage.PartitionKey][]ou [BrokeredMessage.MessageId][]. Tous les messages envoyés dans le cadre de la même transaction doivent spécifier la même clé partition. Si vous essayez d’envoyer un message sans une clé de partition au sein d’une transaction, Service Bus renvoie une exception **InvalidOperationException** . Si vous essayez d’envoyer des messages dans la même transaction plusieurs qui ont des clés partition différente, Service Bus renvoie une exception **InvalidOperationException** . Par exemple :

```
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.PartitionKey = "myPartitionKey";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

Si les propriétés qui servent à une clé de partition sont définies, Bus de Service d’épingle le message vers un fragment spécifique. Ce problème se produit si une transaction est utilisée ou non. Il est recommandé que vous ne spécifiez pas une clé de partition s’il n’est pas nécessaire.

## <a name="using-sessions-with-partitioned-entities"></a>À l’aide de sessions avec entités partitionnées

Pour envoyer un message transactions à un sujet prenant en charge session ou la file d’attente, le message doit être la propriété [BrokeredMessage.SessionId][] définie. Si la propriété [BrokeredMessage.PartitionKey][] est également spécifiée, elle doit être identique à la propriété [ID de session][] . S’ils sont différents, Service Bus renvoie une exception **InvalidOperationException** .

Contrairement aux files d’attente (non partitions) normales ou rubriques, il n’est pas possible d’utiliser une transaction unique pour envoyer des messages de plusieurs sessions différentes. Dans ce cas, Service Bus renvoie une exception **InvalidOperationException **. Par exemple :

```
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.SessionId = "mySession";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Transfert avec des messages automatiques partitionnement entités

Bus des services Azure prend en charge le transfert automatique des messages à partir de, à ou entre entités partitionnées. Pour activer le transfert de messages automatique, définissez la propriété [QueueDescription.ForwardTo][] dans la file d’attente source ou d’abonnement. Si le message spécifie une clé de partition ([ID de session][], [PartitionKey][]ou [MessageId][]), cette touche partition est utilisée pour l’entité de destination.

## <a name="considerations-and-guidelines"></a>Considérations et les instructions

- **Fonctionnalités de cohérence élevé**: si une entité utilise des fonctionnalités telles que les sessions, détection des doublons ou contrôle explicite de partition clé, puis les opérations de messagerie sont toujours routées vers fragments spécifiques. Si un des fragments expérience trafic élevé ou du magasin sous-jacent ne fonctionne pas correctement, ces opérations échouer et disponibilité est réduite. En général, la cohérence est toujours beaucoup Pluse entités non partitions ; uniquement un sous-ensemble de trafic rencontre des problèmes, contrairement à tout le trafic.
- **Gestion**: opérations, telles que Create, mise à jour et supprimer doivent être effectuées sur tous les fragments de l’entité. Si n’importe quel fragment ne fonctionne pas correctement il peut entraîner des erreurs pour effectuer ces opérations. Pour l’opération d’obtention des informations telles que message compte doivent être regroupées à partir de tous les fragments. Si n’importe quel fragment ne fonctionne pas correctement, le statut de disponibilité entité est signalé comme limité.
- **Scénarios de message faible volume**: pour ces scénarios, notamment lorsque vous utilisez le protocole HTTP, vous devrez peut-être effectuer plusieurs opérations de réception afin d’obtenir tous les messages. Pour les demandes de réception, le serveur frontal effectue une réception sur tous les fragments et met en cache toutes les réponses reçues. Une demande de réception suivants sur la même connexion serait bénéficier de cette mise en cache et recevoir latence sera inférieure. Toutefois, si vous avez plusieurs connexions ou utilisez le protocole HTTP, qui établit une connexion pour chaque demande. Par conséquent, il n’existe aucune garantie qu’il serait placer sur le même nœud. Si tous les messages existants sont verrouillés et mis en cache dans un autre serveur frontal, l’opération de réception renvoie la **valeur null**. Messages arriveront à expiration et vous pouvez les recevoir à nouveau. Conservation HTTP est recommandé.
- **Parcourir/lire des messages**: PeekBatch ne retourne pas toujours le nombre de messages spécifié dans la [propriété MessageCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.messagecount.aspx). Il existe deux raisons courantes. L’une des raisons sont que la taille de la collection de messages agrégée dépasse la taille maximale de 256 Ko. Il est possible que si la file d’attente ou une rubrique comporte la [propriété EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx) la valeur **true**, une partition peut-être pas suffisamment de messages pour terminer le nombre requis de messages. En règle générale, si une application souhaite recevoir un certain nombre de messages, elle doit appeler [PeekBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peekbatch.aspx) à plusieurs reprises jusqu'à ce qu’il obtient ce nombre de messages ou aucun message plus à lire. Pour plus d’informations, y compris les exemples de code, voir [QueueClient.PeekBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peekbatch.aspx) ou [SubscriptionClient.PeekBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.peekbatch.aspx).

## <a name="latest-added-features"></a>Dernières fonctionnalités ajoutées

- Ajouter ou supprimer des règle est désormais pris en charge avec entités partitionnées. Différent d’entités non partition, ces opérations ne sont pas pris en charge sous transactions. 
- AMQP est désormais pris en charge pour envoyer et recevoir des messages vers et à partir d’une entité partitionnée.
- AMQP est désormais pris en charge pour les opérations suivantes : [Lot envoyer](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.sendbatch.aspx), [Lot recevoir](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.receivebatch.aspx), [réception par numéro de séquence](https://msdn.microsoft.com/library/azure/hh330765.aspx), [lire](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peek.aspx), [Verrouillage renouveler](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.renewmessagelock.aspx), [Message échéancier](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.schedulemessageasync.aspx), [Annuler le Message planifié](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.cancelscheduledmessageasync.aspx), [Ajouter une règle](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ruledescription.aspx), [Supprimer une règle](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ruledescription.aspx), [Session renouveler verrouiller](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.renewlock.aspx), [Définir l’état de Session](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.setstate.aspx), [Obtenir l’état de Session](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.getstate.aspx), [Lire les Messages de Session](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.peek.aspx)et [Énumérer les Sessions](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.getmessagesessionsasync.aspx).

## <a name="partitioned-entities-limitations"></a>Limitations entités partitionnées

Actuellement Bus des services impose les limitations partitionnées files d’attente et rubriques suivantes :

-   Rubriques et partitionnées files d’attente ne prennent pas en charge envoi de messages appartenant à différentes sessions en une seule opération.
-   Service Bus permet actuellement jusqu'à 100 files d’attente partitionnées ou rubriques sont affichées par l’espace de noms. Chaque file d’attente partitionnée ou la rubrique compte vers le quota de 10 000 entités par espace de noms (ne s’applique pas aux niveau Premium).

## <a name="next-steps"></a>Étapes suivantes

Consultez la rubrique sur la [prise en charge de AMQP 1.0 Bus des services partition files d’attente et rubriques][] pour en savoir plus sur partition entités messageries. 

  [Architecture de Bus de service]: service-bus-architecture.md
  [Portail Azure]: https://portal.azure.com
  [QueueDescription.EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx
  [TopicDescription.EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablepartitioning.aspx
  [BrokeredMessage.SessionId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx
  [BrokeredMessage.PartitionKey]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.partitionkey.aspx
  [ID de session]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx
  [PartitionKey]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.partitionkey.aspx
  [QueueDescription.RequiresDuplicateDetection]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection.aspx
  [BrokeredMessage.MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [QueueDescription.RequiresDuplicateDetection]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection.aspx
  [MessagingFactorySettings.OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
  [OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
  [QueueDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.forwardto.aspx
  [Prise en charge de AMQP 1.0 Bus des services partition files d’attente et rubriques]: service-bus-partitioned-queues-and-topics-amqp-overview.md
