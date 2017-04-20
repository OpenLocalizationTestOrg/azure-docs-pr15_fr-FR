<properties 
   pageTitle="Gestion des appareils avec le Gestionnaire de capture instantanée StorSimple | Microsoft Azure"
   description="Décrit comment utiliser le composant logiciel enfichable MMC Gestionnaire d’instantanés StorSimple pour vous connecter et gestion des appareils StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/18/2016"
   ms.author="v-sharos" />

# <a name="use-storsimple-snapshot-manager-to-connect-and-manage-storsimple-devices"></a>Utiliser le Gestionnaire d’instantané StorSimple pour vous connecter et gestion des appareils StorSimple

## <a name="overview"></a>Vue d’ensemble

Vous pouvez utiliser des nœuds dans le volet Gestionnaire d’instantanés StorSimple **étendue** pour vérifier les données importées de périphérique StorSimple et actualiser les périphériques de stockage connectés. En outre, lorsque vous cliquez sur le nœud **périphériques** , vous pouvez voir une liste des appareils connectés et les informations d’état correspondantes dans le volet des **résultats** .

![Appareils connectés](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**Figure 1 : Gestionnaire d’instantanés StorSimple connecté appareil** 

En fonction de vos sélections dans la **vue** , le volet **résultats** affiche les informations suivantes sur chaque appareil. (Pour plus d’informations sur la configuration d’un affichage, accédez au [menu Affichage](storsimple-use-snapshot-manager.md#view-menu).


| Colonne de résultats  |Description          |
|:----------------|:--------------------| 
| Nom            | Le nom de l’appareil, tel que configuré dans le portail classique Azure|
| Modèle           | Numéro de modèle de l’appareil|
| Version         | La version du logiciel installé sur l’appareil |
| État          | Si l’appareil est disponible |
| Dernière synchronisation     | Date et heure lors de la dernière synchronisation de l’appareil |
| Numéro de série.      | Numéro de série de l’appareil |
 
Si vous clic droit sur le nœud de **périphériques** dans le volet **étendue** , vous pouvez sélectionner parmi les actions suivantes :

- Ajouter ou remplacer un appareil 
- Connectez un appareil et vérifiez importations 
- Actualiser les appareils connectés 

Si vous cliquez sur le nœud **périphériques** , puis le bouton droit sur un appareil dans le volet des **résultats** , vous pouvez sélectionner les actions suivantes :

- Permettent d’authentifier un appareil 
- Afficher les détails d’appareil 
- Actualiser un appareil 
- Supprimer la configuration d’un appareil 
- Modifier un mot de passe

>[AZURE.NOTE] Toutes ces actions sont également disponibles dans le volet **Actions** .
 
Ce didacticiel explique comment utiliser le Gestionnaire d’instantané StorSimple pour vous connecter et gérer les périphériques et effectuer les tâches suivantes :

- Ajouter ou remplacer un appareil 
- Connectez un appareil et vérifiez importations 
- Actualiser les appareils connectés 
- Permettent d’authentifier un appareil 
- Afficher les détails d’appareil 
- Actualiser un périphérique individuel 
- Supprimer la configuration d’un appareil 
- Modifier un mot de passe qui a expiré
- Remplacer un périphérique a échoué

>[AZURE.NOTE] Pour obtenir des informations générales sur l’utilisation de l’interface StorSimple instantané Manager, accédez à [l’interface utilisateur Gestionnaire d’instantanés StorSimple](storsimple-use-snapshot-manager.md).


## <a name="add-or-replace-a-device"></a>Ajouter ou remplacer un appareil

Utilisez la procédure suivante pour ajouter ou remplacer un périphérique StorSimple.

#### <a name="to-add-or-replace-a-device"></a>Pour ajouter ou remplacer un appareil

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet **étendue** , cliquez sur le nœud de **périphériques** , puis cliquez sur **configurer un appareil**. La boîte de dialogue **configurer un appareil** s’affiche.

    ![Configurer un périphérique StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png) 

3. Dans la zone de liste déroulante **périphérique** , sélectionnez l’adresse IP du périphérique ou du périphérique virtuel. 

4. Dans la zone de texte **mot de passe** , tapez le mot de passe Gestionnaire d’instantanés StorSimple que vous avez créé pour le périphérique dans le portail classique Azure. Cliquez sur **OK**. Gestionnaire d’instantanés StorSimple recherche le périphérique que vous avez identifiés. 

    - Si l’appareil n’est disponible, gestionnaire d’instantanés StorSimple ajoute une connexion. 

    - Si l’appareil n’est pas disponible pour une raison quelconque, gestionnaire d’instantanés StorSimple renvoie un message d’erreur. Cliquez sur **OK** pour fermer le message d’erreur, puis cliquez sur **Annuler** pour fermer la boîte de dialogue **configurer un appareil** .

## <a name="connect-a-device-and-verify-imports"></a>Connectez un appareil et vérifiez importations

Utilisez la procédure suivante pour connecter un périphérique StorSimple et vérifiez que les groupes de volumes existants associés à des sauvegardes sont importées.

#### <a name="to-connect-a-device-and-verify-imports"></a>Connecter un périphérique et vérifier importations

1. Pour connecter un appareil à StorSimple instantané Manager, suivez les instructions dans Ajouter ou remplacer un périphérique. Lorsqu’il se connecte à un périphérique, gestionnaire d’instantanés StorSimple répond comme suit :

    - Si l’appareil n’est pas disponible pour une raison quelconque, gestionnaire d’instantanés StorSimple renvoie un message d’erreur. 

   - Si l’appareil n’est disponible, gestionnaire d’instantanés StorSimple ajoute une connexion. Lorsque vous sélectionnez le périphérique, il apparaît dans le volet de **résultats** et le champ État indique que le périphérique est **disponible**. Gestionnaire d’instantanés StorSimple importe tous les groupes volume configurés pour l’appareil, autant que les groupes de volumes ont associé des sauvegardes. Stratégies de sauvegarde ne sont pas importées. Groupes de volumes qui n’ont pas de sauvegardes associés ne sont pas importées.

2. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

3. Le nœud supérieur dans le volet de **l’étendue** d’avec le bouton droit, puis cliquez sur **Importations bascule l’affichage**.

    ![Sélectionnez bascule l’affichage importations](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png) 

4. La boîte de dialogue **Bascule l’affichage importations** apparaît, indiquant l’état des groupes de volume importé et des sauvegardes. Cliquez sur **OK**. 

Une fois les groupes de volumes et des sauvegardes importés avec succès, vous pouvez utiliser Gestionnaire d’instantanés StorSimple pour les gérer comme vous le feriez gérez groupes de volumes et des sauvegardes que vous avez créé et configuré avec le Gestionnaire de capture instantanée StorSimple. 

## <a name="refresh-connected-devices"></a>Actualiser les appareils connectés

Utilisez la procédure suivante pour synchroniser les périphériques StorSimple connectés avec le Gestionnaire de capture instantanée StorSimple.

####<a name="to-refresh-connected-devices"></a>Pour actualiser des appareils connectés

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet **étendue** , avec le bouton droit **périphériques**, puis cliquez sur **Actualiser les appareils**. Synchronise les appareils connectés avec le Gestionnaire de capture instantanée StorSimple afin que vous puissiez afficher les groupes de volume et les sauvegardes, y compris les ajouts récents. 

    ![Actualiser les appareils StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)
 
L’action **Actualiser appareils** récupère les nouveaux groupes de volumes et des sauvegardes associés à partir d’appareils connectés. Contrairement à **l’analyser volumes** action disponible pour le nœud **Volumes** , **Actualiser appareils** ne restaure pas le Registre de sauvegarde.

## <a name="authenticate-a-device"></a>Permettent d’authentifier un appareil

Utilisez la procédure suivante pour authentifier un périphérique StorSimple avec le Gestionnaire de capture instantanée StorSimple.

#### <a name="to-authenticate-a-device"></a>Pour authentifier un appareil

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet **étendue** , cliquez sur **périphériques**.

3. Dans le volet **des résultats** , double-cliquez sur le nom du périphérique, puis cliquez sur **authentification**.

4. La boîte de dialogue **authentifier** s’affiche. Tapez le mot de passe, puis cliquez sur **OK**.

    ![Boîte de dialogue permettent d’authentifier](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png) 
 
## <a name="view-device-details"></a>Afficher les détails d’appareil

Utilisez la procédure suivante pour afficher les détails d’un appareil StorSimple et, si nécessaire, resynchroniser le périphérique avec le Gestionnaire de capture instantanée StorSimple.

#### <a name="to-view-and-resynchronize-device-details"></a>Pour afficher et resynchroniser les détails de l’appareil

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet **étendue** , cliquez sur **périphériques**.

3. Dans le volet **des résultats** , double-cliquez sur le nom du périphérique, puis cliquez sur **Détails**. 

4.la la boîte de dialogue **Détails de l’appareil** s’affiche. Ce champ affiche le nom, modèle, version, numéro de série, état, cible iSCSI nom complet (IQN) et dernière synchronisation date et l’heure. 

   - Cliquez sur **resynchroniser** pour synchroniser l’appareil.

   - Cliquez sur **OK** ou sur **Annuler** pour fermer la boîte de dialogue.

    ![Détails de l’appareil](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png) 
 
## <a name="refresh-an-individual-device"></a>Actualiser un périphérique individuel

Utilisez la procédure suivante pour resynchroniser un périphérique StorSimple avec le Gestionnaire de capture instantanée StorSimple.

#### <a name="to-refresh-a-device"></a>Actualiser un appareil

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple. 

2. Dans le volet **étendue** , cliquez sur **périphériques**. 

3. Dans le volet **des résultats** , double-cliquez sur le nom du périphérique, puis cliquez sur **Actualiser appareil**. Cette opération synchronise le périphérique avec le Gestionnaire de capture instantanée StorSimple. 

## <a name="delete-a-device-configuration"></a>Supprimer la configuration d’un appareil

Utilisez la procédure suivante pour supprimer une configuration appareil StorSimple individuelle à partir du gestionnaire instantané StorSimple.

#### <a name="to-delete-a-device-configuration"></a>Pour supprimer une configuration de l’appareil

1. Cliquez sur l’icône du bureau pour démarrer le Gestionnaire de capture instantanée StorSimple. 

2. Dans le volet **étendue** , cliquez sur **périphériques**. 

3. Dans le volet **des résultats** , double-cliquez sur le nom du périphérique, puis cliquez sur **Supprimer**. 

4. Le message suivant s’affiche. Cliquez sur **Oui** pour supprimer la configuration ou cliquez sur **non** pour annuler la suppression.

    ![Supprimer la configuration d’appareil](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## <a name="change-an-expired-device-password"></a>Modifier un mot de passe qui a expiré

Vous devez entrer un mot de passe pour authentifier un périphérique StorSimple avec le Gestionnaire de capture instantanée StorSimple. Vous configurez ce mot de passe lorsque vous utilisez l’interface Windows PowerShell pour configurer l’appareil. Toutefois, le mot de passe peut expirer. Dans ce cas, vous pouvez utiliser le portail classique Azure pour modifier le mot de passe. Puis, car le périphérique a été configuré dans le Gestionnaire de capture instantanée StorSimple avant l’expiration du mot de passe, vous devez ré-authentifier le périphérique dans le Gestionnaire de capture instantanée StorSimple. 

#### <a name="to-change-the-expired-password"></a>Pour modifier le mot de passe qui a expiré

1. Dans le portail classique Azure, démarrer le service Gestionnaire StorSimple.

2. Cliquez sur **périphériques** > **configurer** pour l’appareil.

3. Faites défiler jusqu'à la section Gestionnaire d’instantanés StorSimple. Entrez un mot de passe 14 et 15 caractères. Assurez-vous que le mot de passe contienne un mélange de caractères en majuscules, minuscules, numériques et spéciaux.

4. Entrez à nouveau le mot de passe pour le confirmer.

5. Cliquez sur **Enregistrer** dans la partie inférieure de la page.

#### <a name="to-re-authenticate-the-device"></a>Authentification à nouveau le périphérique

1. Démarrer le Gestionnaire de capture instantanée StorSimple.

2. Dans le volet **étendue** , cliquez sur **périphériques**. Une liste des périphériques configurés s’affiche dans le volet des **résultats** . 

3. Sélectionnez le périphérique, avec le bouton droit, puis cliquez sur **authentification**.

4. Dans la fenêtre **d’authentification** apparaît, entrez le nouveau mot de passe. 

5. Sélectionnez le périphérique, avec le bouton droit et sélectionnez **Actualiser appareil**. Cette opération synchronise le périphérique avec le Gestionnaire de capture instantanée StorSimple. 

## <a name="replace-a-failed-device"></a>Remplacer un périphérique a échoué

Si un appareil StorSimple échoue et est remplacé par un appareil de mise en veille (basculement), procédez comme suit pour vous connecter vers le nouvel appareil et afficher les sauvegardes associées.

#### <a name="to-connect-to-a-new-device-after-failover"></a>Pour vous connecter à un nouvel appareil après le basculement

1. Reconfigurer la connexion iSCSI vers le nouvel appareil. Pour plus d’informations, accédez à « étape 7 : montage, initialisation et mettre en forme un volume » dans [déployer votre appareil StorSimple local](storsimple-deployment-walkthrough-u2.md). 

>[AZURE.NOTE] Si le nouvel appareil StorSimple possède la même adresse IP que l’ancien, vous pourrez peut-être vous connecter à l’ancienne configuration. 

2. Arrêter le Service de gestion des StorSimple Microsoft :

    1. Démarrer le Gestionnaire de serveur.

    2. Dans le tableau de bord Gestionnaire de serveur, dans le menu **Outils** , sélectionnez **Services**. 

    3. Dans la fenêtre **Services** , sélectionnez le **Service de gestion des StorSimple Microsoft**. 

    4. Dans le volet droit, sous le **Service de gestion des StorSimple Microsoft**, cliquez sur **Arrêter le service**. 

3. Supprimer les informations de configuration relatives à l’ancien périphérique : 

    1. Dans l’Explorateur de fichiers, accédez à C:\ProgramData\Microsoft\StorSimple\BACatalog. 

    2. Supprimez les fichiers dans le dossier BACatalog. 

4. Redémarrez le Service de gestion des StorSimple Microsoft : 

    1. Dans le tableau de bord Gestionnaire de serveur, dans le menu **Outils** , sélectionnez **Services**. 

    2. Dans la fenêtre **Services** , sélectionnez le **Service de gestion des StorSimple Microsoft**. 

    3. Dans le volet droit, sous le **Service de gestion des StorSimple Microsoft**, cliquez sur **redémarrer le service**. 

5. Démarrer le Gestionnaire de capture instantanée StorSimple. 

6. Pour configurer le nouvel appareil StorSimple, effectuez les étapes à l’étape 2 : connecter un périphérique StorSimple dans le [Gestionnaire de capture instantanée StorSimple déployer](storsimple-snapshot-manager-deployment.md). 

7. Cliquez sur le nœud de niveau supérieur dans le volet **étendue** (Gestionnaire d’instantanés StorSimple dans l’exemple), puis cliquez sur **Importations bascule l’affichage**. 

8. Un message s’affiche lorsque les groupes de volume importé et les sauvegardes sont visibles dans le Gestionnaire de capture instantanée StorSimple. Cliquez sur **OK**. 

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [utiliser le Gestionnaire d’instantanés StorSimple pour administrer votre solution StorSimple](storsimple-snapshot-manager-admin.md).
- Découvrez comment [utiliser le Gestionnaire d’instantanés StorSimple pour afficher et gérer des volumes](storsimple-snapshot-manager-manage-volumes.md).

