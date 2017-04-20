<properties 
    pageTitle="Codecs et les formats Standard de décodage d’éléments multimédias" 
    description="Cette rubrique fournit une vue d’ensemble des codecs et les formats Media Encoder Standard." 
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
    ms.date="10/10/2016"
    ms.author="juliako;anilmur"/>

#<a name="media-encoder-standard-formats-and-codecs"></a>Formats de codec et Media Encoder Standard


Ce document contient la liste de l’importation plus courante et les formats de fichier d’exportation que vous pouvez utiliser avec Media Encoder Standard.


##<a name="input-containerfile-formats"></a>Entrée des Formats de fichier/conteneur

Formats de fichier (extensions de fichier)|Prise en charge
---|---|---|---
FLV (avec les codecs H.264 et AAC) (.flv)          |Oui 
MXF (.mxf)                  |Oui 
GXF (.gxf)                  |Oui 
MPEG2-PS, MPEG2-TS, 3GP (.ts, .ps, .3gp, .3gpp, .mpg)   |Oui 
Windows Media Video (WMV) / ASF (.wmv, .asf) |Oui 
AVI (non compressé 8 bits/10 bits) (.avi)|Oui 
MP4 (.mp4, .m4a, .m4v) / ISMV (.isma, .ismv)|Oui 
[Recording(DVR-MS) vidéo Microsoft Photo](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr-ms) |Oui 
Matroska/WebM (.mkv)        |Oui 
VAGUE/WAV (.wav) |Oui 
QuickTime (.mov) |Oui

>[AZURE.NOTE] Ci-dessus est une liste des extensions de fichier plus fréquemment rencontrés. Media Encoder Standard prend-elle en charge les autres (par exemple : .m2ts, .mpeg2video, .qt). Si vous essayez de coder un fichier et que vous obtenez un message d’erreur sur le format n’est ne pas pris en charge, veuillez fournir un commentaires [ici](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/).

###<a name="audio-formats-in-input-containers"></a>Formats audio dans des conteneurs de saisie 

Media Encoder Standard prend en charge effectuer les formats audio suivants dans des conteneurs d’entrée :

- MXF, GXF et QuickTime les fichiers qui ont pistes audio avec entrelacé stéréo ou 5.1 exemples

ou

- Fichiers MXF, GXF et QuickTime où l’audio est exécutée en tant que pistes PCM distinctes, mais le mappage de canal (en stéréo ou 5.1) peut être déduit à partir des métadonnées de fichier

Remarque qui prennent en charge pour la correspondance des canaux fourni par l’utilisateur/explicites est fournie dans un futur proche.


##<a name="input-video-codecs"></a>Codecs vidéo d’entrée

Codecs vidéo d’entrée|Prise en charge
---|---|---|---
AVC 8/10 bits bits, jusqu'à 4:2:2, y compris les AVCIntra   |8 bits 4:2:0 et 4:2:2 
Avid DNxHD (dans MXF)                                 |Oui 
DVCPro/DVCProHD (en MXF)                            |Oui 
Vidéo numérique (DV) (dans les fichiers AVI)                   |Oui
JPEG 2000                                           |Oui 
MPEG-2 (jusqu'à 422 profil et de haut niveau, notamment le variantes tels que XDCAM, XDCAM HD, XDCAM IMX, CableLabs® et D10)|Profil jusqu'à 422 
MPEG-1                                              |Oui 
VC-1/WMV9                                           |Oui 
Canopus siège social/HQX                                      |N° 
MPEG-4, partie 2                                       |Oui 
[Theora](https://en.wikipedia.org/wiki/Theora)      |Oui 
YUV420 décompressé ou mezzanine                   |Oui
Apple ProRes 422                                    |Oui
Apple ProRes 422 LT |Oui
Apple ProRes 422 siège |Oui
Apple ProRes Proxy|Oui
Apple ProRes 4444 |Oui
Apple ProRes 4444 XQ |Oui



##<a name="input-audio-codecs"></a>Codecs Audio d’entrée

Codecs Audio d’entrée|Prise en charge
---|---|---|---
AAC (AAC-LC, HE-AAC et AAC-HEv2 ; jusqu'à 5.1)|Oui 
MPEG Layer 2|Oui 
MP3 (MPEG-1 Audio Layer 3)|Oui 
Audio Windows Media|Oui 
WAV/PCM|Oui 
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|Oui 
[Signature](http://go.microsoft.com/fwlink/?LinkId=822667) |Oui 
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|Oui 
AMR (débit à plusieurs)|Oui
AES (SMPTE 331 M et 302 M, AES3-2003)        |N° 
Dolby® E                                    |N° 
Dolby® Digital (AC3)                        |N° 
Dolby® numérique Plus (E-AC3)                 |N° 


##<a name="output-formats-and-codecs"></a>Codecs et les Formats de sortie

Le tableau suivant répertorie les formats de codec et de fichier pris en charge pour l’exportation.


Format de fichier|Codec vidéo|Codec audio
---|---|---
MP4 <br/><br/>(y compris les conteneurs de MP4 multi-débit) |H.264 (haute, Main et les profils de référence)|AAC-LC, HE-AAC v1, HE-AAC v2 
MPEG2-TS |H.264 (haute, Main et les profils de référence)|AAC-LC, HE-AAC v1, HE-AAC v2 



##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Voir aussi

[Codage de contenu à la demande avec Azure Media Services](media-services-encode-asset.md)

[Comment encoder avec Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)
