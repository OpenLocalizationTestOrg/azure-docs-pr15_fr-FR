<properties 
   pageTitle="Gestionnaire d’instantanés StorSimple et volumes | Microsoft Azure"
   description="Décrit comment utiliser le composant logiciel enfichable MMC Gestionnaire d’instantanés StorSimple pour afficher et gérer des volumes et pour configurer des sauvegardes."
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

# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>Gestionnaire d’instantanés StorSimple permet d’afficher et gérer des volumes

## <a name="overview"></a>Vue d’ensemble

Vous pouvez utiliser le nœud Gestionnaire d’instantanés StorSimple **Volumes** (dans le volet **étendue** ) pour sélectionner les volumes et afficher des informations. Les volumes sont présentés en tant que lecteurs qui correspondent aux volumes chargés par l’hôte. Le nœud **Volumes** affiche les volumes locaux et des types de volume sont pris en charge par StorSimple, y compris les volumes sont détectés au moyen d’iSCSI et un périphérique. 

Pour plus d’informations sur les volumes pris en charge, accédez à la [prise en charge de plusieurs types de volume](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types).

![Liste des volumes dans le volet des résultats](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

Le nœud **Volumes** vous permet d’analyser ou supprimer des volumes après que gestionnaire d’instantanés StorSimple les découvre. 

Ce didacticiel explique comment vous pouvez monter, initialisation et mettre en forme des volumes et ensuite utiliser le Gestionnaire de capture instantanée StorSimple à :

- Afficher des informations sur les volumes 
- Supprimer des volumes
- Analyser les volumes 
- Configurer un volume de base et le sauvegarder
- Configurer un volume en miroir dynamique et le sauvegarder

>[AZURE.NOTE] Toutes les actions de nœud **Volume** sont également disponibles dans le volet **Actions** .
 
## <a name="mount-volumes"></a>Montage de volumes

Utilisez la procédure suivante pour monter, initialisation et mettre en forme des volumes StorSimple. Cette procédure utilise la gestion des disques, un utilitaire système de gestion des disques durs et les volumes ou partitions correspondante. Pour plus d’informations sur la gestion de disque, accédez à la [Gestion de disque](https://technet.microsoft.com/library/cc770943.aspx) sur le site Web Microsoft TechNet.

#### <a name="to-mount-volumes"></a>Pour charger des volumes

1. Sur votre ordinateur hôte, démarrez Microsoft iSCSI initiator.

2. Fournir une des adresses IP de l’interface en tant que le portail cible ou adresse IP de découverte et vous connecter à l’appareil. Une fois que le périphérique est connecté, les volumes seront accessibles à votre système de Windows. Pour plus d’informations sur l’utilisation de Microsoft iSCSI initiator, accédez à la section « Connexion à un périphérique cible iSCSI » dans [installation et configuration de Microsoft iSCSI Initiator][1].

3. Pour démarrer la gestion de disque, utilisez une des options suivantes :

    - Dans la zone **exécuter** , tapez Diskmgmt.msc.

    - Démarrer le Gestionnaire de serveur, développez le nœud de **stockage** et sélectionnez **Gestion des disques**.

    - Démarrer **Outils d’administration**, développez le nœud de **Gestion de l’ordinateur** et sélectionnez **Gestion des disques**. 

    >[AZURE.NOTE] Vous devez utiliser des privilèges d’administrateur pour exécuter Gestion des disques.
 
4. Mettre l’ou les volumes en ligne :

   1. Dans Gestion du disque, cliquez sur n’importe quel volume marquée **hors connexion**.

   2. Cliquez sur **réactiver le disque**. Une fois le disque réactivé, il doit être marqué **en ligne** .

5. Initialisation l’ou les volumes :

   1. Clic droit sur les volumes découvertes.

   2. Dans le menu, sélectionnez **Initialisation disque**.

   3. Dans la boîte de dialogue **Initialisation disque** , sélectionnez les disques que vous souhaitez initialisation, puis cliquez sur **OK**.

6. Mettre en forme des volumes simples :

   1. Clic droit sur un volume que vous souhaitez mettre en forme.

   2. Dans le menu, sélectionnez **Nouveau Volume Simple**.

   3. Utilisez l’Assistant Nouveau Volume Simple pour mettre en forme le volume :

      - Spécifiez la taille du volume.
      - Fournir une lettre de lecteur.
      - Sélectionnez le système de fichiers NTFS.
      - Spécifiez une taille d’unité d’allocation 64 Ko.
      - Effectuer une mise en forme rapide.

7. Formater les volumes partition multiples. Pour plus d’informations, accédez à la section, « Partitions et Volumes » dans la [Mise en œuvre la gestion de disque](https://msdn.microsoft.com/library/dd163556.aspx).

## <a name="view-information-about-your-volumes"></a>Afficher des informations sur vos volumes

La procédure suivante permet d’afficher des informations sur local et Azure StorSimple volumes.

#### <a name="to-view-volume-information"></a>Pour afficher les informations de volume

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple. 

2. Dans le volet **étendue** , cliquez sur le nœud **Volumes** . Une liste des volumes locaux et chargés, y compris tous les volumes Azure StorSimple, apparaît dans le volet des **résultats** . Les colonnes dans le volet **résultats** peuvent être configurés. (Droit sur le nœud **Volumes** , sélectionnez **affichage**, puis **Ajouter/supprimer des colonnes**.)

    ![Configurer les colonnes](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)

    Colonne de résultats | Description 
    :--------------|:-------------
    Nom           | La colonne **nom** contient la lettre affectée à chaque volume découverte.
    APPAREIL         | La colonne **appareil** contient l’adresse IP de l’appareil connecté à l’ordinateur hôte.
    Nom du Volume appareil | La colonne **Nom du Volume appareil** contienne le nom du volume appareil auquel appartient le volume sélectionné. Il s’agit du nom de volume défini dans le portail classique Azure pour ce volume spécifique.
    Chemins d’accès   | La colonne **Chemins d’accès** affiche le chemin d’accès au volume. Il s’agit du lecteur lettre ou point de montage à laquelle le volume est accessible sur l’ordinateur hôte.
 
## <a name="delete-a-volume"></a>Supprimer un volume

Utilisez la procédure suivante pour supprimer un volume à partir du gestionnaire StorSimple instantané.

>[AZURE.NOTE] Vous ne pouvez pas supprimer un volume s’il fait partie d’un groupe en volume. (L’option de suppression n’est pas disponible pour les volumes qui sont membres d’un groupe de volume). Vous devez supprimer le groupe de volume entier pour supprimer le volume.


#### <a name="to-delete-a-volume"></a>Pour supprimer un volume

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet **étendue** , cliquez sur le nœud **Volumes** . 

3. Dans le volet **résultats** , cliquez sur le volume que vous voulez supprimer.

4. Dans le menu, cliquez sur **Supprimer**. 

    ![Supprimer un volume](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 

5. La boîte de dialogue **Supprimer le Volume** s’affiche. Tapez **Confirmer** dans la zone de texte, puis cliquez sur **OK**.

6. Par défaut, gestionnaire d’instantanés StorSimple sauvegarde un volume avant de le supprimer. Cette mesure peut vous protéger contre la perte de données si la suppression a été accidentelle. Gestionnaire d’instantanés StorSimple affiche un message **Instantané automatique** d’avancement alors qu’il annule le volume. 

    ![Message instantané automatique](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>Analyser les volumes

Utilisez la procédure suivante pour analyser les volumes connectés à StorSimple instantané Manager.

#### <a name="to-rescan-the-volumes"></a>Pour analyser les volumes

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet **étendue** , **Volumes**d’avec le bouton droit, puis cliquez sur **analyser les volumes**.

    ![Analyser les volumes](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
 
    Cette procédure synchronise la liste des volumes avec le Gestionnaire de capture instantanée StorSimple. Toutes les modifications, telles que les nouveaux volumes ou les volumes supprimés, seront reflétées dans les résultats.

## <a name="configure-and-back-up-a-basic-volume"></a>Configurer et sauvegarder un volume de base

Utilisez la procédure suivante pour configurer une sauvegarde d’un volume de base, puis démarrer une sauvegarde immédiatement ou créer une stratégie pour les sauvegardes planifiées.

### <a name="prerequisites"></a>Conditions préalables

Avant de commencer :

- Assurez-vous que l’ordinateur hôte et l’appareil StorSimple sont correctement configurés. Pour plus d’informations, accédez à [déployer votre appareil StorSimple local](storsimple-deployment-walkthrough-u2.md).

- Installez et configurez le Gestionnaire d’instantanés StorSimple. Pour plus d’informations, accédez au [Gestionnaire d’instantanés StorSimple déployer](storsimple-snapshot-manager-deployment.md).

#### <a name="to-configure-backup-of-a-basic-volume"></a>Pour configurer la sauvegarde d’un volume de base

1. Créer un volume de base sur le périphérique StorSimple.

2. Monter, initialisation et mettre en forme le volume comme décrit dans [les volumes de montage](#mount-volumes). 

3. Cliquez sur l’icône Gestionnaire d’instantanés StorSimple sur votre bureau. La fenêtre Gestionnaire d’instantanés StorSimple s’affiche. 

4. Dans le volet **étendue** , cliquez sur le nœud **Volumes** , puis **analyser les volumes**. Lorsque l’analyse est terminée, une liste des volumes doit apparaître dans le volet des **résultats** . 

5. Dans le volet **résultats** , le volume d’avec le bouton droit et sélectionnez **Créer un groupe de Volume**. 

    ![Créer le groupe de volume](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 

6. Dans la boîte de dialogue **Créer un groupe de Volume** , tapez un nom pour le groupe en volume et lui affecter des volumes puis cliquez sur **OK**.

7. Dans le volet de **l’arborescence** , développez le nœud de **Groupes de volumes** . Le nouveau groupe de volumes doit apparaître sous le nœud **Groupes de Volume** . 

8. Cliquez sur le nom du groupe en volume.

    - Pour commencer une opération de sauvegarde (à la demande) interactif, cliquez sur **Prendre la sauvegarde**. 

    - Pour planifier une sauvegarde automatique, cliquez sur **Créer une stratégie de sauvegarde**. Dans la page **Général** , sélectionnez un groupe de volumes dans la liste. Dans la page **planification** , entrez les informations de planification. Lorsque vous avez terminé, cliquez sur **OK**. 

9. Pour vérifier que la sauvegarde a commencé, développez le nœud de **tâches** dans le volet **d’étendue** , puis cliquez sur le nœud **en cours d’exécution** . La liste des tâches en cours d’exécution s’affiche dans le volet des **résultats** . 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>Configurer et sauvegarder un volume en miroir dynamique

Effectuez les étapes suivantes pour configurer la sauvegarde d’un volume en miroir dynamique :

- Étape 1 : Utiliser la gestion des disques pour créer un volume en miroir dynamique. 

- Étape 2 : Utilisez le Gestionnaire d’instantané StorSimple pour configurer la sauvegarde.

### <a name="prerequisites"></a>Conditions préalables

Avant de commencer :

- Assurez-vous que l’ordinateur hôte et l’appareil StorSimple sont correctement configurés. Pour plus d’informations, accédez à [déployer votre appareil StorSimple local](storsimple-deployment-walkthrough-u2.md).

- Installez et configurez le Gestionnaire d’instantanés StorSimple. Pour plus d’informations, accédez au [Gestionnaire d’instantanés StorSimple déployer](storsimple-snapshot-manager-deployment.md).

- Configurer deux volumes sur le périphérique StorSimple. (Dans les exemples, les volumes disponibles sont **disque 1** et **2**.) 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>Étape 1 : Utiliser la gestion des disques pour créer un volume en miroir dynamique

Gestion des disques est un utilitaire système de gestion des disques durs et les volumes ou partitions qu’ils contiennent. Pour plus d’informations sur la gestion de disque, accédez à la [Gestion de disque](https://technet.microsoft.com/library/cc770943.aspx) sur le site Web Microsoft TechNet.

#### <a name="to-create-a-dynamic-mirrored-volume"></a>Pour créer un volume en miroir dynamique

1. Pour démarrer la gestion de disque, utilisez une des options suivantes : 

   - Ouvrir la zone **exécuter** , tapez **Diskmgmt.msc**et appuyez sur ENTRÉE.

   - Démarrer le Gestionnaire de serveur, développez le nœud de **stockage** et sélectionnez **Gestion des disques**. 

   - Démarrer **Outils d’administration**, développez le nœud de **Gestion de l’ordinateur** et sélectionnez **Gestion des disques**. 

2. Vérifiez que vous avez deux volumes disponibles sur le périphérique StorSimple. (Dans l’exemple, les volumes disponibles sont **disque 1** et **2**.) 

3. Dans la fenêtre Gestionnaire de disque, dans la colonne de droite du volet inférieur, avec le bouton droit **disque 1** , puis sélectionnez **Nouveau Volume en miroir**. 

    ![Nouveau Volume en miroir](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 

4. Dans la page de l’Assistant **Nouveau Volume en miroir** , cliquez sur **suivant**.

5. Dans la page **Sélectionner les disques** , sélectionnez **disque 2** dans le volet **sélectionnées** , cliquez sur **Ajouter**, puis sur **suivant**. 

6. Dans la page **attribuer une lettre de lecteur ou chemin d’accès** , acceptez les paramètres par défaut, puis cliquez sur **suivant**. 

7. Dans la page **Formatage de Volume** , dans la zone **Taille d’Allocation unitaire** , sélectionnez **64 Ko**. Activez la case à cocher **effectuer une mise en forme rapide** , puis cliquez sur **suivant**. 

8. Dans la page **fin le nouveau Volume en miroir** , passez en revue vos paramètres, puis cliquez sur **Terminer**. 

9. Un message apparaît pour indiquer que le disque de base sera converti en un disque dynamique. Cliquez sur **Oui**.

    ![Message de conversion de disque dynamique](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 

10. Dans Gestion du disque, vérifiez que le disque 1 et 2 sont représentées par des volumes en miroir dynamiques. (**Dynamique** doit apparaître dans la colonne Statut, et la couleur de la barre capacité doit modifier en rouge, ce qui indique un volume en miroir.) 

    ![Gestion des mises en miroir disque des disques dynamiques](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 
 
### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>Étape 2 : Utilisez le Gestionnaire d’instantané StorSimple pour configurer la sauvegarde

Utilisez la procédure suivante pour configurer un volume en miroir dynamique, puis démarrer une sauvegarde immédiatement ou créer une stratégie pour les sauvegardes planifiées.

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>Pour configurer la sauvegarde d’un volume en miroir dynamique

1. Cliquez sur l’icône Gestionnaire d’instantanés StorSimple sur votre bureau. La fenêtre Gestionnaire d’instantanés StorSimple s’affiche. 

2. Dans le volet **étendue** , le nœud **Volumes** d’avec le bouton droit et sélectionnez **analyser les volumes**. Lorsque l’analyse est terminée, une liste des volumes doit apparaître dans le volet des **résultats** . Le volume en miroir dynamique est répertorié sous forme d’un seul volume. 

3. Dans le volet **des résultats** , avec le bouton droit le volume en miroir dynamique, puis cliquez sur **Créer un groupe de Volume**. 

4. Dans la boîte de dialogue **Créer un groupe de Volume** , tapez un nom pour le groupe de volumes, attribuer le volume en miroir dynamique à ce groupe, puis cliquez sur **OK**. 

5. Dans le volet de **l’arborescence** , développez le nœud de **Groupes de volumes** . Le nouveau groupe de volumes doit apparaître sous le nœud **Groupes de Volume** . 

6. Cliquez sur le nom du groupe en volume. 

    - Pour commencer une opération de sauvegarde (à la demande) interactif, cliquez sur **Prendre la sauvegarde**. 

    - Pour planifier une sauvegarde automatique, cliquez sur **Créer une stratégie de sauvegarde**. Dans la page **Général** , sélectionnez le groupe de volume dans la liste. Dans la page **planification** , entrez les informations de planification. Lorsque vous avez terminé, cliquez sur **OK**. 

7. Vous pouvez surveiller le travail de sauvegarde en cours d’exécution. Dans le volet de **l’arborescence** , développez le nœud de **tâches** , puis sur **en cours d’exécution**, les détails de la tâche apparaissent dans le volet **résultats** . Lorsque la sauvegarde est terminée, les détails sont transférés vers la liste des tâches **dernières 24** heures. 

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [utiliser le Gestionnaire d’instantanés StorSimple pour administrer votre solution StorSimple](storsimple-snapshot-manager-admin.md).
- Découvrez comment [utiliser le Gestionnaire d’instantanés StorSimple pour créer et gérer des groupes de volumes](storsimple-snapshot-manager-manage-volume-groups.md).

<!--Reference links-->
[1]: https://msdn.microsoft.com/library/ee338480(v=ws.10).aspx
