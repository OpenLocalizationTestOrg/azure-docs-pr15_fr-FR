<properties 
   pageTitle="Didacticiel sauvegarde tableau virtuel StorSimple | Microsoft Azure"
   description="Décrit comment faire pour sauvegarder tableau virtuel StorSimple partages et des volumes."
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
   ms.date="06/07/2016"
   ms.author="alkohli" />

# <a name="back-up-your-storsimple-virtual-array"></a>Sauvegarder votre tableau virtuel StorSimple

## <a name="overview"></a>Vue d’ensemble 

Ce didacticiel s’applique à la version de Microsoft Azure StorSimple tableau virtuel (également connu sous le périphérique virtuel StorSimple local ou un périphérique virtuel StorSimple) en cours d’exécution mars 2016 officielle (disponibilité générale) ou version ultérieure.

Le tableau virtuel StorSimple est un hybride cloud stockage local périphérique virtuel qui peut être configuré comme un serveur de fichiers ou un serveur iSCSI. Il peut créer des sauvegardes, restaurer à partir de sauvegardes et effectuer le basculement entre périphériques si sinistre est nécessaire. Lorsqu’il est configuré comme un serveur de fichiers, il permet également de restauration au niveau de l’élément. Ce didacticiel décrit comment utiliser le portail classique Azure ou l’interface utilisateur de StorSimple web pour créer des sauvegardes planifiées et manuelles de votre tableau virtuel StorSimple.


## <a name="back-up-shares-and-volumes"></a>Sauvegarder des partages et des volumes

Sauvegardes assurer la protection de point dans le temps, améliorent la récupération et réduire les délais de restauration pour les partages et volumes. Vous pouvez sauvegarder un partage ou un volume sur votre appareil StorSimple de deux façons : **planifiée** ou **manuel**. Chacune des méthodes est décrit dans les sections suivantes.

> [AZURE.NOTE] Dans cette version, les sauvegardes planifiées sont créés par une stratégie par défaut qui s’exécute tous les jours à un moment donné et sauvegarde tous les partages ou volumes sur l’appareil. Il n’est pas possible de créer des stratégies personnalisées pour les sauvegardes planifiées pour le moment.

## <a name="change-the-backup-schedule"></a>Modifier la planification de sauvegarde

Votre périphérique virtuel StorSimple comporte une stratégie de sauvegarde par défaut qui démarre à une heure spécifique de la journée (22:30) et sauvegarde tous les partages ou volumes sur l’appareil une fois par jour. Vous pouvez modifier l’heure à laquelle le démarrage de la sauvegarde, mais la fréquence et la rétention (qui spécifie le nombre de sauvegardes à conserver) ne peuvent pas être modifiés. Pendant ces sauvegardes, le périphérique virtuel entière est sauvegardé ; Par conséquent, nous vous recommandons de planifier ces sauvegardes en dehors des heures.

Effectuez les opérations suivantes dans le [portail classique Azure](https://manage.windowsazure.com/) pour modifier l’heure de début de la sauvegarde par défaut.

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>Pour modifier l’heure de début de la stratégie de sauvegarde par défaut

1. Accédez à l’onglet **Configuration** de périphérique.

2. Sous la section **sauvegarde** , spécifiez l’heure de début pour la sauvegarde quotidienne.

3. Cliquez sur **Enregistrer**.

### <a name="take-a-manual-backup"></a>Effectuer une sauvegarde manuelle

Outre les sauvegardes planifiées, vous pouvez prendre un manuel (à la demande) sauvegarde à tout moment.

#### <a name="to-create-a-manual-on-demand-backup"></a>Pour créer une sauvegarde manuelle (à la demande)

1. Accédez à l’onglet **partages** ou l’onglet **Volumes** .

2. Dans la partie inférieure de la page, cliquez sur **sauvegarder tous les**. Vous devrez vérifier que vous voulez prendre la sauvegarde maintenant. Cliquez sur l’icône ![Vérifiez icône](./media/storsimple-ova-backup/image3.png) pour poursuivre la sauvegarde.

    ![confirmation de sauvegarde](./media/storsimple-ova-backup/image4.png)

    Vous serez informé qu’une opération de sauvegarde démarre.

    ![démarrage de sauvegarde](./media/storsimple-ova-backup/image5.png)

    Vous serez informé que le travail a été créé avec succès.

    ![travail de sauvegarde créé](./media/storsimple-ova-backup/image7.png)

3. Pour suivre la progression de la tâche, cliquez sur **Afficher la tâche**.

4. Une fois l’opération de sauvegarde terminée, cliquez sur l’onglet **catalogue de sauvegarde** . Vous devriez voir votre sauvegarde terminée.

    ![Sauvegarde terminée](./media/storsimple-ova-backup/image8.png)

5. Définir les sélections de filtre sur le périphérique approprié, une stratégie de sauvegarde et un laps de temps, puis cliquez sur l’icône ![vérifier l’icône](./media/storsimple-ova-backup/image3.png).

    La sauvegarde doit apparaître dans la liste des jeux de sauvegarde qui s’affiche dans le catalogue.

## <a name="view-existing-backups"></a>Afficher les sauvegardes existantes

Effectuez les opérations suivantes dans le portail classique Azure pour afficher les sauvegardes existantes.

#### <a name="to-view-existing-backups"></a>Pour afficher les sauvegardes existants

1. Dans la page service StorSimple Manager, cliquez sur l’onglet **catalogue de sauvegarde** .

2. Sélectionnez une sauvegarde définie comme suit :

    1. Sélectionnez l’appareil.

    2. Dans la liste déroulante, sélectionnez le partage ou le volume pour la sauvegarde que vous souhaitez sélectionner.

    3. Spécifiez la plage horaire.

    4. Cliquez sur l’icône ![](./media/storsimple-ova-backup/image3.png) pour exécuter cette requête.

    Les sauvegardes associé au partage sélectionné ou volume doit apparaître dans la liste des jeux de sauvegarde.

![video_icon](./media/storsimple-ova-backup/video_icon.png) **vidéo disponible**

Regardez la vidéo pour voir comment vous pouvez créer des partages, sauvegarder des actions et restaurer les données dans un tableau virtuel StorSimple.

> [AZURE.VIDEO use-the-storsimple-virtual-array]

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [l’administration de votre tableau virtuel StorSimple](storsimple-ova-web-ui-admin.md).
