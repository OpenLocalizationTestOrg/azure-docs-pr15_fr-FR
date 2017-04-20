<properties
   pageTitle="Bibliothèques de l’authentification Azure Active Directory 2.0 | Microsoft Azure"
   description="Bibliothèques client compatible et bibliothèques de logiciels intermédiaires server et bibliothèque connexe, source et liens exemples, du point de terminaison de version 2.0 Azure Active Directory."
   services="active-directory"
   documentationCenter=""
   authors="skwan"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/30/2016"
   ms.author="skwan;bryanla"/>


# <a name="azure-active-directory-v20-authentication-libraries"></a>Bibliothèques de l’authentification Azure Active Directory version 2.0
Le point de terminaison Azure Active Directory (AD Azure) 2.0 prend en charge les protocoles OAuth 2.0 et 1.0 se connecter OpenID standard. Vous pouvez utiliser plusieurs bibliothèques Microsoft et d’autres organisations avec un point de terminaison de la version 2.0.

Quand vous créez une application qui utilise le point de terminaison version 2.0, nous vous conseillons d’utiliser des bibliothèques qui sont écrits par des experts de domaine protocole qui suivent une méthodologie de cycle de vie de développement de sécurité (SDL), similaire à [celui suivi par Microsoft][Microsoft-SDL]. Si vous décidez de prise en charge coder manuellement pour les protocoles, nous vous recommandons de vous suivez SDL méthodologie et attentif aux considérations relatives à la sécurité dans les spécifications normes pour chaque protocole.

## <a name="types-of-libraries"></a>Types de bibliothèques

Azure AD 2.0 fonctionne avec deux types de bibliothèques :

- **Bibliothèques du client**. Serveurs et clients natifs utilisent bibliothèques client pour obtenir des jetons d’accès pour une ressource, tels que Microsoft Graph l’appel.
- **Bibliothèques de logiciels intermédiaires server**. Applications Web utilisent des bibliothèques de logiciels intermédiaires server pour la connexion utilisateur. API Web utiliser des bibliothèques de logiciels intermédiaires server pour valider les jetons qui sont envoyés par des clients natifs ou par d’autres serveurs.

## <a name="library-support"></a>Prise en charge de la bibliothèque
Étant donné que vous pouvez choisir n’importe quelle bibliothèque conformes aux normes lorsque vous utilisez le point de terminaison version 2.0, il est important de savoir comment accéder à la prise en charge. Pour les problèmes et les demandes de fonctionnalités dans le code de la bibliothèque, contactez le propriétaire de la bibliothèque. Pour les problèmes et les demandes de fonctionnalités dans l’implémentation du protocole côté service, contactez Microsoft.

Bibliothèques il existe deux catégories de prise en charge :

- **Prise en charge Microsoft**. Microsoft fournit des solutions pour ces bibliothèques a et SDL préalable sur ces bibliothèques.
- **Compatibles**. Microsoft a testé ces bibliothèques dans les scénarios de base et confirmé qu’ils fonctionnent avec le point de terminaison version 2.0. Microsoft ne fournit pas de solutions pour ces bibliothèques et n’a pas fait une révision de ces bibliothèques. Problèmes et des demandes de fonctionnalités doivent être adressées à projet open source de la bibliothèque.

Pour une liste de bibliothèques qui fonctionnent avec le point de terminaison version 2.0, consultez les sections suivantes dans cet article.

## <a name="microsoft-supported-client-libraries"></a>Bibliothèques de prise en charge Microsoft client
| Plateforme| Nom de la bibliothèque| Télécharger | Code source | Exemple |
| :-: | :-: | :-: | :-: | :-: |
| .NET, du Windows Store, Xamarin | Bibliothèque d’authentification de Microsoft (MSAL) pour .NET | [Microsoft.Identity.Client (NuGet)][ClientLib-NET-Lib] | [MSAL pour .NET (GitHub)][ClientLib-NET-Repo] | [Exemple de client native ordinateur de bureau de Windows][ClientLib-NET-Sample] |
| Node.js | Microsoft Azure Active Directory Passport.js plug-in | [Passport Azure AD (npm)][ClientLib-Node-Lib] | [Passport Azure AD (GitHub)][ClientLib-Node-Repo] | À venir |

<!--- COMMENTING OUT UNTIL THEY ARE READY
| iOS, Mac | Microsoft Authentication Library (MSAL) for ObjC | In development | In development | In development |
| Android | Microsoft Authentication Library (MSAL) for Android | In development | In development | In development |
| JavaScript | Microsoft Authentication Library (MSAL) for JavaScript | In development | In development | In development |
 -->

