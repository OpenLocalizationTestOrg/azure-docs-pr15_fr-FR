<properties
    pageTitle="Notification Hubs rupture News didacticiel - iOS"
    description="Découvrez comment utiliser Azure Service Bus Notification Hubs pour envoyer la rupture news notifications aux appareils iOS."
    services="notification-hubs"
    documentationCenter="ios"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="use-notification-hubs-to-send-breaking-news"></a>Notification Hubs permet d’envoyer des informations de dernière minute

[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##<a name="overview"></a>Vue d’ensemble

Cette rubrique vous montre comment utiliser Azure Notification Hubs pour diffuser la rupture des notifications de news pour une application iOS. Lorsque vous avez terminé, vous être en mesure d’enregistrer à la rupture des catégories d’actualités que qui vous intéresse et recevoir des notifications de transmission uniquement pour les catégories. Ce scénario est un modèle commun pour les nombreuses applications où les notifications doivent être envoyées à des groupes d’utilisateurs qui ont précédemment déclarés intérêt qu’ils, par exemple, lecteur RSS, applications pour les passionnés de musique, etc..

Scénarios de diffusion sont activés en incluant une ou plusieurs _indicateurs_ lorsque vous créez un enregistrement dans le hub de notification. Lorsque les notifications sont envoyées à une balise, tous les périphériques qui ont enregistrés pour la balise reçoivent une notification. Étant donné que les balises sont simplement des chaînes, ils n’ont pas à mettre en service à l’avance. Pour plus d’informations sur les balises, reportez-vous à [Notification Hubs routage et Expressions de balise](notification-hubs-tags-segment-push-message.md).


##<a name="prerequisites"></a>Conditions préalables

Cette rubrique s’appuie sur l’application que vous avez créé dans [prise en main avec Hubs de Notification][get-started]. Avant de commencer ce didacticiel, vous devez déjà avoir effectué [prise en main avec Hubs de Notification][get-started].

##<a name="add-category-selection-to-the-app"></a>Ajouter la sélection d’une catégorie à l’application

La première étape consiste à ajouter les éléments d’interface à votre table de montage séquentiel existante qui permettent à l’utilisateur de sélectionner les catégories d’enregistrer. Les catégories sélectionnées par un utilisateur sont stockées sur le périphérique. Lorsque l’application démarre, l’inscription d’une appareil est créée dans votre concentrateur de notification avec les catégories sélectionnées sous forme de balises.

1. Dans votre MainStoryboard_iPhone.storyboard ajoutez les éléments suivants à partir de la bibliothèque d’objets :
    + Étiquette avec le texte « Transmettre actualités »,
    + Étiquettes avec textes de catégorie « Monde », « Politique », « Entreprise », « Technologies », « Scientifique », « Sports »,
    + Six commutateurs, un par catégorie, définissez chaque commutateur **état** être **désactivée** par défaut.
    + Un bouton intitulée « S’abonner »

    Votre table de montage séquentiel doit se présenter comme suit :

    ![][3]

2. Dans l’éditeur de l’assistant, créez prises pour tous les commutateurs et appelez-le « WorldSwitch », « PoliticsSwitch », « BusinessSwitch », « TechnologySwitch », « ScienceSwitch », « SportsSwitch »


3. Créer une Action pour votre bouton appelée « s’abonner ». Votre ViewController.h doit contenir les éléments suivants :

        @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

        - (IBAction)subscribe:(id)sender;

4. Créer une nouvelle **Classe tactile cacao** appelée `Notifications`. Copiez le code suivant dans la section de l’interface du fichier Notifications.h :

        @property NSData* deviceToken;

        - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;

        - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
                    completion:(void (^)(NSError* error))completion;

        - (NSSet*)retrieveCategories;

        - (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;

5. Ajoutez la directive import suivantes à Notifications.m :

        #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

6. Copiez le code suivant dans la section implémentation du fichier Notifications.m.

        SBNotificationHub* hub;

        - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName{

            hub = [[SBNotificationHub alloc] initWithConnectionString:listenConnectionString
                                        notificationHubPath:hubName];

            return self;
        }

        - (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];

            [self subscribeWithCategories:categories completion:completion];
        }


        - (NSSet*)retrieveCategories {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

            NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

            if (!categories) return [[NSSet alloc] init];
            return [[NSSet alloc] initWithArray:categories];
        }


        - (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion
        {
           //[hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];

            NSString* templateBodyAPNS = @"{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            [hub registerTemplateWithDeviceToken:self.deviceToken name:@"simpleAPNSTemplate" 
                jsonBodyTemplate:templateBodyAPNS expiryTemplate:@"0" tags:categories completion:completion];
        }



    Ce cours utilise le stockage local pour stocker et récupérer les catégories d’actualités qui recevra cet appareil. En outre, il contient une méthode pour vous inscrire à ces catégories à l’aide de l’inscription d’une [modèle](notification-hubs-templates-cross-platform-push-messages.md) .

7. Dans le fichier AppDelegate.h, ajoutez une instruction import pour Notifications.h et ajouter une propriété pour une instance de la classe Notifications :

        #import "Notifications.h"

        @property (nonatomic) Notifications* notifications;
    

8. Dans la méthode **didFinishLaunchingWithOptions** dans AppDelegate.m, ajoutez le code pour l’initialisation de l’instance de notifications au début de la méthode.  
 
    `HUBNAME`et `HUBLISTENACCESS` (défini dans hubinfo.h) doit déjà être la `<hub name>` et `<connection string with listen access>` espaces réservés remplacé par votre nom de concentrateur de notification et la chaîne de connexion pour *DefaultListenSharedAccessSignature* que vous avez obtenu précédemment

        self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];

    > [AZURE.NOTE] Informations d’identification sont fournis avec une application client ne sont pas généralement sécurisées, distribuez uniquement la clé pour l’accès écouter avec votre application client. Écoutez access Active votre application pour vous inscrire à notifications, mais les enregistrements existants ne peuvent pas être modifiée et notifications ne peuvent pas être envoyées. La touche d’accès complet est utilisée dans un service en aval sécurisée pour envoyer des notifications et modifier les enregistrements existants.


