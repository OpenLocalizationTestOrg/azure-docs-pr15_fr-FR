<properties 
   pageTitle="Configurer o sur votre hôte de tableau virtuel StorSimple | Microsoft Azure"
   description="Décrit comment configurer des chemins multiples (/ o) pour votre StorSimple tableau virtuel connecté à un hôte exécutant Windows Server 2012 R2."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/04/2016"
   ms.author="alkohli" />

# <a name="configure-multipath-io-on-windows-server-host-for-the-storsimple-virtual-array"></a>Configurer les chemins multiples e/s sur hôte Windows Server pour la matrice virtuel StorSimple

## <a name="overview"></a>Vue d’ensemble

Cet article décrit comment installer fonctionnalité e/s de chemins multiples (o) sur votre hôte Windows Server, appliquer les paramètres de configuration spécifiques pour les volumes StorSimple seule et vérifier puis o pour les volumes StorSimple. La procédure suppose que votre tableau virtuel StorSimple 1200 avec deux interfaces réseau est connecté à un hôte Windows Server avec deux interfaces réseau. Les informations contenues dans cet article s’applique uniquement à la matrice virtuelle. Pour plus d’informations sur les périphériques de série 8000 StorSimple, accédez à [Configurer o pour StorSimple hôte](storsimple-configure-mpio-windows-server.md). 

