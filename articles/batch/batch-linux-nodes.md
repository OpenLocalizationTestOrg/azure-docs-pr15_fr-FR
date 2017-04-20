<properties
    pageTitle="Nœuds Linux dans des pools d’Azure lot | Microsoft Azure"
    description="Découvrez comment traiter votre cluster en parallèle les charges de travail sur des pools de machines virtuelles de Linux dans Azure lot."
    services="batch"
    documentationCenter="python"
    authors="mmacy"
    manager="timlt"
    editor="" />

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="na"
    ms.date="09/08/2016"
    ms.author="marsma" />

# <a name="provision-linux-compute-nodes-in-azure-batch-pools"></a>Mise en service des nœuds de calcul Linux dans des pools d’Azure lot

Vous pouvez utiliser Azure lot pour exécuter cluster parallèle les charges de travail sur des machines virtuelles Linux et Windows. Cet article explique en détail comment créer des pools Linux des nœuds de calcul dans le service lot à l’aide à la fois le [Lot de Python] [ py_batch_package] et [Lot .NET] [ api_net] bibliothèques du client.

> [AZURE.NOTE] [Packages d’application](batch-application-packages.md) ne sont actuellement pas pris en charge nœuds de calcul Linux.

## <a name="virtual-machine-configuration"></a>Configuration de machine virtuelle

Lorsque vous créez une liste des nœuds de calcul dans le lot, vous disposez de deux options permettant de sélectionner la taille de nœud et le système d’exploitation : Configuration des Services Cloud et Configuration de Machine virtuelle.

**Configuration des Services cloud** fournit que Windows calculent nœuds *uniquement*. Tailles de nœud cluster disponibles sont répertoriées dans les [tailles des Services en nuage](../cloud-services/cloud-services-sizes-specs.md), et les systèmes d’exploitation disponibles sont répertoriées dans [les versions du système d’exploitation invité Azure et matrice de compatibilité SDK](../cloud-services/cloud-services-guestos-update-matrix.md). Lorsque vous créez un pool qui contient des nœuds Azure Cloud Services, vous devez spécifier uniquement la taille du nœud et sa « famille du système d’exploitation » qui figurent dans les articles mentionnés précédemment. Pour les nœuds de calcul des pools de Windows, les Services en nuage est généralement utilisée.

**Configuration de Machine virtuelle** fournit des images Linux et Windows pour les nœuds de calcul. Tailles de nœud cluster disponibles sont répertoriées dans les [tailles des machines virtuelles dans Azure](../virtual-machines/virtual-machines-linux-sizes.md) (Linux) et les [tailles des machines virtuelles dans Azure](../virtual-machines/virtual-machines-windows-sizes.md) (Windows). Lorsque vous créez un pool qui contient des nœuds de Configuration de Machine virtuelle, vous devez spécifier la taille des nœuds, la référence d’image machine virtuelle et l’agent de nœud lot référence (SKU) soit installé sur les nœuds.

### <a name="virtual-machine-image-reference"></a>Référence d’image machine virtuelle

Le service lot utilise [échelle machine virtuelle définit](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) pour fournir des nœuds de calcul Linux. Les images du système d’exploitation pour ces machines virtuelles sont fournis par le [Azure Marketplace][vm_marketplace]. Lorsque vous configurez une référence d’image machine virtuelle, vous spécifiez les propriétés d’une image de machine virtuelle Marketplace. Les propriétés suivantes sont requises lorsque vous créez une référence d’image machine virtuelle :

| **Propriétés de la référence image** | **Exemple** |
| ----------------- | ------------------------ |
| Publisher         | Canonique                |
| Offre             | UbuntuServer             |
| RÉFÉRENCE (SKU)               | 14.04.4-LTS              |
| Version           | dernière                   |

