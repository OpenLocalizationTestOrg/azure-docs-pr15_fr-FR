

<properties
    pageTitle="Flux de Code Azure AD version 2.0 OAuth autorisation | Microsoft Azure"
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
    ms.date="08/08/2016"
    ms.author="dastrock"/>

# <a name="v20-protocols---oauth-20-authorization-code-flow"></a>version 2.0 protocoles - flux de Code OAuth 2.0 d’autorisation

L’octroi 2.0 OAuth d’autorisation code peut être utilisé dans les applications installées sur un appareil d’accéder à des ressources protégées, telles que web API.  À l’aide de mise en œuvre de la version 2.0 modèle application du jeton 2.0, vous pouvez ajouter se connecter et API accéder à vos applications de bureau et mobiles.  Ce guide est indépendant du langage et explique comment envoyer et recevoir des messages HTTP sans utiliser l’un de nos bibliothèques open source.

<!-- TODO: Need link to libraries -->

> [AZURE.NOTE]
    Pas tous les scénarios Azure Active Directory et fonctionnalités sont prises en charge par le point de terminaison version 2.0.  Pour déterminer si vous devez utiliser le point de terminaison version 2.0, lisez les [limitations de la version 2.0](active-directory-v2-limitations.md).

Le flux du code d’autorisation OAuth 2.0 est décrit au [point 4.1 de la spécification 2.0 OAuth](http://tools.ietf.org/html/rfc6749).  Il est utilisé pour effectuer l’authentification et l’autorisation dans la plupart des types d’application, y compris les [applications web](active-directory-v2-flows.md#web-apps) et [en mode natif installé les applications](active-directory-v2-flows.md#mobile-and-native-apps).  Il permet aux applications en toute sécurité acquérir access_tokens qui peut être utilisé pour accéder aux ressources sont sécurisées à l’aide de point de terminaison de la version 2.0.  

## <a name="protocol-diagram"></a>Diagramme de protocole
À un niveau élevé, le flux d’authentification pour une application mobile/natif ressemble un peu comme suit :

![Flux de Code Auth OAuth](../media/active-directory-v2-flows/convergence_scenarios_native.png)

## <a name="request-an-authorization-code"></a>Demander un code d’autorisation
Le flux du code d’autorisation commence par le client dirigeant l’utilisateur de la `/authorize` point de terminaison.  Dans cette demande, le client indique les autorisations que requises pour acquérir à partir de l’utilisateur :

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345
```

> [AZURE.TIP] Cliquez sur le lien ci-dessous pour exécuter cette requête ! Après la connexion, votre navigateur doit être redirigé vers `https://localhost/myapp/` avec un `code` dans la barre d’adresses.
    <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/Common/oauth2/v2.0/Authorize...</a>

| Paramètre | | Description |
| ----------------------- | ------------------------------- | --------------- |
| client | Obligatoire | La `{tenant}` valeur dans le chemin d’accès de la requête peut être utilisée pour contrôler qui peut vous connecter à l’application.  Les valeurs autorisées sont `common`, `organizations`, `consumers`et identificateurs du client.  Pour plus d’informations, voir [Concepts de protocole](active-directory-v2-protocols.md#endpoints). |
| identifiant_client | Obligatoire | L’Id de l’Application que le portail d’inscription ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) affecté à votre application. |
| response_type | Obligatoire | Doit inclure `code` pour le flux de code d’autorisation. |
| redirect_uri | recommandé | Redirect_uri de votre application, où les réponses d’authentification peuvent être envoyés et reçus par votre application.  Il doit correspondre exactement à un de la redirect_uris que vous avez enregistré dans le portail, mais il doit être encoder au format url.  Pour les applications natives et mobiles, vous devez utiliser la valeur par défaut `https://login.microsoftonline.com/common/oauth2/nativeclient`. |
| étendue | Obligatoire | Liste des [étendues](active-directory-v2-scopes.md) que vous voulez que l’utilisateur acceptez séparés par des espaces.  |
| response_mode | recommandé | Spécifie la méthode qui doit être utilisée pour envoyer l’arrière jeton qui en résulte dans votre application.  Peut être `query` ou `form_post`.  |
| état | recommandé | Une valeur incluse dans la requête qui est également retournée dans la réponse jeton.  Il peut être une chaîne de n’importe quel contenu que vous souhaitez.  Une valeur unique générée de manière aléatoire est généralement utilisée pour [protéger contre les attaques de falsification intersites demande](http://tools.ietf.org/html/rfc6749#section-10.12).  L’état est également utilisé pour coder les informations sur l’état de l’utilisateur dans l’application avant la demande d’authentification s’est produite, telles que la page ou la vue qu’ils ont été sur. |
| invite de commandes | facultatif | Indique le type d’interaction de l’utilisateur qui est requis.  Les valeurs valides uniquement pour le moment sont « login », « aucun » et « acceptez ».  `prompt=login`force l’utilisateur à entrer ses informations d’identification sur cette demande, une inversion connexion unique.  `prompt=none`est l’inverse - elle veille à ce que l’utilisateur n’est pas présenté avec n’importe quelle invite interactive que ce soit.  Si la demande ne peut pas être effectuée en mode silencieux via l’authentification unique sur, le point de terminaison version 2.0 renverra une erreur.  `prompt=consent`se déclenche la boîte de dialogue consentement OAuth une fois que l’utilisateur se connecte, demandant à l’utilisateur pour accorder des autorisations à l’application. |
| login_hint | facultatif | Peut servir à remplir au préalable le champ adresse de messagerie/nom d’utilisateur de la page pour l’utilisateur, se connecter si vous connaissez son nom d’utilisateur à l’avance.  Applications souvent utilisent ce paramètre au cours de ré-authentification, après avoir déjà extrait le nom d’utilisateur à partir d’un précédent se connecter à l’aide de la `preferred_username` réclamer. |
| domain_hint | facultatif | Peut s’agir de `consumers` ou `organizations`.  Si inclus, il ignore le processus de découverte des e-mails cet utilisateur parcourt dans la version 2.0 page se connecter, et causer une expérience utilisateur légèrement plus agréable.  Applications souvent utilisent ce paramètre au cours de ré-authentification, en extrayant le `tid` à partir d’une connexion précédente.  Si la `tid` réclamer la valeur est `9188040d-6c67-4c5b-b112-36a304b66dad`, vous devez utiliser `domain_hint=consumers`.  Dans le cas contraire, utilisez `domain_hint=organizations`. |

À ce stade, l’utilisateur est invité à entrer ses informations d’identification et terminer l’authentification.  Le point de terminaison version 2.0 permet également de garantir que l’utilisateur a accepté pour les autorisations indiquées dans le `scope` paramètre de requête.  Si l’utilisateur n’a pas accepté à certaines de ces autorisations, il demande à l’utilisateur à donner son consentement pour les autorisations requises.  Détails des [autorisations, accord et applications clients multiples sont fournies ici](active-directory-v2-scopes.md).

Une fois que l’utilisateur s’authentifie et consentement, le point de terminaison version 2.0 renverra une réponse à votre application en l’indiquée `redirect_uri`, à l’aide de la méthode spécifiée dans le `response_mode` paramètre.

#### <a name="successful-response"></a>Réponse correcte
Une réponse correcte à l’aide `response_mode=query` ressemble à :

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| code | Authorization_code qui a demandé l’application. L’application peut utiliser le code d’autorisation pour demander un jeton d’accès pour la ressource cible.  Authorization_codes sont très courte durée de vie, en général ils expirent après environ 10 minutes. |
| état | Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état dans la demande et réponse sont identiques. |

#### <a name="error-response"></a>Réponse d’erreur
Réponses d’erreur peuvent également être envoyées à la `redirect_uri` afin que l’application peut gérer les correctement :

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| erreur | Une chaîne de code d’erreur qui peut être utilisée pour classer les types d’erreurs qui se produisent et peut être utilisée pour réagir aux erreurs. |
| error_description | Un message d’erreur spécifique qui peut vous aider à un développeur identifier la cause d’une erreur d’authentification.  |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Codes d’erreur pour les erreurs de point de terminaison d’autorisation

Le tableau suivant décrit les différents codes d’erreur qui peuvent être retournés dans le `error` paramètre de la réponse d’erreur.

| Code d’erreur | Description | Action du client |
|------------|-------------|---------------|
| invalid_request | Erreur de protocole, par exemple un paramètre requis manquant. | Résoudre et renvoyer la demande. Il s’agit d’un développement erreur est généralement capturé lors du test initial.|
| unauthorized_client | L’application cliente n’est pas autorisée à demander un code d’autorisation. | Cela se produit généralement lors de l’application cliente n’est pas enregistrée dans Azure AD ou n’est pas ajoutée au client d’Azure AD de l’utilisateur. L’application peut demander à l’utilisateur avec jeu d’instructions pour installer l’application et son ajout à Azure AD. |
| ACCESS_DENIED | Propriétaire de la ressource refusé consentement | L’application cliente peut avertir l’utilisateur qu’il ne peut pas continuer à moins que l’utilisateur accepte. |
| unsupported_response_type | Le serveur d’autorisation ne prend pas en charge le type de réponse dans la demande. | Résoudre et renvoyer la demande. Il s’agit d’un développement erreur est généralement capturé lors du test initial.|
|server_error | Le serveur a rencontré une erreur inattendue. | Relancer la demande. Ces erreurs peuvent provenir de conditions temporaires. L’application cliente peut expliquer à l’utilisateur que sa réponse est retardée raison d’une erreur temporaire. |
| temporarily_unavailable | Le serveur est temporairement occupé et gérer la demande. | Relancer la demande. L’application cliente peut expliquer à l’utilisateur que sa réponse est retardée en raison d’un problème temporaire. |
| invalid_resource |La ressource cible n’est pas valide, car il n’existe pas, Azure AD ne le trouvez pas ou n’est pas configuré correctement.| Cela indique la ressource, le cas échéant, n'a pas été configurée dans le client. L’application peut demander à l’utilisateur avec jeu d’instructions pour installer l’application et son ajout à Azure AD. |

## <a name="request-an-access-token"></a>Demander un jeton d’accès
Maintenant que vous avez acquis un authorization_code et autorisation avez été accordée par l’utilisateur, vous pouvez récupérer la `code` pour un `access_token` à la ressource de votre choix, en envoyant un `POST` demander à la `/token` point de terminaison :

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [AZURE.TIP] Essayez d’exécuter cette requête dans Postman ! (N’oubliez pas de remplacer les `code`)     [ ![Exécuter dans Postman](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)

| Paramètre | | Description |
| ----------------------- | ------------------------------- | --------------------- |
| client | Obligatoire | La `{tenant}` valeur dans le chemin d’accès de la requête peut être utilisée pour contrôler qui peut vous connecter à l’application.  Les valeurs autorisées sont `common`, `organizations`, `consumers`et identificateurs du client.  Pour plus d’informations, voir [Concepts de protocole](active-directory-v2-protocols.md#endpoints). |
| identifiant_client | Obligatoire | L’Id de l’Application que le portail d’inscription ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) affecté à votre application. |
| grant_type | Obligatoire | Doit être `authorization_code` pour le flux de code d’autorisation. |
| étendue | Obligatoire | Liste des étendues séparés par des espaces.  Les étendues demandés dans cette pouce doivent être équivalent à ou un sous-ensemble des étendues demandé dans la première branche.  Si les étendues spécifiés dans cette requête s’étalent sur plusieurs serveurs de ressources, le point de terminaison version 2.0 retournera un jeton pour la ressource spécifiée dans la première étendue.  Pour une explication plus détaillée des étendues, reportez-vous à [autorisations, accord et les étendues](active-directory-v2-scopes.md).  |
| code | Obligatoire | Authorization_code que vous avez acheté dans la première branche du flux.   |
| redirect_uri | Obligatoire | La même valeur redirect_uri qui a été utilisée pour acquérir les authorization_code. |
| client_secret | requis pour les applications web | Le code secret application que vous avez créée dans le portail d’inscription de l’application de votre application.  Il ne doit pas être utilisé dans une application native, car client_secrets ne peuvent pas être stockés fiable sur les appareils.  Il est nécessaire pour web API qui ont la possibilité de stocker le client_secret en toute sécurité sur le côté serveur et les applications web. |

#### <a name="successful-response"></a>Réponse correcte
Une réponse jeton correcte se présente comme :

```
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Paramètre | Description |
| ----------------------- | ------------------------------- |
| access_token | Le jeton d’accès demandés. L’application peut utiliser ce jeton s’authentifier à la ressource sécurisée, telle qu’un site web API. |
| token_type | Indique la valeur de type de jeton. Le seul type qui prend en charge Azure AD est porteur  |
| expires_in | Combien de temps le jeton d’accès est valide (en secondes). |
| étendue | Les étendues l’access_token valide pour. |
| refresh_token |  Jeton d’actualisation OAuth 2.0. L’application peut utiliser ce jeton acquérir des jetons d’accès supplémentaires après le jeton d’accès en cours arrive à expiration.  Refresh_tokens sont à long terme et peut être utilisé pour conserver l’accès aux ressources pour une période prolongée.  Pour plus d’informations, reportez-vous à la [référence de jeton de version 2.0](active-directory-v2-tokens.md).  |
| id_token | Un unsigned JSON Web jeton (JWT). La base64Url can application coder les segments du jeton pour demander des informations sur l’utilisateur connecté. L’application peut mettre en cache les valeurs et les afficher, mais elle ne devez pas compter les les limites de sécurité ou d’autorisation.  Pour plus d’informations sur id_tokens voir la [référence de jeton de point de terminaison de version 2.0](active-directory-v2-tokens.md). |

#### <a name="error-response"></a>Réponse d’erreur
Réponses d’erreur seront présente comme :

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
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

#### <a name="error-codes-for-token-endpoint-errors"></a>Codes d’erreur pour les erreurs de point de terminaison jetons

| Code d’erreur | Description | Action du client |
|------------|-------------|---------------|
| invalid_request | Erreur de protocole, par exemple un paramètre requis manquant. | Résoudre et renvoyer la demande |
| invalid_grant | Le code d’autorisation n’est pas valide ou a expiré. | Essayez une nouvelle demande à la `/authorize` point de terminaison |
| unauthorized_client | Le client authentifié n’est pas autorisé à utiliser ce type d’accorder l’autorisation. | Cela se produit généralement lors de l’application cliente n’est pas enregistrée dans Azure AD ou n’est pas ajoutée au client d’Azure AD de l’utilisateur. L’application peut demander à l’utilisateur avec jeu d’instructions pour installer l’application et son ajout à Azure AD. |
| invalid_client | Échoué de l’authentification de client. | Les informations d’identification client ne sont pas valides. Pour résoudre le problème, l’administrateur d’application met à jour les informations d’identification. |
| unsupported_grant_type | Le serveur d’autorisation ne prend pas en charge le type d’accorder l’autorisation. | Modifier le type de licence dans la demande. Ce type d’erreur doit se produire uniquement pendant le développement et être détecté au cours des tests initial. |
| invalid_resource | La ressource cible n’est pas valide, car il n’existe pas, Azure AD ne le trouvez pas ou n’est pas configuré correctement. | Cela indique la ressource, le cas échéant, n'a pas été configurée dans le client. L’application peut demander à l’utilisateur avec jeu d’instructions pour installer l’application et son ajout à Azure AD. |
| interaction_required | La demande nécessite une interaction utilisateur. Par exemple, une étape d’authentification supplémentaire est requise. | Relancer la demande avec la même ressource. |
| temporarily_unavailable | Le serveur est temporairement occupé et gérer la demande. | Relancer la demande. L’application cliente peut expliquer à l’utilisateur que sa réponse est retardée en raison d’un problème temporaire.|

## <a name="use-the-access-token"></a>Utiliser le jeton d’accès
À présent que vous avez obtenu une `access_token`, vous pouvez utiliser le jeton dans les requêtes à des API Web en incluant dans la `Authorization` en-tête :

> [AZURE.TIP] Exécuter cette demande dans Postman ! (Remplacer les `Authorization` en-tête première)     [ ![Exécuter dans Postman](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>Actualiser le jeton d’accès
Access_tokens sont courte durée de vie, et vous devez actualiser les qui arrivent à expiration pour continuer à accéder à des ressources.  Vous pouvez le faire en envoyant un autre `POST` demander à la `/token` point de terminaison, cette fois fournissant la `refresh_token` au lieu de la `code`:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [AZURE.TIP] Essayez d’exécuter cette requête dans Postman ! (N’oubliez pas de remplacer les `refresh_token`)     [ ![Exécuter dans Postman](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)

| Paramètre | | Description |
| ----------------------- | ------------------------------- | -------- |
| client | Obligatoire | La `{tenant}` valeur dans le chemin d’accès de la requête peut être utilisée pour contrôler qui peut vous connecter à l’application.  Les valeurs autorisées sont `common`, `organizations`, `consumers`et identificateurs du client.  Pour plus d’informations, voir [Concepts de protocole](active-directory-v2-protocols.md#endpoints). |
| identifiant_client | Obligatoire | L’Id de l’Application que le portail d’inscription ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) affecté à votre application. |
| grant_type | Obligatoire | Doit être `refresh_token` pour cette branche du flux du code d’autorisation. |
| étendue | Obligatoire | Liste des étendues séparés par des espaces.  Les étendues demandés dans cette pouce doivent être équivalent à ou un sous-ensemble des étendues demandé dans la branche demande authorization_code d’origine.  Si les étendues spécifiés dans cette requête s’étalent sur plusieurs serveurs de ressources, le point de terminaison version 2.0 retournera un jeton pour la ressource spécifiée dans la première étendue.  Pour une explication plus détaillée des étendues, reportez-vous à [autorisations, accord et les étendues](active-directory-v2-scopes.md).  |
| refresh_token | Obligatoire | Refresh_token que vous avez acheté dans la deuxième branche du flux.   |
| redirect_uri | Obligatoire | La même valeur redirect_uri qui a été utilisée pour acquérir les authorization_code. |
| client_secret | requis pour les applications web | Le code secret application que vous avez créée dans le portail d’inscription de l’application de votre application.  Il ne doit pas être utilisé dans une application native, car client_secrets ne peuvent pas être stockés fiable sur les appareils.  Il est nécessaire pour web API qui ont la possibilité de stocker le client_secret en toute sécurité sur le côté serveur et les applications web. |

#### <a name="successful-response"></a>Réponse correcte
Une réponse jeton correcte se présente comme :

```
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Paramètre | Description |
| ----------------------- | ------------------------------- |
| access_token | Le jeton d’accès demandés. L’application peut utiliser ce jeton s’authentifier à la ressource sécurisée, telle qu’un site web API. |
| token_type | Indique la valeur de type de jeton. Le seul type qui prend en charge Azure AD est porteur  |
| expires_in | Combien de temps le jeton d’accès est valide (en secondes). |
| étendue | Les étendues l’access_token valide pour. |
| refresh_token |  Un nouveau jeton d’actualisation OAuth 2.0. Vous devez remplacer l’ancien jeton d’actualisation avec ce jeton actualisation récemment acquises pour garantir que vos jetons actualisation restent valides pour autant que possible.  |
| id_token | Un unsigned JSON Web jeton (JWT). La base64Url can application coder les segments du jeton pour demander des informations sur l’utilisateur connecté. L’application peut mettre en cache les valeurs et les afficher, mais elle ne devez pas compter les les limites de sécurité ou d’autorisation.  Pour plus d’informations sur id_tokens voir la [référence de jeton de point de terminaison de version 2.0](active-directory-v2-tokens.md). |

#### <a name="error-response"></a>Réponse d’erreur
```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
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

Pour obtenir une description des codes d’erreur et l’action client recommandé, voir [codes d’erreur pour les erreurs de point de terminaison jeton](#error-codes-for-token-endpoint-errors).
