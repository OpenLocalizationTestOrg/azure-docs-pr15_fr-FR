<properties
    pageTitle="Azure AD version 2.0 .NET Web application | Microsoft Azure"
    description="Découvrez comment créer une application Web MVC .NET qui vous permet de comptes d’utilisateurs avec les deux Account Microsoft personnel et Professionnel ou scolaire."
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
    ms.date="09/16/2016"
    ms.author="dastrock"/>

# <a name="add-sign-in-to-an-net-mvc-web-app"></a>Ajouter de se connecter à une application web MVC .NET

Avec le point de terminaison version 2.0, vous pouvez rapidement ajouter l’authentification à vos applications web avec prise en charge pour les deux comptes Microsoft personnels et Professionnel ou scolaire comptes.  Dans les applications web ASP.NET, vous pouvez le faire à l’aide de logiciels d’intermédiaires OWIN de Microsoft inclus dans .NET Framework 4.5.

> [AZURE.NOTE]
    Pas tous les scénarios Azure Active Directory et fonctionnalités sont prises en charge par le point de terminaison version 2.0.  Pour déterminer si vous devez utiliser le point de terminaison version 2.0, lisez les [limitations de la version 2.0](active-directory-v2-limitations.md).

 Ici, nous allons créer une application web qui utilise OWIN pour connecter l’utilisateur, afficher des informations sur l’utilisateur et se l’utilisateur se déconnecter de l’application.
 
 ## <a name="download"></a>Télécharger
Le code de ce didacticiel étant conservée [sur GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet).  Pour suivre, vous pouvez [Télécharger structure de l’application comme un fichier zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) ou cloner la structure :

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

L’application terminée est fournie à la fin de ce didacticiel également.

## <a name="register-an-app"></a>Enregistrer une application
Créer une nouvelle application en [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ou suivez [la procédure détaillée](active-directory-v2-app-registration.md).  Veillez à :

- Copier vers le bas de l' **Id de l’Application** affecté à votre application, vous en aurez besoin plus rapidement.
- Ajouter la plateforme **Web** pour votre application.
- Entrez l' **URI rediriger**. L’uri de redirection indique à Azure Active Directory dans lequel les réponses d’authentification doivent être dirigés - la valeur par défaut pour ce didacticiel est `https://localhost:44326/`.

## <a name="install--configure-owin-authentication"></a>Installer et configurer l’authentification OWIN
Ici, nous allons configurer logiciels intermédiaires OWIN pour utiliser le protocole d’authentification OpenID se connecter.  OWIN est utilisée pour émettre des demandes de connexion et déconnexion, gérer la session de l’utilisateur et obtenir des informations sur l’utilisateur, entre autres.

-   Pour commencer, ouvrez la `web.config` de fichier dans la racine du projet, puis entrez les valeurs de la configuration de votre application dans le `<appSettings>` section.
    -   La `ida:ClientId` est l' **Id de l’Application** affecté à votre application dans le portail d’inscription.
    -   La `ida:RedirectUri` est l' **Rediriger Uri** que vous avez entré dans le portail.

-   Ensuite, ajoutez les packages OWIN logiciels intermédiaires NuGet au projet à l’aide de la Console du Gestionnaire de Package.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

-   Ajouter un « OWIN démarrage classe » au projet appelé `Startup.cs` droite, cliquez sur le projet--> **Ajouter** --> **Nouvel élément** --> recherche pour « OWIN ».  Logiciels intermédiaires OWIN appelle la `Configuration(...)` méthode au démarrage de votre application.
-   Modifier la déclaration de classe à `public partial class Startup` -nous avons déjà implémentée partie de ce cours pour vous dans un autre fichier.  Dans la `Configuration(...)` méthode, effectuer un appel à ConfigureAuth(...) pour configurer l’authentification pour votre application web  

```C#
[assembly: OwinStartup(typeof(Startup))]

namespace TodoList_WebApp
{
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
}
```

-   Ouvrez le fichier `App_Start\Startup.Auth.cs` et mise en œuvre la `ConfigureAuth(...)` méthode.  Les paramètres que vous fournissez dans `OpenIdConnectAuthenticationOptions` servira de coordonnées de votre application pour communiquer avec Azure AD.  Vous devez également configurer l’authentification par cookies - logiciels intermédiaires OpenID connecter utilise des cookies coulisses.

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
                                     RedirectUri = redirectUri,
                                     Scope = "openid email profile",
                                     ResponseType = "id_token",
                                     PostLogoutRedirectUri = redirectUri,
                                     TokenValidationParameters = new TokenValidationParameters
                                     {
                                             ValidateIssuer = false,
                                     },
                                     Notifications = new OpenIdConnectAuthenticationNotifications
                                     {
                                             AuthenticationFailed = OnAuthenticationFailed,
                                     }
                             });
             }
