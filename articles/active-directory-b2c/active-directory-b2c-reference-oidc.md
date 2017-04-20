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

# <a name="azure-active-directory-b2c-web-sign-in-with-openid-connect"></a>Azure B2C annuaire Active : Connexion Web avec OpenID Connect

Se connecter OpenID est un protocole, basé sur OAuth 2.0, qui peut être utilisé pour signer en toute sécurité les utilisateurs dans des applications web.  À l’aide de l’implémentation B2C Azure Active Directory (AD Azure) de OpenID se connecter, vous pouvez confier d’abonnement, connexion et des expériences d’autres tâches de gestion des identités dans vos applications web à Azure Active Directory. Ce guide vous montrent comment procéder d’une manière indépendante du langage. Il décrit comment envoyer et recevoir des messages HTTP sans utiliser l’un de nos bibliothèques open source.

[Se connecter OpenID](http://openid.net/specs/openid-connect-core-1_0.html) étend le protocole *autorisation* OAuth 2.0 pour une utilisation comme protocole *d’authentification* . Cela vous permet d’effectuer de l’authentification unique à l’aide de OAuth. Il présente le concept d’un `id_token`, c'est-à-dire un jeton de sécurité qui permet au client vérifier l’identité de l’utilisateur et obtenir des informations de profil de base de l’utilisateur.

Car elle étend OAuth 2.0, elle permet également d’applications en toute sécurité acquérir **access_tokens**. Vous pouvez utiliser access_tokens pour accéder aux ressources qui sont protégés par un [serveur d’autorisation](active-directory-b2c-reference-protocols.md#the-basics). Nous vous recommandons de vous connecter OpenID si vous créez une application web qui est hébergée sur un serveur et accessible via un navigateur. Si vous souhaitez ajouter la gestion des identités à vos applications mobiles ou de bureau à l’aide de Azure AD B2C, vous devez utiliser [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) plutôt qu’une connexion OpenID.

Azure AD B2C étend le protocole standard OpenID se connecter pour faire plus que d’autorisation et d’authentification simple. Il présente le [**paramètre de stratégie**](active-directory-b2c-reference-policies.md), qui vous permet d’utiliser OpenID se connecter pour ajouter des expériences utilisateur dans votre application, tel que d’abonnement, connexion et gestion des profils. Ici, nous allons vous montrer comment utiliser OpenID se connecter et les stratégies pour implémenter chacune de ces expériences dans vos applications web. Nous allons également vous montrer comment obtenir access_tokens pour accéder au web API.

Les demandes HTTP exemple ci-dessous utilise notre exemple de répertoire B2C, **fabrikamb2c.onmicrosoft.com**, ainsi que notre exemple application **https://aadb2cplayground.azurewebsites.net** et les stratégies. Vous pouvez essayer les demandes vous-même en utilisant les valeurs suivantes, ou remplacez-les par votre propre.
Découvrez comment [obtenir vos propres B2C client, application et les stratégies](#use-your-own-b2c-directory).

## <a name="send-authentication-requests"></a>Envoyer les demandes d’authentification
Lorsque votre application web doit s’authentifier l’utilisateur et exécuter une stratégie, il peut diriger l’utilisateur de la `/authorize` point de terminaison. Il s’agit de la partie interactive de flux, où l’utilisateur doit avoir réellement action, en fonction de la stratégie.

Dans cette demande, le client indique les autorisations dont il a besoin pour obtenir de l’utilisateur dans le `scope` paramètre et la stratégie d’exécution dans le `p` paramètre. Trois exemples sont fournis ci-dessous (avec les sauts de ligne pour une meilleure lisibilité), chacun à l’aide d’une stratégie différente. Pour avoir une idée du fonctionne de chaque demande, essayez de collage de la demande dans un navigateur et de l’exécuter.

#### <a name="use-a-sign-in-policy"></a>Utiliser une stratégie de connexion

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-policy"></a>Utiliser une stratégie d’abonnement

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-policy"></a>Utiliser une stratégie de modifier le profil

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Paramètre | Obligatoire ? | Description |
| ----------------------- | ------------------------------- | ----------------------- |
| identifiant_client | Obligatoire | ID de l’application du [portail Azure](https://portal.azure.com/) affecté à votre application. |
| response_type | Obligatoire | Le type de réponse, qui doit inclure `id_token` OpenID de connexion. Si votre application web devez également jetons pour appeler un API web, vous pouvez utiliser `code+id_token`, comme nous l’avons fait ici. |
| redirect_uri | Recommandé | Redirect_uri de votre application, où les réponses d’authentification peuvent être envoyés et reçus par votre application. Il doit correspondre exactement à un des redirect_uris que vous avez enregistré dans le portail, excepté qu’elle doit être encoder au format URL. |
| étendue | Obligatoire | Liste des étendues séparés par des espaces. Une valeur unique étendue indique à Azure AD à la fois les autorisations qui sont demandés. La `openid` étendue indique une autorisation connecter l’utilisateur et obtenir des données relatives à l’utilisateur dans le formulaire **id_tokens** (supplémentaire à venir sur ce problème plus loin dans l’article). La `offline_access` étendue est facultatif pour les applications web. Il indique que votre application devront un **refresh_token** pour accéder à long terme aux ressources. |
| response_mode | Recommandé | La méthode qui doit être utilisée pour renvoyer l’authorization_code qui en résulte à votre application. Il peut s’agir de 'query', 'form_post' ou 'fragment'.  'form_post' est recommandé pour une sécurité optimale. |
| état | Recommandé | Une valeur incluse dans la requête qui est également retournée dans la réponse jeton. Il peut être une chaîne de n’importe quel contenu souhaité. Une valeur unique générée de manière aléatoire est généralement utilisée pour protéger contre les attaques de falsification intersites demande. L’état est également utilisé pour coder les informations sur l’état de l’utilisateur dans l’application avant la demande d’authentification s’est produite, telles que la page qu’ils ont été sur. |
| valeur à usage unique | Obligatoire | Une valeur incluse dans la demande (générée par l’application) qui sera incluse dans l’id_token résultante comme une demande de remboursement. L’application peut vérifier ensuite cette valeur pour atténuer les attaques de relecture jeton. La valeur est généralement une chaîne aléatoire unique qui peut être utilisée pour identifier l’origine de la demande. |
| p | Obligatoire | La stratégie qui sera exécutée. Il s’agit du nom d’une stratégie qui est créé dans votre client B2C. La valeur de nom de stratégie doit commencer par « b2c\_1\_». En savoir plus sur les stratégies dans le [cadre de politique Extensible](active-directory-b2c-reference-policies.md). |
| invite de commandes | Facultatif | Le type d’interaction de l’utilisateur qui est requis. La seule valeur valide pour l’instant est 'connexion', afin d’obliger l’utilisateur à entrer ses informations d’identification sur cette requête. Authentification unique ne prend pas effet. |

À ce stade, l’utilisateur est invité à exécuter le flux de travail de la stratégie. Cela peut nécessiter l’utilisateur entrer leur nom d’utilisateur et mot de passe pour vous connecter avec une identité de mise en réseau, qui s’inscrit à l’annuaire, ou un nombre quelconque de la procédure, selon la façon dont la stratégie est définie.

Une fois que l’utilisateur termine la stratégie, Azure AD renverra une réponse à votre application en l’indiquée `redirect_uri`, en utilisant la méthode spécifié dans le `response_mode` paramètre. La réponse sera exactement le même pour chacun de ces cas, indépendamment de la stratégie a été exécutée.

Une réponse correcte à l’aide `response_mode=fragment` donnera :

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| id_token | Id_token qui a demandé l’application. Vous pouvez utiliser l’id_token pour vérifier l’identité de l’utilisateur et commencer une session avec l’utilisateur. Obtenir plus d’informations sur id_tokens et leur contenu sont inclus dans la [référence de jeton Azure AD B2C](active-directory-b2c-reference-tokens.md). |
| code | L’authorization_code l’application demandée, si vous avez utilisé `response_type=code+id_token`. L’application peut utiliser le code d’autorisation pour demander une access_token pour une ressource cible. Authorization_codes sont très courte durée de vie. En règle générale, ils expirent après environ 10 minutes. |
| état | Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état dans la demande et réponse sont identiques. |

Réponses d’erreur peuvent également être envoyés à la `redirect_uri` afin que l’application puisse les gérer correctement :

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| erreur | Une chaîne de code d’erreur qui peut être utilisée pour classer les types d’erreurs qui se produisent et peut être utilisée pour réagir aux erreurs. |
| error_description | Un message d’erreur spécifique qui peut vous aider à un développeur identifier la cause d’une erreur d’authentification. |
| état | Consultez la description complète dans le tableau précédent. Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état dans la demande et réponse sont identiques. |


## <a name="validate-the-idtoken"></a>Valider l’id_token
Uniquement recevoir un id_token n’est pas suffisant pour authentifier l’utilisateur, vous devez valider la signature d’id_token et vérifier les revendications dans le jeton conformément aux exigences de votre application. Azure AD B2C utilise [JSON Web jetons (JWTs)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) et chiffrement de clé publique pour signer jetons et vérifier qu’ils sont valides.

Il existe de nombreuses bibliothèques open source qui sont disponibles pour la validation JWTs, selon votre langue de préférence. Nous vous recommandons d’exploration ces options plutôt qu’implémenter votre propre logique de validation. Ces informations seront utiles pour savoir comment utiliser correctement ces bibliothèques.

Azure AD B2C contient une extrémité de métadonnées OpenID se connecter, qui permet de récupérer des informations sur Azure AD B2C lors de l’exécution d’une application. Ces informations comprennent les points de terminaison, le contenu de jetons et jeton de clés de signature. Il existe un document de métadonnées JSON pour chaque stratégie dans votre client B2C. Par exemple le document de métadonnées pour les `b2c_1_sign_in` stratégie dans la `fabrikamb2c.onmicrosoft.com` se trouve à :

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Une des propriétés de ce document de configuration est la `jwks_uri`, dont la valeur pour la même stratégie serait :

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

Dans afin de déterminer la stratégie a été utilisée lors de la signature d’un id_token (et where récupérer les métadonnées à partir de), vous avez deux possibilités. Tout d’abord, le nom de la stratégie est inclus dans le `acr` réclamer dans l’id_token. Pour plus d’informations sur la façon d’analyser les revendications à partir d’un id_token, consultez la [référence de jeton Azure AD B2C](active-directory-b2c-reference-tokens.md). L’autre option consiste à coder la stratégie dans la valeur de la `state` paramètre lorsque vous lancé de demande, puis le décoder pour déterminer quelle stratégie a été utilisée. Les deux méthodes sont parfaitement valide.

Une fois que vous avez acquis le document de métadonnées à partir du point de terminaison de métadonnées OpenID se connecter, vous pouvez utiliser les clés publiques RSA 256 (qui se trouvent dans ce point de terminaison) pour valider la signature de l’id_token. Il peut y avoir plusieurs touches répertoriés sur ce point de terminaison à un moment donné dans le temps, chacune identifiée par une `kid`. L’en-tête de l’id_token contient également un `kid` réclamer, qui indique qui de ces touches a été utilisé pour signer l’id_token. Voir la [référence de jeton Azure AD B2C](active-directory-b2c-reference-tokens.md) pour plus d’informations, y compris les sections sur la [validation des jetons](active-directory-b2c-reference-tokens.md#validating-tokens) et [des informations importantes sur substitution de la clé de signature](active-directory-b2c-reference-tokens.md#validating-tokens).
<!--TODO: Improve the information on this-->

Une fois que vous avez validé la signature de l’id_token, il existe plusieurs créances dont vous aurez besoin pour vérifier, par exemple :

- Vous devez valider le `nonce` revendiquer empêcher les attaques de relecture jeton. Sa valeur doit être que vous avez spécifié dans la demande de connexion.
- Vous devez valider le `aud` réclamer pour vous assurer que l’id_token a été émis pour votre application. Sa valeur doit être l’ID de l’application de votre application.
- Vous devez valider le `iat` et `exp` de revendications pour vous assurer que l’id_token n’a pas expiré.

Il existe également plusieurs validations plus que vous devez effectuer, décrites en détail dans la [Spécification de cœur OpenID se connecter](http://openid.net/specs/openid-connect-core-1_0.html).  Vous souhaiterez également valider les autres plaintes, en fonction de votre scénario. Certains contrôles courants sont les suivants :

- S’assurer que l’utilisateur/organisation s’est inscrite pour l’application.
- S’assurer que l’utilisateur possède des privilèges / d’autorisation appropriées.
- Veiller à ce qu’une certaine force d’authentification s’est produite, telles que l’authentification multifacteur Azure.

Pour plus d’informations sur les revendications dans un id_token, voir la [référence de jeton Azure AD B2C](active-directory-b2c-reference-tokens.md).

Après avoir validé complètement l’id_token, vous pouvez commencer une session avec l’utilisateur et utiliser les revendications dans l’id_token pour obtenir des informations sur l’utilisateur dans votre application. Ces informations peuvent être utilisées pour l’affichage, les enregistrements, d’autorisation et ainsi de suite.

## <a name="get-a-token"></a>Obtenir un jeton
Si vos besoins d’application web faire qu’exécuter les politiques, vous pouvez ignorer les sections suivantes. Les sections suivantes sont applique uniquement aux applications que vous doivent prendre authentifié appels vers un site web API et sont également protégées par Azure AD B2C web.

Vous pouvez récupérer l’authorization_code que vous avez acheté (à l’aide de `response_type=code+id_token`) pour un jeton avec la ressource souhaitée en envoyant un `POST` demander à la `/token` point de terminaison. Pour l’instant, la seule ressource dont vous pouvez demander un jeton pour est votre site web API de l’application serveur principal. La convention pour demander un jeton à vous-même consiste à utiliser des ID de client de votre application comme portée :

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>

```

| Paramètre | Obligatoire ? | Description |
| ----------------------- | ------------------------------- | --------------------- |
| p | Obligatoire | La stratégie qui a été utilisée pour trouver le code d’autorisation. Vous ne pouvez pas utiliser une stratégie différente dans cette demande. Notez que vous ajoutez ce paramètre à la **chaîne de requête**, pas dans le corps du billet. |
| identifiant_client | Obligatoire | ID de l’application du [portail Azure](https://portal.azure.com/) affecté à votre application. |
| grant_type | Obligatoire | Le type de grant, qui doit être `authorization_code` pour le flux de code d’autorisation. |
| étendue | Recommandé | Liste des étendues séparés par des espaces. Une valeur unique étendue indique à Azure AD à la fois les autorisations qui sont demandés. La `openid` étendue indique une autorisation connecter l’utilisateur et obtenir des données relatives à l’utilisateur dans le formulaire de **id_tokens**. Il peut être utilisée pour obtenir des jetons à votre site web API, ce qui est représenté par le même ID de l’application que le client de l’application serveur principal. La `offline_access` étendue indique que votre application devront un **refresh_token** pour accéder à long terme aux ressources. |
| code | Obligatoire | Authorization_code que vous avez acheté dans la première branche du flux. |
| redirect_uri | Obligatoire | Redirect_uri de l’application dans laquelle vous avez reçu l’authorization_code. |
| client_secret | Obligatoire | Le code secret application que vous avez généré dans le [portail Azure](https://portal.azure.com/). Ce mot de passe d’application est un objet de sécurité importants. Vous devez les enregistrer en toute sécurité sur votre serveur. Vous devez également veiller à faire pivoter cette secret client périodiquement. |

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
| access_token | Jeton JWT signé que vous avez demandé. |
| étendue | Les étendues qui le jeton est valide, qui peuvent être utilisés pour la mise en cache des jetons pour une utilisation ultérieure. |
| expires_in | La durée de l’access_token valide (en secondes). |
| refresh_token | Un refresh_token OAuth 2.0. L’application peut utiliser ce jeton d’acquérir des jetons supplémentaires après l’expiration du jeton en cours.  Refresh_tokens sont longue durée de vie et peut être utilisé pour conserver l’accès aux ressources pour une période prolongée. Pour plus d’informations, reportez-vous à la [référence de jeton B2C](active-directory-b2c-reference-tokens.md). Notez que vous devez avoir utilisé l’étendue `offline_access` dans l’autorisation et les demandes de jetons pour recevoir une refresh_token. |

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

## <a name="use-the-token"></a>Utiliser le jeton
À présent que vous avez obtenu une `access_token`, vous pouvez utiliser le jeton dans les requêtes à votre version de serveur web API en incluant dans la `Authorization` en-tête :

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Actualiser le jeton
Les id_tokens sont courte durée de vie. Vous devez actualiser les qui arrivent à expiration pour continuer à accéder à des ressources. Vous pouvez le faire en envoyant un autre `POST` demander à la `/token` point de terminaison. Cette fois, fournissent la `refresh_token` au lieu de la `code`:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Paramètre | Obligatoire | Description |
| ----------------------- | ------------------------------- | -------- |
| p | Obligatoire | La stratégie qui a été utilisée pour acquérir les refresh_token d’origine. Vous ne pouvez pas utiliser une stratégie différente dans cette demande. Notez que vous ajoutez ce paramètre à la **chaîne de requête**, pas dans le corps du billet. |
| identifiant_client | Obligatoire | ID de l’application du [portail Azure](https://portal.azure.com/) affecté à votre application. |
| grant_type | Obligatoire | Le type de grant, qui doit être `refresh_token` pour cette branche du flux du code d’autorisation. |
| étendue | Recommandé | Liste des étendues séparés par des espaces. Une valeur unique étendue indique à Azure AD à la fois les autorisations qui sont demandés. La `openid` étendue indique une autorisation connecter l’utilisateur et obtenir des données relatives à l’utilisateur dans le formulaire de **id_tokens**. Il peut être utilisée pour obtenir des jetons à votre site web API, ce qui est représenté par le même ID de l’application que le client de l’application serveur principal. La `offline_access` étendue indique que votre application devront un **refresh_token** pour accéder à long terme aux ressources. |
| redirect_uri | Recommandé | Redirect_uri de l’application dans laquelle vous avez reçu l’authorization_code. |
| refresh_token | Obligatoire | Refresh_token d’origine que vous avez acheté dans la deuxième branche du flux. Notez que vous devez avoir utilisé l’étendue `offline_access` dans l’autorisation et les demandes de jetons pour recevoir une refresh_token. |
| client_secret | Obligatoire | Le code secret application que vous avez généré dans le [portail Azure](https://portal.azure.com/). Ce mot de passe d’application est un objet de sécurité importants. Vous devez les enregistrer en toute sécurité sur votre serveur. Vous devez également veiller à faire pivoter cette secret client périodiquement. |

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
| access_token | Jeton JWT signé que vous avez demandé. |
| étendue | Les étendues qui le jeton est valide, qui peuvent être utilisés pour la mise en cache des jetons pour une utilisation ultérieure. |
| expires_in | La durée de l’access_token valide (en secondes). |
| refresh_token | Un refresh_token OAuth 2.0. L’application peut utiliser ce jeton d’acquérir des jetons supplémentaires après l’expiration du jeton en cours.  Refresh_tokens sont longue durée de vie et peut être utilisé pour conserver l’accès aux ressources pour une période prolongée. Pour plus d’informations, reportez-vous à la [référence de jeton B2C](active-directory-b2c-reference-tokens.md). |

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


## <a name="send-a-sign-out-request"></a>Envoyer une demande de déconnexion

Lorsque vous voulez vous connecter à l’utilisateur se déconnecter de l’application, il n’est pas suffisamment effacer des cookies ou fin dans le cas contraire de votre application la session avec l’utilisateur. Vous devez également rediriger l’utilisateur vers Azure AD pour se déconnecter. Si vous ne parvenez pas à le faire, l’utilisateur peut-être être en mesure d’authentifier dans votre application sans entrer ses informations d’identification à nouveau. Il s’agit car ils auront une seule authentification session valide avec Azure AD.

Vous pouvez rediriger simplement l’utilisateur de la `end_session_endpoint` qui figure dans le même document de métadonnées OpenID se connecter décrit précédemment dans la section « Valider l’id_token » :

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Paramètre | Obligatoire ? | Description |
| ----------------------- | ------------------------------- | ------------ |
| p | Obligatoire | La stratégie que vous souhaitez utiliser pour vous connecter à l’utilisateur se déconnecter de votre application. |
| post_logout_redirect_uri | Recommandé | L’URL que l’utilisateur doit être redirigé vers après réussie déconnexion. Si elle n’est pas inclus, l’utilisateur s’affichera un message générique par Azure AD B2C.  |

> [AZURE.NOTE]
    Tout en demandant à l’utilisateur le `end_session_endpoint` va supprimer certaines des seul authentification état de l’utilisateur avec Azure AD B2C, il ne signe pas l’utilisateur se déconnecter de session du fournisseur (IDP) social identité de l’utilisateur. Si l’utilisateur sélectionne la même IDP pendant une connexion suivante, ils seront être authentifiés, sans entrer ses informations d’identification. Si un utilisateur souhaite vous déconnecter de votre application B2C, il ne signifie pas nécessairement qu’ils souhaitent vous déconnecter de leur propre compte Facebook entièrement. Toutefois, dans le cas des comptes locaux, session de l’utilisateur sera terminée correctement.

## <a name="use-your-own-b2c-tenant"></a>Utiliser votre propre client B2C

Si vous voulez essayer ces demandes pour vous-même, vous devez tout d’abord effectuer ces trois étapes et puis remplacer les valeurs d’exemple ci-dessus par votre propre :

- [Créer un client B2C](active-directory-b2c-get-started.md), puis utilisez le nom de votre client dans les requêtes.
- [Créer une application](active-directory-b2c-app-registration.md) pour obtenir un ID de l’application et un redirect_uri. Vous souhaiterez inclure un **web application/web api** dans votre application et vous pouvez également créer un **code secret application**.
- [Créer vos stratégies](active-directory-b2c-reference-policies.md) pour obtenir les noms de votre stratégie.
