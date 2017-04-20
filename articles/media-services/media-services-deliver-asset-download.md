<properties 
    pageTitle="Téléchargement d’éléments multimédias" 
    description="Obtenir des informations sur le point de télécharger des ressources sur votre ordinateur. Exemples de code sont écrits en c# et utilisent le Kit de développement de Services de support pour .NET." 
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

#<a name="how-to-deliver-an-asset-by-download"></a>Comment : effectuer un bien par téléchargement

Cette rubrique décrit les options permettant de biens multimédias téléchargés dans Media Services. Vous pouvez fournir un contenu Media Services dans nombreux scénarios d’applications. Vous pouvez télécharger des biens multimédias, ou y accéder à l’aide d’un repère. Vous pouvez envoyer du contenu multimédia dans une autre application ou à un autre fournisseur de contenu. Pour améliorer les performances et extensibilité élevées, vous pouvez également fournir un contenu à l’aide d’un réseau de remise de contenu (CDN).

Cet exemple montre comment télécharger des biens multimédias sur Media Services sur votre ordinateur local. Le code de requêtes les travaux associés au compte de Services de support par identificateur de tâche et accède à sa collection **OutputMediaAssets** (ce qui correspond à l’ensemble d’une ou plusieurs ressources de support sortie qui résulte de l’exécution d’une tâche). Cet exemple montre comment télécharger des éléments multimédias de sortie à partir d’une tâche, mais vous pouvez appliquer la même approche pour télécharger d’autres éléments.

    
    // Download the output asset of the specified job to a local folder.
    static IAsset DownloadAssetToLocal( string jobId, string outputFolder)
    {
        // This method illustrates how to download a single asset. 
        // However, you can iterate through the OutputAssets
        // collection, and download all assets if there are many. 
    
        // Get a reference to the job. 
        IJob job = GetJob(jobId);
    
        // Get a reference to the first output asset. If there were multiple 
        // output media assets you could iterate and handle each one.
        IAsset outputAsset = job.OutputMediaAssets[0];
    
        // Create a SAS locator to download the asset
        IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
        ILocator locator = _context.Locators.CreateLocator(LocatorType.Sas, outputAsset, accessPolicy);
    
        BlobTransferClient blobTransfer = new BlobTransferClient
        {
            NumberOfConcurrentTransfers = 20,
            ParallelTransferThreadCount = 20
        };
    
        var downloadTasks = new List<Task>();
        foreach (IAssetFile outputFile in outputAsset.AssetFiles)
        {
            // Use the following event handler to check download progress.
            outputFile.DownloadProgressChanged += DownloadProgress;
    
            string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);
    
            Console.WriteLine("File download path:  " + localDownloadPath);
    
            downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));
    
            outputFile.DownloadProgressChanged -= DownloadProgress;
        }
    
        Task.WaitAll(downloadTasks.ToArray());
    
        return outputAsset;
    }
    
    static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
    {
        Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
    }



##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

   
##<a name="see-also"></a>Voir aussi 

[Fournir un contenu de diffusion en continu](media-services-deliver-streaming-content.md)

