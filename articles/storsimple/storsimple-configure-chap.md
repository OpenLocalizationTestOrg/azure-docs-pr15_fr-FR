<properties 
   pageTitle="Configurer pour votre appareil StorSimple | Microsoft Azure"
   description="Décrit comment configurer le défi négociation d’authentification Protocol () sur un appareil StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="TBD"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="configure-chap-for-your-storsimple-device"></a>Configurer pour votre appareil StorSimple

Ce didacticiel explique comment configurer pour votre appareil StorSimple. La procédure détaillée dans cet article s’applique aux série 8000 StorSimple ainsi que StorSimple 1200 appareils.

CHAP est l’abréviation de protocole d’authentification. Il s’agit d’un schéma d’authentification utilisé par les serveurs pour valider l’identité des clients à distance. La vérification est basée sur un mot de passe partagé ou un code secret. CHAP peut être à sens unique (unidirectionnelle) ou commun (bidirectionnel). CHAP à sens unique est lorsque la cible authentifie un initiateur. CHAP commun ou inverse, nécessite quant à eux, que la cible authentifier l’initiateur et puis l’initiateur authentifie la cible. Authentification de l’initiateur peut être exécutée sans l’authentification cible. Toutefois, l’authentification cible peut être implémentée uniquement si l’authentification de l’initiateur est également implémentée. 

Pour obtenir les meilleurs résultats, nous vous recommandons d’utiliser CHAP pour améliorer la sécurité iSCSI.

>[AZURE.NOTE] N’oubliez pas que IPSEC n'est pas actuellement pris en charge sur les appareils StorSimple.

Les paramètres CHAP sur le périphérique StorSimple peuvent être configurés des façons suivantes :

- Authentification unidirectionnelle ou à sens unique

- Bidirectionnelle ou authentification commun ou inversée

Dans chacune de ces cas, le portail de l’appareil et du logiciel serveur iSCSI initiator doit être configurée. Les étapes détaillées de cette configuration sont décrites dans le didacticiel suivant.

## <a name="unidirectional-or-one-way-authentication"></a>Authentification unidirectionnelle ou à sens unique

Dans l’authentification unidirectionnelle, la cible authentifie l’initiateur. Ce type d’authentification que vous devez configurer les paramètres d’initiateur CHAP sur le périphérique StorSimple et du logiciel iSCSI Initiator sur l’hôte. Les procédures détaillées pour votre appareil StorSimple et hôte Windows sont décrites ci-après.

#### <a name="to-configure-your-device-for-one-way-authentication"></a>Pour configurer votre périphérique pour l’authentification à sens unique

1. Dans le portail classique Azure, dans la page **appareils** , cliquez sur l’onglet **configurer** .

    ![CHAP initiateur](./media/storsimple-configure-chap/IC740943.png)

2. Faites défiler vers le bas sur cette page et dans la section **CHAP initiateur** :
                                                    
    1. Attribuez un nom d’utilisateur de votre initiateur CHAP.

    2. Fournir un mot de passe de votre initiateur CHAP.

         > [AZURE.IMPORTANT] Le nom d’utilisateur CHAP doit contenir moins de 233 caractères. Le mot de passe CHAP doit être comprise entre 12 et 16 caractères. Un nom d’utilisateur ou mot de passe plus entraîne un échec d’authentification sur l’hôte de Windows.
    
    3. Confirmez le mot de passe.

4. Cliquez sur **Enregistrer**. Un message de confirmation s’affiche. Cliquez sur **OK** pour enregistrer les modifications.

#### <a name="to-configure-one-way-authentication-on-the-windows-host-server"></a>Pour configurer l’authentification à sens unique sur le serveur hôte Windows

1. Sur le serveur hôte Windows, démarrez l’initiateur iSCSI.

2. Dans la fenêtre **iSCSI Initiator Properties** , procédez comme suit :
                                                    
    1. Cliquez sur l’onglet **Discovery** .

        ![propriétés de l’initiateur iSCSI](./media/storsimple-configure-chap/IC740944.png)

    2. Cliquez sur **découvrir portail**.

3. Dans la boîte de dialogue **Découvrir le portail cible** :
                                                    
    1. Spécifier l’adresse IP de votre appareil.

    3. Cliquez sur **Avancé**.

        ![Découvrir portail cible](./media/storsimple-configure-chap/IC740945.png)

4. Dans la boîte de dialogue **Paramètres avancés** :
                                                    
    1. Activez la case à cocher **Activer CHAP se connecter** .

    2. Dans le champ **nom** , indiquez le nom d’utilisateur que vous avez spécifié pour l’initiateur CHAP dans le portail classique.

    3. Dans le champ **code secret cible** , indiquez le mot de passe que vous avez spécifié pour l’initiateur CHAP dans le portail classique.

    4. Cliquez sur **OK**.

        ![Paramètres avancés générales](./media/storsimple-configure-chap/IC740946.png)

5. Sous l’onglet **cibles** de la fenêtre **iSCSI Initiator Properties** , l’état de l’appareil doit apparaître comme **connecté**. Si vous utilisez un appareil StorSimple 1200, chaque volume sera monter qu’une cible iSCSI comme illustré ci-dessous. Par conséquent, les étapes 3 et 4 doit être répétées pour chaque volume.

    ![Volumes chargés comme séparez les cibles](./media/storsimple-configure-chap/chap4.png)

    > [AZURE.IMPORTANT] Si vous changez le nom iSCSI, le nouveau nom servira de nouvelles sessions iSCSI. Nouveaux paramètres ne sont pas utilisés à nouveau pour existantes sessions jusqu'à ce que vous vous déconnectez et connectez-vous.

