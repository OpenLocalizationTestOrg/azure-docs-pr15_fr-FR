<properties
   pageTitle="Bibliothèques d’authentification Azure Active Directory | Microsoft Azure"
   description="La bibliothèque terme (Azure AD authentification ADAL) permet aux développeurs d’applications faciliter l’authentification des utilisateurs vers le cloud à client ou Active Directory (AD) local et puis obtenez jetons d’accès pour sécuriser les appels d’API."
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/11/2016"
   ms.author="mbaldwin" />

# <a name="azure-active-directory-authentication-libraries"></a>Bibliothèques d’authentification Azure Active Directory

L’authentification Azure AD bibliothèque (terme ADAL) permet aux développeurs d’applications clientes faciliter l’authentification des utilisateurs vers le cloud ou Active Directory (AD) local et pour obtenir, jetons d’accès pour sécuriser les appels d’API. Terme ADAL comporte de nombreuses fonctionnalités que l’authentification rendre plus facile pour les développeurs, tels que prise en charge asynchrone, un cache de jetons configurable qui stocke les jetons d’accès et les jetons d’actualisation, l’actualisation jeton automatique lorsqu’un jeton d’accès expire et un jeton d’actualisation est disponible et bien plus encore. En gérant la plupart de la complexité, terme ADAL peut aider un développeur à se concentrer sur la logique métier dans leur application et sécuriser facilement des ressources sans être un expert de sécurité.

Terme ADAL est disponible sur un grand nombre de plateformes.

