<properties
    pageTitle="Média Hyperlapse des fichiers avec Azure Media Hyperlapse | Microsoft Azure"
    description="Azure Media Hyperlapse crée lisses vidéos temps écoulé à partir de la première personne ou l’appareil photo de l’action du contenu. Cette rubrique indique comment utiliser Indexer de Media."
    services="media-services"
    documentationCenter=""
    authors="asolanki"
    manager="johndeu"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/19/2016"  
    ms.author="adsolank"/>


# <a name="hyperlapse-media-files-with-azure-media-hyperlapse"></a>Fichiers de Hyperlapse multimédias avec Azure Media Hyperlapse

Azure Media Hyperlapse est un média processeur (programme minimal) qui crée lisses vidéos temps écoulé à partir de la première personne ou l’appareil photo de l’action du contenu.  Frères à [Microsoft Research bureau Hyperlapse Pro et téléphonique Hyperlapse Mobile](http://aka.ms/hyperlapse)sur le nuage, Microsoft Hyperlapse pour Azure Media Services utilise l’échelle de la plateforme Azure Media Services multimédias traitement à l’échelle horizontalement et paralléliser massif en bloc Hyperlapse de traitement.

>[AZURE.IMPORTANT]Hyperlapse Microsoft est conçu pour fonctionner de façon optimale sur le contenu de la première personne avec un appareil mobile.  Bien qu’une partie d’appareil-photo peut fonctionner toujours, les performances et la qualité du processeur Azure Media Hyperlapse Media ne peut pas être garanties pour d’autres types de contenu.  Pour en savoir plus sur Microsoft Hyperlapse pour Azure Media Services et voir certaines vidéos d’exemple, consultez le [billet de blog introduction](http://aka.ms/azurehyperlapseblog) à partir de la version d’évaluation.

Un Hyperlapse Media Azure travail prend comme entrée un fichier de biens MP4, MOV ou WMV ainsi qu’un fichier de configuration qui spécifie les images de la vidéo doivent être temps écoulé et à quelle vitesse (par exemple, les 10 000 premières cadres en x 2).  Le résultat est un rendu de stabilisées et temps écoulé de l’entrée vidéo.

Pour les dernières mises à jour Azure Media Hyperlapse, voir [blogs Media Services](https://azure.microsoft.com/blog/topics/media-services/).

## <a name="hyperlapse-an-asset"></a>Hyperlapse un bien

Vous devez tout d’abord télécharger votre fichier d’entrée souhaité dans Azure Media Services.  Pour en savoir plus sur les concepts relatifs aux télécharger et de gestion de contenu, consultez l' [article de la gestion de contenu](media-services-portal-vod-get-started.md).

###  <a id="configuration"></a>Configuration prédéfini pour Hyperlapse

Une fois votre contenu dans votre compte Media Services, vous avez besoin créer votre prédéfini de configuration.  Le tableau suivant répertorie les champs définis par l’utilisateur :

 Champ | Description
-------|-------------
StartFrame|L’image sur laquelle le traitement Hyperlapse Microsoft doit commencer.
NumFrames|Le nombre d’images à traiter
Vitesse|Facteurs avec lesquelles vous pouvez accélérer la vidéo d’entrée.

Voici un exemple de fichier de configuration conformes au format XML et JSON :

**XML prédéfini :**

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
        <Sources>
            <Source StartFrame="0" NumFrames="10000" />
        </Sources>
        <Options>
            <Speed>12</Speed>
        </Options>
    </Preset>

**JSON prédéfini :**

    {
        "Version":1.0,
        "Sources": [
            {
                "StartFrame":0,
                "NumFrames":2147483647
            }
        ],
        "Options": {
            "Speed":1,
            "Stabilize":false
        }
    }

###  <a id="sample_code"></a>Microsoft Hyperlapse avec le Kit de développement .NET AMS

La méthode suivante télécharge un fichier multimédia en tant qu’actif et crée une tâche avec le processeur Azure Media Hyperlapse multimédia.

> [AZURE.NOTE] Vous disposez déjà d’un CloudMediaContext dans l’étendue avec le nom « contexte » pour ce code pour l’utiliser.  Pour plus d’informations, consultez l' [article de gestion de contenu](media-services-dotnet-get-started.md).

> [AZURE.NOTE] L’argument de chaîne « hyperConfig » est censée être une configuration conforme prédéfinie dans JSON ou XML comme décrit ci-dessus.

statique bool RunHyperlapseJob (entrée de chaîne, sortie de chaîne, chaîne hyperConfig) {/ / Créez bien avec biens IAsset de fichier d’entrée = contexte. Éléments. CreateAssetAndUploadSingleFile (entrée, « Mon entrée Hyperlapse », AssetCreationOptions.None) ;

récupérer des instances de point de gestion Azure Media Hyperlapse Panneau de gestion IMediaProcessor = contexte. MediaProcessors. GetLatestMediaProcessorByName (« Hyperlapse Azure Media ») ;

créer des travaux avec Hyperlapse tâche IJob = contexte. Tâches. Créer (String.Format (« Hyperlapse {0} », entrée)) ;

Si (String.IsNullOrEmpty(hyperConfig)) {/ / config ne peut pas être vide retourner false ;}

hyperConfig = File.ReadAllText(hyperConfig) ;

ITask hyperlapseTask = travail. Tasks.AddNew (« Hyperlapse tâche », programme minimal, hyperConfig, TaskOptions.None) ; hyperlapseTask.InputAssets.Add(asset) ; hyperlapseTask.OutputAssets.AddNew (« Hyperlapse sortie », AssetCreationOptions.None) ;


tâche. Submit() ;

Créer l’impression de l’avancement et interroger des tâches tâche progressPrintTask = Task(() Nouveau = > {}

IJob jobQuery = null ; Effectuez {var progressContext = contexte ; jobQuery = progressContext.Jobs. Où (j = > j.Id == travail. ID). First() ; Console.WriteLine (chaîne. Format (« \t \t {1} {0} {2} », DateTime.Now, jobQuery.State, jobQuery.Tasks[0]. Cours)) ; Thread.Sleep(10000) ; } pendant (jobQuery.State ! = JobState.Finished & & jobQuery.State ! = JobState.Error & & jobQuery.State ! = JobState.Canceled) ; }); progressPrintTask.Start() ;

            Task progressJobTask = job.GetExecutionProgressTask(
                                                 CancellationToken.None);
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
                return false;                  
            }

        DownloadAsset(job.OutputMediaAssets.First(), output);
        return true;
    }

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }


    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = context.Assets.Create(assetName, options);

        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);

        return asset;
    }

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }

### <a id="file_types"></a>Types de fichiers pris en charge

- MP4
- MOV
- WMV



##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="related-links"></a>Liens connexes

[Azure Media Services Analytique vue d’ensemble](media-services-analytics-overview.md)

[Azure démonstrations multimédias Analytique](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)
