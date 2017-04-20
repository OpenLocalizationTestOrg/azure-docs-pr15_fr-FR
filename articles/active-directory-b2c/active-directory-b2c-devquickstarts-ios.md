<properties
    pageTitle="Azure B2C annuaire Active : Appeler un API web à partir d’une application iOS à l’aide de bibliothèques tiers | Microsoft Azure"
    description="Cet article vous montrent comment créer une application iOS « liste des tâches » qui appelle un site web Node.js API en utilisant des jetons de porteur OAuth 2.0 à l’aide d’une bibliothèque de tiers"
    services="active-directory-b2c"
    documentationCenter="ios"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

< balises ms.workload="identity ms.service= « active-directory-b2c » « ms.tgt_pltfrm="na » ms.devlang="objectivec « ms.topic= « hero-article »

    ms.date="07/26/2016"
    ms.author="brandwe"/>

# <a name="azure-ad-b2c--call-a-web-api-from-an-ios-application-using-a-third-party-library"></a>Azure AD B2C : Appeler un API web à partir d’une application iOS à l’aide d’une bibliothèque de tiers

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

La plateforme d’identité Microsoft utilise des normes ouvertes telles qu’oauth2 n’et OpenID se connecter. Cela permet aux développeurs de tirer parti de n’importe quelle bibliothèque qu’ils souhaitent intégrer nos services. Pour aider les développeurs à l’aide de notre plate-forme avec d’autres bibliothèques que nous avons écrit quelques procédures pas à pas comme celui-ci pour illustrer comment configurer les bibliothèques de tiers pour vous connecter à la plateforme d’identité de Microsoft. La plupart des bibliothèques qui implémenter [la spécification RFC6749 oauth2 ne](https://tools.ietf.org/html/rfc6749) seront en mesure de vous connecter à la plateforme Microsoft Identity.


Si vous débutez avec oauth2 n’ou OpenID connecter grande partie de cet exemple de configuration ne peut pas peu significatif pour vous. Nous vous recommandons de qu'examiner une rapide [vue d’ensemble du protocole que nous avons présentés ici](active-directory-b2c-reference-protocols.md).

> [AZURE.NOTE]
    Certaines fonctionnalités de notre plateforme qui ont une expression dans ces normes, tels que de gestion des stratégies d’accès conditionnel et Intune requièrent vous permettent d’utiliser notre ouvrir la source de Microsoft Azure identité bibliothèques. 
   
Pas tous les scénarios Azure Active Directory et fonctionnalités sont prises en charge par la plateforme B2C.  Pour déterminer si vous devez utiliser la plateforme B2C, lisez les [limitations B2C](active-directory-b2c-limitations.md).


## <a name="get-an-azure-ad-b2c-directory"></a>Obtenir un répertoire Azure AD B2C

Avant de pouvoir utiliser Azure AD B2C, vous devez créer un répertoire ou du client. Un répertoire est un conteneur pour toutes vos utilisateurs, applications, groupes et plus encore. Si vous n’en avez pas déjà fait, [créez un répertoire B2C](active-directory-b2c-get-started.md) avant continuer.

## <a name="create-an-application"></a>Créer une application

Ensuite, vous devez créer une application dans votre annuaire B2C. Ces informations vous permet d’Azure AD dont il a besoin pour communiquer en toute sécurité avec votre application. L’application et l’API web sont représentées dans ce cas, par un seul **ID de l’Application** , car ils constituent une application logique. Pour créer une application, suivez [ces instructions](active-directory-b2c-app-registration.md). N’oubliez pas :

- Inclure un **appareil mobile** dans l’application.
- Copiez l' **ID de l’Application** qui est affectée à votre application. Vous devez également ceci plus tard.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Créer vos stratégies

Dans Azure AD B2C, chaque expérience utilisateur est définie par une [stratégie](active-directory-b2c-reference-policies.md). Cette application contient une expérience d’identité : un combiné se connecter et d’abonnement. Vous devez créer cette stratégie de chaque type, comme décrit dans l' [article de référence de stratégie](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Lorsque vous créez la stratégie, veillez à :

- Choisissez le **nom d’affichage** et les attributs d’abonnement dans votre stratégie.
- Cliquez sur les revendications application **nom d’affichage** et **l’ID de l’objet** dans chaque stratégie. Vous pouvez choisir d’autres réclamations également.
- Copier le **nom** de chaque stratégie après sa création. Il doit avoir le préfixe `b2c_1_`.  Vous devez le nom de la stratégie ultérieurement.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Après avoir créé vos stratégies, vous êtes prêt à créer votre application.


## <a name="download-the-code"></a>Télécharger le code

Le code de ce didacticiel est conservé [sur GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c).  Pour suivre, vous pouvez [Télécharger l’application comme un fichier zip](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c)/archive/master.zip) ou le cloner :

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

