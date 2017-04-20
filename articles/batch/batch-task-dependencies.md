<properties
    pageTitle="Interdépendances dans Azure lot | Microsoft Azure"
    description="Créer des tâches qui dépendent de la réussite d’autres tâches de traitement de style MapReduce et des données similaires volumineux dans Azure lot les charges de travail."
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
    ms.date="09/28/2016"
    ms.author="marsma" />

# <a name="task-dependencies-in-azure-batch"></a>Interdépendances de tâches dans un lot d’Azure

La fonctionnalité de dépendances de tâche du lot Azure indique une bonne si vous souhaitez traiter :

- MapReduce charges de travail dans le cloud.
- Tâches dont les tâches traitement des données peuvent être exprimées comme un graphique acyclique dirigé (DAG).
- N’importe quel autre tâche dans lequel les tâches en aval dépendent de la sortie des tâches en amont.

Interdépendances de tâches par lots permettent de créer des tâches planifiées pour une exécution sur les nœuds de calcul qu’après la réussite d’un ou plusieurs tâches. Par exemple, vous pouvez créer une tâche qui rend chaque image d’une vidéo 3D avec des tâches distinctes, en parallèle. La dernière tâche--la « fusion tâche «--fusionne les images rendues dans la vidéo complète qu’après que toutes les images ont été correctement rendus.

Vous pouvez créer des tâches qui dépendent d’autres tâches dans une relation un à un ou un-à-plusieurs. Vous pouvez même créer une dépendance de plage dans laquelle une tâche dépend de la réussite d’un groupe de tâches dans une plage spécifique de numéros de tâches. Vous pouvez combiner ces trois scénarios de base pour créer des relations plusieurs-à-plusieurs.

## <a name="task-dependencies-with-batch-net"></a>Interdépendances de tâches avec lot .NET

