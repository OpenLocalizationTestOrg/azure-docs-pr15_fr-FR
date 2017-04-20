<properties
    pageTitle="Envoyer les notifications push aux applications de Chrome avec Hubs de Notification Azure | Microsoft Azure"
    description="Découvrez comment utiliser Azure Notification Hubs pour envoyer les notifications push pour une application de Chrome."
    services="notification-hubs"
    keywords="notification, d’émission notifications push mobiles, les notifications push, les notifications push de chrome"
    documentationCenter=""
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-chrome"
    ms.devlang="JavaScript"
    ms.topic="hero-article"
    ms.date="10/03/2016"
    ms.author="yuaxu"/>

# <a name="send-push-notifications-to-chrome-apps-with-azure-notification-hubs"></a>Envoyer les notifications push aux applications de Chrome avec Hubs de Notification Azure

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Cette rubrique vous montre comment utiliser Azure Notification Hubs pour envoyer les notifications push pour une application Chrome, qui sera affichée dans le cadre du navigateur Google Chrome. Dans ce didacticiel, nous allons créer une application de Chrome qui reçoit les notifications push à l’aide de [Google Cloud messagerie (GCM)](https://developers.google.com/cloud-messaging/). 

>[AZURE.NOTE] Pour effectuer ce didacticiel, vous devez disposer d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%notification-hubs-chrome-get-started%2F).

Le didacticiel vous guide dans ces étapes de base pour activer les notifications push :

