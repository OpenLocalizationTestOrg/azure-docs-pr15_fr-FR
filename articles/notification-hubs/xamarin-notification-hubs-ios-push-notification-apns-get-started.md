<properties
    pageTitle="iOS Notifications de transmission avec Hubs de Notification pour les applications Xamarin | Microsoft Azure"
    description="Dans ce didacticiel, vous allez apprendre à utiliser Azure Notification Hubs pour envoyer les notifications push pour une application iOS Xamarin."
    services="notification-hubs"
    keywords="iOS transmission notifications push des messages, les notifications de transmission, pousser le message"
    documentationCenter="xamarin"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="ios-push-notifications-with-notification-hubs-for-xamarin-apps"></a>iOS Notifications de transmission avec Hubs de Notification pour les applications Xamarin

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Vue d’ensemble
> [AZURE.IMPORTANT] Pour effectuer ce didacticiel, vous devez disposer d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

Ce didacticiel montre comment Azure Notification Hubs permet d’envoyer les notifications push pour une application iOS.
Vous devez créer une application vierge Xamarin.iOS qui reçoit les notifications push en utilisant le [Service de Notification Push Apple (APNs)](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html). Lorsque vous avez terminé, vous pourrez utiliser votre concentrateur de notification pour les notifications push pour tous les appareils exécutant votre application de diffusion. Le code terminé est disponible dans l' [application NotificationHubs] [ GitHub] exemple.

Ce didacticiel montre le scénario de diffusion de message push simple avec Hubs de Notification.

##<a name="prerequisites"></a>Conditions préalables

Ce didacticiel requiert les éléments suivants :

+ [Xcode 6.0][Install Xcode]
+ Un iOS 7.0 (ou version ultérieure) périphérique compatible
+ iOS participation au programme développeur
+ [Xamarin Studio]

   > [AZURE.NOTE] En raison de la configuration requise pour iOS transmission des notifications, vous devez déployer et tester l’exemple d’application sur un appareil iOS physique (iPhone ou iPad) plutôt que dans le simulator.

Fin de ce didacticiel est requis pour tous les autres didacticiels Hubs de Notification pour les applications d’iOS Xamarin.


[AZURE.INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]


##<a name="configure-your-notification-hub"></a>Configurer votre concentrateur de notification

Cette section décrit à créer un nouveau concentrateur de notification et configuration de l’authentification avec APNS en utilisant le certificat push **.p12** que vous avez créé. Si vous souhaitez utiliser un concentrateur de notification que vous avez déjà créé, vous pouvez ignorer à l’étape 5.

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


<ol start="7">
<li>
<p>Comme nous voulons configurer la connexion APNS, dans le portail Azure, ouvrir les paramètres de Notification concentrateur, ande cliquez sur <b>Services de Notification</b>, puis cliquez sur l’élément <b>Apple (APNS)</b> dans la liste. Une fois terminé, cliquez sur <b>Télécharger le certificat</b> , sélectionnez le certificat <b>.p12</b> que vous avez exporté plus tôt, ainsi que le mot de passe pour le certificat.</p>
<p>Veillez à sélectionner le mode <b>Sandbox</b> dans la mesure où vous allez envoyer des messages envoyés dans un environnement de développement. Utiliser uniquement le paramètre de <b>Production</b> si vous souhaitez envoyer les notifications push aux utilisateurs qui ont déjà acheté votre application à partir du magasin.</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-ios-get-started/notification-hubs-apns.png)

&emsp;&emsp;![](./media/notification-hubs-ios-get-started/notification-hubs-sandbox.png)


Votre concentrateur de notification est maintenant configuré pour fonctionner avec APNS, et vous avez les chaînes de connexion pour enregistrer votre application et envoyer des notifications de transmission.


##<a name="connect-your-app-to-the-notification-hub"></a>Se connecter votre application au hub de notification

#### <a name="create-a-new-project"></a>Créer un projet

1. Dans Xamarin Studio, créez un nouveau projet iOS et sélectionnez l' **API unifiée** > modèle**d’Application vue unique** .

    ![Xamarin Studio - Type de sélection d’une Application][31]

2. Ajoutez une référence au composant Azure de messagerie. Dans l’affichage de la Solution, cliquez sur le dossier **composants** pour votre projet et choisissez **Obtenir d’autres composants**. Recherchez le composant **Azure messagerie** et ajouter le composant à votre projet.

3. Dans **AppDelegate.cs**, ajoutez les éléments suivants à l’aide de déclaration :

        using WindowsAzure.Messaging;

4. Déclarer une instance de **SBNotificationHub**:

        private SBNotificationHub Hub { get; set; }

5. Créer une classe **Constants.cs** avec les variables suivantes :

        // Azure app-specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";


