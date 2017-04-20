<properties 
    pageTitle="À l’aide des partenaires d’effectuer des licences Widevine devant Azure Media Services | Microsoft Azure" 
    description="Cet article décrit comment vous pouvez utiliser Azure Media Services (AMS) pour faire un flux de données qui est chiffré dynamiquement par AMS avec PlayReady et Widevine DRMs. La licence PlayReady provient de serveur de licences PlayReady de Services de support et Widevine licence est fourni par le serveur de licences castLabs." 
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
    ms.date="09/26/2016"  
    ms.author="juliako"/>

#<a name="using-partners-to-deliver-widevine-licenses-to-azure-media-services"></a>À l’aide des partenaires d’effectuer des licences Widevine devant Azure Media Services

##<a name="overview"></a>Vue d’ensemble

Microsoft Azure Media Services vous permet d’effectuer MPEG-tiret protégé à l’aide Widevine DRM, qui est chiffré par la spécification de chiffrement courantes (CENC).

En commençant par Media Services .NET SDK version 3.5.2, Media Services vous permet de configurer le modèle de licence Widevine et d’obtenir des licences Widevine. Vous pouvez également utiliser les partenaires AMS suivants pour vous aider à effectuer des licences Widevine : [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/).

##<a name="castlabs"></a>castLabs

Vous pouvez utiliser [castLabs](http://castlabs.com/company/partners/azure/) pour faire Widevine des licences. Pour plus d’informations, voir [castLabs à l’aide d’effectuer des licences DRM Azure Media Services](media-services-castlabs-integration.md)

##<a name="axinom"></a>Axinom

Vous pouvez utiliser [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/) pour faire Widevine des licences. Pour plus d’informations, voir [Axinom à l’aide de délivrer des licences DRM Azure Media Services](media-services-axinom-integration.md)


##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Voir aussi

[L’utilisation du chiffrement courantes dynamique PlayReady et/ou Widevine](media-services-protect-with-drm.md)

[Blog de Mingfei](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)

