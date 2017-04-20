<properties
 pageTitle="Options de cluster Windows HPC Pack dans le cloud | Microsoft Azure"
 description="Découvrez les options avec Microsoft HPC Pack pour créer et gérer un Windows haute performance cluster (HPC) dans le cloud Azure"
 services="virtual-machines-windows,cloud-services,batch"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="09/26/2016"
 ms.author="danlep"/>

# <a name="options-with-hpc-pack-to-create-and-manage-a-windows-hpc-cluster-in-azure"></a>Options avec Pack HPC pour créer et gérer un cluster de Windows HPC dans Azure

[AZURE.INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Cet article se concentre sur les options pour créer des clusters HPC Pack pour exécuter les charges de travail de Windows. Il existe également des options pour la création de clusters pour exécuter les [charges de travail Linux HPC avec HPC Pack](virtual-machines-linux-hpcpack-cluster-options.md).


## <a name="run-an-hpc-pack-cluster-in-azure-vms"></a>Exécuter un cluster HPC Pack dans machines virtuelles Azure

### <a name="azure-templates"></a>Modèles Azure

* (Marketplace) [Cluster HPC Pack pour charges de travail de Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/)

* (Marketplace) [Cluster HPC Pack pour charges de travail Excel](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterexcelcn/)

* (Démarrage rapide) [Créer un cluster HPC](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)

* (Démarrage rapide) [Créer un cluster HPC avec l’image nœud cluster personnalisé](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-custom-image)

### <a name="azure-vm-images"></a>Images de machine virtuelle Azure

* [Pack HPC sur Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)

* [Nœud de calcul HPC Pack sur Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)

* [Nœud avec Excel sur Windows Server 2012 R2 de calcul HPC Pack](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)



### <a name="powershell-deployment-script"></a>Script de déploiement PowerShell

* [Créer un cluster HPC avec le script de déploiement HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)

### <a name="tutorials"></a>Didacticiels

* [Didacticiel : Prise en main avec un cluster HPC Pack dans Azure pour exécuter les charges de travail Excel et SOA](virtual-machines-windows-excel-cluster-hpcpack.md)



### <a name="manual-deployment-with-the-azure-portal"></a>Déploiement manuel grâce au portail Azure

* [Configurer le nœud principal d’un cluster HPC Pack dans une machine virtuelle Azure](virtual-machines-windows-hpcpack-cluster-headnode.md)

### <a name="cluster-management"></a>Gestion de cluster

* [Gérer les nœuds de calcul dans un cluster HPC Pack dans Azure](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md)

* [Agrandir et réduire les ressources Azure cluster dans un cluster HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md)

* [Soumettre des tâches à un cluster HPC Pack dans Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)

* [Gestion des projets dans HPC Pack](https://technet.microsoft.com/library/jj899585.aspx)


## <a name="add-worker-role-nodes-to-an-hpc-pack-cluster"></a>Ajouter des nœuds de rôle de collaborateur à un cluster HPC Pack


* [Burst sur des instances de travail Azure avec HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)

* [Didacticiel : Configurer un cluster hybride avec Pack HPC dans Azure](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

* [Ajouter des nœuds Azure « rupture » à un nœud de tête HPC Pack dans Azure](virtual-machines-windows-classic-hpcpack-cluster-node-burst.md)


## <a name="integrate-with-azure-batch"></a>Intégrer lot Azure 

* [Burst au lot Azure avec HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)

## <a name="create-rdma-clusters-for-mpi-workloads"></a>Créer des clusters RDMA pour charges de travail MPI

* [Configurer un cluster RDMA Windows avec HPC Pack à exécuter des applications MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md)