9. Dans la méthode **didRegisterForRemoteNotificationsWithDeviceToken** dans AppDelegate.m, remplacez le code dans la méthode par le code suivant pour passer le jeton appareil à la classe les notifications. La classe notifications effectue l’enregistrement pour les notifications avec les catégories. Si l’utilisateur modifie les sélections de catégorie, nous appelons le `subscribeWithCategories` méthode en réponse au bouton **s’abonner** les mettre à jour.

    > [AZURE.NOTE] Étant donné que le jeton appareil affecté par le Service de Notification Push (Apple APNS) pouvez chance à tout moment, vous devez enregistrer pour les notifications fréquemment afin d’éviter les échecs de notification. Cet exemple s’inscrit à notification chaque fois que l’application démarre. Pour les applications qui sont exécutent fréquemment, plusieurs fois par jour, vous pouvez de probablement ignorer l’enregistrement pour préserver la bande passante si l’inscription précédente remonte à moins d’un jour.

        self.notifications.deviceToken = deviceToken;

        // Retrieves the categories from local storage and requests a registration for these categories
        // each time the app starts and performs a registration.

        NSSet* categories = [self.notifications retrieveCategories];
        [self.notifications subscribeWithCategories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];


    Notez qu’à ce stade ne doit comporter aucun autre code dans la méthode **didRegisterForRemoteNotificationsWithDeviceToken** .

10. Les méthodes suivantes doivent être déjà présents dans AppDelegate.m à partir de la fin de la [prise en main avec Hubs de Notification] [ get-started] didacticiel.  Dans le cas contraire, ajoutez-les.

        -(void)MessageBox:(NSString *)title message:(NSString *)messageText
        {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }

        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
            (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
        }

    Cette méthode gère les notifications reçues lors de l’application est en cours d’exécution en affichant un simple **UIAlert**.

11. Dans ViewController.m, ajoutez une instruction d’importation pour AppDelegate.h et copiez le code suivant dans la méthode généré XCode **s’abonner** . Ce code met à jour l’enregistrement de notification pour utiliser les nouvelles balises de catégorie que l’utilisateur a choisi dans l’interface utilisateur.

        ```
        #import "Notifications.h"
        ```

        NSMutableArray* categories = [[NSMutableArray alloc] init];

        if (self.WorldSwitch.isOn) [categories addObject:@"World"];
        if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
        if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
        if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
        if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
        if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];

        Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

        [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
            if (!error) {
                [(AppDelegate*)[[UIApplication sharedApplication]delegate] MessageBox:@"Notification" message:@"Subscribed!"];
            } else {
                NSLog(@"Error subscribing: %@", error);
            }
        }];

    Cette méthode crée une **NSMutableArray** des catégories et utilise la classe **Notifications** pour stocker la liste dans le stockage local et enregistre les balises correspondantes avec votre concentrateur de notification. Lorsque les catégories sont modifiées, l’enregistrement est recréé avec les nouvelles catégories.

