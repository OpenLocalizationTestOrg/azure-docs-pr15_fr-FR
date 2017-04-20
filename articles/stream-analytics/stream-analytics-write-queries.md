<properties 
    pageTitle="Comment écrire des requêtes dans le flux de données Analytique | Microsoft Azure" 
    description="Écrire des requêtes Analytique de flux et les données de requête | segment de tracé d’apprentissage."
    keywords="comment écrire des requêtes, données de la requête, écrivez une requête, l’écriture de requêtes"
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

# <a name="how-to-write-queries-in-stream-analytics"></a>Comment écrire des requêtes dans le flux de données Analytique

Écriture de requêtes pour flux de traitement d’une logique dans Azure flux Analytique est activée en tant que « permanent requête « défini avant un travail démarre et exécuté sur les données qu’il atteint la tâche. La transformation de données est exprimée en un langage de requête de type SQL, qui est largement un sous-ensemble de T-SQL avec certaines extensions de langage ajouté comme [Windowing](https://msdn.microsoft.com/library/azure/dn835019.aspx) express sémantique temporelle.

## <a name="writing-queries"></a>Écrire des requêtes : ##

1. Dans votre travail Analytique flux dans le portail de gestion Azure, cliquez sur **requête**.

    ![Sélectionnez la requête](./media/stream-analytics-write-queries/1-stream-analytics-write-queries.png)  

    Dans le portail Azure, cliquez sur **requête**.

    ![Sélectionnez Aperçu de la requête](./media/stream-analytics-write-queries/query-preview-portal.png)  

2.  Nouvelles tâches ont un modèle de requête afin de vous aider à démarrer. Le modèle de requête effectue une requête « directe » qui projets tous les champs à partir d’événements d’entrée dans la sortie.  

    - Si vous avez défini au moins une entrée et sortie pour votre projet, vous pouvez remplacer l’espace réservé « [YourOutputAlias] » et « [YourInputAlias] » des champs avec les alias de l’entrée et sortie que vous souhaitez utilisent tout d’abord. En outre, vous pouvez toujours créer et tester votre requête dans le portail classique Azure sans définir les entrées et les sorties sur la tâche.
    - Si vous souhaitez effectuer davantage de traitement qu’un directe simple, vous pouvez modifier la définition de la requête. Pour commencer la création de requête, examinons quelques requête commune modèles sont capturés [ici](stream-analytics-stream-analytics-query-patterns.md).  
  
    ![Fenêtre données de requête](./media/stream-analytics-write-queries/2-stream-analytics-write-queries.png)  

## <a name="to-validate-query-data-is-working"></a>Pour valider les données de la requête fonctionne : ##

Vous pouvez vérifier que votre requête se comporte comme prévu en exécutant dans le navigateur sur un ou plusieurs fichiers JSON locales contenant les données de test. Cela ne sera pas démarrer le travail ou ont des implications facturation.

> [AZURE.NOTE] Actuellement le test de requête dans le navigateur n’est pas pris en charge dans le portail Azure.  

1.  Vérifiez qu’il n’y a pas d’erreurs dans la requête (dans le cas contraire le bouton Test est désactivé) puis cliquez sur le bouton Test.  

    ![Consulter les données de Test](./media/stream-analytics-write-queries/3-stream-analytics-write-queries.png)  

2.  Vous devrez spécifier des fichiers pour chacune des entrées référencées dans la requête. Dans cet exemple, la requête de modèle est laissée tel-est, pour que la boîte de dialogue demande une entrée appelée « yourinputalias ».  

    ![Tester la requête données](./media/stream-analytics-write-queries/4-stream-analytics-write-queries.png)  

3.  Accédez à un fichier de test. Plusieurs exemples de fichiers sont disponibles sur [github](https://github.com/Azure/azure-stream-analytics/tree/master/Sample Data) et vous pouvez également récupérer des exemples de données à partir de votre propre entrées de flux de données via la fonction exemples de données sous l’onglet entrées.  

    ![Entrée de requête](./media/stream-analytics-write-queries/5-stream-analytics-write-queries.png)  

4.  Après avoir fermé la boîte de dialogue, votre requête sera exécutée sur les données de test et vous verrez les résultats en bas de la page de requête.  

    ![Résumé de la requête](./media/stream-analytics-write-queries/6-stream-analytics-write-queries.png)  

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir une aide supplémentaire, essayez de notre [forum Azure flux Analytique](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

- [Présentation des flux de données Azure Analytique](stream-analytics-introduction.md)
- [Commencer à utiliser Azure flux Analytique](stream-analytics-get-started.md)
- [Échelle Azure flux Analytique travaux](stream-analytics-scale-jobs.md)
- [Référence du langage flux Azure Analytique requête](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence de l’API REST gestion Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
