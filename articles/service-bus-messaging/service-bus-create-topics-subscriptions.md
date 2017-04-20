<properties 
    pageTitle="Créer des applications qui utilisent des rubriques Bus des services et abonnements | Microsoft Azure"
    description="Présentation de la publier-s’abonner capacités offertes par les abonnements et les rubriques Bus des services."
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

# <a name="create-applications-that-use-service-bus-topics-and-subscriptions"></a>Créer des applications qui utilisent des abonnements et rubriques Bus des services

Bus des services Azure prend en charge un ensemble de technologies de personnalisées sur le nuage, orientés message, y compris MSMQ fiable et résistant publication/abonnement de messagerie. Cet article s’appuie sur les informations fournies dans les [applications de création qui utilisent des files d’attente Bus des services](service-bus-create-queues.md) et offre une introduction pour les fonctionnalités de publication et d’abonnement offertes par rubriques Bus des services.

## <a name="evolving-retail-scenario"></a>Évolution scénario de vente au détail

Cet article continue le scénario de vente au détail utilisé dans les [applications de création qui utilisent des files d’attente Bus des services](service-bus-create-queues.md). Rappeler ces données à partir de chaque Point de vente terminaux doivent être routés vers un système de gestion des stocks qui utilise ces données pour déterminer quand stock doit être réapprovisionnés ventes. Chaque TPV signale ses données commerciales en envoyant des messages dans la file d’attente **DataCollectionQueue** , où ils restent jusqu'à ce qu’elles sont reçues par le système de gestion des stocks, comme illustré ici :

![Service Bus 1](./media/service-bus-create-topics-subscriptions/IC657161.gif)

Pour évoluer ce scénario, une demande a été ajoutée au système : le propriétaire du magasin souhaite être en mesure de surveiller les performances de la banque en temps réel.

Pour répondre à cette exigence, le système doit « appuyez sur « Désactiver le flux de données de ventes. Nous voulons toujours chaque message envoyé par les TPV soient envoyées par le système de gestion des stocks comme avant, mais nous voulons une autre copie de chaque message que nous pouvons utiliser pour présenter l’affichage tableau de bord au propriétaire d’un magasin.

Dans n’importe quelle situation telles que, dans lequel vous avez besoin de chaque message à consommer par de nombreuses parties, vous pouvez utiliser Service Bus *rubriques*. Rubriques fournissent un motif publier/abonner dans lequel chaque message publié est rendu disponible pour un ou plusieurs abonnements enregistrés auprès de la rubrique. En revanche, files d’attente de chaque message est reçu par un seul consommateur.

Messages sont envoyés à un sujet de la même façon qu’elles sont envoyées à une file d’attente. Toutefois, les messages ne sont pas reçues à partir de la rubrique directement ; ils sont reçus d’abonnements. Vous pouvez considérer un abonnement à un sujet de file d’attente virtuelle qui reçoit une copie des messages qui sont envoyés à ce sujet. Les messages provenant d’un abonnement la même façon telles qu’elles sont reçues à partir d’une file d’attente.

Pour revenir au scénario de vente au détail, la file d’attente est remplacée par une rubrique et un abonnement est ajouté, ce qui permet du composant système de gestion des stocks. Le système se présente comme suit :

![Service Bus 2](./media/service-bus-create-topics-subscriptions/IC657165.gif)

La configuration ici effectue identique à la conception précédente basée sur la file d’attente. Autrement dit, les messages envoyés à la rubrique sont routés vers l’abonnement **stock** , à partir de laquelle le **Système de gestion des stocks** les consomme.

Afin de prendre en charge le tableau de bord de gestion, nous créons un deuxième abonnement dans la rubrique, comme illustré ici :

![Service Bus 3](./media/service-bus-create-topics-subscriptions/IC657166.gif)

Avec cette configuration, chaque message à partir des TPV est accessibles aux abonnements le **tableau de bord** et de **l’inventaire** .

## <a name="show-me-the-code"></a>Afficher le code

