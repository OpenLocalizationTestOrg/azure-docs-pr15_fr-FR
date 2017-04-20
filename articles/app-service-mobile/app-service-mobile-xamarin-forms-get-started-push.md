<properties
    pageTitle="Ajouter les notifications push à votre application Xamarin.Forms | Microsoft Azure"
    description="Découvrez comment utiliser les services Azure pour envoyer les notifications push multi-plateformes à vos applications Xamarin.Forms."
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Ajouter les notifications push à votre application Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##<a name="overview"></a>Vue d’ensemble

Dans ce didacticiel, vous ajoutez push notifications à tous les projets résultant de la [Xamarin.Forms rapide démarrer](app-service-mobile-xamarin-forms-get-started.md) afin qu’une notification push est envoyée à tous les clients de disponibilité sur plusieurs plateformes chaque fois qu’un enregistrement est inséré.

Si vous n’utilisez pas le projet de serveur de démarrage rapide téléchargé, vous devez le package d’extension de notification push. Pour plus d’informations, voir [utiliser avec le Kit de développement pour applications Mobile Azure du serveur principal .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .

##<a name="prerequisites"></a>Conditions préalables

* Pour iOS, vous devez une [appartenance Apple Developer Program](https://developer.apple.com/programs/ios/) et un périphérique iOS physique car le [simulator iOS ne prend pas en charge les notifications de transmission](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

##<a name="configure-hub"></a>Configurer un concentrateur de Notification

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

##<a name="update-the-server-project-to-send-push-notifications"></a>Mettre à jour le projet serveur pour envoyer les notifications push

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]


##<a name="optional-configure-and-run-the-android-project"></a>(Facultatif) Configurer et exécuter le projet Android

Terminer cette section pour activer les notifications push pour le projet Xamarin.Forms Droid pour Android.


###<a name="enable-firebase-cloud-messaging-fcm"></a>Activer le Cloud Firebase Messaging (FCM)

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

###<a name="configure-the-mobile-app-backend-to-send-push-requests-using-fcm"></a>Configurer le système principal de l’application Mobile pour envoyer des demandes de transmission à l’aide de FCM

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

###<a name="add-push-notifications-to-the-android-project"></a>Ajoutez les notifications push au projet Android

Avec le serveur principal configuré avec FCM, nous pouvons ajouter des composants et codes au client pour enregistrer avec FCM, enregistrer les notifications push avec Hubs de Notification Azure via le système principal de l’application mobile et recevoir des notifications.

1. Dans le projet **Droid** , cliquez sur le dossier **composants** et cliquez sur **Obtenir plus composants...**, recherchez le composant de **Client de messagerie Google Cloud** ajoutez au projet. Ce composant prend en charge les notifications push pour un projet Xamarin Android.


2. Ouvrez le fichier de projet MainActivity.cs et ajoutez les éléments suivants à l’aide d’instruction en haut du fichier :

        using Gcm.Client;

3. Ajoutez le code suivant à la méthode **OnCreate** après l’appel à **LoadApplication**:

        try
        {
            // Check to ensure everything's setup right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            System.Diagnostics.Debug.WriteLine("Registering...");
            GcmClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);
        }
        catch (Java.Net.MalformedURLException)
        {
            CreateAndShowDialog("There was an error creating the client. Verify the URL.", "Error");
        }
        catch (Exception e)
        {
            CreateAndShowDialog(e.Message, "Error");
        }


4. Ajouter une nouvelle méthode d’assistance **CreateAndShowDialog** , comme suit :

        private void CreateAndShowDialog(String message, String title)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);

            builder.SetMessage (message);
            builder.SetTitle (title);
            builder.Create().Show ();
        }


5. Ajoutez le code suivant à la classe **MainActivity** :

        // Create a new instance field for this activity.
        static MainActivity instance = null;

        // Return the current activity instance.
        public static MainActivity CurrentActivity
        {
            get
            {
                return instance;
            }
        }

    Affichez l’instance actuelle **MainActivity** afin que nous puissions exécuter sur le thread de l’interface utilisateur principal.

