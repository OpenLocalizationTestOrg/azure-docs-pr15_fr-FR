<properties
    pageTitle="Azure Engagement Mobile iOS SDK atteigne intégration | Microsoft Azure"
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

#<a name="how-to-integrate-engagement-reach-on-ios"></a>Comment intégrer Engagement atteindre sur iOS

Vous devez suivre la procédure d’intégration décrite dans [comment intégrer Engagement sur iOS document](mobile-engagement-ios-integrate-engagement.md) avant de suivre ce guide.

Cette documentation nécessite XCode 8. Si vous dépendez vraiment XCode 7 vous pouvez utiliser [iOS Engagement SDK v3.2.4](https://aka.ms/r6oouh). Il existe un problème connu sur cette version précédente lors de l’exécution sur les appareils iOS 10 : notifications système n’étaient pas activées. Pour corriger cela vous devrez implémenter l’API déconseillée `application:didReceiveRemoteNotification:` dans votre application déléguer comme suit :

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [AZURE.IMPORTANT] **Nous ne recommandons pas cette solution de contournement** dans ce comportement peut modifier dans toute mise à niveau de la version à venir iOS (moindres) parce que cette iOS API est déconseillée. Vous devez basculer vers 8 XCode dès que possible.

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Activer votre application de recevoir des Notifications de transmission en mode silencieux

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

##<a name="integration-steps"></a>Étapes d’intégration

### <a name="embed-the-engagement-reach-sdk-into-your-ios-project"></a>Incorporer du Kit de développement atteigne Engagement dans votre projet iOS

-   Ajouter le Kit de développement accessibles dans votre projet Xcode. Dans Xcode, accédez à **projet \> ajouter au projet** et choisissez la `EngagementReach` dossier.

### <a name="modify-your-application-delegate"></a>Modifier votre délégué d’Application

-   En haut de votre fichier d’implémentation, importer le module Engagement atteindre :

        [...]
        #import "AEReachModule.h"

-   Au sein de méthode `applicationDidFinishLaunching:` ou `application:didFinishLaunchingWithOptions:`, créez un module accessibles et passer à la ligne de l’initialisation Engagement existante :

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
          AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
          [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
          [...]

          return YES;
        }

-   Modifier la chaîne **« icon.png »** avec le nom de l’image que vous souhaitez comme votre icône de notification.
-   Si vous voulez utiliser l’option *mettre à jour la valeur badge* dans campagnes marketing accessibles ou si vous souhaitez utiliser push native \<SaaS/portée API/campagne format/natif Push\> campagnes marketing, vous devez laisser la portée module gérer l’icône badge lui-même (il sera automatiquement désactivez le badge de l’application et également réinitialiser la valeur stockée par Engagement chaque fois que l’application est lancée ou foregrounded). Pour cela en ajoutant la ligne suivante après l’initialisation de module accessibles :

        [reach setAutoBadgeEnabled:YES];

-   Si vous souhaitez gérer push de données accessibles, vous devez laisser votre délégué d’Application sont conformes à la `AEReachDataPushDelegate` protocole. Ajoutez la ligne suivante après l’initialisation de module accessibles :

        [reach setDataPushDelegate:self];

-   Puis vous pouvez mettre en œuvre les méthodes `onDataPushStringReceived:` et `onDataPushBase64ReceivedWithDecodedBody:andEncodedBody:` dans votre délégué d’application :

        -(BOOL)didReceiveStringDataPushWithCategory:(NSString*)category body:(NSString*)body
        {
           NSLog(@"String data push message with category <%@> received: %@", category, body);
           return YES;
        }

        -(BOOL)didReceiveBase64DataPushWithCategory:(NSString*)category decodedBody:(NSData *)decodedBody encodedBody:(NSString *)encodedBody
        {
           NSLog(@"Base64 data push message with category <%@> received: %@", category, encodedBody);
           // Do something useful with decodedBody like updating an image view
           return YES;
        }

### <a name="category"></a>Catégorie

Le paramètre category est facultatif lorsque vous créez une campagne Push de données et permet de que vous permettent de filtrer des données pousse. Cette option est utile si vous souhaitez distribuer des différents types de `Base64` données et que vous souhaitez pour identifier leur type avant d’analyser les.

**Votre application est maintenant prête à recevoir et afficher le contenu accessibles !**

##<a name="how-to-receive-announcements-and-polls-at-any-time"></a>Comment faire pour recevoir des annonces et les sondages à tout moment

Engagement peut envoyer des notifications d’accessibles à vos utilisateurs finaux à tout moment à l’aide du Service de Notification Push Apple.

Pour activer cette fonctionnalité, vous devrez préparer votre application pour les notifications push Apple et modifier votre délégué de l’application.

### <a name="prepare-your-application-for-apple-push-notifications"></a>Préparer votre application pour les notifications push Apple

Suivez le guide : [Comment faire pour préparer votre Application pour les Notifications de transmission Apple](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)

### <a name="add-the-necessary-client-code"></a>Ajouter le code de client nécessaire

*À ce stade votre application doit avoir un certificat push Apple enregistré dans frontend Engagement.*

S’il n’est pas déjà fait, vous devez enregistrer votre application de recevoir des notifications push.

* Importer le `User Notification` framework :

        #import <UserNotifications/UserNotifications.h>

* Ajoutez la ligne suivante au démarrage de votre application (en général en `application:didFinishLaunchingWithOptions:`) :

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

Ensuite, vous devez fournir à Engagement le jeton d’appareil renvoyé par les serveurs d’Apple. Cela dans la méthode nommée `application:didRegisterForRemoteNotificationsWithDeviceToken:` dans votre délégué d’application :

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
        [[EngagementAgent shared] registerDeviceToken:deviceToken];
    }

