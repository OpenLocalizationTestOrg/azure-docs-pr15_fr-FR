<properties
    pageTitle="Azure déclencheurs de fonctions et des liaisons | Microsoft Azure"
    description="Comprendre comment utiliser des déclencheurs et des liaisons dans les fonctions Azure."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="Azure fonctions, fonctions, traitement de l’événement, webhooks, cluster dynamique, architecture sans serveur"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="10/27/2016"
    ms.author="chrande"/>

# <a name="azure-functions-triggers-and-bindings-developer-reference"></a>Azure référence du développeur déclencheurs et des liaisons de fonctions


Cette rubrique fournit référence générale pour déclencheurs et les liaisons. Il inclut des fonctionnalités de liaison avancée et syntaxe prise en charge par tous les types de liaison.  

Si vous cherchez des informations détaillées autour de configuration et le codage de déclencheur ou liaison d’un type spécifique, vous souhaiterez peut-être cliquer sur l’un des déclencheur ou liaisons ci-dessous à la place :

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)] 

Ces articles part du principe que vous avez lu la [référence du développeur Azure fonctions](functions-reference.md)et les articles de référence pour les développeurs [c#](functions-reference-csharp.md), [F #](functions-reference-fsharp.md)ou [Node.js](functions-reference-node.md) .



## <a name="overview"></a>Vue d’ensemble

Déclencheurs sont utilisés pour déclencher votre code personnalisé d’événements réponses. Ils permettent de répondre aux événements via la plateforme Azure ou local. Liaisons représentent les données meta nécessaires utilisées pour connecter votre code déclencheur souhaité ou entrée correspondante ou données de sortie.

Pour obtenir une meilleure idée des liaisons différentes que vous pouvez intégrer avec votre application de la fonction Azure, consultez le tableau suivant.

[AZURE.INCLUDE [dynamic compute](../../includes/functions-bindings.md)]  

Pour mieux comprendre déclenche et liaisons en règle générale, supposons que vous voulez exécuter du code au processus d’un nouvel élément déplacé dans une file d’attente de stockage Azure. Fonctions Azure fournit un déclencheur d’Azure file d’attente en charge de cette. Vous aurez besoin, les informations suivantes pour contrôler la file d’attente :
 
- Le compte de stockage où se trouve la file d’attente.
- Le nom de la file d’attente.
- Un nom de la variable que votre code utiliseriez pour faire référence au nouvel élément a été supprimé dans la file d’attente.  
 
Un déclencheur file d’attente liaison contient ces informations pour une fonction d’Azure. Le fichier *function.json* pour chaque fonction contient toutes les liaisons associées.  Voici un exemple *function.json* contenant une file d’attente déclencher la liaison. 

    {
      "bindings": [
        {
          "name": "myNewUserQueueItem",
          "type": "queueTrigger",
          "direction": "in",
          "queueName": "queue-newusers",
          "connection": "MY_STORAGE_ACCT_APP_SETTING"
        }
      ],
      "disabled": false
    }

Votre code peut envoyer des différents types de sortie selon la façon dont le nouvel élément de file d’attente est traité. Par exemple, vous souhaiterez écrire un nouvel enregistrement à une table de stockage Azure.  Pour ce faire, vous pouvez configurer une liaison de sortie à une table de stockage Azure. Voici un exemple *function.json* qui inclut une liaison de sortie de tableau de stockage qui peut être utilisée avec un déclencheur file d’attente. 


    {
      "bindings": [
        {
          "name": "myNewUserQueueItem",
          "type": "queueTrigger",
          "direction": "in",
          "queueName": "queue-newusers",
          "connection": "MY_STORAGE_ACCT_APP_SETTING"
        },
        {
          "type": "table",
          "name": "myNewUserTableBinding",
          "tableName": "newUserTable",
          "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING",
          "direction": "out"
        }
      ],
      "disabled": false
    }


La fonction c# suivante répond à un nouvel élément qui sont déplacé dans la file d’attente et écrit une nouvelle entrée d’utilisateur dans une table de stockage Azure.

    #r "Newtonsoft.Json"

    using System;
    using Newtonsoft.Json;

    public static async Task Run(string myNewUserQueueItem, IAsyncCollector<Person> myNewUserTableBinding, 
                                    TraceWriter log)
    {
        // In this example the queue item is a JSON string representing an order that contains the name, 
        // address and mobile number of the new customer.
        dynamic order = JsonConvert.DeserializeObject(myNewUserQueueItem);
    
        await myNewUserTableBinding.AddAsync(
            new Person() { 
                PartitionKey = "Test", 
                RowKey = Guid.NewGuid().ToString(), 
                Name = order.name,
                Address = order.address,
                MobileNumber = order.mobileNumber }
            );
    }
    
    public class Person
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Name { get; set; }
        public string Address { get; set; }
        public string MobileNumber { get; set; }
    }

