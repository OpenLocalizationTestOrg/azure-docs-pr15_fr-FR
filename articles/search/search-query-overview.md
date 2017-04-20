<properties
    pageTitle="Interroger votre Index de recherche Azure | Microsoft Azure | Service de recherche cloud hébergé"
    description="Créer une requête de recherche dans Azure rechercher et utiliser des paramètres de recherche pour filtrer et trier les résultats de recherche."
    services="search"
    manager="jhubbard"
    documentationCenter=""
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

# <a name="query-your-azure-search-index"></a>Interroger votre index de recherche Azure
> [AZURE.SELECTOR]
- [Vue d’ensemble](search-query-overview.md)
- [Portail](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [RESTE](search-query-rest-api.md)

Lorsque vous envoyez des demandes de recherche pour rechercher Azure, il existe un certain nombre de paramètres qui peuvent être spécifiées en parallèle avec les mots réels que vous tapez dans la zone de recherche de votre application. Ces paramètres de la requête vous permettent d’obtenir un contrôle plus approfondie de l’expérience de recherche en texte intégral.

Vous trouverez ci-dessous une liste qui explique brièvement utilisations courantes des paramètres de recherche Azure requête. Pour la prise en charge complète des paramètres de la requête et leur comportement, consultez les pages détaillées [API REST](https://msdn.microsoft.com/library/azure/dn798927.aspx) et [SDK.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.searchparameters_properties.aspx).

## <a name="types-of-queries"></a>Types de requêtes

Recherche Azure offre de nombreuses options pour créer des requêtes extrêmement puissants. Les deux principaux types de requête que vous utiliserez sont `search` et `filter`. A `search` requête recherche un ou plusieurs termes dans tous les champs de _recherche_ dans votre index et fonctionne de la manière que vous attendez un moteur de recherche, tels que Google ou Bing pour l’utiliser. A `filter` requête évalue une expression booléenne sur tous _filtrables_ champs dans un index. Contrairement aux `search` requêtes, `filter` requêtes correspondent au contenu exact d’un champ, ce qui signifie qu’ils respectent la casse de champs de type chaîne.

Vous pouvez utiliser des recherches et filtres ensemble ou séparément. Si vous les utilisez conjointement, le filtre est appliqué en premier à tout l’index et la recherche est effectuée dans les résultats du filtre. Filtres peuvent donc être très utile pour améliorer les performances des requêtes dans la mesure où ils réduisent l’ensemble de documents que la requête de recherche doit traiter.

La syntaxe des expressions de filtre est un sous-ensemble de la [langue de filtre OData](https://msdn.microsoft.com/library/azure/dn798921.aspx). Requêtes de recherche, vous pouvez utiliser la [syntaxe simplifiée](https://msdn.microsoft.com/library/azure/dn798920.aspx) ou la [syntaxe de la requête Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx) qui sont décrites ci-dessous.

### <a name="simple-query-syntax"></a>Syntaxe de la requête simple
La [syntaxe de la requête simple](https://msdn.microsoft.com/library/azure/dn798920.aspx) est le langage de requête par défaut utilisé dans Azure recherche. La syntaxe de la requête simple prend en charge un nombre d’opérateurs de recherche courantes, y compris AND, ou non, une phrase, suffixe et la priorité des opérateurs.

### <a name="lucene-query-syntax"></a>Syntaxe de requête Lucene
La [syntaxe de la requête Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx) vous permet d’utiliser le langage de requête largement adopté et expressifs développé dans le cadre de [Lucene Apache](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

L’utilisation de cette syntaxe de requête permet de vous permet d’atteindre facilement les fonctionnalités suivantes : [requêtes à portée de champ](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_fields), [recherche partielle](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_fuzzy), [proximité recherche](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_proximity), [augmentation du magasin de termes](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_termboost), [recherche d’expression régulière](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_regex), [caractères génériques](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_wildcard), [principes de base de syntaxe](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_syntax)et [requêtes à l’aide des opérateurs booléens](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_boolean).



## <a name="ordering-results"></a>Tri des résultats
Lorsque vous recevez des résultats d’une requête de recherche, vous pouvez demander que recherche Azure gère les résultats classés par valeurs dans un champ spécifique. Par défaut, recherche Azure commandes les résultats de recherche basées sur le rang de résultat de recherche de chaque document, qui est dérivée de [TF fil](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Si vous voulez Azure recherche pour renvoyer les résultats classés par une valeur autre que le résultat de la recherche, vous pouvez utiliser la `orderby` paramètre de recherche. Vous pouvez spécifier la valeur de la `orderby` paramètre pour inclure les noms de champs et les appels vers la [ `geo.distance()` fonction](https://msdn.microsoft.com/library/azure/dn798921.aspx) pour les valeurs géospatiales. Chaque expression peut être suivie `asc` pour indiquer que les résultats sont demandés dans l’ordre croissant, et `desc` pour indiquer que les résultats sont demandés dans l’ordre décroissant. Le classement par défaut par ordre croissant.

## <a name="paging"></a>La pagination
Recherche Azure facilite la mise en œuvre d’échange de résultats de recherche. À l’aide de la `top` et `skip` paramètres, vous pouvez facilement émettre des requêtes de recherche qui vous permettent de recevoir l’ensemble des résultats de recherche dans gérables sous-ensembles ordonnées qui permettent de facilement pratiques de l’interface utilisateur de recherche pertinents. Lorsque vous recevez ces sous-ensembles plus petits des résultats, vous pouvez également recevoir le nombre de documents dans l’ensemble de résultats de recherche.

Vous pouvez en savoir plus sur les résultats de recherche dans l’article de [la page résultats de recherche Azure de la recherche](search-pagination-page-layout.md)d’échange.


## <a name="hit-highlighting"></a>Appuyez sur la mise en surbrillance
Dans Rechercher Azure, mettre en évidence la portion exacte de résultats de recherche qui correspondent à la requête de recherche est très simple à l’aide de la `highlight`, `highlightPreTag`, et `highlightPostTag` paramètres. Vous pouvez spécifier les champs de _recherche_ doit ont leur texte correspondant souligné ainsi que spécifiant les balises de chaîne exacte à ajouter au début et de fin du texte correspondant que la recherche Azure renvoie.
