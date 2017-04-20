<properties
    pageTitle="Limitations de point de terminaison version 2.0 et restrictions | Microsoft Azure"
    description="Liste des limitations et restrictions avec le point de terminaison Azure AD version 2.0."
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
    ms.date="09/30/2016"
    ms.author="dastrock"/>

# <a name="should-i-use-the-v20-endpoint"></a>Dois-je utiliser le point de terminaison version 2.0 ?

Lorsque vous créez des applications qui s’intègrent à Azure Active Directory, vous devez décider si les protocoles de point de terminaison et l’authentification version 2.0 correspondent à vos besoins.  Le point de terminaison Azure AD d’origine est toujours entièrement pris en charge et sous certains aspects, est plus riche à la version 2.0.  Toutefois, la version 2.0 point de terminaison [présente des avantages significatifs](active-directory-v2-compare.md) pour les développeurs qui peuvent vous inciter à utiliser le nouveau modèle de programmation.

À ce stade, nous vous recommandons d’utilisation du point de terminaison version 2.0 est la suivante :

- Si vous voulez prendre en charge les comptes Microsoft personnels dans votre application, vous devez utiliser le point de terminaison version 2.0.  Mais n’oubliez pas de comprendre les limitations répertoriées dans cet article, en particulier celles relatives spécifiquement pour travailler et scolaire.
- Si votre application requiert uniquement prise en charge de travail et des comptes scolaires, vous devez utiliser [les points de terminaison Azure AD d’origine](active-directory-developers-guide.md).

Dans le temps, le point de terminaison version 2.0 augmentera à supprimer les restrictions mentionnées ici, afin que vous devez toujours utiliser le point de terminaison version 2.0.  En attendant, cet article est destiné à vous aider à déterminer si le point de terminaison version 2.0 vous concerne.  Nous continue à mettre à jour de cet article au fil du temps pour refléter l’état actuel du point de terminaison version 2.0, aussi consultez précédent pour réévaluez vos besoins par rapport aux fonctionnalités de la version 2.0.

Si vous avez une application existante avec Azure AD qui n’utilise pas de point de terminaison de la version 2.0, il est inutile de démarrer à partir de zéro.  À l’avenir, nous fournir une méthode pour vous permettent d’activer vos applications Azure AD pour une utilisation avec le point de terminaison version 2.0.

## <a name="restrictions-on-apps"></a>Restrictions sur les applications
Les types d’applications suivants ne sont actuellement pas pris en charge par le point de terminaison version 2.0.  Pour obtenir une description des types d’applications pris en charge, consultez [cet article](active-directory-v2-flows.md).

