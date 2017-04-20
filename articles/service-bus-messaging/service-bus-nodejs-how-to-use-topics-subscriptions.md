<properties 
    pageTitle="Comment utiliser les thèmes du Service Bus avec Node.js | Microsoft Azure" 
    description="Découvrez comment utiliser des rubriques Bus des services et abonnements dans Azure à partir d’une application Node.js." 
    services="service-bus" 
    documentationCenter="nodejs" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="sethm"/>


# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Comment utiliser Service Bus rubriques et abonnements

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Ce guide décrit comment utiliser rubriques Bus des services et abonnements à partir d’applications Node.js. Les scénarios présentés incluent **Création rubriques et abonnements**, **Création de filtres de l’abonnement**, **envoi de messages** à un sujet, **recevoir des messages à partir d’un abonnement**et **suppression des rubriques et abonnements**. Pour plus d’informations sur les abonnements et les rubriques, consultez la section [étapes suivantes](#next-steps) .

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-nodejs-application"></a>Créer une application Node.js

Créer une application Node.js vide. Pour obtenir des instructions sur la création d’une application Node.js, voir [créer et déployer une application Node.js à un Site Web Azure], [Node.js Cloud Service][] à l’aide de Windows PowerShell, ou un Site Web avec WebMatrix.

## <a name="configure-your-application-to-use-service-bus"></a>Configurez votre application pour utiliser Bus des services

Pour utiliser le Service Bus, téléchargez le package Node.js Azure. Ce package inclut un ensemble de bibliothèques qui communiquer avec les services Service Bus reste.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Nœud Package Manager (NPM) permet d’obtenir le package

1.  Utiliser une interface de ligne de commande tels que **PowerShell** (Windows), **Terminal Server** (Mac) ou **Bash** (Unix), accédez au dossier où vous avez créé votre exemple d’application.

2.  Tapez **npm installer azure** dans la fenêtre de commande, qui doit avoir pour résultat dans le résultat suivant :

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

3.  Vous pouvez exécuter manuellement la commande **ls** pour vérifier si une **nœud\_modules** dossier a été créé. Dans ce dossier rechercher le package **azure** , qui contient les bibliothèques que vous avez besoin pour accéder aux rubriques Bus des services.

### <a name="import-the-module"></a>Importer le module

Utiliser le bloc-notes ou un autre éditeur de texte, ajoutez le code suivant en haut du fichier **server.js** de l’application :

```
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>Configurer une connexion de Service Bus

Le module Azure lit les variables d’environnement AZURE\_SERVICEBUS\_espace de noms et AZURE\_SERVICEBUS\_ACCESS\_clés pour plus d’informations requises pour vous connecter au Service Bus. Si ces variables d’environnement ne sont pas définis, vous devez spécifier les informations de compte lors de l’appel **createServiceBusService**.

Pour obtenir un exemple de définir les variables d’environnement dans un fichier de configuration d’un Service Cloud Azure, voir [Service en nuage Node.js avec le stockage][].

Pour obtenir un exemple de définition des variables d’environnement dans le [portail classique Azure][] pour un site Web Azure, voir [Node.js Web Application avec du stockage][].

## <a name="create-a-topic"></a>Créer un sujet

L’objet **ServiceBusService** vous permet d’utiliser des thèmes. Le code suivant crée un objet **ServiceBusService** . Ajoutez-le dans la partie supérieure du fichier **server.js** , après l’instruction pour importer le module azure :

```
var serviceBusService = azure.createServiceBusService();
```

En appelant **createTopicIfNotExists** sur l’objet **ServiceBusService** , la rubrique spécifiée sera retournée (s’il existe), ou un nouveau sujet avec le nom spécifié est créé. Le code suivant utilise **createTopicIfNotExists** pour créer ou se connecter à la rubrique intitulée « MyTopic » :

```
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

**createServiceBusService** prend également en charge les options supplémentaires vous permettent de remplacer les paramètres de rubrique par défaut tels que la durée de vie du message ou la taille maximale rubrique. L’exemple suivant définit la taille maximale rubrique 5 Go avec une durée de vie d’une minute :

```
var topicOptions = {
        MaxSizeInMegabytes: '5120',
        DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
    if(!error){
        // topic was created or exists
    }
});
```

### <a name="filters"></a>Filtres

Opérations de filtrage facultatives peuvent être appliquées aux opérations effectuées via **ServiceBusService**. Opérations de filtrage peuvent inclure journalisation, automatiquement une nouvelle tentative, etc.. Filtres sont des objets qui implémentent une méthode avec la signature :

```
function handle (requestOptions, next)
```

Après avoir effectué prétraitement sur les options de requête, elle s’appelle `next` en passant un rappel avec la signature suivante :

```
function (returnObject, finalCallback, next)
```

Dans ce rappel et après le traitement de la **returnObject** (la réponse à la demande sur le serveur), les besoins de rappel à des options d’appel suivant s’il existe pour poursuivre le traitement des autres filtres ou simplement l’appel **finalCallback** dans le cas contraire pour retrouver l’appel de service.

Deux filtres qui implémentent la logique de nouvelle tentative sont inclus dans le Kit de développement Azure pour Node.js, **ExponentialRetryPolicyFilter** et **LinearRetryPolicyFilter**. Le code suivant crée un objet **ServiceBusService** qui utilise **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

## <a name="create-subscriptions"></a>Créer des abonnements

Abonnement à une rubrique est également créés avec l’objet **ServiceBusService** . Abonnements sont nommées et peuvent avoir un filtre facultatif qui limite l’ensemble des messages remis à la file d’attente virtuelle de l’abonnement.

> [AZURE.NOTE] Abonnements sont permanentes et continueront à exister qu’après l’avoir ou la rubrique elles sont associées, sont supprimés. Si votre application contienne une logique pour créer un abonnement, il doit tout d’abord vérifier si l’abonnement existe déjà en utilisant la méthode **getSubscription** .

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Créer un abonnement avec le filtre par défaut (MatchAll)

Le filtre **MatchAll** est le filtre par défaut qui est utilisé si aucun filtre n’est spécifié lors de la création d’un nouvel abonnement. Lorsque le filtre **MatchAll** est utilisé, tous les messages publiés dans la rubrique sont placées dans la file d’attente virtuelle de l’abonnement. L’exemple suivant crée un abonnement nommé « AllMessages » et utilise le filtre de **MatchAll** par défaut.

```
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>Créer des abonnements avec les filtres

Vous pouvez également créer des filtres qui permettent à portée de messages envoyés à un sujet doit figurer au sein d’un abonnement sujet spécifique.

Le type de filtre pris en charge par les abonnements plus souple est le **SqlFilter**, qui met en œuvre un sous-ensemble de SQL92. Filtres SQL fonctionnent sur les propriétés des messages qui sont publiés dans la rubrique. Pour plus d’informations sur les expressions qui peuvent être utilisées avec un filtre SQL, passez en revue les [SqlFilter.SqlExpression] [ SqlFilter.SqlExpression] syntaxe.

Filtres peuvent être ajoutés à un abonnement à l’aide de la méthode **createRule** de l’objet **ServiceBusService** . Cette méthode vous permet d’ajouter de nouveaux filtres à un abonnement existant.

> [AZURE.NOTE] Étant donné que le filtre par défaut est appliqué automatiquement à tous les nouveaux abonnements, vous devez d’abord supprimer le filtre par défaut ou la **MatchAll** remplace tous les autres filtres que vous pouvez spécifier. Vous pouvez supprimer la règle par défaut en utilisant la méthode **deleteRule** de l’objet **ServiceBusService** .

L’exemple suivant crée un abonnement nommé `HighMessages` avec un **SqlFilter** qui sélectionne uniquement les messages qui ont une propriété personnalisée **format** égal à 3 :

```
serviceBusService.createSubscription('MyTopic', 'HighMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'HighMessages', 
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber > 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic', 
            'HighMessages', 
            'HighMessageFilter', 
            ruleOptions, 
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

De même, l’exemple suivant crée un abonnement nommé `LowMessages` avec un **SqlFilter** qui sélectionne uniquement les messages qui ont une propriété **format** inférieur ou égal à 3 :

```
serviceBusService.createSubscription('MyTopic', 'LowMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'LowMessages', 
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber <= 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic', 
            'LowMessages', 
            'LowMessageFilter', 
            ruleOptions, 
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

Lorsqu’un message est maintenant envoyé à `MyTopic`, elle est envoyée aux récepteurs d’abonné à la `AllMessages` abonnement rubrique et sélectivement remis aux récepteurs d’abonné à la `HighMessages` et `LowMessages` abonnements rubrique (en fonction du contenu du message).

## <a name="how-to-send-messages-to-a-topic"></a>Comment envoyer des messages à un sujet

Pour envoyer un message à un sujet de Bus des services, votre application doit utiliser la méthode **sendTopicMessage** de l’objet **ServiceBusService** .
Les messages envoyés vers des rubriques Bus des services sont **BrokeredMessage** objets.
Objets **BrokeredMessage** comportent un ensemble de propriétés standard (par exemple, **étiquette** et **TimeToLive**), un dictionnaire est utilisé pour inclure des propriétés spécifiques application personnalisée et un corps de données de type chaîne. Une application peut définir le corps du message en passant une valeur de chaîne à la **sendTopicMessage** et les propriétés requises standards seront remplies par les valeurs par défaut.

L’exemple suivant montre comment envoyer cinq messages à 'MyTopic' de test. Notez que la valeur de propriété **format** de chaque message varie selon l’itération de la boucle (Cela permet de déterminer les abonnements reçoivent) :

```
var message = {
    body: '',
    customProperties: {
        messagenumber: 0
    }
}

for (i = 0;i < 5;i++) {
    message.customProperties.messagenumber=i;
    message.body='This is Message #'+i;
    serviceBusService.sendTopicMessage(topic, message, function(error) {
      if (error) {
        console.log(error);
      }
    });
}
```

Rubriques Bus des services prend en charge une taille maximale des messages de la [couche Standard](service-bus-premium-messaging.md) 256 Ko et 1 Mo dans la [couche Premium](service-bus-premium-messaging.md). L’en-tête, qui inclut les propriétés d’application standard et personnalisées, peut avoir une taille maximale de 64 Ko. Il n’existe aucune limite du nombre de messages conservés dans une rubrique mais il existe une lettrine sur la taille totale des messages émanant d’une rubrique. La taille de cette rubrique est définie au moment de la création, avec un maximum de 5 Go.

## <a name="receive-messages-from-a-subscription"></a>Recevoir des messages à partir d’un abonnement

Les messages provenant d’un abonnement à l’aide de la méthode **receiveSubscriptionMessage** sur l’objet **ServiceBusService** . Par défaut, les messages sont supprimés de l’abonnement dès qu’elles sont lues ; Toutefois, vous pouvez lire (aperçu) et verrouiller le message sans supprimer de l’abonnement en définissant paramètre facultatif **isPeekLock** sur **true**.

Le comportement par défaut de lire et supprimer le message dans le cadre de l’opération de réception est le modèle le plus simple et convient le mieux pour les scénarios dans lesquels une application tolèrent ne pas traitement d’un message en cas de panne. Pour mieux comprendre cela, envisagez un scénario dans lequel le consommateur envoie la demande de réception, puis se bloque avant de le traiter. Étant donné que Bus des services sera avez marqué le message comme occupé, puis lorsque l’application redémarre et commence à lire des messages à nouveau, il sera ai manqués le message a été consommé avant l’incident.

Si le paramètre **isPeekLock** est défini sur **true**, la réception devient une opération de deux étages, qui permet aux applications de prise en charge qui ne peut pas tolérer messages manquants. Lorsque Service Bus reçoit une demande, il recherche le message suivant à consommer, verrous pour empêcher les autres consommateurs reçoit et renvoie à l’application.
Une fois l’application fini de traiter le message (ou qu’il stocke fiable d’un traitement ultérieur), il termine la deuxième étape du processus de réception en appelant méthode **deleteMessage** et en fournissant le message à supprimer en tant que paramètre. La méthode **deleteMessage** marquer le message comme étant consommées et supprimer de l’abonnement.

L’exemple suivant montre comment les messages pouvant être reçus et traitement à l’aide de **receiveSubscriptionMessage**. L’exemple reçoit d’abord et supprime un message de l’abonnement 'LowMessages', puis reçoit un message de l’abonnement « HighMessages » à l’aide de **isPeekLock** défini sur true. Puis supprime le message à l’aide de **deleteMessage**:

```
serviceBusService.receiveSubscriptionMessage('MyTopic', 'LowMessages', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
        console.log(receivedMessage);
    }
});
serviceBusService.receiveSubscriptionMessage('MyTopic', 'HighMessages', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        console.log(lockedMessage);
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
                console.log('message has been deleted.');
            }
        }
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Comment gérer le blocage d’applications et messages illisibles

