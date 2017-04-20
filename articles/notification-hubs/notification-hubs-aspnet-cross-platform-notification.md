<properties
    pageTitle="Envoyer des notifications de disponibilité sur plusieurs plateformes aux utilisateurs avec Notification Hubs (ASP.NET)"
    description="Découvrez comment utiliser des modèles de Notification Hubs à envoyer, dans une seule demande, une notification indépendant de la plateforme qui cible toutes les plateformes."
    services="notification-hubs"
    documentationCenter=""
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/03/2016" 
    ms.author="yuaxu"/>

# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>Envoyer des notifications de disponibilité sur plusieurs plateformes aux utilisateurs avec Hubs de Notification


Dans le didacticiel précédent [Avertir utilisateurs avec Hubs de Notification], vous avez appris à notifications de transmission à tous les périphériques enregistrés par un utilisateur authentifié spécifique. Dans ce didacticiel, plusieurs requêtes étaient requis pour envoyer une notification à chaque plate-forme client pris en charge. Notification Hubs prend en charge les modèles, qui vous permettent de spécifier comment un périphérique spécifique souhaite recevoir des notifications. Cela simplifie l’envoi des notifications de disponibilité sur plusieurs plateformes. Cette rubrique montre comment tirer parti des modèles à envoyer, dans une seule demande, une notification indépendant de la plateforme qui cible toutes les plateformes. Pour plus d’informations sur les modèles, voir [Vue d’ensemble des Hubs Azure Notification][Templates].

> [AZURE.NOTE] Notification Hubs permet à un périphérique d’enregistrer plusieurs modèles avec la même balise. Dans ce cas, un message entrant ciblage cette balise entraîne plusieurs notifications remies à l’appareil, un pour chaque modèle. Cela vous permet d’afficher le même message dans plusieurs notifications visuelles, telles que les deux comme un badge et une notification toast dans une application du Windows Store.

Procédez comme suit pour envoyer des notifications de disponibilité sur plusieurs plateformes à l’aide de modèles :

1. Dans l’Explorateur de solutions dans Visual Studio, développez le dossier **Controllers** , puis ouvrez le fichier RegisterController.cs.

2. Localisez le bloc de code dans la méthode **Post** qui crée une nouvelle remplacer l’enregistrement le `switch` contenu par le code suivant :

        switch (deviceUpdate.Platform)
        {
            case "mpns":
                var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                    "<wp:Notification xmlns:wp=\"WPNotification\">" +
                       "<wp:Toast>" +
                            "<wp:Text1>$(message)</wp:Text1>" +
                       "</wp:Toast> " +
                    "</wp:Notification>";
                registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "wns":
                toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
                registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "apns":
                var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
                registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
                break;
            case "gcm":
                var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
                registration = new GcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }

    Ce code appelle la méthode spécifique à la plateforme pour créer un enregistrement du modèle au lieu d’un enregistrement natif. Les enregistrements existants ne doivent pas être modifiées étant donné que les enregistrements de modèle dérivent à partir des enregistrements natives.

3. Dans le contrôleur de **Notifications** , remplacez la méthode **sendNotification** par le code suivant :

        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:" + user;

            var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
            await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);

            return Request.CreateResponse(HttpStatusCode.OK);
        }

    Ce code envoie une notification à toutes les plates-formes en même temps et sans avoir à spécifier une charge native. Notification Hubs crée et offre la charge utile correcte sur chaque appareil avec la valeur fourni _balise_ , comme indiqué dans les modèles enregistrés.

4. Publier à nouveau votre projet principale WebApi.

5. Réexécutez l’application client et vérifiez que l’enregistrement a réussi.

6. (Facultatif) Déploiement de l’application client vers un second périphérique, puis exécutez l’application.

    Notez qu’une notification s’affiche sur chaque appareil.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez terminé ce didacticiel, en savoir plus sur la Notification Hubs et modèles dans les rubriques suivantes :

+ **[Notification Hubs permet d’envoyer des informations de dernière minute]** <br/>Montre un autre scénario pour l’utilisation des modèles

+  **[Vue d’ensemble des Hubs Notification Azure][Templates]**<br/>Rubrique de présentation compte des informations plus détaillées sur les modèles.


<!-- Anchors. -->

<!-- Images. -->




<!-- URLs. -->
[Push to users ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Push to users Mobile Services]: /manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Notification Hubs permet d’envoyer des informations de dernière minute]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[Informer les utilisateurs avec Hubs de Notification]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: http://msdn.microsoft.com/library/windowsazure/jj927172.aspx
