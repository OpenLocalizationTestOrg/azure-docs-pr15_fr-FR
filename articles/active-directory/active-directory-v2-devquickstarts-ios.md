<properties
    pageTitle="Azure AD version 2.0 l’application iOS | Microsoft Azure"
    description="Découvrez comment créer une application iOS qui se connecte comptes d’utilisateurs avec les deux compte Microsoft personnel et Professionnel ou scolaire à l’aide des bibliothèques tierces."
    services="active-directory"
    documentationCenter=""
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="06/28/2016"
    ms.author="brandwe"/>

# <a name="add-sign-in-to-an-ios-app-using-a-third-party-library-with-graph-api-using-the-v20-endpoint"></a>Ajouter de se connecter à une application iOS à l’aide d’une bibliothèque de tiers avec l’API de graphique à l’aide de point de terminaison de la version 2.0

La plateforme d’identité Microsoft utilise des normes ouvertes telles qu’oauth2 n’et OpenID se connecter. Les développeurs peuvent utiliser n’importe quelle bibliothèque qu’ils souhaitent intégrer à nos services. Pour aider les développeurs à utiliser notre plateforme avec d’autres bibliothèques, nous avons écrit quelques procédures pas à pas comme celui-ci pour montrer comment configurer les bibliothèques de tiers pour vous connecter à la plateforme d’identité de Microsoft. La plupart des bibliothèques qui implémentent [la spécification RFC6749 oauth2 ne](https://tools.ietf.org/html/rfc6749) peuvent vous connecter à la plateforme d’identité de Microsoft.

Avec l’application qui crée de cette procédure pas à pas, les utilisateurs peuvent se connecter à leur organisation et puis rechercher d’autres utilisateurs de leur organisation à l’aide de l’API de graphique.

Si vous débutez avec oauth2 n’ou OpenID se connecter, grande partie de cet exemple de configuration ne peut-être pas sens pour vous. Nous vous recommandons de lire [protocoles version 2.0 - flux de Code OAuth 2.0 d’autorisation](active-directory-v2-protocols-oauth-code.md) pour l’arrière-plan.


> [AZURE.NOTE]
    Certaines fonctionnalités de notre plateforme qui ont une expression dans les normes oauth2 n’ou OpenID se connecter, tels qu’accès conditionnel et gestion des stratégies de Intune requièrent vous permettent d’utiliser notre ouvrir la source de Microsoft Azure identité bibliothèques.

Point de terminaison de la version 2.0 ne reconnaît pas tous les scénarios Azure Active Directory et fonctionnalités.

> [AZURE.NOTE]
    Pour déterminer si vous devez utiliser le point de terminaison version 2.0, lisez les [limitations de la version 2.0](active-directory-v2-limitations.md).

## <a name="download-code-from-github"></a>Télécharger le code de GitHub
Le code de ce didacticiel étant conservée [sur GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-v2).  Pour suivre, vous pouvez [Télécharger structure de l’application comme un fichier zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) ou cloner la structure :

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

Vous pouvez également télécharger l’échantillon et prise en main immédiatement :

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

## <a name="register-an-app"></a>Enregistrer une application
Créer une nouvelle application sur le [portail de l’enregistrement d’Application](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ou suivez les étapes détaillées à [l’enregistrement d’une application avec le point de terminaison version 2.0](active-directory-v2-app-registration.md).  Veillez à :

- Copiez l' **Id de l’Application** qui est affectée à votre application, car vous en aurez besoin plus rapidement.
- Ajouter la plateforme **Mobile** pour votre application.
- Copier l' **URI rediriger** à partir du portail. Vous devez utiliser la valeur par défaut `urn:ietf:wg:oauth:2.0:oob`.


## <a name="download-the-third-party-nxoauth2-library-and-create-a-workspace"></a>Télécharger la bibliothèque NXOAuth2 tiers et créer un espace de travail

Pour cette procédure pas à pas, vous allez utiliser la OAuth2Client de GitHub, ce qui correspond à une bibliothèque d’oauth2 ne pour Mac OS X et iOS (cacao et ses tactile). Cette bibliothèque est basée sur brouillon 10 de la spécification oauth2 ne. Il vous mettent en œuvre, le profil application native et prend en charge le point de terminaison de l’autorisation de l’utilisateur. Il s’agit de toutes les choses que vous devez intégrer la plateforme d’identité de Microsoft.

### <a name="add-the-library-to-your-project-by-using-cocoapods"></a>Ajouter la bibliothèque à votre projet à l’aide de CocoaPods

CocoaPods est responsable de dépendance pour les projets Xcode. Il gère automatiquement les étapes d’installation précédente.

```
$ vi Podfile
```
1. Ajoutez le code suivant cette podfile :

    ```
     platform :ios, '8.0'

     target 'QuickStart' do

     pod 'NXOAuth2Client'

     end
    ```

2. Charger la podfile à l’aide de CocoaPods. Cela créera un nouvel espace de travail Xcode que vous allez charger.

    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

## <a name="explore-the-structure-of-the-project"></a>Explorer la structure du projet

La structure suivante a été configurée pour notre projet dans la structure :

- Affichage masque avec une recherche UPN
- Une vue détaillée pour les données relatives à l’utilisateur sélectionné
- Une vue de connexion où un utilisateur peut se connecter à l’application pour le graphique de la requête

Nous permet de déplacer vers divers fichiers dans la structure pour ajouter l’authentification. D’autres parties du code, telles que le code visual, ne concernent pas identité, mais elles sont fournies pour vous.

## <a name="set-up-the-settingsplst-file-in-the-library"></a>Configurer le fichier settings.plst dans la bibliothèque

-   Dans le projet de démarrage rapide, ouvrez la `settings.plist` fichier. Remplacer les valeurs des éléments dans la section pour refléter les valeurs que vous avez utilisé dans le portail Azure. Votre code font référence ces valeurs chaque fois qu’il utilise la bibliothèque d’authentification Active Directory.
    -   La `clientId` est l’ID de client de votre application que vous avez copiée à partir du portail.
    -   La `redirectUri` est l’URL de redirection que le portail fourni.

## <a name="set-up-the-nxoauth2client-library-in-your-loginviewcontroller"></a>Configurer la bibliothèque NXOAuth2Client dans votre LoginViewController

La bibliothèque NXOAuth2Client nécessite certaines valeurs de configuration. Après avoir effectué cette tâche, vous pouvez utiliser le jeton acquis d’appeler l’API Graph. Étant donné que `LoginView` sera appelé à tout moment nous avons besoin de s’authentifier, il est préférable de placer les valeurs de configuration dans vers ce fichier.

- Nous allons ajouter des valeurs à la `LoginViewController.m` fichier pour définir le contexte d’authentification et d’autorisation. Plus d’informations sur les valeurs suivent le code.

    ```objc
    NSString *scopes = @"openid offline_access User.Read";
    NSString *authURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/authorize";
    NSString *loginURL = @"https://login.microsoftonline.com/common/login";
    NSString *bhh = @"urn:ietf:wg:oauth:2.0:oob?code=";
    NSString *tokenURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/token";
    NSString *keychain = @"com.microsoft.azureactivedirectory.samples.graph.QuickStart";
    static NSString * const kIDMOAuth2SuccessPagePrefix = @"session_state=";
    NSURL *myRequestedUrl;
    NSURL *myLoadedUrl;
    bool loginFlow = FALSE;
    bool isRequestBusy;
    NSURL *authcode;
    ```

Nous allons étudier plus d’informations sur le code.

La première chaîne concerne `scopes`.  La `User.Read` valeur vous permet de lire le profil de base de l’utilisateur connecté.

Vous pouvez en savoir plus sur toutes les étendues disponibles dans des [portées d’autorisation de Microsoft Graph](https://graph.microsoft.io/docs/authorization/permission_scopes).

Pour `authURL`, `loginURL`, `bhh`, et `tokenURL`, vous devez utiliser les valeurs fournies précédemment. Si vous utilisez Microsoft Azure identité bibliothèques open source, nous avons développé ces données pour vous à l’aide de notre point de terminaison de métadonnées. Nous avons fait le travail d’extraction de ces valeurs pour vous.

La `keychain` valeur est le conteneur de la bibliothèque NXOAuth2Client utilisera pour créer un trousseau pour stocker vos jetons. Si vous voulez obtenir authentification unique (SSO) parmi les nombreuses applications, vous pouvez spécifier le trousseau même dans chacune de vos applications et demander l’utilisation de cette trousseau dans vos droits Xcode. Cela est expliqué dans la documentation d’Apple.

Le reste de ces valeurs sont requises pour utiliser la bibliothèque et créer des emplacements pour réaliser des valeurs au contexte.

### <a name="create-a-url-cache"></a>Créer un cache d’URL

À l’intérieur de `(void)viewDidLoad()`, qui est toujours appelée une fois que la vue est chargée, le code suivant attribue à un cache pour nous utilisons.

Ajoutez le code suivant :

```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    self.loginView.delegate = self;
    [self setupOAuth2AccountStore];
    [self requestOAuth2Access];
    NSURLCache *URLCache = [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                                         diskCapacity:20 * 1024 * 1024
                                                             diskPath:nil];
    [NSURLCache setSharedURLCache:URLCache];

}
```

### <a name="create-a-webview-for-sign-in"></a>Créer un mode d’affichage Web pour se connecter

Un mode d’affichage Web peut demander d’autres facteurs tels que le message texte SMS l’utilisateur (le cas échéant) ou renvoyer des messages d’erreur à l’utilisateur. Vous devez définir le mode d’affichage Web vers le haut, puis écrire ultérieurement le code pour gérer les rappels qui se produiront dans l’affichage Web à partir des services d’identité.

```objc
-(void)requestOAuth2Access {
    //to sign in to Microsoft APIs using OAuth2, we must show an embedded browser (UIWebView)
    [[NXOAuth2AccountStore sharedStore] requestAccessToAccountWithType:@"myGraphService"
                                   withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
                                       //navigate to the URL returned by NXOAuth2Client

                                       NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
                                       [self.loginView loadRequest:r];
                                   }];
}
```

### <a name="override-the-webview-methods-to-handle-authentication"></a>Remplacer les méthodes d’affichage Web pour gérer l’authentification

Pour indiquer le mode d’affichage Web que se passe-t-il lorsqu’un utilisateur doit se connecter, comme indiqué précédemment, vous pouvez coller le code suivant.

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {

    // We get the auth token from a redirect so we need to handle that in the webview.

    if (![NSThread isMainThread]) {
        [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:) withObject:URL waitUntilDone:YES];
        return;
    }

    NSURLRequest *hostnameURLRequest = [NSURLRequest requestWithURL:URL cachePolicy:NSURLRequestUseProtocolCachePolicy timeoutInterval:10.0f];
    isRequestBusy = YES;
    [self.loginView loadRequest:hostnameURLRequest];

    NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)", self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType {

    NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL, (long)navigationType);

    // The webview is where all the communication happens. Slightly complicated.

    myLoadedUrl = [webView.request mainDocumentURL];
    NSLog(@"***Loaded url: %@", myLoadedUrl);

    //if the UIWebView is showing our authorization URL or consent URL, show the UIWebView control
    if ([request.URL.absoluteString rangeOfString:authURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:loginURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:bhh options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = YES;
        [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }
    else {
        self.loginView.hidden = NO;
        //read the Location from the UIWebView, this is how Microsoft APIs is returning the
        //authentication code and relation information. This is controlled by the redirect URL we chose to use from Microsoft APIs
        //continue the OAuth2 flow
       // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }

    return YES;

}
```

### <a name="write-code-to-handle-the-result-of-the-oauth2-request"></a>Écrire du code pour gérer le résultat de la demande oauth2 ne

Le code suivant gérera redirectURL qui renvoie l’affichage Web. Si l’authentification n’a pas réussie, le code va réessayer. Pendant ce temps, la bibliothèque fournira l’erreur que vous pouvez voir dans la console ou gérer asynchrone.

```objc
- (void)handleOAuth2AccessResult:(NSString *)accessResult {

    AppData* data = [AppData getInstance];

    //parse the response for success or failure
     if (accessResult)
    //if success, complete the OAuth2 flow by handling the redirect URL and obtaining a token
     {
         [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
    } else {
        //start over
        [self requestOAuth2Access];
    }
}
```

### <a name="set-up-the-oauth-context-called-account-store"></a>Configurer le contexte OAuth (appelé magasin de comptes)

Ici, vous pouvez appeler `-[NXOAuth2AccountStore setClientID:secret:authorizationURL:tokenURL:redirectURL:forAccountType:]` dans le magasin de compte partagé pour chaque service que vous souhaitez que l’application pour accéder à. Le type de compte est une chaîne qui est utilisée comme un identificateur pour un certain service. Étant donné que vous accédez à l’API de graphique, le code fait référence à en tant que `"myGraphService"`. Vous configurez ensuite un observateur qui vous signale quand quelque chose change avec le jeton. Une fois que vous obtenez le jeton, retour l’utilisateur rétablir l’accès à la `masterView`.



```objc
- (void)setupOAuth2AccountStore {


        AppData* data = [AppData getInstance];

    [[NXOAuth2AccountStore sharedStore] setClientID:data.clientId
                                             secret:data.secret
                                              scope:[NSSet setWithObject:scopes]
                                   authorizationURL:[NSURL URLWithString:authURL]
                                           tokenURL:[NSURL URLWithString:tokenURL]
                                        redirectURL:[NSURL URLWithString:data.redirectUriString]
                                      keyChainGroup: keychain
                                     forAccountType:@"myGraphService"];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      if (aNotification.userInfo) {
                                                          //account added, we have access
                                                          //we can now request protected data
                                                          NSLog(@"Success!! We have an access token.");
                                                          dispatch_async(dispatch_get_main_queue(),^ {

                                                              MasterViewController* masterViewController = [self.storyboard instantiateViewControllerWithIdentifier:@"masterView"];
                                                              [self.navigationController pushViewController:masterViewController animated:YES];
                                                          });
                                                      } else {
                                                          //account removed, we lost access
                                                      }
                                                  }];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      NSError *error = [aNotification.userInfo objectForKey:NXOAuth2AccountStoreErrorKey];
                                                      NSLog(@"Error!! %@", error.localizedDescription);
                                                  }];
}
```

## <a name="set-up-the-master-view-to-search-and-display-the-users-from-the-graph-api"></a>Configurer le mode Masque pour rechercher et afficher les utilisateurs à partir de l’API de graphique

Une application maître-vue-contrôleur (MVC) qui affiche les données renvoyées dans la grille est dépasse le cadre de cette procédure pas à pas et nombreux didacticiels en ligne expliquent comment en créer un. Tout ce code est dans le fichier de structure. Toutefois, vous n’avez pas besoin de traiter quelques aspects de cette application MVC :

* Ordonnée à l’origine lorsqu’un utilisateur tape quelque chose dans le champ de recherche
* Fournir un objet de données vers le MasterView afin d’afficher les résultats dans la grille

Nous allons faire ceux ci-dessous.

### <a name="add-a-check-to-see-if-youre-logged-in"></a>Ajouter un contrôle pour vérifier si vous êtes connecté

L’application effectue peu si l’utilisateur n’est pas connecté, il est judicieux de vérifier s’il existe déjà un jeton dans le cache. Si ce n’est pas le cas, vous redirigez l’appel vers la LoginView pour l’utilisateur à se connecter. Si vous rappelez, la meilleure façon d’effectuer des actions lors du chargement d’une vue consiste à utiliser la `viewDidLoad()` méthode Apple nous fournit.

```objc
- (void)viewDidLoad {
    [super viewDidLoad];


    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];

        if (accounts.count == 0) {

        dispatch_async(dispatch_get_main_queue(),^ {

            LoginViewController* userSelectController = [self.storyboard instantiateViewControllerWithIdentifier:@"LoginUserView"];
            [self.navigationController pushViewController:userSelectController animated:YES];
        });
        }
