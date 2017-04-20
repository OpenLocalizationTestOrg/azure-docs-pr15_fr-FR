<properties
    pageTitle="Azure AD version 2.0 AngularJS mise en route | Microsoft Azure"
    description="Découvrez comment créer une application angulaire JS seule Page qui se connecte à des comptes d’utilisateurs avec les deux comptes Microsoft personnels et Professionnel ou scolaire."
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


# <a name="add-sign-in-to-an-angularjs-single-page-app---nodejs"></a>Ajouter de se connecter à une seule page l’application AngularJS - NodeJS

Dans cet article, nous allons ajouter connectez-vous à l’aide de comptes Microsoft optimisé pour une application AngularJS à l’aide de point de terminaison de la version 2.0 Azure Active Directory. le point de terminaison version 2.0 vous permettent d’effectuer une intégration unique dans votre application et authentifier les utilisateurs avec les comptes personnels et Professionnel ou scolaire.

Cet exemple est une application de page unique liste de tâches simple qui stocke des tâches dans un serveur principal API REST, écrit en NodeJS et sécurisés à l’aide des jetons de porteur OAuth à partir d’Azure AD.  L’application AngularJS utilisera notre ouvrir la source de bibliothèque d’authentification JavaScript [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js) pour gérer le processus de connexion entière et acquérir des jetons pour l’appel de l’API REST.  Le même modèle peut être appliqué pour s’authentifier auprès d’autres API REST, tels que le [Microsoft Graph](https://graph.microsoft.com) ou les API du Gestionnaire de ressources Azure.

> [AZURE.NOTE]
    Pas tous les scénarios Azure Active Directory et fonctionnalités sont prises en charge par le point de terminaison version 2.0.  Pour déterminer si vous devez utiliser le point de terminaison version 2.0, lisez les [limitations de la version 2.0](active-directory-v2-limitations.md).

## <a name="download"></a>Télécharger

Pour commencer, vous devez télécharger et installer [node.js](https://nodejs.org).  Puis vous pouvez cloner ou [Télécharger](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS/archive/skeleton.zip) une application structure :

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS.git
```

L’application structure inclut tout le code réutilisable pour une application AngularJS simple, mais il manque tous les éléments liés à l’identité.  Si vous ne voulez pas suivre, vous pouvez à la place cloner ou [Télécharger](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS/archive/complete.zip) l’exemple terminé.

```
git clone https://github.com/AzureADSamples/SinglePageApp-AngularJS-NodeJS.git
```

## <a name="register-an-app"></a>Enregistrer une application

Tout d’abord, créez une application dans le [Portail de l’enregistrement de l’application](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ou suivez [la procédure détaillée](active-directory-v2-app-registration.md).  Veillez à :

- Ajouter la plateforme **Web** pour votre application.
- Entrez l' **URI rediriger**. La valeur par défaut pour que cet exemple est `http://localhost:8080`.
- Laissez la case à cocher **Autoriser les flux implicite** activé. 

Copier vers le bas de l' **ID de l’Application** est affectée à votre application, vous en aurez besoin peu de temps. 

## <a name="install-adaljs"></a>Installer adal.js
Pour commencer, accédez à un projet vous avez téléchargé et installez adal.js.  Si vous avez [bower](http://bower.io/) installé, vous pouvez exécuter cette commande.  Tout lorsque les incohérences dépendance version, choisissez la version supérieure.
```
bower install adal-angular#experimental
```

Par ailleurs, vous pouvez télécharger manuellement [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal.min.js) et [adal angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal-angular.min.js).  Ajouter les deux documents la `app/lib/adal-angular-experimental/dist` répertoire.

Ouvrez le projet dans votre éditeur de texte et charger adal.js à la fin du corps de la page :

```html
<!--index.html-->

...

<script src="App/bower_components/dist/adal.min.js"></script>
<script src="App/bower_components/dist/adal-angular.min.js"></script>

...
```

## <a name="set-up-the-rest-api"></a>Configurer l’API REST

Tandis que nous allons configurer les choses, permet d’obtenir le fonctionnement de l’API REST principal.  Dans une invite de commandes, installez tous les packages nécessaires en exécutant (Vérifiez que vous êtes dans l’annuaire de niveau supérieur du projet) :

```
npm install
```

Ouvrez maintenant `config.js` et remplacer la `audience` valeur :

```js
exports.creds = {
     
     // TODO: Replace this value with the Application ID from the registration portal
     audience: '<Your-application-id>',
     
     ...
}
```

L’API REST va utiliser cette valeur pour valider les jetons reçu à partir de l’application angulaire sur demandes AJAX.  Notez que cette API REST simple stocke les données en mémoire - pour chaque fois pour arrêter le serveur, vous allez perdre toutes les tâches créés précédemment.

C’est tout le temps, nous allons intégralement traitant de fonctionnement de l’API REST.  N’hésitez pas à examiner le code, mais si vous voulez en savoir plus sur la sécurisation web API avec Azure Active Directory, consultez [cet article](active-directory-v2-devquickstarts-node-api.md). 

## <a name="sign-users-in"></a>Utilisateurs de se connecter dans
Temps d’écrire du code d’identité.  Vous avez peut-être déjà remarqué que adal.js contient un fournisseur AngularJS, qui est lue de façon optimisée avec mécanismes de routage angulaires.  Commencez par ajouter le module adal à l’application :

```js
// app/scripts/app.js

angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {

...
```

Vous pouvez maintenant initialisation le `adalProvider` avec votre ID d’Application :

```js
// app/scripts/app.js

...

adalProvider.init({
        
        // Use this value for the public instance of Azure AD
        instance: 'https://login.microsoftonline.com/', 
        
        // The 'common' endpoint is used for multi-tenant applications like this one
        tenant: 'common',
        
        // Your application id from the registration portal
        clientId: '<Your-application-id>',
        
        // If you're using IE, uncommment this line - the default HTML5 sessionStorage does not work for localhost.
        //cacheLocation: 'localStorage',
         
    }, $httpProvider);
```

Rédaction, maintenant adal.js contient toutes les informations que nécessaires pour sécuriser votre application et connectez-vous aux utilisateurs.  Pour forcer la connexion pour un itinéraire particulier dans l’application, il suffit d’une ligne de code :

```js
// app/scripts/app.js

...

}).when("/TodoList", {
    controller: "todoListCtrl",
    templateUrl: "/static/views/TodoList.html",
    requireADLogin: true, // Ensures that the user must be logged in to access the route
})

...
```

Lorsqu’un utilisateur clique sur le `TodoList` lien, adal.js redirigera automatiquement vers Azure AD pour se connecter si nécessaire.  Vous pouvez également explicitement envoyer les demandes de connexion et déconnexion en appelant adal.js dans vos contrôleurs :

```js
// app/scripts/homeCtrl.js

angular.module('todoApp')
// Load adal.js the same way for use in controllers and views   
.controller('homeCtrl', ['$scope', 'adalAuthenticationService','$location', function ($scope, adalService, $location) {
    $scope.login = function () {
        
        // Redirect the user to sign in
        adalService.login();
        
    };
    $scope.logout = function () {
        
        // Redirect the user to log out    
        adalService.logOut();
    
    };
...
```

## <a name="display-user-info"></a>Afficher les informations utilisateur
À présent que l’utilisateur est connecté, vous devrez probablement accéder aux données de l’authentification de l’utilisateur de connecté dans votre application.  Adal.js expose ces informations pour vous dans le `userInfo` objet.  Pour accéder à cet objet dans une vue, commencez par ajouter adal.js à l’étendue de la racine du contrôleur correspondant :

```js
// app/scripts/userDataCtrl.js

angular.module('todoApp')
// Load ADAL for use in view
.controller('userDataCtrl', ['$scope', 'adalAuthenticationService', function ($scope, adalService) {}]);
```

Puis vous pouvez également traiter directement la `userInfo` objet dans la vue : 

```html
<!--app/views/UserData.html-->

...

    <!--Get the user's profile information from the ADAL userInfo object-->
    <tr ng-repeat="(key, value) in userInfo.profile">
        <td>{{key}}</td>
        <td>{{value}}</td>
    </tr>
...
```

Vous pouvez également utiliser la `userInfo` objet afin de déterminer si l’utilisateur est connecté ou non.

```html
<!--index.html-->

...

    <!--Use the ADAL userInfo object to show the right login/logout button-->
    <ul class="nav navbar-nav navbar-right">
        <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
        <li><a class="btn btn-link" ng-hide="userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    </ul>
...
```

## <a name="call-the-rest-api"></a>Appel de l’API REST
Enfin, il est temps pour obtenir quelques jetons et appeler l’API REST pour créer, lire, mettre à jour et supprimer des tâches.  Bien vous savez quoi ?  Vous n’avez *rien*à faire.  Adal.js se chargent automatiquement de prise, la mise en cache et actualisation des jetons.  Il se charge également de joindre ces jetons aux demandes AJAX sortantes que vous envoyez à l’API REST.  

Comment cela fonctionne-t-il ? Il s’agit tout grâce à la magie [intercepteurs AngularJS](https://docs.angularjs.org/api/ng/service/$http), qui permet de adal.js transformer les messages http entrants et sortants.  En outre, adal.js suppose que toutes les demandes d’envoient au même domaine comme la fenêtre doit utiliser jetons destinés à l’ID de l’Application même en tant que l’application AngularJS.  C’est pourquoi nous avons utilisé le même ID de l’Application dans les deux l’application angulaire et dans l’API REST NodeJS.  Bien entendu, vous pouvez ignorer ce comportement et indiquer à adal.js pour obtenir des jetons pour les autres API REST si nécessaire - mais pour ce scénario simple fera les valeurs par défaut.

Voici un extrait de code qui indique qu’il est facile d’envoyer des demandes avec jeton porteur à partir d’Azure AD :

```js
// app/scripts/todoListSvc.js

...
return $http.get('/api/tasks');
...
```

Félicitations !  Votre application intégrée seule page Azure AD est terminée.  Alors, prendre nœud.  Il peut authentifier les utilisateurs en toute sécurité appel son serveur principal API REST à l’aide de OpenID se connecter et obtenir des informations de base sur l’utilisateur.  Prêts à l’emploi, il prend en charge tout utilisateur possédant un Account Microsoft ou d’un compte professionnel ou scolaire à partir d’Azure AD.  Essayer l’application en exécutant :

```
node server.js
```

Dans un navigateur, accédez à `http://localhost:8080`.  Se connecter à l’aide d’un compte Microsoft personnel ou un compte professionnel ou scolaire.  Ajouter des tâches à la liste des tâches de l’utilisateur et se déconnecter.  Essayez de vous connecter à l’aide de l’autre type de compte. Si vous avez besoin d’un client Azure AD pour créer des utilisateurs ou scolaires, [Apprenez à obtenir un ici](active-directory-howto-tenant.md) (c’est gratuit).

Pour poursuivre l’apprentissage sur le point de terminaison de la version 2.0, revenez à notre [guide du développeur version 2.0](active-directory-appmodel-v2-overview.md).  Pour des ressources supplémentaires, consultez :

- [Exemples de Azure sur GitHub >>](https://github.com/Azure-Samples)
- [Azure AD de dépassement de capacité de pile >>](http://stackoverflow.com/questions/tagged/azure-active-directory)
- Documentation présentée sous d’Azure AD sur [Azure.com >>](https://azure.microsoft.com/documentation/services/active-directory/)

## <a name="get-security-updates-for-our-products"></a>Obtenir des mises à jour de sécurité pour nos produits

Nous vous invitons à obtenir des notifications d’incidents de sécurité se produire en visitant [cette page](https://technet.microsoft.com/security/dd252948) et l’abonnement aux alertes avis de sécurité.
