<properties
    pageTitle="Votre Index de recherche Azure à l’aide de l’API REST de requête | Microsoft Azure | Service de recherche cloud hébergé"
    description="Créer une requête de recherche dans Azure rechercher et utiliser des paramètres de recherche pour filtrer et trier les résultats de recherche."
    services="search"
    documentationCenter=""
    manager="jhubbard"
    authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="query-your-azure-search-index-using-the-rest-api"></a>Interroger votre index de recherche Azure à l’aide de l’API REST
> [AZURE.SELECTOR]
- [Vue d’ensemble](search-query-overview.md)
- [Portail](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [RESTE](search-query-rest-api.md)

Cet article vous explique comment interroger un index à l’aide de l' [API REST de recherche Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx).

Avant de commencer cette procédure pas à pas, vous disposez déjà [créé un index de recherche Azure](search-what-is-an-index.md) et [rempli avec les données](search-what-is-data-import.md).

## <a name="i-identify-your-azure-search-services-query-api-key"></a>J’ai. Identifier requête api-clé de votre service recherche Azure
Un composant clé de chaque opération de recherche par rapport à l’API REST de recherche Azure est la *clé d’api* qui a été généré pour le service que vous sa mise en service. Avoir une clé valide établit approbation, sur une base par demande, entre l’application qui envoie la demande et le service qui la gère.

1. Pour rechercher des clés d’api de votre service que vous devez vous connecter au [Portail Azure](https://portal.azure.com/)
2. Accédez à la carte de votre service de recherche d’Azure
3. Cliquez sur l’icône « Touches »

Votre service aura *clés d’administration* et *requête*.

 - Vos *clés de l’administrateur* principal et secondaire accorder tous les droits sur toutes les opérations, y compris la possibilité pour gérer le service, créer et supprimer des index, indexeurs et sources de données. Il existe deux clés de sorte que vous pouvez continuer à utiliser la clé secondaire si vous décidez d’actualiser la clé primaire et vice versa.
 - Vos *clés de requête* accorder l’accès en lecture seule aux index et aux documents et sont généralement distribués aux applications clientes émettent des requêtes de recherche.

Aux fins d’interrogation d’un index, vous pouvez utiliser un de vos clés de requête. Vos clés d’administration peuvent également être utilisées pour les requêtes, mais vous devez utiliser une clé de requête dans le code de votre application comme ceci mieux suit le [principe du moindre privilège](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

## <a name="ii-formulate-your-query"></a>II. Élaborer votre requête
Il existe deux façons [d’effectuer une recherche dans votre index à l’aide de l’API REST](https://msdn.microsoft.com/library/azure/dn798927.aspx). Un moyen consiste à émettre une demande HTTP POST où vos paramètres de requête doit être définies dans un objet JSON dans le corps de la requête. L’autre consiste à émettre une demande HTTP GET où vos paramètres de requête seront définis dans l’URL de requête. Notez que billet a plus [souple des limites](https://msdn.microsoft.com/library/azure/dn798927.aspx) de la taille des paramètres de la requête à obtenir. Pour cette raison, nous vous recommandons d’utiliser billet à moins d’avoir des circonstances particulières où obtenir serait plus pratique.

Pour valider et obtenir, vous devez fournir votre *nom de service*, *nom de l’index*et la bonne *version de l’API* (la version actuelle de l’API est `2015-02-28` au moment de la publication de ce document) dans l’URL de requête. Pour obtenir, la *chaîne de requête* à la fin de l’URL est l’endroit où vous fournissez les paramètres de requête. Voir ci-dessous pour le format de l’URL :

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2015-02-28

Le format de billet est le même, mais uniquement avec l’api-version dans les paramètres de chaîne de requête.



#### <a name="example-queries"></a>Exemples de requêtes

Voici quelques exemples de requêtes dans un index nommé « hôtel ». Ces requêtes sont représentées au format GET et POST.

La totalité de l’index de recherche du terme « budget » et retourner uniquement la `hotelName` champ :

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=budget&$select=hotelName&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "budget",
    "select": "hotelName"
}
```

Appliquer un filtre à l’index de trouver hôtel moins cher que 150 $ par nuit, et renvoyer la `hotelId` et `description`:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

Effectuer une recherche dans la totalité de l’index, ordre par un champ spécifique (`lastRenovationDate`) dans l’ordre décroissant, les deux premiers des résultats, de n’afficher que `hotelName` et `lastRenovationDate`:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$top=2&$orderby=lastRenovationDate desc&$select=hotelName,lastRenovationDate&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "*",
    "orderby": "lastRenovationDate desc",
    "select": "hotelName,lastRenovationDate",
    "top": 2
}
```

## <a name="iii-submit-your-http-request"></a>III. Envoyez votre demande HTTP
À présent que vous avez préparé votre requête dans le cadre de votre URL de la demande HTTP (pour obtenir) ou le corps (pour POST), vous pouvez définir vos en-têtes de demande et envoyer votre requête.

#### <a name="request-and-request-headers"></a>Demande et les en-têtes de requête
Vous devez définir deux en-têtes de requête pour obtenir ou trois pour billet :
1. La `api-key` en-tête doit être définie à la clé de requête trouvé à l’étape je ci-dessus. Notez que vous pouvez également utiliser une clé d’administration en tant que la `api-key` en-tête, mais il est recommandé d’utiliser une clé de requête car elle accorde l’accès en lecture seule aux index et aux documents en mode exclusif.
2. La `Accept` en-tête doit être définie sur `application/json`.
3. Pour valider uniquement, la `Content-Type` en-tête doit également être définie sur `application/json`.

Voir ci-dessous pour une demande HTTP GET pour effectuer une recherche dans l’index « hôtel » à l’aide de l’API REST de recherche Azure, à l’aide d’une requête simple qui recherche du terme « motel » :

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=motel&api-version=2015-02-28
Accept: application/json
api-key: [query key]
```

Voici la même requête d’exemple, cette fois à l’aide de HTTP POST :

```
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
Content-Type: application/json
Accept: application/json
api-key: [query key]

{
    "search": "motel"
}
```

Une requête réussie provoquera un Code d’état de `200 OK` et les résultats de recherche sont retournés en tant que JSON dans le corps de réponse. Voici le résultat de l’apparence de requête ci-dessus, en supposant que l’index « hôtel » est remplie avec les données d’exemple dans [L’importation des données dans recherche Azure à l’aide de l’API REST](search-import-data-rest-api.md) (Notez que le JSON a été mis en forme pour la clarté).

```JSON
{
    "value": [
        {
            "@search.score": 0.59600675,
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags":["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": {
                "type": "Point",
                "coordinates": [-122.131577, 49.678581],
                "crs": {
                    "type":"name",
                    "properties": {
                        "name": "EPSG:4326"
                    }
                }
            }
        }
    ]
}
```

Pour plus d’informations, consultez la section « Réponse » de [Rechercher des Documents](https://msdn.microsoft.com/library/azure/dn798927.aspx). Pour plus d’informations sur d’autres codes d’état HTTP qui peut être retourné en cas d’échec, voir [codes d’état HTTP (Azure recherche)](https://msdn.microsoft.com/library/azure/dn798925.aspx).
