<properties 
   pageTitle="Déploiement d’appareil StorSimple portail pour le gouvernement | Microsoft Azure"
   description="Décrit les étapes et les meilleures pratiques pour déployer le périphérique StorSimple mise à jour 1 et le service dans le portail pour le gouvernement Azure."
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
   ms.workload="NA"
   ms.date="06/17/2016"
   ms.author="v-sharos" />

# <a name="deploy-your-on-premises-storsimple-device-in-the-government-portal"></a>Déployez votre appareil StorSimple local dans le portail pour le gouvernement

[AZURE.INCLUDE [storsimple-version-selector-deploy-gov](../../includes/storsimple-version-selector-deploy-gov.md)]

## <a name="overview"></a>Vue d’ensemble

Bienvenue dans Microsoft Azure StorSimple déploiement du périphérique. Ces didacticiels de déploiement s’appliquent à la série 8000 StorSimple exécutant le logiciel de mise à jour 1 dans le portail pour le gouvernement Azure. Cette série de didacticiels explique comment configurer votre appareil StorSimple et inclut une liste de vérification de configuration, configuration préalable et configuration détaillée étapes.

Les informations contenues dans ces didacticiels supposent que vous avez examiné les mesures de sécurité et décompressé, montés en rack et relié votre appareil StorSimple. Si vous avez besoin effectuer ces tâches, commencez par examiner les [précautions à prendre](storsimple-safety.md). Selon votre modèle d’appareil, vous pouvez ensuite décompresser, montage en rack et câble en suivant les instructions dans :

- [Décompresser, montage en rack et câble votre 8100](storsimple-8100-hardware-installation.md)
- [Décompresser, montage en rack et câble votre 8600](storsimple-8600-hardware-installation.md)

Vous avez besoin des privilèges d’administrateur pour terminer le processus d’installation et de configuration. Nous vous recommandons de consulter la liste de vérification de configuration avant de commencer. Le processus de configuration et le déploiement peut prendre un certain temps pour terminer.

