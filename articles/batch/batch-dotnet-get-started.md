<properties
    pageTitle="Didacticiel : prise en main la bibliothèque Azure lot .NET | Microsoft Azure"
    description="Découvrez les concepts de base du lot Azure et le développement du service lot avec un exemple de scénario."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-compute"
    ms.date="08/15/2016"
    ms.author="marsma"/>

# <a name="get-started-with-the-azure-batch-library-for-net"></a>Prise en main la bibliothèque par lots Azure pour .NET

> [AZURE.SELECTOR]
- [.NET](batch-dotnet-get-started.md)
- [Python](batch-python-tutorial.md)

Découvrez les notions de base du [Lot Azure] [ azure_batch] et le [Lot .NET] [ net_api] bibliothèque dans cet article que nous aborderons une application exemple c# étape par étape. Nous allons aborder comment cet exemple d’application s’appuie sur le service lot pour traiter une charge de travail parallèle dans le cloud, ainsi que la manière dont il interagit avec [Le stockage Azure](../storage/storage-introduction.md) pour transfert de fichier et l’extraction. Vous allez découvrir les techniques de flux de travail courantes lot application. Vous allez également comprendre base des principaux composants du lot, tels que des tâches, tâches, des pools et nœuds de calcul.

![Flux de travail lot solution (basic)][11]<br/>

## <a name="prerequisites"></a>Conditions préalables

Cet article suppose que vous connaissez les bases de c# et Visual Studio. Il est également supposé que vous êtes en mesure de respecter les exigences de création de compte sont spécifiées en dessous pour Azure et les services de stockage et le lot.

### <a name="accounts"></a>Comptes

- **Compte Azure**: Si vous n’avez pas un abonnement Azure, [créez un compte Azure gratuit][azure_free_account].
- **Compte lot**: une fois que vous avez souscrit un abonnement Azure, [créez un compte Azure lot](batch-account-create-portal.md).
- **Compte de stockage**: voir [créer un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account) dans [les comptes de stockage sur Azure](../storage/storage-create-storage-account.md).

> [AZURE.IMPORTANT] Traitement par lots prend actuellement en charge *uniquement* le type de compte de stockage **usage général** , comme décrit à l’étape 5 # [créer un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account) dans [les comptes de stockage sur Azure](../storage/storage-create-storage-account.md).

### <a name="visual-studio"></a>Visual Studio

Vous devez disposer de **Visual Studio 2015** pour créer l’exemple de projet. Vous trouverez des versions d’évaluation et gratuites de Visual Studio dans [vue d’ensemble des produits Visual Studio 2015][visual_studio].

### <a name="dotnettutorial-code-sample"></a>Exemple de code *DotNetTutorial*

La [DotNetTutorial] [ github_dotnettutorial] exemple est un des nombreux exemples de code figurant dans les [exemples de lot azure] [ github_samples] référentiel de GitHub. Vous pouvez télécharger l’exemple en cliquant sur le bouton **Télécharger le code postal** dans la page d’accueil référentiel, ou en cliquant sur le [azure-lot-exemples-master.zip] [ github_samples_zip] lien de téléchargement direct. Une fois que vous avez extrait le contenu du fichier ZIP, vous trouverez la solution dans le dossier suivant :

`\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial`

### <a name="azure-batch-explorer-optional"></a>Azure Explorer par lots (facultatif)

L' [Explorateur de lot Azure] [ github_batchexplorer] est un utilitaire gratuit qui est inclus dans les [exemples de lot azure] [ github_samples] référentiel de GitHub. Bien que non obligatoire pour effectuer ce didacticiel, il peut être utile lors du développement et débogage vos solutions lot.

## <a name="dotnettutorial-sample-project-overview"></a>Vue d’ensemble du projet DotNetTutorial exemple

L’exemple de code *DotNetTutorial* est une solution Visual Studio 2015 qui comprend deux projets : **DotNetTutorial** et **TaskApplication**.

- **DotNetTutorial** est l’application cliente qui interagit avec les services de stockage et le lot pour exécuter une charge de travail en parallèle sur nœuds (machines virtuelles). DotNetTutorial s’exécute sur votre ordinateur local.

- **TaskApplication** est un programme qui s’exécute sur les nœuds de calcul dans Azure pour effectuer le travail réel. Dans l’échantillon, `TaskApplication.exe` analyse le texte dans un fichier téléchargé à partir du stockage Azure (le fichier d’entrée). Ensuite, elle produit un fichier texte (le fichier de sortie) qui contient la liste des trois mots principales qui s’affichent dans le fichier d’entrée. Après avoir créé le fichier de copie, TaskApplication télécharge le fichier vers le stockage Azure. Cela rend disponible pour l’application cliente à télécharger. TaskApplication s’exécute en parallèle sur plusieurs nœuds de calcul dans le service lot.

Le diagramme suivant illustre les opérations principales qui sont exécutées par l’application cliente et l’application est exécutée par les tâches, *TaskApplication* *DotNetTutorial*. Ce flux de travail est typique de nombreuses solutions cluster créés dans des lots. Pendant qu’il ne présente pas toutes les fonctionnalités disponibles dans le service lot, presque chaque scénario lot inclut des processus similaires.

![Flux de travail lot exemple][8]<br/>

