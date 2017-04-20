<properties
    pageTitle="Ajouter le connecteur de base de données SQL Azure dans vos applications logique | Microsoft Azure"
    description="Vue d’ensemble du connecteur de base de données SQL Azure avec des paramètres de l’API REST"
    services=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="anneta"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="10/18/2016"
   ms.author="mandia"/>


# <a name="get-started-with-the-azure-sql-database-connector"></a>Prise en main du connecteur de base de données SQL Azure
L’utilisation du connecteur de base de données SQL Azure, créer des flux de travail pour votre organisation qui gèrent les données dans vos tableaux. 

Base de données SQL, vous :

- Créer votre flux de travail en ajoutant un nouveau client à une base de données de clients, ou une commande dans une base de données des commandes de mise à jour.
- Utiliser les actions pour obtenir une ligne de données, insérer une nouvelle ligne, ou les supprimer. Par exemple, lorsqu’un enregistrement est créé dans Dynamics CRM Online (un déclencheur), puis insérez une ligne dans une base de données SQL Azure (une action). 

Cette rubrique vous montre comment utiliser le connecteur de base de données SQL dans une application logique et également répertorie les actions.

>[AZURE.NOTE] Cette version de l’article s’applique aux officielle applications logique (disponible). 

Pour en savoir plus sur les applications logique, voir [Quels sont les applications logique](../app-service-logic/app-service-logic-what-are-logic-apps.md) et [créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="connect-to-azure-sql-database"></a>Se connecter à la base de données SQL Azure

Avant que votre application logique peut accéder à n’importe quel service, vous créez tout d’abord une *connexion* au service. Une connexion assure la connectivité entre une application logique et un autre service. Par exemple, pour vous connecter à la base de données SQL, il vous tout d’abord de créer une *connexion*de base de données SQL. Pour créer une connexion, vous entrez les informations d’identification que vous utilisez normalement pour accéder au service que vous vous connectez à. Par conséquent, dans la base de données SQL, entrez vos informations d’identification de base de données SQL pour créer la connexion. 

#### <a name="create-the-connection"></a>Créer la connexion

>[AZURE.INCLUDE [Create the connection to SQL Azure](../../includes/connectors-create-api-sqlazure.md)]

## <a name="use-a-trigger"></a>Utilisation d’un déclencheur

Ce connecteur n’a pas de déclencheurs. Utiliser d’autres déclencheurs pour démarrer l’application logique, par exemple un déclencheur périodicité, un déclencheur d’HTTP Webhook, déclencheurs disponibles avec les autres connecteurs et bien plus encore. [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md) fournit un exemple.

## <a name="use-an-action"></a>Utiliser une action
    
Une action est une opération effectuée par le flux de travail définie dans une application logique. [En savoir plus sur les actions](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

1. Sélectionnez le signe plus. Vous voyez plusieurs choix : **Ajouter une action**, **Ajouter une condition**ou l’une des options **supplémentaires** .

    ![](./media/connectors-create-api-sqlazure/add-action.png)

2. Cliquez sur **Ajouter une action**.

3. Dans la zone de texte, tapez « sql » pour obtenir une liste de toutes les actions disponibles.

    ![](./media/connectors-create-api-sqlazure/sql-1.png) 

4. Dans notre exemple, sélectionnez **SQL Server - obtenir la ligne**. Si une connexion existe déjà, sélectionnez le **nom de la Table** dans la liste déroulante, puis entrez l' **ID de ligne** que vous souhaitez revenir.

    ![](./media/connectors-create-api-sqlazure/sample-table.png)

    Si vous êtes invité aux informations de connexion, puis entrez les détails pour créer la connexion. [Créer la connexion](connectors-create-api-sqlazure.md#create-the-connection) dans cette rubrique décrit ces propriétés. 

    > [AZURE.NOTE] Dans cet exemple, nous retourner une ligne d’une table. Pour afficher les données dans cette ligne, ajoutez une autre action qui crée un fichier en utilisant les champs de la table. Par exemple, ajouter une action OneDrive qui utilise les champs Prénom et nom pour créer un nouveau fichier dans le compte de stockage cloud. 

5. **Enregistrer** vos modifications (coin supérieur gauche de la barre d’outils). Votre application logique est enregistrée et peut être activée automatiquement.


## <a name="technical-details"></a>Détails techniques

## <a name="sql-database-actions"></a>Actions de base de données SQL
Une action est une opération effectuée par le flux de travail définie dans une application logique. Le connecteur de base de données SQL inclut les actions suivantes. 

|Action|Description|
|--- | ---|
|[ExecuteProcedure](connectors-create-api-sqlazure.md#execute-stored-procedure)|Exécute une procédure stockée dans SQL|
|[GetRow](connectors-create-api-sqlazure.md#get-row)|Extrait une seule ligne d’une table SQL|
|[GetRows](connectors-create-api-sqlazure.md#get-rows)|Extrait des lignes d’une table SQL|
|[InsertRow](connectors-create-api-sqlazure.md#insert-row)|Insère une nouvelle ligne dans une table SQL|
|[DeleteRow](connectors-create-api-sqlazure.md#delete-row)|Supprime une ligne d’une table SQL|
|[GetTables](connectors-create-api-sqlazure.md#get-tables)|Extrait les tables d’une base de données SQL|
|[UpdateRow](connectors-create-api-sqlazure.md#update-row)|Met à jour une ligne existante dans une table SQL|

### <a name="action-details"></a>Détails de l’action

Dans cette section, voir les détails relatifs à chaque action, y compris les propriétés d’entrée obligatoire ou facultatives et aucun résultat correspondant associé au connecteur.


#### <a name="execute-stored-procedure"></a>Exécuter la procédure stockée
Exécute une procédure stockée dans SQL.  

| Nom de la propriété| Nom d’affichage |Description|
| ---|---|---|
|procédure * | Nom de la procédure | Le nom de la procédure stockée que vous souhaitez exécuter |
|paramètres * | Paramètres d’entrée | Les paramètres sont dynamiques et en fonction de la procédure stockée que vous choisissez. <br/><br/> Par exemple, si vous utilisez la base de données exemple Adventure Works, choisissez la procédure *ufnGetCustomerInformation* stockées. Le paramètre d’entrée **ID client** s’affiche. Entrez « 6 » ou un des autres ID client. |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails de sortie
ProcedureResult : Effectue résultat de l’exécution de la procédure stockée

| Nom de la propriété | Type de données | Description |
|---|---|---|
|OutputParameters|objet|Valeurs de paramètre de sortie |
|Code retour|nombre entier|Code d’une procédure de retour |
|Jeux de résultats|objet| Jeux de résultats|


#### <a name="get-row"></a>Obtenir la ligne 
Extrait une seule ligne d’une table SQL.  

| Nom de la propriété| Nom d’affichage |Description|
| ---|---|---|
|table * | Nom de la table |Nom de table SQL|
|ID * | Id de ligne |Identificateur unique de la ligne à extraire|

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails de sortie
Élément

| Nom de la propriété | Type de données |
|---|---|
|ItemInternalId|chaîne|


#### <a name="get-rows"></a>Obtenir des lignes 
Extrait des lignes d’une table SQL.  

|Nom de la propriété| Nom d’affichage|Description|
| ---|---|---|
|table *|Nom de la table|Nom de table SQL|
|$skip|Nombre d’ignorer|Nombre d’entrées pour ignorer (par défaut = 0)|
|$top|Nombre maximal d’obtenir|Nombre maximal d’entrées pour récupérer (par défaut = 256)|
|$filter|Requête de filtre|Une requête de filtre ODATA pour limiter le nombre d’entrées.|
|$orderby|Tri par|Une requête orderBy ODATA permettant de spécifier l’ordre des entrées|

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails de sortie
Liste

| Nom de la propriété | Type de données |
|---|---|
|valeur|tableau|


#### <a name="insert-row"></a>Insérer une ligne 
Insère une nouvelle ligne dans une table SQL.  

|Nom de la propriété| Nom d’affichage|Description|
| ---|---|---|
|table *|Nom de la table|Nom de table SQL|
|élément *|Ligne|Ligne à insérer dans la table spécifiée dans SQL|

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails de sortie
Élément

| Nom de la propriété | Type de données |
|---|---|
|ItemInternalId|chaîne|


#### <a name="delete-row"></a>Supprimer la ligne 
Supprimer une ligne d’une table SQL.  

|Nom de la propriété| Nom d’affichage|Description|
| ---|---|---|
|table *|Nom de la table|Nom de table SQL|
|ID *|Id de ligne|Identificateur unique de la ligne à supprimer|

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails de sortie
Aucun.

#### <a name="get-tables"></a>Obtenir des tables 
Extrait les tables d’une base de données SQL.  

Il n’existe pas de paramètres pour cet appel. 

##### <a name="output-details"></a>Détails de sortie 
TablesList

| Nom de la propriété | Type de données |
|---|---|
|valeur|tableau|

#### <a name="update-row"></a>Mettre à jour de ligne 
Met à jour une ligne existante dans une table SQL.  

|Nom de la propriété| Nom d’affichage|Description|
| ---|---|---|
|table *|Nom de la table|Nom de table SQL|
|ID *|Id de ligne|Identificateur unique de la ligne mise à jour|
|élément *|Ligne|Ligne avec les valeurs mises à jour|

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails de sortie  
Élément

| Nom de la propriété | Type de données |
|---|---|
|ItemInternalId|chaîne|


### <a name="http-responses"></a>Réponses HTTP

Lorsque vous appelez les différentes actions, vous pouvez obtenir certaines réponses. Le tableau suivant présente les réponses et leur description :  

|Nom|Description|
|---|---|
|200|Bien|
|202|Accepté|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|500|Erreur interne du serveur. Erreur inconnue|
|par défaut|Échoué de l’opération.|


## <a name="next-steps"></a>Étapes suivantes

[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md). Découvrir les autres connecteurs disponibles dans les applications logique auprès de notre [liste API](apis-list.md).
