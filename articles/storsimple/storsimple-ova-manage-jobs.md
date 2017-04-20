<properties 
   pageTitle="Afficher et gérer les travaux de tableau virtuel StorSimple | Microsoft Azure"
   description="Décrit la page de travaux service Manager StorSimple et comment l’utiliser pour effectuer le suivi des tâches récentes et en cours pour le tableau virtuel StorSimple."
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
   ms.workload="na"
   ms.date="06/07/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>Utiliser le service StorSimple gestionnaire pour afficher les tâches pour le tableau virtuel StorSimple

## <a name="overview"></a>Vue d’ensemble

La page **tâches** fournit un portail central unique pour afficher et gérer les travaux qui est démarrées sur tableaux virtuels (également appelé les périphériques virtuels en local) qui sont connectés à votre service Manager StorSimple. Vous pouvez afficher les tâches en cours d’exécution, terminés et échecs pour plusieurs appareils virtuels. Résultats sont présentés dans un format tabulaire. 

![Page de tâches](./media/storsimple-ova-manage-jobs/ovajobs1.png)

Vous pouvez trouver rapidement les tâches qui que vous intéressent en filtrant sur les champs tels que :

- **État** – vous pouvez rechercher des tâches en cours d’exécution, terminés ou échecs tous.
- **Du et au** – tâches peuvent être filtrées en fonction de la plage de date et heure.
- **Type** – le type de tâche peut être all, sauvegarde, de restauration, basculement, téléchargez les mises à jour ou installer les mises à jour.
- **Appareils** – travaux est initiés sur un appareil spécifique connecté à votre service. Les tâches filtrées sont ensuite sous forme de tableau sur la base les attributs suivants :

    - **Type** – le type de tâche peut être all, sauvegarde, de restauration, basculement, téléchargez les mises à jour ou installer les mises à jour.

    - **État** – can travaux être tout, en cours d’exécution, terminés ou a échoué.

    - **Entité** – les tâches peuvent être associés à un volume, le partage ou appareil. 

    - **Appareil** – le nom de l’appareil sur lequel le travail a été démarré.

    - **Mise en route sur** – l’heure de début de la tâche.

    - **L’avancement** – le pourcentage d’achèvement d’une tâche en cours d’exécution. Pour une tâche terminée, cela doit toujours être 100 %.

La liste des tâches est actualisée toutes les 30 secondes.

## <a name="view-job-details"></a>Afficher les détails de projet

Procédez comme suit pour afficher les détails de n’importe quelle tâche.

#### <a name="to-view-job-details"></a>Pour afficher les détails d’une tâche

1. Dans la page **tâches** , afficher l’ou les travaux que vous intéressent en exécution d’une requête avec les filtres appropriés. Vous pouvez rechercher des tâches terminées ou en cours d’exécution.

2. Sélectionnez une tâche à partir de la liste des tâches sous forme de tableau.

3. Dans la partie inférieure de la page, cliquez sur **Détails**.

4. Dans la boîte de dialogue **Détails** , vous pouvez afficher l’état, les détails et les statistiques de temps. L’illustration suivante montre un exemple de la boîte de dialogue **Détails de la tâche sauvegarde** .
 
    ![Page de détails de projet](./media/storsimple-ova-manage-jobs/ovajobs2.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>Échecs des tâches lorsque la machine virtuelle est suspendue dans l’hyperviseur

Lorsqu’une tâche est en progression de votre tableau virtuel StorSimple et le périphérique (machine virtuelle sa mise en service en hyperviseur) est mis en pause de plus de 15 minutes, le travail échoue. Ceci en raison de votre temps tableau virtuel StorSimple est pas synchronisé avec l’heure de Microsoft Azure. Dans la capture d’écran suivante montre pour un échec de travail restaurer.

![Restaurer une tâche a échoué](./media/storsimple-ova-manage-jobs/restorejobfailure.png)

Ces échecs seront appliquent aux travaux de sauvegarde, restauration, mise à jour et basculement. Si votre ordinateur virtuel n’est configuré dans Hyper-V, l’ordinateur synchronisera finit par heure avec votre hyperviseur. Une fois que cela se produit, vous pouvez redémarrer votre travail. 

## <a name="next-steps"></a>Étapes suivantes

[Découvrez comment utiliser l’interface utilisateur pour administrer votre tableau virtuel StorSimple web local](storsimple-ova-web-ui-admin.md).
