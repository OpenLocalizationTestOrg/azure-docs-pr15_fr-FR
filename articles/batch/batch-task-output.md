<properties
    pageTitle="Projet et la tâche de sortie persistance dans Azure lot | Microsoft Azure"
    description="Découvrez comment utiliser le stockage Azure comme un magasin résistant pour votre tâche et le travail de sortie et activer l’affichage de ce résultat persistant dans le portail Azure."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor="" />

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="big-compute"
    ms.date="09/07/2016"
    ms.author="marsma" />

# <a name="persist-azure-batch-job-and-task-output"></a>Conserver la sortie de projet et la tâche lot Azure

Les tâches que vous exécutez généralement en lot génèrent une sortie qui doit être stockée et récupérée ultérieurement par d’autres tâches dans la tâche, l’application cliente qui exécute la tâche, ou les deux. Ce résultat est peut-être les fichiers créés par la transformation de données d’entrée ou fichiers journaux associés à l’exécution des tâches. Cet article présente une bibliothèque de classes .NET qui utilise une technique basée sur les conventions pour conserver les telle sortie de tâche vers le stockage Blob Azure, rendre disponible même une fois que vous supprimez des pools, tâches et les nœuds de calcul.

À l’aide de la technique dans cet article, vous pouvez également afficher votre sortie de tâche dans **les journaux enregistrée** dans le [portail Azure]et de **fichiers de sortie Saved** [portal].

![Fichiers de sortie Saved et sélecteurs de journaux enregistrées dans le portail][1]

>[AZURE.NOTE] Les [Conventions des fichiers par lots Azure] [ nuget_package] bibliothèque de classes .NET mentionnée dans cet article est en cours d’aperçu. Certaines des fonctionnalités décrites ici peuvent changer avant la disponibilité générale.

## <a name="task-output-considerations"></a>Considérations relatives à la tâche sortie

Lorsque vous créez votre solution lot, vous devez prendre en compte plusieurs facteurs liées à des sorties de projet et la tâche.

* **Calculer la durée de vie nœud**: calculer les nœuds sont souvent transitoires, notamment en pools compatibles avec échelle. Les sorties des tâches qui s’exécutent sur un nœud sont disponibles uniquement lorsque le nœud existe, et uniquement au sein de la période de rétention que vous avez définie pour la tâche. Pour vous assurer que le résultat de la tâche est conservé, vos tâches doivent donc télécharger ses fichiers de sortie dans un magasin résistant, par exemple, le stockage Azure.

* **Stockage de sortie**: pour conserver les données de sortie de tâche vers le stockage résistant, vous pouvez utiliser le [Kit de développement de stockage Azure](../storage/storage-dotnet-how-to-use-blobs.md) dans votre code de tâche pour télécharger le résultat de la tâche dans un conteneur de stockage Blob. Si vous implémentez un conteneur et un fichier convention d’affectation, votre application cliente ou autres tâches dans la tâche peuvent puis localisez et téléchargez ce résultat en fonction de la convention.

* **Récupération de sortie**: vous pouvez récupérer sortie des tâches directement à partir des nœuds de calcul dans le pool ou à partir du stockage Azure si vos tâches persistent leur sortie. Pour récupérer la sortie d’une tâche directement à partir d’un nœud de calcul, vous devez le nom de fichier et son emplacement de sortie sur le nœud. Si vous persistent sortie au stockage Azure, tâches en aval ou votre application cliente doit avoir le chemin d’accès complet au fichier dans le stockage Azure pour la télécharger à l’aide du Kit de développement de stockage Azure.

* **Affichage de sortie**: lorsque vous accédez à une tâche par lots dans le portail Azure et sélectionnez **les fichiers dans le nœud**, vous sont présentées avec tous les fichiers associés à la tâche, pas seulement les fichiers de sortie qui vous intéresse. Là encore, fichiers sur des nœuds de calcul sont disponibles uniquement pendant que le nœud existe et uniquement au sein de la période de rétention que vous avez définie pour la tâche. Pour afficher le résultat de la tâche que vous avez conservé au stockage Azure dans le portail ou d’une application telle que l' [Explorateur de stockage Azure][storage_explorer], vous devez connaître son emplacement et accéder directement au fichier.

## <a name="help-for-persisted-output"></a>Aide pour la sortie persistante

