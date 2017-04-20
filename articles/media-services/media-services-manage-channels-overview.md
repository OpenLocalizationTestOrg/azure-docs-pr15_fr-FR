<properties 
    pageTitle="Vue d’ensemble de Live vapeur à l’aide d’Azure Media Services | Microsoft Azure" 
    description="Cette rubrique fournit une vue d’ensemble de vapeur live à l’aide d’Azure Media Services." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="10/17/2016"
    ms.author="juliako"/>

#<a name="overview-of-live-steaming-using-azure-media-services"></a>Vue d’ensemble de Live vapeur à l’aide d’Azure Media Services

##<a name="overview"></a>Vue d’ensemble

Lors de la remise des événements de diffusion en continu live avec Azure Media Services les composants suivants sont généralement impliqués :

- Caméra qui est utilisée pour diffuser un événement.
- Encodeur vidéo live qui convertit les signaux de la caméra dans des flux qui sont envoyés à un service de diffusion en continu live.

    Vous pouvez également plusieurs codeurs live lors de la synchronisation. Pour certains critique live événements qu’à la demande très haute disponibilité et la qualité de l’expérience, il est recommandé à employer encodeurs redondants actif avec la synchronisation de l’heure pour obtenir un basculement transparent avec aucune perte de données.
- Service de diffusion en continu live qui vous permet d’effectuer les opérations suivantes :
    
    - acquisition de contenu en direct à l’aide de divers protocoles de diffusion en continu live (par exemple RTMP ou Smooth Streaming),
    - (facultatif) encoder votre flux de données en débit adaptive flux
    - afficher un aperçu de votre flux en direct,
    - Enregistrer et stocker le contenu ingéré afin de transmettre en continu ultérieure (vidéos à la demande)
    - fournir le contenu via les protocoles de diffusion en continu courantes (par exemple, MPEG tiret, lisses, TLS, HDS) directement à vos clients ou à un réseau de remise du contenu (CDN) pour la distribution davantage.


**Microsoft Azure Media Services** (AMS) offre la possibilité d’acquisition, Encoder, afficher un aperçu, stocker et présenter votre contenu de diffusion en continu live.

Lors de la remise de votre contenu aux clients votre objectif consiste à fournir une vidéo de haute qualité à divers périphériques dans des conditions réseau différente. Pour ce faire, utilisez encodeurs live à coder votre flux de données dans un flux de vidéo à plusieurs débit (débit adaptive).  Pour prendre en charge des flux de données sur différents appareils, permet de Media Services [emballage dynamique](media-services-dynamic-packaging-overview.md) pour dynamiquement nouveau compresser votre flux à différents protocoles. Media Services prend en charge la remise de la vitesse de transmission adaptive suivant diffusion en continu de technologies d’assistance : diffusion en continu Live HTTP (TLS), Smooth Streaming, MPEG tiret et HDS (pour les détenteurs d’Adobe emploi/Access uniquement).

Dans Azure Media Services, **canaux**, **programmes**et **StreamingEndpoints** gérer toutes les fonctionnalités diffusion en continu live, y compris acquisition, mise en forme, redondance, DVR, sécurité et extensibilité élevées.

Un **canal** représente un pipeline de traitement de contenu de diffusion en continu live. Un canal peut recevoir un instantané d’entrée de flux de données des façons suivantes :

- Un local live encodeur envoie multi-débit **RTMP** ou **Smooth Streaming** (MP4 fragmentés) pour le canal est configuré pour la remise **directe** . La remise **directe** est lorsque les flux ingérés traversent **canal**s sans traitement supplémentaire. Vous pouvez utiliser les encodeurs live suivantes qui sortie multi-débit Smooth Streaming : élémentaire, Envivio, Cisco.  Les encodeurs live suivants de sortie RTMP : transcodeurs Adobe Flash Media Live encodeur (FMLE), Telestream Wirecast et Tricaster.  Un encodeur live pouvez également envoyer un flux de débit unique sur un canal qui n’est pas activée pour le codage live, mais qui n’est pas recommandé. Lors d’une demande, Media Services offre le flux de données.

    >[AZURE.NOTE] À l’aide d’une méthode directe est le moyen le plus économique live en continu lorsque vous effectuez plusieurs événements sur une longue période de temps, et que vous avez déjà investi dans codeurs en local. Voir détails des [tarifs](/pricing/details/media-services/) .
    
    
