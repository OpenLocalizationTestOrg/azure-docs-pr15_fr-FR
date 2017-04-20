<properties
   pageTitle="Déployer StorSimple virtuel tableau - disposition dans VMware"
   description="Ce didacticiel deuxième série de déploiement de tableau virtuel StorSimple implique un périphérique virtuel dans VMware de mise en service."
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
   ms.date="04/12/2016"
   ms.author="alkohli"/>


# <a name="deploy-storsimple-virtual-array---provision-a-virtual-array-in-vmware"></a>Déployer tableau virtuel StorSimple - mise en service d’un tableau virtuel dans VMware

![](./media/storsimple-ova-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>Vue d’ensemble 
Ce didacticiel mise à disponibilité s’applique à StorSimple virtuel matrices (également appelé périphériques virtuels StorSimple locaux ou périphériques virtuels StorSimple) en cours d’exécution mars 2016 officielle (disponibilité générale) version. Ce didacticiel décrit comment configurer et se connecter à un tableau virtuel StorSimple sur un système hôte VMware ESXi 5.5 en cours d’exécution et versions ultérieures. Cet article s’applique au déploiement de tableaux virtuels StorSimple Azure portal classique, ainsi que Microsoft Azure pour le gouvernement Cloud.

Vous aurez besoin des privilèges d’administrateur à la disposition et se connecter à un périphérique virtuel. La configuration initiale et mise en service peut prendre environ 10 minutes pour terminer.


## <a name="provisioning-prerequisites"></a>Mise en service des conditions préalables

Vous trouverez ici les conditions préalables pour configurer un périphérique virtuel sur un système hôte VMware ESXi 5.5 en cours d’exécution et versions ultérieures.

### <a name="for-the-storsimple-manager-service"></a>Pour le service StorSimple Manager

Avant de commencer, vérifiez que :

-   Vous avez effectué toutes les étapes dans [préparer le portail de tableau virtuel StorSimple](storsimple-ova-deploy1-portal-prep.md).

-   Vous avez téléchargé l’image du périphérique virtuel pour VMware à partir du portail Azure. Pour plus d’informations, voir [étape 3 : télécharger l’image du périphérique virtuel](storsimple-ova-deploy1-portal-prep.md#step-3-download-the-virtual-device-image).

### <a name="for-the-storsimple-virtual-device"></a>Pour le périphérique virtuel StorSimple 

Avant de déployer un périphérique virtuel, vérifiez que :

-   Vous avez accès à un système hôte exécutant Hyper-V (2008 R2 ou version ultérieure) qui peut être utilisé pour une mise en service un appareil.

-   Le système hôte est en mesure d’affecter les ressources suivantes pour configurer votre périphérique virtuel :

    -   Un minimum de 4 cœurs.

    -   Au moins 8 Go de RAM.

    -   Une interface réseau.

    -   Un disque virtuel de 500 Go pour les données du système.

### <a name="for-the-network-in-datacenter"></a>Pour le réseau dans le centre de données 

Avant de commencer, vérifiez que :

-   Vous avez examiné les exigences de réseau pour déployer un périphérique virtuel StorSimple et configuré le réseau de centre de données conformément à la configuration requise. Pour plus d’informations, voir configuration [requise de tableau virtuel StorSimple](storsimple-ova-system-requirements.md).

## <a name="step-by-step-provisioning"></a>Mise en service étape par étape 

Pour mettre en service et se connecter à un périphérique virtuel, vous devez effectuer les étapes suivantes :

1.  Assurez-vous que le système hôte dispose des ressources suffisantes pour répondre aux exigences minimale périphérique virtuel.

2.  Configurer un périphérique virtuel dans votre hyperviseur.

3.  Démarrer le périphérique virtuel et obtenir l’adresse IP.

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>Étape 1 : Vérifiez système hôte périphérique virtuel minimale requise

Pour créer un périphérique virtuel, vous devez :

-   Accès à un système hôte exécutant VMware ESXi Server 5.5 et versions ultérieures.

-   VMware vSphere client sur votre système pour gérer l’hôte ESXi.

    -   Un minimum de 4 cœurs.

    -   Au moins 8 Go de RAM.

    -   Une interface réseau connecté au réseau capable de routage le trafic Internet. La bande passante Internet minimale doit être 5 Mbps pour permettre à l’utilisation optimale du périphérique.

    -   Un disque virtuel de 500 Go pour les données.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Étape 2 : Configurer un périphérique virtuel dans hyperviseur

Effectuez les opérations suivantes pour configurer un périphérique virtuel dans votre hyperviseur.

1.  Copier l’image du périphérique virtuel sur votre système. Il s’agit de l’image que vous avez téléchargé à partir du portail classique Azure. 
    1.  Assurez-vous qu’il s’agit de la dernière fichier image que vous avez téléchargé. Si vous avez précédemment téléchargé l’image, téléchargez-le à nouveau pour vous assurer que vous disposez de la dernière image. La dernière image comporte deux fichiers (au lieu d’un).
    2.  Prenez note de l’emplacement où vous avez copié l’image que vous utiliserez cela plus loin dans la procédure.

2.  Se connecter au serveur ESXi utilise le client vSphere. Vous devez disposer des droits d’administrateur pour créer une machine virtuelle.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image1.png)

1.  Dans le client vSphere, dans la section inventaire dans le volet gauche, sélectionnez le serveur ESXi.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image2.png)

