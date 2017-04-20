<properties
    pageTitle="Visualiser et résoudre les problèmes de tâches de flux de données Analytique | Microsoft Azure"
    description="Découvrez comment visualiser une opportunité de tâche de flux Analytique pour libre-service dépannage à l’aide de la fonctionnalité de diagramme diagnostics."
    keywords=""
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>


# <a name="visualize-and-troubleshoot-stream-analytics-jobs"></a>Visualiser et résoudre les problèmes de tâches de flux de données Analytique

Dans flux Analytique, comme avec d’autres technologies sur le nuage, résolution des problèmes sont parfois nécessaire pour étudier la raison pour laquelle une tâche ne génère pas le résultat attendu (ou toute sortie dans notre exemple). Par conséquent, flux Analytique offre la possibilité de visualisation d’une tâche de diffusion en continu. Ceci est également pratique comme un outil de modélisation et a l’avantage de côté pour ces nécessitant une documentation de leur travail.

Les entrées apparaissent dans le volet de visualisation ainsi que la requête en cours d’exécution et puis toutes les sorties configurés. Problèmes de connectivité ou de configuration peuvent devenir plus évidentes et il peut également être utile afficher une représentation visuelle de votre configuration.

## <a name="using-the-diagnosis-diagram-tool"></a>À l’aide de l’outil de diagramme de diagnostic

Pour accéder à ce visualiseur, cliquez simplement sur le bouton « Diagnostic diagramme » dans la carte de « Paramètres » de la de la tâche de flux de données Analytique.

![Stream-Analytics-Troubleshoot-Visualization-Diagnosis-Diagram](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-diagnosis-diagram1.png)

Chaque entrée et sortie sont état de couleur pour indiquer en cours de ce composant, comme illustré ci-dessous.

![Stream-Analytics-Troubleshoot-Visualization-Input-Map](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-input-map.png)

Lorsque l’utilisateur souhaite consulter les étapes de requête intermédiaires de comprendre les modèles de flux de données à l’intérieur d’une tâche, l’outil de visualisation fournit une vue de la répartition de la requête dans ses étapes composant et la séquence de flux. En cliquant sur chaque étape de requête, la section correspondante s’affichent dans une requête volet comme l’illustre la modification. 

![Stream-Analytics-Troubleshoot-Visualization-Intermediate-Steps](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-intermediate-steps.png)




## <a name="next-steps"></a>Étapes suivantes

- [Présentation des flux de données Azure Analytique](stream-analytics-introduction.md)
- [Commencer à utiliser Azure flux Analytique](stream-analytics-get-started.md)
- [Échelle Azure flux Analytique travaux](stream-analytics-scale-jobs.md)
- [Référence du langage flux Azure Analytique requête](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence de l’API REST gestion Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
