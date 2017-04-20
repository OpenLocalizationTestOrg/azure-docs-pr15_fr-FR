<properties
    pageTitle="Modèles Visual Studio pour Azure lot | Microsoft Azure"
    description="Découvrez comment ces modèles de projet Visual Studio peuvent vous aider à mettre en œuvre et exécuter votre cluster accrue les charges de travail sur lot Azure"
    services="batch"
    documentationCenter=".net"
    authors="fayora"
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

# <a name="visual-studio-project-templates-for-azure-batch"></a>Modèles de projet Visual Studio pour lot Azure

Le **Gestionnaire de projet** et **modèles tâche processeur Visual Studio** pour lot fournissent le code pour vous aider à mettre en œuvre et exécuter votre cluster accrue les charges de travail sur lot avec le moins de travail. Ce document décrit ces modèles et fournit des conseils pour savoir comment les utiliser.

>[AZURE.IMPORTANT] Cet article aborde uniquement renseignements qui s’appliquent à ces deux modèles et suppose que vous êtes familiarisé avec le service de lot et concepts clés qui lui sont associés : pools, calculer des nœuds, tâches et tâches, Gestionnaire des tâches, les variables d’environnement et autres informations pertinentes. Vous trouverez plus d’informations dans les [Concepts de base du lot Azure](batch-technical-overview.md), [vue d’ensemble de la fonctionnalité lot pour les développeurs](batch-api-basics.md)et [commencer à utiliser la bibliothèque lot Azure pour .NET](batch-dotnet-get-started.md).

## <a name="high-level-overview"></a>Vue d’ensemble

Les modèles responsable de projet et processeur de tâches pouvant être utilisées pour créer deux composants utiles :

* Une tâche de responsable de projet qui mettent en œuvre, un séparateur de travail qui peut décomposer une tâche en plusieurs tâches qui peuvent exécuter indépendamment, en parallèle.

* Processeur de tâches pouvant être utilisées pour effectuer prétraitement et traiter autour d’une ligne de commande l’application.

Par exemple, dans un scénario de rendu vidéo, le séparateur de travail activer une tâche unique film dans des centaines ou des milliers de tâches distinctes qui seraient traitent des images individuelles séparément. En conséquence, le processeur de tâches serait appeler l’application de rendu et tous les processus dépendants qui sont nécessaires pour le rendu chacune de trame, ainsi que les exécuter toute action supplémentaire (par exemple, copie le cadre rendu vers un emplacement de stockage).

>[AZURE.NOTE] Les modèles de responsable de projet et processeur de tâches sont indépendants entre eux, afin que vous pouvez choisir d’utiliser les deux, ou uniquement un d’eux, en fonction de la configuration requise de votre travail cluster et de vos préférences.

Comme le montre l’illustration ci-dessous, une tâche cluster qui utilise ces modèles sera envoyé par trois étapes :

1. Le code du client (par exemple, application, service web, etc.) soumet une tâche au service lot sur Azure, spécifiant comme son gestionnaire de travail des tâches le programme de gestion de projet.

2. Le service lot s’exécute la tâche Gestionnaire de projet sur un nœud de calcul et le séparateur de travail lance le nombre spécifié de tâche processeur tâches, sur comme plupart calculent nœuds selon vos besoins, selon les paramètres et les spécifications dans le code de fractionnement du projet.

3. Les tâches de processeur tâche exécutent indépendamment, en parallèle, pour traiter les données d’entrée et générer les données de sortie.

![Diagramme montrant comment code client interagit avec le service de traitement par lots][diagram01]

## <a name="prerequisites"></a>Conditions préalables

Pour utiliser les modèles de lot, vous devez les éléments suivants :

* Un ordinateur avec Visual Studio 2015, ou version ultérieure, est déjà installé.

* Les modèles de lot, qui sont disponibles à partir de la [Galerie Visual Studio] [ vs_gallery] comme extensions Visual Studio. Il existe deux façons d’obtenir les modèles :

  * Installer les modèles à l’aide de la boîte de dialogue **mises à jour et Extensions** dans Visual Studio (pour plus d’informations, consultez [recherche et Extensions à l’aide de Visual Studio][vs_find_use_ext]). Dans la boîte de dialogue **Extensions et mises à jour** , rechercher et télécharger les deux extensions suivantes :

    * Gestionnaire des travaux lot Azure avec séparateur de travail
    * Processeur de tâches lot Azure

  * Télécharger les modèles à partir de la galerie en ligne pour Visual Studio : [Modèles Microsoft Azure lot Project][vs_gallery_templates]

* Si vous envisagez d’utiliser la fonctionnalité [Packages d’Application](batch-application-packages.md) pour déployer le Gestionnaire de projet et processeur de tâches au lot les nœuds de calcul, vous devez lier un compte de stockage à votre compte lot.

