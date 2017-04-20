<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Découvrez comment créer une application web qui a d’abonnement, connexion et mot de passe à l’aide d’Azure Active Directory B2C."
    services="active-directory-b2c"
    documentationCenter=".net"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-ad-b2c-sign-up--sign-in-in-a-aspnet-web-app"></a>Azure AD B2C : Inscription et se connecter dans une application Web ASP.NET

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

À l’aide de B2C Azure Active Directory (AD Azure), vous pouvez ajouter des fonctionnalités de gestion des identités en libre-service puissantes à votre application web en quelques étapes. Cet article traite la création d’une application web ASP.NET qui inclut l’utilisateur d’abonnement, connexion, puis réinitialiser votre mot de passe. L’application inclura prise en charge d’abonnement et se connecter à l’aide d’un nom d’utilisateur ou de la messagerie électronique et à l’aide de comptes sociaux tels que Facebook et Google.

Ce didacticiel diffère de [notre autre didacticiel .NET web](active-directory-b2c-devquickstarts-web-dotnet.md) dans la mesure où elle utilise un [inscrit ou connecté dans stratégie](active-directory-b2c-reference-policies.md#create-a-sign-up-or-sign-in-policy) pour fournir d’inscription des utilisateurs et se connecter à l’aide d’un seul bouton, au lieu de deux (une pour inscription et l’autre pour se connecter).  En bref, une connexion ou se connecter stratégie permet aux utilisateurs de se connecter avec un compte existant si elles ont un ou en créer un autre s’il s’agit de leur première fois à l’aide de l’application.

## <a name="get-an-azure-ad-b2c-directory"></a>Obtenir un répertoire Azure AD B2C

Avant de pouvoir utiliser Azure AD B2C, vous devez créer un répertoire ou du client. Un répertoire est un conteneur pour toutes vos utilisateurs, applications, groupes et plus encore.  Si vous n’avez pas encore, [créez un répertoire B2C](active-directory-b2c-get-started.md) avant de vous continuez dans ce guide.

## <a name="create-an-application"></a>Créer une application

Ensuite, vous devez créer une application dans votre annuaire B2C. Cela donne des informations Azure AD dont il a besoin pour communiquer en toute sécurité avec votre application. Pour créer une application, suivez [ces instructions](active-directory-b2c-app-registration.md).  N’oubliez pas :

- Inclure un **site web application API** dans l’application.
- Entrez `https://localhost:44316/` comme un **Rediriger URI**. Il est l’URL par défaut pour cet exemple de code.
- Recopier vers le bas de l' **ID de l’Application** qui est affectée à votre application.  Vous en aurez besoin ultérieurement.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Créer vos stratégies

Dans Azure AD B2C, chaque expérience utilisateur est définie par une [stratégie](active-directory-b2c-reference-policies.md). Cet exemple de code contient deux expériences d’identité : **inscription & connexion**et **Réinitialiser votre mot de passe**.  Vous devez créer une stratégie de chaque type, comme décrit dans l' [article de référence de stratégie](active-directory-b2c-reference-policies.md). Lorsque vous créez les deux stratégies, veillez à :

- Sélectionnez **messagerie d’abonnement** ou **Abonnement à un ID d’utilisateur** dans la carte de fournisseurs d’identité.
- Sélectionnez le **nom d’affichage** et autres attributs d’abonnement dans votre stratégie d’abonnement et se connecter.
- Sélectionnez la demande de **nom d’affichage** en tant qu’application réclamer dans chaque stratégie. Vous pouvez choisir d’autres réclamations également.
- Copier le **nom** de chaque stratégie après sa création. Vous devez les noms de stratégie ultérieurement.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Après avoir créé vos stratégies de deux, vous êtes prêt à créer votre application.

## <a name="download-the-code-and-configure-authentication"></a>Télécharger le code et configurer l’authentification

Le code de cet exemple [étant conservées sur GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI). Pour créer l’exemple fur, vous pouvez [Télécharger le projet de structure sous forme de fichier .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI/archive/skeleton.zip). Vous pouvez également cloner la structure :

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI.git
```

L’exemple complet est également [disponible sous forme de fichier .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI/archive/complete.zip) ou sur la `complete` branche du même référentiel.

Après avoir téléchargé l’exemple de code, ouvrez le fichier .sln Visual Studio pour commencer.

Votre application communique avec Azure AD B2C en envoyant des demandes d’authentification HTTP qui spécifient la stratégie qu’il souhaite exécuter dans le cadre de la demande. Pour les applications web .NET, vous pouvez utiliser une bibliothèque OWIN de Microsoft pour envoyer les demandes d’authentification OpenID se connecter, exécuter des stratégies, gérer les sessions utilisateur et bien plus encore.

Pour commencer, ajoutez les packages OWIN logiciels intermédiaires NuGet au projet à l’aide de la Console du Gestionnaire de Package Visual Studio.

```
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb
Install-Package System.IdentityModel.Tokens.Jwt
```

Ensuite, ouvrez le `web.config` de fichier dans la racine du projet, entrez des valeurs de configuration de votre application dans le `<appSettings>` section, en remplaçant les valeurs inférieures par votre propre.  Vous pouvez être amené à la `ida:RedirectUri` et la `ida:AadInstance` valeurs comme, inchangée.

```
<configuration>
  <appSettings>

    ...

    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SusiPolicyId" value="b2c_1_susi" />
    <add key="ida:PasswordResetPolicyId" value="b2c_1_reset" />
  </appSettings>
...
```

[AZURE.INCLUDE [active-directory-b2c-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Ensuite, ajoutez une classe de démarrage OWIN au projet appelé `Startup.cs`. Avec le bouton droit sur le projet, sélectionnez **Ajouter** et **Un nouvel élément**, puis recherchez « OWIN ». Modifier la déclaration de classe à `public partial class Startup`. Nous implémentée partie de ce cours pour vous dans un autre fichier. Logiciels intermédiaires OWIN appelle la `Configuration(...)` méthode au démarrage de votre application. Dans cette méthode, passer un appel à `ConfigureAuth(...)`, où vous configurer l’authentification de votre application.

```C#
// Startup.cs

public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

Ouvrez le fichier `App_Start\Startup.Auth.cs` et mise en œuvre la `ConfigureAuth(...)` méthode.  Les paramètres que vous fournissez dans `OpenIdConnectAuthenticationOptions` servir de coordonnées de votre application pour communiquer avec Azure AD. Vous devez également configurer l’authentification des cookies. Logiciels intermédiaires OpenID connecter utilise des cookies pour mettre à jour les sessions de l’utilisateur, entre autres choses.

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // App config settings
    private static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    private static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    private static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    private static string redirectUri = ConfigurationManager.AppSettings["ida:RedirectUri"];

    // B2C policy identifiers
    public static string SusiPolicyId = ConfigurationManager.AppSettings["ida:SusiPolicyId"];
    public static string PasswordResetPolicyId = ConfigurationManager.AppSettings["ida:PasswordResetPolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseCookieAuthentication(new CookieAuthenticationOptions());

        // Configure OpenID Connect middleware for each policy
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(PasswordResetPolicyId));
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(SusiPolicyId));

    }

    private Task OnSecurityTokenValidated(SecurityTokenValidatedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
    {
        // If you wanted to keep some local state in the app (like a db of signed up users),
        // you could use this notification to create the user record if it does not already
        // exist.

        return Task.FromResult(0);
    }

    private OpenIdConnectAuthenticationOptions CreateOptionsFromPolicy(string policy)
    {
        return new OpenIdConnectAuthenticationOptions
        {
            // For each policy, give OWIN the policy-specific metadata address, and
            // set the authentication type to the id of the policy
            MetadataAddress = String.Format(aadInstance, tenant, policy),
            AuthenticationType = policy,

            // These are standard OpenID Connect parameters, with values pulled from web.config
            ClientId = clientId,
            RedirectUri = redirectUri,
            PostLogoutRedirectUri = redirectUri,
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = AuthenticationFailed,
                SecurityTokenValidated = OnSecurityTokenValidated,
            },
            Scope = "openid",
            ResponseType = "id_token",

            // This piece is optional - it is used for displaying the user's name in the navigation bar.
            TokenValidationParameters = new TokenValidationParameters
            {
                NameClaimType = "name",
            },
        };
    }
}
...
```

## <a name="send-authentication-requests-to-azure-ad"></a>Envoyer des demandes d’authentification à Azure Active Directory
Votre application est désormais correctement configurée pour communiquer avec Azure AD B2C à l’aide du protocole d’authentification OpenID se connecter.  OWIN a prise en charge de tous les détails d’élaboration des messages d’authentification, la validation des jetons à partir d’Azure AD et la maintenance de session utilisateur.  Ne reste plus qu’à démarrer une conversation de flux de chaque utilisateur.

Lorsqu’un utilisateur sélectionne **Login** ou **votre mot de passe oublié ?** dans l’application web, l’action associée est appelée dans `Controllers\AccountController.cs`. Dans les deux cas, vous pouvez utiliser les méthodes OWIN intégrées pour déclencher la stratégie de droite :

```C#
// Controllers\AccountController.cs

