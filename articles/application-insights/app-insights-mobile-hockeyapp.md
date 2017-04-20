<properties
    pageTitle="Analyse des performances pour les applications web mobile avec Analytique développeur | Microsoft Azure"
    description="Application analyse des performances et l’utilisation pour les développeurs de l’application mobile. , bureau, le service web et applications serveur principal avec HockeyApp et les idées de l’Application."
    authors="alancameronwills"
    services="application-insights"
    documentationCenter=""
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="09/19/2016"
    ms.author="awills"/>

# <a name="mobile-analytics-with-hockeyapp-and-application-insights"></a>Mobile Analytique avec HockeyApp et les idées de l’Application

Surveiller les performances et l’utilisation de votre test bêta et applications de bureau et mobiles déployées avec [HockeyApp](https://hockeyapp.net/). Surveiller les applications de service et serveur principal de web prise en charge avec des [Aperçus de Application Visual Studio](app-insights-overview.md). Analyser des données à partir des applications client et serveur dans Analytique et afficher les graphiques en parallèle avec eux dans un tableau de bord Azure.

Microsoft Developer Analytique propose deux solutions pour l’analyse des performances d’application :

* Applications de bureau et **HockeyApp pour mobile** .
 * Distribuer des versions d’essai aux utilisateurs sélectionnés.
 * Analyse des incidents.
 * Télémétrie personnalisé pour l’analyse de l’utilisation.
* **Application perspectives pour les sites web** et les services et applications.
 * Indicateurs de performance et d’utilisation et les alertes.
 * Rapports d’exceptions et suivi de diagnostic.
 * Recherche des diagnostics avec les liens de télémétrie filtrage et connexes.

Ces deux solutions offrent :

 * **[Langage de requête analytique](app-insights-analytics.md)** puissant d’analyse et de diagnostic.
 * **[Exporter des données](app-insights-export-telemetry.md)** dans votre propre espace de stockage.
 * **Tableau de bord intégré** l’affichage des graphiques et des tables.

## <a name="monitor-your-app-components"></a>Surveiller vos composants d’application

Suivez les instructions dans les pages suivantes pour installer le Kit de développement dans votre code et commencer le suivi de votre application.

### <a name="web-apps---application-insights"></a>Applications Web - Application perspectives

* [Application web ASP.NET](app-insights-asp-net.md) 
* [Dans le navigateur Java](app-insights-java-get-started.md)
* [Node.js dans le navigateur](https://github.com/Microsoft/ApplicationInsights-node.js)
* [Services Cloud Azure](app-insights-cloudservices.md)

### <a name="mobile-apps---hockeyapp"></a>Applications mobiles - HockeyApp

* [application iOS](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-ios)
* [Application Mac OS X](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-mac-os-x)
* [Application Android](https://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk)
* [Application Windows universel](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/how-to-create-an-app-for-uwp)
* [Application Windows Phone 8 et 8.1](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-phone-silverlight-apps-80-and-81)
* [Windows Presentation Foundation application](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-wpf-apps)

Pour les applications de bureau de Windows, nous vous recommandons de HockeyApp. Mais vous pouvez également [Envoyer télémétrie à partir d’une application Windows analyse de l’Application](app-insights-windows-desktop.md). Vous souhaiterez peut-être faire pour tester les analyses de l’Application.


## <a name="analytics-and-export-for-hockeyapp-telemetry"></a>Analytique et exportation de télémétrie HockeyApp

Vous pouvez examiner HockeyApp personnalisée et se connecter à l’aide des fonctionnalités Analytique et d’exportation en continu de perspectives sur Application en [configurant un pont](app-insights-hockeyapp-bridge-app.md)de télémétrie.




