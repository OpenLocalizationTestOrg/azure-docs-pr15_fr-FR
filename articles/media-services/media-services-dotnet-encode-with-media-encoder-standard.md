<properties 
    pageTitle="Encoder un bien avec Media Encoder Standard à l’aide de .NET | Microsoft Azure" 
    description="Cette rubrique indique comment utiliser .NET à encoder un bien à l’aide de Media Encoder Strandard." 
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
    ms.date="09/19/2016"
    ms.author="juliako;anilmur"/>


# <a name="encode-an-asset-with-media-encoder-standard-using-net"></a>Encoder un bien avec Media Encoder Standard à l’aide de .NET

Tâches d’encodage sont une des opérations de traitement plus courantes dans les Services de support. Créer des tâches d’encodage pour convertir des fichiers multimédias à partir d’un codage à un autre. Lorsque vous encodez, vous pouvez utiliser le Media Services intégrés Media Encoder. Vous pouvez également utiliser un encodeur fourni par un partenaire Media Services ; encodeurs tiers sont disponibles via la Azure Marketplace. 

Cette rubrique indique comment utiliser .NET à coder vos biens avec Media Encoder Standard (MES). Media Encoder Standard est configuré à l’aide d’une de l’encodeur Présélections décrites [ici](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

Il est recommandé de toujours coder vos fichiers mezzanine dans un débit adaptive MP4 définir, puis convertir le jeu le format souhaité à l’aide de l' [Emballage dynamique](media-services-dynamic-packaging-overview.md). Pour tirer parti d’emballage dynamique, vous devez d’abord obtenir au moins une unité de diffusion en continu à la demande pour le point de terminaison de diffusion en continu à partir de laquelle vous envisagez de remise votre contenu. Pour plus d’informations, voir [comment échelle Media Services](media-services-portal-manage-streaming-endpoints.md).

Si votre actif de sortie est stockage chiffré, vous devez configurer stratégie de remise de biens. Pour plus d’informations, voir [stratégie de remise de biens de configuration](media-services-dotnet-configure-asset-delivery-policy.md).

>[AZURE.NOTE]MES génère un fichier de sortie avec un nom qui contient les 32 premiers caractères du nom de fichier d’entrée. Le nom est basé sur ce qui est spécifié dans le fichier prédéfini. Par exemple, « nomfichier » : « _ {nom de base} {Index} {Extension} ». {Nom de base} est remplacé par les 32 premiers caractères du nom de fichier d’entrée.

###<a name="mes-formats"></a>Formats de MES

[Formats et codecs](media-services-media-encoder-standard-formats.md)

###<a name="mes-presets"></a>MES Présélections

Media Encoder Standard est configuré à l’aide d’une de l’encodeur Présélections décrites [ici](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

###<a name="input-and-output-metadata"></a>Métadonnées d’entrée et de sortie

Lorsque vous encodez un bien d’entrée (ou actifs) à l’aide de MES, vous obtenez un bien de sortie à la réussite fin de cet Encoder tâche. Les biens de sortie contient la vidéo, audio, miniatures, manifeste, etc. basé sur le codage prédéfini que vous utilisez.

Les biens de sortie contient également un fichier de métadonnées à propos de la ressource d’entrée. Le nom du fichier XML métadonnées a le format suivant : < asset_id > _metadata.xml (par exemple, 41114ad3-eb5e - 4c 57-8d 92-5354e2b7d4a4_metadata.xml), où < asset_id > est la valeur réfbien du capital d’entrée. Le schéma de ces métadonnées d’entrée XML est décrit [ici](http://msdn.microsoft.com/library/azure/dn783120.aspx).

Les biens de sortie contient également un fichier de métadonnées à propos de l’actif de sortie. Le nom du fichier XML métadonnées a le format suivant : < source_file_name > _manifest.xml (par exemple, BigBuckBunny_manifest.xml). Le schéma de ces métadonnées de sortie XML est décrit [ici](http://msdn.microsoft.com/library/azure/dn783217.aspx).

Si vous souhaitez examiner une des deux fichiers de métadonnées, vous pouvez créer un repère associations de sécurité et téléchargez le fichier sur votre ordinateur local. Vous pouvez rechercher un exemple sur la façon de créer un repère associations de sécurité et télécharger un fichier en utilisant les Extensions Media Services .NET SDK.

##<a name="download-sample"></a>Télécharger l’exemple

Obtenir et exécutez un échantillon de [ici](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

##<a name="example"></a>Exemple

L’exemple suivant utilise Media Services .NET SDK pour effectuer les tâches suivantes :

- Créer une tâche de codage.
- Obtenir une référence à l’encodeur Media Encoder Standard.
- Spécifiez pour utiliser le « H264 débit plusieurs 720p « prédéfinis. Vous pouvez voir tous les paramètres prédéfinis [ici](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409). Vous pouvez également examiner le schéma auquel ces Présélections doivent être conformes rubrique [ici](https://msdn.microsoft.com/library/mt269962.aspx) .
- Ajouter une seule tâche codage à la tâche. 
- Spécifier les biens d’entrée à coder.
- Créer un bien de sortie contenant l’actif codé.
- Ajouter un gestionnaire d’événements pour vérifier la progression du projet.
- Soumettre la tâche.
        
        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
        

            // Create a task with the encoding details, using a string preset.
            // In this case "H264 Multiple Bitrate 720p" preset is used.
            ITask task = job.Tasks.AddNew("My encoding task",
                processor,
                "H264 Multiple Bitrate 720p",
                TaskOptions.None);
        
            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);
        
            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();
        
            return job.OutputMediaAssets[0];
        }
        
        private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);
            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
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
                    break;
                default:
                    break;
            }
        }
        
        
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

##<a name="see-also"></a>Voir aussi 

[Comment créer la miniature à l’aide de Media Encoder Standard avec .NET](media-services-dotnet-generate-thumbnail-with-mes.md)
[Media Services codage Overview](media-services-encode-asset.md)
