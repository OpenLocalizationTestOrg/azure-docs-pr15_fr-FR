<properties 
   pageTitle="Configurer o pour votre appareil StorSimple | Microsoft Azure"
   description="Décrit comment configurer des chemins multiples (/ o) pour votre appareil StorSimple connecté à un hôte exécutant Windows Server 2012 R2."
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
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="configure-multipath-io-for-your-storsimple-device"></a>Configurer les chemins multiples e/s pour votre appareil StorSimple

Microsoft a créé le support pour la fonctionnalité de chemins multiples (/ o) dans Windows Server build aide hautement disponible, tolérance configurations SAN. O utilise des composants de chemin d’accès physique redondants — cartes, câbles et commutateurs — pour créer des chemins d’accès logiques entre le serveur et le périphérique de stockage. S’il existe une défaillance de composant, à l’origine d’un chemin d’accès logique échec, une logique de chemins multiples utilise un autre chemin d’entrée/sortie afin que les applications peuvent toujours accéder à leurs données. En outre en fonction de votre configuration, o peut également améliorer les performances en ré-équilibrant la charge sur ces chemins d’accès. Pour plus d’informations, voir [vue d’ensemble des o](https://technet.microsoft.com/library/cc725907.aspx "o présentation et fonctionnalités").  

Pour la disponibilité de votre solution StorSimple, o doit être configuré sur votre appareil StorSimple. O est installé sur votre héberger des serveurs exécutant Windows Server 2012 R2, les serveurs tolèrent puis un lien, de réseau ou Échec de l’interface. 

O est une fonctionnalité facultative sur Windows Server et n’est pas installé par défaut. Il doit être installée en tant que fonctionnalité via le Gestionnaire de serveur. Cette rubrique décrit les étapes que vous devez suivre pour installer et utiliser la fonctionnalité o sur un hôte exécutant Windows Server 2012 R2 et connecté à un appareil physique StorSimple.

>[AZURE.NOTE] **Cette procédure s’applique pour les séries 8000 StorSimple uniquement. O n’est actuellement pas pris en charge sur un appareil virtuel StorSimple.**

Vous devez suivre ces étapes pour configurer o sur votre appareil StorSimple :

- Étape 1 : Installer o sur l’hôte de Windows Server

- Étape 2 : Configurer o pour les volumes StorSimple

- Étape 3 : Volumes de montage StorSimple sur l’hôte

- Étape 4 : Configurer l’o pour la disponibilité et l’équilibrage de charge

Chacune des étapes ci-dessus est décrit dans les sections suivantes.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Étape 1 : Installer o sur l’hôte de Windows Server

Pour installer cette fonctionnalité sur votre hôte Windows Server, procédez comme suit.

#### <a name="to-install-mpio-on-the-host"></a>Installer o sur l’hôte

1. Ouvrez le Gestionnaire de serveur de votre hôte Windows Server. Par défaut, le Gestionnaire de serveur démarre lorsqu’un membre du groupe Administrateurs ouvre une session sur un ordinateur exécutant Windows Server 2012 R2 ou Windows Server 2012. Si le Gestionnaire de serveur n’est pas déjà ouvert, cliquez sur **Démarrer > Gestionnaire de serveur**.
![Gestionnaire de serveur](./media/storsimple-configure-mpio-windows-server/IC740997.png)
2. Cliquez sur **le Gestionnaire de serveur > tableau de bord > ajouter des rôles et fonctionnalités**. Cela démarre l’Assistant **Ajout de rôles et fonctionnalités** .
![Ajouter des rôles et fonctionnalités Assistant 1](./media/storsimple-configure-mpio-windows-server/IC740998.png)
3. Dans l’Assistant **Ajout de rôles et fonctionnalités** , procédez comme suit :

    - Dans la page **avant de commencer** , cliquez sur **suivant**.
    - Dans la page **Sélectionnez le type d’installation** , acceptez le paramètre par défaut de l’installation **basée sur une fonctionnalité ou rôle** . Cliquez sur **suivant**. ![Ajouter des rôles et fonctionnalités Assistant 2](./media/storsimple-configure-mpio-windows-server/IC740999.png)
    - Dans la page **Sélectionnez le serveur de destination** , cliquez sur **Sélectionner un serveur à partir du pool de serveur**. Votre serveur hôte doit être découverte automatiquement. Cliquez sur **suivant**.
    - Dans la page **Sélectionner des rôles de serveur** , cliquez sur **suivant**.
    - Dans la page **Sélectionnez des fonctionnalités** , sélectionnez **E/s de chemins multiples**, puis cliquez sur **suivant**. ![Ajouter des rôles et fonctionnalités Assistant 5](./media/storsimple-configure-mpio-windows-server/IC741000.png)
    - Dans la page **Confirmer sélections pour l’installation** , confirmer la sélection, puis **Redémarrez le serveur de destination automatiquement si nécessaire**, comme illustré ci-dessous. Cliquez sur **installer**. ![Ajouter des rôles et fonctionnalités Assistant 8](./media/storsimple-configure-mpio-windows-server/IC741001.png)
    - Une fois l’installation terminée, vous serez informé. Cliquez sur **Fermer** pour fermer l’Assistant. ![Ajouter des rôles et fonctionnalités Assistant 9](./media/storsimple-configure-mpio-windows-server/IC741002.png)

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Étape 2 : Configurer o pour les volumes StorSimple

O doit être configuré pour identifier les volumes StorSimple. Pour configurer o pour reconnaître StorSimple volumes, procédez comme suit.

#### <a name="to-configure-mpio-for-storsimple-volumes"></a>Pour configurer o pour StorSimple volumes

1. Ouvrez la **configuration o**. Cliquez sur **le Gestionnaire de serveur > tableau de bord > Outils > o**.

2. Dans la boîte de dialogue **Propriétés d’o** , sélectionnez l’onglet **Découvrir plusieurs chemins** .

3. Sélectionnez **Ajouter une prise en charge des périphériques iSCSI**, puis cliquez sur **Ajouter**.  
![Propriétés o découvrir les chemins d’accès à plusieurs](./media/storsimple-configure-mpio-windows-server/IC741003.png)

4. Redémarrez le serveur lorsque vous y êtes invité.
5. Dans la boîte de dialogue **Propriétés d’o** , cliquez sur l’onglet **Périphériques o** . Cliquez sur **Ajouter**.
    </br>![O propriétés o appareils](./media/storsimple-configure-mpio-windows-server/IC741004.png)
6. Dans la boîte de dialogue **Ajouter une prise en charge o** sous **ID matériel du périphérique**, entrez votre numéro de série d’appareil. Vous pouvez obtenir le numéro de série appareil en accédant à votre service Manager StorSimple et en accédant à **appareils > tableau de bord**. Le numéro de série appareil s’affiche dans le volet **Aperçu rapide** du tableau de bord appareil droit.
    </br>![Ajouter un Support o](./media/storsimple-configure-mpio-windows-server/IC741005.png)
7. Redémarrez le serveur lorsque vous y êtes invité.

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Étape 3 : Volumes de montage StorSimple sur l’hôte

Une fois o est configuré sur Windows Server, ou les volumes créés sur le périphérique StorSimple peuvent être installés et puis tirer parti des o pour la redondance. Pour monter un volume, procédez comme suit.

#### <a name="to-mount-volumes-on-the-host"></a>Monter volumes sur l’hôte

1. Ouvrez la fenêtre **iSCSI Initiator Properties** sur l’hôte de Windows Server. Cliquez sur **le Gestionnaire de serveur > tableau de bord > Outils > initiateur iSCSI**.
2. Dans la boîte de dialogue **iSCSI Initiator Properties** , cliquez sur l’onglet recherche, puis cliquez sur **Découvrir le portail cible**.
3. Dans la boîte de dialogue **Découvrir le portail cible** , procédez comme suit :
    
    - Entrez l’adresse IP du point de données de votre appareil StorSimple (par exemple, entrer des données 0).
    - Cliquez sur **OK** pour retourner dans la boîte de dialogue **iSCSI Initiator Properties** .

    >[AZURE.IMPORTANT] **Si vous utilisez un réseau privé pour les connexions, entrez l’adresse IP du port de données est connecté au réseau privé.**

4. Répétez les étapes 2 et 3 pour une deuxième interface réseau (par exemple, données 1) sur votre appareil. N’oubliez pas que ces interfaces doivent être activés pour iSCSI. Pour plus d’informations, voir [Modifier les interfaces réseau](storsimple-modify-device-config.md#modify-network-interfaces).
5. Dans la boîte de dialogue **iSCSI Initiator Properties** , sélectionnez l’onglet **cibles** . Vous devriez voir la cible du périphérique StorSimple nom qualifié sous **Cibles découvertes**.
 ![iSCSI initiateur propriétés cibles onglet](./media/storsimple-configure-mpio-windows-server/IC741007.png)
6. Cliquez sur **se connecter** pour établir une session iSCSI avec votre appareil StorSimple. Une boîte de dialogue **se connecter à la cible** s’affichent.

7. Dans la boîte de dialogue **se connecter à la cible** , activez la case à cocher **Activer les chemins d’accès multiples** . Cliquez sur **Avancé**.

8. Dans la boîte de dialogue **Paramètres avancés** , procédez comme suit :                                       
    -    Dans la liste déroulante **Local Adapter** , sélectionnez **Microsoft iSCSI Initiator**.
    -    Dans la liste déroulante **Initiateur IP** , sélectionnez l’adresse IP de l’hôte.
    -    Dans la liste déroulante IP **Portail cible** , sélectionnez l’adresse IP de l’interface de périphérique.
    -    Cliquez sur **OK** pour retourner dans la boîte de dialogue **iSCSI Initiator Properties** .

9. Cliquez sur **Propriétés**. Dans la boîte de dialogue **Propriétés** , cliquez sur **Ajout d’une Session**.
10. Dans la boîte de dialogue **se connecter à la cible** , activez la case à cocher **Activer les chemins d’accès multiples** . Cliquez sur **Avancé**.
11. Dans la boîte de dialogue **Paramètres avancés** :                                        
    -  Dans la liste déroulante **carte locale** , sélectionnez Microsoft iSCSI Initiator.
    -  Dans la liste déroulante **Initiateur IP** , sélectionnez l’adresse IP correspondant à l’hôte. Dans ce cas, vous vous connectez deux interfaces réseau sur le périphérique à une seule interface réseau sur l’hôte. Par conséquent, cette interface est identique à celui fourni pour la première session.
    -  Dans la liste déroulante **Adresse IP de portail cible** , sélectionnez l’adresse IP de l’interface de données deuxième activé sur le périphérique.
    -  Cliquez sur **OK** pour retourner dans la boîte de dialogue Propriétés de l’initiateur iSCSI. Vous avez ajouté une deuxième session à la cible.

12. Ouvrez **Gestion de l’ordinateur** en accédant à **Gestionnaire de serveur > tableau de bord > Gestion de l’ordinateur**. Dans le volet gauche, cliquez sur **stockage > Gestion des disques**. L’ou les volumes créé sur le périphérique StorSimple qui sont visibles à cet hôte s’affichent sous **Gestion des disques** en tant que nouveau disques.

13. Initialisation le disque et créez un nouveau volume. Pendant le processus de mise en forme, sélectionnez une taille de bloc de 64 Ko.
![Gestion des disques](./media/storsimple-configure-mpio-windows-server/IC741008.png)
14. Sous **Gestion des disques**, cliquez sur le **disque** et sélectionnez **Propriétés**.
15. Dans le modèle StorSimple ### boîte de dialogue **Propriétés de chemins multiples disque périphérique** , cliquez sur l’onglet **o** .
![StorSimple 8100 disque de chemins multiples DeviceProp.](./media/storsimple-configure-mpio-windows-server/IC741009.png)

16. Dans la section **Nom DSM** , cliquez sur **Détails** et vérifiez que les paramètres sont définis sur les paramètres par défaut. Les paramètres par défaut sont :

    - Chemin d’accès vérifier période = 30
    - Nombre de tentatives = 3
    - AOP supprimer période = 20
    - Intervalle entre les tentatives = 1
    - Vérifier le chemin d’accès activé = désactivée.


>[AZURE.NOTE] **Ne modifiez pas les paramètres par défaut.**

## <a name="step-4-configure-mpio-for-high-availability-and-load-balancing"></a>Étape 4 : Configurer l’o pour la disponibilité et l’équilibrage de charge

Pour les chemins d’accès multiples basés sur haute disponibilité et l’équilibrage de charge, plusieurs sessions doivent être ajoutées manuellement à déclarer les différents chemins disponibles. Par exemple, si l’hôte a deux interfaces connectées à SAN et l’appareil comporte deux interfaces connectés à SAN, puis besoin de quatre sessions configurées avec permutations chemin d’accès approprié (uniquement deux sessions seront requis si chaque interface de données et l’interface de l’hôte se trouvant sur un autre sous-réseau IP et ne sont pas prenant).

>[AZURE.IMPORTANT] **Nous vous recommandons de ne pas mélanger 1 solution et 10 interfaces de réseau de solution. Si vous utilisez deux interfaces réseau, les deux interfaces doivent être du même type.**

La procédure suivante explique comment ajouter des sessions quand un périphérique StorSimple avec deux interfaces réseau est connecté à un hôte avec deux interfaces réseau.

### <a name="to-configure-mpio-for-high-availability-and-load-balancing"></a>Pour configurer o de disponibilité et l’équilibrage de charge

1. Effectuer une découverte de la cible : dans la boîte de dialogue **iSCSI Initiator Properties** , sous l’onglet **recherche** , cliquez sur **Détecter le portail**.
2. Dans la boîte de dialogue **se connecter à la cible** , entrez l’adresse IP d’un des interfaces réseau périphérique.
3. Cliquez sur **OK** pour retourner dans la boîte de dialogue **iSCSI Initiator Properties** .

4. Dans la boîte de dialogue **iSCSI Initiator Properties** , cliquez sur l’onglet **cibles** , mettez en surbrillance la cible découverte, puis cliquez sur **se connecter**. La boîte de dialogue **se connecter à la cible** s’affiche.

5. Dans la boîte de dialogue **se connecter à la cible** :
    
    - Laissez la cible sélectionnée par défaut configuration pour **Ajouter cette connexion** à la liste de favoris cibles. Cette option permettra que l’appareil automatiquement essayez de redémarrer la connexion chaque redémarrage de cet ordinateur.
    - Activez la case à cocher **Activer les chemins d’accès multiples** .
    - Cliquez sur **Avancé**.

6. Dans la boîte de dialogue **Paramètres avancés** :
    - Dans la liste déroulante **Local Adapter** , sélectionnez **Microsoft iSCSI Initiator**.
    - Dans la liste déroulante **Initiateur IP** , sélectionnez l’adresse IP de l’hôte.
    - Dans la liste déroulante **Adresse IP de portail cible** , sélectionnez l’adresse IP de l’interface de données activée sur l’appareil.
    - Cliquez sur **OK** pour retourner dans la boîte de dialogue Propriétés de l’initiateur iSCSI.

7. Cliquez sur **Propriétés**, puis dans la boîte de dialogue **Propriétés** , cliquez sur **Ajout d’une Session**.

8. Dans la boîte de dialogue **se connecter à la cible** , activez la case à cocher **Activer les chemins d’accès multiples** , puis cliquez sur **Avancé**.

9. Dans la boîte de dialogue **Paramètres avancés** :
    1. Dans la liste déroulante **carte locale** , sélectionnez **Microsoft iSCSI Initiator**.
    2. Dans la liste déroulante **Initiateur IP** , sélectionnez l’adresse IP correspondant à la seconde interface sur l’hôte.
    3. Dans la liste déroulante **Adresse IP de portail cible** , sélectionnez l’adresse IP de l’interface de données deuxième activé sur le périphérique.
    4. Cliquez sur **OK** pour retourner dans la boîte de dialogue **iSCSI Initiator Properties** . Vous avez maintenant ajouté une deuxième session à la cible.

10. Répétez les étapes 8 à 10 pour ajouter d’autres sessions (chemins d’accès) à la cible. Deux interfaces sur l’hôte et deux sur l’appareil, vous pouvez ajouter un total de quatre sessions.

11. Après avoir ajouté les sessions souhaitées (chemins d’accès), dans la boîte de dialogue **iSCSI Initiator Properties** , sélectionnez la cible, puis cliquez sur **Propriétés**. Sous l’onglet Sessions de la boîte de dialogue **Propriétés** , notez la session quatre identificateurs qui correspondent aux permutations possibles chemin d’accès. Pour annuler une session, activez la case à cocher en regard d’un identificateur de session, puis cliquez sur **se déconnecter**.

12. Pour afficher les appareils présentés dans les sessions, sélectionnez l’onglet **périphériques** . Pour configurer la stratégie o pour un périphérique sélectionné, cliquez sur **o**. La boîte de dialogue **Détails de l’appareil** s’affichent. Sous l’onglet **o** , vous pouvez sélectionner les paramètres de **Stratégie d’équilibrage de charge** appropriés. Vous pouvez également afficher le type de chemin d’accès **Active** ou **mise en veille** .

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [l’utilisation du service Manager StorSimple pour modifier la configuration de votre appareil StorSimple](storsimple-modify-device-config.md).
 
