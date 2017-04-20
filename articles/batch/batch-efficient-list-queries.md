<properties
    pageTitle="Requêtes de liste efficace dans Azure lot | Microsoft Azure"
    description="Améliorer les performances en filtrant vos requêtes lors d’une demande d’informations sur les ressources de traitement par lots, tels que des pools, tâches, tâches et les nœuds de calcul."
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

# <a name="query-the-azure-batch-service-efficiently"></a>Interroger le service Azure lot efficacement

Ici, vous allez découvrir comment faire pour améliorer les performances de votre application Azure lot en réduisant la quantité de données qui sont retournées par le service lorsque vous interroger tâches, tâches et les nœuds avec [.NET par lots] de calcul[ api_net] bibliothèque.

Presque toutes les applications lot doivent effectuer un certain type de surveillance ou toute autre opération qui demande le service lot, souvent à intervalles réguliers. Par exemple, pour déterminer s’il existe des tâches en file d’attente restant dans une tâche, vous devez obtenir des données sur chaque tâche dans la tâche. Pour déterminer l’état des nœuds dans votre liste, vous devez obtenir données sur tous les nœuds dans le pool. Cet article explique comment exécuter des requêtes dans le plus efficacement possible.

## <a name="meet-the-detaillevel"></a>Respecter le niveau de détail

Dans une application de lot production, entités, telles que des tâches, des tâches et des nœuds de calcul peuvent numéroter en les milliers. Lorsque vous demandez des informations sur ces ressources, une potentiellement grande quantité de données doit « croisées le câble » à partir du service lot à votre application sur chaque requête. En limitant le nombre d’éléments et le type d’informations renvoyées par une requête, vous pouvez augmenter la vitesse de vos requêtes et par conséquent les performances de votre application.

Ce [Lot .NET] [ api_net] extrait de code API répertorie *chaque* tâche qui est associé à une tâche, ainsi que *toutes les* propriétés de chaque tâche :

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

Vous pouvez effectuer une requête de liste beaucoup plus efficace, cependant, en appliquant un niveau de détail » » à votre requête. Pour ce faire, l’ajout d’un [ODATADetailLevel] [ odata] objet à la [JobOperations.ListTasks] [ net_list_tasks] méthode. Cet extrait de code renvoie uniquement les ID, ligne de commande cluster nœud informations propriétés et des tâches terminées :

