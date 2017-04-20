<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Découvrez comment créer une application web qui a se connecter, d’abonnement, et gestion des profils à l’aide de Azure Active Directory B2C."
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

# <a name="azure-ad-b2c-build-a-net-web-app"></a>Azure AD B2C : Créer une application web .NET

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

À l’aide de B2C Azure Active Directory (AD Azure), vous pouvez ajouter des fonctionnalités de gestion des identités en libre-service puissantes à votre application web en quelques étapes. Cet article présentent comment créer une application web .NET modèle-Vue-contrôleur (MVC) qui inclut des utilisateurs d’abonnement, connexion et la gestion des profils. L’application inclura prise en charge d’abonnement et se connecter à l’aide d’un nom d’utilisateur ou de la messagerie électronique et à l’aide de comptes sociaux tels que Facebook et Google.

## <a name="get-an-azure-ad-b2c-directory"></a>Obtenir un répertoire Azure AD B2C

Avant de pouvoir utiliser Azure AD B2C, vous devez créer un répertoire ou du client. Un répertoire est un conteneur pour toutes vos utilisateurs, applications, groupes et plus encore.  Si vous n’avez pas encore, [créez un répertoire B2C](active-directory-b2c-get-started.md) avant de vous continuez dans ce guide.

## <a name="create-an-application"></a>Créer une application

Ensuite, vous devez créer une application dans votre annuaire B2C. Cela donne des informations Azure AD dont il a besoin pour communiquer en toute sécurité avec votre application. Pour créer une application, suivez [ces instructions](active-directory-b2c-app-registration.md).  N’oubliez pas :

- Inclure un **site web application API** dans l’application.
- Entrez `https://localhost:44316/` comme un **Rediriger URI**. Il est l’URL par défaut pour cet exemple de code.
- Recopier vers le bas de l' **ID de l’Application** qui est affectée à votre application.  Vous en aurez besoin ultérieurement.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Créer vos stratégies

Dans Azure AD B2C, chaque expérience utilisateur est définie par une [stratégie](active-directory-b2c-reference-policies.md). Cet exemple de code contient trois expériences d’identité : s’inscrire, se connecter et modifier le profil. Vous devez créer une stratégie de chaque type, comme décrit dans l' [article de référence de stratégie](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy).

>[AZURE.NOTE] Azure AD B2C prend également en charge un combiné signe vers le haut ou dans la stratégie qui n’est pas inclu dans ce didacticiel.  La connexion ou se connecter stratégie est présentée dans [ce didacticiel équivalent](active-directory-b2c-devquickstarts-web-dotnet-susi.md).

Lorsque vous créez les trois stratégies, veillez à :

- Sélectionnez **messagerie d’abonnement** ou **Abonnement à un ID d’utilisateur** dans la carte de fournisseurs d’identité.
- Sélectionnez le **nom d’affichage** et autres attributs d’abonnement dans votre stratégie d’inscription.
- Sélectionnez la demande de **nom d’affichage** en tant qu’application réclamer dans chaque stratégie. Vous pouvez choisir d’autres réclamations également.
- Copier le **nom** de chaque stratégie après sa création. Vous devez les noms de stratégie ultérieurement.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Après avoir créé vos stratégies de trois, vous êtes prêt à créer votre application.  

## <a name="download-the-code-and-configure-authentication"></a>Télécharger le code et configurer l’authentification

Le code de cet exemple [étant conservées sur GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet). Pour créer l’exemple fur, vous pouvez [Télécharger le projet de structure sous forme de fichier .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip). Vous pouvez également cloner la structure :

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

L’exemple complet est également [disponible sous forme de fichier .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip) ou sur la `complete` branche du même référentiel.

Après avoir téléchargé l’exemple de code, ouvrez le fichier .sln Visual Studio pour commencer.

Votre application communique avec Azure AD B2C en envoyant des messages d’authentification qui spécifient la stratégie qu’ils souhaitent exécuter dans le cadre de la requête HTTP. Pour les applications web .NET, vous pouvez utiliser les logiciels intermédiaires OWIN de Microsoft pour envoyer les demandes d’authentification OpenID se connecter, exécuter des stratégies, gérer les sessions utilisateur et bien plus encore.

