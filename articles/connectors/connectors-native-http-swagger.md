
<properties
    pageTitle="Ajouter la HTTP + Swagger action dans les applications logique | Microsoft Azure"
    description="Vue d’ensemble du HTTP + Swagger action et opérations"
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

# <a name="get-started-with-the-http--swagger-action"></a>Prise en main la HTTP + Swagger action

Avec l’HTTP + Swagger action, vous pouvez créer un lien afin de n’importe quel point de terminaison reste via un [Swagger document](https://swagger.io)première classe. Vous pouvez également étendre une application logique pour appeler un point de terminaison reste avec tirer le meilleur bénéfice logique App Designer.

Pour commencer à utiliser la HTTP + Swagger action dans une application logique, voir [créer une nouvelle application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

---

## <a name="use-http--swagger-as-a-trigger-or-an-action"></a>Utiliser HTTP + Swagger comme un déclencheur ou une action

La HTTP + Swagger déclencheur et une action identique à l' [action HTTP](connectors-native-http.md) fonctionner mais fournir une meilleure expérience de création en affichant la forme des API et des sorties dans le Concepteur de la [Swagger des métadonnées](https://swagger.io). En outre, vous pouvez utiliser HTTP + Swagger comme un déclencheur. Si vous voulez mettre en œuvre d’un déclencheur d’interrogation, elle doit suivre le modèle d’interrogation qui est décrit dans [Création d’une API personnalisée à utiliser avec les applications de logique](../app-service-logic/app-service-logic-create-api-app.md#polling-triggers).

[En savoir plus sur les actions et déclencheurs d’application logique.](connectors-overview.md)

Voici un exemple de façon à utiliser le protocole HTTP + Swagger opération sous forme d’action dans un flux de travail dans une application logique.

1. Sélectionnez le bouton **Nouvelle étape** .
2. Sélectionnez **Ajouter une action**.
3. Dans la zone de recherche action, tapez **swagger** à liste HTTP + Swagger action.

    ![Sélectionnez HTTP + Swagger action](./media/connectors-native-http-swagger/using-action-1.png)

4. Tapez l’URL d’un document Swagger :
    - Pour travailler de manière à partir du Concepteur d’application logique, l’URL doit être un point de terminaison HTTPS et avez CORS activé.
    - Si le document Swagger ne répond pas à cette exigence, vous pouvez utiliser [Le stockage Azure avec CORS activé](#hosting-swagger-from-storage) pour stocker le document.
5. Cliquez sur **suivant** pour la lecture et le rendu du document Swagger.
6. Ajouter tous les paramètres requis pour l’appel HTTP.

    ![Terminer action HTTP](./media/connectors-native-http-swagger/using-action-2.png)

1. Cliquez sur **Enregistrer** dans le coin supérieur gauche de la barre d’outils et votre logique application à la fois enregistrer et publier (activer).

### <a name="host-swagger-from-azure-storage"></a>Hôte Swagger à partir du stockage Azure

Vous souhaiterez peut-être faire référence à un document Swagger qui n’est pas hébergée, ou qui ne répond pas à la sécurité et la configuration requise d’origine croisée pour le concepteur. Pour résoudre ce problème, vous pouvez enregistrer le document Swagger dans le stockage Azure et activer CORS référencer le document.  

Voici les étapes pour créer, configurer et stocker des documents Swagger dans le stockage Azure :

1. [Créer un compte de stockage Azure avec stockage d’objets Blob Azure](../storage/storage-create-storage-account.md). (Pour ce faire, définir des autorisations d’un **accès Public**).
2. Activer le CORS sur le blob. Vous pouvez utiliser [ce script PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1) pour configurer automatiquement ce paramètre.
3. Téléchargez le fichier Swagger dans le blob. Vous pouvez le faire à partir du [portail Azure](https://portal.azure.com) ou à partir d’un outil comme dans [l’Explorateur d’espace de stockage Azure](http://storageexplorer.com/).
1. Faire référence à un lien HTTPS vers le document dans le stockage Blob Azure. (Le lien suit le format `https://*storageAccountName*.blob.core.windows.net/*container*/*filename*`.)



## <a name="technical-details"></a>Détails techniques

Voici les détails pour les déclencheurs et les actions que cette HTTP + Swagger connecteur prend en charge.

## <a name="http--swagger-triggers"></a>HTTP + déclencheurs Swagger

Un déclencheur est un événement qui peut être utilisé pour démarrer le flux de travail qui est défini dans une application logique. [Apprenez-en davantage sur déclencheurs.](connectors-overview.md) La HTTP + Swagger connecteur comporte un déclencheur.

|Déclencheur|Description|
|---|---|
|HTTP + Swagger|Effectuer un appel HTTP et le contenu de la réponse de retour|

## <a name="http--swagger-actions"></a>HTTP + Swagger actions

Une action est une opération qui est effectuée par le flux de travail qui est défini dans une application logique. [Apprenez-en davantage sur actions.](connectors-overview.md) La HTTP + Swagger connecteur comporte une action possible.

|Action|Description|
|---|---|
|HTTP + Swagger|Effectuer un appel HTTP et le contenu de la réponse de retour|

### <a name="action-details"></a>Détails de l’action

La HTTP + Swagger connecteur est fourni avec une action possible. Voici les informations relatives à chacune des actions, les champs d’entrée obligatoires et facultatifs et les détails de sortie correspondants qui sont associés à leur utilisation.

#### <a name="http--swagger"></a>HTTP + Swagger

Effectuer une demande sortante HTTP avec l’aide de métadonnées Swagger.
Un astérisque (*) indique qu’un champ obligatoire.

|Nom d’affichage|Nom de la propriété|Description|
|---|---|---|
|Méthode *|méthode|Verbe HTTP à utiliser.|
|URI *|URI|URI de la requête HTTP.|
|En-têtes|en-têtes|Un objet JSON d’en-têtes HTTP à inclure.|
|Corps|corps|Le corps de la demande HTTP.|
|Authentification|authentification|Authentification à utiliser pour la demande. [Pour plus d’informations, voir HTTP](./connectors-native-http.md#authentication).|

**Détails de sortie**

Réponse HTTP

|Nom de la propriété|Type de données|Description|
|---|---|---|
|En-têtes|objet|En-têtes de réponse|
|Corps|objet|Objet de réponse|
|Code d’état|ent|Code d’état HTTP|

### <a name="http-responses"></a>Réponses HTTP

Lorsque vous faites appel à diverses actions, vous pouvez obtenir certaines réponses. Voici un tableau qui décrit les descriptions et les réponses correspondantes.

|Nom|Description|
|---|---|
|200|Bien|
|202|Accepté|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|500|Erreur interne du serveur. Erreur inconnue s’est produite.|

---

## <a name="next-steps"></a>Étapes suivantes

Essayer la plateforme et [créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md) maintenant. Vous pouvez Explorer d’autres connecteurs disponibles dans les applications logique en consultant notre [liste d’API](apis-list.md).
