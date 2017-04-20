<properties 
    pageTitle="Vue d’ensemble et de comparaison de Azure sur codeurs media à la demande | Microsoft Azure" 
    description="Cette rubrique fournit une vue d’ensemble et de comparaison de Azure sur codeurs media à la demande." 
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
    ms.date="09/19/2016" 
    ms.author="juliako"/>

#<a name="overview-and-comparison-of-azure-on-demand-media-encoders"></a>Vue d’ensemble et de comparaison de Azure sur codeurs media à la demande

##<a name="encoding-overview"></a>Vue d’ensemble de codage

Azure Media Services offre plusieurs options pour le codage des éléments multimédias dans le cloud.

Lorsque commençant par Media Services, il est important de comprendre la différence entre les formats de codec et de fichier.
Codecs sont des logiciels qui mettent en œuvre, les algorithmes de compression/décompression alors que les formats de fichier sont des conteneurs qui contiennent les données vidéo compressées.

Media Services fournit emballage dynamique qui vous permet d’effectuer votre contenu adaptive débit MP4 ou Smooth Streaming codé en continu les formats pris en charge par Media Services (MPEG tiret, TLS, Smooth Streaming, HDS) sans avoir à nouveau empaqueter dans ces formats de diffusion en continu.

Pour tirer parti [d’emballage dynamique](media-services-dynamic-packaging-overview.md), vous devez effectuer les opérations suivantes :

- Encoder votre fichier mezzanine (source) dans un ensemble de débit adaptive MP4 fichiers ou débit adaptive Smooth Streaming (les étapes de codage vous trouverez plus loin dans ce didacticiel).
- Obtenir au moins une unité de diffusion en continu à la demande pour le point de terminaison de diffusion en continu à partir de laquelle vous envisagez de remise votre contenu. Pour plus d’informations, voir [comment à la demande en continu réservés unités de l’échelle](media-services-portal-manage-streaming-endpoints.md).

Media Services prend en charge ce qui suit encodeurs à la demande qui sont décrites dans cet article :

