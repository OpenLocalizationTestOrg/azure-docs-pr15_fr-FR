<properties 
    pageTitle="Développer des applications du lecteur vidéo" 
    description="La rubrique fournit des liens vers les structures de lecteur et les plug-ins que vous pouvez utiliser pour développer votre propre des applications clientes qui peuvent utiliser le multimédia à partir de Media Services." 
    authors="Juliako" 
    manager="erikre" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="juliako"/>


#<a name="develop-video-player-applications"></a>Développer des applications du lecteur vidéo

##<a name="overview"></a>Vue d’ensemble

Azure Media Services fournit les outils nécessaires pour créer des applications de lecteur client riche dynamique pour la plupart des plates-formes, y compris : iOS appareils, les appareils Android, Windows, Windows Phone, Xbox et Terminal zones. Cette rubrique fournit également des liens vers les structures de lecteur que vous pouvez utiliser pour développer votre propre des applications clientes qui peuvent utiliser le multimédia à partir d’Azure Media Services et SDK.

##<a name="azure-media-player"></a>Lecteur multimédia Azure

[Azure Media Player](http://aka.ms/ampinfo) est un lecteur vidéo web intégré pour lire du contenu multimédia à partir de Microsoft Azure Media Services sur un large éventail de périphériques et de navigateurs. Azure Media Player utilise des normes, tels que HTML5 Media Source Extensions (MSE) et Extensions de médias chiffrées (EME) pour fournir une expérience de diffusion en continu adaptive enrichie. Lorsque ces normes ne sont pas disponibles sur un appareil ou dans un navigateur, Azure Media Player utilise Flash et Silverlight comme technologie de secours. Quelle que soit la technologie lecture utilisée, les développeurs ont une interface unifiée JavaScript pour accéder aux API. Cela permet de contenu pris en charge par Azure Media Services à travers une vaste gamme d’appareils et navigateurs sans effort supplémentaire.

Microsoft Azure Media Services permet d’être pris en charge avec tiret Smooth Streaming et TLS formats de diffusion en continu du contenu à lire le contenu. Azure Media Player prend en compte ces différents formats et lit automatiquement le lien meilleures basé sur les fonctionnalités de plateforme/navigateur. Microsoft Azure Media Services permet également de chiffrement dynamiques du bien avec chiffrement PlayReady ou AES 128 bits chiffrement enveloppe. Permet d’Azure Media Player pour le déchiffrement de PlayReady et AES 128 bits contenu chiffré lorsqu’il est configuré correctement. 

Pour plus d’informations :

- [Lecteur multimédia Azure](http://aka.ms/ampinfo)
- [Documentation présentée sous d’Azure Media Player](http://aka.ms/ampdocs) 
- [Lecteur multimédia Azure prise en main de Blog](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)
- [Inscrivez-vous pour rester à jour avec les dernières actualités à partir d’Azure Media Player](http://aka.ms/ampsignup)
- [Ajouter de nouvelles requêtes de fonctionnalités, des idées et des commentaires](http://aka.ms/ampuservoice ) 


##<a name="other-tools-for-creating-player-applications"></a>Autres outils de création d’Applications de lecteur

Vous pouvez également utiliser un des kits de développement logiciel suivants :

- [Lisse Kit de développement logiciel Client de diffusion en continu](http://www.iis.net/downloads/microsoft/smooth-streaming) 
- [Application du Windows Store diffusion en continu lisse](media-services-build-smooth-streaming-apps.md)
- [Microsoft Media plateforme : Lecteur Framework](http://playerframework.codeplex.com/) 
- [HTML5 Documentation de Framework du lecteur](http://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
- [Smooth Streaming plug-in pour OSMF est Microsoft](https://www.microsoft.com/download/details.aspx?id=36057) 
- [Gestion des licences Microsoft® lisse diffusion en continu Client porter Kit](http://aka.ms/sspk) 
- [Développement d’applications vidéo XBOX](http://xbox.create.msdn.com/) 
 

##<a name="advertising"></a>Publicité

Azure Media Services prend en charge pour l’insertion d’ad via la plate-forme Windows Media : structures de lecteur. Structures de lecteur avec prise en charge ad sont disponibles pour Windows 8, Silverlight, Windows Phone 8 et les appareils iOS. Chaque framework lecteur contient des exemples de code qui vous montre comment mettre en œuvre une application de lecteur. Il existe trois types différents d’annonces, que vous pouvez insérer dans vos supports :

Linéaire – annonces plein cadre qui suspendre la vidéo principale

Non linéaire – annonces superposition qui s’affichent comme la lecture de la vidéo principale, généralement un logo ou une autre image statique placé dans le lecteur

Guide – annonces qui s’affichent en dehors du lecteur

Annonces peuvent être placés à tout moment dans la chronologie de la vidéo principale. Vous devez indiquer le lecteur quand lire l’annonce et les annonces à lire. Pour cela, à l’aide d’un jeu de fichiers basé sur XML standards : vidéo Ad Service modèle (VAST), numérique vidéo plusieurs Ad sélection (VMAP), modèle de séquençage résumés multimédias (mat) et numérique vidéo du lecteur Ad Interface définition (VPAID). Fichiers vastes spécifient les annonces s’affichent. Fichiers VMAP spécifier à quel moment lire différentes annonces et contenir vaste XML. Fichiers mat sont un autre moyen d’annonces séquence peut comporter elle aussi vaste XML. Les fichiers VPAID définissent une interface entre le lecteur vidéo et l’annonce ou le serveur ad. Pour plus d’informations, voir [Insérer des annonces](https://msdn.microsoft.com/library/dn387398.aspx).

Pour plus d’informations sur le support de sous-titres et les annonces fermé dans Live diffusion en continu de vidéos, voir [prise en charge fermé sous-titres et normes de point d’insertion d’Ad](https://msdn.microsoft.com/library/c49e0b4d-357e-4cca-95e5-2288924d1ff3#caption_ad).


##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Voir aussi

[Incorporer une vidéo en continu Adaptive MPEG-tiret dans une Application HTML5 avec DASH.js](media-services-embed-mpeg-dash-in-html5.md)

[GitHub dash.js référentiel](https://github.com/Dash-Industry-Forum/dash.js)
 
