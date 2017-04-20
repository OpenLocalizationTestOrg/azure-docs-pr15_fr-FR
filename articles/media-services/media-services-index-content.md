<properties
    pageTitle="L’indexation des fichiers multimédia avec Indexer de Media Azure"
    description="Azure Media indexeur permet pour rendre le contenu de vos fichiers multimédias que vous pourrez rechercher et générer une transcription en texte intégral pour les légendes et mots clés. Cette rubrique indique comment utiliser Indexer de Media."
    services="media-services"
    documentationCenter=""
    authors="Asolanki"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/12/2016"   
    ms.author="adsolank;juliako;johndeu"/>


# <a name="indexing-media-files-with-azure-media-indexer"></a>L’indexation des fichiers multimédia avec Indexer de Media Azure


Azure Media indexeur permet pour rendre le contenu de vos fichiers multimédias que vous pourrez rechercher et générer une transcription en texte intégral pour les légendes et mots clés. Vous pouvez traiter fichier un multimédia ou plusieurs fichiers multimédias dans un lot.  

>[AZURE.IMPORTANT] Lors de l’indexation du contenu, vérifiez que vous utilisez des fichiers multimédias qui ont vocale très nette (sans musique en arrière-plan, le bruit, effets ou souffle microphone). Voici quelques exemples de contenu approprié : enregistré réunions, conférences ou présentations. Le contenu suivant peut-être ne pas être utile pour indexer : films, télévisées, quoi que ce soit avec mixte audio et des effets sonores, du contenu avec le bruit de fond (souffle) mal enregistrement.


Une opération d’indexation peut générer les sorties suivantes :

- Fermé légende fichiers aux formats suivants : **same**, **TTML**et **WebVTT**.

    Fichiers sous-titres incluent une balise appelée raisons de reconnaissance, les scores une opération d’indexation en fonction de comment reconnaissable la reconnaissance vocale dans la vidéo source.  Vous pouvez utiliser la valeur de raisons de reconnaissance pour filtrer les fichiers de sortie de facilité d’utilisation. Un score faible signifierait mauvais résultats indexation en raison de la qualité audio.
- Fichier de mot clé (XML).
- Audio l’indexation d’objets blob fichier (AIB) pour une utilisation avec SQL server.

    Pour plus d’informations, voir [Utilisation de fichiers AIB avec Azure Media indexeur et SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/).


Cette rubrique montre comment créer des travaux d’indexation **d’un bien Index** et **d’indexer plusieurs fichiers**.

