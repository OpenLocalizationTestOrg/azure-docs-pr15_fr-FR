<properties
   pageTitle="Exemples de Code Azure Active Directory | Microsoft Azure"
   description="Un index des exemples de code Azure Active Directory, organisés par scénario."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="priyamohanram"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>

# <a name="azure-active-directory-code-samples"></a>Exemples de Code Azure Active Directory

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Vous pouvez utiliser Microsoft Azure Active Directory (AD Azure) pour ajouter l’authentification et l’autorisation à vos applications web et des API web. Cette section vous des liens vers des exemples de code qui vous montrent comment procéder et des extraits de code que vous pouvez utiliser dans vos applications. Dans la page d’exemples de code, vous trouverez détaillées en lecture-me rubriques d’aident à la configuration requise, d’installation et la configuration. Et le code est commenté pour vous aider à comprendre les sections critiques.

Pour mieux comprendre le scénario de base pour chaque type d’échantillon, voir scénarios d’authentification pour Azure AD.

Contribuer à nos exemples sur GitHub : [Microsoft Azure Active Directory exemples et la Documentation](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="web-browser-to-web-application"></a>Navigateur Web pour une Application Web

Ces exemples montrent comment créer une application web qui achemine le navigateur de l’utilisateur pour les se connecter à Azure AD.



| Langue/plate-forme | Exemple | Description
| ----------------- | ------ | -----------
| C# / .NET | [WebApp-OpenIDConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) | Utiliser OpenID Connect (ASP.Net OpenID connecter OWIN logiciels intermédiaires) pour l’authentification des utilisateurs à partir d’un client Azure AD.
| C# / .NET | [WebApp-pouvant être partagée-OpenIdConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) | Un multi-client application web MVC .NET qui utilise OpenID se connecter (ASP.Net OpenID connecter OWIN logiciels intermédiaires) pour l’authentification des utilisateurs à partir de plusieurs clients Azure AD.
| C# / .NET | [WebApp-WSFederation-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) | Utilisez Web-Federation (ASP.Net Web-Federation OWIN logiciels intermédiaires) pour l’authentification des utilisateurs à partir d’un client Azure AD.






## <a name="single-page-application-spa"></a>Application de Page unique (SPA)

Cet exemple indique comment rédiger une application seule page sécurisée avec Azure AD.  

| Langue/plate-forme | Exemple | Description
| ----------------- | ------ | -----------
| JavaScript, C# / .NET | [SinglePageApp DotNet](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | Utilisez terme ADAL pour JavaScript et Azure AD pour sécuriser une application basée sur les AngularJS une seule page implémentée avec un principal de l’API web ASP.NET.


## <a name="native-application-to-web-api"></a>Application native pour API Web

Ces exemples de code montrent comment créer des applications clientes native qu’appeler web API qui est protégés par Azure AD. Ils utilisent [Une bibliothèque terme (Azure AD authentification ADAL)](active-directory-authentication-libraries.md) et [2.0 OAuth dans Azure Active Directory](https://msdn.microsoft.com/library/azure/dn645545.aspx).

| Langue/plate-forme | Exemple | Description
| ----------------- | ------ | -----------
| JavaScript | [NativeClient-MultiTarget-Cordova](https://github.com/Azure-Samples/active-directory-cordova-multitarget) | Utiliser le plug-in ADAL pour Apache Cordova pour générer une application Cordova Apache qui appelle une API web et utilise Azure AD pour l’authentification.
| C# / .NET | [NativeClient DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) | Une application WPF .NET qui appelle une API web qui est sécurisée à l’aide de Azure AD.
| C# / .NET | [NativeClient WindowsStore](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) | Une application du Windows Store qui appelle une API web qui est sécurisée avec Azure AD.
| C# / .NET | [NativeClient-WebAPI-pouvant être partagée-WindowsStore](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) | Une application du Windows Store appel à un site web multi-client API est sécurisé avec Azure AD.
| C# / .NET | [WebAPI-OnBehalfOf-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof) | Application cliente native qui appelle une API, qui obtient un jeton pour agir au nom de l’utilisateur d’origine et utilise ensuite le jeton pour appeler un autre site web API web.
| C# / .NET | [NativeClient WindowsPhone8.1](https://github.com/Azure-Samples/active-directory-dotnet-windowsphone-8.1) | Une application du Windows Store pour Windows Phone 8.1 qui appelle une API web qui est sécurisée par Azure AD.
| ObjC | [NativeClient iOS](https://github.com/Azure-Samples/active-directory-ios) | Une application iOS qui appelle une API web qui requiert Azure AD pour l’authentification.
| C# / .NET | [WebAPI-ManuallyValidateJwt-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation) | Une application cliente native qui inclut une logique pour traiter un jeton JWT dans un site web API, au lieu d’utiliser les logiciels intermédiaires OWIN.
| C# / Xamarin | [NativeClient-Xamarin-Android](https://github.com/Azure-Samples/active-directory-xamarin-android) | Une liaison Xamarin à la native Azure AD authentification bibliothèque (terme ADAL) pour la bibliothèque Android.
| C# / Xamarin | [NativeClient-Xamarin-iOS](https://github.com/Azure-Samples/active-directory-xamarin-ios) | Une liaison Xamarin à la native Azure AD authentification bibliothèque (terme ADAL) pour iOS.
| C# / Xamarin | [NativeClient-MultiTarget-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-multitarget) | Un projet Xamarin qui cible cinq plateformes et appelle un site web API qui est sécurisé par Azure AD.
| C# / .NET | [NativeClient sans tête DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-headless) | Une application native qui effectue l’authentification non interactive et appelle un site web API qui est sécurisée par Azure AD.



## <a name="web-application-to-web-api"></a>Application Web aux API Web

Ces exemples de code afficher comment utiliser [OAuth 2.0 dans Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx) pour créer des applications web appeler web API qui est protégés par Azure AD.

| Langue/plate-forme | Exemple | Description
| ----------------- | ------ | -----------
| C# / .NET | [WebApp-WebAPI-OpenIDConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect) | Appeler un API web avec des autorisations de l’utilisateur de connecté dans.
|  C# / .NET | [DotNet du AppIdentity oauth2 ne WebApp WebAPI](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity) | Appeler un API web avec des autorisations de l’application.
| C# / .NET | [Dotnet du identité de l’utilisateur oauth2 ne WebApp WebAPI](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | Ajouter une autorisation avec [OAuth 2.0 dans Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx) pour une application web existante afin de pouvoir appeler un API web.
| JavaScript | [WebAPI Nodejs](https://github.com/Azure-Samples/active-directory-node-webapi) | Configurer un service de l’API REST est intégré à Azure AD pour la protection des API. Comprend un serveur Node.js avec une API Web.
| C# / .NET | [DotNet du OpenIdConnect pouvant être partagée WebApp WebAPI](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-multitenant-openidconnect) |  Un client multiples MVC web application qui utilise OpenID se connecter (ASP.Net OpenID connecter OWIN logiciels intermédiaires) pour l’authentification des utilisateurs à partir d’un client Azure AD. Utilise un code d’autorisation pour appeler l’API Graph.

## <a name="server-or-daemon-application-to-web-api"></a>Serveur ou une Application de processus à l’API Web

Ces exemples de code montrent comment créer une application serveur ou un processus qui obtient des ressources à partir d’un site web API à l’aide de la [Bibliothèque terme (Azure AD authentification ADAL)](active-directory-authentication-libraries.md) et [2.0 OAuth dans Azure Active Directory](https://msdn.microsoft.com/library/azure/dn645545.aspx).

| Langue/plate-forme | Exemple | Description
| ----------------- | ------ | -----------
| C# / .NET | [Processus DotNet](https://github.com/Azure-Samples/active-directory-dotnet-daemon) | Une application console appelle une API web. Les informations d’identification client sont un mot de passe.
| C# / .NET | [Processus-CertificateCredential-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential) | Une application console qui appelle une API web. Les informations d’identification client sont un certificat.


## <a name="calling-azure-ad-graph-api"></a>Appel d’API Azure AD Graph

Ces exemples de code expliquent comment créer des applications faisant appel à l’API Azure AD Graph pour lire et écrire des données de l’annuaire.

| Langue/plate-forme | Exemple | Description
| ----------------- | ------ | -----------
| Java | [WebApp-GraphAPI-Java](https://github.com/Azure-Samples/active-directory-java-graphapi-web) | Une application web qui utilise l’API Graph pour accéder aux données de l’annuaire Azure AD.
| PHP | [WebApp-GraphAPI-PHP](https://github.com/Azure-Samples/active-directory-php-graphapi-web) | Une application web qui utilise l’API Graph pour accéder aux données de l’annuaire Azure AD.
| C# / .NET | [WebApp-GraphAPI-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-web) | Une application web qui utilise l’API Graph pour accéder aux données de l’annuaire Azure AD.
| C# / .NET | [ConsoleApp-GraphAPI-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-console) | Cette application console montre les appels courantes de lecture et d’écriture à l’API de graphique et indique comment exécuter attribution des licences d’utilisateur et mettre à jour les photos miniatures et les liens d’un utilisateur.
| C# / .NET | [ConsoleApp-GraphAPI-DiffQuery-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-diffquery) | Une application console qui utilise la requête différentielle dans l’API Graph pour obtenir périodiques modifications aux objets utilisateur dans un client Azure AD.
| C# / .NET | [WebApp-GraphAPI-DirectoryExtensions-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-directoryextensions-web) | Une application MVC utilise des requêtes de l’API Graph pour générer un organigramme simple société.
| PHP | [WebApp-GraphAPI-DirectoryExtensions-PHP](https://github.com/Azure-Samples/active-directory-php-graphapi-directoryextensions-web) | Une application PHP qui appelle l’API Graph pour vous inscrire une extension et puis lire, mettre à jour et supprimer des valeurs dans l’attribut d’extension.


## <a name="authorization"></a>Autorisation

Ces exemples de code montrent comment utiliser Azure AD pour l’autorisation.

| Langue/plate-forme | Exemple | Description
| ----------------- | ------ | -----------
| C# / .NET | [WebApp-GroupClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) | Effectuer le contrôle d’accès basé sur un rôle (RBAC) à l’aide de revendications de groupe Azure Active Directory dans une application qui est intégrée à Azure AD.
| C# / .NET | [WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Effectuer le contrôle d’accès basé sur un rôle (RBAC) à l’aide de rôles d’application Azure Active Directory dans une application qui est intégrée à Azure AD.


## <a name="legacy-walkthroughs"></a>Procédures pas à pas héritées

Ces procédures pas à pas utiliser la technologie légèrement plus ancienne, mais toujours peuvent être utiles.

| Langue/plate-forme | Exemple | Description
| ----------------- | ------ | -----------
| C# / .NET | [Rôles et et l’autorisation dans une Application AD Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=331694) | Effectuer l’autorisation basée sur les rôles (RBAC) et autorisations et dans une application qui est intégrée à Azure AD.
| C# / .NET |  [Authentification AAL - application du Windows Store service reste-](http://go.microsoft.com/fwlink/?LinkId=330605) |  [Bibliothèque terme (Azure AD authentification ADAL)](active-directory-authentication-libraries.md) (anciennement AAL) pour Windows Store bêta permet d’ajouter des fonctionnalités d’authentification utilisateur pour une application du Windows Store.
| C# / .NET | [Authentification de terme ADAL - application Native au service reste - avec AAD via la boîte de dialogue navigateur](http://go.microsoft.com/fwlink/?LinkId=259814) |  [Bibliothèque terme (Azure AD authentification ADAL)](active-directory-authentication-libraries.md) permet d’ajouter des fonctionnalités d’authentification utilisateur à un client WPF.
| C# / .NET | [Authentification de terme ADAL - application Native au service reste - ACS via la boîte de dialogue navigateur](http://code.msdn.microsoft.com/AAL-Native-App-to-REST-de57f2cc) |  Utiliser [Bibliothèque terme (Azure AD authentification ADAL)](active-directory-authentication-libraries.md) et [Contrôle d’accès Service 2.0 (ACS)](http://msdn.microsoft.com/library/azure/hh147631.aspx) pour ajouter des fonctionnalités d’authentification utilisateur à un client WPF.
| C# / .NET | [ADAL - authentification de serveur à serveur](http://go.microsoft.com/fwlink/?LinkId=259816) | Utilisez [Bibliothèque terme (Azure AD authentification ADAL)](active-directory-authentication-libraries.md) pour sécuriser des appels de service à partir d’un processus côté serveur à un service de MVC4 Web API REST.
| C# / .NET | [Ajout d’authentification à votre Application Web à l’aide de Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) | Configurer une application .NET pour exécuter web SSO par rapport à votre annuaire de l’entreprise Azure AD.
| C# / .NET | [Développement d’Applications Web multiples client avec AD Azure](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) | Azure AD permet d’ajouter à l’authentification unique et aux fonctionnalités d’access répertoire d’une application .NET fonctionne par plusieurs.
JAVA | [Exemple d’application Java pour Azure AD Graph API](http://go.microsoft.com/fwlink/?LinkId=263969) | Utiliser l’API de graphique pour accéder aux données de répertoire à partir d’Azure AD.
PHP | [Exemple d’application PHP pour Azure AD Graph API](http://code.msdn.microsoft.com/PHP-Sample-App-For-Windows-228c6ddb) | Utiliser l’API de graphique pour accéder aux données de répertoire à partir d’Azure AD.
| C# / .NET | [Exemple d’application pour Azure AD Graph API](http://go.microsoft.com/fwlink/?LinkID=262648) | Utiliser l’API de graphique pour accéder aux données de répertoire à partir d’Azure AD.
| C# / .NET | [Exemple d’application pour Azure AD Graph différentielle requête](http://go.microsoft.com/fwlink/?LinkId=275398) | Utilisez la requête différentielle dans l’API Graph pour obtenir périodiques modifications aux objets utilisateur dans un client Azure AD.
| C# / .NET | [Exemple d’application pour l’intégration d’Application client multiples Cloud pour Azure AD](http://go.microsoft.com/fwlink/?LinkId=275397) | Intégrer une application cliente multiples dans Azure AD.
| C# / .NET | [Sécuriser une Application du Windows Store et le Service Web REST utilise Azure Active Directory](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) | Créer une ressource d’API web simple et une application cliente du Windows Store utilise Azure Active Directory et de [Bibliothèque terme (Azure AD authentification ADAL)](active-directory-authentication-libraries.md).
| C# / .NET| [À l’aide de l’API Graph pour interroger Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-web) | Configurer une application Microsoft .NET à l’API Azure AD graphique permet d’accéder aux données à partir d’un répertoire client Azure AD.

## <a name="see-also"></a>Voir aussi

##### <a name="other-resources"></a>Autres ressources

[Guide du développeur Azure Active Directory](active-directory-developers-guide.md)

[Azure AD Graph API conceptuel et référence](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Bibliothèque de l’API d’assistance Azure AD Graph](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)

