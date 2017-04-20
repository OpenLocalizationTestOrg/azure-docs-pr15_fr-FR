<properties 
   pageTitle="Déployer Gestionnaire d’instantanés StorSimple | Microsoft Azure"
   description="Découvrez comment télécharger et installer le Gestionnaire d’instantanés StorSimple, un composant logiciel enfichable MMC pour la gestion des fonctionnalités de protection et la sauvegarde de données StorSimple."
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
   ms.workload="TBD"
   ms.date="05/24/2016"
   ms.author="v-sharos" />

# <a name="deploy-the-storsimple-snapshot-manager-mmc-snap-in"></a>Déployer le composant logiciel enfichable MMC Gestionnaire d’instantanés StorSimple

## <a name="overview"></a>Vue d’ensemble

Le Gestionnaire d’instantané StorSimple est un composant logiciel enfichable Microsoft Management Console (MMC) qui simplifie la protection des données et la gestion des sauvegardes dans un environnement de Microsoft Azure StorSimple. Avec StorSimple instantané Manager, vous pouvez gérer Microsoft Azure StorSimple en local et stockage cloud comme s’il s’agissait d’un système de stockage totalement intégré, et donc simplifier le processus de sauvegarde et restauration et réduire les coûts. 

Ce didacticiel décrit la configuration requise, ainsi que des procédures pour l’installation, la suppression et la mise à niveau gestionnaire d’instantanés StorSimple.

>[AZURE.NOTE] 
>
>- Vous ne pouvez pas utiliser Gestionnaire d’instantanés StorSimple pour gérer les tableaux virtuels Microsoft Azure StorSimple (également appelé StorSimple locaux périphériques virtuels).
>
>- Si vous envisagez d’installer StorSimple mise à jour 2 sur votre appareil StorSimple, veillez à télécharger la dernière version du Gestionnaire de capture instantanée StorSimple et installez-le **avant d’installer StorSimple mise à jour 2**. La dernière version du Gestionnaire de capture instantanée StorSimple une compatibilité descendante et fonctionne avec toutes les versions de Microsoft Azure StorSimple. Si vous utilisez la version précédente du Gestionnaire d’instantané StorSimple, vous devez mettre à jour (vous n’avez pas besoin de désinstaller la version précédente avant d’installer la nouvelle version).

## <a name="storsimple-snapshot-manager-installation"></a>Installation du Gestionnaire de capture instantanée StorSimple

Gestionnaire d’instantanés StorSimple peut être installé sur les ordinateurs qui exécutent Windows Server 2008 R2 SP1, de Windows Server 2012 ou de système d’exploitation Windows Server 2012 R2. Sur les serveurs exécutant Windows 2008 R2, vous devez également installer Windows Server 2008 SP1 et Windows Management Framework 3.0. 

Avant d’installer ou mettre à niveau le composant logiciel enfichable Gestionnaire d’instantanés StorSimple pour Microsoft Management Console (MMC), vérifiez que le serveur Microsoft Azure StorSimple appareil et hôte sont correctement configurés. 

## <a name="configure-prerequisites"></a>Configurer les conditions préalables

Les étapes suivantes fournissent une vue d’ensemble des tâches de configuration que vous devez effectuer avant d’installer le Gestionnaire d’instantanés StorSimple. Pour terminer la configuration de Microsoft Azure StorSimple et informations de configuration, y compris la configuration système requise et obtenir des instructions étape par étape, voir [déployer votre appareil StorSimple local](storsimple-deployment-walkthrough.md).

>[AZURE.IMPORTANT]Avant de commencer, consultez l' [aide-mémoire de configuration de déploiement](storsimple-deployment-walkthrough.md#deployment-configuration-checklist) et et [conditions préalables au déploiement](storsimple-deployment-walkthrough.md#deployment-prerequisites) dans [déployer votre appareil StorSimple local](storsimple-deployment-walkthrough.md).
> <br>
 
### <a name="before-you-install-storsimple-snapshot-manager"></a>Avant d’installer StorSimple instantané Manager

