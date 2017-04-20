<properties
    pageTitle="La fonction préparation et le nettoyage de disque dans lot | Microsoft Azure"
    description="Utilisez les tâches de préparation au niveau du projet pour réduire le transfert de données au lot Azure nœuds de calcul, puis relâchez tâches de nettoyage du nœud à l’achèvement du projet."
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
    ms.date="09/16/2016"
    ms.author="marsma" />

# <a name="run-job-preparation-and-completion-tasks-on-azure-batch-compute-nodes"></a>Exécuter des tâches de préparation et de saisie semi-automatique sur nœuds de calcul lot Azure

 Un traitement par lots Azure nécessite souvent une forme de l’installation avant de ses tâches sont exécutées et des tâches postérieures à la maintenance lorsque ses tâches sont terminées. Vous devrez peut-être télécharger des données d’entrée des tâches courantes sur vos nœuds de calcul, ou téléchargez les données de sortie de tâche au stockage Azure une fois que le travail est terminé. Vous pouvez utiliser les tâches de **Préparation du travail** et **relâchez de travail** pour effectuer ces opérations.

## <a name="what-are-job-preparation-and-release-tasks"></a>Quelles sont préparation du travail et relâchez tâches ?

Avant l’exécution de tâches d’un projet, la tâche de préparation du projet s’exécute sur tous les nœuds de calcul planifiées pour s’exécuter au moins une tâche. Une fois que la tâche est terminée, la tâche de lancement de travail s’exécute sur chaque nœud dans le pool exécutées au moins une tâche. Comme avec des tâches normales lot, vous pouvez spécifier une ligne de commande à appeler lors de l’exécution d’une tâche de préparation ou de la version finale du projet.

Tâches de préparation et version offrent des fonctionnalités de tâche commandes familières telles que le téléchargement de fichier ([fichiers de ressources][net_job_prep_resourcefiles]), élevés d’exécution, variables d’environnement personnalisées, durée maximale d’exécution, nombre de tentatives et la période de rétention.

Dans les sections suivantes, vous allez découvrir comment utiliser le [JobPreparationTask] [ net_job_prep] et [JobReleaseTask] [ net_job_release] classes trouvées dans le [Lot .NET] [ api_net] bibliothèque.

> [AZURE.TIP] Tâches de préparation et de publication sont particulièrement utiles dans les environnements « partagé pool », dans lequel un pool des nœuds de calcul persiste entre les séries de travail et est utilisé par nombreuses tâches.

## <a name="when-to-use-job-preparation-and-release-tasks"></a>Quand utiliser préparation du travail et tâches

Préparation du travail et des tâches de publication de projet sont un bon choix pour les situations suivantes :

**Télécharger des données de tâches courantes**

Traitement par lots nécessite souvent un ensemble de données en tant qu’entrée pour les tâches du projet. Par exemple, dans quotidiennes calculs d’analyse de risque, données de marché sont spécifiques à un projet, encore communes à toutes les tâches de la tâche. Ces données de marché, souvent plusieurs gigaoctets taille, doivent être transférées qu’une seule fois à chaque nœud de calcul afin que n’importe quelle tâche qui s’exécute sur le nœud de s’en servir. Utiliser une **tâche de préparation du projet** pour télécharger ces données à chaque nœud avant l’exécution de la tâche est d’autres tâches.

**Supprimer sortie du projet et la tâche**

Dans un environnement « partagé pool », où les nœuds de calcul d’un pool ne sont pas retirées entre les tâches, vous devrez peut-être supprimer les données de projet entre les exécutions. Vous devrez peut-être économiser de l’espace disque sur les nœuds, ou répondent à des stratégies de sécurité de votre organisation. Utiliser une **version de tâche** pour supprimer des données qui a été téléchargées par une tâche de préparation du projet ou générées lors de l’exécution des tâches.

**Rétention de journal**

Vous souhaiterez peut-être conserver une copie des fichiers journaux qui génèrent de vos tâches ou peut-être blocage des fichiers de vidage pouvant être générées par les applications a échoué. Utiliser une **version de tâche** dans ce cas pour compresser et télécharger ces données dans un [Espace de stockage Azure] [ azure_storage] compte.

