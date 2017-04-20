<properties
    pageTitle="Prise en main avec Hubs de Notification Azure pour les applications Kindle | Microsoft Azure"
    description="Dans ce didacticiel, vous allez apprendre à utiliser Azure Notification Hubs pour envoyer les notifications push pour une application Kindle."
    services="notification-hubs"
    documentationCenter=""
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-kindle"
    ms.devlang="Java"
    ms.topic="hero-article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="get-started-with-notification-hubs-for-kindle-apps"></a>Prise en main avec Hubs de Notification pour les applications Kindle

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Vue d’ensemble

Ce didacticiel montre comment Azure Notification Hubs permet d’envoyer les notifications push pour une application Kindle.
Vous devez créer une application vierge Kindle qui reçoit les notifications push à l’aide d’Amazon appareil messagerie responsable.

##<a name="prerequisites"></a>Conditions préalables

Ce didacticiel requiert les éléments suivants :

+ Obtenir le Kit de développement Android (nous part du principe que vous utiliserez Eclipse) à partir du <a href="http://go.microsoft.com/fwlink/?LinkId=389797">site Android</a>.
+ Suivez les étapes <a href="https://developer.amazon.com/appsandservices/resources/development-tools/ide-tools/tech-docs/01-setting-up-your-development-environment">Configuration votre environnement de développement</a> pour configurer votre environnement de développement pour Kindle.

##<a name="add-a-new-app-to-the-developer-portal"></a>Ajouter une nouvelle application sur le portail du développeur

1. Commencez par créer une application dans le [portail pour les développeurs Amazon].

    ![][0]

2. Copiez la **clé de l’Application**.

    ![][1]

3. Dans le portail, cliquez sur le nom de votre application, puis cliquez sur l’onglet **Messagerie de l’appareil** .

    ![][2]

4. Cliquez sur **créer un nouveau profil de sécurité**, puis créez un nouveau profil de sécurité (par exemple, le **profil de sécurité TestAdm**). Cliquez sur **Enregistrer**.

    ![][3]

5. Cliquez sur **Profils de sécurité** pour afficher le profil de sécurité que vous venez de créer. Copiez les valeurs **d’ID de Client** et le **Code Secret Client** pour une utilisation ultérieure.

    ![][4]

## <a name="create-an-api-key"></a>Créer une clé de l’API

1. Ouvrez une invite de commandes en tant qu’administrateur.
2. Accédez au dossier SDK Android.
3. Entrez la commande suivante :

        keytool -list -v -alias androiddebugkey -keystore ./debug.keystore

    ![][5]

4.  Tapez le mot de passe de la **combinaison de touches** **android**.

5.  Copiez l’empreinte **MD5** .
6.  Dans le portail développeur, sous l’onglet **messagerie** , cliquez sur **Android/Kindle** et entrez le nom du package de votre application (par exemple, **com.sample.notificationhubtest**) et la valeur **MD5** , puis cliquez sur **Générer une clé de l’API**.

## <a name="add-credentials-to-the-hub"></a>Ajouter des informations d’identification au concentrateur

Dans le portail, ajoutez le code secret client et l’ID de client à l’onglet **configuration** de votre concentrateur de notification.

## <a name="set-up-your-application"></a>Configurer votre application

> [AZURE.NOTE] Lorsque vous créez une application, utilisez au moins l’API niveau 17.

Ajouter les bibliothèques ADM à votre projet Eclipse :

1. Pour obtenir la bibliothèque ADM, [Téléchargez le Kit de développement]. Extraire le fichier zip SDK.
2. Dans Eclipse, avec le bouton droit de votre projet, puis cliquez sur **Propriétés**. Sélectionnez **Le chemin d’accès de Java créer** sur la gauche, puis l’onglet **bibliothèques **dans la partie supérieure. Cliquez sur **Ajouter un Jar externe**, puis sélectionnez le fichier `\SDK\Android\DeviceMessaging\lib\amazon-device-messaging-*.jar` à partir du répertoire dans lequel vous avez extrait le Kit de développement Amazon.
3. Téléchargez le Kit de développement NotificationHubs Android (lien).
4. Décompressez le package, puis faites glisser le fichier `notification-hubs-sdk.jar` dans les `libs` dossier dans Eclipse.

Modifier votre manifeste d’application pour prendre en charge ADM :

1. Ajoutez l’espace de noms Amazon dans l’élément manifeste racine :


        xmlns:amazon="http://schemas.amazon.com/apk/res/android"

2. Ajouter des autorisations comme le premier élément sous l’élément manifeste. Remplacez **[Nom du PACKAGE votre]** avec le module qui vous permet de créer votre application.

        <permission
         android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE"
         android:protectionLevel="signature" />

        <uses-permission android:name="android.permission.INTERNET"/>

        <uses-permission android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE" />

        <!-- This permission allows your app access to receive push notifications
        from ADM. -->
        <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE" />

        <!-- ADM uses WAKE_LOCK to keep the processor from sleeping when a message is received. -->
        <uses-permission android:name="android.permission.WAKE_LOCK" />

