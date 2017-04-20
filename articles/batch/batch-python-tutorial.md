<properties
    pageTitle="Didacticiel : prise en main du client Azure lot Python | Microsoft Azure"
    description="Découvrez les concepts de base du lot Azure et de développer le service lot avec un scénario simple"
    services="batch"
    documentationCenter="python"
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.devlang="python"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-compute"
    ms.date="09/27/2016"
    ms.author="marsma"/>

# <a name="get-started-with-the-azure-batch-python-client"></a>Prise en main du client Azure lot Python

> [AZURE.SELECTOR]
- [.NET](batch-dotnet-get-started.md)
- [Python](batch-python-tutorial.md)

Découvrez les notions de base du [Lot Azure] [ azure_batch] et le [Lot de Python] [ py_azure_sdk] client que nous aborderons une petite application lot écrite dans Python. Nous examinons comment deux exemples de scripts utiliser le service lot pour traiter une charge de travail parallèle sur des machines virtuelles de Linux dans le cloud, et comment ils interagissent avec [Le stockage Azure](./../storage/storage-introduction.md) pour transfert de fichier et l’extraction. Vous découvrez un flux de travail application commun du lot et comprendre base des principaux composants du lot tels que des tâches, tâches, des pools et les nœuds de calcul.

![Flux de travail lot solution (basic)][11]<br/>

## <a name="prerequisites"></a>Conditions préalables

Cet article suppose que vous connaissez les bases de Python et de vous familiariser avec Linux. Il est également supposé que vous êtes en mesure de respecter les exigences de création de compte sont spécifiées en dessous pour Azure et les services de stockage et le lot.

### <a name="accounts"></a>Comptes

- **Compte Azure**: Si vous n’avez pas un abonnement Azure, [créez un compte Azure gratuit][azure_free_account].
- **Compte lot**: une fois que vous avez souscrit un abonnement Azure, [créez un compte Azure lot](batch-account-create-portal.md).
- **Compte de stockage**: voir [créer un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account) dans [les comptes de stockage sur Azure](../storage/storage-create-storage-account.md).

### <a name="code-sample"></a>Exemple de code

[L’exemple de code] didacticiels Python[ github_article_samples] est un des nombreux exemples de code lot figurant dans les [exemples de lot azure] [ github_samples] référentiel de GitHub. Vous pouvez télécharger tous les exemples en cliquant sur **cloner ou téléchargement > Télécharger le code postal** sur la page d’accueil référentiel, ou en cliquant sur le [azure-lot-exemples-master.zip] [ github_samples_zip] lien de téléchargement direct. Une fois que vous avez extrait le contenu du fichier ZIP, les deux scripts pour ce didacticiel sont trouvent dans la `article_samples` répertoire :

`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_client.py`<br/>
`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_task.py`

### <a name="python-environment"></a>Environnement Python

Pour exécuter l’exemple de script *python_tutorial_client.py* sur votre ordinateur local, vous devez disposer d’un **relais Python** compatible avec la version **2.7** ou **3.3 +**. Le script a été testé sur Linux et Windows.

### <a name="cryptography-dependencies"></a>dépendances de chiffrement

Vous devez installer les dépendances pour le [chiffrement] [ crypto] bibliothèque, requise par le `azure-batch` et `azure-storage` packages Python. Effectuez l’une des opérations suivantes adaptées à votre plateforme ou s’y référer à l' [installation de chiffrement] [ crypto_install] détails pour plus d’informations :

* Ubuntu

    `apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython-dev python-dev`

* CentOS

    `yum update && yum install -y gcc openssl-dev libffi-devel python-devel`

* SLES/OpenSUSE

    `zypper ref && zypper -n in libopenssl-dev libffi48-devel python-devel`

* Windows

    `pip install cryptography`

>[AZURE.NOTE] Si vous installez pour Python 3.3 + sous Linux, utilisez les équivalents python3 les dépendances Python. Par exemple, sur Ubuntu :`apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython3-dev python3-dev`

### <a name="azure-packages"></a>Packages Azure

Ensuite, installez les packages **Lot Azure** et **Stockage Azure** Python. Vous pouvez effectuer ceci avec **pip** et la *requirements.txt* ici :

`/azure-batch-samples/Python/Batch/requirements.txt`

Problème commande **pip** pour installer les modules de stockage et lot suivante :

`pip install -r requirements.txt`

Vous pouvez également installer le [lot d’azure] [ pypi_batch] et [stockage azure] [ pypi_storage] Python packages manuellement :

`pip install azure-batch`<br/>
`pip install azure-storage`

> [AZURE.TIP] Vous devrez peut-être préfixe vos commandes avec `sudo` si vous utilisez un compte sans privilège. Par exemple, `sudo pip install -r requirements.txt`. Pour plus d’informations sur l’installation de packages Python, consultez [L’installation de Packages] [ pypi_install] sur readthedocs.io.

## <a name="batch-python-tutorial-code-sample"></a>Exemple de code du didacticiel Python lot

L’exemple de code du didacticiel Python lot se compose de deux scripts Python et quelques fichiers de données.

- **python_tutorial_client.py**: interagit avec les services de stockage et le lot pour exécuter une charge de travail en parallèle sur les nœuds de calcul (machines virtuelles). Le script *python_tutorial_client.py* s’exécute sur votre ordinateur local.

