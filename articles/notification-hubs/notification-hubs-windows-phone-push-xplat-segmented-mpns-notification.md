<properties
    pageTitle="Notification Hubs permet d’envoyer des informations de dernière minute (Windows Phone)"
    description="Utiliser Azure Notification Hubs pour utiliser des balises dans les enregistrements pour envoyer des informations de dernière minute à une application Windows Phone."
    services="notification-hubs"
    documentationCenter="windows"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

# <a name="use-notification-hubs-to-send-breaking-news"></a>Notification Hubs permet d’envoyer des informations de dernière minute

[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##<a name="overview"></a>Vue d’ensemble

Cette rubrique vous montre comment utiliser Azure Notification Hubs pour diffuser la rupture des notifications de news pour une application Windows Phone 8.0/8.1 Silverlight. Si vous ciblez du Windows Store ou l’application Windows Phone 8.1, reportez-vous à la version [Windows universel](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md) . Lorsque vous avez terminé, vous pourrez inscrire à la rupture des catégories d’actualités que qui vous intéresse et recevoir des notifications de transmission uniquement pour les catégories. Ce scénario est un modèle commun pour les nombreuses applications où les notifications doivent être envoyées à des groupes d’utilisateurs qui ont précédemment déclarés intérêt qu’ils, par exemple, lecteur RSS, applications pour les passionnés de musique, etc..

Scénarios de diffusion sont activés en incluant une ou plusieurs _indicateurs_ lorsque vous créez un enregistrement dans le hub de notification. Lorsque les notifications sont envoyées à une balise, tous les périphériques qui ont enregistrés pour la balise reçoivent une notification. Étant donné que les balises sont simplement des chaînes, ils n’ont pas à mettre en service à l’avance. Pour plus d’informations sur les balises, reportez-vous à [Notification Hubs routage et Expressions de balise](notification-hubs-tags-segment-push-message.md).

##<a name="prerequisites"></a>Conditions préalables

Cette rubrique s’appuie sur l’application que vous avez créé dans [prise en main avec Hubs de Notification]. Avant de commencer ce didacticiel, vous devez déjà avoir effectué [prise en main avec Hubs de Notification].

##<a name="add-category-selection-to-the-app"></a>Ajouter la sélection d’une catégorie à l’application

La première étape consiste à ajouter les éléments d’interface à votre page principale existante qui permettent à l’utilisateur de sélectionner des catégories pour enregistrer. Les catégories sélectionnées par un utilisateur sont stockées sur le périphérique. Lorsque l’application démarre, l’inscription d’une appareil est créée dans votre concentrateur de notification avec les catégories sélectionnées sous forme de balises.

1. Ouvrez le fichier de projet MainPage.xaml, puis remplacez les éléments de **grille** nommés `TitlePanel` et `ContentPanel` par le code suivant :

        <StackPanel x:Name="TitlePanel" Grid.Row="0" Margin="12,17,0,28">
            <TextBlock Text="Breaking News" Style="{StaticResource PhoneTextNormalStyle}" Margin="12,0"/>
            <TextBlock Text="Categories" Margin="9,-7,0,0" Style="{StaticResource PhoneTextTitle1Style}"/>
        </StackPanel>

        <Grid Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
            <Grid.RowDefinitions>
                <RowDefinition Height="auto"/>
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition />
                <ColumnDefinition />
            </Grid.ColumnDefinitions>
            <CheckBox Name="WorldCheckBox" Grid.Row="0" Grid.Column="0">World</CheckBox>
            <CheckBox Name="PoliticsCheckBox" Grid.Row="1" Grid.Column="0">Politics</CheckBox>
            <CheckBox Name="BusinessCheckBox" Grid.Row="2" Grid.Column="0">Business</CheckBox>
            <CheckBox Name="TechnologyCheckBox" Grid.Row="0" Grid.Column="1">Technology</CheckBox>
            <CheckBox Name="ScienceCheckBox" Grid.Row="1" Grid.Column="1">Science</CheckBox>
            <CheckBox Name="SportsCheckBox" Grid.Row="2" Grid.Column="1">Sports</CheckBox>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="3" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
        </Grid>

2. Dans le projet, créez une nouvelle classe nommée **Notifications**, ajouter la touche de modification **public** à la définition de classe, puis ajoutez les instructions suivantes **à l’aide de** vers le nouveau fichier de code :

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;
        using System.IO.IsolatedStorage;
        using System.Windows;

3. Copiez le code suivant dans la nouvelle classe de **Notifications** :

        private NotificationHub hub;

        // Registration task to complete registration in the ChannelUriUpdated event handler
        private TaskCompletionSource<Registration> registrationTask;

        public Notifications(string hubName, string listenConnectionString)
        {
            hub = new NotificationHub(hubName, listenConnectionString);
        }

        public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string)IsolatedStorageSettings.ApplicationSettings["categories"];
            return categories != null ? categories.Split(',') : new string[0];
        }

        public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
        {
            var categoriesAsString = string.Join(",", categories);
            var settings = IsolatedStorageSettings.ApplicationSettings;
            if (!settings.Contains("categories"))
            {
                settings.Add("categories", categoriesAsString);
            }
            else
            {
                settings["categories"] = categoriesAsString;
            }
            settings.Save();

            return await SubscribeToCategories();
        }

        public async Task<Registration> SubscribeToCategories()
        {
            registrationTask = new TaskCompletionSource<Registration>();

            var channel = HttpNotificationChannel.Find("MyPushChannel");

            if (channel == null)
            {
                channel = new HttpNotificationChannel("MyPushChannel");
                channel.Open();
                channel.BindToShellToast();
                channel.ChannelUriUpdated += channel_ChannelUriUpdated;

                // This is optional, used to receive notifications while the app is running.
                channel.ShellToastNotificationReceived += channel_ShellToastNotificationReceived;
            }

            // If channel.ChannelUri is not null, we will complete the registrationTask here.  
            // If it is null, the registrationTask will be completed in the ChannelUriUpdated event handler.
            if (channel.ChannelUri != null)
            {
                await RegisterTemplate(channel.ChannelUri);
            }
            
            return await registrationTask.Task;
        }

        async void channel_ChannelUriUpdated(object sender, NotificationChannelUriEventArgs e)
        {
            await RegisterTemplate(e.ChannelUri);
        }

        async Task<Registration> RegisterTemplate(Uri channelUri)
        {
            // Using a template registration to support notifications across platforms.
            // Any template notifications that contain messageParam and a corresponding tag expression
            // will be delivered for this registration.

            const string templateBodyMPNS = "<wp:Notification xmlns:wp=\"WPNotification\">" +
                                                "<wp:Toast>" +
                                                    "<wp:Text1>$(messageParam)</wp:Text1>" +
                                                "</wp:Toast>" +
                                            "</wp:Notification>";

            // The stored categories tags are passed with the template registration.

            registrationTask.SetResult(await hub.RegisterTemplateAsync(channelUri.ToString(), 
                templateBodyMPNS, "simpleMPNSTemplateExample", this.RetrieveCategories()));

            return await registrationTask.Task;
        }

        // This is optional. It is used to receive notifications while the app is running.
        void channel_ShellToastNotificationReceived(object sender, NotificationEventArgs e)
        {
            StringBuilder message = new StringBuilder();
            string relativeUri = string.Empty;

            message.AppendFormat("Received Toast {0}:\n", DateTime.Now.ToShortTimeString());

            // Parse out the information that was part of the message.
            foreach (string key in e.Collection.Keys)
            {
                message.AppendFormat("{0}: {1}\n", key, e.Collection[key]);

                if (string.Compare(
                    key,
                    "wp:Param",
                    System.Globalization.CultureInfo.InvariantCulture,
                    System.Globalization.CompareOptions.IgnoreCase) == 0)
                {
                    relativeUri = e.Collection[key];
                }
            }

            // Display a dialog of all the fields in the toast.
            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() => 
            { 
                MessageBox.Show(message.ToString()); 
            });
        }


    Ce cours utilise le stockage isolé pour stocker les catégories de news cet appareil consiste à recevoir. Elle contient également des méthodes pour vous inscrire à ces catégories à l’aide d’un enregistrement de notification de [modèle](notification-hubs-templates-cross-platform-push-messages.md) .


