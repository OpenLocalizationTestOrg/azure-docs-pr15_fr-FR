<properties 
    pageTitle="Écrire des applications qui utilisent des files d’attente Bus des services | Microsoft Azure"
    description="Comment écrire une application file d’attente simple qui utilise Bus des services."
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
    ms.date="10/03/2016"
    ms.author="sethm" />

# <a name="create-applications-that-use-service-bus-queues"></a>Créer des applications qui utilisent des files d’attente Bus des services

Cette rubrique décrit les files d’attente Bus des services et indique comment rédiger une application file d’attente simple qui utilise Bus des services.

Envisagez un scénario du monde de vente au détail dans lequel les données de vente des terminaux sale (PDV) individuels doivent être routées à un système de gestion des stocks qui utilise les données pour déterminer quand stock doit être réapprovisionnés. Cette solution utilise Bus des services de messagerie pour les communications entre les bornes et le système de gestion des stocks, comme illustré dans l’illustration suivante :

![Image de files d’attente Service Bus 1](./media/service-bus-create-queues/IC657161.gif)

Chaque TPV signale ses données commerciales en envoyant des messages à l' **DataCollectionQueue**. Ces messages restent dans cette file d’attente jusqu'à ce qu’ils sont récupérés par le système de gestion des stocks. Ce modèle est souvent appelé *messagerie asynchrone*, car la TPV n’a pas d’attendre une réponse à partir du système de gestion des stocks pour poursuivre le traitement.

## <a name="why-queuing"></a>Pourquoi queuing ?

Avant d’examiner le code requis pour configurer cette application, vous pouvez les avantages liés à l’aide d’une file d’attente dans ce scénario plutôt que de laisser les TPV parler directement (synchrone) pour le système de gestion des stocks.

### <a name="temporal-decoupling"></a>Découplage temporelle

Avec le modèle de messagerie asynchrone, producteur et les consommateurs n’ont pas à être en ligne en même temps. L’infrastructure de messagerie fiable stocke les messages jusqu'à ce que la partie (consommateur) est prête à recevoir les. Cela signifie que les composants de l’application distribuée peuvent être déconnectés, soit volontaire ; par exemple, pour la maintenance, ou en raison d’un blocage de composant, sans affecter l’ensemble du système. En outre, l’application consommateur peut-être uniquement être en ligne à certaines heures de la journée. Par exemple, dans ce scénario de vente au détail, le système de gestion des stocks peut-être uniquement à venir en ligne après la fin de la journée.

### <a name="load-leveling"></a>Nivellement de charge

Dans de nombreuses applications charge système varie au fil du temps, tandis que le temps de traitement requis pour chaque unité de travail est généralement constant. Echanger producteur de message et les consommateurs avec une file d’attente signifie que l’application consommateur (collaborateur) doit uniquement être mis en service pour une charge moyenne au lieu d’une pointe de service. La profondeur de la file d’attente s’agrandit et contrat comme varie en fonction de la charge entrante. Cela directement économise de l’argent en ce qui concerne la quantité d’infrastructure requise pour la charge de l’application de service.

![Image de files d’attente Service Bus 2](./media/service-bus-create-queues/IC657162.gif)

### <a name="load-balancing"></a>Équilibrage de charge

À mesure que la charge augmente, plus les processus de travail peuvent être ajoutées à lire à partir de la file d’attente de travail. Chaque message est traité par l’un des processus de travail. Par ailleurs, cette extraction d’équilibrage de charge permet l’utilisation optimale des ordinateurs collaborateur même si les ordinateurs collaborateur diffèrent en ce qui concerne la puissance de traitement, comme ils extrait les messages à leur propre vitesse maximale. Ce modèle est souvent appelé le modèle consommateur concurrents.

![Image de files d’attente Bus des Services 3](./media/service-bus-create-queues/IC657163.gif)

### <a name="loose-coupling"></a>Couplage faible

À l’aide de message queuing à intermédiaire entre les consommateurs et producteur de message fournit un intrinsèque couplage faible entre les composants. Car producteur et consommateurs ne connaissent pas uns des autres, un consommateur peut être mis à niveau sans avoir une incidence sur le producteur. En outre, la topologie de messagerie peut évoluer sans modifier les points de terminaison existants. Nous aborderons ce plus lorsque nous parlons de publication et d’abonnement.

## <a name="show-me-the-code"></a>Afficher le code

La section suivante montre comment utiliser Bus des services pour générer cette application.

