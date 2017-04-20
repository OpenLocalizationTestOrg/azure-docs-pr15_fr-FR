<properties 
   pageTitle="Gérer vos stratégies de sauvegarde StorSimple | Microsoft Azure"
   description="Explique comment vous pouvez utiliser le service Manager StorSimple pour créer et gérer des sauvegardes manuelles, plannings de sauvegarde et la rétention de sauvegarde."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor=""/>
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/10/2016"
   ms.author="v-sharos"/>

# <a name="use-the-storsimple-manager-service-to-manage-backup-policies"></a>Utiliser le service StorSimple Manager pour gérer les stratégies de sauvegarde

[AZURE.INCLUDE [storsimple-version-selector-manage-backup-policies](../../includes/storsimple-version-selector-manage-backup-policies.md)]

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel explique l’utilisation de la page **Stratégies de sauvegarde** du service Manager StorSimple pour contrôler le processus de sauvegarde et de conservation des sauvegardes pour vos volumes StorSimple. Il décrit également comment effectuer une sauvegarde manuelle.

La page **Stratégies de sauvegarde** vous permet de gérer les stratégies de sauvegarde et planifier local et en nuage instantanés. (Stratégies sauvegarde sont utilisées pour configurer les plannings de sauvegarde et de conservation des sauvegardes pour une collection de volumes). Stratégies de sauvegarde permettent de prendre un instantané de plusieurs volumes simultanément. Cela signifie que les sauvegardes créés par une stratégie de sauvegarde seront copies blocage cohérentes. Cette page répertorie les stratégies de sauvegarde, leurs types, les volumes associés, le nombre de copies de sauvegarde conservées et la possibilité d’activer ces stratégies.

La page **Stratégies de sauvegarde** vous permet également de filtrer les stratégies de sauvegarde existantes par un ou plusieurs des champs suivants :

- **Nom de la stratégie** – le nom associé à la stratégie. Les différents types de stratégies sont les suivantes :

   - Stratégies planifiées, qui sont créés explicitement par l’utilisateur.
   - Stratégies automatiques, qui sont créés lorsque la sauvegarde par défaut pour cette option de volume a été activée au moment de la création du volume. Ces politiques sont nommés VolumeName_Default où le nom du Volume désigne le nom du volume StorSimple configuré par l’utilisateur dans le portail classique Azure. Les stratégies automatiques entraînent quotidiennes instantanés cloud commençant au moment de l’appareil 22:30.
   - Stratégies importées, qui ont été créés dans le Gestionnaire d’instantanés StorSimple. Ces ont une balise qui décrit l’hôte de gestionnaire d’instantanés StorSimple les stratégies ont été importés à partir de.

- **Volumes** – les volumes associés à la stratégie. Tous les volumes associés à une stratégie de sauvegarde sont regroupées lors de la création des sauvegardes.

- **Dernière sauvegarde réussie** – la date et l’heure de la dernière sauvegarde réussie qui a été prise avec cette stratégie.

- **Prochaine sauvegarde** – la date et l’heure de la sauvegarde planifiée suivante qui sera lancée par cette stratégie.

- **Calendriers** – le nombre de prévisions associées à la stratégie de sauvegarde.

Les opérations fréquemment utilisées que vous pouvez effectuer à partir de cette page sont :

- Ajouter une stratégie de sauvegarde 
- Ajouter ou modifier une planification 
- Supprimer une stratégie de sauvegarde 
- Effectuer une sauvegarde manuelle 
- Créer une stratégie de sauvegarde personnalisée avec plusieurs volumes et calendriers 

## <a name="add-a-backup-policy"></a>Ajouter une stratégie de sauvegarde

Ajouter une stratégie de sauvegarde pour planifier automatiquement vos sauvegardes. Effectuez les opérations suivantes dans le portail classique Azure pour ajouter une stratégie de sauvegarde de votre périphérique StorSimple. Après avoir ajouté la stratégie, vous pouvez définir une planification (voir [Ajouter ou modifier une planification](#add-or-modify-a-schedule)).

[AZURE.INCLUDE [storsimple-add-backup-policy](../../includes/storsimple-add-backup-policy.md)]

![Vidéo disponible](./media/storsimple-manage-backup-policies/Video_icon.png) **vidéo disponible**

Pour visionner une vidéo qui montre comment créer une variable locale ou sur le cloud stratégie de sauvegarde, cliquez sur [ici](https://azure.microsoft.com/documentation/videos/create-storsimple-backup-policies/).


## <a name="add-or-modify-a-schedule"></a>Ajouter ou modifier une planification

Vous pouvez ajouter ou modifier un programme est associé à une stratégie de sauvegarde existante sur votre appareil StorSimple. Effectuez les opérations suivantes dans le portail classique Azure pour ajouter ou modifier une planification.

[AZURE.INCLUDE [storsimple-add-modify-backup-schedule](../../includes/storsimple-add-modify-backup-schedule.md)]

## <a name="delete-a-backup-policy"></a>Supprimer une stratégie de sauvegarde

Effectuez les opérations suivantes dans le portail classique Azure pour supprimer une stratégie de sauvegarde sur votre appareil StorSimple.

[AZURE.INCLUDE [storsimple-delete-backup-policy](../../includes/storsimple-delete-backup-policy.md)]


## <a name="take-a-manual-backup"></a>Effectuer une sauvegarde manuelle

Effectuez les opérations suivantes dans le portail classique Azure pour créer une sauvegarde (manuelle) à la demande d’un seul volume.

[AZURE.INCLUDE [storsimple-create-manual-backup](../../includes/storsimple-create-manual-backup.md)]

## <a name="create-a-custom-backup-policy-with-multiple-volumes-and-schedules"></a>Créer une stratégie de sauvegarde personnalisée avec plusieurs volumes et calendriers

Effectuez les opérations suivantes dans le portail classique Azure pour créer une stratégie de sauvegarde personnalisée qui comporte plusieurs volumes et les prévisions.

[AZURE.INCLUDE [storsimple-create-custom-backup-policy](../../includes/storsimple-create-custom-backup-policy.md)]


## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [l’utilisation du service Manager StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).
