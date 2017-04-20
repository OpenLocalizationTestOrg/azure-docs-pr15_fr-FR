<properties
    pageTitle="Liaisons HTTP de fonctions et webhook Azure | Microsoft Azure"
    description="Comprendre comment utiliser des déclencheurs HTTP et webhook et les liaisons dans les fonctions Azure."
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
    ms.date="08/22/2016"
    ms.author="chrande"/>

# <a name="azure-functions-http-and-webhook-bindings"></a>Liaisons HTTP de fonctions et webhook Azure

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Cet article explique comment configurer et code HTTP et webhook déclencheurs et liaisons dans les fonctions Azure. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a name="functionjson-for-http-and-webhook-bindings"></a>Function.JSON pour les liaisons HTTP et webhook

Le fichier *function.json* fournit des propriétés relatives à la demande et la réponse.

Propriétés de la requête HTTP :

- `name`: Nom variable utilisé dans le code de fonction pour l’objet de requête (ou le corps de la requête dans le cas des fonctions Node.js).
- `type`: Doit être définie sur *httpTrigger*.
- `direction`: Doit être définie *dans*. 
- `webHookType`: Pour WebHook déclencheurs, les valeurs valides sont *github*, la *marge*et *genericJson*. Pour un déclencheur d’HTTP qui n’est pas un WebHook, définir cette propriété sur une chaîne vide. Pour plus d’informations sur WebHooks, consultez la section [déclencheurs WebHook](#webhook-triggers) suivante.
- `authLevel`: Ne s’applique pas aux déclencheurs WebHook. La valeur « fonctionner » afin d’exiger la clé API, « anonyme » à portée l’API majeur, ou « administrateur » afin d’exiger la clé API principal. Pour plus d’informations, voir sous les [clés de l’API](#apikeys) .

Propriétés de la réponse HTTP :

- `name`: Nom variable utilisé dans le code de fonction pour l’objet de réponse.
- `type`: Doit être définie sur *http*.
- `direction`: Doit être définie à *Déconnecter*. 
 
Exemple *function.json*:

```json
{
  "bindings": [
    {
      "webHookType": "",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

## <a name="webhook-triggers"></a>Déclencheurs WebHook

Un déclencheur WebHook est un HTTP qui contient les fonctionnalités suivantes conçues pour WebHooks :

* Pour les fournisseurs de WebHook spécifiques (actuellement GitHub et marge sont pris en charge), le runtime fonctions valide la signature du fournisseur.
* Pour les fonctions Node.js, le runtime fonctions fournit le corps de la requête à la place de l’objet de requête. Il n’existe aucune gestion particulière pour les fonctions c#, car contrôler ce qui est fourni en spécifiant le type de paramètre. Si vous spécifiez `HttpRequestMessage` obtenir de l’objet de requête. Si vous spécifiez un type de POCO, l’exécution de fonctions tente d’analyser un objet JSON dans le corps de la demande pour renseigner les propriétés d’objet.
* Pour déclencher un WebHook fonction la requête HTTP doit inclure une clé API. Pour les déclencheurs non - WebHook HTTP, cette exigence est facultative.

Pour plus d’informations sur la façon de configurer un GitHub WebHook, voir [GitHub développeur - WebHooks création](http://go.microsoft.com/fwlink/?LinkID=761099&clcid=0x409).

## <a name="url-to-trigger-the-function"></a>URL pour déclencher la fonction

Pour déclencher la lecture d’une fonction, vous envoyez une demande HTTP à une URL est une combinaison de l’URL de l’application de fonction et le nom de la fonction :

```
 https://{function app name}.azurewebsites.net/api/{function name} 
```

## <a name="api-keys"></a>Touches de l’API

Par défaut, une clé API doit être incluse dans une requête HTTP déclencher une fonction HTTP ou WebHook. La clé peut être incluse dans une variable chaîne de requête nommée `code`, ou elle peut être inclus dans une `x-functions-key` en-tête HTTP. Pour les fonctions non WebHook, vous pouvez indiquer qu’une clé API n’est pas nécessaire en définissant la `authLevel` propriété « anonyme » dans le fichier *function.json* .

Vous pouvez trouver des valeurs de clés de l’API dans le dossier *D:\home\data\Functions\secrets* dans le système de fichiers de l’application de la fonction.  La clé principale et une touche de fonction sont définis dans le fichier *host.json* , comme illustré dans cet exemple. 

```json
{
  "masterKey": "K6P2VxK6P2VxK6P2VxmuefWzd4ljqeOOZWpgDdHW269P2hb7OSJbDg==",
  "functionKey": "OBmXvc2K6P2VxK6P2VxK6P2VxVvCdB89gChyHbzwTS/YYGWWndAbmA=="
}
```

La touche de fonction à partir de *host.json* peut être utilisée pour déclencher n’importe quelle fonction mais ne déclenche pas une fonction désactivée. La clé principale peut être utilisée pour déclencher n’importe quelle fonction et déclenche une fonction même si elle est désactivée. Vous pouvez configurer une fonction pour demander la clé principale en définissant la `authLevel` propriété à « administrateur ». 

Si le dossier *secrets* contient un fichier JSON portant le même nom qu’une fonction, le `key` propriété dans ce fichier peut également être utilisée pour déclencher la fonction et cette touche fonctionne qu’avec la fonction auquel il fait référence. Par exemple, la clé d’API pour une fonction nommée `HttpTrigger` spécifié dans *HttpTrigger.json* dans le dossier *secrets* . Voici un exemple :

```json
{
  "key":"0t04nmo37hmoir2rwk16skyb9xsug32pdo75oce9r4kg9zfrn93wn4cx0sxo4af0kdcz69a4i"
}
```

> [AZURE.NOTE] Lorsque vous configurez un déclencheur WebHook, ne pas partager la clé principale avec le fournisseur WebHook. Utiliser une clé qui fonctionne qu’avec la fonction qui traite le WebHook.  La clé principale peut servir à déclencher n’importe quelle fonction, même désactivé fonctions.

## <a name="example-c-code-for-an-http-trigger-function"></a>Exemple c# de code pour une fonction de déclenchement HTTP 

L’exemple de code recherche une `name` paramètre dans la chaîne de requête ou dans le corps de la requête HTTP.

```csharp
using System.Net;
using System.Threading.Tasks;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

## <a name="example-f-code-for-an-http-trigger-function"></a>Exemple F # de code pour une fonction de déclenchement HTTP

L’exemple de code recherche une `name` paramètre dans la chaîne de requête ou dans le corps de la requête HTTP.

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic

let Run(req: HttpRequestMessage) =
    async {
        let q =
            req.GetQueryNameValuePairs()
                |> Seq.tryFind (fun kv -> kv.Key = "name")
        match q with
        | Some kv ->
            return req.CreateResponse(HttpStatusCode.OK, "Hello " + kv.Value)
        | None ->
            let! data = Async.AwaitTask(req.Content.ReadAsAsync<obj>())
            try
                return req.CreateResponse(HttpStatusCode.OK, "Hello " + data?name)
            with e ->
                return req.CreateErrorResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
    } |> Async.StartAsTask
```

Vous devrez un `project.json` fichier qui utilise NuGet pour faire référence à la `FSharp.Interop.Dynamic` et `Dynamitey` assemblys, comme suit :

```json
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
```

Cette option NuGet pour récupérer vos dépendances et les référencer dans votre script.

## <a name="example-nodejs-code-for-an-http-trigger-function"></a>Exemple de code Node.js pour une fonction de déclenchement HTTP 

Cet exemple de code recherche une `name` paramètre dans la chaîne de requête ou dans le corps de la requête HTTP.

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

## <a name="example-c-code-for-a-github-webhook-function"></a>Exemple c# de code pour une fonction GitHub WebHook 

Cet exemple de code enregistre les commentaires de problème GitHub.

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using System.Threading.Tasks;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    string jsonContent = await req.Content.ReadAsStringAsync();
    dynamic data = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"WebHook was triggered! Comment: {data.comment.body}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        body = $"New GitHub comment: {data.comment.body}"
    });
}
```

## <a name="example-f-code-for-a-github-webhook-function"></a>Exemple F # de code pour une fonction GitHub WebHook

Cet exemple de code enregistre les commentaires de problème GitHub.

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Response = {
    body: string
}

let Run(req: HttpRequestMessage, log: TraceWriter) =
    async {
        let! content = req.Content.ReadAsStringAsync() |> Async.AwaitTask
        let data = content |> JsonConvert.DeserializeObject
        log.Info(sprintf "GitHub WebHook triggered! %s" data?comment?body)
        return req.CreateResponse(
            HttpStatusCode.OK,
            { body = sprintf "New GitHub comment: %s" data?comment?body })
    } |> Async.StartAsTask
```

## <a name="example-nodejs-code-for-a-github-webhook-function"></a>Exemple de code Node.js pour une fonction GitHub WebHook 

Cet exemple de code enregistre les commentaires de problème GitHub.

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)] 