Dans cet article, nous verrons comment configurer des interdépendances de tâches à l’aide du [Lot .NET] [ net_msdn] bibliothèque. Nous vous montrer tout d’abord comment [Activer l’interdépendance des tâches](#enable-task-dependencies) dans vos projets, puis montrez la [configuration d’une tâche avec les dépendances](#create-dependent-tasks). Pour finir, nous aborderons les [scénarios de dépendance](#dependency-scenarios) par lot prend en charge.

## <a name="enable-task-dependencies"></a>Activer les interdépendances de tâches

Pour utiliser les interdépendances de tâches dans votre application lot, vous devez tout d’abord indiquer le service lot que la tâche utilise les interdépendances de tâches. Dans .NET lot, activez-le sur votre [CloudJob] [ net_cloudjob] en définissant son [UsesTaskDependencies] [ net_usestaskdependencies] propriété `true`:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

Dans l’extrait de code précédent, « batchClient » est une instance de la [BatchClient] [ net_batchclient] cours.

## <a name="create-dependent-tasks"></a>Créer des tâches dépendantes

Pour créer une tâche dépendant de la réussite d’un ou plusieurs tâches, vous indiquez lot que la tâche « dépend de « les autres tâches. Dans .NET lot, configurez le [CloudTask][net_cloudtask]. [DependsOn] [net_dependson] propriété avec une instance de la [TaskDependencies] [ net_taskdependencies] cours :

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Cet extrait de code crée une tâche de l’ID de « Fleurs » est programmé pour s’exécuter sur un nœud de cluster uniquement une fois que les tâches avec les ID de « Pluie » et « Dim » réussie.

 > [AZURE.NOTE] Une tâche est censée se terminer lorsqu’il est dans l’état **terminé** et son **code de sortie** est `0`. Dans .NET lot, cela signifie un [CloudTask][net_cloudtask]. [État] [net_taskstate] valeur de propriété `Completed` et de CloudTask [TaskExecutionInformation][net_taskexecutioninformation]. [Code de sortie] [net_exitcode] valeur de propriété est `0`.

## <a name="dependency-scenarios"></a>Scénarios de dépendance

Il existe trois scénarios de dépendance de tâche de base que vous pouvez utiliser dans Azure lot : un vers un, un-à-plusieurs et l’ID de tâche de dépendance plage. Elles peuvent être combinées pour offrir un scénario quatrième, plusieurs-à-plusieurs.

 Scénario&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Exemple | |
 :-------------------: | ------------------- | -------------------
 [Un à un](#one-to-one) | *taskB* dépend de *taskA* <p/> *taskB* ne sera pas planifié pour l’exécution jusqu'à ce que *taskA* a réussi | ![Diagramme : interdépendance des tâches un à un][1]
 [Un-à-plusieurs](#one-to-many) | *taskC* dépend de *taskA* et *taskB* <p/> *taskC* ne sera pas planifié pour l’exécution tant que *taskA* et *taskB* sont terminées | ![Diagramme : relation un-à-plusieurs tâches][2]
 [Plage d’ID de tâche](#task-id-range) | *taskD* varie selon une plage de tâches <p/> *taskD* ne sera pas planifié pour l’exécution jusqu'à ce que les tâches avec ID *1* et *10* réussie | ![Diagramme : Dépendance de plage d’id de la tâche][3]

>[AZURE.TIP] Vous pouvez créer des relations **plusieurs-à-plusieurs** , telles que l’endroit où les tâches C, D, E et F chaque dépendent des tâches A et B. Ceci est utile, par exemple, dans les scénarios prétraitement parallélisées où vos tâches en aval dépendent de la sortie de plusieurs tâches en amont.

### <a name="one-to-one"></a>Un à un

Pour créer une tâche qui a une dépendance sur la réussite d’une autre tâche, vous fournissez un ID de tâche unique pour le [TaskDependencies][net_taskdependencies]. [OnId] [net_onid] méthode statique lorsque vous remplissez les [DependsOn] [ net_dependson] propriété du [CloudTask][net_cloudtask].

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>Un-à-plusieurs

Pour créer une tâche qui a une dépendance sur la réussite de plusieurs tâches, vous fournissez une collection de tâche des ID à la [TaskDependencies][net_taskdependencies]. [OnIds] [net_onids] méthode statique lorsque vous remplissez les [DependsOn] [ net_dependson] propriété du [CloudTask][net_cloudtask].

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

### <a name="task-id-range"></a>Plage d’ID de tâche

Pour créer une tâche qui a une dépendance sur la réussite d’un groupe de tâches dont sont situées ID dans une plage, vous fournissez la première et dernière ID de la tâche dans la plage à la [TaskDependencies][net_taskdependencies]. [OnIdRange] [net_onidrange] méthode statique lorsque vous remplissez les [DependsOn] [ net_dependson] propriété du [CloudTask][net_cloudtask].

>[AZURE.IMPORTANT] Lorsque vous utilisez des plages d’ID de tâche pour vos dépendances, les numéros de tâche dans la plage *doit* être représentations de chaîne des valeurs entières. En outre, chaque tâche dans la plage doit se termine correctement pour la tâche dépendante et peut être planifiée pour l’exécution.

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## <a name="code-sample"></a>Exemple de code

La [TaskDependencies] [ github_taskdependencies] exemple de projet est un des [exemples de code lot Azure] [ github_samples] sur GitHub. Cette solution Visual Studio 2015 montre comment activer la dépendance de tâche pour une tâche, créer des tâches qui dépendent d’autres tâches et exécuter ces tâches sur un pool de nœuds de calcul.

## <a name="next-steps"></a>Étapes suivantes

### <a name="application-deployment"></a>Déploiement des applications

La fonctionnalité [packages d’application](batch-application-packages.md) du lot offre un moyen facile à la fois à déployer et version les applications que vos tâches s’exécutent sur les nœuds de calcul.

### <a name="installing-applications-and-staging-data"></a>Installer des applications et mise en attente

Consultez les [nœuds de calcul installation applications et données intermédiaires sur lot] [ forum_post] publier sur le forum Azure lot pour une vue d’ensemble des différentes méthodes pour préparer votre nœuds pour exécuter des tâches. Rédigé par un des membres de l’équipe Azure lot, ce billet est une bonne notions fondamentales sur les différentes façons d’obtenir les fichiers (y compris les applications et les données d’entrée de tâche) sur vos nœuds de calcul.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_taskdependencies]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_dependson]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.dependson.aspx
[net_exitcode]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.exitcode.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_onid]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onid.aspx
[net_onids]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onids.aspx
[net_onidrange]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onidrange.aspx
[net_taskexecutioninformation]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_usestaskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.usestaskdependencies.aspx
[net_taskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskdependencies.aspx

[1]: ./media/batch-task-dependency/01_one_to_one.png "Diagramme : relation un à un"
[2]: ./media/batch-task-dependency/02_one_to_many.png "Diagramme : relation un-à-plusieurs"
[3]: ./media/batch-task-dependency/03_task_id_range.png "Diagramme : dépendance de plage d’id de la tâche"
