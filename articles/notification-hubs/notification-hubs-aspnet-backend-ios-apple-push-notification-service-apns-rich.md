<properties
    pageTitle="Notification Azure Hubs enrichi Push"
    description="Découvrez comment envoyer les notifications push enrichis pour une application iOS à partir d’Azure. Exemples de code écrits en objectif-C et c#."
    documentationCenter="ios"
    services="notification-hubs"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

#<a name="azure-notification-hubs-rich-push"></a>Notification Azure Hubs enrichi Push


##<a name="overview"></a>Vue d’ensemble

Pour participer à plusieurs utilisateurs avec un contenu enrichi instantané, une application peut souhaiter distribuer au-delà de texte brut. Ces notifications promeuvent interactions utilisateur et présenter un contenu tels que des URL, des sons, des images/coupons et autres éléments. Ce didacticiel s’appuie sur la rubrique [d’Informer les utilisateurs](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) et montre comment envoyer les notifications push qui intègrent charges (par exemple, image).


Ce didacticiel est compatible avec iOS 7 et 8.

  ![][IOS1]

À un niveau élevé :

1. Le système principal de l’application :
    - Stocke la charge utile enrichie (dans ce cas, l’image) dans le stockage de base de données/local principal
    - Envoie des ID de cette notification enrichie à l’appareil
2. Application sur l’appareil :
    - Le système principal demandant la charge utile enrichie avec l’ID qu’il reçoit des contacts
    - Envoie des notifications aux utilisateurs sur le périphérique lors de la récupération des données est terminée et indique la charge utile immédiatement lorsque les utilisateurs appuyez sur pour en savoir plus


## <a name="webapi-project"></a>Projet WebAPI

1. Dans Visual Studio, ouvrez le projet **AppBackend** que vous avez créé dans le didacticiel [Avertir les utilisateurs](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) .
2. Obtenir une image que vous voulez informer les utilisateurs avec et placez-le dans un dossier **img** à votre répertoire de projet.
3. Cliquez sur **Afficher tous les fichiers** dans l’Explorateur de solutions, puis cliquez sur le dossier à **Inclure dans le projet**.
4. Avec l’image sélectionnée, modifier son Action de génération dans la fenêtre Propriétés ressource **Incorporée**.

    ![][IOS2]

5. Dans **Notifications.cs**, ajoutez les éléments suivants à l’aide de déclaration :

        using System.Reflection;