```

## <a name="send-authentication-requests"></a>Envoyer les demandes d’authentification
Votre application est désormais correctement configurée pour communiquer avec le point de terminaison version 2.0 à l’aide du protocole d’authentification OpenID se connecter.  OWIN a prise en charge de tous les détails insupportable d’élaboration des messages d’authentification, la validation des jetons à partir d’Azure AD et la maintenance de session utilisateur.  Tout ce qui reste plus qu’à permettre à vos utilisateurs de se connecter et se déconnecter.

- Vous pouvez utiliser autorisez les indicateurs dans vos contrôleurs afin d’exiger cet utilisateur se connecte avant d’accéder à une page donnée.  Ouvrir `Controllers\HomeController.cs`et ajouter la `[Authorize]` balise pour le contrôleur à propos de.

```C#
[Authorize]
public ActionResult About()
{
  ...
```

-   Vous pouvez également utiliser OWIN pour soumettre directement les demandes d’authentification à partir de votre code.  Ouvrir `Controllers\AccountController.cs`.  Dans les actions SignIn() et SignOut(), émettre HIP OpenID connexion et déconnexion demandes, respectivement.

```C#
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}

// BUGBUG: Ending a session with the v2.0 endpoint is not yet supported.  Here, we just end the session with the web app.  
public void SignOut()
{
    // Send an OpenID Connect sign-out request.
    HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
    Response.Redirect("/");
}
```

-   À présent, ouvrez `Views\Shared\_LoginPartial.cshtml`.  Il s’agit de l’endroit où vous allez afficher les liens de connexion et déconnexion de votre application pour l’utilisateur et imprimez le nom d’utilisateur dans un affichage.

```HTML
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">

                @*The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves.*@

                Hello, @(System.Security.Claims.ClaimsPrincipal.Current.FindFirst("preferred_username").Value)!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

## <a name="display-user-information"></a>Afficher les informations utilisateur
Lors de l’authentification des utilisateurs avec OpenID se connecter, le point de terminaison version 2.0 renvoie un id_token à l’application qui contient [les réclamations](active-directory-v2-tokens.md#id_tokens)ou assertions relatives à l’utilisateur.  Vous pouvez utiliser ces revendications pour personnaliser votre application :

- Ouvrir le `Controllers\HomeController.cs` fichier.  Vous pouvez accéder sur les revendications de l’utilisateur de vos contrôleurs via la `ClaimsPrincipal.Current` objet principal de sécurité.

```C#
[Authorize]
public ActionResult About()
{
    ViewBag.Name = ClaimsPrincipal.Current.FindFirst("name").Value;

    // The object ID claim will only be emitted for work or school accounts at this time.
    Claim oid = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier");
    ViewBag.ObjectId = oid == null ? string.Empty : oid.Value;

    // The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves
    ViewBag.Username = ClaimsPrincipal.Current.FindFirst("preferred_username").Value;

    // The subject or nameidentifier claim can be used to uniquely identify the user
    ViewBag.Subject = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;

    return View();
}
```

## <a name="run"></a>Exécuter

Enfin, créer et exécuter votre application !   Connectez-vous à l’aide d’un Account Microsoft ou un compte professionnel ou scolaire et notez l’identité de l’utilisateur est reflétée dans la barre de navigation supérieure.  Vous avez à présent une application web sécurisée à l’aide des protocoles standard qui peuvent authentifier les utilisateurs avec leurs comptes personnels et Professionnel ou scolaire.

Pour référence, l’exemple terminé (sans vos valeurs configuration) [est fourni sous forme d’un fichier zip ici](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/complete.zip), ou vous pouvez cloner GitHub :

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez désormais déplacer vers des rubriques plus avancées.  Vous souhaiterez peut-être essayer :

[Sécuriser une API Web avec le point de terminaison de la version 2.0 >>](active-directory-devquickstarts-webapi-dotnet.md)

Pour des ressources supplémentaires, consultez :
- [Le guide du développeur version 2.0 >>](active-directory-appmodel-v2-overview.md)
- [Balise StackOverflow « azure active directory » >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Obtenir des mises à jour de sécurité pour nos produits

Nous vous invitons à obtenir des notifications d’incidents de sécurité se produire en visitant [cette page](https://technet.microsoft.com/security/dd252948) et l’abonnement aux alertes avis de sécurité.