```csharp
// Configure an ODATADetailLevel specifying a subset of tasks and
// their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks =
    batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

Dans cet exemple, si il existe des milliers de tâches dans la tâche, les résultats de la deuxième requête seront généralement retournés beaucoup plus rapidement que la première. Plus d’informations sur l’utilisation de ODATADetailLevel lorsque vous mettez des objets avec l’API .NET lot est inclus [ci-dessous](#efficient-querying-in-batch-net).

> [AZURE.IMPORTANT]
> Il est vivement recommandé que vous *toujours* fournir un objet ODATADetailLevel à vos appels liste API .NET pour assurer une efficacité maximale et les performances de votre application. En spécifiant un niveau de détail, vous pouvez vous aider à réduire les temps de réponse lot, améliorer l’utilisation du réseau et réduire l’utilisation de la mémoire par les applications clientes.

## <a name="filter-select-and-expand"></a>Filtrer, sélectionnez et développez

Le [Lot .NET] [ api_net] et [Lot reste] [ api_rest] API permettre de réduire le nombre d’éléments renvoyés dans une liste, ainsi que la quantité d’informations qui sont renvoyées pour chacun. Pour faire en spécifiant **filtrer**, **Sélectionnez**et **développez chaînes** lors de l’exécution des requêtes de liste.

### <a name="filter"></a>Filtre
La chaîne de filtrage est une expression qui permet de réduire le nombre d’éléments renvoyés. Par exemple, répertorier uniquement les tâches en cours d’exécution pour une tâche, ou uniquement les nœuds de calcul qui sont prêts exécuter des tâches de la liste.

- La chaîne de filtrage est composée d’une ou plusieurs expressions, avec une expression qui comprend un nom de la propriété, un opérateur et une valeur. Les propriétés qui peuvent être spécifiées sont spécifiques à chaque type d’entité que vous interroger, sont les opérateurs pris en charge pour chaque propriété.
- Plusieurs expressions peuvent être combinées en utilisant les opérateurs logiques `and` et `or`.
- Cet exemple montre comment filtrer des listes de chaîne uniquement l’exécution « rendu « tâches : `(state eq 'running') and startswith(id, 'renderTask')`.

### <a name="select"></a>Sélectionnez
Les limites de la chaîne de sélectionner les valeurs de propriété sont retournés pour chaque élément. Vous spécifiez une liste de noms de propriétés, et uniquement les valeurs de propriété sont renvoyées pour les éléments dans les résultats de requête.

- La chaîne select est composée d’une liste de noms de propriétés séparées par des virgules. Vous pouvez spécifier les propriétés pour le type d’entité que vous interrogez.
- Cet exemple de chaîne select indique qu’uniquement trois valeurs de propriété doivent être renvoyées pour chaque tâche : `id, state, stateTransitionTime`.

### <a name="expand"></a>Développer
La chaîne de développement réduit le nombre d’appels API qui sont requises pour obtenir des informations. Lorsque vous utilisez une chaîne de développer, plus d’informations sur chaque élément peuvent être obtenus avec un seul appel d’API. Plutôt que d’avoir obtenu au préalable la liste d’entités, puis qui demandent des informations pour chaque élément dans la liste, une chaîne de développement vous permet d’obtenir les mêmes informations dans un seul appel d’API. Moins appels API implique de meilleures performances.

- Similaire à la chaîne select, les contrôles de chaîne développer si certaines données sont incluses dans les résultats de la requête liste.
- La chaîne de développement est uniquement prise en charge lorsqu’elle est utilisée à la liste des tâches, planifications, tâches et des pools. Pour l’instant, il prend uniquement en charge les informations statistiques.
- Lorsque toutes les propriétés sont requises et aucune chaîne select est spécifiée, la chaîne développer *doit* être utilisée pour obtenir des informations statistiques. Si une chaîne select est utilisée pour obtenir un sous-ensemble des propriétés, puis `stats` peut être indiquée dans la chaîne de sélection et la chaîne de développement n’a pas besoin d’être indiquée.
- Cet exemple montre comment développer chaîne indique que les informations statistiques doivent être renvoyées pour chaque élément dans la liste : `stats`.

> [AZURE.NOTE] Lors de la création de tous les types de chaîne de trois requête (filtrer, sélectionnez et développez), vous devez vous assurer que les noms de propriété et les cas correspondent à ceux de leurs équivalents élément API REST. Par exemple, lorsque vous travaillez avec la classe .NET [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask) , vous devez spécifier **état** au lieu d' **état**, même si la propriété .NET est [CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state). Consultez les tableaux ci-dessous pour les mappages de propriétés entre le .NET et API REST.

### <a name="rules-for-filter-select-and-expand-strings"></a>Règles de filtre, sélectionnez, puis développez chaînes

- Noms de propriétés dans le filtre, sélectionnez, puis développez chaînes doit apparaître comme dans le [Reste du lot] [ api_rest] API--même lorsque vous utilisez [.NET lot] [ api_net] ou l’une des autres commandes kits de développement logiciel.
- Tous les noms des propriétés respectent la casse, mais les valeurs de propriété respectent la casse.
- Date/heure chaînes peut être un des deux formats et doit être précédés `DateTime`.

  - Exemple de format W3C DTF :`creationTime gt DateTime'2011-05-08T08:49:37Z'`
  - Exemple de format RFC 1123 :`creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
- Chaînes booléennes sont soit `true` ou `false`.
- Si une propriété non valide ou un opérateur n’est spécifié, un `400 (Bad Request)` erreur se produit.

## <a name="efficient-querying-in-batch-net"></a>Interroger de lot .NET efficace

Dans le [Lot .NET] [ api_net] API, [ODATADetailLevel] [ odata] classe est utilisée pour l’ajout de filtre, sélectionnez et développez chaînes aux opérations de liste. La classe ODataDetailLevel a trois propriétés de chaîne public qui peuvent être spécifiées dans le constructeur ou définir directement sur l’objet. Vous passez ensuite l’objet ODataDetailLevel en tant que paramètre aux diverses opérations liste comme [ListPools][net_list_pools], [ListJobs][net_list_jobs]et [ListTasks][net_list_tasks].

