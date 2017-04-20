<properties 
   pageTitle="Administration Gestionnaire StorSimple virtuelle tableau | Microsoft Azure"
   description="Apprenez à gérer votre tableau de virtuel StorSimple local à l’aide du service Manager StorSimple dans le portail classique Azure."
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
   ms.date="10/11/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-administer-your-storsimple-virtual-array"></a>Utiliser le service Manager StorSimple pour administrer votre tableau virtuel StorSimple

![flux de processus de configuration](./media/storsimple-ova-manager-service-administration/manage4.png)

## <a name="overview"></a>Vue d’ensemble

Cet article décrit l’interface de service Manager StorSimple, notamment comment vous connecter à celle-ci et les différentes options disponibles et fournit des liens vers les flux de travail spécifiques qui peut être effectuées via cette interface utilisateur. 

Après la lecture de cet article, vous savez comment :

- Se connecter au service Gestionnaire de StorSimple
- Naviguer de l’interface utilisateur de StorSimple Manager
- Administrer votre tableau virtuel StorSimple via le service Manager StorSimple

> [AZURE.NOTE] Pour afficher les options de gestion disponibles pour le périphérique série 8000 StorSimple, accédez à [utiliser le service Manager StorSimple pour administrer votre appareil StorSimple](storsimple-manager-service-administration.md).

## <a name="connect-to-the-storsimple-manager-service"></a>Se connecter au service Gestionnaire de StorSimple

Le service StorSimple Manager s’exécute dans Microsoft Azure et se connecte à plusieurs tableaux virtuels StorSimple. Vous utilisez un portail classique Microsoft Azure central en cours d’exécution dans un navigateur pour gérer les appareils suivants. Pour vous connecter au service Gestionnaire de StorSimple, procédez comme suit.

#### <a name="to-connect-to-the-service"></a>Pour vous connecter au service

