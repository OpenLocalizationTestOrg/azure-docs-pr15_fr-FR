<properties
    pageTitle="Intégration de SDK Android Azure Engagement Mobile"
    description="Dernières mises à jour et procédures pour Android SDK pour Azure Mobile Engagement"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

#<a name="how-to-integrate-engagement-on-android"></a>Comment intégrer Engagement sur Android

> [AZURE.SELECTOR]
- [Universel de Windows](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

Cette procédure explique la façon la plus simple d’activation d’Engagement Analytique et surveillance des fonctions dans votre application Android.

> [AZURE.IMPORTANT] Votre niveau API SDK Android minimale doit être 10 ou version ultérieure (Android 2.3.3 ou une version ultérieure).

Les étapes suivantes suffisent pour activer le rapport des journaux nécessaires pour calculer toutes les statistiques concernant les utilisateurs, Sessions, activités, se bloque et Technicals. Le rapport des journaux nécessaires pour calculer d’autres statistiques telles que les événements, les erreurs et les tâches doit être exécuté manuellement à l’aide de l’API Engagement (voir [comment utiliser l’Engagement Mobile avancées marquage API dans votre appareil Android](mobile-engagement-android-use-engagement-api.md) dans la mesure où ces statistiques sont dépend de l’application.

##<a name="embed-the-engagement-sdk-and-service-into-your-android-project"></a>Incorporer la Engagement SDK et le service dans votre projet Android

Téléchargez le Kit de développement Android [ici](https://aka.ms/vq9mfn) Get `mobile-engagement-VERSION.jar` et placez-les dans les `libs` dossier de votre projet Android (créer le dossier et les bibliothèques s’il n’existe pas encore).

> [AZURE.IMPORTANT]
> Si vous créez votre package d’application avec ProGuard, vous devez conserver certaines classes. Vous pouvez utiliser l’extrait de configuration suivant :
>
>
            -keep public class * extends android.os.IInterface
            -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
            <methods>;
            }

Spécifier la chaîne de connexion Engagement en appelant la méthode suivante de l’activité du Lanceur d’applications :

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

La chaîne de connexion pour votre application s’affiche dans le portail Azure.

-   S’il est manquant, ajoutez les autorisations Android suivantes (avant la `<application>` balise) :

            <uses-permission android:name="android.permission.INTERNET"/>
            <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>

-   Ajoutez la section suivante (entre la `<application>` et `</application>` balises) :

            <service
              android:name="com.microsoft.azure.engagement.service.EngagementService"
              android:exported="false"
              android:label="<Your application name>Service"
              android:process=":Engagement"/>

-   Modification `<Your application name>` par le nom de votre application.

> [AZURE.TIP] La `android:label` attribut vous permet de choisir le nom du service Engagement tel qu’il apparaîtra aux utilisateurs finaux dans l’écran « Exécutant les services » de leur téléphone. Il est recommandé d’affecter cet attribut `"<Your application name>Service"` (par exemple, `"AcmeFunGameService"`).

Spécifier la `android:process` attribut garantit que le service Engagement s’exécutera dans sa propre exécution du processus (Engagement dans la même procédure que votre application fera votre thread principal/UI potentiellement moins réactif).

> [AZURE.NOTE] Tout code que vous placez dans `Application.onCreate()` et autres rappels application seront exécutés pour les processus de tous les votre application, y compris le service d’Engagement. Il peut avoir des résultats indésirables (par exemple, l’allocation de mémoire inutiles et threads de processus de l’Engagement, récepteurs de diffusion en double ou services).

Si vous remplacez `Application.onCreate()`, il est recommandé d’ajouter l’extrait de code suivantes au début de votre `Application.onCreate()` fonction :

             public void onCreate()
             {
               if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
                 return;

               ... Your code...
             }

Vous pouvez faire la même chose pour `Application.onTerminate()`, `Application.onLowMemory()` et `Application.onConfigurationChanged(...)`.

Vous pouvez également étendre `EngagementApplication` au lieu de l’extension `Application`: le rappel `Application.onCreate()` effectue le contrôle de processus et les appels `Application.onApplicationProcessCreate()` uniquement si le processus en cours n’est pas celui qui héberge le service d’Engagement, les mêmes règles s’appliquent pour les autres rappels.

##<a name="basic-reporting"></a>Création de rapports de base

### <a name="recommended-method-overload-your-activity-classes"></a>Méthode recommandée : surcharger votre `Activity` classes

Pour activer l’état de tous les journaux requis par Engagement pour calculer les utilisateurs, Sessions, activités, se bloque et techniques statistiques, il vous suffit de tous les votre `*Activity` sous-adresse classes héritent de la correspondant `Engagement*Activity` classes (par exemple, si votre activité héritée étend `ListActivity`, assurez-vous qu’il étend `EngagementListActivity`).

**Sans Engagement :**

            package com.company.myapp;

            import android.app.Activity;
            import android.os.Bundle;

            public class MyApp extends Activity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

**Avec Engagement :**

            package com.company.myapp;

            import com.microsoft.azure.engagement.activity.EngagementActivity;
            import android.os.Bundle;

            public class MyApp extends EngagementActivity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

> [AZURE.IMPORTANT] Lorsque vous utilisez `EngagementListActivity` ou `EngagementExpandableListActivity`, vérifiez que tout appel à `requestWindowFeature(...);` est effectué avant l’appel à `super.onCreate(...);`, sinon un blocage se produit.

Vous pouvez trouver ces cours dans la `src` dossier et vous pouvez les copier dans votre projet. Les classes sont également dans le **JavaDoc**.

### <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>Méthode alternative : appeler `startActivity()` et `endActivity()` manuellement

Si vous ne pouvez pas ou que vous ne souhaitez pas surcharger votre `Activity` classes, vous pouvez à la place de début et de fin de vos activités en appelant `EngagementAgent`de méthodes directement.

> [AZURE.IMPORTANT] Le Kit de développement Android appelle jamais la `endActivity()` méthode, même lorsque l’application est fermée (sur Android, applications sont réellement jamais fermées). Par conséquent, il est *vivement* recommandé d’appeler le `startActivity()` méthode dans le `onResume` rappel de *tous les* vos activités et la `endActivity()` méthode dans le `onPause()` rappel de *tous les* vos activités. Il s’agit de la seule façon pour vous assurer que les sessions pas perdrez. Si une session est perdue, le service Engagement déconnectera jamais à partir du serveur principal Engagement (dans la mesure où le service reste connecté en tant qu’une session est en attente).

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

Cet exemple très similaire à la `EngagementActivity` cours et ses variantes, dont le code source est fourni dans la `src` dossier.

##<a name="test"></a>Test

Veuillez vérifier maintenant l’intégration en exécutant votre application mobile dans un émulateur ou un appareil et en vérifiant qu’il enregistre une session sur l’onglet Moniteur.

Les sections suivantes sont facultatives.

##<a name="location-reporting"></a>Reporting sur l’emplacement

Si vous souhaitez créer des emplacements à signaler, vous devez ajouter quelques lignes de configuration (entre la `<application>` et `</application>` balises).

### <a name="lazy-area-location-reporting"></a>Zone différée reporting sur l’emplacement

Rapport d’emplacement zone différée permet pour signaler le pays, la région et la localisation associés aux appareils. Ce type de reporting sur l’emplacement utilise uniquement les emplacements réseau (basés sur les ID de cellule ou Wi-Fi). La zone de l’appareil est signalée au moins une fois par session. Le GPS n’est jamais utilisé, et ce type de rapport emplacement a donc très peu (ne pas de vous dire non) impact sur la batterie.

Zones signalés sont utilisées pour calculer des statistiques géographiques sur les utilisateurs, les sessions, les événements et les erreurs. Ils permettent également comme critère dans les campagnes accessibles.

Pour activer la zone différée reporting sur l’emplacement, vous pouvez le faire à l’aide de la configuration précédemment mentionnée dans cette procédure :

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Vous devez également ajouter l’autorisation suivante s’il est manquant :

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Ou vous pouvez continuer à utiliser ``ACCESS_FINE_LOCATION`` si vous l’utilisez déjà dans votre application.

### <a name="real-time-location-reporting"></a>Emplacement rapports en temps réel

Rapport d’emplacement en temps réel permet pour signaler le latitude et longitude associés aux appareils. Par défaut, ce type de reporting sur l’emplacement utilise uniquement les emplacements réseau (basés sur les ID de cellule ou Wi-Fi) et la création de rapports est active uniquement lorsque l’application est exécutée au premier plan (c'est-à-dire au cours d’une session).

Emplacements en temps réel ne sont *pas* utilisées pour calculer des statistiques. Leur seul objectif est de permettre l’utilisation d’en temps réel geo-clôture \<gardiennage virtuel les participants d’atteindre\> critère dans campagnes marketing accessibles.

Pour activer l’emplacement en temps réel création de rapports, vous pouvez le faire à l’aide de la configuration précédemment mentionnée dans cette procédure :

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Vous devez également ajouter l’autorisation suivante s’il est manquant :

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Ou vous pouvez continuer à utiliser ``ACCESS_FINE_LOCATION`` si vous l’utilisez déjà dans votre application.

#### <a name="gps-based-reporting"></a>GPS en fonction de création de rapports

Par défaut, reporting sur l’emplacement en temps réel utilise uniquement les emplacements réseau en fonction. Pour activer l’utilisation des emplacements GPS basé (qui sont beaucoup plus précis), utilisez l’objet de configuration :

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Vous devez également ajouter l’autorisation suivante s’il est manquant :

            <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>Création de rapports d’arrière-plan

Par défaut, reporting sur l’emplacement en temps réel est actif uniquement lorsque l’application est exécuté en avant-plan (c'est-à-dire au cours d’une session). Pour activer la création de rapports également en arrière-plan, utilisez l’objet de configuration :

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [AZURE.NOTE] Lorsque l’application est exécutée en arrière-plan, uniquement des emplacements sur le réseau sont signalés, même si vous avez activé le GPS.

Le rapport d’emplacement d’arrière-plan est arrêté si l’utilisateur redémarre son appareil, vous pouvez ajouter cette afin de pouvoir redémarrer automatiquement au démarrage :

            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>

Vous devez également ajouter l’autorisation suivante s’il est manquant :

            <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

### <a name="android-m-permissions"></a>Autorisations M Android

En commençant par M Android, certaines autorisations sont gérées en cours d’exécution et nécessite l’approbation de l’utilisateur.

Les autorisations pour l’exécution seront désactivées par défaut pour les nouvelles installations d’application si vous ciblez niveau API Android 23. Sinon, elle sera être activée par défaut.

L’utilisateur peut activer/désactiver ces autorisations dans le menu Paramètres du périphérique. La désactivation de l’autorisation de menu système arrête des processus d’arrière-plan de l’application, il s’agit d’un comportement du système et n’a aucun impact sur la possibilité de recevoir push en arrière-plan.

Dans le contexte d’Engagement Mobile, les autorisations qui doivent être approuvés lors de l’exécution sont :

- `ACCESS_COARSE_LOCATION`
- `ACCESS_FINE_LOCATION`
- `WRITE_EXTERNAL_STORAGE`(uniquement lorsque le ciblage du niveau d’API Android 23 pour cette)

Stockage externe est utilisé uniquement pour la fonctionnalité de vue d’ensemble accessibles. Si vous trouvez pas demander aux utilisateurs de cette autorisation pour être interruption de service, vous pouvez la supprimer si vous l’avez utilisé uniquement pour Mobile Engagement mais au détriment de la désactivation de la fonctionnalité de vue d’ensemble.

Pour les fonctionnalités d’emplacement, vous devez demander des autorisations pour un utilisateur à l’aide d’une boîte de dialogue système standard. Si l’utilisateur a approuvé, vous devez indiquer ``EngagementAgent`` pour effectuer cette modification en considération en temps réel (sinon, la modification fera la prochaine fois que l’utilisateur lance l’application).

Voici un exemple de code à utiliser dans une activité de votre application à demander des autorisations et les transférer le résultat si positif pour ``EngagementAgent``:

    @Override
    public void onCreate(Bundle savedInstanceState)
    {
      /* Other code... */

      /* Request permissions */
      requestPermissions();
    }

    @TargetApi(Build.VERSION_CODES.M)
    private void requestPermissions()
    {
      /* Avoid crashing if not on Android M */
      if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
      {
        /*
         * Request location permission, but this won't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

        /* Only if you want to keep features using external storage */
        if (checkSelfPermission(android.Manifest.permission.WRITE_EXTERNAL_STORAGE) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.WRITE_EXTERNAL_STORAGE }, 1);
      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }

##<a name="advanced-reporting"></a>Rapports avancée

Si vous le souhaitez, si vous voulez signaler événements spécifiques de l’application, les erreurs et les tâches, vous devez utiliser l’API Engagement via les méthodes de la `EngagementAgent` cours. Un objet de cette classe peut être récupérés en appelant le `EngagementAgent.getInstance()` méthode statique.

L’API Engagement permet d’utiliser toutes les fonctionnalités avancées d’Engagement et est détaillée dans la procédure à utiliser l’API Engagement sur Android (ainsi que dans la documentation technique de le `EngagementAgent` classe).

##<a name="advanced-configuration-in-androidmanifestxml"></a>Configuration avancée (dans AndroidManifest.xml)

### <a name="wake-locks"></a>Réactiver les verrous

Si vous voulez être sûr que les statistiques sont envoyés en temps réel lors de l’utilisation du Wi-Fi ou lorsque l’écran est éteint, ajoutez l’autorisation facultative suivante :

            <uses-permission android:name="android.permission.WAKE_LOCK"/>

### <a name="crash-report"></a>Rapport d’incidents

Si vous voulez désactiver les rapports de blocage, ajouter ce (entre la `<application>` et `</application>` balises) :

            <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>Rupture seuil

Par défaut, les rapports de service Engagement enregistre en temps réel. Si votre application signale les journaux très souvent, il est préférable pour les journaux de la mémoire tampon et les rapports en une seule fois sur une base de temps standard (cette option est appelée « mode rupture »). Pour ce faire, ajoutez ceci (entre la `<application>` et `</application>` balises) :

            <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

Le mode rupture légèrement augmenter la durée de vie de batterie mais a un impact sur l’analyseur d’Engagement : toutes les sessions et travaux la durée est arrondie à la valeur seuil rupture (ainsi, les sessions et les tâches plus courtes que le seuil rupture peut ne pas être visible). Il est recommandé d’utiliser un seuil rupture n’est plus à 30000 (30 s).

### <a name="session-timeout"></a>Expiration de la session

Par défaut, une session est terminée 10 s après la fin de son activité dernière (ce qui survient généralement en appuyant sur la touche origine ou précédent, en définissant le téléphone inactives ou en découverte d’une autre application). Il s’agit pour éviter un fractionnement de session chaque fois la sortie d’utilisateur et revenir à l’application très rapidement (qui peut se produire quand il décrochez une image, vérifier une notification, etc..). Vous souhaiterez peut-être modifier ce paramètre. Pour ce faire, ajoutez ceci (entre la `<application>` et `</application>` balises) :

            <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

##<a name="disable-log-reporting"></a>Désactiver le rapport du journal

### <a name="using-a-method-call"></a>À l’aide d’un appel de méthode

Si vous voulez arrêter l’envoi des journaux d’Engagement, vous pouvez appeler :

            EngagementAgent.getInstance(context).setEnabled(false);

Cet appel est permanent : elle utilise un fichier de préférences partagés.

Si Engagement est actif lorsque vous appelez cette fonction, il peut prendre une minute pour l’arrêt du service. Cependant, il ne lancer le service du tout la prochaine fois que vous lancez l’application.

Vous pouvez activer journal de création de rapports en appelant la même fonction avec `true`.

### <a name="integration-in-your-own-preferenceactivity"></a>Intégration dans votre propre`PreferenceActivity`

Au lieu de l’appel de cette fonction, vous pouvez également intégrer ce paramètre directement dans votre existant `PreferenceActivity`.

Vous pouvez configurer Engagement pour utiliser votre fichier de préférences (avec le mode approprié) dans le `AndroidManifest.xml` fichier avec `application meta-data`:

-   La `engagement:agent:settings:name` clé est utilisée pour définir le nom du fichier de préférences partagés.
-   La `engagement:agent:settings:mode` clé est utilisée pour définir le mode du fichier Préférences partagés, vous devez utiliser le même mode comme dans votre `PreferenceActivity`. Le mode doit être passé comme un nombre : Si vous utilisez une combinaison des indicateurs de constantes dans votre code, vérifiez la valeur totale.

Engagement toujours utiliser le `engagement:key` booléenne clé dans le fichier de préférences pour la gestion de ce paramètre.

L’exemple suivant de `AndroidManifest.xml` affiche les valeurs par défaut :

            <application>
                [...]
                <meta-data
                  android:name="engagement:agent:settings:name"
                  android:value="engagement.agent" />
                <meta-data
                  android:name="engagement:agent:settings:mode"
                  android:value="0" />

Vous pouvez ajouter un `CheckBoxPreference` dans votre mise en page de préférences comme la suivante :

            <CheckBoxPreference
              android:key="engagement:enabled"
              android:defaultValue="true"
              android:title="Use Engagement"
              android:summaryOn="Engagement is enabled."
              android:summaryOff="Engagement is disabled." />

<!-- URLs. -->
[Device API]: http://go.microsoft.com/?linkid=9876094
