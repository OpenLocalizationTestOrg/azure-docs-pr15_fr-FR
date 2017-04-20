<properties
pageTitle="Utiliser le connecteur Office 365 vidéo dans vos applications logique | Microsoft Azure"
description="Commencer à utiliser le connecteur Office 365 vidéo dans vos applications Microsoft Azure application service logique"
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

# <a name="get-started-with-the-office365-video-connector"></a>Prise en main du connecteur Office 365 vidéo
Se connecter à Office 365 vidéo pour obtenir des informations sur un Office 365 vidéo, obtenir une liste des vidéos et bien plus encore. Le connecteur Office 365 vidéo peut être utilisé à partir de :

- Applications de logique 

>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma logique applications 2015-08-01-aperçu. Ce lien n’est pas pris en charge dans les versions précédentes de schéma.

Avec Office 365 vidéo, vous pouvez :

- Créer votre flux d’activité basé sur les données que vous obtenez à partir d’Office 365 vidéo. 
- Utiliser les actions qui vérifier l’état du portail vidéo, obtenir une liste de toutes les fonctionnalités vidéo dans un canal et bien plus encore. Ces actions Obtient une réponse et apportez la sortie disponibles pour d’autres actions. Par exemple, vous pouvez utiliser le connecteur de recherche Bing pour rechercher des vidéos d’Office 365 et puis utiliser le connecteur vidéo Office 365 pour obtenir des informations relatives à cette vidéo. Si la vidéo répond à vos besoins, vous pouvez publier cette vidéo sur Facebook. 

