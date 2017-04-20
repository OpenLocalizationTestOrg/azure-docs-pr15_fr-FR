<properties
    pageTitle="Téléchargement des données dans recherche Azure à l’aide de l’API REST | Microsoft Azure | Service de recherche cloud hébergé"
    description="Découvrez comment télécharger des données dans un index de recherche Azure à l’aide de l’API REST."
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager="jhubbard"
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="upload-data-to-azure-search-using-the-rest-api"></a>Télécharger des données sur recherche Azure à l’aide de l’API REST
> [AZURE.SELECTOR]
- [Vue d’ensemble](search-what-is-data-import.md)
- [.NET](search-import-data-dotnet.md)
- [RESTE](search-import-data-rest-api.md)

Cet article vous explique comment utiliser l' [API REST de recherche Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx) pour importer des données dans un index de recherche Azure.

Avant de commencer cette procédure pas à pas, vous devez déjà avoir [créé un index de recherche Azure](search-what-is-an-index.md).

Pour distribuer des documents dans votre index à l’aide de l’API REST, vous délivrerez une demande HTTP POST au point de terminaison de votre index URL. Le corps du corps de la demande HTTP est un objet JSON qui contient les documents pour être ajoutées, modifiées ou supprimées.

## <a name="i-identify-your-azure-search-services-admin-api-key"></a>J’ai. Identifier la clé api de l’administrateur de votre service de recherche Azure
Lorsque vous émettez des requêtes HTTP contre votre service à l’aide de l’API REST, *chaque* API demande doit comporter clé d’api qui a été générée pour le service de recherche que vous sa mise en service. Avoir une clé valide établit approbation, sur une base par demande, entre l’application qui envoie la demande et le service qui la gère.

1. Pour rechercher des clés d’api de votre service que vous devez vous connecter au [Portail Azure](https://portal.azure.com/)
2. Accédez à la carte de votre service de recherche d’Azure
3. Cliquez sur l’icône « Touches »

Votre service aura *clés d’administration* et *requête*.

  - Vos *clés de l’administrateur* principal et secondaire accorder tous les droits sur toutes les opérations, y compris la possibilité pour gérer le service, créer et supprimer des index, indexeurs et sources de données. Il existe deux clés de sorte que vous pouvez continuer à utiliser la clé secondaire si vous décidez d’actualiser la clé primaire et vice versa.
  - Vos *clés de requête* accorder l’accès en lecture seule aux index et aux documents et sont généralement distribués aux applications clientes émettent des requêtes de recherche.

Aux fins d’importation de données dans un index, vous pouvez utiliser votre clé administrateur principal ou secondaire.

## <a name="ii-decide-which-indexing-action-to-use"></a>II. Décidez de l’action d’indexation à utiliser
Lorsque vous utilisez l’API REST, vous délivrerez requêtes HTTP POST avec corps de requête JSON à URL du point de terminaison de votre index de recherche Azure. L’objet JSON dans le corps de votre demande HTTP contiendra un seul tableau JSON nommé « valeur » qui contient des objets JSON représentant les documents que vous voulez ajouter à votre index, mise à jour ou supprimer.

Chaque objet JSON dans le tableau « valeur » représente un document à indexer. Chacun de ces objets contient clé de document et spécifie l’action d’indexation souhaitée (téléchargement, fusion, suppression, etc.). En fonction de laquelle la sous actions que vous choisissez, uniquement certains champs doivent être inclus dans chaque document :

@search.action | Description | Champs nécessaires pour chaque document | Notes
--- | --- | --- | ---
`upload` | Un `upload` action est similaire à un « upsert » dans laquelle le document sera inséré s’il est nouveau et mis à jour/remplacé s’il existe. | clé, ainsi que tous les autres champs que vous souhaitez définir | Lors de la mise à jour/remplacement d’un document existant, n’importe quel champ qui n’est pas spécifié dans la demande aura son champ de valeur `null`. Cela se produit même lorsque le champ a été défini précédemment à une valeur non nulle.
`merge` | Met à jour un document existant avec les champs spécifiés. Si le document n’existe pas dans l’index, la fusion échoue. | clé, ainsi que tous les autres champs que vous souhaitez définir | N’importe quel champ que vous spécifiez dans une fusion remplacera le champ existant dans le document. Cela inclut les champs de type `Collection(Edm.String)`. Par exemple, si le document contient un champ `tags` avec valeur `["budget"]` et que vous exécutez une fusion avec valeur `["economy", "pool"]` pour `tags`, la dernière valeur de la `tags` champ sera `["economy", "pool"]`. Il ne sera plus `["budget", "economy", "pool"]`.
`mergeOrUpload` | Cette action se comporte comme `merge` s’il existe déjà un document avec la touche donnée dans l’index. Si le document n’existe pas, il se comporte comme `upload` avec un nouveau document. | clé, ainsi que tous les autres champs que vous souhaitez définir | -
`delete` | Supprime le document spécifié de l’index. | clé uniquement | Les champs que vous spécifiez autre que le champ de clé sont ignoré. Si vous voulez supprimer un champ individuel à partir d’un document, utilisez `merge` à la place et simplement la valeur du champ explicitement null.

## <a name="iii-construct-your-http-request-and-request-body"></a>III. Créer votre requête HTTP et corps de requête
À présent que vous avez collectées les valeurs de champ souhaité pour vos actions index, vous êtes prêt à créer la demande HTTP réelle et le corps de la requête JSON pour importer vos données.

#### <a name="request-and-request-headers"></a>Demande et les en-têtes de requête
Dans l’URL, vous devrez fournir votre nom de service, nom de l’index (« hébergement » dans le cas présent), ainsi que la version de l’API NOMPROPRE (la version actuelle de l’API est `2015-02-28` au moment de la publication de ce document). Vous devez définir la `Content-Type` et `api-key` en-têtes de requête. Pour ce dernier, utilisez une des clés d’administrateur de votre service.

    POST https://[search service].search.windows.net/indexes/hotels/docs/index?api-version=2015-02-28
    Content-Type: application/json
    api-key: [admin key]

#### <a name="request-body"></a>Corps de la requête

```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "hotelId": "1",
            "baseRate": 199.0,
            "description": "Best hotel in town",
            "description_fr": "Meilleur hôtel en ville",
            "hotelName": "Fancy Stay",
            "category": "Luxury",
            "tags": ["pool", "view", "wifi", "concierge"],
            "parkingIncluded": false,
            "smokingAllowed": false,
            "lastRenovationDate": "2010-06-27T00:00:00Z",
            "rating": 5,
            "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
            "@search.action": "upload",
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags": ["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "3",
            "baseRate": 129.99,
            "description": "Close to town hall and the river"
        },
        {
            "@search.action": "delete",
            "hotelId": "6"
        }
    ]
}
```

Dans ce cas, nous utilisons `upload`, `mergeOrUpload`, et `delete` comme nos actions de recherche.

Part du principe que cet index « hôtel » exemple est déjà rempli avec un nombre de documents. Notez la manière dont nous n’avez pas à spécifier tous les champs de document possibles lorsque vous utilisez `mergeOrUpload` et comment nous spécifié uniquement la clé de document (`hotelId`) lorsque vous utilisez `delete`.

Notez également que vous ne pouvez inclure jusqu'à 1000 documents (ou 16 Mo) dans une seule demande d’indexation.

## <a name="iv-understand-your-http-response-code"></a>IV. Comprendre votre code de réponse HTTP
#### <a name="200"></a>200
Après l’envoi d’une demande d’indexation réussie, vous recevrez une réponse HTTP avec code d’état de `200 OK`. Le corps JSON de la réponse HTTP sera comme suit :

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": true,
            "errorMessage": null
        },
        ...
    ]
}
```

#### <a name="207"></a>207
Code d’état `207` sera retournée lorsqu’au moins un élément n’a pas été indexé. Le corps JSON de la réponse HTTP contient des informations relatives aux documents échouées.

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": false,
            "errorMessage": "The search service is too busy to process this document. Please try again later."
        },
        ...
    ]
}
```

