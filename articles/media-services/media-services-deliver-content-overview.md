<properties
    pageTitle="Proposer du contenu aux clients | Microsoft Azure"
    description="Cette rubrique fournit une vue d’ensemble des étapes de distribution de votre contenu avec Azure Media Services."
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
    ms.date="09/19/2016"
    ms.author="juliako"/>


# <a name="deliver-content-to-customers"></a>Envoyer du contenu aux clients
Lorsque vous donnez votre contenu de diffusion en continu ou vidéos à la demande aux clients, votre objectif est de diffuser des vidéos de haute qualité à divers périphériques dans des conditions réseau différente.

Pour ce faire, vous pouvez :

- Coder votre flux de données dans un flux vidéo multi-débit (débit adaptive). Cela se charge des conditions de qualité et réseau.
- Microsoft Azure Media Services [emballage dynamique](media-services-dynamic-packaging-overview.md) permet de dynamiquement package nouveau votre flux de données dans les protocoles différents. Cela se charge des flux de données sur différents appareils. Media Services prend en charge la remise de la vitesse de transmission adaptive suivant diffusion en continu de technologies d’assistance : diffusion en continu Live HTTP (TLS), Smooth Streaming, MPEG-tiret et HDS (pour les détenteurs d’Adobe emploi/Access uniquement).

Cet article donne une vue d’ensemble des concepts importants de distribution de contenu.

