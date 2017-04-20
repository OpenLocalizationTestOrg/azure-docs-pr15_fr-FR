<properties
    pageTitle="Azure AD version 2.0 .NET Web application | Microsoft Azure"
    description="Comment créer une application Web de MVC .NET ce site web appels des services à l’aide de comptes Microsoft personnelles et travail ou connexion comptes scolaires pour."
    services="active-directory"
    documentationCenter=".net"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="dastrock"/>

# <a name="calling-a-web-api-from-a-net-web-app"></a>Appel d’un site web API à partir d’une application web .NET

Avec le point de terminaison version 2.0, vous pouvez rapidement ajouter une authentification à vos applications web et web API avec prise en charge pour les deux comptes Microsoft personnels et les comptes professionnel ou scolaire.  Ici, nous allons créer une application web MVC qui se connecte aux utilisateurs au moyen de OpenID à vous connecter, avec l’aide des logiciels intermédiaires OWIN de Microsoft.  L’application web sera obtenir des jetons d’accès OAuth 2.0 pour un site web api sécurisé par 2.0 OAuth qui permet de créer, lire et supprimer sur un utilisateur donné « liste de tâches ».

Ce didacticiel est principalement axée sur l’utilisation de MSAL pour acquérir et utiliser des jetons d’accès dans une application web décrite dans complète [ici](active-directory-v2-flows.md#web-apps).  En tant que conditions préalables, vous souhaiterez peut-être tout d’abord en savoir comment [ajouter se base dans une application web](active-directory-v2-devquickstarts-dotnet-web.md) ou comment [sécuriser une API web](active-directory-v2-devquickstarts-dotnet-api.md).

> [AZURE.NOTE]
    Pas tous les scénarios Azure Active Directory et fonctionnalités sont prises en charge par le point de terminaison version 2.0.  Pour déterminer si vous devez utiliser le point de terminaison version 2.0, lisez les [limitations de la version 2.0](active-directory-v2-limitations.md).

## <a name="download-sample-code"></a>Télécharger des exemples de code

Le code de ce didacticiel étant conservée [sur GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet).  Pour suivre, vous pouvez [Télécharger structure de l’application comme un fichier zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/skeleton.zip) ou cloner la structure :

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

Par ailleurs, vous pouvez [Télécharger l’application finale comme un fichier zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip) ou cloner l’application terminée :

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

## <a name="register-an-app"></a>Enregistrer une application
Créer une nouvelle application en [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ou suivez [la procédure détaillée](active-directory-v2-app-registration.md).  Veillez à :

- Copier vers le bas de l' **Id de l’Application** affecté à votre application, vous en aurez besoin plus rapidement.
- Créer une **Application Secret** du type de **mot de passe** et Recopier vers le bas sa valeur pour rappel ultérieur
- Ajouter la plateforme **Web** pour votre application.
- Entrez l' **URI rediriger**. L’uri de redirection indique à Azure Active Directory dans lequel les réponses d’authentification doivent être dirigés - la valeur par défaut pour ce didacticiel est `https://localhost:44326/`.


## <a name="install-owin"></a>Installer OWIN
Ajouter les packages OWIN logiciels intermédiaires NuGet à la `TodoList-WebApp` à l’aide de la Console du Gestionnaire de Package de projet.  Logiciels intermédiaires OWIN seront utilisées pour émettre des demandes de connexion et déconnexion, gérer la session de l’utilisateur et obtenir des informations sur l’utilisateur, entre autres.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Security.Cookies -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoList-WebApp
```

## <a name="sign-the-user-in"></a>Connecter l’utilisateur
Désormais configurer les logiciels intermédiaires OWIN pour utiliser le [protocole d’authentification de OpenID se connecter](active-directory-v2-protocols.md#openid-connect-sign-in-flow).  

-   Ouvrir le `web.config` fichier à la racine de la `TodoList-WebApp` du projet, puis entrez les valeurs de la configuration de votre application dans le `<appSettings>` section.
    -   La `ida:ClientId` est l' **Id de l’Application** affecté à votre application dans le portail d’inscription.
    - La `ida:ClientSecret` est le **Code Secret application** vous avez créée dans le portail d’inscription.
    -   La `ida:RedirectUri` est l' **Rediriger Uri** que vous avez entré dans le portail.
- Ouvrir le `web.config` fichier à la racine de la `TodoList-Service` du projet, puis remplacez la `ida:Audience` avec le même **Id de l’Application** comme indiqué ci-dessus.


- Ouvrez le fichier `App_Start\Startup.Auth.cs` et ajouter `using` instructions pour les bibliothèques à partir du haut.
- Dans le même fichier, mettre en œuvre la `ConfigureAuth(...)` méthode.  Les paramètres que vous fournissez dans `OpenIDConnectAuthenticationOptions` servira coordonnées de votre application pour communiquer avec Azure AD.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {

                    // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0 
                    // The `Scope` describes the permissions that your app will need.  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
                    // In a real application you could use issuer validation for additional checks, like making sure the user's organization has signed up for your app, for instance.

                    ClientId = clientId,
                    Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0 "),
                    Scope = "openid email profile offline_access",
                    RedirectUri = redirectUri,
                    PostLogoutRedirectUri = redirectUri,
                    TokenValidationParameters = new TokenValidationParameters
                    {
                        ValidateIssuer = false,
                    },

                    // The `AuthorizationCodeReceived` notification is used to capture and redeem the authorization_code that the v2.0 endpoint returns to your app.

                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed,
                        AuthorizationCodeReceived = OnAuthorizationCodeReceived,
                    }

        });
}
// ...
```

## <a name="use-msal-to-get-access-tokens"></a>MSAL permet d’obtenir les jetons d’accès
Dans la `AuthorizationCodeReceived` notification, que nous souhaitons utiliser [OAuth 2.0 en tandem avec OpenID se connecter](active-directory-v2-protocols.md#openid-connect-with-oauth-code-flow) pour échanger l’authorization_code pour un jeton d’accès au service de la liste des tâches.  MSAL peut faciliter ce processus pour vous :

- Tout d’abord, installez la version preview de MSAL :

```PM> Install-Package Microsoft.Identity.Client -ProjectName TodoList-WebApp -IncludePrerelease```
- Et ajouter un autre `using` instruction à la `App_Start\Startup.Auth.cs` fichier pour MSAL.
- Ajoutez maintenant une nouvelle méthode, la `OnAuthorizationCodeReceived` Gestionnaire d’événements.  Ce gestionnaire utilisera MSAL acquérir un jeton d’accès à l’API de la liste des tâches, mais stockera le jeton dans le cache de jetons de MSAL pour rappel ultérieur :

```C#
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
        string userObjectId = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
        string tenantID = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
        string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenantID, string.Empty);
        ClientCredential cred = new ClientCredential(clientId, clientSecret);

        // Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
        app = new ConfidentialClientApplication(Startup.clientId, redirectUri, cred, new NaiveSessionCache(userObjectId, notification.OwinContext.Environment["System.Web.HttpContextBase"] as HttpContextBase)) {};
        var authResult = await app.AcquireTokenByAuthorizationCodeAsync(new string[] { clientId }, notification.Code);
}
// ...
```

- Dans les applications web, MSAL a un cache jeton extensible qui peut servir à stocker jetons.  Cet exemple mettent en œuvre, la `NaiveSessionCache` qui utilise le stockage de session http aux jetons de cache.

<!-- TODO: Token Cache article -->


## <a name="call-the-web-api"></a>Appel de l’API Web
Maintenant, il est temps à utiliser l’access_token que vous avez acquis à l’étape 3.  Ouvrez l’application web `Controllers\TodoListController.cs` fichier, ce qui permet de toutes les demandes CRUD à l’API de la liste des tâches.

- Vous pouvez réutiliser MSAL ici pour récupérer access_tokens à partir du cache MSAL.  Tout d’abord, ajoutez un `using` déclaration MSAL à ce fichier.

    `using Microsoft.Identity.Client;`

- Dans la `Index` , MSAL d’utiliser l’action `AcquireTokenSilentAsync` méthode pour obtenir un access_token pouvant être utilisées pour lire les données à partir du service de la liste des tâches :

```C#
// ...
string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
string tenantID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, tenantID, string.Empty);
ClientCredential credential = new ClientCredential(Startup.clientId, Startup.clientSecret);

// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
app = new ConfidentialClientApplication(Startup.clientId, redirectUri, credential, new NaiveSessionCache(userObjectID, this.HttpContext)){};
result = await app.AcquireTokenSilentAsync(new string[] { Startup.clientId });
// ...
```

- L’exemple puis ajoute le jeton résultant à la demande HTTP GET en tant que la `Authorization` en-tête, le service de la liste des tâches utilise pour authentifier la demande.
- Si le service de la liste des tâches retourne un `401 Unauthorized` réponse, l’access_tokens dans MSAL sont devenus non valides pour une raison quelconque.  Dans ce cas, supprimez tout access_tokens à partir du cache MSAL et afficher un message nécessaires pour vous connecter à nouveau, qui va redémarrer le flux d’acquisition jeton pour l’utilisateur.

```C#
// ...
// If the call failed with access denied, then drop the current access token from the cache,
// and show the user an error indicating they might need to sign-in again.
if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
{
        app.AppTokenCache.Clear(Startup.clientId);

        return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
}
// ...
```

- De même, si MSAL ne peut pas retourner un access_token pour une raison quelconque, vous devez demander à l’utilisateur pour vous connecter à nouveau.  Ceci est aussi simple qu’interception d’une `MSALException`:

```C#
// ...
catch (MsalException ee)
{
        // If MSAL could not get a token silently, show the user an error indicating they might need to sign in again.
        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ee.Message + " You might need to log out and log back in.");
}
// ...
```

- La valeur exacte même `AcquireTokenSilentAsync` appel est implementd dans la `Create` et `Delete` actions.  Dans les applications web, vous pouvez utiliser cette méthode MSAL pour obtenir access_tokens chaque fois que vous avez besoin dans votre application.  MSAL se charge d’acquisition, la mise en cache et l’actualisation des jetons à votre place.

Enfin, créer et exécuter votre application !  Connectez-vous à l’aide d’un Account Microsoft ou compte Azure AD et notez l’identité de l’utilisateur est reflétée dans la barre de navigation supérieure.  Ajoutez et supprimez certains éléments de liste de tâches de l’utilisateur pour afficher que le 2.0 OAuth sécurisé appels API en action.  Vous avez à présent une application web & API web, deux sécurisées à l’aide des protocoles standard, qui peuvent s’authentifier les utilisateurs avec leurs comptes personnels et Professionnel ou scolaire.

Pour référence, l’exemple terminée (sans vos valeurs configuration) [est fourni ici](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip).  

## <a name="next-steps"></a>Étapes suivantes

Pour des ressources supplémentaires, consultez :
- [Le guide du développeur version 2.0 >>](active-directory-appmodel-v2-overview.md)
- [Balise StackOverflow « azure active directory » >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Obtenir des mises à jour de sécurité pour nos produits

Nous vous invitons à obtenir des notifications d’incidents de sécurité se produire en visitant [cette page](https://technet.microsoft.com/security/dd252948) et l’abonnement aux alertes avis de sécurité.
