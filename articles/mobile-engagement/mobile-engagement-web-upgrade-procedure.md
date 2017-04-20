<properties
    pageTitle="Procédures de mise à niveau Kit de développement Mobile Engagement Web Azure | Microsoft Azure"
    description="Les dernières mises à jour et procédures pour le Kit de développement Web pour Azure Mobile Engagement"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="web"
    ms.devlang="js"
    ms.topic="article"
    ms.date="06/07/2016"
    ms.author="piyushjo" />


# <a name="azure-mobile-engagement-web-sdk-upgrade-procedures"></a>Procédures de mise à niveau Kit de développement Mobile Engagement Web Azure

Si vous avez déjà intégré une version antérieure du Azure Mobile Engagement Web SDK dans votre application web, vous devez prendre en considération les points suivants lorsque vous mettez à niveau le Kit de développement.

Si vous avez ignoré plusieurs versions du Kit de développement Mobile Engagement Web, vous devrez peut-être terminer plusieurs procédures au cours du processus de mise à niveau. Par exemple, si vous migrez 1.4.0 à 1.6.0, suivre les procédures permettant de mettre à niveau 1.4.0 vers 1.5.0. Ensuite, suivez les procédures permettant de mettre à niveau 1.5.0 vers 1.6.0.

N’importe quelle version mise à niveau à partir de, remplacez n’importe quelle version antérieure du fichier azure-engagement.js avec la dernière version du fichier.

## <a name="upgrade-from-121-to-200"></a>Mettre à niveau 1.2.1 vers 2.0.0

Cette section décrit comment migrer une intégration Kit de développement Mobile Engagement Web à partir du service Capptain, offert par Capptain SAS, pour une application Azure Mobile Engagement. Si vous effectuez une migration depuis une version antérieure, veuillez consulter le site Web Capptain pour tout d’abord migrer vers 1.2.1, puis appliquez les procédures suivantes.

Cette version du Kit de développement Mobile Engagement Web non prises en charge télévision intelligente Samsung, Opera TV, webOS ou la fonctionnalité accessibles.

>[AZURE.IMPORTANT] Capptain et Azure Mobile Engagement ne sont pas le même service. La procédure suivante met en surbrillance uniquement comment migrer l’application client. Migrer le Kit de développement Mobile Engagement Web dans l’application ne doit pas migrer vos données à partir d’un serveur Capptain sur un serveur Mobile Engagement.

### <a name="javascript-files"></a>Fichiers JavaScript

Remplacer le fichier capptain-sdk.js par le fichier azure-engagement.js et mettez à jour votre importations script en conséquence.

### <a name="remove-capptain-reach"></a>Supprimer Capptain accessibles

Cette version du Kit de développement Mobile Engagement Web non prises en charge la fonctionnalité accessibles. Si vous avez intégré Capptain accessibles dans votre application, vous devez le supprimer.

Supprimer l’importation atteigne CSS à partir de votre page et supprimez le fichier .css connexes (capptain-reach.css, par défaut).

Supprimer les ressources accessibles suivantes : l’image fermer (capptain-close.png, par défaut) et l’icône de marque (capptain--icône de notification, par défaut).

Supprimer l’interface utilisateur de joindre des notifications dans l’application. La mise en page par défaut ressemble à ceci :

    <!-- capptain notification -->
    <div id="capptain_notification_area" class="capptain_category_default">
      <div class="icon">
        <img src="capptain-notification-icon.png" alt="icon" />
      </div>
      <div class="content">
        <div class="title" id="capptain_notification_title"></div>
        <div class="message" id="capptain_notification_message"></div>
      </div>
      <div id="capptain_notification_image"></div>
      <div>
        <button id="capptain_notification_close">Close</button>
      </div>
    </div>

Supprimer l’interface utilisateur atteigne pour le texte et les annonces web et les sondages. La mise en page par défaut ressemble à ceci :

    <div id="capptain_overlay" class="capptain_category_default">
      <button id="capptain_overlay_close">x</button>
      <div id="capptain_overlay_title"></div>
      <div id="capptain_overlay_body"></div>
      <div id="capptain_overlay_poll"></div>
      <div id="capptain_overlay_buttons">
        <button id="capptain_overlay_exit"></button>
        <button id="capptain_overlay_action"></button>
      </div>
    </div>

Supprimer la `reach` objet à partir de votre configuration, le cas échéant. Il ressemble à ceci :

    window.capptain = {
      [...]
      reach: {
        [...]
      }
    }

Supprimer toute autre personnalisation accessibles, telles que les catégories.

### <a name="remove-deprecated-apis"></a>Supprimer les API à éviter

Certaines API de Capptain est déconseillées dans le Kit de développement Mobile Engagement Web.

Supprimez les appels à l’API suivantes : `agent.connect`, `agent.disconnect`, `agent.pause`, et `agent.sendMessageToDevice`.

Supprimer toutes les instances des rappels suivants de votre configuration Capptain : `onConnected`, `onDisconnected`, `onDeviceMessageReceived`, et `onPushMessageReceived`.

### <a name="configuration"></a>Configuration

Engagement mobile utilise une chaîne de connexion pour configurer les identificateurs SDK, par exemple, l’identificateur d’application.

Remplacez l’ID de l’application par la chaîne de connexion. Notez que les modifications provenant de l’objet global pour la configuration du Kit de développement logiciel `capptain` à `azureEngagement`.

Avant la migration :

    window.capptain = {
      appId: ...,
      [...]
    };

Après la migration :

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      [...]
    };

La chaîne de connexion pour votre application s’affiche dans le portail Azure.

### <a name="javascript-apis"></a>API JavaScript

L’objet JavaScript global `window.capptain` a été renommé `window.azureEngagement` mais vous pouvez utiliser la `window.engagement` alias pour les appels API. Vous ne pouvez pas utiliser l’alias pour définir la configuration du Kit de développement logiciel.

Par exemple, `capptain.deviceId` devient `engagement.deviceId`, `capptain.agent.startActivity` devient `engagement.agent.startActivity`, et ainsi de suite.
