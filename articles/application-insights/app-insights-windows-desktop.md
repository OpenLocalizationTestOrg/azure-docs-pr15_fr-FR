<properties 
    pageTitle="Surveiller l’utilisation et les performances des applications de bureau de Windows" 
    description="Analyser l’utilisation et les performances de votre application de bureau de Windows avec HockeyApp et les idées de l’Application." 
    services="application-insights" 
    documentationCenter="windows"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/26/2016" 
    ms.author="awills"/>

# <a name="monitoring-usage-and-performance-in-windows-desktop-apps"></a>Surveiller l’utilisation et les performances dans les applications de bureau de Windows

*Analyse de l’application est en mode Aperçu.*

[Visual Studio Application Insights](app-insights-overview.md) et [HockeyApp](https://hockeyapp.net) vous permettent de contrôler l’application déployée pour l’utilisation et les performances.

> [AZURE.IMPORTANT] Nous vous recommandons [HockeyApp](https://hockeyapp.net) à distribuer et surveiller des applications de bureau et du périphérique. Avec HockeyApp, vous pouvez gérer de distribution, le test direct et commentaires des utilisateurs, mais aussi surveiller les rapports d’utilisation et de blocage. Vous pouvez également [exporter et votre télémétrie avec Analytique de la requête](app-insights-hockeyapp-bridge-app.md).

> Bien que télémétrie peut être envoyé analyse des applications à partir d’une application de bureau, il s’agit principalement utile pour débogage et expérience.


## <a name="to-send-telemetry-to-application-insights-from-a-windows-application"></a>Pour envoyer télémétrie analyse des applications à partir d’une application Windows

1. Dans le [portail Azure](https://portal.azure.com), [créez une ressource d’analyse de l’Application](app-insights-create-new-resource.md). Pour type d’application, sélectionnez l’application ASP.NET.
2. Prendre une copie de la clé d’Instrumentation. Recherchez la clé dans la liste déroulante Essentials de la nouvelle ressource que vous venez de créer. 
3. Dans Visual Studio, modifiez les packages NuGet de votre projet d’application et ajoutez Microsoft.ApplicationInsights.WindowsServer. (Ou choisissez Microsoft.ApplicationInsights si vous voulez simplement sans le système d’exploitation API, sans les modules de collection de sites de télémétrie standard.)
4. Définir la clé d’instrumentation dans votre code :

    `TelemetryConfiguration.Active.InstrumentationKey = "`*votre clé*`";` 

    ou dans ApplicationInsights.config (si vous avez installé une des offres de télémétrie standard) :
 
    `<InstrumentationKey>`*votre clé*`</InstrumentationKey>` 

    Si vous utilisez ApplicationInsights.config, assurez-vous que ses propriétés dans l’Explorateur de solutions sont définies sur **Build Action = Content, copier dans le répertoire de sortie = copier**.
5. [Utiliser l’API](app-insights-api-custom-events-metrics.md) pour envoyer de télémétrie.
6. Exécuter votre application, puis consultez la télémétrie dans la ressource que vous avez créé dans le portail Azure.

## <a name="telemetry"></a>Exemple de code

```C#

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.UserName;
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="next-steps"></a>Étapes suivantes

* [Créer un tableau de bord](app-insights-dashboards.md)
* [Recherche des Diagnostics](app-insights-diagnostic-search.md)
* [Explorer des indicateurs](app-insights-metrics-explorer.md)
* [Écrire des requêtes Analytique](app-insights-analytics.md)
 
