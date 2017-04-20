<properties 
   pageTitle="Compteurs de performance Windows et Linux dans journal Analytique | Microsoft Azure"
   description="Compteurs de performance sont collectées par Analytique journal pour analyser les performances sur agents Windows et Linux.  Cet article décrit comment configurer la collecte des compteurs de Performance pour les deux fenêtres et agents Linux, les détails des qu’ils sont stockés dans le référentiel OMS et comment les analyser dans le portail OMS."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="bwren" />

# <a name="windows-and-linux-performance-data-sources-in-log-analytics"></a>Sources de données de performance Windows et Linux dans journal Analytique 

Compteurs de performance dans Windows et Linux fournissent claires sur les performances des composants matériels, systèmes d’exploitation et applications.  Journal Analytique recueille des compteurs de performance à intervalles fréquents pour l’analyse de près en temps réel (NRT) en plus de consolidation des données de performance pour une analyse plus longue terme et création de rapports.

![Compteurs de performance](media/log-analytics-data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Configuration des compteurs de performances

Configurer des compteurs de Performance à partir du [menu de données dans les paramètres du journal Analytique](log-analytics-data-sources.md#configuring-data-sources).

Lorsque vous configurez tout d’abord Windows ou Linux performances compteurs pour un espace de travail OMS, vous disposez de l’option pour créer rapidement plusieurs compteurs courantes.  Ils sont répertoriés avec une case à cocher en regard de chacun.  Assurez-vous que tous les compteurs que vous voulez créer initialement sont activées, puis cliquez sur **Ajouter des compteurs de performance sélectionnés**.

![Configurer les compteurs de performances de Windows](media/log-analytics-data-sources-performance-counters/configure-windows.png)

Suivez cette procédure pour ajouter un nouveau compteur de performance Windows pour recueillir.

1. Tapez le nom du compteur dans la zone de texte dans le format *\counter objet (instance)*.  Lorsque vous commencez à taper, vous sont présentées contenant la liste correspondante des compteurs courants.  Vous pouvez sélectionner un compteur dans la liste ou tapez dans celle de votre choix.  Vous pouvez également renvoyer toutes les instances d’un compteur particulier en spécifiant *objet/compteur*. 
2. Cliquez sur **+** ou appuyez sur **entrée** pour ajouter le compteur à la liste.
3. Lorsque vous ajoutez un compteur, elle utilise la valeur par défaut de 10 secondes pour l' **Intervalle d’échantillonnage**.  Vous pouvez modifier ce sur une valeur supérieure de 1800 secondes (30 minutes) si vous souhaitez réduire les besoins de stockage des données de performance collectées.
4. Lorsque vous avez terminé d’ajouter des compteurs, cliquez sur le bouton **Enregistrer** dans la partie supérieure de l’écran pour enregistrer la configuration.

![Configurer des compteurs de Performance Linux](media/log-analytics-data-sources-performance-counters/configure-linux.png)

Suivez cette procédure pour ajouter un nouveau compteur de performance Linux pour recueillir.

1. Par défaut, toutes les modifications de configuration sont déplacées automatiquement à tous les agents.  Pour les agents Linux, un fichier de configuration est envoyé au collecteur de données Fluentd.  Si vous souhaitez modifier ce fichier manuellement sur chaque agent Linux, puis décochez la case *appliquer sous configuration à Mes ordinateurs Linux*.
2. Tapez le nom du compteur dans la zone de texte dans le format *\counter objet (instance)*.  Lorsque vous commencez à taper, vous sont présentées contenant la liste correspondante des compteurs courants.  Vous pouvez sélectionner un compteur dans la liste ou tapez dans celle de votre choix.  
2. Cliquez sur **+** ou appuyez sur **entrée** pour ajouter le compteur à la liste des autres compteurs pour l’objet.
3. Tous les compteurs pour un objet utilisent le même **Intervalle d’échantillonnage**.  La valeur par défaut est 10 secondes.  Vous y remédier à une valeur supérieure de 1800 secondes (30 minutes) si vous souhaitez réduire les besoins de stockage des données de performance collectées.
4. Lorsque vous avez terminé d’ajouter des compteurs, cliquez sur le bouton **Enregistrer** dans la partie supérieure de l’écran pour enregistrer la configuration.

## <a name="data-collection"></a>Collecte de données

Journal Analytique collecte tous les performances spécifiées compteurs à leur intervalle d’échantillonnage spécifié sur tous les agents qui ont installé compteur.  Les données ne sont pas regroupées, et les données brutes sont disponibles dans tous les affichages de recherche de journal pendant la durée spécifiée par votre abonnement OMS.


## <a name="performance-record-properties"></a>Propriétés de l’enregistrement performances

Fiches de performance ont un type de **performance** et les propriétés dans le tableau suivant.

| Propriété | Description |
|:--|:--|
| Ordinateur         | Ordinateur sur lequel l’événement a été collectée à partir de. |
| CounterName      | Nom du compteur de performance |
| CounterPath      | Chemin d’accès complet du compteur dans le formulaire \\ \\ \<ordinateur >\\object(instance)\\compteur. |
| Contre-valeurs     | Valeur numérique du compteur.  |
| InstanceName     | Nom de l’instance d’événement.  Vide si aucune instance. |
| Nom_objet       | Nom de l’objet de performance |
| SourceSystem  | Type d’agent de que les données collectées à partir. <br> Se connecter OpsManager – agent Windows, soit directement ou SCOM <br> Linux – tous les agents Linux  <br> AzureStorage – Diagnostics de Windows Azure |
| TimeGenerated       | Date et heure que les données a été échantillonnées. |


## <a name="sizing-estimates"></a>Estimations de redimensionnement

 Une estimation approximative pour la collection de sites d’un compteur particulier à intervalles de 10 secondes est d’environ 1 Mo par jour par instance.  Vous pouvez estimer les besoins de stockage d’un compteur particulier avec la formule suivante.

    1 MB x (number of counters) x (number of agents) x (number of instances)

## <a name="log-searches-with-performance-records"></a>Recherches de journal avec les enregistrements de performances

Le tableau suivant fournit des exemples de recherches journal qui extraient des fiches de Performance.

| Requête | Description |
|:--|:--|
| Type = performance | Toutes les données de Performance |
| Type = ordinateur performance = « Poste de travail » | Toutes les données de Performance à partir d’un ordinateur particulier |
| Type = performance CounterName = « Long. » | Toutes les données de Performance d’un compteur particulier |
| Type = performance (nom_objet = processeur) CounterName = « temps du processeur » InstanceName = _Total & #124 ; mesure Avg(Average) en tant que AVGCPU par ordinateur | Moyenne de l’UC sur tous les ordinateurs |
| Type = performance (CounterName = « temps du processeur ») & #124 ;  mesure max(Max) par ordinateur | Utilisation de l’UC maximale sur tous les ordinateurs |
| Type = performance nom_objet = logique CounterName = ordinateur « Longueur de file d’attente du disque en cours » = « Nom_mon_ordinateur » & #124 ; mesure Avg(Average) par InstanceName | Longueur moyenne de file d’attente du disque actuelle dans toutes les instances d’un ordinateur donné |
| Type = performance CounterName = « DiskTransfers/sec » & #124 ; mesure percentile95(Average) par ordinateur | 95ème centile de transferts disque/s sur tous les ordinateurs |
| Type = performance CounterName = « temps du processeur » InstanceName = « _Total » & #124 ; mesurer avg(CounterValue) par ordinateur intervalle 1 heure | Moyenne horaire de l’UC sur tous les ordinateurs |
| Type = ordinateur performance = « Poste de travail » CounterName = % * InstanceName = _Total & #124 ; mesure percentile70(CounterValue) par intervalle CounterName 1 heure | Centile 70 horaire de chaque compteur pourcentage % sur un ordinateur particulier |
| Type = performance CounterName = « temps du processeur » InstanceName = « _Total » (ordinateur = « Poste de travail ») & #124 ; mesurer min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) par ordinateur intervalle 1 heure | Horaires moyenne, 75 centile, minimale et maximale de l’UC pour un ordinateur spécifique |

## <a name="viewing-performance-data"></a>L’affichage des données de performance

Lorsque vous exécutez une recherche de journal de données de performance, l’affichage du **journal** est affiché par défaut.  Pour afficher les données sous forme graphique, cliquez sur **indicateurs**.  Pour une vue détaillée de graphique, cliquez sur le **+** en regard d’un compteur.  

![Afficher les mesures réduit](media/log-analytics-data-sources-performance-counters/metricscollapsed.png)

Si la plage horaire que vous avez sélectionné est de 6 heures ou moins, le graphique est actualisé après quelques secondes.  Les données réelles sont affiche sur le côté droit du graphique en bleu clair.

![Mode métriques développé avec des données actives](media/log-analytics-data-sources-performance-counters/metricsexpanded.png)

Agréger les données de performance lors d’une recherche de journal, voir [agrégation métrique à la demande et visualisation dans OMS](http://blogs.technet.microsoft.com/msoms/2016/02/26/on-demand-metric-aggregation-and-visualization-in-oms/).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [les recherches de journal](log-analytics-log-searches.md) analyser les données collectées à partir de sources de données et des solutions.  
- Exporter les données collectées à [Power BI](log-analytics-powerbi.md) pour l’analyse et autres visualisations.