* [Activer le Cloud Google messagerie](#register)
* [Configurer votre concentrateur de notification](#configure-hub)
* [Se connecter votre application de Chrome au hub de notification](#connect-app)
* [Envoyer une notification push à votre application de Chrome](#send)
* [Fonctionnalités et fonctionnalités supplémentaires](#next-steps)

>[AZURE.NOTE] Les notifications push chrome application ne sont pas des notifications dans le navigateur génériques - elles sont spécifiques à l’extensibilité navigateur modèle (voir [Vue d’ensemble des applications de Chrome] pour plus d’informations). Outre le navigateur de bureau, les applications de Chrome s’exécuter sur mobile (iOS et Android) par le biais Apache Cordova. Voir les [Applications de Chrome sur appareil Mobile] pour en savoir plus.

Configuration GCM et Azure Notification Hubs est identique à la configuration pour Android, étant donné que [Google Cloud la messagerie pour Chrome] est déconseillée et le même GCM prend désormais en charge les appareils Android et instances de Chrome.

##<a id="register"></a>Activer le Cloud Google messagerie

1. Accédez au site Web [Google Cloud Console] , connectez-vous à l’aide de vos informations d’identification du compte Google et puis cliquez sur le bouton **Créer un projet** . Spécifiez un **Nom de projet**approprié, puis cliquez sur le bouton **créer** .

    ![Google Cloud Console - créer un projet][1]

2. Notez le **Numéro de projet** dans la page de **projets** pour le projet que vous venez de créer. Vous allez utiliser ce que l' **ID de l’expéditeur GCM** dans l’application de Chrome pour enregistrer avec GCM.

    ![Console Google Cloud - numéro de projet][2]

3. Dans le volet gauche, cliquez sur **API & auth**et puis faites défiler vers le bas et cliquez sur le bouton bascule pour activer la **Messagerie de Cloud Google pour Android**. Vous n’êtes pas obligé d’activer la **Messagerie de Cloud Google pour Chrome**.

    ![Console Google Cloud - clé serveur][3]

4. Dans le volet gauche, cliquez sur **informations d’identification** > **Créer une nouvelle clé** > **Clé serveur** > **créer**.

    ![Google Cloud Console - informations d’identification][4]

5. Prenez note du serveur **Clé API**. Vous allez configurer ceci dans votre hub notification ensuite pour lui permettre d’envoyer des notifications push aux GCM.

    ![Console Google Cloud - clé API][5]

##<a id="configure-hub"></a>Configurer votre concentrateur de notification

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


&emsp;&emsp;6. dans la carte de **paramètres** , sélectionnez **Les Services de Notification** , puis **Google (GCM)**. Entrez la clé API et enregistrer.

&emsp;&emsp;![Notification Azure Hubs - Google (GCM)](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

##<a id="connect-app"></a>Se connecter votre application de Chrome au hub de notification

Votre concentrateur de notification est maintenant configuré pour fonctionner avec GCM, et vous avez les chaînes de connexion pour enregistrer votre application pour recevoir et envoyer des notifications de transmission. LK

###<a name="create-a-new-chrome-app"></a>Créer une nouvelle application de Chrome

L’exemple ci-dessous est basé sur l' [Exemple de Chrome application GCM] et utilise les procédures recommandées pour créer une application de Chrome. Nous surligne les étapes spécifiquement liées aux Hubs de Notification Azure. 

>[AZURE.NOTE] Nous vous recommandons de télécharger les sources pour cette application Chrome de [Chrome application Notification concentrateur échantillon].

L’application de Chrome est créée via JavaScript, et vous pouvez utiliser un de vos éditeurs de word par défaut pour la création de celle-ci. Voici l’aspect de cette application Chrome.

![Application de Google Chrome][15]

1. Créer un dossier et nommez-la `ChromePushApp`. Bien entendu, le nom est arbitraire : Si vous donnez-lui un nommez différent, vérifiez que vous remplacez le chemin d’accès dans les segments de code requis.

2. Téléchargez la [bibliothèque et au js] dans le dossier que vous avez créé dans la deuxième étape. Ce dossier de bibliothèque contiendra deux sous-dossiers : `components` et `rollups`.

3. Créer un `manifest.json` fichier. Toutes les applications de Chrome sont accompagnées d’un fichier manifeste qui contient les métadonnées de l’application et plus important, toutes les autorisations accordées à l’application lorsque l’utilisateur installe.

        {
          "name": "NH-GCM Notifications",
          "description": "Chrome platform app.",
          "manifest_version": 2,
          "version": "0.1",
          "app": {
            "background": {
              "scripts": ["background.js"]
            }
          },
          "permissions": ["gcm", "storage", "notifications", "https://*.servicebus.windows.net/*"],
          "icons": { "128": "gcm_128.png" }
        }

    Notification de la `permissions` élément, qui spécifie que cette application Chrome seront en mesure de recevoir les notifications de transmission de GCM. Il doit également spécifier l’URI Hubs de Notification Azure où l’application de Chrome effectue un appel reste à enregistrer.
    Notre exemple d’application utilise également un fichier d’icône, `gcm_128.png`, que vous trouverez à la source est réutilisée à partir de l’échantillon GCM d’origine. Vous pouvez la remplacer par pour n’importe quelle image qui correspond à des [critères de l’icône](https://developer.chrome.com/apps/manifest/icons).

4. Créer un fichier nommé `background.js` par le code suivant :

        // Returns a new notification ID used in the notification.
        function getNotificationId() {
          var id = Math.floor(Math.random() * 9007199254740992) + 1;
          return id.toString();
        }

        function messageReceived(message) {
          // A message is an object with a data property that
          // consists of key-value pairs.

          // Concatenate all key-value pairs to form a display string.
          var messageString = "";
          for (var key in message.data) {
            if (messageString != "")
              messageString += ", "
            messageString += key + ":" + message.data[key];
          }
          console.log("Message received: " + messageString);

          // Pop up a notification to show the GCM message.
          chrome.notifications.create(getNotificationId(), {
            title: 'GCM Message',
            iconUrl: 'gcm_128.png',
            type: 'basic',
            message: messageString
          }, function() {});
        }

        var registerWindowCreated = false;

        function firstTimeRegistration() {
          chrome.storage.local.get("registered", function(result) {

            registerWindowCreated = true;
            chrome.app.window.create(
              "register.html",
              {  width: 520,
                 height: 500,
                 frame: 'chrome'
              },
              function(appWin) {}
            );
          });
        }

        // Set up a listener for GCM message event.
        chrome.gcm.onMessage.addListener(messageReceived);

        // Set up listeners to trigger the first-time registration.
        chrome.runtime.onInstalled.addListener(firstTimeRegistration);
        chrome.runtime.onStartup.addListener(firstTimeRegistration);

    C’est le fichier qui s’affiche la fenêtre de l’application de Chrome HTML (**register.html**) et également définit le gestionnaire **messageReceived** pour gérer la notification push entrants.

5. Créer un fichier nommé `register.html` -définit l’interface utilisateur de l’application de Chrome. 

   >[AZURE.NOTE] Cet exemple utilise **CryptoJS v3.1.2**. Si vous avez téléchargé une autre version de la bibliothèque, vérifiez que vous remplacez correctement par la version dans les `src` chemin d’accès.

        <html>

        <head>
        <title>GCM Registration</title>
        <script src="register.js"></script>
        <script src="CryptoJS v3.1.2/rollups/hmac-sha256.js"></script>
        <script src="CryptoJS v3.1.2/components/enc-base64-min.js"></script>
        </head>

        <body>

        Sender ID:<br/><input id="senderId" type="TEXT" size="20"><br/>
        <button id="registerWithGCM">Register with GCM</button>
        <br/>
        <br/>
        <br/>

        Notification Hub Name:<br/><input id="hubName" type="TEXT" style="width:400px"><br/><br/>
        Connection String:<br/><textarea id="connectionString" type="TEXT" style="width:400px;height:60px"></textarea>

        <br/>

        <button id="registerWithNH" disabled="true">Register with Azure Notification Hubs</button>

        <br/>
        <br/>

        <textarea id="console" type="TEXT" readonly style="width:500px;height:200px;background-color:#e5e5e5;padding:5px"></textarea>

        </body>

        </html>

6. Créer un fichier nommé `register.js` par le code ci-dessous. Ce fichier Spécifie le script derrière `register.html`. Applications de chrome ne permettent pas l’exécution inline, afin que vous deviez créer un script de sauvegarde distincte pour votre interface utilisateur.

        var registrationId = "";
        var hubName        = "", connectionString = "";
        var originalUri    = "", targetUri = "", endpoint = "", sasKeyName = "", sasKeyValue = "", sasToken = "";

        window.onload = function() {
           document.getElementById("registerWithGCM").onclick = registerWithGCM;  
           document.getElementById("registerWithNH").onclick = registerWithNH;
           updateLog("You have not registered yet. Please provider sender ID and register with GCM and then with  Notification Hubs.");
        }

        function updateLog(status) {
          currentStatus = document.getElementById("console").innerHTML;
          if (currentStatus != "") {
            currentStatus = currentStatus + "\n\n";
          }

          document.getElementById("console").innerHTML = currentStatus  + status;
        }

        function registerWithGCM() {
          var senderId = document.getElementById("senderId").value.trim();
          chrome.gcm.register([senderId], registerCallback);

          // Prevent register button from being clicked again before the registration finishes.
          document.getElementById("registerWithGCM").disabled = true;
        }

        function registerCallback(regId) {
          registrationId = regId;
          document.getElementById("registerWithGCM").disabled = false;

          if (chrome.runtime.lastError) {
            // When the registration fails, handle the error and retry the
            // registration later.
            updateLog("Registration failed: " + chrome.runtime.lastError.message);
            return;
          }

          updateLog("Registration with GCM succeeded.");
          document.getElementById("registerWithNH").disabled = false;

          // Mark that the first-time registration is done.
          chrome.storage.local.set({registered: true});
        }

        function registerWithNH() {
          hubName = document.getElementById("hubName").value.trim();
          connectionString = document.getElementById("connectionString").value.trim();
          splitConnectionString();
          generateSaSToken();
          sendNHRegistrationRequest();
        }

        // From http://msdn.microsoft.com/library/dn495627.aspx
        function splitConnectionString()
        {
          var parts = connectionString.split(';');
          if (parts.length != 3)
          throw "Error parsing connection string";

          parts.forEach(function(part) {
            if (part.indexOf('Endpoint') == 0) {
            endpoint = 'https' + part.substring(11);
            } else if (part.indexOf('SharedAccessKeyName') == 0) {
            sasKeyName = part.substring(20);
            } else if (part.indexOf('SharedAccessKey') == 0) {
            sasKeyValue = part.substring(16);
            }
          });

          originalUri = endpoint + hubName;
        }

        function generateSaSToken()
        {
          targetUri = encodeURIComponent(originalUri.toLowerCase()).toLowerCase();
          var expiresInMins = 10; // 10 minute expiration

          // Set expiration in seconds.
          var expireOnDate = new Date();
          expireOnDate.setMinutes(expireOnDate.getMinutes() + expiresInMins);
          var expires = Date.UTC(expireOnDate.getUTCFullYear(), expireOnDate
            .getUTCMonth(), expireOnDate.getUTCDate(), expireOnDate
            .getUTCHours(), expireOnDate.getUTCMinutes(), expireOnDate
            .getUTCSeconds()) / 1000;
          var tosign = targetUri + '\n' + expires;

          // Using CryptoJS.
          var signature = CryptoJS.HmacSHA256(tosign, sasKeyValue);
          var base64signature = signature.toString(CryptoJS.enc.Base64);
          var base64UriEncoded = encodeURIComponent(base64signature);

          // Construct authorization string.
          sasToken = "SharedAccessSignature sr=" + targetUri + "&sig="
                          + base64UriEncoded + "&se=" + expires + "&skn=" + sasKeyName;
        }

        function sendNHRegistrationRequest()
        {
          var registrationPayload =
          "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
          "<entry xmlns=\"http://www.w3.org/2005/Atom\">" +
              "<content type=\"application/xml\">" +
                  "<GcmRegistrationDescription xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/netservices/2010/10/servicebus/connect\">" +
                      "<GcmRegistrationId>{GCMRegistrationId}</GcmRegistrationId>" +
                  "</GcmRegistrationDescription>" +
              "</content>" +
          "</entry>";

          // Update the payload with the registration ID obtained earlier.
          registrationPayload = registrationPayload.replace("{GCMRegistrationId}", registrationId);

          var url = originalUri + "/registrations/?api-version=2014-09";
          var client = new XMLHttpRequest();

          client.onload = function () {
            if (client.readyState == 4) {
              if (client.status == 200) {
                updateLog("Notification Hub Registration succesful!");
                updateLog(client.responseText);
              } else {
                updateLog("Notification Hub Registration did not succeed!");
                updateLog("HTTP Status: " + client.status + " : " + client.statusText);
                updateLog("HTTP Response: " + "\n" + client.responseText);
              }
            }
          };

          client.onerror = function () {
                updateLog("ERROR - Notification Hub Registration did not succeed!");
          }

          client.open("POST", url, true);
          client.setRequestHeader("Content-Type", "application/atom+xml;type=entry;charset=utf-8");
          client.setRequestHeader("Authorization", sasToken);
          client.setRequestHeader("x-ms-version", "2014-09");

          try {
              client.send(registrationPayload);
          }
          catch(err) {
              updateLog(err.message);
          }
        }

    Le script ci-dessus comporte les paramètres de clé suivantes :
    - **Window.OnLoad** définit les événements clic de bouton des deux boutons dans l’interface utilisateur. Une inscrit avec GCM, et l’autre utilise l’ID de l’enregistrement est renvoyé après l’enregistrement auprès de GCM pour inscrire avec Hubs de Notification Azure.
    - **updateLog** est la fonction qui nous permettent de gérer les fonctionnalités de journalisation simple.
    - **registerWithGCM** est le premier clic sur le bouton Gestionnaire, ce qui permet du `chrome.gcm.register` appel à GCM pour enregistrer l’instance actuelle de Chrome application.
    - **registerCallback** est la fonction de rappel est appelée lorsque l’appel d’inscription GCM renvoie.
    - **registerWithNH** est le second gestionnaire de clic de bouton, inscrit avec Hubs de Notification. Il atteigne `hubName` et `connectionString` (que l’utilisateur a spécifié) et développe l’appel API REST de Notification Hubs d’inscription.
    - **splitConnectionString** et **generateSaSToken** sont des Assistants représentant l’implémentation JavaScript d’un processus de création d’un jeton associations de sécurité, qui doit être utilisé dans tous les appels API REST. Pour plus d’informations, voir [Concepts communs](http://msdn.microsoft.com/library/dn495627.aspx).
    - **sendNHRegistrationRequest** est la fonction qui effectue une reste HTTP appel aux Hubs de Notification Azure.
    - **registrationPayload** définit la charge utile XML d’inscription. Pour plus d’informations, voir [Créer d’API REST NH d’inscription]. Nous mettre à jour l’ID de l’enregistrement dedans avec ce que nous avons reçu de GCM.
    - **client** est une instance de **XMLHttpRequest** qui nous permet d’effectuer la requête HTTP POST. Notez que nous mettre à jour la `Authorization` en-tête avec `sasToken`. Réussite de cet appel enregistre cette instance de l’application de Chrome avec Hubs de Notification Azure.


La structure de dossier globale pour ce projet doit ressembler à ceci :     ![l’application Google Chrome - arborescence][21]

###<a name="set-up-and-test-your-chrome-app"></a>Configurer et tester votre application de Chrome

1. Ouvrez votre navigateur Chrome. Ouvrir **les extensions de Chrome** et activer le **mode développeur**.

    ![Google Chrome - activer le Mode développeur][16]

2. Cliquez sur **charger l’extension décompressée** et accédez au dossier où vous avez créé les fichiers. Vous pouvez également éventuellement utiliser les **applications de Chrome et Extensions développeur outil**. Cet outil est une application de Chrome dans lui-même (installé à partir du Web Store Chrome) et fournit des fonctions avancées de débogage pour le développement de votre application de Chrome.

    ![Google Chrome - charger l’Extension décompressée][17]

3. Si l’application de Chrome est créée sans des erreurs, vous verrez votre application Chrome apparaissant.

    ![Google Chrome - affichage de l’application de Chrome][18]

4. Entrez le **Numéro de projet** que vous avez précédemment à partir de **Google Cloud Console** tel que l’ID de l’expéditeur, puis cliquez sur **Enregistrer avec GCM**. Vous devez voir le message **l’enregistrement auprès de GCM a réussi.**

    ![Google Chrome - personnalisation de l’application de Chrome][19]

5. Entrez votre **Nom de concentrateur de Notification** et la **DefaultListenSharedAccessSignature** que vous avez obtenu à partir du portail, puis cliquez sur **Enregistrer avec Azure Notification concentrateur**. Vous devez voir le message **Notification concentrateur inscription réussie !** et les détails de la réponse d’inscription, qui contient l’enregistrement Azure Notification Hubs identifiant.

    ![Google Chrome - spécifier les détails de concentrateur de Notification][20]  

##<a name="send"></a>Envoyer une notification à votre application de Chrome

Pour effectuer un test, nous enverrons les notifications push Chrome en utilisant un .NET application console. 

>[AZURE.NOTE] Vous pouvez envoyer les notifications push avec Hubs de Notification à partir de n’importe quel serveur principal via notre public <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interface REST</a>. Consultez notre [portail documentation](https://azure.microsoft.com/documentation/services/notification-hubs/) pour obtenir d’autres exemples de disponibilité sur plusieurs plateformes.

1. Dans Visual Studio, dans le menu **fichier** , sélectionnez **Nouveau** , puis cliquez sur **projet**. Sous **c#**, cliquez sur **Windows** et **Application Console**, puis cliquez sur **OK**.  Cela crée un nouveau projet d’application console.

2. Dans le menu **Outils** , cliquez sur **Gestionnaire de Package de bibliothèque** , puis **Console du Gestionnaire de Package**. Cela permet d’afficher la Console du Gestionnaire de Package.

3. Dans la fenêtre de la console, exécutez la commande suivante :

        Install-Package Microsoft.Azure.NotificationHubs

    Cette opération ajoute une référence au SDK Azure Service Bus avec le <a href="http://nuget.org/packages/  WindowsAzure.ServiceBus/">package WindowsAzure.ServiceBus NuGet</a>.

4. Ouvrir `Program.cs` et ajoutez le code suivant `using` instruction :

        using Microsoft.Azure.NotificationHubs;

5. Dans la `Program` de classes, ajoutez la méthode suivante :

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            String message = "{\"data\":{\"message\":\"Hello Chrome from Azure Notification Hubs\"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }

    Veillez à remplacer la `<hub name>` espace réservé avec le nom du concentrateur de notification qui s’affiche dans le [portail](https://portal.azure.com) dans votre carte concentrateur de Notification. De même, remplacez l’espace réservé chaîne de connexion avec la chaîne de connexion appelée `DefaultFullSharedAccessSignature` que vous avez obtenu dans la section configuration de concentrateur de notification.

    >[AZURE.NOTE] Assurez-vous que vous utilisez la chaîne de connexion avec un accès **complet** , **écouter** pas l’accès. La chaîne de connexion access **écouter** n’accorde pas les autorisations pour envoyer des notifications push.

5. Ajouter les appels suivants dans la `Main` méthode :

         SendNotificationAsync();
         Console.ReadLine();
         
6. Vérifiez que Chrome est en cours d’exécution et exécutez l’application console.

7. Vous devriez voir la notification suivante contextuelle sur votre bureau.

    ![Google Chrome - Notification][13]

8. Vous pouvez également voir toutes vos notifications à l’aide de la fenêtre Chrome Notifications dans la barre des tâches (dans Windows) lorsque Chrome est en cours d’exécution.

    ![Google Chrome - liste de notification][14]

>[AZURE.NOTE] Vous ne devez avoir l’exécution de l’application de Chrome ou ouvrir dans le navigateur (même si le navigateur Chrome lui-même doit être en cours d’exécution). Vous obtenez également une vue d’ensemble de toutes vos notifications dans la fenêtre Notifications Chrome.

## <a name="next-steps"> </a>Étapes suivantes

Apprenez-en davantage sur Hubs de Notification dans la [Vue d’ensemble des Hubs de Notification].

Pour cibler des utilisateurs spécifiques, consultez le didacticiel [Azure Notification Hubs informer les utilisateurs] . 

Si vous voulez segmenter vos utilisateurs en groupes d’intérêt, vous pouvez suivre le didacticiel [Azure Notification Hubs dernières actualités] .

<!-- Images. -->
[1]: ./media/notification-hubs-chrome-get-started/GoogleConsoleCreateProject.PNG
[2]: ./media/notification-hubs-chrome-get-started/GoogleProjectNumber.png
[3]: ./media/notification-hubs-chrome-get-started/EnableGCM.png
[4]: ./media/notification-hubs-chrome-get-started/CreateServerKey.png
[5]: ./media/notification-hubs-chrome-get-started/ServerKey.png
[6]: ./media/notification-hubs-chrome-get-started/CreateNH.png
[7]: ./media/notification-hubs-chrome-get-started/NHNamespace.png
[8]: ./media/notification-hubs-chrome-get-started/NamespaceConfigure.png
[9]: ./media/notification-hubs-chrome-get-started/NHConfigure.png
[10]: ./media/notification-hubs-chrome-get-started/NHConfigureGCM.png
[11]: ./media/notification-hubs-chrome-get-started/NHDashboard.png
[12]: ./media/notification-hubs-chrome-get-started/NHConnString.png
[13]: ./media/notification-hubs-chrome-get-started/ChromeNotification.png
[14]: ./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png
[15]: ./media/notification-hubs-chrome-get-started/ChromeApp.png
[16]: ./media/notification-hubs-chrome-get-started/ChromeExtensions.png
[17]: ./media/notification-hubs-chrome-get-started/ChromeLoadExtension.png
[18]: ./media/notification-hubs-chrome-get-started/ChromeAppLoaded.png
[19]: ./media/notification-hubs-chrome-get-started/ChromeAppGCM.png
[20]: ./media/notification-hubs-chrome-get-started/ChromeAppNH.png
[21]: ./media/notification-hubs-chrome-get-started/FinalFolderView.png

<!-- URLs. -->
[Exemple de concentrateur de Notification de l’application de chrome]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToChromeApps
[Console Google Cloud]: http://cloud.google.com/console
[Azure Classic Portal]: https://manage.windowsazure.com/
[Vue d’ensemble des Hubs de notification]: notification-hubs-push-notification-overview.md
[Vue d’ensemble des applications de chrome]: https://developer.chrome.com/apps/about_apps
[Exemple d’application GCM chrome]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[Installable Web Apps]: https://developers.google.com/chrome/apps/docs/
[Applications de chrome sur appareil Mobile]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[Créer des API REST NH d’enregistrement]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[bibliothèque de chiffrement js]: http://code.google.com/p/crypto-js/
[GCM with Chrome Apps]: https://developer.chrome.com/apps/cloudMessaging
[Google Cloud la messagerie pour Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Notification Azure Hubs informer les utilisateurs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Informations de dernière minute Notification Hubs Azure]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
