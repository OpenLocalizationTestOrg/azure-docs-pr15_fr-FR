<properties
    pageTitle="Notification Hubs localisées transmettre actualités didacticiel pour iOS"
    description="Découvrez comment utiliser Azure Service Bus Notification Hubs pour envoyer des notifications de news rupture localisés (iOS)."
    services="notification-hubs"
    documentationCenter="ios"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="yuaxu"/>

# <a name="use-notification-hubs-to-send-localized-breaking-news-to-ios-devices"></a>Notification Hubs permet d’envoyer des informations de dernière minute localisés pour les appareils iOS

> [AZURE.SELECTOR]
- [C# du Windows Store](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification)
- [iOS](notification-hubs-ios-xplat-localized-apns-push-notification)


##<a name="overview"></a>Vue d’ensemble

Cette rubrique vous montre comment utiliser la fonctionnalité de [modèles](notification-hubs-templates-cross-platform-push-messages.md) de Azure Notification Hubs pour les notifications de news rupture qui ont été localisées en langue et appareil de diffusion. Dans ce didacticiel, vous commencez avec l’application iOS créée à [l’Utilisation des Hubs de Notification pour envoyer les informations de dernière minute]. Lorsque vous avez terminé, vous ne pourrez pas enregistrer des catégories que qui vous intéresse, spécifiez une langue dans laquelle vous voulez recevoir des notifications et recevoir des notifications de transmission uniquement pour les catégories sélectionnées dans cette langue.


Il existe deux parties à ce scénario :

- application iOS permet aux périphériques pour spécifier une langue et s’abonner à la rupture différentes catégories d’actualités ; clients

- le serveur principal émet les notifications, à l’aide de la **balise** et **modèle** fondements d’Azure Notification Hubs.



##<a name="prerequisites"></a>Conditions préalables

Vous devez déjà avoir effectué le didacticiel [Hubs de Notification utiliser pour envoyer les informations de dernière minute] et dont le code disponible, car ce didacticiel s’appuie directement ce code.

Visual Studio 2012 ou version ultérieure est facultatif.



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

Puis bien sûr qu’appareils enregistrent avec un modèle qui fait référence à la propriété appropriée. Par exemple, une application iOS qui veut inscrire pour obtenir des informations Français enregistre les éléments suivants :

    {
        aps:{
            alert: "$(News_French)"
        }
    }

Les modèles sont une fonctionnalité très puissante que vous pouvez en savoir plus sur dans notre article [modèles](notification-hubs-templates-cross-platform-push-messages.md) .

##<a name="the-app-user-interface"></a>L’interface utilisateur de l’application

Maintenant, nous allons modifier l’application de la résistance à la News que vous avez créé dans la rubrique [Utilisation Notification Hubs pour envoyer les informations de dernière minute] pour envoyer les informations de dernière minute localisés à l’aide de modèles.


Dans votre MainStoryboard_iPhone.storyboard, ajoutez un contrôle segmenté avec les trois langues qui nous prend en charge : anglais, Français et Mandarin.

![][13]

Veillez à ajouter un type de IBOutlet dans votre ViewController.h comme indiqué ci-dessous :

![][14]

##<a name="building-the-ios-app"></a>Création de l’application iOS


1. Dans votre Notification.h ajoutez la méthode *retrieveLocale* et modifier le magasin et s’y abonner méthodes comme indiqué ci-dessous :

        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

        - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

        - (NSSet*) retrieveCategories;

        - (int) retrieveLocale;

    Dans votre Notification.m, modifier la méthode *storeCategoriesAndSubscribe* , en ajoutant le paramètre de paramètres régionaux et le stockage dans les paramètres par défaut de l’utilisateur :

        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
            [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
            [defaults synchronize];

            [self subscribeWithLocale: locale categories:categories completion:completion];
        }

    Modifiez la méthode *s’abonner* pour inclure les paramètres régionaux :

        - (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];

            NSString* localeString;
            switch (locale) {
                case 0:
                    localeString = @"English";
                    break;
                case 1:
                    localeString = @"French";
                    break;
                case 2:
                    localeString = @"Mandarin";
                    break;
            }

            NSString* template = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"$(News_%@)\"},\"inAppMessage\":\"$(News_%@)\"}", localeString, localeString];

            [hub registerTemplateWithDeviceToken:self.deviceToken name:@"localizednewsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
        }

    Remarque : comment nous utilisons maintenant la méthode *registerTemplateWithDeviceToken*, au lieu de *registerNativeWithDeviceToken*. Lors de l’enregistrement d’un modèle, nous avons fournir le modèle json et également un nom pour le modèle (comme notre application souhaiterez peut-être enregistrer différents modèles). Veillez à enregistrer vos catégories sous forme de balises, comme nous voulons veillez à recevoir les notifciations pour obtenir ces informations.

    Ajouter une méthode pour récupérer les paramètres régionaux à partir des paramètres utilisateur par défaut :

        - (int) retrieveLocale {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

            int locale = [defaults integerForKey:@"BreakingNewsLocale"];

            return locale < 0?0:locale;
        }

