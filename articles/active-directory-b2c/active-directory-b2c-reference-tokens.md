<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Les types de jetons émis dans le B2C annuaire Active Azure."
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


# <a name="azure-ad-b2c-token-reference"></a>Azure AD B2C : Référence de jeton

Azure Active Directory (AD Azure) B2C émet plusieurs types de jetons de sécurité lorsqu’il traite chaque [flux d’authentification](active-directory-b2c-apps.md). Ce document décrit le format, caractéristiques de sécurité et le contenu de chaque type de jeton.

## <a name="types-of-tokens"></a>Types de jetons

Azure AD B2C prend en charge le [protocole d’autorisation OAuth 2.0](active-directory-b2c-reference-protocols.md), qui utilise des jetons d’accès et jetons d’actualisation. Il prend également en charge l’authentification et se connecter via [OpenID se connecter](active-directory-b2c-reference-protocols.md), qui présente un troisième type de jeton : le jeton ID. Chacun de ces jetons est représenté par un jeton porteur.

Un jeton porteur est un jeton de sécurité léger qui accorde l’accès « porteur » à une ressource protégée. Le porteur est toute partie qui peut présenter le jeton. Azure AD doit tout d’abord authentifier fête avant de pouvoir recevoir un jeton porteur. Mais si les étapes requises ne sont pas prises pour sécuriser le jeton de stockage et de transmission, il peut être intercepter et utilisé par un tiers involontaire. Certaines des jetons de sécurité ont un mécanisme intégré pour empêcher que des personnes non autorisées à l’aise, mais jetons porteur ne possèdent pas ce mécanisme. Ils doivent être acheminés dans un canal sécurisé, telles que TLS (HTTPS).

Si un jeton porteur est transmis à l’extérieur d’un canal sécurisé, une partie malveillante permet une attaque dans milieu acquérir le jeton et utilisez-le pour accéder à une ressource protégée. Les mêmes principes de sécurité s’appliquent quand jetons PORTEUR sont stockés ou mis en cache pour une utilisation ultérieure. Toujours Assurez-vous que votre application transmet et stocke les jetons des porteur de façon sécurisée.

