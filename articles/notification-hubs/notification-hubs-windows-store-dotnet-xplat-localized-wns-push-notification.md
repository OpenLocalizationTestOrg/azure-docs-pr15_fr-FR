<properties
    pageTitle="Notification Hubs localisées rupture News didacticiel"
    description="Découvrez comment utiliser Azure Notification Hubs pour envoyer des notifications de news rupture localisés."
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

# <a name="use-notification-hubs-to-send-localized-breaking-news"></a>Notification Hubs permet d’envoyer des informations de dernière minute localisés

> [AZURE.SELECTOR]
- [C# du Windows Store](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
- [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

##<a name="overview"></a>Vue d’ensemble

Cette rubrique vous montre comment utiliser la fonctionnalité de **modèle** de Azure Notification Hubs pour les notifications de news rupture qui ont été localisées en langue et appareil de diffusion. Dans ce didacticiel, vous commencez avec l’application Windows Store créée à [l’Utilisation des Hubs de Notification pour envoyer les informations de dernière minute]. Lorsque vous avez terminé, vous ne pourrez pas enregistrer des catégories que qui vous intéresse, spécifiez une langue dans laquelle vous voulez recevoir des notifications et recevoir des notifications de transmission uniquement pour les catégories sélectionnées dans cette langue.


Il existe deux parties à ce scénario :

- l’application Windows Store permet aux périphériques pour spécifier une langue et s’abonner à la rupture différentes catégories d’actualités ; clients

- le serveur principal émet les notifications, à l’aide de la **balise** et **modèle** fondements d’Azure Notification Hubs.



##<a name="prerequisites"></a>Conditions préalables

Vous devez déjà avoir effectué le didacticiel [Hubs de Notification utiliser pour envoyer les informations de dernière minute] et dont le code disponible, car ce didacticiel s’appuie directement ce code.

Vous devez également Visual Studio 2012 ou version ultérieure.


##<a name="template-concepts"></a>Concepts de modèle

Dans [Utiliser Notification Hubs pour envoyer les informations de dernière minute] , vous avez créé une application qui permet de s’abonner à des notifications pour des catégories d’actualités différentes **balises** .
Nombre d’applications, cependant, ciblent plusieurs marchés et requièrent localisation. Cela signifie que le contenu des notifications eux-mêmes doivent être localisées et remis à l’ensemble d’appareils correct.
Dans cette rubrique, nous allons montrer à utiliser la fonctionnalité de **modèle** de Notification Hubs facilement avis rupture localisés news.

Remarque : une des façons d’envoyer des notifications localisées consiste à créer plusieurs versions de chaque étiquette. Par exemple, pour prendre en charge l’anglais, Français et Mandarin, nous devons trois différentes balises pour obtenir des informations du monde : « world_en », « world_fr » et « world_ch ». Il faut envoyer une version localisée de l’actualité mondiale à chacune de ces balises. Dans cette rubrique, nous utilisons modèles pour éviter la prolifération des balises et l’exigence d’envoi de plusieurs messages.

À un niveau élevé, modèles constituent un moyen pour spécifier la manière dont un périphérique spécifique devant recevoir une notification. Le modèle spécifie le format de charge utile exacte en faisant référence à des propriétés qui font partie du message envoyé par votre application principale. Dans notre exemple, nous enverrons un message de paramètres régionaux indépendant contenant des langues prises en charge tous les :

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

Puis bien sûr qu’appareils enregistrent avec un modèle qui fait référence à la propriété appropriée. Par exemple, une application du Windows Store qui souhaite recevoir un message simple toast doit s’inscrire au modèle suivant avec les balises correspondantes :

    <toast>
      <visual>
        <binding template=\"ToastText01\">
          <text id=\"1\">$(News_English)</text>
        </binding>
      </visual>
    </toast>



Les modèles sont une fonctionnalité très puissante que vous pouvez en savoir plus sur dans notre article [modèles](notification-hubs-templates-cross-platform-push-messages.md) . 


##<a name="the-app-user-interface"></a>L’interface utilisateur de l’application

Maintenant, nous allons modifier l’application de la résistance à la News que vous avez créé dans la rubrique [Utilisation Notification Hubs pour envoyer les informations de dernière minute] pour envoyer les informations de dernière minute localisés à l’aide de modèles.

Dans votre application du Windows Store :

Modifier votre MainPage.xaml afin d’inclure une zone de liste déroulante paramètres régionaux :

    <Grid Margin="120, 58, 120, 80"  
            Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top"/>
        <ComboBox Name="Locale" HorizontalAlignment="Left" VerticalAlignment="Center" Width="200" Grid.Row="1" Grid.Column="0">
            <x:String>English</x:String>
            <x:String>French</x:String>
            <x:String>Mandarin</x:String>
        </ComboBox>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="2" Grid.Column="0"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="3" Grid.Column="0"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="4" Grid.Column="0"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="2" Grid.Column="1"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="3" Grid.Column="1"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="4" Grid.Column="1"/>
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
    </Grid>

##<a name="building-the-windows-store-client-app"></a>Création de l’application Windows Store client

1. Dans votre cours Notifications, ajouter un paramètre de paramètres régionaux à vos méthodes *StoreCategoriesAndSubscribe* et *SubscribeToCateories* .

        public async Task<Registration> StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            ApplicationData.Current.LocalSettings.Values["locale"] = locale;
            return await SubscribeToCategories(categories);
        }

        public async Task<Registration> SubscribeToCategories(string locale, IEnumerable<string> categories = null)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            if (categories == null)
            {
                categories = RetrieveCategories();
            }

            // Using a template registration. This makes supporting notifications across other platforms much easier.
            // Using the localized tags based on locale selected.
            string templateBodyWNS = String.Format("<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(News_{0})</text></binding></visual></toast>", locale);

            return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "localizedWNSTemplateExample", categories);
        }

    Notez qu’au lieu de l’appel de la méthode *RegisterNativeAsync* nous appeler *RegisterTemplateAsync*: nous sommes inscription d’un format de notification spécifique dans lequel le modèle varie selon les paramètres régionaux. Nous avons également fournir un nom pour le modèle (« localizedWNSTemplateExample »), car nous voulons enregistrer plusieurs modèles (par exemple une pour les notifications de toast) et l’autre pour les mosaïques et nous avons besoin de leur attribuer un nom afin de pouvoir mettre à jour ou les supprimer.

    Notez que si un appareil enregistre plusieurs modèles avec la même balise, un ciblage de message entrant qui balise entraîne plusieurs notifications remis à l’appareil (un pour chaque modèle). Ce comportement est utile lorsque le message logique même a pour résultat dans plusieurs notifications visuelles, par exemple montrant un badge et un toast dans une application du Windows Store.