Pour les dernières mises à jour Azure Media indexeur, voir [blogs Media Services](#preset).

## <a name="using-configuration-and-manifest-files-for-indexing-tasks"></a>À l’aide de fichiers manifeste et de configuration pour l’indexation de tâches

Vous pouvez spécifier plus de détails pour vos tâches d’indexation en utilisant une configuration de la tâche. Par exemple, vous pouvez spécifier les métadonnées à utiliser pour votre fichier multimédia. Ces métadonnées sont utilisées par le moteur de langue pour développer son vocabulaire et améliore considérablement la précision de la reconnaissance vocale.  Vous pouvez également spécifier vos fichiers de sortie souhaité.

Vous pouvez également traiter plusieurs fichiers multimédias à la fois à l’aide d’un fichier manifeste.

Pour plus d’informations, voir [Tâches prédéfinis pour Azure Media indexeur](https://msdn.microsoft.com/library/dn783454.aspx).

## <a name="index-an-asset"></a>Indexer un bien

La méthode suivante télécharge un fichier multimédia en tant qu’actif et crée un projet pour indexer du bien.

Notez que si aucun fichier de configuration n’est spécifié, le fichier multimédia est indexé avec tous les paramètres par défaut.

    static bool RunIndexingJob(string inputMediaFilePath, string outputFolder, string configurationFile = "")
    {
        // Create an asset and upload the input media file to storage.
        IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Indexing Input Asset",
            AssetCreationOptions.None);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration from file if specified.
        string configuration = string.IsNullOrEmpty(configurationFile) ? "" : File.ReadAllText(configurationFile);

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
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
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
<!-- __ -->
### <a id="output_files"></a>Fichiers de sortie

Par défaut, une opération d’indexation génère les fichiers de sortie suivants. Les fichiers seront stockés dans le premier actif de sortie.

Lorsqu’il y a plusieurs fichiers multimédias d’entrée, indexeur génère un fichier manifeste pour les sorties de projet, nommé « JobResult.txt ». Pour chaque entrée de fichier multimédia, les AIB résultante, Same, TTML, WebVTT et fichiers de mot clé, sont dans un ordre séquentiel numérotées et nommés à l’aide de le « Alias ».

Nom de fichier | Description
----------|------------
__InputFileName.aib__ | Fichier blob indexation audio. <br /><br /> Fichier audio de l’indexation d’objets Blob (AIB) est un fichier binaire qui peut être recherché dans Microsoft SQL server à l’aide de la recherche en texte intégral.  Le fichier AIB est plus puissant que les fichiers légende simple, car elle contient des alternatives pour chaque mot, ce qui permet une expérience de recherche beaucoup plus riche. <br/> <br/>Elle nécessite l’installation du module complémentaire indexeur SQL sur un ordinateur exécutant Microsoft SQL server 2008 ou version ultérieure. Recherche la AIB à l’aide de Microsoft SQL recherche en texte intégral server fournit les résultats de recherche plus précis que la recherche les fichiers sous-titres générés par WAMI. C’est parce que la AIB contient synonymes le son similaire alors que les fichiers sous-titres contiennent le mot de confiance le plus élevé pour chaque segment de l’audio. Si la recherche des mots prononcés est d’importance upmost, il est recommandé d’utiliser la conjonction AIB dans Microsoft SQL Server.<br/><br/> Pour télécharger le module complémentaire, cliquez sur <a href="http://aka.ms/indexersql">Composant additionnel de Azure Media indexeur SQL</a>. <br/><br/>Il est également possible d’utiliser des moteurs de recherche telles que Apache Lucene/mode série sur LAN.r à indexer simplement la vidéo en fonction des sous-titres et fichiers XML de mot clé, mais cela provoquera dans les résultats de recherche moins précises.
__InputFileName.smi__<br />__InputFileName.ttml__<br />__InputFileName.vtt__ |Fichiers de légende (CC) fermés aux formats Same, TTML et WebVTT.<br/><br/>Ils peuvent servir à rendre accessible aux personnes présentant un handicap Audition à des fichiers audio et vidéo.<br/><br/>Fichiers de légende fermés incluent une balise appelée <b>raisons de reconnaissance</b> les scores une opération d’indexation en fonction de comment reconnaissable vocale de la vidéo source est.  Vous pouvez utiliser la valeur de <b>raisons de reconnaissance</b> pour filtrer les fichiers de sortie de facilité d’utilisation. Un score faible signifierait mauvais résultats indexation en raison de la qualité audio.
__InputFileName.kw.xml<br />InputFileName.info__ |Fichiers de mots clés et des informations. <br/><br/>Fichier de mot clé est un fichier XML qui contient des mots clés extraits du contenu vocale, avec la fréquence et les informations d’offset. <br/><br/>Fichier Info est un fichier texte brut qui contient des informations granulaires sur chaque terme reconnu. La première ligne est spéciale et contient le score raisons de reconnaissance. Chaque ligne suivante est une liste séparé par des tabulations les données suivantes : Démarrer heure, heure de fin, word/phrase, confiance. Les temps sont exprimés en secondes et la confiance est donnée sous la forme d’un nombre compris entre 0-1. <br/><br/>Exemple de ligne : « 1,20 1,45 word 0.67 » <br/><br/>Ces fichiers peuvent être utilisés pour un nombre à des fins, par exemple, pour effectuer analytique vocale, ou exposées pour moteurs de recherche comme Bing, Google ou Microsoft SharePoint pour les fichiers multimédias plus identifiable ou même utilisé pour diffuser des annonces plus pertinentes.
__JobResult.txt__ |Manifeste de sortie, présenter uniquement lors de l’indexation de plusieurs fichiers, contenant les informations suivantes :<br/><br/><table border="1"><tr><th>Fichier d’entrée</th><th>Alias</th><th>MediaLength</th><th>Erreur</th></tr><tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/>



Si ce n’est pas tous les fichiers multimédias d’entrée sont indexés avec succès, le travail d’indexation échoue avec le code d’erreur et 4 000. Pour plus d’informations, voir [codes d’erreur](#error_codes).

## <a name="index-multiple-files"></a>Plusieurs fichiers d’index

La méthode suivante télécharge plusieurs fichiers multimédias en tant qu’actif et crée une tâche pour tous ces fichiers dans un lot d’index.

Un fichier manifeste avec l’extension .lst est créé et chargés dans l’actif. Le fichier manifeste contient la liste de tous les fichiers de biens. Pour plus d’informations, voir [Tâches prédéfinis pour Azure Media indexeur](https://msdn.microsoft.com/library/dn783454.aspx).

    static bool RunBatchIndexingJob(string[] inputMediaFiles, string outputFolder)
    {
        // Create an asset and upload to storage.
        IAsset asset = CreateAssetAndUploadMultipleFiles(inputMediaFiles,
            "My Indexing Input Asset - Batch Mode",
            AssetCreationOptions.None);

        // Create a manifest file that contains all the asset file names and upload to storage.
        string manifestFile = "input.lst";            
        File.WriteAllLines(manifestFile, asset.AssetFiles.Select(f => f.Name).ToArray());
        var assetFile = asset.AssetFiles.Create(Path.GetFileName(manifestFile));
        assetFile.Upload(manifestFile);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job - Batch Mode");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration.
        string configuration = File.ReadAllText("batch.config");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task - Batch Mode",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset - Batch Mode", AssetCreationOptions.None);

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
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    private static IAsset CreateAssetAndUploadMultipleFiles(string[] filePaths, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        foreach (string filePath in filePaths)
        {
            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);
        }

        return asset;
    }

### <a name="partially-succeeded-job"></a>Travail partiellement réussi

Si ce n’est pas tous les fichiers multimédias d’entrée sont indexés avec succès, le travail d’indexation échoue avec le code d’erreur et 4 000. Pour plus d’informations, voir [codes d’erreur](#error_codes).


Les mêmes sorties (comme des travaux a réussi) sont générées. Vous pouvez faire référence au fichier manifeste de sortie pour déterminer quels fichiers d’entrée sont a échoué, en fonction des valeurs de colonne d’erreur. Des fichiers d’entrée qui a échoué, la AIB résultante, Same, TTML, WebVTT et mot clé fichiers ne seront pas générés.

### <a id="preset"></a>Tâche prédéfini pour Indexer de Media Azure

Le traitement d’Azure Media indexeur peut être personnalisé en fournissant une tâche facultative prédéfinie à côté de la tâche.  Voici le format de fichier xml de cette configuration.

Nom | Exiger | Description
----|----|---
__entrée__ | faux | Actif ou les fichiers que vous voulez indexer.</p><p>Indexer de Media Azure prend en charge les formats de fichier multimédia suivants : MP4, WMV, MP3, M4A, WMA, AAC, WAV.</p><p>Vous pouvez spécifier le nom du fichier (s) dans l’attribut **nom** ou une **liste** de l’élément **d’entrée** (comme illustré ci-dessous). Si vous ne spécifiez pas le fichier actif à index, le fichier primaire est sélectionné. Si aucun fichier principal n’est définie, le premier fichier de la ressource d’entrée est indexé.</p><p>Pour spécifier explicitement le nom du fichier actif, effectuez :<br />`<input name="TestFile.wmv">`<br /><br />Vous pouvez également effectuer l’indexation plusieurs biens fichiers en même temps (jusqu'à 10). Pour cela :<br /><br /><ol class="ordered"><li><p>Créer un fichier texte (fichier manifest) et lui donner une extension .lst. </p></li><li><p>Ajouter une liste de tous les noms de fichier bien dans vos biens d’entrée à ce fichier manifest. </p></li><li><p>Ajouter (télécharger) le fichier thanifest à l’actif.  </p></li><li><p>Spécifiez le nom du fichier manifeste dans l’attribut de liste de l’entrée.<br />`<input list="input.lst">`</li></ol><br /><br />Remarque : Si vous ajoutez plus de 10 fichiers au fichier manifeste, la tâche d’indexation échoue avec le code d’erreur 2006.
__métadonnées__ | faux | Métadonnées pour l’ou les fichiers bien spécifié utilisé pour l’Adaptation vocabulaire.  Utiles préparer indexeur pour reconnaître les mots non standard vocabulaire tels que des noms propres.<br />`<metadata key="..." value="..."/>` <br /><br />Vous pouvez fournir des __valeurs__ pour prédéfinies __clés__. Les clés suivantes sont actuellement prises en charge :<br /><br />« titre » et « description » - permet de modifier la langue pour l’adaptation vocabulaire modèle pour votre travail et améliorent la précision de la reconnaissance vocale.  Les valeurs amorcer recherches sur Internet pour accéder aux documents de texte de votre choix en fonction du contexte, à l’aide du contenu pour augmenter le dictionnaire interne pendant la durée de la tâche d’indexation.<br />`<metadata key="title" value="[Title of the media file]" />`<br />`<metadata key="description" value="[Description of the media file] />"`
__fonctionnalités__ <br /><br /> Ajouté dans la version 1.2. Pour l’instant, la fonctionnalité de prise en charge uniquement est la reconnaissance vocale (« récupération automatique du système »).| faux | La fonctionnalité de reconnaissance vocale comporte les clés de paramètres suivantes :<table><tr><th><p>Clé</p></th>     <th><p>Description</p></th><th><p>Exemple de valeur</p></th></tr><tr><td><p>Langue</p></td><td><p>Langage naturel à reconnue dans le fichier multimédia.</p></td><td><p>Anglais, espagnol</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>une liste délimitée par des points-virgules des formats de légende souhaitée (le cas échéant)</p></td><td><p>ttml ; Same ; webvtt</p></td></tr><tr><td><p>GenerateAIB</p></td><td><p>Un indicateur booléen indiquant si un fichier AIB est requis (pour une utilisation avec SQL Server et le client IFilter indexeur).  Pour plus d’informations, voir <a href="http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/">Utilisation de fichiers AIB avec Azure Media indexeur et SQL Server</a>.</p></td><td><p>Vrai ; Faux</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>Un indicateur booléen indiquant si un fichier XML de mot clé est requis.</p></td><td><p>Vrai ; False. </p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>Indicateur booléen spécifiant forcer légendes complètes (quelle que soit le niveau de confiance) ou non.  </p><p>Par défaut est false, auquel cas les mots et les phrases qui ont une niveau de confiance 50 % moins sont omis des sorties légende final et remplacés par les points de suspension («... »).  Les points de suspension sont utiles pour l’audit et de contrôle de la qualité de légende.</p></td><td><p>Vrai ; False. </p></td></tr></table>

### <a id="error_codes"></a>Codes d’erreur

Dans le cas d’une erreur signalez Azure Media indexeur sauvegarder un des codes d’erreur suivants :

Code | Nom | Raisons possibles
-----|------|------------------
2000 | Configuration non valide | Configuration non valide
2001 | Éléments d’entrée non valides | Éléments d’entrée, ou bien vide manquante.
2002 | Manifeste non valide | Manifeste est vide ou manifeste contient des éléments non valides.
2003 | Échec du téléchargement du fichier multimédia | URL non valide dans le fichier manifeste.
2004 | Protocole non pris en charge | Protocole d’URL media n’est pas pris en charge.
2005 | Type de fichier non pris en charge | Type de fichier multimédia d’entrée n’est pas pris en charge.
2006 | Trop de fichiers d’entrée | Il existe plus de 10 fichiers dans le manifeste d’entrée.
3000 | Échec du décodage fichier multimédia | Codec de média non pris en charge <br/>ou<br/> Fichier multimédia endommagé <br/>ou<br/> Aucun flux audio dans média d’entrée.
et 4 000 | Indexation partiellement réussie | Certains fichiers multimédia d’entrée sont échec à indexer. Pour plus d’informations, voir <a href="#output_files">fichiers de sortie</a>.
autres | Erreurs internes | Contactez le support d’équipe. indexer@microsoft.com


## <a id="supported_languages"></a>Langues prises en charge

Pour l’instant, les langues anglais et en espagnol sont prises en charge. Pour plus d’informations, voir [le billet de blog version 1.2](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).


##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


## <a name="related-links"></a>Liens connexes

[Azure Media Services Analytique vue d’ensemble](media-services-analytics-overview.md)

[Utilisation de fichiers AIB avec Azure Media indexeur et SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)

[L’indexation de fichiers multimédias avec Azure Media indexeur 2 Preview](media-services-process-content-with-indexer2.md)
