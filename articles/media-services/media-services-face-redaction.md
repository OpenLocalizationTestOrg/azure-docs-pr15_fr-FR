<properties
    pageTitle="Confronté rédaction avec analytique Azure media | Microsoft Azure"
    description="Cette rubrique montre comment procéder faces avec analytique Azure media."
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
    ms.date="09/12/2016"   
    ms.author="juliako;"/>
 
#<a name="face-redaction-with-azure-media-analytics"></a>Rédaction nominale avec analytique Azure media

##<a name="overview"></a>Vue d’ensemble

**Azure Media Redactor** est un processeur de média [Azure Media Analytique](media-services-analytics-overview.md) (programme minimal) qui propose rédaction scalable nominale dans le cloud. Rédaction nominale vous permet de modifier votre vidéo afin de flou faces à des personnes choisies. Vous souhaiterez peut-être utiliser le service de rédaction nominale dans des scénarios de sécurité des messages et éléments multimédias de news public. Quelques minutes de film qui contient plusieurs faces peuvent nécessiter des heures pour procéder manuellement, mais grâce à ce service le processus de rédaction nominale nécessite quelques étapes simples. Pour plus d’informations, voir [ce](https://azure.microsoft.com/blog/azure-media-redactor/) billet de blog.

Cette rubrique fournit des détails sur **Azure Media Redactor** et montre comment l’utiliser avec Media Services SDK pour .NET.

Le panneau de gestion **Azure Media Redactor** est en cours d’aperçu.

## <a name="face-redaction-modes"></a>Modes de rédaction face

Visage rédaction fonctionne par détection de visages dans chaque image de la vidéo et le suivi de l’objet face à la fois avant et en arrière dans le temps, afin que la même personne peut être floue ainsi que d’autres angles. Le processus de rédaction automatisé est très complexe et ne fournit pas de toujours produire 100 % de sortie souhaité, c’est pourquoi Media Analytique vous avec de deux façons de modifier le résultat final.

En plus d’un mode entièrement automatique, est un flux de travail deux phases qui permet de la sélection/désérialiser-selection de faces trouvés via une liste d’ID. En outre, pour rendre arbitraire par ajustements cadre du Panneau de gestion utilise un fichier de métadonnées au format JSON. Ce flux de travail est divisé en modes **analyse** et **Redact** . Vous pouvez combiner les deux modes dans un seul passage qui s’exécute les deux tâches en tâches ; Ce mode est appelé **combinées**.

###<a name="combined-mode"></a>Mode combiné

Vous obtiendrez un mp4 rédigé automatiquement sans n’importe quel manuel d’entrée.

Étape|Nom de fichier|Notes
---|---|---
Biens d’entrée|foo.Bar|Vidéo au format WMV, MOV ou MP4
Configuration de la saisie|Configuration du travail prédéfinis|{'version' :'1.0 ', 'options' : {'mode' : « combinée »}}
Biens de sortie|foo_redacted.mp4|Vidéo avec flou appliqué

####<a name="input-example"></a>Exemple d’entrée :

[Regardez cette vidéo](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fed99001d-72ee-4f91-9fc0-cd530d0adbbc%2FDancing.mp4)

####<a name="output-example"></a>Exemple de sortie :

[Regardez cette vidéo](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc6608001-e5da-429b-9ec8-d69d8f3bfc79%2Fdance_redacted.mp4)

###<a name="analyze-mode"></a>Mode d’analyse

Le test du flux de travail deux passages **analyser** prend une entrée vidéo et génère un fichier JSON des emplacements nominale et images jpg de chaque face détecté.

Étape|Nom de fichier|Notes
---|---|----
Biens d’entrée|foo.Bar|Vidéo au format WMV, MPV ou MP4
Configuration de la saisie|Configuration du travail prédéfinis|{'version' :'1.0 ', 'options' : {'mode' : 'analyser'}}
Biens de sortie|foo_annotations.JSON|Données d’annotation des emplacements face au format JSON. Cela peut être modifié par l’utilisateur à modifier le flou cadres. Voir les exemples ci-dessous.
Biens de sortie|foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg]|Rognage jpg de chaque détecté nominale, où le nombre indique le labelId de la face