6. Initialisation la `instance`, variable au début de la méthode **OnCreate** , comme suit.

        // Set the current instance of MainActivity.
        instance = this;

2. Ajouter un nouveau fichier de classe au projet **Droid** nommé `GcmService.cs`et vérifiez que les instructions suivantes **à l’aide** sont présentes dans la partie supérieure du fichier :

        using Android.App;
        using Android.Content;
        using Android.Media;
        using Android.Support.V4.App;
        using Android.Util;
        using Gcm.Client;
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.Text;


9. Ajoutez les demandes d’autorisation suivant en haut du fichier, après les instructions **à l’aide** et avant la déclaration **d’espace de noms** .

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]
        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]

10. Ajouter la définition de classe suivante à l’espace de noms. 

        [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
        public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
        {
            public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };
        }

    >[AZURE.NOTE]Remplacez **< numero_projet >** par votre numéro de projet que vous indiqué précédemment.   

11. Remplacez la classe **GcmService** vide par le code suivant, qui utilise le nouveau récepteur de diffusion :

         [Service]
         public class GcmService : GcmServiceBase
         {
             public static string RegistrationID { get; private set; }

             public GcmService()
                 : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
         }


12. Ajoutez le code suivant à la classe **GcmService** qui remplace le Gestionnaire d’événements **OnRegistered** et met en œuvre une méthode **Register** .

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose("PushHandlerBroadcastReceiver", "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            var push = TodoItemManager.DefaultManager.CurrentClient.GetPush();

            MainActivity.CurrentActivity.RunOnUiThread(() => Register(push, null));
        }

        public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
        {
            try
            {
                const string templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";

                JObject templates = new JObject();
                templates["genericMessage"] = new JObject
                {
                    {"body", templateBodyGCM}
                };

                await push.RegisterAsync(RegistrationID, templates);
                Log.Info("Push Installation Id", push.InstallationId.ToString());
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

        Note that this code uses the `messageParam` parameter in the template registration. 

13. Ajoutez le code suivant qui mettent en œuvre, **OnMessage**: 

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info("PushHandlerBroadcastReceiver", "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            //Store the message
            var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
            var edit = prefs.Edit();
            edit.PutString("last_msg", msg.ToString());
            edit.Commit();

            string message = intent.Extras.GetString("message");
            if (!string.IsNullOrEmpty(message))
            {
                createNotification("New todo item!", "Todo item: " + message);
                return;
            }

            string msg2 = intent.Extras.GetString("msg");
            if (!string.IsNullOrEmpty(msg2))
            {
                createNotification("New hub message!", msg2);
                return;
            }

            createNotification("Unknown message details", msg.ToString());
        }

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show ui
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Use Notification Builder
            NotificationCompat.Builder builder = new NotificationCompat.Builder(this);

            //Create the notification
            //we use the pending intent, passing our ui intent over which will get called
            //when the notification is tapped.
            var notification = builder.SetContentIntent(PendingIntent.GetActivity(this, 0, uiIntent, 0))
                    .SetSmallIcon(Android.Resource.Drawable.SymActionEmail)
                    .SetTicker(title)
                    .SetContentTitle(title)
                    .SetContentText(desc)

                    //Set the notification sound
                    .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))

                    //Auto cancel will remove the notification once the user touches it
                    .SetAutoCancel(true).Build();

            //Show the notification
            notificationManager.Notify(1, notification);
        }

    Gère les notifications entrantes et les envoyer au responsable de notification à afficher.

14. **GcmServiceBase** requiert également implémenter les méthodes de gestionnaire **OnUnRegistered** et **SurErreur** , vous pouvez procédez comme suit :

        protected override void OnUnRegistered(Context context, string registrationId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
        }

        protected override void OnError(Context context, string errorId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
        }

Vous êtes maintenant prêt le test des notifications push dans l’application en cours d’exécution sur un appareil Android ou l’émulateur.

###<a name="test-push-notifications-in-your-android-app"></a>Notifications de transmission de test dans votre application Android

