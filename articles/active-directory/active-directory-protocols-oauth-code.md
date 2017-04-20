<properties
    pageTitle="Vue d’ensemble du protocole Azure AD .NET | Microsoft Azure"
    description="Cet article décrit comment utiliser les messages HTTP pour autoriser l’accès aux applications web et des API web de votre client à l’aide de Azure Active Directory et OAuth 2.0."
    services="active-directory"
    documentationCenter=".net"
    authors="priyamohanram"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="priyamo"/>


# <a name="authorize-access-to-web-applications-using-oauth-20-and-azure-active-directory"></a>Autoriser l’accès aux applications web à l’aide de OAuth 2.0 et Azure Active Directory

Utilisations Azure Active Directory (AD Azure) OAuth 2.0 vous permet d’autoriser l’accès aux applications web et des API web de votre client Azure AD. Ce guide est indépendante du langage et explique comment envoyer et recevoir des messages HTTP sans utiliser l’un de nos bibliothèques open source.

Le flux de code 2.0 OAuth d’autorisation est décrite dans la [section 4.1 de la spécification 2.0 OAuth](https://tools.ietf.org/html/rfc6749#section-4.1) . Il est utilisé pour effectuer l’authentification et l’autorisation dans la plupart des types d’application, y compris les applications web et installé en mode natif des applications.

[AZURE.INCLUDE [active-directory-protocols-getting-started](../../includes/active-directory-protocols-getting-started.md)]


## <a name="oauth-20-authorization-flow"></a>Flux d’autorisation OAuth 2.0

À un niveau élevé, le flux d’autorisation entière pour une application ressemble un peu comme suit :

![Flux de Code Auth OAuth](media/active-directory-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)


## <a name="request-an-authorization-code"></a>Demander un code d’autorisation

Le flux du code d’autorisation commence par le client dirigeant l’utilisateur de la `/authorize` point de terminaison. Dans cette demande, le client indique les autorisations que requises pour acquérir à partir de l’utilisateur. Vous pouvez obtenir les points de terminaison OAuth 2.0 à partir de votre page de l’application portail classique Azure, sur le bouton **Points de terminaison de vue** dans le bac d’alimentation bas.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&resource=https%3A%2F%2Fservice.contoso.com%2F
&state=12345
```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | --------------- |
| client | Obligatoire | La `{tenant}` valeur dans le chemin d’accès de la requête peut être utilisée pour contrôler qui peut vous connecter à l’application.  Les valeurs autorisées sont les identificateurs de client, par exemple, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` ou `common` pour les jetons indépendante du client |
| identifiant_client | Obligatoire | L’Id de l’Application affectée à votre application lors de l’enregistrement avec Azure AD. Vous pouvez le trouver dans le portail de gestion Azure. Cliquez sur dans **Active Directory**, cliquez sur le répertoire, cliquez sur l’application et puis cliquez sur **configurer** |
| response_type | Obligatoire | Doit inclure `code` pour le flux de code d’autorisation. |
| redirect_uri | recommandé | Redirect_uri de votre application, où les réponses d’authentification peuvent être envoyés et reçus par votre application.  Il doit correspondre exactement à un de la redirect_uris que vous avez enregistré dans le portail, mais il doit être encoder au format url.  Pour les applications natives et mobiles, vous devez utiliser la valeur par défaut `urn:ietf:wg:oauth:2.0:oob`. |
| response_mode | recommandé | Spécifie la méthode qui doit être utilisée pour envoyer l’arrière jeton qui en résulte dans votre application.  Peut être `query` ou `form_post`.  |
| état | recommandé | Une valeur incluse dans la requête qui est également retournée dans la réponse jeton. Une valeur unique générée de manière aléatoire est généralement utilisée pour [protéger contre les attaques de falsification intersites demande](http://tools.ietf.org/html/rfc6749#section-10.12).  L’état est également utilisé pour coder les informations sur l’état de l’utilisateur dans l’application avant la demande d’authentification s’est produite, telles que la page ou la vue qu’ils ont été sur. |
| ressource | facultatif | Application ID URI du site web API (ressource sécurisée). Pour trouver l’URI ID d’application du site web API, dans le portail de gestion Azure, cliquez sur **Active Directory**, cliquez sur le répertoire, cliquez sur l’application et puis cliquez sur **configurer**. |
| invite de commandes | facultatif |  Indiquer le type d’interaction de l’utilisateur qui est requis.<p> Les valeurs valides sont : <p> *connexion*: l’utilisateur doit être invité à s’authentifier à nouveau. <p> *consentement*: consentement de l’utilisateur a été octroyée, mais doit être mis à jour. L’utilisateur doit être invité à consentement. <p> *admin_consent*: un administrateur doit être invité à consentement au nom de tous les utilisateurs de leur organisation |
| login_hint | facultatif | Peut servir à remplir au préalable le champ adresse de messagerie/nom d’utilisateur de la page pour l’utilisateur, se connecter si vous connaissez son nom d’utilisateur à l’avance.  Applications souvent utilisent ce paramètre au cours de ré-authentification, après avoir déjà extrait le nom d’utilisateur à partir d’un précédent se connecter à l’aide de la `preferred_username` réclamer. |
| domain_hint | facultatif | Fournit une indication sur le client ou le domaine que l’utilisateur doit utiliser pour se connecter. La valeur de la domain_hint est un domaine enregistré pour le client. Si le client fédéré dans un répertoire local, AAD redirige vers le serveur de fédération client spécifié. |

> [AZURE.NOTE] Si l’utilisateur fait partie d’une organisation, un administrateur de l’organisation peut consentement ou refuser part de l’utilisateur ou autoriser l’utilisateur à consentement. Il se voit la possibilité de consentement uniquement lorsque l’administrateur le permet.

À ce stade, l’utilisateur est invité à entrer ses informations d’identification et acceptez les autorisations indiquées dans le `scope` paramètre de requête. Une fois que l’utilisateur s’authentifie et consentement, Azure AD envoie une réponse à votre application à la `redirect_uri` adresse de votre requête.

### <a name="successful-response"></a>Réponse correcte

Une réponse correcte peut ressembler à ceci :

```
GET  HTTP/1.1 302 Found
Location: http://localhost/myapp/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Paramètre | Description |
| -----------------------| --------------- |
| admin_consent | La valeur est vrai si un administrateur accepté à l’invite de demande de consentement.|
| code | Le code d’autorisation demandé par l’application. L’application peut utiliser le code d’autorisation pour demander un jeton d’accès pour la ressource cible. |
| session_state | Une valeur unique qui identifie la session utilisateur en cours. Cette valeur est un GUID, mais doit être considérée comme une valeur opaque est passée sans examen. |
| état | Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. Il est conseillé de l’application pour vérifier que les valeurs d’état dans la demande et réponse sont identiques avant d’utiliser la réponse. Cela vous permet de détecter [les attaques intersites demander falsification (CSRF)](https://tools.ietf.org/html/rfc6749#section-10.12) sur le client.  

### <a name="error-response"></a>Réponse d’erreur

Réponses d’erreur peuvent également être envoyées à la `redirect_uri` afin que l’application peut les gérer correctement.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Paramètre | Description |
|-----------|-------------|
| erreur | Une valeur de code d’erreur définie dans la Section 5,2. de [Structure de OAuth 2.0 d’autorisation](http://tools.ietf.org/html/rfc6749). Le tableau suivant décrit les codes d’erreur qui retourne Azure AD. |
| error_description | Une description plus détaillée de l’erreur. Ce message n’est pas destiné à être convivial de l’utilisateur final. |
| état | La valeur d’état est une valeur non réutilisé générée aléatoirement qui est envoyée dans la demande et retournée dans la réponse pour empêcher les attaques intersites demande falsification (CSRF). |

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

## <a name="use-the-authorization-code-to-request-an-access-token"></a>Utiliser le code d’autorisation pour demander un jeton d’accès

Maintenant que vous avez acquis un code d’autorisation et autorisation avez été accordée par l’utilisateur, vous pouvez utiliser le code pour un jeton d’accès à la ressource de votre choix, en envoyant une demande POST à la `/token` point de terminaison :

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded
grant_type=authorization_code
&client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&redirect_uri=https%3A%2F%2Flocalhost%2Fmyapp%2F
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=p@ssw0rd

//NOTE: client_secret only required for web apps
```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | --------------------- |
| client | Obligatoire |  La `{tenant}` valeur dans le chemin d’accès de la requête peut être utilisée pour contrôler qui peut vous connecter à l’application.  Les valeurs autorisées sont les identificateurs de client, par exemple, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` ou `common` pour les jetons indépendante du client |
| identifiant_client | Obligatoire | L’Id de l’Application affectée à votre application lors de l’enregistrement avec Azure AD. Vous pouvez le trouver dans le portail classique Azure. Cliquez sur dans **Active Directory**, cliquez sur le répertoire, cliquez sur l’application et puis cliquez sur **configurer** |
| grant_type | Obligatoire | Doit être `authorization_code` pour le flux de code d’autorisation. |
| code | Obligatoire | La `authorization_code` que vous avez acheté dans la section précédente   |
| redirect_uri | Obligatoire | La même `redirect_uri` valeur qui a été utilisé pour acquérir les `authorization_code`. |
| client_secret | requis pour les applications web | Le code secret application que vous avez créée dans le portail d’inscription de l’application de votre application.  Il ne doit pas être utilisé dans une application native, car client_secrets ne peuvent pas être stockés fiable sur les appareils.  Il est requis pour les applications web et web API qui ont la possibilité de stocker le `client_secret` en toute sécurité sur le côté serveur. |
| ressource | obligatoire si spécifié dans la demande de code d’autorisation, sinon facultatif | Application ID URI du site web API (ressource sécurisée).
Pour trouver l’URI ID de l’application, dans le portail de gestion Azure, cliquez sur **Active Directory**et cliquez sur le répertoire, cliquez sur l’application, puis cliquez sur **configurer**.

### <a name="successful-response"></a>Réponse correcte

Azure AD renvoie un jeton d’accès à une réponse correcte. Pour limiter les appels réseau à partir de l’application cliente et leur latence associé, l’application cliente doit mettre en cache les jetons d’accès pour la durée de vie jeton spécifié dans la réponse OAuth 2.0. Pour déterminer la durée de vie jeton, utilisez des options du `expires_in` ou `expires_on` valeurs de paramètre.

Si une ressource d’API web renvoie un `invalid_token` code d’erreur, cela peut indiquer que la ressource a déterminé que le jeton a expiré. Si les temps d’horloge client et des ressources sont différent (appelé un « délai maximal autorisé »), la ressource pouvez envisager le jeton à expiration avant que le jeton est désactivé dans le cache du client. Dans ce cas, désactivez le jeton à partir du cache, même si elle est toujours dans sa durée de vie calculée.

Une réponse correcte peut ressembler à ceci :

```
{
  "access_token": " eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1388444763",
  "resource": "https://service.contoso.com/",
  "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA",
  "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
"id_token": " eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.”
}

```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| access_token | Le jeton d’accès demandés. L’application peut utiliser ce jeton s’authentifier à la ressource sécurisée, telle qu’un site web API. |
| token_type | Indique la valeur de type de jeton. Le seul type qui prend en charge Azure AD est porteur. Pour plus d’informations sur les jetons porteur, voir [OAuth2.0 autorisation Framework : utilisation jeton porteur (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt)  |
| expires_in | Combien de temps le jeton d’accès est valide (en secondes). |
| expires_on | Heure à laquelle le jeton d’accès arrive à expiration. La date est représentée par le nombre de secondes à partir du 1970-01-01T0:0:0Z UTC jusqu'à ce que le délai d’expiration. Cette valeur est utilisée pour déterminer la durée de vie des jetons mis en cache. |
| ressource | Application ID URI du site web API (ressource sécurisée).|
| étendue | Autorisations d’emprunt d’identité accordées à l’application cliente. L’autorisation par défaut est `user_impersonation`. Le propriétaire de la ressource sécurisée peut enregistrer des valeurs supplémentaires dans Azure AD.|
| refresh_token |  Jeton d’actualisation OAuth 2.0. L’application peut utiliser ce jeton pour acquérir les jetons d’accès supplémentaires après que le jeton d’accès en cours arrive à expiration.  Actualiser les jetons sont à long terme et peut être utilisés pour conserver l’accès aux ressources pour une période prolongée. |
| id_token | Un unsigned JSON Web jeton (JWT). La base64Url can application coder les segments du jeton pour demander des informations sur l’utilisateur connecté. L’application peut mettre en cache les valeurs et les afficher, mais elle ne devez pas compter les les limites de sécurité ou d’autorisation. |

### <a name="jwt-token-claims"></a>Revendications des jetons JWT
Le jeton JWT dans la valeur de la `id_token` paramètre pouvez décoder dans les affirmations suivantes :

```
{
 "typ": "JWT",
 "alg": "none"
}.
{
 "aud": "2d4d11a2-f814-46a7-890a-274a72a7309e",
 "iss": "https://sts.windows.net/7fe81447-da57-4385-becb-6de57f21477e/",
 "iat": 1388440863,
 "nbf": 1388440863,
 "exp": 1388444763,
 "ver": "1.0",
 "tid": "7fe81447-da57-4385-becb-6de57f21477e",
 "oid": "68389ae2-62fa-4b18-91fe-53dd109d74f5",
 "upn": "frank@contoso.com",
 "unique_name": "frank@contoso.com",
 "sub": "JWvYdCWPhhlpS1Zsf7yYUxShUwtUm5yzPmw_-jX3fHY",
 "family_name": "Miller",
 "given_name": "Frank"
}.
```

La `id_token` paramètre inclut les types suivants de la demande. Pour plus d’informations sur les jetons web JSON, voir la [spécification de brouillon JWT IETF](http://go.microsoft.com/fwlink/?LinkId=392344). Pour plus d’informations sur les revendications et les types de jetons, veuillez lire [jeton pris en charge et les Types de revendications](active-directory-token-and-claims.md)

| Type de déclaration | Description |
|------------|-------------|
| AUD | Audience du jeton. Lorsque le jeton est émis pour une application cliente, le public est la `client_id` du client.
| EXP | Délai d’expiration. Heure à laquelle le jeton arrive à expiration. Pour que le jeton soit valide, la date/heure actuelle doit être inférieure ou égale à la `exp` valeur. Le temps est représenté par le nombre de secondes du 1er janvier 1970 (1970-01-01T0:0:0Z) UTC jusqu'à ce que l’heure d’émission du jeton. |
| family_name | L’utilisateur nom de famille. L’application peut afficher cette valeur. |
| given_name | Prénom de l’utilisateur. L’application peut afficher cette valeur. |
| IAT | Émis en temps. Heure à laquelle la JWT a été émis. Le temps est représenté par le nombre de secondes du 1er janvier 1970 (1970-01-01T0:0:0Z) UTC jusqu'à ce que l’heure d’émission du jeton. |
| ISS | Identifie l’émetteur de jetons |
| NBF | Pas avant d’heure. Heure à laquelle le jeton prend effet. Pour que le jeton soit valide, la date/heure actuelle doit être supérieure ou égale à la valeur Nbf. Le temps est représenté par le nombre de secondes du 1er janvier 1970 (1970-01-01T0:0:0Z) UTC jusqu'à ce que l’heure d’émission du jeton. |
| OID | Identificateur d’objet (ID) de l’objet utilisateur dans Active Directory Azure. |
| Sub | Identificateur du jeton sujet. Il s’agit d’un identificateur permanent et immuable pour l’utilisateur qui décrit le jeton. Utiliser cette valeur dans la mise en cache logique. |
| TID | Identificateur (ID) du client Azure AD qui a émis le jeton du client. |
| nom_unique | Un identificateur unique pour qui peut être affiché à l’utilisateur. Il s’agit généralement d’un nom d’utilisateur principal (UPN). |
| UPN | Nom d’utilisateur principal de l’utilisateur. |
| veur | Version. La version du jeton JWT, généralement 1.0. |

### <a name="error-response"></a>Réponse d’erreur

Les erreurs de point de terminaison d’émission du jeton sont les codes d’erreur HTTP, étant donné que le client appelle le point de terminaison d’émission du jeton directement. Outre le code d’état HTTP, le point de terminaison d’émission du jeton Azure AD renvoie également un document JSON avec des objets qui décrivent l’erreur.

Un exemple de réponse d’erreur peut ressembler à ceci :

```
{
  "error": "invalid_grant",
  "error_description": "AADSTS70002: Error validating credentials. AADSTS70008: The provided authorization code or refresh token is expired. Send a new interactive authorization request for this user and resource.\r\nTrace ID: 3939d04c-d7ba-42bf-9cb7-1e5854cdce9e\r\nCorrelation ID: a8125194-2dc8-4078-90ba-7b6592a7f231\r\nTimestamp: 2016-04-11 18:00:12Z",
  "error_codes": [
    70002,
    70008
  ],
  "timestamp": "2016-04-11 18:00:12Z",
  "trace_id": "3939d04c-d7ba-42bf-9cb7-1e5854cdce9e",
  "correlation_id": "a8125194-2dc8-4078-90ba-7b6592a7f231"
}
```
| Paramètre | Description |
| ----------------------- | ------------------------------- |
| erreur | Une chaîne de code d’erreur qui peut être utilisée pour classer les types d’erreurs qui se produisent et peut être utilisée pour réagir aux erreurs. |
| error_description | Un message d’erreur spécifique qui peut vous aider à un développeur identifier la cause d’une erreur d’authentification.  |
| error_codes | Liste des codes d’erreur spécifiques STS qui peuvent vous aider dans diagnostics. |
| horodatage | Le moment auquel l’erreur s’est produite. |
| argument trace_id | Un identificateur unique pour la requête peut vous aider à diagnostics.  |
| correlation_id | Un identificateur unique pour la requête peut vous aider à diagnostics par les différents composants.|

#### <a name="http-status-codes"></a>Codes d’état HTTP

Le tableau suivant répertorie les codes d’état HTTP qui retourne le point de terminaison d’émission du jeton. Dans certains cas, le code d’erreur est suffisant pour décrire la réponse, mais en cas d’erreurs, vous devez analyser le document d’accompagnement JSON et examiner son code d’erreur.

| Code HTTP | Description |
|-----------|-------------|
| 400       | Code HTTP par défaut. Utilisée dans la plupart des cas et est généralement en raison d’une demande incorrecte. Résoudre et renvoyer la demande. |
| 401       | Échoué de l’authentification. Par exemple, le paramètre client_secret est manquante dans la requête.|
| 403       | Échoué de l’autorisation. Par exemple, l’utilisateur ne dispose pas d’autorisation pour accéder à la ressource. |
| 500       | Une erreur interne s’est produite au niveau du service. Relancer la demande. |

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

## <a name="use-the-access-token-to-access-the-resource"></a>Utiliser le jeton d’accès pour accéder à la ressource

À présent que vous avez obtenu une `access_token`, vous pouvez utiliser le jeton dans les demandes au Web API, en incluant dans la `Authorization` en-tête. La spécification [RFC 6750](http://www.rfc-editor.org/rfc/rfc6750.txt) explique comment utiliser des jetons porteur dans les requêtes HTTP pour accéder aux ressources protégées.

### <a name="sample-request"></a>Exemple de requête

```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### <a name="error-response"></a>Réponse d’erreur

Ressources sécurisées qu’implémenter codes d’état HTTP RFC 6750 problème. Si la demande n’inclut pas les informations d’authentification ou manque le jeton, la réponse inclut un `WWW-Authenticate` en-tête. Lorsqu’une requête échoue, le serveur de ressources répond avec le code d’état HTTP et un code d’erreur.

Voici un exemple d’une réponse échoue lors de la requête du client n’inclut pas le jeton PORTEUR :

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.window.net/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>Paramètres d’erreur

| Paramètre | Description |
|-----------|-------------|
| authorization_uri | URI (point de terminaison physique) du serveur d’autorisation. Cette valeur est également utilisée comme clé de recherche pour obtenir plus d’informations sur le serveur à partir d’un point de terminaison de découverte. <p><p> Le client doit vérifier que le serveur d’autorisation est approuvé. Si la ressource est protégée par Azure Active Directory, il suffit de vérifier que l’URL commence par https://login.windows.net ou un autre nom d’hôte qui prend en charge Azure AD. Une ressource client spécifique doit toujours renvoyer une autorisation spécifiques au client URI. |
| erreur | Une valeur de code d’erreur définie dans la Section 5,2. de [Structure de OAuth 2.0 d’autorisation](http://tools.ietf.org/html/rfc6749).|
| error_description | Une description plus détaillée de l’erreur. Ce message n’est pas destiné à être convivial de l’utilisateur final.|
| id_ressource | Renvoie l’identificateur unique de la ressource. L’application cliente peut utiliser cet identificateur en tant que la valeur de la `resource` paramètre lorsqu’il demande un jeton de la ressource. <p><p> Il est très important pour l’application cliente vérifier cette valeur, dans le cas contraire un service malveillant peut-être être en mesure d’entraîner une attaque **élévation de privilèges** <p><p> Il est recommandé pour empêcher une attaque pour vous assurer que les `resource_id` correspond à la base de l’URL de l’API web utilisés. Par exemple, si https://service.contoso.com/data est effectué, la `resource_id` peut être htttps://service.contoso.com/. L’application cliente doit rejeter un `resource_id` qui ne commence pas par l’URL de base sauf s’il existe une autre façon fiable pour vérifier le code. |

#### <a name="bearer-scheme-error-codes"></a>Codes d’erreur jeu PORTEUR

La spécification RFC 6750 définit les erreurs suivantes pour les ressources à l’aide de l’en-tête WWW- et le jeu de porteur dans la réponse.

| Code d’état HTTP | Code d’erreur | Description | Action du client |
|------------------|------------|-------------|---------------|
| 400 | invalid_request | La requête n’est pas correcte. Par exemple, il est peut-être manquant un paramètre ou à l’aide de la même paramètre à deux reprises. | Corriger l’erreur puis relancez la requête. Ce type d’erreur doit se produire uniquement pendant le développement et détecté dans test initial. |
| 401 | invalid_token   | Le jeton d’accès est manquant, non valides ou est révoqué. La valeur du paramètre error_description fournit des détails supplémentaires. |  Demander un nouveau jeton à partir du serveur d’autorisation. Si le nouveau jeton échoue, une erreur inattendue s’est produite. Envoyer un message d’erreur à l’utilisateur et réessayer après retards aléatoires. |
| 403 | insufficient_scope | Le jeton d’accès ne contient pas les autorisations d’emprunt d’identité nécessaires pour accéder à la ressource. | Envoyer une nouvelle demande d’autorisation au point de terminaison d’autorisation. Si la réponse contient le paramètre scope, utilisez la valeur de l’étendue dans la demande pour la ressource. |
| 403 | insufficient_access | L’objet du jeton n’a pas les autorisations requises pour accéder à la ressource. | Demander à l’utilisateur pour utiliser un autre compte ou pour demander des autorisations pour la ressource spécifiée. |

## <a name="refreshing-the-access-tokens"></a>Actualiser les jetons d’accès

Les jetons d’accès sont courtes et doivent être actualisées après que qu’ils expirent pour continuer à accéder à des ressources. Vous pouvez actualiser le `access_token` en envoyant un autre `POST` demander à le `/token` point de terminaison, mais cette fois fournissant les `refresh_token` au lieu de le `code`.

Actualiser les jetons n’ont pas de durée de vie spécifiée. En règle générale, la durée de vie actualisation jetons est relativement longues. Toutefois, dans certains cas, actualiser jetons arriver à expiration, sont supprimées ou ne disposent pas de suffisamment de privilèges pour l’action souhaitée. Votre application doit s’attendre et gérer les erreurs renvoyées par le point de terminaison d’émission du jeton correctement.

Lorsque vous recevez une réponse avec une erreur jeton d’actualisation, ignorer le jeton d’actualisation en cours et demander un nouveau code d’autorisation ou jeton d’accès. En particulier, lorsque à l’aide d’une actualisation jeton dans le flux accorder l’autorisation Code, si vous recevez une réponse avec la `interaction_required` ou `invalid_grant` codes d’erreur, ignorer le jeton actualiser et demander un nouveau code d’autorisation.

Un échantillon demander au point de terminaison **spécifiques au client** (vous pouvez également utiliser le point de terminaison **courantes** ) pour obtenir une nouvelle access jeton en utilisant un jeton d’actualisation ressemble à ceci :

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```
| Paramètre | Description |
|-----------|-------------|
| access_token | Le nouveau jeton d’accès qui a été demandé.|
| expires_in   | La restante Durée de vie du jeton de secondes. Une valeur par défaut est 3600 (une heure). |
| expires_on   | Date et heure à laquelle le jeton arrive à expiration. La date est représentée par le nombre de secondes à partir du 1970-01-01T0:0:0Z UTC jusqu'à ce que le délai d’expiration. |
| refresh_token | Un nouveau refresh_token OAuth 2.0 pouvant être utilisées pour demander des nouveaux jetons d’accès à la fin de l’option dans cette réponse. |
| ressource     | Identifie les ressources sécurisées le jeton d’accès peut être utilisé pour accéder à. |
| étendue        | Autorisations d’emprunt d’identité accordées à l’application cliente native. L’autorisation par défaut est **user_impersonation**. Le propriétaire de la ressource cible peut inscrire autres valeurs dans Azure Active Directory. |
| token_type   | Le type de jeton. La seule valeur pris en charge est **PORTEUR**. |

### <a name="successful-response"></a>Réponse correcte

Une réponse jeton correcte se présente comme :

```
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1460404526",
  "resource": "https://service.contoso.com/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "refresh_token": "AwABAAAAv YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl PM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfmVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA"
}
```

### <a name="error-response"></a>Réponse d’erreur

Un exemple de réponse d’erreur peut ressembler à ceci :

```
{
  "error": "invalid_resource",
  "error_description": "AADSTS50001: The application named https://foo.microsoft.com/mail.read was not found in the tenant named 295e01fc-0c56-4ac3-ac57-5d0ed568f872.  This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant.  You might have sent your authentication request to the wrong tenant.\r\nTrace ID: ef1f89f6-a14f-49de-9868-61bd4072f0a9\r\nCorrelation ID: b6908274-2c58-4e91-aea9-1f6b9c99347c\r\nTimestamp: 2016-04-11 18:59:01Z",
  "error_codes": [
    50001
  ],
  "timestamp": "2016-04-11 18:59:01Z",
  "trace_id": "ef1f89f6-a14f-49de-9868-61bd4072f0a9",
  "correlation_id": "b6908274-2c58-4e91-aea9-1f6b9c99347c"
}
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| erreur | Une chaîne de code d’erreur qui peut être utilisée pour classer les types d’erreurs qui se produisent et peut être utilisée pour réagir aux erreurs. |
| error_description | Un message d’erreur spécifique qui peut vous aider à un développeur identifier la cause d’une erreur d’authentification.  |
| error_codes | Liste des codes d’erreur spécifiques STS qui peuvent vous aider dans diagnostics. |
| horodatage | Le moment auquel l’erreur s’est produite. |
| argument trace_id | Un identificateur unique pour la requête peut vous aider à diagnostics.  |
| correlation_id | Un identificateur unique pour la requête peut vous aider à diagnostics par les différents composants.|

Pour obtenir une description des codes d’erreur et l’action client recommandé, voir [codes d’erreur pour les erreurs de point de terminaison jeton](#error-codes-for-token-endpoint-errors).
