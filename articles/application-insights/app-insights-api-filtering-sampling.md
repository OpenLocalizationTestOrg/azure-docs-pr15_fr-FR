<properties 
    pageTitle="Filtrage et prétraitement dans le Kit de développement de perspectives Application | Microsoft Azure" 
    description="Écrire des processeurs de télémétrie et initialiseurs de télémétrie pour le Kit de développement pour filtrer ou ajouter des propriétés aux données avant la télémétrie est envoyée au portail Application perspectives." 
    services="application-insights"
    documentationCenter="" 
    authors="beckylino" 
    manager="douge"/>
 
<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="08/30/2016" 
    ms.author="borooji"/>

# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Filtrage et prétraitement télémétrie dans le Kit de développement de perspectives Application

*Analyse de l’application est en mode Aperçu.*

Vous pouvez écrire et configurer les plug-ins pour le Kit de développement de perspectives Application Personnaliser la façon télémétrie est capturé et traitée avant d’être envoyé au service Application perspectives. 

Ces fonctionnalités sont actuellement disponibles pour le Kit de développement ASP.NET.

* [Échantillonnage](app-insights-sampling.md) permet de réduire le volume de télémétrie sans modifier vos statistiques. Elle conserve ensemble liés points de données de sorte que vous pouvez vous déplacer entre les diagnostiquer un problème. Dans le portail, le nombre total est multiplié pour compense l’échantillon.
* [Filtrage à l’aide de télémétrie processeurs](#filtering) vous permet de sélectionner ou modifier des télémétrie dans le Kit de développement avant d’être envoyé au serveur. Par exemple, vous pouvez réduire le volume de télémétrie en excluant des demandes de robots. Mais filtrage est une approche plus simple pour réduire le trafic que l’échantillonnage. Il vous permet de mieux contrôler ce qui est transmis, mais vous devez Sachez qu’il affecte vos statistiques - par exemple, si vous filtrez toutes les demandes réussies.
* [Initialiseurs de télémétrie ajouter une propriété](#add-properties) à n’importe quel télémétrie envoyé à partir de votre application, y compris télémétrie à partir des modules standards. Par exemple, vous pouvez ajouter des valeurs calculées ; ou des numéros de version par lequel filtrer les données dans le portail.
* [Le Kit de développement logiciel API](app-insights-api-custom-events-metrics.md) est utilisé pour envoyer les mesures et les événements personnalisés.


Avant de commencer :

* Installer l' [Application Insights Kit de développement pour ASP.NET v2](app-insights-asp-net.md) dans votre application. 


<a name="filtering"></a>
## <a name="filtering-itelemetryprocessor"></a>Filtrage : ITelemetryProcessor

Cette méthode donne un contrôle plus direct sur ce qui est inclus ou exclu à partir du flux de télémétrie. Vous pouvez l’utiliser conjointement avec échantillonnages, ou séparément.

Pour filtrer télémétrie, vous écrivez un traitement de télémétrie et enregistrez avec le Kit de développement. Tous les télémétrie parcourt votre processeur, et vous pouvez choisir de le supprimer à partir du flux, ou ajouter des propriétés. Cela inclut télémétrie à partir des modules standards tels que le collecteur de demande HTTP et que la dépendance collecteur, ainsi que vous avez écrit vous-même de télémétrie. Vous pouvez, par exemple, éliminer télémétrie sur les requêtes émanant robots, ni d’appels de dépendance réussie. 

> [AZURE.WARNING] Filtrage la télémétrie envoyé à partir du Kit de développement à l’aide de processeurs pouvez incliner les statistiques que vous voyez dans le portail et rendent difficile à suivre les éléments associés.
> 
> À la place, envisagez d’utiliser [échantillonnages](app-insights-sampling.md).

### <a name="create-a-telemetry-processor"></a>Créer un traitement de télémétrie

1. Vérifiez que l’Application Insights SDK dans votre projet est version 2.0.0 ou version ultérieure. Avec le bouton droit de votre projet dans l’Explorateur de Visual Studio, puis sélectionnez Manage NuGet Packages. Dans le Gestionnaire de package NuGet, vérifiez Microsoft.ApplicationInsights.Web.

1. Pour créer un filtre, mettre en œuvre ITelemetryProcessor. Il s’agit d’un autre point d’extensibilité comme module de télémétrie, initialiseur de télémétrie et canal de télémétrie. 

    Notez que processeurs de télémétrie construire une chaîne de traitement. Lorsque vous instanciez un traitement de télémétrie, vous passez un lien vers le processeur suivant dans la chaîne. Lorsqu’un point de données de télémétrie est passé à la méthode de processus, il fait son travail, puis appelle le processeur de télémétrie suivante dans la chaîne.

    ``` C#

    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
      {
        private ITelemetryProcessor Next { get; set; }

        // You can pass values from .config
        public string MyParamFromConfigFile { get; set; }

        // Link processors to each other in a chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }
        public void Process(ITelemetry item)
        {
            // To filter out an item, just return 
            if (!OKtoSend(item)) { return; }
            // Modify the item if required 
            ModifyItem(item);

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }

        // Example: replace with your own modifiers.
        private void ModifyItem (ITelemetry item)
        {
            item.Context.Properties.Add("app-version", "1." + MyParamFromConfigFile);
        }
    }
    

    ```
2. Insérez ceci dans ApplicationInsights.config : 

```XML

    <TelemetryProcessors>
      <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
         <!-- Set public property -->
         <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
      </Add>
    </TelemetryProcessors>

```

(Il s’agit de la même section où vous initialisez un filtre d’échantillonnage.)

Vous pouvez passer des valeurs de chaîne à partir du fichier .config en fournissant des propriétés nommées publiques dans votre cours. 

> [AZURE.WARNING] Veillez à faire correspondre le nom du type et les noms des propriétés dans le fichier .config aux noms de classe et propriétés dans le code. Si le fichier .config fait référence à un type d’inexistante ou la propriété, le Kit de développement peut échouer en mode silencieux envoyer tout télémétrie.

 
**Par ailleurs,** vous pouvez initialisation le filtre dans le code. Dans un cours de l’initialisation approprié - par exemple AppStart dans Global.asax.cs - insérer votre processeur dans la chaîne :

```C#

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    builder.Use((next) => new SuccessfulDependencyFilter(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

TelemetryClients créés après cette étape va utiliser vos processeurs.

### <a name="example-filters"></a>Exemple de filtre

#### <a name="synthetic-requests"></a>Demandes de synthèse

Filtrer les tests robots et sur le web. Bien que les indicateurs Explorer vous donne la possibilité pour filtrer les sources de synthèse, cette option permet de réduire le trafic en filtrant les au Kit de développement.

``` C#

    public void Process(ITelemetry item)
    {
      if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

      // Send everything else: 
      this.Next.Process(item);
    }

```

#### <a name="failed-authentication"></a>Échec d’authentification

Filtrer les requêtes avec une réponse « 401 ». 

```C#

public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, just terminate the chain: 
        return;
    }
    // Send everything else: 
    this.Next.Process(item);
}

