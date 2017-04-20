<properties 
    pageTitle="Pont iOS affichage Web avec native Engagement Mobile iOS SDK" 
    description="Décrit comment créer un pont entre le mode d’affichage Web exécutant Javascript et native Engagement Mobile iOS SDK"      
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
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="bridge-ios-webview-with-native-mobile-engagement-ios-sdk"></a>Pont iOS affichage Web avec native Engagement Mobile iOS SDK

> [AZURE.SELECTOR]
- [Pont Android](mobile-engagement-bridge-webview-native-android.md)
- [Pont iOS](mobile-engagement-bridge-webview-native-ios.md)

Certaines applications mobiles sont conçues comme une application hybride dans lequel l’application elle-même est développée à l’aide de développement iOS native objectif C, mais certaines ou même tous les écrans sont rendus au sein d’un mode d’affichage Web iOS. Vous pouvez toujours utiliser Engagement Mobile iOS SDK au sein de ces applications et ce didacticiel décrit comment cela. 

Il existe deux méthodes pour y parvenir bien que les deux ne sont pas documentées :

- Tout d’abord une est décrite sur ce [lien](http://stackoverflow.com/questions/9826792/how-to-invoke-objective-c-method-from-javascript-and-send-back-data-to-javascrip) qui consiste à enregistrer une `UIWebViewDelegate` dans votre affichage web et captures-et-immédiatement-Annuler une modification de l’emplacement dans Javascript. 
- Deuxième un est basé sur cette [session WWDC 2013](https://developer.apple.com/videos/play/wwdc2013/615), une approche qui n’est plus claire que la première et nous découlant de ce guide. Notez que cette approche fonctionne uniquement sur iOS7 et version ultérieure. 

Suivez les étapes ci-dessous pour iOS par-delà les exemples :

1. Tout d’abord, vous devez vous assurer que vous avez consulté notre [didacticiel mise en route](mobile-engagement-ios-get-started.md) pour intégrer l’iOS Engagement Mobile SDK dans votre application hybride. Si vous le souhaitez, vous pouvez également activer la journalisation de test comme suit afin que vous puissiez voir méthodes SDK comme nous les déclencher l’affichage Web. 
    
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
           ....
            [EngagementAgent setTestLogEnabled:YES];
           ....
        }

2. Assurez-vous à présent que votre application hybride a un écran avec un mode d’affichage Web dessus. Vous pouvez l’ajouter à la `Main.storyboard` de l’application. 

3. Ce mode d’affichage Web associer votre **ViewController** en cliquant sur et en faisant glisser le mode d’affichage Web à partir de la scène contrôleur de vue à la `ViewController.h` modifier écran, en le plaçant immédiatement sous le `@interface` ligne. 

4. Une fois que cela, une boîte de dialogue s’affiche demandant un nom. Indiquez le nom en tant que **mode d’affichage Web**. Votre `ViewController.h` fichier doit ressembler à ce qui suit :

        #import <UIKit/UIKit.h>
        #import "EngagementViewController.h"
        
        @interface ViewController : EngagementViewController
        @property (strong, nonatomic) IBOutlet UIWebView *webView;
        
        @end

5. Nous met à jour la `ViewController.m` de fichiers plus tard, mais tout d’abord, nous allons créer le fichier pont qui crée un emballage sur certains iOS Mobile Engagement fréquemment utilisé méthodes SDK. Créer un nouveau fichier d’en-tête appelé **EngagementJsExports.h** qui utilise le `JSExport` mécanisme décrit dans la [session](https://developer.apple.com/videos/play/wwdc2013/615) ci-dessus pour exposer les méthodes iOS native. 

        #import <Foundation/Foundation.h>
        #import <JavaScriptCore/JavascriptCore.h>
        
        @protocol EngagementJsExports <JSExport>
        
        + (void) sendEngagementEvent:(NSString*) name :(NSString*)extras;
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras;
        + (void) endEngagementJob:(NSString*) name;
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras;
        + (void) sendEngagementAppInfo:(NSString*) appInfo;
        
        @end

        @interface EngagementJs : NSObject <EngagementJsExports>

        @end

6. Ensuite, nous allons créer la deuxième partie du fichier pont. Créer un fichier nommé **EngagementJsExports.m** qui contient l’implémentation de création des wrappers réels en appelant les méthodes SDK Engagement Mobile iOS. Notez également que nous apportions l’analyse de la `extras` passé à partir de l’affichage Web javascript et qui en plaçant un `NSMutableDictionary` objet à passer avec les appels de méthode SDK Engagement.  

        #import <UIKit/UIKit.h>
        #import "EngagementAgent.h"
        #import "EngagementJsExports.h"
        
        @implementation EngagementJs
        
        +(void) sendEngagementEvent:(NSString*)name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendEvent:name extras:extrasInput];
        }
        
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] startJob:name extras:extrasInput];
        }
        
        + (void) endEngagementJob:(NSString*) name {
           [[EngagementAgent shared] endJob:name];
        }
        
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendError:name extras:extrasInput];
        }
        
        + (void) sendEngagementAppInfo:(NSString*) appInfo {
           NSMutableDictionary* appInfoInput = [self ParseExtras:appInfo];
           [[EngagementAgent shared] sendAppInfo:appInfoInput];
        }
        
        + (NSMutableDictionary*) ParseExtras:(NSString*) input {
           NSData *data = [input dataUsingEncoding:NSUTF8StringEncoding];
           NSError* error = nil;
           NSMutableDictionary* extras = [NSJSONSerialization JSONObjectWithData:data options:0 error:&error];
           
           return extras;
        }
        
        @end

