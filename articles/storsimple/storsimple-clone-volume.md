<properties
   pageTitle="Cloner le volume de votre StorSimple | Microsoft Azure"
   description="Décrit les types différents cloner et quand les utiliser et explique comment vous pouvez utiliser une jeu de sauvegarde à cloner un volume individuel."
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

# <a name="use-the-storsimple-manager-service-to-clone-a-volume"></a>Utiliser le service Manager StorSimple pour cloner un volume

[AZURE.INCLUDE [storsimple-version-selector-clone-volume](../../includes/storsimple-version-selector-clone-volume.md)]

## <a name="overview"></a>Vue d’ensemble

La page du **Catalogue de sauvegarde** service Manager StorSimple affiche tous les jeux de sauvegarde créés lorsque les sauvegardes manuelles ou automatiques sont effectuées. Vous pouvez utiliser cette page pour toutes les sauvegardes pour une stratégie de sauvegarde ou un volume, sélectionnez la liste ou supprimer des sauvegardes, ou utiliser une sauvegarde à restaurer ou cloner un volume.

![Page du catalogue de sauvegarde](./media/storsimple-clone-volume/HCS_BackupCatalog.png)  

Ce didacticiel décrit comment vous pouvez utiliser une jeu de sauvegarde à cloner un volume individuel. Il explique également la différence entre clones *transitoires* et *permanentes* . 

## <a name="create-a-clone-of-a-volume"></a>Créer une copie d’un volume

Vous pouvez créer un cloner sur le même appareil, un autre périphérique ou même une machine virtuelle à l’aide d’une variable locale ou un instantané de cloud.

#### <a name="to-clone-a-volume"></a>Dupliquer un volume

1. Dans la page service StorSimple Manager, cliquez sur l’onglet **catalogue de sauvegarde** , puis sélectionnez un jeu de sauvegarde.

2. Développez le jeu pour afficher les volumes associés de sauvegarde. Cliquez sur et sélectionnez un volume dans le jeu de sauvegarde.

     ![Dupliquer un volume](./media/storsimple-clone-volume/HCS_Clone.png) 

3. Cliquez sur **cloner** pour commencer clonant le volume sélectionné.

4. Dans l’Assistant Volume cloner, sous **emplacement et spécifier un nom**:

  1. Identifier un appareil cible. Il s’agit de l’emplacement où le cloner est créé. Vous pouvez choisir le même appareil ou spécifier un autre périphérique. Si vous choisissez un volume associé à d’autres fournisseurs de services de cloud (pas Azure), la liste déroulante pour le périphérique cible ne montrera que les périphériques physiques. Vous ne pouvez pas cloner un volume associé à d’autres fournisseurs de services de cloud sur un périphérique virtuel.

        >  [AZURE.NOTE] Assurez-vous que la capacité requise pour la cloner est inférieure à la capacité disponible sur le périphérique cible.
  2. Spécifiez un nom de volume unique pour votre cloner. Le nom doit contenir entre 3 et 127 caractères.
  3. Cliquez sur l’icône de flèche ![icône de flèche](./media/storsimple-clone-volume/HCS_ArrowIcon.png) Pour passer à la page suivante.

5. Sous **spécifier hôtes qui peuvent utiliser ce volume**:

  1. Spécifier un enregistrement de contrôle d’accès (blocage) pour la cloner. Vous pouvez ajouter un nouveau blocage ou choisissez dans la liste existante.
  2. Cliquez sur l’icône de vérification ![icône de vérification](./media/storsimple-clone-volume/HCS_CheckIcon.png)Pour terminer l’opération.

6. Une tâche Cloner sera lancée et vous serez averti lorsque la cloner est créé avec succès. Cliquez sur **Afficher la tâche** pour contrôler la tâche Cloner sur la page **tâches** .

7. Une fois la tâche Cloner terminée :

  1. Accédez à la page **de périphériques** , puis sélectionnez l’onglet **Conteneurs en Volume** . 
  2. Sélectionnez le conteneur de volume associé le volume source que vous cloner. Dans la liste des volumes, vous devez voir cloner qui vient d’être créé.

>[AZURE.NOTE] Par défaut et contrôle sauvegarde sont désactivés automatiquement sur un volume cloné.

Un cloner est créée de cette façon est un cloner transitoire. Pour plus d’informations sur les types de cloner, voir [transitoires et clones permanents](#transient-vs.-permanent-clones).

Cette cloner devient un volume régulier, et toute opération possible sur un volume sera disponible pour la cloner. Vous devrez configurer ce volume pour toutes les sauvegardes.

## <a name="transient-vs-permanent-clones"></a>Transitoires et clones permanents

Clones transitoires et permanentes sont créées uniquement lorsque vous êtes clonant à un autre appareil. Vous pouvez cloner un volume spécifique à partir d’un jeu de sauvegarde à un autre appareil. Une copie créée de cette manière est un cloner *transitoires* . La cloner transitoire auront références vers le volume d’origine et utilise ce volume à lire lors de l’écriture localement. 

Une fois que vous effectuez un instantané de cloud d’un cloner transitoire, la cloner résultante sera un cloner *permanent* . La cloner permanent est indépendant et n’a pas toutes les références au volume d’origine qu’il a été dupliquée à partir de.  

## <a name="scenarios-for-transient-and-permanent-clones"></a>Scénarios de clones transitoires et permanents

Les sections suivantes décrivent les situations d’exemple dans lequel clones transitoires et permanents peuvent être utilisés.

### <a name="item-level-recovery-with-a-transient-clone"></a>Restauration au niveau des éléments grâce à un cloner transitoire

Vous devez récupérer un fichier de présentation Microsoft PowerPoint celui ans. Votre administrateur informatique identifie la sauvegarde spécifique à partir de cet intervalle de temps avant de filtre le volume. L’administrateur puis Clone le volume, recherche le fichier que vous recherchez et fournit pour vous. Dans ce scénario, un cloner transitoire est utilisé. 
 
![Vidéo disponible](./media/storsimple-clone-volume/Video_icon.png) **vidéo disponible**

Pour visionner une vidéo qui montre comment vous pouvez utiliser le cloner et restaurer des fonctionnalités dans StorSimple pour récupérer des fichiers supprimés, cliquez sur [ici](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Test dans l’environnement de production avec un cloner permanent

Vous devez vérifier un bogue test dans l’environnement de production. Vous créez un cloner du volume dans l’environnement de production en prendre un instantané de cloud de cette cloner. Le volume cloné est désormais indépendant. Dans ce scénario, un cloner permanent est utilisé.

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment faire pour [restaurer un volume StorSimple à partir d’un jeu de sauvegarde](storsimple-restore-from-backup-set.md).

- Découvrez comment [utiliser le service du Gestionnaire de StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).

 
