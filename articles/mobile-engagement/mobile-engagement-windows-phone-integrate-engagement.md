<properties 
    pageTitle="Windows Phone SDK Silverlight Engagement intégration" 
    description="Comment intégrer Azure Engagement Mobile Windows Phone Silverlight applications"                  
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="windows-phone-silverlight-engagement-sdk-integration"></a>Windows Phone SDK Silverlight Engagement intégration

> [AZURE.SELECTOR] 
- [Universel de Windows](mobile-engagement-windows-store-integrate-engagement.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
- [iOS](mobile-engagement-ios-integrate-engagement.md) 
- [Android](mobile-engagement-android-integrate-engagement.md) 

Cette procédure explique la façon la plus simple d’activation d’Engagement Azure Mobile Analytique et surveillance des fonctions dans l’application Windows Phone Silverlight.

Les étapes suivantes sont suffisantes pour activer le rapport des journaux nécessaires pour calculer toutes les statistiques concernant les utilisateurs, Sessions, activités, se bloque et Technicals. Le rapport des journaux nécessaires pour calculer d’autres statistiques telles que les événements, les erreurs et les tâches doit être exécuté manuellement à l’aide de l’API Engagement (voir [comment utiliser l’Engagement Mobile avancées marquage API dans votre application Windows Phone Silverlight](mobile-engagement-windows-phone-use-engagement-api.md) ci-dessous) dans la mesure où ces statistiques dépendent de l’application.

##<a name="supported-versions"></a>Versions prises en charge

Le Kit de développement de Engagement Mobile pour Windows Silverlight peut uniquement être intégré dans les applications :

-   Windows Phone 8.0
-   Windows Phone 8.1 Silverlight

> [AZURE.NOTE] Si vous ciblez Windows Phone 8.1 (non Silverlight), reportez-vous à la [procédure de l’intégration Windows universel](mobile-engagement-windows-store-integrate-engagement.md).

##<a name="install-the-mobile-engagement-silverlight-sdk"></a>Installez le Kit de développement Mobile Engagement Silverlight

Le Kit de développement de Engagement Mobile pour Windows Silverlight est disponible sous forme de package Nuget appelé *MicrosoftAzure.MobileEngagement*. Vous pouvez l’installer à partir du Gestionnaire de Package Nuget Visual Studio. 

##<a name="add-the-capabilities"></a>Ajouter les fonctionnalités

Le Kit de développement Engagement doit certaines fonctionnalités du Kit de développement Windows Phone Silverlight pour pouvoir fonctionner correctement.

Ouvrir votre `WMAppManifest.xml` de fichiers et n’oubliez pas que les fonctionnalités suivantes sont déclarées dans le `Capabilities` Panneau de configuration :

-   `ID_CAP_NETWORKING`
-   `ID_CAP_IDENTITY_DEVICE`

##<a name="initialize-the-engagement-sdk"></a>Initialisation l’Engagement SDK

### <a name="engagement-configuration"></a>Configuration d’engagement

La configuration d’Engagement centralisée dans le `Resources\EngagementConfiguration.xml` fichier de votre projet.

Modifier ce fichier pour spécifier :

-   Chaîne de connexion votre application entre balises `<connectionString>` et `<\connectionString>`.

Si vous voulez spécifier à la place en cours d’exécution, vous pouvez appeler la méthode suivante avant l’initialisation de l’agent Engagement :

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(engagementConfiguration);

La chaîne de connexion pour votre application s’affiche dans le portail classique Azure.

### <a name="engagement-initialization"></a>Échec de l’initialisation engagement

Lorsque vous créez un nouveau projet, un `App.xaml.cs` fichier est généré. Cette classe hérite de `Application` et contient de nombreuses méthodes importantes. Il servira également initialisation le Kit de développement Engagement.

Modifier la `App.xaml.cs`:

-   Ajouter à votre `using` instructions :

        using Microsoft.Azure.Engagement;

-   Insérer `EngagementAgent.Instance.Init` dans les `Application_Launching` méthode :

        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
          EngagementAgent.Instance.Init();
        }

