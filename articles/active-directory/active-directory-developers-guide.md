<properties
   pageTitle="Guide du développeur Azure Active Directory | Microsoft Azure"
   description="Cet article fournit un guide complet sur les ressources destinées aux développeurs pour Azure Active Directory."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/24/2016"
   ms.author="mbaldwin"/>


# <a name="azure-active-directory-developers-guide"></a>Guide du développeur Azure Active Directory

## <a name="overview"></a>Vue d’ensemble
Comme une gestion des identités en tant que service (IDMaaS) plate-forme Azure Active Directory (AD) fournit aux développeurs un moyen efficace pour intégrer la gestion des identités dans leurs applications. Les articles suivants fournissent une vue d’ensemble sur l’implémentation et les principales fonctionnalités de Azure AD. Nous vous conseillons les lire dans l’ordre, ou accédez à la [mise en route](#getting-started) si vous êtes prêt à approfondir.


1. [Les avantages de l’intégration Azure AD](./develop/active-directory-how-to-integrate.md): Découvrez pourquoi l’intégration avec Azure AD offre la meilleure solution pour une connexion sécurisée et l’autorisation.

1. [Scénarios d’authentification Azure AD](active-directory-authentication-scenarios.md): tirer parti de l’authentification simplifiée dans Azure AD pour fournir authentification à votre application.

1. [Intégration d’applications avec Azure AD](active-directory-integrating-applications.md): Découvrez comment ajouter, mettre à jour et supprimer des applications à partir d’Azure AD et sur les règles de personnalisation d’applications intégrées.

1. [API Azure AD Graph](active-directory-graph-api.md): utiliser l’API Azure AD Graph pour accéder par programme Azure AD via les points de terminaison API REST. L’API Azure AD Graph est également accessible via [Microsoft Graph](https://graph.microsoft.io/). Microsoft Graph fournit une API unifiée qui autorise l’accès à plusieurs Microsoft cloud API de service, au moyen d’un point de terminaison API REST unique et avec un jeton d’accès unique.

1. [Bibliothèques d’authentification Azure AD](active-directory-authentication-libraries.md): faciliter l’authentification des utilisateurs pour obtenir des jetons d’accès à l’aide de bibliothèques d’authentification Azure AD pour .NET, JavaScript, objectif C et Android.


## <a name="getting-started"></a>Prise en main

Ces didacticiels sont conçues pour plusieurs plateformes et peuvent vous aider à commencer rapidement à développer avec Azure Active Directory. En tant que composant requis, vous devez [obtenir un client Azure Active Directory](active-directory-howto-tenant.md).

### <a name="mobile-and-pc-application-quick-start-guides"></a>Guides de démarrage rapide d’application mobile et PC

|[![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md)|[![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md)|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md)|[![Windows universel](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md)|[![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md)|[![Cordova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md)|[![OAuth 2.0](./media/active-directory-developers-guide/oauth-2.png)](active-directory-protocols-oauth-code.md)
|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
|[iOS](active-directory-devquickstarts-ios.md)|[Android](active-directory-devquickstarts-android.md)|[.NET](active-directory-devquickstarts-dotnet.md)|[Universel de Windows](active-directory-devquickstarts-windowsstore.md)|[Xamarin](active-directory-devquickstarts-xamarin.md)|[Cordova](active-directory-devquickstarts-cordova.md)|[Intégrer directement OAuth 2.0](active-directory-protocols-oauth-code.md)|

### <a name="web-application-quick-start-guides"></a>Guides de démarrage rapide d’application Web

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md)|[![Java](./media/active-directory-developers-guide/java.png)](active-directory-devquickstarts-webapp-java.md)|[![AngularJS](./media/active-directory-developers-guide/angularjs.png)](active-directory-devquickstarts-angular.md)|[![JavaScript](./media/active-directory-developers-guide/javascript.png)](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-openidconnect-nodejs.md) | [![Se connecter OpenID](./media/active-directory-developers-guide/openid-connect.png)](active-directory-protocols-openid-connect-code.md)
|:--:|:--:|:--:|:--:|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapp-dotnet.md)|[Java](active-directory-devquickstarts-webapp-java.md)|[AngularJS](active-directory-devquickstarts-angular.md)|[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi)|[Node.js](active-directory-devquickstarts-openidconnect-nodejs.md)|[Intégrer directement OpenID se connecter](active-directory-protocols-openid-connect-code.md)|

### <a name="web-api-quick-start-guides"></a>Guides de démarrage rapide d’API Web

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md)
|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapi-dotnet.md)|[Node.js](active-directory-devquickstarts-webapi-nodejs.md)

### <a name="querying-the-directory-quickstart-guide"></a>Interroger le guide de démarrage rapide d’annuaire

| [![.NET](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md)|
|:--:|
|[API de graphique](active-directory-graph-api-quickstart.md)|

## <a name="how-tos"></a>Comment faire

Les articles suivants décrivent comment effectuer des tâches spécifiques à l’aide de Azure Active Directory :

- [Obtenir un client Azure AD](active-directory-howto-tenant.md)
- [Connectez-vous à n’importe quel utilisateur Azure AD en utilisant le modèle d’application client multiples](active-directory-devhowto-multi-tenant-overview.md)
- Activer l’authentification unique entre application l’aide du terme ADAL, [Android](active-directory-sso-android.md) et sur les appareils [iOS](active-directory-sso-ios.md)
- [Rendre votre application AppSource certifié pour Azure AD](active-directory-devhowto-appsource-certified.md)
- [Enregistrez votre application dans la galerie des applications Azure AD](active-directory-app-gallery-listing.md)
- [Envoyer des applications web pour Office 365 pour le tableau de bord](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
- [Comprendre le manifeste d’application Azure Active Directory](active-directory-application-manifest.md)
- [Comprendre les instructions de personnalisation des boutons d’acquisition se connecter et application dans votre application cliente](active-directory-branding-guidelines.md)
- [Aperçu : Comment créer des applications qui s’utilisateurs avec des comptes à la fois personnels et Professionnel ou scolaire](active-directory-appmodel-v2-overview.md)
- [Aperçu : Comment créer des applications qui s’inscrire et se connecter aux consommateurs](../active-directory-b2c/active-directory-b2c-overview.md)
- [Aperçu : configuration de la durée de vie des jetons Azure AD](active-directory-configurable-token-lifetimes.md) à l’aide de PowerShell. Voir les [opérations de stratégie](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) et l' [entité de stratégie](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity) pour plus d’informations sur la configuration via l’API Azure AD Graph.

## <a name="reference"></a>Référence

Les articles suivants fournissent une référence foundation de repos et bibliothèque d’authentification API, protocoles, erreurs, exemples de code et points de terminaison.  

###  <a name="support"></a>Prise en charge
- [Questions balisée](http://stackoverflow.com/questions/tagged/azure-active-directory): solutions rechercher Azure Active Directory sur débordement de pile en recherchant le balises [azure active directory](http://stackoverflow.com/questions/tagged/azure-active-directory) et [adal](http://stackoverflow.com/questions/tagged/adal).
- Consultez le [Glossaire de développeur Azure AD](active-directory-dev-glossary.md) pour les définitions de certains des termes couramment utilisés liés à l’intégration et de développement d’applications.

### <a name="code"></a>Code

- [Bibliothèques d’open source Azure Active Directory](http://github.com/AzureAD): est le moyen le plus simple de trouver la source d’une bibliothèque à l’aide de notre [liste de la bibliothèque](active-directory-authentication-libraries.md).

- [Exemples de Azure Active Directory](https://github.com/azure-samples?query=active-directory): le plus simple pour parcourir la liste des échantillons consiste à l’aide de l' [index d’exemples de code](active-directory-code-samples.md).

- [Active Directory authentification bibliothèque (terme ADAL) pour .NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) - documentation de référence est disponible pour [la dernière version majeure](https://docs.microsoft.com/active-directory/adal/microsoft.identitymodel.clients.activedirectory) et la [version principale précédente](https://docs.microsoft.com/active-directory/adal/v2/microsoft.identitymodel.clients.activedirectory).

### <a name="graph-api"></a>API de graphique

- [Référence de l’API Graph](https://msdn.microsoft.com/library/azure/hh974476.aspx): référence reste pour l’API de Graph Azure Active Directory. [Afficher l’expérience de référence API Graph interactif](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- [Les étendues d’autorisation Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes): étendues d’autorisation OAuth 2.0 qui sont utilisés pour contrôler l’accès aux données de l’annuaire dans un client une application.

### <a name="authentication-and-authorization-protocols"></a>Protocoles d’authentification et d’autorisation

- [Signature clé survol dans Azure AD](active-directory-signing-key-rollover.md): en savoir plus sur l’annonce Azure signature cadence de substitution de la clé et comment mettre à jour de la clé pour les scénarios courants d’applications.

- [Protocole OAuth 2.0 : à l’aide de l’octroi de code](active-directory-protocols-oauth-code.md): vous pouvez utiliser octroi code du protocole 2.0 OAuth d’autorisation pour autoriser l’accès aux applications Web et API Web dans votre Azure Active Directory du client.

- [Protocole OAuth 2.0 : comprendre l’octroi implicite](active-directory-dev-understanding-oauth2-implicit-grant.md): en savoir plus sur l’octroi d’autorisations implicites, et si elle est appropriée pour votre application.

- [Protocole OAuth 2.0 : Service pour les appels de Service à l’aide des références de Client](active-directory-protocols-oauth-service-to-service.md): les informations d’identification OAuth 2.0 Client grant permet à un service web (un client confidentiel) à utiliser ses propres informations d’identification pour authentifier lors de l’appel d’un autre service web, au lieu de l’emprunt d’identité d’un utilisateur. Dans ce scénario, le client est généralement un service web intermédiaire, d’un service de processus ou d’un site Web.

- [Protocole OpenID connecter 1.0 : se connecter et l’authentification](active-directory-protocols-openid-connect-code.md): protocole The OpenID connecter 1.0 étend OAuth 2.0 pour une utilisation comme protocole d’authentification. Une application cliente peut recevoir un id_token pour gérer le processus de connexion, ou augmenter le flux du code d’autorisation pour recevoir une id_token et l’autorisation de code.

- [Référence du protocole SAML 2.0](active-directory-saml-protocol-reference.md): protocole The SAML 2.0 permet aux applications de fournir une expérience d’authentification unique à leurs utilisateurs.

- [Protocole Web-Federation 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html): Azure Active Directory prend en charge la fédération Web 1.2 conformément à la spécification Web Services Federation Version 1.2. Pour plus d’informations sur le document de métadonnées de fédération, voir [Métadonnées de fédération](active-directory-federation-metadata.md).

- [Types de jeton et réclamer pris en charge](active-directory-token-and-claims.md): vous pouvez utiliser ce guide pour comprendre et évaluer les revendications dans les jetons SAML 2.0 et JSON Web jetons (JWT).

## <a name="videos"></a>Vidéos

### <a name="build"></a>Génération

Ces présentations vue d’ensemble sur le développement d’applications à l’aide des haut-parleurs de fonctionnalité Azure Active Directory qui travaillent directement dans l’équipe d’ingénierie. Les présentations garde rubriques fondamentales, notamment IDMaaS, l’authentification, la fédération d’identité et de l’authentification unique.

- [Identité Microsoft : État de l’Union et Direction Future](https://azure.microsoft.com/documentation/videos/build-2016-microsoft-identity-state-of-the-union-and-future-direction/)
- [Azure Active Directory : Gestion des identités en tant que service pour les applications modernes](https://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications/)
- [Développer des applications web modernes avec Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory/)
- [Développer des applications natives modernes avec Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory/)

### <a name="azure-friday"></a>Azure vendredi
[Azure vendredi](https://azure.microsoft.com/documentation/videos/azure-friday/) est périodique vendredi série de vidéos de 1:1 qui s’engage à vous courte (10 à 15 minutes) entretiens avec des experts sur un grand nombre de rubriques Azure.  Utiliser la fonctionnalité de filtre de Services dans la page pour voir toutes les vidéos Azure Active Directory.

- [Identité Azure 101](https://azure.microsoft.com/documentation/videos/azure-identity-basics/)
- [Identité Azure 102](https://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
- [Identité Azure 103](https://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## <a name="social"></a>Mise en réseau

- [Blog de l’équipe Active Directory](http://blogs.technet.com/b/ad/): derniers développements dans le monde d’Azure Active Directory.

- [Blog de l’équipe Azure Active Directory Graph](http://blogs.msdn.com/b/aadgraphteam): informations Azure Active Directory spécifiques à l’API de graphique.

- [Identité cloud](http://www.cloudidentity.net): idées sur la gestion des identités en tant que service, à partir d’un principal Azure Active Directory PM.  

- [Azure Active Directory sur Twitter](https://twitter.com/azuread): annonces Azure Active Directory dans 140 caractères ou moins.

## <a name="windows-server-on-premises-development"></a>Windows Server en local développement
Pour des instructions sur l’utilisation de développement Windows Server et Active Directory Federation Services (ADFS), voir :

- [AD FS scénarios pour les développeurs](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/overview/ad-fs-scenarios-for-developers): fournit une vue d’ensemble des composants AD FS et son fonctionnement, avec des détails sur les scénarios pris en charge l’authentification/autorisation.
- [AD FS procédures pas à pas](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/ad-fs-development): une liste des articles étape par étape qui fournissent des instructions détaillées sur l’implémentation les flux d’authentification/autorisation connexes.