- [ODATADetailLevel][odata]. [FilterClause] [ odata_filter]: Limiter le nombre d’éléments renvoyés.
- [ODATADetailLevel][odata]. [SelectClause] [ odata_select]: Spécifiez les valeurs de propriétés sont retournés avec chaque élément.
- [ODATADetailLevel][odata]. [ExpandClause] [ odata_expand]: Récupérer les données pour tous les éléments dans une seule API appelez au lieu d’appels distinctes pour chaque élément.

L’extrait de code suivant utilise l’API de .NET lot efficacement interroger le service lot pour les statistiques d’un ensemble spécifique de pools. Dans ce scénario, l’utilisateur lot a pools de test et de production. Le pool de test ID sont précédés de « test », et le regroupement de production ID sont précédés « produit ». Dans l’extrait de code, *myBatchClient* est une instance de la classe [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient) correctement initialisée.

```csharp
// First we need an ODATADetailLevel instance on which to set the filter, select,
// and expand clause strings
ODATADetailLevel detailLevel = new ODATADetailLevel();

// We want to pull only the "test" pools, so we limit the number of items returned
// by using a FilterClause and specifying that the pool IDs must start with "test"
detailLevel.FilterClause = "startswith(id, 'test')";

// To further limit the data that crosses the wire, configure the SelectClause to
// limit the properties that are returned on each CloudPool object to only
// CloudPool.Id and CloudPool.Statistics
detailLevel.SelectClause = "id, stats";

// Specify the ExpandClause so that the .NET API pulls the statistics for the
// CloudPools in a single underlying REST API call. Note that we use the pool's
// REST API element name "stats" here as opposed to "Statistics" as it appears in
// the .NET API (CloudPool.Statistics)
detailLevel.ExpandClause = "stats";

// Now get our collection of pools, minimizing the amount of data that is returned
// by specifying the detail level that we configured above
List<CloudPool> testPools =
    await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();
```

> [AZURE.TIP] Une instance de [ODATADetailLevel] [ odata] qui est configuré avec, sélectionnez et développer clauses peuvent également être passés approprié, méthodes Get, tels que [PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx), pour limiter la quantité de données sont renvoyées.

## <a name="batch-rest-to-net-api-mappings"></a>Lot reste aux mappages d’API .NET

Noms des propriétés dans le filtre, sélectionnez, puis développez chaînes *doit* refléter leurs équivalents API REST, à la fois dans le nom et la casse. Les tableaux ci-dessous fournissent mappages entre les équivalents .NET et API REST.

### <a name="mappings-for-filter-strings"></a>Mappages de chaînes de filtre

- **Méthodes de liste .NET**: chacune des méthodes API .NET dans cette colonne accepte un [ODATADetailLevel] [ odata] objet en tant que paramètre.
- **Requêtes de la liste reste**: API REST chaque page attaché dans cette colonne contient une table qui spécifie les propriétés et les opérations qui sont autorisées dans les chaînes de *filtre* . Vous allez utiliser ces opérations et les noms de propriété lorsque vous créez un [ODATADetailLevel.FilterClause] [ odata_filter] chaîne.

| Méthodes de liste .NET | Requêtes reste de la liste |
|---|---|
| [CertificateOperations.ListCertificates][net_list_certs] | [Répertorier les certificats dans un compte][rest_list_certs]
| [CloudTask.ListNodeFiles][net_list_task_files] | [Répertorier les fichiers associés à une tâche][rest_list_task_files]
| [JobOperations.ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] | [L’état de la préparation du travail et tâches de version pour une tâche de la liste][rest_list_jobprep_status]
| [JobOperations.ListJobs][net_list_jobs] | [Répertorier les travaux dans un compte][rest_list_jobs]
| [JobOperations.ListNodeFiles][net_list_nodefiles] | [Répertorier les fichiers sur un nœud][rest_list_nodefiles]
| [JobOperations.ListTasks][net_list_tasks] | [Répertorier les tâches associées à un projet][rest_list_tasks]
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] | [Liste des planifications du travail dans un compte][rest_list_job_schedules]
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] | [Liste des tâches associées à un calendrier des tâches][rest_list_schedule_jobs]
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] | [Répertorier les nœuds de calcul dans un pool][rest_list_compute_nodes]
| [PoolOperations.ListPools][net_list_pools] | [Répertorier les pools dans un compte][rest_list_pools]

