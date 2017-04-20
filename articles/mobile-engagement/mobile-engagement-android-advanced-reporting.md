<properties
    pageTitle="Options avancées de création de rapports pour Mobile Engagement Azure SDK Android"
    description="Décrit comment utiliser la fonction avancées à capturer analytique pour Mobile Engagement Azure SDK Android"
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
    ms.date="08/10/2016"
    ms.author="piyushjo;ricksal" />

# <a name="advanced-reporting-with-engagement-on-android"></a>Rapports avec Engagement sur Android avancée

> [AZURE.SELECTOR]
- [Universel de Windows](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advanced-reporting.md)

Cette rubrique décrit des scénarios de création de rapports supplémentaires dans votre application Android. Vous pouvez appliquer ces options à l’application créée dans le didacticiel [Mise en route](mobile-engagement-android-get-started.md) .

## <a name="prerequisites"></a>Conditions préalables

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

Le didacticiel vous terminé a été délibérément directe et simple, mais il sont des options que vous pouvez choisir avancées.

## <a name="modifying-your-activity-classes"></a>Modification de votre `Activity` classes

Dans le [didacticiel mise en route](mobile-engagement-android-get-started.md), tout ce que vous deviez était pour rendre votre `*Activity` sous-classes héritent de correspondantes `Engagement*Activity` cours. Par exemple, si votre activité héritée étendu `ListActivity`, vous devez la rendre étendre `EngagementListActivity`.

> [AZURE.IMPORTANT] Lorsque vous utilisez `EngagementListActivity` ou `EngagementExpandableListActivity`, vérifiez que tout appel à `requestWindowFeature(...);` est effectué avant l’appel à `super.onCreate(...);`, sinon un blocage se produit.

Vous pouvez trouver ces cours dans la `src` dossier et vous pouvez les copier dans votre projet. Les classes sont également dans le **JavaDoc**.

## <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>Méthode alternative : appeler `startActivity()` et `endActivity()` manuellement

Si vous ne pouvez pas ou que vous ne souhaitez pas surcharger votre `Activity` classes, vous pouvez à la place de début et de fin de vos activités en appelant le `EngagementAgent`de méthodes directement.

> [AZURE.IMPORTANT] Le Kit de développement Android appelle jamais la `endActivity()` méthode, même lorsque l’application est fermée (sur Android, applications ne sont jamais fermées). Par conséquent, il est *vivement* recommandé d’appeler le `startActivity()` méthode dans le `onResume` rappel de *tous les* vos activités et la `endActivity()` méthode dans le `onPause()` rappel de *tous les* vos activités. Il s’agit de la seule façon pour vous assurer que les sessions ne sont pas divulguées. Si une session est perdue, le service Engagement déconnecte jamais le système principal Engagement (dans la mesure où le service reste connecté en tant qu’une session est en attente).

Voici un exemple :

    public class MyActivity extends Some3rdPartyActivity
    {
      @Override
      protected void onResume()
      {
        super.onResume();
        String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
        EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
      }

      @Override
      protected void onPause()
      {
        super.onPause();
        EngagementAgent.getInstance(this).endActivity();
      }
    }

Cet exemple est similaire à la `EngagementActivity` cours et ses variantes, dont le code source est fourni dans la `src` dossier.

## <a name="using-applicationoncreate"></a>À l’aide de Application.onCreate()

Tout code que vous placez dans `Application.onCreate()` et dans une autre application rappels est exécuté pour les processus de tous les votre application, y compris le service d’Engagement. Il peut avoir des résultats indésirables, tels qu’allocation de mémoire inutiles et threads de processus de l’Engagement, ou les récepteurs de diffusion en double ou les services.

Si vous remplacez `Application.onCreate()`, nous vous recommandons d’ajouter l’extrait de code suivantes au début de votre `Application.onCreate()` fonction :

     public void onCreate()
     {
       if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
         return;

       ... Your code...
     }

Vous pouvez faire la même chose pour `Application.onTerminate()`, `Application.onLowMemory()`, et `Application.onConfigurationChanged(...)`.

Vous pouvez également étendre `EngagementApplication` au lieu de l’extension `Application`: le rappel `Application.onCreate()` effectue le contrôle de processus et les appels `Application.onApplicationProcessCreate()` uniquement si le processus en cours n’est pas celui qui héberge le service d’Engagement, les mêmes règles s’appliquent pour les autres rappels.

## <a name="tags-in-the-androidmanifestxml-file"></a>Balises dans le fichier AndroidManifest.xml

Dans le numéro de série dans le fichier AndroidManifest.xml, le `android:label` attribut vous permet de choisir le nom du service Engagement tel qu’il apparaît aux utilisateurs finaux dans l’écran « Exécutant les services » de leur téléphone. Nous vous recommandons de cet attribut `"<Your application name>Service"` (par exemple, `"AcmeFunGameService"`).

Spécifier la `android:process` attribut garantit que le service Engagement s’exécute dans son propre processus (en cours d’exécution Engagement dans la même procédure que votre application rend le thread de votre interface utilisateur/principales potentiellement moins réactif).

## <a name="building-with-proguard"></a>Construction avec ProGuard

Si vous créez votre package d’application avec ProGuard, vous devez conserver certaines classes. Vous pouvez utiliser l’extrait de configuration suivant :

    -keep public class * extends android.os.IInterface
    -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
    <methods>;
    }
