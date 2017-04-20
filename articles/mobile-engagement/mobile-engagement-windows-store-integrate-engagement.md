<properties 
    pageTitle="Intégration de kit de développement Windows applications universel Engagement" 
    description="Comment intégrer Azure Engagement Mobile Windows universel applications"                  
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

# <a name="windows-universal-apps-engagement-sdk-integration"></a>Intégration de kit de développement Windows applications universel Engagement

> [AZURE.SELECTOR] 
- [Universel de Windows](mobile-engagement-windows-store-integrate-engagement.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
- [iOS](mobile-engagement-ios-integrate-engagement.md) 
- [Android](mobile-engagement-android-integrate-engagement.md) 

Cette procédure explique la façon la plus simple d’activation d’Engagement Analytique et surveillance des fonctions dans votre application Windows universel.

Les étapes suivantes sont suffisantes pour activer le rapport des journaux nécessaires pour calculer toutes les statistiques concernant les utilisateurs, Sessions, activités, se bloque et Technicals. Le rapport des journaux nécessaires pour calculer d’autres statistiques telles que les événements, les erreurs et les tâches doit être exécuté manuellement à l’aide de l’API Engagement (voir [comment utiliser l’Engagement Mobile avancées API dans votre application Windows universel de marquage](mobile-engagement-windows-store-use-engagement-api.md) dans la mesure où ces statistiques sont dépend de l’application.

## <a name="supported-versions"></a>Versions prises en charge

L’Engagement SDK pour Windows universel applications Mobile peut uniquement être intégrées dans Windows Runtime et les applications de plateforme Windows universel ciblage :

-   Windows 8
-   Windows 8.1
-   Windows Phone 8.1
-   Windows 10 (familles mobiles et de bureau)

> [AZURE.NOTE] Si vous ciblez Silverlight de Windows Phone, reportez-vous à la [procédure de l’intégration de Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md).

## <a name="install-the-mobile-engagement-universal-apps-sdk"></a>Installez le Kit de développement Mobile Engagement universel applications

### <a name="all-platforms"></a>Toutes les plateformes

L’Engagement SDK pour Windows universel application Mobile est disponible sous forme de package Nuget appelé *MicrosoftAzure.MobileEngagement*. Vous pouvez l’installer à partir du Gestionnaire de Package Nuget Visual Studio.

### <a name="windows-8x-and-windows-phone-81"></a>Windows 8.x et Windows Phone 8.1

NuGet déploie automatiquement les ressources SDK dans le `Resources` dossier à la racine de votre projet d’application.

### <a name="windows-10-universal-windows-platform-applications"></a>Applications de la plateforme Windows universel de Windows 10

NuGet ne déploie pas automatiquement les ressources SDK dans votre application UWP encore. Vous devrez le faire manuellement jusqu'à ce que le déploiement des ressources se reproduit dans NuGet :

1.  Ouvrez l’Explorateur de votre fichier.
2.  Accédez à l’emplacement suivant (**x.x.x** est la version d’Engagement vous installez) : *%USERPROFILE%\\.nuget\packages\MicrosoftAzure.MobileEngagement\\**x.x.x**\\content\win81*
3.  Faites glisser et déposez le dossier de **ressources** à partir de l’Explorateur de fichiers à la racine de votre projet dans Visual Studio.
4.  Dans Visual Studio sélectionner votre projet et activer l’icône **Afficher tous les fichiers** en haut de l' **Explorateur de solutions**.
5.  Certains fichiers ne sont pas inclus dans le projet. Pour importer les à la fois avec le bouton droit cliquez sur le dossier **ressources** , **exclure du projet** puis un autre avec le bouton droit sur le dossier **ressources** , **inclure dans le projet** à nouveau inclure la totalité du dossier. Tous les fichiers à partir du dossier de **ressources** sont désormais inclus dans votre projet.

## <a name="add-the-capabilities"></a>Ajouter les fonctionnalités

Le Kit de développement Engagement doit certaines fonctionnalités du Kit de développement Windows pour fonctionner correctement.

Ouvrir votre `Package.appxmanifest` de fichiers et n’oubliez pas que les fonctionnalités suivantes sont déclarées :

-   `Internet (Client)`

## <a name="initialize-the-engagement-sdk"></a>Initialisation l’Engagement SDK

### <a name="engagement-configuration"></a>Configuration d’engagement

La configuration d’Engagement centralisée dans le `Resources\EngagementConfiguration.xml` fichier de votre projet.

Modifier ce fichier pour spécifier :

-   Chaîne de connexion votre application entre balises `<connectionString>` et `<\connectionString>`.

Si vous voulez spécifier à la place en cours d’exécution, vous pouvez appeler la méthode suivante avant l’initialisation de l’agent Engagement :
          
          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);

La chaîne de connexion pour votre application s’affiche dans le portail classique Azure.

### <a name="engagement-initialization"></a>Échec de l’initialisation engagement

Lorsque vous créez un nouveau projet, un `App.xaml.cs` fichier est généré. Cette classe hérite de `Application` et contient de nombreuses méthodes importantes. Il servira également initialisation le Kit de développement Engagement.

Modifier la `App.xaml.cs`:

-   Ajouter à votre `using` instructions :

        using Microsoft.Azure.Engagement;

-   Définir une méthode pour partager l’initialisation Engagement une fois pour tous les appels :

        private void InitEngagement(IActivatedEventArgs e)
        {
          EngagementAgent.Instance.Init(e);
        
          // or
        
          EngagementAgent.Instance.Init(e, engagementConfiguration);
        }
        
-   Appeler `InitEngagement` dans les `OnLaunched` méthode :

        protected override void OnLaunched(LaunchActivatedEventArgs e)
        {
          InitEngagement(e);
        }

-   Lorsque votre application est lancée à l’aide d’un modèle personnalisé, une autre application ou la ligne de commande la `OnActivated` méthode est appelée. Vous devez également lancer le Kit de développement Engagement lorsque votre application est activée. Pour ce faire, substituer `OnActivated` méthode :

        protected override void OnActivated(IActivatedEventArgs args)
        {
          InitEngagement(args);
        }

> [AZURE.IMPORTANT] Nous vous déconseillons vous pour ajouter l’initialisation Engagement à un autre endroit de votre application.

## <a name="basic-reporting"></a>Création de rapports de base

### <a name="recommended-method-overload-your-page-classes"></a>Méthode recommandée : surcharger votre `Page` classes

Pour activer l’état de tous les journaux requis par Engagement pour calculer les utilisateurs, les Sessions, activités, se bloque et statistiques techniques, vous pouvez simplement effectuer tous les votre `Page` sous-adresse classes héritent de la `EngagementPage` classes.

Voici un exemple de la procédure à suivre pour une page de votre application. Vous pouvez faire la même chose pour toutes les pages de votre application.

#### <a name="c-source-file"></a>Fichier Source c#

Modifier votre page `.xaml.cs` fichier :

-   Ajouter à votre `using` instructions :

        using Microsoft.Azure.Engagement;

-   Remplacer `Page` avec `EngagementPage`:

**Sans Engagement :**
    
        namespace Example
        {
          public sealed partial class ExamplePage : Page
          {
            [...]
          }
        }

**Avec Engagement :**

        using Microsoft.Azure.Engagement;
        
        namespace Example
        {
          public sealed partial class ExamplePage : EngagementPage 
          {
            [...]
          }
        }

> [AZURE.IMPORTANT] Si votre page remplace la `OnNavigatedTo` méthode, veillez à appeler `base.OnNavigatedTo(e)`. Dans le cas contraire, l’activité n’est pas signalée (la `EngagementPage` appels `StartActivity` à l’intérieur de sa `OnNavigatedTo` méthode).

#### <a name="xaml-file"></a>Fichier XAML

Modifier votre page `.xaml` fichier :

-   Ajouter à vos espaces de noms déclarations :

        xmlns:engagement="using:Microsoft.Azure.Engagement"

-   Remplacer `Page` avec `engagement:EngagementPage`:

**Sans Engagement :**

        <Page>
            <!-- layout -->
            ...
        </Page>

**Avec Engagement :**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">
            <!-- layout -->
            ...
        </engagement:EngagementPage >

#### <a name="override-the-default-behaviour"></a>Remplacer le comportement par défaut

Par défaut, le nom du cours de la page est signalé comme nom de l’activité, avec sans supplémentaire. Si la classe utilise le suffixe « Page », Engagement est également la supprimer.

Si vous voulez remplacer le comportement par défaut pour le nom, ajoutez simplement ceci à votre code :

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

Si vous ne pouvez pas ou que vous ne souhaitez pas surcharger votre `Page` classes, vous pouvez commencer à la place de vos activités en appelant `EngagementAgent` méthodes directement.

Nous vous recommandons d’appeler `StartActivity` à l’intérieur de votre `OnNavigatedTo` méthode de votre Page.

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [AZURE.IMPORTANT]  Vérifiez que vous fermez votre session correctement.
> 
> Le Kit de développement Windows universel appelle automatiquement le `EndActivity` méthode lorsque l’application est fermée. Par conséquent, il est **vivement** recommandé d’appeler le `StartActivity` méthode chaque fois que l’activité de l’utilisateur modifier, puis sur **jamais** appelez le `EndActivity` méthode, cette méthode envoie au serveur de Engagement qu’utilisateur actuel a laissez l’application, suivant apparaît affecte tous les journaux d’application.

## <a name="advanced-reporting"></a>Rapports avancée

Si vous le souhaitez, vous voudrez peut-être événements spécifiques de l’application rapport, les erreurs et les tâches, pour ce faire, utilisez les autres méthodes figurant dans la `EngagementAgent` cours. L’API Engagement permet d’utiliser toutes les fonctionnalités avancées d’Engagement.

Pour plus d’informations, voir [comment utiliser l’Engagement Mobile avancées marquage API dans votre application Windows universel](mobile-engagement-windows-store-use-engagement-api.md).

##<a name="advanced-configuration"></a>Configuration avancée

### <a name="disable-automatic-crash-reporting"></a>Désactiver le signalement de blocage automatique

Vous pouvez désactiver la fonctionnalité d’Engagement de rapport d’incident automatique. Puis, lorsqu’une exception non gérée se produira, Engagement ne rien faire.

> [AZURE.WARNING] Si vous envisagez de désactiver cette fonctionnalité, n’oubliez pas que lorsqu’un blocage non géré doit se dérouler dans votre application, Engagement n’envoie pas que l’incident **et** ne ferme pas la session et les tâches.

Pour désactiver le signalement de blocage automatique, vous devez simplement personnaliser votre configuration selon la façon dont vous l’avez déclaré :

#### <a name="from-engagementconfigurationxml-file"></a>À partir de `EngagementConfiguration.xml` fichier

Blocage de rapport la valeur `false` entre `<reportCrash>` et `</reportCrash>` balises.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>À partir de `EngagementConfiguration` objet en cours d’exécution

Blocage de rapport la valeur false à l’aide de votre objet EngagementConfiguration.

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        
        /* Disable Engagement crash reporting. */
        engagementConfiguration.Agent.ReportCrash = false;

### <a name="burst-mode"></a>Mode rupture

Par défaut, les rapports de service Engagement enregistre en temps réel. Si votre application signale les journaux très souvent, il est préférable pour les journaux de la mémoire tampon et les rapports en une seule fois sur une base de temps standard (cette option est appelée « mode rupture »).

Pour ce faire, appelez la méthode :

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

L’argument est une valeur en **millisecondes**. À tout moment, si vous voulez réactiver la journalisation en temps réel, appelez simplement la méthode sans paramètres ou avec la valeur 0.

Le mode rupture légèrement augmenter la durée de vie de batterie mais a un impact sur l’analyseur d’Engagement : toutes les sessions et travaux la durée est arrondie à la valeur seuil rupture (ainsi, les sessions et les tâches plus courtes que le seuil rupture peut ne pas être visible). Il est recommandé d’utiliser un seuil rupture n’est plus à 30000 (30 s). Vous devez tenir compte enregistrés journaux sont limités à 300 éléments. Si l’envoi est trop longue, vous pouvez perdre certains journaux.

> [AZURE.WARNING] Le seuil de rupture ne peut pas être configuré pour une période inférieure à 1 s. Si vous essayez de le faire, le Kit de développement affiche une trace de l’erreur et réinitialise automatiquement à la valeur par défaut, c'est-à-dire 0. Cela déclenche le Kit de développement pour signaler les journaux en temps réel.

[here]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]:http://docs.nuget.org/docs/start-here/overview
 
