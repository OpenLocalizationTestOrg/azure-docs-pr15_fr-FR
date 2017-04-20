<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Création d’applications web à l’aide de l’implémentation Azure Active Directory du protocole d’authentification OpenID se connecter."
    services="active-directory-b2c"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-active-directory-b2c-oauth-20-authorization-code-flow"></a>Azure B2C annuaire Active : Flux de code 2.0 OAuth d’autorisation

L’octroi 2.0 OAuth d’autorisation code peut être utilisé dans les applications installées sur un appareil d’accéder à des ressources protégées, telles que web API. À l’aide de l’implémentation B2C Azure Active Directory (AD Azure) de OAuth 2.0, vous pouvez ajouter des tâches de gestion des identités d’abonnement, connexion et d’autres à vos applications de bureau et mobiles. Ce guide est indépendante du langage. Il décrit comment envoyer et recevoir des messages HTTP sans utiliser l’un de nos bibliothèques open source.

<!-- TODO: Need link to libraries -->

Le flux de code 2.0 OAuth d’autorisation est décrite dans la [section 4.1 de la spécification 2.0 OAuth](http://tools.ietf.org/html/rfc6749). Vous pouvez l’utiliser pour effectuer l’authentification et l’autorisation dans la plupart des types d’application, y compris les [applications web](active-directory-b2c-apps.md#web-apps) et [en mode natif installé les applications](active-directory-b2c-apps.md#mobile-and-native-apps). Il permet aux applications en toute sécurité acquérir **access_tokens**, qui peut être utilisé pour accéder aux ressources qui sont protégés par un [serveur d’autorisation](active-directory-b2c-reference-protocols.md#the-basics).

Ce guide se concentre sur une version particulière des 2.0 OAuth d’autorisation code flux--**clients publics**. Un client public est toute application cliente qui ne peuvent pas être habilitée à en toute sécurité préserver l’intégrité du mot de passe secret. Cela inclut les applications mobiles, applications de bureau et pratiquement n’importe quelle application qui s’exécute sur un appareil et a besoin d’obtenir access_tokens. Si vous voulez ajouter la gestion des identités pour une application web à l’aide de Azure AD B2C, vous devez utiliser [OpenID connecter](active-directory-b2c-reference-oidc.md) plutôt que OAuth 2.0.

Azure AD B2C étend la norme Qu'oauth 2.0 flux pour faire plus que d’autorisation et d’authentification simple. Il présente le [**paramètre de stratégie**](active-directory-b2c-reference-policies.md), qui vous permet d’utiliser OAuth 2.0 pour ajouter des expériences utilisateur dans votre application, tel que d’abonnement, connexion et gestion des profils. Ici, nous allons montrer comment utiliser OAuth 2.0 et les stratégies pour chacun de ces expériences implémenter dans vos applications natives. Nous allons également montrent comment obtenir access_tokens pour accéder au web API.

Les demandes HTTP exemple ci-dessous utilise notre exemple de répertoire B2C, **fabrikamb2c.onmicrosoft.com**, ainsi que notre exemple d’application et les stratégies. Vous pouvez essayer les demandes vous-même en utilisant les valeurs suivantes, ou vous pouvez les remplacer par votre propre.
Découvrez comment [obtenir votre propre répertoire B2C, application et les stratégies](#use-your-own-b2c-directory).

## <a name="1-get-an-authorization-code"></a>1. obtenez un code d’autorisation
Le flux du code d’autorisation commence par le client dirigeant l’utilisateur de la `/authorize` point de terminaison. Il s’agit de la partie interactive du flux, où l’utilisateur doit avoir réellement action. Dans cette demande, le client indique les autorisations dont il a besoin pour obtenir de l’utilisateur dans le `scope` paramètre et la stratégie d’exécution dans le `p` paramètre. Trois exemples sont fournis ci-dessous (avec les sauts de ligne pour une meilleure lisibilité), chacun à l’aide d’une stratégie différente.

#### <a name="use-a-sign-in-policy"></a>Utiliser une stratégie de connexion

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-policy"></a>Utiliser une stratégie d’abonnement

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-policy"></a>Utiliser une stratégie de modifier le profil

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_edit_profile
```

| Paramètre | Obligatoire ? | Description |
| ----------------------- | ------------------------------- | ----------------------- |
| identifiant_client | Obligatoire | ID de l’application du [portail Azure](https://portal.azure.com) affecté à votre application. |
| response_type | Obligatoire | Le type de réponse, qui doit inclure `code` pour le flux de code d’autorisation. |
| redirect_uri | Obligatoire | Redirect_uri de votre application, où les réponses d’authentification peuvent être envoyés et reçus par votre application. Il doit correspondre exactement à un des redirect_uris que vous avez enregistré dans le portail, excepté qu’elle doit être encoder au format URL. |
| étendue | Obligatoire | Liste des étendues séparés par des espaces. Une valeur unique étendue indique à Azure AD à la fois les autorisations qui sont demandés. À l’aide de l’ID de client comme l’étendue indique que votre application a besoin d’un **jeton d’accès** qui peut être utilisé avec votre propre service ou API web, représentée par le même ID client.  La `offline_access` étendue indique que votre application devront un **refresh_token** pour accéder à long terme aux ressources.  Vous pouvez également utiliser la `openid` étendue pour demander un **id_token** à partir d’Azure AD B2C. |
| response_mode | Recommandé | La méthode qui doit être utilisée pour renvoyer l’authorization_code qui en résulte à votre application. Il peut s’agir de 'query', 'form_post' ou 'fragment'.
| état | Recommandé | Une valeur incluse dans la requête qui est également retournée dans la réponse jeton. Il peut être une chaîne de n’importe quel contenu souhaité. Une valeur unique générée de manière aléatoire est généralement utilisée pour protéger contre les attaques de falsification intersites demande. L’état est également utilisé pour coder les informations sur l’état de l’utilisateur dans l’application avant la demande d’authentification s’est produite, par exemple la page qu’ils ont été sur ou la stratégie en cours d’exécution. |
| p | Obligatoire | La stratégie qui sera exécutée. Il s’agit du nom d’une stratégie qui est créé dans votre annuaire B2C. La valeur de nom de stratégie doit commencer par « b2c\_1\_». En savoir plus sur les stratégies dans le [cadre de politique Extensible](active-directory-b2c-reference-policies.md). |
| invite de commandes | Facultatif | Le type d’interaction de l’utilisateur qui est requis. La seule valeur valide pour l’instant est 'connexion', afin d’obliger l’utilisateur à entrer ses informations d’identification sur cette requête. Authentification unique ne prend pas effet. |

À ce stade, l’utilisateur est invité à exécuter le flux de travail de la stratégie. Cela peut nécessiter l’utilisateur entrant leur nom d’utilisateur et mot de passe, pour vous connecter avec une identité de mise en réseau, qui s’inscrit à l’annuaire, ou un nombre quelconque de la procédure, selon la façon dont la stratégie est définie.

Une fois que l’utilisateur termine la stratégie, Azure AD renverra une réponse à votre application en l’indiquée `redirect_uri`, à l’aide de la méthode spécifiée dans le `response_mode` paramètre. La réponse sera exactement le même pour chacun de ces cas, indépendamment de la stratégie a été exécutée.

Une réponse correcte qui utilise `response_mode=query` ressemble à :

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| code | Authorization_code qui a demandé l’application. L’application peut utiliser le code d’autorisation pour demander une access_token pour une ressource cible. Authorization_codes sont très courte durée de vie. En règle générale, ils expirent après environ 10 minutes. |
| état | Consultez la description complète dans le tableau précédent. Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état dans la demande et la réponse sont identiques. |

Réponses d’erreur peuvent également être envoyées à la `redirect_uri` afin que l’application puisse les gérer correctement :

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| erreur | Une chaîne de code d’erreur qui peut être utilisée pour classer les types d’erreurs qui se produisent et peut être utilisée pour réagir aux erreurs. |
| error_description | Un message d’erreur spécifique qui puisse aider les développeurs à identifier la cause d’une erreur d’authentification. |
| état | Consultez la description complète de la première table dans cette section. Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état dans la demande et la réponse sont identiques. |


## <a name="2-get-a-token"></a>2. obtenir le jeton
À présent que vous avez acquis un authorization_code, vous pouvez récupérer la `code` pour un jeton avec la ressource souhaitée en envoyant un `POST` demander à la `/token` point de terminaison. Dans Azure AD B2C, la seule ressource dont vous pouvez demander un jeton pour est votre site web API de l’application serveur principal. La convention qui est utilisée pour demander un jeton à vous-même consiste à utiliser des ID de client de votre application comme portée :

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Paramètre | Obligatoire ? | Description |
| ----------------------- | ------------------------------- | --------------------- |
| p | Obligatoire | La stratégie qui a été utilisée pour trouver le code d’autorisation. Vous ne pouvez pas utiliser une stratégie différente dans cette demande. Notez que vous ajoutez ce paramètre à la *chaîne de requête*, pas dans le corps du billet. |
| identifiant_client | Obligatoire | ID de l’application du [portail Azure](https://portal.azure.com) affecté à votre application. |
| grant_type | Obligatoire | Le type de grant, qui doit être `authorization_code` pour le flux de code d’autorisation. |
| étendue | Recommandés | Liste des étendues séparés par des espaces. Une valeur unique étendue indique à Azure AD à la fois les autorisations qui sont demandés. À l’aide de l’ID de client comme l’étendue indique que votre application a besoin d’un **jeton d’accès** qui peut être utilisé avec vos propres web API ou service, représentée par le même ID client.  La `offline_access` étendue indique que votre application devront un **refresh_token** pour accéder à long terme aux ressources.  Vous pouvez également utiliser la `openid` étendue pour demander un **id_token** à partir d’Azure AD B2C. |
| code | Obligatoire | Authorization_code que vous avez acheté dans la première branche du flux. |
| redirect_uri | Obligatoire | Redirect_uri de l’application dans laquelle vous avez reçu l’authorization_code. |

Une réponse jeton correcte se présente comme :

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Paramètre | Description |
| ----------------------- | ------------------------------- |
| not_before | L’heure à laquelle le jeton est considéré comme valide, dans le temps d’origine. |
| token_type | La valeur type de jeton. Le seul type qui prend en charge Azure AD est porteur. |
| access_token | Signé jeton JSON Web jeton (JWT) que vous avez demandé. |
| étendue | Les étendues qui le jeton est valide, qui peuvent être utilisés pour la mise en cache des jetons pour une utilisation ultérieure. |
| expires_in | La durée pendant laquelle le jeton est valide (en secondes). |
| refresh_token | Un refresh_token OAuth 2.0. L’application peut utiliser ce jeton d’acquérir des jetons supplémentaires après l’expiration du jeton en cours. Refresh_tokens sont longue durée de vie et peut être utilisé pour conserver l’accès aux ressources pour une période prolongée. Pour plus d’informations, reportez-vous à la [référence de jeton B2C](active-directory-b2c-reference-tokens.md). |

Réponses d’erreur seront présente comme :

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| erreur | Une chaîne de code d’erreur qui peut être utilisée pour classer les types d’erreurs qui se produisent et peut être utilisée pour réagir aux erreurs. |
| error_description | Un message d’erreur spécifique qui puisse aider les développeurs à identifier la cause d’une erreur d’authentification. |

## <a name="3-use-the-token"></a>3. Utilisez le jeton
À présent que vous avez obtenu une `access_token`, vous pouvez utiliser le jeton dans les requêtes à votre version de serveur web API en incluant dans la `Authorization` en-tête :

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. le jeton de l’actualisation
Jetons un nom et un accès sont courte durée de vie. Vous devez actualiser les qui arrivent à expiration pour continuer à accéder à des ressources. Vous pouvez le faire en envoyant un autre `POST` demander à la `/token` point de terminaison. Cette fois fournir la `refresh_token` au lieu de la `code`:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Paramètre | Obligatoire ? | Description |
| ----------------------- | ------------------------------- | -------- |
| p | Obligatoire | La stratégie qui a été utilisée pour acquérir les refresh_token d’origine. Vous ne pouvez pas utiliser une stratégie différente dans cette demande. Notez que vous ajoutez ce paramètre à la *chaîne de requête*, pas dans le corps du billet. |
| identifiant_client | Recommandé | ID de l’application du [portail Azure](https://portal.azure.com) affecté à votre application. |
| grant_type | Obligatoire | Le type de grant, qui doit être `refresh_token` pour cette branche du flux du code d’autorisation. |
| étendue | Recommandé | Liste des étendues séparés par des espaces. Une valeur unique étendue indique à Azure AD à la fois les autorisations qui sont demandés. À l’aide de l’ID de client comme l’étendue indique que votre application a besoin d’un **jeton d’accès** qui peut être utilisé avec votre propre service ou API web, représentée par le même ID client.  La `offline_access` étendue indique que votre application devront un **refresh_token** pour accéder à long terme aux ressources.  Vous pouvez également utiliser la `openid` étendue pour demander un **id_token** à partir d’Azure AD B2C. |
| redirect_uri | Facultatif | Redirect_uri de l’application dans laquelle vous avez reçu l’authorization_code. |
| refresh_token | Obligatoire | Refresh_token d’origine que vous avez acheté dans la deuxième branche du flux. |

Une réponse jeton correcte se présente comme :

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Paramètre | Description |
| ----------------------- | ------------------------------- |
| not_before | L’heure à laquelle le jeton est considéré comme valide, dans le temps d’origine. |
| token_type | La valeur type de jeton. Le seul type qui prend en charge Azure AD est porteur. |
| access_token | Signé jeton JSON Web jeton (JWT) que vous avez demandé. |
| étendue | Les étendues qui le jeton est valide, qui peuvent être utilisés pour la mise en cache des jetons pour une utilisation ultérieure. |
| expires_in | La durée pendant laquelle le jeton est valide (en secondes). |
| refresh_token | Un refresh_token OAuth 2.0. L’application peut utiliser ce jeton d’acquérir des jetons supplémentaires après l’expiration du jeton en cours. Refresh_tokens sont longue durée de vie et peut être utilisé pour conserver l’accès aux ressources pour une période prolongée. Pour plus d’informations, reportez-vous à la [référence de jeton B2C](active-directory-b2c-reference-tokens.md). |

Réponses d’erreur seront présente comme :

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| erreur | Une chaîne de code d’erreur qui peut être utilisée pour classer les types d’erreurs qui se produisent et peut être utilisée pour réagir aux erreurs. |
| error_description | Un message d’erreur spécifique qui peut vous aider à un développeur identifier la cause d’une erreur d’authentification. |

## <a name="use-your-own-b2c-directory"></a>Utiliser votre propre répertoire B2C

Si vous voulez essayer ces demandes pour vous-même, vous devez tout d’abord effectuer ces trois étapes et puis remplacer les valeurs d’exemple ci-dessus par votre propre :

- [Créer un répertoire B2C](active-directory-b2c-get-started.md) et utilisez le nom de votre répertoire dans les requêtes.
- [Créer une application](active-directory-b2c-app-registration.md) pour obtenir un ID de l’application et un redirect_uri. Vous souhaiterez inclure un **native client** dans votre application.
- [Créer vos stratégies](active-directory-b2c-reference-policies.md) pour obtenir les noms de votre stratégie.
