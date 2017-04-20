<properties
    pageTitle="Prise en main Android applications Azure Mobile Engagement"
    description="Découvrez comment utiliser Azure Mobile Engagement avec notifications push et d’analytique pour les applications Android."
    services="mobile-engagement"
    documentationCenter="android"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="hero-article"
    ms.date="08/10/2016"
    ms.author="piyushjo;ricksal" />

# <a name="get-started-with-azure-mobile-engagement-for-android-apps"></a>Prise en main Azure Mobile Engagement pour les applications Android

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Cette rubrique vous montre comment utiliser Azure Mobile Engagement pour comprendre l’utilisation de votre application et comment envoyer les notifications push aux utilisateurs segmentés d’une application Android.
Ce didacticiel montre le scénario de diffusion simple à l’aide d’Engagement Mobile. Dans, vous créez une application vierge Android qui collecte des données de base et reçoit les notifications push à l’aide de Google Cloud messagerie (GCM).

## <a name="prerequisites"></a>Conditions préalables

Fin de ce didacticiel requiert les [Outils de développement Android](https://developer.android.com/sdk/index.html), qui inclut l’environnement de développement intégré Android et la dernière plateforme Android.

Elle requiert également le [Kit de développement Mobile Engagement Android](https://aka.ms/vq9mfn).

> [AZURE.IMPORTANT] Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-android-get-started).

## <a name="set-up-mobile-engagement-for-your-android-app"></a>Configurer la Engagement Mobile pour votre application Android

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a name="connect-your-app-to-the-mobile-engagement-backend"></a>Connecter votre application sur le serveur principal Engagement Mobile

Ce didacticiel présente une « intégration de base », ce qui correspond à l’ensemble minimal requis pour collecter les données et envoyer une notification push. Vous créez une application de base avec Android Studio pour illustrer l’intégration.

Vous trouverez la documentation intégration complète dans l' [intégration du Kit de développement Mobile Engagement Android](mobile-engagement-android-sdk-overview.md).

### <a name="create-an-android-project"></a>Créer un projet Android

1. Démarrez **Studio Android**et dans le menu contextuel, sélectionnez **Démarrer un nouveau projet Studio Android**.

    ![][1]

2. Fournir un domaine d’application nom et la société. Prenez note de ce que vous remplissez, car vous en avez besoin ultérieurement. Cliquez sur **suivant**.

    ![][2]

3. Sélectionnez le niveau de l’API et le facteur de forme cible, puis cliquez sur **suivant**.

    >[AZURE.NOTE] Engagement Mobile nécessite le niveau de l’API 10 minimale (2.3.3 Android).

    ![][3]

4. Sélectionnez **Activité vide** ici, ce qui correspond à l’écran uniquement pour cette application et cliquez sur **suivant**.

    ![][4]

5. Enfin, laissez les valeurs par défaut et cliquez sur **Terminer**.

    ![][5]

Android Studio crée alors l’application de démonstration dans lequel nous intégrer Mobile Engagement.

### <a name="include-the-sdk-library-in-your-project"></a>Inclure la bibliothèque SDK dans votre projet

1. Téléchargez le [Kit de développement logiciel Engagement Mobile Android](https://aka.ms/vq9mfn).
2. Extraire le fichier d’archive vers un dossier sur votre ordinateur.
3. Identifiez la bibliothèque .jar pour la version actuelle de ce Kit de développement et copier dans le Presse-papiers.

      ![][6]

4. Accédez à la section **projet** (1) et collez le .jar dans le dossier et les bibliothèques (2).

      ![][7]

5. Pour charger la bibliothèque, synchronisez le projet.

      ![][8]

### <a name="connect-your-app-to-mobile-engagement-backend-with-the-connection-string"></a>Se connecter votre application à principal Mobile Engagement avec la chaîne de connexion

1. Copiez les lignes de code suivantes dans la création de l’activité (doit être effectuée uniquement dans un seul endroit de votre application, généralement l’activité principale). Pour cette application exemple, ouvrez la MainActivity sous src -> principaux -> dossier java et ajoutez les éléments suivants :

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
        EngagementAgent.getInstance(this).init(engagementConfiguration);

2. Résoudre les références en appuyant sur Alt + Entrée ou en ajoutant des instructions d’importation suivantes :

        import com.microsoft.azure.engagement.EngagementAgent;
        import com.microsoft.azure.engagement.EngagementConfiguration;

3. Revenez au portail classique Azure dans la page **d’Informations sur la connexion** de votre application et copiez la **Chaîne de connexion**.

      ![][9]

4. Coller dans le `setConnectionString` paramètre, remplacer la totalité de la chaîne indiqué dans le code suivant :

        engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

### <a name="add-permissions-and-a-service-declaration"></a>Ajouter une déclaration de service et des autorisations

1. Ajouter les autorisations suivantes à la Manifest.xml de votre projet immédiatement avant ou après le `<application>` balise :

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

2. Pour déclarer le service agent, ajoutez ce code entre la `<application>` et `</application>` balises :

        <service
            android:name="com.microsoft.azure.engagement.service.EngagementService"
            android:exported="false"
            android:label="<Your application name>"
            android:process=":Engagement"/>

3. Dans le code collé, remplacez `"<Your application name>"` dans l’étiquette, qui est affiché dans le menu **paramètres** dans laquelle vous pouvez voir services en cours d’exécution sur l’appareil. Vous pouvez par exemple ajouter le mot « Service » dans cette étiquette.

### <a name="send-a-screen-to-mobile-engagement"></a>Envoyer un écran à Engagement Mobile

Pour commencer à envoyer des données et assurez-vous que les utilisateurs sont activées, vous devez envoyer au moins un écran (activité) sur le serveur principal Mobile Engagement.

Accédez à **MainActivity.java** et ajoutez le code suivant pour remplacer la classe de base de **MainActivity** à **EngagementActivity**:

    public class MainActivity extends EngagementActivity {

> [AZURE.NOTE] Si votre classe de base n’est pas *activité*, consultez [Avancées Android création de rapports](mobile-engagement-android-advanced-reporting.md#modifying-your-codeactivitycode-classes) pour savoir comment héritent de classes différentes.


Commentez la ligne suivante dans ce scénario simple exemple :

    // setSupportActionBar(toolbar);

Si vous souhaitez conserver la `ActionBar` dans votre application, voir [Avancés des rapports Android](mobile-engagement-android-advanced-reporting.md#modifying-your-codeactivitycode-classes).

## <a name="connect-app-with-real-time-monitoring"></a>Application interrogez la surveillance en temps réel

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a name="enable-push-notifications-and-in-app-messaging"></a>Activer les notifications push et dans l’application de messagerie

Pendant une campagne, Engagement Mobile vous permet d’interagir avec et atteindre vos utilisateurs avec les notifications push et dans l’application de messagerie. Ce module est appelé accessibles dans le portail d’Engagement Mobile.
La section suivante configure votre application pour les recevoir.

### <a name="copy-sdk-resources-in-your-project"></a>Copier les ressources SDK dans votre projet

1. Accédez à votre contenu de téléchargement du Kit de développement logiciel et copiez le dossier **res** .

    ![][10]

2. Accédez à Android Studio, sélectionnez le répertoire **principal** de vos fichiers de projet, puis collez-le pour ajouter les ressources à votre projet.

    ![][11]

[AZURE.INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[AZURE.INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[AZURE.INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

## <a name="next-steps"></a>Étapes suivantes

Accédez à [Android SDK](mobile-engagement-android-sdk-overview.md) pour connaître en détail à propos de l’intégration du Kit de développement logiciel.

<!-- Images. -->
[1]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[2]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[3]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[4]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[5]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[6]: ./media/mobile-engagement-android-get-started/sdk-content.png
[7]: ./media/mobile-engagement-android-get-started/paste-jar.png
[8]: ./media/mobile-engagement-android-get-started/sync-project.png
[9]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[10]: ./media/mobile-engagement-android-get-started/copy-resources.png
[11]: ./media/mobile-engagement-android-get-started/paste-resources.png
