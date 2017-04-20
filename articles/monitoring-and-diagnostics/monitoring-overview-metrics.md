<properties
    pageTitle="Vue d’ensemble des indicateurs dans Microsoft Azure | Microsoft Azure"
    description="Vue d’ensemble des indicateurs et leurs utilisations dans Microsoft Azure"
    authors="kamathashwin"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="ashwink"/>

# <a name="overview-of-metrics-in-microsoft-azure"></a>Vue d’ensemble des indicateurs dans Microsoft Azure 

Cet article décrit les mesures dans Microsoft Azure, leurs avantages et comment commencer à les utiliser.  

## <a name="what-are-metrics"></a>Quelles sont les mesures ?

Azure moniteur vous permet de consommer télémétrie pour visibilité sur les performances et l’état de vos charges de travail sur Azure. Le type de données de télémétrie Azure les plus importantes sont les mesures (également appelés compteurs) émis par des ressources plus Azure. Moniteur Azure fournit plusieurs méthodes pour configurer et utiliser les mesures de surveillance et de dépannage.


## <a name="what-can-you-do-with-metrics"></a>Que pouvez-vous faire avec indicateurs ?

Mesures constituent une source précieuse de télémétrie et vous permettent d’effectuer les opérations suivantes :

- **Suivre les performances** de la ressource (par exemple, un machine virtuelle, un site Web ou une application de logique) par traçage ses mesures dans un graphique de portail et l’épinglage ce graphique à un tableau de bord.
- **Être informé d’un problème** ayant un impact sur les performances de votre ressource lorsqu’une métrique dépasse un certain seuil.
- **Configurer les actions automatique**, par exemple autoscaling une ressource ou une runbook d’activation lorsqu’une métrique dépasse un certain seuil.
- **Effectuer avancées analytique** ou création de rapports sur l’évolution des performances et l’utilisation de vos ressources.
- **Archive** l’historique des performances ou l’intégrité de vos ressources **conformité / d’audit** à des fins.

##  <a name="metric-characteristics"></a>Caractéristiques métriques
Indicateurs ont les caractéristiques suivantes :

- Toutes les mesures ont **fréquence de 1 minute**. Vous recevez une valeur métrique toutes les minutes de la ressource, vous donnant près visibilité en temps réel sur l’état et l’état de la ressource.
- Métriques sont **disponibles - de-prédéfinies sans avoir à participer** ou la configuration des diagnostics supplémentaires.
- Vous pouvez accéder à **30 jours d’historique** pour chaque métrique. Vous pouvez consulter rapidement les tendances récentes et mensuels les performances ou l’état de la ressource.

Vous pouvez :

- Découvrir facilement, access et **Afficher toutes les mesures** via le portail Azure lorsque vous sélectionnez une ressource et les tracez sur un graphique. 
- Configurer une **règle d’alerte qui envoie une notification ou automatique intervient** métriques lorsque la métrique dépasse le seuil que vous avez défini. Échelle automatique est une action automatique spéciale qui vous permet de faire évoluer votre ressource afin de répondre aux demandes entrantes ou charger sur votre site web ou le calcul des ressources. Vous pouvez configurer une règle de paramètre échelle automatique à l’échelle dans/hors basé sur une mesure qui traversent un seuil.
- Métrique **archive** plus longtemps ou les utiliser pour la création de rapports en mode hors connexion. Vous pouvez router votre métriques de stockage BLOB lorsque vous configurez les paramètres de diagnostic pour la ressource.
- Indicateurs de **flux de données** à un concentrateur de l’événement, ce qui vous permet de les déplacer ensuite vers Azure flux Analytique ou des applications personnalisées pour l’analyse de presque en temps réel. Vous pouvez effectuer l’à l’aide des paramètres des Diagnostics.
- **Router** tous les mesures pour OMS journal Analytique à déverrouiller instantanée analytique, recherche et alerte personnalisé sur des données métriques de vos ressources.
- **Consommer** les mesures via les nouvelles API REST de moniteur Azure.
- Indicateurs de **requête** à l’aide de la PowerShell Cmdlets ou API REST de disponibilité sur plusieurs plateformes.

 ![Routage des indicateurs dans le moniteur Azure](./media/monitoring-overview-metrics/MetricsOverview0.png)

## <a name="access-metrics-via-portal"></a>Métrique Access via le portail
Voici un bref aperçu de la création d’un graphique métrique via le portail Azure

### <a name="view-metrics-after-creating-a-resource"></a>Afficher les mesures après la création d’une ressource
1. Portail Azure ouvert
2. Créer un nouveau Service d’application - Site Web.
3. Après avoir créé un site Web, accédez à la carte de vue d’ensemble du site web.
4. Vous pouvez afficher les nouvelles mesures sous forme de mosaïque « Suivi ». Vous pouvez modifier la vignette et sélectionner plusieurs indicateurs

 ![Indicateurs sur une ressource Moniteur d’Azure](./media/monitoring-overview-metrics/MetricsOverview1.png)    

### <a name="access-all-metrics-in-a-single-place"></a>Accéder à toutes les mesures dans un emplacement unique
1. Ouvrez le portail d’Azure 
2. Accédez à l’onglet Moniteur nouveau et sélectionnez l’option métriques son 
3. Vous pouvez sélectionner votre abonnement, groupe de ressources et le nom de la ressource dans la liste déroulante. 
4. Vous pouvez à présent afficher la liste mesures disponibles. 
5. Sélectionnez la mesure vous intéressez et tracez. 
6. Vous pouvez l’épingler au tableau de bord en cliquant sur le code confidentiel dans le coin supérieur droit.

 ![Accéder à toutes les mesures dans un emplacement unique moniteur d’Azure](./media/monitoring-overview-metrics/MetricsOverview2.png) 


