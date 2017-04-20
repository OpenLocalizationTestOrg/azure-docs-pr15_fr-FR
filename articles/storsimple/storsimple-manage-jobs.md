<properties 
   pageTitle="Afficher et gérer les travaux StorSimple | Microsoft Azure"
   description="Décrit la page de travaux service Manager StorSimple et comment l’utiliser pour effectuer le suivi des travaux de sauvegarde récentes, en cours et planifiées."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-view-and-manage-storsimple-jobs"></a>Utiliser le service Manager StorSimple pour afficher et gérer les travaux StorSimple

[AZURE.INCLUDE [storsimple-version-selector-manage-jobs](../../includes/storsimple-version-selector-manage-jobs.md)]

## <a name="overview"></a>Vue d’ensemble

La page **tâches** fournit un portail central unique pour l’affichage et gestion des tâches qui ont été démarrées sur les appareils connectés à votre service Manager StorSimple. Vous pouvez afficher les tâches planifiées, en cours d’exécution, terminés et échecs pour plusieurs appareils. Résultats sont présentés dans un format tabulaire. 

![Page de tâches](./media/storsimple-manage-jobs/HCS_JobsPage.png)

Vous pouvez trouver rapidement les tâches qui que vous intéressent en filtrant sur les champs tels que :

- **État** – tâches peuvent être exécutées, planifiée, échec, terminé, annulation ou annulé.

- **Type** – tâches peuvent être créées à la suite d’une tâche ou une sauvegarde à la demande (**Prendre sauvegarde**), clonant, une appareil restauration ou une opération de mise à jour.

- **Appareils** – travaux est initiés sur un périphérique donné connecté à votre service.

- **Du et au** – tâches peuvent être filtrées en fonction de la plage de date et heure.

Les tâches filtrées sont ensuite sous forme de tableau sur la base les attributs suivants :

- **Type** – sauvegarde, cloner, restaurer, basculement ou mise à jour.

- **État** – en cours d’exécution, planifiée, échec, terminé, annulation ou annulé.

- **Entité** – les tâches peuvent être associés à un volume, une stratégie de sauvegarde ou un appareil. Une tâche Cloner est associée à un volume, alors qu’une sauvegarde planifiée est associée à une stratégie de sauvegarde. Une tâche de l’appareil est créée suite à une récupération d’urgence (DR) ou une opération de restauration.

- **Appareil** – le nom de l’appareil sur lequel le travail a été démarré.

- **Commencer sur** – l’heure de début de la tâche.

- **L’avancement** – le pourcentage d’achèvement d’une tâche en cours d’exécution. Pour une tâche terminée, cela doit toujours être 100 %.

La liste des tâches est actualisée toutes les 30 secondes.

Vous pouvez effectuer les opérations suivantes liées à des projets sur cette page :

- Afficher les détails de projet

- Annulation d’une tâche

## <a name="view-job-details"></a>Afficher les détails de projet

Procédez comme suit pour afficher les détails de n’importe quelle tâche.

#### <a name="to-view-job-details"></a>Pour afficher les détails d’une tâche

1. Dans la page **tâches** , afficher l’ou les travaux que vous intéressent en exécution d’une requête avec les filtres appropriés. Vous pouvez rechercher de complété, en cours d’exécution, ou annulé travaux.

2. Sélectionnez une tâche.

3. Dans la partie inférieure de la page, cliquez sur **Détails**.

4. Dans la boîte de dialogue **Détails de la tâche sauvegarde** , vous pouvez afficher l’état, les détails, statistiques de temps et les statistiques de données.

## <a name="cancel-a-job"></a>Annulation d’une tâche

Procédez comme suit pour annuler une tâche en cours d’exécution.

### <a name="to-cancel-a-job"></a>Pour annuler une tâche

1. Dans la page **tâches** , afficher l’ou les travaux en cours d’exécution que vous souhaitez annuler en exécution d’une requête avec les filtres appropriés.

1. Sélectionnez la tâche.

1. Dans la partie inférieure de la page, cliquez sur **Annuler**.

1. Lorsque vous êtes invité à confirmer, cliquez sur **Oui**.

Cette tâche est maintenant annulée.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [gérer vos stratégies de sauvegarde StorSimple](storsimple-manage-backup-policies.md).

- Découvrez comment [utiliser le service du Gestionnaire de StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).
