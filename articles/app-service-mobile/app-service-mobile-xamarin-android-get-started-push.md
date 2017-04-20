<properties
    pageTitle="Ajouter les notifications push à votre application Xamarin.Android | Service application Azure"
    description="Découvrez comment utiliser Azure Application Service et Azure Notification Hubs pour envoyer les notifications push à votre application Xamarin.Android"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>Ajouter les notifications push à votre application Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##<a name="overview"></a>Vue d’ensemble


Dans ce didacticiel, ajoutez les notifications push au projet [Xamarin.Android rapide démarrer](app-service-mobile-windows-store-dotnet-get-started.md) afin qu’une notification push est envoyée à l’appareil chaque fois qu’un enregistrement est inséré.

Si vous n’utilisez pas le projet de serveur de démarrage rapide téléchargé, vous devez le package d’extension de notification push. Pour plus d’informations, voir [utiliser avec le Kit de développement pour applications Mobile Azure du serveur principal .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .


##<a name="prerequisites"></a>Conditions préalables

Ce didacticiel requiert les éléments suivants :

+ Un compte Google actif. Vous pouvez vous inscrire pour un compte Google sur [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).
+ [Composant de Client de messagerie Google Cloud](http://components.xamarin.com/view/GCMClient/).

##<a name="configure-hub"></a>Configurer un concentrateur de Notification

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

##<a id="register"></a>Activer Firebase Cloud de messagerie

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

##<a name="configure-azure-to-send-push-requests"></a>Configurer Azure pour envoyer des demandes de transmission

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

##<a id="update-server"></a>Mettre à jour le projet serveur pour envoyer les notifications push

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##<a id="configure-app"></a>Configurer le projet client pour les notifications push

[AZURE.INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

##<a id="add-push"></a>Ajouter du code de notifications push dans votre application

[AZURE.INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>Notifications de transmission de test dans votre application

Vous pouvez tester l’application à l’aide d’un périphérique virtuel dans l’émulateur. Il existe des étapes de configuration supplémentaires requises lors de l’exécution sur un émulateur.

1. Vérifiez que vous déployez à ou débogage sur un périphérique virtuel lequel APIs de Google est définie comme cible, comme illustré ci-dessous dans le Gestionnaire de périphériques virtuel Android (AVD).

    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)

2. Ajouter un compte Google à l’appareil Android en cliquant sur **applications** > **paramètres** > **Ajouter un compte**, puis suivez les invites.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)

3. Exécutez l’application de la liste des tâches comme avant et insérer un nouvel élément todo. Cette fois-ci, une icône de notification s’affiche dans la zone de notification. Vous pouvez ouvrir le bac d’alimentation notification pour afficher le texte complet de la notification.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)


<!-- URLs. -->
[Xamarin.Android quick start]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: http://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
