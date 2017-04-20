<properties
    pageTitle="Utiliser les actions de demande et réponse | Microsoft Azure"
    description="Vue d’ensemble de la demande et réponse déclencheur et une action dans une application logique Azure"
    services=""
    documentationCenter=""
    authors="jeffhollan"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# <a name="get-started-with-the-request-and-response-components"></a>Commencer à utiliser les composants de demande et réponse

Avec les composants de demande et la réponse dans une application logique, vous pouvez répondre aux événements en temps réel.

Par exemple, vous pouvez :

- Répondre à une demande HTTP avec des données à partir d’une base de données locale à une application logique.
- Déclencher la lecture d’une application logique à partir d’un événement webhook externes.
- Appeler une application logique avec une action demande et réponse à partir d’une autre application logique.

Pour commencer à utiliser les actions de demande et la réponse dans une application logique, voir [créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="use-the-http-request-trigger"></a>Utiliser le déclencheur demande HTTP

Un déclencheur est un événement qui peut être utilisé pour démarrer le flux de travail qui est défini dans une application logique. [En savoir plus sur les déclencheurs](connectors-overview.md).

Voici un exemple de séquence de la configuration d’une requête HTTP dans le Concepteur d’application logique.

1. Ajouter le déclencheur **- demande HTTP un lors de la demande** dans votre application logique. Vous pouvez éventuellement fournir un schéma JSON (en utilisant un outil comme [JSONSchema.net](http://jsonschema.net)) pour le corps de la requête. Cela permet au concepteur générer des jetons pour les propriétés de la requête HTTP.
2. Ajouter une autre action afin que vous pouvez enregistrer l’application logique.
3. Après avoir enregistré l’application logique, vous pouvez obtenir l’URL de requête HTTP à partir de la carte de la demande.
4. Une publication HTTP (vous pouvez utiliser un outil comme [Postman](https://www.getpostman.com/)) à l’URL de déclencher l’application logique.

>[AZURE.NOTE] Si vous ne définissez pas une action de réponse, un `202 ACCEPTED` réponse est renvoyée immédiatement à l’appelant. Vous pouvez utiliser l’action de réponse pour personnaliser une réponse.

![Déclenchement de réponse](./media/connectors-native-reqres/using-trigger.png)

## <a name="use-the-http-response-action"></a>Utiliser l’action de réponse HTTP

L’action de réponse HTTP est valide uniquement lorsque vous l’utilisez dans un flux de travail qui est déclenchée par une requête HTTP. Si vous ne définissez pas une action de réponse, un `202 ACCEPTED` réponse est renvoyée immédiatement à l’appelant.  Vous pouvez ajouter une action de réponse à une étape dans le flux de travail. L’application logique conserve uniquement la demande entrante ouverte pendant une minute pour une réponse.  Une fois la minute, si aucune réponse n’a été envoyé à partir du flux de travail (et une action de réponse existe dans la définition), un `504 GATEWAY TIMEOUT` est renvoyé à l’appelant.

Voici comment procéder pour ajouter une action de réponse HTTP :

1. Sélectionnez le bouton **Nouvelle étape** .
2. Cliquez sur **Ajouter une action**.
3. Dans la zone de recherche action, tapez la **réponse** pour l’action de réponse de la liste.

    ![Sélectionnez l’action de réponse](./media/connectors-native-reqres/using-action-1.png)

4. Ajouter tous les paramètres requis pour le message de réponse HTTP.

    ![Effectuer l’action de réponse](./media/connectors-native-reqres/using-action-2.png)

5. Cliquez sur le coin supérieur gauche de la barre d’outils pour enregistrer et votre application logique sera à la fois enregistrer et publier (activer).

## <a name="request-trigger"></a>Demande de déclencheur

Voici les détails du déclencheur qui prend en charge ce connecteur. Il existe un déclencheur demande unique.

|Déclencheur|Description|
|---|---|
|Demande|Se produit lors de la réception d’une requête HTTP|

## <a name="response-action"></a>Action de réponse

Voici les détails de l’action qui prend en charge ce connecteur. Il existe une action de réponse unique qui ne peut être utilisée lorsqu’elle est accompagnée d’un déclencheur demande.

|Action|Description|
|---|---|
|Réponse|Renvoie une réponse à la demande HTTP en corrélation|

### <a name="trigger-and-action-details"></a>Détails de déclencheur et une action

Les tableaux suivants décrivent les champs d’entrée pour l’action et le déclencheur et le correspondant de sortie détails.

#### <a name="request-trigger"></a>Demande de déclencheur
Voici un champ d’entrée du déclencheur à partir d’une requête HTTP entrante.

|Nom d’affichage|Nom de la propriété|Description|
|---|---|---|
|Schéma JSON|schéma|Le schéma JSON du corps de la demande HTTP|
<br>

**Détails de sortie**

Vous trouverez ci-dessous des informations de sortie pour la requête.

|Nom de la propriété|Type de données|Description|
|---|---|---|
|En-têtes|objet|En-têtes de requête|
|Corps|objet|Objet de requête|

#### <a name="response-action"></a>Action de réponse

Voici les champs d’entrée pour l’action de réponse HTTP. A * signifie qu’il s’agit d’un champ obligatoire.

|Nom d’affichage|Nom de la propriété|Description|
|---|---|---|
|Code d’état *|statusCode|Code d’état HTTP|
|En-têtes|en-têtes|Un objet JSON les en-têtes de réponse à inclure|
|Corps|corps|Le corps de réponse|

## <a name="next-steps"></a>Étapes suivantes

À présent, essayez la plateforme et [créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md). Vous pouvez Explorer d’autres connecteurs disponibles dans les applications logique en consultant notre [liste API](apis-list.md).
