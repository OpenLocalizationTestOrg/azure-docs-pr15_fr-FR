<properties 
    pageTitle="En mode Web Android pont avec native Engagement Android Kit de développement Mobile" 
    description="Décrit comment créer un pont entre le mode d’affichage Web exécutant Javascript et native Mobile Engagement Android SDK"      
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="erikre" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-android" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="bridge-android-webview-with-native-mobile-engagement-android-sdk"></a>En mode Web Android pont avec native Engagement Android Kit de développement Mobile

> [AZURE.SELECTOR]
- [Pont Android](mobile-engagement-bridge-webview-native-android.md)
- [Pont iOS](mobile-engagement-bridge-webview-native-ios.md)

Certaines applications mobiles sont conçues comme une application hybride dans lequel l’application elle-même est développée à l’aide de développement Android natif mais certains ou même tous les écrans sont rendus au sein d’un mode d’affichage Web Android. Vous pouvez toujours utiliser Mobile Engagement Android SDK au sein de ces applications et ce didacticiel décrit comment cela. L’exemple de code ci-dessous est basé sur la documentation Android [ici](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript). Il décrit comment cette approche documentation pourrait être utilisée pour implémenter le même pour les méthodes couramment utilisées Mobile Engagement Android du Kit de développement telle qu’un mode d’affichage Web à partir d’une application hybride peut également lancer des demandes pour le suivi des événements, des tâches, des erreurs, application-info lors de leur tuyauterie via notre SDK Android. 

1. Tout d’abord, vous devez vous assurer que vous avez consulté notre [didacticiel mise en route](mobile-engagement-android-get-started.md) pour intégrer l’Engagement Android Kit de développement Mobile dans votre application hybride. Une fois que vous effectuez, votre `OnCreate` méthode ressemble à ce qui suit.  
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("<Mobile Engagement Conn String>");
            EngagementAgent.getInstance(this).init(engagementConfiguration);
        }

2. Assurez-vous à présent que votre application hybride a un écran avec un mode d’affichage Web dessus. Le code pour qu’il sera semblable à ce qui suit dans laquelle nous sommes charge un HTML local de fichier **Sample.html** dans l’affichage Web dans le `onCreate` méthode de l’écran. 

        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
        }

        protected void onCreate(Bundle savedInstanceState) {
            ...
            ...
            SetWebView();
        }

3. Maintenant créer un fichier de pont appelé **WebAppInterface** qui crée un emballage sur certaines méthodes Kit de développement Mobile Engagement Android fréquemment utilisés à l’aide de la `@JavascriptInterface` approche décrite dans la [documentation Android](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript):

        import android.content.Context;
        import android.os.Bundle;
        import android.util.Log;
        import android.webkit.JavascriptInterface;
        
        import com.microsoft.azure.engagement.EngagementAgent;
        
        import org.json.JSONArray;
        import org.json.JSONObject;
        
        public class WebAppInterface {
            Context mContext;
        
            /** Instantiate the interface and set the context */
            WebAppInterface(Context c) {
                mContext = c;
            }
        
            @JavascriptInterface
            public void sendEngagementEvent(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendEvent(name, ParseExtras(extras));
            }
        
            @JavascriptInterface
            public void startEngagementJob(String name, String extras ){
                EngagementAgent.getInstance(mContext).startJob(name, ParseExtras(extras));
            }
        
            @JavascriptInterface
            public void endEngagementJob(String name){
                EngagementAgent.getInstance(mContext).endJob(name);
            }
        
            @JavascriptInterface
            public void sendEngagementError(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendError(name, ParseExtras(extras));
            }
        
            @JavascriptInterface
            public void sendEngagementAppInfo(String appInfo){
                EngagementAgent.getInstance(mContext).sendAppInfo(ParseExtras(appInfo));
            }
        
            public Bundle ParseExtras(String input) {
                Bundle extras = new Bundle();
        
                try {
                    JSONObject jObject = new JSONObject(input);
                    extras.putString(jObject.names().getString(0),
                            jObject.get(jObject.names().getString(0)).toString());
                } catch (Exception e) {
                    e.printStackTrace();
                }
                return extras;
            }
        }  

