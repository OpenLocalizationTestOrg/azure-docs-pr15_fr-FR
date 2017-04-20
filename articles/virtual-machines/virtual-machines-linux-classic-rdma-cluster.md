<properties
 pageTitle="Exécution des applications MPI du cluster Linux RDMA | Microsoft Azure"
 description="Créer un cluster Linux de taille H16r, H16mr, A8 ou A9 machines virtuelles à utiliser le réseau RDMA Azure pour exécuter des applications MPI"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="09/21/2016"
 ms.author="danlep"/>

# <a name="set-up-a-linux-rdma-cluster-to-run-mpi-applications"></a>Configurer un cluster Linux RDMA à exécuter des applications MPI


Découvrez comment configurer un cluster Linux RDMA dans Azure avec [machines virtuelles A série H-série ou cluster accrue](virtual-machines-linux-a8-a9-a10-a11-specs.md) à exécuter en parallèle Message transmission des applications MPI (Interface). Cet article fournit les étapes nécessaires pour préparer une image Linux HPC à exécuter MPI Intel sur un cluster. Ensuite, vous déployez un cluster de machines virtuelles à l’aide de cette image et l’autre des tailles RDMA compatibles avec Azure machine virtuelle (actuellement H16r, H16mr, A8 ou A9). Le cluster permet d’exécuter des applications MPI communiquent efficacement via une latence faible, haut débit réseau basé sur l’accès à distance direct à la mémoire technologie (RDMA).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## <a name="cluster-deployment-options"></a>Options de déploiement de cluster

Voici des méthodes que vous pouvez utiliser pour créer un cluster Linux RDMA avec ou sans un planificateur de tâches.


* **Azure infrastructure du langage commun scripts** - comme indiqué plus loin dans cet article, permet de l' [Interface de ligne Azure](../xplat-cli-install.md) de script le déploiement d’un cluster de machines virtuelles RDMA compatibles avec. L’infrastructure du langage commun en mode de gestion des services crée en série les nœuds de cluster dans le modèle de déploiement classique, afin que le déploiement de nombreux nœuds de calcul peut prendre plusieurs minutes. Pour activer la connexion de réseau RDMA lorsque vous utilisez le modèle de déploiement classique, déployer les ordinateurs virtuels dans le même service cloud.

* **Gestionnaire de Ressources azure modèles** - vous pouvez également utiliser le modèle de déploiement du Gestionnaire de ressources pour déployer un cluster de machines virtuelles RDMA compatibles avec qui se connecte au réseau RDMA. Vous pouvez [créer votre propre modèle](../resource-group-authoring-templates.md), ou vérifier les [modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/) des modèles fournis par Microsoft ou de la Communauté pour déployer la solution souhaitée. Gestionnaire de ressources modèles peut fournir un moyen rapide et fiable pour déployer un cluster Linux. Pour activer la connexion de réseau RDMA lorsque vous utilisez le modèle de déploiement du Gestionnaire de ressources, déployer les ordinateurs virtuels dans le même jeu de disponibilité.

