<properties
pageTitle="Wunderlist | Microsoft Azure"
description="Créez des applications de logique avec le service d’application Azure. Wunderlist fournit un gestionnaire de liste et tâche todo pour aider les utilisateurs à leur terminé de mener à bien.  Si vous partagez une liste de courses par exemple un, travaillant sur un projet ou planification des vacances, Wunderlist facilite capturer, partager et terminer votre to¬dos. Wunderlist synchronise instantanément entre votre téléphone, tablette et votre ordinateur, pour pouvoir accéder à toutes vos tâches en tout lieu."
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

# <a name="get-started-with-the-wunderlist-connector"></a>Prise en main du connecteur Wunderlist

Wunderlist fournit un gestionnaire de liste et tâche todo pour aider les utilisateurs à leur terminé de mener à bien.  Si vous partagez une liste de courses par exemple un, travaillant sur un projet ou planification des vacances, Wunderlist facilite capturer, partager et terminer votre to¬dos. Wunderlist synchronise instantanément entre votre téléphone, tablette et votre ordinateur, pour pouvoir accéder à toutes vos tâches en tout lieu.

>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma logique applications 2015-08-01-aperçu. 

Vous pouvez commencer par créer une application logique maintenant, voir [créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Déclencheurs et actions

Le connecteur Wunderlist peut être utilisé comme une action ; Il a déclencheurs. Tous les connecteurs prennent en charge les données aux formats JSON et XML. 

 Le connecteur Wunderlist comporte les déclencheurs disponibles et/ou une ou des actions suivantes :

### <a name="wunderlist-actions"></a>Actions Wunderlist
Vous pouvez suivre ces actions :

|Action|Description|
|--- | ---|
|[RetrieveLists](connectors-create-api-wunderlist.md#retrievelists)|Récupérer les listes associées à votre compte.|
|[CreateList](connectors-create-api-wunderlist.md#createlist)|Créer une liste.|
|[ListTasks](connectors-create-api-wunderlist.md#listtasks)|Récupérez vos tâches à partir d’une liste spécifique.|
|[CreateTask](connectors-create-api-wunderlist.md#createtask)|Créer une tâche|
|[ListSubTasks](connectors-create-api-wunderlist.md#listsubtasks)|Récupérer des tâches subordonnées à partir d’une liste spécifique ou d’une tâche spécifique.|
|[CreateSubTask](connectors-create-api-wunderlist.md#createsubtask)|Créer une tâche subordonnée au sein d’une tâche spécifique|
|[ListNotes](connectors-create-api-wunderlist.md#listnotes)|Récupérer des notes pour une liste spécifique ou une tâche spécifique.|
|[CreateNote](connectors-create-api-wunderlist.md#createnote)|Ajouter une remarque à une tâche spécifique|
|[ListComments](connectors-create-api-wunderlist.md#listcomments)|Récupérez vos commentaires de tâche pour une liste spécifique ou une tâche spécifique.|
|[CreateComment](connectors-create-api-wunderlist.md#createcomment)|Ajouter un commentaire à une tâche spécifique|
|[RetrieveReminders](connectors-create-api-wunderlist.md#retrievereminders)|Récupérer des rappels pour une liste spécifique ou une tâche spécifique.|
|[CreateReminder](connectors-create-api-wunderlist.md#createreminder)|Définir un rappel.|
|[RetrieveFiles](connectors-create-api-wunderlist.md#retrievefiles)|Récupérer des fichiers pour une liste spécifique ou une tâche spécifique.|
|[GetList](connectors-create-api-wunderlist.md#getlist)|Récupère une liste spécifique|
|[DeleteList](connectors-create-api-wunderlist.md#deletelist)|Supprimer une liste|
|[UpdateList](connectors-create-api-wunderlist.md#updatelist)|Mettre à jour une liste spécifique|
|[GetTask](connectors-create-api-wunderlist.md#gettask)|Récupère une tâche spécifique|
|[UpdateTask](connectors-create-api-wunderlist.md#updatetask)|Met à jour une tâche spécifique|
|[DeleteTask](connectors-create-api-wunderlist.md#deletetask)|Supprimer une tâche spécifique|
|[GetSubTask](connectors-create-api-wunderlist.md#getsubtask)|Récupère une tâche subordonnée spécifique|
|[UpdateSubTask](connectors-create-api-wunderlist.md#updatesubtask)|Met à jour une tâche subordonnée spécifique|
|[DeleteSubTask](connectors-create-api-wunderlist.md#deletesubtask)|Supprime une tâche subordonnée spécifique|
|[GetNote](connectors-create-api-wunderlist.md#getnote)|Récupérer une note spécifique|
|[Mise à jourRemarque :](connectors-create-api-wunderlist.md#updatenote)|Mettre à jour une note spécifique|
|[DeleteREMARQUE](connectors-create-api-wunderlist.md#deletenote)|Supprimer une note spécifique|
|[GetComment](connectors-create-api-wunderlist.md#getcomment)|Récupérer un commentaire de la tâche spécifique|
|[UpdateReminder](connectors-create-api-wunderlist.md#updatereminder)|Mettre à jour un rappel spécifique|
|[DeleteReminder](connectors-create-api-wunderlist.md#deletereminder)|Supprimer un rappel spécifique|
### <a name="wunderlist-triggers"></a>Déclencheurs Wunderlist
Vous pouvez écouter ces événements :

|Déclencheur | Description|
|--- | ---|
|Lorsqu’une tâche est d’échéance|Déclenche un nouveau flux lorsqu’une tâche dans la liste est d’échéance|
|Lors de la création d’une nouvelle tâche|Déclenche un nouveau flux lors de la création d’une nouvelle tâche dans la liste|
|Lorsqu’un rappel se produit|Déclencher un nouveau flux lorsque se produit un rappel|


## <a name="create-a-connection-to-wunderlist"></a>Créer une connexion à Wunderlist
Pour créer des applications logique avec Wunderlist, vous devez d’abord créer une **connexion** puis fournir les détails pour les propriétés suivantes : 

|Propriété| Obligatoire|Description|
| ---|---|---|
|Jetons|Oui|Fournir des informations d’identification Wunderlist|
Après avoir créé la connexion, vous pouvez l’utiliser pour exécuter les actions et écouter pour les déclencheurs décrites dans cet article. 


>[AZURE.INCLUDE [Steps to create a connection to Wunderlist](../../includes/connectors-create-api-wunderlist.md)] 


>[AZURE.TIP] Vous pouvez utiliser cette connexion dans les autres applications logique.

## <a name="reference-for-wunderlist"></a>Référence pour Wunderlist
S’applique à la version : 1.0

## <a name="triggertaskdue"></a>TriggerTaskDue
Quand une tâche est due : déclenche un nouveau flux lorsqu’une tâche dans la liste est d’échéance 

```GET: /trigger/tasksdue``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|list_id|nombre entier|Oui|requête|Aucun|ID de la liste|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|


## <a name="triggertasknew"></a>TriggerTaskNew
Lors de la création d’une nouvelle tâche : déclenche un nouveau flux lors de la création d’une nouvelle tâche dans la liste 

```GET: /trigger/tasksnew``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|list_id|nombre entier|Oui|requête|Aucun|ID de la liste|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|


## <a name="triggerreminder"></a>TriggerReminder
Lorsqu’un rappel se produit : déclencher un nouveau flux lorsque se produit un rappel 

```GET: /trigger/reminders``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|list_id|nombre entier|Oui|requête|Aucun|ID de la liste|
|id_tâche|nombre entier|aucune|requête|Aucun|Nº de tâche|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|


## <a name="retrievelists"></a>RetrieveLists
Obtenir les listes : récupérer les listes associées à votre compte. 

```GET: /lists``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|
|400|Demande incorrecte|
|500|Erreur interne du serveur. Erreur inconnue|
|par défaut|Échoué de l’opération.|


## <a name="createlist"></a>CreateList
Créer une liste : créer une liste. 

```POST: /lists``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|Publier| |Oui|corps|Aucun|Nouvelle liste à créer|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|
|par défaut|Échoué de l’opération.|


## <a name="listtasks"></a>ListTasks
Obtenir les tâches : récupérer des tâches à partir d’une liste spécifique. 

```GET: /tasks``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|list_id|nombre entier|Oui|requête|Aucun|ID de la liste|
|terminé|valeur booléenne|aucune|requête|Aucun|Terminé|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|
|400|Demande incorrecte|
|500|Erreur interne du serveur. Erreur inconnue|
|par défaut|Échoué de l’opération.|


## <a name="createtask"></a>CreateTask
Créer une tâche : créer une tâche 

```POST: /tasks``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|Publier| |Oui|corps|Aucun|Nouvelle tâche à créer|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|201|Créé|


## <a name="listsubtasks"></a>ListSubTasks
Obtenir des tâches subordonnées : récupérer les tâches subordonnées d’une liste spécifique ou d’une tâche spécifique. 

```GET: /subtasks``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|list_id|nombre entier|Oui|requête|Aucun|ID de la liste|
|id_tâche|nombre entier|aucune|requête|Aucun|Nº de tâche|
|terminé|valeur booléenne|aucune|requête|Aucun|Terminé|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|
|400|Demande incorrecte|
|500|Erreur interne du serveur. Erreur inconnue|
|par défaut|Échoué de l’opération.|


## <a name="createsubtask"></a>CreateSubTask
Créer une tâche subordonnée : créer une tâche subordonnée au sein d’une tâche spécifique 

```POST: /subtasks``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|Publier| |Oui|corps|Aucun|Nouvelle tâche subordonnée à créer|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|201|Créé|


## <a name="listnotes"></a>ListNotes
Prise de notes : récupérer des notes pour une liste spécifique ou une tâche spécifique. 

```GET: /notes``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|list_id|nombre entier|Oui|requête|Aucun|ID de la liste|
|id_tâche|nombre entier|aucune|requête|Aucun|Nº de tâche|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|
|400|Demande incorrecte|
|500|Erreur interne du serveur. Erreur inconnue|
|par défaut|Échoué de l’opération.|


## <a name="createnote"></a>CreateNote
Créer une note : ajouter une remarque à une tâche spécifique 

```POST: /notes``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|Publier| |Oui|corps|Aucun|Créer une nouvelle note|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|201|Créé|


## <a name="listcomments"></a>ListComments
Obtenir des commentaires sur les tâches : récupérer les commentaires de tâche pour une liste spécifique ou une tâche spécifique. 

```GET: /task_comments``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|list_id|nombre entier|Oui|requête|Aucun|ID de la liste|
|id_tâche|nombre entier|aucune|requête|Aucun|Nº de tâche|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|
|400|Demande incorrecte|
|500|Erreur interne du serveur. Erreur inconnue|
|par défaut|Échoué de l’opération.|


## <a name="createcomment"></a>CreateComment
Ajouter un commentaire à une tâche : ajouter un commentaire à une tâche spécifique 

```POST: /task_comments``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|Publier| |Oui|corps|Aucun|Nouveau commentaire de tâche à créer|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|201|Créé|


## <a name="retrievereminders"></a>RetrieveReminders
Recevoir des rappels : extraire des rappels pour une liste spécifique ou une tâche spécifique. 

```GET: /reminders``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|list_id|nombre entier|Oui|requête|Aucun|ID de la liste|
|id_tâche|nombre entier|aucune|requête|Aucun|Nº de tâche|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|
|400|Demande incorrecte|
|500|Erreur interne du serveur. Erreur inconnue|
|par défaut|Échoué de l’opération.|


## <a name="createreminder"></a>CreateReminder
Définir un rappel : définir un rappel. 

```POST: /reminders``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|Publier| |Oui|corps|Aucun|Créer un nouveau rappel|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|
|par défaut|Échoué de l’opération.|


## <a name="retrievefiles"></a>RetrieveFiles
Obtenir des fichiers : récupérer des fichiers pour une liste spécifique ou une tâche spécifique. 

```GET: /files``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|list_id|nombre entier|Oui|requête|Aucun|ID de la liste|
|id_tâche|nombre entier|aucune|requête|Aucun|Nº de tâche|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|
|400|Demande incorrecte|
|500|Erreur interne du serveur. Erreur inconnue|
|par défaut|Échoué de l’opération.|


## <a name="getlist"></a>GetList
Obtenir la liste : récupère une liste spécifique 

```GET: /lists/{id}``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|ID|chaîne|Oui|chemin d’accès|Aucun|ID de la liste|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|


## <a name="deletelist"></a>DeleteList
Supprimer la liste : supprime une liste 

```DELETE: /lists/{id}``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|ID|nombre entier|Oui|chemin d’accès|Aucun|ID de la liste|
|révision|nombre entier|Oui|requête|Aucun|Révision|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|204|Aucun contenu|


## <a name="updatelist"></a>UpdateList
Mettre à jour une liste : mettre à jour une liste spécifique 

```PATCH: /lists/{id}``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|ID|nombre entier|Oui|chemin d’accès|Aucun|ID de la liste|
|Publier| |Oui|corps|Aucun|Détails de la liste|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|


## <a name="gettask"></a>GetTask
Passer à la tâche : récupère une tâche spécifique 

```GET: /tasks/{id}``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|list_id|nombre entier|Oui|requête|Aucun|ID de la liste|
|ID|nombre entier|Oui|chemin d’accès|Aucun|Nº de tâche|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|


## <a name="updatetask"></a>UpdateTask
Mettre à jour une tâche : met à jour une tâche spécifique 

```PATCH: /tasks/{id}``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|list_id|nombre entier|Oui|requête|Aucun|ID de la liste|
|ID|nombre entier|Oui|chemin d’accès|Aucun|Nº de tâche|
|Publier| |Oui|corps|Aucun|Détails de la tâche|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|


## <a name="deletetask"></a>DeleteTask
Supprimer la tâche : supprime une tâche spécifique 

```DELETE: /tasks/{id}``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|list_id|nombre entier|Oui|requête|Aucun|ID de la liste|
|ID|nombre entier|Oui|chemin d’accès|Aucun|Nº de tâche|
|révision|nombre entier|Oui|requête|Aucun|Révision|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|204|Aucun contenu|


## <a name="getsubtask"></a>GetSubTask
Obtenir des tâches subordonnées : récupère une tâche subordonnée spécifique 

```GET: /subtasks/{id}``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|ID|chaîne|Oui|chemin d’accès|Aucun|ID de la tâche subordonnée|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|


## <a name="updatesubtask"></a>UpdateSubTask
Mettre à jour une tâche subordonnée : met à jour une tâche subordonnée spécifique 

```PATCH: /subtasks/{id}``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|ID|nombre entier|Oui|chemin d’accès|Aucun|ID de la tâche subordonnée|
|Publier| |Oui|corps|Aucun|Détails des tâches subordonnées|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|


## <a name="deletesubtask"></a>DeleteSubTask
Supprimer une tâche subordonnée : supprime une tâche subordonnée spécifique 

```DELETE: /subtasks/{id}``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|ID|nombre entier|Oui|chemin d’accès|Aucun|ID de la tâche subordonnée|
|révision|nombre entier|Oui|requête|Aucun|Révision|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|204|Aucun contenu|


## <a name="getnote"></a>GetNote
Obtenir une note : récupérer une note spécifique 

```GET: /notes/{id}``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|ID|chaîne|Oui|chemin d’accès|Aucun|CODE de note|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|


## <a name="updatenote"></a>Mise à jourRemarque :
Mettre à jour une note : mettre à jour une note spécifique 

```PATCH: /notes/{id}``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|ID|nombre entier|Oui|chemin d’accès|Aucun|CODE de note|
|Publier| |Oui|corps|Aucun|Détails de la note|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|


## <a name="deletenote"></a>DeleteREMARQUE
Supprimer une note : supprimer une note spécifique 

```DELETE: /notes/{id}``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|ID|nombre entier|Oui|chemin d’accès|Aucun|CODE de note|
|révision|nombre entier|Oui|requête|Aucun|Révision|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|204|Aucun contenu|


## <a name="getcomment"></a>GetComment
Obtenir commentaire : extraire un commentaire de la tâche spécifique 

```GET: /task_comments/{id}``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|ID|chaîne|Oui|chemin d’accès|Aucun|CODE de commentaire|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|


## <a name="updatereminder"></a>UpdateReminder
Mettre à jour un rappel : mettre à jour un rappel spécifique 

```PATCH: /reminders/{id}``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|ID|nombre entier|Oui|chemin d’accès|Aucun|ID de rappel|
|Publier| |Oui|corps|Aucun|Détails de rappel|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Bien|


## <a name="deletereminder"></a>DeleteReminder
Supprimer un rappel : supprimer un rappel spécifique 

```DELETE: /reminders/{id}``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|ID|nombre entier|Oui|chemin d’accès|Aucun|ID de la fenêtre de rappel.|
|révision|nombre entier|Oui|requête|Aucun|Révision|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|204|Aucun contenu|


## <a name="object-definitions"></a>Définitions d’objets 

### <a name="list"></a>Liste


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|ID|nombre entier|N° |
|created_at|chaîne|N° |
|titre|chaîne|N° |
|list_type|chaîne|N° |
|type|chaîne|N° |
|révision|nombre entier|N° |



### <a name="createdlist"></a>CreatedList


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|ID|nombre entier|N° |
|created_at|chaîne|N° |
|titre|chaîne|N° |
|révision|nombre entier|N° |
|type|chaîne|N° |



### <a name="task"></a>Tâche


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|ID|nombre entier|N° |
|assignee_id|nombre entier|N° |
|assigner_id|nombre entier|N° |
|created_at|chaîne|N° |
|created_by_id|nombre entier|N° |
|date_fin|chaîne|N° |
|list_id|nombre entier|N° |
|révision|nombre entier|N° |
|mieux|valeur booléenne|N° |
|titre|chaîne|N° |



### <a name="subtask"></a>Tâche subordonnée


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|ID|nombre entier|N° |
|id_tâche|nombre entier|N° |
|created_at|chaîne|N° |
|created_by_id|nombre entier|N° |
|révision|chaîne|N° |
|titre|chaîne|N° |



### <a name="note"></a>Remarque


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|ID|nombre entier|N° |
|id_tâche|nombre entier|N° |
|contenu|chaîne|N° |
|created_at|chaîne|N° |
|updated_at|chaîne|N° |
|révision|nombre entier|N° |



### <a name="comment"></a>Commentaire


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|ID|nombre entier|N° |
|id_tâche|nombre entier|N° |
|révision|nombre entier|N° |
|texte|chaîne|N° |
|type|chaîne|N° |
|created_at|chaîne|N° |



### <a name="reminder"></a>Rappel


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|ID|nombre entier|N° |
|date|chaîne|N° |
|id_tâche|nombre entier|N° |
|révision|nombre entier|N° |
|type|chaîne|N° |
|created_at|chaîne|N° |
|updated_at|chaîne|N° |



### <a name="createdreminder"></a>CreatedReminder


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|ID|nombre entier|N° |
|date|chaîne|N° |
|id_tâche|nombre entier|N° |
|révision|nombre entier|N° |
|created_at|chaîne|N° |
|updated_at|chaîne|N° |



### <a name="file"></a>Fichier


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|ID|nombre entier|N° |
|URL|chaîne|N° |
|id_tâche|nombre entier|N° |
|list_id|nombre entier|N° |
|USER_ID|nombre entier|N° |
|file_name|chaîne|N° |
|content_type|chaîne|N° |
|file_size|nombre entier|N° |
|local_created_at|chaîne|N° |
|created_at|chaîne|N° |
|updated_at|chaîne|N° |
|type|chaîne|N° |
|révision|nombre entier|N° |



### <a name="newtask"></a>NewTask


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|list_id|nombre entier|Oui |
|titre|chaîne|Oui |
|assignee_id|nombre entier|N° |
|terminé|valeur booléenne|N° |
|recurrence_type|chaîne|N° |
|recurrence_count|nombre entier|N° |
|date_fin|chaîne|N° |
|mieux|valeur booléenne|N° |



### <a name="newlist"></a>NewList


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|titre|chaîne|Oui |



### <a name="newsubtask"></a>NewSubtask


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|list_id|nombre entier|Oui |
|id_tâche|nombre entier|Oui |
|titre|chaîne|Oui |
|terminé|valeur booléenne|N° |



### <a name="newnote"></a>NewNote


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|list_id|nombre entier|Oui |
|id_tâche|nombre entier|Oui |
|contenu|chaîne|Oui |



### <a name="newcomment"></a>NvoCommentaire


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|list_id|nombre entier|Oui |
|id_tâche|nombre entier|Oui |
|texte|chaîne|Oui |



### <a name="newreminder"></a>NewReminder


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|list_id|nombre entier|Oui |
|id_tâche|nombre entier|Oui |
|date|chaîne|Oui |



### <a name="updatetask"></a>UpdateTask


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|révision|nombre entier|N° |
|titre|chaîne|N° |
|assignee_id|nombre entier|N° |
|terminé|valeur booléenne|N° |
|recurrence_type|chaîne|N° |
|recurrence_count|nombre entier|N° |
|date_fin|chaîne|N° |
|mieux|valeur booléenne|N° |



### <a name="updatelist"></a>UpdateList


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|révision|nombre entier|N° |
|titre|chaîne|N° |



### <a name="updatesubtask"></a>UpdateSubtask


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|révision|nombre entier|N° |
|titre|chaîne|N° |
|terminé|valeur booléenne|N° |



### <a name="updatenote"></a>Mise à jourRemarque :


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|révision|nombre entier|N° |
|contenu|chaîne|N° |



### <a name="updatereminder"></a>UpdateReminder


| Nom de la propriété | Type de données | Obligatoire |
|---|---|---|
|date|chaîne|N° |
|révision|nombre entier|N° |


## <a name="next-steps"></a>Étapes suivantes
[Créer une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md)