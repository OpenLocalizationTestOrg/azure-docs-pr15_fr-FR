<properties
    pageTitle="Optimiser l’utilisation de nœud lot avec des tâches parallèles | Microsoft Azure"
    description="Augmenter l’efficacité et réduire les coûts à l’aide de moins de nœuds de calcul et tâches simultanées en cours d’exécution sur chaque nœud dans un pool lot Azure"
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
    ms.date="10/25/2016"
    ms.author="marsma" />

# <a name="maximize-azure-batch-compute-resource-usage-with-concurrent-node-tasks"></a>Optimiser l’utilisation des ressources cluster Azure lot à effectuer les tâches simultanées nœud

En exécutant plusieurs tâches simultanément sur chaque nœud de calcul dans votre pool Azure lot, vous pouvez optimiser l’utilisation des ressources sur un petit nombre de nœuds dans le pool. Pour certaines charges de travail, cela peut entraîner plus courte et heures de travail et réduire les coûts.

Tandis que certains scénarios bénéficient de toutes les ressources d’un nœud de consacrer à une seule tâche, plusieurs situations bénéficient d’autoriser plusieurs tâches de partager les ressources :

 - **En limitant le transfert de données** lorsque les tâches sont en mesure de partager des données. Dans ce scénario, vous pouvez réduire les frais de transfert de données en le copiant de données partagée à un plus petit nombre de nœuds et l’exécution de tâches en parallèle sur chaque nœud. Cela s’applique en particulier si les données à copier sur chaque nœud doivent être transférées entre régions géographiques.

 - **Utilisation de la mémoire – optimiser** lors de tâches nécessitent une grande quantité de mémoire, mais uniquement pendant de courtes périodes de temps et à des moments variables pendant l’exécution. Vous pouvez employer des nœuds de calcul moins, mais il est plus grande, avec davantage de mémoire pour gérer efficacement ces pointes. Ces nœuds devrait avoir plusieurs tâches en cours d’exécution en parallèle sur chaque nœud, mais chaque tâche serait tirer parti de la mémoire de nombreuses des nœuds à des moments différents.

 - **Réduction des limites nœud** lors de la communication entre nœud est nécessaire au sein d’un pool. Pour l’instant, pools configurés pour les communications entre nœud sont limités à 50 nœuds de calcul. Si chaque nœud dans cet un pool est en mesure d’exécuter des tâches en parallèle, un plus grand nombre de tâches peut être exécuté simultanément.

 - **Répliquer un local cluster de calcul**, telles que lorsque vous déplacez un environnement cluster vers Azure. Si votre solution locale actuelle exécute plusieurs tâches par nœud de calcul, vous pouvez augmenter le nombre maximal de tâches nœud à mieux refléter cette configuration.

## <a name="example-scenario"></a>Exemple de scénario