Pour commencer, ajoutez les packages OWIN logiciels intermédiaires NuGet au projet à l’aide de la Console du Gestionnaire de Package Visual Studio.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

Ensuite, ouvrez le `web.config` de fichier dans la racine du projet, entrez des valeurs de configuration de votre application dans le `<appSettings>` section, remplacer les valeurs existantes.

```
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
  </appSettings>
...
```

[AZURE.INCLUDE [active-directory-b2c-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Ensuite, ajoutez une classe de démarrage OWIN au projet appelé `Startup.cs`. Avec le bouton droit sur le projet, sélectionnez **Ajouter** et **Un nouvel élément**, puis recherchez « OWIN ». **N’oubliez pas de modifier la déclaration de classe à `public partial class Startup` **. Nous implémentée partie de ce cours pour vous dans un autre fichier. Logiciels intermédiaires OWIN appelle la `Configuration(...)` méthode au démarrage de votre application. Dans cette méthode, passer un appel à `ConfigureAuth(...)`, où vous configurer l’authentification de votre application.

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
    public static string SignUpPolicyId = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
    public static string SignInPolicyId = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
    public static string ProfilePolicyId = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseCookieAuthentication(new CookieAuthenticationOptions());

        // Configure OpenID Connect middleware for each policy
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(SignUpPolicyId));
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(ProfilePolicyId));
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(SignInPolicyId));
    }

    // Used for avoiding yellow-screen-of-death
    private Task AuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
    {
        notification.HandleResponse();
        if (notification.Exception.Message == "access_denied")
        {
            notification.Response.Redirect("/");
        }
        else
        {
            notification.Response.Redirect("/Home/Error?message=" + notification.Exception.Message);
        }

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
```

## <a name="send-authentication-requests-to-azure-ad"></a>Envoyer des demandes d’authentification à Azure Active Directory
Votre application est désormais correctement configurée pour communiquer avec Azure AD B2C à l’aide du protocole d’authentification OpenID se connecter.  OWIN a prise en charge de tous les détails d’élaboration des messages d’authentification, la validation des jetons à partir d’Azure AD et la maintenance de session utilisateur.  Ne reste plus qu’à démarrer une conversation de flux de chaque utilisateur.

Lorsqu’un utilisateur sélectionne **inscrit**, **Connectez-vous**ou **Modifier le profil** dans l’application web, l’action associée est appelée dans `Controllers\AccountController.cs`. Dans les deux cas, vous pouvez utiliser les méthodes OWIN intégrées pour déclencher la stratégie de droite :

```C#
// Controllers\AccountController.cs

public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        // To execute a policy, you simply need to trigger an OWIN challenge.
        // You can indicate which policy to use by specifying the policy id as the AuthenticationType
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties () { RedirectUri = "/" }, Startup.SignInPolicyId);
    }
}

public void SignUp()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties() { RedirectUri = "/" }, Startup.SignUpPolicyId);
    }
}


public void Profile()
{
    if (Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties() { RedirectUri = "/" }, Startup.ProfilePolicyId);
    }
}
```

Vous pouvez également utiliser un `[Authorize]` balise dans vos contrôleurs qui requiert l’exécution d’une certaine stratégie si l’utilisateur n’est pas connecté. Ouvrir `Controllers\HomeController.cs` et ajouter la `[Authorize]` balise pour le contrôleur de revendications.  OWIN permet de sélectionner la dernière stratégie configurée pour exécuter lorsque la balise autoriser est appelée.

```C#
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a policy if the user is not already signed in the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

Vous pouvez également utiliser OWIN pour se déconnecter de l’utilisateur de l’application. In `Controllers\AccountController.cs`:  

```C#
// Controllers\AccountController.cs

public void SignOut()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request
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

Pour référence, l’exemple terminée (sans vos valeurs configuration) [est fourni sous forme d’un fichier .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip). Vous pouvez également cloner GitHub :

```
git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->
