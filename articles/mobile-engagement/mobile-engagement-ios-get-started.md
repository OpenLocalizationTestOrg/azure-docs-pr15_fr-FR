<properties
    pageTitle="Prise en main Azure Mobile Engagement pour iOS dans objectif C | Microsoft Azure"
    description="Découvrez comment utiliser Azure Mobile Engagement avec notifications push et d’analytique pour les applications iOS."
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="hero-article"
    ms.date="10/05/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-ios-apps-in-objective-c"></a>Prise en main Azure Mobile Engagement les applications iOS d’objectif C

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Cette rubrique vous montre comment utiliser Azure Mobile Engagement pour comprendre l’utilisation de votre application et envoyer les notifications push aux utilisateurs segmentés pour une application iOS.
Dans ce didacticiel, vous créez une application iOS vide qui collecte des données de base et reçoit les notifications push à l’aide du système de Notification Push Apple (APNS).

Ce didacticiel requiert les éléments suivants :

+ XCode 8, que vous pouvez installer à partir de votre MAC App Store
+ l' [Engagement Mobile iOS SDK]

Fin de ce didacticiel est requis pour tous les autres didacticiels Engagement Mobile pour les applications iOS.

> [AZURE.NOTE] Pour effectuer ce didacticiel, vous devez disposer d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-get-started).

##<a id="setup-azme"></a>Le programme d’installation Mobile Engagement pour votre application iOS

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Connecter votre application sur le serveur principal Engagement Mobile

Ce didacticiel présente une « intégration de base », ce qui correspond à l’ensemble minimal requis pour collecter les données et envoyer une notification push. Vous trouverez la documentation intégration complète dans l' [intégration de SDK Engagement Mobile iOS](mobile-engagement-ios-sdk-overview.md)

Nous allons créer une application simple avec XCode pour illustrer l’intégration.

###<a name="create-a-new-ios-project"></a>Créer un projet iOS

[AZURE.INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

###<a name="connect-your-app-to-the-mobile-engagement-backend"></a>Connecter votre application sur le serveur principal Engagement Mobile

1. Télécharger l' [Engagement Mobile iOS SDK].
2. Extraire le. tar.gz fichier dans un dossier sur votre ordinateur.
3. Droit sur le projet et sélectionnez **Ajouter des fichiers à**.

    ![][1]

4. Accédez au dossier où vous avez extrait le Kit de développement, sélectionnez le `EngagementSDK` dossier et appuyez sur **OK**.

    ![][2]

5. Ouvrez l’onglet **Phases créer** , puis dans le menu **Binaire avec les bibliothèques de liens** , ajoutez les structures comme indiqué ci-dessous :

    ![][3]

6. Revenez au portail Azure dans la page **d’Informations sur la connexion** de votre application et copiez la chaîne de connexion.

    ![][4]

7. Ajoutez la ligne suivante du code dans votre fichier **AppDelegate.m** .

        #import "EngagementAgent.h"

8. Maintenant coller la chaîne de connexion dans la `didFinishLaunchingWithOptions` délégué.

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
            [...]   
            [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
            [...]
        }

9. `setTestLogEnabled`est une instruction facultative qui permet des journaux SDK pour identifier les problèmes. 

##<a id="monitor"></a>Activer le contrôle en temps réel

Avant de commencer l’envoi de données et de s’assurer que les utilisateurs sont actifs, vous devez envoyer au moins un écran (activité) sur le serveur principal Mobile Engagement.

1. Ouvrez le fichier **ViewController.h** et importer **EngagementViewController.h**:

    `# import "EngagementViewController.h"`

2. Désormais, remplacer la classe super de l’interface **ViewController** par `EngagementViewController`:

    `@interface ViewController : EngagementViewController`

##<a id="monitor"></a>Application interrogez la surveillance en temps réel

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Activer les notifications push et dans l’application de messagerie

Engagement Mobile vous permet d’interagir avec d’autres utilisateurs et atteindre avec les notifications push et dans le contexte des campagnes de messagerie dans l’application. Ce module est appelé accessibles dans le portail d’Engagement Mobile.
Les sections suivantes configurer votre application pour les recevoir.

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Activer votre application de recevoir des Notifications de transmission en mode silencieux

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]  

### <a name="add-the-reach-library-to-your-project"></a>Ajouter la bibliothèque accessibles à votre projet

1. Clic droit sur votre projet.
2. Sélectionnez **Ajouter un fichier à**.
3. Accédez au dossier où vous avez extrait le Kit de développement.
4. Sélectionnez le `EngagementReach` dossier.
5. Cliquez sur **Ajouter**.

### <a name="modify-your-application-delegate"></a>Modifier votre délégué d’Application

1. Dans le fichier **AppDeletegate.m** , importer le module Engagement atteindre.

        #import "AEReachModule.h"
        #import <UserNotifications/UserNotifications.h>

2. À l’intérieur de la `application:didFinishLaunchingWithOptions` méthode, créez un module accessibles et passer à la ligne de l’initialisation Engagement existante :

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
            AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
            [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
            [...]
            return YES;
        }

###<a name="enable-your-app-to-receive-apns-push-notifications"></a>Activer votre application de recevoir des Notifications de transmission APNS

1. Ajoutez la ligne suivante à la `application:didFinishLaunchingWithOptions` méthode :

        if (NSFoundationVersionNumber >= NSFoundationVersionNumber_iOS_8_0)
        {
            if (NSFoundationVersionNumber > NSFoundationVersionNumber_iOS_9_x_Max)
            {
                [UNUserNotificationCenter.currentNotificationCenter requestAuthorizationWithOptions:(UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionAlert) completionHandler:^(BOOL granted, NSError * _Nullable error) {}];
            }else
            {
                [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert)   categories:nil]];
            }
            [application registerForRemoteNotifications];
        }
        else
        {
            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }

2. Ajouter la `application:didRegisterForRemoteNotificationsWithDeviceToken` méthode comme suit :

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
        {
            [[EngagementAgent shared] registerDeviceToken:deviceToken];
            NSLog(@"Registered Token: %@", deviceToken);
        }

3. Ajouter la `didFailToRegisterForRemoteNotificationsWithError` méthode comme suit :

        - (void)application:(UIApplication*)application didFailToRegisterForRemoteNotificationsWithError:(NSError*)error
        {
           
           NSLog(@"Failed to get token, error: %@", error);
        }

4. Ajouter la `didReceiveRemoteNotification:fetchCompletionHandler` méthode comme suit :

        - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
        {
            [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
        }

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[Engagement mobile iOS SDK]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png