- [Standard de décodage d’éléments multimédias](media-services-encode-asset.md#media-encoder-standard)
- [Flux de travail Media Encoder Premium](media-services-encode-asset.md#media-encoder-premium-workflow)

Cet article donne un bref aperçu à la demande codeurs media et fournit des liens vers des articles qui donnent des informations plus détaillées. La rubrique fournit également comparaison des encodeurs.

Notez que par défaut chaque compte Media Services peut avoir une tâche de codage active à la fois. Vous pouvez réserver des unités de codage qui vous permettent d’avoir plusieurs tâches d’encodage qui s’exécutent simultanément, une pour chaque unité réservée codage que vous achetez. Pour plus d’informations, voir [mise à l’échelle codage unités](media-services-scale-media-processing-overview.md).

##<a name="media-encoder-standard"></a>Standard de décodage d’éléments multimédias

###<a name="how-to-use"></a>Comment utiliser

[Comment encoder avec Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

###<a name="formats"></a>Mettre en forme

[Formats et codecs](media-services-media-encoder-standard-formats.md)

###<a name="presets"></a>Présélections

Media Encoder Standard est configuré à l’aide d’une de l’encodeur Présélections décrites [ici](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

###<a name="input-and-output-metadata"></a>Métadonnées d’entrée et de sortie

Les métadonnées d’entrée encodeurs sont décrit [ici](http://msdn.microsoft.com/library/azure/dn783120.aspx).

Les métadonnées de sortie encodeurs sont décrit [ici](http://msdn.microsoft.com/library/azure/dn783217.aspx).

###<a name="generate-thumbnails"></a>Générer des miniatures

Pour plus d’informations, voir [Comment faire pour générer des miniatures à l’aide de Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md#thumbnails).

###<a name="trim-videos-clipping"></a>Découper les vidéos (découpage)

Pour plus d’informations, voir [le découpage de vidéos à l’aide de Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md#trim_video).

###<a name="create-overlays"></a>Créer des calques

Pour plus d’informations, voir [comment créer des superpositions à l’aide de Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md#overlay).

###<a name="see-also"></a>Voir aussi

[Le blog Media Services](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)
 
##<a name="media-encoder-premium-workflow"></a>Flux de travail Media Encoder Premium

###<a name="overview"></a>Vue d’ensemble

[Présentation de Premium codage dans Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

###<a name="how-to-use"></a>Comment utiliser

Flux de travail Media Encoder Premium est configuré à l’aide de flux de travail complexes. Fichiers de flux de travail peuvent être créés et mis à jour à l’aide de l’outil [Concepteur de flux de travail](media-services-workflow-designer.md) .

[Comment utiliser Premium codage dans Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

###<a name="known-issues"></a>Problèmes connus

Si votre vidéo d’entrée ne contient-elle pas des sous-titres, le résultat de biens contient toujours un fichier TTML vide. 


##<a id="compare_encoders"></a>Comparer encodeurs

###<a id="billing"></a>Compteur de facturation utilisé par chaque encodeur

Nom du processeur multimédia|Tarifs applicables|Notes
---|---|---
**Standard de décodage d’éléments multimédias** |DÉCODAGE D’ÉLÉMENTS|Codage de tâches est facturé selon la taille de la sortie de biens, dans Go, au taux indiqué [ici][1], sous la colonne de décodage d’éléments.
**Flux de travail Media Encoder Premium** |ENCODEUR PREMIUM|Codage de tâches est facturé selon la taille de la sortie actif, dans Go, au taux indiqué [ici][1], sous la colonne encodeur PREMIUM.


Cette section compare les fonctionnalités codage de **Media Encoder Standard** et **Flux de travail Media Encoder Premium**.


###<a name="input-containerfile-formats"></a>Entrée des Formats de fichier/conteneur

Entrée des Formats de fichier/conteneur|Standard de décodage d’éléments multimédias|Flux de travail Media Encoder Premium
---|---|---
Adobe® Flash® F4V           |Oui|Oui
MXF/SMPTE 377M              |Oui|Oui
GXF                         |Oui|Oui
Flux de Transport MPEG-2    |Oui|Oui
Flux de programme MPEG-2      |Oui|Oui
MPEG-4/MP4                  |Oui|Oui
Windows Media/ASF           |Oui|Oui
AVI (non compressé 8 bits/10 bits)|Oui|Oui
3GPP/3GPP2                  |Oui|N°
Format de fichier Smooth Streaming (PIFF 1.3)|Oui|N°
[Recording(DVR-MS) vidéo Microsoft Photo](https://msdn.microsoft.com/library/windows/desktop/dd692984)|Oui|N°
Matroska/WebM               |Oui|N°
QuickTime (.mov) |Oui|N°

###<a name="input-video-codecs"></a>Codecs vidéo d’entrée

Codecs vidéo d’entrée|Standard de décodage d’éléments multimédias|Flux de travail Media Encoder Premium
---|---|---
AVC 8/10 bits bits, jusqu'à 4:2:2, y compris les AVCIntra   |8 bits 4:2:0 et 4:2:2|Oui
Avid DNxHD (dans MXF)                                 |Oui|Oui
DVCPro/DVCProHD (en MXF)                            |Oui|Oui
JPEG2000                                            |Oui|Oui
MPEG-2 (jusqu'à 422 profil et de haut niveau, notamment le variantes tels que XDCAM, XDCAM HD, XDCAM IMX, CableLabs® et D10)|Profil jusqu'à 422|Oui
MPEG-1                                              |Oui|Oui
Windows Media Video/VC-1                            |Oui|Oui
Canopus siège social/HQX                                      |N°|N°
MPEG-4, partie 2                                       |Oui|N°
[Theora](https://en.wikipedia.org/wiki/Theora)      |Oui|N°
Apple ProRes 422    |Oui|N°
Apple ProRes 422 LT |Oui|N°
Apple ProRes 422 siège |Oui|N°
Apple ProRes Proxy|Oui|N°
Apple ProRes 4444 |Oui|N°
Apple ProRes 4444 XQ |Oui|N°

###<a name="input-audio-codecs"></a>Codecs Audio d’entrée

Codecs Audio d’entrée|Standard de décodage d’éléments multimédias|Flux de travail Media Encoder Premium
---|---|---
AES (SMPTE 331 M et 302 M, AES3-2003)        |N°|Oui
Dolby® E                                    |N°|Oui
Dolby® Digital (AC3)                        |N°|Oui
Dolby® numérique Plus (E-AC3)                 |N°|Oui
AAC (AAC-LC, HE-AAC et AAC-HEv2 ; jusqu'à 5.1)|Oui|Oui
MPEG Layer 2|Oui|Oui
MP3 (MPEG-1 Audio Layer 3)|Oui|Oui
Audio Windows Media|Oui|Oui
WAV/PCM|Oui|Oui
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|Oui|N°
[Signature](https://en.wikipedia.org/wiki/Opus_(audio_format)) |Oui|N°
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|Oui|N°


###<a name="output-containerfile-formats"></a>Formats de fichier/conteneur de sortie

Formats de fichier/conteneur de sortie|Standard de décodage d’éléments multimédias|Flux de travail Media Encoder Premium
---|---|---
Adobe® Flash® F4V|N°|Oui
MXF (OP1a, XDCAM et AS02)|N°|Oui
DPP (y compris AS11)|N°|Oui
GXF|N°|Oui
MPEG-4/MP4|Oui|Oui
MPEG-TS|Oui|Oui
Windows Media/ASF|N°|Oui
AVI (non compressé 8 bits/10 bits)|N°|Oui
Format de fichier Smooth Streaming (PIFF 1.3)|N°|Oui

###<a name="output-video-codecs"></a>Codecs vidéo de sortie

Codecs vidéo de sortie|Standard de décodage d’éléments multimédias|Flux de travail Media Encoder Premium
---|---|---
AVC (H.264 ; 8 bits ; jusqu'à profil haute, niveau 5,2 ; 4 K très HD ; AVC interne)|Seuls 8 bits 4:2:0|Oui
Avid DNxHD (dans MXF)|N°|Oui
MPEG-2 (jusqu'à 422 profil et de haut niveau, notamment le variantes tels que XDCAM, XDCAM HD, XDCAM IMX, CableLabs® et D10)|N°|Oui
MPEG-1|N°|Oui
Windows Media Video/VC-1|N°|Oui
Création d’une miniature JPEG|N°|Oui

###<a name="output-audio-codecs"></a>Codecs Audio de sortie

Codecs Audio de sortie|Standard de décodage d’éléments multimédias|Flux de travail Media Encoder Premium
---|---|---
AES (SMPTE 331 M et 302 M, AES3-2003)|N°|Oui
Dolby® Digital (AC3)|N°|Oui
Dolby® Digital Plus (E-AC3) jusqu'à 7.1|N°|Oui
AAC (AAC-LC, HE-AAC et AAC-HEv2 ; jusqu'à 5.1)|Oui|Oui
MPEG Layer 2|N°|Oui
MP3 (MPEG-1 Audio Layer 3)|N°|Oui
Audio Windows Media|N°|Oui


##<a name="error-codes"></a>Codes d’erreur  

Le tableau suivant répertorie les codes d’erreur qui peuvent être retournés au cas où une erreur s’est produite lors de l’exécution de tâche codage.  Pour obtenir des détails de l’erreur dans votre code .NET, utilisez la classe [ErrorDetails](http://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx) . Pour obtenir des détails de l’erreur dans votre code reste, utilisez l’API REST [ErrorDetail](https://msdn.microsoft.com/library/jj853026.aspx) .

ErrorDetail.Code|Causes possibles des erreurs
-----|-----------------------
Inconnu| Erreur inconnue lors de l’exécution de la tâche
ErrorDownloadingInputAssetMalformedContent|Catégorie d’erreurs qui recouvre les erreurs dans le téléchargement d’entrée actif tels que les noms de fichier incorrect, zéro fichiers de longueur incorrectes met en forme et ainsi de suite.
ErrorDownloadingInputAssetServiceFailure|Catégorie d’erreurs qui décrit les problèmes sur le côté service - des erreurs de réseau ou de stockage exemple lors du téléchargement.
ErrorParsingConfiguration|Catégorie d’erreurs de la tâche où <see cref="MediaTask.PrivateData"/> (configuration) n’est pas valide, par exemple la configuration n’est pas un système valide prédéfinis ou qu’il contient XML non valide.
ErrorExecutingTaskMalformedContent|Catégorie d’erreurs lors de l’exécution de la tâche où problèmes dans les fichiers multimédias d’entrée entraîner l’échec.
ErrorExecutingTaskUnsupportedFormat|Catégorie d’erreurs où le processeur média ne peut plus traiter les fichiers fournis - format de média non pris en charge ou ne correspond pas à la Configuration. Par exemple, essayez de générer une sortie audio uniquement à partir d’un bien qui comporte uniquement les vidéos
ErrorProcessingTask|Catégorie d’autres erreurs le processeur média rencontre pendant le traitement de la tâche qui ne sont pas associés au contenu.
ErrorUploadingOutputAsset|Catégorie des messages d’erreur lors du chargement de la ressource de sortie
ErrorCancelingTask|Catégorie d’erreurs expliquer échecs lorsque vous tentez d’annuler la tâche
TransientError|Catégorie d’erreurs expliquer problèmes temporaires (par exemple. problèmes réseau temporaires avec le stockage Azure)


Pour obtenir de l’aide de l’équipe **Media Services** , ouvrez une [prise en charge des tickets](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).



##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="related-articles"></a>Articles connexes

- [Effectuer des tâches avancées de codage en personnalisant Présélections Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
- [Les quotas et les Limitations](media-services-quotas-and-limitations.md)

 
<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
