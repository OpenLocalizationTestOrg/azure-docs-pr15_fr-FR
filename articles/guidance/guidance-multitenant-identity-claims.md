<properties
   pageTitle="Utilisation des identités basée sur les revendications dans les applications partagées | Microsoft Azure"
   description="Comment une utilisation réclamations pour l’autorisation et la validation de l’émetteur"
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
   ms.date="05/23/2016"
   ms.author="mwasson"/>

# <a name="working-with-claims-based-identities-in-multitenant-applications"></a>Utilisation des identités basée sur les revendications dans les applications partagées

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Cet article fait [partie d’une série]. Vous trouverez également un [exemple d’application] complète qui accompagne cette série.

## <a name="claims-in-azure-ad"></a>Revendications dans Azure Active Directory

Lorsqu’un utilisateur se connecte, Azure AD envoie un jeton ID qui contient un ensemble de revendications relatives à l’utilisateur. Une demande de remboursement est simplement un passage, exprimé sous forme d’une paire clé/valeur. Par exemple, `email` = `bob@contoso.com`.  Revendications ont un émetteur &mdash; dans ce cas, Azure AD &mdash; qui est l’entité qui authentifie l’utilisateur et crée les revendications. Faites-vous confiance à la réclamation parce que vous faites confiance à l’émetteur. (En revanche, si vous n’approuvez pas l’émetteur, n’approuvez pas les revendications !)

À un niveau élevé :

1.  L’utilisateur s’authentifie.
2.  La IDP envoie un ensemble de revendications.
3.  L’application normalise ou augmente les revendications (facultatives).
4.  L’application utilise les revendications pour prendre des décisions d’autorisation.

Dans OpenID vous connecter, le jeu de responsabilité que vous obtenez est contrôlé par le [paramètre de l’étendue] de la demande d’authentification. Toutefois, Azure AD émet un ensemble limité des revendications à travers OpenID connecter ; voir [jeton pris en charge et les Types de revendications]. Si vous souhaitez plus d’informations sur l’utilisateur, vous devez utiliser l’API Azure AD Graph.

Voici quelques-unes des demandes de AAD une application peut généralement sont importants :

Type de jeton d’ID de la déclaration |    Description
-----------------------|--------------
AUD | Qui a le jeton a été émis pour. Il s’agit d’ID de client. de l’application En règle générale, il ne doit pas inutile à vous soucier de cette demande, car logiciels intermédiaires la valide automatiquement. Exemple :`"91464657-d17a-4327-91f3-2ed99386406f"`
groupes   | Liste de groupes AAD dont l’utilisateur est membre. Exemple :`["93e8f556-8661-4955-87b6-890bc043c30f", "fc781505-18ef-4a31-a7d5-7d931d7b857e"]`
ISS  | L' [émetteur] du jeton OIDC. Exemple :`https://sts.windows.net/b9bd2162-77ac-4fb2-8254-5c36e9c0a9c4/`
nom    | Nom d’affichage de l’utilisateur. Exemple :`"Alice A."`
OID | L’identificateur d’objet pour l’utilisateur dans AAD. Cette valeur est l’identificateur immuable et non réutilisable de l’utilisateur. Utiliser cette valeur, pas de courrier électronique, comme un identificateur unique pour les utilisateurs ; les adresses de messagerie peuvent changer. Si vous utilisez l’API Azure AD Graph dans votre application, ID d’objet est cette valeur utilisée pour demander des informations de profil. Exemple :`"59f9d2dc-995a-4ddf-915e-b3bb314a7fa4"`
rôles   | Liste des rôles d’application pour l’utilisateur. Exemple :`["SurveyCreator"]`
TID | ID de client. Cette valeur est un identificateur unique pour le client dans Azure Active Directory. Exemple :`"b9bd2162-77ac-4fb2-8254-5c36e9c0a9c4"`
nom_unique | Un nom d’affichage lisible humaine de l’utilisateur. Exemple :`"alice@contoso.com"`
UPN | Nom d’utilisateur principal. Exemple :`"alice@contoso.com"`

