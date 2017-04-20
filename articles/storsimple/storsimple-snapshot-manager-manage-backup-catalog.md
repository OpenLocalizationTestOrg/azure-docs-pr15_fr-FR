<properties 
   pageTitle="Catalogue de sauvegarde Gestionnaire d’instantanés StorSimple | Microsoft Azure"
   description="Décrit comment utiliser le composant logiciel enfichable MMC Gestionnaire d’instantanés StorSimple pour afficher et gérer le catalogue de sauvegarde."
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

# <a name="use-storsimple-snapshot-manager-to-manage-the-backup-catalog"></a>Utiliser StorSimple instantané Manager pour gérer le catalogue de sauvegarde

## <a name="overview"></a>Vue d’ensemble

La fonction principale du Gestionnaire de capture instantanée StorSimple consiste à vous permettent de créer des copies de sauvegarde cohérentes avec les applications de volumes StorSimple sous la forme d’instantanés. Instantanés sont répertoriés dans un fichier XML appelé un *catalogue de sauvegarde*. Le catalogue de sauvegarde organise instantanés par groupe de volume, puis par instantané local ou un instantané de cloud. 

Ce didacticiel décrit comment vous pouvez utiliser le nœud de **Catalogue de sauvegarde** à effectuer les tâches suivantes :

- Restaurer un volume 
- Dupliquer un volume ou un groupe de volume 
- Supprimer une sauvegarde 
- Récupérer un fichier
- Restaurer la base de données Gestionnaire d’instantanés Storsimple

Vous pouvez afficher le catalogue de sauvegarde par développer le nœud de **Catalogue de sauvegarde** dans le volet **d’étendue** et puis développer le groupe en volume.

- Si vous cliquez sur le nom du groupe en volume, le volet des **résultats** affiche le nombre d’instantanés locaux et cloud instantanés disponibles pour le groupe de volume. 

- Si vous cliquez sur **Instantané Local** ou un **Instantané de Cloud**, le volet **résultats** affiche les informations suivantes sur chaque instantané sauvegarde (selon vos paramètres **d’affichage** ) : 

    - **Nom** – la durée de la capture instantanée. 

    - **Type** – s’il s’agit d’un instantané local ou un instantané de cloud. 

    - **Propriétaire** – le propriétaire du contenu. 

    - **Disponible** – si l’instantané est actuellement disponible. **True** indique que l’instantané n’est disponible et peut être restauré ; **La valeur False** indique que l’instantané n’est plus disponible. 

    - **Importés** – si la sauvegarde a été importée. **La valeur True** indique que la sauvegarde a été importée à partir du service Manager StorSimple au moment où que le périphérique a été configuré dans le Gestionnaire de capture instantanée StorSimple ; **La valeur False** indique qu’il n'a pas été importé, mais a été créé par le Gestionnaire de capture instantanée StorSimple. (Vous pouvez facilement identifier un groupe de volume importé, car un suffixe est ajouté qui identifie le périphérique à partir duquel le groupe volume a été importé.)

    ![Catalogue de sauvegarde](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)

- Si vous développez **Instantané Local** ou un **Instantané de Cloud**, puis cliquez sur un nom instantané individuels, le volet **résultats** affiche les informations suivantes sur l’instantané que vous avez sélectionné :

    - **Nom** – le volume identifié par une lettre de lecteur. 

    - **Nom local** – le nom local du lecteur (le cas échéant). 

    - **Appareil** – le nom de l’appareil sur lequel se trouve le volume. 

    - **Disponible** – si l’instantané est actuellement disponible. **True** indique que l’instantané n’est disponible et peut être restauré ; **La valeur False** indique que l’instantané n’est plus disponible. 


## <a name="restore-a-volume"></a>Restaurer un volume

Utilisez la procédure suivante pour restaurer un volume de sauvegarde.

#### <a name="prerequisites"></a>Conditions préalables

Si vous ne le n'avez pas déjà fait, créer un groupe de volume et le volume et supprimez ensuite le volume. Par défaut, gestionnaire d’instantanés StorSimple sauvegarde un volume avant d’autoriser le point d’être supprimée. Cette précautions éviter les pertes de données si le volume est supprimé involontairement ou si les données doivent être récupérées pour une raison quelconque. 

Gestionnaire d’instantanés StorSimple affiche le message suivant lors de la création de la sauvegarde préventive.

![Message instantané automatique](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png) 

>[AZURE.IMPORTANT]Vous ne pouvez pas supprimer un volume qui fait partie d’un groupe de volumes. L’option de suppression n’est pas disponible. <br>

#### <a name="to-restore-a-volume"></a>Pour restaurer un volume

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple. 

