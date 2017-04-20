<properties
    pageTitle="Liaisons DocumentDB fonctions Azure | Microsoft Azure"
    description="Comprendre l’utilisation des liaisons Azure DocumentDB dans les fonctions Azure."
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

# <a name="azure-functions-documentdb-bindings"></a>Liaisons DocumentDB fonctions Azure

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Cet article explique comment configurer et liaisons Azure DocumentDB de code dans des fonctions Azure. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a id="docdbinput"></a>Liaison de saisie DocumentDB Azure

Liaisons d’entrée peuvent charger un document à partir d’une collection de sites DocumentDB et passez directement à votre liaison. L’id de document peut être déterminé en fonction du déclencheur ayant appelé la fonction. Dans une fonction c#, toutes les modifications apportées à l’enregistrement être envoyées automatiquement à la collection lorsque la fonction a quitté avec succès.

#### <a name="functionjson-for-documentdb-input-binding"></a>Function.JSON pour la liaison d’entrée DocumentDB

Le fichier *function.json* fournit les propriétés suivantes :

- `name`: Nom variable utilisé dans le code de fonction pour le document.
- `type`: doit être définie sur « documentdb ».
- `databaseName`: La base de données contenant le document.
- `collectionName`: La collection de sites contenant le document.
- `id`: L’Id du document à récupérer. Prend en charge cette propriété liaisons similaires à « {queueTrigger} », qui utilise la valeur de chaîne du message file d’attente en tant que le document identifiant.
- `connection`: Cette chaîne doit être un jeu de paramètre d’Application au point de terminaison pour votre compte DocumentDB. Si vous choisissez votre compte à partir de l’onglet intégrer, un nouveau paramètre d’application est créé pour vous avec un nom qui prend la forme suivante, yourAccount_DOCUMENTDB. Si vous avez besoin créer manuellement le paramètre d’application, la chaîne de connexion doit prendre la forme suivante, AccountEndpoint =<Endpoint for your account>; AccountKey =<Your primary access key>;.
- « direction : doit être définie sur *« in »*.

Exemple *function.json*:
 
    {
      "bindings": [
        {
          "name": "document",
          "type": "documentdb",
          "databaseName": "MyDatabase",
          "collectionName": "MyCollection",
          "id" : "{queueTrigger}",
          "connection": "MyAccount_DOCUMENTDB",     
          "direction": "in"
        }
      ],
      "disabled": false
    }

#### <a name="azure-documentdb-input-code-example-for-a-c-queue-trigger"></a>Exemple de saisie code DocumentDB Azure d’un déclencheur c# file d’attente
 
À l’aide de la function.json exemple ci-dessus, la liaison d’entrée DocumentDB est récupérer le document avec l’id qui correspond à la chaîne de message file d’attente et passer au paramètre « document ». Si ce document n’est pas trouvé, le paramètre « document » sera null. Le document est ensuite mis à jour avec la nouvelle valeur de texte lors de la fermeture de la fonction.
 
    public static void Run(string myQueueItem, dynamic document)
    {   
        document.text = "This has changed.";
    }

#### <a name="azure-documentdb-input-code-example-for-an-f-queue-trigger"></a>Exemple de saisie code DocumentDB Azure pour un déclencheur F # file d’attente

À l’aide de la function.json exemple ci-dessus, la liaison d’entrée DocumentDB est récupérer le document avec l’id qui correspond à la chaîne de message file d’attente et passer au paramètre « document ». Si ce document n’est pas trouvé, le paramètre « document » sera null. Le document est ensuite mis à jour avec la nouvelle valeur de texte lors de la fermeture de la fonction.

    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, document: obj) =
        document?text <- "This has changed."

Vous devrez un `project.json` fichier qui utilise NuGet pour spécifier la `FSharp.Interop.Dynamic` et `Dynamitey` packages comme dépendances du package, comme ceci :

    {
      "frameworks": {
        "net46": {
          "dependencies": {
            "Dynamitey": "1.0.2",
            "FSharp.Interop.Dynamic": "3.0.0"
          }
        }
      }
    }

Cette option NuGet pour récupérer vos dépendances et les référencer dans votre script.

#### <a name="azure-documentdb-input-code-example-for-a-nodejs-queue-trigger"></a>Exemple de saisie code DocumentDB Azure d’un déclencheur file d’attente Node.js
 
À l’aide de la function.json exemple ci-dessus, la liaison d’entrée DocumentDB récupérer le document avec l’id qui correspond à la chaîne de message file d’attente et passer à la `documentIn` liaison de la propriété. Dans les fonctions Node.js, les documents mis à jour ne sont pas envoyées à la collection. Toutefois, vous pouvez passer la liaison d’entrée directement dans une sortie DocumentDB liaison nommée `documentOut` pour prendre en charge les mises à jour. Cet exemple de code met à jour la propriété de texte du document d’entrée et la définit comme le document de sortie.
 
    module.exports = function (context, input) {   
        context.bindings.documentOut = context.bindings.documentIn;
        context.bindings.documentOut.text = "This was updated!";
        context.done();
    };

