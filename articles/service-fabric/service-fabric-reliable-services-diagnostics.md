<properties
   pageTitle="Diagnostics des Services fiables avec état | Microsoft Azure"
   description="Fonctionnalités de diagnostic pour avec état des Services fiables"
   services="service-fabric"
   documentationCenter=".net"
   authors="AlanWarwick"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/17/2016"
   ms.author="alanwar"/>

# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Fonctionnalités de diagnostic pour avec état des Services fiables
La classe avec état fiable Services StatefulServiceBase émet des événements de [source d’événement](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) qui peuvent être utilisés pour déboguer le service, fournissent des informations sur la façon dont le runtime fonctionne et aider à résoudre les problèmes.

## <a name="eventsource-events"></a>Événements de la source d’événement
Le nom de la source d’événement pour la classe StatefulServiceBase Services fiable avec état est « Microsoft-ServiceFabric-Services ». Événements appartenant à cette source d’événements apparaisse dans la fenêtre [Diagnostics événements](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) lorsque le service est en cours [de débogage dans Visual Studio](service-fabric-debugging-your-application.md).

Exemples d’outils et de technologies d’assistance qui vous aident dans collecte et/ou l’affichage des événements de source d’événement sont [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), de [Microsoft Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md)et de la [Bibliothèque TraceEvent Microsoft](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>Événements

|Nom de l’événement|ID de l’événement|Niveau|Description de l’événement|
|----------|--------|-----|-----------------|
|StatefulRunAsyncInvocation|1|D’information|Émis lors de la tâche RunAsync service est démarrée|
|StatefulRunAsyncCancellation|2|D’information|Émis lors de la tâche RunAsync service a été annulée.|
|StatefulRunAsyncCompletion|3|D’information|Émis lorsque le service RunAsync est terminée|
|StatefulRunAsyncSlowCancellation|4|Avertissement|Émis lors de la tâche RunAsync service prend beaucoup de temps pour terminer l’annulation|
|StatefulRunAsyncFailure|5|Erreur|Émis lorsque le service RunAsync tâche lève une exception|

## <a name="interpret-events"></a>Interpréter les événements

Événements StatefulRunAsyncInvocation, StatefulRunAsyncCompletion et StatefulRunAsyncCancellation sont utiles pour l’enregistreur du service pour comprendre le cycle de vie d’un service, ainsi que le minutage lorsqu’un service est démarré, annulé ou terminé. Cela peut être utile quand débogage des problèmes de service ou pour comprendre le cycle de vie du service.

Les auteurs de service doivent faites attention aux événements StatefulRunAsyncSlowCancellation et StatefulRunAsyncFailure car ils indiquent les problèmes avec le service.

StatefulRunAsyncFailure est émis chaque fois que la tâche RunAsync() service lève une exception. En règle générale, une exception indique une erreur ou les bogues dans le service. En outre, l’exception provoque l’échec du service pour que celui-ci est déplacé vers un autre nœud. Cela peut s’avérer coûteux et peut retarder demandes entrantes tandis que le service est déplacé. Rédacteurs du service doivent déterminer l’origine de l’exception et, si possible, atténuer.

StatefulRunAsyncSlowCancellation est émise chaque fois qu’une demande d’annulation de la tâche RunAsync prend plue de quatre secondes. Lorsqu’un service prend beaucoup de temps pour terminer l’annulation, il affecte la possibilité de service être rapidement redémarré sur un autre nœud. Cela peut avoir un impact sur la disponibilité générale du service.