Ou simplement télécharger le code complété et commencer dès maintenant : 

```
git clone --branch complete git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

## <a name="download-the-third-party-library-nxoauth2-and-launch-a-workspace"></a>Télécharger le nxoauth2 bibliothèque tiers et lancer un espace de travail

Pour cette procédure, nous allons utiliser la OAuth2Client de GitHub, une bibliothèque d’oauth2 ne pour Mac OS X & iOS (cacao & cacao tactile). Cette bibliothèque est basée sur brouillon 10 de la spécification oauth2 ne. Il vous mettent en œuvre, le profil application native et prend en charge le point de terminaison de l’autorisation de l’utilisateur final. Il s’agit de tout ce que nous aurons besoin dans l’ordre à développe avec plate-forme Microsoft d’identité.

### <a name="adding-the-library-to-your-project-using-cocoapods"></a>Ajout de la bibliothèque à votre projet à l’aide de CocoaPods

CocoaPods est responsable de dépendance pour les projets Xcode. Il gère automatiquement les étapes d’installation ci-dessus.

```
$ vi Podfile
```
Ajoutez le code suivant cette podfile :

```
 platform :ios, '8.0'
 
 target 'SampleforB2C' do
 
 pod 'NXOAuth2Client'
 
 end
```

Maintenant charger la podfile à l’aide de cocoapods. Cela créera un nouvel espace de travail XCode vous allez charger.

```
$ pod install
...
$ open SampleforB2C.xcworkspace

```

## <a name="the-structure-of-the-project"></a>La structure du projet

Nous avons créé la structure suivante configurer pour notre projet dans la structure :

* **Mode Masque des diapositives** avec un volet Office
* Un **Affichage des tâches ajouter** pour les données relatives à la tâche sélectionnée
* **Mode de connexion** qui permet à un utilisateur de se connecter à l’application.

Nous accédera dans divers fichiers dans le projet pour ajouter l’authentification. D’autres parties du code tel que le code visual n’est pas pertinentes pour identité et sont fournies pour vous.

## <a name="create-the-settingsplist-file-for-your-application"></a>Créer la `settings.plist` fichier de votre application

Il est plus facile à configurer l’application si nous avons un emplacement centralisé pour les valeurs de la configuration. Il vous permet également de comprendre le rôle de chaque paramètre dans votre application. Nous mettons à profit la *Liste des propriétés* permettent de fournir ces valeurs à l’application.

* Créer/ouvrir la `settings.plist` de fichiers sous `Supporting Files` dans votre espace de travail

* Entrez les valeurs suivantes (nous allons étudier les détails bientôt)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>accountIdentifier</key>
    <string>B2C_Acccount</string>
    <key>clientID</key>
    <string><client ID></string>
    <key>clientSecret</key>
    <string></string>
    <key>authURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/authorize?p=<policy name></string>
    <key>loginURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/login</string>
    <key>bhh</key>
    <string>urn:ietf:wg:oauth:2.0:oob</string>
    <key>tokenURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/token?p=<policy name></string>
    <key>keychain</key>
    <string>com.microsoft.azureactivedirectory.samples.graph.QuickStart</string>
    <key>contentType</key>
    <string>application/x-www-form-urlencoded</string>
    <key>taskAPI</key>
    <string>https://aadb2cplayground.azurewebsites.net</string>
</dict>
</plist>
```

Passons dans ces en détail.


Pour `authURL`, `loginURL`, `bhh`, `tokenURL` vous remarquerez que vous avez besoin dans le nom de votre client. C’est le nom de client de votre client B2C qui vous a été affectée. Par exemple, `kidventusb2c.onmicrosoft.com`. Si vous utilisez notre ouvrir la source de Microsoft Azure identité bibliothèques nous serait développé ces données automatiquement à l’aide de notre point de terminaison de métadonnées. Nous avons fait le travail d’extraction de ces valeurs pour vous.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

