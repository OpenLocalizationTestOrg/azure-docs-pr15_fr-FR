<properties 
   pageTitle="Administration du service Manager StorSimple | Microsoft Azure"
   description="Apprenez à gérer votre appareil StorSimple à l’aide du service Manager StorSimple dans le portail classique Azure."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/21/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-administer-your-storsimple-device"></a>Utiliser le service Manager StorSimple pour administrer votre appareil StorSimple

## <a name="overview"></a>Vue d’ensemble

Cet article décrit l’interface de service Manager StorSimple, notamment comment se connecter à, les différentes options disponibles et absence des liens vers des flux de travail spécifiques qui peut être effectuées via cette interface utilisateur. Ce guide s’applique aux deux ; physiques StorSimple et le périphérique virtuel.

Après la lecture de cet article, vous allez apprendre à :

- Se connecter au service Gestionnaire de StorSimple
- Naviguer de l’interface utilisateur de StorSimple Manager
- Administrer votre périphérique StorSimple via le service Manager StorSimple


## <a name="connect-to-storsimple-manager-service"></a>Se connecter au service Gestionnaire de StorSimple

Le service StorSimple Manager s’exécute dans Microsoft Azure et se connecte à plusieurs appareils StorSimple. Vous utilisez un portail classique Microsoft Azure central en cours d’exécution dans un navigateur pour gérer les appareils suivants. Pour vous connecter au service Gestionnaire de StorSimple, procédez comme suit.

#### <a name="to-connect-to-the-service"></a>Pour vous connecter au service

