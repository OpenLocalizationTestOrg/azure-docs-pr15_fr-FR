<properties 
    pageTitle="Windows Phone intégration du Kit de développement logiciel Silverlight accessibles" 
    description="Comment intégrer accessibles Azure Engagement Mobile Windows Phone Silverlight applications"                    
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="na" 
    ms.topic="article"
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="windows-phone-silverlight-reach-sdk-integration"></a>Windows Phone intégration du Kit de développement logiciel Silverlight accessibles

Vous devez suivre la procédure d’intégration décrite dans l' [Intégration de Windows Phone Silverlight Engagement SDK](mobile-engagement-windows-phone-integrate-engagement.md) avant de suivre ce guide.

##<a name="embed-the-engagement-reach-sdk-into-your-windows-phone-silverlight-project"></a>Incorporer du Kit de développement atteigne Engagement dans votre projet Silverlight de Windows Phone

Vous n’avez pas lié à ajouter. `EngagementReach`références et ressources sont déjà présentes dans votre projet.

> [AZURE.TIP]  Vous pouvez personnaliser les images situé dans le `Resources` dossier de votre projet, notamment l’icône de marque (cette valeur par défaut sur l’icône d’Engagement).

##<a name="add-the-capabilities"></a>Ajouter les fonctionnalités

Le Kit de développement atteigne Engagement doit certaines fonctionnalités supplémentaires.

Ouvrir votre `WMAppManifest.xml` de fichiers et n’oubliez pas que les fonctionnalités suivantes sont déclarées :

-   `ID_CAP_PUSH_NOTIFICATION`
-   `ID_CAP_WEBBROWSERCOMPONENT`

La première partie est utilisée par le service MPNS pour permettre l’affichage de la notification toast. Le second exemple est utilisé pour incorporer une tâche de navigateur dans le Kit de développement.

Modifier la `WMAppManifest.xml` de fichier et l’ajouter à l’intérieur de la `<Capabilities />` balise :

    <Capability Name="ID_CAP_PUSH_NOTIFICATION" />
    <Capability Name="ID_CAP_WEBBROWSERCOMPONENT" />

##<a name="enable-the-microsoft-push-notification-service"></a>Activer le Service de Notification Push Microsoft

Pour pouvoir utiliser le **Service de Notification Push Microsoft** (appelé MPNS) votre `WMAppManifest.xml` fichier doit avoir un `<App />` balise avec un `Publisher` attribut défini sur le nom de votre projet.

##<a name="initialize-the-engagement-reach-sdk"></a>Initialisation de la portée d’Engagement SDK

### <a name="engagement-configuration"></a>Configuration d’engagement

La configuration d’Engagement centralisée dans le `Resources\EngagementConfiguration.xml` fichier de votre projet.

Modifier ce fichier pour spécifier la portée configuration :

-   *Facultatif*, indiquez si la diffusion native (MPNS) est activée ou non entre `<enableNativePush>` et `</enableNativePush>` balises, (`true` par défaut).
-   *Facultatif*, indiquer le nom du canal push entre `<channelName>` et `</channelName>` balises, fournissent la même que votre application peut actuellement utiliser ou laissez ce champ vide.

Si vous voulez spécifier à la place en cours d’exécution, vous pouvez appeler la méthode suivante avant l’initialisation de l’agent Engagement :

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
    
    engagementConfiguration.Reach.EnableNativePush = true;                  
    /* [Optional] whether the native push (MPNS) is activated or not. */
    
    engagementConfiguration.Reach.ChannelName = "YOUR_PUSH_CHANNEL_NAME";   
    /* [Optional] Provide the same channel name that your application may currently use. */
    
    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(engagementConfiguration);

> [AZURE.TIP] Vous pouvez spécifier le nom du canal push MPNS de votre application. Par défaut, Engagement crée un nom basé sur l’identificateur. Vous n’avez pas besoin pour spécifier le nom vous-même, sauf si vous prévoyez d’utiliser le canal push en dehors d’Engagement.

### <a name="engagement-initialization"></a>Échec de l’initialisation engagement

Modifier la `App.xaml.cs`:

-   Ajouter à votre `using` instructions :

        using Microsoft.Azure.Engagement;