>[AZURE.TIP] Une autre façon de conserver les journaux de travail et d’autres tâches de sortie données consiste à utiliser la bibliothèque [Conventions des fichiers par lots Azure](batch-task-output.md) .

## <a name="job-preparation-task"></a>Tâche de préparation du projet

Avant l’exécution de tâches d’un projet, lot exécute la tâche Préparation du travail sur chaque nœud de calcul est planifiée pour exécuter une tâche. Par défaut, le service lot attend de la tâche de préparation du projet se terminer avant l’exécution de tâches planifiées pour s’exécuter sur le nœud. Toutefois, vous pouvez configurer le service ne pas d’attente. Si le nœud redémarrage, la tâche de préparation du projet s’exécute à nouveau, mais vous pouvez également désactiver ce comportement.

La tâche de préparation est exécutée uniquement sur les nœuds qui sont planifiées pour exécuter une tâche. Cela empêche l’exécution inutile d’une tâche de préparation au cas où un nœud ne reçoit pas d’une tâche. Cela peut se produire lorsque le nombre de tâches pour une tâche est inférieur au nombre de nœuds dans un pool. Elle s’applique également lors de [l’exécution des tâches simultanées](batch-parallel-node-tasks.md) est activée, ce qui laisse certains nœuds inactif si le nombre de tâches est inférieur au nombre total de tâches simultané possible. En exécutant ne pas la tâche de préparation d’un projet sur les nœuds inactives, vous pouvez dépenser moins d’argent sur les frais de transfert de données.

> [AZURE.NOTE] [JobPreparationTask] [ net_job_prep_cloudjob] diffère de [CloudPool.StartTask] [ pool_starttask] dans la mesure où JobPreparationTask s’exécute au début de chaque tâche, alors que la tâche de début s’exécute uniquement lorsque le nœud de calcul tout d’abord un pool de jointures ou redémarre.

## <a name="job-release-task"></a>Libérer la tâche travail

Une fois qu’une tâche est marquée comme terminée, la version de tâche est exécutée sur chaque nœud dans le pool exécutées au moins une tâche. Vous marquez une tâche comme étant terminée en envoyant une requête de fin. Le service lot puis définit l’état du travail sur *fin*, met fin à toutes les tâches actives ou en cours d’exécution associés au travail et s’exécute la tâche de publication. Le travail puis passe à l’état *terminé* .

> [AZURE.NOTE] Suppression de travail s’exécute la tâche de publication. Toutefois, si une tâche a déjà été arrêtée, la libérer la tâche n'est pas exécutée une deuxième fois si la tâche est supprimée ultérieurement.

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Préparation du travail, puis relâchez tâches avec lot .NET

Pour utiliser une tâche de préparation du projet, attribuer un [JobPreparationTask] [ net_job_prep] objet [CloudJob.JobPreparationTask de votre travail] [ net_job_prep_cloudjob] propriété. De même, initialisation un [JobReleaseTask] [ net_job_release] et affectez-le à [CloudJob.JobReleaseTask de votre travail] [ net_job_prep_cloudjob] propriété à définir libérer la tâche de la tâche.

Dans cet extrait de code, `myBatchClient` est une instance de [BatchClient][net_batch_client], et `myPool` est un pool existant dans le compte lot.

```csharp
// Create the CloudJob for CloudPool "myPool"
CloudJob myJob =
    myBatchClient.JobOperations.CreateJob(
        "JobPrepReleaseSampleJob",
        new PoolInformation() { PoolId = "myPool" });

// Specify the command lines for the job preparation and release tasks
string jobPrepCmdLine =
    "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
string jobReleaseCmdLine =
    "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

// Assign the job preparation task to the job
myJob.JobPreparationTask =
    new JobPreparationTask { CommandLine = jobPrepCmdLine };

// Assign the job release task to the job
myJob.JobReleaseTask =
    new JobPreparationTask { CommandLine = jobReleaseCmdLine };

await myJob.CommitAsync();
```

