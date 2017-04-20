<properties
    pageTitle="Envoyer des notifications de transmission avec Hubs de Notification Azure sur Windows Phone | Microsoft Azure"
    description="Dans ce didacticiel, vous allez apprendre à utiliser Azure Notification Hubs aux notifications push pour une application Windows Phone 8 ou Windows Phone 8.1 Silverlight."
    services="notification-hubs"
    documentationCenter="windows"
    keywords="notification d’émission, de notification, d’émission push de windows phone"
    authors="ysxu"
    manager="erikre"
    editor="erikre"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/03/2016"
    ms.author="yuaxu"/>

# <a name="sending-push-notifications-with-azure-notification-hubs-on-windows-phone"></a>Envoyer des notifications de transmission avec Hubs de Notification Azure sur Windows Phone

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Vue d’ensemble

> [AZURE.NOTE] Pour effectuer ce didacticiel, vous devez disposer d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-phone-get-started%2F).

Ce didacticiel montre comment Azure Notification Hubs permet d’envoyer les notifications push pour une application Windows Phone 8 ou Windows Phone 8.1 Silverlight. Si vous ciblez Windows Phone 8.1 (non Silverlight), puis consultez la version [Windows universel](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) .
Dans ce didacticiel, vous créez une application Windows Phone 8 vierge qui reçoit les notifications push à l’aide du Service de Notification Push Microsoft (MPNS). Lorsque vous avez terminé, vous pourrez utiliser votre concentrateur de notification pour les notifications push pour tous les appareils exécutant votre application de diffusion.

> [AZURE.NOTE] Le Kit de développement de Windows Phone Hubs Notification ne prend pas en charge à l’aide du Service de Notification Push Windows (WNS) avec les applications Windows Phone 8.1 Silverlight. Pour utiliser WNS (au lieu de MPNS) avec les applications Windows Phone 8.1 Silverlight, suivez la [Notification Hubs - didacticiel Windows Phone Silverlight], qui utilise des API REST.

Le didacticiel montre le scénario simple diffusion en utilisant des centraux et de Notification.

##<a name="prerequisites"></a>Conditions préalables

Ce didacticiel requiert les éléments suivants :

+ [Visual Studio 2012 Express pour Windows Phone], ou une version ultérieure.

Fin de ce didacticiel est requis pour tous les autres didacticiels Hubs de Notification pour les applications Windows Phone 8.

##<a name="create-your-notification-hub"></a>Créer votre concentrateur de notification

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li><p>Cliquez sur la section <b>Notification Services</b> (dans les <i>paramètres</i>), cliquez sur <b>Windows Phone (MPNS)</b> , puis sur la case à cocher <b>Activer push non authentifié</b> .</p>
</li>
</ol>

