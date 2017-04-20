<properties 
    pageTitle="Configurer l’encodeur FMLE pour envoyer un flux en direct unique débit | Microsoft Azure" 
    description="Cette rubrique indique comment configurer l’encodeur Flash Media Live encodeur (FMLE) pour envoyer un flux de débit unique à canaux AMS activés pour le codage live." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="10/12/2016"
    ms.author="juliako;cenkdin;anilmur"/>

#<a name="use-the-fmle-encoder-to-send-a-single-bitrate-live-stream"></a>L’encodeur FMLE permet d’envoyer un flux en direct débit unique

> [AZURE.SELECTOR]
- [FMLE](media-services-configure-fmle-live-encoder.md)
- [Élémentaire Live](media-services-configure-elemental-live-encoder.md)
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [Wirecast](media-services-configure-wirecast-live-encoder.md)

Cette rubrique indique comment configurer l’encodeur [Flash Media Encoder Live](http://www.adobe.com/products/flash-media-encoder.html) (FMLE) pour envoyer un flux de débit unique à canaux AMS activés pour le codage live. Pour plus d’informations, voir [utilisation des canaux qui sont habilités à effectuer encodage Live avec Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Ce didacticiel montre comment gérer Azure Media Services (AMS) avec outil Azure Media Services Explorer (AMSE). Cet outil s’exécute uniquement sur PC Windows. Si vous exécutez Mac ou Linux, utilisez le portail Azure pour créer des [canaux](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) et des [programmes](media-services-portal-creating-live-encoder-enabled-channel.md#create-and-manage-a-program).

Notez que ce didacticiel décrit l’utilisation de AAC. Toutefois, FMLE ne prend en charge AAC par défaut. Vous devez acheter un plug-in pour AAC codage tels que de MainConcept : [AAC plug-in](http://www.mainconcept.com/products/plug-ins/plug-ins-for-adobe/aac-encoder-fmle.html)

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

![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle1.png)

2. Spécifiez un nom de canal, le champ description est facultatif. Sous paramètres de canal, sélectionnez **Standard** pour l’option encodage Live, avec le protocole d’entrée défini sur **RTMP**. Vous pouvez laisser tous les autres paramètres en l’état.


Vérifiez que le **début du nouveau canal maintenant** est sélectionnée.

3. Cliquez sur **créer un canal**.
![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle2.png)

>[AZURE.NOTE] Le canal peut prendre jusqu'à 20 minutes pour démarrer.


Pendant le démarrage du canal, vous pouvez [configurer l’encodeur](media-services-configure-fmle-live-encoder.md#configure_fmle_rtmp).

>[AZURE.IMPORTANT] Notez que facturation débute dès que canal se prolonge dans un état prêt. Pour plus d’informations, voir [les États du canal](media-services-manage-live-encoder-enabled-channels.md#states).

##<a id=configure_fmle_rtmp></a>Configurer l’encodeur FMLE

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

1. Accédez à la Flash Media Live l’encodeur (FMLE) de l’interface sur l’ordinateur en cours d’utilisation.

    L’interface est une page principale de paramètres. Prenez note des éléments suivants à démarrer la diffusion en continu à l’aide de FMLE paramètres recommandés.
    
    - Mise en forme : Fréquence H.264 : 30,00 
    - Taille de l’entrée : 1280 x 720 
    - Débit : kb/s 5000 (peut être ajusté en fonction des limitations réseau)  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle3.png)

    Lors de l’utilisation d’entrelacé sources, veuillez coche l’option « Désentrelacer »

2. Sélectionnez l’icône de clé en regard de Format, ces paramètres supplémentaires doivent être :

    - Profil : principaux
    - Niveau : 4.0
    - Fréquence d’image clé : 2 secondes 
    
    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle4.png)

3. Définissez le paramètre audio importants suivant :
    
    - Format : AAC 
    - Taux d’échantillonnage : 44100 Hz
    - Débit : kb/s 192
    
    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle5.png)

6. Obtenir l’URL d’entrée du canal afin des pour attribuer à de la FMLE **Point de terminaison RTMP**.
    
    Accédez à l’outil AMSE et vérifier l’état d’achèvement de canal. Une fois que l’état a changé de **départ** en cours **d’exécution**, vous pouvez obtenir l’URL d’entrée.
      
    Lorsque le canal est en cours d’exécution, cliquez avec le bouton droit sur le nom du canal, naviguer vers le bas jusqu'à hover sur **Copier l’URL entrée dans le Presse-papiers** , puis sélectionnez **URL entrée principale**.  
    
    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle6.png)

7. Collez ces informations dans le champ **URL FMS** de la section de sortie, puis attribuer un nom de flux de données. 

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle7.png)

    Pour la redondance supplémentaire, répétez ces étapes avec l’URL entrée secondaire.
8. Sélectionnez **se connecter**.

>[AZURE.IMPORTANT]Avant de cliquer sur **se connecter**, vous **devez** vous assurer que le canal est prêt. 
>Assurez-vous également que pour ne pas laisser le canal dans un état prêt sans une contribution d’entrée flux pendant plus de 15 minutes >.

##<a name="test-playback"></a>Test de lecture
  
1. Accédez à l’outil AMSE, puis cliquez avec le bouton droit sur le canal à tester. Dans le menu, pointez sur **lecture l’aperçu** et sélectionnez **avec Azure Media Player**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle8.png)

Si le flux de données apparaît dans le lecteur, l’encodeur a été correctement configuré pour se connecter à AMS. 

Si une erreur est reçue, le canal devra être réinitialisés et régler les paramètres de décodage d’éléments. Consultez la rubrique [résolution des problèmes](media-services-troubleshooting-live-streaming.md) pour obtenir des instructions.  

##<a name="create-a-program"></a>Créer un programme

1. Une fois la lecture de canal confirmée, créez un programme. Sous l’onglet **Live** dans l’outil AMSE, cliquez avec le bouton droit sur dans la zone de programme et sélectionnez **Créer un nouveau programme**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle9.png)

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
