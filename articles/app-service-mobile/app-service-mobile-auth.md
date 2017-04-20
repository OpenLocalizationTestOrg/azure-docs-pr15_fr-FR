<properties
    pageTitle="Authentification et autorisation dans les applications mobiles Azure | Microsoft Azure"
    description="Vue d’ensemble de l’authentification et de référence conceptuelle / fonctionnalité d’autorisation pour les applications Mobile Azure"
    services="app-service\mobile"
    documentationCenter=""
    authors="mattchenderson"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="mahender"/>

# <a name="authentication-and-authorization-in-azure-mobile-apps"></a>Authentification et autorisation dans les applications mobiles Azure

## <a name="what-is-app-service-authentication--authorization"></a>Quelle est l’application Service authentification / autorisation ?

> [AZURE.NOTE] Cette rubrique vont être migrée à consolidés [application Service authentification / autorisation](../app-service/app-service-authentication-overview.md) rubrique, qui aborde les applications API Web et Mobile.

Application Service authentification / autorisation est une fonctionnalité qui permet à votre application pour se connecter aux utilisateurs avec aucune modification de code requise sur le serveur principal de l’application. Il propose un moyen facile de protéger votre application et utiliser des données par utilisateur.

Service d’application utilise l’identité fédérée, dans lequel un **fournisseur d’identité** de la 3e partie (« IDP ») stocke les comptes et authentifie les utilisateurs, et l’application utilise cette identité au lieu de sa propre. Application Service prend en charge cinq fournisseurs d’identité prêts à l’emploi : _Azure Active Directory_, _Facebook_, _Google_, _Compte Microsoft_et _Twitter_. Vous pouvez également développer cette prise en charge pour vos applications grâce à l’intégration d’un autre fournisseur d’identité ou de votre propre solution identité personnalisée.

Votre application peut exploiter un nombre quelconque de ces fournisseurs d’identité, vous pouvez permettre aux utilisateurs finaux avec les options de la façon dont ils se connecter.

Si vous souhaitez commencer immédiatement, consultez un des didacticiels suivants :

- [Ajouter une authentification dans votre application iOS]
- [Ajouter une authentification dans votre application Xamarin.iOS]
- [Ajouter une authentification dans votre application Xamarin.Android]
- [Ajouter une authentification dans votre application Windows]

## <a name="how-authentication-works"></a>Fonctionnement de l’authentification

Afin de s’authentifier à l’aide d’un des fournisseurs d’identité, vous devez tout d’abord configurer le fournisseur d’identité à connaître votre application. Le fournisseur d’identité vous fournira des codes secrets que vous fournissez à l’application. Cela termine la relation d’approbation et permet de Service d’application valider les identités qui lui sont fournies.

Ces étapes sont décrites en détail dans les rubriques suivantes :

- [Comment configurer votre application pour utiliser la connexion Azure Active Directory]
- [Comment configurer votre application pour utiliser la connexion Facebook]
- [Comment configurer votre application pour utiliser la connexion de Google]
- [Comment configurer votre application pour utiliser la connexion Account Microsoft]
- [Comment configurer votre application pour utiliser la connexion Twitter]

Une fois que tout est configuré sur le serveur principal, vous pouvez modifier votre client pour se connecter. Il existe deux approches ici :

- À l’aide d’une seule ligne de code, laissez les applications Mobile client SDK se connecter aux utilisateurs.
- Exploiter un SDK publié par un fournisseur d’identité donnée pour définir l’identité et d’accéder au Service d’application.

>[AZURE.TIP] La plupart des applications doivent utiliser un fournisseur SDK pour obtenir une expérience de connexion plus natif-sentiment et exploiter prise en charge de l’actualisation et les autres avantages spécifiques du fournisseur.

### <a name="how-authentication-without-a-provider-sdk-works"></a>Fonctionnement de l’authentification sans un fournisseur SDK

Si vous ne souhaitez pas configurer un fournisseur de kit de développement logiciel, vous pouvez autoriser les applications mobiles effectuer la connexion à votre place. Le client Mobile applications SDK s’ouvre un affichage web pour le fournisseur de votre choix et la connexion. Il peut arriver que blogs et forums vous verrez ceci auquel comme « serveur flux » ou « flux dirigé server » depuis le serveur gère la connexion et le Kit de développement logiciel client reçoit jamais jeton du fournisseur.

Le code requis pour démarrer ce flux est traité dans le didacticiel d’authentification pour chaque plate-forme. À la fin du flux, le Kit de développement logiciel client possède un jeton de Service d’application, puis le jeton est automatiquement joint à toutes les requêtes vers le serveur principal.

