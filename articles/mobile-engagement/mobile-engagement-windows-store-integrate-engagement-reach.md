<properties 
    pageTitle="Intégration de kit de développement Windows universel applications accessibles" 
    description="Comment intégrer Azure Engagement Mobile accessibles aux applications universel Windows"
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

# <a name="windows-universal-apps-reach-sdk-integration"></a>Intégration de kit de développement Windows universel applications accessibles

Vous devez suivre la procédure d’intégration décrite dans l' [Intégration du Kit de développement Windows universel Engagement](mobile-engagement-windows-store-integrate-engagement.md) avant de suivre ce guide.

## <a name="embed-the-engagement-reach-sdk-into-your-windows-universal-project"></a>Incorporer du Kit de développement atteigne Engagement dans votre projet Windows universel

Vous n’avez pas lié à ajouter. `EngagementReach`références et ressources sont déjà présentes dans votre projet.

> [AZURE.TIP] Vous pouvez personnaliser les images situé dans le `Resources` dossier de votre projet, notamment l’icône de marque (cette valeur par défaut sur l’icône d’Engagement). Dans des applications universel vous pouvez également déplacer la `Resources` dossier sur votre projet partagé pour partager le contenu entre les applications, mais vous devrez conserver la `Resources\EngagementConfiguration.xml` de fichiers de son emplacement par défaut qu’il est dépendant de la plate-forme.

## <a name="enable-the-windows-notification-service"></a>Activer le Service de Notification Windows

### <a name="windows-8x-and-windows-phone-81-only"></a>Windows 8.x et Windows Phone 8.1 uniquement

Pour pouvoir utiliser le **Service de Notification de Windows** (appelée WNS) dans votre `Package.appxmanifest` de fichiers `Application UI` cliquez sur `All Image Assets` dans la zone gauche robots. À droite de la zone `Notifications`, modifier `toast capable` de `(not set)` à `(Yes)`.

### <a name="all-platforms"></a>Toutes les plateformes

Vous devez synchroniser votre application pour votre compte Microsoft et la plateforme engagement. Pour cela, vous devez créer un compte ou connectez-vous au [Centre de développement windows](https://dev.windows.com). Après cela, créez une nouvelle application et trouver l’identificateur de sécurité et la clé secrète. Sur le site Web frontal engagement, allez sur la configuration de votre application dans `native push` et coller vos informations d’identification. Ensuite, avec le bouton droit, cliquez sur votre projet, sélectionnez `store` et `Associate App with the Store...`. Vous devez simplement sélectionner l’application que vous avez créer avant de la pour synchroniser.

## <a name="initialize-the-engagement-reach-sdk"></a>Initialisation de la portée d’Engagement SDK

Modifier la `App.xaml.cs`:

-   Insérer `EngagementReach.Instance.Init` juste après `EngagementAgent.Instance.Init` dans votre `InitEngagement` méthode :

        private void InitEngagement(IActivatedEventArgs e)
        {
          EngagementAgent.Instance.Init(e);
          EngagementReach.Instance.Init(e);
        }

    La `EngagementReach.Instance.Init` s’exécute dans un thread dédié. Vous n’avez pas à effectuer vous-même.

> [AZURE.NOTE] Si vous utilisez des notifications push ailleurs dans votre application vous devez [partager votre canal push](#push-channel-sharing) avec Engagement atteindre.

## <a name="integration"></a>Intégration

Engagement propose deux méthodes pour ajouter la portée dans l’application bannières et les affichages INTERSTITIELLES pour les annonces et les sondages dans votre application : l’intégration de superposition et l’intégration de manuel affichages web. Vous ne devez pas combiner les deux approche sur la même page.

Le choix entre l’intégration deux peut se résumer ainsi :

-   Vous pouvez choisir l’intégration superposition si vos pages hérite déjà de l’Agent de `EngagementPage`, il est juste une question de remplacement `EngagementPage` par `EngagementPageOverlay` et `xmlns:engagement="using:Microsoft.Azure.Engagement"` par `xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"` dans vos pages.
-   Vous pouvez choisir les vues web intégration manuelle si vous souhaitez placer précisément l’interface utilisateur atteindre dans vos pages ou si vous ne voulez pas ajouter un autre niveau de l’héritage à vos pages. 

### <a name="overlay-integration"></a>Intégration de superposition

La superposition Engagement ajoute dynamiquement les éléments d’interface utilisateur utilisés pour afficher les campagnes accessibles dans votre page. Si la superposition ne correspondent pas à votre disposition que vous devez prendre en compte les vues web intégration manuelle à la place.

Dans votre modification du fichier .xaml `EngagementPage` fait référence à`EngagementPageOverlay`

-   Ajouter à vos espaces de noms déclarations :

        xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"

-   Remplacer `engagement:EngagementPage` avec `engagement:EngagementPageOverlay`:

**Avec EngagementPage :**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">
        
            <!-- Your layout -->
        </engagement:EngagementPage>

**Avec EngagementPageOverlay :**

        <engagement:EngagementPageOverlay 
            xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">
        
            <!-- Your layout -->
        </engagement:EngagementPageOverlay>

Puis, dans votre fichier .cs baliser votre page dans `EngagementPageOverlay` au lieu de `EngagementPage` et importer `Microsoft.Azure.Engagement.Overlay`.

            using Microsoft.Azure.Engagement.Overlay;

-   Remplacer `EngagementPage` avec `EngagementPageOverlay`:

**Avec EngagementPage :**

            using Microsoft.Azure.Engagement;
            
            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPage
              {
                [...]
              }
            }

**Avec EngagementPageOverlay :**

            using Microsoft.Azure.Engagement.Overlay;
            
            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPageOverlay 
              {
                [...]
              }
            }


