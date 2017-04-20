<properties 
    pageTitle="Vue d’ensemble de Azure Media Services et scénarios courants | Microsoft Azure" 
    description="Cette rubrique vous donne une vue d’ensemble de Azure Media Services" 
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
    ms.topic="hero-article" 
    ms.date="10/12/2016"
    ms.author="juliako;anilmur"/>

#<a name="azure-media-services-overview-and-common-scenarios"></a>Vue d’ensemble de Azure Media Services et scénarios courants

Microsoft Azure Media Services est une plate-forme extensible sur le nuage qui permet aux développeurs de créer des applications de gestion et de livraison scalable multimédia. Media Services est basé sur des API REST qui vous permettent en toute sécurité télécharger, stocker, coder et regrouper contenu vidéo ou audio pour la diffusion en continu à la demande et direct aux clients différents (par exemple, TV, PC et périphériques mobiles).

Vous pouvez créer des flux de travail de bout en bout en conservant tous les Services de support. Vous pouvez également choisir d’utiliser des composants tiers pour certaines parties de votre flux de travail. Par exemple, encoder à l’aide d’un encodeur tiers. Ensuite, télécharger, protéger, du package, effectuer à l’aide de Media Services.

Vous pouvez choisir flux votre contenu live ou fournir un contenu à la demande. Cette rubrique décrit des scénarios courants permettant de votre contenu [live](media-services-overview.md#live_scenarios) ou [à la demande](media-services-overview.md#vod_scenarios). La rubrique également des liens vers d’autres rubriques pertinentes.

## <a name="sdks-and-tools"></a>SDK et outils

Pour créer des solutions et Services de support, vous pouvez utiliser :

- [Services de support API REST](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- Un des kits de développement logiciel client disponibles :
- [Azure Media Services SDK pour .NET](https://github.com/Azure/azure-sdk-for-media-services),
- [Azure SDK pour Java](https://github.com/Azure/azure-sdk-for-java),
- [Azure SDK PHP](https://github.com/Azure/azure-sdk-for-php),
- [Azure Media Services pour Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (Il s’agit d’une version non Microsoft d’un SDK Node.js. Il est géré par une Communauté et n’a pas une couverture de 100 % des AMS APIs).
- Outils existants :
- [Portail Azure](https://portal.azure.com/)
- [Azure Media-Services l’Explorateur](https://github.com/Azure/Azure-Media-Services-Explorer) (Azure Media Services Explorer (AMSE) est un Winforms / application c# pour Windows)

##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

Vous pouvez afficher AMS rubriques d’apprentissage professionnelles ici :

- [AMS Live diffusion en continu du flux de travail](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [AMS à la demande de diffusion en continu de flux de travail](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

##<a name="prerequisites"></a>Conditions préalables

Pour commencer à utiliser Azure Media Services, vous devez disposer des éléments suivants :

3. Un compte Azure. Si vous n’avez pas un compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com).
2. Un compte Azure Media Services. Utiliser le portail Azure, .NET ou API REST pour créer compte Azure Media Services. Pour plus d’informations, voir [Créer un compte](media-services-portal-create-account.md).
3. (Facultatif) Configurer l’environnement de développement. Choisissez .NET ou API REST pour votre environnement de développement. Pour plus d’informations, voir [configurer environnement](media-services-dotnet-how-to-use.md).

En outre, découvrez comment vous connecter par programme [se connecter](media-services-dotnet-connect-programmatically.md).
4. (Recommandé) Allouer une ou plusieurs unités d’échelle. Il est recommandé d’allouer une ou plusieurs unités d’échelle pour les applications dans un environnement de production.   Pour plus d’informations, voir [Gestion des points de terminaison de la diffusion](media-services-portal-manage-streaming-endpoints.md).

##<a name="concepts-and-overview"></a>Vue d’ensemble et concepts

Pour les concepts Azure Media Services, voir [Concepts](media-services-concepts.md).

Pour une série de procédure présente les principaux composants de Azure Media Services, consultez les [didacticiels pas à pas Azure Media Services](https://docs.com/fukushima-shigeyuki/3439/english-azure-media-services-step-by-step-series). Cette série comporte une vue d’ensemble des concepts et qu’il utilise l’outil AMSE pour montrer les tâches AMS. Notez que AMSE est un outil de Windows. Cet outil prend en charge la plupart des tâches que vous pouvez obtenir par programme avec [SDK AMS pour .NET](https://github.com/Azure/azure-sdk-for-media-services), [Azure SDK pour Java](https://github.com/Azure/azure-sdk-for-java)ou [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php).

##<a id="vod_scenarios"></a>Exécution d’éléments multimédias à la demande avec Azure Media Services : scénarios courants et des tâches

Cette section décrit des scénarios courants et fournit des liens vers les rubriques appropriées. Le diagramme suivant affiche les principales parties de la plateforme Media Services impliqués en matière de contenu à la demande. 

![Flux de travail VoD](./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png)


###<a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>Protéger le contenu dans le stockage et diffuser multimédia en clair (non chiffrés)

1. Télécharger un fichier de haute qualité mezzanine dans un bien.
    
    Il est recommandé d’appliquer l’option chiffrement stockage pour vos ressources afin de protéger votre contenu lors du téléchargement et tout en reste dans le stockage.
 
1. Encoder à un ensemble de fichiers de débit adaptive MP4. 

    Il est recommandé d’appliquer l’option chiffrement stockage de la ressource de sortie afin de protéger votre contenu inactives.
    
1. Configurer la stratégie de remise de biens (utilisée par emballage dynamique). 
    
    Si votre actif est stockage chiffré, vous **devez** configurer stratégie de remise de biens. 

1. Publier l’actif en créant un repère à la demande.

    Assurez-vous que vous disposez au moins une unité réservée sur le point de terminaison de diffusion en continu à partir duquel vous souhaitez transmettre du contenu de diffusion en continu.

1. Flux de données du contenu publié.

###<a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>Protéger le contenu dans le stockage, remis dynamiquement chiffré multimédia  

Pour pouvoir utiliser le chiffrement dynamique, vous devez d’abord obtenir au moins une unité réservée diffusion en continu sur le point de terminaison de diffusion en continu à partir de laquelle vous souhaitez diffuser le contenu chiffré.

1. Télécharger un fichier de haute qualité mezzanine dans un bien. Appliquer l’option chiffrement stockage de la ressource.
1. Encoder à un ensemble de fichiers de débit adaptive MP4. Appliquer l’option chiffrement stockage de la ressource de sortie.
1. Créer la clé de chiffrement contenu de l’actif que vous souhaitez être chiffrées dynamiquement pendant la lecture.
2. Configurer la stratégie d’autorisation clé contenu.
1. Configurer la stratégie de remise de biens (utilisée par emballage dynamique et le chiffrement dynamique).
1. Publier l’actif en créant un repère à la demande.
1. Flux de données du contenu publié. 

###<a name="use-media-analytics-to-derive-actionable-insights-from-your-videos"></a>Media Analytique permet de tirer des leçons exploitables à partir de vos vidéos 

Média Analytique est un ensemble de la reconnaissance vocale et vision composants qui facilitent pour les organisations et les entreprises à tirer des leçons exploitables à partir de leurs fichiers vidéo. Pour plus d’informations, voir [Présentation des Analytique Azure Media Services](media-services-analytics-overview.md).

1. Télécharger un fichier de haute qualité mezzanine dans un bien.
2. Utiliser un des services de support Analytique suivants pour traiter vos vidéos :
    
    - **Indexeur** – [vidéos de processus avec Azure Media indexeur 2](media-services-process-content-with-indexer2.md)
    - **Hyperlapse** – [fichiers multimédia Hyperlapse avec Azure Media Hyperlapse](media-services-hyperlapse-content.md)
    - **Détection de mouvement** – [Détection de mouvement pour Azure Media Analytique](media-services-motion-detection.md).
    - **La détection de visage et émotions nominale** – [détection émotions pour Azure Media Analytique et Face](media-services-face-and-emotion-detection.md).
    - **Synthèse vidéo** – [Utiliser Azure Media vidéo les miniatures pour créer une synthèse vidéo](media-services-video-summarization.md)
3. Processeurs multimédia Media Analytique produisent des fichiers MP4 ou JSON. Si un processeur média produit un fichier MP4, vous pouvez progressivement télécharger le fichier. Si un processeur média a donné un fichier JSON, vous pouvez télécharger le fichier à partir du stockage blob Azure. 


###<a name="deliver-progressive-download"></a>Effectuer le téléchargement progressif 

1. Télécharger un fichier de haute qualité mezzanine dans un bien.
1. Coder dans un fichier MP4 unique.
1. Publier l’actif en créant un repère à la demande ou les associations de sécurité.

    Si vous utilisez locator à la demande, assurez-vous que vous disposez au moins une unité réservée sur le point de terminaison de diffusion en continu à partir de laquelle vous souhaitez télécharger progressivement du contenu de diffusion en continu.

    Si vous utilisez locator associations de sécurité, le contenu est téléchargé à partir du stockage blob Azure. Dans ce cas, il est inutile d’avoir réservé unités la diffusion.
  
1. Progressivement télécharger le contenu.

##<a id="live_scenarios"></a>Exécution d’événements de diffusion en continu Live avec Azure Media Services

Lorsque vous travaillez avec Live en continu les composants suivants sont généralement impliqués :

- Caméra qui est utilisée pour diffuser un événement.
- Encodeur vidéo live qui convertit les signaux de la caméra dans des flux qui sont envoyés à un service de diffusion en continu live.

Vous pouvez également plusieurs codeurs live lors de la synchronisation. Pour certains critique live événements qu’à la demande très haute disponibilité et la qualité de l’expérience, il est recommandé à employer encodeurs redondants actif avec l’heure synchronizationto atteindre un basculement transparent avec aucune perte de données.
- Service de diffusion en continu live qui vous permet d’effectuer les opérations suivantes :

- acquisition de contenu en direct à l’aide de divers protocoles de diffusion en continu live (par exemple RTMP ou Smooth Streaming),
- (facultatif) encoder votre flux de données en débit adaptive flux
- afficher un aperçu de votre flux en direct,
- Enregistrer et stocker le contenu ingéré afin de transmettre en continu ultérieure (vidéos à la demande)
- fournir le contenu via les protocoles de diffusion en continu courantes (par exemple, MPEG tiret, lisses, TLS, HDS) directement à vos clients ou à un réseau de remise du contenu (CDN) pour la distribution davantage.


**Microsoft Azure Media Services** (AMS) offre la possibilité d’acquisition, Encoder, afficher un aperçu, stocker et présenter votre contenu de diffusion en continu live.

Lors de la remise de votre contenu aux clients votre objectif consiste à fournir une vidéo de haute qualité à divers périphériques dans des conditions réseau différente. Pour prendre en charge des conditions de qualité et réseau, utilisez live encodeurs pour encoder votre flux au flux vidéo multi-débit (débit adaptive).  Pour prendre en charge des flux de données sur différents appareils, permet de Media Services [emballage dynamique](media-services-dynamic-packaging-overview.md) pour dynamiquement nouveau compresser votre flux à différents protocoles. Media Services prend en charge la remise de la vitesse de transmission adaptive suivant diffusion en continu de technologies d’assistance : diffusion en continu Live HTTP (TLS), Smooth Streaming, MPEG tiret et HDS (pour les détenteurs d’Adobe emploi/Access uniquement).

Dans Azure Media Services, **canaux**, **programmes**et **StreamingEndpoints** gérer toutes les fonctionnalités diffusion en continu live, y compris acquisition, mise en forme, redondance, DVR, sécurité et extensibilité élevées.

Un **canal** représente un pipeline de traitement de contenu de diffusion en continu live. Un canal peut recevoir un instantané d’entrée de flux de données des façons suivantes :

- Un local live encodeur envoie multi-débit **RTMP** ou **Smooth Streaming** (MP4 fragmentés) pour le canal est configuré pour la remise **directe** . La remise **directe** est lorsque les flux ingérés traversent **canal**s sans traitement supplémentaire. Vous pouvez utiliser les encodeurs live suivantes qui sortie multi-débit Smooth Streaming : élémentaire, Envivio, Cisco.  Les encodeurs live suivants de sortie RTMP : transcodeurs Adobe Flash Live Telestream Wirecast et Tricaster.  Un encodeur live pouvez également envoyer un flux de débit unique sur un canal qui n’est pas activée pour le codage live, mais qui n’est pas recommandé. Lors d’une demande, Media Services offre le flux de données.

>[AZURE.NOTE] À l’aide d’une méthode directe est le moyen le plus économique live en continu lorsque vous effectuez plusieurs événements sur une longue période de temps, et que vous avez déjà investi dans codeurs en local. Voir détails des [tarifs](/pricing/details/media-services/) .

- Un encodeur live local envoie un flux de débit binaire unique pour le canal est activé pour effectuer le codage live avec Media Services dans un des formats suivants : RTP (MPEG-TS), RTMP ou Smooth Streaming (MP4 fragmenté). Le canal exécute puis live codage d’entrants flux débit unique à un flux vidéo multi-débit (adaptative). Lors d’une demande, Media Services offre le flux de données.


###<a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Utilisation des canaux qui reçoivent multi-débit de flux d’encodeurs locaux (transfert)

Le diagramme suivant montre les principales parties de la plateforme AMS impliqués dans le flux de travail **directe** .

![Flux de travail direct][live-overview2]

Pour plus d’informations, voir [utilisation des canaux que Multi réception-flux en direct débit de codeurs local](media-services-live-streaming-with-onprem-encoders.md).

###<a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Utilisation des canaux autorisés à effectuer le codage live avec Azure Media Services

Le diagramme suivant montre les principales parties de la plateforme AMS impliqués dans les flux de travail Live en continu où un canal est activé pour effectuer le codage live avec Media Services.

![Flux de travail direct][live-overview1]

Pour plus d’informations, voir [utilisation des canaux qui sont habilités à effectuer encodage Live avec Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).


##<a name="consuming-content"></a>Utiliser le contenu

Azure Media Services fournit les outils nécessaires pour créer des applications de lecteur client riche dynamique pour la plupart des plates-formes, y compris : iOS appareils, les appareils Android, Windows, Windows Phone, Xbox et Terminal zones. La rubrique suivante fournit des liens vers les structures de lecteur que vous pouvez utiliser pour développer votre propre des applications clientes qui peuvent utiliser le multimédia à partir de Media Services et SDK.

[Développement d’Applications du lecteur vidéo](media-services-develop-video-players.md)

##<a name="enabling-azure-cdn"></a>L’activation d’Azure CDN

Media Services prend en charge l’intégration avec Azure CDN. Pour plus d’informations sur l’activation d’Azure CDN, Découvrez [comment gérer les points de terminaison diffusion en continu dans un compte de Services de support](media-services-portal-manage-streaming-endpoints.md).

##<a name="scaling-a-media-services-account"></a>Mise à l’échelle d’un compte Media Services

Vous pouvez mettre à l’échelle **Media Services** en spécifiant le nombre de **Diffusion en continu les unités réservés** et **Codage réservés unités** que vous voulez que votre compte pour la configuration à l’aide.

Vous pouvez également mettre à l’échelle votre compte Media Services en y ajoutant comptes de stockage. Chaque compte de stockage est limité à 500 To. Pour développer votre stockage au-delà des limites par défaut, vous pouvez choisir joindre plusieurs comptes de stockage à un seul compte Media Services.

[Cette](media-services-portal-scale-streaming-endpoints.md) rubrique des liens vers des rubriques pertinentes.

##<a name="support"></a>Prise en charge

[Azure prend en charge](https://azure.microsoft.com/support/options/) fournit des options de support pour Azure, y compris les Services de support.

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="service-level-agreement-sla"></a>Niveau accord de service)

- Pour le codage de Services de support, nous garantit 99,9 % de disponibilité des transactions API REST.
- Pour la diffusion en continu, nous traitera correctement demandes avec une garantie de disponibilité 99,9 % pour le contenu multimédia existant lors de l’achat d’au moins une unité de diffusion en continu.
- Pour les canaux Live, nous garantit que canaux en cours d’exécution aura connectivité externe au moins 99,9 % du temps.
- Pour la Protection du contenu, nous garantit que nous correctement répond à vos demandes de clé au moins 99,9 % du temps.
- D’indexation, nous traitera correctement les demandes de tâche indexeur traitées avec un codage réservé unité 99,9 % du temps.

Pour plus d’informations, voir [Microsoft Azure SLA](https://azure.microsoft.com/support/legal/sla/).

<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png
[vod-overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
[live-overview1]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png
[live-overview2]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png
 
