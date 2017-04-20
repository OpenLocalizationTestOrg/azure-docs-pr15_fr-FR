<properties
    pageTitle="Prise en main applications Azure Mobile Engagement pour Windows Phone Silverlight"
    description="Découvrez comment utiliser Azure Mobile Engagement avec notifications analytique et push pour Windows Phone Silverlight applications."
    services="mobile-engagement"
    documentationCenter="windows"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-windows-phone-silverlight-apps"></a>Prise en main applications Azure Mobile Engagement pour Windows Phone Silverlight

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Cette rubrique vous montre comment utiliser Azure Mobile Engagement pour comprendre l’utilisation de votre application et envoyer les notifications push aux utilisateurs segmentés d’une application Silverlight de Windows Phone.
Ce didacticiel montre le scénario de diffusion simple à l’aide d’Engagement Mobile. Dans, vous créez une application Windows Phone Silverlight vierge qui collecte des données de base et reçoit les notifications push à l’aide du Service de Notification Push Microsoft (MPNS).

> [AZURE.NOTE] Si vous ciblez Windows Phone 8.1 (non Silverlight), consultez le [didacticiel Windows universel](mobile-engagement-windows-store-dotnet-get-started.md).

Ce didacticiel requiert les éléments suivants :

+ Visual Studio 2013
+ Package Nuget [MicrosoftAzure.MobileEngagement]

> [AZURE.NOTE] Pour effectuer ce didacticiel, vous devez disposer d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-windows-phone-get-started).

##<a id="setup-azme"></a>Configuration d’Engagement Mobile pour votre Windows Phone app

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Connecter votre application sur le serveur principal Engagement Mobile

Ce didacticiel présente une « intégration de base », ce qui correspond à l’ensemble minimal requis pour collecter les données et envoyer une notification push. Vous trouverez la documentation intégration complète dans l' [intégration du Kit de développement Mobile Engagement Windows Phone](mobile-engagement-windows-phone-sdk-overview.md)

Nous allons créer une application de base avec Visual Studio pour illustrer l’intégration.

###<a name="create-a-new-windows-phone-silverlight-project"></a>Créer un projet Silverlight de Windows Phone

Les étapes suivantes impliquent l’utilisation de Visual Studio 2015 bien que les étapes sont identiques dans les versions antérieures de Visual Studio. 

1. Démarrez Visual Studio, puis dans l’écran **d’accueil** , sélectionnez **Nouveau projet**.

2. Dans le menu contextuel, sélectionnez **Windows 8** -> **Windows Phone** -> **Application vierge (Windows Phone Silverlight)**. Entrez le **nom de** l’application et **nom de la Solution**, puis cliquez sur **OK**.

    ![][1]

3. Vous pouvez choisir de cibler **Windows Phone 8.0** ou **Windows Phone 8.1**.

Vous venez de créer une nouvelle application Windows Phone Silverlight dans lequel nous allez intégrer le Kit de développement Azure Mobile Engagement.

###<a name="connect-your-app-to-the-mobile-engagement-backend"></a>Connecter votre application sur le serveur principal Engagement Mobile

1. Installez le package nuget [MicrosoftAzure.MobileEngagement] dans votre projet.

2. Ouvrir `WMAppManifest.xml` (sous le dossier Propriétés) et vérifiez que les éléments suivants sont déclarés (ajoutez-les si elles ne sont pas) dans le `<Capabilities />` balise :

        <Capability Name="ID_CAP_NETWORKING" />
        <Capability Name="ID_CAP_IDENTITY_DEVICE" />

    ![][2]

3. Coller la chaîne de connexion que vous avez copiée précédemment pour votre application Mobile Engagement maintenant et la coller dans le `Resources\EngagementConfiguration.xml` de fichiers, entre la `<connectionString>` et `</connectionString>` balises :

    ![][3]

4. Dans la `App.xaml.cs` fichier :

    un. Ajouter la `using` instruction :

            using Microsoft.Azure.Engagement;

    b. Initialisation le Kit de développement dans le `Application_Launching` méthode :

            private void Application_Launching(object sender, LaunchingEventArgs e)
            {
              EngagementAgent.Instance.Init();
            }

    c. Insérez le code suivant dans la `Application_Activated`:

            private void Application_Activated(object sender, ActivatedEventArgs e)
            {
               EngagementAgent.Instance.OnActivated(e);
            }

##<a id="monitor"></a>Activer le contrôle en temps réel

Avant de commencer l’envoi de données et de s’assurer que les utilisateurs sont actifs, vous devez envoyer au moins un écran (activité) sur le serveur principal Mobile Engagement.

1. Dans la MainPage.xaml.cs, ajoutez la `using` instruction :

        using Microsoft.Azure.Engagement;

2. Remplacez la classe de base de **MainPage**, c'est-à-dire avant **PhoneApplicationPage**, **EngagementPage**.

        class MainPage : EngagementPage 
    
3. Dans votre `MainPage.xml` fichier :

    un. Ajouter à vos espaces de noms déclarations :

         xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

    b. Remplacer `phone:PhoneApplicationPage` dans le nom de balise XML avec `engagement:EngagementPage`.

##<a id="monitor"></a>Application interrogez la surveillance en temps réel

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Activer les notifications push et dans l’application de messagerie

Engagement Mobile vous permet d’interagir et d’atteindre vos utilisateurs avec des Notifications de transmission et dans l’application de messagerie dans le contexte des campagnes marketing. Ce module est appelé accessibles dans le portail d’Engagement Mobile.
Les sections suivantes configurer votre application pour les recevoir.

###<a name="enable-your-app-to-receive-mpns-push-notifications"></a>Activer votre application de recevoir des Notifications de transmission MPNS

Ajouter de nouvelles fonctions à votre `WMAppManifest.xml` fichier :

        ID_CAP_PUSH_NOTIFICATION
        ID_CAP_WEBBROWSERCOMPONENT

   ![][5]

###<a name="initialize-the-reach-sdk"></a>Initialisation le Kit de développement accessibles

1. Dans `App.xaml.cs`, appelez `EngagementReach.Instance.Init();` de la fonction **Application_Launching** , avec le bouton droit après l’initialisation de l’agent :

        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
           EngagementAgent.Instance.Init();
           EngagementReach.Instance.Init();
        }

2. Dans `App.xaml.cs`, appelez `EngagementReach.Instance.OnActivated(e);` de la fonction **Application_Activated** , avec le bouton droit après l’initialisation de l’agent :

        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
           EngagementReach.Instance.OnActivated(e);
        }

Vous avez terminé. Nous allons maintenant vérifier que vous avez correctement crié déconnecter cette intégration simple.

##<a id="send"></a>Envoyer une notification à votre application

[AZURE.INCLUDE [Create Windows Push campaign](../../includes/mobile-engagement-windows-push-campaign.md)]

Normalement, une notification sur votre appareil qui s’afficheront sous la forme d’une notification dans l’application si l’application est ouverte dans le cas contraire sous forme de notification toast comme suit : 

![][6]

<!-- URLs. -->
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9874664
[Mobile Engagement Windows Phone SDK documentation]: ../mobile-engagement-windows-phone-integrate-engagement/

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[2]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[3]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[5]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[6]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png
