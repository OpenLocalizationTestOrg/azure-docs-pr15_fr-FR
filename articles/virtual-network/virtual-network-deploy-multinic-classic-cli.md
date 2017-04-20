<properties
   pageTitle="Déployer des machines virtuelles de carte réseau à plusieurs à l’aide de l’infrastructure du langage commun Azure dans le modèle de déploiement classique | Microsoft Azure"
   description="Découvrez comment déployer des machines virtuelles de carte réseau à plusieurs à l’aide de l’infrastructure du langage commun Azure dans le modèle de déploiement classique"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

#<a name="deploy-multi-nic-vms-classic-using-the-azure-cli"></a>Déployer à plusieurs carte réseau machines virtuelles (classique) à l’aide de l’infrastructure du langage commun Azure

[AZURE.INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Vous pouvez créer des machines virtuelles (machines virtuelles) dans Azure et joindre plusieurs interfaces réseau (cartes réseau) vers chacun de vos ordinateurs virtuels. Plusieurs cartes réseau activer la séparation des types de trafic sur cartes réseau. Par exemple, une carte réseau peut-être communiquer avec Internet, tandis que l’autre communique uniquement avec les ressources internes ne pas connectés à Internet. La possibilité pour distribuer le trafic réseau sur plusieurs cartes réseau est requise pour les nombreux appareils virtuel réseau, telles que la livraison des applications et des solutions d’optimisation WAN.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Découvrez comment [effectuer ces étapes en utilisant le modèle de gestionnaire de ressources](virtual-network-deploy-multinic-arm-cli.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Pour l’instant, vous ne peut pas contenir des machines virtuelles avec une seule carte réseau et des ordinateurs virtuels avec plusieurs cartes réseau dans le même service cloud. Par conséquent, vous devez mettre en œuvre les serveurs principaux dans un service cloud autre qu’et tous les autres composants dans le scénario. Les étapes suivantes utilisent un service cloud nommé *IaaSStory* pour les ressources principales et *Serveur principal IaaSStory* pour les serveurs principaux.

## <a name="prerequisites"></a>Conditions préalables

Avant de déployer les serveurs principaux, vous devez déployer le service cloud principale à toutes les ressources nécessaires pour ce scénario. Au minimum, vous devez créer un réseau virtuel avec un sous-réseau pour le serveur principal. Consultez [créer un réseau virtuel à l’aide de l’infrastructure du langage commun Azure](virtual-networks-create-vnet-classic-cli.md) pour apprendre à déployer un réseau virtuel.

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>Déployer le serveur principal machines virtuelles

Les ordinateurs virtuels principal dépendent de la création des ressources ci-dessous.

- **Compte de stockage pour disques de données**. Pour améliorer les performances, les disques de données sur les serveurs de base de données utilisera lecteur (SSD) technologie, qui requiert un compte de stockage premium. Vérifiez que l’emplacement Azure vous déployez pour prendre en charge le stockage premium.
- **Cartes réseau**. Chaque ordinateur virtuel aura deux cartes réseau, un pour l’accès de la base de données et l’autre pour la gestion.
- **Définie la disponibilité**. Tous les serveurs de base de données sont ajoutés à une seule disponibilité défini, afin de garantir au moins un des ordinateurs virtuels est vers le haut et en cours d’exécution lors de la maintenance.

### <a name="step-1---start-your-script"></a>Étape 1 : votre script

Vous pouvez télécharger le script bash complète utilisé [ici](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh). Suivez les étapes ci-dessous pour modifier le script pour l’utiliser dans votre environnement.

1. Modifiez les valeurs des variables ci-dessous en fonction de votre groupe de ressources existant déployé ci-dessus dans les [conditions préalables](#Prerequisites).

        location="useast2"
        vnetName="WTestVNet"
        backendSubnetName="BackEnd"

2. Modifiez les valeurs de variables suivantes, selon les valeurs que vous souhaitez utiliser pour votre déploiement de serveur principal.

        backendCSName="IaaSStory-Backend"
        prmStorageAccountName="iaasstoryprmstorage"
        image="0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1"
        avSetName="ASDB"
        vmSize="Standard_DS3"
        diskSize=127
        vmNamePrefix="DB"
        osDiskName="osdiskdb"
        dataDiskPrefix="db"
        dataDiskName="datadisk"
        ipAddressPrefix="192.168.2."
        username='adminuser'
        password='adminP@ssw0rd'
        numberOfVMs=2

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Étape 2 : créer les ressources nécessaires pour vos ordinateurs virtuels

1. Créer un nouveau service cloud pour tous les ordinateurs virtuels principal. Notez l’utilisation de la `$backendCSName` variable du nom du groupe de ressources, et `$location` Azure à votre région.

        azure service create --serviceName $backendCSName \
            --location $location

2. Créer un compte de stockage premium pour les disques du système d’exploitation et les données devant être utilisé par le vôtre machines virtuelles.

        azure storage account create $prmStorageAccountName \
            --location $location \
            --type PLRS

### <a name="step-3---create-vms-with-multiple-nics"></a>Étape 3 : créer des ordinateurs virtuels avec plusieurs cartes réseau

1. Démarrer une boucle pour créer plusieurs ordinateurs virtuels, en fonction de la `numberOfVMs` variables.

        for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
        do

2. Pour chaque machine virtuelle, spécifiez le nom et l’adresse IP de chacune des deux cartes réseau.

            nic1Name=$vmNamePrefix$suffixNumber-DA
            x=$((suffixNumber+3))
            ipAddress1=$ipAddressPrefix$x

            nic2Name=$vmNamePrefix$suffixNumber-RA
            x=$((suffixNumber+53))
            ipAddress2=$ipAddressPrefix$x

4. Créer la machine virtuelle. Notez l’utilisation de la `--nic-config` paramètre, contenant une liste de toutes les cartes réseau avec nom, sous-réseau et l’adresse IP.

            azure vm create $backendCSName $image $username $password \
                --connect $backendCSName \
                --vm-name $vmNamePrefix$suffixNumber \
                --vm-size $vmSize \
                --availability-set $avSetName \
                --blob-url $prmStorageAccountName.blob.core.windows.net/vhds/$osDiskName$suffixNumber.vhd \
                --virtual-network-name $vnetName \
                --subnet-names $backendSubnetName \
                --nic-config $nic1Name:$backendSubnetName:$ipAddress1::,$nic2Name:$backendSubnetName:$ipAddress2::

5. Pour chaque machine virtuelle, créez deux disques de données.

            azure vm disk attach-new $vmNamePrefix$suffixNumber \
                $diskSize \
                vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd

            azure vm disk attach-new $vmNamePrefix$suffixNumber \
                $diskSize \
                vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
        done

### <a name="step-4---run-the-script"></a>Étape 4 : exécuter le script

À présent que vous avez téléchargé et modifié le script selon vos besoins, exécutez le script pour créer le serveur principal machines virtuelles de base de données avec plusieurs cartes réseau.

1. Enregistrez votre script et exécutez-le à partir de votre terminal **Bash** . Vous verrez la sortie initiale, comme illustré ci-dessous.

        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name IaaSStory-Backend
        info:    service create command OK
        info:    Executing command storage account create
        info:    Creating storage account
        info:    storage account create command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM

2. Après quelques minutes, l’exécution s’arrête et vous verrez le reste de la sortie comme illustré ci-dessous.

        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM
        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
