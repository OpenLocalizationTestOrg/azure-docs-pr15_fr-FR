<properties
    pageTitle="vue d’ensemble du point de terminaison de version 2.0 | Microsoft Azure"
    description="Introduction à la création d’applications avec Microsoft Account et Azure Active Directory se connecter."
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
    ms.date="09/27/2016"
    ms.author="dastrock"/>

# <a name="sign-in-microsoft-account--azure-ad-users-in-a-single-app"></a>Connexion à Microsoft Account & Azure AD utilisateurs dans une application unique

Par le passé, un développeur de l’application qui veulent prennent en charge les comptes Microsoft et Azure Active Directory a été requis pour intégrer à deux systèmes distincts.  Nous vous présentons maintenant une nouvelle version de l’API d’authentification qui permet de vous connecter aux utilisateurs avec les deux types de comptes à l’aide du système Azure AD.  Ce système d’authentification convergent est connu sous le **point de terminaison de la version 2.0**.  Avec le point de terminaison version 2.0, une intégration simple vous permet de joindre une audience qui recouvre des millions d’utilisateurs avec les comptes personnels et Professionnel ou scolaire.

Applications qui utilisent le point de terminaison version 2.0 peuvent également consommer API REST à partir du [Microsoft Graph](https://graph.microsoft.io) et [Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) à l’aide d’un type de compte.

<!-- For a quick introduction to the v2.0 endpoint, please view the [Getting Started with Microsoft Identities: Enterprise Grade Sign In For Your Apps](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/) video. -->

## <a name="getting-started"></a>Prise en main
[AZURE.VIDEO build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps]

Choisissez votre plateforme favori dans la liste suivante pour créer une application à l’aide de notre bibliothèques ouvrir la source et cadres.  Par ailleurs, vous pouvez utiliser notre documentation de protocole 2.0 OAuth & OpenID se connecter à envoyer et recevoir des messages de protocole directement sans recourir à une bibliothèque d’authentification.

<!-- TODO: Finalize this table  -->
[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## <a name="whats-new"></a>Quelles sont les nouveautés
Les informations conceptuelles ici sera utiles de comprendre les nouveautés et ce qui n’est pas possible avec le point de terminaison version 2.0.

- En savoir plus sur les [types d’applications, que vous pouvez créer avec le point de terminaison version 2.0](active-directory-v2-flows.md).
- Comprendre les [limitations, les restrictions et les contraintes](active-directory-v2-limitations.md) avec le point de terminaison version 2.0.
- Nous avons récemment ajouté prise en charge [d’administration restreint les étendues](active-directory-v2-scopes.md) et le [client oauth2 n’accorder des informations d’identification](active-directory-v2-protocols-oauth-client-creds.md).  Les essayer !

## <a name="reference"></a>Référence
Ces liens seront utiles pour l’exploration de la plateforme en profondeur :

- Génération 2016 : [mise en route des identités de Microsoft : entreprise notes se connecter pour vos applications](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/)
- Obtenir de l’aide sur la pile de dépassement de capacité à l’aide de l' [azure active directory](http://stackoverflow.com/questions/tagged/azure-active-directory) ou [adal](http://stackoverflow.com/questions/tagged/adal) balises.
- [Référence au protocole version 2.0](active-directory-v2-protocols.md)
- [Référence de jeton de version 2.0](active-directory-v2-tokens.md)
- [Référence de la bibliothèque version 2.0](active-directory-v2-libraries.md)
- [Les étendues et consentement dans le point de terminaison version 2.0](active-directory-v2-scopes.md)
- [Le portail d’inscription de l’application Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)
- [Référence des API REST Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
- [Le graphique Microsoft](https://graph.microsoft.io)