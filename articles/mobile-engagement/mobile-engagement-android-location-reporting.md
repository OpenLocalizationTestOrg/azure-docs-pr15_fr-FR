<properties
    pageTitle="Emplacement de création de rapports Kit de développement logiciel Azure Engagement Mobile Android"
    description="Décrit comment configurer l’emplacement de création de rapports pour Mobile Engagement Azure SDK Android"
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
    ms.date="08/12/2016"
    ms.author="piyushjo;ricksal" />

# <a name="location-reporting-for-azure-mobile-engagement-android-sdk"></a>Emplacement de création de rapports Kit de développement logiciel Azure Engagement Mobile Android

> [AZURE.SELECTOR]
- [Android](mobile-engagement-android-integrate-engagement.md)

Cette rubrique décrit comment effectuer l’emplacement de création de rapports pour votre application Android.

## <a name="prerequisites"></a>Conditions préalables

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="location-reporting"></a>Reporting sur l’emplacement

Si vous souhaitez créer des emplacements à signaler, vous devez ajouter quelques lignes de configuration (entre la `<application>` et `</application>` balises).

### <a name="lazy-area-location-reporting"></a>Zone différée reporting sur l’emplacement

Emplacement de la zone différée création de rapports vous permettent de conserver signalé pays, la région et la localisation associées aux périphériques. Ce type de reporting sur l’emplacement utilise uniquement les emplacements réseau (basés sur les ID de cellule ou Wi-Fi). La zone de l’appareil est signalée au moins une fois par session. Le périphérique GPS n’est jamais utilisé et par conséquent ce type de rapport d’emplacement a un faible impact sur la batterie.

Zones signalés sont utilisées pour calculer des statistiques géographiques sur les utilisateurs, les sessions, les événements et les erreurs. Ils permettent également comme critère dans les campagnes accessibles.

Vous activez emplacement de la zone différée création de rapports à l’aide de la configuration précédemment mentionnée dans cette procédure :

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Vous devez également spécifier une autorisation d’emplacement. Ce code utilise ``COARSE`` autorisation :

    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Si votre application l’exige, vous pouvez utiliser ``ACCESS_FINE_LOCATION`` à la place.

### <a name="real-time-location-reporting"></a>Création de rapports emplacement en temps réel

Emplacement en temps réel signalé Active le signalement la latitude et longitude associées aux périphériques. Par défaut, ce type de reporting sur l’emplacement utilise uniquement les emplacements réseau, en fonction de l’ID de cellule ou Wi-Fi. La création de rapports est active uniquement lorsque l’application est exécutée au premier plan (par exemple, au cours d’une session).

Emplacements en temps réel ne sont *pas* utilisées pour calculer des statistiques. Leur seul objectif est de permettre l’utilisation de geo-clôture en temps réel \<gardiennage virtuel les participants d’atteindre\> critère dans campagnes marketing accessibles.

Pour activer l’emplacement en temps réel signalé, ajoutez une ligne de code à l’endroit où vous définir la chaîne de connexion Engagement dans l’activité du Lanceur d’applications. Le résultat se présente comme suit :

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

        You also need to specify a location permission. This code uses ``COARSE`` permission:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

        If your app requires it, you can use ``ACCESS_FINE_LOCATION`` instead.

#### <a name="gps-based-reporting"></a>GPS en fonction de création de rapports

Par défaut, emplacement en temps réel reporting utilise uniquement les emplacements sur le réseau. Pour activer l’utilisation des emplacements GPS, qui sont beaucoup plus précis, utilisez l’objet de configuration :

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Vous devez également ajouter l’autorisation suivante s’il est manquant :

    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>Création de rapports d’arrière-plan

Par défaut, reporting sur l’emplacement en temps réel est actif uniquement lorsque l’application est exécutée au premier plan (par exemple, au cours d’une session). Pour activer la création de rapports également en arrière-plan, utilisez cet objet de configuration :

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [AZURE.NOTE] Lorsque l’application est exécutée en arrière-plan, seuls les emplacements réseau sont signalés, même si vous avez activé le GPS.

Si l’utilisateur redémarre leur appareil, le rapport d’emplacement d’arrière-plan est arrêté. Pour qu’il redémarre automatiquement au démarrage, ajoutez ce code.

    <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
           android:exported="false">
        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED" />
        </intent-filter>
    </receiver>

Vous devez également ajouter l’autorisation suivante s’il est manquant :

    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

## <a name="android-m-permissions"></a>Autorisations M Android

En commençant par M Android, certaines autorisations sont gérées lors de l’exécution et vous avez besoin d’approbation de l’utilisateur.

Si vous ciblez niveau API Android 23, les autorisations de runtime sont désactivées par défaut pour les nouvelles installations d’application. Dans le cas contraire elles sont activées par défaut.

Vous pouvez activer/désactiver ces autorisations dans le menu Paramètres du périphérique. La désactivation des autorisations dans le menu système arrête les processus d’arrière-plan de l’application, qui est un comportement du système et n’a aucun impact sur la possibilité de recevoir push en arrière-plan.

Dans le contexte d’emplacement Engagement Mobile Création de rapports, les autorisations qui doivent être approuvés lors de l’exécution sont :

- `ACCESS_COARSE_LOCATION`
- `ACCESS_FINE_LOCATION`

Demander des autorisations de l’utilisateur à l’aide d’une boîte de dialogue système standard. Si l’utilisateur approuve, indiquez ``EngagementAgent`` pour effectuer cette modification en considération en temps réel. Dans le cas contraire, la modification est traitée la prochaine fois que l’utilisateur lance l’application.

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
         * Request location permission, but this doesn't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }
