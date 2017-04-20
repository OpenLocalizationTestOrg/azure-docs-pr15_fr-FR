<properties 
   pageTitle="Gérer votre catalogue de sauvegarde StorSimple | Microsoft Azure"
   description="Explique comment utiliser la page du catalogue de sauvegarde service Manager StorSimple à la liste, sélectionnez et supprimer des jeux de sauvegarde d’un volume."
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
   ms.date="04/28/2016"
   ms.author="v-sharos" />

# <a name="use-the-storsimple-manager-service-to-manage-your-backup-catalog"></a>Utiliser le service StorSimple Manager pour gérer votre catalogue de sauvegarde

## <a name="overview"></a>Vue d’ensemble

La page du **Catalogue de sauvegarde** service Manager StorSimple affiche tous les jeux de sauvegarde créés lors de la sauvegarde manuelle ou planifiée. Vous pouvez utiliser cette page pour toutes les sauvegardes pour une stratégie de sauvegarde ou un volume, sélectionnez la liste ou supprimer des sauvegardes, ou utiliser une sauvegarde à restaurer ou cloner un volume.

Ce didacticiel explique la liste, sélectionnez et supprimer un jeu de sauvegarde. Pour savoir comment restaurer votre appareil à partir de la sauvegarde, accédez à [restaurer votre appareil à partir d’un jeu de sauvegarde](storsimple-restore-from-backup-set.md). Pour apprendre à cloner un volume, accédez à [cloner un volume StorSimple](storsimple-clone-volume.md).

![Catalogue de sauvegarde](./media/storsimple-manage-backup-catalog/backupcatalog.png) 

La page du **Catalogue de sauvegarde** fournit une requête afin d’affiner votre sauvegarde définie la sélection. Vous pouvez filtrer les jeux de sauvegarde qui sont récupérés, selon les paramètres suivants :

- **Appareil** – l’appareil sur lequel le jeu de sauvegarde a été créé.

- **Stratégie de sauvegarde ou du Volume** – la stratégie de sauvegarde ou le volume associé à ce jeu de sauvegarde.

- **Du et au** – la plage de date et heure lors de la création du jeu de sauvegarde.

Les jeux de sauvegarde filtrées sont ensuite sous forme de tableau basé sur les attributs suivants :

- **Nom** : le nom de la stratégie de sauvegarde ou le volume associé le jeu de sauvegarde.

- **Taille** – la taille réelle du jeu de sauvegarde.

- **Créé le** : date et heure auxquelles les sauvegardes ont été créés. 

- **Type** – des jeux de sauvegarde peuvent être instantanés locales ou instantanés le cloud. Un instantané local est une sauvegarde de toutes vos données en volume stockées localement sur le périphérique, tandis qu’un instantané de cloud fait référence à la sauvegarde des données d’un volume qui résident dans le cloud. Instantanés locales fournissent un accès plus rapide, alors que le cloud instantanés sont choisis pour la résilience des données.

- **Initié par** – les sauvegardes peuvent être effectués automatiquement par une planification ou manuellement par un utilisateur. Vous pouvez utiliser une stratégie de sauvegarde pour planifier des sauvegardes. Par ailleurs, vous pouvez utiliser l’option **prendre sauvegarde** pour effectuer une sauvegarde manuelle.

## <a name="list-backup-sets-for-a-volume"></a>Jeux de sauvegarde de la liste pour un volume
 
Effectuez les étapes suivantes pour toutes les sauvegardes pour un volume de la liste.

#### <a name="to-list-backup-sets"></a>Pour les jeux de sauvegarde de liste

1. Dans la page service StorSimple Manager, cliquez sur l’onglet **catalogue de sauvegarde** .

2. Filtrer les sélections comme suit :

    1. Sélectionnez le périphérique approprié.

    2. Dans la liste déroulante, choisissez un volume pour afficher les sauvegardes correspondant.

    3. Spécifiez la plage horaire.

    4. Cliquez sur l’icône de vérification ![Icône de vérification](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) Pour exécuter cette requête.
 
    Les sauvegardes associées au volume sélectionné doivent apparaître dans la liste des jeux de sauvegarde.

## <a name="select-a-backup-set"></a>Sélectionnez un jeu de sauvegarde

Effectuez les étapes suivantes pour sélectionner un jeu pour un volume ou d’une stratégie de sauvegarde de sauvegarde.

#### <a name="to-select-a-backup-set"></a>Pour sélectionner un jeu de sauvegarde

1. Dans la page service StorSimple Manager, cliquez sur l’onglet **catalogue de sauvegarde** .

2. Filtrer les sélections comme suit :

    1. Sélectionnez le périphérique approprié.

    2. Dans la liste déroulante, sélectionnez la stratégie de sauvegarde ou volume pour la sauvegarde que vous souhaitez sélectionner.

    3. Spécifiez la plage horaire.

    4. Cliquez sur l’icône de vérification ![Icône de vérification](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) Pour exécuter cette requête.

    Stratégie de sauvegarde doit apparaître dans la liste des jeux de sauvegarde ou les sauvegardes associés avec le volume sélectionné.

3. Sélectionnez et développez un jeu de sauvegarde. Les options **restaurer** et **Supprimer** sont affichées dans la partie inférieure de la page. Vous pouvez effectuer une des actions suivantes sur le jeu de sauvegarde que vous avez sélectionné.

## <a name="delete-a-backup-set"></a>Supprimer un jeu de sauvegarde

Supprimer une sauvegarde lorsque vous ne souhaitez plus conserver les données qui lui est associées. Procédez comme suit pour supprimer un jeu de sauvegarde.

#### <a name="to-delete-a-backup-set"></a>Pour supprimer un jeu de sauvegarde

1. Dans la page de service StorSimple Manager, cliquez sur l' **onglet catalogue de sauvegarde**.

2. Filtrer les sélections comme suit :

    1. Sélectionnez le périphérique approprié.

    2. Dans la liste déroulante, sélectionnez la stratégie de sauvegarde ou volume pour la sauvegarde que vous souhaitez sélectionner.

    3. Spécifiez la plage horaire.

    4. Cliquez sur l’icône de vérification ![Icône de vérification](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) Pour exécuter cette requête.

    Stratégie de sauvegarde doit apparaître dans la liste des jeux de sauvegarde ou les sauvegardes associés avec le volume sélectionné.

3. Sélectionnez et développez un jeu de sauvegarde. Les options **restaurer** et **Supprimer** sont affichées dans la partie inférieure de la page. Cliquez sur **Supprimer**.

4. Vous serez informé lorsque la suppression est en cours et après avoir terminé avec succès. Une fois la suppression terminée, actualiser la requête dans cette page. Le jeu de sauvegarde supprimé n’apparaît plus dans la liste des jeux de sauvegarde.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [utiliser le catalogue de sauvegarde pour restaurer votre appareil à partir d’un jeu de sauvegarde](storsimple-restore-from-backup-set.md).

- Découvrez comment [utiliser le service du Gestionnaire de StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).
