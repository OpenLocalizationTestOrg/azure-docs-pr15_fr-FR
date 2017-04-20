<properties
    pageTitle="Interroger votre Index de recherche Azure à l’aide du Kit de développement .NET | Microsoft Azure | Service de recherche cloud hébergé"
    description="Créer une requête de recherche dans Azure rechercher et utiliser des paramètres de recherche pour filtrer et trier les résultats de recherche."
    services="search"
    manager="jhubbard"
    documentationCenter=""
    authors="brjohnstmsft"
/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="brjohnst"/>

# <a name="query-your-azure-search-index-using-the-net-sdk"></a>Interroger votre index de recherche Azure à l’aide du Kit de développement .NET
> [AZURE.SELECTOR]
- [Vue d’ensemble](search-query-overview.md)
- [Portail](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [RESTE](search-query-rest-api.md)

Cet article vous explique comment interroger un index à l’aide du [Kit de développement .NET Azure recherche](https://msdn.microsoft.com/library/azure/dn951165.aspx).

Avant de commencer cette procédure pas à pas, vous disposez déjà [créé un index de recherche Azure](search-what-is-an-index.md) et [rempli avec les données](search-what-is-data-import.md).

Notez que tous les exemples de code dans cet article est écrit en c#. Vous pouvez trouver la source complète de code [sur GitHub](http://aka.ms/search-dotnet-howto).

## <a name="i-identify-your-azure-search-services-query-api-key"></a>J’ai. Identifier requête api-clé de votre service recherche Azure
À présent que vous avez créé un index de recherche Azure, vous êtes presque prêt à émettre des requêtes en utilisant le Kit de développement .NET. Tout d’abord, vous devrez obtenir une requête api-des clés de qui a été généré pour le service de recherche que vous sa mise en service. Le Kit de développement .NET vous envoie cette clé api à chaque demande à votre service. Avoir une clé valide établit approbation, sur une base par demande, entre l’application qui envoie la demande et le service qui la gère.

1. Pour rechercher des clés d’api de votre service que vous devez vous connecter au [Portail Azure](https://portal.azure.com/)
2. Accédez à la carte de votre service de recherche d’Azure
3. Cliquez sur l’icône « Touches »

Votre service aura *clés d’administration* et *requête*.

  - Vos *clés de l’administrateur* principal et secondaire accorder tous les droits sur toutes les opérations, y compris la possibilité pour gérer le service, créer et supprimer des index, indexeurs et sources de données. Il existe deux clés de sorte que vous pouvez continuer à utiliser la clé secondaire si vous décidez d’actualiser la clé primaire et vice versa.
  - Vos *clés de requête* accorder l’accès en lecture seule aux index et aux documents et sont généralement distribués aux applications clientes émettent des requêtes de recherche.

Aux fins d’interrogation d’un index, vous pouvez utiliser un de vos clés de requête. Vos clés d’administration peuvent également être utilisées pour les requêtes, mais vous devez utiliser une clé de requête dans le code de votre application comme ceci mieux suit le [principe du moindre privilège](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

## <a name="ii-create-an-instance-of-the-searchindexclient-class"></a>II. Créer une instance de la classe SearchIndexClient
Pour émettre des requêtes avec le Kit de développement .NET de recherche Azure, vous devez créer une instance de le `SearchIndexClient` cours. Cette classe possède plusieurs constructeurs. Celui que vous souhaitez prend votre nom de service de recherche, le nom de l’index et un `SearchCredentials` objet en tant que paramètres. `SearchCredentials`Enveloppe votre clé de l’api.

Le code suivant crée un nouveau `SearchIndexClient` pour l’index « hôtel » (créé dans [créer un index de recherche Azure en utilisant le Kit de développement .NET](search-create-index-dotnet.md)) à l’aide de valeurs pour le nom de service de recherche et la clé api qui sont stockées dans le fichier de configuration de l’application (`app.config` ou `web.config`) :

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string queryApiKey = ConfigurationManager.AppSettings["SearchServiceQueryApiKey"];

SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
```

`SearchIndexClient`a un `Documents` propriété. Cette propriété fournit toutes les méthodes que vous deviez la requête d’index de recherche Azure.

## <a name="iii-query-your-index"></a>III. Interrogez vos index
Recherche avec le Kit de développement .NET est aussi simple que vous appelez le `Documents.Search` méthode sur votre `SearchIndexClient`. Cette méthode accepte quelques paramètres, y compris le texte de recherche, avec une `SearchParameters` objet qui peut être utilisé pour affiner la requête.

#### <a name="types-of-queries"></a>Types de requêtes
Les deux principaux [types de requêtes](search-query-overview.md#types-of-queries) que vous utiliserez sont `search` et `filter`. A `search` recherches pour un ou plusieurs termes dans tous les champs de _recherche_ dans votre index de la requête. A `filter` requête évalue une expression booléenne sur tous _filtrables_ champs dans un index.

Recherche et filtres sont effectuées à l’aide de la `Documents.Search` méthode. Une requête de recherche peut être passée dans la `searchText` paramètre, tandis qu’une expression de filtre peut être passée dans les `Filter` propriété de la `SearchParameters` cours. Pour filtrer les données sans rechercher, passer simplement `"*"` pour la `searchText` paramètre. Pour effectuer une recherche sans filtrage, laissez la `Filter` propriété Annuler, ou ne passez pas dans une `SearchParameters` instance du tout.

#### <a name="example-queries"></a>Exemples de requêtes

Le code suivant montre quelques méthodes pour interroger l’index « hôtel » défini dans [créer un index de recherche Azure en utilisant le Kit de développement .NET](search-create-index-dotnet.md#DefineIndex). Notez que les documents renvoyés avec les résultats de recherche sont des instances de le `Hotel` classe, qui a été définie dans [L’importation des données dans recherche Azure en utilisant le Kit de développement .NET](search-import-data-dotnet.md#HotelClass). L’exemple de code utilise un `WriteDocuments` méthode pour générer les résultats de recherche à la console. Cette méthode est décrite dans la section suivante.

```csharp
SearchParameters parameters;
DocumentSearchResult<Hotel> results;

Console.WriteLine("Search the entire index for the term 'budget' and return only the hotelName field:\n");

parameters =
    new SearchParameters()
    {
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);

Console.Write("Apply a filter to the index to find hotels cheaper than $150 per night, ");
Console.WriteLine("and return the hotelId and description:\n");

parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
Console.Write("in descending order, take the top two results, and show only hotelName and ");
Console.WriteLine("lastRenovationDate:\n");

parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'motel':\n");

parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

## <a name="iv-handle-search-results"></a>IV. Gérer les résultats de recherche
La `Documents.Search` méthode renvoie un `DocumentSearchResult` objet qui contient les résultats de la requête. L’exemple dans la section précédente utilisé une méthode appelée `WriteDocuments` pour générer les résultats de recherche à la console :

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

Voici ce que les résultats ressemblent aux requêtes dans la section précédente, en supposant que l’index « hôtel » est remplie avec les données d’exemple dans [L’importation des données dans recherche Azure en utilisant le Kit de développement .NET](search-import-data-dotnet.md):

```
Search the entire index for the term 'budget' and return only the hotelName field:

Name: Roach Motel

Apply a filter to the index to find hotels cheaper than $150 per night, and return the hotelId and description:

ID: 2   Description: Cheapest hotel in town
ID: 3   Description: Close to town hall and the river

Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Search the entire index for the term 'motel':

ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577

```

Le code de l’exemple ci-dessus utilise la console pour générer les résultats de recherche. Vous avez besoin de la même manière afficher des résultats de recherche dans votre propre application. Consultez [Cet exemple sur GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetSample) pour obtenir un exemple de façon à afficher les résultats de la recherche dans une application web basée sur ASP.NET MVC.
