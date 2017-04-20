<properties
    pageTitle="Prise en main avec Hubs de Notification Azure à l’aide de Baidu | Microsoft Azure"
    description="Dans ce didacticiel, vous allez apprendre à utiliser Azure Notification Hubs aux notifications push pour appareils Android à l’aide de Baidu."
    services="notification-hubs"
    documentationCenter="android"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.tgt_pltfrm="mobile-baidu"
    ms.workload="mobile"
    ms.date="08/19/2016"
    ms.author="yuaxu"/>

# <a name="get-started-with-notification-hubs-using-baidu"></a>Prise en main avec Hubs de Notification à l’aide de Baidu

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Vue d’ensemble

Baidu cloud push est un service cloud chinois que vous pouvez utiliser pour envoyer les notifications push pour les appareils mobiles. Ce service est particulièrement utile en Chine, où livrer les notifications push pour Android est complexe, en raison de la présence d’application différents magasins et services push, en plus de la disponibilité des appareils Android ne sont pas généralement connectés à GCM (Google Cloud messagerie).

##<a name="prerequisites"></a>Conditions préalables

Ce didacticiel requiert les éléments suivants :

+ Android Kit de développement logiciel (nous part du principe que vous utiliserez Eclipse), que vous pouvez télécharger à partir du <a href="http://go.microsoft.com/fwlink/?LinkId=389797">site Android</a>
+ [Kit de développement logiciel Services Mobile Android]
+ [Kit de développement logiciel Baidu Push Android]

>[AZURE.NOTE] Pour effectuer ce didacticiel, vous devez disposer d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-baidu-get-started%2F).


##<a name="create-a-baidu-account"></a>Créer un compte Baidu

Pour utiliser Baidu s’affiche, vous devez disposer d’un compte Baidu. Si vous avez déjà un, connectez-vous au [portail Baidu] et passez à l’étape suivante. Dans le cas contraire, reportez-vous aux instructions ci-dessous pour créer un compte Baidu.  

1. Accédez au [portail Baidu] et cliquez sur le lien**登录**(**connexion**). Cliquez sur**立即注册**pour démarrer le processus d’enregistrement compte.

    ![][1]

2. Entrez les informations requises — code adresse, mot de passe et vérification du téléphone/messagerie, puis cliquez sur **l’inscription**.

    ![][2]

3. Vous recevrez un message électronique à l’adresse de messagerie que vous avez entré avec un lien pour activer votre compte Baidu.

    ![][3]

4. Connectez-vous à votre compte de messagerie, ouvrez le courrier de l’activation Baidu s’affiche, puis cliquez sur le lien d’activation pour activer votre compte Baidu.

    ![][4]

Une fois que vous avez un compte Baidu activé, connectez-vous au [portail Baidu].

##<a name="register-as-a-baidu-developer"></a>Enregistrer en tant qu’un développeur Baidu

1. Une fois que vous êtes connecté au [portail Baidu s’affiche], cliquez sur**更多 >>** (**plus**).

    ![][5]

2. Faites défiler vers le bas dans la section**站长与开发者服务 (administrateur Web et Services pour les développeurs)** , puis cliquez sur**百度开放云平台**(**Baidu ouvrir plateforme cloud**).

    ![][6]

3. Dans la page suivante, cliquez sur**开发者服务**(**Développeur Services**) dans le coin supérieur droit.

    ![][7]

4. Dans la page suivante, cliquez sur**注册开发者**(**Développeurs enregistré**) dans le menu dans le coin supérieur droit.

    ![][8]

5. Entrez votre nom, une description et un numéro de téléphone mobile pour un message SMS vérification, puis cliquez sur**送验证码**(**Envoyer un Code de vérification**). Notez que des numéros de téléphone international, vous devez placer le code du pays entre parenthèses. Par exemple, pour un nombre aux États-Unis, il sera **(1) 1234567890**.

    ![][9]

