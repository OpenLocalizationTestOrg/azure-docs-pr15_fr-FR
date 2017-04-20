<properties
    pageTitle="Types de point de terminaison de la version 2.0 | Microsoft Azure"
    description="Les types d’applications et de scénarios pris en charge par le point de terminaison Azure AD version 2.0."
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

# <a name="types-of-apps-for-the-v20-endpoint"></a>Types d’applications pour le point de terminaison version 2.0
Point de terminaison de la version 2.0 prend en charge l’authentification pour divers architectures application moderne, ce qui sont basé sur les protocoles standard [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) et/ou [OpenID se connecter](active-directory-v2-protocols.md#openid-connect-sign-in-flow).  Ce document décrit brièvement les types d’applications, que vous pouvez créer, indépendamment de la langue ou la plateforme que vous préférez.  Il vous aidera à comprendre les scénarios haut niveau avant de vous [lancer dans le code](active-directory-appmodel-v2-overview.md#getting-started).

> [AZURE.NOTE]
    Pas tous les scénarios Azure Active Directory et fonctionnalités sont prises en charge par le point de terminaison version 2.0.  Pour déterminer si vous devez utiliser le point de terminaison version 2.0, lisez les [limitations de la version 2.0](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Concepts de base
Chaque application qui utilise le point de terminaison version 2.0 doit être enregistré à [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).  Le processus d’inscription de l’application sera collecter et affecter des valeurs dans votre application :

- Un **Id de l’Application** qui identifie votre application
- **Rediriger URI** qui peut être utilisé pour diriger les réponses revenir à votre application
- Quelques autres valeurs spécifiques au scénario.  Pour plus d’informations, découvrez comment [Enregistrer une application](active-directory-v2-app-registration.md).

Une fois inscrit, l’application communique avec Azure AD en envoyant des requêtes au point de terminaison version 2.0 Azure Active Directory.  Nous fournissons des structures d’ouvrir la source et de bibliothèques qui prennent en charge des détails de ces demandes, ou vous pouvez implémenter vous-même la logique d’authentification en créant des requêtes à ces points de terminaison :

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```
<!-- TODO: Need a page for libraries to link to -->

## <a name="web-apps"></a>Applications Web
Pour les applications web (.NET, PHP, Java, Ruby, Python, nœud, etc.) qui sont accessibles via un navigateur, vous pouvez effectuer utilisateur se connecter à l’aide de [OpenID se connecter](active-directory-v2-protocols.md#openid-connect-sign-in-flow).  Dans OpenID connecter l’application web reçoit une `id_token`, un jeton de sécurité qui vérifie l’identité de l’utilisateur et fournit des informations sur l’utilisateur dans le formulaire de responsabilité :

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Vous pouvez en savoir plus sur tous les types de jetons et revendications disponibles pour une application dans la [référence de jeton de version 2.0](active-directory-v2-tokens.md).

Dans les applications de serveur web, le flux d’authentification de connexion prend ces étapes de haut niveau :

![Image de couloirs Web App](../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

La validation de l’id_token à l’aide d’une clé de signature publique reçue à partir de la version 2.0 est suffisante pour garantir l’identité de l’utilisateur et définir des cookies de session qui peuvent être utilisé pour identifier l’utilisateur sur les demandes de la page suivante.

Pour afficher ce scénario en action, essayez un des exemples de code de connexion à l’application web dans la section consacrée à la [Mise en route](active-directory-appmodel-v2-overview.md#getting-started) .

En plus de connexion simple, une application de serveur web peut-être doivent également accéder à un autre service web comme une API REST.  Dans ce cas l’application de serveur web peut participer à un flux OpenID se connecter & OAuth 2.0 combiné, l’aide du [flux de Code de l’autorisation OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow). Ce scénario est abordé ci-après dans notre [rubrique WebApp WebAPI mise en route](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## <a name="web-apis"></a>API Web
Vous pouvez utiliser le point de terminaison version 2.0 pour sécuriser les services web, tels que des API Web RESTful de votre application.  Au lieu de cookies id_tokens et session, API Web utiliser access_tokens OAuth 2.0 pour sécuriser leurs données et authentifier les demandes entrantes.  L’appelant d’une API Web ajoute un access_token dans l’en-tête d’autorisation d’une requête HTTP :

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

L’API Web pouvez ensuite utiliser l’access_token pour vérifier l’identité de l’appelant de l’API et extraire des informations relatives à l’appelant de revendications qui sont encodées dans l’access_token.  Vous pouvez en savoir plus sur tous les types de jetons et revendications disponibles pour une application dans la [référence de jeton de version 2.0](active-directory-v2-tokens.md).

Une API Web peuvent permettent aux utilisateurs de pour cesser d’utiliser-dans/annulations de certaines fonctionnalités ou de données en exposant les autorisations, également appelées [étendues](active-directory-v2-scopes.md).  Pour une application appelante acquérir d’autorisation à une étendue, l’utilisateur doit consentement pour l’étendue au cours d’un flux.  Le point de terminaison version 2.0 se charge de demander à l’utilisateur pour l’autorisation et l’enregistrement de ces autorisations dans tous les access_tokens qui reçoit l’API Web.  Tout ce que l’API Web doit à vous soucier valide l’access_tokens qu’il reçoit sur chaque appel et exécuter les tests de l’autorisation appropriée.

Une API Web peuvent recevoir access_tokens de tous les types d’applications, y compris les applications de serveur web, ordinateur de bureau et applications mobiles, seule page applications, processus du côté serveur et autres API Web.  Le flux de haut niveau pour l’authentification de l’api web est la suivante :

![Image de couloirs API Web](../media/active-directory-v2-flows/convergence_scenarios_webapi.png)

Pour en savoir plus sur les étapes détaillées de l’access_tokens authorization_codes et refresh_tokens, en savoir plus sur le [protocole OAuth 2.0](active-directory-v2-protocols-oauth-code.md).

Pour savoir comment sécuriser un site web api avec oauth2 n’access_tokens, consultez les exemples de code de l’api web dans notre [section mise en route](active-directory-appmodel-v2-overview.md#getting-started).


## <a name="mobile-and-native-apps"></a>Applications mobiles et natives
Applications installées sur un appareil, tels que des applications mobiles et de bureau, souvent besoin d’accéder aux services principaux ou API Web qui stockent les données et effectuer diverses tâches au nom d’un utilisateur.  Ces applications ajoutent se connecter et l’autorisation aux services et l’aide du [flux de Code de l’autorisation OAuth 2.0](active-directory-v2-protocols-oauth-code.md).  

Dans ce flux, un l’application reçoit une authorization_code à partir de la version 2.0 lors de la connexion utilisateur, qui représente l’autorisation de l’application pour appeler des services principaux au nom de l’utilisateur connecté.  L’application peut échanger puis l’authoriztion_code en arrière-plan pour un access_token OAuth 2.0 et un refresh_token.  L’application peut utiliser l’access_token s’authentifier à l’API Web dans les requêtes HTTP et peut utiliser la refresh_token pour obtenir access_tokens nouveau lors de la date d’expiration les plus anciens.

![Application native couloirs Image](../media/active-directory-v2-flows/convergence_scenarios_native.png)

## <a name="single-page-apps-javascript"></a>Applications de page unique (javascript)
Plusieurs applications modernes ont une seule Page application (SPA) frontale principalement écrite dans javascript souvent à l’aide des cadres et tels que AngularJS, Ember.js, Durandal, etc..  Le point de terminaison Azure AD version 2.0 prend en charge ces applications à l’aide du [Jeton de flux implicite 2.0](active-directory-v2-protocols-implicit.md).

Dans ce flux, l’application reçoit des jetons à partir de la version 2.0 autoriser point de terminaison directement, sans effectuer tout échange de serveur à serveur principal.  Ainsi, tous les logique d’authentification et gestion de session de prendre placent entièrement dans le client javascript, sans effectuer des redirections page supplémentaire.

![Image de couloirs flux implicite](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

Pour afficher ce scénario en action, essayez un des exemples de code application seule page dans la section consacrée à la [Mise en route](active-directory-appmodel-v2-overview.md#getting-started) .

### <a name="daemonsserver-side-apps"></a>Processus/serveur côté applications
Applications qui contiennent des processus long terme ou qui fonctionnent sans la présence d’un utilisateur doivent également un moyen d’accéder aux ressources sécurisés, tels que des API Web.  Ces applications peuvent s’authentifier et obtenir des jetons à l’aide identité de l’application (plutôt que déléguée identité d’un utilisateur) en utilisant le client OAuth 2.0 flux des informations d’identification.

Dans ce flux, l’application obtienne jetons en interaction directe avec la `/token` point de terminaison :

![Image de processus application couloirs](../media/active-directory-v2-flows/convergence_scenarios_daemon.png)

Pour créer une application de processus, consultez la documeenation d’informations d’identification client dans la section consacrée à la [Mise en route](active-directory-appmodel-v2-overview.md#getting-started) ou reportez-vous à [cette application exemple .NET](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).

## <a name="current-limitations"></a>Limitations en cours
Ces types d’applications ne sont pas pris en charge par le point de terminaison version 2.0, mais se trouvent sur la feuille de route.  Restrictions du point de terminaison version 2.0 et des limitations supplémentaires sont décrites dans l' [article de limitations version 2.0](active-directory-v2-limitations.md).

### <a name="chained-web-apis-on-behalf-of"></a>Chaîne web API (à la place-de)
Architectures de nombreux inclure une API Web qui doit effectuer des appels d’une autre API Web en aval, les deux sécurisé par le point de terminaison version 2.0.  Ce scénario est courant dans les clients natives qui disposent d’une principale API Web, qui à son tour appelle un service Microsoft Online tel que Office 365 ou l’API Graph.

Ce scénario API Web chaîne peut prendre en charge à l’aide de l’octroi d’informations d’identification OAuth 2.0 Jwt porteur, également appelé du [Flux On-Behalf-Of](active-directory-v2-protocols.md#oauth2-on-behalf-of-flow).  Toutefois, le flux de suite à la place de n’est pas actuellement implémenté dans le point de terminaison version 2.0.  Pour découvrir le fonctionnement de ce flux dans sera-t-il disponible Azure Active Directory du service, consultez l' [exemple de la part de code sur GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).
