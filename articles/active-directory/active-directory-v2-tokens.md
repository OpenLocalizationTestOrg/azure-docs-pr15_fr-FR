<properties
    pageTitle="Référence de Azure AD version 2.0 jeton | Microsoft Azure"
    description="Les types de jetons et revendications émises par le point de terminaison version 2.0"
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

# <a name="v20-token-reference"></a>référence de jeton de version 2.0

Le point de terminaison version 2.0 émet plusieurs types de jetons de sécurité dans le traitement de chaque [flux d’authentification](active-directory-v2-flows.md). Ce document décrit le format, caractéristiques de sécurité et le contenu de chaque type de jeton.

> [AZURE.NOTE]
    Pas tous les scénarios Azure Active Directory et fonctionnalités sont prises en charge par le point de terminaison version 2.0.  Pour déterminer si vous devez utiliser le point de terminaison version 2.0, lisez les [limitations de la version 2.0](active-directory-v2-limitations.md).

## <a name="types-of-tokens"></a>Types de jetons

Point de terminaison de la version 2.0 prend en charge le [protocole d’autorisation OAuth 2.0](active-directory-v2-protocols.md), qui utilise access_tokens et refresh_tokens.  Il prend également en charge l’authentification et se connecter via [OpenID se connecter](active-directory-v2-protocols.md#openid-connect-sign-in-flow), qui présente un troisième type de jeton, l’id_token.  Chacun de ces jetons est représenté par un « jeton porteur ».

Un jeton porteur est un jeton de sécurité léger qui accorde l’accès « porteur » à une ressource protégée. Dans ce cas, « porteur » est une partie qui peut présenter le jeton. Bien que fête doit tout d’abord authentifier avec Azure AD pour recevoir le jeton porteur, si les étapes requises ne sont pas prises pour sécuriser le jeton de stockage et de transmission, il peut être interception et utilisé par un tiers involontaire. Tandis que certains jetons de sécurité ont un mécanisme intégré pour empêcher que des personnes non autorisées à l’aise, jetons PORTEUR n’ont pas ce mécanisme et doivent être acheminés dans un canal sécurisé comme TLS (HTTPS). Si un jeton porteur est transmis en clair, un homme d’attaque peut être utilisée par une partie malveillante pour obtenir le jeton et utilisez-le pour un accès non autorisé à une ressource protégée. Les mêmes principes de sécurité s’appliquent lorsque vous stockez ou la mise en cache des jetons porteur pour une utilisation ultérieure. Toujours vous assurer que votre application transmet et stocke les jetons des porteur de façon sécurisée. Pour des raisons de sécurité plus sur les jetons porteur, voir [RFC 6750 Section 5](http://tools.ietf.org/html/rfc6750).

La plupart des jetons émis par le point de terminaison version 2.0 sont implémentées comme Json Web jetons ou JWTs.  Un JWT est un moyen compact, adaptée aux URL de transfert des informations entre deux parties.  Les informations contenues dans JWTs sont appelées « revendications », ou assertions des informations sur le support et l’objet du jeton.  Les revendications dans JWTs sont des objets JSON codés et sérialisés pour la transmission.  Dans la mesure où les JWTs émis par le point de terminaison version 2.0 sont signés mais pas chiffrés, vous pouvez facilement inspecter le contenu d’un JWT pour le débogage. Pour plus d’informations sur JWTs, vous pouvez faire référence à la [spécification JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## <a name="idtokens"></a>Id_tokens

Id_tokens sont une forme du jeton de sécurité de connexion à votre application reçoit lors de l’authentification à l’aide de [OpenID se connecter](active-directory-v2-protocols.md#openid-connect-sign-in-flow).  Ils sont représentés sous forme de [JWTs](#types-of-tokens)et contient des déclarations que vous pouvez utiliser pour la connexion de l’utilisateur à votre application.  Vous pouvez utiliser les revendications dans un id_token comme vous le souhaitez, fréquemment elles sont utilisées pour afficher les informations de compte ou prendre des décisions de contrôle d’accès dans une application.  Le point de terminaison version 2.0 n’émet un seul type d’id_token, c'est-à-dire un ensemble de revendications quel que soit le type d’utilisateur qui a signé dans cohérent.  C’est à dire que la mise en forme et le contenu de l’id_tokens sera la même pour les deux utilisateurs Account Microsoft personnels et Professionnel ou scolaire comptes.

Id_tokens sont connecté, mais pas chiffré pour le moment.  Lorsque votre application reçoit une id_token, elle doit [valider la signature](#validating-tokens) pour prouver authenticité du jeton et valider quelques revendications du jeton de prouver sa durée de validité.  Les revendications validées par une application varient en fonction des besoins de scénario, mais il existe certaines [validations réclamer courantes](#validating-tokens) que votre application doit effectuer dans tous les scénarios.

Des informations plus complètes sur les revendications dans id_tokens sont fournis ci-dessous, ainsi que d’un exemple id_token.  Notez que les revendications dans id_tokens ne sont pas renvoyées dans un ordre en particulier.  De plus, les nouvelles demandes peuvent être introduites dans id_tokens à tout moment dans le temps, votre application doit s’arrêter pas lors de l’introduction des nouvelles demandes.  La liste ci-dessous répertorie les revendications votre application peut interpréter fiable au moment de la rédaction de ce document.  Si nécessaire, vous trouverez davantage de détails dans la [spécification OpenID se connecter](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-idtoken"></a>Exemple id_token

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiI2NzMxZGU3Ni0xNGE2LTQ5YWUtOTdiYy02ZWJhNjkxNDM5MWUiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vYjk0MTk4MTgtMDlhZi00OWMyLWIwYzMtNjUzYWRjMWYzNzZlL3YyLjAiLCJpYXQiOjE0NTIyODUzMzEsIm5iZiI6MTQ1MjI4NTMzMSwiZXhwIjoxNDUyMjg5MjMxLCJuYW1lIjoiQmFiZSBSdXRoIiwibm9uY2UiOiIxMjM0NSIsIm9pZCI6ImExZGJkZGU4LWU0ZjktNDU3MS1hZDkzLTMwNTllMzc1MGQyMyIsInByZWZlcnJlZF91c2VybmFtZSI6InRoZWdyZWF0YmFtYmlub0BueXkub25taWNyb3NvZnQuY29tIiwic3ViIjoiTUY0Zi1nZ1dNRWppMTJLeW5KVU5RWnBoYVVUdkxjUXVnNWpkRjJubDAxUSIsInRpZCI6ImI5NDE5ODE4LTA5YWYtNDljMi1iMGMzLTY1M2FkYzFmMzc2ZSIsInZlciI6IjIuMCJ9.p_rYdrtJ1oCmgDBggNHB9O38KTnLCMGbMDODdirdmZbmJcTHiZDdtTc-hguu3krhbtOsoYM2HJeZM3Wsbp_YcfSKDY--X_NobMNsxbT7bqZHxDnA2jTMyrmt5v2EKUnEeVtSiJXyO3JWUq9R0dO-m4o9_8jGP6zHtR62zLaotTBYHmgeKpZgTFB9WtUq8DVdyMn_HSvQEfz-LWqckbcTwM_9RNKoGRVk38KChVJo4z5LkksYRarDo8QgQ7xEKmYmPvRr_I7gvM2bmlZQds2OeqWLB1NSNbFZqyFOCgYn3bAQ-nEQSKwBaA36jYGPOVG2r2Qv1uKcpSOxzxaQybzYpQ
```

> [AZURE.TIP] Pour les exercices pratiques, essayez inspectant les revendications dans l’exemple id_token en le collant dans [calebb.net](https://calebb.net).

#### <a name="claims-in-idtokens"></a>Revendications dans id_tokens
| Nom | Réclamer | Exemple de valeur | Description |
| ----------------------- | ------------------------------- | ------------ | --------------------------------- |
| Audience | `aud` | `6731de76-14a6-49ae-97bc-6eba6914391e` | Identifie le destinataire prévu du jeton.  Dans id_tokens, le public est Id de l’Application de votre application, comme affecté à votre application dans le portail d’inscription de l’application.  Votre application doit valider cette valeur et refuser le jeton si elle ne correspond pas. |
| Émetteur | `iss` | `https://login.microsoftonline.com/b9419818-09af-49c2-b0c3-653adc1f376e/v2.0 ` | Identifie le service jeton de sécurité (STS) qui construit et retourne le jeton, ainsi que le client Azure AD dans lequel l’utilisateur a été authentifié.  Votre application doit valider la demande de l’émetteur pour vous assurer que le jeton provenance du point de terminaison de la version 2.0.  Il doit également utiliser la partie guid de la demande de remboursement pour restreindre l’ensemble des clients qui sont autorisés à vous connecter à l’application.  Guid qui indique l’utilisateur est un utilisateur grand public de Microsoft compte est `9188040d-6c67-4c5b-b112-36a304b66dad`. |
| Délivré à | `iat` | `1452285331` | L’heure à laquelle le jeton a été publié, représentée dans le temps d’origine. |
| Délai d’expiration | `exp` | `1452289231` | L’heure à laquelle le jeton devient non valide, sont représentées dans le temps d’origine.  Votre application doit utiliser cette demande pour vérifier la validité de la durée de vie jeton.  |
| Pas avant | `nbf` | `1452285331` |  L’heure à laquelle le jeton est valide, sont représentées dans le temps d’origine. Il est généralement identique à l’heure d’émission.  Votre application doit utiliser cette demande pour vérifier la validité de la durée de vie jeton.  |
| Version | `ver` | `2.0` | La version de l’id_token, telle que définie par Azure AD.  Du point de terminaison version 2.0, la valeur sera `2.0`. |
| Id de client | `tid` | `b9419818-09af-49c2-b0c3-653adc1f376e` | Guid représentant Azure Active Directory du client qui proviennent de l’utilisateur.  Pour les comptes professionnel et scolaires, le guid sera l’ID de client immuable de l’organisation appartenant à l’utilisateur.  Pour les comptes personnels, la valeur sera `9188040d-6c67-4c5b-b112-36a304b66dad`.  La `profile` étendue est nécessaire pour recevoir cette demande. |
| Code hachage | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Le hachage code est inclus dans id_tokens uniquement lorsque l’id_token est émis en parallèle avec un code d’autorisation OAuth 2.0.  Il peut être utilisé pour valider l’authenticité d’un code d’autorisation.  Consultez la [spécification OpenID se connecter](http://openid.net/specs/openid-connect-core-1_0.html) pour plus d’informations sur l’exécution de cette validation. |
| Hachage jeton d’accès | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Le hachage jeton d’accès est inclus dans id_tokens uniquement lorsque l’id_token est émis en parallèle avec un jeton d’accès OAuth 2.0.  Il peut être utilisé pour valider l’authenticité d’un jeton d’accès.  Consultez la [spécification OpenID se connecter](http://openid.net/specs/openid-connect-core-1_0.html) pour plus d’informations sur l’exécution de cette validation. |
| Valeur à usage unique | `nonce` | `12345` | La valeur unique est une stratégie pour atténuer les attaques de relecture jeton.  Votre application peut spécifier une valeur unique dans une requête d’autorisation à l’aide de la `nonce` paramètre de requête.  La valeur que vous fournissez dans la demande sera émise dans l’id_token `nonce` réclamer, non modifié.  Cela permet à votre application vérifier la valeur par rapport à la valeur qu'il spécifié dans la demande, qui associe session de l’application à un id_token donné.  Votre application doit effectuer cette validation au cours du processus de validation id_token. |
| Nom | `name` | `Babe Ruth` | La demande de nom fournit une valeur lisible humaine qui identifie l’objet du jeton. Cette valeur n’est pas forcément uniques, est modifiable et est conçue pour être utilisé uniquement à des fins d’affichage.  La `profile` étendue est nécessaire pour recevoir cette demande. |
| Messagerie | `email` | `thegreatbambino@nyy.onmicrosoft.com` | L’adresse de messagerie principale associé au compte utilisateur, le cas échéant.  Sa valeur est modifiable et peut changer pour un utilisateur donné au fil du temps.  La `email` étendue est nécessaire pour recevoir cette demande. |
| Nom d’utilisateur par défaut | `preferred_username` | `thegreatbambino@nyy.onmicrosoft.com` | Le nom d’utilisateur principal qui est utilisé pour représenter l’utilisateur dans le point de terminaison version 2.0.  Il peut être une adresse de messagerie, numéro de téléphone ou un nom d’utilisateur générique sans une mise en forme spécifiée.  Sa valeur est modifiable et peut changer pour un utilisateur donné au fil du temps.  La `profile` étendue est nécessaire pour recevoir cette demande. |
| Objet | `sub` | `MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q` | L’utilisateur principal sur lequel le jeton indique les informations, telles que l’utilisateur d’une application. Cette valeur est immuable et ne peut pas être réaffectée ou réutilisé, donc il peut être utilisé pour la vérification d’autorisation en toute sécurité, tels que lorsque le jeton est utilisé pour accéder à une ressource. L’objet est toujours présent dans les jetons les problèmes d’AD Azure, il est recommandé à l’aide de cette valeur dans un système d’autorisation objectif général. |
| ObjectId | `oid` | `a1dbdde8-e4f9-4571-ad93-3059e3750d23` | L’Id du compte professionnel ou scolaire dans le système Azure AD objet.  Cette demande ne sera pas émise pour les comptes Microsoft personnels.  La `profile` étendue est nécessaire pour recevoir cette demande. |


## <a name="access-tokens"></a>Jetons d’accès

Jetons d’accès émis par le point de terminaison version 2.0 ne sont à ce stade consommables par les Services Microsoft.  Vos applications ne devez pas effectuer de validation ni inspection des jetons d’accès pour tous les scénarios pris en charge actuellement.  Vous pouvez considérer les jetons d’accès comme complètement opaque - ils sont seulement les chaînes votre application pouvant passer à Microsoft dans les requêtes HTTP.

Dans un futur proche, le point de terminaison version 2.0 présente la possibilité de votre application recevoir des jetons d’accès à partir d’autres clients.  À ce stade, cette information est mise à jour avec les informations de que votre application doit effectuer validation jeton d’accès et d’autres tâches similaires.

Lorsque vous demandez un jeton d’accès à partir de la version 2.0, le point de terminaison version 2.0 renvoie également des métadonnées sur le jeton d’accès à la consommation de votre application.  Ces informations comprennent le délai d’expiration du jeton d’accès et les étendues pour laquelle il est valide.  Cela permet à votre application pour effectuer la mise en cache intelligente des jetons d’accès sans avoir à analyser ouvrir le jeton d’accès.

## <a name="refresh-tokens"></a>Actualiser les jetons

Actualiser les jetons sont jetons de sécurité qui permet de votre application acquérir nouveau jetons dans un flux 2.0 jeton d’accès.  Il permet à votre application obtenir l’accès à long terme aux ressources au nom d’un utilisateur sans interaction par l’utilisateur.

Actualisation des jetons sont ressources multiples.  C'est-à-dire qu’un jeton actualisation reçu au cours d’une demande de jeton pour une ressource peut être utilisés pour les jetons d’accès à une ressource complètement différente.

Pour recevoir une actualisation d’une réponse jeton, votre application doit demander & accordée la `offline_acesss` étendue.   Pour en savoir plus sur les `offline_access` étendue, reportez-vous à l' [article consentement & étendues ici](active-directory-v2-scopes.md).

Actualiser les jetons sont et sera toujours, complètement opaque à votre application.  Ils sont émis par le point de terminaison de version 2.0 Azure AD et peuvent uniquement être inspectées & interprétés par le point de terminaison version 2.0.  Ils sont à long terme, mais votre application ne doit pas être écrites va-t-il se passer qu’un jeton actualisation prendra pour une période donnée.  Jetons d’actualisation peuvent être non valide à tout moment pour diverses raisons.  Le seul moyen pour votre application de savoir si un jeton actualisation est valide consiste à essayer échanger à l’aide d’une demande de jeton au point de terminaison version 2.0.

Lorsque vous échanger un jeton d’actualisation pour un nouveau jeton d’accès (et si votre application a été accordée le `offline_access` étendue), vous recevrez un nouveau jeton d’actualisation dans la réponse jeton.  Vous devez enregistrer le jeton actualisation nouvellement émis, remplaçant celle utilisée dans la demande.  Ceci garantit que vos jetons actualisation restent valides pour autant que possible.

## <a name="validating-tokens"></a>Validation des jetons

À ce stade, la validation uniquement jeton que vos applications ne devez effectuer validation id_tokens.  Pour valider un id_token, votre application doit valider signature d’id_token et les revendications dans l’id_token.

<!-- TODO: Link -->
Nous fournissons des bibliothèques et des exemples de code qui montrent comment gérer facilement la validation jeton - les informations ci-dessous est fournie simplement pour ceux qui souhaitent comprendre le processus sous-jacente.  Plusieurs bibliothèques libres fête 3e sont également disponibles pour la validation JWT - au moins une option pour presque chaque plateforme & langue existent.

#### <a name="validating-the-signature"></a>Validation de la signature
Un JWT contient trois segments, qui sont séparées par le `.` caractère.  Le premier segment est connu sous l' **en-tête**, le second dans le **corps**et la troisième comme la **signature**.  Le segment signature peut être utilisé pour valider l’authenticité de l’id_token afin qu’il peut être approuvé par votre application.

Id_Tokens êtes connecté à l’aide des algorithmes de chiffrement asymétriques standard, par exemple 256 RSA. L’en-tête de l’id_token contient des informations sur la méthode de clé et de chiffrement utilisée pour signer le jeton :

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

La `alg` réclamer indique l’algorithme qui a été utilisé pour signer le jeton, alors que la `kid` réclamer indique la clé publique particulier qui a été utilisée pour signer le jeton.

À un moment donné dans le temps, le point de terminaison version 2.0 peut-être signer un id_token à l’aide de l’une d’un certain ensemble de paires de clés publique-privée.  Le point de terminaison version 2.0 fait pivoter l’ensemble des clés de façon périodique, possible afin que votre application doit être écrits pour gérer ces modifications clées automatiquement.  Une fréquence raisonnablement pour vérifier les mises à jour aux clés publiques utilisées par le point de terminaison version 2.0 est environ 24 heures.

Vous pouvez acquérir les données clées signature nécessaires pour valider la signature à l’aide du document de métadonnées OpenID connecter situé à :

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [AZURE.TIP] Essayez cette URL dans un navigateur !

Ce document métadonnées est un objet JSON contenant plusieurs éléments utiles d’information, telles que l’emplacement de différents points de terminaison requises pour l’authentification OpenID se connecter.  

Il inclut également un `jwks_uri`, ce qui donne l’emplacement de l’ensemble des clés publiques utilisé pour la signature de jetons.  Le document JSON situé à la `jwks_uri` contient toutes les informations de clé publique en cours d’utilisation à ce moment particulier.  Votre application peut utiliser les `kid` réclamer dans l’en-tête JWT pour sélectionner la clé publique dans ce document a été utilisé pour signer un jeton donné.  Il peut ensuite effectuer la validation des signatures à l’aide de la clé publique appropriée et l’algorithme indiquée.

Validation de signature est en dehors de l’étendue de ce document - nombreuses bibliothèques libres sont disponibles pour vous aider à faire si nécessaire.

#### <a name="validating-the-claims"></a>Valider les revendications
Lorsque votre application reçoit une id_token lors de la connexion utilisateur, il doit également effectuer quelques vérifications la réclamation dans l’id_token.  Ces incluent, mais ne sont pas limités à :

- Réclamer de **l’Audience** - pour vérifier que l’id_token a été destinés à donner à votre application.
- Revendications **Pas avant** et **Délai d’Expiration** - pour vérifier que l’id_token n’a pas expiré.
- La demande de **l’émetteur** - pour vérifier que le jeton a été émis bien dans votre application par le point de terminaison version 2.0.
- La **valeur à usage unique** - comme une atténuation d’attaque relecture jeton.
- et bien plus encore...

Pour une liste complète des validations réclamer que votre application doit effectuer, reportez-vous à la [spécification OpenID se connecter](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation).

Détails des valeurs prévues pour ces revendications sont inclus dans la [section id_token](#id_tokens)au-dessus.


## <a name="token-lifetimes"></a>Durée de vie des jetons

La durée de vie des jetons suivantes est fournies purement pour votre présentation, comme elles permettent de développement et de débogage des applications.  Vos applications ne doivent pas être écrites va-t-il se passer d’un de ces durée de vie reste constante - qu’ils peuvent et modifiera à un moment donné dans le temps.

| Jetons | Durée de vie | Description |
| ----------------------- | ------------------------------- | ------------ |
| Id_Tokens (comptes scolaire ou Professionnel) | 1 heure | Id_Tokens sont généralement valides pendant une heure.  Votre application web peut utiliser ce même durée de vie dans la conservation de sa propre session avec l’utilisateur (recommandé) ou choisissez une durée de vie session complètement différente.  Si votre application a besoin d’obtenir un nouveau id_token, elle doit simplement effectuer une nouvelle demande de connexion à la version 2.0 autoriser le point de terminaison.  Si l’utilisateur a une session de navigateur valide avec le point de terminaison version 2.0, ils ne peuvent pas tenus à entrer ses informations d’identification à nouveau. |
| Id_Tokens (comptes personnels) | dernières 24 heures | Id_Tokens pour les comptes personnels sont généralement valides pendant 24 heures.  Votre application web peut utiliser ce même durée de vie dans la conservation de sa propre session avec l’utilisateur (recommandé) ou choisissez une durée de vie session complètement différente.  Si votre application a besoin d’obtenir un nouveau id_token, elle doit simplement effectuer une nouvelle demande de connexion à la version 2.0 autoriser le point de terminaison.  Si l’utilisateur a une session de navigateur valide avec le point de terminaison version 2.0, ils ne peuvent pas tenus à entrer ses informations d’identification à nouveau. |
| Jetons d’accès (comptes scolaire ou Professionnel) | 1 heure | Indiqué dans les réponses jetons dans le cadre des métadonnées jeton. |
| Jetons d’accès (comptes personnels) | 1 heure | Indiqué dans les réponses jetons dans le cadre des métadonnées jeton.  Access_tokens émis au nom de comptes personnels peut être configuré pour une durée de vie différente, mais une heure est généralement le cas. |
| Actualiser les jetons (compte scolaire ou Professionnel) | Jusqu'à 14 jours | Un jeton unique d’actualisation est valide pour un maximum de 14 jours.  Toutefois, le jeton d’actualisation peut devenir non valide à tout moment pour différentes raisons, afin que votre application doit continuer à essayer d’utiliser un jeton d’actualisation jusqu'à ce qu’il ne peut pas, ou jusqu'à ce que votre application remplace par un nouveau jeton d’actualisation.  Un jeton actualiser également devient non valide si elle a été 90 jours dans la mesure où l’utilisateur a entré ses informations d’identification. |
| Actualiser les jetons (comptes personnels) | Jusqu'à 1 an | Un jeton unique d’actualisation est valide pour un maximum de 1 an.  Toutefois, le jeton d’actualisation peut devenir non valide à tout moment pour différentes raisons, afin que votre application doit continuer à essayer d’utiliser un jeton d’actualisation jusqu'à ce qu’il ne peut pas. |
| Codes d’autorisation (comptes scolaire ou Professionnel) | 10 minutes | Codes d’autorisation sont délibérément éphémères et doivent être remboursées immédiatement pour access_tokens et refresh_tokens lors de leur réception. |
| Codes d’autorisation (comptes personnels) | 5 minutes | Codes d’autorisation sont délibérément éphémères et doivent être remboursées immédiatement pour access_tokens et refresh_tokens lors de leur réception.  Codes d’autorisation émis au nom de comptes personnels sont également utilisation unique. |
