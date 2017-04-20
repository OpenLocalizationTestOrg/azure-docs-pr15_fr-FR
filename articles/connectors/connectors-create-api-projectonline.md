<properties
pageTitle="Project Online | Microsoft Azure"
description="Créez des applications de logique avec le service d’application Azure. Projet en ligne est une solution en ligne flexible pour la gestion de portefeuille de projets (PPM) et des tâches quotidiennes de Microsoft. Remis via Office 365, Project Online permet aux organisations de prise en main rapide avec les fonctionnalités de gestion de projet puissantes classer par priorité, planifier et gérer des projets et des investissements de portefeuille — depuis presque n’importe où sur n’importe quel appareil."
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

# <a name="get-started-with-the-projectonline-connector"></a>Prise en main du connecteur Project Online

Projet en ligne est une solution en ligne flexible pour la gestion de portefeuille de projets (PPM) et des tâches quotidiennes de Microsoft. Remis via Office 365, Project Online permet aux organisations de prise en main rapide avec les fonctionnalités de gestion de projet puissantes classer par priorité, planifier et gérer des projets et des investissements de portefeuille — depuis presque n’importe où sur n’importe quel appareil.

>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma logique applications 2015-08-01-aperçu. 

Vous pouvez commencer par créer une application logique maintenant, voir [créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Déclencheurs et actions

Le connecteur Project Online peut être utilisé comme une action ; Il a déclencheurs. Tous les connecteurs prennent en charge les données aux formats JSON et XML. 

 Le connecteur Project Online comporte les déclencheurs disponibles et/ou une ou des actions suivantes :

### <a name="projectonline-actions"></a>Actions de Project Online
Vous pouvez suivre ces actions :

|Action|Description|
|--- | ---|
|[ListProjects](connectors-create-api-projectonline.md#listprojects)|Répertorie les projets dans votre site en ligne de projet|
|[CreateProject](connectors-create-api-projectonline.md#createproject)|Crée un nouveau projet dans votre site en ligne de projet|
|[CreateTask](connectors-create-api-projectonline.md#createtask)|Crée une nouvelle tâche dans votre projet|
|[CreateResource](connectors-create-api-projectonline.md#createresource)|Crée une ressources d’entreprise dans votre site en ligne de projet|
|[ListTasks](connectors-create-api-projectonline.md#listtasks)|Répertorie les tâches publiés dans un projet|
|[CheckoutProject](connectors-create-api-projectonline.md#checkoutproject)|Extrait un projet dans votre site|
|[PublishProject](connectors-create-api-projectonline.md#publishproject)|Archiver et publier un projet dans votre site existant|
### <a name="projectonline-triggers"></a>Déclencheurs Project Online
Vous pouvez écouter ces événements :

|Déclencheur | Description|
|--- | ---|
|Lors de la création d’un projet|Déclenche un flux dès la création d’un projet|
|Lors de la création d’une nouvelle ressource|Déclenche un nouveau flux lors de la création d’une nouvelle ressource|
|Lors de la création d’une nouvelle tâche|Déclenche un flux de création d’une nouvelle tâche|


## <a name="create-a-connection-to-projectonline"></a>Créer une connexion à Project Online
Pour créer des applications logique avec Project Online, vous devez d’abord créer une **connexion** puis fournir les détails pour les propriétés suivantes : 

|Propriété| Obligatoire|Description|
| ---|---|---|
|Jetons|Oui|Fournir des informations d’identification Project Online|

>[AZURE.INCLUDE [Steps to create a connection to ProjectOnline](../../includes/connectors-create-api-projectonline.md)]

>[AZURE.TIP] Vous pouvez utiliser cette connexion dans les autres applications logique.

## <a name="reference-for-projectonline"></a>Référence pour Project Online
S’applique à la version : 1.0

## <a name="onnewproject"></a>OnNewProject
Lors de la création d’un projet : déclenche un flux dès la création d’un projet 

```GET: /trigger/_api/ProjectData/Projects``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|siteUrl|chaîne|Oui|requête|Aucun|Racine url du site de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## <a name="onnewresource"></a>OnNewResource
Lors de la création d’une nouvelle ressource : déclenche un nouveau flux lors de la création d’une nouvelle ressource 

```GET: /trigger/_api/ProjectData/Resources``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|siteUrl|chaîne|Oui|requête|Aucun|Racine url du site de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## <a name="onnewtask"></a>OnNewTask
Lors de la création d’une nouvelle tâche : déclenche un flux de création d’une nouvelle tâche 

```GET: /trigger/_api/ProjectData/Tasks``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|siteUrl|chaîne|Oui|requête|Aucun|Racine url du site de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## <a name="listprojects"></a>ListProjects
Liste des projets : répertorie les projets dans votre site en ligne de projet 

```GET: /_api/ProjectServer/Projects``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|siteUrl|chaîne|Oui|requête|Aucun|Racine url du site de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## <a name="createproject"></a>CreateProject
Crée le nouveau projet : crée un nouveau projet dans votre site en ligne de projet 

```POST: /_api/ProjectServer/Projects``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|siteUrl|chaîne|Oui|requête|Aucun|Racine url du site de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam)|
|projet| |Oui|corps|Aucun|Nouveau projet à créer|

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


## <a name="createtask"></a>CreateTask
Crée une nouvelle tâche : crée une nouvelle tâche dans votre projet 

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Tasks/Add``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|siteUrl|chaîne|Oui|requête|Aucun|Racine url du site de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam)|
|PROJECT_ID|chaîne|Oui|chemin d’accès|Aucun|Nº unique du projet pour ajouter la tâche à|
|tâche| |Oui|corps|Aucun|Nouvelle tâche à ajouter au projet|

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


## <a name="createresource"></a>CreateResource
Créer nouvelle ressource : crée un ressources d’entreprise dans votre site en ligne de projet 

```POST: /_api/ProjectServer/EnterpriseResources``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|siteUrl|chaîne|Oui|requête|Aucun|Racine url du site de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam)|
|ressource| |Oui|corps|Aucun|Nouvelle ressource d’entreprise à ajouter au projet|

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


## <a name="listtasks"></a>ListTasks
Répertorie les tâches : répertorie les tâches publiés dans un projet 

```GET: /_api/ProjectServer/Projects('{project_id}')/Tasks``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|siteUrl|chaîne|Oui|requête|Aucun|Racine url du site de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam)|
|PROJECT_ID|chaîne|Oui|chemin d’accès|Aucun|Nº unique du projet pour récupérer des tâches|

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


## <a name="checkoutproject"></a>CheckoutProject
Extraire un projet : extrait un projet dans votre site 

```POST: /_api/ProjectServer/Projects('{project_id}')/checkOut``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|siteUrl|chaîne|Oui|requête|Aucun|Racine url du site de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam)|
|PROJECT_ID|chaîne|Oui|chemin d’accès|Aucun|Nº unique du projet pour ajouter la tâche à|

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


## <a name="publishproject"></a>PublishProject
Archivage et publier projet : archiver un projet dans votre site existant et publier 

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Publish(true)``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|siteUrl|chaîne|Oui|requête|Aucun|Racine url du site de votre site de projet (exemple : https://sampletenant.sharepoint.com/teams/sampleteam)|
|PROJECT_ID|chaîne|Oui|chemin d’accès|Aucun|Nº unique du projet pour archivage|

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

### <a name="triggerprojectswrapper"></a>TriggerProjectsWrapper


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|valeur|tableau|N° |



### <a name="triggerproject"></a>TriggerProject


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|ProjectStartDate|chaîne|N° |
|ProjectFinishDate|chaîne|N° |
|ProjectCreatedDate|chaîne|N° |
|ProjectId|chaîne|N° |
|ProjectModifiedDate|chaîne|N° |
|ProjectType|nombre entier|N° |
|NomProjet|chaîne|N° |



### <a name="triggerresourceswrapper"></a>TriggerResourcesWrapper


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|valeur|tableau|N° |



### <a name="triggerresource"></a>TriggerResource


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|ResourceId|chaîne|N° |
|ResourceBaseCalendar|chaîne|N° |
|ResourceBookingType|nombre entier|N° |
|ResourceCanLevel|valeur booléenne|N° |
|ResourceCostPerUse|nombre|N° |
|ResourceCreatedDate|chaîne|N° |
|ResourceEarliestAvailableFrom|chaîne|N° |
|ResourceEmail|chaîne|N° |
|ResourceInitials|chaîne|N° |
|ResourceIsActive|valeur booléenne|N° |
|ResourceIsGeneric|valeur booléenne|N° |
|ResourceLatestAvailableTo|chaîne|N° |
|ResourceModifiedDate|chaîne|N° |
|ResourceName|chaîne|N° |
|ResourceStatsuName|chaîne|N° |
|Type de ressource|nombre entier|N° |
|Type de ChampDescription|chaîne|N° |
|TypeName|chaîne|N° |



### <a name="triggertaskswrapper"></a>TriggerTasksWrapper


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|valeur|tableau|N° |



### <a name="triggertask"></a>TriggerTask


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|ProjectId|chaîne|N° |
|Identificateur de tâche|chaîne|N° |
|NomProjet|chaîne|N° |
|Nom de la tâche|chaîne|N° |
|TaskCreatedDate|chaîne|N° |
|TaskModifieddate|chaîne|N° |
|TaskStartDate|chaîne|N° |
|TaskFinishDate|chaîne|N° |
|TaskPriority|nombre entier|N° |
|TaskIsActive|valeur booléenne|N° |



### <a name="newproject"></a>NewProject


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|Nom|chaîne|Oui |
|Description|chaîne|N° |
|Démarrer|chaîne|N° |



### <a name="newreource"></a>NewReource


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|Nom|chaîne|Oui |
|IsBudget|valeur booléenne|N° |
|IsGeneric|valeur booléenne|N° |
|IsInactive|valeur booléenne|N° |



### <a name="project"></a>Projet


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|ApprovedStart|chaîne|N° |
|ApprovedEnd|chaîne|N° |
|CheckedOutDate|chaîne|N° |
|CheckOutDescription|chaîne|N° |
|CheckOutId|chaîne|N° |
|CreatedDate|chaîne|N° |
|ID|chaîne|N° |
|IsCheckedOut|valeur booléenne|N° |
|LastPublishedDate|chaîne|N° |
|DateDernierEnreg|chaîne|N° |
|OptimizerDecision|nombre entier|N° |
|PlannerDecision|nombre entier|N° |
|ProjectType|nombre entier|N° |
|Nom|chaîne|N° |
|WinprojVersion|chaîne|N° |



### <a name="projectswrapper"></a>ProjectsWrapper


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|valeur|tableau|N° |



### <a name="newtask"></a>NewTask


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|paramètres|non défini|Oui |



### <a name="taskparameters"></a>TaskParameters


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|Nom|chaîne|Oui |
|Notes|chaîne|N° |
|Démarrer|chaîne|N° |
|Durée|chaîne|N° |



### <a name="enterpriseresource"></a>EnterpriseResource


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|CanLevel|valeur booléenne|N° |
|Code|chaîne|N° |
|CostAccrual|nombre entier|N° |
|Centre de coût|chaîne|N° |
|Créé|chaîne|N° |
|DefaultBookingType|nombre entier|N° |
|Messagerie|chaîne|N° |
|ExternalId|chaîne|N° |
|Groupe|chaîne|N° |
|HireDate|chaîne|N° |
|ID|chaîne|N° |
|Initiales|chaîne|N° |
|IsActive|valeur booléenne|N° |
|IsBudget|valeur booléenne|N° |
|IsCheckedOut|valeur booléenne|N° |
|IsGeneric|valeur booléenne|N° |
|IsTeam|valeur booléenne|N° |
|MaterialLabel|chaîne|N° |
|Modifié|chaîne|N° |
|Nom|chaîne|N° |
|Phonétique|chaîne|N° |
|Type de ressource|nombre entier|N° |
|TerminationDate|chaîne|N° |



### <a name="taskswrapper"></a>TasksWrapper


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|valeur|tableau|N° |



### <a name="task"></a>Tâche


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|Créé|chaîne|N° |
|Modifié|chaîne|N° |
|Démarrer|chaîne|N° |
|Fin|chaîne|N° |
|Nom|chaîne|N° |
|ID|chaîne|N° |
|Priorité|nombre entier|N° |
|PercentComplete|nombre entier|N° |
|Notes|chaîne|N° |
|Contact|chaîne|N° |


## <a name="next-steps"></a>Étapes suivantes
[Créer une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md)