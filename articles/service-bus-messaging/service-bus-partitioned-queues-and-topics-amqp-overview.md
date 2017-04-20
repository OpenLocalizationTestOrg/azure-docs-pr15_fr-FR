<properties 
    pageTitle="Prise en charge de AMQP 1.0 Bus des services partition files d’attente et rubriques | Microsoft Azure" 
    description="Découvrez comment utiliser les files d’attente avancé Message Queuing Protocol (AMQP) 1.0 à Service partitionnée et les rubriques." 
    services="service-bus" 
    documentationCenter=".net" 
    authors="hillaryc" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="10/14/2016" 
    ms.author="hillaryc;sethm"/>

# <a name="amqp-10-support-for-service-bus-partitioned-queues-and-topics"></a>Prise en charge de AMQP 1.0 Bus des services partition files d’attente et rubriques 

Bus des services Azure prend désormais en charge l’avancées Message Queuing Protocol (**AMQP**) 1.0 Bus des services **partition files d’attente et rubriques.**

**AMQP** est un protocole files d’attente message Ouvrir standard qui vous permet de développer des applications de disponibilité sur plusieurs plateformes à l’aide de différents langages de programmation. Pour des informations générales sur AMQP prise en charge dans Service marché, consultez [AMQP 1.0 prend en charge dans Service marché](service-bus-amqp-overview.md).

**Partitionnées files d’attente et les rubriques**, également appelés *entités partitionnées*, offrent des disponibilité, la fiabilité et débit que files d’attente non partitions conventionnelles et les rubriques plus élevées. Pour plus d’informations sur les entités partitionnées, voir [Partitionnée entités de messagerie](service-bus-partitioning.md).

L’ajout de AMQP 1.0 en tant que protocole pour communiquer avec des rubriques et partitionnées files d’attente vous permet de vous permettent de créer des applications interopérabilitées qui peuvent tirer parti de la disponibilité d’une version ultérieure, la fiabilité et tout au long offertes par entités Bus Service partition.

Pour plus d’informations au niveau du câble AMQP 1.0 protocole, qui explique comment Bus des services mettent en œuvre et s’appuie sur la spécification technique OASIS AMQP, voir le [guide de protocole AMQP 1.0 dans Bus des services Azure et Hubs événement](service-bus-amqp-protocol-guide.md).    

## <a name="use-amqp-with-partitioned-queues"></a>Utiliser AMQP avec partitionnée files d’attente

Files d’attente sont utiles pour des scénarios qui requièrent découplage temporel, chargement de nivellement, l’équilibrage de charge et couplage faible. Une file d’attente, éditeurs envoient des messages dans la file d’attente et consommateurs recevoir des messages à partir de la file d’attente, dans les cas où un message peut être reçu uniquement une fois. Un bon exemple est un système d’inventaire dans lequel terminaux point de vente publier des données dans une file d’attente plutôt que directement à votre système de gestion des stocks. Le système de gestion des stocks consomme puis les données à tout moment pour gérer réapprovisionnement de stock. Cela présente plusieurs avantages, y compris le système de gestion des stocks sans avoir à être en ligne à tout moment. Pour plus d’informations sur les files d’attente Bus des services, consultez [créer d’applications qui utilise des files d’attente Bus des services](service-bus-create-queues.md). 

Une file d’attente partitionnée davantage augmente la disponibilité, la fiabilité et le débit pour les applications, ces files d’attente sont répartis sur plusieurs agents de message et banques de messagerie.     

### <a name="create-partitioned-queues"></a>Créer des files d’attente partitionnées

Vous pouvez créer une file d’attente partitionnée à l’aide du [portail classique Azure][] et le Kit de développement de Bus de Service. Pour créer une file d’attente partitionnée, définissez la propriété [EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx) sur **true** dans l’instance [QueueDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx) . Le code suivant montre comment créer une file d’attente partitionnée à l’aide du Kit de développement de Bus de Service. 
 
```
// Create partitioned queue
var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
var queueDescription = new QueueDescription("myQueue");
queueDescription.EnablePartitioning = true;
nm.CreateQueue(queueDescription);
```

### <a name="send-and-receive-messages-using-amqp"></a>Envoyer et recevoir des messages à l’aide de AMQP

Vous pouvez envoyer des messages vers et recevoir des messages à partir d’une file d’attente partitionnée à l’aide de AMQP comme protocole, en définissant la propriété de [type de transport](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.transporttype.aspx) [TransportType.Amqp](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transporttype.aspx) comme indiqué dans le code suivant.  

