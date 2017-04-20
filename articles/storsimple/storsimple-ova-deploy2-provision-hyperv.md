<properties
   pageTitle="Déployer StorSimple virtuel tableau - disposition dans Hyper-V"
   description="Ce didacticiel deuxième déploiement tableau virtuel StorSimple implique un périphérique virtuel dans Hyper-V de mise en service."
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
   ms.date="10/11/2016"
   ms.author="alkohli"/>

# <a name="deploy-storsimple-virtual-array---provision-a-virtual-array-in-hyper-v"></a>Déployer tableau virtuel StorSimple - mise en service d’un tableau virtuel dans Hyper-V

![](./media/storsimple-ova-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel mise à disponibilité s’applique à Microsoft Azure StorSimple virtuel matrices (également appelé périphériques virtuels StorSimple locaux ou périphériques virtuels StorSimple) en cours d’exécution mars 2016 officielle (disponibilité générale) version. Ce didacticiel décrit comment configurer un tableau virtuel StorSimple sur un système hôte exécutant Hyper-V sur Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2. Cet article s’applique au déploiement de tableaux virtuels StorSimple Azure portal classique, ainsi que Microsoft Azure pour le gouvernement Cloud.

Vous aurez besoin de privilèges d’administrateur pour la mise en service et configurer un périphérique virtuel. La configuration initiale et mise en service peut prendre environ 10 minutes pour terminer.


## <a name="provisioning-prerequisites"></a>Mise en service des conditions préalables

Vous trouverez ici les conditions préalables pour configurer un périphérique virtuel sur un système hôte exécutant Hyper-V sur Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2.

### <a name="for-the-storsimple-manager-service"></a>Pour le service StorSimple Manager

Avant de commencer, vérifiez que :

-   Vous avez effectué toutes les étapes dans [préparer le portail de tableau virtuel StorSimple](storsimple-ova-deploy1-portal-prep.md).

-   Vous avez téléchargé l’image du périphérique virtuel pour Hyper-V à partir du portail Azure. Pour plus d’informations, voir [étape 3 : télécharger l’image du périphérique virtuel](storsimple-ova-deploy1-portal-prep.md#step-3-download-the-virtual-device-image).

    > [AZURE.IMPORTANT] Le logiciel exécuté sur le tableau virtuel StorSimple utilisable uniquement conjointement avec le service Manager Storsimple.

### <a name="for-the-storsimple-virtual-device"></a>Pour le périphérique virtuel StorSimple

Avant de déployer un périphérique virtuel, vérifiez que :

-   Vous avez accès à un système hôte exécutant Hyper-V sur Windows Server 2008 R2 ou version ultérieure qui peut être utilisé pour une mise en service un appareil.

-   Le système hôte est en mesure d’affecter les ressources suivantes pour configurer votre périphérique virtuel :

    -   Un minimum de 4 cœurs.

    -   Au moins 8 Go de RAM.

    -   Une interface réseau.

    -   Un disque virtuel de 500 Go pour les données du système.

### <a name="for-the-network-in-the-datacenter"></a>Pour le réseau dans le centre de données

Avant de commencer, vérifiez la configuration réseau pour déployer un périphérique virtuel StorSimple et configurer le centre de données réseau correctement. Pour plus d’informations, voir [Configuration requise de la mise en réseau de tableau virtuel StorSimple](storsimple-ova-system-requirements.md#networking-requirements).

## <a name="step-by-step-provisioning"></a>Mise en service étape par étape

Pour mettre en service et se connecter à un périphérique virtuel, vous devez effectuer les étapes suivantes :

1.  Assurez-vous que le système hôte dispose des ressources suffisantes pour répondre aux exigences minimale périphérique virtuel.

2.  Configurer un périphérique virtuel dans votre hyperviseur.

3.  Démarrer le périphérique virtuel et obtenir l’adresse IP.

Chacune de ces étapes est décrite dans les sections suivantes.

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements"></a>Étape 1 : Assurez-vous que le système hôte répond aux conditions minimales périphérique virtuel

Pour créer un périphérique virtuel, vous devez :

-   Le rôle Hyper-V est installé sur Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2 SP1.

-   Gestionnaire de Hyper-V de Microsoft sur un client Microsoft Windows connecté à l’hôte.

Vous devez vous assurer que le matériel sous-jacent (système hôte) sur lequel vous créez le périphérique virtuel est en mesure de dédier les ressources suivantes à votre appareil virtuel :

- Un minimum de 4 cœurs.
- Au moins 8 Go de RAM.
- Une interface réseau.
- Un disque virtuel de 500 Go pour les données du système.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Étape 2 : Configurer un périphérique virtuel dans hyperviseur

Effectuez les opérations suivantes pour configurer un périphérique dans votre hyperviseur.

#### <a name="to-provision-a-virtual-device"></a>Pour configurer un périphérique virtuel

1.  Sur votre hôte Windows Server, copiez l’image du périphérique virtuel sur un disque local. Il s’agit de l’image (disque dur virtuel ou VHDX) que vous avez téléchargé à partir du portail Azure. Prenez note de l’emplacement où vous avez copié l’image que vous utiliserez cela plus loin dans la procédure.

2.  Ouvrez le **Gestionnaire de serveur**. Dans le coin supérieur droit, cliquez sur **Outils** , puis sélectionnez **Gestionnaire Hyper-V**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image1.png)

    Si vous exécutez Windows Server 2008 R2, ouvrez le Gestionnaire Hyper-V. Dans le Gestionnaire de serveur, cliquez sur **rôles > Hyper-V > Gestionnaire Hyper-V**.

1.  Dans le **Gestionnaire Hyper-V**, dans le volet étendue, avec le bouton droit le nœud de votre système pour ouvrir le menu contextuel, puis cliquez sur **Nouveau** > **Machine virtuelle**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image2.png)

1.  Dans la page **avant de commencer** de l’Assistant Nouvel ordinateur virtuel, cliquez sur **suivant**.

1.  Dans la page **emplacement et spécifier un nom** , attribuez un **nom** pour votre périphérique virtuel. Cliquez sur **suivant**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image4.png)