## <a id="docdboutput"></a>Liaisons de sortie DocumentDB Azure

Vos fonctions peuvent écrire JSON liaison de sortie des documents à une base de données Azure DocumentDB en utilisant le **Document de DocumentDB Azure** . Pour plus d’informations sur Azure DocumentDB passez en revue [présentation DocumentDB](../documentdb/documentdb-introduction.md) et le [didacticiel mise en route](../documentdb/documentdb-get-started.md).

#### <a name="functionjson-for-documentdb-output-binding"></a>liaison de sortie Function.JSON pour DocumentDB

Le fichier function.json fournit les propriétés suivantes :

- `name`: Nom variable utilisé dans le code de la fonction pour le nouveau document.
- `type`: doit être définie sur *« documentdb »*.
- `databaseName`: La base de données contenant la collection de sites où le nouveau document sera créé.
- `collectionName`: La collection de sites où le nouveau document sera créé.
- `createIfNotExists`: Il s’agit d’une valeur booléenne pour indiquer si la collection de sites doit être créée s’il n’existe pas. La valeur par défaut est *false*. La raison pour laquelle c’est nouveau collections sont créées avec débit réservé, qui applique des tarifs implications. Pour plus d’informations, visitez la [page de tarification](https://azure.microsoft.com/pricing/details/documentdb/).
- `connection`: Cette chaîne doit être qu'un **Paramètre d’Application** défini au point de terminaison pour votre compte DocumentDB. Si vous choisissez votre compte à partir de l’onglet **intégrer** , un nouveau paramètre d’application est créé pour vous avec un nom qui prend la forme suivante, `yourAccount_DOCUMENTDB`. Si vous avez besoin créer manuellement le paramètre d’application, la chaîne de connexion doit prendre la forme suivante, `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>;`. 
- `direction`: doit être définie sur *« arrière »*. 
 
Exemple function.json :

    {
      "bindings": [
        {
          "name": "document",
          "type": "documentdb",
          "databaseName": "MyDatabase",
          "collectionName": "MyCollection",
          "createIfNotExists": false,
          "connection": "MyAccount_DOCUMENTDB",
          "direction": "out"
        }
      ],
      "disabled": false
    }


#### <a name="azure-documentdb-output-code-example-for-a-nodejs-queue-trigger"></a>Exemple de code Azure DocumentDB sortie d’un déclencheur file d’attente Node.js

    module.exports = function (context, input) {
       
        context.bindings.document = {
            text : "I'm running in a Node function! Data: '" + input + "'"
        }   
     
        context.done();
    };

Le document de sortie :

    {
      "text": "I'm running in a Node function! Data: 'example queue data'",
      "id": "01a817fe-f582-4839-b30c-fb32574ff13f"
    }
 

#### <a name="azure-documentdb-output-code-example-for-an-f-queue-trigger"></a>Exemple de code Azure DocumentDB sortie d’un déclencheur F # file d’attente

    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, document: obj) =
        document?text <- (sprintf "I'm running in an F# function! %s" myQueueItem)

#### <a name="azure-documentdb-output-code-example-for-a-c-queue-trigger"></a>Exemple de code Azure DocumentDB sortie d’un déclencheur c# file d’attente


    using System;

    public static void Run(string myQueueItem, out object document, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
       
        document = new {
            text = $"I'm running in a C# function! {myQueueItem}"
        };
    }


#### <a name="azure-documentdb-output-code-example-setting-file-name"></a>Nom du fichier DocumentDB sortie code exemple paramètre Azure

Si vous souhaitez définir le nom du document dans la fonction, vous venez de définir la `id` valeur.  Par exemple, si le contenu d’un employé JSON a été qui sont déplacé dans la file d’attente semblable à ce qui suit :

    {
      "name" : "John Henry",
      "employeeId" : "123456",
      "address" : "A town nearby"
    }

Vous pouvez utiliser le code c# suivant dans une fonction de déclenchement file d’attente : 
    
    #r "Newtonsoft.Json"
    
    using System;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    
    public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
        
        dynamic employee = JObject.Parse(myQueueItem);
        
        employeeDocument = new {
            id = employee.name + "-" + employee.employeeId,
            name = employee.name,
            employeeId = employee.employeeId,
            address = employee.address
        };
    }

Ou le code F # équivalent :

    open FSharp.Interop.Dynamic
    open Newtonsoft.Json

    type Employee = {
        id: string
        name: string
        employeeId: string
        address: string
    }

    let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
        log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
        let employee = JObject.Parse(myQueueItem)
        employeeDocument <-
            { id = sprintf "%s-%s" employee?name employee?employeeId
              name = employee?name
              employeeId = employee?id
              address = employee?address }

Exemple de résultat :

    {
      "id": "John Henry-123456",
      "name": "John Henry",
      "employeeId": "123456",
      "address": "A town nearby"
    }

## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
