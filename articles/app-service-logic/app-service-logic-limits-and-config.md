<properties
    pageTitle="Limites de l’application logique et configuration | Microsoft Azure"
    description="Vue d’ensemble des limites de service et des valeurs de configuration disponibles pour les applications logique."
    services="logic-apps"
    documentationCenter=".net,nodejs,java"
    authors="jeffhollan"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jehollan"/>

# <a name="logic-app-limits-and-configuration"></a>Limites de l’application logique et configuration

Vous trouverez ci-dessous des informations sur les limites en cours et les détails de la configuration pour les applications de logique Azure.

## <a name="limits"></a>Limites

### <a name="http-request-limits"></a>Limites de demande HTTP

Il s’agit des limites pour un seul appel connecteur et/ou la demande HTTP

#### <a name="timeout"></a>Délai d’attente

|Nom|Limite|Notes|
|----|----|----|
|Délai de requête|1 minute|Un [modèle asynchrone](app-service-logic-create-api-app.md) ou [jusqu'à une boucle](app-service-logic-loops-and-scopes.md) pouvez compense perte selon vos besoins|

#### <a name="message-size"></a>Taille du message

|Nom|Limite|Notes|
|----|----|----|
|Taille du message|50 MO|Certains liens et des API ne peut pas en charge 50 Mo.  Demande de déclencheur prend en charge jusqu'à 25 Mo|
|Limite de d’évaluation d’expression|131 072 caractères|`@concat()`, `@base64()`, `string` ne peut pas être plus longue|

#### <a name="retry-policy"></a>Réessayer de stratégie

|Nom|Limite|Notes|
|----|----|----|
|Nouvelles tentatives|4|Pouvez configurer avec le [paramètre de stratégie de réessayer](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|
|Réessayer délai maximal|1 heure|Pouvez configurer avec le [paramètre de stratégie de réessayer](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|
|Min délai entre reprises|20 min|Pouvez configurer avec le [paramètre de stratégie de réessayer](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|

### <a name="run-duration-and-retention"></a>Rétention et la durée d’exécution

Voici les limites pour une application logique unique exécuter.

|Nom|Limite|Notes|
|----|----|----|
|Durée d’exécution|90 jours||
|Rétention du stockage|90 jours|Il s’agit de l’heure de début d’exécution|
|Intervalle de périodicité min|s 15||
|Intervalle de périodicité max|500 jours||


### <a name="looping-and-debatching-limits"></a>Effectuer une boucle, debatching limites

Il s’agit des limites pour une application logique unique exécuter.

|Nom|Limite|Notes|
|----|----|----|
|Éléments ForEach|5 000|Vous pouvez utiliser l' [action de requête](../connectors/connectors-native-query.md) pour filtrer des tableaux plus grands selon vos besoins|
|Jusqu'à itérations|10 000||
|Éléments SplitOn|5 000||
|Parallélisme ForEach|20|Vous pouvez affecter à une foreach séquentielle en ajoutant `"operationOptions": "Sequential"` à la `foreach` action|


### <a name="throughput-limits"></a>Limites du débit

Il s’agit des limites pour une instance d’application logique unique. 

|Nom|Limite|Notes|
|----|----|----|
|Déclencheurs par seconde|100|Distribuer des flux de travail entre plusieurs applications selon vos besoins|

### <a name="definition-limits"></a>Limites de la définition

Il s’agit des limites pour une définition de l’application logique unique.

|Nom|Limite|Notes|
|----|----|----|
|Actions en ForEach|1|Vous pouvez ajouter des flux de travail imbriquées pour étendre cette selon vos besoins|
|Actions par le flux de travail|60|Vous pouvez ajouter des flux de travail imbriquées pour étendre cette selon vos besoins|
|Autorisé d’imbrication action|5|Vous pouvez ajouter des flux de travail imbriquées pour étendre cette selon vos besoins|
|Flux par région par abonnement|1000||
|Déclencheurs par le flux de travail|10||
|Caractères max par expression|8 192||
|Max `trackedProperties` taille en caractères|16 000|
|`action`/`trigger`limite de nom|80||
|`description`longueur maximale|256||
|`parameters`limite|50||
|`outputs`limite|10||

## <a name="configuration"></a>Configuration

### <a name="ip-address"></a>Adresse IP

Appels effectués à partir d’un [connecteur](../connectors/apis-list.md) proviendra de l’adresse IP spécifiée ci-dessous.

Appels effectués à partir d’une application logique directement (c'est-à-dire, via [HTTP](../connectors/connectors-native-http.md) ou [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)) peuvent provenir d’un des [Plages d’adresses IP Azure centre de données](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

|Logique application région|Adresses IP sortante|
|-----|----|
|Australie est|40.126.251.213|
|Australie sud-est.|40.127.80.34|
|Brésil sud|191.232.38.129|
|Inde central|104.211.98.164|
|États-Unis centre|40.122.49.51|
|Asie de l’est|23.99.116.181|
|États-Unis Extrême-Orient|191.237.41.52|
|États-Unis Extrême-Orient 2|104.208.233.100|
|Orient Japon|40.115.186.96|
|Japon ouest|40.74.130.77|
|États-Unis centre nord|65.52.218.230|
|Europe du Nord|104.45.93.9|
|États-Unis centre sud|104.214.70.191|
|Pays d’Asie du Sud-est|13.76.231.68|
|Inde du Sud|104.211.227.225|
|Europe occidentale|40.115.50.13|
|Inde ouest|104.211.161.203|
|États-Unis Ouest|104.40.51.248|


## <a name="next-steps"></a>Étapes suivantes  

- Pour commencer avec les applications logique, suivez le didacticiel [créer une application logique](app-service-logic-create-a-logic-app.md) .  
- [Afficher des exemples et des scénarios courants](app-service-logic-examples-and-scenarios.md)
- [Vous pouvez automatiser des processus métiers avec les applications de logique](http://channel9.msdn.com/Events/Build/2016/T694) 
- [Découvrez comment intégrer vos systèmes applications logique](http://channel9.msdn.com/Events/Build/2016/P462)