Bus des services fournit les fonctionnalités pour vous aider à récupérer normalement les erreurs dans votre application ou difficultés traitement d’un message. Si une application récepteur est impossible de traiter le message pour une raison quelconque, il peut appeler la méthode **unlockMessage** sur l’objet **ServiceBusService** . Cela entraînera Bus des services déverrouiller le message au sein de l’abonnement et le rendre disponible pour être à nouveau, reçus par l’application prend beaucoup de même ou par une autre application prend beaucoup de.

Vous trouverez également un délai d’expiration associée à un message verrouillé au sein de l’abonnement, et si l’application ne parvient pas à traiter le message avant le délai d’expiration de verrouillage expire (par exemple, si l’application se bloque), puis Service Bus déverrouiller automatiquement le message et rend disponible pour être reçus à nouveau.

Dans le cas où l’application se bloque après traitement du message, mais avant l’appel de la méthode **deleteMessage** , puis le message sera remis à nouveau à l’application lorsqu’il redémarre. Cette option est souvent appelée **Au moins une fois que traitement**, c'est-à-dire que chaque message sera traité au moins une fois mais dans certains cas le même message peut être redistribué. Si le scénario ne peut pas tolérer traitement en double, les développeurs d’applications doivent ajouter une logique supplémentaire à son application pour gérer la remise des messages en double. Ceci est souvent obtenue à l’aide de la propriété **MessageId** du message, ce qui reste constante au sein de tentatives de remise.

