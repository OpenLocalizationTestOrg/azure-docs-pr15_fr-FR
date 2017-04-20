<properties 
    pageTitle="Transactions Bus des services | Microsoft Azure" 
    description="Vue d’ensemble des transactions atomiques Bus des services Azure et envoyez-le via" 
    services="service-bus" 
    documentationCenter=".net" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na" 
    ms.date="10/04/2016"
    ms.author="clemensv;sethm"/>

# <a name="overview-of-service-bus-transaction-processing"></a>Vue d’ensemble de traitement des transactions Bus des services

Cet article décrit les fonctionnalités de transaction de Bus des services Azure. Grande partie de la discussion est illustré par les [Transactions atomique avec des exemples de Bus des services](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AtomicTransactions). Cet article est limité à une vue d’ensemble de traitement des transactions et la fonctionnalité *Envoyer* dans Service marché, tandis que l’échantillon Transactions atomique est plus large et plus complexes dans l’étendue.

## <a name="transactions-in-service-bus"></a>Transactions dans marché de Service

Une [transaction](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AtomicTransactions#what-are-transactions) regroupe deux ou plusieurs opérations ensemble dans une *portée d’exécution*. Par nature, une telle transaction devez vous assurer que toutes les opérations appartenant à un groupe donné d’opérations de réussissent ou l’échouent conjointement. À ce sujet transactions ou la définir comme une seule unité, souvent appelée *atomicité*. 

Bus des services est un agent de message transactions et garantit l’intégrité des transactions pour toutes les opérations internes par rapport à sa banques de messages. Tous les transferts de messages à l’intérieur de Bus des services, tels que le déplacement des messages vers une [file d’attente suspendue](service-bus-dead-letter-queues.md) ou le [transfert automatique](service-bus-auto-forwarding.md) des messages entre entités, sont transactions. Dès lors, si le Service Bus accepte un message, il déjà stocké et intitulée avec un numéro de séquence. Dès lors, les transferts de messages au sein de Bus des services sont coordonnées opérations entre les entités et ouvrira aucune perte (établie source et cible échoue) ou duplication (échoue source et cible a réussi) du message.

Bus des services prend en charge les opérations de regroupement par rapport à une seule entité messagerie (file d’attente, rubrique, abonnement) dans le cadre d’une transaction. Par exemple, vous pouvez envoyer plusieurs messages à une file d’attente à partir d’une étendue de transaction et les messages ne sera validées dans le journal de la file d’attente lorsque l’opération terminée.

## <a name="operations-within-a-transaction-scope"></a>Opérations au sein d’une étendue de transaction 

Les opérations qui peuvent être exécutées au sein d’une étendue de transaction sont comme suit :

- ** [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx), [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx), [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx)**: envoyer, SendAsync, SendBatch, SendBatchAsync 

- **[BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx)**: terminée, CompleteAsync, Abandon, AbandonAsync, lettres mortes, DeadletterAsync, différer, DeferAsync, RenewLock, RenewLockAsync 

Recevoir des opérations ne sont pas incluses, car il est supposé égal acquisition de l’application par messages en utilisant le mode [ReceiveMode.PeekLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) , à l’intérieur de certains reçoivent boucle ou avec un [OnMessage](https://msdn.microsoft.com/library/azure/dn369601.aspx) rappel et seulement une étendue de transaction de traitement des messages s’ouvre.

La disposition du message (terminé, abandon, suspendue, différer) se produit alors dans le cadre et en fonction dans les résultats de la transaction globaux.

## <a name="transfers-and-send-via"></a>Transferts et « envoyer »

Pour activer les transactions transfert de données à partir d’une file d’attente à un processeur, puis à une autre file d’attente, Bus des services prend en charge les *transferts*. Dans une opération de transfert, un expéditeur envoie tout d’abord un message à un « file d’attente transfert » et la file d’attente de transfert place immédiatement le message dans la file d’attente de destination prévue à l’aide de la même implémentation de transfert robuste qui dépend de la fonctionnalité de transfert automatique. Le message n’est jamais validé au journal de la file d’attente transfert de sorte qu’il sera visible par les utilisateurs de la file d’attente transfert.

La puissance de cette fonctionnalité transactions devient évident lorsque la file d’attente de transfert lui-même est la source des messages d’entrée de l’expéditeur. En d’autres termes, Bus de Service peut transférer le message dans la file d’attente de destination « par « la file d’attente transfert, tout en effectuant un complète (ou différer, ou suspendue) sur le message d’entrée, tout en une seule opération atomique. 

### <a name="see-it-in-code"></a>Voir dans le code

Pour configurer ces transferts, vous créez un expéditeur du message qui cible la file d’attente de destination via la file d’attente de transfert. Vous disposez également d’un récepteur qui extrait les messages de cette même file d’attente. Par exemple :

```
var sender = this.messagingFactory.CreateMessageSender(destinationQueue, myQueueName);
var receiver = this.messagingFactory.CreateMessageReceiver(myQueueName);
```

Une transaction simple puis utilise ces éléments, comme dans l’exemple suivant :

```
var msg = receiver.Receive();

using (scope = new TransactionScope())
{
    // Do whatever work is required 

    var newmsg = ... // package the result 

    msg.Complete(); // mark the message as done
    sender.Send(newmsg); // forward the result

    scope.Complete(); // declare the transaction done
} 
```

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants pour plus d’informations sur les files d’attente Bus des services :

- [Chaîne entités Bus des services avec transfert automatique](service-bus-auto-forwarding.md)
- [Exemple de transfert automatique](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AutoForward)
- [Transactions atomiques avec des exemples de Bus des services](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AtomicTransactions)
- [Azure files d’attente et Bus des services files d’attente par rapport](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
- [Comment utiliser les files d’attente Bus des services](service-bus-dotnet-get-started-with-queues.md)