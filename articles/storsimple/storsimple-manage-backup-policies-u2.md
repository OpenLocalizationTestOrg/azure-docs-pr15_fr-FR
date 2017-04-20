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

# <a name="use-the-storsimple-manager-service-to-manage-backup-policies-update-2"></a>Utiliser le service StorSimple Manager pour gérer les stratégies de sauvegarde (mise à jour 2)

[AZURE.INCLUDE [storsimple-version-selector-manage-backup-policies](../../includes/storsimple-version-selector-manage-backup-policies.md)]

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel explique l’utilisation de la page **Stratégies de sauvegarde** du service Manager StorSimple pour contrôler le processus de sauvegarde et de conservation des sauvegardes pour vos volumes StorSimple. Il décrit également comment effectuer une sauvegarde manuelle.

Lorsque vous sauvegardez un volume, vous pouvez choisir de créer un instantané local ou un instantané de cloud. Si vous sauvegardez un volume localement épinglé, nous vous recommandons de que vous spécifiez un instantané de cloud. En tenant un grand nombre d’instantanés locales d’un volume localement épinglé associée à un jeu de données qui contient un grand nombre de l’évolution du entraîne une situation dans laquelle vous pouvez rapidement manquer d’espace local. Si vous choisissez de prendre des instantanés locales, nous vous recommandons que vous prenez des instantanés quotidiennes moins sauvegarder état le plus récent, les maintenir pour un jour, puis supprimez.

Lorsque vous effectuez un instantané de cloud d’un volume épinglé localement, vous copiez uniquement les données modifiées dans le cloud, où il est dédupliqué et compressé. 

## <a name="the-backup-policies-page"></a>La page stratégies de sauvegarde

La page **Stratégies de sauvegarde** vous permet de gérer les stratégies de sauvegarde et planifier local et en nuage instantanés. (Stratégies sauvegarde sont utilisées pour configurer les plannings de sauvegarde et de conservation des sauvegardes pour une collection de volumes). Stratégies de sauvegarde permettent de prendre un instantané de plusieurs volumes simultanément. Cela signifie que les sauvegardes créés par une stratégie de sauvegarde seront copies blocage cohérentes. La page **Stratégies de sauvegarde** répertorie les stratégies de sauvegarde, leurs types, les volumes associés, le nombre de copies de sauvegarde conservées et la possibilité d’activer ces stratégies.

La page **Stratégies de sauvegarde** vous permet également de filtrer les stratégies de sauvegarde existantes par un ou plusieurs des champs suivants :

- **Nom de la stratégie** – le nom associé à la stratégie. Les différents types de stratégies sont les suivantes :

   - Stratégies planifiées, qui sont créés explicitement par l’utilisateur.
   - Stratégies automatiques, qui sont créés lorsque la sauvegarde par défaut pour cette option de volume a été activée au moment de la création du volume. Ces politiques sont nommés _Default *nom_volume*où *nom_volume* désigne le nom du volume StorSimple configuré par l’utilisateur dans le portail classique Azure. Les stratégies automatiques entraînent quotidiennes instantanés cloud commençant au moment de l’appareil 22:30.
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

[AZURE.INCLUDE [storsimple-add-backup-policy-u2](../../includes/storsimple-add-backup-policy-u2.md)]

![Vidéo disponible](./media/storsimple-manage-backup-policies-u2/Video_icon.png) **vidéo disponible**

Pour visionner une vidéo qui montre comment créer une variable locale ou sur le cloud stratégie de sauvegarde, cliquez sur [ici](https://azure.microsoft.com/documentation/videos/create-storsimple-backup-policies/).


## <a name="add-or-modify-a-schedule"></a>Ajouter ou modifier une planification

Vous pouvez ajouter ou modifier un programme est associé à une stratégie de sauvegarde existante sur votre appareil StorSimple. Effectuez les opérations suivantes dans le portail classique Azure pour ajouter ou modifier une planification.

[AZURE.INCLUDE [storsimple-add-modify-backup-schedule](../../includes/storsimple-add-modify-backup-schedule-u2.md)]

## <a name="delete-a-backup-policy"></a>Supprimer une stratégie de sauvegarde

Effectuez les opérations suivantes dans le portail classique Azure pour supprimer une stratégie de sauvegarde sur votre appareil StorSimple.

[AZURE.INCLUDE [storsimple-delete-backup-policy](../../includes/storsimple-delete-backup-policy.md)]


## <a name="take-a-manual-backup"></a>Effectuer une sauvegarde manuelle

Effectuez les opérations suivantes dans le portail classique Azure pour créer une sauvegarde (manuelle) à la demande d’un seul volume.

[AZURE.INCLUDE [storsimple-create-manual-backup](../../includes/storsimple-create-manual-backup.md)]

## <a name="create-a-custom-backup-policy-with-multiple-volumes-and-schedules"></a>Créer une stratégie de sauvegarde personnalisée avec plusieurs volumes et calendriers

Effectuez les opérations suivantes dans le portail classique Azure pour créer une stratégie de sauvegarde personnalisée qui comporte plusieurs volumes et les prévisions.

[AZURE.INCLUDE [storsimple-create-custom-backup-policy](../../includes/storsimple-create-custom-backup-policy-u2.md)]


## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [l’utilisation du service Manager StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).