-   Insérer `EngagementReach.Instance.Init` juste après `EngagementAgent.Instance.Init` dans `Application_Launching` :

        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
           EngagementAgent.Instance.Init();
           EngagementReach.Instance.Init();
        }

-   Insérer `EngagementReach.Instance.OnActivated` dans les `Application_Activated` méthode :

        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
           EngagementReach.Instance.OnActivated(e);
        }

> [AZURE.IMPORTANT] La `EngagementReach.Instance.Init` s’exécute dans un thread dédié. Vous n’avez pas à effectuer vous-même.

##<a name="app-store-submission-considerations"></a>Considérations relatives à la présentation App store

Microsoft impose certaines règles lorsque vous utilisez les notifications push :

Dans la documentation de Microsoft [Application stratégies] , section 2,9 :

1) Vous devez demander à l’utilisateur à accepter pour recevoir des notifications de transmission. Ajoutez ensuite un moyen de désactiver les notifications push dans vos paramètres.

L’objet EngagementReach fournit deux méthodes pour gérer l’inscription-dans/annulations, `EnableNativePush()` et `DisableNativePush()`. Vous pouvez, par exemple, créer une option dans les paramètres avec un bouton bascule pour désactiver ou activer MPNS.

Vous pouvez également décider de désactiver MPNS grâce à la configuration d’Engagement\<configuration du accessibles Kit de développement logiciel windows phone\>.

> 2.9.1) l’application doit décrire tout d’abord les notifications doivent être fournies et **obtenir l’autorisation express de l’utilisateur (participer)**et **doit fournir un mécanisme par lequel l’utilisateur peut cesser d’utiliser recevoir des notifications push**. Toutes les notifications fournies à l’aide du Service de Notification Push Microsoft doivent être conformes à la description fournie à l’utilisateur et doivent être conformes applicables toutes les [Stratégies d’Application]  [ Content Policies] et [Configuration requise supplémentaire pour des Types d’Application spécifiques].

2) Vous ne devez pas utiliser trop de notifications de transmission. Engagement gérera les notifications pour vous.

> 2.9.2) l’application et son utilisation du Service de Notification Push Microsoft ne doivent pas trop utiliser la capacité réseau ou la bande passante de Service de Notification Push Microsoft, ou dans le cas contraire trop périphérique libère un Windows Phone ou autre dispositif de Microsoft ou service avec les notifications de transmission excessive, tel que déterminé par Microsoft à sa seule discrétion raisonnablement et devez pas endommager ou interférer avec des réseaux Microsoft ou serveurs ou les serveurs tiers ou les réseaux connectés au Service de Notification Push Microsoft.

3) Ne comptez pas sur MPNS pour envoyer des informations critiques et. Engagement utilise MPNS, donc si cette règle s’applique également pour les campagnes marketing créées à l’intérieur de l’Engagement frontale.

> 2.9.3) le Service de Notification Push Microsoft peut ne pas être utilisé pour envoyer des notifications sont caractère crucial ou autrement peuvent affecter les questions de vie ou de mort, y compris et sans notifications importantes limitation relatives à un dispositif médical ou condition. MICROSOFT EXCLUT EXPRESSÉMENT TOUTE GARANTIE QUE L’UTILISATION DE LA DIFFUSION DE MICROSOFT SERVICE DE NOTIFICATION OU LA LIVRAISON DES NOTIFICATIONS DE SERVICE DE NOTIFICATION PUSH MICROSOFT SERA SANS INTERRUPTION, SANS ERREUR OU AUTREMENT ASSURÉ DE SE PRODUIRE EN TEMPS RÉEL.

**Nous ne pouvons pas vous assurer que votre application passera le processus de validation si vous ne respectez pas ces recommandations.**

##<a name="handle-data-push-optional"></a>Poignée de données push (facultatives)

Si vous souhaitez que votre application doit pouvoir recevoir des campagnes de données accessibles, vous devez mettre en œuvre des deux événements de la classe EngagementReach :

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

##<a name="customize-ui-optional"></a>Personnaliser l’interface utilisateur (facultatif)

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

Ensuite, définissez le contenu de la `EngagementReach.Instance.Handler` champ avec votre objet personnalisé dans votre `App.xaml.cs` classe au sein de la `Application_Launching` méthode.

