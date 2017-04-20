<properties 
    pageTitle="Application perspectives pour Core ASP.NET" 
    description="Surveiller des applications web de disponibilité, les performances et l’utilisation." 
    services="application-insights" 
    documentationCenter=".net"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/30/2016" 
    ms.author="awills"/>

# <a name="application-insights-for-aspnet-core"></a>Application perspectives pour Core ASP.NET

[Visual Studio Application Insights](app-insights-overview.md) vous permet de suivre votre application web de disponibilité, les performances et l’utilisation. Avec les commentaires que pouvez-vous sur les performances et l’efficacité de votre application dans la nature, vous pouvez rendre plusieurs choix pertinents sur l’orientation de la conception dans chaque cycle de vie de développement.

![Exemple](./media/app-insights-asp-net-core/sample.png)

Vous avez besoin d’un abonnement avec [Microsoft Azure](http://azure.com). Connectez-vous à l’aide d’un compte Microsoft, vous devrez pour Windows, XBox Live ou d’autres services de cloud Microsoft. Votre équipe peut avoir un abonnement d’organisation pour Azure : demandez au propriétaire de vous ajouter à l’aide de votre compte Microsoft.


## <a name="getting-started"></a>Prise en main

Suivez le [guide de mise en route](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started).

## <a name="using-application-insights"></a>À l’aide de perspectives d’Application

Se connecter au [portail Microsoft Azure](https://portal.azure.com) et accédez à la ressource vous créés pour surveiller votre application.

Dans une fenêtre distincte du navigateur, utilisez votre application depuis un certain temps. Vous verrez les données figurant dans les graphiques d’analyse de l’Application. (Vous devrez peut-être cliquer sur Actualiser). Il y ait qu’une petite quantité de données pendant que vous développez, mais ces graphiques vraiment animez lorsque vous publiez votre application et demandez nombreux utilisateurs. 

La page Vue d’ensemble affiche les graphiques de performances que vous êtes susceptibles de vous intéresser : temps réponse du serveur, temps de chargement de page et le nombre d’échecs de requêtes. Cliquez sur graphique quelconque pour afficher plus de graphiques et de données.

Affichages dans le portail appartiennent à deux catégories principales :

* [Métrique Explorer](app-insights-metrics-explorer.md) reflète graphiques et tableaux de mesures et les comptages, tels que des temps de réponse, taux d’échec ou indicateurs que vous avez créés avec l' [API](app-insights-api-custom-events-metrics.md). Filtrer et segmenter les données par les valeurs de propriétés pour obtenir une meilleure compréhension de votre application et ses utilisateurs.
* [Explorateur de recherche](app-insights-diagnostic-search.md) répertorie les événements individuels, tels que des requêtes spécifiques, exceptions, journal traces ou les événements que vous avez créées avec l' [API](app-insights-api-custom-events-metrics.md). Filtrer et effectuer une recherche dans les événements et naviguer entre les événements liés pour étudier des problèmes.
* [Analytique](app-insights-analytics.md) vous permet d’exécuter des requêtes SQL sur votre télémétrie et est un outil puissant d’analyse et de diagnostic.

## <a name="alerts"></a>Alertes

* Vous obtiendrez automatiquement [des alertes de diagnostics proactives](app-insights-proactive-diagnostics.md) à vous relatives aux modifications anormales de taux d’échec et autres indicateurs.
* Configurer la [disponibilité des tests](app-insights-monitor-web-app-availability.md) pour tester votre site Web en permanence à partir d’emplacements dans le monde et obtenez des messages électroniques dès qu’un test échoue.
* Configurer des [alertes métriques](app-insights-monitor-web-app-availability.md) de savoir si statistiques telles que le temps de réponse ou le taux d’exception accédez extérieur limites acceptables.

## <a name="get-more-telemetry"></a>Obtenir plus de télémétrie

* [Télémétrie ajouter à vos pages web](app-insights-javascript.md) pour surveiller l’utilisation des pages et les performances.
* [Dépendances moniteur](app-insights-dependencies.md) pour voir si reste, SQL ou autres ressources externes ralentissent vous.
* [Utiliser l’API](app-insights-api-custom-events-metrics.md) pour envoyer vos propres événements et les mesures pour une vue plus détaillée de performances et l’utilisation de votre application.
* [Les tests de disponibilité](app-insights-monitor-web-app-availability.md) vérifier votre application en permanence à partir du monde entier. 


## <a name="open-source"></a>Ouvrir la source

[Lire et d’y contribuer au code](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)