2. Maintenant que nous avons modifié nos cours Notifications, nous avons pour vous assurer que notre ViewController montre comment utiliser la nouvelle UISegmentControl. Ajoutez la ligne suivante dans la méthode *viewDidLoad* pour vous assurer qu’afficher les paramètres régionaux actuellement sélectionné :

        self.Locale.selectedSegmentIndex = [notifications retrieveLocale];

    Puis, dans votre méthode *vous abonner* , modifiez votre appel à la *storeCategoriesAndSubscribe* à ce qui suit :

        [notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
            if (!error) {
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                      @"Subscribed!" delegate:nil cancelButtonTitle:
                                      @"OK" otherButtonTitles:nil, nil];
                [alert show];
            } else {
                NSLog(@"Error subscribing: %@", error);
            }
        }];

3. Enfin, vous devez mettre à jour de la méthode *didRegisterForRemoteNotificationsWithDeviceToken* dans votre AppDelegate.m, afin que vous pouvez actualiser correctement votre inscription au démarrage de votre application. Modifier votre appel à la méthode *s’abonner* des notifications avec les éléments suivants :

        NSSet* categories = [self.notifications retrieveCategories];
        int locale = [self.notifications retrieveLocale];
        [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];

##<a name="optional-send-localized-template-notifications-from-net-console-app"></a>(facultatif) Envoyer des notifications de modèle localisé à partir de l’application console .NET.

[AZURE.INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]



##<a name="optional-send-localized-template-notifications-from-the-device"></a>(facultatif) Envoyer des notifications de modèle localisé de l’appareil

Si vous n’avez accès à Visual Studio, ou pour simplement tester l’envoi des notifications localisés modèle directement à partir de l’application sur l’appareil.  Vous pouvez simple ajouter les paramètres de modèle localisé à la `SendNotificationRESTAPI` méthode vous définie dans le didacticiel précédent.

        - (void)SendNotificationRESTAPI:(NSString*)categoryTag
        {
            NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
                                     defaultSessionConfiguration] delegate:nil delegateQueue:nil];

            NSString *json;

            // Construct the messages REST endpoint
            NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                               HUBNAME, API_VERSION]];

            // Generated the token to be used in the authorization header.
            NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

            //Create the request to add the template notification message to the hub
            NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
            [request setHTTPMethod:@"POST"];

            // Add the category as a tag
            [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

            // Template notification
            json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\","
                    \"News_English\":\"Breaking %@ News in English : %@\","
                    \"News_French\":\"Breaking %@ News in French : %@\","
                    \"News_Mandarin\":\"Breaking %@ News in Mandarin : %@\","
                    categoryTag, self.notificationMessage.text,
                    categoryTag, self.notificationMessage.text,  // insert English localized news here
                    categoryTag, self.notificationMessage.text,  // insert French localized news here
                    categoryTag, self.notificationMessage.text]; // insert Mandarin localized news here

            // Signify template notification format
            [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

            // JSON Content-Type
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

            //Authenticate the notification message POST request with the SaS token
            [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

            //Add the notification message body
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

            // Send the REST request
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                       completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
               {
               NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                   if (error || httpResponse.statusCode != 200)
                   {
                       NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                   }
                   if (data != NULL)
                   {
                       //xmlParser = [[NSXMLParser alloc] initWithData:data];
                       //[xmlParser setDelegate:self];
                       //[xmlParser parse];
                   }
               }];

            [dataTask resume];
        }




## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation des modèles, voir :

- [Informer les utilisateurs avec Hubs de Notification : ASP.NET]
- [Informer les utilisateurs avec Hubs de Notification : Services mobiles]






<!-- Images. -->

[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png






<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[Notification Hubs permet d’envoyer des informations de dernière minute]: /manage/services/notification-hubs/breaking-news-ios
[Mobile Service]: /develop/mobile/tutorials/get-started
[Informer les utilisateurs avec Hubs de Notification : ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Informer les utilisateurs avec Hubs de Notification : Services mobiles]: /manage/services/notification-hubs/notify-users
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript and HTML]: ../get-started-with-push-js.md

[Windows Developer Preview registration steps for Mobile Services]: ../mobile-services-windows-developer-preview-registration.md
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