public void Login()
{
    if (!Request.IsAuthenticated)
    {
        // To execute a policy, you simply need to trigger an OWIN challenge.
        // You can indicate which policy to use by specifying the policy id as the AuthenticationType
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties() { RedirectUri = "/" }, Startup.SusiPolicyId);
    }
}

public void ResetPassword()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
        new AuthenticationProperties() { RedirectUri = "/" }, Startup.PasswordResetPolicyId);
    }
}
```

Pendant l’exécution de connexion ou connectez-vous stratégie, l’utilisateur a la possibilité de cliquer sur un **votre mot de passe oublié ?** lien.  Dans ce cas, Azure AD B2C vous envoie votre application de message d’erreur indiquant qu’il doit s’exécuter un mot de passe réinitialisé stratégie.  Vous pouvez capturer cette erreur dans `Startup.Auth.cs` à l’aide de la `AuthenticationFailed` notification :

```C#
// Used for avoiding yellow-screen-of-death TODO
private Task AuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    notification.HandleResponse();

    if (notification.ProtocolMessage.ErrorDescription != null && notification.ProtocolMessage.ErrorDescription.Contains("AADB2C90118"))
    {
        // If the user clicked the reset password link, redirect to the reset password route
        notification.Response.Redirect("/Account/ResetPassword");
    }
    else if (notification.Exception.Message == "access_denied")
    {
        // If the user canceled the sign in, redirect back to the home page
        notification.Response.Redirect("/");
    }
    else
    {
        notification.Response.Redirect("/Home/Error?message=" + notification.Exception.Message);
    }

    return Task.FromResult(0);
}
```


En plus de l’appelant explicitement une stratégie, vous pouvez utiliser un `[Authorize]` balise dans vos contrôleurs qui s’exécutera une stratégie si l’utilisateur n’est pas connecté. Ouvrir `Controllers\HomeController.cs` et ajouter la `[Authorize]` balise pour le contrôleur de revendications.  OWIN sélectionne le dernier stratégie configurée quand la `[Authorize]` balise est atteint.

```C#
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a certain policy if the user is not already signed into the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

