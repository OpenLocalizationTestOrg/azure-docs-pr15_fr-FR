<properties
    pageTitle="Créer une échelle de Machine virtuelle définie à l’aide de PowerShell | Microsoft Azure"
    description="Créer une échelle de Machine virtuelle définie à l’aide de PowerShell"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="davidmu"/>

# <a name="create-a-windows-virtual-machine-scale-set-using-azure-powershell"></a>Créer une échelle de machine virtuelle Windows à l’aide de PowerShell Azure

Ces étapes suivent une approche remplissage-en-+ u pour la création d’un jeu d’échelle Azure machine virtuelle. Voir [Vue d’ensemble de Machine virtuelle échelle définit](virtual-machine-scale-sets-overview.md) pour en savoir plus sur les jeux d’échelle.

Il doit prendre environ 30 minutes pour effectuer les étapes décrites dans cet article.

## <a name="step-1-install-azure-powershell"></a>Étape 1 : Installer PowerShell Azure

Découvrez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) pour plus d’informations sur l’installation de la dernière version d’Azure PowerShell, en sélectionnant votre abonnement et se connecter à votre compte.

## <a name="step-2-create-resources"></a>Étape 2 : Créer des ressources

Créer les ressources qui sont nécessaires pour votre nouveau jeu d’échelle.

### <a name="resource-group"></a>Groupe de ressources

Un ensemble d’échelle machine virtuelle doit figurer dans un groupe de ressources.

1. Obtenir une liste des emplacements disponibles dans lequel vous pouvez placer des ressources :

        Get-AzureLocation | Sort Name | Select Name

2. Sélectionnez un emplacement qui vous convient le mieux, remplacez la valeur de **$locName** portant le même nom d’emplacement, puis créez la variable :

        $locName = "location name from the list, such as Central US"

3. Remplacez la valeur de **$rgName** par le nom que vous souhaitez utiliser pour le nouveau groupe de ressources, puis créez la variable : 

        $rgName = "resource group name"
        
4. Créer le groupe de ressources :
    
        New-AzureRmResourceGroup -Name $rgName -Location $locName

    Vous devriez voir quelque chose comme dans cet exemple :

        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/########-####-####-####-############/resourceGroups/myrg1

### <a name="storage-account"></a>Compte de stockage

Un compte de stockage permet de stocker le disque de système d’exploitation et les données de diagnostic utilisées pour suivre l’évolution par une machine virtuelle. Si possible, il est préférable d’avoir un compte de stockage pour chaque machine virtuelle créée dans un jeu d’échelle. Si ce n’est pas possible, plan pour les pas plus de 20 machines virtuelles par compte de stockage. Cet article montre trois comptes de stockage en cours de création pour les trois machines virtuelles.

1. Remplacez la valeur de **$saName** par un nom pour le compte de stockage. Testez le nom de l’unicité. 

        $saName = "storage account name"
        Get-AzureRmStorageAccountNameAvailability $saName

    Si la réponse est **vraie**, votre nom proposé est unique.

3. Remplacez la valeur de **$saType** par le type de compte de stockage, puis créez la variable :  

        $saType = "storage account type"
        
    Les valeurs possibles sont : Standard_LRS, Standard_GRS, Standard_RAGRS ou Premium_LRS.
        
4. Créer le compte :
    
        New-AzureRmStorageAccount -Name $saName -ResourceGroupName $rgName –Type $saType -Location $locName

    Vous devriez voir quelque chose comme dans cet exemple :

        ResourceGroupName   : myrg1
        StorageAccountName  : myst1
        Id                  : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microsoft
                              .Storage/storageAccounts/myst1
        Location            : centralus
        AccountType         : StandardLRS
        CreationTime        : 3/15/2016 4:51:52 PM
        CustomDomain        :
        LastGeoFailoverTime :
        PrimaryEndpoints    : Microsoft.Azure.Management.Storage.Models.Endpoints
        PrimaryLocation     : centralus
        ProvisioningState   : Succeeded
        SecondaryEndpoints  :
        SecondaryLocation   :
        StatusOfPrimary     : Available
        StatusOfSecondary   :
        Tags                : {}
        Context             : Microsoft.WindowsAzure.Commands.Common.Storage.AzureStorageContext

