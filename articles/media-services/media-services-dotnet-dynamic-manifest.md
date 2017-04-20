<properties 
    pageTitle="Création de filtres avec Azure Media Services SDK .NET" 
    description="Cette rubrique explique comment créer des filtres afin de votre client de les utiliser à des sections spécifiques d’un flux de flux de données. Media Services crée manifestes dynamiques pour obtenir ce flux sélective." 
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
    ms.date="07/18/2016"
    ms.author="juliako;cenkdin"/>


#<a name="creating-filters-with-azure-media-services-net-sdk"></a>Création de filtres avec Azure Media Services SDK .NET

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-dynamic-manifest.md)
- [RESTE](media-services-rest-dynamic-manifest.md)

Commençant par la version 2.11, Media Services permet de définir des filtres pour vos ressources. Ces filtres sont des règles côté serveur qui permettent de choisir de le faire des éléments tels que vos clients : lecture uniquement une section d’une vidéo (au lieu de la vidéo entière), ou spécifier uniquement un sous-ensemble de formats audio et vidéo qui périphérique de votre client peut gérer (au lieu de tous les formats qui sont associés à l’actif). Ce filtrage de vos ressources par le biais s **Manifeste dynamique**créés à la demande de votre client en continu d’une vidéo sur les filtres spécifiés.

Pour plus d’informations liées à des filtres et manifeste dynamique, voir [vue d’ensemble des manifestes dynamique](media-services-dynamic-manifest-overview.md).

Cette rubrique indique comment utiliser Media Services .NET SDK pour créer, mettre à jour et supprimer des filtres. 


Remarque Si vous mettez à jour un filtre, il peut prendre jusqu'à 2 minutes de diffusion en continu de point de terminaison pour actualiser les règles. Si le contenu a été pris en charge à l’aide de ce filtre (et mis en cache dans les proxys et CDN cache), mise à jour de ce filtre peut entraîner des erreurs de lecteur. Il est recommandé pour vider le cache après mise à jour le filtre. Si cette option n’est pas possible, envisagez d’utiliser un filtre différent. 

##<a name="types-used-to-create-filters"></a>Types d’utilisés pour créer des filtres

Les types suivants sont utilisés lorsque vous créez des filtres : 

- **IStreamingFilter**.  Ce type est basé sur l' API REST suivant [filtre](http://msdn.microsoft.com/library/azure/mt149056.aspx)
- **IStreamingAssetFilter**. Ce type est basé sur l' API REST suivant [AssetFilter](http://msdn.microsoft.com/library/azure/mt149053.aspx)
- **PresentationTimeRange**. Ce type est basé sur l' API REST suivant [PresentationTimeRange](http://msdn.microsoft.com/library/azure/mt149052.aspx)
- **FilterTrackSelectStatement** et **IFilterTrackPropertyCondition**. Ces types sont basés sur l’API REST suivants [FilterTrackSelect et FilterTrackPropertyCondition](http://msdn.microsoft.com/library/azure/mt149055.aspx)


##<a name="createupdatereaddelete-global-filters"></a>Créer/mettre à jour/en lecture/supprimer les filtres globaux

Le code suivant montre comment utiliser .NET pour créer, mettre à jour, lisez et supprimer des filtres actifs.
    
    string filterName = "GlobalFilter_" + Guid.NewGuid().ToString();
                
    List<FilterTrackSelectStatement> filterTrackSelectStatements = new List<FilterTrackSelectStatement>();
    
    FilterTrackSelectStatement filterTrackSelectStatement = new FilterTrackSelectStatement();
    filterTrackSelectStatement.PropertyConditions = new List<IFilterTrackPropertyCondition>();
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackNameCondition("Track Name", FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackBitrateRangeCondition(new FilterTrackBitrateRange(0, 1), FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackTypeCondition(FilterTrackType.Audio, FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatements.Add(filterTrackSelectStatement);
    
    // Create
    IStreamingFilter filter = _context.Filters.Create(filterName, new PresentationTimeRange(), filterTrackSelectStatements);
    
    // Update
    filter.PresentationTimeRange = new PresentationTimeRange(timescale: 500);
    filter.Update();
    
    // Read
    var filterUpdated = _context.Filters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filter.Delete();


##<a name="createupdatereaddelete-asset-filters"></a>Filtres de biens créer/mise à jour/en lecture/supprimer

Le code suivant montre comment utiliser .NET pour créer, mettre à jour, lisez et supprimer des filtres actifs.

    
    string assetName = "AssetFilter_" + Guid.NewGuid().ToString();
    var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);
    
    string filterName = "AssetFilter_" + Guid.NewGuid().ToString();
    
        
    // Create
    IStreamingAssetFilter filter = asset.AssetFilters.Create(filterName,
                                        new PresentationTimeRange(), 
                                        new List<FilterTrackSelectStatement>());
    
    // Update
    filter.PresentationTimeRange = 
            new PresentationTimeRange(start: 6000000000, end: 72000000000);
    
    filter.Update();
    
    // Read
    asset = _context.Assets.Where(c => c.Id == asset.Id).FirstOrDefault();
    var filterUpdated = asset.AssetFilters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);
    
    // Delete
    filterUpdated.Delete();
    



##<a name="build-streaming-urls-that-use-filters"></a>Créer la diffusion en continu les URL qui utilisent des filtres

Pour plus d’informations sur la façon de publier et présenter vos biens, voir [Exécution du contenu à la vue d’ensemble des clients](media-services-deliver-content-overview.md).


Les exemples ci-dessous illustrent comment ajouter des filtres à votre URL de diffusion en continu.

**TIRET MPEG** 

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live en continu V4 (TLS)**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live en continu V3 (TLS)**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Diffusion en continu lisse**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)


**HDS**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f, filter=MyFilter)


##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="see-also"></a>Voir aussi 

[Vue d’ensemble des manifestes dynamique](media-services-dynamic-manifest-overview.md)
 