2. Ajoutez la méthode suivante pour récupérer les paramètres régionaux stocké :

        public string RetrieveLocale()
        {
            var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
            return locale != null ? locale : "English";
        }

3. Dans votre MainPage.xaml.cs, mettez à jour votre bouton Cliquez sur Gestionnaire en récupérant la valeur actuelle de la zone de liste déroulante de paramètres régionaux et fournir à l’appel à la classe les Notifications, comme indiqué :

        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
            var locale = (string)Locale.SelectedItem;

            var categories = new HashSet<string>();
            if (WorldToggle.IsOn) categories.Add("World");
            if (PoliticsToggle.IsOn) categories.Add("Politics");
            if (BusinessToggle.IsOn) categories.Add("Business");
            if (TechnologyToggle.IsOn) categories.Add("Technology");
            if (ScienceToggle.IsOn) categories.Add("Science");
            if (SportsToggle.IsOn) categories.Add("Sports");

            var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(locale,
                 categories);

            var dialog = new MessageDialog("Locale: " + locale + " Subscribed to: " + 
                string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }


4. Enfin, dans votre fichier App.xaml.cs, veillez à mettre à jour votre `InitNotificationsAsync` méthode pour récupérer les paramètres régionaux et utilisez-le lors de l’abonnement :

        private async void InitNotificationsAsync()
        {
            var result = await notifications.SubscribeToCategories(notifications.RetrieveLocale());

            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }


##<a name="send-localized-notifications-from-your-back-end"></a>Envoyer des notifications localisées à partir de votre principale

[AZURE.INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]






<!-- Anchors. -->
[Template concepts]: #concepts
[The app user interface]: #ui
[Building the Windows Store client app]: #building-client
[Send notifications from your back-end]: #send
[Next Steps]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Notify users with Notification Hubs: Mobile Services]: /manage/services/notification-hubs/notify-users
[Notification Hubs permet d’envoyer des informations de dernière minute]: /manage/services/notification-hubs/breaking-news-dotnet

[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js

[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
