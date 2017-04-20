<properties 
    pageTitle="Formats de flux de travail Media Encoder Premium et codecs | Microsoft Azure" 
    description="Cette rubrique vous donne une vue d’ensemble des codecs et les formats de Media Encoder Premium du flux de travail" 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erik43" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"    
    ms.author="juliako;anilmur"/>

#<a name="media-encoder-premium-workflow-formats-and-codecs"></a>Formats de flux de travail Media Encoder Premium et codecs


>[AZURE.NOTE]Pour les questions de décodage d’éléments premium, messagerie mepd Microsoft.com.
>
>Processeur de média de flux de travail Media Encoder Premium mentionné dans cette rubrique n’est pas disponible en Chine. 

Ce document contient la liste des formats de fichier de sortie et d’entrée et codecs pris en charge par la version de présentation publique de l’encodeur de **Flux de travail Media Encoder Premium** .

[Formats de Worflow entrée Media Encoder Premium et Codecs](#input_formats)

[Formats de sortie Worflow Media Encoder Premium et Codecs](#output_formats)

**Flux de travail Premium Media Encoder** prend en charge les sous-titres décrites dans [cette](#closed_captioning) section. 


##<a id="input_formats"></a>Flux de travail Media Encoder Premium entrée Formats et Codecs

La section suivante répertorie les formats de codec et de fichiers qui prend en charge ce processeur média comme entrée.

###<a name="input-containerfile-formats"></a>Entrée des Formats de fichier/conteneur

- Adobe® Flash® F4V
- MXF/SMPTE 377M
- GXF
- Flux de Transport MPEG-2
- Flux de programme MPEG-2
- MPEG-4/MP4
- Windows Media/ASF
- AVI (non compressé 8 bits/10 bits)

###<a name="input-video-codecs"></a>Codecs vidéo d’entrée

- AVC 8/10 bits bits, jusqu'à 4:2:2, y compris les AVCIntra
- Avid DNxHD (dans MXF)
- DVCPro/DVCProHD (en MXF)
- JPEG2000
- MPEG-2 (jusqu'à 422 profil et de haut niveau, notamment le variantes tels que XDCAM, XDCAM HD, XDCAM IMX, CableLabs® et D10)
- MPEG-1
- Windows Media Video/VC-1

###<a name="input-audio-codecs"></a>Codecs Audio d’entrée

- AES (SMPTE 331 M et 302 M, AES3-2003)
- Dolby® E
- Dolby® Digital (AC3)
- AAC (AAC-LC, HE-AAC et AAC-HEv2 ; jusqu'à 5.1)
- MPEG Layer 2
- MP3 (MPEG-1 Audio Layer 3)
- Audio Windows Media
- WAV/PCM
 
##<a id="output_format"></a>Formats de sortie de flux de travail Media Encoder Premium et Codecs

La section suivante répertorie les formats de codec et de fichier pris en charge en tant que sortie à partir de ce processeur multimédia.

###<a name="output-containerfile-formats"></a>Formats de fichier/conteneur de sortie

- Adobe® Flash® F4V
- MXF (OP1a, XDCAM et AS02)
- DPP (y compris AS11)
- GXF
- MPEG-4/MP4
- Windows Media/ASF
- AVI (non compressé 8 bits/10 bits)
- Format de fichier Smooth Streaming (PIFF 1.3)
- MPEG-TS 


###<a name="output-video-codecs"></a>Codecs vidéo de sortie

- AVC (H.264 ; 8 bits ; jusqu'à profil haute, niveau 5,2 ; 4 K très HD ; AVC interne)
- Avid DNxHD (dans MXF)
- DVCPro/DVCProHD (en MXF)
- MPEG-2 (jusqu'à 422 profil et de haut niveau, notamment le variantes tels que XDCAM, XDCAM HD, XDCAM IMX, CableLabs® et D10)
- MPEG-1
- Windows Media Video/VC-1
- Création d’une miniature JPEG

###<a name="output-audio-codecs"></a>Codecs Audio de sortie

- AES (SMPTE 331 M et 302 M, AES3-2003)
- Dolby® Digital (AC3)
- Dolby® Digital Plus (E-AC3) jusqu'à 7.1
- AAC (AAC-LC, HE-AAC et AAC-HEv2 ; jusqu'à 5.1)
- MPEG Layer 2
- MP3 (MPEG-1 Audio Layer 3)
- Audio Windows Media

##<a id="closed_captioning"></a>Prise en charge des sous-titres

Sous transfert, prend en charge des **Flux de travail Media Encoder Premium** :

1. Fichiers de contrôle de code source
1. Fichiers SMPTE-TT
1. ACE-608/ACE-708 – transposables en tant que données utilisateur (messages SEI de flux élémentaires H.264, ATSC/53, SCTE20) ou exécutée en tant que données connexes dans les fichiers MXF/GXF
1. Fichiers de sous-titre STL

Sous sortie, les options suivantes sont disponibles :

1. ACE-608 ACE 708 traduction
1. ACE-608/ACE-708 traversent (incorporés dans des messages SEI de flux élémentaires H.264 ou transposables données comme Accessoires dans les fichiers MXF)
1. CONTRÔLE DE CODE SOURCE
1. Effet IRIS minuté texte (de la source ACE 608 par RP2052 SMPTE, notamment la création d’un fichier DFXP)
1. Fichier SRT sous-titre
1. Flux de données DVB sous-titre

Remarque : tous les formats de sortie ci-dessus sont prises en charge pour la remise par diffusion en continu dans Azure Media Services.

##<a name="known-issues"></a>Problèmes connus

Si votre vidéo d’entrée ne contient-elle pas des sous-titres, le résultat de biens contient toujours un fichier TTML vide. 


##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