5. Maintenant, nous revenez à la **ViewController.m** et mettre à jour avec le code suivant : 
        
        #import <JavaScriptCore/JavaScriptCore.h>
        #import "ViewController.h"
        #import "EngagementJsExports.h"
        
        @interface ViewController ()
        
        @end
        
        @implementation ViewController
        
        - (void)viewDidLoad
        {
           self.webView.delegate = self;
           [super viewDidLoad];
           [self loadWebView];
        }
        
        - (void)loadWebView {
           NSBundle* mainBundle = [NSBundle mainBundle];
           NSURL* htmlPage = [mainBundle URLForResource:@"LocalPage" withExtension:@"html"];
           
           NSURLRequest* urlReq = [NSURLRequest requestWithURL:htmlPage];
           [self.webView loadRequest:urlReq];
        }
        
        - (void)webViewDidFinishLoad:(UIWebView*)wv
        {
           JSContext* context = [wv valueForKeyPath:@"documentView.webView.mainFrame.javaScriptContext"];
           
           context[@"EngagementJs"] = [EngagementJs class];
        }
        
        - (void)webView:(UIWebView*)wv didFailLoadWithError:(NSError*)error
        {
           NSLog(@"Error for WEBVIEW: %@", [error description]);
        }
        
        - (void)didReceiveMemoryWarning {
           [super didReceiveMemoryWarning];
           // Dispose of any resources that can be recreated.
        }
        
        @end

6. Notez les points suivants sur le fichier **ViewController.m** :

    - Dans la `loadWebView` méthode, nous allons chargement d’un fichier HTML local appelé **LocalPage.html** dont nous allons passer en revue ensuite le code. 
    - Dans la `webViewDidFinishLoad` méthode, nous allons saisissant la `JsContext` et en associant nos cours emballage. Cette option permet d’appeler notre emballage méthodes SDK à l’aide de la poignée **EngagementJs** l’affichage Web. 

7. Créer un fichier nommé **LocalPage.html** par le code suivant :

        <!doctype html>
        <html>
           <head>
               <style type='text/css'>
                   html { font-family:Helvetica; color:#222; }
                   h1 { color:steelblue; font-size:22px; margin-top:16px; }
                   h2 { color:grey; font-size:14px; margin-top:18px; margin-bottom:0px; }
               </style>
               
               <script type="text/javascript">
               
               window.onerror = function(err)
               {
                   alert('window.onerror: ' + err);
               }
               
               function send(inputId)
               {
                   var input = document.getElementById(inputId);
                   if(input)
                   {
                       var value = input.value;
                       // Example of how extras info can be passed with the Engagement logs
                       var extras = '{"CustomerId":"MS290011"}';
                   }
                   
                   if(value && value.length > 0)
                   {
                       switch(inputId)
                       {
                           case "event":
                           EngagementJs.sendEngagementEvent(value, extras);
                           break;
                           
                           case "job":
                           EngagementJs.startEngagementJob(value, extras);
                           window.setTimeout(
                           function(){
                               EngagementJs.endEngagementJob(value);
                           }, 10000 );
                           break;
        
                           case "error":
                           EngagementJs.sendEngagementError(value, extras);
                           break;
                           
                           case "appInfo":
                           var appInfo = '{"customer_name":"' + value + '"}';
                           EngagementJs.sendEngagementAppInfo(appInfo);
                           break;
                       }
                   }
               }
               </script>
               
           </head>
           <body>
               <h1>Bridge Tester</h1>
               
               <div id='engagement'>
                   
                   <br/>
                   <h2>Event</h2>
                   <input type="text" id="event" size="35">
                   <button onclick="send('event')">Send</button>
                   
                   <br/>
                   <h2>Job</h2>
                   <input type="text" id="job" size="35">
                   <button onclick="send('job')">Send</button>
                   
                   <br/>
                   <h2>Error</h2>
                   <input type="text" id="error" size="35">
                   <button onclick="send('error')">Send</button
                   
                   <br/>
                   <h2>AppInfo</h2>
                   <input type="text" id="appInfo" size="35">
                   <button onclick="send('appInfo')">Send</button>
               
               </div>
           </body>
        </html>

8. Notez les points suivants sur le fichier HTML ci-dessus :

    -   Il contient un ensemble de zones de saisie dans lequel vous pouvez fournir des données à utiliser comme noms pour votre événement, la tâche, l’erreur, l’AppInfo. Lorsque vous cliquez sur le bouton en regard de celle-ci, un appel est effectué vers le code Javascript qui à son tour appelle les méthodes à partir du fichier pont pour transférer cet appel à l’Engagement Mobile iOS SDK. 
    -   Nous allons marquage sur certaines informations supplémentaires statique aux événements, tâches et même les erreurs pour vous montrer comment cela peut être effectué. Cette information supplémentaire est envoyée en tant qu’un JSON chaîne qui, si vous regardez dans la `EngagementJsExports.m` de fichiers, est analysé et transmises ainsi que l’envoi d’événements, tâches, les erreurs. 
    -   Un travail d’Engagement Mobile est lancé avec le nom que vous spécifiez dans la zone d’entrée, exécuter pendant 10 secondes et arrêter. 
    -   Un Engagement Mobile appinfo ou une balise est passé avec 'customer_name » comme clé statique et la valeur que vous avez entrées dans l’entrée en tant que la valeur de la balise. 
 
9. Exécuter l’application et vous verrez les éléments suivants. Maintenant fournir un nom pour un événement de test similaire à la suivante, puis cliquez sur **Envoyer** en regard. 

    ![][1]

10. Maintenant si vous accédez à l’onglet **Analyseur** de votre application et consultez la section **événements -> plus d’informations**, vous verrez cet événement apparaissant ainsi que les informations de l’application statique que nous apportions envoyez. 

    ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-ios/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-ios/event-output.png
