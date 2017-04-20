<properties
    pageTitle="Ajouter des Notifications de transmission à l’application avec les applications Azure Mobile iOS"
    description="Découvrez comment utiliser les applications Azure Mobile pour envoyer les notifications push dans votre application iOS."
    services="app-service\mobile"
    documentationCenter="ios"
    manager="yochayk"
    editor=""
    authors="ysxu"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="yuaxu"/>


# <a name="add-push-notifications-to-your-ios-app"></a>Ajouter des Notifications de transmission à votre application iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Vue d’ensemble
Dans ce didacticiel, ajoutez les notifications push au projet [iOS rapides démarrer] afin qu’une notification push est envoyée à l’appareil chaque fois qu’un enregistrement est inséré.

Si vous n’utilisez pas le projet de serveur de démarrage rapide téléchargé, vous devez le package d’extension de notification push. Pour plus d’informations, voir [utiliser avec le Kit de développement pour applications Mobile Azure du serveur principal .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .

[IOS simulator ne prend pas en charge les notifications de transmission](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html). Vous avez besoin d’un appareil iOS physique et une [appartenance Apple Developer Program](https://developer.apple.com/programs/ios/).

##<a name="configure-hub"></a>Configurer concentrateur de Notification

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Application de Registre pour les notifications push

[AZURE.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Configurer Azure pour envoyer les notifications push

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

##<a id="update-server"></a>Mettre à jour le serveur principal pour envoyer les notifications push

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a id="add-push"></a>Ajouter les notifications push à application

[AZURE.INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a id="test"></a>Tester les notifications de transmission

[AZURE.INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

##<a id="more"></a>Plus

* Modèles permettent de vous envoyer des campagnes de disponibilité sur plusieurs plateformes et pousse localisés. [Comment utiliser iOS bibliothèque du Client pour les applications Mobile Azure](app-service-mobile-ios-how-to-use-client-library.md#templates) vous explique comment enregistrer des modèles.

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[démarrage rapide iOS]: app-service-mobile-ios-get-started.md
