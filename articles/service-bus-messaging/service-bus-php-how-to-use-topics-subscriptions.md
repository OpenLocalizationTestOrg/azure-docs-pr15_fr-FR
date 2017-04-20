<properties 
    pageTitle="Comment utiliser les thèmes du Service Bus avec PHP | Microsoft Azure" 
    description="Découvrez comment utiliser les thèmes du Service Bus avec PHP dans Azure." 
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
    ms.date="10/14/2016" 
    ms.author="sethm"/>


# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Comment utiliser des abonnements et les rubriques de Bus des services

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Cet article vous explique comment utiliser des abonnements et les rubriques de Bus des services. Les exemples sont écrits dans PHP et utilisent le [Kit de développement logiciel Azure pour PHP](../php-download-sdk.md). Les scénarios présentés incluent **Création rubriques et abonnements**, **Création de filtres de l’abonnement**, **envoyant des messages à un sujet**, **recevoir des messages à partir d’un abonnement**et **suppression des rubriques et abonnements**.

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-php-application"></a>Créer une application PHP

La seule exigence pour créer une application PHP qui accède au service d’objets Blob Azure consiste à référencer classes dans [Azure SDK pour PHP](../php-download-sdk.md) à partir de votre code. Vous pouvez utiliser les outils de développement pour créer votre application, ou le bloc-notes.

> [AZURE.NOTE] Votre installation PHP doit comporter l' [extension OpenSSL](http://php.net/openssl) installé et activé.

Cet article décrit comment utiliser les fonctionnalités de service qui peuvent être appelées au sein d’une application PHP localement, ou dans le code en cours d’exécution dans un rôle web Azure, un rôle de travail ou un site Web.

## <a name="get-the-azure-client-libraries"></a>Obtenir le client Azure bibliothèques

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configurez votre application pour utiliser Bus des services

Pour utiliser les API de Bus de Service :

1. Faire référence au fichier chargeur automatique à l’aide de la [require_once] [ require-once] instruction.
2. Faire référence à toutes les classes que vous utilisez.

L’exemple suivant montre comment inclure le fichier chargeur automatique et faire référence à la classe **ServiceBusService** .

> [AZURE.NOTE] Cet exemple montre comment (et autres exemples de cet article) suppose que vous avez installé les bibliothèques du Client PHP pour Azure via Composer. Si vous avez installé les bibliothèques manuellement ou sous forme de package POIRIERS, vous devez référencer le fichier de chargeur automatique **WindowsAzure.php** .

```
require_once 'vendor\autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

Dans les exemples suivants, la `require_once` instruction apparaissent toujours, mais uniquement les classes nécessaires à l’exemple s’exécute sont référencées.

## <a name="set-up-a-service-bus-connection"></a>Configurer une connexion de Service Bus

Pour instanciation d’un client Bus des services vous devez tout d’abord une chaîne de connexion valide au format suivant :

```
Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]
```

Où `Endpoint` est généralement au format `https://[yourNamespace].servicebus.windows.net`.

Pour créer un client Azure service que vous devez utiliser la classe **ServicesBuilder** . Vous pouvez :

* Passer la chaîne de connexion directement à.
* Utiliser le **CloudConfigurationManager (CCM)** pour vérifier plusieurs sources externes pour la chaîne de connexion :
    * Par défaut, il est fourni avec prise en charge pour une source externe - variables d’environnement.
    * Vous pouvez ajouter de nouvelles sources en étendant la classe **ConnectionStringSource** .

Pour les exemples présentés ici, la chaîne de connexion est passée directement.

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
    
$connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>Créer un sujet

Vous pouvez effectuer des opérations de gestion des rubriques Bus des services via la classe **ServiceBusRestProxy** . Un objet **ServiceBusRestProxy** est généré via la méthode d’usine **ServicesBuilder::createServiceBusService** avec une chaîne de connexion appropriée qui encapsule les autorisations pour gérer le jetons.

L’exemple suivant montre comment instanciation un **ServiceBusRestProxy** et appelez **ServiceBusRestProxy -> createTopic** pour créer un sujet nommé `mytopic` au sein d’un `MySBNamespace` espace de noms :

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\TopicInfo;
    
// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
    