1. Décompresser, monter et connectez le périphérique Microsoft Azure StorSimple comme décrit dans [installer votre appareil StorSimple 8100](storsimple-8100-hardware-installation.md) ou [votre appareil StorSimple 8600](storsimple-8600-hardware-installation.md).

2. Vérifiez que votre ordinateur hôte exécute l’un des systèmes d’exploitation suivants :

    - Windows Server 2008 R2 (sur des serveurs exécutant Windows 2008 R2, vous devez également installer Windows Server 2008 SP1 et Windows Management Framework 3.0)
    - Windows Server 2012
    - Windows Server 2012 R2
 
    Pour un périphérique virtuel StorSimple, l’hôte doit être une Machine virtuelle de Microsoft Azure. 

3. Assurez-vous que vous répondent à toutes les exigences de configuration de Microsoft Azure StorSimple. Pour plus d’informations, voir [conditions préalables du déploiement](storsimple-deployment-walkthrough.md#deployment-prerequisites).

4. Connectez le périphérique à l’hôte et la configuration initiale. Pour plus d’informations, passez aux [étapes de déploiement](storsimple-deployment-walkthrough.md#deployment-steps).

5. Créer des volumes sur l’appareil, les affecter à l’hôte, puis vérifiez que l’hôte peut monter et les utiliser. Gestionnaire d’instantanés StorSimple prend en charge les types de volumes suivants : 

    - Volumes de base
    - Volumes simples
    - Volumes dynamiques
    - Volumes dynamiques en miroir (RAID 1)
    - Volumes cluster partagé
 
    Pour plus d’informations sur la création des volumes sur l’appareil StorSimple ou l’appareil virtuel StorSimple, accédez à [étape 6 : créer un volume](storsimple-deployment-walkthrough.md#step-6-create-a-volume), dans [déployer votre appareil StorSimple local](storsimple-deployment-walkthrough.md).

## <a name="install-a-new-storsimple-snapshot-manager"></a>Installer un nouveau gestionnaire de capture instantanée StorSimple

Avant d’installer le Gestionnaire d’instantanés StorSimple, vérifiez que les volumes que vous avez créé sur le périphérique StorSimple ou StorSimple périphérique virtuel sont monter initialisé et mis en forme comme décrit dans les [conditions préalables pour les configurer](#configure-prerequisites).

>[AZURE.IMPORTANT]
>
>- Pour un périphérique virtuel StorSimple, l’hôte doit être une Machine virtuelle de Microsoft Azure. 
>
>- L’hôte doit exécuter Windows 2008 R2, Windows Server 2012 ou Windows Server 2012 R2. Si votre serveur exécutant Windows Server 2008 R2, vous devez également installer Windows Server 2008 SP1 et Windows Management Framework 3.0.
>
>- Vous devez configurer une connexion iSCSI de l’hôte vers le périphérique StorSimple avant de vous connecter l’appareil à Gestionnaire d’instantanés StorSimple.

Suivez ces étapes pour effectuer une nouvelle installation du Gestionnaire de capture instantanée StorSimple. Si vous installez une mise à niveau, accédez à [mise à niveau ou réinstaller le Gestionnaire d’instantanés StorSimple](#upgrade-or-reinstall-storsimple-snapshot-manager).

- Étape 1 : Installer le Gestionnaire de StorSimple instantané 
- Étape 2 : Connecter Gestionnaire d’instantanés StorSimple à un appareil 
- Étape 3 : Vérifier la connexion à l’appareil 

###<a name="step-1-install-storsimple-snapshot-manager"></a>Étape 1 : Installer le Gestionnaire de StorSimple instantané

Procédez comme suit pour installer le Gestionnaire de capture instantanée StorSimple.

#### <a name="to-install-storsimple-snapshot-manager"></a>Pour installer le Gestionnaire de capture instantanée StorSimple

1. Télécharger le logiciel gestionnaire d’instantanés StorSimple (accéder au [Gestionnaire d’instantanés StorSimple](https://www.microsoft.com/download/details.aspx?id=44220) dans du Microsoft Download Center) et enregistrez-le localement sur l’hôte.

2. Dans l’Explorateur de fichiers, cliquez sur le dossier compressé, puis cliquez sur **extraire tous les**.

3. Dans la fenêtre **extraire les (compressés) dossiers** , dans la zone **Sélectionner une destination et extraire des fichiers** , tapez ou accédez à l’emplacement où vous voulez que dans le fichier à extraire. 

       >[AZURE.IMPORTANT] Vous devez installer le Gestionnaire d’instantanés StorSimple sur le lecteur C:.
 
4. Activez la case à cocher **Afficher les fichiers extraits lorsque vous avez terminé** , puis cliquez sur **Extraire**.

    ![Extraire, boîte de dialogue fichiers](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png) 

4. Lorsque l’extraction est terminée, le dossier de destination s’ouvre. Double-cliquez sur l’icône de mise en application qui apparaît dans le dossier de destination.

5. Lorsque le message **D’installation réussie** s’affiche, cliquez sur **Fermer**. Vous devriez voir l’icône Gestionnaire d’instantanés StorSimple sur votre bureau.

    ![icône du bureau](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png) 

### <a name="step-2-connect-storsimple-snapshot-manager-to-a-device"></a>Étape 2 : Connecter Gestionnaire d’instantanés StorSimple à un appareil

Procédez comme suit pour vous connecter Gestionnaire d’instantanés StorSimple à un appareil StorSimple.

#### <a name="to-connect-storsimple-snapshot-manager-to-a-device"></a>Pour vous connecter Gestionnaire d’instantanés StorSimple à un appareil

1. Cliquez sur l’icône Gestionnaire d’instantanés StorSimple sur votre bureau. La fenêtre Gestionnaire d’instantanés StorSimple s’affiche. La fenêtre contient un volet **étendue** , un volet de **résultats** et un volet **Actions** . 

    ![Interface utilisateur du Gestionnaire de capture instantanée StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png) 

    - Le volet **étendue** (le volet gauche) contient la liste des nœuds organisés dans une arborescence. Vous pouvez développer certains nœuds pour sélectionner un affichage ou les données spécifiques à ce nœud. Cliquez sur l’icône de flèche pour développer ou réduire un nœud. Clic droit sur un élément dans le volet **étendue** pour afficher la liste d’actions disponibles pour cet élément. 

    - Le volet de **résultats** (le volet central) contient des informations d’état détaillées sur le nœud, affichage ou les données que vous avez sélectionné dans le volet de **l’étendue** .

    - Le volet **Actions** répertorie les opérations que vous pouvez effectuer sur le nœud, affichage ou les données que vous avez sélectionné dans le volet de **l’étendue** .

    Pour une description complète de l’interface utilisateur StorSimple instantané Manager, voir [Gestionnaire d’instantanés StorSimple l’interface utilisateur](storsimple-use-snapshot-manager.md).

2. Dans le volet **étendue** , cliquez sur le nœud de **périphériques** , puis cliquez sur **configurer un appareil**. La boîte de dialogue **configurer un appareil** s’affiche.

    ![Configurer un appareil](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png) 

3. Dans la zone de liste **périphérique** , sélectionnez l’adresse IP du périphérique Microsoft Azure StorSimple ou du périphérique virtuel. Dans la zone de texte **mot de passe** , tapez le mot de passe Gestionnaire d’instantanés StorSimple que vous avez créé pour le périphérique dans le portail classique Azure. Cliquez sur **OK**.

4. Gestionnaire d’instantanés StorSimple recherche le périphérique que vous avez identifiés. Si l’appareil n’est disponible, gestionnaire d’instantanés StorSimple ajoute une connexion. Vous pouvez [vérifier la connexion à l’appareil](#to-verify-the-connection) pour confirmer que la connexion a été ajoutée avec succès.

    Si l’appareil n’est pas disponible pour une raison quelconque, gestionnaire d’instantanés StorSimple renvoie un message d’erreur. Cliquez sur **OK** pour fermer le message d’erreur, puis cliquez sur **Annuler** pour fermer la boîte de dialogue **configurer un appareil** .

5. Lorsqu’il se connecte à un périphérique, gestionnaire d’instantanés StorSimple importe chaque groupe de volume configuré pour cet appareil, autant que le groupe de volumes est associé à des sauvegardes. Groupes de volumes qui n’ont pas de sauvegardes associés ne sont pas importées. En outre, les règles de sauvegarde qui ont été créés pour un groupe de volumes ne sont pas importées. Pour afficher les groupes importés, cliquez sur le nœud de **Groupes de volumes** plus haut dans **le volet** , puis cliquez sur **bascule importé des groupes**.

### <a name="step-3-verify-the-connection-to-the-device"></a>Étape 3 : Vérifier la connexion à l’appareil

Utilisez les étapes suivantes pour vérifier que le Gestionnaire d’instantanés StorSimple est connecté à l’appareil StorSimple.

#### <a name="to-verify-the-connection"></a>Pour vérifier la connexion

1. Dans le volet **étendue** , cliquez sur le nœud **périphériques** .

    ![État de l’appareil Gestionnaire d’instantanés StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png) 

2. Consultez le volet de **résultats** : 

   - Si un indicateur vert apparaît sur l’icône du périphérique et **disponible** dans la colonne **statut** , le périphérique est connecté. 

   - Si un indicateur rouge apparaît sur l’icône du périphérique, et non disponible s’affiche dans la colonne **statut** , l’appareil n’est pas connecté. 

   - Si **l’actualisation** s’affiche dans la colonne **statut** , puis Gestionnaire d’instantanés StorSimple récupère des groupes de volumes et sauvegardes associés pour un appareil connecté.

## <a name="upgrade-or-reinstall-storsimple-snapshot-manager"></a>Mettre à jour ou réinstaller le Gestionnaire de capture instantanée StorSimple

Vous devez désinstaller complètement Gestionnaire d’instantanés StorSimple avant de mettre à niveau ou réinstaller le logiciel. 

Avant de réinstaller le Gestionnaire d’instantanés StorSimple, sauvegarder la base de données Gestionnaire d’instantanés StorSimple existant sur l’ordinateur hôte. Cela enregistre les informations de configuration et des stratégies de sauvegarde pour que vous pouvez facilement restaurer ces données à partir de la sauvegarde.

Si vous êtes la mise à niveau ou réinstaller le Gestionnaire d’instantanés StorSimple, procédez comme suit :

- Étape 1 : Désinstaller Gestionnaire d’instantanés StorSimple 
- Étape 2 : Sauvegarder la base de données Gestionnaire d’instantanés StorSimple 
- Étape 3 : Réinstaller le Gestionnaire d’instantanés StorSimple et restaurer la base de données 

### <a name="step-1-uninstall-storsimple-snapshot-manager"></a>Étape 1 : Désinstaller Gestionnaire d’instantanés StorSimple

Utilisez les étapes suivantes pour désinstaller Gestionnaire d’instantanés StorSimple.

#### <a name="to-uninstall-storsimple-snapshot-manager"></a>Pour désinstaller le Gestionnaire de capture instantanée StorSimple

1. Sur l’ordinateur hôte, ouvrez le **Panneau de configuration**, cliquez sur **programmes**, puis cliquez sur **programmes et fonctionnalités**.

2. Dans le volet gauche, cliquez sur **Désinstaller ou modifier un programme**.

3. **Gestionnaire d’instantanés StorSimple**d’avec le bouton droit, puis cliquez sur **désinstaller**.

4. Cela démarre le programme d’installation du Gestionnaire d’instantanés StorSimple. Cliquez sur **Modifier le programme d’installation**, puis cliquez sur **désinstaller**.

    >[AZURE.NOTE] S’il existe un processus MMC ne fonctionne en arrière-plan, tels que le Gestionnaire d’instantanés StorSimple ou gestion des disques, la désinstallation échoue et vous recevrez un message pour fermer toutes les instances de MMC avant d’essayer de désinstaller le programme. Sélectionnez **Fermer automatiquement les applications et essayez de redémarrer les après l’installation**, puis cliquez sur **OK**.
 
5. Lorsque le processus de désinstallation est terminé, un message **D’installation réussie** s’affiche. Cliquez sur **Fermer**.

### <a name="step-2-back-up-the-storsimple-snapshot-manager-database"></a>Étape 2 : Sauvegarder la base de données Gestionnaire d’instantanés StorSimple

Procédez comme suit pour créer et enregistrer une copie de la base de données Gestionnaire d’instantanés StorSimple.

#### <a name="to-back-up-the-database"></a>Pour sauvegarder la base de données

1. Arrêter le Service de gestion des StorSimple Microsoft :

   1. Démarrer le Gestionnaire de serveur.

   2. Dans le tableau de bord Gestionnaire de serveur, dans le menu **Outils** , sélectionnez **Services**.

   3. Dans la page **Services** , sélectionnez **Service de gestion des StorSimple Microsoft**.

   4. Dans le volet droit, sous le **Service de gestion des StorSimple Microsoft**, cliquez sur **Arrêter le service**.

        ![Arrêter le service Manager StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)

2. Accédez à C:\ProgramData\Microsoft\StorSimple\BACatalog. 

    >[AZURE.NOTE] ProgramData est un dossier masqué.

3. Recherchez le fichier XML de catalogue, copiez le fichier et stocker la copie dans un emplacement sécurisé ou dans le cloud.

    ![Fichier de sauvegarde catalogue StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)

4. Redémarrez le Service de gestion des StorSimple Microsoft : 

    1. Dans le tableau de bord Gestionnaire de serveur, dans le menu **Outils** , sélectionnez **Services**.

    2. Dans la page **Services** , sélectionnez le **Service de gestion Microsoft StorSimple**e.

    3. Dans le volet droit, sous le **Service de gestion des StorSimple Microsoft**, cliquez sur **redémarrer le service**. 

### <a name="step-3-reinstall-storsimple-snapshot-manager-and-restore-the-database"></a>Étape 3 : Réinstaller le Gestionnaire d’instantanés StorSimple et restaurer la base de données

Pour réinstaller le Gestionnaire d’instantanés StorSimple, suivez les étapes de [installer un nouveau gestionnaire d’instantanés StorSimple](#install-a-new-storsimple-snapshot-manager). Puis, utilisez la procédure suivante pour restaurer la base de données Gestionnaire d’instantanés StorSimple.

#### <a name="to-restore-the-database"></a>Pour restaurer la base de données

1. Arrêter le Service de gestion des StorSimple Microsoft :

    1. Démarrer le Gestionnaire de serveur.

    2. Dans le tableau de bord Gestionnaire de serveur, dans le menu **Outils** , sélectionnez **Services**.

    3. Dans la page **Services** , sélectionnez **Service de gestion des StorSimple Microsoft**.

    4. Dans le volet droit, sous le **Service de gestion des StorSimple Microsoft**, cliquez sur **Arrêter le service**.

2. Accédez à C:\ProgramData\Microsoft\StorSimple\BACatalog. 

     >[AZURE.NOTE] ProgramData est un dossier masqué.

3. Supprimez le fichier XML de catalogue et remplacez-le par la version que vous avez enregistré précédemment.

4. Redémarrez le Service de gestion des StorSimple Microsoft : 

    1. Dans le tableau de bord Gestionnaire de serveur, dans le menu **Outils** , sélectionnez **Services**.

    2. Dans la page **Services** , sélectionnez **Service de gestion des StorSimple Microsoft**.

    3. Dans le volet droit, sous le **Service de gestion des StorSimple Microsoft**, cliquez sur **redémarrer le service**.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations à propos du gestionnaire instantané StorSimple, accédez à [Nouveautés Gestionnaire d’instantanés StorSimple ?](storsimple-what-is-snapshot-manager.md).

- Pour en savoir plus sur l’interface utilisateur StorSimple instantané Manager, accédez à [l’interface utilisateur Gestionnaire d’instantanés StorSimple](storsimple-use-snapshot-manager.md).

- Pour en savoir plus sur l’utilisation du Gestionnaire d’instantanés StorSimple, accédez à [Utiliser le Gestionnaire d’instantané StorSimple pour administrer votre solution StorSimple](storsimple-snapshot-manager-admin.md).