Pour vérifier les problèmes connus, voir [problèmes connus](media-services-deliver-content-overview.md#known-issues).

## <a name="dynamic-packaging"></a>Emballage dynamique

Avec l’emballage dynamique qui fournit des Services de support, vous pouvez livrer votre contenu adaptive débit MP4 ou Smooth Streaming codé en continu les formats pris en charge par Media Services (MPEG-tiret, TLS, Smooth Streaming, HDS) sans avoir à nouveau empaqueter dans ces formats de diffusion en continu. Nous vous recommandons de diffusion de votre contenu avec emballage dynamique.

Pour tirer parti d’emballage dynamique, vous devez effectuer les opérations suivantes :

- Encoder votre fichier mezzanine (source) dans un jeu de fichiers MP4 adaptive débit ou débit adaptive Smooth Streaming fichiers.
- Obtenir au moins une unité de diffusion en continu à la demande pour le point de terminaison de diffusion en continu à partir de laquelle vous souhaitez présenter votre contenu. Pour plus d’informations, voir [comment mettre à l’échelle de diffusion en continu unités réservées à la demande](media-services-portal-manage-streaming-endpoints.md).

Avec emballage dynamique, vous stockez et payez pour les fichiers au format de stockage unique. Media Services sera créer et répondre à la réponse appropriée en fonction de vos demandes.

En plus de fournir un accès aux fonctionnalités d’emballage dynamique, streaming unités réservées à la demande vous fournit la capacité de sortie dédié qui peut être achetée par incréments de 200 Mbps. Par défaut, la diffusion en continu à la demande est configurée dans un modèle d’instance partagé pour le serveur (par exemple, la capacité cluster ou sortie) les ressources sont partagées avec tous les autres utilisateurs. Vous pouvez améliorer un débit de diffusion en continu à la demande en unités réservées diffusion en continu à la demande d’achat.

Pour plus d’informations, voir [emballage dynamique](media-services-dynamic-packaging-overview.md).

## <a name="filters-and-dynamic-manifests"></a>Manifestes dynamiques et des filtres

Vous pouvez définir des filtres pour vos ressources avec Media Services. Ces filtres sont des règles côté serveur qui aideront vos clients à faire lire une section spécifique d’une vidéo ou spécifier un sous-ensemble de formats audio et vidéo qui périphérique de votre client peut gérer (au lieu de tous les formats qui sont associés à l’actif). Ce filtrage par le biais *dynamiques manifestes* créés quand votre client souhaite pour diffuser une vidéo basée sur un ou plusieurs filtres spécifiés.

Pour plus d’informations, voir [manifestes dynamiques et filtres](media-services-dynamic-manifest-overview.md).

## <a name="locators"></a>Repères

Pour fournir aux utilisateurs une URL qui peut être utilisée pour diffuser en continu ou télécharger votre contenu, vous devez tout d’abord publier vos ressources en créant un repère. Un repère fournit un point d’entrée pour accéder aux fichiers contenus dans un bien. Media Services prend en charge les deux types de repères :

- Repères de OnDemandOrigin. Celles-ci sont utilisées pour diffuser des médias (par exemple, MPEG-tiret, TLS ou Smooth Streaming) ou progressivement téléchargement des fichiers.
-  Repères de URL accès partagé signature (sa). Ils sont utilisés pour télécharger des fichiers multimédias sur votre ordinateur local.

Une *stratégie d’accès* est utilisée pour définir les autorisations (par exemple, lecture, écriture et liste) et la durée pour laquelle un client a accès pour un élément particulier. Notez que l’autorisation de liste (AccessPermissions.List) ne doit pas être utilisée lors de la création d’un repère OrDemandOrigin.

Repères ont des dates d’expiration. Le portail Azure définit une date d’expiration 100 ans à l’avenir pour les repères.

>[AZURE.NOTE] Si vous avez utilisé le portail Azure pour créer des repères avant mars 2015, les repères ont été définies pour qu’ils expirent après deux ans.

Pour mettre à jour une date d’expiration sur un repère, utilisez [reste](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator ) ou API [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) . Notez que lorsque vous mettez à jour la date d’expiration d’un repère associations de sécurité, l’URL change.

Repères ne sont pas conçus pour gérer le contrôle d’accès par utilisateur. Vous pouvez donner différents droits d’accès à des utilisateurs individuels à l’aide de solutions de gestion des droits numériques (DRM). Pour plus d’informations, voir [Sécurisation multimédia](http://msdn.microsoft.com/library/azure/dn282272.aspx).

Lorsque vous créez un repère, il peut y avoir un délai de 30 secondes en raison de stockage requis et des processus propagation dans le stockage Azure.


## <a name="adaptive-streaming"></a>Deux diffusion en continu

Technologies de débit Adaptive permettent aux applications du lecteur vidéo déterminer les conditions de réseau et sélectionnez à partir de plusieurs vitesses de transmission. Lors de la communication réseau diminue, le client peut sélectionner un débit inférieur pour lecture puisse continuer avec une qualité vidéo inférieure. Comme l’amélioration des conditions réseau, le client pouvez basculer vers un débit plus élevé avec amélioration de la qualité vidéo. Azure Media Services prend en charge les technologies de débit adaptive suivantes : diffusion en continu Live HTTP (TLS), Smooth Streaming, MPEG-tiret et HDS.

Pour permettre aux utilisateurs avec l’URL de diffusion en continu, vous devez d’abord créer une locator OnDemandOrigin. Création le repère vous donne le chemin d’accès de base de la ressource qui contient le contenu que vous voulez diffuser. Toutefois, pour pouvoir diffuser ce contenu, vous devez modifier ce chemin d’accès supplémentaires. Pour créer une URL complète vers le fichier manifeste de diffusion en continu, vous devez concaténer valeur de chemin d’accès de l’URL et le manifeste (filename.ism) nom de fichier. Être **ajoutés/manifeste** et un format approprié (si nécessaire) le chemin d’accès locator.

>[AZURE.NOTE]Vous pouvez aussi diffuser votre contenu via une connexion SSL. Pour ce faire, assurez-vous que votre URL de diffusion en continu commencent par HTTPS.

Vous pouvez diffuser uniquement via le protocole SSL si le point de terminaison de diffusion en continu à partir de laquelle vous présenter votre contenu a été créé après 10 septembre 2014. Si votre URL de diffusion en continu est basées sur les points de terminaison de diffusion en continu créées après 10 septembre 2014, l’URL contient « streaming.mediaservices.windows.net ». URL de diffusion en continu qui contiennent « origin.mediaservices.windows.net » (l’ancien format) ne prennent pas en charge SSL. Si votre URL est dans l’ancien format et que vous voulez être en mesure de diffuser en continu sur SSL, créez un point de terminaison diffusion en continu. URL basés sur le nouveau point de terminaison de diffusion en continu permet de diffuser votre contenu via le protocole SSL.


## <a name="streaming-url-formats"></a>Formats d’URL en continu

### <a name="mpeg-dash-format"></a>Format MPEG-tiret

{diffusion en continu du point de terminaison nom media services compte name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(format=MPD-Time-CSF)



### <a name="apple-http-live-streaming-hls-v4-format"></a>Format V4 Apple HTTP Live Streaming (TLS)

{diffusion en continu du point de terminaison nom media services compte name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(format=m3u8-AAPL)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Format V3 Apple HTTP Live Streaming (TLS)

{diffusion en continu du point de terminaison nom media services compte name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(format=m3u8-AAPL-v3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>Format Apple HTTP Live Streaming (TLS) avec filtre audio uniquement

Par défaut, pistes audio uniquement sont inclus dans le TLS manifeste. Il s’agit pour la certification Store d’Apple pour réseaux cellulaires. Dans ce cas, si un client n’a pas suffisamment de bande passante ou est connecté via une connexion de 2G, lecture bascule vers audio uniquement. Cela permet d’éviter que la diffusion en continu du contenu sans mise en mémoire tampon, mais rien ne s’affiche. Dans certains scénarios, lecteur mise en mémoire tampon peut avoir priorité sur audio uniquement. Si vous voulez supprimer la piste audio uniquement, ajoutez **audio uniquement = false** à l’URL.

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(format=m3u8-AAPL-v3,audio-Only=false)

Pour plus d’informations, voir [prise en charge de la Composition dynamique manifeste et TLS sortie des fonctionnalités supplémentaires](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).


### <a name="smooth-streaming-format"></a>Format de diffusion en continu lisse

{diffusion en continu du point de terminaison nom media services compte name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Exemple :

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest

### <a id="fmp4_v20"></a>Manifeste 2.0 de diffusion en continu lisse (manifeste hérité)

Par défaut, Smooth Streaming manifeste format contient la balise répétition (r-balise). Toutefois, certains lecteurs ne prennent pas en charge la balise r. Les clients avec ces lecteurs peuvent utiliser un format qui désactive la balise r :

{diffusion en continu du point de terminaison nom media services compte name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=fmp4-v20)

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

### <a name="hds-for-adobe-primetimeaccess-licensees-only"></a>HDS (pour les détenteurs d’Adobe emploi/Access uniquement)

{diffusion en continu du point de terminaison nom media services compte name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f)

## <a name="progressive-download"></a>Téléchargement progressif

Avec téléchargement progressif, vous pouvez démarrer la lecture des éléments multimédias avant le téléchargement de l’intégralité du fichier. Vous ne pouvez pas progressivement télécharger des fichiers (ismv, isma, ismt ou ismc) .ism *.

Pour télécharger progressivement du contenu, utiliser le type de OnDemandOrigin de locator. L’exemple suivant illustre l’URL qui est basé sur le type de OnDemandOrigin de locator :

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

Vous devez déchiffrer les biens chiffrées de stockage que vous voulez diffuser à partir du service d’origine du téléchargement progressif.

## <a name="download"></a>Télécharger

Pour télécharger votre contenu sur un appareil client, vous devez créer un repère associations de sécurité. L’URL de sa permet d’accéder au conteneur de stockage Azure où se trouve votre fichier. Pour créer l’URL de téléchargement, vous devez incorporer le nom du fichier entre l’hôte et signature associations de sécurité.

L’exemple suivant illustre l’URL qui est basé sur le repère associations de sécurité :

    https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

Les considérations suivantes s’appliquent :

- Vous devez déchiffrer les biens chiffrées de stockage que vous voulez diffuser à partir du service d’origine du téléchargement progressif.
- Téléchargement d’une qui n’a pas terminé dans les 12 heures échouera.

## <a name="streaming-endpoints"></a>Points de terminaison de diffusion en continu

Un point de terminaison de diffusion en continu représente un service de diffusion en continu qui peut fournir un contenu directement dans une application de lecteur client ou à un réseau de distribution de contenu (CDN) pour la distribution davantage. Le flux sortant à partir d’un service de point de terminaison en continu peut être un flux en direct ou une ressource de vidéos à la demande dans votre compte Media Services. Vous pouvez également contrôler la capacité du service de point de terminaison diffusion en continu traiter les besoins croissants de bande passante en ajustant streaming unités réservées. Vous devez allouer au moins une unité réservée pour les applications dans un environnement de production. Pour plus d’informations, voir [comment mettre à l’échelle d’un service de support](media-services-portal-manage-streaming-endpoints.md).

## <a name="known-issues"></a>Problèmes connus

### <a name="changes-to-smooth-streaming-manifest-version"></a>Modifications apportées aux Smooth Streaming manifeste version

Avant la version de service juillet 2016--lors de biens généré par Media Encoder Standard, flux de travail Media Encoder Premium ou l’Azure Media Encoder antérieures ont été transmis à l’aide d’emballage dynamique--Smooth Streaming manifeste renvoyée serait sont conformes à la version 2.0. Dans la version 2.0, les durées de fragment n’utilisent pas les balises dite répéter (« r »). Par exemple :

<?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" n="0" />
            <c d="2000" />
            <c d="2000" />
            <c d="2000" />
        </StreamIndex>
    </SmoothStreamingMedia>

Dans la version de service juillet 2016, manifeste Smooth Streaming généré est conforme à version 2.2, dont la durée est fragment à l’aide de balises répétition. Par exemple :

    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" r="4" />
        </StreamIndex>
    </SmoothStreamingMedia>

Certains des clients hérités Smooth Streaming ne peuvent pas en charge les balises de répétition et ne pourra pas charger le manifeste. Pour atténuer ce problème, vous pouvez utiliser le paramètre format manifeste hérité **(format = fmp4 v20)** ou mettre à jour votre client vers la dernière version, qui prend en charge les balises répétition. Pour plus d’informations, voir [2.0 de diffusion en continu lisse](media-services-deliver-content-overview.md#fmp4_v20).

## <a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Rubriques connexes

[Mettre à jour Locator Media Services après restauration des clés de stockage](media-services-roll-storage-access-keys.md)