Pour plus d’exemples de code et des informations plus spécifiques en ce qui concerne les types de stockage Azure qui sont prises en charge, voir [fonctions Azure déclencheurs et des liaisons pour le stockage Azure](functions-bindings-storage.md).


Pour utiliser les fonctionnalités de liaison plus avancées dans le portail Azure, cliquez sur l’option de **l’Éditeur avancé** sous l’onglet **intégrer** de votre fonction. L’éditeur avancé permet de modifier le *function.json* directement dans le portail.

## <a name="dynamic-parameter-binding"></a>Liaison de paramètre dynamique 

Au lieu d’un paramètre pour vos propriétés de liaison de sortie de configuration statique, vous pouvez configurer les paramètres pour être dynamiquement liée aux données qui fait partie d’une liaison d’entrée de votre déclencheur. Prenons un scénario où de nouveaux traitement des commandes à l’aide d’une file d’attente de stockage Azure. Chaque nouvel élément file d’attente est une chaîne JSON contenant au moins les propriétés suivantes :

    {
      name : "Customer Name",
      address : "Customer's Address".
      mobileNumber : "Customer's mobile number."
    }

Vous souhaiterez peut-être envoyer au client un message texte SMS à l’aide de votre compte Twilio comme une mise à jour que l’ordre a été reçu.  Vous pouvez configurer le `body` et `to` liaison lier dynamiquement de sortie du champ de votre Twilio la `name` et `mobileNumber` qui ont été partie de l’entrée comme suit.

    {
      "name": "myNewOrderItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "queue-newOrders",
      "connection": "orders_STORAGE"
    },
    {
      "type": "twilioSms",
      "name": "message",
      "accountSid": "TwilioAccountSid",
      "authToken": "TwilioAuthToken",
      "to": "{mobileNumber}",
      "from": "+XXXYYYZZZZ",
      "body": "Thank you {name}, your order was received",
      "direction": "out"
    },
 
Votre code de la fonction ne possède initialisation le paramètre de sortie comme suit. Pendant l’exécution les propriétés de sortie seront liées aux données d’entrée souhaitées.

C#

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the message variable.
    message = new SMSMessage();

    // When using dynamic parameter binding no need to set this in code.
    // message.body = msg;
    // message.to = myNewOrderItem.mobileNumber

Node.js

    context.bindings.message = {
        // When using dynamic parameter binding no need to set this in code.
        //body : msg,
        //to : myNewOrderItem.mobileNumber
    };




## <a name="random-guids"></a>GUID aléatoire

Fonctions Azure fournit une syntaxe pour générer des GUID aléatoire avec vos liaisons. La syntaxe de la liaison suivante écrire la sortie dans un nouveau BLOB avec un nom unique dans un conteneur de stockage Azure : 

    {
      "type": "blob",
      "name": "blobOutput",
      "direction": "out",
      "path": "my-output-container/{rand-guid}"
    }



## <a name="returning-a-single-output"></a>Retourne une seule sortie

