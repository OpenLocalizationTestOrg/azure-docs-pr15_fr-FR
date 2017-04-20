<properties
    pageTitle="Créer un index de recherche Azure à l’aide de l’API REST | Microsoft Azure | Service de recherche cloud hébergé"
    description="Créer un index dans le code à l’aide de l’API REST de Azure recherche HTTP."
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager="jhubbard"
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="create-an-azure-search-index-using-the-rest-api"></a>Créer un index de recherche Azure à l’aide de l’API REST
> [AZURE.SELECTOR]
- [Vue d’ensemble](search-what-is-an-index.md)
- [Portail](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [RESTE](search-create-index-rest-api.md)


Cet article vous guidera tout au long du processus de création d’une recherche Azure [index](https://msdn.microsoft.com/library/azure/dn798941.aspx) à l’aide de l’API REST de recherche Azure.

Avant de suivre ce guide et création d’un index, dont vous avez déjà [créé un service de recherche Azure](search-create-service-portal.md).

Pour créer un index de recherche Azure à l’aide de l’API REST, vous envoie une demande HTTP POST unique au point de terminaison de votre service de recherche Azure URL. La définition de votre index figure dans le corps de la requête en tant que contenu JSON correct.


## <a name="i-identify-your-azure-search-services-admin-api-key"></a>J’ai. Identifier la clé api de l’administrateur de votre service de recherche Azure
À présent que vous avez configuré un service de recherche Azure, vous pouvez émettre des requêtes HTTP contre point de terminaison URL de votre service à l’aide de l’API REST. Toutefois, *toutes les* API demandes doivent inclure la clé api qui a été générée pour le service de recherche que vous sa mise en service. Avoir une clé valide établit approbation, sur une base par demande, entre l’application qui envoie la demande et le service qui la gère.

1. Pour rechercher des clés d’api de votre service que vous devez vous connecter au [Portail Azure](https://portal.azure.com/)
2. Accédez à la carte de votre service de recherche d’Azure
3. Cliquez sur l’icône « Touches »

Votre service aura *clés d’administration* et *requête*.

 - Vos *clés de l’administrateur* principal et secondaire accorder tous les droits sur toutes les opérations, y compris la possibilité pour gérer le service, créer et supprimer des index, indexeurs et sources de données. Il existe deux clés de sorte que vous pouvez continuer à utiliser la clé secondaire si vous décidez d’actualiser la clé primaire et vice versa.
 - Vos *clés de requête* accorder l’accès en lecture seule aux index et aux documents et sont généralement distribués aux applications clientes émettent des requêtes de recherche.

Aux fins de création d’un index, vous pouvez utiliser votre clé administrateur principal ou secondaire.

## <a name="ii-define-your-azure-search-index-using-well-formed-json"></a>II. Définir votre index de recherche Azure à l’aide de JSON correct
Une seule demande HTTP POST à votre service créera votre index. Le corps de votre demande HTTP POST contiendra un seul objet JSON qui définit votre index de recherche Azure.

1. La première propriété de cet objet JSON est le nom de votre index.
2. La deuxième propriété de cet objet JSON est un tableau JSON nommé `fields` qui contient un objet JSON distinct pour chaque champ dans l’index. Chacun de ces objets JSON contiennent plusieurs paires nom/valeur de chacun des attributs d’un champ, y compris « nom », « type », etc..

Il est important de maintenir votre recherche utilisateur expérience et des besoins à l’esprit lorsque vous créez votre index comme les [attributs corrects](https://msdn.microsoft.com/library/azure/dn798941.aspx)doit être affectée à chaque champ. Ces contrôler attributs les fonctions (filtrage, faceting, le tri de la recherche en texte intégral, etc.) de recherche s’appliquent à quels champs. Pour n’importe quel attribut que vous ne spécifiez pas, la valeur par défaut sera pour activer la fonctionnalité de recherche correspondant à moins que vous la désactiviez spécifiquement.

Dans notre exemple, nous avons nommé notre index « hôtel » et défini nos champs comme suit :

```JSON
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
```

Nous avons choisi avec soin les attributs d’index pour chaque champ basée sur la manière dont nous ils seront utilisés dans une application. Par exemple, `hotelId` est une clé unique que vous recherchez ne sauront pas susceptibles d’hôtel, afin que nous désactivons recherche en texte intégral de ce champ en définissant les utilisateurs `searchable` à `false`, ce qui économise de l’espace dans l’index.

Veuillez noter qu’exactement un champ dans votre index de type `Edm.String` doit être la désignés en tant que le champ « clé ».

La définition d’index ci-dessus utilise un analyseur de langage personnalisé pour le `description_fr` champ parce qu’il est prévu pour stocker du texte Français. Consultez [la langue prise en charge de la rubrique sur MSDN](https://msdn.microsoft.com/library/azure/dn879793.aspx) ainsi que le correspondant [le billet de blog](https://azure.microsoft.com/blog/language-support-in-azure-search/) pour plus d’informations sur analyseurs de langue.

## <a name="iii-issue-the-http-request"></a>III. Publier la requête HTTP
1. À l’aide de la définition de votre index en tant que corps de la demande, de délivrer une demande HTTP POST à votre URL du point de terminaison du service de recherche Azure. Dans l’URL, veillez à utiliser votre nom de service comme le nom d’hôte et placer le NOMPROPRE `api-version` comme paramètre de chaîne de requête (la version actuelle de l’API est `2015-02-28` au moment de la publication de ce document).
2. Dans les en-têtes de demande, spécifiez la `Content-Type` en tant que `application/json`. Vous devrez également fournir la clé d’administration de votre service que vous avez identifiés à étape I dans le `api-key` en-tête.


Vous devrez fournir votre propre clé de nom et api de service pour publier la requête ci-dessous :


    POST https://[service name].search.windows.net/indexes?api-version=2015-02-28
    Content-Type: application/json
    api-key: [api-key]


Pour une demande réussie, vous devriez voir le code d’état 201 (créé). Pour plus d’informations sur la création d’un index via l’API REST, veuillez consulter la référence des API sur [MSDN](https://msdn.microsoft.com/library/azure/dn798941.aspx). Pour plus d’informations sur d’autres codes d’état HTTP qui peut être retourné en cas d’échec, voir [codes d’état HTTP (Azure recherche)](https://msdn.microsoft.com/library/azure/dn798925.aspx).

Lorsque vous avez terminé avec un index et que vous voulez supprimer, génère une demande HTTP DELETE. Par exemple, il s’agit comment nous serait supprimer l’index « hôtel » :

    DELETE https://[service name].search.windows.net/indexes/hotels?api-version=2015-02-28
    api-key: [api-key]


## <a name="next"></a>Suivant
Après avoir créé un index de recherche Azure, vous serez prêt à [télécharger votre contenu dans l’index](search-what-is-data-import.md) afin que vous pouvez démarrer la recherche de vos données.