> [AZURE.TIP] Vous pouvez en savoir plus sur ces propriétés et comment afficher la liste d’images Marketplace dans [Naviguer et sélectionner les images machine virtuelle Linux dans Azure infrastructure du langage commun ou PowerShell](../virtual-machines/virtual-machines-linux-cli-ps-findimage.md). Notez que pas toutes les images de Marketplace sont actuellement compatibles avec lot. Pour plus d’informations, voir [agent nœud référence (SKU)](#node-agent-sku).

### <a name="node-agent-sku"></a>Agent de nœud référence (SKU)

L’agent de nœud lot est un programme qui s’exécute sur chaque nœud dans le pool et fournit l’interface de commande et de contrôle entre le nœud et le service lot. Il existe différentes mises en œuvre de l’agent de nœud, appelé références SKU, pour différents systèmes d’exploitation. Pour l’essentiel, lorsque vous créez une Configuration de Machine virtuelle, vous indiquez tout d’abord la référence d’image machine virtuelle et puis que vous spécifiez l’agent de nœud installer sur l’image. En règle générale, chaque agent nœud référence (SKU) est compatible avec plusieurs images de machine virtuelle. Voici quelques exemples de l’agent de nœud Références SKU :

* batch.Node.Ubuntu 14.04
* batch.Node.CentOS 7
* amd64 batch.Node.Windows

> [AZURE.IMPORTANT] Pas toutes les images des machines virtuelles qui sont disponibles sur le marché sont compatibles avec les agents nœud lot actuellement disponibles. Vous devez utiliser le SDK lot pour répertorier l’agent de nœud disponible références SKU et les images de machine virtuelle avec lesquels ils sont compatibles. Afficher les [images de liste de Machine virtuelle](#list-of-virtual-machine-images) plus loin dans cet article pour plus d’informations.

## <a name="create-a-linux-pool-batch-python"></a>Créer un pool Linux : lot Python

L’extrait de code suivant montre un exemple de l’utilisation de la [Bibliothèque de Client Microsoft Azure lot pour Python] [ py_batch_package] pour créer un pool de serveur Ubuntu nœuds de calcul. Documentation de référence pour le module Python lot soient retrouvée en [package azure.batch] [py_batch_docs] lors de la lecture les documents.

Cet extrait de code crée un [ImageReference] [ py_imagereference] explicitement et spécifie chacune de ses propriétés (publisher, offre, référence (SKU), version). Dans le code de production, cependant, il est recommandé que vous utilisez la [list_node_agent_skus] [ py_list_skus] méthode pour déterminer et sélectionnez dans les combinaisons disponibles de référence (SKU) d’image et nœud agent en cours d’exécution.

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_A1"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, base_url = batch_url)
client = batch.BatchServiceClient(config)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id = pool_id, vm_size = vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher = "Canonical",
    offer = "UbuntuServer",
    sku = "14.04.2-LTS",
    version = "latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = "batch.node.ubuntu 14.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Comme mentionné précédemment, nous vous recommandons au lieu de créer [ImageReference] [ py_imagereference] explicitement, vous utilisez la [list_node_agent_skus] [ py_list_skus] méthode pour sélectionner dynamiquement des combinaisons image agent/Marketplace nœud actuellement pris en charge. L’extrait de Python suivant montre l’utilisation de cette méthode.

```python
# Get the list of node agents from the Batch service
nodeagents = client.account.list_node_agent_skus()

# Obtain the desired node agent
ubuntu1404agent = next(agent for agent in nodeagents if "ubuntu 14.04" in agent.id)

# Pick the first image reference from the list of verified references
ir = ubuntu1404agent.verified_image_references[0]

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = ubuntu1404agent.id)
```

## <a name="create-a-linux-pool-batch-net"></a>Créer un pool Linux : lot .NET

L’extrait de code suivant montre un exemple d’utilisation du [Lot .NET] [ nuget_batch_net] bibliothèque client pour créer un pool de serveur Ubuntu nœuds de calcul. Vous pouvez trouver la [documentation de référence lot .NET] [ api_net] sur MSDN.

L’extrait de code suivant utilise la [PoolOperations][net_pool_ops]. [ListNodeAgentSkus] [net_list_skus] méthode pour sélectionner dans la liste des actuellement prise en charge Marketplace image et le nœud de référence (SKU) combinaisons d’agents. Cette technique consiste parce que la liste des combinaisons prises en charge peut-être changer de temps à autre. En règle générale, les combinaisons prises en charge sont ajoutées.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_A1";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image
// that we wish to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.SkuId.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. Note that there are one or more image
// references associated with this node agent SKU.
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: ubuntuAgentSku.Id);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicated: nodeCount);

