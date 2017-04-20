<properties
    pageTitle="Comment utiliser les thèmes du Service Bus avec Java | Microsoft Azure"
    description="Découvrez comment utiliser des rubriques Bus des services et abonnements dans Azure. Exemples de code sont écrits dans les applications Java."
    services="service-bus"
    documentationCenter="java"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Comment utiliser des abonnements et les rubriques de Bus des services

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Ce guide décrit l’utilisation des abonnements et les rubriques de Bus des services. Les exemples sont écrits en Java et utilisent le [Kit de développement logiciel Azure pour Java][]. Les scénarios présentés incluent **Création rubriques et abonnements**, **Création de filtres de l’abonnement**, **envoyant des messages à un sujet**, **recevoir des messages à partir d’un abonnement**et **suppression des rubriques et abonnements**.

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Quelles sont les abonnements et les rubriques de Bus des services ?

Abonnements et les rubriques de Service Bus prennent en charge une *publication et d’abonnement* modèle de communication de messagerie. Lorsque vous utilisez des abonnements et les rubriques, des composants d’une application distribuée ne pas communiquent directement entre eux ; à la place qu’ils échangent des messages via une rubrique qui se comporte comme un intermédiaire.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

Contrairement aux files d’attente Bus des services, dans lequel chaque message est traité par un seul consommateur, rubriques et abonnements offrent une forme « un-à-plusieurs » de communication, en utilisant un modèle de publication et d’abonnement. Il est possible d’enregistrer plusieurs abonnements à un sujet. Lorsqu’un message est envoyé à un sujet, puis le rend disponible à chaque abonnement poignée/processus séparément.

Un abonnement à un sujet ressemble à une file d’attente virtuelle qui reçoit une copie des messages qui ont été envoyés à la rubrique. Vous pouvez enregistrer le filtre règles pour une rubrique dans une base par abonnement, ce qui vous permet de restreindre/filtre les messages à un sujet sont reçus par les abonnements rubrique.

Abonnements et les rubriques de Service Bus permettent de mettre à l’échelle de traiter un grand nombre de messages au sein d’un grand nombre d’utilisateurs et d’applications.

## <a name="create-a-service-namespace"></a>Créer un espace de noms de service

Pour commencer à l’aide des rubriques Bus des services et abonnements dans Azure, vous devez d’abord créer un espace de noms de service. Un espace de noms fournit un conteneur portée d’adressage ressources Bus de Service au sein de votre application.

Pour créer un espace de noms :

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configurez votre application pour utiliser Bus des services

Vérifiez que vous avez installé le [Kit de développement logiciel Azure pour Java][] avant de générer cet exemple. Si vous utilisez Eclipse, vous pouvez installer le [Kit de ressources Azure pour Eclipse][] qui inclut le Kit de développement Azure pour Java. Vous pouvez ensuite ajouter les **Microsoft Azure bibliothèques pour Java** à votre projet :

![](media/service-bus-java-how-to-use-topics-subscriptions/eclipselibs.png)

Ajoutez les instructions d’importation suivantes en haut du fichier Java :

```
import com.microsoft.windowsazure.services.servicebus.*;
import com.microsoft.windowsazure.services.servicebus.models.*;
import com.microsoft.windowsazure.core.*;
import javax.xml.datatype.*;
```

Ajouter les bibliothèques Azure pour Java à votre chemin d’accès de build et l’inclure dans votre assembly de déploiement de projet.

## <a name="create-a-topic"></a>Créer un sujet

Opérations de gestion des rubriques Bus des services peuvent être effectuées via la classe **ServiceBusContract** . Un objet **ServiceBusContract** est construit avec une configuration appropriée qui encapsule le jeton associations de sécurité avec les autorisations pour gérer la classe **ServiceBusContract** est le seul point de communication avec Azure

