<properties
    pageTitle="Vue d’ensemble de surveillance dans Microsoft Azure | Microsoft Azure"
    description="Présentation au niveau supérieure de surveillance et les diagnostics de Microsoft Azure, y compris les alertes, webhooks, échelle et bien plus encore."
    authors="rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"l
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/11/2016"
    ms.author="robb"/>

# <a name="overview-of-monitoring-in-microsoft-azure"></a>Vue d’ensemble de surveillance dans Microsoft Azure

Cet article fournit une vue d’ensemble conceptuelle de surveillance des ressources Azure. Il fournit des pointeurs vers des informations sur des types de ressources.  Pour obtenir des informations détaillées sur votre application non Azure point de vue de surveillance des mots clés, voir [recommandations pour la surveillance et de diagnostic](../best-practices-monitoring.md).

Applications cloud sont complexes avec nombreux éléments. Surveillance fournit des données pour vous assurer que votre application reste opérationnel et en cours d’exécution dans un état correct. Il permet également de conjurer les problèmes potentiels ou résoudre les problèmes au-delà de celles. En outre, vous pouvez utiliser des données d’analyse pour Familiarisez-vous approfondie relatives à votre application. Ces informations peuvent vous aider à améliorer les performances de l’application ou la facilité de gestion ou automatiser les actions qui requièrent intervention manuelle dans le cas contraire.

Le diagramme suivant montre une vue conceptuelle de surveillance Azure, notamment le type de journaux que vous pouvez collecter et que vous pouvez faire avec ces données.   

![Modèle logique pour la surveillance et de diagnostic pour les ressources non cluster](./media/monitoring-overview/MonitoringAzureResources-non-compute_v3.png)

Figure 1 : Modèle conceptuel de surveillance et de diagnostic pour les ressources non cluster

<br/>

![Modèle logique pour la surveillance et de diagnostic pour les ressources de calcul](./media/monitoring-overview/MonitoringAzureResources-compute_v3.png)

Figure 2 : Modèle conceptuel de surveillance et de diagnostic pour les ressources de calcul


## <a name="monitoring-sources"></a>Sources de surveillance
### <a name="activity-logs"></a>Journaux d’activité
Vous pouvez rechercher le journal d’activité (appelé auparavant opérationnel ou les journaux d’Audit) pour plus d’informations sur la ressource comme indiqué par l’infrastructure Azure. Le journal contient des informations telles que des heures lorsque les ressources sont créées ou destruction.  

### <a name="host-vm"></a>Hôte machine virtuelle
**Calculer uniquement**


Certaines ressources telles que les Services de Cloud, Machines virtuelles, de calcul et tissu de Service ont une machine virtuelle hôte dédié ils interagissent avec. La machine virtuelle hôte est l’équivalent de machine virtuelle racine dans le modèle hyperviseur Hyper-V. Dans ce cas, vous pouvez collecter les mesures sur simplement l’ordinateur hôte virtuel outre le système d’exploitation invité.  

D’autres services Azure, il n’est pas nécessairement un mappage 1:1 entre vos ressources et une machine virtuelle hôte particulier afin que les mesures de machine virtuelle hôte ne sont pas disponibles.


### <a name="resource---metrics-and-diagnostics-logs"></a>Ressource - indicateurs et des journaux des Diagnostics
Métrique collectable varie en fonction du type de ressource. Par exemple, Machines virtuelles fournit des statistiques sur le disque IO et % de l’UC. Mais ces statistiques ne contient pas d’une file d’attente Bus des services, qui fournit à la place des mesures telles que le débit taille et message file d’attente.

Pour les ressources de calcul, vous pouvez obtenir indicateurs sur le système d’exploitation invité et les diagnostics de modules comme Azure Diagnostics. Diagnostics de Windows Azure permet de collecte et route collectent des données de diagnostics vers d’autres emplacements, y compris le stockage Azure.

Une liste des mesures actuellement collectable est disponible à la [prise en charge des indicateurs](monitoring-supported-metrics.md).

### <a name="application---diagnostics-logs-application-logs-and-metrics"></a>Application - journaux des applications, les journaux de Diagnostics et de mesures
**Calculer uniquement**

Applications peuvent s’exécuter sur le système d’exploitation invité dans le modèle de calcul. Ils émettent leur propre jeu de journaux et d’indicateurs.

Incluent des types de mesures

- Compteurs de performance
- Journaux d’application
- Journaux d’événements Windows
- Source de l’événement .NET
- Journaux IIS
- Manifeste basé ETW
- Vidage sur incident
- Journaux d’erreurs client


## <a name="uses-for-monitoring-data"></a>Utilisations des données d’analyse

### <a name="visualize"></a>Visualiser
Visualisation de vos données dans des graphiques et des graphiques d’analyse vous aide à trouver les tendances beaucoup plus rapidement que vous recherchez via les données lui-même.  

Plusieurs méthodes de visualisation sont les suivantes :

- Utiliser le portail Azure
- Données d’itinéraire d’analyse des applications Azure
- Router des données vers Microsoft PowerBI
- Acheminer les données à un outil tiers visualisation à l’aide de diffusion en continu live ou en demandant à l’outil de lire à partir d’une archive de stockage Azure