4. Une fois que nous avons créé le fichier pont ci-dessus, nous avons besoin de vous assurer qu’il est associé à notre mode d’affichage Web. Pour ce faire, vous devez modifier votre `SetWebview` méthode afin qu’il ressemble à ceci :

        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
            WebSettings webSettings = myWebView.getSettings();
            webSettings.setJavaScriptEnabled(true);
            myWebView.addJavascriptInterface(new WebAppInterface(this), "EngagementJs");
        }

5. Dans l’extrait de code ci-dessus, nous avons appelé `addJavascriptInterface` associer nos cours pont notre en mode Web et également créé une poignée appelée **EngagementJs** pour appeler les méthodes à partir du fichier pont. 

6. Créez maintenant le fichier suivant appelé **Sample.html** dans votre projet dans un dossier nommé **actifs** qui est chargé dans le mode d’affichage Web et dans lequel nous appelons les méthodes à partir du fichier pont.

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
                      log('window.onerror: ' + err);
                    }
        
                    send = function(inputId)
                    {
                        var input = document.getElementById(inputId);
                        if(input)
                        {
                            var value = input.value;
                            // Example of how extras info can be passed with the Engagement logs
                            var extras = '{"CustomerId":"MS290011"}';
        
                            if(value && value.length > 0)
                            {
                                switch(inputId)
                                {
                                    case "event":
                                    EngagementJs.sendEngagementEvent(value, extras);
                                    break;
        
                                    case "job":
                                    EngagementJs.startEngagementJob(value, extras);
                                    window.setTimeout( function()
                                    {
                                      EngagementJs.endEngagementJob(value);
                                    }, 10000 );
                                    break;
        
                                    case "error":
                                    EngagementJs.sendEngagementError(value, extras);
                                    break;
        
                                    case "appInfo":
                                    EngagementJs.sendEngagementAppInfo({"customer_name":value});
                                    break;
                                }
                            }
                        }
                    }
                </script>
            </head>
            <body>
                <h1>Bridge Tester</h1>
                <div id='engagement'>
                    <h2>Event</h2>
                    <input type="text" id="event" size="35">
                    <button onclick="send('event')">Send</button>
        
                    <h2>Job</h2>
                    <input type="text" id="job" size="35">
                    <button onclick="send('job')">Send</button>
        
                    <h2>Error</h2>
                    <input type="text" id="error" size="35">
                    <button onclick="send('error')">Send</button>
        
                    <h2>AppInfo</h2>
                    <input type="text" id="appInfo" size="35">
                    <button onclick="send('appInfo')">Send</button>
                </div>
            </body>
        </html>

8. Notez les points suivants sur le fichier HTML ci-dessus :

    -   Il contient un ensemble de zones de saisie dans lequel vous pouvez fournir des données à utiliser comme noms pour votre événement, la tâche, l’erreur, l’AppInfo. Lorsque vous cliquez sur le bouton en regard de celle-ci, un appel est effectué vers le code Javascript qui à son tour appelle les méthodes à partir du fichier pont pour transférer cet appel vers le Kit de développement Mobile Engagement Android. 
    -   Nous allons marquage sur certaines informations supplémentaires statique aux événements, tâches et même les erreurs pour vous montrer comment cela peut être effectué. Cette information supplémentaire est envoyée en tant qu’un JSON chaîne qui, si vous regardez dans la `WebAppInterface` de fichiers, est analysé et placer dans un appareil Android `Bundle` et est passé en même temps que l’envoi d’événements, tâches, les erreurs. 
    -   Un travail d’Engagement Mobile est lancé avec le nom que vous spécifiez dans la zone d’entrée, exécuter pendant 10 secondes et arrêter. 
    -   Un Engagement Mobile appinfo ou une balise est passé avec 'customer_name » comme clé statique et la valeur que vous avez entrées dans l’entrée en tant que la valeur de la balise. 
 
9. Exécuter l’application et vous verrez les éléments suivants. Maintenant fournir un nom pour un événement de test similaire à la suivante, puis cliquez sur **Envoyer** située en dessous. 

    ![][1]

10. Maintenant si vous accédez à l’onglet **Analyseur** de votre application et consultez la section **événements -> plus d’informations**, vous verrez cet événement apparaissant ainsi que les informations de l’application statique que nous apportions envoyez. 

    ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-android/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-android/event-output.png
