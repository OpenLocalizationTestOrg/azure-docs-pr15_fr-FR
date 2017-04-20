<properties 
    pageTitle="Comment utiliser des files d’attente Bus des services avec PHP | Microsoft Azure" 
    description="Découvrez comment utiliser les files d’attente Bus des services dans Azure. Exemples de code écrits en PHP." 
    services="service-bus" 
    documentationCenter="php" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="PHP" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-queues"></a>Comment utiliser les files d’attente Bus des services

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Ce guide vous montre comment utiliser les files d’attente Bus des services. Les exemples sont écrits dans PHP et utilisent le [Kit de développement logiciel Azure pour PHP](../php-download-sdk.md). Les scénarios présentés incluent **Création files d’attente**, **Envoyer et recevoir des messages**et **suppression des files d’attente**.

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="create-a-php-application"></a>Créer une application PHP

La seule exigence pour la création d’une application PHP qui accède au service d’objets Blob Azure est la référence à des classes dans [Azure SDK pour PHP](../php-download-sdk.md) à partir de votre code. Vous pouvez utiliser les outils de développement pour créer votre application, ou le bloc-notes.

> [AZURE.NOTE] Votre installation PHP doit comporter l' [extension OpenSSL](http://php.net/openssl) installé et activé.

Dans ce guide, vous allez utiliser les fonctionnalités du service qui peuvent être appelées à partir d’une application PHP localement, ou dans le code en cours d’exécution dans un rôle web Azure, un rôle de travail ou un site Web.

## <a name="get-the-azure-client-libraries"></a>Obtenir le client Azure bibliothèques

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configurez votre application pour utiliser Bus des services

Pour utiliser la file d’attente Bus des services API, procédez comme suit :

1. Faire référence au fichier chargeur automatique à l’aide de la [require_once] [ require_once] instruction.
2. Faire référence à toutes les classes que vous utilisez.

L’exemple suivant montre comment inclure le fichier chargeur automatique et faire référence à la classe **ServicesBuilder** .

> [AZURE.NOTE] Cet exemple montre comment (et autres exemples de cet article) suppose que vous avez installé les bibliothèques du Client PHP pour Azure via Composer. Si vous avez installé les bibliothèques manuellement ou sous forme de package POIRIERS, vous devez référencer le fichier de chargeur automatique **WindowsAzure.php** .

