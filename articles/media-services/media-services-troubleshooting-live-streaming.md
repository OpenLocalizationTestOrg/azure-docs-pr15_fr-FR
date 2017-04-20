<properties 
    pageTitle="Guide de dépannage pour la diffusion en continu live | Microsoft Azure" 
    description="Cette rubrique explique comment résoudre les problèmes de diffusion en continu live suggestions." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/12/2016"  
    ms.author="juliako"/>

#<a name="troubleshooting-guide-for-live-streaming"></a>Guide de dépannage pour la diffusion en continu live

Cette rubrique explique comment résoudre certains problèmes de diffusion en continu live suggestions.

## <a name="issues-related-to-on-premises-encoders"></a>Problèmes liés aux codeurs en local 

Cette section fournit des suggestions sur la façon de résoudre les problèmes liés à des codeurs locaux qui sont configurés pour envoyer un flux de débit unique à canaux AMS activés pour le codage live.

###<a name="problem-would-like-to-see-logs"></a>Problème : Souhaiteriez journaux 

- **Problème potentiel**: ne peut pas rechercher les journaux de décodage d’éléments peuvent vous aider dans débogage des problèmes.
    
    - **Telestream Wirecast**: vous pouvez généralement trouver les journaux sous C:\Users\{nom d’utilisateur} \AppData\Roaming\Wirecast\ 
    - **Live élémentaire**: vous pouvez trouver contient des liens vers des journaux dans le portail de gestion. Cliquez sur **statistiques**, puis **les journaux**. Dans la page **Fichiers journaux** , vous verrez une liste des journaux pour tous les éléments d’événement en direct ; Sélectionnez l’option correspondant à votre session active. 
    - **Flash Media Encoder Live**: vous pouvez trouver le **Répertoire journal...** en accédant à l’onglet **Codage de journal** .
    
###<a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>Problème : Il n’existe aucune option pour sortir un flux progressif

- **Problème**: l’encodeur utilisé ne désentrelacer automatiquement. 

    **Étapes de dépannage**: recherchez l’option désentrelacement au sein de l’interface de décodage d’éléments. Une fois DESENTRELACEMENT est activé, vérifiez à nouveau les paramètres de sortie progressive. 
 
###<a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>Problème : Essayé plusieurs paramètres de sortie de décodage d’éléments et parvenez toujours pas à vous connecter. 

- **Problème potentiel**: canal codage Azure a été réinitialisé pas correctement. 

    **Étapes de dépannage**: Assurez-vous que l’encodeur est reçue n’est plus à AMS, arrêtez et réinitialiser le canal. Une fois en cours d’exécution, essayez de vous connecter votre encodeur avec les nouveaux paramètres. Si cela ne résout toujours pas le problème, essayez de créer entièrement un nouveau canal, parfois canaux peuvent être endommagés après que plusieurs tentatives.  

- **Problème potentiel**: taille du groupe d’images ou image clé paramètres ne sont pas optimales. 

    **Étapes de dépannage**: taille ou image clé recommandé de groupe d’images est 2 secondes. Certains codeurs calculent ce paramètre en nombre d’images, tandis que d’autres personnes utilisent secondes. Par exemple : lors de la sortie 30 i/s, la taille de groupe d’images serait 60 d’images, qui équivaut à 2 secondes.  
     
- **Problème potentiel**: ports fermés bloquent le flux de données. 

    **Étapes de dépannage**: lors de la diffusion en continu via RTMP, vérifiez les paramètres de pare-feu et/ou proxy pour confirmer que les ports de sortie 1935 et 1936 sont ouverts. Lorsque vous utilisez flux RTP, vérifiez que le port de sortie 2010 est ouvert. 


###<a name="problem-when-configuring-the-encoder-to-stream-with-the-rtp-protocol-there-is-no-place-to-enter-a-host-name"></a>Problème : Lorsque vous configurez l’encodeur de flux avec le protocole RTP, il est sans entrer un nom d’hôte. 

- **Problème potentiel**: codeurs de nombreux RTP ne permettent pas de noms d’hôtes et une adresse IP devra être acquis.  

    **Étapes de dépannage**: pour trouver l’adresse IP, ouvrez une invite de commande sur n’importe quel ordinateur. Pour ce faire, dans Windows, ouvrez le Lanceur d’exécuter (WIN + R), puis tapez « cmd » pour l’ouvrir.  

    Une fois que l’invite de commandes est ouvert, tapez « Ping [AMS Host Name] ». 

    Le nom d’hôte peut être obtenue en omettant le numéro de port de l’Azure d’acquisition URL, comme illustré dans l’exemple suivant : 

    RTP://Test2-amstest009.RTP.Channel.mediaservices.Windows.NET:2010 / 

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle10.png)

###<a name="problem-unable-to-playback-the-published-stream"></a>Problème : Impossible de lecture du flux de données publié.
 
- **Problème potentiel**: il n’existe aucun point de terminaison la diffusion en cours d’exécution, ou il n’existe aucune diffusion en continu unités (échelle) allouées. 

    **Étapes de dépannage**: accédez à l’onglet « Diffusion en continu de point de terminaison » dans l’outil AMSE et confirmez il y a un point de terminaison la diffusion en cours d’exécution avec une unité de diffusion en continu. 
    


>[AZURE.NOTE] Si, après avoir suivi les étapes de dépannage que vous ne pouvez pas diffuser toujours avec succès, envoyer une demande d’assistance à l’aide du portail Azure.

##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
