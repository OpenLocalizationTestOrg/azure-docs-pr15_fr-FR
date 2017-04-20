<properties
    pageTitle="Prise en main Azure Engagement Mobile pour Cordova/Phonegap"
    description="Découvrez comment utiliser Azure Mobile Engagement avec Analytique et les Notifications de transmission pour les applications Cordova/Phonegap."
    services="mobile-engagement"
    documentationCenter="Mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-phonegap"
    ms.devlang="js"
    ms.topic="hero-article" 
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-cordovaphonegap"></a>Prise en main Azure Engagement Mobile pour Cordova/Phonegap

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Cette rubrique vous montre comment utiliser Azure Mobile Engagement pour comprendre l’utilisation de votre application et envoyer les notifications push aux utilisateurs segmentés pour une application mobile développé avec Cordova.

Dans ce didacticiel, nous créer une application Cordova vierge à l’aide de Mac et puis intégrer le Kit de développement Mobile Engagement. Il collecte des données de base analytique et reçoit les notifications push à l’aide du système de Notification Push Apple (APNS) pour iOS et Google Cloud messagerie (GCM) pour Android. Nous déployez cela un iOS ou un appareil Android pour le test. 

> [AZURE.NOTE] Pour effectuer ce didacticiel, vous devez disposer d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-cordova-get-started).

Ce didacticiel requiert les éléments suivants :

