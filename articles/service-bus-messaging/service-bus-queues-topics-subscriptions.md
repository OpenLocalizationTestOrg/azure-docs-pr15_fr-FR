<properties 
    pageTitle="Files d’attente Bus des services, rubriques et abonnements | Microsoft Azure"
    description="Vue d’ensemble du Service Bus entités de messagerie."
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
    ms.date="10/14/2016"
    ms.author="sethm" />

# <a name="service-bus-queues-topics-and-subscriptions"></a>Abonnements, rubriques et files d’attente Bus des services

Bus des services Microsoft Azure prend en charge un ensemble de technologies d’assistance sur le nuage, logiciels message intermédiaires orientée, y compris MSMQ fiable et résistant publication/abonnement de messagerie. Ces fonctionnalités de messagerie traitées pouvant considérer comme découplé fonctionnalités prise en charge de publication d’abonnement de messagerie, temporelles découplage et scénarios à l’aide de la messagerie TISSU le Bus des services d’équilibrage de charge. Communication découplée offre de nombreux avantages ; par exemple, clients et les serveurs peuvent se connecter selon vos besoins et effectuer les opérations de manière asynchrone.

Les entités de messagerie qui constituent le cœur des fonctionnalités de messagerie traités dans Service marché sont files d’attente, les rubriques/abonnements et les règles/actions.

## <a name="queues"></a>Files d’attente

Files d’attente offrent First In, remise des messages à un ou plusieurs consommateurs concurrents premier arrière (FIFO). Autrement dit, les messages sont généralement prévus devant être reçus et traités par les destinataires dans l’ordre dans lequel ils ont été ajoutés à la file d’attente, et chaque message est reçue et traitée par consommateur qu’un seul message. Des principaux avantages de l’utilisation des files d’attente sont d’obtenir « découplage temporel » des composants d’application. En d’autres termes, le producteur (expéditeurs) et les consommateurs (récepteurs) est inutile d’envoi et la réception des messages en même temps, étant donné que les messages sont stockés durablement dans la file d’attente. En outre, le producteur n’a pas à attendre une réponse du consommateur afin de continuer à traiter et envoyer des messages.

Avantage connexe est « charger l’audit, » qui permet de producteur et les consommateurs envoyer et recevoir des messages à des taux différents. Dans de nombreuses d’applications, les variations de la charge système dans le temps ; Toutefois, le temps de traitement requis pour chaque unité de travail est généralement constant. Echanger producteur de message et les consommateurs avec une file d’attente signifie que l’application consommateur doit uniquement être configuré pour être en mesure de gérer la charge moyenne au lieu de pointe. La profondeur de la file d’attente s’agrandit et contrats comme varie en fonction de la charge entrante. Cela directement économise de l’argent en ce qui concerne la quantité d’infrastructure requise pour la charge de l’application de service. À mesure que la charge augmente, plus les processus de travail peuvent être ajoutées à lire à partir de la file d’attente. Chaque message est traité par l’un des processus de travail. Par ailleurs, cette extraction d’équilibrage de charge permet pour une utilisation optimale des ordinateurs collaborateur même si les ordinateurs collaborateur diffèrent en ce qui concerne la puissance de traitement, comme elles extrait les messages à leur propre vitesse maximale. Ce modèle est souvent appelé le modèle « en patinage consommateur ».

À l’aide de files d’attente et intermédiaire entre les consommateurs et producteur de message fournit un intrinsèque à couplage faible entre les composants. Car producteur et consommateurs ne connaissent pas uns des autres, un consommateur peut être mis à niveau sans avoir une incidence sur le producteur.