6. Dans **AppDelegate.cs**, mettez à jour **FinishedLaunching()** pour faire correspondre les éléments suivants :

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
                var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());

                UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
                UIApplication.SharedApplication.RegisterForRemoteNotifications ();
            } else {
                UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
                UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
            }

            return true;
        }

7. Remplacer la méthode **RegisteredForRemoteNotifications()** dans **AppDelegate.cs**:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            Hub = new SBNotificationHub(Constants.ConnectionString, Constants.NotificationHubPath);

            Hub.UnregisterAllAsync (deviceToken, (error) => {
                if (error != null)
                {
                    Console.WriteLine("Error calling Unregister: {0}", error.ToString());
                    return;
                }

                NSSet tags = null; // create tags if you want
                Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                    if (errorCallback != null)
                        Console.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
                });
            });
        }

8. Remplacer la méthode **ReceivedRemoteNotification()** dans **AppDelegate.cs**:

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }

9. Créer la méthode suivante **ProcessNotification()** dans **AppDelegate.cs**:

        void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
        {
            // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
            if (null != options && options.ContainsKey(new NSString("aps")))
            {
                //Get the aps dictionary
                NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;

                string alert = string.Empty;

                //Extract the alert text
                // NOTE: If you're using the simple alert by just specifying
                // "  aps:{alert:"alert msg here"}  ", this will work fine.
                // But if you're using a complex alert with Localization keys, etc.,
                // your "alert" object from the aps dictionary will be another NSDictionary.
                // Basically the JSON gets dumped right into a NSDictionary,
                // so keep that in mind.
                if (aps.ContainsKey(new NSString("alert")))
                    alert = (aps [new NSString("alert")] as NSString).ToString();

                //If this came from the ReceivedRemoteNotification while the app was running,
                // we of course need to manually process things like the sound, badge, and alert.
                if (!fromFinishedLaunching)
                {
                    //Manually show an alert
                    if (!string.IsNullOrEmpty(alert))
                    {
                        UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                        avAlert.Show();
                    }
                }
            }
        }

    > [AZURE.NOTE] Vous pouvez choisir de substituer **FailedToRegisterForRemoteNotifications()** pour gérer les situations comme aucune connexion réseau. Ceci est particulièrement important dans lequel l’utilisateur peut démarrer votre application en mode hors connexion (par exemple, avion) et que vous voulez gérer push scénarios spécifiques à votre application de messagerie.


10. Exécutez l’application sur votre appareil.


## <a name="sending-push-notifications"></a>Envoi des Notifications Push


Vous pouvez tester reçoit les notifications push dans votre application en envoyant des notifications dans le [Portail Azure] via la fonctionnalité **Tester Envoyer** dans l’ensemble d’outils de **résolution des problèmes** , directement dans la page concentrateur de notification, comme montré dans l’écran ci-dessous.

![](./media/notification-hubs-ios-get-started/notification-hubs-test-send.png)

Les notifications Push sont envoyées en règle générale, un service principale, tels que les Services mobiles ou ASP.NET à l’aide d’une bibliothèque compatible. Vous pouvez également utiliser l’API REST directement à envoyer des messages de push si une bibliothèque n’est pas disponible dans votre scénario. 

Dans ce didacticiel, nous Restez simple et simplement montrer test de votre application client en envoyant des notifications à l’aide du Kit de développement .NET pour hubs de notification dans une application console au lieu d’un service en aval. Nous vous recommandons le didacticiel [Utilisation Notification Hubs aux notifications push pour les utilisateurs](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) en tant que l’étape suivante pour envoyer des notifications à partir d’un serveur principal ASP.NET. Toutefois, les approches suivantes peuvent être utilisées pour envoyer des notifications :

* **Interface REST**: peut prendre en charge notification push sur n’importe quelle plate-forme serveur principal à l’aide de l' [interface REST](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).