```

### <a name="update-the-table-view-when-data-is-received"></a>Mettre à jour de la vue du tableau lors de la réception de données

Lorsque l’API Graph renvoie les données, vous devez afficher les données. Pour simplifier, Voici tout le code pour mettre à jour la table. Vous pouvez coller uniquement les valeurs dans votre code réutilisable MVC.

```objc
#pragma mark - Table View

- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
    return 1;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {

        return [upnArray count];
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {

    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"TaskPrototypeCell" forIndexPath:indexPath];

    if ( cell == nil ) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:@"TaskPrototypeCell"];
    }

    User *user = nil;
     user = [upnArray objectAtIndex:indexPath.row];


    // Configure the cell
    cell.textLabel.text = user.name;
    [cell setAccessoryType:UITableViewCellAccessoryDisclosureIndicator];

    return cell;
}

```

### <a name="provide-a-way-to-call-the-graph-api-when-someone-types-in-the-search-field"></a>Permet d’appeler l’API Graph lorsque quelqu'un tape dans le champ de recherche

Lorsqu’un utilisateur tape une recherche dans la zone, vous devez qui venu à l’API de graphique. La `GraphAPICaller` cours, vous allez créer dans le code suivant, séparant la fonctionnalité de recherche de la présentation. Pour l’instant, nous allons écrire le code de flux des caractères de recherche à l’API de graphique. Pour ce faire, nous fournissant une méthode appelée `lookupInGraph`, qui prend la chaîne qui nous à rechercher.

```objc

