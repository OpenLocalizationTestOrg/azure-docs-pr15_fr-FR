<properties
pageTitle="SendGrid | Microsoft Azure"
description="Créez des applications de logique avec le service d’application Azure. Fournisseur de connexion SendGrid vous permet d’envoyer des messages électroniques et gérer des listes de destinataires."
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

# <a name="get-started-with-the-sendgrid-connector"></a>Prise en main du connecteur SendGrid

Fournisseur de connexion SendGrid vous permet d’envoyer des messages électroniques et gérer des listes de destinataires.

>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma logique applications 2015-08-01-aperçu. 

Vous pouvez commencer par créer une application logique maintenant, voir [créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Déclencheurs et actions

Le connecteur SendGrid peut être utilisé comme une action ; Il a déclencheurs. Tous les connecteurs prennent en charge les données aux formats JSON et XML. 

 Le connecteur SendGrid comporte les actions suivantes disponibles. Il n’existe aucun déclencheur.

### <a name="sendgrid-actions"></a>Actions SendGrid
Vous pouvez suivre ces actions :

|Action|Description|
|--- | ---|
|[SendEmail](connectors-create-api-sendgrid.md#sendemail)|Envoie un message électronique à l’aide de l’API SendGrid (limité à 10 000 destinataires)|
|[AddRecipientToList](connectors-create-api-sendgrid.md#addrecipienttolist)|Ajouter un destinataire individuel à une liste de destinataires|


## <a name="create-a-connection-to-sendgrid"></a>Créer une connexion à SendGrid
Pour créer des applications logique avec SendGrid, vous devez d’abord créer une **connexion** puis fournir les détails pour les propriétés suivantes : 

|Propriété| Obligatoire|Description|
| ---|---|---|
|ApiKey|Oui|Indiquez votre clé de l’Api SendGrid|
 

>[AZURE.INCLUDE [Steps to create a connection to SendGrid](../../includes/connectors-create-api-sendgrid.md)]

>[AZURE.TIP] Vous pouvez utiliser cette connexion dans les autres applications logique.

Après avoir créé la connexion, vous pouvez l’utiliser pour exécuter les actions et écouter pour les déclencheurs décrites dans cet article.

## <a name="reference-for-sendgrid"></a>Référence pour SendGrid
S’applique à la version : 1.0

## <a name="sendemail"></a>SendEmail
Envoyer un message électronique : envoie un message électronique à l’aide de l’API SendGrid (limité à 10 000 destinataires) 

```POST: /api/mail.send.json``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|demande| |Oui|corps|Aucun|Message électronique à envoyer|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|429|Trop de demande|
|500|Erreur interne du serveur. Erreur inconnue|
|par défaut|Échoué de l’opération.|


## <a name="addrecipienttolist"></a>AddRecipientToList
Ajouter un destinataire à la liste : ajouter un destinataire individuel à une liste de destinataires 

```POST: /v3/contactdb/lists/{listId}/recipients/{recipientId}``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|listId|chaîne|Oui|chemin d’accès|Aucun|Nº unique de la liste de destinataires|
|recipientId|chaîne|Oui|chemin d’accès|Aucun|Nº unique du destinataire|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|500|Erreur interne du serveur. Erreur inconnue|
|par défaut|Échoué de l’opération.|


## <a name="object-definitions"></a>Définitions d’objets 

### <a name="emailrequest"></a>EmailRequest


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|De|chaîne|Oui |
|FromName|chaîne|N° |
|À|chaîne|Oui |
|nomà|chaîne|N° |
|Objet|chaîne|Oui |
|corps|chaîne|Oui |
|ishtml|valeur booléenne|N° |
|cc|chaîne|N° |
|ccname|chaîne|N° |
|Cci|chaîne|N° |
|bccname|chaîne|N° |
|ReplyTo|chaîne|N° |
|date|chaîne|N° |
|en-têtes|chaîne|N° |
|fichiers|tableau|N° |
|noms de fichiers|tableau|N° |



### <a name="emailresponse"></a>EmailResponse


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|Message|chaîne|N° |



### <a name="recipientlists"></a>RecipientLists


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|listes|tableau|N° |



### <a name="recipientlist"></a>RecipientList


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|ID|nombre entier|N° |
|nom|chaîne|N° |
|recipient_count|nombre entier|N° |



### <a name="recipients"></a>Destinataires


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|destinataires|tableau|N° |



### <a name="recipient"></a>Destinataire


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|Messagerie|chaîne|N° |
|nom|chaîne|N° |
|Prénom|chaîne|N° |
|ID|chaîne|N° |


## <a name="next-steps"></a>Étapes suivantes
[Créer une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md)