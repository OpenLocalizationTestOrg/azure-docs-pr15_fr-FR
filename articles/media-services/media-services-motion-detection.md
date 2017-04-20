<properties
    pageTitle="Détecter les mouvements avec Azure Media Analytique | Microsoft Azure"
    description="La détection de mouvement Azure Media media processeur () permet d’identifier efficacement les sections utiles au sein d’une vidéo dans le cas contraire versions longue et se déroule normalement."
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
    ms.date="10/10/2016"  
    ms.author="milanga;juliako;"/>
 
# <a name="detect-motions-with-azure-media-analytics"></a>Détecter les mouvements avec Azure Media Analytique

##<a name="overview"></a>Vue d’ensemble

La **Détection de mouvement Azure Media** media processeur () permet d’identifier efficacement les sections utiles au sein d’une vidéo dans le cas contraire versions longue et se déroule normalement. Détection de mouvement peut être utilisée dans une partie caméra statique pour identifier les sections de la vidéo où mouvement s’est produite. Il génère un fichier JSON contenant une entrée de métadonnées avec horodatages et la région de cadre englobant dans lequel l’événement s’est produite.

Ciblée sur flux vidéo de sécurité, cette technologie est en mesure de classer mouvement en fausses tels que les ombres et les changements d’éclairage et événements appropriés. Permet de générer des alertes de sécurité à partir de flux vidéo sans recevoir des messages indésirables avec des événements non pertinents illimitées, tout en la possibilité extraire instants d’intérêt de vidéos de surveillance longs.

La **Détection de mouvement Azure Media** point de gestion est en cours d’aperçu.

Cette rubrique fournit des détails sur la **Détection de mouvement Azure Media** et montre comment l’utiliser avec Media Services SDK pour .NET


##<a name="motion-detector-input-files"></a>Fichiers d’entrée de détection de mouvement

Fichiers vidéo. Pour l’instant, les formats suivants sont pris en charge : WMV, MOV et MP4.

##<a name="task-configuration-preset"></a>Configuration d’une tâche (prédéfini)

Lorsque vous créez une tâche avec **Détection de mouvement Azure Media**, vous devez spécifier un configuration prédéfini. 

###<a name="parameters"></a>Paramètres

Vous pouvez utiliser les paramètres suivants :

Nom|Options|Description|Par défaut
---|---|---|---
sensitivityLevel|Chaîne : « bas », « support », « haut »|Définit le niveau de sensibilité en les mouvements est signalé. Ajuster cette option pour ajuster fausses.|« support »
frameSamplingValue|Nombre entier positif|Définit la fréquence sur lequel s’exécute algorithme. 1 est égal à chaque image, 2 signifie chaque cadre 2ème et ainsi de suite.|1
detectLightChange|Booléenne : « true », « false »|Définit les changements d’éclairage sont signalés dans les résultats|« False »
mergeTimeThreshold|X-heure : Hh : mm :<br/>Exemple : 00:00:03|Spécifie la fenêtre de temps entre les événements de mouvement où 2 événements sont combinées et signalés comme 1.|00:00:00
detectionZones|Tableau de zones de détection :<br/>-Détection Zone est un tableau de points 3 ou plus<br/>-Point est un x et y coordonnées de 0 en 1.|Décrit la liste des zones de détection polygonale à utiliser.<br/>Résultats seront signalées avec les zones en tant qu’un ID, le tout d’abord une étant 'id' : 0|Zone unique qui aborde la trame dans son ensemble.

###<a name="json-example"></a>Exemple JSON

    
    {
      'version': '1.0',
      'options': {
        'sensitivityLevel': 'medium',
        'frameSamplingValue': 1,
        'detectLightChange': 'False',
        "mergeTimeThreshold":
        '00:00:02',
        'detectionZones': [
          [
            {'x': 0, 'y': 0},
            {'x': 0.5, 'y': 0},
            {'x': 0, 'y': 1}
           ],
          [
            {'x': 0.3, 'y': 0.3},
            {'x': 0.55, 'y': 0.3},
            {'x': 0.8, 'y': 0.3},
            {'x': 0.8, 'y': 0.55},
            {'x': 0.8, 'y': 0.8},
            {'x': 0.55, 'y': 0.8},
            {'x': 0.3, 'y': 0.8},
            {'x': 0.3, 'y': 0.55}
          ]
        ]
      }
    }


##<a name="motion-detector-output-files"></a>Fichiers de sortie de détection de mouvement

Une tâche de détection de mouvement renverra un fichier JSON dans l’actif de sortie qui décrit les alertes de mouvement, ainsi que leurs catégories, au sein de la vidéo. Le fichier contient des informations sur l’heure et la durée de mouvement détecté dans la vidéo.

L’API de détection de mouvement fournit des indicateurs une fois qu’il existe des objets en mouvement dans une vidéo d’arrière-plan fixe (par exemple, une surveillance vidéo). La détection de mouvement est formée pour réduire les fausses, tels qu’éclairage et modifications ombre. Limites actuelles des algorithmes incluent nuit vision vidéos, objets semi-transparente et petits objets.

###<a id="output_elements"></a>Éléments du fichier JSON de sortie

>[AZURE.NOTE]Dans la dernière version, le format de sortie JSON a changé et peut représenter une modification avec rupture pour certains clients.

Le tableau suivant décrit les éléments du fichier JSON de sortie.