Par exemple pour illustrer les avantages de l’exécution des tâches en parallèle, supposons que votre application tâche ait processeur et la mémoire configuration requise, tels que [Standard\_D1](../cloud-services/cloud-services-sizes-specs.md#general-purpose-d) nœuds suffisent. Mais, pour terminer le travail dans le temps imparti, 1 000 de ces nœuds sont nécessaires.

Au lieu d’utiliser Standard\_nœuds D1 qui ont 1 processeur cœur, vous pouvez aussi utiliser [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md#memory-intensive-d) nœuds qui disposent de 16 cœurs chaque et activer l’exécution des tâches en parallèle. Par conséquent, *16 heures moins de nœuds* peuvent être utilisées--au lieu de 1 000 postes, 63 uniquement serait requis. En outre, si les fichiers d’application de grande taille ou données de référence sont nécessaires pour chaque nœud, l’efficacité et la durée des tâches sont à nouveau améliorés dans la mesure où les données sont copiées sur uniquement 16 nœuds.

## <a name="enable-parallel-task-execution"></a>Activer l’exécution des tâches en parallèle

Vous configurez les nœuds de calcul pour l’exécution des tâches en parallèle au niveau du pool. Avec la bibliothèque .NET lot, définissez la [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] propriété lorsque vous créez un pool. Si vous utilisez l’API REST lot, définissez la [maxTasksPerNode] [ rest_addpool] élément dans le corps de requête lors de la création du pool.

Azure lot vous permet de définir nombre maximum de tâches par nœud jusqu'à quatre fois (x 4) le nombre de cœurs nœud. Par exemple, si le pool est configuré avec les nœuds de taille « Grande » (quatre cœurs), puis `maxTasksPerNode` peut être défini à 16. Pour plus d’informations sur le nombre de cœurs pour chacun des tailles de nœud, voir [tailles des Services en nuage](../cloud-services/cloud-services-sizes-specs.md). Pour plus d’informations sur les limites de service, voir [les Quotas et les limites pour le service lot Azure](batch-quota-limit.md).

> [AZURE.TIP] Veillez à prendre en considération les `maxTasksPerNode` valeur lorsque vous créez une [formule échelle] [ enable_autoscaling] pour votre pool. Par exemple, une formule qui renvoie `$RunningTasks` pourrait être considérablement affecté par une augmentation des tâches par nœud. Pour plus d’informations, voir [automatiquement échelle nœuds dans un pool d’Azure lot de calcul](batch-automatic-scaling.md) .

## <a name="distribution-of-tasks"></a>Répartition des tâches

Lorsque les nœuds de calcul dans un pool peuvent s’exécuter tâches simultanément, il est important de spécifier la manière dont les tâches réparti sur les nœuds dans le pool.

À l’aide de la [CloudPool.TaskSchedulingPolicy] [ task_schedule] propriété, vous pouvez spécifier que les tâches doivent être affectées uniformément sur tous les nœuds dans le pool (« propagation »). Ou vous pouvez spécifier que les tâches autant que possible doivent être affectées à chaque nœud avant que les tâches sont affectées à un autre nœud dans le pool (« emballage »).

Exemple de comment cette fonctionnalité est utile, il est conseillé du pool de [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md#memory-intensive-d) nœuds (dans l’exemple ci-dessus) qui est configuré avec un [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] valeur 16. Si la [CloudPool.TaskSchedulingPolicy] [ task_schedule] est configuré avec un [ComputeNodeFillType] [ fill_type] du *module*, il serait optimiser l’utilisation de tous les 16 cœurs de chaque nœud et autoriser un [pool autoscaling](batch-automatic-scaling.md) réduire la taille des nœuds inutilisés à partir du pool (nœuds sans les tâches affectées). Cela permet de réduire l’utilisation des ressources et économise de l’argent.

## <a name="batch-net-example"></a>Dans l’exemple .NET lot

Ce [Lot .NET] [ api_net] extrait de code API affiche une demande de création d’un pool contenant quatre nœuds de grande taille avec un maximum de quatre tâches par nœud. Il indique une planification de stratégie qui remplit chaque nœud à effectuer les tâches avant d’attribuer des tâches vers un autre nœud dans la liste des tâches. Pour plus d’informations sur l’ajout de regroupements à l’aide de l’API .NET lot, voir [BatchClient.PoolOperations.CreatePool][poolcreate_net].

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicated: 4
        virtualMachineSize: "large",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

pool.MaxTasksPerComputeNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

## <a name="batch-rest-example"></a>Exemple de reste lot

Ce [Lot reste] [ api_rest] API extrait illustre une demande de création d’un pool qui contient deux nœuds de grande taille avec un maximum de quatre tâches par nœud. Pour plus d’informations sur l’ajout de regroupements à l’aide de l’API REST, voir [Ajouter un pool à un compte][rest_addpool].

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "cloudServiceConfiguration": {
    "osFamily":"4",
    "targetOSVersion":"*",
  }
  "targetDedicated":2,
  "maxTasksPerNode":4,
  "enableInterNodeCommunication":true,
}
```

> [AZURE.NOTE] Vous pouvez définir la `maxTasksPerNode` élément et [MaxTasksPerComputeNode] [ maxtasks_net] propriété uniquement au moment de la création de pool. Ils ne peuvent pas être modifiés après qu’un pool a déjà été créé.

## <a name="code-sample"></a>Exemple de code

La [ParallelNodeTasks] [ parallel_tasks_sample] projet sur GitHub illustre l’utilisation de la [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] propriété.

Cette application console c# utilise le [Lot .NET] [ api_net] bibliothèque pour créer un pool avec un ou plusieurs nœuds de calcul. Il s’exécute un nombre configurable des tâches sur ces nœuds pour simuler la charge variable. Sortie de l’application spécifie les nœuds exécutées chaque tâche. L’application fournit également un résumé des paramètres de la tâche et durée. La partie Résumé de la sortie de deux différentes exécutions de l’application exemple s’affiche en dessous.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

La première exécution de l’exemple d’application qui affiche avec un seul nœud dans la liste et le paramètre par défaut d’une tâche par nœud, que la durée des tâches est plus de 30 minutes.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

La deuxième exécution de l’exemple montre une baisse durée des tâches. C’est parce que le pool a été configuré avec quatre tâches par nœud, qui autorise l’exécution des tâches en parallèle terminer le travail dans près d’un quart du temps.

> [AZURE.NOTE] Les durées de tâche dans les résumés ci-dessus n’incluent pas l’heure de création du pool. Chacune des tâches ci-dessus a été envoyé à des pools créés précédemment dont les nœuds de cluster ont été dans l’état *inactif* au moment de l’envoi.

## <a name="next-steps"></a>Étapes suivantes

### <a name="batch-explorer-heat-map"></a>Carte de chaleur lot Explorer

L' [Explorateur de lot Azure][batch_explorer], l’une des commandes Azure [exemples d’applications][github_samples], contient une fonctionnalité de *Carte thermique* qui fournit la visualisation de l’exécution des tâches. Lorsque vous êtes l’exécution de la [ParallelTasks] [ parallel_tasks_sample] exemple d’application, vous pouvez utiliser la fonctionnalité carte thermique pour visualiser facilement l’exécution de tâches en parallèle sur chaque nœud.

![Carte de chaleur lot Explorer][1]

*Lot Explorer carte thermique illustrant un pool de quatre nœuds, avec chaque nœud quatre tâches en cours d’exécution*

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[rest_addpool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

[1]: ./media/batch-parallel-node-tasks\heat_map.png
