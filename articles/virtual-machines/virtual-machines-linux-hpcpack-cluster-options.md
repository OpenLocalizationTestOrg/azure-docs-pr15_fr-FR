<properties
 pageTitle="Options de cluster Linux HPC Pack dans le cloud | Microsoft Azure"
 description="Découvrez les options avec Microsoft HPC Pack pour créer et gérer un Linux haute performance computing cluster (HPC) dans le cloud Azure"
 services="virtual-machines-linux,cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="09/26/2016"
 ms.author="danlep"/>

# <a name="options-with-hpc-pack-to-create-and-manage-an-hpc-cluster-in-azure-for-linux-workloads"></a>Options avec Pack HPC pour créer et gérer un cluster HPC dans Azure pour charges de travail Linux

[AZURE.INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Cet article se concentre sur les options à utiliser HPC Pack pour exécuter les charges de travail Linux. Il existe également des options permettant d’exécuter [Windows HPC les charges de travail avec HPC Pack](virtual-machines-windows-hpcpack-cluster-options.md).

## <a name="run-an-hpc-pack-cluster-in-azure-vms"></a>Exécuter un cluster HPC Pack dans machines virtuelles Azure

### <a name="azure-templates"></a>Modèles Azure


* (Marketplace) [Cluster HPC Pack pour charges de travail Linux](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/)

* (Démarrage rapide) [Créer un cluster HPC avec Linux les nœuds de calcul](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn)


### <a name="powershell-deployment-script"></a>Script de déploiement PowerShell

* [Créer un cluster Linux HPC avec le script de déploiement HPC Pack IaaS](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md)

### <a name="tutorials"></a>Didacticiels

* [Didacticiel : Prise en main Linux cluster nœuds dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md)

* [Didacticiel : Exécuter NAMD avec Microsoft HPC Pack sous Linux nœuds dans Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)

* [Didacticiel : Exécuter OpenFOAM avec Microsoft HPC Pack sur un cluster Linux RDMA dans Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)

* [Didacticiel : Exécuter étoile-CCM + avec Microsoft HPC Pack sur un Linux RDMA cluster dans Azure](virtual-machines-linux-classic-hpcpack-cluster-starccm.md)

### <a name="cluster-management"></a>Gestion de cluster

* [Soumettre des tâches à un cluster HPC Pack dans Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)

* [Gestion des projets dans HPC Pack](https://technet.microsoft.com/library/jj899585.aspx)


## <a name="create-rdma-clusters-for-mpi-workloads"></a>Créer des clusters RDMA pour charges de travail MPI

* [Didacticiel : Exécuter OpenFOAM avec Microsoft HPC Pack sur un cluster Linux RDMA dans Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)

* [Configurer un cluster Linux RDMA à exécuter des applications MPI](virtual-machines-linux-classic-rdma-cluster.md)