L’article [créer applications qui utilisent des files d’attente Service Bus](service-bus-create-queues.md) décrit comment s’inscrire à un compte Azure et créer un espace de noms de service. Pour utiliser un espace de noms Bus des services, une application doit faire référence l’assembly Bus des services, en particulier Microsoft.ServiceBus.dll. Pour référencer les dépendances Bus des services, le plus simple consiste à installer le Service Bus [package Nuget](https://www.nuget.org/packages/WindowsAzure.ServiceBus/). Vous pouvez également trouver l’assembly dans le cadre du Kit de développement Azure. Le téléchargement est disponible dans la [page de téléchargement Azure SDK](https://azure.microsoft.com/downloads/).

### <a name="create-the-topic-and-subscriptions"></a>Créer le sujet et les abonnements

Opérations de gestion des Bus des services de messagerie entités (rubriques files d’attente et publication/abonnement) sont effectuées via la classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) . Informations d’identification appropriées sont nécessaires pour créer une instance [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) pour un espace de noms particulier. Bus des services utilise un modèle de sécurité en fonction de [Signature accès partagé (sa)](service-bus-sas-overview.md) . La classe [TokenProvider a](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) représente un fournisseur de jeton de sécurité avec méthodes usine intégrée renvoyant certains fournisseurs jetons connus. Nous allons utiliser une méthode [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) pour stocker les informations d’identification associations de sécurité. L’instance [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) puis formule a été créée avec l’adresse de base de l’espace de noms Bus des services et le fournisseur de jetons.

La classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) fournit des méthodes pour créer, énumérer et supprimer des entités de messagerie. Le code est indiqué ici montre comment l’instance [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) est créé et utilisée pour créer la rubrique **DataCollectionTopic** .

```
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";
     
TokenProvider tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = new NamespaceManager(uri, tokenProvider);
 
namespaceManager.CreateTopic("DataCollectionTopic");
```

Notez qu’il existe des surcharges de la méthode [CreateTopic](https://msdn.microsoft.com/library/azure/hh293080.aspx) qui vous permettent de définir les propriétés de la rubrique. Par exemple, vous pouvez définir la valeur par défaut time to live (TTL) pour les messages envoyés à la rubrique. Ensuite, ajoutez les abonnements **inventaire** et **tableau de bord** .

```
namespaceManager.CreateSubscription("DataCollectionTopic", "Inventory");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard");
```

### <a name="send-messages-to-the-topic"></a>Envoyer des messages à la rubrique

Pour les opérations d’exécution sur entités Bus des services ; par exemple, envoyez et recevez des messages, une application doit tout d’abord créer un objet [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) . Similaire à la classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) , l’instance [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) est créé à partir de l’adresse de base de l’espace de noms de service et le fournisseur de jetons.

```
MessagingFactory factory = MessagingFactory.Create(uri, tokenProvider);
```