// Commit the pool to the Batch service
pool.Commit();
```

Bien que l’extrait précédent utilise le [PoolOperations][net_pool_ops]. [ListNodeAgentSkus] [net_list_skus] méthode pour répertorier dynamiquement et sélectionner à partir de prise en charge image et nœud combinaisons de référence (SKU) de l’agent (recommandés), vous pouvez également configurer un [ImageReference] [ net_imagereference] explicitement :

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    skuId: "14.04.2-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>Liste des images de machine virtuelle

Le tableau suivant répertorie les images de machine virtuelle Marketplace qui sont compatibles avec les agents nœud lot disponibles lors de la dernière mise à jour de cet article. Il est important de noter que cette liste n’est pas définitive, car les images et agents nœud peuvent être ajoutés ou supprimés à tout moment. Nous vous recommandons de vos lot applications et services toujours utilisent [list_node_agent_skus] [ py_list_skus] (Python) et [ListNodeAgentSkus] [ net_list_skus] (lot .NET) pour déterminer et sélectionnez dans les références SKU actuellement disponibles.

> [AZURE.WARNING] La liste suivante peut modifier à tout moment. Toujours utiliser les méthodes **agent de nœud liste SKU** disponibles dans les API lot à la liste, puis sélectionnez à partir de la machine virtuelle compatible et agent nœud Références SKU lorsque vous exécutez le traitement par lots.

| **Publisher** | **Offre** | **Image de la référence (SKU)** | **Version** | **Agent de nœud ID de référence (SKU)** |
| ------- | ------- | ------- | ------- | ------- |
| Canonique | UbuntuServer | 14.04.0-LTS | dernière | batch.Node.Ubuntu 14.04 |
| Canonique | UbuntuServer | 14.04.1-LTS | dernière | batch.Node.Ubuntu 14.04 |
| Canonique | UbuntuServer | 14.04.2-LTS | dernière | batch.Node.Ubuntu 14.04 |
| Canonique | UbuntuServer | 14.04.3-LTS | dernière | batch.Node.Ubuntu 14.04 |
| Canonique | UbuntuServer | 14.04.4-LTS | dernière | batch.Node.Ubuntu 14.04 |
| Canonique | UbuntuServer | 14.04.5-LTS | dernière | batch.Node.Ubuntu 14.04 |
| Canonique | UbuntuServer | 16.04.0-LTS | dernière | batch.Node.Ubuntu 16.04 |
| Credativ | Debian | 8 | dernière | batch.Node.Debian 8 |
| OpenLogic | CentOS | 7.0 | dernière | batch.Node.CentOS 7 |
| OpenLogic | CentOS | 7.1 | dernière | batch.Node.CentOS 7 |
| OpenLogic | CentOS HPC | 7.1 | dernière | batch.Node.CentOS 7 |
| OpenLogic | CentOS | 7.2 | dernière | batch.Node.CentOS 7 |
| Oracle | Oracle Linux | 7.0 | dernière | batch.Node.CentOS 7 |
| SUSE | openSUSE | 13.2 | dernière | batch.Node.OpenSuSE 13.2 |
| SUSE | openSUSE Leap | 42.1 | dernière | batch.Node.OpenSuSE 42.1 |
| SUSE | SLES HPC | 12 | dernière | batch.Node.OpenSuSE 42.1 |
| SUSE | SLES | 12-SP1 | dernière | batch.Node.OpenSuSE 42.1 |
| annonces de Microsoft | standard-données-scientifique-machine virtuelle | standard-données-scientifique-machine virtuelle | dernière | amd64 batch.Node.Windows |
| annonces de Microsoft | Linux-données-scientifique-machine virtuelle | linuxdsvm | dernière | batch.Node.CentOS 7 |
| MicrosoftWindowsServer | Windows Server | 2008-R2-SP1 | dernière | amd64 batch.Node.Windows |
| MicrosoftWindowsServer | Windows Server | Centre de données 2012 | dernière | amd64 batch.Node.Windows |
| MicrosoftWindowsServer | Windows Server | 2012 R2-centre de données | dernière | amd64 batch.Node.Windows |
| MicrosoftWindowsServer | Windows Server | Serveur Windows-Aperçu technique | dernière | amd64 batch.Node.Windows |

## <a name="connect-to-linux-nodes"></a>Se connecter à des nœuds Linux

Lors du développement ou lors de la résolution des problèmes, vous serez peut-être amené à se connecter à des nœuds dans votre liste. À la différence des nœuds de calcul Windows, vous ne pouvez pas utiliser protocole RDP (Remote Desktop) pour vous connecter à des nœuds Linux. En revanche, le service lot permet accèsSSH sur chaque nœud de connexion à distance.

L’extrait de code Python suivant crée un utilisateur sur chaque nœud dans un pool, qui est requis pour la connexion à distance. Il imprime ensuite les informations de connexion SSH (secure shell) pour chaque nœud.

```python
import datetime
import getpass
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify your own account credentials
batch_account_name = ''
batch_account_key = ''
batch_account_url = ''