La `keychain` valeur est le conteneur de la bibliothèque NXOAuth2Client utilisera pour créer un trousseau pour stocker vos jetons. Si vous voulez obtenir l’authentification unique parmi les nombreuses applications que vous pouvez spécifier le trousseau même dans chacune de vos applications ainsi que demander l’utilisation de cette trousseau dans votre entitements XCode. Il est traité dans la documentation d’Apple.

Le `<policy name>` à la fin de chaque URL sont les endroits où vous voulez placer la stratégie que vous avez créé précédemment. L’application appellent ces politiques selon le flux.

La `taskAPI` est le point de terminaison reste nous appelons avec votre jeton B2C soit ajouter des tâches ou requête existante. Cela a été configuré spécialement pour cet exemple. Vous n’avez pas besoin pour le modifier pour l’exemple de fonctionner.

Le reste de ces valeurs sont requises pour utiliser la bibliothèque et créer simplement à utiliser pour réaliser des valeurs au contexte.

Maintenant que nous avons la `settings.plist` fichier créé, nous avons besoin de code à le lire.

## <a name="set-up-a-appdata-class-to-read-our-settings"></a>Configurer une classe AppData pour lire les paramètres de notre

Nous allons créer un fichier simple qui analyse simplement notre `settngs.plist` fichier nous avons créé ci-dessus et apportez à l’avenir ces disque paramètres à n’importe quelle classe. Étant donné que nous ne voulez pas créer une nouvelle copie des données chaque fois que vous invite à fournir un cours de celui-ci, nous utiliser un motif Singleton et retourner uniquement la même instance créée chaque fois qu’une demande est effectuée pour les paramètres

* Créer un `AppData.h` fichier :

```objc
#import <Foundation/Foundation.h>

@interface AppData : NSObject

@property(strong) NSString *accountIdentifier;
@property(strong) NSString *taskApiString;
@property(strong) NSString *authURL;
@property(strong) NSString *clientID;
@property(strong) NSString *loginURL;
@property(strong) NSString *bhh;
@property(strong) NSString *keychain;
@property(strong) NSString *tokenURL;
@property(strong) NSString *clientSecret;
@property(strong) NSString *contentType;

+ (id)getInstance;

@end
```

* Créer un `AppData.m` fichier :

```objc
#import "AppData.h"

@implementation AppData

+ (id)getInstance {
  static AppData *instance = nil;
  static dispatch_once_t onceToken;

  dispatch_once(&onceToken, ^{
    instance = [[self alloc] init];

    NSDictionary *dictionary = [NSDictionary
        dictionaryWithContentsOfFile:[[NSBundle mainBundle]
                                         pathForResource:@"settings"
                                                  ofType:@"plist"]];
    instance.accountIdentifier = [dictionary objectForKey:@"accountIdentifier"];
    instance.clientID = [dictionary objectForKey:@"clientID"];
    instance.clientSecret = [dictionary objectForKey:@"clientSecret"];
    instance.authURL = [dictionary objectForKey:@"authURL"];
    instance.loginURL = [dictionary objectForKey:@"loginURL"];
    instance.bhh = [dictionary objectForKey:@"bhh"];
    instance.tokenURL = [dictionary objectForKey:@"tokenURL"];
    instance.keychain = [dictionary objectForKey:@"keychain"];
    instance.contentType = [dictionary objectForKey:@"contentType"];
    instance.taskApiString = [dictionary objectForKey:@"taskAPI"];

  });

  return instance;
}
@end
```

À présent, nous pouvons accéder facilement à nos données en appelant simplement `  AppData *data = [AppData getInstance];` dans un de nos cours en tant que vous voyez ci-dessous.



## <a name="set-up-the-nxoauth2client-library-in-your-appdelegate"></a>Configurer la bibliothèque NXOAuth2Client dans votre AppDelegate

La bibliothèque NXOAuthClient nécessite certaines valeurs de configuration. Une fois cette opération terminée vous pouvez utiliser le jeton qui est entré d’appeler l’API REST. Étant donné que nous sauront pas que les `AppDelegate` sera appelée tout moment nous charger l’application cela est pertinent pour que nous mettons notre valeurs de configuration dans ce fichier.
* Ouvrir `AppDelegate.m` fichier

* Importer certains fichiers d’en-tête que nous utiliserons ultérieurement.

```objc
#import "NXOAuth2.h" // the Identity library we are using
#import "AppData.h" // the class we just created we will use to load the settings of our application
```

