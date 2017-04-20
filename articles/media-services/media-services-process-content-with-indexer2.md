<properties
    pageTitle="L’indexation de fichiers multimédias avec Azure Media indexeur 2 Preview | Microsoft Azure"
    description="Azure Media indexeur permet pour rendre le contenu de vos fichiers multimédias que vous pourrez rechercher et générer une transcription en texte intégral pour les légendes et mots clés. Cette rubrique indique comment utiliser des éléments multimédias indexeur 2 Preview."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/26/2016" 
    ms.author="adsolank;juliako;"/>


# <a name="indexing-media-files-with-azure-media-indexer-2-preview"></a>L’indexation de fichiers multimédias avec Azure Media indexeur 2 Preview

##<a name="overview"></a>Vue d’ensemble

Le processeur de média **Azure Media indexeur 2 Preview** (programme minimal) vous permet Vérifiez que vous pourrez rechercher du contenu et des fichiers multimédias, ainsi que de générer pistes sous-titrage fermés. Par rapport à la version précédente [d’Azure Media indexeur](media-services-index-content.md), **Azure Media indexeur 2 Preview** effectue l’indexation de la plus rapide et offre plus large prise en charge. Langues prises en charge comprennent anglais, espagnol, Français, allemand, italien, chinois, portugais et l’arabe.

Le panneau de gestion **Azure Media indexeur 2 Preview** est actuellement en mode Aperçu avant.

Cette rubrique indique comment créer des travaux d’indexation avec **Azure Media indexeur 2 Preview**.

>[AZURE.NOTE]Les considérations suivantes s’appliquent :
>
>Indexeur 2 n’est pas pris en charge dans Azure Chine et pour le gouvernement Azure.
>
>L’aperçu est limitée à environ 10 minutes de traitement, mais il est gratuit à tous les clients.
>
>Lors de l’indexation du contenu, vérifiez que vous utilisez des fichiers multimédias qui ont vocale très nette (sans musique en arrière-plan, le bruit, effets ou souffle microphone). Voici quelques exemples de contenu approprié : enregistré réunions, conférences ou présentations. Le contenu suivant peut-être ne pas être utile pour indexer : films, télévisées, quoi que ce soit avec mixte audio et des effets sonores, du contenu avec le bruit de fond (souffle) mal enregistrement.


Cette rubrique fournit des détails sur **Azure Media indexeur 2 Preview** et montre comment l’utiliser avec Media Services SDK pour .NET

##<a name="input-and-output-files"></a>Fichiers d’entrée et de sortie

###<a name="input-files"></a>Fichiers d’entrée

Fichiers audio ou vidéo

###<a name="output-files"></a>Fichiers de sortie

Une opération d’indexation peut générer des sous-titres fichiers aux formats suivants :  

- **Lapon**
- **TTML**
- **WebVTT**

Fermé légende (CC) fichiers aux formats suivants peuvent être utilisés pour rendre les fichiers audio et vidéo accessible aux personnes présentant un handicap Audition.

##<a name="task-configuration-preset"></a>Configuration d’une tâche (prédéfini)

Lorsque vous créez une tâche d’indexation avec **Azure Media indexeur 2 Preview**, vous devez spécifier un configuration prédéfini.

Le JSON suivant définit les paramètres disponibles.

    {
      "version":"1.0",
      "Features":
        [
           {
           "Options": {
                "Formats":["WebVtt","ttml"],
                "Language":"enUs",
                "Type":"RecoOptions"
           },
           "Type":"SpReco"
        }]
    }

##<a name="supported-languages"></a>Langues prises en charge  

Azure Media indexeur 2 Preview prend en charge pour texte par synthèse vocale pour les langues suivantes (lorsque vous spécifiez le nom de la langue dans la configuration d’une tâche, utiliser le code de 4 caractères entre crochets, comme illustré ci-dessous) :