# Specify the ID of an existing pool containing Linux nodes
# currently in the 'idle' state
pool_id = ''

# Specify the username and prompt for a password
username = 'linuxuser'
password = getpass.getpass()

# Create a BatchClient
credentials = batchauth.SharedKeyCredentials(
    batch_account_name,
    batch_account_key
)
batch_client = batch.BatchServiceClient(
        credentials,
        base_url=batch_account_url
)

# Create the user that will be added to each node in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = \
    (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = batch_client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    batch_client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = batch_client.compute_node.get_remote_login_settings(pool_id,
                                                                node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

Voici un exemple de sortie pour le code précédent pour un pool contenant quatre nœuds Linux :

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Notez qu’au lieu d’un mot de passe, vous pouvez spécifier une clé publique SSH lorsque vous créez un utilisateur sur un nœud. Dans le Kit de développement Python, cela en utilisant le paramètre **ssh_public_key** sur [ComputeNodeUser][py_computenodeuser]. Dans .NET, cela à l’aide de la [ComputeNodeUser][net_computenodeuser]. [SshPublicKey] [net_ssh_key] propriété.

## <a name="pricing"></a>Tarifs

Lot Azure repose sur la technologie Azure Cloud Services et Machines virtuelles Azure. Le service lot elle-même est proposé sans frais, ce qui signifie que vous êtes chargé uniquement pour les ressources de calcul que vos solutions lot consomment. Lorsque vous choisissez la **Configuration des Services Cloud**, vous devrez payer en fonction du [prix des Services Cloud] [ cloud_services_pricing] structure. Lorsque vous choisissez **Configuration de Machine virtuelle**, vous devrez payer en fonction de la [Machines virtuelles tarifs] [ vm_pricing] structure.

## <a name="next-steps"></a>Étapes suivantes

### <a name="batch-python-tutorial"></a>Didacticiel de Python lot

Pour un didacticiel plus détaillé sur l’utilisation des commandes à l’aide de Python, consultez [prise en main du client Azure lot Python](batch-python-tutorial.md). Son en le Guide [des exemples de code] [ github_samples_pyclient] inclut une fonction d’assistance, `get_vm_config_for_distro`, qui montre une autre technique pour obtenir une configuration machine virtuelle.

### <a name="batch-python-code-samples"></a>Exemples de code Python lot

Consultez les autres [exemples de code Python] [ github_samples_py] dans les [exemples de lot azure] [ github_samples] référentiel GitHub pour plusieurs scripts que vous montrent comment effectuer des opérations de commandes courantes tels que pool de travail et la création d’une tâche. Le [fichier Lisez-moi] [ github_py_readme] qui accompagne Python exemples contient plus d’informations sur la façon d’installer les packages requis.

### <a name="batch-forum"></a>Forum de traitement par lots

Le [Forum de lot Azure] [ forum] sur MSDN est l’endroit idéal pour discuter des commandes et poser des questions sur le service. Lire des publications utiles « stickied », puis envoyez vos questions à mesure qu’ils se produisent lorsque vous établissez vos solutions lot.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_computenodeuser]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.aspx
[net_imagereference]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.imagereference.aspx
[net_list_skus]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listnodeagentskus.aspx
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.sshpublickey.aspx
[nuget_batch_net]: https://www.nuget.org/packages/Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_list_skus]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
