<properties
   pageTitle="Mise à niveau vers le Kit de développement Azure recherche .NET version 1.1 | Microsoft Azure | Service de recherche cloud hébergé"
   description="Mise à niveau vers le Kit de développement Azure recherche .NET version 1.1"
   services="search"
   documentationCenter=""
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="08/15/2016"
   ms.author="brjohnst"/>

# <a name="upgrading-to-the-azure-search-net-sdk-version-20-preview"></a>Mise à niveau vers la version Azure recherche .NET SDK 2.0-aperçu

Si vous utilisez la version 1.1 ou une version antérieure du [Kit de développement .NET Azure recherche](https://msdn.microsoft.com/library/azure/dn951165.aspx), cet article vous aidera à mettre à niveau votre application pour utiliser la version majeure suivante, 2.0 preview.

Pour une procédure pas à pas plus générale du Kit de développement, notamment des exemples, voir [comment utiliser la recherche Azure à partir d’une Application .NET](search-howto-dotnet-sdk.md).

Version 2.0-aperçu de la recherche Azure .NET SDK contient certaines modifications provenant de versions antérieures. Il s’agit des principalement secondaires, afin que la modification votre code nécessite un minimum de tâches. Consultez les [étapes pour mettre à niveau](#UpgradeSteps) pour obtenir des instructions sur la façon de modifier votre code utiliser la nouvelle version du Kit de développement logiciel.

> [AZURE.NOTE] Si vous utilisez la version preview de 0,13 ou version antérieure, vous devez mettre à niveau vers la version 1.1 prénom et mise à niveau puis 2.0 Preview. Voir [appendice : étapes pour mettre à niveau vers la version 1.1](#UpgradeStepsV1) pour obtenir des instructions.

<a name="WhatsNew"></a>
## <a name="whats-new-in-version-20-preview"></a>Quelles sont les nouveautés dans la version 2.0-aperçu

Version 2.0-aperçu est la première version du SDK .NET Azure recherche afin de cibler une version preview de l’API REST recherche Azure, spécifiquement 2015-02-28-aperçu. Cela permet d’utiliser de nombreuses fonctionnalités d’aperçu de la recherche Azure à partir d’une application .NET, y compris les éléments suivants :

- [Analyseurs personnalisés](https://aka.ms/customanalyzers)
- Prise en charge indexeur [Stockage d’objets Blob Azure](search-howto-indexing-azure-blob-storage.md) et de [Stockage de Table Azure](search-howto-indexing-azure-tables.md)
- Personnalisation indexeur via les [mappages de champs](search-indexer-field-mappings.md)
- ETags support pour activer la mise à jour simultanées sûre de définitions d’index, indexeurs et sources de données
- Prise en charge pour les principaux .NET et .NET Portable profil 111

<a name="UpgradeSteps"></a>
## <a name="steps-to-upgrade"></a>Étapes à suivre pour mettre à niveau

Tout d’abord, mettre à jour votre référence NuGet pour `Microsoft.Azure.Search` à l’aide de la Console Gestionnaire de Package NuGet ou en cliquant sur les références de votre projet et en sélectionnant « Gérer NuGet Packages... » dans Visual Studio. Vérifiez que vous activez en version précommerciale packages en sélectionnant « Inclure la version préliminaire » dans le « Gérer les Packages » NuGet fenêtre dans Visual Studio, ou à l’aide de la `-IncludePrerelease` basculer si vous utilisez le Gestionnaire de Package NuGet Console.

Une fois que NuGet a téléchargé les nouveaux packages et leurs dépendances, régénérer votre projet. Selon la façon dont votre code est structuré, il peut reconstruire avec succès. Si c’est le cas, vous êtes prêt à être envoyé !

Si votre génération échoue, vous devriez voir une erreur de build comme suit :

    Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)

L’étape suivante consiste à corriger cette erreur de génération. Pour plus d’informations sur ce qui provoque l’erreur et comment résoudre le problème, voir [Nouveautés dans la version 2.0-aperçu](#ListOfChanges) .

Vous pouvez constater des avertissements de génération supplémentaires relatifs aux méthodes obsolètes ou propriétés. Les avertissements inclut des instructions sur les éléments à utiliser à la place de la fonctionnalité déconseillée. Par exemple, si votre application utilise le `SearchRequestOptions.RequestId` propriété, vous devez obtenir un message d’avertissement qui indique`"This property is deprecated. Please use ClientRequestId instead."`

Une fois que vous avez corrigé les erreurs de génération, vous pouvez apporter des modifications à votre application pour tirer parti des nouvelles fonctionnalités si vous le souhaitez. Nouvelles fonctionnalités dans le Kit de développement sont détaillées dans [Nouveautés de version 2.0-aperçu](#WhatsNew).

<a name="ListOfChanges"></a>
## <a name="breaking-changes-in-version-20-preview"></a>Nouveautés dans la version 2.0 preview

Il existe une modification avec rupture version 2.0-aperçu qui peuvent nécessiter des modifications de code en plus de reconstruction de votre application.

### <a name="indexesgetclient-return-type"></a>Type de retour Indexes.GetClient

La `Indexes.GetClient` méthode a un nouveau type de retour. Auparavant, il retourné `SearchIndexClient`, mais il a été remplacé par `ISearchIndexClient` dans la version 2.0-aperçu. Il s’agit pour prendre en charge aux clients qui souhaitent simuler la `GetClient` méthode pour les tests unitaires en renvoyant une implémentation factice de `ISearchIndexClient`.

#### <a name="example"></a>Exemple

Si votre code ressemble à ceci :

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

Vous pouvez le modifier à cette option pour corriger les erreurs de build :

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

## <a name="conclusion"></a>Conclusion
Si vous avez besoin de plus d’informations sur l’utilisation de la recherche Azure .NET SDK, consultez notre [procédures](search-howto-dotnet-sdk.md)récemment mis à jour.

Nous espérons recevoir vos commentaires sur le Kit de développement. Si vous rencontrez des problèmes, n’hésitez pas à nous demander de l’aide sur le [forum MSDN de recherche Azure](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuresearch). Si vous trouvez un bogue, vous pouvez créer un problème dans le [référentiel Azure .NET SDK GitHub](https://github.com/Azure/azure-sdk-for-net/issues). N’oubliez pas de préfixe le titre de votre problème avec « Search SDK : ».

Nous vous remercions pour utiliser la recherche Azure.

<a name="UpgradeStepsV1"></a>
## <a name="appendix-steps-to-upgrade-to-version-11"></a>Appendice : Étapes mise à niveau vers la version 1.1 

> [AZURE.NOTE] Cette section s’applique uniquement aux utilisateurs de la version Azure recherche .NET SDK 0,13 preview et systèmes plus ancienne.

Tout d’abord, mettre à jour votre référence NuGet pour `Microsoft.Azure.Search` à l’aide de la Console Gestionnaire de Package NuGet ou en cliquant sur les références de votre projet et en sélectionnant « Gérer NuGet Packages... » dans Visual Studio.

Une fois que NuGet a téléchargé les nouveaux packages et leurs dépendances, régénérer votre projet.

Si vous utilisiez auparavant version 1.0.0-preview, 1.0.1-preview ou 1.0.2-preview, la génération doit réussir et que vous êtes prêt à être envoyé !

Si vous utilisiez auparavant version 0.13.0-preview ou version antérieure, vous devriez voir créer des erreurs comme suit :

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

L’étape suivante consiste à corriger les erreurs de génération un par un. La plupart nécessitent modifiant certains noms de classe et la méthode qui ont été renommés dans le Kit de développement. [Liste des modifications dans la version 1.1](#ListOfChangesV1) contient la liste de ces modifications de nom.

Si vous utilisez des classes personnalisées pour modéliser vos documents, et ces cours ont des propriétés de types primitifs non nullable (par exemple, `int` ou `bool` en c#), il existe un correctif de bogues dans la version 1.1 du Kit de développement dont vous devez être informé. Pour plus d’informations, voir [correctifs dans la version 1.1](#BugFixesV1) .

Enfin, une fois que vous avez corrigé les erreurs de génération, vous pouvez apporter des modifications à votre application pour tirer parti des nouvelles fonctionnalités si vous le souhaitez.

<a name="ListOfChangesV1"></a>
### <a name="list-of-breaking-changes-in-version-11"></a>Liste des modifications dans la version 1.1

La liste suivante est classée par la probabilité que la modification affecte votre code de l’application.

#### <a name="indexbatch-and-indexaction-changes"></a>Modifications IndexBatch et IndexAction

`IndexBatch.Create`a été renommé `IndexBatch.New` et n’a plus un `params` argument. Vous pouvez utiliser `IndexBatch.New` pour les lots qui combiner les différents types d’actions (fusions, suppressions, etc.). Par ailleurs, il existe nouvelles méthodes statiques pour la création des lots où toutes les actions sont identiques : `Delete`, `Merge`, `MergeOrUpload`, et `Upload`.

`IndexAction`n’a plus constructeurs publics et ses propriétés sont immuables. Vous devez utiliser les nouvelles méthodes statiques pour la création d’actions à des fins différentes : `Delete`, `Merge`, `MergeOrUpload`, et `Upload`. `IndexAction.Create`a été supprimée. Si vous avez utilisé la surcharge qui prend uniquement un document, vérifiez que vous utilisez `Upload` à la place.

##### <a name="example"></a>Exemple

Si votre code ressemble à ceci :

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

Vous pouvez le modifier à cette option pour corriger les erreurs de build :

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

Si vous le souhaitez, vous pouvez encore le simplifier à ceci :

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

#### <a name="indexbatchexception-changes"></a>Modifications IndexBatchException

La `IndexBatchException.IndexResponse` propriété a été renommée `IndexingResults`, et son type est maintenant `IList<IndexingResult>`.

##### <a name="example"></a>Exemple

Si votre code ressemble à ceci :

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

Vous pouvez le modifier à cette option pour corriger les erreurs de build :

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>
#### <a name="operation-method-changes"></a>Modifications de méthode d’opération

Chaque opération dans le Kit de développement .NET de recherche Azure est exposée comme un ensemble de surcharges de méthode pour les appelants synchrones et asynchrones. Les signatures et tenant compte de ces surcharges de méthode a changé dans la version 1.1.

Par exemple, l’opération « Obtenir des statistiques d’Index » dans les anciennes versions du Kit de développement exposé ces signatures :

In `IIndexOperations`:

    // Asynchronous operation with all parameters
    Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        string indexName,
        CancellationToken cancellationToken);

In `IndexOperationsExtensions`:

    // Asynchronous operation with only required parameters
    public static Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        this IIndexOperations operations,
        string indexName);

    // Synchronous operation with only required parameters
    public static IndexGetStatisticsResponse GetStatistics(
        this IIndexOperations operations,
        string indexName);

Les signatures de méthode pour la même opération dans la version 1.1 ressemblent à ceci :

In `IIndexesOperations`:

    // Asynchronous operation with lower-level HTTP features exposed
    Task<AzureOperationResponse<IndexGetStatisticsResult>> GetStatisticsWithHttpMessagesAsync(
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        Dictionary<string, List<string>> customHeaders = null,
        CancellationToken cancellationToken = default(CancellationToken));

In `IndexesOperationsExtensions`:

    // Simplified asynchronous operation
    public static Task<IndexGetStatisticsResult> GetStatisticsAsync(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        CancellationToken cancellationToken = default(CancellationToken));

    // Simplified synchronous operation
    public static IndexGetStatisticsResult GetStatistics(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions));

Depuis la version 1.1, le Kit de développement .NET de recherche Azure organise les méthodes d’opération différemment :

 - Paramètres facultatifs sont appuient sur maintenant par défaut des paramètres plutôt que surcharges de méthode supplémentaires. Cela réduit le nombre de surcharges de méthode, parfois considérablement.
 - Les méthodes d’extension masquent désormais un grand nombre de superflus des détails HTTP à partir de l’appelant. Par exemple, les versions antérieures du Kit de développement renvoyaient un objet de réponse avec un code d’état HTTP, dont vous n’avez pas souvent besoin à vérifier, car les méthodes d’opération lever `CloudException` pour tout code d’état indiquant une erreur. Les nouvelles méthodes d’extension simplement retournent des objets de modèle, vous évite d’avoir à les renvoi dans votre code.
 - En revanche, les principaux interfaces exposent maintenant les méthodes qui vous offrent davantage de contrôle au niveau HTTP si vous en avez besoin. Vous pouvez maintenant passer dans les en-têtes HTTP personnalisés à inclure dans les demandes et les nouvelles `AzureOperationResponse<T>` type de retour vous permet d’accéder directement à la `HttpRequestMessage` et `HttpResponseMessage` pour l’opération. `AzureOperationResponse`est définie dans le `Microsoft.Rest.Azure` espace de noms et remplace `Hyak.Common.OperationResponse`.

#### <a name="scoringparameters-changes"></a>Modifications ScoringParameters

Une nouvelle classe nommée `ScoringParameter` a été ajouté dans la dernière version du SDK pour faciliter leur fournir des paramètres pour le score profils dans une requête de recherche. Précédemment la `ScoringProfiles` propriétés de la `SearchParameters` classe a été tapé en tant que `IList<string>`; À présent, il est entré en tant que `IList<ScoringParameter>`.

##### <a name="example"></a>Exemple

Si votre code ressemble à ceci :

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };

Vous pouvez le modifier à cette option pour corriger les erreurs de build : 

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters =
        new[]
        {
            new ScoringParameter("featuredParam", new[] { "featured" }),
            new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
        };

#### <a name="model-class-changes"></a>Modifications apportées au modèle classe

En raison de la signature change décrit dans la [méthode opération modifie](#OperationMethodChanges), de nombreuses classes dans le `Microsoft.Azure.Search.Models` espace de noms ont été renommés ou supprimés. Par exemple :

 - `IndexDefinitionResponse`a été remplacé par`AzureOperationResponse<Index>`
 - `DocumentSearchResponse`a été renommé`DocumentSearchResult`
 - `IndexResult`a été renommé`IndexingResult`
 - `Documents.Count()`retourne maintenant une `long` avec le nombre de document au lieu d’un`DocumentCountResponse`
 - `IndexGetStatisticsResponse`a été renommé`IndexGetStatisticsResult`
 - `IndexListResponse`a été renommé`IndexListResult`

Pour résumer, `OperationResponse`-dérivée de classes existantes uniquement pour placer un objet de modèle ont été supprimés. Les classes restantes ont leur suffixe passé de `Response` à `Result`.

##### <a name="example"></a>Exemple

Si votre code ressemble à ceci :

    IndexerGetStatusResponse statusResponse = null;

    try
    {
        statusResponse = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = statusResponse.ExecutionInfo.LastResult;

Vous pouvez le modifier à cette option pour corriger les erreurs de build :

    IndexerExecutionInfo status = null;

    try
    {
        status = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = status.LastResult;

##### <a name="response-classes-and-ienumerable"></a>Cours de réponse et IEnumerable

Une modification supplémentaire qui peut-être affecter votre code est que classes réponse qui contiennent des ensembles implémentent n’est plus `IEnumerable<T>`. À la place, vous pouvez accéder directement la propriété de collection de sites. Par exemple, si votre code ressemble à ceci :

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

Vous pouvez le modifier à cette option pour corriger les erreurs de build :

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

##### <a name="important-note-for-web-applications"></a>Note importante pour les applications web

Si vous avez une application web qui sérialise `DocumentSearchResponse` directement pour envoyer les résultats de recherche dans le navigateur, vous devez modifier votre code ou les résultats sérialisera pas correctement. Par exemple, si votre code ressemble à ceci :

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q)
        };
    }

Vous pouvez le modifier en obtenant le `.Results` propriétés de la réponse de recherche pour résoudre le rendu des résultats de recherche :

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q).Results
        };
    }

Vous devrez peut-être le rechercher dans ce cas dans votre code vous-même ; **Le compilateur ne vous avertit pas** , car `JsonResult.Data` est de type `object`.

#### <a name="cloudexception-changes"></a>Modifications CloudException

La `CloudException` classe a été déplacée à partir de la `Hyak.Common` espace de noms à le `Microsoft.Rest.Azure` espace de noms. En outre, sa `Error` propriété a été renommée `Body`.

#### <a name="searchserviceclient-and-searchindexclient-changes"></a>Modifications SearchServiceClient et SearchIndexClient

Le type de la `Credentials` propriété a été modifiée à partir de `SearchCredentials` à sa classe de base, `ServiceClientCredentials`. Si vous avez besoin pour accéder à la `SearchCredentials` d’un `SearchIndexClient` ou `SearchServiceClient`, utilisez la nouvelle `SearchCredentials` propriété.

Dans les versions antérieures du Kit de développement, `SearchServiceClient` et `SearchIndexClient` avait constructeurs ayant un `HttpClient` paramètre. Ils ont été remplacés par constructeurs qui prennent un `HttpClientHandler` et un tableau de `DelegatingHandler` objets. Cela facilite l’installer gestionnaires personnalisés pour traiter les demandes HTTP si nécessaire.

Enfin, les constructeurs ayant un `Uri` et `SearchCredentials` ont été modifiés. Par exemple, si vous avez un code qui ressemble à ceci :

    var client =
        new SearchServiceClient(
            new SearchCredentials("abc123"),
            new Uri("http://myservice.search.windows.net"));

Vous pouvez le modifier à cette option pour corriger les erreurs de build :

    var client =
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123"));

Notez également que le type du paramètre d’informations d’identification a modifié en `ServiceClientCredentials`. Il s’agit probablement pas d’affecter votre code depuis `SearchCredentials` est dérivée de `ServiceClientCredentials`.

#### <a name="passing-a-request-id"></a>Passage d’un ID de demande

Dans les versions antérieures du Kit de développement, vous pouvez définir un ID de demande sur le `SearchServiceClient` ou `SearchIndexClient` et elle apparaît dans chaque demande à l’API REST. Ceci est utile pour résoudre les problèmes avec votre service de recherche si vous devez contacter le support technique. Toutefois, il n’est plus utile pour définir un ID de demande unique pour chaque opération plutôt que d’utiliser le même ID pour toutes les opérations. Pour cette raison, la `SetClientRequestId` méthodes de `SearchServiceClient` et `SearchIndexClient` ont été supprimés. À la place, vous pouvez passer un ID de demande à chaque méthode opération via l’option `SearchRequestOptions` paramètre.

> [AZURE.NOTE] Dans une version ultérieure du Kit de développement, nous allons ajouter un nouveau mécanisme pour définir globalement un ID de demande sur un client objets cohérente avec l’approche utilisée par d’autres SDK Azure.

#### <a name="example"></a>Exemple

Si vous avez un code qui ressemble à ceci :

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

Vous pouvez le modifier à cette option pour corriger les erreurs de build :

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

#### <a name="interface-name-changes"></a>Modification du nom d’interface

Les noms d’interface groupe opération ont changé pour être compatibles avec les noms de propriété correspondants :

 - Le type de `ISearchServiceClient.Indexes` a été renommée `IIndexOperations` à `IIndexesOperations`.
 - Le type de `ISearchServiceClient.Indexers` a été renommée `IIndexerOperations` à `IIndexersOperations`.
 - Le type de `ISearchServiceClient.DataSources` a été renommée `IDataSourceOperations` à `IDataSourcesOperations`.
 - Le type de `ISearchIndexClient.Documents` a été renommée `IDocumentOperations` à `IDocumentsOperations`.

Cette modification est probablement pas affecte votre code, sauf si vous avez créé des objets fictifs de ces interfaces à des fins de test.

<a name="BugFixesV1"></a>
### <a name="bug-fixes-in-version-11"></a>Correctifs dans la version 1.1

Un bogue est survenu dans les versions antérieures d’Azure recherche .NET SDK liée à la sérialisation des classes modèle personnalisé. Le bogue peut se produire si vous avez créé une classe du modèle personnalisé avec une propriété d’un type de valeur non nulle.

#### <a name="steps-to-reproduce"></a>Étapes de reproduction

Crée un modèle personnalisé avec une propriété de type de valeur non nulle. Par exemple, ajoutez un public `UnitCount` propriété de type `int` au lieu de `int?`.

Si vous indexez un document avec la valeur par défaut de ce type (par exemple, 0 pour `int`), le champ est null dans Azure rechercher. Si vous recherchez par la suite de ce document, la `Search` appel lève `JsonSerializationException` qu’il ne peut pas convertir réclamation `null` à `int`.

En outre, les filtres peuvent ne pas fonctionnent comme prévu depuis la rédaction à l’index au lieu de la valeur null.

#### <a name="fix-details"></a>Corriger les détails

Nous avons résolu ce problème dans la version 1.1 du Kit de développement. Désormais, si vous avez une classe modèle comme suit :

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

et si vous affectez `IntValue` à 0, cette valeur est maintenant correctement sérialisée 0 sur le réseau et stockée en tant que 0 dans l’index. Un aller-retour également fonctionne comme prévu.

Il existe un problème potentiel importants avec cette approche : Si vous utilisez un type de modèle avec une propriété non nullable, vous devez **garantir** qu’aucun documents dans votre index ne contiennent une valeur null pour le champ correspondant. Le Kit de développement ni l’API REST de recherche Azure vous aidera à appliquer cette.

Ce n’est pas simplement un problème hypothétique : imaginer un scénario où vous ajoutez un nouveau champ à un index existant qui est de type `Edm.Int32`. Après mise à jour la définition d’index, tous les documents aura une valeur null pour ce nouveau champ (dans la mesure où tous les types sont nullables dans Azure recherche). Si vous utilisez ensuite une classe du modèle avec non nullable `int` propriété de ce champ, vous obtiendrez un `JsonSerializationException` comme suit lorsque vous essayez d’extraire des documents :

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Pour cette raison, nous recommandons que vous utilisez types nullable dans votre cours modèle comme une meilleure pratique.

Pour plus d’informations sur ce bogue et le correctif, veuillez consulter [ce problème sur GitHub](https://github.com/Azure/azure-sdk-for-net/issues/1063).