4. Dans le fichier de projet App.xaml.cs, ajoutez la propriété suivante à la classe **d’application** . Remplacer le `<hub name>` et `<connection string with listen access>` espaces réservés avec votre nom de concentrateur de notification et la chaîne de connexion pour *DefaultListenSharedAccessSignature* que vous avez obtenu précédemment.

        public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");

    > [AZURE.NOTE] Informations d’identification sont fournis avec une application client ne sont pas généralement sécurisées, distribuez uniquement la clé pour l’accès écouter avec votre application client. Écoutez access Active votre application pour vous inscrire à notifications, mais les enregistrements existants ne peuvent pas être modifiée et notifications ne peuvent pas être envoyées. La touche d’accès complet est utilisée dans un service en aval sécurisée pour envoyer des notifications et modifier les enregistrements existants.

5. Dans votre MainPage.xaml.cs, ajoutez la ligne suivante :

        using Windows.UI.Popups;

6. Dans le fichier de projet MainPage.xaml.cs, ajoutez la méthode suivante :

        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
          var categories = new HashSet<string>();
          if (WorldCheckBox.IsChecked == true) categories.Add("World");
          if (PoliticsCheckBox.IsChecked == true) categories.Add("Politics");
          if (BusinessCheckBox.IsChecked == true) categories.Add("Business");
          if (TechnologyCheckBox.IsChecked == true) categories.Add("Technology");
          if (ScienceCheckBox.IsChecked == true) categories.Add("Science");
          if (SportsCheckBox.IsChecked == true) categories.Add("Sports");
    
          var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);
    
          MessageBox.Show("Subscribed to: " + string.Join(",", categories) + " on registration id : " +
             result.RegistrationId);
        }

    Cette méthode crée une liste des catégories et utilise la classe **Notifications** pour stocker la liste dans le stockage local et enregistrer les balises correspondantes avec votre concentrateur de notification. Lorsque les catégories sont modifiées, l’enregistrement est recréé avec les nouvelles catégories.