-   Insérer `EngagementAgent.Instance.OnActivated` dans les `Application_Activated` méthode :

        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
        }

> [AZURE.WARNING] Nous vous déconseillons vous pour ajouter l’initialisation Engagement à un autre endroit de votre application. Toutefois, n’oubliez pas que la `EngagementAgent.Instance.Init` méthode s’exécute sur un thread dédié et non le thread de l’interface utilisateur.

##<a name="basic-reporting"></a>Création de rapports de base

### <a name="recommended-method--overload-your-phoneapplicationpage-classes"></a>Méthode recommandée : surcharger votre `PhoneApplicationPage` classes

Pour activer l’état de tous les journaux requis par Engagement pour calculer les utilisateurs, les Sessions, activités, se bloque et statistiques techniques, vous pouvez simplement effectuer tous les votre `PhoneApplicationPage` sous-adresse classes héritent de la `EngagementPage` cours.

Voici un exemple de la procédure à suivre pour une page de votre application. Vous pouvez faire la même chose pour toutes les pages de votre application.

#### <a name="c-source-file"></a>Fichier Source c#

Modifier votre page `.xaml.cs` fichier :

-   Ajouter à votre `using` instructions :

        using Microsoft.Azure.Engagement;

-   Remplacer `PhoneApplicationPage` avec `EngagementPage` :

**Sans Engagement :**

        namespace Example
        {
          public partial class ExamplePage : PhoneApplicationPage
          {
            [...]
          }
        }

**Avec Engagement :**

        using Microsoft.Azure.Engagement;
        
        namespace Example
        {
          public partial class ExamplePage : EngagementPage 
          {
            [...]
          }
        }

> [AZURE.WARNING] Si votre page hérite de la `OnNavigatedTo` méthode, en veillant à ne laisser la `base.OnNavigatedTo(e)` appeler. Dans le cas contraire, l’activité n’est pas signalée. En effet, la `EngagementPage` appelle `StartActivity` à l’intérieur de la `OnNavigatedTo` méthode.

#### <a name="xaml-file"></a>Fichier XAML

Modifier votre page `.xaml` fichier :

-   Ajouter à vos espaces de noms déclarations :

        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

-   Remplacer `phone:PhoneApplicationPage` avec `engagement:EngagementPage` :

**Sans Engagement :**

        <phone:PhoneApplicationPage>
            <!-- layout -->
        </phone:PhoneApplicationPage>

**Avec Engagement :**

        <engagement:EngagementPage 
            xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP">
        
            <!-- layout -->
        </engagement:EngagementPage >

#### <a name="override-the-default-behavior"></a>Remplacer le comportement par défaut

Par défaut, le nom du cours de la page est signalé comme nom de l’activité, avec sans supplémentaire. Si la classe utilise le suffixe « Page », Engagement est également la supprimer.

Si vous souhaitez remplacer le comportement par défaut pour le nom, ajoutez simplement ceci à votre code :

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
           /* your code */
           return "new name";
        }

Si vous voulez signaler certaines informations supplémentaires à votre activité, vous pouvez ajouter cela à votre code :

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
           /* your code */
           return extra;
        }

Ces méthodes sont appelées depuis le `OnNavigatedTo` méthode de votre page.

### <a name="alternate-method-call-startactivity-manually"></a>Méthode alternative : appeler `StartActivity()` manuellement

Si vous ne pouvez pas ou que vous ne souhaitez pas surcharger votre `PhoneApplicationPage` classes, vous pouvez commencer à la place de vos activités en appelant `EngagementAgent` méthodes directement.

Nous vous recommandons d’appeler `StartActivity` à l’intérieur de votre `OnNavigatedTo` méthode de votre PhoneApplicationPage.

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
           base.OnNavigatedTo(e);
           EngagementAgent.Instance.StartActivity("MyPage");
        }