La superposition Engagement ajoute un `Grid` élément en haut de votre page composé de votre mise en page et les deux `WebView` éléments celui de la bannière et l’autre pour l’affichage INTERSTITIELLE.

Vous pouvez personnaliser les éléments superposition directement dans le `EngagementPageOverlay.cs` fichier.

### <a name="web-views-manual-integration"></a>Intégration des affichages manuelle Web

Portée recherchent vos pages pour les deux `WebView` éléments chargés pour l’affichage de la bannière et l’affichage INTERSTITIELLE. La seule chose que vous avez à faire est d’ajouter ces deux `WebView` éléments n’importe où dans vos pages, Voici un exemple :

    <Grid x:Name="engagementGrid">

      <!-- Your layout -->

      <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Stretch" VerticalAlignment="Top"/>
      <WebView x:Name="engagement_announcement_content" Visibility="Collapsed"  HorizontalAlignment="Stretch"  VerticalAlignment="Stretch"/>
    </Grid>


Dans cet exemple la `WebView` éléments sont étirés pour l’ajuster à son conteneur qui ré-redimensionne automatiquement les en cas de changement de taille de rotation ou fenêtre de l’écran.

> [AZURE.WARNING] Il est important de maintenir l’appellation même `engagement_notification_content` et `engagement_announcement_content` pour la `WebView` éléments. Atteindre les identifie par son nom. 

## <a name="handle-datapush-optional"></a>Poignée datapush (facultatif)

Si vous souhaitez que votre application doit pouvoir recevoir des campagnes de données accessibles, vous devez mettre en œuvre des deux événements de la classe EngagementReach :

Dans App.xaml.cs dans le constructeur App() ajouter :

            EngagementReach.Instance.DataPushStringReceived += (body) =>
            {
              Debug.WriteLine("String data push message received: " + body);
              return true;
            };
            
            EngagementReach.Instance.DataPushBase64Received += (decodedBody, encodedBody) =>
            {
              Debug.WriteLine("Base64 data push message received: " + encodedBody);
              // Do something useful with decodedBody like updating an image view
              return true;
            };

Vous pouvez voir que le rappel de chaque méthode renvoie une valeur booléenne. Engagement envoie un commentaire à sa principale après la transmission de la diffusion de données. Si le rappel renvoie la valeur false, la `exit` commentaires sera envoyé. Dans le cas contraire, il sera `action`. Si aucun rappel n’est défini pour les événements, la `drop` commentaires seront ramené à Engagement.

> [AZURE.WARNING] Engagement n’est pas en mesure de recevoir des commentaires de multiples pour une diffusion de données. Si vous souhaitez définir plusieurs gestionnaires d’un événement, n’oubliez pas que les commentaires correspond à la dernière une envoyé. Dans ce cas, nous vous recommandons de toujours retourne la même valeur pour éviter de devoir confusion commentaires sur le serveur frontal.

