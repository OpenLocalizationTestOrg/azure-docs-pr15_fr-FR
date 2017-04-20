<properties
pageTitle="L’indexation de stockage de Table Azure avec la recherche Azure"
description="Apprenez à indexer des données stockées dans des Tables avec la recherche Azure Azure"
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
ms.date="08/16/2016"
ms.author="eugenesh" />

# <a name="indexing-azure-table-storage-with-azure-search"></a>L’indexation de stockage de Table Azure avec la recherche Azure

Cet article vous explique comment utiliser la recherche Azure d’indexer des données stockées dans le stockage de Table Azure. Le nouvel indexeur de table Azure recherche rend ce processus rapide et transparente. 

> [AZURE.IMPORTANT] Cette fonctionnalité n’est actuellement dans l’aperçu. Il est disponible uniquement dans l’API REST à l’aide de la version **2015-02-28-aperçu** et dans la version 2.0-aperçu du Kit de développement .NET. Veuillez n’oubliez pas, afficher un aperçu des API destiné de test et d’évaluation et ne doit pas être utilisé dans les environnements de production.

## <a name="setting-up-azure-table-indexing"></a>La configuration de l’indexation de table Azure

Pour installer et configurer un indexeur de table Azure, vous pouvez utiliser l’API REST de recherche Azure pour créer et gérer des **sources de données** et **indexeurs** comme décrit dans [les opérations d’indexeur](https://msdn.microsoft.com/library/azure/dn946891.aspx). Vous pouvez également utiliser la [version 2.0-aperçu](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx) du Kit de développement .NET. À l’avenir, la prise en charge de l’indexation de table est ajouté au portail Azure.

Une source de données spécifie les données à indexer, les informations d’identification nécessaires pour accéder aux données, ainsi que les stratégies qui permettent d’Azure recherche identifier efficacement les modifications des données (nouveaux, modifiés ou supprimés lignes).

Un indexeur lit les données à partir d’une source de données et charge dans un index de recherche cible.

Pour configurer l’indexation de table :

1. Créer une source de données
    - Définir la `type` paramètre`azuretable`
    - Passez la chaîne de connexion de compte de stockage en tant que la `credentials.connectionString` paramètre
    - Spécifiez le nom de table à l’aide de la `container.name` paramètre
    - Vous pouvez également spécifier une requête à l’aide du `container.query` paramètre. Si possible, utilisez un filtre sur PartitionKey pour de meilleures performances ; toute autre requête provoquera une analyse de table complet, ce qui peut entraîner une baisse des performances pour les tables volumineuses.
2. Créer un index de recherche avec le schéma correspondant aux colonnes du tableau que vous souhaitez indexer. 
3. Créer l’indexeur en vous connectant à votre source de données à l’index de recherche.

### <a name="create-data-source"></a>Créer la source de données

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<my storage connection string>" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

Pour plus d’informations sur l’API créer de source de données, voir [Créer la source de données](search-api-indexers-2015-02-28-preview.md#create-data-source).

### <a name="create-index"></a>Créer un index 

    POST https://[service name].search.windows.net/indexes?api-version=2015-02-28
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-target-index",
        "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
        ]
    }

Pour plus d’informations sur l’API créer d’Index, voir [Créer un Index](https://msdn.microsoft.com/library/dn798941.aspx)

### <a name="create-indexer"></a>Créer indexeur 

Enfin, créez l’indexeur qui fait référence à la source de données et l’index cible. Par exemple :

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Pour plus d’informations sur l’API indexeur créer, consultez [Créer un indexeur](search-api-indexers-2015-02-28-preview.md#create-indexer).

C’est tout cela - indexation Joyeux !

## <a name="dealing-with-different-field-names"></a>En matière de gestion des noms de champs différents

Souvent, les noms de champs dans votre index existant sera différents de noms de propriété dans votre tableau. Vous pouvez utiliser les **mappages de champs** pour mapper les noms des propriétés de la table pour les noms de champs dans votre index de recherche. Pour en savoir plus sur les mappages de champs, voir [mappages de champs de recherche Azure indexeur par-delà les différences entre les sources de données et les index de recherche](search-indexer-field-mappings.md).

## <a name="handling-document-keys"></a>Touches de gestion de documents

Dans Rechercher Azure, la clé de document identifie de façon unique un document. Chaque index de recherche peut comporter qu’un champ de clé de type `Edm.String`. Le champ de clé est requis pour chaque document est ajouté à l’index (en réalité, il est le seul champ obligatoire).

Étant donné que les lignes de tableaux ont une clé composée, recherche Azure génère un champ synthétique appelé `Key` qui est une concaténation de valeurs clés partition clé et ligne. Par exemple, si une ligne de PartitionKey est `PK1` et RowKey est `RK1`, puis `Key` valeur du champ sera `PK1RK1`. 

> [AZURE.NOTE] La `Key` valeur peut contenir des caractères qui ne sont pas valides dans les clés de document, tels que des tirets. Vous pouvez traiter les caractères non valides à l’aide de la `base64Encode` [fonction de mappage de champ](search-indexer-field-mappings.md#base64EncodeFunction). Si vous procédez ainsi, n’oubliez pas également utiliser en base 64 adaptée aux URL codage lors de la transmission des touches de documents dans l’API appelle tels que de la recherche.

## <a name="incremental-indexing-and-deletion-detection"></a>Détection de l’indexation et la suppression incrémentielle
 
Lorsque vous configurez un indexeur de table pour s’exécuter sur une planification, il répertorie à nouveau les lignes nouvelles ou mises à jour uniquement, telle que déterminée par une ligne `Timestamp` valeur. Vous n’êtes pas obligé de spécifier une stratégie de détection de changements – incrémentielles l’indexation est activée automatiquement pour vous. 

Pour indiquer que certains documents doivent être supprimés de l’index, vous pouvez utiliser une stratégie de suppression temporaire – au lieu de suppression d’une ligne, ajoutez une propriété pour indiquer qu’il est supprimé et définir une stratégie de détection de suppression de bordures dans la source de données. Par exemple, la stratégie ci-dessous va considérer qu’une ligne est supprimée si elle a une propriété `IsDeleted` avec la valeur `"true"`: 

    PUT https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]
    
    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "query" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   


## <a name="help-us-make-azure-search-better"></a>Aidez-nous à améliorer la recherche Azure

Si vous avez des demandes de fonctionnalités ou idées d’amélioration, veuillez contacter nous sur notre [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).