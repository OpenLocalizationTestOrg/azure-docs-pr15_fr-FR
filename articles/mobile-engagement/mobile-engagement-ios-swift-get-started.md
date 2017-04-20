<properties
    pageTitle="Prise en main Azure Mobile Engagement pour iOS dans Swift | Microsoft Azure"
    description="Découvrez comment utiliser Azure Mobile Engagement avec Analytique et les Notifications de transmission pour iOS applications."
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="swift"
    ms.topic="hero-article"
    ms.date="09/20/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-ios-apps-in-swift"></a>Prise en main Azure Mobile Engagement pour iOS applications dans Swift

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Cette rubrique vous montre comment utiliser Azure Mobile Engagement pour comprendre l’utilisation de votre application et envoyer les notifications push aux utilisateurs segmentés pour une application iOS.
Dans ce didacticiel, vous créez une application iOS vide qui collecte des données de base et reçoit les notifications push à l’aide du système de Notification Push Apple (APNS).

Ce didacticiel requiert les éléments suivants :

+ XCode 8, que vous pouvez installer à partir de votre MAC App Store
+ l' [Engagement Mobile iOS SDK]
+ Certificat de notification push (.p12) que vous pouvez obtenir sur votre centre de développement Apple

> [AZURE.NOTE] Ce didacticiel utilise Swift version 3.0. 

Fin de ce didacticiel est requis pour tous les autres didacticiels Engagement Mobile pour les applications iOS.

> [AZURE.NOTE] Pour effectuer ce didacticiel, vous devez disposer d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-swift-get-started).

##<a id="setup-azme"></a>Le programme d’installation Mobile Engagement pour votre application iOS

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Connecter votre application sur le serveur principal Engagement Mobile

Ce didacticiel présente une « intégration de base », ce qui correspond à l’ensemble minimal requis pour collecter les données et envoyer une notification push. Vous trouverez la documentation intégration complète dans l' [intégration de SDK Engagement Mobile iOS](mobile-engagement-ios-sdk-overview.md)

Nous allons créer une application simple avec XCode pour illustrer l’intégration :

###<a name="create-a-new-ios-project"></a>Créer un projet iOS

