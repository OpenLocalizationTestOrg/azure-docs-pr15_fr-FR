<properties 
    pageTitle="Configurer l’encodeur NewTek TriCaster pour envoyer un flux en direct unique débit | Microsoft Azure" 
    description="Cette rubrique indique comment configurer l’encodeur live Tricaster pour envoyer un flux de débit unique à canaux AMS activés pour le codage live." 
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
    ms.author="juliako;cenkd;anilmur"/>

#<a name="use-the-newtek-tricaster-encoder-to-send-a-single-bitrate-live-stream"></a>L’encodeur NewTek TriCaster permet d’envoyer un flux en direct débit unique

> [AZURE.SELECTOR]
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [Élémentaire Live](media-services-configure-elemental-live-encoder.md)
- [Wirecast](media-services-configure-wirecast-live-encoder.md)
- [FMLE](media-services-configure-fmle-live-encoder.md)

Cette rubrique indique comment configurer l’encodeur live [NewTek TriCaster](http://newtek.com/products/tricaster-40.html) pour envoyer un flux de débit unique à canaux AMS activés pour le codage live. Pour plus d’informations, voir [utilisation des canaux qui sont habilités à effectuer encodage Live avec Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Ce didacticiel montre comment gérer Azure Media Services (AMS) avec outil Azure Media Services Explorer (AMSE). Cet outil s’exécute uniquement sur PC Windows. Si vous exécutez Mac ou Linux, utilisez le portail Azure pour créer des [canaux](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) et des [programmes](media-services-portal-creating-live-encoder-enabled-channel.md#create-and-manage-a-program).

>[AZURE.NOTE]Lorsque vous utilisez Tricaster pour l’envoi une contribution flux à canaux AMS activés pour le codage live, on peut observer problèmes audio/vidéo dans votre événement en direct si vous utilisez certaines fonctionnalités de Tricaster, par exemple rapide coupant entre flux ou passer vers ou à partir de tablettes tactiles. L’équipe AMS travaille sur la résolution de ces problèmes, jusque là, il n'est pas recommandé d’utiliser ces fonctionnalités.


##<a name="prerequisites"></a>Conditions préalables

- [Créer un compte Azure Media Services](media-services-portal-create-account.md)
- Assurez-vous est un point de terminaison la diffusion en cours d’exécution au moins une unité de diffusion en continu alloué. Pour plus d’informations, voir [Gérer les points de terminaison diffusion en continu dans un compte de Services de support](media-services-portal-manage-streaming-endpoints.md)
- Installer la dernière version de l’outil [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) .
- Lancez l’outil et vous connecter à votre compte AMS.

##<a name="tips"></a>Conseils

- Si possible, utilisez une connexion internet non configurable.
- Une règle générale lorsque vous déterminez la bande passante consiste à double la diffusion en continu vitesses de transmission. Ce n’est pas obligatoire, mais elle permet atténuer l’impact d’encombrement du réseau.
- Lorsqu’à l’aide du logiciel basé encodeurs, fermez tous les programmes inutiles.

## <a name="create-a-channel"></a>Créer un canal

1.  Dans l’outil AMSE, accédez à l’onglet **Live** , puis cliquez avec le bouton droit sur dans la zone de canal. Sélectionnez **créer une chaîne...** dans le menu.

![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster1.png)

2. Spécifiez un nom de canal, le champ description est facultatif. Sous paramètres de canal, sélectionnez **Standard** pour l’option encodage Live, avec le protocole d’entrée défini sur **RTMP**. Vous pouvez laisser tous les autres paramètres en l’état.


Vérifiez que le **début du nouveau canal maintenant** est sélectionnée.

3. Cliquez sur **créer un canal**.
![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster2.png)

>[AZURE.NOTE] Le canal peut prendre jusqu'à 20 minutes pour démarrer.


Pendant le démarrage du canal, vous pouvez [configurer l’encodeur](media-services-configure-tricaster-live-encoder.md#configure_tricaster_rtmp).

>[AZURE.IMPORTANT] Notez que facturation débute dès que canal se prolonge dans un état prêt. Pour plus d’informations, voir [les États du canal](media-services-manage-live-encoder-enabled-channels.md#states).

##<a id=configure_tricaster_rtmp></a>Configurer l’encodeur NewTek TriCaster

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


###<a name="configuration-steps"></a>Étapes de configuration

1. Créer un projet **NewTek TriCaster** en fonction de la source d’entrée vidéo est utilisée. 
2. Une fois dans ce projet, recherchez le bouton **flux** , puis cliquez sur l’icône d’engrenage apposé pour accéder au menu de configuration de flux.

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster3.png)
3. Une fois le menu ouvert, cliquez sur **Nouveau** sous l’en-tête de la connexion. Lorsque vous y êtes invité pour le type de connexion, sélectionnez **Adobe Flash**.

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster4.png)

4. Cliquez sur **OK**.

5. Un profil FMLE peut être importé en cliquant sur la flèche déroulante sous **Profil de diffusion en continu** et en accédant à **Parcourir**.

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster5.png)

