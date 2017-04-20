<properties
    pageTitle="Ajouter le connecteur Dynamics CRM Online à vos applications logique | Microsoft Azure"
    description="Créez des applications de logique avec le service d’application Azure. Le fournisseur de connexion Dynamics CRM Online fournit une API pour travailler avec des entités sur Dynamics CRM Online."
    services="logic-apps"    
    documentationCenter=""     
    authors="MandiOhlinger"    
    manager="erikre"    
    editor="" 
    tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/15/2016"
ms.author="mandia"/>

# <a name="get-started-with-the-dynamics-crm-online-connector"></a>Prise en main le connecteur Dynamics CRM Online
Se connecter à Dynamics CRM Online pour créer un nouvel enregistrement, mettre à jour un élément et bien plus encore. Avec CRM Online, vous pouvez :

- Créer votre flux d’activité basé sur les données que vous obtenez à partir de CRM Online. 
- Actions utilisation que supprimer un enregistrement, obtenez entités et bien plus encore. Ces actions Obtient une réponse et apportez la sortie disponibles pour d’autres actions. Par exemple, lorsqu’un élément est mis à jour dans CRM, vous pouvez envoyer un message électronique à l’aide d’Office 365.

Cette rubrique vous montre comment utiliser le connecteur Dynamics CRM Online dans une application logique et répertorie également les déclencheurs et les actions.

>[AZURE.NOTE] Cette version de l’article s’applique aux officielle applications logique (disponible).