Pour vous aider à plusieurs persistent facilement travail et sortie de tâche, l’équipe du lot a défini et mise en œuvre d’un jeu de conventions d’affectation de noms, ainsi que d’une bibliothèque de classes .NET, les [Conventions des fichiers par lots Azure] [ nuget_package] bibliothèque, que vous pouvez utiliser dans vos applications lot. En outre, le portail Azure connaît ces conventions d’affectation de noms afin que vous pouvez trouver facilement les fichiers que vous avez stockés à l’aide de la bibliothèque.

## <a name="using-the-file-conventions-library"></a>À l’aide de la bibliothèque de conventions de fichiers

[Conventions des fichiers par lots Azure] [ nuget_package] est une bibliothèque de classes .NET vos applications .NET lot peuvent utiliser pour stocker et récupérer des sorties de tâche vers et depuis le stockage Azure facilement. Il est destiné à utiliser dans le code de tâche et client--dans le code de tâche pour conserver les fichiers et dans le code client à la liste et de les récupérer. Vos tâches peuvent également utiliser la bibliothèque pour récupérer les sorties des tâches en amont, comme dans un scénario de [interdépendances de tâches](batch-task-dependencies.md) .

La bibliothèque conventions s’occupe de s’assurer que tâche conteneurs de stockage de sortie et fichiers sont nommées selon la convention et sont téléchargés vers l’emplacement approprié lorsque conservé au stockage Azure. Lorsque vous récupérez des sorties, vous retrouver facilement les sorties pour une tâche donnée ou d’une tâche en attribuant ou en récupérant sorties par ID et de fin, au lieu de devoir connaître les noms de fichiers ou où il se trouve dans le stockage.

Par exemple, vous pouvez utiliser la bibliothèque pour « de la liste tous les fichiers intermédiaires pour tâche 7 » ou « obtenir me l’image miniature pour travail *mymovie*, » sans connaître les noms de fichier ou un emplacement au sein de votre compte de stockage.

### <a name="get-the-library"></a>Obtenir la bibliothèque

Vous pouvez obtenir la bibliothèque qui contient des nouvelles classes et étend la [CloudJob] [ net_cloudjob] et [CloudTask] [ net_cloudtask] classes avec de nouvelles méthodes de [NuGet][nuget_package]. Vous pouvez l’ajouter à votre projet Visual Studio en utilisant le [Gestionnaire de Package NuGet bibliothèque][nuget_manager].

>[AZURE.TIP] Vous pouvez trouver le [code source] [ github_file_conventions] pour la bibliothèque Conventions des fichiers par lots Azure sur GitHub dans le Kit de développement Microsoft Azure pour référentiel .NET.

## <a name="requirement-linked-storage-account"></a>Exigence : compte de stockage liées