1. Accédez à [https://manage.windowsazure.com/](https://manage.windowsazure.com/).

1. À l’aide de vos informations d’identification de compte Microsoft, connectez-vous au portail Microsoft Azure classique (situé en haut à droite du volet).

1. Faites défiler le volet de navigation gauche pour accéder au service Manager StorSimple.


## <a name="navigate-storsimple-manager-service-ui"></a>Naviguer service Manager StorSimple l’interface utilisateur

La hiérarchie de navigation pour le service Manager StorSimple l’interface utilisateur est présentée dans le tableau suivant.

- Page d’accueil **StorSimple Manager** vous permet d’accéder aux pages de niveau de service de l’interface utilisateur applicables à tous les périphériques au sein d’un service.

- Page **de périphériques** vous permet d’accéder aux pages de l’interface utilisateur au niveau du périphérique applicables à un périphérique spécifique.

- Page de **Conteneurs de volume** vous permet d’accéder à la page de volume qui affiche tous les volumes associés à un périphérique.


#### <a name="storsimple-manager-service-navigational-hierarchy"></a>Hiérarchie de navigation de service Manager StorSimple

|Page d’accueil|Pages de niveau de service|Pages au niveau du périphérique|Pages au niveau du périphérique|
|---|---|---|---|
|Service Manager StorSimple|Tableau de bord de service|Tableau de bord appareil||
||Appareils →|Moniteur|
||Catalogue de sauvegarde|Volume containers→|Volumes|
||Configurer (Service)|Stratégies de sauvegarde||
||Tâches|Configurer (périphérique)|
||Alertes|Maintenance|

![Vidéo disponible](./media/storsimple-manager-service-administration/Video_icon.png) **vidéo disponible**

Pour visionner une vidéo qui vous guide dans l’interface utilisateur du service StorSimple Manager, cliquez sur [ici](https://azure.microsoft.com/documentation/videos/storsimple-manager-service-overview/).

## <a name="administer-storsimple-device-using-storsimple-manager-service"></a>Administrer appareil StorSimple à l’aide de service Manager StorSimple

Le tableau suivant présente un résumé de toutes les tâches de gestion courantes et des flux de travail complexe qui peut être effectuée dans le service du Gestionnaire de StorSimple l’interface utilisateur. Ces tâches sont organisées selon les pages de l’interface utilisateur sur lequel elles sont lancées.

Pour plus d’informations sur chaque flux de travail, cliquez sur la procédure appropriée dans le tableau.

#### <a name="storsimple-manager-workflows"></a>Flux de travail StorSimple Manager

|Si vous voulez faire ceci...|Accédez à cette page de l’interface utilisateur...|Utilisez cette procédure.|
|---|---|---|
|Création d’un service</br>Supprimer un service</br>Obtenir la clé d’enregistrement de service</br>Régénérer clé d’enregistrement de service|Service Manager StorSimple|[Déployer un service Manager StorSimple](storsimple-manage-service.md)
|Modifier la clé de chiffrement de données de service</br>Afficher les journaux d’opération|Tableau de bord StorSimple → de service Manager|[Utiliser le tableau de bord de service Manager StorSimple](storsimple-service-dashboard.md)|
|Désactiver un appareil</br>Supprimer un appareil|Gestionnaire de StorSimple service → appareils|[Désactiver ou supprimer un appareil](storsimple-deactivate-and-delete-device.md)|
|En savoir plus sur basculement récupération et appareil d’urgence</br>Basculer vers un périphérique physique</br>Basculer vers un périphérique virtuel</br>Continuité reprise (BCDR)|Gestionnaire de StorSimple service → appareils|[Basculement et récupération d’urgence pour votre appareil StorSimple](storsimple-device-failover-disaster-recovery.md)|
|Liste des sauvegardes pour un volume</br>Sélectionnez un jeu de sauvegarde</br>Supprimer un jeu de sauvegarde|Catalogue de sauvegarde StorSimple → de service Manager|[Gérer les sauvegardes](storsimple-manage-backup-catalog.md)|
|Dupliquer un volume|Catalogue de sauvegarde StorSimple → de service Manager|[Dupliquer un volume](storsimple-clone-volume.md)|
|Restaurer un jeu de sauvegarde|Catalogue de sauvegarde StorSimple → de service Manager|[Restaurer un jeu de sauvegarde](storsimple-restore-from-backup-set.md)|
|À propos des comptes de stockage</br>Ajouter un compte de stockage</br>Modifier un compte de stockage</br>Supprimer un compte de stockage</br>Rotation des clés de comptes de stockage|Gestionnaire de StorSimple service → configurer|[Gérer les comptes de stockage](storsimple-manage-storage-accounts.md)|
|À propos des modèles de bande passante</br>Ajouter un modèle de bande passante</br>Modifier un modèle de bande passante</br>Supprimer un modèle de bande passante</br>Utiliser un modèle de bande passante par défaut</br>Créer un modèle de bande passante sur une journée entière qui commence à une certaine date|Gestionnaire de StorSimple service → configurer|[Gérer les modèles de bande passante](storsimple-manage-bandwidth-templates.md)|
|À propos des enregistrements de contrôle d’accès</br>Créer un enregistrement de contrôle d’accès</br>Modifier un enregistrement du contrôle d’accès</br>Supprimer un enregistrement du contrôle d’accès|Gestionnaire de StorSimple service → configurer|[Gérer les enregistrements de contrôle d’accès](storsimple-manage-acrs.md)|
|Afficher les détails de projet</br>Annulation d’une tâche|Gestionnaire de StorSimple service → travaux|[Gérer les travaux](storsimple-manage-jobs.md)
|Recevoir des notifications d’alerte</br>Gérer les alertes</br>Consulter les alertes|Alertes de service → StorSimple Manager|[Afficher et gérer les alertes StorSimple](storsimple-manage-alerts.md)
|Afficher les initiateurs connectés</br>Trouver le numéro de série d’appareil</br>Trouver la cible nom qualifié|Gestionnaire de StorSimple service → appareils → tableau de bord|[Utiliser le tableau de bord du périphérique StorSimple](storsimple-device-dashboard.md)|
|Créer des graphiques de surveillance|Gestionnaire de StorSimple service → appareils → surveiller|[Surveiller votre appareil StorSimple](storsimple-monitor-device.md)|
|Ajouter un conteneur de volume</br>Modifier un conteneur de volume</br>Supprimer un conteneur de volume|StorSimple Manager service → appareils → Volume conteneurs|[Gérer les conteneurs de volume](storsimple-manage-volume-containers.md)|
|Ajouter un volume</br>Modifier un volume</br>Mettre un volume hors connexion</br>Supprimer un volume</br>Analyser un volume|StorSimple Manager service → appareils → Volume conteneurs → Volumes|[Gérer les volumes](storsimple-manage-volumes.md)|
|Modifier les paramètres de l’appareil</br>Modifier les paramètres de temps</br>Modifier les paramètres de DNS.md</br>Configurer les interfaces réseau|Gestionnaire de StorSimple service → appareils → configurer|[Modifier la configuration du périphérique de votre périphérique StorSimple](storsimple-modify-device-config.md)|
|Afficher les paramètres de proxy web|Gestionnaire de StorSimple service → appareils → configurer|[Configurer le proxy web pour votre appareil](storsimple-configure-web-proxy.md)|
|Modifier le mot de passe administrateur</br>Modifier le mot de passe StorSimple instantané Manager|Gestionnaire de StorSimple service → appareils → configurer|[Modifier les mots de passe StorSimple](storsimple-change-passwords.md)|
|Configurer la gestion à distance|Gestionnaire de StorSimple service → appareils → configurer|[Se connecter à distance sur votre appareil StorSimple](storsimple-remote-connect.md)|
|Configurer les paramètres d’alerte|Gestionnaire de StorSimple service → appareils → configurer|[Afficher et gérer les alertes StorSimple](storsimple-manage-alerts.md)|
|Configurer pour votre appareil StorSimple|Gestionnaire de StorSimple service → appareils → configurer|[Configurer pour votre appareil StorSimple](storsimple-configure-chap.md)|
|Ajouter une stratégie de sauvegarde</br>Ajouter ou modifier une planification</br>Supprimer une stratégie de sauvegarde</br>Effectuer une sauvegarde manuelle</br>Créer une stratégie de sauvegarde personnalisée avec plusieurs volumes et calendriers|Gestionnaire de StorSimple service → appareils → sauvegarde des stratégies|[Gérer les stratégies de sauvegarde](storsimple-manage-backup-policies.md)|
|Arrêter contrôleurs de périphérique</br>Redémarrez contrôleurs appareil</br>Arrêter les contrôleurs appareil</br>Restaurez votre périphérique par défaut</br>(Ci-dessus sont pour appareil local uniquement)|Gestionnaire de StorSimple service → appareils → Maintenance|[Gérer contrôleur de périphérique StorSimple](storsimple-manage-device-controller.md)|
|En savoir plus sur les composants matériels StorSimple</br>Surveiller le statut de matériel</br>(Ci-dessus sont pour appareil local uniquement)|Gestionnaire de StorSimple service → appareils → Maintenance|[Composants du matériel moniteur](storsimple-monitor-hardware-status.md)|
|Créer un package de prise en charge|Gestionnaire de StorSimple service → appareils → Maintenance|[Créer et gérer un package de prise en charge](storsimple-create-manage-support-package.md)|
|Installer les mises à jour logicielles|Gestionnaire de StorSimple service → appareils → Maintenance|[Mettre à jour votre périphérique](storsimple-update-device.md)|


##<a name="next-steps"></a>Étapes suivantes
Si vous rencontrez des problèmes avec l’utilisation quotidienne de votre appareil StorSimple ou d’un de ses composants matériels, reportez-vous à :

- [Résoudre les problèmes d’un périphérique opérationnel](storsimple-troubleshoot-operational-device.md)
- [Utiliser StorSimple voyants de surveillance](storsimple-monitoring-indicators.md)

Si vous ne pouvez pas résoudre les problèmes et vous devez créer une demande de service, reportez-vous à [Contacter le support technique Microsoft](storsimple-contact-microsoft-support.md).
