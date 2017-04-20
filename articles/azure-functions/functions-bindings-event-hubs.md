<properties
    pageTitle="Liaisons fonctions événement concentrateur Azure | Microsoft Azure"
    description="Comprendre l’utilisation des liaisons Azure événement concentrateur dans des fonctions Azure."
    services="functions"
    documentationCenter="na"
    authors="wesmc7777"
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
    ms.date="10/17/2016"
    ms.author="wesmc"/>

# <a name="azure-functions-event-hub-bindings"></a>Liaisons fonctions événement concentrateur Azure

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Cet article explique comment configurer et liaisons [Azure événement concentrateur](../event-hubs/event-hubs-overview.md) de code pour les fonctions Azure. Fonctions Azure prend en charge les liaisons déclencheur et de sortie pour Azure événement Hubs.

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

Si vous débutez dans Azure événement Hubs, consultez la [vue d’ensemble Azure événement concentrateur](../event-hubs/event-hubs-overview.md).

## <a name="azure-event-hub-trigger-binding"></a>Azure concentrateur événement déclencheur liaison

Un déclencheur d’Azure événement concentrateur peut servir pour répondre à un événement envoyé à un flux d’événements événement concentrateur. Vous devez avoir accès en lecture au concentrateur événement pour configurer une liaison déclencheur.

#### <a name="functionjson-for-event-hub-trigger-binding"></a>Function.JSON d’événement concentrateur déclencher liaison

Le fichier *function.json* pour un déclencheur d’Azure événement concentrateur spécifie les propriétés suivantes :

- `type`: Doit être définie sur *eventHubTrigger*.
- `name`: Le nom de la variable utilisé dans le code de fonction pour le message d’événement concentrateur. 
- `direction`: Doit être définie *dans*. 
- `path`: Le nom du concentrateur événement.
- `consumerGroup`: Il s’agit d’une propriété facultative utilisée pour définir le [groupe consommateurs](../event-hubs-overview.md#consumer-groups) utilisée pour s’abonner à des événements dans le hub. En cas d’omission, la `$Default` groupe consommateurs est utilisé. 
- `connection`: Nom d’un paramètre d’application qui contient la chaîne de connexion à l’espace de noms résidant dans le hub de l’événement. Copiez cette chaîne de connexion en cliquant sur le bouton **Informations de connexion** pour afficher l’espace de noms, et non au concentrateur d’événement.  Cette chaîne de connexion doit avoir au moins autorisations de lecture pour activer le déclencheur.

        {
          "bindings": [
            {
              "type": "eventHubTrigger",
              "name": "myEventHubMessage",
              "direction": "in",
              "path": "MyEventHub",
              "connection": "myEventHubReadConnectionString"
            }
          ],
          "disabled": false
        }

#### <a name="azure-event-hub-trigger-c-example"></a>Exemple de déclencheur c# événement concentrateur Azure
 
À l’aide de la function.json exemple ci-dessus, le corps du message événement sera enregistré à l’aide de la fonction code c# ci-dessous :
 
    using System;
    
    public static void Run(string myEventHubMessage, TraceWriter log)
    {
        log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
    }

#### <a name="azure-event-hub-trigger-f-example"></a>Exemple de déclencheur F # événement concentrateur Azure

À l’aide de la function.json exemple ci-dessus, le corps du message événement seront enregistré en utilisant le F # fonction code ci-dessous :

    let Run(myEventHubMessage: string, log: TraceWriter) =
        log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)

#### <a name="azure-event-hub-trigger-nodejs-example"></a>Déclencheur d’événement concentrateur Azure Node.js exemple
 
À l’aide de la function.json exemple ci-dessus, le corps du message événement seront enregistré en utilisant le code de la fonction Node.js ci-dessous :
 
    module.exports = function (context, myEventHubMessage) {
        context.log('Node.js eventhub trigger function processed work item', myEventHubMessage);    
        context.done();
    };


## <a name="azure-event-hub-output-binding"></a>Liaison de sortie concentrateur événement Azure

Un concentrateur événement Azure liaison de sortie est utilisé pour écrire des événements dans un flux d’événements événement concentrateur. Vous devez être autorisé envoyer à un concentrateur événement pour écrire des événements à celui-ci. 

#### <a name="functionjson-for-event-hub-output-binding"></a>liaison de sortie Function.JSON pour concentrateur d’événement

Le fichier *function.json* pour un concentrateur événement Azure de sortie liaison spécifie les propriétés suivantes :

- `type`: Doit être définie sur *eventHub*.
- `name`: Le nom de la variable utilisé dans le code de fonction pour le message d’événement concentrateur. 
- `path`: Le nom du concentrateur événement.
- `connection`: Nom d’un paramètre d’application qui contient la chaîne de connexion à l’espace de noms résidant dans le hub de l’événement. Copiez cette chaîne de connexion en cliquant sur le bouton **Informations de connexion** pour afficher l’espace de noms, et non au concentrateur d’événement.  Cette chaîne de connexion doit disposer des autorisations d’envoyer pour envoyer le message dans le flux d’événement concentrateur.
- `direction`: Doit être définie à *Déconnecter*. 

        {
          "type": "eventHub",
          "name": "outputEventHubMessage",
          "path": "myeventhub",
          "connection": "MyEventHubSend",
          "direction": "out"
        }


#### <a name="azure-event-hub-c-code-example-for-output-binding"></a>Exemple de code événement concentrateur c# de Azure pour la liaison de sortie
 
C# fonction code d’exemple suivant illustre l’écriture un événement dans un flux d’événements concentrateur de l’événement. Cet exemple représente le Hub de l’événement de sortie liaison ci-dessus appliquée à un déclencheur minuteur c#.  
 
    using System;
    
    public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
    {
        String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    
        log.Verbose(msg);   
        
        outputEventHubMessage = msg;
    }

#### <a name="azure-event-hub-f-code-example-for-output-binding"></a>Azure événement concentrateur F # code, par exemple pour la liaison de sortie

Le F # fonction code suivant illustre l’écriture un événement dans un flux d’événements concentrateur de l’événement. Cet exemple représente le Hub de l’événement de sortie liaison ci-dessus appliquée à un déclencheur minuteur c#.

    let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
        let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
        log.Verbose(msg);
        outputEventHubMessage <- msg;

#### <a name="azure-event-hub-nodejs-code-example-for-output-binding"></a>Exemple de code événement concentrateur Node.js Azure pour la liaison de sortie
 
Le code fonction Node.js suivant illustre l’écriture un événement dans un flux d’événements concentrateur de l’événement. Cet exemple représente le Hub de l’événement de sortie liaison ci-dessus appliquée à un déclencheur minuteur Node.js.  
 
    module.exports = function (context, myTimer) {
        var timeStamp = new Date().toISOString();
        
        if(myTimer.isPastDue)
        {
            context.log('TimerTriggerNodeJS1 is running late!');
        }

        context.log('TimerTriggerNodeJS1 function ran!', timeStamp);   
        
        context.bindings.outputEventHubMessage = "TimerTriggerNodeJS1 ran at : " + timeStamp;
    
        context.done();
    };

## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
