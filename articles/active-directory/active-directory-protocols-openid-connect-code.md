<properties
    pageTitle="Vue d’ensemble du protocole Azure AD .NET | Microsoft Azure"
    description="Cet article décrit comment utiliser les messages HTTP pour autoriser l’accès aux applications web et des API web de votre client à l’aide de Azure Active Directory et OpenID se connecter."
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


# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>Autoriser l’accès aux applications web à l’aide de OpenID se connecter et Azure Active Directory

[Se connecter OpenID](http://openid.net/specs/openid-connect-core-1_0.html) est un calque identité simple basé sur le protocole OAuth 2.0. OAuth 2.0 définit mécanismes permettant d’obtenir et d’utiliser des **jetons d’accès** pour accéder aux ressources protégées, mais ils ne définissent pas de méthodes standards pour fournir des informations d’identité. OpenID connecter met en œuvre authentification comme une extension pour le processus d’autorisation OAuth 2.0, fournissant des informations sur l’utilisateur final sous la forme d’un `id_token` qui vérifie l’identité de l’utilisateur, ainsi que fournit des informations de profil de base sur l’utilisateur.

Se connecter OpenID nous recommandons si vous créez une application web qui est hébergée sur un serveur et accessible via un navigateur.

## <a name="authentication-flow-using-openid-connect"></a>Flux d’authentification à l’aide de OpenID se connecter

Le flux de connexion plus simple contient les étapes suivantes : chacun d’eux est décrite en détail ci-dessous.

![OpenId connecter flux d’authentification](media/active-directory-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)


## <a name="send-the-sign-in-request"></a>Envoyer la demande de connexion

Lorsque votre application web doit s’authentifier à l’utilisateur, il doit diriger l’utilisateur vers le `/authorize` point de terminaison. Cette requête est similaire à la première branche de du [Flux de Code OAuth 2.0 d’autorisation](active-directory-protocols-oauth-code.md), avec quelques différences importantes :

- La requête doit inclure dans l’étendue `openid` dans les `scope` paramètre.
- La `response_type` paramètre doit inclure `id_token`.
- La requête doit inclure le `nonce` paramètre.

Pour une demande d’exemple présente comme suit :

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=7362CAEA-9CA5-4B43-9BA3-34D7C303EBA7
```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | --------------- |
| client | Obligatoire | La `{tenant}` valeur dans le chemin d’accès de la requête peut être utilisée pour contrôler qui peut vous connecter à l’application.  Les valeurs autorisées sont les identificateurs de client, par exemple, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` ou `common` pour les jetons indépendante du client |
| identifiant_client | Obligatoire | L’Id de l’Application affectée à votre application lors de l’enregistrement avec Azure AD. Vous pouvez le trouver dans le portail classique Azure. Cliquez sur dans **Active Directory**, cliquez sur le répertoire, cliquez sur l’application et puis cliquez sur **configurer** |
| response_type | Obligatoire | Doit inclure `id_token` connexion OpenID se connecter.  Il peut également inclure d’autres response_types, tel que `code`. |
| étendue | Obligatoire | Liste des étendues séparés par des espaces.  OpenID de connexion, elle doit inclure l’étendue `openid`, ce qui correspond à l’autorisation « Pouvons pas vous connecter » dans l’interface utilisateur de consentement.  Vous pouvez également inclure des autres étendues dans cette requête pour demander une autorisation. |
| valeur à usage unique | Obligatoire | Une valeur incluse dans la demande, générée par l’application, qui sera incluse dans résultant `id_token` comme une demande de remboursement.  L’application peut vérifier ensuite cette valeur pour atténuer les attaques de relecture jeton.  La valeur est généralement une chaîne aléatoire et unique ou GUID qui peut être utilisé pour identifier l’origine de la demande.  |
| redirect_uri | recommandé | Redirect_uri de votre application, où les réponses d’authentification peuvent être envoyés et reçus par votre application.  Il doit correspondre exactement à un de la redirect_uris que vous avez enregistré dans le portail, mais il doit être encoder au format url. |
| response_mode | recommandé | Spécifie la méthode qui doit être utilisée pour renvoyer l’authorization_code qui en résulte à votre application.  Valeurs prises en charge sont `form_post` pour la *publication de formulaire HTTP* ou `fragment` pour *fragment d’URL*.  Pour les applications web nous vous recommandons d’utiliser `response_mode=form_post` pour garantir le transfert plus sécurisé des jetons à votre application.  
| état | recommandé | Une valeur incluse dans la requête qui est également retournée dans la réponse jeton.  Il peut être une chaîne de n’importe quel contenu que vous souhaitez.  Une valeur unique générée de manière aléatoire est généralement utilisée pour [protéger contre les attaques de falsification intersites demande](http://tools.ietf.org/html/rfc6749#section-10.12).  L’état est également utilisé pour coder les informations sur l’état de l’utilisateur dans l’application avant la demande d’authentification s’est produite, telles que la page ou la vue qu’ils ont été sur. |
| invite de commandes | facultatif | Indique le type d’interaction de l’utilisateur qui est requis.  Les valeurs valides uniquement pour le moment sont « login », « aucun » et « acceptez ».  `prompt=login`force l’utilisateur à entrer ses informations d’identification sur cette demande, une inversion connexion unique.  `prompt=none`est l’inverse - elle veille à ce que l’utilisateur n’est pas présenté avec n’importe quelle invite interactive que ce soit.  Si la demande ne peut pas être effectuée en mode silencieux via l’authentification unique sur, le point de terminaison renverra une erreur.  `prompt=consent`déclenche la boîte de dialogue consentement OAuth une fois que l’utilisateur se connecte, demandant à l’utilisateur pour accorder des autorisations à l’application. |
| login_hint | facultatif | Peut servir à remplir au préalable le champ adresse de messagerie/nom d’utilisateur de la page pour l’utilisateur, se connecter si vous connaissez son nom d’utilisateur à l’avance.  Applications souvent utilisent ce paramètre au cours de ré-authentification, après avoir déjà extrait le nom d’utilisateur à partir d’un précédent se connecter à l’aide de la `preferred_username` réclamer. |


À ce stade, l’utilisateur est invité à entrer ses informations d’identification et terminer l’authentification.

### <a name="sample-response"></a>Exemple de réponse

Un exemple de réponse, une fois que l’utilisateur a authentifié, peut ressembler à ceci :

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| id_token | La `id_token` qui a demandé l’application. Vous pouvez utiliser la `id_token` pour vérifier l’identité de l’utilisateur et commencer une session avec l’utilisateur.  |
| état | Une valeur incluse dans la requête qui est également retournée dans la réponse jeton. Une valeur unique générée de manière aléatoire est généralement utilisée pour [protéger contre les attaques de falsification intersites demande](http://tools.ietf.org/html/rfc6749#section-10.12).  L’état est également utilisé pour coder les informations sur l’état de l’utilisateur dans l’application avant la demande d’authentification s’est produite, telles que la page ou la vue qu’ils ont été sur. |

### <a name="error-response"></a>Réponse d’erreur
Réponses d’erreur peuvent également être envoyées à la `redirect_uri` afin que l’application peut gérer les correctement :

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
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

## <a name="validate-the-idtoken"></a>Valider l’id_token

Recevoir uniquement un `id_token` n’est pas suffisant pour l’authentification de l’utilisateur ; Vous devez valider la signature et vérifier les revendications dans le `id_token` par la configuration requise de votre application. Le point de terminaison Azure AD utilise JSON Web jetons (JWTs) et chiffrement de clé publique pour signer jetons et vérifier qu’ils sont valides.

Vous pouvez choisir de valider le `id_token` dans le client code, mais une pratique courante consiste à envoyer le `id_token` à un serveur principal et effectuer la validation il. Une fois que vous avez validé la signature de la `id_token`, il existe quelques revendications vous devrez vérifier.

Vous souhaiterez peut-être également valider revendications supplémentaires en fonction de votre scénario. Certains contrôles courants sont les suivants :

- Garantir l’utilisateur/organisation s’est inscrite pour l’application.
- Garantir l’utilisateur a l’autorisation/privilèges NOMPROPRE
- Assurer une certaine force d’authentification s’est produite, telles que l’authentification multifacteur.

Une fois que vous avez complètement validé la `id_token`, vous pouvez commencer une session avec l’utilisateur et utiliser les revendications dans le `id_token` pour obtenir des informations sur l’utilisateur dans votre application. Ces informations peuvent être utilisées pour affichage, les enregistrements, les autorisations, etc.. Pour plus d’informations sur les revendications et les types de jetons, veuillez lire [jeton pris en charge et les Types de revendications](active-directory-token-and-claims.md).

## <a name="send-a-sign-out-request"></a>Envoyer une demande de déconnexion

Lorsque vous souhaitez vous connecter à l’utilisateur se déconnecter de l’application, il n’est pas suffisant effacer les cookies ou fin dans le cas contraire de votre application la session avec l’utilisateur.  Vous devez également rediriger l’utilisateur de la `end_session_endpoint` pour se déconnecter.  Si vous ne parvenez pas à le faire, l’utilisateur sera en mesure de s’authentifier à nouveau dans votre application sans entrer ses informations d’identification à nouveau, car ils auront une seule authentification session valide avec le point de terminaison Azure AD.

Vous pouvez rediriger simplement l’utilisateur de la `end_session_endpoint` répertoriées dans le document de métadonnées OpenID connecter :

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | recommandé | L’URL qui l’utilisateur doit être redirigé vers après la déconnexion réussie.  Si ne pas inclus, l’utilisateur s’affichera un message générique.  |

## <a name="token-acquisition"></a>Acquisition jeton

Beaucoup d’applications web devez non seulement me l’utilisateur dans, mais également accéder à un service web au nom d’utilisateur à l’aide de OAuth. Ce scénario combine OpenID se connecter pour l’authentification des utilisateurs lors de l’acquisition simultanément un `authorization_code` qui peut être utilisée pour obtenir `access_tokens` à l’aide du flux de Code OAuth d’autorisation.

## <a name="get-access-tokens"></a>Obtenir les jetons d’accès

Pour acquérir les jetons d’accès, vous devez modifier la demande de connexion à partir du haut :

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e      // Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered Redirect Uri, url encoded
&response_mode=form_post                              // form_post', or 'fragment'
&scope=openid
&resource=https%3A%2F%2Fservice.contoso.com%2F                                   
&state=12345                                         // Any value, provided by your app
&nonce=678910                                        // Any value, provided by your app
```

En incluant des portées d’autorisation dans la demande et à l’aide de `response_type=code+id_token`, la `authorize` point de terminaison permet de garantir que l’utilisateur a accepté pour les autorisations indiquées dans le `scope` paramètre de requête et retourner à votre application un code d’autorisation à exchange pour un jeton d’accès.

### <a name="successful-response"></a>Réponse correcte

Une réponse correcte à l’aide `response_mode=form_post` ressemble à :

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| id_token | La `id_token` qui a demandé l’application. Vous pouvez utiliser la `id_token` pour vérifier l’identité de l’utilisateur et commencer une session avec l’utilisateur. |
| code | Authorization_code qui a demandé l’application. L’application peut utiliser le code d’autorisation pour demander un jeton d’accès pour la ressource cible. Authorization_codes sont très courte durée de vie, en général ils expirent après environ 10 minutes. |
| état | Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état dans la demande et réponse sont identiques. |

### <a name="error-response"></a>Réponse d’erreur

Réponses d’erreur peuvent également être envoyées à la `redirect_uri` afin que l’application peut gérer les correctement :

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| erreur | Une chaîne de code d’erreur qui peut être utilisée pour classer les types d’erreurs qui se produisent et peut être utilisée pour réagir aux erreurs. |
| error_description | Un message d’erreur spécifique qui peut vous aider à un développeur identifier la cause d’une erreur d’authentification.  |

Pour une description des codes d’erreur possibles et leur action client recommandé, voir [codes d’erreur pour les erreurs de point de terminaison d’autorisation](#error-codes-for-authorization-endpoint-errors).

Une fois que vous avez obtenu une autorisation `code` et un `id_token`, vous pouvez connecter l’utilisateur et obtenir des jetons d’accès en leur nom.  Pour vous connecter l’utilisateur dans, vous devez valider le `id_token` exactement comme décrit ci-dessus. Pour obtenir les jetons d’accès, vous pouvez suivre les étapes décrites dans notre [documentation de protocole OAuth](active-directory-protocols-oauth-code.md#Use-the-Authorization-Code-to-Request-an-Access-Token).