+ XCode, vous pouvez installer à partir de Mac App Store (pour le déploiement sur iOS)
+ [Émulateur & SDK android](http://developer.android.com/sdk/installing/index.html) (pour le déploiement sur Android)
+ Certificat de notification push (.p12) que vous pouvez obtenir à partir du centre de développement Apple APNS
+ Numéro de projet GCM que vous pouvez obtenir à partir de votre Console développeur Google pour GCM
+ [Plug-in Cordova d’Engagement mobile](https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-engagement)

> [AZURE.NOTE] Vous pouvez trouver le code source et le fichier Lisezmoi pour le plug-in Cordova sur [Github](https://github.com/Azure/azure-mobile-engagement-cordova)

##<a id="setup-azme"></a>Le programme d’installation Mobile Engagement pour votre application Cordova

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Connectez votre application sur le serveur principal Engagement Mobile

Ce didacticiel présente une « intégration simple » qui est minimal nécessaires pour collecter les données et envoyer une notification push. 

Nous allons créer une application simple avec Cordova pour illustrer l’intégration :

###<a name="create-a-new-cordova-project"></a>Créer un projet Cordova

1. Démarrez fenêtre *Terminal Server* sur votre ordinateur Mac, puis tapez les informations suivantes qui crée un nouveau projet Cordova à partir du modèle par défaut. Vérifiez que le profil de publication que vous utilisez finalement pour déployer votre application iOS utilise « com.mycompany.myapp » comme application ID. 

        $ cordova create azme-cordova com.mycompany.myapp
        $ cd azme-cordova

2. Exécutez la commande suivante pour configurer votre projet pour **iOS** et exécutez-le dans iOS Simulator :

        $ cordova platform add ios 
        $ cordova run ios

3. Exécutez la commande suivante pour configurer votre projet pour **Android** et l’exécuter dans l’émulateur Android. Assurez-vous que vos paramètres Android SDK émulateur disposent sa cible en tant que Google APIs (Google Inc.) avec l’UC / ABI comme Google APIs ARM.  

        $ cordova platform add android
        $ cordova run android

4.  Ajouter le plug-in Cordova Console. 

        $ cordova plugin add cordova-plugin-console 

###<a name="connect-your-app-to-mobile-engagement-backend"></a>Connecter votre application pour Mobile Engagement principal

1. Installer le plug-in Azure Mobile Engagement Cordova tout en fournissant les valeurs de variable pour configurer le plug-in :

        cordova plugin add cordova-plugin-ms-azure-mobile-engagement    
            --variable AZME_IOS_CONNECTION_STRING=<iOS Connection String> 
            --variable AZME_IOS_REACH_ICON=... (icon name WITH extension) 
            --variable AZME_ANDROID_CONNECTION_STRING=<Android Connection String> 
            --variable AZME_ANDROID_REACH_ICON=... (icon name WITHOUT extension)       
            --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=... (From your Google Cloud console for sending push notifications) 
            --variable AZME_ACTION_URL =... (URL scheme which triggers the app for deep linking)
            --variable AZME_ENABLE_NATIVE_LOG=true|false
            --variable AZME_ENABLE_PLUGIN_LOG=true|false

*Icône atteindre Android* : doit correspondre au nom de la ressource sans n’importe quel numéro de poste, ni préfixe drawable (ex : mynotificationicon), et le fichier icône doit être copié dans votre projet android (plateformes/android/res/drawable)

*iOS icône atteindre* : doit correspondre au nom de la ressource avec l’extension (ex : mynotificationicon.png), et le fichier icône doit être ajouté à votre projet iOS avec XCode (en utilisant le Menu ajouter de fichiers)

##<a id="monitor"></a>Activation de l’analyse en temps réel

1. Dans le projet Cordova - modifier **www/js/index.js** pour ajouter l’appel vers Mobile Engagement déclarer une nouvelle activité une fois l’événement *deviceReady* correctement après réception.

         onDeviceReady: function() {
                Engagement.startActivity("myPage",{});
            }

2. Exécutez l’application :
        
    - **Pour iOS**
    
        Dans `Terminal` fenêtre lancer votre application dans une nouvelle instance Simulator en exécutant le code suivant :

            cordova run ios

    - **Pour Android**
        
        Dans `Terminal` fenêtre lancer votre application dans une nouvelle instance émulateur en exécutant le code suivant :

            cordova run android

3. Vous pouvez voir les éléments suivants dans les journaux de console :

        [Engagement] Agent: Session started
        [Engagement] Agent: Activity 'myPage' started
        [Engagement] Connection: Established
        [Engagement] Connection: Sent: appInfo
        [Engagement] Connection: Sent: startSession
        [Engagement] Connection: Sent: activity name='myPage'

##<a id="monitor"></a>Application interrogez la surveillance en temps réel

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>L’activation des Notifications de transmission et dans l’application de messagerie

Engagement Mobile vous permet d’interagir avec d’autres utilisateurs à l’aide de Notifications de transmission et dans le contexte des campagnes de messagerie dans l’application. Ce module est appelé accessibles dans le portail d’Engagement Mobile.
Les sections suivantes va configurer votre application pour les recevoir.

###<a name="configure-push-credentials-for-mobile-engagement"></a>Configurer les informations d’identification Push pour Engagement Mobile

Pour permettre à Engagement Mobile envoyer des Notifications de transmission à votre place, vous devez lui accorder un accès à votre certificat d’iOS Apple ou clé d’API GCM Server. 
    
1. Accédez à votre portail Mobile Engagement. Veiller à ce que vous êtes dans l’application nous utilisez pour ce projet, puis cliquez sur le bouton **engagez** en bas :
    
    ![][1]
    
2. Vous est alors placé dans la page Paramètres dans votre portail d’Engagement. À partir de là, cliquez sur la section **Transmission Native** :
    
    ![][2]

3. Configurer la clé de certificat/GCM serveur API iOS

    **[iOS]**

    un. Sélectionnez votre .p12, téléchargez-le et tapez votre mot de passe :
    
    ![][3]

    **[Android]**

    un. Cliquez sur l’icône de modification en face de la **Clé de l’API** dans la section Paramètres GCM et dans la fenêtre contextuelle qui s’affiche, collez la clé du serveur GCM et cliquez sur **OK**. 
        
    ![][4]

###<a name="enable-push-notifications-in-the-cordova-app"></a>Activer les notifications push dans l’application Cordova

Modifier **www/js/index.js** pour ajouter l’appel vers Mobile Engagement pour demander des notifications push et déclarer un gestionnaire :

     onDeviceReady: function() {
           Engagement.initializeReach(  
                // on OpenUrl  
                function(_url) {   
                alert(_url);   
                });  
            Engagement.startActivity("myPage",{});  
        }

###<a name="run-the-app"></a>Exécuter l’application

**[iOS]**

1. Nous utiliserons XCode pour créer et déployer l’application sur le périphérique pour tester les notifications push comme iOS permet uniquement les notifications push pour un périphérique réel. Accédez à l’emplacement où votre projet Cordova est créé et accédez à **...\platforms\ios** emplacement. Ouvrez le fichier .xcodeproj native dans XCode. 
    
2. Créez et déployez l’application Cordova à l’appareil iOS en utilisant le compte qui comporte le profil mise en service qui contient le certificat que vous venez téléchargé dans le portail d’Engagement Mobile et l’Id d’application qui correspond à celui vous avez fourni lors de la création de l’application Cordova. Vous pouvez consulter l' *identificateur de groupe* dans votre **ressources\*-info.plist** fichier dans XCode pour correspondre à la. 

3. Vous verrez la fenêtre contextuelle standard iOS sur votre appareil indiquant que l’application demande l’autorisation d’envoyer des notifications. Accorder l’autorisation. 

**[Android]**

Vous pouvez simplement utiliser l’émulateur pour exécuter l’application Android notifications GCM sont pris en charge sur l’émulateur Android. 

    cordova run android

##<a id="send"></a>Envoyer une notification à votre application

Nous allons maintenant créer une campagne de Notification Push simple qui vous envoie une épingle dans votre application en cours d’exécution sur l’appareil :

1. Accédez à l’onglet **Atteindre** dans votre portail Engagement Mobile

2. Cliquez sur **Nouvelle annonce** pour créer votre campagne push

    ![][6]

3. Fournir des entrées pour créer votre campagne **[Android]**
    
    - Attribuez un **nom** pour votre campagne. 
    - Sélectionnez le **Type de livraison** en tant que *notification système* *Simple*
    - Sélectionnez l' **heure de la remise** comme *« n’importe quel temps »*
    - Fournir un **titre** pour votre notification qui sera la première ligne de la diffusion.
    - Fournir un **Message** pour votre notification qui servira le corps du message. 

    ![][11]

4. Fournir des entrées pour créer votre campagne **[iOS]**

    - Attribuez un **nom** pour votre campagne. 
    - Sélectionnez l' **heure de la remise** comme *« déconnecter de l’application uniquement »*
    - Fournir un **titre** pour votre notification qui sera la première ligne de la diffusion.
    - Fournir un **Message** pour votre notification qui servira le corps du message. 
 
    ![][12]

5. Défilement vers le bas, puis dans la section du contenu, sélectionnez **Notification uniquement**

    ![][8]

6. [Facultatif] Vous pouvez également fournir une URL de l’Action. Vérifiez qu’il utilise un schéma d’URL fourni lors de la configuration du plug-in **AZME\_rediriger\_URL** variable, par exemple *myapp://test*.  

7. Vous avez terminé définir la campagne plus simple possible. Maintenant, faites défiler vers le bas à nouveau et cliquez sur le bouton **créer** pour enregistrer votre campagne.

8. Pour finir **Activer** votre campagne
    
    ![][10]

9. Une notification push sur votre appareil ou émulateur normalement, dans le cadre de cette campagne. 

##<a id="next-steps"></a>Étapes suivantes
[Vue d’ensemble de toutes les méthodes disponibles avec Engagement Kit de développement Mobile Cordova](https://github.com/Azure/azure-mobile-engagement-cordova)

<!-- Images. -->

[1]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[2]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[3]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[4]: ./media/mobile-engagement-cordova-get-started/api-key.png
[6]: ./media/mobile-engagement-cordova-get-started/new-announcement.png
[8]: ./media/mobile-engagement-cordova-get-started/campaign-content.png
[10]: ./media/mobile-engagement-cordova-get-started/campaign-activate.png
[11]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-android.png
[12]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-ios.png