## <a name="preparation"></a>Préparation

Nous vous recommandons de créer une solution qui peut contenir votre responsable de projet, ainsi que votre processeur de tâches, car cela peut simplifier partager du code entre votre responsable de projet et programmes de traitement de tâche. Pour créer cette solution, procédez comme suit :

1. Ouvrez Visual Studio 2015 et sélectionnez **fichier** > **Nouveau** > **projet**.

2. Sous **modèles**, développez les **Autres Types de projets**et cliquez sur **Solutions Visual Studio**, puis sélectionnez **Nouvelle Solution**.

3. Tapez un nom décrivant votre application et l’utilisation de cette solution (par exemple, « LitwareBatchTaskPrograms »).

4. Pour créer la nouvelle solution, cliquez sur **OK**.

## <a name="job-manager-template"></a>Gestionnaire de projet de modèle

Le modèle Gestionnaire de travaux vous aide à mettre en œuvre une tâche de responsable de projet que vous pouvez effectuer les opérations suivantes :

* Fractionner une tâche en plusieurs tâches.
* Soumettre ces tâches à exécuter sur lot.

>[AZURE.NOTE] Pour plus d’informations sur le Gestionnaire des tâches, voir [Présentation des fonctionnalités de lot pour les développeurs](batch-api-basics.md#job-manager-task).

### <a name="create-a-job-manager-using-the-template"></a>Créer une tâche à l’aide du modèle

Pour ajouter un gestionnaire de projet à la solution que vous avez créé précédemment, procédez comme suit :

1. Ouvrez votre solution existante dans Visual Studio 2015.

2. Dans l’Explorateur, avec le bouton droit de la solution, cliquez sur **Ajouter** > **Nouveau projet**.

3. Sous **c#**, cliquez sur le **Cloud**, puis cliquez sur **Gestionnaire des travaux Azure lot avec séparateur de travail**.

4. Tapez un nom décrivant votre application et identifie ce projet en tant que Gestionnaire des travaux (par exemple, « LitwareJobManager »).

5. Pour créer le projet, cliquez sur **OK**.

6. Pour finir, créez le projet pour forcer Visual Studio pour charger tous les packages NuGet référencées et vérifier que le projet est valide avant de commencer à modifier.

### <a name="job-manager-template-files-and-their-purpose"></a>Fichiers de modèle du Gestionnaire de projet et leur fonction

Lorsque vous créez un projet à l’aide du modèle de projet Manager, il génère trois groupes de fichiers de code :

* Le fichier de programme principal (Program.cs). Elle contient le point d’entrée de programme et la gestion des exceptions de niveau supérieur. Vous ne doivent pas normalement que vous deviez modifier cette option.

* Le répertoire Framework. Cet onglet contient les fichiers de travail 'réutilisable' effectué par le programme de gestion de projet – décompression paramètres, ajouter des tâches au traitement par lots, etc.. Vous ne devez normalement modifier ces fichiers.

* Le fichier de fractionnement tâche (JobSplitter.cs). Il s’agit de l’endroit où vous allez placer votre logique spécifique à l’application pour fractionner une tâche en tâches.

Bien entendu, vous pouvez ajouter des fichiers supplémentaires si nécessaire pour prendre en charge votre code de fractionnement du travail, selon la complexité de la tâche de fractionnement d’une logique.

Le modèle génère également des fichiers de projet .NET standards comme un fichier .csproj app.config, packages.config, etc..

Le reste de cette section décrit les différents fichiers et leur structure de code et explique que signifie chaque catégorie.

![Explorateur de solutions Visual Studio affichant le Gestionnaire de travaux solution modèle][solution_explorer01]

**Fichiers Framework**

* `Configuration.cs`: Encapsule le chargement des données de configuration de travail tels que les détails du lot de compte, informations d’identification du compte de stockage liées, travail d’informations sur la tâche et des paramètres de la tâche. Il permet également d’accéder aux variables d’environnement définis par lot (voir les paramètres d’environnement pour les tâches, dans la documentation de lot) via la classe Configuration.EnvironmentVariable.

* `IConfiguration.cs`: Résume l’implémentation de la classe de Configuration, afin que vous puissiez test unitaire votre fractionnement de travail à l’aide d’un objet de configuration de liens fictifs ou fictifs.

* `JobManager.cs`: Orchestre les composants du programme Gestionnaire des tâches. Il est responsable de l’initialisation du séparateur de travail, en appelant le séparateur de travail et distribuer les tâches renvoyées par le séparateur de travail à l’émetteur de tâche.

* `JobManagerException.cs`: Représente une erreur qui nécessite le Gestionnaire de projet terminer. JobManagerException est utilisée pour renvoyer à la ligne 'attendus' erreurs d’où les informations de diagnostic spécifiques peuvent être fournies dans le cadre de résiliation.

* `TaskSubmitter.cs`: Cette classe est chargée à l’ajout de tâches renvoyées par le séparateur de travail sur la tâche. Les agrégats de classe JobManager la séquence des tâches en lots pour l’addition efficace mais opportune à la tâche, puis appelle TaskSubmitter.SubmitTasks sur un thread d’arrière-plan pour chaque lot.

**Tâche fractionnée**

`JobSplitter.cs`: Cette classe contient la logique spécifique à l’application pour fractionner la tâche en tâches. L’infrastructure appelle la méthode JobSplitter.Split pour obtenir une séquence des tâches, qu’elle ajoute au projet lors de la méthode retourne les. Il s’agit de la classe où injection de la logique de votre travail. Mettre en œuvre la méthode Split pour retourner une séquence d’instances de CloudTask qui représente les tâches dans lesquelles vous souhaitez partition la tâche.

**Fichiers de projet de ligne de commande .NET standards**

* `App.config`: Fichier de configuration application .NET standard.

* `Packages.config`: Fichier de dépendances package NuGet standard.

* `Program.cs`: Contient le point d’entrée du programme et de la gestion des exceptions de niveau supérieur.

### <a name="implementing-the-job-splitter"></a>Mise en œuvre le séparateur de travail

Lorsque vous ouvrez le Gestionnaire des travaux de modèle de projet, vous devez le projet le fichier JobSplitter.cs s’ouvrent par défaut. Vous pouvez implémenter la logique de fractionnement pour les tâches dans votre charge de travail à l’aide du diaporama de la méthode Split() ci-dessous :

```csharp
/// <summary>
/// Gets the tasks into which to split the job. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The job manager framework invokes the Split method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation should return tasks lazily, for example using a C#
/// iterator and the "yield return" statement; this allows tasks to be added
/// and to start running while splitting is still in progress.
/// </summary>
/// <returns>The tasks to be added to the job. Tasks are added automatically
/// by the job manager framework as they are returned by this method.</returns>
public IEnumerable<CloudTask> Split()
{
    // Your code for the split logic goes here.
    int startFrame = Convert.ToInt32(_parameters["StartFrame"]);
    int endFrame = Convert.ToInt32(_parameters["EndFrame"]);

    for (int i = startFrame; i <= endFrame; i++)
    {
        yield return new CloudTask("myTask" + i, "cmd /c dir");
    }
}
```

>[AZURE.NOTE] La section annotée dans le `Split()` méthode correspond à la section uniquement le Gestionnaire des travaux du code de modèle qui vous permet de modifier en ajoutant la logique est destiné à fractionner vos tâches en tâches différentes. Si vous souhaitez modifier une autre section du modèle, vérifiez que vous êtes être familiarisé avec le fonctionne du lot et essayer quelques des [exemples de code lot][github_samples].

Votre implémentation Split() accède à :

* Les paramètres de la tâche, via la `_parameters` champ.
* L’objet CloudJob qui représente la tâche la `_job` champ.
* L’objet CloudTask qui représente la tâche Gestionnaire de projet, via le `_jobManagerTask` champ.

Votre `Split()` implémentation n’a pas besoin d’ajouter directement des tâches à la tâche. À la place, votre code doit renvoyer une séquence d’objets CloudTask, et ceux-ci sont ajoutés à la tâche automatiquement par les classes framework qui appeler le séparateur de travail. Il est courant d’itérateur utilisation de # (`yield return`) fonctionnalité pour mettre en œuvre les séparateurs de travail en tant qu’il permet les tâches à démarrer en cours d’exécution dès que possible plutôt que d’attendre pour toutes les tâches d’être calculées.

**Échec de fractionnement de tâche**

Si votre fractionnement travail rencontre une erreur, il doit :

* Terminer la séquence à l’aide de c# `yield break` instruction, dans lequel cas le Gestionnaire de travaux sera traité comme étant réussie ; ou

* Une exception, auquel cas que le Gestionnaire de travaux sera traité comme a échoué et peut être retentée selon la façon dont le client a configuré celui-ci).