>[AZURE.NOTE] Vous pouvez accéder au niveau hôte métriques de machines virtuelles (Gestionnaire de ressources Azure basé) et machine virtuelle échelle jeux sans tout paramétrage de diagnostic supplémentaires. Ces nouvelles mesures au niveau hôte sont disponibles pour Windows et Linux instances. Les mesures ne doivent ne pas être confondus avec les mesures au niveau de système d’exploitation invité que vous avez accès à quand vous activez Azure Diagnostics sur vos ordinateurs virtuels ou VMSS. Pour plus d’informations sur la configuration des Diagnostics Azure, voir [Nouveautés de Microsoft Azure Diagnostics](../azure-diagnostics.md).

## <a name="access-metrics-via-rest-api"></a>Métrique Access via l’API REST
Métrique Azure sont accessibles via Azure moniteur API. Il existe deux API pour vous aider à découverte et accéder aux indicateurs. Utiliser le : 

- [Azure moniteur métrique définitions API REST](https://msdn.microsoft.com/library/mt743621.aspx) pour accéder à la liste des mesures disponibles pour un service.
- [API REST de azure moniteur métriques](https://msdn.microsoft.com/library/mt743622.aspx) pour accéder aux données métriques réelles

>[AZURE.NOTE] Cet article aborde les mesures via [nouvelle API pour les mesures](https://msdn.microsoft.com/library/dn931930.aspx) pour les ressources Azure. La version de l’API pour les nouvelles définitions métriques API est 2016-03-01 et la version pour indicateurs API est 2016-09-01. Les indicateurs et les définitions métriques héritées sont accessibles avec la version de l’api 2014-04-01.

Pour plus détaillées à l’aide de l’API Azure moniteur reste, voir [Procédure pas à pas API REST moniteur Azure](monitoring-rest-api-walkthrough.md).

## <a name="export-options-for-metrics"></a>Options d’exportation pour indicateurs
Vous pouvez accéder à la carte de journaux de Diagnostics sous l’onglet Moniteur et afficher les options d’exportation pour indicateurs. Vous pouvez sélectionner des mesures (et les journaux de diagnostic) pour être routés vers le stockage Blob, Hubs événement ou pour OMS journal Analytique pour utilisations mentionnées précédemment dans cet article. 

 ![Options d’exportation pour indicateurs moniteur d’Azure](./media/monitoring-overview-metrics/MetricsOverview3.png)   

Vous pouvez le configurer via le Gestionnaire de ressources des modèles, [PowerShell](insights-powershell-samples.md), [infrastructure du langage commun](insights-cli-samples.md) ou [API REST](https://msdn.microsoft.com/library/dn931943.aspx). 

## <a name="take-action-on-metrics"></a>Agir sur les indicateurs
Vous pouvez recevoir des notifications ou effectuer des actions automatiques sur données métriques. Vous pouvez configurer des règles d’alerte ou paramètres d’échelle pour le faire.

### <a name="alert-rules"></a>Règles d’alerte
Vous pouvez configurer des règles d’alerte sur indicateurs. Ces règles d’alerte vérifier si une métrique a dépassé un certain seuil et vous avertir par courrier électronique ou déclencher un webhook peut ensuite être utilisées pour exécuter un script personnalisé. Vous pouvez également utiliser la webhook pour configurer les intégrations produit 3e.

 ![Mesures et les règles d’alerte dans le moniteur d’Azure](./media/monitoring-overview-metrics/MetricsOverview4.png)

### <a name="autoscale"></a>Échelle automatique
Certaines ressources Azure prend en charge plusieurs instances s’adapter ou pour gérer vos charges de travail. Échelle automatique s’applique aux Services d’application (applications Web), jeux d’échelle Machine virtuelle (VMSS) et les Services en nuage classique. Vous pouvez configurer des règles d’échelle automatique à l’échelle ou lorsqu’une certaine mesure ayant un impact sur votre charge de travail dépasse un seuil que vous spécifiez. Pour plus d’informations, voir [vue d’ensemble des autoscaling](monitoring-overview-autoscale.md).

 ![Mesures et échelle moniteur d’Azure](./media/monitoring-overview-metrics/MetricsOverview5.png)

## <a name="supported-services-and-metrics"></a>Services pris en charge et indicateurs
Moniteur Azure est une nouvelle infrastructure indicateurs. Il prend en charge les services suivants Azure dans le portail Azure et la nouvelle version de l’API de moniteur Azure :

- Machines virtuelles (Gestionnaire de ressources Azure en fonction)
- Machine virtuelle échelle jeux
- Traitement par lots
- Espace de noms événement concentrateur 
- Espace de noms Bus des services (SKU premium uniquement)
- SQL (version 12)
- Pool élastique SQL
- Sites Web
- Batteries de serveurs Web
- Applications de logique
- Hubs IoT
- Redis Cache
- Mise en réseau : Application passerelles
- Recherche

Vous pouvez afficher une liste de tous les services pris en charge et leurs indicateurs au [Moniteur Azure métriques - mesures pris en charge par type de ressource](monitoring-supported-metrics.md). 


## <a name="next-steps"></a>Étapes suivantes

Consultez les liens dans cet article. En outre, découvrez :  

- à propos [des mesures courantes pour autoscaling](insights-autoscale-common-metrics.md)
- Comment [créer des règles d’alerte](insights-alerts-portal.md)




