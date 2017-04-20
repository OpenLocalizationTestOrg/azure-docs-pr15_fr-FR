<properties 
    pageTitle="Modèle de données d’analyse d’application" 
    description="Décrit les propriétés exporté à partir de l’exportation continue dans JSON et utilisés comme filtre." 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="03/21/2016" 
    ms.author="awills"/>

# <a name="application-insights-export-data-model"></a>Modèle de données d’application Insights exporter

Ce tableau répertorie les propriétés de télémétrie envoyé à partir de [l’Application Insights](app-insights-overview.md) SDK au portail. Vous verrez ces propriétés de sortie des données à partir [d’Exporter continue](app-insights-export-telemetry.md).
Ils apparaissent également dans les filtres de propriétés dans [L’Explorateur métrique](app-insights-metrics-explorer.md) et les [Diagnostics de recherche](app-insights-diagnostic-search.md).

Points à noter :

* `[0]`dans ces tables désigne un point à l’emplacement où vous devez insérer un index ; Il n’est pas toujours 0.
* Durées sont dans dizaines de microsecondes, donc 10000000 == 1 seconde.
* Dates et heures sont au format UTC et figurent dans le format ISO`yyyy-MM-DDThh:mm:ss.sssZ`

Il existe plusieurs [exemples](app-insights-export-telemetry.md#code-samples) qui illustrent comment les utiliser.



## <a name="example"></a>Exemple

    // A server report about an HTTP request
    {
    "request": [ 
      {
        "urlData": { // derived from 'url'
          "host": "contoso.org",
          "base": "/",
          "hashTag": "" 
        },
        "responseCode": 200, // Sent to client
        "success": true, // Default == responseCode<400
        // Request id becomes the operation id of child events 
        "id": "fCOhCdCnZ9I=",  
        "name": "GET Home/Index",
        "count": 1, // 100% / sampling rate
        "durationMetric": {
          "value": 1046804.0, // 10000000 == 1 second
          // Currently the following fields are redundant:
          "count": 1.0,
          "min": 1046804.0,
          "max": 1046804.0,
          "stdDev": 0.0,
          "sampledValue": 1046804.0
        },
        "url": "/"
      }
    ],
    "internal": {
      "data": {
        "id": "7f156650-ef4c-11e5-8453-3f984b167d05",
        "documentVersion": "1.61"
      }
    },
    "context": {
      "device": { // client browser
        "type": "PC",
        "screenResolution": { },
        "roleInstance": "WFWEB14B.fabrikam.net"
      },
      "application": { },
      "location": { // derived from client ip
        "continent": "North America",
        "country": "United States",
        // last octagon is anonymized to 0 at portal:
        "clientip": "168.62.177.0", 
        "province": "",
        "city": ""
      },
      "data": {
        "isSynthetic": true, // we identified source as a bot
        // percentage of generated data sent to portal:
        "samplingRate": 100.0, 
        "eventTime": "2016-03-21T10:05:45.7334717Z" // UTC
      },
      "user": {
        "isAuthenticated": false,
        "anonId": "us-tx-sn1-azr", // bot agent id
        "anonAcquisitionDate": "0001-01-01T00:00:00Z",
        "authAcquisitionDate": "0001-01-01T00:00:00Z",
        "accountAcquisitionDate": "0001-01-01T00:00:00Z"
      },
      "operation": {
        "id": "fCOhCdCnZ9I=",
        "parentId": "fCOhCdCnZ9I=",
        "name": "GET Home/Index"
      },
      "cloud": { },
      "serverDevice": { },
      "custom": { // set by custom fields of track calls
        "dimensions": [ ],
        "metrics": [ ]
      },
      "session": {
        "id": "65504c10-44a6-489e-b9dc-94184eb00d86",
        "isFirst": true
      }
    }
  }




## <a name="context"></a>Contexte

Tous les types de télémétrie sont accompagnés d’une section de contexte. Tous ces champs sont transmises à chaque point de données.



|Chemin d’accès|Type|Notes|
|---|---|---|
| Context.Custom.dimensions [0]  | objet]  | Paires de chaînes clé-valeur définie par paramètre de propriétés personnalisées. Longueur maximale de la clé 100, les valeurs de longueur maximale 1024. Plus de 100 valeurs uniques, la propriété peut être recherchée mais ne peuvent pas être utilisée pour segmentation. Touches de 200 max par ikey.  |
| Context.Custom.Metrics [0]  | objet]  | Paires clé-valeur définie par paramètre mesures personnalisé et TrackMetrics. Longueur maximale clé 100, les valeurs peuvent être numériques. |
| context.data.eventTime | chaîne | UTC |
| context.data.isSynthetic | valeur booléenne | Demande semble provenir d’un test robots ou sur le web. |
| context.data.samplingRate | nombre | Pourcentage de télémétrie générée par le Kit de développement est envoyée au portail. Plage 0,0 100.0.|
| Context.Device | objet | Périphérique client |
| Context.Device.Browser | chaîne | Internet Explorer, Chrome... |
| context.device.browserVersion | chaîne | Chrome 48.0... |
| context.device.deviceModel | chaîne | |
| context.device.deviceName | chaîne | |
| Context.Device.ID | chaîne | |
| Context.Device.locale | chaîne | us Go, de-DE... |
| Context.Device.Network | chaîne | |
| context.device.oemName | chaîne | |
| context.device.osVersion | chaîne | Système d’exploitation hôte |
| context.device.roleInstance | chaîne | ID de l’hôte du serveur |
| context.device.roleName | chaîne | |
| Context.Device.type | chaîne | PC, navigateur... |
| Context.Location | objet | Dérivée de clientip. |
| Context.Location.City | chaîne | Dérivée de clientip, le cas échéant  |
| Context.Location.ClientIP | chaîne | Dernière octogone est anonymes à 0. |
| Context.Location.continent | chaîne | |
| Context.Location.Country | chaîne | |
| Context.Location.province | chaîne | Département ou région |
| Context.Operation.ID | chaîne | Tous les éléments ayant le même id opération apparaissent en tant que les éléments liés dans le portail. En règle générale, l’id de la demande. |
| Context.Operation.Name | chaîne | nom d’URL ou demande |
| context.operation.parentId | chaîne | Permet aux éléments associés imbriquées. |
| Context.session.ID | chaîne | ID d’un groupe d’opérations à partir de la même source. Une période de 30 minutes sans opération signale la fin d’une session. |
| context.session.isFirst | valeur booléenne | |
| context.user.accountAcquisitionDate | chaîne | |
| context.user.anonAcquisitionDate | chaîne | |
| context.user.anonId | chaîne | |
| context.user.authAcquisitionDate | chaîne | [Utilisateur authentifié](app-insights-api-custom-events-metrics.md#authenticated-users) |
| context.user.isAuthenticated | valeur booléenne | |
| internal.data.documentVersion | chaîne | |
| Internal.Data.ID | chaîne | |



## <a name="events"></a>Événements

Événements personnalisés générés par [TrackEvent()](app-insights-api-custom-events-metrics.md#track-event). 


|Chemin d’accès|Type|Notes|
|---|---|---|
| nombre d’événements [0] | nombre entier | 100 / (taux[d’échantillonnage](app-insights-sampling.md) ). Par exemple 4 =&gt; 25 %. |
| nom de l’événement [0] | chaîne | Nom de l’événement.  Longueur maximale de 250. |
| url d’événement [0] | chaîne | |
| urlData.base événement [0] | chaîne | |
| urlData.host événement [0] | chaîne | |

## <a name="exceptions"></a>Exceptions

Rapports [exceptions](app-insights-asp-net-exceptions.md) sur le serveur et dans le navigateur. 


|Chemin d’accès|Type|Notes|
|---|---|---|
| assemblage basicException [0] | chaîne | |
| Nb basicException [0] | nombre entier | 100 / (taux[d’échantillonnage](app-insights-sampling.md) ). Par exemple 4 =&gt; 25 %. |
| exceptionGroup basicException [0] | chaîne | |
| exceptionType basicException [0] | chaîne | |chaîne | |
| failedUserCodeMethod basicException [0] | chaîne | |
| failedUserCodeAssembly basicException [0] | chaîne | |
| handledAt basicException [0] | chaîne | |
| hasFullStack basicException [0] | valeur booléenne | |
| id de basicException [0] | chaîne | |
| méthode basicException [0] | chaîne | |
| message basicException [0] | chaîne | Message d’exception. Longueur maximale de 10 Ko.|
| outerExceptionMessage basicException [0] | chaîne | |
| outerExceptionThrownAtAssembly basicException [0] | chaîne | |
| outerExceptionThrownAtMethod basicException [0] | chaîne | |
| outerExceptionType basicException [0] | chaîne | |
| outerId basicException [0] | chaîne | |
| basicException [0] parsedStack [0] assemblage | chaîne | |
| nom de fichier basicException [0] parsedStack [0] | chaîne | |
| niveau de parsedStack [0] basicException [0] | nombre entier | |
| ligne de parsedStack [0] basicException [0] | nombre entier | |
| méthode de parsedStack [0] basicException [0] | chaîne | |
| pile basicException [0] | chaîne | Longueur maximale de 10k|
| typeName basicException [0] | chaîne | |



## <a name="trace-messages"></a>Suivi des Messages

Envoyé par [TrackTrace](app-insights-api-custom-events-metrics.md#track-trace)et par la [journalisation des cartes](app-insights-asp-net-trace-logs.md).


|Chemin d’accès|Type|Notes|
|---|---|---|
| message [0] loggerName | chaîne ||
| paramètres de message [0] | chaîne ||
| message [0] brut | chaîne | Le message du journal, longueur maximale 10 Ko. |
| message [0] severityLevel | chaîne | |



## <a name="remote-dependency"></a>Dépendance à distance

Envoyé par TrackDependency. Permet de performances du rapport et l’utilisation d' [appels de dépendances](app-insights-asp-net-dependencies.md) sur le serveur et AJAX appelle dans le navigateur.

|Chemin d’accès|Type|Notes|
|---|---|---|
| remoteDependency [0] asynchrone | valeur booléenne | |
| nom de base de remoteDependency [0] | chaîne |  |
| commandName remoteDependency [0] | chaîne | Par exemple, « accueil/index » |
| Nb remoteDependency [0] | nombre entier | 100 / (taux[d’échantillonnage](app-insights-sampling.md) ). Par exemple 4 =&gt; 25 %. |
| dependencyTypeName remoteDependency [0] | chaîne | HTTP, SQL... |
| durationMetric.value remoteDependency [0] | nombre | Heure de l’appel à la fin de réponse par dépendance |
| id de remoteDependency [0] | chaîne | |
| nom remoteDependency [0] | chaîne | URL. Longueur maximale de 250.|
| resultCode remoteDependency [0] | chaîne | à partir de dépendance HTTP |
| succès remoteDependency [0] | valeur booléenne | |
| type de remoteDependency [0] | chaîne | HTTP, Sql... |
| url remoteDependency [0] | chaîne |  Longueur maximale de 2000 |
| urlData.base remoteDependency [0] | chaîne | Longueur maximale de 2000 |
| urlData.hashTag remoteDependency [0] | chaîne | |
| urlData.host remoteDependency [0] | chaîne | Longueur maximale de 200 |


## <a name="requests"></a>Demandes

Envoyé par [TrackRequest](app-insights-api-custom-events-metrics.md#track-request). Les modules standard permet de temps de réponse du serveur de rapports, mesurée sur le serveur. 


|Chemin d’accès|Type|Notes|
|---|---|---|
| comptage des demandes [0] | nombre entier | 100 / (taux[d’échantillonnage](app-insights-sampling.md) ). Par exemple : 4 =&gt; 25 %. |
| durationMetric.value demande [0] | nombre | Temps écoulé demande arrivant vers la réponse. 1e7 == 1 s |
| id de la requête [0] | chaîne | Id d’opération |
| nom de la demande [0] | chaîne | OBTENIR/billet + url de base.  Longueur maximale de 250 |
| responseCode demande [0] | nombre entier | Réponse HTTP envoyée au client |
| succès demande [0] | valeur booléenne | Par défaut == (responseCode &lt; 400) |
| url de la requête [0] | chaîne | Non compris hôte |
| urlData.base demande [0] | chaîne | |
| urlData.hashTag demande [0] | chaîne |  |
| urlData.host demande [0] | chaîne | |


## <a name="page-view-performance"></a>Page Afficher les performances

Envoyé par le navigateur. Mesures de la durée de traitement d’une page, à partir de l’utilisateur à l’origine de la demande d’affichage complet (à l’exception des appels AJAX asynchrone).

Valeurs de contexte affichent client du système d’exploitation et version du navigateur. 


|Chemin d’accès|Type|Notes|
|---|---|---|
| clientProcess.value clientPerformance [0] | nombre entier | Heure de fin de recevoir le code HTML à l’affichage de la page. |
| nom clientPerformance [0] | chaîne | |
| networkConnection.value clientPerformance [0] | nombre entier | Temps nécessaire pour établir une connexion réseau. |
| receiveRequest.value clientPerformance [0] | nombre entier | Heure de fin d’envoyer la demande à recevoir le code HTML dans la réponse. |
| sendRequest.value clientPerformance [0] | nombre entier | Temps de prises pour envoyer la demande HTTP. |
| total.value clientPerformance [0] | nombre entier | Heure de début envoyer la demande à l’affichage de la page. |
| url clientPerformance [0] | chaîne | URL de cette requête |
| urlData.base clientPerformance [0] | chaîne | |
| urlData.hashTag clientPerformance [0] | chaîne | |
| urlData.host clientPerformance [0] | chaîne | |
| urlData.protocol clientPerformance [0] | chaîne | |

## <a name="page-views"></a>Affichages de page

Envoyé par trackPageView() ou [stopTrackPage](app-insights-api-custom-events-metrics.md#page-view)

|Chemin d’accès|Type|Notes|
|---|---|---|
| nombre d’affichages [0] | nombre entier | 100 / (taux[d’échantillonnage](app-insights-sampling.md) ). Par exemple 4 =&gt; 25 %. |
| Afficher durationMetric.value [0] | nombre entier | Valeur éventuellement définie dans trackPageView() ou par startTrackPage() - stopTrackPage(). Pas le même que clientPerformance valeurs. |
| nom de l’affichage [0] | chaîne | Titre de la page.  Longueur maximale de 250 |
| url d’affichage [0] | chaîne | |
| Afficher urlData.base [0] | chaîne | |
| Afficher urlData.hashTag [0] | chaîne | |
| Afficher urlData.host [0] | chaîne | |



## <a name="availability"></a>Disponibilité

[Tests de site web de disponibilité](app-insights-monitor-web-app-availability.md)des rapports.

|Chemin d’accès|Type|Notes|
|---|---|---|
| disponibilité [0] availabilityMetric.name | chaîne | disponibilité |
| disponibilité [0] availabilityMetric.value | nombre |1.0 ou 0.0 |
| nombre de disponibilité [0] | nombre entier | 100 / (taux[d’échantillonnage](app-insights-sampling.md) ). Par exemple 4 =&gt; 25 %. |
| disponibilité [0] dataSizeMetric.name | chaîne | |
| disponibilité [0] dataSizeMetric.value | nombre entier | |
| disponibilité [0] durationMetric.name | chaîne | |
| disponibilité [0] durationMetric.value | nombre | Durée de test. 1e7 == 1 s |
| message de disponibilité [0] | chaîne | Échec de diagnostic |
| résultat de disponibilité [0] | chaîne | Réussite/échec |
| disponibilité [0] runLocation | chaîne | Source geo de demande http |
| disponibilité [0] nomtest | chaîne | |
| disponibilité [0] testRunId | chaîne | |
| disponibilité [0] testTimestamp | chaîne | |




## <a name="metrics"></a>Indicateurs

Générés par TrackMetric().

La valeur métrique se trouve dans context.custom.metrics[0]

Par exemple :

    {
     "metric": [ ],
     "context": {
     ...
     "custom": {
        "dimensions": [
          { "ProcessId": "4068" }
        ],
        "metrics": [
          {
            "dispatchRate": {
              "value": 0.001295,
              "count": 1.0,
              "min": 0.001295,
              "max": 0.001295,
              "stdDev": 0.0,
              "sampledValue": 0.001295,
              "sum": 0.001295
            }
          }
         } ] }
    }

## <a name="about-metric-values"></a>À propos des valeurs métriques

Valeurs métriques, à la fois des rapports métriques et ailleurs, sont signalées avec une structure d’objet standard. Par exemple :

      "durationMetric": {
        "name": "contoso.org",
        "type": "Aggregation",
        "value": 468.71603053650279,
        "count": 1.0,
        "min": 468.71603053650279,
        "max": 468.71603053650279,
        "stdDev": 0.0,
        "sampledValue": 468.71603053650279
      }

Actuellement : bien que cela peut changer dans le futur - dans toutes les valeurs signalées par les modules SDK standard, `count==1` et uniquement la `name` et `value` les champs sont utiles. Le seul cas où ils seraient différentes serait si vous écrivez votre propre appels TrackMetric dans lequel vous définissez les autres paramètres. 

L’objectif des autres champs est afin que les mesures être agrégée dans le Kit de développement pour réduire le trafic au portail. Par exemple, vous pourriez trouver la moyenne plusieurs lectures successives avant d’envoyer chaque rapport métrique. Puis vous pouvez de calculer la min, max, l’écart type et valeur d’agrégation (somme ou moyenne) et définir le nombre au nombre de lectures représenté par le rapport. 

Dans les tableaux ci-dessus, nous avons omis le nombre de champs rarement utilisée min, max, stdDev et sampledValue.

Au lieu de groupement des indicateurs, vous pouvez utiliser [échantillonnages](app-insights-sampling.md) si vous avez besoin réduire le volume de télémétrie.


### <a name="durations"></a>Durées

Sauf indication contraire, les durées sont représentées dans dizaines de microsecondes, afin que les 10 000 000,0 signifie 1 seconde.



## <a name="see-also"></a>Voir aussi

* [Analyse de l’application](app-insights-overview.md) 
* [Exporter en continu](app-insights-export-telemetry.md)
* [Exemples de code](app-insights-export-telemetry.md#code-samples)


