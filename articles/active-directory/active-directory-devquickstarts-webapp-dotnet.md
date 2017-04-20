<properties
    pageTitle="Azure AD .NET mise en route | Microsoft Azure"
    description="Découvrez comment créer une application Web MVC .NET qui s’intègre à Azure AD pour se connecter."
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

# <a name="aspnet-web-app-sign-in--sign-out-with-azure-ad"></a>Web ASP.NET application se connecter et se déconnecter avec Azure Active Directory

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD rend plus simple à confier la gestion des identités de votre application web fournissant unique connexion et déconnexion avec uniquement quelques lignes de code.  Dans les applications web Asp.NET, vous pouvez le faire à l’aide de l’implémentation Microsoft de pilotés par la Communauté OWIN logiciels intermédiaires inclus dans .NET Framework 4.5.  Ici, nous allons utiliser OWIN pour :
-   Vous connecter à l’utilisateur à l’application à l’aide d’Azure AD en tant que le fournisseur d’identité.
-   Afficher des informations sur l’utilisateur.
-   Connectez-vous à l’utilisateur se déconnecter de l’application.

Pour ce faire, vous devez :

1. Enregistrer une application avec Azure Active Directory
2. Configurer votre application pour utiliser le pipeline de l’authentification OWIN.
3. Utilisez OWIN pour émettre des demandes de connexion et déconnexion à Azure Active Directory.
4. Afficher les données relatives à l’utilisateur.

Pour démarrer, [Téléchargez la structure de l’application](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) ou [Télécharger l’exemple terminé](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip).  Vous devez également un client Azure AD dans lesquelles vous pouvez enregistrer votre application.  Si vous n’en avez pas déjà fait, [Découvrez comment obtenir un](active-directory-howto-tenant.md).

## <a name="1--register-an-application-with-azure-ad"></a>*1. enregistrer une Application avec Azure Active Directory*
Pour permettre à votre application pour l’authentification des utilisateurs, vous devez tout d’abord enregistrer une nouvelle application dans votre client.

- Se connecter au portail de gestion Azure.
- Dans le volet de navigation gauche, cliquez sur **Active Directory**.
- Sélectionnez le client de l’endroit où vous souhaitez enregistrer l’application.
- Cliquez sur l’onglet **Applications** , et cliquez sur Ajouter dans le bac d’alimentation bas.
- Suivez les invites et créez une nouvelle **Application Web et/ou WebAPI**.
    - Le **nom** de l’application décrira votre application aux utilisateurs finaux
    -   L' **Authentification URL** est l’URL de base de votre application.  Valeur par défaut de la structure est `https://localhost:44320/`.
    - L' **Application ID URI** est un identificateur unique pour votre application.  La convention consiste à utiliser `https://<tenant-domain>/<app-name>`, par exemple :`https://contoso.onmicrosoft.com/my-first-aad-app`
- Une fois que vous avez terminé l’enregistrement, AAD affecter votre application un identificateur unique client.  Vous devez cette valeur dans les sections suivantes, donc copier à partir de l’onglet Configurer.

## <a name="2-set-up-your-app-to-use-the-owin-authentication-pipeline"></a>*2. configurer votre application pour utiliser le pipeline de l’authentification OWIN*
Ici, nous allons configurer logiciels intermédiaires OWIN pour utiliser le protocole d’authentification OpenID se connecter.  OWIN est utilisée pour émettre des demandes de connexion et déconnexion, gérer la session de l’utilisateur et obtenir des informations sur l’utilisateur, entre autres.

-   Pour commencer, ajoutez les packages OWIN logiciels intermédiaires NuGet au projet à l’aide de la Console du Gestionnaire de Package.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

