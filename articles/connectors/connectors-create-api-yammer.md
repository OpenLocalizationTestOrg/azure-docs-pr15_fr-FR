<properties
pageTitle="Ajouter le lien Yammer dans vos applications logique | Microsoft Azure"
description="Vue d’ensemble du connecteur Yammer avec les paramètres de l’API REST"
services=""    
documentationCenter=""     
authors="msftman"    
manager="erikre"    
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="05/18/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-yammer-connector"></a>Prise en main le lien Yammer

Se connecter à Yammer à des conversations access dans votre réseau d’entreprise.

>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma logique applications 2015-08-01-aperçu.

Avec Yammer, vous pouvez :

- Créer votre flux d’activité basé sur les données que vous obtenez à partir de Yammer. 
- Utiliser déclenche lorsqu’il y a un nouveau message dans un groupe ou un flux votre suivantes.
- Utiliser les actions pour publier un message, extraire tous les messages et bien plus encore. Ces actions Obtient une réponse et apportez la sortie disponibles pour d’autres actions. Par exemple, lorsqu’un message s’affiche, vous pouvez envoyer un message électronique à l’aide d’Office 365.

Pour ajouter une opération dans les applications logique, voir [créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Déclencheurs et actions
Yammer inclut les déclencheurs et les actions suivantes. 

Déclencheur | Actions
--- | ---
<ul><li>Lorsqu’il y a un nouveau message dans un groupe</li><li>Lorsqu’il y a un nouveau message dans mon suivantes flux</li></ul>| <ul><li>Obtenir tous les messages</li><li>Obtient des messages dans un groupe</li><li>Obtient que les messages à partir de mon suivantes flux</li><li>Publier le message</li><li>Lorsqu’il y a un nouveau message dans un groupe</li><li>Lorsqu’il y a un nouveau message dans mon suivantes flux</li></ul>

Tous les connecteurs prennent en charge les données aux formats JSON et XML. 

## <a name="create-a-connection-to-yammer"></a>Créer une connexion à Yammer
Pour utiliser le lien Yammer, vous devez d’abord créez une **connexion** puis fournissez les détails de ces propriétés : 

|Propriété| Obligatoire|Description|
| ---|---|---|
|Jetons|Oui|Fournir des informations d’identification Yammer|

>[AZURE.INCLUDE [Steps to create a connection to Yammer](../../includes/connectors-create-api-yammer.md)]


>[AZURE.TIP] Vous pouvez utiliser cette connexion dans les autres applications logique.

## <a name="yammer-rest-api-reference"></a>Référence de l’API REST de Yammer
Cette documentation est de version : 1.0


### <a name="get-all-public-messages-in-the-logged-in-users-yammer-network"></a>Obtenir tous les messages publics de réseau Yammer de l’utilisateur connecté
Correspond à « Toutes les » conversations dans l’interface web de Yammer.  
```GET: /messages.json```

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|older_then|nombre entier|aucune|requête|Aucun|Renvoie les messages antérieurs à l’ID de message spécifié comme une chaîne numérique. Ceci est utile pour la pagination des messages. Par exemple, si vous affichez actuellement 20 messages et la plus ancienne est le nombre 2912, vous pouvez ajouter « ? older_than = 2912″ à votre requête pour obtenir les 20 messages antérieurs à ceux que vous voyez.|
|newer_then|nombre entier|aucune|requête|Aucun|Renvoie la plus récente que l’ID de message spécifié comme une chaîne numérique de messages. Il doit être utilisé lors de l’interrogation de nouveaux messages. Si vous cherchez des messages et que le message le plus récent retourné est 3516, vous pouvez faire une demande avec le paramètre » ? newer_than = 3516″ pour vous assurer que vous n’obtenez pas dupliquer des copies de messages déjà dans votre page.|
|limite|nombre entier|aucune|requête|Aucun|Renvoie uniquement le nombre spécifié de messages.|
|page|nombre entier|aucune|requête|Aucun|Obtenir la page spécifiée. Si renvoyée données sont supérieures à la limite, vous pouvez utiliser ce champ pour accéder aux pages suivantes|


### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|
|400|Demande incorrecte|
|408|Délai de requête|
|429|Trop de demandes|
|500|Erreur interne du serveur. Erreur inconnue|
|503|Yammer Service non disponible|
|504|Délai de la passerelle|
|par défaut|Échoué de l’opération.|


### <a name="post-a-message-to-a-group-or-all-company-feed"></a>Publier un Message à un groupe ou flux toute l’entreprise
Si l’ID du groupe est fourni, le message est publié au groupe spécifié dois-je qu'apparaîtra dans tous les flux de société.    
```POST: /messages.json``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|entrée| |Oui|corps|Aucun|Message de demande de billet|


### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|201|Créé|



## <a name="object-definitions"></a>Définitions d’objets

#### <a name="message-yammer-message"></a>Message : Message de Yammer

| Nom | Type de données | Obligatoire |
|---|---| --- | 
|ID|nombre entier|aucune|
|content_excerpt|chaîne|aucune|
|sender_id|nombre entier|aucune|
|replied_to_id|nombre entier|aucune|
|created_at|chaîne|aucune|
|network_id|nombre entier|aucune|
|message_type|chaîne|aucune|
|sender_type|chaîne|aucune|
|URL|chaîne|aucune|
|web_url|chaîne|aucune|
|group_id|nombre entier|aucune|
|corps|non défini|aucune|
|thread_id|nombre entier|aucune|
|direct_message|valeur booléenne|aucune|
|client_type|chaîne|aucune|
|client_url|chaîne|aucune|
|langue|chaîne|aucune|
|notified_user_ids|tableau|aucune|
|confidentialité|chaîne|aucune|
|liked_by|non défini|aucune|
|system_message|valeur booléenne|aucune|

#### <a name="postoperationrequest-represents-a-post-request-for-yammer-connector-to-post-to-yammer"></a>PostOperationRequest : Représente une demande de publication pour connecteur Yammer publier sur yammer

| Nom | Type de données | Obligatoire |
|---|---| --- | 
|corps|chaîne|Oui|
|group_id|nombre entier|aucune|
|replied_to_id|nombre entier|aucune|
|direct_to_id|nombre entier|aucune|
|diffusion|valeur booléenne|aucune|
|sujet1|chaîne|aucune|
|sujet2|chaîne|aucune|
|topic3|chaîne|aucune|
|topic4|chaîne|aucune|
|topic5|chaîne|aucune|
|topic6|chaîne|aucune|
|topic7|chaîne|aucune|
|topic8|chaîne|aucune|
|topic9|chaîne|aucune|
|rubrique10|chaîne|aucune|
|rubrique11|chaîne|aucune|
|topic12|chaîne|aucune|
|topic13|chaîne|aucune|
|topic14|chaîne|aucune|
|topic15|chaîne|aucune|
|topic16|chaîne|aucune|
|rubrique17|chaîne|aucune|
|rubrique18|chaîne|aucune|
|rubrique19|chaîne|aucune|
|rubrique20|chaîne|aucune|

#### <a name="messagelist-list-of-messages"></a>MessageList : La liste des messages

| Nom | Type de données | Obligatoire |
|---|---| --- | 
|messages|tableau|aucune|


#### <a name="messagebody-message-body"></a>L’élément MessageBody : Corps du Message

| Nom | Type de données | Obligatoire |
|---|---| --- | 
|analysés|chaîne|aucune|
|brut|chaîne|aucune|
|enrichi|chaîne|aucune|

#### <a name="likedby-liked-by"></a>LikedBy : Aime par

| Nom | Type de données | Obligatoire |
|---|---| --- | 
|nombre|nombre entier|aucune|
|noms|tableau|aucune|

#### <a name="yammmerentity-liked-by"></a>YammmerEntity : Aime par

| Nom | Type de données | Obligatoire |
|---|---| --- | 
|type|chaîne|aucune|
|ID|nombre entier|aucune|
|full_name|chaîne|aucune|


## <a name="next-steps"></a>Étapes suivantes
[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

[1]: ./media/connectors-create-api-yammer/connectionconfig1.png
[2]: ./media/connectors-create-api-yammer/connectionconfig2.png 
[3]: ./media/connectors-create-api-yammer/connectionconfig3.png
[4]: ./media/connectors-create-api-yammer/connectionconfig4.png
[5]: ./media/connectors-create-api-yammer/connectionconfig5.png
