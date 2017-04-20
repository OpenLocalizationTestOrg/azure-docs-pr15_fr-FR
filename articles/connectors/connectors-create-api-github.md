<properties
pageTitle="GitHub | Microsoft Azure"
description="Créez des applications de logique avec le service d’application Azure. GitHub est un référentiel Git-based service d’hébergement. Il propose toutes les révision distribué contrôle et source code management (SCM) fonctionnalités de Git ainsi que l’ajout de son propre fonctionnalités."
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

# <a name="get-started-with-the-github-connector"></a>Prise en main du connecteur GitHub

GitHub est un référentiel Git-based service d’hébergement. Il propose toutes les révision distribué contrôle et source code management (SCM) fonctionnalités de Git ainsi que l’ajout de son propre fonctionnalités.

>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma logique applications 2015-08-01-aperçu. 

Vous pouvez commencer par créer une application logique maintenant, voir [créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Déclencheurs et actions

Le connecteur GitHub peut être utilisé comme une action ; Il a déclencheurs. Tous les connecteurs prennent en charge les données aux formats JSON et XML. 

 Le connecteur GitHub comporte les déclencheurs disponibles et/ou une ou des actions suivantes :

### <a name="github-actions"></a>Actions GitHub
Vous pouvez suivre ces actions :

|Action|Description|
|--- | ---|
|[CreateIssue](connectors-create-api-github.md#createissue)|Crée un problème|
### <a name="github-triggers"></a>Déclencheurs GitHub
Vous pouvez écouter ces événements :

|Déclencheur | Description|
|--- | ---|
|Lorsqu’un problème est ouvert|Un problème est ouvert|
|Lors de la fermeture d’un problème|Un problème est fermé|
|Lorsqu’un problème est affecté|Un problème est affecté|


## <a name="create-a-connection-to-github"></a>Créer une connexion à GitHub
Pour créer des applications logique avec GitHub, vous devez d’abord créer une **connexion** puis fournir les détails pour les propriétés suivantes : 

|Propriété| Obligatoire|Description|
| ---|---|---|
|Jetons|Oui|Fournir des informations d’identification GitHub|
Après avoir créé la connexion, vous pouvez l’utiliser pour exécuter les actions et écouter pour les déclencheurs décrites dans cet article. 

>[AZURE.INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]

>[AZURE.TIP] Vous pouvez utiliser cette connexion dans les autres applications logique.

## <a name="reference-for-github"></a>Référence pour GitHub
S’applique à la version : 1.0

## <a name="createissue"></a>CreateIssue
Créer un problème : crée un problème 

```POST: /repos/{repositoryOwner}/{repositoryName}/issues``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|repositoryOwner|chaîne|Oui|chemin d’accès|Aucun|Propriétaire du référentiel|
|RepositoryNameRéférentiel|chaîne|Oui|chemin d’accès|Aucun|Nom du référentiel|
|issueBasicDetails| |Oui|corps|Aucun|Détails du problème|

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


## <a name="issueopened"></a>IssueOpened
Lorsqu’un problème est ouvert : un problème est ouvert 

```GET: /trigger/issueOpened``` 

Aucun paramètre pour cet appel
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


## <a name="issueclosed"></a>IssueClosed
Lors de la fermeture d’un problème : un problème est fermé 

```GET: /trigger/issueClosed``` 

Aucun paramètre pour cet appel
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


## <a name="issueassigned"></a>IssueAssigned
Lorsqu’un problème est affecté : un problème est affecté 

```GET: /trigger/issueAssigned``` 

Aucun paramètre pour cet appel
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

### <a name="issuebasicdetailsmodel"></a>IssueBasicDetailsModel


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|titre|chaîne|Oui |
|corps|chaîne|Oui |
|intervenant|chaîne|Oui |



### <a name="issuedetailsmodel"></a>IssueDetailsModel


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|titre|chaîne|Oui |
|corps|chaîne|Oui |
|intervenant|chaîne|Oui |
|nombre|chaîne|N° |
|état|chaîne|N° |
|created_at|chaîne|N° |
|repository_url|chaîne|N° |


## <a name="next-steps"></a>Étapes suivantes
[Créer une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md)