Enfin, vous devez informer le Kit de développement Engagement lorsque votre application reçoit une notification à distance. Pour ce faire, appelez la méthode `applicationDidReceiveRemoteNotification:fetchCompletionHandler:` dans votre délégué d’application :

    - (void)application:(UIApplication*)application didReceiveRemoteNotification:(NSDictionary*)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }

> [AZURE.NOTE] La méthode ci-dessus est présentée dans iOS 7. Si vous ciblez iOS < 7, veillez à mettre en œuvre la méthode `application:didReceiveRemoteNotification:` dans votre délégué de l’application et appelez `applicationDidReceiveRemoteNotification` sur la EngagementAgent en passant nulle au lieu de la `handler` argument :

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [AZURE.IMPORTANT] Par défaut, Engagement communiquer aux contrôles du completionHandler. Si vous voulez répondre manuellement à la `handler` bloquer dans votre code, vous pouvez passer zéro le `handler` argument et contrôler l’achèvement bloquent vous-même. Voir la `UIBackgroundFetchResult` type d’une liste de valeurs possibles.


### <a name="full-example"></a>Exemple complet

Voici un exemple complet de l’intégration :

    #pragma mark -
    #pragma mark Application lifecycle

    - (BOOL)application:(UIApplication*)application didFinishLaunchingWithOptions:(NSDictionary*)launchOptions
    {
      /* Reach module */
      AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
      [reach setAutoBadgeEnabled:YES];

      /* Engagement initialization */
      [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
      [[EngagementAgent shared] setPushDelegate:self];

      /* Views */
      [window addSubview:[tabBarController view]];
      [window makeKeyAndVisible];

      [application registerForRemoteNotificationTypes:UIRemoteNotificationTypeAlert|UIRemoteNotificationTypeBadge|UIRemoteNotificationTypeSound];
      return YES;
    }

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
      [[EngagementAgent shared] registerDeviceToken:deviceToken];
    }

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }

### <a name="if-you-have-your-own-unusernotificationcenterdelegate-implementation"></a>Si vous disposez de votre propre implémentation UNUserNotificationCenterDelegate

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

##<a name="how-to-customize-campaigns"></a>La personnalisation des campagnes marketing

### <a name="notifications"></a>Notifications

Il existe deux types de notifications : notifications système et dans l’application.

Notifications système sont gérées par iOS et ne peut pas être personnalisées.

Notifications dans l’application qui sont faites d’une vue dynamique ajouté à la fenêtre d’application en cours. Cette option est appelée une superposition de notification. Les superpositions de notification sont parfaites pour une intégration rapide car ils ne nécessite pas vous permet de modifier n’importe quel affichage dans votre application.

#### <a name="layout"></a>Mise en page