5. Répétez les étapes 1 à 4 pour créer trois comptes de stockage, par exemple myst1, myst2 et myst3.

### <a name="virtual-network"></a>Réseau virtuel

Un réseau virtuel est requis pour les ordinateurs virtuels dans l’ensemble d’échelle.

1. Remplacez la valeur de **$subnetName** par le nom que vous souhaitez utiliser pour le sous-réseau dans le réseau virtuel, puis créez la variable : 

        $subnetName = "subnet name"
        
2. Créer la configuration de sous-réseau :
    
        $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
        
    Le préfixe d’adresse peut-être être différent de votre réseau virtuel.

3. Remplacez la valeur de **$netName** par le nom que vous souhaitez utiliser pour le réseau virtuel, puis créez la variable : 

        $netName = "virtual network name"
        
4. Créer le réseau virtuel :
    
        $vnet = New-AzureRmVirtualNetwork -Name $netName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $subnet

### <a name="configuration-of-the-scale-set"></a>Configuration de l’ensemble de l’échelle

Vous avez toutes les ressources dont vous avez besoin pour l’échelle de jeu de configuration, nous allons créer celle-ci.  

1. Remplacez la valeur de **$ipName** par le nom que vous souhaitez utiliser pour la configuration IP, puis créez la variable : 

        $ipName = "IP configuration name"
        
2. Créer la configuration IP :

        $ipConfig = New-AzureRmVmssIpConfig -Name $ipName -LoadBalancerBackendAddressPoolsId $null -SubnetId $vnet.Subnets[0].Id

2. Remplacez la valeur de **$vmssConfig** par le nom que vous souhaitez utiliser pour la configuration de jeu échelle, puis créez la variable :   

        $vmssConfig = "Scale set configuration name"
        
3. Créer la configuration pour l’ensemble d’échelle :

        $vmss = New-AzureRmVmssConfig -Location $locName -SkuCapacity 3 -SkuName "Standard_A0" -UpgradePolicyMode "manual"
        
    Cet exemple montre qu'une échelle de création avec les trois machines virtuelles du jeu. Voir [Vue d’ensemble de Machine virtuelle échelle définit](virtual-machine-scale-sets-overview.md) pour plus d’informations sur la capacité des jeux d’échelle. Cette étape inclut également définir la taille (appelée SkuName) des machines virtuelles dans l’ensemble. Pour rechercher une taille qui correspond à vos besoins, consultez [formats pour les machines virtuelles](../virtual-machines/virtual-machines-windows-sizes.md).
    
4. Ajouter la configuration de l’interface réseau à l’échelle définir la configuration :
        
        Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmss -Name $vmssConfig -Primary $true -IPConfiguration $ipConfig
        
    Vous devriez voir quelque chose comme dans cet exemple :

        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     :
        OverProvision         :
        Id                    :
        Name                  :
        Type                  :
        Location              : Central US
        Tags                  :

#### <a name="operating-system-profile"></a>Profil de système d’exploitation

1. Remplacez la valeur de **$computerName** par le préfixe de nom d’ordinateur que vous souhaitez utiliser, puis créez la variable : 

        $computerName = "computer name prefix"
        
2. Remplacez la valeur de **$adminName** le nom du compte administrateur sur les ordinateurs virtuels, puis créez la variable :

        $adminName = "administrator account name"
        
3. Remplacez la valeur de **$adminPassword** par le mot de passe, puis créez la variable :

        $adminPassword = "password for administrator accounts"
        
