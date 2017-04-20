<properties
    pageTitle="Détecter nominale et les émotions avec Azure Media Analytique | Microsoft Azure"
    description="Cette rubrique montre comment détecter faces et émotions avec Azure Media Analytique."
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
    ms.date="09/26/2016"   
    ms.author="milanga;juliako;"/>
 
#<a name="detect-face-and-emotion-with-azure-media-analytics"></a>Détecter nominale et les émotions avec Azure Media Analytique

##<a name="overview"></a>Vue d’ensemble

La **Détection de visage Azure Media** media processeur () vous permet de compter, suivre les mouvements et même évaluer la participation du public et réaction via des expressions du visage. Ce service propose deux fonctionnalités : 

- **Détection de visage**

    La détection de visage recherche et effectue le suivi des faces humaines dans une vidéo. Plusieurs faces peuvent être détectées et par la suite être suivis qu’ils sont en déplacement, avec les métadonnées de temps et l’emplacement renvoyées dans un fichier JSON. Lors du suivi, il tentera d’attribuer un ID cohérent à la même face alors que la personne est déplacement sur l’écran, même s’ils sont coupés ou laissez brièvement le cadre.

    >[AZURE.NOTE]Ce service n’effectue pas de reconnaissance du visage. Une personne qui laisse le cadre ou devient coupée pour trop longtemps sera attribué un identifiant lorsqu’ils renvoient.

- **Détection des émotions**
    
    Détection des émotions est un composant facultatif du processeur Media détection nominale qui renvoie analyse sur plusieurs attributs émotionnels de faces détectés, notamment bonheur, leur, peur, anger et plus encore. 

La **Détection de visage Azure Media** point de gestion est en cours d’aperçu.

Cette rubrique fournit des détails sur la **Détection de visage Azure Media** et montre comment l’utiliser avec Media Services SDK pour .NET.

##<a name="face-detector-input-files"></a>Fichiers d’entrée de détection de face

Fichiers vidéo. Pour l’instant, les formats suivants sont pris en charge : WMV, MOV et MP4.

##<a name="face-detector-output-files"></a>Fichiers de sortie de détection de face

L’API de détection et le suivi de visage fournit la détection de grande précision visage emplacement et le suivi permettant de détecter jusqu'à 64 faces humaines dans une vidéo. Faces frontales fournissent les meilleurs résultats, tout en face et petites faces (inférieure ou égale à 24 24 pixels) peuvent ne pas être aussi précis.

Les faces détectés et suivis sont retournés avec des coordonnées (gauche, haut, largeur et hauteur) indiquant l’emplacement de faces dans l’image en pixels, mais aussi un numéro d’identification nominale indiquant que le suivi de cette personne. Numéros d’identification nominale sont propice aux réinitialiser dans des circonstances lorsque la face frontale est perdue ou qui se chevauchent dans le cadre, ce qui crée de certaines personnes prise attribuées plusieurs codes.

###<a id="output_elements"></a>Éléments du fichier JSON de sortie

Pour la détection de visage et opération de suivi, le résultat de sortie contient les métadonnées à partir des faces dans le fichier au format JSON.

La détection de visage et suivi JSON inclut les attributs suivants :