####<a name="output-example"></a>Exemple de sortie :

    {
      "version": 1,
      "timescale": 50,
      "offset": 0,
      "framerate": 25.0,
      "width": 1280,
      "height": 720,
      "fragments": [
        {
          "start": 0,
          "duration": 2,
          "interval": 2,
          "events": [
            [  
              {
                "id": 1,
                "x": 0.306415737,
                "y": 0.03199235,
                "width": 0.15357475,
                "height": 0.322126418
              },
              {
                "id": 2,
                "x": 0.5625317,
                "y": 0.0868245438,
                "width": 0.149155334,
                "height": 0.355517566
              }
            ]
          ]
        },

… tronqué


###<a name="redact-mode"></a>Procéder Mode

La deuxième étape du flux de travail prend un plus grand nombre d’entrées qui doivent être combinées dans un seul élément.

Cela inclut une liste des ID de flou, la vidéo d’origine et les annotations JSON. Ce mode utilise les annotations pour appliquer le flou sur l’entrée vidéo.

Le résultat de la phase d’analyse n’inclut pas de la vidéo d’origine. La vidéo doit être téléchargés dans les biens d’entrée de la tâche en mode Redact et sélectionné comme fichier principal.

Étape|Nom de fichier|Notes
---|---|---
Biens d’entrée|foo.Bar|Vidéo au format WMV, MPV ou MP4. Même image vidéo comme à l’étape 1.
Biens d’entrée|foo_annotations.JSON|fichier de métadonnées annotations à partir de la première phase, avec les modifications facultatives.
Biens d’entrée|foo_IDList.txt (facultatif)|Liste de face ID de procéder séparés par facultatif nouvelle ligne. Si laissé vide, il applique un flou toutes les faces.
Configuration de la saisie|Configuration du travail prédéfinis|{'version' :'1.0 ', 'options' : {'mode' : « procéder »}}
Biens de sortie|foo_redacted.mp4|Vidéo avec flou appliqué sur les annotations

####<a name="example-output"></a>Exemple de sortie

Il s’agit de la sortie à partir d’un conjointe avec un code sélectionné.

[Regardez cette vidéo](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fad6e24a2-4f9c-46ee-9fa7-bf05e20d19ac%2Fdance_redacted1.mp4)

##<a name="attribute-descriptions"></a>Descriptions des attributs

Le panneau de gestion de rédaction offre une grande précision nominale emplacement détection et suivi qui peut détecter jusqu'à 64 faces humaines dans un cadre de la vidéo. Faces frontales fournissent les meilleurs résultats, tout en face et petites faces (inférieure ou égale à 24 24 pixels) sont difficiles.

Les faces détectés et suivis sont retournés avec des coordonnées indiquant l’emplacement de faces, mais aussi une face numéro d’identification indiquant que le suivi de cette personne. Numéros d’identification nominale sont propice aux réinitialiser dans des circonstances lorsque la face frontale est perdue ou qui se chevauchent dans le cadre, ce qui crée de certaines personnes prise attribuées plusieurs codes.

Pour obtenir une description détaillée des attributs, voir rubrique [nominale détecter et les émotions avec Azure Media Analytique](media-services-face-and-emotion-detection.md) .

## <a name="sample-code"></a>Exemples de code

Les éléments suivants du programme indique comment :

1. Créer un bien et télécharger un fichier multimédia dans la ressource.
1. Créer une tâche avec une tâche de rédaction nominale basée sur un fichier de configuration qui contient la prédéfini json suivant. 
                    
        {'version':'1.0', 'options': {'mode':'combined'}}

1. Téléchargez les fichiers JSON de sortie. 
         
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace FaceRedaction
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
        
                    // Run the FaceRedaction job.
                    var asset = RunFaceRedactionJob(@"C:\supportFiles\FaceRedaction\SomeFootage.mp4",
                                                @"C:\supportFiles\FaceRedaction\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\FaceRedaction\Output");
                }
        
                static IAsset RunFaceRedactionJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Face Redaction Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Face Redaction Job");
        
                    // Get a reference to Azure Media Redactor.
                    string MediaProcessorName = "Azure Media Redactor";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Face Redaction Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Face Redaction Output Asset", AssetCreationOptions.None);
        
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


##<a name="next-step"></a>Étape suivante

Passez en revue Media Services rubriques d’apprentissage professionnelles.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="related-links"></a>Liens connexes

[Azure Media Services Analytique vue d’ensemble](media-services-analytics-overview.md)

[Azure démonstrations multimédias Analytique](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)