Vous pouvez également utiliser OWIN pour se déconnecter de l’utilisateur de l’application. In `Controllers\AccountController.cs`:  

```C#
// Controllers\AccountController.cs

public void Logout()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request.
    if (Request.IsAuthenticated)
    {
        IEnumerable<AuthenticationDescription> authTypes = HttpContext.GetOwinContext().Authentication.GetAuthenticationTypes();
        HttpContext.GetOwinContext().Authentication.SignOut(authTypes.Select(t => t.AuthenticationType).ToArray());
        Request.GetOwinContext().Authentication.GetAuthenticationTypes();
    }
}
```

## <a name="display-user-information"></a>Afficher les informations utilisateur
Lorsque vous authentifiez les utilisateurs à l’aide de OpenID se connecter, Azure AD renvoie un jeton ID à l’application qui contient les **revendications**. Il s’agit des assertions relatives à l’utilisateur. Vous pouvez utiliser sur les revendications pour personnaliser votre application.  

Ouvrir le `Controllers\HomeController.cs` fichier. Vous pouvez accéder à utilisateur revendications dans vos contrôleurs via la `ClaimsPrincipal.Current` objet principal de sécurité.

```C#
// Controllers\HomeController.cs

[Authorize]
public ActionResult Claims()
{
    Claim displayName = ClaimsPrincipal.Current.FindFirst(ClaimsPrincipal.Current.Identities.First().NameClaimType);
    ViewBag.DisplayName = displayName != null ? displayName.Value : string.Empty;
    return View();
}
```