Ce tableau répertorie les types de revendications tels qu’ils apparaissent dans le jeton d’ID. Dans ASP.NET Core 1.0, logiciels intermédiaires OpenID connecter convertit certains types réclamer lorsqu’elle remplit la collection de revendications pour l’utilisateur principal :

-   OID >`http://schemas.microsoft.com/identity/claims/objectidentifier`
-   TID >`http://schemas.microsoft.com/identity/claims/tenantid`
-   nom_unique >`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`
-   UPN >`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`

## <a name="claims-transformations"></a>Transformations de revendications

Pendant le flux d’authentification, vous souhaiterez peut-être modifier les revendications que vous obtenez à partir de la IDP. Dans ASP.NET Core 1.0, vous pouvez effectuer la transformation de déclarations à l’intérieur de l’événement **AuthenticationValidated** de logiciels intermédiaires OpenID se connecter. (Voir [les événements d’authentification]).

Les réclamations que vous ajoutez pendant **AuthenticationValidated** sont stockées dans les cookies d’authentification de session. Ils n’obtenez déplacées vers Azure AD.

Voici quelques exemples de transformation des déclarations :

-   **Normalisation revendications**ou apporter revendications cohérentes entre les utilisateurs. Ceci est particulièrement pertinent si vous rencontrez sur les revendications à partir de plusieurs IDPs, qui peuvent utiliser les types de revendications différentes pour des informations similaires.
Par exemple, Azure AD envoie une demande de « upn » qui contient le courrier électronique de l’utilisateur. Autres IDPs peuvent envoyer une demande de « messagerie ». Le code suivant convertit la demande « upn » dans une demande « envoyer par courrier électronique » :

    ```csharp
    var email = principal.FindFirst(ClaimTypes.Upn)?.Value;
    if (!string.IsNullOrWhiteSpace(email))
    {
      identity.AddClaim(new Claim(ClaimTypes.Email, email));
    }
    ```

- Ajouter des **valeurs par défaut des revendications** pour revendications qui ne sont pas présenter &mdash; par exemple, attribution d’un utilisateur à un rôle par défaut. Dans certains cas, cela peut simplifier la logique d’autorisation.
- Ajouter des **types de revendications personnalisées** avec des informations spécifiques à l’application sur l’utilisateur. Par exemple, vous pouvez stocker des informations sur l’utilisateur dans une base de données. Vous pouvez ajouter une déclaration personnalisée avec ces informations pour les tickets d’authentification. La demande est stockée dans des cookies, il suffit accéder à partir de la base de données une fois par session de connexion. En revanche, vous souhaitez également Évitez de créer des cookies trop volumineux, afin que vous devez prendre en considération le compromis entre la taille des cookies par rapport aux recherches de base de données.   

Une fois le flux d’authentification terminée, les revendications sont disponibles dans `HttpContext.User`. À ce stade, vous devez les traiter comme une collection en lecture seule &mdash; par exemple, les utiliser pour prendre des décisions d’autorisation.

## <a name="issuer-validation"></a>Validation de l’émetteur
Dans OpenID vous connecter, la demande de l’émetteur (« iss ») identifie IDP qui a émis le jeton ID. Partie du flux d’authentification OIDC consiste à vérifier que la demande de l’émetteur correspond à l’émetteur réel. Logiciels intermédiaires OIDC gère ceci pour vous.

Dans Azure Active Directory, la valeur de l’émetteur est unique par client AD (`https://sts.windows.net/<tenantID>`). Par conséquent, une application doit effectuer une vérification supplémentaire, pour vous assurer que l’émetteur représente un client est autorisé à se connecter à l’application.

