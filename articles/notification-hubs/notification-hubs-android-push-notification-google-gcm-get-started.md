<properties
    pageTitle="Envoi des notifications push pour Android avec Hubs de Notification Azure | Microsoft Azure"
    description="Dans ce didacticiel, vous allez apprendre à utiliser Azure Notification Hubs aux notifications push pour les appareils Android."
    services="notification-hubs"
    documentationCenter="android"
    keywords="notifications Push, notification push, notification push android"
    authors="ysxu"
    manager="erikre"
    editor=""/>
<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.date="07/05/2016"
    ms.author="yuaxu"/>

# <a name="sending-push-notifications-to-android-with-azure-notification-hubs"></a>Envoi des notifications push pour Android avec Hubs de Notification Azure

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Vue d’ensemble

> [AZURE.IMPORTANT] Cette rubrique explique les notifications push avec Google Cloud messagerie (GCM). Si vous n’utilisez pas Firebase Cloud messagerie (FCM de Google), voir [envoi des notifications push pour Android avec Hubs de Notification Azure et FCM](notification-hubs-android-push-notification-google-fcm-get-started.md).

Ce didacticiel montre comment Azure Notification Hubs permet d’envoyer des notifications de transmission à l’application Android.
Vous devez créer une application vierge Android qui reçoit les notifications push à l’aide de Google Cloud messagerie (GCM).

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Le code complété pour ce didacticiel peut être téléchargé à partir de GitHub [ici](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStarted).


##<a name="prerequisites"></a>Conditions préalables

> [AZURE.IMPORTANT] Pour effectuer ce didacticiel, vous devez disposer d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started).

Outre un compte Azure actif présenté ci-dessus, ce didacticiel requiert uniquement la dernière version de [Studio Android](http://go.microsoft.com/fwlink/?LinkId=389797).

Fin de ce didacticiel est requis pour tous les autres didacticiels Hubs de Notification pour les applications Android.

##<a name="creating-a-project-that-supports-google-cloud-messaging"></a>Création d’un projet qui prend en charge la messagerie Cloud Google

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]


##<a name="configure-a-new-notification-hub"></a>Configurer un nouveau concentrateur de notification