Comme mentionné précédemment, la libérer la tâche est exécutée lorsqu’une tâche est terminée ou supprimée. Mettre fin à un projet avec [JobOperations.TerminateJobAsync][net_job_terminate]. Suppression d’une tâche avec [JobOperations.DeleteJobAsync][net_job_delete]. En règle générale, vous mettre fin à ou supprimez un travail lorsque ses tâches sont terminées ou lorsqu’un délai d’expiration que vous avez défini a été atteinte.

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsy("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>Exemple de code de GitHub

Pour voir Préparation du travail et libérer des tâches en action, consultez la [JobPrepRelease] [ job_prep_release_sample] exemple de projet sur GitHub. Cette application console effectue les opérations suivantes :

1. Crée un pool avec deux nœuds « petites ».
2. Crée une tâche avec préparation du travail, release et tâches standard.
3. Exécute la tâche de préparation du projet, qui écrit tout d’abord l’ID du nœud vers un fichier texte dans le répertoire « partagé » d’un nœud.
4. Exécute une tâche sur chaque nœud qui écrit son ID de tâche dans le même fichier texte.
5. Une fois que toutes les tâches sont effectuées (ou le délai d’expiration est atteint), imprime le contenu du fichier de texte de chaque nœud à la console.
6. Lorsque la tâche est terminée, s’exécute la version finale tâche pour supprimer le fichier à partir du nœud.
6. Imprime les codes de sortie des préparation tâches et de version pour chaque nœud sur lequel ils exécutée.
7. Exécution de pauses pour permettre de confirmation de suppression de travail et/ou pool.

Sortie à partir de l’exemple d’application est semblable à ce qui suit :

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 small nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

>[AZURE.NOTE] En raison de la variable de création et date de début des nœuds dans un nouveau pool (certains nœuds sont prêts pour les tâches avant d’autres personnes), vous pouvez constater des sorties différentes. Plus précisément, étant donné que les tâches terminées rapidement, un des nœuds du pool peuvent être exécutées toutes les tâches du projet. Dans ce cas, vous remarquerez que le travail de préparation et tâches release n’existent pas pour le nœud exécutées aucune tâche.

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Inspecter la préparation du travail et des tâches de publication dans le portail Azure

Lorsque vous exécutez l’exemple d’application, vous pouvez utiliser le [portail Azure] [ portal] pour afficher les propriétés de la tâche et ses tâches ou même télécharger le fichier texte partagé modifié par les tâches du projet.

La capture d’écran ci-dessous montre la **carte de tâches de préparation** dans le portail Azure après une exécution de l’exemple d’application. Accédez aux propriétés *JobPrepReleaseSampleJob* une fois vos tâches effectuées (mais avant de le supprimer de votre travail et pool), puis cliquez sur **tâches de préparation** ou des **tâches de publication** pour afficher ses propriétés.

![Propriétés de la préparation du travail portail Azure][1]

## <a name="next-steps"></a>Étapes suivantes

### <a name="application-packages"></a>Packages d’application

Outre la tâche de préparation du projet, vous pouvez également utiliser la fonctionnalité [packages d’application](batch-application-packages.md) du lot de préparation des nœuds de calcul à l’exécution des tâches. Cette fonctionnalité est particulièrement utile pour le déploiement des applications qui ne nécessitent pas l’exécution d’un programme d’installation, les applications qui contiennent beaucoup de fichiers (plus de 100) ou d’applications qui requièrent un contrôle de version stricte.

### <a name="installing-applications-and-staging-data"></a>Installer des applications et mise en attente

Ce billet de forum MSDN fournit une vue d’ensemble de plusieurs méthodes de préparation vos nœuds d’exécution des tâches :

[Installer des applications et de données sur le lot de mise en attente des nœuds de calcul][forum_post]

Rédigé par un des membres de l’équipe lot Azure, il présente plusieurs techniques que vous pouvez utiliser pour déployer des applications et des données pour les nœuds de calcul.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[azure_storage]: https://azure.microsoft.com/services/storage/
[portal]: https://portal.azure.com
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]:https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_job_prep_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_job_prep_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.resourcefiles.aspx
[net_job_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.deletejobasync.aspx
[net_job_terminate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_job_release]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobreleasetask.aspx
[net_job_release_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[1]: ./media/batch-job-prep-release/portal-jobprep-01.png
