 <properties
   pageTitle="Référence Azure AD jeton | Microsoft Azure"
   description="Guide pour comprendre et évaluer les revendications dans les jetons SAML 2.0 et JSON Web jetons (JWT) émis par Azure Active Directory (DAS)"
   documentationCenter="na"
   authors="bryanla"
   services="active-directory"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/06/2016"
   ms.author="mbaldwin"/>

# <a name="azure-ad-token-reference"></a>Référence de jeton Azure AD

Azure Active Directory (AD Azure) émet plusieurs types de jetons de sécurité dans le traitement de chaque flux d’authentification. Ce document décrit le format, caractéristiques de sécurité et le contenu de chaque type de jeton.

## <a name="types-of-tokens"></a>Types de jetons

Azure AD prend en charge le [protocole d’autorisation OAuth 2.0](active-directory-protocols-oauth-code.md), qui utilise access_tokens et refresh_tokens.  Il prend également en charge l’authentification et se connecter via [OpenID se connecter](active-directory-protocols-openid-connect-code.md), qui présente un troisième type de jeton, l’id_token.  Chacun de ces jetons est représenté par un « jeton porteur ».

Un jeton porteur est un jeton de sécurité léger qui accorde l’accès « porteur » à une ressource protégée. Dans ce cas, « porteur » est une partie qui peut présenter le jeton. Bien que l’authentification avec Azure AD est nécessaire pour recevoir un jeton porteur, étapes sont nécessaires pour sécuriser le jeton, afin d’empêcher interception par un tiers involontaire. Car les jetons PORTEUR n’ont pas un mécanisme intégré pour empêcher les personnes non autorisées de leur utilisation, ils doivent acheminés dans un canal sécurisé comme TLS (HTTPS). Si un jeton porteur est transmis en clair, un homme-attaque par interception peut servir à acquérir le jeton et obtenir un accès non autorisé à une ressource protégée. Les mêmes principes de sécurité s’appliquent lorsque vous stockez ou la mise en cache des jetons porteur pour une utilisation ultérieure. Toujours vous assurer que votre application transmet et stocke les jetons des porteur de façon sécurisée. Pour des raisons de sécurité plus sur les jetons porteur, voir [RFC 6750 Section 5](http://tools.ietf.org/html/rfc6750).

La plupart des jetons émis par Azure AD sont implémentées comme JSON Web jetons ou JWTs.  Un JWT est un moyen compact, adaptée aux URL de transfert des informations entre deux parties.  Les informations contenues dans JWTs sont appelées « revendications », ou assertions des informations sur le support et l’objet du jeton.  Les revendications dans JWTs sont des objets JSON codés et sérialisés pour la transmission.  Étant donné que les JWTs émanant d’Azure AD sont signés mais pas chiffrés, vous pouvez facilement inspecter le contenu d’un JWT pour le débogage.  Il existe plusieurs outils disponibles pour ce faire, par exemple [jwt.calebb.net](http://jwt.calebb.net). Pour plus d’informations sur JWTs, vous pouvez faire référence à la [spécification JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## <a name="idtokens"></a>Id_tokens

Id_tokens sont une forme du jeton de sécurité de connexion à votre application reçoit lors de l’authentification à l’aide de [OpenID se connecter](active-directory-protocols-openid-connect-code.md).  Ils sont représentés sous forme de [JWTs](#types-of-tokens)et contient des déclarations que vous pouvez utiliser pour la connexion de l’utilisateur à votre application.  Vous pouvez utiliser les revendications dans un id_token comme vous le souhaitez, fréquemment elles sont utilisées pour afficher les informations de compte ou prendre des décisions de contrôle d’accès dans une application.

Id_tokens sont connecté, mais pas chiffrés pour le moment.  Lorsque votre application reçoit une id_token, elle doit [valider la signature](#validating-tokens) pour prouver authenticité du jeton et valider quelques revendications du jeton de prouver sa durée de validité.  Les revendications validées par une application varient en fonction des besoins de scénario, mais il existe certaines [validations réclamer courantes](#validating-tokens) que votre application doit effectuer dans tous les scénarios.

Voir la section suivante pour plus d’informations sur les revendications id_tokens, mais aussi un exemple id_token.  Notez que les revendications dans id_tokens ne sont pas renvoyées dans un ordre en particulier.  De plus, les nouvelles demandes peuvent être introduites dans id_tokens à tout moment dans le temps, votre application doit s’arrêter pas lors de l’introduction des nouvelles demandes.  La liste suivante répertorie les revendications votre application peut interpréter fiable au moment de la rédaction de ce document.  Si nécessaire, vous trouverez davantage de détails dans la [spécification OpenID se connecter](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-idtoken"></a>Exemple id_token

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.
```

> [AZURE.TIP] Pour les exercices pratiques, essayez inspectant les revendications dans l’exemple id_token en le collant dans [calebb.net](http://jwt.calebb.net).

#### <a name="claims-in-idtokens"></a>Revendications dans id_tokens

| Réclamer JWT | Nom | Description |
|-----------|------|-------------|
| `appid`| ID de l’application | Identifie l’application qui utilise le jeton d’accès à une ressource. L’application peut agir en tant que tel ou au nom d’un utilisateur. En règle générale, l’ID de l’application représente un objet application, mais il peut également représenter un objet principal service dans Azure Active Directory. <br><br> **Exemple JWT valeur**: <br> `"appid":"15CB020F-3984-482A-864D-1D92265E8268"` |
| `aud`| Audience | Le destinataire prévu du jeton. L’application qui reçoit le jeton doit vérifier que la valeur d’audience est correcte et refuser des jetons destinés à une audience différente. <br><br> **Exemple de valeur SAML**: <br> `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>` <br><br> **Exemple de valeur JWT**: <br> `"aud":"https://contoso.com"` |
| `appidacr`| Application d’authentification contexte classe référence | Indique comment le client a été authentifié. Pour un client public, la valeur est égal à 0. Si l’ID de client et le code secret client sont utilisés, la valeur est 1. <br><br> **Exemple JWT valeur**: <br> `"appidacr": "0"`|
| `acr`| Référence de classe contexte d’authentification | Indique la manière dont l’objet a été authentifié, plutôt que d’utiliser le client dans la demande d’Application d’authentification contexte classe référence. La valeur « 0 » indique l’authentification de l’utilisateur final répond pas aux exigences de ISO/IEC 29115. <br><br> **Exemple de valeur JWT**: <br> `"acr": "0"`|
| | Authentification instantanée | Enregistre la date et l’heure de l’authentification. <br><br> **Exemple de valeur SAML**: <br> `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` |
| `amr`| Méthode d’authentification | Identifie la manière dont l’objet du jeton a été authentifié. <br><br> **Exemple de valeur SAML**: <br> `<AuthnContextClassRef>`<br>`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` <br><br> **Exemple de valeur JWT**:`“amr”: ["pwd"]` |
| `given_name`| Prénom | Fournit la première ou » « nom de l’utilisateur, comme définis dans l’objet utilisateur Azure AD. <br><br> **Exemple de valeur SAML**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”>`<br>`<AttributeValue>Frank<AttributeValue>` <br><br> **Exemple de valeur JWT**: <br> `"given_name": "Frank"` |
| `groups`| Groupes | Fournit les ID d’objet qui représentent des appartenances à un groupe de l’objet. Ces valeurs sont unique (voir ID d’objet) et peuvent être utilisés en toute sécurité pour gérer l’accès, telles que l’application d’autorisation pour accéder à une ressource. Les groupes inclus dans la demande de groupes sont configurés sur une base de l’application, via la propriété « groupMembershipClaims » du manifeste d’application. Une valeur null exclut tous les groupes, la valeur « SecurityGroup » inclura seules les appartenances à un groupe de sécurité Active Directory et la valeur « Tout » se comprennent les groupes de sécurité et les listes de Distribution Office 365. <br><br> **Exemple de valeur SAML**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>` <br><br> **Exemple de valeur JWT**: <br> `“groups”: ["0e129f5b-6b0a-4944-982d-f776045632af", … ]` |
| `idp` | Fournisseur d’identité | Enregistre le fournisseur d’identité authentifiés l’objet du jeton. Cette valeur est identique à la valeur de la déclaration de l’émetteur, à moins que le compte d’utilisateur se trouve dans un autre client à l’émetteur. <br><br> **Exemple de valeur SAML**: <br> `<Attribute Name=” http://schemas.microsoft.com/identity/claims/identityprovider”>`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>` <br><br> **Exemple de valeur JWT**: <br> `"idp":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `iat` | IssuedAt | Contient la date à laquelle le jeton a été émis. Il est souvent utilisé pour l’actualisation jeton de mesure. <br><br> **Exemple de valeur SAML**: <br> `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` <br><br> **Exemple JWT valeur**: <br> `"iat": 1390234181` |
| `iss` | Émetteur | Identifie le service jeton de sécurité (STS) qui crée et retourne le jeton. Dans les jetons renvoyant Azure AD, l’émetteur est sts.windows.net. Le GUID dans la valeur de réclamer émetteur est l’ID de client de l’annuaire Active Directory Azure. L’ID de client est un identificateur immuable et fiable du répertoire. <br><br> **Exemple de valeur SAML**: <br> `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` <br><br> **Exemple de valeur JWT**: <br>  `"iss":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `family_name` | Nom de famille | Fournit la dernière nom, prénom ou nom de famille de l’utilisateur, telle que définie dans l’objet utilisateur Azure AD. <br><br> **Exemple de valeur SAML**: <br> `<Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”>`<br>`<AttributeValue>Miller<AttributeValue>` <br><br> **Exemple de valeur JWT**: <br> `"family_name": "Miller"` |
| `unique_name`| Nom | Fournit une valeur lisible humaine qui identifie l’objet du jeton. Cette valeur n’est pas garantie pour être unique au sein d’un client et est conçue pour être utilisé uniquement à des fins d’affichage. <br><br> **Exemple de valeur SAML**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”>`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>` <br><br> **Exemple de valeur JWT**: <br> `"unique_name": "frankm@contoso.com"` |
| `oid` | ID d’objet | Contient un identificateur unique d’un objet dans Azure Active Directory. Cette valeur est immuable et ne peut pas être réaffectée ou réutilisée. Utiliser l’ID d’objet pour identifier un objet dans les requêtes à Azure Active Directory. <br><br> **Exemple de valeur SAML**: <br> `<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>` <br><br> **Exemple de valeur JWT**: <br> `"oid":"528b2ac2-aa9c-45e1-88d4-959b53bc7dd0"` |
| `roles` | Rôles | Représente tous les rôles d’application qui a été accordé à la fois directement ou indirectement via l’appartenance aux groupes de l’objet et peut être utilisé pour appliquer un contrôle d’accès basé sur un rôle. Rôles d’application sont définis sur une base de l’application, par le biais du `appRoles` propriété du manifeste d’application. La `value` propriété de chaque rôle d’application est la valeur figurant dans la demande de rôles. <br><br> **Exemple de valeur SAML**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">`<br>`<AttributeValue>Admin</AttributeValue>` <br><br> **Exemple de valeur JWT**: <br> `“roles”: ["Admin", … ]` |
| `scp` | Étendue | Indique les autorisations d’emprunt d’identité accordées à l’application cliente. L’autorisation par défaut est `user_impersonation`. Le propriétaire de la ressource sécurisée peut enregistrer des valeurs supplémentaires dans Azure AD. <br><br> **Exemple de valeur JWT**: <br> `"scp": "user_impersonation"`|
| `sub` |Objet| Identifie l’utilisateur principal sur lequel le jeton indique les informations, telles que l’utilisateur d’une application. Cette valeur est immuable et ne peut pas être réaffectée ou réutilisé, donc il peut être utilisé pour la vérification d’autorisation en toute sécurité. L’objet est toujours présent dans les jetons les problèmes d’AD Azure, il est recommandé à l’aide de cette valeur dans un système d’autorisation objectif général. <br> `SubjectConfirmation`n’est pas une demande de remboursement. Il décrit comment l’objet du jeton est vérifié. `Bearer`Indique que l’objet est confirmé par leur possession du jeton. <br><br> **Exemple de valeur SAML**: <br> `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>` <br><br> **Exemple de valeur JWT**: <br> `"sub":"92d0312b-26b9-4887-a338-7b00fb3c5eab"`|
| `tid` | ID de client | Un identificateur immuable, non réutilisable qui identifie le client répertoire qui a émis le jeton. Vous pouvez utiliser cette valeur pour accéder aux ressources répertoire client spécifique dans une application cliente multiples. Par exemple, vous pouvez utiliser cette valeur pour identifier le client dans un appel à l’API de graphique. <br><br> **Exemple de valeur SAML**: <br> `<Attribute Name=”http://schemas.microsoft.com/identity/claims/tenantid”>`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>` <br><br> **Exemple de valeur JWT**: <br> `"tid":"cbb1a5ac-f33b-45fa-9bf5-f37db0fed422"`|
| `nbf`, `exp`|Durée de vie des jeton | Définit l’intervalle de temps dans lequel un jeton est valide. Le service qui valide le jeton doit vérifier que la date actuelle est comprise dans la jeton durée de vie, autre qu’il est conseillé de refuser le jeton. Le service peut-être permettre entre Azure AD cinq minutes au-delà de la plage de durée de vie des jetons pour prendre en compte des différences de l’horloge (« décalage horaire ») et le service. <br><br> **Exemple de valeur SAML**: <br> `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br><br> **Exemple de valeur JWT**: <br> `"nbf":1363289634, "exp":1363293234` |
| `upn`| Nom d’utilisateur Principal | Enregistre le nom d’utilisateur de l’utilisateur principal.<br><br> **Exemple de valeur JWT**: <br> `"upn": frankm@contoso.com`|
| `ver`| Version | Enregistre le numéro de version du jeton. <br><br> **Exemple de valeur JWT**: <br> `"ver": "1.0"`|

## <a name="access-tokens"></a>Jetons d’accès

Les jetons d’accès ne sont à ce stade consommables par les Services Microsoft.  Vos applications ne devez pas effectuer de validation ni inspection des jetons d’accès pour tous les scénarios pris en charge actuellement.  Vous pouvez considérer les jetons d’accès comme complètement opaque - ils sont seulement les chaînes votre application pouvant passer à Microsoft dans les requêtes HTTP.

Lorsque vous demandez un jeton d’accès, Azure AD renvoie également des métadonnées sur le jeton d’accès à la consommation de votre application.  Ces informations comprennent le délai d’expiration du jeton d’accès et les étendues pour laquelle il est valide.  Cela permet à votre application pour effectuer la mise en cache intelligente des jetons d’accès sans avoir à analyser ouvrir le jeton d’accès.

## <a name="refresh-tokens"></a>Actualiser les jetons

Actualiser les jetons sont des jetons de sécurité, votre application peut utiliser pour acquérir les nouveaux jetons d’accès dans un flux OAuth 2.0.  Il permet à votre application obtenir l’accès à long terme aux ressources au nom d’un utilisateur sans interaction par l’utilisateur.

Actualisation jetons sont ressources multiples, ce qui signifie qu’ils peuvent être reçus au cours d’une demande de jeton pour une ressource mais perdus pour les jetons d’accès à une ressource complètement différente. Pour spécifier les ressources multiples, définissez la `resource` paramètre dans la demande pour la ressource cible.

Actualisation des jetons sont complètement opaques à votre application. Ils sont à long terme, mais votre application ne doit pas être écrites va-t-il se passer qu’un jeton actualisation prendra pour une période donnée.  Jetons d’actualisation peuvent être non valide à tout moment pour diverses raisons.  Le seul moyen pour votre application de savoir si un jeton actualisation est valide consiste à essayer échanger à l’aide d’une demande de jeton au point de terminaison jeton Azure AD.

Lorsque vous échanger un jeton d’actualisation pour un nouveau jeton d’accès, vous recevrez un nouveau jeton d’actualisation dans la réponse jeton.  Vous devez enregistrer le jeton actualisation nouvellement émis, remplaçant celle utilisée dans la demande.  Ceci garantit que vos jetons actualisation restent valides pour autant que possible.

## <a name="validating-tokens"></a>Validation des jetons

À ce stade, la validation uniquement jeton que votre application client ne devez effectuer validation id_tokens.  Pour valider un id_token, votre application doit valider signature d’id_token et les revendications dans l’id_token.

Nous fournissons des bibliothèques et des exemples de code qui montrent comment gérer facilement la validation jeton, si vous le souhaitez comprendre le processus sous-jacente.  Plusieurs bibliothèques libres tiers sont également disponibles pour la validation des JWT, au moins une option pour presque chaque plate-forme et langue. Pour plus d’informations sur les exemples de code et les bibliothèques d’authentification Azure AD, voir [bibliothèques d’authentification Azure AD](active-directory-authentication-libraries.md).

#### <a name="validating-the-signature"></a>Validation de la signature

Un JWT contient trois segments, qui sont séparées par le `.` caractère.  Le premier segment est connu sous l' **en-tête**, le second dans le **corps**et la troisième comme la **signature**.  Le segment signature peut être utilisé pour valider l’authenticité de l’id_token afin qu’il peut être approuvé par votre application.

Id_Tokens êtes connecté à l’aide des algorithmes de chiffrement asymétriques standard, par exemple 256 RSA. L’en-tête de l’id_token contient des informations sur la méthode de clé et de chiffrement utilisée pour signer le jeton :

```
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "kriMPdmBvx68skT8-mPAB3BseeA"
}
```

La `alg` réclamer indique l’algorithme qui a été utilisé pour signer le jeton, alors que la `x5t` réclamer indique la clé publique particulier qui a été utilisée pour signer le jeton.

À un moment donné dans le temps, Azure AD peut-être signer un id_token à l’aide de l’une d’un certain ensemble de paires de clés publique-privée. Azure AD fait pivoter l’ensemble des clés de façon périodique, possible afin que votre application doit être écrits pour gérer ces modifications clées automatiquement.  Une fréquence raisonnablement pour vérifier les mises à jour aux clés publiques utilisées par Azure AD est de 24 heures.

Vous pouvez acquérir les données clées signature nécessaires pour valider la signature à l’aide du document de métadonnées OpenID connecter situé à :

```
https://login.microsoftonline.com/common/.well-known/openid-configuration
```

> [AZURE.TIP] Essayez cette URL dans un navigateur !

Ce document métadonnées est un objet JSON contenant plusieurs éléments utiles d’information, telles que l’emplacement de différents points de terminaison requises pour l’authentification OpenID se connecter.  

Il inclut également un `jwks_uri`, ce qui donne l’emplacement de l’ensemble des clés publiques utilisé pour la signature de jetons.  Le document JSON situé à la `jwks_uri` contient toutes les informations de clé publique en cours d’utilisation à ce moment particulier.  Votre application peut utiliser les `kid` réclamer dans l’en-tête JWT pour sélectionner la clé publique dans ce document a été utilisé pour signer un jeton donné.  Il peut ensuite effectuer la validation des signatures à l’aide de la clé publique appropriée et l’algorithme indiquée.

Validation de signature est en dehors de l’étendue de ce document - nombreuses bibliothèques libres sont disponibles pour vous aider à faire si nécessaire.

#### <a name="validating-the-claims"></a>Valider les revendications

Lorsque votre application reçoit une id_token lors de la connexion utilisateur, il doit également effectuer quelques vérifications la réclamation dans l’id_token.  Ces incluent, mais ne sont pas limités à :

  - Réclamer de **l’Audience** - pour vérifier que l’id_token a été destinés à donner à votre application.
  - Revendications **Pas avant** et **Délai d’Expiration** - pour vérifier que l’id_token n’a pas expiré.
  - La demande de **l’émetteur** - pour vérifier que le jeton a été émis bien dans votre application par Azure Active Directory.
  - La **valeur à usage unique** - atténuer une jeton attaque.
  - et bien plus encore...

Pour une liste complète des validations réclamer que votre application doit effectuer, reportez-vous à la [spécification OpenID se connecter](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation).

Détails des valeurs prévues pour ces revendications sont inclus dans la section [id_token section](#id-tokens) précédente.

## <a name="sample-tokens"></a>Exemple jetons

Cette section affiche des exemples de jetons SAML et JWT renvoyant Azure AD. Ces exemples vous permettent de voir les revendications dans le contexte.
Jeton SAML

Il s’agit d’un échantillon d’un jeton SAML classique.

    <?xml version="1.0" encoding="UTF-8"?>
    <t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
      <t:Lifetime>
        <wsu:Created xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
        <wsu:Expires xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
      </t:Lifetime>
      <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
        <EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
          <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
        </EndpointReference>
      </wsp:AppliesTo>
      <t:RequestedSecurityToken>
        <Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
          <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
          <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
            <ds:SignedInfo>
              <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
              <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
              <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
                <ds:Transforms>
                  <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
                  <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
                </ds:Transforms>
                <ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha256" />
                <ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
              </ds:Reference>
            </ds:SignedInfo>
            <ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
            <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
              <X509Data>
                <X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
              </X509Data>
            </KeyInfo>
          </ds:Signature>
          <Subject>
            <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
            <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
          </Subject>
          <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
            <AudienceRestriction>
              <Audience>https://contoso.onmicrosoft.com/MyWebApp</Audience>
            </AudienceRestriction>
          </Conditions>
          <AttributeStatement>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
              <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">
              <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
              <AttributeValue>sample.admin@contoso.onmicrosoft.com</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
              <AttributeValue>Admin</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
              <AttributeValue>Sample</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
              <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
              <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
              <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
              <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
              <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
              <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
              <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
              <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
              <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
              <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
              <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
              <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
              <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/identityprovider">
              <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
            </Attribute>
          </AttributeStatement>
          <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
            <AuthnContext>
              <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
            </AuthnContext>
          </AuthnStatement>
        </Assertion>
      </t:RequestedSecurityToken>
      <t:RequestedAttachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedAttachedReference>
      <t:RequestedUnattachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedUnattachedReference>
      <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
      <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
      <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
    </t:RequestSecurityTokenResponse>

### <a name="jwt-token---user-impersonation"></a>JWT - jeton d’emprunt d’identité utilisateur

Il s’agit d’un échantillon d’un type jeton de web JSON (JWT) utilisé dans un flux d’accorder l’autorisation code.
En plus de revendications, le jeton comprend un numéro de version dans **veur** et **appidacr**, la référence de classe de contexte d’authentification, qui indique comment le client a été authentifié. Pour un client public, la valeur est égal à 0. Si un ID client ou secret client a été utilisée, la valeur est 1.

    {
     typ: "JWT",
     alg: "RS256",
     x5t: "kriMPdmBvx68skT8-mPAB3BseeA"
    }.
    {
     aud: "https://contoso.onmicrosoft.com/scratchservice",
     iss: "https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/",
     iat: 1416968588,
     nbf: 1416968588,
     exp: 1416972488,
     ver: "1.0",
     tid: "b9411234-09af-49c2-b0c3-653adc1f376e",
     amr: [
      "pwd"
     ],
     roles: [
      "Admin"
     ],
     oid: "6526e123-0ff9-4fec-ae64-a8d5a77cf287",
     upn: "sample.user@contoso.onmicrosoft.com",
     unique_name: "sample.user@contoso.onmicrosoft.com",
     sub: "yf8C5e_VRkR1egGxJSDt5_olDFay6L5ilBA81hZhQEI",
     family_name: "User",
     given_name: "Sample",
     groups: [
      "0e129f6b-6b0a-4944-982d-f776000632af",
      "323b13b3-1851-4b94-947f-9a4dacb595f4",
      "6e32c250-9b0a-4491-b429-6c60d2ca9a42",
      "f3a161a7-9a58-4e8f-9d47-b70022a07424",
      "8d4c81b2-b1ad-476d-9574-544d155aa6ff",
      "1bf80164-ff24-4866-b19c-6212e5b9a847",
      "76f80127-f2cd-46f4-8c52-8edd8bc749b1",
      "0ba27160-44d0-42b5-b90c-47b3fcc48e35"
     ],
     appid: "b075ddef-0efa-123b-997b-de1337c29185",
     appidacr: "1",
     scp: "user_impersonation",
     acr: "1"
    }.

## <a name="related-content"></a>Contenu associé
- Voir les Azure AD Graph [opérations de stratégie](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) et l' [entité de stratégie](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity), pour en savoir plus sur la gestion des stratégies de durée de vie des jetons via l’API Azure AD Graph.
- Pour plus d’informations et des exemples sur la gestion des stratégies via applets de commande PowerShell, notamment les exemples, voir [durée de vie des jetons Configurable dans Azure Active Directory](active-directory-configurable-token-lifetimes.md). 
