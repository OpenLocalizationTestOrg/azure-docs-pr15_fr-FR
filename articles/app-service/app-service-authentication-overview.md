<properties
    pageTitle="Authentification et autorisation dans le Service d’application Azure | Microsoft Azure"
    description="Vue d’ensemble de l’authentification et de référence conceptuelle / fonctionnalité d’autorisation pour le Service d’application Azure"
    services="app-service"
    documentationCenter=""
    authors="mattchenderson"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="mahender"/>

# <a name="authentication-and-authorization-in-azure-app-service"></a>Authentification et autorisation dans le Service d’application Azure

## <a name="what-is-app-service-authentication--authorization"></a>Quelle est l’application Service authentification / autorisation ?

Application Service authentification / autorisation est une fonctionnalité qui permet à votre application pour vous connecter aux utilisateurs afin que vous n’êtes pas obligé de modifier le code sur le serveur principal de l’application. Il propose un moyen facile de protéger votre application et utiliser des données par utilisateur.

Service d’application utilise l’identité fédérée, dans lequel un fournisseur d’identité tiers stocke les comptes et authentifie les utilisateurs. L’application repose sur les informations d’identité du fournisseur afin que l’application ne doit pas stocker ces informations elle-même. Application Service prend en charge cinq fournisseurs d’identité prêts à l’emploi : Azure Active Directory, Facebook, Google, Account Microsoft et Twitter. Votre application peut utiliser un nombre quelconque de ces fournisseurs d’identité pour offrir à vos utilisateurs avec les options de la façon dont ils se connecter. Pour développer la prise en charge intégrée, vous pouvez intégrer un autre fournisseur d’identité ou [votre propre solution identité personnalisée][custom-auth].

Si vous voulez commencer immédiatement, voir un des didacticiels suivants :

- [Ajouter une authentification dans votre application iOS] [ iOS] (ou [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms]ou [Cordova])
- [Authentification des utilisateurs pour les applications de l’API dans le Service d’application Azure][apia-user]
- [Prise en main Azure Application Service - partie 2][web-getstarted]

## <a name="how-authentication-works-in-app-service"></a>Fonctionnement de l’authentification dans le Service d’application

Pour l’authentification en utilisant l’un des fournisseurs d’identité, vous devez tout d’abord configurer le fournisseur d’identité à connaître votre application. Le fournisseur d’identité fournira puis ID et codes secrets que vous fournissez au Service d’application. Cette action conclut la relation d’approbation afin que l’application Service puissent valider assertions utilisateur, tels que les jetons d’authentification, au fournisseur d’identité.

Pour connecter un utilisateur en utilisant l’un de ces fournisseurs, l’utilisateur doit être redirigé vers un point de terminaison qui se connecte aux utilisateurs pour ce fournisseur. Si les clients utilisent un navigateur web, vous pouvez avoir application Service diriger automatiquement tous les utilisateurs non authentifiés au point de terminaison qui se connecte aux utilisateurs. Dans le cas contraire, vous devrez diriger vos clients dans `{your App Service base URL}/.auth/login/<provider>`, où `<provider>` est une des valeurs suivantes : aad, facebook, google, microsoft ou twitter. Scénarios mobile et API sont décrits dans les sections plus loin dans cet article.

