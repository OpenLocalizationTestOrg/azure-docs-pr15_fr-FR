<properties
    pageTitle="Azure AD AngularJS mise en route | Microsoft Azure"
    description="Découvrez comment créer une application angulaire JS seule Page qui s’intègre à Azure AD pour se connecter et appelle Azure AD protégé API utilisant OAuth."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>


# <a name="securing-angularjs-single-page-apps-with-azure-ad"></a>Sécuriser les applications de Page unique AngularJS avec Azure Active Directory

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD rend simple et rapide que vous ajoutez signe dans se déconnecter et sécuriser des appels API OAuth à vos applications de page unique.  Il permet à votre application authentifier les utilisateurs avec leur compte Active Directory et utiliser un site web API protégé par Azure AD, tels que les API d’Office 365 ou l’API Azure.

Pour les applications javascript qui s’exécutent dans un navigateur, Azure AD fournit la bibliothèque de l’authentification Active Directory, ou adal.js.  But de Adal.js de vie consiste à faciliter pour votre application obtenir des jetons d’accès.  Pour montrer simplement combien il est facile, ici nous allons créer une application AngularJS liste des tâches qui :

- Signe l’utilisateur à l’application à l’aide d’Azure AD en tant que le fournisseur d’identité.
- Affiche des informations sur l’utilisateur.
- Appelle en toute sécurité à faire liste API de l’application utilisant des jetons porteur de AAD.
- Signe l’utilisateur se déconnecter de l’application.

Pour créer l’application de travail complète, vous devez :

2. Enregistrer votre application avec Azure AD.
3. Installation d’un terme ADAL et configurer le SPA.
5. Utilisez terme ADAL pour sécuriser les pages dans le SPA.

Pour démarrer, [Téléchargez la structure de l’application](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) ou [Télécharger l’exemple terminé](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).  Vous devez également un client Azure AD dans lequel vous pouvez créer des utilisateurs et enregistrer une application.  Si vous n’avez pas déjà un client, [Découvrez comment obtenir un](active-directory-howto-tenant.md).

## <a name="1-register-the-directorysearcher-application"></a>*1. enregistrer l’Application DirectorySearcher*
Pour activer votre application authentification des utilisateurs et d’obtenir des jetons, vous devez tout d’abord l’enregistrer dans votre client Azure AD :