Votre application est désormais stocker un ensemble de catégories dans le stockage local sur l’appareil et enregistrer avec le hub de notification chaque fois que l’utilisateur change la sélection de catégories.

##<a name="register-for-notifications"></a>S’inscrire pour les notifications

Ces étapes vous inscrire auprès du concentrateur de notification sur démarrage à l’aide de catégories qui ont été stockées dans le stockage local.

> [AZURE.NOTE] Étant donné que le canal URI affecté par Microsoft transmission Notification Service (MPNS) peut modifier à tout moment, vous devez enregistrer pour les notifications fréquemment afin d’éviter les échecs de notification. Cet exemple s’inscrit aux notifications chaque fois que l’application démarre. Pour les applications qui sont exécutent fréquemment, plusieurs fois par jour, vous pouvez de probablement ignorer l’enregistrement pour préserver la bande passante si l’inscription précédente remonte à moins d’un jour.


1. Ouvrez le fichier App.xaml.cs et ajouter la touche de modification **asynchrone** **Application_Launching** méthode et remplacez le code d’inscription Hubs de Notification que vous avez ajouté dans [prise en main avec Hubs de Notification] par le code suivant :

        private async void Application_Launching(object sender, LaunchingEventArgs e)
        {
            var result = await notifications.SubscribeToCategories();

            if (result != null)
                System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
                {
                    MessageBox.Show("Registration Id :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
                });
        }

    Cela permet de garantir que chaque fois que l’application démarre, il récupère les catégories du stockage local et les demandes d’un enregistrement pour ces catégories.

2. Dans le fichier de projet MainPage.xaml.cs, ajoutez le code suivant qui mettent en œuvre, la méthode **OnNavigatedTo** :

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            var categories = ((App)Application.Current).notifications.RetrieveCategories();

            if (categories.Contains("World")) WorldCheckBox.IsChecked = true;
            if (categories.Contains("Politics")) PoliticsCheckBox.IsChecked = true;
            if (categories.Contains("Business")) BusinessCheckBox.IsChecked = true;
            if (categories.Contains("Technology")) TechnologyCheckBox.IsChecked = true;
            if (categories.Contains("Science")) ScienceCheckBox.IsChecked = true;
            if (categories.Contains("Sports")) SportsCheckBox.IsChecked = true;
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

    ![][2]

3. Après avoir reçu une confirmation que vos catégories ont été abonnement terminée, exécutez l’application console pour envoyer des notifications pour chaque catégories. Vérifiez que vous seulement recevez une notification pour les catégories que vous avez souscrit à.

    ![][3]

Vous avez terminé cette rubrique.

<!--##Next steps

In this tutorial we learned how to broadcast breaking news by category. Consider completing one of the following tutorials that highlight other advanced Notification Hubs scenarios:

+ [Use Notification Hubs to broadcast localized breaking news]

    Learn how to expand the breaking news app to enable sending localized notifications.

+ [Notify users with Notification Hubs]

    Learn how to push notifications to specific authenticated users. This is a good solution for sending notifications only to specific users.
-->

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-breakingnews.png
[2]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-registration.png
[3]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-toast.png



<!-- URLs.-->
[Prise en main avec Hubs de Notification]: /manage/services/notification-hubs/get-started-notification-hubs-wp8/
[Use Notification Hubs to broadcast localized breaking news]: ../breakingnews-localized-wp8.md
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users/
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Phone]: ??

