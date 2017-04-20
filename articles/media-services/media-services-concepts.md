<properties 
    pageTitle="Concepts de Azure Media Services | Microsoft Azure" 
    description="Cette rubrique vous donne une vue d’ensemble des Concepts de Azure Media Services" 
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

#<a name="azure-media-services-concepts"></a>Concepts de Azure Media Services 

Cette rubrique vous donne un aperçu des principaux concepts Media Services.

##<a id="assets"></a>Stockage et biens

###<a name="assets"></a>Actifs

Un [bien](https://msdn.microsoft.com/library/azure/hh974277.aspx) contient des fichiers numériques (y compris la vidéo, audio, images, des collections de miniatures, légendes et sous-titres fichiers) et les métadonnées relatives à ces fichiers. Une fois les fichiers numériques sont téléchargés dans un bien, ils peuvent être utilisés dans les Services de support codage et diffusion en continu de flux de travail.

Un bien est mappé dans un conteneur blob dans le compte de stockage Azure et les fichiers dans l’actif sont stockées sous forme d’objets BLOB dans ce conteneur.

Lorsque vous décidez quel contenu multimédia pour télécharger et stocker dans un bien, les considérations suivantes s’appliquent :

- Un bien doit contenir uniquement une seule et unique instance du contenu multimédia. Par exemple, une seule modification de TV épisode, vidéo ou de publication.
- Un actif ne doit pas contenir plusieurs formats ou des modifications d’un fichier audiovisuel. Un exemple d’une utilisation incorrecte d’un bien essaient de stocker plusieurs épisode TV, publication ou plusieurs angles de la caméra à partir d’un seul de production à l’intérieur d’un bien. Stockage de plusieurs formats ou des modifications d’un fichier audiovisuel dans un bien peut entraîner des difficultés soumettre des tâches de codage, la diffusion et la sécurisation de la remise du bien plus loin dans le flux de travail.  

###<a name="asset-file"></a>Fichier de biens 
Un [AssetFile](https://msdn.microsoft.com/library/azure/hh974275.aspx) représente un fichier vidéo ou audio qui est stocké dans un conteneur blob. Un fichier de biens est toujours associé à un bien, et un bien peut contenir un ou plusieurs fichiers. La tâche Media Services Encoder échoue si un objet de fichier actif n’est pas associé à un fichier dans un conteneur blob numérique.

L’instance **AssetFile** et le fichier multimédia réel sont deux objets distincts. L’instance AssetFile contient des métadonnées sur le fichier multimédia, tandis que le fichier multimédia contient le contenu du média réel.

Vous ne devez pas essayer modifier le contenu des conteneurs blob qui ont été générés par Media Services sans l’aide d’API de Service de support.

###<a name="asset-encryption-options"></a>Options de chiffrement de biens

Selon le type de contenu que vous voulez télécharger, stocker et fournir, Media Services offre différentes options de chiffrement que vous avez le choix entre.

**Aucun** Aucun chiffrement est utilisé. Il s’agit de la valeur par défaut. Notez que lorsque vous utilisez cette option votre contenu n’est pas protégé lors des transferts ou au reste dans le stockage.

Si vous envisagez d’effectuer un MP4 à l’aide de téléchargement progressif, utilisez cette option pour télécharger votre contenu.

**StorageEncrypted** – Utilisez cette option pour chiffrer votre contenu effacer localement en utilisant le chiffrement AES 256 bits et puis téléchargez-le sur le stockage Azure où il est stocké chiffrées au reste. Ressources protégés par le chiffrement de stockage sont automatiquement non chiffrés et placés dans un système de fichiers chiffrés avant le codage et éventuellement chiffrés avant de le télécharger comme un nouvel actif de sortie. Le cas d’utilisation principal pour le chiffrement de stockage est lorsque vous voulez protéger vos fichiers de média d’entrée de haute qualité avec chiffrement fort inactives sur disque. 

Afin de fournir une gestion du stockage chiffré, vous devez configurer stratégie de remise de biens afin que Media Services sache comment vous voulez présenter votre contenu. Avant de vos biens peuvent être diffusés en continu, le serveur de diffusion supprime le chiffrement de stockage et flux de données votre contenu à l’aide de la stratégie de livraison spécifiée (par exemple, AES, PlayReady ou aucun chiffrement). 

**CommonEncryptionProtected** - Utilisez cette option si vous voulez chiffrer (ou télécharger déjà chiffré) contenu avec chiffrement courantes ou PlayReady DRM (par exemple, Smooth Streaming protégé à l’aide PlayReady DRM).

**EnvelopeEncryptionProtected** – Utilisez cette option si vous voulez protéger (ou télécharger déjà protégé) HTTP Live Streaming (TLS) chiffrées avec chiffrement AES (Advanced Standard). Notez que si vous téléchargez TLS déjà chiffré avec AES, il doit avoir été chiffré par Gestionnaire de transformer.

###<a name="access-policy"></a>Stratégie d’accès 

Un [AccessPolicy](https://msdn.microsoft.com/library/azure/hh974297.aspx) définit les autorisations (par exemple, lecture, écriture et liste) et la durée d’accès à une ressource. Vous souhaitez généralement passer un objet AccessPolicy à un repère est ensuite utilisé pour accéder aux fichiers contenus dans un bien.


###<a name="blob-container"></a>Conteneur BLOB

Un conteneur blob fournit un regroupement d’un ensemble d’objets BLOB. Conteneurs BLOB sont utilisées dans Media Services comme point limite pour contrôle d’accès et les repères de Signature accès partagé (sa) sur actifs. Un compte de stockage Azure peut contenir un nombre illimité de conteneurs blob. Un conteneur peut stocker un nombre illimité d’objets BLOB.

>[AZURE.NOTE]Vous ne devez pas essayer modifier le contenu des conteneurs blob qui ont été générés par Media Services sans l’aide d’API de Service de support.

###<a id="locators"></a>Repères

S [Locator](https://msdn.microsoft.com/library/azure/hh974308.aspx)fournissent un point d’entrée pour accéder aux fichiers contenus dans un bien. Une stratégie d’accès est utilisée pour définir les autorisations et la durée qu’un client a accès à une ressource donnée. Repères peuvent avoir une relation plusieurs à un avec une stratégie d’accès, tel que Locator différents peut fournir les heures de début différents types de connexions aux et différents clients lors de l’utilisation tous les mêmes autorisations et les paramètres de durée ; Toutefois, en raison d’une restriction de stratégie d’accès partagé définie par les services de stockage Azure, vous ne peut pas contenir plus de cinq Locator unique associé à une ressource donnée en même temps. 

Media Services prend en charge les deux types de repères : OnDemandOrigin Locator, utilisé pour diffuser des médias (par exemple, MPEG tiret, TLS ou Smooth Streaming) ou télécharger progressivement Locator URL associations de sécurité et éléments multimédias utilisé pour charger ou télécharger des éléments multimédias fichiers signalement to\from stockage Azure. 

Notez que l’autorisation de liste (AccessPermissions.List) ne doit pas être utilisée lorsque vous créez un repère OrDemandOrigin. 

###<a name="storage-account"></a>Compte de stockage

Tout accès au stockage Azure s’effectue via un compte de stockage. Un compte de Service de support pouvez associer un ou plusieurs comptes de stockage. Un compte peut contenir un nombre illimité de conteneurs, tant que leur taille totale est sous 500 To par compte de stockage.  Media Services fournit des outils de niveau SDK pour vous permettre de gérer plusieurs comptes de stockage et équilibrer la distribution de vos ressources lors du téléchargement à ces comptes en fonction de mesures ou distribution aléatoire. Pour plus d’informations, voir utiliser avec [Le stockage Azure](https://msdn.microsoft.com/library/azure/dn767951.aspx). 

##<a name="jobs-and-tasks"></a>Projets et des tâches

Une [tâche](https://msdn.microsoft.com/library/azure/hh974289.aspx) est généralement utilisée pour les processus (par exemple, index ou encoder) une présentation audio/vidéo. Si vous traitez plusieurs vidéos, créer une tâche pour chaque vidéo à encoder.

Une fonction contient des métadonnées sur le traitement à effectuer. Chaque tâche contient une ou plusieurs s [tâche](https://msdn.microsoft.com/library/azure/hh974286.aspx)qui spécifient une tâche de traitement atomique, ses actifs d’entrée, de sortie actifs, un processeur média et ses paramètres associés. Tâches d’un projet peuvent être enchaînés, où les biens de sortie d’une tâche est donné cet élément d’entrée à la tâche suivante. De cette façon une tâche peut contenir tout le traitement nécessaire à une présentation multimédia.

##<a id="encoding"></a>Codage

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

Pour plus d’informations sur les encodeurs pris en charge, voir [encodeurs](media-services-encode-asset.md).


##<a name="live-streaming"></a>Live diffusion en continu

Dans Azure Media Services, un canal représente un pipeline de traitement de contenu de diffusion en continu live. Un canal reçoit le flux d’entrée en direct dans de deux façons :

- Un encodeur live local envoie RTMP multi-débit ou Smooth Streaming (MP4 fragmenté) pour le canal. Vous pouvez utiliser les encodeurs live suivantes qui sortie multi-débit Smooth Streaming : élémentaire, Envivio, Cisco. Les encodeurs live suivants de sortie RTMP : transcodeurs Adobe Flash Live Telestream Wirecast et Tricaster. Les flux ingérés traversent canaux sans traitement supplémentaire. Lors d’une demande, Media Services offre le flux de données.

- Un flux de débit unique (dans un des formats suivants : RTP (MPEG-TS)), RTMP ou Smooth Streaming (MP4 fragmenté)) est envoyée au canal est activé pour effectuer le codage live avec Media Services. Le canal exécute puis live codage d’entrants flux débit unique à un flux vidéo multi-débit (adaptative). Lors d’une demande, Media Services offre le flux de données.

###<a name="channel"></a>Canal

Dans les Services de support, [canal](https://msdn.microsoft.com/library/azure/dn783458.aspx)s sont responsables de traitement du contenu de diffusion en continu live. Un canal fournit un point de terminaison d’entrée (URL d’acquisition) que vous fournissez puis à un TRANSCODEUR live. Le canal reçoit des flux d’entrée en direct à partir de la TRANSCODEUR live et rend disponible pour la diffusion en continu via un ou plusieurs StreamingEndpoints. Canaux également fournissent un aperçu point de terminaison (URL d’aperçu) que vous utilisez pour afficher un aperçu et valider votre flux de données avant d’un traitement approfondi et de livraison.

Lorsque vous créez le canal, vous pouvez obtenir l’URL d’acquisition et l’aperçu. Pour obtenir ces URL, le canal n’a pas à être à l’état démarré. Lorsque vous êtes prêt à transmettre des données à partir d’un TRANSCODEUR live dans le canal, le canal doit être démarré. Une fois que le TRANSCODEUR live commence à traiter les données, vous pouvez afficher un aperçu de votre flux de données.

Chaque compte Media Services peut contenir plusieurs canaux, plusieurs programmes et plusieurs StreamingEndpoints. Selon les besoins de bande passante et de sécurité, services StreamingEndpoint peuvent être dédiés à une ou plusieurs couches. N’importe quel StreamingEndpoint pouvez extraire à partir de n’importe quel canal.


###<a name="program"></a>Programme

Un [programme](https://msdn.microsoft.com/library/azure/dn783463.aspx) vous permet de contrôler la publication et le stockage de segments dans un flux en direct. Chaînes gérer les programmes. La relation de canal et du même programme est très similaire au support traditionnel où un canal possède un flux constant de contenu et un programme portée est limitée à certains événement chronométré sur ce canal.
Vous pouvez spécifier le nombre d’heures pendant lesquelles que vous souhaitez conserver le contenu enregistré pour le programme en définissant la propriété **ArchiveWindowLength** . Cette valeur peut être définie à partir d’un minimum de 5 minutes pour un maximum de 25 heures.

ArchiveWindowLength détermine également que la quantité maximale de fois que les clients pouvez effectuer une recherche dans le temps à partir de la position actuelle live. Programmes peuvent s’exécuter sur la période de temps spécifié, mais contenu qui se situe derrière la longueur de la fenêtre est supprimé en permanence. Cette valeur de cette propriété détermine également la durée pendant laquelle les manifestes client peuvent croître.

Chaque programme est associé à une ressource. Pour publier le programme, vous devez créer un repère pour l’élément associé. Ayant cette locator activer vous permet de générer une URL de diffusion en continu que vous pouvez fournir à vos clients.

Un canal prend en charge jusqu'à trois programmes s’exécutant simultanément afin que vous pouvez créer plusieurs archives du même flux entrant. Cela vous permet de publier et archiver les différentes parties d’un événement selon les besoins. Par exemple, vos besoins consiste à archiver 6 heures d’un programme, mais pour diffuser dernières uniquement les 10 minutes. Pour ce faire, vous devez créer deux programmes s’exécutant simultanément. Un seul programme est défini sur Archiver 6 heures de l’événement, mais le programme n’est pas publié. L’autre programme est défini sur Archiver 10 minutes et ce programme est publié.


Pour plus d’informations, voir :

- [Utilisation des canaux sont habilités à effectuer encodage Live avec Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)
- [Utilisation des canaux qui reçoivent Multi-débit de flux d’encodeurs en local](media-services-live-streaming-with-onprem-encoders.md)
- [Les quotas et les limites](media-services-quotas-and-limitations.md).

##<a name="protecting-content"></a>Protéger le contenu

###<a name="dynamic-encryption"></a>Chiffrement dynamiques

Azure Media Services vous permet de sécuriser votre support entre le moment où qu'il quitte votre ordinateur via stockage, traitement et la livraison. Media Services vous permet d’effectuer votre contenu chiffré dynamiquement avec chiffrement AES (Advanced Standard) (à l’aide de clés de chiffrement 128 bits) et de chiffrement courantes (CENC) à l’aide de PlayReady et/ou Widevine DRM. Media Services fournit également un service permettant de clés AES et PlayReady des licences aux clients autorisés.

Pour l’instant, vous pouvez chiffrer les formats de diffusion en continu suivants : TLS, MPEG tiret et Smooth Streaming. Vous ne pouvez pas chiffrer HDS streaming format ou progressive télécharge.

Si vous souhaitez que pour les Services de support chiffrer un bien, vous devez associer une clé de chiffrement (CommonEncryption ou EnvelopeEncryption) à vos ressources et également configurer les stratégies d’autorisation pour la clé.

Si vous voulez diffuser la gestion du stockage chiffré, vous devez configurer stratégie de remise de biens afin de spécifier la manière dont vous souhaitez effectuer vos biens.

Lorsqu’un flux de données est demandé par un lecteur, Media Services utilise la clé spécifiée pour dynamiquement chiffrer votre contenu à l’aide d’un chiffrement enveloppe (avec AES) ou le chiffrement courantes (avec PlayReady ou Widevine). Pour déchiffrer le flux de données, le lecteur demande la clé à partir du service de remise clés. Pour déterminer si l’utilisateur est autorisé à obtenir la clé, le service évalue les stratégies d’autorisation que vous avez spécifiée pour la clé.


###<a name="token-restriction"></a>Restriction jeton

La stratégie d’autorisation clé contenu peut avoir une ou plusieurs restrictions d’autorisation : ouvrir, jeton restriction ou restriction IP. La stratégie restreint jeton doit être accompagnée d’un jeton émis par un Service (jeton de sécurité). Media Services prend en charge les jetons dans JSON Web jeton (JWT) et le format Simple Web jetons SWT (). Media Services ne fournit pas de Services de jetons banque d’informations sécurisé. Vous pouvez créer un STS personnalisé ou tirer parti de Microsoft Azure ACS aux jetons de problème. Le STS doit être configuré pour créer un jeton connecté avec les revendications spécifiées clé et le problème que vous avez spécifié dans la configuration du jeton de restriction. Le service de remise clés Media Services renvoie la clé demandée (ou la licence) pour le client si le jeton est valide et les revendications dans le jeton adapté ceux configurés la clé (ou la licence).

Lors de la configuration du jeton restreint stratégie, vous devez spécifier la clé primaire de vérification, les émetteur et les paramètres de l’audience. La clé primaire vérification contient la clé de signature avec le jeton, émetteur est le service de jeton sécurisé qui émet le jeton. L’audience (parfois appelée étendue) décrit l’objectif du jeton ou la ressource le jeton autorise l’accès à. Le service de remise clés Media Services valide que les valeurs suivantes dans le jeton correspondent aux valeurs dans le modèle.

Pour plus d’informations, voir les articles suivants :

[Vue d’ensemble du contenu de protéger](media-services-content-protection-overview.md)
[protéger avec AES 128](media-services-protect-with-aes128.md)
[protéger avec DRM](media-services-protect-with-drm.md)

##<a name="delivering"></a>Exécution

###<a id="dynamic_packaging"></a>Emballage dynamique

Lorsque vous travaillez avec Media Services, il est recommandé de coder vos fichiers mezzanine dans un débit adaptive MP4 définir, puis convertir le jeu le format souhaité à l’aide de l' [Emballage dynamique](media-services-dynamic-packaging-overview.md).


###<a name="streaming-endpoint"></a>Point de terminaison de diffusion en continu

Un StreamingEndpoint représente un service de diffusion en continu qui peut fournir un contenu directement dans une application de lecteur client ou à un réseau de remise du contenu (CDN) pour la distribution davantage (Azure Media Services propose désormais l’intégration Azure CDN.) Le flux sortant à partir d’un service StreamingEndpoint peut être un flux en direct ou une vidéo à la demande bien dans votre compte Media Services. En outre, vous pouvez contrôler la capacité du service StreamingEndpoint traiter les besoins croissants de bande passante en ajustant échelle unités (également appelé diffusion en continu). Il est recommandé d’allouer une ou plusieurs unités d’échelle pour les applications dans un environnement de production. Unités de l’échelle vous fournissent la capacité de sortie dédié qui peut être achetée par incréments de 200 Mbps et des fonctionnalités supplémentaires qui inclut actuellement emballage dynamique utiliser.

Il est recommandé d’utiliser le chiffrement dynamique emballage dynamique et/ou. Pour utiliser ces fonctionnalités, vous devez disposer au moins une unité de diffusion en continu du point de terminaison à partir de laquelle vous envisagez de flux. Pour plus d’informations, voir [mise à l’échelle en continu unités](media-services-portal-manage-streaming-endpoints.md).

Par défaut, vous pouvez avoir jusqu'à 2 diffusion en continu de points de terminaison de votre compte Media Services. Pour demander une limite supérieure, consultez [les Quotas et les limitations](media-services-quotas-and-limitations.md).

Vous êtes facturé uniquement lorsque votre StreamingEndpoint est en état d’exécution.

###<a name="asset-delivery-policy"></a>Stratégie de remise de biens

Effectuez les étapes décrites dans le flux de travail de distribution de contenu Media Services est configuration des [stratégies de remise pour les éléments ](https://msdn.microsoft.com/library/azure/dn799055.aspx)que vous souhaitez utiliser un flux. La stratégie de remise de biens indique Media Services souhaité pour vos biens à livrer : dans la diffusion en continu de protocole doit votre bien être dynamiquement empaqueté (par exemple, MPEG tiret, TLS, Smooth Streaming ou tous), si vous souhaitez dynamiquement chiffrer vos biens ou non et la manière dont (enveloppe ou le chiffrement courantes).

Si vous avez une gestion du stockage chiffré, avant de vos biens peuvent être diffusés en continu, le serveur de diffusion supprime le chiffrement de stockage et flux de données votre contenu à l’aide de la stratégie de livraison spécifiée. Par exemple, pour présenter votre bien chiffré avec une clé de chiffrement Standard (AES), définissez le type de stratégie à DynamicEnvelopeEncryption. Pour supprimer le chiffrement de stockage et flux du bien en clair, définissez le type de stratégie à NoDynamicEncryption.

###<a name="progressive-download"></a>Téléchargement progressif

Téléchargement progressif vous permet de commencer la lecture multimédia avant le téléchargement de l’intégralité du fichier. Vous pouvez uniquement progressivement télécharger un fichier MP4.

Notez que vous devez déchiffrer actifs chiffrés si vous le souhaitez pour les rendre disponible au téléchargement progressif.

Pour permettre aux utilisateurs avec des URL de téléchargement progressive, vous devez d’abord créer une locator OnDemandOrigin. Création le repère, vous donne le chemin d’accès de base de la ressource. Vous devez puis ajoutez le nom du fichier MP4. Par exemple :

http://amstest1.Streaming.mediaservices.Windows.NET/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

###<a name="streaming-urls"></a>URL de diffusion en continu

Diffusion en continu de votre contenu aux clients. Pour permettre aux utilisateurs avec l’URL de diffusion en continu, vous devez d’abord créer une locator OnDemandOrigin. Création le repère, vous donne le chemin d’accès de base de la ressource qui contient le contenu que vous voulez diffuser. Toutefois, pour pouvoir diffuser ce contenu, vous devez modifier ce chemin d’accès supplémentaires. Pour créer une URL complète vers le fichier manifeste de diffusion en continu, vous devez concaténer valeur de chemin d’accès de l’URL et le manifeste (filename.ism) nom de fichier. Ajoutez ensuite /manifeste et un format approprié (si nécessaire) le chemin d’accès locator.

Vous pouvez aussi diffuser votre contenu via une connexion SSL. Pour ce faire, assurez-vous que votre URL de diffusion en continu commencent par HTTPS.

Notez que vous pouvez diffuser uniquement via le protocole SSL si le point de terminaison de diffusion en continu à partir de laquelle vous présenter votre contenu a été créé après 10 septembre 2014. Si votre URL de diffusion en continu est basées sur les points de terminaison de diffusion en continu créées après 10 septembre, l’URL contient « streaming.mediaservices.windows.net » (le nouveau format). URL de diffusion en continu qui contiennent « origin.mediaservices.windows.net » (l’ancien format) ne prennent pas en charge SSL. Si votre URL est dans l’ancien format et que vous voulez être en mesure de diffuser en continu sur SSL, créez un point de terminaison diffusion en continu. URL créées en fonction de la nouvelle extrémité de diffusion en continu permet de diffuser votre contenu via le protocole SSL.

La liste suivante décrit les différents formats de diffusion en continu et donne des exemples :

- Diffusion en continu lisse

{diffusion en continu du point de terminaison nom media services compte name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest


- TIRET MPEG

{diffusion en continu du point de terminaison nom media services compte name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(format=MPD-Time-CSF)



- Apple HTTP Live en continu V4 (TLS)

{diffusion en continu du point de terminaison nom media services compte name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(format=m3u8-AAPL)



- Apple HTTP Live en continu V3 (TLS)

{diffusion en continu du point de terminaison nom media services compte name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(format=m3u8-AAPL-v3)

- HDS (pour les détenteurs d’Adobe emploi/Access uniquement)

{diffusion en continu du point de terminaison nom media services compte name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(format=f4m-f4f)


##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
