<properties 
   pageTitle="Stratégies de sauvegarde de gestionnaire d’instantanés StorSimple | Microsoft Azure"
   description="Décrit comment utiliser le composant logiciel enfichable MMC Gestionnaire d’instantanés StorSimple pour créer et gérer les stratégies de sauvegarde qui contrôlent les sauvegardes planifiées."
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
   ms.date="05/12/2016"
   ms.author="v-sharos" />

# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>Gestionnaire d’instantanés StorSimple permet de créer et gérer les stratégies de sauvegarde

## <a name="overview"></a>Vue d’ensemble

Une stratégie de sauvegarde crée une planification pour sauvegarder les données d’un volume localement ou dans le cloud. Lorsque vous créez une stratégie de sauvegarde, vous pouvez également spécifier une stratégie de rétention. (Vous pouvez conserver un maximum de 64 instantanés). Pour plus d’informations sur les stratégies de sauvegarde, voir [types de sauvegarde](storsimple-what-is-snapshot-manager.md#backup-type) dans [série 8000 StorSimple : une solution de cloud hybride](storsimple-overview.md).

Ce didacticiel décrit comment :

- Créer une stratégie de sauvegarde 
- Modifier une stratégie de sauvegarde 
- Supprimer une stratégie de sauvegarde 

## <a name="create-a-backup-policy"></a>Créer une stratégie de sauvegarde

La procédure suivante permet de créer une nouvelle stratégie de sauvegarde.

#### <a name="to-create-a-backup-policy"></a>Pour créer une stratégie de sauvegarde

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet **étendue** , cliquez sur **Stratégies de sauvegarde**, puis cliquez sur **Créer une stratégie de sauvegarde**.

    ![Créer une stratégie de sauvegarde](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    Boîte de dialogue **créer une stratégie** s’affiche. 

    ![Créer une stratégie - onglet Général](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)

3. Sous l’onglet **Général** , renseignez les informations suivantes :

   1. Dans la zone de texte **nom** , tapez un nom pour la stratégie.

   2. Dans la zone de texte **groupe de Volume** , tapez le nom du groupe volume associé à la stratégie.

   3. Sélectionnez **l’instantané Local** ou un **instantané de Cloud**.

   4. Sélectionnez le nombre d’instantanés à conserver. Si vous sélectionnez **tout**, 64 instantanés seront conservés (au maximum). 

4. Cliquez sur l’onglet **prévisions** .

    ![Créer une stratégie - onglet Planification](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)

5. Sous l’onglet **planification** , renseignez les informations suivantes : 

   1. Cliquez sur la case à cocher **Activer** pour planifier la sauvegarde suivante.

   2. Sous **paramètres**, sélectionnez **une seule fois**, **quotidienne**, **hebdomadaire**ou **mensuel**. 

   3. Dans la zone de texte **Démarrer** , cliquez sur l’icône calendrier et sélectionnez une date de début.

   4. Sous **Paramètres avancés**, vous pouvez définir des planifications répétition facultatives et une date de fin.

   5. Cliquez sur **OK**.

Après avoir créé une stratégie de sauvegarde, les informations suivantes s’affiche dans le volet **résultats** :

- **Nom** : le nom de la stratégie de sauvegarde.

- **Type** – instantané local ou un instantané de cloud.

- **Groupe de volumes** – le groupe de volume associé à la stratégie.

- **Rétention** – le nombre d’instantanés conservé ; la valeur maximale est 64.

- **Créé** – la date à laquelle cette stratégie a été créée.

- **Enabled** – si la stratégie est actuellement en réalité : **True** indique qu’il est en vigueur ; **La valeur False** indique qu’il n’est pas activée. 

## <a name="edit-a-backup-policy"></a>Modifier une stratégie de sauvegarde

Utilisez la procédure suivante pour modifier une stratégie de sauvegarde existante.

#### <a name="to-edit-a-backup-policy"></a>Pour modifier une stratégie de sauvegarde

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple. 

2. Dans le volet **étendue** , cliquez sur le nœud de **Stratégies de sauvegarde** . Toutes les stratégies de sauvegarde s’affichent dans le volet des **résultats** . 

3. Avec le bouton droit de la stratégie que vous voulez modifier, puis cliquez sur **Modifier**. 

    ![Modifier une stratégie de sauvegarde](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png) 

4. Lorsque la fenêtre **créer une stratégie** s’affiche, entrez vos modifications, puis cliquez sur **OK**. 

## <a name="delete-a-backup-policy"></a>Supprimer une stratégie de sauvegarde

Utilisez la procédure suivante pour supprimer une stratégie de sauvegarde.

#### <a name="to-delete-a-backup-policy"></a>Pour supprimer une stratégie de sauvegarde

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple. 

2. Dans le volet **étendue** , cliquez sur le nœud de **Stratégies de sauvegarde** . Toutes les stratégies de sauvegarde s’affichent dans le volet des **résultats** . 

3. Avec le bouton droit de la stratégie de sauvegarde que vous voulez supprimer, puis cliquez sur **Supprimer**.

4. Lorsque le message de confirmation s’affiche, cliquez sur **Oui**.

    ![Stratégie de sauvegarde confirmation de suppression](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [utiliser le Gestionnaire d’instantanés StorSimple pour administrer votre solution StorSimple](storsimple-snapshot-manager-admin.md).
- Découvrez comment [utiliser le Gestionnaire d’instantanés StorSimple pour afficher et gérer les travaux de sauvegarde](storsimple-snapshot-manager-manage-backup-jobs.md).
