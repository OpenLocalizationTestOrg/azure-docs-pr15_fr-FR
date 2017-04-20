<properties 
    pageTitle="Comment utiliser des files d’attente Bus des services dans Node.js | Microsoft Azure" 
    description="Découvrez comment utiliser les files d’attente Bus des services dans Azure à partir d’une application Node.js." 
    services="service-bus" 
    documentationCenter="nodejs" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="10/03/2016" 
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-queues"></a>Comment utiliser les files d’attente Bus des services

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Cet article décrit comment utiliser des files d’attente Bus des services dans Node.js. Les exemples sont écrits en JavaScript et utilisent le module Node.js Azure. Les scénarios présentés incluent **Création files d’attente**, **Envoyer et recevoir des messages**et **suppression des files d’attente**. Pour plus d’informations sur les files d’attente, consultez la section [étapes suivantes](#next-steps) .

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="create-a-nodejs-application"></a>Créer une application Node.js

Créer une application Node.js vide. Pour obtenir des instructions sur la façon de créer une application Node.js, voir [créer et déployer une application Node.js vers un site Web Azure][], ou un [Service Cloud Node.js][] à l’aide de Windows PowerShell.

## <a name="configure-your-application-to-use-service-bus"></a>Configurez votre application pour utiliser Bus des services

Pour utiliser Bus des services Azure, téléchargez et utilisez le package Node.js Azure. Ce package inclut un ensemble de bibliothèques qui communiquer avec les services Service Bus reste.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Nœud Package Manager (NPM) permet d’obtenir le package

1. Utiliser la fenêtre de commande **Windows PowerShell pour Node.js** pour accéder à la **c :\\nœud\\sbqueues\\WebRole1** dossier dans lequel vous avez créé votre exemple d’application.

2. Tapez **npm installer azure** dans la fenêtre de commande, qui devrait produit une sortie similaire à ce qui suit :

    ```
    azure@0.7.5 node_modules\azure
        ├── dateformat@1.0.2-1.2.3
        ├── xmlbuilder@0.4.2
        ├── node-uuid@1.2.0
        ├── mime@1.2.9
        ├── underscore@1.4.4
        ├── validator@1.1.1
        ├── tunnel@0.0.2
        ├── wns@0.5.3
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
    ```

3. Vous pouvez exécuter manuellement la commande **ls** pour vérifier si une **nœud\_modules** dossier a été créé. Dans ce dossier rechercher le package **azure** , qui contient les bibliothèques que vous devez accéder aux files d’attente Bus des services.

### <a name="import-the-module"></a>Importer le module

Utiliser le bloc-notes ou un autre éditeur de texte, ajoutez le code suivant en haut du fichier **server.js** de l’application :

```
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Configurer une connexion Bus des services Azure

Le module Azure lit les variables d’environnement AZURE\_SERVICEBUS\_espace de noms et AZURE\_SERVICEBUS\_ACCESS\_clé pour obtenir des informations requises pour vous connecter au Service Bus. Si ces variables d’environnement ne sont pas définis, vous devez spécifier les informations de compte lors de l’appel **createServiceBusService**.

Pour obtenir un exemple de définir les variables d’environnement dans un fichier de configuration d’un Service Cloud Azure, voir [Service en nuage Node.js avec le stockage][].

Pour obtenir un exemple de définition des variables d’environnement dans le [portail classique Azure][] pour un site Web Azure, voir [Node.js Web Application avec du stockage][].

## <a name="create-a-queue"></a>Créer une file d’attente

L’objet **ServiceBusService** vous permet d’utiliser des files d’attente Bus des services. Le code suivant crée un objet **ServiceBusService** . Ajoutez-le dans la partie supérieure du fichier **server.js** , après l’instruction pour importer le module Azure :

```
var serviceBusService = azure.createServiceBusService();
```

En appelant **createQueueIfNotExists** sur l’objet **ServiceBusService** , la file d’attente spécifié est renvoyé (s’il existe), ou une nouvelle file d’attente avec le nom spécifié est créée. Le code suivant utilise **createQueueIfNotExists** pour créer ou se connecter à la file d’attente nommée `myqueue`:

```
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

**createServiceBusService** prend également en charge les options supplémentaires vous permettent de remplacer les paramètres de file d’attente par défaut tels que de la durée de la taille maximale ou live file d’attente du message. L’exemple suivant définit la taille maximale de file d’attente à 5 Go, puis une heure live valeur (TTL) d’une minute :

```
var queueOptions = {
      MaxSizeInMegabytes: '5120',
      DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
    if(!error){
        // Queue exists
    }
});
```

### <a name="filters"></a>Filtres

Opérations de filtrage facultatives peuvent être appliquées aux opérations effectuées via **ServiceBusService**. Opérations de filtrage peuvent inclure journalisation, automatiquement une nouvelle tentative, etc.. Filtres sont des objets qui implémentent une méthode avec la signature :

```
function handle (requestOptions, next)
```

Après avoir effectué son prétraitement sur les options de requête, la méthode doit appeler `next`, en passant un rappel avec la signature suivante :

```
function (returnObject, finalCallback, next)
```

Dans ce rappel et après le traitement de la **returnObject** (la réponse à la demande sur le serveur), le rappel doit appeler soit `next` s’il existe pour poursuivre le traitement des autres filtres, ou simplement appeler `finalCallback`, qui termine l’appel de service.

Deux filtres qui implémentent la logique de nouvelle tentative sont inclus dans le Kit de développement Azure pour Node.js, **ExponentialRetryPolicyFilter** et **LinearRetryPolicyFilter**. Le code suivant crée un objet **ServiceBusService** qui utilise **ExponentialRetryPolicyFilter**:

```
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>Envoyer des messages à une file d’attente

Pour envoyer un message à une file d’attente Bus des services, votre application appelle la méthode **sendQueueMessage** sur l’objet **ServiceBusService** . Aux messages envoyés à (et reçus à partir de) files d’attente de Bus de Service sont des objets **BrokeredMessage** , ont un ensemble de propriétés standards (par exemple, **étiquette** et **TimeToLive**), un dictionnaire est utilisé pour inclure des propriétés personnalisées spécifiques à l’application et un corps de données d’application arbitraire. Une application peut définir le corps du message en passant une chaîne en tant que le message. Les propriétés standards requises sont remplies avec les valeurs par défaut.

L’exemple suivant montre comment envoyer un message de test dans la file nommée `myqueue` à l’aide de **sendQueueMessage**:

```
var message = {
    body: 'Test message',
    customProperties: {
        testproperty: 'TestValue'
    }};
serviceBusService.sendQueueMessage('myqueue', message, function(error){
    if(!error){
        // message sent
    }
});
```

Files d’attente Bus des services prend en charge une taille maximale des messages de la [couche Standard](service-bus-premium-messaging.md) 256 Ko et 1 Mo dans la [couche Premium](service-bus-premium-messaging.md). L’en-tête, qui inclut les propriétés d’application standard et personnalisées, peut avoir une taille maximale de 64 Ko. Il n’existe aucune limite du nombre de messages conservés dans une file d’attente, mais il existe une lettrine sur la taille totale des messages émanant d’une file d’attente. Cette taille file d’attente est définie au moment de la création, avec un maximum de 5 Go. Pour plus d’informations sur les quotas, voir [les quotas de Bus des services][].

## <a name="receive-messages-from-a-queue"></a>Recevoir des messages à partir d’une file d’attente

Les messages provenant d’une file d’attente en utilisant la méthode **receiveQueueMessage** sur l’objet **ServiceBusService** . Par défaut, les messages sont supprimés de la file d’attente dès qu’elles sont lues ; Toutefois, vous pouvez lire (aperçu) et verrouiller le message sans le supprimer de la file d’attente en définissant paramètre facultatif **isPeekLock** sur **true**.

Le comportement par défaut de lire et supprimer le message dans le cadre de l’opération de réception est le modèle le plus simple et convient le mieux pour les scénarios dans lesquels une application tolèrent ne pas traitement d’un message en cas de panne. Pour mieux comprendre cela, envisagez un scénario dans lequel le consommateur envoie la demande de réception, puis se bloque avant de le traiter. Étant donné que Bus des services sera avez marqué le message comme occupé, puis lorsque l’application redémarre et commence à lire des messages à nouveau, il sera ai manqués le message a été consommé avant l’incident.

Si le paramètre **isPeekLock** est défini sur **true**, la réception devient une opération de deux étages, qui permet aux applications de prise en charge qui ne peut pas tolérer messages manquants. Lorsque Service Bus reçoit une demande, il recherche le message suivant à consommer, verrous pour empêcher les autres consommateurs reçoit et renvoie à l’application. Une fois l’application fini de traiter le message (ou qu’il stocke fiable d’un traitement ultérieur), il termine la deuxième étape du processus de réception en appelant méthode **deleteMessage** et en fournissant le message à supprimer en tant que paramètre. La méthode **deleteMessage** marquer le message comme étant consommées et supprimer de la file d’attente.

L’exemple suivant montre comment recevoir et traiter des messages à l’aide de **receiveQueueMessage**. L’exemple tout d’abord reçoit et supprime un message, puis reçoit un message à l’aide **isPeekLock** la valeur **true**, puis supprime le message à l’aide de **deleteMessage**:

```
serviceBusService.receiveQueueMessage('myqueue', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
    }
});
serviceBusService.receiveQueueMessage('myqueue', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
            }
        });
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Comment gérer le blocage d’applications et messages illisibles

