<properties
    pageTitle="Prise en main Azure Engagement Mobile pour Xamarin.Android"
    description="Découvrez comment utiliser Azure Mobile Engagement avec Analytique et les Notifications de transmission pour les applications Xamarin.Android."
    services="mobile-engagement"
    documentationCenter="xamarin"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="06/16/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-xamarinandroid-apps"></a>Prise en main Azure Engagement Mobile pour les applications Xamarin.Android

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Cette rubrique vous montre comment utiliser Azure Mobile Engagement pour comprendre l’utilisation de votre application et comment envoyer les notifications push aux utilisateurs segmentés d’une application Xamarin.Android.
Ce didacticiel montre le scénario de diffusion simple à l’aide d’Engagement Mobile. Dans, vous créez une application vierge Xamarin.Android qui collecte des données de base et reçoit les notifications push à l’aide de Google Cloud messagerie (GCM).

Ce didacticiel requiert les éléments suivants :

+ [Xamarin Studio](http://xamarin.com/studio). Vous pouvez également utiliser Visual Studio avec Xamarin mais ce didacticiel utilise Xamarin Studio. Pour obtenir des instructions d’installation, voir [configurer et installer pour Visual Studio et Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).
+ [Kit de développement mobile Engagement Xamarin](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [AZURE.NOTE] Pour effectuer ce didacticiel, vous devez disposer d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-android-get-started).

##<a id="setup-azme"></a>Le programme d’installation Mobile Engagement pour votre application Android

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Connecter votre application sur le serveur principal Engagement Mobile

Ce didacticiel présente une « intégration de base », ce qui correspond à l’ensemble minimal requis pour collecter les données et envoyer une notification push. 

Nous allons créer une application simple avec Xamarin Studio pour illustrer l’intégration.

###<a name="create-a-new-xamarinandroid-project"></a>Créer un projet Xamarin.Android

1. Menu de lancement **Xamarin Studio** accédez au **fichier** -> **Nouveau** -> **Solution** 

    ![][1]

2. Sélectionnez **Application Android** , puis vérifiez que la langue sélectionnée est **c#** et cliquez sur **suivant**.

    ![][2]

3. Entrez le **Nom de l’application** et l' **identificateur de l’organisation**. Veillez à coche **Services Google Play** , puis sur **suivant**. 

    ![][3]
    
4. Modifier le **Nom du projet**, le **Nom de la Solution** et **l’emplacement** si nécessaire, cliquez sur **créer**.

    ![][4]
 
Xamarin Studio créera l’application dans laquelle nous allez intégrer Mobile Engagement. 

###<a name="connect-your-app-to-mobile-engagement-backend"></a>Connecter votre application pour Mobile Engagement principal

1. Cliquez avec le bouton droit sur le dossier **Packages** dans les fenêtres de Solution, puis sélectionnez **Ajouter des Packages...**

    ![][5]

2. Recherchez le **Kit de développement logiciel Microsoft Azure Mobile Engagement Xamarin** et l’ajouter à votre solution.  

    ![][6]
   
3. Ouvrez **MainActivity.cs** et ajoutez les éléments suivants à l’aide des instructions :

        using Microsoft.Azure.Engagement;
        using Microsoft.Azure.Engagement.Activity;

4. Dans la `OnCreate` méthode, ajoutez le code suivant pour l’initialisation de la connexion avec le serveur principal Mobile Engagement. Veillez à ajouter votre **ConnectionString**. 

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.ConnectionString = "YourConnectionStringFromAzurePortal";
        EngagementAgent.Init(engagementConfiguration);

###<a name="add-permissions-and-a-service-declaration"></a>Ajouter une déclaration de service et des autorisations

1. Ouvrez le fichier **Manifest.xml** sous le dossier Propriétés. Sélectionnez l’onglet Source afin que vous mettez à jour la source XML directement.
 
2. Ajouter les autorisations suivantes à la Manifest.xml (qui se trouve sous le dossier **Propriétés** ) de votre projet immédiatement avant ou après le `<application>` balise :

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

3. Ajouter le code suivant entre la `<application>` et `</application>` balises pour déclarer le service agent :

        <service
            android:name="com.microsoft.azure.engagement.service.EngagementService"
            android:exported="false"
            android:label="<Your application name>"
            android:process=":Engagement"/>

4. Dans le code que vous venez de coller, remplacez `"<Your application name>"` dans l’étiquette. Il s’affiche dans le menu **paramètres** dans laquelle les utilisateurs peuvent voir les services en cours d’exécution sur l’appareil. Vous pouvez par exemple ajouter le mot « Service » dans cette étiquette.

###<a name="send-a-screen-to-mobile-engagement"></a>Envoyer un écran à Engagement Mobile

Avant de commencer l’envoi de données et vérifier que les utilisateurs sont actives, vous devez envoyer au moins un écran vers le serveur principal Mobile Engagement. Pour cela-s’assurer que les `MainActivity` hérite `EngagementActivity` au lieu de `Activity`.

    public class MainActivity : EngagementActivity
    
Par ailleurs, si vous ne pouvez pas hériter `EngagementActivity` puis vous devez ajouter `.StartActivity` et `.EndActivity` méthodes de `OnResume` et `OnPause` respectivement.  

        protected override void OnResume()
            {
                EngagementAgent.StartActivity(EngagementAgentUtils.BuildEngagementActivityName(Java.Lang.Class.FromType(this.GetType())), null);
                base.OnResume();             
            }
    
            protected override void OnPause()
            {
                EngagementAgent.EndActivity();
                base.OnPause();            
            }

##<a id="monitor"></a>Application interrogez la surveillance en temps réel

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Activer les notifications push et dans l’application de messagerie

Engagement Mobile vous permet d’interagir avec et atteindre vos utilisateurs avec les notifications push et dans le contexte des campagnes de messagerie dans l’application. Ce module est appelé accessibles dans le portail d’Engagement Mobile.
Les sections suivantes configure votre application pour les recevoir.

[AZURE.INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[AZURE.INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[AZURE.INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[1]: ./media/mobile-engagement-xamarin-android-get-started/1.png
[2]: ./media/mobile-engagement-xamarin-android-get-started/2.png
[3]: ./media/mobile-engagement-xamarin-android-get-started/3.png
[4]: ./media/mobile-engagement-xamarin-android-get-started/4.png
[5]: ./media/mobile-engagement-xamarin-android-get-started/5.png
[6]: ./media/mobile-engagement-xamarin-android-get-started/6.png