- **python_tutorial_task.py**: le script qui s’exécute sur les nœuds dans Azure pour effectuer le travail réel de calcul. Dans l’échantillon, *python_tutorial_task.py* analyse le texte dans un fichier téléchargé à partir du stockage Azure (le fichier d’entrée). Ensuite, elle produit un fichier texte (le fichier de sortie) qui contient la liste des trois mots principales qui s’affichent dans le fichier d’entrée. Après avoir créé le fichier de copie, *python_tutorial_task.py* télécharge le fichier vers le stockage Azure. Cela rend disponible au téléchargement le script client s’exécutant sur votre poste de travail. Le script *python_tutorial_task.py* s’exécute en parallèle sur plusieurs nœuds de calcul dans le service lot.

- **./data/taskdata\*.txt**: ces fichiers trois texte fournissent l’entrée pour les tâches qui s’exécutent sur les nœuds de calcul.

Le diagramme suivant illustre les opérations principales qui sont effectuées par les scripts client et tâche. Ce flux de travail est classique de nombreuses solutions cluster créés dans des lots. Tandis que celle-ci ne présente pas toutes les fonctionnalités disponibles dans le service lot, presque toutes scénario lot comprend des parties de ce flux de travail.

![Flux de travail lot exemple][8]<br/>

[**Étape 1.**](#step-1-create-storage-containers) Créer des **conteneurs** de stockage d’objets Blob Azure.<br/>
[**Étape 2.**](#step-2-upload-task-script-and-data-files) Télécharger des fichiers de script et d’entrée de tâche aux conteneurs.<br/>
[**Étape 3.**](#step-3-create-batch-pool) Créer un lot **pool**.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3 a.** Le pool de **tâche de début** télécharge le script de tâche (python_tutorial_task.py) à des nœuds dès qu’ils rejoignent le pool.<br/>
[**Étape 4.**](#step-4-create-batch-job) Créer une **tâche**.<br/>
[**Étape 5.**](#step-5-add-tasks-to-job) Ajouter des **tâches** à la tâche.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5 a.** Les tâches sont planifiées pour s’exécuter sur des nœuds.<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5 b.** Chaque tâche télécharge ses données d’entrée à partir du stockage Azure, puis commence à s’exécuter.<br/>
[**Étape 6.**](#step-6-monitor-tasks) Contrôler des tâches.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6 a.** Comme tâches terminées, ils téléchargent les données de sortie au stockage Azure.<br/>
[**Étape 7.**](#step-7-download-task-output) Téléchargez la sortie des tâches à partir du stockage.

Comme indiquée, chaque solution lot effectue ces étapes exactes et peut inclure les nombreux plus, mais cette illustre processus courants figurant dans une solution lot.

## <a name="prepare-client-script"></a>Préparer le script client

Avant d’exécuter l’échantillon, ajoutez vos informations d’identification de compte de stockage et le lot à *python_tutorial_client.py*. Si vous n’avez pas déjà fait, ouvrez le fichier dans votre éditeur favori et mettre à jour les lignes suivantes avec vos informations d’identification.

```python
# Update the Batch and Storage account credential strings below with the values
# unique to your accounts. These are used when constructing connection strings
# for the Batch and Storage client objects.

# Batch account credentials
batch_account_name = "";
batch_account_key  = "";
batch_account_url  = "";

# Storage account credentials
storage_account_name = "";
storage_account_key  = "";
```

Vous pouvez trouver vos informations d’identification compte lot et le stockage au sein de la carte de compte de chaque service dans le [portail Azure][azure_portal]:

![Informations d’identification dans le portail de commandes][9]
![informations d’identification de stockage sur le portail][10]<br/>

Dans les sections suivantes, nous analyser les étapes permettant de traiter une charge de travail dans le service lot par les scripts. Nous vous invitons à consulter régulièrement les scripts dans votre éditeur pendant que vous débutez le reste de l’article.

Accédez à la ligne suivante dans **python_tutorial_client.py** pour commencer à l’étape 1 :

```python
if __name__ == '__main__':
```

## <a name="step-1-create-storage-containers"></a>Étape 1 : Créer des conteneurs de stockage

![Créer des conteneurs dans le stockage Azure][1]
<br/>

Traitement par lots prend en charge l’interaction avec le stockage Azure. Conteneurs dans votre compte de stockage constituent les fichiers requis par les tâches qui s’exécutent dans votre compte lot. Les conteneurs fournissent également un emplacement pour stocker les données de sortie produisant les tâches. La première chose que le script *python_tutorial_client.py* effectue est créer trois conteneurs dans le [Stockage Blob Azure](../storage/storage-introduction.md#blob-storage):

- **application**: ce conteneur stockera le script Python exécuté par les tâches, *python_tutorial_task.py*.
- **Saisie**: tâches télécharger les fichiers de données à traiter à partir du conteneur *d’entrée* .
- **sortie**: tâches issue de traitement du fichier d’entrée, ils sont-elles immédiatement les résultats pour le conteneur de *sortie* .

Pour interagir avec un compte de stockage et de créer des conteneurs, nous utilisons le [stockage azure] [ pypi_storage] package pour créer un [BlockBlobService] [ py_blockblobservice] objet : « client blob ». Nous ensuite créer trois conteneurs dans le compte de stockage en utilisant le client blob.

```python
 # Create the blob client, for use in obtaining references to
 # blob storage containers and uploading files to containers.
 blob_client = azureblob.BlockBlobService(
     account_name=_STORAGE_ACCOUNT_NAME,
     account_key=_STORAGE_ACCOUNT_KEY)

 # Use the blob client to create the containers in Azure Storage if they
 # don't yet exist.
 app_container_name = 'application'
 input_container_name = 'input'
 output_container_name = 'output'
 blob_client.create_container(app_container_name, fail_on_exist=False)
 blob_client.create_container(input_container_name, fail_on_exist=False)
 blob_client.create_container(output_container_name, fail_on_exist=False)
```

Une fois que les conteneurs ont été créés, l’application pouvez maintenant télécharger les fichiers qui seront utilisés par les tâches.

> [AZURE.TIP] [L’utilisation de stockage d’objets Blob Azure à partir de Python](../storage/storage-python-how-to-use-blob-storage.md) fournit un aperçu de l’utilisation d’objets BLOB et conteneurs de stockage Azure. Il doit être dans la partie supérieure de votre liste de lecture que vous commencez à travailler avec lot.

## <a name="step-2-upload-task-script-and-data-files"></a>Étape 2 : Télécharger des fichiers de script et les données de tâche

![Téléchargement d’application des tâches et des fichiers d’entrée (données) à des conteneurs][2]
<br/>

Dans l’opération de téléchargement de fichier, *python_tutorial_client.py* définit tout d’abord les collections **d’application** et **entrée** chemins d’accès tels qu’ils existent sur l’ordinateur local. Puis il télécharge ces fichiers aux conteneurs que vous avez créé à l’étape précédente.

```python
 # Paths to the task script. This script will be executed by the tasks that
 # run on the compute nodes.
 application_file_paths = [os.path.realpath('python_tutorial_task.py')]

 # The collection of data files that are to be processed by the tasks.
 input_file_paths = [os.path.realpath('./data/taskdata1.txt'),
                     os.path.realpath('./data/taskdata2.txt'),
                     os.path.realpath('./data/taskdata3.txt')]

 # Upload the application script to Azure Storage. This is the script that
 # will process the data files, and is executed by each of the tasks on the
 # compute nodes.
 application_files = [
     upload_file_to_container(blob_client, app_container_name, file_path)
     for file_path in application_file_paths]

 # Upload the data files. This is the data that will be processed by each of
 # the tasks executed on the compute nodes in the pool.
 input_files = [
     upload_file_to_container(blob_client, input_container_name, file_path)
     for file_path in input_file_paths]
```

À l’aide de compréhension de liste, la `upload_file_to_container` fonction est appelée pour chaque fichier dans les collections et deux [ResourceFile] [ py_resource_file] collections sont remplies. La `upload_file_to_container` fonction apparaît ci-dessous :

```
def upload_file_to_container(block_blob_client, container_name, file_path):
    """
    Uploads a local file to an Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param str container_name: The name of the Azure Blob storage container.
    :param str file_path: The local path to the file.
    :rtype: `azure.batch.models.ResourceFile`
    :return: A ResourceFile initialized with a SAS URL appropriate for Batch
    tasks.
    """
    blob_name = os.path.basename(file_path)

    print('Uploading file {} to container [{}]...'.format(file_path,
                                                          container_name))

    block_blob_client.create_blob_from_path(container_name,
                                            blob_name,
                                            file_path)

    sas_token = block_blob_client.generate_blob_shared_access_signature(
        container_name,
        blob_name,
        permission=azureblob.BlobPermissions.READ,
        expiry=datetime.datetime.utcnow() + datetime.timedelta(hours=2))

    sas_url = block_blob_client.make_blob_url(container_name,
                                              blob_name,
                                              sas_token=sas_token)

    return batchmodels.ResourceFile(file_path=blob_name,
                                    blob_source=sas_url)
```

### <a name="resourcefiles"></a>ResourceFiles

Un [ResourceFile] [ py_resource_file] fournit des tâches dans un lot avec l’URL vers un fichier dans le stockage Azure qui est téléchargé sur un nœud de calcul avant l’exécution de cette tâche. La [ResourceFile][py_resource_file]. propriété **blob_source** Spécifie l’URL complète du fichier tel qu’il existe dans le stockage Azure. L’URL peut-être également inclure une signature accès partagé (sa) qui offre un accès sécurisé pour le fichier. La plupart des types de tâche dans lot comprennent une propriété *ResourceFiles* , y compris :

- [CloudTask][py_task]
- [Tâche de début][py_starttask]
- [JobPreparationTask][py_jobpreptask]
- [JobReleaseTask][py_jobreltask]

Cet exemple n’utilise pas les types de tâches JobPreparationTask ou JobReleaseTask, mais vous pouvez en savoir plus sur les [tâches de préparation et l’achèvement de projet sur Azure lot exécuter les nœuds de calcul](batch-job-prep-release.md).

### <a name="shared-access-signature-sas"></a>Signature d’un accès partagé (sa)

Accès partagé signatures sont des chaînes qui fournissent un accès sécurisé aux conteneurs et des objets BLOB dans le stockage Azure. Le script *python_tutorial_client.py* utilise les deux blob et conteneur partagé signatures access et montre comment obtenir ces chaînes de signature accès partagé à partir du service de stockage.

- **BLOB partagés accès signatures**: utilisations de tâche de début du pool blob signatures accès partagé lorsqu’il télécharge les fichiers de données de script et d’entrée de tâche à partir du stockage (voir l' [étape 3](#step-3-create-batch-pool) ci-dessous). La `upload_file_to_container` fonction dans *python_tutorial_client.py* contient le code qui obtient la signature d’un accès partagé de chaque blob. Il fait en appelant [BlockBlobService.make_blob_url] [ py_make_blob_url] dans le module de stockage.

- **Signature d’un accès partagé conteneur**: comme chaque tâche termine son travail sur le nœud de calcul, il télécharge son fichier de sortie vers le conteneur de *sortie* dans le stockage Azure. Pour ce faire, *python_tutorial_task.py* utilise une signature d’un accès partagé conteneur qui fournit l’accès en écriture au conteneur. La `get_container_sas_token` fonction dans *python_tutorial_client.py* Obtient une signature accès partagé du conteneur, qui est ensuite passée comme un argument de ligne de commande pour les tâches. Étape 5 #, [Ajouter des tâches à une tâche](#step-5-add-tasks-to-job), traite de l’utilisation du conteneur associations de sécurité.

> [AZURE.TIP] Consultez la série en deux parties sur les signatures accès partagé, [partie 1 : présentation du modèle de sa](../storage/storage-dotnet-shared-access-signature-part-1.md) et [partie 2 : créer et utiliser une associations de sécurité avec le service Blob](../storage/storage-dotnet-shared-access-signature-part-2.md), pour en savoir plus sur fournir un accès sécurisé aux données dans votre compte de stockage.

## <a name="step-3-create-batch-pool"></a>Étape 3 : Créer lot pool

![Créer un pool de traitement par lots][3]
<br/>

Un lot **pool** est un ensemble de nœuds de calcul (machines virtuelles) sur lequel lot exécute les tâches d’un travail.

Une fois qu’il télécharge les fichiers de script et les données de tâche pour le compte de stockage, *python_tutorial_client.py* démarre son interaction avec le service lot à l’aide du module lot Python. Pour cela, une [BatchServiceClient] [ py_batchserviceclient] est créé :

```python
 # Create a Batch service client. We'll now be interacting with the Batch
 # service in addition to Storage.
 credentials = batchauth.SharedKeyCredentials(_BATCH_ACCOUNT_NAME,
                                              _BATCH_ACCOUNT_KEY)

 batch_client = batch.BatchServiceClient(
     credentials,
     base_url=_BATCH_ACCOUNT_URL)
```

Ensuite, un pool de nœuds de calcul est créé dans le compte lot avec un appel à `create_pool`.

```python
def create_pool(batch_service_client, pool_id,
                resource_files, publisher, offer, sku):
    """
    Creates a pool of compute nodes with the specified OS settings.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str pool_id: An ID for the new pool.
    :param list resource_files: A collection of resource files for the pool's
    start task.
    :param str publisher: Marketplace image publisher
    :param str offer: Marketplace image offer
    :param str sku: Marketplace image sku
    """
    print('Creating pool [{}]...'.format(pool_id))

    # Create a new pool of Linux compute nodes using an Azure Virtual Machines
    # Marketplace image. For more information about creating pools of Linux
    # nodes, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/

    # Specify the commands for the pool's start task. The start task is run
    # on each node as it joins the pool, and when it's rebooted or re-imaged.
    # We use the start task to prep the node for running our task script.
    task_commands = [
        # Copy the python_tutorial_task.py script to the "shared" directory
        # that all tasks that run on the node have access to.
        'cp -r $AZ_BATCH_TASK_WORKING_DIR/* $AZ_BATCH_NODE_SHARED_DIR',
        # Install pip and the dependencies for cryptography
        'apt-get update',
        'apt-get -y install python-pip',
        'apt-get -y install build-essential libssl-dev libffi-dev python-dev',
        # Install the azure-storage module so that the task script can access
        # Azure Blob storage
        'pip install azure-storage']

    # Get the node agent SKU and image reference for the virtual machine
    # configuration.
    # For more information about the virtual machine configuration, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/
    sku_to_use, image_ref_to_use = \
        common.helpers.select_latest_verified_vm_image_with_node_agent_sku(
            batch_service_client, publisher, offer, sku)

    new_pool = batch.models.PoolAddParameter(
        id=pool_id,
        virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
            image_reference=image_ref_to_use,
            node_agent_sku_id=sku_to_use),
        vm_size=_POOL_VM_SIZE,
        target_dedicated=_POOL_NODE_COUNT,
        start_task=batch.models.StartTask(
            command_line=
            common.helpers.wrap_commands_in_shell('linux', task_commands),
            run_elevated=True,
            wait_for_success=True,
            resource_files=resource_files),
        )

    try:
        batch_service_client.pool.add(new_pool)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

Lorsque vous créez un pool, vous définissez une [PoolAddParameter] [ py_pooladdparam] indiquant les diverses propriétés du pool :

- **ID** du pool (*id* - obligatoire)<p/>Comme avec la plupart des entités dans le lot, votre nouveau pool doit avoir un ID unique au sein de votre compte lot. Votre code fait référence à ce pool à l’aide de son ID, et il est comment vous identifiez le pool dans le [portail]Azure[azure_portal].

- **Nombre de nœuds de calcul** (*target_dedicated* - obligatoire)<p/>Cette propriété indique combien machines virtuelles doivent être déployés dans le pool. Il est important de noter que tous les comptes lot ont un par défaut **quota** qui limite le nombre de **cœurs** (et par conséquent, les nœuds de calcul) dans un compte lot. Vous pouvez trouver les quotas par défaut et les instructions sur la façon d' [augmenter un quota](batch-quota-limit.md#increase-a-quota) (par exemple, le nombre maximal de cœurs dans votre compte lot) dans [les Quotas et les limites pour le service lot Azure](batch-quota-limit.md). Si vous vous trouvez demandant « Pourquoi ne mon pool atteigne plus de X nœuds ? » Ce quota core peut être la cause.

- **Système d’exploitation** pour les nœuds (*virtual_machine_configuration* **ou** *cloud_service_configuration* - obligatoire)<p/>Dans *python_tutorial_client.py*, nous créons une liste des nœuds Linux à l’aide d’un [VirtualMachineConfiguration][py_vm_config]. La `select_latest_verified_vm_image_with_node_agent_sku` fonctionnent dans `common.helpers` simplifie l’utilisation des [Machines virtuelles Azure Marketplace] [ vm_marketplace] images. Pour plus d’informations sur l’utilisation d’images Marketplace, consultez [Linux disposition nœuds dans des pools d’Azure lot de calcul](batch-linux-nodes.md) .

- **Taille des nœuds de calcul** (*vm_size* - obligatoire)<p/>Étant donné que nous devons spécifier des nœuds Linux pour notre [VirtualMachineConfiguration][py_vm_config], nous spécifiez une taille de la mémoire virtuelle (`STANDARD_A1` dans cet exemple) à partir des [tailles pour les machines virtuelles dans Azure](../virtual-machines/virtual-machines-linux-sizes.md). Là encore, voir [mise en service Linux nœuds dans des pools d’Azure lot](batch-linux-nodes.md) pour plus d’informations.

- **Démarrer la tâche** (*start_task* - ne pas obligatoire)<p/>Avec l’au-dessus de propriétés du nœud physique, vous pouvez également spécifier une [tâche de début] [ py_starttask] pour le pool (il n’est pas obligatoire). La tâche de début s’exécute sur chaque nœud comme ce nœud rejoint le pool, chaque fois qu’un nœud a redémarré. La tâche de début est particulièrement utile de préparation des nœuds de calcul pour l’exécution de tâches, telles que l’installation des applications qui s’exécutent de vos tâches.<p/>Dans cet exemple d’application, la tâche de début copie les fichiers qu’il télécharge à partir du stockage (qui sont spécifiées à l’aide **resource_files** propriété de la tâche de début) de la tâche de début *répertoire de travail* dans le répertoire *partagé* qui peuvent accéder à toutes les tâches en cours d’exécution sur le nœud. Pour l’essentiel, cette action copie `python_tutorial_task.py` au répertoire partagé sur chaque nœud comme le nœud rejoint le pool, afin que toutes les tâches qui s’exécutent sur le nœud d’y accéder.

Vous pouvez remarquer l’appel vers la `wrap_commands_in_shell` fonction d’assistance. Cette fonction prend une collection de commandes distinctes et crée une seule ligne de commande appropriée pour la propriété de ligne de commande d’une tâche.

Également intéressantes dans l’extrait de code ci-dessus consiste à utiliser deux variables d’environnement dans la propriété de **lettre** de la tâche de début : `AZ_BATCH_TASK_WORKING_DIR` et `AZ_BATCH_NODE_SHARED_DIR`. Chaque nœud de calcul au sein d’un pool lot est configuré automatiquement avec plusieurs variables d’environnement spécifiques au lot. N’importe quel processus est exécutée par une tâche a accès à ces variables d’environnement.

> [AZURE.TIP] Pour en savoir plus sur les variables d’environnement qui sont disponibles sur les nœuds de calcul dans un pool lot, ainsi que des informations sur des répertoires de travail de tâche, voir **paramètres d’environnement pour les tâches** et des **fichiers et des répertoires** dans la [vue d’ensemble des fonctionnalités de lot Azure](batch-api-basics.md).

## <a name="step-4-create-batch-job"></a>Étape 4 : Créer par lots

![Créer des lots][4]<br/>

Une **tâche** est un ensemble de tâches et est associé à un pool de nœuds de calcul. Les tâches d’un travail s’exécutent sur nœuds de calcul du pool associé.

Vous pouvez utiliser une tâche non seulement pour organiser et suivi des tâches dans les charges de travail associées, mais également pour imposer certaines contraintes, telles que le runtime maximal pour le travail (et par extension, ses tâches) et priorité par rapport aux autres tâches dans le compte du lot de travail. Dans cet exemple, le travail est associé uniquement à la liste qui a été créée à l’étape 3 #. Aucune propriété supplémentaire n’est configurées.

Toutes les tâches par lots sont associées à un pool spécifique. Cette association indique les nœuds tâches du projet s’exécutent sur. Vous spécifiez le pool à l’aide de la [PoolInformation] [ py_poolinfo] propriété, comme le montre l’extrait de code ci-dessous.

```python
def create_job(batch_service_client, job_id, pool_id):
    """
    Creates a job with the specified ID, associated with the specified pool.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID for the job.
    :param str pool_id: The ID for the pool.
    """
    print('Creating job [{}]...'.format(job_id))

    job = batch.models.JobAddParameter(
        job_id,
        batch.models.PoolInformation(pool_id=pool_id))

    try:
        batch_service_client.job.add(job)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

À présent qu’une tâche a été créée, les tâches sont ajoutées à effectuer le travail.

## <a name="step-5-add-tasks-to-job"></a>Étape 5 : Ajouter des tâches à travail

![Ajouter des tâches à une tâche][5]<br/>
*(1) tâches sont ajoutées à la tâche, (2) les tâches sont planifiées pour s’exécuter sur des nœuds et (3) les tâches téléchargement les fichiers de données à traiter*

Lot les **tâches** sont les unités de travail individuelles qui s’exécutent sur les nœuds de calcul. Une tâche a une ligne de commande et exécute les scripts ou les exécutables que vous spécifiez dans cette ligne de commande.

Pour effectuer le travail en réalité, les tâches doivent être ajoutés à une tâche. Chaque [CloudTask] [ py_task] est configuré avec une propriété de ligne de commande et [ResourceFiles] [ py_resource_file] (comme tâche de début du pool) que la tâche télécharge sur le nœud avant sa ligne de commande s’exécute automatiquement. Dans l’échantillon, chaque tâche ne traite qu’un seul fichier. Par conséquent, sa collection ResourceFiles contient un seul élément.

```python
def add_tasks(batch_service_client, job_id, input_files,
              output_container_name, output_container_sas_token):
    """
    Adds a task for each input file in the collection to the specified job.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID of the job to which to add the tasks.
    :param list input_files: A collection of input files. One task will be
     created for each input file.
    :param output_container_name: The ID of an Azure Blob storage container to
    which the tasks will upload their results.
    :param output_container_sas_token: A SAS token granting write access to
    the specified Azure Blob storage container.
    """

    print('Adding {} tasks to job [{}]...'.format(len(input_files), job_id))

    tasks = list()

    for input_file in input_files:

        command = ['python $AZ_BATCH_NODE_SHARED_DIR/python_tutorial_task.py '
                   '--filepath {} --numwords {} --storageaccount {} '
                   '--storagecontainer {} --sastoken "{}"'.format(
                    input_file.file_path,
                    '3',
                    _STORAGE_ACCOUNT_NAME,
                    output_container_name,
                    output_container_sas_token)]

        tasks.append(batch.models.TaskAddParameter(
                'topNtask{}'.format(input_files.index(input_file)),
                wrap_commands_in_shell('linux', command),
                resource_files=[input_file]
                )
        )

    batch_service_client.task.add_collection(job_id, tasks)
```

> [AZURE.IMPORTANT] Lorsqu’ils accèdent variables d’environnement telles que `$AZ_BATCH_NODE_SHARED_DIR` ou exécuter une application non trouvée dans le nœud `PATH`, lignes commande tâche doit appeler le shell explicitement, par exemple avec `/bin/sh -c MyTaskApplication $MY_ENV_VAR`. Cette exigence n’est pas nécessaire si vos tâches exécutent d’une application dans le nœud `PATH` et ne pas référencer les variables d’environnement.

Dans la `for` boucle dans l’extrait de code ci-dessus, vous pouvez voir que la ligne de commande de la tâche est construite avec cinq arguments de ligne de commande qui sont passés à *python_tutorial_task.py*:

1. **chemin d’accès**: il s’agit du chemin d’accès local vers le fichier tel qu’il existe sur le nœud. Lorsque la ResourceFile objet dans `upload_file_to_container` a été créé à l’étape 2 ci-dessus, le nom du fichier a été utilisé pour cette propriété (la `file_path` paramètre dans le constructeur ResourceFile). Cela indique que le fichier se trouve dans le même répertoire sur le nœud en tant que *python_tutorial_task.py*.

2. **NUMWORDS**: les mots *N* supérieure doivent être écrites dans le fichier de copie.

3. **storageaccount**: le nom du compte de stockage qui possède le conteneur dans lequel le résultat de la tâche doit être téléchargé.

4. **storagecontainer**: le nom du conteneur de stockage à laquelle les fichiers de sortie doivent être téléchargés.

5. **sastoken**: la signature d’un accès partagé (sa) qui fournit l’accès en écriture au conteneur de **sortie** dans le stockage Azure. Le script *python_tutorial_task.py* utilise cette signature accès partagé lorsque crée sa référence BlockBlobService. Cette offre un accès écriture au conteneur sans exiger une touche d’accès rapide pour le compte de stockage.

```python
# NOTE: Taken from python_tutorial_task.py

# Create the blob client using the container's SAS token.
# This allows us to create a client that provides write
# access only to the container.
blob_client = azureblob.BlockBlobService(account_name=args.storageaccount,
                                         sas_token=args.sastoken)
```

## <a name="step-6-monitor-tasks"></a>Étape 6 : Tâches du moniteur

![Tâches du moniteur][6]<br/>
*Le script (1) surveille les tâches pour l’état d’achèvement et (2) les tâches téléchargement des données de résultat au stockage Azure*

Lorsque les tâches sont ajoutées à une tâche, ils sont automatiquement en file d’attente et planifiées pour une exécution sur les nœuds de calcul au sein du pool associé au travail. Selon les paramètres que vous spécifiez, lot gère toutes les tâches queuing, planification, une nouvelle tentative et autres tâches d’administration de tâche à votre place.

Il existe de nombreuses approches à l’analyse de l’exécution des tâches. La `wait_for_tasks_to_complete` fonction dans *python_tutorial_client.py* fournit un exemple simple de surveillance des tâches pour un état spécifique, dans ce cas, le [terminé] [ py_taskstate] état.

```python
def wait_for_tasks_to_complete(batch_service_client, job_id, timeout):
    """
    Returns when all tasks in the specified job reach the Completed state.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The id of the job whose tasks should be to monitored.
    :param timedelta timeout: The duration to wait for task completion. If all
    tasks in the specified job do not reach Completed state within this time
    period, an exception will be raised.
    """
    timeout_expiration = datetime.datetime.now() + timeout

    print("Monitoring all tasks for 'Completed' state, timeout in {}..."
          .format(timeout), end='')

    while datetime.datetime.now() < timeout_expiration:
        print('.', end='')
        sys.stdout.flush()
        tasks = batch_service_client.task.list(job_id)

        incomplete_tasks = [task for task in tasks if
                            task.state != batchmodels.TaskState.completed]
        if not incomplete_tasks:
            print()
            return True
        else:
            time.sleep(1)

    print()
    raise RuntimeError("ERROR: Tasks did not reach 'Completed' state within "
                       "timeout period of " + str(timeout))
```

## <a name="step-7-download-task-output"></a>Étape 7 : Télécharger le résultat de la tâche

![Télécharger le résultat de la tâche à partir du stockage][7]<br/>

Maintenant que la tâche est terminée, le résultat des tâches peut être téléchargé à partir du stockage Azure. Pour cela, avec un appel à `download_blobs_from_container` dans *python_tutorial_client.py*:

```python
def download_blobs_from_container(block_blob_client,
                                  container_name, directory_path):
    """
    Downloads all blobs from the specified Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param container_name: The Azure Blob storage container from which to
     download files.
    :param directory_path: The local directory to which to download the files.
    """
    print('Downloading all files from container [{}]...'.format(
        container_name))

    container_blobs = block_blob_client.list_blobs(container_name)

    for blob in container_blobs.items:
        destination_file_path = os.path.join(directory_path, blob.name)

        block_blob_client.get_blob_to_path(container_name,
                                           blob.name,
                                           destination_file_path)

        print('  Downloaded blob [{}] from container [{}] to {}'.format(
            blob.name,
            container_name,
            destination_file_path))

    print('  Download complete!')
```

> [AZURE.NOTE] L’appel vers `download_blobs_from_container` dans *python_tutorial_client.py* Spécifie que les fichiers doivent être téléchargés vers votre répertoire de base. N’hésitez pas à modifier cet emplacement de sortie.

## <a name="step-8-delete-containers"></a>Étape 8 : Conteneurs de supprimer

Étant donné que vous êtes chargé de données qui résident dans le stockage Azure, il est toujours judicieux de supprimer les objets BLOB qui ne sont plus utiles pour votre lots. Dans *python_tutorial_client.py*, cela est fait avec trois appels à [BlockBlobService.delete_container][py_delete_container]:

```
# Clean up storage resources
print('Deleting containers...')
blob_client.delete_container(app_container_name)
blob_client.delete_container(input_container_name)
blob_client.delete_container(output_container_name)
```

## <a name="step-9-delete-the-job-and-the-pool"></a>Étape 9 : Supprimer le travail et le pool

Dans la dernière étape, vous êtes invité à supprimer la tâche et le pool qui ont été créés par le script *python_tutorial_client.py* . Bien que vous ne seront pas imputés pour les tâches et des tâches elles-mêmes, *sont* facturées nœuds de calcul. Par conséquent, nous vous recommandons d’allouer nœuds uniquement selon vos besoins. Suppression des pools inutilisées peut faire partie de votre processus de gestion des.

De BatchServiceClient [JobOperations] [ py_job] et [PoolOperations] [ py_pool] ont des méthodes de suppression correspondantes, qui sont appelés si vous confirmez la suppression :

```python
# Clean up Batch resources (if the user so chooses).
if query_yes_no('Delete job?') == 'yes':
    batch_client.job.delete(_JOB_ID)

if query_yes_no('Delete pool?') == 'yes':
    batch_client.pool.delete(_POOL_ID)
```

> [AZURE.IMPORTANT] N’oubliez pas que vous êtes chargé des ressources cluster--suppression des pools inutilisées minimiser les coûts. En outre, n’oubliez pas que la suppression d’un pool supprime tous les nœuds de calcul dans ce pool, et que toutes les données sur les nœuds irrécupérables après que le pool est supprimé.

## <a name="run-the-sample-script"></a>Exécuter l’exemple de script

Lorsque vous exécutez le script *python_tutorial_client.py* à partir de [l’exemple de code]didacticiels[github_article_samples], le résultat de la console est semblable à ce qui suit. Il existe une pause en `Monitoring all tasks for 'Completed' state, timeout in 0:20:00...` tandis que les nœuds de calcul du pool sont créés, démarré, et les commandes dans les tâches de début du pool sont exécutées. Utiliser le [portail Azure] [ azure_portal] pour analyser votre pool, calculer les nœuds, les tâches et les tâches pendant et après l’exécution. Utiliser le [portail Azure] [ azure_portal] ou de l' [Explorateur de stockage de Microsoft Azure] [ storage_explorer] pour afficher les ressources de stockage (conteneurs et des objets BLOB) qui sont créés par l’application.

>[AZURE.TIP] Exécuter le script *python_tutorial_client.py* depuis la `azure-batch-samples/Python/Batch/article_samples` répertoire. Il utilise un chemin d’accès relatif pour le `common.helpers` importation du module, vous voyez `ImportError: No module named 'common'` si vous n’exécutez la le script à partir de ce répertoire.

Temps d’exécution par défaut est **environ 5 à 7 minutes** lorsque vous exécutez l’exemple dans sa configuration par défaut.

```
Sample start: 2016-05-20 22:47:10

Uploading file /home/user/py_tutorial/python_tutorial_task.py to container [application]...
Uploading file /home/user/py_tutorial/data/taskdata1.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata2.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata3.txt to container [input]...
Creating pool [PythonTutorialPool]...
Creating job [PythonTutorialJob]...
Adding 3 tasks to job [PythonTutorialJob]...
Monitoring all tasks for 'Completed' state, timeout in 0:20:00..........................................................................
  Success! All tasks reached the 'Completed' state within the specified timeout period.
Downloading all files from container [output]...
  Downloaded blob [taskdata1_OUTPUT.txt] from container [output] to /home/user/taskdata1_OUTPUT.txt
  Downloaded blob [taskdata2_OUTPUT.txt] from container [output] to /home/user/taskdata2_OUTPUT.txt
  Downloaded blob [taskdata3_OUTPUT.txt] from container [output] to /home/user/taskdata3_OUTPUT.txt
  Download complete!
Deleting containers...

Sample end: 2016-05-20 22:53:12
Elapsed time: 0:06:02

Delete job? [Y/n]
Delete pool? [Y/n]

Press ENTER to exit...
```

## <a name="next-steps"></a>Étapes suivantes

N’hésitez pas à apporter des modifications à *python_tutorial_client.py* et *python_tutorial_task.py* pour tester les scénarios cluster différent. Par exemple, essayez d’ajouter un délai d’exécution à *python_tutorial_task.py* pour simuler longue tâches et les analyser dans le portail. Essayez ajoutant davantage de tâches ou en modifiant le nombre de nœuds de calcul. Ajouter une logique à rechercher et permettre l’utilisation d’un pool existant afin d’accélérer l’exécution.

À présent que vous avez l’habitude du flux de travail de base d’une solution lot, il est temps approfondir aux fonctionnalités supplémentaires du service lot.

- Passez en revue l’article de la [vue d’ensemble du lot Azure fonctionnalités](batch-api-basics.md) que nous vous recommandons si vous débutez avec le service.
- Démarrer sur les autres articles de développement lot sous **développement approfondie** dans [lot de rubriques d’apprentissage][batch_learning_path].
- Extraire une autre implémentation de traitement de la charge de travail « mots top N » traitement par lots dans le [TopNWords] [ github_topnwords] exemple.

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[blog_linux]: http://blogs.technet.com/b/windowshpc/archive/2016/03/30/introducing-linux-support-on-azure-batch.aspx
[crypto]: https://cryptography.io/en/latest/
[crypto_install]: https://cryptography.io/en/latest/installation/
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[github_article_samples]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/article_samples

[nuget_packagemgr]: https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build

[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_batchserviceclient]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html#azure.batch.BatchServiceClient
[py_blockblobservice]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.blockblobservice.html#azure.storage.blob.blockblobservice.BlockBlobService
[py_cloudtask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_cs_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudServiceConfiguration
[py_delete_container]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.delete_container
[py_gen_blob_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_blob_shared_access_signature
[py_gen_container_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_container_shared_access_signature
[py_image_ref]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_job]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.JobOperations
[py_jobpreptask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobPreparationTask
[py_jobreltask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobReleaseTask
[py_list_skus]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[py_make_blob_url]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.make_blob_url
[py_pool]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.PoolOperations
[py_pooladdparam]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolAddParameter
[py_poolinfo]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolInformation
[py_resource_file]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ResourceFile
[py_samples_github]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_task]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_taskstate]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.TaskState
[py_vm_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.VirtualMachineConfiguration
[pypi_batch]: https://pypi.python.org/pypi/azure-batch
[pypi_storage]: https://pypi.python.org/pypi/azure-storage

[pypi_install]: http://python-packaging-user-guide.readthedocs.io/en/latest/installing/
[storage_explorer]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/products/vs-2015-product-editions
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

[1]: ./media/batch-python-tutorial/batch_workflow_01_sm.png "Créer des conteneurs dans le stockage Azure"
[2]: ./media/batch-python-tutorial/batch_workflow_02_sm.png "Téléchargement d’application des tâches et des fichiers d’entrée (données) à des conteneurs"
[3]: ./media/batch-python-tutorial/batch_workflow_03_sm.png "Créer lot pool"
[4]: ./media/batch-python-tutorial/batch_workflow_04_sm.png "Créer des lots"
[5]: ./media/batch-python-tutorial/batch_workflow_05_sm.png "Ajouter des tâches à une tâche"
[6]: ./media/batch-python-tutorial/batch_workflow_06_sm.png "Tâches du moniteur"
[7]: ./media/batch-python-tutorial/batch_workflow_07_sm.png "Télécharger le résultat de la tâche à partir du stockage"
[8]: ./media/batch-python-tutorial/batch_workflow_sm.png "Flux de travail lot solution (diagramme complète)"
[9]: ./media/batch-python-tutorial/credentials_batch_sm.png "Informations d’identification du lot dans le portail"
[10]: ./media/batch-python-tutorial/credentials_storage_sm.png "Informations d’identification de stockage dans le portail"
[11]: ./media/batch-python-tutorial/batch_workflow_minimal_sm.png "Flux de travail de solution par lots (un minimum de tâches)"