### <a name="sign-up-for-an-azure-account"></a>S’inscrire à un compte Azure

Vous devez posséder un compte Azure afin de commencer à travailler à Service. Si vous n’en avez pas déjà, vous pouvez vous inscrire pour obtenir un compte gratuit [ici](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A85619ABF).

### <a name="create-a-namespace"></a>Créer un espace de noms

Une fois que vous avez un abonnement, vous pouvez [créer un nouvel espace de noms](service-bus-create-namespace-portal.md). Chaque espace de noms se comporte comme un conteneur portée pour un ensemble d’entités Bus des services. Donnez un nom unique à votre nouvel espace de noms sur tous les comptes de Service Bus. 

### <a name="install-the-nuget-package"></a>Installer le package NuGet

Pour utiliser l’espace de noms Bus des services, une application doit faire référence l’assembly Bus des services, en particulier Microsoft.ServiceBus.dll. Vous pouvez trouver cet assembly dans le cadre du Kit de développement Microsoft Azure et le téléchargement est disponible dans la [page de téléchargement Azure SDK](https://azure.microsoft.com/downloads/). Toutefois, le [package Service Bus NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus) est le moyen le plus simple pour obtenir de l’API Bus Service et configurer votre application avec toutes les dépendances Bus des services.

### <a name="create-the-queue"></a>Créer la file d’attente

Opérations de gestion des Bus des services de messagerie entités (rubriques files d’attente et publication/abonnement) sont effectuées via la classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) . Bus des services utilise un modèle de sécurité en fonction de [Signature accès partagé (sa)](service-bus-sas-overview.md) . La classe [TokenProvider a](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) représente un fournisseur de jeton de sécurité avec méthodes usine intégrée renvoyant certains fournisseurs jetons connus. Nous allons utiliser une méthode [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) pour stocker les informations d’identification associations de sécurité. L’instance [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) puis formule a été créée avec l’adresse de base de l’espace de noms Bus des services et le fournisseur de jetons.

La classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) fournit des méthodes pour créer, énumérer et supprimer des entités de messagerie. Le code est indiqué ici montre comment l’instance [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) est créé et utilisée pour créer la file d’attente **DataCollectionQueue** .

```
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", 
                "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";
 
TokenProvider tokenProvider = 
    TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = 
    new NamespaceManager(uri, tokenProvider);
namespaceManager.CreateQueue("DataCollectionQueue");
```

Notez qu’il existe des surcharges de la méthode [CreateQueue](https://msdn.microsoft.com/library/azure/hh322663.aspx) qui permettent les propriétés de la file d’attente à analyser. Par exemple, vous pouvez définir la valeur par défaut time to live (TTL) pour les messages envoyés à la file d’attente.

### <a name="send-messages-to-the-queue"></a>Envoyer des messages à la file d’attente

Pour les opérations d’exécution sur entités Bus des services ; par exemple, envoyez et recevez des messages, une application doit tout d’abord créer un objet [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) . Similaire à la classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) , l’instance [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) est créé à partir de l’adresse de base de l’espace de noms de service et le fournisseur de jetons.

```
 BrokeredMessage bm = new BrokeredMessage(salesData);
 bm.Label = "SalesReport";
 bm.Properties["StoreName"] = "Redmond";
 bm.Properties["MachineID"] = "POS_1";
```

