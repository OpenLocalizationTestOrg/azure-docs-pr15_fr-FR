<properties
    pageTitle="Rubriques d’aide sur l’inscription de l’application portail | Microsoft Azure"
    description="Une description de diverses fonctionnalités dans le portail d’inscription de l’application Microsoft."
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
    ms.date="09/16/2016"
    ms.author="dastrock"/>

# <a name="app-registration-reference"></a>Référence de l’inscription de l’application
Ce document fournit contexte et les descriptions des différentes fonctionnalités figurant dans le portail de l’enregistrement de l’application Microsoft [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).

## <a name="my-applications"></a>Mes Applications
Cette liste contient toutes vos applications inscrites pour utiliser le point de terminaison Azure AD version 2.0.  Ces applications ont la possibilité de se connecter aux utilisateurs avec les deux comptes personnels à partir de compte Microsoft et comptes Azure Active Directory ou scolaires.  Pour en savoir plus sur le point de terminaison de version 2.0 Azure AD, consultez notre [vue d’ensemble de la version 2.0](active-directory-appmodel-v2-overview.md).  Ces applications peuvent également servir à intégrer à l’extrémité de l’authentification de compte Microsoft, `https://login.live.com`.

## <a name="live-sdk-applications"></a>Applications SDK Live
Cette liste contient toutes vos applications enregistrées pour être utilisé uniquement avec un compte Microsoft.  Ils ne sont pas activés pour une utilisation avec Azure Active Directory quelque.  Il s’agit de l’endroit où vous trouverez toutes les applications qui ont déjà été inscrit auprès du portail de développement MSA en `https://account.live.com/developers/applications`.  Toutes les fonctions que vous avez précédemment effectué en `https://account.live.com/developers/applications` peut désormais être effectuée dans ce nouveau portail, `https://apps.dev.microsoft.com`.  Si vous avez d’autres questions concernant vos applications de compte Microsoft, contactez-nous.

## <a name="application-secrets"></a>Application Secrets
Application secrets sont les informations d’identification qui permettent à votre application effectuer une fiable [l’authentification du client](http://tools.ietf.org/html/rfc6749#section-2.3) avec AD Azure.  Dans OAuth & OpenID se connecter, un secrets application est généralement appelé un `client_secret`.  Dans la version 2.0 de protocole, n’importe quelle application qui reçoit un jeton de sécurité en offrant un emplacement web (à l’aide d’un `https` jeu) devez utiliser un code secret application pour identifier auprès d’Azure AD lors de remboursement de ce jeton de sécurité.  En outre, n’importe quel native client qui reçoit les jetons sur un appareil sera interdit d’utiliser un code secret application pour effectuer l’authentification du client, afin d’empêcher le stockage de secrets dans les environnements non sécurisés.

Chaque application peut contenir deux secrets application valide à un moment donné dans le temps.  En conservant les deux secrets, vous devez l’ablilty pour effectuer périodiquement survol clé au sein de l’ensemble de l’environnement de votre application.  Une fois que vous avez migré l’intégralité de votre application sur un nouveau code secret, vous pouvez supprimer l’ancien secret et mise en service un nouvel identifiant.

Pour le moment, que deux types de secrets d’application sont autorisés dans le portail d’inscription de l’application.  Choix de **Générer un nouveau mot de passe** pour générer et stocker un secret partagé dans le magasin de données correspondantes, vous pouvez utiliser dans votre application.  Choix de **Générer une nouvelle paire de clés** crée une nouvelle paire de clés public/privé qui peut être téléchargée et utilisée pour l’authentification du client vers Azure Active Directory.

## <a name="profile"></a>Profil
La section de profil du portail d’inscription application peut être utilisée pour personnaliser la page de votre application de connexion.  Pour le moment, vous pouvez modifier dans le logo d’application de la page, conditions d’URL du service et la déclaration de confidentialité de la connexion.  Le logo doit être un transparent 48 x 48 ou 50 x 50 pixels une image dans un fichier image GIF, PNG ou JPEG est 15 Ko ou plus petit.  Essayez de modifier les valeurs et en affichant le signe qui en résulte dans page !

## <a name="live-sdk-support"></a>Prise en charge SDK Live
Lorsque vous activez « Live SDK Support », aucun secret d’application que vous créez sera déployé dans les deux Azure AD et banques de données Account Microsoft.  Cela permet à votre application intégrer directement avec le service Microsoft Account (login.live.com).  Si vous souhaitez créer une application à l’aide de Microsoft Account directement (au lieu d’utiliser le point de terminaison de version 2.0 Azure AD), vérifiez que la prise en charge du Kit de développement logiciel de Live est activée.

Désactivation de la prise en charge Live SDK garantit que le code secret application est uniquement écrites dans les données Azure AD stocker.  Les données Azure AD store incorpore réglementations échelle de l’entreprise qui lui permettent de répondre à certaines normes, telles que la conformité la loi FISMA.  Si vous activez le support Live SDK, votre application ne peut pas mettre en conformité avec certains de ces standards.

Si vous envisagez de seulement jamais utiliser le point de terminaison de version 2.0 Azure AD, vous pouvez désactiver en toute sécurité prise en charge Live SDK.

