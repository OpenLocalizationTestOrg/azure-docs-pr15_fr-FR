<properties
 pageTitle="À propos des machines virtuelles cluster accrue avec Windows | Microsoft Azure"
 description="Obtenir des informations générales et considérations pour l’utilisation de la taille de cluster accrue H-série et A8, A9, A10 et A11 Azure machines virtuelles Windows et cloud services"
 services="virtual-machines-windows, cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="09/21/2016"
 ms.author="danlep"/>

# <a name="about-h-series-and-compute-intensive-a-series-vms"></a>À propos des machines virtuelles A série H-série et cluster accrue

Voici des informations générales et quelques considérations pour l’utilisation de la nouvelle série H Azure et les instances de A8, A9, A10 et A11 antérieures, également connu sous le nom des instances *cluster accrue* . Cet article se concentre sur l’utilisation de ces instances pour les machines virtuelles Windows. Cet article est également disponible pour les [Machines virtuelles Linux](virtual-machines-linux-a8-a9-a10-a11-specs.md).


[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>Accès au réseau RDMA

Vous pouvez créer des groupes d’instances RDMA compatibles avec Windows Server et déployer parmi les installations MPI pris en charge pour tirer parti du réseau RDMA Azure. Ce réseau faible latence, haut débit est réservé pour le trafic MPI uniquement.

* **Système d'exploitation**
    * **Machines virtuelles** - Windows Server 2012 R2, Windows Server 2012
    * **Les services en nuage** - Windows Server 2012 R2, Windows Server 2012, la famille de systèmes d’exploitation Windows Server 2008 R2 invité

* **MPI** - Microsoft MPI (MS-MPI) 2012 R2 ou version ultérieure, Bibliothèque MPI Intel 5.x

Pris en charge installations MPI utilisent l’interface Microsoft réseau directe pour communiquer entre des instances. Étapes de configuration des exemples et des options de déploiement, voir [configurer un cluster RDMA Windows avec Pack HPC pour exécuter des applications MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md) et [Utilisez les tâches de plusieurs instances pour exécuter des applications dans Azure lot passant Interface MPI (Message)](../batch/batch-mpi.md) .


>[AZURE.NOTE]Sur machines virtuelles cluster accrue RDMA compatibles avec, l’extension HpcVmDrivers doit être ajoutée aux ordinateurs virtuels pour installer les pilotes de périphériques de réseau de Windows qui sont nécessaires pour la connectivité RDMA. Dans la plupart des déploiements, l’extension HpcVmDrivers est ajoutée automatiquement. Si vous avez besoin ajouter l’extension vous-même, consultez [Gérer machine virtuelle extensions](virtual-machines-windows-classic-manage-extensions.md).

## <a name="considerations-for-hpc-pack-and-windows"></a>Considérations relatives aux HPC Pack et Windows

[Microsoft HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), cluster HPC gratuit de Microsoft et la solution de gestion de projet n’est pas nécessaire pour pouvoir utiliser les instances cluster accrue avec Windows Server. Cependant, il est une option pour créer un cluster de calcul dans Azure pour exécuter des applications MPI fonctionnant sous Windows et autres charges de travail HPC. HPC Pack 2012 R2 et les versions ultérieures incluent un environnement d’exécution pour MS-MPI qui utilisent le réseau Azure RDMA lorsque déployé sur machines virtuelles RDMA compatibles avec.

Pour plus d’informations et des listes de contrôle à utiliser les instances cluster accrue avec HPC Pack sur Windows Server, voir [configurer un cluster RDMA Windows avec Pack HPC pour exécuter des applications MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).




## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la disponibilité et tarifs des tailles de cluster accrue, voir [Machines virtuelles tarifs](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows) et les [prix des Services Cloud](https://azure.microsoft.com/pricing/details/cloud-services/).

* Pour les capacités de stockage et les détails d’un disque, voir [formats pour les machines virtuelles](virtual-machines-linux-sizes.md).

* Pour commencer déploiement et l’utilisation des instances cluster accrue avec Pack HPC sur Windows, voir [configurer un cluster RDMA Windows avec Pack HPC pour exécuter des applications MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).

* Pour plus d’informations sur l’utilisation des instances A8 et A9 pour exécuter des applications MPI avec Azure lot, voir [utiliser plusieurs instances tâches pour exécuter des applications en passant Interface MPI (Message) dans Azure lot](../batch/batch-mpi.md).
