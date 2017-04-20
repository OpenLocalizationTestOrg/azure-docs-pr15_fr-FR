
<properties 
    pageTitle="Gestion des biens et entités associées avec Media Services SDK .NET" 
    description="Découvrez comment gérer les biens et entités associées avec le Kit de développement de Services de support pour .NET." 
    authors="juliako" 
    manager="dwrede" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016"
    ms.author="juliako"/>


#<a name="managing-assets-and-related-entities-with-media-services-net-sdk"></a>Gestion des biens et entités associées avec Media Services SDK .NET


> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-manage-entities.md)
- [RESTE](media-services-rest-manage-entities.md)


Cette rubrique vous explique comment effectuer les tâches de gestion des Services de support suivantes :

- Obtenir une référence de biens 
- Obtenir une référence de la tâche 
- Liste toutes les ressources 
- Liste tâches et ressources 
- Liste toutes les stratégies d’accès 
- Liste tous les repères
- Énumérer les grands ensembles d’entités
- Supprimer un actif 
- Supprimer une tâche 
- Supprimer une stratégie d’accès 

##<a name="prerequisites"></a>Conditions préalables 

Voir [configurer votre environnement](media-services-set-up-computer.md)

##<a name="get-an-asset-reference"></a>Obtenir une référence de biens

Une tâche fréquente consiste à obtenir une référence à une ressource existante dans Media Services. L’exemple suivant montre comment vous pouvez obtenir une référence de biens à partir de la collection de ressources sur le serveur objet contexte, basé sur un bien identifiant.
L’exemple suivant utilise une requête Linq pour obtenir une référence à un objet IAsset existant.

    static IAsset GetAsset(string assetId)
    {
        // Use a LINQ Select query to get an asset.
        var assetInstance =
            from a in _context.Assets
            where a.Id == assetId
            select a;
        // Reference the asset as an IAsset.
        IAsset asset = assetInstance.FirstOrDefault();
    
        return asset;
    }

##<a name="get-a-job-reference"></a>Obtenir une référence de la tâche

Lorsque vous travaillez avec traitement des tâches dans le code de Media Services, vous devez souvent obtenir une référence à une tâche existante basée sur un ID. L’exemple suivant montre comment obtenir une référence à un objet IJob à partir de la collection de tâches.
AvertissementBoîte nécessaires obtenir une référence de la tâche lors du démarrage d’une tâche de codage longue et vous voulez vérifier l’état du travail sur un thread. Dans ce cas, lorsque la méthode retourne une valeur à partir d’un thread, vous devez récupérer une référence actualisée à une tâche.

    static IJob GetJob(string jobId)
    {
        // Use a Linq select query to get an updated 
        // reference by Id. 
        var jobInstance =
            from j in _context.Jobs
            where j.Id == jobId
            select j;
        // Return the job reference as an Ijob. 
        IJob job = jobInstance.FirstOrDefault();
    
        return job;
    }

##<a name="list-all-assets"></a>Liste toutes les ressources

Au fur et à mesure du nombre de biens dont vous disposez dans le stockage, il est utile de vos ressources de la liste. L’exemple suivant montre comment parcourir la collection de ressources sur l’objet de contexte de serveur. Avec chaque ressource, l’exemple de code enregistre également certaines de ses valeurs de propriété dans la console. Par exemple, chaque ressource peut contenir plusieurs fichiers multimédias. L’exemple de code écrit tous les fichiers associés liés chaque ressource.



    static void ListAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);
    
        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();
    
        foreach (IAsset asset in _context.Assets)
        {
            // Display the collection of assets.
            builder.AppendLine("");
            builder.AppendLine("******ASSET******");
            builder.AppendLine("Asset ID: " + asset.Id);
            builder.AppendLine("Name: " + asset.Name);
            builder.AppendLine("==============");
            builder.AppendLine("******ASSET FILES******");
    
            // Display the files associated with each asset. 
            foreach (IAssetFile fileItem in asset.AssetFiles)
            {
                builder.AppendLine("Name: " + fileItem.Name);
                builder.AppendLine("Size: " + fileItem.ContentFileSize);
                builder.AppendLine("==============");
            }
        }
    
        // Display output in console.
        Console.Write(builder.ToString());
    }

##<a name="list-jobs-and-assets"></a>Liste tâches et ressources

Une tâche importante liée consiste aux éléments de liste avec leur tâche associée dans Media Services. L’exemple suivant vous montre comment répertorier chaque objet IJob et puis pour chaque tâche, il affiche les propriétés relatives à la tâche, toutes les tâches liées, toutes les entrées actifs et tous les éléments de sortie. Le code dans cet exemple peut être utile de nombreuses autres tâches. Par exemple, si vous souhaitez lister les biens de sortie à partir d’une ou plusieurs tâches d’encodage que vous avez précédemment exécuté, ce code montre comment accéder aux biens de sortie. Lorsque vous avez une référence à une ressource de sortie, vous pouvez ensuite diffuser le contenu vers d’autres utilisateurs ou les applications en téléchargeant, ou en fournissant URL. 

