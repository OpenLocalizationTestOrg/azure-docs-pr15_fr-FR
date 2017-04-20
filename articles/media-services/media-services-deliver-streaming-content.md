<properties 
    pageTitle="Publier des contenus Azure Media Services à l’aide de .NET" 
    description="Apprenez à créer un repère qui est utilisé pour générer une URL de diffusion en continu. Exemples de code sont écrits en c# et utilisent le Kit de développement de Services de support pour .NET." 
    authors="juliako" 
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
    ms.date="08/30/2016"
    ms.author="juliako"/>


# <a name="publish-azure-media-services-content-using-net"></a>Publier des contenus Azure Media Services à l’aide de .NET
 
> [AZURE.SELECTOR]
- [RESTE](media-services-rest-deliver-streaming-content.md)
- [.NET](media-services-deliver-streaming-content.md)
- [Portail](media-services-portal-publish.md)

##<a name="overview"></a>Vue d’ensemble

Vous pouvez diffuser un débit adaptive MP4 définir en créant un repère de diffusion en continu à la demande et création d’une URL de diffusion en continu. La rubrique [codage un bien](media-services-encode-asset.md) montre comment encoder dans un débit adaptive que MP4 définir. 

>[AZURE.NOTE]Si votre contenu est chiffré, configurez stratégie de remise de biens (comme décrit dans [cette](media-services-dotnet-configure-asset-delivery-policy.md) rubrique) avant de créer un repère. 

Vous pouvez également utiliser une à la demande en continu locator pour créer des URL pointant vers des fichiers MP4 pouvant être téléchargés progressivement.  

Cette rubrique montre comment créer une diffusion en continu locator afin de publier vos biens et créer un lisses, MPEG tiret et TLS URL de diffusion en continu à la demande. Il indique également cœur pour créer des URL de téléchargement progressive. 
     
##<a name="create-an-ondemand-streaming-locator"></a>Créer une demande de diffusion en continu locator

Pour créer la diffusion en continu locator à la demande et obtenir des URL, vous devez effectuer les opérations suivantes :

   1. Si le contenu est chiffré, définir une stratégie d’accès.
   2. Créer une demande de diffusion en continu locator.
   3. Si vous envisagez de flux, obtenir le fichier manifest diffusion en continu (.ism) dans l’actif. 
        
    Si vous souhaitez télécharger progressivement, obtenir les noms de fichiers MP4 dans l’actif.  
   4. Créer des URL pour l’ou les fichiers MP4 manifeste. 
   

###<a name="use-media-services-net-sdk"></a>Utiliser les Services de support .NET SDK 

Créer des URL de diffusion en continu 

    private static void BuildStreamingURLs(IAsset asset)
    {
    
        // Create a 30-day readonly access policy. 
        // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);
    
        // Create a locator to the streaming content on an origin. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));
    
        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();
    
        // Get a reference to the streaming manifest file from the  
        // collection of files in the asset. 
        var manifestFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                                    EndsWith(".ism")).
                                    FirstOrDefault();
        
        // Create a full URL to the manifest file. Use this for playback
        // in streaming media clients. 
        string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
        Console.WriteLine("URL to manifest for client streaming using Smooth Streaming protocol: ");
        Console.WriteLine(urlForClientStreaming);
        Console.WriteLine("URL to manifest for client streaming using HLS protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=m3u8-aapl)");
        Console.WriteLine("URL to manifest for client streaming using MPEG DASH protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=mpd-time-csf)"); 
        Console.WriteLine();
    }

Le code de sortie :
    
    URL to manifest for client streaming using Smooth Streaming protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest
    URL to manifest for client streaming using HLS protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)
    URL to manifest for client streaming using MPEG DASH protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)
    

>[AZURE.NOTE]Vous pouvez aussi diffuser votre contenu via une connexion SSL. Pour ce faire, assurez-vous que votre URL de diffusion en continu commencent par HTTPS. 

Créer des URL de téléchargement progressive 

    private static void BuildProgressiveDownloadURLs(IAsset asset)
    {
        // Create a 30-day readonly access policy. 
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);
    
        // Create an OnDemandOrigin locator to the asset. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));
    
        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();
    
        // Get MP4 files.
        IEnumerable<IAssetFile> mp4AssetFiles = asset
            .AssetFiles
            .ToList()
            .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));
                
        // Create a full URL to the MP4 files. Use this to progressively download files.
        foreach (var pd in mp4AssetFiles)
            Console.WriteLine(originLocator.Path + pd.Name);
    }

Le code de sortie :
    
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4
    
    . . . 

###<a name="use-media-services-net-sdk-extensions"></a>Utiliser des Extensions de Media Services .NET SDK

Le code suivant appelle les méthodes d’extensions .NET SDK créer un repère et génèrent Smooth Streaming, TLS et MPEG-tiret URL pour la diffusion adaptative.

    // Create a loctor.
    _context.Locators.Create(
        LocatorType.OnDemandOrigin,
        inputAsset,
        AccessPermissions.Read,
        TimeSpan.FromDays(30));
    
    // Get the streaming URLs.
    Uri smoothStreamingUri = inputAsset.GetSmoothStreamingUri();
    Uri hlsUri = inputAsset.GetHlsUri();
    Uri mpegDashUri = inputAsset.GetMpegDashUri();
    
    Console.WriteLine(smoothStreamingUri);
    Console.WriteLine(hlsUri);
    Console.WriteLine(mpegDashUri);


##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Voir aussi

[Télécharger des ressources](media-services-deliver-asset-download.md)
[définir les stratégies de remise de biens](media-services-dotnet-configure-asset-delivery-policy.md)
