<properties 
    pageTitle="Procédures de mise à niveau universel SDK applications Windows" 
    description="Procédures de mise à niveau universel SDK applications Windows pour Azure Engagement Mobile"                     
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-store" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="windows-universal-apps-sdk-upgrade-procedures"></a>Procédures de mise à niveau universel SDK applications Windows

Si vous avez déjà intégré une version antérieure d’Engagement dans votre application, vous devez prendre en compte les points suivants lorsque le Kit de développement de la mise à niveau.

Vous devrez quelques procédures à suivre si vous avez manqué plusieurs versions du Kit de développement. Par exemple, si vous migrez à partir de 0.10.1 vers 0.11.0, que vous devez tout d’abord, procédez comme « de 0.9.0 à 0.10.1 » ensuite la procédure « de 0.10.1 à 0.11.0 ».

##<a name="from-330-to-340"></a>À partir de 3.3.0 à 3.4.0

### <a name="test-logs"></a>Tester les journaux

Journaux de console obtenus par le Kit de développement peuvent désormais être activé, désactivé/filtrés. Pour personnaliser ce, mettre à jour la propriété `EngagementAgent.Instance.TestLogEnabled` à un de la valeur disponible à partir de la `EngagementTestLogLevel` énumération, par exemple :

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="resources"></a>Ressources

La superposition portée a été améliorée. Il fait partie des ressources de package NuGet SDK.

Pendant la mise à niveau vers la nouvelle version du Kit de développement vous pouvez choisir si vous souhaitez conserver vos fichiers à partir du dossier superposition de vos ressources ou non :

* Si la superposition précédente fonctionne pour vous ou vous intégrez les `WebView` éléments manuellement puis vous pouvez choisir de conserver vos fichiers de sortie, elle fonctionnera toujours. 
* Si vous voulez mettre à jour vers la nouvelle superposition, remplacez simplement la totalité `overlay` dossier à partir de vos ressources par le nouveau à partir du package Kit de développement logiciel (applications UWP : après la mise à niveau, vous pouvez obtenir le nouveau dossier superposition de %USERPROFILE%\\.nuget\packages\MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources).

> [AZURE.WARNING] À l’aide de la nouvelle superposition remplacera toutes les personnalisations apportées à la version précédente.

##<a name="from-320-to-330"></a>À partir de 3.2.0 à 3.3.0

### <a name="resources"></a>Ressources
Cette étape concerne uniquement les ressources personnalisées. Si vous avez personnalisé les ressources fournies par le Kit de développement logiciel (html, images, superposition) vous devez les sauvegarder avant la mise à niveau et réappliquer votre personnalisation sur les ressources mis à niveau.

##<a name="from-310-to-320"></a>À partir de 3.1.0 à 3.2.0

### <a name="resources"></a>Ressources
Cette étape concerne uniquement les ressources personnalisées. Si vous avez personnalisé les ressources fournies par le Kit de développement logiciel (html, images, superposition) vous devez les sauvegarder avant la mise à niveau et réappliquer votre personnalisation sur les ressources mis à niveau.

### <a name="webview-integration"></a>Intégration d’affichage Web
Les améliorations apportées aux correspond à des facteurs de forme autre appareil introduites dans cette version. Assurez-vous que l’intégration de l’affichage Web correspondent aux opérations suivantes :

Dans votre page XAML () :

            <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Right" VerticalAlignment="Top"/>
            <WebView x:Name="engagement_announcement_content" Visibility="Collapsed" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