-(void)lookupInGraph:(NSString *)searchText {
if (searchText.length > 0) {

    };



        [GraphAPICaller searchUserList:searchText completionBlock:^(NSMutableArray* returnedUpns, NSError* error) {
            if (returnedUpns) {


                upnArray = returnedUpns;


            }
            else
            {
                UIAlertView *alertView = [[UIAlertView alloc]initWithTitle:nil message:[[NSString alloc]initWithFormat:@"Error : %@", error.localizedDescription] delegate:nil cancelButtonTitle:@"Retry" otherButtonTitles:@"Cancel", nil];

                [alertView setDelegate:self];

                dispatch_async(dispatch_get_main_queue(),^ {
                    [alertView show];
                });
            }


        }];


}
```

## <a name="write-a-helper-class-to-access-the-graph-api"></a>Écrire une classe d’assistance pour accéder à l’API de graphique

Il s’agit de la base de notre application. Alors que le reste a été insérer du code dans le modèle MVC par défaut à partir d’Apple, ici vous écrivez du code pour interroger le graphique en tant que l’utilisateur tape et retourner ces données. Voici le code et une description détaillée suit.

### <a name="create-a-new-objective-c-header-file"></a>Créer un nouveau fichier d’en-tête C objectif

Nommez le fichier `GraphAPICaller.h`et ajoutez le code suivant.

```objc
@interface GraphAPICaller : NSObject<NSURLConnectionDataDelegate>

