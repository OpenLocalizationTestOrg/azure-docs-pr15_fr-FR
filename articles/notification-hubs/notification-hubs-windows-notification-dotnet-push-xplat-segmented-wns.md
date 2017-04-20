<properties
    pageTitle="Notification Hubs permet d’envoyer des informations de dernière minute (Windows universel)"
    description="Utilisez Azure Notification Hubs avec des balises dans l’enregistrement pour envoyer des informations de dernière minute à une application Windows universelle."
    services="notification-hubs"
    documentationCenter="windows"
    authors="ysxu"
    manager="erikre"
    editor=""/>


<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="use-notification-hubs-to-send-breaking-news"></a>Notification Hubs permet d’envoyer des informations de dernière minute


[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##<a name="overview"></a>Vue d’ensemble

Cette rubrique vous montre comment utiliser Azure Notification Hubs pour diffuser la rupture des notifications de news à du Windows Store ou l’application Windows Phone 8.1 (non Silverlight). Si vous ciblez Windows Phone 8.1 Silverlight, reportez-vous à la version [De Windows Phone](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md) . Lorsque vous avez terminé, vous pourrez inscrire à la rupture des catégories d’actualités que qui vous intéresse et recevoir des notifications de transmission uniquement pour les catégories. Ce scénario est un modèle commun pour les nombreuses applications où les notifications doivent être envoyées à des groupes d’utilisateurs qui ont précédemment déclarés intérêt qu’ils, par exemple, lecteur RSS, applications pour les passionnés de musique et ainsi de suite. 

Scénarios de diffusion sont activés en incluant une ou plusieurs _indicateurs_ lorsque vous créez un enregistrement dans le hub de notification. Lorsque les notifications sont envoyées à une balise, tous les périphériques qui ont enregistrés pour la balise reçoivent une notification. Étant donné que les balises sont simplement des chaînes, ils n’ont pas à mettre en service à l’avance. Pour plus d’informations sur les balises, reportez-vous à [Notification Hubs routage et Expressions de balise](notification-hubs-tags-segment-push-message.md).

##<a name="prerequisites"></a>Conditions préalables

Cette rubrique s’appuie sur l’application que vous avez créé dans [prise en main avec Hubs de Notification][get-started]. Avant de commencer ce didacticiel, vous devez déjà avoir effectué [prise en main avec Hubs de Notification][get-started].

##<a name="add-category-selection-to-the-app"></a>Ajouter la sélection d’une catégorie à l’application

La première étape consiste à ajouter les éléments d’interface à votre page principale existante qui permettent à l’utilisateur de sélectionner des catégories pour enregistrer. Les catégories sélectionnées par un utilisateur sont stockées sur le périphérique. Lorsque l’application démarre, l’inscription d’une appareil est créée dans votre concentrateur de notification avec les catégories sélectionnées sous forme de balises.

1. Ouvrez le fichier de projet MainPage.xaml, puis copiez le code suivant dans l’élément de **grille** :

        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition/>
                <ColumnDefinition/>
            </Grid.ColumnDefinitions>
            <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="1" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="3" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="2" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="3" Grid.Column="1" HorizontalAlignment="Center"/>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click"/>
        </Grid>


2. Cliquez avec le bouton droit sur le projet **Shared** et ajouter une nouvelle classe nommée **Notifications**, ajouter la touche de modification **public** à la définition de classe, puis ajoutez les instructions suivantes **à l’aide** pour le nouveau fichier de code :

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.Storage;
        using System.Threading.Tasks;

3. Copiez le code suivant dans la nouvelle classe de **Notifications** :

        private NotificationHub hub;

        public Notifications(string hubName, string listenConnectionString)
        {
            hub = new NotificationHub(hubName, listenConnectionString);
        }

        public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            return await SubscribeToCategories(categories);
        }

        public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
            return categories != null ? categories.Split(','): new string[0];
        }

        public async Task<Registration> SubscribeToCategories(IEnumerable<string> categories = null)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            if (categories == null)
            {
                categories = RetrieveCategories();
            }

            // Using a template registration to support notifications across platforms.
            // Any template notifications that contain messageParam and a corresponding tag expression
            // will be delivered for this registration.

            const string templateBodyWNS = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

            return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "simpleWNSTemplateExample",
                    categories);
        }

    Ce cours utilise le stockage local pour stocker les catégories de news cet appareil doit s’afficher. Notez qu’au lieu de l’appel de la méthode *RegisterNativeAsync* nous appeler *RegisterTemplateAsync* pour vous inscrire aux catégories à l’aide de l’inscription d’une modèle. 
    
    Nous avons également fournir un nom pour le modèle (« simpleWNSTemplateExample »), car nous voulons enregistrer plusieurs modèles (par exemple une pour les notifications de toast) et l’autre pour les mosaïques et nous avons besoin de leur attribuer un nom afin de pouvoir mettre à jour ou les supprimer.

    Notez que si un appareil enregistre plusieurs modèles avec la même balise, un ciblage de message entrant qui balise entraîne plusieurs notifications remis à l’appareil (un pour chaque modèle). Ce comportement est utile lorsque le message logique même a pour résultat dans plusieurs notifications visuelles, par exemple montrant un badge et un toast dans une application du Windows Store.

    Pour plus d’informations sur les modèles, voir [modèles](notification-hubs-templates-cross-platform-push-messages.md).




