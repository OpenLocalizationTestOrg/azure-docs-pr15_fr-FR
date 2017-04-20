<properties 
    pageTitle="Comment créer une tâche de traitement analytique données pour flux Analytique | Microsoft Azure" 
    description="Créer une tâche de traitement analytique données pour flux Analytique | segment de tracé d’apprentissage."
    keywords="traitement analytique des données"
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

# <a name="how-to-create-a-data-analytics-processing-job-for-stream-analytics"></a>Comment créer une tâche de traitement analytique données pour flux Analytique

La ressource de niveau supérieur dans Azure flux Analytique est une tâche Analytique flux.  Il se compose d’une ou plusieurs sources de données d’entrée et une ou plusieurs cibles de sortie résultats sont écrits dans une requête exprimant la transformation de données. Ensemble ceux-ci permettent l’utilisateur d’effectuer analytique de données de traitement de diffusion en continu les scénarios de données.

Pour commencer à l’aide de flux de données Analytique, commencez par créer une nouvelle tâche de flux de données Analytique.  Notez que cette action n’a aucune incidence facturation jusqu'à ce que le travail est démarré.

1.  Connectez-vous sur le site online [Azure portal classique](http://manage.windowsazure.com) ou le [portail Azure](https://portal.azure.com/).
2.  Dans le portail : **Cliquez sur Nouveau**, puis cliquez sur **Services de données** ou **Données Analytique** en fonction de votre portail, puis cliquez sur **Azure flux Analytique** ou **Flux Analytique** , puis **Création rapide**.

    ![Assistant de tâche de traitement analytique des données](./media/stream-analytics-create-a-job/1-stream-analytics-create-a-job.png)  

    ![Créer analytique données travail en cours](./media/stream-analytics-create-a-job/4-stream-analytics-create-a-job.png)  

3.  Spécifier la configuration de votre choix pour la tâche de flux de données Analytique.
    - Dans la zone **Nom de la tâche** , entrez un nom pour identifier l’Analytique de flux de travail. Lorsque le **Nom de la tâche** est validé, une coche verte apparaît dans la zone Nom de la tâche. Le **Nom de la tâche** peut contenir uniquement des caractères alphanumériques et «- » de caractères et doit être comprise entre 3 et 63 caractères.
    - Utilisez **région** dans le portail Azure ou **l’emplacement** dans le portail Azure pour spécifier l’emplacement géographique dans lequel vous souhaitez exécuter la tâche.
    - Si vous utilisez le portail Azure, sélectionnez ou créez un compte de stockage à utiliser comme le **Compte de stockage de surveillance régionaux**. Ce compte de stockage est utilisé pour stocker les données d’analyse pour toutes les tâches de flux de données Analytique en cours d’exécution dans cette zone.
    - Si vous utilisez le portail Azure, spécifiez un existant ou nouveau **Groupe de ressources** pour mettre en attente de ressources associées à votre application.

4.  Une fois que les nouvelles options de tâche de flux Analytique sont configurées, cliquez sur **Créer un flux de travail Analytique**. Il peut prendre quelques minutes pour que la tâche de flux Analytique à créer. Pour vérifier l’état, vous pouvez suivre la progression dans le hub de Notifications.

    ![Concentrateur de notifications de données analytique traitement de travail](./media/stream-analytics-create-a-job/2-stream-analytics-create-a-job.png)  

    ![Azure analytique données portail travail en cours Créer tâche](./media/stream-analytics-create-a-job/5-stream-analytics-create-a-job.png)  

5.  La nouvelle tâche est affichée avec le statut **créé**. Notez que le bouton **Démarrer** est désactivé. Vous devez configurer l’entrée de travail, la requête et la sortie avant de démarrer la tâche.

    ![Travail de tâche de traitement analytique des données état](./media/stream-analytics-create-a-job/3-stream-analytics-create-a-job.png)  

    ![Azure analytique données portail travail l’état du travail du traitement](./media/stream-analytics-create-a-job/6-stream-analytics-create-a-job.png)  

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir une aide supplémentaire, essayez de notre [forum Azure flux Analytique](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

- [Présentation des flux de données Azure Analytique](stream-analytics-introduction.md)
- [Commencer à utiliser Azure flux Analytique](stream-analytics-get-started.md)
- [Échelle Azure flux Analytique travaux](stream-analytics-scale-jobs.md)
- [Référence du langage flux Azure Analytique requête](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence de l’API REST gestion Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