* Ajouter la `setupOAuth2AccountStore` méthode dans l’AppDelegate

Nous avons besoin de créer un AccountStore et placez ensuite les données nous vient de lire dans à partir de la `settings.plist` fichier.

Il existe certains éléments que vous devez tenir compte des relatives à ce stade au service de B2C rendre ce code plus compréhensible :


1. Azure AD B2C utilise la *stratégie* fourni par les paramètres de requête pour votre demande de service. Cela permet d’Azure Active Directory pour l’utiliser comme un service indépendant simplement pour votre application. Pour fournir ces paramètres nous devons fournir supplémentaire de requête à la `kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:` méthode avec nos paramètres de stratégie personnalisée. 

2. Azure AD B2C utilise étendues pratiquement la même façon que d’autres serveurs oauth2 ne. Toutefois puisque l’utilisation de B2C est autant de l’authentification d’un utilisateur lorsqu’ils accèdent aux ressources certaines étendues sont absolument requis afin que le flux de fonctionner correctement. Il s’agit de la `openid` étendue. Notre identité Microsoft SDK fournissent automatiquement la `openid` étendue pour vous afin que vous ne voyez que dans notre configuration SDK. Toutefois, étant donné que nous utilisons une bibliothèque de tiers, nous devons spécifier cette étendue.

```objc
- (void)setupOAuth2AccountStore {
  AppData *data = [AppData getInstance]; // The singleton we use to get the settings

  NSDictionary *customHeaders =
      [NSDictionary dictionaryWithObject:@"application/x-www-form-urlencoded"
                                  forKey:@"Content-Type"];

  // Azure B2C needs
  // kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters for
  // sending policy to the server,
  // therefore we use -setConfiguration:forAccountType:
  NSDictionary *B2cConfigDict = @{
    kNXOAuth2AccountStoreConfigurationClientID : data.clientID,
    kNXOAuth2AccountStoreConfigurationSecret : data.clientSecret,
    kNXOAuth2AccountStoreConfigurationScope :
        [NSSet setWithObjects:@"openid", data.clientID, nil],
    kNXOAuth2AccountStoreConfigurationAuthorizeURL :
        [NSURL URLWithString:data.authURL],
    kNXOAuth2AccountStoreConfigurationTokenURL :
        [NSURL URLWithString:data.tokenURL],
    kNXOAuth2AccountStoreConfigurationRedirectURL :
        [NSURL URLWithString:data.bhh],
    kNXOAuth2AccountStoreConfigurationCustomHeaderFields : customHeaders,
    //      kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:customAuthenticationParameters
  };

  [[NXOAuth2AccountStore sharedStore] setConfiguration:B2cConfigDict
                                        forAccountType:data.accountIdentifier];
}
```
Ensuite, vérifiez que vous appelez cette dernière dans la AppDelegate sous `didFinishLaunchingWithOptions:` méthode. 

```
[self setupOAuth2AccountStore];
```


## <a name="create-a-loginviewcontroller-class-that-we-will-use-to-handle-authentication-requests"></a>Créer un `LoginViewController` classe que nous utiliserons pour gérer les demandes d’authentification

Nous utilisons un mode d’affichage Web pour se connecter compte. Cela permet de demander d’autres facteurs tels que le message texte SMS l’utilisateur (le cas échéant) ou rendez les messages d’erreur à l’utilisateur. Nous allons définir ici vers le haut du mode d’affichage Web, puis écrire ultérieurement le code pour gérer les rappels qui se produiront dans l’affichage Web à partir du Service d’identité de Microsoft.

* Créer un `LoginViewController.h` classe

```objc
@interface LoginViewController : UIViewController <UIWebViewDelegate>
@property(weak, nonatomic) IBOutlet UIWebView *loginView; // Our webview that we will use to do authentication

- (void)handleOAuth2AccessResult:(NSURL *)accessResult; // Allows us to get a token after we've received an Access code.
- (void)setupOAuth2AccountStore; // We will need to add to our OAuth2AccountStore we setup in our AppDelegate
- (void)requestOAuth2Access; // This is where we invoke our webview.
```

Nous allons créer chacune de ces méthodes ci-dessous.

> [AZURE.NOTE] 
    Vérifiez que vous liez le `loginView` à l’affichage Web réel qui se trouve dans votre table de montage séquentiel. Dans le cas contraire, vous n’avez un mode d’affichage Web qui consiste à faire apparaître la lorsqu’il est temps de s’authentifier.