1.  Dans la page **spécifier génération** , choisissez le type d’image de périphérique, puis cliquez sur **suivant**. Cette page ne s’affiche pas si vous utilisez Windows Server 2008 R2.

    * Choisissez **génération 2** si vous avez téléchargé une image .vhdx de Windows Server 2012 ou version ultérieure.
    * Choisissez **génération 1** si vous avez téléchargé une image .vhd pour Windows Server 2008 R2 ou version ultérieure.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image5.png)

1.  Dans la page **attribuer de la mémoire** , spécifiez une **mémoire démarrage** d’au moins **Mo 8192**, ne pas activer la mémoire dynamique, puis cliquez sur **suivant**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image6.png)

1.  Dans la page **configurer le réseau** , spécifiez le commutateur virtuel qui est connecté à Internet, puis cliquez sur **suivant**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image7.png)

1.  Dans la page **se connecter disque dur** , choisissez **utiliser un disque dur virtuel existant**, spécifiez l’emplacement de l’image du périphérique virtuel (.vhdx ou .vhd), puis sur **suivant**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image8m.png)

1.  Consultez le **Résumé** , puis cliquez sur **Terminer** pour créer la machine virtuelle. Mais ne passer encore : vous avez encore besoin d’ajouter quelques cœurs du processeur et un second disque. 

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image9.png)

1.  Pour répondre à la configuration minimale requise, vous devrez 4 cœurs. Pour ajouter des processeurs virtuels, avec votre système hôte sélectionné dans la fenêtre **Gestionnaire Hyper-V** , dans le volet droit sous la liste des **Machines virtuelles**, localisez la machine virtuelle que vous venez de créer. Sélectionnez et cliquez sur le nom de l’ordinateur et sélectionnez **paramètres**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image10.png)

1.  Dans la page **paramètres** , dans le volet gauche, cliquez sur **processeur**. Dans le volet de droite, définissez le **nombre de processeurs virtuels** 4 (ou plus). Cliquez sur **Appliquer**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image11.png)

