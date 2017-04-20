<properties 
   pageTitle="Actions du menu MMC Gestionnaire d’instantanés StorSimple | Microsoft Azure"
   description="Décrit comment utiliser les actions de menu standard Microsoft Management Console (MMC) dans le Gestionnaire de capture instantanée StorSimple."
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
   ms.date="04/25/2016"
   ms.author="v-sharos" />

# <a name="use-the-mmc-menu-actions-in-storsimple-snapshot-manager"></a>Utiliser les actions de menu MMC dans le Gestionnaire de capture instantanée StorSimple

## <a name="overview"></a>Vue d’ensemble

Dans le Gestionnaire d’instantané StorSimple, vous verrez les actions suivantes sur tous les menus action et de toutes les variantes du volet **Actions** . 

- Affichage
- Nouvelle fenêtre à partir de cet emplacement 
- Actualiser 
- Liste d’exportation 
- Aide 

Ces actions font partie de Microsoft Management Console (MMC) et ne sont pas spécifiques au gestionnaire d’instantanés StorSimple. Ce didacticiel décrit ces actions et explique comment utiliser chacun d’eux dans le Gestionnaire de capture instantanée StorSimple.

## <a name="view"></a>Affichage

Vous pouvez utiliser l’option **d’affichage** pour modifier l’affichage du volet **résultats** et pour modifier l’affichage de la fenêtre console. 

#### <a name="to-change-the-results-pane-view"></a>Pour modifier l’affichage du volet résultats

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet **étendue** , avec le bouton droit n’importe quel nœud ou développez le nœud et clic droit sur un élément dans le volet des **résultats** , puis cliquez sur l’option **d’affichage** . 

3. Pour ajouter ou supprimer des colonnes qui s’affichent dans le volet des **résultats** , cliquez sur **Ajout/Suppression de colonnes**. La boîte de dialogue **Ajout/Suppression de colonnes** s’affiche.

    ![Ajouter ou supprimer des colonnes à partir du volet des résultats](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png) 

4. Complétez le formulaire comme suit :

    - Sélectionnez les éléments à partir de la liste colonnes **disponibles** , cliquez sur **Ajouter** pour les ajouter à la liste **colonnes affichées** . 

    - Cliquez sur les éléments dans la liste **colonnes affichées** , puis cliquez sur **Supprimer** pour les supprimer de la liste. 

    - Sélectionner un élément dans la liste colonnes **affichés** et cliquez sur **Déplacer vers le haut** ou **Déplacer vers le bas** pour déplacer l’élément vers le haut ou vers le bas dans la liste. 

    - Cliquez sur **Paramètres par défaut** pour revenir à la configuration du volet **des résultats** par défaut. 

5. Lorsque vous avez terminé vos sélections, cliquez sur **OK**. 

#### <a name="to-change-the-console-window-view"></a>Pour modifier l’affichage de la fenêtre console

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet **étendue** , avec le bouton droit n’importe quel nœud, cliquez sur **affichage**, puis cliquez sur **Personnaliser**. La boîte de dialogue **Personnaliser** s’affiche.

    ![Personnaliser la fenêtre de la console](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png) 

3. Activez ou désactivez les cases à cocher pour afficher ou masquer des éléments dans la fenêtre de la console. Lorsque vous avez terminé vos sélections, cliquez sur **OK**.

## <a name="new-window-from-here"></a>Nouvelle fenêtre à partir de cet emplacement

Vous pouvez utiliser l’option **Nouvelle fenêtre** pour ouvrir une nouvelle fenêtre de console.

#### <a name="to-open-a-new-console-window"></a>Pour ouvrir une nouvelle fenêtre de console

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet **étendue** , avec le bouton droit n’importe quel nœud, puis cliquez sur **Nouvelle fenêtre à partir de cet emplacement**. 

    Une nouvelle fenêtre apparaît, n'indiquant que l’étendue que vous avez sélectionné. Par exemple, si vous cliquez sur le nœud de **Stratégies de sauvegarde** , la nouvelle fenêtre affiche uniquement le nœud de **Stratégies de sauvegarde** dans le volet **d’étendue** et la liste des stratégies de sauvegarde définies dans le volet des **résultats** . Voir l’exemple suivant.

    ![Nouvelle fenêtre à partir de cet emplacement](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png) 
 
## <a name="refresh"></a>Actualiser

Vous pouvez utiliser l’action **Actualiser** pour mettre à jour de la fenêtre de la console.

#### <a name="to-update-the-console-window"></a>Pour mettre à jour de la fenêtre de la console

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet **étendue** , avec le bouton droit n’importe quel nœud ou développez le nœud et clic droit sur un élément dans le volet des **résultats** , puis cliquez sur **Actualiser**. 

## <a name="export-list"></a>Liste d’exportation

Vous pouvez utiliser l’action **Exporter la liste** pour enregistrer une liste dans un fichier de valeurs séparées par des virgules (CSV). Par exemple, vous pouvez exporter la liste des stratégies de sauvegarde ou le catalogue de sauvegarde. Vous pouvez ensuite importer le fichier CSV dans une application de feuille de calcul pour l’analyse.

#### <a name="to-save-a-list-in-a-comma-separated-value-csv-file"></a>Pour enregistrer une liste dans un fichier de valeurs séparées par des virgules (CSV)

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple. 

2. Dans le volet **étendue** , avec le bouton droit n’importe quel nœud ou développez le nœud et clic droit sur un élément dans le volet des **résultats** , puis cliquez sur **Exporter la liste**. 

3. La boîte de dialogue **Exporter la liste** s’affiche. Complétez le formulaire comme suit : 

    1. Dans la zone **nom de fichier** , tapez un nom pour le fichier .csv ou cliquez sur la flèche pour sélectionner dans la liste déroulante.

    2. Dans la zone **Enregistrer en tant que type** , cliquez sur la flèche et sélectionnez un type de fichier dans la liste déroulante.

    3. Pour enregistrer uniquement les éléments sélectionnés, sélectionnez les lignes, puis sur la case à cocher **Enregistrer uniquement les lignes sélectionnées** . Pour enregistrer exportés toutes les listes, désactivez la case à cocher **Enregistrer uniquement les lignes sélectionnées** .

    4. Cliquez sur **Enregistrer**.

    ![Liste d’exportation en tant qu’un fichier de valeurs séparées par des virgules](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png) 
 
## <a name="help"></a>Aide

Vous pouvez utiliser le menu **d’aide** pour afficher l’aide en ligne disponibles pour le responsable de capture instantanée StorSimple et la console MMC.

#### <a name="to-view-available-online-help"></a>Pour afficher l’aide en ligne disponibles

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet **étendue** , avec le bouton droit n’importe quel nœud ou développez le nœud et clic droit sur un élément dans le volet des **résultats** , puis cliquez sur **aide**. 

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur l' [interface utilisateur du Gestionnaire de capture instantanée StorSimple](storsimple-use-snapshot-manager.md).
- En savoir plus sur [l’utilisation de gestionnaire d’instantanés StorSimple pour administrer votre solution StorSimple](storsimple-snapshot-manager-admin.md).