```

#### <a name="filter-out-fast-remote-dependency-calls"></a>Filtrer les appels dépendance rapidement à distance

Si vous voulez uniquement diagnostiquer les appels sont lentes, filtrer celles rapide. 

> [AZURE.NOTE] Cela sera incliner les statistiques que vous voyez dans le portail. Le graphique de dépendance se présentera comme si les dépendance les appels sont tous les échecs.

``` C#

public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;
            
    if (request != null && request.Duration.Milliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}

```

#### <a name="diagnose-dependency-issues"></a>Diagnostiquer les problèmes de dépendance

[Ce billet de blog](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) décrit un projet pour diagnostiquer les problèmes de dépendance en envoyant automatiquement ping normales aux dépendances.


<a name="add-properties"></a>
## <a name="add-properties-itelemetryinitializer"></a>Ajouter des propriétés : ITelemetryInitializer

Initialiseurs de télémétrie permet de définir les propriétés générales qui sont envoyées avec toutes les télémétrie ; et remplacement sélectionné comportement des modules télémétrie standard. 

Par exemple, l’analyse des applications package Web collecte télémétrie relatives aux demandes HTTP. Par défaut, il signale échec toute demande avec un code de réponse > = 400. Mais si vous souhaitez traiter 400 comme un succès, vous pouvez fournir un initialiseur de télémétrie qui définit la propriété Success.

Si vous fournissez un initialiseur de télémétrie, elle est appelée chaque fois qu’une des méthodes Track*() est appelée. Cela inclut les méthodes appelées par les modules de télémétrie standard. Par convention, ces modules ne définissez pas de n’importe quelle propriété qui a déjà été définie par un initialiseur. 

**Définir votre initialiseur**

*C#*

```C#

    using System;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MvcWebRole.Telemetry
    {
      /*
       * Custom TelemetryInitializer that overrides the default SDK 
       * behavior of treating response codes >= 400 as failed requests
       * 
       */
      public class MyTelemetryInitializer : ITelemetryInitializer
      {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as RequestTelemetry;
            // Is this a TrackRequest() ?
            if (requestTelemetry == null) return;
            int code;
            bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
            if (!parsed) return;
            if (code >= 400 && code < 500)
            {
                // If we set the Success property, the SDK won't change it:
                requestTelemetry.Success = true;
                // Allow us to filter these requests in the portal:
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }
            // else leave the SDK to set the Success property      
        }
      }
    }
