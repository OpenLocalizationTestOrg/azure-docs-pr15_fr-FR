<properties
   pageTitle="Déployer des machines virtuelles de carte réseau à plusieurs à l’aide de PowerShell dans le modèle de déploiement classique | Microsoft Azure"
   description="Découvrez comment déployer des machines virtuelles de carte réseau à plusieurs à l’aide de PowerShell dans le modèle de déploiement classique"
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

#<a name="deploy-multi-nic-vms-classic-using-powershell"></a>Déployer à plusieurs machines virtuelles carte réseau (classique) à l’aide de PowerShell

[AZURE.INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Vous pouvez créer des machines virtuelles (machines virtuelles) dans Azure et joindre plusieurs interfaces réseau (cartes réseau) vers chacun de vos ordinateurs virtuels. Plusieurs cartes réseau activer la séparation des types de trafic sur cartes réseau. Par exemple, une carte réseau peut-être communiquer avec Internet, tandis que l’autre communique uniquement avec les ressources internes ne pas connectés à Internet. La possibilité pour distribuer le trafic réseau sur plusieurs cartes réseau est requise pour les nombreux appareils virtuel réseau, telles que la livraison des applications et des solutions d’optimisation WAN.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Découvrez comment [effectuer ces étapes en utilisant le modèle de gestionnaire de ressources](virtual-network-deploy-multinic-arm-ps.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Pour l’instant, vous ne peut pas contenir des machines virtuelles avec une seule carte réseau et des ordinateurs virtuels avec plusieurs cartes réseau dans le même service cloud. Par conséquent, vous devez mettre en œuvre les serveurs principaux dans un service cloud autre qu’et tous les autres composants dans le scénario. Les étapes suivantes utilisent un service cloud nommé *IaaSStory* pour les ressources principales et *Serveur principal IaaSStory* pour les serveurs principaux.

## <a name="prerequisites"></a>Conditions préalables

Avant de déployer les serveurs principaux, vous devez déployer le service cloud principale à toutes les ressources nécessaires pour ce scénario. Au minimum, vous devez créer un réseau virtuel avec un sous-réseau pour le serveur principal. Consultez [créer un réseau virtuel à l’aide de PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md) pour apprendre à déployer un réseau virtuel.

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>Déployer le serveur principal machines virtuelles

Les ordinateurs virtuels principal dépendent de la création des ressources ci-dessous.

- **Sous-réseau principal**. Les serveurs de base de données seront partie d’un sous-réseau séparé, pour répartir le trafic. Le script ci-dessous attend ce sous-réseau exister dans un vnet nommé *WTestVnet*.
- **Compte de stockage pour disques de données**. Pour améliorer les performances, les disques de données sur les serveurs de base de données utilisera lecteur (SSD) technologie, qui requiert un compte de stockage premium. Vérifiez que l’emplacement Azure vous déployez pour prendre en charge le stockage premium.
- **Définie la disponibilité**. Tous les serveurs de base de données sont ajoutés à une seule disponibilité défini, afin de garantir au moins un des ordinateurs virtuels est vers le haut et en cours d’exécution lors de la maintenance.

### <a name="step-1---start-your-script"></a>Étape 1 : votre script

Vous pouvez télécharger le script PowerShell complè utilisé [ici](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1). Suivez les étapes ci-dessous pour modifier le script pour l’utiliser dans votre environnement.

1. Modifiez les valeurs des variables ci-dessous en fonction de votre groupe de ressources existant déployé ci-dessus dans les [conditions préalables](#Prerequisites).

        $location              = "West US"
        $vnetName              = "WTestVNet"
        $backendSubnetName     = "BackEnd"

2. Modifiez les valeurs de variables suivantes, selon les valeurs que vous souhaitez utiliser pour votre déploiement de serveur principal.

        $backendCSName         = "IaaSStory-Backend"
        $prmStorageAccountName = "iaasstoryprmstorage"
        $avSetName             = "ASDB"
        $vmSize                = "Standard_DS3"
        $diskSize              = 127
        $vmNamePrefix          = "DB"
        $dataDiskSuffix        = "datadisk"
        $ipAddressPrefix       = "192.168.2."
        $numberOfVMs           = 2

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Étape 2 : créer les ressources nécessaires pour vos ordinateurs virtuels

Vous avez besoin créer un nouveau service cloud et un compte de stockage pour les disques de données pour tous les ordinateurs virtuels. Vous devez également spécifier une image et un compte d’administrateur local pour les ordinateurs virtuels. Pour créer ces ressources, exécutez les étapes suivantes.

1. Créer un nouveau service cloud.

        New-AzureService -ServiceName $backendCSName -Location $location

2. Créer un nouveau compte de stockage premium.

        New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
            -Location $location `
            -Type Premium_LRS

3. Définir le compte de stockage créé ci-dessus en tant que le compte en cours de stockage pour votre abonnement.

        $subscription = Get-AzureSubscription `
            | where {$_.IsCurrent -eq $true}  
        Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
            -CurrentStorageAccountName $prmStorageAccountName

4. Sélectionnez une image de la machine virtuelle.

        $image = Get-AzureVMImage `
            | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
            | sort PublishedDate -Descending `
            | select -ExpandProperty ImageName -First 1

5. Définir les informations d’identification de compte administrateur local.

        $cred = Get-Credential -Message "Enter username and password for local admin account"

### <a name="step-3---create-vms"></a>Étape 3 : créer des ordinateurs virtuels

Vous devez utiliser une boucle pour créer des ordinateurs virtuels autant que vous voulez créez les cartes réseau et les machines virtuelles nécessaires au sein de la boucle. Pour créer les cartes réseau et les ordinateurs virtuels, exécutez les étapes suivantes.

1. Démarrer un `for` boucle pour répéter les commandes permettant de créer une machine virtuelle et deux cartes réseau aussi souvent que nécessaire, en fonction de la valeur de la `$numberOfVMs` variable.

        for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){

2. Créer un `VMConfig` objet en spécifiant l’image, la taille et la disponibilité définie pour la machine virtuelle.

            $vmName = $vmNamePrefix + $suffixNumber
            $vmConfig = New-AzureVMConfig -Name $vmName `
                            -ImageName $image `
                            -InstanceSize $vmSize `
                            -AvailabilitySetName $avSetName  

3. Mise en service de la machine virtuelle comme une machine virtuelle Windows.

            Add-AzureProvisioningConfig -VM $vmConfig -Windows `
                -AdminUsername $cred.UserName `
                -Password $cred.Password

4. Définissez la valeur par défaut carte réseau et attribuez-lui une adresse IP statique.

            Set-AzureSubnet -SubnetNames $backendSubnetName -VM $vmConfig
            Set-AzureStaticVNetIP -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig

5. Ajouter une seconde carte réseau pour chaque machine virtuelle.

            Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
                -SubnetName $backendSubnetName `
                -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
                -VM $vmConfig

6. Créer des disques de données pour chaque machine virtuelle.

            $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
            Add-AzureDataDisk -CreateNew -VM $vmConfig `
                -DiskSizeInGB $diskSize `
                -DiskLabel $dataDisk1Name `
                -LUN 0       

            $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"   
            Add-AzureDataDisk -CreateNew -VM $vmConfig `
                -DiskSizeInGB $diskSize `
                -DiskLabel $dataDisk2Name `
                -LUN 1

7. Créez chaque machine virtuelle et mettre fin à la boucle.

            New-AzureVM -VM $vmConfig `
                -ServiceName $backendCSName `
                -Location $location `
                -VNetName $vnetName
        }

### <a name="step-4---run-the-script"></a>Étape 4 : exécuter le script

À présent que vous avez téléchargé et modifié le script selon vos besoins, exécutez un il script pour créer le serveur principal machines virtuelles de base de données avec plusieurs cartes réseau.

1. Enregistrez votre script et exécutez-le à partir de l’invite de commande **PowerShell** ou **PowerShell ISE**. Vous verrez la sortie initiale, comme illustré ci-dessous.

        OperationDescription    OperationId                          OperationStatus
        --------------------    -----------                          ---------------
        New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded      
        New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded      

        WARNING: No deployment found in service: 'IaaSStory-Backend'.

2. Renseignez les informations nécessaires dans l’invite d’informations d’identification, puis cliquez sur **OK**. La sortie ci-dessous s’affiche.

        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