Créer une file d’attente est un processus en plusieurs étapes. Vous effectuez des opérations de gestion Bus des services de messagerie entités (files d’attente et rubriques) via la classe [Microsoft.ServiceBus.NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) , qui est construite en fournissant l’adresse de base de l’espace de noms Bus des services et les informations d’identification utilisateur. [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) fournit des méthodes pour créer, énumérer et supprimer des entités de messagerie. Après avoir créé un objet [Microsoft.ServiceBus.TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) à partir du nom de sa et clé et un objet de gestion des espace de noms de service, vous pouvez utiliser la méthode [Microsoft.ServiceBus.NamespaceManager.CreateQueue](https://msdn.microsoft.com/library/azure/hh293157.aspx) pour créer la file d’attente. Par exemple :

```
// Create management credentials
TokenProvider credentials = TokenProvider. CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
// Create namespace client
NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);
```

Vous pouvez ensuite créer un objet file d’attente et une usine de messagerie avec l’URI Bus Service en tant qu’argument. Par exemple :

```
QueueDescription myQueue;
myQueue = namespaceClient.CreateQueue("TestQueue");
MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials); 
QueueClient myQueueClient = factory.CreateQueueClient("TestQueue");
```

Vous pouvez envoyer des messages à la file d’attente. Par exemple, si vous disposez d’une liste de messages traités appelé `MessageList`, le code doit ressembler à ce qui suit :

```
for (int count = 0; count < 6; count++)
{
    var issue = MessageList[count];
    issue.Label = issue.Properties["IssueTitle"].ToString();
    myQueueClient.Send(issue);
}
```

Vous pouvez ensuite recevoir des messages à partir de la file d’attente, comme suit :

```
while ((message = myQueueClient.Receive(new TimeSpan(hours: 0, minutes: 0, seconds: 5))) != null)
    {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();

        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
    }
```

Dans le mode [ReceiveAndDelete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) , l’opération de réception est coup ; Autrement dit, lorsque Bus des services reçoit la requête, il marque le message comme étant consommées et retourne à l’application. Le mode [ReceiveAndDelete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) très simple et convient le mieux pour les scénarios dans lesquels l’application peut tolérer ne pas traitement d’un message en cas de panne. Pour mieux comprendre cela, envisagez un scénario dans lequel le consommateur envoie la demande de réception, puis se bloque avant de le traiter. Étant donné que Service Bus marque le message comme étant consommées, lorsque l’application redémarre et commence à lire des messages à nouveau, il sera ai manqués le message a été consommé avant l’incident.

En mode [PeekLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) , l’opération de réception devient deux étages, ce qui permet aux applications de prise en charge qui ne peut pas tolérer messages manquants. Quand Bus des services reçoit la demande, il recherche le message suivant à consommer, verrous pour empêcher d’autres utilisateurs de recevoir et renvoie à l’application. Une fois l’application fini de traiter le message (ou qu’il stocke fiable d’un traitement ultérieur), il termine la deuxième étape du processus de réception en appelant [achevé](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) sur le message reçu. Lorsque Service Bus voit la [achevé](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) appeler, il marque le message comme est utilisé.

Si l’application est impossible de traiter le message pour une raison quelconque, il peut appeler la méthode [Abandon](https://msdn.microsoft.com/library/azure/hh181837.aspx) sur le message reçu (au lieu [achevé](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)). Cela permet de Bus des services déverrouiller le message et rendez-le disponible pour être reçus, par le même consommateur ou par un autre consommateur concurrent. Ensuite, un délai d’associé au verrou et si l’application ne parvient pas à traiter le message avant le verrou expiration (par exemple, si l’application se bloque), puis sur Service Bus déverrouiller le message et rend disponible pour être reçus à nouveau (pour l’essentiel en effectuant une opération [abandonner](https://msdn.microsoft.com/library/azure/hh181837.aspx) par défaut).

Notez que dans l’éventualité où l’application se bloque après traitement du message, mais avant que la requête [achevé](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) est émise, le message est redistribué à l’application lorsqu’il redémarre. Cette option est souvent appelée *Au moins une fois *une transformation ; Autrement dit, chaque message est traité au moins une fois. Toutefois, dans certaines situations le même message peut être redistribué. Si le scénario ne peut pas accepte traitement en double, puis logique supplémentaire est requise dans l’application de détecter les doublons qui peuvent être atteintes en fonction de la propriété **MessageId** du message, ce qui reste constante pour tous les tentatives de remise. Il s’agit *Exactement une fois* de traitement.

Pour plus d’informations et obtenir un exemple montrant comment créer et envoyer des messages vers et depuis files d’attente, voir le [Service Bus demandée messagerie .NET didacticiel](service-bus-brokered-tutorial-dotnet.md).

## <a name="topics-and-subscriptions"></a>Rubriques et abonnements

Contrairement à des files d’attente, dans lequel chaque message est traité par un seul consommateur, *rubriques* et *abonnements* fournissent un formulaire un-à-plusieurs de communication, dans un modèle de *publication et d’abonnement* . Utile pour suivre l’évolution à un grand nombre de destinataires, chaque message publié est rendu disponible à chaque abonnement enregistré auprès de la rubrique. Messages sont envoyés à un sujet et remis à une ou plusieurs abonnements associés, en fonction des règles de filtrage pouvant être définies sur une base par abonnement. Les abonnements peuvent utiliser des filtres supplémentaires pour limiter les messages qu’il souhaite recevoir. Les messages sont envoyés à un sujet de la même façon qu’ils sont envoyés à une file d’attente, mais les messages ne sont pas reçues directement à partir de la rubrique. En revanche, ils sont reçus d’abonnements. Un abonnement rubrique ressemble à une file d’attente virtuelle qui reçoit une copie des messages qui sont envoyés à la rubrique. Les messages sont reçus à partir d’un abonnement identique à la façon dont ils sont reçus à partir d’une file d’attente.

Titre de comparaison, la fonctionnalité d’envoi de messages d’une file d’attente mappe directement sur un sujet et ses fonctionnalités de réception des messages correspond à un abonnement. Entre autres choses, cela signifie que les abonnements prennent en charge les mêmes modèles décrites précédemment dans cette section en ce qui concerne les files d’attente : consommateur concurrent, découplage temporel, nivellement de chargement et l’équilibrage de charge.

Création d’une rubrique est similaire à la création d’une file d’attente, comme le montre l’exemple dans la section précédente. Créer l’URI de service et ensuite utiliser la classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) pour créer le client de l’espace de noms. Vous pouvez ensuite créer une rubrique à l’aide de la méthode [CreateTopic](https://msdn.microsoft.com/library/azure/hh293080.aspx) . Par exemple :

```
TopicDescription dataCollectionTopic = namespaceClient.CreateTopic("DataCollectionTopic");
```

Ensuite, ajoutez des abonnements comme vous le souhaitez :

```
SubscriptionDescription myAgentSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Inventory");
SubscriptionDescription myAuditSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Dashboard");
```

Vous pouvez ensuite créer un client rubrique. Par exemple :

```
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider);
TopicClient myTopicClient = factory.CreateTopicClient(myTopic.Path)
```

À l’aide de l’expéditeur du message, vous pouvez envoyer et recevoir des messages vers et à partir de la rubrique, comme indiqué dans la section précédente. Par exemple :

```
foreach (BrokeredMessage message in messageList)
{
    myTopicClient.Send(message);
    Console.WriteLine(
    string.Format("Message sent: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

Similaires aux files d’attente, les messages sont reçus à partir d’un abonnement à l’aide d’un objet [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx) au lieu d’un objet [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) . Créer le client de l’abonnement, en passant le nom de la rubrique, le nom de l’abonnement et le mode de réception (le cas échéant) en tant que paramètres. Par exemple, avec l’abonnement **inventaire** :

```
// Create the subscription client
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider); 

SubscriptionClient agentSubscriptionClient = factory.CreateSubscriptionClient("IssueTrackingTopic", "Inventory", ReceiveMode.PeekLock);
SubscriptionClient auditSubscriptionClient = factory.CreateSubscriptionClient("IssueTrackingTopic", "Dashboard", ReceiveMode.ReceiveAndDelete); 

while ((message = agentSubscriptionClient.Receive(TimeSpan.FromSeconds(5))) != null)
{
    Console.WriteLine("\nReceiving message from Inventory...");
    Console.WriteLine(string.Format("Message received: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
    message.Complete();
}          

// Create a receiver using ReceiveAndDelete mode
while ((message = auditSubscriptionClient.Receive(TimeSpan.FromSeconds(5))) != null)
{
    Console.WriteLine("\nReceiving message from Dashboard...");
    Console.WriteLine(string.Format("Message received: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

### <a name="rules-and-actions"></a>Règles et actions

Dans de nombreux scénarios, les messages qui ont des caractéristiques spécifiques doivent être traités de différentes manières. Pour ce faire, vous pouvez configurer des abonnements pour rechercher les messages qui ont besoin de propriétés, puis effectuez certaines modifications aux propriétés. Bien que les abonnements Bus des services afficher tous les messages envoyés à la rubrique, vous pouvez copier uniquement un sous-ensemble de ces messages dans la file d’attente virtuelle abonnement. Cela revient à l’aide de filtres de l’abonnement. Ces modifications sont appelées des *actions de filtrage*. Lorsqu’un abonnement est créé, vous pouvez fournir une expression de filtre qui fonctionne sur les propriétés du message, les propriétés système (par exemple, **étiquette**) et les propriétés d’une application personnalisée (par exemple, **StoreName**.) L’expression de filtre SQL est facultative dans ce cas ; sans une expression de filtre SQL, toute action de filtre définie sur un abonnement à effectuer sur tous les messages pour cet abonnement.

À l’aide de l’exemple précédent, pour filtrer les messages provenant d' **banque 1**, vous devez créer l’abonnement de tableau de bord comme suit :

```
namespaceManager.CreateSubscription("IssueTrackingTopic", "Dashboard", new SqlFilter("StoreName = 'Store1'"));
```

Avec ce filtre abonnement en place, seuls les messages qui ont le `StoreName` propriété valeur `Store1` sont copiés dans la file d’attente virtuelle pour le `Dashboard` abonnement.

Pour plus d’informations sur les valeurs de filtre possibles, consultez la documentation pour les classes [SqlFilter](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx) et [SqlRuleAction](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlruleaction.aspx) . Consultez également les [demandé de messagerie : filtres avancés](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749) et des exemples de [Filtres rubrique](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters) .

## <a name="next-steps"></a>Étapes suivantes

Consultez la rubrique suivante rubriques pour plus d’informations et exemples d’utilisation de Bus des services demandé de messagerie entités avancées.

- [Vue d’ensemble de la messagerie Bus des services](service-bus-messaging-overview.md)
- [Bus des services demandé de messagerie .NET didacticiel](service-bus-brokered-tutorial-dotnet.md)
- [Bus des services demandé de messagerie reste didacticiel](service-bus-brokered-tutorial-rest.md)
- [Exemple de filtres rubrique](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters)
- [Messagerie traitée : Avancée des exemples de filtres](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749)

