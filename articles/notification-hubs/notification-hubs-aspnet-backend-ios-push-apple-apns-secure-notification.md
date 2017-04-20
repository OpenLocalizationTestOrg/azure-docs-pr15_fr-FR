<properties
    pageTitle="Notification Azure Hubs sécurisées de transmission"
    description="Découvrez comment envoyer les notifications push sécurisé à une application iOS à partir d’Azure. Exemples de code écrits en objectif-C et c#."
    documentationCenter="ios"
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="ios"
    ms.devlang="objective-c"
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

Ce didacticiel Push Secure montre comment envoyer une notification push en toute sécurité. Le didacticiel s’appuie sur le didacticiel [Avertir les utilisateurs](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) , afin que vous devez effectuer les étapes décrites dans ce didacticiel tout d’abord.

> [AZURE.NOTE] Ce didacticiel suppose que vous avez créé et configuré votre concentrateur de notification comme décrit dans [Prise en main avec Hubs de Notification (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md).

[AZURE.INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>Modifier le projet iOS

À présent que vous avez modifié votre principale application pour envoyer uniquement l' *id* de la notification, vous devez modifier votre application iOS pour gérer cette notification et rappeler votre principale pour récupérer le message sécurisé à afficher.

Pour ce faire, nous devons écrire la logique pour extraire le contenu sécurisé de l’application principale.

1. Dans **AppDelegate.m**, vérifiez que les historiques d’application pour les notifications en mode silencieux donc il traite l’id de notification envoyé à partir du serveur principal. Ajouter l’option **UIRemoteNotificationTypeNewsstandContentAvailability** dans didFinishLaunchingWithOptions :

        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];

2. Dans votre **AppDelegate.m** ajouter une section d’implémentation en haut par la déclaration suivante :

        @interface AppDelegate ()
        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        @end

3. Puis ajoutez le code suivant, en substituant l’espace réservé dans la section implémentation `{back-end endpoint}` avec le point de terminaison pour votre principale obtenu précédemment :

```
        NSString *const GetNotificationEndpoint = @"{back-end endpoint}/api/notifications";

        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        {
            // check if authenticated
            ANHViewController* rvc = (ANHViewController*) self.window.rootViewController;
            NSString* authenticationHeader = rvc.registerClient.authenticationHeader;
            if (!authenticationHeader) return;


            NSURLSession* session = [NSURLSession
                                     sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                     delegate:nil
                                     delegateQueue:nil];


            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, payloadId]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"GET"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200)
                {
                    NSLog(@"Received secure payload: %@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);

                    NSMutableDictionary *json = [NSJSONSerialization JSONObjectWithData:data options: NSJSONReadingMutableContainers error: &error];

                    completion([json objectForKey:@"Payload"], nil);
                }
                else
                {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(nil, error);
                    else {
                        completion(nil, [NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }
```

    This method calls your app back-end to retrieve the notification content using the credentials stored in the shared preferences.

4. Nous avons gérer la notification entrante et utiliser la méthode ci-dessus pour extraire le contenu à afficher. Tout d’abord, nous devons activer votre application iOS exécuter en arrière-plan lorsque vous recevez une notification push. Dans **XCode**, sélectionnez votre projet d’application dans le volet gauche, puis cliquez sur votre cible principale application dans la section **cibles** à partir du volet central.

5. Cliquez sur votre onglet **fonctionnalités** dans la partie supérieure de votre volet central, puis cochez la case **Notifications à distance** .

    ![][IOS1]


6. Dans **AppDelegate.m** ajoutez la méthode suivante pour gérer les notifications push :

        -(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
        {
            NSLog(@"%@", userInfo);

            [self retrieveSecurePayloadWithId:[[userInfo objectForKey:@"secureId"] intValue] completion:^(NSString * payload, NSError *error) {
                if (!error) {
                    // show local notification
                    UILocalNotification* localNotification = [[UILocalNotification alloc] init];
                    localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:0];
                    localNotification.alertBody = payload;
                    localNotification.timeZone = [NSTimeZone defaultTimeZone];
                    [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                    completionHandler(UIBackgroundFetchResultNewData);
                } else {
                    completionHandler(UIBackgroundFetchResultFailed);
                }
            }];

        }

    Notez qu’il est préférable pour gérer les cas de propriété d’en-tête d’authentification manquants ou refus par le serveur principal. La gestion des cas spécifique dépendent principalement votre expérience utilisateur cible. Une option consiste à afficher une notification invitant générique pour l’utilisateur s’authentifier pour récupérer la notification réelle.

## <a name="run-the-application"></a>Exécuter l’Application

Pour exécuter l’application, procédez comme suit :

1. Dans XCode, exécutez l’application sur un appareil iOS physique (push notifications ne fonctionnent pas dans le simulator).

2. Dans l’application iOS l’interface utilisateur, entrez un nom d’utilisateur et mot de passe. Il peut s’agir n’importe quelle chaîne, mais ils doivent avoir la même valeur.

3. Dans l’application iOS l’interface utilisateur, cliquez sur **se connecter**. Cliquez ensuite sur **Envoyer push**. Vous devriez voir la notification sécurisée s’affiche dans votre centre de notification.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
