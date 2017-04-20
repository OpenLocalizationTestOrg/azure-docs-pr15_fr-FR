<properties
    pageTitle="Azure AD .NET mise en route | Microsoft Azure"
    description="Découvrez comment créer une API Web MVC .NET qui s’intègre à Azure AD pour l’authentification et d’autorisation."
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


# <a name="protect-a-web-api-using-bearer-tokens-from-azure-ad"></a>Protéger une API Web utilisant des jetons porteur d’Azure Active Directory

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Si vous créez une application qui permet d’accéder à des ressources protégées, vous avez besoin de savoir comment protéger ces ressources à partir d’access injustifiés.
Azure AD rend plus simple à protéger un site web à l’aide des jetons d’accès OAuth PORTEUR 2.0 avec uniquement quelques lignes de code de l’API.

Dans les applications web Asp.NET, vous pouvez le faire à l’aide de l’implémentation Microsoft de pilotés par la Communauté OWIN logiciels intermédiaires inclus dans .NET Framework 4.5.  Ici, nous allons utiliser OWIN pour créer un site web « Liste des tâches » API qui :
-   Indique quelle API est protégés.
-   Vérifie que les appels d’API Web contiennent un jeton d’accès valide.

Pour ce faire, vous devez :

1. Enregistrer une application avec Azure Active Directory
2. Configurer votre application pour utiliser le pipeline de l’authentification OWIN.
3. Configurer une application cliente pour appeler l’à faire liste API Web

Pour démarrer, [Téléchargez la structure de l’application](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip) ou [Télécharger l’exemple terminé](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip).  Chacun est une solution Visual Studio 2013.  Vous devez également un client Azure AD dans lesquelles vous pouvez enregistrer votre application.  Si vous n’en avez pas déjà fait, [Découvrez comment obtenir un](active-directory-howto-tenant.md).


## <a name="1--register-an-application-with-azure-ad"></a>*1. enregistrer une Application avec Azure Active Directory*
Pour sécuriser votre application, vous devez tout d’abord créer une application de votre client et lui affecter Azure AD avec quelques informations essentielles.