La fonctionnalité o dans les configurations de stockage hautement disponible, tolérance Windows Server vous aide à créer. O utilise des composants de chemin d’accès physique redondants — cartes, câbles et commutateurs — pour créer des chemins d’accès logiques entre le serveur et le périphérique de stockage. S’il existe une défaillance de composant, à l’origine d’un chemin d’accès logique échec, une logique de chemins multiples utilise un autre chemin d’entrée/sortie afin que les applications peuvent toujours accéder à leurs données. En outre en fonction de votre configuration, o peut également améliorer les performances en ré-équilibrant la charge sur ces chemins d’accès. Pour plus d’informations, voir [vue d’ensemble des o](https://technet.microsoft.com/library/cc725907.aspx "o présentation et fonctionnalités").  

Pour la disponibilité de votre solution StorSimple, configurez o sur hôtes Windows Server connectés à votre tableau virtuel StorSimple 1200 (également connu sous le local périphérique virtuel). Les serveurs hôtes tolèrent puis un lien, de réseau ou Échec de l’interface. 

Vous devez suivre ces étapes pour configurer o : 

- Conditions préalables de configuration

- Étape 1 : Installer o sur l’hôte de Windows Server

- Étape 2 : Configurer o pour les volumes StorSimple

- Étape 3 : Volumes de montage StorSimple sur l’hôte

Chacune des étapes ci-dessus est décrit dans les sections suivantes.


## <a name="prerequisites"></a>Conditions préalables

Cette section décrit les conditions préalables configuration pour l’hôte de Windows Server et votre tableau virtuel.

### <a name="on-windows-server-host"></a>Sur l’hôte de Windows Server

-  Assurez-vous que votre hôte Windows Server a 2 interfaces réseau activé.


### <a name="on-storsimple-virtual-array"></a>Dans le tableau virtuel StorSimple

- Le tableau virtuel doit être configuré comme un serveur iSCSI. Pour plus d’informations, voir [configurer la matrice virtuel qu’un serveur iSCSI](storsimple-ova-deploy3-iscsi-setup.md). Une ou plusieurs interfaces réseau doivent être activés sur le tableau.   

- Les interfaces réseau de votre tableau virtuel doivent être accessibles à partir de l’hôte du serveur de Windows.

- Un ou plusieurs volumes doivent être créés dans votre tableau virtuel StorSimple. Pour plus d’informations, voir [Ajouter un volume](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume) de votre tableau virtuel StorSimple 1200. Dans cette procédure, nous avons créé 3 volumes (2 localement épinglés et 1 hiérarchisé volume comme illustré ci-dessous) dans le tableau virtuel.
    
    ![mpio0](./media/storsimple-ova-configure-mpio-windows-server/mpio0.png)

### <a name="hardware-configuration-for-storsimple-virtual-array"></a>Configuration matérielle pour tableau virtuel StorSimple

L’illustration suivante montre la configuration matérielle de disponibilité et l’équilibrage de charge de chemins multiples pour votre hôte Windows Server et tableau virtuel StorSimple utilisés dans cette procédure.  

![configuration matérielle o](./media/storsimple-ova-configure-mpio-windows-server/1200hardwareconfig.png)

Comme le montre l’illustration précédente :

- Votre tableau virtuel StorSimple sa mise en service sur Hyper-V est un appareil active nœud unique configuré comme un serveur iSCSI.

- Deux interfaces réseau virtuel sont activées dans votre tableau. Dans l’interface utilisateur de votre tableau virtuel 1200 de web local, vérifiez que les deux interfaces réseau sont activés en accédant à **Paramètres du réseau** , comme indiqué ci-dessous :

    ![Activé 1200 des interfaces réseau](./media/storsimple-ova-configure-mpio-windows-server/mpio9.png)
    
    Remarque : les adresses IPv4 des interfaces réseau activée (Ethernet, Ethernet 2 par défaut) et enregistrer pour une utilisation ultérieure sur l’hôte.

- Deux interfaces réseau sont activés sur votre hôte Windows Server. Si les interfaces connectés pour hôte et matrice sont sur le même sous-réseau, puis il pourra être 4 chemins d’accès. C’est le cas dans cette procédure. Toutefois, si chaque interface réseau dans l’interface de tableau et de l’hôte est sur un autre sous-réseau IP (et non prenant), puis uniquement 2 chemins sera disponibles.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Étape 1 : Installer o sur l’hôte de Windows Server

O est une fonctionnalité facultative sur Windows Server et n’est pas installé par défaut. Il doit être installée en tant que fonctionnalité via le Gestionnaire de serveur. Pour installer cette fonctionnalité sur votre hôte Windows Server, procédez comme suit.

[AZURE.INCLUDE [storsimple-install-mpio-windows-server-host](../../includes/storsimple-install-mpio-windows-server-host.md)]


## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Étape 2 : Configurer o pour les volumes StorSimple

O doit être configuré pour identifier les volumes StorSimple. Pour configurer o pour reconnaître StorSimple volumes, procédez comme suit.

[AZURE.INCLUDE [storsimple-configure-mpio-volumes](../../includes/storsimple-configure-mpio-volumes.md)]

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Étape 3 : Volumes de montage StorSimple sur l’hôte

Une fois o est configuré sur Windows Server, ou les volumes créés sur une batterie StorSimple peuvent être installés et puis tirer parti des o pour la redondance. Pour monter un volume, procédez comme suit.

#### <a name="to-mount-volumes-on-the-host"></a>Monter volumes sur l’hôte

1. Ouvrez la fenêtre **iSCSI Initiator Properties** sur l’hôte de Windows Server. Cliquez sur **le Gestionnaire de serveur > tableau de bord > Outils > initiateur iSCSI**.
2. Dans la boîte de dialogue **iSCSI Initiator Properties** , cliquez sur l’onglet recherche, puis cliquez sur **Découvrir le portail cible**.
3. Dans la boîte de dialogue **Découvrir le portail cible** , procédez comme suit :
    
    - Entrez l’adresse IP de la première interface réseau activées dans votre tableau virtuel StorSimple. Par défaut, ce serait **Ethernet**. 
    - Cliquez sur **OK** pour retourner dans la boîte de dialogue **iSCSI Initiator Properties** .

    >[AZURE.IMPORTANT] **Si vous utilisez un réseau privé pour les connexions, entrez l’adresse IP du port de données est connecté au réseau privé.**

4. Répétez les étapes 2 et 3 pour une deuxième interface réseau (par exemple, Ethernet 2) dans votre tableau. 

5. Dans la boîte de dialogue **iSCSI Initiator Properties** , sélectionnez l’onglet **cibles** . Pour votre tableau virtuel, vous devriez voir chaque surface volume comme cible sous **Cibles découvertes**. Dans ce cas, trois cibles (correspondant à trois volumes) seraient découvertes.

    ![mpio1](./media/storsimple-ova-configure-mpio-windows-server/mpio1.png)

6. Cliquez sur **se connecter** pour établir une session iSCSI avec votre tableau StorSimple. Une boîte de dialogue **se connecter à la cible** s’affichent. Activez la case à cocher **Activer les chemins d’accès multiples** . Cliquez sur **Avancé**.

    ![mpio2](./media/storsimple-ova-configure-mpio-windows-server/mpio2.png)

8. Dans la boîte de dialogue **Paramètres avancés** , procédez comme suit :                                       
    -    Dans la liste déroulante **Local Adapter** , sélectionnez **Microsoft iSCSI Initiator**.
    -    Dans la liste déroulante **Initiateur IP** , sélectionnez l’adresse IP de l’hôte.
    -    Dans la liste déroulante IP **Portail cible** , sélectionnez l’adresse IP de l’interface de tableau.
    -    Cliquez sur **OK** pour retourner dans la boîte de dialogue **iSCSI Initiator Properties** .

    ![mpio3](./media/storsimple-ova-configure-mpio-windows-server/mpio3.png)

9. Cliquez sur **Propriétés**. 

    ![mpio4](./media/storsimple-ova-configure-mpio-windows-server/mpio4.png)
10. Dans la boîte de dialogue **Propriétés** , cliquez sur **Ajout d’une Session**.

    ![mpio5](./media/storsimple-ova-configure-mpio-windows-server/mpio5.png)

10. Dans la boîte de dialogue **se connecter à la cible** , activez la case à cocher **Activer les chemins d’accès multiples** . Cliquez sur **Avancé**.
11. Dans la boîte de dialogue **Paramètres avancés** :                                        
    -  Dans la liste déroulante **carte locale** , sélectionnez Microsoft iSCSI Initiator.
    -  Dans la liste déroulante **Initiateur IP** , sélectionnez l’adresse IP correspondant à l’hôte. Dans ce cas, vous vous connectez deux interfaces réseau sur le tableau à une seule interface réseau sur l’hôte. Par conséquent, cette interface est identique à celui fourni pour la première session.
    -  Dans la liste déroulante **Adresse IP de portail cible** , sélectionnez l’adresse IP de l’interface de données deuxième activé sur le groupe.
    -  Cliquez sur **OK** pour retourner dans la boîte de dialogue Propriétés de l’initiateur iSCSI. Vous avez ajouté une deuxième session à la cible.

        ![mpio11](./media/storsimple-ova-configure-mpio-windows-server/mpio11.png)

    - Après avoir ajouté les sessions souhaitées (chemins d’accès), dans la boîte de dialogue **iSCSI Initiator Properties** , sélectionnez la cible, puis cliquez sur **Propriétés**. Sous l’onglet Sessions de la boîte de dialogue **Propriétés** , notez la session quatre identificateurs qui correspondent aux permutations possibles chemin d’accès. Pour annuler une session, activez la case à cocher en regard d’un identificateur de session, puis cliquez sur **se déconnecter**.
 
    - Pour afficher les appareils présentés dans les sessions, sélectionnez l’onglet **périphériques** . Pour configurer la stratégie o pour un périphérique sélectionné, cliquez sur **o**. La **
    -  Détails** boîte de dialogue s’affiche. Sur la **o** onglet, vous pouvez sélectionner appropriés **stratégie d’équilibrage de charge** paramètres. Vous pouvez également afficher la **Active** ou **mise en veille ** chemin d’accès au type.

10. Répétez les étapes 8-11 pour ajouter d’autres sessions (chemins d’accès) à la cible. Deux interfaces sur l’hôte et deux sur le tableau virtuel, vous pouvez ajouter un total de quatre sessions pour chaque cible. 

    ![mpio14](./media/storsimple-ova-configure-mpio-windows-server/mpio14.png)

11. Vous devrez Répétez ces étapes pour chaque volume (surfaces comme cible).

    ![mpio15](./media/storsimple-ova-configure-mpio-windows-server/mpio15.png)

12. Ouvrez **Gestion de l’ordinateur** en accédant à **Gestionnaire de serveur > tableau de bord > Gestion de l’ordinateur**. Dans le volet gauche, cliquez sur **stockage > Gestion des disques**. L’ou les volumes créé sur une batterie virtuel StorSimple qui sont visibles à cet hôte s’affichent sous **Gestion des disques** en tant que nouveau disques.

13. Initialisation le disque et créez un nouveau volume. Pendant le processus de mise en forme, sélectionnez une taille d’allocation unitaire (Australie) de 64 Ko. Répétez le processus pour tous les volumes disponibles.

    ![Gestion des disques](./media/storsimple-ova-configure-mpio-windows-server/mpio20.png)

14. Sous **Gestion des disques**, cliquez sur le **disque** et sélectionnez **Propriétés**.

15. Dans la boîte de dialogue **Propriétés de l’appareil disque chemins multiples** , cliquez sur l’onglet **o** .

    ![Propriétés d’un disque](./media/storsimple-ova-configure-mpio-windows-server/mpio21.png)

16. Dans la section **Nom DSM** , cliquez sur **Détails** et vérifiez que les paramètres sont définis sur les paramètres par défaut. Les paramètres par défaut sont :

    - Chemin d’accès vérifier période = 30
    - Nombre de tentatives = 3
    - AOP supprimer période = 20
    - Intervalle entre les tentatives = 1
    - Vérifier le chemin d’accès activé = désactivée.

    >[AZURE.NOTE] **Ne modifiez pas les paramètres par défaut.**


## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [l’utilisation du service Manager StorSimple pour administrer votre tableau virtuel StorSimple](storsimple-ova-manager-service-administration.md).
 