### <a name="archive"></a>Archive
Données d’analyse sont généralement écrite dans le stockage Azure et conserve jusqu'à ce que vous supprimiez.

Moyens d’utiliser ces données :

- Une fois écrite, vous pouvez avoir d’autres outils au sein ou en dehors d’Azure Lisez-le et traiter.
- Vous téléchargez les données localement pour une archive locale ou modifiez votre stratégie de rétention dans le cloud pour conserver les données pour une période prolongée.  
- Vous laissez les données dans le stockage Azure indéfiniment, bien que vous devez payer pour le stockage Azure en fonction de la quantité de données que vous conservez.
-

### <a name="query"></a>Requête
Vous pouvez utiliser l’API REST de moniteur de Azure, croix commandes Interface de ligne de commande (CLI) plateforme, applets de commande PowerShell ou le Kit de développement .NET pour accéder aux données dans le système ou le stockage Azure

Voici quelques exemples :

-  Obtention de données pour une application de surveillance personnalisée que vous avez écrit
-  Création de requêtes personnalisées et envoyer ces données à une application tierce.

### <a name="route"></a>Acheminer
Vous pouvez diffuser en continu les données d’analyse à d’autres emplacements en temps réel.

Voici quelques exemples :

- Envoyer analyse des applications afin d’utiliser les outils de visualisation il.
- Envoyez aux événements Hubs afin que vous pouvez acheminer les messages vers des outils tiers pour effectuer une analyse en temps réel.

### <a name="automate"></a>Automatiser
Vous pouvez utiliser les données d’analyse pour déclencher des événements ou processus entier pair quelques exemples :

- Utiliser des données à l’échelle cluster instances vers le haut ou vers le bas selon la charge de l’application.
- Envoyer des messages électroniques lorsqu’une métrique dépasse un seuil prédéterminé.
- Appeler une URL web (webhook) pour exécuter une action dans un système en dehors d’Azure
- Démarrer une procédure opérationnelle dans Azure automation pour effectuer une variété de tâches

## <a name="methods-of-use"></a>Méthodes d’utilisation
En règle générale, vous pouvez manipuler suivi des données, le routage et récupération à l’aide d’une des méthodes suivantes. Toutes les méthodes sont disponibles pour tous les types de données ou les actions.

- [Portail Azure](https://portal.azure.com)
- [PowerShell](insights-powershell-samples.md)  
- [Disponibilité sur plusieurs plateformes Interface de ligne de commande (CLI)](insights-cli-samples.md)
- [API REST](https://msdn.microsoft.com/library/dn931943.aspx)
- [KIT DE DÉVELOPPEMENT .NET](https://msdn.microsoft.com/library/dn802153.aspx)

## <a name="azures-monitoring-offerings"></a>Offres de surveillance d’Azure
Azure a offres disponibles pour analyser vos services d’infrastructure vierge de télémétrie d’application. La meilleure stratégie de surveillance combine utilisation des trois pour obtenir un aperçu complète et détaillé l’état de vos services.

- [Moniteur azure](http://aka.ms/azmondocs) – offres visualisation, requête, le routage, d’alerte, échelle et automation sur des données à la fois à partir de l’infrastructure Azure (journaux d’activité) et chaque ressource Azure (les journaux de Diagnostic). Cet article fait partie de la documentation du moniteur Azure. Le nom du moniteur Azure a été publié 25 septembre en Ignite 2016.  Le nom précédent a été « Perspectives Azure ».  
- [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) – fournit détection enrichie et les diagnostics de problèmes au niveau des applications de votre service, bien intégrée en plus des données à partir d’Azure surveillance. C’est la plate-forme de diagnostics par défaut pour l’application de Service Web Apps.  Vous pouvez acheminer des données à partir d’autres services vers celui-ci.  
- Partie de [Journal Analytique](https://azure.microsoft.com/documentation/services/log-analytics/) d' [Opérations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite) – fournit une solution de gestion informatique holistique pour locaux et tiers infrastructure basée sur le cloud (par exemple, AWS) en plus des ressources Azure.  Données à partir du moniteur Azure peuvent être routées directement au journal Analytique afin de voir les mesures et les journaux pour votre environnement entier en un seul endroit.     


## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur

- [Moniteur Azure dans une vidéo à partir d’Ignite 2016](https://myignite.microsoft.com/videos/4977)
- [Prise en main moniteur Azure](monitoring-get-started.md)
- [Diagnostics Azure](../azure-diagnostics.md) si vous essayez de diagnostiquer les problèmes dans votre application de Service Cloud, Machine virtuelle ou tissu de Service.
- [Analyse de l’application](https://azure.microsoft.com/documentation/services/application-insights/) si vous essayez de problèmes de diagnostic dans votre application Service Web app.
- [Résolution des problèmes de stockage Azure](../storage/storage-e2e-troubleshooting.md) lors de l’utilisation de stockage BLOB, des tableaux ou des files d’attente
- [Journal Analytique](https://azure.microsoft.com/documentation/services/log-analytics/) et la [Suite de gestion des opérations](https://www.microsoft.com/cloud-platform/operations-management-suite)