* Créer un `LoginViewController.m` classe

* Ajouter des variables pour transporter état comme nous authentifier

```objc
NSURL *myRequestedUrl; \\ The URL request to Azure Active Directory 
NSURL *myLoadedUrl; \\ The URL loaded for Azure Active Directory
bool loginFlow = FALSE; 
bool isRequestBusy; \\ A way to give status to the thread that the request is still happening
NSURL *authcode; \\ A placeholder for our auth code.
```

* Remplacer les méthodes d’affichage Web pour gérer l’authentification

Nous avons besoin indiquer le mode d’affichage Web le comportement que nous voulons lorsqu’un utilisateur doit se connecter comme indiqué ci-dessus. Vous pouvez simplement coupez et collez le code ci-dessous.

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {
  // We get the auth token from a redirect so we need to handle that in the
  // webview.

  if (![NSThread isMainThread]) {
    [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:)
                           withObject:URL
                        waitUntilDone:YES];
    return;
  }

  NSURLRequest *hostnameURLRequest =
      [NSURLRequest requestWithURL:URL
                       cachePolicy:NSURLRequestUseProtocolCachePolicy
                   timeoutInterval:10.0f];
  isRequestBusy = YES;
  [self.loginView loadRequest:hostnameURLRequest];

  NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)",
        self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView
    shouldStartLoadWithRequest:(NSURLRequest *)request
                navigationType:(UIWebViewNavigationType)navigationType {
  AppData *data = [AppData getInstance];

  NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL,
        (long)navigationType);

  // The webview is where all the communication happens. Slightly complicated.

  myLoadedUrl = [webView.request mainDocumentURL];
  NSLog(@"***Loaded url: %@", myLoadedUrl);

  // if the UIWebView is showing our authorization URL or consent URL, show the
  // UIWebView control
  if ([request.URL.absoluteString rangeOfString:data.authURL
                                        options:NSCaseInsensitiveSearch]
          .location != NSNotFound) {
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.loginURL
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.bhh
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = YES;
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  } else {
    self.loginView.hidden = NO;
    // read the Location from the UIWebView, this is how Microsoft APIs is
    // returning the
    // authentication code and relation information. This is controlled by the
    // redirect URL we chose to use from Microsoft APIs
    // continue the OAuth2 flow
    // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  }

  return YES;
}

```

* Écrire du code pour gérer le résultat de la demande oauth2 ne

Nous avons besoin de code qui gèrera le redirectURL revient à partir de l’affichage Web. Si elle n’a pas réussi, nous allons essayer à nouveau. Pendant ce temps, la bibliothèque fournira l’erreur que vous pouvez voir dans la console ou gérer de façon asynchrone. 

```objc
- (void)handleOAuth2AccessResult:(NSURL *)accessResult {
  // parse the response for success or failure
  if (accessResult)
  // if success, complete the OAuth2 flow by handling the redirect URL and
  // obtaining a token
  {
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
  } else {
    // start over
    [self requestOAuth2Access];
  }
}
```

* Configurer les références de Notification.

Nous créer la même méthode nous l’avons fait dans le `AppDelegate` ci-dessus, mais cette fois, nous allons ajouter certaines `NSNotification`s pour dites-nous ce que neuf dans notre service. Nous allons configurer un observateur qui nous l’indiquera lorsque quelque chose change avec le jeton. Une fois que nous obtenir le jeton nous renvoyer l’utilisateur à la `masterView`.



```objc
- (void)setupOAuth2AccountStore {
  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                if (aNotification.userInfo) {
                  // account added, we have access
                  // we can now request protected data
                  NSLog(@"Success!! We have an access token.");
                  dispatch_async(dispatch_get_main_queue(), ^{

                    MasterViewController *masterViewController =
                        [self.storyboard
                            instantiateViewControllerWithIdentifier:@"master"];
                    [self.navigationController
                        pushViewController:masterViewController
                                  animated:YES];
                  });
                } else {
                  // account removed, we lost access
                }
              }];

  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                NSError *error = [aNotification.userInfo
                    objectForKey:NXOAuth2AccountStoreErrorKey];
                NSLog(@"Error!! %@", error.localizedDescription);
              }];
}

