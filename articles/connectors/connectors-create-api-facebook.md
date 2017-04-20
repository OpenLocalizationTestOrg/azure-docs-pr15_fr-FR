<properties
    pageTitle="Ajouter le connecteur Facebook dans vos applications logique | Microsoft Azure"
    description="Vue d’ensemble du connecteur Facebook avec des paramètres de l’API REST"
    services=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="08/18/2016"
   ms.author="mandia"/>

# <a name="get-started-with-the-facebook-connector"></a>Prise en main du connecteur Facebook
Se connecter à Facebook et publier des billets dans une barre de planning, obtenir une page de flux et bien plus encore. 

>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma logique applications 2015-08-01-aperçu.


Avec Facebook, vous pouvez :

- Créer votre flux d’activité basé sur les données que vous obtenez à partir de Facebook. 
- Utilisez un déclencheur lors de la réception d’un nouveau billet.
- Actions d’utilisation publier des billets dans votre barre de planning, obtenez une page de flux et bien plus encore. Ces actions Obtient une réponse et apportez la sortie disponibles pour d’autres actions. Par exemple, lorsqu’il y a un nouveau billet dans votre barre de planning, vous pouvez prendre ce billet et transmission à votre flux Twitter. 



Pour ajouter une opération dans les applications logique, voir [créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Déclencheurs et actions
Le connecteur Facebook inclut le déclencheur et les actions suivantes. 

| Déclencheurs | Actions|
| --- | --- |
| <ul><li>Lorsqu’il y a un nouveau billet dans ma barre de planning</li></ul> |<ul><li>Accéder au flux à partir de ma barre de planning</li><li>Publier des billets dans ma barre de planning</li><li>Lorsqu’il y a un nouveau billet dans ma barre de planning</li><li>Obtenir le saut de page</li><li>Obtenir utilisateur chronologie</li><li>Publier des billets dans la page</li></ul>

Tous les connecteurs prennent en charge les données aux formats JSON et XML.

## <a name="create-a-connection-to-facebook"></a>Créer une connexion à Facebook
Lorsque vous ajoutez ce connecteur à vos applications logique, vous devez autoriser les applications logique pour vous connecter à votre Facebook.

1. Se connecter à votre compte Facebook
2. Sélectionnez **Autoriser**et permettre à vos applications logique pour vous connecter et utiliser votre Facebook. 

>[AZURE.INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]

>[AZURE.TIP] Vous pouvez utiliser cette connexion à Facebook même dans les autres applications logique.

## <a name="swagger-rest-api-reference"></a>Référence des API REST swagger
S’applique aux versions : 1.0.

### <a name="get-feed-from-my-timeline"></a>Accéder au flux à partir de ma barre de planning
Obtient les flux de la chronologie de l’utilisateur connecté.  
```GET: /me/feed```

| Nom|Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|champs|chaîne|aucune|requête|Aucun |Spécifier les champs à renvoyer. Exemple (id, nom, image).|
|limite|nombre entier|aucune|requête| Aucun|Nombre maximal de billets à récupérer|
|avec|chaîne|aucune|requête| Aucun|Limiter la liste des billets aux personnes ayant un emplacement connecté.|
|filtre|chaîne|aucune|requête| Aucun|Récupérer uniquement les billets qui correspondent à un filtre de flux de données particulière.|

#### <a name="response"></a>Réponse
|Nom|Description|
|---|---|
|200|Bien|
|400|Demande incorrecte|
|500|Erreur interne du serveur|
|par défaut|Échoué de l’opération.|


### <a name="post-to-my-timeline"></a>Publier des billets dans ma barre de planning
Publier un message d’état à la chronologie de l’utilisateur connecté.  
```POST: /me/feed```

| Nom|Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|Publier|chaîne |Oui|corps|Aucun |Nouveau message pour être publié|

#### <a name="response"></a>Réponse
|Nom|Description|
|---|---|
|200|Bien|
|400|Demande incorrecte|
|500|Erreur interne du serveur|
|par défaut|Échoué de l’opération.|


### <a name="when-there-is-a-new-post-on-my-timeline"></a>Lorsqu’il y a un nouveau billet dans ma barre de planning
Déclenche un nouveau flux lorsqu’il y a un nouveau billet sur barre de planning de l’utilisateur connecté.  
```GET: /trigger/me/feed```

Il n’existe pas de paramètres. 

#### <a name="response"></a>Réponse
|Nom|Description|
|---|---|
|200|Bien|
|400|Demande incorrecte|
|500|Erreur interne du serveur|
|par défaut|Échoué de l’opération.|


### <a name="get-page-feed"></a>Obtenir le saut de page
Obtenez des billets du flux d’une page spécifique.  
```GET: /{pageId}/feed```

| Nom|Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|ID de page|chaîne|Oui|chemin d’accès| Aucun|ID de la page à partir de laquelle les publications doivent être récupérées.|
|limite|nombre entier|aucune|requête| Aucun|Nombre maximal de billets à récupérer|
|include_hidden|valeur booléenne|aucune|requête|Aucun |Pour inclure tous les billets qui ont été masquées par la Page ou non|
|champs|chaîne|aucune|requête|Aucun |Spécifier les champs à renvoyer. Exemple (id, nom, image).|

#### <a name="response"></a>Réponse
|Nom|Description|
|---|---|
|200|Bien|
|400|Demande incorrecte|
|500|Erreur interne du serveur|
|par défaut|Échoué de l’opération.|


### <a name="get-user-timeline"></a>Obtenir utilisateur chronologie
Obtenez des billets de barre de planning d’un utilisateur.  
```GET: /{userId}/feed```

| Nom|Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|ID d’utilisateur|chaîne|Oui|chemin d’accès|Aucun |ID de l’utilisateur dont chronologie doivent être extraits.|
|limite|nombre entier|aucune|requête|Aucun |Nombre maximal de billets à récupérer|
|avec|chaîne|aucune|requête|Aucun |Limiter la liste des billets aux personnes ayant un emplacement connecté.|
|filtre|chaîne|aucune|requête| Aucun|Récupérer uniquement les billets qui correspondent à un filtre de flux de données particulière.|
|champs|chaîne|aucune|requête| Aucun|Spécifier les champs à renvoyer. Exemple (id, nom, image).|

#### <a name="response"></a>Réponse
|Nom|Description|
|---|---|
|200|Bien|
|400|Demande incorrecte|
|500|Erreur interne du serveur|
|par défaut|Échoué de l’opération.|


### <a name="post-to-page"></a>Publier des billets dans la page
Publier un message dans une Facebook Page en tant que l’utilisateur connecté.  
```POST: /{pageId}/feed```

| Nom|Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|ID de page|chaîne|Oui|chemin d’accès|Aucun |ID de la page pour publier.|
|Publier|nombre |Oui|corps|Aucun |Nouveau message à valider.|

#### <a name="response"></a>Réponse
|Nom|Description|
|---|---|
|200|Bien|
|400|Demande incorrecte|
|500|Erreur interne du serveur|
|par défaut|Échoué de l’opération.|


## <a name="object-definitions"></a>Définitions d’objets

#### <a name="getfeedresponse"></a>GetFeedResponse

|Nom de la propriété | Type de données | Obligatoire|
|---|---|---|
|données|tableau|aucune|

#### <a name="triggerfeedresponse"></a>TriggerFeedResponse

|Nom de la propriété | Type de données |Obligatoire|
|---|---|---|
|données|tableau|aucune|

#### <a name="postitem-a-single-entry-in-a-profiles-feed"></a>PostItem : Une seule entrée dans un profil de flux
Le profil peut être un utilisateur, page, application ou groupe. 

|Nom de la propriété | Type de données |Obligatoire|
|---|---|---|
|ID|chaîne|aucune|
|admin_creator|tableau|aucune|
|légende|chaîne|aucune|
|created_time|chaîne|aucune|
|Description|chaîne|aucune|
|feed_targeting|non défini|aucune|
|De|non défini|aucune|
|icône|chaîne|aucune|
|is_hidden|valeur booléenne|aucune|
|is_published|valeur booléenne|aucune|
|lien|chaîne|aucune|
|Message|chaîne|aucune|
|nom|chaîne|aucune|
|object_id|chaîne|aucune|
|image|chaîne|aucune|
|emplacement|non défini|aucune|
|confidentialité|non défini|aucune|
|propriétés|tableau|aucune|
|source|chaîne|aucune|
|status_type|chaîne|aucune|
|article|chaîne|aucune|
|ciblage|non défini|aucune|
|À|tableau|aucune|
|type|chaîne|aucune|
|updated_time|chaîne|aucune|
|with_tags|non défini|aucune|

#### <a name="triggeritem-a-single-entry-in-a-profiles-feed"></a>TriggerItem : Une seule entrée dans un profil de flux
Le profil peut être un utilisateur, page, application ou groupe.

|Nom de la propriété | Type de données |Obligatoire|
|---|---|---|
|ID|chaîne|aucune|
|created_time|chaîne|aucune|
|De|non défini|aucune|
|Message|chaîne|aucune|
|type|chaîne|aucune|

#### <a name="adminitem"></a>AdminItem

|Nom de la propriété | Type de données |Obligatoire|
|---|---|---|
|ID|chaîne|aucune|
|lien|chaîne|aucune|

#### <a name="propertyitem"></a>PropertyItem

|Nom de la propriété | Type de données |Obligatoire|
|---|---|---|
|nom|chaîne|aucune|
|texte|chaîne|aucune|
|href|chaîne|aucune|

#### <a name="userpostfeedrequest"></a>UserPostFeedRequest

|Nom de la propriété | Type de données |Obligatoire|
|---|---|---|
|Message|chaîne|Oui|
|lien|chaîne|aucune|
|image|chaîne|aucune|
|nom|chaîne|aucune|
|légende|chaîne|aucune|
|Description|chaîne|aucune|
|emplacement|chaîne|aucune|
|balises|chaîne|aucune|
|confidentialité|non défini|aucune|
|object_attachment|chaîne|aucune|

#### <a name="pagepostfeedrequest"></a>PagePostFeedRequest

|Nom de la propriété | Type de données |Obligatoire|
|---|---|---|
|Message|chaîne|Oui|
|lien|chaîne|aucune|
|image|chaîne|aucune|
|nom|chaîne|aucune|
|légende|chaîne|aucune|
|Description|chaîne|aucune|
|actions|tableau|aucune|
|emplacement|chaîne|aucune|
|balises|chaîne|aucune|
|object_attachment|chaîne|aucune|
|ciblage|non défini|aucune|
|feed_targeting|non défini|aucune|
|publié|valeur booléenne|aucune|
|scheduled_publish_time|chaîne|aucune|
|backdated_time|chaîne|aucune|
|backdated_time_granularity|chaîne|aucune|
|child_attachments|tableau|aucune|
|multi_share_end_card|valeur booléenne|aucune|

#### <a name="postfeedresponse"></a>PostFeedResponse

|Nom de la propriété | Type de données |Obligatoire|
|---|---|---|
|ID|chaîne|aucune|

#### <a name="profilecollection"></a>ProfileCollection

|Nom de la propriété | Type de données |Obligatoire|
|---|---|---|
|données|tableau|aucune|

#### <a name="useritem"></a>UserItem

|Nom de la propriété | Type de données |Obligatoire|
|---|---|---|
|ID|chaîne|aucune|
|Prénom|chaîne|aucune|
|nom|chaîne|aucune|
|nom|chaîne|aucune|
|sexe|chaîne|aucune|
|à propos de|chaîne|aucune|

#### <a name="actionitem"></a>ActionItem

|Nom de la propriété | Type de données |Obligatoire|
|---|---|---|
|nom|chaîne|aucune|
|lien|chaîne|aucune|

#### <a name="targetitem"></a>TargetItem

|Nom de la propriété | Type de données |Obligatoire|
|---|---|---|
|pays|tableau|aucune|
|paramètres régionaux|tableau|aucune|
|régions|tableau|aucune|
|villes|tableau|aucune|

#### <a name="feedtargetitem-object-that-controls-news-feed-targeting-for-this-post"></a>FeedTargetItem : Objet contrôlant news flux ciblage pour ce billet
Tout le monde à ces groupes est censée voir ce billet, d’autres personnes sont rarement. S’applique uniquement aux Pages.

|Nom de la propriété | Type de données |Obligatoire|
|---|---|---|
|pays|tableau|aucune|
|régions|tableau|aucune|
|villes|tableau|aucune|
|age_min|nombre entier|aucune|
|age_max|nombre entier|aucune|
|des deux sexes|tableau|aucune|
|relationship_statuses|tableau|aucune|
|interested_in|tableau|aucune|
|college_years|tableau|aucune|
|centres d’intérêt|tableau|aucune|
|relevant_until|nombre entier|aucune|
|education_statuses|tableau|aucune|
|paramètres régionaux|tableau|aucune|

#### <a name="placeitem"></a>PlaceItem

|Nom de la propriété | Type de données |Obligatoire|
|---|---|---|
|ID|chaîne|aucune|
|nom|chaîne|aucune|
|overall_rating|nombre|aucune|
|emplacement|non défini|aucune|

#### <a name="locationitem"></a>LocationItem

|Nom de la propriété | Type de données |Obligatoire|
|---|---|---|
|Ville|chaîne|aucune|
|pays|chaîne|aucune|
|Latitude|nombre|aucune|
|located_in|chaîne|aucune|
|longitude|nombre|aucune|
|nom|chaîne|aucune|
|région|chaîne|aucune|
|état|chaîne|aucune|
|rue|chaîne|aucune|
|ZIP|chaîne|aucune|

#### <a name="privacyitem"></a>PrivacyItem

|Nom de la propriété | Type de données |Obligatoire|
|---|---|---|
|Description|chaîne|aucune|
|valeur|chaîne|Oui|
|Autoriser|chaîne|aucune|
|refuser|chaîne|aucune|
|amis|chaîne|aucune|

#### <a name="childattachmentsitem"></a>ChildAttachmentsItem

|Nom de la propriété | Type de données |Obligatoire|
|---|---|---|
|lien|chaîne|aucune|
|image|chaîne|aucune|
|image_hash|chaîne|aucune|
|nom|chaîne|aucune|
|Description|chaîne|aucune|

#### <a name="postphotorequest"></a>PostPhotoRequest

|Nom de la propriété | Type de données |Obligatoire|
|---|---|---|
|URL|chaîne|Oui|
|légende|chaîne|aucune|

#### <a name="postphotoresponse"></a>PostPhotoResponse

|Nom de la propriété | Type de données |Obligatoire|
|---|---|---|
|ID|chaîne|Oui|
|post_id|chaîne|Oui|

#### <a name="postvideorequest"></a>PostVideoRequest

|Nom de la propriété | Type de données |Obligatoire|
|---|---|---|
|videoData|chaîne|Oui|
|Description|chaîne|Oui|
|titre|chaîne|Oui|
|uploadedVideoName|chaîne|aucune|

#### <a name="getphotoresponse"></a>GetPhotoResponse

|Nom de la propriété | Type de données |Obligatoire|
|---|---|---|
|données|non défini|Oui|

#### <a name="getphotoresponseitem"></a>GetPhotoResponseItem

|Nom de la propriété | Type de données |Obligatoire|
|---|---|---|
|URL|chaîne|Oui|
|is_silhouette|valeur booléenne|Oui|
|hauteur|chaîne|aucune|
|Largeur|chaîne|aucune|

#### <a name="geteventresponse"></a>GetEventResponse

|Nom de la propriété | Type de données |Obligatoire|
|---|---|---|
|données|tableau|Oui|

#### <a name="geteventresponseitem"></a>GetEventResponseItem

|Nom de la propriété | Type de données |Obligatoire|
|---|---|---|
|ID|chaîne|Oui|
|nom|chaîne|Oui|
|start_time|chaîne|aucune|
|end_time|chaîne|aucune|
|fuseau horaire|chaîne|aucune|
|emplacement|chaîne|aucune|
|Description|chaîne|aucune|
|ticket_uri|chaîne|aucune|
|rsvp_status|chaîne|Oui|


## <a name="next-steps"></a>Étapes suivantes

[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).