try {       
    // Create topic.
    $topicInfo = new TopicInfo("mytopic");
    $serviceBusRestProxy->createTopic($topicInfo);
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

> [AZURE.NOTE] Vous pouvez utiliser la `listTopics` méthode sur `ServiceBusRestProxy` objets pour vérifier si une rubrique avec un nom spécifié existe déjà dans un espace de noms de service.

## <a name="create-a-subscription"></a>Créer un abonnement

Abonnement à une rubrique est également créées avec la méthode **ServiceBusRestProxy -> createSubscription** . Abonnements sont nommées et peuvent avoir un filtre facultatif qui limite l’ensemble des messages transmis à la file d’attente virtuelle de l’abonnement.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Créer un abonnement avec le filtre par défaut (MatchAll)

Le filtre **MatchAll** est le filtre par défaut qui est utilisé si aucun filtre n’est spécifié lors de la création d’un nouvel abonnement. Lorsque le filtre **MatchAll** est utilisé, tous les messages publiés dans la rubrique sont placées dans la file d’attente virtuelle de l’abonnement. L’exemple suivant crée un abonnement nommé « mysubscription » et utilise le filtre de **MatchAll** par défaut.

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
    
try {
    // Create subscription.
    $subscriptionInfo = new SubscriptionInfo("mysubscription");
    $serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/azure/dd179357
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

### <a name="create-subscriptions-with-filters"></a>Créer des abonnements avec les filtres

Vous pouvez également définir des filtres qui vous permettent de spécifier lesquelles messages envoyés à un sujet doivent apparaître un abonnement sujet spécifique. Le type de filtre pris en charge par les abonnements plus souple est le **SqlFilter**, qui met en œuvre un sous-ensemble de SQL92. Filtres SQL fonctionnent sur les propriétés des messages qui sont publiés dans la rubrique. Pour plus d’informations sur SqlFilters, consultez [SqlFilter.SqlExpression propriété][sqlfilter].

> [AZURE.NOTE] Chaque règle sur un abonnement traite les messages entrants séparément, ajout de leurs messages résultat à l’abonnement. En outre, chaque nouvel abonnement comporte un objet de la **règle** par défaut avec un filtre qui ajoute tous les messages à partir de la rubrique à l’abonnement. Pour recevoir uniquement les messages qui correspondent à votre filtre, vous devez supprimer la règle par défaut. Vous pouvez supprimer la règle par défaut à l’aide de la `ServiceBusRestProxy->deleteRule` méthode.

L’exemple suivant crée un abonnement nommé **HighMessages** avec un **SqlFilter** qui sélectionne uniquement les messages qui ont une propriété **format** personnalisée égal à 3 (pour plus d’informations sur l’ajout de propriétés personnalisées pour les messages, voir [Envoyer des messages à un sujet](#send-messages-to-a-topic) ) :

```
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

Notez que ce code nécessite l’utilisation d’un espace de noms supplémentaire : `WindowsAzure\ServiceBus\Models\SubscriptionInfo`.

De même, l’exemple suivant crée un abonnement nommé **LowMessages** avec un **SqlFilter** qui sélectionne uniquement les messages qui ont une propriété **format** inférieur ou égal à 3 :

```
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

À présent, lorsqu’un message est envoyé à le `mytopic` rubrique, il est toujours livré aux récepteurs d’abonné à la `mysubscription` abonnement et sélectivement remis aux récepteurs d’abonné à la `HighMessages` et `LowMessages` abonnements (en fonction du contenu du message).

## <a name="send-messages-to-a-topic"></a>Envoyer des messages à un sujet

Pour envoyer un message à un sujet de Bus des services, votre application appelle la méthode **ServiceBusRestProxy -> sendTopicMessage** . Le code suivant montre comment envoyer un message à le `mytopic` rubrique précédemment créé dans la `MySBNamespace` espace de noms de service.

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
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/azure/hh780775
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Les messages envoyés vers des rubriques Bus des services sont des instances de la classe **BrokeredMessage** . Les objets **BrokeredMessage** ont un ensemble de propriétés standards et les méthodes (par exemple, **getLabel**, **getTimeToLive**, **setLabel**et **setTimeToLive**), ainsi que les propriétés qui peuvent servir à stocker des propriétés personnalisées spécifiques à l’application. L’exemple suivant montre comment envoyer des messages test 5 à la `mytopic` rubrique précédemment créé. La méthode **DéfinirPropriété** est utilisée pour ajouter une propriété personnalisée (`MessageNumber`) à chaque message. Notez que la `MessageNumber` valeur de propriété varie sur chaque message (vous pouvez utiliser cette valeur pour déterminer les abonnements reçoivent, comme illustré dans la section [Création d’un abonnement](#create-a-subscription) ) :

```
for($i = 0; $i < 5; $i++){
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message ".$i);
            
    // Set custom property.
    $message->setProperty("MessageNumber", $i);
            
    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
```

Rubriques Bus des services prend en charge une taille maximale des messages de la [couche Standard](service-bus-premium-messaging.md) 256 Ko et 1 Mo dans la [couche Premium](service-bus-premium-messaging.md). L’en-tête, qui inclut les propriétés d’application standard et personnalisées, peut avoir une taille maximale de 64 Ko. Il n’existe aucune limite du nombre de messages conservés dans une rubrique mais il existe une lettrine sur la taille totale des messages émanant d’une rubrique. Cette limite de taille de la rubrique est 5 Go. Pour plus d’informations sur les quotas, voir [les quotas de Bus des services][].

## <a name="receive-messages-from-a-subscription"></a>Recevoir des messages à partir d’un abonnement

La meilleure façon de recevoir des messages à partir d’un abonnement consiste à utiliser une méthode **ServiceBusRestProxy -> receiveSubscriptionMessage** . Messages reçus peuvent fonctionner dans deux modes différents : **ReceiveAndDelete** (par défaut) et **PeekLock**.

Lorsque l’utilisation du mode **ReceiveAndDelete** , recevez est une opération de capture d’unique ; Autrement dit, quand Bus des services reçoit une demande de lecture d’un message dans un abonnement, il marque le message comme étant consommées et retourne à l’application. Le mode **ReceiveAndDelete** très simple et convient le mieux pour les scénarios dans lesquels une application tolèrent ne pas traitement d’un message en cas de panne. Pour mieux comprendre cela, envisagez un scénario dans lequel le consommateur envoie la demande de réception, puis se bloque avant de le traiter. Étant donné que Bus des services sera avez marqué le message comme occupé, puis lorsque l’application redémarre et commence à lire des messages à nouveau, il sera ai manqués le message a été consommé avant l’incident.

En mode de **PeekLock** , la réception d’un message devient une opération de deux étages, qui permet aux applications de prise en charge qui ne peut pas tolérer messages manquants. Lorsque Service Bus reçoit une demande, il recherche le message suivant à consommer, verrous pour empêcher les autres consommateurs reçoit et renvoie à l’application. Une fois l’application fini de traiter le message (ou qu’il stocke fiable d’un traitement ultérieur), il termine la deuxième étape du processus de réception en passant le message reçu à **ServiceBusRestProxy -> deleteMessage**. Lorsque Service Bus voit la **deleteMessage** appeler, il sera marquer le message comme étant consommées et supprimer de la file d’attente.

L’exemple suivant montre comment recevoir et de traiter un message à l’aide de mode **PeekLock** (pas le mode par défaut). 

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
try {
    // Set receive mode to PeekLock (default is ReceiveAndDelete)
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();
    
    // Get message.
    $message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", "mysubscription", $options);

    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";
        
    /*---------------------------
        Process message here.
    ----------------------------*/
        
    // Delete message. Not necessary if peek lock is not set.
    echo "Deleting message...<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/hh780735
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Comment : gérer le blocage d’applications et messages illisibles

Bus des services fournit les fonctionnalités pour vous aider à récupérer normalement les erreurs dans votre application ou difficultés traitement d’un message. Si une application récepteur est impossible de traiter le message pour une raison quelconque, il peut appeler la méthode **unlockMessage** sur le message reçu (au lieu de la méthode **deleteMessage** ). Cela entraînera Bus des services déverrouiller le message dans la file d’attente et rendez-le disponible pour être à nouveau, reçus par l’application prend beaucoup de même ou par une autre application prend beaucoup de.

Vous trouverez également un délai d’expiration associée à un message verrouillé dans la file d’attente, et si l’application ne parvient pas à traiter le message avant le délai d’expiration de verrouillage expire (par exemple, si l’application se bloque), puis Bus des services sera déverrouiller automatiquement le message et rendez-le disponible perçu à nouveau.

Dans le cas où l’application se bloque après traitement du message, mais avant que la requête **deleteMessage** est émise, puis le message sera remis à nouveau à l’application lorsqu’il redémarre. Cette option est souvent appelée **Au moins une fois traitement**; Autrement dit, chaque message est traité au moins une fois, mais dans certains cas le même message peut être redistribué. Si le scénario ne peut pas tolérer traitement en double, les développeurs d’applications doivent ajouter une logique supplémentaire aux applications pour gérer la remise des messages en double. Ceci est souvent obtenue en utilisant la méthode **getMessageId** du message, ce qui reste constante pour tous les tentatives de remise.

## <a name="delete-topics-and-subscriptions"></a>Supprimer des rubriques et des abonnements

Pour supprimer un sujet ou un abonnement, utilisez le **ServiceBusRestProxy -> deleteTopic** ou les méthodes **ServiceBusRestProxy -> deleteSubscripton** , respectivement. Notez que la suppression d’un sujet supprime également tous les abonnements qui sont enregistrés avec la rubrique.

L’exemple suivant montre comment supprimer une rubrique nommée `mytopic` et ses abonnements enregistrés.

```
require_once 'vendor/autoload.php';

use WindowsAzure\ServiceBus\ServiceBusService;
use WindowsAzure\ServiceBus\ServiceBusSettings;
use WindowsAzure\Common\ServiceException;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
    
try {       
    // Delete topic.
    $serviceBusRestProxy->deleteTopic("mytopic");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/azure/dd179357
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

En utilisant la méthode **deleteSubscription** , vous pouvez supprimer un abonnement indépendamment :

```
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris les notions de base des files d’attente Bus des services, voir [files d’attente, rubriques et des abonnements][] pour plus d’informations.

[Abonnements, rubriques et files d’attente]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[require-once]: http://php.net/require_once
[Quotas de Bus des services]: service-bus-quotas.md
