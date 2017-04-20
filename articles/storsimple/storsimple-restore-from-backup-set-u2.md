<properties 
   pageTitle="Restaurer un volume StorSimple à partir de sauvegarde | Microsoft Azure"
   description="Explique comment utiliser la page du catalogue de sauvegarde service Manager StorSimple restaurer un volume StorSimple à partir d’un jeu de sauvegarde."
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

# <a name="restore-a-storsimple-volume-from-a-backup-set-update-2"></a>Restaurer un volume StorSimple à partir d’un jeu de sauvegarde (mise à jour 2)

[AZURE.INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## <a name="overview"></a>Vue d’ensemble

La page du **Catalogue de sauvegarde** affiche tous les jeux de sauvegarde créés lorsque les sauvegardes manuelles ou automatiques sont effectuées. Vous pouvez utiliser cette page pour toutes les sauvegardes pour une stratégie de sauvegarde ou un volume, sélectionnez la liste ou supprimer des sauvegardes, ou utiliser une sauvegarde à restaurer ou cloner un volume.

 ![Page de catalogue pour la sauvegarde](./media/storsimple-restore-from-backup-set-u2/restore.png)

Ce didacticiel décrit l’utilisation de la page du **Catalogue de sauvegarde** à restaurer votre appareil à partir d’un jeu de sauvegarde.

Vous pouvez restaurer une sauvegarde de cloud ou le volume à partir d’une variable locale. Dans les deux cas, l’opération de restauration fait apparaître le volume en ligne immédiatement lorsque les données sont téléchargées en arrière-plan. 

Avant de lancer une opération de restauration, vous devez tenir compte des éléments suivants :

- **Que vous devez prendre le volume en mode hors connexion** – prendre le volume en mode hors connexion sur l’hôte et le périphérique avant de lancer l’opération de restauration. Bien que l’opération de restauration donne automatiquement le volume en ligne sur l’appareil, vous devez mettre manuellement le périphérique en ligne sur l’hôte. Vous pouvez porter le volume en ligne sur l’hôte dès que le volume est en ligne sur l’appareil. (Vous n’avez pas besoin patienter jusqu'à ce que vous avez terminée l’opération de restauration.) Pour plus d’informations, accédez à [prendre un volume en mode hors connexion](storsimple-manage-volumes-u2.md#take-a-volume-offline).

- **Type de volume après restauration** – volumes supprimés sont restaurés en fonction du type de l’instantané ; Autrement dit, volumes qui se trouvaient localement sont restaurées en tant que volumes localement épinglés et volumes qui ont été hiérarchisées sont restaurées en tant que volumes hiérarchisés.

    Pour les volumes existants, le type d’utilisation actuelle du volume remplace le type qui est stocké dans l’instantané. Par exemple, si vous restaurez un volume à partir d’un instantané qui a été pris lorsque le type de volume a été hiérarchisé et que ce type de volume est maintenant localement épinglé (en raison d’une opération de conversion qui a été exécutée), le volume restauré comme un volume localement épinglé. De même, si un volume localement épinglé existant a été développé et restauré par la suite à partir d’un instantané plus ancien pris lorsque le volume a été plus petit, le volume restauré conserve la taille de développé actuelle.

    Vous ne pouvez pas convertir un volume à partir d’un volume hiérarchisé vers un volume localement épinglé ou d’un volume localement épinglé vers un volume hiérarchisé tandis que le volume est en cours de restauration. Patientez jusqu'à ce que vous avez terminée l’opération de restauration, puis vous pouvez convertir le volume vers un autre type. Pour plus d’informations concernant la conversion d’un volume, accédez à [Modifier le type de volume](storsimple-manage-volumes-u2.md#change-the-volume-type). 

- **La taille du volume est reflétée dans le volume restauré** – il s’agit d’un facteur important si vous restaurez un volume localement épinglé qui a été supprimé (car localement épinglés volumes sont entièrement sa mise en service). Vérifiez que vous disposez de suffisamment d’espace avant de pouvoir restaurer un volume localement épinglé qui a été supprimé précédemment. 

- **Que vous ne pouvez pas développer un volume pendant qu’il est en cours de restauration** , attendez que la restauration soit terminée avant d’essayer d’étendre le volume. Pour plus d’informations sur le développement d’un volume, accédez à [Modifier un volume](storsimple-manage-volumes-u2.md#modify-a-volume).

- **Vous pouvez effectuer une sauvegarde tandis que vous restaurez un volume local** – pour plus d’informations accédez à [utiliser le service StorSimple Manager pour gérer les stratégies de sauvegarde](storsimple-manage-backup-policies.md).

- **Que vous pouvez annuler une opération de restauration** – si vous annulez le travail de restauration, puis le volume sera restaurée à l’état dans lequel elle se trouvait avant que vous avez démarrées l’opération de restauration. Pour plus d’informations, accédez à [Annuler une tâche](storsimple-manage-jobs-u2.md#cancel-a-job).

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

Vous pouvez utiliser la page du **Catalogue de sauvegarde** à restaurer le volume de votre StorSimple à partir d’une sauvegarde spécifique. N’oubliez pas, toutefois, cette restauration d’un volume reprend le volume à l’état que qui était le sien lors de la sauvegarde a été effectuée. Toutes les données qui a été ajoutées après que l’opération de sauvegarde seront perdue.

> [AZURE.WARNING] Restaurer à partir d’une sauvegarde remplacent les volumes existants à partir de la sauvegarde. Cela peut entraîner la perte de données qui ont été écrites après que la sauvegarde a été effectuée.

### <a name="to-restore-your-volume"></a>Pour restaurer le volume de votre

1. Dans la page service StorSimple Manager, cliquez sur l’onglet **catalogue de sauvegarde** .

    ![Catalogue de sauvegarde](./media/storsimple-restore-from-backup-set-u2/restore.png)

2. Sélectionnez une sauvegarde définie comme suit :
  1. Sélectionnez le périphérique approprié.
  2. Dans la liste déroulante, sélectionnez la stratégie de sauvegarde ou volume pour la sauvegarde que vous souhaitez sélectionner.
  3. Spécifiez la plage horaire.
  4. Cliquez sur l’icône de vérification ![vérifier l’icône](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png) Pour exécuter cette requête.
 
    Stratégie de sauvegarde doit apparaître dans la liste des jeux de sauvegarde ou les sauvegardes associés avec le volume sélectionné.

3. Développez le jeu pour afficher les volumes associés de sauvegarde. Ces volumes doivent être prises en mode hors connexion sur l’hôte et le périphérique avant de les restaurer. Accéder aux volumes dans la page **Volume conteneurs** et puis suivez les étapes de [prendre un volume en mode hors connexion](storsimple-manage-volumes-u2.md#take-a-volume-offline) pour les déconnecter.

    > [AZURE.IMPORTANT] Vérifiez que vous avez bien suivi les volumes en mode hors connexion sur l’hôte tout d’abord, avant de suivre les volumes en mode hors connexion sur le périphérique. Si vous ne prenez pas les volumes en mode hors connexion sur l’hôte, il peut entraîner une altération des données.

4. Accédez à l’onglet **Catalogue de sauvegarde** , puis sélectionnez un jeu de sauvegarde.

5. Cliquez sur **restaurer** en bas de la page.

6. Vous demandera confirmation. Passez en revue les informations de restauration, puis sélectionnez la case à cocher confirmation.

    ![Page de confirmation](./media/storsimple-restore-from-backup-set-u2/ConfirmRestore.png)

7. Cliquez sur l’icône ![Vérifiez icône](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png). Cela ouvrira un travail de restauration que vous pouvez afficher en accédant à la page **tâches** . 

8. Une fois la restauration terminée, vous pouvez vérifier que le contenu de vos volumes est remplacé par les volumes de la sauvegarde.

![Vidéo disponible](./media/storsimple-restore-from-backup-set-u2/Video_icon.png) **vidéo disponible**

Pour visionner une vidéo qui montre comment vous pouvez utiliser le cloner et restaurer des fonctionnalités dans StorSimple pour récupérer des fichiers supprimés, cliquez sur [ici](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

## <a name="if-the-restore-fails"></a>Si la restauration échoue

Si l’opération de restauration échoue pour une raison quelconque, vous recevez une alerte. Dans ce cas, actualisez la sauvegarde de la liste pour vérifier que la sauvegarde est toujours valide. Si la sauvegarde est valide et que vous restaurez à partir du cloud, puis problèmes de connectivité peuvent être à l’origine du problème. 

Pour terminer l’opération de restauration, effectuez le volume en mode hors connexion sur l’hôte et recommencez l’opération de restauration. Notez que les modifications apportées aux données du volume qui ont été effectuées pendant le processus de restauration seront perdues.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [Gérer StorSimple volumes](storsimple-manage-volumes-u2.md).

- Découvrez comment [utiliser le service du Gestionnaire de StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).