2. Dans le volet de **l’arborescence** , développez le nœud de **Catalogue de sauvegarde** , développer un groupe de volume, puis cliquez sur **Local instantanés** ou **Des instantanés Cloud**. Une liste d’instantanés de sauvegarde s’affiche dans le volet des **résultats** . 

3. Recherchez la sauvegarde que vous souhaitez restaurer, avec le bouton droit, puis cliquez sur **restaurer**. 

    ![Restaurer le catalogue de sauvegarde](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png) 

4. Dans la page de confirmation, vérifiez les détails, tapez **Confirmer**, puis cliquez sur **OK**. Gestionnaire d’instantanés StorSimple utilise la sauvegarde pour restaurer le volume. 

    ![Restaurer le message de confirmation](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png) 

5. Vous pouvez surveiller l’action en cours d’exécution. Dans le volet de **l’arborescence** , développez le nœud de **tâches** , puis cliquez sur **en cours d’exécution**. Les détails de la tâche apparaissent dans le volet des **résultats** . Lorsque le travail de restauration est terminé, les détails d’une tâche sont transférés à la liste des **dernières 24 heures** .

## <a name="clone-a-volume-or-volume-group"></a>Dupliquer un volume ou un groupe de volume

Utilisez la procédure suivante pour créer un doublon (cloner) d’un volume ou un groupe de volumes.

#### <a name="to-clone-a-volume-or-volume-group"></a>Dupliquer un volume ou un groupe de volume

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet de **l’arborescence** , développez le nœud de **Catalogue de sauvegarde** , développer un groupe de volume, puis cliquez sur **Nuage instantanés**. Une liste des sauvegardes apparaît dans le volet des **résultats** .