## <a name="delete-topics-and-subscriptions"></a>Supprimer des rubriques et des abonnements

Rubriques d’abonnements sont permanentes et doivent être explicitement supprimés à partir du [portail classique Azure][] ou par programme.
L’exemple suivant montre comment supprimer la rubrique nommée `MyTopic`:

    serviceBusService.deleteTopic('MyTopic', function (error) {
        if (error) {
            console.log(error);
        }
    });

Suppression d’un sujet va également supprimer tous les abonnements qui sont enregistrés avec la rubrique. Abonnements peuvent également être supprimés séparément. L’exemple suivant illustre la suppression d’un abonnement nommé `HighMessages` à partir de la `MyTopic` rubrique :

    serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
        if(error) {
            console.log(error);
        }
    });

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris les notions de base des rubriques Bus des services, suivez ces liens pour en savoir plus.

-   Voir [files d’attente, de sujets et abonnements][].
-   Référence des API pour [SqlFilter][].
-   Visitez le référentiel [Azure SDK pour nœud][] sur GitHub.

  [Azure SDK pour nœud]: https://github.com/Azure/azure-sdk-for-node
  [Portail classique Azure]: https://manage.windowsazure.com
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Abonnements, rubriques et files d’attente]: service-bus-queues-topics-subscriptions.md
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Service de nuage Node.js]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Créer et déployer une application Node.js à un Site Web Azure]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
  [Service de nuage Node.js avec le stockage]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Application Web Node.js avec stockage]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
 
