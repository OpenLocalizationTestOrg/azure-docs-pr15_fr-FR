<properties
    pageTitle="Référence du développeur Azure fonctions NodeJS | Microsoft Azure"
    description="Comprendre comment développer des fonctions Azure à l’aide de NodeJS."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="Azure fonctions, fonctions, traitement de l’événement, webhooks, cluster dynamique, architecture sans serveur"/>

<tags
    ms.service="functions"
    ms.devlang="nodejs"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="05/13/2016"
    ms.author="chrande"/>

# <a name="azure-functions-nodejs-developer-reference"></a>Référence du développeur NodeJS fonctions Azure

> [AZURE.SELECTOR]
- [Script c#](../articles/azure-functions/functions-reference-csharp.md)
- [Script F #](../articles/azure-functions/functions-reference-fsharp.md)
- [Node.js](../articles/azure-functions/functions-reference-node.md)

L’expérience de nœud/JavaScript pour les fonctions Azure facilite l’exporter une fonction qui est passée un `context` objet de communication avec le runtime et pour recevoir et envoyer des données via les liaisons.

Cet article suppose que vous avez déjà lus la [référence du développeur fonctions Azure](functions-reference.md).

## <a name="exporting-a-function"></a>Exportation d’une fonction

Toutes les fonctions JavaScript doivent exporter un seul `function` via `module.exports` pour le runtime rechercher la fonction et l’exécuter. Cette fonction doit toujours contenir une `context` objet.

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // Additional inputs can be accessed by the arguments property
    if(arguments.length === 4) {
        context.log('This function has 4 inputs');
    }
};
// or you can include additional inputs in your arguments
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
};
```

Liaisons de `direction === "in"` sont passées comme arguments de la fonction, ce qui signifie que vous pouvez utiliser le long [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) gérer dynamiquement les nouvelles entrées (par exemple, en utilisant `arguments.length` pour parcourir toutes vos entrées). Cette fonctionnalité est très utile si vous disposez d’un déclencheur avec aucune entrées supplémentaires, comme vous pouvez mis accéder à vos données déclencheur sans référencer votre `context` objet.

Les arguments sont toujours transmis à la fonction dans l’ordre qu’ils se produisent dans *function.json*, même si vous ne spécifiez pas les dans votre relevé exportations. Par exemple, si vous avez `function(context, a, b)` puis remplacez-le par `function(context, a)`, vous pouvez encore obtenir la valeur de `b` dans le code de la fonction en faisant référence à `arguments[3]`.

Toutes les liaisons, quelle que soit la direction, sont également transmis le long de la `context` objet (voir ci-dessous). 

## <a name="context-object"></a>objet de contexte

Le runtime utilise un `context` objet pour passer des données vers et depuis votre fonction et pour vous permettre de communiquer avec le runtime.

L’objet de contexte est toujours le premier paramètre à une fonction et doivent toujours être inclus car il comporte des méthodes telles que `context.done` et `context.log` qui sont requises pour utiliser correctement le runtime. Vous pouvez nommer l’objet à votre convenance (c'est-à-dire `ctx` ou `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // function logic goes here :)
};
```

## <a name="contextbindings"></a>Context.Bindings

La `context.bindings` objet collecte toutes les données d’entrée et de sortie. Les données sont ajoutées à la `context.bindings` objet la `name` propriété de la liaison. Par exemple, compte tenu de la définition de liaison suivant *function.json*, vous pouvez accéder au contenu de la file d’attente via `context.bindings.myInput`. 

```json
    {
        "type":"queue",
        "direction":"in",
        "name":"myInput"
        ...
    }
```

```javascript
// myInput contains the input data which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

## `context.done([err],[propertyBag])`

La `context.done` fonction indique que vous avez terminé à l’exécution en cours d’exécution. Il est important d’appeler lorsque vous avez terminé avec la fonction ; Si vous n’est pas le cas, le runtime ne sauront toujours jamais que votre fonction terminé. 