1.  Pour répondre à la configuration minimale requise, vous devez également ajouter un disque de données virtuel 500 Go. Dans la page **paramètres** :

    1.  Dans le volet gauche, sélectionnez **Contrôleur SCSI**.
    2.  Dans le volet droit, sélectionnez le **Disque dur,** puis cliquez sur **Ajouter**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image12.png)

1.  Dans la page **disque dur** , sélectionnez l’option de **disque dur virtuel** et cliquez sur **Nouveau**. Démarre l' **Assistant nouveau disque dur virtuel**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image13.png)

1.  Dans la page **avant de commencer** de l’Assistant Création de disque dur virtuel, cliquez sur **suivant**.

1.  Dans la **page Sélectionnez le Format du disque**, acceptez l’option par défaut du format **VHDX** . Cliquez sur **suivant**. Vous ne verrez cet écran si vous exécutez Windows Server 2012 R2 ou Windows Server 2008 R2.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image15.png)

1.  Dans la **page Choisir un Type de disque**, définir le type de disque dur virtuel comme **taille dynamique** (recommandé). Si vous choisissez disque **taille fixe** , elle fonctionne également, mais vous devrez peut-être patienter beaucoup de temps. Nous vous recommandons de ne pas utiliser l’option **différenciation** . Cliquez sur **suivant**. Notez que la **taille dynamique** est la valeur par défaut dans Windows Server 2012 R2 et Windows Server 2012. Dans Windows Server 2008 R2, la valeur par défaut est **taille fixe**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image16.png)

1.  Dans la page **spécifier le nom et l’emplacement** , indiquez un **nom** , ainsi que les **emplacement** (vous pouvez naviguer jusqu'à un) pour le disque de données. Cliquez sur **suivant**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image17.png)

1.  Dans la page **Configurer un disque** , sélectionnez l’option **créer un nouveau disque dur virtuel vide** et spécifiez la taille comme **500 Go** (ou plus). 500 Go est la configuration minimale requise, vous pouvez toujours prévoir un disque plus volumineux. Notez que vous ne pouvez pas développer ou réduire le disque une fois sa mise en service. Pour plus d’informations sur la taille du disque mise en service, consultez la section de redimensionnement dans le document de [meilleures pratiques](storsimple-ova-best-practices.md#configuration-best-practices) . Cliquez sur **suivant**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image18.png)

1.  Dans la page de **Résumé** , vérifiez les détails de votre disque données virtuel et si vous êtes satisfait, cliquez sur **Terminer** pour créer le disque. L’Assistant se ferme et un disque dur virtuel sont ajouté à votre ordinateur.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image19.png)

2.  Vous renverra à la page **paramètres** . Cliquez sur **OK** pour fermer la page **paramètres** et revenir à la fenêtre Gestionnaire Hyper-V.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Étape 3 : Démarrer le périphérique virtuel et obtenir l’adresse IP

Procédez comme suit pour démarrer votre périphérique virtuel et s’y connecter.

#### <a name="to-start-the-virtual-device"></a>Pour démarrer le périphérique virtuel

1.  Démarrez le périphérique virtuel.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image21.png)

1.  Une fois que l’appareil est en cours d’exécution, sélectionnez le périphérique, cliquez avec le bouton droit sur, puis sélectionnez **se connecter**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image22.png)

1.  Vous devrez peut-être patienter 10-5 minutes pour que le périphérique soit prêt. Un message d’état s’affiche dans la console pour indiquer la progression. Une fois que l’appareil est prêt, accédez à **l’Action**. Appuyez sur la touche `Ctrl + Alt + Delete` pour vous connecter à l’appareil virtuel. L’utilisateur par défaut est *StorSimpleAdmin* et le mot de passe par défaut est *le mot de passe1*.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image23.png)

1.  Pour des raisons de sécurité, le mot de passe administrateur appareil expire en premier. Vous devrez modifier le mot de passe.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image24.png)

    Entrez un mot de passe qui contient au moins 8 caractères. Le mot de passe doit satisfaire au moins 3 déconnecter 4 conditions suivantes : majuscules, minuscules, numériques et caractères spéciaux. Retapez le mot de passe pour le confirmer. Vous serez informé que le mot de passe a changé.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image25.png)

