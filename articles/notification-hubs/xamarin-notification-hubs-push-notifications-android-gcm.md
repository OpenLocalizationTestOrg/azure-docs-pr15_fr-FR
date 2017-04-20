<properties
    pageTitle="Prise en main avec Hubs de Notification pour les applications Xamarin.Android | Microsoft Azure"
    description="Dans ce didacticiel, vous allez apprendre à utiliser Azure Notification Hubs pour envoyer les notifications push pour une application Xamarin Android."
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"
    documentationCenter="xamarin"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="get-started-with-notification-hubs-with-xamarin-for-android"></a>Prise en main avec Hubs de Notification avec Xamarin pour Android

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Vue d’ensemble

Ce didacticiel montre comment Azure Notification Hubs permet d’envoyer les notifications push pour une application Xamarin.Android.
Vous devez créer une application vierge Xamarin.Android qui reçoit les notifications push à l’aide de Google Cloud messagerie (GCM). Lorsque vous avez terminé, vous pourrez utiliser votre concentrateur de notification pour les notifications push pour tous les appareils exécutant votre application de diffusion. Le code terminé est disponible dans l' [application NotificationHubs] [ GitHub] exemple.

Ce didacticiel montre le scénario simple diffusion en utilisant des centraux et de Notification.


## <a name="before-you-begin"></a>Avant de commencer

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Le code complété pour ce didacticiel, vous pouvez trouver sur GitHub [ici](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid).



##<a name="prerequisites"></a>Conditions préalables

Ce didacticiel requiert les éléments suivants :

+ Visual Studio avec Xamarin sur Windows ou Xamarin Studio sur Mac OS x complète instructions d’installation se trouvent sur [configurer et installer pour Visual Studio et Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).
+ Compte Google actif
+ [Azure composant de messagerie]
+ [Composant du Client de messagerie Google Cloud]

Fin de ce didacticiel est requis pour tous les autres didacticiels Hubs de Notification pour les applications Xamarin.Android.

> [AZURE.IMPORTANT] Pour effectuer ce didacticiel, vous devez disposer d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-android-get-started%2F).

##<a name="enable-google-cloud-messaging"></a>Activer le Cloud Google messagerie

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##<a name="configure-your-notification-hub"></a>Configurer votre concentrateur de notification

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


<ol start="7">
<li><p>Cliquez sur l’onglet <b>configurer</b> en haut, entrez la valeur de <b>Clé d’API</b> que vous avez obtenu dans la section précédente, puis cliquez sur <b>Enregistrer</b>.</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-android-get-started/notification-hub-configure-android.png)


Votre concentrateur de notification est maintenant configuré pour fonctionner avec GCM, et vous avez les chaînes de connexion à la fois à enregistrer votre application pour recevoir des notifications et envoyer les notifications push.

##<a name="connect-your-app-to-the-notification-hub"></a>Se connecter votre application au hub de notification

###<a name="create-a-new-project"></a>Créer un projet

1. Dans Xamarin Studio, cliquez sur **Nouvelle Solution**, cliquez sur **Application Android**et cliquez sur **suivant**.

    ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png)

2. Entrez votre **Nom de l’application** et **l’identificateur**. Cliquez sur les **Plateformes cible** que vous voulez prend en charge, puis cliquez sur **suivant** et **créer**.

    ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png)


    Cela crée un nouveau projet Android.

2. Ouvrez les propriétés du projet par clic droit sur votre projet dans l’affichage de la Solution et en choisissant **Options**. Sélectionnez l’élément **Application Android** dans la section **Générer** .

    Vérifiez que la première lettre du **nom du Package** est en minuscules.

    > [AZURE.IMPORTANT] La première lettre du nom du package doit être en minuscule. Dans le cas contraire, vous recevrez erreurs manifeste d’application lorsque vous enregistrez votre **BroadcastReceiver** et **IntentFilter** pour les notifications push ci-dessous.

    ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub--xamarin-android-app-options.png)


3. Vous pouvez également définir la **version minimale Android** à un autre niveau de l’API.

