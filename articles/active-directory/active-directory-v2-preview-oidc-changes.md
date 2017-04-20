<properties
    pageTitle="Modifications apportées à l’extrémité de la version 2.0 Azure AD | Microsoft Azure"
    description="Description des modifications apportées aux protocoles application modèle version 2.0 version d’évaluation."
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
    ms.date="09/16/2016"
    ms.author="dastrock"/>

# <a name="important-updates-to-the-v20-authentication-protocols"></a>Mises à jour importantes pour les protocoles d’authentification version 2.0
Développeurs attention ! Sur les deux prochaines semaines, nous allons apporter quelques mises à jour à notre protocoles d’authentification version 2.0 qui peuvent entraîner l’incapacité Nouveautés pour toutes les applications que vous avez écrit pendant la période d’aperçu.  

## <a name="who-does-this-affect"></a>Qui cela affecte-t-il ?
Toutes les applications qui ont été écrites à utiliser la version 2.0 inverse point de terminaison de l’authentification,

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
```

Vous pouvez trouver plus d’informations sur le point de terminaison version 2.0 [ici](active-directory-appmodel-v2-overview.md).

Si vous avez créé une application à l’aide de point de terminaison de la version 2.0 en codage directement sur le protocole version 2.0, à l’aide de notre middlewares web OpenID se connecter ou OAuth, ou à l’aide d’autres bibliothèques de fête 3e pour effectuer une authentification, vous devez savoir pour tester vos projets et apporter des modifications si nécessaire.

## <a name="who-doesnt-this-affect"></a>Qui cela n’affecte ?
Toutes les applications qui ont été écrits sur le point de terminaison d’authentification de production Azure AD,

```
https://login.microsoftonline.com/common/oauth2/authorize
```

Ce protocole est et ne fait pas l’objet toutes les modifications.

En outre, si de votre application **uniquement** utilise notre bibliothèque ADAL pour effectuer une authentification, vous ne devez rien modifier.  Terme ADAL a protégé votre application à partir des modifications.  

## <a name="what-are-the-changes"></a>Quelles sont les modifications ?
### <a name="removing-the-x5t-value-from-jwt-headers"></a>Suppression de la valeur x5t à partir d’en-têtes JWT
Point de terminaison de la version 2.0 utilise les jetons JWT extensive, qui contiennent une section Paramètres d’en-tête de métadonnées pertinentes sur le jeton.  Si vous décodez l’en-tête d’un de nos JWTs en cours, vous recherche s’intitule :

```
{ 
    "type": "JWT",
    "alg": "RS256",
    "x5t": "MnC_VZcATfM5pOYiJHMba9goEKY",
    "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

Où les « x5t » « enfants » propriétés et identifient la clé publique qui doit être utilisée pour valider la signature du jeton, récupéré à partir du point de terminaison de métadonnées OpenID se connecter.

La modification que nous faisons ici consiste à supprimer la propriété « x5t ».  Vous pouvez continuer à utiliser les mêmes mécanismes pour valider les jetons, mais vous devez compter uniquement sur la propriété « enfants » pour récupérer la clé publique appropriée, comme indiqué dans le protocole OpenID se connecter. 

> [AZURE.IMPORTANT] **Votre travail : Vérifiez que votre application ne dépend pas l’existence de la valeur x5t.**

### <a name="removing-profileinfo"></a>Suppression de profile_info
Auparavant, le point de terminaison version 2.0 a été renvoyer un objet JSON en base 64 codé dans les réponses jetons appelés `profile_info`.  Lorsque vous demandez un jeton d’accès à partir de la version 2.0 en envoyant une demande pour :

```
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

La réponse doit ressembler à l’objet JSON suivant :
```
{ 
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https://outlook.office.com/mail.read",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "profile_info": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

La `profile_info` informations valeur contenue sur l’utilisateur connecté à l’application - leur nom complet, prénom, nom, adresse de messagerie, identificateur et ainsi de suite.  À l’origine, le `profile_info` a été utilisé pour la mise en cache jeton et afficher à des fins.

Nous sommes en train de supprimer le `profile_info` valeur – mais ne vous inquiétez pas, nous vous fournissons toujours cette information aux développeurs dans un emplacement légèrement différent.  Au lieu de `profile_info`, le point de terminaison version 2.0 retournera désormais un `id_token` dans chaque réponse jeton :

```
{ 
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https://outlook.office.com/mail.read",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

Vous pouvez décoder et analyser les id_token pour extraire les mêmes informations que vous avez reçu de profile_info.  L’id_token est un JSON Web jeton (JWT), avec un contenu comme spécifié par OpenID se connecter.  Le code pour ce faire doit donc être très similaire : il vous suffit d’extraire le deuxième prénom segment (le corps) de l’id_token et en base 64 décoder à accéder à l’objet JSON dans.

Sur les deux prochaines semaines, vous devez coder votre application pour récupérer les informations utilisateur à partir du `id_token` ou `profile_info`; Si elle est présente.  De cette manière lorsque la modification est effectuée, votre application peut gérer en toute transparence la transition à partir de `profile_info` à `id_token` sans interruption.

> [AZURE.IMPORTANT] **Votre travail : Vérifiez que votre application ne dépend pas l’existence de la `profile_info` valeur.**

### <a name="removing-idtokenexpiresin"></a>Suppression d’id_token_expires_in
Semblable à `profile_info`, nous avons également supprimer le `id_token_expires_in` paramètre à partir des réponses.  Auparavant, le point de terminaison version 2.0 retournerait une valeur pour `id_token_expires_in` ainsi que chaque réponse id_token, par exemple dans une réponse autoriser :

```
https://myapp.com?id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...&id_token_expires_in=3599...
```

Ou dans une réponse jeton :

```
{ 
    "token_type": "Bearer",
    "id_token_expires_in": 3599,
    "scope": "openid",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "profile_info": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

La `id_token_expires_in` valeur indique le nombre de secondes l’id_token serait de validité.  À présent, nous allons supprimer la `id_token_expires_in` valeur complètement.  À la place, vous pouvez utiliser la norme OpenID connecter `nbf` et `exp` de revendications pour examiner la validité d’une id_token.  Voir la [référence de jeton de version 2.0](active-directory-v2-tokens.md) pour plus d’informations sur ces revendications.

> [AZURE.IMPORTANT] **Votre travail : Vérifiez que votre application ne dépend pas l’existence de la `id_token_expires_in` valeur.**


### <a name="changing-the-claims-returned-by-scopeopenid"></a>Modifier les revendications renvoyées par scope = openid
Cette modification sera les plus importantes – en fait, elle peut affecter presque chaque application qui utilise le point de terminaison version 2.0.  De nombreuses applications envoient des demandes au point de terminaison de la version 2.0 à l’aide du `openid` étendue, comme :

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=...
&redirect_uri=...
&response_mode=form_post
&response_type=id_token
&scope=openid offline_access https://outlook.office.com/mail.read
```

Aujourd'hui, lorsque l’utilisateur consentement pour le `openid` étendue, votre application reçoit une mine d’informations sur l’utilisateur dans l’id_token qui en résulte.  Ces revendications peuvent inclure son nom, nom d’utilisateur par défaut, adresse de messagerie, ID de l’objet et autres éléments.

Dans cette mise à jour, nous allons modifier les informations que la `openid` étendue offre votre application accès, pour une meilleure comform avec la spécification OpenID se connecter.  La `openid` étendue uniquement votre application pour vous connecter à l’utilisateur dans et de recevoir un identificateur spécifique à l’application pour l’utilisateur dans le `sub` réclamer de l’id_token.  Les revendications dans un id_token avec uniquement le `openid` étendue accordée sera sans les informations d’identification personnelle.  Exemple id_token revendications sont les suivantes :

```
{ 
    "aud": "580e250c-8f26-49d0-bee8-1c078add1609",
    "iss": "https://login.microsoftonline.com/b9410318-09af-49c2-b0c3-653adc1f376e/v2.0 ",
    "iat": 1449520283,
    "nbf": 1449520283,
    "exp": 1449524183,
    "nonce": "12345",
    "sub": "MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q",
    "tid": "b9410318-09af-49c2-b0c3-653adc1f376e",
    "ver": "2.0",
}
```

Si vous souhaitez obtenir des informations d’identification personnelle (informations d’identification personnelle) sur l’utilisateur dans votre application, votre application devez demander des autorisations supplémentaires à partir de l’utilisateur.  Nous présentons prise en charge des deux nouvelles étendues à partir de la spécification OpenID connecter – la `email` et `profile` étendues – qui vous permettent de le faire.

- La `email` étendue est très simple : il permet d’accéder de votre application à l’adresse de messagerie principale de l’utilisateur via la `email` réclamer dans l’id_token.  Notez que la `email` réclamer pas seront toujours présent dans id_tokens – il sera incluse uniquement s’il est disponible dans le profil utilisateur.
- La `profile` étendue offre l’accès d’application à toutes les autres informations de base sur l’utilisateur, son nom, le nom d’utilisateur par défaut, ID de l’objet et ainsi de suite.

Cela vous permet de coder votre application de manière minimales divulgation : vous pouvez demander à l’utilisateur pour l’ensemble d’informations que votre application a besoin pour faire son travail.  Si vous voulez l’obtention de l’ensemble des informations sur l’utilisateur qui reçoit actuellement votre application, vous devez inclure toutes les trois étendues dans vos demandes d’autorisation :

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=...
&redirect_uri=...
&response_mode=form_post
&response_type=id_token
&scope=openid profile email offline_access https://outlook.office.com/mail.read
```

Votre application peut commencer à envoyer la `email` et `profile` étendues immédiatement et l’extrémité de la version 2.0 acceptera ces deux portées et commencer à la demande d’autorisations des utilisateurs que nécessaire.  Toutefois, la modification de l’interprétation de la `openid` étendue ne prend pas effet pendant quelques semaines.

> [AZURE.IMPORTANT] **Votre travail : ajouter la `profile` et `email` étendues si votre application nécessite les informations sur l’utilisateur.**  Notez que terme ADAL inclura ces deux autorisations dans les requêtes par défaut. 

### <a name="removing-the-issuer-trailing-slash"></a>Suppression de l’émetteur oblique.
Auparavant, la valeur de l’émetteur qui apparaît dans les jetons à partir de la version 2.0 a pris la forme

```
https://login.microsoftonline.com/{some-guid}/v2.0/
```

Où le guid a été le tenantId du client Azure AD qui a émis le jeton.  Grâce à ces modifications, la valeur d’émetteur devient

```
https://login.microsoftonline.com/{some-guid}/v2.0 
```

dans les deux jetons et dans le document de découverte OpenID se connecter.

> [AZURE.IMPORTANT] **Votre travail : Vérifiez que votre application accepte la valeur de l’émetteur avec et sans une barre oblique lors de la validation de l’émetteur.**

## <a name="why-change"></a>Pourquoi modifier ?
La principale raison d’introduire ces modifications est conforme à la spécification standard OpenID se connecter.  En étant OpenID connecter respecter les exigences, nous espérons que réduire les différences entre l’intégration avec les services d’identité de Microsoft et avec d’autres services d’identité du secteur.  Nous voulons permettent aux développeurs d’utiliser leurs bibliothèques d’authentification libres Favoris sans avoir à modifier les bibliothèques pour tenir compte des différences de Microsoft.

## <a name="what-can-you-do"></a>Que pouvez-vous faire ?
À partir d’aujourd'hui, vous pouvez commencer à effectuer toutes les modifications ci-dessus.  Vous devez immédiatement :

1.  **Supprimez toutes les dépendances sur les `x5t` paramètre d’en-tête.**
2.  **Gérer correctement la transition à partir de `profile_info` à `id_token` dans les réponses jetons.**
3.  **Supprimez toutes les dépendances sur les `id_token_expires_in` paramètre de réponse.**
3.  **Ajouter la `profile` et `email` étendues dans votre application si votre application a besoin d’informations utilisateur de base.**
4.  **Accepter les valeurs de l’émetteur dans les jetons avec et sans une barre oblique.**

Notre [documentation de protocole version 2.0](active-directory-v2-protocols.md) a déjà été mis à jour pour refléter ces modifications, afin que vous pouvez l’utiliser comme référence pour aider à mettre à jour votre code.

Si vous avez d’autres questions sur l’étendue des modifications, n’hésitez pas à communiquer aux nous sur Twitter à @AzureAD.

## <a name="how-often-will-protocol-changes-occur"></a>La fréquence à laquelle les modifications de protocole se produira ?
Nous ne pas prévoir les transmettre les modifications pour les protocoles d’authentification.  Nous allons intentionnellement regroupement ces modifications dans une version afin que vous n’aurez traitée ce type de processus de mise à jour de si tôt à nouveau.  Bien entendu, nous continuent à ajouter des fonctionnalités au service d’authentification convergent version 2.0 vous pouvez tirer parti des, mais ces modifications doivent être additif et saut pas de code existant.

Enfin, nous aimerions dire Merci pour essayer choses pendant la période d’aperçu.  Les idées et expériences de nos premiers ont été particulièrement jusqu'à présent, et nous espérons que vous devez continuer à partager votre avis et des idées.

Bon codage !

La Division identité Microsoft
