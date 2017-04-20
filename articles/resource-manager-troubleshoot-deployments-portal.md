<properties
   pageTitle="Afficher les opérations de déploiement avec portail | Microsoft Azure"
   description="Décrit comment utiliser le portail Azure pour détecter les erreurs de déploiement du Gestionnaire de ressources."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="06/15/2016"
   ms.author="tomfitz"/>

# <a name="view-deployment-operations-with-azure-portal"></a>Afficher les opérations de déploiement avec Azure portail

> [AZURE.SELECTOR]
- [Portail](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Azure infrastructure du langage commun](resource-manager-troubleshoot-deployments-cli.md)
- [API REST](resource-manager-troubleshoot-deployments-rest.md)

Vous pouvez afficher les opérations pour un déploiement via le portail d’Azure. Vous intéresser plus dans l’affichage les opérations lorsque vous avez reçu un message d’erreur pendant le déploiement afin de cet article se concentre sur l’affichage des opérations qui n’ont pas pu. Le portail fournit une interface qui vous permet de trouver les erreurs et déterminer correctifs potentiels facilement.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

## <a name="use-deployment-operations-to-troubleshoot"></a>Opérations de déploiement permet de résoudre les problèmes

Pour afficher les opérations de déploiement, procédez comme suit :

1. Pour le groupe de ressources impliqué dans le déploiement, notez l’état du dernier déploiement. Vous pouvez sélectionner ce statut pour obtenir plus d’informations.

    ![statut du déploiement](./media/resource-manager-troubleshoot-deployments-portal/deployment-status.png)

2. Vous verrez l’historique de déploiement récent. Sélectionnez le déploiement qui a échoué.

    ![statut du déploiement](./media/resource-manager-troubleshoot-deployments-portal/select-deployment.png)

3. Sélectionnez **a échoué. Cliquez ici pour plus d’informations** pour consulter une description des raisons de l’échec du déploiement. Dans l’image ci-dessous, l’enregistrement DNS n’est pas unique.  

    ![afficher l’échec du déploiement](./media/resource-manager-troubleshoot-deployments-portal/view-error.png)

    Ce message d’erreur doit être suffisant pour pouvoir commencer le dépannage. Toutefois, si vous avez besoin de plus d’informations sur les tâches terminées, vous pouvez afficher les opérations comme indiqué dans les étapes suivantes.

4. Vous pouvez afficher toutes les opérations de déploiement de la carte de **déploiement** . Sélectionnez n’importe quelle opération pour afficher plus de détails.

    ![afficher les opérations](./media/resource-manager-troubleshoot-deployments-portal/view-operations.png)

    Dans ce cas, vous voyez que le compte de stockage, le réseau virtuel et l’ensemble de disponibilité ont été créés correctement. Échec de l’adresse IP et d’autres ressources n’ont pas été tentées.

5. Vous pouvez afficher les événements pour le déploiement en sélectionnant des **événements**.

    ![afficher les événements](./media/resource-manager-troubleshoot-deployments-portal/view-events.png)

6. Vous consultez tous les événements pour le déploiement et sélectionnez n’importe quel pour plus d’informations.

    ![afficher les événements](./media/resource-manager-troubleshoot-deployments-portal/see-all-events.png)

## <a name="use-audit-logs-to-troubleshoot"></a>Utilisation des journaux d’audit pour résoudre les problèmes

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Pour afficher les erreurs d’un déploiement, procédez comme suit :

1. Afficher les journaux d’audit pour un groupe de ressources en sélectionnant **Les journaux d’Audit**.

    ![Sélectionnez les journaux d’audit](./media/resource-manager-troubleshoot-deployments-portal/select-audit-logs.png)

2. Dans la carte **Les journaux d’Audit** , vous verrez un résumé des opérations récentes pour tous les groupes de ressources dans votre abonnement. Il inclut une représentation graphique de l’heure et l’état des opérations, ainsi que la liste des opérations.

    ![afficher les actions](./media/resource-manager-troubleshoot-deployments-portal/audit-summary.png)

3. Vous pouvez filtrer l’affichage des journaux d’audit pour vous concentrer sur des conditions spécifiques. Sélectionnez le **filtre** en haut de la cuillère **journaux d’Audit** .

    ![journaux de filtre](./media/resource-manager-troubleshoot-deployments-portal/filter-logs.png)

4. À partir de la carte de **filtre** , sélectionnez les conditions pour limiter l’affichage des journaux d’audit à uniquement les opérations que vous voulez afficher. Par exemple, vous pouvez filtrer les opérations pour afficher uniquement les erreurs du groupe de ressources.

    ![définir les options de filtre](./media/resource-manager-troubleshoot-deployments-portal/set-filter.png)

5. Vous pouvez filtrer davantage les opérations en définissant un intervalle de temps. L’image suivante filtre la vue timespan 20 minutes particulier.

    ![définir l’heure](./media/resource-manager-troubleshoot-deployments-portal/select-time.png)

6. Vous pouvez sélectionner une des opérations dans la liste. Sélectionnez l’opération qui contient l’erreur que vous voulez rechercher.

    ![Sélectionnez opération](./media/resource-manager-troubleshoot-deployments-portal/select-operation.png)
  
7. Vous verrez tous les événements pour cette opération. Notez les **ID de corrélation** dans le résumé. Cet identifiant est utilisé pour le suivi des événements connexes. Il peut être utile lorsque vous travaillez avec le support technique pour résoudre un problème. Vous pouvez sélectionner aucune des événement pour afficher les informations concernant l’événement.

    ![Sélectionnez l’événement](./media/resource-manager-troubleshoot-deployments-portal/select-event.png)

8. Vous verrez plus d’informations sur l’événement. En particulier, faites attention aux **Propriétés** pour plus d’informations sur l’erreur.

    ![afficher les détails du journal d’audit](./media/resource-manager-troubleshoot-deployments-portal/audit-details.png)

Le filtre que vous avez appliqué au journal d’audit est conservé la prochaine fois que vous affichez, vous serez peut-être amené à modifier ces valeurs pour élargir votre affichage des opérations.

## <a name="next-steps"></a>Étapes suivantes

- Pour vous aider à résoudre les erreurs de déploiement particulier, voir [résoudre les erreurs courantes lors du déploiement de ressources avec le Gestionnaire de ressources Azure Azure](resource-manager-common-deployment-errors.md).
- Pour en savoir plus sur l’utilisation des journaux d’audit pour surveiller d’autres types d’actions, voir [Auditer opérations avec le Gestionnaire de ressources](resource-group-audit.md).
- Pour valider votre déploiement avant de l’exécuter, voir [déploiement d’un groupe de ressources avec le Gestionnaire de ressources Azure modèle](resource-group-template-deploy.md).