Élément|Description
---|---
Version|Cela fait référence à la version de l’API de la vidéo. La version actuelle est 2.
Échelle de temps|« Graduations » par seconde de la vidéo.
DECALER|Le décalage de temps pour les horodatages dans « graduations ». Dans la version 1.0 d’API de vidéo, il sera toujours 0. À l’avenir nous prenons en charge les scénarios, cette valeur peut changer.
Fréquence d’images|Images par seconde de la vidéo.
Largeur, hauteur|Fait référence à la largeur et la hauteur de la vidéo en pixels.
Démarrer|L’horodatage de début en « graduations ».
Durée|La longueur de l’événement, dans « graduations ».
Intervalle|L’intervalle de chaque entrée de l’événement, dans « graduations ».
Événements|Chaque fragment événement contient le mouvement détecté dans cette durée.
Type|Dans la version actuelle, il est toujours « 2 » pour mouvement générique. Cette vidéo API la flexibilité à classer vous permet d’étiquette de mouvement dans les futures versions.
RegionID|Comme indiqué ci-dessus, il s’agit toujours 0 dans cette version. Cette étiquette permet de vidéo API pour rechercher des trajectoires dans différentes régions dans les versions ultérieures.
Régions|Fait référence à la zone de l’endroit où vous intéresse mouvement dans votre vidéo. <br/><br/>-« id » représente la zone région – dans cette version il existe un seul ID 0. <br/>-« type » représente la forme de la zone qui vous intéresse pour mouvement. Pour l’instant, « rectangle » et « polygone » sont pris en charge.<br/> Si vous avez spécifié « rectangle », la région a dimensions dans X, Y, largeur et hauteur. Les coordonnées X et Y représentent les coordonnées XY supérieur gauche de la région dans une échelle de 0.0 à 1.0 centrée réduite. La largeur et la hauteur représentent la taille de la région dans une échelle de 0.0 à 1.0 centrée réduite. Dans la version actuelle, X, Y, largeur et hauteur sont toujours fixes à 0, 0 et 1, 1. <br/>Si vous avez spécifié « polygone », la région a dimensions en points. <br/>
Fragments|Les métadonnées sont segmentée vers le haut dans différents segments appelés fragments. Chaque fragment contient un début, la durée, nombre d’intervalles et événements. Un fragment avec aucun événement signifie qu’aucun mouvement a été détecté au cours de cette heure de début et une durée.
Entre crochets]|Chaque crochet représente un intervalle de dans l’événement. Vider entre crochets pour cet intervalle signifie qu’aucun mouvement a été détectée.
emplacements|Cette nouvelle entrée sous événements répertorie l’emplacement où le mouvement s’est produit. Il s’agit plus spécifique que les zones de détection.

Voici un exemple de sortie JSON

    {
      "version": 2,
      "timescale": 23976,
      "offset": 0,
      "framerate": 24,
      "width": 1280,
      "height": 720,
      "regions": [
        {
          "id": 0,
          "type": "polygon",
          "points": [{'x': 0, 'y': 0},
            {'x': 0.5, 'y': 0},
            {'x': 0, 'y': 1}]
        }
      ],
      "fragments": [
        {
          "start": 0,
          "duration": 226765
        },
        {
          "start": 226765,
          "duration": 47952,
          "interval": 999,
          "events": [
            [
              {
                "type": 2,
                "typeName": "motion",
                "locations": [
                  {
                    "x": 0.004184,
                    "y": 0.007463,
                    "width": 0.991667,
                    "height": 0.985185
                  }
                ],
                "regionId": 0
              }
            ],
    
    …
##<a name="limitations"></a>Limitations

- Formats vidéo pris en charge d’entrée incluent WMV, MOV et MP4.
- Détection de mouvement est optimisée pour les vidéos arrière-plan fixe. L’algorithme met l’accent sur la réduction des fausses, tels que des changements d’éclairage et ombres.
- Certains mouvement peut ne pas être détecté en raison de problèmes techniques ; par exemple, nuit vision vidéos, objets semi-transparente et petits objets.


## <a name="sample-code"></a>Exemples de code

Les éléments suivants du programme indique comment :

1. Créer un bien et télécharger un fichier multimédia dans la ressource.
1. Crée une tâche avec une tâche de détection de mouvement vidéo basée sur un fichier de configuration qui contient la prédéfini json suivant. 
                    
        {
          'Version': '1.0',
          'Options': {
            'SensitivityLevel': 'medium',
            'FrameSamplingValue': 1,
            'DetectLightChange': 'False',
            "MergeTimeThreshold":
            '00:00:02',
            'DetectionZones': [
              [
                {'x': 0, 'y': 0},
                {'x': 0.5, 'y': 0},
                {'x': 0, 'y': 1}
               ],
              [
                {'x': 0.3, 'y': 0.3},
                {'x': 0.55, 'y': 0.3},
                {'x': 0.8, 'y': 0.3},
                {'x': 0.8, 'y': 0.55},
                {'x': 0.8, 'y': 0.8},
                {'x': 0.55, 'y': 0.8},
                {'x': 0.3, 'y': 0.8},
                {'x': 0.3, 'y': 0.55}
              ]
            ]
          }
        }

1. Téléchargements de fichiers JSON de sortie. 
         
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace VideoMotionDetection
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
        
                    // Run the VideoMotionDetection job.
                    var asset = RunVideoMotionDetectionJob(@"C:\supportFiles\VideoMotionDetection\BigBuckBunny.mp4",
                                                @"C:\supportFiles\VideoMotionDetection\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\VideoMotionDetection\Output");
                }
        
                static IAsset RunVideoMotionDetectionJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Video Motion Detection Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Video Motion Detection Job");
        
                    // Get a reference to Azure Media Motion Detector.
                    string MediaProcessorName = "Azure Media Motion Detector";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Video Motion Detection Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Video Motion Detectoion Output Asset", AssetCreationOptions.None);
        
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
[Azure Media Services mouvement détection blog](https://azure.microsoft.com/blog/motion-detector-update/)

[Azure Media Services Analytique vue d’ensemble](media-services-analytics-overview.md)

[Azure démonstrations multimédias Analytique](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)