Pour des raisons de sécurité supplémentaires sur les jetons porteur, voir [RFC 6750 Section 5](http://tools.ietf.org/html/rfc6750).

La plupart des jetons Azure AD B2C problèmes sont implémentées en tant que jetons web JSON (JWTs). Un JWT est un moyen compact, adaptée aux URL de transfert des informations entre deux parties. JWTs contiennent des informations appelées revendications. Il s’agit des assertions d’informations sur le porteur et l’objet du jeton. Les revendications dans JWTs sont des objets JSON qui sont codés et sérialisés pour la transmission. Étant donné que les JWTs émanant d’Azure AD B2C sont signés mais pas chiffrés, vous pouvez facilement inspecter le contenu d’un JWT pour le déboguer. Plusieurs outils sont disponibles que cela, y compris [calebb.net](http://calebb.net). Pour plus d’informations sur JWTs, reportez-vous aux [spécifications JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

### <a name="id-tokens"></a>Jetons ID

Un jeton ID est une forme de jeton de sécurité qui reçoit votre application à partir de la AD B2C Azure `authorize` et `token` points de terminaison. Jetons ID sont représentés sous forme de [JWTs](#types-of-tokens), et qu’ils contiennent revendications que vous pouvez utiliser pour identifier les utilisateurs dans votre application. Lorsque les jetons ID sont acquis à partir de la `authorize` point de terminaison, ils sont souvent utilisés pour se connecter aux utilisateurs d’applications web. Lorsque les jetons ID sont acquis à partir de la `token` point de terminaison, ils peuvent être envoyés dans les requêtes HTTP pendant la communication entre deux composants de l’application ou service même. Vous pouvez utiliser les revendications dans un jeton ID selon vos besoins. Elles sont utilisées pour afficher les informations de compte ou pour prendre des décisions de contrôle d’accès dans une application.  

Jetons ID sont signés, mais ils ne sont pas chiffrés pour le moment. Lorsque votre application ou API reçoit un jeton d’ID, elle doit [valider la signature](#token-validation) pour prouver que le jeton est authentique. Votre application ou API doit également valider quelques revendications du jeton de prouver qu’elle est valide. En fonction des exigences du scénario, les revendications validées par une application peuvent varier, mais votre application doit effectuer certaines [validations réclamer courantes](#token-validation) dans tous les scénarios.

#### <a name="sample-id-token"></a>Jeton d’ID exemple
```
// Line breaks for display purposes only

eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IklkVG9rZW5TaWduaW5nS2V5Q29udGFpbmVyIn0.
eyJleHAiOjE0NDIzNjAwMzQsIm5iZiI6MTQ0MjM1NjQzNCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly9s
b2dpbi5taWNyb3NvZnRvbmxpbmUuY29tLzc3NTUyN2ZmLTlhMzctNDMwNy04YjNkLWNjMzExZjU4ZDkyNS92
Mi4wLyIsImFjciI6ImIyY18xX3NpZ25faW5fc3RvY2siLCJzdWIiOiJOb3Qgc3VwcG9ydGVkIGN1cnJlbnRs
eS4gVXNlIG9pZCBjbGFpbS4iLCJhdWQiOiI5MGMwZmU2My1iY2YyLTQ0ZDUtOGZiNy1iOGJiYzBiMjlkYzYi
LCJpYXQiOjE0NDIzNTY0MzQsImF1dGhfdGltZSI6MTQ0MjM1NjQzNCwiaWRwIjoiZmFjZWJvb2suY29tIn0.
h-uiKcrT882pSKUtWCpj-_3b3vPs3bOWsESAhPMrL-iIIacKc6_uZrWxaWvIYkLra5czBcGKWrYwrAC8ZvQe
DJWZ50WXQrZYODEW1OUwzaD_I1f1HE0c2uvaWdGXBpDEVdsD3ExKaFlKGjFR2V7F-fPThkVDdKmkUDQX3bVc
yyj2V2nlCQ9jd7aGnokTPfLfpOjuIrTsAdPcGpe5hfSEuwYDmqOJjGs9Jp1f-eSNEiCDQOaTBSvr479L5ptP
XWeQZyX2SypN05Rjr05bjZh3j70ZUimiocfJzjibeoDCaQTz907yAg91WYuFOrQxb-5BaUoR7K-O7vxr2M-_
CQhoFA

```

### <a name="access-tokens"></a>Jetons d’accès

Un jeton d’accès est également un formulaire de jeton de sécurité qui reçoit votre application à partir de la AD B2C Azure `authorize` et `token` points de terminaison. Jetons d’accès sont également représentés sous forme de [JWTs](#types-of-tokens), et qu’ils contiennent revendications que vous pouvez utiliser pour identifier les utilisateurs dans votre API & services web.

Les jetons d’accès sont signés, mais elles ne sont pas chiffrées pour le moment - et très similaire à jetons id.  Jetons d’accès doivent être utilisés pour accéder aux services web et des API et pour identifier et authentifier l’utilisateur dans ces services.  Toutefois, ils ne fournissent pas tout commentaire du autorisation auprès de ces services.  Cela signifie que la `scp` réclamer dans jeton d’accès ne pas limiter ou dans le cas contraire représentent le niveau d’accès que l’objet du jeton a été accordée.

Lorsque votre API reçoit un jeton d’accès, il doit [valider la signature](#token-validation) pour prouver que le jeton est authentique. Votre API doit également valider quelques revendications du jeton de prouver que celui-ci est valide. En fonction des exigences du scénario, les revendications validées par une application peuvent varier, mais votre application doit effectuer certaines [validations réclamer courantes](#token-validation) dans tous les scénarios.

### <a name="claims-in-id--access-tokens"></a>Revendications dans ID et jetons d’accès

Lorsque vous utilisez Azure AD B2C, vous pouvez permissions contrôler le contenu de vos jetons. Vous pouvez configurer des [stratégies](active-directory-b2c-reference-policies.md) pour envoyer certains jeux de données de l’utilisateur dans revendications dont votre application a besoin pour ses opérations. Ces revendications peuvent inclure des propriétés standards, telles que l’utilisateur `displayName` et `emailAddress`. Ils peuvent également inclure des [attributs d’utilisateurs personnalisés](active-directory-b2c-reference-custom-attr.md) que vous pouvez définir dans votre annuaire B2C. Chaque ID et accès jeton que vous recevez contiendra un certain ensemble de revendications liés à la sécurité. Vos applications peuvent utiliser ces revendications s’authentifier les utilisateurs et les demandes.

Notez que les revendications dans des jetons ID ne sont pas renvoyées dans un ordre en particulier. En outre, nouvelles demandes peuvent être introduites dans les jetons ID à tout moment. Votre application doit être pas interrompu lors de l’introduction des nouvelles demandes. Voici les revendications que vous considérez comme existante dans access et identification des jetons émanant d’Azure AD B2C. Les autres plaintes seront déterminés par les stratégies. Pour les exercices pratiques, essayez inspectant les revendications dans le jeton d’ID exemple en le collant dans [calebb.net](http://calebb.net). Vous trouverez des détails supplémentaires dans la [spécification OpenID se connecter](http://openid.net/specs/openid-connect-core-1_0.html).

| Nom | Réclamer | Exemple de valeur | Description |
| ----------------------- | ------------------------------- | ------------ | --------------------------------- |
| Audience | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Une audience réclamer identifie le destinataire prévu du jeton. Pour Azure AD B2C, le public est ID de l’Application de votre application, comme affecté à votre application dans le portail d’inscription de l’application. Votre application doit valider cette valeur et refuser le jeton si elle ne correspond pas. |
| Émetteur | `iss` | `https://login.microsoftonline.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Cette demande identifie le service jeton de sécurité (STS) qui crée et retourne le jeton. Elle identifie également le répertoire Azure AD dans lequel l’utilisateur a été authentifié. Votre application doit valider la demande de l’émetteur pour vous assurer que le jeton provenance du point de terminaison de la version 2.0. |
| Délivré à | `iat` | `1438535543` | Cet argument est le moment auquel le jeton a été publié, représentée dans le temps d’origine. |
| Délai d’expiration | `exp` | `1438539443` | Le délai d’expiration réclamer est le moment auquel le jeton devient non valide, représentée dans heure d’origine. Votre application doit utiliser cette demande pour vérifier la validité de la durée de vie jeton.  |
| Pas avant | `nbf` | `1438535543` | Cette demande est l’heure à laquelle le jeton est valide, représenté dans le temps d’origine. Il s’agit généralement identique à l’heure de que émission du jeton. Votre application doit utiliser cette demande pour vérifier la validité de la durée de vie jeton.  |
| Version | `ver` | `1.0` | Il s’agit de la version du jeton ID, telle que définie par Azure AD. |
| Code hachage | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Un hachage code est inclus dans un jeton ID uniquement lorsque le jeton est émis avec un code d’autorisation OAuth 2.0. Un hachage code peut être utilisé pour valider l’authenticité d’un code d’autorisation. Consultez la [spécification OpenID se connecter](http://openid.net/specs/openid-connect-core-1_0.html) pour plus d’informations sur la façon d’effectuer cette validation. |
| Hachage jeton d’accès | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Un hachage jeton d’accès est inclus dans un jeton ID uniquement lorsque le jeton est émis avec un jeton d’accès OAuth 2.0. Un hachage jeton access peut être utilisé pour valider l’authenticité d’un jeton d’accès. Consultez la [spécification OpenID se connecter](http://openid.net/specs/openid-connect-core-1_0.html) pour plus d’informations sur la façon d’effectuer cette validation. |
| Valeur à usage unique | `nonce` | `12345` | Stratégie utilisée pour atténuer les attaques de relecture jeton est une valeur unique. Votre application peut spécifier une valeur unique dans une requête d’autorisation à l’aide de la `nonce` paramètre de requête. La valeur que vous fournissez dans la demande sera émise non modifiée dans le `nonce` revendiquer d’un jeton d’ID uniquement. Cela permet à votre application vérifier la valeur par rapport à la valeur qu'il spécifié dans la demande, qui associe session de l’application avec un jeton ID donné. Votre application doit effectuer cette validation au cours du processus de validation jeton ID. |
| Objet | `sub` | `Not supported currently. Use oid claim.` | Il s’agit d’une entité sur laquelle le jeton indique les informations, telles que l’utilisateur d’une application. Cette valeur est immuable et ne peut pas être réaffectée ou réutilisée. Il peut être utilisé pour la vérification d’autorisation en toute sécurité, tels que lorsque le jeton est utilisé pour accéder à une ressource. Toutefois, la demande d’objet n’est pas encore implémentée dans les AD B2C Azure. Vous devez configurer vos stratégies pour inclure l’ID d’objet `oid` réclamer et sa valeur permet d’identifier les utilisateurs, plutôt que d’utiliser la réclamation d’objet pour l’autorisation. |
| Référence de classe contexte d’authentification | `acr` | `b2c_1_sign_in` | Il s’agit du nom de la stratégie qui a été utilisé pour acquérir le jeton ID.  |
| Moment de l’authentification | `auth_time` | `1438535543` | Cette demande est l’heure à laquelle un utilisateur dernière saisi les informations d’identification, représentée dans le temps d’origine. |


### <a name="refresh-tokens"></a>Actualiser les jetons

Actualiser les jetons sont des jetons de sécurité que votre application peut utiliser pour acquérir les nouveaux jetons ID et jetons dans un flux 2.0 jeton d’accès. Ils fournissent votre application access à long terme aux ressources au nom d’utilisateurs sans interaction avec les utilisateurs.

Pour recevoir une actualisation jeton dans une réponse jeton, votre application doit demander la `offline_acesss` étendue. Pour en savoir plus sur les `offline_access` étendue, reportez-vous à la [référence au protocole Azure AD B2C](active-directory-b2c-reference-protocols.md).

Actualiser les jetons sont et sera toujours, complètement opaque à votre application. Ils sont émis par Azure AD et peuvent être inspectées et interprétés uniquement par Azure AD. Ils sont à long terme, mais votre application ne doit pas apparaître dans l’attente que prendra un jeton d’actualisation pour une période donnée. Jetons d’actualisation peuvent être non valide à tout moment pour diverses raisons. Le seul moyen pour votre application de savoir si un jeton actualisation est valide consiste à essayer échanger à l’aide d’une demande de jeton à Azure Active Directory.

Lorsque vous échanger un jeton d’actualisation pour un nouveau jeton (et si votre application a été octroyée le `offline_access` étendue), vous recevrez un nouveau jeton d’actualisation dans la réponse jeton. Vous devez enregistrer le jeton actualisation nouvellement émis. Il doit remplacer le jeton d’actualisation que vous avez utilisées précédemment dans la demande. Cela permet de garantir que vos jetons actualisation restent valides pour autant que possible.

## <a name="token-validation"></a>Validation des jeton

Pour valider un jeton, votre application doit vérifier la signature et les revendications du jeton.

De nombreuses bibliothèques libres sont disponibles pour validation JWTs, en fonction de la langue de votre choix. Nous vous recommandons d’Explorer ces options plutôt que de mettre en œuvre votre propre logique de validation. Les informations contenues dans ce guide peuvent vous aider à apprendre à utiliser correctement ces bibliothèques.

### <a name="validate-the-signature"></a>Valider la signature
Un JWT contient trois segments, séparés par le `.` caractère. Le premier segment est l' **en-tête**, le second est le **corps**et la troisième est la **signature**. Le segment signature peut être utilisé pour valider l’authenticité du jeton afin qu’il peut être approuvé par votre application.

Azure AD B2C jetons êtes connectés à l’aide des algorithmes de chiffrement asymétriques standard, tels que RSA 256. L’en-tête du jeton contient des informations sur la méthode de clé et de chiffrement utilisée pour signer le jeton :

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

La `alg` réclamer indique l’algorithme qui a été utilisé pour signer le jeton. La `kid` réclamer indique la clé publique particulier qui a été utilisée pour signer le jeton.

À tout moment, Azure AD peut-être signer un jeton à l’aide de l’une d’un certain ensemble de paires de clés publique-privée. Azure AD fait pivoter l’ensemble de clés possible régulièrement, afin que votre application doit être écrits pour gérer ces modifications clées automatiquement. Une fréquence raisonnablement pour vérifier les mises à jour aux clés publiques utilisées par Azure AD est de 24 heures.

Azure AD B2C a un point de terminaison de métadonnées OpenID se connecter. Ceci permet aux applications récupérer des informations sur Azure AD B2C en cours d’exécution. Ces informations comprennent les points de terminaison, le contenu de jetons et jeton de clés de signature. Dans l’annuaire B2C contient un document de métadonnées JSON pour chaque stratégie. Par exemple, le document de métadonnées pour les `b2c_1_sign_in` stratégie dans la `fabrikamb2c.onmicrosoft.com` se trouve à :

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in
```

`fabrikamb2c.onmicrosoft.com`est le répertoire B2C utilisé pour authentifier l’utilisateur, et `b2c_1_sign_in` est la stratégie utilisée pour obtenir le jeton. Pour déterminer la stratégie a été utilisée pour signer un jeton (et où vous pouvez pour récupérer les métadonnées), vous avez deux possibilités. Tout d’abord, le nom de la stratégie est inclus dans le `acr` réclamer dans le jeton. Vous pouvez analyser revendications en dehors du corps de la JWT en base 64 décodage le corps et désérialise la chaîne JSON qui résulte. La `acr` réclamer s’agit du nom de la stratégie a été utilisé pour le jeton de problème.  L’autre option consiste à coder la stratégie dans la valeur de la `state` paramètre lorsque vous lancé de demande, puis le décoder pour déterminer quelle stratégie a été utilisée. Les deux méthodes sont valide.

Le document de métadonnées est un objet JSON qui contient plusieurs informations utiles. Ces incluent l’emplacement des points de terminaison requis pour effectuer l’authentification OpenID se connecter. Ils incluent également `jwks_uri`, ce qui donne l’emplacement de l’ensemble des clés publiques qui sont utilisés pour signer les jetons. Qu’emplacement est indiqué ici, mais il est préférable récupérer l’emplacement dynamiquement en utilisant le document de métadonnées et l’analyse des `jwks_uri`:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in
```

Le document JSON situé dans cette URL contient toutes les informations de clé publique en cours d’utilisation à un moment donné. Votre application peut utiliser les `kid` revendiquer dans l’en-tête JWT pour sélectionner la clé publique dans le document JSON qui est utilisé pour signer un jeton donné. Il peut effectuer puis validation de la signature à l’aide de la clé publique appropriée et l’algorithme indiquée.

Une description de la façon d’effectuer la validation de signature est en dehors de l’étendue de ce document. De nombreuses bibliothèques libres sont disponibles pour vous aider à ceci si vous en avez besoin.

### <a name="validate-the-claims"></a>Valider les revendications
Lorsque votre application ou API reçoit un jeton ID, il doit également exécuter plusieurs contrôles sur les revendications dans le jeton d’ID. Ils incluent, mais ne sont pas limités à :

- La demande **d’assistance** : Cela permet de vérifier que le jeton ID a été destiné à donner à votre application.
- Les réclamations **pas avant** et **délai d’expiration** : ces vérifier que le jeton d’ID n’a pas expiré.
- La demande de **l’émetteur** : Cela permet de vérifier que le jeton a été émis à votre application par Azure Active Directory.
- La **valeur à usage unique**: il s’agit d’une stratégie d’atténuation d’attaque relecture jeton.

Pour une liste complète des contrôles de que votre application doit effectuer, reportez-vous à la [spécification OpenID se connecter](https://openid.net). Détails des valeurs prévues pour ces revendications sont inclus dans la [section jeton](#types-of-tokens)précédente.  

## <a name="token-lifetimes"></a>Durée de vie des jetons

La durée de vie des jetons suivantes est fournies pour favoriser vos connaissances. Ils peuvent vous aider lorsque vous développez et déboguez des applications. Notez que vos applications ne doivent pas être écrites va-t-il se passer d’un de ces durée de vie reste constante. Ils peuvent et modifiera.  Vous pouvez en savoir plus sur la personnalisation de la durée de vie des jetons dans Azure AD B2C [ici](active-directory-b2c-token-session-sso.md).

| Jetons | Durée de vie | Description |
| ----------------------- | ------------------------------- | ------------ |
| Jetons ID | Une heure | Jetons ID sont généralement valides pendant une heure. Votre application web peut utiliser cette durée de vie pour maintenir ses propres sessions avec des utilisateurs (recommandés). Vous pouvez également choisir une durée de vie de session différente. Si votre application a besoin d’obtenir un identifiant jeton, il devra simplement effectuer une nouvelle demande de connexion à Azure Active Directory. Si un utilisateur possède une session de navigateur valide avec Azure AD, cet utilisateur ne peut pas obligé entrer à nouveau les informations d’identification. |
| Actualiser les jetons | Jusqu'à 14 jours | Un jeton unique d’actualisation est valide pour un maximum de 14 jours. Toutefois, un jeton d’actualisation peut devenir non valide à tout moment pour différentes raisons. Votre application doit continuer à essayer d’utiliser un jeton d’actualisation jusqu'à ce que la requête échoue, ou jusqu'à ce que votre application remplace le jeton actualisation par une nouvelle.  Un jeton d’actualisation peut devenir non valide si 90 jours écoulée depuis l’utilisateur entré en dernier des informations d’identification. |
| Codes d’autorisation | Cinq minutes | Codes d’autorisation sont intentionnellement éphémères. Ils doivent être échangés immédiatement des jetons d’accès, des jetons ID ou jetons actualisation lors de leur réception. |
