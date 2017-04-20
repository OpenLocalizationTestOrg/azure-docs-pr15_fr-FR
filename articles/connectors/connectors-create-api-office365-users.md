<properties
    pageTitle="Ajouter le lien utilisateurs d’Office 365 dans les applications logique | Microsoft Azure"
    description="Vue d’ensemble du connecteur utilisateurs d’Office 365 avec des paramètres de l’API REST"
    services=""    
    documentationCenter=""     
    authors="msftman"    
    manager="erikre"    
    editor="" 
    tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/18/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-office-365-users-connector"></a>Prise en main du connecteur utilisateurs Office 365.

Se connecter à des utilisateurs d’Office 365 pour obtenir des profils et rechercher des utilisateurs. 

>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma logique applications 2015-08-01-aperçu.

Avec les utilisateurs d’Office 365, vous pouvez :

- Créer votre flux d’activité basé sur les données que provenant d’Office 365 utilisateurs. 
- Actions utilisation qui obtiennent des collaborateurs directs, obtenez profil utilisateur du responsable et bien plus encore. Ces actions Obtient une réponse et apportez la sortie disponibles pour d’autres actions. Par exemple, obtenir collaborateurs d’une personne, puis effectuez ces informations et mettre à jour une base de données SQL Azure. 

Pour ajouter une opération dans les applications logique, voir [créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Déclencheurs et actions

Le connecteur d’utilisateurs d’Office 365 avec les actions suivantes disponibles. Il n’existe aucun déclencheur.

| Déclencheurs | Actions|
| --- | --- |
|Aucun | <ul><li>Utilisez le Gestionnaire de</li><li>Obtenir mon profil</li><li>Obtenir des collaborateurs directs</li><li>Obtenir le profil utilisateur</li><li>Rechercher des utilisateurs</li></ul>|

Tous les connecteurs prennent en charge les données aux formats JSON et XML. 


## <a name="create-a-connection-to-office-365-users"></a>Créer une connexion à des utilisateurs d’Office 365

Lorsque vous ajoutez ce connecteur à vos applications logique, vous devez se connecter à votre compte Office 365 utilisateurs et autoriser les applications logique pour vous connecter à votre compte.

>[AZURE.INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]

Après avoir créé la connexion, vous entrez les propriétés des utilisateurs d’Office 365, tels que l’ID utilisateur. La **référence de l’API REST** dans cette rubrique décrit ces propriétés.

>[AZURE.TIP] Vous pouvez utiliser cette connexion utilisateurs d’Office 365 même dans les autres applications logique.


## <a name="office-365-users-rest-api-reference"></a>Référence des API REST utilisateurs Office 365
S’applique aux versions : 1.0.

### <a name="get-my-profile"></a>Obtenir mon profil 
Extrait le profil de l’utilisateur actuel.  
```GET: /users/me``` 

Il n’existe pas de paramètres pour cet appel.

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|
|202|Opération réussie|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|par défaut|Échoué de l’opération.|


### <a name="get-user-profile"></a>Obtenir le profil utilisateur 
Extraire un profil utilisateur spécifique.  
```GET: /users/{userId}``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|ID d’utilisateur|chaîne|Oui|chemin d’accès|Aucun|Id utilisateur nom ou de messagerie principal|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|
|202|Opération réussie|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|par défaut|Échoué de l’opération.|


### <a name="get-manager"></a>Utilisez le Gestionnaire de 
Extrait le profil utilisateur pour le Gestionnaire de l’utilisateur spécifié.  
```GET: /users/{userId}/manager``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|ID d’utilisateur|chaîne|Oui|chemin d’accès|Aucun|Id utilisateur nom ou de messagerie principal|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|
|202|Opération réussie|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|par défaut|Échoué de l’opération.|



### <a name="get-direct-reports"></a>Obtenir des collaborateurs directs 
Obtenir des collaborateurs directs.  
```GET: /users/{userId}/directReports``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|ID d’utilisateur|chaîne|Oui|chemin d’accès|Aucun|Id utilisateur nom ou de messagerie principal|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|
|202|Opération réussie|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|par défaut|Échoué de l’opération.|



### <a name="search-for-users"></a>Rechercher des utilisateurs 
Résultats de profils utilisateur de recherche récupère.  
```GET: /users``` 

| Nom| Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|searchTerm|chaîne|aucune|requête|Aucun|Chaîne de recherche (s’applique à : nom complet, prénom, nom, courrier, messagerie surnom et principal nom d’utilisateur)|

#### <a name="response"></a>Réponse

|Nom|Description|
|---|---|
|200|Opération réussie|
|202|Opération réussie|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|par défaut|Échoué de l’opération.|



## <a name="object-definitions"></a>Définitions d’objets

#### <a name="user-user-model-class"></a>Utilisateur : Classe du modèle utilisateur

|Nom de la propriété | Type de données |Obligatoire
|---|---|---|
|Nom complet|chaîne|aucune|
|Prénom|chaîne|aucune|
|Nom de famille|chaîne|aucune|
|Courrier|chaîne|aucune|
|MailNickname|chaîne|aucune|
|Numéro de téléphone|chaîne|aucune|
|AccountEnabled|valeur booléenne|aucune|
|ID|chaîne|Oui
|UserPrincipalName|chaîne|aucune|
|Département|chaîne|aucune|
|JobTitle|chaîne|aucune|
|mobilePhone|chaîne|aucune|


## <a name="next-steps"></a>Étapes suivantes

[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

Revenez à la [liste des API](apis-list.md).

<!--References-->
[5]: https://portal.azure.com
[7]: ./media/connectors-create-api-office365-users/aad-tenant-applications.PNG
[8]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/connectors-create-api-office365-users/contoso-aad-app.PNG
[11]: ./media/connectors-create-api-office365-users/contoso-aad-app-configure.PNG
