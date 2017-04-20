
<properties
    pageTitle="Informations d’identification Azure AD version 2.0 OAuth Client flux | Microsoft Azure"
    description="Création d’applications web à l’aide de l’implémentation d’Azure AD du protocole d’authentification OAuth 2.0."
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
    ms.date="09/26/2016"
    ms.author="dastrock"/>

# <a name="v20-protocols---oauth-20-client-credentials-flow"></a>version 2.0 protocoles - informations d’identification du Client 2.0 OAuth flux

L' [accorder des informations d’identification du client 2.0 OAuth](http://tools.ietf.org/html/rfc6749#section-4.4), parfois appelés « OAuth à deux branches », peut servir à accéder aux ressources web hébergé à l’aide de l’identité d’une application.  Il est généralement utilisée pour les interactions serveur à serveur qui doivent s’exécuter en arrière-plan sans le precense immédiate d’un utilisateur final.  Ces types d’applications sont souvent appelés **processus** ou **les comptes de service**.

> [AZURE.NOTE]
    Pas tous les scénarios Azure Active Directory et fonctionnalités sont prises en charge par le point de terminaison version 2.0.  Pour déterminer si vous devez utiliser le point de terminaison version 2.0, lisez les [limitations de la version 2.0](active-directory-v2-limitations.md).

Dans le plus courant trois « trois phases OAuth, » une application cliente est autorisée à accéder à une ressource au nom d’un utilisateur particulier.  L’autorisation est **déléguée** à partir de l’utilisateur à l’application, généralement pendant le processus [d’autorisation](active-directory-v2-scopes.md) .  Toutefois, dans le flux des informations d’identification des clients, les autorisations sont accordées **directement** à l’application elle-même.  Lorsque l’application présente un jeton à une ressource, la ressource garantit que l’application elle-même est autorisé à effectuer certaines actions - not que certains utilisateur dispose d’autorisation.

## <a name="protocol-diagram"></a>Diagramme de protocole
Le flux des informations d’identification des clients entière ressemble à ceci : chacune des étapes sont décrites en détail ci-dessous.

![Flux des informations d’identification des clients](../media/active-directory-v2-flows/convergence_scenarios_client_creds.png)

## <a name="get-direct-authorization"></a>Obtenir l’autorisation directe 
Il existe deux manières qu’une application reçoit généralement directe d’autorisation pour accéder à une ressource : via une liste de contrôle d’accès au niveau de la ressource ou affectation d’autorisations d’applications dans Azure Active Directory.  Il existe plusieurs manières qu'une ressource peut choisir d’autoriser ses clients, et chaque serveur de la ressource peut choisir la méthode qui convient le mieux pour son application.  Ces deux méthodes sont les plus courants dans Azure Active Directory et sont recommandés pour les clients et les ressources que vous souhaitent exécuter le flux des informations d’identification des clients.

### <a name="access-control-lists"></a>Listes de contrôle d’accès
Un fournisseur de ressources donné peut-être appliquer un contrôle d’autorisation basé sur une liste d’ID d’application qu’il connaît et accorde certain niveau particulier d’accès.  Lorsque la ressource reçoit un jeton de point de terminaison de la version 2.0, il peut décoder le jeton et extraire l’ID de l’Application du client à partir de la `appid` et `iss` revendications.  Il peut ensuite comparer que l’application par rapport à une liste de contrôle d’accès (et) il met à jour.  La précision et la méthode de la liste de contrôle d’accès peuvent varier considérablement à partir d’une ressource à.

Un cas d’utilisation courantes pour ces utilisateurs est tester coureurs pour une application web ou web api.  Le site web api peut-être attribuer uniquement un sous-ensemble de toutes les autorisations à ses clients différents.  Mais pour exécuter les tests de bout en bout sur l’api, un client de test est créé et acquisition par jetons à partir de la version 2.0 et les envoie à l’api.  L’api peut et puis ID de l’Application du client de test pour un accès intégral aux fonctionnalités entière de l’api.  Notez que si vous avez une telle liste sur votre service, veillez à valider non seulement l’appelant `appid`, mais également valider que la `iss` du jeton est fiable également.

Ce type d’autorisation est courant pour les comptes de service que vous avez besoin pour accéder aux données appartienne à des utilisateurs grand public avec des comptes Microsoft personnels et processus.  Pour les données appartienne à des organisations, il est recommandé d’obtenir l’autorisation nécessaire via l’application perimssions.

### <a name="application-permissions"></a>Autorisations des applications
Au lieu d’utiliser des utilisateurs, API peut exposer un ensemble d' **autorisations des applications** qui peuvent être accordées à l’application.  Une autorisation de l’application est autorisée à l’application par un administrateur d’une organisation et peut uniquement être utilisée pour accéder aux données appartienne à cette organisation et ses employés.  Par exemple, le graphique Microsoft expose plusieurs autorisations des applications :