[**Étape 1.**](#step-1-create-storage-containers) Créer des **conteneurs** de stockage d’objets Blob Azure.<br/>
[**Étape 2.**](#step-2-upload-task-application-and-data-files) Télécharger des fichiers de l’application Office et des fichiers d’entrée aux conteneurs.<br/>
[**Étape 3.**](#step-3-create-batch-pool) Créer un lot **pool**.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3 a.** Le pool de **tâche de début** télécharge les fichiers binaires de tâche (TaskApplication) à des nœuds dès qu’ils rejoignent le pool.<br/>
[**Étape 4.**](#step-4-create-batch-job) Créer une **tâche**.<br/>
[**Étape 5.**](#step-5-add-tasks-to-job) Ajouter des **tâches** à la tâche.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5 a.** Les tâches sont planifiées pour s’exécuter sur des nœuds.<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5 b.** Chaque tâche télécharge ses données d’entrée à partir du stockage Azure, puis commence à s’exécuter.<br/>
[**Étape 6.**](#step-6-monitor-tasks) Contrôler des tâches.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6 a.** Comme tâches terminées, ils téléchargent les données de sortie au stockage Azure.<br/>
[**Étape 7.**](#step-7-download-task-output) Téléchargez la sortie des tâches à partir du stockage.

Comme indiqué, pas chaque solution lot effectue ces étapes exactes et peut inclure bien d’autres encore, mais l’application d’exemple *DotNetTutorial* illustre processus courants figurant dans une solution lot.

## <a name="build-the-dotnettutorial-sample-project"></a>Créer l’exemple de projet *DotNetTutorial*

Avant d’exécuter avec succès l’échantillon, vous devez spécifier des informations d’identification de compte lot et stockage dans le projet *DotNetTutorial* `Program.cs` fichier. Si vous n’avez pas déjà fait, ouvrez la solution dans Visual Studio en double-cliquant sur la `DotNetTutorial.sln` fichier solution. Ou l’ouvrir à partir de Visual Studio à l’aide de la **fichier > Ouvrir > Projet/Solution** menu.

Ouvrir `Program.cs` au sein du projet *DotNetTutorial* . Ajoutez ensuite vos informations d’identification comme indiqué dans la partie supérieure du fichier :

```
// Update the Batch and Storage account credential strings below with the values
// unique to your accounts. These are used when constructing connection strings
// for the Batch and Storage client objects.

// Batch account credentials
private const string BatchAccountName = "";
private const string BatchAccountKey  = "";
private const string BatchAccountUrl  = "";

// Storage account credentials
private const string StorageAccountName = "";
private const string StorageAccountKey  = "";
```

> [AZURE.IMPORTANT] Comme indiqué ci-dessus, vous devez spécifier actuellement les informations d’identification d’un compte de stockage **objectif général** dans le stockage Azure. Vos applications lot utiliser stockage d’objets blob au sein du compte de stockage **objectif général** . Ne spécifiez pas les informations d’identification d’un compte de stockage qui a été créé en sélectionnant le type de compte de *stockage d’objets Blob* .

Vous pouvez trouver vos informations d’identification compte lot et le stockage au sein de la carte de compte de chaque service dans le [portail Azure][azure_portal]:

![Informations d’identification dans le portail de commandes][9]
![informations d’identification de stockage sur le portail][10]<br/>

À présent que vous avez mis à jour le projet avec vos informations d’identification, avec le bouton droit de la solution dans l’Explorateur, puis cliquez sur **Générer la Solution**. Confirmer la restauration de tous les packages NuGet, si vous êtes invité.

> [AZURE.TIP] Si les packages NuGet ne sont pas restaurées automatiquement, ou si vous rencontrez des erreurs un échec de restaurer les packages, assurez-vous d’avoir le [Gestionnaire de Package NuGet] [ nuget_packagemgr] installé. Activer le téléchargement des packages manquants. Découvrez [L’activation de Package restaurer pendant créer] [ nuget_restore] pour permettre le téléchargement du package.

Dans les sections suivantes, nous décomposer l’exemple d’application dans les étapes qu’il exécute pour traiter une charge de travail dans le service lot et discuter ces étapes en détail. Nous vous invitons à consulter la solution ouverte dans Visual Studio pendant que vous débutez le reste de cet article, dans la mesure où pas chaque ligne de code dans l’échantillon est décrite.

Accédez à la partie supérieure de la `MainAsync` méthode dans le projet *DotNetTutorial* `Program.cs` fichier permettant de commencer à l’étape 1. Chaque étape ci-dessous puis à peu près suit la progression d’appels de méthode dans `MainAsync`.

## <a name="step-1-create-storage-containers"></a>Étape 1 : Créer des conteneurs de stockage

![Créer des conteneurs dans le stockage Azure][1]
<br/>

Traitement par lots prend en charge l’interaction avec le stockage Azure. Conteneurs dans votre compte de stockage constituent les fichiers requis par les tâches qui s’exécutent dans votre compte lot. Les conteneurs fournissent également un emplacement pour stocker les données de sortie produisant les tâches. La première chose que l’application cliente *DotNetTutorial* est créer trois conteneurs dans le [Stockage Blob Azure](../storage/storage-introduction.md):

- **application**: ce conteneur de stockage de l’application exécutée par les tâches, ainsi que ses dépendances, telles que des DLL.
- **Saisie**: tâches télécharger les fichiers de données à traiter à partir du conteneur *d’entrée* .
- **sortie**: tâches issue de traitement du fichier d’entrée, ils sont-elles immédiatement les résultats pour le conteneur de *sortie* .

Pour interagir avec un compte de stockage et de créer des conteneurs, nous utilisons la [Bibliothèque de Client de stockage Azure pour .NET][net_api_storage]. Nous créer une référence au compte avec [CloudStorageAccount][net_cloudstorageaccount]et de celle créer un [CloudBlobClient][net_cloudblobclient]:

```csharp
// Construct the Storage account connection string
string storageConnectionString = String.Format(
    "DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
    StorageAccountName,
    StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount =
    CloudStorageAccount.Parse(storageConnectionString);

// Create the blob client, for use in obtaining references to
// blob storage containers
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

Nous utilisons la `blobClient` référence dans l’ensemble de l’application et passer en tant que paramètre à plusieurs méthodes. Un exemple de ce se trouve dans le bloc de code qui suit immédiatement ci-dessus, où que nous appelons `CreateContainerIfNotExistAsync` pour créer les conteneurs.

```csharp
// Use the blob client to create the containers in Azure Storage if they don't
// yet exist
const string appContainerName    = "application";
const string inputContainerName  = "input";
const string outputContainerName = "output";
await CreateContainerIfNotExistAsync(blobClient, appContainerName);
await CreateContainerIfNotExistAsync(blobClient, inputContainerName);
await CreateContainerIfNotExistAsync(blobClient, outputContainerName);
```

```csharp
private static async Task CreateContainerIfNotExistAsync(
    CloudBlobClient blobClient,
    string containerName)
{
        CloudBlobContainer container =
            blobClient.GetContainerReference(containerName);

        if (await container.CreateIfNotExistsAsync())
        {
                Console.WriteLine("Container [{0}] created.", containerName);
        }
        else
        {
                Console.WriteLine("Container [{0}] exists, skipping creation.",
                    containerName);
        }
}
```

Une fois que les conteneurs ont été créés, l’application pouvez maintenant télécharger les fichiers qui seront utilisés par les tâches.

> [AZURE.TIP] [L’utilisation de stockage d’objets Blob à partir de .NET](../storage/storage-dotnet-how-to-use-blobs.md) fournit un aperçu de l’utilisation d’objets BLOB et conteneurs de stockage Azure. Il doit être dans la partie supérieure de votre liste de lecture que vous commencez à travailler avec lot.

## <a name="step-2-upload-task-application-and-data-files"></a>Étape 2 : Télécharger des fichiers de données et d’application Office

![Téléchargement d’application des tâches et des fichiers d’entrée (données) à des conteneurs][2]
<br/>

Dans l’opération de téléchargement de fichier, *DotNetTutorial* définit tout d’abord les collections **d’application** et **entrée** chemins d’accès tels qu’ils existent sur l’ordinateur local. Puis il télécharge ces fichiers aux conteneurs que vous avez créé à l’étape précédente.

```
// Paths to the executable and its dependencies that will be executed by the tasks
List<string> applicationFilePaths = new List<string>
{
    // The DotNetTutorial project includes a project reference to TaskApplication,
    // allowing us to determine the path of the task application binary dynamically
    typeof(TaskApplication.Program).Assembly.Location,
    "Microsoft.WindowsAzure.Storage.dll"
};

// The collection of data files that are to be processed by the tasks
List<string> inputFilePaths = new List<string>
{
    @"..\..\taskdata1.txt",
    @"..\..\taskdata2.txt",
    @"..\..\taskdata3.txt"
};

// Upload the application and its dependencies to Azure Storage. This is the
// application that will process the data files, and will be executed by each
// of the tasks on the compute nodes.
List<ResourceFile> applicationFiles = await UploadFilesToContainerAsync(
    blobClient,
    appContainerName,
    applicationFilePaths);

// Upload the data files. This is the data that will be processed by each of
// the tasks that are executed on the compute nodes within the pool.
List<ResourceFile> inputFiles = await UploadFilesToContainerAsync(
    blobClient,
    inputContainerName,
    inputFilePaths);
```

Il existe deux manières dans `Program.cs` impliqués dans le processus de téléchargement :

- `UploadFilesToContainerAsync`: Cette méthode retourne une collection de [ResourceFile] [ net_resourcefile] objets (voir ci-dessous) et en interne appels `UploadFileToContainerAsync` pour charger chaque fichier est passé dans le paramètre *filePaths* .
- `UploadFileToContainerAsync`: Il s’agit de la méthode qui effectue le téléchargement de fichier et crée le [ResourceFile] [ net_resourcefile] objets. Après avoir téléchargé le fichier, il obtient une signature accès partagé (sa) pour le fichier et renvoie un objet ResourceFile qui le représente. L’accès au partagé signatures sont également décrites ci-dessous.

```
private static async Task<ResourceFile> UploadFileToContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string filePath)
{
        Console.WriteLine(
            "Uploading file {0} to container [{1}]...", filePath, containerName);

        string blobName = Path.GetFileName(filePath);

        CloudBlobContainer container = blobClient.GetContainerReference(containerName);
        CloudBlockBlob blobData = container.GetBlockBlobReference(blobName);
        await blobData.UploadFromFileAsync(filePath, FileMode.Open);

        // Set the expiry time and permissions for the blob shared access signature.
        // In this case, no start time is specified, so the shared access signature
        // becomes valid immediately
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
        {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
                Permissions = SharedAccessBlobPermissions.Read
        };

        // Construct the SAS URL for blob
        string sasBlobToken = blobData.GetSharedAccessSignature(sasConstraints);
        string blobSasUri = String.Format("{0}{1}", blobData.Uri, sasBlobToken);

        return new ResourceFile(blobSasUri, blobName);
}
```

### <a name="resourcefiles"></a>ResourceFiles

Un [ResourceFile] [ net_resourcefile] fournit des tâches dans un lot avec l’URL vers un fichier dans le stockage Azure qui est téléchargé sur un nœud de calcul avant l’exécution de cette tâche. La [ResourceFile.BlobSource] [ net_resourcefile_blobsource] propriété spécifie l’URL complète du fichier telle qu’elle existe dans le stockage Azure. L’URL peut-être également inclure une signature accès partagé (sa) qui offre un accès sécurisé pour le fichier. La plupart des types de tâches dans .NET lot incluent une propriété *ResourceFiles* , y compris :

- [CloudTask][net_task]
- [Tâche de début][net_pool_starttask]
- [JobPreparationTask][net_jobpreptask]
- [JobReleaseTask][net_jobreltask]

L’exemple d’application DotNetTutorial n’utilise pas les types de tâches JobPreparationTask ou JobReleaseTask, mais vous pouvez en savoir plus sur les [tâches de préparation et l’achèvement de projet sur Azure lot exécuter les nœuds de calcul](batch-job-prep-release.md).

### <a name="shared-access-signature-sas"></a>Signature d’un accès partagé (sa)

Accès partagé signatures sont des chaînes qui — lorsque inclus dans le cadre d’une URL, fournir un accès sécurisé à des conteneurs et des objets BLOB dans le stockage Azure. La DotNetTutorial application utilise blob et conteneur partagés accéder aux URL de signature et montre comment obtenir ces chaînes de signature accès partagé à partir du service de stockage.

- **BLOB partagés accès signatures**: tâche de début du pool dans DotNetTutorial utilise des signatures blob partagé access lorsqu’il télécharge les fichiers binaires d’application et les fichiers de données d’entrée à partir du stockage (voir l’étape 3 ci-dessous). La `UploadFileToContainerAsync` méthode dans de DotNetTutorial `Program.cs` contient le code qui obtient la signature d’un accès partagé de chaque blob. Il fait en appelant [CloudBlob.GetSharedAccessSignature][net_sas_blob].

- **Conteneur partagés accès signatures**: comme chaque tâche termine son travail sur le nœud de calcul, il télécharge son fichier de sortie vers le conteneur de *sortie* dans le stockage Azure. Pour ce faire, TaskApplication utilise une signature d’un accès partagé conteneur qui fournit l’accès en écriture au conteneur dans le cadre de la trajectoire lorsqu’il télécharge le fichier. Obtention de la signature d’un accès partagé conteneur est effectué de la même manière que lorsque obtenir le blob partagés signature access. Dans DotNetTutorial, vous constaterez que les `GetContainerSasUrl` méthode d’assistance appelle [CloudBlobContainer.GetSharedAccessSignature] [ net_sas_container] pour le faire. Vous pouvez en savoir plus sur la manière dont TaskApplication utilise le conteneur partagés signature access dans » étape 6 : tâches du moniteur. »

> [AZURE.TIP] Consultez la série en deux parties sur les signatures accès partagé, [partie 1 : présentation du modèle de signature (sa) accès partagé](../storage/storage-dotnet-shared-access-signature-part-1.md) et [partie 2 : créer et utiliser une signature accès partagé (sa) avec le stockage Blob](../storage/storage-dotnet-shared-access-signature-part-2.md), pour en savoir plus sur fournir un accès sécurisé aux données dans votre compte de stockage.

## <a name="step-3-create-batch-pool"></a>Étape 3 : Créer lot pool

![Créer un pool de traitement par lots][3]
<br/>

Un lot **pool** est un ensemble de nœuds de calcul (machines virtuelles) sur lequel lot exécute les tâches d’un travail.

Une fois qu’il télécharge les fichiers de données et d’application sur le compte de stockage, *DotNetTutorial* démarre son interaction avec le service lot à l’aide de la bibliothèque .NET lot. Pour cela, une [BatchClient] [ net_batchclient] création :

```csharp
BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials(
    BatchAccountUrl,
    BatchAccountName,
    BatchAccountKey);

using (BatchClient batchClient = BatchClient.Open(cred))
{
    ...
```

Ensuite, un pool de nœuds de calcul est créé dans le compte lot avec un appel à `CreatePoolAsync`. `CreatePoolAsync`utilise la [BatchClient.PoolOperations.CreatePool] [ net_pool_create] méthode pour créer une liste dans le service lot.

```csharp
private static async Task CreatePoolAsync(
    BatchClient batchClient,
    string poolId,
    IList<ResourceFile> resourceFiles)
{
    Console.WriteLine("Creating pool [{0}]...", poolId);

    // Create the unbound pool. Until we call CloudPool.Commit() or CommitAsync(),
    // no pool is actually created in the Batch service. This CloudPool instance is
    // therefore considered "unbound," and we can modify its properties.
    CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: poolId,
            targetDedicated: 3,           // 3 compute nodes
            virtualMachineSize: "small",  // single-core, 1.75 GB memory, 224 GB disk
            cloudServiceConfiguration:
                new CloudServiceConfiguration(osFamily: "4")); // Win Server 2012 R2

    // Create and assign the StartTask that will be executed when compute nodes join
    // the pool. In this case, we copy the StartTask's resource files (that will be
    // automatically downloaded to the node by the StartTask) into the shared
    // directory that all tasks will have access to.
    pool.StartTask = new StartTask
    {
        // Specify a command line for the StartTask that copies the task application
        // files to the node's shared directory. Every compute node in a Batch pool
        // is configured with several pre-defined environment variables that you can
        // reference by using commands or applications run by tasks.

        // Since a successful execution of robocopy can return a non-zero exit code
        // (e.g. 1 when one or more files were successfully copied) we need to
        // manually exit with a 0 for Batch to recognize StartTask execution success.
        CommandLine = "cmd /c (robocopy %AZ_BATCH_TASK_WORKING_DIR% %AZ_BATCH_NODE_SHARED_DIR%) ^& IF %ERRORLEVEL% LEQ 1 exit 0",
        ResourceFiles = resourceFiles,
        WaitForSuccess = true
    };

    await pool.CommitAsync();
}
```

Lorsque vous créez un pool avec [CreatePool][net_pool_create], que vous spécifiez plusieurs paramètres tels que le nombre de nœuds de calcul, la [taille des nœuds](../cloud-services/cloud-services-sizes-specs.md)et système d’exploitation des nœuds. Dans *DotNetTutorial*, nous utilisons [CloudServiceConfiguration] [ net_cloudserviceconfiguration] pour spécifier Windows Server 2012 R2 auprès des [Services Cloud](../cloud-services/cloud-services-guestos-update-matrix.md). Toutefois, en spécifiant une [VirtualMachineConfiguration] [ net_virtualmachineconfiguration] à la place, vous pouvez créer des pools de nœuds créés à partir d’images Marketplace, qui comprend des images Windows et Linux : pour plus d’informations, consultez [nœuds dans des pools d’Azure lot de calcul Linux de mise en service](batch-linux-nodes.md) .

> [AZURE.IMPORTANT] Vous êtes chargé de ressources de cluster dans un lot. Pour réduire les coûts, vous pouvez réduire `targetDedicated` 1 avant d’exécuter l’échantillon.

En même temps que ces propriétés nœud physique, vous pouvez également spécifier une [tâche de début] [ net_pool_starttask] pour le pool. La tâche de début s’exécute sur chaque nœud comme ce nœud rejoint le pool, chaque fois qu’un nœud a redémarré. La tâche de début est particulièrement utile pour installer des applications sur les nœuds de calcul avant l’exécution de tâches. Par exemple, si vos tâches traitent des données à l’aide des scripts Python, vous pouvez utiliser une tâche de début pour installer Python sur les nœuds de calcul.

Dans cet exemple d’application, la tâche de début copie les fichiers qu’il télécharge à partir du stockage (qui sont spécifiés à l’aide de la [tâche de début][net_starttask].[ ResourceFiles] [ net_starttask_resourcefiles] propriété) à partir du répertoire de travail de tâche de début au répertoire partagé qui peuvent accéder à *toutes les* tâches en cours d’exécution sur le nœud. Pour l’essentiel, cette action copie `TaskApplication.exe` et ses dépendances au répertoire partagé sur chaque nœud comme le nœud rejoint le pool, afin que toutes les tâches qui s’exécutent sur le nœud d’y accéder.

> [AZURE.TIP] La fonctionnalité **packages d’application** du lot Azure fournit un autre moyen d’obtenir votre application sur les nœuds de calcul dans un pool. Pour plus d’informations, voir [déploiement d’applications avec Azure lot des packages d’application](batch-application-packages.md) .

Également intéressantes dans l’extrait de code ci-dessus consiste à utiliser deux variables d’environnement dans la propriété de *ligne de commande* de la tâche de début : `%AZ_BATCH_TASK_WORKING_DIR%` et `%AZ_BATCH_NODE_SHARED_DIR%`. Chaque nœud de calcul au sein d’un pool lot est configuré automatiquement avec plusieurs variables d’environnement spécifiques au lot. N’importe quel processus est exécutée par une tâche a accès à ces variables d’environnement.

> [AZURE.TIP] Pour en savoir plus sur l’environnement variables qui sont disponibles dans les nœuds de calcul dans un pool de commandes et des informations sur des répertoires de travail de tâche, reportez-vous aux sections [les paramètres d’environnement pour les tâches](batch-api-basics.md#environment-settings-for-tasks) et des [fichiers et des répertoires](batch-api-basics.md#files-and-directories) dans la [vue d’ensemble de la fonctionnalité lot pour les développeurs](batch-api-basics.md).

## <a name="step-4-create-batch-job"></a>Étape 4 : Créer par lots

![Créer des lots][4]<br/>

Une **tâche** est un ensemble de tâches et est associé à un pool de nœuds de calcul. Les tâches d’un travail s’exécutent sur nœuds de calcul du pool associé.

Vous pouvez utiliser une tâche non seulement pour organiser et suivi des tâches dans les charges de travail associées, mais également pour imposer certaines contraintes, telles que le runtime maximal pour le travail (et par extension, ses tâches) ainsi que de priorité de la tâche par rapport aux autres tâches dans le compte lot. Dans cet exemple, le travail est associé uniquement à la liste qui a été créée à l’étape 3 #. Aucune propriété supplémentaire n’est configurées.

Toutes les tâches par lots sont associées à un pool spécifique. Cette association indique les nœuds tâches du projet seront exécute sur. Vous spécifiez en utilisant la [CloudJob.PoolInformation] [ net_job_poolinfo] propriété, comme le montre l’extrait de code ci-dessous.

```csharp
private static async Task CreateJobAsync(
    BatchClient batchClient,
    string jobId,
    string poolId)
{
    Console.WriteLine("Creating job [{0}]...", jobId);

    CloudJob job = batchClient.JobOperations.CreateJob();
    job.Id = jobId;
    job.PoolInformation = new PoolInformation { PoolId = poolId };

    await job.CommitAsync();
}
```

À présent qu’une tâche a été créée, les tâches sont ajoutées à effectuer le travail.

## <a name="step-5-add-tasks-to-job"></a>Étape 5 : Ajouter des tâches à travail

![Ajouter des tâches à une tâche][5]<br/>
*(1) tâches sont ajoutées à la tâche, (2) les tâches sont planifiées pour s’exécuter sur des nœuds et (3) les tâches téléchargement les fichiers de données à traiter*

Lot les **tâches** sont les unités de travail individuelles qui s’exécutent sur les nœuds de calcul. Une tâche a une ligne de commande et exécute les scripts ou les exécutables que vous spécifiez dans cette ligne de commande.

Pour effectuer le travail en réalité, les tâches doivent être ajoutés à une tâche. Chaque [CloudTask] [ net_task] est configuré à l’aide d’une propriété de ligne de commande et [ResourceFiles] [ net_task_resourcefiles] (comme tâche de début du pool) que la tâche télécharge sur le nœud avant sa ligne de commande s’exécute automatiquement. Dans l’exemple de projet *DotNetTutorial* , chaque tâche ne traite qu’un seul fichier. Par conséquent, sa collection ResourceFiles contient un seul élément.

```csharp
private static async Task<List<CloudTask>> AddTasksAsync(
    BatchClient batchClient,
    string jobId,
    List<ResourceFile> inputFiles,
    string outputContainerSasUrl)
{
    Console.WriteLine("Adding {0} tasks to job [{1}]...", inputFiles.Count, jobId);

    // Create a collection to hold the tasks that we'll be adding to the job
    List<CloudTask> tasks = new List<CloudTask>();

    // Create each of the tasks. Because we copied the task application to the
    // node's shared directory with the pool's StartTask, we can access it via
    // the shared directory on the node that the task runs on.
    foreach (ResourceFile inputFile in inputFiles)
    {
        string taskId = "topNtask" + inputFiles.IndexOf(inputFile);
        string taskCommandLine = String.Format(
            "cmd /c %AZ_BATCH_NODE_SHARED_DIR%\\TaskApplication.exe {0} 3 \"{1}\"",
            inputFile.FilePath,
            outputContainerSasUrl);

        CloudTask task = new CloudTask(taskId, taskCommandLine);
        task.ResourceFiles = new List<ResourceFile> { inputFile };
        tasks.Add(task);
    }

    // Add the tasks as a collection, as opposed to issuing a separate AddTask call
    // for each. Bulk task submission helps to ensure efficient underlying API calls
    // to the Batch service.
    await batchClient.JobOperations.AddTaskAsync(jobId, tasks);

    return tasks;
}
```

> [AZURE.IMPORTANT] Lorsqu’ils accèdent variables d’environnement telles que `%AZ_BATCH_NODE_SHARED_DIR%` ou exécuter une application non trouvée dans le nœud `PATH`, doit être précédés de lignes de commande tâche `cmd /c`. Cela sera explicitement exécuter le relais de commande et lui demander de pour mettre fin après avoir effectué votre commande. Cette exigence n’est pas nécessaire si vos tâches exécutent d’une application dans le nœud `PATH` (par exemple, *robocopy.exe* ou *powershell.exe*) et aucune variable d’environnement n’est utilisés.

Dans la `foreach` boucle dans l’extrait de code ci-dessus, vous pouvez voir que la ligne de commande de la tâche est construite telle que trois arguments de ligne de commande sont passés à *TaskApplication.exe*:

1. **premier argument** est le chemin d’accès du fichier à traiter. Il s’agit du chemin d’accès local vers le fichier tel qu’il existe sur le nœud. Lorsque la ResourceFile objet dans `UploadFileToContainerAsync` a été créé tout d’abord ci-dessus, le nom du fichier a été utilisé pour cette propriété (en tant que paramètre au constructeur ResourceFile). Cela indique que le fichier se trouve dans le même répertoire que *TaskApplication.exe*.

2. **deuxième argument** Spécifie que les mots *N* supérieure doivent apparaître dans le fichier de sortie. Dans l’échantillon, il est codé en dur afin que les trois premières mots sont écrits sur le fichier de copie.

3. **troisième argument** est la signature d’un accès partagé (sa) qui fournit l’accès en écriture au conteneur de **sortie** dans le stockage Azure. *TaskApplication.exe* utilise cette URL signature accès partagé lorsqu’il télécharge le fichier de sortie au stockage Azure. Vous pouvez trouver le code pour ce dans le `UploadFileToContainer` méthode dans le projet TaskApplication `Program.cs` fichier :

```csharp
// NOTE: From project TaskApplication Program.cs

private static void UploadFileToContainer(string filePath, string containerSas)
{
        string blobName = Path.GetFileName(filePath);

        // Obtain a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(containerSas));

        // Upload the file (as a new blob) to the container
        try
        {
                CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
                blob.UploadFromFile(filePath, FileMode.Open);

                Console.WriteLine("Write operation succeeded for SAS URL " + containerSas);
                Console.WriteLine();
        }
        catch (StorageException e)
        {

                Console.WriteLine("Write operation failed for SAS URL " + containerSas);
                Console.WriteLine("Additional error information: " + e.Message);
                Console.WriteLine();

                // Indicate that a failure has occurred so that when the Batch service
                // sets the CloudTask.ExecutionInformation.ExitCode for the task that
                // executed this application, it properly indicates that there was a
                // problem with the task.
                Environment.ExitCode = -1;
        }
}
```

## <a name="step-6-monitor-tasks"></a>Étape 6 : Tâches du moniteur

![Tâches du moniteur][6]<br/>
*L’application cliente (1) surveille les tâches d’achèvement et le message de réussite, et (2) les tâches téléchargement des données de résultat au stockage Azure*

Lorsque les tâches sont ajoutées à une tâche, ils sont automatiquement en file d’attente et planifiées pour une exécution sur les nœuds de calcul au sein du pool associé au travail. Selon les paramètres que vous spécifiez, lot gère toutes les tâches queuing, planification, une nouvelle tentative et autres tâches d’administration de tâche à votre place. Il existe de nombreuses approches à l’analyse de l’exécution des tâches. DotNetTutorial montre un exemple simple qui signale uniquement les tâches et saisie semi-automatique échec ou la réussite États.

Dans la `MonitorTasks` méthode dans de DotNetTutorial `Program.cs`, il existe trois concepts lot .NET qui nécessitent une discussion. Ils sont présentées ci-après dans leur ordre d’apparition :

1. **ODATADetailLevel**: spécifiant [ODATADetailLevel] [ net_odatadetaillevel] dans la liste des opérations (par exemple, pour obtenir la liste des tâches d’un travail) est essentiel dans l’optimisation des performances du lot application. Ajouter [interroger le service Azure lot efficacement](batch-efficient-list-queries.md) à votre liste de lecture si vous prévoyez d’effectuer un tri de surveillance au sein de vos applications par lots de l’état.

2. **TaskStateMonitor**: [TaskStateMonitor] [ net_taskstatemonitor] fournit aux applications .NET lot utilitaires d’assistance pour analyser les États de tâches. Dans `MonitorTasks`, *DotNetTutorial* attend de toutes les tâches atteindre [TaskState.Completed] [ net_taskstate] dans un délai. Puis elle termine la tâche.

3. **TerminateJobAsync**: mettre fin à un projet avec [JobOperations.TerminateJobAsync] [ net_joboperations_terminatejob] (ou le blocage JobOperations.TerminateJob) marque cette tâche comme terminée. Il est essentiel de le faire si votre solution lot utilise un [JobReleaseTask][net_jobreltask]. Il s’agit d’un type spécial de tâche, qui est décrit dans les [tâches de préparation et l’achèvement du projet](batch-job-prep-release.md).

La `MonitorTasks` méthode à partir de *DotNetTutorial* `Program.cs` s’affiche sous :

```csharp
private static async Task<bool> MonitorTasks(
    BatchClient batchClient,
    string jobId,
    TimeSpan timeout)
{
    bool allTasksSuccessful = true;
    const string successMessage = "All tasks reached state Completed.";
    const string failureMessage = "One or more tasks failed to reach the Completed state within the timeout period.";

    // Obtain the collection of tasks currently managed by the job. Note that we use
    // a detail level to  specify that only the "id" property of each task should be
    // populated. Using a detail level for all list operations helps to lower
    // response time from the Batch service.
    ODATADetailLevel detail = new ODATADetailLevel(selectClause: "id");
    List<CloudTask> tasks =
        await batchClient.JobOperations.ListTasks(JobId, detail).ToListAsync();

    Console.WriteLine("Awaiting task completion, timeout in {0}...",
        timeout.ToString());

    // We use a TaskStateMonitor to monitor the state of our tasks. In this case, we
    // will wait for all tasks to reach the Completed state.
    TaskStateMonitor taskStateMonitor
        = batchClient.Utilities.CreateTaskStateMonitor();

    try
    {
        await taskStateMonitor.WhenAll(tasks, TaskState.Completed, timeout);
    }
    catch (TimeoutException)
    {
        await batchClient.JobOperations.TerminateJobAsync(jobId, failureMessage);
        Console.WriteLine(failureMessage);
        return false;
    }

    await batchClient.JobOperations.TerminateJobAsync(jobId, successMessage);

    // All tasks have reached the "Completed" state, however, this does not
    // guarantee all tasks completed successfully. Here we further check each task's
    // ExecutionInfo property to ensure that it did not encounter a scheduling error
    // or return a non-zero exit code.

    // Update the detail level to populate only the task id and executionInfo
    // properties. We refresh the tasks below, and need only this information for
    // each task.
    detail.SelectClause = "id, executionInfo";

    foreach (CloudTask task in tasks)
    {
        // Populate the task's properties with the latest info from the
        // Batch service
        await task.RefreshAsync(detail);

        if (task.ExecutionInformation.SchedulingError != null)
        {
            // A scheduling error indicates a problem starting the task on the node.
            // It is important to note that the task's state can be "Completed," yet
            // still have encountered a scheduling error.

            allTasksSuccessful = false;

            Console.WriteLine("WARNING: Task [{0}] encountered a scheduling error: {1}",
                task.Id,
                task.ExecutionInformation.SchedulingError.Message);
        }
        else if (task.ExecutionInformation.ExitCode != 0)
        {
            // A non-zero exit code may indicate that the application executed by
            // the task encountered an error during execution. As not every
            // application returns non-zero on failure by default (e.g. robocopy),
            // your implementation of error checking may differ from this example.

            allTasksSuccessful = false;

            Console.WriteLine("WARNING: Task [{0}] returned a non-zero exit code - this may indicate task execution or completion failure.", task.Id);
        }
    }

    if (allTasksSuccessful)
    {
        Console.WriteLine("Success! All tasks completed successfully within the specified timeout period.");
    }

    return allTasksSuccessful;
}
```

## <a name="step-7-download-task-output"></a>Étape 7 : Télécharger le résultat de la tâche

![Télécharger le résultat de la tâche à partir du stockage][7]<br/>

Maintenant que la tâche est terminée, le résultat des tâches peut être téléchargé à partir du stockage Azure. Pour cela, avec un appel à `DownloadBlobsFromContainerAsync` dans *DotNetTutorial* `Program.cs`:

```csharp
private static async Task DownloadBlobsFromContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string directoryPath)
{
        Console.WriteLine("Downloading all files from container [{0}]...", containerName);

        // Retrieve a reference to a previously created container
        CloudBlobContainer container = blobClient.GetContainerReference(containerName);

        // Get a flat listing of all the block blobs in the specified container
        foreach (IListBlobItem item in container.ListBlobs(
                    prefix: null,
                    useFlatBlobListing: true))
        {
                // Retrieve reference to the current blob
                CloudBlob blob = (CloudBlob)item;

                // Save blob contents to a file in the specified folder
                string localOutputFile = Path.Combine(directoryPath, blob.Name);
                await blob.DownloadToFileAsync(localOutputFile, FileMode.Create);
        }

        Console.WriteLine("All files downloaded to {0}", directoryPath);
}
```

> [AZURE.NOTE] L’appel vers `DownloadBlobsFromContainerAsync` dans la *DotNetTutorial* application indique que les fichiers doivent être téléchargés vers votre `%TEMP%` dossier. N’hésitez pas à modifier cet emplacement de sortie.

## <a name="step-8-delete-containers"></a>Étape 8 : Conteneurs de supprimer

Étant donné que vous êtes chargé de données qui résident dans le stockage Azure, il est toujours judicieux de supprimer les objets BLOB qui ne sont plus utiles pour votre lots. Dans de DotNetTutorial `Program.cs`, cela avec trois appels à la méthode d’assistance `DeleteContainerAsync`:

```csharp
// Clean up Storage resources
await DeleteContainerAsync(blobClient, appContainerName);
await DeleteContainerAsync(blobClient, inputContainerName);
await DeleteContainerAsync(blobClient, outputContainerName);
```

La méthode elle-même obtient simplement une référence au conteneur, puis appelle [CloudBlobContainer.DeleteIfExistsAsync][net_container_delete]:

```csharp
private static async Task DeleteContainerAsync(
    CloudBlobClient blobClient,
    string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    if (await container.DeleteIfExistsAsync())
    {
        Console.WriteLine("Container [{0}] deleted.", containerName);
    }
    else
    {
        Console.WriteLine("Container [{0}] does not exist, skipping deletion.",
            containerName);
    }
}
```

## <a name="step-9-delete-the-job-and-the-pool"></a>Étape 9 : Supprimer le travail et le pool

Dans la dernière étape, l’utilisateur est invité à supprimer la tâche et le pool qui ont été créés par l’application DotNetTutorial. Bien que vous ne seront pas imputés pour les tâches et des tâches elles-mêmes, *sont* facturées nœuds de calcul. Par conséquent, nous vous recommandons d’allouer nœuds uniquement selon vos besoins. Suppression des pools inutilisées peut faire partie de votre processus de gestion des.

De BatchClient [JobOperations] [ net_joboperations] et [PoolOperations] [ net_pooloperations] ont des méthodes de suppression correspondantes, qui sont appelés si l’utilisateur confirme suppression :

```csharp
// Clean up the resources we've created in the Batch account if the user so chooses
Console.WriteLine();
Console.WriteLine("Delete job? [yes] no");
string response = Console.ReadLine().ToLower();
if (response != "n" && response != "no")
{
    await batchClient.JobOperations.DeleteJobAsync(JobId);
}

Console.WriteLine("Delete pool? [yes] no");
response = Console.ReadLine();
if (response != "n" && response != "no")
{
    await batchClient.PoolOperations.DeletePoolAsync(PoolId);
}
```

> [AZURE.IMPORTANT] N’oubliez pas que vous êtes chargé des ressources cluster — suppression des pools inutilisées minimiser les coûts. En outre, n’oubliez pas que la suppression d’un pool supprime tous les nœuds de calcul dans ce pool, et que toutes les données sur les nœuds irrécupérables après que le pool est supprimé.

## <a name="run-the-dotnettutorial-sample"></a>Exécuter l’exemple *DotNetTutorial*

Lorsque vous exécutez l’exemple d’application, le résultat de la console sera semblable à ce qui suit. Pendant l’exécution, vous serez confronté à une pause en `Awaiting task completion, timeout in 00:30:00...` tandis que les nœuds de calcul du pool sont démarrés. Utiliser le [portail Azure] [ azure_portal] pour analyser votre pool, calculer les nœuds, les tâches et les tâches pendant et après l’exécution. Utiliser le [portail Azure] [ azure_portal] ou de l' [Explorateur de stockage Azure] [ storage_explorers] pour afficher les ressources de stockage (conteneurs et des objets BLOB) qui sont créés par l’application.

Temps d’exécution par défaut est **environ 5 minutes** lorsque vous exécutez l’application dans sa configuration par défaut.

```
Sample start: 1/8/2016 09:42:58 AM

Container [application] created.
Container [input] created.
Container [output] created.
Uploading file C:\repos\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial\bin\Debug\TaskApplication.exe to container [application]...
Uploading file Microsoft.WindowsAzure.Storage.dll to container [application]...
Uploading file ..\..\taskdata1.txt to container [input]...
Uploading file ..\..\taskdata2.txt to container [input]...
Uploading file ..\..\taskdata3.txt to container [input]...
Creating pool [DotNetTutorialPool]...
Creating job [DotNetTutorialJob]...
Adding 3 tasks to job [DotNetTutorialJob]...
Awaiting task completion, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Downloading all files from container [output]...
All files downloaded to C:\Users\USERNAME\AppData\Local\Temp
Container [application] deleted.
Container [input] deleted.
Container [output] deleted.

Sample end: 1/8/2016 09:47:47 AM
Elapsed time: 00:04:48.5358142

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>Étapes suivantes

N’hésitez pas à apporter des modifications à *DotNetTutorial* et *TaskApplication* pour tester les scénarios cluster différent. Par exemple, essayez d’ajouter un délai d’exécution à *TaskApplication*, tels qu’avec [Thread.Sleep][net_thread_sleep], pour simuler longue tâches et les analyser dans le portail. Essayez ajoutant davantage de tâches ou en modifiant le nombre de nœuds de calcul. Ajouter une logique à rechercher et permettre l’utilisation d’un pool existant afin d’accélérer l’exécution (*Conseil*: consultez `ArticleHelpers.cs` dans les [Microsoft.Azure.Batch.Samples.Common] [ github_samples_common] projet dans les [exemples de lot azure][github_samples]).

À présent que vous avez l’habitude du flux de travail de base d’une solution lot, il est temps approfondir aux fonctionnalités supplémentaires du service lot.

- Lire la [Présentation des fonctionnalités de lot pour les développeurs](batch-api-basics.md), que nous vous recommandons pour tous les nouveaux utilisateurs du lot.
- Démarrer sur les autres articles de développement lot sous **développement approfondie** dans [lot de rubriques d’apprentissage][batch_learning_path].
- Extraire une autre implémentation de traitement de la charge de travail « mots top N » à l’aide de lot dans la [TopNWords] [ github_topnwords] exemple.

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[github_batchexplorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[github_dotnettutorial]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/DotNetTutorial
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_common]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/Common
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[net_api]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[net_api_storage]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudblobclient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobclient.aspx
[net_cloudblobcontainer]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudserviceconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudserviceconfiguration.aspx
[net_container_delete]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteifexistsasync.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_poolinfo]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.protocol.models.cloudjob.poolinformation.aspx
[net_joboperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.joboperations
[net_joboperations_terminatejob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_jobpreptask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_jobreltask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_odatadetaillevel]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_pooloperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.pooloperations
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_resourcefile_blobsource]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.blobsource.aspx
[net_sas_blob]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblob.getsharedaccesssignature.aspx
[net_sas_container]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblobcontainer.getsharedaccesssignature.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.resourcefiles.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_task_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.resourcefiles.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_taskstatemonitor]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskstatemonitor.aspx
[net_thread_sleep]: https://msdn.microsoft.com/library/274eh01d(v=vs.110).aspx
[net_virtualmachineconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.virtualmachineconfiguration.aspx
[nuget_packagemgr]: https://docs.nuget.org/consume/installing-nuget
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build
[storage_explorers]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/products/vs-2015-product-editions

[1]: ./media/batch-dotnet-get-started/batch_workflow_01_sm.png "Créer des conteneurs dans le stockage Azure"
[2]: ./media/batch-dotnet-get-started/batch_workflow_02_sm.png "Téléchargement d’application des tâches et des fichiers d’entrée (données) à des conteneurs"
[3]: ./media/batch-dotnet-get-started/batch_workflow_03_sm.png "Créer lot pool"
[4]: ./media/batch-dotnet-get-started/batch_workflow_04_sm.png "Créer des lots"
[5]: ./media/batch-dotnet-get-started/batch_workflow_05_sm.png "Ajouter des tâches à une tâche"
[6]: ./media/batch-dotnet-get-started/batch_workflow_06_sm.png "Tâches du moniteur"
[7]: ./media/batch-dotnet-get-started/batch_workflow_07_sm.png "Télécharger le résultat de la tâche à partir du stockage"
[8]: ./media/batch-dotnet-get-started/batch_workflow_sm.png "Flux de travail lot solution (diagramme complète)"
[9]: ./media/batch-dotnet-get-started/credentials_batch_sm.png "Informations d’identification du lot dans le portail"
[10]: ./media/batch-dotnet-get-started/credentials_storage_sm.png "Informations d’identification de stockage dans le portail"
[11]: ./media/batch-dotnet-get-started/batch_workflow_minimal_sm.png "Flux de travail de solution par lots (un minimum de tâches)"