> [AZURE.IMPORTANT] Vérifiez que vous fermez votre session correctement.
>
> Le Kit de développement appelle automatiquement le `EndActivity` méthode lorsque l’application est fermée. Par conséquent, il est **vivement** recommandé d’appeler le `StartActivity` méthode chaque fois que l’activité de l’utilisateur modifier, puis sur **jamais** appelez le `EndActivity` méthode. Cette méthode envoie un message sur le serveur Engagement que l’utilisateur a quitté l’application et tous les journaux d’application sont affectées.

##<a name="advanced-reporting"></a>Rapports avancée

Si vous le souhaitez, vous voudrez peut-être événements spécifiques de l’application rapport, les erreurs et les tâches, pour ce faire, utilisez les autres méthodes figurant dans la `EngagementAgent` cours. L’API Engagement permet d’utiliser toutes les fonctionnalités avancées d’Engagement.

Pour plus d’informations, voir [comment utiliser l’Engagement Mobile avancées marquage API dans votre application Silverlight de Windows Phone](mobile-engagement-windows-phone-use-engagement-api.md).

##<a name="advanced-configuration"></a>Configuration avancée

### <a name="disable-automatic-crash-reporting"></a>Désactiver le signalement de blocage automatique

Vous pouvez désactiver la fonctionnalité d’Engagement de rapport d’incident automatique. Puis, lorsqu’une exception non gérée se produira, Engagement ne rien faire.

> [AZURE.WARNING] Si vous envisagez de désactiver cette fonctionnalité, n’oubliez pas que lorsqu’un blocage non géré doit se dérouler dans votre application, Engagement n’envoie pas l’incident **et** qu’il ne ferme pas la session et les tâches.

Pour désactiver le signalement de blocage automatique, vous devez simplement personnaliser votre configuration selon la façon dont vous l’avez déclaré :

#### <a name="from-engagementconfigurationxml-file"></a>À partir de `EngagementConfiguration.xml` fichier

Blocage de rapport la valeur `false` entre `<reportCrash>` et `</reportCrash>` balises.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>À partir de `EngagementConfiguration` objet en cours d’exécution

Blocage de rapport la valeur false à l’aide de votre objet EngagementConfiguration.

        /* Engagement configuration. */

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration(); engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        /\* Disable Engagement crash reporting. \*/ engagementConfiguration.Agent.ReportCrash = false;

### <a name="burst-mode"></a>Mode rupture

Par défaut, les rapports de service Engagement ouvre une session en temps réel. Si votre application signale les journaux très souvent, il est préférable pour les journaux de la mémoire tampon et les rapports en une seule fois sur une base de temps standard (cette option est appelée « mode rupture »).

Pour ce faire, appelez la méthode :

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

L’argument est une valeur en **millisecondes**. À tout moment, si vous voulez réactiver la journalisation en temps réel, appelez simplement la méthode sans paramètres ou avec la valeur 0.

Le mode rupture légèrement augmenter la durée de vie de batterie mais a un impact sur l’analyseur d’Engagement : toutes les sessions et travaux la durée est arrondie à la valeur seuil rupture (ainsi, les sessions et les tâches plus courtes que le seuil rupture peut ne pas être visible). Il est recommandé d’utiliser un seuil rupture n’est plus à 30000 (30 s). Vous devez tenir compte enregistrés journaux sont limités à 300 éléments. Si l’envoi est trop longue, vous pouvez perdre certains journaux.

> [AZURE.WARNING] Le seuil de rupture ne peut pas être configuré pour une période inférieure à une seconde. Si vous essayez de faire, le Kit de développement affiche une trace avec l’erreur et réinitialiser automatiquement à la valeur par défaut, c'est-à-dire zéro secondes. Cela déclenche le Kit de développement pour signaler les journaux en temps réel.
 