Dans les deux cas, toutes les tâches déjà renvoyées par le séparateur de travail et ajouté à la tâche par lot seront autorisées à exécuter. Si vous ne voulez pas cela se produise, vous pouvez :

* Mettre fin à la tâche avant le retour le séparateur de travail

* Élaborer la collection de la totalité de la tâche avant de la retourner (autrement dit, retourner un `ICollection<CloudTask>` ou `IList<CloudTask>` au lieu d’implémenter votre fractionnement de travail à l’aide d’un itérateur c#)

* Utiliser des interdépendances de tâches pour effectuer toutes les tâches dépendent de la réussite du Gestionnaire de projet

**Nouvelles tentatives Gestionnaire de projet**

Si le Gestionnaire de travail échoue, il peut être retentée par le service lot selon les paramètres de nouvelles tentatives client. En règle générale, il s’agit approuvé, car lorsque l’infrastructure ajoute des tâches à la tâche, il ignore toutes les tâches qui existent déjà. Toutefois, si le calcul des tâches est coûteux, vous pouvez choisir pas de charge le coût du recalcul des tâches qui ont déjà été ajoutés au projet ; en revanche, si la réexécuter n’est pas garantie pour générer les numéros de tâche même puis le comportement d’ignorer les doublons pas démarre. Dans ce cas, vous devez concevoir votre fractionnement de travail pour détecter le travail qui a déjà été effectué et se répète pas, par exemple en effectuant un CloudJob.ListTasks avant de commencer à générer des tâches.

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>Codes de sortie et les exceptions dans le modèle de gestionnaire de projet

Codes de sortie et exceptions fournissent un mécanisme pour déterminer l’issue de l’exécution d’un programme, et ils peuvent aider à identifier les problèmes liés à l’exécution du programme. Le modèle de Gestionnaire des travaux met en œuvre les codes de sortie et exceptions décrites dans cette section.

Une tâche de responsable de projet qui est implémentée avec le modèle de gestionnaire de travaux peut retourner trois codes de sortie possibles :

| Code | Description |
|------|-------------|
| 0    | Le Gestionnaire de tâche terminé correctement. Votre code de fractionnement de tâche est terminée, et toutes les tâches ont été ajoutés à la tâche. |
| 1    | Échec de la tâche Gestionnaire de projet avec une exception dans une partie du programme « attendue ». L’exception était traduite une JobManagerException avec informations de diagnostic et, si possible, suggestions pour résoudre le problème. |
| 2    | La tâche Gestionnaire de projet a échoué avec une exception « inattendue ». L’exception a été enregistrée sur la sortie standard, mais le Gestionnaire de travaux n’a pas pu ajoutez les autres informations de diagnostic ou mise à jour. |

Dans le cas d’échec de la tâche Gestionnaire des tâches, certaines tâches peuvent toujours ont été ajoutés au service avant que l’erreur est survenue. Ces tâches s’exécutent normales. Pour discussion de ce chemin d’accès de code, voir « Une tâche fractionnée a échoué » ci-dessus.

Toutes les informations renvoyées par exceptions écrit dans des fichiers stdout.txt et stderr.txt. Pour plus d’informations, voir [Gestion des erreurs](batch-api-basics.md#error-handling).

### <a name="client-considerations"></a>Considérations relatives aux clients

Cette section décrit certaines conditions de mise en œuvre client lors de l’appel d’un responsable de projet basé sur ce modèle. Découvrez [comment passer des paramètres et les variables d’environnement du code client](#pass-environment-settings) pour plus d’informations sur le passage de paramètres et les paramètres d’environnement.

**Informations d’identification obligatoires**

Pour ajouter des tâches à la tâche Azure, la tâche Gestionnaire nécessite votre lot Azure compte URL et la clé. Vous devez passer ces éléments dans les variables d’environnement nommées YOUR_BATCH_URL et YOUR_BATCH_KEY. Vous pouvez définir dans le Gestionnaire de projet ces paramètres d’environnement tâche. Par exemple, dans un client c# :

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Informations d’identification de l’espace de stockage**

En règle générale, le client n’a pas besoin de fournir les informations d’identification du compte de stockage liées à la tâche Gestionnaire de projet, car (a) plus les responsables de projet n’avez pas besoin d’accéder explicitement du compte de stockage liées et (b) du compte de stockage liée est souvent fourni sur toutes les tâches sous forme d’un paramètre d’environnement courantes pour la tâche. Si vous ne fournissez pas le compte de stockage liées via les paramètres d’environnement courantes, et le Gestionnaire de travaux exige l’accès au stockage liée, vous devez fournir les informations d’identification de stockage liées comme suit :

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**Paramètres de tâche du Gestionnaire de projet**

Le client doit définir l’indicateur tâche Gestionnaire *killJobOnCompletion* sur **false**.

Il est généralement recommandé pour le client de définir *runExclusive* sur **false**.

Le client doit utiliser la collection *resourceFiles* ou *applicationPackageReferences* pour que la tâche Gestionnaire exécutable (et ses DLL requis) déployé sur le nœud de calcul.

Par défaut, le Gestionnaire de projet ne sera pas retentée en cas d’échec. Selon votre logique du Gestionnaire de projet, le client souhaiterez activer tentatives via *les contraintes*/*maxTaskRetryCount*.

**Paramètres de travail**

Si le séparateur de travail émet des tâches avec des dépendances, le client doit définir usesTaskDependencies du travail sur true.

Dans le modèle de fractionnement du travail, il est inhabituel pour les clients à ajouter des tâches à travaux en plus de ce qui crée le séparateur de travail. Le client doit donc normalement défini du travail *onAllTasksComplete* sur **terminatejob**.

## <a name="task-processor-template"></a>Modèle de traitement de tâche

Un modèle de traitement de tâche vous permet d’implémenter un processeur de tâches que vous pouvez effectuer les opérations suivantes :

* Configurer les informations requises par chaque tâche à exécuter.
* Exécuter toutes les actions requises par chaque tâche.
* Enregistrer des sorties de tâche de façon permanente.

Bien que processeur de tâches n’est pas requis pour effectuer des tâches sur le lot, le principal avantage des processeur de tâches n’est qu’elle fournit un emballage pour mettre en œuvre toutes les actions de l’exécution de tâches dans un seul emplacement. Par exemple, si vous devez exécuter plusieurs applications dans le contexte de chaque tâche, ou si vous avez besoin copier les données de façon permanente la fin de chaque tâche.

Les actions exécutées par le processeur de tâches peuvent être en tant que simple ou complexe et la plupart ou peu, comme requis par votre charge de travail. En outre, en mettant en œuvre toutes les actions de tâche dans un seul processeur de tâche, vous pouvez facilement mettre à jour ou ajouter des actions en fonction des modifications apportées aux applications ou charges de travail. Toutefois, dans certains cas, un processeur de tâches peut-être pas le choix idéal pour votre implémentation comme cela peut ajouter la complexité inutile, par exemple lors de l’exécution de tâches qui peuvent être démarrés rapidement à partir d’une ligne de commande simple.

### <a name="create-a-task-processor-using-the-template"></a>Créer un processeur de tâches à l’aide du modèle

Pour ajouter un processeur de tâches à la solution que vous avez créé précédemment, procédez comme suit :

1. Ouvrez votre solution existante dans Visual Studio 2015.

2. Dans l’Explorateur de solutions, avec le bouton droit de la solution, cliquez sur **Ajouter**, puis cliquez sur **Nouveau projet**.

3. Sous **c#**, cliquez sur le **Cloud**, puis cliquez sur **Processeur de tâche par lots Azure**.

4. Tapez un nom décrivant votre application et identifie ce projet en tant que processeur de tâches (par exemple, « LitwareTaskProcessor »).

5. Pour créer le projet, cliquez sur **OK**.

6. Pour finir, créez le projet pour forcer Visual Studio pour charger tous les packages NuGet référencées et vérifier que le projet est valide avant de commencer à modifier.

### <a name="task-processor-template-files-and-their-purpose"></a>Fichiers de modèle de processeur de tâches et de leur rôle

Lorsque vous créez un projet à l’aide du modèle de processeur tâche, il génère trois groupes de fichiers de code :

* Le fichier de programme principal (Program.cs). Elle contient le point d’entrée de programme et la gestion des exceptions de niveau supérieur. Vous ne doivent pas normalement que vous deviez modifier cette option.

* Le répertoire Framework. Cet onglet contient les fichiers de travail 'réutilisable' effectué par le programme de gestion de projet – décompression paramètres, ajouter des tâches au traitement par lots, etc.. Vous ne devez normalement modifier ces fichiers.

* Le fichier du processeur Office (TaskProcessor.cs). Il s’agit de l’endroit où vous allez placer votre logique spécifique à l’application pour l’exécution d’une tâche (en général en appelant un exécutable existant). Avant et après traitement code, telles que le téléchargement des données supplémentaires ou le chargement de fichiers de résultats, également ici.

Bien entendu, vous pouvez ajouter des fichiers supplémentaires si nécessaire pour prendre en charge votre code processeur tâche, en fonction de la complexité de la tâche de fractionnement d’une logique.

Le modèle génère également des fichiers de projet .NET standards comme un fichier .csproj app.config, packages.config, etc..

Le reste de cette section décrit les différents fichiers et leur structure de code et explique que signifie chaque catégorie.

![Explorateur de solutions Visual Studio affichant la solution modèle processeur de tâches][solution_explorer02]

**Fichiers Framework**

* `Configuration.cs`: Encapsule le chargement des données de configuration de travail tels que les détails du lot de compte, informations d’identification du compte de stockage liées, travail d’informations sur la tâche et des paramètres de la tâche. Il permet également d’accéder aux variables d’environnement définis par lot (voir les paramètres d’environnement pour les tâches, dans la documentation de lot) via la classe Configuration.EnvironmentVariable.

* `IConfiguration.cs`: Résume l’implémentation de la classe de Configuration, afin que vous puissiez test unitaire votre fractionnement de travail à l’aide d’un objet de configuration de liens fictifs ou fictifs.

* `TaskProcessorException.cs`: Représente une erreur qui nécessite le Gestionnaire de projet terminer. TaskProcessorException est utilisée pour renvoyer à la ligne 'attendus' erreurs d’où les informations de diagnostic spécifiques peuvent être fournies dans le cadre de résiliation.

**Processeur de tâches**

* `TaskProcessor.cs`: Exécute la tâche. L’infrastructure appelle la méthode TaskProcessor.Run. Il s’agit de la classe où injection de la logique spécifique à l’application de la tâche. Mettre en œuvre la méthode exécuter pour :
  * Analyser et valider les paramètres de la tâche
  * Composer la ligne de commande pour n’importe quel programme externes que vous voulez appeler
  * Connecter les informations de diagnostic souhaité pour le débogage
  * Démarrer un processus à l’aide de cette ligne de commande
  * Attendez que le processus se termine
  * Capturer le code de sortie de la procédure à suivre pour déterminer si elle a réussi ou a échoué
  * Enregistrer des fichiers de sortie que vous souhaitez conserver de façon permanente

**Fichiers de projet de ligne de commande .NET standards**

* `App.config`: Fichier de configuration application .NET standard.
* `Packages.config`: Fichier de dépendances package NuGet standard.
* `Program.cs`: Contient le point d’entrée du programme et de la gestion des exceptions de niveau supérieur.

## <a name="implementing-the-task-processor"></a>Implémentation du processeur de tâches

Lorsque vous ouvrez le modèle de projet processeur de tâches, vous devez le projet le fichier TaskProcessor.cs s’ouvrent par défaut. Vous pouvez implémenter la logique d’exécution des tâches dans votre charge de travail à l’aide de la méthode Run() illustrée ci-dessous :

```csharp
/// <summary>
/// Runs the task processing logic. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The task processor framework invokes the Run method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation will execute an external program (from resource files or
/// an application package), check the exit code of that program and
/// save output files to persistent storage.
/// </summary>
public async Task<int> Run()

{
    try
    {
        //Your code for the task processor goes here.
        var command = $"compare {_parameters["Frame1"]} {_parameters["Frame2"]} compare.gif";
        using (var process = Process.Start($"cmd /c {command}"))
        {
            process.WaitForExit();
            var taskOutputStorage = new TaskOutputStorage(
            _configuration.StorageAccount,
            _configuration.JobId,
            _configuration.TaskId
            );
            await taskOutputStorage.SaveAsync(
            TaskOutputKind.TaskOutput,
            @"..\stdout.txt",
            @"stdout.txt"
            );
            return process.ExitCode;
        }
    }
    catch (Exception ex)
    {
        throw new TaskProcessorException(
        $"{ex.GetType().Name} exception in run task processor: {ex.Message}",
        ex
        );
    }
}
```
>[AZURE.NOTE] La section annotée de la méthode Run() est la seule partie du code modèle processeur de tâches qui vous permettent de modifier en ajoutant la logique d’exécution des tâches dans votre charge de travail est destiné. Si vous souhaitez modifier une autre section du modèle, Veuillez tout d’abord se familiariser avec le lot de fonctionne par passer en revue la documentation lot et essayer quelques des exemples de code lot.

La méthode Run() est responsable de lancement de la ligne de commande, en commençant une ou plusieurs processus, en attente de tous les processus pour s’exécuter, l’enregistrement des résultats et enfin renvoi avec un code de sortie. La méthode Run() est l’endroit où la mise en œuvre de la logique de traitement de vos tâches. L’infrastructure de processeur tâche appelle la méthode Run() pour vous ; vous n’avez pas besoin d’appeler vous-même.

Votre implémentation Run() accède à :

* Les paramètres de la tâche, via la `_parameters` champ.
* Les codes de projet et la tâche, via la `_jobId` et `_taskId` champs.
* La configuration d’une tâche, via la `_configuration` champ.

**Échec de la tâche**

En cas d’échec, vous pouvez quitter la méthode Run() en lever une exception, mais cela laisse le Gestionnaire d’exceptions de niveau supérieur dans le contrôle de code de fermeture de la tâche. Si vous avez besoin contrôler le code de sortie afin que vous pouvez distinguer les différents types de panne, par exemple pour des raisons de diagnostic ou parce que certains modes d’échec doivent se terminer le travail et d’autres personnes ne doivent pas, vous devez quitter la méthode Run() en renvoyant un code de sortie non nulle. Cela devient le code de sortie de tâche.

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>Codes de sortie et les exceptions dans le modèle de traitement de tâche

Codes de sortie et exceptions fournissent un mécanisme pour déterminer l’issue de l’exécution d’un programme, et ils peuvent aider à identifier les problèmes liés à l’exécution du programme. Le modèle de traitement de tâche met en œuvre les codes de sortie et exceptions décrites dans cette section.

Une processeur une tâche qui est implémentée avec le modèle de processeur de tâches peut retourner trois codes de sortie possibles :

| Code | Description |
|------|-------------|
|  [Process.ExitCode][process_exitcode] | Processeur de tâches est terminée. Notez que cela n’implique pas que le programme que vous avez appelé a réussi – uniquement que le processeur de tâches appelé correctement et effectuée tout traitement sans exceptions. Sens du code de sortie varie selon le programme appelé – code de sortie 0 signifie généralement le programme a réussi et tout autre code quitter signifie que le programme a échoué. |
| 1    | Processeur de tâches a échoué avec une exception dans une partie du programme « attendue ». L’exception était traduite un `TaskProcessorException` avec informations de diagnostic et, si possible, suggestions pour résoudre le problème. |
| 2    | Processeur de tâches a échoué avec une exception « inattendue ». L’exception a été enregistrée sur la sortie standard, mais le processeur de tâches n’a pas pu ajoutez les autres informations de diagnostic ou mise à jour. |

>[AZURE.NOTE] Si le programme que vous appelez utilise des codes de sortie 1 et 2 pour indiquer les modes de panne spécifique, puis en utilisant les codes de sortie 1 et 2 pour les erreurs liées aux tâches processeur est ambiguë. Vous pouvez modifier ces codes d’erreur processeur tâche pour les codes de sortie distinctive en modifiant les cas d’exception dans le fichier Program.cs.

Toutes les informations renvoyées par exceptions écrit dans des fichiers stdout.txt et stderr.txt. Pour plus d’informations, voir gestion des erreurs, dans la documentation de lot.

### <a name="client-considerations"></a>Considérations relatives aux clients

**Informations d’identification de l’espace de stockage**

Si votre processeur de tâches utilise le stockage blob Azure pour conserver les sorties, par exemple à l’aide de la bibliothèque d’assistance de conventions de fichier, puis il doit avoir accès à *soit* le cloud stockage compte informations d’identification *ou* une URL de conteneur blob qui inclut une signature accès partagé (sa). Le modèle prend en charge pour fournir des informations d’identification via les variables d’environnement courantes. Votre client peut transmettre les informations d’identification de stockage comme suit :

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

Le compte de stockage puis est disponible dans la classe TaskProcessor via la `_configuration.StorageAccount` propriété.

Si vous préférez utiliser une URL conteneur avec les associations de sécurité, vous pouvez également le transmettre via un paramètre d’environnement courantes de travail, mais le modèle de processeur tâche n’inclut pas actuellement prise en charge intégrée pour cela.

**Programme d’installation de stockage**

Il est recommandé que la tâche Gestionnaire de client ou tâche Créer tous les conteneurs requis par les tâches avant d’ajouter les tâches à la tâche. Ce champ est obligatoire que si vous utilisez une URL conteneur avec les associations de sécurité, en tant que tel une URL n’inclut pas d’autorisation pour créer le conteneur. Il est recommandé même si vous passez les informations d’identification du compte de stockage, lorsqu’il enregistre toutes les tâches devoir appeler CloudBlobContainer.CreateIfNotExistsAsync sur le conteneur.

## <a name="pass-parameters-and-environment-variables"></a>Passer des paramètres et les variables d’environnement

### <a name="pass-environment-settings"></a>Passer les paramètres d’environnement

Un client peut transmettre les informations à la tâche du Gestionnaire de projet dans l’écran des paramètres d’environnement. Ces informations puis utilisable par la tâche Gestionnaire de projet lors de la génération les tâches de processeur tâche qui s’exécutent dans le cadre de la tâche de calcul. Exemples d’informations que vous pouvez passer en tant que les paramètres d’environnement sont :

* Clés de nom et le compte de compte de stockage
* URL de compte par lots
* Clé de compte par lots

Le service lot comporte un mécanisme simple pour passer des paramètres d’environnement à une tâche de gestionnaire de projet à l’aide de la `EnvironmentSettings` propriété dans [Microsoft.Azure.Batch.JobManagerTask][net_jobmanagertask].

Par exemple, pour obtenir le `BatchClient` instance d’un compte lot, vous pouvez passer comme variables d’environnement à partir du client de l’URL et les informations d’identification de clé partagées pour le compte lot code. De même, pour accéder au compte de stockage qui est lié au compte lot, vous pouvez passer le nom de compte de stockage et de la clé de compte de stockage comme variables d’environnement.

### <a name="pass-parameters-to-the-job-manager-template"></a>Passer des paramètres au modèle Gestionnaire de projet

Dans de nombreux cas, il est utile pour passer des paramètres par projet à la tâche Gestionnaire de projet, pour contrôler le fractionnement de processus de travail ou pour configurer les tâches du projet. Vous pouvez le faire en téléchargeant un fichier JSON nommé parameters.json sous forme d’un fichier de ressources pour la tâche Gestionnaire de projet. Les paramètres sont ensuite accessibles dans le `JobSplitter._parameters` champ dans le modèle de gestionnaire de projet.

>[AZURE.NOTE] Le Gestionnaire de paramètre intégrée prend en charge uniquement les dictionnaires de chaîne à chaîne. Si vous voulez passer des valeurs JSON complexes comme valeurs de paramètre, vous devez passer comme des chaînes et les analyser dans le séparateur de travail ou modifier l’infrastructure `Configuration.GetJobParameters` méthode.

### <a name="pass-parameters-to-the-task-processor-template"></a>Passer des paramètres pour le modèle de traitement de tâche

Vous pouvez également transmettre des paramètres à des tâches individuelles implémentées à l’aide du modèle de traitement de tâche. Comme avec le modèle de gestionnaire de projet, le modèle de processeur tâches recherche un fichier de ressources nommé

Parameters.JSON et le cas échéant il charge en tant que le dictionnaire de paramètres. Il existe un certain nombre d’options pour savoir comment passer des paramètres aux tâches processeur tâche :

* Réutiliser les paramètres de la tâche JSON. Cela fonctionne bien si les seuls paramètres sont ceux qui sont à l’échelle de la tâche (par exemple, un rendu hauteur et largeur). Pour cela, lorsque vous créez un CloudTask dans le séparateur de travail, ajoutez une référence à l’objet de fichier de ressource parameters.json à partir de ResourceFiles de la tâche Gestionnaire de projet (`JobSplitter._jobManagerTask.ResourceFiles`) à la collection de sites de CloudTask ResourceFiles.

* Générer et télécharger un document parameters.json spécifiques à une tâche dans le cadre de l’exécution du travail fractionnement et faire référence à ce blob dans la collection de fichiers de ressources de la tâche. Cela est nécessaire si différentes tâches ont des paramètres différents. Un exemple peut être un scénario de rendu 3D où l’index de cadre est passé à la tâche en tant que paramètre.

>[AZURE.NOTE] Le Gestionnaire de paramètre intégrée prend en charge uniquement les dictionnaires de chaîne à chaîne. Si vous voulez passer des valeurs JSON complexes comme valeurs de paramètre, vous devez passer comme des chaînes et les analyser dans le processeur de tâches ou modifier l’infrastructure `Configuration.GetTaskParameters` méthode.

## <a name="next-steps"></a>Étapes suivantes

### <a name="persist-job-and-task-output-to-azure-storage"></a>Conserver la sortie projet et la tâche sur le stockage Azure

Un autre outil utile dans le développement d’une solution lot est [Conventions des fichiers par lots Azure][nuget_package]. Utilisez cette bibliothèque de classes .NET (actuellement en preview) dans vos applications .NET lot pour stocker et récupérer des sorties de tâche vers et depuis le stockage Azure. [Tâches et conserver Azure lots de sortie](batch-task-output.md) contient une description détaillée de la bibliothèque et son utilisation.

### <a name="batch-forum"></a>Forum de traitement par lots

Le [Forum de lot Azure] [ forum] sur MSDN est l’endroit idéal pour discuter des commandes et poser des questions sur le service. Accédez pour les billets « pense-bête » utiles et publiez vos questions à mesure qu’ils se produisent lorsque vous établissez vos solutions lot.

[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[net_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobmanagertask.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[process_exitcode]: https://msdn.microsoft.com/library/system.diagnostics.process.exitcode.aspx
[vs_gallery]: https://visualstudiogallery.msdn.microsoft.com/
[vs_gallery_templates]: https://go.microsoft.com/fwlink/?linkid=820714
[vs_find_use_ext]: https://msdn.microsoft.com/library/dd293638.aspx

[diagram01]: ./media/batch-visual-studio-templates/diagram01.png
[solution_explorer01]: ./media/batch-visual-studio-templates/solution_explorer01.png
[solution_explorer02]: ./media/batch-visual-studio-templates/solution_explorer02.png