6. Accédez à l’endroit où le profil FMLE configuré a été enregistré.
7. Sélectionnez-le, puis appuyez sur **OK**.

    Une fois que le profil est téléchargé, passez à l’étape suivante.

6. Obtenir l’URL d’entrée du canal afin des pour attribuer à Tricaster **Point de terminaison RTMP**.
    
    Accédez à l’outil AMSE et vérifier l’état d’achèvement de canal. Une fois que l’état a changé de **départ** en cours **d’exécution**, vous pouvez obtenir l’URL d’entrée.
      
    Lorsque le canal est en cours d’exécution, cliquez avec le bouton droit sur le nom du canal, naviguer vers le bas jusqu'à hover sur **Copier l’URL entrée dans le Presse-papiers** , puis sélectionnez **URL entrée principale**.  
    
    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster6.png)

7. Coller ces informations dans le champ **emplacement** sous **Flash Server** au sein du projet Tricaster. Également attribuer un nom de flux de données dans le champ **ID flux de données** . 

    Si les informations de flux de données a été ajoutées au profil FMLE, il peut également être importé dans cette section en cliquant sur **Paramètres d’importation**, déplacement vers le profil FMLE enregistré sur **OK**. Les champs appropriés Flash Server doivent remplir avec les informations FMLE.

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster7.png)

9. Lorsque vous avez terminé, cliquez sur **OK** dans la partie inférieure de l’écran. Lorsque les entrées audio et vidéo de la Tricaster sont prêtes, commencer la diffusion en continu à AMS en cliquant sur le bouton **flux** .

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster11.png)

>[AZURE.IMPORTANT]Avant de cliquer sur **flux de données**, vous **devez** vous assurer que le canal est prêt. 
>Assurez-vous également que pour ne pas laisser le canal dans un état prêt sans une contribution d’entrée flux pendant plus de 15 minutes >. 

##<a name="test-playback"></a>Test de lecture
  
1. Accédez à l’outil AMSE, puis cliquez avec le bouton droit sur le canal à tester. Dans le menu, pointez sur **lecture l’aperçu** et sélectionnez **avec Azure Media Player**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster8.png)

Si le flux de données apparaît dans le lecteur, l’encodeur a été correctement configuré pour se connecter à AMS. 

Si une erreur est reçue, le canal devra être réinitialisés et régler les paramètres de décodage d’éléments. Consultez la rubrique [résolution des problèmes](media-services-troubleshooting-live-streaming.md) pour obtenir des instructions.  

##<a name="create-a-program"></a>Créer un programme

1. Une fois la lecture de canal confirmée, créez un programme. Sous l’onglet **Live** dans l’outil AMSE, cliquez avec le bouton droit sur dans la zone de programme et sélectionnez **Créer un nouveau programme**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster9.png)

2. Le nom du programme et, si nécessaire, ajustez la **Longueur de la fenêtre Archive** (par défaut à 4 heures). Vous pouvez également spécifier un emplacement de stockage ou conservez la valeur par défaut.  
3. Cochez la case **Démarrer le programme maintenant** .
4. Cliquez sur **créer un programme**.  
  
    Remarque : Création d’un programme prend moins de temps que la création du canal.    
 
5. Une fois que le programme est en cours d’exécution, vérifiez la lecture en cliquant avec le bouton droit sur le programme et accédant à **lecture les programmes** , puis en sélectionnant **avec Azure Media Player**.  
6. Une fois que confirmé, avec le bouton droit, cliquez sur le programme à nouveau et sélectionnez **Copier l’URL de sortie dans le Presse-papiers** (ou le récupérer ces informations à partir de l’option **paramètres et informations sur le programme** à partir du menu). 

Le flux de données est maintenant prêt à être incorporés dans un lecteur ou distribué à une audience pour l’affichage actif.  


## <a name="troubleshooting"></a>Résolution des problèmes

Consultez la rubrique [résolution des problèmes](media-services-troubleshooting-live-streaming.md) pour obtenir des instructions. 


##<a name="next-step"></a>Étape suivante

Passez en revue Media Services rubriques d’apprentissage professionnelles.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
