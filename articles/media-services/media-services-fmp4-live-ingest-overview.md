<properties 
    pageTitle="Spécification d’acquisition Azure Media Services MP4 fragmentés live | Microsoft Azure" 
    description="Cette spécification décrit le protocole et le format de réception de diffusion en continu live MP4 fragmenté en fonction de Microsoft Azure Media Services. Microsoft Azure Media Services fournit service de diffusion en continu live qui permet aux clients de diffuser des événements en direct et diffuser le contenu en temps réel à l’aide de Microsoft Azure en tant que la plateforme en nuage. Ce document traite également les meilleures pratiques dans la création de hautement redondantes et robuste live acquisition mécanismes." 
    services="media-services" 
    documentationCenter="" 
    authors="cenkdin" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"     
    ms.author="cenkdin;juliako"/>

#<a name="azure-media-services-fragmented-mp4-live-ingest-specification"></a>Spécification d’acquisition Azure Media Services MP4 fragmentés live

Cette spécification décrit le protocole et le format de réception de diffusion en continu live MP4 fragmenté en fonction de Microsoft Azure Media Services. Microsoft Azure Media Services fournit service de diffusion en continu live qui permet aux clients de diffuser des événements en direct et diffuser le contenu en temps réel à l’aide de Microsoft Azure en tant que la plateforme en nuage. Ce document traite également les meilleures pratiques dans la création de hautement redondantes et robuste live acquisition mécanismes.


##<a name="1-conformance-notation"></a>1. Notation de conformité

Les mots clés « doit », « Ne doit pas », « REQUIRED », « Devra », « Est pas », « Devrait », « Ne doivent pas », « Recommandé », « Peut » et « Facultatif » dans ce document doivent être interprétés comme décrit dans RFC 2119.

##<a name="2-service-diagram"></a>2. diagramme du Service de 

L’illustration ci-dessous montre l’architecture de haut niveau du service de diffusion en continu live dans Microsoft Azure Media Services :

1.  L’encodeur pousse flux live dans canaux qui sont créées et sa mise en service via Microsoft Azure Media Services SDK.
2.  Canaux, les programmes et toutes les fonctionnalités diffusion en continu live, y compris acquisition, mise en forme, la diffusion point de terminaison de la poignée de Microsoft Azure Media Services cloud redondance, DVR, sécurité et extensibilité élevées.
3.  Vous pouvez également clients pourraient choisir de déployer un calque CDN entre le point de terminaison de diffusion en continu et les points de terminaison client.
4.  Flux de points de terminaison de client à partir du point de terminaison de diffusion en continu à l’aide des protocoles diffusion adaptative HTTP (par exemple, Smooth Streaming, tiret, HDS ou TLS).

![Image1][image1]


##<a name="3-bit-stream-format--iso-14496-12-fragmented-mp4"></a>3. débit Format – ISO 14496-12 fragmenté MP4