> [AZURE.NOTE] Les informations de déploiement StorSimple publiées sur le site Web Microsoft Azure s’applique aux périphériques série 8000 StorSimple uniquement. Pour obtenir des informations complètes sur les appareils 7000 série, accédez à : [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Pour plus d’informations de déploiement de série 7000, consultez le [Guide de démarrage rapide StorSimple système](http://onlinehelp.storsimple.com/111_Appliance/).

## <a name="deployment-steps"></a>Étapes de déploiement

Effectuer ces étapes requises pour configurer votre appareil StorSimple et se connecter à votre service Manager StorSimple. Outre les étapes nécessaires, il existe des étapes facultatives et les procédures que nécessaires au cours du déploiement. Les instructions de déploiement étape par étape indiquent lorsque vous devez effectuer chacune de ces étapes facultatives.


| Étape                                                                                   | Description                                                                                                                                                   |
|----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **CONDITIONS PRÉALABLES**                                                                      | Ils doivent être effectuées en vue du déploiement à venir.                                                                                        |
| Aide-mémoire de configuration de déploiement.                                                     | Utilisez cette liste de vérification pour collecter et enregistrer les informations avant et pendant le déploiement.                                                                       |
| Conditions préalables au déploiement.                                                               | Ces valider l’environnement est prêt pour le déploiement.                                                                                                     |
|                                                                                        |                                                                                                                                                               |
| **DÉPLOIEMENT ÉTAPE PAR ÉTAPE**                                                                   | Ces étapes sont nécessaires pour déployer votre appareil StorSimple en production.                                                                                      |
| Étape 1 : Créer un nouveau service.                                                         | Configurer la gestion de cloud et de stockage pour votre appareil StorSimple. Ignorer cette étape si vous avez un service existant pour les autres appareils StorSimple.                |
| Étape 2 : Obtenir la clé d’enregistrement de service.                                               | Utilisez cette touche pour enregistrer et vous connecter votre périphérique StorSimple avec le service de gestion.                                                                         |
| Étape 3 : Configurer et inscrire le périphérique à utiliser Windows PowerShell pour StorSimple.    | Connectez le périphérique à votre réseau et l’enregistrer avec Azure pour terminer l’installation à l’aide du service de gestion.                                            |
| Étape 4 : Finaliser la configuration minimale appareil</br>Facultatif : Mettre à jour votre périphérique StorSimple.      | Utiliser le service de gestion pour terminer l’installation de l’appareil et activez-le fournir le stockage.                                                                      |
| Étape 5 : Créer un conteneur de volume.                                                      | Créer un conteneur vers des volumes de mise en service. Un conteneur de volume a compte de stockage, la bande passante et les paramètres de chiffrement pour tous les volumes qu’il contient.    |
| Étape 6 : Créer un volume.                                                                | Mise en service des volumes de stockage sur le périphérique StorSimple pour vos serveurs.                                                                                        |
| Étape 7 : Monter, initialisation et mettre en forme un volume.</br>Facultatif : Configurez o.            | Se connecter à vos serveurs pour le stockage iSCSI fourni par l’appareil. Vous pouvez également configurer o pour vous assurer que vos serveurs tolèrent un lien, le réseau et Échec de l’interface.                                                                                                                                                              |
| Étape 8 : Effectuer une sauvegarde.                                                                  | Configurer votre stratégie de sauvegarde pour protéger vos données                                                                                                                 |
|                                                                                        |                                                                                                                                                               |
| **AUTRES PROCÉDURES**                                                                   | Vous devrez peut-être faire référence à ces procédures lorsque vous déployez votre solution.                                                                                        |
| Configurer un nouveau compte de stockage pour le service.                                      |                                                                                                                                                               |
| Utilisez PuTTY pour vous connecter à la console série appareil.                                    |                                                                                                                                                               |
| Rechercher et appliquer des mises à jour.                                                   |                                                                                                                                                               |
| Obtenir le nom qualifié d’un hôte Windows Server.                                                   |                                                                                                                                                               |
| Créer une sauvegarde manuelle.                                                                 | 
| Configurer o.                                                                          |


## <a name="deployment-configuration-checklist"></a>Aide-mémoire de configuration de déploiement

La liste de configuration de déploiement suivante décrit les informations dont vous avez besoin pour recueillir avant et que vous configurez le logiciel sur votre appareil StorSimple. Préparation de certaines de ces informations à l’avance aideront à simplifier le processus de déploiement de l’appareil StorSimple dans votre environnement. Utilisez cette liste de vérification pour Notez également les modalités de configuration lorsque vous déployez votre appareil.

| Étape                                  | Paramètre                                         | Plus d’informations                                                                                                                                                                | Valeurs |
|----------------------------------------|---------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|
| **Câble votre appareil**                      | Accès série                                     | Configuration initiale d’un appareil                                                                  | Oui/non |
|   |   |  |  |
| **Configurer et inscrire appareil**          | Paramètres du réseau de données 0                           | Adresse IP de données 0 :</br>Masque :</br>Passerelle :</br>Serveur DNS principal :</br>Serveur NTP principal :</br>Serveur de proxy Web IP/nom de domaine complet (facultatif) :</br>Port du proxy Web :|        |
|                                        | Mot de passe administrateur                     | Mot de passe doit être comprise entre 8 et 15 caractères contenant des caractères minuscules, majuscules, numériques et spéciaux. |        |
|                                        | Gestionnaire d’instantanés StorSimple le mot de passe              | Mot de passe doit être 14 ou 15 caractères contenant des caractères minuscules, majuscules, numériques et spéciaux.|        |
|                                        | Clé d’enregistrement de service                          | Cette clé est générée à partir du portail Azure.    |        |
|                                        | Clé de chiffrement de données de service                       | Cette clé est créée lorsque l’appareil est inscrit auprès du service de gestion via le Windows PowerShell pour StorSimple. Copiez cette touche et enregistrez-le dans un emplacement sécurisé.|  |
|   |   |  |  |
| **Configuration minimale complète des périphériques**          | Nom convivial pour votre appareil                     | Il s’agit d’un nom descriptif pour l’appareil. |        |
|                                        | Fuseau horaire                                          | Votre périphérique utilisera ce fuseau horaire pour toutes les opérations planifiées.  |        |
|                                        | Serveur DNS secondaire                              | Il s’agit d’une configuration requise.                                  |        |
|                                        | Interface réseau : contrôleur de données 0 fixe adresses IP                                     | Ces IP doit être prenant à Internet.</br>Contrôleur 0 fixe IP adresse :</br>Contrôleur 1 fixe IP adresse :|
|   |   |  |  |
| **Paramètres de l’interface réseau supplémentaires**  | Interface réseau : données 1</br>Si iSCSI activé, ne configurez pas la passerelle.      | Objectif : Cloud/iSCSI / n’est ne pas utilisé</br>Adresse IP :</br>Masque :</br>Passerelle :|
|                                        | Interface réseau : données 2</br>Si iSCSI activé, ne configurez pas la passerelle.      | Objectif : Cloud/iSCSI / n’est ne pas utilisé</br>Adresse IP :</br>Masque :</br>Passerelle :|
|                                        | Interface réseau : données 3</br>Si iSCSI activé, ne configurez pas la passerelle.      | Objectif : Cloud/iSCSI / n’est ne pas utilisé</br>Adresse IP :</br>Masque :</br>Passerelle :|
|                                        | Interface réseau : données 4</br>Si iSCSI activé, ne configurez pas la passerelle.      | Objectif : Cloud/iSCSI / n’est ne pas utilisé</br>Adresse IP :</br>Masque :</br>Passerelle :|
|                                        | Interface réseau : données 5</br>Si iSCSI activé, ne configurez pas la passerelle.      | Objectif : Cloud/iSCSI / n’est ne pas utilisé</br>Adresse IP :</br>Masque :</br>Passerelle :|
|   |   |  |  |
| **Créer un conteneur de volume**                      | Nom du conteneur en volume :                            | Nom pour le conteneur                                                                                                                                                 |        |
|                                        | Compte de stockage Azure :                            | Clé d’accès aux et nom de compte à associer à ce conteneur de volume stockage                                                                                              |        |
|                                        | Clé de chiffrement de stockage cloud :                     | Clé de chiffrement pour le stockage dans chaque conteneur                                                                                                                           |        |
|   |   |  |  |
| **Créer un volume**                        | Détails de chaque volume                           | Nom du volume :                                                                                                                                                           |        |
|                                        |                                                   | Taille :                                                                                                                                                                  |        |
|                                        |                                                   | Type d’utilisation :                                                                                                                                                            |        |
|                                        |                                                   | Nom du blocage :                                                                                                                                                              |        |
|                                        |                                                   | Stratégie de sauvegarde par défaut :                                                                                                                                                 |        |
|   |   |  |  |
| **Monter, initialisation et mettre en forme un volume** | Détails de chaque serveur hôte de la connexion à l’espace de stockage | Nom du serveur de Windows :                                                                                                                                                   |        |
|                                        |                                                   | Windows Server nom qualifié :                                                                                                                                                    |        |
|                                        |                                                   | Nom de volume de Windows Server :                                                                                                                                                   |        |
|                                        |                                                   | NTFS monter point/lettre :                                                                                                                                      |        |


## <a name="deployment-prerequisites"></a>Conditions préalables au déploiement

Les sections suivantes décrivent les conditions préalables configuration pour votre service Manager StorSimple et votre appareil StorSimple.

### <a name="for-the-storsimple-manager-service"></a>Pour le service StorSimple Manager

Avant de commencer, vérifiez que :

- Vous avez votre compte Microsoft avec les informations d’accès.

- Vous avez votre compte de stockage de Microsoft Azure avec les informations d’accès.

- Votre abonnement Microsoft Azure est activée pour le service Manager StorSimple. Votre abonnement doit être acheté via l' [Accord entreprise](https://azure.microsoft.com/pricing/enterprise-agreement/).

- Vous avez accès au logiciel émulation tel que PuTTY.

### <a name="for-the-device-in-the-datacenter"></a>Pour le périphérique dans le centre de données

Avant de configurer le périphérique, vérifiez que :

- Votre périphérique est entièrement décompressé, monter dans un rayon et entièrement relié d’alimentation, réseau et accès série comme décrit dans :

    -  [Décompresser, montage en rack et câble votre appareil 8100](storsimple-8100-hardware-installation.md)
    -  [Décompresser, montage en rack et câble votre appareil 8600](storsimple-8600-hardware-installation.md)


### <a name="for-the-network-in-the-datacenter"></a>Pour le réseau dans le centre de données

Avant de commencer, vérifiez que :

- Les ports de pare-feu de votre centre de données sont ouverts pour autoriser pour iSCSI et le trafic comme décrit dans [mise en réseau configuration requise pour votre appareil StorSimple](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device)le cloud.

## <a name="step-by-step-deployment"></a>Déploiement étape par étape

Utilisez la procédure à suivre pour déployer votre appareil StorSimple dans le centre de données.

## <a name="step-1-create-a-new-service"></a>Étape 1 : Créer un nouveau service

Un service Manager StorSimple peut gérer plusieurs appareils StorSimple. Procédez comme suit pour créer une nouvelle instance du service Manager StorSimple.

[AZURE.INCLUDE [storsimple-create-new-service-gov](../../includes/storsimple-create-new-service-gov.md)]

> [AZURE.IMPORTANT] Si vous n’avez pas activé la création automatique d’un compte de stockage auprès de votre service, vous devrez créer au moins un compte de stockage une fois que vous avez créé un service. Ce compte de stockage sera utilisé lorsque vous créez un conteneur en volume. 
>
> * Si vous n’avez pas créé un compte de stockage automatiquement, accédez à [configurer un nouveau compte de stockage pour le service](#configure-a-new-storage-account-for-the-service) pour obtenir des instructions détaillées. 
> * Si vous avez activé la création automatique d’un compte de stockage, accédez à [étape 2 : obtenir la clé d’inscription du service](#step-2-get-the-service-registration-key).

## <a name="step-2-get-the-service-registration-key"></a>Étape 2 : Obtenir la clé d’inscription de service

Une fois que le service Manager StorSimple est en cours d’exécution, vous devrez obtenir la clé d’enregistrement de service. Cette clé est utilisée pour enregistrer et connecter votre appareil StorSimple au service.

Effectuez les opérations suivantes dans le portail d’administration.

[AZURE.INCLUDE [storsimple-get-service-registration-key-gov](../../includes/storsimple-get-service-registration-key-gov.md)]


## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>Étape 3 : Configurer et inscrire le périphérique à utiliser Windows PowerShell pour StorSimple

Utiliser Windows PowerShell pour StorSimple pour terminer la configuration initiale de votre appareil StorSimple comme indiqué dans la procédure suivante. Vous devrez utiliser le logiciel d’émulation terminal pour exécuter cette étape. Pour plus d’informations, voir [Utiliser PuTTY pour vous connecter à la console série appareil](#use-putty-to-connect-to-the-device-serial-console).

[AZURE.INCLUDE [storsimple-configure-and-register-device-gov](../../includes/storsimple-configure-and-register-device-gov.md)]

## <a name="step-4-complete-minimum-device-setup"></a>Étape 4 : Finaliser la configuration minimale appareil

Pour la configuration du périphérique minimale de votre appareil StorSimple, vous êtes obligé de : 

- Configurer le serveur DNS secondaire.
- Activer iSCSI sur au moins une interface réseau.
- Affecter des adresses IP fixes aux deux contrôleurs.

Effectuez les opérations suivantes dans le portail pour le gouvernement pour terminer l’installation de périphériques minimum.

[AZURE.INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup-u1.md)]

## <a name="step-5-create-a-volume-container"></a>Étape 5 : Créer un conteneur de volume

Un conteneur de volume a compte de stockage, la bande passante et les paramètres de chiffrement pour tous les volumes qu’il contient. Vous devrez créer un conteneur de volume avant de commencer à mise en service des volumes sur votre appareil StorSimple. 

Effectuez les opérations suivantes dans le portail pour le gouvernement pour créer un conteneur de volume.

[AZURE.INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>Étape 6 : Créer un volume

Après avoir créé un conteneur de volume, vous pouvez configurer un volume de stockage sur le périphérique StorSimple pour vos serveurs. Effectuez les opérations suivantes dans le portail d’administration pour créer un volume.

> [AZURE.IMPORTANT] Azure StorSimple pouvez créer des volumes exactement générés uniquement.  Vous ne pouvez pas créer entièrement généré ou partiellement généré volumes sur un système StorSimple Azure. 

[AZURE.INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>Étape 7 : Monter, initialisation et mettre en forme un volume

Effectuer ces étapes sur votre hôte Windows Server.

> [AZURE.IMPORTANT]

> - Pour la disponibilité de votre solution StorSimple, nous vous recommandons de configurer o sur vos serveurs d’hôte (facultatifs) avant la configuration iSCSI. Configuration o sur vos serveurs hôtes permet de garantir que les serveurs tolèrent un lien, de réseau ou Échec de l’interface.

> - Pour o et iSCSI installation et la configuration des instructions sur l’hôte de Windows Server, accédez à [Configurer o pour votre appareil StorSimple](storsimple-configure-mpio-windows-server.md). Il inclut également les étapes pour monter, initialisation et mettre en forme des volumes StorSimple.

> - Pour o et iSCSI installation et la configuration des instructions sur un hôte Linux, accédez à [Configurer o à votre hôte StorSimple Linux](storsimple-configure-mpio-on-linux.md)

Si vous décidez de ne pas configurer o, procédez comme suit pour monter, initialisation et mettre en forme vos volumes StorSimple sur un hôte Windows Server.

[AZURE.INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>Étape 8 : Effectuer une sauvegarde

Des sauvegardes point-à-temps protègent des volumes et d’améliorent la récupération et en limitant les délais de restauration. Vous pouvez effectuer les deux types de sauvegarde sur votre appareil StorSimple : instantanés locaux et des instantanés cloud. Chacun de ces types de sauvegarde peut être **planifiée** ou **manuel**. 

Effectuez les opérations suivantes dans le portail pour le gouvernement pour créer une sauvegarde planifiée.

[AZURE.INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

Vous pouvez prendre un manuel de sauvegarde à tout moment. Pour plus d’informations, accédez à [créer une sauvegarde manuelle](#create-a-manual-backup). 

## <a name="configure-a-new-storage-account-for-the-service"></a>Configurer un nouveau compte de stockage pour le service

Il s’agit d’une étape facultative dont vous avez besoin pour effectuer uniquement si vous n’avez pas activé la création automatique d’un compte de stockage auprès de votre service. Un compte de stockage de Microsoft Azure est requis pour créer un conteneur de volume StorSimple.

Si vous avez besoin créer un compte de stockage Azure dans une zone différente, voir [à propos des comptes d’espace de stockage Azure](../storage/storage-create-storage-account.md) pour obtenir des instructions étape par étape.

Effectuez les opérations suivantes dans le portail pour le gouvernement, dans la page **service Manager StorSimple** .

[AZURE.INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-configure-new-storage-account-u1.md)]


## <a name="use-putty-to-connect-to-the-device-serial-console"></a>Permet de vous connecter à la console série appareil PuTTY

Pour vous connecter à Windows PowerShell pour StorSimple, vous devez utiliser le logiciel d’émulation terminal tel que PuTTY. Vous pouvez utiliser PuTTY lorsque vous accédez à l’appareil directement par le biais de la console en série ou en ouvrant un présents depuis un ordinateur distant.

[AZURE.INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## <a name="scan-for-and-apply-updates"></a>Rechercher et appliquer des mises à jour

Mise à jour de votre appareil peut prendre plusieurs heures. Procédez comme suit pour rechercher et appliquer des mises à jour sur votre appareil.

<!--If you have a gateway configured on a network interface other than Data 0, you will need to disable Data 2 and Data 3 network interfaces before installing the update. Go to **Devices > Configure** and disable Data 2 and Data 3 interfaces. You should re-enable these interfaces after the device is updated.-->

#### <a name="to-update-your-device"></a>Mettre à jour votre appareil

1.  Dans la page de **Démarrage rapide** de périphérique, cliquez sur **périphériques**. Sélectionnez le périphérique physique, cliquez sur **Maintenance** , puis sur **Rechercher les mises à jour**.  
2.  Une tâche pour rechercher les mises à jour disponibles est créée. Si mises à jour sont disponibles, les **Mises à jour analyse** devient **Installer les mises à jour**. Cliquez sur **installer les mises à jour**. 
3.  Un projet de mise à jour est créée. Surveiller l’état de votre mise à jour en accédant à des **projets**.

    > [AZURE.NOTE] Lorsque l’opération de mise à jour démarre, il affiche immédiatement le statut de 50 %. Le statut devient à 100 pour cent qu’après que la mise à jour de tâche est terminée. Il n’existe aucun état en temps réel pour le processus de mise à jour.

4.  Une fois que l’appareil est correctement mis à jour, activer les interfaces de réseau de données 2 et 3 de données si celles-ci ont été désactivés.

## <a name="get-the-iqn-of-a-windows-server-host"></a>Obtenir le nom qualifié d’un hôte Windows Server

Effectuez les opérations suivantes pour obtenir l’iSCSI complet nom (nom qualifié) d’un hôte Windows exécutant Windows Server® 2012.

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>Créer une sauvegarde manuelle

Effectuez les opérations suivantes dans le portail d’administration pour créer une sauvegarde manuelle à la demande d’un seul volume sur votre appareil StorSimple.

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-create-manual-backup-gov.md)]

## <a name="configure-mpio"></a>Configurer o

Chemins multiples (/ o) est une fonctionnalité facultative et n’est pas installé sur Windows Server par défaut. Il doit être installée en tant que fonctionnalité via le Gestionnaire de serveur. Pour obtenir des instructions d’installation o, accédez à [Configurer o pour votre appareil StorSimple](storsimple-configure-mpio-windows-server.md).

Pour obtenir des instructions d’installation o pour un appareil StorSimple connecté à un hôte Linux, accédez à [Configurer o à votre hôte Linux](storsimple-configure-mpio-on-linux.md).

> [AZURE.NOTE] O n’est pas pris en charge sur un appareil virtuel StorSimple. 

## <a name="next-steps"></a>Étapes suivantes

- Configurer un [périphérique virtuel](storsimple-virtual-device-u2.md).

- Utiliser le [service StorSimple Manager](https://msdn.microsoft.com/library/azure/dn772396.aspx) pour gérer votre appareil StorSimple.
 
