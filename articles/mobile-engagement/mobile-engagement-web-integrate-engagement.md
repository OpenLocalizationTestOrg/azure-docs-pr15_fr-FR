<properties
    pageTitle="Intégration de kit de développement Mobile Engagement Web Azure | Microsoft Azure"
    description="Les dernières mises à jour et procédures pour le SDK Azure Mobile Engagement Web"
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
    ms.date="02/29/2016"
    ms.author="piyushjo" />

#<a name="integrate-azure-mobile-engagement-in-a-web-application"></a>Intégrer Azure Mobile Engagement dans une application web

> [AZURE.SELECTOR]
- [Universel de Windows](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

Les procédures décrites dans cet article décrivent la manière la plus simple pour activer l’analytique et les fonctions de surveillance dans Azure Mobile Engagement dans votre application web.

Suivez les étapes pour activer les rapports du journal qui sont nécessaires pour calculer toutes les statistiques sur les utilisateurs, les sessions, activités, se bloque et technicals. Pour les statistiques dépendent de l’application, tels que des événements, les erreurs et les tâches, vous devez activer manuellement les rapports du journal à l’aide de l’API Azure Mobile Engagement. Pour plus d’informations, Découvrez [comment utiliser l’Engagement Mobile avancées marquage API dans une application web](mobile-engagement-web-use-engagement-api.md).

## <a name="introduction"></a>Introduction

[Télécharger le Web Azure Engagement Mobile SDK](http://aka.ms/P7b453).
Le Kit de développement Mobile Engagement Web est fourni sous forme d’un seul fichier JavaScript, azure-engagement.js, vous devez inclure dans chaque page de votre site ou une application web.

> [AZURE.IMPORTANT] Avant d’exécuter ce script, vous devez exécuter un extrait de code ou un script que vous écrivez pour configurer Engagement Mobile de votre application.

## <a name="browser-compatibility"></a>Compatibilité avec les navigateurs

Le Kit de développement Mobile Engagement Web utilise native JSON encodage et de décodage, en plus de demandes AJAX domaines (s’appuyant sur la spécification W3C CORS). Il est compatible avec les navigateurs suivants :

* Microsoft Edge 12 +
* Internet Explorer 10 +
* Firefox 3.5 +
* Chrome 4 +
* Safari 6 +
* Opera 12 +

## <a name="configure-mobile-engagement"></a>Configurer Engagement Mobile

Écrire un script qui crée un global `azureEngagement` objet JavaScript, comme dans l’exemple suivant. Étant donné que votre site peut avoir pages multiples, cela suppose que ce script est inclus dans chaque page. Dans cet exemple, l’objet JavaScript est nommé `azure-engagement-conf.js`.

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
    };

La `connectionString` valeur de votre application s’affiche dans le portail Azure.

> [AZURE.NOTE] `appVersionName`et `appVersionCode` sont facultatives. Toutefois, nous vous recommandons de les configurer pour qu’analytique puisse traiter les informations de version.

## <a name="include-mobile-engagement-scripts-in-your-pages"></a>Inclure des scripts Engagement Mobile dans vos pages
Ajouter des scripts Engagement Mobile à vos pages dans une des façons suivantes :

    <head>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </head>

Ou cela :

    <body>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </body>

## <a name="alias"></a>Alias

Une fois le script Kit de développement Mobile Engagement Web est chargé, il crée l’alias **d’engagement** pour accéder à l’API SDK. Vous ne pouvez pas utiliser cet alias pour définir la configuration du Kit de développement logiciel. Cet alias est utilisé comme référence dans cette documentation.

Notez que si l’alias par défaut est en conflit avec une autre variable globale à partir de votre page, vous pouvez redéfinir dans la configuration comme suit avant de charger le Kit de développement Mobile Engagement Web :

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
      alias:'anotherAlias'
    };

## <a name="basic-reporting"></a>Création de rapports de base

Rapports de base lors de l’Engagement Mobile couvre les statistiques de niveau session, tels que des statistiques sur les utilisateurs, les sessions, les activités et les incidents.

### <a name="session-tracking"></a>Suivi de session

Une session Engagement Mobile est divisée en une séquence d’activités, chacune identifiée par un nom.

Dans un site Web classique, nous vous recommandons de déclarer une activité différente sur chaque page de votre site. Pour une site Web ou une application web dans laquelle la page active change jamais, vous souhaiterez peut-être suivre les activités sur une échelle plus petite, par exemple dans la page.

Dans les deux cas, pour démarrer ou modifier l’activité des utilisateurs en cours, appelez le `engagement.agent.startActivity` fonction. Par exemple :

    <body onload="yourOnload()">

    <!-- -->

    yourOnload = function() {
      [...]
      engagement.agent.startActivity('welcome');
    };

Le serveur Mobile Engagement termine automatiquement une session ouverte dans les trois minutes après la fermeture de la page d’application.

Par ailleurs, vous pouvez terminer une session manuellement en appelant `engagement.agent.endActivity`. Cette option définit l’activité utilisateur actuel pour « Idle ».  La session va se terminer 10 secondes plus tard, sauf si un nouvel appel à `engagement.agent.startActivity` reprend la session.

Vous pouvez configurer le délai de 10 secondes dans l’objet engagement global, comme suit :

    engagement.sessionTimeout = 2000; // 2 seconds
    // or
    engagement.sessionTimeout = 0; // end the session as soon as endActivity is called

> [AZURE.NOTE] Vous ne pouvez pas utiliser `engagement.agent.endActivity` dans les `onunload` rappel, car vous ne pouvez pas passer des appels AJAX à ce stade.

## <a name="advanced-reporting"></a>Rapports avancée

Vous pouvez également si vous souhaitez déclarer des tâches, les erreurs et les événements d’application, vous devez utiliser l’API Engagement Mobile. Vous accédez à l’API Engagement Mobile via la `engagement.agent` objet.

Vous pouvez accéder à toutes les fonctions avancées dans Engagement Mobile dans l’API d’Engagement Mobile. L’API est détaillée dans l’article [comment utiliser l’Engagement Mobile avancées marquage API dans une application web](mobile-engagement-web-use-engagement-api.md).

## <a name="customize-the-urls-used-for-ajax-calls"></a>Personnaliser les URL utilisées pour les appels AJAX

Vous pouvez personnaliser les URL qui utilise le Kit de développement Mobile Engagement Web. Par exemple, pour redéfinir l’URL de connexion (point de terminaison du Kit de développement pour l’enregistrement), vous pouvez ignorer la configuration comme suit :

    window.azureEngagement = {
      ...
      urls: {
        ...        
        getLoggerUrl: function() {
        return 'someProxy/log';
        }
      }
    };

Si votre URL renvoient une chaîne commençant par `/`, `//`, `http://`, ou `https://`, le modèle par défaut n’est pas utilisé. Par défaut, la `https://` schéma est utilisé pour ces URL. Si vous voulez personnaliser le modèle par défaut, remplacer la configuration, comme suit :

    window.azureEngagement = {
      ...
      urls: {
        ...      
        scheme: '//'
      }
    };
