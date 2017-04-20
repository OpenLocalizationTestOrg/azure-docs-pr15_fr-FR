<properties 
   pageTitle="Tâches de sauvegarde Gestionnaire d’instantanés StorSimple | Microsoft Azure"
   description="Décrit comment utiliser le composant logiciel enfichable MMC Gestionnaire d’instantanés StorSimple pour afficher et gérer les travaux de sauvegarde planifiée, en cours d’exécution et terminées."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/26/2016"
   ms.author="v-sharos" />


# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>Gestionnaire d’instantanés StorSimple permet d’afficher et gérer les travaux de sauvegarde

## <a name="overview"></a>Vue d’ensemble

Le nœud de **tâches** dans le volet **étendue** indique les **planifiée**, **dernières 24 heures**et **exécute** les tâches de sauvegarde que vous avez lancé de façon interactive ou par une stratégie configurée. 

Ce didacticiel décrit comment vous pouvez utiliser le nœud de **tâches** pour afficher des informations sur les travaux de sauvegarde planifiées, récentes et en cours d’exécution. (La liste des tâches et des informations correspondantes s’affiche dans le volet **résultats** ). Vous pouvez en outre, avec le bouton droit à une tâche répertoriée et afficher un menu contextuel qui répertorie les actions disponibles.

## <a name="view-scheduled-jobs"></a>Afficher les tâches planifiées

Utilisez la procédure suivante pour afficher les tâches de sauvegarde.

#### <a name="to-view-scheduled-jobs"></a>Pour afficher les tâches planifiées

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple. 

2. Dans le volet de **l’arborescence** , développez le nœud de **tâches** , puis cliquez sur **planifiée**. Les informations suivantes s’affiche dans le volet **résultats** :

    - **Nom** : le nom de l’instantané planifiée

    - **Prochaine exécution** – la date et l’heure de la prochaine capture programmée

    - **Dernière exécution** – la date et l’heure de la dernière capture instantanée planifiée

    >[AZURE.NOTE] Pour les instantanés uniquement ponctuel, la **Prochaine exécution** et **Dernière exécution** sera la même. 
 
    ![Tâches de sauvegarde](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
 
3. Pour effectuer des actions supplémentaires sur une tâche spécifique, droit le nom du travail dans le volet des **résultats** , puis sélectionnez parmi les options de menu.

## <a name="view-recent-jobs"></a>Afficher les tâches récentes

Utilisez la procédure suivante pour afficher de sauvegarde et restauration des tâches effectuées dans les dernières 24 heures.

#### <a name="to-view-recent-jobs"></a>Pour afficher les tâches récentes

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet de **l’arborescence** , développez le nœud de **tâches** , puis cliquez sur les **dernières 24 heures**. Pour les dernières 24 heures (avec un maximum de 64 travaux), le volet des **résultats** affiche des travaux de sauvegarde. Les informations suivantes s’affiche dans le volet de **résultats** , selon les options **d’affichage** que vous spécifiez :

    - **Nom** : le nom de l’instantané planifiée.
 
    - **Mise en route** – la date et l’heure de début lors de l’instantané.

    - **Arrêt** – la date et l’heure de l’instantané terminée ou a été arrêté.

    - Délai d’attente **écoulé** – le délai entre la **mise en route** et **arrêté** .

    - **État** – l’état du travail effectué récemment. **Réussite** indique que la sauvegarde a été créée avec succès. **Échec** indique que le travail ne s’est pas exécuté correctement.

    - **Informations** : la raison de l’échec.

    - **Octets traités (Mo)** – la quantité de données à partir du groupe de volume qui a été traité (en Mo). 

    ![Tâches qui se sont exécutés dans les dernières 24 heures](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 

3. Pour effectuer des actions supplémentaires sur une tâche spécifique, droit le nom du travail dans le volet des **résultats** , puis sélectionnez parmi les options de menu.

    ![Supprimer une tâche](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png) 
     
## <a name="view-currently-running-jobs"></a>Afficher les tâches en cours d’exécution

Utilisez la procédure suivante pour afficher les tâches en cours d’exécution.

#### <a name="to-view-currently-running-jobs"></a>Pour afficher les tâches en cours d’exécution

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet de **l’arborescence** , développez le nœud de **tâches** , puis cliquez sur **en cours d’exécution**. Selon les options **d’affichage** que vous spécifiez, les informations suivantes s’affiche dans le volet **résultats** : 

    - **Nom** : le nom de l’instantané planifiée.

    - **Mise en route** – la date et l’heure de début lors de l’instantané.

    - **Point de contrôle** – l’action en cours de la sauvegarde.

    - **État** – le pourcentage d’achèvement.
    
    - **Écoulé** – la durée écoulée depuis le début de la sauvegarde. 

    - **Moyenne de débit (Mo)** – rapport du nombre total d’octets des données traitées à celle de la durée totale de traitement (Mo).

    - **Octets traités (Mo)** – nombre total d’octets des données traitées (en Mo).

    - **Octets écrits (Mo)** – nombre total d’octets de données écrites (en Mo). Il inclut les données, ainsi que les métadonnées et par conséquent est généralement supérieure à l’octets traités.

    ![Tâches en cours d’exécution](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)

3. Pour effectuer des actions supplémentaires sur une tâche spécifique, droit le nom du travail dans le volet des **résultats** , puis sélectionnez parmi les options de menu.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [utiliser le Gestionnaire d’instantanés StorSimple pour administrer votre solution StorSimple](storsimple-snapshot-manager-admin.md).
- Découvrez comment [utiliser StorSimple instantané Manager pour gérer le catalogue de sauvegarde](storsimple-snapshot-manager-manage-backup-catalog.md).















            


 

