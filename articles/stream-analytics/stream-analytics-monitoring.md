<properties 
    pageTitle="Contrôle des travaux compréhension flux Analytique | Microsoft Azure" 
    description="Présentation de contrôle des travaux flux Analytique" 
    keywords="Analyseur de requête"
    services="stream-analytics" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>

# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Comprendre Analytique de flux de travail surveillance et comment surveiller les requêtes

## <a name="introduction-the-monitor-page"></a>Introduction : La page Moniteur

Le portail de gestion Azure et Azure Portal surface d’indicateurs de performance clés pouvant être utilisées pour surveiller et résoudre les problèmes de performances de votre requête et tâche. 

Dans le portail de gestion de Azure, cliquez sur l’onglet **Moniteur** d’une tâche de flux de données Analytique en cours d’exécution pour afficher les mesures. Il y a un délai de 1 minute au maximum dans les mesures de performances ne s’affiche dans la page Moniteur.  

  ![Travail d’analyse du tableau de bord](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

Dans le portail Azure, accédez à la tâche de flux Analytique vous intéressez Voir mesures pour et afficher la section de **surveillance** .  

  ![Travail de surveillance portail Azure du tableau de bord](./media/stream-analytics-monitoring/06-stream-analytics-monitoring.png)  

La première fois qu'une tâche de flux de données Analytique est créée dans une région, vous devrez configurer des Diagnostics pour la région. Pour ce faire, cliquez n’importe où dans la section de **surveillance** et la carte **Diagnostics** s’affichent. Ici, vous pouvez activer diagnostics et spécifiez un compte de stockage d’analyser les données.  

  ![Azure Portal configurer requête Diagnostics](./media/stream-analytics-monitoring/07-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Mesures disponibles pour les flux Analytique


| Métrique | Définition |
|--------|-------------|
| Taux d’utilisation % so | L’utilisation de l’unité de diffusion en continu affecté à une tâche à partir de l’onglet échelle du travail. Cet indicateur doit parvenir à 80 % ou au-dessus, il est forte probabilité que le traitement de l’événement peut être retardée ou arrêté sa progression. |
| Événements d’entrée | Volume de données reçues par la tâche de flux Analytique, nombre d’événements. Cela peut servir à valider que les événements sont envoyés à la source d’entrée. |
| Événements de sortie | Quantité de données envoyées par la tâche de flux de données Analytique à la cible de sortie, en nombre d’événements. |
| Événements de sortie de commande | Nombre d’événements reçus dans l’ordre qui ont été supprimées ou donné un horodatage ajusté, en fonction de la stratégie de classement événement. Cela peut être affecté par la configuration du paramètre de tolérance fenêtre. |
| Erreurs de Conversion de données | Nombre d’erreurs de conversion de données prévu par une tâche de flux de données Analytique. |
| Erreurs d’exécution | Nombre d’erreurs qui se produisent pendant l’exécution d’une tâche de flux de données Analytique. |
| Événements d’entrée en retard | Nombre d’événements arrive au plus tard à partir de la source qui soit ont été supprimées ou leur horodatage a été ajusté, selon la configuration de stratégie de classement des événements du paramètre au plus tard arrivée tolérance fenêtre. |

## <a name="customizing-monitoring-in-the-azure-management-portal"></a>Personnalisation de surveillance dans le portail de gestion d’Azure ##

Jusqu'à 6 métriques peuvent être affichés sur un graphique.

Pour basculer entre l’affichage des valeurs relatives (valeur finale uniquement pour chaque métrique) et absolue (axe Y affiché), sélectionnez Relative ou absolue dans la partie supérieure du graphique.

  ![Analyseur de requête Relative absolue](./media/stream-analytics-monitoring/02-stream-analytics-monitoring.png)  

Indicateurs peut être affiché dans le graphique de l’analyseur dans les agrégations d’une heure, 12 heures, 24 heures ou des 7 derniers jours.

Pour modifier la période la métrique graphique s’ouvre, sélectionnez 1 hour, 24 heures ou jours 7 dans la partie supérieure du graphique.

  ![Analyseur de requête échelle de temps](./media/stream-analytics-monitoring/03-stream-analytics-monitoring.png)  

Vous pouvez définir des règles qui peuvent vous avertir par courrier électronique au cas où le travail dépasse un seuil défini. 

## <a name="customizing-monitoring-in-the-azure-portal"></a>Personnalisation de l’analyse dans le portail Azure ##

Vous pouvez ajuster le type de graphique, indicateurs indiqué et ajuster le minutage plage dans les paramètres de modifier le graphique. Pour plus d’informations, voir [comment personnaliser surveillance](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

  ![Échelle de temps du moniteur requête portail Azure](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  

## <a name="job-status"></a>État du travail

Le statut des tâches de flux de données Analytique peut être affiché dans le portail classique Azure où vous voyez une liste des tâches. Vous pouvez afficher la liste des tâches en cliquant sur l’icône de flux de données Analytique dans le portail classique Azure.

| État | Définition |
|--------|------------|
| Créé | Une tâche a été créé, toutefois n’a pas été démarré. |
| Démarrage | Un utilisateur a cliqué sur Démarrer la tâche et la tâche démarre |
| En cours d’exécution | La tâche est affectée, traiter les données entrées, ou en attente de traiter l’entrée. Si la tâche montre un état en cours d’exécution sans générer une sortie, il est probable que la fenêtre de temps de traitement des données est grande ou la logique de requête est complexe. Une autre raison peut être qu’actuellement il n’existe pas les données envoyées à la tâche. |
| Arrêt | Un utilisateur a cliqué sur Arrêter le travail, et la tâche doit s’arrêter. |
| Arrêté | La tâche a été arrêtée. |
| Est détérioré | Cet état indique qu’une tâche de flux de données Analytique rencontre des erreurs transitoires (pour ex. Erreurs d’entrée/sorties, des erreurs de traitement, des erreurs de conversion etc.). La tâche est toujours en cours d’exécution, cependant, il existe un grand nombre de messages d’erreur. Cette tâche une attention immédiate client et le client peut voir les journaux d’opérations pour les erreurs. |
| A échoué | Cela indique que le travail a échoué en raison des erreurs, et le traitement a arrêté. Le client a besoin de consulter les journaux d’opérations pour déboguer les erreurs. |
| La suppression | Cela indique que la tâche est supprimée. |

## <a name="diagnosis"></a>Diagnostic

Dans le portail de gestion de Azure, le tableau de bord de travail fournit des informations sur l’endroit où vous devez rechercher pour le diagnostic, c'est-à-dire entrées, sorties et/ou les opérations de se connecter. Vous pouvez cliquer sur le lien pour consulter l’emplacement approprié pour examiner le diagnostic.

  ![Analyseur de requête d’erreur](./media/stream-analytics-monitoring/04-stream-analytics-monitoring.png)  

En cliquant sur la ressource entrée ou en sortie d’informations de diagnostic détaillées. Ceci est actualisé avec les dernières informations de diagnostic pendant l’exécution de la tâche.

  ![Diagnostics de requête](./media/stream-analytics-monitoring/05-stream-analytics-monitoring.png)  

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir une aide supplémentaire, essayez de notre [forum Azure flux Analytique](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

- [Présentation des flux de données Azure Analytique](stream-analytics-introduction.md)
- [Commencer à utiliser Azure flux Analytique](stream-analytics-get-started.md)
- [Échelle Azure flux Analytique travaux](stream-analytics-scale-jobs.md)
- [Référence du langage flux Azure Analytique requête](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence de l’API REST gestion Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
