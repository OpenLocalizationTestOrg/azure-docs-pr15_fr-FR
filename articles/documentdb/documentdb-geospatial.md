<properties 
    pageTitle="Utiliser des données géospatiales dans Azure DocumentDB | Microsoft Azure" 
    description="Apprenez à créer, indexer et objets spatiaux avec Azure DocumentDB de la requête." 
    services="documentdb" 
    documentationCenter="" 
    authors="arramac" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="documentdb" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="10/25/2016" 
    ms.author="arramac"/>
    
# <a name="working-with-geospatial-data-in-azure-documentdb"></a>Utiliser des données géospatiales dans Azure DocumentDB

Cet article est une introduction à la fonctionnalité géospatiales dans [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/). Lire, vous serez en mesure de répondre aux questions suivantes :

- Comment stocker les données spatiales dans DocumentDB Azure ?
- Comment puis-je interroger des données géospatiales dans Azure DocumentDB dans SQL et LINQ ?
- Comment activer ou désactiver l’indexation spatiale dans DocumentDB ?

Voir ce [projet Github](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs) des échantillons de code.

## <a name="introduction-to-spatial-data"></a>Introduction aux données spatiales

Données spatiales décrivent la position et la forme d’objets dans l’espace. Dans la plupart des applications, ils correspondent aux objets de la terre, c'est-à-dire les données géographiques. Données spatiales peuvent être utilisées pour représenter l’emplacement d’une personne, un emplacement d’intérêt ou la limite d’une ville ou un lake. Scénarios d’utilisation courants impliquent souvent des requêtes proximité, par exemple, « Find tous les restaurants près mon emplacement actuel ». 

### <a name="geojson"></a>GeoJSON
DocumentDB prend en charge l’indexation et l’interrogation des données géospatiales point qui sont représentées à l’aide de la [spécification GeoJSON](http://geojson.org/geojson-spec.html). Les structures de données GeoJSON sont toujours valides objets JSON, afin qu’ils peuvent être stockés et interrogé à l’aide de DocumentDB sans les outils spécialisés ou les bibliothèques. Kits de développement logiciel DocumentDB fournissent des classes d’assistance et les méthodes qui permettent d’utiliser des données spatiales. 

### <a name="points-linestrings-and-polygons"></a>Points, linestrings et polygone
Un **Point** indique une position unique dans l’espace. Dans les données géographiques, un point représente l’emplacement exact, qui pourrait être une adresse postale d’une épicerie, une borne, une voiture ou une ville.  Un point est représenté en GeoJSON (et DocumentDB) à l’aide de ses coordonnées paire ou longitude et latitude. Voici un exemple JSON pour un point.

**Points de DocumentDB**

    {
       "type":"Point",
       "coordinates":[ 31.9, -4.8 ]
    }

>[AZURE.NOTE] La spécification GeoJSON spécifie longitude prénom et latitude deuxième. Comme dans d’autres applications de mappage, longitude latitude angles et sont exprimé en degrés. Valeurs longitude sont mesurées à partir du premier méridien et est comprise entre -180 et 180.0 degrés et latitude valeurs sont mesurées à partir de l’Équateur et soient comprises entre -90.0 et 90.0 degrés. 
>
> DocumentDB interprète coordonnées comme représenté par le système de référence WGS 84. Voir ci-dessous pour plus d’informations sur les systèmes de référence des coordonnées.

Cela peut être incorporé dans un document DocumentDB comme le montre cet exemple d’un profil utilisateur contenant les données d’emplacement :

**Utiliser le profil avec l’emplacement stockée dans DocumentDB**

    {
       "id":"documentdb-profile",
       "screen_name":"@DocumentDB",
       "city":"Redmond",
       "topics":[ "NoSQL", "Javascript" ],
       "location":{
          "type":"Point",
          "coordinates":[ 31.9, -4.8 ]
       }
    }

En plus de points, GeoJSON prend également en charge les LineStrings et polygone. **LineStrings** représenter une série de deux ou plusieurs points dans l’espace et les segments de ligne qui les relient. Dans les données géographiques, linestrings sont généralement utilisées pour représenter des autoroutes ou terminaux. Un **Polygone** est une limite de points connectés qui constitue un LineString fermé. Polygone est fréquemment utilisées pour représenter les formations Natural Keyboard comme lacs ou politiques juridictions tels que villes et les États. Voici un exemple d’un polygone dans DocumentDB. 

**Polygone dans DocumentDB**

    {
       "type":"Polygon",
       "coordinates":[
           [ 31.8, -5 ],
           [ 31.8, -4.7 ],
           [ 32, -4.7 ],
           [ 32, -5 ],
           [ 31.8, -5 ]
       ]
    }

>[AZURE.NOTE] La spécification GeoJSON requiert que pour polygone valide, la dernière paire de coordonnées fournie convient identique à la première, pour créer une forme fermée.
>
>Points à l’intérieur d’un polygone doivent être indiquées dans l’ordre inverse des aiguilles. Un polygone spécifié dans le sens horaire représente l’inverse de la région qu’il contient.

Outre les Point, LineString et polygone, GeoJSON spécifie également la représentation pour le regroupement de plusieurs emplacements géographiques, ainsi que comment associer propriétés arbitraires géolocalisation en tant que **fonctionnalité**. Dans la mesure où ces objets sont JSON valide, elles peuvent toutes stockées et traitées dans DocumentDB. Toutefois DocumentDB ne prend en charge l’indexation automatique des points.

### <a name="coordinate-reference-systems"></a>Repères de référence

Étant donné que la forme de la terre est irrégulière, les coordonnées des données géospatiales est représenté dans de nombreux systèmes de référence des coordonnées (CRS), chacune avec leur propre cadres de référence et les unités de mesure. Par exemple, le « National grille de Grande-Bretagne » est un système de référence est très précis pour le Royaume-Uni, mais pas à l’extérieur. 

Le SIR les plus populaires en cours d’utilisation nous sommes aujourd'hui le système géodésique World [WGS 84](http://earth-info.nga.mil/GandG/wgs84/). Appareils GPS et les services de mappage, y compris des cartes Google et API de cartes Bing utilisent WGS 84. DocumentDB prend en charge l’indexation et l’interrogation des données géospatiales à l’aide de la CRS WGS 84 uniquement. 

## <a name="creating-documents-with-spatial-data"></a>Création de documents avec données spatiales
Lorsque vous créez des documents qui contiennent des valeurs GeoJSON, ils sont automatiquement indexés avec un index spatial conformément à la politique d’indexation de la collection de sites. Si vous travaillez avec un SDK DocumentDB dans une langue, tels que Python ou Node.js dynamiquement tapée, vous devez créer GeoJSON valide.

**Créer un Document avec des données géospatiales dans Node.js**

    var userProfileDocument = {
       "name":"documentdb",
       "location":{
          "type":"Point",
          "coordinates":[ -122.12, 47.66 ]
       }
    };

    client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
        // additional code within the callback
    });

