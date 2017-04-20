<properties
    pageTitle="Azure AD version 2.0 protocoles | Microsoft Azure"
    description="Guide pour les protocoles pris en charge par le point de terminaison Azure AD version 2.0."
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

# <a name="v20-protocols---oauth-20--openid-connect"></a>Connecter des protocoles - OAuth 2.0 & OpenID version 2.0

Le point de terminaison version 2.0 permet Azure AD d’identité-as-a-service avec les protocoles standard, OpenID se connecter et OAuth 2.0.  Le service est conformes aux normes, mais peut être légères différences entre les deux mises en œuvre de ces protocoles.  Ces informations seront utiles si vous choisissez d’écrire votre code en envoyant directement et gérer des requêtes HTTP ou utiliser un 3e partie ouvrir bibliothèque de sources, plutôt que d’utiliser un de nos bibliothèques ouvrir la source.
<!-- TODO: Need link to libraries above -->

> [AZURE.NOTE]
    Pas tous les scénarios Azure Active Directory et fonctionnalités sont prises en charge par le point de terminaison version 2.0.  Pour déterminer si vous devez utiliser le point de terminaison version 2.0, lisez les [limitations de la version 2.0](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Concepts de base
Dans presque tous les flux OAuth & OpenID se connecter, il existe quatre parties impliqués dans l’exchange :

![Rôles OAuth 2.0](../media/active-directory-v2-flows/protocols_roles.png)

- Le **Serveur de l’autorisation** est le point de terminaison version 2.0.  Il est responsable d’assurant identité de l’utilisateur, l’attribution de l’annulation de l’accès aux ressources et des émission jetons.  Il est également connue sous le fournisseur d’identité - il traite de manière sécurisée quelque chose à faire avec les informations utilisateur, leur accès et les relations d’approbation entre parties dans un flux.
- Le **Propriétaire de la ressource** est généralement l’utilisateur final.  C’est la fête est propriétaire des données, et dont la puissance pour permettre à des tiers accéder à ces données, ou les ressources.
- Le **OAuth Client** est votre application, identifiée par son ID d’Application.  Il s’agit généralement de la personne qui a l’utilisateur final interagit avec, et qu’elle jetons à partir du serveur d’autorisation.  Le client doit être autorisé à accéder à la ressource par le propriétaire de la ressource.
- Le **Serveur de ressources** où réside la ressource ou les données.  Il approuve le serveur d’autorisation pour l’authentification sécurisée et autoriser le OAuth Client et utilise access_tokens porteur pour s’assurer que l’accès à une ressource peut être accordé.


## <a name="app-registration"></a>Inscription de l’application
Chaque application qui utilise le point de terminaison version 2.0 doit être enregistré à [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) avant qu’elle peut interagir à l’aide de OAuth ou OpenID se connecter.  Le processus d’inscription de l’application sera collecter et affecter des valeurs dans votre application :

- Un **Id de l’Application** qui identifie votre application
- Un **URI rediriger** ou un **Identificateur de Package** pouvant être utilisées pour diriger les réponses revenir à votre application
- Quelques autres valeurs spécifiques au scénario.

Pour plus d’informations, découvrez comment [Enregistrer une application](active-directory-v2-app-registration.md).

## <a name="endpoints"></a>Points de terminaison
Une fois inscrit, l’application communique avec Azure AD en envoyant des requêtes au point de terminaison version 2.0 :

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Où les `{tenant}` peut prendre une des quatre valeurs différentes :

| Valeur | Description |
| ----------------------- | ------------------------------- |
| `common` | Permet aux utilisateurs avec les comptes Microsoft personnels et Professionnel ou scolaire à partir d’Azure Active Directory pour vous connecter à l’application. |
| `organizations` | Permet uniquement les utilisateurs possédant un compte professionnel ou scolaire à partir d’Azure Active Directory pour vous connecter à l’application. |
| `consumers` | Permet aux utilisateurs uniquement avec les comptes Microsoft personnels (MSA) pour vous connecter à l’application. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490`ou`contoso.onmicrosoft.com` | Permet uniquement les utilisateurs possédant un compte professionnel ou scolaire à partir d’un client Azure Active Directory particulier pour vous connecter à l’application.  Le nom de domaine convivial du client Azure AD ou identificateur guid du client peut être utilisé.  |

Pour plus d’informations sur la façon d’interagir avec ces points de terminaison, choisissez un type d’application spécifique ci-dessous.

## <a name="tokens"></a>Jetons
L’implémentation de la version 2.0 de OAuth 2.0 et OpenID se connecter utilisent beaucoup de jetons porteur, y compris des jetons porteur représentés sous la forme JWTs. Un jeton porteur est un jeton de sécurité léger qui accorde l’accès « porteur » à une ressource protégée. Dans ce sens, « porteur » est une partie qui peut présenter le jeton. Bien que fête doit tout d’abord authentifier avec Azure AD pour recevoir le jeton porteur, si les étapes requises ne sont pas prises pour sécuriser le jeton de stockage et de transmission, il peut être interception et utilisé par un tiers involontaire. Tandis que certains jetons de sécurité ont un mécanisme intégré pour empêcher que des personnes non autorisées à l’aise, jetons PORTEUR n’ont pas ce mécanisme et doivent être acheminés dans un canal sécurisé comme TLS (HTTPS). Si un jeton porteur est transmis en clair, un homme d’attaque peut être utilisée par une partie malveillante pour obtenir le jeton et utilisez-le pour un accès non autorisé à une ressource protégée. Les mêmes principes de sécurité s’appliquent lorsque vous stockez ou la mise en cache des jetons porteur pour une utilisation ultérieure. Toujours Assurez-vous que votre application transmet et stocke les jetons des porteur de façon sécurisée. Pour des raisons de sécurité plus sur les jetons porteur, voir [RFC 6750 Section 5](http://tools.ietf.org/html/rfc6750).

En savoir plus sur différents types de jetons utilisés dans le point de terminaison version 2.0 est disponible dans [la référence de jeton de point de terminaison de version 2.0](active-directory-v2-tokens.md).

## <a name="protocols"></a>Protocoles

Si vous êtes prêt à voir certaines demandes d’exemple, commencer à utiliser un de le ci-dessous didacticiels.  Chacun d'entre eux correspond à un scénario d’authentification particulier.  Si vous avez besoin d’aide pour déterminer qui est le flux de droite pour vous, consultez [les types d’applications, que vous pouvez créer avec la version 2.0](active-directory-v2-flows.md).

- [Générer Mobile et Application Native avec OAuth 2.0](active-directory-v2-protocols-oauth-code.md)
- [Créer des Web applications avec ID d’ouverture se connecter](active-directory-v2-protocols-oidc.md)
- [Créer des applications de Page unique avec le flux implicite OAuth 2.0](active-directory-v2-protocols-implicit.md)
- [Processus de génération ou processus du côté serveur avec les informations d’identification du Client OAuth 2.0 flux](active-directory-v2-protocols-oauth-client-creds.md)
- Obtenir des jetons dans une API Web avec le jeton 2.0 de la part de flux (bientôt disponible)

<!-- - Get tokens using a username & password with the OAuth 2.0 Resource Owner Password Credentials Flow (coming soon) --> 