+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray*, NSError* error))completionBlock;

@end
```

Vous trouverez qu’une méthode spécifiée prend une chaîne et renvoie un completionBlock. Cette completionBlock, comme vous pouvez le deviner, met à jour la table à l’aide d’un objet et données remplie en temps réel en tant que les recherches de l’utilisateur.


### <a name="create-a-new-objective-c-file"></a>Créer un nouveau fichier objectif C

Nommez le fichier `GraphAPICaller.m`et ajoutez la méthode suivante.

```objc
+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    AppData* data = [AppData getInstance];

    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];

    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSDictionary* params = [self convertParamsToDictionary:searchString];

    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
                           }

                           completionBlock(Users, nil);
                       }
                       else
                       {
                           completionBlock(nil, error);
                       }

                   }];
}

```

Passons en revue cette méthode en détail.

Cœur de ce code se trouve dans le `NXOAuth2Request`, la méthode qui prend les paramètres que vous avez déjà définie dans le fichier settings.plist.

La première étape consiste à construire l’appel API Graph droite. Étant donné que vous appelez `/users`, que vous spécifiez qui en l’ajoutant à la ressource Graph API ainsi que la version. Il est préférable d’insertion dans un fichier de paramètres externe car il peuvent changer lorsque l’API évolue.


```objc
NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];
```

Ensuite, vous devez spécifier les paramètres que vous souhaitez également offrir à l’appel API Graph. Il est *très important* que vous ne placez pas les paramètres dans le point de terminaison ressource car qui est modifié pour tous les caractères conforme non URI en cours d’exécution. Tout le code requête doit être fourni dans les paramètres.

```objc