Si vous travaillez avec le .NET (ou Java) SDK, vous pouvez utiliser les nouvelles classes Point et polygone au sein de l’espace de noms Microsoft.Azure.Documents.Spatial pour incorporer des informations d’emplacement au sein des objets de votre application. Ces classes permettent de simplifier la sérialisation et désérialisation de données spatiales dans GeoJSON.

**Créer un Document avec des données géospatiales dans .NET**

    using Microsoft.Azure.Documents.Spatial;
    
    public class UserProfile
    {
        [JsonProperty("name")]
        public string Name { get; set; }

        [JsonProperty("location")]
        public Point Location { get; set; }
        
        // More properties
    }
    
    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri("db", "profiles"), 
        new UserProfile 
        { 
            Name = "documentdb", 
            Location = new Point (-122.12, 47.66) 
        });

Si vous n’avez pas les informations de latitude et longitude, mais possédez les adresses physiques ou le nom d’emplacement, tels que ville ou pays, vous pouvez rechercher les coordonnées réelles à l’aide d’un service de géocodage tel que Bing Maps reste Services. En savoir plus sur le géocodage Bing Maps [ici](https://msdn.microsoft.com/library/ff701713.aspx).

## <a name="querying-spatial-types"></a>Interroger types spatiales

À présent que nous avons observez comment insérer des données géospatiales, examinons comment interroger ces données à l’aide de DocumentDB à l’aide de SQL et LINQ.

### <a name="spatial-sql-built-in-functions"></a>Fonctions intégrées SQL spatiales
DocumentDB prend en charge les fonctions intégrées ouvrir géospatiales Consortium (OGC) suivantes pour interroger géospatiales. Pour plus d’informations sur l’ensemble des fonctions intégrées dans le langage SQL, reportez-vous à la [Requête DocumentDB](documentdb-sql-query.md).

<table>
<tr>
  <td><strong>Utilisation</strong></td>
  <td><strong>Description</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (point_expr, point_expr)</td>
  <td>Renvoie la distance entre les deux expressions point GeoJSON.</td>
</tr>
<tr>
  <td>ST_WITHIN (point_expr, polygon_expr)</td>
  <td>Retourne une expression booléenne qui indique si le point de GeoJSON spécifié dans le premier argument est intérieur du polygone GeoJSON dans le deuxième argument.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Renvoie une valeur booléenne indiquant si l’expression de point ou polygone GeoJSON spécifiée est valide.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Renvoie la valeur d’une valeur JSON qui contient une valeur booléenne si l’expression de point ou polygone GeoJSON spécifiée est valide et si non valide, plus la raison pour laquelle comme une valeur de chaîne.</td>
</tr>
</table>

Fonctions spatiales peuvent être utilisées pour exécuter des requêtes de proximité sur données spatiales. Par exemple, voici une requête qui renvoie tous les documents famille situés à 30 kilomètres de l’emplacement spécifié à l’aide de la fonction intégrée ST_DISTANCE. 

**Requête**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Résultats**

    [{
      "id": "WakefieldFamily"
    }]

Si vous incluez une indexation spatiale dans votre stratégie d’indexation, puis « requêtes distance » seront servis efficacement dans l’index. Pour plus d’informations sur l’indexation spatiale, voir la section ci-dessous. Si vous n’avez pas un index spatial pour les chemins d’accès spécifiés, vous pouvez toujours effectuer des recherches spatiales en spécifiant `x-ms-documentdb-query-enable-scan` en-tête de requête avec la valeur définie sur « true ». Dans .NET, il est possible en passant l’argument facultatif **FeedOptions** requêtes avec [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) défini sur true. 

ST_WITHIN peut être utilisé pour vérifier si un point se trouve dans un polygone. Polygone est fréquemment utilisées pour représenter des limites, comme les codes postaux, les frontières des États ou des formations Natural Keyboard. Nouveau si vous incluez une indexation spatiale dans votre stratégie d’indexation, puis requêtes « within » seront servis efficacement dans l’index. 

Arguments polygone dans ST_WITHIN peuvent contenir uniquement une sonnerie simple, c'est-à-dire polygone ne doit pas contenir holes qu’ils. 

**Requête**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon', 
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**Résultats**

    [{
      "id": "WakefieldFamily",
    }]
    
>[AZURE.NOTE] Similaire à qui ne correspondent pas comment fonctionne types dans une requête DocumentDB, si la valeur de l’emplacement spécifié dans l’argument est incorrecte ou non valide, puis il prend la valeur **non défini** et le document évalué sont ignorés dans les résultats de requête. Si votre requête ne renvoie aucun résultat, exécutez ST_ISVALIDDETAILED à déboguer pourquoi le type de spatail n’est pas valide.     

DocumentDB prend également en charge l’exécution des requêtes inverses, c'est-à-dire que vous pouvez indexer polygone ou lignes dans DocumentDB, puis recherchez les zones qui contiennent un point spécifié. Ce modèle est généralement utilisé dans une organisation à identifier, par exemple lorsqu’un chariot entre ou quitte une région. 

**Requête**

    SELECT * 
    FROM Areas a 
    WHERE ST_WITHIN({'type': 'Point', 'coordinates':[31.9, -4.8]}, a.location)


**Résultats**

    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon", 
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]