* **HPC Pack** - créer un cluster Microsoft HPC Pack dans Azure et ajoutez RDMA compatibles avec nœuds de calcul qui s’exécutent une distribution Linux pris en charge pour accéder au réseau RDMA. Voir [prise en main Linux cluster nœuds dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

## <a name="sample-deployment-steps-in-classic-model"></a>Étapes de déploiement exemple de modèle classique

Les étapes suivantes montrent comment utiliser l’infrastructure du langage commun Azure pour déployer un ordinateur SUSE Linux Enterprise Server (SLES) 12 SP1 HPC virtuel depuis le Azure Marketplace, personnaliser et créer une image de machine virtuelle personnalisée. Utilisez ensuite l’image pour le déploiement d’un cluster de machines virtuelles RDMA compatibles avec un script. 

>[AZURE.TIP]Utiliser une procédure similaire pour déployer un cluster de machines virtuelles RDMA compatibles avec basée sur d’autres images HPC pris en charge dans les Azure Marketplace. Certaines étapes peuvent différer légèrement, comme indiqué. Par exemple, Intel MPI est inclus et configuré dans uniquement certaines de ces images. Et si vous déployez un ordinateur SLES 12 HPC virtuel au lieu d’un ordinateur de HPC virtuel SLES 12 SP1, vous devez mettre à jour les pilotes RDMA. Pour plus d’informations, voir [à propos du A8, A9, A10 et A11 instances cluster accrue](virtual-machines-linux-a8-a9-a10-a11-specs.md#rdma-driver-updates-for-sles-12).


### <a name="prerequisites"></a>Conditions préalables

*   **Ordinateur client** - vous besoin d’un Mac, Linux ou client fonctionnant sous Windows ordinateur pour communiquer avec Azure. Cette procédure suppose que vous utilisez un client Linux.

*   **Abonnement azure** - si vous n’avez pas un abonnement, vous pouvez créer un [compte de libérer](https://azure.microsoft.com/free/) en quelques minutes. Pour clusters de grande taille, envisagez un abonnement paiement ou autres options d’achat. 

*   **Disponibilité de taille de la mémoire virtuelle** - actuellement les tailles instance suivantes sont RDMA capable : H16r, H16mr, A8 et A9. Vérifier la disponibilité dans les régions Azure [produits disponibles par région](https://azure.microsoft.com/regions/services/) . 

*   **Quota de cœurs** - vous devrez peut-être augmenter le quota de cœurs pour déployer un cluster de calcul accrue machines virtuelles. Par exemple, vous devez au moins 128 cœurs si vous voulez déployer 8 machines virtuelles A9 comme indiqué dans cet article. Votre abonnement peut également limiter le nombre de cœurs que vous pouvez déployer dans certaines familles de taille de la mémoire virtuelle, y compris la série H. Pour demander un quota augmenter, [Ouvrez une demande de support client en ligne](../azure-supportability/how-to-create-azure-support-request.md) sans frais. 

*   **Azure infrastructure du langage commun** - [installer](../xplat-cli-install.md) l’infrastructure du langage commun Azure et [se connecter à votre abonnement Azure](../xplat-cli-connect.md) à partir de l’ordinateur client.


### <a name="step-1-provision-a-sles-12-sp1-hpc-vm"></a>Étape 1. Mise en service un ordinateur SLES SP1 12 HPC virtuel

Après la connexion à Azure avec l’infrastructure du langage commun Azure, exécutez `azure config list` pour confirmer que la sortie indique le mode de gestion des services. Si elle n’est pas le cas, définissez le mode en exécutant cette commande :


    azure config mode asm


Tapez les informations suivantes pour répertorier tous les abonnements que vous êtes autorisé à utiliser :


    azure account list

L’abonnement actif en cours est identifié avec `Current` défini sur `true`. Si cet abonnement n’est pas celle que vous voulez utiliser pour créer le cluster, définissez l’Id de l’abonnement approprié en tant que l’abonnement actif :

    azure account set <subscription-Id>

Pour afficher les images SLES 12 SP1 HPC accessibles dans Azure, exécutez une commande semblable au suivant, en supposant que votre de prise en charge de l’environnement shell **grep**:


    azure vm image list | grep "suse.*hpc"

Mise en service maintenant une machine virtuelle RDMA compatible avec une image SLES 12 SP1 HPC en exécutant une commande semblable à ce qui suit :

    azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824

où

* La taille (A9 dans cet exemple) est un des formats de machine virtuelle RDMA compatibles avec.

* Le numéro de port SSH externes (22 dans cet exemple, ce qui correspond à la valeur par défaut SSH) est n’importe quel numéro de port valide. Le numéro de port SSH interne est défini sur 22.

* Un service en nuage est créé dans la zone Azure spécifiée par l’emplacement. Spécifier un emplacement dans lequel la taille de la mémoire virtuelle choisie est disponible.

* Le nom de l’image SLES 12 SP1 actuellement peut être `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824` ou `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-priority-v20160824` prise en charge de la priorité SUSE (frais supplémentaires s’appliquent).

### <a name="step-2-customize-the-vm"></a>Étape 2. Personnaliser la machine virtuelle

Une fois que la machine virtuelle termine la mise en service, SSH de la machine virtuelle à l’aide des adresse IP externe de l’ordinateur (ou nom DNS) et le port externe numéro que vous configuré et personnalisez-le. Détails de connexion, voir [comment se connecter à un Linux en cours d’exécution Machine virtuelle](virtual-machines-linux-mac-create-ssh-keys.md). Exécution de commandes en tant que l’utilisateur que vous avez configuré sur la machine virtuelle, à moins d’avoir accès à la racine est nécessaire pour terminer une étape.

>[AZURE.IMPORTANT]Microsoft Azure ne fournit pas accès à la racine aux machines virtuelles Linux. Pour obtenir un accès administratif lorsque connecté en tant qu’utilisateur de la machine virtuelle, exécuter à l’aide des commandes `sudo`.

* **Mises à jour** - mises à jour de l’installation à l’aide de **zypper**. Vous souhaiterez également installer utilitaires NFS. 

    >[AZURE.IMPORTANT]Sur un ordinateur SLES 12 SP1 HPC virtuel, nous vous recommandons de que vous ne s’appliquent pas mises à jour du noyau, qui peuvent entraîner des problèmes avec la Linux RDMA pilotes.

* **Intel MPI** - terminer l’installation d’Intel sur l’ordinateur SLES 12 SP1 HPC virtuel en exécutant la commande suivante :

        sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm

* **Verrouillage de mémoire** - codes pour MPI pour verrouiller la mémoire disponible pour RDMA, ajouter ou modifier les paramètres suivants dans le fichier /etc/security/limits.conf. (Vous devez accès à la racine pour modifier ce fichier). 

    ```
    <User or group name> hard    memlock <memory required for your application in KB>

    <User or group name> soft    memlock <memory required for your application in KB>
    ```

    >[AZURE.NOTE]Pour effectuer un test, vous pouvez également définir memlock illimité. Par exemple : `<User or group name>    hard    memlock unlimited`. Pour plus d’informations, voir [Méthodes de meilleures connus de taille de la mémoire le paramètre verrouillé](https://software.intel.com/en-us/blogs/2014/12/16/best-known-methods-for-setting-locked-memory-size).

* **Touches SSH pour les machines virtuelles SLES** - générer SSH clés pour établir l’approbation pour votre compte d’utilisateur parmi les nœuds de calcul dans le cluster SLES lors de l’exécution des travaux MPI. (Si vous avez déployé un VM HPC basée sur CentOS, ne procédez comme suit. Reportez-vous aux instructions plus loin dans l’article pour configurer la gestion de la confidentialité passwordless SSH parmi les nœuds de cluster après avoir capturer l’image et déploiement du cluster.) 

    Exécutez la commande suivante pour créer des clés SSH. Lorsque vous y êtes invité pour la saisie, appuyez sur ENTRÉE pour générer les clés dans l’emplacement par défaut sans définir un mot de passe.

        ssh-keygen

    Ajouter la clé publique au fichier authorized_keys pour des clés publiques connus.

        cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

    Dans le répertoire ~/.ssh, modifier ou créer le fichier « configuration ». Entrez la plage d’adresses IP du réseau privé que vous prévoyez d’utiliser dans Azure (10.32.0.0/16 dans cet exemple) :

        host 10.32.0.*
        StrictHostKeyChecking no

    Vous pouvez également répertorier l’adresse IP de réseau privé de chaque machine virtuelle dans votre cluster comme suit :

    ```
    host 10.32.0.1
     StrictHostKeyChecking no
    host 10.32.0.2
     StrictHostKeyChecking no
    host 10.32.0.3
     StrictHostKeyChecking no
    ```

    >[AZURE.NOTE]Configuration `StrictHostKeyChecking no` pouvez créer un risque de sécurité lorsqu’une adresse IP spécifique ou une plage n’est pas spécifié.

* **Applications** - installer des applications que vous avez besoin sur cet ordinateur virtuel ou effectuez d’autres personnalisations avant l’acquisition de l’image.

### <a name="step-3-capture-the-image"></a>Étape 3. Capture de l’image

Pour capturer l’image, tout d’abord exécuter la commande suivante dans la VM Linux. Cette commande arrête la machine virtuelle, mais conserve les comptes d’utilisateur et les clés SSH que vous avez configurées.

```
sudo waagent -deprovision
```

Ensuite, à partir de votre ordinateur client, exécutez les commandes Azure infrastructure du langage commun suivantes pour capturer l’image. Pour plus d’informations, voir [Comment faire pour capturer une machine virtuelle Linux classique en tant qu’image](virtual-machines-linux-classic-capture-image.md) .  

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

Après avoir exécuté ces commandes, l’image de la machine virtuelle est capturé pour votre utilisation et la machine virtuelle est supprimée. Vous avez maintenant votre image personnalisée prêt à déployer un cluster.

### <a name="step-4-deploy-a-cluster-with-the-image"></a>Étape 4. Déployer un cluster avec l’image

Modifier le script Bash suivant avec les valeurs appropriées pour votre environnement, puis exécutez-le à partir de votre ordinateur client. Comme Azure déploie les ordinateurs virtuels en série dans le modèle de déploiement classique, il faut quelques minutes pour déployer les ordinateurs 8 virtuels les A9 suggérées dans ce script.

```
#!/bin/bash -x
# Script to create a compute cluster without a scheduler in a VNet in Azure
# Create a custom private network in Azure
# Replace 10.32.0.0 with your virtual network address space
# Replace <network-name> with your network identifier
# Replace "West US" with an Azure region where the VM size is available
# See Azure Pricing pages for prices and availability of compute-intensive VMs

azure network vnet create -l "West US" -e 10.32.0.0 -i 16 <network-name>

# Create a cloud service. All the compute-intensive instances need to be in the same cloud service for Linux RDMA to work across InfiniBand.
# Note: The current maximum number of VMs in a cloud service is 50. If you need to provision more than 50 VMs in the same cloud service in your cluster, contact Azure Support.

azure service create <cloud-service-name> --location "West US" –s <subscription-ID>

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Define a prefix for external port numbers. If you want to turn off external ports and use only internal ports to communicate between compute nodes via port 22, don’t use this option. Since port numbers up to 10000 are reserved, use numbers after 10000. Leave external port on for rank 0 and head node.

portnumber=101

# In this cluster there will be 8 size A9 nodes, named cluster11 to cluster18. Specify your captured image in <image-name>. Specify the username and password you used when creating the SSH keys.

for (( i=11; i<19; i++ )); do
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i -w <network-name> -b Subnet-1 <image-name>
done

# Save this script with a name like makecluster.sh and run it in your shell environment to provision your cluster
```

## <a name="considerations-for-a-centos-hpc-cluster"></a>Considérations sur un cluster HPC CentOS

Si vous voulez configurer un cluster basé sur l’une des images basées sur CentOS HPC sur le marché Azure au lieu de 12 SLES pour HPC, suivez les étapes générales dans la section précédente. Notez les différences suivantes lorsque vous mise en service et configurez la machine virtuelle :

1. Intel MPI est déjà installée sur un ordinateur virtuel sa mise en service à partir d’une image basée sur CentOS HPC. 

2. Verrouiller les paramètres de mémoire sont déjà ajoutés dans le fichier /etc/security/limits.conf de l’ordinateur.

2. Ne pas générer clés SSH sur l’ordinateur virtuel vous mise en service pour la capture. En revanche, nous vous recommandons de la configuration de l’authentification basée sur l’utilisateur une fois que vous déployez le cluser. Consultez la section suivante.  

### <a name="set-up-passwordless-ssh-trust-on-the-cluster"></a>Configurer la gestion de la confidentialité passwordless SSH sur le cluster

Sur un cluster HPC basée sur CentOS, il existe deux méthodes pour établir une approbation entre les nœuds de calcul : authentification basée sur l’hôte et l’authentification basée sur l’utilisateur. L’authentification basée sur l’hôte est en dehors de l’étendue de cet article et doit généralement être effectuée via un script extension pendant le déploiement. Authentification basée sur l’utilisateur est pratique pour établir une approbation après le déploiement et nécessite la génération et le partage de clés SSH parmi les nœuds de calcul dans le cluster. Cette méthode est connue sous passwordless connexion SSH et est nécessaire lors de l’exécution des travaux MPI. 

Un exemple de script contribué à partir de la Communauté est disponible sur [GitHub](https://github.com/tanewill/utils/blob/master/user_authentication.sh) pour activer l’authentification utilisateur simple sur un cluster HPC basée sur CentOS. Télécharger et utiliser ce script en suivant ces étapes. Vous pouvez également modifier ce script ou utiliser une autre méthode pour établir une authentification SSH passwordless entre les nœuds de cluster de calcul.

    wget https://raw.githubusercontent.com/tanewill/utils/master/ user_authentication.sh
    
Pour exécuter le script, vous devez connaître le préfixe pour vos adresses IP sous-réseau. Obtenir le préfixe en exécutant la commande suivante sur l’un des nœuds du cluster. Votre sortie doit ressembler à 10.1.3.5, et le préfixe est la 10.1.3 partie.

    ifconfig eth0 | grep -w inet | awk '{print $2}'

Maintenant exécuter le script à l’aide de trois paramètres : le nom d’utilisateur courantes sur les nœuds de calcul, le mot de passe courantes pour cet utilisateur sur les nœuds de calcul et le préfixe de sous-réseau qui a été retourné à partir de la commande précédente.


    ./user_authentication.sh <myusername> <mypassword> 10.1.3

Ce script effectue les opérations suivantes :

* Crée un répertoire sur le nœud hôte nommé .ssh, qui est requis pour la connexion passwordless. 
* Crée un fichier de configuration dans le répertoire .ssh qui indique passwordless se connecter à autoriser la connexion à partir de n’importe quel nœud dans le cluster. 
* Crée des fichiers contenant les noms des nœuds et les adresses IP de nœud pour tous les nœuds du cluster. Ces fichiers sont conservés après que le script est exécuté pour référence future. 
* Crée une paire de clés privée et publique pour chaque nœud de cluster, y compris le nœud hôte et crée des entrées dans le fichier authorized_keys.

>[AZURE.WARNING]Exécuter ce script peut créer un risque de sécurité. Vérifiez que les informations de clé publique dans ~/.ssh ne sont pas distribuées.


## <a name="configure-intel-mpi"></a>Configurer Intel MPI

Pour exécuter des applications MPI sur Azure Linux RDMA, vous devez configurer certaines variables d’environnement spécifiques à MPI Intel. Voici un exemple de script Bash pour configurer les variables nécessaires à l’exécution d’une application. Modifier le chemin d’accès à mpivars.sh selon les besoins de votre installation d’Intel.

```
#!/bin/bash -x

# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster 

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh

export I_MPI_FABRICS=shm:dapl

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB
# Setting the variable to shm:dapl gives best performance for some applications
# If your application doesn’t take advantage of shared memory and MPI together, then set only dapl

export I_MPI_DAPL_PROVIDER=ofa-v2-ib0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

export I_MPI_DYNAMIC_CONNECTION=0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

# Command line to run the job

mpirun -n <number-of-cores> -ppn <core-per-node> -hostfile <hostfilename>  /path <path to the application exe> <arguments specific to the application>

#end
```

Le format du fichier hôte est comme suit. Ajouter une ligne pour chaque nœud de votre cluster. Spécifier les adresses IP privées à partir de la VNet défini précédemment, pas les noms DNS. Par exemple, pour les deux hôtes avec des adresses IP 10.32.0.1 et 10.32.0.2 le fichier contient les éléments suivants :

```
10.32.0.1:16
10.32.0.2:16
```

## <a name="run-mpi-on-a-basic-two-node-cluster"></a>Exécuter MPI sur un cluster de deux nœuds simple

Si vous n’avez pas déjà fait, configurez d’abord l’environnement pour MPI Intel. 

```
# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster 

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh
```

### <a name="run-a-simple-mpi-command"></a>Exécuter une commande MPI simple

Exécuter une commande MPI simple sur l’un des nœuds de calcul pour afficher que MPI est installé correctement et communiquer au moins que deux nœuds de calcul. La commande **mpirun** suivante s’exécute la commande **hostname** sur deux nœuds.

```
mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
Votre sortie doit répertorier les noms de tous les nœuds que vous avez passées comme entrée pour `-hosts`. Par exemple, une commande **mpirun** avec deux nœuds renvoie sortie semblable à ce qui suit :

```
cluster11
cluster12
```

### <a name="run-an-mpi-benchmark"></a>Exécuter un test d’évaluation MPI

La commande suivante MPI Intel s’exécute un test d’évaluation pingpong pour vérifier la configuration du cluster et la connexion au réseau RDMA.

```
mpirun -hosts <host1>,<host2> -ppn 1 -n 2 -env I_MPI_FABRICS=dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 IMB-MPI1 pingpong
```

Sur un cluster opérationnel avec deux nœuds, vous devriez voir résultat semblable au suivant. Sur le réseau Azure RDMA, ils doivent s’attendre latence à ou en dessous 3 microsecondes message tailles jusqu'à 512 octets.

```
#------------------------------------------------------------
#    Intel (R) MPI Benchmarks 4.0 Update 1, MPI-1 part
#------------------------------------------------------------
# Date                  : Fri Jul 17 23:16:46 2015
# Machine               : x86_64
# System                : Linux
# Release               : 3.12.39-44-default
# Version               : #5 SMP Thu Jun 25 22:45:24 UTC 2015
# MPI Version           : 3.0
# MPI Thread Environment:
# New default behavior from Version 3.2 on:
# the number of iterations per message size is cut down
# dynamically when a certain run time (per message size sample)
# is expected to be exceeded. Time limit is defined by variable
# "SECS_PER_SAMPLE" (=> IMB_settings.h)
# or through the flag => -time

# Calling sequence was:
# /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
# Minimum message length in bytes:   0
# Maximum message length in bytes:   4194304
#
# MPI_Datatype                   :   MPI_BYTE
# MPI_Datatype for reductions    :   MPI_FLOAT
# MPI_Op                         :   MPI_SUM
#
#
# List of Benchmarks to run:
# PingPong
#---------------------------------------------------
# Benchmarking PingPong
# #processes = 2
#---------------------------------------------------
       #bytes #repetitions      t[usec]   Mbytes/sec
            0         1000         2.23         0.00
            1         1000         2.26         0.42
            2         1000         2.26         0.85
            4         1000         2.26         1.69
            8         1000         2.26         3.38
           16         1000         2.36         6.45
           32         1000         2.57        11.89
           64         1000         2.36        25.81
          128         1000         2.64        46.19
          256         1000         2.73        89.30
          512         1000         3.09       157.99
         1024         1000         3.60       271.53
         2048         1000         4.46       437.57
         4096         1000         6.11       639.23
         8192         1000         7.49      1043.47
        16384         1000         9.76      1600.76
        32768         1000        14.98      2085.77
        65536          640        25.99      2405.08
       131072          320        50.68      2466.64
       262144          160        80.62      3101.01
       524288           80       145.86      3427.91
      1048576           40       279.06      3583.42
      2097152           20       543.37      3680.71
      4194304           10      1082.94      3693.63

# All processes entering MPI_Finalize

```



## <a name="next-steps"></a>Étapes suivantes

* Essayez de déploiement et l’exécution des applications de votre Linux MPI sur votre cluster Linux.

* Consultez la [documentation de la bibliothèque de MPI Intel](https://software.intel.com/en-us/articles/intel-mpi-library-documentation/) pour obtenir des instructions sur MPI Intel.

* Essayez un [modèle de démarrage rapide](https://github.com/Azure/azure-quickstart-templates/tree/master/intel-lustre-clients-on-centos) pour créer un cluster Intel Lustre en utilisant une image basée sur CentOS HPC. Pour plus d’informations, consultez le [billet de blog](https://blogs.msdn.microsoft.com/arsen/2015/10/29/deploying-intel-cloud-edition-for-lustre-on-microsoft-azure/).