- Un encodeur live local envoie un flux de débit binaire unique pour le canal est activé pour effectuer le codage live avec Media Services dans un des formats suivants : RTMP ou Smooth Streaming (MP4 fragmentés). RTP (MPEG-TS) est également prise en charge, à condition que vous avez une connexion dédiée au centre de données Azure. Les encodeurs live suivants avec sortie RTMP connus pour fonctionner avec des canaux de ce type : Telestream Wirecast, FMLE. Le canal exécute puis live codage d’entrants flux débit unique à un flux vidéo multi-débit (adaptative). Lors d’une demande, Media Services offre le flux de données.


Commençant par la version 2.10 de Services de support, lorsque vous créez un canal, vous pouvez spécifier la façon dont vous voulez pour votre canal recevoir le flux d’entrée et ou non souhaitée pour le canal effectuer encodage live de votre flux de données. Vous avez deux possibilités :

- **Aucun** (transfert) – spécifiez cette valeur, si vous envisagez d’utiliser un encodeur live local qui aura pour résultat multi-débit flux (un flux de données SQL directe). Dans ce cas, le flux entrant passer à la sortie sans n’importe quel codage. C’est le comportement d’un canal avant la version 2.10.  

- **Standard** – choisir cette valeur, si vous prévoyez d’utiliser Media Services pour encoder votre flux live débit unique au flux multi-débit. Cette méthode est plus économique pour suivre l’évolution rapidement pour les événements peu fréquents. N’oubliez pas qu’il existe un impact sur facturation pour codage direct et vous devez vous rappeler que quitter un canal codage live « en cours d’exécution » implique des frais de facturation.  Il est recommandé d’arrêter votre canaux en cours d’exécution immédiatement une fois votre événement de diffusion en continu live terminée pour éviter des frais supplémentaires par heure. 

##<a name="comparison-of-channel-types"></a>Comparaison des Types de canal

Tableau suivant fournit un guide pour comparer les deux types de canal pris en charge par Media Services

Fonctionnalité|Canal directe|Canal standard
---|---|---
Entrée de débit unique est codée dans plusieurs vitesses de transmission dans le cloud|N°|Oui
Résolution maximale, le nombre de calques|1080p, 8 couches, 60 + i|720p, 6 couches, 30 i/s
Protocoles d’entrée|RTMP, Smooth Streaming est|RTMP Smooth Streaming et RTP
Prix|Consultez les [tarifs page](/pricing/details/media-services/) et cliquez sur l’onglet « Vidéo Live »|Consultez les [tarifs de page](/pricing/details/media-services/) 
Durée maximale d’exécution|24 x 7|8 heures
Prise en charge pour l’insertion de tablettes|N°|Oui
Prise en charge de signalisation ad|N°|Oui
Légendes ACE 608/708 directe|Oui|Oui
Capacité à récupérer brèves places dans contribution flux|Oui|Non (canal commence slating après 6 + secondes sans données d’entrée)
Prise en charge des groupes d’images non uniforme d’entrée|Oui|Non – entrée doit être corrigée 2sec contigus
Prise en charge pour l’entrée de taux de trame variable|Oui|Non – entrée doit être constants fréquence d’images.<br/>Variations mineures sont tolérées, par exemple, au cours de scènes à haut débit. Mais Impossible de supprimer l’encodeur 10 cadres/s.
Automatique-fermeture des canaux lorsque entrée flux est perdue|N°|Après avoir 12 heures, s’il n’existe aucun programme en cours d’exécution 

##<a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Utilisation des canaux qui reçoivent multi-débit de flux d’encodeurs locaux (transfert)

Le diagramme suivant montre les principales parties de la plateforme AMS impliqués dans le flux de travail **directe** .

![Flux de travail direct](./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png)

