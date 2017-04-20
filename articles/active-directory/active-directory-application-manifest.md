<properties
   pageTitle="Comprendre le manifeste d’Application Azure Active Directory | Microsoft Azure"
   description="Couverture détaillés du manifeste d’application Azure Active Directory, qui représente la configuration d’identité d’une application d’un client Azure AD et est utilisé pour faciliter OAuth d’autorisation, expérience consentement et plus encore."
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/11/2016"
   ms.author="dkershaw;bryanla"/>

# <a name="understanding-the-azure-active-directory-application-manifest"></a>Comprendre le manifeste d’application Azure Active Directory

Applications qui s’intègrent avec Azure Active Directory (AD) doivent être enregistrées avec un client Azure AD, fournir une configuration permanente identité de l’application. Cette configuration est consultée en cours d’exécution, l’activation de scénarios qui permettent à une application de confier et broker authentification/autorisation via Azure AD. Pour plus d’informations sur le modèle d’application Azure AD, voir [Ajout, mise à jour et suppression d’une Application] [ ADD-UPD-RMV-APP] article.

## <a name="updating-an-applications-identity-configuration"></a>Mise à jour de la configuration d’identité d’une application

En fait plusieurs options sont disponibles pour mettre à jour les propriétés de configuration d’identité d’une application, qui peuvent varier dans les fonctionnalités et degrés de difficulté, y compris les éléments suivants :

- La ** [Azure portail classique] [ AZURE-CLASSIC-PORTAL] interface utilisateur Web** vous permet de mettre à jour les propriétés les plus courantes d’une application. Cette erreur le plus rapide et le moins de manière propice de mise à jour des propriétés de votre application, mais ne vous donne pas accès complet à toutes les propriétés, comme les deux méthodes suivantes.
- Pour des scénarios plus avancées dans laquelle vous devez mettre à jour les propriétés qui ne sont pas exposées dans le portail classique Azure, vous pouvez modifier le **manifeste d’application**. C’est l’objectif de cet article et est décrit plus en détail démarrage dans la section suivante.
- Il est également possible de **écrire une application qui utilise l' [API Graph] [ GRAPH-API] ** mettre à jour votre application, ce qui nécessite le meilleur effort. Cela peut être une option intéressante bien que, si vous écrivez du logiciel de gestion, ou que vous devez mettre à jour les propriétés de l’application régulièrement de manière automatique.

## <a name="using-the-application-manifest-to-update-an-applications-identity-configuration"></a>À l’aide du manifeste d’application pour mettre à jour de la configuration d’identité d’une application
À partir du [portail classique Azure][AZURE-CLASSIC-PORTAL], vous pouvez gérer la configuration de l’identité de votre application, en téléchargeant et télécharger un JSON fichier représentation, qui est appelée un manifeste d’application. Aucun fichier réel n’est stocké dans le répertoire. Le manifeste d’application est simplement une opération HTTP GET sur l’entité Application API Azure AD Graph et le téléchargement est une opération HTTP correctif sur l’entité de l’Application.

Par conséquent, afin de mieux comprendre les mettre en forme et les propriétés du manifeste d’application, vous devrez faire référence à l' API Graph [entité d’Application] [ APPLICATION-ENTITY] documentation. Voici quelques exemples de mises à jour qui peuvent être exécutées bien que téléchargement manifeste d’application :

- **Étendues autorisation Declare (oauth2Permissions)** exposé par votre site web API. Consultez la rubrique « Exposant API aux autres Applications Web » dans les [Applications intégration avec Azure Active Directory] [ INTEGRATING-APPLICATIONS-AAD] pour plus d’informations sur l’implémentation d’emprunt d’identité utilisateur à l’aide de l’oauth2Permissions déléguée étendue d’autorisation. Comme mentionné précédemment, propriétés entité d’Application sont présentées dans le graphique API [entité et Type complexe] [ APPLICATION-ENTITY] article de référence, y compris la propriété oauth2Permissions qui est une collection de type [OAuth2Permission][APPLICATION-ENTITY-OAUTH2-PERMISSION].
- **Rôles d’application Declare (appRoles) exposés par votre application**. Propriété d’appRoles de l’entité de l’Application est une collection de type [AppRole][APPLICATION-ENTITY-APP-ROLE]. Consultez le [contrôle d’accès dans les applications cloud Azure AD basée sur les rôles] [ RBAC-CLOUD-APPS-AZUREAD] article pour obtenir un exemple de mise en œuvre.
- **Declare connus client applications (knownClientApplications)**, ce qui vous permettent de lier logiquement l’autorisation des applications à la ressource/du web API client spécifié.
- **Demander Azure AD pour émettre groupe appartenances revendiquer** pour l’utilisateur connecté (groupMembershipClaims).  Cela peut également être configuré pour émettre des revendications sur appartenances de rôles d’annuaire de l’utilisateur. Consultez l' [autorisation dans les Applications Cloud à l’aide de groupes d’annonces] [ AAD-GROUPS-FOR-AUTHORIZATION] article pour obtenir un exemple de mise en œuvre.
- **Autoriser votre application pour prendre en charge OAuth 2.0 implicite accorder** flux (oauth2AllowImplicitFlow). Ce type de flux grant est utilisé avec des pages web incorporées JavaScript ou Applications de Page unique (SPA). Pour plus d’informations sur l’octroi d’autorisations implicites, voir [Présentation de l’oauth2 n’implicite accorder flux dans Azure Active Directory][IMPLICIT-GRANT].
- **Activer l’utilisation des X509 certificats comme clé secrète** (keyCredentials). Voir [créer des applications de service et processus dans Office 365] [ O365-SERVICE-DAEMON-APPS] et [guide du développeur pour auth avec l’API Gestionnaire de ressources Azure] [ DEV-GUIDE-TO-AUTH-WITH-ARM] articles pour des exemples de mise en œuvre.
- **Ajouter un nouvel URI ID application** pour votre application (identifierURIs[]). Application ID URI sont utilisés pour identifier une application au sein de son client Azure AD (ou entre plusieurs clients Azure AD, pour les scénarios clients multiples lorsque qualifié via un domaine personnalisé vérifié). Ils sont utilisés lorsque la demande d’autorisations pour une application de ressource ou d’acquérir un jeton d’accès pour une application de ressources. Lorsque vous mettez à jour cet élément, la même mise à jour est effectuée servicePrincipalNames [] collection de sites de l’entité de service correspondantes, qui se trouve dans le client accueil de l’application.

Le manifeste d’application fournit également un bon moyen de suivre l’état de l’inscription de votre application. Car il n’est disponible au format JSON, la représentation peut être archivée dans le contrôle de source, ainsi que le code source de votre application.

## <a name="step-by-step-example"></a>Étape par exemple étape
Vous permet de suivre les étapes nécessaires pour mettre à jour de la configuration d’identité de votre application par le biais du manifeste d’application. Nous un des exemples précédents, surligne montrant comment déclarer une nouvelle étendue d’autorisation sur une application ressource :

1. Accédez au [portail classique Azure] [ AZURE-CLASSIC-PORTAL] et vous connecter avec un compte doté des privilèges administrateur ou administrateur de co-création de service.

2. Une fois que vous avez authentifié, faites défiler vers le bas et sélectionnez l’extension Azure « Active Directory » dans le volet de navigation gauche (1), puis cliquez sur le client Azure AD où se trouve votre application enregistré (2).

    ![Sélectionnez le client Azure AD][SELECT-AZURE-AD-TENANT]

3. Lorsque la page annuaire s’affiche, cliquez sur « Applications » (1) en haut de la page pour afficher la liste des applications enregistrées dans le client. Recherchez ensuite l’application que vous voulez mettre à jour dans la liste et cliquez dessus (2).

    ![Sélectionnez le client Azure AD][SELECT-AZURE-AD-APP]

4. À présent que vous avez sélectionné la page principale de l’application, notez la fonctionnalité « Gérer manifeste » en bas de la page (1). Si vous cliquez sur ce lien, vous devrez télécharger ou télécharger le fichier manifest JSON. Cliquez sur « Télécharger manifeste » (2). Cela sera immédiatement suivi avec la boîte de dialogue de confirmation de téléchargement vous invitant à confirmer en cliquant sur « Télécharger manifeste » (3), puis ouvrez ou enregistrez le fichier localement (4).

    ![Gérer le manifeste, l’option de téléchargement][MANAGE-MANIFEST-DOWNLOAD]

    ![Télécharger le manifeste][DOWNLOAD-MANIFEST]

5. Dans cet exemple, nous avons enregistré le fichier localement, ce qui nous permet d’ouvrir dans un éditeur, apporter des modifications à la JSON et enregistrez de nouveau. Voici à quoi ressemble la structure JSON à l’intérieur de l’éditeur Visual Studio JSON. Remarquez qu’il suit le schéma pour l' [entité d’Application] [ APPLICATION-ENTITY] comme mentionné précédemment :

    ![Mettre à jour le manifest JSON][UPDATE-MANIFEST]

    Par exemple, en supposant que nous souhaitons mettre en œuvre/exposent d’une autorisation appelée « Employees.Read.All » sur notre application de ressources (API), vous souhaitez simplement ajouter un nouveau/deuxième élément à la collection oauth2Permissions, Internet Explorer :

        "oauth2Permissions": [
        {
        "adminConsentDescription": "Allow the application to access MyWebApplication on behalf of the signed-in user.",
        "adminConsentDisplayName": "Access MyWebApplication",
        "id": "aade5b35-ea3e-481c-b38d-cba4c78682a0",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to access MyWebApplication on your behalf.",
        "userConsentDisplayName": "Access MyWebApplication",
        "value": "user_impersonation"
        },
        {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
        }
        ],

    L’entrée doit être unique, et vous devez donc générer un nouveau globalement ID Unique (GUID) pour la `"id"` propriété. Dans ce cas, comme nous avons spécifié `"type": "User"`, cette autorisation peut être acceptée pour par n’importe quel compte authentifié par Azure Active Directory du client dans lequel l’application API des ressources est enregistrée. Cela permet d’octroyer le client application l’autorisation d’y accéder en nom du compte. Les chaînes de nom description et l’affichage sont utilisées pendant consentement et pour l’affichage dans le portail classique Azure.