Pour en savoir plus sur les applications logique, voir [Quels sont les applications logique](../app-service-logic/app-service-logic-what-are-logic-apps.md) et [créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="connect-to-dynamics-crm-online"></a>Se connecter à Dynamics CRM Online

Avant que votre application logique peut accéder à n’importe quel service, vous créez tout d’abord une *connexion* au service. Une connexion assure la connectivité entre une application logique et un autre service. Par exemple, pour vous connecter à Dynamics, vous devez tout d’abord une *connexion*Dynamics CRM Online. Pour créer une connexion, entrez les informations d’identification que vous utilisez normalement pour accéder au service que vous voulez vous connecter. Ainsi, avec Dynamics, entrez les informations d’identification à votre compte Dynamics CRM Online pour créer la connexion.


### <a name="create-the-connection"></a>Créer la connexion

>[AZURE.INCLUDE [Steps to create a connection to Dynamics CRM Online Connection Provider](../../includes/connectors-create-api-crmonline.md)]

## <a name="use-a-trigger"></a>Utilisation d’un déclencheur

Un déclencheur est un événement qui peut être utilisé pour démarrer le flux de travail définie dans une application logique. Déclencheurs « teste » le service à un intervalle et la fréquence à laquelle vous voulez. [En savoir plus sur les déclencheurs](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

1. Dans l’application logique, tapez « dynamics » pour obtenir une liste de déclencheurs :  

    ![](./media/connectors-create-api-crmonline/dynamics-triggers.png)

2. Sélectionnez **Dynamics CRM Online - lorsqu’un enregistrement est créé**. Si une connexion existe déjà, puis sélectionnez une organisation et une entité dans la liste déroulante.

    ![](./media/connectors-create-api-crmonline/select-organization.png)

    Si vous êtes invité à se connecter, puis entrez le signe dans Détails pour créer la connexion. [Créer la connexion](connectors-create-api-crmonline.md#create-the-connection) dans cette rubrique répertorie les étapes. 

    > [AZURE.NOTE] Dans cet exemple, l’application logique s’exécute lors de la création d’un enregistrement. Pour afficher les résultats de ce déclencheur, ajouter une autre action qui vous envoie un message électronique. Par exemple, ajoutez l’action Office 365 *Envoyer un message électronique* qui vous avertit lors de l’ajout du nouvel enregistrement. 

3. Sélectionnez le bouton **Modifier** et définissez les valeurs **fréquence** et **intervalle** . Par exemple, si vous souhaitez que le déclencheur d’interrogation toutes les 15 minutes, puis définissez la **fréquence** sur **minutes**et définir un **intervalle de** **15**. 

    ![](./media/connectors-create-api-crmonline/edit-properties.png)

4. **Enregistrer** vos modifications (coin supérieur gauche de la barre d’outils). Votre application logique est enregistrée et peut être activée automatiquement.


## <a name="use-an-action"></a>Utiliser une action

Une action est une opération effectuée par le flux de travail définie dans une application logique. [En savoir plus sur les actions](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

1. Sélectionnez le signe plus. Vous voyez plusieurs choix : **Ajouter une action**, **Ajouter une condition**ou l’une des options **supplémentaires** .

    ![](./media/connectors-create-api-crmonline/add-action.png)

2. Cliquez sur **Ajouter une action**.

3. Dans la zone de texte, tapez « dynamics » pour obtenir une liste de toutes les actions disponibles.

    ![](./media/connectors-create-api-crmonline/dynamics-actions.png)

4. Dans notre exemple, choisissez **Dynamics CRM Online : mettre à jour un enregistrement**. Si une connexion existe déjà, puis choisissez le **Nom de l’organisation**, **Nom de l’entité**et d’autres propriétés :  

    ![](./media/connectors-create-api-crmonline/sample-action.png)

    Si vous êtes invité aux informations de connexion, puis entrez les détails pour créer la connexion. [Créer la connexion](connectors-create-api-crmonline.md#create-the-connection) dans cette rubrique décrit ces propriétés. 

    > [AZURE.NOTE] Dans cet exemple, nous mettre à jour un enregistrement existant dans CRM Online. Vous pouvez utiliser la sortie à partir d’un autre déclencheur pour mettre à jour l’enregistrement. Par exemple, ajoutez le déclencheur SharePoint *lors de la modification d’un élément existant* . Ajoutez ensuite l’action CRM Online *mettre à jour un enregistrement* qui utilise les champs SharePoint pour mettre à jour l’enregistrement existant dans CRM Online. 

5. **Enregistrer** vos modifications (coin supérieur gauche de la barre d’outils). Votre application logique est enregistrée et peut être activée automatiquement.


## <a name="technical-details"></a>Détails techniques

## <a name="triggers"></a>Déclencheurs

|Déclencheur | Description|
|--- | ---|
|[Lors de la création d’un enregistrement](connectors-create-api-crmonline.md#when-a-record-is-created)|Déclenche un flux lorsqu’un objet est créé dans CRM.|
|[Lorsqu’un enregistrement est mis à jour](connectors-create-api-crmonline.md#when-a-record-is-updated)|Déclenche un flux lorsqu’un objet est modifié dans CRM.|
|[Lorsqu’un enregistrement est supprimé](connectors-create-api-crmonline.md#when-a-record-is-deleted)|Déclenche un flux lorsqu’un objet est supprimé dans CRM.|


## <a name="actions"></a>Actions

|Action|Description|
|--- | ---|
|[Liste des enregistrements](connectors-create-api-crmonline.md#list-records)|Cette opération Obtient les enregistrements d’une entité.|
|[Créer un enregistrement](connectors-create-api-crmonline.md#create-a-new-record)|Cette opération crée un enregistrement d’une entité.|
|[Obtenir l’enregistrement](connectors-create-api-crmonline.md#get-record)|Cette opération Obtient l’enregistrement spécifié pour une entité.|
|[Supprimer un enregistrement](connectors-create-api-crmonline.md#delete-a-record)|Cette opération supprime un enregistrement à partir d’une collection d’entités.|
|[Mettre à jour un enregistrement](connectors-create-api-crmonline.md#update-a-record)|Cette opération met à jour un enregistrement existant pour une entité.|

### <a name="trigger-and-action-details"></a>Détails de déclencheur et une Action

Dans cette section, voir les détails relatifs à chaque déclencheur et une action, y compris les propriétés d’entrée obligatoire ou facultatives et aucun résultat correspondant associé au connecteur.

#### <a name="when-a-record-is-created"></a>Lors de la création d’un enregistrement
Déclenche un flux lorsqu’un objet est créé dans CRM. 

|Nom de la propriété| Nom d’affichage|Description|
| ---|---|---|
|jeu de données *|Nom de l’organisation|Nom de l’organisation CRM comme Contoso|
|table *|Nom de l’entité|Nom de l’entité|
|$skip|Nombre d’ignorer|Nombre d’entrées pour ignorer (par défaut = 0)|
|$top|Nombre maximal d’obtenir|Nombre maximal d’entrées pour obtenir (par défaut = 256)|
|$filter|Requête de filtre|Une requête de filtre ODATA pour restreindre les entrées retournées|
|$orderby|Tri par|Une requête orderBy ODATA permettant de spécifier l’ordre des entrées|

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails de sortie
Liste

| Nom de la propriété | Type de données |
|---|---|
|valeur|tableau|


#### <a name="when-a-record-is-updated"></a>Lorsqu’un enregistrement est mis à jour
Déclenche un flux lorsqu’un objet est modifié dans CRM. 

|Nom de la propriété| Nom d’affichage|Description|
| ---|---|---|
|jeu de données *|Nom de l’organisation|Nom de l’organisation CRM comme Contoso|
|table *|Nom de l’entité|Nom de l’entité|
|$skip|Nombre d’ignorer|Nombre d’entrées pour ignorer (par défaut = 0)|
|$top|Nombre maximal d’obtenir|Nombre maximal d’entrées pour obtenir (par défaut = 256)|
|$filter|Requête de filtre|Une requête de filtre ODATA pour restreindre les entrées retournées|
|$orderby|Tri par|Une requête orderBy ODATA permettant de spécifier l’ordre des entrées|

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails de sortie
Liste

| Nom de la propriété | Type de données |
|---|---|
|valeur|tableau|


#### <a name="when-a-record-is-deleted"></a>Lorsqu’un enregistrement est supprimé
Déclenche un flux lorsqu’un objet est supprimé dans CRM. 

|Nom de la propriété| Nom d’affichage|Description|
| ---|---|---|
|jeu de données *|Nom de l’organisation|Nom de l’organisation CRM comme Contoso|
|table *|Nom de l’entité|Nom de l’entité|
|$skip|Nombre d’ignorer|Nombre d’entrées pour ignorer (par défaut = 0)|
|$top|Nombre maximal d’obtenir|Nombre maximal d’entrées pour obtenir (par défaut = 256)|
|$filter|Requête de filtre|Une requête de filtre ODATA pour restreindre les entrées retournées|
|$orderby|Tri par|Une requête orderBy ODATA permettant de spécifier l’ordre des entrées|

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails de sortie
Liste

| Nom de la propriété | Type de données |
|---|---|
|valeur|tableau|


#### <a name="list-records"></a>Liste des enregistrements
Cette opération Obtient les enregistrements d’une entité. 

|Nom de la propriété| Nom d’affichage|Description|
| ---|---|---|
|jeu de données *|Nom de l’organisation|Nom de l’organisation CRM comme Contoso|
|table *|Nom de l’entité|Nom de l’entité|
|$skip|Nombre d’ignorer|Nombre d’entrées pour ignorer (par défaut = 0)|
|$top|Nombre maximal d’obtenir|Nombre maximal d’entrées pour obtenir (par défaut = 256)|
|$filter|Requête de filtre|Une requête de filtre ODATA pour restreindre les entrées retournées|
|$orderby|Tri par|Une requête orderBy ODATA permettant de spécifier l’ordre des entrées|

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails de sortie
Liste

| Nom de la propriété | Type de données |
|---|---|
|valeur|tableau|


#### <a name="create-a-new-record"></a>Créer un enregistrement
Cette opération crée un enregistrement d’une entité. 

|Nom de la propriété| Nom d’affichage|Description|
| ---|---|---|
|jeu de données *|Nom de l’organisation|Nom de l’organisation CRM comme Contoso|
|table *|Nom de l’entité|Nom de l’entité|

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails de sortie
Aucun.


#### <a name="get-record"></a>Obtenir l’enregistrement
Cette opération Obtient l’enregistrement spécifié pour une entité. 

|Nom de la propriété| Nom d’affichage|Description|
| ---|---|---|
|jeu de données *|Nom de l’organisation|Nom de l’organisation CRM comme Contoso|
|table *|Nom de l’entité|Nom de l’entité|
|ID *|Identificateur de l’élément|Spécifier l’identificateur de l’enregistrement|

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails de sortie
Aucun.


#### <a name="delete-a-record"></a>Supprimer un enregistrement
Cette opération supprime un enregistrement à partir d’une collection d’entités. 

|Nom de la propriété| Nom d’affichage|Description|
| ---|---|---|
|jeu de données *|Nom de l’organisation|Nom de l’organisation CRM comme Contoso|
|table *|Nom de l’entité|Nom de l’entité|
|ID *|Identificateur de l’élément|Spécifier l’identificateur de l’enregistrement|

Un astérisque (*) signifie que la propriété est requise.


#### <a name="update-a-record"></a>Mettre à jour un enregistrement
Cette opération met à jour un enregistrement existant pour une entité. 

|Nom de la propriété| Nom d’affichage|Description|
| ---|---|---|
|jeu de données *|Nom de l’organisation|Nom de l’organisation CRM comme Contoso|
|table *|Nom de l’entité|Nom de l’entité|
|ID *|Identificateur d’enregistrement|Spécifier l’identificateur de l’enregistrement|

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails de sortie
Aucun.


## <a name="http-responses"></a>Réponses HTTP

Actions et déclencheurs peuvent renvoyer une ou plusieurs des codes d’état HTTP suivants : 

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

[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md). Découvrir les autres connecteurs disponibles dans les applications logique auprès de notre [liste API](apis-list.md).

