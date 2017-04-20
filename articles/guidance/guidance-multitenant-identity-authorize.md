<properties
   pageTitle="Autorisation dans les applications partagées | Microsoft Azure"
   description="Comment procéder à l’autorisation dans une application partagée"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/02/2016"
   ms.author="mwasson"/>

# <a name="role-based-and-resource-based-authorization-in-multitenant-applications"></a>Autorisation basée sur les rôles et basée sur les ressources dans les applications partagées

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Cet article fait [partie d’une série]. Vous trouverez également un [exemple d’application] complète qui accompagne cette série.

Notre [implémentation de référence] est une application Web ASP.NET Core 1.0. Dans cet article nous allons aborder deux approches générales de l’autorisation, à l’aide de l’autorisation API fournies dans ASP.NET Core 1.0.

-   **Autorisation par rôle**. Autoriser une action basée sur les rôles attribués à un utilisateur. Par exemple, certaines actions nécessitent un rôle d’administrateur.
-   **Autorisation basée sur les ressources**. Autoriser une action basée sur une ressource particulière. Par exemple, chaque ressource a un propriétaire. Le propriétaire peut supprimer la ressource. d’autres utilisateurs ne peuvent pas.

Une application classique emploie un mélange des deux. Par exemple, pour supprimer une ressource, l’utilisateur doit être la ressource propriétaire _ou_ administrateur.


## <a name="role-based-authorization"></a>Autorisation par rôle

Les [Enquêtes Tailspin] [ Tailspin] application définit les rôles suivants :

- Administrateur. Peuvent effectuer toutes les opérations CRUD sur n’importe quel enquête appartenant à ce client.
- Créateur. Peut créer de nouvelles enquêtes
- Lecteur. Peut lire les enquêtes appartenant à ce client

Rôles s’appliquent aux _utilisateurs_ de l’application. Dans l’application enquêtes, un utilisateur est un administrateur, le créateur ou le lecteur.

Pour plus d’informations définir et gérer les rôles, voir [les rôles d’Application].

Quelle que soit la façon dont vous gérez les rôles, votre code d’autorisation est semblable. Core ASP.NET 1.0 présente une abstraction appelée [stratégies d’autorisation][policies]. Avec cette fonctionnalité, vous définissez les stratégies d’autorisation dans le code et appliquez ces politiques actions du contrôleur. La stratégie est dissociée de celui-ci.

### <a name="create-policies"></a>Créer des stratégies

Pour définir une stratégie, commencez par créer une classe qui mettent en œuvre, `IAuthorizationRequirement`. Il est plus facile de dérivent `AuthorizationHandler`. Dans la `Handle` méthode, examinez la claim(s) pertinentes.

Voici un exemple de l’application Tailspin enquêtes :

```csharp
public class SurveyCreatorRequirement : AuthorizationHandler<SurveyCreatorRequirement>, IAuthorizationRequirement
{
    protected override void Handle(AuthorizationContext context, SurveyCreatorRequirement requirement)
    {
        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyAdmin) ||
            context.User.HasClaim(ClaimTypes.Role, Roles.SurveyCreator))
        {
            context.Succeed(requirement);
        }
    }
}
```

> [AZURE.NOTE] Voir [SurveyCreatorRequirement.cs]

Ce cours définit la configuration minimale requise pour un utilisateur créer une nouvelle enquête. L’utilisateur doit être placé dans le rôle SurveyAdmin ou SurveyCreator.