Pour modifier l’apparence de vos notifications dans l’application, vous pouvez simplement modifier le fichier `AENotificationView.xib` à vos besoins, tant que vous conservez les valeurs des balises et les types des sous-vues existants.

Par défaut, dans l’application notifications sont présentées dans la partie inférieure de l’écran. Si vous préférez pour les afficher dans la partie supérieure de l’écran, modifier fourni `AENotificationView.xib` et modifier la `AutoSizing` propriétés de la vue principale afin de peut être conservé en haut de son superview.

#### <a name="categories"></a>Catégories

Lorsque vous modifiez la disposition fournie, vous modifiez l’apparence de toutes vos notifications. Les catégories permettent de définir différentes apparences ciblées (éventuellement comportements) pour les notifications. Une catégorie peut être spécifiée lorsque vous créez une campagne accessibles. N’oubliez pas que catégories également vous permettent de personnaliser les annonces et les sondages, qui est décrit plus loin dans ce document.

Pour enregistrer un gestionnaire de catégorie pour les notifications, vous devez ajouter un appel une fois que le module accessibles est initialisé.

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerNotifier:myNotifier forCategory:@"my_category"];
    ...

`myNotifier`doit être une instance d’un objet qui est conforme au protocole `AENotifier`.

Vous pouvez implémenter les méthodes de protocole par vous-même ou vous pouvez choisir d’implémenter de nouveau la classe existante `AEDefaultNotifier` qui exécute déjà la plupart du travail.

Par exemple, si vous voulez redéfinir l’affichage de notification pour une catégorie spécifique, vous pouvez suivre cet exemple :

    #import "AEDefaultNotifier.h"
    #import "AENotificationView.h"
    @interface MyNotifier : AEDefaultNotifier
    @end

    @implementation MyNotifier

    -(NSString*)nibNameForCategory:(NSString*)category
    {
      return "MyNotificationView";
    }

    @end

Cet exemple simple de catégorie part du principe que vous avez un fichier nommé `MyNotificationView.xib` dans votre offre application principale. Si la méthode n’est pas en mesure de trouver un correspondant `.xib`, la notification ne s’affichera pas et Engagement génère un message dans la console.

Le fichier Plume fournies doit respecter les règles suivantes :

-   Il doit contenir uniquement un seul affichage.
-   Sous-vues doivent être des mêmes types que ceux à l’intérieur du fichier Plume fournies nommés`AENotificationView.xib`
-   Sous-vues doivent avoir les mêmes balises que ceux à l’intérieur de la fournies fichier Plume nommé`AENotificationView.xib`

> [AZURE.TIP] Copier simplement le fichier Plume fourni, nommé `AENotificationView.xib`et commencé à utiliser à partir de là. Mais en veillant à ne, l’affichage à l’intérieur de ce fichier plume est associé à la classe `AENotificationView`. Ce cours redéfini la méthode `layoutSubViews` à déplacer et redimensionner des ses sous-vues en fonction du contexte. Vous souhaiterez peut-être Remplacez-le par un `UIView` ou vous classe affichage personnalisé.

Si vous avez besoin plus approfondie personnalisation des notifications de (si vous voulez par exemple charger votre vue directement à partir du code), il est recommandé de consultez la documentation de code et classe source fournie de `Protocol ReferencesDefaultNotifier` et `AENotifier`.

Notez que vous pouvez utiliser le même notificateur pour plusieurs catégories.

Vous pouvez également redéfini le notificateur par défaut comme suit :

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerNotifier:myNotifier forCategory:kAEReachDefaultCategory];

##### <a name="notification-handling"></a>Gestion de la notification

Lorsque vous utilisez la catégorie par défaut, certaines méthodes de cycle de vie sont appelées sur le `AEReachContent` objet pour communiquer les statistiques et mettre à jour de l’état de campagne :

-   La notification qui s’affiche dans l’application, la `displayNotification` méthode est appelée (qui signale les statistiques) par `AEReachModule` si `handleNotification:` renvoie `YES`.
-   Si la notification est fermée, la `exitNotification` méthode est appelée, statistique est signalée et campagnes suivantes peuvent désormais être traités.
-   Si vous cliquez sur la notification, `actionNotification` est appelé statistique est signalée et que l’action associée est effectuée.

Si votre implémentation de `AENotifier` ignore le comportement par défaut, vous avez besoin d’appeler ces méthodes de cycle de vie par vous-même. Les exemples suivants illustrent certains cas où le comportement par défaut est ignoré :

