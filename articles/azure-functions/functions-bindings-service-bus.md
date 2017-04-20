<properties
    pageTitle="Azure fonctions Service Bus déclencheurs et liaisons | Microsoft Azure"
    description="Comprendre comment utiliser des déclencheurs Bus des services Azure et les liaisons dans les fonctions Azure."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="Azure fonctionne, fonctions, traitement des événements, cluster dynamique, architecture sans serveur"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="08/22/2016"
    ms.author="chrande; glenga"/>

# <a name="azure-functions-service-bus-triggers-and-bindings-for-queues-and-topics"></a>Azure fonctions Service Bus déclencheurs et liaisons pour files d’attente et rubriques

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Cet article explique comment configurer et code Bus des services Azure déclencheurs et liaisons dans les fonctions Azure. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a id="sbtrigger"></a>Déclencheur file d’attente ou rubrique Bus des services

#### <a name="functionjson"></a>Function.JSON

Le fichier *function.json* spécifie les propriétés suivantes.

- `name`: Le nom de la variable utilisé dans le code de fonction pour la file d’attente ou rubrique ou le message file d’attente ou une rubrique. 
- `queueName`: Pour file d’attente déclencheur uniquement, le nom de la file d’attente d’interrogation.
- `topicName`: Pour la rubrique déclencheur uniquement, le nom de la rubrique d’interrogation.
- `subscriptionName`: Pour la rubrique déclencheur uniquement, le nom de l’abonnement.
- `connection`: Nom d’un paramètre d’application qui contient une chaîne de connexion de Service Bus. La chaîne de connexion doit être un espace de noms de Service Bus ne pas limité à un file d’attente spécifique ou une rubrique. Si la chaîne de connexion n’ont gestion des droits, définissez la `accessRights` propriété. Si vous laissez `connection` vide, la liaison ou le déclencheur est prises en charge avec la chaîne de connexion de Service Bus par défaut pour l’application de fonction qui a été spécifiée par le paramètre d’application AzureWebJobsServiceBus.
- `accessRights`: Spécifie les droits d’accès disponibles pour la chaîne de connexion. Valeur par défaut est `manage`. La valeur `listen` si vous utilisez une chaîne de connexion qui ne fournit pas gérer les autorisations. Dans le cas contraire les fonctions runtime peut essayer et fail pour effectuer des opérations nécessitant une gestion des droits.
- `type`: Doit être définie sur *serviceBusTrigger*.
- `direction`: Doit être définie *dans*. 

Exemple *function.json* d’un déclencheur file d’attente Bus des services :

```json
{
  "bindings": [
    {
      "queueName": "testqueue",
      "connection": "MyServiceBusConnection",
      "name": "myQueueItem",
      "type": "serviceBusTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### <a name="c-code-example-that-processes-a-service-bus-queue-message"></a>Exemple de code c# qui traite un message de file d’attente Bus des services

```csharp
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

#### <a name="f-code-example-that-processes-a-service-bus-queue-message"></a>Exemple de code F # qui traite un message de file d’attente Bus des services

```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

#### <a name="nodejs-code-example-that-processes-a-service-bus-queue-message"></a>Exemple de code Node.js qui traite un message de file d’attente Bus des services

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

#### <a name="supported-types"></a>Types pris en charge

Le message de file d’attente Bus des services peut être désérialisé à un des types suivants :

* Objet (à partir de JSON)
* chaîne
* tableau d’octets 
* `BrokeredMessage`(C#) 

#### <a id="sbpeeklock"></a>Comportement PeekLock

L’exécution de fonctions reçoit un message dans `PeekLock` des appels et mode `Complete` sur le message si la fonction est terminée avec succès, ni d’appels `Abandon` si la fonction échoue. Si la fonction s’exécute plue de la `PeekLock` délai d’attente, le verrou est renouvelé automatiquement.

#### <a id="sbpoison"></a>Gestion des messages empoisonnés

Service Bus effectue son propre message poison gestion qui ne peut pas être contrôlé ou configuré dans configuration fonctions Azure ou le code. 

#### <a id="sbsinglethread"></a>Thread unique

Par défaut les fonctions runtime traite simultanément plusieurs messages en file d’attente. Pour diriger le runtime pour traiter uniquement une seule file d’attente ou le message de la rubrique à la fois, définir `serviceBus.maxConcurrrentCalls` 1 dans le fichier *host.json* . Pour plus d’informations sur le fichier *host.json* , voir [Arborescence](functions-reference.md#folder-structure) dans l’article de référence pour les développeurs et [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) dans le wiki référentiel WebJobs.Script.

## <a id="sboutput"></a>File d’attente Bus des services ou la rubrique liaison de sortie

#### <a name="functionjson"></a>Function.JSON

Le fichier *function.json* spécifie les propriétés suivantes.

- `name`: Le nom de la variable utilisé dans le code de la fonction de la file d’attente ou le message file d’attente. 
- `queueName`: Pour file d’attente déclencheur uniquement, le nom de la file d’attente d’interrogation.
- `topicName`: Pour la rubrique déclencheur uniquement, le nom de la rubrique d’interrogation.
- `subscriptionName`: Pour la rubrique déclencheur uniquement, le nom de l’abonnement.
- `connection`: Déclencher la lecture identique à celle des Bus des services.
- `accessRights`: Spécifie les droits d’accès disponibles pour la chaîne de connexion. Valeur par défaut est `manage`. La valeur `send` si vous utilisez une chaîne de connexion qui ne fournit pas gérer les autorisations. Dans le cas contraire les fonctions runtime peut essayer et fail pour effectuer des opérations nécessitant une gestion des droits, telles que la création des files d’attente.
- `type`: Doit être définie sur *serviceBus*.
- `direction`: Doit être définie à *Déconnecter*. 

Exemple *function.json* pour l’utilisation d’un déclencheur minuteur pour écrire des messages de file d’attente Bus des services :

```JSON
{
  "bindings": [
    {
      "schedule": "0/15 * * * * *",
      "name": "myTimer",
      "runsOnStartup": true,
      "type": "timerTrigger",
      "direction": "in"
    },
    {
      "name": "outputSbQueue",
      "type": "serviceBus",
      "queueName": "testqueue",
      "connection": "MyServiceBusConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### <a name="supported-types"></a>Types pris en charge

Fonctions Azure peuvent créer un message de file d’attente Bus de Service à partir d’un des types suivants.

* Objet (toujours crée un message JSON, crée le message avec un objet null si la valeur est null à la fin de la fonction)
* chaîne (crée un message si la valeur est non null expiration de la fonction)
* tableau d’octets (fonctionne de façon chaîne) 
* `BrokeredMessage`(C#, fonctionne de façon chaîne)

Pour la création de plusieurs messages dans une fonction c#, vous pouvez utiliser `ICollector<T>` ou `IAsyncCollector<T>`. Un message est créé quand vous appelez le `Add` méthode.

#### <a name="c-code-examples-that-create-service-bus-queue-messages"></a>Exemples de code c# qui créer des messages de file d’attente Bus des services

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

#### <a name="f-code-example-that-creates-a-service-bus-queue-message"></a>Exemple de code F # qui crée un message de file d’attente Bus des services

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

#### <a name="nodejs-code-example-that-creates-a-service-bus-queue-message"></a>Exemple de code Node.js qui crée un message de file d’attente Bus des services

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    
    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)] 