Les deux premières étapes sont nécessaires uniquement lorsque vous testez sur un émulateur.

1. Vérifiez que vous déployez à ou débogage sur un périphérique virtuel lequel APIs de Google est définie comme cible, comme illustré ci-dessous dans le Gestionnaire de périphériques virtuel Android (AVD).

2. Ajouter un compte Google à l’appareil Android en cliquant sur **applications** > **paramètres** > **Ajouter un compte**, puis suivez les invites pour utiliser Ajouter un compte Google existant sur l’appareil afin de créer un nouveau.

1. Dans Visual Studio ou Xamarin Studio, cliquez avec le bouton droit sur le projet **Droid** et cliquez sur **définir comme projet de démarrage**.

2. Appuyez sur le bouton **exécuter** pour générer le projet et lancer l’application sur votre appareil Android ou émulateur.

3. Dans l’application, tapez une tâche, puis cliquez sur le signe plus (**+**) icône.

4. Vérifiez la réception d’une notification lorsqu’un élément est ajouté.


##<a name="optional-configure-and-run-the-ios-project"></a>(Facultatif) Configurer et exécuter le projet iOS

Cette section s’applique à l’exécution du projet iOS Xamarin pour les appareils iOS. Vous pouvez ignorer cette section si vous ne travaillez pas avec des appareils iOS.

[AZURE.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]


####<a name="configure-the-notification-hub-for-apns"></a>Configurer le hub de notification pour APNS

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

Ensuite, vous allez configurer le paramètre du projet iOS dans Xamarin Studio ou Visual Studio.

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]


####<a name="add-push-notifications-to-your-ios-app"></a>Ajouter les notifications push dans votre application iOS

1. Dans le projet **iOS** , ouvrez AppDelegate.cs ajoutez l’instruction **using** suivante en haut du fichier de code.

        using Newtonsoft.Json.Linq;

4. Dans la classe **AppDelegate** , ajoutez une substitution de l’événement **RegisteredForRemoteNotifications** inscription des notifications :

        public override void RegisteredForRemoteNotifications(UIApplication application, 
            NSData deviceToken)
        {
            const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyAPNS}
                };

            // Register for push with your mobile app
            Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }

5. Dans **AppDelegate**, ajoutez également le remplacement suivant pour le Gestionnaire d’événements **DidReceivedRemoteNotification** :

        public override void DidReceiveRemoteNotification(UIApplication application, 
            NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps[new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

    Cette méthode gère les notifications entrantes pendant l’exécution de l’application.

2. Dans la classe **AppDelegate** , ajoutez le code suivant à la méthode **FinishedLaunching** : 

        // Register for push notifications.
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

    Cela permet de prise en charge des notifications à distance et demandes transmission d’inscription.

Votre application est désormais mis à jour pour prendre en charge les notifications push.

####<a name="test-push-notifications-in-your-ios-app"></a>Notifications de transmission de test dans votre application iOS

1. Cliquez avec le bouton droit sur le projet iOS, puis cliquez sur **définir comme projet StartPp**.

2. Appuyez sur le bouton **exécuter** ou **F5** dans Visual Studio pour générer le projet et lancer l’application dans un appareil iOS, puis cliquez sur **OK** pour accepter les notifications push.

    > [AZURE.NOTE] Vous devez explicitement accepter les notifications push à partir de votre application. Cette requête se produit uniquement la première fois que l’application s’exécute.

3. Dans l’application, tapez une tâche, puis cliquez sur le signe plus (**+**) icône.

4. Vérifiez qu’une notification correctement après réception, puis cliquez sur **OK** pour fermer la notification.


##<a name="optional-configure-and-run-the-windows-projects"></a>(Facultatif) Configurer et exécuter les projets Windows

Cette section s’adresse en cours d’exécution Xamarin.Forms WinApp et des projets de WinPhone81 pour les appareils Windows. Ces étapes prennent également en charge les projets universel Windows plateforme (UWP). Vous pouvez ignorer cette section si vous ne travaillez pas avec des appareils Windows.


####<a name="register-your-windows-app-for-push-notifications-with-wns"></a>Enregistrer votre application Windows pour les notifications push avec WNS

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


####<a name="configure-the-notification-hub-for-wns"></a>Configurer le hub de notification pour WNS

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


####<a name="add-push-notifications-to-your-windows-app"></a>Ajouter les notifications push dans votre application Windows

1. Dans Visual Studio, ouvrez **App.xaml.cs** dans un projet Windows et ajoutez les instructions suivantes **à l’aide** .

        using Newtonsoft.Json.Linq;
        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;
        using <your_TodoItemManager_portable_class_namespace>;

    Remplacer `<your_TodoItemManager_portable_class_namespace>` avec espace de noms de votre projet portable qui contient le `TodoItemManager` cours.
 

2. Dans App.xaml.cs ajoutez la méthode **InitNotificationsAsync** suivante : 

        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            const string templateBodyWNS = 
                "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

            JObject headers = new JObject();
            headers["X-WNS-Type"] = "wns/toast";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBodyWNS},
                {"headers", headers} // Needed for WNS.
            };

            await TodoItemManager.DefaultManager.CurrentClient.GetPush()
                .RegisterAsync(channel.Uri, templates);
        }

    Cette méthode obtient le canal de notification push et enregistre un modèle pour recevoir des notifications de modèle à partir de votre concentrateur de notification. Une notification de modèle qui prend en charge *messageParam* est remise à ce client.

