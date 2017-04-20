<properties
 pageTitle="À propos des machines virtuelles cluster accrue avec Linux | Microsoft Azure"
 description="Obtenir des informations générales et des remarques sur l’utilisation de la taille de cluster accrue H-série et A8, A9, A10 et A11 pour les machines virtuelles Linux"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="09/21/2016"
 ms.author="danlep"/>

# <a name="about-h-series-and-compute-intensive-a-series-vms"></a>À propos des machines virtuelles A série H-série et cluster accrue 

Voici des informations générales et quelques considérations pour l’utilisation de la nouvelle série H Azure et les tailles de A8, A9, A10 et A11 antérieures, également connu sous le nom des instances *cluster accrue* . Cet article se concentre sur l’utilisation de ces tailles pour les machines virtuelles Linux. Cet article est également disponible pour les [Machines virtuelles Windows](virtual-machines-windows-a8-a9-a10-a11-specs.md).




[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>Accès au réseau RDMA

Vous pouvez créer des groupes de RDMA compatibles avec Linux machines virtuelles qu’exécuter une des opérations suivantes pris en charge Linux HPC répartitions et une mise en œuvre MPI pris en charge pour tirer parti du réseau RDMA Azure. Étapes de configuration des exemples et des options de déploiement, voir [configurer un cluster Linux RDMA afin d’exécuter des applications MPI](virtual-machines-linux-classic-rdma-cluster.md) .

* **Répartitions** - vous devez déployer des machines virtuelles à partir d’images compatible avec RDMA SUSE Linux Enterprise Server (SLES) ou basée sur les OpenLogic CentOS HPC sur le marché Azure. Uniquement les images Marketplace suivantes prennent en charge les pilotes Linux RDMA nécessaires :

    * SLES 12 SP1 pour HPC, SLES 12 SP1 pour HPC (Premium)
    
    * SLES 12 pour HPC, SLES 12 pour HPC (Premium)
    
    * En fonction des centOS 7.1 HPC
    
    * En fonction des centOS 6.5 HPC
    
    >[AZURE.NOTE]Pour les machines virtuelles H-série, nous vous recommandons d’un 12 SLES SP1 d’une image HPC ou CentOS 7.1 HPC image de.
    >
    >Sur les images basées sur CentOS HPC, noyau mises à jour sont désactivées dans le fichier de configuration **yum** . C’est parce que la distribution des axes stratégiques Linux RDMA comme un package de t/min et mises à jour du pilote ne fonctionnent pas si le noyau est mis à jour.

* **MPI** - Bibliothèque MPI Intel 5.x

    En fonction de l’image Marketplace choisie, installation de gestion des licences, distinct, ou configuration d’Intel peut-être être nécessaires, comme suit : 
    
    * **SLES 12 SP1 d’une image HPC** - installer les packages MPI Intel distribués sur l’ordinateur virtuel en exécutant la commande suivante :
    
            sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm

    * **12 SLES d’une image HPC** : vous devez vous inscrire séparément pour télécharger et installer Intel MPI. Consultez le [guide d’installation Bibliothèque MPI Intel](https://software.intel.com/sites/default/files/managed/7c/2c/intelmpi-2017-installguide-linux.pdf).
    
    * **Images basées sur centOS HPC** - Intel MPI 5.1 est déjà installée.  

    Configuration système supplémentaire est nécessaire pour exécuter des tâches MPI sur machines virtuelles groupés. Par exemple, sur un cluster de machines virtuelles, vous devez établir une approbation entre les nœuds de calcul. Pour les paramètres par défaut, voir [configurer un cluster Linux RDMA afin d’exécuter des applications MPI](virtual-machines-linux-classic-rdma-cluster.md).


## <a name="considerations-for-hpc-pack-and-linux"></a>Considérations relatives aux HPC Pack et Linux

[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), solution de gestion de projet et cluster HPC gratuit de Microsoft fournit une option pour pouvoir utiliser les instances cluster accrue avec Linux. Les dernières versions de prise en charge HPC Pack 2012 R2 plusieurs versions de Linux à s’exécuter sur les nœuds de calcul déployées dans Azure machines virtuelles, gérés par un nœud de tête Windows Server. Linux RDMA compatibles avec les nœuds cluster MPI Intel en cours d’exécution, HPC Pack puisse planifier et exécuter des applications qui accèdent au réseau RDMA Linux MPI. Pour commencer, voir [prise en main Linux cluster nœuds dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

## <a name="network-topology-considerations"></a>Considérations relatives à la topologie de réseau

* Sous activé RDMA Linux machines virtuelles dans Azure, Eth1 est réservé pour le trafic réseau RDMA. Ne modifiez pas les paramètres Eth1 ou toutes les informations du fichier de configuration faisant référence à ce réseau. Eth0 est réservé pour le trafic réseau Azure normal.

* Dans Azure, IP sur InfiniBand (i) n’est pas pris en charge. Uniquement les RDMA sur i est pris en charge.

## <a name="rdma-driver-updates-for-sles-12"></a>Mises à jour des pilotes RDMA pour SLES 12

Après avoir créé une machine virtuelle basée sur une image SLES 12 HPC, vous devrez peut-être mettre à jour les pilotes RDMA sur les ordinateurs virtuels pour la connectivité réseau RDMA. 

>[AZURE.IMPORTANT]Cette étape est **requis** pour SLES 12 pour les déploiements HPC VM dans toutes les régions Azure. 
>Cette étape n’est pas nécessaire si vous déployez une 12 SLES SP1 pour HPC, en fonction des CentOS 7.1 HPC ou basée sur CentOS 6.5 HPC machine virtuelle. 

Avant de vous mettre à jour les pilotes, arrêtez tous les processus **zypper** ou tous les processus verrouillent les bases de données SUSE mis en pension sur l’ordinateur virtuel. Dans le cas contraire les pilotes ne peut-être pas mettre à jour correctement.  

Pour mettre à jour les pilotes Linux RDMA sur chaque ordinateur virtuel, exécutez un des jeux de commandes Azure infrastructure du langage commun suivants à partir de votre ordinateur client.

**SLES 12 pour HPC VM mis en service dans le modèle de déploiement classique**

```
azure config mode asm

azure vm extension set <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

**SLES 12 pour HPC VM mis en service dans le modèle de déploiement du Gestionnaire de ressources**

```
azure config mode arm

azure vm extension set <resource-group> <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

>[AZURE.NOTE]Cela peut prendre quelques instants pour installer les pilotes, et la commande renvoie sans sortie. Après la mise à jour, votre ordinateur virtuel redémarre et doit être prêt à être utilisé dans plusieurs minutes.

### <a name="sample-script-for-driver-updates"></a>Exemple de script les mises à jour du pilote

Si vous disposez d’un cluster de 12 SLES pour les machines virtuelles HPC, vous pouvez créer un script la mise à jour du pilote sur tous les nœuds dans votre cluster. Par exemple, le script suivant met à jour les pilotes dans un cluster 8 nœuds.

```

#!/bin/bash -x

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Plug in appropriate numbers in the for loop below.

for (( i=11; i<19; i++ )); do

# For VMs created in the classic deployment model, use the following command in your script.

azure vm extension set $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

# For VMs created in the Resource Manager deployment model, use the following command in your script.

# azure vm extension set <resource-group> $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

done

```


## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la disponibilité et tarifs des tailles de cluster accrue, voir [Machines virtuelles tarifs](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux).

* Pour les capacités de stockage et les détails d’un disque, voir [formats pour les machines virtuelles](virtual-machines-linux-sizes.md).

* Pour commencer déploiement et l’utilisation de tailles de cluster accrue avec RDMA sous Linux, voir [configurer un cluster Linux RDMA afin d’exécuter des applications MPI](virtual-machines-linux-classic-rdma-cluster.md).


