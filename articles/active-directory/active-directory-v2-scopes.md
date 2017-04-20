<properties
    pageTitle="Azure AD version 2.0 étendues, autorisations et consentement | Microsoft Azure"
    description="Une description de l’autorisation dans le point de terminaison de version 2.0 Azure AD, y compris les étendues, les autorisations et consentement."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="dastrock"/>

# <a name="scopes-permissions--consent-in-the-v20-endpoint"></a>Étendues, autorisations et consentement dans le point de terminaison version 2.0

Applications qui s’intègrent à Azure AD suivent un modèle d’autorisation qui permet aux utilisateurs de contrôler la façon dont une application peut accéder à leurs données.  L’implémentation de la version 2.0 de ce modèle d’autorisation a été mis à jour, modifier la façon dont une application doit interagir avec Azure AD.  Cette rubrique couvre les concepts de base de ce modèle d’autorisation, y compris les étendues, les autorisations et consentement.

> [AZURE.NOTE]
    Pas tous les scénarios Azure Active Directory et fonctionnalités sont prises en charge par le point de terminaison version 2.0.  Pour déterminer si vous devez utiliser le point de terminaison version 2.0, lisez les [limitations de la version 2.0](active-directory-v2-limitations.md).

## <a name="scopes--permissions"></a>Les étendues et autorisations

Azure AD met en œuvre le protocole [2.0 OAuth](active-directory-v2-protocols.md) d’autorisation, qui est une méthode permettant à une application de fête 3e accéder aux ressources de site web hébergé au nom d’un utilisateur.  N’importe quelle ressource de site web hébergé qui s’intègre à Azure AD ont un identificateur de ressource ou **URI ID de l’application**.  Par exemple, voici quelques-unes des ressources de site web hébergé de Microsoft :

- Office 365 unifiée courrier API :`https://outlook.office.com`
- L’API Azure AD Graph :`https://graph.windows.net`
- Le graphique Microsoft :`https://graph.microsoft.com`

Il en est de même pour les ressources 3e partie qui a intégré à Azure AD.  Une de ces ressources peut également définir un jeu d’autorisations qui peut être utilisé pour diviser la fonctionnalité de cette ressource en plus petits blocs.  Par exemple, le graphique Microsoft définie quelques autorisations :

