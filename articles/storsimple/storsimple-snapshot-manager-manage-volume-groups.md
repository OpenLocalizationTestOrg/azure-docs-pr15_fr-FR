<properties 
   pageTitle="Groupes de volumes Gestionnaire d’instantanés StorSimple | Microsoft Azure"
   description="Décrit comment utiliser le composant logiciel enfichable MMC Gestionnaire d’instantanés StorSimple pour créer et gérer des groupes de volumes."
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
   ms.date="04/18/2016"
   ms.author="v-sharos" />

# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>Gestionnaire d’instantanés StorSimple permet de créer et gérer des groupes de volume

## <a name="overview"></a>Vue d’ensemble

Vous pouvez utiliser le nœud de **Groupes de volumes** dans le volet **étendue** pour attribuer des volumes aux groupes de volume, afficher les informations relatives à un groupe de volume, planifier des sauvegardes et modifier des groupes de volumes. 

Groupes de volumes sont des pools de volumes apparentés permet de garantir que les sauvegardes sont cohérentes avec les applications. Pour plus d’informations, voir [Volumes et des groupes de volumes](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups) et [l’intégration avec Windows Volume Shadow Copy Service](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

>[AZURE.IMPORTANT] 
>
> * Tous les volumes dans un groupe de volumes doivent provenir d’un fournisseur de services de cloud unique.
> 
> * Lorsque vous configurez des groupes de volumes, ne confondez pas volumes partagés de cluster (CSVs) et non CSVs dans le même groupe en volume. Gestionnaire d’instantanés StorSimple ne reconnaît pas un mélange de CSVs et non CSVs dans le même instantané.
 
![Nœud de groupes de volume](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**Figure 1 : Nœud de groupes de volumes StorSimple instantané Gestionnaire** 

Ce didacticiel vous explique comment utiliser Gestionnaire d’instantanés StorSimple à :

- Afficher des informations sur vos groupes de volume 
- Créer un groupe de volume
- Sauvegarder un groupe de volume
- Modifier un groupe de volume
- Supprimer un groupe de volume

Toutes ces actions sont également disponibles dans le volet **Actions** .
 
## <a name="view-volume-groups"></a>Afficher les groupes de volume

Si vous cliquez sur le nœud **Groupes de Volume** , le volet **résultats** affiche les informations suivantes sur chaque groupe de volume, en fonction de la colonne que vous sélectionnez. (Les colonnes dans le volet **résultats** sont configurables. Clic droit sur le nœud, sélectionnez **View**et puis sélectionnez **Ajouter/supprimer des colonnes** **Volumes** .)

Colonne de résultats | Description 
:--------------|:------------ 
Nom           | La colonne **nom** contienne le nom du groupe en volume.
Application    | La colonne **Applications** indique le nombre de writersVSS actuellement installé et exécuté sur l’hôte de Windows.
Sélectionnée       | La colonne **sélectionné** indique le nombre de volumes qui figurent dans le groupe de volume. Zéro (0) indique qu’aucune application n’est associée aux volumes dans le groupe de volume.
Importé       | La colonne **importés** indique le nombre de volumes importés. Lorsque la valeur **true**, cette colonne indique qu’un groupe de volume a été importé à partir du portail classique Azure et n'a pas été créé dans le Gestionnaire de capture instantanée StorSimple.
 
>[AZURE.NOTE] Groupes de volumes Gestionnaire d’instantanés StorSimple sont également affichées sous l’onglet **Stratégies de sauvegarde** dans le portail classique Azure.
 
## <a name="create-a-volume-group"></a>Créer un groupe de volume

Utilisez la procédure suivante pour créer un groupe en volume.

#### <a name="to-create-a-volume-group"></a>Pour créer un groupe de volume

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple. 

2. Dans le volet **étendue** , avec le bouton droit **En Volume groupes**, puis cliquez sur **Créer un groupe de Volume**. 

    ![Créer le groupe de volume](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
 
    Boîte de dialogue **créer un groupe de volumes** s’affiche. 

    ![Créer une boîte de dialogue du groupe en volume](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png) 

3.  Entrez les informations suivantes : 

    1. Dans la zone **nom** , tapez un nom unique pour le nouveau groupe de volume. 

    2. Dans la zone **Applications** , sélectionnez applications associées les volumes qui seront ajoutés au groupe de volumes. 

        Listes de case à cocher **Applications** uniquement les applications qui utilisent des volumes StorSimple et ont writersVSS activées pour eux. EnregistreurVSS est activé uniquement si tous les volumes connaît le writer sont StorSimple volumes. Si la zone Applications est vide, aucune application qui utilisent des volumes StorSimple Azure et est prises en charge writersVSS n’est installées. (Pour l’instant, StorSimple Azure prend en charge Microsoft Exchange et SQL Server.) Pour plus d’informations sur writersVSS, voir [l’intégration avec Windows Volume Shadow Copy Service](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

        Si vous sélectionnez une application, tous les volumes associés sont automatiquement sélectionnées. En revanche, si vous sélectionnez volumes associés à une application spécifique, l’application est automatiquement sélectionnée dans la zone **d’Applications** . 

    3. Dans la zone **Volumes** , sélectionnez volumes StorSimple à ajouter au groupe de volumes. 

      - Vous pouvez inclure des volumes avec un ou plusieurs partitions. (Plusieurs volumes partition peuvent être des disques dynamiques ou des disques de base avec plusieurs partitions.) Un volume qui contient plusieurs partitions est considéré comme une seule unité. Par conséquent, si vous ajoutez uniquement une des partitions à un groupe de volume, toutes les partitions sont ajoutées automatiquement à ce groupe de volume en même temps. Après avoir ajouté un volume partition plusieurs à un groupe de volume, le volume de partition plusieurs continue à être considéré comme une seule unité.

      - Vous pouvez créer des groupes de volume vide en ne pas leur attribuant tous les volumes. 

      - Ne pas mélanger volumes partagés de cluster (CSVs) et non CSVs dans le même groupe en volume. Gestionnaire d’instantanés StorSimple ne reconnaît pas un mélange des volumes CSV et non-CSV dans le même instantané. 

4. Cliquez sur **OK** pour enregistrer le groupe de volumes.

## <a name="back-up-a-volume-group"></a>Sauvegarder un groupe de volume

Utilisez la procédure suivante pour sauvegarder un groupe de volumes.

#### <a name="to-back-up-a-volume-group"></a>Pour sauvegarder un groupe de volume

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet de **l’arborescence** , développez le nœud de **Groupes de volumes** , avec le bouton droit à un nom de groupe en volume, puis cliquez sur **Prendre une sauvegarde**. 

    ![Sauvegardez immédiatement groupe de volume](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)

3. Dans la boîte de dialogue **Prendre une sauvegarde** , sélectionnez **Instantané Local** ou un **Instantané de Cloud**, puis cliquez sur **créer**. 

    ![Utiliser la boîte de dialogue sauvegarde](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png) 

4. Pour vérifier que la sauvegarde s’exécute, développez le nœud de **tâches** , puis cliquez sur **en cours d’exécution**. La sauvegarde doit être répertoriée.

5. Pour afficher l’instantané terminée, développez le nœud de **Catalogue de sauvegarde** , développez le nom du groupe en volume, puis cliquez sur **Instantané Local** ou un **Instantané de Cloud**. La sauvegarde s’affichent si elle est terminée correctement. 

## <a name="edit-a-volume-group"></a>Modifier un groupe de volume

Utilisez la procédure suivante pour modifier un groupe de volumes.

#### <a name="to-edit-a-volume-group"></a>Pour modifier un groupe de volume

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet de **l’arborescence** , développez le nœud de **Groupes de volumes** , droit sur un nom de groupe en volume, puis cliquez sur **Modifier**. 

3. Boîte de dialogue **créer un groupe de volumes **s’affiche. Vous pouvez modifier les entrées de **nom**, des **Applications**et des **Volumes** . 

4. Cliquez sur **OK** pour enregistrer vos modifications.

## <a name="delete-a-volume-group"></a>Supprimer un groupe de volume

Utilisez la procédure suivante pour supprimer un groupe de volumes. 

>[AZURE.WARNING] Cela supprime également toutes les sauvegardes associés au groupe de volume.

#### <a name="to-delete-a-volume-group"></a>Pour supprimer un groupe de volume

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple. 

2. Dans le volet de **l’arborescence** , développez le nœud de **Groupes de volumes** , droit sur un nom de groupe en volume, puis cliquez sur **Supprimer**. 

3. La boîte de dialogue **Supprimer le groupe de Volume** s’affiche. Tapez **Confirmer** dans la zone de texte, puis cliquez sur **OK**. 

    Le groupe volume supprimé disparaît de la liste dans le volet des **résultats** et toutes les sauvegardes qui sont associés à ce groupe de volume sont supprimés à partir du catalogue de sauvegarde.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [utiliser le Gestionnaire d’instantanés StorSimple pour administrer votre solution StorSimple](storsimple-snapshot-manager-admin.md).
- Découvrez comment [utiliser le Gestionnaire d’instantanés StorSimple pour créer et gérer des règles de sauvegarde](storsimple-snapshot-manager-manage-backup-policies.md).