Élément|Description
---|---
Version|Cela fait référence à la version de l’API de la vidéo.
Échelle de temps|« Graduations » par seconde de la vidéo.
DECALER|Il s’agit le décalage de temps pour les horodatages. Dans la version 1.0 d’API de vidéo, il sera toujours 0. À l’avenir nous prenons en charge les scénarios, cette valeur peut changer.
Fréquence d’images|Images par seconde de la vidéo.
Fragments|Les métadonnées sont segmentée vers le haut dans différents segments appelés fragments. Chaque fragment contient un début, la durée, nombre d’intervalles et événements.
Démarrer|L’heure de début du premier événement en « graduations ».
Durée|La longueur du fragment, dans « graduations ».
Intervalle|L’intervalle de chaque entrée d’événement dans le fragment, dans « graduations ».
Événements|Chaque événement contient les faces détecté et suivies dans cette durée. Il s’agit d’un tableau de tableau d’événements. Le tableau externe représente un intervalle de temps. Le tableau interne se compose de 0 ou des événements supplémentaires qui s’est produite à ce stade. Un [] fermant vide indique qu’aucun faces a n’étaient détectées.
ID| ID de l’image est suivi. Ce numéro peut modifier par inadvertance si une face devient non détectée. Une personne donnée doit avoir le même ID tout au long de la vidéo globale, mais cela ne peut pas être garantie en raison des limitations dans l’algorithme de détection (occlusion, etc.).
X, Y|La partie supérieure gauche X et Y coordonnées de la face englobant dans une échelle normalisée de 0.0 à 1.0. <br/>-X et Y coordonnées sont relatives à l’affichage paysage toujours, afin que si vous avez un portrait vidéo (ou envers, dans le cas d’iOS), vous devrez transposer les coordonnées en conséquence.
Largeur, hauteur|La largeur et la hauteur de la face englobant dans une échelle normalisée de 0.0 à 1.0.
facesDetected|Il se trouve à la fin des résultats JSON et résume le nombre de faces qui l’algorithme détectée au cours de la vidéo. Étant donné que les ID peuvent être réinitialisés par inadvertance si une face devient non détectée (par exemple, nominale s’éteint écran, apparences absent (e)), ce nombre peut égal pas toujours au nombre true de faces dans la vidéo.

Visage détection utilise des techniques de fragmentation (où les métadonnées peuvent être décomposées de segments temporelles et vous pouvez télécharger uniquement ce que vous devez) et segmentation (où les événements sont décomposées au cas où ils obtiennent trop volumineux). Certains calculs simples peuvent vous aider à transformer les données. Par exemple, si un événement commencé à 6300 (graduations), avec une échelle de temps de 2997 (graduations/sec) et la fréquence d’images de 29,97 (trames/s), puis :

- Début/échelle de temps = secondes 2.1
- Secondes x (fréquence d’images/échelle de temps) = 63 cadres

Voici un exemple simple d’extraire le JSON dans un par un format de trame de détection de visage et de suivi :
    
    var faceDetectionResultJsonString = operationResult.ProcessingResult;
    var faceDetecionTracking = 
         JsonConvert.DeserializeObject<FaceDetectionResult>(faceDetectionResultJsonString, settings);


##<a name="face-detection-input-and-output-example"></a>Confronté détection d’entrée et de sortie exemple

###<a name="input-video"></a>Vidéo d’entrée

