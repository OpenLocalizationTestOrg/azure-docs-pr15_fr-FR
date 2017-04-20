<properties
    pageTitle="Azure AD B2C | Microsoft Azure"
    description="Les types d’applications que vous pouvez créer dans le B2C annuaire Active Azure."
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
    ms.topic="hero-article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-active-directory-b2c-types-of-applications"></a>Azure B2C annuaire Active : Types d’applications

Azure Active Directory (AD Azure) B2C prend en charge l’authentification pour divers architectures application moderne. Celles-ci sont basées sur les protocoles standard [OAuth 2.0](active-directory-b2c-reference-protocols.md) ou [OpenID se connecter](active-directory-b2c-reference-protocols.md). Ce document décrit brièvement les types d’applications que vous pouvez générer, indépendamment de la langue ou la plateforme que vous préférez. Il vous permet également de comprendre les scénarios globale avant de [commencer à créer des applications](active-directory-b2c-overview.md#getting-started).

## <a name="the-basics"></a>Concepts de base
Chaque application qui utilise Azure AD B2C doit être enregistrée dans votre [annuaire B2C](active-directory-b2c-get-started.md) via le [Portail Azure](https://portal.azure.com/). Le processus d’inscription application collecte et assigne des valeurs dans votre application :

- Un **ID de l’Application** qui identifie votre application.
- **Rediriger URI** qui peut être utilisé pour diriger les réponses revenir à votre application.
- Toutes les valeurs spécifiques au scénario. Pour plus d’informations, découvrez comment [Enregistrer une application](active-directory-b2c-app-registration.md).

Une fois que l’application est enregistrée, il communique avec Azure AD en envoyant des requêtes au point de terminaison Azure AD version 2.0 :

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Chaque demande est envoyée à Azure AD B2C spécifie une **stratégie**. Une stratégie de contrôle le comportement d’Azure AD. Vous pouvez également utiliser ces points de terminaison pour créer un jeu hautement personnalisable de l’expérience utilisateur. Stratégies courantes incluent d’abonnement, connexion et stratégies de profil à modifier. Si vous n’êtes pas familiarisé avec les stratégies, vous devez lire sur Azure AD B2C [framework stratégie extensible](active-directory-b2c-reference-policies.md) avant de poursuivre.

L’interaction de chaque application avec un point de terminaison version 2.0 suit un modèle similaire de haut niveau :

1. L’application indique l’utilisateur au point de terminaison version 2.0 pour exécuter une [stratégie](active-directory-b2c-reference-policies.md).
2. L’utilisateur termine la stratégie en fonction de la définition de stratégie.
4. L’application reçoit une sorte de jeton de sécurité à partir de la version 2.0.
5. L’application utilise le jeton de sécurité pour accéder aux informations protégées ou une ressource protégée.
6. Le serveur de ressources valide le jeton de sécurité pour vérifier que l’accès peut être accordé.
7. L’application actualise régulièrement du jeton de sécurité.

<!-- TODO: Need a page for libraries to link to -->
Ces étapes peuvent différer légèrement en fonction du type d’application que vous créez. Bibliothèques d’ouvrir la source peuvent également traiter les détails pour vous.

## <a name="web-apps"></a>Applications Web
Pour les applications web (y compris .NET, PHP, Java, Ruby, Python et Node.js) qui sont hébergées sur un serveur et accessible via un navigateur, Azure AD B2C prend en charge [OpenID se connecter](active-directory-b2c-reference-protocols.md) pour toutes les expériences utilisateur. Cela inclut la connexion, d’abonnement et gestion des profils. Dans l’implémentation Azure AD B2C de OpenID Connect, votre application web lance ces expériences utilisateur à l’aide de requêtes d’authentification vers Azure Active Directory. Le résultat de la demande est un `id_token`. Ce jeton de sécurité représente l’identité de l’utilisateur. Il fournit également des informations sur l’utilisateur dans le formulaire de responsabilité :

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

En savoir plus sur les types de jetons et revendications disponibles pour une application dans le [jeton B2C référencer](active-directory-b2c-reference-tokens.md).

Dans une application web, chaque exécution d’une [stratégie](active-directory-b2c-reference-policies.md) prend ces étapes principales :

![Image de couloirs Web App](./media/active-directory-b2c-apps/webapp.png)

Validation de le `id_token` en utilisant une clé de signature publique reçus à partir d’Azure AD est suffisante pour vérifier l’identité de l’utilisateur. Cette commande définit également des cookies de session qui peuvent être utilisé pour identifier l’utilisateur sur les demandes de la page suivante.

Pour voir ce scénario en action, essayez un des exemples de code de connexion à l’application web dans notre [que section mise en route](active-directory-b2c-overview.md#getting-started).

En plus de faciliter la connexion simple, une application de serveur web devrez également accéder à un service web principale. Dans ce cas, l’application web peut effectuer un légèrement différente [flux OpenID se connecter](active-directory-b2c-reference-oidc.md) et acquérir des jetons en utilisant des codes d’autorisation et actualiser les jetons. Ce scénario est décrite dans la [section API Web](#web-apis)suivante.

<!--, and in our [WebApp-WebAPI Getting started topic](active-directory-b2c-devquickstarts-web-api-dotnet.md).-->

## <a name="web-apis"></a>API Web
Vous pouvez utiliser Azure AD B2C pour sécuriser les services web tels que web RESTful de votre application API. API Web utiliser OAuth 2.0 pour sécuriser leurs données, en les requêtes HTTP entrantes à l’aide des jetons d’authentification. L’appelant d’un site web API ajoute un jeton dans l’en-tête d’autorisation d’une requête HTTP :

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

L’API web peut ensuite utiliser le jeton pour vérifier l’identité de l’appelant de l’API et extraire des informations à propos de l’appelant de revendications qui sont encodées dans le jeton. En savoir plus sur les types de jetons et revendications disponibles pour une application dans le [jeton Azure AD B2C référencer](active-directory-b2c-reference-tokens.md).

> [AZURE.NOTE]
    Azure AD B2C prend actuellement en charge uniquement web API qui est accessibles par leurs propres clients connus. Par exemple, votre application complète peut inclure une application iOS, une application Android et un site web principale API. Cette architecture est entièrement pris en charge. Permettre à un client de partenaire, par exemple une autre application iOS accéder au même web QU'API n’est pas pris en charge. Tous les composants de votre application achevée doivent partager un ID d’application unique.

Un site web API peut recevoir des jetons de nombreux types de clients, y compris les applications web, ordinateur de bureau et applications mobiles, seule page applications, processus côté serveur et autres API web. Voici un exemple de l’ensemble du flux pour une application web qui appelle une API web :

![Image de couloirs application Web API Web](./media/active-directory-b2c-apps/webapi.png)

Pour en savoir plus sur les codes d’autorisation, jetons actualiser et les étapes permettant d’obtenir les jetons, en savoir plus sur le [protocole OAuth 2.0](active-directory-b2c-reference-oauth-code.md).

Pour savoir comment sécuriser une API web à l’aide Azure AD B2C, consultez le site web didacticiels API dans notre [section mise en route](active-directory-b2c-overview.md#getting-started).

## <a name="mobile-and-native-apps"></a>Applications mobiles et natives
Applications installées sur les appareils, tels que les applications mobiles et de bureau, souvent besoin d’accéder aux services principaux ou web API au nom d’utilisateurs. Vous pouvez ajouter des expériences de gestion des identités personnalisée à vos applications natives et en toute sécurité services principaux appel à l’aide de Azure AD B2C et le [flux de code 2.0 OAuth d’autorisation](active-directory-b2c-reference-oauth-code.md).  

Dans ce flux, l’application exécute [stratégies](active-directory-b2c-reference-policies.md) et reçoit une `authorization_code` à partir d’Azure AD une fois que l’utilisateur a terminé la stratégie. La `authorization_code` représente l’autorisation de l’application pour appeler des services principaux au nom de l’utilisateur qui est actuellement connecté. L’application peut ensuite échanger la `authorization_code` en arrière-plan pour un `id_token` et un `refresh_token`.  L’application peut utiliser les `id_token` s’authentifier à un site web principale API dans les requêtes HTTP. Il peut également utiliser la `refresh_token` pour obtenir un nouveau `id_token` lorsqu’un ancien arrive à expiration.

> [AZURE.NOTE]
    Azure AD B2C prend actuellement en charge uniquement les jetons qui sont utilisées pour accéder à un service web principale d’application. Par exemple, votre application complète peut inclure une application iOS, une application Android et un site web principale API. Cette architecture est entièrement pris en charge. Ce qui permet de votre application iOS accéder à un site web partenaire API en utilisant les jetons d’accès OAuth 2.0 n’est pas actuellement pris en charge. Tous les composants de votre application achevée doivent partager un ID d’application unique.

![Application native couloirs Image](./media/active-directory-b2c-apps/native.png)

## <a name="current-limitations"></a>Limitations en cours
Azure AD B2C ne prend pas en charge les types suivants d’applications, mais ils se trouvent sur la roadmapy. Restrictions liées à Azure AD B2C et limites supplémentaires sont décrites dans les [restrictions et Limitations](active-directory-b2c-limitations.md).

### <a name="single-page-apps-javascript"></a>Applications de page unique (JavaScript)
Plusieurs applications modernes ont une page simple application frontale écrite principalement en JavaScript. Ils utilisent souvent un cadre comme AngularJS, Ember.js ou Durandal. Généralement disponibles service Azure AD prend en charge ces applications à l’aide du flux implicite OAuth 2.0. Toutefois, ce flux n’est pas encore disponible dans Azure AD B2C.

### <a name="daemonsserver-side-apps"></a>Applications de processus/côté serveur
Applications qui contiennent des processus longs ou qui fonctionnent sans la présence d’un utilisateur doivent également un moyen d’accéder aux ressources sécurisées comme API web. Ces applications peuvent s’authentifier et obtenir des jetons à l’aide d’identité de l’application (plutôt que d’identité déléguée d’un utilisateur) et en utilisant le client OAuth 2.0 flux des informations d’identification.

Ce flux n’est pas actuellement pris en charge par Azure AD B2C. Ces applications peuvent accéder jetons uniquement après qu’un flux interactif utilisateur s’est produite.

### <a name="web-api-chains-on-behalf-of-flow"></a>API Web chaînes (sur à la place de flux)
Architectures de nombreux inclure un site web API dont a besoin pour appeler un autre site web en aval API, où les deux sont sécurisés par Azure AD B2C. Ce scénario est courant dans les clients natives qui ont une API Web principale. Cela appelle ensuite un service en ligne Microsoft tels que l’API Azure AD Graph.

Ce scénario web chaîne API peut être pris en charge à l’aide de l’octroi d’informations d’identification PORTEUR OAuth 2.0 JWT, également connu sous le flux sur à la place de.  Toutefois, le flux sur à la place de n’est pas actuellement implémenté dans les AD B2C Azure.