6. Mettre à jour toute la classe **les Notifications** par le code suivant. N’oubliez pas de remplacer les espaces réservés par vos informations d’identification du concentrateur de notification et le nom du fichier image.

        public class Notification {
            public int Id { get; set; }
            // Initial notification message to display to users
            public string Message { get; set; }
            // Type of rich payload (developer-defined)
            public string RichType { get; set; }
            public string Payload { get; set; }
            public bool Read { get; set; }
        }

        public class Notifications {
            public static Notifications Instance = new Notifications();

            private List<Notification> notifications = new List<Notification>();

            public NotificationHubClient Hub { get; set; }

            private Notifications() {
                // Placeholders: replace with the connection string (with full access) for your notification hub and the hub name from the Azure Classics Portal
                Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",  "{hub name}");
            }

            public Notification CreateNotification(string message, string richType, string payload) {
                var notification = new Notification() {
                    Id = notifications.Count,
                    Message = message,
                    RichType = richType,
                    Payload = payload,
                    Read = false
                };

                notifications.Add(notification);

                return notification;
            }

            public Stream ReadImage(int id) {
                var assembly = Assembly.GetExecutingAssembly();
                // Placeholder: image file name (for example, logo.png).
                return assembly.GetManifestResourceStream("AppBackend.img.{logo.png}");
            }
        }

    >[AZURE.NOTE]  (facultatif) Pour plus d’informations sur l’ajout et obtenir des ressources de projet, voir [comment incorporer et accéder aux ressources à l’aide de Visual c#](http://support.microsoft.com/kb/319292) .

7. Dans **NotificationsController.cs**, redéfinir **NotificationsController** avec les extraits suivants. Cela envoie un id de notification riches en mode silencieux initiale au périphérique et permet d’extraction côté client de l’image :

        // Return http response with image binary
        public HttpResponseMessage Get(int id) {
            var stream = Notifications.Instance.ReadImage(id);

            var result = new HttpResponseMessage(HttpStatusCode.OK);
            result.Content = new StreamContent(stream);
            // Switch in your image extension for "png"
            result.Content.Headers.ContentType = new System.Net.Http.Headers.MediaTypeHeaderValue("image/{png}");

            return result;
        }

        // Create rich notification and send initial silent notification (containing id) to client
        public async Task<HttpResponseMessage> Post() {
            // Replace the placeholder with image file name
            var richNotificationInTheBackend = Notifications.Instance.CreateNotification("Check this image out!", "img",  "{logo.png}");

            var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;

            // Silent notification with content available
            var aboutUser = "{\"aps\": {\"content-available\": 1, \"sound\":\"\"}, \"richId\": \"" + richNotificationInTheBackend.Id.ToString() + "\",  \"richMessage\": \"" + richNotificationInTheBackend.Message + "\", \"richType\": \"" + richNotificationInTheBackend.RichType + "\"}";

            // Send notification to apns
            await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(aboutUser, usernameTag);

            return Request.CreateResponse(HttpStatusCode.OK);
        }

8. Maintenant nous sera redéployez cette application vers un site Web Azure afin de pouvoir pour modifier accessibles à partir de tous les périphériques. Avec le bouton droit sur le projet **AppBackend** et sélectionnez **Publier**.

9. Sélectionnez le site Web Azure comme cible à publier. Ouvrez une session avec votre compte Azure et sélectionnez un site Web existant ou nouveau prenez note de la propriété **URL de destination** dans l’onglet **connexion** . Nous fera référence à cette URL en tant que votre *point de terminaison de serveur principal* plus loin dans ce didacticiel. Cliquez sur **Publier**.

## <a name="modify-the-ios-project"></a>Modifier le projet iOS

À présent que vous avez modifié la principale application pour envoyer uniquement l' *id* de la notification, vous devez modifier votre application iOS pour gérer cette id et récupérer le message enrichi à partir de votre serveur principal.

1. Ouvrez votre projet iOS et activer les notifications à distance en accédant à votre cible principale application dans la section **cibles** .

2. Cliquez sur **fonctions**, activer les **Modes de l’arrière-plan**et cochez la case **Notifications à distance** .

    ![][IOS3]

3. Accédez à **Main.storyboard**et vérifiez que vous disposez d’un contrôleur de vue (référence famille contrôleur d’affichage dans ce didacticiel) didacticiel [Notifier l’utilisateur](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) .

4. Ajouter un **Contrôleur de Navigation** à celle-ci, puis sur CTRL + faire glisser pour famille contrôleur d’affichage pour faciliter l' **affichage de la racine** de navigation. Vérifiez que le **Contrôleur d’affichage Initial est** dans l’inspecteur d’attributs est sélectionnée pour le contrôleur de Navigation uniquement.

5. Ajouter un **Contrôleur d’affichage** pour la table de montage séquentiel et ajouter une **Vue de l’Image**. Il s’agit de la page utilisateurs verront une fois qu’ils le souhaitent en savoir plus en cliquant sur la notifiication. Votre table de montage séquentiel doit se présenter comme suit :

    ![][IOS4]

6. Cliquez sur le **Contrôleur de la vue famille** dans la table de montage séquentiel et vérifiez qu’il a **homeViewController** en tant que sa **Classe personnalisée** et **l’ID de la table de montage séquentiel** sous l’inspecteur d’identité.

7. Procédez de même pour contrôleur de la vue Image en tant que **imageViewController**.

8. Ensuite, créez une nouvelle classe contrôleur d’affichage intitulé **imageViewController** pour gérer l’interface utilisateur que vous venez de créer.

9. Dans **imageViewController.h**, ajoutez le code suivant déclarations d’interface du contrôleur. Veillez à CTRL + faire glisser à partir de l’affichage d’image de la table de montage séquentiel à ces propriétés pour lier les deux :

        @property (weak, nonatomic) IBOutlet UIImageView *myImage;
        @property (strong) UIImage* imagePayload;

10. Dans **imageViewController.m**, ajoutez le code suivant à la fin de **viewDidload**:

        // Display the UI Image in UI Image View
        [self.myImage setImage:self.imagePayload];

11. Importer le contrôleur d’image que vous avez créé des **AppDelegate.m**:

        #import "imageViewController.h"

12. Ajouter une section interface avec la déclaration suivante :

        @interface AppDelegate ()

        @property UIImage* imagePayload;
        @property NSDictionary* userInfo;
        @property BOOL iOS8;

        // Obtain content from backend with notification id
        - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion;

        // Redirect to Image View Controller after notification interaction
        - (void)redirectToImageViewWithImage: (UIImage *)img;

        @end

13. Dans **AppDelegate**, vérifiez que votre application s’inscrit aux notifications en mode silencieux dans **application : didFinishLaunchingWithOptions**:

        // Software version
        self.iOS8 = [[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && [[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)];

        // Register for remote notifications for iOS8 and previous versions
        if (self.iOS8) {
            NSLog(@"This device is running with iOS8.");

            // Action
            UIMutableUserNotificationAction *richPushAction = [[UIMutableUserNotificationAction alloc] init];
            richPushAction.identifier = @"richPushMore";
            richPushAction.activationMode = UIUserNotificationActivationModeForeground;
            richPushAction.authenticationRequired = NO;
            richPushAction.title = @"More";

            // Notification category
            UIMutableUserNotificationCategory* richPushCategory = [[UIMutableUserNotificationCategory alloc] init];
            richPushCategory.identifier = @"richPush";
            [richPushCategory setActions:@[richPushAction] forContext:UIUserNotificationActionContextDefault];

            // Notification categories
            NSSet* richPushCategories = [NSSet setWithObjects:richPushCategory, nil];


            UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                                    UIUserNotificationTypeAlert |
                                                    UIUserNotificationTypeBadge
                                                                                     categories:richPushCategories];

            [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
            [[UIApplication sharedApplication] registerForRemoteNotifications];

        }
        else {
            // Previous iOS versions
            NSLog(@"This device is running with iOS7 or earlier versions.");

            [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
        }

        return YES;

14. Remplacez dans l’implémentation suivante pour **l’application : didRegisterForRemoteNotificationsWithDeviceToken** à prendre la table de montage séquentiel interface utilisateur se transforme en compte :

        // Access navigation controller which is at the root of window
        UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
        // Get home view controller from stack on navigation controller
        homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
        hvc.deviceToken = deviceToken;

15. Ensuite, ajoutez les méthodes suivantes pour **AppDelegate.m** récupérer l’image à partir de votre point de terminaison et envoyer une notification locale lorsque l’extraction est terminée. N’oubliez pas de remplacer l’espace réservé `{backend endpoint}` avec votre point de terminaison serveur principal :

        NSString *const GetNotificationEndpoint = @"{backend endpoint}/api/notifications";

        // Helper: retrieve notification content from backend with rich notification id
        - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion {
            UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
            homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
            NSString* authenticationHeader = hvc.registerClient.authenticationHeader;
            // Check if authenticated
            if (!authenticationHeader) return;

            NSURLSession* session = [NSURLSession
                                     sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                     delegate:nil
                                     delegateQueue:nil];

            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, richId]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"GET"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {

                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200) {
                    // From NSData to UIImage
                    self.imagePayload = [UIImage imageWithData:data];

                    completion(nil);
                }
                else {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(error);
                    else {
                        completion([NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }

        // Handle silent push notifications when id is sent from backend
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler {
            self.userInfo = userInfo;
            int richId = [[self.userInfo objectForKey:@"richId"] intValue];
            NSString* richType = [self.userInfo objectForKey:@"richType"];

            // Retrieve image data
            if ([richType isEqualToString:@"img"]) {  
                [self retrieveRichImageWithId:richId completion:^(NSError* error) {
                    if (!error){
                        // Send local notification
                        UILocalNotification* localNotification = [[UILocalNotification alloc] init];

                        // "5" is arbitrary here to give you enough time to quit out of the app and receive push notifications
                        localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:5];
                        localNotification.userInfo = self.userInfo;
                        localNotification.alertBody = [self.userInfo objectForKey:@"richMessage"];
                        localNotification.timeZone = [NSTimeZone defaultTimeZone];

                        // iOS8 categories
                        if (self.iOS8) {
                            localNotification.category = @"richPush";
                        }

                        [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                        handler(UIBackgroundFetchResultNewData);
                    }
                    else{
                        handler(UIBackgroundFetchResultFailed);
                    }
                }];
            }
            // Add "else if" here to handle more types of rich content such as url, sound files, etc.
        }

16. Gérer la notification locale ci-dessus en ouvrant le contrôleur de vue image **AppDelegate.m** avec les méthodes suivantes :

        // Helper: redirect users to image view controller
        - (void)redirectToImageViewWithImage: (UIImage *)img {
            UINavigationController *navigationController = (UINavigationController*) self.window.rootViewController;
            UIStoryboard *mainStoryboard = [UIStoryboard storyboardWithName:@"Main"
                                                                     bundle: nil];
            imageViewController *imgViewController = [mainStoryboard instantiateViewControllerWithIdentifier: @"imageViewController"];
            // Pass data/image to image view controller
            imgViewController.imagePayload = img;

            // Redirect
            [navigationController pushViewController:imgViewController animated:YES];
        }

        // Handle local notification sent above in didReceiveRemoteNotification
        - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification {
            if (application.applicationState == UIApplicationStateActive) {
                // Show in-app alert with an extra "more" button
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:notification.alertBody delegate:self cancelButtonTitle:@"OK" otherButtonTitles:@"More", nil];

                [alert show];
            }
            // App becomes active from user's tap on notification
            else {
                [self redirectToImageViewWithImage:self.imagePayload];
            }
        }

        // Handle buttons in in-app alerts and redirect with data/image
        - (void)alertView:(UIAlertView *)alertView clickedButtonAtIndex:(NSInteger)buttonIndex {
            // Handle "more" button
            if (buttonIndex == 1)
            {
                [self redirectToImageViewWithImage:self.imagePayload];
            }
            // Add "else if" here to handle more buttons
        }

        // Handle notification setting actions in iOS8
        - (void)application:(UIApplication *)application handleActionWithIdentifier:(NSString *)identifier forLocalNotification:(UILocalNotification *)notification completionHandler:(void (^)())completionHandler {
            // Handle richPush related buttons
            if ([identifier isEqualToString:@"richPushMore"]) {
                [self redirectToImageViewWithImage:self.imagePayload];
            }
            completionHandler();
        }

## <a name="run-the-application"></a>Exécuter l’Application

1. Dans XCode, exécutez l’application sur un appareil iOS physique (push notifications ne fonctionnent pas dans le simulator).

2. Dans l’application iOS l’interface utilisateur, entrez un nom d’utilisateur et mot de passe de la même valeur pour l’authentification, puis cliquez sur **Log In**.

3. Cliquez sur **Envoyer push** et vous devriez voir une alerte intégrée dans l’application. Si vous cliquez sur **plus**, vous serez amené à l’image que vous avez choisi à inclure dans votre système d’application principal.

4. Vous pouvez également cliquer sur **Envoyer push** et appuyez immédiatement sur le bouton Accueil de votre appareil. Dans un certain temps, vous recevrez une notification push. Si vous appuyez dessus ou cliquez sur plus, vous serez amené à votre application et le contenu d’image enrichi.


[IOS1]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-1.png
[IOS2]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-2.png
[IOS3]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-3.png
[IOS4]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-4.png