### <a name="mappings-for-select-strings"></a>Mappages de chaînes select

- **Types de lot .NET**: types de lot .NET API.
- **API REST entités**: chaque page dans cette colonne contient une ou plusieurs tables qui répertorient les noms de propriété API REST pour le type. Ces noms de propriété sont utilisés lorsque vous créez des chaînes de *Sélectionner* . Vous allez utiliser ces mêmes noms propriété lorsque vous créez un [ODATADetailLevel.SelectClause] [ odata_select] chaîne.

| Types de .NET lots | Entités API REST |
|---|---|
| [Certificat][net_cert] | [Obtenir des informations sur un certificat][rest_get_cert] |
| [CloudJob][net_job] | [Obtenir des informations sur une tâche][rest_get_job] |
| [CloudJobSchedule][net_schedule] | [Obtenir des informations sur la planification d’une tâche][rest_get_schedule] |
| [ComputeNode][net_node] | [Obtenir des informations sur un nœud][rest_get_node] |
| [CloudPool][net_pool] | [Obtenir des informations sur un pool][rest_get_pool] |
| [CloudTask][net_task] | [Obtenir des informations sur une tâche][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>Exemple : construire une chaîne de filtrage

Lorsque vous construisez une chaîne de filtre pour [ODATADetailLevel.FilterClause][odata_filter], consultez le tableau ci-dessus sous « Mappages de chaînes de filtre » à la page de la documentation rechercher l’API REST qui correspond à l’opération de liste que vous souhaitez effectuer. Vous trouverez les propriétés filtrables et leurs opérateurs pris en charge dans la première table MultiRow, dans la page. Si vous souhaitez récupérer toutes les tâches dont quitter le code a été différente de zéro, par exemple, cette ligne sur [les tâches associées à un projet de liste] [ rest_list_tasks] spécifie la chaîne de propriété applicable et les opérateurs autorisés :

| Propriété | Opérations autorisées | Type |
| :--- | :--- | :--- |
| `executionInfo/exitCode` | `eq, ge, gt, le , lt` | `Int` |

Par conséquent, la chaîne de filtrage qui permet de répertorier toutes les tâches avec un code de sortie non nul serait le suivant :

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Exemple : construire une chaîne de sélection

Construire [ODATADetailLevel.SelectClause][odata_select], consultez le tableau ci-dessus sous « Mappages de chaînes select » et accédez à la page de l’API REST qui correspond au type d’entité que vous mettez en vente. Vous trouverez les propriétés sélectionnables et leurs opérateurs pris en charge dans la première table MultiRow, dans la page. Si vous souhaitez récupérer uniquement l’ID et la ligne de commande pour chaque tâche dans une liste, par exemple, vous trouverez ces lignes dans la table applicable sur [obtenir des informations sur une tâche][rest_get_task]:

| Propriété | Type | Notes |
| :--- | :--- | :--- |
| `id` | `String` | `The ID of the task.` |
| `commandLine` | `String` | `The command line of the task.` |

La chaîne de sélection pour incluant uniquement l’ID de ligne de commande avec chaque tâche dans la liste sera donc :

`id, commandLine`

## <a name="code-samples"></a>Exemples de code

### <a name="efficient-list-queries-code-sample"></a>Exemple de code qui optimise la liste des requêtes

Consultez [EfficientListQueries] [ efficient_query_sample] exemple de projet sur GitHub pour voir comment efficace liste interroger peut affecter les performances dans une application. Cette application console c# crée et ajoute un grand nombre de tâches à une tâche. Ensuite, elle effectue plusieurs appels vers la [JobOperations.ListTasks] [ net_list_tasks] méthode et passe [ODATADetailLevel] [ odata] objets qui sont configurés avec différentes valeurs de propriété pour modifier la quantité de données à renvoyer. Elle produit une sortie similaire à ce qui suit :

```
Adding 5000 tasks to job jobEffQuery...
5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

Sample complete, hit ENTER to continue...
```

Comme indiqué dans les temps, vous pouvez considérablement réduire les temps de réponse en limitant les propriétés et le nombre d’éléments renvoyés. Vous pouvez trouver cette et autres exemples de projets dans les [exemples de lot azure] [ github_samples] référentiel de GitHub.

### <a name="batchmetrics-library-and-code-sample"></a>Exemple de bibliothèque et code BatchMetrics

Outre l’exemple de code EfficientListQueries ci-dessus, vous pouvez trouver la [BatchMetrics] [ batch_metrics] projet dans les [exemples de lot azure] [ github_samples] GitHub référentiel. L’exemple de projet BatchMetrics montre comment efficacement surveiller l’avancement du travail lot Azure à l’aide de l’API lot.

La [BatchMetrics] [ batch_metrics] exemple inclut un projet de bibliothèque de classes .NET qui vous pouvez incorporer dans vos propres projets et un programme de ligne de commande simple exercice et illustrent l’utilisation de la bibliothèque.

L’exemple d’application au sein du projet montre les opérations suivantes :

1. Sélection des attributs spécifiques afin de télécharger uniquement les propriétés que vous avez besoin
2. Filtrage sur les temps de transition état afin de télécharger les modifications uniquement depuis la dernière requête

Par exemple, la méthode suivante s’affiche dans la bibliothèque BatchMetrics. Elle retourne un ODATADetailLevel qui spécifie que seuls les `id` et `state` propriétés doivent être obtenues pour les entités qui sont interrogées. Il indique également que seules les entités dont l’état a changé depuis spécifié `DateTime` paramètre doit être retournée.

```csharp
internal static ODATADetailLevel OnlyChangedAfter(DateTime time)
{
    return new ODATADetailLevel(
        selectClause: "id, state",
        filterClause: string.Format("stateTransitionTime gt DateTime'{0:o}'", time)
    );
}
```

## <a name="next-steps"></a>Étapes suivantes

### <a name="parallel-node-tasks"></a>Tâches de nœud parallèles

[Agrandir le lot Azure calculer l’utilisation des ressources à effectuer les tâches simultanées nœud](batch-parallel-node-tasks.md) est un autre article lié aux performances des application lot. Certains types de charges de travail peuvent bénéficier de l’exécution de tâches en parallèle sur plus grande, mais moins--nœuds de calcul. Consultez le [scénario d’exemple](batch-parallel-node-tasks.md#example-scenario) dans l’article pour plus d’informations sur un tel scénario.

### <a name="batch-forum"></a>Forum de traitement par lots

Le [Forum de lot Azure] [ forum] sur MSDN est l’endroit idéal pour discuter des commandes et poser des questions sur le service. Accédez pour les billets « pense-bête » utiles et publiez vos questions à mesure qu’ils se produisent lorsque vous établissez vos solutions lot.


[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_metrics]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchMetrics
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[odata_ctor]: https://msdn.microsoft.com/library/azure/dn866178.aspx
[odata_expand]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx
[odata_filter]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx
[odata_select]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx

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

[rest_list_certs]: https://msdn.microsoft.com/library/azure/dn820154.aspx
[rest_list_compute_nodes]: https://msdn.microsoft.com/library/azure/dn820159.aspx
[rest_list_job_schedules]: https://msdn.microsoft.com/library/azure/mt282174.aspx
[rest_list_jobprep_status]: https://msdn.microsoft.com/library/azure/mt282170.aspx
[rest_list_jobs]: https://msdn.microsoft.com/library/azure/dn820117.aspx
[rest_list_nodefiles]: https://msdn.microsoft.com/library/azure/dn820151.aspx
[rest_list_pools]: https://msdn.microsoft.com/library/azure/dn820101.aspx
[rest_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/mt282169.aspx
[rest_list_task_files]: https://msdn.microsoft.com/library/azure/dn820142.aspx
[rest_list_tasks]: https://msdn.microsoft.com/library/azure/dn820187.aspx

[rest_get_cert]: https://msdn.microsoft.com/library/azure/dn820176.aspx
[rest_get_job]: https://msdn.microsoft.com/library/azure/dn820106.aspx
[rest_get_node]: https://msdn.microsoft.com/library/azure/dn820168.aspx
[rest_get_pool]: https://msdn.microsoft.com/library/azure/dn820165.aspx
[rest_get_schedule]: https://msdn.microsoft.com/library/azure/mt282171.aspx
[rest_get_task]: https://msdn.microsoft.com/library/azure/dn820133.aspx

[net_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificate.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_schedule]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjobschedule.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