&emsp;&emsp;![Portail Azure - activer les notifications push unathenticated](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

Votre concentrateur est maintenant créé et configuré pour envoyer une notification non authentifiée pour Windows Phone.

> [AZURE.NOTE] Ce didacticiel utilise MPNS en mode non authentifié. Mode non authentifié MPNS est fourni avec des restrictions sur les notifications que vous pouvez envoyer à chaque canal. Notification Hubs prend en charge [MPNS authentifié mode](http://msdn.microsoft.com/library/windowsphone/develop/ff941099.aspx) qui vous permet de télécharger votre certificat.

##<a name="connecting-your-app-to-the-notification-hub"></a>Connectez votre application au hub de notification

1. Dans Visual Studio, créez une nouvelle application Windows Phone 8.

    ![Visual Studio - projet - Windows Phone App][13]

    Dans Visual Studio 2013 mise à jour 2 ou version ultérieure, vous créez à la place une application Silverlight de Windows Phone.

    ![Visual Studio - projet - Blank application - Windows Phone Silverlight][11]

2. Dans Visual Studio, avec le bouton droit de la solution, puis cliquez sur **Gérer les Packages NuGet**.

    Cela permet d’afficher la boîte de dialogue **Gérer les Packages NuGet** .

3. Recherchez `WindowsAzure.Messaging.Managed` et cliquez sur **l’installation**et acceptez les conditions d’utilisation.

    ![Visual Studio - Gestionnaire de Package NuGet][20]

    Cette téléchargements, installations et ajoute une référence à la bibliothèque de messagerie Azure pour Windows en utilisant le <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">package WindowsAzure.Messaging.Managed NuGet</a>.

4. Ouvrez le fichier App.xaml.cs et ajoutez le code suivant `using` instructions :

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;

5. Ajoutez le code suivant en haut de la méthode **Application_Launching** dans App.xaml.cs :

        var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }

        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            var result = await hub.RegisterNativeAsync(args.ChannelUri.ToString());

            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
        });

    >[AZURE.NOTE] La valeur **MyPushChannel** est un index est utilisé pour rechercher un canal existant dans la collection [HttpNotificationChannel](https://msdn.microsoft.com/library/windows/apps/microsoft.phone.notification.httpnotificationchannel.aspx) . Si il n’existe pas il, créez une nouvelle entrée portant le même nom.
    
    Veillez à insérer le nom de votre concentrateur et la chaîne de connexion appelé **DefaultListenSharedAccessSignature** que vous avez obtenu dans la section précédente.
    Ce code extrait l’URI du canal pour l’application à partir de MPNS, puis enregistre ce canal URI avec votre concentrateur de notification. Il également garantit que le canal QU'URI est enregistrée dans votre concentrateur de notification chaque fois que l’application est lancée.

    >[AZURE.NOTE]Ce didacticiel envoie une notification toast à l’appareil. Lorsque vous envoyez une notification de mosaïque, vous devez appeler à la place la méthode **BindToShellTile** sur le canal. Pour prendre en charge les deux toast et notifications avec la vignette, appelez **BindToShellTile** et **BindToShellToast**.

6. Dans l’Explorateur de solutions, développez **Propriétés**, ouvrez le `WMAppManifest.xml` de fichiers, cliquez sur l’onglet **fonctionnalités** et assurez-vous que la fonctionnalité **ID_CAP_PUSH_NOTIFICATION** est activée.

    ![Visual Studio - Windows Phone App fonctionnalités][14]

    Cela garantit que votre application peut recevoir des notifications de transmission. Sinon, toute tentative d’envoyer une notification d’émission à l’application échouera.

7. Appuyez sur la `F5` touche pour exécuter l’application.

    Un message d’enregistrement s’affiche dans l’application.

8. Fermez l’application.  

   >[AZURE.NOTE] Pour recevoir une notification d’émission toast, l’application ne doit pas exécuté en avant-plan.

##<a name="send-push-notifications-from-your-backend"></a>Envoyer les notifications push à partir de votre serveur principal

Vous pouvez envoyer les notifications push à l’aide de Notification Hubs à partir de n’importe quel serveur principal via l' <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interface REST</a>public. Dans ce didacticiel, vous envoyez des notifications push à l’aide d’une application console .NET. 

Pour obtenir un exemple de l’envoi des notifications de transmission à partir d’un serveur principal ASP.NET WebAPI intégré avec Hubs de Notification, voir [Azure Notification Hubs informer les utilisateurs avec le serveur principal .NET](./notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md).  

Pour obtenir un exemple de l’envoi des notifications de transmission à l’aide de l' [API REST](https://msdn.microsoft.com/library/azure/dn223264.aspx), consultez [comment utiliser Hubs de Notification à partir de Java](./notification-hubs-java-push-notification-tutorial.md) et [l’utilisation de Hubs de Notification à partir de PHP](./notification-hubs-php-push-notification-tutorial.md).

1. Avec le bouton droit de la solution, sélectionnez **Ajouter** et **Nouveau projet...**, puis sous **Visual c#**, cliquez sur **Windows** et **Application Console**, puis cliquez sur **OK**.

    ![Application Console - nouveau projet - Visual Studio][6]

    Cette opération ajoute une nouvelle application Visual c# console à la solution. Vous pouvez également le faire dans une solution distincte.

4. Cliquez sur **Outils**, cliquez sur **Gestionnaire de Package de bibliothèque**, puis cliquez sur **Gestionnaire de Package Console**.

    Cela permet d’afficher la Console du Gestionnaire de Package.

5.  Dans la fenêtre **Gestionnaire de Package Console** , la valeur **par défaut de projet** votre nouveau projet d’application console et puis, dans la fenêtre de la console, exécutez la commande suivante :

        Install-Package Microsoft.Azure.NotificationHubs

    Cette opération ajoute une référence à la Notification de Hubs Azure SDK en utilisant le <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">package Microsoft.Azure.Notification Hubs NuGet</a>.

6. Ouvrir le `Program.cs` de fichiers et ajoutez le code suivant `using` instruction :

        using Microsoft.Azure.NotificationHubs;

6. Dans la `Program` de classes, ajoutez la méthode suivante :

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
                .CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            string toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from a .NET App!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
            await hub.SendMpnsNativeNotificationAsync(toast);
        }

    Veillez à remplacer la `<hub name>` espace réservé avec le nom du concentrateur de notification qui s’affiche dans le portail. De même, remplacez l’espace réservé chaîne de connexion avec la chaîne de connexion appelée **DefaultFullSharedAccessSignature** que vous avez obtenu dans la section « Configurer votre concentrateur de notification ».

    >[AZURE.NOTE]Assurez-vous que vous utilisez la chaîne de connexion avec un accès **complet** , **écouter** pas l’accès. La chaîne écouter access n’a pas les autorisations pour envoyer des notifications push.

4. Ajoutez la ligne suivante dans votre `Main` méthode :

         SendNotificationAsync();
         Console.ReadLine();

5. Avec votre émulateur Windows Phone en cours d’exécution et que votre application fermé, définir le projet d’application console comme projet de démarrage par défaut et appuyez sur la `F5` touche pour exécuter l’application.

    Vous recevrez une notification push toast. En cliquant sur la bannière toast du chargement de l’application.

Vous pouvez trouver toutes les charges possibles dans les rubriques [catalogue toast] et [créez une mosaïque catalogue] sur MSDN.

##<a name="next-steps"></a>Étapes suivantes

Dans cet exemple simple, diffusée les notifications push pour tous vos appareils Windows Phone 8. 

Afin de cibler des utilisateurs spécifiques, consultez le didacticiel [Utilisation Notification Hubs aux notifications push pour les utilisateurs] . 

Si vous voulez segmenter vos utilisateurs en groupes d’intérêt, vous pouvez lire [Hubs de Notification utiliser pour envoyer les informations de dernière minute]. 

En savoir plus sur l’utilisation de Notification Hubs dans [Notification Hubs Guide].



<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png





<!-- URLs. -->
[Visual Studio 2012 Express pour Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Conseils de Hubs de notification]: http://msdn.microsoft.com/library/jj927170.aspx
[MPNS authenticated mode]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Utiliser des Hubs de Notification pour les notifications push pour les utilisateurs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Notification Hubs permet d’envoyer des informations de dernière minute]: notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md
[catalogue toast]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[vignette de catalogue]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx
[Notification Hubs - didacticiel Silverlight de Windows Phone]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSLPhoneApp

