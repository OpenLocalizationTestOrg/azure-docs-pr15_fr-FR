<properties
    pageTitle="Exécuter des applications MPI dans Azure lot à effectuer les tâches plusieurs instances | Microsoft Azure"
    description="Apprenez à exécuter des applications en passant Interface MPI (Message) en utilisant le type de tâche plusieurs instances lot Azure."
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
    ms.date="10/21/2016"
    ms.author="marsma" />

# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-azure-batch"></a>Utilisez les tâches de plusieurs instances pour exécuter des applications en passant Interface MPI (Message) en lot Azure

Plusieurs instances options vous permettent d’exécuter une tâche Azure lot sur plusieurs nœuds de calcul simultanément. Ces tâches activer haute performance computing scénarios comme Message transmission des applications MPI (Interface) dans le lot. Dans cet article, vous apprenez à exécuter plusieurs instances des tâches à l’aide du [Lot .NET] [ api_net] bibliothèque.

>[AZURE.NOTE] Tandis que les exemples dans cet article vous concentrer sur lot .NET, MS-MPI, et Windows les nœuds de calcul, les concepts de tâche plusieurs instances mentionnés dans cet article sont appliquent à d’autres plateformes et technologies (Python et MPI Intel sur des nœuds Linux, par exemple).

## <a name="multi-instance-task-overview"></a>Vue d’ensemble des tâches de plusieurs instances

Dans le lot, chaque tâche est normalement exécuté sur un nœud unique cluster--vous envoyez plusieurs tâches à une tâche, et le service lot planifie chaque tâche pour une exécution sur un nœud. Toutefois, en configurant les **paramètres plusieurs instances**d’une tâche, vous indiquez lot à la place créer une tâche principale et plusieurs tâches subordonnées sont ensuite exécutées sur plusieurs nœuds.

![Vue d’ensemble des tâches de plusieurs instances][1]

Lorsque vous envoyez une tâche avec les paramètres de plusieurs instances à une tâche, lot effectue plusieurs étapes uniques aux tâches plusieurs instances :

1. Le service lot crée un **principal** et plusieurs **tâches subordonnées** basé sur les paramètres de plusieurs instances. Le nombre total de tâches (principales ainsi que toutes les tâches subordonnées) représente le nombre d' **instances** (nœuds de calcul) que vous spécifiez dans les paramètres de plusieurs instances.
1. Lot désigne l’un des nœuds de calcul en tant que la **base**et planifie la tâche principale pour s’exécuter sur la forme de base. Elle planifie les tâches subordonnées à s’exécuter sur le reste des nœuds cluster affectée à la tâche plusieurs instances, une tâche subordonnée par nœud.
1. Principale et toutes les tâches subordonnées le téléchargement de **fichiers de ressources communs** que vous spécifiez dans les paramètres de plusieurs instances.
1. Après la ressource courantes fichiers ont été téléchargés, le serveur principal et les tâches subordonnées exécuter la **commande débute** que vous spécifiez dans les paramètres de plusieurs instances. La commande débute est généralement utilisée pour préparer des nœuds pour l’exécution de la tâche. Il peut s’agir en exécutant les services d’arrière-plan (tels que [Microsoft MPI][msmpi_msdn]de `smpd.exe`) et vérifiez que les nœuds sont prêtes à traiter les messages de nœuds entre.
1. La première tâche exécute la **commande de l’application** sur le nœud maître *après* que la commande débute a été correctement effectuée par le serveur principal et toutes les tâches subordonnées. La commande de l’application est la ligne de commande de la tâche plusieurs instances lui-même et est exécutée uniquement par la tâche principale. Dans un [MS-MPI][msmpi_msdn]-en fonction de solution, il s’agit de l’endroit où vous exécutez votre application compatibles avec les MPI à l’aide `mpiexec.exe`.

> [AZURE.NOTE] Bien qu’il soit fonctionne distinct, la tâche « plusieurs instances » n’est pas un type de tâche unique à la [tâche de début] [ net_starttask] ou [JobPreparationTask][net_jobprep]. La tâche plusieurs instances est simplement une tâche par lots standard ([CloudTask] [ net_task] dans .NET lot) dont plusieurs instances a été configurée. Dans cet article, nous faire référence à ce que la **tâche contenant plusieurs instances**.

## <a name="requirements-for-multi-instance-tasks"></a>Configuration requise pour les tâches de plusieurs instances

Plusieurs instances tâches nécessitent un pool avec **la communication entre nœud activée**et **l’exécution des tâches simultanées désactivée**. Si vous essayez d’exécuter une tâche plusieurs instances d’un pool avec la communication entre les nœuds désactivée, ou avec une valeur *maxTasksPerNode* supérieure à 1, la tâche n’est pas planifiée--il reste indéfiniment dans l’état « actif ». Cet extrait de code montre la création d’une liste partagées à l’aide de la bibliothèque .NET lot.

```csharp
CloudPool myCloudPool =
    myBatchClient.PoolOperations.CreatePool(
        poolId: "MultiInstanceSamplePool",
        targetDedicated: 3
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.MaxTasksPerComputeNode = 1;
```

Par ailleurs, plusieurs instances tâches peuvent être effectuées *uniquement* sur les nœuds dans **des pools créées après 14 décembre 2015**.

### <a name="use-a-starttask-to-install-mpi"></a>Utilisez une tâche de début pour installer MPI

Pour exécuter des applications MPI avec une tâche de plusieurs instances, vous devez tout d’abord installer une implémentation MPI (MS-MPI ou MPI Intel, par exemple) sur les nœuds de calcul dans le pool. Il s’agit d’un moment idéal pour utiliser une [tâche de début][net_starttask], laquelle s’exécute chaque fois qu’un nœud rejoint un pool, ou est redémarré. Cet extrait de code crée une tâche de début qui spécifie le package d’installation MS-MPI en tant que [fichier de ressources][net_resourcefile]. Ligne de commande de cette tâche est exécutée après avoir téléchargé le fichier de ressources pour le nœud. Dans ce cas, la ligne de commande effectue une installation sans assistance de MS-MPI.

```csharp
// Create a StartTask for the pool which we use for installing MS-MPI on
// the nodes as they join the pool (or when they are restarted).
StartTask startTask = new StartTask
{
    CommandLine = "cmd /c MSMpiSetup.exe -unattend -force",
    ResourceFiles = new List<ResourceFile> { new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MSMpiSetup.exe", "MSMpiSetup.exe") },
    RunElevated = true,
    WaitForSuccess = true
};
myCloudPool.StartTask = startTask;

// Commit the fully configured pool to the Batch service to actually create
// the pool and its compute nodes.
await myCloudPool.CommitAsync();
```

### <a name="remote-direct-memory-access-rdma"></a>Accès direct à la mémoire à distance (RDMA)

Lorsque vous choisissez une [taille RDMA compatibles avec](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md) comme A9 pour les nœuds de calcul dans le pool de lot, votre application MPI peut tirer parti du High performance, faible latence distant direct à la mémoire access (RDMA) réseau d’Azure.

Recherchez les tailles spécifiés en tant que « RDMA capable » dans les articles suivants :

* Pools **CloudServiceConfiguration**

  * [Taille des Services en nuage](../cloud-services/cloud-services-sizes-specs.md) (Windows uniquement)

* Pools **VirtualMachineConfiguration**

  * [Formats pour les machines virtuelles dans Azure](../virtual-machines/virtual-machines-linux-sizes.md) (Linux)

  * [Formats pour les machines virtuelles dans Azure](../virtual-machines/virtual-machines-windows-sizes.md) (Windows)

