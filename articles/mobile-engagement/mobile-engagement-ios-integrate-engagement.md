<properties
    pageTitle="Azure Engagement Mobile iOS intégration SDK | Microsoft Azure"
    description="Dernières mises à jour et procédures pour iOS SDK pour Azure Mobile Engagement"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="piyushjo" />

#<a name="how-to-integrate-engagement-on-ios"></a>Comment faire pour intégrer Engagement sur iOS

> [AZURE.SELECTOR]
- [Universel de Windows](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

Cette procédure explique la façon la plus simple d’activation d’Engagement Analytique et surveillance des fonctions dans votre application iOS.

Le Kit de développement Engagement nécessite iOS6 + et Xcode 8 : la cible du déploiement de votre application doit être au moins iOS 6.

> [AZURE.NOTE]
> Si vous dépendez vraiment XCode 7 vous pouvez utiliser [iOS Engagement SDK v3.2.4](https://aka.ms/r6oouh). Il existe un problème connu sur le module portée de cette version précédente alors qu’en cours d’exécution sur les appareils iOS 10 voir [l’intégration de module accessibles](mobile-engagement-ios-integrate-engagement-reach.md) pour plus d’informations. Si vous choisissez d’utiliser le Kit de développement logiciel v3.2.4 ignorez la `UserNotifications.framework` importer dans l’étape suivante.

Les étapes suivantes sont suffisantes pour activer le rapport des journaux nécessaires pour calculer toutes les statistiques concernant les utilisateurs, Sessions, activités, se bloque et Technicals. Le rapport des journaux nécessaires pour calculer d’autres statistiques telles que les événements, les erreurs et les tâches doit être exécuté manuellement à l’aide de l’API Engagement (voir [comment utiliser l’Engagement Mobile avancées marquage API dans votre application iOS](mobile-engagement-ios-use-engagement-api.md) dans la mesure où ces statistiques sont dépend de l’application.

##<a name="embed-the-engagement-sdk-into-your-ios-project"></a>Incorporer du Kit de développement Engagement dans votre projet iOS

- Téléchargez le Kit de développement logiciel iOS à partir [d’ici](http://aka.ms/qk2rnj).

- Ajouter le Kit de développement Engagement à votre projet iOS : dans Xcode, cliquez avec le bouton droit sur votre projet et sélectionnez **« Ajouter des fichiers à... »** , puis choisissez le `EngagementSDK` dossier.

- Engagement nécessite des structures supplémentaires pour l’utiliser : dans l’Explorateur de projets, ouvrez votre volet du projet et sélectionnez la bonne cible. Ensuite, ouvrez l’onglet **« Créer les phases »** et dans le menu **« lien binaire avec bibliothèques »** , ajoutez ces structures :

    -   `UserNotifications.framework`-définir le lien en tant que`Optional`
    -   `AdSupport.framework`-définir le lien en tant que`Optional`
    -   `SystemConfiguration.framework`
    -   `CoreTelephony.framework`
    -   `CFNetwork.framework`
    -   `CoreLocation.framework`
    -   `libxml2.dylib`

> [AZURE.NOTE] L’infrastructure d’AdSupport peut être supprimée. Engagement doit ce cadre pour recueillir le IDFA. Toutefois, collection IDFA peut être désactivée \<ios-sdk-engagement-idfa\> pour se conformer à la nouvelle stratégie de Apple en ce qui concerne ce code.

##<a name="initialize-the-engagement-sdk"></a>Initialisation l’Engagement SDK

Vous devez modifier votre délégué d’Application :

-   En haut de votre fichier d’implémentation, importer l’agent Engagement :

        [...]
        #import "EngagementAgent.h"

-   Initialisation Engagement à l’intérieur de la méthode «**applicationDidFinishLaunching :**« ou »**application : didFinishLaunchingWithOptions :**» :

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
          [...]
          [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
          [...]
        }

##<a name="basic-reporting"></a>Création de rapports de base

### <a name="recommended-method-overload-your-uiviewcontroller-classes"></a>Méthode recommandée : surcharger votre `UIViewController` classes

Pour activer l’état de tous les journaux requis par Engagement pour calculer les utilisateurs, les Sessions, activités, se bloque et statistiques techniques, vous pouvez simplement effectuer tous les votre `UIViewController` sous-adresse classes héritent de la `EngagementViewController` classes (même règle pour `UITableViewController`  - \> `EngagementTableViewController`).

**Sans Engagement :**

    #import <UIKit/UIKit.h>

    @interface Tab1ViewController : UIViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }

    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

**Avec Engagement :**

    #import <UIKit/UIKit.h>
    #import "EngagementViewController.h"

    @interface Tab1ViewController : EngagementViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }

    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

### <a name="alternate-method-call-startactivity-manually"></a>Méthode alternative : appeler `startActivity()` manuellement

Si vous ne pouvez pas ou que vous ne souhaitez pas surcharger votre `UIViewController` classes, vous pouvez commencer à la place de vos activités en appelant `EngagementAgent`de méthodes directement.

> [AZURE.IMPORTANT] IOS SDK appelle automatiquement le `endActivity()` méthode lorsque l’application est fermée. Par conséquent, il est *vivement* recommandé d’appeler le `startActivity` méthode chaque fois que l’activité de l’utilisateur modifier, puis sur *jamais* appelez le `endActivity` méthode, étant donné que l’appel de cette méthode force la session active à terminer.

##<a name="location-reporting"></a>Reporting sur l’emplacement

Conditions d’utilisation de Apple n’autorisent pas les applications pour utiliser le suivi uniquement à des fins de statistiques d’emplacement. Par conséquent, il est recommandé d’activer emplacement rapports uniquement si votre application également utiliser la recherche pour une autre raison d’emplacement.

En commençant par iOS 8, vous devez fournir une description de la façon dont votre application utilise les services emplacement en définissant une chaîne de la clé [NSLocationWhenInUseUsageDescription] ou [NSLocationAlwaysUsageDescription] Info.plist fichier de votre application. Si vous voulez l’emplacement du rapport en arrière-plan avec Engagement, ajoutez la clé NSLocationAlwaysUsageDescription. Dans tous les autres cas, ajoutez la clé NSLocationWhenInUseUsageDescription.

### <a name="lazy-area-location-reporting"></a>Zone différée reporting sur l’emplacement

Rapport d’emplacement zone différée permet pour signaler le pays, la région et la localisation associés aux appareils. Ce type de reporting sur l’emplacement utilise uniquement les emplacements réseau (basés sur les ID de cellule ou Wi-Fi). La zone de l’appareil est signalée au moins une fois par session. Le GPS n’est jamais utilisé, et ce type de rapport emplacement a donc très peu (ne pas de vous dire non) impact sur la batterie.

Zones signalés sont utilisées pour calculer des statistiques géographiques sur les utilisateurs, les sessions, les événements et les erreurs. Ils permettent également comme critère dans les campagnes accessibles. La dernière zone connue reportée pour un appareil peut être récupérée grâce à l' [API de l’appareil].

Pour activer le rapport d’emplacement zone différée, ajoutez la ligne suivante après l’initialisation de l’agent Engagement :

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
      [...]
      [[EngagementAgent shared] setLazyAreaLocationReport:YES];
      [...]
    }

### <a name="real-time-location-reporting"></a>Emplacement rapports en temps réel

Rapport d’emplacement en temps réel permet pour signaler le latitude et longitude associés aux appareils. Par défaut, ce type de reporting sur l’emplacement utilise uniquement les emplacements réseau (basés sur les ID de cellule ou Wi-Fi) et la création de rapports est active uniquement lorsque l’application est exécutée au premier plan (c'est-à-dire au cours d’une session).

Emplacements en temps réel ne sont *pas* utilisées pour calculer des statistiques. Leur seul objectif est de permettre l’utilisation d’en temps réel geo-clôture \<gardiennage virtuel les participants d’atteindre\> critère dans campagnes marketing accessibles.

Pour activer le rapport d’emplacement en temps réel, ajoutez la ligne suivante après l’initialisation de l’agent Engagement :

    [[EngagementAgent shared] setRealtimeLocationReport:YES];

#### <a name="gps-based-reporting"></a>GPS en fonction de création de rapports

Par défaut, reporting sur l’emplacement en temps réel utilise uniquement les emplacements réseau en fonction. Pour activer l’utilisation des emplacements GPS basé (qui sont beaucoup plus précis), ajoutez :

    [[EngagementAgent shared] setFineRealtimeLocationReport:YES];

#### <a name="background-reporting"></a>Création de rapports d’arrière-plan

Par défaut, reporting sur l’emplacement en temps réel est actif uniquement lorsque l’application est exécuté en avant-plan (c'est-à-dire au cours d’une session). Pour activer la création de rapports également en arrière-plan, ajoutez :

    [[EngagementAgent shared] setBackgroundRealtimeLocationReport:YES withLaunchOptions:launchOptions];

> [AZURE.NOTE] Lorsque l’application est exécutée en arrière-plan, uniquement des emplacements sur le réseau sont signalés, même si vous avez activé le GPS.

Implémentation de cette fonction appelle [startMonitoringSignificantLocationChanges] lorsque votre application s’exécute en arrière-plan. N’oubliez pas qu’il sera automatiquement relancez votre application dans l’arrière-plan si un nouvel événement emplacement arrive.

##<a name="advanced-reporting"></a>Rapports avancée

Si vous le souhaitez, si vous voulez signaler événements spécifiques de l’application, les erreurs et les tâches, vous devez utiliser l’API Engagement via les méthodes de la `EngagementAgent` cours. Un objet de cette classe peut être récupéré en appelant le `[EngagementAgent shared]` méthode statique.

L’API Engagement permet d’utiliser toutes les fonctionnalités avancées d’Engagement et est détaillée dans la procédure d’utiliser l’API Engagement sur iOS (ainsi que dans la documentation technique de le `EngagementAgent` classe).

##<a name="disable-idfa-collection"></a>Désactiver la collection IDFA

Par défaut, Engagement utilisera [IDFA] pour identifier un utilisateur. Mais si vous n’utilisez pas de publicité ailleurs dans l’application, vous pouvez être rejeté par le processus de révision App Store. Collection de sites IDFA peut être désactivée en ajoutant la macro de préprocesseur `ENGAGEMENT_DISABLE_IDFA` dans votre fichier spécifiant un nom (ou dans le `Build Settings` de votre application). Cela garantit qu’il n’existe aucune référence à `ASIdentifierManager`, `advertisingIdentifier` ou `isAdvertisingTrackingEnabled` dans la version de l’application.

Intégration dans le fichier **prefix.pch** :

    #define ENGAGEMENT_DISABLE_IDFA
    ...

Vous pouvez vérifier que la collection IDFA est correctement désactivée dans votre application en vérifiant les journaux de test Engagement. Consultez l’intégration de Test\<idfa du test engagement ios sdk\> documentation pour plus d’informations.

##<a name="disable-log-reporting"></a>Désactiver le rapport du journal

### <a name="using-a-method-call"></a>À l’aide d’un appel de méthode

Si vous voulez arrêter l’envoi des journaux d’Engagement, vous pouvez appeler :

    [[EngagementAgent shared] setEnabled:NO];

Cet appel est permanent : elle utilise `NSUserDefaults` pour stocker les informations.

Vous pouvez activer journal de création de rapports en appelant la même fonction avec `YES`.

### <a name="integration-in-your-settings-bundle"></a>Intégration dans votre groupe de paramètres

Au lieu de l’appel de cette fonction, vous pouvez également intégrer ce paramètre directement dans votre existant `Settings.bundle` fichier. La chaîne `engagement_agent_enabled` doit être utilisé comme un l’identificateur de préférences et qu’il doivent être associés à un bouton bascule (`PSToggleSwitchSpecifier`).

L’exemple suivant de `Settings.bundle` montre comment mettre en œuvre :

    <dict>
        <key>PreferenceSpecifiers</key>
        <array>
            <dict>
                <key>DefaultValue</key>
                <true/>
                <key>Key</key>
                <string>engagement_agent_enabled</string>
                <key>Title</key>
                <string>Log reporting enabled</string>
                <key>Type</key>
                <string>PSToggleSwitchSpecifier</string>
            </dict>
        </array>
        <key>StringsTable</key>
        <string>Root</string>
    </dict>

<!-- URLs. -->
[API de l’appareil]: http://go.microsoft.com/?linkid=9876094
[NSLocationWhenInUseUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26
[NSLocationAlwaysUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18
[startMonitoringSignificantLocationChanges]:http://developer.apple.com/library/IOs/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html#//apple_ref/occ/instm/CLLocationManager/startMonitoringSignificantLocationChanges
[IDFA]:https://developer.apple.com/library/ios/documentation/AdSupport/Reference/ASIdentifierManager_Ref/ASIdentifierManager.html#//apple_ref/occ/instp/ASIdentifierManager/advertisingIdentifier