6. Lorsque vous avez terminé la mise à jour le manifeste, revenir à la page d’application dans le portail classique Azure Azure AD, puis cliquez sur la « Gérer manifeste » fonctionnalité à nouveau (1). Mais cette fois-ci, sélectionnez l’option « Télécharger manifeste » (2). Comme pour le téléchargement, vous êtes accueilli à nouveau avec une deuxième boîte de dialogue vous invitant à l’emplacement du fichier JSON. Cliquez sur « Rechercher le fichier... » (3), puis utilisez la boîte de dialogue « Choisissez fichier à télécharger » pour sélectionner le fichier JSON (4), puis appuyez sur « Ouvrir ». Une fois que la boîte de dialogue disparaît, activez la case à cocher de « OK » (5) et votre manifeste est téléchargée.  

    ![Gérer le manifeste, options de téléchargement][MANAGE-MANIFEST-UPLOAD]

    ![Télécharger le JSON manifest][UPLOAD-MANIFEST]

    ![Télécharger le manifest JSON - confirmation][UPLOAD-MANIFEST-CONFIRM]

À présent que le manifeste est enregistré, vous pouvez lui donner un accès aux applications client pour la nouvelle autorisation nous avons ajouté ci-dessus. Cette fois, vous pouvez utiliser l’interface utilisateur du Web du portail classique Azure au lieu de modification manifeste de l’application cliente :  

1. Tout d’abord accédez à la page « Configurer » de l’application cliente à laquelle vous souhaitez ajouter l’accès à la nouvelle API, puis cliquez sur le bouton « Ajouter l’application ».
2. Puis s’affiche avec la liste des applications de ressource inscrit (API) dans le client. Cliquez sur le signe plus / symbole + en regard du nom de l’application de la ressource pour la sélectionner.  
3. Puis cliquez sur la coche située dans le coin inférieur droit.
4. Lorsque vous revenez à la section « Ajouter une Application » de la page de configuration de votre client, vous verrez la nouvelle application de ressources dans la liste. Si vous pointez sur la section « Délégué les autorisations » à droite de cette ligne, vous verrez une liste déroulante apparaissant. Cliquez sur la liste, puis sélectionnez l’autorisation de nouveau afin d’ajouter à la liste du client demandées d’autorisations. Remarque : cette nouvelle autorisation sera stockée dans la configuration d’identité de l’application cliente, dans la propriété de collection de sites « requiredResourceAccess ».

![Autorisations à d’autres applications][PERMS-TO-OTHER-APPS]

![Autorisations à d’autres applications][PERMS-SELECT-APP]

![Autorisations à d’autres applications][PERMS-SELECT-PERMS]

Voilà. À présent vos applications seront exécutent à l’aide de leur nouvelle configuration identité.

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur la relation entre les objets d’Application et Principal de Service d’une application, voir [Application et objets principal du service dans Azure AD][AAD-APP-OBJECTS].
- Consultez le [Glossaire de développeur Azure AD] [ AAD-DEVELOPER-GLOSSARY] définition de certains les concepts fondamentaux Azure Active Directory (AD) pour les développeurs.

Utilisez la section commentaires DISQUS ci-dessous pour fournir des commentaires et aidez-nous à affiner et mettre en forme notre contenu.

<!--Image references-->
[DOWNLOAD-MANIFEST]: ./media/active-directory-application-manifest/download-manifest.png
[MANAGE-MANIFEST-DOWNLOAD]: ./media/active-directory-application-manifest/manage-manifest-download.png
[MANAGE-MANIFEST-UPLOAD]: ./media/active-directory-application-manifest/manage-manifest-upload.png
[PERMS-SELECT-APP]: ./media/active-directory-application-manifest/portal-perms-select-app.png
[PERMS-SELECT-PERMS]: ./media/active-directory-application-manifest/portal-perms-select-perms.png
[PERMS-TO-OTHER-APPS]: ./media/active-directory-application-manifest/portal-perms-to-other-apps.png
[SELECT-AZURE-AD-APP]: ./media/active-directory-application-manifest/select-azure-ad-application.png
[SELECT-AZURE-AD-TENANT]: ./media/active-directory-application-manifest/select-azure-ad-tenant.png
[UPDATE-MANIFEST]: ./media/active-directory-application-manifest/update-manifest.png
[UPLOAD-MANIFEST]: ./media/active-directory-application-manifest/upload-manifest.png
[UPLOAD-MANIFEST-CONFIRM]: ./media/active-directory-application-manifest/upload-manifest-confirm.png

<!--article references -->
[AAD-APP-OBJECTS]: active-directory-application-objects.md
[AAD-DEVELOPER-GLOSSARY]: active-directory-dev-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-CLASSIC-PORTAL]: https://manage.windowsazure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]: active-directory-dev-understanding-oauth2-implicit-grant.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