-   Vous ne dépassent pas `AEDefaultNotifier`, par exemple, vous avez implémenté gestion de catégorie à partir de zéro.
-   Vous a substitué `prepareNotificationView:forContent:`, veillez à mapper au moins `onNotificationActioned` ou `onNotificationExited` à un de vos U.I contrôles.

> [AZURE.WARNING] Si `handleNotification:` lève une exception, le contenu est supprimée et `drop` est appelé, cela est signalée dans les statistiques et des campagnes suivantes peuvent désormais être traités.

#### <a name="include-notification-as-part-of-an-existing-view"></a>Inclure la notification dans le cadre d’un affichage existant

Superpositions sont parfaites pour une intégration rapide mais peuvent être parfois pas pratiques ou peuvent avoir des résultats indésirables.

Si vous n’êtes pas satisfait par le système de superposition de certains de vos affichages, vous pouvez le personnaliser pour ces vues.

Vous pouvez décider d’inclure notre disposition notification dans vos affichages existants. Pour cela, il existe deux styles de mise en œuvre :

1.  Ajouter la vue des notifications à l’aide du Générateur d’interface

    -   Ouvrir *Générateur d’Interface*
    -   Placer un 320 x 60 (ou 768 x 60 si vous êtes sur iPad) `UIView` où vous souhaitez que la notification doit apparaître
    -   Définir la valeur de balise pour cet affichage pour : **36822491**

2.  Ajouter la vue des notifications par programme. Ajoutez simplement le code suivant lors de l’initialisation de la vue :

        UIView* notificationView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 320, 60)]; //Replace x and y coordinate values to your needs.
        notificationView.tag = NOTIFICATION_AREA_VIEW_TAG;
        [self.view addSubview:notificationView];

`NOTIFICATION_AREA_VIEW_TAG`macro se trouve dans `AEDefaultNotifier.h`.

> [AZURE.NOTE] L’utilitaire de notification par défaut détecte automatiquement que la mise en page de notification est inclus dans ce mode d’affichage et ajoutera pas une superposition de celui-ci.

### <a name="announcements-and-polls"></a>Annonces et sondages

#### <a name="layouts"></a>Mises en page

Vous pouvez modifier les fichiers `AEDefaultAnnouncementView.xib` et `AEDefaultPollView.xib` tant que vous conservez les valeurs des balises et les types des sous-vues existants.

#### <a name="categories"></a>Catégories

##### <a name="alternate-layouts"></a>Autres mises en page

Telles que les notifications, catégorie de la campagne peut être utilisée pour que les autres mises en page pour vos annonces et les sondages.

Pour créer une catégorie pour une annonce, vous devez étendre **AEAnnouncementViewController** et l’enregistrer une fois que le module portée a été initialisé :

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:@"my_category"];

> [AZURE.NOTE] Chaque fois un utilisateur clique sur une notification pour une annonce de la catégorie « Mes\_catégorie », votre manette affichage inscrits (dans ce cas `MyCustomAnnouncementViewController`) sera initialisé par l’appel de la méthode `initWithAnnouncement:` et l’affichage est ajouté à la fenêtre d’application en cours.

Dans votre implémentation de la `AEAnnouncementViewController` cours vous devrez lire la propriété `announcement` initialisation votre sous-vues. Prenons l’exemple ci-dessous, où deux étiquettes sont initialisés à l’aide de `title` et `body` propriétés de la `AEReachAnnouncement` cours :

    -(void)loadView
    {
        [super loadView];

        UILabel* titleLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
        titleLabel.font = [UIFont systemFontOfSize:32.0];
        titleLabel.text = self.announcement.title;

        UILabel* bodyLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
        bodyLabel.font = [UIFont systemFontOfSize:24.0];
        bodyLabel.text = self.announcement.body;

        [self.view addSubview:titleLabel];
        [self.view addSubview:bodyLabel];
    }

Si vous ne voulez pas charger vos affichages par vous-même mais que vous voulez simplement réutiliser la mise en page de la vue de l’annonce par défaut, vous pouvez simplement rendre votre contrôleur d’affichage personnalisé étend la classe `AEDefaultAnnouncementViewController`. Dupliquer dans ce cas, le fichier Plume `AEDefaultAnnouncementView.xib` et renommez-le afin de pouvoir être chargé par votre contrôleur d’affichage personnalisé (pour un contrôleur nommé `CustomAnnouncementViewController`, vous devez appeler votre fichier Plume `CustomAnnouncementView.xib`).

