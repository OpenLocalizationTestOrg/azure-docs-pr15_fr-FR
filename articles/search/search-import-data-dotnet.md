<properties
    pageTitle="Téléchargement des données dans recherche Azure en utilisant le Kit de développement .NET | Microsoft Azure | Service de recherche cloud hébergé"
    description="Découvrez comment télécharger des données dans un index de recherche Azure en utilisant le Kit de développement .NET."
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
    manager="jhubbard"
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="brjohnst"/>

# <a name="upload-data-to-azure-search-using-the-net-sdk"></a>Télécharger des données sur recherche Azure en utilisant le Kit de développement .NET
> [AZURE.SELECTOR]
- [Vue d’ensemble](search-what-is-data-import.md)
- [.NET](search-import-data-dotnet.md)
- [RESTE](search-import-data-rest-api.md)

Cet article vous explique comment utiliser le [Kit de développement .NET Azure recherche](https://msdn.microsoft.com/library/azure/dn951165.aspx) pour importer des données dans un index de recherche Azure.

Avant de commencer cette procédure pas à pas, vous devez déjà avoir [créé un index de recherche Azure](search-what-is-an-index.md). Cet article suppose également que vous avez déjà créé un `SearchServiceClient` objet, comme illustré dans [créer un index de recherche Azure en utilisant le Kit de développement .NET](search-create-index-dotnet.md#CreateSearchServiceClient).

Notez que tous les exemples de code dans cet article est écrit en c#. Vous pouvez trouver la source complète de code [sur GitHub](http://aka.ms/search-dotnet-howto).

Pour distribuer des documents dans votre index à l’aide du Kit de développement .NET, vous devez :

  1. Créer un `SearchIndexClient` objet pour vous connecter à votre index de recherche.
  2. Créer un `IndexBatch` contenant les documents à ajouter, modifiés ou supprimés.
  3. Appelez le `Documents.Index` méthode de votre `SearchIndexClient` pour envoyer la `IndexBatch` à votre index de recherche.

## <a name="i-create-an-instance-of-the-searchindexclient-class"></a>J’ai. Créer une instance de la classe SearchIndexClient
Pour importer des données dans votre index à l’aide de la recherche Azure .NET SDK, vous devrez créer une instance de le `SearchIndexClient` cours. Vous pouvez construire cette instance vous-même, mais il est plus facile si vous possédez déjà un `SearchServiceClient` instance d’appeler son `Indexes.GetClient` méthode. Par exemple, voici comment obtenir un `SearchIndexClient` pour l’index nommé « hôtel » à partir d’un `SearchServiceClient` nommé `serviceClient`:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

> [AZURE.NOTE] Dans une application de recherche par défaut, remplissage et la gestion des index est gérée par un composant distinct de requêtes de recherche. `Indexes.GetClient`est pratique pour remplir un index, car elle vous évite le souci de fournir un autre `SearchCredentials`. Pour cela en passant la clé d’administration que vous avez utilisé pour créer la `SearchServiceClient` vers le nouveau `SearchIndexClient`. Toutefois, dans la partie de votre application qui exécute des requêtes, il est préférable de créer la `SearchIndexClient` directement afin que vous pouvez passer d’une clé de requête plutôt qu’une clé d’administration. C’est cohérente avec le [principe du moindre privilège](https://en.wikipedia.org/wiki/Principle_of_least_privilege) et que vous aide à renforcer la sécurité de votre application. Vous trouverez plus d’informations sur les clés d’administration et requête dans la [référence de l’API REST de recherche Azure sur MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx).

`SearchIndexClient`a un `Documents` propriété. Cette propriété fournit toutes les méthodes que vous voulez ajouter, modifier, supprimer ou interroger des documents dans votre index.

## <a name="ii-decide-which-indexing-action-to-use"></a>II. Décidez de l’action d’indexation à utiliser
Pour importer des données à l’aide du Kit de développement .NET, vous devez empaqueter vos données dans un `IndexBatch` objet. Un `IndexBatch` encapsule une collection de `IndexAction` objets, chacun d'entre eux contenant un document et une propriété qui indique Azure recherche action à effectuer sur ce document (téléchargement, fusion, suppression, etc.). En fonction de laquelle la sous actions que vous choisissez, uniquement certains champs doivent être inclus dans chaque document :

Action | Description | Champs nécessaires pour chaque document | Notes
--- | --- | --- | ---
`Upload` | Un `Upload` action est similaire à un « upsert » dans laquelle le document sera inséré s’il est nouveau et mis à jour/remplacé s’il existe. | clé, ainsi que tous les autres champs que vous souhaitez définir | Lors de la mise à jour/remplacement d’un document existant, n’importe quel champ qui n’est pas spécifié dans la demande aura son champ de valeur `null`. Cela se produit même lorsque le champ a été défini précédemment à une valeur non nulle.
`Merge` | Met à jour un document existant avec les champs spécifiés. Si le document n’existe pas dans l’index, la fusion échoue. | clé, ainsi que tous les autres champs que vous souhaitez définir | N’importe quel champ que vous spécifiez dans une fusion remplacera le champ existant dans le document. Cela inclut les champs de type `DataType.Collection(DataType.String)`. Par exemple, si le document contient un champ `tags` avec valeur `["budget"]` et que vous exécutez une fusion avec valeur `["economy", "pool"]` pour `tags`, la dernière valeur de la `tags` champ sera `["economy", "pool"]`. Il ne sera plus `["budget", "economy", "pool"]`.
`MergeOrUpload` | Cette action se comporte comme `Merge` s’il existe déjà un document avec la touche donnée dans l’index. Si le document n’existe pas, il se comporte comme `Upload` avec un nouveau document. | clé, ainsi que tous les autres champs que vous souhaitez définir | -
`Delete` | Supprime le document spécifié de l’index. | clé uniquement | Les champs que vous spécifiez autre que le champ de clé sont ignoré. Si vous voulez supprimer un champ individuel à partir d’un document, utilisez `Merge` à la place et simplement la valeur du champ explicitement null.

Vous pouvez spécifier l’action que vous voulez utiliser avec les différentes méthodes statiques de la `IndexBatch` et `IndexAction` classes, comme indiqué dans la section suivante.

## <a name="iii-construct-your-indexbatch"></a>III. Construire votre IndexBatch
Maintenant que vous connaissez les actions à effectuer sur vos documents, vous êtes prêt à créer le `IndexBatch`. L’exemple ci-dessous montre comment créer un lot avec quelques différentes actions. Notez que notre exemple utilise une classe personnalisée appelée `Hotel` qui correspond à un document dans l’index « hôtel ».

```csharp
var actions =
    new IndexAction<Hotel>[]
    {
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "1",
                BaseRate = 199.0,
                Description = "Best hotel in town",
                DescriptionFr = "Meilleur hôtel en ville",
                HotelName = "Fancy Stay",
                Category = "Luxury",
                Tags = new[] { "pool", "view", "wifi", "concierge" },
                ParkingIncluded = false,
                SmokingAllowed = false,
                LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                Rating = 5,
                Location = GeographyPoint.Create(47.678581, -122.131577)
            }),
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "2",
                BaseRate = 79.99,
                Description = "Cheapest hotel in town",
                DescriptionFr = "Hôtel le moins cher en ville",
                HotelName = "Roach Motel",
                Category = "Budget",
                Tags = new[] { "motel", "budget" },
                ParkingIncluded = true,
                SmokingAllowed = true,
                LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                Rating = 1,
                Location = GeographyPoint.Create(49.678581, -122.131577)
            }),
        IndexAction.MergeOrUpload(
            new Hotel()
            {
                HotelId = "3",
                BaseRate = 129.99,
                Description = "Close to town hall and the river"
            }),
        IndexAction.Delete(new Hotel() { HotelId = "6" })
    };

var batch = IndexBatch.New(actions);
```

Dans ce cas, nous utilisons `Upload`, `MergeOrUpload`, et `Delete` comme nos actions de recherche, tel que spécifié par les méthodes appelées sur le `IndexAction` cours.

Part du principe que cet index « hôtel » exemple est déjà rempli avec un nombre de documents. Notez la manière dont nous n’avez pas à spécifier tous les champs de document possibles lorsque vous utilisez `MergeOrUpload` et comment nous spécifié uniquement la clé de document (`HotelId`) lorsque vous utilisez `Delete`.

Notez également que vous pouvez inclure uniquement jusqu'à 1000 documents dans une seule demande d’indexation.

> [AZURE.NOTE] Dans cet exemple, nous allons appliquant différentes actions vers d’autres documents. Si vous souhaitez effectuer les mêmes actions sur tous les documents dans le lot, au lieu d’appeler `IndexBatch.New`, vous pouvez aussi utiliser les autres méthodes statiques de `IndexBatch`. Par exemple, vous pouvez créer des lots en appelant `IndexBatch.Merge`, `IndexBatch.MergeOrUpload`, ou `IndexBatch.Delete`. Ces méthodes définissent un ensemble de documents (objets de type `Hotel` dans cet exemple) au lieu de `IndexAction` objets.

## <a name="iv-import-data-to-the-index"></a>IV. Importer des données à l’index
Maintenant que vous avez initialisé `IndexBatch` objet, vous pouvez l’envoyer à l’index en appelant `Documents.Index` sur votre `SearchIndexClient` objet. L’exemple suivant montre comment appeler `Index`, ainsi que quelques étapes supplémentaires que vous devez effectuer :

```csharp
try
{
    indexClient.Documents.Index(batch);
}
catch (IndexBatchException e)
{
    // Sometimes when your Search service is under load, indexing will fail for some of the documents in
    // the batch. Depending on your application, you can take compensating actions like delaying and
    // retrying. For this simple demo, we just log the failed document keys and continue.
    Console.WriteLine(
        "Failed to index some of the documents: {0}",
        String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
}

Console.WriteLine("Waiting for documents to be indexed...\n");
Thread.Sleep(2000);
```

Remarque la `try` / `catch` entourant l’appel vers la `Index` méthode. Le bloc de captures gère un cas d’erreur importantes pour l’indexation. Si votre service de recherche Azure ne parvient pas à certaines des documents dans le lot, un `IndexBatchException` est levée par `Documents.Index`. Cela peut arriver si vous indexez documents pendant que votre service est surchargé. **Nous vous recommandons vivement explicitement traitant ce dossier dans votre code.** Vous pouvez retarder, puis recommencez l’indexation les documents qui a échoué, ou vous pouvez ouvrir une session et poursuivre l’exemple effectue, ou vous pouvez effectuer une opération autre selon les besoins de votre application de la cohérence des données.

Enfin, le code dans l’exemple ci-dessus retards pendant deux secondes. L’indexation s’est-il passé asynchrone dans votre service de recherche Azure, afin de l’exemple d’application doit attendre quelques instants pour vous assurer que les documents sont disponibles pour la recherche. Retards à ceci ne sont généralement nécessaires dans démonstrations des tests et des exemples d’applications.

<a name="HotelClass"></a>
### <a name="how-the-net-sdk-handles-documents"></a>Comment le Kit de développement .NET traite les documents

Vous vous demandez peut-être comment le Kit de développement Azure recherche .NET est en mesure de télécharger les instances d’une classe définie par l’utilisateur comme `Hotel` à l’index. Pour vous aider à répondre à cette question, nous allons étudier le `Hotel` classe, qui est mappé au schéma index défini dans [créer un index de recherche Azure en utilisant le Kit de développement .NET](search-create-index-dotnet.md#DefineIndex):

```csharp
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    public string HotelId { get; set; }

    public double? BaseRate { get; set; }

    public string Description { get; set; }

    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    public string HotelName { get; set; }

    public string Category { get; set; }

    public string[] Tags { get; set; }

    public bool? ParkingIncluded { get; set; }

    public bool? SmokingAllowed { get; set; }

    public DateTimeOffset? LastRenovationDate { get; set; }

    public int? Rating { get; set; }

    public GeographyPoint Location { get; set; }

    // ToString() method omitted for brevity...
}
```

La première chose à noter est que chaque propriété publique `Hotel` correspond à un champ dans la définition d’index, mais avec une différence essentielle : le nom de chaque champ commence par une lettre en minuscule (« casse mixte »), tandis que le nom de chaque propriété publique `Hotel` commence par une lettre en majuscule (« casse Pascal »). Il s’agit d’un scénario courant dans les applications .NET qui effectuer la liaison de données où le schéma cible est en dehors du contrôle développeur de l’application. Plutôt que d’avoir à violer .NET conventions en effectuant casse mixte propriété noms, vous pouvez déterminer le Kit de développement pour établir une correspondance entre les noms de propriété casse mixte automatiquement à l’aide du `[SerializePropertyNamesAsCamelCase]` attribut.

> [AZURE.NOTE] Le Kit de développement .NET de recherche Azure utilise la bibliothèque [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) pour sérialiser et désérialiser des objets de votre modèle personnalisé vers et depuis JSON. Vous pouvez personnaliser cette sérialisation si nécessaire. Vous trouverez plus d’informations dans la [mise à niveau vers le Kit de développement Azure recherche .NET version 1.1](search-dotnet-sdk-migration.md#WhatsNew). Un exemple est l’utilisation de la `[JsonProperty]` attribut sur la `DescriptionFr` propriété dans le code de l’exemple ci-dessus.

La deuxième plus important de la `Hotel` cours sont les types de données des propriétés publiques. Les types de .NET ces propriétés correspondent à leurs types de champ équivalent dans la définition d’index. Par exemple, le `Category` chaîne mappages des propriétés de la `category` champ, qui est de type `DataType.String`. Il existe des mappages de type similaire entre `bool?` et `DataType.Boolean`, `DateTimeOffset?` et `DataType.DateTimeOffset`, etc.. Les règles spécifiques pour le mappage de type sont présentées avec la `Documents.Get` méthode sur [MSDN](https://msdn.microsoft.com/library/azure/dn931291.aspx).

Cette fonctionnalité permettant d’utiliser vos propres classes en tant que documents fonctionne dans les deux sens ; Vous pouvez également extraire les résultats de recherche et que le Kit de développement désérialiser automatiquement les à un type de votre choix, comme illustré dans l' [article suivant](search-query-dotnet.md).

> [AZURE.NOTE] Le Kit de développement .NET de recherche Azure prend également en charge les documents dynamiquement tapé à l’aide de la `Document` classe, qui est un mappage de clé/valeur de noms de champs aux valeurs de champ. Ceci est utile dans les scénarios où vous ne connaissez pas le schéma d’index au moment de la conception ou l’emplacement dans lequel il serait pas pratique créer une liaison aux classes de modèle spécifique. Toutes les méthodes dans le Kit de développement qui gèrent les documents ont des surcharges qui fonctionnent avec la `Document` cours, ainsi que les surcharges fortement typée qui prennent un paramètre de type générique. Ce dernier est utilisé dans l’exemple de code dans cet article. Vous pouvez trouver plus d’informations sur la `Document` de cours [sur MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.document.aspx).

**Une note importante sur les types de données**

Lorsque vous créez vos propres classes de modèle pour établir une correspondance entre un index de recherche Azure, nous vous recommandons de déclarer des propriétés de types valeur tels que `bool` et `int` Nullable (par exemple, `bool?` au lieu de `bool`). Si vous utilisez une propriété non nullable, vous devez **vous assurer** qu’aucune des documents dans votre index ne contiennent une valeur null pour le champ correspondant. Le service de recherche Azure ni le Kit de développement vous aidera à appliquer cette.

Ce n’est pas simplement un problème hypothétique : imaginer un scénario où vous ajoutez un nouveau champ à un index existant qui est de type `DataType.Int32`. Après mise à jour la définition d’index, tous les documents aura une valeur null pour ce nouveau champ (dans la mesure où tous les types sont nullables dans Azure recherche). Si vous utilisez ensuite une classe du modèle avec non nullable `int` propriété de ce champ, vous obtiendrez un `JsonSerializationException` comme suit lorsque vous essayez d’extraire des documents :

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Pour cette raison, nous vous recommandons d’utiliser types nullable dans votre cours modèle comme une meilleure pratique.

## <a name="next"></a>Suivant
Après avoir rempli votre index de recherche Azure, vous serez prêt à commencer à les distribuer des requêtes pour rechercher des documents. Pour plus d’informations, voir [Index de recherche Azure votre requête](search-query-overview.md) .