-   Se connecter au [portail de gestion Azure](https://manage.windowsazure.com)
-   Dans le volet de navigation gauche, cliquez sur **Active Directory**
-   Sélectionnez un client dans lesquelles vous pouvez enregistrer l’application.
-   Cliquez sur l’onglet **Applications** , puis cliquez sur **Ajouter** dans le bac d’alimentation bas.
-   Suivez les invites et créez une nouvelle **Application Web et/ou WebAPI**.
    -   Le **nom** de l’application décrira votre application aux utilisateurs finaux.  Entrez « Pour la liste des tâches Service ».
    -   L' **Rediriger Uri** est une combinaison de schéma et chaîne Azure AD utiliseriez pour renvoyer des jetons votre application demandée. Entrez `https://localhost:44321/` pour cette valeur.
-   Une fois que vous avez terminé l’enregistrement, accédez à l’onglet **configurer** et recherchez le champ **URI ID de l’application** .  Entrez un identificateur client spécifiques pour cette valeur, par exemple :`https://contoso.onmicrosoft.com/TodoListService`
- Enregistrer la configuration.  Laissez le portail ouverte : vous devez également enregistrer votre application cliente peu de temps.

## <a name="2-set-up-your-app-to-use-the-owin-authentication-pipeline"></a>*2. configurer votre application pour utiliser le pipeline de l’authentification OWIN*

À présent que vous avez enregistré une application avec Azure Active Directory, vous devez configurer votre application de communiquer avec Azure AD pour valider entrants demandes & jetons.

-   Pour commencer, ouvrez la solution et ajoutez les packages OWIN logiciels intermédiaires NuGet au projet TodoListService à l’aide de la Console du Gestionnaire de Package.

```
PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

-   Ajouter une classe démarrage OWIN au projet TodoListService appelé `Startup.cs`.  Droite, cliquez sur le projet--> **Ajouter** --> **Nouvel élément** --> recherche pour « OWIN ».  Logiciels intermédiaires OWIN appelle la `Configuration(…)` méthode au démarrage de votre application.
-   Modifier la déclaration de classe à `public partial class Startup` -nous avons déjà implémentée partie de ce cours pour vous dans un autre fichier.  Dans la `Configuration(…)` méthode, effectuer un appel à ConfgureAuth(...) pour configurer l’authentification pour votre application web.

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-   Ouvrez le fichier `App_Start\Startup.Auth.cs` et mise en œuvre la `ConfigureAuth(…)` méthode.  Les paramètres que vous fournissez dans `WindowsAzureActiveDirectoryBearerAuthenticationOptions` servira coordonnées de votre application pour communiquer avec Azure AD.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.UseWindowsAzureActiveDirectoryBearerAuthentication(
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions
        {
            Audience = ConfigurationManager.AppSettings["ida:Audience"],
            Tenant = ConfigurationManager.AppSettings["ida:Tenant"]
        });
}
```

-   Vous pouvez désormais utiliser `[Authorize]` attributs pour protéger vos contrôleurs et actions avec l’authentification JWT porteur.  Décorer les `Controllers\TodoListController.cs` classe avec une balise autoriser.  Cela oblige l’utilisateur à se connecter avant d’accéder à cette page.

```C#
[Authorize]
public class TodoListController : ApiController
{
```

- Lorsqu’un appelant conseillers correctement appelle l’une de la `TodoListController` API, l’action avoir besoin d’accéder aux informations relatives à l’appelant.  OWIN permet d’accéder aux créances à l’intérieur du jeton PORTEUR via la `ClaimsPrincpal` objet.  
- Une obligation courantes pour web API consiste à valider les « étendues » présents dans le jeton - ainsi que l’utilisateur final a accepté des autorisations requises pour accéder au Service de liste Todo :

```C#
public IEnumerable<TodoItem> Get()
{
    // user_impersonation is the default permission exposed by applications in AAD
    if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "user_impersonation")
    {
        throw new HttpResponseException(new HttpResponseMessage {
          StatusCode = HttpStatusCode.Unauthorized,
          ReasonPhrase = "The Scope claim does not contain 'user_impersonation' or scope claim not found"
        });
    }
    ...
}
```

-   Enfin, ouvrez la `web.config` de fichier dans la racine du projet TodoListService, puis entrez les valeurs de configuration dans la `<appSettings>` section.
  - La `ida:Tenant` est le nom de votre client Azure AD, par exemple, « contoso.onmicrosoft.com ».
  - Votre `ida:Audience` est l’URI ID de l’application de l’application que vous avez entrées dans le portail Azure.

## <a name="3--configure-a-client-application--run-the-service"></a>*3. Configuration d’une application cliente et exécuter le service*
Avant de pouvoir afficher le Service de liste Todo en action, vous devez configurer le Client de liste Todo afin de pouvoir bénéficier des jetons AAD et passer des appels au service.

- Revenir au [Portail de gestion Azure](https://manage.windowsazure.com)
- Créer une nouvelle application dans votre client Azure AD, puis sélectionnez **Application cliente Native** dans l’invite qui en résulte.
    -   Le **nom** de l’application décrira votre application aux utilisateurs finaux
    -   Entrez `http://TodoListClient/` pour la valeur **Rediriger Uri** .
- Une fois que vous avez terminé l’enregistrement, AAD affecter votre application un **Id Client**unique. Vous devez cette valeur dans la procédure suivante, donc copier à partir de l’onglet Configurer.
- Également dans l’onglet **configurer** , recherchez la section « Autorisations pour les autres Applications ». Cliquez sur « Ajouter une Application ». Sélectionnez « Toutes les applications » dans la liste déroulante « Afficher », puis cliquez sur la coche supérieure. Recherchez et cliquez sur à faire liste service, puis cliquez sur la coche en bas pour ajouter l’application. Sélectionnez « À faire liste Service d’accès » dans le menu déroulant « Délégué les autorisations » et enregistrer la configuration.


- Dans Visual Studio, ouvrez `App.config` dans la TodoListClient project et entrez vos valeurs de configuration dans la `<appSettings>` section.
  - La `ida:Tenant` est le nom de votre client Azure AD, par exemple, « contoso.onmicrosoft.com ».
  - Votre `ida:ClientId` ID de l’application que vous avez copié à partir du portail Azure.
  - Votre `todo:TodoListResourceId` est l’URI ID de l’application de l’application de Service de liste à faire que vous avez entrées dans le portail Azure.

Pour finir, nettoyer, créer et exécuter chaque projet !  Si vous n’avez pas déjà, c’est le moment pour créer un nouvel utilisateur dans votre client avec une *. domaine onmicrosoft.com.  Se connecter pour le client de la liste des tâches avec cet utilisateur et ajouter des tâches à liste l’utilisateur des tâches.

Pour référence, l’échantillon terminée (sans vos valeurs configuration) est fourni [ici](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip).  Vous pouvez maintenant déplacer sur aux autres scénarios identité supplémentaire.

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
