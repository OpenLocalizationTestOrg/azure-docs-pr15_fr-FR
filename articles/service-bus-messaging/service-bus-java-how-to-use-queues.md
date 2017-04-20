<properties
    pageTitle="Comment utiliser des files d’attente Bus des services avec Java | Microsoft Azure"
    description="Découvrez comment utiliser les files d’attente Bus des services dans Azure. Exemples de code écrits en Java."
    services="service-bus"
    documentationCenter="java"
    authors="sethmanheim"
    manager="timlt"
    />

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-queues"></a>Comment utiliser les files d’attente Bus des services

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Cet article décrit comment utiliser les files d’attente Bus des services. Les exemples sont écrits en Java et utilisent le [Kit de développement logiciel Azure pour Java][]. Les scénarios présentés incluent **Création files d’attente**, **Envoyer et recevoir des messages**et **suppression des files d’attente**.

## <a name="what-are-service-bus-queues"></a>Que sont les files d’attente de Service Bus ?

Files d’attente Bus des services prend en charge un modèle de communication **traité de messagerie** . Lorsque vous utilisez des files d’attente, les composants d’une application distribuée ne pas communiquent directement entre eux ; à la place qu’ils échangent des messages via une file d’attente, qui se comporte comme un intermédiaire (intermédiaire). Un producteur de message (expéditeur) transmet un message dans la file d’attente et puis continue son traitement.
En mode asynchrone, un consommateur de message (récepteur) extrait le message à partir de la file d’attente et la traite. Le producteur n’a pas à attendre une réponse du consommateur afin de continuer à traiter et envoyer des messages. Files d’attente offrent **First In, première absence (FIFO)** remise des messages à un ou plusieurs consommateurs concurrents. Autrement dit, les messages sont généralement reçus et traités par les destinataires dans l’ordre dans lequel ils ont été ajoutés à la file d’attente, et chaque message est reçue et traitée par consommateur qu’un seul message.

![QueueConcepts](./media/service-bus-java-how-to-use-queues/sb-queues-08.png)

Files d’attente Bus des services sont une technologie à usage général qui peut être utilisée pour un large éventail de scénarios :

- Communication entre les rôles web et de travail dans une application Azure à plusieurs niveaux.
- Communication entre les applications en local et Azure hébergé applications dans une solution hybride.
- Communication entre les composants d’une application distribuée en cours d’exécution en local dans différentes organisations ou départements d’une organisation.

Grâce files d’attente, vous pouvez évoluer plus facilement vos applications et activer la résilience dans votre architecture.

## <a name="create-a-service-namespace"></a>Créer un espace de noms de service

Pour commencer à utiliser des files d’attente Bus des services dans Azure, vous devez d’abord créer un espace de noms. Un espace de noms fournit un conteneur portée d’adressage ressources Bus de Service au sein de votre application.

Pour créer un espace de noms :

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configurez votre application pour utiliser Bus des services

Vérifiez que vous avez installé le [Kit de développement logiciel Azure pour Java][] avant de générer cet exemple. Si vous utilisez Eclipse, vous pouvez installer le [Kit de ressources Azure pour Eclipse][] qui inclut le Kit de développement Azure pour Java. Vous pouvez ensuite ajouter les **Microsoft Azure bibliothèques pour Java** à votre projet :

![](./media/service-bus-java-how-to-use-queues/eclipselibs.png)

Ajoutez le code suivant `import` instructions au début du fichier Java :

```
// Include the following imports to use Service Bus APIs
import com.microsoft.windowsazure.services.servicebus.*;
import com.microsoft.windowsazure.services.servicebus.models.*;
import com.microsoft.windowsazure.core.*;
import javax.xml.datatype.*;
```

## <a name="create-a-queue"></a>Créer une file d’attente

Opérations de gestion des files d’attente Bus des services peuvent être effectuées via la classe **ServiceBusContract** . Un objet **ServiceBusContract** est construit avec une configuration appropriée qui encapsule le jeton associations de sécurité avec les autorisations pour gérer la classe **ServiceBusContract** est le seul point de communication avec Azure