1.  Vous allez tout d’abord télécharger le VMDK au serveur ESXi. Accédez à l’onglet **Configuration** dans le volet droit. Sous **matériel**, sélectionnez **l’espace de stockage**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image3.png)

1.  Dans le volet droit, sous **banques de données**, sélectionnez le magasin de données dans lequel vous voulez télécharger le VMDK. Le magasin de données doit avoir suffisamment d’espace pour les disques système d’exploitation et de données.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image4.png)

1.  Cliquez avec le bouton droit sur et sélectionnez **Parcourir le magasin de données**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image5.png)

1.  Une fenêtre de **Navigateur de magasin de données** s’affichent.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image6.png)

1.  Dans la barre d’outils, cliquez sur ![](./media/storsimple-ova-deploy2-provision-vmware/image7.png) icône pour créer un nouveau dossier. Spécifiez le nom du dossier et prenez note de celle-ci. Vous allez utiliser ce nom de dossier ultérieurement lorsque vous créez une machine virtuelle (recommandée). Cliquez sur **OK**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image8.png)

1.  Le nouveau dossier s’affichent dans le volet gauche du **Navigateur de magasin de données**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image9.png)

1.  Cliquez sur l’icône de téléchargement ![](./media/storsimple-ova-deploy2-provision-vmware/image10.png) , puis sélectionnez **Télécharger un fichier**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image11.png)

1.  Vous devez maintenant parcourir et pointer vers les fichiers VMDK que vous avez téléchargé. Il y a deux fichiers. Sélectionnez un fichier à télécharger.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image12m.png)

1.  Cliquez sur **Ouvrir**. Cela va démarrer le téléchargement du fichier VMDK au magasin de données spécifiée. Il peut prendre plusieurs minutes pour que le fichier à télécharger.


1.  Une fois le téléchargement terminé, vous verrez le fichier dans le magasin de données dans le dossier que vous avez créé. 

    ![](./media/storsimple-ova-deploy2-provision-vmware/image14.png)

    Vous devrez maintenant télécharger le fichier VMDK deuxième dans le même magasin de données.

1.  Revenir à la fenêtre du client vSphere. Serveur ESXi sélectionné, avec le bouton droit et sélectionnez **nouvelle Machine virtuelle**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image15.png)

1.  Une fenêtre **créer un nouvel ordinateur virtuel** s’affiche. Dans la page **Configuration** , sélectionnez l’option **personnalisée** . Cliquez sur **suivant**.
    ![](./media/storsimple-ova-deploy2-provision-vmware/image16.png)

2.  Dans la page **nom et un emplacement** , spécifiez le nom de votre machine virtuelle. Ce nom doit correspondre au nom de dossier (méthode recommandée) que vous avez spécifié à l’étape 8.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image17.png)

1.  Dans la page de **stockage** , sélectionnez un magasin de données que vous souhaitez utiliser pour configurer votre machine virtuelle.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image18.png)

1.  Dans la page de la **Version de Machine virtuelle** , sélectionnez **Machine virtuelle Version : 8**. Notez que les versions 8 à 11 sont compatibles.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image19.png)

1.  Dans la page **système d’exploitation invité** , sélectionnez un **système d’exploitation invité** **Windows**. **Version**, dans la liste déroulante, sélectionnez **Microsoft Windows Server 2012 (64 bits)**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image20.png)