-   Ajouter une classe démarrage OWIN au projet appelé `Startup.cs` droite, cliquez sur le projet--> **Ajouter** --> **Nouvel élément** --> recherche pour « OWIN ».  Logiciels intermédiaires OWIN appelle la `Configuration(...)` méthode au démarrage de votre application.
-   Modifier la déclaration de classe à `public partial class Startup` -nous avons déjà implémentée partie de ce cours pour vous dans un autre fichier.  Dans la `Configuration(...)` méthode, effectuer un appel à ConfgureAuth(...) pour configurer l’authentification pour votre application web  

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-   Ouvrez le fichier `App_Start\Startup.Auth.cs` et mise en œuvre la `ConfigureAuth(...)` méthode.  Les paramètres que vous fournissez dans `OpenIDConnectAuthenticationOptions` servira de coordonnées de votre application pour communiquer avec Azure AD.  Vous devez également configurer l’authentification par cookies - logiciels intermédiaires OpenID connecter utilise des cookies coulisses.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            ClientId = clientId,
            Authority = authority,
            PostLogoutRedirectUri = postLogoutRedirectUri,
        });
}
```

-   Enfin, ouvrez la `web.config` de fichier dans la racine du projet, puis entrez les valeurs de configuration dans la `<appSettings>` section.
    -   Votre application `ida:ClientId` est le Guid que vous avez copiée à partir du portail Azure à l’étape 1.
    -   La `ida:Tenant` est le nom de votre client Azure AD, par exemple, « contoso.onmicrosoft.com ».
    -   Votre `ida:PostLogoutRedirectUri` indique à Azure Active Directory où un utilisateur doit être redirigé une fois terminée une demande de déconnexion.

## <a name="3-use-owin-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>*3. Utilisez OWIN pour émettre des demandes de connexion et déconnexion à Azure Active Directory*
Votre application est désormais correctement configurée pour communiquer avec Azure Active Directory à l’aide du protocole d’authentification OpenID se connecter.  OWIN a prise en charge de tous les détails insupportable d’élaboration des messages d’authentification, la validation des jetons à partir d’Azure AD et la maintenance de session utilisateur.  Tout ce qui reste plus qu’à permettre à vos utilisateurs de se connecter et se déconnecter.

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
public void SignOut()
{
    // Send an OpenID Connect sign-out request.
    HttpContext.GetOwinContext().Authentication.SignOut(
        OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
}
```

-   À présent, ouvrez `Views\Shared\_LoginPartial.cshtml`.  Il s’agit de l’endroit où vous allez afficher les liens de connexion et déconnexion de votre application pour l’utilisateur et imprimez le nom d’utilisateur dans un affichage.

```HTML
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
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

## <a name="4--display-user-information"></a>*4. afficher les informations utilisateur*
Lors de l’authentification des utilisateurs avec OpenID se connecter, Azure AD retourne un id_token à l’application qui contient « revendications », ou assertions relatives à l’utilisateur.  Vous pouvez utiliser ces revendications pour personnaliser votre application :

- Ouvrir le `Controllers\HomeController.cs` fichier.  Vous pouvez accéder sur les revendications de l’utilisateur de vos contrôleurs via la `ClaimsPrincipal.Current` objet principal de sécurité.

```C#
public ActionResult About()
{
    ViewBag.Name = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Name).Value;
    ViewBag.ObjectId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    ViewBag.GivenName = ClaimsPrincipal.Current.FindFirst(ClaimTypes.GivenName).Value;
    ViewBag.Surname = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Surname).Value;
    ViewBag.UPN = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Upn).Value;

    return View();
}
```

Enfin, créer et exécuter votre application !  Si vous n’avez pas déjà, c’est le moment pour créer un nouvel utilisateur dans votre client avec une *. domaine onmicrosoft.com.  Connectez-vous à l’aide de cet utilisateur et notez l’identité de l’utilisateur est reflétée dans la barre de navigation supérieure.  Déconnectez-vous, puis reconnectez-vous en tant qu’un autre utilisateur de votre client.  Si vous avez l’impression particulièrement ambitieux, enregistrer et exécuter une autre instance de cette application (avec son propre identifiant du client) et Espion Voir authentification unique sur en action.

Pour référence, l’exemple terminée (sans vos valeurs configuration) [est fourni ici](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip).  

Vous pouvez désormais déplacer vers des rubriques plus avancées.  Vous souhaiterez peut-être essayer :

[Sécuriser un site Web API avec Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