1. Accédez à [https://manage.windowsazure.com/](https://manage.windowsazure.com/).

2. À l’aide de vos informations d’identification de compte Microsoft, connectez-vous au portail Microsoft Azure classique (situé en haut à droite du volet).

3. Faites défiler le volet de navigation gauche pour accéder au service Manager StorSimple.

    ![Faites défiler vers le service](./media/storsimple-ova-manager-service-administration/admin-scroll.png)

## <a name="navigate-the-storsimple-manager-service-ui"></a>Parcourir le service Manager StorSimple l’interface utilisateur

La hiérarchie de navigation pour le service Manager StorSimple l’interface utilisateur est présentée dans le tableau suivant.

- La page de lancement **StorSimple Manager** vous permet d’accéder aux pages de niveau de service de l’interface utilisateur applicables à tous les tableaux virtuel au sein d’un service.

- La page **périphériques** vous permet d’accéder aux pages de l’interface utilisateur au niveau du périphérique applicables à un tableau virtuel spécifique.

#### <a name="storsimple-manager-service-navigational-hierarchy"></a>Hiérarchie de navigation de service Manager StorSimple

|Page d’accueil|Pages de niveau de service|Pages au niveau du périphérique|
|---|---|---|
|Service Manager StorSimple|Tableau de bord (service)|Tableau de bord (appareil)|
||Appareils →|Moniteur|
||Catalogue de sauvegarde|Actions (serveur de fichiers) ou </br>Volumes (iSCSI server)|
||Configurer (service)|Configurer (périphérique)|
||Tâches|Maintenance|
||Alertes|

## <a name="use-the-storsimple-manager-service-to-perform-management-tasks"></a>Le service Manager StorSimple permet d’effectuer les tâches de gestion

Le tableau suivant présente un résumé de toutes les tâches de gestion courantes et des flux de travail complexe qui peut être effectuée dans le service du Gestionnaire de StorSimple l’interface utilisateur. Ces tâches sont organisées selon les pages de l’interface utilisateur sur lequel elles sont lancées.

Pour plus d’informations sur chaque flux de travail, cliquez sur la procédure appropriée dans le tableau.

#### <a name="storsimple-manager-workflows"></a>Flux de travail StorSimple Manager

|Si vous voulez faire ceci...|Accédez à cette page de l’interface utilisateur...|Utilisez cette procédure|
|---|---|---|
|Création d’un service</br>Supprimer un service</br>Obtenir la clé d’inscription de service</br>Régénérer la clé d’inscription de service|Service Manager StorSimple|[Déployer le service Manager StorSimple](storsimple-ova-manage-service.md)|
|Modifier la clé de chiffrement de données de service</br>Afficher les journaux d’opérations|Tableau de bord StorSimple → de service Manager|[Utiliser le tableau de bord de service StorSimple](storsimple-ova-service-dashboard.md)|
|Désactiver un tableau virtuel</br>Supprimer un tableau virtuel|Gestionnaire de StorSimple service → appareils|[Désactiver ou supprimer un tableau virtuel](storsimple-ova-deactivate-and-delete-device.md)|
|Basculement récupération et appareil d’urgence</br>Conditions préalables à basculement</br>Basculer vers un périphérique virtuel</br>Continuité reprise (BCDR)</br>Erreurs lors de sinistre|Gestionnaire de StorSimple service → appareils|[Basculement de récupération et appareil urgence pour votre tableau virtuel StorSimple](storsimple-ova-failover-dr.md)|
|Sauvegarder des partages et des volumes</br>Effectuer une sauvegarde manuelle</br>Modifier la planification de sauvegarde</br>Afficher les sauvegardes existantes|StorSimple Gestionnaire de service → sauvegarde catalogue|[Sauvegarder votre tableau virtuel StorSimple](storsimple-ova-backup.md)|
|Restaurer des actions à partir d’un jeu de sauvegarde</br>Restaurer des volumes à partir d’un jeu de sauvegarde</br>Restauration d’au niveau de l’élément (serveur de fichiers uniquement)|Catalogue de sauvegarde StorSimple → de service Manager|[Restaurer à partir d’une sauvegarde de votre tableau virtuel StorSimple](storsimple-ova-restore.md)|
|À propos des comptes de stockage</br>Ajouter un compte de stockage</br>Modifier un compte de stockage</br>Supprimer un compte de stockage|Gestionnaire de StorSimple service → configurer|[Gérer les comptes de stockage pour le tableau virtuel StorSimple](storsimple-ova-manage-storage-accounts.md)|
|À propos des enregistrements de contrôle d’accès</br>Ajouter ou modifier un enregistrement de contrôle d’accès </br>Supprimer un enregistrement du contrôle d’accès|Gestionnaire de StorSimple service → configurer|[Gérer les enregistrements de contrôle d’accès pour le tableau virtuel StorSimple](storsimple-ova-manage-acrs.md)|
|Afficher les détails de projet|Gestionnaire de StorSimple service → travaux| [Gérer les travaux de tableau virtuel StorSimple](storsimple-ova-manage-jobs.md)|
|Configurer les paramètres d’alerte</br>Recevoir des notifications d’alerte</br>Gérer les alertes</br>Consulter les alertes|Alertes de service → StorSimple Manager|[Afficher et gérer les alertes pour le tableau virtuel StorSimple](storsimple-ova-manage-alerts.md)|
|Modifier le mot de passe administrateur|Gestionnaire de StorSimple service → appareils → configurer|[Modifier le mot de passe administrateur de périphérique tableau virtuel StorSimple](storsimple-ova-change-device-admin-password.md)|
|Installer les mises à jour logicielles|Gestionnaire de StorSimple service → appareils → Maintenance|[Mettre à jour votre tableau virtuel](storsimple-ova-install-update-01.md)|

>[AZURE.NOTE] Vous devez utiliser l' [interface utilisateur web local](storsimple-ova-web-ui-admin.md) pour les tâches suivantes :
>
>- [Récupérer la clé de chiffrement de données de service](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
>- [Créer un package de prise en charge](storsimple-ova-web-ui-admin.md#generate-a-log-package)
>- [Arrêter et redémarrer un tableau virtuel](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)

##<a name="next-steps"></a>Étapes suivantes
Pour des informations sur l’interface utilisateur web et comment l’utiliser, accédez à [utiliser le site web StorSimple l’interface utilisateur pour administrer votre tableau virtuel StorSimple](storsimple-ova-web-ui-admin.md).