Et dans votre fichier .cs associées :

    using Microsoft.Azure.Engagement;
    using System;
    using Windows.ApplicationModel.Core;
    using Windows.UI.ViewManagement;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Navigation;

    namespace My.Namespace.Example
    {
            /// <summary>
            /// An empty page that can be used on its own or navigated to within a Frame.
            /// </summary>
            public sealed partial class ExampleEngagementReachPage : EngagementPage
            {
              public ExampleEngagementReachPage()
              {
                this.InitializeComponent();
            
                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }
            
              #region to implement
              /* Attach events when page is navigated. */
              protected override void OnNavigatedTo(NavigationEventArgs e)
              {
                /* Update the webview when the app window is resized. */
                Window.Current.SizeChanged += DisplayProperties_OrientationChanged;

                /* Update the webview when the app/status bar is resized. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged += DisplayProperties_VisibleBoundsChanged; 
    #endif
                base.OnNavigatedTo(e);
              }

              /* When page is left ensure to detach SizeChanged handler. */
              protected override void OnNavigatedFrom(NavigationEventArgs e)
              {
                Window.Current.SizeChanged -= DisplayProperties_OrientationChanged;
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged -= DisplayProperties_VisibleBoundsChanged;
    #endif
                base.OnNavigatedFrom(e);
              }
              
              /* "width" and "height" are the current size of your application display. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
              double width = ApplicationView.GetForCurrentView().VisibleBounds.Width;
              double height = ApplicationView.GetForCurrentView().VisibleBounds.Height;
    #else
              double width =  Window.Current.Bounds.Width;
              double height =  Window.Current.Bounds.Height;
    #endif
            
              /// <summary>
              /// Set your webview elements to the correct size.
              /// </summary>
              /// <param name="width">The width of your current display.</param>
              /// <param name="height">The height of your current display.</param>
              private void SetWebView(double width, double height)
              {
                #pragma warning disable 4014
                CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
                        () =>
                        {
                          this.engagement_notification_content.Width = width;
                          this.engagement_announcement_content.Width = width;
                          this.engagement_announcement_content.Height = height;
                        });
              }
            
              /// <summary>
              /// Handler that takes the Windows.Current.SizeChanged and indicates that webviews have to be resized.
              /// </summary>
              /// <param name="sender">Original event trigger.</param>
              /// <param name="e">Window Size Changed Event arguments.</param>
              private void DisplayProperties_OrientationChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
              {
                double width = e.Size.Width;
                double height = e.Size.Height;
            
                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }

    #if WINDOWS_PHONE_APP || WINDOWS_UWP              
              /// <summary>
              /// Handler that takes the ApplicationView.VisibleBoundsChanged and indicates that webviews have to be resized
              /// </summary>
              /// <param name="sender">The related application view.</param>
              /// <param name="e">Related event arguments.</param>
              private void DisplayProperties_VisibleBoundsChanged(ApplicationView sender, Object e)
              {
                double width = sender.VisibleBounds.Width;
                double height = sender.VisibleBounds.Height;
            
                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }
    #endif
              #endregion
            }
    }

##<a name="from-200-to-300"></a>À partir de 2.0.0 à 3.0.0

### <a name="resources"></a>Ressources
Cette étape concerne uniquement les ressources personnalisées. Si vous avez personnalisé les ressources fournies par le Kit de développement logiciel (html, images, superposition) vous devez les sauvegarder avant la mise à niveau et réappliquer votre personnalisation sur les ressources mis à niveau.

##<a name="from-111-to-200"></a>À partir de 1.1.1 à 2.0.0

Voici comment migrer une intégration SDK à partir du service Capptain offert par Capptain SAS dans une application optimisée par Azure Mobile Engagement. 

> [Azure.IMPORTANT] Capptain et Engagement Mobile ne sont pas les mêmes services et la procédure décrite ci-dessous clairs uniquement comment effectuer une migration de l’application client. Migrer le Kit de développement dans l’application ne doit pas migrer vos données à partir des serveurs Capptain aux serveurs Engagement Mobile

Si vous effectuez une migration depuis une version antérieure, consultez le site web Capptain pour migrer vers 1.1.1 tout d’abord, puis appliquez la procédure suivante

### <a name="nuget-package"></a>Package NuGet

Remplacez **Capptain.WindowsPhone** par **MicrosoftAzure.MobileEngagement** Nuget package.

### <a name="applying-mobile-engagement"></a>Application d’Engagement Mobile

Le Kit de développement utilise le terme `Engagement`. Vous devez mettre à jour votre projet pour correspondre à cette modification.

Vous devez désinstaller votre package nuget Capptain en cours. Envisagez de toutes vos modifications dans le dossier Capptain ressources sont supprimées. Si vous souhaitez conserver ces fichiers puis copiez-les d'entre eux.

Après cela, installez le nouveau package nuget Microsoft Azure Engagement sur votre projet. Vous pouvez trouver directement sur [nuget site Web]. ou index ici. Cette action remplace tous les fichiers de ressources utilisées par Engagement et ajoute la nouvelle DLL Engagement à vos références de projet.

Vous devez effacer les références de votre projet en supprimant les références Capptain DLL. Si vous n’effectuez pas cette option, la version de Capptain créera un conflit et erreurs seront produit.

Si vous avez personnalisé Capptain ressources, copiez le contenu de votre ancien fichiers et collez-les dans les nouveaux fichiers Engagement. Notez que les fichiers xaml et SC doivent être mis à jour.

Une fois ces étapes terminées, il vous suffit de remplacer les anciennes références Capptain par les nouvelles références Engagement.

1. Tous les espaces de noms Capptain doivent être mis à jour.

    Avant la migration :
    
        using Capptain.Agent;
        using Capptain.Reach;
    
    Après la migration :
    
        using Microsoft.Azure.Engagement;

2. Toutes les classes Capptain qui contiennent « Capptain » doivent contenir « Engagement ».

    Avant la migration :
    
        public sealed partial class MainPage : CapptainPage
        {
          protected override string GetCapptainPageName()
          {
            return "Capptain Demo";
          }
          ...
        }
    
    Après la migration :
    
        public sealed partial class MainPage : EngagementPage
        {
          protected override string GetEngagementPageName()
          {
            return "Engagement Demo";
          }
          ...
        }

3. Pour les fichiers xaml attributs et espace de noms Capptain également modifier.

    Avant la migration :
    
        <capptain:CapptainPage
        ...
        xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
        ...
        </capptain:CapptainPage>
    
    Après la migration :
    
        <engagement:EngagementPage
        ...
        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
        ...
        </engagement:EngagementPage>

4. Superposition de modifications de la page
    > [AZURE.IMPORTANT] Superposition change également. Son nouvel espace de noms est `Microsoft.Azure.Engagement.Overlay`. Il doit être utilisé dans les fichiers xaml et SC. En outre `CapptainGrid` doit être nommé `EngagementGrid`, `capptain_notification_content` et `capptain_announcement_content` sont nommés `engagement_notification_content` et `engagement_announcement_content`.
    
    Pour superposition :
    
        <capptain:CapptainPageOverlay
          xmlns:capptain="using:Capptain.Overlay"
          ...
        </capptain:CapptainPageOverlay>
    
    Il se transforme en :
    
        <EngagementPageOverlay
          engagement="using:Microsoft.Azure.Engagement.Overlay"
          ...
        </engagement:EngagementPageOverlay>

5. Pour les autres ressources comme des images Capptain et les fichiers HTML, notez qu’ils également ont été renommés pour utiliser « Engagement ».

### <a name="project-declaration"></a>Déclaration de projet

Sous Package.appxmanifest `File Type Associations` a été mis à jour à partir de :

 -   capptain\_atteigne\_le contenu afin d’engagement\_atteigne\_contenu
 -   capptain\_journal\_fichier à engagement\_journal\_fichier

### <a name="application-id--sdk-key"></a>ID de l’application / clé SDK

Engagement utilise une chaîne de connexion. Vous n’êtes pas obligé de spécifier un ID de l’application et une touche SDK avec Engagement Mobile, vous suffit spécifier une chaîne de connexion. Vous pouvez configurer celui-ci sur votre fichier EngagementConfiguration.

La configuration d’Engagement pouvant être définie dans votre `Resources\EngagementConfiguration.xml` fichier de votre projet.

Modifier ce fichier pour spécifier :

-   Chaîne de connexion votre application entre balises `<connectionString>` et `<\connectionString>`.

Si vous voulez spécifier à la place en cours d’exécution, vous pouvez appeler la méthode suivante avant l’initialisation de l’agent Engagement :

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
    
    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(args, engagementConfiguration);

La chaîne de connexion pour votre application s’affiche dans le portail classique Azure.

### <a name="items-name-change"></a>Modification du nom des éléments

Tous les éléments nommés *capptain* ont été nommées *engagement*. De même pour *Capptain* à *Engagement*.

Exemples d’éléments Capptain fréquemment utilisées :

-   CapptainConfiguration nommé maintenant EngagementConfiguration
-   CapptainAgent nommé maintenant EngagementAgent
-   CapptainReach nommé maintenant EngagementReach
-   CapptainHttpConfig nommé maintenant EngagementHttpConfig
-   GetCapptainPageName nommé maintenant GetEngagementPageName

Notez que renommer concerne également les méthodes remplacées.

 