```
// Sending and receiving messages to and from a queue
var myConnectionStringBuilder = new ServiceBusConnectionStringBuilder(myConnectionString);
myConnectionStringBuilder.TransportType = TransportType.Amqp;
string amqpConnectionString = myConnectionStringBuilder.ToString();
var queueClient = QueueClient.CreateFromConnectionString(amqpConnectionString, "myQueue");

BrokeredMessage message = new BrokeredMessage("Hello AMQP");
Console.WriteLine("Sending message {0}...", message.MessageId);
queueClient.Send(message);

var receivedMessage = queueClient.Receive();
Console.WriteLine("Received message: {0}", receivedMessage.GetBody<string>());
receivedMessage.Complete();
```

## <a name="use-amqp-with-partitioned-topics"></a>Utiliser AMQP avec rubriques partitionnées

Rubriques conceptuel similaires aux files d’attente, mais rubriques peuvent router une copie du même message à plusieurs *abonnements*. Dans une rubrique éditeurs envoient des messages à la rubrique et consommateurs recevoir des messages d’abonnements. Dans le scénario de point de vente système stock, terminaux publier des données à la rubrique. Le système de gestion des stocks puis reçoit des messages à partir d’un abonnement. En outre, un système de surveillance peut recevoir le même message à partir d’un autre abonnement. Pour plus d’informations sur les abonnements et les rubriques Bus des services, consultez [créer d’applications qui utilise des abonnements et les rubriques de Bus des services](service-bus-create-topics-subscriptions.md). 

Comme avec files d’attente, sujets partitionnées augmentent la disponibilité, la fiabilité et le débit pour les applications, comme ces rubriques et leurs abonnements sont répartis sur plusieurs agents de message et banques de messagerie. 

### <a name="create-partitioned-topics"></a>Créer des rubriques partitionnées

Vous pouvez créer une rubrique partitionnée à l’aide du [portail classique Azure][] et le Kit de développement de Bus de Service. Pour créer un sujet partitionné, définissez la propriété [EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablepartitioning.aspx) sur **true** dans l’instance [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx) . Le code suivant montre comment créer une rubrique partitionnée en utilisant le Kit de développement de Bus de Service.
    
```
// Create partitioned topic
var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
var topicDescription = new TopicDescription("myTopic");
topicDescription.EnablePartitioning = true;
nm.CreateTopic(topicDescription);

var subscriptionDescription = new SubscriptionDescription("myTopic", "mySubscription");
nm.CreateSubscription(subscriptionDescription);
```

### <a name="send-and-receive-messages-using-amqp"></a>Envoyer et recevoir des messages à l’aide de AMQP

Vous pouvez envoyer des messages vers et recevoir des messages à partir d’un abonnement rubrique partitionnées à l’aide de AMQP en tant que protocole, en définissant la propriété de [type de transport](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.transporttype.aspx) [TransportType.Amqp](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transporttype.aspx), comme indiqué dans le code suivant.  

```
// Sending and receiving messages to a topic and from a subscription
var myConnectionStringBuilder = new ServiceBusConnectionStringBuilder(myConnectionString);
myConnectionStringBuilder.TransportType = TransportType.Amqp;
string amqpConnectionString = myConnectionStringBuilder.ToString();
    
var topicClient = TopicClient.CreateFromConnectionString(amqpConnectionString, "myTopic");
BrokeredMessage message = new BrokeredMessage("Hello AMQP");
Console.WriteLine("Sending message {0}...", message.MessageId);
topicClient.Send(message);
    
var subcriptionClient = SubscriptionClient.CreateFromConnectionString(amqpConnectionString, "myTopic", "mySubscription");
var receivedMessage = subcriptionClient.Receive();
Console.WriteLine("Received message: {0}", receivedMessage.GetBody<string>());
receivedMessage.Complete();
```

## <a name="next-steps"></a>Étapes suivantes

Consultez les informations supplémentaires suivantes pour en savoir plus sur entités messageries partitionnées ainsi que AMQP.

*    [Entités messageries partitionnées](service-bus-partitioning.md)
*    [OASIS Advanced Message Queuing Protocol (AMQP), Version 1.0](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
*    [Prise en charge de AMQP 1.0 dans marché de Service](service-bus-amqp-overview.md)
*    [1.0 AMQP Guide de protocole Bus des services Azure et Hubs d’événement](service-bus-amqp-protocol-guide.md)
*    [L’utilisation de l’API Java Message Service (JMS) avec Bus des services et AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md)
*    [Comment utiliser AMQP 1.0 avec l’API .NET Bus du Service](service-bus-dotnet-advanced-message-queuing.md)

[Portail classique Azure]: http://manage.windowsazure.com
