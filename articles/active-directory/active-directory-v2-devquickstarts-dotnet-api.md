<properties
    pageTitle="Azure AD version 2.0 .NET Web API | Microsoft Azure"
    description="Découvrez comment créer une Api Web MVC .NET qui accepte jetons à partir de deux Account Microsoft personnel et Professionnel ou scolaire comptes."
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
    ms.date="10/10/2016"
    ms.author="dastrock"/>

# <a name="secure-an-mvc-web-api"></a>Sécuriser une web MVC API

Avec Azure Active Directory du point de terminaison version 2.0, vous pouvez protéger un API Web à l’aide des jetons d’accès [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) , qui permet aux utilisateurs avec compte Microsoft personnel et Professionnel ou scolaire comptes pour accéder en toute sécurité à votre API Web.

> [AZURE.NOTE]
    Pas tous les scénarios Azure Active Directory et fonctionnalités sont prises en charge par le point de terminaison version 2.0.  Pour déterminer si vous devez utiliser le point de terminaison version 2.0, lisez les [limitations de la version 2.0](active-directory-v2-limitations.md).

Dans web ASP.NET API, vous pouvez faire à l’aide de logiciels d’intermédiaires OWIN de Microsoft inclus dans .NET Framework 4.5.  Ici, nous allons utiliser OWIN pour générer une API Web MVC « Liste des tâches » qui permet aux clients de créer et lire des tâches à partir de la liste des tâches d’un utilisateur.  L’API web valide que les demandes entrantes contiennent un jeton d’accès valide et refuser toute demande qui ne passe pas validation sur un itinéraire protégé.  Cet exemple a été généré à l’aide de Visual Studio 2015.

## <a name="download"></a>Télécharger
Le code de ce didacticiel étant conservée [sur GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet).  Pour suivre, vous pouvez [Télécharger structure de l’application comme un fichier zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) ou cloner la structure :

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

L’application structure comprend tout le code réutilisable pour une API simple, mais il manque tous les éléments liés à l’identité. Si vous ne voulez pas suivre, vous pouvez à la place cloner ou [Télécharger l’exemple terminé](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip).