Pour stocker des sorties au stockage résistant à l’aide de la bibliothèque de conventions de fichiers et les afficher dans le portail Azure, vous devez [lier un compte de stockage Azure](batch-application-packages.md#link-a-storage-account) à votre compte lot. Si vous n’avez pas déjà, lier un compte de stockage à votre compte lot à l’aide du portail Azure :

Carte de **compte lot** > **paramètres** > **Compte de stockage** > **Compte de stockage** (aucun) > Sélectionner un compte de stockage dans votre abonnement

Pour une vue d’ensemble plus détaillée sur la liaison d’un compte de stockage, voir [déploiement d’applications avec Azure lot des packages d’application](batch-application-packages.md).

## <a name="persist-output"></a>Conserver la sortie

Il existe deux actions principales à effectuer lors de l’enregistrement de sortie au projet et la tâche avec la bibliothèque de conventions fichiers : créer le conteneur de stockage et enregistrer le résultat dans le conteneur.

>[AZURE.WARNING] Étant donné que toutes les sorties de projet et la tâche sont stockées dans le même conteneur, [stockage la limitation limites](../storage/storage-performance-checklist.md#blobs) peut appliquée si vous essayez d’un grand nombre de tâches conserver les fichiers en même temps.

### <a name="create-storage-container"></a>Créer le conteneur de stockage

Avant de vos tâches commencent conservation sortie au stockage, vous devez créer un conteneur de stockage blob auquel ils allez télécharger leur sortie. Pour ce faire, appel [CloudJob][net_cloudjob]. [PrepareOutputStorageAsync] [net_prepareoutputasync]. Cette méthode d’extension prend une [CloudStorageAccount] [ net_cloudstorageaccount] de l’objet en tant que paramètre et crée un conteneur nommé de façon à ce que son contenu est détectable par le portail Azure et les méthodes d’extraction traitées plus loin dans cet article.

En règle générale, vous placez ce code dans votre application cliente--l’application qui crée des pools, des tâches et des tâches.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob(
    "myJob",
    new PoolInformation { PoolId = "myPool" });

// Create reference to the linked Azure Storage account
CloudStorageAccount linkedStorageAccount =
    new CloudStorageAccount(myCredentials, true);

// Create the blob storage container for the outputs
await job.PrepareOutputStorageAsync(linkedStorageAccount);
```

### <a name="store-task-outputs"></a>Stocker des sorties de tâche

À présent que vous avez préparé un conteneur dans le stockage blob, tâches peuvent enregistrer la sortie au conteneur à l’aide de la [TaskOutputStorage] [ net_taskoutputstorage] cours figurant dans la bibliothèque de conventions de fichiers.

Dans votre code des tâches, tout d’abord créer une [TaskOutputStorage] [ net_taskoutputstorage] objet, puis lorsque la tâche a terminé son travail, appelez le [TaskOutputStorage][net_taskoutputstorage]. [SaveAsync] [net_saveasync] méthode pour enregistrer le résultat au stockage Azure.

```csharp
CloudStorageAccount linkedStorageAccount = new CloudStorageAccount(myCredentials);
string jobId = Environment.GetEnvironmentVariable("AZ_BATCH_JOB_ID");
string taskId = Environment.GetEnvironmentVariable("AZ_BATCH_TASK_ID");

TaskOutputStorage taskOutputStorage = new TaskOutputStorage(
    linkedStorageAccount, jobId, taskId);

/* Code to process data and produce output file(s) */

await taskOutputStorage.SaveAsync(TaskOutputKind.TaskOutput, "frame_full_res.jpg");
await taskOutputStorage.SaveAsync(TaskOutputKind.TaskPreview, "frame_low_res.jpg");
```

Le paramètre « type de sortie » pour classer les fichiers persistantes. Il existe quatre prédéfinies [TaskOutputKind] [ net_taskoutputkind] types : « TaskOutput », « TaskPreview », « TaskLog » et « TaskIntermediate ». Vous pouvez également définir des types personnalisés si elles peuvent être utiles dans votre flux de travail.

Ces types de sortie permettent de spécifier le type de sortie à la liste lorsque vous recherchez plus tard lot des sorties persistantes d’une tâche donnée. En d’autres termes, lorsque vous mettez les sorties pour une tâche, vous pouvez filtrer la liste sur l’un des types de sortie. Par exemple, « me donner la sortie *d’Aperçu* pour tâche *109*. » Plus dans la liste et l’extraction des sorties d’apparaît dans [récupérer sortie](#retrieve-output) plus loin dans l’article.

>[AZURE.TIP] Le type de sortie désigne également où un fichier particulier s’affiche dans le portail Azure : *TaskOutput*-fichiers classés s’affichent dans « Fichiers de sortie de tâche » et les fichiers *TaskLog* apparaissent dans « Journaux des tâches ».

### <a name="store-job-outputs"></a>Stocker des sorties de tâche

Outre le stockage des sorties de tâche, vous pouvez stocker les sorties associés à l’intégralité du travail. Par exemple, dans la tâche de fusion d’un travail de rendu vidéo, vous pourriez conserver le film entièrement rendu comme une sortie des tâches. Lorsque votre tâche est terminée, votre application cliente pouvez Dressez la liste et extraire les sorties pour le projet et n’a pas besoin interroger des tâches individuelles.

Stocker de sortie des tâches en appelant le [JobOutputStorage][net_joboutputstorage]. [SaveAsync] [net_joboutputstorage_saveasync] méthode, et spécifiez le [JobOutputKind] [ net_joboutputkind] et nom de fichier :

```
CloudJob job = await batchClient.JobOperations.GetJobAsync(jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Comme avec TaskOutputKind pour sorties de tâche, vous utilisez la [JobOutputKind] [ net_joboutputkind] paramètre à classer une tâche est conservé fichiers. Ce paramètre vous permet de requête ultérieure pour (liste) d’un type spécifique de sortie. La JobOutputKind inclut les types de sortie et aperçu et prend en charge la création de types personnalisés.

### <a name="store-task-logs"></a>Stocker les journaux des tâches

En plus de conserver un fichier vers un stockage résistant lorsqu’une tâche ou une tâche est terminée, il peut s’avérer nécessaire pour conserver les fichiers qui sont mis à jour lors de l’exécution d’une tâche, les fichiers journaux ou `stdout.txt` et `stderr.txt`, par exemple. À cet effet, la bibliothèque Conventions des fichiers par lots Azure fournit la [TaskOutputStorage][net_taskoutputstorage]. [SaveTrackedAsync] [net_savetrackedasync] méthode. Avec [SaveTrackedAsync][net_savetrackedasync], vous pouvez effectuer le suivi des mises à jour dans un fichier sur le nœud (à un intervalle que vous spécifiez) et conserver les mises à jour au stockage Azure.

Dans l’extrait de code suivantes, nous utilisons [SaveTrackedAsync] [ net_savetrackedasync] pour mettre à jour `stdout.txt` dans le stockage Azure toutes les 15 secondes pendant l’exécution de la tâche :

```csharp
TimeSpan stdoutFlushDelay = TimeSpan.FromSeconds(3);
string logFilePath = Path.Combine(
    Environment.GetEnvironmentVariable("AZ_BATCH_TASK_DIR"), "stdout.txt");

// The primary task logic is wrapped in a using statement that sends updates to
// the stdout.txt blob in Storage every 15 seconds while the task code runs.
using (ITrackedSaveOperation stdout =
        await taskStorage.SaveTrackedAsync(
        TaskOutputKind.TaskLog,
        logFilePath,
        "stdout.txt",
        TimeSpan.FromSeconds(15)))
{
    /* Code to process data and produce output file(s) */

    // We are tracking the disk file to save our standard output, but the
    // node agent may take up to 3 seconds to flush the stdout stream to
    // disk. So give the file a moment to catch up.
    await Task.Delay(stdoutFlushDelay);
}
```

`Code to process data and produce output file(s)`est simplement un espace réservé pour le code de la tâche, exécutées normalement. Par exemple, vous devrez code qui télécharge des données à partir du stockage Azure et effectue une transformation ou calcul dessus. L’essentiel de cet extrait de code est montrant comment vous pouvez placer ce code dans un `using` bloc pour mettre à jour régulièrement un fichier avec [SaveTrackedAsync][net_savetrackedasync].

La `Task.Delay` est nécessaire à la fin de cet `using` bloc pour vous assurer que l’agent de nœud temps vider le contenu de la sortie standard vers le fichier stdout.txt sur le nœud (l’agent de nœud est un programme qui s’exécute sur chaque nœud dans le pool et fournit l’interface de commande et de contrôle entre le nœud et le service lot). Immédiatement, il est possible de manquer les dernières secondes de sortie. Ce délai peut ne pas être nécessaire pour tous les fichiers.

>[AZURE.NOTE] Lorsque vous activez le suivi avec SaveTrackedAsync de fichier, uniquement *ajoute* au fichier de suivi sont conservées au stockage Azure. Utilisez cette méthode uniquement pour les fichiers journaux en rotation ou autres fichiers qui sont ajoutées, c'est-à-dire, les données de suivi est ajoutée uniquement à la fin du fichier lorsqu’il est mis à jour.

## <a name="retrieve-output"></a>Récupérer sortie

Lorsque vous récupérez votre sortie persistante à l’aide de la bibliothèque Conventions des fichiers par lots Azure, faire d’une manière centré sur la tâche et à la tâche. Vous pouvez demander la sortie pour tâche ou une tâche sans avoir à connaître son chemin d’accès dans le stockage blob, ou encore son nom de fichier. Vous pouvez simplement dire, « Donnez-moi fichiers de sortie de tâche *109*. »

L’extrait de code suivant parcourt tous les tâches d’un projet, imprime certaines informations sur les fichiers de sortie de la tâche, puis télécharge ses fichiers à partir du stockage.

```csharp
foreach (CloudTask task in myJob.ListTasks())
{
    foreach (TaskOutputStorage output in
        task.OutputStorage(storageAccount).ListOutputs(
            TaskOutputKind.TaskOutput))
    {
        Console.WriteLine($"output file: {output.FilePath}");

        output.DownloadToFileAsync(
            $"{jobId}-{output.FilePath}",
            System.IO.FileMode.Create).Wait();
    }
}
```

## <a name="task-outputs-and-the-azure-portal"></a>Sorties de tâche et le portail Azure

Le portail Azure affiche des sorties de tâche et des journaux qui sont conservées à un compte de stockage Azure lié en utilisant les conventions d’appellation figurant dans la [Azure lot fichier Conventions Lisez-moi][github_file_conventions_readme]. Vous pouvez implémenter vous-même ces conventions dans une langue de votre choix, ou vous pouvez utiliser la bibliothèque de conventions de fichiers dans vos applications .NET.

### <a name="enable-portal-display"></a>Activer l’affichage du portail

Pour activer l’affichage de votre sorties dans le portail, vous devez répondre aux conditions suivantes :

 1. [Lier un compte de stockage Azure](#requirement-linked-storage-account) à votre compte lot.
 2. Respecter les conventions d’appellation prédéfinies pour les conteneurs de stockage et de fichiers lors de la conservation des sorties. Vous pouvez trouver la définition de ces conventions dans la bibliothèque de conventions fichier [Lisez-moi][github_file_conventions_readme]. Si vous utilisez les [Conventions des fichiers par lots Azure] [ nuget_package] bibliothèque pour conserver votre sortie, cette condition est remplie.

### <a name="view-outputs-in-the-portal"></a>Affichage des sorties dans le portail

Pour afficher des sorties de tâche et des journaux dans le portail Azure, accédez à la tâche dont la sortie vous intéressez, puis cliquez sur **fichiers de sortie Saved** ou **des journaux enregistrés**. Cette image montre les **fichiers de sortie Saved** de la tâche avec l’ID « 007 » :

![Carte des sorties de tâche dans le portail Azure][2]

## <a name="code-sample"></a>Exemple de code

La [PersistOutputs] [ github_persistoutputs] exemple de projet est un des [exemples de code lot Azure] [ github_samples] sur GitHub. Cette solution Visual Studio 2015 montre comment utiliser la bibliothèque Conventions des fichiers par lots Azure pour conserver la sortie de tâche vers le stockage résistant. Pour exécuter l’exemple, procédez comme suit :

1. Ouvrez le projet dans **Visual Studio 2015**.
2. Ajoutez vos **informations d’identification de compte** lot et stockage à **AccountSettings.settings** dans le projet Microsoft.Azure.Batch.Samples.Common.
3. **Créer** (mais ne sont pas exécutées) la solution. Restaurer les packages NuGet si vous y êtes invité.
4. Utiliser le portail Azure pour télécharger un [package d’application](batch-application-packages.md) pour **PersistOutputsTask**. Inclure la `PersistOutputsTask.exe` et ses assemblys dépendants dans le package .zip, définissez l’ID de l’application à « PersistOutputsTask » et la version de package d’application sur « 1.0 ».
5. **Démarrer** projet (exécuter) le **PersistOutputs** .

## <a name="next-steps"></a>Étapes suivantes

### <a name="application-deployment"></a>Déploiement des applications

La fonctionnalité [packages d’application](batch-application-packages.md) du lot offre un moyen facile à la fois à déployer et version les applications que vos tâches s’exécutent sur les nœuds de calcul.

### <a name="installing-applications-and-staging-data"></a>Installer des applications et mise en attente

Consultez les [nœuds de calcul installation applications et données intermédiaires sur lot] [ forum_post] publier sur le forum Azure lot Découvrez les différentes méthodes de préparation vos nœuds d’exécution des tâches. Rédigé par un des membres de l’équipe Azure lot, ce billet est une bonne notions fondamentales sur les différentes façons d’obtenir les fichiers (y compris les applications et les données d’entrée de tâche) sur des nœuds de votre cluster et considérations particulières pour chaque méthode.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Batch/FileConventions
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[net_joboutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputkind.aspx
[net_joboutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.aspx
[net_joboutputstorage_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.saveasync.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_prepareoutputasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.cloudjobextensions.prepareoutputstorageasync.aspx
[net_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx
[net_savetrackedasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.savetrackedasync.aspx
[net_taskoutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputkind.aspx
[net_taskoutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx
[nuget_manager]: https://docs.nuget.org/consume/installing-nuget
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: http://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "Fichiers de sortie Saved et sélecteurs de journaux enregistrées dans le portail"
[2]: ./media/batch-task-output/task-output-02.png "Carte des sorties de tâche dans le portail Azure"