[Vidéo d’entrée](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

###<a name="task-configuration-preset"></a>Configuration d’une tâche (prédéfini)

Lorsque vous créez une tâche avec **Azure Media nominale détection**, vous devez spécifier un configuration prédéfini. Le paramètre de configuration suivant prédéfini est seulement pour la détection de visage.

    {"version":"1.0"}

###<a name="json-output"></a>Sortie JSON

L’exemple suivant de sortie JSON a été tronqué.

    {
    "version": 1,
    "timescale": 30000,
    "offset": 0,
    "framerate": 29.97,
    "width": 1280,
    "height": 720,
    "fragments": [
        {
        "start": 0,
        "duration": 60060
        },
        {
        "start": 60060,
        "duration": 60060,
        "interval": 1001,
        "events": [
            [
            {
                "id": 0,
                "x": 0.519531,
                "y": 0.180556,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517969,
                "y": 0.181944,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517187,
                "y": 0.183333,
                "width": 0.0851562,
                "height": 0.151389
            }
            ],

        . . . 

##<a name="emotion-detection-input-and-output-example"></a>Détection des émotions entrée et sortie exemple


###<a name="input-video"></a>Vidéo d’entrée

[Vidéo d’entrée](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

###<a name="task-configuration-preset"></a>Configuration d’une tâche (prédéfini)

Lorsque vous créez une tâche avec **Azure Media nominale détection**, vous devez spécifier un configuration prédéfini. Le paramètre de configuration suivant prédéfini spécifie pour créer JSON basé sur la détection émotions.
    
    {
      "version": "1.0",
      "options": {
        "aggregateEmotionWindowMs": "987",
        "mode": "aggregateEmotion",
        "aggregateEmotionIntervalMs": "342"
      }
    }


####<a name="attribute-descriptions"></a>Descriptions des attributs

Nom de l’attribut|Description
---|---
Mode|Faces : Confronté uniquement détection <br/>AggregateEmotion : Les valeurs de retour émotions moyenne de toutes les faces dans le cadre.
AggregateEmotionWindowMs|Utilisez si AggregateEmotion mode sélectionné. Spécifie la longueur de la vidéo permet de générer chaque résultat d’agrégation, en millisecondes.
AggregateEmotionIntervalMs|Utilisez si AggregateEmotion mode sélectionné. Spécifie à quelle fréquence pour produire des résultats de regroupement.

####<a name="aggregate-defaults"></a>Agrégation par défaut

Ci-dessous sont recommandés valeurs pour les paramètres de fenêtre et l’intervalle de regroupement. AggregateEmotionWindowMs peut contenir plus de AggregateEmotionIntervalMs.

   |Valeurs par défaut (s)|Max(s)|Min(s)
---|---|---|---
AggregateEmotionWindowMs|0,5|2|0,25
AggregateEmotionIntervalMs|0,5|1|0,25

###<a name="json-output"></a>Sortie JSON

JSON de sortie pour l’agrégation émotions (tronquées) :
 
    
    {
     "version": 1,
     "timescale": 30000,
     "offset": 0,
     "framerate": 29.97,
     "width": 1280,
     "height": 720,
     "fragments": [
       {
         "start": 0,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ]
         ]
       },
       {
         "start": 60060,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0.688541,
                 "happiness": 0.0586323,
                 "surprise": 0.227184,
                 "sadness": 0.00945675,
                 "anger": 0.00592107,
                 "disgust": 0.00154993,
                 "fear": 0.00450447,
                 "contempt": 0.0042109
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,


##<a name="limitations"></a>Limitations

- Formats vidéo pris en charge d’entrée incluent WMV, MOV et MP4.
- La plage de taille nominale détectable est 24 24 à 2048 x 2048 pixels. Les faces se déconnecter de cette plage ne seront pas détectés.
- Pour chaque vidéo, le nombre maximal de faces renvoyée est 64.
- Certaines faces peut ne pas être détectés en raison de problèmes techniques ; par exemple, très grandes angles de face (tête pose) et occlusion volumineuse. Faces frontales et près frontal ont les meilleurs résultats.


## <a name="sample-code"></a>Exemples de code

Les éléments suivants du programme indique comment :

1. Créer un bien et télécharger un fichier multimédia dans la ressource.
1. Crée une tâche avec une tâche de détection nominale basée sur un fichier de configuration qui contient la prédéfini json suivant. 
                    
        {
            "version": "1.0"
        }

1. Téléchargements de fichiers JSON de sortie. 
         
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace FaceDetection
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
        
                    // Run the FaceDetection job.
                    var asset = RunFaceDetectionJob(@"C:\supportFiles\FaceDetection\BigBuckBunny.mp4",
                                                @"C:\supportFiles\FaceDetection\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\FaceDetection\Output");
                }
        
                static IAsset RunFaceDetectionJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Face Detection Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Face Detection Job");
        
                    // Get a reference to Azure Media Face Detector.
                    string MediaProcessorName = "Azure Media Face Detector";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Face Detection Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Face Detectoion Output Asset", AssetCreationOptions.None);
        
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

##<a name="related-links"></a>Liens connexes

[Azure Media Services Analytique vue d’ensemble](media-services-analytics-overview.md)

[Azure démonstrations multimédias Analytique](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)