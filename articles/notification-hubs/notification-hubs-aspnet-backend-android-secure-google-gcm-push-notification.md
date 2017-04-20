<properties
    pageTitle="Envoi des Notifications Push sécurisé avec Hubs de Notification Azure"
    description="Découvrez comment envoyer les notifications push sécurisé à une application Android à partir d’Azure. Exemples de code écrits en Java et c#."
    documentationCenter="android"
    keywords="notification, les notifications push, d’émission transmission des messages, les notifications push android"
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

#<a name="sending-secure-push-notifications-with-azure-notification-hubs"></a>Envoi des Notifications Push sécurisé avec Hubs de Notification Azure

> [AZURE.SELECTOR]
- [Universel de Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
- [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
- [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

##<a name="overview"></a>Vue d’ensemble

> [AZURE.IMPORTANT] Pour effectuer ce didacticiel, vous devez disposer d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

Prise en charge de notification push dans Microsoft Azure permet d’accéder à une infrastructure de message push facile à utiliser, multi-plateformes, plus grande échelle, ce qui simplifie considérablement la mise en œuvre des notifications push pour les particuliers ou entreprises applications pour plates-formes mobiles.

En raison des contraintes de sécurité, parfois une application peut également inclure un élément dans la notification qui ne peut pas être transmise au moyen de l’infrastructure de notification push standard. Ce didacticiel décrit comment effectuer la même expérience en envoyant des informations sensibles via une connexion sécurisée et authentifiée entre l’appareil client et le système principal de l’application.

À un niveau élevé, le flux est la suivante :

1. L’application principale :
    - Banques d’informations sécurisé charge utile dans la base de données principale.
    - Envoie l’ID de la notification à l’appareil Android (aucune information sécurisée est envoyée).
2. L’application sur l’appareil, lorsque vous recevez la notification :
    - L’appareil Android contacte principaux demandant la charge utile sécurisée.
    - L’application peut vous indiquer la charge utile dans une notification sur l’appareil.

Il est important de noter que dans le flux précédent (et, dans ce didacticiel), nous part du principe que l’appareil stocke un jeton d’authentification dans le stockage local, une fois que l’utilisateur se connecte. Cela garantit une expérience complètement transparente, comme le périphérique peut récupérer à l’aide de ce jeton de données sécurisé de la notification. Si votre application n’enregistre pas les jetons d’authentification sur le périphérique, ou si ces jetons peuvent être expirés, l’application d’appareil, lorsqu’il reçoit les notifications doit afficher une notification générique demander à l’utilisateur pour lancer l’application. L’application authentifie l’utilisateur puis et la charge utile de notification s’affiche.

Ce didacticiel montre comment envoyer les notifications push sécurisé. Il appuie sur le didacticiel [Avertir les utilisateurs](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md) , afin que vous devez effectuer tout d’abord les étapes décrites dans ce didacticiel si vous n’avez pas déjà.

> [AZURE.NOTE] Ce didacticiel suppose que vous avez créé et configuré votre concentrateur de notification comme décrit dans [Prise en main avec Hubs de Notification (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md).

[AZURE.INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>Modifier le projet Android

À présent que vous avez modifié votre principale application pour envoyer uniquement l' *id* d’une notification push, vous devez modifier votre application Android pour gérer cette notification et rappeler votre principale pour récupérer le message sécurisé à afficher.
Pour ce faire, vous devez vous assurer que votre application Android sait comment authentifier avec votre principale lorsqu’il reçoit les notifications push.

Nous allons maintenant modifier le flux de *connexion* afin d’enregistrer la valeur d’en-tête d’authentification dans les préférences de votre application partagées. Mécanismes analogues peuvent servir à stocker un jeton d’authentification (par exemple, OAuth jetons) que l’application devront utiliser sans avoir besoin d’informations d’identification utilisateur.

1. Dans votre projet Android application, ajoutez les constantes suivantes en haut de la classe **MainActivity** :

        public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
        public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";

2. Toujours dans la classe **MainActivity** , mettez à jour la `getAuthorizationHeader()` méthode contenant le code suivant :

        private String getAuthorizationHeader() throws UnsupportedEncodingException {
            EditText username = (EditText) findViewById(R.id.usernameText);
            EditText password = (EditText) findViewById(R.id.passwordText);
            String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
            basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);

            SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
            sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();

            return basicAuthHeader;
        }

3. Ajoutez le code suivant `import` instructions dans la partie supérieure du fichier **MainActivity** :

        import android.content.SharedPreferences;

Nous allons maintenant modifier le gestionnaire qui est appelé lors de la réception de la notification.

4. Dans la **MyHandler** classe modifier la `OnReceive()` méthode ne contienne :

        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String secureMessageId = bundle.getString("secureId");
            retrieveNotification(secureMessageId);
        }

5. Puis ajoutez le `retrieveNotification()` méthode, en remplaçant l’espace réservé `{back-end endpoint}` avec le point de terminaison principale obtenue lors du déploiement de votre principale :

        private void retrieveNotification(final String secureMessageId) {
            SharedPreferences sp = ctx.getSharedPreferences(MainActivity.NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
            final String authorizationHeader = sp.getString(MainActivity.AUTHORIZATION_HEADER_PROPERTY, null);

            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
                        HttpUriRequest request = new HttpGet("{back-end endpoint}/api/notifications/"+secureMessageId);
                        request.addHeader("Authorization", "Basic "+authorizationHeader);
                        HttpResponse response = new DefaultHttpClient().execute(request);
                        if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                            Log.e("MainActivity", "Error retrieving secure notification" + response.getStatusLine().getStatusCode());
                            throw new RuntimeException("Error retrieving secure notification");
                        }
                        String secureNotificationJSON = EntityUtils.toString(response.getEntity());
                        JSONObject secureNotification = new JSONObject(secureNotificationJSON);
                        sendNotification(secureNotification.getString("Payload"));
                    } catch (Exception e) {
                        Log.e("MainActivity", "Failed to retrieve secure notification - " + e.getMessage());
                        return e;
                    }
                    return null;
                }
            }.execute(null, null, null);
        }


Cette méthode appelle votre principale application pour extraire le contenu de notification avec les informations d’identification stockées dans les préférences partagés et affiche sous la forme d’une notification standard. La notification ressemble à l’utilisateur d’application exactement comme les autres notifications push.

Notez qu’il est préférable pour gérer les cas de propriété d’en-tête d’authentification manquants ou refus par le serveur principal. La gestion des cas spécifique dépendent principalement votre expérience utilisateur cible. Une option consiste à afficher une notification invitant générique pour l’utilisateur s’authentifier pour récupérer la notification réelle.

## <a name="run-the-application"></a>Exécuter l’Application

Pour exécuter l’application, procédez comme suit :

1. Assurez-vous que **AppBackend** est déployée dans Azure. Si vous utilisez Visual Studio, exécutez l’application de l’API Web **AppBackend** . Une page web ASP.NET s’affiche.

2. Dans Eclipse, exécutez l’application sur un appareil Android physique ou l’émulateur.

3. Dans l’interface utilisateur d’application Android, entrez un nom d’utilisateur et mot de passe. Il peut s’agir n’importe quelle chaîne, mais ils doivent avoir la même valeur.

4. Dans l’interface utilisateur d’application Android, cliquez sur **se connecter**. Cliquez ensuite sur **Envoyer push**.