ST_ISVALID et ST_ISVALIDDETAILED peuvent être utilisés pour vérifier si un objet spatial est valide. Par exemple, la requête suivante vérifie la validité d’un point avec une valeur hors limites latitude (-132.8). ST_ISVALID renvoie une valeur booléenne et ST_ISVALIDDETAILED renvoie la valeur booléenne et une chaîne contenant la raison pour laquelle il est non valide.

**Requête**

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Résultats**

    [{
      "$1": false
    }]

Ces fonctions peuvent également être utilisées pour valider polygone. Par exemple, ici nous utilisons ST_ISVALIDDETAILED pour valider un polygone qui n’est pas fermé. 

**Requête**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})

**Résultats**

    [{
       "$1": { 
          "valid": false, 
          "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points." 
        }
    }]
    
### <a name="linq-querying-in-the-net-sdk"></a>LINQ interroger dans le Kit de développement .NET

Le Kit de développement .NET DocumentDB également fournisseurs stub méthodes `Distance()` et `Within()` à utiliser dans des expressions LINQ. Le fournisseur DocumentDB LINQ se traduit par ces appels de méthode pour les appels de fonctions intégrées SQL équivalentes (ST_DISTANCE et ST_WITHIN respectivement). 

Voici un exemple d’une requête LINQ que trouve tous les documents de la collection DocumentDB dont la valeur « emplacement » est dans un rayon de 30 kilomètres du spécifié pointez sur l’utilisation de LINQ.

**Requête LINQ pour Distance**

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(u => u.ProfileType == "Public" && a.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }

De même, voici une requête pour rechercher tous les documents dont « emplacement » se trouve dans la zone/polygone spécifié. 

**LINQ interroger dans**

    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }


À présent que nous avons observez comment interroger des documents à l’aide de LINQ et SQL, examinons comment configurer DocumentDB pour l’indexation spatiale.

