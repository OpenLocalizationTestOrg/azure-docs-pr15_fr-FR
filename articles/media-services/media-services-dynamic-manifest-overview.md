<properties 
    pageTitle="Filtres et manifestes dynamiques | Microsoft Azure" 
    description="Cette rubrique explique comment créer des filtres afin de votre client de les utiliser à des sections spécifiques d’un flux de flux de données. Media Services crée dynamiques manifestes pour archiver ce flux sélective." 
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
    ms.date="09/26/2016" 
    ms.author="cenkdin;juliako"/>

# <a name="filters-and-dynamic-manifests"></a>Manifestes dynamiques et des filtres

Commençant par la version 2.11, Media Services permet de définir des filtres pour vos ressources. Ces filtres sont des règles côté serveur qui permettent de choisir de le faire des éléments tels que vos clients : lecture uniquement une section d’une vidéo (au lieu de la vidéo entière), ou spécifier uniquement un sous-ensemble de formats audio et vidéo qui périphérique de votre client peut gérer (au lieu de tous les formats qui sont associés à l’actif). Ce filtrage de vos ressources est archivé via s **Manifeste dynamique**créés à la demande de votre client en continu d’une vidéo sur les filtres spécifiés.

Les rubriques décrit des scénarios courants dans lesquels à l’aide de filtres serait très utile de vos clients et les liens vers des rubriques qui montrent comment créer des filtres par programme (pour l’instant, vous pouvez créer filtres avec l’API REST uniquement).

##<a name="overview"></a>Vue d’ensemble

Lors de la remise de votre contenu aux clients (en continu les événements en direct ou vidéos à la demande) votre objectif consiste à fournir une vidéo de haute qualité à divers périphériques dans des conditions réseau différente. Pour obtenir ce objectif ce qui suit :