Pour ajouter une opération dans les applications logique, voir [créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Déclencheurs et actions

Le connecteur Office 365 vidéo avec les actions suivantes disponibles. Il n’existe aucun déclencheur.

| Déclencheurs | Actions|
| --- | --- |
| Aucun | <ul><li>Vérifie l’état du portail vidéo</li><li>Obtenir tous les canaux visibles</li><li>Obtenir l’url de lecture du manifeste Azure Media Services pour une vidéo</li><li>Obtenir le jeton porteur pour accéder à déchiffrer la vidéo</li><li>Obtient des informations sur un particulier Office 365 vidéo</li><li>Répertorie toutes les vidéos d’Office 365 présents dans un canal</li></ul>

Tous les connecteurs prennent en charge les données aux formats JSON et XML. 

## <a name="create-a-connection-to-office365-video-connector"></a>Créer une connexion au connecteur Office 365 vidéo
Lorsque vous ajoutez ce connecteur à vos applications logique, vous devez se connecter à votre compte Office 365 vidéo et autoriser les applications logique pour vous connecter à votre compte.

>[AZURE.INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]

Après avoir créé la connexion, vous entrez les propriétés vidéo Office 365, telles que le nom du client ou ID du canal La **référence de l’API REST** dans cette rubrique décrit ces propriétés.

>[AZURE.TIP] Vous pouvez utiliser cette connexion Office 365 vidéo même dans les autres applications logique.

## <a name="swagger-rest-api-reference"></a>Référence des API REST swagger
S’applique aux versions : 1.0.

### <a name="checks-video-portal-status"></a>Vérifie l’état du portail vidéo 
Vérifie l’état du portail vidéo pour voir si vidéo services sont activés.  
```GET: /{tenant}/IsEnabled``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|client|chaîne|Oui|chemin d’accès|Aucun|Le nom de client de l’annuaire de l’utilisateur fait partie d'|


#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|
|400|BadRequest|
|401|Non autorisé|
|404|Introuvable|
|500|Erreur interne du serveur|
|par défaut|Échoué de l’opération.|



### <a name="get-all-viewable-channels"></a>Obtenir tous les canaux visibles 
Obtient tous les canaux que l’utilisateur a l’affichage de l’accès à.  
```GET: /{tenant}/Channels``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|client|chaîne|Oui|chemin d’accès|Aucun|Le nom de client de l’annuaire de l’utilisateur fait partie d'|


#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|
|400|BadRequest|
|401|Non autorisé|
|404|Introuvable|
|500|Erreur interne du serveur|
|par défaut|Échoué de l’opération.|




### <a name="lists-all-the-office365-videos-present-in-a-channel"></a>Répertorie toutes les vidéos d’Office 365 présents dans un canal 
Répertorie toutes les vidéos d’Office 365 présents dans un canal.  
```GET: /{tenant}/Channels/{channelId}/Videos``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|client|chaîne|Oui|chemin d’accès|Aucun|Le nom de client de l’annuaire de l’utilisateur fait partie d'|
|channelId|chaîne|Oui|chemin d’accès|Aucun|L’id de canal à partir de laquelle vidéos doivent être extrait|


#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|
|400|BadRequest|
|401|Non autorisé|
|404|Introuvable|
|500|Erreur interne du serveur|
|par défaut|Échoué de l’opération.|




### <a name="gets-information-about-a-particular-office365-video"></a>Obtient des informations sur un particulier Office 365 vidéo 
Obtient des informations sur un particulier Office 365 vidéo.  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|client|chaîne|Oui|chemin d’accès|Aucun|Le nom de client de l’annuaire de l’utilisateur fait partie d'|
|channelId|chaîne|Oui|chemin d’accès|Aucun|L’id de canal|
|videoId|chaîne|Oui|chemin d’accès|Aucun|L’id de vidéo|


#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|
|400|BadRequest|
|401|Non autorisé|
|404|Introuvable|
|500|Erreur interne du serveur|
|par défaut|Échoué de l’opération.|




### <a name="get-playback-url-of-the-azure-media-services-manifest-for-a-video"></a>Obtenir l’url de lecture du manifeste Azure Media Services pour une vidéo 
Obtenir des url de lecture du manifeste Azure Media Services pour une vidéo.  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/playbackurl``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|client|chaîne|Oui|chemin d’accès|Aucun|Le nom de client de l’annuaire de l’utilisateur fait partie d'|
|channelId|chaîne|Oui|chemin d’accès|Aucun|L’id de canal|
|videoId|chaîne|Oui|chemin d’accès|Aucun|L’id de vidéo|
|streamingFormatType|chaîne|Oui|requête|Aucun|Type de format de diffusion en continu. 1 - smooth Streaming est ou MPEG-tiret. 0 - TLS diffusion en continu|


#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|
|400|BadRequest|
|401|Non autorisé|
|404|Introuvable|
|500|Erreur interne du serveur|
|par défaut|Échoué de l’opération.|




### <a name="get-the-bearer-token-to-get-access-to-decrypt-the-video"></a>Obtenir le jeton porteur pour accéder à déchiffrer la vidéo 
Obtenir le jeton porteur pour accéder à déchiffrer la vidéo.  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/token```

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|client|chaîne|Oui|chemin d’accès|Aucun|Le nom de client de l’annuaire de l’utilisateur fait partie d'|
|channelId|chaîne|Oui|chemin d’accès|Aucun|L’id de canal|
|videoId|chaîne|Oui|chemin d’accès|Aucun|L’id de vidéo|


#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|
|400|BadRequest|
|401|Non autorisé|
|404|Introuvable|
|500|Erreur interne du serveur|
|par défaut|Échoué de l’opération.|


## <a name="object-definitions"></a>Définitions d’objets

#### <a name="channel-channel-class"></a>Canal : Cours de canal

| Nom | Type de données | Obligatoire|
|---|---|---|
|ID|chaîne|aucune|
|Titre|chaîne|aucune|
|Description|chaîne|aucune|


#### <a name="video"></a>Vidéo 

| Nom | Type de données |Obligatoire|
|---|---|---|
|ID|chaîne|aucune|
|Titre|chaîne|aucune|
|Description|chaîne|aucune|
|CreationDate|chaîne|aucune|
|Propriétaire|chaîne|aucune|
|ThumbnailUrl|chaîne|aucune|
|VideoUrl|chaîne|aucune|
|VideoDuration|nombre entier|aucune|
|VideoProcessingStatus|nombre entier|aucune|
|ViewCount|nombre entier|aucune|


## <a name="next-steps"></a>Étapes suivantes
[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).