### <a name="how-authentication-with-a-provider-sdk-works"></a>Fonctionnement de l’authentification auprès d’un fournisseur SDK

Travailler avec un fournisseur SDK permet de l’expérience de journal interagir plus étroitement avec la plateforme du système d’exploitation, que l’application est en cours d’exécution. Cela vous donne également un jeton de fournisseur et certaines informations utilisateur sur le client, ce qui facilite la consommer graph API et personnaliser l’expérience utilisateur. Il peut arriver que sur les blogs et les forums vous verrez ceci appelé « flux de clients » ou « client dirigé flux « depuis le code sur le client gère la connexion et le code client a accès à un jeton de fournisseur.

Une fois un jeton de fournisseur obtenu, il doit être envoyée au Service d’application de validation. À la fin du flux, le Kit de développement logiciel client possède un jeton de Service d’application, puis le jeton est automatiquement joint à toutes les requêtes vers le serveur principal. Le développeur peut également conserver une référence au jeton du fournisseur s’ils le souhaitent.

## <a name="how-authorization-works"></a>Fonctionnement des autorisations

Application Service authentification / autorisation propose plusieurs choix pour **Action à effectuer lors de la requête n’est pas authentifié**. Avant que votre code reçoit une demande donnée, vous pouvez avoir à cocher Service d’application pour voir si la demande est authentifiée et si pas, rejeter et tentera pour les utilisateurs de se connecter avant de réessayer.

Une option consiste à avoir non authentifié demandes rediriger vers un des fournisseurs d’identité. Dans un navigateur web, ce serait réellement effectuer l’utilisateur vers une nouvelle page. Toutefois, votre client mobile ne peut pas être redirigé de cette façon, et non authentifiés réponses recevront une réponse HTTP _401 non autorisé_ . Étant donné cette, la première requête que votre client effectue doit toujours être au point de terminaison de connexion, et puis vous pouvez passer des appels à n’importe quel autre API. Si vous essayez d’appeler une autre API avant de se connecter, votre client reçoit une erreur.

Si vous souhaitez que plus granulaires contrôle sur les points de terminaison exigent une authentification, vous pouvez également choisir « aucune action (autoriser les requêtes) » pour les demandes non authentifiés. Dans ce cas, toutes les décisions d’authentification sont reportées au code de votre application. Cela vous permet également autoriser l’accès à des utilisateurs spécifiques en fonction des règles d’autorisation personnalisé.

## <a name="documentation"></a>Documentation

Les didacticiels suivants montrent comment ajouter l’authentification à vos clients mobiles à l’aide du Service d’application :

- [Ajouter une authentification dans votre application iOS]
- [Ajouter une authentification dans votre application Xamarin.iOS]
- [Ajouter une authentification dans votre application Xamarin.Android]
- [Ajouter une authentification dans votre application Windows]

Les didacticiels suivants montrent comment configurer le Service d’application pour tirer parti de différents fournisseurs d’authentification :

- [Comment configurer votre application pour utiliser la connexion Azure Active Directory]
- [Comment configurer votre application pour utiliser la connexion Facebook]
- [Comment configurer votre application pour utiliser la connexion de Google]
- [Comment configurer votre application pour utiliser la connexion Account Microsoft]
- [Comment configurer votre application pour utiliser la connexion Twitter]

Si vous souhaitez utiliser un système d’identité autres que ceux fournis ici, vous pouvez également tirer parti [Afficher un aperçu de la prise en charge de l’authentification personnalisée dans le Kit de développement logiciel .NET server](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth).

[Ajouter une authentification dans votre application iOS]: app-service-mobile-ios-get-started-users.md
[Ajouter une authentification dans votre application Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started-users.md
[Ajouter une authentification dans votre application Xamarin.Android]: app-service-mobile-xamarin-android-get-started-users.md
[Ajouter une authentification dans votre application Windows]: app-service-mobile-windows-store-dotnet-get-started-users.md

[Comment configurer votre application pour utiliser la connexion Azure Active Directory]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Comment configurer votre application pour utiliser la connexion Facebook]: app-service-mobile-how-to-configure-facebook-authentication.md
[Comment configurer votre application pour utiliser la connexion de Google]: app-service-mobile-how-to-configure-google-authentication.md
[Comment configurer votre application pour utiliser la connexion Account Microsoft]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Comment configurer votre application pour utiliser la connexion Twitter]: app-service-mobile-how-to-configure-twitter-authentication.md
