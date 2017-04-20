<properties
    pageTitle="Azure AD version 2.0 implicite flux | Microsoft Azure"
    description="Création d’applications web à l’aide de la mise en œuvre de la version 2.0 d’Azure AD du flux implicite pour les applications de page unique."
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

# <a name="v20-protocols---spas-using-the-implicit-flow"></a>version 2.0 protocoles - SPAs à l’aide du flux implicite
Avec le point de terminaison version 2.0, vous pouvez signer des utilisateurs dans vos applications de page unique avec des comptes personnels et Professionnel ou scolaire de Microsoft.  Page unique et autres applications JavaScript qui s’exécutent principalement dans une face de navigateur quelques intéressantes défis lorsqu’il s’agit d’authentification :

- Les caractéristiques de sécurité de ces applications sont significatives à partir des applications web serveur traditionnel.
- Nombreux serveurs d’autorisation et fournisseurs d’identité ne prennent pas en charge CORS demandes.
- Navigateur pleine page redirige en dehors de l’application deviennent particulièrement invasive à l’expérience utilisateur.

Pour ces applications (penser : AngularJS, Ember.js, React.js, etc.) Azure AD prend en charge le flux Grant implicite OAuth 2.0.  Le flux implicite est décrite dans la [Spécification 2.0 OAuth](http://tools.ietf.org/html/rfc6749#section-4.2).  Son principal avantage est qu’elle permet de l’application obtenir des jetons à partir d’Azure AD sans effectuer un serveur principal d’informations d’identification exchange.  Cela permet à l’application à se connecter l’utilisateur, maintenir la session et obtenir jetons à d’autres API web du client de code JavaScript.  Il existe quelques considérations relatives à la sécurité importantes à prendre en compte lors de l’utilisation du flux implicite - spécifiquement autour de [client](http://tools.ietf.org/html/rfc6749#section-10.3) et de [l’emprunt d’identité utilisateur](http://tools.ietf.org/html/rfc6749#section-10.3).

Si vous souhaitez utiliser le flux implicite et Azure AD pour ajouter l’authentification dans votre application JavaScript, nous vous recommandons de qu'utiliser notre bibliothèque JavaScript open source, [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js).  Peu de didacticiels AngularJS sont disponibles [ici](active-directory-appmodel-v2-overview.md#getting-started) pour vous aider à commencer.  

Toutefois, si vous ne souhaitez plus utiliser une bibliothèque dans votre application de page unique et envoyer des messages de protocole vous-même, suivez les étapes générales ci-dessous.

> [AZURE.NOTE]
    Pas tous les scénarios Azure Active Directory et fonctionnalités sont prises en charge par le point de terminaison version 2.0.  Pour déterminer si vous devez utiliser le point de terminaison version 2.0, lisez les [limitations de la version 2.0](active-directory-v2-limitations.md).
    
## <a name="protocol-diagram"></a>Diagramme de protocole
Dans le flux de la connexion implicite entière ressemble à ceci : chacune des étapes sont décrites en détail ci-dessous.

![OpenId connecter couloirs](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-the-sign-in-request"></a>Envoyer la demande de connexion

Pour connecter initialement l’utilisateur à votre application, vous pouvez envoyer une demande d’autorisation [OpenID se connecter](active-directory-v2-protocols-oidc.md) et obtenir un `id_token` à partir de la version 2.0 :

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token+token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&response_mode=fragment
&state=12345
&nonce=678910
```

> [AZURE.TIP] Cliquez sur le lien ci-dessous pour exécuter cette requête ! Après la connexion, votre navigateur doit être redirigé vers `https://localhost/myapp/` avec un `id_token` dans la barre d’adresses.
    <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token+token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/Common/oauth2/v2.0/Authorize...</a>

| Paramètre | | Description |
| ----------------------- | ------------------------------- | --------------- |
| client | Obligatoire | La `{tenant}` valeur dans le chemin d’accès de la requête peut être utilisée pour contrôler qui peut vous connecter à l’application.  Les valeurs autorisées sont `common`, `organizations`, `consumers`et identificateurs du client.  Pour plus d’informations, voir [Concepts de protocole](active-directory-v2-protocols.md#endpoints). |
| identifiant_client | Obligatoire | L’Id de l’Application que le portail d’inscription ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) affecté à votre application. |
| response_type | Obligatoire | Doit inclure `id_token` connexion OpenID se connecter.  Il peut également inclure la response_type `token`. À l’aide de `token` ici permettra à votre application de recevoir immédiatement un jeton d’accès du point de terminaison Autoriser sans avoir à effectuer une deuxième demande au point de terminaison autoriser.  Si vous utilisez la `token` response_type, le `scope` paramètre doit contenir une étendue indiquant les ressources pour le jeton pour des problèmes. |
| redirect_uri | recommandé | Redirect_uri de votre application, où les réponses d’authentification peuvent être envoyés et reçus par votre application.  Il doit correspondre exactement à un de la redirect_uris que vous avez enregistré dans le portail, mais il doit être encoder au format url. |
| étendue | Obligatoire | Liste des étendues séparés par des espaces.  OpenID de connexion, elle doit inclure l’étendue `openid`, ce qui correspond à l’autorisation « Pouvons pas vous connecter » dans l’interface utilisateur de consentement.  Vous pouvez également vous pouvez également inclure le `email` ou `profile` [étendues](active-directory-v2-scopes.md) pour accéder aux données utilisateur supplémentaires.  Vous pouvez également inclure des autres étendues dans cette requête pour demander une consentement à diverses ressources.  |
| response_mode | recommandé | Spécifie la méthode qui doit être utilisée pour envoyer l’arrière jeton qui en résulte dans votre application.  Doit être `fragment` pour le flux implicite.  |
| état | recommandé | Une valeur incluse dans la requête qui est également retournée dans la réponse jeton.  Il peut être une chaîne de n’importe quel contenu que vous souhaitez.  Une valeur unique générée de manière aléatoire est généralement utilisée pour [protéger contre les attaques de falsification intersites demande](http://tools.ietf.org/html/rfc6749#section-10.12).  L’état est également utilisé pour coder les informations sur l’état de l’utilisateur dans l’application avant la demande d’authentification s’est produite, telles que la page ou la vue qu’ils ont été sur. |
| valeur à usage unique | Obligatoire | Une valeur est incluse dans la demande, générée par l’application, qui sera incluse dans l’id_token résultante comme une demande de remboursement.  L’application peut vérifier ensuite cette valeur pour atténuer les attaques de relecture jeton.  La valeur est généralement une chaîne aléatoire unique qui peut être utilisée pour identifier l’origine de la demande.  |
| invite de commandes | facultatif | Indique le type d’interaction de l’utilisateur qui est requis.  Les valeurs valides uniquement pour le moment sont « login », « aucun » et « acceptez ».  `prompt=login`force l’utilisateur à entrer ses informations d’identification sur cette demande, une inversion connexion unique.  `prompt=none`est l’inverse - elle veille à ce que l’utilisateur n’est pas présenté avec n’importe quelle invite interactive que ce soit.  Si la demande ne peut pas être effectuée en mode silencieux via l’authentification unique sur, le point de terminaison version 2.0 renverra une erreur.  `prompt=consent`se déclenche la boîte de dialogue consentement OAuth une fois que l’utilisateur se connecte, demandant à l’utilisateur pour accorder des autorisations à l’application. |
| login_hint | facultatif | Peut servir à remplir au préalable le champ adresse de messagerie/nom d’utilisateur de la page pour l’utilisateur, se connecter si vous connaissez son nom d’utilisateur à l’avance.  Applications souvent utilisent ce paramètre au cours de ré-authentification, après avoir déjà extrait le nom d’utilisateur à partir d’un précédent se connecter à l’aide de la `preferred_username` réclamer. |
| domain_hint | facultatif | Peut s’agir de `consumers` ou `organizations`.  Si inclus, il ignore le processus de découverte des e-mails cet utilisateur parcourt dans la version 2.0 page se connecter, et causer une expérience utilisateur légèrement plus agréable.  Applications souvent utilisent ce paramètre au cours de ré-authentification, en extrayant le `tid` réclamer à partir de l’id_token.  Si la `tid` réclamer la valeur est `9188040d-6c67-4c5b-b112-36a304b66dad`, vous devez utiliser `domain_hint=consumers`.  Dans le cas contraire, utilisez `domain_hint=organizations`. |

À ce stade, l’utilisateur est invité à entrer ses informations d’identification et terminer l’authentification.  Le point de terminaison version 2.0 permet également de garantir que l’utilisateur a accepté pour les autorisations indiquées dans le `scope` paramètre de requête.  Si l’utilisateur n’a pas accepté à certaines de ces autorisations, il demande à l’utilisateur à donner son consentement pour les autorisations requises.  Détails des [autorisations, accord et applications clients multiples sont fournies ici](active-directory-v2-scopes.md).

Une fois que l’utilisateur s’authentifie et consentement, le point de terminaison version 2.0 renverra une réponse à votre application en l’indiquée `redirect_uri`, à l’aide de la méthode spécifiée dans le `response_mode` paramètre.

#### <a name="successful-response"></a>Réponse correcte

Une réponse correcte à l’aide `response_mode=fragment` et `response_type=id_token+token` se présente comme suit, avec des sauts de ligne pour le rendre plus lisibles :

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fmail.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| access_token | Incluse en cas `response_type` inclut `token`. Le jeton d’accès que l’application demandée, dans ce cas pour le graphique Microsoft.  Le jeton d’accès ne doit pas être décoder ou inspecté dans le cas contraire, il peut être considéré comme une chaîne opaque. |
| token_type | Incluse en cas `response_type` inclut `token`.  Sera toujours `Bearer`. |
| expires_in | Incluse en cas `response_type` inclut `token`.  Indique le nombre de secondes que le jeton est valide, à des fins de mise en cache. |
| étendue | Incluse en cas `response_type` inclut `token`.  Indique l’ou les étendues pour laquelle l’access_token sera valide. |
| id_token | Id_token qui a demandé l’application. Vous pouvez utiliser l’id_token pour vérifier l’identité de l’utilisateur et commencer une session avec l’utilisateur.  Obtenir plus d’informations sur id_tokens et leur contenu est inclus dans la [version 2.0 référence de jeton de point de terminaison](active-directory-v2-tokens.md).  |
| état | Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état dans la demande et réponse sont identiques. |


#### <a name="error-response"></a>Réponse d’erreur
Réponses d’erreur peuvent également être envoyées à la `redirect_uri` afin que l’application peut gérer les correctement :

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| erreur | Une chaîne de code d’erreur qui peut être utilisée pour classer les types d’erreurs qui se produisent et peut être utilisée pour réagir aux erreurs. |
| error_description | Un message d’erreur spécifique qui peut vous aider à un développeur identifier la cause d’une erreur d’authentification.  |

## <a name="validate-the-idtoken"></a>Valider l’id_token
Uniquement recevoir un id_token n’est pas suffisant pour l’authentification de l’utilisateur ; Vous devez valider la signature d’id_token et vérifier les revendications dans le jeton par la configuration requise de votre application.  Le point de terminaison version 2.0 utilise [JSON Web jetons (JWTs)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) et chiffrement de clé publique pour signer jetons et vérifier qu’ils sont valides.

Vous pouvez choisir de valider le `id_token` dans le client code, mais une pratique courante consiste à envoyer le `id_token` à un serveur principal et effectuer la validation il.  Une fois que vous avez validé la signature de l’id_token, il existe quelques revendications que vous devrez vérifier.  Voir la [référence de jeton de version 2.0](active-directory-v2-tokens.md) pour plus d’informations, y compris les [Jetons de validation](active-directory-v2-tokens.md#validating-tokens) et [Importantes d’informations sur la signature clé survol](active-directory-v2-tokens.md#validating-tokens).  Nous vous recommandons recourant à une bibliothèque pour l’analyse et la validation des jetons - au moins une est disponible pour la plupart des langues et plateformes.
<!--TODO: Improve the information on this-->

Vous souhaiterez peut-être également valider revendications supplémentaires en fonction de votre scénario.  Certains contrôles courants sont les suivants :

- Garantir l’utilisateur/organisation s’est inscrite pour l’application.
- Garantir l’utilisateur a l’autorisation/privilèges
- Assurer une certaine force d’authentification s’est produite, telles que l’authentification multifacteur.

Pour plus d’informations sur les revendications dans un id_token, voir la [référence de jeton de point de terminaison de version 2.0](active-directory-v2-tokens.md).

Une fois que vous avez complètement validé l’id_token, vous pouvez commencer une session avec l’utilisateur et utiliser les revendications dans l’id_token pour obtenir des informations sur l’utilisateur dans votre application.  Ces informations peuvent être utilisées pour affichage, les enregistrements, les autorisations, etc..

## <a name="get-access-tokens"></a>Obtenir les jetons d’accès

À présent que vous avez connecté l’utilisateur à votre application seule page, vous pouvez obtenir des jetons d’accès pour web appel API sécurisé par Azure AD, tels que le [Microsoft Graph](https://graph.microsoft.io).  Même si vous avez déjà reçu un jeton à l’aide du `token` response_type, vous pouvez utiliser cette méthode pour acquérir des jetons vers des ressources supplémentaires sans avoir à rediriger l’utilisateur pour vous connecter à nouveau.

Dans le flux OpenID se connecter/OAuth normal, vous pour ce faire, effectuer une requête vers la version 2.0 `/token` point de terminaison.  Toutefois, le point de terminaison version 2.0 ne reconnaît pas les demandes CORS, afin de passer des appels AJAX d’obtenir et d’actualiser les jetons n’appartient pas la question.  À la place, vous pouvez utiliser le flux implicite dans un iframe masqué pour obtenir des nouveaux jetons pour d’autres API web : 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

> [AZURE.TIP] Essayez de copier et coller la demande dans un onglet du navigateur ci-dessous ! (N’oubliez pas de remplacer les `domain_hint` et la `login_hint` valeurs avec les valeurs correctes pour votre utilisateur)

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&domain_hint={{consumers-or-organizations}}&login_hint={{your-username}}
```

| Paramètre | | Description |
| ----------------------- | ------------------------------- | --------------- |
| client | Obligatoire | La `{tenant}` valeur dans le chemin d’accès de la requête peut être utilisée pour contrôler qui peut vous connecter à l’application.  Les valeurs autorisées sont `common`, `organizations`, `consumers`et identificateurs du client.  Pour plus d’informations, voir [Concepts de protocole](active-directory-v2-protocols.md#endpoints). |
| identifiant_client | Obligatoire | L’Id de l’Application que le portail d’inscription ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) affecté à votre application. |
| response_type | Obligatoire | Doit inclure `id_token` connexion OpenID se connecter.  Il peut également inclure d’autres response_types, tel que `code`. |
| redirect_uri | recommandé | Redirect_uri de votre application, où les réponses d’authentification peuvent être envoyés et reçus par votre application.  Il doit correspondre exactement à un de la redirect_uris que vous avez enregistré dans le portail, mais il doit être encoder au format url. |
| étendue | Obligatoire | Liste des étendues séparés par des espaces.  Pour les jetons de mise en route, incluez toutes les [étendues](active-directory-v2-scopes.md) , vous avez besoin pour la ressource d’intérêt.  |
| response_mode | recommandé | Spécifie la méthode qui doit être utilisée pour envoyer l’arrière jeton qui en résulte dans votre application.  Peut s’agir de `query`, `form_post`, ou `fragment`.  |
| état | recommandé | Une valeur incluse dans la requête qui est également retournée dans la réponse jeton.  Il peut être une chaîne de n’importe quel contenu que vous souhaitez.  Une valeur unique générée de manière aléatoire est généralement utilisée pour protéger contre les attaques de falsification intersites demande.  L’état est également utilisé pour coder les informations sur l’état de l’utilisateur dans l’application avant la demande d’authentification s’est produite, telles que la page ou la vue qu’ils ont été sur. |
| valeur à usage unique | Obligatoire | Une valeur est incluse dans la demande, générée par l’application, qui sera incluse dans l’id_token résultante comme une demande de remboursement.  L’application peut vérifier ensuite cette valeur pour atténuer les attaques de relecture jeton.  La valeur est généralement une chaîne aléatoire unique qui peut être utilisée pour identifier l’origine de la demande.  |
| invite de commandes | Obligatoire | Pour l’actualisation et prise jetons dans un iframe masqué, vous devez utiliser `prompt=none` pour vous assurer que l’iframe ne se bloque pas dans la version 2.0 page se connecter et retourne immédiatement. |
| login_hint | Obligatoire | Pour l’actualisation et prise jetons dans un iframe masqué, vous devez inclure le nom d’utilisateur de l’utilisateur dans cette information afin de faire la distinction entre plusieurs sessions de que l’utilisateur peut avoir à un moment donné dans le temps. Vous pouvez extraire le nom d’utilisateur d’un précédent se connecter à l’aide de la `preferred_username` revendiquer. |
| domain_hint | Obligatoire | Peut s’agir de `consumers` ou `organizations`.  Pour l’actualisation et prise jetons dans un iframe masqué, vous devez inclure la domain_hint dans la demande.  Vous devriez extraire le `tid` réclamer à partir de l’id_token d’une connexion précédente pour déterminer la valeur à utiliser.  Si la `tid` réclamer la valeur est `9188040d-6c67-4c5b-b112-36a304b66dad`, vous devez utiliser `domain_hint=consumers`.  Dans le cas contraire, utilisez `domain_hint=organizations`. |

Grâce à la `prompt=none` paramètre, cette demande soit réussir ou échoue immédiatement et revenir à votre application.  Une réponse correcte est communiquée à votre application en l’indiquée `redirect_uri`, à l’aide de la méthode spécifiée dans le `response_mode` paramètre.

#### <a name="successful-response"></a>Réponse correcte
Une réponse correcte à l’aide `response_mode=fragment` ressemble à :

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| access_token | Le jeton de l’application demandée. |
| token_type | Sera toujours `Bearer`. |
| état | Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état dans la demande et réponse sont identiques. |
| expires_in | Combien de temps le jeton d’accès est valide (en secondes). |
| étendue | Les étendues le jeton d’accès valide pour. |

#### <a name="error-response"></a>Réponse d’erreur
Réponses d’erreur peuvent également être envoyées à la `redirect_uri` afin que l’application peut traiter de manière appropriée.  Dans le cas de `prompt=none`, une erreur attendue sera :

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Paramètre | Description |
| ----------------------- | ------------------------------- |
| erreur | Une chaîne de code d’erreur qui peut être utilisée pour classer les types d’erreurs qui se produisent et peut être utilisée pour réagir aux erreurs. |
| error_description | Un message d’erreur spécifique qui peut vous aider à un développeur identifier la cause d’une erreur d’authentification.  |

Si vous recevez cette erreur dans la demande iframe, l’utilisateur doit interactive vous reconnecter à récupérer un nouveau jeton.  Vous pouvez choisir de gérer ce cas dans un autre moyen est préférable pour votre application.

## <a name="refreshing-tokens"></a>Actualisation des jetons

Les deux `id_token`s et `access_token`s expirera après un laps de temps, afin que votre application doit être préparée pour actualiser ces jetons régulièrement.  Pour actualiser ces deux types de jeton, vous pouvez effectuer la même demande iframe masqué dessus à l’aide de la `prompt=none` paramètre pour contrôler le comportement d’Azure AD.  Si vous souhaitez recevoir une nouvelle `id_token`, veillez à utiliser `response_type=id_token` et `scope=openid`, ainsi qu’un `nonce` paramètre.


## <a name="send-a-sign-out-request"></a>Envoyer une demande de déconnexion

La OpenIdConnect `end_session_endpoint` n’est pas actuellement pris en charge par le point de terminaison version 2.0. Cela signifie que votre application ne peut pas envoyer une demande à l’extrémité de la version 2.0 à la fin d’une session et désactivez les cookies définis par le point de terminaison version 2.0.
Pour se déconnecter d’un utilisateur, votre application peut simplement mettre fin à sa propre session avec l’utilisateur, puis quitter la session de l’utilisateur avec le point de terminaison version 2.0-intacts.  La prochaine fois que l’utilisateur essaie de se connecter, ils verront une page « Sélectionnez le compte », avec leurs comptes activement connecté dans répertoriés.
Dans la page, l’utilisateur peut choisir pour se déconnecter de n’importe quel compte mettre fin à la session avec le point de terminaison version 2.0.

<!--

When you wish to sign the user out of the  app, it is not sufficient to clear your app's cookies or otherwise end the session with the user.  You must also redirect the user to the v2.0 endpoint for sign out.  If you fail to do so, the user will be able to re-authenticate to your app without entering their credentials again, because they will have a valid single sign-on session with the v2.0 endpoint.

You can simply redirect the user to the `end_session_endpoint` listed in the OpenID Connect metadata document:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | | Description |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | recommended | The URL which the user should be redirected to after successful logout.  If not included, the user will be shown a generic message by the v2.0 endpoint.  |

-->