Vous pouvez accéder à toute demande qui reçoit votre application de la même façon.  Une liste de toutes les demandes que reçoit de l’application est disponible pour vous dans la page **revendications** .

## <a name="run-the-sample-app"></a>Exécuter l’application d’exemple

Enfin, vous pouvez créer et exécuter votre application. Inscrivez-vous à l’application à l’aide d’un nom d’utilisateur ou une adresse de messagerie. Déconnectez et vous reconnectez en tant que le même utilisateur. Modifier le profil de cet utilisateur. Se déconnecter et inscrire comme un autre utilisateur. Notez que les informations affichées sous l’onglet **revendications** correspondant aux informations que vous avez configuré sur vos stratégies.

## <a name="add-social-idps"></a>Ajouter IDPs mise en réseau

Pour l’instant, l’application prend en charge uniquement les utilisateurs d’abonnement et se connecter à l’aide de **comptes locaux**. Il s’agit des comptes stockés dans votre annuaire B2C qui utilisent un nom d’utilisateur et mot de passe. À l’aide de Azure AD B2C, vous pouvez ajouter la prise en charge pour les autres **fournisseurs d’identité** (IDPs) sans modifier votre code.

Pour ajouter IDPs sociales dans votre application, commencez en suivant les instructions détaillées dans les articles suivants. Pour chaque IDP que vous souhaitez prendre en charge, vous devez enregistrer une application de ce système et obtenir un ID client.

- [Configurer la Facebook comme un IDP](active-directory-b2c-setup-fb-app.md)
- [Configurer la Google comme un IDP](active-directory-b2c-setup-goog-app.md)
- [Configurer la Amazon comme un IDP](active-directory-b2c-setup-amzn-app.md)
- [Configurer la LinkedIn comme un IDP](active-directory-b2c-setup-li-app.md)

Après avoir ajouté les fournisseurs d’identité à votre répertoire B2C, vous devez modifier chacune de vos stratégies de trois pour inclure les nouvelle IDPs, comme décrit dans l' [article de référence de stratégie](active-directory-b2c-reference-policies.md). Après avoir enregistré votre stratégies, exécutez l’application à nouveau.  Vous devriez voir les nouveau IDPs ajoutés en tant que connexion et des expériences d’options d’abonnement dans chacune des votre identité.

Vous pouvez tester les vos stratégies et observez l’effet sur votre application exemple. Ajouter ou supprimer IDPs, manipuler revendications application ou modifier les attributs d’abonnement. Expérience jusqu'à ce que vous pouvez voir comment stratégies, les demandes d’authentification et OWIN relier.

Pour référence, l’exemple terminée (sans vos valeurs configuration) [est fourni sous forme d’un fichier .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI/archive/complete.zip). Vous pouvez également cloner GitHub :

```
git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI.git
```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->
