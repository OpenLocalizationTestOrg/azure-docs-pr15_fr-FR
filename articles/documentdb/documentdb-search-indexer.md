<properties
    pageTitle="Connexion DocumentDB avec recherche Azure à l’aide d’indexeurs | Microsoft Azure"
    description="Cet article vous explique comment utiliser pour indexeur Azure recherche avec DocumentDB comme source de données."
    services="documentdb"
    documentationCenter=""
    authors="dennyglee"
    manager="jhubbard"
    editor="mimig"/>

<tags
    ms.service="documentdb"
    ms.devlang="rest-api"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-services"
    ms.date="07/08/2016"
    ms.author="denlee"/>

#<a name="connecting-documentdb-with-azure-search-using-indexers"></a>Connexion DocumentDB avec recherche Azure à l’aide d’indexeurs

Si vous avez besoin pour mettre en œuvre des expériences recherche exceptionnelle sur vos données DocumentDB, utilisez indexeur recherche Azure pour DocumentDB ! Dans cet article, nous vous montrent comment intégrer DocumentDB Azure Azure recherche sans avoir à écrire du code pour conserver une infrastructure d’indexation !

Pour ce faire, vous devez [configurer un compte Azure recherche](../search/search-create-service-portal.md) (vous n’avez pas besoin pour mettre à niveau vers recherche standard) et puis appelez l' [API REST de recherche Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx) pour créer une **source de données** de DocumentDB et un **indexeur** pour cette source de données.

