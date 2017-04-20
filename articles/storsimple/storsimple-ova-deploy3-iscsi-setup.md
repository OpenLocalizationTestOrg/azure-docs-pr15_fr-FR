<properties 
   pageTitle="Le programme d’installation de tableau virtuel StorSimple iSCSI server | Microsoft Azure"
   description="Décrit comment effectuer la configuration initiale, enregistrez votre serveur iSCSI StorSimple et terminer l’installation de l’appareil."
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
   ms.date="07/18/2016"
   ms.author="alkohli" />


# <a name="deploy-storsimple-virtual-array--set-up-your-virtual-device-as-an-iscsi-server"></a>Déployer StorSimple virtuelle tableau – configurer votre périphérique virtuel comme un serveur iSCSI

![flux de processus de configuration iSCSI](./media/storsimple-ova-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel déploiement s’applique à la version officielle (disponibilité générale) mars 2016 en cours d’exécution de Microsoft Azure StorSimple tableau virtuel (également connu sous le périphérique de virtuel StorSimple locaux ou le périphérique virtuel StorSimple). Ce didacticiel décrit comment effectuer la configuration initiale, enregistrer votre serveur iSCSI StorSimple, terminer l’installation de l’appareil, puis créer, monter, initialisation et mettre en forme des volumes sur votre serveur StorSimple périphérique virtuel iSCSI. Les informations de configuration StorSimple dans cet article s’applique uniquement aux tableaux virtuel StorSimple. 

Les procédures décrites prennent ici environ 30 minutes sur 1 heure pour terminer. Les informations publiées dans cet article s’applique uniquement aux tableaux virtuel StorSimple.

## <a name="setup-prerequisites"></a>Paramétrage des conditions préalables

Avant de configurer et configurer votre appareil virtuel StorSimple, vérifiez que :

- Vous avez configuré un périphérique virtuel et connecté à celui-ci, comme décrit dans [Déployer StorSimple virtuel tableau - disposition un tableau virtuel dans Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) ou [Déployer StorSimple virtuel tableau - disposition un tableau virtuel dans VMware](storsimple-ova-deploy2-provision-vmware.md).

- Vous disposez de la clé de l’enregistrement de service à partir du service Manager StorSimple que vous avez créé pour gérer les appareils virtuels StorSimple. Pour plus d’informations, voir **étape 2 : obtenir la clé d’inscription service** dans [Déployer StorSimple tableau virtuel - préparer le portail](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key).

- Si c’est le périphérique virtuel deuxième ou ultérieure que vous enregistrez avec un service Manager StorSimple existant, vous devez avoir la clé de chiffrement des données de service. Cette clé a été générée lors de la première unité a été correctement inscrite grâce à ce service. Si vous avez perdu cette clé, voir **obtenir la clé de chiffrement des données de service** en cours [d’utilisation de l’interface utilisateur Web pour administrer votre tableau virtuel StorSimple](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key).

## <a name="step-by-step-setup"></a>Configuration étape par étape 

Utilisez la procédure à suivre pour installer et configurer votre périphérique virtuel StorSimple :

-  [Étape 1 : Terminer l’installation de l’interface utilisateur web local et inscrire votre appareil](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
-  [Étape 2 : Terminer l’installation de périphérique requis](#step-2-complete-the-required-device-setup)
-  [Étape 3 : Ajouter un volume](#step-3-add-a-volume)
-  [Étape 4 : Monter, initialisation et mettre en forme un volume](#step-4-mount-initialize-and-format-a-volume)  

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Étape 1 : Terminer l’installation de l’interface utilisateur web local et inscrire votre appareil 

#### <a name="to-complete-the-setup-and-register-the-device"></a>Pour terminer l’installation et inscrire l’appareil

1. Ouvrez une fenêtre de navigateur et connectez-vous à l’interface utilisateur web en tapant :

    `https://<ip-address of network interface>`

    Utiliser l’URL de connexion de noter à l’étape précédente. Vous verrez un message d’erreur vous informant qu’il existe un problème avec le certificat de sécurité du site Web. Cliquez sur **Continuer pour cette page web**.

    ![Erreur de certificat de sécurité](./media/storsimple-ova-deploy3-iscsi-setup/image3.png)

2. Connectez-vous à l’interface utilisateur de votre périphérique virtuel web en tant que **StorSimpleAdmin**. Entrez le mot de passe appareil administrateur que vous avez modifié à l’étape 3 : démarrer le périphérique virtuel dans [Déployer StorSimple virtuel tableau - disposition un périphérique virtuel dans Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) ou [Déployer StorSimple virtuel tableau - disposition un périphérique virtuel dans VMware](storsimple-ova-deploy2-provision-vmware.md).

    ![Page de connexion](./media/storsimple-ova-deploy3-iscsi-setup/image4.png)

3. Vous serez sur la page **d’accueil** . Cette page décrit les différents paramètres requises pour configurer et inscrire le périphérique virtuel auprès du service Manager StorSimple. Notez que les **paramètres réseau**, **les paramètres de proxy Web**et **les paramètres de temps** sont facultatives. Les seuls paramètres requis sont **les paramètres de périphérique** et **Cloud**.

    ![Page d’accueil](./media/storsimple-ova-deploy3-iscsi-setup/image5.png)

4. Dans la page **paramètres du réseau** sous **interfaces réseau**, données 0 seront automatiquement configurées pour vous. Chaque interface réseau est défini par défaut pour obtenir une adresse IP automatiquement (DHCP). Par conséquent, une adresse IP, sous-réseau et la passerelle seront automatiquement affectés (pour IPv4 et IPv6).

    Lorsque vous planifiez à déployer votre appareil comme un serveur iSCSI (pour la mise en service de stockage de blocs), nous vous recommandons de désactiver l’option **adresse IP obtenir automatiquement** et de configurer des adresses IP statiques.

    ![Page Paramètres du réseau](./media/storsimple-ova-deploy3-iscsi-setup/image6.png)

    Si vous avez ajouté plusieurs interfaces réseau lors de la mise en service du périphérique, vous pouvez les configurer ici. Remarque Vous pouvez configurer votre interface de réseau IPv4 uniquement ou en tant que IPv4 et IPv6. Seules les configurations du protocole IPv6 ne sont pas pris en charge.

5. Serveurs DNS sont requis, car ils sont utilisés lorsque votre appareil tente de communiquer avec vos fournisseurs de service de stockage cloud ou résoudre votre appareil en nom s’il est configuré comme un serveur de fichiers. Dans la page **paramètres du réseau** sous les **serveurs DNS**:

    1. Un serveur DNS principal et secondaire sera configuré automatiquement. Si vous choisissez de configurer les adresses IP, vous pouvez spécifier des serveurs DNS. Pour une disponibilité élevée, nous vous recommandons de configurer un serveur principal et un serveur DNS secondaire.

    2. Cliquez sur **Appliquer**. Cela s’appliquent et validez les paramètres réseau.

6. Dans la page **paramètres de l’appareil** :

    1. Attribuer un **nom** unique à votre appareil. Ce nom peut contenir 1-15 caractères et peut contenir des lettres, des chiffres et des traits d’union.

    2. Cliquez sur l’icône du **serveur iSCSI** ![l’icône du serveur iSCSI](./media/storsimple-ova-deploy3-iscsi-setup/image7.png) correspondant au **Type** d’appareil que vous créez. Un serveur iSCSI vous permettra de stockage de blocs de mise en service.

    3. Indiquez si vous souhaitez cet appareil à être associés à un domaine. Si votre périphérique est un serveur iSCSI, puis joindre le domaine est facultative. Si vous décidez de ne joindre pas votre serveur iSCSI à un domaine, cliquez sur **Appliquer**, attendez que les paramètres à appliquer et passez à l’étape suivante.

        Si vous souhaitez participer à l’appareil à un domaine. Entrez un **nom de domaine**, puis cliquez sur **Appliquer**.

        > [AZURE.NOTE] Si vous rejoignez votre serveur iSCSI à un domaine, vérifiez que votre tableau virtuel est dans sa propre unité d’organisation (OU) pour Microsoft Azure Active Directory et aucun objet de stratégie de groupe (stratégie de groupe) n’est appliquées à celui-ci.

    5. Une boîte de dialogue s’affiche. Entrez vos informations d’identification de domaine au format spécifié. Cliquez sur l’icône de vérification ![vérifier l’icône](./media/storsimple-ova-deploy3-iscsi-setup/image15.png). Les informations d’identification de domaine seront vérifiées. Vous verrez un message d’erreur si les informations d’identification sont incorrectes.

        ![informations d’identification](./media/storsimple-ova-deploy3-iscsi-setup/image8.png)

    6. Cliquez sur **Appliquer**. Cela s’appliquent et validez les paramètres du périphérique.
 
7. (Facultatif) configurer votre serveur de proxy web. Configuration du proxy web est facultative, n’oubliez pas que si vous utilisez un proxy web, vous pouvez uniquement configurez-le ici.

    ![configurer le proxy web](./media/storsimple-ova-deploy3-iscsi-setup/image9.png)

    Dans la page **proxy Web** :

    1. Fournir l' **URL du proxy Web** au format suivant : *http://host-IP adresse* ou *un numéro FDQN:Port*. Notez que les URL HTTPS ne sont pas pris en charge.

    2. Spécifier **l’authentification** en tant que **base** ou **Aucun**.

    3. Si vous utilisez l’authentification, vous devez également à fournir un **nom d’utilisateur** et **mot de passe**.

    4. Cliquez sur **Appliquer**. Cela valider et appliquer les paramètres de proxy web configurés.
 
8. (Facultatif) configurer les paramètres de temps pour votre appareil, tels que le fuseau horaire et les serveurs NTP principales et secondaires. Serveurs NTP sont requis, car votre appareil doit synchroniser heure afin qu’il peut s’authentifier avec vos fournisseurs de services cloud.

    ![Paramètres de temps](./media/storsimple-ova-deploy3-iscsi-setup/image10.png)

    Dans la page **paramètres de temps** :

    1. Dans la liste déroulante, sélectionnez le **fuseau horaire** selon l’emplacement géographique dans lequel l’appareil est déployé. Le fuseau horaire par défaut pour votre appareil est PST. Votre périphérique utilisera ce fuseau horaire pour toutes les opérations planifiées.

    2. Spécifier un **serveur NTP principal** pour votre appareil ou acceptez la valeur par défaut time.windows.com. Assurez-vous que votre réseau autorise le trafic NTP passer de votre centre de données à Internet.

    3. Vous pouvez également spécifier un **serveur NTP secondaire** pour votre appareil.

    4. Cliquez sur **Appliquer**. Cela valider et appliquer les paramètres de temps configurée.

9. Configurer les paramètres de cloud pour votre appareil. Dans cette étape, vous sera terminer la configuration de périphérique local, puis enregistrez le périphérique avec votre service Manager StorSimple.

    1. Entrez la **clé d’inscription Service** que vous vous trouvez dans **étape 2 : obtenir la clé d’inscription service** dans [Déployer StorSimple tableau virtuel - préparer le portail](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key).

    2. Si ce n’est pas le premier appareil que vous enregistrez grâce à ce service, vous devrez fournir la **clé de chiffrement de données de Service**. Cette clé est requise avec la clé d’inscription de service pour enregistrer des périphériques supplémentaires avec le service Manager StorSimple. Pour plus d’informations, reportez-vous à [obtenir la clé de chiffrement des données de service](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) sur votre site web local l’interface utilisateur.

    3. Cliquez sur **Enregistrer**. Cela va redémarrer l’appareil. Vous devrez peut-être patienter pendant 2-3 minutes avant que l’appareil est inscrit correctement. Après le redémarrage de l’appareil, vous allez être dirigé vers la page de connexion.

       ![Inscrire appareil](./media/storsimple-ova-deploy3-iscsi-setup/image11.png)

10. Revenez au portail classique Azure. Dans la page **appareils** , vérifiez que l’appareil a réussi à connecter au service en recherchant l’état. L’état de l’appareil doit être **Active**.

    ![Page de périphériques](./media/storsimple-ova-deploy3-iscsi-setup/image12.png)

## <a name="step-2-complete-the-required-device-setup"></a>Étape 2 : Terminer l’installation de périphérique requis

Pour terminer la configuration du périphérique de votre appareil StorSimple, vous devez :

- Sélectionnez un compte de stockage à associer à votre appareil.

- Sélectionnez paramètres de chiffrement pour les données envoyées vers le cloud.

Effectuez les opérations suivantes dans le portail classique Azure pour terminer l’installation de périphérique requis.

#### <a name="to-complete-the-minimum-device-setup"></a>Pour terminer l’installation de périphériques minimum

1. Dans la page **appareils mobiles** , sélectionnez le périphérique que vous venez de créer. Cet appareil serait apparaissant comme étant **actif**. Cliquez sur la flèche en regard du nom de l’appareil, puis sur **Démarrage rapide**.

    ![Page de périphériques](./media/storsimple-ova-deploy3-iscsi-setup/image13.png)

2. Cliquez sur **le programme d’installation complète des périphériques** pour démarrer l’Assistant Appareil configurer.

    ![Configurer l’Assistant de l’appareil](./media/storsimple-ova-deploy3-iscsi-setup/image14.png)

3. Dans l’Assistant de périphérique configurer, dans la page **Paramètres de base** , procédez comme suit :

   1. Spécifiez un compte de stockage à utiliser avec votre appareil. Dans cet abonnement, vous pouvez sélectionner un compte de stockage existant dans la liste déroulante, ou vous pouvez spécifier **Ajouter plus** pour choisir un compte à partir d’un autre abonnement.

   2. Définir les paramètres de chiffrement pour toutes les données au repos qui sera envoyé dans le cloud. (StorSimple utilise le chiffrement AES-256). Pour chiffrer vos données, activez la case à cocher **Activer le chiffrement stockage cloud** . Entrez un chiffrement de stockage cloud contenant 32 caractères. Entrez à nouveau la clé pour le confirmer.

   3. Cliquez sur l’icône de vérification ![vérifier l’icône](./media/storsimple-ova-deploy3-iscsi-setup/image15.png).

    ![Paramètres de base](./media/storsimple-ova-deploy3-iscsi-setup/image16.png)

    Les paramètres seront maintenant mise à jour. Une fois que les paramètres sont mis à jour, le bouton de configuration complète des périphériques n’est pas disponible. Vous renverra à la page de **Démarrage rapide** d’appareil.                                                        

>[AZURE.NOTE]Vous pouvez modifier tous les autres paramètres d’appareil à tout moment en accédant à la page **configurer** .

## <a name="step-3-add-a-volume"></a>Étape 3 : Ajouter un volume

Effectuez les opérations suivantes dans le portail classique Azure pour créer un volume.

#### <a name="to-create-a-volume"></a>Pour créer un volume

1. Dans la page de **Démarrage rapide** de périphérique, cliquez sur **Ajouter un volume**. Cette opération démarre l’Assistant Ajouter un volume.

2. Dans l’Assistant Ajouter un volume, sous **Paramètres de base**, procédez comme suit :

    1. Indiquez un nom unique pour le volume. Le nom doit être une chaîne qui contient des caractères 3 à 127.

    2. Fournir une description pour le volume. La description aidera à identifier les propriétaires de volume.

    3. Sélectionnez un type d’utilisation pour le volume. Le type d’utilisation peut être **volume hiérarchisé** ou **localement épinglées volume.** (**Volume hiérarchisé** est la valeur par défaut). Pour les charges de travail nécessitant garanties locales basse latence et améliorer les performances, sélectionnez **épinglé localement** **en volume**. Pour toutes les autres données, sélectionnez **hiérarchisé** **en volume**.

        Un volume localement épinglé est configuré thickly et garantit que les données principales dans le volume restent sur l’appareil et ne pas renverser dans le cloud. Si vous créez un volume localement épinglé, le périphérique recherchent l’espace disponible sur les niveaux locales pour prévoir un volume de la taille demandée. Création d’un volume localement épinglé peut-être nécessiter débordement des données existantes à partir de l’appareil dans le cloud, et le temps nécessaire pour créer le volume est peut-être long. La durée totale dépend de la taille du volume généré, bande passante réseau disponible et les données sur votre appareil.

        Un volume hiérarchisé est légèrement sa mise en service quant à eux et peut être créé très rapidement. Lorsque vous créez un volume hiérarchisé, environ 10 % de l’espace est mis en service au niveau local et 90 % de l’espace est mis en service dans le cloud. Par exemple, si vous sa mise en service un volume de 1 To, 100 Go peut résider dans l’espace local et 900 Go doit être utilisée dans le cloud lorsque les niveaux de données. Cela implique à son tour est que si vous manquez tout l’espace local sur l’appareil, vous devez ne peut pas configurer un partage hiérarchisé (comme les 10 % ne seront pas disponibles).

    4. Spécifier la capacité générée pour le volume. Notez que la capacité spécifiée doit être inférieure à la capacité disponible. Si vous créez un volume hiérarchisé, la taille doit être comprise entre 500 Go et 5 To. Pour un volume localement épinglé, spécifiez une taille de volume entre 50 et 500 Go. Utiliser la capacité disponible comme un guide pour un volume de mise en service. Si la capacité locale disponible est 0 Go, vous ne pas être autorisé à mettre en service un localement épinglés ou un volume hiérarchisé.

        ![Paramètres de base](./media/storsimple-ova-deploy3-iscsi-setup/image17.png)

    5. Cliquez sur l’icône de flèche ![icône de flèche](./media/storsimple-ova-deploy3-iscsi-setup/image18.png) Pour accéder à la page suivante.

3. Dans la page **Des paramètres supplémentaires** , ajoutez un nouvel enregistrement de contrôle d’accès (blocage) :

    1. Fournir un **nom** pour votre blocage.

    2. Sous **iSCSI nom**, bénéficient le protocole nom complet (IQN) de votre hôte Windows. Si vous n’avez pas le nom qualifié, accédez à [l’appendice a : obtenir le nom qualifié d’un hôte Windows Server](#appendix-a-get-the-iqn-of-a-windows-server-host).

    3. Nous vous recommandons d’activer une sauvegarde par défaut en activant la case à cocher **activer une sauvegarde par défaut pour ce volume** . La sauvegarde par défaut crée une stratégie qui s’exécute à chaque jour (heure de l’appareil) 22:30 et crée un instantané de cloud de ce volume.

        ![paramètres supplémentaires](./media/storsimple-ova-deploy3-iscsi-setup/image19.png)

    4. Cliquez sur l’icône de vérification ![vérifier l’icône](./media/storsimple-ova-deploy3-iscsi-setup/image15.png). Cette opération démarre le travail de création de volume. Vous verrez un message de progression semblable au suivant.

        ![message d’avancement](./media/storsimple-ova-deploy3-iscsi-setup/image20.png)

        Un volume est créé avec les paramètres spécifiés. Par défaut, la sauvegarde et surveillance seront être activés pour le volume.

    5. Pour vérifier que le volume a été créé, accédez à la page **Volumes** . Vous devriez voir le volume indiqué.

        ![](./media/storsimple-ova-deploy3-iscsi-setup/image21.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>Étape 4 : Monter, initialisation et mettre en forme un volume

Effectuez les opérations suivantes pour monter, initialisation et mettre en forme vos volumes StorSimple sur un hôte Windows Server.

#### <a name="to-mount-initialize-and-format-a-volume"></a>Monter, initialisation et mettre en forme un volume

1. Démarrez Microsoft iSCSI initiator.

2. Dans la fenêtre **iSCSI Initiator Properties** , sous l’onglet **recherche** , cliquez sur **Détecter le portail**.

    ![découvrir le portail](./media/storsimple-ova-deploy3-iscsi-setup/image22.png)

3. Dans la boîte de dialogue **Découvrir le portail cible** , indiquez l’adresse IP de votre interface réseau avec iSCSI, puis cliquez sur **OK**.

    ![Adresse IP](./media/storsimple-ova-deploy3-iscsi-setup/image23.png)

4. Dans la fenêtre **iSCSI Initiator Properties** , sous l’onglet **cibles** , recherchez les **cibles découvert**. (Chaque volume sera une cible découverte). L’état de l’appareil doit apparaître comme **inactif**.

    ![cibles découvertes](./media/storsimple-ova-deploy3-iscsi-setup/image24.png)

5. Sélectionnez un périphérique cible, puis cliquez sur **se connecter**. Une fois que le périphérique est connecté, l’état doit prendre **connecté**. (Pour plus d’informations sur l’utilisation de Microsoft iSCSI initiator, voir [installation et configuration de Microsoft iSCSI Initiator] [1].

    ![Sélectionnez le périphérique cible](./media/storsimple-ova-deploy3-iscsi-setup/image25.png)

6. Sur votre hôte Windows, appuyez sur la touche du Logo Windows + X, puis cliquez sur **exécuter**.

7. Dans la boîte de dialogue **exécuter** , tapez **Diskmgmt.msc**. Cliquez sur **OK**, et la boîte de dialogue **Gestion des disques** s’affiche. Le volet de droite affiche les volumes de votre hôte.

8. Dans la fenêtre de **Gestion des disques** , les volumes montés apparaît comme le montre l’illustration suivante. Avec le bouton droit le volume découvert (cliquez sur le nom du disque), puis cliquez sur **en ligne**.

    ![gestion des disques](./media/storsimple-ova-deploy3-iscsi-setup/image26.png)

9. Avec le bouton droit et sélectionnez **Initialisation disque**.

    ![initialisation disque 1](./media/storsimple-ova-deploy3-iscsi-setup/image27.png)

10. Dans la boîte de dialogue, sélectionnez l’ou les disques initialisation, puis cliquez sur **OK**.

    ![initialisation disque 2](./media/storsimple-ova-deploy3-iscsi-setup/image28.png)

11. L’Assistant Nouveau Volume Simple démarre. Sélectionnez une taille de disque, puis cliquez sur **suivant**.

    ![Assistant nouveau volume 1](./media/storsimple-ova-deploy3-iscsi-setup/image29.png)

12. Affecter une lettre de lecteur au volume, puis cliquez sur **suivant**.

    ![Assistant nouveau volume 2](./media/storsimple-ova-deploy3-iscsi-setup/image30.png)

13. Entrez les paramètres pour mettre en forme le volume. **Sur Windows Server, NTFS uniquement est pris en charge.** Définir l’Australie à 64 Ko. Fournir une étiquette pour le volume. Il est recommandé pour ce nom devant être identiques pour le nom de volume fourni sur votre appareil virtuel StorSimple. Cliquez sur **suivant**.

    ![Assistant nouveau volume 3](./media/storsimple-ova-deploy3-iscsi-setup/image31.png)

14. Vérifiez les valeurs pour le volume, puis cliquez sur **Terminer**.

    ![Assistant nouveau volume 4](./media/storsimple-ova-deploy3-iscsi-setup/image32.png)

    Les volumes seront affichent comme étant **en ligne** dans la page **Gestion des disques** .

    ![volumes en ligne](./media/storsimple-ova-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment utiliser l’interface utilisateur web local pour [administrer votre tableau virtuel StorSimple](storsimple-ova-web-ui-admin.md).

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>Annexe a : obtenir le nom qualifié d’un hôte Windows Server

Effectuez les opérations suivantes pour obtenir l’iSCSI complet nom (nom qualifié) d’un hôte Windows exécutant Windows Server 2012.

#### <a name="to-get-the-iqn-of-a-windows-host"></a>Pour obtenir le nom qualifié d’un hôte Windows

1. Démarrez Microsoft iSCSI initiator sur votre hôte Windows.

2. Dans la fenêtre **iSCSI Initiator Properties** , sous l’onglet **Configuration** , sélectionnez et copiez la chaîne dans le champ **Nom** .

    ![propriétés de l’initiateur iSCSI](./media/storsimple-ova-deploy3-iscsi-setup/image34.png)

2. Enregistrez cette chaîne.

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx



