<properties 
    pageTitle="Notes de publication pour Application perspectives pour Windows" 
    description="Les dernières mises à jour pour le Kit de développement Windows Store." 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>
<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/12/2016" 
    ms.author="joshweb"/>
 
# <a name="release-notes-for-application-insights-sdk-for-windows-phone-and-store"></a>Notes de publication pour avoir un aperçu Application SDK pour Windows Phone et stocker

Le Kit de développement de perspectives Application envoie télémétrie sur votre application live [Application perspectives](https://azure.microsoft.com/services/application-insights/), où vous pouvez analyser son utilisation et les performances.


## <a name="version-111"></a>Version1.1.1

### <a name="windows-sdk"></a>Kit de développement Windows

- Corriger un blocage lors de blocage lors de l’utilisation Silverlight SDK de Windows Phone. Après cette modification, tout blocage qui se trouve au plus tard le ~ 2 secondes après l’appel à WindowsAppInitialier.InitializeAsync(...) est conservé à disque et sera envoyé la prochaine fois que l’application est démarrée. Si un blocage se produit avant ~ 2 secondes après l’appel, il sera ignoré.  
- Définir des dépendances de NuGet vers une version spécifique de Core et Microsoft.ApplicationInsights.PersistenceChannel (v1.2.3).   

### <a name="core-sdk"></a>Kit de développement SDK

- Cœur est géré dans github. Notes de publication futures du Kit de développement Core sont accessibles [dans github](http://github.com/Microsoft/ApplicationInsights-dotnet/releases)

## <a name="version-11"></a>Version 1.1

### <a name="core-sdk"></a>Kit de développement SDK

- Kit de développement logiciel présente désormais un nouveau type de télémétrie ```DependencyTelemetry``` qui contient des informations sur la dépendance appel à partir d’application
- Nouvelle méthode ```TelemetryClient.TrackDependency``` permet d’envoyer des informations sur les appels de dépendance à partir d’application

## <a name="version-100"></a>Version 1.0.0

### <a name="windows-app-sdk"></a>Kit de développement de l’application Windows

- Échec de l’initialisation nouveau pour les applications Windows. Nouvelle `WindowsAppInitializer` classe avec `InitializeAsync()` méthode autorise pour démarrer l’initialisation de la collection de sites SDK. Cette modification autoriser un contrôle plus précis et améliorations des performances d’initialisation application significative sur précédente ApplicationInsights.config technique.
- DeveloperMode est défini n’est plus automatiquement. Pour modifier le comportement DeveloperMode que vous devez spécifier dans le code.
- Package NuGet injecte n’est plus ApplicationInsights.config. Vous recommandons d’utiliser la nouvelle WindowsAppInitializer lorsque vous ajoutez manuellement package NuGet.
- ApplicationInsights.config lit uniquement `<InstrumentationKey>`, tous les autres paramètres sont ignorés dans les préférences pour les paramètres WindowsAppInitializer.
- Store marché sera automatique collectée par le Kit de développement logiciel.
- Nombre important de correctifs et améliorations des performances stabilité.

### <a name="core-sdk"></a>Kit de développement SDK

- Fichier ApplicationInsights.config n’est plus obligatoires. Et pas encore été ajouté par le package NuGet. Configuration peut être entièrement spécifiée dans le code.
- Package NuGet ajoute n’est plus un fichier cibles à votre solution. Cela supprime le paramètre de DeveloperMode automatique au cours d’une version de débogage. DeveloperMode doit être définie manuellement dans le code.

## <a name="version-017"></a>Version 0.17

### <a name="windows-app-sdk"></a>Kit de développement de l’application Windows

- Kit de développement Windows application prend désormais en charge les applications Windows universel créé par rapport à la version d’évaluation technique Windows 10 et avec VS 2015 RC.

### <a name="core-sdk"></a>Kit de développement SDK

- Valeurs par défaut TelemetryClient initialisation avec la InMemoryChannel.
- Nouvelle API ajouté, `TelemetryClient.Flush()`. Cette méthode vidage déclenche un téléchargement blocage immédiat de télémétrie tous les connecté à ce client. Cela permet de déclencher manuellement téléchargement avant l’arrêt du processus.
- Package NuGet ajouté une cible .net 4.5. Cette cible n’a pas de dépendances externes (dépendances supprimées BCL et source d’événement).

## <a name="version-016"></a>Version 0,16 

Aperçu 2015-04-28

- Kit de développement logiciel prend désormais en charge la plateforme de cible DNX pour activer le contrôle des applications [Core .NET framework](http://www.dotnetfoundation.org/NETCore5) .
- Instances de ```TelemetryClient``` ne pas mettre en cache clé Instrumentation plus. Maintenant si clé instrumentation n’a pas été définie dans ```TelemetryClient``` explicitement ```InstrumentationKey``` renvoie la valeur null. Il résout un problème lorsque vous définissez ```TelemetryConfiguration.Active.InstrumentationKey``` après certaines télémétrie collectée déjà, modules de télémétrie comme collecteur de dépendance, web demandes collection de sites et de performances compteurs collecteur de données utilisera nouvelle clé instrumentation.

## <a name="version-015"></a>Version 0,15.

- Nouvelle propriété ```Operation.SyntheticSource``` désormais disponible sur ```TelemetryContext```. Vous pouvez désormais marquer vos éléments de télémétrie comme « pas un réel le trafic des utilisateurs » et spécifier comment ce trafic a été généré. Par exemple en définissant cette propriété, vous pouvez reconnaître le trafic à partir de votre automation de test de charge tester le trafic.
- Logique de canal a été déplacée vers le NuGet séparée appelée Microsoft.ApplicationInsights.PersistenceChannel. Canal par défaut s’appelle désormais InMemoryChannel
- Nouvelle méthode ```TelemetryClient.Flush``` permet de vider synchrone des éléments de télémétrie à partir de la mémoire tampon

## <a name="version-013"></a>Version 0,13

Aucune notes de publication pour les versions antérieures disponibles. 