NSDictionary* params = [self convertParamsToDictionary:searchString];
```

Vous remarquerez peut-être cela appelle un `convertParamsToDictionary` méthode qui vous n’avez pas encore écrit. Nous allons faire maintenant à la fin du fichier :

```objc
+(NSDictionary*) convertParamsToDictionary:(NSString*)searchString
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

        NSString *query = [NSString stringWithFormat:@"startswith(givenName, '%@')", searchString];

           [dictionary setValue:query forKey:@"$filter"];



    return dictionary;
}

```
Ensuite, nous allons utiliser la `NXOAuth2Request` méthode pour obtenir des données à partir de l’API au format JSON.

```objc
NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];
```

Pour finir, nous allons voir comment vous renvoyez les données vers le MasterViewController. Les données retourne comme sérialisé et doivent être désérialisé et chargés dans un objet que la MainViewController peut utiliser. À cet effet, la structure comporte un `User.m/h` fichier qui crée un objet utilisateur. Vous remplissez cet objet utilisateur avec des informations à partir du graphique.

```objc
                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
```


## <a name="run-the-sample"></a>Exécuter l’exemple

Si vous avez utilisé la structure ou suivi ainsi que la procédure pas à pas que votre application doit maintenant s’exécuter. Démarrez le simulator et cliquez sur **se connecter** pour utiliser l’application.

## <a name="get-security-updates-for-our-product"></a>Obtenir des mises à jour de sécurité pour nos produits

Nous vous invitons à obtenir des notifications d’incidents de sécurité se produire en visitant le [Site Web TechCenter de sécurité](https://technet.microsoft.com/security/dd252948) et de l’abonnement aux alertes avis de sécurité.