4. Vous pouvez également définir la **version cible Android** vers une autre version de l’API que vous souhaitez cibler (doit être au niveau de l’API 8 ou version ultérieure).

Cliquez sur **OK** et fermez la boîte de dialogue Options de Project.


###<a name="add-the-required-components-to-your-project"></a>Ajouter les composants requis à votre projet

Le Google Cloud Client de messagerie disponibles dans le magasin de composant Xamarin simplifie le processus de prise en charge les notifications push dans Xamarin.Android.

1. Cliquez sur le dossier composants dans l’application Xamarin.Android et choisissez **Obtenir d’autres composants**.

2. Recherchez le composant **Azure messagerie** et ajoutez-la au projet.

3. Recherchez le composant de **Client de messagerie Google Cloud** et ajoutez au projet.


###<a name="set-up-notification-hubs-in-your-project"></a>Configurer la hubs de notification dans votre projet

1. Collectez les informations suivantes pour votre plateforme d’application et de notification Android :

    - **GoogleProjectNumber**: obtenir cette valeur numéro de projet à partir de la vue d’ensemble de votre application sur le portail de développement Google. Vous avez noté de cette valeur précédemment lorsque vous avez créé l’application sur le portail.
    - **Écouter la chaîne de connexion**: dans le tableau de bord dans le [Portail classique Azure], cliquez sur **chaînes de connexion à afficher**. Copiez la chaîne de connexion *DefaultListenSharedAccessSignature* pour cette valeur.
    - **Nom Hub**: il s’agit du nom de votre plateforme à partir du [Portail classique Azure]. Par exemple, *mynotificationhub2*.

    Créer une classe **Constants.cs** pour votre projet Xamarin et définissez les valeurs de la constante suivantes dans la classe. Remplacer les espaces réservés par vos valeurs.

        public static class Constants
        {
            public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number
            public const string ListenConnectionString = "<Listen connection string>";
            public const string NotificationHubName = "<hub name>";
        }

2. Ajoutez les éléments suivants à l’aide d’instructions à **MainActivity.cs**:

        using Android.Util;
        using Gcm.Client;

3. Ajouter une variable d’instance à la `MainActivity` classe qui sera utilisé pour afficher une boîte de dialogue alerte lorsque l’application est en cours d’exécution :

        public static MainActivity instance;


3. Créer la méthode suivante de la classe **MainActivity** :

        private void RegisterWithGCM()
        {
            // Check to ensure everything's set up right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            Log.Info("MainActivity", "Registering...");
            GcmClient.Register(this, Constants.SenderID);
        }

4. Dans la `OnCreate` initialisation de la méthode de **MainActivity.cs**, la `instance` variable et ajoutez un appel à `RegisterWithGCM`:

        protected override void OnCreate (Bundle bundle)
        {
            instance = this;

            base.OnCreate (bundle);

            // Set your view from the "main" layout resource
            SetContentView (Resource.Layout.Main);

            // Get your button from the layout resource,
            // and attach an event to it
            Button button = FindViewById<Button> (Resource.Id.myButton);

            RegisterWithGCM();
        }


4. Créer une nouvelle classe, **MyBroadcastReceiver**.

    > [AZURE.NOTE] Nous allons découvrir la création d’une classe **BroadcastReceiver** à partir de zéro ci-dessous. Toutefois, une rapide manuellement consiste à créer **MyBroadcastReceiver.cs** pour faire référence au fichier **GcmService.cs** figurant dans l’exemple de projet Xamarin.Android inclus avec les [exemples de NotificationHubs][GitHub]. Duplication de **GcmService.cs** et en modifiant les noms de classe peuvent être un bon point de départ également.

5. Ajoutez les éléments suivants à l’aide des instructions **MyBroadcastReceiver.cs** (faisant référence à l’assembly que vous avez ajouté précédemment et le composant) :

        using System.Collections.Generic;
        using System.Text;
        using Android.App;
        using Android.Content;
        using Android.Util;
        using Gcm.Client;
        using WindowsAzure.Messaging;

