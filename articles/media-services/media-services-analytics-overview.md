<properties
    pageTitle="Vue d’ensemble Analytique des Services Azure Media | Microsoft Azure"
    description="Azure Media Services offre la version d’évaluation d’Azure Media Analytique, un ensemble de services de vision vocale et de l’ordinateur à l’échelle de l’entreprise, la conformité, sécurité et envergure à. Azure Media Analytique services sont créées à l’aide des composants principaux de la plateforme Azure Media Services et sont donc prêts à traiter les supports de traitement à l’échelle sur dès le premier jour. "
    services="media-services"
    documentationCenter=""
    authors="juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/24/2016"   
    ms.author="milanga;juliako;johndeu"/>

# <a name="azure-media-services-analytics-overview"></a>Azure Media Services Analytique vue d’ensemble

##<a name="overview"></a>Vue d’ensemble

Autres organisations et les entreprises adoptent vidéo comme support préféré pour former leurs employés, recours à une de ses clients et les fonctions d’entreprise document. Cloud computing rend efficace pour stocker, de flux et accéder à ces fichiers multimédia volumineux, mais comme sociétés agrandir leur bibliothèque de contenu vidéo, ils doivent avoir un moyen également efficace pour extraction de nouvelles analyses à partir de la vidéo afin de créer plus significatifs, personnalisée interactions avec leurs audiences et suivre leurs activités pour un niveau avancé.

Pour répondre à ce besoin croissant sur le marché, Azure Media Services offre Media Analytique, un ensemble de la reconnaissance vocale et vision composants (à l’échelle de l’entreprise, la conformité, sécurité et envergure) afin de faciliter pour les entreprises et les entreprises à tirer des leçons exploitables à partir de leurs fichiers vidéo. Azure Media Analytique services sont créées à l’aide des composants principaux de la plateforme Azure Media Services et sont donc prêts à traiter les supports de traitement à l’échelle sur dès le premier jour.

Azure Media Analytique permettent aux développeurs de commencer à utiliser les fonctionnalités de vision pour vidéo à l’échelle limitée rapidement et accédez à cette fonctionnalité avancée dans les applications. Azure Media Analytique est conçu pour être utilisé par les environnements d’entreprise avec l’échelle complète, conformité, sécurité et envergure requis par les grandes entreprises.

Le diagramme suivant illustre **Analytique multimédia** et autres composants principaux de la plateforme Media Services. 

![Flux de travail VoD](./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png)

Processeurs multimédia Media Analytique produisent des fichiers MP4 ou JSON. Si un processeur média produit un fichier MP4, vous pouvez progressivement télécharger le fichier. Si un processeur média a donné un fichier JSON, vous pouvez télécharger le fichier à partir du stockage blob Azure. 

## <a name="azure-media-analytics-services"></a>Services de support Analytique Azure

- **Indexeur** – Azure Media indexeur permet de rendre le contenu disponible pour la recherche, ainsi que de générer pistes sous-titrage fermés. Azure Media Services publié **Azure Media indexeur 2 Preview** prenant en charge le plus rapide d’indexation et plus large. Langues prises en charge comprennent anglais, espagnol, Français, allemand, italien, chinois, portugais et l’arabe. Pour plus d’informations et d’exemples, voir les [vidéos de processus avec Azure Media indexeur 2](media-services-process-content-with-indexer2.md)
 
- **Hyperlapse** – Hyperlapse Microsoft est un résultat de recherche de vision ordinateur auprès de Microsoft Research (MSR) combinant stabilisation video et heure origine pour créer des vidéos rapides, consommables superbes à partir de votre forme longue contenu de plus de 20 ans. Outre des délais, vous pouvez également utiliser Hyperlapse pour créer des vidéos stables à partir de vidéos tremblante capturées à l’aide des téléphones et caméras. Pour plus d’informations et d’exemples, consultez [Hyperlapse de fichiers multimédia avec Azure Media Hyperlapse](media-services-hyperlapse-content.md)
 
- **Détection de mouvement** – vous pouvez utiliser ce service pour la détection de mouvement dans une vidéo avec un arrière-plan de papier à lettres. Il s’agit idéal pour les clients qui souhaitent vérifier pour fausses sur les événements de mouvement détectés par caméras de surveillance sur les flux vidéo surveillance. Pour plus d’informations et d’exemples, voir [Détection de mouvement pour Azure Media Analytique](media-services-motion-detection.md).
 
- **La détection de visage et émotions nominale** – à l’aide de ce service, vous pouvez détecter faces populaire et leurs émotions, y compris bonheur, leur, surprise, anger, contempt, peur, sentira et indifférence/neutre. Cela a plusieurs industrie utile applications, décrites ci-dessous, y compris agrégation et l’analyse des effets de personnes participant à un événement. Pour plus d’informations et d’exemples, voir [nominale et détection émotions pour Azure Media Analytique](media-services-face-and-emotion-detection.md).
 