6. Vous devez puis recevez un message texte avec un numéro de vérification, comme le montre l’exemple suivant :

    ![][10]

7. Entrez le numéro de vérification à partir du message dans**验证码**(**code de Confirmation**).

8. Enfin, terminez l’enregistrement développeur en acceptant ce contrat Baidu en cliquant sur**提交**(**Envoyer**). Vous verrez la page suivante de réussite de l’enregistrement :

    ![][11]

##<a name="create-a-baidu-cloud-push-project"></a>Créer un projet Baidu cloud push

Lorsque vous créez un projet Baidu cloud push, vous recevez votre ID d’application, clé d’API et clé secrète.

1. Une fois que vous êtes connecté au [portail Baidu s’affiche], cliquez sur**更多 >>** (**plus**).

    ![][5]

2. Faites défiler vers le bas dans la section**站长与开发者服务**(**administrateur Web et Services pour les développeurs**), puis cliquez sur**百度开放云平台**(**Baidu ouvrir plateforme cloud**).

    ![][6]

3. Dans la page suivante, cliquez sur**开发者服务**(**Développeur Services**) dans le coin supérieur droit.

    ![][7]

4. Dans la page suivante, cliquez sur**云推送**(**Cloud Push**) à partir de la section**云服务**(**Cloud Services**).

    ![][12]

5. Une fois que vous êtes un développeur enregistré, vous verrez**管理控制台**(**Console de gestion**) au niveau du menu supérieur. Cliquez sur**开发者服务管理**(**Gestion des services développeurs**).

    ![][13]

6. Dans la page suivante, cliquez sur**创建工程**(**Créer un projet**).

    ![][14]

7. Entrez un nom d’application, puis cliquez sur**创建**(**créer**).

    ![][15]

8. Lors de la création de réussite d’un projet Baidu cloud push, vous verrez une page avec **identificateur** **Clé d’API**et **Clé secrète**. Prenez note de la clé d’API et la clé secrète, que nous utiliserons ultérieurement.

    ![][16]

9. Configurer le projet pour les notifications push en cliquant sur**云推送**(**Cloud Push**) dans le volet gauche.

    ![][31]

10. Dans la page suivante, cliquez sur le bouton**推送设置**(**paramètres de distribution**).

    ![][32]  

11. Dans la page de configuration, ajoutez le nom du package que vous utiliserez dans votre projet Android dans le champ**应用包名**(**package d’Application**) et puis cliquez sur**保存设置**(**Enregistrer**).  

    ![][33]

Vous verrez le**保存成功 !** (**correctement enregistré !**) message.

##<a name="configure-your-notification-hub"></a>Configurer votre concentrateur de notification

1. Connectez-vous au [Portail classique Azure], puis cliquez sur **+ Nouveau** dans la partie inférieure de l’écran.

2. Cliquez sur **Services d’application**et cliquez sur **Bus des services**, cliquez sur **Concentrateur de Notification**, puis cliquez sur **Création rapide**.

3. Indiquez un nom pour votre **Concentrateur de Notification**, sélectionnez la **région** et la **Namespace** où ce concentrateur de notification est créé, puis cliquez sur **créer un nouveau concentrateur de Notification**.  

    ![][17]

4. Cliquez sur l’espace de noms dans lequel vous avez créé votre concentrateur de notification, puis cliquez sur **Hubs de Notification** dans la partie supérieure.

    ![][18]

5. Sélectionnez le hub de notification que vous avez créé, puis cliquez sur **configurer** dans le menu supérieur.

    ![][19]

6. Faites défiler jusqu'à la section **paramètres de notification baidu** et entrez la clé d’API et clé secrète que vous avez obtenu à partir de la console Baidu précédemment pour votre projet Baidu cloud push. Cliquez sur **Enregistrer**.

    ![][20]

7. Cliquez sur l’onglet **tableau de bord** dans la partie supérieure pour le hub de notification, puis cliquez sur **Mode connexion chaîne**.

    ![][21]