Bus des services fournit les fonctionnalités pour vous aider à récupérer normalement les erreurs dans votre application ou difficultés traitement d’un message. Si une application récepteur est impossible de traiter le message pour une raison quelconque, il peut appeler la méthode **unlockMessage** sur l’objet **ServiceBusService** . Cela entraînera Bus des services déverrouiller le message dans la file d’attente et rendez-le disponible pour être à nouveau, reçus par l’application prend beaucoup de même ou par une autre application prend beaucoup de.

Vous trouverez également un délai d’expiration associée à un message verrouillé dans la file d’attente, et si l’application ne parvient pas à traiter le message avant le délai d’expiration de verrouillage expire (par exemple, si l’application se bloque), puis Bus des services sera déverrouiller automatiquement le message et rendez-le disponible perçu à nouveau.

Dans le cas où l’application se bloque après traitement du message, mais avant l’appel de la méthode **deleteMessage** , puis le message sera remis à nouveau à l’application lorsqu’il redémarre. Cette option est souvent appelée **Au moins une fois que traitement**, c'est-à-dire que chaque message sera traité au moins une fois mais dans certains cas le même message peut être redistribué. Si le scénario ne peut pas tolérer traitement en double, les développeurs d’applications doivent ajouter une logique supplémentaire à son application pour gérer la remise des messages en double. Ceci est souvent obtenue à l’aide de la propriété **MessageId** du message, ce qui reste constante au sein de tentatives de remise.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les files d’attente, consultez les ressources suivantes.

-   [Abonnements, rubriques et files d’attente][]
-   Référentiel de [Azure SDK pour nœud][] GitHub
-   [Centre de développement Node.js](/develop/nodejs/)

  [Azure SDK pour nœud]: https://github.com/Azure/azure-sdk-for-node
  [Portail classique Azure]: http://manage.windowsazure.com
  
  [Service de nuage Node.js]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Abonnements, rubriques et files d’attente]: service-bus-queues-topics-subscriptions.md
  [Créer et déployer une application Node.js vers un site Web Azure]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
  [Service de nuage Node.js avec le stockage]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
  [Application Web Node.js avec stockage]: ../storage/storage-nodejs-how-to-use-table-storage.md
  [Quotas de Bus des services]: service-bus-quotas.md
 