Le format de transmission pour diffusion en continu live d’acquisition qui est décrit dans ce document est basé sur [ISO-14496-12]. Reportez-vous au [[MS-SSTR]](http://msdn.microsoft.com/library/ff469518.aspx) pour une explication détaillée de format MP4 fragmenté et d’extensions de deux fichiers vidéo à la demande et live diffusion en continu de réception.

###<a name="live-ingest-format-definitions"></a>Acquisition Live définitions de format 

Vous trouverez ci-dessous une liste de format spécial définitions qui s’appliquent actives d’acquisition dans Microsoft Azure Media Services :

1. Les 'ftyp', LiveServerManifestBox et boîte 'moov' doivent être envoyés avec chaque demande (HTTP POST).  Il doit être envoyé au début du flux de données et à chaque fois que l’encodeur doit se reconnecter pour reprendre l’acquisition de flux de données.  Pour plus d’informations, reportez-vous à la Section 6 [1].
2. Point 3.3.2 [1] définit une zone facultative appelée StreamManifestBox pour l’acquisition. En raison de la logique de routage d’équilibrage de charge de Microsoft Azure, l’utilisation de cette case est déconseillée et ne doivent ne pas être présent lors de réception dans Microsoft Azure Media Services. Si cette case est disponible, Azure Media Services ignore silencieusement.
3. Le TrackFragmentExtendedHeaderBox défini dans 3.2.3.2 [1] doit être présent pour chaque fragment.
4. La version 2 de la TrackFragmentExtendedHeaderBox doit être utilisé pour générer des segments de médias avec des URL identiques dans plusieurs centres de données. Le champ index fragment est requis pour basculement centre de données entre des flux de données à partir d’un index formats tels que Apple HTTP Live Streaming (TLS) et MPEG-tiret à partir d’un index.  Pour activer le basculement entre-centre de données, l’index de fragment doit être synchronisé sur plusieurs codeurs et augmenter par 1 pour chaque fragment media successifs, même à travers le redémarrage de décodage d’éléments ou défaillances.
5. Section 3.3.6 [1] définit zone appelée MovieFragmentRandomAccessBox (« mfra ») qui peut-être être envoyé à la fin de réception live pour indiquer la fin du support (fin de flux) pour le canal. En raison de la logique d’acquisition de Azure Media Services, l’utilisation de fin du support (fin de flux) est déconseillée et la zone « mfra » pour l’intégration ne doivent ne pas être envoyées. Si envoyé, Azure Media Services ignore silencieusement. Il est recommandé d’utiliser le [Canal réinitialiser](https://msdn.microsoft.com/library/azure/dn783458.aspx#reset_channels) pour réinitialiser l’état du point de réception et également qu’il est recommandé d’utiliser le [Programme cesser de](https://msdn.microsoft.com/library/azure/dn783463.aspx#stop_programs) pour mettre fin à une présentation et le flux de données.
6. La durée de fragment MP4 doit être constante, afin de réduire la taille des manifestes client et améliorer heuristique de téléchargement client grâce à l’utilisation de balises répétition.  La durée peut varier afin de compense la fréquence d’images non entier.
7. La durée de fragment MP4 doit être comprise entre environ 2 et 6 secondes.
8. MP4 fragment horodatages et index (TrackFragmentExtendedHeaderBox fragment_ absolute_ temps et fragment_index) doivent arrivent dans l’ordre croissant.  Bien que Azure Media Services est résistant aux fragments en double, il est très limité possibilité pour réorganiser les fragments en fonction de la chronologie du média.

##<a name="4-protocol-format--http"></a>4. Format du protocole – HTTP

ISO fragmenté MP4 basé live d’acquisition pour Microsoft Azure Media Services montre comment utiliser une norme longues HTTP POST demander pour transmettre des données multimédias codé compressées au format MP4 fragmenté au service. Chaque billet HTTP envoie une complète MP4 fragmenté flux de bits (« flux ») en commençant par commençant par zones d’en-tête (zone « ftyp », « Live de la zone serveur manifeste » et « moov ») et en continuant avec une séquence de fragments (zones « moof » et « mdat »). Reportez-vous à la section 9.2 in [1] pour la syntaxe d’URL pour la requête HTTP POST. Exemple de l’URL du billet est la suivante : 

    http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)

###<a name="requirements"></a>Configuration requise

Voici les exigences en détail :

1. Encodeur doit démarrer la diffusion en envoyant une demande HTTP POST avec un « corps vide » (longueur nulle contenu) à l’aide de la même URL de réception. Cela peut aider à détecter rapidement si le point de terminaison live réception est valide et s’il existe d’authentification ou d’autres conditions requises. Par protocole HTTP, le serveur ne pourrez envoyer de réponse HTTP précédent jusqu'à ce que la totalité de la demande, y compris le corps du billet correctement après réception. Étant donné la nature longue de l’événement, sans cette étape, l’encodeur peut ne pas être en mesure de détecter les erreurs jusqu'à ce qu’elle termine à envoyer toutes les données.
2. Encodeur doit gérer les erreurs ou les demandes d’authentification résulte (1). Si (1) a réussi avec une réponse 200, continuer.
3. Encodeur doit commencer une nouvelle demande HTTP POST par le flux MP4 fragmenté.  La charge utile doit commencer par les zones d’en-tête suivies fragments.  Remarque la zone « ftyp », « Live de la zone serveur manifeste » et « moov » (dans cet ordre) doit être envoyée à chaque demande, même si l’encodeur doit se reconnecter, car la demande précédente a été interrompue avant la fin du flux de données. 
4. Encodeur doit utiliser segmenté transférer encodage pour le téléchargement car il est impossible de prédire la longueur totale du contenu de l’événement en direct.
5. Une fois l’événement terminée, après avoir envoyé le dernier fragment, l’encodeur doit se terminer normalement la séquence segmenté encodage de transfert des messages (la plupart des piles de clients HTTP traitez-le automatiquement). Encodeur doit attendre service retourner le code de réponse finale, puis se ferme la connexion. 
6. Encodeur ne doit pas utiliser le substantif Events() comme décrit dans in 9.2 [1] pour l’intégration à Microsoft Azure Media Services.
7. Si la requête HTTP POST se termine ou arrive à expiration avant la fin du flux de données avec une erreur TCP, l’encodeur doit délivrer une nouvelle demande de publication à l’aide d’une nouvelle connexion et suivre les spécifications ci-dessus avec l’exigence supplémentaire que l’encodeur doit renvoyer les fragments MP4 deux précédente pour chaque piste dans le flux et reprendre sans introduire discontinuités dans la chronologie du média.  Renvoyer les deux derniers fragments MP4 pour chaque piste garantit qu’il n’existe aucune perte de données.  En d’autres termes, si un flux de données contient une piste audio et vidéo, et la demande de billet en cours échoue, l’encodeur doit vous reconnecter et renvoyer les deux derniers fragments pour la piste audio, qui ont déjà été envoyés, et les deux derniers fragments pour le suivi de vidéo, qui ont déjà été envoyés, afin de vous assurer qu’il n’existe aucune perte de données.  L’encodeur doit maintenir un tampon de « transfert » de fragments de support qui renvoie les lorsque vous vous reconnectez.

##<a name="5-timescale"></a>5. échelle de temps 

[[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx) décrit l’utilisation de « Échelle de temps » pour SmoothStreamingMedia (Section 2.2.2.1), StreamElement (Section 2.2.2.3), StreamFragmentElement(2.2.2.6) et LiveSMIL (Section 2.2.7.3.1). Si la valeur de l’échelle de temps n’est pas présente, la valeur par défaut utilisée est 10 000 000 (10 MHz). Bien que la spécification de Format de diffusion en continu lisse ne bloquer l’utilisation d’autres valeurs de l’échelle de temps, la plupart des cas de mises en œuvre encodeur cette valeur par défaut (10 MHz) pour générer Smooth Streaming acquisition de données. En raison de la fonctionnalité [Azure Media dynamique emballage](media-services-dynamic-packaging-overview.md) , il est conseillé d’utiliser kHz 90 l’échelle de temps pour les flux vidéo et kHz 44,1 ou 48.1 aux flux audio. Si l’échelle de temps différentes valeurs sont utilisées pour différents flux, l’échelle de temps de niveau de flux de données doit être envoyé. Reportez-vous à [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).     

##<a name="6-definition-of-stream"></a>6. définition de « Flux »  

« Flux » est l’unité de base d’opération dans l’acquisition de rédaction présentation en direct, la gestion en continu scénarios de basculement et redondance. « Flux » est définie comme un unique MP4 fragmenté flux de bits qui peut contenir une seule piste ou plusieurs pistes. Une présentation en direct peut contenir un ou plusieurs flux selon la configuration de l’encoder(s) live. Les exemples ci-dessous illustrent diverses options de l’utilisation d’autorisera pour composer une présentation en direct.

**Exemple :** 

Client souhaite créer une présentation de diffusion en continu live qui inclut les vitesses de transmission audio/vidéo suivants :

Vidéo – kb/s 3000, 1500 kb/s, 750 kb/s

Audio – 128 kb/s

###<a name="option-1-all-tracks-in-one-stream"></a>Option 1 : Toutes les pistes dans un flux de données

Dans cette option, un seul encodeur génère toutes les pistes audio/vidéo et les regrouper dans un seul MP4 fragmenté flux de bits qui obtient ensuite envoyé via une connexion HTTP POST unique. Dans cet exemple, il n'est qu’un seul flux de données pour cette présentation en direct :

![Image2][image2]

###<a name="option-2-each-track-in-a-separate-stream"></a>Option 2 : Chaque piste dans un flux distinct

Dans cette option, l’encoder(s) uniquement placer une effectuer le suivi dans chaque flux de bits MP4 Fragment et publiez tous les flux sur plusieurs connexions HTTP distinctes. Cela peut être effectué avec codeurs une ou plusieurs codeurs. Du point de vue de l’acquisition, cette présentation en direct est constituée de quatre flux.

![sa stratégie partenaires3][image3]

###<a name="option-3-bundle-audio-track-with-the-lowest-bitrate-video-track-into-one-stream"></a>Option 3 : Regrouper piste audio avec la piste vidéo débit les plus faibles dans un flux de données

Dans cette option, le client choisit regrouper la piste audio avec la piste vidéo débit les plus faibles dans un flux de bits MP4 Fragment et laisser les autres pistes vidéo deux chaque son propre flux en cours. 


![image4][image4]


###<a name="summary"></a>Résumé

Ce qui est affiché ci-dessus n’est pas une liste exhaustive de toutes les options de réception possibles pour cet exemple. En fait, n’importe quel regroupement de pistes dans le flux de données est pris en charge par la réception live. Clients et des fournisseurs de décodage d’éléments peuvent choisir leurs propre mises en œuvre basés sur la complexité ingénierie, capacité de décodage d’éléments et redondance et basculement considérations. Toutefois, il convient de noter que dans la plupart des cas, il n'est qu’une seule piste audio pour toute la présentation live aussi est-il important de s’assurer la santé du flux acquisition qui contient la piste audio. Cette considération souvent comme résultat placer piste audio dans son propre flux (comme dans l’Option 2) ou de regroupement avec la piste vidéo débit les plus faibles (comme dans l’Option 3). Également pour une meilleure redondance et la tolérance de panne, envoyer la même piste audio dans deux flux différents (Option 2 avec pistes audio redondants) ou regroupement la piste audio au moins deux des pistes vidéo débit les plus faibles (Option 3 son regroupées dans au moins deux flux vidéo) est fortement recommandée pour live d'acquisition dans Microsoft Azure Media Services.

##<a name="7-service-failover"></a>7. basculement de Service 

Nature en continu, en charge le basculement bonne est essentiel pour garantir la disponibilité du service. Microsoft Azure Media Services est conçu pour traiter les différents types d’échecs, y compris les erreurs de réseau, les erreurs de serveur, les problèmes de stockage, etc.. Lorsqu’il est utilisé conjointement avec une logique de basculement approprié à partir du bord encodeur live, client pouvez obtenir un service de diffusion en continu live hautement fiable à partir du cloud.

Dans cette section, nous allons étudier des scénarios de basculement de service. Dans ce cas, l’erreur se produit quelque part au sein du service et se manifeste comme une erreur réseau. Voici quelques recommandations pour la mise en œuvre encodeur adaptées à basculement de service :


1. Utiliser un délai d’expiration deuxième 10 pour établir la connexion TCP.  Si une tentative d’établir la connexion prend plue de 10 secondes, abandonner l’opération et essayez à nouveau. 
2. Utilisez un court délai d’expiration pour l’envoi de la requête HTTP segments de message.  Si la durée de fragment cible MP4 est N secondes, utilisez un délai d’émission entre N et 2 n secondes ; par exemple, utilisez un délai d’expiration de 6 à 12 secondes si la durée de fragment MP4 est de 6 secondes.  En cas d’expiration, rétablir la connexion, ouvrez une nouvelle connexion et reprendre le flux d’acquisition sur la nouvelle connexion. 
3. Mettre à jour un tampon propagée contenant deux derniers fragments, pour chaque heure, qui ont été correctement et complètement envoyés au service.  Si la requête HTTP POST pour un flux de données se termine ou arrive à expiration avant la fin du flux de données, ouvrir une nouvelle connexion et commencer une autre requête HTTP POST, renvoyer les en-têtes de flux, renvoyer les deux derniers fragments pour chaque piste et reprendre le flux de données sans introduire une discontinuité dans la chronologie du média.  Cela permet de réduire les risques de perte de données.
4. Il est recommandé que l’encodeur ne limite pas le nombre de tentatives pour établir une connexion ou reprendre la diffusion en continu après qu’une erreur TCP se produit.
5. Après une erreur TCP :
    1. La connexion en cours doit être fermée, et une nouvelle connexion doit être créée pour une nouvelle demande HTTP POST.
    2. Le nouveau HTTP POST URL doit être identique à l’URL du billet initial.
    3. Le nouveau HTTP POST doit inclure les en-têtes de flux de données (zone « ftyp », « Live de la zone serveur manifeste » et « moov ») identiques aux en-têtes de flux de données dans le billet initial.
    4. Les deux derniers fragments envoyés pour chaque piste doivent être renvoyés et diffusion en continu reprend sans introduire une discontinuité dans la chronologie du média.  Les horodatages fragment MP4 doivent augmenter en continu, y compris dans les requêtes HTTP POST.
6. L’encodeur doit se terminer la requête HTTP POST si les données ne sont pas envoyées à un rythme adapté à la durée de fragment MP4.  Une demande HTTP POST qui n’envoie pas de données, vous pouvez empêcher Azure Media Services de déconnecter rapidement l’encodeur en cas d’une mise à jour de service.  Pour cette raison, le billet HTTP pour incomplets pistes (signal ad) doivent être courte durée de vie, fin au plus tôt le fragment incomplet est envoyé.

##<a name="8-encoder-failover"></a>8. basculement encodeur

Le basculement encodeur est le deuxième type de scénario de basculement qui doit être résolu de bout en bout de diffusion en continu live. Dans ce scénario, la condition d’erreur s’est produite sur le côté de décodage d’éléments. 

![image5][image5]


Voici les attentes du point de terminaison réception live lorsque le basculement encodeur se produit :

1. Une nouvelle instance de décodage d’éléments doit être créée afin de pouvoir continuer la diffusion en continu, comme illustré dans le schéma ci-dessus (flux pour 3000 k vidéo par ligne en pointillés).
2. Nouvel encodeur doit utiliser la même URL pour les requêtes HTTP POST comme l’instance non réussie.
3. Demande de billet l’encodeur nouveau doit comporter les mêmes zones d’en-tête MP4 fragmentés que l’instance a échoué.
4. Nouvel encodeur doit être synchronisé correctement avec tous les autres codeurs en cours d’exécution pour la même présentation live pour générer des échantillons audio/vidéo synchronisées avec les limites de fragment alignées.
5. Nouveau flux de données doit être sémantique équivalent avec le flux précédente et interchangeable au niveau de l’en-tête et le fragment.
6. Nouvel encodeur essayez de limiter la perte de données.  La fragment_absolute_time et fragment_index des éléments multimédias fragments doivent augmenter à partir du point où l’encodeur arrêté dernière.  La fragment_absolute_time et fragment_index doivent augmenter de manière continue, mais il est possible de présenter une discontinuité si nécessaire.  Azure Media Services ignore fragments qu’il a déjà reçu et traitées, pourquoi il est préférable de d'où vient le problème renvoi fragments qu’à l’usage discontinuités dans la chronologie du média. 

##<a name="9-encoder-redundancy"></a>9. redondance encoder 

Les événements que plus grande disponibilité à la demande et la qualité de l’expérience, il est recommandé à employer encodeurs redondants actif pour obtenir un basculement transparent avec aucune perte de données en direct pour certains critique.

![image6][image6]

Comme illustré dans le schéma ci-dessus, il existe deux groupe des encodeurs reçue simultanément deux copies de chaque flux de données dans le service live. Cette configuration est prise en charge, car Microsoft Azure Media Services a la possibilité de filtrage des fragments en double en fonction de l’horodatage ID et fragment de flux de données. Le flux live résultant et l’archivage sera un simple de copie de tous les flux qui se trouve l’agrégation meilleures possible des deux sources. Par exemple, dans un cas extrême hypothétique, tant qu’il existe un encodeur (n’est soit la même que celle) en cours d’exécution à un moment donné dans le temps pour chaque flux de données, le flux résultant live à partir du service sera continu sans perte de données. 

La configuration minimale requise pour ce scénario est presque identique à la configuration requise en cas de basculement de décodage d’éléments à l’exception du second jeu de codeurs en cours d’exécution en même temps que les encodeurs principales.

##<a name="10-service-redundancy"></a>10. redondance des services  

Pour la distribution globale très redondante, il parfois doit avoir sauvegarde entre région pour gérer les incidents régionaux. Développement de la topologie « Encodeur redondance », clients la possibilité d’avoir un déploiement de service redondantes dans une zone différente qui est connectée à l’ensemble des encodeurs 2ème. Clients pourraient également travailler avec un fournisseur CDN pour déployer un lancement (Gestionnaire de trafic globale) devant les déploiements deux service en toute transparence acheminer le trafic du client. La configuration requise pour les encodeurs est identique cas « Encodeur redondance » avec la seule exception nécessitant le second jeu de codeurs de pointer vers un autre live d’acquisition point de terminaison. L’illustration ci-dessous montre cette configuration :

![image7][image7]

##<a name="11-special-types-of-ingestion-formats"></a>11. Types de Formats de réception spéciaux 

Cette section présente un type spécial de formats de réception live sont conçus pour gérer certains scénarios spécifiques.

###<a name="sparse-track"></a>Suivi des incomplet

Lors de l’exécution d’une présentation de diffusion en continu live avec l’expérience client riche, il est souvent nécessaire pour transmettre les événements synchronisés ou signale des intrabande avec les données multimédias principale. Un exemple est dynamique insertion annonces live. Ce type d’événement signalisation diffère de diffusion en continu, en raison de sa nature incomplet audio/vidéo normale. En d’autres termes, les données signalisation généralement ne sont pas lieu en permanence et l’intervalle peut être difficile de prédire. Le concept de piste incomplet a été conçu spécialement d’acquisition et de diffuser des données de signalisation intrabande.

Vous trouverez ci-dessous une implémentation recommandée pour traiter le suivi des incomplets :

1. Créer un distincte MP4 fragmenté bit-flux qui contient uniquement incomplets piste (s) sans pistes audio/vidéo.
2. Dans le « Live Server zone manifeste », telle que définie à la Section 6 [1], utilisez « parentTrackName » paramètre pour spécifier le nom de la piste parent. Reportez-vous à la section 4.2.1.2.1.2 [1] pour plus d’informations.
3. Dans le « Live Server zone manifeste », manifestOutput doit être définie sur « true ».
4. Étant donné la nature incomplet de l’événement signalisation, il est recommandé que :
    1. Au début de l’événement en direct, encodeur envoie les zones d’en-tête initiale dans le service qui permet au service pour enregistrer la piste incomplet dans le manifeste du client.
    2. L’encodeur doit se terminer la requête HTTP POST lorsque les données ne sont pas envoyées.  Une publication HTTP longue qui n’envoie pas de données peut empêcher Azure Media Services déconnecter rapidement l’encodeur en cas d’un redémarrage de serveur ou mise à jour de service, comme le serveur de médias est bloqué temporairement dans une opération de réception sur le socket. 
    3. Au moment où les données de signalisation ne sont pas disponibles, l’encodeur devrait fermer le billet HTTP demander.  Lorsque la demande POST est active, l’encodeur doit renvoyer des données 
    4. Lorsque vous envoyez fragments incomplets, encodeur pouvez définir explicite en-tête de longueur du contenu s’il est disponible.
    5. Lorsque vous envoyez fragment incomplet avec une nouvelle connexion, encodeur doit commencer à envoyer dans les zones d’en-tête suivis par les fragments de nouveau. Il s’agit de gérer le cas où basculement a eu lieu entre les deux et la nouvelle connexion incomplet est établie vers un nouveau serveur qui n’ont pas été vus la piste incomplet avant.
    6. Le fragment de suivi des incomplets seront disponible pour le client lorsque le parent correspondant effectuez le suivi de fragment qui a égale ou plus grande valeur d’horodatage est mis à disposition du client. Par exemple, si le fragment incomplet a un horodatage de t = 1000, il est prévu une fois que le client voit vidéo (en supposant que le nom du suivi des parent est vidéo) fragment horodatage 1000 ou éléments fondamentaux, il peut télécharger le fragment incomplet t = 1000. Veuillez noter que le signal réel pourrait parfaitement être utilisé pour un autre emplacement dans la chronologie de présentation pour son objectif désigné. Dans l’exemple ci-dessus, il est possible que le fragment incomplet de t = 1 000 a une charge XML qui est d’insertion d’une annonce dans une position qui est quelques secondes plus tard.
    7. La charge utile de fragment de suivi des incomplet peut se trouver dans différents formats (par exemple, XML ou du texte ou binaire, etc.) en fonction de différents scénarios. 


###<a name="redundant-audio-track"></a>Piste Audio redondant

Dans un diffusion adaptative HTTP scénario classique (par exemple, Smooth Streaming ou tiret), il est souvent qu’une seule piste audio dans toute la présentation. Contrairement aux pistes vidéo qui ont plusieurs niveaux de qualité pour le client pour choisir dans les conditions d’erreur, la piste audio peut être un point de défaillance unique si l’intégration du flux de données qui contient la piste audio est interrompue. 

Pour résoudre ce problème, Microsoft Azure Media Services prend en charge l’acquisition de pistes audio redondants. L’idée est que la même piste audio peut être envoyée plusieurs fois dans des flux différents. Tandis que le service uniquement enregistre la piste audio une fois dans le manifeste du client, il est en mesure d’utiliser redondants pistes audio en tant que sauvegardes d’extraction audio fragments si la piste audio principale rencontre des problèmes. Afin d’acquisition redondants pistes audio, l’encodeur doit :

1. Créer la même piste audio dans plusieurs flux Fragment MP4 binaires. Les pistes redondants doivent être sémantique équivalent avec exactement les même horodatages fragment et interchangeable au niveau de l’en-tête et le fragment.
2. Assurez-vous que le « son » entrée dans le Live Server manifeste (Section 6 [1]) être identique pour toutes les pistes audio redondants.

Voici une mise en œuvre recommandé pour les pistes audio redondants :

1. Envoyer chaque piste audio unique dans un flux de données en elle-même.  Envoyer également un flux de données redondante pour chacun de ces flux piste audio, où le flux 2ème diffère de la 1re uniquement par l’identificateur de l’URL du billet HTTP : {protocole}  :// {adresse du serveur} / {path}/Streams({identifier}) de point de publication.
2. Utilisez flux distincts pour envoyer les vitesses de transmission vidéo les plus faibles deux. Chacun de ces flux doit également contenir une copie de chaque piste audio unique.  Par exemple, lorsque plusieurs langues sont prises en charge, ces flux doit contenir pistes pour chaque langue.
3. Instances de serveur distinct (encoder) permet de coder et envoyer les flux de données redondantes mentionnées dans (1) et (2). 



##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png

 