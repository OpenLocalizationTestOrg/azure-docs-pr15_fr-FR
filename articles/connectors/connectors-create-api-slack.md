<properties
pageTitle=" Utiliser le connecteur marge dans vos applications logique | Microsoft Azure"
description="Commencer à utiliser le connecteur de marge dans vos applications Microsoft Azure Application Service logique"
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

# <a name="get-started-with-the-slack-connector"></a>Prise en main du connecteur marge

La marge est un outil de communication d’équipe, qui regroupe toutes les communications de votre équipe dans une placent instantanément disponible pour la recherche et disponible à l’endroit où vous allez.

>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma logique applications 2015-08-01-aperçu.

Avec le lien marge, vous pouvez :

* Utilisez-le pour créer des applications de logique

Pour ajouter une opération dans les applications logique, voir [créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="lets-talk-about-triggers-and-actions"></a>À propos des déclencheurs et actions

Le connecteur marge peut être utilisé comme action ; Il n’existe aucun déclencheur. Tous les connecteurs prennent en charge les données aux formats JSON et XML. 

 Le connecteur marge comporte les déclencheurs disponibles et/ou une ou des actions suivantes :

### <a name="slack-actions"></a>Actions de marge
Vous pouvez suivre ces actions :

|Action|Description|
|--- | ---|
|PostMessage|Publier un Message sur un canal spécifié.|
## <a name="create-a-connection-to-slack"></a>Créer une connexion à la marge
Pour utiliser le connecteur marge, vous devez d’abord créez une **connexion** puis fournissez les détails de ces propriétés : 

|Propriété| Obligatoire|Description|
| ---|---|---|
|Jetons|Oui|Fournir des informations d’identification de marges|

Suivez ces étapes pour vous connecter à la marge et terminer la configuration de la **connexion** de marge dans votre application logique :

1. Sélectionnez la **périodicité**
2. Sélectionnez une **fréquence** , entrez un **intervalle**
3. Sélectionnez **Ajouter une action**  
![Configurer la marge][1]  
4. Entrez la marge dans la zone de recherche et attendre la recherche renvoyer toutes les entrées dont la marge dans le nom
5. Sélectionnez **marge - publier le message**
6. Sélectionnez **se connecter à marge**:  
![Configurer la marge][2]
7. Fournir vos informations d’identification marges pour vous connecter autoriser l’application    
![Configurer la marge][3]  
8. Vous serez redirigé vers la page d’accès de votre organisation. **Autorisez les** Marge pour interagir avec votre application logique :      
![Configurer la marge][5] 
9. Une fois terminée l’autorisation vous serez redirigé vers votre application logique à effectuer en configurant la section **marge - obtenir tous les messages** . Ajouter d’autres déclencheurs et les actions que vous avez besoin.  
![Configurer la marge][6]
10. Enregistrez votre travail en sélectionnant **Enregistrer** dans la barre de menus ci-dessus.


>[AZURE.TIP] Vous pouvez utiliser cette connexion dans les autres applications logique.

## <a name="slack-rest-api-reference"></a>Marge référence API REST
#### <a name="this-documentation-is-for-version-10"></a>Cette documentation est de version : 1.0


### <a name="post-a-message-to-a-specified-channel"></a>Publier un Message sur un canal spécifié.
**```POST: /chat.postMessage```** 



| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|canal|chaîne|Oui|requête|Aucun|Canal, groupe privé ou canal de messagerie instantanée pour envoyer le message. Peut être un nom (ex : #general) ou un code codé.|
|texte|chaîne|Oui|requête|Aucun|Texte du message à envoyer. Pour les options de mise en forme, voir https://api.slack.com/docs/formatting.|
|nom d’utilisateur|chaîne|aucune|requête|Aucun|Nom de la robot|
|as_user|valeur booléenne|aucune|requête|Aucun|Passer la valeur true pour publier le message en tant que l’utilisateur authentifié, plutôt que d’en robot|
|analyser|chaîne|aucune|requête|Aucun|Modifier la façon dont les messages sont traités. Pour plus d’informations, voir https://api.slack.com/docs/formatting.|
|link_names|nombre entier|aucune|requête|Aucun|Rechercher et lier des noms de canaux et des noms d’utilisateur.|
|unfurl_links|valeur booléenne|aucune|requête|Aucun|Passer la valeur True Activer ferlage de principalement basées sur texte contenu.|
|unfurl_media|valeur booléenne|aucune|requête|Aucun|Passez false pour désactiver ferlage du contenu multimédia.|
|icon_url|chaîne|aucune|requête|Aucun|URL d’une image à utiliser comme une icône pour ce message|
|icon_emoji|chaîne|aucune|requête|Aucun|Emoji à utiliser comme une icône pour ce message|


### <a name="here-are-the-possible-responses"></a>Voici les réponses possibles :

|Nom|Description|
|---|---|
|200|Bien|
|400|Demande incorrecte|
|408|Délai de requête|
|429|Trop de demandes|
|500|Erreur interne du serveur. Erreur inconnue|
|503|Marge Service non disponible|
|504|Délai de la passerelle|
|par défaut|Échoué de l’opération.|
------



## <a name="object-definitions"></a>Ou les définitions d’objet : 

 **Message**: Message de Yammer

Propriétés requises pour le Message :


Aucune des propriétés sont nécessaires. 


**Toutes les propriétés**: 


| Nom | Type de données |
|---|---|
|ID|nombre entier|
|content_excerpt|chaîne|
|sender_id|nombre entier|
|replied_to_id|nombre entier|
|created_at|chaîne|
|network_id|nombre entier|
|message_type|chaîne|
|sender_type|chaîne|
|URL|chaîne|
|web_url|chaîne|
|group_id|nombre entier|
|corps|non défini|
|thread_id|nombre entier|
|direct_message|valeur booléenne|
|client_type|chaîne|
|client_url|chaîne|
|langue|chaîne|
|notified_user_ids|tableau|
|confidentialité|chaîne|
|liked_by|non défini|
|system_message|valeur booléenne|



 **PostOperationRequest**: représente une demande de publication pour connecteur Yammer publier sur yammer

Propriétés requises pour PostOperationRequest :

corps

**Toutes les propriétés**: 


| Nom | Type de données |
|---|---|
|corps|chaîne|
|group_id|nombre entier|
|replied_to_id|nombre entier|
|direct_to_id|nombre entier|
|diffusion|valeur booléenne|
|sujet1|chaîne|
|sujet2|chaîne|
|topic3|chaîne|
|topic4|chaîne|
|topic5|chaîne|
|topic6|chaîne|
|topic7|chaîne|
|topic8|chaîne|
|topic9|chaîne|
|rubrique10|chaîne|
|rubrique11|chaîne|
|topic12|chaîne|
|topic13|chaîne|
|topic14|chaîne|
|topic15|chaîne|
|topic16|chaîne|
|rubrique17|chaîne|
|rubrique18|chaîne|
|rubrique19|chaîne|
|rubrique20|chaîne|



 **MessageList**: liste de messages

Propriétés requises pour MessageList :


Aucune des propriétés sont nécessaires. 


**Toutes les propriétés**: 


| Nom | Type de données |
|---|---|
|messages|tableau|



 **L’élément MessageBody**: corps du Message

Propriétés requises pour l’élément MessageBody :


Aucune des propriétés sont nécessaires. 


**Toutes les propriétés**: 


| Nom | Type de données |
|---|---|
|analysés|chaîne|
|brut|chaîne|
|enrichi|chaîne|



 **LikedBy**: aime par

Propriétés requises pour LikedBy :


Aucune des propriétés sont nécessaires. 


**Toutes les propriétés**: 


| Nom | Type de données |
|---|---|
|nombre|nombre entier|
|noms|tableau|



 **YammmerEntity**: aime par

Propriétés requises pour YammmerEntity :


Aucune des propriétés sont nécessaires. 


**Toutes les propriétés**: 


| Nom | Type de données |
|---|---|
|type|chaîne|
|ID|nombre entier|
|full_name|chaîne|


## <a name="next-steps"></a>Étapes suivantes
[Créer une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md)
## <a name="object-definitions"></a>Ou les définitions d’objet : 

 **WebResultModel**: les résultats de recherche Bing

Propriétés requises pour WebResultModel :


Aucune des propriétés sont nécessaires. 


**Toutes les propriétés**: 


| Nom | Type de données |
|---|---|
|Titre|chaîne|
|Description|chaîne|
|DisplayUrl|chaîne|
|ID|chaîne|
|FullUrl|chaîne|



 **VideoResultModel**: résultats vidéo Bing

Propriétés requises pour VideoResultModel :


Aucune des propriétés sont nécessaires. 


**Toutes les propriétés**: 


| Nom | Type de données |
|---|---|
|Titre|chaîne|
|DisplayUrl|chaîne|
|ID|chaîne|
|MediaUrl|chaîne|
|Runtime|nombre entier|
|Miniature|non défini|



 **ThumbnailModel**: propriétés miniature de l’élément multimédia

Propriétés requises pour ThumbnailModel :


Aucune des propriétés sont nécessaires. 


**Toutes les propriétés**: 


| Nom | Type de données |
|---|---|
|MediaUrl|chaîne|
|ContentType|chaîne|
|Largeur|nombre entier|
|Hauteur|nombre entier|
|Taille du fichier|nombre entier|



 **ImageResultModel**: résultats de la recherche Bing images

Propriétés requises pour ImageResultModel :


Aucune des propriétés sont nécessaires. 


**Toutes les propriétés**: 


| Nom | Type de données |
|---|---|
|Titre|chaîne|
|DisplayUrl|chaîne|
|ID|chaîne|
|MediaUrl|chaîne|
|SourceUrl|chaîne|
|Miniature|non défini|



 **NewsResultModel**: résultats de la recherche Bing news

Propriétés requises pour NewsResultModel :


Aucune des propriétés sont nécessaires. 


**Toutes les propriétés**: 


| Nom | Type de données |
|---|---|
|Titre|chaîne|
|Description|chaîne|
|DisplayUrl|chaîne|
|ID|chaîne|
|Source|chaîne|
|Date|chaîne|



 **SpellResultModel**: résultats des suggestions orthographiques Bing

Propriétés requises pour SpellResultModel :


Aucune des propriétés sont nécessaires. 


**Toutes les propriétés**: 


| Nom | Type de données |
|---|---|
|ID|chaîne|
|Valeur|chaîne|



 **RelatedSearchResultModel**: Bing liés des résultats de recherche

Propriétés requises pour RelatedSearchResultModel :


Aucune des propriétés sont nécessaires. 


**Toutes les propriétés**: 


| Nom | Type de données |
|---|---|
|Titre|chaîne|
|ID|chaîne|
|BingUrl|chaîne|



 **CompositeSearchResultModel**: résultats de la recherche composite Bing

Propriétés requises pour CompositeSearchResultModel :


Aucune des propriétés sont nécessaires. 


**Toutes les propriétés**: 


| Nom | Type de données |
|---|---|
|WebResultsTotal|nombre entier|
|ImageResultsTotal|nombre entier|
|VideoResultsTotal|nombre entier|
|NewsResultsTotal|nombre entier|
|SpellSuggestionsTotal|nombre entier|
|WebResults|tableau|
|ImageResults|tableau|
|VideoResults|tableau|
|NewsResults|tableau|
|SpellSuggestionResults|tableau|
|RelatedSearchResults|tableau|


## <a name="object-definitions"></a>Ou les définitions d’objet : 

 **PostOperationResponse**: représente réponse opération post du connecteur marge pour la validation marge

Propriétés requises pour PostOperationResponse :


Aucune des propriétés sont nécessaires. 


**Toutes les propriétés**: 


| Nom | Type de données |
|---|---|
|Bien|valeur booléenne|
|canal|chaîne|
|TS|chaîne|
|Message|non défini|
|erreur|chaîne|



 **MessageItem ne**: un message de canal.

Propriétés requises pour MessageItem ne :


Aucune des propriétés sont nécessaires. 


**Toutes les propriétés**: 


| Nom | Type de données |
|---|---|
|texte|chaîne|
|ID|chaîne|
|utilisateur|chaîne|
|créé|nombre entier|
|supprimés is_user|valeur booléenne|


## <a name="next-steps"></a>Étapes suivantes
[Créer une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md)

[1]: ./media/connectors-create-api-slack/connectionconfig1.png
[2]: ./media/connectors-create-api-slack/connectionconfig2.png 
[3]: ./media/connectors-create-api-slack/connectionconfig3.png
[4]: ./media/connectors-create-api-slack/connectionconfig4.png
[5]: ./media/connectors-create-api-slack/connectionconfig5.png
[6]: ./media/connectors-create-api-slack/connectionconfig6.png
