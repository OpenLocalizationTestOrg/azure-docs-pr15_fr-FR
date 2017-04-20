<properties
   pageTitle="Gestion des identités pour les Applications partagées | Microsoft Azure"
   description="Meilleures pratiques pour la gestion de l’authentification, l’autorisation et identité dans les applications partagées."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/02/2016"
   ms.author="mwasson"/>

# <a name="identity-management-for-multitenant-applications-in-microsoft-azure"></a>Gestion des identités pour les applications partagées dans Microsoft Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Cette série décrit les meilleures pratiques pour donc le partage, lorsque vous utilisez Azure AD pour la gestion de l’authentification et d’identité.

Lorsque vous créez une application partagée, une des premières difficultés est la gestion des identités d’utilisateurs, car chaque utilisateur appartient à un client. Par exemple :

- Les utilisateurs se connectent avec leurs informations d’identification d’organisation.
- Les utilisateurs doivent y accéder aux données de leur organisation, mais pas les données qui appartiennent à d’autres clients.
- Une organisation peut s’inscrire à l’application et puis attribuer des rôles d’application à ses membres.

Azure Active Directory (AD Azure) contient quelques fonctionnalités qui prennent en charge tous ces scénarios.

Pour accompagner cette série d’articles, nous avons créé également une [mise en œuvre de bout en bout] complète,[ tailspin] d’une application partagée. Les articles reflètent ce que nous avons appris en cours de création de l’application. Pour commencer à utiliser l’application, voir [l’exécution de l’application d’enquêtes](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md).

Voici la liste des articles dans cette série :

- [Introduction à la gestion des identités pour les applications partagées](guidance-multitenant-identity-intro.md)
- [À propos de l’application Tailspin enquêtes](guidance-multitenant-identity-tailspin.md)
- [Authentification à l’aide d’Azure AD et OpenID se connecter](guidance-multitenant-identity-authenticate.md)
- [Utilisation des identités basée sur les revendications](guidance-multitenant-identity-claims.md)
- [Inscription et d’intégration du client](guidance-multitenant-identity-signup.md)
- [Rôles d’application](guidance-multitenant-identity-app-roles.md)
- [Autorisation basée sur les rôles et basée sur les ressources](guidance-multitenant-identity-authorize.md)
- [Sécurisation d’un site web et API](guidance-multitenant-identity-web-api.md)
- [La mise en cache oauth2 ne les jetons d’accès](guidance-multitenant-identity-token-cache.md)
- [Fédérer avec AD FS un client pour les applications partagées dans Azure](guidance-multitenant-identity-adfs.md)
- [À l’aide d’assertion client pour obtenir des jetons d’accès à partir d’Azure AD](guidance-multitenant-identity-client-assertion.md)
- [À l’aide de l’archivage sécurisé clé pour protéger la confidentialité de l’application](guidance-multitenant-identity-keyvault.md)

[tailspin]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
