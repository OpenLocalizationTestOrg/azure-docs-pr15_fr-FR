<properties
    pageTitle="Vue d’ensemble des Diagnostics de Windows Azure | Microsoft Azure"
    description="Utiliser les diagnostics de Windows Azure pour débogage, mesurer les performances, surveillance, l’analyse du trafic dans les services en nuage, machines virtuelles et tissu de service"
    services="multiple"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/02/2016"
    ms.author="robb"/>


# <a name="what-is-microsoft-azure-diagnostics"></a>Nouveautés de Microsoft Azure Diagnostics


Diagnostics de Windows Azure est la capacité dans Azure qui active la collecte de données de diagnostic sur une application déployée. Vous pouvez utiliser l’extension diagnostics à partir d’un nombre de sources différentes. Prise en charge actuellement sont Azure Cloud Service Web et les rôles de travail, Machines virtuelles Azure exécutant Microsoft Windows et tissu de Service. D’autres services Azure avoir leur propre diagnostics distinct.

## <a name="data-you-can-collect"></a>Vous pouvez collecter des données

Diagnostics de Windows Azure recueille des types de données suivants :

Source de données|Description
---|---
Compteurs de performance | Système d’exploitation et compteurs de performance personnalisés
Journaux d’application     | Suivi des messages rédigés par votre application
Journaux d’événements Windows   | Informations envoyées au système de journalisation des événements de Windows
Source de l’événement .NET    | Code écrire des événements à l’aide de la classe .NET [source d’événement](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx)
Journaux IIS             | Informations sur les sites web IIS
Manifeste basé ETW   | Événements de suivi pour Windows événements générés par n’importe quel processus
Vidage sur incident          | Informations sur l’état du processus en cas d’un blocage d’application
Journaux d’erreur personnalisée    | Journaux créés par votre application ou service
Journaux d’Azure infrastructure de Diagnostic|Informations sur les Diagnostics lui-même

L’extension diagnostics de Windows Azure peut transférer ces données à un compte de stockage Azure ou l’envoyer aux services tels que [Application perspectives](./application-insights/app-insights-cloudservices.md). Vous pouvez utiliser les données de débogage et résolution des problèmes de mesurer les performances, l’utilisation des ressources, l’analyse du trafic et planification de la capacité de surveillance et d’audit.


## <a name="versioning"></a>Contrôle de version
Afficher [l’historique de contrôle de version Diagnostics de Windows Azure](azure-diagnostics-versioning-history.md).

## <a name="next-steps"></a>Étapes suivantes
Sélectionnez le service que vous voulez collecter des diagnostics sur et utilisez les articles suivants pour commencer. Utilisez les liens général diagnostics Azure Reference for des tâches spécifiques.

## <a name="web-apps"></a>Applications Web
Notez que les applications Web n’utilisent pas Azure Diagnostics. Trouver des informations équivalentes au [Web Apps](./app-service-web/web-sites-enable-diagnostic-log.md)

## <a name="cloud-services-using-azure-diagnostics"></a>Services de cloud à l’aide des Diagnostics Azure
- Si vous utilisez Visual Studio, voir [Utiliser Visual Studio pour tracer une application de Services de Cloud](./vs-azure-tools-debug-cloud-services-virtual-machines.md) pour commencer. Dans le cas contraire, voir
- [Comment contrôler les services de Cloud à l’aide des Diagnostics Azure](./cloud-services/cloud-services-how-to-monitor.md)
- [Configurer des Diagnostics Azure dans une Application de Services de Cloud](./cloud-services/cloud-services-dotnet-diagnostics.md)

Pour des rubriques plus avancées, voir

- [À l’aide des Diagnostics de Windows Azure avec des aperçus de l’Application pour les Services en nuage](./application-insights/app-insights-cloudservices.md)
- [Suivre le flux d’une application de Services de Cloud avec Diagnostics Azure](./cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
- [Utiliser PowerShell pour configurer des diagnostics sur les Services en nuage](./virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md)


## <a name="virtual-machines-using-azure-diagnostics"></a>Machines virtuelles à l’aide des Diagnostics Azure
- Si vous utilisez Visual Studio, voir [Utiliser Visual Studio pour la trace Machines virtuelles Azure](./vs-azure-tools-debug-cloud-services-virtual-machines.md) pour commencer. Dans le cas contraire, voir
- [Configurer la Azure Diagnostics sur une Machine virtuelle Azure](./virtual-machines-dotnet-diagnostics.md)

Pour des rubriques plus avancées, voir

- [Utiliser PowerShell pour configurer diagnostics Azure machines virtuelles en fonctionnement](./virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md)
- [Créer une machine virtuelle Windows avec la surveillance et les diagnostics à l’aide du modèle de gestionnaire de ressources Azure](./virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)

## <a name="service-fabric-using-azure-diagnostics"></a>TISSU de service à l’aide des Diagnostics Azure
Prise en main à [surveiller une application de Service TISSU](./service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). De nombreux autres articles diagnostics tissu de Service sont disponibles dans l’arborescence de navigation gauche une fois que vous obtenez à cet article.

## <a name="general-azure-diagnostics-articles"></a>Articles général Azure Diagnostics
- [Configuration du schéma azure Diagnostics](https://msdn.microsoft.com/library/azure/mt634524.aspx) - Découvrez comment modifier le fichier de schéma pour collecter et router les données de diagnostic. Notez que vous pouvez également utiliser Visual Studio pour modifier le fichier de schéma.
- [Comment les Diagnostics Azure données sont stockées dans le stockage Azure](./cloud-services/cloud-services-dotnet-diagnostics-storage.md) - connaître les noms des tables et des objets BLOB l’endroit où les données de diagnostic sont signées.
- Découvrez comment [utiliser des compteurs de Performance dans Azure Diagnostics](./cloud-services/cloud-services-dotnet-diagnostics-performance-counters.md).
- Découvrez comment [les informations de diagnostic itinéraire Azure analyse des applications](./azure-diagnostics-configure-applicationinsights.md)
- Si vous rencontrez des problèmes avec les diagnostics démarrage ou d’une recherche de vos données dans des tables de stockage Azure, voir [Résolution des problèmes de diagnostic Azure](./azure-diagnostics-troubleshooting.md)