```
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

Dans les exemples ci-dessous, la `require_once` instruction apparaissent toujours, mais uniquement les classes nécessaires à l’exemple s’exécute sont référencées.

## <a name="set-up-a-service-bus-connection"></a>Configurer une connexion de Service Bus

Pour instanciation un client Bus des services, vous devez tout d’abord une chaîne de connexion valide au format suivant :

```
Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]
```

Où **point de terminaison** est généralement au format `[yourNamespace].servicebus.windows.net`.

Pour créer un client Azure service que vous devez utiliser la classe **ServicesBuilder** . Vous pouvez :

* Passer la chaîne de connexion directement à.
* Utiliser le **CloudConfigurationManager (CCM)** pour vérifier plusieurs sources externes pour la chaîne de connexion :
    * Par défaut, il est fourni avec prise en charge pour une source externe - variables d’environnement
    * Vous pouvez ajouter de nouvelles sources en étendant la classe **ConnectionStringSource**

Pour les exemples présentés ici, la chaîne de connexion est passée directement.

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="how-to-create-a-queue"></a>Comment : créer une file d’attente

Vous pouvez effectuer des opérations de gestion des files d’attente de Bus des services via la classe **ServiceBusRestProxy** . Un objet **ServiceBusRestProxy** est généré via la méthode d’usine **ServicesBuilder::createServiceBusService** avec une chaîne de connexion appropriée qui encapsule les autorisations pour gérer le jetons.

L’exemple suivant montre comment instanciation un **ServiceBusRestProxy** et appelez **ServiceBusRestProxy -> createQueue** pour créer une file d’attente nommée `myqueue` au sein d’un `MySBNamespace` espace de noms de service :

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\QueueInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
    
try {
    $queueInfo = new QueueInfo("myqueue");
        
    // Create queue.
    $serviceBusRestProxy->createQueue($queueInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/windowsazure/dd179357
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [AZURE.NOTE] Vous pouvez utiliser la `listQueues` méthode sur `ServiceBusRestProxy` objets pour vérifier si une file d’attente avec un nom spécifié existe déjà dans un espace de noms.

## <a name="how-to-send-messages-to-a-queue"></a>Comment : envoyer des messages à une file d’attente

Pour envoyer un message à une file d’attente Bus des services, votre application appelle la méthode **ServiceBusRestProxy -> sendQueueMessage** . Le code suivant montre comment envoyer un message à le `myqueue` file d’attente précédemment créée au sein de la `MySBNamespace` espace de noms de service.

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
try {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");
    
    // Send message.
    $serviceBusRestProxy->sendQueueMessage("myqueue", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/windowsazure/hh780775
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Messages envoyés à (et reçus à partir de) files d’attente de Bus de Service sont des instances de la classe **BrokeredMessage** . Objets **BrokeredMessage** ont un ensemble de méthodes standards (par exemple, **getLabel**, **getTimeToLive**, **setLabel**et **setTimeToLive**) et les propriétés qui sont utilisées pour stocker des propriétés personnalisées spécifiques à l’application et un corps de données d’application arbitraire.

Files d’attente Bus des services prend en charge une taille maximale des messages de la [couche Standard](service-bus-premium-messaging.md) 256 Ko et 1 Mo dans la [couche Premium](service-bus-premium-messaging.md). L’en-tête, qui inclut les propriétés d’application standard et personnalisées, peut avoir une taille maximale de 64 Ko. Il n’existe aucune limite du nombre de messages conservés dans une file d’attente, mais il existe une lettrine sur la taille totale des messages émanant d’une file d’attente. Cette limite supérieure sur la taille de la file d’attente est 5 Go.

## <a name="how-to-receive-messages-from-a-queue"></a>Comment faire pour recevoir des messages à partir d’une file d’attente

La meilleure façon de recevoir des messages à partir d’une file d’attente consiste à utiliser une méthode **ServiceBusRestProxy -> receiveQueueMessage** . Messages pouvant être reçus dans deux modes : **ReceiveAndDelete** (par défaut) et **PeekLock**.

Lorsque l’utilisation du mode **ReceiveAndDelete** , recevez est une opération coup - autrement dit, lorsque Bus des services reçoit une demande de lecture d’un message dans une file d’attente, il marque le message comme étant consommées et retourne à l’application. Le mode **ReceiveAndDelete** très simple et convient le mieux pour les scénarios dans lesquels une application tolèrent ne pas traitement d’un message en cas de panne. Pour mieux comprendre cela, envisagez un scénario dans lequel le consommateur envoie la demande de réception, puis se bloque avant de le traiter. Étant donné que Bus des services sera avez marqué le message comme occupé, puis lorsque l’application redémarre et commence à lire des messages à nouveau, il sera ai manqués le message a été consommé avant l’incident.

En mode de **PeekLock** , la réception d’un message devient une opération de deux étages, qui permet aux applications de prise en charge qui ne peut pas tolérer messages manquants. Lorsque Service Bus reçoit une demande, il recherche le message suivant à consommer, verrous pour empêcher d’autres utilisateurs de recevoir et renvoie à l’application. Une fois l’application fini de traiter le message (ou qu’il stocke fiable d’un traitement ultérieur), il termine la deuxième étape du processus de réception en passant le message reçu à **ServiceBusRestProxy -> deleteMessage**. Lorsque Service Bus voit la **deleteMessage** appeler, il sera marquer le message comme étant consommées et supprimer de la file d’attente.

L’exemple suivant montre comment un message pouvant être reçu et traités à l’aide du mode **PeekLock** (pas le mode par défaut).

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
try {
    // Set the receive mode to PeekLock (default is ReceiveAndDelete).
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();
        
    // Receive message.
    $message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";
        
    /*---------------------------
        Process message here.
    ----------------------------*/
        
    // Delete message. Not necessary if peek lock is not set.
    echo "Message deleted.<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/windowsazure/hh780735
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Comment : gérer le blocage d’applications et messages illisibles

Bus des services fournit les fonctionnalités pour vous aider à récupérer normalement les erreurs dans votre application ou difficultés traitement d’un message. Si une application récepteur est impossible de traiter le message pour une raison quelconque, il peut appeler la méthode **unlockMessage** sur le message reçu (au lieu de la méthode **deleteMessage** ). Cela entraînera Bus des services déverrouiller le message dans la file d’attente et rendez-le disponible pour être à nouveau, reçus par l’application prend beaucoup de même ou par une autre application prend beaucoup de.

Vous trouverez également un délai d’expiration associée à un message verrouillé dans la file d’attente, et si l’application ne parvient pas à traiter le message avant le délai d’expiration de verrouillage expire (par exemple, si l’application se bloque), puis Bus des services sera déverrouiller automatiquement le message et rendez-le disponible perçu à nouveau.

Dans le cas où l’application se bloque après traitement du message, mais avant que la requête **deleteMessage** est émise, puis le message sera remis à nouveau à l’application lorsqu’il redémarre. Cette option est souvent appelée **Au moins une fois traitement**; Autrement dit, chaque message est traité au moins une fois, mais dans certains cas le même message peut être redistribué. Si le scénario ne peut pas tolérer traitement en double, puis ajouter une logique supplémentaire aux applications pour gérer la remise des messages en double est recommandé. Ceci est souvent obtenue en utilisant la méthode **getMessageId** du message, ce qui reste constante pour tous les tentatives de remise.

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris les notions de base des files d’attente Bus des services, voir [files d’attente, rubriques et des abonnements][] pour plus d’informations.

Pour plus d’informations, voir également le [Centre de développement PHP](/develop/php/).

[Abonnements, rubriques et files d’attente]: service-bus-queues-topics-subscriptions.md
[require_once]: http://php.net/require_once

 
