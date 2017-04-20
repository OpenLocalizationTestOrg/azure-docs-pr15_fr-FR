<properties
    pageTitle="Azure déclencheurs de fonctions et des liaisons pour le stockage Azure | Microsoft Azure"
    description="Comprendre comment utiliser des déclencheurs stockage Azure et les liaisons dans les fonctions Azure."
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
    ms.author="chrande"/>

# <a name="azure-functions-triggers-and-bindings-for-azure-storage"></a>Azure déclencheurs de fonctions et des liaisons pour le stockage Azure

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Cet article explique comment configurer et code stockage Azure déclencheurs et liaisons dans les fonctions Azure. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a id="storagequeuetrigger"></a>Azure déclencheur file d’attente de stockage

#### <a name="functionjson-for-storage-queue-trigger"></a>Function.JSON déclencheur file d’attente de stockage

Le fichier *function.json* spécifie les propriétés suivantes.

- `name`: Le nom de la variable utilisé dans le code de fonction pour la file d’attente ou le message file d’attente. 
- `queueName`: Nom de la file d’attente d’interrogation. Pour les règles d’appellation file d’attente, voir [dénomination files d’attente et les métadonnées](https://msdn.microsoft.com/library/dd179349.aspx).
- `connection`: Nom d’un paramètre d’application qui contient une chaîne de connexion de stockage. Si vous laissez `connection` vide, le déclencheur est prises en charge par la chaîne de connexion de stockage par défaut pour l’application de fonction qui a été spécifiée par le paramètre d’application AzureWebJobsStorage.
- `type`: Doit être définie sur *queueTrigger*.
- `direction`: Doit être définie *dans*. 

Exemple *function.json* d’un déclencheur file d’attente de stockage :

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"",
            "type": "queueTrigger",
            "direction": "in"
        }
    ]
}
```

#### <a name="queue-trigger-supported-types"></a>Types pris en charge déclencheur file d’attente

Le message file d’attente peut être désérialisé à un des types suivants :

* Objet (à partir de JSON)
* Chaîne
* Tableau d’octets 
* `CloudQueueMessage`(C#) 

#### <a name="queue-trigger-metadata"></a>Métadonnées de déclencheur file d’attente

Vous pouvez obtenir des métadonnées file d’attente dans votre fonction à l’aide de ces noms de variables :

* expirationTime
* insertionTime
* nextVisibleTime
* ID
* popReceipt
* dequeueCount
* queueTrigger (une autre manière pour récupérer le texte du message file d’attente sous forme de chaîne)

Cet exemple de code c# récupère et métadonnées en file d’attente les journaux :

```csharp
public static void Run(string myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

#### <a name="handling-poison-queue-messages"></a>Gestion des messages poison file d’attente

Messages dont le contenu permet à une fonction échec sont appelés *messages poison*. En cas d’échec de la fonction, le message file d’attente n’est pas supprimé et finalement est sélectionné, à l’origine du cycle à répéter. Le Kit de développement peut interrompre automatiquement le cycle d’après un nombre limité d’itérations, ou vous pouvez le faire manuellement.

Le Kit de développement appelle une fonction jusqu'à 5 fois pour traiter un message file d’attente. Si la cinquième tentative échoue, le message est déplacé vers une file d’attente poison.

La file d’attente poison nommé *{originalqueuename}*-poison. Vous pouvez écrire une fonction pour traiter les messages à partir de la file d’attente poison en vous connectant les ou envoyer une notification que l’attention manuelle est nécessaire. 

Si vous souhaitez gérer les messages poison manuellement, vous pouvez obtenir le nombre de fois un message a été sélectionné pour le traitement en vérifiant `dequeueCount`.

## <a id="storagequeueoutput"></a>Liaison de sortie Azure file d’attente de stockage

#### <a name="functionjson-for-storage-queue-output-binding"></a>liaison de sortie Function.JSON pour file d’attente de stockage

Le fichier *function.json* spécifie les propriétés suivantes.

- `name`: Le nom de la variable utilisé dans le code de fonction pour la file d’attente ou le message file d’attente. 
- `queueName`: Nom de la file d’attente. Pour les règles d’appellation file d’attente, voir [affectation des noms files d’attente et les métadonnées](https://msdn.microsoft.com/library/dd179349.aspx).
- `connection`: Nom d’un paramètre d’application qui contient une chaîne de connexion de stockage. Si vous laissez `connection` vide, le déclencheur est prises en charge par la chaîne de connexion de stockage par défaut pour l’application de fonction qui a été spécifiée par le paramètre d’application AzureWebJobsStorage.
- `type`: Doit être défini à *file d’attente*.
- `direction`: Doit être définie à *Déconnecter*. 

Exemple *function.json* pour une file d’attente de stockage de sortie de liaison qui utilise un déclencheur file d’attente et écrit un message file d’attente :

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myQueue",
      "queueName": "samples-workitems-out",
      "connection": "MyStorageConnection",
      "type": "queue",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### <a name="queue-output-binding-supported-types"></a>Types de liaisons de sortie pris en charge file d’attente

La `queue` liaison peut sérialiser les types suivants à un message file d’attente :

* Objet (`out T` en c#, crée un message avec un objet null si le paramètre est null expiration de la fonction)
* Chaîne (`out string` en c#, crée message file d’attente si la valeur du paramètre n’est pas null expiration de la fonction)
* Tableau d’octets (`out byte[]` en c#, fonctionne comme chaîne) 
* `out CloudQueueMessage`(C#, fonctionne de façon chaîne) 

En c# vous pouvez également lier à `ICollector<T>` ou `IAsyncCollector<T>` où `T` est un des types pris en charge.

#### <a name="queue-output-binding-code-examples"></a>Exemples de code de liaison de sortie de file d’attente

Cet exemple de code c# écrit un message de file d’attente de sortie unique pour chaque message file d’attente d’entrée.

```csharp
public static void Run(string myQueueItem, out string myOutputQueueItem, TraceWriter log)
{
    myOutputQueueItem = myQueueItem + "(next step)";
}
```

Cet exemple de code c# écrit plusieurs messages à l’aide de `ICollector<T>` (utilisez `IAsyncCollector<T>` dans une fonction asynchrone) :

```csharp
public static void Run(string myQueueItem, ICollector<string> myQueue, TraceWriter log)
{
    myQueue.Add(myQueueItem + "(step 1)");
    myQueue.Add(myQueueItem + "(step 2)");
}
```

## <a id="storageblobtrigger"></a>Azure déclencheur d’objets blob Storage

#### <a name="functionjson-for-storage-blob-trigger"></a>Function.JSON pour déclencheur d’objets blob storage

Le fichier *function.json* spécifie les propriétés suivantes.

- `name`: Le nom de la variable utilisé dans le code de fonction pour le blob. 
- `path`: Un chemin d’accès qui spécifie le conteneur pour surveiller et éventuellement un modèle de nom blob.
- `connection`: Nom d’un paramètre d’application qui contient une chaîne de connexion de stockage. Si vous laissez `connection` vide, le déclencheur est prises en charge par la chaîne de connexion de stockage par défaut pour l’application de fonction qui a été spécifiée par le paramètre d’application AzureWebJobsStorage.
- `type`: Doit être définie sur *blobTrigger*.
- `direction`: Doit être définie *dans*.

Exemple *function.json* d’un déclencheur stockage blob qui surveille pour les objets BLOB qui sont ajoutés au conteneur exemples-éléments de travail :

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":""
        }
    ]
}
```

#### <a name="blob-trigger-supported-types"></a>Déclencheur d’objets BLOB types pris en charge

Le blob peut être désérialisé à un des types suivants dans les fonctions nœud ou c# :

* Objet (à partir de JSON)
* Chaîne

Dans les fonctions c#, vous pouvez également lier à un des types suivants :

* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `IEnumerable<CloudBlockBlob>`
* `IEnumerable<CloudPageBlob>`
* Autres types désérialisés par [ICloudBlobStreamBinder](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md#icbsb) 

#### <a name="blob-trigger-c-code-example"></a>Exemple code BLOB déclencheur c#

Cet exemple de code c# enregistre le contenu de chaque blob est ajoutée au conteneur analysé.

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

#### <a name="blob-trigger-name-patterns"></a>Modèles de noms BLOB déclencheur

Vous pouvez spécifier un modèle de nom blob dans le `path` propriété. Par exemple :

```json
"path": "input/original-{name}",
```

Ce chemin d’accès pourrait trouver un blob appelé *original Blob1.txt* dans le conteneur *d’entrée* et la valeur de la `name` variable dans le code de la fonction serait `Blob1`.

Un autre exemple :

```json
"path": "input/{blobname}.{blobextension}",
```

Ce chemin d’accès est également trouver un blob appelé *original Blob1.txt*et la valeur de la `blobname` et `blobextension` variables dans le code de la fonction serait *original Blob1* et *txt*.

Vous pouvez limiter les types d’objets BLOB déclenchant la fonction en spécifiant un motif avec une valeur fixe pour l’extension de fichier. Si vous définissez la `path` *à/exemples / {nom} .png*, *.png* BLOB uniquement dans le conteneur *exemples* déclenche la fonction.

Si vous avez besoin spécifier un modèle de nom pour les noms d’objets blob qui ont des accolades dans le nom, double-cliquez sur les accolades. Par exemple, si vous souhaitez rechercher des objets BLOB dans le conteneur *images* qui portent des noms à ceci :

        {20140101}-soundfile.mp3

Utilisez cette option pour la `path` propriété :

        images/{{20140101}}-{name}

Dans l’exemple, le `name` valeur de la variable serait *soundfile.mp3*. 

#### <a name="blob-receipts"></a>Confirmations de BLOB

L’exécution de fonctions Azure vérifie qu’aucune fonction déclencheur blob n’est appelée plusieurs fois pour le même blob nouveau ou mis à jour. Pour cela, la conservation de *confirmations blob* afin de déterminer si une version blob donné a été traitée.

Confirmations de BLOB sont stockées dans un conteneur nommé *azure-webjobs-hôtes* dans le compte de stockage Azure spécifié par la chaîne de connexion AzureWebJobsStorage. Un accusé de réception blob comporte les informations suivantes :

* La fonction qui a été appelée le blob («*{nom de l’application fonction}*. Fonctions. *{nom de la fonction}*», par exemple : « functionsf74b96f7. Functions.CopyBlob »)
* Le nom du conteneur
* Le type de blob (« BlockBlob » ou « PageBlob »)
* Le nom d’objets blob
* L’ETag (un identificateur de version blob, par exemple : « 0x8D1DC6E70A277EF »)

Si vous voulez forcer nouveau traitement d’un objet blob, vous pouvez supprimer manuellement la réception d’objets blob pour ce blob à partir du conteneur *azure-webjobs-hosts* .

#### <a name="handling-poison-blobs"></a>Traitement des objets BLOB poison

Lorsqu’une fonction déclencheur blob échoue, le Kit de développement appelle à nouveau, au cas où l’échec a été provoqué par une erreur temporaire. Si l’erreur est provoquée par le contenu de l’objet blob, la fonction échoue chaque fois qu’il tente de traiter le blob. Par défaut, le Kit de développement appelle une fonction jusqu'à 5 fois pour un blob donné. Si la cinquième tentative échoue, le Kit de développement ajoute un message à une file d’attente nommée *webjobs-blobtrigger-poison*.

Le message file d’attente pour les objets BLOB poison est un objet JSON qui contient les propriétés suivantes :

* FunctionId (dans le format *{nom de l’application fonction}*. Fonctions. *{nom de la fonction}*)
* BlobType (« BlockBlob » ou « PageBlob »)
* ContainerName
* BlobName
* ETag (un identificateur de version blob, par exemple : « 0x8D1DC6E70A277EF »)

#### <a name="blob-polling-for-large-containers"></a>BLOB interrogation de grands conteneurs

Si le conteneur blob que le déclencheur est analysez contient plus de 10 000 objets BLOB, les analyses de runtime fonctions journaux pour surveiller les objets BLOB nouveaux ou modifiés. Ce processus n’est pas en temps réel ; une fonction longues ne pouvant pas être déclenchée jusqu'à ce que quelques minutes ou plus après avoir créé le blob. En outre, base de [stockage journaux créés sur un « efforts meilleures »](https://msdn.microsoft.com/library/azure/hh343262.aspx) ; Il n’existe aucune garantie que tous les événements seront capturées. Dans certaines conditions, les journaux peuvent être manqués. Si les limites de vitesse et la fiabilité de déclencheurs blob pour les grands conteneurs ne sont pas acceptables pour votre application, la méthode recommandée consiste à créer un message file d’attente lorsque vous créez le blob et utilisez un déclencheur file d’attente au lieu d’un déclencheur d’objets blob pour traiter le blob.
 
## <a id="storageblobbindings"></a>Azure blob Storage entrée et sortie des liaisons

#### <a name="functionjson-for-a-storage-blob-input-or-output-binding"></a>Function.JSON pour un blob de stockage d’entrée ou de liaison de sortie

Le fichier *function.json* spécifie les propriétés suivantes.

- `name`: Le nom de la variable utilisé dans le code de fonction pour le blob. 
- `path`: Un chemin d’accès qui spécifie le conteneur pour lire le blob à partir d’ou d’écrire le blob dans et vous pouvez également un modèle de nom blob.
- `connection`: Nom d’un paramètre d’application qui contient une chaîne de connexion de stockage. Si vous laissez `connection` vide, la liaison est prises en charge par la chaîne de connexion de stockage par défaut pour l’application de fonction qui a été spécifiée par le paramètre d’application AzureWebJobsStorage.
- `type`: Doit être définie sur *blob*.
- `direction`: La valeur *ou *l’arrière** . 

Exemple *function.json* un espace de stockage blob d’entrée ou de sortie de liaison, à l’aide d’un déclencheur file d’attente pour copier un blob :

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### <a name="blob-input-and-output-supported-types"></a>BLOB d’entrée et de sortie types pris en charge

La `blob` liaison peut sérialiser ou désérialiser les types suivants dans les fonctions Node.js ou c# :

* Objet (`out T` en c# pour blob de sortie : crée un blob en tant qu’objet null si une valeur de paramètre est null à la fin de la fonction)
* Chaîne (`out string` en c# pour blob de sortie : crée un blob uniquement si le paramètre de chaîne n’est pas null lorsque la fonction retourne une valeur)

Dans les fonctions c#, vous pouvez également lier aux types suivants :

* `TextReader`(entrée uniquement)
* `TextWriter`(sortie uniquement)
* `Stream`
* `CloudBlobStream`(sortie uniquement)
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

#### <a name="blob-output-c-code-example"></a>Exemple code BLOB sortie c#

Cet exemple de code c# copie un blob dont le nom est reçu dans un message file d’attente.

```csharp
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

## <a id="storagetablesbindings"></a>Tables de stockage Azure entrée et sortie des liaisons

#### <a name="functionjson-for-storage-tables"></a>Function.JSON pour les tableaux de stockage

La *function.json* spécifie les propriétés suivantes.

- `name`: Le nom de la variable utilisé dans le code de fonction pour la liaison de la table. 
- `tableName`: Nom de la table.
- `partitionKey`et `rowKey` : conjointement pour lire une seule entité dans une fonction nœud c# ou ou pour écrire une entité unique dans une fonction nœud.
- `take`: Le nombre maximal de lignes à lire pour la table d’entrée dans une fonction nœud.
- `filter`: Expression de filtre OData pour la table d’entrée dans une fonction nœud.
- `connection`: Nom d’un paramètre d’application qui contient une chaîne de connexion de stockage. Si vous laissez `connection` vide, la liaison est prises en charge par la chaîne de connexion de stockage par défaut pour l’application de fonction qui a été spécifiée par le paramètre d’application AzureWebJobsStorage.
- `type`: Doit être définie à *table*.
- `direction`: La valeur *ou *l’arrière** . 

L' exemple suivant *function.json* utilise un déclencheur file d’attente pour lire une seule ligne du tableau. Le JSON fournit une valeur de clé partition codé en dur et indique que la clé de ligne est fourni à partir du message file d’attente.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### <a name="storage-tables-input-and-output-supported-types"></a>Tables de stockage d’entrée et sortie types pris en charge

La `table` liaison peut sérialiser ou désérialiser des objets dans des fonctions Node.js ou c#. Les objets présentera les propriétés RowKey et PartitionKey. 

Dans les fonctions c#, vous pouvez également lier aux types suivants :

* `T`où `T` mettent en œuvre,`ITableEntity`
* `IQueryable<T>`(entrée uniquement)
* `ICollector<T>`(sortie uniquement)
* `IAsyncCollector<T>`(sortie uniquement)

#### <a name="storage-tables-binding-scenarios"></a>Stockage de tables des scénarios de liaison

La liaison de table prend en charge les scénarios suivants :

* Lire une seule ligne dans une fonction nœud c# ou.

    Définir `partitionKey` et `rowKey`. La `filter` et `take` propriétés ne sont pas utilisées dans ce scénario.

* Lire plusieurs lignes dans une fonction c#.

    Le runtime fonctions fournit un `IQueryable<T>` objet lié à la table. Type de `T` doit être dérivé `TableEntity` ou mettre en œuvre `ITableEntity`. La `partitionKey`, `rowKey`, `filter`, et `take` propriétés ne sont pas utilisées dans ce scénario ; Vous pouvez utiliser la `IQueryable` objet pour effectuer un filtrage obligatoire. 

* Lire plusieurs lignes dans une fonction nœud.

    Définir la `filter` et `take` propriétés. Ne définissez pas `partitionKey` ou `rowKey`.

* Écrivez une ou plusieurs lignes dans une fonction c#.

    Le runtime fonctions fournit un `ICollector<T>` ou `IAsyncCollector<T>` lié à la table, où `T` Spécifie le schéma des entités que vous voulez ajouter. En règle générale, tapez `T` dérivée de `TableEntity` ou mettent en œuvre, `ITableEntity`, mais n’a pas besoin. La `partitionKey`, `rowKey`, `filter`, et `take` propriétés ne sont pas utilisées dans ce scénario.

#### <a name="storage-tables-example-read-a-single-table-entity-in-c-or-node"></a>Exemple de tables de stockage : lire une entité table unique dans c# ou nœud

L’exemple de code c# suivant fonctionne avec le fichier *function.json* précédent ci-dessus pour lire une entité table unique. Le message file d’attente contient la valeur de clé de ligne et l’entité de table est lue dans un type défini dans le fichier *run.csx* . Le type inclut `PartitionKey` et `RowKey` propriétés et ne pas dérivent `TableEntity`. 

```csharp
public static void Run(string myQueueItem, Person personEntity, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    log.Info($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

L’exemple suivant F # code fonctionne également avec le fichier *function.json* précédent pour lire une entité table unique.

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.Info(sprintf "Name in Person entity: %s" personEntity.Name)
```

L’exemple suivant nœud fonctionne également avec le fichier *function.json* précédent pour lire une entité table unique.

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

#### <a name="storage-tables-example-read-multiple-table-entities-in-c"></a>Exemple de tables de stockage : lire plusieurs entités de table dans C# 

Le suivant *function.json* et c# code exemple lectures entités pour une clé de partition spécifié dans le message file d’attente.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnection",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Le code c# ajoute une référence dans le Kit de développement de stockage Azure afin que le type d’entité pouvez dérivent `TableEntity`.

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.Info($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
``` 

#### <a name="storage-tables-example-create-table-entities-in-c"></a>Exemple de tables de stockage : créer des entités de table dans C# 

L’exemple *function.json* et *run.csx* suivant montre comment écrire entités de table en c#.

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

```csharp
public static void Run(string input, ICollector<Person> tableBinding, TraceWriter log)
{
    for (int i = 1; i < 10; i++)
        {
            log.Info($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

#### <a name="storage-tables-example-create-table-entities-in-f"></a>Exemple de tables de stockage : créer des entités de table dans F#

L’exemple *function.json* et *run.fsx* suivant montre comment écrire entités de table en F #.

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: TraceWriter) =
    for i = 1 to 10 do
        log.Info(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

#### <a name="storage-tables-example-create-a-table-entity-in-node"></a>Exemple de tables de stockage : créer une entité de table dans nœud

L’exemple *function.json* et *run.csx* suivant montre comment écrire une entité de table dans le nœud.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "name": "personEntity",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": true
}
```

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.bindings.personEntity = {"Name": "Name" + myQueueItem }
    context.done();
};
```

## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)] 
