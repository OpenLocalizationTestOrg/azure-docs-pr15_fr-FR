<properties
   pageTitle="Gestion du type de logique applications contenues | Microsoft Azure"
   description="Comprendre comment logique applications traite des types de contenu à la conception et d’exécution"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>

# <a name="logic-apps-content-type-handling"></a>Gestion du Type de contenu des applications de logique

Il existe de nombreux types de contenu qui peuvent être transmis à une logique de l’application - notamment JSON, XML, fichiers plats et données binaires.  Pendant que tous les types de contenu sont prises en charge, certaines sont comprises en mode natif par le moteur d’applications logique et d’autres personnes peuvent nécessiter cast ou conversions selon vos besoins.  L’article suivant explique comment le moteur gère les différents types de contenu et comment ils peuvent être correctement gérés selon vos besoins.

## <a name="content-type-header"></a>En-tête de Type de contenu

Pour démarrer simple, nous allons étudier deux `Content-Types` qui ne nécessitent pas toute conversion d’à utiliser dans une application logique - `application/json` et `text/plain`.

### <a name="applicationjson"></a>Application/json

Le moteur de flux de travail s’appuie sur la `Content-Type` en-tête HTTP appelle pour déterminer la gestion appropriée.  Toute demande avec le type de contenu `application/json` seront stockées et gérées comme un objet JSON.  En outre, JSON contenu peut être analysé par défaut sans avoir besoin de conversion.  Si une requête qui comporte l’en-tête de type de contenu `application/json ` comme suit :

```
{
    "data": "a",
    "foo": [
        "bar"
    ]
}
```

peut être analysée dans un flux de travail avec une expression comme `@body('myAction')['foo'][0]` pour obtenir une valeur (dans ce cas, `bar`).  Aucun conversion supplémentaires ne sont nécessaires.  Si vous travaillez avec des données JSON mais ne sont pas un en-tête spécifié, vous pouvez la diffuser manuellement à l’aide de JSON la `@json()` fonction (par exemple : `@json(triggerBody())['foo']`).

### <a name="textplain"></a>Texte/brut

Semblable à `application/json`, les messages HTTP envoyés avec la `Content-Type` en-tête de `text/plain` sera stocké dans sa forme brute.  En outre, si inclus dans des actions suivantes sans conversion la demande passera avec un `Content-Type`: `text/plain` en-tête.  Par exemple, si vous travaillez dans un fichier plat le contenu HTTP suivant peut s’afficher :

```
Date,Name,Address
Oct-1,Frank,123 Ave.
```

as `text/plain`.  Si dans l’action suivante vous avez envoyé dans le corps d’une autre requête (`@body('flatfile')`), la requête aurait un `text/plain` en-tête de Type de contenu.  Si vous travaillez avec des données au format texte brut mais ne sont pas un en-tête spécifié, vous pouvez la diffuser manuellement pour le texte en utilisant la `@string()` fonction (par exemple : `@string(triggerBody())`)

### <a name="applicationxml-and-applicationoctet-stream-and-converter-functions"></a>Application/xml et Application/octet-flux et les fonctions de conversion

Le moteur de l’application logique conserve toujours la `Content-Type` qui a été reçu sur la requête HTTP ou la réponse.  Ce que cela signifie est si un contenu est reçu avec `Content-Type` de `application/octet-stream`, y compris celui de la prochaine action avec aucun cast entraînera dans une requête sortante avec `Content-Type`: `application/octet-stream`.  De cette façon le moteur peuvent guaruntee données ne soient pas perdues lorsqu’elle est déplacée dans l’ensemble du flux de travail.  Toutefois, l’état de l’action (entrées et sorties) sont stockées dans un objet JSON qu’elles se trouvent dans l’ensemble du flux de travail.  Cela signifie que pour préserver certains types de données, le moteur convertit le contenu à une chaîne en base 64 binaire codé avec les métadonnées appropriées préservant les deux `$content` et `$content-type` -qui seront automatiquement convertis.  Vous pouvez convertir manuellement entre les types de contenu à l’aide de fonctions convertisseur intégré :

* `@json()`-effectue un cast de données`application/json`
* `@xml()`-effectue un cast de données`application/xml`
* `@binary()`-effectue un cast de données`application/octet-stream`
* `@string()`-effectue un cast de données`text/plain`
* `@base64()`-Convertit du contenu à une chaîne en base 64
* `@base64toString()`-Convertit une chaîne en base 64 codé en`text/plain`
* `@base64toBinary()`-Convertit une chaîne en base 64 codé en`application/octet-stream`
* `@encodeDataUri()`-code une chaîne en tant que tableau d’octets dataUri
* `@decodeDataUri()`-décodage un dataUri dans un tableau d’octets

Par exemple, si vous avez reçu une demande HTTP avec `Content-Type`: `application/xml` de :

```
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Je peux soumettez et utiliser ultérieurement avec s’intitule `@xml(triggerBody())`, ou dans une fonction comme `@xpath(xml(triggerBody()), '/CustomerName')`.

### <a name="other-content-types"></a>Types de contenu autres

Autres types de contenu sont prises en charge et sont prises en charge avec une logique de l’application, mais peuvent nécessiter récupération manuellement le corps du message par le décodage la `$content`.  Par exemple, si j’avais déclenchement désactiver d’un `application/x-www-url-formencoded` demande qui ressemble à ce qui suit :

```
CustomerName=Frank&Address=123+Avenue
```

Étant donné que cela un non texte brut ou JSON il sera stocké dans l’action en tant que :

```
...
"body": {
    "$content-type": "application/x-www-url-formencoded",
    "$content": "AAB1241BACDFA=="
}
```

Où `$content` la charge utile codée sous forme de chaîne en base 64 permet de conserver toutes les données.  Dans la mesure où il n’existe actuellement pas d’une fonction native pour les données de formulaire, je peux toujours utiliser ces données au sein d’un flux de travail en accédant à la manuellement les données avec une fonction comme `@string(body('formdataAction'))`.  Si j’ai voulu ma demande sortant ait également la `application/x-www-url-formencoded` en-tête du type de contenu, je peux simplement ajouter au corps d’action sans conversion comme `@body('formdataAction')`.  Toutefois, cela ne fonctionne que si le corps est le seul paramètre dans le `body` d’entrée.  Si vous essayez d’effectuer `@body('formdataAction')` à l’intérieur d’une `application/json` demander vous obtenez une erreur d’exécution comme il vous envoie le corps codé.