5. Dans **MyBroadcastReceiver.cs**, ajoutez les demandes d’autorisation suivant entre les instructions **à l’aide** et la déclaration **d’espace de noms** :

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

        //GET_ACCOUNTS is needed only for Android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

6. Dans **MyBroadcastReceiver.cs**, modifiez la classe **MyBroadcastReceiver** pour faire correspondre les éléments suivants :

        [BroadcastReceiver(Permission=Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        public class MyBroadcastReceiver : GcmBroadcastReceiverBase<PushHandlerService>
        {
            public static string[] SENDER_IDS = new string[] { Constants.SenderID };

            public const string TAG = "MyBroadcastReceiver-GCM";
        }

7. Ajouter une autre classe dans **MyBroadcastReceiver.cs** nommé **PushHandlerService**, qui est dérivée de **GcmServiceBase**. Veillez à appliquer l’attribut de **Service** à la classe :

        [Service] // Must use the service tag
        public class PushHandlerService : GcmServiceBase
        {
            public static string RegistrationID { get; private set; }
            private NotificationHub Hub { get; set; }

            public PushHandlerService() : base(Constants.SenderID)
            {
                Log.Info(MyBroadcastReceiver.TAG, "PushHandlerService() constructor");
            }
        }


8. **GcmServiceBase** met en œuvre les méthodes **OnRegistered()**, **OnUnRegistered()**, **OnMessage()**, **OnRecoverableError()**et **OnError()**. Notre classe d’implémentation **PushHandlerService** devez substituer ces méthodes, et ces méthodes seront déclenche en réponse à l’interaction avec le hub de notification.


9. Remplacer la méthode **OnRegistered()** dans **PushHandlerService** en utilisant le code suivant :

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            createNotification("PushHandlerService-GCM Registered...",
                                "The device has been Registered!");

            Hub = new NotificationHub(Constants.NotificationHubName, Constants.ListenConnectionString,
                                        context);
            try
            {
                Hub.UnregisterAll(registrationId);
            }
            catch (Exception ex)
            {
                Log.Error(MyBroadcastReceiver.TAG, ex.Message);
            }

            //var tags = new List<string>() { "falcons" }; // create tags if you want
            var tags = new List<string>() {};

            try
            {
                var hubRegistration = Hub.Register(registrationId, tags.ToArray());
            }
            catch (Exception ex)
            {
                Log.Error(MyBroadcastReceiver.TAG, ex.Message);
            }
        }

    > [AZURE.NOTE] Dans le code **OnRegistered()** ci-dessus, vous devez noter la possibilité de spécifier des balises pour vous inscrire à des canaux de messagerie spécifiques.

10. Remplacer la méthode **OnMessage** dans **PushHandlerService** en utilisant le code suivant :

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info(MyBroadcastReceiver.TAG, "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            string messageText = intent.Extras.GetString("message");
            if (!string.IsNullOrEmpty (messageText))
            {
                createNotification ("New hub message!", messageText);
            }
            else
            {
                createNotification ("Unknown message details", msg.ToString ());
            }
        }

11. Ajoutez les méthodes suivantes **createNotification** et **dialogNotify** à **PushHandlerService** pour informer les utilisateurs lors de la réception d’une notification.

    >[AZURE.NOTE] Création de notification dans la version Android 5.0 et versions ultérieure représente un départ significatif de celle des versions précédentes. Si vous testez ceci sur Android 5.0 ou version ultérieure, l’application devez exécuter pour recevoir une notification. Pour plus d’informations, voir [Notifications Android](http://go.microsoft.com/fwlink/?LinkId=615880).

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show UI
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Create the notification
            var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);

            //Auto-cancel will remove the notification once the user touches it
            notification.Flags = NotificationFlags.AutoCancel;

            //Set the notification info
            //we use the pending intent, passing our ui intent over, which will get called
            //when the notification is tapped.
            notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));

            //Show the notification
            notificationManager.Notify(1, notification);
            dialogNotify (title, desc);
        }

        protected void dialogNotify(String title, String message)
        {

            MainActivity.instance.RunOnUiThread(() => {
                AlertDialog.Builder dlg = new AlertDialog.Builder(MainActivity.instance);
                AlertDialog alert = dlg.Create();
                alert.SetTitle(title);
                alert.SetButton("Ok", delegate {
                    alert.Dismiss();
                });
                alert.SetMessage(message);
                alert.Show();
            });
        }