1.  Dans la page **processeurs** , ajustez le **nombre de sockets virtuels** et le **nombre de cœurs par socket virtuel** afin que le **nombre Total de cœurs** est 4 (ou plus). Cliquez sur **suivant**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image21.png)

1.  Dans la page de la **mémoire** , spécifiez 8 Go (ou plus) de RAM. Cliquez sur **suivant**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image22.png)

1.  Dans la page **réseau** , indiquez le nombre des interfaces réseau. La configuration minimale requise est une interface réseau.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image23.png)

1.  Dans la page **Contrôleur SCSI** , acceptez le **contrôleur LSI logique sa**par défaut.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image24.png)

1.  Dans la page **Sélectionner un disque** , sélectionnez **utiliser un disque virtuel existant**. Cliquez sur **suivant**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image25.png)

1.  Dans la page **Sélectionner un disque existant** , sous **Chemin d’accès du fichier de disque**, cliquez sur **Parcourir**. Cette action ouvre une boîte de dialogue **Parcourir les banques de données** . Accédez à l’emplacement où vous avez téléchargé le VMDK. Vous verrez maintenant qu’un seul fichier dans le magasin de données que les deux fichiers que vous avez téléchargée initiale ont été fusionnées. Sélectionnez le fichier, puis cliquez sur **OK**. Cliquez sur **suivant**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image26.png)

1.  Dans la page **Options avancées** , acceptez la valeur par défaut, puis cliquez sur **suivant**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image27.png)

1.  Dans la page **prêt à achevé** , passez en revue tous les paramètres associés à la nouvelle machine virtuelle. Vérifiez que **Modifier les paramètres de la machine virtuelle avant la fin**. Cliquez sur **Continuer**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image28.png)

1.  Dans la page **Propriétés de Machines virtuelles** , dans l’onglet **matériel** , recherchez le matériel du périphérique. Sélectionnez **nouveau disque dur**. Cliquez sur **Ajouter**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image29.png)

1.  Cela affiche la fenêtre **d’Ajout de matériel** . Dans la page **Type d’appareil** , sous **Choisissez le type d’appareil que vous voulez ajouter**, sélectionnez le **disque dur** , puis cliquez sur **suivant**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image30.png)

1.  Dans la page **Sélectionner un disque** , sélectionnez **créer un nouveau disque virtuel**. Cliquez sur **suivant**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image31.png)

1.  Dans la page **créer un disque** , modifier la **Taille du disque** 500 Go (ou plus). Sous **Mise en service de disque**, sélectionnez **Mise en service fine**. Cliquez sur **suivant**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image32.png)

1.  Dans la page **Options avancées** , acceptez la valeur par défaut.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image33.png)

1.  Dans la page **prêt à achevé** , passez en revue les options de disque. Cliquez sur **Terminer**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image34.png)

1.  Vous revenez ensuite à la page Propriétés de la Machine virtuelle. Un nouveau disque dur est ajouté à votre machine virtuelle. Cliquez sur **Terminer**.
  
    ![](./media/storsimple-ova-deploy2-provision-vmware/image35.png)

2.  Avec votre machine virtuelle sélectionnée dans le volet droit, accédez à l’onglet **Résumé** . Passez en revue les paramètres de votre machine virtuelle.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image36.png)

Votre machine virtuelle est maintenant mis en service. L’étape suivante consiste à obtenir l’adresse IP et de power sur cet ordinateur.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Étape 3 : Démarrer le périphérique virtuel et obtenir l’adresse IP

Procédez comme suit pour démarrer votre périphérique virtuel et s’y connecter.

#### <a name="to-start-the-virtual-device"></a>Pour démarrer le périphérique virtuel

1.  Démarrez le périphérique virtuel. Dans le Gestionnaire de Configuration, vSphere dans le volet gauche, sélectionnez votre appareil et avec le bouton droit pour afficher le menu contextuel. Sélectionnez **Power** , puis **sous tension**. Cela doit s’allumer votre machine virtuelle. Vous pouvez afficher l’état dans le volet de **Tâches récentes** en bas du client vSphere.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image37.png)

1.  Les tâches de configuration prendra quelques minutes pour terminer. Une fois que l’appareil est en cours d’exécution, accédez à l’onglet **Console** . Envoyer Ctrl + Alt + Suppr pour vous connecter à l’appareil. Vous pouvez également, pointez le curseur dans la fenêtre de la console et appuyez sur Ctrl + Alt + Inser. L’utilisateur par défaut est *StorSimpleAdmin* et le mot de passe par défaut est *le mot de passe1*.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image38.png)