8. Prenez note des **DefaultListenSharedAccessSignature** et **DefaultFullSharedAccessSignature** à partir de la fenêtre **accéder aux informations de connexion** .

    ![][22]

##<a name="connect-your-app-to-the-notification-hub"></a>Se connecter votre application au hub de notification

1. Dans ADT Eclipse, créez un nouveau projet Android (**fichier** > **Nouveau** > **Android projet d’Application**).

    ![][23]

2. Entrez un **Nom de l’Application** et vérifiez que la version **Minimale requise SDK** est définie sur **API 16 : Android 4.1**.

    ![][24]

3. Cliquez sur **suivant** et passez la suite de l’Assistant jusqu'à ce que la fenêtre **Créer une activité** s’affiche. Assurez-vous que **l’Activité vide** est sélectionnée et enfin sélectionnez **Terminer** pour créer une nouvelle Application Android.

    ![][25]

4. Assurez-vous que la **Cible Build projet** est correctement définie.

    ![][26]

5. Téléchargez le fichier notification-hubs-0.4.jar à partir de l’onglet **fichiers** de la [Notification-Hubs-Android-SDK sur Bintray](https://bintray.com/microsoftazuremobile/SDK/Notification-Hubs-Android-SDK/0.4). Ajoutez le fichier dans le dossier **et les bibliothèques** de votre projet Eclipse et actualisez le dossier *et les bibliothèques* .

6. Télécharger et décompresser le [Baidu transmission Android SDK], ouvrez le dossier **et les bibliothèques** et puis copiez le fichier jar **pushservice x.y.z** et **armeabi** & **mips** dossiers dans le dossier **et les bibliothèques** de votre application Android.

7. Ouvrez le fichier **AndroidManifest.xml** de votre projet Android et ajouter les autorisations requises par le Baidu SDK.

        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.READ_PHONE_STATE" />
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.WRITE_SETTINGS" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
        <uses-permission android:name="android.permission.DISABLE_KEYGUARD" />
        <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
        <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
        <uses-permission android:name="android.permission.ACCESS_DOWNLOAD_MANAGER" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION" />

8. Ajoutez la propriété **android : nom** à votre élément **d’application** dans **AndroidManifest.xml**, remplaçant *yourprojectname* (par exemple, **com.example.BaiduTest**). Assurez-vous que ce nom de projet correspond à celui que vous avez configuré dans la console Baidu.

        <application android:name="yourprojectname.DemoApplication"

9. Ajouter la configuration suivante dans l’élément application après la **. MainActivity** élément activité, remplaçant *yourprojectname* (par exemple, **com.example.BaiduTest**) :

        <receiver android:name="yourprojectname.MyPushMessageReceiver">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.MESSAGE" />
                <action android:name="com.baidu.android.pushservice.action.RECEIVE" />
                <action android:name="com.baidu.android.pushservice.action.notification.CLICK" />
            </intent-filter>
        </receiver>

        <receiver android:name="com.baidu.android.pushservice.PushServiceReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED" />
                <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
                <action android:name="com.baidu.android.pushservice.action.notification.SHOW" />
            </intent-filter>
        </receiver>

        <receiver android:name="com.baidu.android.pushservice.RegistrationReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.METHOD" />
                <action android:name="com.baidu.android.pushservice.action.BIND_SYNC" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.PACKAGE_REMOVED"/>
                <data android:scheme="package" />
            </intent-filter>
        </receiver>

        <service
            android:name="com.baidu.android.pushservice.PushService"
            android:exported="true"
            android:process=":bdservice_v1"  >
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.PUSH_SERVICE" />
            </intent-filter>
        </service>

9. Ajouter une nouvelle classe nommée **ConfigurationSettings.java** au projet.

    ![][28]

    ![][29]

10. Ajoutez le code suivant à celui-ci :

        public class ConfigurationSettings {
                public static String API_KEY = "...";
                public static String NotificationHubName = "...";
                public static String NotificationHubConnectionString = "...";
            }

    Définissez la valeur de **API_KEY** avec vous récupérées à partir du Baidu cloud projet plus tôt, **NotificationHubName** avec votre nom de concentrateur de notification à partir du portail classique Azure et **NotificationHubConnectionString** avec DefaultListenSharedAccessSignature à partir du portail classique Azure.

11. Ajouter une nouvelle classe appelée **DemoApplication.java**et ajoutez le code suivant à celui-ci :

        import com.baidu.frontia.FrontiaApplication;

        public class DemoApplication extends FrontiaApplication {
            @Override
            public void onCreate() {
                super.onCreate();
            }
        }

12. Ajouter un autre nouvelle classe appelée **MyPushMessageReceiver.java**et ajoutez le code ci-dessous à celui-ci. Il s’agit de la classe qui gère les notifications push reçus à partir du serveur push Baidu.

        import java.util.List;
        import android.content.Context;
        import android.os.AsyncTask;
        import android.util.Log;
        import com.baidu.frontia.api.FrontiaPushMessageReceiver;
        import com.microsoft.windowsazure.messaging.NotificationHub;

        public class MyPushMessageReceiver extends FrontiaPushMessageReceiver {
            /** TAG to Log */
            public static NotificationHub hub = null;
            public static String mChannelId, mUserId;
            public static final String TAG = MyPushMessageReceiver.class
                    .getSimpleName();

            @Override
            public void onBind(Context context, int errorCode, String appid,
                    String userId, String channelId, String requestId) {
                String responseString = "onBind errorCode=" + errorCode + " appid="
                        + appid + " userId=" + userId + " channelId=" + channelId
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
                mChannelId = channelId;
                mUserId = userId;

                try {
                 if (hub == null) {
                        hub = new NotificationHub(
                                ConfigurationSettings.NotificationHubName,
                                ConfigurationSettings.NotificationHubConnectionString,
                                context);
                        Log.i(TAG, "Notification hub initialized");
                    }
                } catch (Exception e) {
                   Log.e(TAG, e.getMessage());
                }

                registerWithNotificationHubs();
            }

            private void registerWithNotificationHubs() {
               new AsyncTask<Void, Void, Void>() {
                  @Override
                  protected Void doInBackground(Void... params) {
                     try {
                         hub.registerBaidu(mUserId, mChannelId);
                         Log.i(TAG, "Registered with Notification Hub - '"
                                + ConfigurationSettings.NotificationHubName + "'"
                                + " with UserId - '"
                                + mUserId + "' and Channel Id - '"
                                + mChannelId + "'");
                     } catch (Exception e) {
                         Log.e(TAG, e.getMessage());
                     }
                     return null;
                 }
               }.execute(null, null, null);
            }

            @Override
            public void onSetTags(Context context, int errorCode,
                    List<String> sucessTags, List<String> failTags, String requestId) {
                String responseString = "onSetTags errorCode=" + errorCode
                        + " sucessTags=" + sucessTags + " failTags=" + failTags
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
            }

            @Override
            public void onDelTags(Context context, int errorCode,
                    List<String> sucessTags, List<String> failTags, String requestId) {
                String responseString = "onDelTags errorCode=" + errorCode
                        + " sucessTags=" + sucessTags + " failTags=" + failTags
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
            }

            @Override
            public void onListTags(Context context, int errorCode, List<String> tags,
                    String requestId) {
                String responseString = "onListTags errorCode=" + errorCode + " tags="
                        + tags;
                Log.d(TAG, responseString);
            }

            @Override
            public void onUnbind(Context context, int errorCode, String requestId) {
                String responseString = "onUnbind errorCode=" + errorCode
                        + " requestId = " + requestId;
                Log.d(TAG, responseString);
            }

            @Override
            public void onNotificationClicked(Context context, String title,
                    String description, String customContentString) {
                String notifyString = "title=\"" + title + "\" description=\""
                        + description + "\" customContent=" + customContentString;
                Log.d(TAG, notifyString);
            }

            @Override
            public void onMessage(Context context, String message,
                    String customContentString) {
                String messageString = "message=\"" + message + "\" customContentString=" + customContentString;
                Log.d(TAG, messageString);
            }
        }

13. Ouvrez **MainActivity.java**et ajoutez le code suivant à la méthode **onCreate** :

            PushManager.startWork(getApplicationContext(),
                    PushConstants.LOGIN_TYPE_API_KEY, ConfigurationSettings.API_KEY);

14. Ouvrez les instructions d’importation suivantes dans la partie supérieure :

            import com.baidu.android.pushservice.PushConstants;
            import com.baidu.android.pushservice.PushManager;

##<a name="send-notifications-to-your-app"></a>Envoyer des notifications dans votre application


Vous pouvez rapidement tester la réception de notifications dans votre application en envoyant des notifications dans le [Portail Azure](https://portal.azure.com/) à l’aide du bouton **Tester Envoyer** dans le hub de notification, comme montré dans l’écran ci-dessous.

![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)

Les notifications Push sont envoyées en règle générale, dans un service principale, tels que les Services mobiles ou ASP.NET à l’aide d’une bibliothèque compatible. Vous pouvez également utiliser l’API REST directement à envoyer des messages de notification si une bibliothèque n’est pas disponible pour votre principale.

Dans ce didacticiel, nous Restez simple et simplement montrer test de votre application client en envoyant des notifications à l’aide du Kit de développement .NET pour hubs de notification dans une application console au lieu d’un service en aval. Nous vous recommandons le didacticiel [Utilisation Notification Hubs aux notifications push pour les utilisateurs](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) en tant que l’étape suivante pour envoyer des notifications à partir d’un serveur principal ASP.NET. Toutefois, les approches suivantes peuvent être utilisées pour envoyer des notifications :

* **Interface REST**: peut prendre en charge notification sur n’importe quelle plate-forme serveur principal à l’aide de l' [interface REST](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).

* **Kit de développement logiciel Microsoft Azure Notification Hubs .NET**: dans le Gestionnaire de Package Nuget pour Visual Studio, exécuter le [Package d’installation Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

* **Node.js** : [comment utiliser les références de Notification de Node.js](notification-hubs-nodejs-push-notification-tutorial.md).

* **Applications Mobile**: pour obtenir un exemple de l’envoi des notifications à partir d’un serveur principal Azure Application Service Mobile applications intégrées avec Hubs de Notification, voir [Ajouter les notifications de transmission pour votre application mobile](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md).

* **Java / PHP**: pour obtenir un exemple de l’envoi des notifications à l’aide de l’API REST, voir « Comment utiliser Hubs de Notification à partir de Java/PHP » ([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)).

##<a name="optional-send-notifications-from-a-net-console-app"></a>(Facultatif) Envoyer des notifications à partir d’une application console .NET.

Dans cette section, nous affichons envoie une notification à l’aide d’une application console .NET.

1. Créer une nouvelle application console c# :

    ![][30]

2. Dans la fenêtre Gestionnaire de Package Console, la valeur **par défaut de projet** votre nouveau projet d’application console et puis, dans la fenêtre de la console, exécutez la commande suivante :

        Install-Package Microsoft.Azure.NotificationHubs

    Cette opération ajoute une référence à la Notification de Hubs Azure SDK en utilisant le <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">package Microsoft.Azure.Notification Hubs NuGet</a>.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)

3. Ouvrez le fichier **Program.cs** et ajoutez les éléments suivants à l’aide de déclaration :

        using Microsoft.Azure.NotificationHubs;

4. Dans votre `Program` classe, ajoutez la méthode suivante et remplacer *DefaultFullSharedAccessSignatureSASConnectionString* et *NotificationHubName* avec les valeurs que vous avez.

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("DefaultFullSharedAccessSignatureSASConnectionString", "NotificationHubName");
            string message = "{\"title\":\"((Notification title))\",\"description\":\"Hello from Azure\"}";
            var result = await hub.SendBaiduNativeNotificationAsync(message);
        }

5. Ajoutez les lignes suivantes dans votre méthode **Main** :

         SendNotificationAsync();
         Console.ReadLine();

##<a name="test-your-app"></a>Tester votre application

Pour tester cette application avec un téléphone réel, simplement vous connecter le téléphone à votre ordinateur à l’aide d’un câble USB. Votre application sur le téléphone jointe est chargée.

Pour tester cette application avec l’émulateur, dans la barre d’outils supérieure de Eclipse, cliquez sur **exécuter**, puis sélectionnez puis votre application. Lance l’émulateur et puis charge et s’exécute à l’application.

L’application récupère les « nom d’utilisateur » et « channelId » sur le service de notification Baidu Push et inscrit auprès du concentrateur de notification.

Pour envoyer une notification de test, vous pouvez utiliser l’onglet déboguer du portail classique Azure. Si vous avez créé l’application de console .NET pour Visual Studio, appuyez sur la touche F5 dans Visual Studio pour exécuter l’application. L’application vous envoie une notification qui s’affichent dans la zone de notification supérieure de votre appareil ou émulateur.


<!-- Images. -->
[1]: ./media/notification-hubs-baidu-get-started/BaiduRegistration.png
[2]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationInput.png
[3]: ./media/notification-hubs-baidu-get-started/BaiduConfirmation.png
[4]: ./media/notification-hubs-baidu-get-started/BaiduActivationEmail.png
[5]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationMore.png
[6]: ./media/notification-hubs-baidu-get-started/BaiduOpenCloudPlatform.png
[7]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperServices.png
[8]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperRegistration.png
[9]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationInput.png
[10]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationConfirmation.png
[11]: ./media/notification-hubs-baidu-get-started/BaiduDevConfirmationFinal.png
[12]: ./media/notification-hubs-baidu-get-started/BaiduCloudPush.png
[13]: ./media/notification-hubs-baidu-get-started/BaiduDevSvcMgmt.png
[14]: ./media/notification-hubs-baidu-get-started/BaiduCreateProject.png
[15]: ./media/notification-hubs-baidu-get-started/BaiduCreateProjectInput.png
[16]: ./media/notification-hubs-baidu-get-started/BaiduProjectKeys.png
[17]: ./media/notification-hubs-baidu-get-started/AzureNHCreation.png
[18]: ./media/notification-hubs-baidu-get-started/NotificationHubs.png
[19]: ./media/notification-hubs-baidu-get-started/NotificationHubsConfigure.png
[20]: ./media/notification-hubs-baidu-get-started/NotificationHubBaiduConfigure.png
[21]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionStringView.png
[22]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionString.png
[23]: ./media/notification-hubs-baidu-get-started/EclipseNewProject.png
[24]: ./media/notification-hubs-baidu-get-started/EclipseProjectCreation.png
[25]: ./media/notification-hubs-baidu-get-started/EclipseBlankActivity.png
[26]: ./media/notification-hubs-baidu-get-started/EclipseProjectBuildProperty.png
[27]: ./media/notification-hubs-baidu-get-started/EclipseBaiduReferences.png
[28]: ./media/notification-hubs-baidu-get-started/EclipseNewClass.png
[29]: ./media/notification-hubs-baidu-get-started/EclipseConfigSettingsClass.png
[30]: ./media/notification-hubs-baidu-get-started/ConsoleProject.png
[31]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig1.png
[32]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig2.png
[33]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig3.png

<!-- URLs. -->
[Kit de développement logiciel Services Mobile Android]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Kit de développement logiciel Baidu Push Android]: http://developer.baidu.com/wiki/index.php?title=docs/cplat/push/sdk/clientsdk
[Portail classique Azure]: https://manage.windowsazure.com/
[Portail Baidu]: http://www.baidu.com/