1.  Une fois que le mot de passe est correctement modifiée, le périphérique virtuel peut redémarrer. Attendez que le périphérique pour démarrer.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image26.png)

    La console Windows PowerShell du périphérique s’afficheront avec une barre de progression.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image27.png)

1.  Les étapes 6 à 8 s’appliquent uniquement lors du démarrage vers le haut dans un environnement non DHCP. Si vous êtes dans un environnement DHCP, ignorez ces étapes, puis passez à l’étape 9. Si vous avez démarré votre appareil dans un environnement non DHCP, vous verrez l’écran suivant.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image28m.png)

    Maintenant, vous devrez configurer le réseau.

1.  Utiliser la `Get-HcsIpAddress` commande pour répertorier les interfaces réseau activés sur votre appareil virtuel. Si votre appareil dispose d’une seule interface réseau activée, le nom par défaut affecté à cette interface est `Ethernet`.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image29m.png)

1.  Utiliser la `Set-HcsIpAddress` applet de commande pour configurer le réseau. Voici un exemple :

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image30.png)

1.  Une fois la configuration initiale terminée et l’appareil a démarré, vous verrez le texte de bannière appareil. Notez l’adresse IP et l’URL affichée dans le texte de bannière à gérer le périphérique. Vous allez utiliser cette adresse IP pour se connecter à l’interface utilisateur de votre périphérique virtuel web et procéder à la mise en page et enregistrement.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image31m.png)



1. (Facultatif) Exécuter cette étape uniquement si vous déployez votre appareil dans le Cloud pour le gouvernement. Vous allez maintenant activer le mode États-Unis FIPS Federal Information Processing Standard () sur votre appareil. La norme FIPS 140 définit les algorithmes de chiffrement approuvées par le Federal nous systèmes informatiques pour le gouvernement pour la protection des données sensibles.
    1. Pour activer le mode FIPS, exécutez l’applet de commande suivante :

        `Enter-HcsFIPSMode`

    2. Redémarrez votre appareil après avoir activé le mode FIPS pour que les validations cryptographiques prennent effet.

        > [AZURE.NOTE] Vous pouvez activer ou désactiver le mode FIPS sur votre appareil. En alternance le périphérique entre les modes FIPS et non-FIPS n’est pas pris en charge.

Si votre appareil ne répond pas à la configuration minimale requise, vous verrez une erreur dans le texte de bannière (illustré ci-dessous). Vous devez modifier la configuration du périphérique de sorte qu’il dispose des ressources appropriées pour la configuration minimale requise. Vous pouvez ensuite redémarrer et se connecter à l’appareil. Reportez-vous à la configuration minimale requise dans [étape 1 : Assurez-vous que le système hôte répond aux conditions minimales périphérique virtuel](#step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-ova-deploy2-provision-hyperv/image32.png)

Si vous rencontrez d’autres erreurs lors de la configuration initiale à l’aide de l’interface utilisateur web local, consultez le flux de travail suivants dans [Gérer votre tableau virtuel StorSimple à l’aide de l’interface utilisateur web local](storsimple-ova-web-ui-admin.md).

-   Exécuter les tests de diagnostic pour [résoudre les problèmes d’installation de l’interface utilisateur web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).

-   [Package de journal générer et afficher les fichiers journaux](storsimple-ova-web-ui-admin.md#generate-a-log-package).

![icône de vidéo](./media/storsimple-ova-deploy2-provision-hyperv/video_icon.png)  **vidéo disponible**

Regardez la vidéo pour découvrir comment vous pouvez configurer un tableau virtuel StorSimple dans Hyper-V.

> [AZURE.VIDEO create-a-storsimple-virtual-array]

## <a name="next-steps"></a>Étapes suivantes

-   [Configurez votre tableau virtuel StorSimple comme un serveur de fichiers](storsimple-ova-deploy3-fs-setup.md)

-   [Configurez votre tableau virtuel StorSimple comme un serveur iSCSI](storsimple-ova-deploy3-iscsi-setup.md)