4. Créez le profil de système d’exploitation :

        Set-AzureRmVmssOsProfile -VirtualMachineScaleSet $vmss -ComputerNamePrefix $computerName -AdminUsername $adminName -AdminPassword $adminPassword

#### <a name="storage-profile"></a>Profil de stockage

1. Remplacez la valeur de **$storageProfile** par le nom que vous souhaitez utiliser pour le profil de stockage, puis créez la variable :  

        $storageProfile = "storage profile name"
        
2. Créer les variables qui définissent l’image à utiliser :  
      
        $imagePublisher = "MicrosoftWindowsServer"
        $imageOffer = "WindowsServer"
        $imageSku = "2012-R2-Datacenter"
        
    Pour rechercher les informations relatives à d’autres images à utiliser, consultez [Naviguer et sélectionnez machine virtuelle Azure images avec Windows PowerShell et l’infrastructure du langage commun Azure](../virtual-machines/virtual-machines-windows-cli-ps-findimage.md).
        
3. Remplacez la valeur de **$vhdContainers** par une liste qui contient les chemins d’accès où sont stockés les disques durs virtuels, tels que « https://mystorage.blob.core.windows.net/vhds », puis créez la variable :
       
        $vhdContainers = @("https://myst1.blob.core.windows.net/vhds","https://myst2.blob.core.windows.net/vhds","https://myst3.blob.core.windows.net/vhds")
        
4. Créez le profil de stockage :

        Set-AzureRmVmssStorageProfile -VirtualMachineScaleSet $vmss -ImageReferencePublisher $imagePublisher -ImageReferenceOffer $imageOffer -ImageReferenceSku $imageSku -ImageReferenceVersion "latest" -Name $storageProfile -VhdContainer $vhdContainers -OsDiskCreateOption "FromImage" -OsDiskCaching "None"  

### <a name="virtual-machine-scale-set"></a>Machine virtuelle échelle ensemble

Enfin, vous pouvez créer le jeu d’échelle.

1. Remplacez la valeur de **$vmssName** par le nom de l’ensemble d’échelle machine virtuelle, puis créez la variable :

        $vmssName = "scale set name"
        
2. Créer le jeu d’échelle :

        New-AzureRmVmss -ResourceGroupName $rgName -Name $vmssName -VirtualMachineScaleSet $vmss

    Vous devriez voir quelque chose comme dans cet exemple montrant un déploiement réussi :

        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     : Updating
        OverProvision         :
        Id                    : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microso
                                ft.Compute/virtualMachineScaleSets/myvmss1
        Name                  : myvmss1
        Type                  : Microsoft.Compute/virtualMachineScaleSets
        Location              : centralus
        Tags                  :

## <a name="step-3-explore-resources"></a>Étape 3 : Explorer les ressources

Utilisez ces ressources pour parcourir l’ensemble d’échelle machine virtuelle que vous avez créé :

- Portail Azure - limitée des informations est disponible à l’aide du portail.
- [Explorateur de Ressources azure](https://resources.azure.com/) - cet outil est la meilleure pour l’exploration de l’état actuel de votre jeu d’échelle.
- Azure PowerShell - Utilisez cette commande pour obtenir des informations :

        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
        
        Or 
        
        Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
        

## <a name="next-steps"></a>Étapes suivantes

- Gérer l’ensemble d’échelle que vous venez de créer en utilisant les informations dans la [Gestion des machines virtuelles dans un jeu d’échelle de Machine virtuelle](virtual-machine-scale-sets-windows-manage.md)
- Pensez à définir une échelle automatique de votre échelle définie à l’aide des informations dans les [jeux d’automatique machine virtuelle et mise à l’échelle de l’échelle](virtual-machine-scale-sets-autoscale-overview.md)
- En savoir plus sur la mise à l’échelle verticale en passant en revue [échelle verticale avec des jeux d’échelle Machine virtuelle](virtual-machine-scale-sets-vertical-scale-reprovision.md)