- encoder votre flux au flux vidéo multi-débit ([débit adaptive](http://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)) (cela se charge des conditions de qualité et réseau) et 
- Media Services [Emballage dynamique](media-services-dynamic-packaging-overview.md) permet de dynamiquement package nouveau votre flux dans différents protocoles (cela se charge des flux de données sur différents appareils). Media Services prend en charge la remise de la vitesse de transmission adaptive suivant diffusion en continu de technologies d’assistance : diffusion en continu Live HTTP (TLS), Smooth Streaming, MPEG tiret et HDS (pour les détenteurs d’Adobe emploi/Access uniquement). 

###<a name="manifest-files"></a>Fichiers manifeste 

Lorsque vous encodez un bien pour adaptive débit en continu, un fichier **manifeste** (sélection) est créé (le fichier est basée sur XML ou texte). Le fichier de **manifeste** inclut la diffusion en continu telles que les métadonnées : effectuer le suivi de type (audio, vidéo ou texte), effectuer le suivi de nom, l’heure de début et de fin, débit (caractéristiques) et le suivi des langues, présentation fenêtre (décalée de durée fixe), codec vidéo (FourCC). Il indique également le lecteur pour récupérer le fragment suivant en fournissant des informations sur les fragments vidéo exécutable suivantes disponibles et leur emplacement. Fragments (ou segments) sont les « segments » réels d’un contenu vidéo.


Voici un exemple d’un fichier manifeste : 

    
    <?xml version="1.0" encoding="UTF-8"?>  
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="330187755" TimeScale="10000000">
    
    <StreamIndex Chunks="17" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="8">
    <QualityLevel Index="0" Bitrate="5860941" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931300016E360000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="1" Bitrate="4602724" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931100011EDC00002CD29FF8C7076850A45800000000168E9093525" />
    <QualityLevel Index="2" Bitrate="3319311" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="000000016764001FAC2CA5014016EC054808080A00000300020000030064C0800067C28000103667F8C7076850A4580000000168E9093525" />
    <QualityLevel Index="3" Bitrate="2195119" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C1000044AA0000ABA9FE31C1DA14291600000000168E9093525" />
    <QualityLevel Index="4" Bitrate="1469881" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C04000B71A0000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="5" Bitrate="978815" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C08001E8480004C4B7F8C7076850A45800000000168E9093525" />
    <QualityLevel Index="6" Bitrate="638374" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C080013D60000C65DFE31C1DA1429160000000168E9093525" />
    <QualityLevel Index="7" Bitrate="388851" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DAC2CA505067E7C054830303200000300020000030064C040030D40003D093F8C7076850A45800000000168E9093525" />
    
    <c t="0" d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="9600000"/>
    </StreamIndex>
    
    
    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_128kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_128kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="125658" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />
    
    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>
    
    
    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_56kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_56kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="53655" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />
    
    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>
    
    </SmoothStreamingMedia>
    
###<a name="dynamic-manifests"></a>Manifestes dynamiques

Il existe des [scénarios](media-services-dynamic-manifest-overview.md#scenarios) lorsque votre client a besoin de davantage de flexibilité que ce qui est décrit dans le fichier manifeste de l’actif par défaut. Par exemple :

- Appareil spécifique : effectuer uniquement les formats spécifié et/ou spécifiées pistes de langue sont pris en charge par le périphérique qui est utilisé pour lire le contenu (« filtrage d’un rendu »). 
- Réduisez le manifeste pour afficher une image clipart sous-adresse d’un événement en direct (« clip sous-adresse filtrage »).
- Découper le début d’une vidéo (« réduction une vidéo »).
- Ajuster la fenêtre de présentation (DVR) pour fournir une longueur limitée de la fenêtre DVR dans le lecteur (« fenêtre Présentation ajustement »).
 
Pour atteindre cette flexibilité, Media Services offre **Manifestes dynamique** basé sur des [filtres](media-services-dynamic-manifest-overview.md#filters)de prédéfinis.  Une fois que vous définissez les filtres, vos clients peuvent les utiliser pour diffuser un format associé spécifique ou clips sous-adresse de votre vidéo. Ils spécifiez ou les filtres dans l’URL de diffusion en continu. Filtres peuvent être appliqués au débit adaptive streaming protocoles pris en charge par [Emballage dynamique](media-services-dynamic-packaging-overview.md): TLS, MPEG-tiret, Smooth Streaming et HDS. Par exemple :

URL de tiret MPEG avec filtre

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)

URL de diffusion en continu lisse avec filtre

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)


Pour plus d’informations sur la façon de présenter votre contenu et créer les URL de diffusion en continu, voir [vue d’ensemble du contenu de remise](media-services-deliver-content-overview.md).


>[AZURE.NOTE]Notez que manifestes dynamique ne changent pas l’actif et le manifeste par défaut pour cette ressource. La possibilité de votre client demander un flux de données avec ou sans filtres. 


###<a id="filters"></a>Filtres 

Il existe deux types de filtres de biens : 

- Filtres globaux (peuvent être appliqués à n’importe quel élément dans le compte Azure Media Services, ont une durée de vie du compte) et 
- Filtres locaux (peut uniquement être appliqué à une ressource à laquelle le filtre a été associé lors de sa création, ont une durée de vie du bien). 

Types de filtres globaux et locaux ont exactement les mêmes propriétés. La différence entre les deux principale est à quels scénarios quel type d’un serveur de fichiers est plus approprié. Filtres globaux conviennent généralement les profils de périphérique (un rendu filtrage) où les filtres locaux peuvent être utilisés pour découper une ressource spécifique.


##<a id="scenarios"></a>Scénarios courants 

Comme mentionné précédemment, lors de la remise de votre contenu aux clients (en continu les événements en direct ou vidéos à la demande) votre objectif est de fournir une qualité vidéo à divers périphériques dans des conditions réseau différente. En outre, votre peut avoir d’autres impératifs qui impliquent de filtrage de vos biens et à l’aide de s **Manifeste dynamique**. Les sections suivantes présentent une brève présentation des différents scénarios de filtrage.

- Indiquer uniquement un sous-ensemble des formats audio et vidéo capable de certains appareils (au lieu de tous les formats qui sont associés à l’actif). 
- Lire une partie d’une vidéo (au lieu de lire la vidéo entière) uniquement.
- Ajuster la fenêtre Présentation DVR.

##<a name="rendition-filtering"></a>Filtrage d’un rendu 

Vous pouvez choisir de codage de votre élément à plusieurs profils de codage (référence H.264, haute H.264, AACL, AACH, Dolby Digital Plus) et plusieurs vitesses de transmission qualité. Cependant, tous les périphériques client prend en charge les profils et vitesses de transmission de tous vos biens. Par exemple, les appareils Android anciennes prend uniquement en charge H.264 référence + AACL. Envoyer des vitesses de transmission supérieure à un périphérique qui ne peuvent pas accéder les avantages, fait perdre du calcul de la bande passante et appareil. Tel dispositif doit coder toutes les informations données, uniquement à l’échelle vers le bas pour l’affichage.

Avec manifeste dynamique, vous pouvez créer des profils de périphériques par exemple mobile, de la console, HD/SD, etc. et inclure les pistes et les caractères qui vous voulez faire partie de chaque profil.

 
![Exemple de filtrage associé][renditions2]

Dans l’exemple suivant, un encodeur a été utilisé pour le codage un élément mezzanine dans sept rendus vidéo ISO MP4s (à partir de 180p à 1080p). Les biens codé peuvent être dynamiquement empaquetés dans n’importe lequel des protocoles de diffusion en continu suivants : TLS, lisses, MPEG tiret et HDS.  Dans la partie supérieure du diagramme, le manifeste TLS pour l’actif et aucun filtre n’est affiché (qu’il contient tous les sept formats).  En bas à gauche, le manifeste TLS auquel a été appliqué un filtre nommé « ott » s’affiche. Le filtre « ott » spécifie pour supprimer toutes les vitesses de transmission ci-dessous 1 Mbps, ce qui a donné lieu aux niveaux de qualité deux bas est supprimés dans la réponse.  En bas à droite, le manifeste TLS auquel a été appliqué un filtre nommé « mobile » s’affiche. Spécifie le filtre « mobile » pour supprimer les rendus d’où la résolution est supérieure à 720p a donné lieu à deux formats 1080p est supprimés.

![Filtrage d’un rendu][renditions1]

##<a name="removing-language-tracks"></a>Suppression de pistes de langue

Vos biens peuvent inclure plusieurs langues audio comme l’anglais, espagnol, Français, etc.. En règle générale, les responsables du Kit de développement logiciel du lecteur par défaut sélection d’une piste audio et audio disponible effectue le suivi par sélection de l’utilisateur. Il est difficile de développer ces SDK du lecteur, il nécessite différentes mises en œuvre entre les spécifiques au périphérique lecteur-environnements. En outre, sur certaines plateformes, API Player sont limitées et n’incluez pas la fonctionnalité de sélection audio où les utilisateurs ne peuvent pas sélectionner ou modifier la piste audio par défaut. Avec les filtres de biens, vous pouvez contrôler le comportement en créant des filtres qui incluent uniquement les langues audio souhaitées.

![Filtrage de pistes de langue][language_filter]


##<a name="trimming-start-of-an-asset"></a>Réduction de début d’un bien 

Dans la plupart des événements de diffusion en continu live, les opérateurs exécuter des tests avant l’événement réel. Par exemple, ils peuvent inclure une tablette tactile comme suit avant le début de l’événement : « Programme commence momentanément ». Si le programme est l’archivage, le test et les données ardoise sont également archivées et seront incluses dans la présentation. Toutefois, ces informations ne doivent pas s’afficher pour les clients. Avec manifeste dynamique, vous pouvez créer un filtre d’heure de début et supprimer les données inutiles à partir du manifeste.

![Réduction de début][trim_filter]

##<a name="creating-sub-clips-views-from-a-live-archive"></a>Création d’éléments sous-adresse (vues) à partir d’une archive en ligne

Grand nombre d’événements live est longue et une archive en ligne peut-être inclure plusieurs événements. Après l’événement live chaînes de télévision extrémités souhaiterez décomposer l’archive en ligne dans le programme logique de démarrage et d’arrêt de séquences. Ensuite, publiez ces programmes virtuels séparément sans post traitement l’archive en ligne et créez ne pas actifs distincts (qui n’obtiendrez pas les avantages des fragments mis en cache existants dans les CDN). Exemples de ces programmes virtuelles (clips sous-adresse) sont les trimestres d’un football ou de basket, les tournées dans joueur, ou les événements individuels d’une après-midi du programme jeux Olympiques.

Avec manifeste dynamique, vous pouvez créer des filtres à l’aide des heures de début/fin et créer des affichages virtuels au-dessus d’une archive en ligne. 

![Filtre sous-élément][subclip_filter]

Bien filtrée :

![Ski][skiing]

##<a name="adjusting-presentation-window-dvr"></a>Ajustement de la fenêtre de présentation (DVR)

Azure Media Services propose actuellement, archive circulaire où la durée peut être configurée entre 5 minutes - 25 heures. Filtrage manifeste pouvant être utilisé pour créer une fenêtre DVR propagée sur la partie supérieure de l’archive, sans supprimer les éléments multimédias. Il existe de nombreux scénarios où chaînes de télévision à fournir une fenêtre DVR limitée qui déplace avec le bord live et en même temps conserver une plus grande fenêtre d’archivage. Une chaîne de télévision souhaiterez peut-être utiliser les données qui n’appartient pas la fenêtre DVR pour mettre en évidence des clips, ou he\she souhaitiez fournissent différentes fenêtres DVR pour différents appareils. Par exemple, la plupart des périphériques mobiles ne gérer windows DVR volumineux (vous pouvez avoir une fenêtre DVR 2 minutes pour les appareils mobiles et 1 heure pour les clients de bureau).

![Fenêtre DVR][dvr_filter]

##<a name="adjusting-livebackoff-live-position"></a>Ajustement LiveBackoff (live position)

Filtrage manifeste peut être utilisé pour supprimer plusieurs secondes à partir du bord live d’un programme live. Cela permet de chaînes de télévision lire la présentation sur le point de composition preview et créer des points d’insertion de publication avant des visionneuses recevoir le flux de données (généralement sauvegardée-désactivé à 30 secondes). Chaînes de télévision pouvant puis appuyez sur ces dernières à leurs structures client dans le temps pour qu’ils reçu et traiter les informations avant que l’opportunité de publication.

Outre la prise en charge de la publication, LiveBackoff peuvent servir permettant d’ajuster la position de téléchargement live client afin que lorsque des clients DERIVE et appuyez sur le bord live qu’ils peuvent toujours accéder fragments du serveur plutôt que vous rencontrez des erreurs HTTP 404 ou 412.

![livebackoff_filter][livebackoff_filter]


##<a name="combining-multiple-rules-in-a-single-filter"></a>Combiner plusieurs règles dans un seul filtre

Vous pouvez combiner plusieurs règles de filtrage dans un seul filtre. Par exemple, vous pouvez définir une règle de plage pour supprimer la tablette tactile d’une archive en ligne et également filtrer vitesses de transmission disponibles. Pour plusieurs règles de filtrage le résultat final est la composition (intersection uniquement) de ces règles.

![plusieurs règles][multiple-rules]

##<a name="create-filters-programmatically"></a>Créer des filtres par programme

La rubrique suivante explique entités Media Services qui sont liées aux filtres. La rubrique indique également comment créer des filtres par programme.  

[Créer des filtres avec API REST](media-services-rest-dynamic-manifest.md).

## <a name="combining-multiple-filters-filter-composition"></a>Pour combiner plusieurs filtres (filtre composition)

Vous pouvez également combiner plusieurs filtres dans une seule URL. 

Le scénario suivant illustre pourquoi vous souhaiterez peut-être combiner des filtres :

1. Vous devez filtrer votre qualité vidéo pour les appareils mobiles tels qu’Android ou iPAD (afin de limiter la qualité vidéo). Pour supprimer les caractéristiques indésirables, vous devez créer un filtre général qui convient pour les profils de l’appareil. Comme indiqué ci-dessus, filtres globaux peuvent servir à tous vos sous le même compte de services de support sans aucune autre association. 
2. Vous voulez également découper l’heure de début et de fin d’un bien. Pour ce faire, vous créez un filtre local et définir l’heure de début/fin. 
3. Vous voulez combiner les deux de ces filtres (sans combinaison que nécessaires pour ajouter le filtrage de la qualité du filtre de filtrage qui rend l’utilisation de filtre difficile).

Pour combiner des filtres, vous devez définir les noms de filtre à la sélection/manifeste d’URL avec délimitée par des points-virgules. Supposons que vous ayez un filtre nommé *MyMobileDevice* que les caractéristiques de filtres et que vous avez un autre nommé *MyStartTime* pour définir une heure précise. Vous pouvez combiner les comme suit :

    http://teststreaming.streaming.mediaservices.windows.net/3d56a4d-b71d-489b-854f-1d67c0596966/64ff1f89-b430-43f8-87dd-56c87b7bd9e2.ism/Manifest(filter=MyMobileDevice;MyStartTime)

Vous pouvez combiner jusqu'à 3 filtres. 

Pour plus d’informations, voir [ce](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) billet de blog.


##<a name="know-issues-and-limitations"></a>Problèmes connus et limitations

- Manifeste dynamique fonctionne dans le groupe d’images frontières (trames de clé), par conséquent, réduction a précision de groupe d’images. 
- Vous pouvez utiliser le même nom du filtre pour les filtres locaux et globaux. Remarque : ce filtre local ont une priorité plus élevée et remplace les filtres globaux.
- Si vous mettez à jour un filtre, il peut prendre jusqu'à 2 minutes point de terminaison de diffusion en continu actualiser les règles. Si le contenu a été pris en charge des filtres (et mis en cache dans les proxys et CDN cache), ces filtres de mise à jour peut entraîner des erreurs de lecteur. Il est recommandé pour vider le cache après mise à jour le filtre. Si cette option n’est pas possible, envisagez d’utiliser un filtre différent.


##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



##<a name="see-also"></a>Voir aussi

[Fournir du contenu à la vue d’ensemble des clients](media-services-deliver-content-overview.md)

[renditions1]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/media-services-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/media-services-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/media-services-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/media-services-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/media-services-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/media-services-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/media-services-dynamic-manifest-overview/media-services-skiing.png
 