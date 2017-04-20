<properties
    pageTitle="Windows universel Reporting avec Engagement MobileApps avancé"
    description="Comment intégrer Azure Engagement Mobile Windows universel applications"                  
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="piyushjo;ricksal" />

# <a name="advanced-reporting-with-the-windows-universal-apps-engagement-sdk"></a>Rapports avec l’Engagement d’universel applications Windows SDK avancée

> [AZURE.SELECTOR]
- [Universel de Windows](mobile-engagement-windows-store-advanced-reporting.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advanced-reporting.md)

Cette rubrique décrit des scénarios de création de rapports supplémentaires dans votre application Windows universel. Ces scénarios comprennent des options que vous pouvez choisir d’appliquer à l’application créée dans le didacticiel [Mise en route](mobile-engagement-windows-store-dotnet-get-started.md) .

## <a name="prerequisites"></a>Conditions préalables

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

Avant de commencer ce didacticiel, vous devez tout d’abord effectuer le didacticiel [Mise en route](mobile-engagement-windows-store-dotnet-get-started.md) , qui est délibérément simple et direct. Ce didacticiel décrit les options supplémentaires que vous avez le choix entre.

## <a name="specifying-engagement-configuration-at-runtime"></a>Si vous spécifiez configuration engagement en cours d’exécution

La configuration d’Engagement centralisée dans le `Resources\EngagementConfiguration.xml` fichier de votre projet, ce qui correspond à l’endroit où il a été spécifié dans la rubrique [Mise en route](mobile-engagement-windows-store-dotnet-get-started.md) .

Mais vous pouvez également le spécifier en cours d’exécution : vous pouvez appeler la méthode suivante avant l’initialisation de l’agent Engagement :

          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);



## <a name="recommended-method-overload-your-page-classes"></a>Méthode recommandée : surcharger votre `Page` classes

Pour activer le signalement de tous les journaux requis par Engagement pour calculer les utilisateurs, Sessions, activités, se bloque et techniques statistiques, apportez toutes les votre `Page` sous-adresse classes héritent de la `EngagementPage` cours.

Voici un exemple d’une page de votre application. Vous pouvez faire la même chose pour toutes les pages de votre application.

### <a name="c-source-file"></a>Fichier Source c#

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

> [AZURE.IMPORTANT] Si votre page remplace la `OnNavigatedTo` méthode, veillez à appeler `base.OnNavigatedTo(e)`. Dans le cas contraire, l’activité n’est pas être signalé (la `EngagementPage` appels `StartActivity` à l’intérieur de sa `OnNavigatedTo` méthode).

### <a name="xaml-file"></a>Fichier XAML

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

### <a name="override-the-default-behaviour"></a>Remplacer le comportement par défaut

Par défaut, le nom du cours de la page est signalé comme nom de l’activité, avec sans supplémentaire. Si la classe utilise le suffixe « Page », Engagement supprime.

Pour remplacer le comportement par défaut pour le nom, ajoutez le code suivant :

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
          /* your code */
          return "new name";
        }

Pour signaler des informations supplémentaires avec votre activité, ajoutez le code suivant :

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
> Le Kit de développement Windows universel appelle automatiquement le `EndActivity` méthode lorsque l’application est fermée. Par conséquent, il est **vivement** recommandé d’appeler le `StartActivity` méthode chaque fois que l’activité de l’utilisateur modifier, puis sur **jamais** appelez le `EndActivity` méthode. Cette méthode avertit le serveur Engagement que l’utilisateur a quitté l’application, qui aura un impact sur tous les journaux d’application.

## <a name="advanced-reporting"></a>Rapports avancée

Si vous le souhaitez, vous souhaiterez peut-être signaler les événements d’application, les erreurs et les tâches, à le faire, utilisent les autres méthodes figurant dans la `EngagementAgent` cours. L’API Engagement permet l’utilisation de fonctionnalités avancées de tous les Engagement.

Pour plus d’informations, voir [comment utiliser l’Engagement Mobile avancées marquage API dans votre application Windows universel](mobile-engagement-windows-store-use-engagement-api.md).