- Anglais [EnUs]
- Espagnol [EsEs]
- Chinois [ZhCn]
- Français [FrFr]
- Allemand [DeDe]
- Italien [ItIt]
- Portugais [PtBr]
- Arabe (égyptien) [ArEg]


## <a name="sample-code"></a>Exemples de code

Les éléments suivants du programme indique comment :

1. Créer un bien et télécharger un fichier multimédia dans la ressource.
1. Crée une tâche avec une tâche d’indexation basée sur un fichier de configuration qui contient la prédéfini json suivant.
            
        {
          "version":"1.0",
          "Features":
            [
               {
               "Options": {
                    "Formats":["WebVtt","ttml"],
                    "Language":"enUs",
                    "Type":"RecoOptions"
               },
               "Type":"SpReco"
            }]
        }

1. Télécharge les fichiers de sortie. 
    
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace IndexContent
        {
            class Program
            {
                // Read values from the App.config file.
                private static readonly string _mediaServicesAccountName =
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string _mediaServicesAccountKey =
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];
        
                // Field for service context.
                private static CloudMediaContext _context = null;
                private static MediaServicesCredentials _cachedCredentials = null;
        
                static void Main(string[] args)
                {
        
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    _mediaServicesAccountName,
                                    _mediaServicesAccountKey);
                    // Used the cached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
        
                    // Run indexing job.
                    var asset = RunIndexingJob(@"C:\supportFiles\Indexer\BigBuckBunny.mp4",
                                                @"C:\supportFiles\Indexer\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\Indexer\Output");
                }
        
                static IAsset RunIndexingJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Indexing Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Indexing Job");
        
                    // Get a reference to Azure Media Indexer 2 Preview.
                    string MediaProcessorName = "Azure Media Indexer 2 Preview";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Indexing Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset to be indexed.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);
        
                    // Use the following event handler to check job progress.  
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
        
                    // Launch the job.
                    job.Submit();
        
                    // Check job execution and wait for job to finish.
                    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        
                    progressJobTask.Wait();
        
                    // If job state is Error, the event handling
                    // method for job progress should log errors.  Here we check
                    // for error state and exit if needed.
                    if (job.State == JobState.Error)
                    {
                        ErrorDetail error = job.Tasks.First().ErrorDetails.First();
                        Console.WriteLine(string.Format("Error: {0}. {1}",
                                                        error.Code,
                                                        error.Message));
                        return null;
                    }
        
                    return job.OutputMediaAssets[0];
                }
        
                static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
                {
                    IAsset asset = _context.Assets.Create(assetName, options);
        
                    var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                    assetFile.Upload(filePath);
        
                    return asset;
                }
        
                static void DownloadAsset(IAsset asset, string outputDirectory)
                {
                    foreach (IAssetFile file in asset.AssetFiles)
                    {
                        file.Download(Path.Combine(outputDirectory, file.Name));
                    }
                }
        
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
        
                static private void StateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine("Job state changed event:");
                    Console.WriteLine("  Previous state: " + e.PreviousState);
                    Console.WriteLine("  Current state: " + e.CurrentState);
        
                    switch (e.CurrentState)
                    {
                        case JobState.Finished:
                            Console.WriteLine();
                            Console.WriteLine("Job is finished.");
                            Console.WriteLine();
                            break;
                        case JobState.Canceling:
                        case JobState.Queued:
                        case JobState.Scheduled:
                        case JobState.Processing:
                            Console.WriteLine("Please wait...\n");
                            break;
                        case JobState.Canceled:
                        case JobState.Error:
                            // Cast sender as a job.
                            IJob job = (IJob)sender;
                            // Display or log error details as needed.
                            // LogJobStop(job.Id);
                            break;
                        default:
                            break;
                    }
                }
            }
        }


##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


## <a name="related-links"></a>Liens connexes

[Azure Media Services Analytique vue d’ensemble](media-services-analytics-overview.md)

[Azure démonstrations multimédias Analytique](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)