|Plateforme|Nom du package|Client/serveur|Télécharger|Code source|Documentation et exemples|
|---|---|---|---|---|---|
|Client .NET, du Windows Store, UWP, Xamarin iOS et Android|Bibliothèque d’authentification Active Directory (terme ADAL) pour .NET v3 |Client|[Microsoft.IdentityModel.Clients.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory)|[ADAL pour .NET (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet)|[Documentation](https://docs.microsoft.com/active-directory/adal/microsoft.identitymodel.clients.activedirectory)|
|.NET client, du Windows Store, Windows Phone 8.1 |Bibliothèque d’authentification Active Directory (terme ADAL) pour .NET v2 |Client|[Microsoft.IdentityModel.Clients.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.2)|[ADAL pour .NET (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.2)|[Documentation](https://docs.microsoft.com/active-directory/adal/v2/microsoft.identitymodel.clients.activedirectory)|
|JavaScript|Bibliothèque d’authentification Active Directory (terme ADAL) pour JavaScript|Client|[ADAL pour JavaScript (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-js)|[ADAL pour JavaScript (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-js)|Exemple : [SinglePageApp-DotNet (Github)](https://github.com/AzureADSamples/SinglePageApp-DotNet)|
|OS X, iOS|Bibliothèque d’authentification Active Directory (terme ADAL) pour objectif-c|Client|[ADAL pour objectif-C (CocoaPods)](http://cocoadocs.org/docsets/ADAL/)|[ADAL pour objectif-c (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-objc)|Exemple : [NativeClient-iOS (Github)](https://github.com/AzureADSamples/NativeClient-iOS)|
|Android|Bibliothèque d’authentification Active Directory (terme ADAL) pour Android|Client|[ADAL pour Android (le référentiel Central)](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/)|[ADAL pour Android (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-android)|Exemple : [NativeClient-Android (Github)](https://github.com/AzureADSamples/NativeClient-Android)|
|Node.js|Bibliothèque d’authentification Active Directory (terme ADAL) pour Node.js|Client|[Terme ADAL pour Node.js (npm)](https://www.npmjs.com/package/adal-node)|[ADAL pour Node.js (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs)|Exemple : [WebAPI-Nodejs (Github)](https://github.com/AzureADSamples/WebAPI-Nodejs)|
|Node.js|Microsoft Azure Active Directory Passport authentification logiciels intermédiaires nœud|Client|[Azure Active Directory Passport Node.js (npm)](https://www.npmjs.com/package/passport-azure-ad)|[Azure Active Directory pour Node.js (Github)](https://github.com/AzureAD/passport-azure-ad)||
|Java|Bibliothèque d’authentification Active Directory (terme ADAL) pour Java|Client|[ADAL pour Java (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-java)|[ADAL pour Java (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-java)||
|.NET|Extensions de protocole identité pour Microsoft .NET Framework 4.5|Serveur|[Microsoft.IdentityModel.Protocol.Extensions (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions)|[Extensions de modèle Azure AD identité pour .NET (Github)](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)||
|.NET|Gestionnaire de jeton Web JSON pour Microsoft .net Framework 4.5|Serveur|[System.IdentityModel.Tokens.Jwt (NuGet)](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt)|[Extensions de modèle Azure AD identité pour .NET (Github)](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)||
|.NET|Logiciels intermédiaires OWIN qui permet à une application à utiliser la technologie de Microsoft pour l’authentification.|Serveur|[Microsoft.Owin.Security.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)||
|.NET|Logiciels intermédiaires OWIN qui permet à une application à utiliser OpenIDConnect pour l’authentification.|Serveur|[Microsoft.Owin.Security.OpenIdConnect (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)|Exemple : [WebApp OpenIDConnecty DotNet (Github)](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet)|
|.NET|Logiciels intermédiaires OWIN qui permet à une application à utiliser la fédération Web pour l’authentification.|Serveur|[Microsoft.Owin.Security.WsFederation (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)|Exemple : [WebApp WSFederation DotNet (Github)](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet)|

## <a name="scenarios"></a>Scénarios

Voici trois scénarios courants dans lequel terme ADAL peut être utilisé pour l’authentification.  

### <a name="authenticating-users-of-a-client-application-to-a-remote-resource"></a>L’authentification des utilisateurs d’une Application cliente à une ressource distante

Dans ce scénario, un développeur a un client, tel qu’une application WPF, qui doit accéder à une ressource à distance sécurisée par Azure AD, par exemple un site web API. Il dispose d’un abonnement Azure, sait comment appeler l’API web en aval et connaît le client Azure AD qui utilise l’API web. Par conséquent, il peut utiliser terme ADAL pour faciliter l’authentification avec Azure AD, en entièrement délégation de l’expérience d’authentification à terme ADAL ou en explicitement de gestion des informations d’identification utilisateur. ADAL rend plus facile authentifier l’utilisateur, obtenir un jeton d’accès et le jeton d’actualisation à partir d’Azure AD, puis utilisez le jeton d’accès pour vérifier les demandes de sur le web API.

Pour obtenir un exemple de code qui montre ce scénario en utilisant l’authentification à Azure Active Directory, voir [Native Application cliente WPF API Web](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-server-application-to-a-remote-resource"></a>Une Application serveur pour une ressource distante d’authentification

Dans ce scénario, un développeur a une application en cours d’exécution sur un serveur qui doit accéder à une ressource à distance sécurisée par Azure AD, par exemple un site web API. Il dispose d’un abonnement Azure sait comment appeler le service en aval et sait qu'utilise le client Azure AD l’API web. Par conséquent, il peut utiliser terme ADAL pour faciliter l’authentification avec Azure AD par explicitement de gestion des informations d’identification de l’application. ADAL rend plus facile récupérer un jeton d’Azure Active Directory à l’aide des informations d’identification du client de l’application, puis utilisez ce jeton pour rendre demande à l’API web. Terme ADAL gère également la gestion de la durée de vie du jeton access en la mise en cache et en renouvelant si nécessaire. Pour obtenir un exemple de code qui montre ce scénario, voir [Application Console d’API Web](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-server-application-on-behalf-of-a-user-to-access-a-remote-resource"></a>Le couple une Application serveur au nom d’un utilisateur pour accéder à une ressource à distance

Dans ce scénario, un développeur a une application en cours d’exécution sur un serveur qui doit accéder à une ressource à distance sécurisée par Azure AD, par exemple un site web API. La demande doit également être apportées au nom d’un utilisateur dans Active Directory Azure. Il dispose d’un abonnement Azure sait comment appeler l’API web en aval et sait Azure Active Directory du client le service utilise. Une fois l’utilisateur authentifié à l’application web, l’application peut obtenir un code d’autorisation pour l’utilisateur à partir d’Azure AD. L’application web pouvez ensuite utiliser terme ADAL pour obtenir un jeton d’accès et actualiser jeton au nom d’un utilisateur avec les références de code et le client d’autorisations associés avec l’application à partir d’Azure AD. Une fois que l’application web est en possession du jeton d’accès, il peut appeler l’API web jusqu'à ce que le jeton arrive à expiration. Lorsque le jeton arrive à expiration, l’application web peut utiliser terme ADAL pour obtenir un nouveau jeton d’accès à l’aide de l’actualisation jeton qui a été reçu précédemment.


## <a name="see-also"></a>Voir aussi

[Guide du développeur de Azure Active Directory](active-directory-developers-guide.md)

[Scénarios d’authentification pour Azure Active directory](active-directory-authentication-scenarios.md)

[Exemples de code Azure Active Directory](active-directory-code-samples.md)