3. Recherchez le volume ou le groupe de volume que vous voulez dupliquer, avec le bouton droit le volume ou le nom du groupe, puis cliquez sur **cloner**. La boîte de dialogue **Cloner Cloud instantané** apparaît.

    ![Dupliquer un instantané de cloud](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 

4. Complétez la boîte de dialogue **Cloner Cloud instantané** comme suit : 

    1. Dans la zone de texte **nom** , tapez un nom pour le volume cloné. Ce nom apparaît dans le nœud **Volumes** . 

    2. (Facultatif) sélectionnez **lecteur**, puis une lettre dans la liste déroulante. 

    3. (Facultatif) sélectionnez **Dossier (NTFS)**et tapez un chemin d’accès ou cliquez sur Parcourir et sélectionnez un emplacement pour le dossier. 

    4. Cliquez sur **créer**.

5. Une fois le processus de clonage terminé, vous devez initialisation le volume cloné. Démarrer le Gestionnaire de serveur, puis démarrez puis gestion des disques. Pour obtenir des instructions détaillées, voir [charger les volumes](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Après son initialisation, le volume apparaît sous le nœud **Volumes** dans le volet de **l’étendue** . Si vous ne voyez pas le volume indiqué, actualiser la liste des volumes (droit sur le nœud **Volumes** , puis cliquez sur **Actualiser**).

## <a name="delete-a-backup"></a>Supprimer une sauvegarde

Utilisez la procédure suivante pour supprimer un instantané du catalogue de sauvegarde. 

>[AZURE.NOTE]Suppression d’un instantané supprime des données sauvegardées associées à l’instantané. Toutefois, le processus de nettoyage des données à partir du cloud peut prendre un certain temps.<br>
 
#### <a name="to-delete-a-backup"></a>Pour supprimer une sauvegarde

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet de **l’arborescence** , développez le nœud de **Catalogue de sauvegarde** , développer un groupe de volume, puis cliquez sur **Local instantanés** ou **Des instantanés Cloud**. Une liste des instantanés apparaît dans le volet des **résultats** . 

3. Avec le bouton droit de l’instantané à supprimer, puis cliquez sur **Supprimer**.

4. Lorsque le message de confirmation s’affiche, cliquez sur **OK**. 

## <a name="recover-a-file"></a>Récupérer un fichier

Si un fichier est accidentellement supprimé à partir d’un volume, vous pouvez récupérer le fichier à récupération d’un instantané antérieur à la suppression, l’aide de l’instantané pour créer une copie du volume, puis copier le fichier à partir du volume cloné sur le volume d’origine.

#### <a name="prerequisites"></a>Conditions préalables

Avant de commencer, vérifiez que vous disposez d’une sauvegarde actuelle du groupe en volume. Ensuite, supprimez un fichier stocké sur un des volumes de ce groupe en volume. Pour finir, procédez comme suit pour restaurer le fichier supprimé à partir de votre sauvegarde. 

#### <a name="to-recover-a-deleted-file"></a>Récupérer un fichier supprimé

1. Cliquez sur l’icône Gestionnaire d’instantanés StorSimple sur votre bureau. La fenêtre de la console Gestionnaire d’instantanés StorSimple s’affiche. 

2. Dans le volet de **l’arborescence** , développez le nœud de **Catalogue de sauvegarde** , puis naviguez vers un instantané qui contient le fichier supprimé. En règle générale, vous devez sélectionner un instantané qui a été créé juste avant la suppression. 

3. Recherchez le volume que vous voulez dupliquer, avec le bouton droit, puis cliquez sur **cloner**. La boîte de dialogue **Cloner Cloud instantané** apparaît.

    ![Dupliquer un instantané de cloud](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 

4. Complétez la boîte de dialogue **Cloner Cloud instantané** comme suit : 

   1. Dans la zone de texte **nom** , tapez un nom pour le volume cloné. Ce nom apparaît dans le nœud **Volumes** . 

   2. (Facultatif) Sélectionnez **lecteur**, puis une lettre dans la liste déroulante. 

   3. (Facultatif) Sélectionnez **Dossier (NTFS)**et tapez un chemin d’accès ou cliquez sur **Parcourir** et sélectionnez un emplacement pour le dossier. 

   4. Cliquez sur **créer**. 

5. Une fois le processus de clonage terminé, vous devez initialisation le volume cloné. Démarrer le Gestionnaire de serveur, puis démarrez puis gestion des disques. Pour obtenir des instructions détaillées, voir [charger les volumes](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Après son initialisation, le volume apparaît sous le nœud **Volumes** dans le volet de **l’étendue** . 

    Si vous ne voyez pas le volume répertorié, actualiser la liste des volumes (droit sur le nœud **Volumes** , puis cliquez sur **Actualiser**).

6. Ouvrez le dossier NTFS qui contient le volume cloné, développez le nœud **Volumes** et ouvrez le volume cloné. Recherchez le fichier que vous voulez récupérer, puis copiez-le dans le volume principal.

7. Une fois que vous restaurez le fichier, vous pouvez supprimer le dossier NTFS qui contient le volume cloné.

## <a name="restore-the-storsimple-snapshot-manager-database"></a>Restaurer la base de données Gestionnaire d’instantanés StorSimple

Vous devez sauvegarder régulièrement la base de données Gestionnaire d’instantanés StorSimple sur l’ordinateur hôte. Si un incident se produit ou l’ordinateur hôte échoue pour une raison quelconque, restaurez-la à partir de la sauvegarde. Création de la sauvegarde de la base de données est un processus manuel.

#### <a name="to-back-up-and-restore-the-database"></a>Pour sauvegarder et restaurer la base de données

1. Arrêter le Service de gestion des StorSimple Microsoft :

    1. Démarrer le Gestionnaire de serveur.

    2. Dans le tableau de bord Gestionnaire de serveur, dans le menu **Outils** , sélectionnez **Services**.

    3. Dans la fenêtre **Services** , sélectionnez le **Service de gestion des StorSimple Microsoft**.

    4. Dans le volet droit, sous le **Service de gestion des StorSimple Microsoft**, cliquez sur **Arrêter le service**.

2. Sur l’ordinateur hôte, accédez à C:\ProgramData\Microsoft\StorSimple\BACatalog. 

    >[AZURE.NOTE] ProgramData est un dossier masqué.
 
3. Recherchez le fichier XML de catalogue, copiez le fichier et stocker la copie dans un emplacement sécurisé ou dans le cloud. Si l’hôte échoue, vous pouvez utiliser ce fichier de sauvegarde afin de récupérer les stratégies de sauvegarde que vous avez créé dans le Gestionnaire de capture instantanée StorSimple.

    ![Fichier de sauvegarde catalogue StorSimple Azure](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)

4. Redémarrez le Service de gestion des StorSimple Microsoft : 

    1. Dans le tableau de bord Gestionnaire de serveur, dans le menu **Outils** , sélectionnez **Services**.
    
    2. Dans la fenêtre **Services** , sélectionnez le **Service de gestion des StorSimple Microsoft**.

    3. Dans le volet droit, sous le **Service de gestion des StorSimple Microsoft**, cliquez sur **redémarrer le service**.

5. Sur l’ordinateur hôte, accédez à C:\ProgramData\Microsoft\StorSimple\BACatalog. 

6. Supprimez le fichier XML de catalogue et remplacez-le par la version de sauvegarde que vous avez créé. 

7. Cliquez sur l’icône du Gestionnaire d’instantanés StorSimple bureau pour démarrer le Gestionnaire de capture instantanée StorSimple. 

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [l’utilisation de gestionnaire d’instantanés StorSimple pour administrer votre solution StorSimple](storsimple-snapshot-manager-admin.md).
- En savoir plus sur les [flux de travail et des tâches dans le Gestionnaire de capture instantanée StorSimple](storsimple-snapshot-manager-admin.md#storsimple-snapshot-manager-tasks-and-workflows).
