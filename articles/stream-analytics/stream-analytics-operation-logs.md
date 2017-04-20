<properties 
    pageTitle="Déboguer à l’aide d’opération et service se connecte flux Analytique | Microsoft Azure" 
    description="Utilisation de procédures flux Analytique opération journaux" 
    keywords="journaux de service"
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

# <a name="debug-stream-analytics-jobs-using-service-and-operation-logs"></a>Déboguer des tâches de flux Analytique à l’aide de service et le fonctionnement des journaux

Tous les services Azure fournissent des messages de journalisation opérationnel aux utilisateurs pour enregistrer les détails relatifs aux opérations d’administration. Dans Azure flux Analytique, ces informations peuvent être utilisées pour le débogage telles que l’affichage de l’état de la tâche, l’avancement des tâches, et messages d’échec effectuer le suivi de l’avancement d’une tâche dans le temps, de commencent à traitement en sortie.

## <a name="find-operation-logs-in-the-azure-management-portal"></a>Rechercher ouvre une opération dans le portail de gestion d’Azure

Journaux d’opération est accessible de deux façons :  

- Tableau de bord de la tâche de flux de données Analytique  
- Gestion des Services dans le portail Azure classique  

## <a name="dashboard-of-the-stream-analytics-job"></a>Tableau de bord de la tâche de flux de données Analytique

Un lien vers les journaux d’une tâche de flux de données Analytique correspondants s’affiche dans l’onglet tableau de bord de la tâche. Si vous cliquez sur ce lien, il définira les filtres de sorte qu’il affiche dernières journaux pour cette tâche spécifique.

  ![Sélectionnez les journaux de gestion des Services](./media/stream-analytics-operation-logs/01-stream-analytics-operation-logs.png)  

## <a name="management-services"></a>Gestion des Services

Pour accéder manuellement dans les journaux d’opération de flux de données Analytique et d’autres services dans le portail Azure classique :

1.  Cliquez sur **Gestion des Services** dans le [portail Azure classique](https://manage.windowsazure.com).
2.  Sélectionnez **Analytique de flux de données** pour le **Type** et le nom de la tâche pour le **Nom de Service**.  

  ![Sélectionnez flux Analytique](./media/stream-analytics-operation-logs/02-stream-analytics-operation-logs.png)  

## <a name="find-audit-logs-in-the-azure-portal"></a>Dans le portail Azure rechercher des journaux d’audit ##

Pour rechercher les journaux des opérations de votre tâche de flux Analytique dans le portail Azure, cliquez sur **Parcourir** et puis sélectionnez **journaux d’Audit**.

  ![Portail Azure sélectionnez flux Analytique](./media/stream-analytics-operation-logs/06-stream-analytics-operation-logs.png)  

Cette action ouvre une carte montrant les événements appartenant à ces 7 derniers jours pour toutes les ressources dans votre abonnement.  Vous pouvez filtrer pour afficher les événements d’un type de spécifier ou d’un intervalle de temps en cliquant sur la commande **filtrer** .

  ![Portail Azure sélectionnez flux Analytique](./media/stream-analytics-operation-logs/07-stream-analytics-operation-logs.png)  

## <a name="get-log-details"></a>Obtenez des informations de journal

Vous pouvez filtrer par période et état pour afficher les journaux pour votre projet.

Dans le portail de gestion de Azure, cliquez sur le bouton **Détails** dans la partie inférieure de la fenêtre pour afficher plus d’informations sur un événement sélectionné. 

  ![Sélectionnez les détails](./media/stream-analytics-operation-logs/03-stream-analytics-operation-logs.png)  

Dans le portail Azure, cliquez sur une entrée de journal pour afficher les événements détaillées qu’elle contient.

  ![Portail Azure sélectionnez Détails](./media/stream-analytics-operation-logs/08-stream-analytics-operation-logs.png)  

À partir de là, vous pouvez ouvrir la carte de **Détails** en cliquant sur l’événement.

  ![Portail Azure sélectionnez Détails](./media/stream-analytics-operation-logs/09-stream-analytics-operation-logs.png)  

## <a name="debug-a-failed-job"></a>Déboguer Échec d’un travail

Dans le portail de gestion de Azure, cliquez sur l’icône de recherche et le type « a échoué ». Cela vous donne un résultat de tous les journaux avec des erreurs. 

  ![Échec d’un travail de débogage](./media/stream-analytics-operation-logs/04-stream-analytics-operation-logs.png)  

Dans le portail Azure, vous pouvez filtrer par niveau de message pour afficher les événements **critique** .

  ![Déboguer portail Azure](./media/stream-analytics-operation-logs/10-stream-analytics-operation-logs.png)  

Vous pouvez sélectionner l’une échecs de, puis cliquez sur les **Détails** pour plus d’informations sur l’erreur.  Certains messages d’erreur fournissent des informations sur la façon de réduire le problème. 

  ![Détails de l’opération](./media/stream-analytics-operation-logs/05-stream-analytics-operation-logs.png)  

Au cas où vous devez contacter le [support technique](https://azure.microsoft.com/support/options/) ou fournissent des informations à l’équipe via le [forum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics), notez les détails de l’opération, en particulier l' **ID de corrélation**. 

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir une aide supplémentaire, essayez de notre [forum Azure flux Analytique](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

- [Présentation des flux de données Azure Analytique](stream-analytics-introduction.md)
- [Commencer à utiliser Azure flux Analytique](stream-analytics-get-started.md)
- [Échelle Azure flux Analytique travaux](stream-analytics-scale-jobs.md)
- [Référence du langage flux Azure Analytique requête](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence de l’API REST gestion Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
