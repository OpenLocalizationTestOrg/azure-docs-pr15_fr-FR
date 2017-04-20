<properties 
    pageTitle="Lecture du contenu | Microsoft Azure" 
    description="Cette rubrique répertorie les lecteurs existants que vous pouvez utiliser pour la lecture de votre contenu." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
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


#<a name="playing-your-content-with-existing-players"></a>Lecture du contenu avec lecteurs existants

Azure Media Services prend en charge plusieurs formats de diffusion en continu populaires, tels que Smooth Streaming HTTP Live diffusion en continu et MPEG-tiret. Cette rubrique vous oriente vers lecteurs existants que vous pouvez utiliser pour tester votre flux de données.

>[AZURE.NOTE]Pour lire dynamiquement empaqueté ou contenu dynamique chiffré, veillez à obtenir au moins une unité de diffusion en continu pour le point de terminaison de diffusion en continu à partir de laquelle vous souhaitez présenter votre contenu. Pour plus d’informations sur la mise à l’échelle des unités de diffusion en continu, voir : [comment adapter les unités de diffusion en continu](media-services-portal-manage-streaming-endpoints.md).

### <a name="the-azure-portal-media-services-content-player"></a>Le lecteur de contenu Media Services du portail Azure

Le portail **Azure** fournit un lecteur de contenu que vous pouvez utiliser pour tester votre vidéo.

Cliquez sur la vidéo souhaitée (Vérifiez qu’il a été [publié](media-services-portal-publish.md)) et cliquez sur le bouton de **lecture** en bas du portail.

Certaines considérations s’appliquent :

- Le **Lecteur de contenu MEDIA SERVICES** est lu à partir de la valeur par défaut diffusion en continu de point de terminaison. Si vous souhaitez lire à partir d’un point de terminaison de la diffusion en continu de ceux définis par défaut, utilisez un autre lecteur. Par exemple, [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).


![AMSPlayer][AMSPlayer]

###<a name="azure-media-player"></a>Lecteur multimédia Azure

Utilisez [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) pour lecture votre contenu (effacer ou protégé) dans un des formats suivants :

- Diffusion en continu lisse
- TIRET MPEG
- TLS
- MP4 progressive


###<a name="flash-player"></a>Adobe Flash Player

####<a name="aes-encrypted-with-token"></a>Chiffrées AES avec jeton

[http://aestoken.azurewebsites.NET](http://aestoken.azurewebsites.net)

###<a name="silverlight-players"></a>Lecteurs Silverlight

####<a name="monitoring"></a>Surveillance des mots clés

[http://SMF.cloudapp.NET/healthmonitor](http://smf.cloudapp.net/healthmonitor)

####<a name="playready-with-token"></a>PlayReady avec jeton

[http://sltoken.azurewebsites.NET](http://sltoken.azurewebsites.net)

### <a name="dash-players"></a>TIRET joueurs

[http://dashplayer.azurewebsites.NET](http://dashplayer.azurewebsites.net)

[http://dashif.org](http://dashif.org)

###<a name="other"></a>Autres

Pour tester les URL TLS vous pouvez également utiliser :

- **Safari** sur un appareil iOS ou
- **le lecteur TLS 3ivx** sous Windows.

##<a name="developing-video-players"></a>Développer les lecteurs vidéo

Pour plus d’informations sur le développement de votre propre joueurs, voir [lecteurs vidéo de développement](media-services-develop-video-players.md)




##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png
