<properties
   pageTitle="Déployer StorSimple virtuelle tableau 3 : configurer le périphérique virtuel du serveur de fichiers"
   description="Ce didacticiel troisième dans déploiement tableau virtuel StorSimple vous demande de configurer un appareil virtuel comme serveur de fichiers."
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
   ms.date="05/26/2016"
   ms.author="alkohli"/>

# <a name="deploy-storsimple-virtual-array---set-up-as-file-server"></a>Déployer StorSimple virtuelle matrice - définir en tant que serveur de fichiers

![](./media/storsimple-ova-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>Introduction 

Cet article s’applique à Microsoft Azure StorSimple tableau virtuel (également connu sous le périphérique virtuel StorSimple local ou un périphérique virtuel StorSimple) en cours d’exécution mars 2016 officielle (disponibilité générale) version. Cet article décrit comment effectuer la configuration initiale, enregistrer votre serveur de fichiers StorSimple, terminer l’installation de l’appareil et créer et se connecter aux partages de PME. Cet article est le dernier dans la série de didacticiels de déploiement requis pour le déploiement complètement votre tableau virtuel comme un serveur de fichiers ou un serveur iSCSI.

Le processus d’installation et de configuration peut prendre environ 10 minutes pour terminer.


## <a name="setup-prerequisites"></a>Paramétrage des conditions préalables

Avant de configurer et configurer votre appareil virtuel StorSimple, vérifiez que :

-   Vous avez configuré un périphérique virtuel et connecté à celui-ci comme indiqué dans la [mise en service un tableau virtuel StorSimple dans Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) ou la [mise en service un tableau virtuel StorSimple dans VMware](storsimple-ova-deploy2-provision-vmware.md).

-   Vous disposez de la clé de l’enregistrement de service à partir du service Manager StorSimple que vous avez créé pour gérer les appareils virtuels StorSimple. Pour plus d’informations, voir [étape 2 : obtenir la clé d’inscription service](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key) pour tableau virtuel StorSimple.

-   Si c’est le périphérique virtuel deuxième ou ultérieure que vous enregistrez avec un service Manager StorSimple existant, vous devez avoir la clé de chiffrement des données de service. Cette clé a été générée lors de la première unité a été correctement inscrite grâce à ce service. Si vous avez perdu cette clé, voir [obtenir la clé de chiffrement des données de service](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) pour votre tableau virtuel StorSimple.

## <a name="step-by-step-setup"></a>Configuration étape par étape

Utilisez la procédure à suivre pour installer et configurer votre périphérique virtuel StorSimple.

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Étape 1 : Terminer l’installation de l’interface utilisateur web local et inscrire votre appareil 


#### <a name="to-complete-the-setup-and-register-the-device"></a>Pour terminer l’installation et inscrire l’appareil

1.  Ouvrez une fenêtre de navigateur et connectez-vous à l’interface utilisateur web local. Type : 

    `https://<ip-address of network interface>`

    Utiliser l’URL de connexion de noter à l’étape précédente. Vous voyez une erreur indiquant qu’il existe un problème avec le certificat de sécurité du site Web. Cliquez sur **Continuer pour cette page Web**.

    ![](./media/storsimple-ova-deploy3-fs-setup/image2.png)

1.  Connectez-vous à l’interface utilisateur de votre périphérique virtuel web en tant que **StorSimpleAdmin**. Entrez le mot de passe appareil administrateur que vous avez modifié à l’étape 3 : démarrer le périphérique virtuel dans [mise en service un tableau virtuel StorSimple dans Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) ou dans [un tableau virtuel StorSimple dans VMware disposition](storsimple-ova-deploy2-provision-vmware.md).

    ![](./media/storsimple-ova-deploy3-fs-setup/image3.png)

1.  Vous serez sur la page **d’accueil** . Cette page décrit les différents paramètres requises pour configurer et inscrire le périphérique virtuel auprès du service Manager StorSimple. Notez que les **paramètres réseau**, **les paramètres de proxy Web**et **les paramètres de temps** sont facultatives. Les seuls paramètres requis sont **les paramètres de périphérique** et **Cloud**.

    ![](./media/storsimple-ova-deploy3-fs-setup/image4.png)

1.  Dans la page **paramètres du réseau** sous **interfaces réseau**, données 0 seront automatiquement configurées pour vous. Chaque interface réseau est défini par défaut pour obtenir adresse IP automatiquement (DHCP). Par conséquent, une adresse IP, du sous-réseau et passerelle seront automatiquement affectés (pour IPv4 et IPv6).

    ![](./media/storsimple-ova-deploy3-fs-setup/image5.png)

    Si vous avez ajouté plusieurs interfaces réseau lors de la mise en service du périphérique, vous pouvez les configurer ici. Remarque Vous pouvez configurer votre interface de réseau IPv4 uniquement ou en tant que IPv4 et IPv6. Seules les configurations du protocole IPv6 ne sont pas pris en charge.

1.  Serveurs DNS sont requis car ils sont utilisés lorsque votre appareil tente de communiquer avec votre fournisseurs de service de stockage cloud ou pour résoudre votre appareil par nom lorsqu’il est configuré comme un serveur de fichiers. Dans la page **paramètres du réseau** sous les **serveurs DNS**:

    1.  Un serveur DNS principal et secondaire sera configuré automatiquement. Si vous choisissez de configurer les adresses IP, vous pouvez spécifier des serveurs DNS. Pour une disponibilité élevée, nous vous recommandons de configurer un serveur principal et un serveur DNS secondaire.

    2.  Cliquez sur **Appliquer**. Cela s’appliquent et validez les paramètres réseau.

2.  Dans la page **paramètres de l’appareil** :

    1.  Attribuer un **nom** unique à votre appareil. Ce nom peut contenir 1-15 caractères et peut contenir des lettres, des chiffres et des traits d’union.

    2.  Cliquez sur l’icône de **serveur de fichiers** ![](./media/storsimple-ova-deploy3-fs-setup/image6.png) correspondant au **Type** d’appareil que vous créez. Un serveur de fichiers vous permettra de créer des dossiers partagés.

    3.  Que votre périphérique est un serveur de fichiers, vous devez joindre le périphérique à un domaine. Entrez un **nom de domaine**.

    1.  Cliquez sur **Appliquer**.

2.  Une boîte de dialogue s’affiche. Entrez vos informations d’identification de domaine au format spécifié. Cliquez sur l’icône de vérification. Les informations d’identification de domaine seront vérifiées. Vous verrez un message d’erreur si les informations d’identification sont incorrectes.

    ![](./media/storsimple-ova-deploy3-fs-setup/image7.png)

1.  Cliquez sur **Appliquer**. Cela s’appliquent et validez les paramètres du périphérique.

    ![](./media/storsimple-ova-deploy3-fs-setup/image8.png)

    > [AZURE.NOTE]
    > 
    > Assurez-vous que votre tableau virtuel concerne dans sa propre unité d’organisation (OU) Active Directory et aucun objet de stratégie de groupe (stratégie de groupe) est appliqués ou hérités. Stratégie de groupe peut-être installer des applications tel qu’un logiciel antivirus sur le tableau virtuel StorSimple. Installer des logiciels supplémentaires n’est pas prise en charge et peut entraîner une altération des données. 

1.  (Facultatif) configurer votre serveur de proxy web. Configuration du proxy web est facultative, n’oubliez pas que si vous utilisez un proxy web, vous pouvez uniquement configurez-le ici.

    ![](./media/storsimple-ova-deploy3-fs-setup/image9.png)

    Dans la page **proxy Web** :

    1.  Fournir l' **URL du proxy Web** au format suivant : *http://&lt;hôte adresse IP ou FQDN&gt;: numéro de Port*. Notez que les URL HTTPS ne sont pas pris en charge.

    2.  Spécifier **l’authentification** en tant que **base** ou **Aucun**.

    3.  Si l’authentification, vous devrez également fournir un **nom d’utilisateur** et **mot de passe**.

    4.  Cliquez sur **Appliquer**. Cela valider et appliquer les paramètres de proxy web configurés.

1.  (Facultatif) configurer les paramètres de temps pour votre appareil, tels que le fuseau horaire et les serveurs NTP principales et secondaires. Serveurs NTP sont requis, car votre appareil doit synchroniser heure afin qu’il peut s’authentifier avec vos fournisseurs de services cloud.

    ![](./media/storsimple-ova-deploy3-fs-setup/image10.png)

    Dans la page **paramètres de temps** :

    1.  Dans la liste déroulante, sélectionnez le **fuseau horaire** selon l’emplacement géographique dans lequel l’appareil est déployé. Le fuseau horaire par défaut pour votre appareil est PST. Votre périphérique utilisera ce fuseau horaire pour toutes les opérations planifiées.

    2.  Spécifier un **serveur NTP principal** pour votre appareil ou acceptez la valeur par défaut time.windows.com. Assurez-vous que votre réseau autorise le trafic NTP passer de votre centre de données à Internet.

    3.  Vous pouvez également spécifier un **serveur NTP secondaire** pour votre appareil.

    4.  Cliquez sur **Appliquer**. Cela valider et appliquer les paramètres de temps configurée.

1.  Configurer les paramètres de cloud pour votre appareil. Dans cette étape, vous sera terminer la configuration de périphérique local, puis enregistrez le périphérique avec votre service Manager StorSimple.

    1.  Entrez la **clé d’inscription Service** que vous vous trouvez dans [étape 2 : obtenir la clé d’inscription service](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key) pour tableau virtuel StorSimple.

    2.  Ignorer cette étape s’il s’agit de votre appareil premier enregistrement grâce à ce service et accédez à l’étape suivante. Si ce n’est pas le premier appareil que vous enregistrez grâce à ce service, vous devrez fournir la **clé de chiffrement de données de Service**. Cette clé est requise avec la clé d’inscription de service pour enregistrer des périphériques supplémentaires avec le service Manager StorSimple. Pour plus d’informations, consultez pour obtenir la [clé de chiffrement de données de service](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) sur votre site web local l’interface utilisateur.

    3.  Cliquez sur **Enregistrer**. Cela va redémarrer l’appareil. Vous devrez peut-être patienter pendant 2-3 minutes avant que l’appareil est inscrit correctement. Après le redémarrage de l’appareil, vous allez être dirigé vers la page de connexion.

        ![](./media/storsimple-ova-deploy3-fs-setup/image13.png)
    

1.  Revenez au portail classique Azure. Dans la page **appareils** , vérifiez que l’appareil a réussi à connecter au service en recherchant l’état. L’état de l’appareil doit être **Active**.

![](./media/storsimple-ova-deploy3-fs-setup/image12.png)

## <a name="step-2-complete-the-required-device-setup"></a>Étape 2 : Terminer l’installation de périphérique requis

Pour terminer la configuration du périphérique de votre appareil StorSimple, vous devez :

-   Sélectionnez un compte de stockage à associer à votre appareil.

-   Sélectionnez paramètres de chiffrement pour les données envoyées vers le cloud.

Effectuez les opérations suivantes dans le [portail classique Azure](https://manage.windowsazure.com/) pour terminer l’installation de périphérique requis.

#### <a name="to-complete-the-minimum-device-setup"></a>Pour terminer l’installation de périphériques minimum

1.  Dans la page **périphériques** , sélectionnez le périphérique que vous venez de créer. Cet appareil serait apparaissant comme étant **actif**. Cliquez sur la flèche sur le nom du périphérique, puis sur **Démarrage rapide**.

2.  Cliquez sur **le programme d’installation complète des périphériques** pour démarrer l’Assistant Appareil configurer.

3.  Dans l’Assistant Appareil configurer dans la page **Paramètres de base** , procédez comme suit :

    1.  Spécifiez un compte de stockage à utiliser avec votre appareil. Vous pouvez sélectionner un compte de stockage existant dans cet abonnement dans la liste déroulante ou spécifier **Ajouter plus** pour choisir un compte à partir d’un autre abonnement.

    2.  Définir les paramètres de chiffrement pour tous les le données au repos (chiffrement AES) qui sera envoyé dans le cloud. Pour chiffrer vos données, cochez la case liste déroulante pour **Activer la clé de chiffrement de stockage cloud**. Entrez un chiffrement de stockage cloud contenant 32 caractères. Entrez à nouveau la clé pour le confirmer. Une clé AES 256 bits sera utilisée avec la touche définies par l’utilisateur pour le chiffrement.

    3.  Cliquez sur l’icône du ![](./media/storsimple-ova-deploy3-fs-setup/image15.png).

        ![](./media/storsimple-ova-deploy3-fs-setup/image16.png)

Les paramètres seront maintenant mise à jour. Une fois que les paramètres sont mis à jour, le bouton de mise en dispositif complet sera grisé. Vous renverra à la page de **Démarrage rapide** d’appareil.

 ![](./media/storsimple-ova-deploy3-fs-setup/image17.png)


> [AZURE.NOTE]                                                              
>
> Vous pouvez modifier tous les autres paramètres d’appareil à tout moment en accédant à la page **configurer** .

## <a name="step-3-add-a-share"></a>Étape 3 : Ajouter un partage

Effectuez les opérations suivantes dans le [portail classique Azure](https://manage.windowsazure.com/) pour créer un partage.

#### <a name="to-create-a-share"></a>Pour créer un partage

1.  Dans la page de **Démarrage rapide** de périphérique, cliquez sur **Ajouter un partage**. Cette opération démarre l’Assistant Ajouter un partage.

    ![](./media/storsimple-ova-deploy3-fs-setup/image17.png)

1.  Dans la page **Paramètres de base** , procédez comme suit :

    1.  Spécifiez un nom unique pour le partage. Le nom doit être une chaîne qui contient des caractères 3 à 127.

    2.  (Facultatif) Fournir une description pour le partage. La description aidera à identifier les propriétaires de partage.

    3.  Sélectionnez un type d’utilisation pour le partage. Le type d’utilisation peut être **hiérarchisé** ou **épinglées localement**, hiérarchisé étant la valeur par défaut. Pour les charges de travail nécessitant garanties locales basse latence et améliorer les performances, sélectionnez un partage **localement épinglées** . Pour toutes les autres données, sélectionnez un partage **hiérarchisé** .

    Un partage localement épinglé est configuré thickly et garantit que les données principales sur le partage restent locales à l’appareil et ne pas renverser dans le cloud. Un partage hiérarchisé quant à eux est légèrement sa mise en service. Lorsque vous créez un partage hiérarchisé, 10 % de l’espace est mis en service au niveau local et 90 % de l’espace est mis en service dans le cloud. Par exemple, si vous sa mise en service un volume de 1 To, 100 Go peut résider dans l’espace local et 900 Go doit être utilisée dans le cloud lorsque les niveaux de données. Cela implique à son tour si vous manquez tout l’espace local sur l’appareil, vous devez ne peut pas configurer un partage de plusieurs niveaux.

1.  Spécifier la capacité générée pour votre partage. Notez que la capacité spécifiée doit être inférieure à la capacité disponible. Si vous utilisez un partage de plusieurs niveaux, la taille de partage doit être comprise entre 500 Go et 20 To. Pour un partage localement épinglé, spécifiez une taille de partager entre 50 Go et 2 To. Utiliser la capacité disponible comme un guide de mise en service un partage. Si la capacité locale disponible est 0 Go, puis vous ne pas être autorisé en service des partages hiérarchisés ou locales.

    ![](./media/storsimple-ova-deploy3-fs-setup/image18.png)

1.  Cliquez sur l’icône de flèche ![](./media/storsimple-ova-deploy3-fs-setup/image19.png) pour accéder à la page suivante.

1.  Dans la page **Des paramètres supplémentaires** , attribuer les autorisations pour l’utilisateur ou le groupe auquel l’accès à ce partage. Indiquez le nom de l’utilisateur ou groupe d’utilisateurs dans *<john@contoso.com>* format. Nous vous recommandons d’utiliser un groupe d’utilisateurs (au lieu d’un seul utilisateur) pour permettre les privilèges d’administrateur accéder à ces partages. Une fois que vous avez attribué les autorisations ici, vous pouvez ensuite utiliser l’Explorateur Windows pour modifier les autorisations suivantes.

    ![](./media/storsimple-ova-deploy3-fs-setup/image20.png)

1.  Cliquez sur l’icône du ![](./media/storsimple-ova-deploy3-fs-setup/image21.png). Un partage est créé avec les paramètres spécifiés. Par défaut, de surveillance et de la sauvegarde seront être activés pour le partage.

## <a name="step-4-connect-to-the-share"></a>Étape 4 : Se connecter au partage

Vous devez maintenant pour vous connecter aux partages que vous avez créé à l’étape précédente. Effectuer ces étapes sur votre hôte Windows Server.

#### <a name="to-connect-to-the-share"></a>Pour vous connecter au partage

1.  Appuyez sur la touche ![](./media/storsimple-ova-deploy3-fs-setup/image22.png) + r. Dans la fenêtre Exécuter, spécifiez la * \\ * en tant que le chemin d’accès, remplaçant *nom de serveur de fichier* par le nom de l’appareil que vous avez affecté à votre serveur de fichiers. Cliquez sur **OK**.

    ![](./media/storsimple-ova-deploy3-fs-setup/image23.png)

2.  Explorer s’ouvre. Vous devriez maintenant pouvoir voir les actions que vous avez créé sous forme de dossiers. Sélectionnez et double-cliquez sur un partage (dossier) pour afficher le contenu.

    ![](./media/storsimple-ova-deploy3-fs-setup/image24.png)

3.  Vous pouvez ensuite ajouter des fichiers à ces actions et effectuer une sauvegarde.

![icône de vidéo](./media/storsimple-ova-deploy3-fs-setup/video_icon.png) **vidéo disponible**

Regardez la vidéo pour découvrir comment vous pouvez configurer et inscrire un tableau virtuel StorSimple comme un serveur de fichiers.

> [AZURE.VIDEO configure-a-storsimple-virtual-array]

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment utiliser l’interface utilisateur web local pour [administrer votre tableau virtuel StorSimple](storsimple-ova-web-ui-admin.md).
