<properties
   pageTitle="Résolution des problèmes avec le suivi d’événements | Microsoft Azure"
   description="Les problèmes courants rencontrés lors du déploiement des services sur Microsoft Azure Service tissu."
   services="service-fabric"
   documentationCenter=".net"
   authors="mattrowmsft"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/31/2016"
   ms.author="mattrow"/>


# <a name="troubleshoot-common-issues-when-you-deploy-services-on-azure-service-fabric"></a>Résoudre les problèmes courants lorsque vous déployez services sur tissu de Service Azure

Lorsque vous exécutez services sur votre ordinateur pour les développeurs, il est facile d’utilisation [d’Outils de débogage de Visual Studio](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Pour les clusters à distance, [les rapports d’intégrité](service-fabric-view-entities-aggregated-health.md) sont toujours un bon point de départ. Le plus simple pour accéder à ces rapports sont via PowerShell ou [SFX](service-fabric-visualizing-your-cluster.md). Cet article suppose que vous effectuez le débogage un cluster distant et avez une connaissance de base de l’utilisation de ces outils.

##<a name="application-crash"></a>Blocage d’application
Le « Partition est inférieur à cibler Nb réplica ou instance « état est une bonne indication que votre service est cesse-t-il de fonctionner. Pour déterminer où votre service est cesse-t-il de fonctionner prend un peu plus enquête. Lorsque votre service s’exécute à l’échelle, votre meilleur ami sera un ensemble de votre bien traces.  Nous vous recommandons que vous essayez [d’Azure Diagnostics](service-fabric-diagnostics-how-to-setup-wad.md) pour la collecte de ces traces et à l’aide d’une solution tels que [Recherche élastique](service-fabric-diagnostic-how-to-use-elasticsearch.md) pour l’affichage et de recherche dans les traces.

![SFX Partition santé](./media/service-fabric-diagnostics-troubleshoot-common-scenarios/crashNewApp.png)

###<a name="during-service-or-actor-initialization"></a>Lors de l’initialisation de service ou un acteur
Les exceptions avant que le type de service est initialisé provoque le blocage du processus. Pour ces types de pannes, le journal des événements affiche l’erreur de votre service.
Il s’agit des exceptions les plus courantes pour afficher avant l’initialisation du service.

***System.IO.FileNotFoundException***

Cette erreur est souvent en raison des dépendances d’assembly manquantes. Vérifiez la propriété CopyLocal dans Visual Studio ou le global assembly cache pour le nœud.

***System.Runtime.InteropServices.COMException***
 *en System.Fabric.Interop.NativeRuntime+IFabricRuntime.RegisterStatefulServiceFactory (IntPtr, IFabricStatefulServiceFactory)*
 
 Cela indique que le nom du type service enregistré ne correspond pas le manifeste de service.

[Diagnostics Azure](service-fabric-diagnostics-how-to-setup-wad.md) peut être configuré pour télécharger automatiquement le journal des événements pour tous les nœuds de votre application.

###<a name="runasync-or-onactivateasync"></a>RunAsync() ou OnActivateAsync()
Si le blocage se produit lors de l’initialisation ou en cours d’exécution de votre type de service enregistrés ou un acteur, l’exception sera détectée par tissu de Service Azure. Vous pouvez les afficher à partir des fournisseurs de source d’événement en détail dans la section « Étapes suivantes ».

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les diagnostics existant fourni par tissu de Service :

* [Diagnostics intervenants fiable](service-fabric-reliable-actors-diagnostics.md)
* [Diagnostics Services fiable](service-fabric-reliable-services-diagnostics.md)
