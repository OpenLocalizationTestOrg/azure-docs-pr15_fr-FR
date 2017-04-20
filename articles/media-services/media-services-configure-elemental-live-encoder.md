<properties 
    pageTitle="Configurer l’encodeur Live élémentaire pour envoyer un flux en direct unique débit | Microsoft Azure" 
    description="Cette rubrique indique comment configurer l’encodeur Live élémentaire pour envoyer un flux de débit unique à canaux AMS activés pour le codage live." 
    services="media-services" 
    documentationCenter="" 
    authors="cenkdin" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="10/12/2016"
    ms.author="cenkdin;anilmur;juliako"/>

#<a name="use-the-elemental-live-encoder-to-send-a-single-bitrate-live-stream"></a>Utiliser l’encodeur Live élémentaire pour envoyer un flux en direct débit unique

> [AZURE.SELECTOR]
- [Élémentaire Live](media-services-configure-elemental-live-encoder.md)
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [Wirecast](media-services-configure-wirecast-live-encoder.md)
- [FMLE](media-services-configure-fmle-live-encoder.md)

Cette rubrique indique comment configurer l’encodeur [Live élémentaire](http://www.elementaltechnologies.com/products/elemental-live) pour envoyer un flux de débit unique à canaux AMS activés pour le codage live.  Pour plus d’informations, voir [utilisation des canaux qui sont habilités à effectuer encodage Live avec Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Ce didacticiel montre comment gérer Azure Media Services (AMS) avec outil Azure Media Services Explorer (AMSE). Cet outil s’exécute uniquement sur PC Windows. Si vous exécutez Mac ou Linux, utilisez le portail Azure pour créer des [canaux](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) et des [programmes](media-services-portal-creating-live-encoder-enabled-channel.md#create-and-manage-a-program).

##<a name="prerequisites"></a>Conditions préalables

- Doit avoir une connaissance de l’utilisation de Live élémentaire une interface de web pour créer des événements en direct.
- [Créer un compte Azure Media Services](media-services-portal-create-account.md)
- Assurez-vous est un point de terminaison la diffusion en cours d’exécution au moins une unité de diffusion en continu alloué. Pour plus d’informations, voir [Gérer les points de terminaison diffusion en continu dans un compte de Services de support](media-services-portal-manage-streaming-endpoints.md).
- Installer la dernière version de l’outil [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) .
- Lancez l’outil et vous connecter à votre compte AMS.

##<a name="tips"></a>Conseils

- Si possible, utilisez une connexion internet non configurable.
- Une règle générale lorsque vous déterminez la bande passante consiste à double la diffusion en continu vitesses de transmission. Ce n’est pas obligatoire, mais elle permet atténuer l’impact d’encombrement du réseau.
- Lorsqu’à l’aide du logiciel basé encodeurs, fermez tous les programmes inutiles.

## <a name="elemental-live-with-rtp-ingest"></a>Live élémentaire avec RTP d’acquisition

Cette section montre comment configurer l’encodeur Live élémentaire permettant d’envoyer un flux en direct débit unique via RTP.  Pour plus d’informations, voir [flux MPEG-TS sur RTP](media-services-manage-live-encoder-enabled-channels.md#channel).

### <a name="create-a-channel"></a>Créer un canal

1.  Dans l’outil AMSE, accédez à l’onglet **Live** , puis cliquez avec le bouton droit sur dans la zone de canal. Sélectionnez **créer une chaîne...** dans le menu.

![Élémentaire](./media/media-services-elemental-live-encoder/media-services-elemental1.png)

2. Spécifiez un nom de canal, le champ description est facultatif. Sous paramètres de canal, sélectionnez **Standard** pour l’option encodage Live, avec le protocole d’entrée défini sur **RTP (MPEG-TS)**. Vous pouvez laisser tous les autres paramètres en l’état.


Vérifiez que le **début du nouveau canal maintenant** est sélectionnée.

3. Cliquez sur **créer un canal**.
![Élémentaire](./media/media-services-elemental-live-encoder/media-services-elemental12.png)

>[AZURE.NOTE] Le canal peut prendre jusqu'à 20 minutes pour démarrer.

Pendant le démarrage du canal, vous pouvez [configurer l’encodeur](media-services-configure-elemental-live-encoder.md#configure_elemental_rtp).

>[AZURE.IMPORTANT] Notez que facturation débute dès que canal se prolonge dans un état prêt. Pour plus d’informations, voir [les États du canal](media-services-manage-live-encoder-enabled-channels.md#states).

###<a id=configure_elemental_rtp></a>Configurer l’encodeur Live élémentaire 

Dans ce didacticiel, les paramètres de sortie suivants sont utilisés. Le reste de cette section décrit les étapes de configuration plus en détail. 

**Vidéo**:
 
- Codec : H.264 
- Profil : Max (niveau 4.0) 
- Débit : kb/s 5000 
- Image clé : 2 secondes (60 secondes) 
- Fréquence d’image : 30
 
**Audio**:

- Codec : AAC (LC) 
- Débit : kb/s 192 
- Taux d’échantillonnage : 44,1 kHz


####<a name="configuration-steps"></a>Étapes de configuration

1. Accédez à l’interface web **Live élémentaire** et configurer l’encodeur de diffusion en continu **UDP/TS** . 

2. Une fois qu’un événement est créé, faites défiler jusqu'à les groupes de sortie et ajoutez le groupe de sortie **UDP/TS** . 

3. Créer une nouvelle sortie en sélectionnant **nouveau flux de données** , puis en cliquant sur **Ajouter une sortie**.  
    
    ![Élémentaire](./media/media-services-elemental-live-encoder/media-services-elemental13.png)
    
    >[AZURE.NOTE] Il est recommandé que l’événement élémentaire comporte le code temporel défini sur « Horloge système » pour aider l’encodeur vous reconnecter en cas d’échec de flux de données.

4. Maintenant que la sortie a été créée, cliquez sur **Ajouter un flux de données**. Les paramètres de sortie peuvent désormais être configurés. 
5. Faites défiler jusqu'à la « flux 1 » qui vient d’être créé, cliquez sur l’onglet **vidéo** sur la gauche et développer la section paramètres **avancés** . 

    ![Élémentaire](./media/media-services-elemental-live-encoder/media-services-elemental4.png)

    Live élémentaire propose une large gamme de personnalisation disponibles, les paramètres suivants sont recommandées pour la mise en route des flux à AMS. 
    
    - Résolution : 1280 x 720 
    - Fréquence d’images : 30 
    - Taille du groupe d’images : 60 images 
    - Entrelacé Mode : Progressive 
    - Débit : (Cela peut être ajusté en fonction des limitations réseau) bit/s 5000000 
    

    ![Élémentaire](./media/media-services-elemental-live-encoder/media-services-elemental5.png)

6. Obtenir l’URL d’entrée du canal.
    
    Accédez à l’outil AMSE et vérifier l’état d’achèvement de canal. Une fois que l’état a changé de **départ** en cours **d’exécution**, vous pouvez obtenir l’URL d’entrée.
      
    Lorsque le canal est en cours d’exécution, cliquez avec le bouton droit sur le nom du canal, naviguer vers le bas jusqu'à hover sur **Copier l’URL entrée dans le Presse-papiers** , puis sélectionnez **URL entrée principale**.  
    
    ![Élémentaire](./media/media-services-elemental-live-encoder/media-services-elemental6.png)
    
1. Coller ces informations dans le champ de **Destination principale** d’éléments. Tous les autres paramètres peuvent rester la valeur par défaut.
    
    ![Élémentaire](./media/media-services-elemental-live-encoder/media-services-elemental14.png)

    Pour la redondance supplémentaire, répétez ces étapes avec l’URL entrée secondaire en créant un autre onglet que « Sortie » pour la diffusion UDP/TS.
    
7. Cliquez sur **créer** (si un nouvel événement a été créé) ou **mise à jour** (si Modifier un événement existant), puis passez à démarrer l’encodeur. 

>[AZURE.IMPORTANT]Avant de cliquer sur **Démarrer** sur l’interface web Live élémentaire, vous **devez** vous assurer que le canal est prêt. 
>Assurez-vous également que pour ne pas laisser le canal dans un état prêt sans un événement pendant plus de 15 minutes >.

Une fois que le flux de données a fonctionné pendant 30 secondes, accédez à la lecture d’outil et test AMSE.  

###<a name="test-playback"></a>Test de lecture
  
1. Accédez à l’outil AMSE, puis cliquez avec le bouton droit sur le canal à tester. Dans le menu, pointez sur **lecture l’aperçu** et sélectionnez **avec Azure Media Player**.  

    ![Élémentaire](./media/media-services-elemental-live-encoder/media-services-elemental8.png)

Si le flux de données apparaît dans le lecteur, l’encodeur a été correctement configuré pour se connecter à AMS. 

Si une erreur est reçue, le canal devra être réinitialisés et régler les paramètres de décodage d’éléments. Consultez la rubrique [résolution des problèmes](media-services-troubleshooting-live-streaming.md) pour obtenir des instructions.   

###<a name="create-a-program"></a>Créer un programme

1. Une fois la lecture de canal confirmée, créez un programme. Sous l’onglet **Live** dans l’outil AMSE, cliquez avec le bouton droit sur dans la zone de programme et sélectionnez **Créer un nouveau programme**.  

    ![Élémentaire](./media/media-services-elemental-live-encoder/media-services-elemental9.png)

2. Le nom du programme et, si nécessaire, ajustez la **Longueur de la fenêtre Archive** (par défaut à 4 heures). Vous pouvez également spécifier un emplacement de stockage ou conservez la valeur par défaut.  
3. Cochez la case **Démarrer le programme maintenant** .
4. Cliquez sur **créer un programme**.  
  
    Remarque : Création d’un programme prend moins de temps que la création du canal.    
 
5. Une fois que le programme est en cours d’exécution, vérifiez la lecture en cliquant avec le bouton droit sur le programme et accédant à **lecture les programmes** , puis en sélectionnant **avec Azure Media Player**.  
6. Une fois que confirmé, avec le bouton droit, cliquez sur le programme à nouveau et sélectionnez **Copier l’URL de sortie dans le Presse-papiers** (ou le récupérer ces informations à partir de l’option **paramètres et informations sur le programme** à partir du menu). 

Le flux de données est maintenant prêt à être incorporés dans un lecteur ou distribué à une audience pour l’affichage actif.  

## <a name="troubleshooting"></a>Résolution des problèmes

Consultez la rubrique [résolution des problèmes](media-services-troubleshooting-live-streaming.md) pour obtenir des instructions. 


##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