Pour plus d’informations, voir [utilisation des canaux que Multi réception-flux en direct débit de codeurs local](media-services-live-streaming-with-onprem-encoders.md).

##<a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Utilisation des canaux autorisés à effectuer le codage live avec Azure Media Services

Le diagramme suivant montre les principales parties de la plateforme AMS impliqués dans les flux de travail Live en continu où un canal est activé pour effectuer le codage live avec Media Services.

![Flux de travail direct](./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png)

Pour plus d’informations, voir [utilisation des canaux qui sont habilités à effectuer encodage Live avec Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

##<a name="description-of-a-channel-and-its-related-components"></a>Description d’un canal et ses composants associés

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


##<a name="billing-implications"></a>Implications de facturation

Un canal commence dès qu’elle est transitions d’état à « Exécuter » via l’API de facturation.  

Le tableau suivant montre la correspondance entre les États de canal et les États de facturation dans le portail de l’API et Azure. Notez que les États sont légèrement différentes entre l’API et le portail expérience utilisateur. Dès qu’un canal est à l’état « En cours d’exécution » via l’API, ou dans l’état « Prêt » ou « Diffusion » dans le portail Azure, facturation seront actif.

Pour arrêter le canal de facturation vous supplémentaire, vous devez arrêter le canal via l’API ou dans le portail Azure.
Vous êtes responsable arrêt vos canaux lorsque vous avez terminé avec le canal. Pour arrêter le canal provoque dans Facturation continue.

>[AZURE.NOTE]Lorsque vous travaillez avec des canaux Standard, AMS automatique fermeture n’importe quel canal est toujours en « en cours d’exécution » 12 heures une fois que le flux d’entrée est perdu et aucun programme en cours d’exécution. Toutefois, vous serez toujours facturé pour la fois que le canal était dans l’état « En cours d’exécution ».

###<a id="states"></a>États de canal et la manière dont elles sont mappées vers le mode de facturation 

L’état actuel d’un canal. Les valeurs possibles sont les suivantes :

- **Arrêté**. Il s’agit de l’état initial du canal après sa création (sauf si le démarrage automatique a été activé dans le portail.) Aucune facturation se produit dans cet état. Dans cet état, les propriétés de canal pouvant être mis à jour, mais la diffusion n’est pas autorisée.
- **Démarrage**. Le canal est démarré. Aucune facturation se produit dans cet état. Aucune mise à jour ou diffusion en continu est autorisée pendant cet état. Si une erreur se produit, le canal revient à l’état arrêté.
- **En cours d’exécution**. Le canal est capable de traiter les flux en direct. Il est maintenant facturation l’utilisation. Vous devez arrêter le canal afin d’empêcher davantage de facturation. 
- **Arrêt**. Le canal s’est arrêté. Aucune facturation se produit dans cet état transitoire. Aucune mise à jour ou diffusion en continu est autorisée pendant cet état.
- **La suppression**. Le canal est supprimé. Aucune facturation se produit dans cet état transitoire. Aucune mise à jour ou diffusion en continu est autorisée pendant cet état.

Le tableau suivant montre comment les États de canal établir une correspondance entre le mode de facturation. 
 
État de canal|Indicateurs de l’interface utilisateur du portail|Il est facturation ?
---|---|---
Démarrage|Démarrage|Aucun (état transitoire)
En cours d’exécution|Prêt (en cours d’exécution de programmes)<br/>ou<br/>Diffusion en continu (au moins un programme en cours)|OUI
Arrêt|Arrêt|Aucun (état transitoire)
Arrêté|Arrêté|N°


##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



##<a name="related-topics"></a>Rubriques connexes

[Spécification d’acquisition Azure Media Services MP4 fragmentés Live](media-services-fmp4-live-ingest-overview.md)

[Utilisation des canaux sont habilités à effectuer encodage Live avec Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)

[Utilisation des canaux qui reçoivent Multi-débit de flux d’encodeurs en local](media-services-live-streaming-with-onprem-encoders.md)

[Les quotas et les limites](media-services-quotas-and-limitations.md).  

[Concepts de Services de support](media-services-concepts.md)