Pour plus d’informations sur les options permettant de biens, voir [Présenter des ressources avec le Kit de développement de Services de support pour .NET](media-services-deliver-streaming-content.md).

    // List all jobs on the server, and for each job, also list 
    // all tasks, all input assets, all output assets.

    static void ListJobsAndAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);
    
        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();
    
        foreach (IJob job in _context.Jobs)
        {
            // Display the collection of jobs on the server.
            builder.AppendLine("");
            builder.AppendLine("******JOB*******");
            builder.AppendLine("Job ID: " + job.Id);
            builder.AppendLine("Name: " + job.Name);
            builder.AppendLine("State: " + job.State);
            builder.AppendLine("Order: " + job.Priority);
            builder.AppendLine("==============");
    
    
            // For each job, display the associated tasks (a job  
            // has one or more tasks). 
            builder.AppendLine("******TASKS*******");
            foreach (ITask task in job.Tasks)
            {
                builder.AppendLine("Task Id: " + task.Id);
                builder.AppendLine("Name: " + task.Name);
                builder.AppendLine("Progress: " + task.Progress);
                builder.AppendLine("Configuration: " + task.Configuration);
                if (task.ErrorDetails != null)
                {
                    builder.AppendLine("Error: " + task.ErrorDetails);
                }
                builder.AppendLine("==============");
            }
    
            // For each job, display the list of input media assets.
            builder.AppendLine("******JOB INPUT MEDIA ASSETS*******");
            foreach (IAsset inputAsset in job.InputMediaAssets)
            {
    
                if (inputAsset != null)
                {
                    builder.AppendLine("Input Asset Id: " + inputAsset.Id);
                    builder.AppendLine("Name: " + inputAsset.Name);
                    builder.AppendLine("==============");
                }
            }
    
            // For each job, display the list of output media assets.
            builder.AppendLine("******JOB OUTPUT MEDIA ASSETS*******");
            foreach (IAsset theAsset in job.OutputMediaAssets)
            {
                if (theAsset != null)
                {
                    builder.AppendLine("Output Asset Id: " + theAsset.Id);
                    builder.AppendLine("Name: " + theAsset.Name);
                    builder.AppendLine("==============");
                }
            }
    
        }
    
        // Display output in console.
        Console.Write(builder.ToString());
    }

##<a name="list-all-access-policies"></a>Liste toutes les stratégies d’accès

Dans les Services de support, vous pouvez définir une stratégie d’accès sur un bien ou ses fichiers. Une stratégie d’accès définit les autorisations pour un fichier ou d’un bien (type d’accès, ainsi que la durée). Dans votre code Media Services, vous définissez généralement une stratégie d’accès en créant un objet IAccessPolicy et en associant un bien existant. Puis vous créez un objet ILocator, qui vous permet de fournir un accès direct aux ressources dans les Services de support. Le projet Visual Studio qui accompagne cette série de documentation contient plusieurs exemples de code qui montrent comment créer et affecter des stratégies d’accès et les repères actifs.

L’exemple suivant montre comment répertorier tous les stratégies d’accès sur le serveur et affiche le type d’autorisations associés à chaque. Une autre façon utile pour afficher les stratégies d’accès consiste à répertorier tous les objets ILocator sur le serveur, et ensuite pour chaque locator, vous pouvez répertorier sa stratégie d’accès associé à l’aide de sa propriété AccessPolicy.

    static void ListAllPolicies()
    {
        foreach (IAccessPolicy policy in _context.AccessPolicies)
        {
            Console.WriteLine("");
            Console.WriteLine("Name:  " + policy.Name);
            Console.WriteLine("ID:  " + policy.Id);
            Console.WriteLine("Permissions: " + policy.Permissions);
            Console.WriteLine("==============");
    
        }
    }

##<a name="list-all-locators"></a>Liste tous les repères

Un repère est une URL qui fournit un chemin d’accès direct pour accéder à un bien, ainsi que des autorisations de la ressource, telle que définie par la stratégie d’accès associé de l’URL. Chaque ressource peut avoir une collection d’objets ILocator associée sur sa propriété Locator. Le contexte de serveur possède également un ensemble de repères qui contient tous les repères.

L’exemple suivant répertorie tous les repères sur le serveur. Pour chaque locator, il affiche l’Id de la stratégie de biens et accès associée. Il affiche également le type d’autorisations, la date d’expiration et le chemin d’accès complet de la ressource.