Pour une application unique client, vous pouvez simplement vérifier que l’émetteur est votre propre client. En fait, logiciels intermédiaires OIDC fait automatiquement par défaut. Dans une application cliente multiples, vous devez autoriser plusieurs émetteur, correspondant à l’autres clients. Voici une approche générale à utiliser :

-   Dans les options de logiciels intermédiaires OIDC, définissez **ValidateIssuer** sur false. Cette option désactive la vérification automatique.
-   Lorsqu’un client s’inscrit, stocker le client et l’émetteur dans votre base de données de l’utilisateur.
-   Chaque fois qu’un utilisateur se connecte, recherchez l’émetteur dans la base de données. Si l’émetteur n’est pas trouvée, cela signifie que ce client n’a pas encore inscrit. Vous pouvez les rediriger vers une page de connexion.
-  Vous pouvez également liste de blocage certains clients ; par exemple, pour les clients qui n’a pas été payer son abonnement.

Pour plus d’informations, voir [d’abonnement et d’intégration dans une application partagée du client][signup].

## <a name="using-claims-for-authorization"></a>À l’aide des demandes d’autorisation

Avec les déclarations, identité d’un utilisateur n’est plus une entité monolithique. Par exemple, un utilisateur peut avoir une adresse de messagerie numéro de téléphone, d’anniversaire, sexe, etc.. Peut-être IDP de l’utilisateur stocke toutes ces informations. Mais lorsque vous authentifiez l’utilisateur, vous recevez généralement un sous-ensemble de ces en tant que revendications. Dans ce modèle, identité de l’utilisateur est uniquement un groupe de responsabilité. Lorsque vous apportez des décisions d’autorisation relatives à un utilisateur, vous recherche ensembles de revendications particuliers. En d’autres termes, la question « Peut effectuer des actions Y utilisateur X » devient finalement « Signifie utilisateur X a réclamer Z ».

Voici des modèles de base pour la vérification des affirmations.

-  Pour vérifier que l’utilisateur possède une demande de remboursement particulier avec une valeur particulière :

    ```csharp
    if (User.HasClaim(ClaimTypes.Role, "Admin")) { ... }
    ```
    Ce code vérifie si l’utilisateur possède une déclaration de rôle avec la valeur « Administrateur ». Il traite correctement le cas où l’utilisateur n’a aucun rôle ou des plusieurs créances rôle.

    La classe **ClaimTypes** définit les constantes pour les types de revendications fréquemment utilisées. Toutefois, vous pouvez utiliser n’importe quelle valeur de chaîne pour le type de déclaration.

-   Pour obtenir une valeur unique pour un type de déclaration, lorsque vous prévoyez qu’au moins une valeur :
    ```csharp
     string email = User.FindFirst(ClaimTypes.Email)?.Value;
    ```
-   Pour obtenir toutes les valeurs pour un type de déclaration :

    ```csharp
     IEnumerable<Claim> groups = User.FindAll("groups");
    ```

Pour plus d’informations, voir [autorisation basée sur les rôles et basée sur les ressources dans les applications partagées][authorization].

## <a name="next-steps"></a>Étapes suivantes

- Consultez l’article suivant de cette série : [d’abonnement et d’intégration dans une application partagée du client][signup]
- En savoir plus sur [l’autorisation basée sur les revendications] dans la documentation ASP.NET Core 1.0

<!-- Links -->
[partie d’une série]: guidance-multitenant-identity.md
[paramètre d’étendue]: http://nat.sakimura.org/2012/01/26/scopes-and-claims-in-openid-connect/
[Jeton pris en charge et les Types de revendications]: ../active-directory/active-directory-token-and-claims.md
[émetteur]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken
[Événements d’authentification]: guidance-multitenant-identity-authenticate.md#authentication-events
[signup]: guidance-multitenant-identity-signup.md
[Autorisation basée sur les revendications]: https://docs.asp.net/en/latest/security/authorization/claims.html
[exemple d’application]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
[authorization]: guidance-multitenant-identity-authorize.md