Dans votre cours démarrage, définir une stratégie nommée qui inclut un ou plusieurs conditions. S’il existe plusieurs conditions, l’utilisateur doit répondre à _toutes_ les exigences à autoriser. Le code suivant définit deux stratégies :

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy(PolicyNames.RequireSurveyCreator,
        policy =>
        {
            policy.AddRequirements(new SurveyCreatorRequirement());
            policy.AddAuthenticationSchemes(CookieAuthenticationDefaults.AuthenticationScheme);
        });

    options.AddPolicy(PolicyNames.RequireSurveyAdmin,
        policy =>
        {
            policy.AddRequirements(new SurveyAdminRequirement());
            policy.AddAuthenticationSchemes(CookieAuthenticationDefaults.AuthenticationScheme);
        });
});
```

> [AZURE.NOTE] Voir [Startup.cs]

Ce code définit également le schéma d’authentification, qui dit ASP.NET les logiciels intermédiaires authentification doivent s’exécuter si l’autorisation échoue. Dans ce cas, nous spécifier des cookies d’authentification logiciels intermédiaires, car des cookies d’authentification logiciels intermédiaires pouvez rediriger l’utilisateur vers une page « Interdit ». L’emplacement de la page interdit est défini dans l’option AccessDeniedPath aux logiciels intermédiaires des cookies ; voir [configuration logiciels intermédiaires d’authentification].

### <a name="authorize-controller-actions"></a>Autorisez les actions de contrôleur

Enfin, si vous êtes une action dans un contrôleur MVC, définissez la stratégie la `Authorize` attribut :

```csharp
[Authorize(Policy = "SurveyCreatorRequirement")]
public IActionResult Create()
{
    // ...
}
```

Dans les versions antérieures d’ASP.NET, vous pouvez définir la propriété **rôles** pour l’attribut :

```csharp
// old way
[Authorize(Roles = "SurveyCreator")]