## <a name="customize-ui-optional"></a>Personnaliser l’interface utilisateur (facultatif)

### <a name="first-step"></a>Première étape

Nous permettent de personnaliser la portée de l’interface utilisateur.

Pour ce faire, vous devez créer une sous-classe de la `EngagementReachHandler` cours.

**Exemples de Code :**

            using Microsoft.Azure.Engagement;
            
            namespace Example
            {
              internal class ExampleReachHandler : EngagementReachHandler
              {
               // Override EngagementReachHandler methods depending on your needs
              }
            }

Ensuite, définissez le contenu de la `EngagementReach.Instance.Handler` champ avec votre objet personnalisé dans votre `App.xaml.cs` classe au sein de la `App()` méthode.

**Exemples de Code :**

            protected override void OnLaunched(LaunchActivatedEventArgs args)
            {
              // your app initialization 
              EngagementReach.Instance.Handler = new ExampleReachHandler();
              // Engagement Agent and Reach initialization
            }

> [AZURE.NOTE]Par défaut, Engagement utilise sa propre implémentation de `EngagementReachHandler`.
> Vous n’êtes pas obligé de créer votre propre liste, et si vous procédez ainsi, vous n’êtes pas obligé de substituer chaque méthode. Le comportement par défaut consiste à sélectionner l’objet de base d’Engagement.

### <a name="web-view"></a>Mode Web

Par défaut, accessibles utilisera les ressources de la DLL incorporés pour afficher les notifications et les pages.

Pour fournir une personnalisation complète possibilités nous n’utilisons que l’affichage web. Si vous voulez personnaliser des mises en page, substituer directement les fichiers de ressources `EngagementAnnouncement.html` et `EngagementNotification.html`. Engagement doit tout le code dans `<body></body>` s’exécutent correctement. Mais vous pouvez ajouter des balises externe `engagement_webview_area`.

Toutefois, vous pouvez décider d’utiliser vos propres ressources.

Vous pouvez ignorer `EngagementReachHandler` méthodes dans votre sous-classe pour indiquer à Engagement utiliser vos mises en page, mais veiller à incorporé le mécanisme engagement :

**Exemples de Code :**
            
            // In your subclass of EngagementReachHandler
            
            public override string GetAnnouncementHTML()
            {
              return base.GetAnnouncementHTML();
            }
            public override string GetAnnouncementName()
            {
              return base.GetAnnouncementName();
            }
            public override string GetNotfificationHTML()
            {
              return base.GetNotfificationHTML();
            }
            public override string GetNotfificationName()
            {
              return base.GetNotfificationName();
            }


Par défaut, AnnouncementHTML est `ms-appx-web:///Resources/EngagementAnnouncement.html`. Il représente le fichier html concevoir le contenu d’un message push (annonce de texte, anoucement Web et annonce de sondage). AnnouncementName est `engagement_announcement_content`. Il est le nom de la conception du mode d’affichage Web dans votre page xaml.

NotfificationHTML est `ms-appx-web:///Resources/EngagementNotification.html`. Il représente le fichier html concevoir la notification d’un message envoyé. NotfificationName est `engagement_notification_content`. Il est le nom de la conception du mode d’affichage Web dans votre page xaml.

### <a name="customization"></a>Personnalisation

Vous pouvez personnaliser de notification et d’annonce affichage web a de choix si vous conservez objet Engagement. Ce mode d’affichage Web une attention objet est décrit trois fois - la première fois dans votre xaml, le deuxième fois dans votre fichier .cs dans la méthode « setwebview() » et l’heure troisième dans le fichier html.

-   Dans votre xaml vous décrire le composant d’affichage Web disposition graphique en cours.
-   Dans votre fichier .cs, vous pouvez définir « setwebview() » dans lequel vous définissez les dimensions de l’affichage Web deux (de notification, annonce). Il est très efficace lors du redimensionne de l’application.
-   Dans le fichier html Engagement nous décrivent le contenu de l’affichage Web, conception ainsi que les positions des éléments entre eux.

### <a name="launch-message"></a>Lancement de message

Lorsqu’un utilisateur clique sur une notification système (un toast), Engagement lance l’application, charger le contenu des messages push et afficher la page de la campagne correspondante.

Il y a un délai entre le menu de lancement de l’application et l’affichage de la page (selon la vitesse de votre réseau).

