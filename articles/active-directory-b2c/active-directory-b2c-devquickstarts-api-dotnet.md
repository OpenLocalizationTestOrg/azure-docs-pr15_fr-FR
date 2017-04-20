<properties
    pageTitle="Azure AD B2C | Microsoft Azure"
    description="Découvrez comment créer une API Web .NET à l’aide de Azure Active Directory B2C, sécurisés à l’aide des jetons d’accès OAuth 2.0 pour l’authentification."
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
    ms.topic="hero-article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-active-directory-b2c-build-a-net-web-api"></a>Azure B2C annuaire Active : Créer un site web .NET API

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Avec B2C Azure Active Directory (AD Azure), vous pouvez sécuriser une API web à l’aide des jetons d’accès OAuth 2.0. Ces jetons autoriser vos applications client qui utilisent Azure AD B2C s’authentifier à l’API. Cet article vous explique comment créer une API de « liste de tâches » .NET modèle-Vue-contrôleur (MVC) qui permet aux utilisateurs de tâches CRUD. Le site web API est sécurisé à l’aide d’Azure AD B2C et autorise uniquement les utilisateurs authentifiés à gérer leur liste des tâches.

## <a name="create-an-azure-ad-b2c-directory"></a>Créer un répertoire Azure AD B2C

Avant de pouvoir utiliser Azure AD B2C, vous devez créer un répertoire ou du client. Un répertoire est un conteneur pour toutes vos utilisateurs, applications, groupes et plus encore. Si vous n’avez pas encore, [créez un répertoire B2C](active-directory-b2c-get-started.md) avant de vous continuez dans ce guide.

## <a name="create-an-application"></a>Créer une application

Ensuite, vous devez créer une application dans votre annuaire B2C. Cela donne des informations Azure AD dont il a besoin pour communiquer en toute sécurité avec votre application. Pour créer une application, suivez [ces instructions](active-directory-b2c-app-registration.md). N’oubliez pas :