Pour remplacer la catégorie par défaut des annonces, il enregistre simplement votre contrôleur d’affichage personnalisé pour la catégorie définie dans `kAEReachDefaultCategory`:

    [reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:kAEReachDefaultCategory];

Sondages peuvent être personnalisées de la même façon :

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerPollController:[MyCustomPollViewController class] forCategory:@"my_category"];

Cette fois, à la condition `MyCustomPollViewController` doit étendre `AEPollViewController`. Ou vous pouvez choisir d’étendre à partir du contrôleur par défaut : `AEDefaultPollViewController`.

> [AZURE.IMPORTANT] N’oubliez pas d’appeler des options `action` (`submitAnswers:` pour contrôleurs de la vue personnalisée sondage) ou `exit` méthode avant le contrôleur de la vue est fermé. Dans le cas contraire, statistiques ne seront pas envoyées (c'est-à-dire aucun analytique sur la campagne) et autres campagnes important suivantes ne serez pas avertis jusqu'à ce que le processus d’application est redémarré.

##### <a name="implementation-example"></a>Exemple d’implémentation

Dans cette implémentation de la vue annonce personnalisée est chargée à partir d’un fichier xib externes.

Comme pour la personnalisation avancées de notification, il est recommandé d’examiner le code source de la mise en œuvre standard.

`CustomAnnouncementViewController.h`

    //Interface
    @interface CustomAnnouncementViewController : AEAnnouncementViewController {
      UILabel* titleLabel;
      UITextView* descTextView;
      UIWebView* htmlWebView;
      UIButton* okButton;
      UIButton* cancelButton;
    }

    @property (nonatomic, retain) IBOutlet UILabel* titleLabel;
    @property (nonatomic, retain) IBOutlet UITextView* descTextView;
    @property (nonatomic, retain) IBOutlet UIWebView* htmlWebView;
    @property (nonatomic, retain) IBOutlet UIButton* okButton;
    @property (nonatomic, retain) IBOutlet UIButton* cancelButton;

    -(IBAction)okButtonClicked:(id)sender;
    -(IBAction)cancelButtonClicked:(id)sender;

`CustomAnnouncementViewController.m`

    //Implementation
    @implementation CustomAnnouncementViewController
    @synthesize titleLabel;
    @synthesize descTextView;
    @synthesize htmlWebView;
    @synthesize okButton;
    @synthesize cancelButton;

    -(id)initWithAnnouncement:(AEReachAnnouncement*)anAnnouncement
    {
      self = [super initWithNibName:@"CustomAnnouncementViewController" bundle:nil];
      if (self != nil) {
        self.announcement = anAnnouncement;
      }
      return self;
    }

    - (void) dealloc
    {
      [titleLabel release];
      [descTextView release];
      [htmlWebView release];
      [okButton release];
      [cancelButton release];
      [super dealloc];
    }

    - (void)viewDidLoad {
      [super viewDidLoad];

      /* Init announcement title */
      titleLabel.text = self.announcement.title;

      /* Init announcement body */
      if(self.announcement.type == AEAnnouncementTypeHtml)
      {
        titleLabel.hidden = YES;
        htmlWebView.hidden = NO;
        [htmlWebView loadHTMLString:self.announcement.body baseURL:[NSURL URLWithString:@"http://localhost/"]];
      }
      else
      {
        titleLabel.hidden = NO;
        htmlWebView.hidden = YES;
        descTextView.text = self.announcement.body;
      }

      /* Set action button label */
      if([self.announcement.actionLabel length] > 0)
        [okButton setTitle:self.announcement.actionLabel forState:UIControlStateNormal];

      /* Set exit button label */
      if([self.announcement.exitLabel length] > 0)
        [cancelButton setTitle:self.announcement.exitLabel forState:UIControlStateNormal];
    }

    #pragma mark Actions

    -(IBAction)okButtonClicked:(id)sender
    {
        [self action];
    }

    -(IBAction)cancelButtonClicked:(id)sender
    {
        [self exit];
    }

    @end
