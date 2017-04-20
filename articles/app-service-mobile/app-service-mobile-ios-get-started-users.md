<properties
    pageTitle="Ajouter une authentification sur iOS avec les applications Mobile Azure"
    description="Découvrez comment utiliser les applications Mobile Azure pour authentifier les utilisateurs de votre application iOS via un grand nombre de fournisseurs d’identité, y compris AAD, Google, Facebook, Twitter et Microsoft."
    services="app-service\mobile"
    documentationCenter="ios"
    authors="ysxu"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="add-authentication-to-your-ios-app"></a>Ajouter une authentification dans votre application iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Dans ce didacticiel, vous ajoutez l’authentification au projet [iOS rapides démarrer] à l’aide d’un fournisseur d’identité pris en charge. Ce didacticiel est basé sur le didacticiel [Démarrer iOS rapides] , vous devez effectuer tout d’abord.

##<a name="register"></a>Enregistrer votre application pour l’authentification et configurer le Service d’application

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restreindre les autorisations pour les utilisateurs authentifiés

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Dans Xcode, appuyez sur **exécuter** pour lancer l’application. Une exception est élevée, car l’application tente d’accéder à la version serveur en tant qu’utilisateur non authentifié, mais _TodoItem_ table maintenant requiert une authentification.

##<a name="add-authentication"></a>Ajouter l’authentification à l’application

[AZURE.INCLUDE [app-service-mobile-ios-authenticate-app](../../includes/app-service-mobile-ios-authenticate-app.md)]


<!-- URLs. -->

[démarrage rapide iOS]: app-service-mobile-ios-get-started.md

[Azure portal]: https://portal.azure.com
