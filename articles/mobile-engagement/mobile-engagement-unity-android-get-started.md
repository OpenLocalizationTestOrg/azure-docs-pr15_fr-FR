<properties
    pageTitle="Prise en main Azure Mobile Engagement pour le déploiement unité Android"
    description="Découvrez comment utiliser Azure Mobile Engagement avec Analytique et les Notifications de transmission pour les applications unité déploiement sur les appareils iOS."
    services="mobile-engagement"
    documentationCenter="unity"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-unity-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-unity-android-deployment"></a>Prise en main Azure Mobile Engagement pour le déploiement unité Android

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Cette rubrique vous montre comment utiliser Azure Mobile Engagement pour comprendre l’utilisation de votre application et comment envoyer les notifications push segmenté les utilisateurs d’une application unité lors du déploiement sur un appareil Android.
Ce didacticiel utilise l’unité classique opérationnelle un didacticiel coudre comme point de départ. Vous devez suivre les étapes décrites dans ce [didacticiel](mobile-engagement-unity-roll-a-ball.md) avant de procéder à l’intégration d’Engagement Mobile que nous présenter dans le didacticiel ci-dessous. 

Ce didacticiel requiert les éléments suivants :

+ [Éditeur d’unité](http://unity3d.com/get-unity)
+ [Unité mobile Engagement SDK](https://aka.ms/azmeunitysdk)
+ Google Android SDK

> [AZURE.NOTE] Pour effectuer ce didacticiel, vous devez disposer d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-android-get-started).

##<a id="setup-azme"></a>Le programme d’installation Mobile Engagement pour votre application Android

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Connecter votre application sur le serveur principal Engagement Mobile

###<a name="import-the-unity-package"></a>Importer le package unité

1. Télécharger le [package Mobile Engagement unité](https://aka.ms/azmeunitysdk) et enregistrez-le sur votre ordinateur local. 

2. Accédez à **actifs -> Importer le Package -> Package personnalisé** et sélectionnez le package que vous avez téléchargée à l’étape ci-dessus. 

    ![][70] 

3. Assurez-vous que tous les fichiers sont sélectionnées et cliquez sur le bouton **Importer** . 

    ![][71] 

4. Une fois l’importation terminée, vous verrez les fichiers SDK importés dans votre projet.  

    ![][72] 

###<a name="update-the-engagementconfiguration"></a>Mettre à jour la EngagementConfiguration

1. Ouvrez le fichier de script **EngagementConfiguration** à partir du dossier SDK et mettre à jour la **ANDROID\_connexion\_chaîne** avec la chaîne de connexion que vous avez obtenu précédemment à partir du portail Azure.  

    ![][73]

2. Enregistrer le fichier 

3. Exécuter **fichier -> Engagement -> Générer manifeste Android**. Il s’agit du plug-in ajouté par le Kit de développement Mobile Engagement et en cliquant sur il met automatiquement à jour vos paramètres de projet. 

    ![][74]

> [AZURE.IMPORTANT] Veillez à exécuter cette méthode chaque fois que vous mettez à jour le fichier **EngagementConfiguration** dans le cas contraire, que vos modifications n’apparaîtront pas dans l’application. 

###<a name="configure-the-app-for-basic-tracking"></a>Configurer l’application de suivi de base

1. Ouvrez le script **PlayerController** joint à l’objet du lecteur pour modification. 

2. Ajoutez les éléments suivants à l’aide de déclaration :

        using Microsoft.Azure.Engagement.Unity;

3. Ajoutez les éléments suivants à le `Start()` méthode
    
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

###<a name="deploy-and-run-the-app"></a>Déploiement et l’exécution de l’application
Vérifiez que vous avez Android SDK installé sur votre ordinateur avant d’essayer de déployer cette application unité sur votre appareil. 

1. Se connecter à un appareil Android à votre ordinateur. 

2. Ouvrir des **fichiers -> Paramètres de génération** 

    ![][40]

3. Sélectionnez **Android** , puis cliquez sur **Commutateur plate-forme**

    ![][51]

    ![][52]

4. Cliquez sur **paramètres du lecteur** , fournissent un identificateur offre valide. 

    ![][53]

5. Cliquez sur **Créer et exécuter**

    ![][54]

6. Vous devrez peut-être fournir un nom de dossier pour stocker le lot Android. 

7. Si tout se passe fin, puis le package sera déployé sur votre appareil connecté et vous devriez voir votre jeu unité sur votre téléphone ! 

##<a id="monitor"></a>Application interrogez la surveillance en temps réel

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Activer les notifications push et dans l’application de messagerie

[AZURE.INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

###<a name="update-the-engagementconfiguration"></a>Mettre à jour la EngagementConfiguration

1. Ouvrez le fichier de script **EngagementConfiguration** à partir du dossier SDK et mettre à jour la **ANDROID\_GOOGLE\_nombre** avec le **Numéro de projet Google** obtenues précédemment à partir du portail de Google Cloud développeur. Il s’agit d’une chaîne de valeur donc veillez à encadrer par des guillemets doubles. 

    ![][75]

2. Enregistrez le fichier. 

3. Exécuter **fichier -> Engagement -> Générer manifeste Android**. Il s’agit du plug-in ajouté par le Kit de développement Mobile Engagement et en cliquant sur il met automatiquement à jour vos paramètres de projet. 

    ![][74]

###<a name="configure-the-app-to-receive-notifications"></a>Configurer l’application pour recevoir des notifications

1. Ouvrez le script **PlayerController** joint à l’objet du lecteur pour modification. 

2. Ajoutez les éléments suivants à le `Start()` méthode

        EngagementReachAgent.Initialize();

3. À présent que l’application est mis à jour, déploiement et l’exécution de l’application sur un appareil conformément aux instructions fournies ci-dessous. 

[AZURE.INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[40]: ./media/mobile-engagement-unity-android-get-started/40.png
[70]: ./media/mobile-engagement-unity-android-get-started/70.png
[71]: ./media/mobile-engagement-unity-android-get-started/71.png
[72]: ./media/mobile-engagement-unity-android-get-started/72.png
[73]: ./media/mobile-engagement-unity-android-get-started/73.png
[74]: ./media/mobile-engagement-unity-android-get-started/74.png
[75]: ./media/mobile-engagement-unity-android-get-started/75.png
[51]: ./media/mobile-engagement-unity-android-get-started/51.png
[52]: ./media/mobile-engagement-unity-android-get-started/52.png
[53]: ./media/mobile-engagement-unity-android-get-started/53.png
[54]: ./media/mobile-engagement-unity-android-get-started/54.png