4. Dans le fichier de projet App.xaml.cs, ajoutez la propriété suivante à la classe **d’application** :

        public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");

    Cette propriété est utilisée pour créer et accéder à une instance de **Notifications** .

    Dans le code ci-dessus, remplacez le `<hub name>` et `<connection string with listen access>` espaces réservés avec votre nom de concentrateur de notification et la chaîne de connexion pour *DefaultListenSharedAccessSignature* que vous avez obtenu précédemment.

    > [AZURE.NOTE] Informations d’identification sont fournis avec une application client ne sont pas généralement sécurisées, distribuez uniquement la clé pour l’accès écouter avec votre application client. Écoutez access Active votre application pour vous inscrire à notifications, mais les enregistrements existants ne peuvent pas être modifiée et notifications ne peuvent pas être envoyées. La touche d’accès complet est utilisée dans un service en aval sécurisée pour envoyer des notifications et modifier les enregistrements existants.

5. Dans votre MainPage.xaml.cs, ajoutez la ligne suivante :

        using Windows.UI.Popups;

6. Dans le fichier de projet MainPage.xaml.cs, ajoutez la méthode suivante :

        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
            var categories = new HashSet<string>();
            if (WorldToggle.IsOn) categories.Add("World");
            if (PoliticsToggle.IsOn) categories.Add("Politics");
            if (BusinessToggle.IsOn) categories.Add("Business");
            if (TechnologyToggle.IsOn) categories.Add("Technology");
            if (ScienceToggle.IsOn) categories.Add("Science");
            if (SportsToggle.IsOn) categories.Add("Sports");

            var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

            var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

    Cette méthode crée une liste des catégories et utilise la classe **Notifications** pour stocker la liste dans le stockage local et enregistrer les balises correspondantes avec votre concentrateur de notification. Lorsque les catégories sont modifiées, l’enregistrement est recréé avec les nouvelles catégories.

Votre application est désormais stocker un ensemble de catégories dans le stockage local sur l’appareil et enregistrer avec le hub de notification chaque fois que l’utilisateur change la sélection de catégories.

##<a name="register-for-notifications"></a>S’inscrire pour les notifications

Ces étapes vous inscrire auprès du concentrateur de notification sur démarrage à l’aide de catégories qui ont été stockées dans le stockage local.

> [AZURE.NOTE] Étant donné que le canal URI affecté par le Service de Notification de Windows (WNS) peut modifier à tout moment, vous devez enregistrer pour les notifications fréquemment afin d’éviter les échecs de notification. Cet exemple s’inscrit à notification chaque fois que l’application démarre. Pour les applications qui sont exécutent fréquemment, plusieurs fois par jour, vous pouvez de probablement ignorer l’enregistrement pour préserver la bande passante si l’inscription précédente remonte à moins d’un jour.

1. Ouvrez le fichier App.xaml.cs et mettre à jour de la méthode **InitNotificationsAsync** à utiliser le `notifications` cours pour vous abonner en fonction des catégories.

        // *** Remove or comment out these lines *** 
        //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
        //var hub = new NotificationHub("your hub name", "your listen connection string");
        //var result = await hub.RegisterNativeAsync(channel.Uri);
    
        var result = await notifications.SubscribeToCategories();

    Cela permet de garantir que chaque fois que l’application démarre, il récupère les catégories du stockage local et demande une registeration pour ces catégories. La méthode **InitNotificationsAsync** a été créée dans le cadre de la [prise en main avec Hubs de Notification] [ get-started] didacticiel.

3. Dans le fichier de projet MainPage.xaml.cs, ajoutez le code suivant à la méthode *OnNavigatedTo* :

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            var categories = ((App)Application.Current).notifications.RetrieveCategories();

            if (categories.Contains("World")) WorldToggle.IsOn = true;
            if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
            if (categories.Contains("Business")) BusinessToggle.IsOn = true;
            if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
            if (categories.Contains("Science")) ScienceToggle.IsOn = true;
            if (categories.Contains("Sports")) SportsToggle.IsOn = true;
        }

    Cela met à jour la page principale basée sur l’état des catégories enregistrées précédemment.

L’application est terminée et peut stocker un ensemble de catégories dans le stockage local appareil utilisé pour inscrire dans le hub de notification chaque fois que l’utilisateur change la sélection de catégories. Ensuite, nous allons définir un serveur principal qui peut envoyer des notifications de catégorie pour cette application.

##<a name="sending-tagged-notifications"></a>Envoi des notifications balises

[AZURE.INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

##<a name="run-the-app-and-generate-notifications"></a>Exécutez l’application et générer des notifications

1. Dans Visual Studio, appuyez sur F5 pour compiler et lancer l’application.

    ![][1]

    Notez que l’interface utilisateur de l’application propose un ensemble de bascule qui vous permet de choisir les catégories pour vous abonner à.

2. Activer Active ou désactive une ou plusieurs catégories, puis cliquez sur **s’abonner**.

    L’application convertit les catégories sélectionnées en balises et les demandes d’un nouvel enregistrement d’appareil pour les marqueurs sélectionnés à partir du hub de notification. Les catégories inscrits sont retournés et affichés dans une boîte de dialogue.

    ![][19]

4. Envoyer une notification de nouveau à partir du serveur principal dans une des façons suivantes :

    + **Application console :** lancer l’application console.

    + **Java/PHP :** exécuter votre application/script.

    Notifications pour les catégories sélectionnées s’affichent sous forme de notifications toast.

    ![][14]

##<a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, nous avons appris à diffuser des informations de dernière minute par catégorie. Tenez compte des effectuant l’une des didacticiels suivants qui mettent en surbrillance autres scénarios Hubs Notification avancés :

+ [Permet de diffuser des informations de dernière minute localisés Hubs de Notification]

    Apprenez à développer l’application de news rupture pour activer les notifications localisées envoi.



<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breakingnews-win1.png

[14]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast-2.png


[19]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-reg-2.png

<!-- URLs.-->
[get-started]: /manage/services/notification-hubs/getting-started-windows-dotnet/
[Permet de diffuser des informations de dernière minute localisés Hubs de Notification]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
