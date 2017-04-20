<properties 
    pageTitle="Configuration d’Application Insights SDK avec ApplicationInsights.config ou .xml" 
    description="Activer ou désactiver les modules de collecte de données et ajouter des compteurs de performances et d’autres paramètres" 
    services="application-insights"
    documentationCenter="" 
    authors="OlegAnaniev-MSFT"
    editor="alancameronwills" 
    manager="douge"/>
 
<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="03/12/2016" 
    ms.author="awills"/>

# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>Configuration de l’analyse des applications SDK avec ApplicationInsights.config ou .xml

Application Insights .NET SDK est constitué d’un nombre de packages NuGet. Le [package de base](http://www.nuget.org/packages/Microsoft.ApplicationInsights) fournit l’API pour l’envoi de télémétrie l’analyse de l’Application. [Packages supplémentaires](http://www.nuget.org/packages?q=Microsoft.ApplicationInsights) fournit de télémétrie _modules_ et _initialiseurs_ pour le suivi automatiquement télémétrie à partir de votre application et son contexte. En ajustant le fichier de configuration, vous pouvez activer ou désactiver les initialiseurs et les modules de télémétrie et choisir les paramètres pour certains d'entre eux.

Le fichier de configuration est nommé `ApplicationInsights.config` ou `ApplicationInsights.xml`, en fonction du type de votre application. Il est ajouté automatiquement à votre projet lorsque vous [installez la plupart des versions du Kit de développement][start]. Il est également ajouté à une application web par [l’Analyseur de statut sur un serveur IIS][redfield], ou lorsque vous sélectionnez l' analyse des Appplication [extension d’un site Web Azure ou machine virtuelle](app-insights-azure-web-apps.md).

Il n’est pas un fichier équivalent pour contrôler le [Kit de développement dans une page web][client].

Ce document décrit les sections que vous consultez dans la configuration du fichier, comment ils contrôlent les composants du Kit de développement, et quels packages NuGet chargement ces composants.

## <a name="telemetry-modules-aspnet"></a>Modules de télémétrie (ASP.NET)

Chaque module de télémétrie collecte de données d’un type spécifique et utilise l’API de base pour envoyer les données. Les modules sont installés par les différents packages NuGet, qui comprend également les lignes requises pour le fichier .config.

Il existe un nœud dans le fichier de configuration pour chaque module. Pour désactiver un module, supprimez le nœud ou transformer en commentaire.



### <a name="dependency-tracking"></a>Suivi des dépendances

[Suivi des dépendances](app-insights-dependencies.md) collecte télémétrie concernant appels votre application à des bases de données et des services externes et des bases de données. Pour permettre à ce module à travailler dans un serveur IIS, vous devez [installer moniteur d’état][redfield]. Pour l’utiliser dans les applications web Azure ou machines virtuelles, [Sélectionnez l’extension Application perspectives](app-insights-azure-web-apps.md).

Vous pouvez également écrire votre propre dépendance le suivi du code à l’aide de l' [API TrackDependency](app-insights-api-custom-events-metrics.md#track-dependency).


* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* Package NuGet [Microsoft.ApplicationInsights.DependencyCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) .

### <a name="performance-collector"></a>Collecteur de performance

[Collecte des compteurs de performance système](app-insights-performance-counters.md) tels que processeur, mémoire et réseau charger à partir d’installations IIS. Vous pouvez spécifier des compteurs à collecter, y compris des compteurs de performance que vous avez configuré vous-même.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* Package NuGet [Microsoft.ApplicationInsights.PerfCounterCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) .


### <a name="application-insights-diagnostics-telemetry"></a>Application Insights Diagnostics télémétrie

La `DiagnosticsTelemetryModule` signale les erreurs dans le code d’instrumentation Insights Application proprement dit. Par exemple, si le code ne peut pas accéder à des compteurs de performance ou si un `ITelemetryInitializer` une exception. Télémétrie trace suivie par ce module apparaît dans la [Recherche de Diagnostic][diagnostic]. Envoie des données de diagnostic pour dc.services.vsallin.net.
 
* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* Package NuGet [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) . Si vous installez uniquement ce package, le fichier ApplicationInsights.config n’est pas automatiquement créé. 

### <a name="developer-mode"></a>Mode développeur

`DeveloperModeWithDebuggerAttachedTelemetryModule`force la création d’analyse de l’Application `TelemetryChannel` pour envoyer des données immédiatement, élément d’une télémétrie à la fois, quand un débogueur est joint au processus d’application. Cela réduit la durée entre le moment lorsque votre application effectue le suivi de télémétrie et lorsqu’il apparaît dans le portail d’analyse de l’Application. Il génère encombrement dans processeur et bande passante.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Application Insights Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) Package NuGet

### <a name="web-request-tracking"></a>Suivi des demandes Web

Indique le [code de temps et résultat réponse](app-insights-asp-net.md) de requêtes HTTP. 

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* Package NuGet [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)

### <a name="exception-tracking"></a>Exceptions de suivi

`ExceptionTrackingTelemetryModule`pistes exceptions non gérées dans votre application web. Voir les [échecs et les exceptions][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* Package NuGet [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)


* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule`-effectue le suivi des [exceptions de tâche non prise en charge](http://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx).
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule`-effectue le suivi des exceptions non prise en charge pour les rôles de travail, windows services et applications console.
* [Application Insights Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) Package NuGet.

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights

Le package Microsoft.ApplicationInsights fournit l' [API principale](https://msdn.microsoft.com/library/mt420197.aspx) du Kit de développement. Les autres modules de télémétrie utilisent cette option, et vous pouvez également [l’utiliser pour définir votre propre télémétrie](app-insights-api-custom-events-metrics.md).

* Aucune entrée dans ApplicationInsights.config.
* Package NuGet [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) . Si vous installez uniquement cette NuGet, aucun fichier .config est généré.

## <a name="telemetry-channel"></a>Canal de télémétrie

Le canal de télémétrie gère la mémoire tampon et transmission de télémétrie au service Application perspectives. 

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel`est le canal par défaut pour les services. Il place en mémoire tampon des données.
* `Microsoft.ApplicationInsights.PersistenceChannel`est une alternative pour les applications console. Il peut enregistrer des données vidées de façon permanente lorsque votre application se ferme vers le bas et vous envoie au démarrage de l’application à nouveau.


## <a name="telemetry-initializers-aspnet"></a>Initialiseurs de télémétrie (ASP.NET)

Initialiseurs de télémétrie définir les propriétés de contexte qui sont envoyées en même temps que chaque élément de télémétrie. 

Vous pouvez [écrire votre propre initialiseurs](app-insights-api-filtering-sampling.md#add-properties) pour définir les propriétés de contexte.

Les initialiseurs standards se voient attribuer l’aide du Web ou WindowsServer NuGet packages :


* `AccountIdTelemetryInitializer`définit la propriété ID de compte.
* `AuthenticatedUserIdTelemetryInitializer`définit la propriété AuthenticatedUserId tel que défini par le JavaScript SDK.
* `AzureRoleEnvironmentTelemetryInitializer`mises à jour la `RoleName` et `RoleInstance` propriétés de la `Device` contexte pour tous les éléments de télémétrie avec informations extraites à partir de l’environnement d’exécution Azure.
* `BuildInfoConfigComponentVersionTelemetryInitializer`mises à jour la `Version` propriété de la `Component` contexte pour tous les éléments de télémétrie avec la valeur extraite de le `BuildInfo.config` fichier obtenus par MS Build.
* `ClientIpHeaderTelemetryInitializer`mises à jour `Ip` propriété de la `Location` contexte de tous les éléments de télémétrie basé sur le `X-Forwarded-For` en-tête HTTP de la requête.
* `DeviceTelemetryInitializer`met à jour les propriétés suivantes de la `Device` contexte pour tous les éléments de télémétrie.
 - `Type`est défini sur « PC »
 - `Id`est défini sur le nom de domaine de l’ordinateur où l’application web est en cours d’exécution.
 - `OemName`est défini sur la valeur extraite de le `Win32_ComputerSystem.Manufacturer` de champ à l’aide de WMI.
 - `Model`est défini sur la valeur extraite de le `Win32_ComputerSystem.Model` de champ à l’aide de WMI.
 - `NetworkType`est défini sur la valeur extraite de le `NetworkInterface`.
 - `Language`définir le nom de la `CurrentCulture`.
* `DomainNameRoleInstanceTelemetryInitializer`mises à jour la `RoleInstance` propriétés de la `Device` contexte pour tous les éléments de télémétrie avec le nom de domaine de l’ordinateur sur lequel s’exécute l’application web.
* `OperationNameTelemetryInitializer`mises à jour la `Name` propriété de la `RequestTelemetry` et la `Name` propriété de la `Operation` contexte de tous les éléments de télémétrie en fonction de la méthode HTTP, ainsi que les noms de contrôleur MVC ASP.NET et une action appelée pour traiter la demande.
* `OperationIdTelemetryInitializer`ou `OperationCorrelationTelemetryInitializer` mises à jour la `Operation.Id` propriété de contexte de tous les éléments de télémétrie suivies lors du traitement d’une demande de généré automatiquement par `RequestTelemetry.Id`.
* `SessionTelemetryInitializer`mises à jour la `Id` propriété de la `Session` contexte pour tous les éléments de télémétrie avec valeur extraite de le `ai_session` cookies généré par le code d’instrumentation ApplicationInsights JavaScript en cours d’exécution dans le navigateur de l’utilisateur. 
* `SyntheticTelemetryInitializer`ou `SyntheticUserAgentTelemetryInitializer` mises à jour la `User`, `Session` et `Operation` propriétés contextes de tous les éléments de télémétrie suivies lors de la gestion une demande à partir d’une source de synthèse, telles que disponibilité tester ou robots des moteurs de recherche. Par défaut, [Indicateurs Explorer](app-insights-metrics-explorer.md) n’affiche pas de télémétrie synthétique. 

    La `<Filters>` définir l’identification des propriétés des requêtes.
* `UserAgentTelemetryInitializer`mises à jour la `UserAgent` propriété de la `User` contexte de tous les éléments de télémétrie basé sur le `User-Agent` en-tête HTTP de la requête.
* `UserTelemetryInitializer`mises à jour la `Id` et `AcquisitionDate` propriétés de `User` contexte pour tous les éléments de télémétrie avec des valeurs extraites la `ai_user` cookies généré par le code d’instrumentation Application Insights JavaScript en cours d’exécution dans le navigateur de l’utilisateur.
* `WebTestTelemetryInitializer`définit l’id utilisateur, les id de session et les propriétés de la source synthétique pour les demandes HTTP qui proviennent [les tests de disponibilité](app-insights-monitor-web-app-availability.md).
La `<Filters>` définir l’identification des propriétés des requêtes.

## <a name="telemetry-processors-aspnet"></a>Processeurs de télémétrie (ASP.NET)

Processeurs de télémétrie peuvent de filtrer et de modifier chaque élément de télémétrie juste avant qu’il est envoyé à partir du Kit de développement au portail.

Vous pouvez [écrire vos propres processeurs de télémétrie](app-insights-api-filtering-sampling.md#filtering).


#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>Processeur de télémétrie d’échantillonnage adapté (à partir de 2.0.0-beta3)

Ceci est activée par défaut. Si votre application envoie un grand nombre de télémétrie, ce processeur supprime partie.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

Le paramètre fournit la cible l’algorithme s’efforce d’atteindre. Chaque instance du Kit de développement fonctionne séparément, afin que si votre serveur est un cluster de plusieurs ordinateurs, le volume réel de télémétrie sera multiplié en conséquence.

[En savoir plus sur les échantillonnages](app-insights-sampling.md).



#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>Processeur de télémétrie d’échantillonnage de taux fixe (à partir de 2.0.0-beta1)

Il existe également un [processeur de télémétrie d’échantillonnage](app-insights-api-filtering-sampling.md#sampling) standard (à partir de 2.0.1) :

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```



## <a name="channel-parameters-java"></a>Paramètres du canal (Java)

Ces paramètres affectent la manière le Kit de développement Java doit stocker et vider les données de télémétrie qu’il collecte.

#### <a name="maxtelemetrybuffercapacity"></a>MaxTelemetryBufferCapacity

Le nombre d’éléments de télémétrie pouvant être stockés dans un stockage en mémoire du Kit de développement. Lorsque ce nombre est atteint, la mémoire tampon de télémétrie est vidée - autrement dit, les éléments de télémétrie sont envoyés au serveur Application perspectives.

-   Min : 1
-   Max : 1000
-   Par défaut : 500

```

  <ApplicationInsights>
      ...
      <Channel>
       <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity>
      </Channel>
      ...
  </ApplicationInsights>
```

#### <a name="flushintervalinseconds"></a>FlushIntervalInSeconds 

Détermine la fréquence à laquelle les données sont stockées dans le stockage en mémoire doivent être vidées (envoyés analyse des applications).

-   Min : 1
-   Max : 300
-   Par défaut : 5

```

    <ApplicationInsights>
      ...
      <Channel>
        <FlushIntervalInSeconds>100</FlushIntervalInSeconds>
      </Channel>
      ...
    </ApplicationInsights>
```

#### <a name="maxtransmissionstoragecapacityinmb"></a>MaxTransmissionStorageCapacityInMB

Détermine la taille maximale en Mo alloué au stockage permanent sur le disque local. Ce stockage est utilisé pour conserver les éléments de télémétrie n’a pas pu être transmises au point de terminaison Insights Application. Lorsque la taille de stockage a été rencontrée, nouveaux éléments de télémétrie seront ignorés.

-   Min : 1
-   Max : 100
-   Par défaut : 10

```

   <ApplicationInsights>
      ...
      <Channel>
        <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB>
      </Channel>
      ...
   </ApplicationInsights>
```



## <a name="instrumentationkey"></a>InstrumentationKey

Ceci permet de déterminer la ressource Application Insights dans laquelle vos données apparaissent. En général, vous créez une ressource distincte, avec une clé distincte pour chacun de vos applications.

Si vous souhaitez définir la clé de façon dynamique, par exemple si vous souhaitez envoyer les résultats à partir de votre application à différentes ressources - vous pouvez omettre la clé du fichier de configuration et définir dans le code.

Pour définir la clé pour toutes les instances de TelemetryClient, y compris les modules de télémétrie standard, définissez la clé dans TelemetryConfiguration.Active. Procédez comme suit dans une méthode d’initialisation, par exemple global.aspx.cs dans un service ASP.NET :

```C#

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      //...
```

Si vous voulez juste envoyer un ensemble spécifique d’événements à une autre ressource, vous pouvez définir la clé pour un TelemetryClient spécifique :

```C#

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

[En savoir plus sur l’API][api].

Pour obtenir une nouvelle clé, [créez une nouvelle ressource dans le portail d’Application Insights][new].

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[netlogs]: app-insights-asp-net-trace-logs.md
[new]: app-insights-create-new-resource.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md

