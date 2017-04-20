<properties
    pageTitle="Créer une application Cordova dans des applications mobiles Azure Application Service | Microsoft Azure"
    description="Suivez ce didacticiel pour l’utilisation de l’application mobile Azure les serveurs principaux pour le développement Cordova Apache"
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""
    tags=""
    keywords="cordova, client mobile, javascript," />

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

#<a name="create-an-apache-cordova-app"></a>Créer un Apache Cordova application

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel montre comment ajouter un service de nuage serveur principal dans une application mobile Cordova Apache à l’aide d’un serveur principal Azure application mobile.  Vous allez créer un nouveau contexte de l’application mobile et une simple _liste Todo_ application Apache Cordova qui stocke les données d’application dans Azure.

Fin de ce didacticiel est requis pour tous les autres didacticiels Apache Cordova sur l’utilisation de la fonctionnalité d’applications Mobile dans le Service d’application Azure.

## <a name="prerequisites"></a>Conditions préalables

Pour effectuer ce didacticiel, vous devez les éléments suivants :

* Un PC avec [Visual Studio Communauté 2015] ou version ultérieure.
* [Outils visual Studio pour Apache Cordova].
* Un [compte Azure active](https://azure.microsoft.com/pricing/free-trial/).

Vous pouvez également ignorer Visual Studio et utiliser la ligne de commande Cordova Apache directement.  Ceci est utile lorsqu’il a terminé le didacticiel sur un ordinateur Mac.  Compilation des applications clientes Cordova Apache à l’aide de la ligne de commande n’est pas traité par ce didacticiel.

## <a name="create-a-new-azure-mobile-app-backend"></a>Créer un nouveau contexte Azure application mobile

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

[Regardez une vidéo montrant une procédure similaire](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-1-Create-an-Azure-Mobile-App)

## <a name="configure-the-server-project"></a>Configurer le projet de serveur

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-apache-cordova-app"></a>Téléchargez et exécutez l’application Cordova Apache

[AZURE.INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous terminé ce didacticiel de démarrage rapide, passer à un des didacticiels suivants :

* [Ajouter une authentification] à votre Apache Cordova application.
* [Ajouter les Notifications Push] à votre Apache Cordova application.

Découvrez les concepts clés associés aux services d’application Azure.

* [Authentification]
* [Notifications de transmission]

Découvrez comment utiliser le SDK.

* [Kit de développement logiciel Apache Cordova]
* [ASP.NET Server (SDK)]
* [Node.js Server (SDK)]

<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Communauté Visual Studio 2015]: http://www.visualstudio.com/
[Outils Visual Studio pour Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Ajouter une authentification]: app-service-mobile-cordova-get-started-users.md
[Ajouter les Notifications Push]: app-service-mobile-cordova-get-started-push.md
[Authentification]: app-service-mobile-auth.md
[Notifications de transmission]: ../notification-hubs/notification-hubs-push-notification-overview.md
[Kit de développement logiciel Apache Cordova]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server (SDK)]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server (SDK)]: app-service-mobile-node-backend-how-to-use-server-sdk.md