Notez qu’un chemin d’accès locator à une ressource est uniquement une URL de base de la ressource. Pour créer un chemin d’accès direct aux fichiers individuels Parcourir pour un utilisateur ou une application, votre code devez ajouter le chemin d’accès de fichier spécifique dans le chemin d’accès locator. Pour plus d’informations sur la façon de procéder, voir la rubrique [Effectuer actifs avec le Kit de développement de Services de support pour .NET](media-services-deliver-streaming-content.md).

    static void ListAllLocators()
    {
        foreach (ILocator locator in _context.Locators)
        {
            Console.WriteLine("***********");
            Console.WriteLine("Locator Id: " + locator.Id);
            Console.WriteLine("Locator asset Id: " + locator.AssetId);
            Console.WriteLine("Locator access policy Id: " + locator.AccessPolicyId);
            Console.WriteLine("Access policy permissions: " + locator.AccessPolicy.Permissions);
            Console.WriteLine("Locator expiration: " + locator.ExpirationDateTime);
            // The locator path is the base or parent path (with included permissions) to access  
            // the media content of an asset. To create a full URL to a specific media file, take 
            // the locator path and then append a file name and info as needed.  
            Console.WriteLine("Locator base path: " + locator.Path);
            Console.WriteLine("");
        }
    }

## <a name="enumerating-through-large-collections-of-entities"></a>Énumérer les grands ensembles d’entités

Lorsque vous interrogez entités, il est limité à 1000 entités retournées en même temps, car public reste v2 limite les résultats de requête dans les résultats de 1000. Vous devez utiliser ignorer et prendre lorsque l’énumération dans les grands ensembles d’entités. 
    
La fonction suivante parcourt toutes les tâches dans le compte de Services de support fourni. Media Services renvoie 1000 travaux de Collection de tâches. La fonction utilise les ignorer et prendre pour vous assurer que toutes les tâches sont énumérés (au cas où vous avez plus de 1000 tâches dans votre compte).
    
    static void ProcessJobs()
    {
        try
        {
    
            int skipSize = 0;
            int batchSize = 1000;
            int currentBatch = 0;
    
            while (true)
            {
                // Loop through all Jobs (1000 at a time) in the Media Services account
                IQueryable _jobsCollectionQuery = _context.Jobs.Skip(skipSize).Take(batchSize);
                foreach (IJob job in _jobsCollectionQuery)
                {
                    currentBatch++;
                    Console.WriteLine("Processing Job Id:" + job.Id);
                }
    
                if (currentBatch == batchSize)
                {
                    skipSize += batchSize;
                    currentBatch = 0;
                }
                else
                {
                    break;
                }
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
        }
    }

##<a name="delete-an-asset"></a>Supprimer un actif

L’exemple suivant supprime un bien.

    static void DeleteAsset( IAsset asset)
    {
        // delete the asset
        asset.Delete();
    
        // Verify asset deletion
        if (GetAsset(asset.Id) == null)
            Console.WriteLine("Deleted the Asset");
    
    }

##<a name="delete-a-job"></a>Supprimer une tâche

Pour supprimer une tâche, vous devez vérifier l’état de la tâche comme indiqué dans la propriété état. Tâches qui sont terminées ou annulés peuvent être supprimés, tandis que les tâches qui se trouvent dans certains autres États, par exemple en file d’attente, planifiée ou traitement, doivent être annulées tout d’abord, puis elles peuvent être supprimées.
L’exemple suivant montre une méthode pour supprimer une tâche en vérifiant États de travail et supprimer puis lorsque l’état est terminée ou annulée. Ce code dépend de la section précédente dans cette rubrique pour obtenir une référence à une tâche : obtenir une référence de la tâche.

    static void DeleteJob(string jobId)
    {
        bool jobDeleted = false;
    
        while (!jobDeleted)
        {
            // Get an updated job reference.  
            IJob job = GetJob(jobId);
    
            // Check and handle various possible job states. You can 
            // only delete a job whose state is Finished, Error, or Canceled.   
            // You can cancel jobs that are Queued, Scheduled, or Processing,  
            // and then delete after they are canceled.
            switch (job.State)
            {
                case JobState.Finished:
                case JobState.Canceled:
                case JobState.Error:
                    // Job errors should already be logged by polling or event 
                    // handling methods such as CheckJobProgress or StateChanged.
                    // You can also call job.DeleteAsync to do async deletes.
                    job.Delete();
                    Console.WriteLine("Job has been deleted.");
                    jobDeleted = true;
                    break;
                case JobState.Canceling:
                    Console.WriteLine("Job is cancelling and will be deleted "
                        + "when finished.");
                    Console.WriteLine("Wait while job finishes canceling...");
                    Thread.Sleep(5000);
                    break;
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    job.Cancel();
                    Console.WriteLine("Job is scheduled or processing and will "
                        + "be deleted.");
                    break;
                default:
                    break;
            }
    
        }
    }


##<a name="delete-an-access-policy"></a>Supprimer une stratégie d’accès

L’exemple suivant montre comment obtenir une référence à une stratégie d’accès basée sur une stratégie Id, puis à supprimer de la stratégie.

    static void DeleteAccessPolicy(string existingPolicyId)
    {
        // To delete a specific access policy, get a reference to the policy.  
        // based on the policy Id passed to the method.
        var policyInstance =
                from p in _context.AccessPolicies
                where p.Id == existingPolicyId
                select p;
        IAccessPolicy policy = policyInstance.FirstOrDefault();
    
        policy.Delete();
    
    }
    


##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