La `context.done` fonction consiste à repasser une erreur définie par l’utilisateur pour le runtime, ainsi que d’un ensemble de propriétés de propriétés qui remplace les propriétés de la `context.bindings` objet.

```javascript
// Even though we set myOutput to have:
//  -> text: hello world, number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: hello there, world, noNumber: true
```

## <a name="contextlogmessage"></a>Context.log(message)

La `context.log` méthode permet de vous permettent d’obtenir des instructions log corrélation entre ensemble pour l’enregistrement. Si vous utilisez `console.log`, vos messages ne montrera que pour l’enregistrement au niveau du processus, qui n’est pas aussi utile.

```javascript
/* You can use context.log to log output specific to this 
function. You can access your bindings via context.bindings */
context.log({hello: 'world'}); // logs: { 'hello': 'world' } 
```

La `context.log` méthode prend en charge le même format de paramètre le nœud [util.format méthode](https://nodejs.org/api/util.html#util_util_format_format) prend en charge. Par conséquent, par exemple, code à ceci :

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

peuvent être écrites comme suit :

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

## <a name="http-triggers-contextreq-and-contextres"></a>Déclencheurs HTTP : context.req et context.res

Dans le cas des déclencheurs HTTP, car il s’agit tel un modèle commun à utiliser `req` et `res` pour les objets de demande et réponse HTTP, nous avons décidé permettent d’accéder à celles de l’objet de contexte, à la place qui vous oblige à utiliser la version complète `context.bindings.name` motif.

```javascript
// You can access your http request off of the context ...
if(context.req.body.emoji === ':pizza:') context.log('Yay!');
// and also set your http response
context.res = { status: 202, body: 'You successfully ordered more coffee!' };   
```

## <a name="node-version--package-management"></a>Nœud Version et gestion des packages

La version de nœud est verrouillée en `5.9.1`. Nous améliorons une solution prise en charge l’ajout de plusieurs versions et rendant configurable.

Vous pouvez inclure des packages dans votre fonction en téléchargeant un fichier *package.json* au dossier de votre fonction dans le système de fichiers de l’application de fonction. Fichier télécharger instructions, voir la section **comment mettre à jour des fichiers d’application de fonction** de la [rubrique de référence du développeur fonctions Azure](functions-reference.md#fileupdate). 

Vous pouvez également utiliser `npm install` dans l’interface de ligne de commande de l’application de fonction SCM (Kudu) :

1. Accédez à : `https://<function_app_name>.scm.azurewebsites.net`.

2. Cliquez sur **Déboguer Console > CMD**.

3. Accédez à `D:\home\site\wwwroot\<function_name>`.

4. Exécuter `npm install`.

Une fois que les packages que vous avez besoin sont installés, vous les importer dans votre fonction de la façon habituelle (c'est-à-dire, via `require('packagename')`)

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v5.9.1'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

## <a name="environment-variables"></a>Variables d’environnement

Pour obtenir une variable d’environnement ou une application définition de valeur, utilisez `process.env`, comme illustré dans l’exemple suivant :

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    
    context.log('Node.js timer trigger function ran!', timeStamp);   
    context.log(GetEnvironmentVariable("AzureWebJobsStorage"));
    context.log(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
    
    context.done();
};

function GetEnvironmentVariable(name)
{
    return name + ": " + process.env[name];
}
```

## <a name="typescriptcoffeescript-support"></a>Machine à écrire/CoffeeScript prise en charge

Il n’existe pas encore, d’assistance directe pour automatique-compilation machine à écrire/CoffeeScript via le runtime, afin que tout est doivent être gérées en dehors du runtime, au moment du déploiement. 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

* [Référence du développeur fonctions Azure](functions-reference.md)
* [Référence du développeur Azure fonctions c#](functions-reference-csharp.md)
* [Référence du développeur Azure fonctions F #](functions-reference-fsharp.md)
* [Liaisons et azure déclencheurs de fonctions](functions-triggers-bindings.md)
