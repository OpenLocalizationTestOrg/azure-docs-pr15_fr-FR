<properties
   pageTitle="Déployer tableau virtuel StorSimple 1 : préparation du portail"
   description="Première didacticiel pour déployer StorSimple tableau virtuel implique la préparation du portail"
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
   ms.date="05/24/2016"
   ms.author="alkohli"/>

# <a name="deploy-storsimple-virtual-array---prepare-the-portal"></a>Déployer StorSimple virtuelle tableau - préparer le portail

![](./media/storsimple-ova-deploy1-portal-prep/getstarted4.png)

## <a name="overview"></a>Vue d’ensemble

Cet article s’applique à Microsoft Azure StorSimple tableau virtuel (également connu sous le périphérique virtuel StorSimple local ou un périphérique virtuel StorSimple) en cours d’exécution mars 2016 officielle (disponibilité générale) version. Il s’agit du premier article de la série de didacticiels de déploiement requis pour le déploiement complètement votre tableau virtuel comme un serveur de fichiers ou un serveur iSCSI. Cet article décrit la préparation nécessaire pour créer et configurer votre service Manager StorSimple avant la mise en service d’un tableau virtuel. Cet article également liée absence d’une liste de vérification de configuration déploiement ainsi que de configuration conditions préalables.

Vous avez besoin des privilèges d’administrateur pour terminer le processus d’installation et de configuration. Nous vous recommandons de consulter la liste de vérification de configuration de déploiement avant de commencer. La préparation du portail prendra inférieure à 10 minutes.

Les informations publiées dans cet article s’applique au déploiement de tableaux virtuels StorSimple Azure portal classique, ainsi que Microsoft Azure pour le gouvernement Cloud.

### <a name="get-started"></a>Prise en main

Le flux de travail de déploiement se compose de préparation du portail, mise en service d’un tableau dans votre environnement virtualisé virtuel et la fin de l’installation. Pour commencer à utiliser le déploiement tableau virtuel StorSimple comme un serveur de fichiers ou un serveur iSCSI, vous devrez faire référence aux ressources suivantes tabulation (articles et vidéos).

#### <a name="deployment-articles"></a>Articles sur le déploiement

Consultez les articles suivants dans l’ordre indiqué pour déployer votre tableau virtuel StorSimple.

| **#** | **Dans cette étape**                          | **Vous effectuez cette procédure...**                                                         | **Utilisez ces documents.**|
|------|-------------------------------------------|--------------------------------------------------------------------------------|------------------------|
|1.   | **Configurer le portail classique Azure**       | Créez et configurez votre service Manager StorSimple avant la mise en service un périphérique virtuel StorSimple.  |[Préparer le portail](storsimple-ova-deploy1-portal-prep.md)|
|2.   | **Mise en service de la matrice virtuelle**           | Pour Hyper-V, mise en service et se connecter à un périphérique virtuel StorSimple sur un système hôte exécutant Hyper-V sur Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2. <br></br> <br></br> Pour VMware, mise en service et se connecter à un périphérique de local virtuel StorSimple sur un système hôte VMware ESXi 5.5 en cours d’exécution et versions ultérieures.<br></br>| [Mise en service d’un tableau virtuel dans Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) <br></br> <br></br> [Mise en service d’un tableau virtuel dans VMware](storsimple-ova-deploy2-provision-vmware.md)|
|3.    | **Définir le tableau virtuel**              | Pour votre serveur de fichiers, effectuer la configuration initiale, enregistrer votre serveur de fichiers StorSimple et terminer l’installation de l’appareil. Vous pouvez prévoir puis partages de PME. <br></br> <br></br> Pour votre serveur iSCSI, effectuer la configuration initiale, enregistrez votre serveur iSCSI StorSimple et terminer l’installation de l’appareil. Vous pouvez ensuite configurer des volumes iSCSI.| [Configurer la tableau virtuel comme serveur de fichiers](storsimple-ova-deploy3-fs-setup.md)<br></br> <br></br>[Configurer la tableau virtuel comme serveur iSCSI](storsimple-ova-deploy3-iscsi-setup.md)|

#### <a name="deployment-videos"></a>Vidéos de déploiement