La classe **ServiceBusService** fournit des méthodes pour créer, énumérer et supprimer des rubriques. L’exemple suivant montre comment un objet **ServiceBusService** pouvant être utilisé pour créer une rubrique nommée `TestTopic`, avec un espace de noms appelé `HowToSample`:

    Configuration config =
        ServiceBusConfiguration.configureWithSASAuthentication(
          "HowToSample",
          "RootManageSharedAccessKey",
          "SAS_key_value",
          ".servicebus.windows.net"
          );

    ServiceBusContract service = ServiceBusService.create(config);
    TopicInfo topicInfo = new TopicInfo("TestTopic");
    try  
    {
        CreateTopicResult result = service.createTopic(topicInfo);
    }
    catch (ServiceException e) {
        System.out.print("ServiceException encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

Il existe méthodes sur **TopicInfo** qui permettent de la rubrique à définir les propriétés (par exemple : pour définir la valeur par défaut time to live (TTL) à appliquer aux messages envoyés à la rubrique). L’exemple suivant montre comment créer une rubrique nommée `TestTopic` avec une taille maximale de 5 Go :

    long maxSizeInMegabytes = 5120;  
    TopicInfo topicInfo = new TopicInfo("TestTopic");  
    topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
    CreateTopicResult result = service.createTopic(topicInfo);

Notez que vous pouvez utiliser la méthode **listTopics** sur les objets **ServiceBusContract** pour vérifier si une rubrique avec un nom spécifié existe déjà dans un espace de noms de service.

## <a name="create-subscriptions"></a>Créer des abonnements

Abonnements à des rubriques sont également créées avec la classe **ServiceBusService** . Abonnements sont nommées et peuvent avoir un filtre facultatif qui limite l’ensemble des messages transmis à la file d’attente virtuelle de l’abonnement.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Créer un abonnement avec le filtre par défaut (MatchAll)

Le filtre **MatchAll** est le filtre par défaut qui est utilisé si aucun filtre n’est spécifié lors de la création d’un nouvel abonnement. Lorsque le filtre **MatchAll** est utilisé, tous les messages publiés dans la rubrique sont placées dans la file d’attente virtuelle de l’abonnement. L’exemple suivant crée un abonnement nommé « AllMessages » et utilise le filtre de **MatchAll** par défaut.

    SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
    CreateSubscriptionResult result =
        service.createSubscription("TestTopic", subInfo);

### <a name="create-subscriptions-with-filters"></a>Créer des abonnements avec les filtres

Vous pouvez également créer des filtres qui vous permettent d’étendue qui les messages envoyés à un sujet doivent figurer au sein d’un abonnement sujet spécifique.

Le type de filtre pris en charge par les abonnements plus souple est le [SqlFilter][], qui met en œuvre un sous-ensemble de SQL92. Filtres SQL fonctionnent sur les propriétés des messages qui sont publiés dans la rubrique. Pour plus d’informations sur les expressions qui peuvent être utilisées avec un filtre SQL, passez en revue la syntaxe de la [SqlFilter.SqlExpression][] .

L’exemple suivant crée un abonnement nommé `HighMessages` avec un objet [SqlFilter][] qui sélectionne uniquement les messages qui ont une propriété **format** personnalisée égal à 3 :

```
// Create a "HighMessages" filtered subscription  
SubscriptionInfo subInfo = new SubscriptionInfo("HighMessages");
CreateSubscriptionResult result = service.createSubscription("TestTopic", subInfo);
RuleInfo ruleInfo = new RuleInfo("myRuleGT3");
ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber > 3");
CreateRuleResult ruleResult = service.createRule("TestTopic", "HighMessages", ruleInfo);
// Delete the default rule, otherwise the new rule won't be invoked.
service.deleteRule("TestTopic", "HighMessages", "$Default");
```

De même, l’exemple suivant crée un abonnement nommé `LowMessages` avec un objet [SqlFilter][] qui sélectionne uniquement les messages qui ont une propriété **format** inférieur ou égal à 3 :

```
// Create a "LowMessages" filtered subscription
SubscriptionInfo subInfo = new SubscriptionInfo("LowMessages");
CreateSubscriptionResult result = service.createSubscription("TestTopic", subInfo);
RuleInfo ruleInfo = new RuleInfo("myRuleLE3");
ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber <= 3");
CreateRuleResult ruleResult = service.createRule("TestTopic", "LowMessages", ruleInfo);
// Delete the default rule, otherwise the new rule won't be invoked.
service.deleteRule("TestTopic", "LowMessages", "$Default");
```

Lorsqu’un message est maintenant envoyé à `TestTopic`, elle est envoyée aux récepteurs d’abonné à la `AllMessages` abonnement et sélectivement remis aux récepteurs d’abonné à la `HighMessages` et `LowMessages` abonnements (en fonction du contenu du message).

## <a name="send-messages-to-a-topic"></a>Envoyer des messages à un sujet

Pour envoyer un message à un sujet de Bus des services, votre application obtient un objet **ServiceBusContract** . Le code suivant montre comment envoyer un message pour le `TestTopic` rubrique créé précédemment dans le `HowToSample` espace de noms :

```
BrokeredMessage message = new BrokeredMessage("MyMessage");
service.sendTopicMessage("TestTopic", message);
```

Les messages envoyés vers des rubriques de Bus de Service sont des instances de la classe [BrokeredMessage][] . [BrokeredMessage][] *objets comportent un ensemble de méthodes standard (tel que * *setLabel* * et * *TimeToLive**), un dictionnaire est utilisé pour inclure des propriétés personnalisées spécifiques à l’application et corps des données d’application arbitraire. Une application peut définir le corps du message en passant tout objet sérialisable dans le constructeur de la [BrokeredMessage][]et appropriés * *DataContractSerializer* * puis servira à sérialiser l’objet. Par ailleurs, un * *java.io.InputStream** peut être fourni.

L’exemple suivant montre comment envoyer cinq tester les messages à la `TestTopic` **MessageSender** nous obtenues dans l’extrait de code ci-dessus.
Notez la manière dont la valeur de propriété **format** de chaque message varie selon l’itération de la boucle (Cela permet de déterminer les abonnements reçoivent) :

```
for (int i=0; i<5; i++)  {
// Create message, passing a string message for the body
BrokeredMessage message = new BrokeredMessage("Test message " + i);
// Set some additional custom app-specific property
message.setProperty("MessageNumber", i);
// Send message to the topic
service.sendTopicMessage("TestTopic", message);
}
```

Rubriques Bus des services prend en charge une taille maximale des messages de la [couche Standard](service-bus-premium-messaging.md) 256 Ko et 1 Mo dans la [couche Premium](service-bus-premium-messaging.md). L’en-tête, qui inclut les propriétés d’application standard et personnalisées, peut avoir une taille maximale de 64 Ko. Il n’existe aucune limite du nombre de messages conservés dans une rubrique mais il existe une lettrine sur la taille totale des messages émanant d’une rubrique. La taille de cette rubrique est définie au moment de la création, avec un maximum de 5 Go.

## <a name="how-to-receive-messages-from-a-subscription"></a>Comment faire pour recevoir des messages à partir d’un abonnement

Pour recevoir des messages à partir d’un abonnement, utilisez un objet **ServiceBusContract** . Messages reçus peuvent fonctionner dans deux modes différents : **ReceiveAndDelete** et **PeekLock**.

Lorsque l’utilisation du mode **ReceiveAndDelete** , recevez est une opération coup - autrement dit, lorsque Bus des services reçoit une demande de lecture d’un message, il marque le message comme étant consommées et retourne à l’application. Le mode **ReceiveAndDelete** très simple et convient le mieux pour les scénarios dans lesquels une application tolèrent ne pas traitement d’un message en cas de panne. Pour mieux comprendre cela, envisagez un scénario dans lequel le consommateur envoie la demande de réception, puis se bloque avant de le traiter. Étant donné que Bus des services sera avez marqué le message comme occupé, puis lorsque l’application redémarre et commence à lire des messages à nouveau, il sera ai manqués le message a été consommé avant l’incident.

En mode **PeekLock** , recevez devient une opération de deux étages, qui permet aux applications de prise en charge qui ne peut pas tolérer messages manquants. Lorsque Service Bus reçoit une demande, il recherche le message suivant à consommer, verrous pour empêcher les autres consommateurs reçoit et renvoie à l’application. Une fois l’application fini de traiter le message (ou qu’il stocke fiable d’un traitement ultérieur), il termine la deuxième étape du processus de réception en appelant **Supprimer** sur le message reçu. Lorsque Service Bus voit l’appel **Supprimer** , il sera marquer le message comme étant consommées et supprimer de la rubrique.

L’exemple ci-dessous montre comment les messages pouvant être reçus et traitement à l’aide du mode **PeekLock** (pas le mode par défaut). L’exemple ci-dessous effectue une boucle et traite les messages dans l’abonnement « HighMessages » et puis se ferme lorsqu’il n’y a aucun autre message (sinon, elle peut être définie attendre que les nouveaux messages).

```
try
{
    ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
    opts.setReceiveMode(ReceiveMode.PEEK_LOCK);

    while(true)  {
        ReceiveSubscriptionMessageResult  resultSubMsg =
            service.receiveSubscriptionMessage("TestTopic", "HighMessages", opts);
        BrokeredMessage message = resultSubMsg.getValue();
        if (message != null && message.getMessageId() != null)
        {
            System.out.println("MessageID: " + message.getMessageId());
            // Display the topic message.
            System.out.print("From topic: ");
            byte[] b = new byte[200];
            String s = null;
            int numRead = message.getBody().read(b);
            while (-1 != numRead)
            {
                s = new String(b);
                s = s.trim();
                System.out.print(s);
                numRead = message.getBody().read(b);
            }
            System.out.println();
            System.out.println("Custom Property: " +
                message.getProperty("MessageNumber"));
            // Delete message.
            System.out.println("Deleting this message.");
            service.deleteMessage(message);
        }  
        else  
        {
            System.out.println("Finishing up - no more messages.");
            break;
            // Added to handle no more messages.
            // Could instead wait for more messages to be added.
        }
    }
}
catch (ServiceException e) {
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
catch (Exception e) {
    System.out.print("Generic exception encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Comment gérer le blocage d’applications et messages illisibles

Bus des services fournit les fonctionnalités pour vous aider à récupérer normalement les erreurs dans votre application ou difficultés traitement d’un message. Si une application récepteur est impossible de traiter le message pour une raison quelconque, il peut appeler la méthode **unlockMessage** sur le message reçu (au lieu de la méthode **deleteMessage** ). Cela entraînera Bus des services déverrouiller le message dans la rubrique et rendez-le disponible pour être à nouveau, reçus par l’application prend beaucoup de même ou par une autre application prend beaucoup de.

Vous trouverez également un délai d’expiration associée à un message verrouillé dans la rubrique, et si l’application ne parvient pas à traiter le message avant le délai d’expiration de verrouillage expire (par exemple, si l’application se bloque), Bus des services sera déverrouiller automatiquement le message et rendez-le disponible pour être reçus à nouveau.

Dans le cas où l’application se bloque après traitement du message, mais avant que la requête **deleteMessage** est émise, puis le message sera remis à nouveau à l’application lorsqu’il redémarre. Cette option est souvent appelée **Au moins une fois que traitement**, c'est-à-dire que chaque message sera traité au moins une fois mais dans certains cas le même message peut être redistribué. Si le scénario ne peut pas tolérer traitement en double, les développeurs d’applications doivent ajouter une logique supplémentaire à son application pour gérer la remise des messages en double. Ceci est souvent obtenue en utilisant la méthode **getMessageId** du message, ce qui reste constante au sein de tentatives de remise.

## <a name="delete-topics-and-subscriptions"></a>Supprimer des rubriques et des abonnements

Pour supprimer des rubriques et abonnements, le principal consiste à utiliser un objet **ServiceBusContract** . Suppression d’un sujet va également supprimer tous les abonnements qui sont enregistrés avec la rubrique. Abonnements peuvent également être supprimés séparément.

```
// Delete subscriptions
service.deleteSubscription("TestTopic", "AllMessages");
service.deleteSubscription("TestTopic", "HighMessages");
service.deleteSubscription("TestTopic", "LowMessages");

// Delete a topic
service.deleteTopic("TestTopic");
```

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris les notions de base des files d’attente Bus des services, voir [abonnements, rubriques et les files d’attente Bus des services][] pour plus d’informations.

  [Azure SDK pour Java]: http://azure.microsoft.com/develop/java/
  [Kit de ressources Azure pour Éclipse]: https://msdn.microsoft.com/library/azure/hh694271.aspx
  [Azure classic portal]: http://manage.windowsazure.com/
  [Abonnements, rubriques et files d’attente Bus des services]: service-bus-queues-topics-subscriptions.md
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx 
  [SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  
  [0]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-13.png
  [2]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-04.png
  [3]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-09.png
