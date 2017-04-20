<properties
   pageTitle="Restaurer à partir d’une sauvegarde de votre tableau virtuel StorSimple"
   description="Découvrez comment restaurer une sauvegarde de votre tableau virtuel StorSimple."
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
   ms.workload="NA"
   ms.date="06/07/2016"
   ms.author="alkohli"/>

# <a name="restore-from-a-backup-of-your-storsimple-virtual-array"></a>Restaurer à partir d’une sauvegarde de votre tableau virtuel StorSimple

## <a name="overview"></a>Vue d’ensemble 

Cet article s’applique à la version officielle (disponibilité générale) mars 2016 en cours d’exécution de Microsoft Azure StorSimple tableau virtuel (également connu sous le périphérique virtuel StorSimple local ou un périphérique virtuel StorSimple) ou version ultérieure. Cet article décrit étape par étape comment restaurer à partir d’un jeu de sauvegarde de vos partages ou volumes dans votre tableau virtuel StorSimple. L’article présente également en détail le fonctionne de la récupération au niveau des éléments dans votre tableau virtuel StorSimple qui est configuré comme un serveur de fichiers.


## <a name="restore-shares-from-a-backup-set"></a>Restaurer des actions à partir d’un jeu de sauvegarde


**Avant d’essayer de restaurer des partages, vérifiez que vous disposez de suffisamment d’espace sur le périphérique pour effectuer cette opération.** Pour restaurer à partir d’une sauvegarde, dans le [portail classique Azure](https://manage.windowsazure.com/), procédez comme suit.

#### <a name="to-restore-a-share"></a>Pour restaurer un partage

1.  Accédez à la **sauvegarde du catalogue**. Filtrer par périphérique approprié et laps de temps à rechercher vos sauvegardes. Cliquez sur l’icône ![](./media/storsimple-ova-restore/image1.png) pour exécuter la requête.


1.  Dans la liste des jeux de sauvegarde affichée, cliquez sur, puis sélectionnez une sauvegarde spécifique. Développez la sauvegarde pour afficher les différents partages qu’il contient. Cliquez sur, puis sélectionnez un partage que vous souhaitez restaurer.

2.  Dans la partie inférieure de la page, cliquez sur **restaurer en tant que nouveau**.

3.  Ceci va démarrer l’Assistant **restaurer en tant que nouveau partage** . Dans la page **emplacement et spécifier un nom** :


    1.  Vérifiez le nom du périphérique source. Il s’agit de l’appareil qui contient le partage que vous souhaitez restaurer. La sélection de l’appareil est grisée. Pour sélectionner un périphérique autre source, vous devez quitter l’Assistant et resélectionnez le nouveau jeu de sauvegarde.

    2.  Attribuez un nom de partage. Le nom du partage doit contenir 3 à 127 caractères.

    3.  Passez en revue la taille, type et autorisations associées au partage que vous voulez restaurer. Vous ne pourrez pas modifier les propriétés du partage via l’Explorateur Windows une fois la restauration terminée.

    4.  Cliquez sur l’icône du ![](./media/storsimple-ova-restore/image1.png).

        ![](./media/storsimple-ova-restore/image9.png)

1.  Une fois l’opération de restauration terminée, la restauration démarre et vous verrez une autre notification. Pour surveiller l’avancement de restauration, cliquez sur **Afficher la tâche**. Vous accédez alors à la page **tâches** .

2.  Vous pouvez suivre la progression de la tâche de restauration. Lorsque la restauration est achevée à 100 %, accédez à la page **actions** sur votre appareil.

3.  Vous pouvez à présent afficher le nouveau partage restauré dans la liste d’actions sur votre appareil. Notez que restaurer est effectuée vers le même type du partage. Un partage hiérarchisé est restauré comme hiérarchisé et un partage localement épinglé comme partage localement épinglé.

Vous avez maintenant terminé la configuration de l’appareil et appris sauvegarder ou restaurer un partage. 


## <a name="restore-volumes-from-a-backup-set"></a>Restaurer des volumes à partir d’un jeu de sauvegarde


Pour restaurer à partir d’une sauvegarde, dans le portail classique Azure, procédez comme suit. L’opération de restauration restaure la sauvegarde vers un nouveau volume sur le même appareil virtuel ; Vous ne pouvez pas restaurer vers un autre appareil.

#### <a name="to-restore-a-volume"></a>Pour restaurer un volume

1.  Accédez à la **sauvegarde du catalogue**. Filtrer par périphérique approprié et laps de temps à rechercher vos sauvegardes. Cliquez sur l’icône ![](./media/storsimple-ova-restore/image1.png) pour exécuter la requête.

2.  Dans la liste des jeux de sauvegarde affichée, cliquez sur, puis sélectionnez une sauvegarde spécifique. Développez la sauvegarde pour afficher les différents volumes qu’il contient. Sélectionnez le volume que vous souhaitez restaurer. 

5.  Dans la partie inférieure de la page, cliquez sur **restaurer en tant que nouveau**. L’Assistant **restaurer en tant que nouveau volume** démarre.

1.  Dans la page **emplacement et spécifier un nom** :


    1.  Vérifiez le nom du périphérique source. Il s’agit de l’appareil qui contient le volume que vous souhaitez restaurer. La sélection de l’appareil n’est pas disponible. Pour sélectionner un périphérique autre source, vous devez quitter l’Assistant et resélectionnez le nouveau jeu de sauvegarde.

    2.  Attribuez un nom de volume pour le volume en cours de restauration en tant que nouvelle. Le nom du volume doit contenir 3 à 127 caractères.

    3.  Cliquez sur l’icône de flèche.

        ![](./media/storsimple-ova-restore/image12.png)

1.  Dans la page **spécifier hôtes qui peuvent utiliser ce volume** , sélectionnez les ACRs appropriés dans la liste déroulante.

    ![](./media/storsimple-ova-restore/image13.png)

1.  Cliquez sur l’icône du ![](./media/storsimple-ova-restore/image1.png). Cela ouvrira un travail de restauration et vous verrez la notification suivante le travail est en cours.

2.  Une fois l’opération de restauration terminée, la restauration démarre et vous verrez une autre notification. Pour surveiller l’avancement de restauration, cliquez sur **Afficher la tâche**. Vous accédez alors à la page **tâches** .

3.  Vous pouvez suivre la progression de la tâche de restauration. Accédez à la page **Volumes** sur votre appareil.

4.  Vous pouvez à présent afficher le nouveau volume restauré dans la liste des volumes sur votre appareil. Notez que restaurer est effectuée vers le même type de volume. Un volume hiérarchisé est restauré comme hiérarchisé et un volume localement épinglé est restauré comme un volume localement épinglé.

5.  Une fois que le volume apparaît en ligne sur la liste des volumes, le volume est disponible pour une utilisation.  Sur l’hôte d’initiateur iSCSI, actualisez la liste des cibles dans la fenêtre de propriétés initiateur iSCSI.  Une nouvelle cible qui contient le nom du volume restauré doit apparaître comme 'inactive' sous la colonne État.

6.  Sélectionnez la cible et cliquez sur **se connecter**.   Une fois l’initiateur est connecté à la cible, l’état doit prendre **connecté**. 

7.  Dans la fenêtre de **Gestion des disques** , les volumes montés apparaît comme le montre l’illustration suivante. Avec le bouton droit le volume découvert (cliquez sur le nom du disque), puis cliquez sur **en ligne**.

> [AZURE.IMPORTANT] Lorsque vous essayez de restaurer un volume ou un partage à partir d’une sauvegarde définir, le cas d’échec de la tâche de restauration, un volume cible ou partager peut-être toujours être créé dans le portail. Il est important que vous pouvez supprimer ce volume cible ou partager dans le portail pour réduire les éventuels problèmes futurs découlant de cet élément.

## <a name="item-level-recovery-ilr"></a>Restauration d’au niveau de l’élément (ILR)

Cette version présente la récupération au niveau des éléments (ILR) sur un périphérique virtuel StorSimple configuré comme un serveur de fichiers. La fonctionnalité vous permet de faire granulaire récupération de fichiers et dossiers à partir d’une sauvegarde de cloud de toutes les actions sur le périphérique StorSimple. Les utilisateurs peuvent récupérer des fichiers supprimés à partir de sauvegardes récentes à l’aide d’un modèle de libre-service.

Chaque partage contient un dossier *.backups* qui contient les sauvegardes plus récentes. L’utilisateur peut accéder à la sauvegarde de votre choix, copiez les fichiers appropriés et les dossiers à partir de la sauvegarde et les restaurer. Cela supprime les appels pour les administrateurs pour restaurer des fichiers à partir de sauvegardes.

1.  Lorsque vous effectuez la ILR, vous pouvez afficher les sauvegardes via l’Explorateur Windows. Cliquez sur le partage spécifique que vous souhaitez examiner la sauvegarde pour. Vous verrez un dossier *.backups* créé sous le partage qui stocke toutes les sauvegardes. Développez le dossier *.backups* pour afficher les sauvegardes. Le dossier puis affiche la vue décomposées de toute la hiérarchie de sauvegarde. Cet affichage est créé à la demande et ne prend que quelques secondes pour en créer.

    Les 5 derniers sauvegardes s’affichent de cette manière et peuvent être utilisés pour effectuer une récupération au niveau de l’élément. Les 5 sauvegardes récentes incluent la valeur par défaut planifiée et les sauvegardes manuels.

    
    -   **Des sauvegardes planifiées** nommé en tant que &lt;nom de l’appareil&gt;DailySchedule-AAAAMMJJ-HHMMSS-UTC.

    -   **Sauvegardes manuelles** nommé Ad-hoc-AAAAMMJJ-HHMMSS-UTC.
    
        ![](./media/storsimple-ova-restore/image14.png)

1.  Identifiez la sauvegarde contenant la version la plus récente du fichier supprimé. Bien que le nom du dossier contient un horodatage UTC dans chacun des cas ci-dessus, l’heure à laquelle le dossier a été créé est appareil réel lors du démarrage de la sauvegarde. Utiliser l’horodatage de dossier pour localiser et d’identifier les sauvegardes.

2.  Recherchez le dossier ou le fichier que vous souhaitez restaurer dans la sauvegarde que vous avez identifiés à l’étape précédente. Remarque Vous pouvez afficher uniquement les fichiers ou dossiers que vous disposez d’autorisations. Si vous n’êtes pas en mesure d’accéder à certains fichiers ou dossiers, vous devrez contacter un administrateur de partage qui peut utiliser l’Explorateur Windows pour modifier les autorisations de partage et vous permettent d’accéder au fichier spécifique ou au dossier. Il est recommandé que l’administrateur de partage soit un groupe d’utilisateurs au lieu d’un seul utilisateur.

3.  Copiez le fichier ou le dossier dans le partage approprié sur votre serveur de fichiers StorSimple.

![video_icon](./media/storsimple-ova-restore/video_icon.png) **vidéo disponible**

Regardez la vidéo pour voir comment vous pouvez créer des partages, sauvegarder des actions et restaurer les données dans un tableau virtuel StorSimple.

> [AZURE.VIDEO use-the-storsimple-virtual-array]

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la façon [d’administrer votre tableau virtuel StorSimple à l’aide de l’interface utilisateur web local](storsimple-ova-web-ui-admin.md).
