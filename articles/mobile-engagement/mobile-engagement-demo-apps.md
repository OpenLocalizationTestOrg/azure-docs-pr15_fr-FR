<properties
    pageTitle="Application de démonstration Azure Engagement Mobile | Microsoft Azure"
    description="Décrit les avantages de l’utilisation de l’application démo Azure Mobile Engagement où télécharger et l’utilisation"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/10/2016"
    ms.author="piyushjo" />

# <a name="azure-mobile-engagement-demo-app"></a>Application de démonstration Azure Engagement Mobile

Nous avons publié une application démo Azure Mobile Engagement pour **iOS**, **Android**et **Windows** plateformes de pour vous aider à trouver des ressources utiles et en savoir plus sur Mobile Engagement.

L’application vous aidera à :

- Trouver facilement des liens utiles vers des ressources Engagement Mobile tels que des vidéos, la documentation, le forum de support et où vous pouvez pour augmenter les demandes de fonctionnalités.
- Notifications d’exemple expérience sont pris en charge par Mobile Engagement pour obtenir des idées pour vos propres applications mobiles.
- Utiliser une implémentation de référence pour étudier la mise en œuvre Engagement Mobile dans votre propre application. Vous pouvez apprendre à :

    - Collecter les données analytique.
    - Mettre en œuvre des scénarios de notification de types de *plein écran INTERSTITIEL* ou *contextuel*avancés.
    - Mise en œuvre d’enquêtes et sondages.
    - Mettre en œuvre des scénarios de données et push push en mode silencieux.   

## <a name="app-installation"></a>Installation de l’application
Cette application est disponible dans le magasin d’application suivant :

