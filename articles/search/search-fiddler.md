<properties
    pageTitle="Comment utiliser Fiddler pour évaluer et tester Azure recherche reste API | Microsoft Azure | Service de recherche cloud hébergé"
    description="Utilisez Fiddler d’une approche sans code de vérification de la disponibilité de recherche Azure et essayer de l’API REST."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="mblythe"
    editor=""/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="10/17/2016"
    ms.author="heidist"/>

# <a name="use-fiddler-to-evaluate-and-test-azure-search-rest-apis"></a>Utilisez Fiddler pour évaluer et tester Azure recherche reste API
> [AZURE.SELECTOR]
- [Vue d’ensemble](search-query-overview.md)
- [Explorateur de recherche](search-explorer.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [RESTE](search-query-rest-api.md)

Cet article explique comment utiliser Fiddler, [téléchargeable gratuitement à partir de Telerik](http://www.telerik.com/fiddler), les requêtes HTTP problème vers et afficher les réponses à l’aide de l’API REST de recherche Azure, sans avoir à écrire du code. Recherche Azure est entièrement géré, hébergé service de recherche de cloud sur Microsoft Azure, facilement programmable via .NET et API REST. Le service de recherche Azure API REST sont présentées sur [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx).

Dans la procédure suivante, vous allez créer un index, télécharger des documents, l’index de la requête et puis interroger le système pour plus d’informations de service.

Pour effectuer cette procédure, vous devez un service de recherche Azure et `api-key`. Pour obtenir des instructions sur la mise en route, voir [créer un service de recherche Azure dans le portail](search-create-service-portal.md) .

## <a name="create-an-index"></a>Créer un index

1. Démarrez Fiddler. Dans le menu **fichier** , désactivez **Le trafic capturer** pour masquer superflue activité HTTP qui n’est pas liée à la tâche actuelle.

3. Sous l’onglet **éditeur** , vous devez formuler une requête qui ressemble à l’écran suivant.

    ![][1]

2. Sélectionnez **placer**.

3. Entrez l’URL qui spécifie l’URL du service, les attributs de la demande et la version de l’api. Quelques éléments à garder à l’esprit :
   + Utiliser le protocole HTTPS comme préfixe.
   + Attribut de requête est « / index/hôtel ». Cette option indique à la recherche pour créer un index nommé « hôtel ».
   + Version de l’API est en minuscule, spécifié en tant que « ? version de l’api = 2015-02-28 ». Versions de l’API sont importantes car Azure recherche déploie régulièrement mises à jour. Parfois, une mise à jour de service peut présenter une modification avec rupture à l’API. Pour cette raison, la recherche Azure nécessite une version de l’api sur chaque demande afin que vous êtes dans un contrôle total sur celui qui est utilisé.

    L’URL complète doit ressembler à l’exemple suivant.

            https://my-app.search.windows.net/indexes/hotels?api-version=2015-02-28

4.  Spécifier l’en-tête de demande en remplaçant l’hôte et la clé d’api avec des valeurs qui sont valides pour votre service.

            User-Agent: Fiddler
            host: my-app.search.windows.net
            content-type: application/json
            api-key: 1111222233334444

5.  Dans le corps de la requête, collez dans les champs qui composent la définition d’index.
            
             {
            "name": "hotels",  
            "fields": [
              {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
              {"name": "baseRate", "type": "Edm.Double"},
              {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
              {"name": "hotelName", "type": "Edm.String"},
              {"name": "category", "type": "Edm.String"},
              {"name": "tags", "type": "Collection(Edm.String)"},
              {"name": "parkingIncluded", "type": "Edm.Boolean"},
              {"name": "smokingAllowed", "type": "Edm.Boolean"},
              {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
              {"name": "rating", "type": "Edm.Int32"},
              {"name": "location", "type": "Edm.GeographyPoint"}
             ]
            }

6.  Cliquez sur **exécuter**.

En quelques secondes, vous devez voir une réponse HTTP 201 dans la liste des sessions, indiquant que l’index a été créé avec succès.

Si vous obtenez HTTP 504, vérifiez que l’URL spécifie HTTPS. Si vous voyez HTTP 400 ou 404, vérifiez le corps de la requête pour vérifier qu’il n’existe aucune erreur de copier-coller. Un HTTP 403 indique généralement un problème avec la touche api (une clé non valide ou un problème de syntaxe avec la façon dont la clé de l’api est spécifiée).

## <a name="load-documents"></a>Charger des documents

Sous l’onglet **éditeur** , votre demande de publier des documents se présentera comme suit. Le corps de la requête contient les données de recherche pour le 4 hôtel.

   ![][2]

1. Sélectionnez **Publier**.

2.  Entrez une URL qui commence par HTTPS, suivi de votre URL du service, suivi par « /indexes/ <'indexname ' >/documents/index ? version de l’api = 2015-02-28 ». L’URL complète doit ressembler à l’exemple suivant.

            https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2015-02-28

3.  En-tête de la requête doit être identique avant. N’oubliez pas de remplacer l’hôte et la clé d’api avec des valeurs qui sont valides pour votre service.

            User-Agent: Fiddler
            host: my-app.search.windows.net
            content-type: application/json
            api-key: 1111222233334444

4.  Le corps demander contient quatre documents à ajouter à l’index hôtel.

            {
            "value": [
            {
                "@search.action": "upload",
                "hotelId": "1",
                "baseRate": 199.0,
                "description": "Best hotel in town",
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
                "@search.action": "upload",
                "hotelId": "3",
                "baseRate": 279.99,
                "description": "Surprisingly expensive",
                "hotelName": "Dew Drop Inn",
                "category": "Bed and Breakfast",
                "tags": ["charming", "quaint"],
                "parkingIncluded": true,
                "smokingAllowed": false,
                "lastRenovationDate": null,
                "rating": 4,
                "location": { "type": "Point", "coordinates": [-122.33207, 47.60621] }
              },
              {
                "@search.action": "upload",
                "hotelId": "4",
                "baseRate": 220.00,
                "description": "This could be the one",
                "hotelName": "A Hotel for Everyone",
                "category": "Basic hotel",
                "tags": ["pool", "wifi"],
                "parkingIncluded": true,
                "smokingAllowed": false,
                "lastRenovationDate": null,
                "rating": 4,
                "location": { "type": "Point", "coordinates": [-122.12151, 47.67399] }
              }
             ]
            }

8.  Cliquez sur **exécuter**.

En quelques secondes, vous devez voir une réponse HTTP 200 dans la liste des sessions. Cela indique les documents ont été créés correctement. Si vous obtenez un 207, au moins un document Échec du téléchargement. Si vous obtenez une erreur 404, vous avez une erreur de syntaxe dans l’en-tête ou le corps de la demande.

## <a name="query-the-index"></a>L’index de requête

À présent qu’un index et les documents sont chargés, vous pouvez émettre des requêtes sur les.  Sous l’onglet **éditeur** , une commande **GET** qui demande votre service est semblable à la capture d’écran suivante.

   ![][3]

1.  Sélectionnez **Insérer**.

2.  Entrez une URL qui commence par HTTPS, suivi de votre URL du service, suivi par « /indexes/ <'indexname ' > / documents ? », suivi de paramètres de la requête. Par exemple, utilisez l’URL suivante, en remplaçant le nom d’hôte exemple par un valide pour votre service.
    
            https://my-app.search.windows.net/indexes/hotels/docs?search=motel&facet=category&facet=rating,values:1|2|3|4|5&api-version=2015-02-28

    Cette requête effectue une recherche sur le terme « motel » et récupère catégories facette pour les évaluations.

3.  En-tête de la requête doit être identique avant. N’oubliez pas de remplacer l’hôte et la clé d’api avec des valeurs qui sont valides pour votre service.

            User-Agent: Fiddler
            host: my-app.search.windows.net
            content-type: application/json
            api-key: 1111222233334444

Le code de réponse doit être 200, et le résultat de la réponse doit ressembler à la capture d’écran suivante.

   ![][4]

Exemple de requête ci-dessous provient de l' [opération d’Index de recherche (API de recherche Azure)](http://msdn.microsoft.com/library/dn798927.aspx) sur MSDN. La plupart des exemples de requêtes dans cette rubrique incluent des espaces, qui ne sont pas autorisées dans Fiddler. Remplacer chaque espace avec caractère + avant de coller dans la requête de chaîne avant d’essayer de la requête dans Fiddler.

**Avant d’espaces sont remplacés :**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28

**Une fois que les espaces sont remplacées par + :**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2015-02-28

## <a name="query-the-system"></a>Interroger le système

Vous pouvez également interroger le système pour obtenir le nombre de document et la consommation de stockage. Sous l’onglet **éditeur** , votre requête est semblable à ce qui suit, et la réponse renvoie un nombre pour le nombre de documents et d’espace utilisé.

 ![][5]

1.  Sélectionnez **Insérer**.

2.  Entrez une URL qui comprend votre URL du service, suivi de « / index/hôtel/statistiques ? version de l’api = 2015-02-28 » :

            https://my-app.search.windows.net/indexes/hotels/stats?api-version=2015-02-28

3.  Spécifier l’en-tête de demande en remplaçant l’hôte et la clé d’api avec des valeurs qui sont valides pour votre service.

            User-Agent: Fiddler
            host: my-app.search.windows.net
            content-type: application/json
            api-key: 1111222233334444

4.  Laissez le corps de la requête vide.

5.  Cliquez sur **exécuter**. Vous devez voir un code d’état HTTP 200 dans la liste des sessions. Sélectionnez l’entrée validée pour votre commande.

6.  Cliquez sur l’onglet **inspecteurs** et cliquez sur l’onglet **en-têtes** , puis sélectionnez le format JSON. Vous devriez voir la taille de stockage et le nombre de document (en Ko).

## <a name="next-steps"></a>Étapes suivantes

Voir [Gérer votre service de recherche sur Azure](search-manage.md) pour un sans code approche gestion et l’utilisation de recherche Azure.


<!--Image References-->
[1]: ./media/search-fiddler/AzureSearch_Fiddler1_PutIndex.png
[2]: ./media/search-fiddler/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-fiddler/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-fiddler/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-fiddler/AzureSearch_Fiddler5_QueryStats.png
