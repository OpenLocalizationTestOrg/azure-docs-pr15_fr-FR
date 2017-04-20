<properties
    pageTitle="Configuration avancée pour Mobile Engagement Azure SDK Android"
    description="Décrit les options de configuration avancée, y compris le manifeste Android avec Azure Mobile Engagement SDK Android"
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
    ms.date="10/04/2016"
    ms.author="piyushjo;ricksal" />

# <a name="advanced-configuration-for-azure-mobile-engagement-android-sdk"></a>Configuration avancée pour Mobile Engagement Azure SDK Android

> [AZURE.SELECTOR]
- [Universel de Windows](mobile-engagement-windows-store-advanced-configuration.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advanced-configuration.md)

Cette procédure explique comment configurer les différentes options de configuration pour les applications Azure Mobile Engagement Android.

## <a name="prerequisites"></a>Conditions préalables

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="permission-requirements"></a>Autorisations requises
Certaines options nécessitent des autorisations spécifiques, ce qui sont répertorié ici pour référence et incluses dans la fonctionnalité spécifique. Ajouter les autorisations suivantes à la AndroidManifest.xml de votre projet immédiatement avant ou après le `<application>` balise.

Le code d’autorisation doit ressembler à ce qui suit, où vous renseignez l’autorisation appropriée dans le tableau suivant.

    <uses-permission android:name="android.permission.[specific permission]"/>


| Autorisation | Lorsqu’il est utilisé |
| ---------- | --------- |
| INTERNET | Obligatoire. Création de rapports de base |
| ACCESS_NETWORK_STATE | Obligatoire. Création de rapports de base |
| RECEIVE_BOOT_COMPLETED | Obligatoire. Pour afficher le centre de notifications après le redémarrage de l’appareil |
| WAKE_LOCK | Recommandé. Permet la collecte des données lors de l’utilisation du Wi-Fi ou lorsque l’écran est désactivée |
| VIBRATIONS | Facultatif. Active les vibrations lors de la réception de notifications |
| DOWNLOAD_WITHOUT_NOTIFICATION | Facultatif. Active la Notification Android vue d’ensemble |
| WRITE_EXTERNAL_STORAGE | Facultatif. Active la Notification Android vue d’ensemble |
| ACCESS_COARSE_LOCATION | Facultatif. Active le signalement d’emplacement en temps réel |
| ACCESS_FINE_LOCATION | Facultatif. Active le signalement d’emplacement sur GPS |

En commençant par M Android, [certaines autorisations sont gérées au moment de l’exécution](mobile-engagement-android-location-reporting.md#Android-M-Permissions).

Si vous utilisez déjà ``ACCESS_FINE_LOCATION``, puis que vous n’avez pas besoin d’utiliser également ``ACCESS_COARSE_LOCATION``.

## <a name="android-manifest-configuration-options"></a>Options de configuration manifeste Android

### <a name="crash-report"></a>Rapport d’incidents

Pour désactiver les rapports d’incidents, ajoutez ce code entre la `<application>` et `</application>` balises :

    <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>Rupture seuil

Par défaut, les rapports de service Engagement ouvre une session en temps réel. Si vos journaux de rapport application varie fréquemment, il est préférable pour les journaux de la mémoire tampon et les signaler simultanément sur une base de temps standard (appelé « mode rafale »). Pour ce faire, ajoutez le code suivant entre la `<application>` et `</application>` balises :

    <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

Mode rupture légèrement augmente la durée de vie de batterie mais a un impact sur l’analyseur d’Engagement : toutes les sessions et travaux la durée sont arrondis à la valeur seuil rupture (ainsi, les sessions et les tâches plus courtes que le seuil rupture peut ne pas être visible). Votre seuil rupture doit être pas dépasser 30000 (30 s).

### <a name="session-timeout"></a>Expiration de la session

 Vous pouvez mettre fin à une activité en appuyant sur la touche **famille** ou **vers l’arrière** , en définissant le téléphone inactif ou en découverte d’une autre application. Par défaut, une session se termine dix secondes après la fin de la dernière activité. Cela permet d’éviter un fractionnement session chaque fois que l’utilisateur a quitté et retourne à l’application rapidement, ce qui peut se produire lorsque l’utilisateur sélectionne une image, vérifie une notification, etc.. Vous souhaiterez peut-être modifier ce paramètre. Pour ce faire, ajoutez le code suivant entre la `<application>` et `</application>` balises :

    <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

## <a name="disable-log-reporting"></a>Désactiver le rapport du journal

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
-   La `engagement:agent:settings:mode` clé est utilisée pour définir le mode du fichier Préférences partagés. Utiliser le même mode comme dans votre `PreferenceActivity`. Le mode doit être passé comme un nombre : Si vous utilisez une combinaison des indicateurs de constantes dans votre code, vérifiez la valeur totale.

Engagement toujours utilise le `engagement:key` booléenne clé dans le fichier de préférences pour la gestion de ce paramètre.

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