Pour plus d’informations sur la configuration CHAP sur le serveur hôte Windows, accédez à [Considérations supplémentaires](#additional-considerations).


## <a name="bidirectional-or-mutual-authentication"></a>Authentification bidirectionnelle ou communs

Dans l’authentification bidirectionnelle, la cible authentifie l’initiateur et puis l’initiateur authentifie la cible. Cette fonctionnalité nécessite l’utilisateur configurer les paramètres d’initiateur CHAP, ainsi que les paramètres CHAP inversées sur le logiciel initiateur iSCSI et l’appareil sur l’hôte. Les procédures suivantes décrivent les étapes pour configurer l’authentification commun sur l’appareil et sur l’hôte de Windows.

#### <a name="to-configure-your-device-for-mutual-authentication"></a>Pour configurer votre périphérique pour l’authentification commun

1. Dans le portail classique Azure, dans la page **appareils** , cliquez sur l’onglet **configurer** .

    ![CHAP cible](./media/storsimple-configure-chap/IC740948.png)

2. Faites défiler vers le bas sur cette page et dans la section **Cible CHAP** :
                                                    
    1. Attribuez un **nom d’utilisateur inverser CHAP** pour votre appareil.

    2. Fournir un **mot de passe inverser CHAP** pour votre appareil.

    3. Confirmez le mot de passe.

3. Dans la section **CHAP initiateur** :
                                                
    1. Attribuez un **nom d’utilisateur** pour votre appareil.

    1. Fournir un **mot de passe** pour votre appareil.

    3. Confirmez le mot de passe.

4. Cliquez sur **Enregistrer**. Un message de confirmation s’affiche. Cliquez sur **OK** pour enregistrer les modifications.

#### <a name="to-configure-bidirectional-authentication-on-the-windows-host-server"></a>Pour configurer l’authentification bidirectionnelle sur le serveur hôte Windows

1. Sur le serveur hôte Windows, démarrez l’initiateur iSCSI.

2. Dans la fenêtre **iSCSI Initiator Properties** , cliquez sur l’onglet **Configuration** .

3. Cliquez sur **CHAP**.

4. Dans la boîte de dialogue **iSCSI commun initiateur CHAP Secret** :
                                                    
    1. Entrez le **Inverser le mot de passe CHAP** que vous avez configuré dans le portail classique Azure.

    2. Cliquez sur **OK**.

        ![code secret CHAP commun iSCSI initiateur](./media/storsimple-configure-chap/IC740949.png)

5. Cliquez sur l’onglet **cibles** .

6. Cliquez sur le bouton **se connecter** . 

7. Dans la boîte de dialogue **Se connecter à la cible** , cliquez sur **Avancé**.

8. Dans la boîte de dialogue **Propriétés avancées** :
                                                    
    1. Activez la case à cocher **Activer CHAP se connecter** .

    2. Dans le champ **nom** , indiquez le nom d’utilisateur que vous avez spécifié pour l’initiateur CHAP dans le portail classique.

    3. Dans le champ **code secret cible** , indiquez le mot de passe que vous avez spécifié pour l’initiateur CHAP dans le portail classique.

    4. Activez la case à cocher **effectuer l’authentification commun** .

        ![Authentification commun des paramètres avancés](./media/storsimple-configure-chap/IC740950.png)

    5. Cliquez sur **OK** pour terminer la configuration CHAP
     
Pour plus d’informations sur la configuration CHAP sur le serveur hôte Windows, accédez à [Considérations supplémentaires](#additional-considerations).

## <a name="additional-considerations"></a>Considérations supplémentaires

La fonctionnalité **Connexion rapide** ne prend pas en charge les connexions ayant pour activer ce protocole. Lorsque CHAP est activée, assurez-vous que vous utilisez le bouton de **connexion** est disponible sous l’onglet **cibles** pour vous connecter à une cible.

![Se connecter à la cible](./media/storsimple-configure-chap/IC740947.png)

Dans la boîte de dialogue **se connecter à la cible** qui est présentée, activez la case à cocher **Ajouter cette connexion à la liste de favoris cibles** . Cela garantit que chaque fois que l’ordinateur redémarre, une tentative est effectuée pour restaurer la connexion aux cibles iSCSI favorites.

## <a name="errors-during-configuration"></a>Erreurs lors de la configuration

Si votre configuration CHAP est incorrecte, vous êtes probablement afficher un message d’erreur **Échec de l’authentification** .

## <a name="verification-of-chap-configuration"></a>Vérification de la configuration CHAP

Vous pouvez vérifier que CHAP est utilisée en procédant comme suit.

#### <a name="to-verify-your-chap-configuration"></a>Pour vérifier votre configuration CHAP

1. Cliquez sur **Favoris cibles**.

2. Sélectionnez la cible dont vous avez activé l’authentification.

3. Cliquez sur **Détails**.

    ![cibles favorites des propriétés initiateur iSCSI](./media/storsimple-configure-chap/IC740951.png)

4. Dans la boîte de dialogue **Détails de la cible favoris** , notez l’entrée dans le champ **authentification** . Si la configuration a réussi, vous devriez voir **CHAP**.

    ![Détails de la cible favoris](./media/storsimple-configure-chap/IC740952.png)

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [sécurité StorSimple](storsimple-security.md).

- En savoir plus sur [l’utilisation du service Manager StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).