[AZURE.INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

###<a name="connect-your-app-to-mobile-engagement-backend"></a>Connecter votre application pour Mobile Engagement principal

1. Télécharger l' [Engagement Mobile iOS SDK]
2. Extraire le. tar.gz fichier dans un dossier sur votre ordinateur
3. Cliquez avec le bouton droit sur le projet, puis sélectionnez « Ajouter des fichiers à... »

    ![][1]

4. Accédez au dossier où vous avez extrait le Kit de développement logiciel, puis sélectionnez le `EngagementSDK` dossier puis appuyez sur OK.

    ![][2]

5. Ouvrir le `Build Phases` onglet puis, dans le `Link Binary With Libraries` menu Ajouter les structures comme indiqué ci-dessous :

    ![][3]

8. Créer un en-tête de transition pour pouvoir utiliser objectif C API du Kit de développement en cliquant sur fichier > Nouveau > fichier > iOS > Source > fichier d’en-tête.

    ![][4]

9. Modifier le fichier d’en-tête transition pour exposer le code Mobile Engagement objectif-C à votre code rapide, ajoutez les importations ci-après :

        /* Mobile Engagement Agent */
        #import "AEModule.h"
        #import "AEPushMessage.h"
        #import "AEStorage.h"
        #import "EngagementAgent.h"
        #import "EngagementTableViewController.h"
        #import "EngagementViewController.h"
        #import "AEUserNotificationHandler.h"
        #import "AEIdfaProvider.h"

10. Sous paramètres de génération, vérifiez que du paramètre sous Swift compilateur - génération de Code de génération objectif C pont en-tête a un chemin d’accès à cet en-tête. Voici un exemple de chemin d’accès : **$(SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h (selon le chemin d’accès)**

    ![][6]

11. Revenez au portail Azure dans la page *d’Informations sur la connexion* de votre application et copiez la chaîne de connexion

    ![][5]

12. Maintenant coller la chaîne de connexion dans la `didFinishLaunchingWithOptions` déléguer

        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool
        {
            [...]
                EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
            [...]
        }

##<a id="monitor"></a>Activation de l’analyse en temps réel

Avant de commencer l’envoi de données et de s’assurer que les utilisateurs sont actifs, vous devez envoyer au moins un écran (activité) sur le serveur principal Mobile Engagement.

1. Ouvrez le fichier **ViewController.swift** et remplacez la classe de base de **ViewController** être **EngagementViewController**:

    `class ViewController : EngagementViewController {`

##<a id="monitor"></a>Application interrogez la surveillance en temps réel

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>L’activation des Notifications de transmission et dans l’application de messagerie

Engagement Mobile vous permet d’interagir et d’atteindre avec d’autres utilisateurs avec les Notifications de transmission et dans l’application de messagerie dans le contexte des campagnes marketing. Ce module est appelé accessibles dans le portail d’Engagement Mobile.
Les sections suivantes va configurer votre application pour les recevoir.

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Activer votre application de recevoir des Notifications de transmission en mode silencieux

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### <a name="add-the-reach-library-to-your-project"></a>Ajouter la bibliothèque accessibles à votre projet

1. Cliquez avec le bouton droit sur votre projet
2. Sélectionnez`Add file to ...`
3. Accédez au dossier où vous avez extrait le Kit de développement
4. Sélectionnez le `EngagementReach` dossier
5. Cliquez sur Ajouter
6. Modifier le fichier d’en-tête transition pour exposer des en-têtes Mobile Engagement objectif C atteindre et ajoutez les importations suivantes :

        /* Mobile Engagement Reach */
        #import "AEAnnouncementViewController.h"
        #import "AEAutorotateView.h"
        #import "AEContentViewController.h"
        #import "AEDefaultAnnouncementViewController.h"
        #import "AEDefaultNotifier.h"
        #import "AEDefaultPollViewController.h"
        #import "AEInteractiveContent.h"
        #import "AENotificationView.h"
        #import "AENotifier.h"
        #import "AEPollViewController.h"
        #import "AEReachAbstractAnnouncement.h"
        #import "AEReachAnnouncement.h"
        #import "AEReachContent.h"
        #import "AEReachDataPush.h"
        #import "AEReachDataPushDelegate.h"
        #import "AEReachModule.h"
        #import "AEReachNotifAnnouncement.h"
        #import "AEReachPoll.h"
        #import "AEReachPollQuestion.h"
        #import "AEViewControllerUtil.h"
        #import "AEWebAnnouncementJsBridge.h"

### <a name="modify-your-application-delegate"></a>Modifier votre délégué d’Application

1. À l’intérieur de la `didFinishLaunchingWithOptions` - créer un module accessibles et passer à la ligne de l’initialisation Engagement existante :

        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool 
        {
            let reach = AEReachModule.module(withNotificationIcon: UIImage(named:"icon.png")) as! AEReachModule
            EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}", modulesArray:[reach])
            [...]
            return true
        }

###<a name="enable-your-app-to-receive-apns-push-notifications"></a>Activer votre application de recevoir des Notifications de transmission APNS
1. Ajoutez la ligne suivante à la `didFinishLaunchingWithOptions` méthode :

        if #available(iOS 8.0, *)
        {
            if #available(iOS 10.0, *)
            {
                UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .badge, .sound]) { (granted, error) in }
            }else
            {
                let settings = UIUserNotificationSettings(types: [.alert, .badge, .sound], categories: nil)
                application.registerUserNotificationSettings(settings)
            }
            application.registerForRemoteNotifications()
        }
        else
        {
            application.registerForRemoteNotifications(matching: [.alert, .badge, .sound])
        }

2. Ajouter la `didRegisterForRemoteNotificationsWithDeviceToken` méthode comme suit :

        func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
            EngagementAgent.shared().registerDeviceToken(deviceToken)
        }

3. Ajouter la `didReceiveRemoteNotification:fetchCompletionHandler:` méthode comme suit :

        func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any], fetchCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -> Void) {
            EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo, fetchCompletionHandler:completionHandler)
        }

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[Engagement mobile iOS SDK]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-swift-get-started/add-header-file.png
[5]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[6]: ./media/mobile-engagement-ios-swift-get-started/add-bridging-header.png