## <a name="indexing"></a>L’indexation

Comme décrit dans le papier [Schéma indépendant indexation avec Azure DocumentDB](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) , nous avons conçu moteur de base de données de DocumentDB être véritablement indépendant du schéma et première classe prennent en charge JSON. Le moteur de base de données écriture optimisée de DocumentDB prend en charge en mode natif les données spatiales (points, lignes et polygone) représentées par la norme GeoJSON.

En bref, la géométrie est projetée à partir des coordonnées géodésiques sur un plan 2D puis progressivement divisée entre cellules à l’aide d’un **quadtree**. Ces cellules sont mappés aux 1J selon l’emplacement de la cellule au sein d’une **courbe de remplissage Hilbert espace**, qui conserve la localisation des points. En outre lorsque les données d’emplacement sont indexées, il passe par un processus connu sous le **fractionnement**, c'est-à-dire que toutes les cellules qui se coupent un emplacement sont identifiés et stockés en tant que clés dans l’index DocumentDB. Au moment de la requête, arguments, comme les points et polygone sont également fractionnées pour extraire les plages d’ID de cellule appropriée, puis utilisées pour récupérer des données à partir de l’index.

Si vous spécifiez une politique d’indexation incluant des index spatial pour / * (tous les chemins d’accès), puis tous les points figurant dans la collection sont indexés pour des recherches spatiales efficaces (ST_WITHIN et ST_DISTANCE). Index spatiaux ne pas avoir une valeur de précision et utilisez toujours une valeur de précision par défaut.

>[AZURE.NOTE] DocumentDB prend en charge l’indexation automatique de Points, polygone et LineStrings

L’extrait JSON suivant montre une politique d’indexation avec indexation spatiale activé, c'est-à-dire index n’importe quel point GeoJSON trouvé dans les documents pour interroger spatiale. Si vous modifiez la politique d’indexation à l’aide du portail Azure, vous pouvez spécifier la JSON suivant pour la stratégie d’indexation pour activer spatiale l’indexation sur votre collection de sites.

**JSON stratégie de collection de sites l’indexation avec Spatial activé pour les points et polygone**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Range",
                   "dataType":"String",
                   "precision":-1
                },
                {
                   "kind":"Range",
                   "dataType":"Number",
                   "precision":-1
                },
                {
                   "kind":"Spatial",
                   "dataType":"Point"
                },
                {
                   "kind":"Spatial",
                   "dataType":"Polygon"
                }                
             ]
          }
       ],
       "excludedPaths":[
       ]
    }

Voici un extrait de code dans .NET qui montre comment créer une collection de sites avec spatiale l’indexation est activée pour tous les chemins d’accès contenant des points. 

**Créer une collection de sites avec une indexation spatiale**

    DocumentCollection spatialData = new DocumentCollection()
    spatialData.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point)); //override to turn spatial on by default
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), spatialData);

Et Voici comment vous pouvez modifier un regroupement existant pour tirer parti de l’indexation spatiale sur tous les points qui sont stockés dans des documents.

**Modifier un regroupement existant avec l’indexation spatiale**

    Console.WriteLine("Updating collection with spatial indexing enabled in indexing policy...");
    collection.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point));
    await client.ReplaceDocumentCollectionAsync(collection);

    Console.WriteLine("Waiting for indexing to complete...");
    long indexTransformationProgress = 0;
    while (indexTransformationProgress < 100)
    {
        ResourceResponse<DocumentCollection> response = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
        indexTransformationProgress = response.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromSeconds(1));
    }

> [AZURE.NOTE] Si l’emplacement GeoJSON valeur au sein du document est incorrect ou non valide, puis elle ne sera pas obtenir indexée pour interroger spatiale. Vous pouvez valider les valeurs emplacement à l’aide de ST_ISVALID et ST_ISVALIDDETAILED.
>
> Si la définition de votre collection de sites inclut une clé de partition, l’indexation de la progression de la transformation n’est pas signalé. 

## <a name="next-steps"></a>Étapes suivantes
À présent que vous avez appris sur la prise en main géospatiales prise en charge dans DocumentDB, vous pouvez :

- Démarrer le codage avec les [exemples de code .NET géospatiales sur Github](https://github.com/Azure/azure-documentdb-dotnet/blob/fcf23d134fc5019397dcf7ab97d8d6456cd94820/samples/code-samples/Geospatial/Program.cs)
- Prise en main de géospatiales obtenir interroger à l' [DocumentDB requête laboratoire](http://www.documentdb.com/sql/demo#geospatial)
- Pour plus d’informations sur la [Requête DocumentDB](documentdb-sql-query.md)
- Pour plus d’informations sur les [Stratégies d’indexation DocumentDB](documentdb-indexing-policies.md)