Les messages envoyés à, puis reçu de Service Bus files d’attente sont des instances de la classe [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) . Ce cours est constitué d’un ensemble de propriétés standard (par exemple, [étiquette](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) et [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), un dictionnaire est utilisé pour stocker des propriétés de l’application et un corps de données d’application arbitraire. Une application peut définir le corps en passant tout objet sérialisable (l’exemple suivant passe dans un objet **SalesData** représentant les données de ventes de la TPV), qui seront utilisés par le [DataContractSerializer](https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx) pour sérialiser l’objet. Par ailleurs, un objet [Stream](https://msdn.microsoft.com/library/azure/system.io.stream.aspx) peut être fourni.

Pour envoyer des messages à une file d’attente donnée, dans notre cas la **DataCollectionQueue**, le plus simple consiste à utiliser [CreateMessageSender](https://msdn.microsoft.com/library/azure/hh322659.aspx) pour créer un objet [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) directement à partir de l’instance [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) .

```
MessageSender sender = factory.CreateMessageSender("DataCollectionQueue");
sender.Send(bm);
```

### <a name="receiving-messages-from-the-queue"></a>Recevoir des messages à partir de la file d’attente

Pour recevoir des messages à partir de la file d’attente, vous pouvez utiliser un objet [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) que vous créez directement à partir de la [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) à l’aide de [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx). Destinataires de messages peuvent fonctionner dans deux modes différents : **ReceiveAndDelete** et **PeekLock**. La [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) est définie lorsque le destinataire du message est créé, comme un paramètre à l’appel [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx) .


Lorsque vous utilisez le mode **ReceiveAndDelete** , la réception est une opération de capture d’unique ; Autrement dit, lorsque Bus des services reçoit la requête, il marque le message comme étant consommées et retourne à l’application. Le mode **ReceiveAndDelete** très simple et convient le mieux pour les scénarios dans lesquels l’application peut tolérer traitement ne pas d’un message si une défaillance. Pour mieux comprendre cela, envisagez un scénario dans lequel le consommateur envoie la demande de réception, puis se bloque avant de le traiter. Dans la mesure où Bus des services marqué le message comme étant consommées, lors du redémarrage de l’application et le démarrage lire des messages à nouveau, il sera manqués le message qui a été utilisé avant d’être le blocage.

En mode de **PeekLock** , la réception devient une opération de deux étages, ce qui permet de prendre en charge les applications qui ne peuvent tolérer messages manquants. Lorsque Service Bus reçoit la requête, il recherche le message suivant à consommer, verrous pour empêcher les autres consommateurs reçoit et renvoie à l’application. Une fois l’application fini de traiter le message (ou qu’il stocke fiable d’un traitement ultérieur), il termine la deuxième étape du processus de réception en appelant [achevé](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) sur le message reçu. Lorsque Service Bus voit la [achevé](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) appeler, il marque le message comme est utilisé.

Deux autres résultats sont possibles. Tout d’abord, si l’application est impossible de traiter le message pour une raison quelconque, il peut appeler [abandonner](https://msdn.microsoft.com/library/azure/hh181837.aspx) sur le message reçu (au lieu [achevé](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)). Dans ce cas Bus des services déverrouiller le message et rendez-le disponible pour être reçus, par le même consommateur ou par un autre consommateur fin. Ensuite, il existe un délai d’expiration associé au verrou et si l’application ne peut pas traiter le message avant du verrou expiration (par exemple, si l’application se bloque), puis Bus des services sera déverrouiller le message et rendez-le disponible perçu à nouveau (pour l’essentiel en effectuant une opération [abandonner](https://msdn.microsoft.com/library/azure/hh181837.aspx) par défaut).

Notez que si l’application se bloque après qu’il traite le message, mais avant la [achevé](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) demande a été émise, le message sera remis à nouveau à l’application lorsqu’il redémarre. Il est souvent appelé traitement *Au moins une fois* . Cela signifie que chaque message sera traité au moins une fois, mais dans certains cas le même message peut être redistribué. Si le scénario ne peut pas tolérer traitement en double, une logique supplémentaire est requise dans l’application de détecter les doublons. Il est possible en fonction de la propriété [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) du message. La valeur de cette propriété reste constante au sein de tentatives de remise. Ceci est appelé traitement *Une seule fois* .

Le code est indiqué ici reçoit et traite un message en utilisant le mode **PeekLock** , ce qui correspond à la valeur par défaut si aucune valeur [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) n’est fourni explicitement.

```
MessageReceiver receiver = factory.CreateMessageReceiver("DataCollectionQueue");
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

### <a name="use-the-queue-client"></a>Utiliser le client file d’attente

Les exemples plus haut dans cette section créé objets [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) et [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) directement à partir de la [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) pour envoyer et recevoir des messages à partir de la file d’attente, respectivement. Une autre solution consiste à utiliser la classe [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) , qui prend en charge envoyer et recevoir des opérations en plus de fonctionnalités plus avancées, telles que des sessions.

```
QueueClient queueClient = factory.CreateQueueClient("DataCollectionQueue");
queueClient.Send(bm);
            
BrokeredMessage message = queueClient.Receive();

try
{
    ProcessMessage(message);
    message.Complete();
}
catch (Exception e)
{
    message.Abandon();
} 
```

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris les notions de base des files d’attente, consultez [créer applications qui utilisent des abonnements et les rubriques de Bus des services](service-bus-create-topics-subscriptions.md) pour continuer cette discussion utilisant les fonctionnalités de publication et d’abonnement de rubriques Bus des services et abonnements.