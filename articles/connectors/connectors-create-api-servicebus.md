<properties
pageTitle="Découvrez comment utiliser le connecteur Azure Service dans vos applications logique | Microsoft Azure"
description="Créez des applications de logique avec le service d’application Azure. Se connecter à Bus des services Azure pour envoyer et recevoir des messages. Vous pouvez effectuer des actions telles que l’envoi en file d’attente, envoyer à la rubrique, de file d’attente, des reçoive d’abonnement."
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
ms.date="08/02/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-azure-service-bus-connector"></a>Prise en main du connecteur Azure Service

Se connecter à Bus des services Azure pour envoyer et recevoir des messages. Vous pouvez effectuer des actions telles que l’envoi en file d’attente, envoyer à la rubrique, de file d’attente, des reçoive d’abonnement.

Pour utiliser [un connecteur quelconque](./apis-list.md), vous devez tout d’abord créer une application logique. Vous pouvez commencer par [créer une application logique maintenant](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="connect-to-service-bus"></a>Se connecter au Service Bus

Avant que votre application logique peut accéder à n’importe quel service, vous devez tout d’abord créer une connexion au service. Une [connexion](./connectors-overview.md) assure la connectivité entre une application logique et un autre service.  

>[AZURE.INCLUDE [Steps to create a connection to Azure Service Bus](../../includes/connectors-create-api-servicebus.md)]

## <a name="use-a-service-bus-trigger"></a>Utilisez un déclencheur Bus des services

Un déclencheur est un événement qui peut être utilisé pour démarrer le flux de travail définie dans une application logique. [En savoir plus sur les déclencheurs](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).  

>[AZURE.INCLUDE [Steps to create a Service Bus trigger](../../includes/connectors-create-api-servicebus-trigger.md)]  

## <a name="use-a-service-bus-action"></a>Utiliser une action Bus des services

Une action est une opération effectuée par le flux de travail définie dans une application logique. [En savoir plus sur les actions](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

[AZURE.INCLUDE [Steps to create a Service Bus action](../../includes/connectors-create-api-servicebus-action.md)]  

## <a name="technical-details"></a>Détails techniques

Voici les informations concernant les déclencheurs, les actions et les réponses qui prend en charge cette connexion.

### <a name="service-bus-triggers"></a>Déclencheurs Bus des services

Service Bus contient les déclencheurs suivants :  

|Déclencheur | Description|
|--- | ---|
|[Lorsqu’un message est reçu dans une file d’attente](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-queue)|Cette opération déclenche un flux lors de la réception d’un message dans une file d’attente.|
|[Lorsqu’un message est reçu dans un abonnement rubrique](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-topic-subscription)|Cette opération déclenche un flux lors de la réception d’un message dans un abonnement rubrique.|


### <a name="service-bus-actions"></a>Actions de Bus des services

Service Bus contient les actions suivantes :


|Action|Description|
|--- | ---|
|[Envoyer des messages](connectors-create-api-servicebus.md#send-message)|Cette opération envoie un message à un file d’attente ou une rubrique.|
### <a name="action-and-trigger-details"></a>Détails de l’action et le déclencheur

Voici les détails des actions et déclencheurs pour ce connecteur, ainsi que leurs réponses.



#### <a name="send-message"></a>Envoyer des messages

|Nom de la propriété| Nom d’affichage|Description|
| ---|---|---|
|ContentData *|Contenu|Contenu du message.|
|ContentType|Type de contenu|Type de contenu du contenu du message.|
|Propriétés|Propriétés|Paires clé-valeur pour chaque demandé de propriété.|
|entityName *|Nom de la rubrique/file d’attente|Nom de la file d’attente ou une rubrique.|

Ces paramètres avancés sont également disponibles :

|Nom de la propriété| Nom d’affichage|Description|
| ---|---|---|
|MessageId|Id de message|Valeur définie par l’utilisateur qui Bus des services peuvent utiliser pour identifier les messages en double, si activé.|
|À|À|Adresse de destination.|
|ReplyTo|Répondre à|Adresse de la file d’attente pour répondre à.|
|ReplyToSessionId|Réponse à l’Id de Session|Identificateur de la session pour répondre à.|
|Étiquette|Étiquette|Étiquette spécifiques à l’application.|
|ScheduledEnqueueTimeUtc|ScheduledEnqueueTimeUtc|Date et heure, au format UTC, lorsque le message est ajouté à la file d’attente.|
|ID de session|Id de session|Identificateur de la session.|
|ID de corrélation|Id de corrélation|Identificateur de la corrélation.|
|Propriété TimeToLive|Durée de vie|La durée, en graduations, qu’un message est valide. La durée commence lorsque le message est envoyé au Service Bus.|



Un * indique qu’une propriété est requise.


#### <a name="when-a-message-is-received-in-a-queue"></a>Lorsqu’un message est reçu dans une file d’attente

|Nom de la propriété| Nom d’affichage|Description|
| ---|---|---|
|Queue *|Nom de la file d’attente|Nom de la file d’attente.|


Un * indique qu’une propriété est requise.


##### <a name="output-details"></a>Détails de sortie

ServiceBusMessage : Cet objet comporte le contenu et les propriétés d’un message Bus des services.


| Nom de la propriété | Type de données | Description |
|---|---|---|
|ContentData|chaîne|Contenu du message.|
|ContentType|chaîne|Type de contenu du contenu du message.|
|Propriétés|objet|Paires clé-valeur pour chaque demandé de propriété.|
|MessageId|chaîne|Valeur définie par l’utilisateur qui Bus des services peuvent utiliser pour identifier les messages en double, si activé.|
|À|chaîne|Envoyer vers l’adresse.|
|ReplyTo|chaîne|Adresse de la file d’attente pour répondre à.|
|ReplyToSessionId|chaîne|Identificateur de la session pour répondre à.|
|Étiquette|chaîne|Étiquette spécifiques à l’application.|
|ScheduledEnqueueTimeUtc|chaîne|Date et heure, au format UTC, lorsque le message est ajouté à la file d’attente.|
|ID de session|chaîne|Identificateur de la session.|
|ID de corrélation|chaîne|Identificateur de la corrélation.|
|Propriété TimeToLive|chaîne|La durée, en graduations, qu’un message est valide. La durée commence lorsque le message est envoyé au Service Bus.|




#### <a name="when-a-message-is-received-in-a-topic-subscription"></a>Lorsqu’un message est reçu dans un abonnement rubrique

|Nom de la propriété| Nom d’affichage|Description|
| ---|---|---|
|topicName *|Nom de la rubrique|Nom de la rubrique.|
|subscriptionName *|Nom de l’abonnement rubrique|Nom de l’abonnement rubrique.|


Un * indique qu’une propriété est requise.


##### <a name="output-details"></a>Détails de sortie

ServiceBusMessage : Cet objet comporte le contenu et les propriétés d’un message Bus des services.


| Nom de la propriété | Type de données | Description |
|---|---|---|
|ContentData|chaîne|Contenu du message.|
|ContentType|chaîne|Type de contenu du contenu du message.|
|Propriétés|objet|Paires clé-valeur pour chaque demandé de propriété.|
|MessageId|chaîne|Valeur définie par l’utilisateur qui Bus des services peuvent utiliser pour identifier les messages en double, si activé.|
|À|chaîne|Envoyer vers l’adresse.|
|ReplyTo|chaîne|Adresse de la file d’attente pour répondre à.|
|ReplyToSessionId|chaîne|Identificateur de la session pour répondre à.|
|Étiquette|chaîne|Étiquette spécifiques à l’application.|
|ScheduledEnqueueTimeUtc|chaîne|Date et heure, au format UTC, lorsque le message est ajouté à la file d’attente.|
|ID de session|chaîne|Identificateur de la session.|
|ID de corrélation|chaîne|Identificateur de la corrélation.|
|Propriété TimeToLive|chaîne|La durée, en graduations, qu’un message est valide. La durée commence lorsque le message est envoyé au Service Bus.|



### <a name="http-responses"></a>Réponses HTTP

Actions et déclencheurs précédent peuvent renvoyer une ou plusieurs des codes d’état HTTP suivants :

|Nom|Description|
|---|---|
|200|Bien|
|202|Accepté|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|500|Erreur interne du serveur. Erreur inconnue s’est produite.|
|par défaut|Échoué de l’opération.|

## <a name="next-steps"></a>Étapes suivantes
[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).