>[AZURE.NOTE] Pour tirer parti de RDMA sur [Linux les nœuds de calcul](batch-linux-nodes.md), vous devez utiliser **MPI Intel** sur les nœuds. Pour plus d’informations sur les pools CloudServiceConfiguration et VirtualMachineConfiguration, consultez la section [Pool](batch-api-basics.md#Pool) de la vue d’ensemble de la fonctionnalité lot.

## <a name="create-a-multi-instance-task-with-batch-net"></a>Créer une tâche contenant plusieurs instances avec lot .NET

Maintenant que nous avons abordé les exigences du pool et l’installation du package MPI, nous allons créer la tâche contenant plusieurs instances. Dans cet extrait, nous créons une standard [CloudTask][net_task], puis configurez ses [MultiInstanceSettings] [ net_multiinstance_prop] propriété. Comme mentionné précédemment, la tâche plusieurs instances n’est pas un type de tâche distinctes, mais une tâche par lots standard configuré avec les paramètres de plusieurs instances.

```csharp
// Create the multi-instance task. Its command line is the "application command"
// and will be executed *only* by the primary, and only after the primary and
// subtasks execute the CoordinationCommandLine.
CloudTask myMultiInstanceTask = new CloudTask(id: "mymultiinstancetask",
    commandline: "cmd /c mpiexec.exe -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe");

// Configure the task's MultiInstanceSettings. The CoordinationCommandLine will be executed by
// the primary and all subtasks.
myMultiInstanceTask.MultiInstanceSettings =
    new MultiInstanceSettings(numberOfNodes) {
    CoordinationCommandLine = @"cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d",
    CommonResourceFiles = new List<ResourceFile> {
    new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MyMPIApplication.exe",
                     "MyMPIApplication.exe")
    }
};

// Submit the task to the job. Batch will take care of splitting it into subtasks and
// scheduling them for execution on the nodes.
await myBatchClient.JobOperations.AddTaskAsync("mybatchjob", myMultiInstanceTask);
```

## <a name="primary-task-and-subtasks"></a>Les tâches subordonnées et tâches principales

Lorsque vous créez les paramètres de plusieurs instances d’une tâche, vous spécifiez le nombre de nœuds de calcul que vous souhaitez exécuter la tâche. Lorsque vous soumettez la tâche à une tâche, le service lot crée une tâche **principale** et suffisamment **tâches subordonnées** qui ensemble correspond au nombre de nœuds que vous avez spécifié.

Ces tâches sont affectées un id entier dans la plage de 0 à *numberOfInstances* - 1. La tâche avec l’id 0 est la première tâche et tous les autres ID est des tâches subordonnées. Par exemple, si vous créez les paramètres suivants de plusieurs instances d’une tâche, la tâche principale aura un id de 0, et les tâches subordonnées aurait ID 1 à 9.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Nœud maître

Lorsque vous envoyez une tâche contenant plusieurs instances, le service lot désigne l’un des nœuds de calcul en tant que le nœud « maître » et planifie la tâche à exécuter sur le nœud maître principale. Les tâches subordonnées sont planifiées pour s’exécuter sur le reste des nœuds affectée à la tâche contenant plusieurs instances.

## <a name="coordination-command"></a>Commande débute

La **commande débute** est exécutée par les deux principale et les tâches subordonnées.

Blocage de l’appel de la commande débute--lot ne s’exécute pas la commande application jusqu'à ce que la commande débute a réussi pour toutes les tâches subordonnées. La commande débute doit donc démarrer tous les services d’arrière-plan requis, vérifiez qu’elles sont prêts à l’emploi et puis quittez. Par exemple, cette commande débute pour une solution à l’aide de la version de MS-MPI 7 démarre le service SMPD sur le nœud, puis se ferme :

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

Notez l’utilisation de `start` dans cette commande débute. Cela est nécessaire car le `smpd.exe` application ne renvoie pas immédiatement après l’exécution. Sans l’utilisation de la [Démarrer] [ cmd_start] de commande, cette commande débute retournerait pas et par conséquent, pour bloquer la commande de l’application de s’exécuter.

## <a name="application-command"></a>Commande de l’application

Une fois la tâche principale et toutes les tâches subordonnées en exécutant la commande débute, ligne de commande de la tâche plusieurs instances est exécutée par la tâche principale *uniquement*. Ce que nous appelons la **commande de l’application** pour se distinguer de la commande débute.

Pour les applications MS-MPI, utilisez la commande applications pour exécuter votre application compatibles avec les MPI avec `mpiexec.exe`. Par exemple, voici une commande d’application pour une solution à l’aide de MS-MPI version 7 :

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

>[AZURE.NOTE] Étant donné que MS-MPI `mpiexec.exe` utilise le `CCP_NODES` variable par défaut (voir [variables d’environnement](#environment-variables)) l’exemple de ligne de commande application ci-dessus exclut celui-ci.

## <a name="environment-variables"></a>Variables d’environnement

Traitement par lots crée plusieurs [variables d’environnement] [ msdn_env_var] spécifiques aux tâches plusieurs instances dans les nœuds de calcul affectées à une tâche contenant plusieurs instances. Les lignes de commande débute et d’application peut référencer ces variables d’environnement, comme les scripts et les programmes qu’ils exécutent.

Variables d’environnement suivantes sont créés par le service lot pour une utilisation par tâches plusieurs instances :

* `CCP_NODES`
* `AZ_BATCH_NODE_LIST`
* `AZ_BATCH_HOST_LIST`
* `AZ_BATCH_MASTER_NODE`
* `AZ_BATCH_TASK_SHARED_DIR`
* `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Pour plus d’informations sur ces questions et les autres commandes cluster nœud variables d’environnement, y compris leur contenu et la visibilité, voir [calculer les variables d’environnement nœud][msdn_env_var].

>[AZURE.TIP] L’exemple de code lot Linux MPI contient illustre comment plusieurs de ces variables peuvent être utilisées. [Débute-cmd] [ coord_cmd_example] Bash script téléchargements application commune et des fichiers à partir du stockage Azure input, permet à un partage réseau NFS (File System) sur le nœud maître et configure les autres nœuds affectées à la tâche plusieurs instances en tant que clients NFS.

## <a name="resource-files"></a>Fichiers de ressources

Il existe deux jeux de fichiers de ressources à prendre en compte pour les tâches de plusieurs instances : **fichiers de ressources communs** que *toutes les* tâches téléchargement (principaux et des tâches subordonnées), ainsi que les **fichiers de ressources** spécifiés pour l’instance à plusieurs tâches proprement dit, *seule la principale* tâche télécharge.

Vous pouvez spécifier un ou plusieurs **fichiers de ressources communs** dans les paramètres de plusieurs instances d’une tâche. Ces fichiers de ressources communs sont téléchargés à partir du [Stockage Azure](./../storage/storage-introduction.md) dans **répertoire partagé tâche** par le serveur principal chaque nœud et toutes les tâches subordonnées. Vous pouvez accéder au répertoire partagé de tâche à partir des lignes de commande application et débute à l’aide de la `AZ_BATCH_TASK_SHARED_DIR` variable d’environnement. La `AZ_BATCH_TASK_SHARED_DIR` chemin d’accès est identique sur chaque nœud affectée à la tâche plusieurs instances, par conséquent, vous pouvez partager une commande débute unique entre le principal et toutes les tâches subordonnées. Lot de ne pas « partage » le répertoire dans un sens d’accès à distance, mais vous pouvez l’utiliser comme un montage ou partager point comme mentionné précédemment dans l’info-bulle sur variables d’environnement.

Fichiers de ressources que vous spécifiez pour la tâche de plusieurs instances sont téléchargés vers le répertoire de travail de la tâche, `AZ_BATCH_TASK_WORKING_DIR`, par défaut. Comme indiqué, contrairement à des fichiers de ressources communs, uniquement la première tâche télécharge spécifiées pour la tâche de plusieurs instances des fichiers de ressources.

> [AZURE.IMPORTANT] Toujours utiliser les variables d’environnement `AZ_BATCH_TASK_SHARED_DIR` et `AZ_BATCH_TASK_WORKING_DIR` pour faire référence à ces répertoires dans les lignes de commande. N’essayez pas de construire manuellement les chemins d’accès.

## <a name="task-lifetime"></a>Durée de vie de tâche

La durée de vie de la tâche principale contrôle la durée de vie de la tâche entière plusieurs instances. Lorsque le serveur principal s’arrête, toutes les tâches subordonnées sont terminées. Le code de sortie du primaire est le code de sortie de la tâche et est donc utilisé pour déterminer la réussite ou l’échec de la tâche à des fins de réessayer.

Si un des tâches subordonnées échoue, quitté le programme avec un code de retour différente de zéro, par exemple, la tâche entière plusieurs instances échoue. La tâche plusieurs instances est terminée, puis retentée jusqu'à sa limite de nouvelle tentative.

Lorsque vous supprimez une tâche contenant plusieurs instances, principale et toutes les tâches subordonnées sont également supprimées par le service lot. Toutes les sous-tâches répertoires et ses fichiers sont supprimées des nœuds de calcul, comme pour une tâche standard.

[TaskConstraints] [ net_taskconstraints] pour une tâche de plusieurs instances, telles que la [MaxTaskRetryCount][net_taskconstraint_maxretry], [MaxWallClockTime][net_taskconstraint_maxwallclock]et [RetentionTime] [ net_taskconstraint_retention] propriétés, sont prises en charge qu’il s’agit d’une tâche standard, s’appliquent aux principale et toutes les tâches subordonnées. Toutefois, si vous modifiez la [RetentionTime] [ net_taskconstraint_retention] propriété après avoir ajouté la tâche plusieurs instances sur la tâche, cette modification est appliquée uniquement à la tâche principale. Toutes les tâches subordonnées continuent à utiliser la d’origine [RetentionTime][net_taskconstraint_retention].

Liste de tâches récentes d’un nœud cluster reflète l’id d’une tâche subordonnée si la tâche récente fait partie d’une tâche de plusieurs instances.

## <a name="obtain-information-about-subtasks"></a>Obtenir des informations sur les tâches subordonnées

Pour obtenir des informations sur les tâches subordonnées à l’aide de la bibliothèque .NET lot, appelez le [CloudTask.ListSubtasks] [ net_task_listsubtasks] méthode. Cette méthode renvoie des informations sur toutes les tâches subordonnées et des informations sur le nœud de calcul qui exécute les tâches. Ces informations, vous pouvez déterminer le répertoire racine de chaque tâche subordonnée, l’id du pool, son état actuel, code de sortie et autres éléments. Vous pouvez utiliser ces informations en combinaison avec le [PoolOperations.GetNodeFile] [ poolops_getnodefile] méthode pour obtenir des fichiers de la tâche subordonnée. Notez que cette méthode ne retourne pas d’informations pour la première tâche (id 0).

> [AZURE.NOTE] Sauf indication contraire, méthodes lot .NET qui fonctionnent sur les instances multiples [CloudTask] [ net_task] lui-même s’appliquent *uniquement* à la tâche principale. Par exemple, lorsque vous appelez le [CloudTask.ListNodeFiles] [ net_task_listnodefiles] méthode sur une tâche de plusieurs instances, seuls les fichiers de la tâche principale sont retournés.

L’extrait de code suivant montre comment obtenir des informations de tâche subordonnée, ainsi que de demander le contenu du fichier à partir des nœuds sur lequel ils exécutée.

```csharp
// Obtain the job and the multi-instance task from the Batch service
CloudJob boundJob = batchClient.JobOperations.GetJob("mybatchjob");
CloudTask myMultiInstanceTask = boundJob.GetTask("mymultiinstancetask");

// Now obtain the list of subtasks for the task
IPagedEnumerable<SubtaskInformation> subtasks = myMultiInstanceTask.ListSubtasks();

// Asynchronously iterate over the subtasks and print their stdout and stderr
// output if the subtask has completed
await subtasks.ForEachAsync(async (subtask) =>
{
    Console.WriteLine("subtask: {0}", subtask.Id);
    Console.WriteLine("exit code: {0}", subtask.ExitCode);

    if (subtask.State == TaskState.Completed)
    {
        ComputeNode node =
            await batchClient.PoolOperations.GetComputeNodeAsync(subtask.ComputeNodeInformation.PoolId,
                                                                 subtask.ComputeNodeInformation.ComputeNodeId);

        NodeFile stdOutFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardOutFileName);
        NodeFile stdErrFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardErrorFileName);
        stdOut = await stdOutFile.ReadAsStringAsync();
        stdErr = await stdErrFile.ReadAsStringAsync();

        Console.WriteLine("node: {0}:", node.Id);
        Console.WriteLine("stdout.txt: {0}", stdOut);
        Console.WriteLine("stderr.txt: {0}", stdErr);
    }
    else
    {
        Console.WriteLine("\tSubtask {0} is in state {1}", subtask.Id, subtask.State);
    }
});
```

## <a name="code-sample"></a>Exemple de code

La [MultiInstanceTasks] [ github_mpi] exemple de code de GitHub montre comment utiliser une tâche de plusieurs instances pour exécuter un [MS-MPI] [ msmpi_msdn] application sur lot les nœuds de calcul. Suivez les étapes de [préparation](#preparation) et [d’exécution](#execution) pour exécuter l’exemple.

### <a name="preparation"></a>Préparation

1. Suivez les deux premières étapes de la rubrique [comment compiler et exécuter un programme MS-MPI simple][msmpi_howto]. Il répond à la prerequesites pour l’étape suivante.
1. Créer *une version de la [MPIHelloWorld] * [ helloworld_proj] exemple de programme MPI. Il s’agit du programme qui sera exécuté sur les nœuds de calcul par la tâche contenant plusieurs instances.
1. Créer un fichier zip contenant `MPIHelloWorld.exe` (que vous pouvez intégré étape 2) et `MSMpiSetup.exe` (que vous avez téléchargé étape 1). Vous allez télécharger ce fichier zip comme un package d’application dans l’étape suivante.
1. Utiliser le [portail Azure] [ portal] pour créer un lot d' [application](batch-application-packages.md) appelé « MPIHelloWorld » et spécifiez le fichier zip vous avez créé à l’étape précédente en tant que la version « 1.0 » du package d’application. Pour plus d’informations, voir [télécharger et gérer des applications](batch-application-packages.md#upload-and-manage-applications) .

>[AZURE.TIP] Créer *une version de* `MPIHelloWorld.exe` afin que vous n’êtes pas obligé d’inclure des dépendances supplémentaires (par exemple, `msvcp140d.dll` ou `vcruntime140d.dll`) dans votre package d’application.

### <a name="execution"></a>Erreur d’exécution

1. Télécharger les [exemples de lot azure] [ github_samples_zip] de GitHub.
1. Ouvrez la MultiInstanceTasks **solution** dans Visual Studio 2015. La `MultiInstanceTasks.sln` solution fichier se trouve dans :

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`

1. Entrez vos informations d’identification compte lot et stockage dans `AccountSettings.settings` dans le projet **Microsoft.Azure.Batch.Samples.Common** .
1. **Créer et exécuter** la solution MultiInstanceTasks pour exécuter le MPI exemple d’application sur les nœuds dans un pool de traitement par lots de calcul.
1. *Facultatif*: utiliser le [portail Azure] [ portal] ou de l' [Explorateur de lot] [ batch_explorer] pour examiner le pool d’exemple, le projet et la tâche (« MultiInstanceSamplePool », « MultiInstanceSampleJob », « MultiInstanceSampleTask ») avant de suppriment les ressources.

>[AZURE.TIP] Vous pouvez télécharger [Visual Studio Communauté] [ visual_studio] gratuitement si vous ne disposez pas de Visual Studio.

Sortie de `MultiInstanceTasks.exe` est semblable à ce qui suit :

```
Creating pool [MultiInstanceSamplePool]...
Creating job [MultiInstanceSampleJob]...
Adding task [MultiInstanceSampleTask] to job [MultiInstanceSampleJob]...
Awaiting task completion, timeout in 00:30:00...

Main task [MultiInstanceSampleTask] is in state [Completed] and ran on compute node [tvm-1219235766_1-20161017t162002z]:
---- stdout.txt ----
Rank 2 received string "Hello world" from Rank 0
Rank 1 received string "Hello world" from Rank 0

---- stderr.txt ----

Main task completed, waiting 00:00:10 for subtasks to complete...

---- Subtask information ----
subtask: 1
        exit code: 0
        node: tvm-1219235766_3-20161017t162002z
        stdout.txt:
        stderr.txt:
subtask: 2
        exit code: 0
        node: tvm-1219235766_2-20161017t162002z
        stdout.txt:
        stderr.txt:

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>Étapes suivantes

- Le blog Microsoft HPC et Azure lot équipe traite [MPI prend en charge pour Linux sur Azure lot][blog_mpi_linux]et inclut des informations sur l’utilisation de [OpenFOAM] [ openfoam] avec lot. Vous trouverez des exemples de code Python pour obtenir un [exemple de OpenFOAM dans GitHub][github_mpi].

- Découvrez comment [créer des pools Linux des nœuds de calcul](batch-linux-nodes.md) à utiliser dans vos solutions Azure lot MPI.

[helloworld_proj]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[blog_mpi_linux]: https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/
[cmd_start]: https://technet.microsoft.com/library/cc770297.aspx
[coord_cmd_example]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/mpi/data/linux/openfoam/coordination-cmd
[github_mpi]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[msdn_env_var]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[msmpi_msdn]: https://msdn.microsoft.com/library/bb524831.aspx
[msmpi_sdk]: http://go.microsoft.com/FWLink/p/?LinkID=389556
[msmpi_howto]: http://blogs.technet.com/b/windowshpc/archive/2015/02/02/how-to-compile-and-run-a-simple-ms-mpi-program.aspx
[openfoam]: http://www.openfoam.com/
[visual_studio]: https://www.visualstudio.com/vs/community/

[net_jobprep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_multiinstance_class]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_multiinstance_prop]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.multiinstancesettings.aspx
[net_multiinsance_commonresfiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.commonresourcefiles.aspx
[net_multiinstance_coordcmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.coordinationcommandline.aspx
[net_multiinstance_numinstances]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.numberofinstances.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_cmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.commandline.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_taskconstraints]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.aspx
[net_taskconstraint_maxretry]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxtaskretrycount.aspx
[net_taskconstraint_maxwallclock]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxwallclocktime.aspx
[net_taskconstraint_retention]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.retentiontime.aspx
[net_task_listsubtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listsubtasks.aspx
[net_task_listnodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[poolops_getnodefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getnodefile.aspx

[portal]: https://portal.azure.com
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx

[1]: ./media/batch-mpi/batch_mpi_01.png "Vue d’ensemble de plusieurs instances"