Les utilisateurs qui peuvent interagir avec votre application via un navigateur web aura un cookies définir afin qu’ils restent authentifiés comme elles parcourent votre application. Pour d’autres types de clients, par exemple mobile, un jeton de web JSON (JWT), qui doivent être présenté dans le `X-ZUMO-AUTH` en-tête, sera émise au client. Le client Mobile applications SDK traitera à votre place. Par ailleurs, un jeton d’identité Azure Active Directory ou jeton d’accès peut être directement inclus dans le `Authorization` en-tête en tant que [PORTEUR OAuth](https://tools.ietf.org/html/rfc6750).

Application Service valide les cookies ou jeton que votre application émet pour authentifier les utilisateurs. Pour restreindre l’accès à votre application, consultez la section [autorisation](#authorization) plus loin dans cet article.

### <a name="mobile-authentication-with-a-provider-sdk"></a>Authentification mobile avec un fournisseur SDK

Une fois que tout est configuré sur le serveur principal, vous pouvez modifier les clients mobiles à se connecter avec l’application de Service. Il existe deux approches ici :

- Utiliser un SDK qui publie un fournisseur d’identité donnée pour définir l’identité et d’accéder au Service d’application.
- Utiliser une seule ligne de code afin que le client Mobile applications SDK de se connecter aux utilisateurs.

>[AZURE.TIP] La plupart des applications doit utiliser un fournisseur SDK pour obtenir une expérience plus cohérente lorsque les utilisateurs se connectent, en utilisant la prise en charge de l’actualisation et pour obtenir d’autres avantages qui spécifie le fournisseur.

Lorsque vous utilisez un fournisseur SDK, les utilisateurs peuvent se connecter à une expérience est plus étroitement intégré avec le système d’exploitation que l’application est en cours d’exécution. Cela vous donne également un jeton de fournisseur et certaines informations utilisateur sur le client, ce qui facilite la consommer graph API et personnaliser l’expérience utilisateur. Il peut arriver que sur les blogs et les forums, vous verrez ceci appelé « flux de clients » ou « flux client dirigé », car le code sur le client se connecte aux utilisateurs et le code client a accès à un jeton de fournisseur.

Une fois un jeton de fournisseur obtenu, elle doit être envoyée au Service d’application de validation. Après que l’application de Service valide le jeton, le Service application crée un nouveau jeton de Service d’application qui est renvoyé au client. Le client Mobile applications SDK comporte les méthodes d’assistance pour gérer cette exchange et lier automatiquement le jeton à toutes les requêtes vers le serveur principal application. Les développeurs peuvent également conserver une référence au jeton du fournisseur s’ils le souhaitent.

### <a name="mobile-authentication-without-a-provider-sdk"></a>Authentification mobile sans un fournisseur SDK

Si vous ne souhaitez pas configurer un fournisseur de kit de développement logiciel, vous pouvez autoriser la fonctionnalité Mobile applications de Service d’application Azure pour vous connecter à votre place. Le client Mobile applications SDK vous ouvrez un affichage web pour le fournisseur de votre choix et connectez-vous à l’utilisateur. Il peut arriver que sur les blogs et les forums, vous verrez ceci appelées « serveur flux » ou « flux dirigées par le serveur », car le serveur gère le processus qui se connecte aux utilisateurs, et le Kit de développement logiciel client reçoit jamais le jeton de fournisseur.

Code pour démarrer ce flux est inclus dans le didacticiel d’authentification pour chaque plate-forme. À la fin du flux, le Kit de développement logiciel client possède un jeton de Service d’application, puis le jeton est automatiquement joint à toutes les requêtes vers le serveur principal application.

### <a name="service-to-service-authentication"></a>Authentification de service à service

Bien que vous pouvez autoriser l’accès à votre application, vous pouvez également faire confiance appeler vos propres API d’une autre application. Par exemple, vous pouvez avoir une application web appeler une API dans une autre application web. Dans ce scénario, vous utilisez les informations d’identification pour un compte de service au lieu des informations d’identification utilisateur pour obtenir un jeton. Un compte de service est également appelé un *service principal* dans jargon Azure Active Directory et l’authentification qui utilise un tel compte est également connue sous un scénario de service à service.

>[AZURE.IMPORTANT] Comme les applications mobiles s’exécutent sur les périphériques client, des applications mobiles effectuez _pas_ nombre sous forme de confiance des applications et ne devez pas utiliser un flux principal de service. En revanche, ils doivent utiliser un flux d’utilisateur qui a été décrit précédemment.

Scénarios de service à service, application Service pouvez protéger votre application à l’aide de Azure Active Directory. L’application appelante doit simplement fournir un jeton d’autorisation principal service Azure Active Directory obtenue en fournissant au client ID et secrète à partir d’Azure Active Directory du client. Exemple de ce scénario qui utilise l’API ASP.NET applications est expliqué par le didacticiel, [l’authentification principale du Service pour les applications de l’API][apia-service].

Si vous voulez utiliser l’authentification du Service d’application pour gérer un scénario de service à service, vous pouvez utiliser les certificats clients ou l’authentification de base. Pour plus d’informations sur les certificats client dans Azure, voir [Procédure pour configurer TLS commun l’authentification pour les applications Web](../app-service-web/app-service-web-configure-tls-mutual-auth.md). Pour plus d’informations sur l’authentification de base dans ASP.NET, voir [Filtres d’authentification 2 de l’API Web ASP.NET](http://www.asp.net/web-api/overview/security/authentication-filters).

Authentification de compte de service d’une application de logique de Service d’application pour une application API est un cas spécial qui est détaillé dans [à l’aide de votre API personnalisé hébergé sur Service d’application avec les applications logique](../app-service-logic/app-service-logic-custom-hosted-api.md).

## <a name="authorization"></a>Fonctionnement des autorisations dans le Service d’application

Vous contrôlez totalement les requêtes qui peuvent accéder à votre application. Application Service authentification / autorisation peut être configurée avec un des problèmes suivants :

- Autoriser uniquement les requêtes authentifiés accéder à votre application.

    Si un navigateur reçoit une demande anonyme, application Service vous redirige vers une page pour le fournisseur d’identité que vous choisissez afin que les utilisateurs de se connecter. Si la demande provient d’un appareil mobile, la réponse renvoyée est une réponse HTTP _401 non autorisé_ .

    Avec cette option, vous n’avez pas besoin d’écrire du code d’authentification tout dans votre application. Si vous avez besoin d’autorisation plus précie, les informations sur l’utilisateur sont disponibles pour votre code.

- Autoriser toutes les demandes atteindre votre application, mais valider les demandes authentifiés et transmettre les informations d’authentification dans les en-têtes HTTP.

    Cette option diffère décisions d’autorisation à votre code de l’application. Il offre plus de souplesse dans la gestion des demandes anonymes, mais vous devez écrire du code.

- Autoriser toutes les demandes d’atteindre votre application et aucun agir sur les informations d’authentification dans les requêtes.

    Dans ce cas, l’authentification / fonctionnalité d’autorisation est désactivée. Les tâches d’authentification et d’autorisation sont entièrement jusqu'à votre code de l’application.

Les comportements précédente sont contrôlées par l’option **Action à effectuer lors de la requête n’est pas authentifié** dans le portail Azure. Si vous sélectionnez * *se connecter avec *le nom du fournisseur* **, toutes les demandes doivent être authentifié.** Autoriser les requêtes (aucune action) ** diffère la décision d’autorisation à votre code, mais il toujours fournit des informations d’authentification. Si vous souhaitez utiliser votre code pour gérer tous les éléments, vous pouvez désactiver l’authentification / fonctionnalité d’autorisation.

## <a name="working-with-user-identities-in-your-application"></a>Utilisation des identités d’utilisateurs dans votre application

Application Service passe certaines informations utilisateur à votre application à l’aide des en-têtes spéciaux. Les requêtes externes interdisent ces en-têtes et sera uniquement if présenter définie par l’application de Service d’authentification / autorisation. Certains en-têtes exemple sont les suivantes :

* NOM DU PRINCIPAL CLIENT X MS
* ID DU PRINCIPAL CLIENT X MS
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON

Code écrit dans une langue ou un cadre peut obtenir les informations dont il a besoin de ces en-têtes. Pour les applications ASP.NET 4.6, la **ClaimsPrincipal** est définie automatiquement avec les valeurs appropriées.

Votre application peut également obtenir des détails utilisateur supplémentaires via un HTTP GET sur la `/.auth/me` point de terminaison de votre application. Un jeton valide inclus dans la demande renverra une charge utile JSON avec plus d’informations sur le fournisseur qui est utilisée, le jeton de fournisseur sous-jacent et d’autres informations utilisateur. Le serveur d’applications Mobile SDK fournissent des méthodes d’assistance pour travailler avec ces données. Pour plus d’informations, voir [comment utiliser le Kit de développement logiciel Azure Mobile applications Node.js](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity)et [travailler sur le serveur principal de .NET SDK pour les applications Mobile Azure](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="documentation-and-additional-resources"></a>Documentation et ressources supplémentaires

### <a name="identity-providers"></a>Fournisseurs d’identité
Les didacticiels suivants montrent comment configurer le Service d’application pour utiliser différents fournisseurs d’authentification :

- [Comment configurer votre application pour utiliser la connexion Azure Active Directory][AAD]
- [Comment configurer votre application pour utiliser la connexion Facebook][Facebook]
- [Comment configurer votre application pour utiliser la connexion de Google][Google]
- [Comment configurer votre application pour utiliser la connexion Account Microsoft][MSA]
- [Comment configurer votre application pour utiliser la connexion Twitter][Twitter]

Si vous souhaitez utiliser un système d’identité autres que ceux fournis ici, vous pouvez également utiliser l' [Afficher un aperçu de la prise en charge de l’authentification personnalisée dans le serveur Mobile applications .NET SDK][custom-auth], qui peut être utilisée dans les applications web, des applications mobiles ou des applications de l’API.

### <a name="web-applications"></a>Applications Web
Les didacticiels suivants montrent comment ajouter une authentification à une application web :

- [Prise en main Azure Application Service - partie 2][web-getstarted]

### <a name="mobile-applications"></a>Applications mobiles
Les didacticiels suivants montrent comment ajouter une authentification à vos clients mobiles à l’aide du flux dirigées par le serveur :

- [Ajouter une authentification dans votre application iOS][iOS]
- [Ajouter une authentification dans votre application Android] [Android]
- [Ajouter une authentification dans votre application Windows] [Windows]
- [Authentification ajouter à votre application Xamarin.iOS] [Xamarin.iOS]
- [Authentification ajouter à votre application Xamarin.Android] [Xamarin.Android]
- [Authentification ajouter à votre application Xamarin.Forms] [Xamarin.Forms]
- [Ajouter une authentification dans votre application Cordova] [Cordova]

Utilisez les ressources suivantes si vous souhaitez utiliser le flux client dirigé pour Azure Active Directory :

- [Utiliser la bibliothèque d’authentification Active Directory pour iOS][ADAL-iOS]
- [Utiliser la bibliothèque d’authentification Active Directory pour Android][ADAL-Android]
- [Utiliser la bibliothèque d’authentification Active Directory pour Windows et Xamarin][ADAL-dotnet]

Utilisez les ressources suivantes si vous souhaitez utiliser le flux client dirigé pour Facebook :

- [Utiliser le SDK Facebook pour iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Utilisez les ressources suivantes si vous souhaitez utiliser le flux client dirigé pour Twitter :

- [Utiliser TISSU Twitter pour iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Utilisez les ressources suivantes si vous souhaitez utiliser le flux client dirigé pour Google :

- [Utiliser le Google connexion SDK pour iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

### <a name="api-applications"></a>Applications API
Les didacticiels suivants montrent comment protéger vos applications API :

- [Authentification des utilisateurs pour les applications de l’API dans le Service d’application Azure][apia-user]
- [Authentification principale du service pour les applications de l’API dans le Service d’application Azure][apia-service]









[apia-user]: ../app-service-api/app-service-api-dotnet-user-principal-auth.md
[apia-service]: ../app-service-api/app-service-api-dotnet-service-principal-auth.md

[web-getstarted]: ../app-service-web/app-service-web-get-started-2.md#authenticate-your-users

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: ../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md
[Facebook]: ../app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication.md
[Google]: ../app-service-mobile/app-service-mobile-how-to-configure-google-authentication.md
[MSA]: ../app-service-mobile/app-service-mobile-how-to-configure-microsoft-authentication.md
[Twitter]: ../app-service-mobile/app-service-mobile-how-to-configure-twitter-authentication.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