```
git clone https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

## <a name="register-an-app"></a>Enregistrer une application
Créer une nouvelle application en [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ou suivez [la procédure détaillée](active-directory-v2-app-registration.md).  Veillez à :

- Copier vers le bas de l' **Id de l’Application** affecté à votre application, vous en aurez besoin plus rapidement.

Cette solution visual studio contienne également un « TodoListClient », ce qui est une application WPF simple.  Le TodoListClient est utilisé pour vous montrer comment un utilisateur signes compléments et comment un client émettre des demandes à votre API Web.  Dans ce cas, le TodoListClient et le TodoListService sont représentés par l’application même.  Pour configurer le TodoListClient, vous devez également :

- Ajouter la plateforme **Mobile** pour votre application.


## <a name="install-owin"></a>Installer OWIN

À présent que vous avez enregistré une application, vous devez configurer votre application pour communiquer avec le point de terminaison version 2.0 afin de valider entrants demandes & jetons.

- Pour commencer, ouvrez la solution et ajoutez les packages OWIN logiciels intermédiaires NuGet au projet TodoListService à l’aide de la Console du Gestionnaire de Package.

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

## <a name="configure-oauth-authentication"></a>Configurer l’authentification OAuth

- Ajouter une classe démarrage OWIN au projet TodoListService appelé `Startup.cs`.  Droite, cliquez sur le projet--> **Ajouter** --> **Nouvel élément** --> recherche pour « OWIN ».  Logiciels intermédiaires OWIN appelle la `Configuration(…)` méthode au démarrage de votre application.
- Modifier la déclaration de classe à `public partial class Startup` -nous avons déjà implémentée partie de ce cours pour vous dans un autre fichier.  Dans la `Configuration(…)` méthode, effectuer un appel à ConfgureAuth(...) pour configurer l’authentification pour votre application web.

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

- Ouvrez le fichier `App_Start\Startup.Auth.cs` et mise en œuvre la `ConfigureAuth(…)` méthode, qui est configurée de l’API Web pour accepter des jetons à partir de la version 2.0.

```C#
public void ConfigureAuth(IAppBuilder app)
{
        var tvps = new TokenValidationParameters
        {
                // In this app, the TodoListClient and TodoListService
                // are represented using the same Application Id - we use
                // the Application Id to represent the audience, or the
                // intended recipient of tokens.

                ValidAudience = clientId,

                // In a real applicaiton, you might use issuer validation to
                // verify that the user's organization (if applicable) has
                // signed up for the app.  Here, we'll just turn it off.

                ValidateIssuer = false,
        };

        // Set up the OWIN pipeline to use OAuth 2.0 Bearer authentication.
        // The options provided here tell the middleware about the type of tokens
        // that will be recieved, which are JWTs for the v2.0 endpoint.

        // NOTE: The usual WindowsAzureActiveDirectoryBearerAuthenticaitonMiddleware uses a
        // metadata endpoint which is not supported by the v2.0 endpoint.  Instead, this
        // OpenIdConenctCachingSecurityTokenProvider can be used to fetch & use the OpenIdConnect
        // metadata document.

        app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
        {
                AccessTokenFormat = new Microsoft.Owin.Security.Jwt.JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider("https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration")),
        });
}
```

- Vous pouvez désormais utiliser `[Authorize]` attributs pour protéger vos contrôleurs et actions avec l’authentification PORTEUR OAuth 2.0.  Décorer les `Controllers\TodoListController.cs` classe avec une balise autoriser.  Cela va forcer l’utilisateur à se connecter avant d’accéder à cette page.

```C#
[Authorize]
public class TodoListController : ApiController
{
```

- Lorsqu’un appelant conseillers correctement appelle l’une de la `TodoListController` API, l’action avoir besoin d’accéder aux informations relatives à l’appelant.  OWIN permet d’accéder à la réclamation à l’intérieur du jeton PORTEUR via la `ClaimsPrincpal` objet.  

```C#
public IEnumerable<TodoItem> Get()
{
    // You can use the ClaimsPrincipal to access information about the
    // user making the call.  In this case, we use the 'sub' or
    // NameIdentifier claim to serve as a key for the tasks in the data store.

    Claim subject = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier);

    return from todo in todoBag
           where todo.Owner == subject.Value
           select todo;
}
```

-   Enfin, ouvrez la `web.config` de fichier dans la racine du projet TodoListService, puis entrez les valeurs de configuration dans la `<appSettings>` section.
  - Votre `ida:Audience` est l' **Id de l’Application** de l’application que vous avez entrées dans le portail.

## <a name="configure-the-client-app"></a>Configurer l’application client
Avant de pouvoir afficher le Service de liste Todo en action, vous devez configurer le Client de liste Todo afin de pouvoir obtenir des jetons à partir de la version 2.0 et passer des appels au service.

- Dans le projet TodoListClient, ouvrez `App.config` et entrez vos valeurs de configuration dans la `<appSettings>` section.
  - Votre `ida:ClientId` Id de l’Application que vous avez copié à partir du portail.

Pour finir, nettoyer, créer et exécuter chaque projet !  Vous avez à présent une API Web MVC .NET acceptant les jetons des deux comptes Microsoft personnels et Professionnel ou scolaire comptes.  Connectez-vous à la TodoListClient et appeler votre site web api pour ajouter des tâches à la liste des tâches de l’utilisateur.

Pour référence, l’exemple terminé (sans vos valeurs configuration) [est fourni sous forme d’un fichier zip ici](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/complete.zip), ou vous pouvez cloner GitHub :

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git```

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez désormais déplacer vers des rubriques supplémentaires.  Vous souhaiterez peut-être essayer :

[Appel d’un site Web API à partir d’une application Web >>](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)

Pour des ressources supplémentaires, consultez :
- [Le guide du développeur version 2.0 >>](active-directory-appmodel-v2-overview.md)
- [Balise StackOverflow « azure active directory » >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Obtenir des mises à jour de sécurité pour nos produits

Nous vous invitons à obtenir des notifications d’incidents de sécurité se produire en visitant [cette page](https://technet.microsoft.com/security/dd252948) et l’abonnement aux alertes avis de sécurité.
