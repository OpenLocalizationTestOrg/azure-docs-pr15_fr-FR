<properties
    pageTitle="Azure Engagement Mobile iOS procédure de mise à niveau SDK | Microsoft Azure"
    description="Dernières mises à jour et procédures pour iOS SDK pour Azure Mobile Engagement"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="piyushjo" />

#<a name="upgrade-procedures"></a>Procédures de mise à niveau

Si vous avez déjà intégré une version antérieure d’Engagement dans votre application, vous devez prendre en compte les points suivants lorsque le Kit de développement de la mise à niveau.

Pour chaque nouvelle version du Kit de développement vous devez tout d’abord remplacer (supprimer et réimporter dans xcode) les dossiers EngagementSDK et EngagementReach.

##<a name="from-300-to-400"></a>À partir de 3.0.0 à 4.0.0

### <a name="xcode-8"></a>XCode 8
8 XCode est obligatoire en commençant par version4.0.0 du Kit de développement.

> [AZURE.NOTE] Si vous dépendez vraiment XCode 7 vous pouvez utiliser [iOS Engagement SDK v3.2.4](https://aka.ms/r6oouh). Il existe un problème connu sur le module portée de cette version précédente lors de l’exécution sur les appareils iOS 10 : notifications système n’étaient pas activées. Pour corriger cela vous devrez implémenter l’API déconseillée `application:didReceiveRemoteNotification:` dans votre application déléguer comme suit :

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [AZURE.IMPORTANT] **Nous ne recommandons pas cette solution de contournement** dans ce comportement peut modifier dans toute mise à niveau de la version à venir iOS (moindres) parce que cette iOS API est déconseillée. Vous devez basculer vers 8 XCode dès que possible.

### <a name="usernotifications-framework"></a>UserNotifications framework
Vous devez ajouter la `UserNotifications` framework dans vos Phases de créer.

dans l’Explorateur de projets, ouvrez le volet de votre projet et sélectionnez la bonne cible. Ensuite, ouvrez l’onglet **« Créer les phases »** et dans le menu **« lien binaire avec bibliothèques »** , ajoutez framework `UserNotifications.framework` -définir le lien en tant que`Optional`

### <a name="application-push-capability"></a>« Pousser » application
8 XCode peuvent réinitialiser votre application transmission fonctionnalité, vérifiez l’à double dans le `capability` onglet de votre cible sélectionné.

### <a name="add-the-new-ios-10-notification-registration-code"></a>Ajoutez le nouveau code de l’enregistrement de notification iOS 10
L’extrait de code plus ancien pour enregistrer l’application aux notifications fonctionne toujours mais utilise API déconseillées lors de l’exécution sur iOS 10.

Importer le `User Notification` framework :

        #import <UserNotifications/UserNotifications.h> 

Dans votre délégué application `application:didFinishLaunchingWithOptions` méthode remplacer :

    if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
        [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
        [application registerForRemoteNotifications];
    }
    else {

        [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
    }

par :

        if (NSFoundationVersionNumber >= NSFoundationVersionNumber_iOS_8_0)
        {
            if (NSFoundationVersionNumber > NSFoundationVersionNumber_iOS_9_x_Max)
            {
                [UNUserNotificationCenter.currentNotificationCenter requestAuthorizationWithOptions:(UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionAlert) completionHandler:^(BOOL granted, NSError * _Nullable error) {}];
            }else
            {
                [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert)   categories:nil]];
            }
            [application registerForRemoteNotifications];
        }
        else
        {
            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }

### <a name="if-you-already-have-your-own-unusernotificationcenterdelegate-implementation"></a>Si vous avez déjà votre propre implémentation UNUserNotificationCenterDelegate

Le Kit de développement est également sa propre implémentation du protocole UNUserNotificationCenterDelegate. Il est utilisé par le Kit de développement pour contrôler le cycle de vie des notifications d’Engagement sur les appareils exécutant sur iOS 10 ou version ultérieure. Si le Kit de développement détecte votre délégué il utilisera pas sa propre implémentation, car il est possible qu’un seul délégué UNUserNotificationCenter par application. Cela signifie que vous devrez ajouter la logique d’Engagement à votre propre délégué.