12. Dans ViewController.m, ajoutez le code suivant dans la méthode **viewDidLoad** pour définir l’interface utilisateur en fonction des catégories enregistrées précédemment.


        // This updates the UI on startup based on the status of previously saved categories.

        Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

        NSSet* categories = [notifications retrieveCategories];

        if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
        if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
        if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
        if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
        if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
        if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;



L’application peut désormais stocker un ensemble de catégories dans le stockage local appareil utilisé pour inscrire dans le hub de notification à chaque démarrage de l’application.  L’utilisateur peut modifier la sélection des catégories en cours d’exécution et cliquez sur la méthode pour mettre à jour l’inscription de l’appareil **s’abonner** . Ensuite, vous met à jour l’application pour envoyer les notifications de news rupture directement dans l’application elle-même.


##<a name="optional-sending-tagged-notifications"></a>(facultatif) Envoi des notifications balises

Si vous n’avez pas accès à Visual Studio, vous pouvez passer à la section suivante et envoyer des notifications à partir de l’application elle-même. Vous pouvez également envoyer la notification de modèle approprié à partir du [Portail classique Azure] à l’aide de l’onglet Déboguer pour votre concentrateur de notification. 

[AZURE.INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]


##<a name="optional-send-notifications-from-the-device"></a>(facultatif) Envoyer des notifications à partir de l’appareil

En règle générale, les notifications seraient envoyées par un service en aval mais, vous pouvez envoyer des notifications de news rupture directement à partir de l’application. Pour effectuer cette action nous met à jour la `SendNotificationRESTAPI` méthode que nous avons définie dans la [prise en main avec Hubs de Notification] [ get-started] didacticiel.


1. Dans mise à jour ViewController.m la `SendNotificationRESTAPI` méthode comme suit pour qu’elle accepte un paramètre pour la balise category et envoie la notification approprié [modèle](notification-hubs-templates-cross-platform-push-messages.md) .

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
            json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\"}",
                    categoryTag, self.notificationMessage.text];

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



2. ViewController.m mettre à jour l’action **Envoyer une Notification** comme indiqué dans le code suivant. Afin qu’il sera envoyer les notifications à l’aide de chaque balise individuellement et envoyer à plusieurs plateformes.



        - (IBAction)SendNotificationMessage:(id)sender
        {
            self.sendResults.text = @"";

            NSArray* categories = [NSArray arrayWithObjects: @"World", @"Politics", @"Business",
                                    @"Technology", @"Science", @"Sports", nil];

            // Lets send the message as breaking news for each category to WNS, GCM, and APNS
            // using a template.
            for(NSString* category in categories)
            {
                [self SendNotificationRESTAPI:category];
            }
        }



3. Reconstruire votre projet et vérifiez que vous ne disposez aucune erreur de génération.


##<a name="run-the-app-and-generate-notifications"></a>Exécutez l’application et générer des notifications

1. Appuyez sur le bouton Exécuter pour générer le projet et lancer l’application. Sélectionner des options de news résistance à s’y abonner, puis appuyez sur le bouton **s’abonner** . Vous devriez voir une boîte de dialogue indiquant que les notifications sont abonnées.

    ![][1]

    Lorsque vous cliquez sur **s’abonner**, l’application convertit les catégories sélectionnées en balises et les demandes d’un nouvel enregistrement d’appareil pour les marqueurs sélectionnés à partir du hub de notification.

2. Entrez un message pour être envoyé en tant que les informations de dernière minute, puis appuyez sur le bouton **Envoyer une Notification** . Vous pouvez également exécuter l’application de console .NET pour générer des notifications.

    ![][2]


3. Chaque appareil abonné à dernières actualités recevront les notifications de news la rupture que vous venez d’envoyer.



## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, nous avons appris à diffuser des informations de dernière minute par catégorie. Tenez compte des effectuant l’une des didacticiels suivants qui mettent en surbrillance autres scénarios Hubs Notification avancés :

+ **[Permet de diffuser des informations de dernière minute localisés Hubs de Notification]**

    Apprenez à développer l’application de news rupture pour activer les notifications localisées envoi.





<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png








<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[Permet de diffuser des informations de dernière minute localisés Hubs de Notification]: notification-hubs-ios-xplat-localized-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/dn530749.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[get-started]: /manage/services/notification-hubs/get-started-notification-hubs-ios/
[Portail classique Azure]: https://manage.windowsazure.com