> [AZURE.NOTE] Cela signifie généralement que la charge sur votre service de recherche est atteindre un point où l’indexation demandes commencera pour renvoyer `503` les réponses. Dans ce cas, nous hautement est recommandé que votre code client interruption et nous attendre avant le renouvellement. Cela donne le système peu de temps pour résoudre le problème moindres réussite de requêtes futures. Rapidement une nouvelle tentative de vos demandes sera uniquement prolonger la situation.

#### <a name="429"></a>429
Code d’état `429` sera retournée lorsque vous avez dépassé votre quota du nombre de documents par index.

#### <a name="503"></a>503
Code d’état `503` sera retournée si aucun des éléments dans la demande a été correctement indexé. Cette erreur signifie que le système est surchargé et que votre demande ne peut pas être traitée pour le moment.

> [AZURE.NOTE] Dans ce cas, nous hautement est recommandé que votre code client interruption et nous attendre avant le renouvellement. Cela donne le système peu de temps pour résoudre le problème moindres réussite de requêtes futures. Rapidement une nouvelle tentative de vos demandes sera uniquement prolonger la situation.

Pour plus d’informations sur les actions de document et les réponses de réussite / d’erreur, voir [Ajouter, mettre à jour ou supprimer des Documents](https://msdn.microsoft.com/library/azure/dn798930.aspx). Pour plus d’informations sur d’autres codes d’état HTTP qui peut être retourné en cas d’échec, voir [codes d’état HTTP (Azure recherche)](https://msdn.microsoft.com/library/azure/dn798925.aspx).

## <a name="next"></a>Suivant
Après avoir rempli votre index de recherche Azure, vous serez prêt à commencer à les distribuer des requêtes pour rechercher des documents. Pour plus d’informations, voir [Index de recherche Azure votre requête](search-query-overview.md) .
