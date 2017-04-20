<properties
    pageTitle="Prise en main Azure Engagement Mobile pour Xamarin.iOS"
    description="Découvrez comment utiliser Azure Mobile Engagement avec Analytique et les Notifications de transmission pour les applications Xamarin.iOS."
    services="mobile-engagement"
    documentationCenter="xamarin"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-xamarinios-apps"></a>Prise en main Azure Engagement Mobile pour les applications Xamarin.iOS

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Cette rubrique vous montre comment utiliser Azure Mobile Engagement pour comprendre l’utilisation de votre application et envoyer les notifications push aux utilisateurs segmentés dans une application Xamarin.iOS.
Dans ce didacticiel, vous créez une application vierge Xamarin.iOS qui collecte des données de base et reçoit les notifications push à l’aide du système de Notification Push Apple (APNS).

Ce didacticiel requiert les éléments suivants :

+ [Xamarin Studio](http://xamarin.com/studio). Vous pouvez également utiliser Visual Studio avec Xamarin mais ce didacticiel utilise Xamarin Studio. Pour obtenir des instructions d’installation, voir [configurer et installer pour Visual Studio et Xamarin](https://msdn.microsoft.com/library/mt613162.aspx). 
+ [Kit de développement mobile Engagement Xamarin](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [AZURE.NOTE] Pour effectuer ce didacticiel, vous devez disposer d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-ios-get-started).

##<a id="setup-azme"></a>Le programme d’installation Mobile Engagement pour votre application iOS

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Connecter votre application sur le serveur principal Engagement Mobile

Ce didacticiel présente une « intégration simple » qui est minimal nécessaires pour collecter les données et envoyer une notification push.

Nous allons créer une application simple avec Xamarin pour illustrer l’intégration :

###<a name="create-a-new-xamarinios-project"></a>Créer un projet Xamarin.iOS

1. Lancez Xamarin Studio. Accédez au **fichier** -> **Nouveau** -> **Solution** 

    ![][1]

2. Sélectionnez **Application Affichage seul**, vérifiez que la langue sélectionnée est **c#** , puis sur **suivant**.

    ![][2]

3. Entrez le **Nom de l’application** et l' **Identificateur de l’organisation** , puis sur **suivant**. 

    ![][3]

    > [AZURE.IMPORTANT] Vérifiez que le profil de publication que vous utilisez finalement pour déployer votre application iOS utilise un ID d’application qui correspond exactement avec l’identificateur de groupe que vous avez ici. 

4. Modifier le **Nom du projet**, le **Nom de la Solution** et **l’emplacement** si nécessaire, cliquez sur **créer**.

    ![][4]
 
Xamarin Studio créera l’application de démonstration dans lequel nous allez intégrer Mobile Engagement. 

###<a name="connect-your-app-to-mobile-engagement-backend"></a>Connecter votre application pour Mobile Engagement principal

1. Cliquez avec le bouton droit sur le dossier **Packages** dans les fenêtres de Solution, puis sélectionnez **Ajouter des Packages...**

    ![][5]

2. Recherchez le **Kit de développement logiciel Microsoft Azure Mobile Engagement Xamarin** et l’ajouter à votre solution.  

    ![][6]
   
3. Ouvrez **AppDelegate.cs** et ajoutez les éléments suivants à l’aide de déclaration :

        using Microsoft.Azure.Engagement.Xamarin;

4. Dans la méthode **FinishedLaunching** , ajoutez le code suivant pour l’initialisation de la connexion avec le serveur principal Mobile Engagement. Veillez à ajouter votre **ConnectionString**. Ce code utilise également un factice **NotificationIcon** qui est ajoutée par le Kit de développement Engagement Mobile à remplacer. 

        EngagementConfiguration config = new EngagementConfiguration {
                        ConnectionString = "YourConnectionStringFromAzurePortal",
                        NotificationIcon = UIImage.FromBundle("close")
                    };
        EngagementAgent.Init (config);

##<a id="monitor"></a>Activation de l’analyse en temps réel

Avant de commencer l’envoi de données et vérifier que les utilisateurs sont actives, vous devez envoyer au moins un écran vers le serveur principal Mobile Engagement.

1. Ouvrez **ViewController.cs** et ajoutez les éléments suivants à l’aide de déclaration :

        using Microsoft.Azure.Engagement.Xamarin;

2. Remplacez la classe à partir de laquelle `ViewController` hérite `UIViewController` à `EngagementViewController`. 

##<a id="monitor"></a>Application interrogez la surveillance en temps réel

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Activer les notifications push et dans l’application de messagerie

Engagement Mobile vous permet d’interagir avec d’autres utilisateurs et atteindre avec les notifications push et dans le contexte des campagnes de messagerie dans l’application. Ce module est appelé accessibles dans le portail d’Engagement Mobile.
Les sections suivantes configurer votre application pour les recevoir.

### <a name="modify-your-application-delegate"></a>Modifier votre délégué d’Application

1. Ouvrez **AppDelegate.cs** et ajoutez les éléments suivants à l’aide de déclaration :

        using System; 

2. Maintenant à l’intérieur de la `FinishedLaunching` méthode, ajoutez le code suivant pour vous inscrire à des messages de type Pousser après`EngagementAgent.init(...)`

        if (UIDevice.CurrentDevice.CheckSystemVersion(8,0))
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                (UIUserNotificationType.Badge |
                    UIUserNotificationType.Sound |
                    UIUserNotificationType.Alert),
                null);
            UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications ();
        }
        else
        {
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (
                UIRemoteNotificationType.Badge |
                UIRemoteNotificationType.Sound |
                UIRemoteNotificationType.Alert);
        }

3. Pour finir - mettre à jour ou ajoutez les méthodes suivantes :

        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, 
            Action<UIBackgroundFetchResult> completionHandler)
        {
            EngagementAgent.ApplicationDidReceiveRemoteNotification(userInfo, completionHandler);
        }

        public override void RegisteredForRemoteNotifications (UIApplication application, NSData deviceToken)
        {
            // Register device token on Engagement
            EngagementAgent.RegisterDeviceToken(deviceToken);
        }

        public override void FailedToRegisterForRemoteNotifications(UIApplication application, NSError error)
        {
            Console.WriteLine("Failed to register for remote notifications: Error '{0}'", error);
        }

4. Dans votre fichier **Info.plist** dans la solution, vérifiez que l' **Ensemble de guides identificateur** correspond à l' **ID de l’application** que vous avez dans votre profil mise à disponibilité dans le centre de développement Apple. 

    ![][7]

5. Dans le même fichier **Info.plist** , vérifiez que vous avez activé les **Activer les Modes de l’arrière-plan** et les **Notifications à distance**. 

    ![][8]

6. Exécuter l’application sur le périphérique que vous avez associé à ce profil de publication. 

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[1]: ./media/mobile-engagement-xamarin-ios-get-started/new-solution.png
[2]: ./media/mobile-engagement-xamarin-ios-get-started/app-type.png
[3]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-name.png
[4]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-confirm.png
[5]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget.png
[6]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget-azme.png
[7]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-confirm-bundle.png
[8]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-configure-push.png