## <a name="microsoft-supported-server-middleware-libraries"></a>Bibliothèques de logiciels intermédiaires server pris en charge Microsoft
| Plateforme| Nom de la bibliothèque| Télécharger | Code source | Exemple |
| :-: | :-: | :-: | :-: | :-: |
| .NET 4.x | OWIN OpenID connecter logiciels intermédiaires pour ASP.NET | [Microsoft.Owin.Security.OpenIdConnect (NuGet)][ServerLib-Net4-Owin-Oidc-Lib] | [Projet interconnexions (CodePlex)][ServerLib-Net4-Owin-Oidc-Repo] | [Exemple d’application Web][ServerLib-Net4-Owin-Oidc-Sample] |
| .NET 4.x | Logiciels intermédiaires OWIN PORTEUR OAuth ASP.NET | [Microsoft.Owin.Security.OAuth (NuGet)][ServerLib-Net4-Owin-Oauth-Lib] | [Projet interconnexions (CodePlex)][ServerLib-Net4-Owin-Oauth-Repo] | [Exemple d’API Web][ServerLib-Net4-Owin-Oauth-Sample] |
| Core .NET | OWIN OpenID connecter logiciels intermédiaires pour .NET Core | [Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] | [Sécurité ASP.NET (GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] | [Exemple d’application Web][ServerLib-NetCore-Owin-Oidc-Sample] |
| Core .NET | Logiciels intermédiaires OWIN PORTEUR OAuth Core .NET | [Microsoft.AspNetCore.Authentication.OAuth (NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] | [Sécurité ASP.NET (GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] | À venir |
| Node.js | Microsoft Azure Active Directory Passport.js plug-in | [Passport Azure AD (npm)][ServerLib-Node-Lib] | [Passport Azure AD (GitHub)][ServerLib-Node-Repo] | [Exemple d’application Web][ServerLib-Node-Sample] |
<!--- COMMENTING UNTIL SAMPLE IS AVAILABLE
| .NET 4.x, .NET Core | JSON Web Token Handler for .NET | [System.IdentityModel.Tokens.Jwt (NuGet)][ServerLib-Net-Jwt-Lib] | [Azure AD identity model extensions for .NET (GitHub)][ServerLib-Net-Jwt-Repo] | Coming soon |
--->
## <a name="compatible-client-libraries"></a>Bibliothèques de client compatible
| Plateforme| Nom de la bibliothèque | Version testée | Code source | Exemple |
| :-: | :-: | :-: | :-: | :-: |
| Android | [OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/wiki) | 0.2.1 | [OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) | [Exemple d’application native](active-directory-v2-devquickstarts-android.md) |
| iOS | [NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) | 1.2.8 | [NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) | [Exemple d’application native](active-directory-v2-devquickstarts-ios.md)|
| JavaScript | [Hello.js](https://adodson.com/hello.js/) | question 1.13.5 | [Hello.js](https://github.com/MrSwitch/hello.js) | À venir |
| Ballon de Python | [Ballon OAuthlib](https://github.com/lepture/flask-oauthlib) | 0.9.3 | [Ballon OAuthlib](https://github.com/lepture/flask-oauthlib) | À venir |
| Ruby | [OmniAuth](https://github.com/omniauth/omniauth/wiki) | omniauth:1.3.1</br>omniauth-oauth2:1.4.0 | [OmniAuth](https://github.com/omniauth/omniauth)</br>[Oauth2 ne OmniAuth](https://github.com/intridea/omniauth-oauth2) | À venir |
<!--- REMOVING BRANDON'S FOR NOW
|  |  |  |  |  |
| Android | [OAuth2 Client](https://github.com/wuman/android-oauth-client) |   | [OAuth2 Client](https://github.com/wuman/android-oauth-client)  | Coming soon  |
| Java | [WSO2 Identity Server](https://docs.wso2.com/display/IS500/Introducing+the+Identity+Server) | [Version 5.2.0](http://wso2.com/products/identity-server/) | [Source](https://docs.wso2.com/display/IS500/Building+from+Source) | [Samples index](https://docs.wso2.com/display/IS500/Samples)  |
| Java | [Java Gluu Server](https://gluu.org/docs/) |   | [oxAuth](https://github.com/GluuFederation/oxAuth)  | Coming soon |
| Node.js | [NPM passport-openidconnect](https://www.npmjs.com/package/passport-openidconnect) | 0.0.1  | [Passport-OpenID Connect](https://github.com/jaredhanson/passport-openidconnect) | Coming soon  |
| PHP | [OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP) |   | [OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP)  | Coming soon  |
-->

## <a name="compatible-server-middleware-libraries"></a>Bibliothèques de logiciels intermédiaires serveur compatibles
À venir

## <a name="related-content"></a>Contenu associé
Pour plus d’informations sur le point de terminaison de version 2.0 Azure Active Directory, consultez la [vue d’ensemble du version 2.0 de modèle d’application Azure AD][AAD-App-Model-V2-Overview].

Pour nous aider à affiner et mettre en forme notre contenu, utilisez la fonctionnalité de commentaires Disqus à la fin de cet article pour fournir des commentaires.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: active-directory-appmodel-v2-overview.md
[ClientLib-NET-Lib]: http://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:
[ClientLib-Iosmac-Lib]:
[ClientLib-Iosmac-Repo]:
[ClientLib-Iosmac-Sample]:
[ClientLib-Android-Lib]:
[ClientLib-Android-Repo]:
[ClientLib-Android-Sample]:
[ClientLib-Js-Lib]:
[ClientLib-Js-Repo]:
[ClientLib-Js-Sample]:
[Microsoft-SDL]: http://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-v2-devquickstarts-node-web/
