<properties 
    pageTitle="Comment démarrer la diffusion en continu les tâches de flux de données Analytique | Microsoft Azure" 
    description="Comment exécuter une tâche de diffusion en continu dans Azure flux Analytique | segment de tracé d’apprentissage."
    keywords="tâches de diffusion en continu"
    documentationCenter=""
    services="stream-analytics"
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

# <a name="how-to-run-a-streaming-job-in-azure-stream-analytics"></a>Comment faire pour exécuter une tâche de diffusion en continu dans Azure flux Analytique

Quand une tâche de saisie, la requête et la sortie ont toutes été spécifiés que vous pouvez démarrer la tâche de flux de données Analytique.

Pour commencer votre travail :

1.  Dans le portail Azure classique, à partir du tableau de bord de travail, cliquez sur **Démarrer** dans la partie inférieure de la page.

    ![Démarrer le travail bouton](./media/stream-analytics-run-a-job/1-stream-analytics-run-a-job.png)  

    Dans le portail Azure, cliquez sur **Démarrer** en haut de la page de votre travail.

    ![Tâche de début du portail Azure bouton](./media/stream-analytics-run-a-job/4-stream-analytics-run-a-job.png)  

2.  Spécifier une valeur de **Sortie démarrer** pour déterminer quand cette tâche démarre à générer une sortie. Le paramètre par défaut pour les tâches qui n’ont pas été précédemment démarré est **Heure de début du projet**, ce qui signifie que la tâche démarre immédiatement traitement des données. Vous pouvez également spécifier une heure **personnalisée** dans le passé (pour l’utilisation des données historiques) ou l’avenir (pour différer le traitement jusqu'à une date ultérieure). Dans les cas lorsqu’une tâche a été précédemment démarrée et arrêtée, l’option **Dernière fois arrêté** est disponible pour reprendre la tâche à partir de la dernière fois de sortie et éviter de perdre des données.  

    ![Démarrer la diffusion en continu de temps de travail](./media/stream-analytics-run-a-job/2-stream-analytics-run-a-job.png)  

    ![Azure portail en continu tâche de début heure](./media/stream-analytics-run-a-job/5-stream-analytics-run-a-job.png)  

3.  Confirmez votre sélection. L’état du travail met en *commençant* et se déplace peu de temps à *l’exécution* d’une fois que le travail a démarré. Vous pouvez surveiller l’avancement de l’opération **commencer** dans le **Hub de Notification**:

    ![diffusion en continu de l’avancement des tâches](./media/stream-analytics-run-a-job/3-stream-analytics-run-a-job.png)  

    ![Portail Azure diffusion en continu de l’avancement des tâches](./media/stream-analytics-run-a-job/6-stream-analytics-run-a-job.png)  

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir une aide supplémentaire, essayez de notre [forum Azure flux Analytique](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

- [Présentation des flux de données Azure Analytique](stream-analytics-introduction.md)
- [Commencer à utiliser Azure flux Analytique](stream-analytics-get-started.md)
- [Échelle Azure flux Analytique travaux](stream-analytics-scale-jobs.md)
- [Référence du langage flux Azure Analytique requête](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence de l’API REST gestion Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