- Lire le calendrier d’un utilisateur
- Écrire dans le calendrier d’un utilisateur
- Envoyer du courrier en tant qu’utilisateur
- [+ plus](https://graph.microsoft.io)

En définissant des autorisations suivantes, la ressource peut avoir un contrôle précis sur ses données et la manière dont il est exposé à l’extérieur.  Une application de fête 3e peut alors demander ces autorisations à partir d’un utilisateur final - et l’utilisateur final doit approuver les autorisations afin que l’application peut agir en leur nom.  Par segmentation des fonctionnalités de la ressource dans les jeux d’autorisations plus petits, 3e partie applications peuvent être créées pour faire en sorte que les autorisations spécifiques dont ils ont besoin pour effectuer leur obligation.  Il permet également aux utilisateurs finaux connaître exactement comment une application utilise des données, afin qu’ils soient plus il est probable que l’application ne semble pas fonctionne avec les utilisateurs malveillants.

Dans Azure AD et OAuth, les autorisations suivantes sont appelées **étendues**.  Vous pouvez également voir les appelées **oAuth2Permissions**.  L’étendue est représentée dans Azure AD comme une valeur de chaîne.  Poursuivre l’exemple Microsoft Graph, la valeur de la portée pour chaque autorisation est la suivante :

- Lire le calendrier d’un utilisateur :`Calendar.Read`
- Enregistrer dans le calendrier d’un utilisateur :`Mail.ReadWrite`
- Envoyer du courrier en tant qu’utilisateur :`Mail.Send`

Une application peut demander ces autorisations en spécifiant les étendues dans les demandes au point de terminaison de la version 2.0, comme décrit ci-dessous.

## <a name="openid-connect-scopes"></a>Se connecter OpenId étendues

L’implémentation de la version 2.0 de OpenID Connect comporte quelques étendues bien définis qui ne s’appliquent pas aux ressources particulier - `openid`, `email`, `profile`, et `offline_access`.

#### <a name="openid"></a>OpenId

Si une application effectue se connecter à l’aide de [OpenID se connecter](active-directory-v2-protocols.md#openid-connect-sign-in-flow), elle doit demander la `openid` étendue.  La `openid` étendue s’affiche-t-il dans l’écran travail compte consentement que l’autorisation « Pouvons pas vous connecter » et dans l’écran de consentement de compte Microsoft personnel en tant que l’autorisation « Afficher votre profil et se connecter aux applications et services à l’aide de votre compte Microsoft ».  Cette autorisation permet à une application recevoir un identificateur unique pour l’utilisateur dans l’écran de la `sub` réclamer.  Il permet également d’utiliser le niveau d’accès au point de terminaison informations utilisateur application.  La `openid` étendue peut également être utilisé au point de terminaison jeton version 2.0 pour acquérir id_tokens, qui peut être utilisé pour sécuriser les appels HTTP entre les différents composants d’une application.

#### <a name="email"></a>Messagerie

La `email` étendue peut être inclus avec le `openid` étendues et autres.  Il offre l’accès d’application à l’adresse de messagerie principale de l’utilisateur dans l’écran de la `email` réclamer.  La `email` réclamer n’est inclus dans les jetons si une adresse de messagerie est associée avec le compte d’utilisateur, qui n’est pas toujours la casse.  Si vous utilisez la `email` étendue, votre application doit être préparée à gérer le cas dans lequel la `email` réclamer n’existe pas dans le jeton.

#### <a name="profile"></a>Profil

La `profile` étendue peut être inclus avec le `openid` étendues et autres.  Il offre l’accès de l’application à une mine d’informations sur l’utilisateur.  Cela inclut, mais n’est pas limité à l’utilisateur prénom, nom, nom d’utilisateur par défaut, ID de l’objet et ainsi de suite.  Pour une liste complète des créances profil disponibles dans id_tokens par un utilisateur donné, reportez-vous à la [référence de jeton de version 2.0](active-directory-v2-tokens.md).

#### <a name="offlineaccess"></a>Offline_access

La [ `offline_access` étendue](http://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) permet à votre application pour accéder aux ressources au nom de l’utilisateur pour une période prolongée.  Dans l’écran de consentement compte de travail, cette étendue s’affichent en tant que l’autorisation « Accéder à vos données à tout moment ».  Dans l’écran de consentement de compte Microsoft personnel, il apparaît sous forme de l’autorisation « Accéder à vos informations à tout moment ».  Lorsqu’un utilisateur accorde le `offline_access` étendue, votre application sera activée pour recevoir des jetons d’actualisation du point de terminaison jeton version 2.0.  Actualiser les jetons sont à long terme et permettre à votre application acquérir nouveau jetons d’accès que les plus anciens expirent.

Si votre application ne demande pas la `offline_access` étendue, il ne recevrez pas de refresh_tokens.  Cela signifie que lorsque vous échanger un authorization_code dans le [flux de code 2.0 OAuth d’autorisation](active-directory-v2-protocols.md#oauth2-authorization-code-flow), vous ne recevrez que revenir un access_token à partir de la `/token` point de terminaison.  Cette access_token restera valide pour un laps de temps (en général une heure), mais arriveront à expiration.  AT nécessitant des point dans le temps, votre application pour rediriger l’utilisateur en retour vers le `/authorize` point de terminaison pour récupérer un nouveau authorization_code.  Au cours de cette redirection, l’utilisateur peut ou non besoin d’entrer ses informations d’identification à nouveau ou nouveau consentement aux autorisations, selon le type de l’application.

Pour plus d’informations sur comment obtenir et utiliser l’actualisation jetons, reportez-vous à la [référence au protocole version 2.0](active-directory-v2-protocols.md).


## <a name="requesting-individual-user-consent"></a>Demande de consentement des utilisateurs

Dans une requête d’autorisation [OpenID se connecter ou 2.0 OAuth](active-directory-v2-protocols.md) , une application peut demander les autorisations requises à l’aide de la `scope` paramètre de requête.  Par exemple, lorsqu’un utilisateur s’inscrit dans une application, l’application envoie une demande à ce qui suit (avec les sauts de ligne pour une meilleure lisibilité) :

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendar.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

La `scope` paramètre est une liste séparée par des espaces des étendues qui demande l’application.  Chaque étendue individuelles est indiqué par l’ajout de la valeur de l’étendue à de la ressource (application ID URI).  La requête ci-dessus indique que l’application doit d’autorisation lecture calendrier de l’utilisateur et envoyer du courrier en tant que l’utilisateur.

Une fois que l’utilisateur entre ses informations d’identification, le point de terminaison version 2.0 doit vérifier pour correspondre à un enregistrement de **consentement de l’utilisateur**.  Si l’utilisateur n’a pas accepté à n’importe quel les autorisations requises par le passé, le point de terminaison version 2.0 vous demande de l’utilisateur d’octroyer les autorisations requises.  

![Capture d’écran de travail compte consentement](../media/active-directory-v2-flows/work_account_consent.png)

Lorsque l’utilisateur accorde l’autorisation, l’autorisation sera enregistrée afin que l’utilisateur n’a pas à nouveau consentement sur les connexions suivantes.

## <a name="requesting-consent-for-an-entire-tenant"></a>Demande de consentement pour l’intégralité du client

Quand une organisation des achats de licence ou d’abonnement à l’application, qu’ils souhaitent entièrement la mise en service pour les employés.  Dans le cadre de ce processus, un administrateur de société peut accorder consentement pour cette application agir au nom d’un employé.  En accordant consentement pour un client entier, employés de cette organisation conséquences pas l’écran consentement pour cette application.

Pour demander consentement pour tous les utilisateurs dans un client, votre application peut utiliser le **point de terminaison de consentement d’administration**, décrite ci-dessous.

## <a name="admin-restricted-scopes"></a>Étendues restreint aux administrateurs

Certaines autorisations haute privilège dans le réseau de Microsoft peuvent être marquées comme **restreint aux administrateurs**.  Voici quelques exemples de ces étendues :

- Lecture des données de l’annuaire d’un l :`Directory.Read`
- Écrire des données dans l’annuaire d’une organisation :`Directory.ReadWrite`
- Lecture des groupes de sécurité dans l’annuaire d’une organisation :`Groups.Read.All`

Pendant un utilisateur grand public peut accorder l’accès à ces données d’une application, les utilisateurs d’organisation sont limités accorder l’accès au même jeu de données d’entreprise sensibles.  Si votre application demande l’accès à un de ces autorisations d’un utilisateur professionnel, l’utilisateur reçoit un message d’erreur indiquant qu’ils ne sont pas autorisés à consentement aux autorisations de votre application.

Si votre application requiert l’accès à ces étendues restreint aux administrateurs pour les entreprises, vous devez les demander directement à partir d’un administrateur de la société également à l’aide de l' **administrateur consentement point de terminaison**, décrite ci-dessous.

Lorsqu’un administrateur accorde les autorisations suivantes via le point de terminaison consentement d’administration, consentement hériteront pour tous les utilisateurs dans le client, comme décrit ci-dessus.

## <a name="using-the-admin-consent-endpoint"></a>À l’aide du point de terminaison consentement d’administration

En suivant ces étapes, votre application pourront rassembler des autorisations pour tous les utilisateurs dans un client donné, y compris les étendues restreint aux administrateurs.  Pour visualiser un exemple de code qui mettent en œuvre, l’utiliser étapes pour les inventaires ci-dessous, reportez-vous à l' [exemple d’étendues restreint d’administration](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Demander les autorisations dans le portail d’inscription de l’application

- Accédez à votre application [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ou [créer une application](active-directory-v2-app-registration.md) si vous n’avez pas déjà.
- Recherchez la section **Microsoft Graph autorisations** et ajouter les autorisations requises par votre application.
- Veillez à **Enregistrer** l’inscription de l’application

#### <a name="recommended-sign-the-user-into-your-app"></a>Recommandé : connecter l’utilisateur à votre application

En règle générale lors de la création d’une application qui utilise l’administrateur consentement point de terminaison, l’application devrez page/vue permet à l’administrateur d’approuver des autorisations de l’application.  Cette page peut faire partie de flux d’inscription de l’application, fait partie des paramètres de l’application ou un flux dédié « connecter ».  Dans de nombreux cas, il est préférable pour l’application afficher ce » se connecter » vue que lorsqu’un utilisateur a connectés avec un compte professionnel ou scolaire Microsoft.

La connexion de l’utilisateur à l’application vous permet d’identifier le visant à laquelle l’administrateur appartient avant leur demandant d’approuver les autorisations nécessaires.  Tandis que pas indispensable, il peut vous aider à créer une expérience plus intuitive pour vos utilisateurs d’organisation.  Pour vous connecter l’utilisateur dans, suivez nos [didacticiels de protocole version 2.0](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Demander les autorisations d’un administrateur de l’annuaire

Lorsque vous êtes prêt pour demander des autorisations d’administrateur de la société, vous pouvez rediriger l’utilisateur à version 2.0 **administrateur consentement point de terminaison**.

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro Tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | --------------- |
| client | Obligatoire | Le client de l’annuaire que vous souhaitez demander l’autorisation à partir de.  Peut être fourni au format de nom convivial ou guid. |
| identifiant_client | Obligatoire | L’Id de l’Application que le portail d’inscription ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) affecté à votre application. |
| redirect_uri | Obligatoire | Redirect_uri où vous souhaitez que la réponse à envoyer pour votre application gérer.  Il doit correspondre exactement à un de la redirect_uris que vous avez enregistré dans le portail. |
| état | recommandé | Une valeur incluse dans la requête qui est également retournée dans la réponse jeton.  Il peut être une chaîne de n’importe quel contenu que vous souhaitez.  L’état est utilisé pour coder les informations sur l’état de l’utilisateur dans l’application avant la demande d’authentification s’est produite, telles que la page ou la vue qu’ils ont été sur. |

À ce stade, Azure AD appliquera que seul l’administrateur client peut se connecter à terminer la demande.  L’administrateur est invité à approuver toutes les autorisations que vous avez demandé pour votre application dans le portail d’inscription. 

##### <a name="successful-response"></a>Réponse correcte
Si l’administrateur approuve les autorisations pour votre application, la réponse réussie seront :

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Paramètre | Description |
| ----------------------- | ------------------------------- | --------------- |
| client | Le client directory auquel les autorisations accordées à votre application il a demandé, au format guid. |
| état | Une valeur incluse dans la requête qui est également retournée dans la réponse jeton.  Il peut être une chaîne de n’importe quel contenu que vous souhaitez.  L’état est utilisé pour coder les informations sur l’état de l’utilisateur dans l’application avant la demande d’authentification s’est produite, telles que la page ou la vue qu’ils ont été sur. |
| admin_consent | Est définie sur `True`. |



##### <a name="error-response"></a>Réponse d’erreur
Si l’administrateur n’approuve pas les autorisations pour votre application, la réponse échec seront :

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Paramètre | Description |
| ----------------------- | ------------------------------- | --------------- |
| erreur | Une chaîne de code d’erreur qui peut être utilisée pour classer les types d’erreurs qui se produisent et peut être utilisée pour réagir aux erreurs. |
| error_description | Un message d’erreur spécifique qui peut vous aider à un développeur identifier la cause d’une erreur.  |

Une fois que vous avez reçu une réponse correcte du point de terminaison consentement d’administration, votre application a acquis les autorisations qu'il a demandé.  Vous pouvez désormais déplacer sur demande un jeton pour la ressource souhaitée, comme décrit ci-dessous.

## <a name="using-permissions"></a>À l’aide d’autorisations

Une fois que l’utilisateur accepte d’autorisations pour votre application, votre application peut acquérir des jetons d’accès qui représentent d’autorisation de votre application pour accéder à une ressource dans certaines capacité.  Un jeton d’accès donné peut uniquement être utilisé pour une ressources unique, mais codée qu’elle contient sera chaque autorisation votre application a été accordée pour cette ressource.  Pour acquérir un jeton d’accès, votre application peut effectuer une requête au point de terminaison jeton de version 2.0 :

```
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

Le jeton d’accès qui en résulte utilisable dans les requêtes HTTP à la ressource - fiable indiquera à la ressource que votre application a l’autorisation appropriée pour effectuer une tâche donnée.  

Pour plus d’informations sur le protocole OAuth 2.0 et comment acquérir des jetons d’accès, voir la [référence au protocole version 2.0 point de terminaison](active-directory-v2-protocols.md).