```
* Ajouter du code qui gère l’utilisateur chaque fois qu’une requête est lancée pour se natif

Nous allons créer une méthode qui sera appelée chaque fois que nous avons une demande d’authentification. Il s’agit de la méthode qui crée un affichage en mode Web

```objc
- (void)requestOAuth2Access {
  AppData *data = [AppData getInstance];

  // in order to login to Mircosoft APIs using OAuth2 we must show an embedded
  // browser (UIWebView)
  [[NXOAuth2AccountStore sharedStore]
           requestAccessToAccountWithType:data.accountIdentifier
      withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
        // navigate to the URL returned by NXOAuth2Client

        NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
        [self.loginView loadRequest:r];
      }];
}
```

* Pour finir, nous allons appelez toutes ces méthodes que nous avons écrit au-dessus de chaque fois que le `LoginViewController` charge. Pour ce faire, nous Ajout de ces méthodes à notre `viewDidLoad` permet de méthode Apple

```objc
  [super viewDidLoad];
  // Do any additional setup after loading the view.

  // OAuth2 Code

  self.loginView.delegate = self;
  [self requestOAuth2Access];
  [self setupOAuth2AccountStore];
  NSURLCache *URLCache =
      [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                    diskCapacity:20 * 1024 * 1024
                                        diskPath:nil];
  [NSURLCache setSharedURLCache:URLCache];
```

Vous avez maintenant terminé avec la création de la manière principale, que nous allons interagir avec notre application pour se connecter. Une fois que nous avons connecté, nous devez utiliser notre jetons que nous avons reçu. Pour que nous allons créer un code d’assistance qui appelle API REST pour que nous puissions à l’aide de cette bibliothèque.


## <a name="create-a-graphapicaller-class-to-handle-our-requests-to-a-rest-api"></a>Créer un `GraphAPICaller` cours pour gérer les demandes notre à une API REST

Nous avons une configuration chargée chaque fois que nous charger notre application. Maintenant, nous avons besoin de faire quelque chose avec elle une fois que nous avons un jeton. 

* Créer un `GraphAPICaller.h` fichier

```objc
@interface GraphAPICaller : NSObject <NSURLConnectionDataDelegate>

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock;

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *error))completionBlock;

@end
```

Vous voyez et ce code que nous allons créer deux méthodes : afin d’obtenir les tâches à partir d’une API et une autre pour ajouter des tâches à l’API.

Maintenant que nous avons configuré notre interface, nous allons ajouter l’implémentation réelle :

* Créer un`GraphAPICaller.m file`

```objc
@implementation GraphAPICaller

// 
// Gets the tasks from our REST endpoint we specified in settings
//

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *))completionBlock

{
  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSMutableArray *Tasks = [[NSMutableArray alloc] init];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"GET"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:nil
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (!error) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          if ([dataReturned count] != 0) {

            for (NSMutableDictionary *theTask in dataReturned) {

              Task *t = [[Task alloc] init];
              t.name = [theTask valueForKey:@"Text"];

              [Tasks addObject:t];
            }
          }

          completionBlock(Tasks, nil);
        } else {
          completionBlock(nil, error);
        }

      }];
}

// 
// Adds a task from our REST endpoint we specified in settings
//

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock {

  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSDictionary *params = [self convertParamsToDictionary:task.name];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"POST"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:params
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (responseData) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          completionBlock(TRUE, nil);
        } else {
          completionBlock(FALSE, error);
        }

      }];
}

+ (NSDictionary *)convertParamsToDictionary:(NSString *)task {
  NSMutableDictionary *dictionary = [[NSMutableDictionary alloc] init];

  [dictionary setValue:task forKey:@"Text"];

  return dictionary;
}

@end
```

## <a name="run-the-sample-app"></a>Exécuter l’application d’exemple

Enfin, générez et exécutez l’application dans Xcode. S’inscrire ou se connecter à l’application et créer des tâches pour un utilisateur connecté. Se déconnecter et reconnectez-vous en tant qu’un autre utilisateur et créer des tâches pour cet utilisateur.

Notez que les tâches sont stockées par utilisateur sur l’API, car l’API extrait l’identité de l’utilisateur du jeton d’accès qu’il reçoit.


## <a name="next-steps"></a>Étapes suivantes

Vous pouvez désormais déplacer vers des rubriques B2C plus avancées. Vous pouvez essayer :

[Appeler un site web Node.js API à partir d’une application web Node.js]()

[Personnaliser l’expérience utilisateur pour une application B2C]()
