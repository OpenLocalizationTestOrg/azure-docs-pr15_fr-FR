<properties
    pageTitle="Définir des alertes pour les requêtes de flux Analytique | Microsoft Azure"
    description="Présentation Analytique de flux d’alerte"
    keywords="configurer des alertes"
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


# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Définir des alertes pour les travaux Azure flux Analytique

## <a name="introduction-monitor-page"></a>Introduction : Page Moniteur

Vous pouvez définir des alertes pour déclencher une alerte lorsqu’un indicateur atteint une condition que vous spécifiez.

Par exemple, « si les événements de sortie pour depuis 15 minutes est < 100, Envoyer notification par courrier électronique à l’id de courrier électronique : xyz@company.com”.

Règles peuvent être configurés sur indicateurs via le portail ou peuvent être configuré [par programme](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) sur les données des journaux d’opération.

## <a name="set-up-alerts-through-the-azure-classic-portal"></a>Configurer des alertes via le portail classique Azure

Il existe deux manières de définir des alertes dans le portail Azure classique :  

1.  L’onglet **Analyseur** de votre tâche de flux de données Analytique  
2.  Le journal des opérations dans les services de gestion  

## <a name="set-up-alert-through-the-monitor-tab-of-the-job-in-the-portal"></a>Définir une alerte via l’onglet Moniteur de la tâche dans le portail

1.  Sélectionnez la mesure dans l’onglet Moniteur, cliquez sur le bouton **Ajouter une règle** dans la partie inférieure du tableau de bord et d’installation de vos règles.  

    ![Tableau de bord](./media/stream-analytics-set-up-alerts/01-stream-analytics-set-up-alerts.png)  

2.  Définir le nom et la description de l’alerte  

    ![Créer la règle](./media/stream-analytics-set-up-alerts/02-stream-analytics-set-up-alerts.png)  

3.  Entrez les seuils, fenêtre alerte d’évaluation et les actions de l’alerte  

    ![Définir des Conditions](./media/stream-analytics-set-up-alerts/03-stream-analytics-set-up-alerts.png)  

## <a name="set-up-alerts-through-the-operations-logs"></a>Configurer des alertes par le biais les journaux d’opérations

1.  Accédez à l’onglet **alertes** dans la gestion des Services dans le [Portail classique Azure](https://manage.windowsazure.com).  
2.  Cliquez sur **Ajouter une règle**  

    ![Critères](./media/stream-analytics-set-up-alerts/04-stream-analytics-set-up-alerts.png)  

3.  Définir le nom et la description de l’alerte. Sélectionnez « Flux Analytique » comme Type de Service et le nom du travail en tant que le nom du Service.  

    ![Définition d’alerte](./media/stream-analytics-set-up-alerts/05-stream-analytics-set-up-alerts.png)  

## <a name="set-up-alerts-in-the-azure-portal"></a>Configurer des alertes dans le portail Azure ##

Dans le portail Azure, accédez à la tâche de flux Analytique qui vous intéresse dans l’alerte, puis cliquez sur la section **surveillance** .  Dans la carte **métrique** qui s’ouvre, cliquez sur la commande **Ajouter une alerte** .

  ![Programme d’installation du portail Azure](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)  

Vous pouvez nommer votre règle d’alerte, puis choisissez une description qui s’affichent dans le message électronique de notification.

Lorsque vous sélectionnez métrique vous devez choisir une condition et une valeur de seuil pour la mesure.

  ![Métrique select portail Azure](./media/stream-analytics-set-up-alerts/07-stream-analytics-set-up-alerts.png)  

Pour plus d’informations sur la configuration des alertes dans le portail Azure, voir [notifications d’alerte réception](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).  

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir une aide supplémentaire, essayez de notre [forum Azure flux Analytique](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

- [Présentation des flux de données Azure Analytique](stream-analytics-introduction.md)
- [Commencer à utiliser Azure flux Analytique](stream-analytics-get-started.md)
- [Échelle Azure flux Analytique travaux](stream-analytics-scale-jobs.md)
- [Référence du langage flux Azure Analytique requête](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence de l’API REST gestion Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