Pour indiquer à l’utilisateur que quelque chose se charge, vous devez fournir une informations visuelles, par exemple, une barre de progression ou un indicateur de progression. Engagement ne peuvent pas gérer que lui-même, mais fournit les gestionnaires quelques pour vous.

Pour mettre en œuvre le rappel, dans App.xaml.cs dans « {} App() Public » ajouter :

            /* The application has launched and the content is loading.
             * You should display an indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageStarted += () => { [...] };
            
            /* The application has finished loading the content and the page
             * is about to be displayed.
             * You should hide the indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageCompleted += () => { [...] };
            
            /* The content has been loaded, but an error has occurred.
             * You can provide an information to the user.
             * You should hide the indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageFailed += () => { [...] };

Vous pouvez définir le rappel dans votre méthode « {} App() Public » de votre `App.xaml.cs` fichier préférence avant le `EngagementReach.Instance.Init()` appeler.

> [AZURE.TIP] Chaque gestionnaire est appelé par le Thread de l’interface utilisateur. Vous n’avez pas à vous soucier lorsque vous utilisez un MessageBox ou quelque chose liés à l’interface utilisateur.

##<a id="push-channel-sharing"></a>Appuyez sur le partage de canal

Si vous utilisez les notifications push pour un autre objet dans votre application vous devez utiliser le canal push fonctionnalité du Kit de développement Engagement de partage. Il s’agit afin d’éviter push manquée.

- Vous pouvez fournir votre propre canal push pour l’initialisation Engagement atteindre. Le Kit de développement utilisera au lieu de demander une nouvelle.

Mettre à jour l’initialisation atteigne Engagement avec votre canal épingle dans le `InitEngagement` méthode à partir de la `App.xaml.cs` fichier :
    
    /* Your own push channel logic... */
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    
    /*...Engagement initialization */
    EngagementAgent.Instance.Init(e);
    EngagementReach.Instance.Init(e,pushChannel);

- Par ailleurs, si vous voulez simplement consommer le canal push après que l’initialisation de la portée, vous pouvez définir un rappel sur Engagement atteigne pour obtenir une fois le canal push, il est créé par le Kit de développement.

Définir votre rappel en tout lieu **après** l’initialisation de la portée :

    /* Set action on the SDK push channel. */
    EngagementReach.Instance.SetActionOnPushChannel((PushNotificationChannel channel) => 
    {
      /* The forwarded channel can be null if its creation fails for any reason. */
      if (channel != null)
      {
        /* Your own push channel logic... */
      });
    }

## <a name="custom-scheme-tip"></a>Conseil de couleurs personnalisé

Nous fournissons utilisation de couleurs personnalisé. Vous pouvez envoyer autre type d’URI d’engagement frontend à utiliser dans votre application engagement. Modèle par défaut comme `http, ftp, ...` gérer par Windows, une fenêtre fonctionnera invite s’ils ne sont aucune application par défaut est installée sur l’appareil. Vous pouvez également créer votre propre modèle personnalisé pour votre application.

Pour définir un modèle personnalisé dans votre application de façon simple consiste à ouvrir votre `Package.appxmanifest` accédez dans `Declarations` Panneau de configuration. Sélectionnez `Protocol` dans les déclarations disponible faire défiler zone et l’ajouter. Modifier la `Name` champ avec votre nouveau protocole le nom voulu.

Maintenant pour utiliser ce protocole, modifier votre `App.xaml.cs` avec la `OnActivated` méthode, n’oubliez pas d’initialisation engagement ici également :

            /// <summary>
            /// Enter point when app his called by another way than user click
            /// </summary>
            /// <param name="args">Activation args</param>
            protected override void OnActivated(IActivatedEventArgs args)
            {
              /* Init engagement like it was launch by a custom uri scheme */
              EngagementAgent.Instance.Init(args);
              EngagementReach.Instance.Init(args);
            
              //TODO design action to do when app is launch
            
              #region Custom scheme use
              if (args.Kind == ActivationKind.Protocol)
              {
                ProtocolActivatedEventArgs myProtocol = (ProtocolActivatedEventArgs)args;
            
                if (myProtocol.Uri.Scheme.Equals("protocolName"))
                {
                  string path = myProtocol.Uri.AbsolutePath;
                }
              }
              #endregion
 