**Exemples de Code :**

    private void Application_Launching(object sender, LaunchingEventArgs e)
    {
       // your app initialization 
       EngagementReach.Instance.Handler = new ExampleReachHandler();
       // Engagement Agent and Reach initialization
    }

> [AZURE.NOTE] Par défaut, Engagement utilise sa propre implémentation de `EngagementReachHandler`. Vous n’êtes pas obligé de créer votre propre liste, et si vous procédez ainsi, vous n’êtes pas obligé de substituer chaque méthode. Le comportement par défaut consiste à sélectionner l’objet de base d’Engagement.

### <a name="layouts"></a>Mises en page

Par défaut, accessibles utilisera les ressources de la DLL incorporés pour afficher les notifications et les pages.

Toutefois, vous pouvez décider d’utiliser vos propres ressources pour refléter la marque dans ces composants.

Vous pouvez ignorer `EngagementReachHandler` méthodes dans votre sous-classe pour indiquer à Engagement à utiliser vos mises en page :

**Exemples de Code :**

    // In your subclass of EngagementReachHandler
    
    public override string GetTextViewAnnouncementUri()
    {
       // return the path of your own xaml
    }
    
    public override string GetWebViewAnnouncementUri()
    {
       // return the path of your own xaml
    }
    
    public override string GetPollUri()
    {
       // return the path of your own xaml
    }
    
    public override EngagementNotificationView CreateNotification(EngagementNotificationViewModel viewModel)
    {
       // return a new instance of your own notification
    }

> [AZURE.TIP] La `CreateNotification` méthode peut retourner null. La notification n’est pas affichée et la campagne portée est supprimée.

Pour simplifier l’implémentation de votre présentation, nous fournissons également notre propre xaml qui peut servir de base pour votre code. Ils sont situés dans l’archive Engagement SDK (/ src/portée /).

> [AZURE.WARNING] Les sources que nous proposons sont les mêmes que nous utilisons. Afin que si vous voulez les modifier directement, n’oubliez pas de modifier l’espace de noms et le nom.

### <a name="notification-position"></a>Position de notification

Par défaut, une notification dans l’application est affichée en l’angle inférieur gauche de l’application. Vous pouvez modifier ce comportement en substituant la `GetNotificationPosition` méthode de le `EngagementReachHandler` objet.

    // In your subclass of EngagementReachHandler
    
    public override EngagementReachHandler.NotificationPosition GetNotificationPosition(EngagementNotificationViewModel viewModel)
    {
       // return a value of the EngagementReachHandler.NotificationPosition enum (TOP or BOTTOM)
    }

Pour l’instant, vous pouvez choisir entre la `BOTTOM` (par défaut) et `TOP` positions.

### <a name="launch-message"></a>Lancement de message

Lorsqu’un utilisateur clique sur une notification système (un toast), Engagement lance l’application, charger le contenu des messages push et afficher la page de la campagne correspondante.

Il y a un délai entre le menu de lancement de l’application et l’affichage de la page (selon la vitesse de votre réseau).

Pour indiquer à l’utilisateur que quelque chose se charge, vous devez fournir une informations visuelles, par exemple, une barre de progression ou un indicateur de progression. Engagement ne peuvent pas gérer que lui-même, mais fournit les gestionnaires quelques pour vous.

Pour mettre en œuvre le rappel, effectuez :

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

Vous pouvez définir le rappel dans votre `Application_Launching` méthode de votre `App.xaml.cs` fichier préférence avant le `EngagementReach.Instance.Init()` appeler.

> [AZURE.TIP] Chaque gestionnaire est appelé par le Thread de l’interface utilisateur. Vous n’avez pas à vous soucier lorsque vous utilisez un MessageBox ou quelque chose liés à l’interface utilisateur.

[Stratégies d’application]:http://msdn.microsoft.com/library/windows/apps/hh184841(v=vs.105).aspx
[Content Policies]:http://msdn.microsoft.com/library/windows/apps/hh184842(v=vs.105).aspx
[Configuration requise supplémentaire pour les Types d’Application spécifique]:http://msdn.microsoft.com/library/windows/apps/hh184838(v=vs.105).aspx
 
