<properties
pageTitle="L’indexation des objets BLOB CSV avec indexeur d’objets blob Azure recherche | Microsoft Azure"
description="Apprenez à indexer des objets BLOB CSV avec la recherche Azure"
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
ms.date="07/12/2016"
ms.author="eugenesh" />

# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>L’indexation des objets BLOB CSV avec indexeur d’objets blob Azure recherche 

Par défaut, [indexeur d’objets blob Azure recherche](search-howto-indexing-azure-blob-storage.md) analyse délimitée par des objets BLOB texte comme un segment unique de texte. Toutefois, avec des objets BLOB contenant des données CSV, fréquence souhaitée traiter chaque ligne dans le blob comme un document distinct. Par exemple, avec le texte délimité par des tabulations suivant : 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

Vous souhaiterez peut-être analyser en 2 documents, chacun contenant des champs « balises », « datePublished » et « id ».

Dans cet article, vous allez apprendre à analyser des objets BLOB CSV avec un indexeur blob Azure recherche. 

> [AZURE.IMPORTANT] Cette fonctionnalité est actuellement dans l’aperçu. Il est disponible uniquement dans l’API REST à l’aide de la version **2015-02-28-aperçu**. Veuillez n’oubliez pas, afficher un aperçu des API destiné de test et d’évaluation et ne doit pas être utilisé dans les environnements de production. 

## <a name="setting-up-csv-indexing"></a>La configuration de l’indexation CSV

Indexer des objets BLOB CSV, créer ou mettre à jour la définition d’indexeur avec la `delimitedText` mode d’analyse :  

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

Pour plus d’informations sur l’API indexeur créer, consultez [Créer un indexeur](search-api-indexers-2015-02-28-preview.md#create-indexer).

`firstLineContainsHeaders`Indique que la première ligne (non vides) de chaque blob contienne des en-têtes.
Si des objets BLOB ne contiennent pas une ligne d’en-tête initial, les en-têtes doivent être indiquées dans la configuration indexeur : 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Pour l’instant, l’uniquement le codage UTF-8 est pris en charge. En outre, uniquement la virgule `','` caractère est pris en charge comme séparateur. Si vous avez besoin de prise en charge pour les autres codages ou les séparateurs, n’hésitez pas sur le [site UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [AZURE.IMPORTANT] Lorsque vous utilisez le mode d’analyse de texte délimité, recherche Azure suppose que tous les objets BLOB dans votre source de données sera CSV. Si vous avez besoin prendre en charge un mélange d’objets BLOB CSV et non-CSV dans la même source de données, n’hésitez pas sur le [site UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="request-examples"></a>Exemples de requête

Cela regroupant l’ensemble, voici les exemples de charge utile complète. 

Source de données : 

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<my storage connection string>" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Indexeur :

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-search-better"></a>Aidez-nous à améliorer la recherche Azure

Si vous avez des demandes de fonctionnalités ou idées d’amélioration, veuillez contacter nous sur notre [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).