12. Substituer des membres résumés **OnUnRegistered()**, **OnRecoverableError()**et **OnError()** afin que votre code compile :

        protected override void OnUnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Unregistered: " + registrationId);

            createNotification("GCM Unregistered...", "The device has been unregistered!");
        }

        protected override bool OnRecoverableError(Context context, string errorId)
        {
            Log.Warn(MyBroadcastReceiver.TAG, "Recoverable Error: " + errorId);

            return base.OnRecoverableError (context, errorId);
        }

        protected override void OnError(Context context, string errorId)
        {
            Log.Error(MyBroadcastReceiver.TAG, "GCM Error: " + errorId);
        }


##<a name="run-your-app-in-the-emulator"></a>Exécuter votre application dans l’émulateur

Si vous exécutez cette application dans l’émulateur, assurez-vous que vous utilisez un Android virtuel appareil (AVD) qui prend en charge APIs de Google.

> [AZURE.IMPORTANT] Pour recevoir les notifications push, vous devez configurer un compte Google sur votre appareil Android virtuel. (Dans l’émulateur, accédez aux **paramètres** , puis cliquez sur **Ajouter un compte**.) En outre, vérifiez que l’émulateur est connecté à Internet.

>[AZURE.NOTE] Création de notification dans la version Android 5.0 et versions ultérieure représente un départ significatif de celle des versions précédentes. Pour plus d’informations, voir [Notifications Android](http://go.microsoft.com/fwlink/?LinkId=615880).


1. Dans les **Outils**, cliquez sur **Ouvrir le Gestionnaire d’émulateur Android**et sélectionnez votre appareil, puis cliquez sur **Modifier**.

    ![][18]

2. Sélectionnez **API de Google** dans **cible**, puis cliquez sur **OK**.

    ![][19]

3. Dans la barre d’outils supérieure, cliquez sur **exécuter**, puis sélectionnez puis votre application. Cela démarre l’émulateur et s’exécute à l’application.

  L’application récupère les *attributs de registrationId* GCM et inscrit auprès du concentrateur de notification.

##<a name="send-notifications-from-your-backend"></a>Envoyer des notifications à partir de votre serveur principal


Vous pouvez tester la réception de notifications dans votre application en envoyant des notifications dans le [Portail classique Azure] via l’onglet débogage dans le hub de notification, comme montré dans l’écran ci-dessous.

![][30]


Les notifications Push sont envoyées en règle générale, dans un service en aval, tels que les Services mobiles ou ASP.NET via une bibliothèque compatible. Vous pouvez également utiliser l’API REST directement à envoyer des messages de notification si une bibliothèque n’est pas disponible pour votre version de serveur.

Voici une liste de certaines autres didacticiels vous souhaiterez peut-être passer en revue d’envoi des notifications :

- ASP.NET : Voir [Utiliser Notification Hubs aux notifications push pour les utilisateurs].
- Azure Notification Hubs Java SDK : Découvrez [comment utiliser Hubs de Notification à partir de Java](notification-hubs-java-push-notification-tutorial.md) d’envoi des notifications à partir de Java. Cela a été testé dans Eclipse pour le développement Android.
- PHP : Découvrez [comment utiliser Hubs de Notification à partir de PHP](notification-hubs-php-push-notification-tutorial.md).


Dans les sous-sections suivantes de ce didacticiel, vous envoyez des notifications à l’aide d’une application console .NET et à l’aide d’un service mobile via un script nœud.

####<a name="optional-send-notifications-by-using-a-net-app"></a>(Facultatif) Envoyer des notifications à l’aide d’une application .NET

Dans cette section, nous vous envoie des notifications à l’aide d’une application console .NET

1. Créer une nouvelle application console c# :

    ![][20]

2. Dans Visual Studio, cliquez sur **Outils**, cliquez sur **Gestionnaire de Package NuGet**, puis cliquez sur **Gestionnaire de Package Console**.

    Cela permet d’afficher la Console du Gestionnaire de Package dans Visual Studio.

3. Dans la fenêtre Gestionnaire de Package Console, la valeur **par défaut de projet** votre nouveau projet d’application console et puis, dans la fenêtre de la console, exécutez la commande suivante :

        Install-Package Microsoft.Azure.NotificationHubs

    Cette opération ajoute une référence à la Notification de Hubs Azure SDK en utilisant le <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">package Microsoft.Azure.Notification Hubs NuGet</a>.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)

4. Ouvrez le fichier Program.cs et ajoutez le code suivant `using` instruction :

        using Microsoft.Azure.NotificationHubs;

5. Dans votre `Program` de classes, ajoutez la méthode suivante. Mettre à jour le texte d’espace réservé avec votre *DefaultFullSharedAccessSignature* chaîne et concentrateur nom de la connexion à partir du [Portail classique Azure].

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"message\":\"Hello from Azure!\"}}");
        }