```

Il est toujours pris en charge dans ASP.NET Core 1.0, mais il a quelques inconvénients par rapport aux stratégies d’autorisation :

-   Il est supposé un type de demande de remboursement particulier. Stratégies vérifie-t-il pour n’importe quel type de déclaration. Les rôles sont simplement un type de déclaration.
-   Le nom de rôle est codé en dur dans l’attribut. Avec les stratégies, la logique d’autorisation est un emplacement unique, ce qui facilite à mettre à jour ou même charger à partir des paramètres de configuration.
-   Activent les stratégies habilités plus complexes (par exemple, l’âge > = 21) qui ne peut pas être exprimée par l’appartenance aux rôles simple.

## <a name="resource-based-authorization"></a>Autorisation en fonction des ressources

_Autorisation basé sur les ressources_ se produit lorsque l’autorisation dépend une ressource spécifique qui est affectée par une opération. Dans l’application Tailspin enquêtes, chaque enquête a un propriétaire et collaborateurs zéro-à-plusieurs.

-   Le propriétaire peut lire, mettre à jour, supprimer, publier et annuler la publication de l’enquête.
-   Le propriétaire peut affecter des collaborateurs à l’enquête.
-   Les collaborateurs peuvent lire et mettre à jour de l’enquête.

Notez que « propriétaire » et « collaborateur » ne sont pas les rôles d’application ; ils sont stockés par enquête, dans la base de données de l’application. Pour vérifier si un utilisateur peut supprimer une enquête, par exemple, l’application vérifie si l’utilisateur est le propriétaire de cette enquête.

Dans ASP.NET Core 1.0, mise en œuvre d’autorisation basée sur les ressources par dérivation de **AuthorizationHandler** et substitution de la méthode **Gérer** .

```csharp
public class SurveyAuthorizationHandler : AuthorizationHandler<OperationAuthorizationRequirement, Survey>
{
     protected override void Handle(AuthorizationContext context, OperationAuthorizationRequirement operation, Survey resource)
    {
    }
}
```

Notez que cette classe est fortement typée pour les objets d’une enquête.  S’inscrire la classe DI au démarrage :

```csharp
services.AddSingleton<IAuthorizationHandler>(factory =>
{
    return new SurveyAuthorizationHandler();
});
```

Pour effectuer des contrôles d’autorisation, utilisez l’interface **IAuthorizationService** , qui vous pouvez injecte dans vos contrôleurs. Le code suivant vérifie si un utilisateur peut lire une enquête :

```csharp
if (await _authorizationService.AuthorizeAsync(User, survey, Operations.Read) == false)
{
    return new HttpStatusCodeResult(403);
}
```

Étant donné que nous passer dans un `Survey` objet, cet appel appelle la `SurveyAuthorizationHandler`.

Dans votre code d’autorisation, une bonne approche consiste à agrégation : toutes les autorisations basées sur les ressources et rôle de l’utilisateur, puis à cocher définie l’agrégat par rapport à l’opération souhaitée.
Voici un exemple de l’application d’enquêtes. L’application définit plusieurs types d’autorisation :

- Administrateur
- Collaboration
- Créateur de blocs-notes
- Propriétaire
- Reader

L’application définit également un ensemble d’opérations possibles concernant les enquêtes sur :

- Créer
- Lecture
- Mise à jour
- Supprimer
- Publier
- Unpublsh

Le code suivant crée une liste des autorisations pour un utilisateur particulier et l’enquête. Remarquez que ce code ressemble à la fois les rôles d’application de l’utilisateur et les champs propriétaire/collaboration dans l’enquête.

```csharp
protected override void Handle(AuthorizationContext context, OperationAuthorizationRequirement operation, Survey resource)
{
    var permissions = new List<UserPermissionType>();
    string userTenantId = context.User.GetTenantIdValue();
    int userId = ClaimsPrincipalExtensions.GetUserKey(context.User);
    string user = context.User.GetUserName();

    if (resource.TenantId == userTenantId)
    {
        // Admin can do anything, as long as the resource belongs to the admin's tenant.
        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyAdmin))
        {
            context.Succeed(operation);
            return;
        }

        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyCreator))
        {
            permissions.Add(UserPermissionType.Creator);
        }
        else
        {
            permissions.Add(UserPermissionType.Reader);
        }

        if (resource.OwnerId == userId)
        {
            permissions.Add(UserPermissionType.Owner);
        }
    }
    if (resource.Contributors != null && resource.Contributors.Any(x => x.UserId == userId))
    {
        permissions.Add(UserPermissionType.Contributor);
    }
    if (ValidateUserPermissions[operation](permissions))
    {
        context.Succeed(operation);
    }
}
```

> [AZURE.NOTE] Voir [SurveyAuthorizationHandler.cs].

Dans une application cliente multiples, vous devez vous assurer que les autorisations ne « perdre » à des données d’un autre client. Dans l’application enquêtes, l’autorisation collaboration est autorisée entre les clients &mdash; vous pouvez affecter une personne à partir d’un autre client comme un contriubutor. Les autres types d’autorisations sont limitées aux ressources qui appartiennent au client de cet utilisateur. Pour appliquer cette exigence, le code vérifie l’ID de client avant d’accorder l’autorisation. (La `TenantId` de champ, comme affectées lorsque l’enquête est créée.)

L’étape suivante consiste à vérifier le fonctionnement (lecture, mettre à jour, suppression, etc.) contre les autorisations. L’application enquêtes met en œuvre cette étape à l’aide d’une table de choix des fonctions :

```csharp
static readonly Dictionary<OperationAuthorizationRequirement, Func<List<UserPermissionType>, bool>> ValidateUserPermissions
    = new Dictionary<OperationAuthorizationRequirement, Func<List<UserPermissionType>, bool>>

    {
        { Operations.Create, x => x.Contains(UserPermissionType.Creator) },

        { Operations.Read, x => x.Contains(UserPermissionType.Creator) ||
                                x.Contains(UserPermissionType.Reader) ||
                                x.Contains(UserPermissionType.Contributor) ||
                                x.Contains(UserPermissionType.Owner) },

        { Operations.Update, x => x.Contains(UserPermissionType.Contributor) ||
                                x.Contains(UserPermissionType.Owner) },

        { Operations.Delete, x => x.Contains(UserPermissionType.Owner) },

        { Operations.Publish, x => x.Contains(UserPermissionType.Owner) },

        { Operations.UnPublish, x => x.Contains(UserPermissionType.Owner) }
    };
```


## <a name="next-steps"></a>Étapes suivantes

- Consultez l’article suivant de cette série : [sécurisation d’un serveur principal web API dans une application partagée][web-api]
- Pour en savoir plus sur les ressources en fonction de l’autorisation dans ASP.NET 1.0 Core, consultez [Ressources d’autorisations][rbac].

<!-- Links -->
[Tailspin]: guidance-multitenant-identity-tailspin.md
[partie d’une série]: guidance-multitenant-identity.md
[Rôles d’application]: guidance-multitenant-identity-app-roles.md
[policies]: https://docs.asp.net/en/latest/security/authorization/policies.html
[rbac]: https://docs.asp.net/en/latest/security/authorization/resourcebased.html
[implémentation de référence]: guidance-multitenant-identity-tailspin.md
[SurveyCreatorRequirement.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Security/Policy/SurveyCreatorRequirement.cs
[Startup.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs
[Configurer l’authentification logiciels intermédiaires]: guidance-multitenant-identity-authenticate.md#configuring-the-authentication-middleware
[SurveyAuthorizationHandler.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Security/Policy/SurveyAuthorizationHandler.cs
[exemple d’application]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
[web-api]: guidance-multitenant-identity-web-api.md