- Inclure une **application web** ou **API web** dans l’application.
- Utiliser les **Rediriger URI** `https://localhost:44316/` pour l’application web. Il s’agit de l’emplacement par défaut du client de l’application web pour cet exemple de code.
- Copiez l' **ID de l’application** qui est affectée à votre application. Vous en aurez besoin ultérieurement.

 [AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Créer vos stratégies

Dans Azure AD B2C, chaque expérience utilisateur est définie par une [stratégie](active-directory-b2c-reference-policies.md). Le client dans cet exemple de code contient trois expériences d’identité : s’inscrire, se connecter et modifier le profil. Vous avez besoin créer une stratégie pour chaque type, comme décrit dans l' [article de référence de stratégie](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Lorsque vous créez vos stratégies de trois, veillez à :

- Sélectionnez **ID d’utilisateur d’abonnement** ou **d’abonnement à un courrier électronique** dans la carte de fournisseurs d’identité.
- Cliquez sur **nom d’affichage** et d’autres attributs d’abonnement dans votre stratégie d’inscription.
- Choisissez revendications **nom d’affichage** et **l’ID de l’objet** comme application réclamations au titre de chaque stratégie. Vous pouvez choisir d’autres réclamations également.
- Copier le **nom** de chaque stratégie après sa création. Vous aurez besoin ultérieurement ces noms de stratégie.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Une fois que vous avez créé les trois stratégies, vous êtes prêt à créer votre application.

## <a name="download-the-code"></a>Télécharger le code

Le code de ce didacticiel [étant conservées sur GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet). Pour créer l’exemple fur, vous pouvez [télécharger un projet de structure sous forme de fichier .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/skeleton.zip). Vous pouvez également cloner la structure :

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet.git
```

L’application terminée est également [disponible sous forme de fichier .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/complete.zip) ou sur la `complete` branche du même référentiel.

Après avoir téléchargé l’exemple de code, ouvrez le fichier .sln Visual Studio pour commencer. Le fichier solution contient deux projets : `TaskWebApp` et `TaskService`. `TaskWebApp`est une application web MVC que l’utilisateur interagit avec. `TaskService`est web principale de l’application API qui stocke la liste des tâches de chaque utilisateur.

## <a name="configure-the-task-web-app"></a>Configurer l’application web de tâche

Lorsqu’un utilisateur interagit avec `TaskWebApp`, le client envoie des requêtes à Azure AD et récupère des jetons qui peuvent être utilisés pour appeler le `TaskService` web API. Pour se connecter l’utilisateur et obtenir des jetons, vous devez fournir `TaskWebApp` avec des informations sur votre application. Dans la `TaskWebApp` ouvert le projet, le `web.config` de fichier dans la racine du projet et remplacer les valeurs de la `<appSettings>` section.  Vous pouvez laisser la `AadInstance`, `RedirectUri`, et `TaskServiceUrl` valeurs comme-est.

```
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
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
  </appSettings>
```

Cet article n’aborde pas construction la `TaskWebApp` client.  Pour apprendre à créer une application web à l’aide d’Azure AD B2C, consultez [notre didacticiel .NET web app](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="secure-the-api"></a>Sécuriser l’API

Lorsque vous avez un client qui appelle l’API au nom d’utilisateurs, vous pouvez protéger `TaskService` en utilisant des jetons de porteur OAuth 2.0. Votre API peut accepter et valider des jetons à l’aide Open Web Interface de Microsoft bibliothèque .NET (OWIN).

### <a name="install-owin"></a>Installer OWIN
Commencez par installer le pipeline de l’authentification OAuth OWIN :

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskService
```

### <a name="enter-your-b2c-details"></a>Entrez vos informations B2C
Ouvrir le `web.config` fichier à la racine de la `TaskService` project et remplacer les valeurs de la `<appSettings>` section. Ces valeurs seront utilisées dans l’ensemble de la bibliothèque de l’API et OWIN.  Vous pouvez laisser la `AadInstance` valeur inchangée.

```
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
  </appSettings>
```

### <a name="add-an-owin-startup-class"></a>Ajouter une classe de démarrage OWIN
Ajouter une classe de démarrage OWIN à la `TaskService` projet appelé `Startup.cs`.  Avec le bouton droit sur le projet, sélectionnez **Ajouter** et **Un nouvel élément**, puis recherchez OWIN.


```C#
// Startup.cs

// Change the class declaration to "public partial class Startup" - we’ve already implemented part of this class for you in another file.
public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

### <a name="configure-oauth-20-authentication"></a>Configurer l’authentification OAuth 2.0
Ouvrez le fichier `App_Start\Startup.Auth.cs`et mettre en œuvre la `ConfigureAuth(...)` méthode :

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // These values are pulled from web.config
    public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    public static string signUpPolicy = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
    public static string signInPolicy = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
    public static string editProfilePolicy = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {   
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(signUpPolicy));
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(signInPolicy));
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(editProfilePolicy));
    }

    public OAuthBearerAuthenticationOptions CreateBearerOptionsFromPolicy(string policy)
    {
        TokenValidationParameters tvps = new TokenValidationParameters
        {
            // This is where you specify that your API only accepts tokens from its own clients
            ValidAudience = clientId,
            AuthenticationType = policy,
        };

        return new OAuthBearerAuthenticationOptions
        {
            // This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
            AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(aadInstance, tenant, policy))),
        };
    }
}
```

### <a name="secure-the-task-controller"></a>Sécuriser le contrôleur de tâche
Une fois que l’application est configurée pour utiliser l’authentification OAuth 2.0, vous pouvez protéger votre site web API en ajoutant une `[Authorize]` balise pour le contrôleur de tâche. Il s’agit le contrôleur où tous les manipulation de liste des tâches a lieu, afin que vous devez sécuriser le contrôleur entière au niveau de classe. Vous pouvez également ajouter le `[Authorize]` balise pour des actions spécifiques pour un contrôle plus précis.

```C#
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
    ...
}
```

### <a name="get-user-information-from-the-token"></a>Obtenir des informations utilisateur à partir du jeton
`TasksController`stocke les tâches dans une base de données où chaque tâche a un utilisateur associé qui « propriétaire » de la tâche. Le propriétaire est identifié par **ID de l’objet l’utilisateur**. (C’est pourquoi vous avez besoin ajouter l’ID d’objet en tant qu’application réclamer dans l’ensemble de vos stratégies.)

```C#
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
    string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
    return userTasks;
}
```

## <a name="run-the-sample-app"></a>Exécuter l’application d’exemple

Enfin, créer et exécuter les deux `TaskWebApp` et `TaskService`. Inscrivez-vous à l’application à l’aide d’une adresse de messagerie ou le nom d’utilisateur. Créer des tâches dans la liste des tâches de l’utilisateur et notez la manière dont elles sont conservées dans l’API même une fois que vous arrêtez et redémarrez le client.

## <a name="edit-your-policies"></a>Modifier vos stratégies

Une fois que vous avez sécurisé une API à l’aide de Azure AD B2C, vous pouvez tester les stratégies de votre application et afficher les effets (ou leur absence) sur l’API. Vous pouvez manipuler les demandes d’application dans les stratégies et modifier les informations utilisateur qui sont disponibles dans le site web API. Les réclamations que vous ajoutez sera disponibles pour votre site web MVC .NET API dans la `ClaimsPrincipal` objet, comme décrit plus haut dans cet article.

<!--

## Next steps

You can now move onto more advanced B2C topics. You may try:

[Call a web API from a web app]()

[Customize the UX of your B2C app]()

-->