```

**Charger votre initialiseur**

Dans ApplicationInsights.config :

    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/> 
        ...
      </TelemetryInitializers>
    </ApplicationInsights>

*Par ailleurs,* vous pouvez instanciation l’initialiseur dans le code, par exemple dans Global.aspx.cs :


```C#
    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


[Afficher une plus grande de cet exemple.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

<a name="js-initializer"></a>
### <a name="javascript-telemetry-initializers"></a>Initialiseurs de télémétrie JavaScript

*JavaScript*

Insérer un initialiseur de télémétrie immédiatement après le code d’initialisation que vous avez obtenu à partir du portail : 

```JS

    <script type="text/javascript">
        // ... initialization code
        ...({
            instrumentationKey: "your instrumentation key"
        });
        window.appInsights = appInsights;


        // Adding telemetry initializer.
        // This is called whenever a new telemetry item
        // is created.

        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;

                // To check the telemetry item’s type - for example PageView:
                if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                    // this statement removes url from all page view documents
                    telemetryItem.url = "URL CENSORED";
                }

                // To set custom properties:
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["globalProperty"] = "boo";

                // To set custom metrics:
                telemetryItem.measurements = telemetryItem.measurements || {};
                telemetryItem.measurements["globalMetric"] = 100;
            });
        });

        // End of inserted code.

        appInsights.trackPageView();
    </script>
```

Pour consulter un résumé des propriétés personnalisées non disponibles sur le telemetryItem, consultez le [modèle de données](app-insights-export-data-model.md#lttelemetrytypegt).

Vous pouvez ajouter autant d’initialiseurs que vous le souhaitez. 


## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor et ITelemetryInitializer

Quelle est la différence entre les processeurs de télémétrie et initialiseurs de télémétrie ?

* Il existe certaines chevauche dans ce que vous pouvez faire avec eux : à la fois peuvent servir à ajouter des propriétés aux télémétrie.
* TelemetryInitializers toujours exécutées avant TelemetryProcessors.
* TelemetryProcessors vous permettent complètement remplacer ou supprimer un élément de télémétrie.
* TelemetryProcessors ne pas traiter télémétrie compteur de performances.



## <a name="persistence-channel"></a>Persistance canal 

Si votre application s’exécute à l’endroit où la connexion internet n’est pas toujours disponible ou lente, envisagez d’utiliser le canal persistance au lieu du canal en mémoire par défaut. 

Le canal en mémoire par défaut perd toute télémétrie n’a pas été envoyé au moment où l’application se ferme. Bien que vous puissiez utiliser `Flush()` pour tenter d’envoyer des données restant dans la mémoire tampon, il toujours perd données s’il n’existe aucune connexion internet, ou si l’application s’arrête avant transmission est terminée.

En revanche, le canal persistance tampons de télémétrie dans un fichier, avant d’envoyer au portail. `Flush()`garantit que les données sont stockées dans le fichier. Si aucune donnée n’est pas envoyée au moment où l’application se ferme, il est conservé dans le fichier. Lors du redémarrage de l’application, les données sont envoyées puis, s’il existe une connexion internet. Données sont additionnés dans le fichier ainsi que la durée est nécessaire jusqu'à ce qu’une connexion est disponible. 

### <a name="to-use-the-persistence-channel"></a>Pour utiliser le canal persistance

1. Importer le package NuGet [Microsoft.ApplicationInsights.PersistenceChannel](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PersistenceChannel/1.2.3).
2. Inclure ce code dans votre application, dans un emplacement de l’initialisation appropriés :
 
    ```C# 

      using Microsoft.ApplicationInsights.Channel;
      using Microsoft.ApplicationInsights.Extensibility;
      ...

      // Set up 
      TelemetryConfiguration.Active.InstrumentationKey = "YOUR INSTRUMENTATION KEY";
 
      TelemetryConfiguration.Active.TelemetryChannel = new PersistenceChannel();
    
    ``` 
3. Utiliser `telemetryClient.Flush()` avant votre ferme l’application, pour vous assurer que des données sont envoyées au portail ou enregistrées dans le fichier.

    Notez que Flush() est synchrone pour le canal persistance, mais asynchrone pour les autres canaux.

 
Le canal persistance est optimisé pour les appareils scénarios, où le nombre d’événements générés par l’application est relativement faible et la connexion est souvent sources non fiable. Ce canal sera écrire des événements sur le disque dans le stockage fiable tout d’abord, puis essayez pour l’envoyer. 

#### <a name="example"></a>Exemple

Supposons que vous voulez analyser les exceptions non gérées. Vous vous abonnez à la `UnhandledException` événement. Dans le rappel, vous incluez un appel à vidage pour vous assurer que la télémétrie est conservée.
 
```C# 

AppDomain.CurrentDomain.UnhandledException += CurrentDomain_UnhandledException; 
 
... 
 
private void CurrentDomain_UnhandledException(object sender, UnhandledExceptionEventArgs e) 
{ 
    ExceptionTelemetry excTelemetry = new ExceptionTelemetry((Exception)e.ExceptionObject); 
    excTelemetry.SeverityLevel = SeverityLevel.Critical; 
    excTelemetry.HandledAt = ExceptionHandledAt.Unhandled; 
 
    telemetryClient.TrackException(excTelemetry); 
 
    telemetryClient.Flush(); 
} 

``` 

Lorsque l’application s’arrête, vous verrez un fichier dans `%LocalAppData%\Microsoft\ApplicationInsights\`, qui contient les événements compressés. 
 
Prochain que démarrage de cette application, le canal décrochez ce fichier et livrer télémétrie l’analyse des applications le cas échéant.

#### <a name="test-example"></a>Exemple de test

```C#

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            // Send data from the last time the app ran
            System.Threading.Thread.Sleep(5 * 1000);

            // Set up persistence channel

            TelemetryConfiguration.Active.InstrumentationKey = "YOUR KEY";
            TelemetryConfiguration.Active.TelemetryChannel = new PersistenceChannel();

            // Send some data

            var telemetry = new TelemetryClient();

            for (var i = 0; i < 100; i++)
            {
                var e1 = new Microsoft.ApplicationInsights.DataContracts.EventTelemetry("persistenceTest");
                e1.Properties["i"] = "" + i;
                telemetry.TrackEvent(e1);
            }

            // Make sure it's persisted before we close
            telemetry.Flush();
        }
    }
}

```


Le code du canal persistance est [github](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/v1.2.3/src/TelemetryChannels/PersistenceChannel). 


## <a name="reference-docs"></a>Documents de référence

* [Vue d’ensemble de l’API](app-insights-api-custom-events-metrics.md)

* [Référence ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)


## <a name="sdk-code"></a>Kit de développement logiciel Code

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [Kit de développement logiciel JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)


## <a name="next"></a>Étapes suivantes


* [Rechercher des événements et des journaux][diagnostic]
* [Échantillonnages](app-insights-sampling.md)
* [Résolution des problèmes][qna]


<!--Link references-->

[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[create]: app-insights-create-new-resource.md
[data]: app-insights-data-retention-privacy.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

 