1.  Pour des raisons de sécurité, le mot de passe administrateur appareil expire en premier. Vous devrez modifier le mot de passe.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image39.png)

1.  Entrez un mot de passe qui contient au moins 8 caractères. Le mot de passe doit contenir 3 sur 4 de ces exigences : majuscules, minuscules, numériques et caractères spéciaux. Retapez le mot de passe pour le confirmer. Vous serez informé que le mot de passe a changé.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image40.png)

1.  Une fois que le mot de passe est correctement modifiée, le périphérique virtuel peut redémarrer. Attendez que le redémarrage terminer. La console Windows PowerShell du périphérique peut être affichée en même temps que d’une barre de progression.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image41.png)

1.  Les étapes 6 à 8 s’appliquent uniquement lors du démarrage vers le haut dans un environnement non DHCP. Si vous êtes dans un environnement DHCP, ignorez ces étapes, puis passez à l’étape 9. Si vous avez démarré votre appareil dans un environnement non DHCP, vous verrez l’écran suivant. 

    ![](./media/storsimple-ova-deploy2-provision-vmware/image42m.png)

    Maintenant, vous devrez configurer le réseau.

1.  Utiliser la `Get-HcsIpAddress` commande pour répertorier les interfaces réseau activés sur votre appareil virtuel. Si votre appareil dispose d’une seule interface réseau activée, le nom par défaut affecté à cette interface est `Ethernet`.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image43m.png)

1.  Utiliser la `Set-HcsIpAddress` applet de commande pour configurer le réseau. Voici un exemple :


    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-ova-deploy2-provision-vmware/image44.png)

1.  Une fois la configuration initiale terminée et l’appareil a démarré, vous verrez le texte de bannière appareil. Notez l’adresse IP et l’URL affichée dans le texte de bannière à gérer le périphérique. Vous allez utiliser cette adresse IP pour se connecter à l’interface utilisateur de votre périphérique virtuel web et procéder à la mise en page et enregistrement.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image45.png)


1. (Facultatif) Exécuter cette étape uniquement si vous déployez votre appareil dans le Cloud pour le gouvernement. Vous allez maintenant activer le mode États-Unis FIPS Federal Information Processing Standard () sur votre appareil. La norme FIPS 140 définit les algorithmes de chiffrement approuvées par le Federal nous systèmes informatiques pour le gouvernement pour la protection des données sensibles.
    1. Pour activer le mode FIPS, exécutez l’applet de commande suivante :
        
        `Enter-HcsFIPSMode`

    2. Redémarrez votre appareil après avoir activé le mode FIPS pour que les validations cryptographiques prennent effet.

        > [AZURE.NOTE] Vous pouvez activer ou désactiver le mode FIPS sur votre appareil. En alternance le périphérique entre les modes FIPS et non-FIPS n’est pas pris en charge.


Si votre appareil ne répond pas à la configuration minimale requise, vous verrez une erreur dans le texte de bannière (illustré ci-dessous). Vous devez modifier la configuration du périphérique de sorte qu’il dispose des ressources appropriées pour la configuration minimale requise. Vous pouvez ensuite redémarrer et se connecter à l’appareil. Reportez-vous à la configuration minimale requise dans [étape 1 : Assurez-vous que le système hôte répond aux conditions minimales périphérique virtuel](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-ova-deploy2-provision-vmware/image46.png)

Si vous rencontrez d’autres erreurs lors de la configuration initiale à l’aide de l’interface utilisateur web local, consultez le flux de travail suivants dans [Gérer votre tableau virtuel StorSimple à l’aide de l’interface utilisateur web local](storsimple-ova-web-ui-admin.md).

-   Exécuter les tests de diagnostic pour [résoudre les problèmes d’installation de l’interface utilisateur web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).

-   [Package de journal générer et afficher les fichiers journaux](storsimple-ova-web-ui-admin.md#generate-a-log-package)...

## <a name="next-steps"></a>Étapes suivantes

-   [Configurez votre tableau virtuel StorSimple comme un serveur de fichiers](storsimple-ova-deploy3-fs-setup.md)

-   [Configurez votre tableau virtuel StorSimple comme un serveur iSCSI](storsimple-ova-deploy3-iscsi-setup.md)