3. Dans App.xaml.cs, mettre à jour la définition de méthode du Gestionnaire d’événements de **OnLaunched** en ajoutant le `async` touche de modification, puis ajoutez la ligne suivante du code à la fin de la méthode : 

        await InitNotificationsAsync();

    Cela permet de garantir que l’enregistrement de notification push est créé ou actualisé chaque fois que l’application est lancée. Il est important de le faire pour vous assurer que le canal push WNS est toujours actif.  

4. Dans l’Explorateur de solutions pour Visual Studio, ouvrez le fichier **Package.appxmanifest** et définissez **Toast Capable** **Oui** sous **Notifications**.

5. Créer l’application et vérifiez que vous n’avez aucune erreur.  Vous application client doit maintenant enregistrer pour les notifications de modèle à partir de l’application Mobile principal. Répétez cette section pour chaque projet Windows de votre solution.


####<a name="test-push-notifications-in-your-windows-app"></a>Notifications de transmission de test dans votre application Windows

1. Dans Visual Studio, cliquez avec le bouton droit sur un projet Windows, puis cliquez sur **définir comme projet de démarrage**.

2. Appuyez sur le bouton **exécuter** pour générer le projet et lancer l’application.

3. Dans l’application, tapez un nom pour une nouvelle todoitem, puis cliquez sur le signe plus (**+**) icône pour l’ajouter.

4. Vérifiez la réception d’une notification lorsque l’élément est ajouté.

##<a name="next-steps"></a>Étapes suivantes

En savoir plus sur les notifications push :

* [Diagnostiquer les problèmes de notification push](../notification-hubs/notification-hubs-push-notification-fixer.md)  
Il existe différentes raisons pourquoi les notifications peuvent être perdues ou ne finissent pas sur les appareils. Cette rubrique vous montre comment analyser et identifier la cause des échecs de notification push. 

Vous pouvez passer à un des didacticiels suivants :

* [Ajouter une authentification dans votre application](app-service-mobile-xamarin-forms-get-started-users.md)  
Découvrez comment authentifier les utilisateurs de votre application avec un fournisseur d’identité.

* [Activer la synchronisation hors connexion pour votre application](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Découvrez comment ajouter une prise en charge en mode hors connexion votre application à l’aide d’un serveur principal application Mobile. Synchronisation hors connexion permet aux utilisateurs finaux d’interagir avec une application mobile&mdash;l’affichage, l’ajout ou modification de données&mdash;même lorsqu’il n’y a aucune connexion réseau.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

