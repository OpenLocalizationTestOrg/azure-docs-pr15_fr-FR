<properties
   pageTitle="Rôles d’application | Microsoft Azure"
   description="Comment procéder à l’autorisation à l’aide de rôles d’application"
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
   ms.date="02/16/2016"
   ms.author="mwasson"/>

#  <a name="application-roles-in-multitenant-applications"></a>Rôles d’application dans les applications partagées

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Cet article fait [partie d’une série]. Vous trouverez également un [exemple d’application] complète qui accompagne cette série.

Rôles d’application sont utilisées pour attribuer des autorisations aux utilisateurs. Par exemple, les [Enquêtes Tailspin] [ Tailspin] application définit les rôles suivants :

- Administrateur. Peuvent effectuer toutes les opérations CRUD sur n’importe quel enquête appartenant à ce client.
- Créateur. Peut créer de nouvelles enquêtes.
- Lecteur. Pour en savoir les enquêtes appartenant à ce client.

Vous pouvez voir que rôles finalement obtenir traduire autorisations, lors de [l’autorisation]. Mais la première question est l’affectation et de gérer les rôles. Nous avons identifié trois options principales :

-   [Rôles d’application Azure AD](#roles-using-azure-ad-app-roles)
-   [Groupes de sécurité Azure AD](#roles-using-azure-ad-security-groups)
-   [Gestionnaire de rôles d’application](#roles-using-an-application-role-manager).

## <a name="roles-using-azure-ad-app-roles"></a>Rôles à l’aide de rôles d’application Azure AD

Il s’agit de l’approche que nous avons utilisé dans l’application Tailspin enquêtes.

Dans cette approche, SaaS le fournisseur définit les rôles d’application en les ajoutant au manifeste d’application. Une fois un client s’inscrit, un administrateur annuaire d’Active Directory du client affecte les utilisateurs aux rôles. Lorsqu’un utilisateur se connecte, les rôles attribués de l’utilisateur sont envoyées en tant que revendications.

> [AZURE.NOTE] Si le client a Azure AD Premium, l’administrateur peut affecter un groupe de sécurité à un rôle et membres du groupe hériteront le rôle d’application. C’est un moyen pratique pour gérer les rôles, parce que le propriétaire du groupe ne doit pas être un administrateur AD.

Avantages de cette approche :

-   Modèle de programmation simple.
-   Les rôles sont spécifiques à l’application. Les demandes de rôle pour une application ne sont pas envoyés à une autre application.
-   Si le client supprime l’application de leur client AD, les rôles disparaissent.
-   L’application n’a pas besoin d’autorisations Active Directory supplémentaires, différent de lecture du profil utilisateur.

Inconvénients :

- Clients sans Azure AD Premium ne pouvez pas attribuer des groupes de sécurité aux rôles. Pour ces clients, toutes les affectations de l’utilisateur doivent être effectuées par un administrateur AD.
- Si vous avez un site web et API, qui est différente de l’application web, les attributions de rôle pour l’application web ne s’appliquent à l’API web. Pour savoir plus sur ce point, voir [sécurisation d’un serveur principal web API].

### <a name="implementation"></a>Mise en œuvre

**Définir les rôles.** Le fournisseur SaaS déclare les rôles d’application dans le [manifeste d’application]. Voici, par exemple, l’entrée de manifeste pour l’application enquêtes :

```
"appRoles": [
  {
    "allowedMemberTypes": [
      "User"
    ],
    "description": "Creators can create Surveys",
    "displayName": "SurveyCreator",
    "id": "1b4f816e-5eaf-48b9-8613-7923830595ad",
    "isEnabled": true,
    "value": "SurveyCreator"
  },
  {
    "allowedMemberTypes": [
      "User"
    ],
    "description": "Administrators can manage the Surveys in their tenant",
    "displayName": "SurveyAdmin",
    "id": "c20e145e-5459-4a6c-a074-b942bbd4cfe1",
    "isEnabled": true,
    "value": "SurveyAdmin"
  }
],
```

La `value` propriété s’affiche dans la demande de rôle. La `id` propriété est l’identificateur unique pour le rôle défini. Toujours générer une nouvelle valeur GUID pour `id`.

**Attribuer aux utilisateurs**. Lorsqu’un nouveau client s’inscrit, l’application est enregistrée dans le client d’Active Directory du client. À ce stade, un administrateur AD pour ce client peut affecter des utilisateurs aux rôles.

> [AZURE.NOTE] Comme indiqué précédemment, clients avec Azure AD Premium peuvent également affecter des groupes de sécurité aux rôles.

La capture d’écran suivante à partir du portail Azure présente trois utilisateurs. Alice a été affectée directement à un rôle. Alexandre héritées un rôle en tant que membre d’un groupe de sécurité nommé « Administrateur d’enquêtes, » est affectée à un rôle. Charles n’est pas affecté à n’importe quel rôle.

![Utilisateurs](media/guidance-multitenant-identity/role-assignments.png)

> [AZURE.NOTE] Par ailleurs, l’application peut affecter des rôles par programme, à l’aide de l’API Azure AD Graph.  Toutefois, cette fonctionnalité nécessite l’application pour obtenir des autorisations d’écriture pour annuaire d’Active Directory du client. Une application disposant de ces autorisations peut effectuer un grand nombre d’inconvénient &mdash; le client est plus faire confiance à l’application ne pas pour n’endommagera leur répertoire. De nombreux clients peuvent être refuse octroyer à ce niveau d’accès.

**Obtenir des revendications de rôle**. Lorsqu’un utilisateur se connecte, l’application reçoit ou les rôles affectées de l’utilisateur dans une demande de remboursement avec type `http://schemas.microsoft.com/ws/2008/06/identity/claims/role`.  

Un utilisateur peut avoir plusieurs rôles, ou aucun rôle. Dans votre code d’autorisation, ne pensez pas qu'un utilisateur a un rôle réclamer. À la place, écrire du code qui vérifie si une valeur de demande de remboursement particulier est présente :

```csharp
if (context.User.HasClaim(ClaimTypes.Role, "Admin")) { ... }
```

## <a name="roles-using-azure-ad-security-groups"></a>Rôles à l’aide de groupes de sécurité Azure AD

Dans cette approche, les rôles sont représentés sous forme de groupes de sécurité AD. L’application attribue des autorisations aux utilisateurs en fonction de leur appartenance aux groupes de sécurité.

Avantages :

-   Pour les clients qui ne possèdent pas Azure AD Premium, cette approche permet au client d’utiliser des groupes de sécurité pour gérer les attributions de rôle.

Inconvénients :

- Complexité. Étant donné que chaque client envoie revendications de groupe différents, l’application doit effectuer le suivi des groupes de sécurité correspondant aux rôles d’application, pour chaque client.
- Si le client supprime l’application de leur client AD, les groupes de sécurité sont laissés dans leur annuaire Active Directory.

### <a name="implementation"></a>Mise en œuvre

Dans le manifeste d’application, définissez la `groupMembershipClaims` propriété « SecurityGroup ». Cela est nécessaire pour obtenir des revendications de l’appartenance aux groupes d’AAD.

```
{
   // ...
   "groupMembershipClaims": "SecurityGroup",
}
```

Lorsqu’un nouveau client s’inscrit, l’application indique le client pour créer des groupes de sécurité pour les rôles nécessaires à l’application. Le client doit puis entrez l’ID de l’objet groupe dans l’application. L’application stocke ces éléments dans une table qui mappe les ID de groupe de rôles d’application, par client.

> [AZURE.NOTE] Par ailleurs, l’application peut créer des groupes par programme, à l’aide de l’API Azure AD Graph.  Il s’agit moins source d’erreurs. Toutefois, elle nécessite l’application à obtenir « lire et écrire tous les groupes » autorisations AD répertoire du client. De nombreux clients peuvent être refuse octroyer à ce niveau d’accès.

Lorsqu’un utilisateur se connecte :

1.  L’application reçoit des groupes de l’utilisateur en tant que revendications. La valeur de chaque demande est l’ID d’objet d’un groupe.
2.  Azure AD limite le nombre de groupes envoyés dans le jeton. Si le nombre de groupes dépasse cette limite, Azure AD envoie une demande de remboursement « limites d’âge » spécial. Si cette déclaration est présente, l’application doit demander l’API Azure AD Graph pour intégrer l’ensemble des groupes auxquels appartient cet utilisateur. Pour plus d’informations, voir [autorisation dans les Applications Cloud à l’aide de groupes d’annonces], sous la section intitulée « Groupes réclamer excédent ».
3.  L’application recherche les ID d’objet dans sa propre base de données, pour rechercher les rôles d’application correspondant à attribuer à l’utilisateur.
4.  L’application ajoute une valeur réclamer personnalisé à l’utilisateur principal qui exprime le rôle d’application. Par exemple : `survey_role` = « SurveyAdmin ».

Stratégies d’autorisation doivent utiliser la déclaration de rôle personnalisé, et non du groupe réclamer.

## <a name="roles-using-an-application-role-manager"></a>Rôles à l’aide d’un gestionnaire de rôles d’application

Avec cette approche, les rôles d’application ne sont pas stockés dans Azure Active Directory du tout. En revanche, l’application stocke les attributions de rôle pour chaque utilisateur dans sa propre base de données &mdash; par exemple, à l’aide de la classe **RoleManager** ASP.NET identité de connexion.

Avantages :

-   L’application a un contrôle total sur les rôles et les affectations de l’utilisateur.

Inconvénients :

- Plus complexes, plus difficile à gérer.
- Impossible d’utiliser des groupes de sécurité Active Directory pour gérer les attributions de rôle.
- Stocke les informations utilisateur dans la base de données d’application, où il accessible pas synchronisé avec répertoire d’Active Directory du client, que les utilisateurs sont ajoutées ou supprimées.   

Il existe de nombreux exemples existants pour cette approche. Par exemple, voir [créer une application ASP.NET MVC avec auth et base de données SQL et déployer Azure Application Service].

## <a name="next-steps"></a>Étapes suivantes

- Consultez l’article suivant de cette série : [autorisation basée sur les rôles et basée sur les ressources dans les applications partagées][autorisation]

<!-- Links -->
[Tailspin]: guidance-multitenant-identity-tailspin.md
[partie d’une série]: guidance-multitenant-identity.md
[autorisation]: guidance-multitenant-identity-authorize.md
[Sécurisation d’un site web et API]: guidance-multitenant-identity-web-api.md
[Créer une application ASP.NET MVC avec auth et base de données SQL et déployer au Service d’application Azure]: ../app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md
[manifeste d’application]: ../active-directory/active-directory-application-manifest.md
[exemple d’application]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