Dans les cas où votre code de la fonction retourne une seule sortie, vous pouvez utiliser une liaison de sortie nommée `$return` pour conserver une signature de fonction plus naturel dans votre code. Cet peut uniquement être utilisé avec langues qui prennent en charge d’une valeur de retour (c#, Node.js, F #). La liaison serait similaire à la liaison de sortie blob suivantes qui est utilisée avec un déclencheur file d’attente.

    {
      "bindings": [
        {
          "type": "queueTrigger",
          "name": "input",
          "direction": "in",
          "queueName": "test-input-node"
        },
        {
          "type": "blob",
          "name": "$return",
          "direction": "out",
          "path": "test-output-node/{id}"
        }
      ]
    }


Le code c# suivant retourne la sortie plus naturellement sans utiliser un `out` paramètre dans la signature de fonction.


    public static string Run(WorkItem input, TraceWriter log)
    {
        string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
        log.Info($"C# script processed queue message. Item={json}");
        return json;
    }

    // Async example
    public static Task<string> Run(WorkItem input, TraceWriter log)
    {
        string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
        log.Info($"C# script processed queue message. Item={json}");
        return json;
    }


Cette approche est présentée ci-dessous avec Node.js.

    module.exports = function (context, input) {
        var json = JSON.stringify(input);
        context.log('Node.js script processed queue message', json);
        context.done(null, json);
    }

F # fourni ci-dessous, par exemple.

    let Run(input: WorkItem, log: TraceWriter) =
        let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
        log.Info(sprintf "F# script processed queue message '%s'" json)
        json

Cela peut également être utilisé avec plusieurs paramètres de sortie par désigner une seule sortie avec `$return`.


## <a name="route-support"></a>Prise en charge de l’itinéraire

Par défaut lorsque vous créez une fonction d’un déclencheur HTTP ou WebHook, la fonction est dédiée avec un itinéraire de l’écran :

    http://<yourapp>.azurewebsites.net/api/<funcname> 

Vous pouvez personnaliser cet itinéraire à l’aide de l’option `route` propriété sur le déclencheur HTTP d’entrée de liaison. Par exemple, le fichier *function.json* suivant définit un `route` propriété d’un déclencheur HTTP :

    {
      "bindings": [
        {
          "type": "httpTrigger",
          "name": "req",
          "direction": "in",
          "methods": [ "get" ],
          "route": "products/{category:alpha}/{id:int?}"
        },
        {
          "type": "http",
          "name": "res",
          "direction": "out"
        }
      ]
    }

À l’aide de cette configuration, la fonction devient dédiée avec l’itinéraire suivant au lieu de l’itinéraire d’origine.

    http://<yourapp>.azurewebsites.net/api/products/electronics/357

Cela permet au code de fonction prendre en charge des deux paramètres dans la zone Adresse, `category` et `id`. Vous pouvez utiliser une [Contrainte d’itinéraire API Web](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) avec vos paramètres. Le code de fonction c# suivant utilise les deux paramètres.

    public static Task<HttpResponseMessage> Run(HttpRequestMessage request, string category, int? id, 
                                                    TraceWriter log)
    {
        if (id == null)
           return  req.CreateResponse(HttpStatusCode.OK, $"All {category} items were requested.");
        else
           return  req.CreateResponse(HttpStatusCode.OK, $"{category} item with id = {id} has been requested.");
    }

Voici le code de la fonction Node.js à utiliser les mêmes paramètres d’itinéraire.

    module.exports = function (context, req) {

        var category = context.bindingData.category;
        var id = context.bindingData.id;
    
        if (!id) {
            context.res = {
                // status: 200, /* Defaults to 200 */
                body: "All " + category + " items were requested."
            };
        }
        else {
            context.res = {
                // status: 200, /* Defaults to 200 */
                body: category + " item with id = " + id + " was requested."
            };
        }
    
        context.done();
    } 

Par défaut, tous les chemins de fonction sont précédés *d’api*. Vous pouvez également personnaliser ou supprimer le préfixe à l’aide du `http.routePrefix` propriété dans votre fichier *host.json* . L’exemple suivant supprime le préfixe d’itinéraire *api* à l’aide d’une chaîne vide pour le préfixe dans le fichier *host.json* .

    {
      "http": {
        "routePrefix": ""
      }
    }

Pour plus d’informations sur la façon de mettre à jour le fichier *host.json* pour votre fonction, voir [comment mettre à jour l’application de la fonction des fichiers](functions-reference.md#fileupdate). 

En ajoutant cette configuration, la fonction est désormais dédiée avec l’itinéraire suivant :

    http://<yourapp>.azurewebsites.net/products/electronics/357

Pour plus d’informations sur les autres propriétés que vous pouvez configurer dans votre fichier *host.json* , voir [informations de référence host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json).





## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

* [Test d’une fonction](functions-test-a-function.md)
* [Échelle une fonction](functions-scale.md)
