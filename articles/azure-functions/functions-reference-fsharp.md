<properties
    pageTitle="Référence du développeur Azure fonctions F # | Microsoft Azure"
    description="Comprendre comment développer des fonctions Azure à l’aide de F #."
    services="functions"
    documentationCenter="fsharp"
    authors="sylvanc"
    manager="jbronsk"
    editor=""
    tags=""
    keywords="Azure fonctions, les fonctions, les événements de traitement, webhooks, cluster dynamique, sans serveur architecture F#"/>

<tags
    ms.service="functions"
    ms.devlang="fsharp"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="09/09/2016"
    ms.author="syclebsc"/>

# <a name="azure-functions-f-developer-reference"></a>Référence du développeur F # fonctions Azure

> [AZURE.SELECTOR]
- [Script c#](../articles/azure-functions/functions-reference-csharp.md)
- [Script F #](../articles/azure-functions/functions-reference-fsharp.md)
- [Node.js](../articles/azure-functions/functions-reference-node.md)

F # pour les fonctions Azure est une solution pour l’exécution facilement des petits fragments de code, ou « fonctions », dans le cloud. Flux de données dans votre fonction F # via les arguments de la fonction. Noms d’arguments sont spécifiés dans `function.json`, et qu’il existe des noms prédéfinis pour accéder à des éléments tels que les jetons de journal et annulation de fonction.

Cet article suppose que vous avez déjà lus la [référence du développeur fonctions Azure](functions-reference.md).

## <a name="how-fsx-works"></a>Fonctionnement de .fsx

Un `.fsx` fichier est un script F #. Il peut être considéré comme un projet F # se trouvant dans un seul fichier. Le fichier contient le code de votre programme (dans ce cas, la fonction Azure) et les directives pour la gestion des dépendances.

Lorsque vous utilisez un `.fsx` pour une fonction d’Azure, fréquemment requis assemblys sont automatiquement incluses à votre place, ce qui permet de vous recentrer sur le code de la fonction plutôt que « standard ».

## <a name="binding-to-arguments"></a>Liaison aux arguments

Chaque liaison prend en charge certains arguments, comme indiqué dans les [fonctions Azure déclencheurs et référence du développeur liaisons](functions-triggers-bindings.md). Par exemple, un des liaisons argument que prend en charge un déclencheur d’objets blob est un POCO, qui peut être exprimée à l’aide d’un enregistrement F #. Par exemple :

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

Votre fonction Azure F # prendra un ou plusieurs arguments. Lorsque nous parlons arguments fonctions Azure, nous faire référence à des arguments _d’entrée_ et _sortie_ arguments. Argument d’entrée est exactement il ressemble : entrée à votre fonction Azure F #. Un argument de _sortie_ n’est modifiable données ou une `byref<>` argument sert d’une manière de passer données retour _arrière_ de votre fonction.

Dans l’exemple ci-dessus, `blob` est un argument d’entrée, et `output` est un argument de sortie. Notez que nous avons utilisé `byref<>` pour `output` (il est inutile d’ajouter le `[<Out>]` annotation). À l’aide d’un `byref<>` type permet de votre fonction pour modifier les enregistrement ou l’objet l’argument fait référence à.

Lorsqu’un enregistrement F # est utilisé comme un type d’entrée, la définition d’enregistrement doit être marquée avec `[<CLIMutable>]` afin de permettre l’infrastructure d’Azure fonctions définir les champs correctement avant de passer de l’enregistrement à votre fonction. Les paramètres avancés, `[<CLIMutable>]` génère méthodes Set pour les propriétés de l’enregistrement. Par exemple :

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: TraceWriter) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

Une classe F # peut également être utilisée pour les arguments et. Pour une classe, propriétés devez généralement GET et méthodes Set. Par exemple :

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Journalisation

Pour vous connecter sortie à votre [diffusion en continu les journaux](../app-service-web/web-sites-streaming-logs-and-console.md) en F #, la fonction doit prendre un argument de type `TraceWriter`. Pour garantir la cohérence, nous vous recommandons de cet argument est nommé `log`. Par exemple :

```fsharp
let Run(blob: string, output: byref<string>, log: TraceWriter) =
    log.Verbose(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Asynchrone

La `async` flux de travail peut être utilisé, mais le résultat doit renvoyer un `Task`. Vous pouvez faire avec `Async.StartAsTask`, par exemple :

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Jeton d’annulation

Si votre fonction a besoin gérer arrêt correctement, vous pouvez lui donner un [`CancellationToken`](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) argument. Cela peut être combiné avec `async`, par exemple :

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>Importer des espaces de noms

Espaces de noms peuvent être ouverts de la manière habituelle :

```fsharp
open System.Net
open System.Threading.Tasks

let Run(req: HttpRequestMessage, log: TraceWriter) =
    ...
```

Espaces de noms suivants sont ouvrent automatiquement :

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Référencement des assemblys externes

De même, les assembly framework références soient ajoutés dans le `#r "AssemblyName"` directive.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks

let Run(req: HttpRequestMessage, log: TraceWriter) =
    ...
```

Les assemblys suivants sont automatiquement ajoutés par les fonctions Azure environnement d’hébergement :

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`.

En outre, les assemblys suivants sont spéciales casse et peuvent être référencées par simplename (par exemple, `#r "AssemblyName"`) :

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Si vous avez besoin de faire référence à un assembly privé, vous pouvez télécharger le fichier d’assemblage dans un `bin` dossier par rapport à votre fonction et de référence à l’aide du fichier nom (par exemple,  `#r "MyAssembly.dll"`). Pour plus d’informations sur la façon de télécharger des fichiers vers votre dossier de fonction, voir la section suivante sur la gestion du package.

## <a name="editor-prelude"></a>Éditeur prélude

Un éditeur qui prend en charge les Services du compilateur F # ne sera pas connaissance des espaces de noms et assemblys qui inclut automatiquement les fonctions Azure. Dès lors, il peut être utile d’inclure une étape préliminaire qui vous permet de l’éditeur de trouver les assemblys que vous utilisez, ainsi que pour ouvrir explicitement des espaces de noms. Par exemple :

```fsharp
#if !COMPILED
#I "../../bin/Binaries/WebJobs.Script.Host"
#r "Microsoft.Azure.WebJobs.Host.dll"
#endif

open Sytem
open Microsoft.Azure.WebJobs.Host

let Run(blob: string, output: byref<string>, log: TraceWriter) =
    ...
```

Lorsque les fonctions Azure exécute votre code, il traite la source avec `COMPILED` défini, de sorte que le prélude éditeur sont ignorée.

## <a name="package-management"></a>Gestion des packages

Pour utiliser les packages NuGet dans une fonction F #, ajoutez un `project.json` de fichiers vers le dossier de la fonction système de l’application de fonction de fichiers. Voici un exemple `project.json` fichier qui ajoute une référence de package NuGet à `Microsoft.ProjectOxford.Face` version 1.1.0 :

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

Uniquement la 4.6 Framework .NET est pris en charge, assurez-vous que votre `project.json` fichier spécifie `net46` comme illustré ici.

Lorsque vous téléchargez un `project.json` fichier, le runtime Obtient les packages et ajoute automatiquement des références aux assemblys de package. Vous n’avez pas besoin d’ajouter `#r "AssemblyName"` directives. Il suffit d’ajouter les requis `open` instructions à vos `.fsx` fichier.

Vous souhaiterez peut-être placer automatiquement assemblys de références dans votre étape préliminaire de l’éditeur, à améliorer l’interaction de votre éditeur avec F # compiler les Services.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>Comment ajouter un `project.json` fichier vers votre fonction Azure

1. Début en veillant à votre application de la fonction est en cours d’exécution, ce que vous pouvez faire en ouvrant votre fonction dans le portail Azure. Ceci permet également accéder aux journaux de diffusion en continu lesquelles résultat package d’installation s’affiche.

2. Pour télécharger un `project.json` de fichiers, utilisez une des méthodes décrites dans [la mise à jour des fichiers d’application de fonction](functions-reference.md#fileupdate). Si vous utilisez [Déploiement continue pour les fonctions Azure](functions-continuous-deployment.md), vous pouvez ajouter un `project.json` fichier vers votre branche intermédiaire afin d’essayer avant d’ajouter à votre branche déploiement.

3. Après le `project.json` fichier est ajouté, vous verrez sortie similaire à l’exemple suivant dans la fonction de diffusion en continu de journal :

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189
2016-04-04T19:02:57.189
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>Variables d’environnement

Pour obtenir une variable d’environnement ou une application définition de valeur, utilisez `System.Environment.GetEnvironmentVariable`, par exemple :

```fsharp
open System.Environment

let Run(timer: TimerInfo, log: TraceWriter) =
    log.Info("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.Info("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>Réutilisation du code .fsx

Vous pouvez utiliser le code d’autres `.fsx` fichiers en utilisant un `#load` directive. Par exemple :

`run.fsx`

```fsharp
#load "logger.fsx"

let Run(timer: TimerInfo, log: TraceWriter) =
    mylog log (sprintf "Timer: %s" DateTime.Now.ToString())
```

`logger.fsx`

```fsharp
let mylog(log: TraceWriter, text: string) =
    log.Verbose(text);
```

Fournit des chemins d’accès à la `#load` directive sont par rapport à l’emplacement de votre `.fsx` fichier.

* `#load "logger.fsx"`charger un fichier situé dans le dossier de fonction.

* `#load "package\logger.fsx"`charge un fichier situé dans le `package` dossier dans le dossier de fonction.

* `#load "..\shared\mylogger.fsx"`charge un fichier situé dans le `shared` dossier au même niveau que le dossier de fonction, c'est-à-dire, juste en dessous de `wwwroot`.

La `#load` directive ne fonctionne qu’avec `.fsx` fichiers (script F #) et non avec `.fs` fichiers.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

* [Guide de F #](https://docs.microsoft.com/en-us/dotnet/articles/fsharp/index)
* [Référence du développeur fonctions Azure](functions-reference.md)
* [Référence du développeur Azure fonctions c#](functions-reference-csharp.md)
* [Référence du développeur NodeJS fonctions Azure](functions-reference-node.md)
* [Liaisons et azure déclencheurs de fonctions](functions-triggers-bindings.md)
* [Fonctions Azure test](functions-test-a-function.md)
* [Fonctions Azure mise à l’échelle](functions-scale.md)