##### <a name="standalone-web-apis"></a>API Web autonome
Avec le point de terminaison version 2.0, vous avez la possibilité pour [Générer une API Web qui est sécurisé à l’aide de OAuth 2.0](active-directory-v2-flows.md#web-apis).  Toutefois, cette API Web uniquement seront en mesure de recevoir des jetons à partir d’une application qui partage le même ID d’Application.  Création d’un site web API est accessible à partir d’un client avec un Id différent de l’Application n’est pas pris en charge.  Ce client ne pourrez pas demander ou obtenir l’autorisation de votre site web API.

Pour apprendre à créer une API Web qui accepte des jetons à partir d’un client avec le même Id de l’application, consultez les exemples d’API Web de point de terminaison version 2.0 dans [Prise en main](active-directory-appmodel-v2-overview.md#getting-started).

##### <a name="web-api-on-behalf-of-flow"></a>API Web sur à la place de flux
Architectures de nombreux inclure une API Web qui doit effectuer des appels d’une autre API Web en aval, les deux sécurisé par le point de terminaison version 2.0.  Ce scénario est courant dans les clients natifs qui disposent d’une principale API Web, qui à son tour appelle un service en ligne Microsoft ou un autre personnalisées web API qui prend en charge Azure AD.

Ce scénario peut prendre en charge à l’aide de l’octroi d’informations d’identification OAuth 2.0 Jwt porteur, également appelé du flux On-Behalf-Of.  Toutefois, le flux de suite à la place de n'est pas pris en charge pour le point de terminaison version 2.0.  Pour découvrir le fonctionnement de ce flux dans sera-t-il disponible Azure Active Directory du service, consultez l' [exemple de la part de code sur GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

## <a name="restrictions-on-app-registrations"></a>Restrictions sur les enregistrements d’application
À ce stade, toutes les applications que vous voulez intégrer dans le point de terminaison version 2.0 doivent créer un nouvel enregistrement d’application en [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).  N’importe quelle annonce Azure existant ou des applications Account Microsoft ne sera pas compatibles avec le point de terminaison version 2.0, ni sera applications enregistrées dans n’importe quel portail outre le portail d’inscription application nouveau.  Nous prévoyons de fournir un moyen pour permettre aux applications existantes à utiliser comme une application version 2.0. Pour le moment, il n’existe aucun chemin d’accès de migration pour une application au point de terminaison version 2.0.

De même, applications enregistrées dans le portail d’inscription application nouveau ne fonctionnent pas sur le point de terminaison de l’authentification Azure AD d’origine.  Vous pouvez, toutefois, utiliser des applications créées dans le portail d’inscription de l’application pour intégrer correctement à l’extrémité de l’authentification de compte Microsoft, `https://login.live.com`.

En outre, les enregistrements application créées en [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) comprennent les avertissements suivants :

- La propriété de la **page d’accueil** , également connu sous le nom de l' **URL authentification** n’est pas pris en charge.  Sans une page d’accueil, ces applications affichera pas dans le volet Mes applications Office.
- Seules deux secrets d’application sont autorisées par Id d’application pour le moment.
- Un enregistrement d’application peut uniquement être affichée et gérée par un compte de développeur unique.  Il ne peut pas être partagé entre plusieurs développeurs.
- Il existe plusieurs restrictions sur le format de redirect_uri autorisée.  Consultez la section suivante pour plus d’informations.

## <a name="restrictions-on-redirect-uris"></a>Restrictions sur URI de redirection
Les applications qui sont enregistrées dans le portail d’inscription Application nouveau sont actuellement limitées à un jeu de valeurs redirect_uri limité.  Le redirect_uri pour les services et applications web doit commencer par le schéma `https`, et toutes les valeurs redirect_uri doivent partager un seul domaine DNS.  Par exemple, il n’est pas possible d’enregistrer une application web qui a redirect_uris :

`https://login-east.contoso.com`  
`https://login-west.contoso.com`

Le système d’inscription compare le nom DNS entière de la redirect_uri existant avec le nom de la redirect_uri que vous ajoutez DNS. La demande pour ajouter le nom DNS échouera si une des conditions suivantes sont remplie :  

- Si le nom de la nouvelle redirect_uri DNS entier ne correspond pas le nom DNS de la redirect_uri existant
- Si le nom de la nouvelle redirect_uri DNS entier n’est pas un sous-domaine de la redirect_uri existant

Par exemple, si l’application a actuellement redirect_uri :

`https://login.contoso.com`

Il est possible d’ajouter :

`https://login.contoso.com/new`

qui correspond exactement au nom DNS, ou :

`https://new.login.contoso.com`

qui est un sous-domaine DNS de login.contoso.com.  Si vous voulez avoir une application qui a login east.contoso.com et connexion-west.contoso.com en tant que redirect_uris, puis vous devez ajouter le redirect_uris suivant dans l’ordre :

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Les deux derniers peuvent être ajoutés, car ils constituent sous-domaines de la première redirect_uri, contoso.com. Cette limitation sera supprimée dans une version à venir.

Pour savoir comment enregistrer une application dans le portail d’inscription Application nouveau, reportez-vous à [cet article](active-directory-v2-app-registration.md).

## <a name="restrictions-on-services--apis"></a>Restrictions sur les services et API
Le point de terminaison version 2.0 actuellement connexion prend en charge pour n’importe quelle application enregistrée dans le portail d’inscription Application nouvelle, si elle est comprise dans la liste des [flux d’authentification pris en charge](active-directory-v2-flows.md).  Toutefois, ces applications seulement seront en mesure d’acquérir des jetons d’accès OAuth 2.0 pour un ensemble très limité de ressources.  Le point de terminaison version 2.0 émet uniquement access_tokens pour :

- L’application qui a demandé le jeton.  Une application peut acquérir un access_token pour proprement dit, si l’application logique se compose de plusieurs composants différents ou niveaux.  Pour savoir ce scénario en action, consultez nos didacticiels [Mise en route](active-directory-appmodel-v2-overview.md#getting-started) .
- Le courrier Outlook, calendrier et Contacts reste API, ce qui se trouvent dans https://outlook.office.com.  Pour savoir comment écrire une application qui accède à ces API, reportez-vous à ces didacticiels [Office mise en route](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) .
- Les API Microsoft Graph.  Pour en savoir plus sur le graphique Microsoft et toutes les données qui ne sont disponibles, visitez le site [https://graph.microsoft.io](https://graph.microsoft.io).

Aucun d’autres services ne sont prises en charge pour le moment.  Plus de Microsoft Online services sont ajoutés à l’avenir, ainsi que la prise en charge pour votre propre personnalisé API Web par défaut et les services.

## <a name="restrictions-on-libraries--sdks"></a>Restrictions sur les bibliothèques et SDK
Prise en charge de la bibliothèque pour le point de terminaison version 2.0 est limitée pour l’instant.  Si vous souhaitez utiliser le point de terminaison version 2.0 dans une application de production, vous disposez des options suivantes :

- Si vous créez une application web, vous pouvez utiliser en toute sécurité nos logiciels intermédiaires côté serveur sera-t-il disponible à effectuer de connexion et de validation des jetons.  Ces bibliothèques incluent logiciels intermédiaires se connecter ID OWIN ouvert pour ASP.NET et notre plug-in NodeJS Passport.  Exemples de code à l’aide de nos logiciels intermédiaires sont disponibles dans notre section [Mise en route](active-directory-appmodel-v2-overview.md#getting-started) .
- Pour d’autres plateformes et les applications natives et mobiles, vous pouvez également intégrer le point de terminaison version 2.0 par directement envoi et réception de messages de protocole de code de l’application.  La version 2.0 OpenID se connecter et OAuth protocoles [ont été explicitement présentées](active-directory-v2-protocols.md) pour vous aider à effectuer une telle intégration.
- Enfin, vous pouvez utiliser bibliothèques ouvrir ID se connecter et OAuth ouvrir la source pour intégrer le point de terminaison version 2.0.  Le protocole version 2.0 doit être compatible avec de nombreuses bibliothèques de protocole libres sans principales modifications apportées.  La disponibilité de ces bibliothèques varie en fonction de langue et de la plateforme et les sites Web [Ouverts ID de connexion](http://openid.net/connect/) et [2.0 OAuth](http://oauth.net/2/) tenir à jour une liste des mises en œuvre populaires. Voir [bibliothèques version 2.0 et l’authentification Azure Active Directory (AD)](active-directory-v2-libraries.md) pour obtenir plus d’informations et la liste des bibliothèques d’ouvrir la source du client et des exemples qui ont été testés avec le point de terminaison version 2.0.

Nous avons également publié un aperçu de la [Bibliothèque d’authentification Microsoft (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) initial uniquement pour .NET.  N’hésitez pas à essayer cette bibliothèque dans les applications clientes et serveur .NET, mais comme une bibliothèque preview qu'il ne sera pas accompagné disponibilité générale qualité prend en charge.

## <a name="restrictions-on-protocols"></a>Restrictions sur les protocoles
Point de terminaison de la version 2.0 prend en charge uniquement connecter ID ouvrir & OAuth 2.0.  Toutefois, pas toutes les fonctionnalités et les capacités de chaque protocole ont été incorporées dans le point de terminaison version 2.0, y compris :

- La connexion OpenID `end_session_endpoint`, qui permet à une application mettre fin à la session utilisateur avec le point de terminaison version 2.0.
- id_tokens émis par le point de terminaison version 2.0 uniquement contenir un identificateur par paire pour l’utilisateur.  Cela signifie que deux applications différentes recevront un ID différent pour le même utilisateur.  Notez que par interroger le graphique Microsoft `/me` point de terminaison, vous pourrez obtenir une identification correlatable pour l’utilisateur qui peut être utilisé dans les applications.
- id_tokens émis par le point de terminaison version 2.0 ne contiennent pas une `email` revendiquer pour l’utilisateur pour le moment, même si vous faites l’acquisition d’autorisation de l’utilisateur pour afficher leurs messages.
- Point de terminaison OpenID d’informations utilisateur se connecter. Le point de terminaison informations utilisateur n’est pas activée sur le point de terminaison version 2.0 pour le moment.  Toutefois, toutes les données de profil utilisateur vous recevrez potentiellement à ce point de terminaison est disponible à partir du graphique Microsoft `/me` point de terminaison.
- Rôle et revendications de groupe.  Pour le moment, le point de terminaison version 2.0 ne reconnaît pas émission revendications rôle ou un groupe dans id_tokens.

Pour mieux comprendre l’étendue de la fonctionnalité du protocole pris en charge par le point de terminaison version 2.0, lisez notre [OpenID se connecter et référence du protocole OAuth 2.0](active-directory-v2-protocols.md).

## <a name="restrictions-for-work--school-accounts"></a>Restrictions pour les comptes professionnel & scolaires
Il existe quelques fonctionnalités spécifiques aux utilisateurs d’entreprise/Microsoft qui ne sont pas encore pris en charge par le point de terminaison version 2.0.

##### <a name="device-based-conditional-access-native-and-mobile-apps-and-the-microsoft-graph"></a>Accès conditionnelle basée sur un appareil, applications natives et mobiles, puis le graphique Microsoft
Point de terminaison de la version 2.0 ne reconnaît pas encore une authentification des périphériques pour les applications mobiles et natives, tels que des applications natives s’exécutant sur iOS ou Android.  Cela peut empêcher votre application native d’appeler le Microsoft Graph pour certaines organisations.  Authentification de l’appareil est nécessaire lorsqu’un administrateur définit une stratégie d’accès conditionnelle basée sur un appareil sur une application.  Du point de terminaison version 2.0, le scénario probablement d’accès conditionnelle basée sur un appareil est un administrateur définition d’une stratégie sur une ressource dans Microsoft Graph, telles que l’API Outlook.  Si un administrateur définit cette stratégie et que votre application native demande un jeton à Microsoft Graph, la demande finalement échouera, car l’authentification appareil n’est pas encore pris en charge.  Applications Web qui vous demandent des jetons à Microsoft Graph, cependant, sont prises en charge lorsque les règles basées sur l’appareil sont configurés.  Dans le site web application scénario appareil authentification est effectuée via un navigateur web de l’utilisateur.

En tant que développeur, il est probablement avez pas le contrôle lorsque vous définissez des stratégies sur les ressources de Microsoft Graph, ou encore prenant en charge lorsque ce problème survient.  Si vous créez une application pour les utilisateurs scolaires et Professionnel, vous devez utiliser [le point de terminaison Azure AD d’origine](active-directory-developers-guide.md) jusqu'à ce que le point de terminaison version 2.0 prend en charge l’authentification appareil.  Pour plus d’informations sur accès conditionnelle basée sur un appareil, consultez [cet article](active-directory-conditional-access.md#device-based-conditional-access).

##### <a name="windows-integrated-authentication-for-federated-tenants"></a>Authentification intégrée Windows pour les clients fédérés
Si vous avez utilisé le terme ADAL (et par conséquent le point de terminaison Azure AD d’origine) dans les applications Windows, vous avez peut-être commencé parti de ce qui est appelé l’octroi assertion SAML.  Cette licence permet aux utilisateurs de publicité Azure fédérée clients silencieusement s’authentifier avec leur instance Active Directory sur site sans avoir à entrer ses informations d’identification.  L’octroi d’assertion SAML n’est actuellement pas pris en charge sur le point de terminaison version 2.0.
