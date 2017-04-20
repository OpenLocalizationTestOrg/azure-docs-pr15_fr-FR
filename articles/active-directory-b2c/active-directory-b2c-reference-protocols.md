<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Découvrez comment créer des applications directement en utilisant les protocoles pris en charge par Azure Active Directory B2C."
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

# <a name="azure-ad-b2c-authentication-protocols"></a>Azure AD B2C : Protocoles d’authentification

Azure Active Directory (AD Azure) B2C fournit identité en tant que service pour vos applications en prenant en charge les deux protocoles standard : OpenID se connecter et OAuth 2.0. Le service est conformes aux normes, mais les mises en deux œuvre de ces protocoles présentant de légères différences.  Les informations contenues dans ce guide seront utiles si vous écrivez votre code en envoyant directement et gestion des demandes HTTP, plutôt que d’utiliser une bibliothèque open source. Nous vous recommandons de lire cette page avant d’approfondir les détails de chaque protocole spécifique. Mais si vous êtes déjà familiarisé avec Azure AD B2C, vous pouvez accéder directement aux [guides de référence protocole](#protocols).

<!-- TODO: Need link to libraries above -->

## <a name="the-basics"></a>Concepts de base
Chaque application qui utilise Azure AD B2C doit être enregistré dans votre annuaire B2C dans le [portail Azure](https://portal.azure.com). Le processus d’inscription application collecte et assigne des valeurs dans votre application :

- Un **ID de l’Application** qui identifie votre application.
- Un **URI rediriger** ou un **identificateur de package** qui peut être utilisé pour diriger les réponses revenir à votre application.
- Quelques autres valeurs spécifiques au scénario. Pour plus d’informations, Découvrez [comment enregistrer votre application](active-directory-b2c-app-registration.md).

Après avoir enregistré votre application, il communique avec Azure AD en envoyant des requêtes au point de terminaison version 2.0 :

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Dans presque tous les flux OAuth et OpenID se connecter, quatre parties sont impliquées dans l’exchange :

![Rôles OAuth 2.0](./media/active-directory-b2c-reference-protocols/protocols_roles.png)

- Le **serveur de l’autorisation** est le point de terminaison Azure AD version 2.0. Il traite de manière sécurisée tout ce qui concerne l’accès et les informations utilisateur. Il gère également les relations d’approbation entre les parties dans un flux. Il est responsable de la vérification de l’identité de l’utilisateur, l’attribution de l’annulation de l’accès aux ressources et des émission jetons. Il est également connue sous le fournisseur d’identité.
- Le **propriétaire de la ressource** est généralement l’utilisateur final. Il s’agit de la partie qui est propriétaire des données, et qu’il possède la puissance pour permettre à des tiers accéder aux données ou la ressource.
- Le **client OAuth** est votre application. Il est identifié par son ID d’Application. Il s’agit généralement de la personne qui a les utilisateurs finaux interagissent avec. Il demande également jetons à partir du serveur d’autorisation. Propriétaire de la ressource doit accorder l’autorisation client pour accéder à la ressource.
- Le **serveur de ressources** où réside la ressource ou les données. Il approuve le serveur d’autorisation pour authentifier et autoriser le client OAuth en toute sécurité. Il utilise également jetons d’accès porteur pour vous assurer que l’accès à une ressource peut être accordé.

## <a name="policies"></a>Stratégies
Sans doute, Azure AD B2C stratégies sont les fonctionnalités les plus importantes du service. Azure AD B2C étend les protocoles OAuth 2.0 et se connecter OpenID standards en introduisant de stratégies. Celles-ci permettent Azure AD B2C effectuer bien plus simple d’authentification et d’autorisation. Stratégies de décrivent entièrement consommateur identité expériences, y compris d’abonnement, connexion et la modification du profil. Stratégies peuvent être définies dans une interface utilisateur d’administration. Elles peuvent être exécutées à l’aide d’un paramètre de requête spécial dans les demandes d’authentification HTTP. Stratégies n’étant pas des fonctionnalités standard de OAuth 2.0 et OpenID à vous connecter, vous devez prendre le temps de comprendre les. Pour plus d’informations, voir le [guide de référence de stratégie Azure AD B2C](active-directory-b2c-reference-policies.md).

## <a name="tokens"></a>Jetons
L’implémentation Azure AD B2C du jeton 2.0 et OpenID connecter utilise fréquemment des jetons porteur, y compris des jetons porteur qui sont représentés sous forme de jetons web JSON (JWTs). Un jeton porteur est un jeton de sécurité léger qui accorde l’accès « porteur » à une ressource protégée. Le porteur est toute partie qui peut présenter le jeton. Azure AD doit tout d’abord authentifier fête avant de pouvoir recevoir un jeton porteur. Mais si les étapes requises ne sont pas prises pour sécuriser le jeton de stockage et de transmission, il peut être intercepter et utilisé par un tiers involontaire.

Certaines des jetons de sécurité ont un mécanisme qui empêche les personnes non autorisées à utiliser ces derniers, mais jetons porteur ne possèdent pas ce mécanisme. Ils doivent être acheminés dans un canal sécurisé, telles que TLS (HTTPS). Si un jeton porteur est transmis à l’extérieur d’un canal sécurisé, une partie malveillante permet une attaque dans milieu acquérir le jeton et utilisez-le pour accéder à une ressource protégée. Les mêmes principes de sécurité s’appliquent quand jetons PORTEUR sont stockés ou mis en cache pour une utilisation ultérieure. Toujours vous assurer que votre application transmet et stocke les jetons des porteur de façon sécurisée.

Pour des raisons de sécurité jeton PORTEUR supplémentaires, voir [RFC 6750 Section 5](http://tools.ietf.org/html/rfc6750).

Plus d’informations sur les différents types de jetons utilisés dans Azure AD B2C sont disponibles dans [la référence de jeton Azure AD](active-directory-b2c-reference-tokens.md).

## <a name="protocols"></a>Protocoles

Lorsque vous êtes prêt à réviser certaines demandes d’exemple, vous pouvez commencer avec parmi les didacticiels suivants. Chacun d'entre eux correspond à un scénario d’authentification particulier. Si vous avez besoin de vous aider à déterminer quel flux est adaptée à vos besoins, consultez [les types d’applications que vous pouvez créer à l’aide Azure AD B2C](active-directory-b2c-apps.md).

- [Créer des applications mobiles et natives à l’aide OAuth 2.0](active-directory-b2c-reference-oauth-code.md)
- [Créer des applications web à l’aide de OpenID se connecter](active-directory-b2c-reference-oidc.md)