Il existe deux manières de procéder.

Il vous suffit de votre délégué de transfert d’appels vers le Kit de développement :

    #import <UIKit/UIKit.h>
    #import "EngagementAgent.h"
    #import <UserNotifications/UserNotifications.h>


    @interface MyAppDelegate : NSObject <UIApplicationDelegate, UNUserNotificationCenterDelegate>
    @end

    @implementation MyAppDelegate

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterWillPresentNotification:notification withCompletionHandler:completionHandler]
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterDidReceiveNotificationResponse:response withCompletionHandler:completionHandler]
    }
    @end

Ou en héritant la `AEUserNotificationHandler` classe

    #import "AEUserNotificationHandler.h"
    #import "EngagementAgent.h"

    @interface CustomUserNotificationHandler :AEUserNotificationHandler
    @end

    @implementation CustomUserNotificationHandler

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center willPresentNotification:notification withCompletionHandler:completionHandler];
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse: UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center didReceiveNotificationResponse:response withCompletionHandler:completionHandler];
    }

    @end

> [AZURE.NOTE] Vous pouvez déterminer si une notification vient d’Engagement ou non en passant son `userInfo` dictionnaire à l’Agent `isEngagementPushPayload:` méthode de classe.

##<a name="from-200-to-300"></a>À partir de 2.0.0 à 3.0.0
Supprimée prise en charge pour iOS 4.X. À partir de cette version de la cible du déploiement de votre application doit contenir au moins iOS 6.

Si vous utilisez accessibles dans votre application, vous devez ajouter `remote-notification` valeur à la `UIBackgroundModes` tableau dans votre fichier Info.plist afin de recevoir des notifications à distance.

La méthode `application:didReceiveRemoteNotification:` doit être remplacée par `application:didReceiveRemoteNotification:fetchCompletionHandler:` dans votre délégué d’application.

« AEPushDelegate.h » est déconseillée interface et que vous devez supprimer toutes les références. Cela inclut la suppression `[[EngagementAgent shared] setPushDelegate:self]` et les méthodes de délégué à partir de votre délégué d’application :

    -(void)willRetrieveLaunchMessage;
    -(void)didFailToRetrieveLaunchMessage;
    -(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

##<a name="from-1160-to-200"></a>À partir de 1.16.0 à 2.0.0
Voici comment migrer une intégration SDK à partir du service Capptain offert par Capptain SAS dans une application optimisée par Azure Mobile Engagement.
Si vous effectuez une migration depuis une version antérieure, consultez le site web Capptain pour migrer vers 1.16 tout d’abord, puis appliquez la procédure suivante.

>[AZURE.IMPORTANT] Capptain et Engagement Mobile ne sont pas les mêmes services et la procédure décrite ci-dessous clairs uniquement comment effectuer une migration de l’application client. Migrer le Kit de développement dans l’application ne doit pas migrer vos données à partir des serveurs Capptain aux serveurs Engagement Mobile

### <a name="agent"></a>Agent

La méthode `registerApp:` a été remplacé par la nouvelle méthode `init:`. Votre délégué application doit être mis à jour en conséquence et utiliser la chaîne de connexion :

            - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
            {
              [...]
              [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
              [...]
            }

Suivi SmartAd a été supprimé du Kit de développement logiciel avez simplement supprimer toutes les instances de `AETrackModule` classe

### <a name="class-name-changes"></a>Modifications de nom de classe

Dans le cadre de la redésignation, il existe deux cours/des noms de fichiers qui doivent être modifiées.

Toutes les classes le préfixe « CP » sont renommés avec préfixe « CP ».

Exemple :

-   `CPModule.h`renommé en `AEModule.h`.

Toutes les classes le préfixe « Capptain » sont renommés avec préfixe « Engagement ».

Exemples :

-   La classe `CapptainAgent` est renommé `EngagementAgent`.
-   La classe `CapptainTableViewController` est renommé `EngagementTableViewController`.
-   La classe `CapptainUtils` est renommé `EngagementUtils`.
-   La classe `CapptainViewController` est renommé `EngagementViewController`.