6. Ajoutez les lignes suivantes dans votre méthode **Main** :

         SendNotificationAsync();
         Console.ReadLine();

7. Appuyez sur la touche F5 pour exécuter l’application. Vous recevrez une notification dans l’application.

    ![][21]

####<a name="optional-send-notifications-by-using-a-mobile-service"></a>(Facultatif) Envoyer des notifications à l’aide d’un service mobile

1. Suivez [prise en main Services mobiles].

1. Connectez-vous au [Portail classique Azure], puis sélectionnez votre service mobile.

2. Sélectionnez l’onglet **Planificateur** dans la partie supérieure.

    ![][22]

3. Créer un nouveau travail planifié, insérer un nom, puis sélectionnez **à la demande**.

    ![][23]

4. Lorsque le travail est créé, cliquez sur le nom du travail. Cliquez ensuite sur l’onglet **Script** dans la barre supérieure.

5. Insérez le script suivant à l’intérieur de votre fonction scheduler. Veillez à remplacer les espaces réservés avec votre nom de concentrateur de notification et la chaîne de connexion pour *DefaultFullSharedAccessSignature* que vous avez obtenu précédemment. Cliquez sur **Enregistrer**.

        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string>');
        notificationHubService.gcm.send(null,'{"data":{"message" : "Hello from Mobile Services!"}}',
          function (error)
          {
            if (!error) {
               console.warn("Notification successful");
            }
            else
            {
              console.warn("Notification failed" + error);
            }
          }
        );

6. Cliquez sur **Exécuter une fois** dans la barre inférieure. Vous devez recevoir une notification toast.

##<a name="next-steps"></a>Étapes suivantes

Dans cet exemple simple, diffusée notifications à tous vos appareils Android. Afin de cibler des utilisateurs spécifiques, consultez le didacticiel [Utilisation Notification Hubs aux notifications push pour les utilisateurs]. Si vous voulez segmenter vos utilisateurs en groupes d’intérêt, vous pouvez lire [Hubs de Notification utiliser pour envoyer les informations de dernière minute]. En savoir plus sur l’utilisation de Hubs de Notification dans la [Notification Hubs conseils] et dans la [Notification Hubs procédures pour Android].

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->

[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler2.png

[30]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Prise en main Services mobiles]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js

[Portail classique Azure]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Conseils de Hubs de notification]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs procédures pour Android]: http://msdn.microsoft.com/library/dn282661.aspx

[Utiliser des Hubs de Notification pour les notifications push pour les utilisateurs]: /manage/services/notification-hubs/notify-users-aspnet
[Notification Hubs permet d’envoyer des informations de dernière minute]: /manage/services/notification-hubs/breaking-news-dotnet
[GCMClient Component page]: http://components.xamarin.com/view/GCMClient
[Xamarin.NotificationHub GitHub page]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Composant du Client de messagerie Google Cloud]: http://components.xamarin.com/view/GCMClient/
[Azure composant de messagerie]: http://components.xamarin.com/view/azure-messaging