- Lecture de messages dans toutes les boîtes aux lettres
- Lire et écrire des messages dans toutes les boîtes aux lettres
- Envoi de courrier électronique en tant que tous les utilisateurs
- Lecture des données de l’annuaire
- [+ plus](https://graph.microsoft.io)

Pour acquérir les autorisations suivantes dans votre application, vous pouvez effectuer les étapes suivantes.

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Demander les autorisations dans le portail d’inscription de l’application

- Accédez à votre application [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ou [créer une application](active-directory-v2-app-registration.md) si vous n’avez pas déjà.  Vous devez vous assurer que votre application a créé au moins une Application Secret.
- Recherchez la section **Autorisations des applications Direct** et ajouter les autorisations requises par votre application.
- Veillez à **Enregistrer** l’inscription de l’application

#### <a name="recommended-sign-the-user-into-your-app"></a>Recommandé : connecter l’utilisateur à votre application

En règle générale lors de la création d’une application qui utilise des autorisations de l’application, l’application devrez page/vue permet à l’administrateur d’approuver des autorisations de l’application.  Cette page peut faire partie de flux d’inscription de l’application, fait partie des paramètres de l’application ou un flux dédié « connecter ».  Dans de nombreux cas, il est préférable pour l’application afficher ce » se connecter » vue que lorsqu’un utilisateur a connectés avec un compte professionnel ou scolaire Microsoft.

La connexion de l’utilisateur à l’application vous permet d’identifier le visant à laquelle l’utilisateur appartient avant leur demandant d’approuver les autorisations d’application.  Tandis que pas indispensable, il peut vous aider à créer une expérience plus intuitive pour vos utilisateurs d’organisation.  Pour vous connecter l’utilisateur dans, suivez nos [didacticiels de protocole version 2.0](active-directory-v2-protocols.md).

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
| client | Obligatoire | Le client de l’annuaire que vous souhaitez demander l’autorisation à partir de.  Peut être fourni au format de nom convivial ou guid.  Si vous ne pas savoir quel client appartient l’utilisateur et souhaitez lui permettre de vous connecter avec un client, utilisez `common`. |
| identifiant_client | Obligatoire | L’Id de l’Application que le portail d’inscription ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) affecté à votre application. |
| redirect_uri | Obligatoire | Redirect_uri où vous souhaitez que la réponse à envoyer pour votre application gérer.  Il doit correspondre exactement à un de la redirect_uris que vous avez enregistré dans le portail, mais il doit être encoder au format url et peut inclure des segments de chemin d’accès supplémentaire. |
| état | recommandé | Une valeur incluse dans la requête qui est également retournée dans la réponse jeton.  Il peut être une chaîne de n’importe quel contenu que vous souhaitez.  L’état est utilisé pour coder les informations sur l’état de l’utilisateur dans l’application avant la demande d’authentification s’est produite, telles que la page ou la vue qu’ils ont été sur. |

À ce stade, Azure AD appliquera que seul l’administrateur client peut se connecter à terminer la demande.  L’administrateur est invité à approuver toutes les autorisations d’application directe que vous avez demandé pour votre application dans le portail d’inscription. 

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

Une fois que vous avez reçu une réponse correcte de l’application mise en service de point de terminaison, votre application a acquis les autorisations d’application directe qu'il a demandé.  Vous pouvez désormais déplacer sur demande un jeton pour la ressource souhaitée.

## <a name="get-a-token"></a>Obtenir un jeton
Une fois que vous avez acquis les autorisations requises pour votre application, vous pouvez poursuivre acquisition de jetons d’accès pour les API.  Pour obtenir un jeton à l’aide du client accorder des informations d’identification, envoyez une demande de publication pour le `/token` point de terminaison version 2.0 :

```
POST /common/oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials
```

```
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | --------------- |
| identifiant_client | Obligatoire | L’Id de l’Application que le portail d’inscription ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) affecté à votre application. |
| étendue | Obligatoire | La valeur passée pour la `scope` paramètre dans cette demande doit être l’identifiant (application ID URI) de la ressource souhaitée, apposée avec la `.default` suffixe.  Donc dans l’exemple Microsoft Graph donné, la valeur doit être `https://graph.microsoft.com/.default`.  Cette valeur indique le point de terminaison version 2.0 que de toutes les autorisations d’application directe que vous avez configuré pour votre application, il doit afficher un jeton pour ceux relatifs à la ressource souhaitée. |
| client_secret | Obligatoire | Le code Secret Application que vous avez généré dans le portail d’inscription de votre application. |
| grant_type | Obligatoire | Doit être `client_credentials`. | 

#### <a name="successful-response"></a>Réponse correcte
Une réponse correcte prendra la forme :

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| access_token | Le jeton d’accès demandés. L’application peut utiliser ce jeton s’authentifier à la ressource sécurisée, telle qu’un site web API. |
| token_type | Indique la valeur de type de jeton. Le seul type qui prend en charge Azure AD est `Bearer`.  |
| expires_in | Combien de temps le jeton d’accès est valide (en secondes). |

#### <a name="error-response"></a>Réponse d’erreur
Une réponse d’erreur prendra la forme :

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| erreur | Une chaîne de code d’erreur qui peut être utilisée pour classer les types d’erreurs qui se produisent et peut être utilisée pour réagir aux erreurs. |
| error_description | Un message d’erreur spécifique qui peut vous aider à un développeur identifier la cause d’une erreur d’authentification.  |
| error_codes | Liste des codes d’erreur spécifiques STS qui peuvent vous aider dans diagnostics.  |
| horodatage | Le moment auquel l’erreur s’est produite. |
| argument trace_id | Un identificateur unique pour la requête peut vous aider à diagnostics.  |
| correlation_id | Un identificateur unique pour la requête peut vous aider à diagnostics par les différents composants. |

## <a name="use-a-token"></a>Utiliser un jeton
À présent que vous avez acquis un jeton, vous pouvez utiliser ce jeton pour demander à la ressource.  Si le jeton a expiré, il suffit de répéter la demande à la `/token` point de terminaison d’acquérir un jeton d’accès novateur.

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro Tip: Try the below command out! (but replace the token with your own)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q" 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-sample"></a>Exemple de code
Pour visualiser un exemple d’une application met en œuvre que la client_credentials accorder à l’aide de l’administrateur de consentement point de terminaison, reportez-vous à notre [exemple de code de processus version 2.0](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
