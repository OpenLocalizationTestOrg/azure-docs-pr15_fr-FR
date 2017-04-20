<properties
    pageTitle="Notification Azure Hubs sécurisées de transmission"
    description="Découvrez comment envoyer les notifications push sécurisé dans Azure. Exemples de code écrits en c# à l’aide de l’API .NET."
    documentationCenter="windows"
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"/>

<tags
    ms.service="notification-hubs" 
    ms.workload="mobile"
    ms.tgt_pltfrm="windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

#<a name="azure-notification-hubs-secure-push"></a>Notification Azure Hubs sécurisées de transmission

> [AZURE.SELECTOR]
- [Universel de Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
- [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
- [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)


##<a name="overview"></a>Vue d’ensemble

Prise en charge de notification push dans Microsoft Azure permet d’accéder à une infrastructure push facile à utiliser, multi-plateformes, plus grande échelle, ce qui simplifie considérablement la mise en œuvre des notifications push pour les particuliers ou entreprises applications pour plates-formes mobiles.

En raison des contraintes de sécurité, parfois une application peut également inclure un élément dans la notification qui ne peut pas être transmise au moyen de l’infrastructure de notification push standard. Ce didacticiel décrit comment effectuer la même expérience en envoyant des informations sensibles via une connexion sécurisée et authentifiée entre le périphérique client et le système principal de l’application.

À un niveau élevé, le flux est la suivante :

1. L’application principale :
    - Banques d’informations sécurisé charge utile dans la base de données principale.
    - Envoie l’ID de la notification à l’appareil (aucune information sécurisée est envoyée).
2. L’application sur l’appareil, lorsque vous recevez la notification :
    - Le périphérique de contacts principaux demandant la charge utile sécurisée.
    - L’application peut vous indiquer la charge utile dans une notification sur l’appareil.

Il est important de noter que dans le flux précédent (et, dans ce didacticiel), nous part du principe que l’appareil stocke un jeton d’authentification dans le stockage local, une fois que l’utilisateur se connecte. Cela garantit une expérience complètement transparente, comme le périphérique peut récupérer à l’aide de ce jeton de données sécurisé de la notification. Si votre application n’enregistre pas les jetons d’authentification sur le périphérique, ou si ces jetons peuvent être expirés, l’application d’appareil, lorsqu’il reçoit la notification doit afficher une notification générique demander à l’utilisateur pour lancer l’application. L’application authentifie l’utilisateur puis et la charge utile de notification s’affiche.

Ce didacticiel Push Secure montre comment envoyer une notification push en toute sécurité. Le didacticiel s’appuie sur le didacticiel [Avertir les utilisateurs](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) , afin que vous devez effectuer les étapes décrites dans ce didacticiel tout d’abord.

> [AZURE.NOTE] Ce didacticiel suppose que vous avez créé et configuré votre concentrateur de notification comme décrit dans [Prise en main avec Hubs de Notification (Windows Store)](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
Notez également que Windows Phone 8.1 requiert les informations d’identification Windows (pas de Windows Phone), et que les tâches en arrière-plan ne fonctionnent pas sur Windows Phone 8.0 ou Silverlight 8.1. Pour les applications du Windows Store, vous pouvez recevoir des notifications via une tâche en arrière-plan uniquement si l’application est activé d’écran de verrouillage (cliquez sur la case à cocher dans la Appmanifest).

[AZURE.INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-windows-phone-project"></a>Modifier le projet de Windows Phone

1. Dans le projet **NotifyUserWindowsPhone** , ajoutez le code suivant à App.xaml.cs pour enregistrer la tâche d’arrière-plan push. Ajoutez la ligne suivante du code à la fin de la `OnLaunched()` méthode :

        RegisterBackgroundTask();

2. Toujours dans App.xaml.cs, ajoutez ce qui suit le code immédiatement après la `OnLaunched()` méthode :

        private async void RegisterBackgroundTask()
        {
            if (!Windows.ApplicationModel.Background.BackgroundTaskRegistration.AllTasks.Any(i => i.Value.Name == "PushBackgroundTask"))
            {
                var result = await BackgroundExecutionManager.RequestAccessAsync();
                var builder = new BackgroundTaskBuilder();

                builder.Name = "PushBackgroundTask";
                builder.TaskEntryPoint = typeof(PushBackgroundComponent.PushBackgroundTask).FullName;
                builder.SetTrigger(new Windows.ApplicationModel.Background.PushNotificationTrigger());
                BackgroundTaskRegistration task = builder.Register();
            }
        }

3. Ajoutez le code suivant `using` instructions dans la partie supérieure du fichier App.xaml.cs :

        using Windows.Networking.PushNotifications;
        using Windows.ApplicationModel.Background;

4. Dans le menu **fichier** dans Visual Studio, cliquez sur **Enregistrer tout**.

## <a name="create-the-push-background-component"></a>Créer le composant d’arrière-plan Push

L’étape suivante consiste à créer le composant d’arrière-plan push.

1. Dans l’Explorateur, droit sur le nœud de niveau supérieur de la solution (**Solution SecurePush** dans le cas présent), puis cliquez sur **Ajouter**, puis cliquez sur **Nouveau projet**.

2. Développez des **Applications du Store**, puis cliquez sur **Windows Phone applications**, puis cliquez sur **Composant d’exécution Windows (Windows Phone)**. Nommez le projet **PushBackgroundComponent**, puis cliquez sur **OK** pour créer le projet.

    ![][12]

3. Dans l’Explorateur, droit sur le projet **PushBackgroundComponent (Windows Phone 8.1)** , puis cliquez sur **Ajouter**, puis cliquez sur **cours**. Nom de la nouvelle classe **PushBackgroundTask.cs**. Cliquez sur **Ajouter** pour générer la classe.

4. Remplacer tout le contenu de la définition d’espace de noms **PushBackgroundComponent** par le code suivant, en substituant l’espace réservé `{back-end endpoint}` avec le point de terminaison principale obtenue lors du déploiement de votre principale :

        public sealed class Notification
            {
                public int Id { get; set; }
                public string Payload { get; set; }
                public bool Read { get; set; }
            }

            public sealed class PushBackgroundTask : IBackgroundTask
            {
                private string GET_URL = "{back-end endpoint}/api/notifications/";

                async void IBackgroundTask.Run(IBackgroundTaskInstance taskInstance)
                {
                    // Store the content received from the notification so it can be retrieved from the UI.
                    RawNotification raw = (RawNotification)taskInstance.TriggerDetails;
                    var notificationId = raw.Content;

                    // retrieve content
                    BackgroundTaskDeferral deferral = taskInstance.GetDeferral();
                    var httpClient = new HttpClient();
                    var settings = ApplicationData.Current.LocalSettings.Values;
                    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                    var notificationString = await httpClient.GetStringAsync(GET_URL + notificationId);

                    var notification = JsonConvert.DeserializeObject<Notification>(notificationString);

                    ShowToast(notification);

                    deferral.Complete();
                }

                private void ShowToast(Notification notification)
                {
                    ToastTemplateType toastTemplate = ToastTemplateType.ToastText01;
                    XmlDocument toastXml = ToastNotificationManager.GetTemplateContent(toastTemplate);
                    XmlNodeList toastTextElements = toastXml.GetElementsByTagName("text");
                    toastTextElements[0].AppendChild(toastXml.CreateTextNode(notification.Payload));
                    ToastNotification toast = new ToastNotification(toastXml);
                    ToastNotificationManager.CreateToastNotifier().Show(toast);
                }
            }

5. Dans l’Explorateur, droit sur le projet **PushBackgroundComponent (Windows Phone 8.1)** , puis sur **Gérer les Packages NuGet**.

6. Sur le côté gauche, cliquez sur **en ligne**.

7. Dans la zone **Rechercher** , tapez **Http Client**.

8. Dans la liste des résultats, cliquez sur **Bibliothèques du Client Microsoft HTTP**, puis cliquez sur **installer**. Terminer l’installation.

9. Dans la zone NuGet **recherche** , tapez **Json.net**. Installer le package **Json.NET** , puis fermez la fenêtre Gestionnaire de Package NuGet.

10. Ajoutez le code suivant `using` instructions dans la partie supérieure du fichier **PushBackgroundTask.cs** :

        using Windows.ApplicationModel.Background;
        using Windows.Networking.PushNotifications;
        using System.Net.Http;
        using Windows.Storage;
        using System.Net.Http.Headers;
        using Newtonsoft.Json;
        using Windows.UI.Notifications;
        using Windows.Data.Xml.Dom;

11. Dans l’Explorateur, dans le projet **NotifyUserWindowsPhone (Windows Phone 8.1)** , cliquez sur **références**, puis cliquez sur **Ajouter une référence**. Dans la boîte de dialogue Gestionnaire de référence, cochez la case en regard de **PushBackgroundComponent**, puis cliquez sur **OK**.

12. Dans l’Explorateur de solutions, double-cliquez sur **Package.appxmanifest** dans le projet **NotifyUserWindowsPhone (Windows Phone 8.1)** . Sous **Notifications**, définissez **Toast Capable** sur **Oui**.

    ![][3]

13. Toujours dans **Package.appxmanifest**, cliquez sur le menu des **déclarations** dans la partie supérieure. Dans la liste déroulante **Déclarations disponibles** , cliquez sur **Tâches en arrière-plan**, puis cliquez sur **Ajouter**.

14. Dans **Package.appxmanifest**, sous **Propriétés**, vérifiez la **notification d’émission**.

15. Dans **Package.appxmanifest**, sous **Paramètres de l’application**, tapez **PushBackgroundComponent.PushBackgroundTask** dans le champ de **Point d’entrée** .

    ![][13]

16. Dans le menu **fichier** , cliquez sur **Enregistrer tout**.

## <a name="run-the-application"></a>Exécuter l’Application

Pour exécuter l’application, procédez comme suit :

1. Dans Visual Studio, exécutez l’application de l’API Web **AppBackend** . Une page web ASP.NET s’affiche.

2. Dans Visual Studio, exécutez l’application Windows Phone **NotifyUserWindowsPhone (Windows Phone 8.1)** . L’émulateur Windows Phone s’exécute et charge automatiquement l’application.

3. Dans l’application **NotifyUserWindowsPhone** interface utilisateur, entrez un nom d’utilisateur et mot de passe. Il peut s’agir n’importe quelle chaîne, mais ils doivent avoir la même valeur.

4. Dans l’application **NotifyUserWindowsPhone** interface utilisateur, cliquez sur **se connecter et inscrire**. Cliquez ensuite sur **Envoyer push**.

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
