<properties
pageTitle="RSS | Microsoft Azure"
description="Créez des applications de logique avec le service d’application Azure. Connecteur RSS permet aux utilisateurs de publier et de récupérer des éléments de flux. Il permet également aux utilisateurs de déclencher des opérations lorsqu’un nouvel élément est publié sur le flux."
services="logic-apps"   
documentationCenter=".net,nodejs,java"  
authors="msftman"   
manager="erikre"    
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/18/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-rss-connector"></a>Prise en main le lien RSS
RSS est un format de syndication web les plus courants utilisé pour publier du contenu fréquemment mis à jour – tels que les billets et les titres des actualités.  De nombreux éditeurs de contenu fournissent un flux RSS pour permettre aux utilisateurs de s’abonner à celui-ci.  Utilisez le lien RSS pour récupérer les flux d’informations et le déclencheur flux lorsque les nouveaux éléments sont publiés dans un flux RSS.

>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma logique applications 2015-08-01-aperçu. 

Vous pouvez commencer par créer une application logique maintenant, voir [créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Déclencheurs et actions

Le connecteur RSS peut être utilisé comme action ; Il a déclencheurs. Tous les connecteurs prennent en charge les données aux formats JSON et XML. 

 Le lien RSS comporte les déclencheurs disponibles et/ou une ou des actions suivantes :

### <a name="rss-actions"></a>Actions RSS
Vous pouvez suivre ces actions :

|Action|Description|
|--- | ---|
|[ListFeedItems](connectors-create-api-rss.md#listfeeditems)|Obtenez tous les flux RSS éléments.|
### <a name="rss-triggers"></a>Déclencheurs RSS
Vous pouvez écouter ces événements :

|Déclencheur | Description|
|--- | ---|
|Lorsqu’un nouvel élément de flux publié|Déclenche un flux de travail lorsqu’un nouveau flux est publié|


## <a name="create-a-connection-to-rss"></a>Créer une connexion à RSS

>[AZURE.INCLUDE [Steps to create a connection to an RSS feed](../../includes/connectors-create-api-rss.md)]

>[AZURE.TIP] Vous pouvez utiliser cette connexion dans les autres applications logique.

## <a name="reference-for-rss"></a>Référence RSS
S’applique à la version : 1.0

## <a name="onnewfeed"></a>OnNewFeed
Lorsqu’un nouvel élément de flux publié : déclenche un flux de travail lorsqu’un nouveau flux est publié 

```GET: /OnNewFeed``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|feedUrl|chaîne|Oui|requête|Aucun|Url du flux|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|
|202|Accepté|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|500|Erreur interne du serveur. Erreur inconnue|
|par défaut|Échoué de l’opération.|


## <a name="listfeeditems"></a>ListFeedItems
Liste de tous les flux RSS éléments. : obtenir tous les flux RSS éléments. 

```GET: /ListFeedItems``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|feedUrl|chaîne|Oui|requête|Aucun|Url du flux|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|
|202|Accepté|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|500|Erreur interne du serveur. Erreur inconnue|
|par défaut|Échoué de l’opération.|


## <a name="object-definitions"></a>Définitions d’objets 

### <a name="triggerbatchresponsefeeditem"></a>TriggerBatchResponse [FeedItem]


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|valeur|tableau|N° |



### <a name="feeditem"></a>FeedItem


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|ID|chaîne|Oui |
|titre|chaîne|Oui |
|contenu|chaîne|Oui |
|liens|tableau|N° |
|updatedOn|chaîne|N° |


## <a name="next-steps"></a>Étapes suivantes
[Créer une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md)