-   Se connecter au [portail de gestion Azure](https://manage.windowsazure.com)
-   Dans le volet de navigation gauche, cliquez sur **Active Directory**
-   Sélectionnez un client dans lesquelles vous pouvez enregistrer l’application.
-   Cliquez sur l’onglet **Applications** , puis cliquez sur **Ajouter** dans le bac d’alimentation bas.
-   Suivez les invites et créez une nouvelle **Application Web et/ou WebAPI**.
    -   Le **nom** de l’application décrira votre application aux utilisateurs finaux.
    -   **Rediriger Uri** est emplacement vers lequel AAD renverra jetons.  L’emplacement par défaut par cet exemple est`https://localhost:44326/`
-   Une fois que vous avez terminé l’enregistrement, AAD affecter votre application un **ID Client**unique.  Vous devez cette valeur dans les sections suivantes, donc copier à partir de l’onglet **configurer** .
- Adal.js utilise le flux implicite OAuth pour communiquer avec Azure AD.  Vous devez activer le flux implicite pour votre application par :
    - Téléchargez le manifeste d’application en cliquant sur **Gérer manifeste**.
    - Ouvrez le manifeste et recherchez le `oauth2AllowImplicitFlow` propriété. Définissez sa valeur sur `true`.
    - Enregistrer et télécharger le manifeste d’application en cliquant sur **Gérer manifeste** une nouvelle fois.

## <a name="2-install-adal--configure-the-spa"></a>*2. installation d’un terme ADAL et configurer le SPA*
Maintenant que vous avez une application dans Azure Active Directory, vous pouvez installer adal.js et écrivez votre code liées à l’identité.

-   Commencez par ajouter adal.js au projet TodoSPA à l’aide de la Console du Gestionnaire de Package :
  - Télécharger [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) et l’ajouter à la `App/Scripts/` répertoire du projet.
  - Télécharger [adal angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) et l’ajouter à la `App/Scripts/` répertoire du projet.
  - Charger chaque script avant la fin de la `</body>` dans `index.html`:

```js
...
<script src="App/Scripts/adal.js"></script>
<script src="App/Scripts/adal-angular.js"></script>
...
```

-   Pour serveur principal du SPA à faire liste API pour accepter des jetons à partir du navigateur, le système principal doit informations de configuration de l’inscription de l’application. Dans le projet TodoSPA, ouvrez `web.config`.  Remplacer les valeurs des éléments dans la `<appSettings>` section pour refléter les valeurs que vous saisissez dans le portail Azure.  Votre code font référence ces valeurs chaque fois qu’il utilise le terme ADAL.
    -   La `ida:Tenant` est le domaine de votre client Azure AD, par exemple contoso.onmicrosoft.com
    -   La `ida:Audience` doit être l' **ID de Client** de votre application que vous avez copiée à partir du portail.

## <a name="3--use-adal-to-secure-pages-in-the-spa"></a>*3. Utilisez terme ADAL pour sécuriser les pages dans SPA*
Adal.js a été conçu pour intégrer avec les fournisseurs de route et http AngularJS, qui vous permet de sécuriser les vues individuelles dans votre SPA.

- Dans `App/Scripts/app.js`, avancer le module adal.js :

```js
angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {
...
```
- Vous pouvez maintenant initialisation le `adalProvider` avec les valeurs de configuration de votre inscription de l’application, également dans `App/Scripts/app.js`:

```js
adalProvider.init(
  {
      instance: 'https://login.microsoftonline.com/',
      tenant: 'Enter your tenant name here e.g. contoso.onmicrosoft.com',
      clientId: 'Enter your client ID here e.g. e9a5a8b6-8af7-4719-9821-0deef255f68e',
      extraQueryParameter: 'nux=1',
      //cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
  },
  $httpProvider
);
```
- Maintenant pour sécuriser la `TodoList` afficher dans l’application, seule une ligne de code est nécessaire - `requireADLogin`.

```js
...
}).when("/TodoList", {
        controller: "todoListCtrl",
        templateUrl: "/App/Views/TodoList.html",
        requireADLogin: true,
...
```

Vous avez à présent une application de page unique sécurisée avec la possibilité de connecter les utilisateurs et envoyer de requêtes protégées jetons porteur à sa version serveur API.  Lorsqu’un utilisateur clique sur le `TodoList` lien, adal.js redirigera automatiquement vers Azure AD pour se connecter si nécessaire.  En outre, adal.js s’attache automatiquement un access_token à des demandes d’ajax qui sont envoyés au serveur principal de l’application.  Ce qui précède est au minimum nécessaire pour générer un SPA avec adal.js - vierge mais il existe un certain nombre d’autres fonctionnalités qui sont utiles dans SPAs :

- Explicitement problème se connecter et se déconnecter demandes vous pouvez définir des fonctions dans vos contrôleurs qui appellent adal.js.  In `App/Scripts/homeCtrl.js`:

```js
...
$scope.login = function () {
    adalService.login();
};
$scope.logout = function () {
    adalService.logOut();
};
...
```
- Vous souhaiterez également présente des informations utilisateur dans l’interface utilisateur de l’application.  Le service adal a déjà été ajouté à la `userDataCtrl` contrôleur, pour pouvoir accéder à la `userInfo` objet dans la vue associée, `App/Views/UserData.html`:

```js
<p>{{userInfo.userName}}</p>
<p>aud:{{userInfo.profile.aud}}</p>
<p>iss:{{userInfo.profile.iss}}</p>
...
```

- Il existe également beaucoup de scénarios dans lesquels vous souhaiterez savoir si l’utilisateur est connecté ou non.  Vous pouvez également utiliser la `userInfo` objet rassembler ces informations.  Par exemple, dans `index.html` vous pouvez afficher le bouton « Déconnexion » ou « Connexion » en fonction de l’état d’authentification :

```js
<li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
<li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
```

Félicitations ! Votre annonce Azure intégré App Page unique est terminée.  Il peut authentifier les utilisateurs en toute sécurité appel son serveur principal à l’aide de OAuth 2.0 et obtenir des informations de base sur l’utilisateur.  Si vous n’avez pas déjà, c’est le temps de remplir votre client avec certains utilisateurs.  Exécuter votre à effectuer de liste SPA, puis connectez-vous à l’aide d’un de ces utilisateurs.  Ajouter des tâches pour les utilisateurs à suivre la liste, déconnectez et vous reconnectez.

Adal.js facilite l’incorporer toutes ces fonctionnalités identité courants dans votre application.  Il s’occupe de tout le travail dirty pour vous - gestion du cache, OAuth protocoles pris en charge, proposez à l’utilisateur avec une connexion de l’interface utilisateur, l’actualisation des jetons qui a expiré et bien plus encore.

Pour référence, l’échantillon terminée (sans vos valeurs configuration) est fourni [ici](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).  Vous pouvez désormais atteindre des scénarios supplémentaires.  Vous souhaiterez peut-être essayer :

[Appeler un site Web CORS API à partir d’un SPA >>](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
