<properties 
   pageTitle="Restaurer un volume StorSimple à partir de sauvegarde | Microsoft Azure"
   description="Explique comment utiliser la page du catalogue de sauvegarde service Manager StorSimple restaurer un volume StorSimple à partir d’un jeu de sauvegarde."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>Restaurer un volume StorSimple à partir d’un jeu de sauvegarde

[AZURE.INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## <a name="overview"></a>Vue d’ensemble

La page du **Catalogue de sauvegarde** affiche tous les jeux de sauvegarde créés lorsque les sauvegardes manuelles ou automatiques sont effectuées. Vous pouvez utiliser cette page pour toutes les sauvegardes pour une stratégie de sauvegarde ou un volume, sélectionnez la liste ou supprimer des sauvegardes, ou utiliser une sauvegarde à restaurer ou cloner un volume.

 ![Page de catalogue pour la sauvegarde](./media/storsimple-restore-from-backup-set/HCS_BackupCatalog.png)

Ce didacticiel décrit l’utilisation de la page du **Catalogue de sauvegarde** à restaurer un volume sur votre appareil à partir d’un jeu de sauvegarde.

## <a name="how-to-use-the-backup-catalog"></a>Comment utiliser le catalogue de sauvegarde 

La page du **Catalogue de sauvegarde** fournit une requête qui vous permet d’affiner votre sauvegarde définie la sélection. Vous pouvez filtrer les jeux de sauvegarde qui sont récupérés selon les paramètres suivants :

- **Appareil** – l’appareil sur lequel le jeu de sauvegarde a été créé.
- **Stratégie de sauvegarde** ou du **volume** – la stratégie de sauvegarde ou le volume associé à ce jeu de sauvegarde.
- **À partir** d’et **à** – la plage de date et heure lors de la création du jeu de sauvegarde.

Les jeux de sauvegarde filtrées sont ensuite sous forme de tableau basé sur les attributs suivants :

- **Nom** : le nom de la stratégie de sauvegarde ou le volume associé le jeu de sauvegarde.
- **Taille** – la taille réelle du jeu de sauvegarde.
- **Créé le** – la date et l’heure auxquelles les sauvegardes ont été créés. 
- **Type** – des jeux de sauvegarde peuvent être instantanés locales ou instantanés le cloud. Un instantané local est une sauvegarde de toutes vos données en volume stockées localement sur le périphérique, tandis qu’un instantané de cloud fait référence à la sauvegarde des données d’un volume qui résident dans le cloud. Instantanés locales fournissent un accès plus rapide, alors que le cloud instantanés sont choisis pour la résilience des données.
- **Initié par** – les sauvegardes peuvent être lancées automatiquement selon une planification ou manuellement par un utilisateur. (Vous pouvez utiliser une stratégie de sauvegarde pour planifier des sauvegardes. Par ailleurs, vous pouvez utiliser l’option **prendre sauvegarde** pour effectuer une sauvegarde interactive.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Comment faire pour restaurer le volume de votre StorSimple à partir d’une sauvegarde

Vous pouvez utiliser la page du **Catalogue de sauvegarde** à restaurer le volume de votre StorSimple à partir d’une sauvegarde spécifique. 

> [AZURE.WARNING] Restaurer à partir d’une sauvegarde remplacent les volumes existants à partir de la sauvegarde. Cela peut entraîner la perte de données qui ont été écrites après que la sauvegarde a été effectuée.

Avant de lancer une restauration sur un volume, vérifiez que le volume est en mode hors connexion. Vous devez prendre le volume en mode hors connexion sur l’hôte première et puis le périphérique. Suivez les étapes de [prendre un volume en mode hors connexion](storsimple-manage-volumes.md#take-a-volume-offline). Procédez comme suit pour restaurer un volume à partir d’un jeu de sauvegarde.

### <a name="to-restore-from-a-backup-set"></a>Restaurer à partir d’un jeu de sauvegarde

1. Dans la page service StorSimple Manager, cliquez sur l’onglet **catalogue de sauvegarde** .

    ![Catalogue de sauvegarde](./media/storsimple-restore-from-backup-set/HCS_Restore.png)

2. Sélectionnez une sauvegarde définie comme suit :
  1. Sélectionnez le périphérique approprié.
  2. Dans la liste déroulante, sélectionnez la stratégie de sauvegarde ou volume pour la sauvegarde que vous souhaitez sélectionner.
  3. Spécifiez la plage horaire.
  4. Cliquez sur l’icône de vérification ![vérifier l’icône](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png) Pour exécuter cette requête.
 
    Stratégie de sauvegarde doit apparaître dans la liste des jeux de sauvegarde ou les sauvegardes associés avec le volume sélectionné.

3. Développez le jeu pour afficher les volumes associés de sauvegarde. Ces volumes doivent être prises en mode hors connexion sur l’hôte et le périphérique avant de les restaurer. Suivez les étapes de [prendre un volume en mode hors connexion](storsimple-manage-volumes.md#take-a-volume-offline).

    >  [AZURE.IMPORTANT] Vérifiez que vous avez bien suivi les volumes en mode hors connexion sur l’hôte tout d’abord, avant de suivre les volumes en mode hors connexion sur le périphérique. Si vous ne prenez pas les volumes en mode hors connexion sur l’hôte, il peut entraîner une altération des données.

4. Sélectionnez un jeu de sauvegarde. Cliquez sur **restaurer** en bas de la page.

6. Vous demandera confirmation. 

    ![Page de confirmation](./media/storsimple-restore-from-backup-set/HCS_ConfirmRestore.png)

7. Passez en revue les informations de restauration, puis cliquez sur l’icône ![Vérifiez icône](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png). Cela ouvrira un travail de restauration que vous pouvez afficher en accédant à la page **tâches** . 

8. Une fois la restauration terminée, vous pouvez vérifier que le contenu de vos volumes est remplacé par les volumes de la sauvegarde.

![Vidéo disponible](./media/storsimple-restore-from-backup-set/Video_icon.png) **vidéo disponible**

Pour visionner une vidéo qui montre comment vous pouvez utiliser le cloner et restaurer des fonctionnalités dans StorSimple pour récupérer des fichiers supprimés, cliquez sur [ici](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [Gérer StorSimple volumes](storsimple-manage-volumes.md).

- Découvrez comment [utiliser le service du Gestionnaire de StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).
