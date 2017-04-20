<properties
    pageTitle="B2C Azure Active Directory : Vue d’ensemble | Microsoft Azure"
    description="Développement d’applications pour les consommateurs avec Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-sign-up-and-sign-in-consumers-in-your-applications"></a>Azure B2C annuaire Active : S’inscrire et se connecter consommateurs dans vos applications

Azure B2C annuaire Active est une solution de gestion d’identité cloud complète pour votre web pour les consommateurs et les applications mobiles. C’est un service global hautement disponible qui s’adapte à des centaines de millions d’identités. Basé sur une plateforme sécurisée échelle de l’entreprise, Azure Active Directory B2C permet de conserver vos applications et vos consommateurs protégé par votre entreprise.

Par le passé, les développeurs d’applications qui veulent s’inscrire et se connecter consommateurs dans leurs applications seraient avez écrit leur propre code. Et serait ont utilisés systèmes ou bases de données locale pour stocker les noms d’utilisateur et les mots de passe. Azure B2C annuaire Active offre aux développeurs un meilleur moyen d’intégrer la gestion des identités consommateur dans leurs applications à l’aide d’une plateforme sécurisée, basés sur des normes et un large éventail de stratégies extensibles. Lorsque vous utilisez Azure Active Directory B2C, vos consommateurs peuvent vous inscrire pour vos applications à l’aide de leurs comptes sociales existants (Facebook, Google, Amazon, LinkedIn) ou en créant des nouvelles informations d’identification (adresse de messagerie et mot de passe, ou nom d’utilisateur et mot de passe) ; Nous appelons les dernier « comptes locaux. »

## <a name="get-started"></a>Prise en main

Pour créer une application qui accepte l’inscription du consommateur et se connecter, vous devez devez enregistrer l’application avec un Active Directory B2C du Azure du client. Obtenez votre propre client en suivant les étapes décrites dans [créer un client Azure AD B2C](active-directory-b2c-get-started.md).

Vous pouvez écrire votre application par rapport au service Azure Active Directory B2C en choisissant d’envoyer des messages de protocole directement, à l’aide de [OAuth 2.0](active-directory-b2c-reference-protocols.md#oauth2-authorization-code-flow) ou [Ouvrir ID de connexion](active-directory-b2c-reference-protocols.md#openid-connect-sign-in-flow), ou à l’aide de notre bibliothèques pour effectuer le travail pour vous. Sélectionnez votre plateforme favori dans le tableau suivant, prise en main.

[AZURE.INCLUDE [active-directory-b2c-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

## <a name="whats-new"></a>Quelles sont les nouveautés

Revenez souvent pour en savoir plus sur les modifications ultérieures la B2C annuaire Active Azure. Nous allons également tweet à propos des mises à jour à l’aide de @AzureAD.

- Obtenir des informations à propos de notre [infrastructure de stratégie extensible](active-directory-b2c-reference-policies.md) et sur les types de stratégies que vous pouvez créer et utiliser dans vos applications.
- Créez un signet notre [blog service](https://blogs.msdn.microsoft.com/azureadb2c/) pour les notifications de problèmes de service secondaires, mises à jour, état et minimisation. Continuez à surveiller le [tableau de bord état Azure](https://azure.microsoft.com/status/) .
- Active [les contraintes, restrictions et limitations du service](active-directory-b2c-limitations.md).
- Enfin, un [exemple de code](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-b2c) utilisant Azure AD B2C & Core ASP.NET.

## <a name="how-to-articles"></a>Articles sur les procédures

Découvrez comment utiliser les fonctionnalités Azure Active Directory B2C spécifiques :

- Configurer des comptes [Facebook](active-directory-b2c-setup-fb-app.md), [Google +](active-directory-b2c-setup-goog-app.md), [compte Microsoft](active-directory-b2c-setup-msa-app.md), [Amazon](active-directory-b2c-setup-amzn-app.md)et [LinkedIn](active-directory-b2c-setup-li-app.md) à utiliser dans vos applications pour les consommateurs.
- [Utilisation des attributs personnalisés pour recueillir des informations sur vos consommateurs](active-directory-b2c-reference-custom-attr.md).
- [Activer l’authentification multifacteur Azure dans vos applications pour les consommateurs](active-directory-b2c-reference-mfa.md).
- [Configurer la libre-service du mot de passe permettant à vos consommateurs](active-directory-b2c-reference-sspr.md).
- [Personnaliser l’apparence de connexion, se connecter et autres pages pour les consommateurs](active-directory-b2c-reference-ui-customization.md) pris en charge par Azure Active Directory B2C.
- [Utiliser l’API Graph Azure Active Directory par programme créer, lire, mettre à jour, et supprimer les consommateurs](active-directory-b2c-devquickstarts-graph-dotnet.md) dans votre client Azure Active Directory B2C.

## <a name="next-steps"></a>Étapes suivantes

Ces liens seront utiles pour l’exploration du service en profondeur :

- Afficher les [informations de tarification Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
- Obtenir de l’aide sur débordement de pile à l’aide de l' [azure active directory](http://stackoverflow.com/questions/tagged/azure-active-directory) ou [adal](http://stackoverflow.com/questions/tagged/adal) balises.
- Envoyez-nous vos idées à l’aide de [Voix utilisateur](https://feedback.azure.com/forums/169401-azure-active-directory/)--nous voulons entendre les ! Utilisez l’expression « AzureADB2C : » dans le titre de votre billet afin de nous permettre de le retrouver.
- Passez en revue la [référence au protocole Azure AD B2C](active-directory-b2c-reference-protocols.md).
- Passez en revue la [référence de jeton Azure AD B2C](active-directory-b2c-reference-tokens.md).
- Lisez les [questions fréquentes B2C Azure Active Directory](active-directory-b2c-faqs.md).
- [Fichier prend en charge les demandes d’Azure Active Directory B2C](active-directory-b2c-support.md).

## <a name="get-security-updates-for-our-products"></a>Obtenir des mises à jour de sécurité pour nos produits

Nous vous invitons à obtenir des notifications d’incidents de sécurité se produire en visitant [cette page](https://technet.microsoft.com/security/dd252948) et l’abonnement aux alertes avis de sécurité.
