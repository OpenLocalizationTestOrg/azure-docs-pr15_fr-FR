<properties
    pageTitle="Ajouter les Notifications Push pour Android application à l’aide des applications mobiles Azure"
    description="Découvrez comment utiliser les applications Azure Mobile pour envoyer les notifications push dans votre application Android."
    services="app-service\mobile"
    documentationCenter="android"
    manager="erikre"
    editor=""
    authors="ysxu"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-android-app"></a>Ajouter les Notifications Push dans votre application Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Vue d’ensemble
Dans ce didacticiel, vous ajoutez les notifications push au projet [Android démarrage rapide] afin qu’une notification push est envoyée à l’appareil chaque fois qu’un enregistrement est inséré.

Si vous n’utilisez pas le projet de serveur de démarrage rapide téléchargé, vous devez le package d’extension de notification push. Pour plus d’informations, voir [utiliser avec le Kit de développement pour applications Mobile Azure du serveur principal .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Conditions préalables

Vous devez les éléments suivants :

* Un IDE en fonction de la version serveur de votre projet :

    * [Android Studio](https://developer.android.com/sdk/index.html) si cette application est un serveur principal Node.js.

    * [Visual Studio Communauté 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) ou une version ultérieure si cette application utilise un serveur principal .net.

* Android 2.3 ou version ultérieure, la révision Google référentiel 27 ou une version ultérieure et Services Google Play 9.0.2 ou une version ultérieure pour la messagerie Cloud Firebase.

* Terminer le [démarrage rapide Android].

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Créer un projet qui prend en charge la messagerie Cloud Firebase

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Configurer un concentrateur de Notification

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Configurer Azure pour envoyer les notifications push

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="enable-push-notifications-for-the-server-project"></a>Activer les notifications push pour le projet serveur

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="add-push-notifications-to-your-app"></a>Ajouter les notifications push dans votre application

Dans cette section, vous mettez à jour votre application Android client pour gérer les notifications push.

### <a name="verify-android-sdk-version"></a>Vérifier la Version SDK Android

[AZURE.INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

L’étape suivante consiste à installer les services Google Play. Google Cloud messagerie requiert un minimum API au niveau de développement et de test, qui la propriété **minSdkVersion** dans le manifeste doit être conformes aux.

Si vous testez avec un périphérique plus ancien, puis, consultez [Définir la Google Play SDK Services] afin de déterminer comment faible définissez cette valeur et définir en conséquence.

### <a name="add-google-play-services-to-the-project"></a>Ajouter des Services Google Play au projet

[AZURE.INCLUDE [Add Play Services](../../includes/app-service-mobile-add-google-play-services.md)]

### <a name="add-code"></a>Ajouter du code

[AZURE.INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>Tester l’application auprès du service mobile publié

Vous pouvez tester l’application en joignant directement un téléphone Android avec un câble USB, ou à l’aide d’un périphérique virtuel dans l’émulateur.

## <a name="more"></a>Plus

<!-- URLs -->
[Démarrage rapide Android]: app-service-mobile-android-get-started.md

[Configurer les Services Google Play SDK]:https://developers.google.com/android/guides/setup