| **Pour effectuer cette étape...** |  **Regardez cette vidéo.**|
|----------------|-------------|
| Obtenir des instructions détaillées pour la prise en main du tableau virtuel StorSimple. | [Prise en main du tableau virtuel StorSimple](https://azure.microsoft.com/documentation/videos/get-started-with-the-storsimple-virtual-array/)|
| Obtenir des instructions détaillées pour configurer un tableau virtuel StorSimple dans Hyper-V.|[Créer un tableau virtuel StorSimple](https://azure.microsoft.com/documentation/videos/create-a-storsimple-virtual-array/) |
|Instructions détaillées pour configurer et inscrire un tableau virtuel StorSimple|[Configurer un tableau virtuel StorSimple](https://azure.microsoft.com/documentation/videos/configure-a-storsimple-virtual-array/)|
|Des instructions détaillées pour créer des partages, sauvegarder des actions et restaurer des données dans un tableau virtuel StorSimple configuré comme un serveur de fichiers|[Utiliser le tableau virtuel StorSimple](https://azure.microsoft.com/documentation/videos/use-the-storsimple-virtual-array/)|
|Instructions détaillées pour basculement et récupération d’urgence d’une matrice StorSimple virtuel.|[Récupération d’urgence StorSimple tableau virtuel](https://azure.microsoft.com/documentation/videos/storsimple-virtual-array-disaster-recovery/)

Vous pouvez maintenant commencer à configurer le portail classique Azure.

## <a name="configuration-checklist"></a>Aide-mémoire de configuration

La liste de vérification de configuration décrit les informations dont vous avez besoin pour recueillir avant de configurer le logiciel sur votre appareil StorSimple. Préparation de ces informations à l’avance aideront à simplifier le processus de déploiement de l’appareil StorSimple dans votre environnement. Selon que votre périphérique virtuel StorSimple sera déployée comme un serveur de fichiers ou un serveur iSCSI, vous devrez parmi les aide-mémoire suivants.

-   Téléchargez [liste de vérification de la Configuration de serveur de StorSimple tableau virtuel fichier](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf).

-   Télécharger [tableau virtuel StorSimple iSCSI aide-mémoire de Configuration de serveur](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf).

## <a name="prerequisites"></a>Conditions préalables

Vous trouverez ici les conditions préalables configuration pour votre service Manager StorSimple, votre périphérique virtuel StorSimple et le réseau de centre de données.

### <a name="for-the-storsimple-manager-service"></a>Pour le service StorSimple Manager

Avant de commencer, vérifiez que :

-   Vous avez votre compte Microsoft avec les informations d’accès.

-   Vous avez votre compte de stockage de Microsoft Azure avec les informations d’accès.

-   Votre abonnement Microsoft Azure doit être activé pour le service Manager StorSimple.

### <a name="for-the-storsimple-virtual-device"></a>Pour le périphérique virtuel StorSimple

Avant de déployer un périphérique virtuel, vérifiez que :

-   Vous avez accès à un système hôte exécutant Hyper-V sur Windows Server 2008 R2 ou version ultérieure ou VMware (ESXi 5,5 ou version ultérieure) qui peut être utilisé pour une mise en service un appareil.

-   Le système hôte est en mesure d’affecter les ressources suivantes pour configurer votre périphérique virtuel :

    -   Un minimum de 4 cœurs.

    -   Au moins 8 Go de RAM.

    -   Une interface réseau.

    -   Un disque virtuel de 500 Go pour les données du système.

### <a name="for-the-datacenter-network"></a>Pour le réseau de centre de données

Avant de commencer, vérifiez que :

-   Configuration du réseau dans votre centre de données conformément aux exigences de réseau de votre périphérique StorSimple. Pour plus d’informations, voir [StorSimple virtuel tableau requise](storsimple-ova-system-requirements.md).

-   Votre périphérique virtuel StorSimple a une bande passante Internet Mbps 5 dédiée (ou plus) disponibles à tout moment. La bande passante ne doit pas être partagée avec d’autres applications.

## <a name="step-by-step-preparation"></a>Préparation étape par étape

Utilisez la procédure à suivre pour préparer votre portail pour le service Manager StorSimple.

## <a name="step-1-create-a-new-service"></a>Étape 1 : Créer un nouveau service

Une seule instance du service Manager StorSimple peut gérer plusieurs appareils StorSimple 1200. Procédez comme suit pour créer une nouvelle instance du service Manager StorSimple. Si vous avez un service Manager StorSimple existant pour gérer vos périphériques 1200, ignorez cette étape et accédez à [étape 2 : obtenir la clé d’inscription du service](#step-2-get-the-service-registration-key).

[AZURE.INCLUDE [storsimple-ova-create-new-service](../../includes/storsimple-ova-create-new-service.md)]

> [AZURE.IMPORTANT]
>
> Si vous n’avez pas activé la création automatique d’un compte de stockage auprès de votre service, vous devrez créer au moins un compte de stockage une fois que vous avez créé un service.
>

> - Si vous n’avez pas créé un compte de stockage automatiquement, accédez à [configurer un nouveau compte de stockage pour le service](#optional-step-configure-a-new-storage-account-for-the-service) pour obtenir des instructions détaillées.
>

> - Si vous avez activé la création automatique d’un compte de stockage, accédez à [étape 2 : obtenir la clé d’inscription du service](#step-2-get-the-service-registration-key).


## <a name="step-2-get-the-service-registration-key"></a>Étape 2 : Obtenir la clé d’inscription de service


Une fois que le service Manager StorSimple est en cours d’exécution, vous devrez obtenir la clé d’enregistrement de service. Cette clé est utilisée pour enregistrer et connecter votre appareil StorSimple avec le service.

Effectuez les opérations suivantes dans le [portail classique Azure](https://manage.windowsazure.com/).


[AZURE.INCLUDE [storsimple-ova-get-service-registration-key](../../includes/storsimple-ova-get-service-registration-key.md)]

> [AZURE.NOTE]
>
> La clé d’inscription service sert à enregistrer tous les appareils StorSimple Gestionnaire nécessaire d’enregistrer votre Service Manager StorSimple.

## <a name="step-3-download-the-virtual-device-image"></a>Étape 3 : Télécharger l’image du périphérique virtuel

Une fois que vous avez la clé d’inscription du service, vous devrez télécharger l’image du périphérique virtuel appropriées pour configurer un périphérique virtuel sur votre système hôte. Les images de périphérique virtuel sont propres au système d’exploitation et peuvent être téléchargés à partir de la page de démarrage rapide dans le portail classique Azure.

> [AZURE.IMPORTANT] Le logiciel exécuté sur le tableau virtuel StorSimple utilisable uniquement conjointement avec le service Manager Storsimple.


Effectuez les opérations suivantes dans le [portail classique Azure](https://manage.windowsazure.com/).

#### <a name="to-get-the-virtual-device-image"></a>Pour obtenir de l’image du périphérique virtuel

1.  Dans la page **service StorSimple Manager** , cliquez sur le service que vous avez créé. Vous accédez alors à la page de **Démarrage rapide** . (Vous pouvez cliquer sur l’icône de démarrage rapide ![](./media/storsimple-ova-deploy1-portal-prep/image8.png) pour accéder à la page de **Démarrage rapide** à tout moment.)

1.  Cliquez sur le lien correspondant à l’image que vous voulez télécharger à partir du Microsoft Download Center. Les fichiers image sont environ 4,8 Go.

    -   VHDX pour Hyper-V sur Windows Server 2012 et versions ultérieures

    -   Disque dur virtuel pour Hyper-V sur Windows Server 2008 R2 et versions ultérieures

    -   VMDK pour VMWare ESXi 5.5 et versions ultérieures

2.  Téléchargez et décompressez le fichier sur un disque local, effectuer une note de l’endroit où se trouve le fichier décompressé dans.

![icône de vidéo](./media/storsimple-ova-deploy1-portal-prep/video_icon.png) **vidéo disponible**

Regardez la vidéo pour obtenir des instructions étape par étape commencer à utiliser le tableau virtuel StorSimple.

> [AZURE.VIDEO get-started-with-the-storsimple-virtual-array]



## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>Étape facultative : configurer un nouveau compte de stockage pour le service

Il s’agit d’une étape facultative qui doit être effectuée uniquement si vous n’avez pas activé la création automatique d’un compte de stockage auprès de votre service.

Si vous avez besoin créer un compte de stockage Azure dans une zone différente, Découvrez [comment créer un compte de stockage](storage-create-storage-account.md#create-a-storage-account) pour obtenir des instructions étape par étape.

Effectuez les opérations suivantes dans le [portail classique Azure](https://manage.windowsazure.com/) dans la page service Manager StorSimple pour ajouter un compte de stockage de Microsoft Azure existant.

#### <a name="to-add-a-storage-account"></a>Pour ajouter un compte de stockage

1.  Le Service Manager StorSimple page d’accueil, sélectionnez votre service et double-cliquez dessus. Vous accédez alors à la page de **Démarrage rapide** . Sélectionnez la page **configurer** .

2.  Cliquez sur **Ajouter/modifier le compte de stockage**. Dans la boîte de dialogue **Add/Edit stockage compte** , procédez comme suit :

    1.  Cliquez sur **Ajouter nouveau**.

    1.  Indiquez un nom pour votre compte de stockage.

    1.  Fournir la **Touche d’accès rapide** principal pour votre compte de stockage de Microsoft Azure.

    1.  Sélectionnez **Activer SSL mode** pour créer un canal sécurisé pour les communications réseau entre votre appareil et le cloud. Désactivez la case à cocher **Activer SSL mode** uniquement si vous travaillez au sein d’un nuage privé.

    1.  Cliquez sur l’icône du ![](./media/storsimple-ova-deploy1-portal-prep/image7.png). Vous serez averti une fois le compte de stockage est créé.

        ![](./media/storsimple-ova-deploy1-portal-prep/image11.png)

1.  Le compte de stockage nouvellement créé s’affichera sur la page **configurer** sous **comptes de stockage**. Cliquez sur **Enregistrer** pour enregistrer le compte de stockage nouvellement créé. Cliquez sur **OK** lorsque vous êtes invité à confirmer.


## <a name="next-step"></a>Étape suivante

L’étape suivante consiste à configurer une machine virtuelle pour votre périphérique virtuel StorSimple. Selon votre système d’exploitation hôte, consultez les instructions détaillées dans :

-   [Mise en service d’un tableau virtuel StorSimple dans Hyper-V](storsimple-ova-deploy2-provision-hyperv.md)

-   [Mise en service d’un tableau virtuel StorSimple dans VMware](storsimple-ova-deploy2-provision-vmware.md)