* **Kit de développement logiciel Microsoft Azure Notification Hubs .NET**: dans le Gestionnaire de Package Nuget pour Visual Studio, exécuter le [Package d’installation Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

* **Node.js** : [comment utiliser les références de Notification de Node.js](notification-hubs-nodejs-push-notification-tutorial.md).

**Applications Mobile**: pour obtenir un exemple de l’envoi des notifications à partir d’un serveur principal Azure Application Service Mobile applications intégrées avec Hubs de Notification, voir [Ajouter les notifications de transmission pour votre application mobile](../app-service-mobile/app-service-mobile-ios-get-started-push.md).

* **Java / PHP**: pour obtenir un exemple de l’envoi des notifications de transmission à l’aide de l’API REST, voir « Comment utiliser Hubs de Notification à partir de Java/PHP » ([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)).


####<a name="optional-send-push-notifications-from-a-net-console-app"></a>(Facultatif) Envoyer les Notifications Push à partir d’une application Console .NET

Dans cette section, nous vous envoie des notifications de transmission à l’aide d’une application console .NET simple. Aux fins de cet exemple, nous allons la transformer dans un environnement de développement Windows avec Visual Studio sont déjà installées.

1. Dans Visual Studio, créez une nouvelle application console c# :

    ![Visual Studio - créer une nouvelle application console][213]

2. Dans Visual Studio, cliquez sur **Outils**, cliquez sur **Gestionnaire de Package NuGet**, puis cliquez sur **Gestionnaire de Package Console**.

    La console du Gestionnaire de package doit apparaître ancrée à la fin de votre espace de travail Visual Studio.

3. Dans la fenêtre Gestionnaire de Package Console, la valeur **par défaut de projet** votre nouveau projet d’application console et puis, dans la fenêtre de la console, exécutez la commande suivante :

        Install-Package Microsoft.Azure.NotificationHubs

    Cette opération ajoute une référence à la Notification de Hubs Azure SDK en utilisant le <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">package Microsoft.Azure.Notification Hubs NuGet</a>.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)


4. Ouvrir le `Program.cs` de fichiers et ajoutez le code suivant `using` instruction, permet de garantir que nous pouvons utiliser classes Azure et fonctions au sein de votre cours principale :

        using Microsoft.Azure.NotificationHubs;

3. Dans votre `Program` de classes, ajoutez la méthode suivante (n’oubliez pas de remplacer la **chaîne de connexion** et le **nom hub**) :

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

4. Ajoutez les lignes suivantes dans votre `Main` méthode :

         SendNotificationAsync();
         Console.ReadLine();

5. Appuyez sur la touche F5 pour exécuter l’application. Quelques secondes, une notification push doit s’afficher sur votre appareil. Si vous utilisez Wi-Fi ou un réseau de données cellulaires, vérifiez que vous disposez d’une connexion internet active sur l’appareil.

Vous pouvez trouver toutes les charges possibles dans l’Apple [Local et Guide de programmation de Notification Push].

####<a name="optional-send-notifications-from-a-mobile-service"></a>(Facultatif) Envoyer des Notifications à partir d’un Service Mobile

Dans cette section, nous vous envoie des notifications de transmission à l’aide d’un service mobile via un script nœud.

Pour envoyer une notification à l’aide d’un service mobile, procédez comme [prise en main Services mobiles], puis :

1. Connectez-vous au [Portail classique Azure], puis sélectionnez votre service mobile.

2. Sélectionnez l’onglet **Planificateur** dans la partie supérieure.

    ![Azure Portal classique - Scheduler][215]

3. Créer un nouveau travail planifié, insérer un nom, puis sélectionnez **à la demande**.

    ![Azure Portal classique - créer un nouveau travail][216]

4. Lorsque le travail est créé, cliquez sur le nom du travail. Cliquez ensuite sur l’onglet **Script** dans la barre supérieure.

5. Insérez le script suivant à l’intérieur de votre fonction scheduler. Veillez à remplacer les espaces réservés avec votre nom de concentrateur de notification et la chaîne de connexion pour *DefaultFullSharedAccessSignature* que vous avez obtenu précédemment. Cliquez sur **Enregistrer**.

        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<Hubname>', '<SAS Full access >');
        notificationHubService.apns.send(
            null,
            {"aps":
                {
                "alert": "Hello from Mobile Services!"
                }
            },
            function (error)
            {
                if (!error) {
                    console.warn("Notification successful");
                }
            }
        );


6. Cliquez sur **Exécuter une fois** dans la barre inférieure. Vous devez recevoir une alerte sur votre appareil.

##<a name="next-steps"></a>Étapes suivantes

Dans cet exemple simple, diffusée les notifications push pour tous vos appareils iOS. Afin de cibler des utilisateurs spécifiques, consultez le didacticiel [Utilisation Notification Hubs aux notifications push pour les utilisateurs]. Si vous voulez segmenter vos utilisateurs en groupes d’intérêt, vous pouvez lire [Hubs de Notification utiliser pour envoyer les informations de dernière minute]. En savoir plus sur l’utilisation de Hubs de Notification dans la [Notification Hubs conseils] et dans la [Notification Hubs procédures pour iOS].


<!-- Images. -->

[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png

[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png


[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png




<!-- URLs. -->
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Prise en main Services mobiles]: /develop/mobile/tutorials/get-started-xamarin-ios
[Portail classique Azure]: https://manage.windowsazure.com/
[Conseils de Hubs de notification]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs procédures pour iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Utiliser des Hubs de Notification pour les notifications push pour les utilisateurs]: /manage/services/notification-hubs/notify-users-aspnet
[Notification Hubs permet d’envoyer des informations de dernière minute]: /manage/services/notification-hubs/breaking-news-dotnet

[Local et le Guide de programmation notifications de transmission]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin Studio]: http://xamarin.com/download
[WindowsAzure.Messaging]: https://github.com/infosupport/WindowsAzure.Messaging.iOS
[Portail Azure]: https://portal.azure.com
