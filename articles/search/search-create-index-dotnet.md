<properties
    pageTitle="Créer un index de recherche Azure en utilisant le Kit de développement .NET | Microsoft Azure | Service de recherche cloud hébergé"
    description="Créer un index dans le code à l’aide de la recherche Azure .NET SDK."
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
    manager="jhubbard"
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="brjohnst"/>

# <a name="create-an-azure-search-index-using-the-net-sdk"></a>Créer un index de recherche Azure en utilisant le Kit de développement .NET
> [AZURE.SELECTOR]
- [Vue d’ensemble](search-what-is-an-index.md)
- [Portail](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [RESTE](search-create-index-rest-api.md)


Cet article vous guidera tout au long du processus de création d’une recherche Azure [index](https://msdn.microsoft.com/library/azure/dn798941.aspx) à l’aide du [Kit de développement .NET Azure recherche](https://msdn.microsoft.com/library/azure/dn951165.aspx).

Avant de suivre ce guide et création d’un index, dont vous avez déjà [créé un service de recherche Azure](search-create-service-portal.md).

Notez que tous les exemples de code dans cet article est écrit en c#. Vous pouvez trouver la source complète de code [sur GitHub](http://aka.ms/search-dotnet-howto).

## <a name="i-identify-your-azure-search-services-admin-api-key"></a>J’ai. Identifier la clé api de l’administrateur de votre service de recherche Azure
À présent que vous avez configuré un service de recherche Azure, vous êtes presque prêt à émettre des requêtes par rapport à votre point de terminaison du service à l’aide du Kit de développement .NET. Tout d’abord, vous devrez obtenir un administrateur api-des clés de qui a été généré pour le service de recherche que vous sa mise en service. Le Kit de développement .NET vous envoie cette clé api à chaque demande à votre service. Avoir une clé valide établit approbation, sur une base par demande, entre l’application qui envoie la demande et le service qui la gère.

1. Pour rechercher des clés d’api de votre service que vous devez vous connecter au [Portail Azure](https://portal.azure.com/)
2. Accédez à la carte de votre service de recherche d’Azure
3. Cliquez sur l’icône « Touches »

Votre service aura *clés d’administration* et *requête*.

  - Vos *clés de l’administrateur* principal et secondaire accorder tous les droits sur toutes les opérations, y compris la possibilité pour gérer le service, créer et supprimer des index, indexeurs et sources de données. Il existe deux clés de sorte que vous pouvez continuer à utiliser la clé secondaire si vous décidez d’actualiser la clé primaire et vice versa.
  - Vos *clés de requête* accorder l’accès en lecture seule aux index et aux documents et sont généralement distribués aux applications clientes émettent des requêtes de recherche.

Aux fins de création d’un index, vous pouvez utiliser votre clé administrateur principal ou secondaire.

<a name="CreateSearchServiceClient"></a>
## <a name="ii-create-an-instance-of-the-searchserviceclient-class"></a>II. Créer une instance de la classe SearchServiceClient
Pour commencer à utiliser le Kit de développement .NET de recherche Azure, vous devez créer une instance de le `SearchServiceClient` cours. Cette classe possède plusieurs constructeurs. Celle qui vous intéresse prend le nom de votre service de recherche et une `SearchCredentials` objet en tant que paramètres. `SearchCredentials`Enveloppe votre clé de l’api.

Le code suivant crée un nouveau `SearchServiceClient` à l’aide de valeurs pour le nom de service de recherche et la clé api qui sont stockées dans le fichier de configuration de l’application (`app.config` ou `web.config`) :

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string adminApiKey = ConfigurationManager.AppSettings["SearchServiceAdminApiKey"];

SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
```

`SearchServiceClient`a un `Indexes` propriété. Cette propriété fournit toutes les méthodes que vous avez besoin pour créer, afficher, mettre à jour ou supprimer des index de recherche Azure.

> [AZURE.NOTE] La `SearchServiceClient` classe gère les connexions à votre service de recherche. Afin d’éviter l’ouverture des connexions trop, il est conseillé de partager une seule instance de `SearchServiceClient` dans votre application si possible. Ses méthodes sont thread-safe pour activer le partage.

<a name="DefineIndex"></a>
## <a name="iii-define-your-azure-search-index-using-the-index-class"></a>III. Définir votre index de recherche Azure à l’aide de la `Index` classe
Un seul appel à la `Indexes.Create` méthode crée votre index. Cette méthode prend comme paramètre un `Index` objet qui définit votre index de recherche Azure. Vous devez créer un `Index` objet et initialisation comme suit :

1. Définir la `Name` propriétés de la `Index` objet le nom de votre index.
2. Définir la `Fields` propriétés de la `Index` objet dans un tableau de `Field` objets. Chacun de la `Field` objets définit le comportement d’un champ dans l’index. Vous pouvez fournir le nom du champ auquel le constructeur, ainsi que le type de données (ou l’Analyseur de champs de type chaîne). Vous pouvez également définir d’autres propriétés telles que `IsSearchable`, `IsFilterable`, etc..

Il est important de maintenir votre recherche utilisateur expérience et des besoins à l’esprit lorsque vous créez votre index que chaque `Field` doit être affectée aux [propriétés appropriées](https://msdn.microsoft.com/library/azure/dn798941.aspx). Ces propriétés le contrôle qui fonctions (filtrage, faceting, le tri de la recherche en texte intégral, etc.) de recherche appliquer des champs. Pour les propriétés que vous ne définissez pas explicitement, le `Field` classe les valeurs par défaut à désactiver la fonctionnalité de recherche correspondante, sauf si vous l’activer.

Dans notre exemple, nous avons nommé notre index « hôtel » et défini nos champs comme suit :

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = new[]
    {
        new Field("hotelId", DataType.String)                       { IsKey = true, IsFilterable = true },
        new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("description", DataType.String)                   { IsSearchable = true },
        new Field("description_fr", AnalyzerName.FrLucene),
        new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true, IsSortable = true },
        new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
        new Field("smokingAllowed", DataType.Boolean)               { IsFilterable = true, IsFacetable = true },
        new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
    }
};
```

Nous avons choisi avec soin les valeurs de propriété pour chaque `Field` basée sur la manière dont nous ils seront utilisés dans une application. Par exemple, il est probable que les personnes recherchant hôtel sera intéressés correspondances de mots-clés sur les `description` de champ, afin de nous permettre de recherche en texte intégral de ce champ en définissant `IsSearchable` à `true`.

Veuillez noter qu’exactement un champ dans votre index de type `DataType.String` doit être la désignés en tant que le champ de _clé_ en définissant `IsKey` à `true` (voir `hotelId` dans l’exemple ci-dessus).

La définition d’index ci-dessus utilise un analyseur de langage personnalisé pour le `description_fr` champ parce qu’il est prévu pour stocker du texte Français. Consultez [la langue prise en charge de la rubrique sur MSDN](https://msdn.microsoft.com/library/azure/dn879793.aspx) ainsi que le correspondant [le billet de blog](https://azure.microsoft.com/blog/language-support-in-azure-search/) pour plus d’informations sur analyseurs de langue.

> [AZURE.NOTE]  Notez qu’en passant `AnalyzerName.FrLucene` dans le constructeur, la `Field` sera automatiquement de type `DataType.String` et créera `IsSearchable` défini sur `true`.

## <a name="iv-create-the-index"></a>IV. Créer l’index
Maintenant que vous avez initialisé `Index` objet, vous pouvez créer l’index en appelant simplement `Indexes.Create` sur votre `SearchServiceClient` objet :

```csharp
serviceClient.Indexes.Create(definition);
```

Pour une demande réussie, la méthode retournera normalement. S’il existe un problème avec la requête comme un paramètre non valide, la méthode lève `CloudException`.

Lorsque vous avez terminé avec un index et que vous voulez supprimer, appelons le `Indexes.Delete` méthode sur votre `SearchServiceClient`. Par exemple, il s’agit comment nous serait supprimer l’index « hôtel » :

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [AZURE.NOTE] L’exemple de code dans cet article utilise les méthodes synchrones du SDK .NET recherche Azure pour simplicité. Nous vous recommandons d’utiliser les méthodes asynchrones dans vos propres applications pour les garder scalable et réactif. Par exemple, dans l’exemple ci-dessus, vous pouvez utiliser `CreateAsync` et `DeleteAsync` au lieu de `Create` et `Delete`.

## <a name="next"></a>Suivant
Après avoir créé un index de recherche Azure, vous serez prêt à [télécharger votre contenu dans l’index](search-what-is-data-import.md) afin que vous pouvez démarrer la recherche de vos données.