- **Application démo universel de Windows**:

    - Téléchargez l’application à l' [application Windows stocker](https://www.microsoft.com/en-us/store/apps/azure-mobile-engagement/9nblggh4qmh2).
    - L’application a été développée comme une application Windows 10 universel. Le code source est disponible sur [GitHub](https://github.com/Azure/azure-mobile-engagement-app-windows).

- **iOS Démo application**:

    - Téléchargez l’application auprès de l' [Apple stocker](https://itunes.apple.com/us/app/azure%20mobile%20engagement/id1105090090).
    - L’application a été développée dans iOS Swift. Le code source est disponible sur [GitHub](https://github.com/Azure/azure-mobile-engagement-app-ios).

- **Application démo android**:

    - Téléchargez l’application auprès de [Google Play stocker](https://play.google.com/store/apps/details?id=com.microsoft.azure.engagement).
    - Le code source est disponible sur [GitHub](https://github.com/Azure/azure-mobile-engagement-app-android).

![Application démo Windows universel][1]

![iOS Démo application][2]
![application démo Android][3]


## <a name="usage"></a>Utilisation

Vous pouvez utiliser cette application des façons suivantes :

**Télécharger l’application sur votre appareil à partir de l’application de stocker les liens (fournies plus haut) :**

>[AZURE.IMPORTANT] Vous n’avez pas besoin un compte Azure ou qu’il soit nécessaire pour se connecter l’application à un serveur principal. L’application fonctionne indépendamment.

- Une fois que vous avez l’application sur votre appareil, vous pouvez faire défiler les liens dans le menu de gauche pour trouver les ressources utiles sur Mobile Engagement.
- Nous avons ajouté le [flux RSS de service](https://aka.ms/azmerssfeed) dans cette application afin que vous êtes toujours mis à jour sur les dernières mises à jour de produit.
- Vous pouvez également faire défiler les exemples de scénarios de notification de rencontrer le type des notifications sont pris en charge par Mobile Engagement pour chaque plate-forme. Ces notifications vous peuvent rencontrer localement--qui se trouve, vous pouvez cliquer sur les boutons sur les écrans pour montrer que vous l’expérience de notifications, qui est identique à l’envoi de notifications à partir de la plateforme Mobile Engagement.

![Menu de l’application pour Windows][4]

![Menu de l’application pour iOS][5]
![menu de l’application pour Android][6]

**Télécharger le code source dans les liens GitHub (fournies plus haut) :**

- Une fois que vous avez téléchargé le code source, ouvrez-le dans l’environnement de développement respectifs--XCode pour iOS, Android Studio pour Android et Visual Studio pour Windows.
- Vous devez ensuite suivre notre [base étapes d’intégration SDK](mobile-engagement-windows-store-dotnet-get-started.md) afin que vous ne pouvez vous connecter de cette application à sa propre instance de principale Mobile Engagement.
    - Vous devez configurer une chaîne de connexion dans l’application.
    - Vous devez également configurer la plateforme de notification push pour votre application.
- Vous remarquerez que l’application elle-même est équipée de Mobile Engagement. Par conséquent, que vous ouvrez l’application après la connexion au serveur principal, vous serez en mesure de voir la session de l’utilisateur, activités, événements et ainsi de suite, sous l’onglet **Moniteur** .
- Vous pourrez également envoyer de notifications de cette application à partir de votre propre instance Engagement Mobile, au lieu d’utiliser des notifications locales.
    - Vous pouvez ici ajouter votre périphérique comme un périphérique de test à l’aide de l’élément de menu **obtenir l’ID de périphérique** dans l’application. Cela vous donne un ID de périphérique que vous enregistrez puis comme un périphérique de test avec votre instance principale plateforme.

    ![ID de périphérique sous Windows][7]

    ![ID de périphérique sur iOS][8]
    ![ID de l’appareil sur Android][9]

## <a name="key-features-of-the-demo-app"></a>Principales fonctionnalités de l’application démo

- Comme mentionné précédemment, avec cette application, vous avez toutes les ressources clés pour Mobile Engagement dans votre main. Vous pouvez faire défiler les liens dans le menu de gauche.

- Vous pouvez rencontrer des notifications d’absence d’application pour chaque plate-forme. Ces notifications peuvent être remises en tant que **Notification uniquement**, où en cliquant sur la notification simplement ouvre un écran de l’application natif (à l’aide de **liaison approfondie**)--ou un **Web annonce**, où vous pouvez fournir du contenu HTML supplémentaire de la fin Mobile Engagement à afficher lorsque vous cliquez sur la notification.

    ![Notifications d’absence d’application][29]

- Sur iOS, vous devez fermer l’application pour afficher les notifications push absence d’application ou de système. Vous pouvez consulter l’implémentation ici pour ajouter des **boutons d’Action**, tels que ceux qui est ajoutés à cette notification d’absence de app *commentaire* et *partage* (afin que l’utilisateur peut prendre directement action à partir de la notification elle-même).

    ![Notifications d’absence d’application sur iOS][11] ![Affichage de notification d’absence d’application sur iOS][14]

- Sur Android, les options qui sont prises en charge sont Ajout de texte multiligne (**Texte long**) ou une image de notification (**Vue d’ensemble**) la notification, ainsi que les **boutons d’Action** (comme pris en charge par iOS).

    ![Notifications d’absence d’application sur Android][12] ![Affichage de notification d’absence d’application sur Android][15]

- Sur Windows 10, vous pouvez voir à quoi des notifications sur le PC. Cette notification apparaît également dans le **Centre de Notification**de Windows 10. Il n’existe aucune prise en charge pour l’ajout de **boutons d’Action** pour le moment dans le Kit de développement Windows.

    ![Notifications d’absence d’application sur Windows][10] ![Affichage absence d’application sur Windows][13]

- Vous pouvez rencontrer des notifications de « dans l’application » par défaut pour chaque plate-forme. Il s’agit d’une expérience en deux étapes où une fenêtre de **Notification** est affichée en premier. Lorsque vous cliquez dessus, il ouvre un plein écran **annonce**, tel qu’affiché dans l’écran suivant. Le contenu de cette annonce provient de votre instance principale Mobile Engagement. Le Kit de développement comporte les modèles pour les notifications et les annonces. Vous pouvez facilement les personnaliser, comme illustré dans cette application démo grâce à l’ajout de notre logo et les couleurs.  

    ![Notifications dans l’application sur Windows][16]

    ![Notifications dans l’application sur iOS][17]  ![Notifications dans l’application sur Android][18]

    **iOS**, **Android**

- Vous pouvez également utiliser la fonctionnalité de **catégorie** d’Engagement Mobile pour personnaliser l’expérience par défaut. Dans l’application démo, nous avons montré pour modifier l’expérience des notifications de deux façons. Notez que la fonctionnalité de catégorie n'est pas encore pris en charge dans le Kit de développement Windows.

    **Plein écran INTERSTITIELLE :**

    ![Dans l’application notification - catégorie INTERSTITIELLE][30]

    ![Catégorie INTERSTITIELLE sur iOS][21]  ![Catégorie INTERSTITIELLE sur Android][22]

    **Notification contextuelle :**

    ![Dans l’application notification - contextuel catégorie][31]

    ![Notification contextuelle sur iOS][19]   ![Notification contextuelle sur Android][20]

**iOS**, **Android**

- Engagement Mobile prennent également en charge un type spécialisé de notification dans l’application appelée **sondages**. Cela vous permet à envoyer des sondages rapides à vos utilisateurs application segmenté. Vous pouvez ajouter des questions et des options pour chaque question comme dans l’écran suivant. Cela sera prise affichée sous forme de notification dans l’application à l’utilisateur de l’application.   

    ![Notifications de sondage][32]

    ![Enquête sur Windows][26]

    ![Enquête sur iOS][27]   ![Enquête sur Android][28]

**iOS**, **Android**

- Engagement mobile prend également en charge l’envoi des notifications de **Transmission des données** en mode silencieux. Avec ces notifications, vous pouvez envoyer des données à partir de votre service (par exemple, les données JSON dans l’exemple suivant), que vous pouvez gérer dans votre application et exécuter une action. Un exemple est comment nous passons le prix d’un élément sélectivement à l’aide de notifications de transmission des données.

    ![Notification d’émission de données][33]

    ![Notification d’émission de données sur Windows][23]

    ![Notification d’émission de données sur iOS][24]  ![Notification d’émission de données sur Android][25]

**iOS**, **Android**

> [AZURE.NOTE] Vous pouvez afficher des instructions détaillées permettant un de ces notifications en cliquant sur **Cliquez ici pour obtenir des instructions sur l’envoi de ces notifications à partir de la plateforme Engagement Mobile** sur un écran de notification d’exemple.


[1]: ./media/mobile-engagement-demo-apps/home-windows.png
[2]: ./media/mobile-engagement-demo-apps/home-ios.png
[3]: ./media/mobile-engagement-demo-apps/home-android.png
[4]: ./media/mobile-engagement-demo-apps/menu-windows.png
[5]: ./media/mobile-engagement-demo-apps/menu-ios.png
[6]: ./media/mobile-engagement-demo-apps/menu-android.png
[7]: ./media/mobile-engagement-demo-apps/device-id-windows.png
[8]: ./media/mobile-engagement-demo-apps/device-id-ios.png
[9]: ./media/mobile-engagement-demo-apps/device-id-android.png
[10]: ./media/mobile-engagement-demo-apps/out-of-app-windows.png
[11]: ./media/mobile-engagement-demo-apps/out-of-app-ios.png
[12]: ./media/mobile-engagement-demo-apps/out-of-app-android.png
[13]: ./media/mobile-engagement-demo-apps/out-of-app-display-windows.png
[14]: ./media/mobile-engagement-demo-apps/out-of-app-display-ios.png
[15]: ./media/mobile-engagement-demo-apps/out-of-app-display-android.png
[16]: ./media/mobile-engagement-demo-apps/in-app-windows.png
[17]: ./media/mobile-engagement-demo-apps/in-app-ios.png
[18]: ./media/mobile-engagement-demo-apps/in-app-android.png
[19]: ./media/mobile-engagement-demo-apps/pop-up-ios.png
[20]: ./media/mobile-engagement-demo-apps/pop-up-android.png
[21]: ./media/mobile-engagement-demo-apps/interstitial-ios.png
[22]: ./media/mobile-engagement-demo-apps/interstitial-android.png
[23]: ./media/mobile-engagement-demo-apps/data-push-windows.png
[24]: ./media/mobile-engagement-demo-apps/data-push-ios.png
[25]: ./media/mobile-engagement-demo-apps/data-push-android.png
[26]: ./media/mobile-engagement-demo-apps/survey-windows.png
[27]: ./media/mobile-engagement-demo-apps/survey-ios.png
[28]: ./media/mobile-engagement-demo-apps/survey-android.png
[29]: ./media/mobile-engagement-demo-apps/out-of-app.png
[30]: ./media/mobile-engagement-demo-apps/in-app-interstitial.png
[31]: ./media/mobile-engagement-demo-apps/in-app-pop-up.png
[32]: ./media/mobile-engagement-demo-apps/notification-poll.png
[33]: ./media/mobile-engagement-demo-apps/notification-data-push.png