[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


&emsp;&emsp;6. dans la carte de **paramètres** , sélectionnez **Les Services de Notification** , puis **Google (GCM)**. Entrez la clé API et cliquez sur **Enregistrer**.

&emsp;&emsp;![Notification Azure Hubs - Google (GCM)](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Votre concentrateur de notification est maintenant configuré pour fonctionner avec GCM, et vous avez les chaînes de connexion à la fois à enregistrer votre application pour recevoir et envoyer des notifications de transmission.

##<a id="connecting-app"></a>Se connecter votre application au hub de notification

###<a name="create-a-new-android-project"></a>Créer un projet Android

1. Dans Android Studio, démarrez un nouveau projet Studio Android.

    ![Studio Android - nouveau projet][13]

2. Cliquez sur le facteur de forme **téléphones et tablettes** et le **Kit de développement logiciel Minimum** à prendre en charge. Cliquez sur **suivant**.

    ![Studio Android - flux de travail de projet Création][14]

3. Sélectionnez **Activité vide** pour l’activité principale et cliquez sur **suivant**, puis cliquez sur **Terminer**.

###<a name="add-google-play-services-to-the-project"></a>Ajouter des services Google Play au projet

[AZURE.INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

###<a name="adding-azure-notification-hubs-libraries"></a>Ajout de bibliothèques Hubs de Notification Azure


1. Dans la `Build.Gradle` pour l' **application**, ajoutez les lignes suivantes dans la section **dépendances** .

        compile 'com.microsoft.azure:notification-hubs-android-sdk:0.4@aar'
        compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'

2. Ajoutez le référentiel suivant après la section **dépendances** .

        repositories {
            maven {
                url "http://dl.bintray.com/microsoftazuremobile/SDK"
            }
        }

### <a name="updating-the-androidmanifestxml"></a>Mise à jour de la AndroidManifest.xml.


1. Pour prendre en charge GCM, nous devons implémenter un service d’ID de l’Instance écoute dans notre code qui est utilisé pour [obtenir des jetons de l’inscription](https://developers.google.com/cloud-messaging/android/client#sample-register) à l’aide [Instance ID API de Google](https://developers.google.com/instance-id/). Dans ce didacticiel, nous allons appeler la classe `MyInstanceIDService`. 
 
    Ajoutez la définition de service suivante vers le fichier AndroidManifest.xml, à l’intérieur du `<application>` balise. Remplacer le `<your package>` espace réservé avec le nom de votre package réelle affichée en haut de la `AndroidManifest.xml` fichier.

        <service android:name="<your package>.MyInstanceIDService" android:exported="false">
            <intent-filter>
                <action android:name="com.google.android.gms.iid.InstanceID"/>
            </intent-filter>
        </service>


2. Une fois que nous avons reçu notre jeton d’inscription GCM à partir de l’API ID Instance, nous allons l’utiliser pour [Enregistrer avec le Hub de Notification Azure](notification-hubs-push-notification-registration-management.md). Nous prend en charge l’enregistrement dans l’arrière-plan à l’aide un `IntentService` nommé `RegistrationIntentService`. Ce service seront également chargé de [l’actualisation notre jeton d’inscription GCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens).
 
    Ajoutez la définition de service suivante vers le fichier AndroidManifest.xml, à l’intérieur du `<application>` balise. Remplacer le `<your package>` espace réservé avec le nom de votre package réelle affichée en haut de la `AndroidManifest.xml` fichier. 

        <service
            android:name="<your package>.RegistrationIntentService"
            android:exported="false">
        </service>



3. Nous allons également définir un récepteur pour recevoir des notifications. Ajoutez la définition de récepteur suivante vers le fichier AndroidManifest.xml, à l’intérieur du `<application>` balise. Remplacer le `<your package>` espace réservé avec le nom de votre package réelle affichée en haut de la `AndroidManifest.xml` fichier.

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your package name>" />
            </intent-filter>
        </receiver>



4. Ajoutez le code suivant GCM nécessaire liées autorisations ci-dessous la `</application>` balise. Veillez à remplacer `<your package>` avec le nom du package apparaît en haut de la `AndroidManifest.xml` fichier.

    Pour plus d’informations sur ces autorisations, voir [configurer une application Client GCM pour Android](https://developers.google.com/cloud-messaging/android/client#manifest).

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
        <uses-permission android:name="android.permission.WAKE_LOCK"/>
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

        <permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
        <uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>


### <a name="adding-code"></a>Ajout de code


1. Dans l’affichage de projet, développez **application** > **src** > **principale** > **java**. Avec le bouton droit de votre dossier package sous **java**, cliquez sur **Nouveau**, puis cliquez sur **Classe Java**. Ajouter une nouvelle classe nommée `NotificationSettings`. 

    ![Studio Android - nouvelle classe Java][6]

    Veillez à mettre à jour ces trois espaces réservés dans le code suivant pour la `NotificationSettings` cours :
    * **ID de l’expéditeur**: le numéro de projet que vous avez obtenu précédemment dans la [Console de Cloud Google](http://cloud.google.com/console).
    * **HubListenConnectionString**: la chaîne de connexion **DefaultListenAccessSignature** pour votre plateforme. Vous pouvez copier cette chaîne de connexion en cliquant sur **Les stratégies d’accès** dans la cuillère **paramètres** du votre concentrateur sur le [Portail Azure].
    * **HubName**: utilisez le nom de votre concentrateur de notification qui s’affiche dans la carte concentrateur dans le [Portail Azure].

    `NotificationSettings`code :

        public class NotificationSettings {
            public static String SenderId = "<Your project number>";
            public static String HubName = "<Your HubName>";
            public static String HubListenConnectionString = "<Your default listen connection string>";
        }

2. La procédure décrite ci-dessus, ajoutez un autre nouvelle classe nommée `MyInstanceIDService`. Il s’agit de notre implémentation de service d’ID de l’Instance écoute.

    Le code de ce cours rejoindront notre `IntentService` [le jeton GCM l’actualisation](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) en arrière-plan.

        import android.content.Intent;
        import android.util.Log;
        import com.google.android.gms.iid.InstanceIDListenerService;
        
        
        public class MyInstanceIDService extends InstanceIDListenerService {
        
            private static final String TAG = "MyInstanceIDService";
        
            @Override
            public void onTokenRefresh() {
        
                Log.i(TAG, "Refreshing GCM Registration Token");
        
                Intent intent = new Intent(this, RegistrationIntentService.class);
                startService(intent);
            }
        };


3. Ajouter un autre nouveau cours à votre projet nommé, `RegistrationIntentService`. Il s’agit de l’implémentation de notre `IntentService` qui gérera [Actualiser le jeton GCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) et [enregistrement avec le hub de notification](notification-hubs-push-notification-registration-management.md).

    Utilisez le code suivant pour ce cours.

        import android.app.IntentService;
        import android.content.Intent;
        import android.content.SharedPreferences;
        import android.preference.PreferenceManager;
        import android.util.Log;
        
        import com.google.android.gms.gcm.GoogleCloudMessaging;
        import com.google.android.gms.iid.InstanceID;
        import com.microsoft.windowsazure.messaging.NotificationHub;
        
        public class RegistrationIntentService extends IntentService {
        
            private static final String TAG = "RegIntentService";
        
            private NotificationHub hub;
        
            public RegistrationIntentService() {
                super(TAG);
            }
        
            @Override
            protected void onHandleIntent(Intent intent) {      
                SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
                String resultString = null;
                String regID = null;
        
                try {
                    InstanceID instanceID = InstanceID.getInstance(this);
                    String token = instanceID.getToken(NotificationSettings.SenderId,
                            GoogleCloudMessaging.INSTANCE_ID_SCOPE);        
                    Log.i(TAG, "Got GCM Registration Token: " + token);
        
                    // Storing the registration id that indicates whether the generated token has been
                    // sent to your server. If it is not stored, send the token to your server,
                    // otherwise your server should have already received the token.
                    if ((regID=sharedPreferences.getString("registrationID", null)) == null) {      
                        NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                                NotificationSettings.HubListenConnectionString, this);
                        Log.i(TAG, "Attempting to register with NH using token : " + token);

                        regID = hub.register(token).getRegistrationId();

                        // If you want to use tags...
                        // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
                        // regID = hub.register(token, "tag1", "tag2").getRegistrationId();

                        resultString = "Registered Successfully - RegId : " + regID;
                        Log.i(TAG, resultString);       
                        sharedPreferences.edit().putString("registrationID", regID ).apply();
                    } else {
                        resultString = "Previously Registered Successfully - RegId : " + regID;
                    }
                } catch (Exception e) {
                    Log.e(TAG, resultString="Failed to complete token refresh", e);
                    // If an exception happens while fetching the new token or updating our registration data
                    // on a third-party server, this ensures that we'll attempt the update at a later time.
                }
        
                // Notify UI that registration has completed.
                if (MainActivity.isVisible) {
                    MainActivity.mainActivity.ToastNotify(resultString);
                }
            }
        }


        
4. Dans votre `MainActivity` de classes, ajoutez le code suivant `import` instructions au-dessus de la déclaration de classe.

        import com.google.android.gms.common.ConnectionResult;
        import com.google.android.gms.common.GoogleApiAvailability;
        import com.google.android.gms.gcm.*;
        import com.microsoft.windowsazure.notifications.NotificationsManager;
        import android.util.Log;
        import android.widget.TextView;
        import android.widget.Toast;

5. Ajoutez les membres privés suivants en haut de la classe. Nous allons utiliser ces [vérifier la disponibilité des Services Google Play recommandée par Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

        public static MainActivity mainActivity;
        public static Boolean isVisible = false;    
        private GoogleCloudMessaging gcm;
        private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;

6. Dans votre `MainActivity` de classes, ajoutez la méthode suivante à la disponibilité des Services Google Play. 

        /**
         * Check the device to make sure it has the Google Play Services APK. If
         * it doesn't, display a dialog that allows users to download the APK from
         * the Google Play Store or enable it in the device's system settings.
         */
        private boolean checkPlayServices() {
            GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
            int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
            if (resultCode != ConnectionResult.SUCCESS) {
                if (apiAvailability.isUserResolvableError(resultCode)) {
                    apiAvailability.getErrorDialog(this, resultCode, PLAY_SERVICES_RESOLUTION_REQUEST)
                            .show();
                } else {
                    Log.i(TAG, "This device is not supported by Google Play Services.");
                    ToastNotify("This device is not supported by Google Play Services.");
                    finish();
                }
                return false;
            }
            return true;
        }


7. Dans votre `MainActivity` de classes, ajoutez le code suivant vérifie les Services Google Play avant d’appeler votre `IntentService` pour obtenir votre inscription GCM jeton et inscrire avec votre concentrateur de notification.

        public void registerWithNotificationHubs()
        {
            Log.i(TAG, " Registering with Notification Hubs");
    
            if (checkPlayServices()) {
                // Start IntentService to register this application with GCM.
                Intent intent = new Intent(this, RegistrationIntentService.class);
                startService(intent);
            }
        }


8. Dans la `OnCreate` méthode de le `MainActivity` de classes, ajoutez le code suivant pour commencer le processus d’inscription lorsque l’activité est créée.

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mainActivity = this;
            NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
            registerWithNotificationHubs();
        }


9. Ajoutez ces méthodes supplémentaires à la `MainActivity` pour vérifier l’état de l’application état et un rapport dans votre application.

        @Override
        protected void onStart() {
            super.onStart();
            isVisible = true;
        }
    
        @Override
        protected void onPause() {
            super.onPause();
            isVisible = false;
        }
    
        @Override
        protected void onResume() {
            super.onResume();
            isVisible = true;
        }
    
        @Override
        protected void onStop() {
            super.onStop();
            isVisible = false;
        }
    
        public void ToastNotify(final String notificationMessage) {
            runOnUiThread(new Runnable() {
                @Override
                public void run() {
                    Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
                    TextView helloText = (TextView) findViewById(R.id.text_hello);
                    helloText.setText(notificationMessage);
                }
            });
        }


10. La `ToastNotify` méthode utilise le *« Hello World »* `TextView` contrôle pour signaler l’état et les notifications de façon permanente dans l’application. Dans votre disposition activity_main.xml, ajoutez le code suivant pour ce contrôle.

        android:id="@+id/text_hello"

11. Ensuite, nous allons ajouter une sous-classe pour notre récepteur que nous avons définie dans le AndroidManifest.xml. Ajouter un autre nouveau cours à votre projet nommé `MyHandler`.

12. Ajoutez les instructions d’importation suivantes dans la partie supérieure du `MyHandler.java`:

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;
        import com.microsoft.windowsazure.notifications.NotificationsHandler;

13. Ajoutez le code suivant pour la `MyHandler` classe rendant une sous-classe de `com.microsoft.windowsazure.notifications.NotificationsHandler`.

    Ce code substitue le `OnReceive` méthode, afin que le gestionnaire signalera notifications sont reçues. Le gestionnaire envoie également la notification push au responsable de notification Android à l’aide de la `sendNotification()` méthode. La `sendNotification()` méthode doit être exécutée lorsque l’application n’est pas en cours d’exécution et réception d’une notification.

        public class MyHandler extends NotificationsHandler {
            public static final int NOTIFICATION_ID = 1;
            private NotificationManager mNotificationManager;
            NotificationCompat.Builder builder;
            Context ctx;
        
            @Override
            public void onReceive(Context context, Bundle bundle) {
                ctx = context;
                String nhMessage = bundle.getString("message");
                sendNotification(nhMessage);
                if (MainActivity.isVisible) {
                    MainActivity.mainActivity.ToastNotify(nhMessage);
                }
            }
        
            private void sendNotification(String msg) {
        
                Intent intent = new Intent(ctx, MainActivity.class);
                intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
        
                mNotificationManager = (NotificationManager)
                        ctx.getSystemService(Context.NOTIFICATION_SERVICE);
        
                PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                        intent, PendingIntent.FLAG_ONE_SHOT);
        
                Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
                NotificationCompat.Builder mBuilder =
                        new NotificationCompat.Builder(ctx)
                                .setSmallIcon(R.mipmap.ic_launcher)
                                .setContentTitle("Notification Hub Demo")
                                .setStyle(new NotificationCompat.BigTextStyle()
                                        .bigText(msg))
                                .setSound(defaultSoundUri)
                                .setContentText(msg);
        
                mBuilder.setContentIntent(contentIntent);
                mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
            }
        }


14. Dans Studio Android dans la barre de menus, cliquez sur **Générer** > **Reconstruire le projet** pour vous assurer qu’aucune erreur n’est présents dans votre code.

##<a name="sending-push-notifications"></a>Envoi des notifications push

Vous pouvez tester reçoit les notifications push dans votre application en leur envoyant via le [Portail Azure] - recherchez la Section **résolution des problèmes** dans la carte concentrateur, comme illustré ci-dessous.

![Envoyer une Notification Azure Hubs - Test](./media/notification-hubs-android-get-started/notification-hubs-test-send.png)

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## <a name="optional-send-push-notifications-directly-from-the-app"></a>(Facultatif) Envoyer les notifications push directement à partir de l’application

En règle générale, vous le feriez envoyer des notifications à l’aide d’un serveur principal. Dans certains cas, vous souhaiterez peut-être en mesure d’envoyer les notifications push directement à partir de l’application cliente. Cette section explique comment envoyer des notifications à partir du client à l’aide de l' [API REST de Azure Notification concentrateur](https://msdn.microsoft.com/library/azure/dn223264.aspx).

1. Dans la vue de projet Android Studio, développez **application** > **src** > **principale** > **res** > **mise en page**. Ouvrir le `activity_main.xml` fichier mise en page et cliquez sur le **texte** de l’onglet pour mettre à jour le contenu de texte du fichier. Mettre à jour avec le code ci-dessous, qui ajoute de nouveaux `Button` et `EditText` contrôles pour envoyer des messages de notification push pour le hub de notification. Ajoutez ce code dans la partie inférieure, juste avant `</RelativeLayout>`.

        <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/send_button"
        android:id="@+id/sendbutton"
        android:layout_centerVertical="true"
        android:layout_centerHorizontal="true"
        android:onClick="sendNotificationButtonOnClick" />

        <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editTextNotificationMessage"
        android:layout_above="@+id/sendbutton"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="42dp"
        android:hint="@string/notification_message_hint" />

2. Dans la vue de projet Android Studio, développez **application** > **src** > **principale** > **res** > **valeurs**. Ouvrir le `strings.xml` et ajoutez les valeurs de chaîne qui sont référencées par la nouvelle `Button` et `EditText` contrôles. Ajoutez ces en bas du fichier, juste avant `</resources>`.

        <string name="send_button">Send Notification</string>
        <string name="notification_message_hint">Enter notification message text</string>


3. Dans votre `NotificationSetting.java` , ajoutez le paramètre suivant sur la `NotificationSettings` cours.

    Mise à jour `HubFullAccess` avec la chaîne de connexion **DefaultFullSharedAccessSignature** pour votre plateforme. Cette chaîne de connexion peut être copiée à partir du [Portail Azure] en cliquant sur **Les stratégies d’accès** dans la carte de **paramètres** pour votre concentrateur de notification.

        public static String HubFullAccess = "<Enter Your DefaultFullSharedAccess Connection string>";

4. Dans votre `MainActivity.java` de fichiers, ajoutez le code suivant `import` instructions ci-dessus la `MainActivity` cours.

        import java.io.BufferedOutputStream;
        import java.io.BufferedReader;
        import java.io.InputStreamReader;
        import java.io.OutputStream;
        import java.net.HttpURLConnection;
        import java.net.URL;
        import java.net.URLEncoder;
        import javax.crypto.Mac;
        import javax.crypto.spec.SecretKeySpec;
        import android.util.Base64;
        import android.view.View;
        import android.widget.EditText;

6. Dans votre `MainActivity.java` de fichiers, ajoutez les membres suivants dans la partie supérieure de la `MainActivity` cours.  

        private String HubEndpoint = null;
        private String HubSasKeyName = null;
        private String HubSasKeyValue = null;

6. Vous devez créer un jeton de logiciel Access Signature (sa) pour authentifier une demande de publication pour envoyer des messages à votre concentrateur de notification. Pour cela, l’analyse des données clées à partir de la chaîne de connexion, puis en créant le jeton associations de sécurité, comme indiqué dans la référence de l’API REST [Concepts communs](http://msdn.microsoft.com/library/azure/dn495627.aspx) . Le code suivant est un exemple d’implémentation.

    Dans `MainActivity.java`, ajoutez la méthode suivante à la `MainActivity` cours pour analyser la chaîne de connexion.

        /**
         * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx
         * to parse the connection string so a SaS authentication token can be
         * constructed.
         *
         * @param connectionString This must be the DefaultFullSharedAccess connection
         *                         string for this example.
         */
        private void ParseConnectionString(String connectionString)
        {
            String[] parts = connectionString.split(";");
            if (parts.length != 3)
                throw new RuntimeException("Error parsing connection string: "
                        + connectionString);
    
            for (int i = 0; i < parts.length; i++) {
                if (parts[i].startsWith("Endpoint")) {
                    this.HubEndpoint = "https" + parts[i].substring(11);
                } else if (parts[i].startsWith("SharedAccessKeyName")) {
                    this.HubSasKeyName = parts[i].substring(20);
                } else if (parts[i].startsWith("SharedAccessKey")) {
                    this.HubSasKeyValue = parts[i].substring(16);
                }
            }
        }


7. Dans `MainActivity.java`, ajoutez la méthode suivante à la `MainActivity` classe pour créer un jeton d’authentification associations de sécurité.

        /**
         * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx to
         * construct a SaS token from the access key to authenticate a request.
         *
         * @param uri The unencoded resource URI string for this operation. The resource
         *            URI is the full URI of the Service Bus resource to which access is
         *            claimed. For example,
         *            "http://<namespace>.servicebus.windows.net/<hubName>"
         */
        private String generateSasToken(String uri) {
    
            String targetUri;
            String token = null;
            try {
                targetUri = URLEncoder
                        .encode(uri.toString().toLowerCase(), "UTF-8")
                        .toLowerCase();
    
                long expiresOnDate = System.currentTimeMillis();
                int expiresInMins = 60; // 1 hour
                expiresOnDate += expiresInMins * 60 * 1000;
                long expires = expiresOnDate / 1000;
                String toSign = targetUri + "\n" + expires;
    
                // Get an hmac_sha1 key from the raw key bytes
                byte[] keyBytes = HubSasKeyValue.getBytes("UTF-8");
                SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");
    
                // Get an hmac_sha1 Mac instance and initialize with the signing key
                Mac mac = Mac.getInstance("HmacSHA256");
                mac.init(signingKey);
    
                // Compute the hmac on input data bytes
                byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));
    
                // Using android.util.Base64 for Android Studio instead of
                // Apache commons codec
                String signature = URLEncoder.encode(
                        Base64.encodeToString(rawHmac, Base64.NO_WRAP).toString(), "UTF-8");
    
                // Construct authorization string
                token = "SharedAccessSignature sr=" + targetUri + "&sig="
                        + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
            } catch (Exception e) {
                if (isVisible) {
                    ToastNotify("Exception Generating SaS : " + e.getMessage().toString());
                }
            }
    
            return token;
        }



8. Dans `MainActivity.java`, ajoutez la méthode suivante à la `MainActivity` classe pour gérer le clic sur le bouton **Envoyer une Notification** et envoyer le message de notification push au concentrateur à l’aide de l’API REST intégrés.

        /**
         * Send Notification button click handler. This method parses the
         * DefaultFullSharedAccess connection string and generates a SaS token. The
         * token is added to the Authorization header on the POST request to the
         * notification hub. The text in the editTextNotificationMessage control
         * is added as the JSON body for the request to add a GCM message to the hub.
         *
         * @param v
         */
        public void sendNotificationButtonOnClick(View v) {
            EditText notificationText = (EditText) findViewById(R.id.editTextNotificationMessage);
            final String json = "{\"data\":{\"message\":\"" + notificationText.getText().toString() + "\"}}";
    
            new Thread()
            {
                public void run()
                {
                    try
                    {
                        // Based on reference documentation...
                        // http://msdn.microsoft.com/library/azure/dn223273.aspx
                        ParseConnectionString(NotificationSettings.HubFullAccess);
                        URL url = new URL(HubEndpoint + NotificationSettings.HubName +
                                "/messages/?api-version=2015-01");
    
                        HttpURLConnection urlConnection = (HttpURLConnection)url.openConnection();
    
                        try {
                            // POST request
                            urlConnection.setDoOutput(true);
    
                            // Authenticate the POST request with the SaS token
                            urlConnection.setRequestProperty("Authorization", 
                                generateSasToken(url.toString()));
    
                            // Notification format should be GCM
                            urlConnection.setRequestProperty("ServiceBusNotification-Format", "gcm");
    
                            // Include any tags
                            // Example below targets 3 specific tags
                            // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
                            // urlConnection.setRequestProperty("ServiceBusNotification-Tags", 
                            //      "tag1 || tag2 || tag3");
    
                            // Send notification message
                            urlConnection.setFixedLengthStreamingMode(json.length());
                            OutputStream bodyStream = new BufferedOutputStream(urlConnection.getOutputStream());
                            bodyStream.write(json.getBytes());
                            bodyStream.close();
    
                            // Get reponse
                            urlConnection.connect();
                            int responseCode = urlConnection.getResponseCode();
                            if ((responseCode != 200) && (responseCode != 201)) {
                                BufferedReader br = new BufferedReader(new InputStreamReader((urlConnection.getErrorStream())));
                                String line;
                                StringBuilder builder = new StringBuilder("Send Notification returned " +
                                        responseCode + " : ")  ;
                                while ((line = br.readLine()) != null) {
                                    builder.append(line);
                                }

                                ToastNotify(builder.toString());
                            }
                        } finally {
                            urlConnection.disconnect();
                        }
                    }
                    catch(Exception e)
                    {
                        if (isVisible) {
                            ToastNotify("Exception Sending Notification : " + e.getMessage().toString());
                        }
                    }
                }
            }.start();
        }



##<a name="testing-your-app"></a>Test de votre application

####<a name="push-notifications-in-the-emulator"></a>Notifications de transmission dans l’émulateur

Si vous souhaitez tester les notifications push à l’intérieur d’un émulateur, assurez-vous que votre image émulateur prend en charge le niveau de l’API Google que vous avez choisi pour votre application. Si votre image ne prend pas en charge native APIs de Google, vous obtiendrez la **SERVICE\_pas\_disponible** exception.

En outre, assurez-vous que vous avez ajouté votre compte Google vers votre émulateur en cours d’exécution sous **paramètres** > **comptes**. Dans le cas contraire, votre tentative d’enregistrement avec GCM peut entraîner le **authentification\_échec** exception.

####<a name="running-the-application"></a>Exécution de l’application

1. Exécuter l’application et notez que l’ID de l’enregistrement est signalée pour un enregistrement est effectué.

    ![Test sur Android - inscription de canal][18]

2. Entrez un message de notification à envoyer à tous les appareils Android qui ont enregistrés avec le hub.

    ![Test sur Android - envoyer un message][19]

3. Appuyez sur **Envoyer une Notification**. Affichent les périphériques dotées de l’application en cours d’exécution un `AlertDialog` instance avec le message de notification push. Appareils qui n’ont pas l’application en cours d’exécution mais précédemment inscrits pour les notifications push recevront une notification dans le Gestionnaire de Notification Android. Ceux peuvent être affichés en balayant vers le bas à partir du coin supérieur gauche.

    ![Test sur Android - notifications][21]

##<a name="next-steps"></a>Étapes suivantes

Nous vous recommandons le didacticiel [Utilisation Notification Hubs aux notifications push pour les utilisateurs] en tant que l’étape suivante. Il vous montrent comment envoyer des notifications à partir d’un serveur principal ASP.NET utilisant des balises pour cibler des utilisateurs spécifiques.

Si vous voulez segmenter vos utilisateurs en groupes d’intérêt, consultez le didacticiel [Hubs de Notification utiliser pour envoyer les informations de dernière minute] .

Pour en savoir plus d’informations générales sur la Notification Hubs, consultez notre [Guide Hubs de Notification].

<!-- Images. -->
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png

[12]: ./media/notification-hubs-android-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-new-project.png
[14]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-choose-form-factor.png
[15]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png
[16]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app5.png
[17]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app6.png

[18]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-registered.png
[19]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-set-message.png

[20]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-received-message.png
[22]: ./media/notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/notification-hubs-android-get-started/notification-hub-scheduler2.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png

[30]: ./media/notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png

[31]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-add-ui.png


<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Azure Classic Portal]: https://manage.windowsazure.com/
[Conseils de Hubs de notification]: http://msdn.microsoft.com/library/jj927170.aspx
[Utiliser des Hubs de Notification pour les notifications push pour les utilisateurs]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[Notification Hubs permet d’envoyer des informations de dernière minute]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[Portail Azure]: https://portal.azure.com
