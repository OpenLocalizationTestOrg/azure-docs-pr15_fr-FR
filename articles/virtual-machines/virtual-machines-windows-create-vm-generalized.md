<properties
    pageTitle="Créer des machines virtuelles à partir d’un disque dur virtuel GRG | Microsoft Azure"
    description="Apprenez à créer une machine virtuelle Windows à partir d’une image de disque dur virtuel GRG à l’aide de PowerShell Azure, dans le modèle de déploiement du Gestionnaire de ressources."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="cynthn"/>

# <a name="create-a-vm-from-a-generalized-vhd-image"></a>Créer une machine virtuelle à partir d’une image de disque dur virtuel GRG

Une image de disque dur virtuel GRG a eu toutes vos informations de compte personnel supprimées à l’aide de [Sysprep](virtual-machines-windows-generalize-vhd.md). Vous pouvez créer un disque dur virtuel GRG en exécutant Sysprep sur un ordinateur virtuel en local, puis de [Télécharger le disque dur virtuel sur Azure](virtual-machines-windows-upload-image.md), ou en exécutant Sysprep sur une machine virtuelle Azure existant, puis sur [Copier le disque dur virtuel](virtual-machines-windows-vhd-copy.md).

Si vous voulez créer une machine virtuelle à partir d’un disque dur virtuel spécialisé, voir [créer une machine virtuelle à partir d’un disque dur virtuel spécialisée](virtual-machines-windows-create-vm-specialized.md).

Le plus rapide pour créer une machine virtuelle à partir d’un disque dur virtuel GRG consiste à utiliser un [modèle de démarrage rapide] (https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image). 


## <a name="prerequisites"></a>Conditions préalables

Si vous vous apprêtez à utiliser un disque dur virtuel téléchargé à partir d’une machine virtuelle en local, tels que ceux créés à l’aide de Hyper-V, vous devez vous assurer que vous avez suivi les instructions de [préparer un disque dur virtuel Windows pour télécharger dans Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md). 

Les disques durs virtuels téléchargées et existant Azure machine virtuelle durs doivent être généralisée avant que vous puissiez créer une machine virtuelle à l’aide de cette méthode. Pour plus d’informations, voir [Generalize une machine virtuelle Windows à l’aide de Sysprep](virtual-machines-windows-generalize-vhd.md). 


## <a name="set-the-uri-of-the-vhd"></a>Définir l’URI de ce dernier

L’URI du disque dur virtuel à utiliser est au format : https://**mystorageaccount**.blob.core.windows.net/**mycontainer**/**MyVhdName**.vhd. Dans cet exemple le disque dur virtuel nommé **myVHD** se trouve dans le compte de stockage **mystorageaccount** dans le conteneur **mycontainer**.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


## <a name="create-a-virtual-network"></a>Créer un réseau virtuel

Créer la vNet et sous réseau du [réseau virtuel](../virtual-network/virtual-networks-overview.md).


1. Créer le sous-réseau. L’exemple suivant crée un sous-réseau nommé **mySubnet** dans le groupe de ressources **myResourceGroup** par le préfixe adresse **10.0.0.0/24**.  

    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
      
2. Créer le réseau virtuel. L’exemple suivant crée un réseau virtuel nommé **myVnet** à l’emplacement **Américain ouest** avec le préfixe d’adresse de **10.0.0.0/16**.  

    ```powershell
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
            
## <a name="create-a-public-ip-address-and-network-interface"></a>Créer une interface de réseau et adresse IP publique

Pour activer la communication avec la machine virtuelle dans le réseau virtuel, vous avez besoin d’une [adresse IP publique](../virtual-network/virtual-network-ip-addresses-overview-arm.md) et une interface réseau.

1. Créer une adresse IP publique. Cet exemple crée une adresse IP publique nommée **myPip**. 

    ```powershell
    $ipName = "myPip"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic
    ```       

2. Créer la carte réseau. Cet exemple montre comment créer une carte réseau nommée **myNic**. 

    ```powershell
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>Créer une règle RDP et le groupe de sécurité réseau

Pour pouvoir se connecter à votre ordinateur virtuel à l’aide de RDP, vous devez avoir une règle de sécurité qui autorise l’accès RDP sur le port 3389. 

Cet exemple crée un NSG nommé **myNsg** qui contient une règle appelée **myRdpRule** qui permet du trafic RDP sur le port 3389. Pour plus d’informations sur NSGs, voir [ouverture de ports pour une machine virtuelle dans Azure à l’aide de PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md).

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


## <a name="create-a-variable-for-the-virtual-network"></a>Créer une variable pour le réseau virtuel

Créer une variable pour le réseau virtuel terminée. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

## <a name="create-the-vm"></a>Créer la machine virtuelle

Le script PowerShell suivant montre comment configurer les configurations machine virtuelle et utiliser l’image chargée machine virtuelle comme source pour la nouvelle installation.

</br>


```powershell
    # Enter a new user name and password to use as the local administrator account 
    # for remotely accessing the VM.
    $cred = Get-Credential
    
    # Name of the storage account where the VHD is located. This example sets the 
    # storage account name as "myStorageAccount"
    $storageAccName = "myStorageAccount"
    
    # Name of the virtual machine. This example sets the VM name as "myVM".
    $vmName = "myVM"
    
    # Size of the virtual machine. This example creates "Standard_D2_v2" sized VM. 
    # See the VM sizes documentation for more information: 
    # https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
    $vmSize = "Standard_D2_v2"
    
    # Computer name for the VM. This examples sets the computer name as "myComputer".
    $computerName = "myComputer"
    
    # Name of the disk that holds the OS. This example sets the 
    # OS disk name as "myOsDisk"
    $osDiskName = "myOsDisk"
    
    # Assign a SKU name. This example sets the SKU name as "Standard_LRS"
    # Valid values for -SkuName are: Standard_LRS - locally redundant storage, Standard_ZRS - zone redundant storage, Standard_GRS - geo redundant storage, Standard_RAGRS - read access geo redundant storage, Premium_LRS - premium locally redundant storage. 
    $skuName = "Standard_LRS"
    
    # Get the storage account where the uploaded image is stored
    $storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName
    
    # Set the VM name and size
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
    
    #Set the Windows operating system configuration and add the NIC
    $vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    
    # Create the OS disk URI
    $osDiskUri = '{0}vhds/{1}-{2}.vhd' `
        -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
    
    # Configure the OS disk to be created from the existing VHD image (-CreateOption fromImage).
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri `
        -CreateOption fromImage -SourceImageUri $imageURI -Windows
    
    # Create the new VM
    New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

## <a name="verify-that-the-vm-was-created"></a>Vérifier que la machine virtuelle a été créée 

Lorsque vous avez terminé, vous devriez voir la machine virtuelle nouvellement créée dans le [portail Azure](https://portal.azure.com) sous **Parcourir** > **machines virtuelles**, ou en utilisant les commandes PowerShell suivantes :

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Étapes suivantes

Pour gérer votre nouvelle machine virtuelle avec Azure PowerShell, consultez [machines virtuelles gérer à l’aide du Gestionnaire de ressources Azure et PowerShell](virtual-machines-windows-ps-manage.md).