3. Insérer l’élément suivant comme premier enfant de l’élément d’application. N’oubliez pas de remplacer **[Nom de votre SERVICE]** par le nom de votre gestionnaire de messages ADM que vous créez dans la section suivante (y compris le package) et remplacez **[Nom du PACKAGE votre]** par le nom du package avec lequel vous avez créé votre application.

        <amazon:enable-feature
              android:name="com.amazon.device.messaging"
                     android:required="true"/>
        <service
            android:name="[YOUR SERVICE NAME]"
            android:exported="false" />

        <receiver
            android:name="[YOUR SERVICE NAME]$Receiver" />

            <!-- This permission ensures that only ADM can send your app registration broadcasts. -->
            android:permission="com.amazon.device.messaging.permission.SEND" >

            <!-- To interact with ADM, your app must listen for the following intents. -->
            <intent-filter>
          <action android:name="com.amazon.device.messaging.intent.REGISTRATION" />
          <action android:name="com.amazon.device.messaging.intent.RECEIVE" />

          <!-- Replace the name in the category tag with your app's package name. -->
          <category android:name="[YOUR PACKAGE NAME]" />
            </intent-filter>
        </receiver>

## <a name="create-your-adm-message-handler"></a>Créer votre gestionnaire de messages ADM

1. Créer une nouvelle classe héritant de `com.amazon.device.messaging.ADMMessageHandlerBase` et nommez-la `MyADMMessageHandler`, comme indiqué dans l’illustration suivante :

    ![][6]

2. Ajoutez le code suivant `import` instructions :

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.support.v4.app.NotificationCompat;
        import com.amazon.device.messaging.ADMMessageReceiver;
        import com.microsoft.windowsazure.messaging.NotificationHub

3. Ajoutez le code suivant dans la classe que vous avez créé. N’oubliez pas de remplacer par la chaîne de connexion et le nom des concentrateur (écoute) :

        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        private static NotificationHub hub;
        public static NotificationHub getNotificationHub(Context context) {
            Log.v("com.wa.hellokindlefire", "getNotificationHub");
            if (hub == null) {
                hub = new NotificationHub("[hub name]", "[listen connection string]", context);
            }
            return hub;
        }

        public MyADMMessageHandler() {
                super("MyADMMessageHandler");
            }

            public static class Receiver extends ADMMessageReceiver
            {
                public Receiver()
                {
                    super(MyADMMessageHandler.class);
                }
            }

            private void sendNotification(String msg) {
                Context ctx = getApplicationContext();

             mNotificationManager = (NotificationManager)
                    ctx.getSystemService(Context.NOTIFICATION_SERVICE);

            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                new Intent(ctx, MainActivity.class), 0);

            NotificationCompat.Builder mBuilder =
                new NotificationCompat.Builder(ctx)
                .setSmallIcon(R.mipmap.ic_launcher)
                .setContentTitle("Notification Hub Demo")
                .setStyle(new NotificationCompat.BigTextStyle()
                         .bigText(msg))
                .setContentText(msg);

            mBuilder.setContentIntent(contentIntent);
            mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
        }

4. Ajoutez le code suivant à la `OnMessage()` méthode :

        String nhMessage = intent.getExtras().getString("msg");
        sendNotification(nhMessage);

5. Ajoutez le code suivant à la `OnRegistered` méthode :

            try {
        getNotificationHub(getApplicationContext()).register(registrationId);
            } catch (Exception e) {
        Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
            }

6.  Ajoutez le code suivant à la `OnUnregistered` méthode :

            try {
                getNotificationHub(getApplicationContext()).unregister();
            } catch (Exception e) {
                Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
            }

7. Dans la `MainActivity` méthode, ajoutez l’instruction import suivante :

        import com.amazon.device.messaging.ADM;

8. Ajoutez le code suivant à la fin de la `OnCreate` méthode :

        final ADM adm = new ADM(this);
        if (adm.getRegistrationId() == null)
        {
           adm.startRegister();
        } else {
            new AsyncTask() {
                  @Override
                  protected Object doInBackground(Object... params) {
                     try {                       MyADMMessageHandler.getNotificationHub(getApplicationContext()).register(adm.getRegistrationId());
                     } catch (Exception e) {
                         Log.e("com.wa.hellokindlefire", "Failed registration with hub", e);
                         return e;
                     }
                     return null;
                 }
               }.execute(null, null, null);
        }

## <a name="add-your-api-key-to-your-app"></a>Ajouter votre clé de l’API dans votre application

1. Dans Eclipse, créez un nouveau fichier nommé **api_key.txt** dans les biens de l’annuaire de votre projet.
2. Ouvrez le fichier et copiez la clé d’API que vous avez généré dans le portail de développement Amazon.

## <a name="run-the-app"></a>Exécuter l’application

1. Démarrer l’émulateur.
2. Dans l’émulateur, balayez à partir du haut et cliquez sur **paramètres**, puis cliquez sur **Mon compte** et enregistrer avec un compte d’Amazon valide.
3. Dans Eclipse, exécutez l’application.

> [AZURE.NOTE] En cas de problème, vérifiez l’heure de l’émulateur (ou l’appareil). La valeur de temps doit être exacte. Pour modifier l’heure de l’émulateur Kindle, vous pouvez exécuter la commande suivante à partir de votre répertoire des outils de la plateforme SDK Android :

        adb shell  date -s "yyyymmdd.hhmmss"

## <a name="send-a-message"></a>Envoyer un message

Pour envoyer un message à l’aide de .NET :

        static void Main(string[] args)
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("[conn string]", "[hub name]");

            hub.SendAdmNativeNotificationAsync("{\"data\":{\"msg\" : \"Hello from .NET!\"}}").Wait();
        }

![][7]

<!-- URLs. -->
[Portail pour les développeurs Amazon]: https://developer.amazon.com/home.html
[Téléchargez le Kit de développement]: https://developer.amazon.com/public/resources/development-tools/sdk

[0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png
[1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png
[2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png
[3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png
[4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png
[5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png
[6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png
[7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png
