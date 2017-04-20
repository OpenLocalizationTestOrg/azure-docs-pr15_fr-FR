<properties
pageTitle="L’indexation des objets BLOB JSON avec indexeur d’objets blob Azure recherche"
description="L’indexation des objets BLOB JSON avec indexeur d’objets blob Azure recherche"
services="search"
documentationCenter=""
authors="chaosrealm"
manager="pablocas"
editor="" />

<tags
ms.service="search"
ms.devlang="rest-api"
ms.workload="search" ms.topic="article"  
ms.tgt_pltfrm="na"
ms.date="07/26/2016"
ms.author="eugenesh" />

# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>L’indexation des objets BLOB JSON avec indexeur d’objets blob Azure recherche 

Cet article vous explique comment configurer indexeur d’objets blob Azure recherche pour extraire un contenu structuré d’objets BLOB contenant JSON.

## <a name="scenarios"></a>Scénarios

Par défaut, [indexeur d’objets blob Azure recherche](search-howto-indexing-azure-blob-storage.md) analyse des objets BLOB JSON comme un segment unique de texte. Souvent, vous souhaitez conserver la structure de vos documents JSON. Par exemple, avec le document JSON 

    { 
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13" 
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Vous souhaiterez peut-être analyser dans un document Azure recherche avec champs « balises », « datePublished » et « texte ».

Par ailleurs, lorsque vos objets BLOB contient un **tableau d’objets JSON**, vous souhaiterez peut-être chaque élément du tableau pour devenir un document recherche Azure distinct. Par exemple, étant donné un blob avec cette JSON :  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

Vous pouvez remplir votre index de recherche Azure avec 3 documents distincts, chacune avec les champs « id » et « texte ». 

> [AZURE.IMPORTANT] Cette fonctionnalité est actuellement dans l’aperçu. Il est disponible uniquement dans l’API REST à l’aide de la version **2015-02-28-aperçu**. Veuillez n’oubliez pas, afficher un aperçu des API destiné de test et d’évaluation et ne doit pas être utilisé dans les environnements de production. 

## <a name="setting-up-json-indexing"></a>La configuration de l’indexation JSON

Indexer des objets BLOB JSON, définissez la `parsingMode` paramètre de configuration `json` (à indexer chaque blob comme un document unique) ou `jsonArray` (si votre BLOB contiennent un tableau JSON) : 

    {
      "name" : "my-json-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "json" | "jsonArray" } }
    }

Si nécessaire, utiliser les **mappages de champs** pour sélectionner les propriétés du document JSON source utilisée pour renseigner votre index de recherche cible.  Ceci est décrit dans le détail ci-dessous. 

> [AZURE.IMPORTANT] Lorsque vous utilisez `json` ou `jsonArray` mode d’analyse, recherche Azure suppose que tous les objets BLOB dans votre source de données sera JSON. Si vous avez besoin prendre en charge un mélange d’objets BLOB JSON et non JSON dans la même source de données, n’hésitez pas sur le [site UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="using-field-mappings-to-build-search-documents"></a>Utilisation de mappages de champs pour créer des rechercher des documents 

Pour l’instant, Azure recherche ne peut pas indexer arbitraires documents JSON directement, car il prend en charge les types de données primitifs uniquement, les tableaux de chaînes et les points de GeoJSON. Toutefois, vous pouvez utiliser les **mappages de champs** sélectionner des parties de votre document JSON, « relever » dans les champs de niveau supérieur de la recherche dans le document. Pour connaître les notions fondamentales sur les mappages de champ, voir [mappages de champs de recherche Azure indexeur par-delà les différences entre les sources de données et les index de recherche](search-indexer-field-mappings.md).

Revenir à notre exemple de document JSON : 

    { 
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13" 
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Imaginons que vous ayez un index de recherche avec les champs suivants : `text` de type Edm.String, `date` de type Edm.DateTimeOffset, et `tags` de type Collection(Edm.String). Pour mapper votre JSON dans la forme souhaitée, utilisez les mappages de champs suivants : 

    "fieldMappings" : [ 
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
    ]

Les noms des champs source dans les mappages sont spécifiées en utilisant la notation de [Pointeur JSON](http://tools.ietf.org/html/rfc6901) . Vous commencez avec une barre oblique pour faire référence à la racine de votre document JSON, puis Explorez la propriété souhaitée (au niveau arbitraire d’imbrication) à l’aide de chemin d’accès séparées par une barre oblique transférer. 

Vous pouvez également faire référence aux éléments du tableau individuels à l’aide d’un index de base zéro. Par exemple, pour sélectionner le premier élément de la matrice « balises » de l’exemple ci-dessus, vous devez utiliser un mappage des champs comme suit :

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [AZURE.NOTE] Si un nom de champ source dans un chemin d’accès du mappage de champ fait référence à une propriété qui n’existe pas dans JSON, ce mappage est ignoré sans une erreur. Cela est fait afin que nous pouvons prendre en charge des documents avec un schéma différent (ce qui correspond à un cas d’utilisation courants). Étant donné qu’aucune validation, vous devez veiller à ne pas de fautes de frappe dans votre spécification de mappage de champ. 

Si vos documents JSON contient uniquement des propriétés de niveau supérieur simples, pas nécessaires mappages de champs du tout. Par exemple, si votre JSON ressemble à ceci, les propriétés de niveau supérieur « texte », « datePublished » et « balises » seront associées directement dans les champs correspondants dans l’index de recherche : 
 
    { 
       "text" : "A hopefully useful article explaining how to parse JSON blobs",
       "datePublished" : "2016-04-13" 
       "tags" : [ "search", "storage", "howto" ]    
    }

## <a name="indexing-nested-json-arrays"></a>Indexation des tableaux JSON imbriquées

Que se passe-t-il si vous souhaitez indexer un tableau d’objets JSON, mais ce tableau est imbriqué n’importe où dans le document ? Vous pouvez choisir la propriété qui contient le tableau à l’aide du `documentRoot` propriété de configuration. Par exemple, si votre BLOB ressemble à ceci : 

    { 
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" }, 
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    } 

Utilisez cette configuration pour indexer le tableau contenu dans la propriété de niveau « 2 » : 

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }


## <a name="request-examples"></a>Exemples de requête

Cela regroupant l’ensemble, voici les exemples de charges complète. 

Source de données : 

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<my storage connection string>" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

Indexeur :

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "useJsonParser" : true } }, 
      "fieldMappings" : [ 
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]
    }

## <a name="help-us-make-azure-search-better"></a>Aidez-nous à améliorer la recherche Azure

Si vous avez des demandes de fonctionnalités ou idées d’amélioration, veuillez contacter nous sur notre [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).