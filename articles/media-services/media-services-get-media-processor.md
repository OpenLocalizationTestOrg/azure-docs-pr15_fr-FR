<properties 
    pageTitle="Comment créer un processeur média | Microsoft Azure" 
    description="Découvrez comment créer un composant de traitement multimédia pour encoder, convertir le format, chiffrer ou déchiffrer du contenu multimédia pour Azure Media Services. Exemples de code sont écrits en c# et utilisent le Kit de développement de Services de support pour .NET." 
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
    ms.date="09/26/2016" 
    ms.author="juliako"/>


#<a name="how-to-get-a-media-processor-instance"></a>Comment : obtenir une Instance de processeur multimédia

> [AZURE.SELECTOR]
- [.NET](media-services-get-media-processor.md)
- [RESTE](media-services-rest-get-media-processor.md)


##<a name="overview"></a>Vue d’ensemble

Dans Media Services qu'un processeur média est un composant qui gère une tâche de traitement spécifiques, telles que le codage, mettre en forme de conversion, le chiffrement ou déchiffrement du contenu multimédia. Vous créez généralement un processeur média lorsque vous créez une tâche à coder, chiffrez ou convertir le format du contenu multimédia.

Le tableau suivant fournit le nom et la description de chaque processeur média disponible.

Nom du processeur multimédia|Description|Plus d’informations
---|---|---
Standard de décodage d’éléments multimédias|Fournit les fonctionnalités standards pour le codage à la demande. |[Vue d’ensemble et de comparaison de Azure sur codeurs Media à la demande](media-services-encode-asset.md)
Flux de travail Media Encoder Premium|Vous permet d’exécuter des tâches d’encodage à l’aide de flux de travail Media Encoder Premium.|[Vue d’ensemble et de comparaison de Azure sur codeurs Media à la demande](media-services-encode-asset.md)
Indexer de Media Azure| Permet de rendre disponible pour la recherche du contenu et des fichiers multimédias, ainsi que de générer pistes sous-titrage fermés et des mots clés.|[Indexer de Media Azure](media-services-index-content.md)
Azure Media Hyperlapse (preview)|Vous permet d’atténuer les « bosses » dans votre vidéo avec stabilisation vidéo. Vous permet également d’accélérer votre contenu dans un élément consommable.|[Azure Media Hyperlapse](media-services-hyperlapse-content.md)
Azure Media Encoder|Amortissement
Déchiffrement de stockage| Amortissement|
Gestionnaire de médias Azure|Amortissement|
Azure Media chiffreur|Amortissement|

##<a name="get-media-processor"></a>Obtenir processeur média

La méthode suivante montre comment obtenir une instance de processeur multimédia. L’exemple de code suppose que l’utilisation d’une variable au niveau du module nommée **_context** pour référencer le contexte de serveur comme décrit dans la section [Comment : se connecter à Media Services par programme](media-services-dotnet-connect-programmatically.md).

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
        
        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
        
        return processor;
    }


##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment obtenir une instance de processeur média, accédez à la rubrique [comment encoder un bien](media-services-dotnet-encode-with-media-encoder-standard.md) qui vous montrent comment utiliser la norme de décodage d’éléments multimédias à encoder un bien.