La classe **ServiceBusService** fournit des méthodes pour créer, énumérer et supprimer des files d’attente. L’exemple ci-dessous montre comment un objet **ServiceBusService** pouvant être utilisé pour créer une file d’attente nommée « TestQueue », avec un espace de noms « HowToSample » :

```
Configuration config =
    ServiceBusConfiguration.configureWithSASAuthentication(
            "HowToSample",
            "RootManageSharedAccessKey",
            "SAS_key_value",
            ".servicebus.windows.net"
            );

ServiceBusContract service = ServiceBusService.create(config);
QueueInfo queueInfo = new QueueInfo("TestQueue");
try
{
    CreateQueueResult result = service.createQueue(queueInfo);
}
catch (ServiceException e)
{
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

Il existe des méthodes sur **QueueInfo** qui permettent de propriétés de la file d’attente à analyser (par exemple : pour définir la valeur par défaut time to live (TTL) à appliquer aux messages envoyés à la file d’attente). L’exemple suivant montre comment créer une file d’attente nommée `TestQueue` avec une taille maximale de 5 Go :

````
long maxSizeInMegabytes = 5120;
QueueInfo queueInfo = new QueueInfo("TestQueue");
queueInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
CreateQueueResult result = service.createQueue(queueInfo);
````

Notez que vous pouvez utiliser la méthode **listQueues** sur les objets **ServiceBusContract** pour vérifier si une file d’attente avec un nom spécifié existe déjà dans un espace de noms de service.

## <a name="send-messages-to-a-queue"></a>Envoyer des messages à une file d’attente

Pour envoyer un message à une file d’attente Bus des services, votre application obtient un objet **ServiceBusContract** . Le code suivant montre comment envoyer un message le `TestQueue` file d’attente précédemment créée dans le `HowToSample` espace de noms :

```
try
{
    BrokeredMessage message = new BrokeredMessage("MyMessage");
    service.sendQueueMessage("TestQueue", message);
}
catch (ServiceException e)
{
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

Les messages envoyés à, puis reçu de Service Bus files d’attente sont des instances de la classe [BrokeredMessage][] . Objets [BrokeredMessage][] comportent un ensemble de propriétés standard (par exemple, [étiquette](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) et [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), un dictionnaire est utilisé pour inclure des propriétés spécifiques application personnalisée et un corps de données d’application arbitraire. Une application peut définir le corps du message en passant tout objet sérialisable dans le constructeur de la [BrokeredMessage][]et le sérialiseur approprié puis servira à sérialiser l’objet. Par ailleurs, vous pouvez fournir une java **. IO. InputStream** objet.

L’exemple suivant montre comment envoyer cinq tester les messages à la `TestQueue` **MessageSender** nous obtenues dans l’extrait de code précédent :

```
for (int i=0; i<5; i++)
{
     // Create message, passing a string message for the body.
     BrokeredMessage message = new BrokeredMessage("Test message " + i);
     // Set an additional app-specific property.
     message.setProperty("MyProperty", i);
     // Send message to the queue
     service.sendQueueMessage("TestQueue", message);
}
```

Files d’attente Bus des services prend en charge une taille maximale des messages de la [couche Standard](service-bus-premium-messaging.md) 256 Ko et 1 Mo dans la [couche Premium](service-bus-premium-messaging.md). L’en-tête, qui inclut les propriétés d’application standard et personnalisées, peut avoir une taille maximale de 64 Ko. Il n’existe aucune limite du nombre de messages conservés dans une file d’attente, mais il existe une lettrine sur la taille totale des messages émanant d’une file d’attente. Cette taille file d’attente est définie au moment de la création, avec un maximum de 5 Go.

## <a name="receive-messages-from-a-queue"></a>Recevoir des messages à partir d’une file d’attente

Le moyen principal de recevoir des messages à partir d’une file d’attente consiste à utiliser un objet **ServiceBusContract** . Messages reçus peuvent fonctionner dans deux modes différents : **ReceiveAndDelete** et **PeekLock**.

Lorsque l’utilisation du mode **ReceiveAndDelete** , recevez est une opération coup - autrement dit, lorsque Bus des services reçoit une demande de lecture d’un message dans une file d’attente, il marque le message comme étant consommées et retourne à l’application. Le mode **ReceiveAndDelete** (qui est le mode par défaut) est le modèle le plus simple et convient le mieux pour les scénarios dans lesquels une application tolèrent ne pas traitement d’un message en cas de panne. Pour mieux comprendre cela, envisagez un scénario dans lequel le consommateur envoie la demande de réception, puis se bloque avant de le traiter.
Étant donné que Bus des services sera avez marqué le message comme occupé, puis lorsque l’application redémarre et commence à lire des messages à nouveau, il sera ai manqués le message a été consommé avant l’incident.

En mode **PeekLock** , recevez devient une opération de deux étages, qui permet aux applications de prise en charge qui ne peut pas tolérer messages manquants. Lorsque Service Bus reçoit une demande, il recherche le message suivant à consommer, verrous pour empêcher les autres consommateurs reçoit et renvoie à l’application. Une fois l’application fini de traiter le message (ou qu’il stocke fiable d’un traitement ultérieur), il termine la deuxième étape du processus de réception en appelant **Supprimer** sur le message reçu. Lorsque Service Bus voit l’appel **Supprimer** , il sera marquer le message comme étant consommées et supprimer de la file d’attente.

L’exemple suivant montre comment les messages pouvant être reçus et traitement à l’aide du mode **PeekLock** (pas le mode par défaut). L’exemple ci-dessous ne boucle et traite les messages entrants dans notre « TestQueue » :

```
try
{
    ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
    opts.setReceiveMode(ReceiveMode.PEEK_LOCK);

    while(true)  {
         ReceiveQueueMessageResult resultQM =
                service.receiveQueueMessage("TestQueue", opts);
        BrokeredMessage message = resultQM.getValue();
        if (message != null && message.getMessageId() != null)
        {
            System.out.println("MessageID: " + message.getMessageId());
            // Display the queue message.
            System.out.print("From queue: ");
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
                message.getProperty("MyProperty"));
            // Remove message from queue.
            System.out.println("Deleting this message.");
            //service.deleteMessage(message);
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

Bus des services fournit les fonctionnalités pour vous aider à récupérer normalement les erreurs dans votre application ou difficultés traitement d’un message. Si une application récepteur est impossible de traiter le message pour une raison quelconque, il peut appeler la méthode **unlockMessage** sur le message reçu (au lieu de la méthode **deleteMessage** ). Cela entraînera Bus des services déverrouiller le message dans la file d’attente et rendez-le disponible pour être à nouveau, reçus par l’application prend beaucoup de même ou par une autre application prend beaucoup de.

Vous trouverez également un délai d’expiration associée à un message verrouillé dans la file d’attente, et si l’application ne parvient pas à traiter le message avant le délai d’expiration de verrouillage expire (par exemple, si l’application se bloque), puis Bus des services sera déverrouiller automatiquement le message et rendez-le disponible perçu à nouveau.

Dans le cas où l’application se bloque après traitement du message, mais avant que la requête **deleteMessage** est émise, puis le message sera remis à nouveau à l’application lorsqu’il redémarre. Cette option est souvent appelée **Au moins une fois que traitement**, c'est-à-dire que chaque message sera traité au moins une fois mais dans certains cas le même message peut être redistribué. Si le scénario ne peut pas tolérer traitement en double, les développeurs d’applications doivent ajouter une logique supplémentaire à son application pour gérer la remise des messages en double. Ceci est souvent obtenue en utilisant la méthode **getMessageId** du message, ce qui reste constante au sein de tentatives de remise.

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris les notions de base des files d’attente Bus des services, voir [files d’attente, rubriques et des abonnements][] pour plus d’informations.

Pour plus d’informations, voir le [Centre de développement Java](/develop/java/).


  [Azure SDK pour Java]: http://azure.microsoft.com/develop/java/
  [Kit de ressources Azure pour Éclipse]: https://msdn.microsoft.com/library/azure/hh694271.aspx
  [Abonnements, rubriques et files d’attente]: service-bus-queues-topics-subscriptions.md
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx

