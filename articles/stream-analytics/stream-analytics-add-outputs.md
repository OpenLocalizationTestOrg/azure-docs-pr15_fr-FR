<properties 
    pageTitle="Comment configurer les données de sortie pour les tâches de flux de données Analytique | Microsoft Azure" 
    description="Configurer des sorties pour les tâches de flux de données Analytique | segment de tracé d’apprentissage."
    keywords="données de sortie, déplacement de données"
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

# <a name="how-to-configure-data-outputs-for-stream-analytics-jobs"></a>Comment configurer les données de sortie pour les tâches de flux de données Analytique

Tâches de flux Analytique Azure pouvant être connectés à une ou plusieurs sorties de données qui définissent une connexion à un récepteur de données existante. Comme votre tâche de flux de données Analytique traite et des transformations de données entrantes, un flux d’événements de sortie de données est écrits au résultat de votre travail.

Flux Analytique données sorties pouvant être utilisées pour la source de tableaux de bord en temps réel ou des alertes, déclenchement de flux de travail données mouvement ou simplement archiver les données pour traitement par lots ultérieurement. Flux Analytique a première classe intégration avec plusieurs services Azure qui sont présentés en détail ici.

Pour ajouter une sortie à votre tâche de flux de données Analytique :

1. Dans le portail classique Azure, cliquez sur **sorties** , puis sur **Ajouter une sortie** dans votre tâche de flux de données Analytique.

    ![Ajouter des sorties](./media/stream-analytics-add-outputs/1-stream-analytics-add-outputs.png)  

    Dans le portail Azure cliquez sur la vignette **des sorties** dans votre tâche de flux de données Analytique.

    ![Azure Portal ajouter des sorties](./media/stream-analytics-add-outputs/5-stream-analytics-add-outputs.png)

2. Spécifier le type de la sortie :

    ![Sélectionnez un type de déplacement de données](./media/stream-analytics-add-outputs/2-stream-analytics-add-outputs.png)  

    ![Portail Azure sélectionnez un type de déplacement de données](./media/stream-analytics-add-outputs/6-stream-analytics-add-outputs.png)

3. Indiquez un nom convivial pour ce résultat dans la zone **Alias de sortie** . Ce nom peut être utilisé dans une requête de votre travail ultérieurement pour faire référence à la sortie.  
    
    Renseignez le reste des propriétés de connexion requise pour vous connecter à votre sortie.  Ces champs varient selon le type de sortie et sont définies en détail ici.  

    ![Ajouter des propriétés de sortie de données](./media/stream-analytics-add-outputs/3-stream-analytics-add-outputs.png)  

4. Selon le type de sortie, vous devrez peut-être spécifier comment les données sont sérialisées ou mis en forme. Les paramètres de sérialisation spécifique pour chaque type de sortie sont présentés ici.

    Renseignez le reste des propriétés de connexion requise pour vous connecter à votre source de données. Ces champs varient selon le type d’entrée et source type et sont définies en détail [ici](stream-analytics-create-a-job.md).  

    ![Ajouter de sortie des données à concentrateur d’événement](./media/stream-analytics-add-outputs/4-stream-analytics-add-outputs.png)  

    ![Sortie des données portail Azure à concentrateur d’événement](./media/stream-analytics-add-outputs/7-stream-analytics-add-outputs.png)  

> [Azure.Note] N’importe quel élément de sortie ajouté à la tâche, doivent exister avant le travail est démarré et événements Démarrer s’étalant. Par exemple, si vous utilisez le stockage Blob comme résultat, le travail ne crée pas un compte de stockage automatiquement. Il doit être créés par l’utilisateur avant le début de la tâche ASA.

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir une aide supplémentaire, essayez de notre [forum Azure flux Analytique](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

- [Présentation des flux de données Azure Analytique](stream-analytics-introduction.md)
- [Commencer à utiliser Azure flux Analytique](stream-analytics-get-started.md)
- [Échelle Azure flux Analytique travaux](stream-analytics-scale-jobs.md)
- [Référence du langage flux Azure Analytique requête](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence de l’API REST gestion Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