- **Synthèse vidéo** – synthèse vidéo peut vous aider à créer des synthèses des vidéos longues en sélectionnant automatiquement extraits intéressantes à partir de la vidéo source. Cela est utile lorsque vous souhaitez fournir un bref aperçu à quoi s’attendre dans une vidéo longue. Pour plus d’informations et d’exemples, voir [Utiliser Azure Media Video les miniatures pour créer une synthèse vidéo](media-services-video-summarization.md)

- **Reconnaissance optique des caractères** - Azure Media Analytique OCR (reconnaissance optique des caractères) permet de contenu de texte dans les fichiers vidéo pour convertir en texte numérique modifiable, que vous pourrez rechercher. Cela vous permet d’automatiser l’extraction de métadonnées significative de signal vidéo de votre support.
 
- **Rédaction nominale Scalable** - **Azure Media Redactor** est un Azure Media Analytique Pack d’administration qui offre rédaction scalable nominale dans le cloud. Rédaction nominale vous permet de modifier votre vidéo afin de flou faces à des personnes choisies. Vous souhaiterez peut-être utiliser le service de rédaction nominale dans des scénarios de sécurité des messages et éléments multimédias de news public. Quelques minutes de film qui contient plusieurs faces peuvent nécessiter des heures pour procéder manuellement, mais grâce à ce service le processus de rédaction nominale nécessite quelques étapes simples. Pour plus d’informations, voir [cet](media-services-face-redaction.md) article.

 
## <a name="common-scenarios"></a>Scénarios courants

Vous trouverez ci-dessous quelques scénarios dans lesquels Azure Media Analytique peut aider les organisations et les entreprises secteurs déduire nouvelles analyses à partir de la vidéo pour créer plus personnalisé public et engagements employé, ainsi que gérer plus efficacement grand volume de contenu vidéo :

- **Centres d’appels** – pair avec l’apparition de médias sociaux, appel client centres de facilitent toujours une grande partie des transactions service client. Codé dans ces données audio est une mine d’informations sur les clients qui peut être analysé pour améliorer les produits et également former les employés du centre d’appel pour obtenir la satisfaction client par le plus élevé. À l’aide de Indexer de Media Azure, les clients sont en mesure d’extraire le texte et créer un index de recherche et des tableaux de bord pour extraire intelligence autour de la plus courante se plaint, source des se plaint et autres ces données pertinentes.

- **Modération du contenu généré par l’utilisateur** – à partir de médias prises aux départements de police, de nombreuses organisations ont publics portails qui fait face vers l’endroit où ils acceptent multimédias UGC, tels que les vidéos et d’images. Le volume de contenu peut PIC en raison d’événements inattendus. Dans ces scénarios, il est presque impossible d’effectuer une analyse manuelle efficace du contenu de manière correcte. Clients peuvent compter sur le service modération du contenu pour se concentrer sur le contenu approprié.

- **Surveillance** - avec la croissance de caméras IP, il existe une explosion de vidéos de surveillance. Examen manuel de vidéo de surveillance est long et propice aux erreurs humaines. Azure Media Analytique propose plusieurs composants tels que détection de mouvement et la détection de visage Hyperlapse pour rendre le processus de révision, de gestion et de création dérivés plus facilement.

## <a name="media-services-analytics-media-processors"></a>Media Services Analytique Media processeurs 

Cette section répertorie tous les éléments multimédias Services Analytique Media processeurs (Panneau de gestion) et montre comment utiliser .NET ou reste pour obtenir un objet de point de gestion.

### <a name="mp-names"></a>Noms de point de gestion


- Azure Media indexeur 2 Preview
- Indexer de Media Azure
- Azure Media Hyperlapse
- Détection de visage Azure Media
- Détection de mouvement Azure Media
- Miniatures de vidéo Azure Media
- Reconnaissance optique des caractères Azure Media

### <a name="net"></a>.NET

La fonction suivante prend un des noms de point de gestion spécifiées et retourner un objet de point de gestion.

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
            .Where(p => p.Name == mediaProcessorName)
            .ToList()
            .OrderBy(p => new Version(p.Version))
            .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }


## <a name="rest"></a>RESTE

Demande :

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20OCR' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.12
    Host: media.windows.net
    
Réponse :
        
    . . .
    
    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:074c3899-d9fb-448f-9ae1-4ebcbe633056",
             "Description":"Azure Media OCR",
             "Name":"Azure Media OCR",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

##<a name="demos"></a>Démonstrations

[Azure démonstrations multimédias Analytique](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

##<a name="next-steps"></a>Étapes suivantes

Passez en revue Media Services rubriques d’apprentissage professionnelles.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="related-articles"></a>Articles connexes

[Annonce Media Services Analytique](https://azure.microsoft.com/blog/introducing-azure-media-analytics/)
  

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