Pour les messages envoyés et reçus à partir de rubriques Bus des services, sont instances de la classe [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) . Ce cours est constitué d’un ensemble de propriétés standard (par exemple, [étiquette](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) et [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), un dictionnaire est utilisé pour stocker des propriétés de l’application et un corps de données d’application arbitraire. Une application peut définir le corps en passant tout objet sérialisable (l’exemple suivant passe dans un objet **SalesData** représentant les données de ventes de la TPV), qui seront utilisés par le [DataContractSerializer](https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx) pour sérialiser l’objet. Par ailleurs, un objet [Stream](https://msdn.microsoft.com/library/azure/system.io.stream.aspx) peut être fourni.

```
BrokeredMessage bm = new BrokeredMessage(salesData);
bm.Label = "SalesReport";
bm.Properties["StoreName"] = "Redmond";
bm.Properties["MachineID"] = "POS_1";
```

Pour envoyer des messages à la rubrique, le plus simple consiste à utiliser [CreateMessageSender](https://msdn.microsoft.com/library/azure/hh322659.aspx) pour créer un objet [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) directement à partir de l’instance [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) .

```
MessageSender sender = factory.CreateMessageSender("DataCollectionTopic");
sender.Send(bm);
```

### <a name="receive-messages-from-a-subscription"></a>Recevoir des messages à partir d’un abonnement

Semblable à l’utilisation de files d’attente, pour recevoir des messages à partir d’un abonnement que vous pouvez utiliser un objet [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) que vous créez directement à partir de la [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) à l’aide de [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx). Vous pouvez utiliser l’une des deux différentes recevoir modes (**ReceiveAndDelete** et **PeekLock**), comme indiqué dans les [applications de création qui utilisent des files d’attente Bus des services](service-bus-create-queues.md).

Notez que lorsque vous créez un [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) pour les abonnements, le paramètre *entityPath* est de l’écran `topicPath/subscriptions/subscriptionName`. Par conséquent, pour créer un [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) pour l’abonnement de **l’inventaire** de la rubrique **DataCollectionTopic** , *entityPath* doit être définie `DataCollectionTopic/subscriptions/Inventory`. Le code apparaît comme suit :

```
MessageReceiver receiver = factory.CreateMessageReceiver("DataCollectionTopic/subscriptions/Inventory");
BrokeredMessage receivedMessage = receiver.Receive();
try
{
    ProcessMessage(receivedMessage);
    receivedMessage.Complete();
}
catch (Exception e)
{
    receivedMessage.Abandon();
}
```

## <a name="subscription-filters"></a>Filtres d’abonnement

Jusque là, dans ce scénario tous les messages envoyés à la rubrique sont accessibles à tous les abonnements enregistrés. L’expression clé ici est « disposition. » Bien que les abonnements Bus des services afficher tous les messages envoyés à la rubrique, vous pouvez copier uniquement un sous-ensemble de ces messages dans la file d’attente virtuelle abonnement. Cette opération est effectuée à l’aide de *filtres*d’abonnement. Lorsque vous créez un abonnement, vous pouvez fournir une expression de filtre dans l’écran d’un prédicat de style SQL92 qui fonctionne avec les propriétés du message, les propriétés du système (par exemple, [étiquette](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx)) et les propriétés de l’application, tel que **StoreName** dans l’exemple précédent.

Voyez-vous le scénario afin d’illustrer cela, une deuxième banque doit être ajouté à notre scénario de vente au détail. Toutes les TPV les deux bases de données de ventes doivent toujours être routés vers le système de gestion centralisée stock, mais un responsable store à l’aide de l’outil de tableau de bord est uniquement les performances de cette banque d’informations. Vous pouvez utiliser abonnement filtrage pour y parvenir. Notez que lorsque les TPV publiez des messages, ils définissent la propriété application **StoreName** sur le message. Les TPV dans la Redmond donné deux magasins, par exemple **Redmond** et **Seattle**, stockent cachet leurs messages de données de ventes avec un **StoreName** égal à **Redmond**, tandis que les TPV du store Seattle utilisent un **StoreName** égal à **Seattle**. Le Gestionnaire de magasin de la banque Redmond souhaite uniquement afficher des données à partir de son TPV. Le système se présente comme suit :

![Service Bus4](./media/service-bus-create-topics-subscriptions/IC657167.gif)

Pour configurer ce routage, créez l’abonnement de **tableau de bord** comme suit :

```
SqlFilter dashboardFilter = new SqlFilter("StoreName = 'Redmond'");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard", dashboardFilter);
```

Avec ce filtre abonnement, seulement les messages qui ont la propriété **StoreName** **Redmond** seront copiées dans la file d’attente virtuelle pour l’abonnement de **tableau de bord** . Il est beaucoup plus de filtrage de l’abonnement, cependant. Applications peuvent avoir plusieurs règles de filtre par abonnement outre la possibilité de modifier les propriétés d’un message lorsqu’il passe à file d’attente virtuelle d’un abonnement.

## <a name="summary"></a>Résumé

Toutes les raisons d’utiliser queuing décrites dans [créer des applications qui utilisent des files d’attente Bus des services](service-bus-create-queues.md) s’appliquent également aux rubriques, en particulier :

- Découplage temporel – producteur de message et les consommateurs n’ont pas à être en ligne en même temps.

- Charger l’audit – pointes dans le chargement sont faibles sont supprimés à la rubrique permettant aux applications beaucoup d’être mis en service pour charge moyenne au lieu de pointe.

- Équilibrage de charge – semblable à une file d’attente, vous pouvez avoir plusieurs consommateurs concurrents à l’écoute sur un seul abonnement dans chaque message remis à une seule des consommateurs, ce qui permet l’équilibrage de charge.

- Couplage faible – vous pouvez évoluer le réseau de messagerie sans toucher aux points de terminaison existants ; par exemple, ajouter des abonnements ou modification des filtres à une rubrique pour autoriser pour les nouveaux consommateurs.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation des files d’attente dans le scénario de vente au détail POS, voir [créer des applications qui utilisent des files d’attente Bus des services](service-bus-create-queues.md) .