Dans la commande Envoyer des demandes d’interagir avec l’API REST, vous pouvez utiliser [Postman](https://www.getpostman.com/), [Fiddler](http://www.telerik.com/fiddler)ou n’importe quel outil de votre choix.


##<a id="Concepts"></a>Concepts d’indexeur recherche Azure

Azure Search prend en charge la création et la gestion des données sources (y compris DocumentDB) et indexeurs opérant par rapport à ces sources de données.

Une **source de données** indique quelles données doivent être indexées, les informations d’identification pour accéder aux données et des stratégies pour activer la recherche Azure pour identifier efficacement les modifications dans les données (tel que modifié ou supprimé de documents à l’intérieur de votre collection de sites). La source de données est définie comme une ressource indépendamment de sorte qu’il peut être utilisé par plusieurs indexeurs.

Un **indexeur** décrit les flux des données à partir de votre source de données dans un index de recherche cible. Vous devez planifier sur la création d’un indexeur pour chaque combinaison de source de données et d’index cible. Vous pouvez avoir plusieurs indexeurs rédaction de l’index, un indexeur ne peut écrire dans un index unique. Un indexeur est utilisé pour :

- Effectuer une copie des données pour remplir un index unique.
- Synchroniser un index avec les modifications dans la source de données sur un planning. La planification fait partie de la définition d’indexeur.
- L’appel à la demande de mises à jour un index selon vos besoins.

##<a id="CreateDataSource"></a>Étape 1 : Créer une source de données

Émettre une requête HTTP POST pour créer une nouvelle source de données dans votre service de recherche Azure, y compris les en-têtes de demande suivante.

    POST https://[Search service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

La `api-version` est requis. Les valeurs valides incluent `2015-02-28` ou une version ultérieure. Visitez les [versions de l’API dans Azure recherche](../search/search-api-versions.md) pour afficher tous les versions API de recherche.

Le corps de la requête contient la définition de source de données, qui doit inclure les champs suivants :

- **nom**: cliquez sur n’importe quel nom pour représenter votre base de données DocumentDB.

- **type**: utilisez `documentdb`.

- **informations d’identification**:

    - **connectionString**: obligatoire. Spécifier les informations de connexion à votre base de données Azure DocumentDB au format suivant :`AccountEndpoint=<DocumentDB endpoint url>;AccountKey=<DocumentDB auth key>;Database=<DocumentDB database id>`

- **conteneur**:

    - **nom**: obligatoire. Spécifier l’id de la collection DocumentDB à indexer.

    - **requête**: facultatif. Vous pouvez spécifier une requête pour fusionner un document JSON arbitraire dans un schéma à plat Azure recherche peut indexer.

- **dataChangeDetectionPolicy**: facultatif. Consultez les [données changent stratégie de détection](#DataChangeDetectionPolicy) ci-dessous.

- **dataDeletionDetectionPolicy**: facultatif. Consultez la [stratégie de détection de suppression de données](#DataDeletionDetectionPolicy) ci-dessous.

Consultez un [corps de demande d’exemple](#CreateDataSourceExample)ci-dessous.

###<a id="DataChangeDetectionPolicy"></a>Capturer les documents modifiés

L’objectif d’une stratégie de détection de modification de données est d’identifier efficacement les éléments de données modifiées. Pour l’instant, la seule stratégie pris en charge est la `High Water Mark` stratégie à l’aide du `_ts` propriété horodatage de dernière modification fournie par DocumentDB - qui est spécifiée comme suit :

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

Vous devrez également ajouter `_ts` dans la projection et `WHERE` clause pour votre requête. Par exemple :

    SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts >= @HighWaterMark


###<a id="DataDeletionDetectionPolicy"></a>Capture de documents supprimés

Lorsque des lignes sont supprimées de la table source, vous devez supprimer les lignes d’ainsi que l’index de recherche. L’objectif d’une stratégie de détection de suppression de données est d’identifier efficacement les éléments de données supprimées. Pour l’instant, la seule stratégie pris en charge est la `Soft Delete` stratégie (suppression est marquée avec un indicateur quelconque), qui est spécifié comme suit :

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

> [AZURE.NOTE] Vous avez besoin inclure la propriété softDeleteColumnName dans votre clause SELECT si vous utilisez une projection personnalisée.

###<a id="CreateDataSourceExample"></a>Exemple de corps de requête

L’exemple suivant crée une source de données avec un conseils de requête et stratégie personnalisée :

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": {
            "name": "myDocDbCollectionId",
            "query": "SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts > @HighWaterMark"
        },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

###<a name="response"></a>Réponse

Vous recevrez une réponse HTTP 201 créé si la source de données a été créée.

##<a id="CreateIndex"></a>Étape 2 : Créer un index

Créer un index de recherche Azure cible si vous n’en avez pas déjà. Vous pouvez le faire à partir de l' [Interface utilisateur de portail Azure](../search/search-create-index-portal.md) ou à l’aide de l' [API de Index créer](https://msdn.microsoft.com/library/azure/dn798941.aspx).

    POST https://[Search service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]


Vérifiez que le schéma de votre index cible est compatible avec le schéma des documents JSON source ou la sortie de votre projection de requêtes personnalisées.

>[AZURE.NOTE] Pour les collections partitionnées, la clé de document par défaut est de DocumentDB `_rid` propriété qui obtient renommée en `rid` dans Azure rechercher. En outre, DocumentDB `_rid` valeurs contiennent des caractères qui ne sont pas valides dans les clés de recherche Azure ; Par conséquent, la `_rid` les valeurs sont en base 64 codé.

###<a name="figure-a-mapping-between-json-data-types-and-azure-search-data-types"></a>Figure a : mappage entre les Types de données JSON et Types de données de recherche Azure

| TYPE DE DONNÉES JSON|   TYPES DE CHAMP INDEX CIBLE COMPATIBLES|
|---|---|
|Bool|Edm.Boolean, Edm.String|
|Nombres qui s’affichent comme les entiers|Edm.Int32, Edm.Int64, Edm.String|
|Nombres similaires à des points flottants|Edm.Double, Edm.String|
|Chaîne|Edm.String|
|Par exemple, « a », « b », « c » les types de tableaux de base |Collection(EDM.String)|
|Chaînes qui ressemblent à des dates| Edm.DateTimeOffset, Edm.String|
|GeoJSON des objets, par exemple {« type » : « Point », « coordonnées » : [long, locales]} | Edm.GeographyPoint |
|D’autres objets JSON|N/A|

###<a id="CreateIndexExample"></a>Exemple de corps de requête

L’exemple suivant crée un index avec un champ id et une description :

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

###<a name="response"></a>Réponse

Vous recevrez une réponse HTTP 201 créé si l’index a été créé.

##<a id="CreateIndexer"></a>Étape 3 : Créer un indexeur

Vous pouvez créer un nouvel indexeur au sein d’un service de recherche Azure à l’aide d’une demande HTTP POST avec les en-têtes suivants.

    POST https://[Search service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

Le corps de la requête contient la définition d’indexeur qui doit inclure les champs suivants :

- **nom**: obligatoire. Le nom de l’indexeur.

- **dataSourceName**: obligatoire. Le nom d’une source de données existante.

- **targetIndexName**: obligatoire. Le nom d’un index existant.

- **planification**: facultatif. Voir [planification d’indexation](#IndexingSchedule) ci-dessous.

###<a id="IndexingSchedule"></a>Indexeurs en cours d’exécution sur un planning

Un indexeur peut vous pouvez également spécifier une planification. Si une planification est présente, l’indexeur s’exécutera régulièrement selon une planification. Planification comporte les attributs suivants :

- **intervalle**: obligatoire. Valeur de durée qui spécifie un intervalle ou une période d’indexeur s’exécute. Le plus petit intervalle autorisé est de 5 minutes ; la plus longue est un jour. Il doit être mis en forme comme une valeur de « dayTimeDuration » XSD (un sous-ensemble restreint d’une valeur de [durée ISO8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Le modèle pour cela est : `P(nD)(T(nH)(nM))`. Exemples : `PT15M` pour toutes les 15 minutes, `PT2H` pour toutes les 2 heures.

- **heure de début**: obligatoire. Une date/heure au format UTC qui spécifie quand l’indexeur doit commencer en cours d’exécution.

###<a id="CreateIndexerExample"></a>Exemple de corps de requête

L’exemple suivant crée un indexeur qui copie des données à partir de la collection référencée par la `myDocDbDataSource` de source de données à la `mySearchIndex` index selon un calendrier qui commence le 1er janvier 2015 UTC et exécute toutes les heures.

    {
        "name" : "mysearchindexer",
        "dataSourceName" : "mydocdbdatasource",
        "targetIndexName" : "mysearchindex",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" }
    }

###<a name="response"></a>Réponse

Vous recevrez une réponse HTTP 201 créé si l’indexeur a été créé.

##<a id="RunIndexer"></a>Étape 4 : Exécuter un indexeur

Outre les exécute régulièrement sur une planification, un indexeur peut également être appelé à la demande à l’aide de la requête HTTP POST suivante :

    POST https://[Search service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [Search service admin key]

###<a name="response"></a>Réponse

Vous recevrez une réponse HTTP 202 accepté si l’indexeur a été appelé avec succès.

##<a name="GetIndexerStatus"></a>Étape 5 : Obtenir le statut de l’indexation

Vous pouvez émettre une demande HTTP GET pour récupérer l’historique de statut et l’exécution en cours d’un indexeur :

    GET https://[Search service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [Search service admin key]

###<a name="response"></a>Réponse

Vous verrez une réponse HTTP 200 OK renvoyée avec un corps de la réponse qui contient des informations sur l’état indexeur général, le dernier appel indexeur, ainsi que l’historique des appels indexeur récentes (le cas échéant).

La réponse doit ressembler à ce qui suit :

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

L’historique d’exécution contient jusqu'à les 50 exécutions terminées plus récentes, qui sont triées dans l’ordre chronologique inverse (de sorte que l’exécution de dernière vient en premier dans la réponse).

##<a name="NextSteps"></a>Étapes suivantes

Félicitations ! Vous venez d’apprendre comment intégrer DocumentDB Azure Azure recherche à l’aide de l’indexeur pour DocumentDB.

 - Pour savoir comment DocumentDB Azure, consultez la [page du service DocumentDB](https://azure.microsoft.com/services/documentdb/).

 - Pour savoir comment plus Azure recherche, consultez la [page de service de recherche](https://azure.microsoft.com/services/search/).
